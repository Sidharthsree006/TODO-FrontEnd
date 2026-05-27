<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const currentUser = ref({ name: '', email: '' })

onMounted(() => {
  const user = JSON.parse(localStorage.getItem('currentUser') || 'null')
  if (user) {
    currentUser.value = user
  } else {
    router.push('/login')
  }
})

const handleLogout = () => {
  localStorage.removeItem('currentUser')
  router.push('/login')
}
</script>

<template>
  <div class="app-container">
    <aside class="sidebar">
      <div class="logo-section">
        <div class="logo-box">
          <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="20 6 9 17 4 12"/>
          </svg>
        </div>
        <span class="logo-title">Todo App</span>
      </div>
    </aside>

    <main class="main-panel">
      <header class="top-header">
        <h1 class="page-title">Dashboard (Placeholder)</h1>
        <div class="profile-section">
          <div class="profile-trigger">
            <div class="header-avatar">
              {{ currentUser.name ? currentUser.name.charAt(0).toUpperCase() : 'U' }}
            </div>
            <span class="profile-name">{{ currentUser.name || 'User' }}</span>
          </div>
        </div>
      </header>

      <div class="auth-content-container">
        <div class="content-card auth-form-card" style="text-align: center; max-width: 640px; padding: 3rem 2rem;">
          <h2 style="font-family: var(--font-secondary); font-size: 1.25rem; font-weight: 700; color: var(--text-primary); margin-bottom: 0.75rem;">Placeholder Dashboard</h2>
          <p style="color: var(--text-secondary); font-size: 0.95rem; line-height: 1.6; margin-bottom: 1.25rem;">
            The real dashboard component is maintained by another teammate. This placeholder keeps routing stable locally and will be replaced when your teammate merges the real dashboard.
          </p>
          <div style="display:flex; gap:0.5rem; justify-content:center; margin-top:1rem;">
            <button class="btn-primary" @click="handleLogout">Sign Out</button>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped>
/* Minimal styling to keep layout consistent */
.auth-content-container { display:flex; justify-content:center; padding:2rem; }
.content-card { border-radius: 8px; box-shadow: 0 1px 4px rgba(0,0,0,0.06); }
.btn-primary { background: var(--color-primary); color: white; padding: 0.6rem 1rem; border: none; border-radius: 6px; cursor: pointer }
</style>
