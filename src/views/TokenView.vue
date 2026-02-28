<script setup lang="ts">
import router, { decodeToken } from '@/router'
import { useCookies } from 'vue3-cookies'

const { cookies } = useCookies()
const url = new URL(window.location.href)
const jwt = url.searchParams.get('jwt') || ''
const origin = url.searchParams.get('origin') || ''

if (!jwt) {
  router.replace(origin || '/')
} else {
  try {
    const props = await decodeToken(jwt)
    cookies.set('jwt', jwt, new Date(props.exp * 1000), '/')
    router.replace(origin || '/')
  } catch (error) {
    console.error('Token verification failed:', error)
    router.replace(origin || '/')
  }
}
</script>

<template>
  <div style="padding: 20px;">
    <p>Processing login...</p>
  </div>
</template>
