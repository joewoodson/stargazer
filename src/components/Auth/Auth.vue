<script setup>
import { ref, toRefs } from 'vue';
import { supabase } from 'app/supabase';

const props = defineProps(['session'])
const { session } = toRefs(props)
const loading = ref(false)
const email = ref('')

const handleLogin = async () => {
  try {
    loading.value = true
    const { error } = await supabase.auth.signInWithOtp({
      email: email.value,
      options: {
        emailRedirectTo: process.env.VUE_APP_SUPABASE_AUTH_REDIRECT_URL
      }
    })
    if (error) throw error
    alert('Check your email for the login link!')
  } catch (error) {
    if (error instanceof Error) {
      alert(error.message)
    }
  } finally {
    loading.value = false
  }
}

const handleLogout = async () => {
  try {
    const { error } = await supabase.auth.signOut()
    if (error) throw error
  } catch (error) {
    if (error instanceof Error) {
      alert(error.message)
    }
  }
}
</script>

<template>
  <div v-if='session'>
    <q-btn v-if='session' dense flat icon="logout" @click="handleLogout">
      <q-tooltip>Logout</q-tooltip>
    </q-btn>
  </div>
  <div v-else>
    <form class="row flex-center flex" @submit.prevent="handleLogin">
          <input class="inputField" required type="email" placeholder="Email" v-model="email" />
          <input
            type="submit"
            class="button block"
            :value="loading ? 'Loading' : 'Login'"
            :disabled="loading"
          />
    </form>
  </div>
</template>
