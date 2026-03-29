<template>
  <div class="auth-root relative flex min-h-screen items-center justify-center overflow-hidden p-4">

    <!-- Background -->
    <div class="pointer-events-none absolute inset-0 overflow-hidden">
      <div class="auth-orb auth-orb-tr"></div>
      <div class="auth-orb auth-orb-bl"></div>
      <div class="auth-grid"></div>
      <div class="auth-scan"></div>
    </div>

    <!-- Content -->
    <div class="relative z-10 w-full max-w-md">

      <!-- Brand -->
      <div class="mb-8 text-center">
        <template v-if="settingsLoaded">
          <!-- Logo -->
          <div class="auth-logo-wrap mb-5 inline-flex h-16 w-16 items-center justify-center overflow-hidden rounded-2xl">
            <img :src="siteLogo || '/logo.png'" alt="Logo" class="h-full w-full object-contain" />
          </div>

          <!-- Site name -->
          <h1 class="auth-site-name mb-2 text-3xl font-black tracking-tight">
            {{ siteName }}
          </h1>

          <!-- Subtitle -->
          <p class="auth-subtitle font-mono text-xs uppercase tracking-widest">
            // {{ siteSubtitle }}
          </p>
        </template>
      </div>

      <!-- Card -->
      <div class="auth-card rounded-2xl p-8">
        <slot />
      </div>

      <!-- Footer links -->
      <div class="mt-6 text-center text-sm">
        <slot name="footer" />
      </div>

      <!-- Copyright -->
      <div class="mt-8 text-center font-mono text-xs text-slate-600">
        &copy; {{ currentYear }} {{ siteName }}. All rights reserved.
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted } from 'vue'
import { useAppStore } from '@/stores'
import { sanitizeUrl } from '@/utils/url'

const appStore = useAppStore()

const siteName = computed(() => appStore.siteName || 'TrafficAPI')
const siteLogo = computed(() => sanitizeUrl(appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || 'Intelligent AI Traffic Routing & Management')
const settingsLoaded = computed(() => appStore.publicSettingsLoaded)

const currentYear = computed(() => new Date().getFullYear())

onMounted(() => {
  appStore.fetchPublicSettings()
})
</script>

<style scoped>
/* ── Root ────────────────────────────────── */
.auth-root {
  background: #182a40;
  color: white;
}

/* ── Background layers ───────────────────── */
.auth-orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(90px);
  pointer-events: none;
}
.auth-orb-tr {
  top: -10%;
  right: -10%;
  width: 420px;
  height: 420px;
  background: radial-gradient(ellipse, rgba(20,184,166,0.18) 0%, transparent 70%);
}
.auth-orb-bl {
  bottom: -10%;
  left: -10%;
  width: 380px;
  height: 380px;
  background: radial-gradient(ellipse, rgba(6,182,212,0.13) 0%, transparent 70%);
}
.auth-grid {
  position: absolute;
  inset: 0;
  background-image:
    linear-gradient(rgba(20,184,166,0.07) 1px, transparent 1px),
    linear-gradient(90deg, rgba(20,184,166,0.07) 1px, transparent 1px);
  background-size: 48px 48px;
}
.auth-scan {
  position: absolute;
  left: 0;
  right: 0;
  height: 1px;
  background: linear-gradient(90deg, transparent 0%, rgba(20,184,166,0.45) 30%, rgba(20,184,166,0.7) 50%, rgba(20,184,166,0.45) 70%, transparent 100%);
  animation: auth-scan 12s linear infinite;
  box-shadow: 0 0 8px rgba(20,184,166,0.35);
}
@keyframes auth-scan {
  0%   { top: 0%;   opacity: 0; }
  4%   { opacity: 1; }
  96%  { opacity: 0.4; }
  100% { top: 100%; opacity: 0; }
}

/* ── Logo ────────────────────────────────── */
.auth-logo-wrap {
  box-shadow:
    0 0 0 1px rgba(20,184,166,0.3),
    0 0 20px rgba(20,184,166,0.18),
    0 8px 24px rgba(0,0,0,0.4);
}

/* ── Brand text ──────────────────────────── */
.auth-site-name {
  background: linear-gradient(135deg, #ffffff 0%, #7dd3c8 45%, #14b8a6 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.auth-subtitle {
  color: rgba(20,184,166,0.5);
}

/* ── Card ────────────────────────────────── */
.auth-card {
  background: rgba(26,46,72,0.72);
  border: 1px solid rgba(255,255,255,0.09);
  backdrop-filter: blur(18px);
  box-shadow:
    0 0 0 1px rgba(20,184,166,0.08),
    0 24px 60px rgba(0,0,0,0.45),
    0 0 50px rgba(20,184,166,0.05);
}

/* ── Slot overrides: make child inputs/links readable ── */
:deep(.input-label) {
  color: #94a3b8;
}
:deep(.input) {
  background: rgba(15,28,48,0.7);
  border-color: rgba(255,255,255,0.1);
  color: white;
}
:deep(.input:focus) {
  border-color: rgba(20,184,166,0.5);
  box-shadow: 0 0 0 3px rgba(20,184,166,0.12);
}
:deep(.input::placeholder) {
  color: #475569;
}
:deep(h2) {
  color: white !important;
}
:deep(.text-gray-500),
:deep(.dark\:text-dark-400) {
  color: #64748b !important;
}
</style>
