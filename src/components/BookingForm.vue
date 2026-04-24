<script setup>
import { ref, reactive, computed, onMounted } from 'vue'
import { supabase } from '../lib/supabase'

const form = reactive({
  name: '',
  phone: '',
  email: '',
      selectedServices: [{
        category: '',
        service: '',
        service_id: null,
        price: 0,
        duration_minutes: 0,
        time: '',
        specialist_id: null
      }],
  date: '',
  notes: ''
})

const isSubmitted = ref(false)
const errors = ref({})

const services = ref([])
const specialists = ref([])
const employeeServices = ref([])

onMounted(async () => {
  const { data: servicesData, error: servicesError } = await supabase
      .from('services')
      .select('*')

  console.log('servicesData:', servicesData)
  console.log('servicesError:', servicesError)

  if (servicesError) {
    console.error('Services error:', servicesError)
  } else {
    services.value = servicesData || []
  }

  const { data: employeesData, error: employeesError } = await supabase
      .from('employees')
      .select('*')

  console.log('employeesData:', employeesData)
  console.log('employeesError:', employeesError)

  if (employeesError) {
    console.error('Employees error:', employeesError)
  } else {
    specialists.value = employeesData || []
  }

  const { data: employeeServicesData, error: employeeServicesError } = await supabase
      .from('employee_services')
      .select('*')

  if (employeeServicesError) {
    console.error('Employee services error:', employeeServicesError)
  } else {
    employeeServices.value = employeeServicesData || []
  }

})

const categories = computed(() => [...new Set(services.value.map(s => s.category))])


const selectedCategoriesSummary = computed(() => {
  return form.selectedServices
      .map(s => {
        const specialist = specialists.value.find(e => e.id === s.specialist_id)
        return `${s.service || 'Unnamed'} – ${s.time || '--:--'} – ${specialist ? specialist.full_name : 'No specialist'}`
      })
      .join(', ')
})

const totalPrice = computed(() => {
  return form.selectedServices.reduce((sum, s) => sum + s.price, 0)
})

const addService = () => {
  form.selectedServices.push({
    category: '',
    service: '',
    service_id: null,
    price: 0,
    duration_minutes: 0,
    time: '',
    specialist_id: null
  })
}

const removeService = (index) => {
  if (form.selectedServices.length > 1) {
    form.selectedServices.splice(index, 1)
  }
}

const getServicesByCategory = (category) => {
  return services.value.filter(s => s.category === category)
}

const getSpecialistsByService = (serviceId) => {
  if (!serviceId) return []

  const employeeIds = employeeServices.value
      .filter(row => row.service_id === serviceId)
      .map(row => row.employee_id)

  return specialists.value.filter(employee =>
      employeeIds.includes(employee.id)
  )
}

const updateService = (index, field, value) => {
  if (field === 'category') {
    form.selectedServices[index].category = value
    form.selectedServices[index].service = ''
    form.selectedServices[index].service_id = null
    form.selectedServices[index].price = 0
    form.selectedServices[index].duration_minutes = 0
    form.selectedServices[index].specialist_id = null
  }

  if (field === 'service') {
    const selectedService = services.value.find(
        s => s.name === value && s.category === form.selectedServices[index].category
    )

    form.selectedServices[index].service = value
    form.selectedServices[index].service_id = selectedService ? selectedService.id : null
    form.selectedServices[index].price = selectedService ? Number(selectedService.price) : 0
    form.selectedServices[index].duration_minutes = selectedService ? selectedService.duration_minutes : 60
    form.selectedServices[index].specialist_id = null
  }
}

  const validateServiceTime = (time) => {
    if (time) {
      const [hours] = time.split(':').map(Number)
      return hours >= 9 && hours <= 21
    }
    return true
  }

  const validateForm = () => {
    errors.value = {}
    if (!form.name.trim()) errors.value.name = 'Name is required'
    if (!form.phone.trim()) errors.value.phone = 'Phone is required'
    if (!form.email.trim()) errors.value.email = 'Email is required'
    else if (!/\S+@\S+\.\S+/.test(form.email)) errors.value.email = 'Invalid email'
    if (form.selectedServices.length === 0) errors.value.services = 'At least one service is required'
    else {
      for (let i = 0; i < form.selectedServices.length; i++) {
        const service = form.selectedServices[i]

        if (!service.service_id) {
          errors.value.services = `Please select a service`
          break
        }

        if (!service.time) {
          errors.value.services = `Time is required for all services`
          break
        }
        if (!validateServiceTime(service.time)) {
          errors.value.services = `All times must be between 09:00 and 21:00`
          break
        }
        if (!service.specialist_id) {
          errors.value.services = 'Please select a specialist for all services'
          break
        }
      }
    }
    if (!form.date) errors.value.date = 'Date is required'
    return Object.keys(errors.value).length === 0
  }

  const submitForm = async () => {
    if (!validateForm()) return

    for (const selectedService of form.selectedServices) {
      const startAt = new Date(`${form.date}T${selectedService.time}`)
      const endAt = new Date(
          startAt.getTime() + selectedService.duration_minutes * 60000
      )

      const {error} = await supabase.from('appointments').insert([
        {
          customer_name: form.name,
          customer_phone: form.phone,
          customer_email: form.email,
          service_id: selectedService.service_id,
          employee_id: selectedService.specialist_id,
          start_at: startAt.toISOString(),
          end_at: endAt.toISOString(),
          notes: form.notes
        }
      ])

      if (error) {
        console.error('Booking error:', error)
        alert(error.message)
        return
      }
    }

    isSubmitted.value = true
}

</script>

<template>
  <div class="booking-form">
    <h2>Book Your Appointment</h2>
    <div v-if="isSubmitted" class="success-message">
      <p>Thank you! Your appointment request has been submitted. We'll contact you soon to confirm.</p>
      <button @click="isSubmitted = false">Book Another</button>
    </div>
    <form v-else @submit.prevent="submitForm">
      <div class="form-row">
        <div class="form-group">
          <label for="name">Full Name *</label>
          <input id="name" v-model="form.name" type="text" />
          <span v-if="errors.name" class="error">{{ errors.name }}</span>
        </div>
        <div class="form-group">
          <label for="phone">Phone *</label>
          <input id="phone" v-model="form.phone" type="tel" />
          <span v-if="errors.phone" class="error">{{ errors.phone }}</span>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group form-group-full">
          <label>Services *</label>
          <div v-for="(selectedService, index) in form.selectedServices" :key="index" class="service-selection">
            <select v-model="selectedService.category" @change="updateService(index, 'category', selectedService.category)">
              <option value="">Category</option>
              <option v-for="category in categories" :key="category" :value="category">{{ category }}</option>
            </select>
            <select v-model="selectedService.service" :disabled="!selectedService.category" @change="updateService(index, 'service', selectedService.service)">
              <option value="">Service</option>
              <option v-for="service in getServicesByCategory(selectedService.category)" :key="service.name" :value="service.name">
                {{ service.name }}
              </option>
            </select>
            <input v-model="selectedService.time" type="time" :disabled="!selectedService.service" min="09:00" max="21:00" class="service-time-input" />
            <select v-model="selectedService.specialist_id" :disabled="!selectedService.service_id">
              <option :value="null">Specialist</option>
              <option
                  v-for="specialist in getSpecialistsByService(selectedService.service_id)"
                  :key="specialist.id"
                  :value="specialist.id"
              >
                {{ specialist.full_name }}
              </option>
            </select>
            <button type="button" @click="removeService(index)" :disabled="form.selectedServices.length === 1" class="remove-service-btn">Remove</button>
          </div>
          <button type="button" @click="addService" class="add-service-btn">Add Another Service</button>
          <span v-if="errors.services" class="error">{{ errors.services }}</span>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="email">Email *</label>
          <input id="email" v-model="form.email" type="email" />
          <span v-if="errors.email" class="error">{{ errors.email }}</span>
        </div>
        <div class="form-group">
          <label for="date">Appointment Date *</label>
          <input id="date" v-model="form.date" type="date" />
          <span v-if="errors.date" class="error">{{ errors.date }}</span>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group form-group-full">
          <label for="notes">Notes</label>
          <textarea id="notes" v-model="form.notes" rows="3"></textarea>
        </div>
      </div>
      <div class="form-summary">
        <h3>Appointment Summary</h3>
        <p>Services: {{ selectedCategoriesSummary }}</p>
        <p>Total Price: {{ totalPrice }} MKD</p>
      </div>
      <button type="submit" class="submit-btn">Submit Booking</button>
    </form>
  </div>
</template>

<style scoped>
.booking-form {
  max-width: 950px;
  margin: 0 auto;
  padding: 2.5rem 1.5rem;
  background: var(--color-background);
  border-radius: 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.booking-form h2 {
  text-align: center;
  margin-bottom: 2.5rem;
  color: var(--color-text-primary);
  font-size: 2rem;
  letter-spacing: 0.5px;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
  margin-bottom: 1.5rem;
}

.form-group-full {
  grid-column: 1 / -1;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group label {
  margin-bottom: 0.6rem;
  font-weight: 500;
  color: var(--color-text-primary);
  text-transform: uppercase;
  font-size: 0.75rem;
  letter-spacing: 0.6px;
}

.form-group input,
.form-group select,
.form-group textarea {
  padding: 0.85rem;
  border: 1px solid var(--color-border);
  border-radius: 10px;
  font-family: var(--font-sans);
  font-size: 0.95rem;
  transition: all 0.3s ease;
  background: var(--color-background);
}

.form-group input:hover,
.form-group select:hover,
.form-group textarea:hover {
  border-color: var(--color-accent);
}

.form-group input:focus,
.form-group select:focus,
.form-group textarea:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px rgba(201, 162, 126, 0.1);
}

.service-selection {
  display: flex;
  gap: 0.75rem;
  margin-bottom: 1rem;
  align-items: flex-start;
}

.service-selection select {
  flex: 1;
  min-width: 0;
}

.service-selection select:nth-of-type(4) {
  flex: 0.9;
}

.service-time-input {
  flex: 0.8;
  padding: 0.85rem;
  border: 1px solid var(--color-border);
  border-radius: 10px;
  font-family: var(--font-sans);
  font-size: 0.95rem;
  transition: all 0.3s ease;
  background: var(--color-background);
}

.service-time-input:hover:not(:disabled) {
  border-color: var(--color-accent);
}

.service-time-input:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px rgba(201, 162, 126, 0.1);
}

.service-time-input:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.error {
  color: #c9574a;
  font-size: 0.75rem;
  font-weight: 500;
}

.submit-btn {
  width: 100%;
  padding: 1rem;
  background: var(--color-accent);
  color: var(--color-background);
  border: none;
  border-radius: 50px;
  font-size: 0.95rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.6px;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 1.5rem;
  box-shadow: 0 4px 12px rgba(201, 162, 126, 0.2);
}

.submit-btn:hover {
  background: var(--color-accent-dark);
  box-shadow: 0 6px 16px rgba(201, 162, 126, 0.3);
  transform: translateY(-2px);
}

.submit-btn:active {
  transform: translateY(0);
}

.success-message {
  text-align: center;
  padding: 2.5rem 1.5rem;
}

.success-message p {
  margin-bottom: 1.5rem;
  color: var(--color-text-secondary);
  font-size: 1rem;
  line-height: 1.6;
}

.success-message button {
  padding: 0.8rem 1.5rem;
  background: var(--color-accent);
  color: var(--color-background);
  border: none;
  border-radius: 50px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(201, 162, 126, 0.2);
}

.success-message button:hover {
  background: var(--color-accent-dark);
  box-shadow: 0 6px 16px rgba(201, 162, 126, 0.3);
  transform: translateY(-2px);
}

.add-service-btn {
  padding: 0.7rem 1.2rem;
  background: var(--color-accent);
  color: var(--color-background);
  border: none;
  border-radius: 8px;
  font-size: 0.85rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(201, 162, 126, 0.15);
  text-transform: uppercase;
  letter-spacing: 0.4px;
  margin-top: 0.5rem;
}

.add-service-btn:hover {
  background: var(--color-accent-dark);
  box-shadow: 0 4px 12px rgba(201, 162, 126, 0.25);
  transform: translateY(-1px);
}

.remove-service-btn {
  padding: 0.65rem 1rem;
  background: #9d8b7d;
  color: var(--color-background);
  border: none;
  border-radius: 8px;
  font-size: 0.8rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  text-transform: uppercase;
  letter-spacing: 0.4px;
  white-space: nowrap;
}

.remove-service-btn:hover {
  background: #7a6d63;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transform: translateY(-1px);
}

.remove-service-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  transform: none;
}

.remove-service-btn:active,
.add-service-btn:active {
  transform: translateY(0);
}

.form-summary {
  margin-top: 2rem;
  padding: 1.5rem;
  background: linear-gradient(135deg, var(--color-background-soft) 0%, rgba(201, 162, 126, 0.08) 100%);
  border-radius: 12px;
  border: 1px solid var(--color-border);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.form-summary h3 {
  margin: 0 0 1rem 0;
  font-size: 1.1rem;
  color: var(--color-text-primary);
  font-weight: 600;
  letter-spacing: 0.3px;
}

.form-summary p {
  margin: 0.6rem 0;
  color: var(--color-text-secondary);
  font-size: 0.95rem;
  line-height: 1.5;
}

@media (max-width: 768px) {
  .form-row {
    grid-template-columns: 1fr;
    gap: 1.2rem;
  }

  .booking-form {
    padding: 1.5rem 1rem;
  }

  .booking-form h2 {
    font-size: 1.6rem;
    margin-bottom: 1.8rem;
  }

  .service-selection {
    flex-direction: column;
  }

  .service-selection select,
  .service-selection input,
  .remove-service-btn {
    width: 100%;
  }
}

@media (max-width: 480px) {
  .booking-form {
    padding: 1rem;
  }

  .booking-form h2 {
    font-size: 1.4rem;
    margin-bottom: 1.5rem;
  }

  .form-group label {
    font-size: 0.7rem;
  }

  .form-summary {
    padding: 1rem;
  }

  .form-summary h3 {
    font-size: 1rem;
  }

  .form-summary p {
    font-size: 0.9rem;
  }
}
</style>
