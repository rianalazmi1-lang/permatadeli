<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard PERMATA DELI - UPT Puskesmas Dalu Sepuluh</title>
    
    <!-- Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                    colors: {
                        primary: '#163B65',
                        bgLight: '#F4F6F9',
                        infoBg: '#EAF2FA',
                        textDark: '#374151',
                        risk: {
                            low: '#2E8B57',
                            lowBg: '#E8F5E9',
                            med: '#F2C94C',
                            medBg: '#FFF8DB',
                            high: '#D64545',
                            highBg: '#FCE8E8',
                        }
                    }
                }
            }
        }
    </script>

    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <!-- SheetJS for Excel Export -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    
    <!-- jsPDF & AutoTable for PDF Export -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.31/jspdf.plugin.autotable.min.js"></script>

    <style>
        body { font-family: 'Inter', sans-serif; background-color: #F4F6F9; color: #374151; }
        .sidebar-item { transition: all 0.2s; border-left: 4px solid transparent; }
        .sidebar-item.active { background-color: rgba(255,255,255,0.1); border-left-color: #F2C94C; font-weight: 600; }
        .sidebar-item:hover:not(.active) { background-color: rgba(255,255,255,0.05); }
        .view-section { display: none; }
        .view-section.active { display: block; }
        
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #c1c1c1; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #a8a8a8; }
        
        /* Notification Toast */
        #toast {
            visibility: hidden; min-width: 250px; background-color: #2E8B57; color: #fff;
            text-align: center; border-radius: 4px; padding: 16px; position: fixed;
            z-index: 50; right: 30px; bottom: 30px; font-size: 14px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        #toast.show { visibility: visible; animation: fadein 0.5s, fadeout 0.5s 2.5s; }
        @keyframes fadein { from {bottom: 0; opacity: 0;} to {bottom: 30px; opacity: 1;} }
        @keyframes fadeout { from {bottom: 30px; opacity: 1;} to {bottom: 0; opacity: 0;} }
    </style>
</head>
<body class="flex h-screen overflow-hidden text-textDark">

    <!-- Sidebar -->
    <aside class="w-64 bg-primary text-white flex flex-col h-full flex-shrink-0 transition-transform duration-300 md:translate-x-0 absolute md:relative z-40 -translate-x-full" id="sidebar">
        <div class="p-4 border-b border-blue-800 flex items-center justify-between">
            <div>
                <h1 class="text-xl font-bold tracking-wider">PERMATA DELI</h1>
                <p class="text-xs text-gray-300 mt-1">Puskesmas Dalu Sepuluh</p>
            </div>
            <button class="md:hidden text-white" onclick="toggleSidebar()"><i class="fas fa-times"></i></button>
        </div>
        <div class="overflow-y-auto flex-1 py-4 text-sm">
            <a href="#" onclick="navigate('dashboard')" id="nav-dashboard" class="sidebar-item active flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-chart-pie w-6"></i> Dashboard Utama
            </a>
            <a href="#" onclick="navigate('data-keluarga')" id="nav-data-keluarga" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-users w-6"></i> Data Keluarga
            </a>
            <a href="#" onclick="navigate('form-pendataan')" id="nav-form-pendataan" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-clipboard-list w-6"></i> Pendataan & Kunjungan
            </a>
            <a href="#" onclick="navigate('skrining')" id="nav-skrining" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-stethoscope w-6"></i> Skrining Kesehatan
            </a>
            <a href="#" onclick="navigate('pemetaan')" id="nav-pemetaan" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-map-marked-alt w-6"></i> Pemetaan Risiko
            </a>
            <a href="#" onclick="navigate('rujukan')" id="nav-rujukan" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-hospital-user w-6"></i> Penanganan & Rujukan
            </a>
            <a href="#" onclick="navigate('lintas-sektor')" id="nav-lintas-sektor" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-hands-helping w-6"></i> Intervensi Lintas Sektor
            </a>
            <a href="#" onclick="navigate('monitoring')" id="nav-monitoring" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-chart-line w-6"></i> Monitoring Berkala
            </a>
            <a href="#" onclick="navigate('desa-binaan')" id="nav-desa-binaan" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-city w-6"></i> Data Desa Binaan
            </a>
            <a href="#" onclick="navigate('laporan')" id="nav-laporan" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-file-excel w-6"></i> Laporan & Export
            </a>
            <a href="#" onclick="navigate('pengaturan')" id="nav-pengaturan" class="sidebar-item flex items-center py-3 px-4 text-gray-100">
                <i class="fas fa-cog w-6"></i> Pengaturan
            </a>
        </div>
        <div class="p-4 border-t border-blue-800 text-xs text-center text-gray-400">
            &copy; 2026 Kab. Deli Serdang
        </div>
    </aside>

    <!-- Main Content -->
    <div class="flex-1 flex flex-col h-full overflow-hidden w-full relative">
        <!-- Header -->
        <header class="bg-white shadow-sm border-b px-6 py-3 flex justify-between items-center shrink-0">
            <div class="flex items-center gap-4">
                <button class="md:hidden text-gray-600 focus:outline-none" onclick="toggleSidebar()">
                    <i class="fas fa-bars text-xl"></i>
                </button>
                <div class="flex items-center gap-3">
                    <img src="https://placehold.co/40x40/163B65/FFFFFF?text=DS" alt="Logo Deli Serdang" class="w-10 h-10 rounded-full">
                    <div>
                        <h2 class="text-sm font-bold text-primary md:text-base hidden sm:block">UPT PUSKESMAS DALU SEPULUH</h2>
                        <p class="text-xs text-gray-500 hidden sm:block">Monitoring Skrining & Risiko Keluarga 2026</p>
                    </div>
                </div>
            </div>
            
            <div class="flex items-center gap-4">
                <span id="cloud-status" class="text-xs bg-yellow-100 text-yellow-800 px-2.5 py-1 rounded-full border border-yellow-300 font-semibold flex items-center gap-1.5">
                    <i class="fas fa-spinner fa-spin text-xs"></i> Menghubungkan Cloud...
                </span>
                <div class="text-right hidden md:block">
                    <p class="text-sm font-semibold"> Admin Puskesmas </p>
                    <p class="text-xs text-gray-500" id="current-date"></p>
                </div>
                <div class="h-8 w-8 rounded-full bg-primary text-white flex items-center justify-center font-bold">
                    A
                </div>
                <button class="text-gray-400 hover:text-red-500 transition-colors" title="Logout">
                    <i class="fas fa-sign-out-alt text-lg"></i>
                </button>
            </div>
        </header>

        <!-- Dynamic Views -->
        <main class="flex-1 overflow-y-auto p-4 md:p-6 bg-bgLight">
            
            <!-- VIEW: DASHBOARD -->
            <section id="view-dashboard" class="view-section active">
                <div class="mb-6">
                    <h2 class="text-2xl font-bold text-gray-800">Dashboard Utama</h2>
                    <p class="text-sm text-gray-500">Ringkasan capaian pendataan, kunjungan, dan status risiko keluarga PERMATA DELI (Tersinkronisasi Cloud).</p>
                </div>

                <!-- Stats Cards -->
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mb-6">
                    <div class="bg-white rounded-lg p-4 shadow-sm border-l-4 border-primary flex items-center">
                        <div class="p-3 bg-infoBg rounded-full text-primary mr-4"><i class="fas fa-map-marked-alt text-xl"></i></div>
                        <div><p class="text-xs text-gray-500 font-semibold uppercase">Total Desa Binaan</p><h3 class="text-2xl font-bold">6</h3></div>
                    </div>
                    <div class="bg-white rounded-lg p-4 shadow-sm border-l-4 border-blue-500 flex items-center">
                        <div class="p-3 bg-blue-50 rounded-full text-blue-500 mr-4"><i class="fas fa-home text-xl"></i></div>
                        <div><p class="text-xs text-gray-500 font-semibold uppercase">Total Keluarga Terdata</p><h3 class="text-2xl font-bold" id="stat-terdata">0</h3></div>
                    </div>
                    <div class="bg-white rounded-lg p-4 shadow-sm border-l-4 border-purple-500 flex items-center">
                        <div class="p-3 bg-purple-50 rounded-full text-purple-500 mr-4"><i class="fas fa-walking text-xl"></i></div>
                        <div><p class="text-xs text-gray-500 font-semibold uppercase">Kunjungan Rumah</p><h3 class="text-2xl font-bold" id="stat-kunjungan">0</h3></div>
                    </div>
                    <div class="bg-white rounded-lg p-4 shadow-sm border-l-4 border-gray-400 flex items-center">
                        <div class="p-3 bg-gray-100 rounded-full text-gray-500 mr-4"><i class="fas fa-clock text-xl"></i></div>
                        <div><p class="text-xs text-gray-500 font-semibold uppercase">Belum Dikunjungi</p><h3 class="text-2xl font-bold" id="stat-belum-kunjungan">0</h3></div>
                    </div>
                </div>

                <!-- Risk Stats -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                    <div class="bg-white rounded-lg p-4 shadow-sm border-l-4 border-risk-low">
                        <div class="flex justify-between items-start">
                            <div>
                                <p class="text-sm text-gray-500 font-semibold mb-1">Risiko Rendah</p>
                                <h3 class="text-3xl font-bold text-risk-low" id="stat-hijau">0</h3>
                                <p class="text-xs text-gray-400 mt-2">Tidak ditemukan masalah primer</p>
                            </div>
                            <div class="p-2 bg-risk-lowBg rounded text-risk-low"><i class="fas fa-shield-alt text-2xl"></i></div>
                        </div>
                    </div>
                    <div class="bg-white rounded-lg p-4 shadow-sm border-l-4 border-risk-med">
                        <div class="flex justify-between items-start">
                            <div>
                                <p class="text-sm text-gray-500 font-semibold mb-1">Risiko Sedang</p>
                                <h3 class="text-3xl font-bold text-risk-med" id="stat-kuning">0</h3>
                                <p class="text-xs text-gray-400 mt-2">Ditemukan faktor risiko (Rokok, Lansia, dll)</p>
                            </div>
                            <div class="p-2 bg-risk-medBg rounded text-risk-med"><i class="fas fa-exclamation-triangle text-2xl"></i></div>
                        </div>
                    </div>
                    <div class="bg-white rounded-lg p-4 shadow-sm border-l-4 border-risk-high">
                        <div class="flex justify-between items-start">
                            <div>
                                <p class="text-sm text-gray-500 font-semibold mb-1">Risiko Tinggi</p>
                                <h3 class="text-3xl font-bold text-risk-high" id="stat-merah">0</h3>
                                <p class="text-xs text-gray-400 mt-2">Kasus aktif (Stunting, HT, DM, TB, dll)</p>
                            </div>
                            <div class="p-2 bg-risk-highBg rounded text-risk-high"><i class="fas fa-heartbeat text-2xl"></i></div>
                        </div>
                    </div>
                </div>

                <!-- Charts -->
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-6">
                    <div class="bg-white p-5 rounded-lg shadow-sm">
                        <h3 class="font-semibold text-gray-700 mb-4">Distribusi Keluarga Berdasarkan Risiko</h3>
                        <div class="relative h-64 w-full flex justify-center">
                            <canvas id="chartRisk"></canvas>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-lg shadow-sm">
                        <h3 class="font-semibold text-gray-700 mb-4">Capaian Pendataan per Desa Binaan</h3>
                        <div class="relative h-64 w-full">
                            <canvas id="chartDesa"></canvas>
                        </div>
                    </div>
                </div>
            </section>

            <!-- VIEW: DATA KELUARGA -->
            <section id="view-data-keluarga" class="view-section">
                <div class="mb-6 flex flex-col md:flex-row md:justify-between md:items-end gap-4">
                    <div>
                        <h2 class="text-2xl font-bold text-gray-800">Data Keluarga</h2>
                        <p class="text-sm text-gray-500">Daftar keluarga yang telah didata dan diskrining (Penyimpanan Cloud Aktif).</p>
                    </div>
                    <button onclick="navigate('form-pendataan')" class="bg-primary hover:bg-blue-800 text-white px-4 py-2 rounded-lg shadow text-sm font-medium transition flex items-center gap-2">
                        <i class="fas fa-plus"></i> Tambah Data Baru
                    </button>
                </div>

                <!-- Filters -->
                <div class="bg-white p-4 rounded-lg shadow-sm mb-4 flex flex-col md:flex-row gap-4">
                    <div class="flex-1">
                        <input type="text" id="search-nama" oninput="renderTableKeluarga()" placeholder="Cari Nama Kepala Keluarga / NIK..." class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:outline-none focus:border-primary">
                    </div>
                    <div class="w-full md:w-48">
                        <select id="filter-desa" onchange="renderTableKeluarga()" class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:outline-none focus:border-primary">
                            <option value="">Semua Desa</option>
                            <!-- Populated via JS -->
                        </select>
                    </div>
                    <div class="w-full md:w-48">
                        <select id="filter-risiko" onchange="renderTableKeluarga()" class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:outline-none focus:border-primary">
                            <option value="">Semua Risiko</option>
                            <option value="HIJAU">Risiko Rendah (Hijau)</option>
                            <option value="KUNING">Risiko Sedang (Kuning)</option>
                            <option value="MERAH">Risiko Tinggi (Merah)</option>
                        </select>
                    </div>
                    <button onclick="renderTableKeluarga()" class="bg-gray-100 hover:bg-gray-200 text-gray-700 px-4 py-2 rounded border border-gray-300 text-sm font-medium transition">
                        <i class="fas fa-filter"></i> Filter
                    </button>
                </div>

                <!-- Table -->
                <div class="bg-white rounded-lg shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full text-left border-collapse text-sm">
                            <thead class="bg-gray-50 text-gray-600 border-b border-gray-200">
                                <tr>
                                    <th class="p-3 font-semibold">No</th>
                                    <th class="p-3 font-semibold">Kepala Keluarga</th>
                                    <th class="p-3 font-semibold">NIK</th>
                                    <th class="p-3 font-semibold">Desa / Dusun</th>
                                    <th class="p-3 font-semibold">Tgl Pendataan</th>
                                    <th class="p-3 font-semibold text-center">Risiko</th>
                                    <th class="p-3 font-semibold text-center">Status</th>
                                    <th class="p-3 font-semibold text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="tbody-keluarga" class="divide-y divide-gray-100">
                                <!-- Data rendered by JS -->
                            </tbody>
                        </table>
                    </div>
                    <div class="p-4 border-t border-gray-100 text-xs text-gray-500 flex justify-between items-center">
                        <span id="table-info">Menampilkan 0 data</span>
                    </div>
                </div>
            </section>

            <!-- VIEW: FORM PENDATAAN & SKRINING -->
            <section id="view-form-pendataan" class="view-section">
                <div class="mb-6 flex items-center justify-between">
                    <div>
                        <h2 class="text-2xl font-bold text-gray-800">Form Pendataan & Skrining</h2>
                        <p class="text-sm text-gray-500">Input data identitas dan hasil skrining untuk disimpan secara cloud.</p>
                    </div>
                    <button onclick="navigate('data-keluarga')" class="text-gray-500 hover:text-primary text-sm font-medium">
                        <i class="fas fa-arrow-left mr-1"></i> Kembali
                    </button>
                </div>

                <div class="bg-white rounded-lg shadow-sm">
                    <form id="formKeluarga" onsubmit="handleFormSubmit(event)">
                        <!-- Section A: Identitas -->
                        <div class="p-5 border-b border-gray-100">
                            <h3 class="text-lg font-semibold text-primary mb-4 border-l-4 border-primary pl-2">A. Identitas Keluarga</h3>
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                <div>
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Nama Kepala Keluarga *</label>
                                    <input type="text" id="f-nama" required class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary focus:ring-1 focus:ring-primary outline-none">
                                </div>
                                <div>
                                    <label class="block text-xs font-medium text-gray-700 mb-1">NIK KK *</label>
                                    <input type="text" id="f-nik" required pattern="[0-9]{16}" title="NIK harus 16 digit angka" class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none">
                                </div>
                                <div>
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Desa Binaan *</label>
                                    <select id="f-desa" required class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none">
                                        <!-- JS populates -->
                                    </select>
                                </div>
                                <div>
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Dusun / RT / RW</label>
                                    <input type="text" id="f-dusun" class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none">
                                </div>
                                <div class="md:col-span-2">
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Alamat Lengkap</label>
                                    <textarea id="f-alamat" rows="2" class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none"></textarea>
                                </div>
                            </div>
                        </div>

                        <!-- Section B: Skrining -->
                        <div class="p-5 border-b border-gray-100 bg-gray-50">
                            <h3 class="text-lg font-semibold text-primary mb-4 border-l-4 border-primary pl-2">B. Skrining Risiko Kesehatan</h3>
                            <p class="text-xs text-gray-500 mb-4">Centang kondisi yang ditemukan pada anggota keluarga. Kategori risiko akan dikalkulasi otomatis.</p>
                            
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                <!-- Faktor Risiko Sedang (KUNING) -->
                                <div class="bg-white p-4 rounded border border-gray-200">
                                    <h4 class="font-semibold text-risk-med mb-3 flex items-center gap-2"><i class="fas fa-exclamation-triangle"></i> Faktor Risiko (Kuning)</h4>
                                    <div class="space-y-2">
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-kuning w-4 h-4 text-risk-med" value="Ada anggota keluarga merokok"> Ada anggota keluarga merokok</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-kuning w-4 h-4 text-risk-med" value="Anggota keluarga Obesitas"> Anggota keluarga Obesitas</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-kuning w-4 h-4 text-risk-med" value="Terdapat Ibu Hamil (Normal)"> Terdapat Ibu Hamil (Normal)</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-kuning w-4 h-4 text-risk-med" value="Terdapat Lansia"> Terdapat Lansia</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-kuning w-4 h-4 text-risk-med" value="Terdapat Balita"> Terdapat Balita</label>
                                    </div>
                                </div>

                                <!-- Faktor Risiko Tinggi (MERAH) -->
                                <div class="bg-white p-4 rounded border border-gray-200">
                                    <h4 class="font-semibold text-risk-high mb-3 flex items-center gap-2"><i class="fas fa-heartbeat"></i> Kasus Aktif / Risiko Tinggi (Merah)</h4>
                                    <div class="space-y-2">
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-merah w-4 h-4 text-risk-high" value="Balita Stunting / Wasting"> Balita Stunting / Wasting</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-merah w-4 h-4 text-risk-high" value="Hipertensi Tidak Terkontrol"> Hipertensi Tidak Terkontrol</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-merah w-4 h-4 text-risk-high" value="Diabetes Melitus Tidak Terkontrol"> Diabetes Melitus Tidak Terkontrol</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-merah w-4 h-4 text-risk-high" value="TB Paru Aktif"> TB Paru Aktif</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-merah w-4 h-4 text-risk-high" value="Suspek/Positif HIV atau Hepatitis"> Suspek/Positif HIV atau Hepatitis</label>
                                        <label class="flex items-center gap-2 text-sm cursor-pointer"><input type="checkbox" class="risk-merah w-4 h-4 text-risk-high" value="Ibu Hamil KEK / Risiko Tinggi"> Ibu Hamil KEK / Risiko Tinggi</label>
                                    </div>
                                </div>
                            </div>

                            <div class="mt-4 p-4 rounded bg-blue-50 border border-blue-100 flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-semibold text-gray-700">Prediksi Risiko Sistem:</p>
                                    <p class="text-xs text-gray-500">Centang opsi di atas untuk melihat perubahan otomatis.</p>
                                </div>
                                <div id="live-risk-badge" class="px-4 py-2 rounded-full font-bold text-sm bg-risk-lowBg text-risk-low border border-risk-low">
                                    HIJAU - RISIKO RENDAH
                                </div>
                            </div>
                        </div>

                        <!-- Section C: Kunjungan -->
                        <div class="p-5">
                            <h3 class="text-lg font-semibold text-primary mb-4 border-l-4 border-primary pl-2">C. Data Kunjungan & Tindak Lanjut</h3>
                            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                                <div>
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Tanggal Kunjungan *</label>
                                    <input type="date" id="f-tgl" required class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none">
                                </div>
                                <div>
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Petugas/Kader *</label>
                                    <input type="text" id="f-petugas" required class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none">
                                </div>
                                <div>
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Status Kunjungan *</label>
                                    <select id="f-status" required class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none">
                                        <option value="Sudah Dikunjungi">Sudah Dikunjungi</option>
                                        <option value="Perlu Kunjungan Ulang">Perlu Kunjungan Ulang</option>
                                    </select>
                                </div>
                                <div class="md:col-span-3">
                                    <label class="block text-xs font-medium text-gray-700 mb-1">Catatan Intervensi / Rujukan</label>
                                    <textarea id="f-catatan" rows="2" placeholder="Contoh: Edukasi PHBS diberikan. Dirujuk ke Pustu besok." class="w-full border border-gray-300 rounded px-3 py-2 text-sm focus:border-primary outline-none"></textarea>
                                </div>
                            </div>
                        </div>

                        <div class="p-5 bg-gray-50 border-t border-gray-200 flex justify-end gap-3">
                            <button type="button" onclick="document.getElementById('formKeluarga').reset(); updateLiveRisk();" class="px-4 py-2 border border-gray-300 text-gray-700 rounded hover:bg-gray-100 text-sm font-medium transition">Reset</button>
                            <button type="submit" id="btn-submit" class="px-6 py-2 bg-primary text-white rounded hover:bg-blue-800 text-sm font-medium transition shadow">Simpan ke Cloud</button>
                        </div>
                    </form>
                </div>
            </section>

            <!-- VIEW: DESA BINAAN -->
            <section id="view-desa-binaan" class="view-section">
                <div class="mb-6 flex flex-col md:flex-row md:justify-between md:items-end gap-4">
                    <div>
                        <h2 class="text-2xl font-bold text-gray-800">Data Desa Binaan</h2>
                        <p class="text-sm text-gray-500">Capaian kinerja pendataan per desa berdasarkan SK PERMATA DELI.</p>
                    </div>
                    <div class="flex gap-2">
                        <button onclick="exportToExcel('table-desa', 'Data_Desa_Binaan_PERMATA_DELI')" class="bg-green-600 hover:bg-green-700 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-excel"></i> Excel
                        </button>
                        <button onclick="exportToPDF('table-desa', 'Laporan Data Desa Binaan PERMATA DELI', 'Data_Desa_Binaan')" class="bg-red-500 hover:bg-red-600 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-pdf"></i> PDF
                        </button>
                    </div>
                </div>
                <div class="bg-white rounded-lg shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table id="table-desa" class="w-full text-left border-collapse text-sm">
                            <thead class="bg-primary text-white border-b border-primary">
                                <tr>
                                    <th class="p-3 font-semibold text-center w-12">No</th>
                                    <th class="p-3 font-semibold">Nama Desa Binaan</th>
                                    <th class="p-3 font-semibold text-center">Target KK</th>
                                    <th class="p-3 font-semibold text-center">KK Terdata</th>
                                    <th class="p-3 font-semibold text-center">Belum Terdata</th>
                                    <th class="p-3 font-semibold text-center">Capaian (%)</th>
                                    <th class="p-3 font-semibold">Penanggung Jawab</th>
                                </tr>
                            </thead>
                            <tbody id="tbody-desa" class="divide-y divide-gray-100">
                                <!-- Populated via JS -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- VIEW: PEMETAAN RISIKO -->
            <section id="view-pemetaan" class="view-section">
                <div class="mb-6 flex flex-col md:flex-row md:justify-between md:items-end gap-4">
                    <div>
                        <h2 class="text-2xl font-bold text-gray-800">Pemetaan Risiko per Desa</h2>
                        <p class="text-sm text-gray-500">Agregasi jumlah keluarga berdasarkan tingkat risiko di masing-masing desa binaan.</p>
                    </div>
                    <div class="flex gap-2">
                        <button onclick="exportToExcel('table-pemetaan', 'Pemetaan_Risiko_PERMATA_DELI')" class="bg-green-600 hover:bg-green-700 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-excel"></i> Excel
                        </button>
                        <button onclick="exportToPDF('table-pemetaan', 'Laporan Pemetaan Risiko Desa', 'Pemetaan_Risiko')" class="bg-red-500 hover:bg-red-600 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-pdf"></i> PDF
                        </button>
                    </div>
                </div>
                <div class="bg-white rounded-lg shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table id="table-pemetaan" class="w-full text-left border-collapse text-sm">
                            <thead class="bg-gray-50 text-gray-600 border-b border-gray-200">
                                <tr>
                                    <th class="p-3 font-semibold text-center">No</th>
                                    <th class="p-3 font-semibold">Nama Desa Binaan</th>
                                    <th class="p-3 font-semibold text-center">Total Terdata</th>
                                    <th class="p-3 font-semibold text-center bg-risk-lowBg text-risk-low">Risiko Rendah (Hijau)</th>
                                    <th class="p-3 font-semibold text-center bg-risk-medBg text-risk-med">Risiko Sedang (Kuning)</th>
                                    <th class="p-3 font-semibold text-center bg-risk-highBg text-risk-high">Risiko Tinggi (Merah)</th>
                                </tr>
                            </thead>
                            <tbody id="tbody-pemetaan" class="divide-y divide-gray-100">
                                <!-- Populated via JS -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- VIEW: PENANGANAN & RUJUKAN -->
            <section id="view-rujukan" class="view-section">
                <div class="mb-6 flex flex-col md:flex-row md:justify-between md:items-end gap-4">
                    <div>
                        <h2 class="text-2xl font-bold text-gray-800">Data Penanganan & Rujukan</h2>
                        <p class="text-sm text-gray-500">Daftar keluarga kategori Risiko Tinggi (Merah) yang memerlukan tindak lanjut.</p>
                    </div>
                    <div class="flex gap-2">
                        <button onclick="exportToExcel('table-rujukan', 'Data_Rujukan_PERMATA_DELI')" class="bg-green-600 hover:bg-green-700 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-excel"></i> Excel
                        </button>
                        <button onclick="exportToPDF('table-rujukan', 'Data Rujukan Keluarga Risiko Tinggi', 'Data_Rujukan')" class="bg-red-500 hover:bg-red-600 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-pdf"></i> PDF
                        </button>
                    </div>
                </div>
                <div class="bg-white rounded-lg shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table id="table-rujukan" class="w-full text-left border-collapse text-sm">
                            <thead class="bg-risk-highBg text-risk-high border-b border-red-200">
                                <tr>
                                    <th class="p-3 font-semibold text-center">No</th>
                                    <th class="p-3 font-semibold">Nama Keluarga</th>
                                    <th class="p-3 font-semibold">Desa / Dusun</th>
                                    <th class="p-3 font-semibold">Faktor Risiko (Merah)</th>
                                    <th class="p-3 font-semibold">Status Penanganan</th>
                                </tr>
                            </thead>
                            <tbody id="tbody-rujukan" class="divide-y divide-gray-100">
                                <!-- Populated via JS -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- VIEW: SKRINING KESEHATAN -->
            <section id="view-skrining" class="view-section">
                <div class="mb-6 flex flex-col md:flex-row md:justify-between md:items-end gap-4">
                    <div>
                        <h2 class="text-2xl font-bold text-gray-800">Skrining Kesehatan Terintegrasi</h2>
                        <p class="text-sm text-gray-500">Formulir dan riwayat skrining kesehatan keluarga tersinkronisasi langsung dengan pendataan.</p>
                    </div>
                    <div class="flex gap-2">
                        <button onclick="navigate('form-pendataan')" class="bg-primary hover:bg-blue-800 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-plus"></i> Input Skrining Baru
                        </button>
                        <button onclick="exportToExcel('table-skrining', 'Data_Skrining_PERMATA_DELI')" class="bg-green-600 hover:bg-green-700 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-excel"></i> Excel
                        </button>
                        <button onclick="exportToPDF('table-skrining', 'Data Skrining Kesehatan Keluarga', 'Data_Skrining')" class="bg-red-500 hover:bg-red-600 text-white px-3 py-2 rounded shadow text-sm font-medium transition flex items-center gap-2">
                            <i class="fas fa-file-pdf"></i> PDF
                        </button>
                    </div>
                </div>

                <!-- Integrated Quick Screening Summary / Filters -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                    <div class="bg-white p-4 rounded-lg shadow-sm border-l-4 border-risk-low flex items-center justify-between">
                        <div>
                            <p class="text-xs text-gray-500 font-semibold uppercase">Skrining Risiko Rendah</p>
                            <h3 class="text-2xl font-bold text-risk-low" id="skrining-stat-hijau">0</h3>
                        </div>
                        <div class="p-3 bg-risk-lowBg rounded-full text-risk-low"><i class="fas fa-shield-alt text-lg"></i></div>
                    </div>
                    <div class="bg-white p-4 rounded-lg shadow-sm border-l-4 border-risk-med flex items-center justify-between">
                        <div>
                            <p class="text-xs text-gray-500 font-semibold uppercase">Skrining Risiko Sedang</p>
                            <h3 class="text-2xl font-bold text-risk-med" id="skrining-stat-kuning">0</h3>
                        </div>
                        <div class="p-3 bg-risk-medBg rounded-full text-risk-med"><i class="fas fa-exclamation-triangle text-lg"></i></div>
                    </div>
                    <div class="bg-white p-4 rounded-lg shadow-sm border-l-4 border-risk-high flex items-center justify-between">
                        <div>
                            <p class="text-xs text-gray-500 font-semibold uppercase">Skrining Risiko Tinggi</p>
                            <h3 class="text-2xl font-bold text-risk-high" id="skrining-stat-merah">0</h3>
                        </div>
                        <div class="p-3 bg-risk-highBg rounded-full text-risk-high"><i class="fas fa-heartbeat text-lg"></i></div>
                    </div>
                </div>

                <div class="bg-white rounded-lg shadow-sm overflow-hidden">
                    <div class="p-4 border-b border-gray-100 flex justify-between items-center bg-gray-50">
                        <h3 class="font-bold text-gray-700 text-sm"><i class="fas fa-stethoscope mr-2 text-primary"></i> Daftar Hasil Skrining Keluarga Terintegrasi</h3>
                        <input type="text" id="search-skrining" oninput="renderTableSkrining()" placeholder="Cari Kepala Keluarga / Faktor Risiko..." class="border border-gray-300 rounded px-3 py-1.5 text-sm w-64 focus:outline-none focus:border-primary">
                    </div>
                    <div class="overflow-x-auto">
                        <table id="table-skrining" class="w-full text-left border-collapse text-sm">
                            <thead class="bg-gray-100 text-gray-600 border-b border-gray-200">
                                <tr>
                                    <th class="p-3 font-semibold text-center w-12">No</th>
                                    <th class="p-3 font-semibold">Kepala Keluarga / NIK</th>
                                    <th class="p-3 font-semibold">Desa / Dusun</th>
                                    <th class="p-3 font-semibold">Faktor Risiko Ditemukan</th>
                                    <th class="p-3 font-semibold text-center">Kategori Final</th>
                                    <th class="p-3 font-semibold text-center">Tgl Skrining</th>
                                </tr>
                            </thead>
                            <tbody id="tbody-skrining" class="divide-y divide-gray-100">
                                <!-- Populated via JS -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- VIEW: LAPORAN & EXPORT -->
            <section id="view-laporan" class="view-section">
                <div class="mb-6">
                    <h2 class="text-2xl font-bold text-gray-800">Laporan & Export Data</h2>
                    <p class="text-sm text-gray-500">Pusat unduhan laporan sistem monitoring PERMATA DELI ke format Excel dan PDF.</p>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                    <!-- Laporan Desa -->
                    <div class="bg-white p-5 rounded-lg shadow-sm border border-gray-100">
                        <div class="flex items-center gap-3 mb-4">
                            <div class="p-3 bg-blue-50 text-primary rounded-lg"><i class="fas fa-users text-xl"></i></div>
                            <h3 class="font-bold text-gray-700">Data Desa Binaan</h3>
                        </div>
                        <p class="text-xs text-gray-500 mb-4">Rekapitulasi target dan capaian pendataan per desa binaan sesuai SK.</p>
                        <div class="flex gap-2">
                            <button onclick="exportToExcel('table-desa', 'Laporan_Desa_Binaan')" class="flex-1 bg-green-50 hover:bg-green-100 text-green-700 px-3 py-2 rounded text-sm font-medium transition text-center border border-green-200"><i class="fas fa-file-excel mr-1"></i> Excel</button>
                            <button onclick="exportToPDF('table-desa', 'Laporan Data Desa Binaan', 'Laporan_Desa_Binaan')" class="flex-1 bg-red-50 hover:bg-red-100 text-red-700 px-3 py-2 rounded text-sm font-medium transition text-center border border-red-200"><i class="fas fa-file-pdf mr-1"></i> PDF</button>
                        </div>
                    </div>
                    <!-- Laporan Pemetaan -->
                    <div class="bg-white p-5 rounded-lg shadow-sm border border-gray-100">
                        <div class="flex items-center gap-3 mb-4">
                            <div class="p-3 bg-blue-50 text-primary rounded-lg"><i class="fas fa-map-marked-alt text-xl"></i></div>
                            <h3 class="font-bold text-gray-700">Pemetaan Risiko</h3>
                        </div>
                        <p class="text-xs text-gray-500 mb-4">Agregasi jumlah keluarga per kategori risiko (Hijau, Kuning, Merah).</p>
                        <div class="flex gap-2">
                            <button onclick="exportToExcel('table-pemetaan', 'Laporan_Pemetaan_Risiko')" class="flex-1 bg-green-50 hover:bg-green-100 text-green-700 px-3 py-2 rounded text-sm font-medium transition text-center border border-green-200"><i class="fas fa-file-excel mr-1"></i> Excel</button>
                            <button onclick="exportToPDF('table-pemetaan', 'Laporan Pemetaan Risiko', 'Laporan_Pemetaan_Risiko')" class="flex-1 bg-red-50 hover:bg-red-100 text-red-700 px-3 py-2 rounded text-sm font-medium transition text-center border border-red-200"><i class="fas fa-file-pdf mr-1"></i> PDF</button>
                        </div>
                    </div>
                    <!-- Laporan Rujukan -->
                    <div class="bg-white p-5 rounded-lg shadow-sm border border-gray-100">
                        <div class="flex items-center gap-3 mb-4">
                            <div class="p-3 bg-red-50 text-red-500 rounded-lg"><i class="fas fa-heartbeat text-xl"></i></div>
                            <h3 class="font-bold text-gray-700">Data Rujukan</h3>
                        </div>
                        <p class="text-xs text-gray-500 mb-4">Daftar keluarga yang memerlukan intervensi lanjutan atau rujukan medis.</p>
                        <div class="flex gap-2">
                            <button onclick="exportToExcel('table-rujukan', 'Laporan_Rujukan')" class="flex-1 bg-green-50 hover:bg-green-100 text-green-700 px-3 py-2 rounded text-sm font-medium transition text-center border border-green-200"><i class="fas fa-file-excel mr-1"></i> Excel</button>
                            <button onclick="exportToPDF('table-rujukan', 'Laporan Penanganan & Rujukan', 'Laporan_Rujukan')" class="flex-1 bg-red-50 hover:bg-red-100 text-red-700 px-3 py-2 rounded text-sm font-medium transition text-center border border-red-200"><i class="fas fa-file-pdf mr-1"></i> PDF</button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- VIEW: PENGATURAN -->
            <section id="view-pengaturan" class="view-section">
                <div class="mb-6">
                    <h2 class="text-2xl font-bold text-gray-800">Pengaturan Sistem (Cloud Admin)</h2>
                    <p class="text-sm text-gray-500">Konfigurasi sinkronisasi cloud Puskesmas Dalu Sepuluh.</p>
                </div>
                
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                    <div class="bg-white p-6 rounded-lg shadow-sm">
                        <h3 class="font-bold text-primary mb-4 border-b pb-2"><i class="fas fa-cloud mr-2"></i>Status Koneksi Database</h3>
                        <div class="space-y-3 text-sm">
                            <div class="p-3 bg-green-50 text-green-700 rounded border border-green-200 flex items-center justify-between">
                                <span><i class="fas fa-check-circle mr-2"></i> Cloud Firestore Terhubung</span>
                                <span class="font-mono text-xs bg-white px-2 py-1 rounded border">Active</span>
                            </div>
                            <p class="text-xs text-gray-500">Semua kader dan petugas dapat memasukkan data secara bersamaan dan data akan langsung diperbarui di perangkat lain secara real-time.</p>
                        </div>
                    </div>

                    <div class="bg-white p-6 rounded-lg shadow-sm">
                        <h3 class="font-bold text-red-600 mb-4 border-b pb-2"><i class="fas fa-database mr-2"></i>Manajemen Data Cloud</h3>
                        <div class="space-y-4">
                            <p class="text-sm text-gray-600">Fitur pemeliharaan data server pusat Puskesmas.</p>
                            
                            <div class="flex items-center justify-between p-4 border border-red-200 bg-red-50 rounded">
                                <div>
                                    <h4 class="font-semibold text-red-700 text-sm">Reset Seluruh Data Cloud</h4>
                                    <p class="text-xs text-red-600 mt-1">Hapus semua data keluarga dari database cloud.</p>
                                </div>
                                <button onclick="resetCloudData()" class="px-3 py-2 bg-red-600 hover:bg-red-700 text-white text-xs rounded font-medium transition shadow-sm">Kosongkan Cloud</button>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Placeholder for other sections -->
            <section id="view-placeholder" class="view-section">
                <div class="flex flex-col items-center justify-center h-64 text-center">
                    <div class="w-20 h-20 bg-gray-200 rounded-full flex items-center justify-center text-gray-400 text-3xl mb-4">
                        <i class="fas fa-tools"></i>
                    </div>
                    <h2 class="text-xl font-bold text-gray-700 mb-2" id="placeholder-title">Fitur Dalam Pengembangan</h2>
                    <p class="text-gray-500 text-sm max-w-md">Halaman ini adalah bagian dari sistem utama PERMATA DELI.</p>
                    <button onclick="navigate('dashboard')" class="mt-6 px-4 py-2 bg-primary text-white rounded text-sm hover:bg-blue-800 transition">Kembali ke Dashboard</button>
                </div>
            </section>

        </main>
    </div>

    <!-- Notification Toast -->
    <div id="toast">Notifikasi pesan.</div>

    <!-- Firebase SDK Imports -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, deleteDoc, doc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // --- GLOBAL APP STATE & FIREBASE INIT ---
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'permata-deli-puskesmas-dalu-sepuluh';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {
            apiKey: "demo-key", authDomain: "demo.firebaseapp.com", projectId: "demo-project"
        };

        let app, db, auth;
        let families = [];
        let currentChartRisk = null;
        let currentChartDesa = null;

        const listDesa = [
            { id: 1, nama: "Bangun Sari", target: 850, pj: "Tim A (Dr. Andi)" },
            { id: 2, nama: "Bangun Sari Baru", target: 720, pj: "Tim B (Dr. Budi)" },
            { id: 3, nama: "Buntu Bedimbar", target: 630, pj: "Tim C (Dr. Citra)" },
            { id: 4, nama: "Telaga Sari Dalu Sepuluh A", target: 540, pj: "Tim D (Dr. Dewi)" },
            { id: 5, nama: "Dagang Kelambir Dalu Sepuluh B", target: 600, pj: "Tim E (Dr. Eka)" },
            { id: 6, nama: "Wonosari Perdamean Penara", target: 480, pj: "Tim F (Dr. Fajar)" }
        ];

        async function initFirebase() {
            try {
                app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    await signInAnonymously(auth);
                }

                document.getElementById('cloud-status').innerHTML = '<i class="fas fa-cloud text-xs text-green-600"></i> Cloud Sinkron';
                document.getElementById('cloud-status').className = 'text-xs bg-green-100 text-green-800 px-2.5 py-1 rounded-full border border-green-300 font-semibold flex items-center gap-1.5';
                
                setupRealtimeListener();
            } catch (err) {
                console.error("Firebase init error:", err);
                document.getElementById('cloud-status').innerHTML = '<i class="fas fa-exclamation-triangle text-xs text-red-600"></i> Mode Offline';
                document.getElementById('cloud-status').className = 'text-xs bg-red-100 text-red-800 px-2.5 py-1 rounded-full border border-red-300 font-semibold flex items-center gap-1.5';
            }
        }

        function setupRealtimeListener() {
            if (!db) return;
            // RULE 1: Strict Paths
            const colRef = collection(db, 'artifacts', appId, 'public', 'data', 'families');
            
            // RULE 2: No Complex Queries, fetch all and filter in memory
            onSnapshot(colRef, (snapshot) => {
                families = [];
                snapshot.forEach((docSnap) => {
                    families.push({ idDoc: docSnap.id, ...docSnap.data() });
                });
                
                // Refresh all views
                renderDashboard();
                renderTableKeluarga();
                renderTableDesa();
                renderTablePemetaan();
                renderTableRujukan();
                renderTableSkrining();
            }, (error) => {
                console.error("Snapshot error:", error);
            });
        }

        // --- UI & NAVIGATION ---
        window.navigate = function(viewId) {
            if(window.innerWidth < 768 && document.getElementById('sidebar').classList.contains('translate-x-0')) {
                toggleSidebar();
            }

            document.querySelectorAll('.sidebar-item').forEach(el => el.classList.remove('active'));
            document.getElementById('nav-' + viewId)?.classList.add('active');
            document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));

            const mainViews = ['dashboard', 'data-keluarga', 'form-pendataan', 'desa-binaan', 'pemetaan', 'rujukan', 'skrining', 'laporan', 'pengaturan'];
            if(mainViews.includes(viewId)) {
                document.getElementById('view-' + viewId).classList.add('active');
                if(viewId === 'dashboard') renderDashboard();
                else if (viewId === 'data-keluarga') renderTableKeluarga();
                else if (viewId === 'pemetaan') renderTablePemetaan();
                else if (viewId === 'rujukan') renderTableRujukan();
                else if (viewId === 'skrining') renderTableSkrining();
            } else {
                document.getElementById('view-placeholder').classList.add('active');
            }
        }

        window.toggleSidebar = function() {
            const sidebar = document.getElementById('sidebar');
            sidebar.classList.toggle('-translate-x-full');
            sidebar.classList.toggle('translate-x-0');
        }

        window.showToast = function(message, type = 'success') {
            const toast = document.getElementById('toast');
            toast.innerText = message;
            toast.style.backgroundColor = type === 'success' ? '#2E8B57' : '#D64545';
            toast.className = 'show';
            setTimeout(() => { toast.className = toast.className.replace("show", ""); }, 3000);
        }

        // --- DASHBOARD RENDERING ---
        function renderDashboard() {
            const totalData = families.length;
            const hijau = families.filter(f => f.risiko === 'HIJAU').length;
            const kuning = families.filter(f => f.risiko === 'KUNING').length;
            const merah = families.filter(f => f.risiko === 'MERAH').length;

            document.getElementById('stat-terdata').innerText = totalData;
            document.getElementById('stat-kunjungan').innerText = totalData;
            document.getElementById('stat-belum-kunjungan').innerText = Math.max(0, 3870 - totalData);
            document.getElementById('stat-hijau').innerText = hijau;
            document.getElementById('stat-kuning').innerText = kuning;
            document.getElementById('stat-merah').innerText = merah;

            const ctxRisk = document.getElementById('chartRisk').getContext('2d');
            if(currentChartRisk) currentChartRisk.destroy();
            currentChartRisk = new Chart(ctxRisk, {
                type: 'doughnut',
                data: {
                    labels: ['Risiko Rendah', 'Risiko Sedang', 'Risiko Tinggi'],
                    datasets: [{
                        data: [hijau, kuning, merah],
                        backgroundColor: ['#2E8B57', '#F2C94C', '#D64545'],
                        borderWidth: 0
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'bottom' } }, cutout: '65%' }
            });

            const ctxDesa = document.getElementById('chartDesa').getContext('2d');
            let desaLabels = [];
            let desaData = [];
            listDesa.forEach(d => {
                desaLabels.push(d.nama.replace("Dalu Sepuluh", "DS"));
                desaData.push(families.filter(f => f.desa === d.nama).length);
            });

            if(currentChartDesa) currentChartDesa.destroy();
            currentChartDesa = new Chart(ctxDesa, {
                type: 'bar',
                data: {
                    labels: desaLabels,
                    datasets: [{ label: 'Keluarga Terdata', data: desaData, backgroundColor: '#163B65', borderRadius: 4 }]
                },
                options: { responsive: true, maintainAspectRatio: false, scales: { y: { beginAtZero: true } }, plugins: { legend: { display: false } } }
            });
        }

        // --- DATA KELUARGA TABLE ---
        window.renderTableKeluarga = function() {
            const tbody = document.getElementById('tbody-keluarga');
            const filterDesa = document.getElementById('filter-desa').value;
            const filterRisiko = document.getElementById('filter-risiko').value;
            const searchNama = document.getElementById('search-nama').value.toLowerCase();

            let filtered = families.filter(f => {
                const matchDesa = filterDesa === "" || f.desa === filterDesa;
                const matchRisiko = filterRisiko === "" || f.risiko === filterRisiko;
                const matchSearch = (f.namaKK || '').toLowerCase().includes(searchNama) || (f.nik || '').includes(searchNama);
                return matchDesa && matchRisiko && matchSearch;
            });

            tbody.innerHTML = '';
            if(filtered.length === 0) {
                tbody.innerHTML = `<tr><td colspan="8" class="text-center py-8 text-gray-500">Belum ada data keluarga di cloud. Silakan tambah data baru.</td></tr>`;
            } else {
                filtered.forEach((f, index) => {
                    let riskBadge = '';
                    if(f.risiko === 'HIJAU') riskBadge = `<span class="bg-risk-lowBg text-risk-low px-2 py-1 rounded text-xs font-semibold">Rendah</span>`;
                    else if(f.risiko === 'KUNING') riskBadge = `<span class="bg-risk-medBg text-risk-med px-2 py-1 rounded text-xs font-semibold">Sedang</span>`;
                    else riskBadge = `<span class="bg-risk-highBg text-risk-high px-2 py-1 rounded text-xs font-semibold">Tinggi</span>`;

                    const tr = document.createElement('tr');
                    tr.className = "hover:bg-gray-50 transition-colors";
                    tr.innerHTML = `
                        <td class="p-3 border-b border-gray-100">${index + 1}</td>
                        <td class="p-3 border-b border-gray-100 font-medium text-gray-800">${f.namaKK}</td>
                        <td class="p-3 border-b border-gray-100 text-gray-500 font-mono text-xs">${f.nik}</td>
                        <td class="p-3 border-b border-gray-100">${f.desa} <br><span class="text-xs text-gray-400">${f.dusun || '-'}</span></td>
                        <td class="p-3 border-b border-gray-100 text-gray-500">${f.tglPendataan}</td>
                        <td class="p-3 border-b border-gray-100 text-center">${riskBadge}</td>
                        <td class="p-3 border-b border-gray-100 text-center text-xs"><span class="px-2 py-1 bg-gray-100 rounded text-gray-600">${f.statusKunjungan}</span></td>
                        <td class="p-3 border-b border-gray-100 text-center">
                            <button onclick="deleteFamily('${f.idDoc}')" class="text-red-400 hover:text-red-600 mx-1" title="Hapus"><i class="fas fa-trash-alt"></i></button>
                        </td>
                    `;
                    tbody.appendChild(tr);
                });
            }
            document.getElementById('table-info').innerText = `Menampilkan ${filtered.length} dari ${families.length} data`;
        }

        // --- FORM SUBMIT & CLOUD SAVE ---
        window.updateLiveRisk = function() {
            const cKuning = document.querySelectorAll('.risk-kuning:checked');
            const cMerah = document.querySelectorAll('.risk-merah:checked');
            const badge = document.getElementById('live-risk-badge');

            if(cMerah.length > 0) {
                badge.className = "px-4 py-2 rounded-full font-bold text-sm bg-risk-highBg text-risk-high border border-risk-high";
                badge.innerHTML = "<i class='fas fa-heartbeat mr-1'></i> MERAH - RISIKO TINGGI";
            } else if (cKuning.length > 0) {
                badge.className = "px-4 py-2 rounded-full font-bold text-sm bg-risk-medBg text-risk-med border border-risk-med";
                badge.innerHTML = "<i class='fas fa-exclamation-triangle mr-1'></i> KUNING - RISIKO SEDANG";
            } else {
                badge.className = "px-4 py-2 rounded-full font-bold text-sm bg-risk-lowBg text-risk-low border border-risk-low";
                badge.innerHTML = "<i class='fas fa-shield-alt mr-1'></i> HIJAU - RISIKO RENDAH";
            }
        }

        window.handleFormSubmit = async function(e) {
            e.preventDefault();
            if(!db) { showToast("Database belum terhubung.", "error"); return; }

            const btn = document.getElementById('btn-submit');
            btn.disabled = true;
            btn.innerText = "Menyimpan ke Cloud...";

            const nama = document.getElementById('f-nama').value;
            const nik = document.getElementById('f-nik').value;
            const desa = document.getElementById('f-desa').value;
            const dusun = document.getElementById('f-dusun').value;
            const alamat = document.getElementById('f-alamat').value;
            const tgl = document.getElementById('f-tgl').value;
            const petugas = document.getElementById('f-petugas').value;
            const status = document.getElementById('f-status').value;

            const cKuning = Array.from(document.querySelectorAll('.risk-kuning:checked')).map(el => el.value);
            const cMerah = Array.from(document.querySelectorAll('.risk-merah:checked')).map(el => el.value);
            
            let risk = 'HIJAU';
            let factors = [];
            if(cMerah.length > 0) {
                risk = 'MERAH';
                factors = cMerah.concat(cKuning);
            } else if (cKuning.length > 0) {
                risk = 'KUNING';
                factors = cKuning;
            }

            try {
                // RULE 1: Strict Paths
                const colRef = collection(db, 'artifacts', appId, 'public', 'data', 'families');
                await addDoc(colRef, {
                    namaKK: nama, nik, desa, dusun, alamat, tglPendataan: tgl, statusKunjungan: status, risiko: risk, faktor: factors, petugas, createdAt: new Date().toISOString()
                });

                showToast("Data berhasil disimpan ke cloud Puskesmas!");
                document.getElementById('formKeluarga').reset();
                updateLiveRisk();
                navigate('data-keluarga');
            } catch (err) {
                console.error(err);
                showToast("Gagal menyimpan data ke cloud.", "error");
            } finally {
                btn.disabled = false;
                btn.innerText = "Simpan ke Cloud";
            }
        }

        window.deleteFamily = async function(idDoc) {
            if(!confirm("Yakin ingin menghapus data ini dari cloud?")) return;
            try {
                const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'families', idDoc);
                await deleteDoc(docRef);
                showToast("Data berhasil dihapus dari cloud.");
            } catch (err) {
                console.error(err);
                showToast("Gagal menghapus data.", "error");
            }
        }

        window.resetCloudData = async function() {
            if(!confirm("PERINGATAN: Semua data keluarga di cloud akan dihapus permanen. Lanjutkan?")) return;
            try {
                for(let f of families) {
                    await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'families', f.idDoc));
                }
                showToast("Semua data cloud berhasil direset.");
            } catch(err) {
                showToast("Gagal mereset data.", "error");
            }
        }

        // --- ADDITIONAL TABLES ---
        window.renderTableDesa = function() {
            const tbody = document.getElementById('tbody-desa');
            if(!tbody) return;
            tbody.innerHTML = '';
            listDesa.forEach((d, index) => {
                const terdata = families.filter(f => f.desa === d.nama).length;
                const belum = Math.max(0, d.target - terdata);
                const persentase = d.target > 0 ? ((terdata/d.target) * 100).toFixed(1) : 0;
                
                const tr = document.createElement('tr');
                tr.className = "hover:bg-blue-50 transition-colors";
                tr.innerHTML = `
                    <td class="p-3 border-b border-gray-100 text-center">${index + 1}</td>
                    <td class="p-3 border-b border-gray-100 font-semibold text-gray-800">${d.nama}</td>
                    <td class="p-3 border-b border-gray-100 text-center">${d.target}</td>
                    <td class="p-3 border-b border-gray-100 text-center font-bold text-primary">${terdata}</td>
                    <td class="p-3 border-b border-gray-100 text-center text-gray-500">${belum}</td>
                    <td class="p-3 border-b border-gray-100 text-center font-bold">${persentase}%</td>
                    <td class="p-3 border-b border-gray-100 text-sm">${d.pj}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        window.renderTablePemetaan = function() {
            const tbody = document.getElementById('tbody-pemetaan');
            if (!tbody) return;
            tbody.innerHTML = '';
            listDesa.forEach((d, index) => {
                const fDesa = families.filter(f => f.desa === d.nama);
                const total = fDesa.length;
                const hijau = fDesa.filter(f => f.risiko === 'HIJAU').length;
                const kuning = fDesa.filter(f => f.risiko === 'KUNING').length;
                const merah = fDesa.filter(f => f.risiko === 'MERAH').length;

                const tr = document.createElement('tr');
                tr.className = "hover:bg-gray-50 transition-colors";
                tr.innerHTML = `
                    <td class="p-3 border-b border-gray-100 text-center">${index + 1}</td>
                    <td class="p-3 border-b border-gray-100 font-medium text-gray-800">${d.nama}</td>
                    <td class="p-3 border-b border-gray-100 text-center font-bold">${total}</td>
                    <td class="p-3 border-b border-gray-100 text-center text-risk-low">${hijau}</td>
                    <td class="p-3 border-b border-gray-100 text-center text-risk-med">${kuning}</td>
                    <td class="p-3 border-b border-gray-100 text-center text-risk-high font-bold">${merah}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        window.renderTableRujukan = function() {
            const tbody = document.getElementById('tbody-rujukan');
            if (!tbody) return;
            tbody.innerHTML = '';
            const rujukanData = families.filter(f => f.risiko === 'MERAH');

            if(rujukanData.length === 0) {
                tbody.innerHTML = `<tr><td colspan="5" class="text-center py-4 text-gray-500">Tidak ada keluarga dengan Risiko Tinggi saat ini.</td></tr>`;
                return;
            }

            rujukanData.forEach((f, index) => {
                const faktor = f.faktor && f.faktor.length > 0 ? f.faktor.join(', ') : '-';
                const tr = document.createElement('tr');
                tr.className = "hover:bg-red-50 transition-colors";
                tr.innerHTML = `
                    <td class="p-3 border-b border-red-100 text-center">${index + 1}</td>
                    <td class="p-3 border-b border-red-100 font-medium text-gray-800">${f.namaKK}</td>
                    <td class="p-3 border-b border-red-100 text-gray-600 text-xs">${f.desa} / <br>${f.dusun || '-'}</td>
                    <td class="p-3 border-b border-red-100 text-sm text-risk-high">${faktor}</td>
                    <td class="p-3 border-b border-red-100 text-center"><span class="px-2 py-1 bg-red-100 text-red-700 text-xs rounded">Perlu Intervensi</span></td>
                `;
                tbody.appendChild(tr);
            });
        }

        window.renderTableSkrining = function() {
            const tbody = document.getElementById('tbody-skrining');
            if(!tbody) return;
            tbody.innerHTML = '';
            
            const searchVal = (document.getElementById('search-skrining')?.value || '').toLowerCase();
            let filtered = families.filter(f => {
                const matchSearch = (f.namaKK || '').toLowerCase().includes(searchVal) || 
                                    (f.nik || '').includes(searchVal) || 
                                    (f.faktor || []).some(k => k.toLowerCase().includes(searchVal));
                return matchSearch;
            });

            // Update stats cards in skrining view
            document.getElementById('skrining-stat-hijau').innerText = families.filter(f => f.risiko === 'HIJAU').length;
            document.getElementById('skrining-stat-kuning').innerText = families.filter(f => f.risiko === 'KUNING').length;
            document.getElementById('skrining-stat-merah').innerText = families.filter(f => f.risiko === 'MERAH').length;

            if(filtered.length === 0) {
                tbody.innerHTML = `<tr><td colspan="6" class="text-center py-8 text-gray-500">Belum ada data skrining terintegrasi. Silakan tambah pendataan baru.</td></tr>`;
                return;
            }

            filtered.forEach((f, index) => {
                let riskBadge = '';
                if(f.risiko === 'HIJAU') riskBadge = `<span class="bg-risk-lowBg text-risk-low px-2.5 py-1 rounded-full text-xs font-semibold">Risiko Rendah</span>`;
                else if(f.risiko === 'KUNING') riskBadge = `<span class="bg-risk-medBg text-risk-med px-2.5 py-1 rounded-full text-xs font-semibold">Risiko Sedang</span>`;
                else riskBadge = `<span class="bg-risk-highBg text-risk-high px-2.5 py-1 rounded-full text-xs font-semibold">Risiko Tinggi</span>`;

                const faktorList = f.faktor && f.faktor.length > 0 ? f.faktor.map(item => `<span class="inline-block bg-gray-100 text-gray-700 text-xs px-2 py-0.5 rounded mr-1 mb-1">• ${item}</span>`).join('') : '<span class="text-xs text-gray-400">Tidak ada masalah primer</span>';
                
                const tr = document.createElement('tr');
                tr.className = "hover:bg-gray-50 transition-colors";
                tr.innerHTML = `
                    <td class="p-3 border-b border-gray-100 text-center">${index + 1}</td>
                    <td class="p-3 border-b border-gray-100">
                        <div class="font-medium text-gray-800">${f.namaKK}</div>
                        <div class="text-xs font-mono text-gray-400">NIK: ${f.nik}</div>
                    </td>
                    <td class="p-3 border-b border-gray-100 text-sm">
                        <div>${f.desa}</div>
                        <div class="text-xs text-gray-400">${f.dusun || '-'}</div>
                    </td>
                    <td class="p-3 border-b border-gray-100 max-w-xs">${faktorList}</td>
                    <td class="p-3 border-b border-gray-100 text-center">${riskBadge}</td>
                    <td class="p-3 border-b border-gray-100 text-center text-xs text-gray-500">${f.tglPendataan}</td>
                `;
                tbody.appendChild(tr);
            });
        }

        // --- EXPORT TOOLS ---
        window.exportToExcel = function(tableId, filename) {
            try {
                const table = document.getElementById(tableId);
                if (!table) return showToast("Tabel data tidak ditemukan.", "error");
                const wb = XLSX.utils.table_to_book(table, { sheet: "Sheet 1" });
                XLSX.writeFile(wb, filename + ".xlsx");
                showToast("Berhasil mengekspor ke Excel.", "success");
            } catch (error) {
                showToast("Gagal mengekspor data ke Excel.", "error");
            }
        }

        window.exportToPDF = function(tableId, title, filename) {
            try {
                const { jsPDF } = window.jspdf;
                const doc = new jsPDF('landscape');
                doc.setFontSize(14);
                doc.text(title, 14, 15);
                doc.setFontSize(10);
                doc.text("Diekspor pada: " + new Date().toLocaleString('id-ID'), 14, 22);
                doc.autoTable({ html: '#' + tableId, startY: 28, theme: 'grid', headStyles: { fillColor: [22, 59, 101] }, styles: { fontSize: 9 } });
                doc.save(filename + ".pdf");
                showToast("Berhasil mengekspor ke PDF.", "success");
            } catch (error) {
                showToast("Gagal mengekspor data ke PDF.", "error");
            }
        }

        // --- INIT LOAD ---
        document.addEventListener('DOMContentLoaded', () => {
            const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
            document.getElementById('current-date').innerText = new Date().toLocaleDateString('id-ID', options);

            const selectDesaForm = document.getElementById('f-desa');
            const filterDesa = document.getElementById('filter-desa');
            listDesa.forEach(d => {
                selectDesaForm.innerHTML += `<option value="${d.nama}">${d.nama}</option>`;
                filterDesa.innerHTML += `<option value="${d.nama}">${d.nama}</option>`;
            });

            document.querySelectorAll('.risk-kuning, .risk-merah').forEach(cb => {
                cb.addEventListener('change', updateLiveRisk);
            });

            initFirebase();
        });
    </script>
</body>
</html>
