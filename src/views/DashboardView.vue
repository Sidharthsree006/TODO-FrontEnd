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
    <!-- Clean branding sidebar similar to login/register -->
    <aside class="sidebar">
      <div class="logo-section">
        <div class="logo-box">
          <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="20 6 9 17 4 12"/>
          </svg>
        </div>
        <span class="logo-title">Todo App</span>
      </div>

      <nav class="sidebar-nav" style="padding: 0 1.25rem;">
        <div style="margin-bottom: 2rem; padding: 0 0.5rem;">
          <h2 style="font-family: var(--font-secondary); font-size: 1.1rem; font-weight: 700; color: var(--text-primary); margin-bottom: 0.65rem;">Workspace</h2>
          <p style="font-size: 0.85rem; color: var(--text-secondary); line-height: 1.5;">
            Successfully authenticated. Ready to merge teammate dashboard module.
          </p>
        </div>
      </nav>

      <footer class="sidebar-footer">
        <button class="btn-sidebar-logout" @click="handleLogout">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/>
          </svg>
          <span>Sign Out</span>
        </button>
      </footer>
    </aside>

    <main class="main-panel">
      <header class="top-header">
        <h1 class="page-title">Dashboard</h1>
        
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
        <div class="content-card auth-form-card" style="text-align: center; max-width: 540px; padding: 3rem 2rem;">
          <div style="color: var(--color-primary); margin-bottom: 1.5rem;">
            <svg xmlns="http://www.w3.org/2000/svg" width="64" height="64" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
              <path d="M12 22c5.523 0 10-4.477 10-10S17.523 2 12 2 2 6.477 2 12s4.477 10 10 10z"/>
              <path d="m9 12 2 2 4-4"/>
            </svg>
          </div>
          <h2 style="font-family: var(--font-secondary); font-size: 1.5rem; font-weight: 700; color: var(--text-primary); margin-bottom: 0.75rem;">Welcome to Todo App!</h2>
          <p style="color: var(--text-secondary); font-size: 0.95rem; line-height: 1.6; margin-bottom: 2rem;">
            You have successfully signed in as <strong>{{ currentUser.email }}</strong>. 
            This is a placeholder view. Your team's custom calendar and task dashboard will integrate directly into this component.
          </p>
          
          <div style="border-top: 1px solid var(--border-color); padding-top: 1.5rem; display: flex; flex-direction: column; gap: 0.5rem; align-items: center; font-size: 0.85rem; color: var(--text-muted);">
            <div>Session Status: Active</div>
            <div>User Database: localStorage.users</div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>
