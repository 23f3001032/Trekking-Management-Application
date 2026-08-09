<template>
  <AppLayout>
    <h3 class="fw-bold mb-3">Browse Treks</h3>

    <div class="row g-2 mb-4">
      <div class="col-md-5">
        <input v-model="filters.q" @input="debouncedLoad" class="form-control" placeholder="Search treks...">
      </div>
      <div class="col-md-3">
        <select v-model="filters.difficulty" @change="loadTreks" class="form-select">
          <option value="All">Difficulty: All</option>
          <option>Easy</option>
          <option>Moderate</option>
          <option>Hard</option>
        </select>
      </div>
      <div class="col-md-4">
        <select v-model="filters.location" @change="loadTreks" class="form-select">
          <option value="All">Location: All</option>
          <option v-for="loc in locations" :key="loc" :value="loc">{{ loc }}</option>
        </select>
      </div>
    </div>

    <div class="row g-3">
      <div class="col-md-4" v-for="t in treks" :key="t.id">
        <div class="card border-0 shadow-sm h-100">
          <div class="card-body">
            <h6 class="fw-bold">{{ t.name }}</h6>
            <p class="text-muted small mb-1">{{ t.location }}</p>
            <p class="small mb-1">{{ t.difficulty }} • {{ t.duration_days }} Days</p>
            <p class="small mb-2">Slots Left: <strong>{{ t.available_slots }}</strong></p>
            <button class="btn btn-sm btn-primary w-100" @click="book(t)" :disabled="t.available_slots === 0">
              {{ t.available_slots === 0 ? 'Not Available' : 'Book Now' }}
            </button>
          </div>
        </div>
      </div>
      <div class="col-12" v-if="!treks.length">
        <p class="text-muted text-center py-4">No treks match your search.</p>
      </div>
    </div>
  </AppLayout>
</template>

<script setup>
import { onMounted, reactive, ref } from 'vue'
import AppLayout from '../../components/AppLayout.vue'
import api from '../../api'

const treks = ref([])
const locations = ref([])
const filters = reactive({ q: '', difficulty: 'All', location: 'All' })

async function loadTreks() {
  const res = await api.get('/user/treks', { params: filters })
  treks.value = res.data
}

async function loadLocations() {
  const res = await api.get('/user/treks') // unfiltered, for the dropdown
  locations.value = Array.from(new Set(res.data.map(t => t.location))).sort()
}

let searchTimer = null
function debouncedLoad() {
  clearTimeout(searchTimer)
  searchTimer = setTimeout(loadTreks, 300)
}

async function book(t) {
  try {
    await api.post(`/user/treks/${t.id}/book`)
    await loadTreks()
  } catch (err) {
    alert(err.response?.data?.error || 'Could not book this trek.')
  }
}

onMounted(() => { loadTreks(); loadLocations() })
</script>
