<template>
  <Form @submit="login">
    <h2 class="text-2xl mb-3 text-center">Войти</h2>
    <InputText class="mb-3" name="email" type="email" placeholder="Почта" fluid required />
    <InputText class="mb-5" name="password" type="password" placeholder="Пароль" fluid required />
    <Button type="submit" severity="primary" label="Войти" fluid :loading />
    <div
      class="mt-3 text-right text-green-300 font-semibold cursor-pointer"
      @click="$emit('switch-to-signup')"
    >
      Зарегестрироваться
    </div>
  </Form>
</template>

<script setup lang="ts">
import { ref } from 'vue'

import InputText from 'primevue/inputtext'
import { Form } from '@primevue/forms'
import Button from 'primevue/button'

import services from '@/services/services'

import type { UserAuthType } from '@/types/AuthTypes'

import { useToast } from 'primevue/usetoast'
import { useUserStore } from '@/stores/userStore'
import { useRouter } from 'vue-router'

const toast = useToast()
const userStore = useUserStore()
const router = useRouter()

const loading = ref(false)

const login = async ({ values }: { values: unknown }) => {
  try {
    loading.value = true

    const response = await services.auth.login(values as UserAuthType, toast)

    if (response?.user) userStore.user = response.user

    localStorage.setItem('access_token', response?.session.access_token || '')
    localStorage.setItem('refresh_token', response?.session.refresh_token || '')

    router.push({ name: 'home' })
  } catch (error) {
    console.error(error)
  } finally {
    loading.value = false
  }
}
</script>

<style lang="scss" scoped></style>
