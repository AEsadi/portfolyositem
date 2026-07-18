<script setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'

let revealObserver
const isDark = ref(false)

const skills = [
  {
    index: '01',
    title: 'Frontend',
    items: ['HTML', 'CSS', 'JavaScript', 'Vue.js (Vue)', 'Bootstrap', 'DevExtreme'],
  },
  {
    index: '02',
    title: 'Backend',
    items: ['C#', '.NET', 'ASP.NET Core Web API', 'ASP.NET Core MVC', 'Entity Framework Core'],
  },
  {
    index: '03',
    title: 'Veri, Bulut & Operasyon',
    items: [
      'Microsoft SQL Server',
      'Azure AI Translator',
      'Git / GitHub',
      'GitHub Actions',
      'Jenkins',
      'IIS Deployment',
      'Hangfire',
    ],
  },
]

const projects = [
  {
    type: 'Konaklama Teknolojileri',
    title: 'Multi-tenant Otel Yönetim Sistemi',
    summary:
      'ASP.NET Core tabanlı çok kiracılı web uygulamasında veri modeli, kullanıcı işlemleri ve iş akışları üzerine çalışıyorum.',
    details: ['ASP.NET Core', 'Entity Framework Core', 'SQL Server', 'REST servisleri'],
  },
  {
    type: 'Kurumsal Sistemler',
    title: 'Navision Entegre Web ERP',
    summary:
      '.NET tabanlı kurumsal ERP sisteminde backend iş akışları, raporlama ve Navision entegrasyonları geliştiriyorum.',
    details: ['.NET', 'MediatR', 'DevExpress Reporting', 'Navision'],
  },
]

function applyTheme(theme, persist = true) {
  const nextTheme = theme === 'dark' ? 'dark' : 'light'
  isDark.value = nextTheme === 'dark'
  document.documentElement.dataset.theme = nextTheme
  document.querySelector('meta[name="theme-color"]')?.setAttribute('content', isDark.value ? '#1d1713' : '#f3eee5')

  if (persist) localStorage.setItem('portfolio-theme', nextTheme)
}

function toggleTheme() {
  applyTheme(isDark.value ? 'light' : 'dark')
}

onMounted(() => {
  applyTheme(document.documentElement.dataset.theme || 'light', false)

  revealObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add('is-visible')
          revealObserver.unobserve(entry.target)
        }
      })
    },
    { threshold: 0.14 },
  )

  document.querySelectorAll('[data-reveal]').forEach((element) => revealObserver.observe(element))
})

onBeforeUnmount(() => revealObserver?.disconnect())
</script>

<template>
  <div class="site-shell">
    <header class="site-header">
      <a class="brand" href="#top" aria-label="Ana sayfaya dön">
        <span class="brand-mark">AE</span>
        <span>Esad İkiz</span>
      </a>

      <nav class="site-nav" aria-label="Ana menü">
        <a href="#hakkimda">Hakkımda</a>
        <a href="#deneyim">Deneyim</a>
        <a href="#yetkinlikler">Yetkinlikler</a>
      </nav>

      <div class="header-actions">
        <button
          class="theme-toggle"
          type="button"
          :aria-label="isDark ? 'Açık temaya geç' : 'Koyu temaya geç'"
          :aria-pressed="isDark"
          :title="isDark ? 'Açık tema' : 'Koyu tema'"
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
        <a class="header-contact" href="mailto:ikizesad99@gmail.com">E-posta</a>
      </div>
    </header>

    <main id="top">
      <section class="hero" aria-labelledby="hero-title">
        <div class="hero-copy">
          <p class="eyebrow hero-kicker">Full Stack Developer · İstanbul</p>
          <h1 id="hero-title">
            <span class="hero-name-first">Abdurrahman</span>
            <em>Esad İkiz</em>
          </h1>
          <p class="hero-intro">
            .NET, ASP.NET Core, Vue ve SQL Server ile kurumsal web uygulamaları geliştirme deneyimine
            sahibim.
          </p>
          <div class="hero-actions">
            <a class="button button-primary" href="#deneyim">
              İş deneyimi
              <span aria-hidden="true">↘</span>
            </a>
            <a class="text-link" href="https://github.com/AEsadi" target="_blank" rel="noreferrer">GitHub profili ↗</a>
          </div>
        </div>

        <a class="scroll-cue" href="#hakkimda">
          <span>Devam et</span>
          <span class="scroll-line" aria-hidden="true"></span>
        </a>
      </section>

      <section id="hakkimda" class="about section-pad" data-reveal>
        <div class="section-index">
          <span>01</span>
          <span>Hakkımda</span>
        </div>
        <div class="about-content">
          <p class="eyebrow">Kısa profil</p>
          <h2>.NET ve Vue ekosisteminde full stack geliştirme.</h2>
          <div class="about-columns">
            <p>
              Kurumsal ERP ve otel yönetim sistemlerinde backend servisleri, veri modeli, raporlama ve
              kullanıcı arayüzü geliştirme süreçlerinde deneyim kazandım.
            </p>
            <p>
              Balıkesir Üniversitesi Bilgisayar Programcılığı ön lisans programından Haziran 2026'da 3.44
              genel not ortalamasıyla mezun oldum.
            </p>
          </div>
        </div>
      </section>

      <section id="deneyim" class="work-section section-pad">
        <div class="section-heading" data-reveal>
          <div class="section-index light">
            <span>02</span>
            <span>Deneyim</span>
          </div>
          <div>
            <h2>İş deneyimi</h2>
          </div>
        </div>

        <div class="experience" data-reveal>
          <div class="experience-meta">
            <p>ST ERP Danışmanlık</p>
            <span>Haziran 2025 — Günümüz</span>
            <span>İstanbul, Kadıköy</span>
          </div>
          <div class="experience-role">
            <span class="eyebrow light">Görev kapsamı</span>
            <p>
              Full Stack Developer olarak backend servisleri, veri tabanı süreçleri, raporlama ve üretim ortamı
              sorunlarının çözümünde görev alıyorum.
            </p>
            <a href="https://st-erp.com" target="_blank" rel="noreferrer">st-erp.com ↗</a>
          </div>
        </div>

        <div class="project-list">
          <article v-for="(project, index) in projects" :key="project.title" class="project" data-reveal>
            <span class="project-number">0{{ index + 1 }}</span>
            <div class="project-main">
              <p class="eyebrow light">{{ project.type }}</p>
              <h3>{{ project.title }}</h3>
              <p>{{ project.summary }}</p>
            </div>
            <ul class="project-tech" :aria-label="`${project.title} teknolojileri`">
              <li v-for="item in project.details" :key="item">{{ item }}</li>
            </ul>
          </article>
        </div>
      </section>

      <section id="yetkinlikler" class="skills-section section-pad">
        <div class="section-heading" data-reveal>
          <div class="section-index">
            <span>03</span>
            <span>Yetkinlikler</span>
          </div>
          <div>
            <p class="eyebrow">Teknoloji ve araçlar</p>
            <h2>Teknik yetkinlikler</h2>
          </div>
        </div>

        <div class="skill-list">
          <article v-for="skill in skills" :key="skill.title" class="skill-row" data-reveal>
            <span>{{ skill.index }}</span>
            <h3>{{ skill.title }}</h3>
            <ul>
              <li v-for="item in skill.items" :key="item">{{ item }}</li>
            </ul>
          </article>
        </div>
      </section>

      <section class="education section-pad" data-reveal>
        <div class="section-index">
          <span>04</span>
          <span>Eğitim</span>
        </div>
        <div class="education-main">
          <p class="eyebrow">Eğitim bilgisi</p>
          <h2>Balıkesir Üniversitesi</h2>
          <div class="education-details">
            <p>Bilgisayar Programcılığı<br /><span>Ön Lisans</span></p>
            <p>Eylül 2024 — Haziran 2026<br /><span>GPA 3.44</span></p>
          </div>
        </div>
      </section>

      <section class="contact-section section-pad" data-reveal>
        <p class="eyebrow light">İletişim</p>
        <h2>İletişim bilgileri ve<br />profesyonel profiller.</h2>
        <div class="contact-bottom">
          <a class="button button-light" href="mailto:ikizesad99@gmail.com">
            E-posta gönder
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
      <span>© {{ new Date().getFullYear() }} Abdurrahman Esad İkiz</span>
      <span>Full Stack Developer · İstanbul</span>
      <a href="#top">Yukarı dön ↑</a>
    </footer>
  </div>
</template>
