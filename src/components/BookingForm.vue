<script setup>
import { ref, reactive, computed, onMounted, watch } from 'vue'
import { supabase } from '../lib/supabase'
import Datepicker from 'vue3-datepicker'

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
    date: null,
    selectedTime: '',
    specialist_id: null,
    availableSlots: []
  }],
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

// Watch for date changes and regenerate slots
watch(
  () => form.selectedServices.map(s => s.date),
  (newDates) => {
    form.selectedServices.forEach((service, index) => {
      service.availableSlots = getAvailableTimeSlots(index)
      service.selectedTime = ''
    })
  },
  { deep: true }
)

// Watch for specialist changes and re-filter slots
watch(
  () => form.selectedServices.map(s => s.specialist_id),
  (newSpecialists) => {
    form.selectedServices.forEach((service, index) => {
      service.availableSlots = getAvailableTimeSlots(index)
      service.selectedTime = ''
    })
  },
  { deep: true }
)

const categories = computed(() => [...new Set(services.value.map(s => s.category))])

const isSunday = (date) => {
  if (!date) return false
  return new Date(date).getDay() === 0
}

const formatDateForDisplay = (date) => {
  if (!date) return '--'
  const d = new Date(date)
  const days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
  const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
  return `${days[d.getDay()]}, ${months[d.getMonth()]} ${d.getDate()}`
}

const selectedCategoriesSummary = computed(() => {
  return form.selectedServices
    .map(s => {
      const specialist = specialists.value.find(e => e.id === s.specialist_id)
      const date = formatDateForDisplay(s.date)
      const time = s.selectedTime || '--:--'
      const spec = specialist ? specialist.full_name : 'No specialist'
      return `${s.service || 'Unnamed'} – ${date} – ${time} – ${spec}`
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
    date: null,
    selectedTime: '',
    specialist_id: null,
    availableSlots: []
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
    form.selectedServices[index].selectedTime = ''
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
    form.selectedServices[index].selectedTime = ''
  }
}

const getAvailableTimeSlots = (serviceIndex) => {
  const service = form.selectedServices[serviceIndex]
  if (!service.date) return []

  // Determine day of week: 0 = Sunday, 1 = Monday, ..., 6 = Saturday
  const day = new Date(service.date).getDay()

  // Define working hours per day
  let startHour, endHour
  if (day === 0) {
    // Sunday: Closed
    return []
  } else if (day >= 1 && day <= 5) {
    // Monday–Friday: 09:00 – 19:00
    startHour = 9
    endHour = 19
  } else if (day === 6) {
    // Saturday: 08:00 – 17:00
    startHour = 8
    endHour = 17
  }

  const slots = []

  // Generate 30-minute slots
  for (let hour = startHour; hour < endHour; hour++) {
    for (let minute = 0; minute < 60; minute += 30) {
      const startMin = String(minute).padStart(2, '0')
      const endHour = minute + 30 >= 60 ? hour + 1 : hour
      const endMin = String((minute + 30) % 60).padStart(2, '0')

      const startTime = `${String(hour).padStart(2, '0')}:${startMin}`
      const endTime = `${String(endHour).padStart(2, '0')}:${endMin}`

      slots.push({
        start: startTime,
        end: endTime,
        display: `${startTime} - ${endTime}`
      })
    }
  }

  // Filter by specialist availability if specialist is selected
  if (service.specialist_id) {
    // TODO: Filter based on specialist's booked appointments
    // For now, return all slots (ready for API integration)
    return slots
  }

  return slots
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

        if (!service.date) {
          errors.value.services = `Please select a date for all services`
          break
        }

        if (!service.selectedTime) {
          errors.value.services = `Please select a time for all services`
          break
        }

        if (!service.specialist_id) {
          errors.value.services = 'Please select a specialist for all services'
          break
        }
      }
    }
    return Object.keys(errors.value).length === 0
  }

  const submitForm = async () => {
    if (!validateForm()) return

    for (const selectedService of form.selectedServices) {
      // Convert Date object to ISO string format YYYY-MM-DD
      let dateString
      if (selectedService.date instanceof Date) {
        const year = selectedService.date.getFullYear()
        const month = String(selectedService.date.getMonth() + 1).padStart(2, '0')
        const day = String(selectedService.date.getDate()).padStart(2, '0')
        dateString = `${year}-${month}-${day}`
      } else {
        dateString = selectedService.date
      }

      const startAt = new Date(`${dateString}T${selectedService.selectedTime}`)
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
          <div v-for="(selectedService, index) in form.selectedServices" :key="index" class="service-block">
            <div class="service-selection">
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
            <!-- Date Picker -->
            <div class="date-picker-wrapper">
              <label class="date-label"></label>
              <Datepicker
                v-model="selectedService.date"
                :disabled-dates="{ days: [0], past: true }"
                placeholder="Pick a date"
                :enable-time-picker="false"
                class="custom-datepicker"
              />
            </div>
              <select v-model="selectedService.specialist_id" :disabled="!selectedService.service_id" class="service-specialist-select">
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

            <!-- Time Slots -->
            <div v-if="selectedService.date" class="time-slots-container">
              <div v-if="isSunday(selectedService.date)" class="closed-message">
                <p>We are closed on Sundays</p>
              </div>
              <div v-else-if="getAvailableTimeSlots(index).length > 0" class="time-slots-grid">
                <button
                  v-for="slot in getAvailableTimeSlots(index)"
                  :key="slot.start"
                  type="button"
                  @click="selectedService.selectedTime = slot.start"
                  :class="['time-slot', { active: selectedService.selectedTime === slot.start }]"
                >
                  {{ slot.display }}
                </button>
              </div>
              <div v-else class="no-slots-message">
                <p>No available slots for this date</p>
              </div>
            </div>
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
  margin-bottom: 0.5rem;
  align-items: stretch;
}

.service-selection select,
.custom-datepicker,
.date-picker-wrapper,
.remove-service-btn {
  height: 3rem;
  box-sizing: border-box;
}

.date-picker-wrapper {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.service-specialist-select {
  flex: 1;
  min-width: 0;
}

.service-date-input {
  flex: 0.9;
  padding: 0.85rem;
  border: 1px solid var(--color-border);
  border-radius: 10px;
  font-family: var(--font-sans);
  font-size: 0.95rem;
  transition: all 0.3s ease;
  background: var(--color-background);
}

.service-date-input:hover:not(:disabled) {
  border-color: var(--color-accent);
}

.service-date-input:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px rgba(201, 162, 126, 0.1);
}

.service-date-input:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.service-block {
  margin-bottom: 1.5rem;
}

.time-slots-container {
  margin-top: 1rem;
  padding: 1rem;
  background: rgba(201, 162, 126, 0.05);
  border-radius: 10px;
  border: 1px solid rgba(201, 162, 126, 0.1);
}

.time-slots-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 0.75rem;
}

.time-slot {
  padding: 0.6rem 1rem;
  background: #d4a5a5;
  color: white;
  border: 2px solid #d4a5a5;
  border-radius: 50px;
  cursor: pointer;
  font-size: 0.85rem;
  font-weight: 500;
  transition: all 0.3s ease;
  white-space: nowrap;
}

.time-slot:hover {
  background: #c89090;
  border-color: #c89090;
  transform: translateY(-2px);
}

.time-slot.active {
  background: #8b4545;
  border-color: #8b4545;
  color: white;
  box-shadow: 0 4px 12px rgba(139, 69, 69, 0.3);
}

.time-slot.active:hover {
  background: #7a3a3a;
  border-color: #7a3a3a;
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
  .service-date-input,
  .custom-datepicker,
  .date-picker-wrapper,
  .remove-service-btn {
    width: 100%;
  }

  .time-slots-grid {
    gap: 0.5rem;
  }

  .time-slot {
    padding: 0.5rem 0.8rem;
    font-size: 0.8rem;
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
