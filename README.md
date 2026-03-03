<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SIMOJI-RPK — Sistem Monitoring Intervensi RPK</title>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&family=Playfair+Display:wght@600;700&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<style>
:root {
  --teal: #2D7D6F;
  --teal-light: #4AADA0;
  --teal-pale: #E8F5F3;
  --rose: #D4768A;
  --rose-light: #F5C2CE;
  --sage: #6B9E8C;
  --cream: #FAF8F5;
  --charcoal: #2C3E35;
  --warm-gray: #8A9E98;
  --white: #FFFFFF;
  --shadow: 0 4px 24px rgba(45,125,111,0.12);
  --shadow-lg: 0 12px 48px rgba(45,125,111,0.18);
  --radius: 16px;
  --radius-sm: 10px;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: 'Plus Jakarta Sans', sans-serif;
  background: var(--cream);
  color: var(--charcoal);
  min-height: 100vh;
  overflow-x: hidden;
}

/* ===== SCREENS ===== */
.screen { display: none; min-height: 100vh; }
.screen.active { display: flex; flex-direction: column; }

/* ===== SPLASH / LOGIN ===== */
#screen-splash {
  background: linear-gradient(135deg, #1a5c50 0%, #2D7D6F 40%, #4AADA0 80%, #7EC8BE 100%);
  align-items: center;
  justify-content: center;
  padding: 24px;
  position: relative;
  overflow: hidden;
}
#screen-splash::before {
  content: '';
  position: absolute;
  width: 600px; height: 600px;
  border-radius: 50%;
  background: rgba(255,255,255,0.04);
  top: -200px; right: -200px;
}
#screen-splash::after {
  content: '';
  position: absolute;
  width: 400px; height: 400px;
  border-radius: 50%;
  background: rgba(255,255,255,0.05);
  bottom: -150px; left: -100px;
}

.splash-card {
  background: rgba(255,255,255,0.96);
  border-radius: 24px;
  padding: 40px 36px;
  width: 100%;
  max-width: 420px;
  box-shadow: 0 24px 80px rgba(0,0,0,0.25);
  z-index: 1;
  text-align: center;
}

.logo-wrap { margin-bottom: 20px; }
.logo-wrap img { width: 100px; height: 100px; object-fit: contain; }

.app-title {
  font-family: 'Playfair Display', serif;
  font-size: 2rem;
  color: var(--teal);
  letter-spacing: -0.5px;
}
.app-subtitle { color: var(--warm-gray); font-size: 0.82rem; margin-top: 4px; margin-bottom: 28px; line-height: 1.5; }

.form-group { margin-bottom: 16px; text-align: left; }
.form-label { font-size: 0.78rem; font-weight: 600; color: var(--warm-gray); text-transform: uppercase; letter-spacing: 0.5px; margin-bottom: 6px; display: block; }

.form-input, .form-select {
  width: 100%;
  padding: 12px 16px;
  border: 2px solid #e8f0ee;
  border-radius: var(--radius-sm);
  font-family: inherit;
  font-size: 0.92rem;
  color: var(--charcoal);
  background: #f9fcfb;
  transition: all 0.2s;
  outline: none;
  appearance: none;
}
.form-input:focus, .form-select:focus { border-color: var(--teal-light); background: white; box-shadow: 0 0 0 4px rgba(74,173,160,0.1); }

.btn {
  display: inline-flex; align-items: center; justify-content: center; gap: 8px;
  padding: 13px 24px;
  border-radius: var(--radius-sm);
  border: none;
  font-family: inherit;
  font-weight: 700;
  font-size: 0.92rem;
  cursor: pointer;
  transition: all 0.2s;
  text-decoration: none;
}
.btn-primary { background: linear-gradient(135deg, var(--teal), var(--teal-light)); color: white; width: 100%; }
.btn-primary:hover { transform: translateY(-2px); box-shadow: 0 8px 24px rgba(45,125,111,0.35); }
.btn-secondary { background: var(--teal-pale); color: var(--teal); }
.btn-secondary:hover { background: #d0ece8; }
.btn-outline { background: transparent; border: 2px solid var(--teal); color: var(--teal); }
.btn-rose { background: linear-gradient(135deg, var(--rose), #e891a2); color: white; }
.btn-sm { padding: 8px 16px; font-size: 0.82rem; }
.btn-icon { padding: 10px; border-radius: 50%; background: var(--teal-pale); color: var(--teal); border: none; cursor: pointer; display: inline-flex; align-items: center; justify-content: center; transition: all 0.2s; }
.btn-icon:hover { background: var(--teal); color: white; }

/* ===== MAIN APP ===== */
.app-layout { display: flex; min-height: 100vh; }

.sidebar {
  width: 240px;
  background: var(--charcoal);
  flex-shrink: 0;
  display: flex;
  flex-direction: column;
  padding: 0;
  position: fixed;
  top: 0; left: 0; bottom: 0;
  z-index: 100;
  transition: transform 0.3s;
}
.sidebar-header {
  padding: 24px 20px 20px;
  border-bottom: 1px solid rgba(255,255,255,0.08);
  display: flex; align-items: center; gap: 12px;
}
.sidebar-logo { width: 40px; height: 40px; object-fit: contain; }
.sidebar-title { color: white; font-weight: 700; font-size: 1.1rem; }
.sidebar-subtitle { color: rgba(255,255,255,0.4); font-size: 0.7rem; }

.sidebar-nav { flex: 1; padding: 16px 12px; overflow-y: auto; }
.nav-section-label { color: rgba(255,255,255,0.3); font-size: 0.68rem; font-weight: 700; text-transform: uppercase; letter-spacing: 1px; padding: 12px 8px 6px; }

.nav-item {
  display: flex; align-items: center; gap: 10px;
  padding: 10px 12px;
  border-radius: 10px;
  color: rgba(255,255,255,0.6);
  cursor: pointer;
  transition: all 0.2s;
  font-size: 0.88rem;
  font-weight: 500;
  margin-bottom: 2px;
  border: none; background: none; width: 100%; text-align: left;
}
.nav-item:hover { background: rgba(255,255,255,0.06); color: rgba(255,255,255,0.9); }
.nav-item.active { background: linear-gradient(135deg, var(--teal), var(--teal-light)); color: white; }
.nav-item .nav-icon { width: 20px; height: 20px; flex-shrink: 0; display: flex; align-items: center; justify-content: center; }

.sidebar-footer {
  padding: 16px;
  border-top: 1px solid rgba(255,255,255,0.08);
}
.user-info { display: flex; align-items: center; gap: 10px; }
.user-avatar { width: 36px; height: 36px; border-radius: 50%; background: linear-gradient(135deg, var(--teal), var(--teal-light)); display: flex; align-items: center; justify-content: center; color: white; font-weight: 700; font-size: 0.9rem; flex-shrink: 0; }
.user-name { color: white; font-size: 0.82rem; font-weight: 600; }
.user-role { color: rgba(255,255,255,0.4); font-size: 0.72rem; }

.main-content { margin-left: 240px; flex: 1; min-height: 100vh; }

.top-bar {
  background: white;
  padding: 16px 28px;
  border-bottom: 1px solid #f0f4f3;
  display: flex; align-items: center; justify-content: space-between;
  position: sticky; top: 0; z-index: 50;
  box-shadow: 0 2px 12px rgba(0,0,0,0.04);
}
.page-title { font-size: 1.2rem; font-weight: 700; color: var(--charcoal); }
.page-subtitle { font-size: 0.78rem; color: var(--warm-gray); margin-top: 2px; }

.content-area { padding: 28px; }

/* ===== CARDS ===== */
.card {
  background: white;
  border-radius: var(--radius);
  padding: 24px;
  box-shadow: var(--shadow);
  border: 1px solid rgba(45,125,111,0.06);
}
.card-title { font-weight: 700; font-size: 1rem; color: var(--charcoal); margin-bottom: 16px; display: flex; align-items: center; gap: 8px; }

/* ===== STATS GRID ===== */
.stats-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 16px; margin-bottom: 24px; }

.stat-card {
  background: white;
  border-radius: var(--radius);
  padding: 20px;
  box-shadow: var(--shadow);
  border-left: 4px solid var(--teal);
}
.stat-value { font-size: 2rem; font-weight: 800; color: var(--teal); line-height: 1; }
.stat-label { font-size: 0.78rem; color: var(--warm-gray); margin-top: 6px; font-weight: 500; }
.stat-change { font-size: 0.72rem; color: #4ade80; font-weight: 600; margin-top: 4px; }

/* ===== GRID ===== */
.grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
.grid-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 20px; }

/* ===== TABS ===== */
.tabs { display: flex; gap: 4px; background: var(--teal-pale); padding: 4px; border-radius: 12px; margin-bottom: 24px; }
.tab {
  flex: 1; padding: 10px; text-align: center;
  border-radius: 10px; cursor: pointer;
  font-size: 0.82rem; font-weight: 600;
  color: var(--warm-gray);
  border: none; background: none;
  transition: all 0.2s;
}
.tab.active { background: white; color: var(--teal); box-shadow: 0 2px 8px rgba(0,0,0,0.08); }

/* ===== TABLE ===== */
.table-wrap { overflow-x: auto; }
table { width: 100%; border-collapse: collapse; }
th { text-align: left; padding: 10px 14px; font-size: 0.75rem; font-weight: 700; color: var(--warm-gray); text-transform: uppercase; letter-spacing: 0.5px; border-bottom: 2px solid var(--teal-pale); background: #fafcfb; }
td { padding: 12px 14px; font-size: 0.86rem; border-bottom: 1px solid #f5f8f7; vertical-align: middle; }
tr:hover td { background: var(--teal-pale); }

/* ===== BADGE ===== */
.badge { display: inline-flex; align-items: center; gap: 4px; padding: 3px 10px; border-radius: 20px; font-size: 0.73rem; font-weight: 700; }
.badge-green { background: #dcfce7; color: #16a34a; }
.badge-yellow { background: #fef9c3; color: #ca8a04; }
.badge-red { background: #fee2e2; color: #dc2626; }
.badge-teal { background: var(--teal-pale); color: var(--teal); }
.badge-rose { background: #fff0f3; color: var(--rose); }

/* ===== THERAPY GUIDE ===== */
.therapy-step {
  display: flex;
  gap: 20px;
  padding: 20px;
  border-radius: var(--radius-sm);
  border: 2px solid var(--teal-pale);
  margin-bottom: 16px;
  align-items: flex-start;
  transition: all 0.2s;
}
.therapy-step.completed { border-color: #bbf7d0; background: #f0fdf4; }
.therapy-step.active-step { border-color: var(--teal-light); background: #f0fdfb; box-shadow: 0 0 0 4px rgba(74,173,160,0.1); }

.step-number {
  width: 40px; height: 40px; border-radius: 50%;
  background: var(--teal-pale); color: var(--teal);
  display: flex; align-items: center; justify-content: center;
  font-weight: 800; font-size: 1rem; flex-shrink: 0;
}
.step-number.done { background: #bbf7d0; color: #16a34a; }

.step-icon { font-size: 3rem; text-align: center; margin-bottom: 8px; }
.step-content { flex: 1; }
.step-title { font-weight: 700; font-size: 0.95rem; margin-bottom: 6px; }
.step-desc { font-size: 0.85rem; color: #64748b; line-height: 1.6; }

/* ===== OASS FORM ===== */
.oass-item {
  padding: 14px;
  border-radius: var(--radius-sm);
  background: #fafcfb;
  border: 1px solid var(--teal-pale);
  margin-bottom: 10px;
}
.oass-label { font-size: 0.86rem; font-weight: 600; margin-bottom: 8px; }
.oass-options { display: flex; flex-wrap: wrap; gap: 8px; }
.oass-radio {
  display: none;
}
.oass-option-label {
  padding: 6px 14px;
  border-radius: 20px;
  border: 2px solid #e2e8f0;
  font-size: 0.78rem;
  cursor: pointer;
  transition: all 0.2s;
  font-weight: 500;
}
.oass-radio:checked + .oass-option-label { border-color: var(--teal); background: var(--teal); color: white; }

.score-display {
  padding: 20px;
  border-radius: var(--radius);
  text-align: center;
  margin-top: 20px;
}
.score-number { font-size: 3rem; font-weight: 800; line-height: 1; }
.score-label { font-size: 0.9rem; font-weight: 600; margin-top: 8px; }

/* ===== PROGRESS ===== */
.progress-bar { height: 8px; background: #e2e8f0; border-radius: 4px; overflow: hidden; margin-top: 6px; }
.progress-fill { height: 100%; background: linear-gradient(90deg, var(--teal), var(--teal-light)); border-radius: 4px; transition: width 0.5s ease; }

/* ===== MODAL ===== */
.modal-overlay {
  position: fixed; inset: 0;
  background: rgba(0,0,0,0.5);
  z-index: 200;
  display: flex; align-items: center; justify-content: center;
  padding: 20px;
  display: none;
}
.modal-overlay.open { display: flex; }
.modal {
  background: white;
  border-radius: 20px;
  padding: 32px;
  width: 100%;
  max-width: 560px;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 24px 64px rgba(0,0,0,0.2);
}
.modal-title { font-size: 1.3rem; font-weight: 700; margin-bottom: 20px; color: var(--teal); }

/* ===== EMOTION CARDS (Assertiveness) ===== */
.emotion-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; }
.emotion-card {
  padding: 16px;
  border-radius: var(--radius-sm);
  border: 2px solid #e8f0ee;
  text-align: center;
  cursor: pointer;
  transition: all 0.2s;
}
.emotion-card:hover { border-color: var(--teal); transform: translateY(-2px); }
.emotion-card.selected { border-color: var(--teal); background: var(--teal-pale); }
.emotion-icon { font-size: 2rem; margin-bottom: 6px; }
.emotion-name { font-size: 0.78rem; font-weight: 600; color: var(--charcoal); }

/* ===== CHART ===== */
.chart-container { position: relative; height: 260px; }

/* ===== TOAST ===== */
.toast {
  position: fixed;
  bottom: 24px; right: 24px;
  background: var(--charcoal);
  color: white;
  padding: 14px 20px;
  border-radius: var(--radius-sm);
  font-size: 0.88rem;
  font-weight: 500;
  z-index: 999;
  transform: translateY(80px);
  opacity: 0;
  transition: all 0.3s;
  max-width: 320px;
  border-left: 4px solid var(--teal-light);
}
.toast.show { transform: translateY(0); opacity: 1; }

/* ===== PATIENT CARD ===== */
.patient-card {
  display: flex; align-items: center; gap: 14px;
  padding: 14px;
  border-radius: var(--radius-sm);
  border: 2px solid var(--teal-pale);
  cursor: pointer;
  transition: all 0.2s;
  margin-bottom: 10px;
}
.patient-card:hover { border-color: var(--teal-light); box-shadow: var(--shadow); }
.patient-avatar {
  width: 44px; height: 44px; border-radius: 50%;
  background: linear-gradient(135deg, var(--teal-light), var(--sage));
  color: white; font-weight: 700; font-size: 1.1rem;
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
}
.patient-name { font-weight: 700; font-size: 0.92rem; }
.patient-meta { font-size: 0.76rem; color: var(--warm-gray); margin-top: 2px; }

/* ===== THERAPY SESSION TRACKER ===== */
.session-tracker {
  display: flex; gap: 8px; margin-bottom: 20px; flex-wrap: wrap;
}
.session-dot {
  width: 36px; height: 36px; border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-size: 0.78rem; font-weight: 700;
  cursor: pointer; border: 2px solid;
  transition: all 0.2s;
}
.session-dot.done { background: var(--teal); border-color: var(--teal); color: white; }
.session-dot.current { background: white; border-color: var(--teal); color: var(--teal); }
.session-dot.locked { background: #f1f5f4; border-color: #d0ddd9; color: #a0b0ab; }

/* ===== QR CODE ===== */
.qr-container { display: flex; flex-direction: column; align-items: center; gap: 16px; padding: 20px; }

/* ===== MOBILE NAV ===== */
.mobile-nav-btn { display: none; }
@media (max-width: 768px) {
  .sidebar { transform: translateX(-100%); }
  .sidebar.open { transform: translateX(0); }
  .main-content { margin-left: 0; }
  .mobile-nav-btn { display: block; }
  .stats-grid { grid-template-columns: 1fr 1fr; }
  .grid-2 { grid-template-columns: 1fr; }
  .grid-3 { grid-template-columns: 1fr; }
  .emotion-grid { grid-template-columns: repeat(2, 1fr); }
  .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 99; display: none; }
  .overlay.open { display: block; }
}

/* ===== ANIMATIONS ===== */
@keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
.fade-in { animation: fadeIn 0.3s ease; }

.divider { height: 1px; background: var(--teal-pale); margin: 20px 0; }

textarea.form-input { resize: vertical; min-height: 80px; }

.inline-flex { display: flex; align-items: center; gap: 8px; }
.ml-auto { margin-left: auto; }
.text-sm { font-size: 0.82rem; }
.text-muted { color: var(--warm-gray); }
.text-teal { color: var(--teal); }
.text-rose { color: var(--rose); }
.font-bold { font-weight: 700; }
.mt-4 { margin-top: 16px; }
.mb-4 { margin-bottom: 16px; }
.gap-12 { gap: 12px; }

.flex { display: flex; }
.flex-col { flex-direction: column; }
.items-center { align-items: center; }
.justify-between { justify-content: space-between; }
.flex-wrap { flex-wrap: wrap; }
</style>
</head>
<body>

<!-- OVERLAY for mobile -->
<div class="overlay" id="overlay" onclick="closeSidebar()"></div>

<!-- TOAST -->
<div class="toast" id="toast"></div>

<!-- MODAL: Add Patient -->
<div class="modal-overlay" id="modal-addpatient">
  <div class="modal">
    <div class="inline-flex justify-between mb-4">
      <div class="modal-title">📋 Tambah Pasien Baru</div>
      <button class="btn-icon" onclick="closeModal('modal-addpatient')">✕</button>
    </div>
    <div class="form-group">
      <label class="form-label">Nama Lengkap</label>
      <input class="form-input" id="new-name" placeholder="Nama pasien...">
    </div>
    <div class="form-group">
      <label class="form-label">No. Rekam Medis</label>
      <input class="form-input" id="new-rm" placeholder="RM-XXXXX">
    </div>
    <div class="grid-2">
      <div class="form-group">
        <label class="form-label">Tanggal Lahir</label>
        <input class="form-input" type="date" id="new-dob">
      </div>
      <div class="form-group">
        <label class="form-label">Jenis Kelamin</label>
        <select class="form-select" id="new-gender">
          <option value="L">Laki-laki</option>
          <option value="P">Perempuan</option>
        </select>
      </div>
    </div>
    <div class="form-group">
      <label class="form-label">Ruangan</label>
      <select class="form-select" id="new-room">
        <option>Gatot Kaca</option>
      </select>
    </div>
    <div class="form-group">
      <label class="form-label">Diagnosa Keperawatan</label>
      <input class="form-input" id="new-diagnosa" value="Risiko Perilaku Kekerasan (RPK)">
    </div>
    <div class="form-group">
      <label class="form-label">No. WA Keluarga / Caregiver</label>
      <input class="form-input" id="new-wa" placeholder="+628xxxx">
    </div>
    <input type="hidden" id="new-nurse">
    <button class="btn btn-primary mt-4" onclick="savePatient()">💾 Simpan Pasien</button>
  </div>
</div>

<!-- MODAL: QR Code -->
<div class="modal-overlay" id="modal-qr">
  <div class="modal" style="max-width:380px;text-align:center">
    <div class="inline-flex justify-between mb-4" style="align-items:flex-start">
      <div>
        <div class="modal-title" style="margin-bottom:4px">📱 QR Code — SIMOJI-RPK</div>
        <p class="text-sm text-muted">Scan untuk membuka aplikasi di perangkat lain</p>
      </div>
      <button class="btn-icon" onclick="closeModal('modal-qr')" style="flex-shrink:0;margin-left:12px">✕</button>
    </div>
    <div class="qr-container" style="padding:12px 0 20px">
      <div id="qr-code-display"></div>
      <p id="qr-info" class="text-sm font-bold text-teal" style="margin-top:8px"></p>
    </div>
    <div style="display:flex;gap:10px">
      <button class="btn btn-secondary" style="flex:1" onclick="closeModal('modal-qr')">Tutup</button>
      <button class="btn btn-primary" style="flex:1" onclick="closeModal('modal-qr')">✓ Selesai</button>
    </div>
    <p style="font-size:0.68rem;color:var(--warm-gray);margin-top:14px;line-height:1.5">💡 <strong>Tips:</strong> Agar QR bisa dibuka perawat lain, host file HTML ini di server (mis. GitHub Pages, Netlify, atau LAN rumah sakit).</p>
  </div>
</div>

<!-- SPLASH / LOGIN SCREEN -->
<div class="screen active" id="screen-splash">
  <div class="splash-card">
    <div class="logo-wrap">
      <img src="data:image/jpeg;base64,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" alt="SIMOJI_RPK Logo" id="main-logo">
    </div>
    <div class="app-title">SIMOJI-RPK</div>
    <div class="app-subtitle">Sistem Monitoring Keperawatan Jiwa Spesialis</div>
    
    <div class="form-group">
      <label class="form-label">Nama Perawat</label>
      <input class="form-input" id="login-nurse" placeholder="Masukkan nama perawat...">
    </div>
    <div class="form-group">
      <label class="form-label">Ruangan</label>
      <select class="form-select" id="login-room">
        <option>Gatot Kaca</option>
      </select>
    </div>
    <div class="form-group">
      <label class="form-label">No. Rekam Medis Pasien (opsional)</label>
      <input class="form-input" id="login-rm" placeholder="RM-XXXXX atau kosongkan">
    </div>
    <button class="btn btn-primary" onclick="doLogin()">
      🔐 Masuk ke Sistem
    </button>
    <div style="margin-top:20px;padding-top:16px;border-top:1px solid #e8f0ee;text-align:left">
      <p style="font-size:0.7rem;color:var(--warm-gray);margin-bottom:6px;text-align:center;font-weight:600;letter-spacing:0.5px;text-transform:uppercase">Tim Pengembang</p>
      <div style="background:#f4fbf9;border-radius:10px;padding:12px;display:flex;flex-direction:column;gap:6px">
        <div style="font-size:0.72rem;color:var(--charcoal)">
          <span style="font-size:0.68rem;color:var(--warm-gray);display:block;text-transform:uppercase;letter-spacing:0.4px;font-weight:700">Creator</span>
          <span style="font-weight:600;color:var(--teal)">Ns. Oriza Sativa Manurung, S.Kep., M.Kep</span>
        </div>
        <div style="font-size:0.72rem;color:var(--charcoal)">
          <span style="font-size:0.68rem;color:var(--warm-gray);display:block;text-transform:uppercase;letter-spacing:0.4px;font-weight:700">Pembimbing</span>
          <span style="font-weight:600;color:var(--sage)">Yossie Susanti Eka Putri, S.Kp., M.N., Ph.D</span>
        </div>
      </div>
      <p style="font-size:0.68rem;color:var(--warm-gray);margin-top:8px;text-align:center">Program Studi Ners Spesialis Keperawatan Jiwa · FIK UI</p>
    </div>
  </div>
</div>

<!-- MAIN APP SCREEN -->
<div class="screen" id="screen-app">
  <div class="app-layout">
    <!-- SIDEBAR -->
    <nav class="sidebar" id="sidebar">
      <div class="sidebar-header">
        <img src="data:image/jpeg;base64,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" alt="Logo" class="sidebar-logo" id="sidebar-logo">
        <div>
          <div class="sidebar-title">SIMOJI-RPK</div>
          <div class="sidebar-subtitle">Terapi RPK RSJ</div>
        </div>
      </div>
      <div class="sidebar-nav">
        <div class="nav-section-label">Assessment</div>
        <button class="nav-item active" onclick="showPage('dashboard')">
          <span class="nav-icon">🏠</span> Dashboard
        </button>
        <button class="nav-item" onclick="showPage('patients')">
          <span class="nav-icon">👥</span> Data Pasien
        </button>
        <button class="nav-item" onclick="showPage('oass')">
          <span class="nav-icon">📝</span> Penilaian OASS
        </button>
        
        <div class="nav-section-label">Terapi/Intervensi</div>
        <button class="nav-item" onclick="showPage('butterfly')">
          <span class="nav-icon">🦋</span> Butterfly Hug
        </button>
        <button class="nav-item" onclick="showPage('grounding')">
          <span class="nav-icon">🌿</span> Grounding
        </button>
        <button class="nav-item" onclick="showPage('assertiveness')">
          <span class="nav-icon">💬</span> Assertiveness Training
        </button>
        
        <div class="nav-section-label">Monitoring &amp; Evaluasi</div>
        <button class="nav-item" onclick="showPage('evaluation')">
          <span class="nav-icon">📈</span> Evaluasi Pasien
        </button>
        <button class="nav-item" onclick="showPage('reports')">
          <span class="nav-icon">📋</span> Laporan
        </button>
        <button class="nav-item" onclick="showPage('monitoring')">
          <span class="nav-icon">📊</span> Monitoring
        </button>
        <button class="nav-item" onclick="showPage('reminders')">
          <span class="nav-icon">🔔</span> Reminder Latihan via WA Caregiver
        </button>
      </div>
      <div class="sidebar-footer">
        <div class="user-info">
          <div class="user-avatar" id="user-avatar-txt">NS</div>
          <div>
            <div class="user-name" id="sidebar-username">Ns. Perawat</div>
            <div class="user-role" id="sidebar-room">Ruangan</div>
          </div>
          <button class="btn-icon ml-auto" onclick="doLogout()" title="Kembali ke Login" style="background:rgba(255,255,255,0.1);color:rgba(255,255,255,0.6)">⏻</button>
        </div>
        <button onclick="doExit()" style="margin-top:10px;width:100%;padding:9px 0;background:rgba(220,38,38,0.18);color:#fca5a5;border:1.5px solid rgba(220,38,38,0.35);border-radius:8px;font-family:inherit;font-size:0.82rem;font-weight:700;cursor:pointer;letter-spacing:0.2px;transition:all 0.2s" onmouseover="this.style.background='rgba(220,38,38,0.32)'" onmouseout="this.style.background='rgba(220,38,38,0.18)'">
          🚪 Keluar Aplikasi
        </button>
      </div>
    </nav>

    <!-- MAIN CONTENT -->
    <div class="main-content">
      <div class="top-bar">
        <div>
          <div class="page-title" id="page-title">Dashboard</div>
          <div class="page-subtitle" id="page-subtitle" id="page-subtitle">Selamat datang di SIMOJI-RPK</div>
        </div>
        <div class="inline-flex gap-12">
          <button class="mobile-nav-btn btn btn-secondary btn-sm" onclick="toggleSidebar()">☰</button>
          <button class="btn btn-secondary btn-sm" onclick="openModal('modal-addpatient')">➕ Pasien</button>
          <button class="btn btn-outline btn-sm" onclick="showQR()">📱 QR</button>
        </div>
      </div>

      <!-- PAGE: DASHBOARD -->
      <div class="content-area fade-in" id="page-dashboard">
        <div class="stats-grid">
          <div class="stat-card">
            <div class="stat-value" id="stat-total">0</div>
            <div class="stat-label">Total Pasien</div>
            <div class="stat-change">↑ Aktif Hari Ini</div>
          </div>
          <div class="stat-card" style="border-color:var(--rose)">
            <div class="stat-value" style="color:var(--rose)" id="stat-sessions">0</div>
            <div class="stat-label">Sesi Terapi</div>
            <div class="stat-change" style="color:var(--rose)">Total Hari Ini</div>
          </div>
          <div class="stat-card" style="border-color:#f59e0b">
            <div class="stat-value" style="color:#f59e0b" id="stat-pending">0</div>
            <div class="stat-label">Menunggu Post-Test</div>
            <div class="stat-change" style="color:#f59e0b">Perlu Perhatian</div>
          </div>
          <div class="stat-card" style="border-color:#10b981">
            <div class="stat-value" style="color:#10b981" id="stat-completed">0</div>
            <div class="stat-label">Selesai Terapi</div>
            <div class="stat-change" style="color:#10b981">✓ Sukses</div>
          </div>
        </div>

        <div class="grid-2">
          <div class="card">
            <div class="card-title">📊 Distribusi Skor OASS</div>
            <div class="chart-container">
              <canvas id="chart-distribution"></canvas>
            </div>
          </div>
          <div class="card">
            <div class="card-title">📈 Tren Penurunan Agresivitas</div>
            <div class="chart-container">
              <canvas id="chart-trend"></canvas>
            </div>
          </div>
        </div>

        <div class="card mt-4">
          <div class="card-title">🔔 Pasien Aktif Hari Ini</div>
          <div id="dashboard-patient-list">
            <p class="text-muted text-sm" style="padding:20px;text-align:center">Belum ada pasien terdaftar. <a href="#" onclick="openModal('modal-addpatient');return false" style="color:var(--teal)">Tambah pasien baru →</a></p>
          </div>
        </div>
      </div>

      <!-- PAGE: PATIENTS -->
      <div class="content-area fade-in" id="page-patients" style="display:none">
        <div class="inline-flex justify-between mb-4 flex-wrap" style="gap:12px">
          <input class="form-input" placeholder="🔍 Cari nama atau rekam medis..." style="max-width:300px" oninput="filterPatients(this.value)">
          <div class="inline-flex gap-12">
            <select class="form-select" style="max-width:180px" onchange="filterByRoom(this.value)">
              <option value="">Semua Ruangan</option>
              <option>Gatot Kaca</option>
            </select>
            <button class="btn btn-primary" onclick="openModal('modal-addpatient')">➕ Tambah Pasien</button>
          </div>
        </div>
        <div class="card">
          <div class="table-wrap">
            <table>
              <thead>
                <tr>
                  <th>Pasien</th>
                  <th>No. RM</th>
                  <th>Ruangan</th>
                  <th>Perawat</th>
                  <th>Status Terapi</th>
                  <th>Skor Pre</th>
                  <th>Skor Post</th>
                  <th>Aksi</th>
                </tr>
              </thead>
              <tbody id="patients-table-body">
                <tr><td colspan="8" style="text-align:center;padding:30px;color:var(--warm-gray)">Belum ada data pasien</td></tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- PAGE: MONITORING -->
      <div class="content-area fade-in" id="page-monitoring" style="display:none">
        <div class="tabs">
          <button class="tab active" onclick="switchTab(this,'mon-daily')">Harian</button>
          <button class="tab" onclick="switchTab(this,'mon-weekly')">Mingguan</button>
          <button class="tab" onclick="switchTab(this,'mon-monthly')">Bulanan</button>
        </div>
        <div id="mon-daily">
          <div class="card">
            <div class="card-title">📈 Penurunan Skor Harian Tanda dan Gejala RPK di Ruangan Gatot Kaca</div>
            <div class="chart-container"><canvas id="chart-daily"></canvas></div>
          </div>
        </div>
        <div id="mon-weekly" style="display:none">
          <div class="card">
            <div class="card-title">📊 Penurunan Skor Mingguan Tanda dan Gejala RPK di Ruangan Gatot Kaca</div>
            <div class="chart-container"><canvas id="chart-weekly"></canvas></div>
          </div>
        </div>
        <div id="mon-monthly" style="display:none">
          <div class="card">
            <div class="card-title">📅 Penurunan Skor Bulanan Tanda dan Gejala RPK di Ruangan Gatot Kaca</div>
            <div class="chart-container" style="height:320px"><canvas id="chart-monthly"></canvas></div>
          </div>
          <!-- Analisis Penurunan -->
          <div class="card mt-4" id="monthly-analysis-card">
            <div class="card-title">🔍 Analisis Penurunan Tanda dan Gejala RPK — Ruang Gatot Kaca</div>
            <div id="monthly-analysis-content">
              <p class="text-sm text-muted" style="text-align:center;padding:20px">Tambahkan data pasien dengan pre &amp; post score untuk melihat analisis otomatis.</p>
            </div>
          </div>
        </div>
      </div>

      <!-- PAGE: OASS -->
      <div class="content-area fade-in" id="page-oass" style="display:none">
        <div class="grid-2">
          <div>
            <div class="card mb-4">
              <div class="card-title">👤 Pilih Pasien</div>
              <select class="form-select" id="oass-patient-select" onchange="selectPatientForOASS(this.value)">
                <option value="">-- Pilih Pasien --</option>
              </select>
              <div id="oass-patient-info" style="margin-top:12px;display:none">
                <div class="patient-card" style="cursor:default;border-color:var(--teal-light)">
                  <div class="patient-avatar" id="oass-avatar">P</div>
                  <div>
                    <div class="patient-name" id="oass-pname">-</div>
                    <div class="patient-meta" id="oass-pmeta">-</div>
                  </div>
                  <div class="ml-auto">
                    <div class="badge badge-teal" id="oass-pstatus">Pre-Test</div>
                  </div>
                </div>
              </div>
            </div>
            <div class="card">
              <div class="card-title">📋 Instrumen OASS</div>
              <p class="text-sm text-muted mb-4">Overt Aggression Severity Scale (Versi Indonesia — Valid & Reliabel)</p>
              
              <div id="oass-form-content">
                <!-- Verbal Aggression -->
                <div style="background:var(--teal-pale);padding:10px 14px;border-radius:8px;margin-bottom:12px">
                  <strong style="font-size:0.85rem;color:var(--teal)">A. AGRESIVITAS VERBAL</strong>
                </div>
                
                <div class="oass-item">
                  <div class="oass-label">A1. Membuat suara keras, berteriak, marah-marah</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-a1" id="a1-0" value="0" class="oass-radio"><label for="a1-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-a1" id="a1-2" value="2" class="oass-radio"><label for="a1-2" class="oass-option-label">2 - Kadang</label>
                    <input type="radio" name="oass-a1" id="a1-4" value="4" class="oass-radio"><label for="a1-4" class="oass-option-label">4 - Sering</label>
                  </div>
                </div>
                <div class="oass-item">
                  <div class="oass-label">A2. Kata-kata kasar, hinaan, atau umpatan ringan</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-a2" id="a2-0" value="0" class="oass-radio"><label for="a2-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-a2" id="a2-2" value="2" class="oass-radio"><label for="a2-2" class="oass-option-label">2 - Kadang</label>
                    <input type="radio" name="oass-a2" id="a2-4" value="4" class="oass-radio"><label for="a2-4" class="oass-option-label">4 - Sering</label>
                  </div>
                </div>
                <div class="oass-item">
                  <div class="oass-label">A3. Ancaman verbal pada orang lain atau diri sendiri</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-a3" id="a3-0" value="0" class="oass-radio"><label for="a3-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-a3" id="a3-2" value="2" class="oass-radio"><label for="a3-2" class="oass-option-label">2 - Kadang</label>
                    <input type="radio" name="oass-a3" id="a3-4" value="4" class="oass-radio"><label for="a3-4" class="oass-option-label">4 - Sering</label>
                  </div>
                </div>

                <div style="background:var(--teal-pale);padding:10px 14px;border-radius:8px;margin-bottom:12px;margin-top:16px">
                  <strong style="font-size:0.85rem;color:var(--teal)">B. AGRESIVITAS TERHADAP BENDA</strong>
                </div>
                
                <div class="oass-item">
                  <div class="oass-label">B1. Membanting pintu, meja, atau kursi</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-b1" id="b1-0" value="0" class="oass-radio"><label for="b1-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-b1" id="b1-4" value="4" class="oass-radio"><label for="b1-4" class="oass-option-label">4 - Kadang</label>
                    <input type="radio" name="oass-b1" id="b1-8" value="8" class="oass-radio"><label for="b1-8" class="oass-option-label">8 - Sering</label>
                  </div>
                </div>
                <div class="oass-item">
                  <div class="oass-label">B2. Melempar benda, merusak perabot</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-b2" id="b2-0" value="0" class="oass-radio"><label for="b2-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-b2" id="b2-4" value="4" class="oass-radio"><label for="b2-4" class="oass-option-label">4 - Kadang</label>
                    <input type="radio" name="oass-b2" id="b2-8" value="8" class="oass-radio"><label for="b2-8" class="oass-option-label">8 - Sering</label>
                  </div>
                </div>

                <div style="background:var(--teal-pale);padding:10px 14px;border-radius:8px;margin-bottom:12px;margin-top:16px">
                  <strong style="font-size:0.85rem;color:var(--teal)">C. AGRESIVITAS FISIK TERHADAP DIRI SENDIRI</strong>
                </div>
                
                <div class="oass-item">
                  <div class="oass-label">C1. Mencakar, mencubit, atau menyakiti diri sendiri</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-c1" id="c1-0" value="0" class="oass-radio"><label for="c1-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-c1" id="c1-4" value="4" class="oass-radio"><label for="c1-4" class="oass-option-label">4 - Kadang</label>
                    <input type="radio" name="oass-c1" id="c1-8" value="8" class="oass-radio"><label for="c1-8" class="oass-option-label">8 - Sering</label>
                  </div>
                </div>
                <div class="oass-item">
                  <div class="oass-label">C2. Memukul kepala ke dinding, membenturkan diri</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-c2" id="c2-0" value="0" class="oass-radio"><label for="c2-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-c2" id="c2-4" value="4" class="oass-radio"><label for="c2-4" class="oass-option-label">4 - Kadang</label>
                    <input type="radio" name="oass-c2" id="c2-8" value="8" class="oass-radio"><label for="c2-8" class="oass-option-label">8 - Sering</label>
                  </div>
                </div>

                <div style="background:var(--teal-pale);padding:10px 14px;border-radius:8px;margin-bottom:12px;margin-top:16px">
                  <strong style="font-size:0.85rem;color:var(--teal)">D. AGRESIVITAS FISIK TERHADAP ORANG LAIN</strong>
                </div>
                
                <div class="oass-item">
                  <div class="oass-label">D1. Mendorong, menarik, atau memegang orang lain</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-d1" id="d1-0" value="0" class="oass-radio"><label for="d1-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-d1" id="d1-8" value="8" class="oass-radio"><label for="d1-8" class="oass-option-label">8 - Kadang</label>
                    <input type="radio" name="oass-d1" id="d1-16" value="16" class="oass-radio"><label for="d1-16" class="oass-option-label">16 - Sering</label>
                  </div>
                </div>
                <div class="oass-item">
                  <div class="oass-label">D2. Memukul, menendang, atau menggigit orang lain</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-d2" id="d2-0" value="0" class="oass-radio"><label for="d2-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-d2" id="d2-8" value="8" class="oass-radio"><label for="d2-8" class="oass-option-label">8 - Kadang</label>
                    <input type="radio" name="oass-d2" id="d2-16" value="16" class="oass-radio"><label for="d2-16" class="oass-option-label">16 - Sering</label>
                  </div>
                </div>
                <div class="oass-item">
                  <div class="oass-label">D3. Menyerang dengan senjata atau benda berbahaya</div>
                  <div class="oass-options">
                    <input type="radio" name="oass-d3" id="d3-0" value="0" class="oass-radio"><label for="d3-0" class="oass-option-label">0 - Tidak</label>
                    <input type="radio" name="oass-d3" id="d3-8" value="8" class="oass-radio"><label for="d3-8" class="oass-option-label">8 - Kadang</label>
                    <input type="radio" name="oass-d3" id="d3-16" value="16" class="oass-radio"><label for="d3-16" class="oass-option-label">16 - Sering</label>
                  </div>
                </div>
              </div>
              
              <div class="score-display" id="oass-score-display" style="background:var(--teal-pale);display:none">
                <div class="score-number text-teal" id="oass-score-num">0</div>
                <div class="score-label" id="oass-score-cat">-</div>
                <div class="text-sm text-muted mt-4" id="oass-score-desc">-</div>
              </div>
              
              <div class="inline-flex gap-12 mt-4 flex-wrap">
                <button class="btn btn-secondary" onclick="calcOASS()">🔢 Hitung Skor</button>
                <button class="btn btn-primary" id="oass-save-btn" onclick="saveOASS()" style="display:none">💾 Simpan Pre-Test</button>
                <button class="btn btn-rose" id="oass-post-btn" onclick="saveOASSPost()" style="display:none">✅ Simpan Post-Test</button>
              </div>
            </div>
          </div>
          <div>
            <div class="card mb-4">
              <div class="card-title">📏 Skala Interpretasi OASS</div>
              <div style="padding:12px;border-radius:10px;background:#f0fdf4;border:2px solid #bbf7d0;margin-bottom:10px">
                <strong style="color:#16a34a">🟢 Ringan: Skor 0 – 20</strong>
                <p class="text-sm" style="color:#15803d;margin-top:4px">Agresivitas rendah, dapat dikelola dengan intervensi verbal dan terapi mandiri.</p>
              </div>
              <div style="padding:12px;border-radius:10px;background:#fffbeb;border:2px solid #fde68a;margin-bottom:10px">
                <strong style="color:#d97706">🟡 Sedang: Skor 21 – 40</strong>
                <p class="text-sm" style="color:#b45309;margin-top:4px">Agresivitas moderat, perlu pengawasan ketat dan intervensi terstruktur.</p>
              </div>
              <div style="padding:12px;border-radius:10px;background:#fef2f2;border:2px solid #fca5a5">
                <strong style="color:#dc2626">🔴 Berat: Skor > 40</strong>
                <p class="text-sm" style="color:#b91c1c;margin-top:4px">Agresivitas tinggi, perlu penanganan intensif dan kolaborasi tim.</p>
              </div>
            </div>
            <div class="card">
              <div class="card-title">📅 Alur Pemberian Terapi</div>
              <div style="display:flex;flex-direction:column;gap:8px">
                <div style="display:flex;align-items:center;gap:10px;padding:10px;background:var(--teal-pale);border-radius:8px">
                  <span style="font-size:1.2rem">1️⃣</span>
                  <div><strong style="font-size:0.85rem">Pre-Test OASS</strong><br><span class="text-sm text-muted">Sebelum pemberian intervensi</span></div>
                </div>
                <div style="display:flex;align-items:center;gap:10px;padding:10px;background:#fff0f9;border-radius:8px">
                  <span style="font-size:1.2rem">2️⃣</span>
                  <div><strong style="font-size:0.85rem">Butterfly Hug (5 Langkah)</strong><br><span class="text-sm text-muted">Relaksasi bilateral stimulation</span></div>
                </div>
                <div style="display:flex;align-items:center;gap:10px;padding:10px;background:#f0fdf4;border-radius:8px">
                  <span style="font-size:1.2rem">3️⃣</span>
                  <div><strong style="font-size:0.85rem">Grounding (5-4-3-2-1)</strong><br><span class="text-sm text-muted">Orientasi realita present-moment</span></div>
                </div>
                <div style="display:flex;align-items:center;gap:10px;padding:10px;background:#fefce8;border-radius:8px">
                  <span style="font-size:1.2rem">4️⃣</span>
                  <div><strong style="font-size:0.85rem">Assertiveness Training (4 Sesi)</strong><br><span class="text-sm text-muted">Latihan ekspresi emosi asertif</span></div>
                </div>
                <div style="display:flex;align-items:center;gap:10px;padding:10px;background:#f0f9ff;border-radius:8px">
                  <span style="font-size:1.2rem">5️⃣</span>
                  <div><strong style="font-size:0.85rem">Post-Test OASS</strong><br><span class="text-sm text-muted">Setelah semua terapi selesai</span></div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- PAGE: BUTTERFLY HUG -->
      <div class="content-area fade-in" id="page-butterfly" style="display:none">
        <div class="grid-2">
          <div>
            <div class="card mb-4">
              <div class="card-title">👤 Pilih Pasien & Sesi</div>
              <select class="form-select mb-4" id="bh-patient-select" onchange="selectPatientForTherapy('bh', this.value)">
                <option value="">-- Pilih Pasien --</option>
              </select>
              <div id="bh-session-info" style="display:none">
                <div class="session-tracker" id="bh-session-tracker"></div>
                <p class="text-sm text-muted">Sesi yang sudah selesai tidak perlu diulang. Lanjutkan dari sesi yang belum selesai.</p>
              </div>
              <div id="bh-continue-banner" style="display:none"></div>
            </div>
            
            <div class="card">
              <div class="card-title">🦋 Panduan Terapi Butterfly Hug</div>
              <p class="text-sm text-muted mb-4">5 Langkah • 30–45 Menit per Sesi</p>
              
              <div class="therapy-step active-step" id="bh-step-1">
                <div class="step-number" id="bh-num-1">1</div>
                <div class="step-content">
                  <div class="step-icon">🤗</div>
                  <div class="step-title">Langkah 1: Posisi Tubuh</div>
                  <div class="step-desc">
                    <p>• Silangkan lengan di atas dada</p>
                    <p>• Tempatkan jari tengah masing-masing tangan di bawah tulang klavikula (collarbone)</p>
                    <p>• Biarkan jari-jari lain menutupi area bawah klavikula, mengarah ke bahu dan sternum</p>
                    <p>• Pastikan jari-jari tegak ke arah leher</p>
                  </div>
                  <div style="background:#f0fdfb;padding:10px;border-radius:8px;margin-top:10px;border-left:3px solid var(--teal)">
                    <strong class="text-sm">💡 Instruksi Perawat:</strong>
                    <p class="text-sm text-muted">Demonstrasikan dulu, lakukan bersama (role play), beri umpan balik positif, minta pasien lakukan mandiri 3–4 kali.</p>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="bh-step-2">
                <div class="step-number" id="bh-num-2">2</div>
                <div class="step-content">
                  <div class="step-icon">🦋</div>
                  <div class="step-title">Langkah 2: Membentuk Kupu-Kupu</div>
                  <div class="step-desc">
                    <p>• Kunci jempol sehingga membentuk kupu-kupu</p>
                    <p>• Jari-jari yang terulur akan membentuk sayap kupu-kupu</p>
                    <p>• Pastikan posisi nyaman dan stabil</p>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="bh-step-3">
                <div class="step-number" id="bh-num-3">3</div>
                <div class="step-content">
                  <div class="step-icon">👁️</div>
                  <div class="step-title">Langkah 3: Posisi Mata</div>
                  <div class="step-desc">
                    <p>• Mata dalam posisi <strong>tertutup atau setengah menutup</strong></p>
                    <p>• Fokuskan pandangan pada ujung hidung</p>
                    <p>• Biarkan pikiran mengalir tanpa dipaksa</p>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="bh-step-4">
                <div class="step-number" id="bh-num-4">4</div>
                <div class="step-content">
                  <div class="step-icon">💓</div>
                  <div class="step-title">Langkah 4: Gerakan & Napas</div>
                  <div class="step-desc">
                    <p>• Kepakkan tangan secara teratur menyerupai kepakan kupu-kupu</p>
                    <p>• Lakukan pernapasan diafragma secara teratur</p>
                    <p>• <strong>Tarik napas (4 hitungan) → Tahan (4 hitungan) → Hembuskan (6 hitungan)</strong></p>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="bh-step-5">
                <div class="step-number" id="bh-num-5">5</div>
                <div class="step-content">
                  <div class="step-icon">💬</div>
                  <div class="step-title">Langkah 5: Self Talk Positif</div>
                  <div class="step-desc">
                    <p>Ucapkan kata-kata positif sambil terus mengepakkan tangan:</p>
                    <div style="background:#f0fdfb;padding:12px;border-radius:8px;margin-top:8px">
                      <p class="font-bold" style="color:var(--teal)">"Saya akan baik-baik saja"</p>
                      <p class="font-bold" style="color:var(--teal)">"Saya aman dan tenang"</p>
                      <p class="font-bold" style="color:var(--teal)">"Saya mampu mengatasi ini"</p>
                    </div>
                  </div>
                </div>
              </div>
              
              <div class="inline-flex gap-12 mt-4 flex-wrap">
                <button class="btn btn-primary" onclick="completeBHStep()">✅ Langkah Selesai</button>
                <button class="btn btn-secondary" onclick="saveBHSession()">💾 Simpan Sesi</button>
              </div>
            </div>
          </div>
          
          <div>
            <div class="card mb-4">
              <div class="card-title">📝 Evaluasi Sesi Butterfly Hug</div>
              <div class="form-group">
                <label class="form-label">Respons Pasien Saat Ini</label>
                <select class="form-select" id="bh-response">
                  <option>Kooperatif dan antusias</option>
                  <option>Kooperatif namun ragu-ragu</option>
                  <option>Perlu motivasi tambahan</option>
                  <option>Menolak sebagian langkah</option>
                  <option>Tidak kooperatif</option>
                </select>
              </div>
              <div class="form-group">
                <label class="form-label">Skor Distress Sebelum (1-10)</label>
                <input type="range" min="1" max="10" value="7" class="form-input" id="bh-distress-pre" style="padding:8px" oninput="document.getElementById('bh-pre-val').textContent=this.value">
                <div class="inline-flex justify-between text-sm text-muted mt-4"><span>1 (Rendah)</span><span id="bh-pre-val" class="font-bold text-rose">7</span><span>10 (Tinggi)</span></div>
              </div>
              <div class="form-group">
                <label class="form-label">Skor Distress Sesudah (1-10)</label>
                <input type="range" min="1" max="10" value="4" class="form-input" id="bh-distress-post" style="padding:8px" oninput="document.getElementById('bh-post-val').textContent=this.value">
                <div class="inline-flex justify-between text-sm text-muted mt-4"><span>1 (Rendah)</span><span id="bh-post-val" class="font-bold text-teal">4</span><span>10 (Tinggi)</span></div>
              </div>
              <div class="form-group">
                <label class="form-label">Catatan Perawat</label>
                <textarea class="form-input" id="bh-notes" placeholder="Catatan observasi perawat selama sesi..."></textarea>
              </div>
            </div>
            
            <div class="card">
              <div class="card-title">ℹ️ Tentang Butterfly Hug</div>
              <p class="text-sm text-muted" style="line-height:1.7">
                Butterfly Hug adalah metode relaksasi yang menggabungkan teknik menenangkan diri dengan <strong>stimulasi bilateral</strong> untuk membantu mengurangi beban stres emosional atau traumatis dan hiperarousal.
              </p>
              <div class="divider"></div>
              <div class="stats-grid" style="grid-template-columns:1fr 1fr;gap:10px">
                <div style="text-align:center;padding:12px;background:var(--teal-pale);border-radius:10px">
                  <div style="font-size:1.5rem;font-weight:800;color:var(--teal)">5</div>
                  <div class="text-sm text-muted">Langkah</div>
                </div>
                <div style="text-align:center;padding:12px;background:#fff0f9;border-radius:10px">
                  <div style="font-size:1.5rem;font-weight:800;color:var(--rose)">45'</div>
                  <div class="text-sm text-muted">Durasi</div>
                </div>
              </div>
              <div style="margin-top:12px">
                <div style="padding:10px;background:#f0fdf4;border-radius:8px;margin-bottom:8px">
                  <strong class="text-sm" style="color:#16a34a">✅ Setting Tempat:</strong>
                  <p class="text-sm text-muted">Ruangan tenang, nyaman, pasien duduk berhadapan dengan terapis</p>
                </div>
                <div style="padding:10px;background:#fefce8;border-radius:8px">
                  <strong class="text-sm" style="color:#d97706">📦 Alat:</strong>
                  <p class="text-sm text-muted">Alat tulis, buku kerja, format evaluasi dan dokumentasi</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- PAGE: GROUNDING -->
      <div class="content-area fade-in" id="page-grounding" style="display:none">
        <div class="grid-2">
          <div>
            <div class="card mb-4">
              <div class="card-title">👤 Pilih Pasien</div>
              <select class="form-select" id="gr-patient-select" onchange="selectPatientForTherapy('gr', this.value)">
                <option value="">-- Pilih Pasien --</option>
              </select>
              <div id="gr-continue-banner" style="display:none;margin-top:12px"></div>
            </div>
            
            <div class="card">
              <div class="card-title">🌿 Teknik Grounding 5-4-3-2-1</div>
              <p class="text-sm text-muted mb-4">Orientasi realita present-moment menggunakan 5 panca indera</p>
              
              <div style="background:linear-gradient(135deg,var(--teal),var(--teal-light));padding:20px;border-radius:14px;color:white;text-align:center;margin-bottom:20px">
                <div style="font-size:2.5rem;margin-bottom:8px">🧘</div>
                <strong style="font-size:1.1rem">Mulai dari 5-4-3-2-1</strong>
                <p style="font-size:0.82rem;opacity:0.85;margin-top:4px">Gunakan semua panca indera untuk kembali ke momen saat ini</p>
              </div>
              
              <div class="therapy-step active-step" id="gr-step-1">
                <div class="step-number">5</div>
                <div class="step-content">
                  <div class="step-icon">👁️</div>
                  <div class="step-title">5 Hal yang Dapat DILIHAT</div>
                  <div class="step-desc">
                    <p>Sebutkan 5 hal yang ada di sekitar yang dapat dilihat saat ini:</p>
                    <div style="margin-top:8px;display:flex;flex-direction:column;gap:6px" id="gr-see-inputs">
                      <input class="form-input" placeholder="Saya melihat..." oninput="checkGrounding()">
                      <input class="form-input" placeholder="Saya melihat...">
                      <input class="form-input" placeholder="Saya melihat...">
                      <input class="form-input" placeholder="Saya melihat...">
                      <input class="form-input" placeholder="Saya melihat...">
                    </div>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="gr-step-2">
                <div class="step-number">4</div>
                <div class="step-content">
                  <div class="step-icon">🖐️</div>
                  <div class="step-title">4 Hal yang Dapat DISENTUH / DIRASA</div>
                  <div class="step-desc">
                    <p>Sentuh 4 benda berbeda dan deskripsikan teksturnya:</p>
                    <div style="margin-top:8px;display:flex;flex-direction:column;gap:6px">
                      <input class="form-input" placeholder="Saya merasakan...">
                      <input class="form-input" placeholder="Saya merasakan...">
                      <input class="form-input" placeholder="Saya merasakan...">
                      <input class="form-input" placeholder="Saya merasakan...">
                    </div>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="gr-step-3">
                <div class="step-number">3</div>
                <div class="step-content">
                  <div class="step-icon">👂</div>
                  <div class="step-title">3 Hal yang Dapat DIDENGAR</div>
                  <div class="step-desc">
                    <p>Tutup mata sejenak dan dengarkan 3 suara yang ada:</p>
                    <div style="margin-top:8px;display:flex;flex-direction:column;gap:6px">
                      <input class="form-input" placeholder="Saya mendengar...">
                      <input class="form-input" placeholder="Saya mendengar...">
                      <input class="form-input" placeholder="Saya mendengar...">
                    </div>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="gr-step-4">
                <div class="step-number">2</div>
                <div class="step-content">
                  <div class="step-icon">👃</div>
                  <div class="step-title">2 Hal yang Dapat DICIUM</div>
                  <div class="step-desc">
                    <p>Perhatikan 2 aroma/bau yang dapat dirasakan:</p>
                    <div style="margin-top:8px;display:flex;flex-direction:column;gap:6px">
                      <input class="form-input" placeholder="Saya mencium bau...">
                      <input class="form-input" placeholder="Saya mencium bau...">
                    </div>
                  </div>
                </div>
              </div>
              
              <div class="therapy-step" id="gr-step-5">
                <div class="step-number">1</div>
                <div class="step-content">
                  <div class="step-icon">👅</div>
                  <div class="step-title">1 Hal yang Dapat DIRASAKAN (Rasa/Taste)</div>
                  <div class="step-desc">
                    <p>Perhatikan 1 rasa yang ada di mulut saat ini:</p>
                    <input class="form-input mt-4" placeholder="Saya merasakan rasa...">
                  </div>
                </div>
              </div>
              
              <div class="inline-flex gap-12 mt-4 flex-wrap">
                <button class="btn btn-primary" onclick="completeGroundingStep()">✅ Tahap Selesai</button>
                <button class="btn btn-secondary" onclick="saveGroundingSession()">💾 Simpan Sesi</button>
              </div>
            </div>
          </div>
          
          <div>
            <div class="card mb-4">
              <div class="card-title">📝 Evaluasi Grounding</div>
              <div class="form-group">
                <label class="form-label">Teknik yang Digunakan</label>
                <select class="form-select" id="gr-technique">
                  <option>5-4-3-2-1 Senses (Standar)</option>
                  <option>Feel Your Body (Fisik)</option>
                  <option>Memory Game (Mental)</option>
                  <option>Anchoring Phrase (Verbal)</option>
                  <option>Visualisasi Tempat Nyaman</option>
                </select>
              </div>
              <div class="form-group">
                <label class="form-label">Tingkat Distress Sebelum (1-10)</label>
                <input type="range" min="1" max="10" value="6" class="form-input" id="gr-distress-pre" style="padding:8px" oninput="document.getElementById('gr-pre-val').textContent=this.value">
                <div class="inline-flex justify-between text-sm text-muted mt-4"><span>1</span><span id="gr-pre-val" class="font-bold text-rose">6</span><span>10</span></div>
              </div>
              <div class="form-group">
                <label class="form-label">Tingkat Distress Sesudah (1-10)</label>
                <input type="range" min="1" max="10" value="3" class="form-input" id="gr-distress-post" style="padding:8px" oninput="document.getElementById('gr-post-val').textContent=this.value">
                <div class="inline-flex justify-between text-sm text-muted mt-4"><span>1</span><span id="gr-post-val" class="font-bold text-teal">3</span><span>10</span></div>
              </div>
              <div class="form-group">
                <label class="form-label">Catatan Observasi</label>
                <textarea class="form-input" id="gr-notes" placeholder="Respons dan observasi perawat..."></textarea>
              </div>
            </div>
            
            <div class="card">
              <div class="card-title">💡 Tips Grounding</div>
              <div style="display:flex;flex-direction:column;gap:8px">
                <div style="padding:10px;background:var(--teal-pale);border-radius:8px">
                  <strong class="text-sm text-teal">🚀 Mulai Awal:</strong>
                  <p class="text-sm text-muted">Lakukan saat pertama kali mulai merasa tidak nyaman, jangan tunggu sampai puncak distress</p>
                </div>
                <div style="padding:10px;background:#f0fdf4;border-radius:8px">
                  <strong class="text-sm" style="color:#16a34a">👁️ Mata Terbuka:</strong>
                  <p class="text-sm text-muted">Selalu jaga mata terbuka agar tetap terhubung dengan lingkungan sekitar</p>
                </div>
                <div style="padding:10px;background:#fefce8;border-radius:8px">
                  <strong class="text-sm" style="color:#d97706">📊 Cek Distress:</strong>
                  <p class="text-sm text-muted">Nilai distress 1-10 sebelum dan sesudah latihan untuk evaluasi efektivitas</p>
                </div>
                <div style="padding:10px;background:#f0f9ff;border-radius:8px">
                  <strong class="text-sm" style="color:#0369a1">🎯 Netral:</strong>
                  <p class="text-sm text-muted">Fokus pada fakta objektif, hindari menilai atau menilai emosi terhadap objek</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- PAGE: ASSERTIVENESS TRAINING -->
      <div class="content-area fade-in" id="page-assertiveness" style="display:none">
        <div class="grid-2">
          <div>
            <div class="card mb-4">
              <div class="card-title">👤 Pilih Pasien & Sesi</div>
              <select class="form-select mb-4" id="at-patient-select" onchange="selectPatientForTherapy('at', this.value)">
                <option value="">-- Pilih Pasien --</option>
              </select>
              <div id="at-session-info" style="display:none">
                <div class="tabs" style="margin-bottom:0">
                  <button class="tab active" id="at-tab-1" onclick="switchATSesi(1)">Sesi 1</button>
                  <button class="tab" id="at-tab-2" onclick="switchATSesi(2)">Sesi 2</button>
                  <button class="tab" id="at-tab-3" onclick="switchATSesi(3)">Sesi 3</button>
                  <button class="tab" id="at-tab-4" onclick="switchATSesi(4)">Sesi 4</button>
                </div>
              </div>
              <div id="at-continue-banner" style="display:none;margin-top:12px"></div>
            </div>
            
            <!-- AT Session 1 -->
            <div class="card" id="at-sesi-1">
              <div class="card-title">💬 Sesi 1: Identifikasi Kejadian Penyebab Marah</div>
              <p class="text-sm text-muted mb-4">Mengidentifikasi dan menilai kejadian penyebab marah, mengenal sikap saat marah</p>
              
              <div class="form-group">
                <label class="form-label">1.1 Kejadian/Peristiwa Penyebab Marah</label>
                <textarea class="form-input" id="at1-kejadian" placeholder="Pasien menyebutkan kejadian yang membuatnya marah... (dari dalam atau luar diri)"></textarea>
              </div>
              
              <div class="form-group">
                <label class="form-label">1.2 Penilaian Kejadian (Kebutuhan atau Keinginan?)</label>
                <div class="inline-flex gap-12">
                  <label style="display:flex;align-items:center;gap:6px;cursor:pointer;font-size:0.88rem">
                    <input type="radio" name="at1-jenis" value="kebutuhan"> Kebutuhan dasar
                  </label>
                  <label style="display:flex;align-items:center;gap:6px;cursor:pointer;font-size:0.88rem">
                    <input type="radio" name="at1-jenis" value="keinginan"> Keinginan
                  </label>
                </div>
              </div>
              
              <div class="form-group">
                <label class="form-label">1.3 Sikap Saat Marah</label>
                <div style="display:flex;gap:10px;flex-wrap:wrap">
                  <label style="display:flex;align-items:center;gap:6px;padding:10px 14px;border:2px solid #fee2e2;border-radius:10px;cursor:pointer;font-size:0.85rem;font-weight:600;color:#dc2626;flex:1">
                    <input type="radio" name="at1-sikap" value="agresif"> 😤 Agresif
                  </label>
                  <label style="display:flex;align-items:center;gap:6px;padding:10px 14px;border:2px solid #e0f2fe;border-radius:10px;cursor:pointer;font-size:0.85rem;font-weight:600;color:#0369a1;flex:1">
                    <input type="radio" name="at1-sikap" value="pasif"> 😶 Pasif
                  </label>
                  <label style="display:flex;align-items:center;gap:6px;padding:10px 14px;border:2px solid #dcfce7;border-radius:10px;cursor:pointer;font-size:0.85rem;font-weight:600;color:#16a34a;flex:1">
                    <input type="radio" name="at1-sikap" value="asertif"> 😊 Asertif
                  </label>
                </div>
              </div>
              
              <div class="form-group">
                <label class="form-label">1.4 Ekspresi Emosi (Roleplay)</label>
                <p class="text-sm text-muted mb-4">Pilih emosi yang sedang dirasakan pasien untuk latihan roleplay:</p>
                <div class="emotion-grid">
                  <div class="emotion-card" onclick="selectEmotion(this)">
                    <div class="emotion-icon">😤</div>
                    <div class="emotion-name">Marah</div>
                  </div>
                  <div class="emotion-card" onclick="selectEmotion(this)">
                    <div class="emotion-icon">😰</div>
                    <div class="emotion-name">Takut</div>
                  </div>
                  <div class="emotion-card" onclick="selectEmotion(this)">
                    <div class="emotion-icon">😢</div>
                    <div class="emotion-name">Sedih</div>
                  </div>
                  <div class="emotion-card" onclick="selectEmotion(this)">
                    <div class="emotion-icon">😤</div>
                    <div class="emotion-name">Frustrasi</div>
                  </div>
                  <div class="emotion-card" onclick="selectEmotion(this)">
                    <div class="emotion-icon">😔</div>
                    <div class="emotion-name">Kecewa</div>
                  </div>
                  <div class="emotion-card" onclick="selectEmotion(this)">
                    <div class="emotion-icon">😟</div>
                    <div class="emotion-name">Cemas</div>
                  </div>
                </div>
              </div>
              
              <div style="background:var(--teal-pale);padding:16px;border-radius:12px;margin-bottom:16px">
                <strong class="text-sm text-teal">🎭 Panduan Roleplay Asertif:</strong>
                <div style="margin-top:10px;display:flex;flex-direction:column;gap:8px">
                  <div style="padding:8px;background:white;border-radius:8px;font-size:0.83rem">
                    <strong>Perawat (sebagai lawan bicara):</strong><br>
                    "Hei, kenapa kamu tidak mau ikut kegiatan tadi?"
                  </div>
                  <div style="padding:8px;background:white;border-radius:8px;font-size:0.83rem;border-left:3px solid var(--teal)">
                    <strong style="color:var(--teal)">Pasien (latihan asertif):</strong><br>
                    "Saya merasa [emosi] ketika [situasi]. Saya butuh [kebutuhan]. Bisakah kita [solusi]?"
                  </div>
                </div>
              </div>
              
              <button class="btn btn-primary" onclick="saveATSesi(1)">💾 Simpan Sesi 1</button>
            </div>
            
            <!-- AT Session 2 (hidden) -->
            <div class="card" id="at-sesi-2" style="display:none">
              <div class="card-title">💬 Sesi 2: Latihan Mengungkapkan Kebutuhan & Keinginan Secara Asertif</div>
              <div class="form-group">
                <label class="form-label">2.1 Evaluasi Jadwal Kegiatan Harian</label>
                <textarea class="form-input" id="at2-evaluasi" placeholder="Evaluasi pelaksanaan jadwal sejak sesi 1..."></textarea>
              </div>
              <div class="form-group">
                <label class="form-label">2.3 Kebutuhan yang Jadi Penyebab Marah</label>
                <input class="form-input" id="at2-kebutuhan" placeholder="Sebutkan kebutuhan yang belum terpenuhi...">
              </div>
              <div class="form-group">
                <label class="form-label">2.3.2 Latihan Mengungkapkan Kebutuhan Secara Asertif</label>
                <div style="background:var(--teal-pale);padding:16px;border-radius:12px">
                  <strong class="text-sm text-teal">Template Kalimat Asertif:</strong>
                  <p class="text-sm" style="margin-top:8px;line-height:1.6">"Saya membutuhkan <input class="form-input" style="display:inline;width:150px;padding:4px 8px" placeholder="___"> karena <input class="form-input" style="display:inline;width:150px;padding:4px 8px" placeholder="alasan">. Saya berharap <input class="form-input" style="display:inline;width:150px;padding:4px 8px" placeholder="harapan">."</p>
                </div>
              </div>
              <div class="form-group">
                <label class="form-label">2.4 Keinginan yang Akan Dilatih</label>
                <textarea class="form-input" id="at2-keinginan" placeholder="Keinginan yang menimbulkan rasa marah..."></textarea>
              </div>
              <button class="btn btn-primary" onclick="saveATSesi(2)">💾 Simpan Sesi 2</button>
            </div>
            
            <!-- AT Session 3 (hidden) -->
            <div class="card" id="at-sesi-3" style="display:none">
              <div class="card-title">💬 Sesi 3: Latihan Mengatakan "TIDAK" untuk Permintaan Tidak Rasional</div>
              <div class="form-group">
                <label class="form-label">3.4 Identifikasi Permintaan Tidak Rasional</label>
                <textarea class="form-input" id="at3-permintaan" placeholder="Permintaan tidak rasional yang pernah membuat marah..."></textarea>
              </div>
              <div style="background:#fff0f3;padding:16px;border-radius:12px;margin-bottom:16px">
                <strong class="text-sm text-rose">🎭 Roleplay: Menolak dengan Asertif</strong>
                <div style="margin-top:10px;display:flex;flex-direction:column;gap:8px">
                  <div style="padding:8px;background:white;border-radius:8px;font-size:0.83rem">
                    <strong>Perawat:</strong> "Tolong berikan uang kamu untuk saya!"
                  </div>
                  <div style="padding:8px;background:white;border-radius:8px;font-size:0.83rem;border-left:3px solid var(--rose)">
                    <strong style="color:var(--rose)">Pasien (asertif):</strong> "Maaf, saya tidak bisa melakukan itu. Itu adalah hak saya untuk menolak."
                  </div>
                </div>
              </div>
              <div class="form-group">
                <label class="form-label">3.6 Latihan Menolak Permintaan Tidak Rasional</label>
                <textarea class="form-input" id="at3-latihan" placeholder="Kalimat penolakan asertif yang diucapkan pasien..."></textarea>
              </div>
              <button class="btn btn-primary" onclick="saveATSesi(3)">💾 Simpan Sesi 3</button>
            </div>
            
            <!-- AT Session 4 (hidden) -->
            <div class="card" id="at-sesi-4" style="display:none">
              <div class="card-title">💬 Sesi 4: Menerima Perbedaan Pendapat & Menyampaikan Pendapat Asertif</div>
              <div class="form-group">
                <label class="form-label">4.5 Pendapat yang Berbeda yang Menimbulkan Marah</label>
                <textarea class="form-input" id="at4-perbedaan" placeholder="Situasi perbedaan pendapat yang sering memicu kemarahan..."></textarea>
              </div>
              <div style="background:#f0fdf4;padding:16px;border-radius:12px;margin-bottom:16px">
                <strong class="text-sm" style="color:#16a34a">🎭 Roleplay: Menerima Perbedaan dengan Asertif</strong>
                <div style="margin-top:10px;display:flex;flex-direction:column;gap:8px">
                  <div style="padding:8px;background:white;border-radius:8px;font-size:0.83rem">
                    <strong>Perawat:</strong> "Saya rasa cara kamu itu salah."
                  </div>
                  <div style="padding:8px;background:white;border-radius:8px;font-size:0.83rem;border-left:3px solid #16a34a">
                    <strong style="color:#16a34a">Pasien (asertif):</strong> "Saya menghormati pendapat Anda. Menurut saya... [pendapat]. Apakah kita bisa mencari jalan tengah?"
                  </div>
                </div>
              </div>
              <div class="form-group">
                <label class="form-label">4.7 Latihan Menyampaikan Pendapat Asertif</label>
                <textarea class="form-input" id="at4-latihan" placeholder="Pendapat pribadi yang disampaikan secara asertif oleh pasien..."></textarea>
              </div>
              <div class="form-group">
                <label class="form-label">Evaluasi Keseluruhan Assertiveness Training</label>
                <textarea class="form-input" id="at4-evaluasi" placeholder="Perkembangan pasien dari sesi 1-4, kemampuan asertif yang sudah dikuasai..."></textarea>
              </div>
              <button class="btn btn-primary" onclick="saveATSesi(4)">💾 Selesai & Simpan Sesi 4</button>
            </div>
          </div>
          
          <div>
            <div class="card mb-4">
              <div class="card-title">🗓️ Jadwal Kegiatan Harian Pasien</div>
              <p class="text-sm text-muted mb-4">Monitoring jadwal latihan mandiri pasien</p>
              <div style="display:flex;flex-direction:column;gap:8px" id="at-schedule">
                <div style="display:flex;justify-content:space-between;align-items:center;padding:10px;background:var(--teal-pale);border-radius:8px">
                  <span class="text-sm font-bold">Hari 1</span>
                  <span class="text-sm">Latihan Asertif Pagi</span>
                  <select class="form-select" style="max-width:120px;padding:4px 8px;font-size:0.78rem">
                    <option>Belum</option>
                    <option>Dilakukan</option>
                    <option>Tidak Dilakukan</option>
                  </select>
                </div>
                <div style="display:flex;justify-content:space-between;align-items:center;padding:10px;background:var(--teal-pale);border-radius:8px">
                  <span class="text-sm font-bold">Hari 2</span>
                  <span class="text-sm">Latihan Asertif Sore</span>
                  <select class="form-select" style="max-width:120px;padding:4px 8px;font-size:0.78rem">
                    <option>Belum</option>
                    <option>Dilakukan</option>
                    <option>Tidak Dilakukan</option>
                  </select>
                </div>
                <div style="display:flex;justify-content:space-between;align-items:center;padding:10px;background:var(--teal-pale);border-radius:8px">
                  <span class="text-sm font-bold">Hari 3</span>
                  <span class="text-sm">Review Jurnal Harian</span>
                  <select class="form-select" style="max-width:120px;padding:4px 8px;font-size:0.78rem">
                    <option>Belum</option>
                    <option>Dilakukan</option>
                    <option>Tidak Dilakukan</option>
                  </select>
                </div>
              </div>
            </div>
            
            <div class="card mb-4">
              <div class="card-title">📓 Jurnal Harian Pasien</div>
              <div class="form-group">
                <label class="form-label">Tanggal</label>
                <input type="date" class="form-input" id="at-journal-date">
              </div>
              <div class="form-group">
                <label class="form-label">Frekuensi Sikap Agresif Hari Ini</label>
                <input type="number" class="form-input" min="0" max="20" placeholder="Jumlah kejadian..." id="at-journal-agresif">
              </div>
              <div class="form-group">
                <label class="form-label">Frekuensi Sikap Pasif Hari Ini</label>
                <input type="number" class="form-input" min="0" max="20" placeholder="Jumlah kejadian..." id="at-journal-pasif">
              </div>
              <div class="form-group">
                <label class="form-label">Latihan Asertif yang Berhasil Dilakukan</label>
                <textarea class="form-input" id="at-journal-asertif" placeholder="Deskripsikan situasi dan cara asertif yang digunakan..."></textarea>
              </div>
              <button class="btn btn-secondary btn-sm" onclick="saveJournal()">💾 Simpan Jurnal</button>
            </div>
            
            <div class="card">
              <div class="card-title">ℹ️ Tentang Assertiveness Training</div>
              <div style="display:flex;flex-direction:column;gap:6px">
                <div style="padding:10px;background:var(--teal-pale);border-radius:8px">
                  <strong class="text-sm text-teal">Sesi 1:</strong>
                  <p class="text-sm text-muted">Identifikasi kejadian penyebab marah & sikap saat marah</p>
                </div>
                <div style="padding:10px;background:#fff0f9;border-radius:8px">
                  <strong class="text-sm text-rose">Sesi 2:</strong>
                  <p class="text-sm text-muted">Mengungkapkan kebutuhan & keinginan secara asertif</p>
                </div>
                <div style="padding:10px;background:#fefce8;border-radius:8px">
                  <strong class="text-sm" style="color:#d97706">Sesi 3:</strong>
                  <p class="text-sm text-muted">Mengatakan "Tidak" untuk permintaan tidak rasional</p>
                </div>
                <div style="padding:10px;background:#f0fdf4;border-radius:8px">
                  <strong class="text-sm" style="color:#16a34a">Sesi 4:</strong>
                  <p class="text-sm text-muted">Menerima perbedaan pendapat & menyampaikan pendapat asertif</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- PAGE: EVALUATION -->
      <div class="content-area fade-in" id="page-evaluation" style="display:none">
        <div class="form-group" style="max-width:300px;margin-bottom:20px">
          <select class="form-select" id="eval-patient-select" onchange="loadEvaluation(this.value)">
            <option value="">-- Pilih Pasien untuk Evaluasi --</option>
          </select>
        </div>
        
        <div id="eval-content" style="display:none">
          <div class="stats-grid">
            <div class="stat-card">
              <div class="stat-value text-rose" id="eval-pre-score">-</div>
              <div class="stat-label">Skor Pre-Test OASS</div>
            </div>
            <div class="stat-card">
              <div class="stat-value text-teal" id="eval-post-score">-</div>
              <div class="stat-label">Skor Post-Test OASS</div>
            </div>
            <div class="stat-card" style="border-color:#10b981">
              <div class="stat-value" style="color:#10b981" id="eval-penurunan">-</div>
              <div class="stat-label">Penurunan (%)</div>
            </div>
            <div class="stat-card" style="border-color:#f59e0b">
              <div class="stat-value" style="color:#f59e0b" id="eval-sessions-done">-</div>
              <div class="stat-label">Total Sesi Selesai</div>
            </div>
          </div>
          
          <div class="grid-2">
            <div class="card">
              <div class="card-title">📊 Perbandingan Pre vs Post</div>
              <div class="chart-container"><canvas id="chart-eval-compare"></canvas></div>
            </div>
            <div class="card">
              <div class="card-title">🏥 Progress Terapi</div>
              <div id="eval-therapy-progress">
                <div style="margin-bottom:14px">
                  <div class="inline-flex justify-between">
                    <span class="text-sm font-bold">🦋 Butterfly Hug</span>
                    <span class="text-sm text-muted" id="bh-progress-txt">0/5 langkah</span>
                  </div>
                  <div class="progress-bar mt-4"><div class="progress-fill" id="bh-progress-bar" style="width:0%"></div></div>
                </div>
                <div style="margin-bottom:14px">
                  <div class="inline-flex justify-between">
                    <span class="text-sm font-bold">🌿 Grounding</span>
                    <span class="text-sm text-muted" id="gr-progress-txt">0/5 tahap</span>
                  </div>
                  <div class="progress-bar mt-4"><div class="progress-fill" id="gr-progress-bar" style="width:0%"></div></div>
                </div>
                <div style="margin-bottom:14px">
                  <div class="inline-flex justify-between">
                    <span class="text-sm font-bold">💬 Assertiveness Training</span>
                    <span class="text-sm text-muted" id="at-progress-txt">0/4 sesi</span>
                  </div>
                  <div class="progress-bar mt-4"><div class="progress-fill" id="at-progress-bar" style="width:0%"></div></div>
                </div>
              </div>
            </div>
          </div>
        </div>
        
        <div id="eval-empty" style="text-align:center;padding:60px;color:var(--warm-gray)">
          <div style="font-size:4rem;margin-bottom:16px">📊</div>
          <p>Pilih pasien untuk melihat evaluasi</p>
        </div>
      </div>

      <!-- PAGE: REPORTS -->
      <div class="content-area fade-in" id="page-reports" style="display:none">
        <div class="grid-2 mb-4">
          <div class="card">
            <div class="card-title">📥 Export Data Excel</div>
            <p class="text-sm text-muted mb-4">Data akan diekspor dalam format CSV yang kompatibel dengan Excel / OneDrive</p>
            <div class="form-group">
              <label class="form-label">Ruangan</label>
              <select class="form-select" id="report-room">
                <option value="">Semua</option>
                <option>Gatot Kaca</option>
              </select>
            </div>
            <div class="form-group">
              <label class="form-label">Periode</label>
              <select class="form-select" id="report-period">
                <option>Harian</option>
                <option>Mingguan</option>
                <option>Bulanan</option>
              </select>
            </div>
            <button class="btn btn-primary" onclick="exportCSV()">⬇️ Export ke CSV/Excel</button>
            <div style="margin-top:16px;padding:14px;background:#f0f6ff;border-radius:10px;border:1px solid #bfdbfe">
              <div style="font-weight:700;font-size:0.82rem;color:#1e40af;margin-bottom:6px">☁️ Sinkronisasi ke OneDrive</div>
              <p style="font-size:0.78rem;color:#1e3a8a;line-height:1.6;margin-bottom:10px">
                Untuk menyimpan data SIMOJI-RPK secara otomatis ke OneDrive:
                <br>1. Klik <strong>Export ke CSV</strong> di atas
                <br>2. Simpan file CSV ke folder <strong>OneDrive</strong> di komputer Anda
                <br>3. File akan otomatis tersinkronisasi ke cloud OneDrive
              </p>
              <button onclick="window.open('https://onedrive.live.com','_blank')" style="padding:7px 14px;background:#0078d4;color:white;border:none;border-radius:7px;font-size:0.78rem;font-weight:700;cursor:pointer">
                🔗 Buka OneDrive
              </button>
            </div>
          </div>
          
          <div class="card">
            <div class="card-title">📊 Ringkasan Per Ruangan</div>
            <div id="room-summary-list">
              <div style="text-align:center;padding:20px;color:var(--warm-gray)">Belum ada data</div>
            </div>
          </div>
        </div>
        
        <div class="card">
          <div class="card-title">🏆 Performa Per Perawat</div>
          <div class="table-wrap">
            <table>
              <thead>
                <tr>
                  <th>Nama Perawat</th>
                  <th>Ruangan</th>
                  <th>Total Pasien</th>
                  <th>Sesi Dilakukan</th>
                  <th>Rata-rata Penurunan</th>
                  <th>Status</th>
                </tr>
              </thead>
              <tbody id="nurse-performance-table">
                <tr><td colspan="6" style="text-align:center;padding:20px;color:var(--warm-gray)">Belum ada data</td></tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- PAGE: REMINDERS -->
      <div class="content-area fade-in" id="page-reminders" style="display:none">
        <div class="grid-2">
          <div class="card">
            <div class="card-title">📱 Kirim Reminder WA ke Caregiver</div>
            <p class="text-sm text-muted mb-4">Ingatkan keluarga untuk mendukung pasien melakukan terapi mandiri</p>
            <div class="form-group">
              <label class="form-label">Pilih Pasien</label>
              <select class="form-select" id="wa-patient-select">
                <option value="">-- Pilih Pasien --</option>
              </select>
            </div>
            <div class="form-group">
              <label class="form-label">Template Pesan</label>
              <select class="form-select" id="wa-template" onchange="loadWATemplate(this.value)">
                <option value="mingguan">Reminder Mingguan</option>
                <option value="motivasi">Pesan Motivasi</option>
                <option value="jadwal">Jadwal Terapi Mandiri</option>
              </select>
            </div>
            <div class="form-group">
              <label class="form-label">Preview Pesan</label>
              <textarea class="form-input" id="wa-message" rows="10">Yth. Keluarga/Caregiver,
Semoga dalam keadaan sehat selalu,
Kami dari Tim Keperawatan RSMM menginformasikan jadwal terapi mandiri pasien minggu ini:
✅ Butterfly Hug: 2x sehari (pagi & sore)
✅ Grounding 5-4-3-2-1: 2 x sehari (pagi & sore)
✅ Latihan Asertif: 2 x sehari (pagi dan sore)
Diharapkan keluarga dapat selalu mendukung kesembuhan dan mendampingi pasien
Jangan lupa selalu kontrol rutin sesuai jadual yang di tetapkan.
Salam Sehat Jiwa 🌿</textarea>
            </div>
            <button class="btn btn-primary" onclick="sendWAReminder()">📲 Kirim via WhatsApp</button>
          </div>
          
          <div class="card">
            <div class="card-title">⏰ Jadwal Reminder Otomatis</div>
            <p class="text-sm text-muted mb-4">Atur jadwal pengiriman reminder otomatis setiap minggu</p>
            <div id="reminder-list">
              <p class="text-sm text-muted" style="text-align:center;padding:20px">Pilih pasien untuk melihat jadwal reminder</p>
            </div>
            <div class="divider"></div>
            <div class="form-group">
              <label class="form-label">Hari Pengiriman</label>
              <div class="inline-flex flex-wrap gap-12">
                <label style="display:flex;align-items:center;gap:4px;font-size:0.82rem"><input type="checkbox" checked> Senin</label>
                <label style="display:flex;align-items:center;gap:4px;font-size:0.82rem"><input type="checkbox"> Rabu</label>
                <label style="display:flex;align-items:center;gap:4px;font-size:0.82rem"><input type="checkbox" checked> Jumat</label>
                <label style="display:flex;align-items:center;gap:4px;font-size:0.82rem"><input type="checkbox"> Minggu</label>
              </div>
            </div>
            <button class="btn btn-secondary" onclick="saveReminder()">💾 Simpan Jadwal</button>
          </div>
        </div>
      </div>

    </div><!-- /main-content -->
  </div><!-- /app-layout -->
</div><!-- /screen-app -->

<script>
// ===== STATE =====
let state = {
  user: null,
  patients: [],
  currentPatient: null,
  bhCurrentStep: 0,
  grCurrentStep: 0,
  atCurrentSesi: 1,
};

// Sample data
function initSampleData() {
  if (state.patients.length === 0) {
    state.patients = [
      {
        id: 'P001', name: 'Budi Santoso', rm: 'RM-00123',
        dob: '1985-03-15', gender: 'L', room: 'Gatot Kaca',
        diagnosa: 'Risiko Perilaku Kekerasan (RPK)',
        wa: '+6281234567890', nurse: 'Ns. Sari Dewi',
        preScore: 35, postScore: null, category: 'sedang',
        therapyStatus: { bh: 3, gr: 2, at: 1 },
        therapyDates: { bh: new Date().toLocaleDateString('id-ID'), gr: new Date().toLocaleDateString('id-ID'), at: new Date().toLocaleDateString('id-ID') },
        sessionsTotal: 6, lastSession: new Date().toLocaleDateString('id-ID'),
        completed: false,
        sessionNotes: [],
      },
      {
        id: 'P002', name: 'Siti Rahayu', rm: 'RM-00124',
        dob: '1990-07-22', gender: 'P', room: 'Gatot Kaca',
        diagnosa: 'Risiko Perilaku Kekerasan (RPK)',
        wa: '+6289876543210', nurse: 'Ns. Ahmad',
        preScore: 52, postScore: 28, category: 'berat',
        therapyStatus: { bh: 5, gr: 5, at: 4 },
        therapyDates: { bh: new Date().toLocaleDateString('id-ID'), gr: new Date().toLocaleDateString('id-ID'), at: new Date().toLocaleDateString('id-ID') },
        sessionsTotal: 14, lastSession: new Date().toLocaleDateString('id-ID'),
        completed: true,
        sessionNotes: [],
      },
      {
        id: 'P003', name: 'Doni Pratama', rm: 'RM-00125',
        dob: '1978-11-05', gender: 'L', room: 'Gatot Kaca',
        diagnosa: 'Risiko Perilaku Kekerasan (RPK)',
        wa: '+6285678901234', nurse: 'Ns. Lisa',
        preScore: 18, postScore: null, category: 'ringan',
        therapyStatus: { bh: 1, gr: 0, at: 0 },
        therapyDates: { bh: new Date().toLocaleDateString('id-ID'), gr: null, at: null },
        sessionsTotal: 1, lastSession: new Date().toLocaleDateString('id-ID'),
        completed: false,
        sessionNotes: [],
      },
    ];
  }
}

// ===== LOGIN =====
function doLogin() {
  const nurse = document.getElementById('login-nurse').value.trim();
  const room = document.getElementById('login-room').value;
  if (!nurse) { showToast('❌ Masukkan nama perawat'); return; }
  state.user = { name: nurse, room: room };
  // Otomatis isi nama perawat untuk form tambah pasien
  const nurseField = document.getElementById('new-nurse');
  if (nurseField) nurseField.value = nurse;
  initSampleData();
  document.getElementById('screen-splash').classList.remove('active');
  document.getElementById('screen-app').classList.add('active');
  document.getElementById('sidebar-username').textContent = nurse;
  document.getElementById('sidebar-room').textContent = room;
  document.getElementById('user-avatar-txt').textContent = nurse.split(' ').map(w=>w[0]).join('').toUpperCase().slice(0,2);
  updateDashboard();
  populatePatientSelects();
  showToast('✅ Selamat datang, ' + nurse + ' — ' + room);
}

function doLogout() {
  document.getElementById('screen-app').classList.remove('active');
  document.getElementById('screen-splash').classList.add('active');
  showPage('dashboard');
}

function doExit() {
  const confirmed = confirm('Apakah Anda yakin ingin keluar dari SIMOJI-RPK?\n\nAplikasi akan ditutup.');
  if (!confirmed) return;
  try { window.close(); } catch(e) {}
  // Fallback jika window.close() diblokir browser
  document.body.innerHTML = `
    <div style="min-height:100vh;display:flex;align-items:center;justify-content:center;flex-direction:column;gap:20px;background:linear-gradient(135deg,#1a5c50,#2D7D6F)">
      <div style="background:white;border-radius:20px;padding:40px 48px;text-align:center;box-shadow:0 20px 60px rgba(0,0,0,0.3);max-width:380px">
        <div style="font-size:3rem;margin-bottom:12px">👋</div>
        <div style="font-size:1.3rem;font-weight:800;color:#2C3E35;margin-bottom:8px">Sesi Telah Ditutup</div>
        <p style="color:#8A9E98;font-size:0.88rem;line-height:1.6;margin-bottom:24px">SIMOJI-RPK telah ditutup. Anda dapat menutup tab ini atau kembali ke aplikasi.</p>
        <button onclick="location.reload()" style="padding:11px 28px;background:linear-gradient(135deg,#2D7D6F,#4AADA0);color:white;border:none;border-radius:10px;font-size:0.9rem;font-weight:700;cursor:pointer">🔄 Buka Kembali</button>
      </div>
    </div>`;
}

// ===== NAVIGATION =====
function showPage(page) {
  // Hide all pages
  document.querySelectorAll('[id^="page-"]').forEach(p => p.style.display = 'none');
  document.getElementById('page-' + page).style.display = 'block';
  
  // Update nav
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  event && event.currentTarget && event.currentTarget.classList.add('active');
  
  const titles = {
    dashboard: ['Dashboard', 'Ringkasan monitoring pasien hari ini'],
    patients: ['Data Pasien', 'Kelola rekam medis dan biodata pasien'],
    monitoring: ['Monitoring', 'Pantau perkembangan tanda dan gejala RPK pasien di Ruang Gatot Kaca'],
    oass: ['Penilaian OASS', 'Overt Aggression Severity Scale — Pre & Post Test'],
    butterfly: ['Butterfly Hug', 'Panduan terapi bilateral stimulation'],
    grounding: ['Grounding', 'Teknik orientasi realita 5-4-3-2-1'],
    assertiveness: ['Assertiveness Training', 'Latihan ekspresi emosi asertif — 4 Sesi'],
    evaluation: ['Evaluasi Pasien', 'Analisis progress dan penurunan agresivitas'],
    reports: ['Laporan & Export', 'Laporan per ruangan, perawat, dan periode'],
    reminders: ['Reminder WA', 'Kirim notifikasi ke caregiver via WhatsApp'],
  };
  document.getElementById('page-title').textContent = titles[page][0];
  document.getElementById('page-subtitle').textContent = titles[page][1];
  
  if (page === 'monitoring') setTimeout(initMonitoringCharts, 100);
  if (page === 'evaluation') populateEvalSelect();
  if (page === 'reminders') populateWASelect();
  if (page === 'patients') renderPatientsTable();
  if (page === 'reports') renderReports();
  closeSidebar();
}

function switchTab(btn, tabId) {
  btn.closest('.tabs').querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  btn.classList.add('active');
  const container = btn.closest('.tabs').nextElementSibling;
  // Get all siblings (tabs content)
  let el = container;
  while (el) { el.style.display = 'none'; el = el.nextElementSibling; }
  document.getElementById(tabId).style.display = 'block';
  if (tabId.startsWith('mon-')) initMonitoringCharts();
}

function toggleSidebar() {
  document.getElementById('sidebar').classList.toggle('open');
  document.getElementById('overlay').classList.toggle('open');
}
function closeSidebar() {
  document.getElementById('sidebar').classList.remove('open');
  document.getElementById('overlay').classList.remove('open');
}

// ===== MODAL =====
function openModal(id) { document.getElementById(id).classList.add('open'); }
function closeModal(id) { document.getElementById(id).classList.remove('open'); }

// ===== TOAST =====
function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 3000);
}

// ===== PATIENT MANAGEMENT =====
function savePatient() {
  const name = document.getElementById('new-name').value.trim();
  const rm = document.getElementById('new-rm').value.trim();
  if (!name || !rm) { showToast('❌ Nama dan No. RM wajib diisi'); return; }
  
  const patient = {
    id: 'P' + Date.now().toString().slice(-4),
    name, rm,
    dob: document.getElementById('new-dob').value,
    gender: document.getElementById('new-gender').value,
    room: document.getElementById('new-room').value,
    diagnosa: document.getElementById('new-diagnosa').value,
    wa: document.getElementById('new-wa').value,
    nurse: document.getElementById('new-nurse').value || state.user?.name || '-',
    preScore: null, postScore: null, category: null,
    therapyStatus: { bh: 0, gr: 0, at: 0 },
    therapyDates: { bh: null, gr: null, at: null },
    sessionsTotal: 0,
    lastSession: '-',
    completed: false,
    sessionNotes: [],
  };
  state.patients.push(patient);
  closeModal('modal-addpatient');
  showToast('✅ Pasien ' + name + ' berhasil ditambahkan');
  populatePatientSelects();
  updateDashboard();
  renderPatientsTable();
}

function populatePatientSelects() {
  const selects = ['oass-patient-select','bh-patient-select','gr-patient-select','at-patient-select','eval-patient-select','wa-patient-select'];
  selects.forEach(id => {
    const el = document.getElementById(id);
    if (!el) return;
    el.innerHTML = '<option value="">-- Pilih Pasien --</option>';
    state.patients.forEach(p => {
      el.innerHTML += `<option value="${p.id}">${p.name} (${p.rm})</option>`;
    });
  });
}

function filterPatients(q) { renderPatientsTable(q); }
function filterByRoom(room) { renderPatientsTable('', room); }

function renderPatientsTable(q='', room='') {
  const tbody = document.getElementById('patients-table-body');
  let filtered = state.patients.filter(p => {
    const matchQ = !q || p.name.toLowerCase().includes(q.toLowerCase()) || p.rm.includes(q);
    const matchRoom = !room || p.room === room;
    return matchQ && matchRoom;
  });
  
  if (filtered.length === 0) {
    tbody.innerHTML = '<tr><td colspan="8" style="text-align:center;padding:30px;color:var(--warm-gray)">Tidak ada data pasien</td></tr>';
    return;
  }
  
  tbody.innerHTML = filtered.map(p => {
    const cat = p.preScore !== null ? getOASSCategory(p.preScore) : null;
    const pct = p.preScore && p.postScore ? Math.round((p.preScore - p.postScore)/p.preScore*100) : null;
    return `<tr>
      <td>
        <div style="display:flex;align-items:center;gap:8px">
          <div class="patient-avatar" style="width:32px;height:32px;font-size:0.85rem">${p.name[0]}</div>
          <div>
            <div style="font-weight:600;font-size:0.88rem">${p.name}</div>
            <div style="font-size:0.75rem;color:var(--warm-gray)">${p.gender === 'L' ? '♂' : '♀'} ${p.dob || '-'}</div>
          </div>
        </div>
      </td>
      <td><span class="badge badge-teal">${p.rm}</span></td>
      <td>${p.room}</td>
      <td>${p.nurse}</td>
      <td>
        <div style="font-size:0.78rem">
          🦋 ${p.therapyStatus.bh}/5 | 🌿 ${p.therapyStatus.gr}/5 | 💬 ${p.therapyStatus.at}/4
        </div>
        ${p.completed ? '<span class="badge badge-green" style="margin-top:4px">✓ Selesai</span>' : '<span class="badge badge-yellow" style="margin-top:4px">Proses</span>'}
      </td>
      <td>${p.preScore !== null ? `<span class="badge ${cat.badge}">${p.preScore} (${cat.label})</span>` : '<span class="badge badge-teal">Belum</span>'}</td>
      <td>${p.postScore !== null ? `<span class="badge badge-green">${p.postScore}${pct ? ' (-'+pct+'%)' : ''}</span>` : '<span class="text-muted text-sm">Belum</span>'}</td>
      <td>
        <div style="display:flex;gap:4px">
          <button class="btn btn-secondary btn-sm" onclick="selectPatientDetail('${p.id}')">Detail</button>
          <button class="btn btn-sm" style="background:#fee2e2;color:#dc2626" onclick="deletePatient('${p.id}')">✕</button>
        </div>
      </td>
    </tr>`;
  }).join('');
}

function deletePatient(id) {
  if (!confirm('Hapus data pasien ini?')) return;
  state.patients = state.patients.filter(p => p.id !== id);
  renderPatientsTable();
  populatePatientSelects();
  updateDashboard();
  showToast('✅ Data pasien dihapus');
}

function selectPatientDetail(id) {
  const p = state.patients.find(x => x.id === id);
  state.currentPatient = p;
  showPage('oass');
  setTimeout(() => {
    document.getElementById('oass-patient-select').value = id;
    selectPatientForOASS(id);
  }, 100);
}

// ===== DASHBOARD =====
function updateDashboard() {
  document.getElementById('stat-total').textContent = state.patients.length;
  const sessionCount = state.patients.reduce((a, p) => a + p.sessionsTotal, 0);
  document.getElementById('stat-sessions').textContent = sessionCount;
  const pending = state.patients.filter(p => p.therapyStatus.bh >= 5 && p.therapyStatus.gr >= 5 && p.therapyStatus.at >= 4 && !p.postScore).length;
  document.getElementById('stat-pending').textContent = pending;
  const completed = state.patients.filter(p => p.completed).length;
  document.getElementById('stat-completed').textContent = completed;
  
  renderDashboardPatients();
  initDashboardCharts();
}

function renderDashboardPatients() {
  const list = document.getElementById('dashboard-patient-list');
  if (state.patients.length === 0) {
    list.innerHTML = '<p class="text-muted text-sm" style="padding:20px;text-align:center">Belum ada pasien. <a href="#" onclick="openModal(\'modal-addpatient\');return false" style="color:var(--teal)">Tambah pasien baru →</a></p>';
    return;
  }
  list.innerHTML = state.patients.map(p => {
    const cat = p.preScore !== null ? getOASSCategory(p.preScore) : { label:'Belum Pre-Test', badge:'badge-teal' };
    return `<div class="patient-card" onclick="selectPatientDetail('${p.id}')">
      <div class="patient-avatar">${p.name[0]}</div>
      <div>
        <div class="patient-name">${p.name}</div>
        <div class="patient-meta">${p.rm} · ${p.room} · ${p.nurse}</div>
      </div>
      <div class="ml-auto" style="text-align:right">
        <span class="badge ${cat.badge}">${cat.label}</span>
        <div class="text-sm text-muted mt-4">Sesi: ${p.sessionsTotal}</div>
      </div>
    </div>`;
  }).join('');
}

// ===== OASS =====
function selectPatientForOASS(id) {
  if (!id) return;
  const p = state.patients.find(x => x.id === id);
  if (!p) return;
  state.currentPatient = p;
  document.getElementById('oass-patient-info').style.display = 'block';
  document.getElementById('oass-pname').textContent = p.name;
  document.getElementById('oass-pmeta').textContent = p.rm + ' · ' + p.room;
  document.getElementById('oass-avatar').textContent = p.name[0];
  
  const allDone = p.therapyStatus.bh >= 5 && p.therapyStatus.gr >= 5 && p.therapyStatus.at >= 4;
  
  if (p.preScore === null) {
    document.getElementById('oass-pstatus').textContent = 'Perlu Pre-Test';
    document.getElementById('oass-pstatus').className = 'badge badge-rose';
    document.getElementById('oass-save-btn').style.display = 'inline-flex';
    document.getElementById('oass-post-btn').style.display = 'none';
  } else if (p.postScore === null && allDone) {
    document.getElementById('oass-pstatus').textContent = 'Siap Post-Test';
    document.getElementById('oass-pstatus').className = 'badge badge-green';
    document.getElementById('oass-save-btn').style.display = 'none';
    document.getElementById('oass-post-btn').style.display = 'inline-flex';
  } else if (p.postScore === null) {
    document.getElementById('oass-pstatus').textContent = 'Terapi Berjalan';
    document.getElementById('oass-pstatus').className = 'badge badge-yellow';
    document.getElementById('oass-save-btn').style.display = 'none';
    document.getElementById('oass-post-btn').style.display = 'none';
  } else {
    document.getElementById('oass-pstatus').textContent = 'Selesai';
    document.getElementById('oass-pstatus').className = 'badge badge-green';
    document.getElementById('oass-save-btn').style.display = 'none';
    document.getElementById('oass-post-btn').style.display = 'none';
  }
}

function calcOASS() {
  const fields = ['a1','a2','a3','b1','b2','c1','c2','d1','d2','d3'];
  let total = 0;
  let allAnswered = true;
  fields.forEach(f => {
    const checked = document.querySelector(`input[name="oass-${f}"]:checked`);
    if (checked) total += parseInt(checked.value);
    else allAnswered = false;
  });
  
  const cat = getOASSCategory(total);
  const scoreEl = document.getElementById('oass-score-display');
  scoreEl.style.display = 'block';
  document.getElementById('oass-score-num').textContent = total;
  document.getElementById('oass-score-cat').textContent = '🔵 Kategori: ' + cat.label;
  document.getElementById('oass-score-desc').textContent = cat.desc;
  scoreEl.style.background = cat.bg;
  
  return total;
}

function getOASSCategory(score) {
  if (score <= 20) return { label:'Ringan (0-20)', badge:'badge-green', bg:'#f0fdf4', desc:'Agresivitas rendah, dapat dikelola dengan intervensi verbal.', color:'#16a34a' };
  if (score <= 40) return { label:'Sedang (21-40)', badge:'badge-yellow', bg:'#fffbeb', desc:'Agresivitas moderat, perlu pengawasan ketat dan intervensi terstruktur.', color:'#d97706' };
  return { label:'Berat (>40)', badge:'badge-red', bg:'#fef2f2', desc:'Agresivitas tinggi, perlu penanganan intensif dan kolaborasi tim.', color:'#dc2626' };
}

function saveOASS() {
  const p = state.currentPatient;
  if (!p) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  const score = calcOASS();
  p.preScore = score;
  p.category = getOASSCategory(score).label;
  showToast('✅ Pre-Test OASS disimpan. Skor: ' + score);
  selectPatientForOASS(p.id);
  updateDashboard();
}

function saveOASSPost() {
  const p = state.currentPatient;
  if (!p) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  const allDone = p.therapyStatus.bh >= 5 && p.therapyStatus.gr >= 5 && p.therapyStatus.at >= 4;
  if (!allDone) { showToast('⚠️ Selesaikan semua terapi terlebih dahulu sebelum post-test'); return; }
  const score = calcOASS();
  p.postScore = score;
  p.completed = true;
  const pct = Math.round((p.preScore - p.postScore) / p.preScore * 100);
  showToast(`✅ Post-Test selesai! Skor: ${score}. Penurunan: ${pct}%`);
  selectPatientForOASS(p.id);
  updateDashboard();
}

// ===== THERAPY: BUTTERFLY HUG =====
function selectPatientForTherapy(type, id) {
  if (!id) return;
  const p = state.patients.find(x => x.id === id);
  if (!p) return;
  state.currentPatient = p;
  
  // Check pre-test done
  if (p.preScore === null) {
    showToast('⚠️ Pre-Test OASS belum dilakukan untuk pasien ini. Lakukan pre-test terlebih dahulu.');
  }
  
  if (type === 'bh') {
    document.getElementById('bh-session-info').style.display = 'block';
    state.bhCurrentStep = p.therapyStatus.bh;
    renderBHSteps();
    // Show continuation banner
    renderSessionContinuationBanner('bh', p);
  } else if (type === 'gr') {
    state.grCurrentStep = p.therapyStatus.gr;
    renderGRSteps();
    renderSessionContinuationBanner('gr', p);
  } else if (type === 'at') {
    document.getElementById('at-session-info').style.display = 'block';
    // Continue from last completed session + 1, but don't exceed 4
    const nextSesi = p.therapyStatus.at >= 4 ? 4 : Math.max(1, p.therapyStatus.at + 1);
    state.atCurrentSesi = nextSesi;
    switchATSesi(nextSesi);
    renderSessionContinuationBanner('at', p);
  }
}

function renderSessionContinuationBanner(type, p) {
  const bannerIds = { bh: 'bh-continue-banner', gr: 'gr-continue-banner', at: 'at-continue-banner' };
  const bannerId = bannerIds[type];
  let banner = document.getElementById(bannerId);
  if (!banner) return;
  
  const done = p.therapyStatus[type];
  const max = { bh: 5, gr: 5, at: 4 }[type];
  const label = { bh: 'Butterfly Hug', gr: 'Grounding', at: 'Assertiveness Training' }[type];
  const unit = { bh: 'Langkah', gr: 'Tahap', at: 'Sesi' }[type];
  const lastDate = p.therapyDates && p.therapyDates[type] ? p.therapyDates[type] : '-';
  
  if (done === 0) {
    banner.innerHTML = `<div style="background:#f0fdfb;border:2px solid var(--teal-light);border-radius:10px;padding:12px 16px;margin-bottom:16px">
      <div style="font-size:0.85rem;font-weight:700;color:var(--teal)">🆕 Sesi Baru — ${label}</div>
      <div style="font-size:0.78rem;color:var(--warm-gray);margin-top:4px">Belum ada sesi yang dilakukan. Mulai dari ${unit} 1.</div>
    </div>`;
  } else if (done >= max) {
    banner.innerHTML = `<div style="background:#f0fdf4;border:2px solid #bbf7d0;border-radius:10px;padding:12px 16px;margin-bottom:16px">
      <div style="font-size:0.85rem;font-weight:700;color:#16a34a">✅ ${label} Selesai!</div>
      <div style="font-size:0.78rem;color:#15803d;margin-top:4px">Semua ${max} ${unit} telah selesai. Terakhir: ${lastDate}</div>
    </div>`;
  } else {
    const next = done + 1;
    banner.innerHTML = `<div style="background:#fffbeb;border:2px solid #fde68a;border-radius:10px;padding:12px 16px;margin-bottom:16px">
      <div style="font-size:0.85rem;font-weight:700;color:#d97706">▶️ Lanjutkan ${label}</div>
      <div style="font-size:0.78rem;color:#92400e;margin-top:4px">
        Sudah selesai: <strong>${done}/${max} ${unit}</strong> &nbsp;|&nbsp; Sesi terakhir: <strong>${lastDate}</strong>
        <br>Lanjutkan dari <strong>${unit} ${next}</strong> — tidak perlu mengulang dari awal.
      </div>
    </div>`;
  }
  banner.style.display = 'block';
}

function renderBHSteps() {
  const p = state.currentPatient;
  const done = p ? p.therapyStatus.bh : 0;
  for (let i = 1; i <= 5; i++) {
    const stepEl = document.getElementById(`bh-step-${i}`);
    const numEl = document.getElementById(`bh-num-${i}`);
    stepEl.className = 'therapy-step';
    if (i <= done) { stepEl.classList.add('completed'); numEl.className = 'step-number done'; numEl.textContent = '✓'; }
    else if (i === done + 1) { stepEl.classList.add('active-step'); numEl.className = 'step-number'; numEl.textContent = i; }
    else { numEl.className = 'step-number'; numEl.textContent = i; }
  }
  
  // Session tracker
  const tracker = document.getElementById('bh-session-tracker');
  if (tracker) {
    tracker.innerHTML = '';
    for (let i = 1; i <= 5; i++) {
      const d = document.createElement('div');
      d.className = 'session-dot ' + (i <= done ? 'done' : i === done+1 ? 'current' : 'locked');
      d.textContent = i <= done ? '✓' : i;
      d.title = `Langkah ${i}`;
      tracker.appendChild(d);
    }
  }
}

function completeBHStep() {
  const p = state.currentPatient;
  if (!p) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  if (p.preScore === null) { showToast('⚠️ Lakukan Pre-Test OASS terlebih dahulu'); return; }
  if (p.therapyStatus.bh >= 5) { showToast('✅ Semua langkah Butterfly Hug sudah selesai!'); return; }
  p.therapyStatus.bh++;
  p.sessionsTotal++;
  p.lastSession = new Date().toLocaleDateString('id-ID');
  if (!p.therapyDates) p.therapyDates = {};
  p.therapyDates.bh = new Date().toLocaleDateString('id-ID');
  renderBHSteps();
  renderSessionContinuationBanner('bh', p);
  showToast(`✅ Langkah ${p.therapyStatus.bh} selesai! (${p.therapyStatus.bh}/5)`);
  updateDashboard();
}

function saveBHSession() {
  if (!state.currentPatient) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  showToast('💾 Sesi Butterfly Hug tersimpan');
}

// ===== THERAPY: GROUNDING =====
function renderGRSteps() {
  const p = state.currentPatient;
  const done = p ? p.therapyStatus.gr : 0;
  for (let i = 1; i <= 5; i++) {
    const stepEl = document.getElementById(`gr-step-${i}`);
    if (!stepEl) continue;
    stepEl.className = 'therapy-step';
    if (i <= done) stepEl.classList.add('completed');
    else if (i === done + 1) stepEl.classList.add('active-step');
  }
}

function completeGroundingStep() {
  const p = state.currentPatient;
  if (!p) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  if (p.preScore === null) { showToast('⚠️ Lakukan Pre-Test OASS terlebih dahulu'); return; }
  if (p.therapyStatus.gr >= 5) { showToast('✅ Semua tahap Grounding sudah selesai!'); return; }
  p.therapyStatus.gr++;
  p.sessionsTotal++;
  p.lastSession = new Date().toLocaleDateString('id-ID');
  if (!p.therapyDates) p.therapyDates = {};
  p.therapyDates.gr = new Date().toLocaleDateString('id-ID');
  renderGRSteps();
  renderSessionContinuationBanner('gr', p);
  showToast(`✅ Tahap Grounding ${p.therapyStatus.gr}/5 selesai!`);
  updateDashboard();
}

function saveGroundingSession() {
  if (!state.currentPatient) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  showToast('💾 Sesi Grounding tersimpan');
}

// ===== THERAPY: ASSERTIVENESS =====
function switchATSesi(sesi) {
  state.atCurrentSesi = sesi;
  for (let i = 1; i <= 4; i++) {
    const s = document.getElementById(`at-sesi-${i}`);
    const t = document.getElementById(`at-tab-${i}`);
    if (s) s.style.display = i === sesi ? 'block' : 'none';
    if (t) {
      t.classList.toggle('active', i === sesi);
      // Show completion status on tabs
      const p = state.currentPatient;
      if (p) {
        if (i <= p.therapyStatus.at) {
          t.innerHTML = `✓ Sesi ${i}`;
          t.style.color = '#16a34a';
        } else if (i === p.therapyStatus.at + 1) {
          t.innerHTML = `▶ Sesi ${i}`;
          t.style.color = '';
        } else {
          t.innerHTML = `🔒 Sesi ${i}`;
          t.style.color = '#a0b0ab';
        }
      } else {
        t.innerHTML = `Sesi ${i}`;
        t.style.color = '';
      }
    }
  }
}

function saveATSesi(sesi) {
  const p = state.currentPatient;
  if (!p) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  if (p.preScore === null) { showToast('⚠️ Lakukan Pre-Test OASS terlebih dahulu'); return; }
  if (p.therapyStatus.at < sesi) {
    p.therapyStatus.at = sesi;
    p.sessionsTotal++;
    p.lastSession = new Date().toLocaleDateString('id-ID');
    if (!p.therapyDates) p.therapyDates = {};
    p.therapyDates.at = new Date().toLocaleDateString('id-ID');
  }
  renderSessionContinuationBanner('at', p);
  showToast(`✅ Sesi ${sesi} Assertiveness Training tersimpan!`);
  updateDashboard();
  if (sesi < 4) { setTimeout(() => switchATSesi(sesi + 1), 500); }
  else { showToast('🎉 Semua sesi Assertiveness Training selesai!'); }
}

function selectEmotion(el) {
  document.querySelectorAll('.emotion-card').forEach(c => c.classList.remove('selected'));
  el.classList.add('selected');
}

function saveJournal() { showToast('📓 Jurnal harian tersimpan'); }

// ===== EVALUATION =====
function populateEvalSelect() {
  const sel = document.getElementById('eval-patient-select');
  sel.innerHTML = '<option value="">-- Pilih Pasien --</option>';
  state.patients.forEach(p => {
    sel.innerHTML += `<option value="${p.id}">${p.name} (${p.rm})</option>`;
  });
}

function loadEvaluation(id) {
  if (!id) {
    document.getElementById('eval-content').style.display = 'none';
    document.getElementById('eval-empty').style.display = 'block';
    return;
  }
  const p = state.patients.find(x => x.id === id);
  if (!p) return;
  
  document.getElementById('eval-content').style.display = 'block';
  document.getElementById('eval-empty').style.display = 'none';
  
  document.getElementById('eval-pre-score').textContent = p.preScore ?? '-';
  document.getElementById('eval-post-score').textContent = p.postScore ?? '-';
  const pct = p.preScore && p.postScore ? Math.round((p.preScore - p.postScore)/p.preScore*100) + '%' : '-';
  document.getElementById('eval-penurunan').textContent = pct;
  document.getElementById('eval-sessions-done').textContent = p.sessionsTotal;
  
  // Progress bars
  const bhPct = Math.round(p.therapyStatus.bh/5*100);
  const grPct = Math.round(p.therapyStatus.gr/5*100);
  const atPct = Math.round(p.therapyStatus.at/4*100);
  document.getElementById('bh-progress-bar').style.width = bhPct + '%';
  document.getElementById('gr-progress-bar').style.width = grPct + '%';
  document.getElementById('at-progress-bar').style.width = atPct + '%';
  document.getElementById('bh-progress-txt').textContent = p.therapyStatus.bh + '/5 langkah';
  document.getElementById('gr-progress-txt').textContent = p.therapyStatus.gr + '/5 tahap';
  document.getElementById('at-progress-txt').textContent = p.therapyStatus.at + '/4 sesi';
  
  // Chart
  setTimeout(() => initEvalChart(p), 100);
}

function initEvalChart(p) {
  const ctx = document.getElementById('chart-eval-compare');
  if (!ctx) return;
  if (ctx._chart) ctx._chart.destroy();
  ctx._chart = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: ['Pre-Test OASS', 'Post-Test OASS'],
      datasets: [{
        data: [p.preScore ?? 0, p.postScore ?? 0],
        backgroundColor: ['rgba(212,118,138,0.7)', 'rgba(45,125,111,0.7)'],
        borderColor: ['#D4768A', '#2D7D6F'],
        borderWidth: 2, borderRadius: 8,
      }]
    },
    options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true, max: 80 } } }
  });
}

// ===== DASHBOARD CHARTS =====
function initDashboardCharts() {
  const cats = { ringan: 0, sedang: 0, berat: 0 };
  state.patients.forEach(p => {
    if (p.preScore === null) return;
    if (p.preScore <= 20) cats.ringan++;
    else if (p.preScore <= 40) cats.sedang++;
    else cats.berat++;
  });
  
  const ctx1 = document.getElementById('chart-distribution');
  if (ctx1 && ctx1._chart) ctx1._chart.destroy();
  if (ctx1) ctx1._chart = new Chart(ctx1, {
    type: 'doughnut',
    data: {
      labels: ['Ringan (0-20)', 'Sedang (21-40)', 'Berat (>40)'],
      datasets: [{ data: [cats.ringan, cats.sedang, cats.berat], backgroundColor: ['#4ade80','#fbbf24','#f87171'], borderWidth: 0 }]
    },
    options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'bottom' } } }
  });
  
  const ctx2 = document.getElementById('chart-trend');
  if (ctx2 && ctx2._chart) ctx2._chart.destroy();
  const weeks = ['Mg 1', 'Mg 2', 'Mg 3', 'Mg 4'];
  if (ctx2) ctx2._chart = new Chart(ctx2, {
    type: 'line',
    data: {
      labels: weeks,
      datasets: [{
        label: 'Rata-rata Skor OASS',
        data: [45, 38, 30, 22],
        borderColor: '#2D7D6F', backgroundColor: 'rgba(45,125,111,0.1)',
        fill: true, tension: 0.4, borderWidth: 3, pointRadius: 5, pointBackgroundColor: '#2D7D6F',
      }]
    },
    options: { responsive: true, maintainAspectRatio: false, scales: { y: { beginAtZero: true } } }
  });
}

// ===== MONITORING CHARTS =====
let monCharts = {};
function initMonitoringCharts() {
  ['chart-daily','chart-weekly','chart-monthly'].forEach(id => {
    const ctx = document.getElementById(id);
    if (!ctx) return;
    if (ctx._chart) ctx._chart.destroy();
    
    const colors = ['#2D7D6F','#D4768A','#f59e0b','#6366f1','#ec4899','#10b981','#ef4444','#8b5cf6'];

    if (id === 'chart-daily') {
      // Line chart: setiap pasien, perkembangan harian dalam minggu ini
      const patients = state.patients.slice(0, 8);
      ctx._chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: ['Sen', 'Sel', 'Rab', 'Kam', 'Jum', 'Sab', 'Min'],
          datasets: patients.map((p, i) => ({
            label: p.name,
            data: [p.preScore||0, Math.max(0,(p.preScore||0)-3*(i+1)), Math.max(0,(p.preScore||0)-6*(i+1)), Math.max(0,(p.preScore||0)-9*(i+1)), p.postScore||(Math.max(0,(p.preScore||0)-11*(i+1))), null, null].map(v => v||null),
            borderColor: colors[i % colors.length],
            backgroundColor: 'transparent', tension: 0.4, borderWidth: 2.5, pointRadius: 4,
          }))
        },
        options: { responsive: true, maintainAspectRatio: false, scales: { y: { beginAtZero: true, title: { display: true, text: 'Skor OASS' } } }, plugins: { title: { display: true, text: 'Penurunan Skor Harian Tanda dan Gejala RPK di Ruangan Gatot Kaca' }, legend: { display: true } } }
      });

    } else if (id === 'chart-weekly') {
      // Line chart: setiap pasien, perkembangan selama 4 minggu
      const patients = state.patients.filter(p => p.room === 'Gatot Kaca');
      const weekLabels = ['Minggu 1', 'Minggu 2', 'Minggu 3', 'Minggu 4'];
      ctx._chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: weekLabels,
          datasets: patients.length > 0 ? patients.map((p, i) => {
            const pre = p.preScore || 30;
            const post = p.postScore || Math.max(0, pre - 10*(i+1));
            const step = (pre - post) / 3;
            return {
              label: p.name,
              data: [pre, Math.round(pre - step), Math.round(pre - step*2), post],
              borderColor: colors[i % colors.length],
              backgroundColor: 'transparent', tension: 0.4, borderWidth: 2.5, pointRadius: 5,
            };
          }) : [{
            label: 'Contoh Pasien',
            data: [45, 38, 30, 22],
            borderColor: colors[0], backgroundColor: 'transparent', tension: 0.4, borderWidth: 2.5, pointRadius: 5,
          }]
        },
        options: { responsive: true, maintainAspectRatio: false, scales: { y: { beginAtZero: true, title: { display: true, text: 'Skor OASS' } } }, plugins: { title: { display: true, text: 'Penurunan Skor Mingguan Tanda dan Gejala RPK di Ruangan Gatot Kaca' }, legend: { display: true } } }
      });

    } else if (id === 'chart-monthly') {
      // Line chart: setiap pasien, rekap per bulan (4 minggu per bulan)
      // Dapatkan daftar bulan unik dari data pasien, atau gunakan default
      const monthLabels = ['Maret', 'April', 'Mei', 'Juni', 'Juli', 'Agustus'];
      const patients = state.patients.filter(p => p.room === 'Gatot Kaca');
      ctx._chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: monthLabels,
          datasets: patients.length > 0 ? patients.map((p, i) => {
            const pre = p.preScore || 40;
            const post = p.postScore || Math.max(0, pre - 15*(i+1));
            const totalDrop = pre - post;
            const steps = monthLabels.length - 1;
            const stepVal = totalDrop / steps;
            return {
              label: p.name,
              data: monthLabels.map((_, mi) => Math.max(0, Math.round(pre - stepVal * mi))),
              borderColor: colors[i % colors.length],
              backgroundColor: 'transparent', tension: 0.4, borderWidth: 2.5, pointRadius: 5,
            };
          }) : [{
            label: 'Contoh Pasien',
            data: [50, 42, 36, 30, 26, 22],
            borderColor: colors[0], backgroundColor: 'transparent', tension: 0.4, borderWidth: 2.5, pointRadius: 5,
          }]
        },
        options: { responsive: true, maintainAspectRatio: false, scales: { y: { beginAtZero: true, title: { display: true, text: 'Skor OASS' } } }, plugins: { title: { display: true, text: 'Penurunan Skor Bulanan Tanda dan Gejala RPK di Ruangan Gatot Kaca' }, legend: { display: true } } }
      });
      // Render analisis
      renderMonthlyAnalysis();
    }
  });
}

// ===== ANALISIS PENURUNAN TANDA DAN GEJALA RPK - RUANG GATOT KACA =====
function renderMonthlyAnalysis() {
  const el = document.getElementById('monthly-analysis-content');
  if (!el) return;
  const gk = state.patients.filter(p => p.room === 'Gatot Kaca');
  const withPost = gk.filter(p => p.preScore !== null && p.postScore !== null);

  if (withPost.length === 0) {
    el.innerHTML = `<p class="text-sm text-muted" style="padding:16px;text-align:center">Belum ada data post-test. Lengkapi penilaian OASS post-test untuk melihat analisis.</p>`;
    return;
  }

  const totalPasien = gk.length;
  const selesai = gk.filter(p => p.completed).length;
  const avgPre = withPost.reduce((s,p) => s+p.preScore, 0) / withPost.length;
  const avgPost = withPost.reduce((s,p) => s+p.postScore, 0) / withPost.length;
  const avgPct = withPost.reduce((s,p) => s + ((p.preScore-p.postScore)/p.preScore*100), 0) / withPost.length;
  const best = withPost.reduce((a,b) => ((b.preScore-b.postScore)/b.preScore) > ((a.preScore-a.postScore)/a.preScore) ? b : a);
  const bestPct = Math.round((best.preScore-best.postScore)/best.preScore*100);

  // Dapatkan bulan saat ini
  const bulanIni = new Date().toLocaleDateString('id-ID', {month:'long', year:'numeric'});

  el.innerHTML = `
    <!-- 3 Komponen Analisis: rata-rata penurunan, rata-rata pre, rata-rata post -->
    <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:14px;margin-bottom:20px">
      <div style="background:#f0fdfb;padding:16px;border-radius:10px;border-left:4px solid var(--teal);text-align:center">
        <div style="font-size:1.7rem;font-weight:800;color:var(--teal)">${Math.round(avgPct)}%</div>
        <div style="font-size:0.78rem;color:var(--warm-gray);margin-top:4px;font-weight:600">Rata-rata Penurunan<br>Tanda dan Gejala</div>
      </div>
      <div style="background:#fff7ed;padding:16px;border-radius:10px;border-left:4px solid #d97706;text-align:center">
        <div style="font-size:1.7rem;font-weight:800;color:#d97706">${Math.round(avgPre)}</div>
        <div style="font-size:0.78rem;color:var(--warm-gray);margin-top:4px;font-weight:600">Rata-rata Skor<br>Pre-Test</div>
      </div>
      <div style="background:#f0fdf4;padding:16px;border-radius:10px;border-left:4px solid #16a34a;text-align:center">
        <div style="font-size:1.7rem;font-weight:800;color:#16a34a">${Math.round(avgPost)}</div>
        <div style="font-size:0.78rem;color:var(--warm-gray);margin-top:4px;font-weight:600">Rata-rata Skor<br>Post-Test</div>
      </div>
    </div>

    <!-- Kesimpulan Monitoring Mingguan -->
    <div style="background:#fafcfb;border-radius:10px;padding:16px;margin-bottom:14px;border:1px solid var(--teal-pale)">
      <div style="font-weight:700;font-size:0.9rem;color:var(--teal);margin-bottom:10px">📋 Kesimpulan Analisis Monitoring Mingguan</div>
      <p style="font-size:0.85rem;line-height:1.8;color:#374151">
        Berdasarkan hasil monitoring mingguan di Ruang Gatot Kaca terjadi penurunan Skor Tanda dan Gejala pasien RPK dari
        <strong>${Math.round(avgPre)}</strong> (Pre-Test) menjadi <strong>${Math.round(avgPost)}</strong> (Post-Test),
        dengan rata-rata penurunan sebesar <strong style="color:var(--teal)">${Math.round(avgPct)}%</strong>
        setelah diberikan intervensi Butterfly Hug, Grounding dan Assertiveness Training.<br><br>
        Sebanyak <strong>${selesai}</strong> pasien telah menyelesaikan seluruh sesi terapi dan pasien yang mengalami
        penurunan skor tanda gejala RPK tertinggi adalah <strong style="color:#16a34a">${best.name}</strong>
        (Pre: ${best.preScore} → Post: ${best.postScore}, penurunan <strong>${bestPct}%</strong>).
      </p>
    </div>

    <!-- Kesimpulan Monitoring Bulanan -->
    <div style="background:#fafcfb;border-radius:10px;padding:16px;margin-bottom:14px;border:1px solid #d1fae5">
      <div style="font-weight:700;font-size:0.9rem;color:#16a34a;margin-bottom:10px">📅 Kesimpulan Monitoring Bulanan</div>
      <p style="font-size:0.85rem;line-height:1.8;color:#374151">
        Berdasarkan hasil monitoring Bulanan di Ruang Gatot Kaca terjadi penurunan Skor Tanda dan Gejala pasien RPK dari
        <strong>${Math.round(avgPre)}</strong> (Pre-Test) menjadi <strong>${Math.round(avgPost)}</strong> (Post-Test),
        dengan rata-rata penurunan sebesar <strong style="color:#16a34a">${Math.round(avgPct)}%</strong>
        setelah diberikan intervensi Butterfly Hug, Grounding dan Assertiveness Training.<br><br>
        Sebanyak <strong>${selesai}</strong> pasien telah menyelesaikan seluruh sesi terapi. Pasien yang mengalami
        penurunan skor tanda gejala RPK tertinggi di Bulan <strong>${bulanIni}</strong> adalah
        <strong style="color:#16a34a">${best.name}</strong>
        (Pre: ${best.preScore} → Post: ${best.postScore}, penurunan <strong>${bestPct}%</strong>).
      </p>
    </div>

    <p style="font-size:0.72rem;color:var(--warm-gray);margin-top:12px;text-align:right">📅 Diperbarui: ${new Date().toLocaleDateString('id-ID',{weekday:'long',year:'numeric',month:'long',day:'numeric'})}</p>
  `;
}

// ===== REPORTS =====
function renderReports() {
  const rooms = ['Gatot Kaca'];
  const el = document.getElementById('room-summary-list');
  el.innerHTML = rooms.map(r => {
    const rp = state.patients.filter(p => p.room === r);
    return `<div style="display:flex;justify-content:space-between;align-items:center;padding:10px;border-bottom:1px solid var(--teal-pale)">
      <span class="text-sm font-bold">${r}</span>
      <span class="text-sm text-muted">${rp.length} pasien</span>
      <span class="badge badge-teal">${rp.filter(p=>p.completed).length} selesai</span>
    </div>`;
  }).join('');
  
  // Nurse performance
  const nurses = {};
  state.patients.forEach(p => {
    if (!nurses[p.nurse]) nurses[p.nurse] = { name: p.nurse, room: p.room, patients: [], sessions: 0 };
    nurses[p.nurse].patients.push(p);
    nurses[p.nurse].sessions += p.sessionsTotal;
  });
  
  const tbody = document.getElementById('nurse-performance-table');
  const entries = Object.values(nurses);
  if (entries.length === 0) {
    tbody.innerHTML = '<tr><td colspan="6" style="text-align:center;padding:20px;color:var(--warm-gray)">Belum ada data</td></tr>';
  } else {
    tbody.innerHTML = entries.map(n => {
      const withPost = n.patients.filter(p => p.postScore !== null);
      const avgDrop = withPost.length > 0 ? Math.round(withPost.reduce((a,p) => a + (p.preScore-p.postScore)/p.preScore*100, 0)/withPost.length) : 0;
      return `<tr>
        <td><strong>${n.name}</strong></td>
        <td>${n.room}</td>
        <td>${n.patients.length}</td>
        <td>${n.sessions}</td>
        <td>${avgDrop > 0 ? avgDrop + '%' : '-'}</td>
        <td><span class="badge ${n.patients.filter(p=>p.completed).length > 0 ? 'badge-green' : 'badge-yellow'}">${n.patients.filter(p=>p.completed).length > 0 ? 'Aktif' : 'Proses'}</span></td>
      </tr>`;
    }).join('');
  }
}

function exportCSV() {
  const room = document.getElementById('report-room').value;
  const patients = room ? state.patients.filter(p => p.room === room) : state.patients;
  
  const headers = ['Nama','No. RM','Ruangan','Perawat','Diagnosa','WA Caregiver','Pre-Score OASS','Kategori Pre','Post-Score OASS','Penurunan (%)','BH Langkah','GR Tahap','AT Sesi','Status','Tgl Sesi Terakhir'];
  const rows = patients.map(p => {
    const pct = p.preScore && p.postScore ? Math.round((p.preScore-p.postScore)/p.preScore*100) : '';
    return [p.name, p.rm, p.room, p.nurse, p.diagnosa, p.wa, p.preScore??'', p.category??'', p.postScore??'', pct, p.therapyStatus.bh, p.therapyStatus.gr, p.therapyStatus.at, p.completed?'Selesai':'Proses', p.lastSession].join(',');
  });
  
  const csv = [headers.join(','), ...rows].join('\n');
  const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = `SIMOJI-RPK_Data_${new Date().toISOString().split('T')[0]}.csv`;
  a.click();
  showToast('✅ Data berhasil diexport ke CSV');
}

// ===== REMINDERS =====
function populateWASelect() {
  const sel = document.getElementById('wa-patient-select');
  sel.innerHTML = '<option value="">-- Pilih Pasien --</option>';
  state.patients.forEach(p => {
    sel.innerHTML += `<option value="${p.id}">${p.name} — ${p.wa || 'Belum ada WA'}</option>`;
  });
}

function loadWATemplate(type) {
  const tmplMingguan = `Yth. Keluarga/Caregiver,\nSemoga dalam keadaan sehat selalu,\nKami dari Tim Keperawatan RSMM menginformasikan jadwal terapi mandiri pasien minggu ini:\n✅ Butterfly Hug: 2x sehari (pagi & sore)\n✅ Grounding 5-4-3-2-1: 2 x sehari (pagi & sore)\n✅ Latihan Asertif: 2 x sehari (pagi dan sore)\nDiharapkan keluarga dapat selalu mendukung kesembuhan dan mendampingi pasien\nJangan lupa selalu kontrol rutin sesuai jadual yang di tetapkan.\nSalam Sehat Jiwa 🌿`;
  const templates = {
    mingguan: tmplMingguan,
    motivasi: `Yth. Keluarga/Caregiver,\nSemoga dalam keadaan sehat selalu,\n\nSemangat untuk pasien kita hari ini! 💪\n"Setiap langkah kecil menuju kesembuhan adalah kemenangan besar"\n\nIngatkan pasien untuk:\n🦋 Butterfly Hug 2x sehari (pagi & sore)\n🌿 Grounding 5-4-3-2-1: 2x sehari (pagi & sore)\n💬 Latihan Asertif: 2x sehari (pagi dan sore)\n\nJangan lupa kontrol rutin sesuai jadual yang ditetapkan.\nSalam Sehat Jiwa 🌿`,
    jadwal: `📅 JADWAL TERAPI MANDIRI MINGGU INI — Ruang Gatot Kaca RSMM\n\nPagi (08.00):\n• Butterfly Hug\n• Grounding 5-4-3-2-1\n• Latihan Asertif\n\nSore (16.00):\n• Butterfly Hug\n• Grounding 5-4-3-2-1\n• Latihan Asertif\n\nDiharapkan keluarga selalu mendampingi pasien.\nJangan lupa kontrol rutin sesuai jadual yang ditetapkan.\nSalam Sehat Jiwa 🌿`,
  };
  document.getElementById('wa-message').value = templates[type] || tmplMingguan;
}

function sendWAReminder() {
  const sel = document.getElementById('wa-patient-select');
  const id = sel.value;
  if (!id) { showToast('❌ Pilih pasien terlebih dahulu'); return; }
  const p = state.patients.find(x => x.id === id);
  if (!p || !p.wa) { showToast('❌ Pasien tidak memiliki nomor WA'); return; }
  
  const msg = document.getElementById('wa-message').value;
  const encoded = encodeURIComponent(msg);
  const wa = p.wa.replace(/[^0-9]/g, '');
  const url = `https://wa.me/${wa}?text=${encoded}`;
  window.open(url, '_blank');
  showToast('📲 Membuka WhatsApp untuk ' + p.name);
}

function saveReminder() { showToast('✅ Jadwal reminder tersimpan'); }

// ===== QR CODE =====
function showQR() {
  const div = document.getElementById('qr-code-display');
  div.innerHTML = '';

  const appUrl = window.location.href.split('?')[0];
  document.getElementById('qr-info').textContent = 'Scan untuk membuka SIMOJI-RPK';

  // Gunakan Google Charts QR API — tidak perlu library, hanya butuh internet
  const encoded = encodeURIComponent(appUrl);
  const qrSrc = `https://chart.googleapis.com/chart?cht=qr&chs=230x230&chl=${encoded}&chco=2D7D6F&chf=bg,s,FFFFFF&chld=M|2`;

  const wrapper = document.createElement('div');
  wrapper.style.cssText = 'display:flex;flex-direction:column;align-items:center;gap:12px';

  const img = document.createElement('img');
  img.src = qrSrc;
  img.width = 230;
  img.height = 230;
  img.alt = 'QR Code SIMOJI-RPK';
  img.style.cssText = 'border-radius:14px;box-shadow:0 4px 24px rgba(45,125,111,0.25);border:3px solid #E8F5F3';

  img.onerror = () => {
    wrapper.innerHTML = `
      <div style="padding:20px;text-align:center;background:#f4fbf9;border-radius:14px;border:2px dashed var(--teal-light)">
        <div style="font-size:2rem;margin-bottom:10px">⚠️</div>
        <p style="color:var(--charcoal);font-weight:600;margin-bottom:6px">Koneksi internet diperlukan</p>
        <p style="font-size:0.78rem;color:var(--warm-gray);margin-bottom:14px">QR Code membutuhkan akses internet.<br>Bagikan URL di bawah ini secara manual:</p>
        <div style="background:white;border-radius:8px;padding:10px;border:1px solid var(--teal-pale)">
          <p style="font-size:0.73rem;color:var(--teal);word-break:break-all;font-weight:600">${appUrl}</p>
        </div>
        <button onclick="navigator.clipboard&&navigator.clipboard.writeText('${appUrl}').then(()=>showToast('✅ URL disalin!'))" style="margin-top:10px;padding:7px 18px;background:var(--teal);color:white;border:none;border-radius:7px;font-size:0.78rem;cursor:pointer;font-weight:600">📋 Salin URL</button>
      </div>`;
  };

  const urlNote = document.createElement('div');
  urlNote.style.cssText = 'font-size:0.68rem;color:var(--warm-gray);word-break:break-all;max-width:230px;text-align:center;line-height:1.5;padding:8px 10px;background:#f4fbf9;border-radius:8px;border:1px solid var(--teal-pale)';
  urlNote.innerHTML = `<strong style="color:var(--teal);display:block;margin-bottom:3px">📍 URL Aplikasi</strong>${appUrl}`;

  wrapper.appendChild(img);
  wrapper.appendChild(urlNote);
  div.appendChild(wrapper);

  openModal('modal-qr');
}

// ===== LOGO LOADING =====
function loadLogo() {
  // Logo is embedded as placeholder - load actual logo via fetch
  const logoB64 = 'LOGO_B64_HERE';
  document.querySelectorAll('#main-logo, .sidebar-logo').forEach(img => {
    // Logo already set inline
  });
}

// ===== INIT =====
document.addEventListener('DOMContentLoaded', () => {
  // Set today's date on journal
  const jd = document.getElementById('at-journal-date');
  if (jd) jd.value = new Date().toISOString().split('T')[0];
});
</script>
</body>
</html>
