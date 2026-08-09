<template>
  <div class="d-flex align-items-center justify-content-center min-vh-100" style="background-color: var(--tma-bg);">
    <div class="card shadow-sm border-0" style="max-width: 420px; width: 100%;">
      <div class="card-body p-4">
        <div class="text-center mb-3">
          <i class="bi bi-signpost-split-fill fs-1 text-primary-tma"></i>
          <h4 class="mt-2 mb-0 fw-bold">Trekking Management</h4>
          <p class="text-muted small">Login to your account</p>
        </div>

        <ul class="nav nav-pills nav-fill mb-3">
          <li class="nav-item" v-for="r in roles" :key="r.value">
            <a class="nav-link" :class="{active: role === r.value}" href="#" @click.prevent="role = r.value">
              <i :class="r.icon" class="me-1"></i>{{ r.label }}
            </a>
          </li>
        </ul>

        <div v-if="errorMsg" class="alert alert-danger py-2">{{ errorMsg }}</div>

        <form @submit.prevent="handleLogin">
          <div class="mb-3">
            <label class="form-label">Email address</label>
            <input v-model="email" type="email" class="form-control" required />
          </div>
          <div class="mb-3">
            <label class="form-label">Password</label>
            <input v-model="password" type="password" class="form-control" required minlength="6" />
          </div>
          <button type="submit" class="btn btn-primary w-100" :disabled="loading">
            <span v-if="loading" class="spinner-border spinner-border-sm me-1"></span>
            Login
          </button>
        </form>

        <div class="text-center mt-3" v-if="role === 'trekker'">
          <small>Don't have an account? <router-link to="/register">Register as User</router-link></small>
        </div>

        <div class="alert alert-info small mt-3 mb-0">
          <i class="bi bi-info-circle me-1"></i>
          Only Users (Trekkers) can register themselves. Trekking Staff are created by Admin.
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useAuthStore } from '../store/auth'

const email = ref('')
const password = ref('')
const role = ref('trekker')
const loading = ref(false)
const errorMsg = ref('')

const roles = [
  { value: 'admin', label: 'Admin', icon: 'bi bi-shield-lock' },
  { value: 'staff', label: 'Staff', icon: 'bi bi-person-badge' },
  { value: 'trekker', label: 'User', icon: 'bi bi-person' },
]

const auth = useAuthStore()
const router = useRouter()

async function handleLogin() {
  errorMsg.value = ''
  loading.value = true
  try {
    const user = await auth.login(email.value, password.value, role.value)
    const dest = user.role === 'trekker' ? 'user-dashboard' : `${user.role}-dashboard`
    router.push({ name: dest })
  } catch (err) {
    errorMsg.value = err.response?.data?.error || 'Login failed. Please try again.'
  } finally {
    loading.value = false
  }
}
</script>
