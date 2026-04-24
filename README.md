<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>Sistem Input Barang Masuk | Inventory Management</title>
    <style>
        * {
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            font-family: 'Segoe UI', Roboto, 'Helvetica Neue', system-ui, -apple-system, sans-serif;
            background: #f5f7fb;
            padding: 16px;
            margin: 0;
            min-height: 100vh;
            color: #1e293b;
        }

        /* Container Utama */
        .app-container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            border-radius: 32px;
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.08), 0 8px 10px -6px rgba(0,0,0,0.02);
            overflow: hidden;
        }

        /* Header */
        .main-header {
            background: linear-gradient(135deg, #0f2b3d 0%, #1a3a4f 100%);
            color: white;
            padding: 28px 32px;
        }

        .main-header h1 {
            font-size: 1.8rem;
            font-weight: 600;
            margin: 0 0 8px 0;
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .main-header h1::before {
            content: "📦";
            font-size: 2rem;
        }

        .main-header p {
            font-size: 0.9rem;
            opacity: 0.9;
            margin: 0;
        }

        /* Konten Utama */
        .main-content {
            padding: 28px 32px;
        }

        /* Kartu Form */
        .form-card {
            background: #ffffff;
            border-radius: 24px;
            padding: 24px;
            margin-bottom: 32px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            border: 1px solid #eef2f8;
        }

        .form-title {
            font-size: 1.3rem;
            font-weight: 600;
            margin-bottom: 20px;
            color: #0f2b3d;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .form-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 20px;
            margin-bottom: 24px;
        }

        .input-group {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .input-group label {
            font-weight: 600;
            font-size: 0.85rem;
            color: #334155;
            letter-spacing: 0.3px;
        }

        .input-group label .required {
            color: #ef4444;
            margin-left: 4px;
        }

        .input-group input,
        .input-group select,
        .input-group textarea {
            padding: 10px 14px;
            border: 1.5px solid #e2e8f0;
            border-radius: 12px;
            font-size: 0.9rem;
            transition: all 0.2s;
            background: #fafcff;
            font-family: inherit;
        }

        .input-group input:focus,
        .input-group select:focus,
        .input-group textarea:focus {
            outline: none;
            border-color: #2c6e9e;
            box-shadow: 0 0 0 3px rgba(44,110,158,0.1);
        }

        .btn-group {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
        }

        .btn {
            padding: 10px 24px;
            font-weight: 600;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            font-size: 0.9rem;
            transition: all 0.2s;
            font-family: inherit;
        }

        .btn-primary {
            background: #0f2b3d;
            color: white;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

        .btn-primary:hover {
            background: #1a4a65;
            transform: translateY(-1px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.15);
        }

        .btn-secondary {
            background: #f1f5f9;
            color: #334155;
            border: 1px solid #cbd5e1;
        }

        .btn-secondary:hover {
            background: #e2e8f0;
        }

        /* Section Data */
        .data-section {
            background: #ffffff;
            border-radius: 24px;
            border: 1px solid #eef2f8;
            overflow: hidden;
        }

        .section-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 16px;
            padding: 20px 24px;
            background: #fafcfd;
            border-bottom: 1px solid #eef2f8;
        }

        .title-badge {
            display: flex;
            align-items: baseline;
            gap: 12px;
        }

        .title-badge h2 {
            font-size: 1.3rem;
            margin: 0;
            color: #0f2b3d;
        }

        .badge {
            background: #e2e8f0;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            font-weight: 700;
            color: #1e293b;
        }

        .aksi-buttons {
            display: flex;
            gap: 10px;
        }

        .icon-btn {
            background: white;
            border: 1px solid #cbd5e1;
            padding: 8px 16px;
            border-radius: 10px;
            font-size: 0.85rem;
            font-weight: 500;
            cursor: pointer;
            transition: 0.2s;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .icon-btn:hover {
            background: #f1f5f9;
            transform: translateY(-1px);
        }

        /* Filter Panel */
        .filter-panel {
            background: #f9fbfd;
            padding: 20px 24px;
            display: flex;
            flex-wrap: wrap;
            gap: 16px;
            align-items: flex-end;
            border-bottom: 1px solid #eef2f8;
        }

        .filter-item {
            flex: 1;
            min-width: 180px;
        }

        .filter-item label {
            font-size: 0.75rem;
            font-weight: 600;
            color: #4b5563;
            display: block;
            margin-bottom: 6px;
        }

        .filter-item input,
        .filter-item select {
            width: 100%;
            padding: 8px 12px;
            border: 1px solid #d1d9e8;
            border-radius: 10px;
            font-size: 0.85rem;
            background: white;
        }

        .btn-reset-filter {
            background: white;
            border: 1px solid #cbd5e1;
            padding: 8px 20px;
            border-radius: 10px;
            font-weight: 500;
            cursor: pointer;
            font-size: 0.85rem;
            transition: 0.2s;
        }

        .btn-reset-filter:hover {
            background: #f1f5f9;
        }

        /* Info Panel */
        .info-panel {
            padding: 12px 24px;
            background: #f0f9ff;
            border-bottom: 1px solid #e2e8f0;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 12px;
        }

        .info-search {
            font-size: 0.8rem;
            color: #0284c7;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        /* Tabel */
        .table-wrapper {
            overflow-x: auto;
            padding: 0;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.85rem;
        }

        th {
            text-align: left;
            padding: 14px 12px;
            background: #f8fafc;
            color: #1e293b;
            font-weight: 600;
            border-bottom: 2px solid #e2e8f0;
        }

        td {
            padding: 12px;
            border-bottom: 1px solid #f1f5f9;
            vertical-align: middle;
            color: #334155;
        }

        .delete-btn {
            background: none;
            border: none;
            font-size: 1.2rem;
            cursor: pointer;
            color: #dc2626;
            padding: 6px 10px;
            border-radius: 8px;
            transition: all 0.2s;
        }

        .delete-btn:hover {
            background: #fee2e2;
            transform: scale(1.05);
        }

        .empty-row td {
            text-align: center;
            padding: 48px;
            color: #94a3b8;
            font-style: italic;
        }

        /* Notifikasi */
        .notification {
            position: fixed;
            top: 20px;
            right: 20px;
            background: #10b981;
            color: white;
            padding: 12px 20px;
            border-radius: 12px;
            font-size: 0.9rem;
            z-index: 1000;
            animation: slideIn 0.3s ease;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        }

        @keyframes slideIn {
            from {
                transform: translateX(100%);
                opacity: 0;
            }
            to {
                transform: translateX(0);
                opacity: 1;
            }
        }

        footer {
            padding: 20px 24px;
            text-align: center;
            font-size: 0.75rem;
            color: #64748b;
            border-top: 1px solid #eef2f8;
            background: #fafcfd;
        }

        @media (max-width: 768px) {
            body {
                padding: 12px;
            }
            .main-content {
                padding: 20px;
            }
            .form-grid {
                grid-template-columns: 1fr;
                gap: 16px;
            }
            .filter-panel {
                flex-direction: column;
                align-items: stretch;
            }
            .section-header {
                flex-direction: column;
                align-items: flex-start;
            }
            .btn-group .btn {
                flex: 1;
                text-align: center;
            }
            th, td {
                padding: 10px 8px;
                font-size: 0.75rem;
            }
        }
    </style>
</head>
<body>
<div class="app-container">
    <!-- HEADER -->
    <div class="main-header">
        <h1>Sistem Pencatatan Barang Masuk</h1>
        <p>Kelola penerimaan barang dari supplier dengan mudah dan efisien</p>
    </div>

    <div class="main-content">
        <!-- FORM TAMBAH BARANG -->
        <div class="form-card">
            <div class="form-title">
                <span>➕</span> Tambah Barang Baru
            </div>
            <form id="barangForm">
                <div class="form-grid">
                    <div class="input-group">
                        <label>🏭 Nama Supplier <span class="required">*</span></label>
                        <input type="text" id="supplier" placeholder="Contoh: PT Maju Jaya, CV Sukses" required>
                    </div>
                    <div class="input-group">
                        <label>📦 Nama Barang <span class="required">*</span></label>
                        <input type="text" id="namaBarang" placeholder="Nama produk / material" required>
                    </div>
                    <div class="input-group">
                        <label>🏷️ Kategori <span class="required">*</span></label>
                        <select id="kategori" required>
                            <option value="" disabled selected>-- Pilih Kategori --</option>
                            <option>Booklet 1 1/4</option>
                            <option>Booklet 1 1/4 tips</option>
                            <option>Booklet Kss</option>
                            <option>Booklet Kss tips</option>
                            <option>Display box</option>
                            <option>Filter tips 21</option>
                            <option>Filter tips 26</option>
                            <option>Filter tips 30</option>
                            <option>Trapezoid</option>
                            <option>Sticker</option>
                            <option>Lainnya</option>
                        </select>
                    </div>
                    <div class="input-group">
                        <label>🔢 Jumlah <span class="required">*</span></label>
                        <input type="number" id="jumlah" placeholder="0" min="1" required>
                    </div>
                    <div class="input-group">
                        <label>📏 Satuan <span class="required">*</span></label>
                        <input type="text" id="unit" placeholder="pcs, box, kg, roll, unit" required>
                    </div>
                    <div class="input-group">
                        <label>📅 Tanggal Masuk <span class="required">*</span></label>
                        <input type="date" id="tanggalMasuk" required>
                    </div>
                    <div class="input-group">
                        <label>📝 Catatan / No. PO</label>
                        <input type="text" id="catatan" placeholder="Opsional: PO-123 / INV-xxx / Keterangan">
                    </div>
                </div>
                <div class="btn-group">
                    <button type="submit" class="btn btn-primary">✨ Tambah Barang</button>
                    <button type="button" id="resetFormBtn" class="btn btn-secondary">🗑️ Reset Form</button>
                </div>
            </form>
        </div>

        <!-- SECTION DATA & TABEL -->
        <div class="data-section">
            <div class="section-header">
                <div class="title-badge">
                    <h2>📋 Riwayat Barang Masuk</h2>
                    <span class="badge" id="totalDataCount">0 item</span>
                </div>
                <div class="aksi-buttons">
                    <button id="printBtn" class="icon-btn">🖨️ Cetak</button>
                    <button id="exportCsvBtn" class="icon-btn">📎 Export CSV</button>
                </div>
            </div>

            <!-- PANEL FILTER -->
            <div class="filter-panel">
                <div class="filter-item">
                    <label>🔍 Cari Nama Barang</label>
                    <input type="text" id="filterNama" placeholder="Ketik nama barang...">
                </div>
                <div class="filter-item">
                    <label>🏭 Cari Supplier</label>
                    <input type="text" id="filterSupplier" placeholder="Nama supplier...">
                </div>
                <div class="filter-item">
                    <label>📂 Filter Kategori</label>
                    <select id="filterKategori">
                        <option value="">Semua Kategori</option>
                        <option>Booklet 1 1/4</option>
                        <option>Booklet 1 1/4 tips</option>
                        <option>Booklet Kss</option>
                        <option>Booklet Kss tips</option>
                        <option>Display box</option>
                        <option>Filter tips 21</option>
                        <option>Filter tips 26</option>
                        <option>Filter tips 30</option>
                        <option>Trapezoid</option>
                        <option>Sticker</option>
                        <option>Lainnya</option>
                    </select>
                </div>
                <button id="clearFilterBtn" class="btn-reset-filter">✖️ Reset Filter</button>
            </div>

            <!-- INFO PANEL -->
            <div class="info-panel">
                <div id="infoFilter" class="info-search">
                    📊 Memuat data...
                </div>
            </div>

            <!-- TABEL -->
            <div class="table-wrapper">
                <table id="mainTable">
                    <thead>
                        <tr>
                            <th>Supplier</th>
                            <th>Nama Barang</th>
                            <th>Kategori</th>
                            <th>Jumlah</th>
                            <th>Unit</th>
                            <th>Tanggal Masuk</th>
                            <th>Catatan</th>
                            <th style="width: 60px;">Aksi</th>
                        </tr>
                    </thead>
                    <tbody id="tableBody">
                        <tr class="empty-row">
                            <td colspan="8">⚡ Belum ada data. Silakan tambah barang masuk.</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
    <footer>
        📌 Data disimpan secara lokal di perangkat Anda. Pencarian berlaku pada Nama Barang, Supplier & Kategori.
    </footer>
</div>

<script>
    // ------------- DATA GLOBAL --------------
    let inventory = [];           // Menyimpan semua item
    let filteredInventory = [];   // Hasil filter

    // DOM Elements
    const form = document.getElementById('barangForm');
    const supplierInput = document.getElementById('supplier');
    const namaBarangInput = document.getElementById('namaBarang');
    const kategoriSelect = document.getElementById('kategori');
    const jumlahInput = document.getElementById('jumlah');
    const unitInput = document.getElementById('unit');
    const tglMasukInput = document.getElementById('tanggalMasuk');
    const catatanInput = document.getElementById('catatan');
    const resetFormBtn = document.getElementById('resetFormBtn');
    const tableBody = document.getElementById('tableBody');
    const totalDataCount = document.getElementById('totalDataCount');
    const printBtn = document.getElementById('printBtn');
    const exportCsvBtn = document.getElementById('exportCsvBtn');
    const filterNama = document.getElementById('filterNama');
    const filterSupplier = document.getElementById('filterSupplier');
    const filterKategori = document.getElementById('filterKategori');
    const clearFilterBtn = document.getElementById('clearFilterBtn');
    const infoFilter = document.getElementById('infoFilter');

    // ---------- Helper: Notifikasi ----------
    function showNotification(message, isError = false) {
        const notification = document.createElement('div');
        notification.className = 'notification';
        notification.style.background = isError ? '#ef4444' : '#10b981';
        notification.textContent = message;
        document.body.appendChild(notification);
        setTimeout(() => {
            notification.remove();
        }, 3000);
    }

    // ---------- Helper: Set default tanggal hari ini ----------
    function setDefaultDate() {
        if (!tglMasukInput.value) {
            const today = new Date();
            const yyyy = today.getFullYear();
            const mm = String(today.getMonth() + 1).padStart(2, '0');
            const dd = String(today.getDate()).padStart(2, '0');
            tglMasukInput.value = `${yyyy}-${mm}-${dd}`;
        }
    }

    // ---------- Load from localStorage ----------
    function loadData() {
        const stored = localStorage.getItem('inventory_barang_masuk');
        if (stored) {
            try {
                inventory = JSON.parse(stored);
                if (!Array.isArray(inventory)) inventory = [];
            } catch(e) { 
                inventory = [];
            }
        } else {
            // Data contoh untuk demo
            const sampleData = [
                {
                    id: Date.now() + '-1',
                    supplier: 'PT Sumber Makmur',
                    namaBarang: 'Kertas A4 70gr',
                    kategori: 'Lainnya',
                    jumlah: 50,
                    unit: 'rim',
                    tanggalMasuk: new Date().toISOString().split('T')[0],
                    catatan: 'PO-2024/001'
                },
                {
                    id: Date.now() + '-2',
                    supplier: 'CV Aksara Jaya',
                    namaBarang: 'Booklet Cover',
                    kategori: 'Booklet 1 1/4',
                    jumlah: 1000,
                    unit: 'pcs',
                    tanggalMasuk: new Date().toISOString().split('T')[0],
                    catatan: 'Sample order'
                }
            ];
            inventory = sampleData;
            saveData();
        }
        applyFilters();
    }

    // ---------- Save to localStorage ----------
    function saveData() {
        localStorage.setItem('inventory_barang_masuk', JSON.stringify(inventory));
    }

    // ---------- Format tanggal (YYYY-MM-DD ke DD/MM/YYYY) ----------
    function formatDate(dateStr) {
        if (!dateStr) return '-';
        const parts = dateStr.split('-');
        if (parts.length !== 3) return dateStr;
        return `${parts[2]}/${parts[1]}/${parts[0]}`;
    }

    // Escape HTML
    function escapeHtml(str) {
        if (!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if (m === '&') return '&amp;';
            if (m === '<') return '&lt;';
            if (m === '>') return '&gt;';
            return m;
        }).replace(/[\uD800-\uDBFF][\uDC00-\uDFFF]/g, function(c) {
            return c;
        });
    }

    // ---------- Proses Filter ----------
    function applyFilters() {
        const keywordNama = filterNama.value.trim().toLowerCase();
        const keywordSupplier = filterSupplier.value.trim().toLowerCase();
        const kategoriValue = filterKategori.value;

        filteredInventory = inventory.filter(item => {
            let match = true;
            if (keywordNama && !item.namaBarang.toLowerCase().includes(keywordNama)) match = false;
            if (keywordSupplier && !item.supplier.toLowerCase().includes(keywordSupplier)) match = false;
            if (kategoriValue && item.kategori !== kategoriValue) match = false;
            return match;
        });

        renderTable();
        updateInfoFilter();
    }

    // Update info filter
    function updateInfoFilter() {
        const totalAll = inventory.length;
        const totalFiltered = filteredInventory.length;
        if (filterNama.value || filterSupplier.value || filterKategori.value) {
            infoFilter.innerHTML = `🔎 Menampilkan ${totalFiltered} dari ${totalAll} data (filter aktif)`;
        } else {
            infoFilter.innerHTML = `📋 Total semua data: ${totalAll}`;
        }
        totalDataCount.textContent = `${totalAll} item`;
    }

    // ---------- Render Tabel ----------
    function renderTable() {
        if (!tableBody) return;

        if (filteredInventory.length === 0) {
            let emptyMessage = inventory.length === 0 ? 
                "⚡ Belum ada data. Silakan tambah barang masuk." : 
                "🔍 Tidak ada data yang sesuai dengan pencarian.";
            tableBody.innerHTML = `<tr class="empty-row"><td colspan="8">${emptyMessage}</td></tr>`;
            return;
        }

        tableBody.innerHTML = filteredInventory.map((item) => {
            return `
                <tr data-id="${item.id}">
                    <td><strong>${escapeHtml(item.supplier)}</strong></td>
                    <td>${escapeHtml(item.namaBarang)}</td>
                    <td><span style="background:#f1f5f9; padding:4px 8px; border-radius:6px; font-size:0.75rem;">${escapeHtml(item.kategori)}</span></td>
                    <td style="text-align:right; font-weight:600;">${Number(item.jumlah).toLocaleString()}</td>
                    <td>${escapeHtml(item.unit)}</td>
                    <td>${formatDate(item.tanggalMasuk)}</td>
                    <td>${escapeHtml(item.catatan) || '-'}</td>
                    <td><button class="delete-btn" data-id="${item.id}" title="Hapus Data">🗑️</button></td>
                </tr>
            `;
        }).join('');

        // Attach event listener ke tombol hapus
        document.querySelectorAll('.delete-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                e.stopPropagation();
                const id = btn.getAttribute('data-id');
                if (id && confirm('⚠️ Yakin ingin menghapus data ini?')) {
                    deleteItemById(id);
                }
            });
        });
    }

    // Hapus berdasarkan ID
    function deleteItemById(id) {
        const deletedItem = inventory.find(item => item.id === id);
        inventory = inventory.filter(item => item.id !== id);
        saveData();
        applyFilters();
        if (deletedItem) {
            showNotification(`✅ Berhasil menghapus "${deletedItem.namaBarang}"`);
        }
    }

    // ---------- Tambah Data Baru ----------
    function addItem(event) {
        event.preventDefault();

        const supplier = supplierInput.value.trim();
        const namaBarang = namaBarangInput.value.trim();
        const kategori = kategoriSelect.value;
        const jumlah = jumlahInput.value;
        const unit = unitInput.value.trim();
        const tanggalMasuk = tglMasukInput.value;
        const catatan = catatanInput.value.trim();

        if (!supplier || !namaBarang || !kategori || !jumlah || !unit || !tanggalMasuk) {
            showNotification('⚠️ Semua field yang bertanda * harus diisi!', true);
            return;
        }

        if (parseInt(jumlah) <= 0) {
            showNotification('⚠️ Jumlah harus lebih dari 0', true);
            return;
        }

        const id = Date.now() + '-' + Math.random().toString(36).substr(2, 8);

        const newItem = {
            id: id,
            supplier: supplier,
            namaBarang: namaBarang,
            kategori: kategori,
            jumlah: parseInt(jumlah),
            unit: unit,
            tanggalMasuk: tanggalMasuk,
            catatan: catatan || ''
        };

        inventory.unshift(newItem);
        saveData();
        resetForm();
        applyFilters();
        
        showNotification(`✅ Berhasil menambahkan "${namaBarang}" dari supplier "${supplier}"`);
    }

    // Reset form
    function resetForm() {
        supplierInput.value = '';
        namaBarangInput.value = '';
        kategoriSelect.value = '';
        jumlahInput.value = '';
        unitInput.value = '';
        catatanInput.value = '';
        setDefaultDate();
        supplierInput.focus();
    }

    // Reset filter
    function resetFilters() {
        filterNama.value = '';
        filterSupplier.value = '';
        filterKategori.value = '';
        applyFilters();
        showNotification('🔄 Filter telah direset');
    }

    // Cetak data
    function printData() {
        if (filteredInventory.length === 0) {
            showNotification('Tidak ada data untuk dicetak', true);
            return;
        }

        const printWindow = window.open('', '_blank');
        if (!printWindow) {
            showNotification('Harap izinkan popup untuk mencetak', true);
            return;
        }

        let tableHtml = `
            <!DOCTYPE html>
            <html>
            <head>
                <title>Laporan Barang Masuk</title>
                <style>
                    body { font-family: 'Segoe UI', Arial, sans-serif; margin: 40px; }
                    h1 { color: #0f2b3d; border-bottom: 2px solid #0f2b3d; padding-bottom: 10px; }
                    .header { margin-bottom: 30px; }
                    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
                    th, td { border: 1px solid #ddd; padding: 10px; text-align: left; }
                    th { background: #f2f2f2; font-weight: bold; }
                    .footer { margin-top: 30px; font-size: 12px; color: #666; text-align: center; }
                    .total { margin-top: 20px; font-weight: bold; }
                </style>
            </head>
            <body>
                <div class="header">
                    <h1>📋 Laporan Barang Masuk</h1>
                    <p>Tanggal cetak: ${new Date().toLocaleString('id-ID')}</p>
                </div>
        `;

        tableHtml += `
            <table>
                <thead>
                    <tr>
                        <th>Supplier</th><th>Nama Barang</th><th>Kategori</th>
                        <th>Jumlah</th><th>Unit</th><th>Tanggal Masuk</th><th>Catatan</th>
                    </tr>
                </thead>
                <tbody>
        `;
        
        filteredInventory.forEach(item => {
            tableHtml += `
                <tr>
                    <td>${escapeHtml(item.supplier)}</td>
                    <td>${escapeHtml(item.namaBarang)}</td>
                    <td>${escapeHtml(item.kategori)}</td>
                    <td style="text-align:right">${Number(item.jumlah).toLocaleString()}</td>
                    <td>${escapeHtml(item.unit)}</td>
                    <td>${formatDate(item.tanggalMasuk)}</td>
                    <td>${escapeHtml(item.catatan) || '-'}</td>
                </tr>
            `;
        });
        
        tableHtml += `
                </tbody>
            </table>
            <div class="total">
                <p><strong>Total Data: ${filteredInventory.length}</strong></p>
            </div>
            <div class="footer">
                Sistem Pencatatan Barang Masuk
            </div>
            </body>
            </html>
        `;

        printWindow.document.write(tableHtml);
        printWindow.document.close();
        printWindow.print();
    }

    // Export CSV
    function exportToCSV() {
        if (filteredInventory.length === 0) {
            showNotification('Tidak ada data untuk diekspor', true);
            return;
        }

        const headers = ['Supplier', 'Nama Barang', 'Kategori', 'Jumlah', 'Unit', 'Tanggal Masuk', 'Catatan'];
        const rows = filteredInventory.map(item => [
            `"${item.supplier.replace(/"/g, '""')}"`,
            `"${item.namaBarang.replace(/"/g, '""')}"`,
            `"${item.kategori.replace(/"/g, '""')}"`,
            item.jumlah,
            `"${item.unit.replace(/"/g, '""')}"`,
            formatDate(item.tanggalMasuk),
            `"${(item.catatan || '').replace(/"/g, '""')}"`
        ]);

        const csvContent = [headers, ...rows].map(row => row.join(',')).join('\n');
        const blob = new Blob(["\uFEFF" + csvContent], { type: 'text/csv;charset=utf-8;' });
        const link = document.createElement('a');
        const url = URL.createObjectURL(blob);
        link.href = url;
        link.setAttribute('download', `barang_masuk_${new Date().toISOString().slice(0,19).replace(/:/g, '-')}.csv`);
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        URL.revokeObjectURL(url);
        
        showNotification(`✅ Berhasil mengekspor ${filteredInventory.length} data ke CSV`);
    }

    // ---------- Event Listeners ----------
    form.addEventListener('submit', addItem);
    resetFormBtn.addEventListener('click', resetForm);
    printBtn.addEventListener('click', printData);
    exportCsvBtn.addEventListener('click', exportToCSV);
    clearFilterBtn.addEventListener('click', resetFilters);
    
    // Debounce filter
    let debounceTimer;
    function debounceFilter() {
        clearTimeout(debounceTimer);
        debounceTimer = setTimeout(() => {
            applyFilters();
        }, 300);
    }
    
    filterNama.addEventListener('input', debounceFilter);
    filterSupplier.addEventListener('input', debounceFilter);
    filterKategori.addEventListener('change', applyFilters);

    // ---------- Inisialisasi ----------
    function init() {
        loadData();
        setDefaultDate();
    }
    
    init();
</script>
</body>
</html>
