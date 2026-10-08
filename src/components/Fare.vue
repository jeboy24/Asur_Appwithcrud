<script setup>
import { ref } from 'vue'

const props = defineProps({
  fare: Object
})

const emit = defineEmits([
  'delete',
  'update'
])

const editing = ref(false)

const editTransport = ref('')
const editRoute = ref('')
const editFare = ref('')

function editRecord() {
  editTransport.value = props.fare.transportType
  editRoute.value = props.fare.route
  editFare.value = props.fare.fare

  editing.value = true
}

function saveRecord() {

  if (
    editTransport.value.trim() === '' ||
    editRoute.value.trim() === '' ||
    editFare.value === ''
  ) {
    return
  }

  emit('update', {
    ...props.fare,
    transportType: editTransport.value,
    route: editRoute.value,
    fare: Number(editFare.value)
  })

  editing.value = false
}

function cancelEdit() {
  editing.value = false
}

function removeRecord() {
  emit('delete', props.fare.id)
}

function changePayment() {

  emit('update', {
    ...props.fare,
    paid: !props.fare.paid
  })

}
</script>

<template>

  <div class="fare-card">

    <!-- Normal View -->

    <div v-if="!editing" class="record">

      <div class="record-info">

        <h3>
          {{ fare.transportType }}
        </h3>

        <p>
          Route: {{ fare.route }}
        </p>

        <p class="fare-price">
          Fare: ₱{{ fare.fare }}
        </p>

        <p
          :class="fare.paid ? 'paid' : 'unpaid'"
        >
          {{ fare.paid ? 'Paid' : 'Unpaid' }}
        </p>

      </div>

      <div class="actions">

        <button @click="changePayment">
          {{ fare.paid ? 'Mark Unpaid' : 'Mark Paid' }}
        </button>

        <button @click="editRecord">
          Edit
        </button>

        <button
          class="delete-button"
          @click="removeRecord"
        >
          Delete
        </button>

      </div>

    </div>

    <!-- Edit View -->

    <div v-else class="edit-form">

      <input
        v-model="editTransport"
        type="text"
        placeholder="Transportation Type"
      />

      <input
        v-model="editRoute"
        type="text"
        placeholder="Route"
      />

      <input
        v-model="editFare"
        type="number"
        placeholder="Fare"
      />

      <button @click="saveRecord">
        Save
      </button>

      <button @click="cancelEdit">
        Cancel
      </button>

    </div>

  </div>

</template>