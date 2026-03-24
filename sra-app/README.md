# SRA - Scheduler Risk Analysis

Dashboard analisis risiko jadwal berbasis Monte Carlo untuk planner dan risk analyst. Aplikasi ini berjalan sepenuhnya di browser, menyimpan workspace secara lokal, dan cocok untuk hosting statis seperti Cloudflare Pages.

## Ringkasan fitur

- Workflow terpandu `Input -> Run -> Results -> Insights`
- Workspace local-first: multi-project, multi-scenario, recent projects
- Simulasi Monte Carlo dengan distribusi triangular
- Compare scenario untuk `P50`, `P80`, `P90`, contingency, dan risk driver
- Export `CSV`, `PNG`, dan `PDF`
- CSV import, template download, dan preset proyek
- Validation assistant dengan jump-to-row, dismiss, dan auto-fix dasar
- Mitigation register per aktivitas
- Dependency ringan `FS/SS`, lag, mode `plan risk` / `remaining risk`
- Calendar setup dengan `5/6/7` hari kerja dan holiday override sederhana

## Tech stack

- React
- Vite
- Recharts
- jsPDF
- html-to-image
- Vitest + Testing Library + axe

## Menjalankan lokal

Prasyarat:

- Node.js 18+
- npm 9+

Command:

```bash
npm install
npm run dev
```

Build production:

```bash
npm run build
npm run preview
```

Test:

```bash
npm test
```

## Deploy ke Cloudflare Pages via Wrangler

Project ini sudah disiapkan untuk direct upload ke Cloudflare Pages.

```bash
npm run cf:login
npm run cf:project
npm run build
npm run cf:deploy
```

Preview branch:

```bash
npm run build
npm run cf:deploy:staging
```

Konfigurasi deploy ada di [wrangler.toml](/E:/Project/sra-app/sra-app/wrangler.toml).

## Struktur penting

```text
src/
  App.jsx                     Shell utama workflow, persistence, compare, export
  App.css                     Theme dan layout dashboard
  components/
    Header.jsx                Hero, stepper, run controls
    WorkspacePanel.jsx        Project/scenario workspace + calendar setup
    InputTab.jsx              Tabel aktivitas, import, preset, validation assistant
    ResultsTab.jsx            Executive summary, critical path, mitigation readiness
    ChartsTab.jsx             Histogram dan S-curve
    SensitivityTab.jsx        Tornado chart, risk table, mitigation context
    CompareSection.jsx        Perbandingan 2 scenario
  utils/
    simulation.js             Engine Monte Carlo, diagnostics, dependency summary
    workspace.js              Model workspace dan local storage
    import.js                 CSV import + template
    compare.js                Delta compare scenario
    report.js                 Export PNG/PDF
    templates.js              Preset template proyek
```

## Model data saat ini

Workspace menyimpan:

- `Project`
  - `id`, `name`, `createdAt`, `updatedAt`, `notes`
  - `calendarConfig`
  - `scenarios`
- `Scenario`
  - `id`, `name`, `createdAt`, `updatedAt`
  - `iterations`, `activities`, `notes`, `lastRunSummary`
- `Activity`
  - O/M/P
  - predecessor tunggal
  - `dependencyType`, `lag`
  - `progressPercent`, `remainingDuration`, `actualStart`
  - mitigation fields

## Catatan modeling

- Dependency yang didukung saat ini: `FS` dan `SS`
- Holiday override saat ini diperlakukan sebagai allowance global: setiap baris override menambah 1 hari ke total durasi proyek
- Belum ada scheduling berbasis tanggal kalender penuh atau CPM editor
- Tidak ada backend; semua data tersimpan di browser user

## Quality gates

Baseline saat ini:

- `npm test` lulus
- `npm run build` lulus
- Audit accessibility otomatis lewat `axe` sudah aktif di test suite
- Bundle chart tetap lazy-loaded agar initial load ringan

## Roadmap berikutnya

Prioritas lanjutan yang paling logis:

1. Dependency realism yang lebih kuat: multi-predecessor, logic validation, holiday/date-aware scheduling
2. Import Primavera P6 / MS Project tahap awal
3. Report export yang lebih kaya dengan lampiran chart per scenario
4. Portfolio view dan collaboration setelah local-first workflow matang
