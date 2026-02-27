<script setup lang="ts">
import router, { decodeToken } from '@/router'
import { useCookies } from 'vue3-cookies'

const { cookies } = useCookies()
const url = new URL(window.location.href)
const jwt = url.searchParams.get('jwt') || ''
const origin = url.searchParams.get('origin') || ''

console.log('[TokenView] URL:', url.href)
console.log('[TokenView] JWT present:', !!jwt, 'length:', jwt.length)
console.log('[TokenView] Origin:', origin)

if (!jwt) {
  console.error('[TokenView] No JWT in URL params')
  router.replace(origin || '/')
} else {
  // Log the raw JWT parts
  const parts = jwt.split('.')
  console.log('[TokenView] JWT parts:', parts.length)
  if (parts.length === 3) {
    try {
      const header = JSON.parse(atob(parts[0].replace(/-/g, '+').replace(/_/g, '/')))
      const payload = JSON.parse(atob(parts[1].replace(/-/g, '+').replace(/_/g, '/')))
      console.log('[TokenView] JWT header:', header)
      console.log('[TokenView] JWT payload:', payload)
      console.log('[TokenView] JWT payload.student:', payload.student, typeof payload.student)
      console.log('[TokenView] JWT payload.exp:', payload.exp, 'now:', Math.floor(Date.now() / 1000))
      console.log('[TokenView] JWT payload.unique_id:', payload.unique_id)
    } catch (e) {
      console.error('[TokenView] Failed to decode JWT parts:', e)
    }
  }

  // Log public key
  const pubKey = import.meta.env.VITE_JWT_PUBLIC_KEY
  console.log('[TokenView] Public key present:', !!pubKey, 'length:', pubKey?.length)
  console.log('[TokenView] Public key first 20 chars:', pubKey?.substring(0, 20))

  try {
    const props = await decodeToken(jwt)
    console.log('[TokenView] decodeToken succeeded:', props)
    cookies.set('jwt', jwt, new Date(props.exp * 1000), '/')
    console.log('[TokenView] Cookie set, redirecting to:', origin)

    // Verify cookie was actually set
    const verify = cookies.get('jwt')
    console.log('[TokenView] Cookie readback:', !!verify, 'length:', verify?.length)

    router.replace(origin || '/')
  } catch (error) {
    console.error('[TokenView] decodeToken FAILED:', error)
    console.error('[TokenView] Error name:', (error as Error).name)
    console.error('[TokenView] Error message:', (error as Error).message)
    router.replace(origin || '/')
  }
}
</script>

<template>
  <div style="padding: 20px;">
    <p>Processing login...</p>
  </div>
</template>
