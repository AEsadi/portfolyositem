<script setup>
import { nextTick, onBeforeUnmount, onMounted, ref } from 'vue'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const site = ref(null)
const isDark = ref(false)
const currentYear = new Date().getFullYear()

let animationContext
let mediaContext
let themeTween
const cleanupTasks = []

const projects = [
  {
    index: '01',
    label: 'Konaklama teknolojileri',
    title: 'Multi-tenant otel yönetim sistemi',
    summary:
      'Aynı uygulamayı kullanan farklı oteller için veri ayrımını, kullanıcı işlemlerini ve günlük operasyon ekranlarını geliştiriyorum.',
    focus: 'Servisler · Veri modeli · Operasyon ekranları',
    technologies: ['ASP.NET Core', 'Entity Framework Core', 'SQL Server', 'REST'],
    variant: 'hotel',
  },
  {
    index: '02',
    label: 'Kurumsal sistemler',
    title: 'Navision entegre web ERP',
    summary:
      'ERP tarafında iş akışları, raporlar ve Navision bağlantıları üzerinde çalışıyorum; ihtiyaçları web tarafında kullanılabilir ekranlara taşıyorum.',
    focus: 'İş akışları · Raporlar · Navision entegrasyonu',
    technologies: ['.NET', 'MediatR', 'DevExpress Reporting', 'Navision'],
    variant: 'erp',
  },
]

const capabilities = [
  {
    index: '01',
    title: 'Uygulama geliştirme',
    items: ['C#', '.NET', 'ASP.NET Core', 'Vue.js', 'JavaScript'],
  },
  {
    index: '02',
    title: 'Veri & entegrasyon',
    items: ['SQL Server', 'Entity Framework Core', 'REST API', 'Azure AI Translator'],
  },
  {
    index: '03',
    title: 'Geliştirme süreçleri',
    items: ['GitHub Actions', 'Jenkins', 'IIS', 'Hangfire', 'Git / GitHub'],
  },
]

const tickerItems = ['.NET', 'Vue.js', 'ASP.NET Core', 'SQL Server', 'REST API', 'CI / CD', 'IIS']

function applyTheme(theme, persist = true) {
  const nextTheme = theme === 'light' ? 'light' : 'dark'
  isDark.value = nextTheme === 'dark'
  document.documentElement.dataset.theme = nextTheme
  document
    .querySelector('meta[name="theme-color"]')
    ?.setAttribute('content', isDark.value ? '#11100f' : '#f3eee5')

  if (persist) localStorage.setItem('portfolio-theme', nextTheme)
}

function toggleTheme(event) {
  applyTheme(isDark.value ? 'light' : 'dark')

  if (!window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    themeTween?.kill()
    themeTween = gsap.fromTo(
      event.currentTarget,
      { rotate: -18, scale: 0.9 },
      { rotate: 0, scale: 1, duration: 0.55, ease: 'back.out(2)', overwrite: 'auto' },
    )
  }
}

function registerMagneticTargets(targets) {
  const removers = []

  targets.forEach((target) => {
    const moveX = gsap.quickTo(target, 'x', { duration: 0.45, ease: 'power3.out' })
    const moveY = gsap.quickTo(target, 'y', { duration: 0.45, ease: 'power3.out' })

    const handleMove = (event) => {
      const bounds = target.getBoundingClientRect()
      moveX((event.clientX - bounds.left - bounds.width / 2) * 0.14)
      moveY((event.clientY - bounds.top - bounds.height / 2) * 0.14)
    }

    const handleLeave = () => {
      moveX(0)
      moveY(0)
    }

    target.addEventListener('pointermove', handleMove)
    target.addEventListener('pointerleave', handleLeave)
    removers.push(() => {
      target.removeEventListener('pointermove', handleMove)
      target.removeEventListener('pointerleave', handleLeave)
    })
  })

  return () => removers.forEach((remove) => remove())
}

onMounted(async () => {
  applyTheme(document.documentElement.dataset.theme || 'dark', false)
  await nextTick()

  const root = site.value
  const header = root.querySelector('.site-header')
  const updateHeader = () => header.classList.toggle('is-scrolled', window.scrollY > 24)

  updateHeader()
  window.addEventListener('scroll', updateHeader, { passive: true })
  cleanupTasks.push(() => window.removeEventListener('scroll', updateHeader))

  animationContext = gsap.context(() => {
    const select = gsap.utils.selector(root)
    mediaContext = gsap.matchMedia()

    mediaContext.add('(prefers-reduced-motion: no-preference)', () => {
      const heroTimeline = gsap.timeline({ defaults: { ease: 'power3.out' } })

      heroTimeline
        .from('.site-header', { y: -18, autoAlpha: 0, duration: 0.4 })
        .from('.hero-kicker', { y: 16, autoAlpha: 0, duration: 0.35 }, '-=0.18')
        .from('[data-hero-line]', { y: 28, autoAlpha: 0, duration: 0.58, stagger: 0.08 }, '-=0.18')
        .from('.hero-actions > *', { y: 12, autoAlpha: 0, duration: 0.35, stagger: 0.06 }, '-=0.24')

      gsap.to('.scroll-progress', {
        scaleX: 1,
        ease: 'none',
        scrollTrigger: {
          start: 0,
          end: 'max',
          scrub: true,
        },
      })

      gsap.to('.hero-stars', {
        yPercent: -5,
        ease: 'none',
        scrollTrigger: { trigger: '.hero', start: 'top top', end: 'bottom top', scrub: 0.9 },
      })

      gsap.to('.marquee-track', {
        xPercent: -14,
        ease: 'none',
        scrollTrigger: {
          trigger: '.tech-marquee',
          start: 'top bottom',
          end: 'bottom top',
          scrub: 0.8,
        },
      })

      select('[data-reveal]').forEach((element) => {
        gsap.from(element, {
          y: 56,
          opacity: 0,
          duration: 1,
          ease: 'power3.out',
          scrollTrigger: {
            trigger: element,
            start: 'top 86%',
            once: true,
          },
        })
      })

      select('.project-story').forEach((project) => {
        const rule = project.querySelector('.project-rule')
        const number = project.querySelector('.project-visual-number')

        gsap.from(rule, {
          scaleX: 0,
          transformOrigin: 'left center',
          duration: 1.1,
          ease: 'power3.out',
          scrollTrigger: { trigger: project, start: 'top 78%', once: true },
        })

        gsap.to(number, {
          yPercent: -14,
          ease: 'none',
          scrollTrigger: { trigger: project, start: 'top bottom', end: 'bottom top', scrub: 0.8 },
        })
      })

      gsap.to('.work-progress-fill', {
        scaleY: 1,
        ease: 'none',
        scrollTrigger: {
          trigger: '.project-stories',
          start: 'top 65%',
          end: 'bottom 45%',
          scrub: 0.6,
        },
      })

      gsap.to('.contact-orbit', {
        rotate: 95,
        scale: 1.08,
        ease: 'none',
        scrollTrigger: { trigger: '.contact-section', start: 'top bottom', end: 'bottom bottom', scrub: 0.8 },
      })
    })

    mediaContext.add(
      '(min-width: 960px) and (hover: hover) and (pointer: fine) and (prefers-reduced-motion: no-preference)',
      () => {
        const removeMagneticTargets = registerMagneticTargets(select('[data-magnetic]'))

        gsap.to('.hero-name-first', {
          xPercent: -4,
          ease: 'none',
          scrollTrigger: { trigger: '.hero', start: 'top top', end: 'bottom top', scrub: 0.7 },
        })

        gsap.to('.hero-name-last', {
          xPercent: 5,
          ease: 'none',
          scrollTrigger: { trigger: '.hero', start: 'top top', end: 'bottom top', scrub: 0.7 },
        })

        return () => {
          removeMagneticTargets()
        }
      },
    )

    mediaContext.add('(prefers-reduced-motion: reduce)', () => {
      gsap.set('.scroll-progress', { scaleX: 1 })
      gsap.set('.work-progress-fill', { scaleY: 1 })
    })
  }, root)

  document.fonts?.ready.then(() => {
    if (site.value) ScrollTrigger.refresh()
  })
})

onBeforeUnmount(() => {
  cleanupTasks.forEach((cleanup) => cleanup())
  themeTween?.kill()
  mediaContext?.revert()
  animationContext?.revert()
})
</script>

<template>
  <div ref="site" class="site-shell">
    <a class="skip-link" href="#main-content">İçeriğe geç</a>

    <header class="site-header">
      <a class="brand" href="#top" aria-label="Esad İkiz ana sayfa">
        <span class="brand-mark" aria-hidden="true">Eİ</span>
        <span class="brand-copy">
          <strong>Esad İkiz</strong>
          <small>Full Stack Developer</small>
        </span>
      </a>

      <nav class="site-nav" aria-label="Ana menü">
        <a href="#projeler">Projeler</a>
        <a href="#yetkinlikler">Yetkinlikler</a>
      </nav>

      <div class="header-actions">
        <button
          class="theme-toggle"
          type="button"
          :aria-label="isDark ? 'Açık temaya geç' : 'Koyu temaya geç'"
          :aria-pressed="isDark"
          @click="toggleTheme"
        >
          <svg v-if="isDark" aria-hidden="true" viewBox="0 0 24 24">
            <circle cx="12" cy="12" r="3.5" />
            <path d="M12 2v2M12 20v2M4.93 4.93l1.42 1.42M17.65 17.65l1.42 1.42M2 12h2M20 12h2M4.93 19.07l1.42-1.42M17.65 6.35l1.42-1.42" />
          </svg>
          <svg v-else aria-hidden="true" viewBox="0 0 24 24">
            <path d="M20.2 15.2A8.5 8.5 0 0 1 8.8 3.8 8.5 8.5 0 1 0 20.2 15.2Z" />
          </svg>
        </button>
        <a class="header-contact icon-link icon-link--mail" href="mailto:ikizesad99@gmail.com" aria-label="E-posta gönder">
          <svg aria-hidden="true" viewBox="0 0 24 24">
            <rect x="3" y="5" width="18" height="14" rx="2" />
            <path d="m4 7 8 6 8-6" />
          </svg>
        </a>
      </div>

      <span class="scroll-progress" aria-hidden="true"></span>
    </header>

    <main id="main-content" tabindex="-1">
      <section id="top" class="hero" aria-labelledby="hero-title">
        <div class="hero-grid" aria-hidden="true"></div>
        <div class="hero-stars" aria-hidden="true"><i></i><i></i><i></i></div>

        <div class="hero-copy">
          <p class="eyebrow hero-kicker">Full Stack Developer</p>
          <h1 id="hero-title">
            <span class="hero-line" data-hero-line><span class="hero-name-first">ESAD</span></span>
            <span class="hero-line hero-line-offset" data-hero-line><span class="hero-name-last">İKİZ</span></span>
          </h1>

          <div class="hero-detail">
            <div class="hero-actions">
              <a class="button button-primary" href="#projeler">
                <span>Projeler</span>
                <span aria-hidden="true">↘</span>
              </a>
              <a class="quiet-link icon-link" href="https://github.com/AEsadi" target="_blank" rel="noreferrer" aria-label="GitHub profili">
                <svg aria-hidden="true" viewBox="0 0 24 24"><path d="M12 2.7a9.3 9.3 0 0 0-2.94 18.12c.47.09.64-.2.64-.46v-1.8c-2.6.57-3.15-1.1-3.15-1.1-.42-1.08-1.04-1.36-1.04-1.36-.85-.58.06-.57.06-.57.94.07 1.43.96 1.43.96.84 1.42 2.2 1.01 2.73.77.08-.6.33-1.01.6-1.24-2.08-.23-4.27-1.04-4.27-4.64 0-1.03.37-1.87.97-2.53-.1-.24-.42-1.2.09-2.5 0 0 .8-.26 2.56.96A8.9 8.9 0 0 1 12 6.55c.78 0 1.56.1 2.3.31 1.76-1.22 2.55-.96 2.55-.96.51 1.3.19 2.26.1 2.5.6.66.96 1.5.96 2.53 0 3.61-2.2 4.4-4.29 4.63.34.29.63.84.63 1.7v2.52c0 .26.17.56.65.46A9.3 9.3 0 0 0 12 2.7Z" /></svg>
              </a>
              <a class="quiet-link icon-link" href="https://www.linkedin.com/in/esad-ikiz-b971662a9/" target="_blank" rel="noreferrer" aria-label="LinkedIn profili">
                <svg aria-hidden="true" viewBox="0 0 24 24"><path d="M6.1 8.6H3.2V21h2.9V8.6ZM4.65 3A1.68 1.68 0 1 0 4.7 6.35 1.68 1.68 0 0 0 4.65 3ZM20.8 13.9c0-3.75-2-5.5-4.68-5.5-2.16 0-3.13 1.19-3.67 2.02V8.6H9.56V21h2.9v-6.14c0-1.62.3-3.18 2.31-3.18 1.98 0 2 1.85 2 3.28V21h2.9v-7.1Z" /></svg>
              </a>
            </div>
          </div>
        </div>
      </section>

      <div class="tech-marquee" aria-label="Kullandığım temel teknolojiler">
        <div class="marquee-track">
          <div v-for="group in 2" :key="group" class="marquee-group" :aria-hidden="group === 2">
            <span v-for="item in tickerItems" :key="`${group}-${item}`">{{ item }} <i aria-hidden="true">✦</i></span>
          </div>
        </div>
      </div>

      <section id="projeler" class="work-section section-pad" aria-label="İş deneyimi ve seçili projeler">
        <header class="work-heading" data-reveal>
          <div class="section-label section-label-light">
            <span>01</span>
            <span>İş deneyimi</span>
          </div>
        </header>

        <div class="work-layout">
          <aside class="work-aside" data-reveal>
            <div class="work-progress" aria-hidden="true"><span class="work-progress-fill"></span></div>
            <p class="eyebrow eyebrow-light">Deneyim</p>
            <h3>ST ERP<br />Danışmanlık</h3>
            <p>Full Stack Developer</p>
            <span>Haziran 2025 — Günümüz</span>
            <span>İstanbul, Kadıköy</span>
            <a href="https://st-erp.com" target="_blank" rel="noreferrer">st-erp.com ↗</a>
          </aside>

          <div class="project-stories">
            <article v-for="project in projects" :key="project.title" class="project-story">
              <span class="project-rule" aria-hidden="true"></span>
              <div class="project-visual" :class="`project-visual-${project.variant}`" aria-hidden="true">
                <span class="project-visual-number">{{ project.index }}</span>
                <svg v-if="project.variant === 'hotel'" class="project-art" viewBox="60 60 520 520" role="presentation">
                  <path class="project-tenant-connection" d="M208 170 260 242" />
                  <path class="project-tenant-connection" d="M432 170 380 242" />
                  <path class="project-tenant-connection" d="M208 430 260 398" />
                  <path class="project-tenant-connection" d="M432 430 380 398" />

                  <g class="project-tenant-card" transform="translate(88 102)">
                    <rect width="120" height="68" rx="8" />
                    <circle cx="20" cy="22" r="4" />
                    <text x="32" y="26">HOTEL 01</text>
                    <text class="project-tenant-subtitle" x="20" y="49">TENANT A</text>
                  </g>
                  <g class="project-tenant-card" transform="translate(432 102)">
                    <rect width="120" height="68" rx="8" />
                    <circle cx="20" cy="22" r="4" />
                    <text x="32" y="26">HOTEL 02</text>
                    <text class="project-tenant-subtitle" x="20" y="49">TENANT B</text>
                  </g>
                  <g class="project-tenant-card" transform="translate(88 430)">
                    <rect width="120" height="68" rx="8" />
                    <circle cx="20" cy="22" r="4" />
                    <text x="32" y="26">HOTEL 03</text>
                    <text class="project-tenant-subtitle" x="20" y="49">TENANT C</text>
                  </g>
                  <g class="project-tenant-card" transform="translate(432 430)">
                    <rect width="120" height="68" rx="8" />
                    <circle cx="20" cy="22" r="4" />
                    <text x="32" y="26">HOTEL 04</text>
                    <text class="project-tenant-subtitle" x="20" y="49">TENANT D</text>
                  </g>

                  <g class="project-tenant-core">
                    <rect x="190" y="242" width="260" height="156" rx="12" />
                    <text class="project-tenant-core-kicker" x="320" y="290" text-anchor="middle">SHARED CORE</text>
                    <text class="project-tenant-core-title" x="320" y="326" text-anchor="middle">PLATFORM</text>
                    <path d="M240 350H400" />
                    <text class="project-tenant-core-detail" x="320" y="374" text-anchor="middle">ONE APPLICATION · ISOLATED DATA</text>
                  </g>
                </svg>
                <svg v-else class="project-art" viewBox="60 60 520 520" role="presentation">
                  <path class="project-erp-connection" d="M230 320H260" />
                  <path class="project-erp-connection" d="M440 276 470 176M440 320H470M440 364 470 464" />

                  <g class="project-erp-source" transform="translate(90 265)">
                    <rect width="140" height="110" rx="8" />
                    <circle cx="22" cy="27" r="5" />
                    <text x="36" y="32">NAVISION</text>
                    <text class="project-erp-subtitle" x="22" y="65">ERP SOURCE</text>
                    <path d="M22 80H118" />
                    <text class="project-erp-detail" x="22" y="98">FINANCE · STOCK</text>
                  </g>

                  <g class="project-erp-hub">
                    <rect x="260" y="230" width="180" height="180" rx="12" />
                    <text class="project-erp-hub-kicker" x="350" y="282" text-anchor="middle">WEB ERP</text>
                    <text class="project-erp-hub-title" x="350" y="322" text-anchor="middle">INTEGRATION</text>
                    <path d="M292 346H408" />
                    <text class="project-erp-hub-detail" x="350" y="374" text-anchor="middle">BUSINESS LAYER</text>
                  </g>

                  <g class="project-erp-module" transform="translate(470 142)">
                    <rect width="110" height="68" rx="8" />
                    <circle cx="18" cy="22" r="4" />
                    <text x="30" y="27">RAPORLAR</text>
                    <text class="project-erp-module-subtitle" x="18" y="49">REPORTING</text>
                  </g>
                  <g class="project-erp-module" transform="translate(470 286)">
                    <rect width="110" height="68" rx="8" />
                    <circle cx="18" cy="22" r="4" />
                    <text x="30" y="27">AKIŞLAR</text>
                    <text class="project-erp-module-subtitle" x="18" y="49">WORKFLOWS</text>
                  </g>
                  <g class="project-erp-module" transform="translate(470 430)">
                    <rect width="110" height="68" rx="8" />
                    <circle cx="18" cy="22" r="4" />
                    <text x="30" y="27">EKRANLAR</text>
                    <text class="project-erp-module-subtitle" x="18" y="49">USERS</text>
                  </g>
                </svg>
                <div class="project-diagram">
                  <span v-for="item in project.technologies" :key="item">{{ item }}</span>
                </div>
              </div>
              <div class="project-copy" data-reveal>
                <p class="eyebrow eyebrow-light">{{ project.label }}</p>
                <h3>{{ project.title }}</h3>
                <p>{{ project.summary }}</p>
                <div class="project-focus">
                  <span>Odak</span>
                  <strong>{{ project.focus }}</strong>
                </div>
                <ul :aria-label="`${project.title} teknolojileri`">
                  <li v-for="technology in project.technologies" :key="technology">{{ technology }}</li>
                </ul>
              </div>
            </article>
          </div>
        </div>
      </section>

      <section id="yetkinlikler" class="capabilities-section section-pad" aria-labelledby="capabilities-title">
        <header class="capabilities-heading">
          <div class="section-label" data-reveal>
            <span>02</span>
            <span>Yetkinlikler</span>
          </div>
          <div data-reveal>
            <p class="eyebrow">Teknolojiler & araçlar</p>
            <h2 id="capabilities-title">Günlük tech stack’im.</h2>
          </div>
        </header>

        <div class="capability-list">
          <article v-for="capability in capabilities" :key="capability.title" class="capability-row" data-reveal>
            <span>{{ capability.index }}</span>
            <div>
              <h3>{{ capability.title }}</h3>
            </div>
            <ul>
              <li v-for="item in capability.items" :key="item">{{ item }}</li>
            </ul>
          </article>
        </div>
      </section>

      <section class="profile-section section-pad">
        <div class="section-label" data-reveal>
          <span>03</span>
          <span>Profil</span>
        </div>
        <div class="profile-main">
          <p class="eyebrow" data-reveal>Eğitim</p>
          <h2 data-reveal>Eğitim</h2>
          <div class="profile-details" data-reveal>
            <div>
              <span>Balıkesir Üniversitesi</span>
              <strong>Bilgisayar Programcılığı · Ön Lisans</strong>
            </div>
            <div>
              <span>Eylül 2024 — Haziran 2026</span>
              <strong>GPA 3.44</strong>
            </div>
          </div>
        </div>
      </section>

      <section class="contact-section section-pad" aria-labelledby="contact-title">
        <div class="contact-orbit" aria-hidden="true"><span></span><span></span></div>
        <p class="eyebrow eyebrow-light" data-reveal>İletişim</p>
        <h2 id="contact-title" data-reveal>İletişim<br /><em>bilgileri.</em></h2>
        <div class="contact-bottom" data-reveal>
          <div class="contact-links" aria-label="İletişim bağlantıları">
            <a class="icon-link icon-link--mail" href="mailto:ikizesad99@gmail.com" aria-label="E-posta gönder">
              <svg aria-hidden="true" viewBox="0 0 24 24"><rect x="3" y="5" width="18" height="14" rx="2" /><path d="m4 7 8 6 8-6" /></svg>
            </a>
            <a class="icon-link" href="https://www.linkedin.com/in/esad-ikiz-b971662a9/" target="_blank" rel="noreferrer" aria-label="LinkedIn profili">
              <svg aria-hidden="true" viewBox="0 0 24 24"><path d="M6.1 8.6H3.2V21h2.9V8.6ZM4.65 3A1.68 1.68 0 1 0 4.7 6.35 1.68 1.68 0 0 0 4.65 3ZM20.8 13.9c0-3.75-2-5.5-4.68-5.5-2.16 0-3.13 1.19-3.67 2.02V8.6H9.56V21h2.9v-6.14c0-1.62.3-3.18 2.31-3.18 1.98 0 2 1.85 2 3.28V21h2.9v-7.1Z" /></svg>
            </a>
            <a class="icon-link" href="https://github.com/AEsadi" target="_blank" rel="noreferrer" aria-label="GitHub profili">
              <svg aria-hidden="true" viewBox="0 0 24 24"><path d="M12 2.7a9.3 9.3 0 0 0-2.94 18.12c.47.09.64-.2.64-.46v-1.8c-2.6.57-3.15-1.1-3.15-1.1-.42-1.08-1.04-1.36-1.04-1.36-.85-.58.06-.57.06-.57.94.07 1.43.96 1.43.96.84 1.42 2.2 1.01 2.73.77.08-.6.33-1.01.6-1.24-2.08-.23-4.27-1.04-4.27-4.64 0-1.03.37-1.87.97-2.53-.1-.24-.42-1.2.09-2.5 0 0 .8-.26 2.56.96A8.9 8.9 0 0 1 12 6.55c.78 0 1.56.1 2.3.31 1.76-1.22 2.55-.96 2.55-.96.51 1.3.19 2.26.1 2.5.6.66.96 1.5.96 2.53 0 3.61-2.2 4.4-4.29 4.63.34.29.63.84.63 1.7v2.52c0 .26.17.56.65.46A9.3 9.3 0 0 0 12 2.7Z" /></svg>
            </a>
          </div>
        </div>
      </section>
    </main>

    <footer class="site-footer">
      <span>© {{ currentYear }} Abdurrahman Esad İkiz</span>
      <a href="#top">Yukarı dön ↑</a>
    </footer>
  </div>
</template>
