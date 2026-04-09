<template>
  <main class="page-shell">
    <NuxtRouteAnnouncer />
    <section class="repro-card">
      <h1>Nuxt 4.4.2 Windows CSS @fs Repro</h1>
      <p>
        This page is wired to load CSS from a local package dependency. On
        Windows dev mode, Nuxt may inject a malformed stylesheet URL such as
        <code>/@fsD:/...</code>, which then returns 404.
      </p>

      <h2>How To Reproduce</h2>
      <ol>
        <li>Use a Windows machine.</li>
        <li>Run <code>npm install</code> and then <code>npm run dev</code>.</li>
        <li>Open the local URL printed by Nuxt in the terminal.</li>
        <li>
          Open browser DevTools with <code>F12</code> or
          <code>Ctrl+Shift+I</code>.
        </li>
        <li>
          Check the Console and Network tabs for a stylesheet request containing
          <code>@fsD:</code>.
        </li>
      </ol>

      <h2>Expected Evidence</h2>
      <ul>
        <li>
          A stylesheet request like
          <code>/_nuxt/@fsD:/.../style.css</code> returns 404.
        </li>
        <li>
          The package styles below do not fully apply when that request fails.
        </li>
      </ul>

      <p class="repro-note">
        Local package used for the repro:
        <code>packages/windows-css-repro/style.css</code>
      </p>
    </section>
  </main>
</template>

<style scoped>
.page-shell {
  min-height: 100vh;
  display: grid;
  place-items: center;
  padding: 2rem;
  background: #f8fafc;
  color: #0f172a;
  font-family: Georgia, serif;
}

.repro-card {
  max-width: 40rem;
  padding: 1.5rem;
  border-radius: 1rem;
  box-shadow: 0 20px 50px rgba(15, 23, 42, 0.08);
  border: 1px solid rgba(15, 23, 42, 0.08);
  background: rgba(255, 255, 255, 0.95);
}

h1,
h2 {
  margin: 0;
}

h1 {
  font-size: 1.75rem;
  margin-bottom: 1rem;
}

h2 {
  font-size: 1.05rem;
  margin-top: 1.5rem;
}

p,
ol,
ul {
  margin: 0.75rem 0 0;
  line-height: 1.6;
}

ol,
ul {
  padding-left: 1.25rem;
}

code {
  padding: 0.1rem 0.35rem;
  border-radius: 0.35rem;
  background: #e2e8f0;
  font-family: 'Cascadia Code', Consolas, monospace;
  font-size: 0.95em;
}

.repro-note {
  margin-top: 1.5rem;
  color: #334155;
}
</style>
