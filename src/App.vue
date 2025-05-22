<script setup>
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <div class="portfolio-container">
    <div class="info-card">
      <h1>Merhaba, Ben Esad!</h1>
      <h2>Bilgisayar Programcılığı Öğrencisi</h2>
      <p>Balıkesir Üniversitesi Bilgisayar Programcılığı bölümü 1. sınıf öğrencisiyim.</p>
      <p>Daha fazla bilgi için aşağıdaki ikonları kullanabilirsiniz.</p>
    </div>
    <div class="icon-links">
      <a href="https://github.com/AEsadi" target="_blank" aria-label="GitHub">
        <svg height="32" width="32" viewBox="0 0 16 16" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
          <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.19 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"/>
        </svg>
      </a>
      <a href="https://www.linkedin.com/in/esad-ikiz-b971662a9/" target="_blank" aria-label="LinkedIn">
        <svg height="32" width="32" viewBox="0 0 24 24" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
          <path d="M19 0h-14c-2.76 0-5 2.24-5 5v14c0 2.76 2.24 5 5 5h14c2.76 0 5-2.24 5-5v-14c0-2.76-2.24-5-5-5zm-11 19h-3v-10h3v10zm-1.5-11.28c-.97 0-1.75-.79-1.75-1.75s.78-1.75 1.75-1.75 1.75.79 1.75 1.75-.78 1.75-1.75 1.75zm15.5 11.28h-3v-5.6c0-1.34-.03-3.07-1.87-3.07-1.87 0-2.16 1.46-2.16 2.97v5.7h-3v-10h2.89v1.36h.04c.4-.75 1.38-1.54 2.84-1.54 3.04 0 3.6 2 3.6 4.59v5.59z"/>
        </svg>
      </a>
    </div>
  </div>
</template>

<style scoped>
:root {
  --background: linear-gradient(135deg, #23272a 0%, #35495e 100%);
  --container-bg: rgba(35, 39, 42, 0.95);
  --card-bg: #222c2a;
  --text-main: #f5f6fa;
  --text-secondary: #bfc9d1;
  --accent: #42b883;
  --icon-bg: #263a36;
  --icon-hover-bg: #42b88322;
  --shadow: 0 2px 24px 0 #42b88355;
}
body {
  background: var(--background);
}
.portfolio-container {
  text-align: left;
  max-width: 600px;
  margin: 40px auto;
  padding: 32px;
  background: var(--container-bg);
  border-radius: 16px;
  box-shadow: var(--shadow);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  color: var(--text-main);
}
.info-card {
  background: var(--card-bg);
  border-radius: 14px;
  box-shadow: 0 2px 24px 0 #42b88344;
  padding: 28px 24px 20px 24px;
  margin-bottom: 2em;
  border: 1.5px solid #42b88344;
}
h1 {
  margin-bottom: 0.5em;
  color: var(--accent);
  letter-spacing: 1px;
}
h2 {
  margin-top: 0;
  color: var(--text-secondary);
  font-size: 0.95em;
  font-style: italic;
  font-weight: normal;
}
p {
  color: var(--text-secondary);
  font-size: 1.1em;
}
.icon-links {
  margin-top: 1.5em;
  display: flex;
  gap: 24px;
}
.icon-links a {
  display: inline-flex;
  align-items: center;
  color: var(--accent);
  background: var(--icon-bg);
  border-radius: 50%;
  width: 48px;
  height: 48px;
  justify-content: center;
  transition: background 0.2s, box-shadow 0.2s, color 0.2s;
  box-shadow: 0 2px 8px rgba(66,184,131,0.18);
  font-size: 1.5em;
  border: 1.5px solid #42b88344;
}
.icon-links a:hover {
  background: var(--icon-hover-bg);
  color: #fff;
  box-shadow: 0 4px 16px #42b88355;
}
.icon-links svg {
  display: block;
  fill: #f5f5f5;
  transition: fill 0.2s;
}
.icon-links a:hover svg {
  fill: #42b883;
}
</style>
