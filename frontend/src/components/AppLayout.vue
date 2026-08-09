<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-dark" style="background-color: var(--tma-primary);">
      <div class="container-fluid">
        <span class="navbar-brand">
          <i class="bi bi-signpost-split-fill me-2"></i>Trekking Management Application
        </span>
        <div class="d-flex align-items-center text-white">
          <i class="bi bi-person-circle me-2"></i>
          <span class="me-3">{{ auth.user?.name }} <small class="text-white-50">({{ roleLabel }})</small></span>
          <button class="btn btn-sm btn-outline-light" @click="handleLogout">
            <i class="bi bi-box-arrow-right me-1"></i>Logout
          </button>
        </div>
      </div>
    </nav>

    <div class="container-fluid">
      <div class="row">
        <aside class="col-md-2 sidebar py-3">
          <ul class="nav nav-pills flex-column">
            <li class="nav-item" v-for="item in navItems" :key="item.name">
              <router-link class="nav-link" :to="{ name: item.name }" active-class="active">
                <i :class="item.icon" class="me-2"></i>{{ item.label }}
              </router-link>
            </li>
          </ul>
        </aside>
        <main class="col-md-10 py-4">
          <slot />
        </main>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '../store/auth'

const auth = useAuthStore()
const router = useRouter()

const roleLabel = computed(() => {
  if (auth.role === 'trekker') return 'User'
  if (auth.role === 'staff') return 'Trek Staff'
  return 'Admin'
})

const navMap = {
  admin: [
    { name: 'admin-dashboard', label: 'Dashboard', icon: 'bi bi-speedometer2' },
    { name: 'admin-treks', label: 'Treks', icon: 'bi bi-map' },
    { name: 'admin-staff', label: 'Trekking Staff', icon: 'bi bi-person-badge' },
    { name: 'admin-users', label: 'Users (Trekkers)', icon: 'bi bi-people' },
    { name: 'admin-bookings', label: 'Bookings', icon: 'bi bi-journal-check' },
    { name: 'admin-reports', label: 'Reports', icon: 'bi bi-bar-chart' },
  ],
  staff: [
    { name: 'staff-dashboard', label: 'Dashboard', icon: 'bi bi-speedometer2' },
  ],
  trekker: [
    { name: 'user-dashboard', label: 'Dashboard', icon: 'bi bi-speedometer2' },
    { name: 'user-treks', label: 'Browse Treks', icon: 'bi bi-search' },
    { name: 'user-history', label: 'History', icon: 'bi bi-clock-history' },
    { name: 'user-profile', label: 'Profile', icon: 'bi bi-person' },
  ],
}

const navItems = computed(() => navMap[auth.role] || [])

function handleLogout() {
  auth.logout()
  router.push({ name: 'login' })
}
</script>
