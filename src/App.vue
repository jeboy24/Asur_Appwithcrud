<script setup>
import { ref, computed, onMounted } from 'vue'
import Fare from './components/Fare.vue'

const fares = ref([])

const transportType = ref('')
const route = ref('')
const fareAmount = ref('')

function addFare() {
  if (
    transportType.value.trim() === '' ||
    route.value.trim() === '' ||
    fareAmount.value === ''
  ) {
    return
  }

  fares.value.push({
    id: Date.now(),
    transportType: transportType.value,
    route: route.value,
    fare: Number(fareAmount.value),
    paid: false
  })

  transportType.value = ''
  route.value = ''
  fareAmount.value = ''

  saveFares()
}

function deleteFare(id) {
  fares.value = fares.value.filter(fare => fare.id !== id)

  saveFares()
}

function updateFare(updatedFare) {
  const index = fares.value.findIndex(
    fare => fare.id === updatedFare.id
  )

  if (index !== -1) {
    fares.value[index] = updatedFare
  }

  saveFares()
}

function saveFares() {
  localStorage.setItem(
    'transportationFares',
    JSON.stringify(fares.value)
  )
}

const totalFare = computed(() => {
  return fares.value.reduce(
    (total, fare) => total + Number(fare.fare),
    0
  )
})

onMounted(() => {
  const savedFares = localStorage.getItem(
    'transportationFares'
  )

  if (savedFares) {
    fares.value = JSON.parse(savedFares)
  }
})
</script>

<template>

  <div class="container">

    <header>
      <h1>Transportation Fare Management System</h1>
      <p>Manage transportation routes and fare information</p>
    </header>

    <!-- Add Fare -->

    <section class="fare-form">

      <h2>Add Transportation Fare</h2>

      <div class="form-row">

        <input
          v-model="transportType"
          type="text"
          placeholder="Transportation Type"
        />

        <input
          v-model="route"
          type="text"
          placeholder="Route"
        />

        <input
          v-model="fareAmount"
          type="number"
          placeholder="Fare Amount"
        />

        <button @click="addFare">
          Add Fare
        </button>

      </div>

    </section>

    <!-- Summary -->

    <section class="summary">

      <div>
        <h3>Total Records</h3>
        <strong>{{ fares.length }}</strong>
      </div>

      <div>
        <h3>Total Fare</h3>
        <strong>₱{{ totalFare }}</strong>
      </div>

    </section>

    <!-- Fare Records -->

    <section>

      <h2 class="list-title">
        Transportation Fare Records
      </h2>

      <div class="fare-list">

        <TodoItem
          v-for="fare in fares"
          :key="fare.id"
          :fare="fare"
          @delete="deleteFare"
          @update="updateFare"
        />

        <p v-if="fares.length === 0" class="empty">
          No transportation fare records yet.
        </p>

      </div>

    </section>

  </div>

</template>