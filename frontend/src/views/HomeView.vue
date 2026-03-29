<template>
  <!-- Custom Home Content: Full Page Mode -->
  <div v-if="homeContent" class="min-h-screen">
    <iframe
      v-if="isHomeContentUrl"
      :src="homeContent.trim()"
      class="h-screen w-full border-0"
      allowfullscreen
    ></iframe>
    <!-- HTML mode - SECURITY: homeContent is admin-only setting, XSS risk is acceptable -->
    <div v-else v-html="homeContent"></div>
  </div>

  <!-- Default Home Page — Tech Redesign -->
  <div v-else class="home-root relative flex min-h-screen flex-col overflow-hidden">

    <!-- ══════════════════════════════════════════
         BACKGROUND
    ══════════════════════════════════════════ -->
    <div class="pointer-events-none absolute inset-0 overflow-hidden">
      <!-- Glow orbs -->
      <div class="orb orb-tr"></div>
      <div class="orb orb-bl"></div>
      <div class="orb orb-center"></div>
      <!-- Grid -->
      <div class="grid-overlay"></div>
      <!-- Scan line -->
      <div class="scan-line"></div>
    </div>

    <!-- ══════════════════════════════════════════
         HEADER
    ══════════════════════════════════════════ -->
    <header class="home-header relative z-20 px-6 py-4">
      <nav class="mx-auto flex max-w-6xl items-center justify-between">
        <!-- Logo + Brand -->
        <div class="flex items-center gap-3">
          <div class="logo-ring h-9 w-9 overflow-hidden rounded-lg">
            <img :src="siteLogo || '/logo.png'" alt="Logo" class="h-full w-full object-contain" />
          </div>
          <span class="hidden font-mono text-sm font-semibold uppercase tracking-widest text-white/70 sm:block">
            {{ siteName }}
          </span>
        </div>

        <!-- Actions -->
        <div class="flex items-center gap-2">
          <LocaleSwitcher />

          <a
            v-if="docUrl"
            :href="docUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="nav-btn"
            :title="t('home.viewDocs')"
          >
            <Icon name="book" size="sm" class="mr-1" />
            <span class="hidden sm:inline">{{ t('home.docs') }}</span>
          </a>

          <button @click="toggleTheme" class="nav-btn icon-only" :title="isDark ? t('home.switchToLight') : t('home.switchToDark')">
            <Icon v-if="isDark" name="sun" size="sm" />
            <Icon v-else name="moon" size="sm" />
          </button>

          <router-link
            v-if="isAuthenticated"
            :to="dashboardPath"
            class="cta-nav-btn"
          >
            <span class="user-dot">{{ userInitial }}</span>
            {{ t('home.dashboard') }}
            <svg class="h-3 w-3 opacity-60" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
              <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 19.5l15-15m0 0H8.25m11.25 0v11.25" />
            </svg>
          </router-link>
          <router-link v-else to="/login" class="cta-nav-btn">
            {{ t('home.login') }}
          </router-link>
        </div>
      </nav>
    </header>

    <!-- ══════════════════════════════════════════
         MAIN
    ══════════════════════════════════════════ -->
    <main class="relative z-10 flex-1 px-6">

      <!-- ── HERO ────────────────────────────── -->
      <section class="mx-auto max-w-6xl py-20 lg:py-28">
        <div class="flex flex-col items-center gap-14 lg:flex-row lg:gap-20">

          <!-- Text side -->
          <div class="flex-1 text-center lg:text-left">
            <!-- Badge -->
            <div class="mb-7 inline-flex items-center gap-2.5 rounded-full border border-teal-500/25 bg-teal-500/8 px-4 py-1.5 font-mono text-xs font-medium text-teal-400">
              <span class="h-1.5 w-1.5 animate-pulse rounded-full bg-teal-400 shadow-[0_0_6px_#14b8a6]"></span>
              AI API GATEWAY · ENTERPRISE GRADE
            </div>

            <!-- Headline -->
            <h1 class="mb-5 font-black leading-[1.08] tracking-tight">
              <span class="gradient-text block whitespace-nowrap text-[clamp(1.6rem,4.5vw,3.75rem)]">{{ t('home.heroSubtitle') }}</span>
            </h1>

            <!-- Mono accent line -->
            <p class="mb-5 font-mono text-[11px] uppercase tracking-[0.2em] text-teal-600/60">
              // {{ siteName }} · intelligent traffic layer
            </p>

            <p class="mb-10 max-w-lg text-base leading-relaxed text-slate-400 lg:text-lg">
              {{ t('home.heroDescription') }}
            </p>

            <!-- CTA buttons -->
            <div class="flex flex-wrap items-center justify-center gap-4 lg:justify-start">
              <router-link
                :to="isAuthenticated ? dashboardPath : '/login'"
                class="cta-primary inline-flex items-center gap-2 rounded-lg px-7 py-3 text-sm font-semibold text-white"
              >
                {{ isAuthenticated ? t('home.goToDashboard') : t('home.getStarted') }}
                <Icon name="arrowRight" size="sm" :stroke-width="2.5" />
              </router-link>
              <a
                v-if="docUrl"
                :href="docUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="inline-flex items-center gap-2 rounded-lg border border-white/10 px-7 py-3 text-sm font-medium text-slate-300 transition-all hover:border-teal-500/30 hover:text-teal-300"
              >
                <Icon name="book" size="sm" />
                {{ t('home.viewDocs') }}
              </a>
            </div>

            <!-- Stats -->
            <div class="mt-12 flex flex-wrap items-center justify-center gap-8 lg:justify-start">
              <div v-for="s in stats" :key="s.label" class="stat-item">
                <span class="stat-value">{{ s.value }}</span>
                <span class="stat-label">{{ s.label }}</span>
              </div>
            </div>
          </div>

          <!-- Terminal side -->
          <div class="flex flex-1 justify-center lg:justify-end">
            <div class="terminal-wrap">
              <div class="terminal-glow"></div>
              <div class="terminal-window">

                <!-- Header bar -->
                <div class="terminal-header">
                  <div class="terminal-dots">
                    <span class="dot-red"></span>
                    <span class="dot-yellow"></span>
                    <span class="dot-green"></span>
                  </div>
                  <span class="terminal-title">trafficapi · gateway</span>
                  <span class="terminal-live">
                    <span class="h-1.5 w-1.5 animate-pulse rounded-full bg-teal-400 inline-block shadow-[0_0_5px_#14b8a6]"></span>
                    LIVE
                  </span>
                </div>

                <!-- Code body -->
                <div class="terminal-body">
                  <div class="t-line line-1">
                    <span class="t-prompt">→</span>
                    <span class="t-method">POST</span>
                    <span class="t-path">/v1/messages</span>
                    <span class="t-dim ml-auto">claude-3-5-sonnet</span>
                  </div>
                  <div class="t-line line-2">
                    <span class="t-comment">&nbsp;&nbsp;⠿ routing to pool · 3 accounts available</span>
                  </div>
                  <div class="t-line line-3">
                    <span class="t-ok">✓ 200</span>
                    <span class="t-ms">38ms</span>
                    <span class="t-dim">· account[1] selected</span>
                  </div>

                  <div class="t-sep sep-1"></div>

                  <div class="t-line line-4">
                    <span class="t-prompt">→</span>
                    <span class="t-method">POST</span>
                    <span class="t-path">/v1/chat/completions</span>
                    <span class="t-dim ml-auto">gpt-4o</span>
                  </div>
                  <div class="t-line line-5">
                    <span class="t-comment">&nbsp;&nbsp;⠿ load balancing · 2 upstream accounts</span>
                  </div>
                  <div class="t-line line-6">
                    <span class="t-ok">✓ 200</span>
                    <span class="t-ms">57ms</span>
                    <span class="t-dim">· account[3] selected</span>
                  </div>

                  <div class="t-sep sep-2"></div>

                  <div class="t-line line-7">
                    <span class="t-stat">📊 analytics</span>
                    <span class="t-dim">&nbsp;req: 1.4k/min · p99: 68ms · err: 0%</span>
                  </div>
                  <div class="t-line line-8">
                    <span class="t-prompt">_</span>
                    <span class="t-cursor"></span>
                  </div>
                </div>

              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- ── TAGS BAR ─────────────────────────── -->
      <section class="mx-auto max-w-6xl pb-14">
        <div class="flex flex-wrap items-center justify-center gap-3">
          <div v-for="tag in featureTags" :key="tag.text" class="tag-pill">
            <span class="tag-pip"></span>
            {{ tag.text }}
          </div>
        </div>
      </section>

      <!-- ── FEATURES ─────────────────────────── -->
      <section class="mx-auto max-w-6xl pb-16">
        <div class="mb-10 text-center">
          <h2 class="mb-2 text-2xl font-bold text-white md:text-3xl">
            <span class="gradient-text">{{ t('home.solutions.title') }}</span>
          </h2>
          <p class="font-mono text-xs uppercase tracking-widest text-slate-600">
            // {{ t('home.solutions.subtitle') }}
          </p>
        </div>

        <div class="grid gap-4 md:grid-cols-3">
          <div
            v-for="feat in features"
            :key="feat.key"
            class="feat-card group"
          >
            <div class="feat-accent" :style="{ background: feat.color }"></div>
            <div class="feat-corner-tr"></div>
            <div class="feat-corner-bl"></div>

            <div
              class="feat-icon mb-5"
              :style="{ background: feat.iconBg, boxShadow: `0 4px 20px ${feat.glow}` }"
            >
              <component :is="feat.iconComponent" />
            </div>

            <h3 class="mb-2 font-semibold text-white">{{ t(`home.features.${feat.key}`) }}</h3>
            <p class="text-sm leading-relaxed text-slate-500 transition-colors group-hover:text-slate-400">
              {{ t(`home.features.${feat.key}Desc`) }}
            </p>
          </div>
        </div>
      </section>

      <!-- ── PROVIDERS ────────────────────────── -->
      <section class="mx-auto max-w-6xl pb-20">
        <div class="mb-8 text-center">
          <h2 class="mb-2 text-xl font-bold text-white">{{ t('home.providers.title') }}</h2>
          <p class="font-mono text-xs uppercase tracking-[0.2em] text-slate-600">
            // {{ t('home.providers.description') }}
          </p>
        </div>

        <div class="flex flex-wrap items-center justify-center gap-3">
          <div
            v-for="p in providerList"
            :key="p.name"
            class="provider-card"
            :class="{ 'provider-active': p.live, 'provider-inactive': !p.live }"
          >
            <span
              class="provider-status-dot"
              :class="p.live ? 'dot-live' : 'dot-offline'"
            ></span>
            <div class="provider-icon-badge" :style="{ background: p.gradient }">
              <span class="text-[11px] font-bold text-white">{{ p.letter }}</span>
            </div>
            <span class="text-sm font-medium text-slate-300">{{ p.name }}</span>
            <span class="provider-tag" :class="p.live ? 'tag-live' : 'tag-soon'">
              {{ p.live ? t('home.providers.supported') : t('home.providers.soon') }}
            </span>
          </div>
        </div>
      </section>

    </main>

    <!-- ══════════════════════════════════════════
         FOOTER
    ══════════════════════════════════════════ -->
    <footer class="home-footer relative z-10 px-6 py-6">
      <div class="mx-auto flex max-w-6xl flex-col items-center justify-between gap-3 text-center sm:flex-row sm:text-left">
        <p class="font-mono text-xs text-slate-700">
          &copy; {{ currentYear }} {{ siteName }} · {{ t('home.footer.allRightsReserved') }}
        </p>
        <div class="flex items-center gap-5">
          <a
            v-if="docUrl"
            :href="docUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="font-mono text-xs text-slate-700 transition-colors hover:text-teal-400"
          >
            /docs
          </a>
          <a
            :href="githubUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="font-mono text-xs text-slate-700 transition-colors hover:text-teal-400"
          >
            /github
          </a>
        </div>
      </div>
    </footer>

  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, defineComponent, h } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore, useAppStore } from '@/stores'
import LocaleSwitcher from '@/components/common/LocaleSwitcher.vue'
import Icon from '@/components/icons/Icon.vue'

const { t } = useI18n()
const authStore = useAuthStore()
const appStore = useAppStore()

// Site settings
const siteName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'TrafficAPI')
const siteLogo = computed(() => appStore.cachedPublicSettings?.site_logo || appStore.siteLogo || '')
const docUrl = computed(() => appStore.cachedPublicSettings?.doc_url || appStore.docUrl || '')
const homeContent = computed(() => appStore.cachedPublicSettings?.home_content || '')

const isHomeContentUrl = computed(() => {
  const content = homeContent.value.trim()
  return content.startsWith('http://') || content.startsWith('https://')
})

// Theme
const isDark = ref(document.documentElement.classList.contains('dark'))

// GitHub
const githubUrl = 'https://github.com/Wei-Shaw/sub2api'

// Auth
const isAuthenticated = computed(() => authStore.isAuthenticated)
const isAdmin = computed(() => authStore.isAdmin)
const dashboardPath = computed(() => isAdmin.value ? '/admin/dashboard' : '/dashboard')
const userInitial = computed(() => {
  const user = authStore.user
  if (!user?.email) return ''
  return user.email.charAt(0).toUpperCase()
})

const currentYear = computed(() => new Date().getFullYear())

// Stats
const stats = [
  { value: '3+', label: 'AI Providers' },
  { value: '99.9%', label: 'SLA Uptime' },
  { value: '1', label: 'API Key' },
]

// Feature tags
const featureTags = computed(() => [
  { text: t('home.tags.subscriptionToApi') },
  { text: t('home.tags.stickySession') },
  { text: t('home.tags.realtimeBilling') },
])

// SVG icon components (inline)
const IconServer = defineComponent({
  render: () => h('svg', { class: 'h-5 w-5 text-white', fill: 'none', viewBox: '0 0 24 24', stroke: 'currentColor', 'stroke-width': '1.5' }, [
    h('path', { 'stroke-linecap': 'round', 'stroke-linejoin': 'round', d: 'M5.25 14.25h13.5m-13.5 0a3 3 0 01-3-3m3 3a3 3 0 100 6h13.5a3 3 0 100-6m-16.5-3a3 3 0 013-3h13.5a3 3 0 013 3m-19.5 0a4.5 4.5 0 01.9-2.7L5.737 5.1a3.375 3.375 0 012.7-1.35h7.126c1.062 0 2.062.5 2.7 1.35l2.587 3.45a4.5 4.5 0 01.9 2.7m0 0a3 3 0 01-3 3m0 3h.008v.008h-.008v-.008zm0-6h.008v.008h-.008v-.008zm-3 6h.008v.008h-.008v-.008zm0-6h.008v.008h-.008v-.008z' })
  ])
})
const IconRoute = defineComponent({
  render: () => h('svg', { class: 'h-5 w-5 text-white', fill: 'none', viewBox: '0 0 24 24', stroke: 'currentColor', 'stroke-width': '1.5' }, [
    h('path', { 'stroke-linecap': 'round', 'stroke-linejoin': 'round', d: 'M7.5 21L3 16.5m0 0L7.5 12M3 16.5h13.5m0-13.5L21 7.5m0 0L16.5 12M21 7.5H7.5' })
  ])
})
const IconChart = defineComponent({
  render: () => h('svg', { class: 'h-5 w-5 text-white', fill: 'none', viewBox: '0 0 24 24', stroke: 'currentColor', 'stroke-width': '1.5' }, [
    h('path', { 'stroke-linecap': 'round', 'stroke-linejoin': 'round', d: 'M3 13.125C3 12.504 3.504 12 4.125 12h2.25c.621 0 1.125.504 1.125 1.125v6.75C7.5 20.496 6.996 21 6.375 21h-2.25A1.125 1.125 0 013 19.875v-6.75zM9.75 8.625c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125v11.25c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V8.625zM16.5 4.125c0-.621.504-1.125 1.125-1.125h2.25C20.496 3 21 3.504 21 4.125v15.75c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V4.125z' })
  ])
})

// Features config — all icons use inline components, no string name needed
const features = [
  {
    key: 'unifiedGateway',
    iconComponent: IconServer,
    iconBg: 'linear-gradient(135deg, #3b82f6, #1d4ed8)',
    glow: 'rgba(59,130,246,0.3)',
    color: '#3b82f6',
  },
  {
    key: 'multiAccount',
    iconComponent: IconRoute,
    iconBg: 'linear-gradient(135deg, #14b8a6, #0d9488)',
    glow: 'rgba(20,184,166,0.3)',
    color: '#14b8a6',
  },
  {
    key: 'balanceQuota',
    iconComponent: IconChart,
    iconBg: 'linear-gradient(135deg, #a855f7, #7c3aed)',
    glow: 'rgba(168,85,247,0.3)',
    color: '#a855f7',
  },
]

// Providers
const providerList = computed(() => [
  { name: t('home.providers.claude'),       letter: 'C', gradient: 'linear-gradient(135deg,#f97316,#ea580c)', live: true },
  { name: 'GPT',                            letter: 'G', gradient: 'linear-gradient(135deg,#22c55e,#16a34a)', live: true },
  { name: t('home.providers.gemini'),       letter: 'G', gradient: 'linear-gradient(135deg,#3b82f6,#2563eb)', live: true },
  { name: t('home.providers.antigravity'),  letter: 'A', gradient: 'linear-gradient(135deg,#f43f5e,#db2777)', live: true },
  { name: t('home.providers.more'),         letter: '+', gradient: 'linear-gradient(135deg,#475569,#334155)', live: false },
])

// Theme toggle
function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

function initTheme() {
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark' || (!savedTheme && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }
}

onMounted(() => {
  initTheme()
  authStore.checkAuth()
  if (!appStore.publicSettingsLoaded) {
    appStore.fetchPublicSettings()
  }
})
</script>

<style scoped>
/* ══════════════════════════════════════════════
   ROOT — always dark for this landing page
══════════════════════════════════════════════ */
.home-root {
  background: #182a40;
  color: white;
}

/* ══════════════════════════════════════════════
   BACKGROUND LAYERS
══════════════════════════════════════════════ */
.orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(100px);
  pointer-events: none;
}
.orb-tr {
  top: -15%;
  right: -15%;
  width: 600px;
  height: 600px;
  background: radial-gradient(ellipse, rgba(20,184,166,0.18) 0%, transparent 70%);
}
.orb-bl {
  bottom: -15%;
  left: -15%;
  width: 500px;
  height: 500px;
  background: radial-gradient(ellipse, rgba(6,182,212,0.13) 0%, transparent 70%);
}
.orb-center {
  top: 30%;
  left: 40%;
  width: 400px;
  height: 400px;
  background: radial-gradient(ellipse, rgba(20,184,166,0.08) 0%, transparent 70%);
}
.grid-overlay {
  position: absolute;
  inset: 0;
  background-image:
    linear-gradient(rgba(20,184,166,0.09) 1px, transparent 1px),
    linear-gradient(90deg, rgba(20,184,166,0.09) 1px, transparent 1px);
  background-size: 48px 48px;
}
.scan-line {
  position: absolute;
  left: 0;
  right: 0;
  height: 1px;
  background: linear-gradient(90deg, transparent 0%, rgba(20,184,166,0.5) 30%, rgba(20,184,166,0.8) 50%, rgba(20,184,166,0.5) 70%, transparent 100%);
  animation: scan 10s linear infinite;
  box-shadow: 0 0 8px rgba(20,184,166,0.4);
}
@keyframes scan {
  0%   { top: 0%;   opacity: 0;   }
  3%   { opacity: 1; }
  97%  { opacity: 0.4; }
  100% { top: 100%; opacity: 0;   }
}

/* ══════════════════════════════════════════════
   HEADER
══════════════════════════════════════════════ */
.home-header {
  border-bottom: 1px solid rgba(20,184,166,0.12);
  backdrop-filter: blur(12px);
  background: rgba(24,42,64,0.8);
}
.logo-ring {
  box-shadow: 0 0 0 1px rgba(20,184,166,0.25), 0 0 16px rgba(20,184,166,0.15);
}
.nav-btn {
  display: inline-flex;
  align-items: center;
  padding: 6px 10px;
  border-radius: 6px;
  font-size: 12px;
  color: #94a3b8;
  transition: all 0.2s;
}
.nav-btn:hover {
  background: rgba(20,184,166,0.08);
  color: #5eead4;
}
.nav-btn.icon-only {
  padding: 6px 8px;
}
.cta-nav-btn {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 6px 14px;
  border-radius: 7px;
  border: 1px solid rgba(20,184,166,0.3);
  background: rgba(20,184,166,0.08);
  font-size: 12px;
  font-weight: 500;
  color: #5eead4;
  transition: all 0.2s;
}
.cta-nav-btn:hover {
  border-color: rgba(20,184,166,0.6);
  background: rgba(20,184,166,0.15);
  box-shadow: 0 0 14px rgba(20,184,166,0.2);
}
.user-dot {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: linear-gradient(135deg, #14b8a6, #0d9488);
  font-size: 9px;
  font-weight: 700;
  color: white;
}

/* ══════════════════════════════════════════════
   HERO
══════════════════════════════════════════════ */
.gradient-text {
  background: linear-gradient(135deg, #ffffff 0%, #7dd3c8 40%, #14b8a6 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.cta-primary {
  background: linear-gradient(135deg, #0f766e, #14b8a6, #0d9488);
  background-size: 200% 200%;
  animation: gradient-shift 4s ease infinite;
  box-shadow: 0 0 0 1px rgba(20,184,166,0.4), 0 4px 20px rgba(20,184,166,0.25);
  transition: box-shadow 0.25s, transform 0.25s;
}
.cta-primary:hover {
  box-shadow: 0 0 0 1px rgba(20,184,166,0.7), 0 6px 28px rgba(20,184,166,0.4);
  transform: translateY(-2px);
}
@keyframes gradient-shift {
  0%   { background-position: 0% 50%;   }
  50%  { background-position: 100% 50%; }
  100% { background-position: 0% 50%;   }
}

/* Stats */
.stat-item {
  display: flex;
  flex-direction: column;
  gap: 2px;
  position: relative;
}
.stat-item + .stat-item::before {
  content: '';
  position: absolute;
  left: -16px;
  top: 50%;
  transform: translateY(-50%);
  width: 1px;
  height: 28px;
  background: rgba(20,184,166,0.2);
}
.stat-value {
  font-size: 1.6rem;
  font-weight: 900;
  font-family: ui-monospace, monospace;
  color: white;
  line-height: 1;
  letter-spacing: -0.02em;
}
.stat-label {
  font-size: 10px;
  color: #475569;
  text-transform: uppercase;
  letter-spacing: 0.1em;
}

/* ══════════════════════════════════════════════
   TERMINAL
══════════════════════════════════════════════ */
.terminal-wrap {
  position: relative;
  display: inline-block;
}
.terminal-glow {
  position: absolute;
  inset: -30px;
  background: radial-gradient(ellipse at center, rgba(20,184,166,0.1) 0%, transparent 65%);
  pointer-events: none;
  z-index: 0;
}
.terminal-window {
  position: relative;
  z-index: 1;
  width: 460px;
  max-width: 100%;
  background: linear-gradient(160deg, #1a2e48 0%, #162540 100%);
  border-radius: 13px;
  border: 1px solid rgba(20,184,166,0.18);
  box-shadow:
    0 0 0 1px rgba(255,255,255,0.025),
    0 28px 64px rgba(0,0,0,0.7),
    0 0 50px rgba(20,184,166,0.07);
  overflow: hidden;
  transform: perspective(900px) rotateX(1.5deg) rotateY(-2.5deg);
  transition: transform 0.4s ease, box-shadow 0.4s ease;
}
.terminal-window:hover {
  transform: perspective(900px) rotateX(0deg) rotateY(0deg) translateY(-8px);
  box-shadow:
    0 0 0 1px rgba(20,184,166,0.28),
    0 36px 80px rgba(0,0,0,0.75),
    0 0 70px rgba(20,184,166,0.12);
}
.terminal-header {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 11px 16px;
  background: rgba(22,37,60,0.9);
  border-bottom: 1px solid rgba(20,184,166,0.12);
}
.terminal-dots {
  display: flex;
  gap: 6px;
}
.terminal-dots span {
  width: 11px;
  height: 11px;
  border-radius: 50%;
}
.dot-red    { background: #ef4444; }
.dot-yellow { background: #eab308; }
.dot-green  { background: #22c55e; }
.terminal-title {
  flex: 1;
  text-align: center;
  font-size: 11px;
  font-family: ui-monospace, monospace;
  color: #334155;
}
.terminal-live {
  display: flex;
  align-items: center;
  gap: 5px;
  font-size: 10px;
  font-family: ui-monospace, monospace;
  color: #14b8a6;
  letter-spacing: 0.06em;
}
.terminal-body {
  padding: 18px 22px 22px;
  font-family: ui-monospace, 'Fira Code', monospace;
  font-size: 12.5px;
  line-height: 1.85;
}

/* Terminal lines */
.t-line {
  display: flex;
  align-items: center;
  gap: 7px;
  opacity: 0;
  animation: t-appear 0.35s ease forwards;
}
.t-sep {
  height: 1px;
  background: rgba(20,184,166,0.07);
  margin: 5px 0;
  opacity: 0;
  animation: t-appear 0.2s ease forwards;
}
.line-1 { animation-delay: 0.2s;  }
.line-2 { animation-delay: 0.7s;  }
.line-3 { animation-delay: 1.2s;  }
.sep-1  { animation-delay: 1.75s; }
.line-4 { animation-delay: 2.0s;  }
.line-5 { animation-delay: 2.5s;  }
.line-6 { animation-delay: 3.0s;  }
.sep-2  { animation-delay: 3.5s;  }
.line-7 { animation-delay: 3.8s;  }
.line-8 { animation-delay: 4.4s;  }
@keyframes t-appear {
  from { opacity: 0; transform: translateX(-5px); }
  to   { opacity: 1; transform: translateX(0);    }
}

.t-prompt { color: #14b8a6; font-weight: 700; }
.t-method { color: #38bdf8; font-weight: 600; }
.t-path   { color: #bfdbfe; }
.t-comment{ color: #1e3a4a; font-style: italic; }
.t-ok {
  color: #4ade80;
  background: rgba(74,222,128,0.07);
  border: 1px solid rgba(74,222,128,0.15);
  padding: 1px 6px;
  border-radius: 3px;
  font-weight: 700;
  font-size: 11px;
}
.t-ms   { color: #fcd34d; font-size: 11px; }
.t-dim  { color: #1e3a4a; font-size: 11px; }
.t-stat { color: #a78bfa; }

/* Cursor */
.t-cursor {
  display: inline-block;
  width: 7px;
  height: 13px;
  background: #14b8a6;
  box-shadow: 0 0 8px rgba(20,184,166,0.7);
  animation: blink 1.1s step-end infinite;
  border-radius: 1px;
}
@keyframes blink {
  0%,44%  { opacity: 1; }
  50%,94% { opacity: 0; }
  100%    { opacity: 1; }
}

/* ══════════════════════════════════════════════
   FEATURE TAGS
══════════════════════════════════════════════ */
.tag-pill {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  border: 1px solid rgba(20,184,166,0.14);
  background: rgba(20,184,166,0.04);
  border-radius: 9999px;
  padding: 7px 18px;
  font-size: 12px;
  font-weight: 500;
  color: #64748b;
  transition: all 0.2s;
  cursor: default;
}
.tag-pill:hover {
  border-color: rgba(20,184,166,0.3);
  background: rgba(20,184,166,0.08);
  color: #94a3b8;
}
.tag-pip {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: #14b8a6;
  box-shadow: 0 0 7px rgba(20,184,166,0.9);
  flex-shrink: 0;
}

/* ══════════════════════════════════════════════
   FEATURE CARDS
══════════════════════════════════════════════ */
.feat-card {
  position: relative;
  padding: 26px;
  background: rgba(26,46,72,0.65);
  border: 1px solid rgba(255,255,255,0.09);
  border-radius: 13px;
  backdrop-filter: blur(14px);
  overflow: hidden;
  transition: all 0.3s ease;
}
.feat-card:hover {
  border-color: rgba(255,255,255,0.15);
  background: rgba(26,46,72,0.88);
  transform: translateY(-3px);
  box-shadow: 0 20px 50px rgba(0,0,0,0.5);
}
/* left accent bar */
.feat-accent {
  position: absolute;
  left: 0;
  top: 0;
  width: 3px;
  height: 100%;
  opacity: 0.55;
  transition: opacity 0.3s, box-shadow 0.3s;
}
.feat-card:hover .feat-accent {
  opacity: 1;
  box-shadow: 0 0 12px currentColor;
}
/* corner brackets */
.feat-corner-tr,
.feat-corner-bl {
  position: absolute;
  width: 10px;
  height: 10px;
  opacity: 0;
  transition: opacity 0.3s;
}
.feat-corner-tr {
  top: 10px;
  right: 10px;
  border-top: 1px solid rgba(20,184,166,0.45);
  border-right: 1px solid rgba(20,184,166,0.45);
}
.feat-corner-bl {
  bottom: 10px;
  right: 10px;
  border-bottom: 1px solid rgba(20,184,166,0.45);
  border-right: 1px solid rgba(20,184,166,0.45);
}
.feat-card:hover .feat-corner-tr,
.feat-card:hover .feat-corner-bl {
  opacity: 1;
}
.feat-icon {
  width: 44px;
  height: 44px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.3s, box-shadow 0.3s;
}
.feat-card:hover .feat-icon {
  transform: scale(1.08);
}

/* ══════════════════════════════════════════════
   PROVIDERS
══════════════════════════════════════════════ */
.provider-card {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 16px;
  border-radius: 10px;
  border: 1px solid rgba(255,255,255,0.1);
  background: rgba(26,46,72,0.55);
  backdrop-filter: blur(8px);
  transition: all 0.25s;
}
.provider-active:hover {
  border-color: rgba(20,184,166,0.22);
  background: rgba(20,184,166,0.05);
  box-shadow: 0 4px 20px rgba(0,0,0,0.35);
}
.provider-inactive {
  opacity: 0.38;
}
.provider-status-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  flex-shrink: 0;
}
.dot-live {
  background: #34d399;
  box-shadow: 0 0 7px rgba(52,211,153,0.8);
}
.dot-offline {
  background: #334155;
}
.provider-icon-badge {
  width: 28px;
  height: 28px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}
.provider-tag {
  font-size: 10px;
  font-weight: 600;
  padding: 2px 8px;
  border-radius: 9999px;
  letter-spacing: 0.05em;
  text-transform: uppercase;
}
.tag-live {
  background: rgba(20,184,166,0.1);
  color: #2dd4bf;
  border: 1px solid rgba(20,184,166,0.2);
}
.tag-soon {
  background: rgba(71,85,105,0.2);
  color: #475569;
  border: 1px solid rgba(71,85,105,0.25);
}

/* ══════════════════════════════════════════════
   FOOTER
══════════════════════════════════════════════ */
.home-footer {
  border-top: 1px solid rgba(20,184,166,0.1);
  background: rgba(24,42,64,0.8);
}
</style>
