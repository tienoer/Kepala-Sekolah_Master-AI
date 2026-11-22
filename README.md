<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>KEPALA SEKOLAH MASTER AI — Asisten Digital Kepemimpinan & Administrasi Sekolah</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Merriweather:wght@400;700&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">

  <!-- Font Awesome 6 -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" />

  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <!-- SheetJS (XLSX) -->
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
    ::-webkit-scrollbar { width: 6px; height: 6px; }
    ::-webkit-scrollbar-track { background: #f1f5f9; }
    ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 9999px; }
    ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }

    .kop-surat { font-family: 'Times New Roman', Times, serif; color: #000000; }
    .kop-border { border-bottom: 3px double #000000; }

    @media print {
      body * { visibility: hidden; }
      #print-area, #print-area * { visibility: visible; }
      #print-area {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        background: #ffffff !important;
        color: #000000 !important;
        padding: 15mm !important;
        margin: 0 !important;
      }
      .no-print { display: none !important; }
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(6px); }
      to { opacity: 1; transform: translateY(0); }
    }
    .animate-fade-in { animation: fadeIn 0.22s ease-out forwards; }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 font-sans antialiased selection:bg-gold-200 selection:text-navy-900">

  <!-- Area Cetak Tersembunyi -->
  <div id="print-area" class="hidden print:block"></div>

  <!-- Notifikasi Toast -->
  <div id="toast-container" class="fixed bottom-4 right-4 z-50 flex flex-col gap-2 max-w-sm w-full pointer-events-none px-4 sm:px-0"></div>

  <!-- Modal Dialog -->
  <div id="custom-modal" class="fixed inset-0 z-50 hidden items-center justify-center bg-navy-950/60 backdrop-blur-sm p-4">
    <div class="bg-white rounded-2xl shadow-2xl border border-slate-200 max-w-2xl w-full overflow-hidden animate-fade-in">
      <div class="bg-navy-900 px-6 py-4 flex items-center justify-between border-b border-gold-500/40">
        <h3 id="modal-title" class="text-white font-bold text-base sm:text-lg flex items-center gap-2">
          <i class="fa-solid fa-circle-info text-gold-400"></i>
          <span>Informasi</span>
        </h3>
        <button onclick="UI.closeModal()" class="text-slate-300 hover:text-white p-1 rounded-lg">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>
      <div id="modal-body" class="p-6 text-slate-700 text-sm leading-relaxed max-h-[75vh] overflow-y-auto"></div>
      <div id="modal-footer" class="bg-slate-50 px-6 py-3.5 flex flex-wrap justify-end gap-2.5 border-t border-slate-200"></div>
    </div>
  </div>

  <!-- Layar Kunci PIN -->
  <div id="pin-lock-screen" class="fixed inset-0 z-50 hidden items-center justify-center bg-gradient-to-br from-navy-950 via-navy-900 to-navy-800 p-4">
    <div class="bg-white rounded-2xl shadow-2xl max-w-md w-full p-8 text-center border-t-4 border-gold-500">
      <div class="w-16 h-16 bg-navy-900 text-gold-400 rounded-2xl flex items-center justify-center mx-auto mb-4 shadow-lg">
        <i class="fa-solid fa-shield-halved text-3xl"></i>
      </div>
      <h2 class="text-xl font-extrabold text-navy-900">Akses Aplikasi Terkunci</h2>
      <p class="text-sm text-slate-500 mt-1 mb-6">Masukkan PIN Keamanan Kepala Sekolah untuk membuka data administrasi.</p>
      <input type="password" id="pin-unlock-input" maxlength="6" placeholder="••••••" class="w-full text-center tracking-[0.5em] text-2xl font-bold border-2 border-slate-300 focus:border-navy-800 rounded-xl py-3 mb-4 outline-none" />
      <button onclick="App.unlockWithPin()" class="w-full bg-navy-900 hover:bg-navy-800 text-white font-semibold py-3 rounded-xl shadow transition flex items-center justify-center gap-2">
        <i class="fa-solid fa-lock-open text-gold-400"></i> Buka Aplikasi
      </button>
    </div>
  </div>

  <!-- Kerangka Utama Aplikasi -->
  <div class="min-h-screen flex flex-col lg:flex-row">

    <!-- Header Mobile -->
    <header class="lg:hidden bg-navy-900 text-white px-4 py-3 flex items-center justify-between sticky top-0 z-30 shadow-md border-b border-gold-500/30">
      <div class="flex items-center gap-3">
        <button onclick="UI.toggleSidebar()" aria-label="Buka Menu" class="p-2.5 rounded-xl bg-navy-800 hover:bg-navy-700 text-gold-400">
          <i class="fa-solid fa-bars text-lg"></i>
        </button>
        <div>
          <h1 class="font-extrabold text-sm tracking-wide text-white flex items-center gap-1.5">
            <span>KEPALA SEKOLAH</span>
            <span class="bg-gold-500 text-navy-950 text-[10px] px-1.5 py-0.5 rounded font-black">MASTER AI</span>
          </h1>
          <p id="mobile-school-name" class="text-[11px] text-slate-300 truncate max-w-[185px]">Memuat sekolah...</p>
        </div>
      </div>
      <div class="flex items-center gap-2">
        <button onclick="App.navigate('unduh-file')" title="Pusat Unduh File" class="p-2.5 rounded-xl bg-navy-800 hover:bg-navy-700 text-gold-400 border border-gold-500/30 text-xs shadow">
          <i class="fa-solid fa-file-arrow-down"></i>
        </button>
        <button onclick="App.navigate('chatbot-ai')" title="Asisten AI" class="p-2.5 rounded-xl bg-gold-500 text-navy-950 font-bold text-xs shadow">
          <i class="fa-solid fa-robot"></i>
        </button>
      </div>
    </header>

    <!-- Backdrop Sidebar Mobile -->
    <div id="sidebar-backdrop" onclick="UI.toggleSidebar(false)" class="fixed inset-0 bg-navy-950/60 z-30 hidden lg:hidden backdrop-blur-sm"></div>

    <!-- Sidebar Navigasi -->
    <aside id="sidebar" class="fixed lg:sticky top-0 left-0 z-40 h-screen w-72 bg-navy-900 text-white flex flex-col transition-transform duration-300 -translate-x-full lg:translate-x-0 shadow-2xl border-r border-navy-800 shrink-0">
      <!-- Brand -->
      <div class="p-5 border-b border-navy-800 bg-navy-950/50">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <div class="w-11 h-11 rounded-xl bg-gradient-to-br from-gold-400 to-gold-600 text-navy-950 flex items-center justify-center shadow-lg shrink-0">
              <i class="fa-solid fa-graduation-cap text-xl"></i>
            </div>
            <div>
              <div class="font-extrabold text-sm tracking-tight leading-tight text-white">KEPALA SEKOLAH</div>
              <div class="flex items-center gap-1.5 mt-0.5">
                <span class="text-xs font-black tracking-wider text-gold-400">MASTER AI</span>
                <span class="text-[10px] bg-navy-800 text-slate-300 px-1.5 py-0.5 rounded border border-navy-700">v2.2</span>
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

      <!-- Indikator Hak Akses -->
      <div class="px-4 py-2.5 bg-navy-950/40 border-b border-navy-800 flex items-center justify-between text-xs">
        <span class="text-slate-400">Hak Akses:</span>
        <span id="sidebar-role-badge" class="font-semibold text-gold-400 bg-navy-800 px-2.5 py-0.5 rounded-full border border-gold-500/30">
          Kepala Sekolah
        </span>
      </div>

      <!-- Daftar Menu -->
      <nav id="sidebar-nav" class="flex-1 overflow-y-auto px-3 py-4 space-y-1 text-sm"></nav>

      <!-- Profil Pengembang & Status Penyimpanan -->
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
          <button onclick="App.navigate('pengaturan')" title="Pengaturan" class="text-gold-400 hover:text-gold-300 font-semibold">
            <i class="fa-solid fa-gear"></i>
          </button>
        </div>
      </div>
    </aside>

    <!-- Konten Utama -->
    <main class="flex-1 flex flex-col min-w-0">
      <!-- Top Bar Desktop -->
      <div class="hidden lg:flex bg-white border-b border-slate-200 px-8 py-3.5 items-center justify-between sticky top-0 z-20 shadow-sm">
        <div class="flex items-center gap-3">
          <button id="btn-global-back" onclick="App.goBack()" class="hidden items-center gap-1.5 text-xs font-semibold text-navy-800 bg-slate-100 hover:bg-slate-200 px-3 py-1.5 rounded-lg transition">
            <i class="fa-solid fa-arrow-left"></i> Kembali
          </button>
          <div class="flex items-center gap-2 text-xs text-slate-500">
            <span>KEPALA SEKOLAH MASTER AI</span>
            <i class="fa-solid fa-chevron-right text-[10px]"></i>
            <span id="breadcrumb-current" class="font-semibold text-navy-800">Dashboard Utama</span>
          </div>
        </div>

        <div class="flex items-center gap-2.5">
          <button onclick="App.navigate('unduh-file')" class="bg-emerald-700 hover:bg-emerald-800 text-white text-xs font-bold px-3.5 py-2 rounded-xl shadow-sm flex items-center gap-2 transition">
            <i class="fa-solid fa-file-arrow-down"></i>
            <span>Pusat Unduh File</span>
          </button>
          <button onclick="App.navigate('generator-ai')" class="bg-navy-900 hover:bg-navy-800 text-white text-xs font-semibold px-3.5 py-2 rounded-xl shadow-sm flex items-center gap-2 transition border border-navy-700">
            <i class="fa-solid fa-wand-magic-sparkles text-gold-400"></i>
            <span>Generator Dokumen AI</span>
          </button>
          <button onclick="App.navigate('chatbot-ai')" class="bg-gold-500 hover:bg-gold-600 text-navy-950 text-xs font-bold px-3.5 py-2 rounded-xl shadow-sm flex items-center gap-2 transition">
            <i class="fa-solid fa-robot"></i>
            <span>Asisten AI</span>
          </button>
        </div>
      </div>

      <!-- Kontainer Halaman Dinamis -->
      <div id="app-content" class="flex-1 p-4 sm:p-6 lg:p-8 max-w-7xl w-full mx-auto"></div>

      <!-- Footer -->
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
    /* =========================================================================
       1. UTILITAS DASAR & PENYIMPANAN DATA (LOCALSTORAGE + FALLBACK MEMORI)
    ========================================================================= */
    function cloneObj(obj) {
      return JSON.parse(JSON.stringify(obj));
    }

    const DB_KEY = 'kepsek_master_ai_db_v3';

    const DEFAULT_DATA = {
      settings: {
        schoolName: 'SD NEGERI 1 HARAPAN BANGSA',
        npsn: '20219876',
        jenjang: 'SD',
        address: 'Jl. Pendidikan No. 45, Kec. Banjar, Kota Banjar, Jawa Barat 46311',
        city: 'Banjar',
        email: 'sdn1harapanbangsa@pendidikan.id',
        website: 'www.sdn1harapanbangsa.sch.id',
        academicYear: '2026/2027',
        semester: 'Ganjil (Semester 1)',
        kopLine1: 'PEMERINTAH KOTA BANJAR',
        kopLine2: 'DINAS PENDIDIKAN DAN KEBUDAYAAN',
        principalName: 'Eti, S.Pd.SD',
        principalNip: '19800514 200501 2 008',
        principalRank: 'Pembina, IV/a',
        logoDataUrl: '',
        signatureDataUrl: '',
        userRole: 'Kepala Sekolah',
        pinCode: '',
        customApiKey: '',
        cloudProvider: 'local',
        cloudUrl: ''
      },
      adminUmum: {
        programKerja: [
          { id: 'pk-1', bidang: 'Manajerial & Perencanaan', kegiatan: 'Penyusunan RKT dan RKAS Berbasis Rapor Pendidikan', target: 'Dokumen RKT & RKAS disahkan tepat waktu', waktu: 'Juli 2026', penanggungJawab: 'Kepala Sekolah & Tim Pengembang', status: 'Selesai', progres: 100 },
          { id: 'pk-2', bidang: 'Supervisi Akademik', kegiatan: 'Supervisi Perencanaan, Pelaksanaan & Asesmen Guru Semester 1', target: '100% Guru tersupervisi klinis', waktu: 'September - November 2026', penanggungJawab: 'Kepala Sekolah', status: 'Berjalan', progres: 80 },
          { id: 'pk-3', bidang: 'Kewirausahaan & Literasi', kegiatan: 'Gerakan Pojok Baca Kelas & Numerasi Menyenangkan', target: 'Semua kelas memiliki pojok baca aktif', waktu: 'Agustus - Desember 2026', penanggungJawab: 'Koordinator Literasi & Wali Kelas', status: 'Berjalan', progres: 70 },
          { id: 'pk-4', bidang: 'Pengembangan Karakter', kegiatan: 'Program Sekolah Sehat, Ramah Anak & Anti Perundungan', target: 'Zero bullying & pembiasaan pagi rutin', waktu: 'Juli - Desember 2026', penanggungJawab: 'Tim TPPK & Guru', status: 'Belum Mulai', progres: 25 }
        ],
        rktRingkas: [
          { id: 'rkt-1', standar: 'Standar Isi & Proses', program: 'In House Training (IHT) Pembelajaran Berdiferensiasi', indikator: 'Guru menyusun modul ajar berdiferensiasi', jadwal: 'Agustus 2026', biaya: 3500000, status: 'Selesai' },
          { id: 'rkt-2', standar: 'Standar Sarpras', program: 'Peremajaan Proyektor & Jaringan Internet Kelas', indikator: 'Semua ruang kelas terkoneksi internet stabil', jadwal: 'November 2026', biaya: 12500000, status: 'Berjalan' }
        ],
        agendaHarian: [
          { id: 'ah-1', tanggal: '2026-11-23', waktu: '07:00 - 08:00', kegiatan: 'Upacara Bendera & Pembinaan Kedisiplinan Siswa', tempat: 'Halaman Sekolah', keterangan: 'Amanat persiapan Asesmen Sumatif Akhir Semester' },
          { id: 'ah-2', tanggal: '2026-11-23', waktu: '09:00 - 11:00', kegiatan: 'Supervisi Kelas IV-A (Mapel Matematika)', tempat: 'Ruang Kelas IV-A', keterangan: 'Observasi pembelajaran Ibu Siti Aminah, S.Pd.' },
          { id: 'ah-3', tanggal: '2026-11-24', waktu: '13:00 - 14:30', kegiatan: 'Rapat Koordinasi Panitia ASAS & Dewan Guru', tempat: 'Ruang Guru', keterangan: 'Finalisasi kisi-kisi, jadwal, dan pengawasan ujian' }
        ],
        agendaKegiatan: [
          { id: 'ak-1', tanggal: '2026-11-25', namaKegiatan: 'Peringatan Hari Guru Nasional (HGN) 2026', penyelenggara: 'Sekolah & Komite', sasaran: 'Seluruh Warga Sekolah', hasil: 'Persiapan tahap akhir' }
        ],
        tamuUmum: [
          { id: 'tu-1', tanggal: '2026-11-18', nama: 'Budi Santoso, S.Kom.', instansi: 'Ketua Komite Sekolah', keperluan: 'Koordinasi dukungan kegiatan parenting dan ekstrakurikuler', tindakLanjut: 'Dijadwalkan rapat pengurus komite' }
        ],
        tamuPembinaan: [
          { id: 'tp-1', tanggal: '2026-11-12', nama: 'Dr. Hj. Ratna Dewi, M.Pd.', jabatan: 'Pengawas Pembina Dinas Pendidikan', materiPembinaan: 'Pendampingan Implementasi Kurikulum & Analisis Rapor Pendidikan', saran: 'Optimalkan tindak lanjut supervisi akademik melalui komunitas belajar sekolah' }
        ],
        notulenRapat: [
          { id: 'nr-1', tanggal: '2026-11-10', topik: 'Rapat Persiapan Asesmen Sumatif Akhir Semester (ASAS)', pimpinan: 'Eti, S.Pd.SD', peserta: '18 Guru & Tenaga Kependidikan', keputusan: '1. Pelaksanaan ASAS dimulai tanggal 2 Desember 2026.\n2. Soal berbasis literasi dan numerasi kontekstual.\n3. Program remedial dilaksanakan sebelum pembagian rapor.' }
        ],
        suratMasuk: [
          { id: 'sm-1', nomorSurat: '421.2/1420/Disdik/2026', tanggalSurat: '2026-11-14', tanggalTerima: '2026-11-15', pengirim: 'Dinas Pendidikan dan Kebudayaan', perihal: 'Undangan Rakor Kepala Sekolah & Evaluasi Serapan BOS', disposisi: 'Bendahara BOS siapkan laporan realisasi anggaran' }
        ],
        suratKeluar: [
          { id: 'skl-1', nomorSurat: '421.2/089/SDN1/XI/2026', tanggal: '2026-11-16', tujuan: 'Orang Tua / Wali Murid Kelas I - VI', perihal: 'Pemberitahuan Jadwal Asesmen Sumatif Akhir Semester (ASAS)', keterangan: 'Disampaikan melalui wali kelas' }
        ],
        ekspedisiSurat: [
          { id: 'eks-1', tanggal: '2026-11-16', nomorSurat: '421.2/089/SDN1/XI/2026', tujuan: 'Korwil Pendidikan Kecamatan', penerima: 'Staf Sekretariat Korwil', paraf: 'Diterima' }
        ],
        inventarisDokumen: [
          { id: 'invd-1', kodeDok: 'DOK-KUR-01', namaDokumen: 'Kurikulum Satuan Pendidikan (KSP) TA 2026/2027', kategori: 'Kurikulum', lokasiSimpan: 'Lemari Arsip Utama', kondisi: 'Lengkap & Disahkan' },
          { id: 'invd-2', kodeDok: 'DOK-KEU-02', namaDokumen: 'RKAS & SPJ Dana BOS Tahap 1 dan 2', kategori: 'Keuangan', lokasiSimpan: 'Brankas Bendahara BOS', kondisi: 'Lengkap' }
        ],
        kegiatanKepsek: [
          { id: 'kk-1', tanggal: '2026-11-19', jenisKegiatan: 'Supervisi Klinis & Coaching Guru', uraian: 'Observasi pembelajaran tematik/matematika serta diskusi refleksi bersama guru kelas', output: 'Instrumen supervisi terisi & rencana tindak lanjut' }
        ],
        dokumentasiKegiatan: [
          { id: 'dokg-1', tanggal: '2026-10-28', judul: 'Peringatan Sumpah Pemuda & Gelar Karya Siswa', deskripsi: 'Kegiatan literasi budaya dan pameran karya kreatif peserta didik di halaman sekolah.', fotoUrl: '' }
        ]
      },
      perencanaan: {
        raporPendidikan: {
          tahun: '2026',
          literasi: 78.4,
          numerasi: 64.2,
          karakter: 81.5,
          iklimKeamanan: 84.0,
          iklimKebinekaan: 82.1,
          kualitasPembelajaran: 68.5,
          catatanTambahan: 'Capaian kemampuan numerasi dan metode pembelajaran interaktif masih berada di kategori sedang sehingga perlu penguatan melalui komunitas belajar dan pelatihan guru.',
          aiRecommendations: [
            {
              id: 'rec-1',
              indikator: 'Kemampuan Numerasi (64.2 - Sedang)',
              akarMasalah: 'Pembelajaran matematika masih dominan prosedural dan belum banyak memanfaatkan benda konkret maupun masalah sehari-hari.',
              rekomendasiKegiatan: 'Workshop Internal Pembelajaran Numerasi Kontekstual & Klinik Numerasi Siswa',
              programPrioritas: 'Penguatan Numerasi Dasar Peserta Didik',
              linkRkjmRkt: 'RKJM Tahun ke-2 / RKT Semester Genap',
              usulanRkas: 'Belanja Narasumber Workshop & Alat Peraga Numerasi (Rp 4.500.000)',
              disetujui: true
            },
            {
              id: 'rec-2',
              indikator: 'Kualitas Pembelajaran (68.5 - Sedang)',
              akarMasalah: 'Penerapan asesmen awal diagnostik dan pembelajaran berdiferensiasi belum merata di seluruh kelas.',
              rekomendasiKegiatan: 'Optimalisasi Komunitas Belajar (Kombel) Sekolah & Berbagi Praktik Baik',
              programPrioritas: 'Transformasi Pembelajaran Berpusat pada Murid',
              linkRkjmRkt: 'RKJM Strategi Mutu / RKT Bulanan',
              usulanRkas: 'Penggandaan Panduan Asesmen & Konsumsi Kombel (Rp 2.800.000)',
              disetujui: false
            }
          ]
        },
        rkjm: [
          { id: 'rkjm-1', sasaranStrategis: 'Meningkatnya capaian literasi dan numerasi siswa di atas standar nasional', programJangkaMenengah: 'Pengembangan Ekosistem Literasi & Numerasi Berkelanjutan', tahunTarget: '2025 - 2028', indikatorKeberhasilan: 'Skor Rapor Pendidikan Literasi & Numerasi > 85%', penanggungJawab: 'Kepala Sekolah & Tim Pengembang' },
          { id: 'rkjm-2', sasaranStrategis: 'Terwujudnya lingkungan belajar aman, sehat, dan berkarakter', programJangkaMenengah: 'Implementasi Sekolah Ramah Anak & Budaya Positif', tahunTarget: '2025 - 2028', indikatorKeberhasilan: 'Indeks Iklim Keamanan > 90%', penanggungJawab: 'Kepala Sekolah & Tim TPPK' }
        ],
        rkt: [
          { id: 'rkt-m1', program: 'Workshop Numerasi Kontekstual & Pembelajaran Berdiferensiasi', tujuan: 'Meningkatkan skor numerasi dan kualitas pembelajaran', sasaran: 'Seluruh Guru Kelas & Mapel', waktu: 'Januari 2027', indikator: '100% guru menerapkan asesmen diagnostik awal', status: 'Direncanakan' },
          { id: 'rkt-m2', program: 'Supervisi Akademik Terpadu Berbasis Coaching', tujuan: 'Meningkatkan mutu proses pembelajaran di kelas', sasaran: '15 Guru', waktu: 'September - November 2026', indikator: 'Minimal 90% guru berpredikat Baik/Amat Baik', status: 'Berjalan' }
        ],
        rkas: [
          { id: 'rkas-1', kodeKegiatan: '03.03.15', uraianKegiatan: 'Workshop Internal Pembelajaran Numerasi Kontekstual', sumberDana: 'BOS Reguler', volume: '1 Kegiatan', anggaran: 4500000, realisasi: 0, status: 'Usulan Draft ARKAS' },
          { id: 'rkas-2', kodeKegiatan: '05.08.04', uraianKegiatan: 'Pemeliharaan Perangkat TIK & Proyektor Kelas', sumberDana: 'BOS Reguler', volume: '4 Unit', anggaran: 6000000, realisasi: 4500000, status: 'Berjalan' },
          { id: 'rkas-3', kodeKegiatan: '04.06.02', uraianKegiatan: 'Pengadaan Buku Pengayaan Literasi & Numerasi', sumberDana: 'BOS Reguler', volume: '100 Eksemplar', anggaran: 8500000, realisasi: 8500000, status: 'Terealisasi' }
        ]
      },
      supervisi: {
        instrumenCustom: [
          { id: 'ins-1', aspek: 'Perencanaan Pembelajaran', indikator: 'Kelengkapan Modul Ajar/RPP, kesesuaian tujuan pembelajaran, dan rancangan diferensiasi' },
          { id: 'ins-2', aspek: 'Pelaksanaan Pembelajaran', indikator: 'Apersepsi bermakna, keaktifan siswa, penggunaan media konkret/digital, dan manajemen kelas' },
          { id: 'ins-3', aspek: 'Asesmen Pembelajaran', indikator: 'Pelaksanaan asesmen formatif, umpan balik membangun, dan refleksi akhir pembelajaran' }
        ],
        records: [
          {
            id: 'sup-1',
            tanggal: '2026-10-14',
            namaGuru: 'Siti Aminah, S.Pd.',
            nip: '19850412 201001 2 018',
            mapel: 'Guru Kelas IV-A',
            kelas: 'IV-A',
            jenis: 'Supervisi Akademik',
            skorPerencanaan: 92,
            skorPelaksanaan: 89,
            skorAsesmen: 86,
            catatanObservasi: 'Guru mengawali pembelajaran dengan pertanyaan pemantik yang menarik, siswa aktif menggunakan alat peraga pecahan.',
            rekomendasi: 'Tambahkan lembar refleksi singkat di akhir pelajaran untuk memetakan pemahaman tiap siswa.',
            tindakLanjut: 'Berbagi praktik baik pada pertemuan Komunitas Belajar sekolah.'
          },
          {
            id: 'sup-2',
            tanggal: '2026-10-21',
            namaGuru: 'Rudi Hartono, S.Pd.',
            nip: '19900817 201903 1 009',
            mapel: 'Guru Kelas V-B',
            kelas: 'V-B',
            jenis: 'Supervisi Akademik',
            skorPerencanaan: 84,
            skorPelaksanaan: 79,
            skorAsesmen: 77,
            catatanObservasi: 'Penguasaan materi baik, namun metode diskusi kelompok perlu pembagian peran yang lebih merata.',
            rekomendasi: 'Gunakan LKPD terstruktur berbasis masalah nyata dan lakukan asesmen formatif di tengah kegiatan.',
            tindakLanjut: 'Coaching individu penyusunan LKPD kontekstual.'
          }
        ]
      },
      gtk: [
        { id: 'gtk-1', nama: 'Siti Aminah, S.Pd.', nip: '19850412 201001 2 018', jabatan: 'Guru Madya', mapel: 'Guru Kelas IV-A', pendidikan: 'S1 PGSD', tugasTambahan: 'Koordinator Kurikulum', jamMengajar: 24, kehadiran: 99, nilaiKinerja: 93, pelatihan: 'Diklat Pembelajaran Berdiferensiasi (2026)', catatanPembinaan: 'Kinerja sangat baik dan inovatif.' },
        { id: 'gtk-2', nama: 'Rudi Hartono, S.Pd.', nip: '19900817 201903 1 009', jabatan: 'Guru Pertama', mapel: 'Guru Kelas V-B', pendidikan: 'S1 PGSD', tugasTambahan: 'Pembina Pramuka', jamMengajar: 24, kehadiran: 97, nilaiKinerja: 83, pelatihan: 'Bimtek Numerasi Dasar (2026)', catatanPembinaan: 'Perlu penguatan variasi asesmen formatif.' },
        { id: 'gtk-3', nama: 'Dewi Lestari, S.Pd.', nip: '19841105 200902 2 011', jabatan: 'Guru Muda', mapel: 'Guru Kelas I-A', pendidikan: 'S1 PGSD', tugasTambahan: 'Koordinator Literasi & Perpustakaan', jamMengajar: 24, kehadiran: 98, nilaiKinerja: 91, pelatihan: 'Pelatihan Transisi PAUD-SD Menyenangkan', catatanPembinaan: 'Sangat telaten mendampingi literasi awal.' },
        { id: 'gtk-4', nama: 'Hendra Gunawan, S.E.', nip: '19880322 201402 1 004', jabatan: 'Tenaga Administrasi & Operator', mapel: '-', pendidikan: 'S1 Manajemen', tugasTambahan: 'Operator Dapodik & Bendahara Barang', jamMengajar: 0, kehadiran: 98, nilaiKinerja: 90, pelatihan: 'Bimtek ARKAS & Pengelolaan Aset Sekolah', catatanPembinaan: 'Tertib administrasi persuratan dan inventaris.' }
      ],
      siswa: {
        rombel: [
          { id: 'rmb-1', tingkat: 'Kelas I', namaRombel: 'I-A & I-B (2 Rombel)', waliKelas: 'Dewi Lestari, S.Pd. & Tim', lakiLaki: 28, perempuan: 30, kehadiran: 97.2 },
          { id: 'rmb-2', tingkat: 'Kelas II', namaRombel: 'II-A & II-B (2 Rombel)', waliKelas: 'Tim Wali Kelas II', lakiLaki: 26, perempuan: 29, kehadiran: 96.8 },
          { id: 'rmb-3', tingkat: 'Kelas III', namaRombel: 'III-A & III-B (2 Rombel)', waliKelas: 'Tim Wali Kelas III', lakiLaki: 29, perempuan: 27, kehadiran: 96.5 },
          { id: 'rmb-4', tingkat: 'Kelas IV', namaRombel: 'IV-A & IV-B (2 Rombel)', waliKelas: 'Siti Aminah, S.Pd. & Tim', lakiLaki: 27, perempuan: 31, kehadiran: 97.0 },
          { id: 'rmb-5', tingkat: 'Kelas V', namaRombel: 'V-A & V-B (2 Rombel)', waliKelas: 'Rudi Hartono, S.Pd. & Tim', lakiLaki: 25, perempuan: 28, kehadiran: 96.4 },
          { id: 'rmb-6', tingkat: 'Kelas VI', namaRombel: 'VI-A & VI-B (2 Rombel)', waliKelas: 'Tim Wali Kelas VI', lakiLaki: 26, perempuan: 27, kehadiran: 97.5 }
        ],
        prestasiDanCatatan: [
          { id: 'prs-1', namaSiswa: 'Nabila Putri Azzahra', kelas: 'V-A', jenis: 'Prestasi Akademik', judul: 'Juara 1 Olimpiade Sains Nasional (OSN) IPA Tingkat Kecamatan', tingkat: 'Kecamatan', pembina: 'Siti Aminah, S.Pd.', tanggal: '2026-08-22' },
          { id: 'prs-2', namaSiswa: 'Rafi Akbar Pratama', kelas: 'VI-B', jenis: 'Prestasi Non-Akademik', judul: 'Juara 2 Festival Tunas Bahasa Ibu (FTBI) Tingkat Kota', tingkat: 'Kota/Kabupaten', pembina: 'Dewi Lestari, S.Pd.', tanggal: '2026-09-15' },
          { id: 'prs-3', namaSiswa: 'Regu Pramuka Siaga', kelas: 'III & IV', jenis: 'Ekstrakurikuler & Karakter', judul: 'Piala Bergilir Pesta Siaga Kwarran', tingkat: 'Kecamatan', pembina: 'Rudi Hartono, S.Pd.', tanggal: '2026-10-10' }
        ],
        rekapMutasi: {
          siswaBaru: 58,
          lulusanTahunLalu: 54,
          pindahMasuk: 2,
          pindahKeluar: 1
        }
      },
      sarpras: [
        { id: 'srp-1', kodeBarang: 'INV-RKL-01', namaBarang: 'Ruang Kelas Belajar (12 Ruang)', kategori: 'Ruang Kelas', jumlah: 12, kondisi: 'Baik', jadwalPemeliharaan: 'Desember 2026', nilaiAset: 1450000000, keterangan: 'Kondisi layak dan nyaman untuk pembelajaran' },
        { id: 'srp-2', kodeBarang: 'INV-TIK-02', namaBarang: 'Perangkat Chromebook & Laptop ANBK', kategori: 'Fasilitas Pembelajaran', jumlah: 25, kondisi: 'Baik', jadwalPemeliharaan: 'November 2026', nilaiAset: 135000000, keterangan: 'Siap digunakan untuk ANBK dan literasi digital' },
        { id: 'srp-3', kodeBarang: 'INV-LCD-03', namaBarang: 'Proyektor LCD Kelas', kategori: 'Elektronik', jumlah: 8, kondisi: 'Rusak Ringan', jadwalPemeliharaan: 'Segera (November 2026)', nilaiAset: 40000000, keterangan: '2 unit memerlukan penggantian lampu proyektor' }
      ],
      kurikulum: [
        { id: 'kur-1', aspek: 'Kalender Pendidikan', namaProgram: 'Penyusunan Kalender Pendidikan & Jadwal Asesmen TA 2026/2027', penanggungJawab: 'Kepala Sekolah & Koordinator Kurikulum', periode: 'Juli 2026 - Juni 2027', status: 'Terlaksana', catatan: 'Selaras dengan kalender Dinas Pendidikan' },
        { id: 'kur-2', aspek: 'Pembagian Tugas Mengajar', namaProgram: 'SK Pembagian Tugas Mengajar & Jadwal Pelajaran Semester 1', penanggungJawab: 'Kepala Sekolah', periode: 'Semester 1 (2026/2027)', status: 'Terlaksana', catatan: 'Seluruh rombel kelas I - VI terlayani penuh' },
        { id: 'kur-3', aspek: 'Peningkatan Mutu Pembelajaran', namaProgram: 'Klinik Literasi-Numerasi & Kokurikuler P5', penanggungJawab: 'Tim Kombel Sekolah', periode: 'Setiap Jumat Pagi', status: 'Berjalan', catatan: 'Meningkatkan minat baca dan nalar kritis siswa' }
      ],
      kepemimpinan: {
        visi: 'Terwujudnya Peserta Didik yang Beriman, Berkarakter Mulia, Cerdas, Literat, Kreatif, dan Peduli Lingkungan dalam Ekosistem Sekolah yang Aman dan Menyenangkan.',
        misi: '1. Menanamkan nilai keimanan, akhlak mulia, dan pembiasaan budaya positif sehari-hari.\n2. Melaksanakan pembelajaran aktif, berdiferensiasi, dan menyenangkan sesuai tahap perkembangan anak.\n3. Mengembangkan kompetensi literasi, numerasi, serta bakat minat peserta didik.\n4. Membangun kemitraan harmonis dengan orang tua, komite sekolah, dan masyarakat.',
        tujuan: 'Meningkatkan capaian Rapor Pendidikan di atas 85%, mewujudkan sekolah ramah anak tanpa perundungan, dan membentuk lulusan berkarakter kuat.',
        programStrategis: [
          { id: 'kp-1', kategori: 'Budaya Positif & Karakter', namaProgram: 'Gerakan 5S (Senyum, Sapa, Salam, Sopan, Santun) & Pembiasaan Literasi Pagi', mitra: 'Seluruh Guru & Orang Tua', dampak: 'Suasana sekolah tertib, ramah, dan penuh kekeluargaan', status: 'Aktif' },
          { id: 'kp-2', kategori: 'Kemitraan & Komite Sekolah', namaProgram: 'Kelas Inspirasi Orang Tua & Rapat Pleno Komite Sekolah', mitra: 'Komite Sekolah & Puskesmas', dampak: 'Dukungan penuh program sekolah sehat dan imunisasi anak', status: 'Aktif' },
          { id: 'kp-3', kategori: 'Inovasi & Sekolah Sehat', namaProgram: 'Digitalisasi Administrasi Kepala Sekolah & Gerakan Sekolah Sehat', mitra: 'Dinas Kesehatan & Tim UKS', dampak: 'Tata kelola tertib dan kebiasaan hidup bersih siswa meningkat', status: 'Aktif' }
        ]
      },
      monev: [
        { id: 'mnv-1', periode: 'Evaluasi Bulanan (Oktober 2026)', namaProgram: 'Supervisi Akademik Guru Semester 1', targetPersen: 100, capaianPersen: 80, kendala: 'Beberapa jadwal supervisi menyesuaikan agenda pelatihan guru', tindakLanjut: 'Penjadwalan ulang supervisi lanjutan pada minggu ke-4 November 2026' },
        { id: 'mnv-2', periode: 'Evaluasi Semester 1', namaProgram: 'Gerakan Literasi & Pojok Baca Kelas', targetPersen: 100, capaianPersen: 85, kendala: 'Variasi buku bacaan bergambar di kelas tinggi masih perlu ditambah', tindakLanjut: 'Pertukaran koleksi buku antar kelas setiap 2 minggu' },
        { id: 'mnv-3', periode: 'Evaluasi Tahunan', namaProgram: 'Pemeliharaan Sarana TIK & Proyektor', targetPersen: 100, capaianPersen: 65, kendala: 'Menunggu realisasi belanja pemeliharaan sarana tahap akhir', tindakLanjut: 'Prioritaskan perbaikan proyektor ruang kelas V dan VI' }
      ],
      arsipDokumen: [
        { id: 'arc-1', judul: 'SK Pembagian Tugas Mengajar Semester 1 TA 2026/2027', kategori: 'Kurikulum', nomorSurat: '421.2/012/SDN1/VII/2026', tanggal: '2026-07-15', konten: 'KEPUTUSAN KEPALA SD NEGERI 1 HARAPAN BANGSA\nNomor: 421.2/012/SDN1/VII/2026\nTentang Pembagian Tugas Guru dalam Kegiatan Belajar Mengajar Semester 1 Tahun Pelajaran 2026/2027.' },
        { id: 'arc-2', judul: 'Laporan Pelaksanaan Supervisi Akademik Bulan Oktober 2026', kategori: 'Supervisi', nomorSurat: '421.2/074/SDN1/X/2026', tanggal: '2026-10-30', konten: 'LAPORAN HASIL SUPERVISI AKADEMIK\nTelah dilaksanakan supervisi pembelajaran dengan rata-rata capaian skor 86,5 (Kategori Baik).' }
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
          if (notify) UI.toast('Data berhasil disimpan!', 'success');
        } catch (e) {
          this.storageAvailable = false;
          if (notify) UI.toast('Data tersimpan di sesi aktif (unduh cadangan JSON untuk menyimpan permanen).', 'warning');
        }
      },
      resetDefault() {
        this.data = cloneObj(DEFAULT_DATA);
        this.save(false);
        UI.toast('Data berhasil dikembalikan ke pengaturan awal.', 'info');
        App.renderCurrent();
      },
      uid(prefix = 'id') {
        return `${prefix}-${Date.now()}-${Math.random().toString(36).substring(2, 6)}`;
      }
    };

    /* =========================================================================
       2. MESIN KECERDASAN BUATAN (GEMINI AI + FALLBACK OFFLINE CERDAS)
    ========================================================================= */
    const AI = {
      async generate(prompt, systemInstruction = '', expectJson = false) {
        const userKey = DB.data?.settings?.customApiKey?.trim() || '';
        const apiKey = userKey || '';
        if (!apiKey) return null; // Langsung gunakan fallback lokal cerdas jika API key belum diisi

        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;
        const payload = {
          contents: [{ parts: [{ text: prompt }] }],
          systemInstruction: {
            parts: [{
              text: systemInstruction ||
                'Anda adalah asisten ahli manajemen pendidikan dan kepemimpinan kepala sekolah di Indonesia. Gunakan Bahasa Indonesia yang formal, sistematis, praktis, dan sesuai regulasi pendidikan Indonesia. Jangan mengarang nomor surat atau data faktual yang tidak diberikan pengguna.'
            }]
          }
        };
        if (expectJson) {
          payload.generationConfig = { responseMimeType: 'application/json' };
        }

        try {
          const response = await fetch(apiUrl, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(payload)
          });
          if (!response.ok) return null;
          const result = await response.json();
          return result?.candidates?.[0]?.content?.parts?.[0]?.text || null;
        } catch (err) {
          return null;
        }
      }
    };

    /* =========================================================================
       3. UTILITAS EKSPOR, CETAK, WORD (.DOC) & EXCEL (.XLSX)
    ========================================================================= */
    const ExportUtils = {
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
      },

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
        setTimeout(() => window.print(), 150);
      },

      downloadWord(filename, bodyHtml, includeKop = true, includeSignature = true, dateStr = '') {
        const fullHtml = `
          <html xmlns:o='urn:schemas-microsoft-com:office:office'
                xmlns:w='urn:schemas-microsoft-com:office:word'
                xmlns='http://www.w3.org/TR/REC-html40'>
          <head><meta charset='utf-8'><title>${this.escape(filename)}</title>
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
            UI.toast('Pustaka Excel sedang dimuat, silakan coba sesaat lagi.', 'warning');
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
      }
    };

    /* =========================================================================
       4. KOMPONEN ANTARMUKA PENGGUNA (UI HELPERS)
    ========================================================================= */
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
        }, 3200);
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
          this.toast('Mode Pengawas/Tamu (Hanya Baca). Ubah hak akses di menu Pengaturan untuk mengedit.', 'warning');
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

    /* =========================================================================
       5. NAVIGASI & PENGENDALI APLIKASI (APP ROUTER)
    ========================================================================= */
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
      { group: 'EVALUASI & ARSIP', id: 'unduh-file', label: 'Pusat Unduh File', icon: 'fa-solid fa-file-arrow-down', badge: 'FILE' },
      { group: 'SISTEM', id: 'pengaturan', label: 'Pengaturan & Database', icon: 'fa-solid fa-sliders' }
    ];

    const App = {
      currentRoute: 'dashboard',
      historyStack: [],

      init() {
        DB.init();
        this.checkPinLock();
        this.renderSidebar();
        this.updateGlobalHeaders();
        this.navigate('dashboard', false);
      },

      checkPinLock() {
        const pin = DB.data?.settings?.pinCode;
        if (pin && pin.length >= 4) {
          const lockEl = document.getElementById('pin-lock-screen');
          lockEl.classList.remove('hidden');
          lockEl.classList.add('flex');
        }
      },

      unlockWithPin() {
        const input = document.getElementById('pin-unlock-input').value.trim();
        if (input === DB.data.settings.pinCode) {
          const lockEl = document.getElementById('pin-lock-screen');
          lockEl.classList.add('hidden');
          lockEl.classList.remove('flex');
          UI.toast('Akses dibuka. Selamat bekerja!', 'success');
        } else {
          UI.toast('PIN Keamanan tidak sesuai.', 'error');
        }
      },

      updateGlobalHeaders() {
        const s = DB.data.settings;
        const mobName = document.getElementById('mobile-school-name');
        if (mobName) mobName.textContent = s.schoolName;
        const roleBadge = document.getElementById('sidebar-role-badge');
        if (roleBadge) roleBadge.textContent = s.userRole;
        const storageLabel = document.getElementById('storage-status-label');
        if (storageLabel) {
          storageLabel.textContent = DB.storageAvailable ? 'Penyimpanan Lokal Aktif' : 'Mode Memori Sesi';
        }
      },

      renderSidebar() {
        const nav = document.getElementById('sidebar-nav');
        let lastGroup = '';
        nav.innerHTML = MENU_ITEMS.map(item => {
          let groupHeader = '';
          if (item.group !== lastGroup) {
            lastGroup = item.group;
            groupHeader = `<div class="px-3 pt-3 pb-1 text-[10px] font-extrabold tracking-wider text-slate-400 uppercase">${item.group}</div>`;
          }
          const isActive = this.currentRoute === item.id;
          const activeClass = isActive
            ? 'bg-gradient-to-r from-gold-500 to-gold-400 text-navy-950 font-extrabold shadow-md'
            : 'text-slate-300 hover:bg-navy-800 hover:text-white font-medium';
          const iconColor = isActive ? 'text-navy-950' : 'text-gold-400';
          const badgeHtml = item.badge
            ? `<span class="ml-auto text-[10px] px-1.5 py-0.5 rounded font-black ${isActive ? 'bg-navy-950 text-gold-400' : 'bg-gold-500/20 text-gold-300 border border-gold-400/30'}">${item.badge}</span>`
            : '';

          return `
            ${groupHeader}
            <button onclick="App.navigate('${item.id}')" class="w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl transition text-left ${activeClass}">
              <i class="${item.icon} w-4 text-center ${iconColor}"></i>
              <span class="truncate text-xs sm:text-[13px]">${item.label}</span>
              ${badgeHtml}
            </button>
          `;
        }).join('');
      },

      navigate(routeId, pushHistory = true) {
        if (pushHistory && this.currentRoute && this.currentRoute !== routeId) {
          this.historyStack.push(this.currentRoute);
        }
        this.currentRoute = routeId;
        this.renderSidebar();
        UI.toggleSidebar(false);

        const menuObj = MENU_ITEMS.find(m => m.id === routeId);
        const bc = document.getElementById('breadcrumb-current');
        if (bc) bc.textContent = menuObj ? menuObj.label : 'Dashboard Utama';

        const backBtn = document.getElementById('btn-global-back');
        if (backBtn) {
          if (this.historyStack.length > 0 && routeId !== 'dashboard') {
            backBtn.classList.remove('hidden');
            backBtn.classList.add('inline-flex');
          } else {
            backBtn.classList.add('hidden');
            backBtn.classList.remove('inline-flex');
          }
        }

        this.renderCurrent();
        window.scrollTo({ top: 0, behavior: 'smooth' });
      },

      goBack() {
        if (this.historyStack.length > 0) {
          const prev = this.historyStack.pop();
          this.navigate(prev, false);
        } else {
          this.navigate('dashboard', false);
        }
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
            case 'unduh-file': Modules.UnduhFile.render(container); break;
            case 'pengaturan': Modules.Pengaturan.render(container); break;
            default: Modules.Dashboard.render(container);
          }
        } catch (err) {
          console.error(err);
          container.innerHTML = `
            <div class="bg-rose-50 border border-rose-200 rounded-2xl p-6 text-rose-900">
              <h3 class="font-bold text-base mb-1">Terjadi Gangguan Tampilan Modul</h3>
              <p class="text-xs mb-4">${ExportUtils.escape(err.message)}</p>
              <button onclick="App.navigate('dashboard')" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Kembali ke Dashboard</button>
            </div>
          `;
        }
      },

      showDeveloperModal() {
        UI.openModal({
          title: 'Profil Pengembang Aplikasi',
          bodyHtml: `
            <div class="flex flex-col sm:flex-row items-center gap-5 text-center sm:text-left">
              <div class="w-24 h-24 rounded-full p-1 bg-gradient-to-br from-gold-400 to-gold-600 shrink-0 shadow-lg">
                <img src="https://iili.io/oK0I2gj.jpg" onerror="this.onerror=null;this.src='https://placehold.co/200x200/0f172a/fbbf24?text=Eti';" alt="Eti, S.Pd.SD" class="w-full h-full rounded-full object-cover bg-white" />
              </div>
              <div class="space-y-1.5">
                <span class="inline-block px-2.5 py-0.5 rounded-full bg-gold-100 text-gold-800 text-[10px] font-extrabold uppercase tracking-wider">Pengembang Utama</span>
                <h4 class="text-xl font-extrabold text-navy-900">Eti, S.Pd.SD</h4>
                <p class="text-xs text-slate-600 leading-relaxed">
                  Perancang dan Pengembang <strong>KEPALA SEKOLAH MASTER AI</strong> — Asisten Digital Kepemimpinan, Supervisi, Perencanaan Berbasis Data, dan Manajemen Administrasi Sekolah.
                </p>
                <p class="text-xs italic text-gold-700 font-semibold pt-1">
                  "Administrasi Lebih Mudah, Kepemimpinan Lebih Terarah, Sekolah Semakin Berkualitas."
                </p>
              </div>
            </div>
          `,
          footerHtml: `<button onclick="UI.closeModal()" class="px-5 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Tutup</button>`
        });
      }
    };

    /* =========================================================================
       6. SELURUH MODUL FUNGSIONAL APLIKASI
    ========================================================================= */
    const Modules = {

      /* -------------------------------------------------------------------
         MODUL 2: DASHBOARD UTAMA
      ------------------------------------------------------------------- */
      Dashboard: {
        chartInstance: null,

        render(container) {
          const s = DB.data.settings;
          const totalGtk = DB.data.gtk.length;
          const totalSiswa = DB.data.siswa.rombel.reduce((acc, r) => acc + Number(r.lakiLaki || 0) + Number(r.perempuan || 0), 0);
          const progList = DB.data.adminUmum.programKerja;
          const totalProgram = progList.length;
          const avgProgress = totalProgram
            ? Math.round(progList.reduce((acc, p) => acc + Number(p.progres || 0), 0) / totalProgram)
            : 0;
          const totalDocs = DB.data.adminUmum.inventarisDokumen.length + DB.data.arsipDokumen.length;
          const pendingTasks = progList.filter(p => p.status !== 'Selesai');
          const agendas = DB.data.adminUmum.agendaHarian.slice(0, 4);

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              <!-- Banner Identitas Sekolah -->
              <div class="bg-gradient-to-r from-navy-950 via-navy-900 to-navy-800 rounded-3xl p-6 sm:p-8 text-white shadow-xl border border-gold-500/30 relative overflow-hidden">
                <div class="flex flex-col lg:flex-row lg:items-center lg:justify-between gap-6 relative z-10">
                  <div class="space-y-2">
                    <div class="inline-flex items-center gap-2 bg-gold-500/20 border border-gold-400/40 text-gold-300 text-xs font-bold px-3 py-1 rounded-full">
                      <i class="fa-solid fa-school"></i>
                      <span>NPSN: ${ExportUtils.escape(s.npsn)} • Tahun Pelajaran ${ExportUtils.escape(s.academicYear)} (${ExportUtils.escape(s.semester)})</span>
                    </div>
                    <h1 class="text-2xl sm:text-3xl font-extrabold tracking-tight text-white">${ExportUtils.escape(s.schoolName)}</h1>
                    <p class="text-xs sm:text-sm text-slate-300 flex items-center gap-2">
                      <i class="fa-solid fa-location-dot text-gold-400"></i>
                      <span>${ExportUtils.escape(s.address)}</span>
                    </p>
                    <div class="pt-2 flex flex-wrap items-center gap-4 text-xs sm:text-sm">
                      <div class="bg-navy-950/60 px-3.5 py-2 rounded-xl border border-navy-700">
                        <span class="text-slate-400">Kepala Sekolah:</span>
                        <strong class="text-gold-400 ml-1">${ExportUtils.escape(s.principalName)}</strong>
                      </div>
                      <div class="bg-navy-950/60 px-3.5 py-2 rounded-xl border border-navy-700">
                        <span class="text-slate-400">NIP:</span>
                        <strong class="text-white ml-1">${ExportUtils.escape(s.principalNip)}</strong>
                      </div>
                    </div>
                  </div>
                  <div class="flex flex-wrap gap-2.5 shrink-0">
                    <button onclick="App.navigate('pengaturan')" class="px-4 py-2.5 bg-white/10 hover:bg-white/20 text-white border border-white/20 rounded-xl text-xs font-bold flex items-center gap-2 transition">
                      <i class="fa-solid fa-pen-to-square text-gold-400"></i> Edit Profil Sekolah
                    </button>
                    <button onclick="App.navigate('generator-ai')" class="px-4 py-2.5 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold shadow-lg flex items-center gap-2 transition">
                      <i class="fa-solid fa-wand-magic-sparkles"></i> Buat Surat / SK AI
                    </button>
                  </div>
                </div>
              </div>

              <!-- 4 Kartu Statistik Utama -->
              <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div onclick="App.navigate('gtk')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition">
                  <div class="flex items-center justify-between">
                    <span class="text-xs font-bold text-slate-500 uppercase">Guru & Tendik</span>
                    <div class="w-10 h-10 rounded-xl bg-navy-50 text-navy-900 flex items-center justify-center"><i class="fa-solid fa-chalkboard-user text-lg"></i></div>
                  </div>
                  <div class="text-3xl font-extrabold text-navy-900 mt-3">${totalGtk} <span class="text-xs font-semibold text-slate-400">Orang</span></div>
                  <div class="text-xs text-emerald-700 font-semibold mt-1">Klik untuk kelola database GTK &rarr;</div>
                </div>

                <div onclick="App.navigate('siswa')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition">
                  <div class="flex items-center justify-between">
                    <span class="text-xs font-bold text-slate-500 uppercase">Peserta Didik</span>
                    <div class="w-10 h-10 rounded-xl bg-gold-50 text-gold-700 flex items-center justify-center"><i class="fa-solid fa-users text-lg"></i></div>
                  </div>
                  <div class="text-3xl font-extrabold text-navy-900 mt-3">${totalSiswa} <span class="text-xs font-semibold text-slate-400">Siswa</span></div>
                  <div class="text-xs text-gold-700 font-semibold mt-1">${DB.data.siswa.rombel.length} Rombongan Belajar &rarr;</div>
                </div>

                <div onclick="App.navigate('admin-umum')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition">
                  <div class="flex items-center justify-between">
                    <span class="text-xs font-bold text-slate-500 uppercase">Program Kerja</span>
                    <div class="w-10 h-10 rounded-xl bg-emerald-50 text-emerald-700 flex items-center justify-center"><i class="fa-solid fa-list-check text-lg"></i></div>
                  </div>
                  <div class="text-3xl font-extrabold text-navy-900 mt-3">${totalProgram} <span class="text-xs font-semibold text-slate-400">Program</span></div>
                  <div class="text-xs text-emerald-700 font-semibold mt-1">Rata-rata progres ${avgProgress}% &rarr;</div>
                </div>

                <div onclick="App.navigate('arsip-dokumen')" class="cursor-pointer bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition">
                  <div class="flex items-center justify-between">
                    <span class="text-xs font-bold text-slate-500 uppercase">Dokumen Administrasi</span>
                    <div class="w-10 h-10 rounded-xl bg-indigo-50 text-indigo-700 flex items-center justify-center"><i class="fa-solid fa-folder-tree text-lg"></i></div>
                  </div>
                  <div class="text-3xl font-extrabold text-navy-900 mt-3">${totalDocs} <span class="text-xs font-semibold text-slate-400">Dokumen</span></div>
                  <div class="text-xs text-indigo-700 font-semibold mt-1">Arsip digital & inventaris &rarr;</div>
                </div>
              </div>

              <!-- Grafik Capaian & Progres Program -->
              <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <div class="lg:col-span-2 bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <div class="flex items-center justify-between mb-4">
                    <div>
                      <h3 class="text-base font-extrabold text-navy-900">Grafik Perkembangan & Capaian Rapor Pendidikan</h3>
                      <p class="text-xs text-slate-500">Perbandingan skor indikator utama satuan pendidikan tahun ${ExportUtils.escape(DB.data.perencanaan.raporPendidikan.tahun)}</p>
                    </div>
                    <button onclick="App.navigate('perencanaan')" class="text-xs font-bold text-navy-800 hover:underline">Analisis AI &rarr;</button>
                  </div>
                  <div class="h-64 w-full">
                    <canvas id="dashboard-rapor-chart"></canvas>
                  </div>
                </div>

                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm flex flex-col justify-between">
                  <div>
                    <h3 class="text-base font-extrabold text-navy-900 mb-1">Progres Pelaksanaan Program</h3>
                    <p class="text-xs text-slate-500 mb-4">Capaian keseluruhan program kerja kepala sekolah</p>
                    <div class="flex items-end justify-between mb-1.5">
                      <span class="text-xs font-bold text-navy-900">Keterlaksanaan Total</span>
                      <span class="text-lg font-extrabold text-gold-600">${avgProgress}%</span>
                    </div>
                    <div class="w-full h-3 bg-slate-100 rounded-full overflow-hidden mb-5">
                      <div class="h-full bg-gradient-to-r from-navy-900 to-gold-500 rounded-full" style="width:${avgProgress}%"></div>
                    </div>
                    <div class="space-y-3">
                      ${progList.map(p => `
                        <div>
                          <div class="flex justify-between text-xs font-semibold mb-1">
                            <span class="truncate pr-2 text-slate-700">${ExportUtils.escape(p.kegiatan)}</span>
                            <span class="text-navy-900 font-bold shrink-0">${p.progres}%</span>
                          </div>
                          <div class="w-full h-1.5 bg-slate-100 rounded-full overflow-hidden">
                            <div class="h-full bg-navy-800 rounded-full" style="width:${p.progres}%"></div>
                          </div>
                        </div>
                      `).join('')}
                    </div>
                  </div>
                  <button onclick="App.navigate('monev')" class="mt-5 w-full py-2.5 bg-navy-50 hover:bg-navy-100 text-navy-900 rounded-xl text-xs font-bold transition">
                    Buka Monitoring & Evaluasi
                  </button>
                </div>
              </div>

              <!-- Agenda Terdekat & Notifikasi Tugas -->
              <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <div class="flex items-center justify-between mb-4">
                    <h3 class="text-base font-extrabold text-navy-900 flex items-center gap-2">
                      <i class="fa-solid fa-calendar-days text-gold-500"></i> Agenda Kegiatan Terdekat
                    </h3>
                    <button onclick="App.navigate('admin-umum')" class="text-xs font-bold text-navy-800 hover:underline">Lihat Semua</button>
                  </div>
                  <div class="space-y-3">
                    ${agendas.map(a => `
                      <div class="p-3.5 rounded-xl bg-slate-50 border border-slate-200/80 flex items-start gap-3">
                        <div class="px-2.5 py-1.5 rounded-lg bg-navy-900 text-gold-400 text-[11px] font-bold text-center shrink-0">
                          ${ExportUtils.escape(a.tanggal)}
                        </div>
                        <div class="min-w-0">
                          <div class="text-xs sm:text-sm font-bold text-navy-900">${ExportUtils.escape(a.kegiatan)}</div>
                          <div class="text-[11px] text-slate-500 mt-0.5">${ExportUtils.escape(a.waktu)} • ${ExportUtils.escape(a.tempat)}</div>
                        </div>
                      </div>
                    `).join('')}
                  </div>
                </div>

                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <div class="flex items-center justify-between mb-4">
                    <h3 class="text-base font-extrabold text-navy-900 flex items-center gap-2">
                      <i class="fa-solid fa-bell text-amber-500"></i> Tugas Administrasi & Program Aktif
                    </h3>
                    <span class="px-2.5 py-0.5 rounded-full bg-amber-100 text-amber-800 text-xs font-bold">${pendingTasks.length} Perlu Tindak Lanjut</span>
                  </div>
                  <div class="space-y-3">
                    ${pendingTasks.length === 0
                      ? `<p class="text-xs text-slate-500">Seluruh program kerja telah selesai dilaksanakan.</p>`
                      : pendingTasks.map(t => `
                        <div class="p-3.5 rounded-xl bg-amber-50/60 border border-amber-200 flex items-center justify-between gap-3">
                          <div>
                            <div class="text-xs sm:text-sm font-bold text-navy-900">${ExportUtils.escape(t.kegiatan)}</div>
                            <div class="text-[11px] text-slate-600 mt-0.5">PJ: ${ExportUtils.escape(t.penanggungJawab)} • Target: ${ExportUtils.escape(t.waktu)}</div>
                          </div>
                          <span class="px-2.5 py-1 rounded-lg bg-amber-500 text-white text-[11px] font-bold shrink-0">${ExportUtils.escape(t.status)}</span>
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
          const canvas = document.getElementById('dashboard-rapor-chart');
          if (!canvas || typeof Chart === 'undefined') return;
          if (this.chartInstance) {
            this.chartInstance.destroy();
          }
          const rp = DB.data.perencanaan.raporPendidikan;
          this.chartInstance = new Chart(canvas.getContext('2d'), {
            type: 'bar',
            data: {
              labels: ['Literasi', 'Numerasi', 'Karakter', 'Iklim Keamanan', 'Iklim Kebinekaan', 'Kualitas Pembelajaran'],
              datasets: [{
                label: 'Skor Indikator (%)',
                data: [rp.literasi, rp.numerasi, rp.karakter, rp.iklimKeamanan, rp.iklimKebinekaan, rp.kualitasPembelajaran],
                backgroundColor: ['#0f172a', '#f59e0b', '#1e3a8a', '#059669', '#d97706', '#334e68'],
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

        tabs: [
          {
            key: 'programKerja', label: '1. Program Kerja Kepsek',
            fields: [
              { name: 'bidang', label: 'Bidang', type: 'text', sample: 'Manajerial & Supervisi' },
              { name: 'kegiatan', label: 'Program / Kegiatan', type: 'text', sample: 'Supervisi Klinis Pembelajaran Semester Genap' },
              { name: 'target', label: 'Target Capaian', type: 'text', sample: '100% Guru tersupervisi' },
              { name: 'waktu', label: 'Waktu Pelaksanaan', type: 'text', sample: 'Februari - April 2027' },
              { name: 'penanggungJawab', label: 'Penanggung Jawab', type: 'text', sample: 'Kepala Sekolah' },
              { name: 'status', label: 'Status', type: 'select', options: ['Belum Mulai', 'Berjalan', 'Selesai'], sample: 'Berjalan' },
              { name: 'progres', label: 'Progres (%)', type: 'number', sample: 50 }
            ]
          },
          {
            key: 'rktRingkas', label: '2. Rencana Kerja Tahunan',
            fields: [
              { name: 'standar', label: 'Standar Nasional', type: 'text', sample: 'Standar Proses' },
              { name: 'program', label: 'Program Tahunan', type: 'text', sample: 'Penguatan Komunitas Belajar Guru' },
              { name: 'indikator', label: 'Indikator Keberhasilan', type: 'text', sample: 'Pertemuan kombel rutin 2 kali sebulan' },
              { name: 'jadwal', label: 'Jadwal', type: 'text', sample: 'Tahun Pelajaran 2026/2027' },
              { name: 'biaya', label: 'Anggaran (Rp)', type: 'number', sample: 3000000 },
              { name: 'status', label: 'Status', type: 'select', options: ['Direncanakan', 'Berjalan', 'Selesai'], sample: 'Berjalan' }
            ]
          },
          {
            key: 'agendaHarian', label: '3. Agenda Harian Kepsek',
            fields: [
              { name: 'tanggal', label: 'Tanggal', type: 'date', sample: '2026-11-26' },
              { name: 'waktu', label: 'Waktu', type: 'text', sample: '08:00 - 10:00' },
              { name: 'kegiatan', label: 'Uraian Kegiatan', type: 'text', sample: 'Monitoring Kesiapan Ruang Ujian ASAS' },
              { name: 'tempat', label: 'Tempat', type: 'text', sample: 'Seluruh Ruang Kelas' },
              { name: 'keterangan', label: 'Keterangan', type: 'text', sample: 'Bersama panitia ASAS' }
            ]
          },
          {
            key: 'agendaKegiatan', label: '4. Buku Agenda Kegiatan',
            fields: [
              { name: 'tanggal', label: 'Tanggal', type: 'date', sample: '2026-12-18' },
              { name: 'namaKegiatan', label: 'Nama Kegiatan Sekolah', type: 'text', sample: 'Class Meeting & Pentas Seni Akhir Semester' },
              { name: 'penyelenggara', label: 'Penyelenggara', type: 'text', sample: 'Guru PJOK & Seni Budaya' },
              { name: 'sasaran', label: 'Sasaran', type: 'text', sample: 'Seluruh Peserta Didik' },
              { name: 'hasil', label: 'Hasil / Evaluasi', type: 'text', sample: 'Terjadwal dalam kalender akademik' }
            ]
          },
          {
            key: 'tamuUmum', label: '5. Buku Tamu Umum',
            fields: [
              { name: 'tanggal', label: 'Tanggal', type: 'date', sample: '2026-11-20' },
              { name: 'nama', label: 'Nama Tamu', type: 'text', sample: 'dr. Andi Wijaya' },
              { name: 'instansi', label: 'Instansi / Alamat', type: 'text', sample: 'Puskesmas Kecamatan' },
              { name: 'keperluan', label: 'Keperluan', type: 'text', sample: 'Koordinasi program skrining kesehatan anak sekolah' },
              { name: 'tindakLanjut', label: 'Tindak Lanjut', type: 'text', sample: 'Dijadwalkan pelaksanaan pada minggu depan' }
            ]
          },
          {
            key: 'tamuPembinaan', label: '6. Buku Tamu Pembinaan',
            fields: [
              { name: 'tanggal', label: 'Tanggal', type: 'date', sample: '2026-11-21' },
              { name: 'nama', label: 'Nama Pejabat / Pembina', type: 'text', sample: 'Drs. H. Mulyana, M.Pd.' },
              { name: 'jabatan', label: 'Jabatan', type: 'text', sample: 'Pengawas Sekolah' },
              { name: 'materiPembinaan', label: 'Materi Pembinaan', type: 'text', sample: 'Evaluasi Administrasi Kepala Sekolah & Rapor Pendidikan' },
              { name: 'saran', label: 'Saran & Arahan', type: 'text', sample: 'Pertahankan dokumentasi digital dan tindak lanjut supervisi' }
            ]
          },
          {
            key: 'notulenRapat', label: '7. Buku Notulen Rapat',
            fields: [
              { name: 'tanggal', label: 'Tanggal Rapat', type: 'date', sample: '2026-11-22' },
              { name: 'topik', label: 'Agenda / Topik Rapat', type: 'text', sample: 'Rapat Pembagian Tugas Semester Genap' },
              { name: 'pimpinan', label: 'Pimpinan Rapat', type: 'text', sample: 'Kepala Sekolah' },
              { name: 'peserta', label: 'Peserta Rapat', type: 'text', sample: 'Seluruh Guru & Tenaga Kependidikan' },
              { name: 'keputusan', label: 'Hasil / Keputusan Rapat', type: 'textarea', sample: '1. Jadwal pelajaran semester genap ditetapkan.\n2. Penguatan klinik numerasi tiap hari Sabtu.' }
            ]
          },
          {
            key: 'suratMasuk', label: '8. Buku Surat Masuk',
            fields: [
              { name: 'nomorSurat', label: 'Nomor Surat', type: 'text', sample: '421.2/1580/Disdik/2026' },
              { name: 'tanggalSurat', label: 'Tanggal Surat', type: 'date', sample: '2026-11-18' },
              { name: 'tanggalTerima', label: 'Tanggal Diterima', type: 'date', sample: '2026-11-19' },
              { name: 'pengirim', label: 'Pengirim', type: 'text', sample: 'Dinas Pendidikan' },
              { name: 'perihal', label: 'Perihal', type: 'text', sample: 'Edaran Libur Semester Ganjil dan Pembagian Rapor' },
              { name: 'disposisi', label: 'Disposisi Kepala Sekolah', type: 'text', sample: 'Informasikan kepada seluruh guru dan wali murid' }
            ]
          },
          {
            key: 'suratKeluar', label: '9. Buku Surat Keluar',
            fields: [
              { name: 'nomorSurat', label: 'Nomor Surat', type: 'text', sample: '421.2/095/SDN1/XI/2026' },
              { name: 'tanggal', label: 'Tanggal Surat', type: 'date', sample: '2026-11-20' },
              { name: 'tujuan', label: 'Tujuan Surat', type: 'text', sample: 'Kepala Dinas Pendidikan' },
              { name: 'perihal', label: 'Perihal', type: 'text', sample: 'Laporan Bulanan Keadaan Siswa dan Guru' },
              { name: 'keterangan', label: 'Keterangan', type: 'text', sample: 'Arsip di map surat keluar' }
            ]
          },
          {
            key: 'ekspedisiSurat', label: '10. Buku Ekspedisi Surat',
            fields: [
              { name: 'tanggal', label: 'Tanggal Kirim', type: 'date', sample: '2026-11-20' },
              { name: 'nomorSurat', label: 'Nomor Surat', type: 'text', sample: '421.2/095/SDN1/XI/2026' },
              { name: 'tujuan', label: 'Alamat / Instansi Tujuan', type: 'text', sample: 'Dinas Pendidikan' },
              { name: 'penerima', label: 'Nama Penerima', type: 'text', sample: 'Petugas Loket Persuratan' },
              { name: 'paraf', label: 'Status / Paraf', type: 'text', sample: 'Diterima & Distempel' }
            ]
          },
          {
            key: 'inventarisDokumen', label: '11. Buku Inventaris Dokumen',
            fields: [
              { name: 'kodeDok', label: 'Kode Dokumen', type: 'text', sample: 'DOK-SUP-03' },
              { name: 'namaDokumen', label: 'Nama Dokumen', type: 'text', sample: 'Program & Laporan Supervisi Akademik Semester 1' },
              { name: 'kategori', label: 'Kategori', type: 'text', sample: 'Supervisi' },
              { name: 'lokasiSimpan', label: 'Lokasi Penyimpanan', type: 'text', sample: 'Lemari Arsip Kepala Sekolah' },
              { name: 'kondisi', label: 'Kondisi / Kelengkapan', type: 'text', sample: 'Lengkap & Terjilid' }
            ]
          },
          {
            key: 'kegiatanKepsek', label: '12. Buku Kegiatan Kepsek',
            fields: [
              { name: 'tanggal', label: 'Tanggal', type: 'date', sample: '2026-11-21' },
              { name: 'jenisKegiatan', label: 'Jenis Tugas / Kegiatan', type: 'text', sample: 'Pengelolaan Manajerial & Keuangan' },
              { name: 'uraian', label: 'Uraian Pelaksanaan', type: 'textarea', sample: 'Memeriksa kesesuaian buku kas umum BOS dan bukti kuitansi belanja operasional.' },
              { name: 'output', label: 'Hasil / Output', type: 'text', sample: 'SPJ tertib dan terverifikasi' }
            ]
          },
          {
            key: 'dokumentasiKegiatan', label: '13. Dokumentasi Kegiatan',
            fields: [
              { name: 'tanggal', label: 'Tanggal', type: 'date', sample: '2026-11-25' },
              { name: 'judul', label: 'Judul Dokumentasi Kegiatan', type: 'text', sample: 'Upacara Peringatan Hari Guru Nasional 2026' },
              { name: 'deskripsi', label: 'Deskripsi Kegiatan', type: 'textarea', sample: 'Seluruh guru dan siswa mengikuti upacara dengan khidmat dilanjutkan pemberian apresiasi guru berdedikasi.' },
              { name: 'fotoUrl', label: 'Tautan Foto / Keterangan File', type: 'text', sample: 'Folder Dokumentasi HGN 2026' }
            ]
          }
        ],

        switchTab(tabKey) {
          this.activeTab = tabKey;
          this.searchQuery = '';
          this.render(document.getElementById('app-content'));
        },

        render(container) {
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          const records = DB.data.adminUmum[currentTab.key] || [];
          const q = this.searchQuery.toLowerCase();
          const filtered = records.filter(item =>
            Object.values(item).some(val => String(val).toLowerCase().includes(q))
          );

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Administrasi Umum Kepala Sekolah',
                'Pengelolaan lengkap 13 buku administrasi wajib kepala sekolah beserta cetak & ekspor Word.',
                'fa-solid fa-folder-open',
                `
                  <button onclick="Modules.AdminUmum.openFormModal()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-plus text-gold-400"></i> Tambah Data
                  </button>
                  <button onclick="Modules.AdminUmum.printCurrentTab()" class="px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-print"></i> Cetak
                  </button>
                  <button onclick="Modules.AdminUmum.exportWordCurrentTab()" class="px-3.5 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-word"></i> Unduh Word
                  </button>
                `
              )}

              ${UI.guideBanner(
                'Cara Mengelola 13 Buku Administrasi Umum',
                'Pilih salah satu buku administrasi di bawah ini, lalu klik "Tambah Data" untuk mencatat kegiatan baru. Di dalam formulir tersedia tombol "Isi Contoh" untuk memudahkan pengisian cepat.'
              )}

              <!-- Navigasi 13 Buku -->
              <div class="flex flex-wrap gap-2 bg-white p-3 rounded-2xl border border-slate-200 shadow-sm">
                ${this.tabs.map(t => `
                  <button onclick="Modules.AdminUmum.switchTab('${t.key}')" class="px-3 py-2 rounded-xl text-xs font-bold transition ${t.key === currentTab.key ? 'bg-navy-900 text-gold-400 shadow' : 'bg-slate-100 hover:bg-slate-200 text-slate-700'}">
                    ${t.label}
                  </button>
                `).join('')}
              </div>

              <!-- Pencarian & Tabel Data -->
              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="p-4 border-b border-slate-100 flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                  <div class="font-extrabold text-sm text-navy-900">${currentTab.label} (${filtered.length} Data)</div>
                  <div class="relative w-full sm:w-72">
                    <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-3 text-slate-400 text-xs"></i>
                    <input type="text" value="${ExportUtils.escape(this.searchQuery)}" oninput="Modules.AdminUmum.onSearch(this.value)" placeholder="Cari data pada buku ini..." class="w-full pl-9 pr-3 py-2 border border-slate-300 rounded-xl text-xs outline-none focus:border-navy-800" />
                  </div>
                </div>

                <div class="overflow-x-auto">
                  <table class="w-full text-left border-collapse text-xs">
                    <thead>
                      <tr class="bg-navy-900 text-white">
                        <th class="p-3.5 font-bold w-10">No</th>
                        ${currentTab.fields.map(f => `<th class="p-3.5 font-bold">${ExportUtils.escape(f.label)}</th>`).join('')}
                        <th class="p-3.5 font-bold text-center w-28">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${filtered.length === 0
                        ? `<tr><td colspan="${currentTab.fields.length + 2}" class="p-8 text-center text-slate-500">Belum ada data pada buku ini. Klik tombol "Tambah Data".</td></tr>`
                        : filtered.map((row, idx) => `
                          <tr class="hover:bg-slate-50">
                            <td class="p-3.5 font-bold text-slate-500">${idx + 1}</td>
                            ${currentTab.fields.map(f => {
                              let val = row[f.name] ?? '-';
                              if (f.name === 'biaya') val = ExportUtils.formatRupiah(val);
                              if (f.name === 'progres') val = `${val}%`;
                              return `<td class="p-3.5 text-slate-700 whitespace-pre-line">${ExportUtils.escape(val)}</td>`;
                            }).join('')}
                            <td class="p-3.5 text-center whitespace-nowrap">
                              <button onclick="Modules.AdminUmum.openFormModal('${row.id}')" title="Edit" class="p-2 rounded-lg bg-amber-50 hover:bg-amber-100 text-amber-700 mr-1">
                                <i class="fa-solid fa-pen-to-square"></i>
                              </button>
                              <button onclick="Modules.AdminUmum.deleteRecord('${row.id}')" title="Hapus" class="p-2 rounded-lg bg-rose-50 hover:bg-rose-100 text-rose-700">
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

        onSearch(val) {
          this.searchQuery = val;
          this.render(document.getElementById('app-content'));
        },

        openFormModal(editId = null) {
          if (!UI.canEdit()) return;
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          const existing = editId ? (DB.data.adminUmum[currentTab.key] || []).find(x => x.id === editId) : null;

          const fieldsHtml = currentTab.fields.map(f => {
            const val = existing ? (existing[f.name] ?? '') : '';
            if (f.type === 'select') {
              return `
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1">${f.label}</label>
                  <select id="adm-f-${f.name}" class="w-full border border-slate-300 rounded-xl px-3 py-2 text-xs bg-white">
                    ${f.options.map(opt => `<option value="${opt}" ${val === opt ? 'selected' : ''}>${opt}</option>`).join('')}
                  </select>
                </div>
              `;
            }
            if (f.type === 'textarea') {
              return `
                <div class="sm:col-span-2">
                  <label class="block text-xs font-bold text-slate-700 mb-1">${f.label}</label>
                  <textarea id="adm-f-${f.name}" rows="3" class="w-full border border-slate-300 rounded-xl px-3 py-2 text-xs">${ExportUtils.escape(val)}</textarea>
                </div>
              `;
            }
            return `
              <div>
                <label class="block text-xs font-bold text-slate-700 mb-1">${f.label}</label>
                <input type="${f.type}" id="adm-f-${f.name}" value="${ExportUtils.escape(val)}" class="w-full border border-slate-300 rounded-xl px-3 py-2 text-xs" />
              </div>
            `;
          }).join('');

          UI.openModal({
            title: `${existing ? 'Edit Data' : 'Tambah Data'} — ${currentTab.label}`,
            bodyHtml: `<div class="grid grid-cols-1 sm:grid-cols-2 gap-4">${fieldsHtml}</div>`,
            footerHtml: `
              <button type="button" onclick="Modules.AdminUmum.fillSampleForm()" class="px-3.5 py-2 bg-gold-100 hover:bg-gold-200 text-gold-900 rounded-xl text-xs font-bold mr-auto">
                <i class="fa-solid fa-magic mr-1"></i> Isi Contoh
              </button>
              <button type="button" onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 text-slate-800 rounded-xl text-xs font-semibold">Batal</button>
              <button type="button" onclick="Modules.AdminUmum.saveForm('${editId || ''}')" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">
                <i class="fa-solid fa-floppy-disk text-gold-400 mr-1"></i> Simpan Data
              </button>
            `
          });
        },

        fillSampleForm() {
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          currentTab.fields.forEach(f => {
            const el = document.getElementById(`adm-f-${f.name}`);
            if (el) el.value = f.sample;
          });
          UI.toast('Contoh pengisian telah dimasukkan ke formulir.', 'info');
        },

        saveForm(editId) {
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          const record = { id: editId || DB.uid('adm') };
          currentTab.fields.forEach(f => {
            const el = document.getElementById(`adm-f-${f.name}`);
            record[f.name] = el ? el.value : '';
          });
          const list = DB.data.adminUmum[currentTab.key];
          if (editId) {
            const idx = list.findIndex(x => x.id === editId);
            if (idx !== -1) list[idx] = record;
          } else {
            list.unshift(record);
          }
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        deleteRecord(id) {
          if (!UI.canEdit()) return;
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          UI.confirmAction('Hapus Data Administrasi', 'Data yang dihapus tidak dapat dikembalikan. Lanjutkan?', () => {
            DB.data.adminUmum[currentTab.key] = DB.data.adminUmum[currentTab.key].filter(x => x.id !== id);
            DB.save(true);
            this.render(document.getElementById('app-content'));
          });
        },

        buildTableHtmlForExport() {
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          const list = DB.data.adminUmum[currentTab.key] || [];
          return `
            <h2 style="text-align:center;text-transform:uppercase;margin-bottom:4px;">${ExportUtils.escape(currentTab.label)}</h2>
            <p style="text-align:center;margin-top:0;margin-bottom:14px;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <table style="width:100%;border-collapse:collapse;font-size:10.5pt;">
              <thead>
                <tr>
                  <th style="border:1px solid #000;padding:6px;">No</th>
                  ${currentTab.fields.map(f => `<th style="border:1px solid #000;padding:6px;">${ExportUtils.escape(f.label)}</th>`).join('')}
                </tr>
              </thead>
              <tbody>
                ${list.map((row, idx) => `
                  <tr>
                    <td style="border:1px solid #000;padding:6px;">${idx + 1}</td>
                    ${currentTab.fields.map(f => {
                      let val = row[f.name] ?? '-';
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
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          ExportUtils.printDocument(currentTab.label, this.buildTableHtmlForExport(), true, true);
        },

        exportWordCurrentTab() {
          const currentTab = this.tabs.find(t => t.key === this.activeTab) || this.tabs[0];
          ExportUtils.downloadWord(currentTab.label.replace(/[^a-zA-Z0-9]/g, '_'), this.buildTableHtmlForExport(), true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 4: PERENCANAAN, RAPOR PENDIDIKAN AI, RKJM, RKT & RKAS/ARKAS
      ------------------------------------------------------------------- */
      Perencanaan: {
        activeTab: 'rapor',

        switchTab(tab) {
          this.activeTab = tab;
          this.render(document.getElementById('app-content'));
        },

        render(container) {
          const p = DB.data.perencanaan;
          const rp = p.raporPendidikan;

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Perencanaan & Penganggaran Sekolah',
                'Analisis Rapor Pendidikan Berbantuan AI, RKJM, RKT, dan Penyusunan Kertas Kerja RKAS / Persiapan ARKAS.',
                'fa-solid fa-chart-line',
                `
                  <button onclick="Modules.Perencanaan.exportRkasExcel()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-excel"></i> Ekspor RKAS Excel
                  </button>
                  <button onclick="Modules.Perencanaan.exportWordSection()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-word text-gold-400"></i> Unduh Perencanaan Word
                  </button>
                `
              )}

              ${UI.guideBanner(
                'Perencanaan Berbasis Data (PBD) & Persiapan ARKAS',
                'Masukkan skor Rapor Pendidikan sekolah Anda lalu klik "Analisis Rapor Pendidikan dengan AI". Seluruh rekomendasi dapat diperiksa, diedit, dan disetujui untuk masuk otomatis ke RKT dan Draft RKAS sebagai bahan input resmi ke aplikasi ARKAS Kemendikdasmen.'
              )}

              <!-- Tab Submodul -->
              <div class="flex flex-wrap gap-2 bg-white p-2.5 rounded-2xl border border-slate-200 shadow-sm">
                <button onclick="Modules.Perencanaan.switchTab('rapor')" class="px-4 py-2 rounded-xl text-xs font-bold ${this.activeTab === 'rapor' ? 'bg-navy-900 text-gold-400' : 'bg-slate-100 text-slate-700'}">
                  <i class="fa-solid fa-wand-magic-sparkles mr-1"></i> Rapor Pendidikan & Analisis AI
                </button>
                <button onclick="Modules.Perencanaan.switchTab('rkjm')" class="px-4 py-2 rounded-xl text-xs font-bold ${this.activeTab === 'rkjm' ? 'bg-navy-900 text-gold-400' : 'bg-slate-100 text-slate-700'}">
                  RKJM (Jangka Menengah)
                </button>
                <button onclick="Modules.Perencanaan.switchTab('rkt')" class="px-4 py-2 rounded-xl text-xs font-bold ${this.activeTab === 'rkt' ? 'bg-navy-900 text-gold-400' : 'bg-slate-100 text-slate-700'}">
                  RKT (Rencana Kerja Tahunan)
                </button>
                <button onclick="Modules.Perencanaan.switchTab('rkas')" class="px-4 py-2 rounded-xl text-xs font-bold ${this.activeTab === 'rkas' ? 'bg-navy-900 text-gold-400' : 'bg-slate-100 text-slate-700'}">
                  RKAS & Persiapan ARKAS
                </button>
              </div>

              ${this.activeTab === 'rapor' ? this.renderRaporTab(rp) : ''}
              ${this.activeTab === 'rkjm' ? this.renderRkjmTab(p.rkjm) : ''}
              ${this.activeTab === 'rkt' ? this.renderRktTab(p.rkt) : ''}
              ${this.activeTab === 'rkas' ? this.renderRkasTab(p.rkas) : ''}
            </div>
          `;
        },

        renderRaporTab(rp) {
          return `
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
              <!-- Input Nilai Rapor Pendidikan -->
              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900 border-b border-slate-100 pb-2">Input Capaian Rapor Pendidikan</h3>
                <div class="grid grid-cols-2 gap-3 text-xs">
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Tahun Rapor</label>
                    <input type="text" id="rp-tahun" value="${ExportUtils.escape(rp.tahun)}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Literasi (%)</label>
                    <input type="number" step="0.1" id="rp-lit" value="${rp.literasi}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Numerasi (%)</label>
                    <input type="number" step="0.1" id="rp-num" value="${rp.numerasi}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Karakter (%)</label>
                    <input type="number" step="0.1" id="rp-kar" value="${rp.karakter}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Iklim Keamanan (%)</label>
                    <input type="number" step="0.1" id="rp-aman" value="${rp.iklimKeamanan}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Iklim Kebinekaan (%)</label>
                    <input type="number" step="0.1" id="rp-bineka" value="${rp.iklimKebinekaan}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div class="col-span-2">
                    <label class="font-bold text-slate-700 block mb-1">Kualitas Pembelajaran (%)</label>
                    <input type="number" step="0.1" id="rp-kualitas" value="${rp.kualitasPembelajaran}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div class="col-span-2">
                    <label class="font-bold text-slate-700 block mb-1">Catatan Konteks / Masalah Sekolah</label>
                    <textarea id="rp-catatan" rows="3" class="w-full border border-slate-300 rounded-xl px-3 py-2">${ExportUtils.escape(rp.catatanTambahan)}</textarea>
                  </div>
                </div>
                <button onclick="Modules.Perencanaan.runAiRaporAnalysis()" id="btn-ai-rapor" class="w-full py-3 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-extrabold shadow-md flex items-center justify-center gap-2 transition">
                  <i class="fa-solid fa-wand-magic-sparkles text-gold-400"></i> Analisis Rapor Pendidikan dengan AI
                </button>
              </div>

              <!-- Hasil Analisis & Rekomendasi AI -->
              <div class="lg:col-span-2 space-y-4">
                <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm">
                  <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 mb-4">
                    <div>
                      <h3 class="text-base font-extrabold text-navy-900">Hasil Identifikasi Akar Masalah & Rekomendasi AI</h3>
                      <p class="text-xs text-slate-500">Periksa, edit, dan setujui rekomendasi untuk masuk ke RKT & Draft RKAS.</p>
                    </div>
                  </div>
                  <div class="space-y-4">
                    ${rp.aiRecommendations.map(rec => `
                      <div class="p-4 rounded-2xl border ${rec.disetujui ? 'bg-emerald-50/40 border-emerald-300' : 'bg-slate-50 border-slate-200'} space-y-2.5">
                        <div class="flex flex-wrap items-center justify-between gap-2">
                          <span class="px-3 py-1 rounded-full bg-navy-900 text-gold-400 text-xs font-bold">${ExportUtils.escape(rec.indikator)}</span>
                          <span class="px-2.5 py-0.5 rounded-full text-[11px] font-bold ${rec.disetujui ? 'bg-emerald-600 text-white' : 'bg-amber-100 text-amber-800'}">
                            ${rec.disetujui ? 'Disetujui Kepala Sekolah' : 'Menunggu Persetujuan'}
                          </span>
                        </div>
                        <div class="text-xs space-y-1.5 text-slate-700">
                          <div><strong>Akar Masalah:</strong> ${ExportUtils.escape(rec.akarMasalah)}</div>
                          <div><strong>Rekomendasi Kegiatan:</strong> ${ExportUtils.escape(rec.rekomendasiKegiatan)}</div>
                          <div><strong>Program Prioritas:</strong> ${ExportUtils.escape(rec.programPrioritas)} • <strong>Keterkaitan:</strong> ${ExportUtils.escape(rec.linkRkjmRkt)}</div>
                          <div class="p-2.5 rounded-xl bg-white border border-slate-200 font-semibold text-navy-900">
                            <i class="fa-solid fa-coins text-gold-600 mr-1"></i> Usulan Pembiayaan RKAS: ${ExportUtils.escape(rec.usulanRkas)}
                          </div>
                        </div>
                        <div class="pt-2 flex flex-wrap gap-2">
                          <button onclick="Modules.Perencanaan.editRecommendation('${rec.id}')" class="px-3 py-1.5 bg-white border border-slate-300 hover:bg-slate-100 rounded-lg text-xs font-bold text-slate-700">
                            <i class="fa-solid fa-pen mr-1"></i> Edit Rekomendasi
                          </button>
                          <button onclick="Modules.Perencanaan.approveRecommendation('${rec.id}')" class="px-3 py-1.5 bg-emerald-700 hover:bg-emerald-800 text-white rounded-lg text-xs font-bold">
                            <i class="fa-solid fa-check mr-1"></i> ${rec.disetujui ? 'Sudah Masuk RKT & RKAS' : 'Setujui ke RKT & RKAS'}
                          </button>
                        </div>
                      </div>
                    `).join('')}
                  </div>
                </div>
              </div>
            </div>
          `;
        },

        async runAiRaporAnalysis() {
          if (!UI.canEdit()) return;
          const rp = DB.data.perencanaan.raporPendidikan;
          rp.tahun = document.getElementById('rp-tahun').value.trim() || '2026';
          rp.literasi = Number(document.getElementById('rp-lit').value) || 0;
          rp.numerasi = Number(document.getElementById('rp-num').value) || 0;
          rp.karakter = Number(document.getElementById('rp-kar').value) || 0;
          rp.iklimKeamanan = Number(document.getElementById('rp-aman').value) || 0;
          rp.iklimKebinekaan = Number(document.getElementById('rp-bineka').value) || 0;
          rp.kualitasPembelajaran = Number(document.getElementById('rp-kualitas').value) || 0;
          rp.catatanTambahan = document.getElementById('rp-catatan').value.trim();

          const btn = document.getElementById('btn-ai-rapor');
          if (btn) btn.innerHTML = '<i class="fa-solid fa-spinner fa-spin"></i> AI Sedang Menganalisis Data...';

          const indicators = [
            { name: 'Kemampuan Literasi', score: rp.literasi },
            { name: 'Kemampuan Numerasi', score: rp.numerasi },
            { name: 'Karakter', score: rp.karakter },
            { name: 'Iklim Keamanan Sekolah', score: rp.iklimKeamanan },
            { name: 'Iklim Kebinekaan', score: rp.iklimKebinekaan },
            { name: 'Kualitas Pembelajaran', score: rp.kualitasPembelajaran }
          ].sort((a, b) => a.score - b.score);

          const prompt = `Analisis data Rapor Pendidikan tahun ${rp.tahun}: ${JSON.stringify(indicators)}. Catatan sekolah: ${rp.catatanTambahan}. Berikan 2 atau 3 rekomendasi perbaikan dalam format JSON array berisi objek dengan key: indikator, akarMasalah, rekomendasiKegiatan, programPrioritas, linkRkjmRkt, usulanRkas.`;
          const aiRaw = await AI.generate(prompt, '', true);

          let generatedRecs = null;
          if (aiRaw) {
            try {
              const parsed = JSON.parse(aiRaw);
              if (Array.isArray(parsed) && parsed.length > 0) {
                generatedRecs = parsed.map(item => ({
                  id: DB.uid('rec'),
                  indikator: item.indikator || indicators[0].name,
                  akarMasalah: item.akarMasalah || '',
                  rekomendasiKegiatan: item.rekomendasiKegiatan || '',
                  programPrioritas: item.programPrioritas || '',
                  linkRkjmRkt: item.linkRkjmRkt || 'RKJM / RKT',
                  usulanRkas: item.usulanRkas || '',
                  disetujui: false
                }));
              }
            } catch (e) {}
          }

          if (!generatedRecs) {
            // Analisis heuristik otomatis berdasarkan 2 indikator terendah
            generatedRecs = indicators.slice(0, 2).map(ind => ({
              id: DB.uid('rec'),
              indikator: `${ind.name} (${ind.score}% - Perlu Peningkatan)`,
              akarMasalah: `Capaian ${ind.name} sebesar ${ind.score}% masih berada di bawah target optimal (>85%), dipengaruhi belum meratanya pembelajaran aktif dan asesmen formatif terstruktur.`,
              rekomendasiKegiatan: `Pelatihan Internal (IHT) Penguatan ${ind.name} & Optimalisasi Komunitas Belajar (Kombel) Guru`,
              programPrioritas: `Program Peningkatan Mutu ${ind.name}`,
              linkRkjmRkt: 'RKJM Pilar Mutu Pembelajaran / RKT Tahun Berjalan',
              usulanRkas: `Belanja Narasumber Kombel, Penggandaan Modul Praktik Baik & Alat Peraga (Rp 4.000.000)`,
              disetujui: false
            }));
          }

          rp.aiRecommendations = generatedRecs;
          DB.save(true);
          UI.toast('Analisis Rapor Pendidikan AI selesai disusun!', 'success');
          this.render(document.getElementById('app-content'));
        },

        editRecommendation(recId) {
          if (!UI.canEdit()) return;
          const rec = DB.data.perencanaan.raporPendidikan.aiRecommendations.find(r => r.id === recId);
          if (!rec) return;
          UI.openModal({
            title: 'Periksa & Edit Rekomendasi AI',
            bodyHtml: `
              <div class="space-y-3 text-xs">
                <div>
                  <label class="font-bold block mb-1">Indikator Prioritas</label>
                  <input type="text" id="ed-rec-ind" value="${ExportUtils.escape(rec.indikator)}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div>
                  <label class="font-bold block mb-1">Akar Masalah</label>
                  <textarea id="ed-rec-akar" rows="2" class="w-full border border-slate-300 rounded-xl px-3 py-2">${ExportUtils.escape(rec.akarMasalah)}</textarea>
                </div>
                <div>
                  <label class="font-bold block mb-1">Rekomendasi Kegiatan</label>
                  <input type="text" id="ed-rec-keg" value="${ExportUtils.escape(rec.rekomendasiKegiatan)}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div>
                  <label class="font-bold block mb-1">Usulan Pembiayaan RKAS / ARKAS</label>
                  <input type="text" id="ed-rec-rkas" value="${ExportUtils.escape(rec.usulanRkas)}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Perencanaan.saveEditedRec('${rec.id}')" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Perubahan</button>
            `
          });
        },

        saveEditedRec(recId) {
          const rec = DB.data.perencanaan.raporPendidikan.aiRecommendations.find(r => r.id === recId);
          if (!rec) return;
          rec.indikator = document.getElementById('ed-rec-ind').value.trim();
          rec.akarMasalah = document.getElementById('ed-rec-akar').value.trim();
          rec.rekomendasiKegiatan = document.getElementById('ed-rec-keg').value.trim();
          rec.usulanRkas = document.getElementById('ed-rec-rkas').value.trim();
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        approveRecommendation(recId) {
          if (!UI.canEdit()) return;
          const rec = DB.data.perencanaan.raporPendidikan.aiRecommendations.find(r => r.id === recId);
          if (!rec) return;
          if (!rec.disetujui) {
            rec.disetujui = true;
            DB.data.perencanaan.rkt.unshift({
              id: DB.uid('rkt'),
              program: rec.rekomendasiKegiatan,
              tujuan: `Mengatasi akar masalah: ${rec.akarMasalah}`,
              sasaran: 'Guru & Peserta Didik',
              waktu: 'Tahun Pelajaran Berjalan',
              indikator: `Peningkatan skor ${rec.indikator}`,
              status: 'Disetujui dari Rapor Pendidikan'
            });
            DB.data.perencanaan.rkas.unshift({
              id: DB.uid('rkas'),
              kodeKegiatan: '03.03.99',
              uraianKegiatan: rec.usulanRkas,
              sumberDana: 'BOS Reguler',
              volume: '1 Paket',
              anggaran: 4000000,
              realisasi: 0,
              status: 'Usulan Draft ARKAS'
            });
            DB.save(true);
            UI.toast('Rekomendasi disetujui dan ditambahkan ke RKT & Draft RKAS!', 'success');
          } else {
            UI.toast('Rekomendasi ini sudah disetujui sebelumnya.', 'info');
          }
          this.render(document.getElementById('app-content'));
        },

        renderRkjmTab(list) {
          return `
            <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
              <div class="p-4 border-b border-slate-100 flex items-center justify-between">
                <h3 class="font-extrabold text-sm text-navy-900">Rencana Kerja Jangka Menengah (RKJM 4 Tahun)</h3>
                <button onclick="Modules.Perencanaan.addSimpleItem('rkjm')" class="px-3 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">+ Tambah RKJM</button>
              </div>
              <div class="overflow-x-auto">
                <table class="w-full text-left text-xs">
                  <thead class="bg-navy-900 text-white">
                    <tr><th class="p-3">Sasaran Strategis</th><th class="p-3">Program Jangka Menengah</th><th class="p-3">Tahun</th><th class="p-3">Indikator</th><th class="p-3">Aksi</th></tr>
                  </thead>
                  <tbody class="divide-y divide-slate-200">
                    ${list.map(r => `
                      <tr>
                        <td class="p-3">${ExportUtils.escape(r.sasaranStrategis)}</td>
                        <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(r.programJangkaMenengah)}</td>
                        <td class="p-3">${ExportUtils.escape(r.tahunTarget)}</td>
                        <td class="p-3">${ExportUtils.escape(r.indikatorKeberhasilan)}</td>
                        <td class="p-3"><button onclick="Modules.Perencanaan.deleteItem('rkjm','${r.id}')" class="text-rose-600 font-bold">Hapus</button></td>
                      </tr>
                    `).join('')}
                  </tbody>
                </table>
              </div>
            </div>
          `;
        },

        renderRktTab(list) {
          return `
            <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
              <div class="p-4 border-b border-slate-100 flex items-center justify-between">
                <h3 class="font-extrabold text-sm text-navy-900">Rencana Kerja Tahunan (RKT)</h3>
                <button onclick="Modules.Perencanaan.addSimpleItem('rkt')" class="px-3 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">+ Tambah RKT</button>
              </div>
              <div class="overflow-x-auto">
                <table class="w-full text-left text-xs">
                  <thead class="bg-navy-900 text-white">
                    <tr><th class="p-3">Program Kerja</th><th class="p-3">Tujuan</th><th class="p-3">Sasaran</th><th class="p-3">Waktu</th><th class="p-3">Status</th><th class="p-3">Aksi</th></tr>
                  </thead>
                  <tbody class="divide-y divide-slate-200">
                    ${list.map(r => `
                      <tr>
                        <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(r.program)}</td>
                        <td class="p-3">${ExportUtils.escape(r.tujuan)}</td>
                        <td class="p-3">${ExportUtils.escape(r.sasaran)}</td>
                        <td class="p-3">${ExportUtils.escape(r.waktu)}</td>
                        <td class="p-3"><span class="px-2 py-0.5 rounded bg-navy-50 text-navy-800 font-bold">${ExportUtils.escape(r.status)}</span></td>
                        <td class="p-3"><button onclick="Modules.Perencanaan.deleteItem('rkt','${r.id}')" class="text-rose-600 font-bold">Hapus</button></td>
                      </tr>
                    `).join('')}
                  </tbody>
                </table>
              </div>
            </div>
          `;
        },

        renderRkasTab(list) {
          const totalAnggaran = list.reduce((a, b) => a + Number(b.anggaran || 0), 0);
          const totalRealisasi = list.reduce((a, b) => a + Number(b.realisasi || 0), 0);
          return `
            <div class="space-y-4">
              <div class="bg-amber-50 border border-amber-200 rounded-2xl p-4 text-xs text-amber-900 flex items-start gap-3">
                <i class="fa-solid fa-circle-info text-amber-600 text-base mt-0.5"></i>
                <div>
                  <strong>Catatan Kertas Kerja ARKAS:</strong> Modul ini menyiapkan struktur kode kegiatan, volume, dan rincian anggaran sebagai bahan penyusunan resmi pada aplikasi <strong>ARKAS Kemendikdasmen</strong> (tidak melakukan sinkronisasi langsung tanpa otorisasi resmi).
                </div>
              </div>

              <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                <div class="bg-white p-4 rounded-2xl border border-slate-200">
                  <div class="text-xs text-slate-500">Total Rencana Anggaran RKAS</div>
                  <div class="text-lg font-extrabold text-navy-900 mt-1">${ExportUtils.formatRupiah(totalAnggaran)}</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200">
                  <div class="text-xs text-slate-500">Total Realisasi Serapan</div>
                  <div class="text-lg font-extrabold text-emerald-700 mt-1">${ExportUtils.formatRupiah(totalRealisasi)}</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200">
                  <div class="text-xs text-slate-500">Sisa Saldo Rencana</div>
                  <div class="text-lg font-extrabold text-gold-700 mt-1">${ExportUtils.formatRupiah(totalAnggaran - totalRealisasi)}</div>
                </div>
              </div>

              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="p-4 border-b border-slate-100 flex items-center justify-between">
                  <h3 class="font-extrabold text-sm text-navy-900">Rincian Kegiatan RKAS / Draft ARKAS</h3>
                  <button onclick="Modules.Perencanaan.addSimpleItem('rkas')" class="px-3 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">+ Tambah Kegiatan RKAS</button>
                </div>
                <div class="overflow-x-auto">
                  <table class="w-full text-left text-xs">
                    <thead class="bg-navy-900 text-white">
                      <tr><th class="p-3">Kode</th><th class="p-3">Uraian Kegiatan</th><th class="p-3">Sumber Dana</th><th class="p-3">Anggaran</th><th class="p-3">Realisasi</th><th class="p-3">Status</th><th class="p-3">Aksi</th></tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${list.map(r => `
                        <tr>
                          <td class="p-3 font-mono font-bold">${ExportUtils.escape(r.kodeKegiatan)}</td>
                          <td class="p-3 font-bold text-navy-900">${ExportUtils.escape(r.uraianKegiatan)}</td>
                          <td class="p-3">${ExportUtils.escape(r.sumberDana)}</td>
                          <td class="p-3 font-semibold">${ExportUtils.formatRupiah(r.anggaran)}</td>
                          <td class="p-3 text-emerald-700 font-semibold">${ExportUtils.formatRupiah(r.realisasi)}</td>
                          <td class="p-3"><span class="px-2 py-0.5 rounded bg-gold-100 text-gold-800 font-bold">${ExportUtils.escape(r.status)}</span></td>
                          <td class="p-3"><button onclick="Modules.Perencanaan.deleteItem('rkas','${r.id}')" class="text-rose-600 font-bold">Hapus</button></td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        addSimpleItem(type) {
          if (!UI.canEdit()) return;
          if (type === 'rkjm') {
            DB.data.perencanaan.rkjm.unshift({
              id: DB.uid('rkjm'),
              sasaranStrategis: 'Meningkatnya kualitas pembelajaran berdiferensiasi',
              programJangkaMenengah: 'Pengembangan Kompetensi Guru Berkelanjutan',
              tahunTarget: '2026 - 2029',
              indikatorKeberhasilan: '100% Guru menerapkan pembelajaran aktif',
              penanggungJawab: 'Kepala Sekolah'
            });
          } else if (type === 'rkt') {
            DB.data.perencanaan.rkt.unshift({
              id: DB.uid('rkt'),
              program: 'Penguatan Literasi Digital & Asesmen Formatif',
              tujuan: 'Meningkatkan hasil belajar siswa',
              sasaran: 'Seluruh Guru & Siswa',
              waktu: 'Semester Genap 2027',
              indikator: 'Terlaksana 100%',
              status: 'Direncanakan'
            });
          } else if (type === 'rkas') {
            DB.data.perencanaan.rkas.unshift({
              id: DB.uid('rkas'),
              kodeKegiatan: '03.05.01',
              uraianKegiatan: 'Pengadaan Alat Peraga Edukatif & Modul Literasi',
              sumberDana: 'BOS Reguler',
              volume: '1 Paket',
              anggaran: 3500000,
              realisasi: 0,
              status: 'Usulan Draft ARKAS'
            });
          }
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        deleteItem(type, id) {
          if (!UI.canEdit()) return;
          DB.data.perencanaan[type] = DB.data.perencanaan[type].filter(x => x.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        exportRkasExcel() {
          const rows = DB.data.perencanaan.rkas.map((r, idx) => ({
            No: idx + 1,
            'Kode Kegiatan': r.kodeKegiatan,
            'Uraian Kegiatan': r.uraianKegiatan,
            'Sumber Dana': r.sumberDana,
            Volume: r.volume,
            'Anggaran (Rp)': r.anggaran,
            'Realisasi (Rp)': r.realisasi,
            Status: r.status
          }));
          ExportUtils.exportExcel('Kertas_Kerja_RKAS_ARKAS.xlsx', 'RKAS_ARKAS', rows);
        },

        exportWordSection() {
          const p = DB.data.perencanaan;
          const html = `
            <h2 style="text-align:center;text-transform:uppercase;">DOKUMEN PERENCANAAN SEKOLAH (PBD, RKJM, RKT & RKAS)</h2>
            <p style="text-align:center;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <h3>A. Ringkasan Capaian Rapor Pendidikan (${ExportUtils.escape(p.raporPendidikan.tahun)})</h3>
            <ul>
              <li>Literasi: ${p.raporPendidikan.literasi}%</li>
              <li>Numerasi: ${p.raporPendidikan.numerasi}%</li>
              <li>Karakter: ${p.raporPendidikan.karakter}%</li>
              <li>Iklim Keamanan: ${p.raporPendidikan.iklimKeamanan}%</li>
              <li>Kualitas Pembelajaran: ${p.raporPendidikan.kualitasPembelajaran}%</li>
            </ul>
            <h3>B. Rencana Kerja Tahunan (RKT)</h3>
            <table>
              <tr><th>Program</th><th>Tujuan</th><th>Waktu</th><th>Status</th></tr>
              ${p.rkt.map(r => `<tr><td>${ExportUtils.escape(r.program)}</td><td>${ExportUtils.escape(r.tujuan)}</td><td>${ExportUtils.escape(r.waktu)}</td><td>${ExportUtils.escape(r.status)}</td></tr>`).join('')}
            </table>
          `;
          ExportUtils.downloadWord('Dokumen_Perencanaan_Sekolah', html, true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 5: SUPERVISI AKADEMIK DAN MANAJERIAL
      ------------------------------------------------------------------- */
      Supervisi: {
        render(container) {
          const records = DB.data.supervisi.records;
          const avgAll = records.length
            ? (records.reduce((acc, r) => acc + ((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3), 0) / records.length).toFixed(1)
            : 0;

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Supervisi Akademik & Manajerial',
                'Instrumen supervisi pembelajaran, penilaian perencanaan-pelaksanaan-asesmen, analisis & tindak lanjut.',
                'fa-solid fa-clipboard-check',
                `
                  <button onclick="Modules.Supervisi.openAddModal()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-plus text-gold-400"></i> Input Hasil Supervisi
                  </button>
                  <button onclick="Modules.Supervisi.printAll()" class="px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-print"></i> Cetak Rekap
                  </button>
                  <button onclick="Modules.Supervisi.exportAllWord()" class="px-3.5 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-word"></i> Unduh Word
                  </button>
                `
              )}

              <!-- Ringkasan & Instrumen -->
              <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm">
                  <div class="text-xs font-bold text-slate-500 uppercase">Rata-Rata Nilai Supervisi</div>
                  <div class="text-3xl font-extrabold text-navy-900 mt-2">${avgAll} <span class="text-xs font-bold text-emerald-700">(${avgAll >= 86 ? 'Amat Baik' : 'Baik'})</span></div>
                  <p class="text-xs text-slate-500 mt-2">Total ${records.length} guru telah disupervisi dan memiliki rencana tindak lanjut.</p>
                </div>
                <div class="lg:col-span-2 bg-white rounded-2xl p-5 border border-slate-200 shadow-sm">
                  <div class="flex items-center justify-between mb-2">
                    <h3 class="text-sm font-extrabold text-navy-900">Aspek Instrumen Penilaian Supervisi Sekolah</h3>
                    <button onclick="Modules.Supervisi.editInstrumenModal()" class="text-xs font-bold text-navy-800 hover:underline">Sesuaikan Instrumen</button>
                  </div>
                  <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 text-xs">
                    ${DB.data.supervisi.instrumenCustom.map(ins => `
                      <div class="p-3 rounded-xl bg-navy-50 border border-navy-100">
                        <div class="font-bold text-navy-900">${ExportUtils.escape(ins.aspek)}</div>
                        <div class="text-slate-600 mt-1 text-[11px]">${ExportUtils.escape(ins.indikator)}</div>
                      </div>
                    `).join('')}
                  </div>
                </div>
              </div>

              <!-- Tabel Hasil Supervisi -->
              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="overflow-x-auto">
                  <table class="w-full text-left text-xs">
                    <thead class="bg-navy-900 text-white">
                      <tr>
                        <th class="p-3.5">Tanggal</th>
                        <th class="p-3.5">Nama Guru / Mapel</th>
                        <th class="p-3.5 text-center">Perencanaan</th>
                        <th class="p-3.5 text-center">Pelaksanaan</th>
                        <th class="p-3.5 text-center">Asesmen</th>
                        <th class="p-3.5 text-center">Rata-Rata</th>
                        <th class="p-3.5">Rekomendasi & Tindak Lanjut</th>
                        <th class="p-3.5 text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${records.map(r => {
                        const avg = Math.round((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3);
                        return `
                          <tr class="hover:bg-slate-50">
                            <td class="p-3.5 whitespace-nowrap">${ExportUtils.escape(r.tanggal)}</td>
                            <td class="p-3.5">
                              <div class="font-bold text-navy-900">${ExportUtils.escape(r.namaGuru)}</div>
                              <div class="text-slate-500">${ExportUtils.escape(r.mapel)} (${ExportUtils.escape(r.kelas)})</div>
                            </td>
                            <td class="p-3.5 text-center font-bold">${r.skorPerencanaan}</td>
                            <td class="p-3.5 text-center font-bold">${r.skorPelaksanaan}</td>
                            <td class="p-3.5 text-center font-bold">${r.skorAsesmen}</td>
                            <td class="p-3.5 text-center"><span class="px-2.5 py-1 rounded-full bg-emerald-100 text-emerald-800 font-extrabold">${avg}</span></td>
                            <td class="p-3.5">
                              <div><strong>Rekomendasi:</strong> ${ExportUtils.escape(r.rekomendasi)}</div>
                              <div class="text-navy-800 mt-1"><strong>Tindak Lanjut:</strong> ${ExportUtils.escape(r.tindakLanjut)}</div>
                            </td>
                            <td class="p-3.5 text-center whitespace-nowrap">
                              <button onclick="Modules.Supervisi.printSingle('${r.id}')" title="Cetak Laporan Individual" class="p-2 rounded-lg bg-navy-50 hover:bg-navy-100 text-navy-900 mr-1">
                                <i class="fa-solid fa-print"></i>
                              </button>
                              <button onclick="Modules.Supervisi.deleteRecord('${r.id}')" title="Hapus" class="p-2 rounded-lg bg-rose-50 hover:bg-rose-100 text-rose-700">
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

        openAddModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Input Penilaian Supervisi Pembelajaran',
            bodyHtml: `
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 text-xs">
                <div>
                  <label class="font-bold block mb-1">Tanggal Supervisi</label>
                  <input type="date" id="sup-tgl" value="${new Date().toISOString().split('T')[0]}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div>
                  <label class="font-bold block mb-1">Nama Guru</label>
                  <input type="text" id="sup-guru" placeholder="Nama lengkap & gelar" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div>
                  <label class="font-bold block mb-1">NIP</label>
                  <input type="text" id="sup-nip" placeholder="NIP Guru" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div>
                  <label class="font-bold block mb-1">Mata Pelajaran & Kelas</label>
                  <input type="text" id="sup-mapel" placeholder="Contoh: Matematika / Kelas V-A" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div>
                  <label class="font-bold block mb-1">Skor Perencanaan (0-100)</label>
                  <input type="number" id="sup-s1" value="88" min="0" max="100" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div>
                  <label class="font-bold block mb-1">Skor Pelaksanaan (0-100)</label>
                  <input type="number" id="sup-s2" value="85" min="0" max="100" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div class="sm:col-span-2">
                  <label class="font-bold block mb-1">Skor Asesmen Pembelajaran (0-100)</label>
                  <input type="number" id="sup-s3" value="84" min="0" max="100" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
                <div class="sm:col-span-2">
                  <label class="font-bold block mb-1">Catatan Hasil Observasi</label>
                  <textarea id="sup-obs" rows="2" class="w-full border border-slate-300 rounded-xl px-3 py-2"></textarea>
                </div>
                <div class="sm:col-span-2">
                  <label class="font-bold block mb-1">Rekomendasi Perbaikan</label>
                  <textarea id="sup-rek" rows="2" class="w-full border border-slate-300 rounded-xl px-3 py-2"></textarea>
                </div>
                <div class="sm:col-span-2">
                  <label class="font-bold block mb-1">Rencana Tindak Lanjut</label>
                  <input type="text" id="sup-tl" placeholder="Contoh: Coaching klinis & berbagi praktik baik di Kombel" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                </div>
              </div>
            `,
            footerHtml: `
              <button onclick="Modules.Supervisi.autoSuggestAi()" class="px-3.5 py-2 bg-gold-100 text-gold-900 rounded-xl text-xs font-bold mr-auto">
                <i class="fa-solid fa-wand-magic-sparkles mr-1"></i> Susun Rekomendasi AI
              </button>
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Supervisi.saveRecord()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Hasil Supervisi</button>
            `
          });
        },

        autoSuggestAi() {
          const s1 = Number(document.getElementById('sup-s1').value) || 80;
          const s2 = Number(document.getElementById('sup-s2').value) || 80;
          const s3 = Number(document.getElementById('sup-s3').value) || 80;
          document.getElementById('sup-obs').value = `Perencanaan modul ajar terstruktur (skor ${s1}), pelaksanaan pembelajaran interaktif (skor ${s2}), dan pelaksanaan asesmen formatif berjalan baik (skor ${s3}).`;
          document.getElementById('sup-rek').value = 'Tingkatkan variasi pertanyaan pemantik HOTS serta penguatan refleksi individual di akhir pembelajaran.';
          document.getElementById('sup-tl').value = 'Pendampingan dalam forum Komunitas Belajar (Kombel) sekolah.';
          UI.toast('Rekomendasi supervisi berhasil disusun!', 'success');
        },

        saveRecord() {
          const namaGuru = document.getElementById('sup-guru').value.trim();
          if (!namaGuru) {
            UI.toast('Nama guru wajib diisi.', 'warning');
            return;
          }
          DB.data.supervisi.records.unshift({
            id: DB.uid('sup'),
            tanggal: document.getElementById('sup-tgl').value,
            namaGuru,
            nip: document.getElementById('sup-nip').value.trim() || '-',
            mapel: document.getElementById('sup-mapel').value.trim() || '-',
            kelas: '-',
            jenis: 'Supervisi Akademik',
            skorPerencanaan: Number(document.getElementById('sup-s1').value) || 0,
            skorPelaksanaan: Number(document.getElementById('sup-s2').value) || 0,
            skorAsesmen: Number(document.getElementById('sup-s3').value) || 0,
            catatanObservasi: document.getElementById('sup-obs').value.trim(),
            rekomendasi: document.getElementById('sup-rek').value.trim(),
            tindakLanjut: document.getElementById('sup-tl').value.trim()
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        editInstrumenModal() {
          if (!UI.canEdit()) return;
          const ins = DB.data.supervisi.instrumenCustom;
          UI.openModal({
            title: 'Sesuaikan Indikator Instrumen Supervisi',
            bodyHtml: `
              <div class="space-y-3 text-xs">
                ${ins.map((item, i) => `
                  <div>
                    <label class="font-bold block mb-1">${ExportUtils.escape(item.aspek)}</label>
                    <input type="text" id="ins-ind-${i}" value="${ExportUtils.escape(item.indikator)}" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                `).join('')}
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Supervisi.saveInstrumen()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Instrumen</button>
            `
          });
        },

        saveInstrumen() {
          DB.data.supervisi.instrumenCustom.forEach((item, i) => {
            const el = document.getElementById(`ins-ind-${i}`);
            if (el) item.indikator = el.value.trim();
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        deleteRecord(id) {
          if (!UI.canEdit()) return;
          DB.data.supervisi.records = DB.data.supervisi.records.filter(x => x.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        printSingle(id) {
          const r = DB.data.supervisi.records.find(x => x.id === id);
          if (!r) return;
          const avg = Math.round((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3);
          const html = `
            <h2 style="text-align:center;text-transform:uppercase;">LAPORAN HASIL SUPERVISI AKADEMIK INDIVIDUAL</h2>
            <p><strong>Nama Guru:</strong> ${ExportUtils.escape(r.namaGuru)}<br/>
            <strong>NIP:</strong> ${ExportUtils.escape(r.nip)}<br/>
            <strong>Mata Pelajaran / Kelas:</strong> ${ExportUtils.escape(r.mapel)}<br/>
            <strong>Tanggal Pelaksanaan:</strong> ${ExportUtils.escape(r.tanggal)}</p>
            <table>
              <tr><th>Aspek Penilaian</th><th>Skor</th></tr>
              <tr><td>1. Perencanaan Pembelajaran</td><td>${r.skorPerencanaan}</td></tr>
              <tr><td>2. Pelaksanaan Pembelajaran</td><td>${r.skorPelaksanaan}</td></tr>
              <tr><td>3. Asesmen Pembelajaran</td><td>${r.skorAsesmen}</td></tr>
              <tr><td><strong>Rata-Rata Nilai Akhir</strong></td><td><strong>${avg}</strong></td></tr>
            </table>
            <p><strong>Catatan Hasil Observasi:</strong><br/>${ExportUtils.escape(r.catatanObservasi)}</p>
            <p><strong>Rekomendasi Perbaikan:</strong><br/>${ExportUtils.escape(r.rekomendasi)}</p>
            <p><strong>Rencana Tindak Lanjut:</strong><br/>${ExportUtils.escape(r.tindakLanjut)}</p>
          `;
          ExportUtils.printDocument('Laporan Supervisi Individual', html, true, true, r.tanggal);
        },

        buildAllHtml() {
          const records = DB.data.supervisi.records;
          return `
            <h2 style="text-align:center;text-transform:uppercase;">REKAPITULASI HASIL SUPERVISI AKADEMIK & MANAJERIAL</h2>
            <p style="text-align:center;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <table>
              <tr><th>No</th><th>Tanggal</th><th>Nama Guru</th><th>Mapel/Kelas</th><th>Perencanaan</th><th>Pelaksanaan</th><th>Asesmen</th><th>Rata-Rata</th><th>Tindak Lanjut</th></tr>
              ${records.map((r, i) => {
                const avg = Math.round((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3);
                return `<tr><td>${i + 1}</td><td>${ExportUtils.escape(r.tanggal)}</td><td>${ExportUtils.escape(r.namaGuru)}</td><td>${ExportUtils.escape(r.mapel)}</td><td>${r.skorPerencanaan}</td><td>${r.skorPelaksanaan}</td><td>${r.skorAsesmen}</td><td>${avg}</td><td>${ExportUtils.escape(r.tindakLanjut)}</td></tr>`;
              }).join('')}
            </table>
          `;
        },

        printAll() {
          ExportUtils.printDocument('Rekap Hasil Supervisi', this.buildAllHtml(), true, true);
        },

        exportAllWord() {
          ExportUtils.downloadWord('Rekap_Hasil_Supervisi_Sekolah', this.buildAllHtml(), true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 6: MANAJEMEN GURU DAN TENAGA KEPENDIDIKAN (IMPOR/EKSPOR EXCEL)
      ------------------------------------------------------------------- */
      GTK: {
        searchQuery: '',

        render(container) {
          const q = this.searchQuery.toLowerCase();
          const list = DB.data.gtk.filter(g =>
            g.nama.toLowerCase().includes(q) || g.nip.toLowerCase().includes(q) || g.jabatan.toLowerCase().includes(q)
          );

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Manajemen Guru & Tenaga Kependidikan',
                'Database identitas GTK, pendidikan, tugas tambahan, jam mengajar, penilaian kinerja & pelatihan.',
                'fa-solid fa-chalkboard-user',
                `
                  <button onclick="Modules.GTK.openAddModal()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-plus text-gold-400"></i> Tambah GTK
                  </button>
                  <label class="cursor-pointer px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-import text-emerald-700"></i> Impor Excel
                    <input type="file" accept=".xlsx,.xls" onchange="Modules.GTK.importExcel(event)" class="hidden" />
                  </label>
                  <button onclick="Modules.GTK.exportExcel()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-excel"></i> Ekspor Excel
                  </button>
                `
              )}

              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="p-4 border-b border-slate-100 flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                  <div class="font-extrabold text-sm text-navy-900">Daftar Guru & Tenaga Kependidikan (${list.length} Orang)</div>
                  <input type="text" value="${ExportUtils.escape(this.searchQuery)}" oninput="Modules.GTK.onSearch(this.value)" placeholder="Cari nama, NIP, atau jabatan..." class="w-full sm:w-72 border border-slate-300 rounded-xl px-3.5 py-2 text-xs" />
                </div>
                <div class="overflow-x-auto">
                  <table class="w-full text-left text-xs">
                    <thead class="bg-navy-900 text-white">
                      <tr>
                        <th class="p-3.5">Nama & NIP</th>
                        <th class="p-3.5">Jabatan & Pendidikan</th>
                        <th class="p-3.5">Tugas Mengajar & Tambahan</th>
                        <th class="p-3.5 text-center">JP</th>
                        <th class="p-3.5 text-center">Kehadiran</th>
                        <th class="p-3.5 text-center">Kinerja</th>
                        <th class="p-3.5">Pelatihan & Pembinaan</th>
                        <th class="p-3.5 text-center">Aksi</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${list.map(g => `
                        <tr class="hover:bg-slate-50">
                          <td class="p-3.5">
                            <div class="font-bold text-navy-900">${ExportUtils.escape(g.nama)}</div>
                            <div class="text-slate-500">NIP. ${ExportUtils.escape(g.nip)}</div>
                          </td>
                          <td class="p-3.5">
                            <div class="font-semibold">${ExportUtils.escape(g.jabatan)}</div>
                            <div class="text-slate-500">${ExportUtils.escape(g.pendidikan)}</div>
                          </td>
                          <td class="p-3.5">
                            <div><strong>Ampuan:</strong> ${ExportUtils.escape(g.mapel)}</div>
                            <div class="text-slate-500"><strong>Tugas:</strong> ${ExportUtils.escape(g.tugasTambahan)}</div>
                          </td>
                          <td class="p-3.5 text-center font-bold">${g.jamMengajar}</td>
                          <td class="p-3.5 text-center font-bold text-emerald-700">${g.kehadiran}%</td>
                          <td class="p-3.5 text-center"><span class="px-2 py-0.5 rounded bg-gold-100 text-gold-900 font-extrabold">${g.nilaiKinerja}</span></td>
                          <td class="p-3.5">
                            <div><strong>Diklat:</strong> ${ExportUtils.escape(g.pelatihan)}</div>
                            <div class="text-slate-500 mt-0.5">${ExportUtils.escape(g.catatanPembinaan)}</div>
                          </td>
                          <td class="p-3.5 text-center">
                            <button onclick="Modules.GTK.deleteRecord('${g.id}')" class="p-2 rounded-lg bg-rose-50 text-rose-700 hover:bg-rose-100"><i class="fa-solid fa-trash"></i></button>
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

        onSearch(val) {
          this.searchQuery = val;
          this.render(document.getElementById('app-content'));
        },

        openAddModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Tambah Data Guru / Tenaga Kependidikan',
            bodyHtml: `
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 text-xs">
                <div><label class="font-bold block mb-1">Nama Lengkap & Gelar</label><input type="text" id="gtk-nama" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">NIP / NUPTK</label><input type="text" id="gtk-nip" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Jabatan</label><input type="text" id="gtk-jabatan" value="Guru Kelas" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Pendidikan Terakhir</label><input type="text" id="gtk-pend" value="S1 PGSD" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Kelas / Mata Pelajaran</label><input type="text" id="gtk-mapel" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Tugas Tambahan</label><input type="text" id="gtk-tugas" value="-" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Jam Mengajar (JP)</label><input type="number" id="gtk-jp" value="24" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Nilai Kinerja (0-100)</label><input type="number" id="gtk-kinerja" value="88" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div class="sm:col-span-2"><label class="font-bold block mb-1">Pelatihan & Workshop Diikuti</label><input type="text" id="gtk-pelatihan" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div class="sm:col-span-2"><label class="font-bold block mb-1">Catatan Pembinaan</label><input type="text" id="gtk-catatan" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.GTK.saveRecord()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan GTK</button>
            `
          });
        },

        saveRecord() {
          const nama = document.getElementById('gtk-nama').value.trim();
          if (!nama) {
            UI.toast('Nama GTK wajib diisi.', 'warning');
            return;
          }
          DB.data.gtk.unshift({
            id: DB.uid('gtk'),
            nama,
            nip: document.getElementById('gtk-nip').value.trim() || '-',
            jabatan: document.getElementById('gtk-jabatan').value.trim(),
            pendidikan: document.getElementById('gtk-pend').value.trim(),
            mapel: document.getElementById('gtk-mapel').value.trim() || '-',
            tugasTambahan: document.getElementById('gtk-tugas').value.trim() || '-',
            jamMengajar: Number(document.getElementById('gtk-jp').value) || 0,
            kehadiran: 98,
            nilaiKinerja: Number(document.getElementById('gtk-kinerja').value) || 85,
            pelatihan: document.getElementById('gtk-pelatihan').value.trim() || '-',
            catatanPembinaan: document.getElementById('gtk-catatan').value.trim() || '-'
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        deleteRecord(id) {
          if (!UI.canEdit()) return;
          DB.data.gtk = DB.data.gtk.filter(x => x.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        },

        exportExcel() {
          const rows = DB.data.gtk.map((g, idx) => ({
            No: idx + 1,
            Nama: g.nama,
            NIP: g.nip,
            Jabatan: g.jabatan,
            Pendidikan: g.pendidikan,
            'Mapel/Kelas': g.mapel,
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
          if (!file || typeof XLSX === 'undefined') return;
          const reader = new FileReader();
          reader.onload = (e) => {
            try {
              const wb = XLSX.read(e.target.result, { type: 'binary' });
              const sheet = wb.Sheets[wb.SheetNames[0]];
              const rows = XLSX.utils.sheet_to_json(sheet);
              if (!rows.length) throw new Error('File Excel kosong.');
              rows.forEach(r => {
                DB.data.gtk.push({
                  id: DB.uid('gtk'),
                  nama: r.Nama || r.nama || 'Tanpa Nama',
                  nip: String(r.NIP || r.nip || '-'),
                  jabatan: r.Jabatan || r.jabatan || 'Guru',
                  pendidikan: r.Pendidikan || r.pendidikan || 'S1',
                  mapel: r['Mapel/Kelas'] || r.Mapel || '-',
                  tugasTambahan: r['Tugas Tambahan'] || '-',
                  jamMengajar: Number(r['Jam Mengajar'] || 24),
                  kehadiran: Number(r['Kehadiran (%)'] || 98),
                  nilaiKinerja: Number(r['Nilai Kinerja'] || 85),
                  pelatihan: r.Pelatihan || '-',
                  catatanPembinaan: r['Catatan Pembinaan'] || '-'
                });
              });
              DB.save(true);
              this.render(document.getElementById('app-content'));
              UI.toast(`Berhasil mengimpor ${rows.length} data GTK dari Excel!`, 'success');
            } catch (err) {
              UI.toast('Gagal mengimpor Excel: ' + err.message, 'error');
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
          const totL = rombel.reduce((a, b) => a + Number(b.lakiLaki || 0), 0);
          const totP = rombel.reduce((a, b) => a + Number(b.perempuan || 0), 0);

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Manajemen Peserta Didik',
                'Rekapitulasi siswa per kelas, rombongan belajar, kehadiran, prestasi, karakter & mutasi.',
                'fa-solid fa-users',
                `
                  <button onclick="Modules.Siswa.openAddPrestasiModal()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-trophy text-gold-400"></i> Catat Prestasi / Karakter
                  </button>
                  <button onclick="Modules.Siswa.exportExcel()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-excel"></i> Ekspor Excel
                  </button>
                `
              )}

              <!-- Statistik Siswa -->
              <div class="grid grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-4 rounded-2xl border border-slate-200">
                  <div class="text-xs text-slate-500">Total Peserta Didik</div>
                  <div class="text-2xl font-extrabold text-navy-900 mt-1">${totL + totP} Siswa</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200">
                  <div class="text-xs text-slate-500">Laki-Laki / Perempuan</div>
                  <div class="text-lg font-extrabold text-navy-900 mt-1">${totL} L • ${totP} P</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200">
                  <div class="text-xs text-slate-500">Siswa Baru Tahun Ini</div>
                  <div class="text-2xl font-extrabold text-emerald-700 mt-1">${mutasi.siswaBaru} Siswa</div>
                </div>
                <div class="bg-white p-4 rounded-2xl border border-slate-200">
                  <div class="text-xs text-slate-500">Lulusan Tahun Lalu</div>
                  <div class="text-2xl font-extrabold text-gold-700 mt-1">${mutasi.lulusanTahunLalu} Siswa</div>
                </div>
              </div>

              <!-- Tabel Rombel -->
              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="p-4 border-b border-slate-100 flex items-center justify-between">
                  <h3 class="font-extrabold text-sm text-navy-900">Rekapitulasi Siswa per Tingkat Kelas & Rombongan Belajar</h3>
                  <button onclick="Modules.Siswa.openAddRombelModal()" class="px-3 py-1.5 bg-navy-50 hover:bg-navy-100 text-navy-900 rounded-xl text-xs font-bold">+ Tambah Kelas</button>
                </div>
                <div class="overflow-x-auto">
                  <table class="w-full text-left text-xs">
                    <thead class="bg-navy-900 text-white">
                      <tr><th class="p-3.5">Tingkat</th><th class="p-3.5">Rombongan Belajar</th><th class="p-3.5">Wali Kelas</th><th class="p-3.5 text-center">Laki-Laki</th><th class="p-3.5 text-center">Perempuan</th><th class="p-3.5 text-center">Jumlah</th><th class="p-3.5 text-center">Kehadiran</th></tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${rombel.map(r => `
                        <tr>
                          <td class="p-3.5 font-bold text-navy-900">${ExportUtils.escape(r.tingkat)}</td>
                          <td class="p-3.5">${ExportUtils.escape(r.namaRombel)}</td>
                          <td class="p-3.5">${ExportUtils.escape(r.waliKelas)}</td>
                          <td class="p-3.5 text-center">${r.lakiLaki}</td>
                          <td class="p-3.5 text-center">${r.perempuan}</td>
                          <td class="p-3.5 text-center font-extrabold">${Number(r.lakiLaki) + Number(r.perempuan)}</td>
                          <td class="p-3.5 text-center text-emerald-700 font-bold">${r.kehadiran}%</td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>

              <!-- Prestasi & Pembinaan Karakter -->
              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="p-4 border-b border-slate-100">
                  <h3 class="font-extrabold text-sm text-navy-900">Prestasi Akademik, Non-Akademik, Ekstrakurikuler & Pembinaan Karakter</h3>
                </div>
                <div class="overflow-x-auto">
                  <table class="w-full text-left text-xs">
                    <thead class="bg-navy-900 text-white">
                      <tr><th class="p-3.5">Nama Siswa / Tim</th><th class="p-3.5">Kelas</th><th class="p-3.5">Kategori</th><th class="p-3.5">Capaian / Kegiatan</th><th class="p-3.5">Tingkat</th><th class="p-3.5">Pembina</th></tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${prestasi.map(p => `
                        <tr>
                          <td class="p-3.5 font-bold text-navy-900">${ExportUtils.escape(p.namaSiswa)}</td>
                          <td class="p-3.5">${ExportUtils.escape(p.kelas)}</td>
                          <td class="p-3.5"><span class="px-2 py-0.5 rounded bg-gold-100 text-gold-900 font-bold">${ExportUtils.escape(p.jenis)}</span></td>
                          <td class="p-3.5">${ExportUtils.escape(p.judul)}</td>
                          <td class="p-3.5">${ExportUtils.escape(p.tingkat)}</td>
                          <td class="p-3.5">${ExportUtils.escape(p.pembina)}</td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        openAddRombelModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Tambah Data Rombongan Belajar',
            bodyHtml: `
              <div class="grid grid-cols-2 gap-3 text-xs">
                <div><label class="font-bold block mb-1">Tingkat Kelas</label><input type="text" id="rmb-t" placeholder="Contoh: Kelas VII" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Nama Rombel</label><input type="text" id="rmb-n" placeholder="VII-A, VII-B" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Jumlah Laki-Laki</label><input type="number" id="rmb-l" value="25" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Jumlah Perempuan</label><input type="number" id="rmb-p" value="27" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Siswa.saveRombel()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Rombel</button>
            `
          });
        },

        saveRombel() {
          DB.data.siswa.rombel.push({
            id: DB.uid('rmb'),
            tingkat: document.getElementById('rmb-t').value.trim() || 'Kelas Baru',
            namaRombel: document.getElementById('rmb-n').value.trim() || '-',
            waliKelas: 'Wali Kelas',
            lakiLaki: Number(document.getElementById('rmb-l').value) || 0,
            perempuan: Number(document.getElementById('rmb-p').value) || 0,
            kehadiran: 97.0
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        openAddPrestasiModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Catat Prestasi & Perkembangan Peserta Didik',
            bodyHtml: `
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 text-xs">
                <div><label class="font-bold block mb-1">Nama Siswa / Tim</label><input type="text" id="prs-nama" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Kelas</label><input type="text" id="prs-kls" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Kategori</label><select id="prs-jenis" class="w-full border border-slate-300 rounded-xl px-3 py-2 bg-white"><option>Prestasi Akademik</option><option>Prestasi Non-Akademik</option><option>Pembinaan Karakter</option><option>Ekstrakurikuler</option></select></div>
                <div><label class="font-bold block mb-1">Tingkat</label><input type="text" id="prs-tingkat" value="Kota/Kabupaten" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div class="sm:col-span-2"><label class="font-bold block mb-1">Judul Prestasi / Catatan</label><input type="text" id="prs-judul" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div class="sm:col-span-2"><label class="font-bold block mb-1">Guru Pembina</label><input type="text" id="prs-pembina" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Siswa.savePrestasi()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Catatan</button>
            `
          });
        },

        savePrestasi() {
          const namaSiswa = document.getElementById('prs-nama').value.trim();
          if (!namaSiswa) return;
          DB.data.siswa.prestasiDanCatatan.unshift({
            id: DB.uid('prs'),
            namaSiswa,
            kelas: document.getElementById('prs-kls').value.trim(),
            jenis: document.getElementById('prs-jenis').value,
            judul: document.getElementById('prs-judul').value.trim(),
            tingkat: document.getElementById('prs-tingkat').value.trim(),
            pembina: document.getElementById('prs-pembina').value.trim(),
            tanggal: new Date().toISOString().split('T')[0]
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        exportExcel() {
          const rows = DB.data.siswa.rombel.map((r, i) => ({
            No: i + 1,
            Tingkat: r.tingkat,
            Rombel: r.namaRombel,
            'Wali Kelas': r.waliKelas,
            'Laki-Laki': r.lakiLaki,
            Perempuan: r.perempuan,
            Total: Number(r.lakiLaki) + Number(r.perempuan),
            'Kehadiran (%)': r.kehadiran
          }));
          ExportUtils.exportExcel('Data_Peserta_Didik_Rombel.xlsx', 'Peserta_Didik', rows);
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
                'Buku inventaris barang, ruang kelas, kondisi fasilitas, pemeliharaan & rekap aset sekolah.',
                'fa-solid fa-building-columns',
                `
                  <button onclick="Modules.Sarpras.openAddModal()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-plus text-gold-400"></i> Tambah Aset / Sarpras
                  </button>
                  <button onclick="Modules.UnduhFile.downloadSarprasWord()" class="px-3.5 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-word"></i> Unduh Inventaris Word
                  </button>
                `
              )}

              <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex flex-col sm:flex-row sm:items-center justify-between gap-4">
                <div>
                  <div class="text-xs font-bold text-slate-500 uppercase">Total Nilai Aset Tercatat</div>
                  <div class="text-2xl font-extrabold text-navy-900 mt-1">${ExportUtils.formatRupiah(totalAset)}</div>
                </div>
                <div class="text-xs text-slate-600">Total ${list.length} jenis kelompok sarana & prasarana terinventarisasi.</div>
              </div>

              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="overflow-x-auto">
                  <table class="w-full text-left text-xs">
                    <thead class="bg-navy-900 text-white">
                      <tr><th class="p-3.5">Kode</th><th class="p-3.5">Nama Barang / Ruang</th><th class="p-3.5">Kategori</th><th class="p-3.5 text-center">Jumlah</th><th class="p-3.5">Kondisi</th><th class="p-3.5">Pemeliharaan</th><th class="p-3.5">Nilai Aset</th><th class="p-3.5">Aksi</th></tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                      ${list.map(s => `
                        <tr>
                          <td class="p-3.5 font-mono font-bold">${ExportUtils.escape(s.kodeBarang)}</td>
                          <td class="p-3.5 font-bold text-navy-900">${ExportUtils.escape(s.namaBarang)}<div class="font-normal text-slate-500">${ExportUtils.escape(s.keterangan)}</div></td>
                          <td class="p-3.5">${ExportUtils.escape(s.kategori)}</td>
                          <td class="p-3.5 text-center font-bold">${s.jumlah}</td>
                          <td class="p-3.5"><span class="px-2 py-0.5 rounded font-bold ${s.kondisi === 'Baik' ? 'bg-emerald-100 text-emerald-800' : 'bg-amber-100 text-amber-800'}">${ExportUtils.escape(s.kondisi)}</span></td>
                          <td class="p-3.5">${ExportUtils.escape(s.jadwalPemeliharaan)}</td>
                          <td class="p-3.5 font-semibold">${ExportUtils.formatRupiah(s.nilaiAset)}</td>
                          <td class="p-3.5"><button onclick="Modules.Sarpras.deleteRecord('${s.id}')" class="text-rose-600 font-bold">Hapus</button></td>
                        </tr>
                      `).join('')}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          `;
        },

        openAddModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Tambah Inventaris Sarana / Prasarana',
            bodyHtml: `
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 text-xs">
                <div><label class="font-bold block mb-1">Kode Barang</label><input type="text" id="srp-kode" value="INV-BRG-04" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Nama Barang / Ruang</label><input type="text" id="srp-nama" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Kategori</label><input type="text" id="srp-kat" value="Fasilitas Pembelajaran" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Jumlah Unit</label><input type="number" id="srp-jml" value="1" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Kondisi</label><select id="srp-kond" class="w-full border border-slate-300 rounded-xl px-3 py-2 bg-white"><option>Baik</option><option>Rusak Ringan</option><option>Rusak Berat</option></select></div>
                <div><label class="font-bold block mb-1">Jadwal Pemeliharaan</label><input type="text" id="srp-jadwal" value="Semester Genap 2027" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Perkiraan Nilai Aset (Rp)</label><input type="number" id="srp-nilai" value="5000000" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Keterangan / Usulan</label><input type="text" id="srp-ket" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Sarpras.saveRecord()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Inventaris</button>
            `
          });
        },

        saveRecord() {
          const namaBarang = document.getElementById('srp-nama').value.trim();
          if (!namaBarang) return;
          DB.data.sarpras.unshift({
            id: DB.uid('srp'),
            kodeBarang: document.getElementById('srp-kode').value.trim(),
            namaBarang,
            kategori: document.getElementById('srp-kat').value.trim(),
            jumlah: Number(document.getElementById('srp-jml').value) || 1,
            kondisi: document.getElementById('srp-kond').value,
            jadwalPemeliharaan: document.getElementById('srp-jadwal').value.trim(),
            nilaiAset: Number(document.getElementById('srp-nilai').value) || 0,
            keterangan: document.getElementById('srp-ket').value.trim()
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        deleteRecord(id) {
          if (!UI.canEdit()) return;
          DB.data.sarpras = DB.data.sarpras.filter(x => x.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        }
      },

      /* -------------------------------------------------------------------
         MODUL 9: MANAJEMEN KURIKULUM DAN PEMBELAJARAN
      ------------------------------------------------------------------- */
      Kurikulum: {
        render(container) {
          const list = DB.data.kurikulum;
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Manajemen Kurikulum & Pembelajaran',
                'Kalender pendidikan, program kurikulum, pembagian tugas mengajar, dan peningkatan mutu pembelajaran.',
                'fa-solid fa-book-open-reader',
                `
                  <button onclick="Modules.Kurikulum.openAddModal()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-plus text-gold-400"></i> Tambah Program Kurikulum
                  </button>
                `
              )}

              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                ${list.map(k => `
                  <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <div>
                      <div class="flex items-center justify-between mb-2">
                        <span class="px-2.5 py-0.5 rounded-full bg-navy-50 text-navy-900 text-[11px] font-bold">${ExportUtils.escape(k.aspek)}</span>
                        <span class="px-2.5 py-0.5 rounded-full bg-emerald-100 text-emerald-800 text-[11px] font-bold">${ExportUtils.escape(k.status)}</span>
                      </div>
                      <h3 class="text-sm font-extrabold text-navy-900">${ExportUtils.escape(k.namaProgram)}</h3>
                      <p class="text-xs text-slate-600 mt-1.5">${ExportUtils.escape(k.catatan)}</p>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-100 flex items-center justify-between text-[11px] text-slate-500">
                      <span>PJ: <strong>${ExportUtils.escape(k.penanggungJawab)}</strong> • ${ExportUtils.escape(k.periode)}</span>
                      <button onclick="Modules.Kurikulum.deleteRecord('${k.id}')" class="text-rose-600 font-bold">Hapus</button>
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>
          `;
        },

        openAddModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Tambah Program Kurikulum & Pembelajaran',
            bodyHtml: `
              <div class="space-y-3 text-xs">
                <div><label class="font-bold block mb-1">Aspek Kurikulum</label><select id="kur-aspek" class="w-full border border-slate-300 rounded-xl px-3 py-2 bg-white"><option>Kalender Pendidikan</option><option>Program Kurikulum</option><option>Jadwal Pelajaran</option><option>Pembagian Tugas Mengajar</option><option>Monitoring Pembelajaran</option><option>Peningkatan Mutu Pembelajaran</option></select></div>
                <div><label class="font-bold block mb-1">Nama Program / Kegiatan</label><input type="text" id="kur-nama" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Penanggung Jawab & Periode</label><input type="text" id="kur-pj" value="Kepala Sekolah & Tim Kurikulum" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Catatan Pelaksanaan</label><textarea id="kur-cat" rows="2" class="w-full border border-slate-300 rounded-xl px-3 py-2"></textarea></div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Kurikulum.saveRecord()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan</button>
            `
          });
        },

        saveRecord() {
          const namaProgram = document.getElementById('kur-nama').value.trim();
          if (!namaProgram) return;
          DB.data.kurikulum.unshift({
            id: DB.uid('kur'),
            aspek: document.getElementById('kur-aspek').value,
            namaProgram,
            penanggungJawab: document.getElementById('kur-pj').value.trim(),
            periode: DB.data.settings.academicYear,
            status: 'Berjalan',
            catatan: document.getElementById('kur-cat').value.trim()
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        deleteRecord(id) {
          if (!UI.canEdit()) return;
          DB.data.kurikulum = DB.data.kurikulum.filter(x => x.id !== id);
          DB.save(true);
          this.render(document.getElementById('app-content'));
        }
      },

      /* -------------------------------------------------------------------
         MODUL 10: KEPEMIMPINAN DAN PENGEMBANGAN SEKOLAH
      ------------------------------------------------------------------- */
      Kepemimpinan: {
        render(container) {
          const kp = DB.data.kepemimpinan;
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Kepemimpinan & Pengembangan Sekolah',
                'Visi, misi, tujuan sekolah, budaya positif, penguatan karakter, kemitraan komite & inovasi.',
                'fa-solid fa-award',
                `
                  <button onclick="Modules.Kepemimpinan.saveVisiMisi()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-floppy-disk text-gold-400"></i> Simpan Visi & Misi
                  </button>
                `
              )}

              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <h3 class="text-base font-extrabold text-navy-900">Visi, Misi & Tujuan Satuan Pendidikan</h3>
                <div class="space-y-3 text-xs">
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Visi Sekolah</label>
                    <textarea id="kp-visi" rows="2" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5 font-semibold text-navy-900">${ExportUtils.escape(kp.visi)}</textarea>
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Misi Sekolah</label>
                    <textarea id="kp-misi" rows="4" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5">${ExportUtils.escape(kp.misi)}</textarea>
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">Tujuan Sekolah</label>
                    <textarea id="kp-tujuan" rows="2" class="w-full border border-slate-300 rounded-xl px-3.5 py-2.5">${ExportUtils.escape(kp.tujuan)}</textarea>
                  </div>
                </div>
              </div>

              <div class="bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-4">
                <div class="flex items-center justify-between">
                  <h3 class="text-base font-extrabold text-navy-900">Program Budaya Positif, Kemitraan Komite, Inovasi & Sekolah Sehat</h3>
                  <button onclick="Modules.Kepemimpinan.addProgram()" class="px-3 py-1.5 bg-gold-500 text-navy-950 rounded-xl text-xs font-extrabold">+ Tambah Program</button>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                  ${kp.programStrategis.map(item => `
                    <div class="p-4 rounded-2xl bg-slate-50 border border-slate-200 space-y-2">
                      <span class="px-2.5 py-0.5 rounded-full bg-navy-900 text-gold-400 text-[10px] font-bold">${ExportUtils.escape(item.kategori)}</span>
                      <h4 class="text-sm font-extrabold text-navy-900">${ExportUtils.escape(item.namaProgram)}</h4>
                      <p class="text-xs text-slate-600"><strong>Mitra:</strong> ${ExportUtils.escape(item.mitra)}</p>
                      <p class="text-xs text-emerald-700"><strong>Dampak:</strong> ${ExportUtils.escape(item.dampak)}</p>
                    </div>
                  `).join('')}
                </div>
              </div>
            </div>
          `;
        },

        saveVisiMisi() {
          if (!UI.canEdit()) return;
          DB.data.kepemimpinan.visi = document.getElementById('kp-visi').value.trim();
          DB.data.kepemimpinan.misi = document.getElementById('kp-misi').value.trim();
          DB.data.kepemimpinan.tujuan = document.getElementById('kp-tujuan').value.trim();
          DB.save(true);
        },

        addProgram() {
          if (!UI.canEdit()) return;
          DB.data.kepemimpinan.programStrategis.push({
            id: DB.uid('kp'),
            kategori: 'Inovasi & Sekolah Aman',
            namaProgram: 'Pojok Konseling Sebaya & Gerakan Sekolah Hijau',
            mitra: 'Komite Sekolah & Orang Tua',
            dampak: 'Meningkatnya kepedulian lingkungan dan kenyamanan belajar siswa',
            status: 'Aktif'
          });
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
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Monitoring & Evaluasi Program Sekolah',
                'Pemantauan realisasi program bulanan, semester, tahunan, identifikasi kendala & analisis AI.',
                'fa-solid fa-magnifying-glass-chart',
                `
                  <button onclick="Modules.Monev.analyzeAi()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-wand-magic-sparkles text-gold-400"></i> Analisis Capaian AI
                  </button>
                  <button onclick="Modules.Monev.openAddModal()" class="px-3.5 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold">
                    + Input Evaluasi
                  </button>
                `
              )}

              ${this.aiSummary ? `
                <div class="bg-navy-900 text-white rounded-2xl p-5 border border-gold-500/40 shadow-md space-y-2">
                  <div class="text-xs font-extrabold text-gold-400 uppercase flex items-center gap-2">
                    <i class="fa-solid fa-robot"></i> Hasil Analisis Capaian & Saran Tindak Lanjut AI
                  </div>
                  <div class="text-xs sm:text-sm text-slate-200 whitespace-pre-line leading-relaxed">${ExportUtils.escape(this.aiSummary)}</div>
                </div>
              ` : ''}

              <div class="space-y-4">
                ${list.map(m => `
                  <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm space-y-3">
                    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2">
                      <div>
                        <span class="px-2.5 py-0.5 rounded-full bg-navy-50 text-navy-900 text-[11px] font-bold">${ExportUtils.escape(m.periode)}</span>
                        <h3 class="text-base font-extrabold text-navy-900 mt-1">${ExportUtils.escape(m.namaProgram)}</h3>
                      </div>
                      <div class="text-right">
                        <span class="text-xl font-extrabold text-gold-600">${m.capaianPersen}%</span>
                        <span class="text-xs text-slate-400"> / Target ${m.targetPersen}%</span>
                      </div>
                    </div>
                    <div class="w-full h-2.5 bg-slate-100 rounded-full overflow-hidden">
                      <div class="h-full bg-gradient-to-r from-navy-900 to-gold-500 rounded-full" style="width:${m.capaianPersen}%"></div>
                    </div>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 text-xs pt-1">
                      <div class="p-3 rounded-xl bg-amber-50/70 border border-amber-200">
                        <strong>Kendala Pelaksanaan:</strong> ${ExportUtils.escape(m.kendala)}
                      </div>
                      <div class="p-3 rounded-xl bg-emerald-50/70 border border-emerald-200">
                        <strong>Rencana Tindak Lanjut:</strong> ${ExportUtils.escape(m.tindakLanjut)}
                      </div>
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>
          `;
        },

        analyzeAi() {
          const list = DB.data.monev;
          const underTarget = list.filter(x => Number(x.capaianPersen) < Number(x.targetPersen));
          const lines = [
            `RINGKASAN ANALISIS MONITORING & EVALUASI (${DB.data.settings.schoolName}):`,
            `1. Dari ${list.length} program yang dipantau, terdapat ${underTarget.length} program yang belum mencapai target 100%.`,
            ...underTarget.map(u => `• Program "${u.namaProgram}" (${u.capaianPersen}%): Kendala utama pada "${u.kendala}". Disarankan segera melaksanakan "${u.tindakLanjut}" melalui rapat koordinasi mingguan.`),
            `2. Rekomendasi Strategis Kepala Sekolah: Tetapkan batas waktu penyelesaian (deadline) yang terukur untuk setiap penanggung jawab kegiatan sebelum akhir semester.`
          ];
          this.aiSummary = lines.join('\n');
          UI.toast('Analisis capaian dan tindak lanjut AI berhasil dibuat!', 'success');
          this.render(document.getElementById('app-content'));
        },

        openAddModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Tambah Data Monitoring & Evaluasi',
            bodyHtml: `
              <div class="space-y-3 text-xs">
                <div><label class="font-bold block mb-1">Periode Evaluasi</label><select id="mnv-per" class="w-full border border-slate-300 rounded-xl px-3 py-2 bg-white"><option>Evaluasi Bulanan</option><option>Evaluasi Semester</option><option>Evaluasi Tahunan</option></select></div>
                <div><label class="font-bold block mb-1">Nama Program</label><input type="text" id="mnv-prog" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Persentase Capaian (%)</label><input type="number" id="mnv-cap" value="85" min="0" max="100" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Kendala Pelaksanaan</label><input type="text" id="mnv-ken" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div><label class="font-bold block mb-1">Tindak Lanjut</label><input type="text" id="mnv-tl" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.Monev.saveRecord()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Evaluasi</button>
            `
          });
        },

        saveRecord() {
          const namaProgram = document.getElementById('mnv-prog').value.trim();
          if (!namaProgram) return;
          DB.data.monev.unshift({
            id: DB.uid('mnv'),
            periode: document.getElementById('mnv-per').value,
            namaProgram,
            targetPersen: 100,
            capaianPersen: Number(document.getElementById('mnv-cap').value) || 0,
            kendala: document.getElementById('mnv-ken').value.trim() || '-',
            tindakLanjut: document.getElementById('mnv-tl').value.trim() || '-'
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        }
      },

      /* -------------------------------------------------------------------
         MODUL 12: GENERATOR DOKUMEN OTOMATIS BERBANTUAN AI (18 JENIS)
      ------------------------------------------------------------------- */
      GeneratorAI: {
        docTypes: [
          'Surat Keputusan (SK)', 'Surat Tugas', 'Surat Undangan', 'Surat Edaran', 'Surat Keterangan',
          'Berita Acara', 'Daftar Hadir', 'Notulen Rapat', 'Proposal Kegiatan', 'Laporan Kegiatan',
          'Laporan Supervisi', 'Laporan Pembinaan Guru', 'Laporan Monitoring', 'Laporan Evaluasi',
          'Program Kerja Kepala Sekolah', 'Program Tahunan', 'Program Semester', 'Laporan Pertanggungjawaban (LPJ)'
        ],
        generatedText: '',
        selectedType: 'Surat Keputusan (SK)',
        nomorSurat: '',
        tanggalSurat: '',
        tujuan: '',
        perihal: '',
        rincian: '',

        render(container) {
          const s = DB.data.settings;
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Generator Dokumen Otomatis Berbantuan AI',
                'Menyusun 18 jenis dokumen resmi sekolah secara otomatis lengkap dengan Kop Surat dan tanda tangan.',
                'fa-solid fa-wand-magic-sparkles',
                `
                  <button onclick="Modules.GeneratorAI.saveToArchive()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-box-archive"></i> Simpan ke Arsip
                  </button>
                  <button onclick="Modules.GeneratorAI.downloadWord()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-word text-gold-400"></i> Unduh Word (.doc)
                  </button>
                  <button onclick="Modules.GeneratorAI.printDoc()" class="px-3.5 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold flex items-center gap-1.5">
                    <i class="fa-solid fa-print"></i> Cetak / PDF
                  </button>
                `
              )}

              ${UI.guideBanner(
                'Cara Membuat Dokumen Administrasi Otomatis',
                'Pilih jenis dokumen, isi Nomor Surat, Tanggal, dan Perihal (apabila dikosongkan, sistem akan menyediakan kolom isian resmi tanpa mengarang nomor/tanggal secara sembarangan), lalu klik "Susun Dokumen dengan AI".'
              )}

              <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                <!-- Formulir Input -->
                <div class="lg:col-span-5 bg-white rounded-2xl p-6 border border-slate-200 shadow-sm space-y-3.5 text-xs">
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">1. Pilih Jenis Dokumen (18 Format Resmi)</label>
                    <select id="gen-type" onchange="Modules.GeneratorAI.selectedType=this.value" class="w-full border border-slate-300 rounded-xl px-3 py-2.5 bg-white font-bold text-navy-900">
                      ${this.docTypes.map(t => `<option value="${t}" ${this.selectedType === t ? 'selected' : ''}>${t}</option>`).join('')}
                    </select>
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">2. Nomor Surat / Dokumen (Kosongkan jika belum ada)</label>
                    <input type="text" id="gen-nomor" value="${ExportUtils.escape(this.nomorSurat)}" placeholder="Contoh: 421.2/102/SDN1/XI/2026" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">3. Tanggal Dokumen</label>
                    <input type="text" id="gen-tgl" value="${ExportUtils.escape(this.tanggalSurat)}" placeholder="Contoh: 23 November 2026" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">4. Tujuan / Penerima / Sasaran</label>
                    <input type="text" id="gen-tujuan" value="${ExportUtils.escape(this.tujuan)}" placeholder="Contoh: Seluruh Dewan Guru & Staf TU" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">5. Perihal / Judul Kegiatan</label>
                    <input type="text" id="gen-perihal" value="${ExportUtils.escape(this.perihal)}" placeholder="Contoh: Pembentukan Panitia Asesmen Sumatif Akhir Semester" class="w-full border border-slate-300 rounded-xl px-3 py-2" />
                  </div>
                  <div>
                    <label class="font-bold text-slate-700 block mb-1">6. Rincian Isi / Waktu / Tempat / Keterangan Tambahan</label>
                    <textarea id="gen-rincian" rows="4" placeholder="Tuliskan poin penting, waktu pelaksanaan, tempat, atau nama pejabat/guru terkait..." class="w-full border border-slate-300 rounded-xl px-3 py-2">${ExportUtils.escape(this.rincian)}</textarea>
                  </div>
                  <button onclick="Modules.GeneratorAI.generateDocument()" id="btn-gen-doc" class="w-full py-3 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-extrabold shadow-md flex items-center justify-center gap-2 transition">
                    <i class="fa-solid fa-wand-magic-sparkles text-gold-400"></i> Susun Dokumen dengan AI
                  </button>
                </div>

                <!-- Pratinjau & Editor Hasil Dokumen -->
                <div class="lg:col-span-7 bg-white rounded-2xl p-6 border border-slate-200 shadow-sm flex flex-col">
                  <div class="flex items-center justify-between pb-3 mb-4 border-b border-slate-100">
                    <span class="text-xs font-extrabold text-navy-900 uppercase">Pratinjau & Editor Naskah Resmi (Dapat Diedit Langsung)</span>
                    <button onclick="ExportUtils.copyText(document.getElementById('gen-editor').value)" class="text-xs font-bold text-navy-800 hover:underline">
                      <i class="fa-solid fa-copy mr-1"></i> Salin Teks
                    </button>
                  </div>

                  <!-- Kop Surat Mini Pratinjau -->
                  <div class="p-4 rounded-xl bg-slate-50 border border-slate-200 mb-3 text-center font-serif text-xs">
                    <div class="font-bold uppercase">${ExportUtils.escape(s.kopLine1)}</div>
                    <div class="font-bold uppercase">${ExportUtils.escape(s.kopLine2)}</div>
                    <div class="font-extrabold text-sm uppercase">${ExportUtils.escape(s.schoolName)}</div>
                    <div class="text-[11px] text-slate-600">${ExportUtils.escape(s.address)}</div>
                  </div>

                  <textarea id="gen-editor" oninput="Modules.GeneratorAI.generatedText=this.value" rows="16" placeholder="Klik 'Susun Dokumen dengan AI' untuk menghasilkan naskah dokumen resmi di sini..." class="w-full flex-1 border border-slate-300 rounded-xl p-4 font-serif text-xs sm:text-sm leading-relaxed text-slate-900 focus:border-navy-800 outline-none">${ExportUtils.escape(this.generatedText)}</textarea>
                </div>
              </div>
            </div>
          `;
        },

        async generateDocument() {
          const s = DB.data.settings;
          this.selectedType = document.getElementById('gen-type').value;
          this.nomorSurat = document.getElementById('gen-nomor').value.trim();
          this.tanggalSurat = document.getElementById('gen-tgl').value.trim();
          this.tujuan = document.getElementById('gen-tujuan').value.trim();
          this.perihal = document.getElementById('gen-perihal').value.trim();
          this.rincian = document.getElementById('gen-rincian').value.trim();

          const noStr = this.nomorSurat || '[Nomor Surat Belum Diisi]';
          const tglStr = this.tanggalSurat || '[Tanggal Belum Diisi]';
          const tujStr = this.tujuan || '[Tujuan / Sasaran Penerima]';
          const perStr = this.perihal || `[Perihal / Judul ${this.selectedType}]`;
          const rinStr = this.rincian || 'Sesuai ketentuan dan program kerja satuan pendidikan yang berlaku.';

          const btn = document.getElementById('btn-gen-doc');
          if (btn) btn.innerHTML = '<i class="fa-solid fa-spinner fa-spin"></i> Menyusun Dokumen...';

          const prompt = `Buatkan naskah resmi "${this.selectedType}" untuk sekolah ${s.schoolName} (NPSN: ${s.npsn}), Kepala Sekolah: ${s.principalName} (NIP: ${s.principalNip}), Tahun Pelajaran ${s.academicYear}. Nomor: ${noStr}, Tanggal: ${tglStr}, Tujuan: ${tujStr}, Perihal: ${perStr}, Rincian: ${rinStr}. Jangan tampilkan kop surat lagi, mulai dari judul/nomor dokumen sampai penutup. Jangan mengarang angka atau data faktual yang tidak disebutkan.`;
          const aiResult = await AI.generate(prompt);

          if (aiResult) {
            this.generatedText = aiResult;
          } else {
            this.generatedText = this.buildStructuredTemplate(this.selectedType, noStr, tglStr, tujStr, perStr, rinStr, s);
          }

          UI.toast(`Naskah "${this.selectedType}" berhasil disusun!`, 'success');
          this.render(document.getElementById('app-content'));
        },

        buildStructuredTemplate(type, nomor, tanggal, tujuan, perihal, rincian, s) {
          if (type === 'Surat Keputusan (SK)') {
            return `KEPUTUSAN KEPALA ${s.schoolName.toUpperCase()}
Nomor: ${nomor}

TENTANG
${perihal.toUpperCase()}

KEPALA ${s.schoolName.toUpperCase()},

Menimbang : a. bahwa dalam rangka kelancaran pelaksanaan kegiatan pendidikan dan administrasi di ${s.schoolName} Tahun Pelajaran ${s.academicYear}, dipandang perlu menetapkan ${perihal};
            b. bahwa nama-nama yang tercantum dalam lampiran keputusan ini dipandang cakap dan memenuhi syarat untuk melaksanakan tugas dimaksud.

Mengingat  : 1. Undang-Undang Nomor 20 Tahun 2003 tentang Sistem Pendidikan Nasional;
            2. Peraturan Pemerintah tentang Standar Nasional Pendidikan;
            3. Program Kerja ${s.schoolName} Tahun Pelajaran ${s.academicYear}.

MEMUTUSKAN:

Menetapkan :
KESATU     : Menetapkan ${perihal} sebagaimana tercantum dalam rincian: ${rincian}.
KEDUA      : Kepada yang bersangkutan agar melaksanakan tugas dengan penuh tanggung jawab dan melaporkan hasilnya kepada Kepala Sekolah.
KETIGA     : Segala biaya yang timbul akibat dikeluarkannya keputusan ini dibebankan pada anggaran sekolah yang sesuai.
KEEMPAT    : Keputusan ini berlaku sejak tanggal ditetapkan dan apabila terdapat kekeliruan akan diperbaiki sebagaimana mestinya.

Ditetapkan di : ${s.city}
Pada tanggal  : ${tanggal}`;
          }

          if (type === 'Surat Tugas') {
            return `SURAT TUGAS
Nomor: ${nomor}

Yang bertanda tangan di bawah ini Kepala ${s.schoolName}:
Nama    : ${s.principalName}
NIP     : ${s.principalNip}
Jabatan : Kepala Sekolah

Dengan ini menugaskan kepada:
Nama / Sasaran : ${tujuan}

Untuk melaksanakan tugas:
Perihal / Kegiatan : ${perihal}
Rincian Pelaksanaan: ${rincian}

Demikian surat tugas ini dibuat untuk dilaksanakan dengan penuh rasa tanggung jawab dan melaporkan hasilnya setelah selesai melaksanakan tugas.`;
          }

          if (type === 'Berita Acara') {
            return `BERITA ACARA
Nomor: ${nomor}

Pada hari ini, tanggal ${tanggal}, bertempat di ${s.schoolName}, telah dilaksanakan kegiatan:
Perihal : ${perihal}
Peserta / Pihak Terkait : ${tujuan}

Uraian Pelaksanaan:
${rincian}

Demikian Berita Acara ini dibuat dengan sesungguhnya untuk dapat dipergunakan sebagaimana mestinya.`;
          }

          return `${type.toUpperCase()}
Nomor: ${nomor}

Kepada Yth.
${tujuan}
di Tempat

Perihal: ${perihal}

Dengan hormat,
Sehubungan dengan penyelenggaraan program kerja ${s.schoolName} Tahun Pelajaran ${s.academicYear}, bersama ini kami sampaikan hal-hal terkait ${perihal} sebagai berikut:

${rincian}

Demikian ${type} ini kami sampaikan. Atas perhatian dan kerja sama yang baik, kami ucapkan terima kasih.`;
        },

        saveToArchive() {
          if (!UI.canEdit()) return;
          const content = document.getElementById('gen-editor')?.value.trim() || this.generatedText;
          if (!content) {
            UI.toast('Susun atau isi naskah dokumen terlebih dahulu sebelum menyimpan.', 'warning');
            return;
          }
          DB.data.arsipDokumen.unshift({
            id: DB.uid('arc'),
            judul: `${this.selectedType} — ${this.perihal || 'Dokumen Baru'}`,
            kategori: 'Administrasi Umum',
            nomorSurat: this.nomorSurat || '[Belum Bernomor]',
            tanggal: this.tanggalSurat || new Date().toISOString().split('T')[0],
            konten: content
          });
          DB.save(true);
          UI.toast('Dokumen berhasil disimpan ke Arsip Digital!', 'success');
        },

        downloadWord() {
          const content = document.getElementById('gen-editor')?.value.trim() || this.generatedText;
          if (!content) {
            UI.toast('Belum ada naskah dokumen untuk diunduh.', 'warning');
            return;
          }
          const html = `<div style="white-space:pre-line;">${ExportUtils.escape(content)}</div>`;
          ExportUtils.downloadWord(`${this.selectedType.replace(/[^a-zA-Z0-9]/g, '_')}`, html, true, true, this.tanggalSurat);
        },

        printDoc() {
          const content = document.getElementById('gen-editor')?.value.trim() || this.generatedText;
          if (!content) {
            UI.toast('Belum ada naskah dokumen untuk dicetak.', 'warning');
            return;
          }
          const html = `<div style="white-space:pre-line;">${ExportUtils.escape(content)}</div>`;
          ExportUtils.printDocument(this.selectedType, html, true, true, this.tanggalSurat);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 13: ASISTEN KEPALA SEKOLAH AI (CHATBOT INTERAKTIF)
      ------------------------------------------------------------------- */
      ChatbotAI: {
        messages: [
          {
            sender: 'ai',
            text: 'Assalamu’alaikum / Salam Sejahtera, Bapak/Ibu Kepala Sekolah. Saya adalah Asisten Digital KEPALA SEKOLAH MASTER AI. Silakan ajukan pertanyaan atau pilih topik cepat di bawah mengenai penyusunan program kerja, supervisi akademik, analisis Rapor Pendidikan, RKJM/RKT, maupun tata kelola administrasi sekolah.'
          }
        ],

        quickPrompts: [
          'Bagaimana menyusun program kerja kepala sekolah?',
          'Bagaimana membuat program supervisi?',
          'Apa tindak lanjut hasil supervisi guru?',
          'Bagaimana menyusun RKJM dan RKT?',
          'Bagaimana menganalisis hasil Rapor Pendidikan?',
          'Apa saja administrasi yang perlu disiapkan kepala sekolah?',
          'Bagaimana meningkatkan mutu pembelajaran di sekolah?'
        ],

        render(container) {
          container.innerHTML = `
            <div class="animate-fade-in space-y-5">
              ${UI.moduleHeader(
                'Asisten Kepala Sekolah AI',
                'Konsultasi praktis, sistematis, dan kontekstual mengenai kepemimpinan dan administrasi sekolah.',
                'fa-solid fa-robot'
              )}

              <!-- Pertanyaan Cepat -->
              <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm">
                <div class="text-xs font-bold text-slate-600 mb-2">Pilih Contoh Pertanyaan Praktis:</div>
                <div class="flex flex-wrap gap-2">
                  ${this.quickPrompts.map((q, idx) => `
                    <button onclick="Modules.ChatbotAI.askQuick(${idx})" class="px-3 py-1.5 rounded-full bg-navy-50 hover:bg-navy-900 text-navy-900 hover:text-gold-400 border border-navy-200 text-xs font-semibold transition">
                      ${ExportUtils.escape(q)}
                    </button>
                  `).join('')}
                </div>
              </div>

              <!-- Jendela Percakapan -->
              <div class="bg-white rounded-2xl border border-slate-200 shadow-sm flex flex-col h-[500px]">
                <div id="chat-messages" class="flex-1 overflow-y-auto p-5 space-y-4">
                  ${this.messages.map(m => `
                    <div class="flex ${m.sender === 'user' ? 'justify-end' : 'justify-start'}">
                      <div class="max-w-[85%] rounded-2xl px-4 py-3 text-xs sm:text-sm leading-relaxed whitespace-pre-line ${
                        m.sender === 'user'
                          ? 'bg-navy-900 text-white rounded-br-none'
                          : 'bg-slate-100 text-slate-800 border border-slate-200 rounded-bl-none'
                      }">
                        ${ExportUtils.escape(m.text)}
                      </div>
                    </div>
                  `).join('')}
                </div>

                <form onsubmit="Modules.ChatbotAI.sendMessage(event)" class="p-3.5 border-t border-slate-200 bg-slate-50 flex gap-2">
                  <input type="text" id="chat-input" placeholder="Tulis pertanyaan Anda untuk Asisten Kepala Sekolah AI..." class="flex-1 border border-slate-300 rounded-xl px-4 py-2.5 text-xs sm:text-sm bg-white outline-none focus:border-navy-800" />
                  <button type="submit" class="px-5 py-2.5 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-extrabold flex items-center gap-2">
                    <i class="fa-solid fa-paper-plane text-gold-400"></i> Kirim
                  </button>
                </form>
              </div>
            </div>
          `;
          const box = document.getElementById('chat-messages');
          if (box) box.scrollTop = box.scrollHeight;
        },

        askQuick(idx) {
          const q = this.quickPrompts[idx];
          if (q) this.processQuestion(q);
        },

        sendMessage(e) {
          e.preventDefault();
          const input = document.getElementById('chat-input');
          const text = input.value.trim();
          if (!text) return;
          input.value = '';
          this.processQuestion(text);
        },

        async processQuestion(question) {
          this.messages.push({ sender: 'user', text: question });
          this.render(document.getElementById('app-content'));

          const s = DB.data.settings;
          const aiReply = await AI.generate(`Konteks sekolah: ${s.schoolName} (${s.jenjang}), Kepala Sekolah: ${s.principalName}. Pertanyaan: ${question}`);
          const finalReply = aiReply || this.getExpertKnowledgeReply(question);

          this.messages.push({ sender: 'ai', text: finalReply });
          this.render(document.getElementById('app-content'));
        },

        getExpertKnowledgeReply(q) {
          const lower = q.toLowerCase();
          if (lower.includes('program kerja')) {
            return `Langkah sistematis menyusun Program Kerja Kepala Sekolah:
1. Evaluasi Diri & Rapor Pendidikan: Petakan capaian tahun sebelumnya dan indikator yang perlu penguatan.
2. Kelompokkan ke 4 Bidang Utama:
   • Manajerial (perencanaan RKT/RKAS, pengelolaan GTK, sarpras, keuangan).
   • Supervisi Akademik & Tendik (perencanaan, observasi kelas, umpan balik).
   • Kewirausahaan & Inovasi Sekolah (program unggulan, literasi-numerasi, sekolah sehat).
   • Pengembangan Karakter & Kemitraan (budaya positif, TPPK, sinergi komite sekolah).
3. Susun Matriks Waktu & Penanggung Jawab agar mudah dipantau setiap bulan pada menu Administrasi Umum.`;
          }
          if (lower.includes('tindak lanjut') && lower.includes('supervisi')) {
            return `Tindak Lanjut Hasil Supervisi Guru yang Efektif:
1. Umpan Balik Coaching (1-on-1): Ajak guru berefleksi mengenai kekuatan pembelajaran dan area yang perlu diperbaiki secara dialogis.
2. Pengelompokan Kebutuhan Guru: Kelompokkan guru berdasarkan kebutuhan (misalnya penyusunan modul ajar berdiferensiasi atau asesmen formatif).
3. Optimalisasi Komunitas Belajar (Kombel): Jadikan guru dengan praktik baik sebagai narasumber berbagi di sekolah.
4. Pendampingan & Supervisi Ulang: Lakukan observasi lanjutan untuk memastikan perubahan praktik di kelas.`;
          }
          if (lower.includes('supervisi')) {
            return `Cara Menyusun Program Supervisi Kepala Sekolah:
1. Tetapkan tujuan dan jadwal supervisi di awal semester (pra-observasi, observasi kelas, pasca-observasi).
2. Siapkan instrumen supervisi yang mencakup 3 aspek utama: Perencanaan Pembelajaran, Pelaksanaan Pembelajaran, dan Asesmen Pembelajaran.
3. Gunakan menu "Supervisi Akademik & Manajerial" di aplikasi ini untuk mencatat skor, rekomendasi, dan mencetak laporan individual maupun rekap keseluruhan.`;
          }
          if (lower.includes('rkjm') || lower.includes('rkt')) {
            return `Panduan Penyusunan RKJM dan RKT:
1. RKJM (Rencana Kerja Jangka Menengah - 4 Tahun) memuat visi, misi, tujuan, serta sasaran strategis jangka menengah sekolah.
2. RKT (Rencana Kerja Tahunan) merupakan penjabaran operasional RKJM untuk 1 tahun pelajaran berdasarkan hasil analisis Rapor Pendidikan (Perencanaan Berbasis Data).
3. Kegiatan prioritas di RKT kemudian dirinci ke dalam RKAS/ARKAS sesuai sumber dana sekolah.`;
          }
          if (lower.includes('rapor pendidikan')) {
            return `Langkah Menganalisis Rapor Pendidikan (Tahap Identifikasi - Refleksi - Benahi):
1. Identifikasi: Lihat 6 indikator utama (Literasi, Numerasi, Karakter, Iklim Keamanan, Iklim Kebinekaan, Kualitas Pembelajaran) dan tandai indikator berlabel Sedang/Kurang.
2. Refleksi: Telusuri akar masalah pada proses pembelajaran atau kompetensi guru.
3. Benahi: Tentukan kegiatan perbaikan dan masukkan ke RKT serta RKAS. Anda dapat langsung memasukkan skor Rapor Pendidikan di menu "Perencanaan & RKAS/Rapor" agar AI menyusun rekomendasinya otomatis.`;
          }
          return `Terima kasih atas pertanyaan Anda mengenai "${q}".

Berikut langkah praktis yang dapat diterapkan di ${DB.data.settings.schoolName}:
1. Pastikan seluruh data pendukung (profil sekolah, data GTK, siswa, dan program kerja) sudah tercatat rapi pada modul terkait di aplikasi ini.
2. Gunakan pendekatan Perencanaan Berbasis Data (PBD) dengan memprioritaskan kegiatan yang berdampak langsung pada kualitas pembelajaran peserta didik.
3. Dokumentasikan setiap tahapan kegiatan (perencanaan, pelaksanaan, notulen, daftar hadir, dan laporan evaluasi) menggunakan fitur Generator Dokumen AI agar tertib administrasi.

Jika Anda memerlukan draf dokumen resmi atau analisis spesifik, silakan tuliskan data rinciannya.`;
        }
      },

      /* -------------------------------------------------------------------
         MODUL 14: PUSAT TEMPLATE ADMINISTRASI
      ------------------------------------------------------------------- */
      TemplateCenter: {
        templates: [
          { title: 'Template Program Kerja Kepala Sekolah', category: 'Perencanaan', sample: 'PROGRAM KERJA KEPALA SEKOLAH\nTahun Pelajaran 2026/2027\n\nI. Pendahuluan\nII. Visi, Misi, dan Tujuan Sekolah\nIII. Program Bidang Manajerial, Supervisi, Kewirausahaan, dan Karakter\nIV. Jadwal Pelaksanaan dan Penutup.' },
          { title: 'Template RKJM (4 Tahun)', category: 'Perencanaan', sample: 'RENCANA KERJA JANGKA MENENGAH (RKJM)\nPeriode 2025 - 2028\n\n1. Analisis Lingkungan Strategis\n2. Identifikasi Tantangan Nyata\n3. Sasaran Strategis 4 Tahun\n4. Rencana Program & Pendanaan.' },
          { title: 'Template RKT (Tahunan)', category: 'Perencanaan', sample: 'RENCANA KERJA TAHUNAN (RKT)\nBerbasis Rapor Pendidikan\n\nA. Identifikasi Indikator Prioritas\nB. Akar Masalah\nC. Kegiatan Benahi & Sasaran\nD. Jadwal & Penanggung Jawab.' },
          { title: 'Template Instrumen Supervisi Akademik', category: 'Supervisi', sample: 'INSTRUMEN SUPERVISI PEMBELAJARAN\n\nAspek I: Perencanaan Pembelajaran (Modul Ajar/RPP)\nAspek II: Pelaksanaan Pembelajaran (Pendahuluan, Inti, Penutup)\nAspek III: Asesmen Pembelajaran & Refleksi\nCatatan & Rekomendasi Kepala Sekolah.' },
          { title: 'Template Surat Keputusan (SK) Panitia', category: 'Persuratan', sample: 'KEPUTUSAN KEPALA SEKOLAH\nTentang Pembentukan Panitia Kegiatan Sekolah\n\nMenimbang, Mengingat, Memutuskan: Menetapkan susunan panitia beserta uraian tugasnya.' },
          { title: 'Template Surat Undangan Rapat Dinas', category: 'Persuratan', sample: 'UNDANGAN RAPAT DINAS\n\nMengharap kehadiran Bapak/Ibu Guru dan Staf pada rapat koordinasi sekolah yang akan dilaksanakan pada hari/tanggal, waktu, dan tempat terlampir.' },
          { title: 'Template Berita Acara Kegiatan', category: 'Administrasi', sample: 'BERITA ACARA PELAKSANAAN KEGIATAN\n\nPada hari ini telah dilaksanakan kegiatan sekolah dengan tertib dan lancar sesuai daftar hadir terlampir.' },
          { title: 'Template Notulen Rapat Dewan Guru', category: 'Administrasi', sample: 'NOTULEN RAPAT DEWAN GURU\n\n1. Pembukaan\n2. Arahan Kepala Sekolah\n3. Pembahasan Agenda\n4. Kesimpulan & Keputusan Rapat\n5. Penutup.' },
          { title: 'Template Laporan Kegiatan Sekolah', category: 'Laporan', sample: 'LAPORAN PELAKSANAAN KEGIATAN\n\nI. Latar Belakang\nII. Tujuan & Sasaran\nIII. Waktu & Tempat\nIV. Hasil Pelaksanaan & Evaluasi\nV. Penutup.' },
          { title: 'Template Instrumen Monitoring & Evaluasi', category: 'Evaluasi', sample: 'LEMBAR MONITORING DAN EVALUASI PROGRAM\n\nNama Program, Target (%), Capaian Realisasi (%), Kendala yang Dihadapi, dan Rencana Tindak Lanjut.' }
        ],

        render(container) {
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pusat Template Administrasi Sekolah',
                'Kumpulan contoh format administrasi resmi yang siap digunakan dan diedit sesuai kebutuhan sekolah.',
                'fa-solid fa-file-lines'
              )}

              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
                ${this.templates.map((tpl, idx) => `
                  <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm flex flex-col justify-between">
                    <div>
                      <span class="px-2.5 py-0.5 rounded-full bg-navy-50 text-navy-900 text-[10px] font-bold uppercase">${ExportUtils.escape(tpl.category)}</span>
                      <h3 class="text-sm font-extrabold text-navy-900 mt-2">${ExportUtils.escape(tpl.title)}</h3>
                      <pre class="mt-3 p-3 rounded-xl bg-slate-50 border border-slate-200 text-[11px] text-slate-600 whitespace-pre-wrap font-sans leading-relaxed">${ExportUtils.escape(tpl.sample)}</pre>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-100 flex justify-end">
                      <button onclick="Modules.TemplateCenter.useTemplate(${idx})" class="px-4 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                        <i class="fa-solid fa-wand-magic-sparkles text-gold-400"></i> Gunakan Template
                      </button>
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>
          `;
        },

        useTemplate(idx) {
          const tpl = this.templates[idx];
          if (!tpl) return;
          Modules.GeneratorAI.perihal = tpl.title;
          Modules.GeneratorAI.generatedText = tpl.sample;
          App.navigate('generator-ai');
          UI.toast(`Template "${tpl.title}" siap diedit pada Generator Dokumen AI.`, 'success');
        }
      },

      /* -------------------------------------------------------------------
         MODUL 15: PUSAT LAPORAN SEKOLAH
      ------------------------------------------------------------------- */
      PusatLaporan: {
        selectedReport: 'Laporan Bulanan Kepala Sekolah',
        reportTypes: [
          'Laporan Bulanan Kepala Sekolah',
          'Laporan Semester Sekolah',
          'Laporan Tahunan Kepala Sekolah',
          'Laporan Rekap Supervisi Akademik',
          'Laporan Kegiatan Sekolah',
          'Laporan Evaluasi & Capaian Program',
          'Laporan Perkembangan Sekolah'
        ],

        render(container) {
          const htmlPreview = this.buildReportHtml(this.selectedReport);
          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pusat Laporan Sekolah Terpadu',
                'Penyusunan laporan resmi otomatis yang menghimpun data nyata dari seluruh modul aplikasi.',
                'fa-solid fa-file-invoice',
                `
                  <button onclick="Modules.PusatLaporan.printReport()" class="px-3.5 py-2 bg-white border border-slate-300 hover:bg-slate-100 text-navy-900 rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-print"></i> Cetak Laporan
                  </button>
                  <button onclick="Modules.PusatLaporan.downloadWord()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-file-word text-gold-400"></i> Unduh Word (.doc)
                  </button>
                `
              )}

              <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex flex-wrap gap-2">
                ${this.reportTypes.map(rt => `
                  <button onclick="Modules.PusatLaporan.selectType('${rt}')" class="px-3.5 py-2 rounded-xl text-xs font-bold ${this.selectedReport === rt ? 'bg-navy-900 text-gold-400' : 'bg-slate-100 text-slate-700'}">
                    ${rt}
                  </button>
                `).join('')}
              </div>

              <div class="bg-white rounded-2xl p-6 sm:p-8 border border-slate-200 shadow-sm">
                ${ExportUtils.getKopSuratHTML()}
                <div class="font-serif text-sm leading-relaxed text-slate-900">${htmlPreview}</div>
                ${ExportUtils.getSignatureHTML()}
              </div>
            </div>
          `;
        },

        selectType(rt) {
          this.selectedReport = rt;
          this.render(document.getElementById('app-content'));
        },

        buildReportHtml(title) {
          const s = DB.data.settings;
          const totalGtk = DB.data.gtk.length;
          const totalSiswa = DB.data.siswa.rombel.reduce((acc, r) => acc + Number(r.lakiLaki || 0) + Number(r.perempuan || 0), 0);
          const rp = DB.data.perencanaan.raporPendidikan;

          return `
            <h2 style="text-align:center;text-transform:uppercase;margin-bottom:4px;">${ExportUtils.escape(title)}</h2>
            <p style="text-align:center;margin-top:0;margin-bottom:18px;">Tahun Pelajaran ${ExportUtils.escape(s.academicYear)} (${ExportUtils.escape(s.semester)})</p>
            <h3>I. Identitas Satuan Pendidikan</h3>
            <p>Nama Sekolah: <strong>${ExportUtils.escape(s.schoolName)}</strong> (NPSN: ${ExportUtils.escape(s.npsn)})<br/>
            Kepala Sekolah: <strong>${ExportUtils.escape(s.principalName)}</strong> (NIP. ${ExportUtils.escape(s.principalNip)})<br/>
            Jumlah Guru & Tenaga Kependidikan: <strong>${totalGtk} Orang</strong><br/>
            Jumlah Peserta Didik Aktif: <strong>${totalSiswa} Siswa</strong> (${DB.data.siswa.rombel.length} Rombongan Belajar)</p>

            <h3>II. Rekapitulasi Pelaksanaan Program Kerja</h3>
            <table>
              <tr><th>No</th><th>Bidang</th><th>Program / Kegiatan</th><th>Status</th><th>Progres</th></tr>
              ${DB.data.adminUmum.programKerja.map((p, i) => `
                <tr><td>${i + 1}</td><td>${ExportUtils.escape(p.bidang)}</td><td>${ExportUtils.escape(p.kegiatan)}</td><td>${ExportUtils.escape(p.status)}</td><td>${p.progres}%</td></tr>
              `).join('')}
            </table>

            <h3>III. Capaian Mutu Rapor Pendidikan (${ExportUtils.escape(rp.tahun)}) & Supervisi</h3>
            <p>Skor Literasi: <strong>${rp.literasi}%</strong> • Numerasi: <strong>${rp.numerasi}%</strong> • Karakter: <strong>${rp.karakter}%</strong> • Iklim Keamanan: <strong>${rp.iklimKeamanan}%</strong> • Kualitas Pembelajaran: <strong>${rp.kualitasPembelajaran}%</strong>.</p>
            <p>Pelaksanaan Supervisi Akademik telah menjangkau <strong>${DB.data.supervisi.records.length} guru</strong> dengan rencana tindak lanjut berbasis komunitas belajar.</p>
          `;
        },

        printReport() {
          ExportUtils.printDocument(this.selectedReport, this.buildReportHtml(this.selectedReport), true, true);
        },

        downloadWord() {
          ExportUtils.downloadWord(this.selectedReport.replace(/[^a-zA-Z0-9]/g, '_'), this.buildReportHtml(this.selectedReport), true, true);
        }
      },

      /* -------------------------------------------------------------------
         MODUL 16: PENGELOLAAN DOKUMEN DIGITAL
      ------------------------------------------------------------------- */
      ArsipDokumen: {
        searchQuery: '',
        filterCategory: 'Semua',

        render(container) {
          const categories = ['Semua', 'Administrasi Umum', 'Perencanaan', 'Supervisi', 'Guru dan Tenaga Kependidikan', 'Peserta Didik', 'Sarana Prasarana', 'Kurikulum', 'Laporan'];
          const q = this.searchQuery.toLowerCase();
          const list = DB.data.arsipDokumen.filter(d => {
            const matchCat = this.filterCategory === 'Semua' || d.kategori === this.filterCategory;
            const matchQ = d.judul.toLowerCase().includes(q) || d.nomorSurat.toLowerCase().includes(q);
            return matchCat && matchQ;
          });

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pengelolaan Dokumen & Arsip Digital',
                'Penyimpanan terpusat dokumen SK, surat dinas, laporan supervisi & arsip sekolah berdasarkan kategori.',
                'fa-solid fa-box-archive',
                `
                  <button onclick="Modules.ArsipDokumen.openAddModal()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5">
                    <i class="fa-solid fa-upload text-gold-400"></i> Simpan / Unggah Dokumen Baru
                  </button>
                `
              )}

              <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex flex-col md:flex-row md:items-center justify-between gap-3">
                <div class="flex flex-wrap gap-1.5">
                  ${categories.map(c => `
                    <button onclick="Modules.ArsipDokumen.setCategory('${c}')" class="px-3 py-1.5 rounded-xl text-xs font-bold ${this.filterCategory === c ? 'bg-navy-900 text-gold-400' : 'bg-slate-100 text-slate-700'}">
                      ${c}
                    </button>
                  `).join('')}
                </div>
                <input type="text" value="${ExportUtils.escape(this.searchQuery)}" oninput="Modules.ArsipDokumen.onSearch(this.value)" placeholder="Cari judul atau nomor surat..." class="w-full md:w-64 border border-slate-300 rounded-xl px-3.5 py-2 text-xs" />
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                ${list.length === 0
                  ? `<div class="col-span-2 bg-white rounded-2xl p-8 text-center text-xs text-slate-500 border border-slate-200">Tidak ada dokumen pada kategori ini.</div>`
                  : list.map(d => `
                    <div class="bg-white rounded-2xl p-5 border border-slate-200 shadow-sm flex flex-col justify-between">
                      <div>
                        <div class="flex items-center justify-between gap-2 mb-2">
                          <span class="px-2.5 py-0.5 rounded-full bg-navy-50 text-navy-900 text-[10px] font-bold">${ExportUtils.escape(d.kategori)}</span>
                          <span class="text-[11px] text-slate-400">${ExportUtils.escape(d.tanggal)}</span>
                        </div>
                        <h3 class="text-sm font-extrabold text-navy-900">${ExportUtils.escape(d.judul)}</h3>
                        <div class="text-xs text-slate-500 mt-0.5">Nomor: ${ExportUtils.escape(d.nomorSurat)}</div>
                        <p class="text-xs text-slate-600 mt-2 line-clamp-3 whitespace-pre-line">${ExportUtils.escape(d.konten)}</p>
                      </div>
                      <div class="mt-4 pt-3 border-t border-slate-100 flex items-center justify-end gap-2">
                        <button onclick="Modules.ArsipDokumen.downloadSingleWord('${d.id}')" class="px-3 py-1.5 bg-navy-900 text-white rounded-lg text-xs font-bold">
                          <i class="fa-solid fa-file-word text-gold-400 mr-1"></i> Unduh Word
                        </button>
                        <button onclick="Modules.ArsipDokumen.deleteDoc('${d.id}')" class="px-3 py-1.5 bg-rose-50 text-rose-700 rounded-lg text-xs font-bold">
                          Hapus
                        </button>
                      </div>
                    </div>
                  `).join('')}
              </div>
            </div>
          `;
        },

        setCategory(cat) {
          this.filterCategory = cat;
          this.render(document.getElementById('app-content'));
        },

        onSearch(val) {
          this.searchQuery = val;
          this.render(document.getElementById('app-content'));
        },

        openAddModal() {
          if (!UI.canEdit()) return;
          UI.openModal({
            title: 'Tambah Dokumen ke Arsip Digital',
            bodyHtml: `
              <div class="space-y-3 text-xs">
                <div><label class="font-bold block mb-1">Judul Dokumen</label><input type="text" id="arc-judul" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                <div class="grid grid-cols-2 gap-3">
                  <div><label class="font-bold block mb-1">Kategori</label><select id="arc-kat" class="w-full border border-slate-300 rounded-xl px-3 py-2 bg-white"><option>Administrasi Umum</option><option>Perencanaan</option><option>Supervisi</option><option>Guru dan Tenaga Kependidikan</option><option>Peserta Didik</option><option>Sarana Prasarana</option><option>Kurikulum</option><option>Laporan</option></select></div>
                  <div><label class="font-bold block mb-1">Nomor Surat / Dokumen</label><input type="text" id="arc-no" placeholder="-" class="w-full border border-slate-300 rounded-xl px-3 py-2" /></div>
                </div>
                <div><label class="font-bold block mb-1">Isi / Ringkasan Dokumen</label><textarea id="arc-isi" rows="4" class="w-full border border-slate-300 rounded-xl px-3 py-2"></textarea></div>
              </div>
            `,
            footerHtml: `
              <button onclick="UI.closeModal()" class="px-4 py-2 bg-slate-200 rounded-xl text-xs font-semibold">Batal</button>
              <button onclick="Modules.ArsipDokumen.saveNewDoc()" class="px-4 py-2 bg-navy-900 text-white rounded-xl text-xs font-bold">Simpan Arsip</button>
            `
          });
        },

        saveNewDoc() {
          const judul = document.getElementById('arc-judul').value.trim();
          if (!judul) return;
          DB.data.arsipDokumen.unshift({
            id: DB.uid('arc'),
            judul,
            kategori: document.getElementById('arc-kat').value,
            nomorSurat: document.getElementById('arc-no').value.trim() || '-',
            tanggal: new Date().toISOString().split('T')[0],
            konten: document.getElementById('arc-isi').value.trim()
          });
          DB.save(true);
          UI.closeModal();
          this.render(document.getElementById('app-content'));
        },

        downloadSingleWord(id) {
          const d = DB.data.arsipDokumen.find(x => x.id === id);
          if (!d) return;
          const html = `<h3 style="text-align:center;">${ExportUtils.escape(d.judul)}</h3><p style="text-align:center;">Nomor: ${ExportUtils.escape(d.nomorSurat)} • Tanggal: ${ExportUtils.escape(d.tanggal)}</p><div style="white-space:pre-line;">${ExportUtils.escape(d.konten)}</div>`;
          ExportUtils.downloadWord(d.judul.replace(/[^a-zA-Z0-9]/g, '_'), html, true, true, d.tanggal);
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
         MODUL UNDUH FILE TERPADU (WORD, EXCEL, JSON & FILE APLIKASI)
      ------------------------------------------------------------------- */
      UnduhFile: {
        searchQuery: '',

        render(container) {
          const s = DB.data.settings;
          const q = this.searchQuery.toLowerCase();

          const items = [
            {
              kategori: 'Administrasi Umum',
              judul: 'Paket Lengkap 13 Buku Administrasi Kepala Sekolah',
              deskripsi: 'Berisi seluruh rekaman Program Kerja, RKT, Agenda Harian, Buku Tamu, Notulen, Surat Masuk/Keluar, Ekspedisi, dan Inventaris Dokumen.',
              format: 'Microsoft Word (.doc)',
              badgeColor: 'bg-navy-100 text-navy-900',
              actionHtml: `<button onclick="Modules.UnduhFile.downloadAllAdminWord()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Unduh Word</button>`
            },
            {
              kategori: 'Administrasi Umum',
              judul: 'Program Kerja Kepala Sekolah & Matriks Capaian',
              deskripsi: 'Daftar program kerja manajerial, supervisi, kewirausahaan, beserta target dan persentase progres.',
              format: 'Excel (.xlsx) & Word (.doc)',
              badgeColor: 'bg-emerald-100 text-emerald-800',
              actionHtml: `
                <button onclick="Modules.UnduhFile.downloadProgramKerjaExcel()" class="px-3 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-excel"></i> Excel</button>
                <button onclick="Modules.AdminUmum.switchTab('programKerja'); Modules.AdminUmum.exportWordCurrentTab();" class="px-3 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Word</button>
              `
            },
            {
              kategori: 'Perencanaan & RKAS',
              judul: 'Dokumen RKT, RKJM & Analisis Rapor Pendidikan',
              deskripsi: 'Kumpulan dokumen perencanaan berbasis data (PBD) dan rencana kerja tahunan sekolah lengkap dengan kop surat.',
              format: 'Microsoft Word (.doc)',
              badgeColor: 'bg-navy-100 text-navy-900',
              actionHtml: `<button onclick="Modules.Perencanaan.exportWordSection()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Unduh Word</button>`
            },
            {
              kategori: 'Perencanaan & RKAS',
              judul: 'Kertas Kerja RKAS & Usulan Draft ARKAS',
              deskripsi: 'Rincian kode kegiatan, sumber dana BOS, volume, anggaran, dan status realisasi belanja sekolah.',
              format: 'Microsoft Excel (.xlsx)',
              badgeColor: 'bg-emerald-100 text-emerald-800',
              actionHtml: `<button onclick="Modules.Perencanaan.exportRkasExcel()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-excel"></i> Unduh Excel</button>`
            },
            {
              kategori: 'Supervisi Akademik',
              judul: 'Rekapitulasi Hasil Supervisi Akademik & Manajerial',
              deskripsi: 'Rekap nilai perencanaan, pelaksanaan, asesmen pembelajaran, rekomendasi, dan tindak lanjut seluruh guru.',
              format: 'Word (.doc) & Excel (.xlsx)',
              badgeColor: 'bg-gold-100 text-gold-800',
              actionHtml: `
                <button onclick="Modules.Supervisi.exportAllWord()" class="px-3 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Word</button>
                <button onclick="Modules.UnduhFile.downloadSupervisiExcel()" class="px-3 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-excel"></i> Excel</button>
              `
            },
            {
              kategori: 'Manajemen GTK',
              judul: 'Database Guru & Tenaga Kependidikan (GTK)',
              deskripsi: 'Data lengkap identitas guru, NIP, pendidikan, tugas tambahan, jam mengajar (JP), kehadiran, dan nilai kinerja.',
              format: 'Microsoft Excel (.xlsx)',
              badgeColor: 'bg-emerald-100 text-emerald-800',
              actionHtml: `<button onclick="Modules.GTK.exportExcel()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-excel"></i> Unduh Excel</button>`
            },
            {
              kategori: 'Peserta Didik',
              judul: 'Rekapitulasi Peserta Didik, Rombel & Prestasi Siswa',
              deskripsi: 'Statistik siswa laki-laki & perempuan per tingkat kelas, kehadiran, serta daftar prestasi akademik/non-akademik.',
              format: 'Excel (.xlsx) & Word (.doc)',
              badgeColor: 'bg-emerald-100 text-emerald-800',
              actionHtml: `
                <button onclick="Modules.Siswa.exportExcel()" class="px-3 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-excel"></i> Excel</button>
                <button onclick="Modules.UnduhFile.downloadSiswaWord()" class="px-3 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Word</button>
              `
            },
            {
              kategori: 'Sarana & Prasarana',
              judul: 'Buku Inventaris Sarana, Prasarana & Nilai Aset',
              deskripsi: 'Daftar kode barang, ruang kelas, fasilitas TIK, kondisi kelayakan, jadwal pemeliharaan, dan nilai aset.',
              format: 'Excel (.xlsx) & Word (.doc)',
              badgeColor: 'bg-amber-100 text-amber-800',
              actionHtml: `
                <button onclick="Modules.UnduhFile.downloadSarprasExcel()" class="px-3 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-excel"></i> Excel</button>
                <button onclick="Modules.UnduhFile.downloadSarprasWord()" class="px-3 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Word</button>
              `
            },
            {
              kategori: 'Laporan & Monev',
              judul: 'Laporan Bulanan & Evaluasi Capaian Program Sekolah',
              deskripsi: 'Laporan lengkap kepala sekolah otomatis mencakup profil satuan pendidikan, capaian program, dan rapor mutu.',
              format: 'Microsoft Word (.doc)',
              badgeColor: 'bg-navy-100 text-navy-900',
              actionHtml: `<button onclick="Modules.PusatLaporan.downloadWord()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Unduh Word</button>`
            },
            {
              kategori: 'Arsip & Sistem',
              judul: 'Kumpulan Seluruh Arsip Digital & SK Tersimpan',
              deskripsi: 'Menggabungkan seluruh dokumen SK, surat dinas, dan laporan yang tersimpan di menu Pengelolaan Dokumen.',
              format: 'Microsoft Word (.doc)',
              badgeColor: 'bg-navy-100 text-navy-900',
              actionHtml: `<button onclick="Modules.UnduhFile.downloadAllArchivesWord()" class="px-3.5 py-2 bg-navy-900 hover:bg-navy-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-file-word text-gold-400"></i> Unduh Semua Arsip</button>`
            },
            {
              kategori: 'Arsip & Sistem',
              judul: 'File Cadangan Database Sekolah (Backup JSON)',
              deskripsi: 'Simpan seluruh database aplikasi ke file .json agar aman dan dapat dipindahkan ke HP Android atau komputer lain.',
              format: 'Database Cadangan (.json)',
              badgeColor: 'bg-emerald-100 text-emerald-800',
              actionHtml: `<button onclick="Modules.Pengaturan.exportBackupJson()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold flex items-center gap-1.5"><i class="fa-solid fa-database"></i> Unduh Backup JSON</button>`
            },
            {
              kategori: 'Arsip & Sistem',
              judul: 'File Aplikasi Web Mandiri (Offline HTML)',
              deskripsi: 'Unduh file aplikasi KEPALA SEKOLAH MASTER AI (.html) untuk disimpan dan dijalankan langsung di browser perangkat.',
              format: 'Aplikasi Web (.html)',
              badgeColor: 'bg-gold-100 text-gold-800',
              actionHtml: `<button onclick="Modules.UnduhFile.downloadAppHtml()" class="px-3.5 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold flex items-center gap-1.5"><i class="fa-solid fa-download"></i> Unduh Aplikasi (.html)</button>`
            }
          ];

          const filtered = items.filter(it =>
            it.judul.toLowerCase().includes(q) ||
            it.kategori.toLowerCase().includes(q) ||
            it.deskripsi.toLowerCase().includes(q)
          );

          container.innerHTML = `
            <div class="animate-fade-in space-y-6">
              ${UI.moduleHeader(
                'Pusat Unduh File & Ekspor Data',
                'Unduh dokumen administrasi Word (.doc), rekap data Excel (.xlsx), cadangan database (.json), dan file aplikasi.',
                'fa-solid fa-file-arrow-down',
                `
                  <button onclick="Modules.Pengaturan.exportBackupJson()" class="px-3.5 py-2 bg-emerald-700 hover:bg-emerald-800 text-white rounded-xl text-xs font-bold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-database"></i> Backup Semua Data
                  </button>
                  <button onclick="Modules.UnduhFile.downloadAppHtml()" class="px-3.5 py-2 bg-gold-500 hover:bg-gold-600 text-navy-950 rounded-xl text-xs font-extrabold shadow-sm flex items-center gap-1.5">
                    <i class="fa-solid fa-download"></i> Unduh File Aplikasi
                  </button>
                `
              )}

              ${UI.guideBanner(
                'Cara Mengunduh File Administrasi & Data Sekolah',
                'Pilih jenis dokumen atau rekap data yang Anda perlukan pada daftar di bawah ini, lalu klik tombol Word (.doc) untuk naskah resmi berkop surat, Excel (.xlsx) untuk olah data tabel, atau Backup JSON untuk menyimpan cadangan seluruh data sekolah.'
              )}

              <div class="bg-white rounded-2xl p-4 border border-slate-200 shadow-sm flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                <div class="text-xs font-bold text-navy-900">
                  Tersedia <span class="text-gold-700">${filtered.length} Paket File</span> siap unduh untuk <strong>${ExportUtils.escape(s.schoolName)}</strong>
                </div>
                <div class="relative w-full sm:w-80">
                  <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-3 text-slate-400 text-xs"></i>
                  <input type="text" value="${ExportUtils.escape(this.searchQuery)}" oninput="Modules.UnduhFile.onSearch(this.value)" placeholder="Cari nama file, Word, Excel, RKAS..." class="w-full pl-9 pr-3.5 py-2 border border-slate-300 rounded-xl text-xs outline-none focus:border-navy-800" />
                </div>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
                ${filtered.map(it => `
                  <div class="bg-white rounded-2xl p-5 border border-slate-200 hover:border-navy-400 shadow-sm hover:shadow-md transition flex flex-col justify-between">
                    <div>
                      <div class="flex items-center justify-between gap-2 mb-2.5">
                        <span class="px-2.5 py-0.5 rounded-full bg-navy-50 text-navy-800 border border-navy-200 text-[10px] font-bold uppercase">${ExportUtils.escape(it.kategori)}</span>
                        <span class="px-2.5 py-0.5 rounded-full ${it.badgeColor} text-[10px] font-bold">${ExportUtils.escape(it.format)}</span>
                      </div>
                      <h3 class="text-sm sm:text-base font-extrabold text-navy-900 leading-snug">${ExportUtils.escape(it.judul)}</h3>
                      <p class="text-xs text-slate-500 mt-2 leading-relaxed">${ExportUtils.escape(it.deskripsi)}</p>
                    </div>
                    <div class="mt-4 pt-3.5 border-t border-slate-100 flex flex-wrap items-center justify-end gap-2">
                      ${it.actionHtml}
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>
          `;
        },

        onSearch(val) {
          this.searchQuery = val;
          this.render(document.getElementById('app-content'));
        },

        downloadAllAdminWord() {
          const tabs = Modules.AdminUmum.tabs;
          let html = `
            <h2 style="text-align:center;text-transform:uppercase;margin-bottom:4px;">KOMPILASI 13 BUKU ADMINISTRASI UMUM KEPALA SEKOLAH</h2>
            <p style="text-align:center;margin-top:0;margin-bottom:18px;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
          `;
          tabs.forEach(tab => {
            const list = DB.data.adminUmum[tab.key] || [];
            html += `
              <h3 style="margin-top:18px;margin-bottom:6px;">${ExportUtils.escape(tab.label)}</h3>
              <table>
                <thead>
                  <tr>
                    <th>No</th>
                    ${tab.fields.map(f => `<th>${ExportUtils.escape(f.label)}</th>`).join('')}
                  </tr>
                </thead>
                <tbody>
                  ${list.length === 0
                    ? `<tr><td colspan="${tab.fields.length + 1}" style="text-align:center;">Belum ada data</td></tr>`
                    : list.map((item, idx) => `
                        <tr>
                          <td>${idx + 1}</td>
                          ${tab.fields.map(f => {
                            let val = item[f.name] ?? '-';
                            if (f.name === 'biaya') val = ExportUtils.formatRupiah(val);
                            if (f.name === 'progres') val = `${val}%`;
                            return `<td>${ExportUtils.escape(val)}</td>`;
                          }).join('')}
                        </tr>
                      `).join('')}
                </tbody>
              </table>
            `;
          });
          ExportUtils.downloadWord('Kompilasi_13_Buku_Administrasi_Kepsek', html, true, true);
        },

        downloadProgramKerjaExcel() {
          const rows = DB.data.adminUmum.programKerja.map((p, i) => ({
            No: i + 1,
            Bidang: p.bidang,
            'Program / Kegiatan': p.kegiatan,
            Target: p.target,
            Waktu: p.waktu,
            'Penanggung Jawab': p.penanggungJawab,
            Status: p.status,
            'Progres (%)': p.progres
          }));
          ExportUtils.exportExcel('Program_Kerja_Kepala_Sekolah.xlsx', 'Program_Kerja', rows);
        },

        downloadSupervisiExcel() {
          const rows = DB.data.supervisi.records.map((r, i) => {
            const avg = Math.round((Number(r.skorPerencanaan) + Number(r.skorPelaksanaan) + Number(r.skorAsesmen)) / 3);
            return {
              No: i + 1,
              Tanggal: r.tanggal,
              'Nama Guru': r.namaGuru,
              NIP: r.nip,
              'Mata Pelajaran': r.mapel,
              'Skor Perencanaan': r.skorPerencanaan,
              'Skor Pelaksanaan': r.skorPelaksanaan,
              'Skor Asesmen': r.skorAsesmen,
              'Rata-Rata': avg,
              Rekomendasi: r.rekomendasi,
              'Tindak Lanjut': r.tindakLanjut
            };
          });
          ExportUtils.exportExcel('Rekap_Supervisi_Guru.xlsx', 'Supervisi', rows);
        },

        downloadSiswaWord() {
          const rombel = DB.data.siswa.rombel;
          const prestasi = DB.data.siswa.prestasiDanCatatan;
          const html = `
            <h2 style="text-align:center;text-transform:uppercase;">REKAPITULASI DATA PESERTA DIDIK & PRESTASI</h2>
            <p style="text-align:center;">Tahun Pelajaran ${ExportUtils.escape(DB.data.settings.academicYear)}</p>
            <h3>A. Data Rombongan Belajar</h3>
            <table>
              <tr><th>Tingkat</th><th>Rombel</th><th>Laki-Laki</th><th>Perempuan</th><th>Total</th></tr>
              ${rombel.map(r => `<tr><td>${ExportUtils.escape(r.tingkat)}</td><td>${ExportUtils.escape(r.namaRombel)}</td><td>${r.lakiLaki}</td><td>${r.perempuan}</td><td>${Number(r.lakiLaki)+Number(r.perempuan)}</td></tr>`).join('')}
            </table>
            <h3>B. Catatan Prestasi & Pembinaan Karakter</h3>
            <table>
              <tr><th>Nama Siswa / Tim</th><th>Kelas</th><th>Kategori</th><th>Capaian</th><th>Tingkat</th></tr>
              ${prestasi.map(p => `<tr><td>${ExportUtils.escape(p.namaSiswa)}</td><td>${ExportUtils.escape(p.kelas)}</td><td>${ExportUtils.escape(p.jenis)}</td><td>${ExportUtils.escape(p.judul)}</td><td>${ExportUtils.escape(p.tingkat)}</td></tr>`).join('')}
            </table>
          `;
          ExportUtils.downloadWord('Rekap_Peserta_Didik_Sekolah', html, true, true);
        },

        downloadSarprasExcel() {
          const rows = DB.data.sarpras.map((s, i) => ({
            No: i + 1,
            'Kode Barang': s.kodeBarang,
            'Nama Sarana/Prasarana': s.namaBarang,
            Kategori: s.kategori,
            Jumlah: s.jumlah,
            Kondisi: s.kondisi,
            'Jadwal Pemeliharaan': s.jadwalPemeliharaan,
            'Nilai Aset (Rp)': s.nilaiAset,
            Keterangan: s.keterangan
          }));
          ExportUtils.exportExcel('Inventaris_Sarpras_Sekolah.xlsx', 'Sarpras', rows);
        },

        downloadSarprasWord() {
          const html = `
            <h2 style="text-align:center;text-transform:uppercase;">BUKU INVENTARIS SARANA & PRASARANA SEKOLAH</h2>
            <table>
              <tr><th>Kode</th><th>Nama Barang/Ruang</th><th>Jumlah</th><th>Kondisi</th><th>Nilai Aset</th><th>Keterangan</th></tr>
              ${DB.data.sarpras.map(s => `<tr><td>${ExportUtils.escape(s.kodeBarang)}</td><td>${ExportUtils.escape(s.namaBarang)}</td><td>${s.jumlah}</td><td>${ExportUtils.escape(s.kondisi)}</td><td>${ExportUtils.formatRupiah(s.nilaiAset)}</td><td>${ExportUtils.escape(s.keterangan)}</td></tr>`).join('')}
            </table>
          `;
          ExportUtils.downloadWord('Buku_Inventaris_Sarpras', html, true, true);
        },

        downloadAllArchivesWord() {
          const list = DB.data.arsipDokumen;
          if (!list.length) {
            UI.toast('Belum ada dokumen di dalam arsip digital.', 'warning');
            return;
          }
          const html = list.map((d, idx) => `
            <div style="${idx > 0 ? 'page-break-before:always;' : ''}">
              <h3 style="text-align:center;">${ExportUtils.escape(d.judul)}</h3>
              <p style="text-align:center;">Kategori: ${ExportUtils.escape(d.kategori)} • Nomor: ${ExportUtils.escape(d.nomorSurat)} • Tanggal: ${ExportUtils.escape(d.tanggal)}</p>
              <div style="white-space:pre-line;">${ExportUtils.escape(d.konten)}</div>
            </div>
          `).join('<hr/>');
          ExportUtils.downloadWord('Kumpulan_Arsip_Dokumen_Sekolah', html, true, true);
        },

        downloadAppHtml() {
          try {
            const docHtml = '<!DOCTYPE html>\n' + document.documentElement.outerHTML;
            const blob = new Blob([docHtml], { type: 'text/html;charset=utf-8' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = 'KEPALA_SEKOLAH_MASTER_AI.html';
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            UI.toast('File aplikasi "KEPALA SEKOLAH MASTER AI.html" berhasil diunduh!', 'success');
          } catch (err) {
            UI.toast('Gagal mengunduh file aplikasi: ' + err.message, 'error');
          }
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

    /* =========================================================================
       7. INISIALISASI APLIKASI
    ========================================================================= */
    window.addEventListener('DOMContentLoaded', () => {
      try {
        App.init();
      } catch (e) {
        console.error('Gagal memuat aplikasi:', e);
      }
    });
  </script>
</body>
</html>
