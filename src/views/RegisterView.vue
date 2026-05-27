<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const name = ref('')
const email = ref('')
const password = ref('')
const acceptTerms = ref(false)

const nameError = ref('')
const emailError = ref('')
const passwordError = ref('')
const termsError = ref('')
const registerError = ref('')

const isPasswordVisible = ref(false)
const isLoading = ref(false)
const isSuccess = ref(false)

const togglePasswordVisibility = () => {
  isPasswordVisible.value = !isPasswordVisible.value
}

// Password Strength Evaluation
const passwordStrength = computed(() => {
  const p = password.value
  if (!p) return { score: 0, text: '', class: '' }
  
  let score = 0
  if (p.length >= 8) score++
  if (/[A-Z]/.test(p)) score++
  if (/[0-9]/.test(p)) score++
  if (/[^A-Za-z0-9]/.test(p)) score++

  let text = 'Weak'
  let className = 'weak'

  if (score === 2) {
    text = 'Fair'
    className = 'medium'
  } else if (score === 3) {
    text = 'Good'
    className = 'medium'
  } else if (score === 4) {
    text = 'Strong'
    className = 'strong'
  }

  return { score, text, class: className }
})

const validateName = () => {
  nameError.value = ''
  if (!name.value) {
    nameError.value = 'Full name is required'
    return false
  }
  if (name.value.length < 2) {
    nameError.value = 'Name must be at least 2 characters'
    return false
  }
  return true
}

const validateEmail = () => {
  emailError.value = ''
  if (!email.value) {
    emailError.value = 'Email address is required'
    return false
  }
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!emailRegex.test(email.value)) {
    emailError.value = 'Please enter a valid email address'
    return false
  }
  return true
}

const validatePassword = () => {
  passwordError.value = ''
  if (!password.value) {
    passwordError.value = 'Password is required'
    return false
  }
  if (password.value.length < 8) {
    passwordError.value = 'Password must be at least 8 characters'
    return false
  }
  return true
}

const validateTerms = () => {
  termsError.value = ''
  if (!acceptTerms.value) {
    termsError.value = 'You must accept the Terms of Service'
    return false
  }
  return true
}

const handleRegister = () => {
  registerError.value = ''
  
  const isNameValid = validateName()
  const isEmailValid = validateEmail()
  const isPasswordValid = validatePassword()
  const isTermsValid = validateTerms()

  if (!isNameValid || !isEmailValid || !isPasswordValid || !isTermsValid) return

  isLoading.value = true

  // Simulate server call
  setTimeout(() => {
    const users = JSON.parse(localStorage.getItem('users') || '[]')
    const userExists = users.some(u => u.email.toLowerCase() === email.value.toLowerCase())

    if (userExists) {
      isLoading.value = false
      registerError.value = 'An account with this email already exists'
      return
    }

    // Register User
    users.push({
      name: name.value,
      email: email.value.toLowerCase(),
      password: password.value
    })
    localStorage.setItem('users', JSON.stringify(users))

    isLoading.value = false
    isSuccess.value = true
    setTimeout(() => {
      router.push('/login')
    }, 1500)
  }, 1200)
}
</script>

<template>
  <div class="app-container">
    <!-- Left Sidebar (Branding info, welcome quote, active tabs) -->
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
          <h2 style="font-family: var(--font-secondary); font-size: 1.1rem; font-weight: 700; color: var(--text-primary); margin-bottom: 0.65rem;">Create Account</h2>
          <p style="font-size: 0.85rem; color: var(--text-secondary); line-height: 1.5;">
          </p>
        </div>

        <router-link to="/login" class="nav-item">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M15 3h4a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2h-4"/><polyline points="10 17 15 12 10 7"/><line x1="15" y1="12" x2="3" y2="12"/>
          </svg>
          <span>Sign In</span>
        </router-link>

        <router-link to="/register" class="nav-item active">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/>
          </svg>
          <span>Create Account</span>
        </router-link>
      </nav>
    </aside>

    <!-- Right Content Panel containing the Form card centered -->
    <main class="main-panel">
      <!-- Success Overlay (simulated checkmark loader) -->
      <div class="logout-overlay" v-if="isSuccess">
        <div class="success-icon-wrap" style="width: 80px; height: 80px; margin-bottom: 1rem;">
          <svg class="checkmark" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 52 52">
            <circle class="checkmark__circle" cx="26" cy="26" r="25" fill="none"/>
            <path class="checkmark__check" fill="none" d="M14.1 27.2l7.1 7.2 16.7-16.8"/>
          </svg>
        </div>
        <h3>Account Created!</h3>
        <p>Redirecting to Sign In...</p>
      </div>

      <header class="top-header auth-header">
        <h1 class="page-title">Sign Up</h1>
      </header>

      <!-- Centered Sign Up form wrapper -->
      <div class="auth-content-container">
        <!-- Register Form Card -->
        <div class="content-card auth-form-card">
          <!-- General Register Error -->
          <div class="error-banner" v-if="registerError" style="margin-bottom: 1.5rem;">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/>
            </svg>
            <span>{{ registerError }}</span>
          </div>

          <form @submit.prevent="handleRegister" novalidate>
            <!-- Name Field -->
            <div class="form-group">
              <label for="name" class="form-label">Full Name</label>
              <div class="input-wrapper">
                <span class="input-icon-left">
                  <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M19 21v-2a4 4 0 0 0-4-4H9a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/>
                  </svg>
                </span>
                <input 
                  type="text" 
                  id="name" 
                  class="form-input" 
                  :class="{ 'is-invalid': nameError, 'is-valid': name && !nameError }"
                  placeholder="Sidhu" 
                  v-model="name"
                  @blur="validateName"
                  @input="nameError = ''"
                  required
                />
              </div>
              <span class="feedback-msg error" v-if="nameError">
                {{ nameError }}
              </span>
            </div>

            <!-- Email Field -->
            <div class="form-group">
              <label for="email" class="form-label">Email Address</label>
              <div class="input-wrapper">
                <span class="input-icon-left">
                  <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <rect width="20" height="16" x="2" y="4" rx="2"/><path d="m22 7-8.97 5.7a1.94 1.94 0 0 1-2.06 0L2 7"/>
                  </svg>
                </span>
                <input 
                  type="email" 
                  id="email" 
                  class="form-input" 
                  :class="{ 'is-invalid': emailError, 'is-valid': email && !emailError }"
                  placeholder="name@example.com" 
                  v-model="email"
                  @blur="validateEmail"
                  @input="emailError = ''"
                  required
                />
              </div>
              <span class="feedback-msg error" v-if="emailError">
                {{ emailError }}
              </span>
            </div>

            <!-- Password Field -->
            <div class="form-group">
              <label for="password" class="form-label">Password</label>
              <div class="input-wrapper">
                <span class="input-icon-left">
                  <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <rect width="18" height="11" x="3" y="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/>
                  </svg>
                </span>
                <input 
                  :type="isPasswordVisible ? 'text' : 'password'" 
                  id="password" 
                  class="form-input" 
                  :class="{ 'is-invalid': passwordError, 'is-valid': password && !passwordError }"
                  placeholder="At least 8 characters" 
                  v-model="password"
                  @blur="validatePassword"
                  @input="passwordError = ''"
                  required
                />
                <button 
                  type="button" 
                  class="input-icon-right" 
                  @click="togglePasswordVisibility"
                  aria-label="Toggle password visibility"
                >
                  <svg v-if="isPasswordVisible" xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M9.88 9.88a3 3 0 1 0 4.24 4.24"/><path d="M10.73 5.08A10.43 10.43 0 0 1 12 5c7 0 10 7 10 7a13.16 13.16 0 0 1-1.67 2.68"/><path d="M6.61 6.61A13.52 13.52 0 0 0 2 12s3 7 10 7a9.74 9.74 0 0 0 5.39-1.61"/><line x1="2" y1="2" x2="22" y2="22"/>
                  </svg>
                  <svg v-else xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z"/><circle cx="12" cy="12" r="3"/>
                  </svg>
                </button>
              </div>
              <!-- Live Password Strength Meter -->
              <div class="strength-container" v-if="password">
                <div class="strength-bar-wrapper">
                  <div class="strength-bar" :class="{ 'weak': passwordStrength.score >= 1 }"></div>
                  <div class="strength-bar" :class="{ 'medium': passwordStrength.score >= 2 }"></div>
                  <div class="strength-bar" :class="{ 'medium': passwordStrength.score >= 3 }"></div>
                  <div class="strength-bar" :class="{ 'strong': passwordStrength.score >= 4 }"></div>
                </div>
                <span class="strength-text" v-if="password">
                  Password Strength: <strong :class="passwordStrength.class">{{ passwordStrength.text }}</strong>
                </span>
              </div>
              <span class="feedback-msg error" v-if="passwordError">
                {{ passwordError }}
              </span>
            </div>

            <!-- Terms and Conditions Checkbox -->
            <div class="terms-row">
              <label class="checkbox-container">
                <input type="checkbox" v-model="acceptTerms" @change="termsError = ''"/>
                <span class="checkmark-box" :class="{ 'is-invalid': termsError }"></span>
                <span class="checkbox-label">
                  I agree to the <a href="#" @click.prevent="registerError = 'Terms simulation. You agreed successfully!'">Terms of Service</a> & <a href="#" @click.prevent="registerError = 'Privacy policy simulation.'">Privacy Policy</a>
                </span>
              </label>
              <span class="feedback-msg error" v-if="termsError">
                {{ termsError }}
              </span>
            </div>

            <!-- Submit Button -->
            <button type="submit" class="btn-primary" :disabled="isLoading">
              <span v-if="isLoading" class="spinner"></span>
              <span>{{ isLoading ? 'Creating Account...' : 'Sign Up' }}</span>
              <svg v-if="!isLoading" xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="5" y1="12" x2="19" y2="12"/><polyline points="12 5 19 12 12 19"/>
              </svg>
            </button>
          </form>

          <div class="card-footer">
            <p>Already have an account? <router-link to="/login" class="signin-link">Sign In</router-link></p>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped>
.error-banner {
  background: #c20616;
  border: 1px solid rgba(36, 18, 158, 0.2);
  border-radius: var(--radius-md);
  padding: 0.75rem 1rem;
  color: var(--color-error);
  font-size: 0.85rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  animation: shake 0.4s ease;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-4px); }
  75% { transform: translateX(4px); }
}

.success-icon-wrap {
  display: flex;
  align-items: center;
  justify-content: center;
}

.checkmark__circle {
  stroke-dasharray: 166;
  stroke-dashoffset: 166;
  stroke-width: 2;
  stroke-miterlimit: 10;
  stroke: var(--color-success);
  fill: none;
  animation: stroke 0.6s cubic-bezier(0.65, 0, 0.45, 1) forwards;
}

.checkmark {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  display: block;
  stroke-width: 2;
  stroke: #fff;
  stroke-miterlimit: 10;
  box-shadow: inset 0px 0px 0px var(--color-success);
  animation: fill .4s ease-in-out .4s forwards, scale .3s ease-in-out .9s forwards;
}

.checkmark__check {
  transform-origin: 50% 50%;
  stroke-dasharray: 48;
  stroke-dashoffset: 48;
  animation: stroke 0.3s cubic-bezier(0.65, 0, 0.45, 1) 0.8s forwards;
}

@keyframes stroke {
  100% { stroke-dashoffset: 0; }
}
@keyframes fill {
  100% { box-shadow: inset 0px 0px 0px 40px var(--color-success); }
}
@keyframes scale {
  0%, 100% { transform: none; }
  50% { transform: scale3d(1.1, 1.1, 1); }
}

.weak {
  color: var(--color-error) !important;
}

.medium {
  color: var(--color-warning) !important;
}

.strong {
  color: var(--color-success) !important;
}
</style>
