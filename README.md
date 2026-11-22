<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>KEPALA SEKOLAH MASTER AI — Asisten Digital Kepemimpinan & Administrasi Sekolah</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- Google Fonts: Plus Jakarta Sans & Merriweather -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Merriweather:wght@400;700&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  
  <!-- Font Awesome 6 Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" />
  
  <!-- Chart.js for Interactive Analytics -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  
  <!-- SheetJS (XLSX) for Real Excel Import & Export -->
  <script src="https://cdn.sheetjs.com/xlsx-0.20.1/package/dist/xlsx.full.min.js"></script>

  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['"Plus Jakarta Sans"', 'sans-serif'],
            serif: ['"Merriweather"', 'Georgia', 'serif'],
          },
          colors: {
            navy: {
              50: '#f0f4f8',
              100: '#d9e2ec',
              200: '#bcccdc',
              300: '#9fb3c8',
              400: '#829ab1',
              500: '#627d98',
              600: '#486581',
              700: '#334e68',
              800: '#1e3a8a',
              900: '#0f172a',
              950: '#091124',
            },
            gold: {
              50: '#fffbeb',
              100: '#fef3c7',
              200: '#fde68a',
              300: '#fcd34d',
              400: '#fbbf24',
              500: '#f59e0b',
              600: '#d97706',
              700: '#b45309',
              800: '#92400e',
            }
          }
        }
      }
    };
  </script>

  <style>
    /* Custom Scrollbar */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #f1f5f9;
    }
    ::-webkit-scrollbar-thumb {
      background: #cbd5e1;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94a3b8;
    }

    /* Official Letterhead (Kop Surat) Styling */
    .kop-surat {
      font-family: 'Times New Roman', Times, serif;
      color: #000000;
    }
    .kop-border {
      border-bottom: 3px double #000000;
    }

    /* Print Styles */
    @media print {
      body * {
        visibility: hidden;
      }
      #print-area, #print-area * {
        visibility: visible;
      }
      #print-area {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        background: white !important;
        color: black !important;
        padding: 15mm !important;
        margin: 0 !important;
        box-shadow: none !important;
      }
      .no-print {
        display: none !important;
      }
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(6px); }
      to { opacity: 1; transform: translateY(0); }
    }
    .animate-fade-in {
      animation: fadeIn 0.25s ease-out forwards;
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 font-sans antialiased selection:bg-gold-200 selection:text-navy-900">

  <!-- Hidden Print Container -->
  <div id="print-area" class="hidden print:block"></div>

  <!-- Toast Notification Container -->
  <div id="toast-container" class="fixed bottom-4 right-4 z-50 flex flex-col gap-2 max-w-sm w-full pointer-events-none px-4 sm:px-0"></div>

  <!-- Custom Confirmation / Input Modal -->
  <div id="custom-modal" class="fixed inset-0 z-50 hidden items-center justify-center bg-navy-950/60 backdrop-blur-sm p-4">
    <div class="bg-white rounded-2xl shadow-2xl border border-slate-200 max-w-lg w-full overflow-hidden animate-fade-in">
      <div class="bg-navy-900 px-6 py-4 flex items-center justify-between border-b border-gold-500/40">
        <h3 id="modal-title" class="text-white font-bold text-lg flex items-center gap-2">
          <i class="fa-solid fa-circle-info text-gold-400"></i>
          <span>Konfirmasi</span>
        </h3>
        <button onclick="UI.closeModal()" class="text-slate-300 hover:text-white p-1 rounded-lg">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>
      <div id="modal-body" class="p-6 text-slate-700 text-sm leading-relaxed max-h-[70vh] overflow-y-auto"></div>
      <div id="modal-footer" class="bg-slate-50 px-6 py-3.5 flex justify-end gap-3 border-t border-slate-200"></div>
    </div>
  </div>

  <!-- PIN Lock Overlay -->
  <div id="pin-lock-screen" class="fixed inset-0 z-50 hidden items-center justify-center bg-gradient-to-br from-navy-950 via-navy-900 to-navy-800 p-4">
    <div class="bg-white rounded-2xl shadow-2xl max-w-md w-full p-8 text-center border-t-4 border-gold-500">
      <div class="w-16 h-16 bg-navy-900 text-gold-400 rounded-2xl flex items-center justify-center mx-auto mb-4 shadow-lg">
        <i class="fa-solid fa-shield-halved text-3xl"></i>
      </div>
      <h2 class="text-xl font-extrabold text-navy-900">Akses Terkunci</h2>
      <p class="text-sm text-slate-500 mt-1 mb-6">Masukkan PIN Keamanan Kepala Sekolah untuk membuka data administrasi.</p>
      <input type="password" id="pin-unlock-input" maxlength="6" placeholder="••••••" class="w-full text-center tracking-[0.5em] text-2xl font-bold border-2 border-slate-300 focus:border-navy-800 rounded-xl py-3 mb-4 outline-none" />
      <button onclick="App.unlockWithPin()" class="w-full bg-navy-900 hover:bg-navy-800 text-white font-semibold py-3 rounded-xl shadow transition flex items-center justify-center gap-2">
        <i class="fa-solid fa-lock-open text-gold-400"></i> Buka Aplikasi
      </button>
    </div>
  </div>

  <!-- Main App Wrapper -->
  <div class="min-h-screen flex flex-col lg:flex-row">

    <!-- Mobile Top Header -->
    <header class="lg:hidden bg-navy-900 text-white px-4 py-3 flex items-center justify-between sticky top-0 z-30 shadow-md border-b border-gold-500/30">
      <div class="flex items-center gap-3">
        <button onclick="UI.toggleSidebar()" aria-label="Buka Menu" class="p-2.5 rounded-xl bg-navy-800 hover:bg-navy-700 text-gold-400 focus:outline-none">
          <i class="fa-solid fa-bars text-lg"></i>
        </button>
        <div>
          <h1 class="font-extrabold text-sm tracking-wide text-white flex items-center gap-1.5">
            <span>KEPALA SEKOLAH</span>
            <span class="bg-gold-500 text-navy-950 text-[10px] px-1.5 py-0.5 rounded font-black">MASTER AI</span>
          </h1>
          <p id="mobile-school-name" class="text-[11px] text-slate-300 truncate max-w-[200px]">Memuat sekolah...</p>
        </div>
      </div>
      <div class="flex items-center gap-2">
        <button onclick="App.navigate('chatbot-ai')" title="Asisten AI" class="p-2.5 rounded-xl bg-gold-500 text-navy-950 font-bold text-xs shadow">
          <i class="fa-solid fa-robot"></i>
        </button>
      </div>
    </header>

    <!-- Mobile Backdrop -->
    <div id="sidebar-backdrop" onclick="UI.toggleSidebar(false)" class="fixed inset-0 bg-navy-950/60 z-30 hidden lg:hidden backdrop-blur-sm"></div>

    <!-- Sidebar Navigation -->
    <aside id="sidebar" class="fixed lg:sticky top-0 left-0 z-40 h-screen w-72 bg-navy-900 text-white flex flex-col transition-transform duration-300 -translate-x-full lg:translate-x-0 shadow-2xl border-r border-navy-800 shrink-0">
      <!-- Brand Header -->
      <div class="p-5 border-b border-navy-800 bg-navy-950/50">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <div class="w-11 h-11 rounded-xl bg-gradient-to-br from-gold-400 to-gold-600 text-navy-950 flex items-center justify-center shadow-lg shrink-0">
              <i class="fa-solid fa-graduation-cap text-xl"></i>
            </div>
            <div>
              <div class="font-extrabold text-sm tracking-tight leading-tight text-white">
                KEPALA SEKOLAH
              </div>
              <div class="flex items-center gap-1.5 mt-0.5">
                <span class="text-xs font-black tracking-wider text-gold-400">MASTER AI</span>
                <span class="text-[10px] bg-navy-800 text-slate-300 px-1.5 py-0.5 rounded border border-navy-700">v2.1</span>
              </div>
            </div>
          </div>
          <button onclick="UI.toggleSidebar(false)" class="lg:hidden text-slate-400 hover:text-white p-1.5">
            <i class="fa-solid fa-xmark text-lg"></i>
          </button>
        </div>
        <p class="text-[11px] text-gold-300/90 italic mt-3 leading-snug border-t border-navy-800/80 pt-2.5">
          "Administrasi Lebih Mudah, Kepemimpinan Lebih Terarah, Sekolah Semakin Berkualitas."
        </p>
      </div>

      <!-- Role Indicator Pill -->
      <div class="px-4 py-2.5 bg-navy-950/40 border-b border-navy-800 flex items-center justify-between text-xs">
        <span class="text-slate-400">Hak Akses:</span>
        <span id="sidebar-role-badge" class="font-semibold text-gold-400 bg-navy-800 px-2.5 py-0.5 rounded-full border border-gold-500/30">
          Kepala Sekolah
        </span>
      </div>

      <!-- Navigation Links -->
      <nav id="sidebar-nav" class="flex-1 overflow-y-auto px-3 py-4 space-y-1 text-sm"></nav>

      <!-- Developer Credit & Sidebar Footer -->
      <div class="border-t border-navy-800 bg-navy-950/70">
        <div onclick="App.showDeveloperModal()" title="Klik untuk melihat profil pengembang" class="cursor-pointer px-3.5 py-2.5 flex items-center gap-2.5 hover:bg-navy-800/70 transition border-b border-navy-800/70">
          <img src="https://iili.io/oK0I2gj.jpg" onerror="this.onerror=null;this.src='https://placehold.co/120x120/0f172a/fbbf24?text=Eti';" alt="Eti, S.Pd.SD" class="w-9 h-9 rounded-full object-cover border-2 border-gold-400 shrink-0 shadow-sm" />
          <div class="min-w-0 flex-1">
            <div class="text-[10px] uppercase tracking-wider text-gold-400 font-bold">Pengembang</div>
            <div class="text-xs font-extrabold text-white truncate">Eti, S.Pd.SD</div>
          </div>
          <i class="fa-solid fa-circle-info text-slate-400 text-xs"></i>
        </div>
        <div class="px-3.5 py-2.5 text-xs text-slate-400 flex items-center justify-between">
          <div class="flex items-center gap-2">
            <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
            <span id="storage-status-label">Penyimpanan Lokal Aktif</span>
          </div>
          <button onclick="App.navigate('pengaturan')" class="text-gold-400 hover:text-gold-300 font-semibold">
            <i class="fa-solid fa-gear"></i>
          </button>
        </div>
      </div>
    </aside>

    <!-- Main Content Area -->
    <main class="flex-1 flex flex-col min-w-0">
      <!-- Desktop Top Context Bar -->
      <div class="hidden lg:flex bg-white border-b border-slate-200 px-8 py-3.5 items-center justify-between sticky top-0 z-20 shadow-sm">
        <div class="flex items-center gap-3">
          <button id="btn-global-back" onclick="App.goBack()" class="hidden items-center gap-1.5 text-xs font-semibold text-navy-800 bg-slate-100 hover:bg-slate-200 px-3 py-1.5 rounded-lg transition">
            <i class="fa-solid fa-arrow-left"></i> Kembali
          </button>
          <div>
            <div class="flex items-center gap-2 text-xs text-slate-500">
              <span>KEPALA SEKOLAH MASTER AI</span>
              <i class="fa-solid fa-chevron-right text-[10px]"></i>
              <span id="breadcrumb-current" class="font-semibold text-navy-800">Dashboard Utama</span>
            </div>
          </div>
        </div>

        <div class="flex items-center gap-3">
          <div class="text-right">
            <div id="topbar-school-name" class="text-xs font-bold text-navy-900">SMP Negeri 1 Harapan Bangsa</div>
            <div id="topbar-principal-name" class="text-[11px] text-slate-500">Drs. H. Ahmad Fauzi, M.Pd. • TA 2026/2027</div>
          </div>
          <button onclick="App.navigate('generator-ai')" class="bg-navy-900 hover:bg-navy-800 text-white text-xs font-semibold px-3.5 py-2 rounded-xl shadow-sm flex items-center gap-2 transition border border-navy-700">
            <i class="fa-solid fa-wand-magic-sparkles text-gold-400"></i>
            <span>Buat Dokumen AI</span>
          </button>
          <button onclick="App.navigate('chatbot-ai')" class="bg-gold-500 hover:bg-gold-600 text-navy-950 text-xs font-bold px-3.5 py-2 rounded-xl shadow-sm flex items-center gap-2 transition">
            <i class="fa-solid fa-robot"></i>
            <span>Tanya Asisten AI</span>
          </button>
        </div>
      </div>

      <!-- Dynamic View Container -->
      <div id="app-content" class="flex-1 p-4 sm:p-6 lg:p-8 max-w-7xl w-full mx-auto"></div>

      <!-- App Footer -->
      <footer class="bg-white border-t border-slate-200 px-6 py-4 flex flex-col md:flex-row items-center justify-between gap-3 text-xs text-slate-500">
        <div class="text-center md:text-left">
          &copy; 2026 <strong class="text-navy-900">KEPALA SEKOLAH MASTER AI</strong> — Sistem Manajemen & Asisten Digital Kepemimpinan Sekolah.
          <div class="text-gold-700 font-medium mt-0.5">
            "Administrasi Lebih Mudah, Kepemimpinan Lebih Terarah, Sekolah Semakin Berkualitas."
          </div>
        </div>
        <div onclick="App.showDeveloperModal()" title="Lihat Profil Pengembang" class="cursor-pointer flex items-center gap-2.5 bg-navy-50 hover:bg-navy-100 border border-navy-200 px-3.5 py-1.5 rounded-full transition shrink-0">
          <img src="https://iili.io/oK0I2gj.jpg" onerror="this.onerror=null;this.src='https://placehold.co/120x120/0f172a/fbbf24?text=Eti';" alt="Eti, S.Pd.SD" class="w-7 h-7 rounded-full object-cover border border-gold-500" />
          <span class="text-slate-600">Pengembang: <strong class="text-navy-900">Eti, S.Pd.SD</strong></span>
        </div>
      </footer>
    </main>
  </div>

  <script>
    /**
     * HELPER: Safe Deep Clone for Older Android Browsers
     */
    function cloneObj(obj) {
      if (typeof structuredClone === 'function') {
        return structuredClone(obj);
      }
      return JSON.parse(JSON.stringify(obj));
    }

    /**
     * 1. DATABASE & PERSISTENCE LAYER
     */
    const DB_KEY = 'kepsek_master_ai_db_v2';

    const DEFAULT_DATA = {
      settings: {
        schoolName: 'SMP NEGERI 1 HARAPAN BANGSA',
        npsn: '20219876',
        jenjang: 'SMP',
        status: 'Negeri',
        address: 'Jl. Pendidikan No. 45, Kec. Banjar, Kota Banjar, Jawa Barat 46311',
        city: 'Banjar',
        province: 'Jawa Barat',
        email: 'smpn1harapanbangsa@pendidikan.id',
        website: 'www.smpn1harapanbangsa.sch.id',
        academicYear: '2026/2027',
        semester: 'Ganjil (Semester 1)',
        kopLine1: 'PEMERINTAH KOTA BANJAR',
        kopLine2: 'DINAS PENDIDIKAN DAN KEBUDAYAAN',
        principalName: 'Drs. H. Ahmad Fauzi, M.Pd.',
        principalNip: '19720514 199802 1 003',
        principalRank: 'Pembina Utama Muda, IV/c',
        logoDataUrl: '',
        signatureDataUrl: '',
        userRole: 'Kepala Sekolah',
        pinCode: '',
        customApiKey: '',
        cloudProvider: 'local',
        cloudUrl: '',
        cloudKey: ''
      },
      adminUmum: {
        programKerja: [
          { id: 'pk-1', bidang: 'Manajerial & Perencanaan', kegiatan: 'Penyusunan RKT dan RKAS Berbasis Rapor Pendidikan', target: 'Dokumen RKT & RKAS disahkan tepat waktu', waktu: 'Juli 2026', penanggungJawab: 'Kepala Sekolah & Tim Pengembang', status: 'Selesai', progres: 100 },
          { id: 'pk-2', bidang: 'Supervisi Akademik', kegiatan: 'Supervisi Perencanaan, Pelaksanaan & Asesmen Guru Semester 1', target: '100% Guru tersupervisi klinis', waktu: 'September - Oktober 2026', penanggungJawab: 'Kepala Sekolah & Tim Supervisi', status: 'Berjalan', progres: 75 },
          { id: 'pk-3', bidang: 'Kewirausahaan & Inovasi', kegiatan: 'Program Literasi Digital & Pojok Baca Berbasis AI', target: 'Terbentuk 18 pojok baca kelas aktif', waktu: 'Agustus - November 2026', penanggungJawab: 'Waka Kurikulum & Pustakawan', status: 'Berjalan', progres: 60 },
          { id: 'pk-4', bidang: 'Pengembangan Karakter', kegiatan: 'Gerakan Sekolah Sehat, Ramah Anak & Anti Perundungan', target: 'Zero bullying & kantin sehat tersertifikasi', waktu: 'Juli - Desember 2026', penanggungJawab: 'Waka Kesiswaan & Tim TPPK', status: 'Belum Mulai', progres: 20 }
        ],
        rktRingkas: [
          { id: 'rkt-1', standar: 'Standar Isi & Proses', program: 'Workshop Penyusunan Modul Ajar Berdiferensiasi', indikator: 'Guru mampu menyusun modul ajar kontekstual', jadwal: 'Agustus 2026', biaya: 4500000, status: 'Selesai' },
          { id: 'rkt-2', standar: 'Standar Sarpras', program: 'Peremajaan Proyektor & Jaringan Wi-Fi Ruang Kelas', indikator: '12 kelas terkoneksi internet stabil', jadwal: 'Oktober 2026', biaya: 18500000, status: 'Berjalan' }
        ],
        agendaHarian: [
          { id: 'ah-1', tanggal: '2026-11-23', waktu: '07:00 - 08:00', kegiatan: 'Upacara Bendera & Pembinaan Kedisiplinan Siswa', tempat: 'Lapangan Utama', keterangan: 'Amanat tentang persiapan Asesmen Sumatif Akhir Semester' },
          { id: 'ah-2', tanggal: '2026-11-23', waktu: '09:30 - 11:30', kegiatan: 'Supervisi Kelas VIII-B (Mapel IPA)', tempat: 'Ruang Kelas VIII-B', keterangan: 'Observasi pembelajaran Ibu Siti Aminah, S.Pd.' },
          { id: 'ah-3', tanggal: '2026-11-24', waktu: '13:00 - 15:00', kegiatan: 'Rapat Koordinasi Panitia ASAS & Tim Pengembang Kurikulum', tempat: 'Ruang Rapat Kepala Sekolah', keterangan: 'Finalisasi kisi-kisi dan jadwal ujian' }
        ],
        agendaKegiatan: [
          { id: 'ak-1', tanggal: '2026-11-25', namaKegiatan: 'Peringatan Hari Guru Nasional (HGN) 2026', penyelenggara: 'OSIS & Dewan Guru', sasaran: 'Seluruh Warga Sekolah', hasil: 'Dalam persiapan akhir' }
        ],
        tamuUmum: [
          { id: 'tu-1', tanggal: '2026-11-18', nama: 'Budi Santoso, S.Kom.', instansi: 'Komite Sekolah', keperluan: 'Koordinasi dukungan program ekstrakurikuler', tindakLanjut: 'Dijadwalkan rapat pleno komite' }
        ],
        tamuPembinaan: [
          { id: 'tp-1', tanggal: '2026-11-12', nama: 'Dr. Hj. Ratna Dewi, M.Pd.', jabatan: 'Pengawas Pembina Dinas Pendidikan', materiPembinaan: 'Pendampingan Implementasi Kurikulum & Analisis Rapor Pendidikan', saran: 'Tingkatkan tindak lanjut hasil supervisi akademik pada aspek asesmen formatif' }
        ],
        notulenRapat: [
          { id: 'nr-1', tanggal: '2026-11-10', topik: 'Rapat Evaluasi Tengah Semester & Kesiapan ASAS', pimpinan: 'Drs. H. Ahmad Fauzi, M.Pd.', peserta: '32 Guru & Staf TU', keputusan: '1. Pelaksanaan ASAS dimulai 2 Desember 2026.\n2. Remedial dilaksanakan terstruktur sebelum pembagian rapor.\n3. Peningkatan pendampingan literasi-numerasi bagi siswa kelas VIII.' }
        ],
        suratMasuk: [
          { id: 'sm-1', nomorSurat: '421.3/1420/Disdik/2026', tanggalSurat: '2026-11-14', tanggalTerima: '2026-11-15', pengirim: 'Dinas Pendidikan dan Kebudayaan', perihal: 'Undangan Rakor Kepala Sekolah & Evaluasi Serapan BOS', disposisi: 'Waka Kurikulum & Bendahara BOS siapkan bahan laporan' }
        ],
        suratKeluar: [
          { id: 'skl-1', nomorSurat: '421.3/089/SMPN1/XI/2026', tanggal: '2026-11-16', tujuan: 'Orang Tua / Wali Murid Kelas VII, VIII, IX', perihal: 'Pemberitahuan Jadwal Asesmen Sumatif Akhir Semester (ASAS)', keterangan: 'Dikirim melalui wali kelas dan grup resmi' }
        ],
        ekspedisiSurat: [
          { id: 'eks-1', tanggal: '2026-11-16', nomorSurat: '421.3/089/SMPN1/XI/2026', tujuan: 'Dinas Pendidikan (Tembusan Jadwal ASAS)', penerima: 'Staf Sekretariat Disdik', paraf: 'Terkirim & Diterima' }
        ],
        inventarisDokumen: [
          { id: 'invd-1', kodeDok: 'DOK-KUR-01', namaDokumen: 'Kurikulum Satuan Pendidikan (KSP) TA 2026/2027', kategori: 'Kurikulum', lokasiSimpan: 'Lemari Arsip A1 & Drive Sekolah', kondisi: 'Lengkap & Disahkan' },
          { id: 'invd-2', kodeDok: 'DOK-KEU-02', namaDokumen: 'RKAS & SPJ Dana BOS Tahap 1 & 2', kategori: 'Keuangan', lokasiSimpan: 'Brankas Bendahara BOS', kondisi: 'Lengkap' }
        ],
        kegiatanKepsek: [
          { id: 'kk-1', tanggal: '2026-11-19', jenisKegiatan: 'Pelaksanaan Supervisi & Coaching Guru', uraian: 'Melakukan supervisi klinis pembelajaran Matematika kelas VII-A serta diskusi umpan balik', output: 'Instrumen supervisi terisi & rencana tindak lanjut' }
        ],
        dokumentasiKegiatan: [
          { id: 'dokg-1', tanggal: '2026-10-28', judul: 'Peringatan Sumpah Pemuda & Panen Karya P5', deskripsi: 'Pameran karya proyek penguatan profil pelajar dan pentas seni siswa di aula sekolah.', fotoUrl: '' }
        ]
      },
      perencanaan: {
        raporPendidikan: {
          tahun: '2026',
          literasi: 74.5,
          numerasi: 62.8,
          karakter: 78.2,
          iklimKeamanan: 81.0,
          iklimKebinekaan: 79.4,
          kualitasPembelajaran: 66.5,
          catatanTambahan: 'Kemampuan numerasi pada domain aljabar dan geometri masih berada pada kategori sedang, serta metode pembelajaran interaktif perlu ditingkatkan.',
          aiRecommendations: [
            {
              id: 'rec-1',
              indikator: 'Kemampuan Numerasi (62.8 - Sedang)',
              akarMasalah: 'Pembelajaran matematika masih dominan prosedural dan kurang menggunakan konteks masalah nyata (HOTS).',
              rekomendasiKegiatan: 'Pelatihan Internal (IHT) Strategi Pembelajaran Numerasi Kontekstual & Klinik Numerasi Siswa',
              programPrioritas: 'Peningkatan Kompetensi Numerasi Peserta Didik',
              linkRkjmRkt: 'RKJM Tahun ke-2 / RKT Semester Genap',
              usulanRkas: 'Belanja Narasumber IHT & Penggandaan Modul Latihan Numerasi (Rp 6.500.000)',
              disetujui: true
            },
            {
              id: 'rec-2',
              indikator: 'Kualitas Pembelajaran (66.5 - Sedang)',
              akarMasalah: 'Refleksi pembelajaran dan diferensiasi sesuai kesiapan belajar siswa belum konsisten di semua kelas.',
              rekomendasiKegiatan: 'Optimalisasi Komunitas Belajar (Kombel) Sekolah & Lesson Study Antar Guru',
              programPrioritas: 'Transformasi Pembelajaran Berpusat pada Murid',
              linkRkjmRkt: 'RKJM Strategi Mutu / RKT Bulanan',
              usulanRkas: 'Konsumsi Rapat Kombel & Alat Peraga Pembelajaran Aktif (Rp 4.200.000)',
              disetujui: false
            }
          ]
        },
        rkjm: [
          { id: 'rkjm-1', sasaranStrategis: 'Meningkatnya capaian literasi dan numerasi siswa di atas standar nasional', programJangkaMenengah: 'Pengembangan Ekosistem Literasi Digital & Klinik Numerasi Berkelanjutan', tahunTarget: '2025 - 2028', indikatorKeberhasilan: 'Skor Rapor Pendidikan Literasi & Numerasi > 85%', penanggungJawab: 'Kepala Sekolah & Waka Kurikulum' },
          { id: 'rkjm-2', sasaranStrategis: 'Terwujudnya lingkungan belajar yang aman, inklusif, dan berkarakter', programJangkaMenengah: 'Implementasi Sekolah Ramah Anak & Budaya Positif Terintegrasi', tahunTarget: '2025 - 2028', indikatorKeberhasilan: 'Indeks Iklim Keamanan > 90%', penanggungJawab: 'Waka Kesiswaan & Tim TPPK' }
        ],
        rkt: [
          { id: 'rkt-m1', program: 'IHT Peningkatan Numerasi & Pembelajaran Berdiferensiasi', tujuan: 'Meningkatkan skor kualitas pembelajaran dan numerasi siswa', sasaran: '30 Guru Mata Pelajaran', waktu: 'Januari 2027', indikator: '100% guru menerapkan asesmen awal diagnostik', status: 'Direncanakan' },
          { id: 'rkt-m2', program: 'Supervisi Akademik Terpadu Berbasis Coaching', tujuan: 'Membantu guru memperbaiki proses pembelajaran di kelas', sasaran: 'Seluruh Guru', waktu: 'September - November 2026', indikator: 'Minimal 85% guru memperoleh predikat Baik/Amat Baik', status: 'Berjalan' }
        ],
        rkas: [
          { id: 'rkas-1', kodeKegiatan: '03.03.15', uraianKegiatan: 'Pelaksanaan In House Training (IHT) Numerasi Kontekstual', sumberDana: 'BOS Reguler', volume: '1 Kegiatan', anggaran: 6500000, realisasi: 0, status: 'Usulan Draft ARKAS' },
          { id: 'rkas-2', kodeKegiatan: '05.08.04', uraianKegiatan: 'Pemeliharaan & Perbaikan LCD Proyektor Ruang Kelas', sumberDana: 'BOS Reguler', volume: '6 Unit', anggaran: 9000000, realisasi: 6000000, status: 'Berjalan' },
          { id: 'rkas-3', kodeKegiatan: '04.06.02', uraianKegiatan: 'Pengadaan Buku Pengayaan Literasi Perpustakaan', sumberDana: 'BOS Reguler', volume: '120 Eksemplar', anggaran: 12000000, realisasi: 12000000, status: 'Terealisasi' }
        ]
      },
      supervisi: {
        instrumenCustom: [
          { id: 'ins-1', aspek: 'Perencanaan Pembelajaran', indikator: 'Kelengkapan Modul Ajar/RPP, kesesuaian CP-TP-ATP, dan rancangan diferensiasi' },
          { id: 'ins-2', aspek: 'Pelaksanaan Pembelajaran', indikator: 'Apersepsi bermakna, keaktifan siswa, pemanfaatan media/teknologi, dan manajemen kelas' },
          { id: 'ins-3', aspek: 'Asesmen Pembelajaran', indikator: 'Pelaksanaan asesmen formatif, umpan balik konstruktif, dan refleksi akhir pembelajaran' }
        ],
        records: [
          {
            id: 'sup-1',
            tanggal: '2026-10-14',
            namaGuru: 'Siti Aminah, S.Pd.',
            nip: '19850412 201001 2 018',
            mapel: 'Ilmu Pengetahuan Alam (IPA)',
            kelas: 'VIII-B',
            jenis: 'Supervisi Akademik',
            skorPerencanaan: 92,
            skorPelaksanaan: 88,
            skorAsesmen: 85,
            catatanObservasi: 'Guru sangat menguasai materi, praktikum sederhana berjalan tertib, siswa antusias berdiskusi kelompok.',
            rekomendasi: 'Perlu memperdalam pertanyaan pemantik di akhir sesi untuk memastikan refleksi individual siswa.',
            tindakLanjut: 'Berbagi praktik baik pada forum Kombel sekolah bulan November 2026.'
          },
          {
            id: 'sup-2',
            tanggal: '2026-10-21',
            namaGuru: 'Rudi Hartono, S.Pd.',
            nip: '19900817 201903 1 009',
            mapel: 'Matematika',
            kelas: 'VII-A',
            jenis: 'Supervisi Akademik',
            skorPerencanaan: 84,
            skorPelaksanaan: 78,
            skorAsesmen: 76,
            catatanObservasi: 'Penjelasan konsep jelas namun latihan soal masih berfokus pada rumus cepat, belum menyentuh pemecahan masalah kontekstual.',
            rekomendasi: 'Gunakan alat peraga atau studi kasus sehari-hari serta lakukan asesmen diagnostik awal.',
            tindakLanjut: 'Pendampingan khusus penyusunan LKPD berbasis numerasi kontekstual.'
          }
        ]
      },
      gtk: [
        { id: 'gtk-1', nama: 'Siti Aminah, S.Pd.', nip: '19850412 201001 2 018', jabatan: 'Guru Madya / Waka Kurikulum', mapel: 'IPA', pendidikan: 'S1 Pendidikan Biologi', tugasTambahan: 'Wakil Kepala Sekolah Bidang Kurikulum', jamMengajar: 18, kehadiran: 99, nilaiKinerja: 93, pelatihan: 'Pelatihan Pembelajaran Berdiferensiasi (2026)', catatanPembinaan: 'Kinerja sangat baik, siap menjadi narasumber Kombel.' },
        { id: 'gtk-2', nama: 'Rudi Hartono, S.Pd.', nip: '19900817 201903 1 009', jabatan: 'Guru Pertama', mapel: 'Matematika', pendidikan: 'S1 Pendidikan Matematika', tugasTambahan: 'Pembina OSIS', jamMengajar: 24, kehadiran: 96, nilaiKinerja: 82, pelatihan: 'Bimtek Numerasi Tingkat Kota (2026)', catatanPembinaan: 'Perlu penguatan variasi metode asesmen formatif.' },
        { id: 'gtk-3', nama: 'Dewi Lestari, S.Pd., M.Pd.', nip: '19821105 200801 2 012', jabatan: 'Guru Madya / Koordinator Supervisi', mapel: 'Bahasa Indonesia', pendidikan: 'S2 Pendidikan Bahasa', tugasTambahan: 'Kepala Perpustakaan', jamMengajar: 18, kehadiran: 98, nilaiKinerja: 91, pelatihan: 'Diklat Penguatan Literasi Sekolah (2026)', catatanPembinaan: 'Sangat aktif menggerakkan gerakan literasi sekolah.' },
        { id: 'gtk-4', nama: 'Hendra Gunawan, S.E.', nip: '19880322 201402 1 004', jabatan: 'Kepala Tenaga Administrasi', mapel: '-', pendidikan: 'S1 Manajemen', tugasTambahan: 'Operator Dapodik & Bendahara Barang', jamMengajar: 0, kehadiran: 98, nilaiKinerja: 90, pelatihan: 'Bimtek ARKAS & Pengelolaan Aset Daerah', catatanPembinaan: 'Tertib administrasi persuratan dan inventaris.' }
      ],
      siswa: {
        rombel: [
          { id: 'rmb-1', tingkat: 'Kelas VII', namaRombel: 'VII-A, VII-B, VII-C, VII-D (4 Rombel)', waliKelas: 'Tim Wali Kelas VII', lakiLaki: 64, perempuan: 68, kehadiran: 96.5 },
          { id: 'rmb-2', tingkat: 'Kelas VIII', namaRombel: 'VIII-A, VIII-B, VIII-C, VIII-D (4 Rombel)', waliKelas: 'Tim Wali Kelas VIII', lakiLaki: 61, perempuan: 65, kehadiran: 95.8 },
          { id: 'rmb-3', tingkat: 'Kelas IX', namaRombel: 'IX-A, IX-B, IX-C, IX-D (4 Rombel)', waliKelas: 'Tim Wali Kelas IX', lakiLaki: 58, perempuan: 62, kehadiran: 97.1 }
        ],
        prestasiDanCatatan: [
          { id: 'prs-1', namaSiswa: 'Nabila Putri Azzahra', kelas: 'VIII-A', jenis: 'Prestasi Akademik', judul: 'Juara 1 Olimpiade Sains Nasional (OSN) IPA Tingkat Kota', tingkat: 'Kota/Kabupaten', pembina: 'Siti Aminah, S.Pd.', tanggal: '2026-08-22' },
          { id: 'prs-2', namaSiswa: 'Rafi Akbar Pratama', kelas: 'IX-B', jenis: 'Prestasi Non-Akademik', judul: 'Juara 2 Kejuaraan Pencak Silat Pelajar Provinsi', tingkat: 'Provinsi', pembina: 'Guru PJOK', tanggal: '2026-09-15' },
          { id: 'prs-3', namaSiswa: 'Tim Pramuka Garuda', kelas: 'VII & VIII', jenis: 'Ekstrakurikuler & Karakter', judul: 'Juara Umum Lomba Tingkat II Kwarran', tingkat: 'Kecamatan', pembina: 'Rudi Hartono, S.Pd.', tanggal: '2026-10-10' }
        ],
        rekapMutasi: {
          siswaBaru: 132,
          lulusanTahunLalu: 124,
          pindahMasuk: 3,
          pindahKeluar: 1
        }
      },
      sarpras: [
        { id: 'srp-1', kodeBarang: 'INV-RKL-01', namaBarang: 'Ruang Kelas Belajar (12 Ruang)', kategori: 'Ruang Kelas', jumlah: 12, kondisi: 'Baik', jadwalPemeliharaan: 'Desember 2026', nilaiAset: 1800000000, keterangan: '10 kondisi sangat baik, 2 perlu pengecatan ulang' },
        { id: 'srp-2', kodeBarang: 'INV-LAB-02', namaBarang: 'Laboratorium Komputer & Perangkat Chromebook', kategori: 'Fasilitas Pembelajaran', jumlah: 35, kondisi: 'Baik', jadwalPemeliharaan: 'November 2026', nilaiAset: 210000000, keterangan: 'Siap untuk ANBK & ASAS berbasis komputer' },
        { id: 'srp-3', kodeBarang: 'INV-LCD-03', namaBarang: 'Proyektor LCD Kelas', kategori: 'Elektronik', jumlah: 12, kondisi: 'Rusak Ringan', jadwalPemeliharaan: 'Segera (November 2026)', nilaiAset: 60000000, keterangan: '2 unit lampu proyektor redup, masuk usulan pengadaan/perbaikan' }
      ],
      kurikulum: [
        { id: 'kur-1', aspek: 'Kalender Pendidikan', namaProgram: 'Penyusunan Kalender Akademik & Jadwal Asesmen TA 2026/2027', penanggungJawab: 'Waka Kurikulum', periode: 'Juli 2026 - Juni 2027', status: 'Terlaksana', catatan: 'Selaras dengan kalender Dinas Pendidikan Provinsi & Kota' },
        { id: 'kur-2', aspek: 'Pembagian Tugas Mengajar', namaProgram: 'SK Pembagian Tugas Mengajar & Jadwal Pelajaran Semester 1', penanggungJawab: 'Kepala Sekolah & Waka Kurikulum', periode: 'Semester 1 (2026/2027)', status: 'Terlaksana', catatan: 'Total 288 JP terdistribusi proporsional ke seluruh guru mapel' },
        { id: 'kur-3', aspek: 'Peningkatan Mutu Pembelajaran', namaProgram: 'Klinik Literasi-Numerasi & Kokurikuler P5', penanggungJawab: 'Koordinator P5 & Kombel', periode: 'Setiap Jumat Pagi', status: 'Berjalan', catatan: 'Antusiasme siswa tinggi, karya dipamerkan tiap akhir semester' }
      ],
      kepemimpinan: {
        visi: 'Terwujudnya Peserta Didik yang Beriman, Berkarakter Mulia, Bernalar Kritis, Kreatif, dan Berdaya Saing Global dalam Lingkungan Belajar yang Aman dan Menyenangkan.',
        misi: '1. Menanamkan nilai keimanan, ketakwaan, dan pembiasaan budaya positif sehari-hari.\n2. Menyelenggarakan pembelajaran aktif, berdiferensiasi, dan berbasis teknologi.\n3. Mengembangkan potensi literasi, numerasi, serta bakat minat siswa secara optimal.\n4. Membangun kemitraan harmonis antara sekolah, orang tua, komite, dan masyarakat.',
        tujuan: 'Meningkatkan rata-rata capaian Rapor Pendidikan di atas 85%, mewujudkan sekolah ramah anak tanpa perundungan, dan melahirkan lulusan berkarakter kuat.',
        programStrategis: [
          { id: 'kp-1', kategori: 'Budaya Positif & Karakter', namaProgram: 'Gerakan 5S (Senyum, Sapa, Salam, Sopan, Santun) & Pembiasaan Pagi', mitra: 'OSIS, Guru BK, Orang Tua', dampak: 'Meningkatnya kedisiplinan dan suasana kekeluargaan di sekolah', status: 'Aktif' },
          { id: 'kp-2', kategori: 'Kemitraan & Komite Sekolah', namaProgram: 'Forum Parenting & Sinergi Komite Sekolah', mitra: 'Komite Sekolah & Puskesmas Setempat', dampak: 'Dukungan penuh program sekolah sehat dan pencegahan kenakalan remaja', status: 'Aktif' },
          { id: 'kp-3', kategori: 'Inovasi & Sekolah Sehat', namaProgram: 'Digitalisasi Administrasi & Kantin Sehat Bebas Plastik', mitra: 'Dinas Kesehatan & Tim Adiwiyata', dampak: 'Efisiensi tata kelola dan berkurangnya sampah plastik hingga 70%', status: 'Aktif' }
        ]
      },
      monev: [
        { id: 'mnv-1', periode: 'Evaluasi Bulanan (Oktober 2026)', namaProgram: 'Supervisi Akademik Guru Semester 1', targetPersen: 100, capaianPersen: 75, kendala: 'Beberapa jadwal bentrok dengan kegiatan pelatihan guru di luar sekolah', tindakLanjut: 'Penjadwalan ulang supervisi susulan pada minggu ke-3 November 2026' },
        { id: 'mnv-2', periode: 'Evaluasi Semester 1', namaProgram: 'Gerakan Literasi & Pojok Baca Kelas', targetPersen: 100, capaianPersen: 85, kendala: 'Variasi buku bacaan non-fiksi di beberapa kelas masih terbatas', tindakLanjut: 'Rotasi koleksi buku perpustakaan antar kelas setiap 2 minggu' },
        { id: 'mnv-3', periode: 'Evaluasi Tahunan', namaProgram: 'Pemeliharaan Sarana TIK & Jaringan Internet', targetPersen: 100, capaianPersen: 60, kendala: 'Menunggu pencairan anggaran pemeliharaan tahap berikutnya', tindakLanjut: 'Prioritaskan perbaikan 2 proyektor ruang kelas IX terlebih dahulu' }
      ],
      arsipDokumen: [
        { id: 'arc-1', judul: 'SK Pembagian Tugas Mengajar Semester 1 TA 2026/2027', kategori: 'Kurikulum', nomorSurat: '421.3/012/SMPN1/VII/2026', tanggal: '2026-07-15', konten: 'KEPUTUSAN KEPALA SMP NEGERI 1 HARAPAN BANGSA\nNomor: 421.3/012/SMPN1/VII/2026\nTentang Pembagian Tugas Guru dalam Kegiatan Belajar Mengajar Semester 1 Tahun Pelajaran 2026/2027...' },
        { id: 'arc-2', judul: 'Laporan Pelaksanaan Supervisi Akademik Bulan Oktober 2026', kategori: 'Supervisi', nomorSurat: '421.3/074/SMPN1/X/2026', tanggal: '2026-10-30', konten: 'LAPORAN HASIL SUPERVISI AKADEMIK\nTelah dilaksanakan supervisi terhadap 24 dari 30 guru dengan rata-rata capaian skor 86,5 (Kategori Baik)...' }
      ]
    };

    const DB = {
      data: null,
      storageAvailable: true,
      init() {
        try {
          const raw = localStorage.getItem(DB_KEY);
          if (raw) {
            const parsed = JSON.parse(raw);
            this.data = this.mergeDeep(cloneObj(DEFAULT_DATA), parsed);
          } else {
            this.data = cloneObj(DEFAULT_DATA);
            this.save(false);
          }
        } catch (err) {
          console.warn('Penyimpanan lokal terbatas, beralih ke penyimpanan sesi:', err);
          this.storageAvailable = false;
          this.data = cloneObj(DEFAULT_DATA);
        }
      },
      mergeDeep(target, source) {
        if (!source || typeof source !== 'object') return target;
        for (const key of Object.keys(source)) {
          if (source[key] && typeof source[key] === 'object' && !Array.isArray(source[key]) && key in target && target[key]) {
            this.mergeDeep(target[key], source[key]);
          } else {
            target[key] = source[key];
          }
        }
        return target;
      },
      save(notify = true) {
        try {
          if (this.storageAvailable) {
            localStorage.setItem(DB_KEY, JSON.stringify(this.data));
          }
          App.updateGlobalHeaders();
          if (notify) {
            UI.toast('Data berhasil disimpan!', 'success');
          }
        } catch (e) {
          this.storageAvailable = false;
          if (notify) {
            UI.toast('Data tersimpan di memori sesi (gunakan Ekspor Cadangan JSON untuk menyimpan file).', 'warning');
          }
        }
      },
      resetDefault() {
        this.data = cloneObj(DEFAULT_DATA);
        this.save(false);
        UI.toast('Data berhasil dikembalikan ke data awal standar.', 'info');
        App.renderCurrent();
      },
      uid(prefix = 'id') {
        return `${prefix}-${Date.now()}-${Math.random().toString(36).substring(2, 6)}`;
      }
    };

    /**
     * 2. GEMINI AI ENGINE (Live API + Exponential Backoff + Smart Fallback)
     */
    const AI = {
      async generate(prompt, systemInstruction = '', expectJson = false) {
        const userKey = DB.data?.settings?.customApiKey?.trim() || '';
        const apiKey = userKey || "";
        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;

        const payload = {
          contents: [{ parts: [{ text: prompt }] }],
          systemInstruction: {
            parts: [{
              text: systemInstruction ||
                'Anda adalah asisten ahli manajemen pendidikan dan kepemimpinan kepala sekolah di Indonesia. Gunakan Bahasa Indonesia yang formal, sistematis, praktis, dan sesuai regulasi pendidikan Indonesia. Jangan pernah mengarang nomor surat, tanggal faktual, atau data sekolah yang tidak diberikan pengguna.'
            }]
          }
        };

        if (expectJson) {
          payload.generationConfig = { responseMimeType: 'application/json' };
        }

        const delays = [1000, 2000, 4000];
        for (let attempt = 0; attempt <= delays.length; attempt++) {
          try {
            const response = await fetch(apiUrl, {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify(payload)
            });

            if (!response.ok) {
              throw new Error(`HTTP ${response.status}`);
            }

            const result = await response.json();
            const text = result?.candidates?.[0]?.content?.parts?.[0]?.text;
            if (!text) throw new Error('Empty AI response');
            return text;
          } catch (err) {
            if (attempt < delays.length) {
              await new Promise(res => setTimeout(res, delays[attempt]));
            } else {
              return null;
            }
          }
        }
        return null;
      }
    };

    /**
     * 3. EXPORT, PRINT, WORD (.DOC) & EXCEL (.XLSX) UTILITIES
     */
    const ExportUtils = {
      getKopSuratHTML() {
        const s = DB.data.settings;
        const logoHtml = s.logoDataUrl
          ? `<img src="${s.logoDataUrl}" alt="Logo" style="width:72px;height:72px;object-fit:contain;" />`
          : `<div style="width:68px;height:68px;border:2px solid #1e3a8a;border-radius:12px;display:flex;align-items:center;justify-content:center;font-weight:bold;font-size:11px;text-align:center;color:#1e3a8a;">LOGO<br/>SEKOLAH</div>`;

        return `
          <div class="kop-surat kop-border" style="display:flex;align-items:center;gap:16px;padding-bottom:10px;margin-bottom:18px;">
            <div>${logoHtml}</div>
            <div style="flex:1;text-align:center;line-height:1.25;">
              <div style="font-size:13pt;font-weight:bold;text-transform:uppercase;">${this.escape(s.kopLine1)}</div>
              <div style="font-size:14pt;font-weight:bold;text-transform:uppercase;">${this.escape(s.kopLine2)}</div>
              <div style="font-size:16pt;font-weight:bold;text-transform:uppercase;margin:2px 0;">${this.escape(s.schoolName)}</div>
              <div style="font-size:9.5pt;">NPSN: ${this.escape(s.npsn)} • ${this.escape(s.address)}</div>
              <div style="font-size:9pt;">Email: ${this.escape(s.email)} • Website: ${this.escape(s.website)}</div>
            </div>
          </div>
        `;
      },

      getSignatureHTML(tanggalStr = '') {
        const s = DB.data.settings;
        const tgl = tanggalStr || '[Tanggal Penetapan]';
        const sigImg = s.signatureDataUrl
          ? `<img src="${s.signatureDataUrl}" alt="TTD" style="height:64px;object-fit:contain;margin:4px 0;" />`
          : `<div style="height:64px;"></div>`;

        return `
          <div style="margin-top:32px;display:flex;justify-content:flex-end;font-family:'Times New Roman',serif;font-size:12pt;">
            <div style="width:270px;text-align:left;line-height:1.35;">
              <div>${this.escape(s.city)}, ${this.escape(tgl)}</div>
              <div>Kepala Sekolah,</div>
              ${sigImg}
              <div style="font-weight:bold;text-decoration:underline;">${this.escape(s.principalName)}</div>
              <div>${this.escape(s.principalRank)}</div>
              <div>NIP. ${this.escape(s.principalNip)}</div>
            </div>
          </div>
        `;
      },

      printDocument(title, bodyHtml, includeKop = true, includeSignature = true, dateStr = '') {
        const printArea = document.getElementById('print-area');
        printArea.innerHTML = `
          ${includeKop ? this.getKopSuratHTML() : ''}
          <div style="font-family:'Times New Roman',serif;font-size:12pt;line-height:1.5;color:#000;">
            ${bodyHtml}
          </div>
          ${includeSignature ? this.getSignatureHTML(dateStr) : ''}
        `;
        setTimeout(() => {
          window.print();
        }, 150);
      },

      downloadWord(filename, bodyHtml, includeKop = true, includeSignature = true, dateStr = '') {
        const fullHtml = `
          <html xmlns:o='urn:schemas-microsoft-com:office:office'
                xmlns:w='urn:schemas-microsoft-com:office:word'
                xmlns='http://www.w3.org/TR/REC-html40'>
          <head><meta charset='utf-8'><title>${filename}</title>
          <style>
            body { font-family: 'Times New Roman', serif; font-size: 12pt; line-height: 1.5; color: #000; }
            table { border-collapse: collapse; width: 100%; margin: 10px 0; }
            th, td { border: 1px solid #000; padding: 6px 8px; text-align: left; font-size: 11pt; }
            th { background-color: #f2f2f2; }
          </style>
          </head>
          <body>
            ${includeKop ? this.getKopSuratHTML() : ''}
            <div>${bodyHtml}</div>
            ${includeSignature ? this.getSignatureHTML(dateStr) : ''}
          </body>
          </html>
        `;
        const blob = new Blob(['\ufeff', fullHtml], { type: 'application/msword' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = filename.endsWith('.doc') ? filename : `${filename}.doc`;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
        UI.toast(`Dokumen Word "${a.download}" berhasil diunduh!`, 'success');
      },

      exportExcel(filename, sheetName, rowsArray) {
        try {
          if (typeof XLSX === 'undefined') {
            UI.toast('Pustaka Excel sedang dimuat, coba sesaat lagi.', 'warning');
            return;
          }
          const ws = XLSX.utils.json_to_sheet(rowsArray);
          const wb = XLSX.utils.book_new();
          XLSX.utils.book_append_sheet(wb, ws, sheetName.substring(0, 31));
          XLSX.writeFile(wb, filename.endsWith('.xlsx') ? filename : `${filename}.xlsx`);
          UI.toast(`File Excel "${filename}" berhasil diunduh!`, 'success');
        } catch (err) {
          UI.toast('Gagal mengekspor Excel: ' + err.message, 'error');
        }
      },

      copyText(text) {
        const textarea = document.createElement('textarea');
        textarea.value = text;
        textarea.style.position = 'fixed';
        textarea.style.opacity = '0';
        document.body.appendChild(textarea);
        textarea.select();
        try {
          document.execCommand('copy');
          UI.toast('Teks berhasil disalin ke papan klip!', 'success');
        } catch (e) {
          UI.toast('Gagal menyalin teks.', 'error');
        }
        document.body.removeChild(textarea);
      },

      escape(str) {
        if (str === null || str === undefined) return '';
        return String(str)
          .replace(/&/g, '&amp;')
          .replace(/</g, '&lt;')
          .replace(/>/g, '&gt;')
          .replace(/"/g, '&quot;');
      },

      formatRupiah(num) {
        return new Intl.NumberFormat('id-ID', { style: 'currency', currency: 'IDR', maximumFractionDigits: 0 }).format(Number(num) || 0);
      }
    };

    /**
     * 4. UI HELPERS
     */
    const UI = {
      toast(message, type = 'info') {
        const container = document.getElementById('toast-container');
        const colors = {
          success: 'bg-emerald-900 border-emerald-400 text-white',
          error: 'bg-rose-900 border-rose-400 text-white',
          warning: 'bg-amber-900 border-amber-400 text-white',
          info: 'bg-navy-900 border-gold-400 text-white'
        };
        const icons = {
          success: 'fa-circle-check text-emerald-400',
          error: 'fa-circle-exclamation text-rose-400',
          warning: 'fa-triangle-exclamation text-amber-400',
          info: 'fa-circle-info text-gold-400'
        };
        const el = document.createElement('div');
        el.className = `pointer-events-auto flex items-center gap-3 px-4 py-3 rounded-xl shadow-xl border-l-4 ${colors[type] || colors.info} animate-fade-in text-xs sm:text-sm`;
        el.innerHTML = `
          <i class="fa-solid ${icons[type] || icons.info} text-base shrink-0"></i>
          <span class="flex-1 font-medium leading-snug">${ExportUtils.escape(message)}</span>
        `;
        container.appendChild(el);
        setTimeout(() => {
          el.style.opacity = '0';
          el.style.transition = 'opacity 0.3s';
          setTimeout(() => el.remove(), 300);
        }, 3400);
      },

      openModal({ title, bodyHtml, footerHtml }) {
        document.getElementById('modal-title').innerHTML = `<i class="fa-solid fa-circle-info text-gold-400"></i> <span>${title}</span>`;
        document.getElementById('modal-body').innerHTML = bodyHtml;
        document.getElementById('modal-footer').innerHTML = footerHtml || `<button onclick="UI.closeModal()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-semibold">Tutup</button>`;
        const modal = document.getElementById('custom-modal');
        modal.classList.remove('hidden');
        modal.classList.add('flex');
      },

      closeModal() {
        const modal = document.getElementById('custom-modal');
        modal.classList.add('hidden');
        modal.classList.remove('flex');
      },

      confirmAction(title, message, onConfirm) {
        this.openModal({
          title,
          bodyHtml: `<p class="text-slate-700">${message}</p>`,
          footerHtml: `
            <button onclick="UI.closeModal()" class="px-4 py-2.5 bg-slate-200 hover:bg-slate-300 text-slate-800 rounded-xl text-xs font-semibold transition">Batal</button>
            <button id="modal-confirm-btn" class="px-4 py-2.5 bg-rose-600 hover:bg-rose-700 text-white rounded-xl text-xs font-semibold shadow transition">Ya, Lanjutkan</button>
          `
        });
        document.getElementById('modal-confirm-btn').onclick = () => {
          this.closeModal();
          onConfirm();
        };
      },

      toggleSidebar(forceState) {
        const sidebar = document.getElementById('sidebar');
        const backdrop = document.getElementById('sidebar-backdrop');
        const isClosed = sidebar.classList.contains('-translate-x-full');
        const shouldOpen = forceState !== undefined ? forceState : isClosed;

        if (shouldOpen) {
          sidebar.classList.remove('-translate-x-full');
          backdrop.classList.remove('hidden');
        } else {
          sidebar.classList.add('-translate-x-full');
          backdrop.classList.add('hidden');
        }
      },

      canEdit() {
        const role = DB.data?.settings?.userRole || 'Kepala Sekolah';
        if (role === 'Pengawas') {
          this.toast('Mode Pengawas/Tamu (Hanya Baca). Ubah hak akses di Pengaturan untuk mengedit.', 'warning');
          return false;
        }
        return true;
      },

      guideBanner(title, stepsText) {
        return `
          <div class="bg-navy-50 border border-navy-200 rounded-2xl p-4 mb-6 flex items-start gap-3.5">
            <div class="w-9 h-9 rounded-xl bg-navy-900 text-gold-400 flex items-center justify-center shrink-0 mt-0.5 shadow-sm">
              <i class="fa-solid fa-lightbulb"></i>
            </div>
            <div class="text-xs sm:text-sm text-navy-900 leading-relaxed">
              <div class="font-bold text-navy-900 mb-0.5">Petunjuk Penggunaan — ${title}</div>
              <p class="text-slate-600">${stepsText}</p>
            </div>
          </div>
        `;
      },

      moduleHeader(title, subtitle, iconClass, extraActionsHtml = '') {
        return `
          <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4 mb-6 pb-4 border-b border-slate-200">
            <div class="flex items-start sm:items-center gap-3.5">
              <button onclick="App.navigate('dashboard')" title="Kembali ke Dashboard" class="p-2.5 rounded-xl bg-white border border-slate-200 hover:bg-slate-100 text-navy-900 shadow-sm transition shrink-0">
                <i class="fa-solid fa-arrow-left"></i>
              </button>
              <div class="w-12 h-12 rounded-2xl bg-gradient-to-br from-navy-900 to-navy-800 text-gold-400 flex items-center justify-center shadow-md shrink-0">
                <i class="${iconClass} text-xl"></i>
              </div>
              <div>
                <h2 class="text-xl sm:text-2xl font-extrabold text-navy-900 tracking-tight">${title}</h2>
                <p class="text-xs sm:text-sm text-slate-500 mt-0.5">${subtitle}</p>
              </div>
            </div>
            <div class="flex flex-wrap items-center gap-2">
              ${extraActionsHtml}
            </div>
          </div>
        `;
      }
    };

    /**
     * 5. NAVIGATION & ROUTER SYSTEM
     */
    const MENU_ITEMS = [
      { group: 'UTAMA', id: 'dashboard', label: 'Dashboard Utama', icon: 'fa-solid fa-gauge-high' },
      { group: 'FITUR UNGGULAN AI', id: 'generator-ai', label: 'Generator Dokumen AI', icon: 'fa-solid fa-wand-magic-sparkles', badge: 'AI' },
      { group: 'FITUR UNGGULAN AI', id: 'chatbot-ai', label: 'Asisten Kepala Sekolah AI', icon: 'fa-solid fa-robot', badge: 'AI' },
      { group: 'ADMINISTRASI & PERENCANAAN', id: 'admin-umum', label: 'Administrasi Umum Kepsek', icon: 'fa-solid fa-folder-open' },
      { group: 'ADMINISTRASI & PERENCANAAN', id: 'perencanaan', label: 'Perencanaan & RKAS/Rapor', icon: 'fa-solid fa-chart-line' },
      { group: 'ADMINISTRASI & PERENCANAAN', id: 'supervisi', label: 'Supervisi Akademik & Manajerial', icon: 'fa-solid fa-clipboard-check' },
      { group: 'MANAJEMEN SEKOLAH', id: 'gtk', label: 'Manajemen Guru & GTK', icon: 'fa-solid fa-chalkboard-user' },
      { group: 'MANAJEMEN SEKOLAH', id: 'siswa', label: 'Manajemen Peserta Didik', icon: 'fa-solid fa-users' },
      { group: 'MANAJEMEN SEKOLAH', id: 'sarpras', label: 'Sarana & Prasarana', icon: 'fa-solid fa-building-columns' },
      { group: 'MANAJEMEN SEKOLAH', id: 'kurikulum', label: 'Kurikulum & Pembelajaran', icon: 'fa-solid fa-book-open-reader' },
      { group: 'MANAJEMEN SEKOLAH', id: 'kepemimpinan', label: 'Kepemimpinan & Pengembangan', icon: 'fa-solid fa-award' },
      { group: 'EVALUASI & ARSIP', id: 'monev', label: 'Monitoring & Evaluasi', icon: 'fa-solid fa-magnifying-glass-chart' },
      { group: 'EVALUASI & ARSIP', id: 'template-center', label: 'Pusat Template Administrasi', icon: 'fa-solid fa-file-lines' },
      { group: 'EVALUASI & ARSIP', id: 'pusat-laporan', label: 'Pusat Laporan Sekolah', icon: 'fa-solid fa-file-invoice' },
      { group: 'EVALUASI & ARSIP', id: 'arsip-dokumen', label: 'Pengelolaan Dokumen', icon: 'fa-solid fa-box-archive' },
      { group: 'SISTEM', id: 'pengaturan', label: 'Pengaturan & Database', icon: 'fa-solid fa-sliders' }
    ];

    const App = {
      currentRoute: 'dashboard',
      historyStack: [],
      charts: {},

      init() {
        DB.init();
        this.checkPinLock();
        this.updateGlobalHeaders();
        this.renderSidebar();
        this.navigate('dashboard', false);
      },

      checkPinLock() {
        const pin = DB.data?.settings?.pinCode;
        if (pin && pin.trim().length >= 4) {
          const lockScreen = document.getElementById('pin-lock-screen');
          lockScreen.classList.remove('hidden');
          lockScreen.classList.add('flex');
        }
      },

      unlockWithPin() {
        const input = document.getElementById('pin-unlock-input').value.trim();
        if (input === DB.data.settings.pinCode) {
          const lockScreen = document.getElementById('pin-lock-screen');
          lockScreen.classList.add('hidden');
          lockScreen.classList.remove('flex');
          UI.toast('Selamat datang kembali, Kepala Sekolah!', 'success');
        } else {
          UI.toast('PIN tidak sesuai. Silakan coba lagi.', 'error');
        }
      },

      updateGlobalHeaders() {
        const s = DB.data.settings;
        const mSchool = document.getElementById('mobile-school-name');
        const tSchool = document.getElementById('topbar-school-name');
        const tPrin = document.getElementById('topbar-principal-name');
        const roleBadge = document.getElementById('sidebar-role-badge');
        const storageLabel = document.getElementById('storage-status-label');

        if (mSchool) mSchool.textContent = s.schoolName;
        if (tSchool) tSchool.textContent = `${s.schoolName} (NPSN: ${s.npsn})`;
        if (tPrin) tPrin.textContent = `${s.principalName} • TA ${s.academicYear}`;
        if (roleBadge) roleBadge.textContent = s.userRole || 'Kepala Sekolah';
        if (storageLabel) storageLabel.textContent = DB.storageAvailable ? 'Penyimpanan Lokal Aktif' : 'Mode Memori Sesi';
      },

      renderSidebar() {
        const nav = document.getElementById('sidebar-nav');
        let html = '';
        let lastGroup = '';

        MENU_ITEMS.forEach(item => {
          if (item.group !== lastGroup) {
            html += `<div class="px-3 pt-4 pb-1.5 text-[10px] font-bold tracking-wider text-slate-400 uppercase">${item.group}</div>`;
            lastGroup = item.group;
          }
          const active = this.currentRoute === item.id;
          const activeClass = active
            ? 'bg-gradient-to-r from-gold-500 to-gold-600 text-navy-950 font-bold shadow-md'
            : 'text-slate-300 hover:bg-navy-800/80 hover:text-white font-medium';
          const iconColor = active ? 'text-navy-950' : 'text-gold-400';

          html += `
            <button onclick="App.navigate('${item.id}')" class="w-full flex items-center justify-between px-3.5 py-2.5 rounded-xl transition text-left ${activeClass}">
              <span class="flex items-center gap-3 min-w-0">
                <i class="${item.icon} ${iconColor} w-4 text-center shrink-0"></i>
                <span class="truncate text-xs sm:text-[13px]">${item.label}</span>
              </span>
              ${item.badge ? `<span class="text-[10px] font-black px-1.5 py-0.5 rounded ${active ? 'bg-navy-950 text-gold-400' : 'bg-gold-500/20 text-gold-300'}">${item.badge}</span>` : ''}
            </button>
          `;
        });
        nav.innerHTML = html;
      },

      navigate(routeId, pushHistory = true) {
        if (pushHistory && this.currentRoute !== routeId) {
          this.historyStack.push(this.currentRoute);
        }
        this.currentRoute = routeId;
        this.renderSidebar();
        UI.toggleSidebar(false);

        const menuObj = MENU_ITEMS.find(m => m.id === routeId);
        const bc = document.getElementById('breadcrumb-current');
        if (bc && menuObj) bc.textContent = menuObj.label;

        const backBtn = document.getElementById('btn-global-back');
        if (backBtn) {
          if (this.historyStack.length > 0 && routeId !== 'dashboard') {
            backBtn.classList.remove('hidden');
            backBtn.classList.add('flex');
          } else {
            backBtn.classList.add('hidden');
            backBtn.classList.remove('flex');
          }
        }

        window.scrollTo({ top: 0, behavior: 'smooth' });
        this.renderCurrent();
      },

      goBack() {
        if (this.historyStack.length > 0) {
          const prev = this.historyStack.pop();
          this.navigate(prev, false);
        } else {
          this.navigate('dashboard', false);
        }
      },

      showDeveloperModal() {
        UI.openModal({
          title: 'Profil Pengembang Aplikasi',
          bodyHtml: `
            <div class="text-center py-2">
              <div class="w-28 h-28 mx-auto mb-4 rounded-full p-1 bg-gradient-to-br from-navy-900 via-gold-500 to-navy-800 shadow-lg">
                <img src="https://iili.io/oK0I2gj.jpg" onerror="this.onerror=null;this.src='https://placehold.co/200x200/0f172a/fbbf24?text=Eti';" alt="Eti, S.Pd.SD" class="w-full h-full rounded-full object-cover bg-white" />
              </div>
              <div class="inline-block px-3 py-0.5 rounded-full bg-gold-100 text-gold-800 text-[11px] font-bold uppercase tracking-wider mb-1">
                Pengembang Aplikasi
              </div>
              <h4 class="text-xl font-extrabold text-navy-900">Eti, S.Pd.SD</h4>
              <p class="text-xs text-slate-500 mt-1">Inisiator & Pengembang <strong>KEPALA SEKOLAH MASTER AI</strong></p>
              <div class="mt-4 p-3.5 rounded-xl bg-navy-50 border border-navy-100 text-xs text-navy-900 italic">
                "Administrasi Lebih Mudah, Kepemimpinan Lebih Terarah, Sekolah Semakin Berkualitas."
              </div>
            </div>
          `,
          footerHtml: `
            <button onclick="UI.closeModal()" class="px-5 py-2 bg-navy-900 hover:bg-navy-800 text-gold-400 rounded-xl text-xs font-bold">Tutup</button>
          `
        });
      },

      renderCurrent() {
        const container = document.getElementById('app-content');
        try {
          switch (this.currentRoute) {
            case 'dashboard': Modules.Dashboard.render(container); break;
            case 'generator-ai': Modules.GeneratorAI.render(container); break;
            case 'chatbot-ai': Modules.ChatbotAI.render(container); break;
            case 'admin-umum': Modules.AdminUmum.render(container); break;
            case 'perencanaan': Modules.Perencanaan.render(container); break;
            case 'supervisi': Modules.Supervisi.render(container); break;
            case 'gtk': Modules.GTK.render(container); break;
            case 'siswa': Modules.Siswa.render(container); break;
            case 'sarpras': Modules.Sarpras.render(container); break;
            case 'kurikulum': Modules.Kurikulum.render(container); break;
            case 'kepemimpinan': Modules.Kepemimpinan.render(container); break;
            case 'monev': Modules.Monev.render(container); break;
            case 'template-center': Modules.TemplateCenter.render(container); break;
            case 'pusat-laporan': Modules.PusatLaporan.render(container); break;
            case 'arsip-dokumen': Modules.ArsipDokumen.render(container); break;
            case 'pengaturan': Modules.Pengaturan.render(container); break;
            default: Modules.Dashboard.render(container);
          }
        } catch (err) {
          console.error('Render error:', err);
          container.innerHTML = `
            <div class="bg-rose-50 border border-rose-200 rounded-2xl p-6 text-rose-900">
              <h3 class="font-bold text-lg mb-2"><i class="fa-solid fa-triangle-exclamation mr-2"></i>Terjadi Kendala Tampilan</h3>
              <p class="text-sm mb-4">${ExportUtils.escape(err.message)}</p>
              <button onclick="App.navigate('dashboard')" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Kembali ke Dashboard</button>
            </div>
          `;
        }
      }
    };

    /**
     * 6. ALL APPLICATION MODULES
     */
    const Modules = {

      /* -------------------------------------------------------------------
         MODUL 2: DASHBOARD UTAMA
      ------------------------------------------------------------------- */
      Dashboard: {
        render(container) {
          const s = DB.data.settings;
          const totalGtk = DB.data.gtk.length;
          const totalSiswa = DB.data.siswa.rombel.reduce((acc, r) => acc + Number(r.lakiLaki || 0) + Number(r.perempuan || 0), 0);
          const totalProgram = DB.data.adminUmum.programKerja.length;
          const totalDokumen = DB.data.arsipDokumen.length + DB.data.adminUmum.inventarisDokumen.length;
          const avgProgres = totalProgram > 0
            ? Math.round(DB.data.adminUmum.programKerja.reduce((acc, p) => acc + Number(p.progres || 0), 0) / totalProgram)
            : 0;

          const pendingTasks = [
            ...DB.data.adminUmum.programKerja.filter(p => p.status !== 'Selesai').map(p => ({
              title: p.kegiatan,
              kategori: 'Program Kerja Kepsek',
              status: `${p.status} (${p.progres}%)`,
              route: 'admin-umum'
            })),
            ...DB.data.perencanaan.raporPendidikan.aiRecommendations.filter(r => !r.disetujui).map(r => ({
              title: `Tinjau Rekomendasi AI: ${r.indikator}`,
              kategori: 'Rapor Pendidikan & RKAS',
              status: 'Menunggu Persetujuan',
              route: 'perencanaan'
            }))
          ];

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              <!-- Hero Identity Banner -->
              <div class="bg-gradient-to-r from-navy-950 via-navy-900 to-navy-800 rounded-3xl p-6 sm:p-8 text-white shadow-xl border border-gold-500/30 relative overflow-hidden">
                <div class="relative z-10 flex flex-col lg:flex-row lg:items-center lg:justify-between gap-6">
                  <div class="flex items-start sm:items-center gap-4">
                    <div class="w-16 h-16 sm:w-20 sm:h-20 rounded-2xl bg-white/10 border border-gold-400/50 flex items-center justify-center shrink-0 overflow-hidden shadow-lg">
                      ${s.logoDataUrl
                        ? `<img src="${s.logoDataUrl}" alt="Logo Sekolah" class="w-full h-full object-contain p-1.5 bg-white" />`
                        : `<i class="fa-solid fa-school text-3xl text-gold-400"></i>`}
                    </div>
                    <div>
                      <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-gold-500/20 border border-gold-400/40 text-gold-300 text-xs font-semibold mb-2">
                        <i class="fa-solid fa-calendar-check"></i>
                        <span>Tahun Pelajaran ${ExportUtils.escape(s.academicYear)} • ${ExportUtils.escape(s.semester)}</span>
                      </div>
                      <h1 class="text-xl sm:text-3xl font-extrabold tracking-tight text-white">${ExportUtils.escape(s.schoolName)}</h1>
                      <p class="text-xs sm:text-sm text-slate-300 mt-1">
                        NPSN: <strong class="text-gold-300">${ExportUtils.escape(s.npsn)}</strong> • Kepala Sekolah: <strong class="text-white">${ExportUtils.escape(s.principalName)}</strong>
                      </p>
                      <p class="text-xs text-slate-400 mt-0.5"><i class="fa-solid fa-location-dot text-gold-400 mr-1"></i>${ExportUtils.escape(s.address)}</p>
                    </div>
                  </div>

                  <div class="flex flex-wrap gap-2.5 shrink-0">
                    <button onclick="App.navigate('pengaturan')" class="px-4 py-2.5 rounded-xl bg-white/10 hover:bg-white/20 text-white text-xs font-semibold border border-white/20 transition flex items-center gap-2">
                      <i class="fa-solid fa-pen-to-square text-gold-400"></i> Edit Profil Sekolah
                    </button>
                    <button onclick="App.navigate('generator-ai')" class="px-4 py-2.5 rounded-xl bg-gold-500 hover:bg-gold-600 text-navy-950 text-xs font-extrabold shadow-lg transition flex items-center gap-2">
                      <i class="fa-solid fa-wand-magic-sparkles"></i> Generator Dokumen AI
                    </button>
                  </div>
                </div>
              </div>

              <!-- 4 Key Metric Cards -->
              <div class="grid grid-cols-1 sm:grid-cols-2 xl:grid-cols-4 gap-4">
                <div onclick="App.navigate('gtk')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition group">
                  <div class="flex items-center justify-between">
                    <div>
                      <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Guru & Tenaga Kependidikan</p>
                      <h3 class="text-3xl font-extrabold text-navy-900 mt-1">${totalGtk} <span class="text-sm font-medium text-slate-500">Orang</span></h3>
                    </div>
                    <div class="w-12 h-12 rounded-2xl bg-navy-50 text-navy-800 group-hover:bg-navy-900 group-hover:text-gold-400 flex items-center justify-center transition">
                      <i class="fa-solid fa-chalkboard-user text-xl"></i>
                    </div>
                  </div>
                  <div class="mt-3 text-xs text-emerald-700 font-semibold flex items-center gap-1">
                    <i class="fa-solid fa-arrow-up-right-from-square"></i> Kelola Database GTK & Kinerja
                  </div>
                </div>

                <div onclick="App.navigate('siswa')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition group">
                  <div class="flex items-center justify-between">
                    <div>
                      <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Jumlah Peserta Didik</p>
                      <h3 class="text-3xl font-extrabold text-navy-900 mt-1">${totalSiswa} <span class="text-sm font-medium text-slate-500">Siswa</span></h3>
                    </div>
                    <div class="w-12 h-12 rounded-2xl bg-gold-50 text-gold-700 group-hover:bg-gold-500 group-hover:text-navy-950 flex items-center justify-center transition">
                      <i class="fa-solid fa-users text-xl"></i>
                    </div>
                  </div>
                  <div class="mt-3 text-xs text-navy-700 font-semibold flex items-center gap-1">
                    <i class="fa-solid fa-layer-group"></i> ${DB.data.siswa.rombel.length} Tingkat Rombongan Belajar
                  </div>
                </div>

                <div onclick="App.navigate('admin-umum')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition group">
                  <div class="flex items-center justify-between">
                    <div>
                      <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Program Kerja Sekolah</p>
                      <h3 class="text-3xl font-extrabold text-navy-900 mt-1">${totalProgram} <span class="text-sm font-medium text-slate-500">Program</span></h3>
                    </div>
                    <div class="w-12 h-12 rounded-2xl bg-emerald-50 text-emerald-700 group-hover:bg-emerald-700 group-hover:text-white flex items-center justify-center transition">
                      <i class="fa-solid fa-list-check text-xl"></i>
                    </div>
                  </div>
                  <div class="mt-3 text-xs text-emerald-700 font-semibold">
                    Rata-rata Progres Pelaksanaan: ${avgProgres}%
                  </div>
                </div>

                <div onclick="App.navigate('arsip-dokumen')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition group">
                  <div class="flex items-center justify-between">
                    <div>
                      <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Dokumen Administrasi</p>
                      <h3 class="text-3xl font-extrabold text-navy-900 mt-1">${totalDokumen} <span class="text-sm font-medium text-slate-500">Arsip</span></h3>
                    </div>
                    <div class="w-12 h-12 rounded-2xl bg-amber-50 text-amber-700 group-hover:bg-navy-900 group-hover:text-gold-400 flex items-center justify-center transition">
                      <i class="fa-solid fa-folder-tree text-xl"></i>
                    </div>
                  </div>
                  <div class="mt-3 text-xs text-amber-700 font-semibold">
                    Tersimpan rapi & siap cetak/unduh
                  </div>
                </div>
              </div>

              <!-- Analytics Chart + Program Progress -->
              <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <div class="lg:col-span-2 bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 mb-4">
                    <div>
                      <h3 class="text-base font-extrabold text-navy-900">Grafik Capaian Mutu & Rapor Pendidikan Sekolah</h3>
                      <p class="text-xs text-slate-500">Visualisasi indikator utama mutu sekolah berdasarkan data terkini</p>
                    </div>
                    <button onclick="App.navigate('perencanaan')" class="text-xs font-bold text-navy-800 hover:text-gold-700 bg-navy-50 px-3 py-1.5 rounded-lg">
                      Analisis AI Rapor <i class="fa-solid fa-arrow-right ml-1"></i>
                    </button>
                  </div>
                  <div class="h-64 w-full">
                    <canvas id="dashboard-rapor-chart"></canvas>
                  </div>
                </div>

                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm flex flex-col justify-between">
                  <div>
                    <h3 class="text-base font-extrabold text-navy-900 mb-1">Progres Pelaksanaan Program</h3>
                    <p class="text-xs text-slate-500 mb-4">Capaian program kerja utama kepala sekolah</p>
                    <div class="space-y-4">
                      ${DB.data.adminUmum.programKerja.slice(0, 4).map(p => `
                        <div>
                          <div class="flex justify-between text-xs font-semibold mb-1">
                            <span class="text-navy-900 truncate pr-2">${ExportUtils.escape(p.kegiatan)}</span>
                            <span class="text-gold-700 shrink-0">${p.progres}%</span>
                          </div>
                          <div class="w-full bg-slate-100 rounded-full h-2.5 overflow-hidden">
                            <div class="bg-gradient-to-r from-navy-800 to-gold-500 h-2.5 rounded-full" style="width:${Math.min(100, Math.max(0, p.progres))}%"></div>
                          </div>
                        </div>
                      `).join('')}
                    </div>
                  </div>
                  <button onclick="App.navigate('monev')" class="mt-5 w-full py-2.5 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold transition">
                    Lihat Monitoring & Evaluasi Lengkap
                  </button>
                </div>
              </div>

              <!-- Agenda Terdekat & Notifikasi Tugas Administrasi -->
              <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <div class="flex items-center justify-between mb-4">
                    <h3 class="text-base font-extrabold text-navy-900 flex items-center gap-2">
                      <i class="fa-solid fa-calendar-days text-gold-600"></i> Agenda Kegiatan Terdekat
                    </h3>
                    <button onclick="App.navigate('admin-umum')" class="text-xs font-semibold text-navy-800 hover:underline">Kelola Agenda</button>
                  </div>
                  <div class="space-y-3">
                    ${DB.data.adminUmum.agendaHarian.slice(0, 4).map(ag => `
                      <div class="p-3.5 rounded-xl bg-slate-50 border border-slate-200/80 flex items-start gap-3">
                        <div class="px-2.5 py-1.5 rounded-lg bg-navy-900 text-gold-400 text-center shrink-0">
                          <div class="text-[10px] font-bold uppercase">${ExportUtils.escape(ag.tanggal)}</div>
                          <div class="text-[11px] font-extrabold text-white">${ExportUtils.escape(ag.waktu)}</div>
                        </div>
                        <div class="min-w-0">
                          <div class="text-sm font-bold text-navy-900">${ExportUtils.escape(ag.kegiatan)}</div>
                          <div class="text-xs text-slate-500 mt-0.5"><i class="fa-solid fa-location-pin mr-1 text-gold-600"></i>${ExportUtils.escape(ag.tempat)}</div>
                        </div>
                      </div>
                    `).join('')}
                  </div>
                </div>

                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <div class="flex items-center justify-between mb-4">
                    <h3 class="text-base font-extrabold text-navy-900 flex items-center gap-2">
                      <i class="fa-solid fa-bell text-amber-500"></i> Notifikasi Tugas Administrasi Belum Selesai
                    </h3>
                    <span class="px-2.5 py-0.5 rounded-full bg-amber-100 text-amber-800 text-xs font-bold">${pendingTasks.length} Tugas</span>
                  </div>
                  <div class="space-y-3">
                    ${pendingTasks.length === 0
                      ? `<p class="text-sm text-emerald-700 bg-emerald-50 p-4 rounded-xl">Seluruh tugas administrasi utama telah selesai!</p>`
                      : pendingTasks.slice(0, 4).map(t => `
                        <div onclick="App.navigate('${t.route}')" class="cursor-pointer p-3.5 rounded-xl bg-amber-50/70 hover:bg-amber-100/80 border border-amber-200 flex items-center justify-between gap-3 transition">
                          <div class="min-w-0">
                            <div class="text-xs font-bold text-amber-800 uppercase">${ExportUtils.escape(t.kategori)}</div>
                            <div class="text-sm font-bold text-navy-900 truncate">${ExportUtils.escape(t.title)}</div>
                          </div>
                          <span class="px-2.5 py-1 rounded-lg bg-white text-amber-800 border border-amber-300 text-[11px] font-bold shrink-0">${ExportUtils.escape(t.status)}</span>
                        </div>
                      `).join('')}
                  </div>
                </div>
              </div>
            </div>
          `;

          this.initChart();
        },

        initChart() {
          const ctx = document.getElementById('dashboard-rapor-chart');
          if (!ctx || typeof Chart === 'undefined') return;
          if (App.charts.dashboard) {
            App.charts.dashboard.destroy();
          }
          const rp = DB.data.perencanaan.raporPendidikan;
          App.charts.dashboard = new Chart(ctx, {
            type: 'bar',
            data: {
              labels: ['Literasi', 'Numerasi', 'Karakter', 'Iklim Keamanan', 'Kebinekaan', 'Kualitas Pembelajaran'],
              datasets: [{
                label: `Capaian Rapor Pendidikan (${rp.tahun})`,
                data: [rp.literasi, rp.numerasi, rp.karakter, rp.iklimKeamanan, rp.iklimKebinekaan, rp.kualitasPembelajaran],
                backgroundColor: ['#1e3a8a', '#f59e0b', '#0f172a', '#d97706', '#334e68', '#fbbf24'],
                borderRadius: 8
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false,
              scales: {
                y: { beginAtZero: true, max: 100 }
              },
              plugins: {
                legend: { display: false }
              }
            }
          });
        }
      },

      /* -------------------------------------------------------------------
         MODUL 3: ADMINISTRASI UMUM KEPALA SEKOLAH (13 BUKU LENGKAP)
      ------------------------------------------------------------------- */
      AdminUmum: {
        activeTab: 'programKerja',
        searchQuery: '',
        editId: null,

        tabs: [
          { key: 'programKerja', label: '1. Program Kerja Kepsek', fields: [
            { name: 'bidang', label: 'Bidang Garapan', type: 'text', placeholder: 'Contoh: Manajerial & Supervisi' },
            { name: 'kegiatan', label: 'Uraian Program / Kegiatan', type: 'text', placeholder: 'Nama program kerja...' },
            { name: 'target', label: 'Target Capaian', type: 'text', placeholder: 'Indikator keberhasilan...' },
            { name: 'waktu', label: 'Waktu Pelaksanaan', type: 'text', placeholder: 'Contoh: Semester 1 / Juli 2026' },
            { name: 'penanggungJawab', label: 'Penanggung Jawab', type: 'text', placeholder: 'Nama / Jabatan' },
            { name: 'status', label: 'Status', type: 'select', options: ['Belum Mulai', 'Berjalan', 'Selesai'] },
            { name: 'progres', label: 'Progres (%)', type: 'number', placeholder: '0 - 100' }
          ]},
          { key: 'rktRingkas', label: '2. Rencana Kerja Tahunan', fields: [
            { name: 'standar', label: 'Standar Nasional Pendidikan', type: 'text', placeholder: 'Contoh: Standar Proses' },
            { name: 'program', label: 'Nama Program RKT', type: 'text', placeholder: 'Uraian program tahunan...' },
            { name: 'indikator', label: 'Indikator Keberhasilan', type: 'text', placeholder: 'Output terukur...' },
            { name: 'jadwal', label: 'Jadwal', type: 'text', placeholder: 'Bulan pelaksanaan' },
            { name: 'biaya', label: 'Estimasi Biaya (Rp)', type: 'number', placeholder: '0' },
            { name: 'status', label: 'Status', type: 'select', options: ['Direncanakan', 'Berjalan', 'Selesai'] }
          ]},
          { key: 'agendaHarian', label: '3. Agenda Harian Kepsek', fields: [
            { name: 'tanggal', label: 'Tanggal', type: 'date' },
            { name: 'waktu', label: 'Pukul / Waktu', type: 'text', placeholder: '08:00 - 10:00' },
            { name: 'kegiatan', label: 'Uraian Kegiatan', type: 'text', placeholder: 'Kegiatan harian...' },
            { name: 'tempat', label: 'Tempat', type: 'text', placeholder: 'Lokasi kegiatan' },
            { name: 'keterangan', label: 'Keterangan / Hasil', type: 'text', placeholder: 'Catatan ringkas' }
          ]},
          { key: 'agendaKegiatan', label: '4. Buku Agenda Kegiatan', fields: [
            { name: 'tanggal', label: 'Tanggal', type: 'date' },
            { name: 'namaKegiatan', label: 'Nama Kegiatan Sekolah', type: 'text', placeholder: 'Contoh: Peringatan Hari Guru' },
            { name: 'penyelenggara', label: 'Penyelenggara', type: 'text', placeholder: 'Panitia / OSIS / Sekolah' },
            { name: 'sasaran', label: 'Sasaran Peserta', type: 'text', placeholder: 'Guru / Siswa / Orang Tua' },
            { name: 'hasil', label: 'Hasil / Evaluasi', type: 'text', placeholder: 'Ringkasan keterlaksanaan' }
          ]},
          { key: 'tamuUmum', label: '5. Buku Tamu Umum', fields: [
            { name: 'tanggal', label: 'Tanggal Kunjungan', type: 'date' },
            { name: 'nama', label: 'Nama Lengkap Tamu', type: 'text', placeholder: 'Nama beserta gelar' },
            { name: 'instansi', label: 'Instansi / Asal', type: 'text', placeholder: 'Komite / Orang Tua / Instansi' },
            { name: 'keperluan', label: 'Maksud & Keperluan', type: 'text', placeholder: 'Tujuan kunjungan' },
            { name: 'tindakLanjut', label: 'Tindak Lanjut', type: 'text', placeholder: 'Hasil pertemuan' }
          ]},
          { key: 'tamuPembinaan', label: '6. Buku Tamu Pembinaan', fields: [
            { name: 'tanggal', label: 'Tanggal Pembinaan', type: 'date' },
            { name: 'nama', label: 'Nama Pejabat / Pengawas', type: 'text', placeholder: 'Nama pengawas/pejabat pembina' },
            { name: 'jabatan', label: 'Jabatan', type: 'text', placeholder: 'Pengawas Pembina / Kabid' },
            { name: 'materiPembinaan', label: 'Materi Pembinaan', type: 'text', placeholder: 'Fokus supervisi/pembinaan' },
            { name: 'saran', label: 'Saran & Rekomendasi', type: 'text', placeholder: 'Arahan untuk sekolah' }
          ]},
          { key: 'notulenRapat', label: '7. Buku Notulen Rapat', fields: [
            { name: 'tanggal', label: 'Tanggal Rapat', type: 'date' },
            { name: 'topik', label: 'Agenda / Topik Rapat', type: 'text', placeholder: 'Pokok bahasan rapat' },
            { name: 'pimpinan', label: 'Pimpinan Rapat', type: 'text', placeholder: 'Nama pimpinan rapat' },
            { name: 'peserta', label: 'Jumlah / Unsur Peserta', type: 'text', placeholder: 'Contoh: 30 Guru & Tendik' },
            { name: 'keputusan', label: 'Hasil & Keputusan Rapat', type: 'textarea', placeholder: 'Poin-poin keputusan rapat...' }
          ]},
          { key: 'suratMasuk', label: '8. Buku Surat Masuk', fields: [
            { name: 'nomorSurat', label: 'Nomor Surat', type: 'text', placeholder: 'Isi sesuai surat asli' },
            { name: 'tanggalSurat', label: 'Tanggal Surat', type: 'date' },
            { name: 'tanggalTerima', label: 'Tanggal Diterima', type: 'date' },
            { name: 'pengirim', label: 'Pengirim / Instansi', type: 'text', placeholder: 'Dinas Pendidikan / Instansi lain' },
            { name: 'perihal', label: 'Perihal Surat', type: 'text', placeholder: 'Ringkasan isi surat' },
            { name: 'disposisi', label: 'Disposisi Kepala Sekolah', type: 'text', placeholder: 'Tujuan disposisi & arahan' }
          ]},
          { key: 'suratKeluar', label: '9. Buku Surat Keluar', fields: [
            { name: 'nomorSurat', label: 'Nomor Surat Keluar', type: 'text', placeholder: 'Nomor agenda surat sekolah' },
            { name: 'tanggal', label: 'Tanggal Surat', type: 'date' },
            { name: 'tujuan', label: 'Tujuan / Penerima', type: 'text', placeholder: 'Instansi / Orang Tua' },
            { name: 'perihal', label: 'Perihal', type: 'text', placeholder: 'Isi pokok surat keluar' },
            { name: 'keterangan', label: 'Keterangan', type: 'text', placeholder: 'Catatan pengiriman' }
          ]},
          { key: 'ekspedisiSurat', label: '10. Buku Ekspedisi Surat', fields: [
            { name: 'tanggal', label: 'Tanggal Kirim', type: 'date' },
            { name: 'nomorSurat', label: 'Nomor Surat', type: 'text', placeholder: 'Nomor surat yang dikirim' },
            { name: 'tujuan', label: 'Alamat / Tujuan Pengiriman', type: 'text', placeholder: 'Tujuan pengiriman' },
            { name: 'penerima', label: 'Nama Penerima / Kurir', type: 'text', placeholder: 'Petugas penerima' },
            { name: 'paraf', label: 'Status / Paraf', type: 'text', placeholder: 'Terkirim / Diterima' }
          ]},
          { key: 'inventarisDokumen', label: '11. Inventaris Dokumen', fields: [
            { name: 'kodeDok', label: 'Kode Dokumen', type: 'text', placeholder: 'DOK-01' },
            { name: 'namaDokumen', label: 'Nama Dokumen Administrasi', type: 'text', placeholder: 'Contoh: KSP / RKAS / SK' },
            { name: 'kategori', label: 'Kategori', type: 'text', placeholder: 'Kurikulum / Keuangan / Umum' },
            { name: 'lokasiSimpan', label: 'Lokasi Penyimpanan', type: 'text', placeholder: 'Lemari Arsip / Cloud' },
            { name: 'kondisi', label: 'Kondisi / Kelengkapan', type: 'text', placeholder: 'Lengkap & Disahkan' }
          ]},
          { key: 'kegiatanKepsek', label: '12. Buku Kegiatan Kepsek', fields: [
            { name: 'tanggal', label: 'Tanggal', type: 'date' },
            { name: 'jenisKegiatan', label: 'Jenis Tugas Kepemimpinan', type: 'text', placeholder: 'Manajerial / Supervisi / Kewirausahaan' },
            { name: 'uraian', label: 'Uraian Pelaksanaan', type: 'textarea', placeholder: 'Deskripsi aktivitas kepala sekolah...' },
            { name: 'output', label: 'Output / Bukti Fisik', type: 'text', placeholder: 'Dokumen / Notulen / Instrumen' }
          ]},
          { key: 'dokumentasiKegiatan', label: '13. Dokumentasi Kegiatan', fields: [
            { name: 'tanggal', label: 'Tanggal Kegiatan', type: 'date' },
            { name: 'judul', label: 'Judul Dokumentasi Kegiatan', type: 'text', placeholder: 'Nama acara/kegiatan' },
            { name: 'deskripsi', label: 'Deskripsi Singkat Kegiatan', type: 'textarea', placeholder: 'Narasi dokumentasi kegiatan...' },
            { name: 'fotoUrl', label: 'Tautan Foto (Opsional)', type: 'text', placeholder: 'https://...' }
          ]}
        ],

        render(container) {
          const currentTabObj = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          const rawList = DB.data.adminUmum[currentTabObj.key] || [];
          const q = this.searchQuery.toLowerCase();
          const filtered = rawList.filter(item =>
            Object.values(item).some(v => String(v).toLowerCase().includes(q))
          );
          const editItem = this.editId ? rawList.find(x => x.id === this.editId) : null;

          container.innerHTML = `
            <div class="animate-fade-in">
              ${UI.moduleHeader(
                'Administrasi Umum Kepala Sekolah',
                'Pengelolaan 13 buku administrasi resmi kepala sekolah yang terstruktur, dapat dicari, dicetak, dan diekspor.',
                'fa-solid fa-folder-open',
                `
                  <button onclick="Modules.AdminUmum.printCurrentTab()" class="px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-print text-navy-700"></i> Cetak Buku
                  </button>
                  <button onclick="Modules.AdminUmum.exportWordCurrentTab()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-file-word text-gold-400"></i> Unduh Word
                  </button>
                `
              )}

              ${UI.guideBanner(
                '13 Buku Administrasi Kepala Sekolah',
                'Pilih jenis buku administrasi pada tombol tab di bawah ini. Gunakan tombol "Isi Contoh" untuk melihat format pengisian cepat, lalu klik "Simpan Data". Seluruh data dapat dicetak dengan kop surat resmi atau diunduh ke Microsoft Word.'
              )}

              <!-- Sub-module Tabs -->
              <div class="flex flex-wrap gap-1.5 mb-6 bg-white p-2.5 rounded-2xl border border-slate-200 shadow-sm">
                ${this.tabs.map(t => `
                  <button onclick="Modules.AdminUmum.switchTab('${t.key}')" class="px-3 py-2 rounded-xl text-xs font-semibold transition ${
                    this.activeTab === t.key
                      ? 'bg-navy-900 text-gold-400 shadow-sm'
                      : 'bg-slate-50 text-slate-600 hover:bg-slate-100'
                  }">
                    ${t.label}
                  </button>
                `).join('')}
              </div>

              <!-- Input Form Card -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm mb-6">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 mb-4 pb-3 border-b border-slate-100">
                  <h3 class="text-base font-extrabold text-navy-900">
                    ${editItem ? 'Edit Data:' : 'Tambah Data Baru:'} <span class="text-gold-700">${currentTabObj.label}</span>
                  </h3>
                  <button type="button" onclick="Modules.AdminUmum.fillSample()" class="text-xs font-bold text-navy-800 bg-gold-100 hover:bg-gold-200 px-3 py-1.5 rounded-lg transition self-start">
                    <i class="fa-solid fa-magic mr-1"></i> Isi Contoh Pengisian
                  </button>
                </div>

                <form id="admin-umum-form" onsubmit="Modules.AdminUmum.saveEntry(event)" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                  ${currentTabObj.fields.map(f => {
                    const val = editItem ? (editItem[f.name] ?? '') : '';
                    if (f.type === 'textarea') {
                      return `
                        <div class="md:col-span-2">
                          <label class="block text-xs font-bold text-slate-700 mb-1">${f.label}</label>
                          <textarea name="${f.name}" rows="3" required placeholder="${f.placeholder || ''}" class="w-full border border-slate-300 focus:border-navy-800 rounded-xl px-3.5 py-2.5 text-sm outline-none">${ExportUtils.escape(val)}</textarea>
                        </div>
                      `;
                    }
                    if (f.type === 'select') {
                      return `
                        <div>
                          <label class="block text-xs font-bold text-slate-700 mb-1">${f.label}</label>
                          <select name="${f.name}" class="w-full border border-slate-300 focus:border-navy-800 rounded-xl px-3.5 py-2.5 text-sm outline-none bg-white">
                            ${f.options.map(opt => `<option value="${opt}" ${val === opt ? 'selected' : ''}>${opt}</option>`).join('')}
                          </select>
                        </div>
                      `;
                    }
                    return `
                      <div>
                        <label class="block text-xs font-bold text-slate-700 mb-1">${f.label}</label>
                        <input type="${f.type}" name="${f.name}" value="${ExportUtils.escape(val)}" required placeholder="${f.placeholder || ''}" class="w-full border border-slate-300 focus:border-navy-800 rounded-xl px-3.5 py-2.5 text-sm outline-none" />
                      </div>
                    `;
                  }).join('')}

                  <div class="md:col-span-2 flex flex-wrap items-center gap-2.5 pt-2">
                    <button type="submit" class="px-5 py-2.5 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow transition flex items-center gap-2">
                      <i class="fa-solid fa-floppy-disk text-gold-400"></i> ${editItem ? 'Perbarui Data' : 'Simpan Data'}
                    </button>
                    ${editItem ? `
                      <button type="button" onclick="Modules.AdminUmum.cancelEdit()" class="px-4 py-2.5 bg-slate-200 hover:bg-slate-300 text-slate-800 rounded-xl text-xs font-semibold">
                        Batal Edit
                      </button>
                    ` : ''}
                  </div>
                </form>
              </div>

              <!-- Data Table Card -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-4">
                  <h4 class="text-sm font-extrabold text-navy-900">Daftar Rekaman (${filtered.length} Data)</h4>
                  <div class="relative w-full sm:w-72">
                    <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-3 text-slate-400 text-xs"></i>
                    <input type="text" value="${ExportUtils.escape(this.searchQuery)}" oninput="Modules.AdminUmum.onSearch(this.value)" placeholder="Cari data pada buku ini..." class="w-full pl-9 pr-3.5 py-2 border border-slate-300 rounded-xl text-xs outline-none focus:border-navy-800" />
                  </div>
                </div>

                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs sm:text-sm">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3 rounded-tl-xl">No</th>
                        ${currentTabObj.fields.map(f => `<th class="p-3">${f.label}</th>`).join('')}
                        <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${filtered.length === 0
                        ? `<tr><td colspan="${currentTabObj.fields.length + 2}" class="p-6 text-center text-slate-400">Belum ada data atau tidak ditemukan hasil pencarian.</td></tr>`
                        : filtered.map((item, idx) => `
                          <tr class="hover:bg-slate-50">
                            <td class="p-3 font-bold text-slate-500">${idx + 1}</td>
                            ${currentTabObj.fields.map(f => {
                              let v = item[f.name] ?? '-';
                              if (f.name === 'biaya') v = ExportUtils.formatRupiah(v);
                              if (f.name === 'progres') v = `${v}%`;
                              return `<td class="p-3 whitespace-pre-line max-w-xs">${ExportUtils.escape(v)}</td>`;
                            }).join('')}
                            <td class="p-3 text-center whitespace-nowrap">
                              <button onclick="Modules.AdminUmum.startEdit('${item.id}')" title="Edit" class="p-2 text-navy-700 hover:bg-navy-100 rounded-lg mr-1">
                                <i class="fa-solid fa-pen-to-square"></i>
                              </button>
                              <button onclick="Modules.AdminUmum.deleteEntry('${item.id}')" title="Hapus" class="p-2 text-rose-600 hover:bg-rose-50 rounded-lg">
                                <i class="fa-solid fa-trash"></i>
                              </button>
                            </td>
                          </tr>
                        `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        switchTab(key) {
          this.activeTab = key;
          this.editId = null;
          this.searchQuery = '';
          this.render(document.getElementById('app-content'));
        },

        onSearch(val) {
          this.searchQuery = val;
          this.render(document.getElementById('app-content'));
        },

        fillSample() {
          const form = document.getElementById('admin-umum-form');
          if (!form) return;
          const today = new Date().toISOString().split('T')[0];
          const samples = {
            bidang: 'Supervisi & Pengembangan Mutu',
            kegiatan: 'Pendampingan Penyusunan Asesmen Formatif Berbasis HOTS',
            target: 'Seluruh guru menerapkan asesmen formatif terstruktur',
            waktu: 'November 2026',
            penanggungJawab: 'Kepala Sekolah & Waka Kurikulum',
            status: 'Berjalan',
            progres: '65',
            standar: 'Standar Penilaian Pendidikan',
            program: 'Optimalisasi Asesmen Diagnostik Awal Semester',
            indikator: '100% mata pelajaran memiliki pemetaan kesiapan belajar siswa',
            jadwal: 'Januari 2027',
            biaya: '3500000',
            tanggal: today,
            tanggalSurat: today,
            tanggalTerima: today,
            tempat: 'Aula Utama Sekolah',
            keterangan: 'Dilaksanakan sesuai jadwal kalender sekolah',
            namaKegiatan: 'Gelar Karya Projek Penguatan Profil Pelajar',
            penyelenggara: 'Tim Fasilitator P5 & OSIS',
            sasaran: 'Siswa Kelas VII, VIII, IX & Orang Tua',
            hasil: 'Berjalan lancar dan mendapat apresiasi orang tua',
            nama: 'H. Rahmat Hidayat, M.Pd.',
            instansi: 'Dinas Pendidikan Kota',
            keperluan: 'Monitoring Kesiapan Asesmen Akhir Semester',
            tindakLanjut: 'Berkas kesiapan ujian telah diverifikasi',
            jabatan: 'Pengawas Pembina',
            materiPembinaan: 'Penguatan Komunitas Belajar & Refleksi Pembelajaran',
            saran: 'Dokumentasikan praktik baik guru setiap bulan',
            topik: 'Rapat Koordinasi Persiapan Pembagian Rapor Semester 1',
            pimpinan: DB.data.settings.principalName,
            peserta: '32 Guru dan Staf Tata Usaha',
            keputusan: '1. Pengolahan nilai selesai H-3 pembagian rapor.\n2. Undangan orang tua disampaikan secara resmi.',
            nomorSurat: '421.3/105/SMPN1/XI/2026',
            pengirim: 'Dinas Pendidikan dan Kebudayaan',
            perihal: 'Penyampaian Kalender Akademik Semester Genap',
            disposisi: 'Waka Kurikulum untuk disesuaikan dengan jadwal sekolah',
            tujuan: 'Kepala Dinas Pendidikan dan Kebudayaan',
            penerima: 'Bagian Umum Sekretariat',
            paraf: 'Diterima & Ditandatangani',
            kodeDok: 'DOK-SUP-04',
            namaDokumen: 'Laporan Rekapitulasi Supervisi Akademik Semester 1',
            kategori: 'Supervisi',
            lokasiSimpan: 'Lemari Arsip Kepala Sekolah Rak B2',
            kondisi: 'Lengkap & Sah',
            jenisKegiatan: 'Supervisi & Pembinaan GTK',
            uraian: 'Melakukan observasi pembelajaran dan diskusi umpan balik bersama guru mata pelajaran.',
            output: 'Lembar observasi dan rencana tindak lanjut guru',
            judul: 'Rapat Kerja Penyusunan Program Sekolah Sehat',
            deskripsi: 'Koordinasi lintas tim sekolah bersama Puskesmas untuk mewujudkan kantin sehat dan lingkungan bersih.',
            fotoUrl: ''
          };

          Array.from(form.elements).forEach(el => {
            if (el.name && samples[el.name] !== undefined) {
              el.value = samples[el.name];
            }
          });
          UI.toast('Contoh pengisian telah dimasukkan ke formulir.', 'info');
        },

        saveEntry(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          const form = e.target;
          const fd = new FormData(form);
          const obj = { id: this.editId || DB.uid('adm') };
          for (const [k, v] of fd.entries()) {
            obj[k] = (k === 'progres' || k === 'biaya') ? Number(v || 0) : v;
          }

          const list = DB.data.adminUmum[this.activeTab];
          if (this.editId) {
            const idx = list.findIndex(x => x.id === this.editId);
            if (idx !== -1) list[idx] = obj;
            this.editId = null;
          } else {
            list.unshift(obj);
          }
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        startEdit(id) {
          if (!UI.canEdit()) return;
          this.editId = id;
          this.render(document.getElementById('app-content'));
          window.scrollTo({ top: 180, behavior: 'smooth' });
        },

        cancelEdit() {
          this.editId = null;
          this.render(document.getElementById('app-content'));
        },

        deleteEntry(id) {
          if (!UI.canEdit()) return;
          UI.confirmAction('Hapus Data Administrasi', 'Apakah Anda yakin ingin menghapus catatan ini?', () => {
            DB.data.adminUmum[this.activeTab] = DB.data.adminUmum[this.activeTab].filter(x => x.id !== id);
            DB.save(true);
            this.render(document.getElementById('app-content'));
          });
        },

        buildPrintableTableHtml() {
          const tabObj = this.tabs.find(t => t.key === this.activeTab);
          const list = DB.data.adminUmum[this.activeTab] || [];
          return `
            <h2 style="text-align:center;text-transform:uppercase;margin-bottom:4px;">${ExportUtils.escape(tabObj.label)}</h2>
            <p style="text-align:center;margin-top:0;margin-bottom:16px;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <table style="width:100%;border-collapse:collapse;font-size:10.5pt;">
              <thead>
                <tr>
                  <th style="border:1px solid #000;padding:6px;">No</th>
                  ${tabObj.fields.map(f => `<th style="border:1px solid #000;padding:6px;">${ExportUtils.escape(f.label)}</th>`).join('')}
                </tr>
              </thead>
              <tbody>
                ${list.map((item, i) => `
                  <tr>
                    <td style="border:1px solid #000;padding:6px;">${i + 1}</td>
                    ${tabObj.fields.map(f => {
                      let val = item[f.name] ?? '-';
                      if (f.name === 'biaya') val = ExportUtils.formatRupiah(val);
                      if (f.name === 'progres') val = `${val}%`;
                      return `<td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(val)}</td>`;
                    }).join('')}
                  </tr>
                `).join('')}
              </tbody>
            </table>
          `;
        },

        printCurrentTab() {
          const tabObj = this.tabs.find(t => t.key === this.activeTab);
          ExportUtils.printDocument(tabObj.label, this.buildPrintableTableHtml(), true, true);
        },

        exportWordCurrentTab() {
          const tabObj = this.tabs.find(t => t.key === this.activeTab);
          ExportUtils.downloadWord(`Administrasi_${this.activeTab}`, this.buildPrintableTableHtml(), true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 4: PERENCANAAN, PENGANGGARAN (RKJM/RKT/RKAS) & RAPOR PENDIDIKAN AI
      ------------------------------------------------------------------- */
      Perencanaan: {
        activeSection: 'rapor', // 'rapor' | 'rkjm' | 'rkt' | 'rkas'
        isAnalyzing: false,

        render(container) {
          const rp = DB.data.perencanaan.raporPendidikan;
          const totalRkas = DB.data.perencanaan.rkas.reduce((a, b) => a + Number(b.anggaran || 0), 0);
          const totalRealisasi = DB.data.perencanaan.rkas.reduce((a, b) => a + Number(b.realisasi || 0), 0);

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Perencanaan & Penganggaran Sekolah',
                'Integrasi Rapor Pendidikan (Analisis AI), RKJM, RKT, dan Penyusunan Bahan RKAS / ARKAS.',
                'fa-solid fa-chart-line',
                `
                  <button onclick="Modules.Perencanaan.printSection()" class="px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-print mr-1"></i> Cetak Dokumen
                  </button>
                  <button onclick="Modules.Perencanaan.exportWordSection()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-file-word text-gold-400 mr-1"></i> Unduh Word
                  </button>
                `
              )}

              ${UI.guideBanner(
                'Perencanaan Berbasis Data (PBD) & Integrasi RKAS',
                'Masukkan capaian Rapor Pendidikan sekolah Anda, lalu klik "Analisis Rapor Pendidikan dengan AI". AI akan mengidentifikasi akar masalah, merekomendasikan kegiatan perbaikan, dan mengusulkan pembiayaan RKAS yang dapat Anda edit serta setujui langsung ke RKT & RKAS.'
              )}

              <!-- Section Navigation Pills -->
              <div class="grid grid-cols-2 lg:grid-cols-4 gap-3">
                <button onclick="Modules.Perencanaan.setSection('rapor')" class="p-4 rounded-2xl border text-left transition ${this.activeSection === 'rapor' ? 'bg-navy-900 text-white border-navy-900 shadow-md' : 'bg-white text-navy-900 border-slate-200 hover:border-navy-400'}">
                  <div class="text-xs font-bold ${this.activeSection === 'rapor' ? 'text-gold-400' : 'text-gold-700'}">LANGKAH 1 • ANALISIS AI</div>
                  <div class="font-extrabold text-sm mt-0.5">Rapor Pendidikan</div>
                </button>
                <button onclick="Modules.Perencanaan.setSection('rkjm')" class="p-4 rounded-2xl border text-left transition ${this.activeSection === 'rkjm' ? 'bg-navy-900 text-white border-navy-900 shadow-md' : 'bg-white text-navy-900 border-slate-200 hover:border-navy-400'}">
                  <div class="text-xs font-bold ${this.activeSection === 'rkjm' ? 'text-gold-400' : 'text-gold-700'}">JANGKA MENENGAH</div>
                  <div class="font-extrabold text-sm mt-0.5">RKJM (4 Tahunan)</div>
                </button>
                <button onclick="Modules.Perencanaan.setSection('rkt')" class="p-4 rounded-2xl border text-left transition ${this.activeSection === 'rkt' ? 'bg-navy-900 text-white border-navy-900 shadow-md' : 'bg-white text-navy-900 border-slate-200 hover:border-navy-400'}">
                  <div class="text-xs font-bold ${this.activeSection === 'rkt' ? 'text-gold-400' : 'text-gold-700'}">PROGRAM TAHUNAN</div>
                  <div class="font-extrabold text-sm mt-0.5">RKT (${DB.data.perencanaan.rkt.length} Program)</div>
                </button>
                <button onclick="Modules.Perencanaan.setSection('rkas')" class="p-4 rounded-2xl border text-left transition ${this.activeSection === 'rkas' ? 'bg-navy-900 text-white border-navy-900 shadow-md' : 'bg-white text-navy-900 border-slate-200 hover:border-navy-400'}">
                  <div class="text-xs font-bold ${this.activeSection === 'rkas' ? 'text-gold-400' : 'text-gold-700'}">BAHAN PENGANGGARAN</div>
                  <div class="font-extrabold text-sm mt-0.5">RKAS & Draft ARKAS</div>
                </button>
              </div>

              ${this.activeSection === 'rapor' ? this.renderRaporSection(rp) : ''}
              ${this.activeSection === 'rkjm' ? this.renderRkjmSection() : ''}
              ${this.activeSection === 'rkt' ? this.renderRktSection() : ''}
              ${this.activeSection === 'rkas' ? this.renderRkasSection(totalRkas, totalRealisasi) : ''}
            </div>
          `;
        },

        setSection(sec) {
          this.activeSection = sec;
          this.render(document.getElementById('app-content'));
        },

        renderRaporSection(rp) {
          return `
            <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-6">
              <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 border-b border-slate-100 pb-4">
                <div>
                  <h3 class="text-lg font-extrabold text-navy-900">Input Capaian Rapor Pendidikan Sekolah</h3>
                  <p class="text-xs text-slate-500">Masukkan skor (0 - 100) sesuai profil Rapor Pendidikan satuan pendidikan Anda</p>
                </div>
                <span class="px-3 py-1 rounded-full bg-navy-50 text-navy-800 text-xs font-bold border border-navy-200">
                  Perencanaan Berbasis Data (PBD)
                </span>
              </div>

              <form onsubmit="Modules.Perencanaan.saveAndAnalyzeRapor(event)" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1">Tahun Rapor Pendidikan</label>
                  <input type="text" id="rp-tahun" value="${ExportUtils.escape(rp.tahun)}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm" />
                </div>
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1">Kemampuan Literasi (0-100)</label>
                  <input type="number" step="0.1" min="0" max="100" id="rp-literasi" value="${rp.literasi}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm" />
                </div>
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1">Kemampuan Numerasi (0-100)</label>
                  <input type="number" step="0.1" min="0" max="100" id="rp-numerasi" value="${rp.numerasi}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm" />
                </div>
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1">Indeks Karakter (0-100)</label>
                  <input type="number" step="0.1" min="0" max="100" id="rp-karakter" value="${rp.karakter}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm" />
                </div>
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1">Iklim Keamanan Sekolah (0-100)</label>
                  <input type="number" step="0.1" min="0" max="100" id="rp-keamanan" value="${rp.iklimKeamanan}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm" />
                </div>
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1">Kualitas Pembelajaran (0-100)</label>
                  <input type="number" step="0.1" min="0" max="100" id="rp-kualitas" value="${rp.kualitasPembelajaran}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm" />
                </div>
                <div class="sm:col-span-2 lg:col-span-3">
                  <label class="block text-xs font-bold text-slate-700 mb-1">Catatan Konteks / Kondisi Nyata Sekolah</label>
                  <textarea id="rp-catatan" rows="2" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-sm">${ExportUtils.escape(rp.catatanTambahan)}</textarea>
                </div>
                <div class="sm:col-span-2 lg:col-span-3 flex flex-wrap gap-3">
                  <button type="submit" ${this.isAnalyzing ? 'disabled' : ''} class="px-5 py-3 bg-gradient-to-r from-navy-900 to-navy-800 hover:from-navy-800 hover:to-navy-700 text-white rounded-xl text-xs font-extrabold shadow-md flex items-center gap-2">
                    <i class="fa-solid ${this.isAnalyzing ? 'fa-spinner fa-spin' : 'fa-wand-magic-sparkles'} text-gold-400"></i>
                    <span>${this.isAnalyzing ? 'AI Sedang Menganalisis Rapor Pendidikan...' : 'Simpan & Analisis Rapor Pendidikan dengan AI'}</span>
                  </button>
                </div>
              </form>

              <!-- AI Recommendations Review & Approval Table -->
              <div class="pt-4 border-t border-slate-200">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 mb-4">
                  <div>
                    <h4 class="text-base font-extrabold text-navy-900 flex items-center gap-2">
                      <i class="fa-solid fa-robot text-gold-600"></i> Hasil Identifikasi Akar Masalah & Rekomendasi Kegiatan AI
                    </h4>
                    <p class="text-xs text-slate-500">Periksa, edit, atau setujui rekomendasi di bawah ini untuk dimasukkan ke RKT dan bahan RKAS/ARKAS.</p>
                  </div>
                </div>

                <div class="space-y-4">
                  ${rp.aiRecommendations.map(rec => `
                    <div class="p-5 rounded-2xl border ${rec.disetujui ? 'bg-emerald-50/40 border-emerald-300' : 'bg-slate-50 border-slate-200'} space-y-3">
                      <div class="flex flex-wrap items-center justify-between gap-2">
                        <span class="px-3 py-1 rounded-full bg-navy-900 text-gold-400 text-xs font-bold">${ExportUtils.escape(rec.indikator)}</span>
                        <div class="flex flex-wrap items-center gap-2">
                          <button onclick="Modules.Perencanaan.editRecommendation('${rec.id}')" class="px-3 py-1.5 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-lg text-xs font-semibold">
                            <i class="fa-solid fa-pen mr-1"></i> Edit Rekomendasi
                          </button>
                          <button onclick="Modules.Perencanaan.approveAndPushToRktRkas('${rec.id}')" class="px-3.5 py-1.5 ${rec.disetujui ? 'bg-emerald-700 text-white' : 'bg-gold-500 hover:bg-gold-600 text-navy-950'} rounded-lg text-xs font-bold shadow-sm">
                            <i class="fa-solid ${rec.disetujui ? 'fa-circle-check' : 'fa-check'} mr-1"></i>
                            ${rec.disetujui ? 'Disetujui & Masuk RKT/RKAS' : 'Setujui & Masukkan ke RKT/RKAS'}
                          </button>
                        </div>
                      </div>
                      <div class="grid grid-cols-1 md:grid-cols-2 gap-3 text-xs">
                        <div class="bg-white p-3 rounded-xl border border-slate-200">
                          <div class="font-bold text-rose-700 mb-1">Akar Masalah Teridentifikasi:</div>
                          <p class="text-slate-700">${ExportUtils.escape(rec.akarMasalah)}</p>
                        </div>
                        <div class="bg-white p-3 rounded-xl border border-slate-200">
                          <div class="font-bold text-navy-900 mb-1">Rekomendasi Kegiatan Perbaikan:</div>
                          <p class="text-slate-700">${ExportUtils.escape(rec.rekomendasiKegiatan)}</p>
                        </div>
                        <div class="bg-white p-3 rounded-xl border border-slate-200">
                          <div class="font-bold text-amber-800 mb-1">Program Prioritas & Keterkaitan RKJM/RKT:</div>
                          <p class="text-slate-700">${ExportUtils.escape(rec.programPrioritas)} — <em>${ExportUtils.escape(rec.linkRkjmRkt)}</em></p>
                        </div>
                        <div class="bg-white p-3 rounded-xl border border-slate-200">
                          <div class="font-bold text-emerald-800 mb-1">Usulan Pembiayaan (Bahan RKAS/ARKAS):</div>
                          <p class="text-slate-700">${ExportUtils.escape(rec.usulanRkas)}</p>
                        </div>
                      </div>
                    </div>
                  `).join('')}
                </div>
              </div>
            </div>
          `;
        },

        async saveAndAnalyzeRapor(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          const rp = DB.data.perencanaan.raporPendidikan;
          rp.tahun = document.getElementById('rp-tahun').value.trim();
          rp.literasi = parseFloat(document.getElementById('rp-literasi').value) || 0;
          rp.numerasi = parseFloat(document.getElementById('rp-numerasi').value) || 0;
          rp.karakter = parseFloat(document.getElementById('rp-karakter').value) || 0;
          rp.iklimKeamanan = parseFloat(document.getElementById('rp-keamanan').value) || 0;
          rp.kualitasPembelajaran = parseFloat(document.getElementById('rp-kualitas').value) || 0;
          rp.catatanTambahan = document.getElementById('rp-catatan').value.trim();

          this.isAnalyzing = true;
          this.render(document.getElementById('app-content'));

          const prompt = `Analisis data Rapor Pendidikan sekolah berikut dan hasilkan 3 rekomendasi prioritas dalam format JSON array:
Data Sekolah: ${DB.data.settings.schoolName} (${DB.data.settings.jenjang})
Tahun: ${rp.tahun}
Literasi: ${rp.literasi}, Numerasi: ${rp.numerasi}, Karakter: ${rp.karakter}, Iklim Keamanan: ${rp.iklimKeamanan}, Kualitas Pembelajaran: ${rp.kualitasPembelajaran}.
Catatan Konteks: ${rp.catatanTambahan}

Kembalikan array JSON berisi objek dengan properti persis:
[{"indikator":"...","akarMasalah":"...","rekomendasiKegiatan":"...","programPrioritas":"...","linkRkjmRkt":"...","usulanRkas":"..."}]`;

          const aiText = await AI.generate(prompt, 'Anda adalah konsultan Perencanaan Berbasis Data (PBD) Kemendikdasmen. Keluarkan JSON murni yang valid.', true);

          let parsedRecs = null;
          if (aiText) {
            try {
              const cleanJson = aiText.replace(/```json|```/g, '').trim();
              parsedRecs = JSON.parse(cleanJson);
            } catch (err) {
              parsedRecs = null;
            }
          }

          if (!Array.isArray(parsedRecs) || parsedRecs.length === 0) {
            // Deterministic analytical engine based on user's actual lowest scores
            const scores = [
              { name: 'Kemampuan Numerasi', val: rp.numerasi, akar: 'Strategi pembelajaran numerasi belum banyak mengaitkan konteks pemecahan masalah kehidupan nyata.', keg: 'In House Training (IHT) Numerasi Kontekstual & Klinik Numerasi Siswa', prio: 'Penguatan Numerasi Sekolah', rkas: 'Honor Narasumber IHT & Penggandaan Modul Numerasi (Rp 6.000.000)' },
              { name: 'Kemampuan Literasi', val: rp.literasi, akar: 'Pemanfaatan buku bacaan bermutu dan pembiasaan membaca 15 menit belum merata di semua kelas.', keg: 'Revitalisasi Pojok Baca Kelas & Workshop Strategi Literasi Lintas Mapel', prio: 'Gerakan Literasi Sekolah Terpadu', rkas: 'Pengadaan Buku Bacaan Pengayaan & Bahan Pojok Baca (Rp 7.500.000)' },
              { name: 'Kualitas Pembelajaran', val: rp.kualitasPembelajaran, akar: 'Penerapan pembelajaran berdiferensiasi dan umpan balik formatif masih perlu ditingkatkan.', keg: 'Optimalisasi Komunitas Belajar (Kombel) & Supervisi Akademik Berbasis Coaching', prio: 'Peningkatan Mutu Proses Pembelajaran', rkas: 'Operasional Kombel Sekolah & Media Pembelajaran Aktif (Rp 4.500.000)' },
              { name: 'Indeks Karakter', val: rp.karakter, akar: 'Pembiasaan budaya positif dan keteladanan profil pelajar perlu penguatan konsisten.', keg: 'Program Pembiasaan Budaya Positif 5S & Kokurikuler Penguatan Karakter', prio: 'Penguatan Pendidikan Karakter', rkas: 'Kegiatan Pembinaan Karakter & Kokurikuler Siswa (Rp 5.000.000)' }
            ].sort((a, b) => a.val - b.val);

            parsedRecs = scores.slice(0, 2).map(s => ({
              indikator: `${s.name} (Skor: ${s.val})`,
              akarMasalah: s.akar,
              rekomendasiKegiatan: s.keg,
              programPrioritas: s.prio,
              linkRkjmRkt: 'RKJM Strategi Mutu / RKT Tahun Berjalan',
              usulanRkas: s.rkas
            }));
          }

          rp.aiRecommendations = parsedRecs.map((item, idx) => ({
            id: DB.uid(`rec-${idx}`),
            indikator: item.indikator || 'Indikator Prioritas',
            akarMasalah: item.akarMasalah || '-',
            rekomendasiKegiatan: item.rekomendasiKegiatan || '-',
            programPrioritas: item.programPrioritas || '-',
            linkRkjmRkt: item.linkRkjmRkt || 'RKT Tahun Berjalan',
            usulanRkas: item.usulanRkas || 'Sesuai standar pembiayaan BOS',
            disetujui: false
          }));

          this.isAnalyzing = false;
          DB.save(false);
          UI.toast('Analisis Rapor Pendidikan selesai disusun!', 'success');
          this.render(document.getElementById('app-content'));
        },

        editRecommendation(id) {
          if (!UI.canEdit()) return;
          const rec = DB.data.perencanaan.raporPendidikan.aiRecommendations.find(r => r.id === id);
          if (!rec) return;
          UI.openModal({
            title: 'Edit Rekomendasi Perencanaan AI',
            bodyHtml: `
              <div class="space-y-3">
                <div>
                  <label class="block text-xs font-bold mb-1">Akar Masalah</label>
                  <textarea id="edit-rec-akar" rows="2" class="w-full border border-slate-300 rounded-xl p-2.5 text-xs">${ExportUtils.escape(rec.akarMasalah)}</textarea>
                </div>
                <div>
                  <label class="block text-xs font-bold mb-1">Rekomendasi Kegiatan</label>
                  <textarea id="edit-rec-keg" rows="2" class="w-full border border-slate-300 rounded-xl p-2.5 text-xs">${ExportUtils.escape(rec.rekomendasiKegiatan)}</textarea>
                </div>
                <div>
                  <label class="block text-xs font-bold mb-1">Usulan Pembiayaan (Bahan RKAS)</label>
                  <input type="text" id="edit-rec-rkas" value="${ExportUtils.escape(rec.usulanRkas)}" class="w-full border border-slate-300 rounded-xl p-2.5 text-xs" />
                </div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Perencanaan.saveEditedRec('${id}')" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Perubahan</button>
            `
          });
        },

        saveEditedRec(id) {
          const rec = DB.data.perencanaan.raporPendidikan.aiRecommendations.find(r => r.id === id);
          if (rec) {
            rec.akarMasalah = document.getElementById('edit-rec-akar').value.trim();
            rec.rekomendasiKegiatan = document.getElementById('edit-rec-keg').value.trim();
            rec.usulanRkas = document.getElementById('edit-rec-rkas').value.trim();
            DB.save(true);
          }
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        approveAndPushToRktRkas(id) {
          if (!UI.canEdit()) return;
          const rec = DB.data.perencanaan.raporPendidikan.aiRecommendations.find(r => r.id === id);
          if (!rec) return;
          rec.disetujui = true;

          // Push to RKT if not already added
          if (!DB.data.perencanaan.rkt.some(x => x.program === rec.rekomendasiKegiatan)) {
            DB.data.perencanaan.rkt.unshift({
              id: DB.uid('rkt'),
              program: rec.rekomendasiKegiatan,
              tujuan: `Mengatasi akar masalah: ${rec.akarMasalah}`,
              sasaran: 'Guru & Peserta Didik',
              waktu: DB.data.settings.academicYear,
              indikator: `Meningkatnya capaian ${rec.indikator}`,
              status: 'Disetujui Kepala Sekolah'
            });
          }

          // Extract estimated cost if present in text
          const matchNum = rec.usulanRkas.replace(/\./g, '').match(/\d{5,}/);
          const estBiaya = matchNum ? parseInt(matchNum[0], 10) : 5000000;

          if (!DB.data.perencanaan.rkas.some(x => x.uraianKegiatan === rec.usulanRkas)) {
            DB.data.perencanaan.rkas.unshift({
              id: DB.uid('rkas'),
              kodeKegiatan: '03.05.01',
              uraianKegiatan: rec.usulanRkas,
              sumberDana: 'BOS Reguler',
              volume: '1 Paket Kegiatan',
              anggaran: estBiaya,
              realisasi: 0,
              status: 'Usulan Draft ARKAS (Disetujui)'
            });
          }

          DB.save(false);
          UI.toast('Rekomendasi disetujui dan otomatis ditambahkan ke RKT serta Usulan RKAS/ARKAS!', 'success');
          this.render(document.getElementById('app-content'));
        },

        renderRkjmSection() {
          const list = DB.data.perencanaan.rkjm;
          return `
            <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-6">
              <h3 class="text-lg font-extrabold text-navy-900">Rencana Kerja Jangka Menengah (RKJM 4 Tahunan)</h3>
              <form onsubmit="Modules.Perencanaan.addRkjm(event)" class="grid grid-cols-1 md:grid-cols-2 gap-4 bg-slate-50 p-4 rounded-2xl border border-slate-200">
                <input type="text" id="rkjm-sasaran" required placeholder="Sasaran Strategis 4 Tahun" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkjm-program" required placeholder="Program Jangka Menengah" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkjm-tahun" required placeholder="Periode Tahun (misal: 2025 - 2028)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkjm-indikator" required placeholder="Indikator Keberhasilan" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkjm-pj" required placeholder="Penanggung Jawab" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <button type="submit" class="bg-navy-900 hover:bg-navy-800 text-white rounded-xl px-4 py-2.5 text-xs font-bold">
                  <i class="fa-solid fa-plus text-gold-400 mr-1"></i> Tambah Program RKJM
                </button>
              </form>
              <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse text-xs">
                  <thead>
                    <tr class="bg-navy-900 text-white">
                      <th class="p-3 rounded-tl-xl">Sasaran Strategis</th>
                      <th class="p-3">Program Jangka Menengah</th>
                      <th class="p-3">Periode</th>
                      <th class="p-3">Indikator</th>
                      <th class="p-3">Penanggung Jawab</th>
                      <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                    </tr>
                  </thead>
                  <tbody class="divide-y divide-slate-200">
                    ${list.map(item => `
                      <tr>
                        <td class="p-3 font-semibold">${ExportUtils.escape(item.sasaranStrategis)}</td>
                        <td class="p-3">${ExportUtils.escape(item.programJangkaMenengah)}</td>
                        <td class="p-3">${ExportUtils.escape(item.tahunTarget)}</td>
                        <td class="p-3">${ExportUtils.escape(item.indikatorKeberhasilan)}</td>
                        <td class="p-3">${ExportUtils.escape(item.penanggungJawab)}</td>
                        <td class="p-3 text-center">
                          <button onclick="Modules.Perencanaan.deleteItem('rkjm','${item.id}')" class="p-1.5 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                        </td>
                      </tr>
                    `).join('')}
                  </tbody>
                </table>
              </div>
            </div>
          `;
        },

        addRkjm(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.perencanaan.rkjm.unshift({
            id: DB.uid('rkjm'),
            sasaranStrategis: document.getElementById('rkjm-sasaran').value.trim(),
            programJangkaMenengah: document.getElementById('rkjm-program').value.trim(),
            tahunTarget: document.getElementById('rkjm-tahun').value.trim(),
            indikatorKeberhasilan: document.getElementById('rkjm-indikator').value.trim(),
            penanggungJawab: document.getElementById('rkjm-pj').value.trim()
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        renderRktSection() {
          const list = DB.data.perencanaan.rkt;
          return `
            <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-6">
              <h3 class="text-lg font-extrabold text-navy-900">Rencana Kerja Tahunan (RKT)</h3>
              <form onsubmit="Modules.Perencanaan.addRkt(event)" class="grid grid-cols-1 md:grid-cols-3 gap-3 bg-slate-50 p-4 rounded-2xl border border-slate-200">
                <input type="text" id="rkt-program" required placeholder="Nama Program Tahunan" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkt-tujuan" required placeholder="Tujuan Kegiatan" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkt-sasaran" required placeholder="Sasaran" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkt-waktu" required placeholder="Jadwal Pelaksanaan" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkt-indikator" required placeholder="Indikator Ketercapaian" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <button type="submit" class="bg-navy-900 hover:bg-navy-800 text-white rounded-xl px-4 py-2.5 text-xs font-bold">
                  <i class="fa-solid fa-plus text-gold-400 mr-1"></i> Tambah RKT
                </button>
              </form>
              <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse text-xs">
                  <thead>
                    <tr class="bg-navy-900 text-white">
                      <th class="p-3 rounded-tl-xl">Program RKT</th>
                      <th class="p-3">Tujuan</th>
                      <th class="p-3">Sasaran</th>
                      <th class="p-3">Waktu</th>
                      <th class="p-3">Status</th>
                      <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                    </tr>
                  </thead>
                  <tbody class="divide-y divide-slate-200">
                    ${list.map(item => `
                      <tr>
                        <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(item.program)}</td>
                        <td class="p-3">${ExportUtils.escape(item.tujuan)}</td>
                        <td class="p-3">${ExportUtils.escape(item.sasaran)}</td>
                        <td class="p-3">${ExportUtils.escape(item.waktu)}</td>
                        <td class="p-3"><span class="px-2.5 py-1 rounded-full bg-gold-100 text-gold-800 font-bold">${ExportUtils.escape(item.status)}</span></td>
                        <td class="p-3 text-center">
                          <button onclick="Modules.Perencanaan.deleteItem('rkt','${item.id}')" class="p-1.5 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                        </td>
                      </tr>
                    `).join('')}
                  </tbody>
                </table>
              </div>
            </div>
          `;
        },

        addRkt(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.perencanaan.rkt.unshift({
            id: DB.uid('rkt'),
            program: document.getElementById('rkt-program').value.trim(),
            tujuan: document.getElementById('rkt-tujuan').value.trim(),
            sasaran: document.getElementById('rkt-sasaran').value.trim(),
            waktu: document.getElementById('rkt-waktu').value.trim(),
            indikator: document.getElementById('rkt-indikator').value.trim(),
            status: 'Direncanakan'
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        renderRkasSection(totalRkas, totalRealisasi) {
          const list = DB.data.perencanaan.rkas;
          return `
            <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-6">
              <div class="bg-amber-50 border border-amber-200 rounded-xl p-3.5 text-xs text-amber-900 flex items-start gap-2.5">
                <i class="fa-solid fa-circle-info text-amber-600 mt-0.5"></i>
                <div>
                  <strong>Catatan Kepatuhan ARKAS:</strong> Modul ini menyusun kertas kerja perencanaan & pembiayaan RKAS untuk memudahkan penginputan ke aplikasi resmi <strong>ARKAS Kemendikdasmen</strong> (tidak terhubung otomatis ke server ARKAS tanpa API resmi).
                </div>
              </div>

              <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                <div class="p-4 rounded-2xl bg-navy-900 text-white">
                  <div class="text-xs text-gold-400 font-bold uppercase">Total Rencana Anggaran (RKAS)</div>
                  <div class="text-2xl font-extrabold mt-1">${ExportUtils.formatRupiah(totalRkas)}</div>
                </div>
                <div class="p-4 rounded-2xl bg-emerald-900 text-white">
                  <div class="text-xs text-emerald-300 font-bold uppercase">Total Realisasi Serapan</div>
                  <div class="text-2xl font-extrabold mt-1">${ExportUtils.formatRupiah(totalRealisasi)}</div>
                </div>
              </div>

              <form onsubmit="Modules.Perencanaan.addRkas(event)" class="grid grid-cols-1 md:grid-cols-3 gap-3 bg-slate-50 p-4 rounded-2xl border border-slate-200">
                <input type="text" id="rkas-kode" required placeholder="Kode Kegiatan (misal: 03.03.15)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkas-uraian" required placeholder="Uraian Kegiatan & Belanja" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkas-sumber" value="BOS Reguler" required placeholder="Sumber Dana" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="text" id="rkas-volume" required placeholder="Volume / Satuan" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <input type="number" id="rkas-anggaran" required placeholder="Jumlah Anggaran (Rp)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                <button type="submit" class="bg-navy-900 hover:bg-navy-800 text-white rounded-xl px-4 py-2.5 text-xs font-bold">
                  <i class="fa-solid fa-plus text-gold-400 mr-1"></i> Tambah Item RKAS
                </button>
              </form>

              <div class="flex justify-end">
                <button onclick="Modules.Perencanaan.exportRkasExcel()" class="px-4 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold">
                  <i class="fa-solid fa-file-excel mr-1"></i> Ekspor Kertas Kerja RKAS (.xlsx)
                </button>
              </div>

              <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse text-xs">
                  <thead>
                    <tr class="bg-navy-900 text-white">
                      <th class="p-3 rounded-tl-xl">Kode</th>
                      <th class="p-3">Uraian Kegiatan</th>
                      <th class="p-3">Sumber Dana</th>
                      <th class="p-3">Volume</th>
                      <th class="p-3">Anggaran</th>
                      <th class="p-3">Status</th>
                      <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                    </tr>
                  </thead>
                  <tbody class="divide-y divide-slate-200">
                    ${list.map(item => `
                      <tr>
                        <td class="p-3 font-mono font-bold">${ExportUtils.escape(item.kodeKegiatan)}</td>
                        <td class="p-3 font-semibold">${ExportUtils.escape(item.uraianKegiatan)}</td>
                        <td class="p-3">${ExportUtils.escape(item.sumberDana)}</td>
                        <td class="p-3">${ExportUtils.escape(item.volume)}</td>
                        <td class="p-3 font-bold text-navy-900">${ExportUtils.formatRupiah(item.anggaran)}</td>
                        <td class="p-3"><span class="px-2.5 py-1 rounded-full bg-navy-100 text-navy-800 font-bold">${ExportUtils.escape(item.status)}</span></td>
                        <td class="p-3 text-center">
                          <button onclick="Modules.Perencanaan.deleteItem('rkas','${item.id}')" class="p-1.5 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                        </td>
                      </tr>
                    `).join('')}
                  </tbody>
                </table>
              </div>
            </div>
          `;
        },

        addRkas(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.perencanaan.rkas.unshift({
            id: DB.uid('rkas'),
            kodeKegiatan: document.getElementById('rkas-kode').value.trim(),
            uraianKegiatan: document.getElementById('rkas-uraian').value.trim(),
            sumberDana: document.getElementById('rkas-sumber').value.trim(),
            volume: document.getElementById('rkas-volume').value.trim(),
            anggaran: Number(document.getElementById('rkas-anggaran').value || 0),
            realisasi: 0,
            status: 'Usulan Draft ARKAS'
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteItem(listName, id) {
          if (!UI.canEdit()) return;
          UI.confirmAction('Hapus Item Perencanaan', 'Yakin ingin menghapus data perencanaan ini?', () => {
            DB.data.perencanaan[listName] = DB.data.perencanaan[listName].filter(x => x.id !== id);
            DB.save(true);
            this.render(document.getElementById('app-content'));
          });
        },

        exportRkasExcel() {
          const rows = DB.data.perencanaan.rkas.map((r, i) => ({
            No: i + 1,
            'Kode Kegiatan': r.kodeKegiatan,
            'Uraian Kegiatan': r.uraianKegiatan,
            'Sumber Dana': r.sumberDana,
            Volume: r.volume,
            'Anggaran (Rp)': r.anggaran,
            'Realisasi (Rp)': r.realisasi,
            Status: r.status
          }));
          ExportUtils.exportExcel('Kertas_Kerja_RKAS_ARKAS.xlsx', 'RKAS', rows);
        },

        buildPrintHtml() {
          const p = DB.data.perencanaan;
          return `
            <h2 style="text-align:center;text-transform:uppercase;">DOKUMEN PERENCANAAN & PENGANGGARAN SEKOLAH</h2>
            <p style="text-align:center;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <h3>A. Rencana Kerja Tahunan (RKT)</h3>
            <table style="width:100%;border-collapse:collapse;">
              <tr><th style="border:1px solid #000;padding:6px;">No</th><th style="border:1px solid #000;padding:6px;">Program</th><th style="border:1px solid #000;padding:6px;">Tujuan</th><th style="border:1px solid #000;padding:6px;">Jadwal</th></tr>
              ${p.rkt.map((r, i) => `<tr><td style="border:1px solid #000;padding:6px;">${i+1}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.program)}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.tujuan)}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.waktu)}</td></tr>`).join('')}
            </table>
            <h3>B. Rencana Kegiatan dan Anggaran Sekolah (RKAS)</h3>
            <table style="width:100%;border-collapse:collapse;">
              <tr><th style="border:1px solid #000;padding:6px;">Kode</th><th style="border:1px solid #000;padding:6px;">Uraian Kegiatan</th><th style="border:1px solid #000;padding:6px;">Sumber Dana</th><th style="border:1px solid #000;padding:6px;">Anggaran</th></tr>
              ${p.rkas.map(r => `<tr><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.kodeKegiatan)}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.uraianKegiatan)}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.sumberDana)}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.formatRupiah(r.anggaran)}</td></tr>`).join('')}
            </table>
          `;
        },

        printSection() {
          ExportUtils.printDocument('Perencanaan RKT & RKAS', this.buildPrintHtml(), true, true);
        },

        exportWordSection() {
          ExportUtils.downloadWord('Perencanaan_RKT_RKAS', this.buildPrintHtml(), true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 5: SUPERVISI AKADEMIK & MANAJERIAL
      ------------------------------------------------------------------- */
      Supervisi: {
        isGeneratingAdvice: false,

        render(container) {
          const records = DB.data.supervisi.records;
          const instrumen = DB.data.supervisi.instrumenCustom;

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Supervisi Akademik & Manajerial',
                'Penilaian perencanaan, pelaksanaan, dan asesmen pembelajaran serta tindak lanjut coaching guru.',
                'fa-solid fa-clipboard-check',
                `
                  <button onclick="Modules.Supervisi.printAllRekap()" class="px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-print mr-1"></i> Cetak Rekap Semua Guru
                  </button>
                  <button onclick="Modules.Supervisi.exportAllWord()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-file-word text-gold-400 mr-1"></i> Unduh Rekap Word
                  </button>
                `
              )}

              ${UI.guideBanner(
                'Pelaksanaan Supervisi & Coaching',
                'Isi skor hasil observasi (0-100) untuk aspek Perencanaan, Pelaksanaan, dan Asesmen. Klik tombol "Susun Rekomendasi AI" untuk mendapatkan saran coaching dan rencana tindak lanjut otomatis, lalu simpan dan cetak laporan individual maupun rekap keseluruhan.'
              )}

              <!-- Customizable Instrument Aspects -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 mb-4">
                  <div>
                    <h3 class="text-base font-extrabold text-navy-900">Instrumen Supervisi Sekolah (Dapat Disesuaikan)</h3>
                    <p class="text-xs text-slate-500">Acuan indikator penilaian observasi pembelajaran</p>
                  </div>
                  <button onclick="Modules.Supervisi.openAddInstrumenModal()" class="px-3 py-1.5 bg-navy-50 hover:bg-navy-100 text-navy-900 border border-navy-200 rounded-xl text-xs font-bold">
                    <i class="fa-solid fa-plus mr-1"></i> Tambah Indikator Instrumen
                  </button>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
                  ${instrumen.map((ins, i) => `
                    <div class="p-3.5 rounded-xl bg-slate-50 border border-slate-200 relative">
                      <div class="text-xs font-extrabold text-navy-900">${i + 1}. ${ExportUtils.escape(ins.aspek)}</div>
                      <p class="text-xs text-slate-600 mt-1">${ExportUtils.escape(ins.indikator)}</p>
                    </div>
                  `).join('')}
                </div>
              </div>

              <!-- Form Input Hasil Supervisi -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                <h3 class="text-base font-extrabold text-navy-900 mb-4">Input Hasil Observasi & Penilaian Supervisi</h3>
                <form onsubmit="Modules.Supervisi.saveRecord(event)" class="grid grid-cols-1 md:grid-cols-3 gap-4">
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Tanggal Supervisi</label>
                    <input type="date" id="sup-tanggal" value="${new Date().toISOString().split('T')[0]}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Nama Guru / Tendik</label>
                    <input type="text" id="sup-nama" list="gtk-datalist" required placeholder="Pilih atau ketik nama guru" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    <datalist id="gtk-datalist">
                      ${DB.data.gtk.map(g => `<option value="${ExportUtils.escape(g.nama)}"></option>`).join('')}
                    </datalist>
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">NIP (Opsional)</label>
                    <input type="text" id="sup-nip" placeholder="NIP Guru" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Mata Pelajaran / Bidang</label>
                    <input type="text" id="sup-mapel" required placeholder="Contoh: Matematika / Administrasi" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Kelas / Unit</label>
                    <input type="text" id="sup-kelas" required placeholder="Contoh: VIII-A" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Jenis Supervisi</label>
                    <select id="sup-jenis" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white">
                      <option value="Supervisi Akademik">Supervisi Akademik</option>
                      <option value="Supervisi Manajerial">Supervisi Manajerial</option>
                    </select>
                  </div>

                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Skor Perencanaan (0-100)</label>
                    <input type="number" min="0" max="100" id="sup-s1" value="85" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Skor Pelaksanaan (0-100)</label>
                    <input type="number" min="0" max="100" id="sup-s2" value="85" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Skor Asesmen (0-100)</label>
                    <input type="number" min="0" max="100" id="sup-s3" value="84" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  </div>

                  <div class="md:col-span-3">
                    <label class="block text-xs font-bold text-slate-700 mb-1">Catatan Hasil Observasi Pembelajaran</label>
                    <textarea id="sup-catatan" rows="2" required placeholder="Tuliskan temuan kekuatan dan hal yang perlu ditingkatkan selama observasi..." class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs"></textarea>
                  </div>

                  <div class="md:col-span-3 flex justify-end">
                    <button type="button" onclick="Modules.Supervisi.generateAiFeedback()" class="px-4 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold shadow-sm flex items-center gap-1.5">
                      <i class="fa-solid fa-wand-magic-sparkles"></i> Susun Rekomendasi & Tindak Lanjut AI
                    </button>
                  </div>

                  <div class="md:col-span-3 grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Rekomendasi Perbaikan</label>
                      <textarea id="sup-rekomendasi" rows="2" required placeholder="Rekomendasi perbaikan untuk guru..." class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs"></textarea>
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Rencana Tindak Lanjut</label>
                      <textarea id="sup-tindaklanjut" rows="2" required placeholder="Coaching / Kombel / Pendampingan..." class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs"></textarea>
                    </div>
                  </div>

                  <div class="md:col-span-3">
                    <button type="submit" class="px-5 py-2.5 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow">
                      <i class="fa-solid fa-floppy-disk text-gold-400 mr-1"></i> Simpan Hasil Supervisi
                    </button>
                  </div>
                </form>
              </div>

              <!-- Rekap Hasil Supervisi Seluruh Guru -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                <h3 class="text-base font-extrabold text-navy-900 mb-4">Rekapitulasi Hasil Supervisi (${records.length} Laporan)</h3>
                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3 rounded-tl-xl">Tanggal</th>
                        <th class="p-3">Nama Guru</th>
                        <th class="p-3">Mapel / Kelas</th>
                        <th class="p-3 text-center">Nilai Akhir</th>
                        <th class="p-3 text-center">Predikat</th>
                        <th class="p-3">Tindak Lanjut</th>
                        <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${records.map(r => {
                        const avg = Math.round((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3);
                        const predikat = avg >= 91 ? 'Amat Baik' : avg >= 81 ? 'Baik' : avg >= 71 ? 'Cukup' : 'Perlu Pembinaan';
                        return `
                          <tr class="hover:bg-slate-50">
                            <td class="p-3">${ExportUtils.escape(r.tanggal)}</td>
                            <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(r.namaGuru)}</td>
                            <td class="p-3">${ExportUtils.escape(r.mapel)} (${ExportUtils.escape(r.kelas)})</td>
                            <td class="p-3 text-center font-extrabold text-navy-900">${avg}</td>
                            <td class="p-3 text-center"><span class="px-2.5 py-1 rounded-full bg-gold-100 text-gold-800 font-bold">${predikat}</span></td>
                            <td class="p-3 max-w-xs">${ExportUtils.escape(r.tindakLanjut)}</td>
                            <td class="p-3 text-center whitespace-nowrap">
                              <button onclick="Modules.Supervisi.printIndividual('${r.id}')" title="Cetak Laporan Individual" class="p-2 text-navy-800 hover:bg-navy-100 rounded-lg mr-1">
                                <i class="fa-solid fa-print"></i>
                              </button>
                              <button onclick="Modules.Supervisi.deleteRecord('${r.id}')" title="Hapus" class="p-2 text-rose-600 hover:bg-rose-50 rounded-lg">
                                <i class="fa-solid fa-trash"></i>
                              </button>
                            </td>
                          </tr>
                        `;
                      }).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        openAddInstrumenModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Tambah Indikator Instrumen Supervisi',
            bodyHtml: `
              <div class="space-y-3">
                <div>
                  <label class="block text-xs font-bold mb-1">Aspek Penilaian</label>
                  <input type="text" id="new-ins-aspek" placeholder="Contoh: Pemanfaatan Teknologi Digital" class="w-full border border-slate-300 rounded-xl p-2.5 text-xs" />
                </div>
                <div>
                  <label class="block text-xs font-bold mb-1">Indikator Observasi</label>
                  <textarea id="new-ins-indikator" rows="2" placeholder="Deskripsi indikator..." class="w-full border border-slate-300 rounded-xl p-2.5 text-xs"></textarea>
                </div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Supervisi.saveNewInstrumen()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan</button>
            `
          });
        },

        saveNewInstrumen() {
          const aspek = document.getElementById('new-ins-aspek').value.trim();
          const indikator = document.getElementById('new-ins-indikator').value.trim();
          if (!aspek || !indikator) return;
          DB.data.supervisi.instrumenCustom.push({ id: DB.uid('ins'), aspek, indikator });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        async generateAiFeedback() {
          const nama = document.getElementById('sup-nama').value.trim() || 'Guru';
          const mapel = document.getElementById('sup-mapel').value.trim() || 'Mata Pelajaran';
          const s1 = document.getElementById('sup-s1').value;
          const s2 = document.getElementById('sup-s2').value;
          const s3 = document.getElementById('sup-s3').value;
          const catatan = document.getElementById('sup-catatan').value.trim();

          UI.toast('Menyusun rekomendasi coaching AI...', 'info');
          const prompt = `Berikan rekomendasi perbaikan pembelajaran dan rencana tindak lanjut supervisi untuk guru berikut dalam format JSON {"rekomendasi":"...","tindakLanjut":"..."}:
Nama Guru: ${nama}, Mapel: ${mapel}, Skor Perencanaan: ${s1}, Pelaksanaan: ${s2}, Asesmen: ${s3}.
Catatan Observasi: ${catatan || 'Pembelajaran berjalan cukup baik namun perlu penguatan asesmen formatif dan keaktifan murid.'}`;

          const res = await AI.generate(prompt, 'Anda adalah supervisor pendidikan profesional. Berikan saran coaching yang konstruktif, spesifik, dan memotivasi guru.', true);
          let obj = null;
          if (res) {
            try {
              obj = JSON.parse(res.replace(/```json|```/g, '').trim());
            } catch (e) {
              obj = null;
            }
          }
          if (!obj) {
            obj = {
              rekomendasi: `Pertahankan pengelolaan kelas yang kondusif pada mapel ${mapel}, serta tingkatkan variasi asesmen formatif dan pertanyaan pemantik HOTS untuk menggali pemahaman siswa.`,
              tindakLanjut: `Diskusi refleksi individual (coaching) bersama Kepala Sekolah dan berbagi praktik baik di forum Komunitas Belajar (Kombel) sekolah.`
            };
          }
          document.getElementById('sup-rekomendasi').value = obj.rekomendasi;
          document.getElementById('sup-tindaklanjut').value = obj.tindakLanjut;
          UI.toast('Rekomendasi & tindak lanjut berhasil diisi!', 'success');
        },

        saveRecord(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.supervisi.records.unshift({
            id: DB.uid('sup'),
            tanggal: document.getElementById('sup-tanggal').value,
            namaGuru: document.getElementById('sup-nama').value.trim(),
            nip: document.getElementById('sup-nip').value.trim() || '-',
            mapel: document.getElementById('sup-mapel').value.trim(),
            kelas: document.getElementById('sup-kelas').value.trim(),
            jenis: document.getElementById('sup-jenis').value,
            skorPerencanaan: Number(document.getElementById('sup-s1').value),
            skorPelaksanaan: Number(document.getElementById('sup-s2').value),
            skorAsesmen: Number(document.getElementById('sup-s3').value),
            catatanObservasi: document.getElementById('sup-catatan').value.trim(),
            rekomendasi: document.getElementById('sup-rekomendasi').value.trim(),
            tindakLanjut: document.getElementById('sup-tindaklanjut').value.trim()
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteRecord(id) {
          if (!UI.canEdit()) return;
          UI.confirmAction('Hapus Laporan Supervisi', 'Hapus rekaman supervisi guru ini?', () => {
            DB.data.supervisi.records = DB.data.supervisi.records.filter(x => x.id !== id);
            DB.save(true);
            this.render(document.getElementById('app-content'));
          });
        },

        printIndividual(id) {
          const r = DB.data.supervisi.records.find(x => x.id === id);
          if (!r) return;
          const avg = Math.round((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3);
          const html = `
            <h2 style="text-align:center;text-transform:uppercase;margin-bottom:4px;">LAPORAN HASIL ${ExportUtils.escape(r.jenis)}</h2>
            <p style="text-align:center;margin-top:0;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <table style="width:100%;border:none;margin-bottom:14px;">
              <tr><td style="border:none;width:180px;">Nama Guru</td><td style="border:none;">: <strong>${ExportUtils.escape(r.namaGuru)}</strong></td></tr>
              <tr><td style="border:none;">NIP</td><td style="border:none;">: ${ExportUtils.escape(r.nip)}</td></tr>
              <tr><td style="border:none;">Mata Pelajaran / Kelas</td><td style="border:none;">: ${ExportUtils.escape(r.mapel)} / ${ExportUtils.escape(r.kelas)}</td></tr>
              <tr><td style="border:none;">Tanggal Pelaksanaan</td><td style="border:none;">: ${ExportUtils.escape(r.tanggal)}</td></tr>
            </table>
            <table style="width:100%;border-collapse:collapse;margin-bottom:14px;">
              <tr><th style="border:1px solid #000;padding:6px;">Aspek Penilaian</th><th style="border:1px solid #000;padding:6px;text-align:center;">Skor (0-100)</th></tr>
              <tr><td style="border:1px solid #000;padding:6px;">1. Perencanaan Pembelajaran</td><td style="border:1px solid #000;padding:6px;text-align:center;">${r.skorPerencanaan}</td></tr>
              <tr><td style="border:1px solid #000;padding:6px;">2. Pelaksanaan Pembelajaran</td><td style="border:1px solid #000;padding:6px;text-align:center;">${r.skorPelaksanaan}</td></tr>
              <tr><td style="border:1px solid #000;padding:6px;">3. Asesmen Pembelajaran</td><td style="border:1px solid #000;padding:6px;text-align:center;">${r.skorAsesmen}</td></tr>
              <tr><th style="border:1px solid #000;padding:6px;">Rata-rata Nilai Akhir</th><th style="border:1px solid #000;padding:6px;text-align:center;">${avg}</th></tr>
            </table>
            <p><strong>Catatan Hasil Observasi:</strong><br/>${ExportUtils.escape(r.catatanObservasi)}</p>
            <p><strong>Rekomendasi Perbaikan:</strong><br/>${ExportUtils.escape(r.rekomendasi)}</p>
            <p><strong>Rencana Tindak Lanjut:</strong><br/>${ExportUtils.escape(r.tindakLanjut)}</p>
          `;
          ExportUtils.printDocument(`Supervisi_${r.namaGuru}`, html, true, true, r.tanggal);
        },

        buildAllRekapHtml() {
          const list = DB.data.supervisi.records;
          return `
            <h2 style="text-align:center;text-transform:uppercase;">REKAPITULASI HASIL SUPERVISI GURU</h2>
            <p style="text-align:center;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <table style="width:100%;border-collapse:collapse;font-size:10.5pt;">
              <thead>
                <tr>
                  <th style="border:1px solid #000;padding:6px;">No</th>
                  <th style="border:1px solid #000;padding:6px;">Tanggal</th>
                  <th style="border:1px solid #000;padding:6px;">Nama Guru</th>
                  <th style="border:1px solid #000;padding:6px;">Mapel / Kelas</th>
                  <th style="border:1px solid #000;padding:6px;">Nilai Akhir</th>
                  <th style="border:1px solid #000;padding:6px;">Rekomendasi & Tindak Lanjut</th>
                </tr>
              </thead>
              <tbody>
                ${list.map((r, i) => {
                  const avg = Math.round((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3);
                  return `
                    <tr>
                      <td style="border:1px solid #000;padding:6px;">${i + 1}</td>
                      <td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.tanggal)}</td>
                      <td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.namaGuru)}</td>
                      <td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.mapel)} (${ExportUtils.escape(r.kelas)})</td>
                      <td style="border:1px solid #000;padding:6px;text-align:center;"><strong>${avg}</strong></td>
                      <td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(r.rekomendasi)} — <em>${ExportUtils.escape(r.tindakLanjut)}</em></td>
                    </tr>
                  `;
                }).join('')}
              </tbody>
            </table>
          `;
        },

        printAllRekap() {
          ExportUtils.printDocument('Rekap_Supervisi_Guru', this.buildAllRekapHtml(), true, true);
        },

        exportAllWord() {
          ExportUtils.downloadWord('Rekap_Supervisi_Guru', this.buildAllRekapHtml(), true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 6: MANAJEMEN GURU & TENAGA KEPENDIDIKAN (EXCEL IMPORT/EXPORT)
      ------------------------------------------------------------------- */
      GTK: {
        searchQuery: '',

        render(container) {
          const q = this.searchQuery.toLowerCase();
          const list = DB.data.gtk.filter(g =>
            Object.values(g).some(v => String(v).toLowerCase().includes(q))
          );

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Manajemen Guru & Tenaga Kependidikan',
                'Database GTK, pembagian tugas mengajar, kehadiran, penilaian kinerja, pelatihan, serta impor/ekspor Excel.',
                'fa-solid fa-chalkboard-user',
                `
                  <label class="cursor-pointer px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-file-import text-emerald-700"></i> Impor Excel
                    <input type="file" accept=".xlsx,.xls" onchange="Modules.GTK.importExcel(event)" class="hidden" />
                  </label>
                  <button onclick="Modules.GTK.exportExcel()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-file-excel"></i> Ekspor Excel
                  </button>
                `
              )}

              ${UI.guideBanner(
                'Database & Pembinaan GTK',
                'Tambahkan data guru dan staf tata usaha beserta beban jam mengajar, tingkat kehadiran, nilai kinerja, dan riwayat pelatihan. Anda juga dapat mengekspor seluruh tabel ke format Excel (.xlsx) atau mengimpor dari file Excel.'
              )}

              <!-- Form Tambah GTK -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                <h3 class="text-base font-extrabold text-navy-900 mb-4">Tambah Data Guru / Tenaga Kependidikan</h3>
                <form onsubmit="Modules.GTK.addGtk(event)" class="grid grid-cols-1 md:grid-cols-3 gap-4">
                  <input type="text" id="gtk-nama" required placeholder="Nama Lengkap & Gelar" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="text" id="gtk-nip" placeholder="NIP / NUPTK (Opsional)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="text" id="gtk-jabatan" required placeholder="Jabatan / Golongan" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="text" id="gtk-mapel" required placeholder="Mata Pelajaran / Bidang Tugas" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="text" id="gtk-pendidikan" required placeholder="Riwayat Pendidikan Terakhir" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="text" id="gtk-tambahan" placeholder="Tugas Tambahan (Waka/Wali Kelas/dll)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="number" id="gtk-jam" min="0" max="45" value="24" required placeholder="Jam Mengajar / Minggu" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="number" id="gtk-hadir" min="0" max="100" value="98" required placeholder="Kehadiran (%)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="number" id="gtk-kinerja" min="0" max="100" value="90" required placeholder="Skor Penilaian Kinerja (0-100)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="text" id="gtk-pelatihan" placeholder="Riwayat Pelatihan / Workshop" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <input type="text" id="gtk-pembinaan" placeholder="Catatan Pembinaan Kepala Sekolah" class="md:col-span-2 border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                  <div class="md:col-span-3">
                    <button type="submit" class="px-5 py-2.5 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow">
                      <i class="fa-solid fa-user-plus text-gold-400 mr-1"></i> Simpan Data GTK
                    </button>
                  </div>
                </form>
              </div>

              <!-- Tabel GTK -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-4">
                  <h4 class="text-sm font-extrabold text-navy-900">Direktori Guru & Tendik (${list.length} Orang)</h4>
                  <input type="text" value="${ExportUtils.escape(this.searchQuery)}" oninput="Modules.GTK.onSearch(this.value)" placeholder="Cari nama, NIP, mapel..." class="w-full sm:w-72 border border-slate-300 rounded-xl px-3.5 py-2 text-xs outline-none" />
                </div>
                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3 rounded-tl-xl">Nama & NIP</th>
                        <th class="p-3">Jabatan & Pendidikan</th>
                        <th class="p-3">Mapel & Tugas Tambahan</th>
                        <th class="p-3 text-center">JP</th>
                        <th class="p-3 text-center">Hadir / Kinerja</th>
                        <th class="p-3">Pelatihan & Pembinaan</th>
                        <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${list.map(g => `
                        <tr class="hover:bg-slate-50">
                          <td class="p-3">
                            <div class="font-bold text-navy-900">${ExportUtils.escape(g.nama)}</div>
                            <div class="text-[11px] text-slate-500">NIP: ${ExportUtils.escape(g.nip)}</div>
                          </td>
                          <td class="p-3">
                            <div class="font-semibold">${ExportUtils.escape(g.jabatan)}</div>
                            <div class="text-[11px] text-slate-500">${ExportUtils.escape(g.pendidikan)}</div>
                          </td>
                          <td class="p-3">
                            <div class="font-semibold text-navy-800">${ExportUtils.escape(g.mapel)}</div>
                            <div class="text-[11px] text-slate-500">${ExportUtils.escape(g.tugasTambahan || '-')}</div>
                          </td>
                          <td class="p-3 text-center font-bold">${g.jamMengajar} JP</td>
                          <td class="p-3 text-center">
                            <span class="px-2 py-0.5 rounded bg-emerald-100 text-emerald-800 font-bold">${g.kehadiran}%</span>
                            <span class="px-2 py-0.5 rounded bg-gold-100 text-gold-800 font-bold ml-1">${g.nilaiKinerja}</span>
                          </td>
                          <td class="p-3 max-w-xs">
                            <div class="text-[11px] font-semibold text-navy-800">${ExportUtils.escape(g.pelatihan || '-')}</div>
                            <div class="text-[11px] text-slate-500">${ExportUtils.escape(g.catatanPembinaan || '-')}</div>
                          </td>
                          <td class="p-3 text-center">
                            <button onclick="Modules.GTK.deleteGtk('${g.id}')" class="p-2 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                          </td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        onSearch(v) {
          this.searchQuery = v;
          this.render(document.getElementById('app-content'));
        },

        addGtk(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.gtk.unshift({
            id: DB.uid('gtk'),
            nama: document.getElementById('gtk-nama').value.trim(),
            nip: document.getElementById('gtk-nip').value.trim() || '-',
            jabatan: document.getElementById('gtk-jabatan').value.trim(),
            mapel: document.getElementById('gtk-mapel').value.trim(),
            pendidikan: document.getElementById('gtk-pendidikan').value.trim(),
            tugasTambahan: document.getElementById('gtk-tambahan').value.trim() || '-',
            jamMengajar: Number(document.getElementById('gtk-jam').value || 0),
            kehadiran: Number(document.getElementById('gtk-hadir').value || 0),
            nilaiKinerja: Number(document.getElementById('gtk-kinerja').value || 0),
            pelatihan: document.getElementById('gtk-pelatihan').value.trim() || '-',
            catatanPembinaan: document.getElementById('gtk-pembinaan').value.trim() || '-'
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteGtk(id) {
          if (!UI.canEdit()) return;
          UI.confirmAction('Hapus Data GTK', 'Yakin ingin menghapus data guru/tendik ini?', () => {
            DB.data.gtk = DB.data.gtk.filter(g => g.id !== id);
            DB.save(true);
            this.render(document.getElementById('app-content'));
          });
        },

        exportExcel() {
          const rows = DB.data.gtk.map((g, i) => ({
            No: i + 1,
            Nama: g.nama,
            NIP: g.nip,
            Jabatan: g.jabatan,
            'Mata Pelajaran': g.mapel,
            Pendidikan: g.pendidikan,
            'Tugas Tambahan': g.tugasTambahan,
            'Jam Mengajar': g.jamMengajar,
            'Kehadiran (%)': g.kehadiran,
            'Nilai Kinerja': g.nilaiKinerja,
            Pelatihan: g.pelatihan,
            'Catatan Pembinaan': g.catatanPembinaan
          }));
          ExportUtils.exportExcel('Database_GTK_Sekolah.xlsx', 'Data_GTK', rows);
        },

        importExcel(event) {
          if (!UI.canEdit()) return;
          const file = event.target.files[0];
          if (!file) return;
          const reader = new FileReader();
          reader.onload = (e) => {
            try {
              const wb = XLSX.read(e.target.result, { type: 'binary' });
              const sheet = wb.Sheets[wb.SheetNames[0]];
              const rows = XLSX.utils.sheet_to_json(sheet);
              if (!rows.length) {
                UI.toast('File Excel kosong.', 'warning');
                return;
              }
              rows.forEach(r => {
                DB.data.gtk.push({
                  id: DB.uid('gtk'),
                  nama: r.Nama || r.nama || 'Nama Guru',
                  nip: String(r.NIP || r.nip || '-'),
                  jabatan: r.Jabatan || r.jabatan || 'Guru',
                  mapel: r['Mata Pelajaran'] || r.Mapel || '-',
                  pendidikan: r.Pendidikan || 'S1',
                  tugasTambahan: r['Tugas Tambahan'] || '-',
                  jamMengajar: Number(r['Jam Mengajar'] || 24),
                  kehadiran: Number(r['Kehadiran (%)'] || 98),
                  nilaiKinerja: Number(r['Nilai Kinerja'] || 88),
                  pelatihan: r.Pelatihan || '-',
                  catatanPembinaan: r['Catatan Pembinaan'] || '-'
                });
              });
              DB.save(true);
              this.render(document.getElementById('app-content'));
            } catch (err) {
              UI.toast('Format file Excel tidak valid: ' + err.message, 'error');
            }
          };
          reader.readAsBinaryString(file);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 7: MANAJEMEN PESERTA DIDIK
      ------------------------------------------------------------------- */
      Siswa: {
        render(container) {
          const rombel = DB.data.siswa.rombel;
          const prestasi = DB.data.siswa.prestasiDanCatatan;
          const mutasi = DB.data.siswa.rekapMutasi;
          const totalL = rombel.reduce((a, b) => a + Number(b.lakiLaki || 0), 0);
          const totalP = rombel.reduce((a, b) => a + Number(b.perempuan || 0), 0);

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Manajemen Peserta Didik',
                'Rekapitulasi rombongan belajar, komposisi siswa laki-laki & perempuan, kehadiran, prestasi, dan karakter.',
                'fa-solid fa-users',
                `
                  <button onclick="Modules.Siswa.exportExcel()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-file-excel mr-1"></i> Ekspor Rekap Siswa
                  </button>
                `
              )}

              <!-- Ringkasan Statistik Siswa -->
              <div class="grid grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                  <div class="text-xs font-bold text-slate-500">Total Siswa Aktif</div>
                  <div class="text-2xl font-extrabold text-navy-900 mt-1">${totalL + totalP} Siswa</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                  <div class="text-xs font-bold text-slate-500">Laki-Laki / Perempuan</div>
                  <div class="text-2xl font-extrabold text-navy-900 mt-1">${totalL} L • ${totalP} P</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                  <div class="text-xs font-bold text-slate-500">Siswa Baru Tahun Ini</div>
                  <div class="text-2xl font-extrabold text-emerald-700 mt-1">${mutasi.siswaBaru} Siswa</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                  <div class="text-xs font-bold text-slate-500">Lulusan Tahun Lalu</div>
                  <div class="text-2xl font-extrabold text-gold-700 mt-1">${mutasi.lulusanTahunLalu} Lulusan</div>
                </div>
              </div>

              <!-- Data Rombel & Jumlah Siswa -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Data Rombongan Belajar & Kehadiran</h3>
                <form onsubmit="Modules.Siswa.addRombel(event)" class="grid grid-cols-1 sm:grid-cols-5 gap-3 bg-slate-50 p-3.5 rounded-xl border border-slate-200">
                  <input type="text" id="rmb-tingkat" required placeholder="Tingkat (misal: Kelas VII)" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="rmb-nama" required placeholder="Nama Rombel" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="number" id="rmb-l" required placeholder="Jumlah Laki-laki" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="number" id="rmb-p" required placeholder="Jumlah Perempuan" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <button type="submit" class="bg-navy-900 text-white rounded-xl px-4 py-2 text-xs font-bold">Tambah Rombel</button>
                </form>
                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3 rounded-tl-xl">Tingkat</th>
                        <th class="p-3">Rombongan Belajar</th>
                        <th class="p-3 text-center">Laki-Laki</th>
                        <th class="p-3 text-center">Perempuan</th>
                        <th class="p-3 text-center">Total</th>
                        <th class="p-3 text-center">Kehadiran</th>
                        <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${rombel.map(r => `
                        <tr>
                          <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(r.tingkat)}</td>
                          <td class="p-3">${ExportUtils.escape(r.namaRombel)}</td>
                          <td class="p-3 text-center">${r.lakiLaki}</td>
                          <td class="p-3 text-center">${r.perempuan}</td>
                          <td class="p-3 text-center font-extrabold">${Number(r.lakiLaki) + Number(r.perempuan)}</td>
                          <td class="p-3 text-center"><span class="px-2 py-0.5 rounded bg-emerald-100 text-emerald-800 font-bold">${r.kehadiran}%</span></td>
                          <td class="p-3 text-center">
                            <button onclick="Modules.Siswa.deleteRombel('${r.id}')" class="p-1.5 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                          </td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>

              <!-- Prestasi, Ekstrakurikuler & Pembinaan Karakter -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Catatan Prestasi, Ekstrakurikuler & Pembinaan Karakter</h3>
                <form onsubmit="Modules.Siswa.addPrestasi(event)" class="grid grid-cols-1 md:grid-cols-3 gap-3 bg-slate-50 p-3.5 rounded-xl border border-slate-200">
                  <input type="text" id="prs-nama" required placeholder="Nama Siswa / Tim" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="prs-kelas" required placeholder="Kelas" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <select id="prs-jenis" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white">
                    <option value="Prestasi Akademik">Prestasi Akademik</option>
                    <option value="Prestasi Non-Akademik">Prestasi Non-Akademik</option>
                    <option value="Ekstrakurikuler & Karakter">Ekstrakurikuler & Karakter</option>
                  </select>
                  <input type="text" id="prs-judul" required placeholder="Nama Kejuaraan / Kegiatan Pembinaan" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="prs-tingkat" required placeholder="Tingkat (Sekolah/Kota/Provinsi/Nasional)" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <button type="submit" class="bg-navy-900 text-white rounded-xl px-4 py-2 text-xs font-bold">Simpan Catatan Siswa</button>
                </form>
                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3 rounded-tl-xl">Nama Siswa / Tim</th>
                        <th class="p-3">Kelas</th>
                        <th class="p-3">Kategori</th>
                        <th class="p-3">Capaian / Kegiatan</th>
                        <th class="p-3">Tingkat</th>
                        <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${prestasi.map(p => `
                        <tr>
                          <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(p.namaSiswa)}</td>
                          <td class="p-3">${ExportUtils.escape(p.kelas)}</td>
                          <td class="p-3"><span class="px-2 py-0.5 rounded bg-gold-100 text-gold-800 font-bold">${ExportUtils.escape(p.jenis)}</span></td>
                          <td class="p-3">${ExportUtils.escape(p.judul)}</td>
                          <td class="p-3">${ExportUtils.escape(p.tingkat)}</td>
                          <td class="p-3 text-center">
                            <button onclick="Modules.Siswa.deletePrestasi('${p.id}')" class="p-1.5 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                          </td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        addRombel(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.siswa.rombel.push({
            id: DB.uid('rmb'),
            tingkat: document.getElementById('rmb-tingkat').value.trim(),
            namaRombel: document.getElementById('rmb-nama').value.trim(),
            waliKelas: 'Wali Kelas',
            lakiLaki: Number(document.getElementById('rmb-l').value || 0),
            perempuan: Number(document.getElementById('rmb-p').value || 0),
            kehadiran: 96.5
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteRombel(id) {
          if (!UI.canEdit()) return;
          DB.data.siswa.rombel = DB.data.siswa.rombel.filter(r => r.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        addPrestasi(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.siswa.prestasiDanCatatan.unshift({
            id: DB.uid('prs'),
            namaSiswa: document.getElementById('prs-nama').value.trim(),
            kelas: document.getElementById('prs-kelas').value.trim(),
            jenis: document.getElementById('prs-jenis').value,
            judul: document.getElementById('prs-judul').value.trim(),
            tingkat: document.getElementById('prs-tingkat').value.trim(),
            pembina: 'Guru Pembina',
            tanggal: new Date().toISOString().split('T')[0]
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deletePrestasi(id) {
          if (!UI.canEdit()) return;
          DB.data.siswa.prestasiDanCatatan = DB.data.siswa.prestasiDanCatatan.filter(p => p.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        exportExcel() {
          const rows = DB.data.siswa.rombel.map((r, i) => ({
            No: i + 1,
            Tingkat: r.tingkat,
            'Rombongan Belajar': r.namaRombel,
            'Laki-Laki': r.lakiLaki,
            Perempuan: r.perempuan,
            Total: Number(r.lakiLaki) + Number(r.perempuan),
            'Kehadiran (%)': r.kehadiran
          }));
          ExportUtils.exportExcel('Rekap_Peserta_Didik.xlsx', 'Peserta_Didik', rows);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 8: MANAJEMEN SARANA DAN PRASARANA
      ------------------------------------------------------------------- */
      Sarpras: {
        render(container) {
          const list = DB.data.sarpras;
          const totalAset = list.reduce((a, b) => a + Number(b.nilaiAset || 0), 0);

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Manajemen Sarana & Prasarana',
                'Buku inventaris barang, data ruang kelas, kondisi fasilitas, jadwal pemeliharaan, dan usulan perbaikan.',
                'fa-solid fa-building-columns',
                `
                  <button onclick="Modules.Sarpras.printInventaris()" class="px-3.5 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-print text-gold-400 mr-1"></i> Cetak Buku Inventaris
                  </button>
                `
              )}

              <div class="bg-navy-900 text-white p-5 rounded-2xl flex flex-col sm:flex-row sm:items-center justify-between gap-2">
                <div>
                  <div class="text-xs text-gold-400 font-bold uppercase">Total Estimasi Nilai Aset Tercatat</div>
                  <div class="text-2xl font-extrabold mt-0.5">${ExportUtils.formatRupiah(totalAset)}</div>
                </div>
                <div class="text-xs text-slate-300">${list.length} Kelompok Inventaris Terdata</div>
              </div>

              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Tambah Inventaris / Laporan Kerusakan / Usulan Pengadaan</h3>
                <form onsubmit="Modules.Sarpras.addSarpras(event)" class="grid grid-cols-1 md:grid-cols-3 gap-3 bg-slate-50 p-4 rounded-xl border border-slate-200">
                  <input type="text" id="srp-kode" required placeholder="Kode Barang (INV-01)" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="srp-nama" required placeholder="Nama Ruang / Barang / Fasilitas" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="srp-kategori" required placeholder="Kategori (Ruang Kelas/TIK/Mebel)" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="number" id="srp-jumlah" required placeholder="Jumlah Unit" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <select id="srp-kondisi" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white">
                    <option value="Baik">Baik</option>
                    <option value="Rusak Ringan">Rusak Ringan (Perlu Perbaikan)</option>
                    <option value="Rusak Berat">Rusak Berat (Usulan Pengadaan)</option>
                  </select>
                  <input type="text" id="srp-jadwal" required placeholder="Jadwal Pemeliharaan" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="number" id="srp-nilai" placeholder="Nilai Aset (Rp)" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="srp-ket" placeholder="Keterangan / Monitoring Perbaikan" class="md:col-span-2 border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <div class="md:col-span-3">
                    <button type="submit" class="px-5 py-2.5 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Data Sarpras</button>
                  </div>
                </form>

                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3 rounded-tl-xl">Kode</th>
                        <th class="p-3">Nama Sarana/Prasarana</th>
                        <th class="p-3 text-center">Jumlah</th>
                        <th class="p-3 text-center">Kondisi</th>
                        <th class="p-3">Jadwal Pemeliharaan</th>
                        <th class="p-3">Nilai Aset</th>
                        <th class="p-3">Keterangan</th>
                        <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${list.map(s => `
                        <tr>
                          <td class="p-3 font-mono font-bold">${ExportUtils.escape(s.kodeBarang)}</td>
                          <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(s.namaBarang)}</td>
                          <td class="p-3 text-center">${s.jumlah}</td>
                          <td class="p-3 text-center">
                            <span class="px-2 py-0.5 rounded font-bold ${s.kondisi === 'Baik' ? 'bg-emerald-100 text-emerald-800' : 'bg-amber-100 text-amber-800'}">${ExportUtils.escape(s.kondisi)}</span>
                          </td>
                          <td class="p-3">${ExportUtils.escape(s.jadwalPemeliharaan)}</td>
                          <td class="p-3">${ExportUtils.formatRupiah(s.nilaiAset)}</td>
                          <td class="p-3">${ExportUtils.escape(s.keterangan)}</td>
                          <td class="p-3 text-center">
                            <button onclick="Modules.Sarpras.deleteSarpras('${s.id}')" class="p-1.5 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                          </td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        addSarpras(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.sarpras.unshift({
            id: DB.uid('srp'),
            kodeBarang: document.getElementById('srp-kode').value.trim(),
            namaBarang: document.getElementById('srp-nama').value.trim(),
            kategori: document.getElementById('srp-kategori').value.trim(),
            jumlah: Number(document.getElementById('srp-jumlah').value || 1),
            kondisi: document.getElementById('srp-kondisi').value,
            jadwalPemeliharaan: document.getElementById('srp-jadwal').value.trim(),
            nilaiAset: Number(document.getElementById('srp-nilai').value || 0),
            keterangan: document.getElementById('srp-ket').value.trim() || '-'
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteSarpras(id) {
          if (!UI.canEdit()) return;
          DB.data.sarpras = DB.data.sarpras.filter(s => s.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        printInventaris() {
          const html = `
            <h2 style="text-align:center;text-transform:uppercase;">BUKU INVENTARIS SARANA & PRASARANA SEKOLAH</h2>
            <table style="width:100%;border-collapse:collapse;font-size:10.5pt;">
              <tr><th style="border:1px solid #000;padding:6px;">Kode</th><th style="border:1px solid #000;padding:6px;">Nama Barang/Ruang</th><th style="border:1px solid #000;padding:6px;">Jumlah</th><th style="border:1px solid #000;padding:6px;">Kondisi</th><th style="border:1px solid #000;padding:6px;">Nilai Aset</th></tr>
              ${DB.data.sarpras.map(s => `<tr><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(s.kodeBarang)}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(s.namaBarang)}</td><td style="border:1px solid #000;padding:6px;">${s.jumlah}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(s.kondisi)}</td><td style="border:1px solid #000;padding:6px;">${ExportUtils.formatRupiah(s.nilaiAset)}</td></tr>`).join('')}
            </table>
          `;
          ExportUtils.printDocument('Inventaris_Sarpras', html, true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 9: MANAJEMEN KURIKULUM & PEMBELAJARAN
      ------------------------------------------------------------------- */
      Kurikulum: {
        render(container) {
          const list = DB.data.kurikulum;
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Manajemen Kurikulum & Pembelajaran',
                'Kalender pendidikan, program kurikulum, pembagian tugas mengajar, dan peningkatan mutu pembelajaran.',
                'fa-solid fa-book-open-reader'
              )}

              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Tambah Program / Monitoring Kurikulum</h3>
                <form onsubmit="Modules.Kurikulum.addItem(event)" class="grid grid-cols-1 md:grid-cols-3 gap-3 bg-slate-50 p-4 rounded-xl border border-slate-200">
                  <select id="kur-aspek" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white">
                    <option value="Kalender Pendidikan">Kalender Pendidikan</option>
                    <option value="Program Kurikulum">Program Kurikulum</option>
                    <option value="Jadwal & Pembagian Tugas Mengajar">Jadwal & Pembagian Tugas Mengajar</option>
                    <option value="Monitoring & Evaluasi Pembelajaran">Monitoring & Evaluasi Pembelajaran</option>
                    <option value="Program Peningkatan Mutu Pembelajaran">Program Peningkatan Mutu Pembelajaran</option>
                  </select>
                  <input type="text" id="kur-nama" required placeholder="Nama Program / Kegiatan Kurikulum" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="kur-pj" required placeholder="Penanggung Jawab" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="kur-periode" required placeholder="Periode Pelaksanaan" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="kur-catatan" placeholder="Catatan / Evaluasi" class="md:col-span-2 border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <div class="md:col-span-3">
                    <button type="submit" class="px-5 py-2.5 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Program Kurikulum</button>
                  </div>
                </form>

                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3 rounded-tl-xl">Aspek</th>
                        <th class="p-3">Program Kurikulum</th>
                        <th class="p-3">Penanggung Jawab</th>
                        <th class="p-3">Periode</th>
                        <th class="p-3">Catatan</th>
                        <th class="p-3 rounded-tr-xl text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${list.map(k => `
                        <tr>
                          <td class="p-3"><span class="px-2 py-0.5 rounded bg-navy-100 text-navy-800 font-bold">${ExportUtils.escape(k.aspek)}</span></td>
                          <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(k.namaProgram)}</td>
                          <td class="p-3">${ExportUtils.escape(k.penanggungJawab)}</td>
                          <td class="p-3">${ExportUtils.escape(k.periode)}</td>
                          <td class="p-3">${ExportUtils.escape(k.catatan)}</td>
                          <td class="p-3 text-center">
                            <button onclick="Modules.Kurikulum.deleteItem('${k.id}')" class="p-1.5 text-rose-600 hover:bg-rose-50 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                          </td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        addItem(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.kurikulum.unshift({
            id: DB.uid('kur'),
            aspek: document.getElementById('kur-aspek').value,
            namaProgram: document.getElementById('kur-nama').value.trim(),
            penanggungJawab: document.getElementById('kur-pj').value.trim(),
            periode: document.getElementById('kur-periode').value.trim(),
            status: 'Berjalan',
            catatan: document.getElementById('kur-catatan').value.trim() || '-'
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteItem(id) {
          if (!UI.canEdit()) return;
          DB.data.kurikulum = DB.data.kurikulum.filter(k => k.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        }
      },

      /* -------------------------------------------------------------------
         MODUL 10: KEPEMIMPINAN & PENGEMBANGAN SEKOLAH
      ------------------------------------------------------------------- */
      Kepemimpinan: {
        render(container) {
          const kp = DB.data.kepemimpinan;
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Kepemimpinan & Pengembangan Sekolah',
                'Visi, misi, tujuan sekolah, program budaya positif, kemitraan komite, inovasi, dan sekolah sehat.',
                'fa-solid fa-award'
              )}

              <!-- Visi Misi Tujuan Editable -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Visi, Misi & Tujuan Satuan Pendidikan</h3>
                <form onsubmit="Modules.Kepemimpinan.saveVisiMisi(event)" class="space-y-3">
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1">Visi Sekolah</label>
                    <textarea id="kp-visi" rows="2" class="w-full border border-slate-300 rounded-xl p-3 text-xs font-semibold text-navy-900">${ExportUtils.escape(kp.visi)}</textarea>
                  </div>
                  <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Misi Sekolah</label>
                      <textarea id="kp-misi" rows="4" class="w-full border border-slate-300 rounded-xl p-3 text-xs">${ExportUtils.escape(kp.misi)}</textarea>
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Tujuan Sekolah</label>
                      <textarea id="kp-tujuan" rows="4" class="w-full border border-slate-300 rounded-xl p-3 text-xs">${ExportUtils.escape(kp.tujuan)}</textarea>
                    </div>
                  </div>
                  <button type="submit" class="px-5 py-2.5 bg-navy-900 text-white rounded-xl text-xs font-bold">
                    <i class="fa-solid fa-floppy-disk text-gold-400 mr-1"></i> Simpan Visi, Misi & Tujuan
                  </button>
                </form>
              </div>

              <!-- Program Budaya Positif, Kemitraan & Sekolah Sehat -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Program Budaya Positif, Kemitraan Komite, Inovasi & Sekolah Sehat</h3>
                <form onsubmit="Modules.Kepemimpinan.addProgram(event)" class="grid grid-cols-1 md:grid-cols-4 gap-3 bg-slate-50 p-4 rounded-xl border border-slate-200">
                  <select id="kp-kat" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white">
                    <option value="Budaya Positif & Karakter">Budaya Positif & Karakter</option>
                    <option value="Kemitraan & Komite Sekolah">Kemitraan & Komite Sekolah</option>
                    <option value="Inovasi & Peningkatan Mutu">Inovasi & Peningkatan Mutu</option>
                    <option value="Sekolah Sehat & Aman">Sekolah Sehat & Aman</option>
                  </select>
                  <input type="text" id="kp-prog-nama" required placeholder="Nama Program Unggulan" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="kp-mitra" required placeholder="Mitra / Pelaksana" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <button type="submit" class="bg-navy-900 text-white rounded-xl px-4 py-2 text-xs font-bold">Tambah Program</button>
                </form>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                  ${kp.programStrategis.map(p => `
                    <div class="p-4 rounded-2xl border border-slate-200 bg-slate-50/70 flex flex-col justify-between">
                      <div>
                        <span class="px-2.5 py-0.5 rounded-full bg-gold-100 text-gold-800 text-[11px] font-bold">${ExportUtils.escape(p.kategori)}</span>
                        <h4 class="font-extrabold text-navy-900 text-sm mt-2">${ExportUtils.escape(p.namaProgram)}</h4>
                        <p class="text-xs text-slate-600 mt-1">Mitra: ${ExportUtils.escape(p.mitra)}</p>
                        <p class="text-xs text-emerald-700 mt-1">${ExportUtils.escape(p.dampak)}</p>
                      </div>
                      <div class="mt-3 pt-2 border-t border-slate-200 flex justify-end">
                        <button onclick="Modules.Kepemimpinan.deleteProgram('${p.id}')" class="text-xs text-rose-600 hover:underline">Hapus</button>
                      </div>
                    </div>
                  `).join('')}
                </div>
              </div>
            </div>
          `;
        },

        saveVisiMisi(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.kepemimpinan.visi = document.getElementById('kp-visi').value.trim();
          DB.data.kepemimpinan.misi = document.getElementById('kp-misi').value.trim();
          DB.data.kepemimpinan.tujuan = document.getElementById('kp-tujuan').value.trim();
          DB.save(true);
        },

        addProgram(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.kepemimpinan.programStrategis.unshift({
            id: DB.uid('kp'),
            kategori: document.getElementById('kp-kat').value,
            namaProgram: document.getElementById('kp-prog-nama').value.trim(),
            mitra: document.getElementById('kp-mitra').value.trim(),
            dampak: 'Meningkatkan budaya positif dan mutu layanan pendidikan',
            status: 'Aktif'
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteProgram(id) {
          if (!UI.canEdit()) return;
          DB.data.kepemimpinan.programStrategis = DB.data.kepemimpinan.programStrategis.filter(x => x.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        }
      },

      /* -------------------------------------------------------------------
         MODUL 11: MONITORING DAN EVALUASI
      ------------------------------------------------------------------- */
      Monev: {
        aiSummary: '',

        render(container) {
          const list = DB.data.monev;
          const avgCapaian = list.length > 0
            ? Math.round(list.reduce((a, b) => a + Number(b.capaianPersen || 0), 0) / list.length)
            : 0;

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Monitoring & Evaluasi Program Sekolah',
                'Pemantauan capaian program bulanan, semester, dan tahunan lengkap dengan grafik serta analisis tindak lanjut AI.',
                'fa-solid fa-magnifying-glass-chart'
              )}

              <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <div class="lg:col-span-2 bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <h3 class="text-base font-extrabold text-navy-900 mb-3">Grafik Persentase Capaian Program Sekolah</h3>
                  <div class="h-60 w-full">
                    <canvas id="monev-chart"></canvas>
                  </div>
                </div>

                <div class="bg-gradient-to-br from-navy-900 to-navy-800 text-white rounded-2xl p-6 shadow-md flex flex-col justify-between">
                  <div>
                    <div class="text-xs font-bold text-gold-400 uppercase">Rata-Rata Capaian Monev</div>
                    <div class="text-4xl font-extrabold mt-2">${avgCapaian}%</div>
                    <p class="text-xs text-slate-300 mt-2">Berdasarkan ${list.length} instrumen evaluasi program sekolah yang dipantau.</p>
                  </div>
                  <button onclick="Modules.Monev.analyzeWithAi()" class="mt-6 w-full py-3 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold shadow flex items-center justify-center gap-2">
                    <i class="fa-solid fa-robot"></i> Analisis Capaian & Saran AI
                  </button>
                </div>
              </div>

              ${this.aiSummary ? `
                <div class="bg-navy-50 border-l-4 border-gold-500 rounded-2xl p-5 text-xs sm:text-sm text-navy-900 whitespace-pre-line leading-relaxed shadow-sm">
                  <div class="font-extrabold text-navy-900 mb-1.5 flex items-center gap-2">
                    <i class="fa-solid fa-wand-magic-sparkles text-gold-600"></i> Analisis & Rekomendasi Tindak Lanjut AI:
                  </div>
                  ${ExportUtils.escape(this.aiSummary)}
                </div>
              ` : ''}

              <!-- Form Tambah Evaluasi -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Input Data Monitoring & Evaluasi</h3>
                <form onsubmit="Modules.Monev.addMonev(event)" class="grid grid-cols-1 md:grid-cols-3 gap-3 bg-slate-50 p-4 rounded-xl border border-slate-200">
                  <select id="mnv-periode" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white">
                    <option value="Evaluasi Bulanan">Evaluasi Bulanan</option>
                    <option value="Evaluasi Semester">Evaluasi Semester</option>
                    <option value="Evaluasi Tahunan">Evaluasi Tahunan</option>
                  </select>
                  <input type="text" id="mnv-program" required placeholder="Nama Program yang Dievaluasi" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="number" id="mnv-capaian" min="0" max="100" required placeholder="Realisasi Capaian (%)" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="mnv-kendala" required placeholder="Kendala Pelaksanaan" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <input type="text" id="mnv-tl" required placeholder="Rencana Tindak Lanjut" class="border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white" />
                  <button type="submit" class="bg-navy-900 text-white rounded-xl px-4 py-2 text-xs font-bold">Simpan Evaluasi</button>
                </form>

                <div class="space-y-3">
                  ${list.map(m => `
                    <div class="p-4 rounded-2xl border border-slate-200 bg-white">
                      <div class="flex flex-wrap items-center justify-between gap-2 mb-2">
                        <div>
                          <span class="px-2.5 py-0.5 rounded-full bg-navy-100 text-navy-800 text-[11px] font-bold">${ExportUtils.escape(m.periode)}</span>
                          <h4 class="font-extrabold text-navy-900 text-sm mt-1">${ExportUtils.escape(m.namaProgram)}</h4>
                        </div>
                        <div class="flex items-center gap-3">
                          <span class="text-sm font-extrabold text-gold-700">${m.capaianPersen}% / ${m.targetPersen}%</span>
                          <button onclick="Modules.Monev.deleteMonev('${m.id}')" class="text-rose-600 hover:bg-rose-50 p-1.5 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                        </div>
                      </div>
                      <div class="w-full bg-slate-100 h-2 rounded-full overflow-hidden mb-2">
                        <div class="bg-gradient-to-r from-navy-800 to-gold-500 h-2" style="width:${Math.min(100, m.capaianPersen)}%"></div>
                      </div>
                      <div class="grid grid-cols-1 md:grid-cols-2 gap-2 text-xs text-slate-600">
                        <div><strong>Kendala:</strong> ${ExportUtils.escape(m.kendala)}</div>
                        <div><strong>Tindak Lanjut:</strong> ${ExportUtils.escape(m.tindakLanjut)}</div>
                      </div>
                    </div>
                  `).join('')}
                </div>
              </div>
            </div>
          `;
          this.initChart();
        },

        initChart() {
          const ctx = document.getElementById('monev-chart');
          if (!ctx || typeof Chart === 'undefined') return;
          if (App.charts.monev) App.charts.monev.destroy();
          const list = DB.data.monev;
          App.charts.monev = new Chart(ctx, {
            type: 'bar',
            data: {
              labels: list.map(m => m.namaProgram.substring(0, 26) + (m.namaProgram.length > 26 ? '...' : '')),
              datasets: [{
                label: 'Capaian (%)',
                data: list.map(m => m.capaianPersen),
                backgroundColor: '#1e3a8a',
                borderRadius: 8
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false,
              scales: { y: { beginAtZero: true, max: 100 } },
              plugins: { legend: { display: false } }
            }
          });
        },

        addMonev(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.monev.unshift({
            id: DB.uid('mnv'),
            periode: document.getElementById('mnv-periode').value,
            namaProgram: document.getElementById('mnv-program').value.trim(),
            targetPersen: 100,
            capaianPersen: Number(document.getElementById('mnv-capaian').value || 0),
            kendala: document.getElementById('mnv-kendala').value.trim(),
            tindakLanjut: document.getElementById('mnv-tl').value.trim()
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteMonev(id) {
          if (!UI.canEdit()) return;
          DB.data.monev = DB.data.monev.filter(m => m.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        async analyzeWithAi() {
          const list = DB.data.monev;
          const summaryData = list.map(m => `- ${m.namaProgram} (${m.periode}): Capaian ${m.capaianPersen}%, Kendala: ${m.kendala}`).join('\n');
          UI.toast('AI sedang menganalisis data monitoring...', 'info');
          const res = await AI.generate(
            `Berikan analisis evaluasi capaian program sekolah berikut, identifikasi program yang belum optimal (<80%), dan susun 3 langkah tindak lanjut konkret untuk kepala sekolah:\n${summaryData}`
          );
          this.aiSummary = res ||
            `1. Evaluasi Umum: Rata-rata keterlaksanaan program berjalan baik, namun program dengan capaian di bawah 80% memerlukan penjadwalan ulang dan penguatan koordinasi tim.\n2. Identifikasi Hambatan: Bentrok jadwal kegiatan eksternal guru dan tahapan pencairan anggaran pemeliharaan.\n3. Saran Tindak Lanjut: Terapkan pemantauan dua mingguan (bi-weekly check-in) bersama koordinator bidang dan prioritaskan kegiatan berdampak langsung pada mutu pembelajaran siswa.`;
          this.render(document.getElementById('app-content'));
        }
      },

      /* -------------------------------------------------------------------
         MODUL 12: GENERATOR DOKUMEN OTOMATIS BERBANTUAN AI (18 JENIS DOKUMEN)
      ------------------------------------------------------------------- */
      GeneratorAI: {
        docTypes: [
          'Surat Keputusan (SK)',
          'Surat Tugas',
          'Surat Undangan',
          'Surat Edaran',
          'Surat Keterangan',
          'Berita Acara',
          'Daftar Hadir',
          'Notulen Rapat',
          'Proposal Kegiatan',
          'Laporan Kegiatan',
          'Laporan Supervisi',
          'Laporan Pembinaan Guru',
          'Laporan Monitoring',
          'Laporan Evaluasi',
          'Program Kerja Kepala Sekolah',
          'Program Tahunan',
          'Program Semester',
          'Laporan Pertanggungjawaban (LPJ)'
        ],
        selectedType: 'Surat Keputusan (SK)',
        generatedHtml: '',
        docDateStr: '',
        isGenerating: false,

        render(container) {
          const s = DB.data.settings;
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Generator Dokumen Otomatis AI',
                'Susun 18 jenis dokumen resmi administrasi kepala sekolah lengkap dengan Kop Surat dan tanda tangan.',
                'fa-solid fa-wand-magic-sparkles'
              )}

              ${UI.guideBanner(
                'Cara Membuat Dokumen Resmi Otomatis',
                '1) Pilih jenis dokumen yang dibutuhkan. 2) Isi kolom nomor surat, tanggal, tujuan/penerima, serta pokok materi (jangan dikosongkan agar tidak dibuat sembarangan). 3) Klik "Susun Dokumen dengan AI", lalu edit hasilnya jika perlu, simpan ke Arsip Digital, unduh Word, atau cetak.'
              )}

              <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                <!-- Form Input Parameter -->
                <div class="lg:col-span-5 bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                  <h3 class="text-base font-extrabold text-navy-900 border-b border-slate-100 pb-3">Parameter Dokumen</h3>
                  <form onsubmit="Modules.GeneratorAI.generateDoc(event)" class="space-y-3.5">
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">1. Pilih Jenis Dokumen</label>
                      <select id="gen-type" onchange="Modules.GeneratorAI.selectedType = this.value" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs font-semibold bg-white">
                        ${this.docTypes.map(t => `<option value="${t}" ${this.selectedType === t ? 'selected' : ''}>${t}</option>`).join('')}
                      </select>
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">2. Nomor Surat / Dokumen (Isi Manual / Kosongkan untuk [Isi Nomor])</label>
                      <input type="text" id="gen-nomor" placeholder="Contoh: 421.3/112/SMPN1/XI/2026" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">3. Tanggal Dokumen</label>
                      <input type="text" id="gen-tanggal" placeholder="Contoh: 23 November 2026" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">4. Judul / Perihal / Nama Kegiatan</label>
                      <input type="text" id="gen-perihal" required placeholder="Contoh: Penetapan Tim Pengembang Kurikulum Sekolah" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">5. Pihak Sasaran / Penerima / Peserta</label>
                      <input type="text" id="gen-sasaran" required placeholder="Contoh: Dewan Guru dan Staf Tata Usaha" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">6. Rincian Isi / Waktu / Tempat / Keterangan Tambahan</label>
                      <textarea id="gen-detail" rows="4" required placeholder="Tuliskan poin-poin penting yang harus dimuat dalam dokumen..." class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs"></textarea>
                    </div>

                    <button type="submit" ${this.isGenerating ? 'disabled' : ''} class="w-full py-3 bg-gradient-to-r from-navy-900 to-navy-800 hover:from-navy-800 hover:to-navy-700 text-white rounded-xl text-xs font-extrabold shadow-md flex items-center justify-center gap-2">
                      <i class="fa-solid ${this.isGenerating ? 'fa-spinner fa-spin' : 'fa-wand-magic-sparkles'} text-gold-400"></i>
                      <span>${this.isGenerating ? 'Menyusun Dokumen Resmi...' : 'Susun Dokumen Otomatis dengan AI'}</span>
                    </button>
                  </form>
                </div>

                <!-- Live Editable Official Preview -->
                <div class="lg:col-span-7 bg-white rounded-2xl p-6 border border-slate-200 shadow-sm flex flex-col">
                  <div class="flex flex-wrap items-center justify-between gap-2 pb-3 mb-4 border-b border-slate-200">
                    <h3 class="text-base font-extrabold text-navy-900">Pratinjau & Editor Naskah Resmi</h3>
                    <div class="flex flex-wrap gap-2">
                      <button onclick="Modules.GeneratorAI.saveToArchive()" class="px-3 py-1.5 bg-emerald-700 hover:bg-emerald-800 text-white rounded-lg text-xs font-bold">
                        <i class="fa-solid fa-box-archive mr-1"></i> Simpan Arsip
                      </button>
                      <button onclick="Modules.GeneratorAI.downloadWord()" class="px-3 py-1.5 bg-navy-900 hover:bg-navy-800 text-white rounded-lg text-xs font-bold">
                        <i class="fa-solid fa-file-word text-gold-400 mr-1"></i> Word
                      </button>
                      <button onclick="Modules.GeneratorAI.printDoc()" class="px-3 py-1.5 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-lg text-xs font-bold">
                        <i class="fa-solid fa-print mr-1"></i> Cetak / PDF
                      </button>
                    </div>
                  </div>

                  <!-- Official Paper Sheet -->
                  <div class="border border-slate-300 rounded-xl p-5 sm:p-8 bg-white shadow-inner flex-1 overflow-y-auto max-h-[680px]">
                    ${ExportUtils.getKopSuratHTML()}
                    <div id="gen-editable-body" contenteditable="true" class="outline-none font-serif text-sm leading-relaxed text-black min-h-[260px]">
                      ${this.generatedHtml || `
                        <p class="text-slate-400 italic font-sans text-xs text-center py-12">
                          Isi parameter di sebelah kiri lalu klik <strong>"Susun Dokumen Otomatis dengan AI"</strong>. Naskah yang dihasilkan dapat langsung Anda klik dan edit di area ini sebelum dicetak atau diunduh.
                        </p>
                      `}
                    </div>
                    ${ExportUtils.getSignatureHTML(this.docDateStr)}
                  </div>
                </div>
              </div>
            </div>
          `;
        },

        async generateDoc(e) {
          e.preventDefault();
          const s = DB.data.settings;
          const jenis = document.getElementById('gen-type').value;
          const nomor = document.getElementById('gen-nomor').value.trim() || '[Nomor Surat Belum Diisi]';
          const tanggal = document.getElementById('gen-tanggal').value.trim() || '[Tanggal Belum Diisi]';
          const perihal = document.getElementById('gen-perihal').value.trim();
          const sasaran = document.getElementById('gen-sasaran').value.trim();
          const detail = document.getElementById('gen-detail').value.trim();

          this.selectedType = jenis;
          this.docDateStr = tanggal;
          this.isGenerating = true;
          this.render(document.getElementById('app-content'));

          const prompt = `Susun isi dokumen administrasi sekolah resmi dengan ketentuan berikut:
Jenis Dokumen: ${jenis}
Nama Sekolah: ${s.schoolName} (NPSN: ${s.npsn})
Kepala Sekolah: ${s.principalName} (NIP: ${s.principalNip})
Tahun Pelajaran: ${s.academicYear}
Nomor Dokumen: ${nomor}
Tanggal Dokumen: ${tanggal}
Judul/Perihal: ${perihal}
Sasaran/Penerima: ${sasaran}
Rincian Materi: ${detail}

ATURAN PENTING:
1. Jangan sertakan Kop Surat dan jangan sertakan Blok Tanda Tangan di akhir karena sudah ditambahkan otomatis oleh sistem.
2. Kembalikan langsung dalam format HTML bersih (<h3 style="text-align:center">...</h3>, <p>, <ol>, <table>) tanpa blok markdown \`\`\`html.
3. Gunakan struktur tata naskah dinas pendidikan Indonesia yang baku, rapi, sopan, dan lengkap.`;

          const aiHtml = await AI.generate(prompt);
          if (aiHtml) {
            this.generatedHtml = aiHtml.replace(/```html|```/g, '').trim();
          } else {
            this.generatedHtml = `
              <h3 style="text-align:center;text-transform:uppercase;margin-bottom:2px;">${ExportUtils.escape(jenis)}</h3>
              <p style="text-align:center;margin-top:0;">Nomor: ${ExportUtils.escape(nomor)}</p>
              <p><strong>Perihal / Judul:</strong> ${ExportUtils.escape(perihal)}<br/>
              <strong>Kepada / Sasaran:</strong> ${ExportUtils.escape(sasaran)}</p>
              <p>Dengan hormat,</p>
              <p>Dalam rangka mendukung kelancaran penyelenggaraan program pendidikan dan tata kelola administrasi di <strong>${ExportUtils.escape(s.schoolName)}</strong> Tahun Pelajaran ${ExportUtils.escape(s.academicYear)}, bersama ini kami sampaikan hal-hal sebagai berikut:</p>
              <p>${ExportUtils.escape(detail).replace(/\n/g, '<br/>')}</p>
              <p>Demikian ${ExportUtils.escape(jenis.toLowerCase())} ini dibuat untuk dilaksanakan dengan penuh tanggung jawab dan sebagaimana mestinya.</p>
            `;
          }

          this.isGenerating = false;
          UI.toast('Dokumen resmi berhasil disusun! Anda dapat mengedit teksnya secara langsung.', 'success');
          this.render(document.getElementById('app-content'));
        },

        getCurrentEditedHtml() {
          const el = document.getElementById('gen-editable-body');
          return el ? el.innerHTML : this.generatedHtml;
        },

        saveToArchive() {
          if (!UI.canEdit()) return;
          const bodyHtml = this.getCurrentEditedHtml();
          if (!bodyHtml || bodyHtml.includes('Isi parameter di sebelah kiri')) {
            UI.toast('Susun dokumen terlebih dahulu sebelum menyimpan ke arsip.', 'warning');
            return;
          }
          const perihal = document.getElementById('gen-perihal')?.value.trim() || this.selectedType;
          const nomor = document.getElementById('gen-nomor')?.value.trim() || '-';
          DB.data.arsipDokumen.unshift({
            id: DB.uid('arc'),
            judul: `${this.selectedType} — ${perihal}`,
            kategori: 'Administrasi Umum',
            nomorSurat: nomor,
            tanggal: new Date().toISOString().split('T')[0],
            konten: bodyHtml.replace(/<[^>]*>?/gm, ' ').replace(/\s+/g, ' ').trim()
          });
          DB.save(false);
          UI.toast('Dokumen berhasil disimpan ke Arsip Digital!', 'success');
        },

        downloadWord() {
          const bodyHtml = this.getCurrentEditedHtml();
          ExportUtils.downloadWord(`${this.selectedType.replace(/\s+/g, '_')}`, bodyHtml, true, true, this.docDateStr);
        },

        printDoc() {
          const bodyHtml = this.getCurrentEditedHtml();
          ExportUtils.printDocument(this.selectedType, bodyHtml, true, true, this.docDateStr);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 13: ASISTEN KEPALA SEKOLAH AI (CHATBOT INTERAKTIF)
      ------------------------------------------------------------------- */
      ChatbotAI: {
        messages: [
          {
            role: 'ai',
            text: 'Assalamu’alaikum Warahmatullahi Wabarakatuh / Salam Sejahtera, Bapak/Ibu Kepala Sekolah.\n\nSaya adalah **Asisten Kepala Sekolah Master AI**. Saya siap membantu Anda menyusun program kerja, merancang supervisi akademik, menindaklanjuti hasil supervisi guru, menganalisis Rapor Pendidikan, hingga menyusun RKJM, RKT, dan laporan kegiatan sekolah.\n\nSilakan pilih pertanyaan cepat di bawah atau ketik pertanyaan Anda.'
          }
        ],
        isTyping: false,

        quickQuestions: [
          'Bagaimana menyusun program kerja kepala sekolah?',
          'Bagaimana membuat program supervisi?',
          'Apa tindak lanjut hasil supervisi guru?',
          'Bagaimana menyusun RKJM dan RKT?',
          'Bagaimana menganalisis hasil Rapor Pendidikan?',
          'Bagaimana membuat laporan kegiatan sekolah?',
          'Apa saja administrasi yang perlu disiapkan?',
          'Bagaimana meningkatkan mutu pembelajaran?',
          'Bagaimana menyusun program peningkatan kompetensi guru?'
        ],

        render(container) {
          container.innerHTML = `
            <div class="animate-fade-in space-y-5">
              ${UI.moduleHeader(
                'Asisten Kepala Sekolah AI',
                'Konsultan digital kepemimpinan sekolah berbasis konteks pendidikan Indonesia.',
                'fa-solid fa-robot',
                `
                  <button onclick="Modules.ChatbotAI.clearChat()" class="px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-slate-700 rounded-xl text-xs font-bold">
                    <i class="fa-solid fa-rotate-right mr-1"></i> Bersihkan Percakapan
                  </button>
                `
              )}

              <!-- Quick Prompts -->
              <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm">
                <div class="text-xs font-bold text-navy-900 mb-2.5 flex items-center gap-1.5">
                  <i class="fa-solid fa-sparkles text-gold-600"></i> Pertanyaan Cepat Kepala Sekolah:
                </div>
                <div class="flex flex-wrap gap-2">
                  ${this.quickQuestions.map((q, idx) => `
                    <button onclick="Modules.ChatbotAI.askQuick(${idx})" class="text-left text-xs bg-navy-50 hover:bg-navy-900 text-navy-900 hover:text-gold-300 border border-navy-200 px-3 py-1.5 rounded-xl transition font-medium">
                      ${q}
                    </button>
                  `).join('')}
                </div>
              </div>

              <!-- Chat Window -->
              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm flex flex-col h-[500px]">
                <div id="chat-messages-box" class="flex-1 overflow-y-auto p-5 space-y-4">
                  ${this.messages.map(m => {
                    if (m.role === 'user') {
                      return `
                        <div class="flex justify-end">
                          <div class="max-w-[82%] bg-navy-900 text-white rounded-2xl rounded-tr-sm px-4 py-3 text-xs sm:text-sm shadow-sm whitespace-pre-line leading-relaxed">
                            ${ExportUtils.escape(m.text)}
                          </div>
                        </div>
                      `;
                    }
                    return `
                      <div class="flex items-start gap-3">
                        <div class="w-9 h-9 rounded-xl bg-gradient-to-br from-gold-400 to-gold-600 text-navy-950 flex items-center justify-center shrink-0 shadow">
                          <i class="fa-solid fa-robot"></i>
                        </div>
                        <div class="max-w-[85%] bg-slate-50 border border-slate-200 text-slate-800 rounded-2xl rounded-tl-sm px-4 py-3 text-xs sm:text-sm whitespace-pre-line leading-relaxed">
                          ${this.formatMarkdownBasic(m.text)}
                        </div>
                      </div>
                    `;
                  }).join('')}
                  ${this.isTyping ? `
                    <div class="flex items-center gap-2 text-xs text-slate-500 italic pl-12">
                      <i class="fa-solid fa-circle-notch fa-spin text-gold-600"></i> Asisten AI sedang menyusun jawaban sistematis...
                    </div>
                  ` : ''}
                </div>

                <form onsubmit="Modules.ChatbotAI.sendMessage(event)" class="p-3.5 border-t border-slate-200 bg-slate-50 rounded-b-2xl flex gap-2">
                  <input type="text" id="chat-input" required placeholder="Ketik pertanyaan seputar kepemimpinan, supervisi, RKAS, atau administrasi..." class="flex-1 border border-slate-300 focus:border-navy-800 rounded-xl px-4 py-3 text-xs sm:text-sm bg-white outline-none" />
                  <button type="submit" ${this.isTyping ? 'disabled' : ''} class="px-5 py-3 bg-navy-900 hover:bg-navy-800 text-gold-400 rounded-xl text-xs sm:text-sm font-extrabold shadow flex items-center gap-2 shrink-0">
                    <span>Kirim</span> <i class="fa-solid fa-paper-plane"></i>
                  </button>
                </form>
              </div>
            </div>
          `;
          const box = document.getElementById('chat-messages-box');
          if (box) box.scrollTop = box.scrollHeight;
        },

        formatMarkdownBasic(str) {
          return ExportUtils.escape(str).replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
        },

        askQuick(idx) {
          const q = this.quickQuestions[idx];
          if (!q) return;
          this.processQuery(q);
        },

        sendMessage(e) {
          e.preventDefault();
          const input = document.getElementById('chat-input');
          const text = input.value.trim();
          if (!text) return;
          input.value = '';
          this.processQuery(text);
        },

        async processQuery(userText) {
          this.messages.push({ role: 'user', text: userText });
          this.isTyping = true;
          this.render(document.getElementById('app-content'));

          const s = DB.data.settings;
          const contextInfo = `Konteks Sekolah Pengguna: ${s.schoolName} (Jenjang ${s.jenjang}), Kepala Sekolah: ${s.principalName}, Tahun Pelajaran: ${s.academicYear}, Jumlah GTK: ${DB.data.gtk.length}.`;

          const aiReply = await AI.generate(
            `${contextInfo}\n\nPertanyaan Kepala Sekolah: ${userText}\n\nBerikan jawaban praktis, sistematis (berpoin), dan sesuai regulasi pendidikan Indonesia. Jika membutuhkan dokumen/data spesifik yang belum tersedia, jelaskan data apa yang diperlukan tanpa mengarang.`
          );

          const finalReply = aiReply || this.getKnowledgeFallback(userText);
          this.messages.push({ role: 'ai', text: finalReply });
          this.isTyping = false;
          this.render(document.getElementById('app-content'));
        },

        getKnowledgeFallback(q) {
          const low = q.toLowerCase();
          if (low.includes('program kerja')) {
            return `**Langkah Sistematis Menyusun Program Kerja Kepala Sekolah:**\n1. **Evaluasi Diri & Rapor Pendidikan:** Identifikasi capaian dan akar masalah tahun sebelumnya.\n2. **Penyelarasan Visi-Misi:** Hubungkan target jangka menengah (RKJM) ke sasaran tahunan (RKT).\n3. **Pemetaan 3 Bidang Utama Kepemimpinan:**\n   - Pengembangan Pembelajaran & Kurikulum\n   - Manajemen Sumber Daya (GTK, Sarpras, Keuangan/RKAS)\n   - Kewirausahaan, Inovasi & Kemitraan Sekolah\n4. **Penetapan Jadwal & Indikator:** Tentukan penanggung jawab, lini masa bulanan, dan ukuran keberhasilan.`;
          }
          if (low.includes('supervisi')) {
            return `**Panduan Program & Tindak Lanjut Supervisi Akademik:**\n1. **Perencanaan:** Susun SK Tim Supervisi, jadwal observasi kelas, dan sepakati instrumen bersama guru.\n2. **Pelaksanaan (3 Tahap):** Pra-observasi (telaah modul ajar), Observasi kelas (proses & asesmen), dan Pasca-observasi (coaching reflektif).\n3. **Tindak Lanjut:** Kelompokkan kebutuhan guru untuk pendampingan individual, berbagi praktik baik di Komunitas Belajar (Kombel), atau pelatihan (IHT).`;
          }
          return `**Panduan Praktis Kepemimpinan Sekolah:**\n1. **Identifikasi Kebutuhan:** Pastikan data dukung sekolah (Rapor Pendidikan, data GTK, siswa, dan sarpras) telah terinput pada menu aplikasi.\n2. **Perencanaan Terukur:** Tuangkan program prioritas ke dalam RKT dan kertas kerja RKAS sesuai skala prioritas mutu.\n3. **Pelaksanaan & Monev:** Lakukan pemantauan berkala bulanan serta dokumentasikan seluruh kegiatan dalam arsip digital sekolah.\n\n*Catatan: Jika Anda ingin menyusun draf surat atau laporan spesifik, silakan berikan rincian nama kegiatan, tanggal, dan sasarannya agar tidak ada data faktual yang dibuat sembarangan.*`;
        },

        clearChat() {
          this.messages = [this.messages[0]];
          this.render(document.getElementById('app-content'));
        }
      },

      /* -------------------------------------------------------------------
         MODUL 14: PUSAT TEMPLATE ADMINISTRASI
      ------------------------------------------------------------------- */
      TemplateCenter: {
        templates: [
          {
            id: 'tpl-1',
            kategori: 'Perencanaan',
            judul: 'Template Program Kerja Kepala Sekolah',
            deskripsi: 'Format lengkap program kerja tahunan kepala sekolah mencakup bidang manajerial, supervisi, dan kewirausahaan.',
            contoh: 'PROGRAM KERJA KEPALA SEKOLAH\nTahun Pelajaran 2026/2027\n\nI. PENDAHULUAN\nA. Latar Belakang\nB. Dasar Hukum\nC. Tujuan dan Sasaran\n\nII. MATRIKS PROGRAM KERJA\n1. Bidang Manajerial: Penyusunan RKT & RKAS Berbasis Rapor Pendidikan (Target: Juli 2026)\n2. Bidang Supervisi Akademik: Supervisi Klinis 100% Guru (Target: Oktober 2026)\n3. Bidang Pengembangan Kewirausahaan: Program Literasi Digital & Kantin Sehat\n\nIII. PENUTUP'
          },
          {
            id: 'tpl-2',
            kategori: 'Perencanaan',
            judul: 'Template RKJM & RKT Sekolah',
            deskripsi: 'Struktur Rencana Kerja Jangka Menengah (4 Tahun) dan Rencana Kerja Tahunan berbasis Rapor Pendidikan.',
            contoh: 'RENCANA KERJA TAHUNAN (RKT)\n1. Identifikasi Masalah (dari Rapor Pendidikan): Skor Numerasi masih kategori sedang.\n2. Akar Masalah: Pembelajaran belum banyak menggunakan soal pemecahan masalah kontekstual.\n3. Benahi (Program Kegiatan): In House Training (IHT) Numerasi & Optimalisasi Kombel.\n4. Indikator Keberhasilan: Minimal 85% guru menerapkan pembelajaran berbasis numerasi kontekstual.'
          },
          {
            id: 'tpl-3',
            kategori: 'Supervisi',
            judul: 'Template Program & Instrumen Supervisi',
            deskripsi: 'Format jadwal supervisi akademik, lembar observasi kelas, dan catatan umpan balik coaching.',
            contoh: 'PROGRAM SUPERVISI AKADEMIK\nA. Tujuan: Meningkatkan kualitas perencanaan, pelaksanaan, dan asesmen pembelajaran.\nB. Jadwal Pelaksanaan:\n   - Minggu 1-2: Telaah Perangkat Ajar (Modul Ajar/RPP)\n   - Minggu 3-4: Observasi Pembelajaran di Kelas\n   - Minggu 5: Refleksi Coaching & Rencana Tindak Lanjut di Kombel'
          },
          {
            id: 'tpl-4',
            kategori: 'Persuratan',
            judul: 'Template Surat Keputusan (SK) & Surat Tugas',
            deskripsi: 'Format baku Surat Keputusan Kepala Sekolah dengan konsiderans Menimbang, Mengingat, dan Memutuskan.',
            contoh: 'KEPUTUSAN KEPALA SEKOLAH\nNomor: [Isi Nomor SK]\nTENTANG PENETAPAN TIM PENGEMBANG KURIKULUM\n\nMenimbang : a. bahwa dalam rangka kelancaran pelaksanaan kurikulum...\nMengingat : 1. Undang-Undang Nomor 20 Tahun 2003 tentang Sistem Pendidikan Nasional...\nMEMUTUSKAN:\nMenetapkan :\nKESATU : Membentuk Tim Pengembang Kurikulum dengan susunan terlampir.\nKEDUA : Keputusan ini berlaku sejak tanggal ditetapkan.'
          },
          {
            id: 'tpl-5',
            kategori: 'Rapat & Kegiatan',
            judul: 'Template Berita Acara & Notulen Rapat',
            deskripsi: 'Format resmi berita acara serah terima/kegiatan dan notulen rapat dewan guru beserta daftar keputusan.',
            contoh: 'BERITA ACARA & NOTULEN RAPAT\nHari/Tanggal : [Isi Hari & Tanggal]\nTempat : Ruang Rapat Sekolah\nPimpinan Rapat : Kepala Sekolah\nAgenda : Evaluasi Pembelajaran dan Persiapan Asesmen\n\nHasil Keputusan Rapat:\n1. Pelaksanaan asesmen dilaksanakan sesuai kalender akademik.\n2. Wali kelas melakukan pemantauan kehadiran dan pendampingan siswa secara berkala.'
          },
          {
            id: 'tpl-6',
            kategori: 'Pelaporan',
            judul: 'Template Laporan Kegiatan & Monev',
            deskripsi: 'Sistematika laporan pelaksanaan kegiatan sekolah dan laporan monitoring evaluasi bulanan/semester.',
            contoh: 'LAPORAN PELAKSANAAN KEGIATAN SEKOLAH\nI. Nama Kegiatan: [Isi Nama Kegiatan]\nII. Waktu & Tempat: [Isi Waktu dan Lokasi]\nIII. Peserta & Sasaran: [Isi Jumlah Peserta]\nIV. Hasil Pelaksanaan & Capaian Target: [Uraian Hasil]\nV. Kendala & Rencana Tindak Lanjut: [Uraian Evaluasi]'
          }
        ],

        render(container) {
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pusat Template Administrasi',
                'Kumpulan format baku administrasi kepala sekolah dilengkapi contoh pengisian dan tombol gunakan langsung.',
                'fa-solid fa-file-lines'
              )}

              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
                ${this.templates.map(t => `
                  <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <div>
                      <span class="px-2.5 py-0.5 rounded-full bg-gold-100 text-gold-800 text-[11px] font-bold">${t.kategori}</span>
                      <h3 class="text-base font-extrabold text-navy-900 mt-2">${t.judul}</h3>
                      <p class="text-xs text-slate-500 mt-1">${t.deskripsi}</p>
                      <div class="mt-3 p-3 rounded-xl bg-slate-50 border border-slate-200 text-[11px] font-mono text-slate-700 whitespace-pre-line max-h-36 overflow-y-auto">
                        ${ExportUtils.escape(t.contoh)}
                      </div>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-100 flex gap-2">
                      <button onclick="Modules.TemplateCenter.useTemplate('${t.id}')" class="flex-1 py-2.5 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow-sm">
                        <i class="fa-solid fa-wand-magic-sparkles text-gold-400 mr-1"></i> Gunakan Template
                      </button>
                      <button onclick="ExportUtils.copyText(Modules.TemplateCenter.getContoh('${t.id}'))" class="px-3 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold" title="Salin Teks">
                        <i class="fa-solid fa-copy"></i>
                      </button>
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>
          `;
        },

        getContoh(id) {
          const t = this.templates.find(x => x.id === id);
          return t ? t.contoh : '';
        },

        useTemplate(id) {
          const t = this.templates.find(x => x.id === id);
          if (!t) return;
          Modules.GeneratorAI.generatedHtml = `<pre style="font-family:'Times New Roman',serif;white-space:pre-wrap;font-size:12pt;">${ExportUtils.escape(t.contoh)}</pre>`;
          App.navigate('generator-ai');
          UI.toast(`Template "${t.judul}" dimuat ke editor dokumen!`, 'success');
        }
      },

      /* -------------------------------------------------------------------
         MODUL 15: PUSAT LAPORAN SEKOLAH (OTOMATIS DARI DATA DATABASE)
      ------------------------------------------------------------------- */
      PusatLaporan: {
        reportTypes: [
          'Laporan Bulanan Kepala Sekolah',
          'Laporan Semester Sekolah',
          'Laporan Tahunan Kepala Sekolah',
          'Laporan Supervisi Akademik & Manajerial',
          'Laporan Evaluasi & Capaian Program',
          'Laporan Perkembangan & Profil Sekolah'
        ],
        selectedReport: 'Laporan Bulanan Kepala Sekolah',

        render(container) {
          const previewHtml = this.buildReportHtml(this.selectedReport);

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pusat Laporan Sekolah Terpadu',
                'Penyusunan laporan resmi otomatis berdasarkan data nyata yang telah dimasukkan ke dalam aplikasi.',
                'fa-solid fa-file-invoice',
                `
                  <button onclick="Modules.PusatLaporan.downloadWord()" class="px-3.5 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-file-word text-gold-400 mr-1"></i> Unduh Word
                  </button>
                  <button onclick="Modules.PusatLaporan.printReport()" class="px-3.5 py-2 bg-gold-500 text-navy-950 rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-print mr-1"></i> Cetak / PDF
                  </button>
                `
              )}

              <div class="flex flex-wrap gap-2 bg-white p-3 rounded-2xl border border-slate-200 shadow-sm">
                ${this.reportTypes.map(rt => `
                  <button onclick="Modules.PusatLaporan.selectReport('${rt}')" class="px-3.5 py-2 rounded-xl text-xs font-bold transition ${
                    this.selectedReport === rt ? 'bg-navy-900 text-gold-400' : 'bg-slate-50 text-slate-700 hover:bg-slate-100'
                  }">
                    ${rt}
                  </button>
                `).join('')}
              </div>

              <div class="bg-white rounded-2xl p-6 sm:p-10 border border-slate-300 shadow-sm">
                ${ExportUtils.getKopSuratHTML()}
                <div class="font-serif text-sm text-black leading-relaxed">
                  ${previewHtml}
                </div>
                ${ExportUtils.getSignatureHTML(new Date().toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' }))}
              </div>
            </div>
          `;
        },

        selectReport(rt) {
          this.selectedReport = rt;
          this.render(document.getElementById('app-content'));
        },

        buildReportHtml(title) {
          const s = DB.data.settings;
          const totalGtk = DB.data.gtk.length;
          const totalSiswa = DB.data.siswa.rombel.reduce((a, b) => a + Number(b.lakiLaki || 0) + Number(b.perempuan || 0), 0);
          const rp = DB.data.perencanaan.raporPendidikan;

          return `
            <h2 style="text-align:center;text-transform:uppercase;margin-bottom:4px;">${ExportUtils.escape(title)}</h2>
            <p style="text-align:center;margin-top:0;margin-bottom:18px;">${ExportUtils.escape(s.schoolName)} • Tahun Pelajaran ${ExportUtils.escape(s.academicYear)}</p>

            <h4>I. IDENTITAS & PROFIL SINGKAT SATUAN PENDIDIKAN</h4>
            <p>Nama Sekolah: <strong>${ExportUtils.escape(s.schoolName)}</strong> (NPSN: ${ExportUtils.escape(s.npsn)})<br/>
            Kepala Sekolah: <strong>${ExportUtils.escape(s.principalName)}</strong> (NIP. ${ExportUtils.escape(s.principalNip)})<br/>
            Jumlah Guru & Tendik: <strong>${totalGtk} Orang</strong> | Jumlah Peserta Didik: <strong>${totalSiswa} Siswa</strong></p>

            <h4>II. CAPAIAN PROGRAM KERJA & MONITORING EVALUASI</h4>
            <table style="width:100%;border-collapse:collapse;font-size:10.5pt;">
              <tr>
                <th style="border:1px solid #000;padding:6px;">No</th>
                <th style="border:1px solid #000;padding:6px;">Program Kegiatan</th>
                <th style="border:1px solid #000;padding:6px;">Status</th>
                <th style="border:1px solid #000;padding:6px;text-align:center;">Progres</th>
              </tr>
              ${DB.data.adminUmum.programKerja.map((p, i) => `
                <tr>
                  <td style="border:1px solid #000;padding:6px;">${i + 1}</td>
                  <td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(p.kegiatan)}</td>
                  <td style="border:1px solid #000;padding:6px;">${ExportUtils.escape(p.status)}</td>
                  <td style="border:1px solid #000;padding:6px;text-align:center;">${p.progres}%</td>
                </tr>
              `).join('')}
            </table>

            <h4>III. RINGKASAN SUPERVISI AKADEMIK & MUTU RAPOR PENDIDIKAN</h4>
            <p>Capaian Rapor Pendidikan (${ExportUtils.escape(rp.tahun)}): Literasi (${rp.literasi}), Numerasi (${rp.numerasi}), Karakter (${rp.karakter}), Iklim Keamanan (${rp.iklimKeamanan}), Kualitas Pembelajaran (${rp.kualitasPembelajaran}).<br/>
            Jumlah Guru Tersupervisi Tercatat: <strong>${DB.data.supervisi.records.length} Guru</strong>.</p>

            <h4>IV. KESIMPULAN & TINDAK LANJUT</h4>
            <p>Seluruh program administrasi, pembelajaran, dan pengelolaan sumber daya sekolah berjalan sesuai rencana kerja tahunan. Tindak lanjut difokuskan pada penguatan numerasi, komunitas belajar guru, dan pemeliharaan sarana pembelajaran.</p>
          `;
        },

        printReport() {
          const tgl = new Date().toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' });
          ExportUtils.printDocument(this.selectedReport, this.buildReportHtml(this.selectedReport), true, true, tgl);
        },

        downloadWord() {
          const tgl = new Date().toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' });
          ExportUtils.downloadWord(this.selectedReport.replace(/\s+/g, '_'), this.buildReportHtml(this.selectedReport), true, true, tgl);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 16: PENGELOLAAN DOKUMEN (ARSIP DIGITAL)
      ------------------------------------------------------------------- */
      ArsipDokumen: {
        selectedCategory: 'Semua',
        searchQuery: '',

        categories: ['Semua', 'Administrasi Umum', 'Perencanaan', 'Supervisi', 'Guru dan Tenaga Kependidikan', 'Peserta Didik', 'Sarana Prasarana', 'Kurikulum', 'Laporan'],

        render(container) {
          const q = this.searchQuery.toLowerCase();
          const filtered = DB.data.arsipDokumen.filter(d => {
            const matchCat = this.selectedCategory === 'Semua' || d.kategori === this.selectedCategory;
            const matchText = d.judul.toLowerCase().includes(q) || (d.nomorSurat || '').toLowerCase().includes(q) || (d.konten || '').toLowerCase().includes(q);
            return matchCat && matchText;
          });

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pengelolaan Dokumen Digital',
                'Penyimpanan arsip digital sekolah berdasarkan kategori, pencarian cepat, unggah dokumen, dan unduh.',
                'fa-solid fa-box-archive'
              )}

              <!-- Form Unggah / Tambah Dokumen Digital -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Unggah / Simpan Dokumen Baru ke Arsip Digital</h3>
                <form onsubmit="Modules.ArsipDokumen.addDoc(event)" class="grid grid-cols-1 md:grid-cols-3 gap-3 bg-slate-50 p-4 rounded-xl border border-slate-200">
                  <input type="text" id="arc-judul" required placeholder="Judul Dokumen" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                  <select id="arc-kategori" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white">
                    ${this.categories.filter(c => c !== 'Semua').map(c => `<option value="${c}">${c}</option>`).join('')}
                  </select>
                  <input type="text" id="arc-nomor" placeholder="Nomor Dokumen / Surat (Opsional)" class="border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white" />
                  <textarea id="arc-konten" rows="2" required placeholder="Ringkasan isi dokumen atau tempel teks dokumen..." class="md:col-span-2 border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white"></textarea>
                  <button type="submit" class="bg-navy-900 hover:bg-navy-800 text-white rounded-xl px-4 py-2.5 text-xs font-bold">
                    <i class="fa-solid fa-cloud-arrow-up text-gold-400 mr-1"></i> Simpan ke Arsip
                  </button>
                </form>
              </div>

              <!-- Filter & Pencarian -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                  <div class="flex flex-wrap gap-1.5">
                    ${this.categories.map(c => `
                      <button onclick="Modules.ArsipDokumen.setCategory('${c}')" class="px-3 py-1.5 rounded-xl text-xs font-semibold transition ${
                        this.selectedCategory === c ? 'bg-navy-900 text-gold-400' : 'bg-slate-100 text-slate-600 hover:bg-slate-200'
                      }">${c}</button>
                    `).join('')}
                  </div>
                  <input type="text" value="${ExportUtils.escape(this.searchQuery)}" oninput="Modules.ArsipDokumen.onSearch(this.value)" placeholder="Cari judul atau nomor arsip..." class="w-full sm:w-64 border border-slate-300 rounded-xl px-3.5 py-2 text-xs" />
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                  ${filtered.length === 0
                    ? `<p class="col-span-2 text-center text-slate-400 py-8 text-xs">Tidak ada dokumen pada kategori ini.</p>`
                    : filtered.map(d => `
                      <div class="p-4 rounded-2xl border border-slate-200 bg-slate-50/70 flex flex-col justify-between">
                        <div>
                          <div class="flex items-center justify-between gap-2">
                            <span class="px-2.5 py-0.5 rounded-full bg-navy-900 text-gold-400 text-[10px] font-bold">${ExportUtils.escape(d.kategori)}</span>
                            <span class="text-[11px] text-slate-500">${ExportUtils.escape(d.tanggal)}</span>
                          </div>
                          <h4 class="font-extrabold text-navy-900 text-sm mt-2">${ExportUtils.escape(d.judul)}</h4>
                          <div class="text-[11px] text-slate-500 mt-0.5">No: ${ExportUtils.escape(d.nomorSurat || '-')}</div>
                          <p class="text-xs text-slate-600 mt-2 line-clamp-3">${ExportUtils.escape(d.konten)}</p>
                        </div>
                        <div class="mt-4 pt-3 border-t border-slate-200 flex justify-end gap-2">
                          <button onclick="Modules.ArsipDokumen.downloadSingleWord('${d.id}')" class="px-3 py-1.5 bg-navy-900 text-white rounded-lg text-xs font-bold">
                            <i class="fa-solid fa-download text-gold-400 mr-1"></i> Unduh Word
                          </button>
                          <button onclick="Modules.ArsipDokumen.deleteDoc('${d.id}')" class="px-3 py-1.5 bg-rose-50 text-rose-700 hover:bg-rose-100 rounded-lg text-xs font-bold">
                            Hapus
                          </button>
                        </div>
                      </div>
                    `).join('')}
                </div>
              </div>
            </div>
          `;
        },

        setCategory(c) {
          this.selectedCategory = c;
          this.render(document.getElementById('app-content'));
        },

        onSearch(v) {
          this.searchQuery = v;
          this.render(document.getElementById('app-content'));
        },

        addDoc(e) {
          e.preventDefault();
          if (!UI.canEdit()) return;
          DB.data.arsipDokumen.unshift({
            id: DB.uid('arc'),
            judul: document.getElementById('arc-judul').value.trim(),
            kategori: document.getElementById('arc-kategori').value,
            nomorSurat: document.getElementById('arc-nomor').value.trim() || '-',
            tanggal: new Date().toISOString().split('T')[0],
            konten: document.getElementById('arc-konten').value.trim()
          });
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        downloadSingleWord(id) {
          const d = DB.data.arsipDokumen.find(x => x.id === id);
          if (!d) return;
          const html = `
            <h3 style="text-align:center;">${ExportUtils.escape(d.judul)}</h3>
            <p style="text-align:center;">Nomor: ${ExportUtils.escape(d.nomorSurat)} • Tanggal: ${ExportUtils.escape(d.tanggal)}</p>
            <p>${ExportUtils.escape(d.konten).replace(/\n/g, '<br/>')}</p>
          `;
          ExportUtils.downloadWord(d.judul.replace(/\s+/g, '_'), html, true, true, d.tanggal);
        },

        deleteDoc(id) {
          if (!UI.canEdit()) return;
          UI.confirmAction('Hapus Arsip Dokumen', 'Dokumen ini akan dihapus permanen dari arsip. Lanjutkan?', () => {
            DB.data.arsipDokumen = DB.data.arsipDokumen.filter(x => x.id !== id);
            DB.save(true);
            this.render(document.getElementById('app-content'));
          });
        }
      },

      /* -------------------------------------------------------------------
         MODUL 17: PENGATURAN APLIKASI, KOP SURAT, HAK AKSES & BACKUP DATABASE
      ------------------------------------------------------------------- */
      Pengaturan: {
        render(container) {
          const s = DB.data.settings;
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pengaturan Aplikasi & Profil Sekolah',
                'Pengaturan identitas sekolah, kop surat, logo, tanda tangan, hak akses pengguna, dan pencadangan data.',
                'fa-solid fa-sliders'
              )}

              <form onsubmit="Modules.Pengaturan.saveSettings(event)" class="space-y-6">
                <!-- 1. Profil Sekolah & Kop Surat -->
                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                  <h3 class="text-base font-extrabold text-navy-900 border-b border-slate-100 pb-2">1. Identitas Sekolah & Kop Surat Resmi</h3>
                  <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Kop Baris 1 (Pemerintah / Yayasan)</label>
                      <input type="text" id="set-kop1" value="${ExportUtils.escape(s.kopLine1)}" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Kop Baris 2 (Dinas Pendidikan)</label>
                      <input type="text" id="set-kop2" value="${ExportUtils.escape(s.kopLine2)}" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Nama Satuan Pendidikan</label>
                      <input type="text" id="set-school" value="${ExportUtils.escape(s.schoolName)}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs font-bold" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">NPSN</label>
                      <input type="text" id="set-npsn" value="${ExportUtils.escape(s.npsn)}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Jenjang Pendidikan</label>
                      <select id="set-jenjang" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white">
                        ${['TK/PAUD', 'SD', 'SMP', 'SMA', 'SMK', 'SLB'].map(j => `<option value="${j}" ${s.jenjang === j ? 'selected' : ''}>${j}</option>`).join('')}
                      </select>
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Tahun Pelajaran & Semester</label>
                      <div class="grid grid-cols-2 gap-2">
                        <input type="text" id="set-ta" value="${ExportUtils.escape(s.academicYear)}" placeholder="2026/2027" class="border border-slate-300 rounded-xl px-3 py-2.5 text-xs" />
                        <input type="text" id="set-sem" value="${ExportUtils.escape(s.semester)}" placeholder="Ganjil" class="border border-slate-300 rounded-xl px-3 py-2.5 text-xs" />
                      </div>
                    </div>
                    <div class="md:col-span-2">
                      <label class="block text-xs font-bold text-slate-700 mb-1">Alamat Lengkap Sekolah</label>
                      <input type="text" id="set-address" value="${ExportUtils.escape(s.address)}" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Kota / Kabupaten (Untuk Titimangsa Surat)</label>
                      <input type="text" id="set-city" value="${ExportUtils.escape(s.city)}" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Email & Website Sekolah</label>
                      <div class="grid grid-cols-2 gap-2">
                        <input type="text" id="set-email" value="${ExportUtils.escape(s.email)}" class="border border-slate-300 rounded-xl px-3 py-2.5 text-xs" />
                        <input type="text" id="set-web" value="${ExportUtils.escape(s.website)}" class="border border-slate-300 rounded-xl px-3 py-2.5 text-xs" />
                      </div>
                    </div>
                  </div>
                </div>

                <!-- 2. Identitas Kepala Sekolah, Logo & Tanda Tangan -->
                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                  <h3 class="text-base font-extrabold text-navy-900 border-b border-slate-100 pb-2">2. Identitas Kepala Sekolah, Logo & Tanda Tangan</h3>
                  <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Nama Kepala Sekolah & Gelar</label>
                      <input type="text" id="set-prin-name" value="${ExportUtils.escape(s.principalName)}" required class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs font-bold" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">NIP Kepala Sekolah</label>
                      <input type="text" id="set-prin-nip" value="${ExportUtils.escape(s.principalNip)}" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Pangkat / Golongan</label>
                      <input type="text" id="set-prin-rank" value="${ExportUtils.escape(s.principalRank)}" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>

                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Unggah Logo Sekolah (PNG/JPG)</label>
                      <input type="file" accept="image/*" onchange="Modules.Pengaturan.handleImageUpload(event, 'logoDataUrl')" class="w-full text-xs border border-slate-300 rounded-xl p-2" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Unggah Scan Tanda Tangan (PNG/JPG)</label>
                      <input type="file" accept="image/*" onchange="Modules.Pengaturan.handleImageUpload(event, 'signatureDataUrl')" class="w-full text-xs border border-slate-300 rounded-xl p-2" />
                    </div>
                    <div class="flex items-end">
                      <button type="button" onclick="Modules.Pengaturan.clearImages()" class="px-3.5 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-semibold">
                        Hapus Logo & TTD
                      </button>
                    </div>
                  </div>
                </div>

                <!-- 3. Hak Akses Pengguna, PIN Keamanan & Opsi Cloud -->
                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                  <h3 class="text-base font-extrabold text-navy-900 border-b border-slate-100 pb-2">3. Hak Akses, Keamanan & Integrasi Multi-Perangkat</h3>
                  <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Peran Pengguna Aktif</label>
                      <select id="set-role" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white font-semibold">
                        <option value="Kepala Sekolah" ${s.userRole === 'Kepala Sekolah' ? 'selected' : ''}>Kepala Sekolah (Akses Penuh)</option>
                        <option value="Operator" ${s.userRole === 'Operator' ? 'selected' : ''}>Operator Sekolah (Input & Edit)</option>
                        <option value="Pengawas" ${s.userRole === 'Pengawas' ? 'selected' : ''}>Pengawas / Tamu (Hanya Baca)</option>
                      </select>
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">PIN Kunci Aplikasi (4-6 Angka, Opsional)</label>
                      <input type="password" id="set-pin" maxlength="6" value="${ExportUtils.escape(s.pinCode)}" placeholder="Kosongkan jika tanpa PIN" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Kunci API Gemini Kustom (Opsional)</label>
                      <input type="password" id="set-apikey" value="${ExportUtils.escape(s.customApiKey)}" placeholder="Otomatis jika kosong" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                    <div>
                      <label class="block text-xs font-bold text-slate-700 mb-1">Mode Penyimpanan Database</label>
                      <select id="set-cloud" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs bg-white">
                        <option value="local" ${s.cloudProvider === 'local' ? 'selected' : ''}>Penyimpanan Lokal Browser (Offline Ready)</option>
                        <option value="supabase" ${s.cloudProvider === 'supabase' ? 'selected' : ''}>Siap Sinkronisasi Supabase (Multi-Perangkat)</option>
                        <option value="firebase" ${s.cloudProvider === 'firebase' ? 'selected' : ''}>Siap Sinkronisasi Firebase (Multi-Perangkat)</option>
                      </select>
                    </div>
                    <div class="md:col-span-2">
                      <label class="block text-xs font-bold text-slate-700 mb-1">Endpoint URL / Project ID Cloud (Opsional)</label>
                      <input type="text" id="set-cloud-url" value="${ExportUtils.escape(s.cloudUrl)}" placeholder="https://xyzcompany.supabase.co atau Firebase Project ID" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 text-xs" />
                    </div>
                  </div>
                </div>

                <div class="flex flex-wrap gap-3">
                  <button type="submit" class="px-6 py-3 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-extrabold shadow-md flex items-center gap-2">
                    <i class="fa-solid fa-floppy-disk text-gold-400"></i> Simpan Semua Pengaturan
                  </button>
                </div>
              </form>

              <!-- 4. Pencadangan & Pemulihan Database JSON -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900 border-b border-slate-100 pb-2">4. Cadangan (Backup) & Pemulihan Data Sekolah</h3>
                <p class="text-xs text-slate-600">Gunakan fitur ini untuk memindahkan seluruh data administrasi sekolah antar HP Android dan komputer dengan aman.</p>
                <div class="flex flex-wrap gap-3">
                  <button onclick="Modules.Pengaturan.exportBackupJson()" class="px-4 py-2.5 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold shadow-sm">
                    <i class="fa-solid fa-download mr-1"></i> Unduh Cadangan Data (.json)
                  </button>
                  <label class="cursor-pointer px-4 py-2.5 bg-navy-50 hover:bg-navy-100 text-navy-900 border border-navy-300 rounded-xl text-xs font-bold">
                    <i class="fa-solid fa-upload mr-1"></i> Pulihkan Data dari File (.json)
                    <input type="file" accept=".json" onchange="Modules.Pengaturan.importBackupJson(event)" class="hidden" />
                  </label>
                  <button onclick="Modules.Pengaturan.confirmReset()" class="px-4 py-2.5 bg-rose-50 hover:bg-rose-100 text-rose-700 border border-rose-200 rounded-xl text-xs font-bold">
                    <i class="fa-solid fa-rotate-left mr-1"></i> Reset ke Data Awal Default
                  </button>
                </div>
              </div>

              <!-- 5. Informasi Pengembang Aplikasi -->
              <div class="bg-gradient-to-r from-navy-950 via-navy-900 to-navy-800 rounded-2xl p-6 shadow-md border border-gold-500/40 text-white flex flex-col sm:flex-row items-center gap-5">
                <div class="w-20 h-20 rounded-full p-1 bg-gradient-to-br from-gold-400 to-gold-600 shrink-0 shadow-lg">
                  <img src="https://iili.io/oK0I2gj.jpg" onerror="this.onerror=null;this.src='https://placehold.co/160x160/0f172a/fbbf24?text=Eti';" alt="Eti, S.Pd.SD" class="w-full h-full rounded-full object-cover bg-white" />
                </div>
                <div class="text-center sm:text-left">
                  <span class="inline-block text-[10px] font-extrabold uppercase tracking-wider bg-gold-500/20 text-gold-300 border border-gold-400/40 px-2.5 py-0.5 rounded-full mb-1">
                    Pengembang Aplikasi
                  </span>
                  <h3 class="text-lg sm:text-xl font-extrabold text-white">Eti, S.Pd.SD</h3>
                  <p class="text-xs text-slate-300 mt-1">
                    Pengembang <strong>KEPALA SEKOLAH MASTER AI</strong> — Asisten Digital Kepemimpinan dan Manajemen Administrasi Sekolah.
                  </p>
                </div>
              </div>
            </div>
          `;
        },

        saveSettings(e) {
          e.preventDefault();
          const s = DB.data.settings;
          s.kopLine1 = document.getElementById('set-kop1').value.trim();
          s.kopLine2 = document.getElementById('set-kop2').value.trim();
          s.schoolName = document.getElementById('set-school').value.trim();
          s.npsn = document.getElementById('set-npsn').value.trim();
          s.jenjang = document.getElementById('set-jenjang').value;
          s.academicYear = document.getElementById('set-ta').value.trim();
          s.semester = document.getElementById('set-sem').value.trim();
          s.address = document.getElementById('set-address').value.trim();
          s.city = document.getElementById('set-city').value.trim();
          s.email = document.getElementById('set-email').value.trim();
          s.website = document.getElementById('set-web').value.trim();
          s.principalName = document.getElementById('set-prin-name').value.trim();
          s.principalNip = document.getElementById('set-prin-nip').value.trim();
          s.principalRank = document.getElementById('set-prin-rank').value.trim();
          s.userRole = document.getElementById('set-role').value;
          s.pinCode = document.getElementById('set-pin').value.trim();
          s.customApiKey = document.getElementById('set-apikey').value.trim();
          s.cloudProvider = document.getElementById('set-cloud').value;
          s.cloudUrl = document.getElementById('set-cloud-url').value.trim();

          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        handleImageUpload(event, targetField) {
          const file = event.target.files[0];
          if (!file) return;
          const reader = new FileReader();
          reader.onload = (e) => {
            DB.data.settings[targetField] = e.target.result;
            DB.save(true);
          };
          reader.readAsDataURL(file);
        },

        clearImages() {
          DB.data.settings.logoDataUrl = '';
          DB.data.settings.signatureDataUrl = '';
          DB.save(true);
        },

        exportBackupJson() {
          const blob = new Blob([JSON.stringify(DB.data, null, 2)], { type: 'application/json' });
          const url = URL.createObjectURL(blob);
          const a = document.createElement('a');
          a.href = url;
          a.download = `Backup_KepsekMasterAI_${new Date().toISOString().split('T')[0]}.json`;
          document.body.appendChild(a);
          a.click();
          document.body.removeChild(a);
          URL.revokeObjectURL(url);
          UI.toast('File cadangan (.json) berhasil diunduh!', 'success');
        },

        importBackupJson(event) {
          const file = event.target.files[0];
          if (!file) return;
          const reader = new FileReader();
          reader.onload = (e) => {
            try {
              const parsed = JSON.parse(e.target.result);
              if (!parsed.settings || !parsed.adminUmum) {
                throw new Error('Struktur file cadangan tidak dikenali.');
              }
              DB.data = DB.mergeDeep(cloneObj(DEFAULT_DATA), parsed);
              DB.save(true);
              this.render(document.getElementById('app-content'));
            } catch (err) {
              UI.toast('Gagal memulihkan data: ' + err.message, 'error');
            }
          };
          reader.readAsText(file);
        },

        confirmReset() {
          UI.confirmAction(
            'Reset Data Aplikasi',
            'Semua data yang Anda ubah akan dikembalikan ke data awal standar. Pastikan Anda sudah mengunduh file cadangan (.json) jika diperlukan.',
            () => DB.resetDefault()
          );
        }
      }
    };

    /**
     * 7. APPLICATION BOOTSTRAP
     */
    window.addEventListener('DOMContentLoaded', () => {
      App.init();
    });
  </script>
</body>
</html>
