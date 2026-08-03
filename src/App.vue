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
      'Farklı otellerin aynı ürün içinde güvenle çalıştığı yapıda veri modeli, kullanıcı işlemleri ve operasyon akışlarını geliştiriyorum.',
    focus: 'Backend servisleri · Veri modeli · Kullanıcı akışları',
    technologies: ['ASP.NET Core', 'Entity Framework Core', 'SQL Server', 'REST'],
    variant: 'hotel',
  },
  {
    index: '02',
    label: 'Kurumsal sistemler',
    title: 'Navision entegre web ERP',
    summary:
      'Kurumsal ERP sisteminde backend iş akışları, raporlama ve Navision entegrasyonlarını tek, sürdürülebilir ürün akışında buluşturuyorum.',
    focus: 'İş akışları · Raporlama · Sistem entegrasyonu',
    technologies: ['.NET', 'MediatR', 'DevExpress Reporting', 'Navision'],
    variant: 'erp',
  },
]

const capabilities = [
  {
    index: '01',
    title: 'Uygulama geliştirme',
    description: 'Arayüzden API katmanına uzanan, ürünün tamamını gözeten web geliştirme.',
    items: ['C#', '.NET', 'ASP.NET Core', 'Vue.js', 'JavaScript'],
  },
  {
    index: '02',
    title: 'Veri & entegrasyon',
    description: 'İş kurallarını doğru veri modeli ve güvenilir servis bağlantılarıyla kurma.',
    items: ['SQL Server', 'Entity Framework Core', 'REST API', 'Azure AI Translator'],
  },
  {
    index: '03',
    title: 'Teslim & operasyon',
    description: 'Kodun üretime uzanan yolunu izleyen, sorun çözmeye odaklı geliştirme yaklaşımı.',
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
      const heroTimeline = gsap.timeline({ defaults: { ease: 'power4.out' } })

      heroTimeline
        .from('.site-header', { yPercent: -110, autoAlpha: 0, duration: 0.85 })
        .from('.hero-kicker', { y: 22, autoAlpha: 0, duration: 0.7 }, '-=0.35')
        .from(
          '[data-hero-line] > span',
          { yPercent: 115, rotate: 2, duration: 1.15, stagger: 0.1 },
          '-=0.42',
        )
        .from('.hero-summary', { y: 26, autoAlpha: 0, duration: 0.75 }, '-=0.65')
        .from('.hero-actions > *', { y: 18, autoAlpha: 0, duration: 0.65, stagger: 0.09 }, '-=0.5')
        .from('.hero-visual-inner', { y: 24, autoAlpha: 0, duration: 1.1 }, '-=1')
        .from('.hero-meta > *', { y: 14, autoAlpha: 0, duration: 0.55, stagger: 0.08 }, '-=0.55')

      gsap.from('.system-path', {
        strokeDashoffset: 1,
        duration: 1.5,
        stagger: 0.09,
        ease: 'power2.inOut',
        delay: 0.55,
      })

      gsap.from('.orbit-ring-a', {
        rotate: -24,
        duration: 1.8,
        ease: 'power3.out',
        transformOrigin: '50% 50%',
      })

      gsap.from('.orbit-ring-b', {
        rotate: 32,
        duration: 2,
        ease: 'power3.out',
        transformOrigin: '50% 50%',
      })

      gsap.fromTo(
        '.visual-scanner',
        { yPercent: 0, autoAlpha: 0 },
        {
          yPercent: 620,
          autoAlpha: 0.65,
          duration: 2.2,
          ease: 'power2.inOut',
        },
      )

      gsap.to('.scroll-progress', {
        scaleX: 1,
        ease: 'none',
        scrollTrigger: {
          start: 0,
          end: 'max',
          scrub: true,
        },
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
        const visual = select('.hero-visual')[0]
        const visualInner = select('.hero-visual-inner')[0]
        const moveX = gsap.quickTo(visualInner, 'x', { duration: 0.9, ease: 'power3.out' })
        const moveY = gsap.quickTo(visualInner, 'y', { duration: 0.9, ease: 'power3.out' })

        const handleVisualMove = (event) => {
          const bounds = visual.getBoundingClientRect()
          moveX(((event.clientX - bounds.left) / bounds.width - 0.5) * 28)
          moveY(((event.clientY - bounds.top) / bounds.height - 0.5) * 28)
        }

        const handleVisualLeave = () => {
          moveX(0)
          moveY(0)
        }

        visual.addEventListener('pointermove', handleVisualMove)
        visual.addEventListener('pointerleave', handleVisualLeave)
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

        gsap.to('.hero-visual-inner', {
          rotate: 12,
          scale: 1.08,
          yPercent: 12,
          ease: 'none',
          scrollTrigger: { trigger: '.hero', start: 'top top', end: 'bottom top', scrub: 0.8 },
        })

        return () => {
          visual.removeEventListener('pointermove', handleVisualMove)
          visual.removeEventListener('pointerleave', handleVisualLeave)
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
        <a href="#yaklasim">Yaklaşım</a>
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
        <a class="header-contact" href="mailto:ikizesad99@gmail.com">Konuşalım <span aria-hidden="true">↗</span></a>
      </div>

      <span class="scroll-progress" aria-hidden="true"></span>
    </header>

    <main id="main-content" tabindex="-1">
      <section id="top" class="hero" aria-labelledby="hero-title">
        <div class="hero-grid" aria-hidden="true"></div>

        <div class="hero-copy">
          <p class="eyebrow hero-kicker"><span>İstanbul / TR</span> Full Stack Developer</p>
          <h1 id="hero-title">
            <span class="hero-line" data-hero-line><span class="hero-name-first">ESAD</span></span>
            <span class="hero-line hero-line-offset" data-hero-line><span class="hero-name-last">İKİZ</span></span>
          </h1>

          <div class="hero-detail">
            <p class="hero-summary">
              .NET ve Vue ile kurumsal iş akışlarını yalın, sürdürülebilir web ürünlerine dönüştürüyorum.
            </p>
            <div class="hero-actions">
              <a class="button button-primary" href="#projeler" data-magnetic>
                <span>Seçili projeler</span>
                <span aria-hidden="true">↘</span>
              </a>
              <a class="quiet-link" href="https://github.com/AEsadi" target="_blank" rel="noreferrer">GitHub ↗</a>
              <a class="quiet-link" href="https://www.linkedin.com/in/esad-ikiz-b971662a9/" target="_blank" rel="noreferrer">LinkedIn ↗</a>
            </div>
          </div>
        </div>

        <div class="hero-visual" aria-hidden="true">
          <div class="hero-visual-inner">
            <span class="visual-caption">Full-stack system / live</span>
            <span class="visual-scanner"></span>
            <svg class="system-map" viewBox="0 0 640 640" role="presentation">
              <circle class="orbit-ring orbit-ring-a" cx="320" cy="320" r="236" />
              <circle class="orbit-ring orbit-ring-b" cx="320" cy="320" r="166" />
              <path class="system-path" pathLength="1" d="M117 205 232 275 320 155 435 253 531 185" />
              <path class="system-path" pathLength="1" d="M109 421 227 369 320 482 421 378 535 445" />
              <path class="system-path system-path-soft" pathLength="1" d="M232 275 227 369M435 253 421 378M320 155V482" />
              <g class="system-node" transform="translate(117 205)"><circle r="9" /><text x="18" y="5">VUE</text></g>
              <g class="system-node" transform="translate(531 185)"><circle r="9" /><text x="-76" y="-18">API</text></g>
              <g class="system-node" transform="translate(109 421)"><circle r="9" /><text x="18" y="5">SQL</text></g>
              <g class="system-node" transform="translate(535 445)"><circle r="9" /><text x="-90" y="28">CI / CD</text></g>
              <g class="system-core" transform="translate(320 320)">
                <circle r="72" />
                <text text-anchor="middle" y="8">.NET</text>
              </g>
            </svg>
            <span class="orbit-chip orbit-chip-one">Interface</span>
            <span class="orbit-chip orbit-chip-two">Data</span>
            <span class="orbit-chip orbit-chip-three">Delivery</span>
          </div>
        </div>

        <div class="hero-meta">
          <span>01 — Ürün</span>
          <span>02 — Sistem</span>
          <span>03 — Teslim</span>
          <a href="#projeler">Kaydır <span aria-hidden="true">↓</span></a>
        </div>
      </section>

      <div class="tech-marquee" aria-label="Kullandığım temel teknolojiler">
        <div class="marquee-track">
          <div v-for="group in 2" :key="group" class="marquee-group" :aria-hidden="group === 2">
            <span v-for="item in tickerItems" :key="`${group}-${item}`">{{ item }} <i aria-hidden="true">✦</i></span>
          </div>
        </div>
      </div>

      <section id="yaklasim" class="about-section section-pad">
        <div class="section-label" data-reveal>
          <span>01</span>
          <span>Yaklaşım</span>
        </div>

        <div class="about-content">
          <p class="eyebrow" data-reveal>Arayüzün arkasındaki sistemi de tasarlamak</p>
          <h2 data-reveal>
            Backend mantığını, veri akışını ve arayüzü
            <em>tek bir ürün</em> gibi düşünüyorum.
          </h2>
          <div class="about-copy" data-reveal>
            <p>
              Kurumsal ERP ve otel yönetim sistemlerinde servislerden veri modeline, raporlamadan kullanıcı
              arayüzüne kadar ürünün farklı katmanlarında çalışıyorum.
            </p>
            <p>
              Amacım yalnızca çalışan kod üretmek değil; anlaşılır, sürdürülebilir ve gerçek operasyonun
              yükünü taşıyan sistemler kurmak.
            </p>
          </div>
        </div>
      </section>

      <section id="projeler" class="work-section section-pad" aria-labelledby="work-title">
        <header class="work-heading" data-reveal>
          <div class="section-label section-label-light">
            <span>02</span>
            <span>Seçili işler</span>
          </div>
          <div>
            <p class="eyebrow eyebrow-light">Güncel çalışma alanı</p>
            <h2 id="work-title">Sistemin tamamını gören geliştirme.</h2>
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
            <span>03</span>
            <span>Yetkinlikler</span>
          </div>
          <div data-reveal>
            <p class="eyebrow">Teknoloji & araçlar</p>
            <h2 id="capabilities-title">Fikirden üretime uzanan teknik kapsam.</h2>
          </div>
        </header>

        <div class="capability-list">
          <article v-for="capability in capabilities" :key="capability.title" class="capability-row" data-reveal>
            <span>{{ capability.index }}</span>
            <div>
              <h3>{{ capability.title }}</h3>
              <p>{{ capability.description }}</p>
            </div>
            <ul>
              <li v-for="item in capability.items" :key="item">{{ item }}</li>
            </ul>
          </article>
        </div>
      </section>

      <section class="profile-section section-pad">
        <div class="section-label" data-reveal>
          <span>04</span>
          <span>Profil</span>
        </div>
        <div class="profile-main">
          <p class="eyebrow" data-reveal>Eğitim & yön</p>
          <h2 data-reveal>Merakla öğrenen,<br /><em>üreterek derinleşen.</em></h2>
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
        <p class="eyebrow eyebrow-light" data-reveal>Yeni bir fikir mi var?</p>
        <h2 id="contact-title" data-reveal>Birlikte<br /><em>üretelim.</em></h2>
        <div class="contact-bottom" data-reveal>
          <a class="button button-light" href="mailto:ikizesad99@gmail.com" data-magnetic>
            <span>E-posta gönder</span>
            <span aria-hidden="true">↗</span>
          </a>
          <div class="contact-links">
            <a href="mailto:ikizesad99@gmail.com">E-posta ↗</a>
            <a href="tel:+905442438922">Telefon ↗</a>
            <a href="https://www.linkedin.com/in/esad-ikiz-b971662a9/" target="_blank" rel="noreferrer">LinkedIn ↗</a>
            <a href="https://github.com/AEsadi" target="_blank" rel="noreferrer">GitHub ↗</a>
          </div>
        </div>
      </section>
    </main>

    <footer class="site-footer">
      <span>© {{ currentYear }} Abdurrahman Esad İkiz</span>
      <span>Full Stack Developer · İstanbul</span>
      <a href="#top">Yukarı dön ↑</a>
    </footer>
  </div>
</template>
