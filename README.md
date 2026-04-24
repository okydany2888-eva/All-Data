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
            min-width: 480px;
            width: 100%;
            color: #1e293b;
        }

        /* Container Utama */
        .app-container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            border-radius: 32px;
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.08), 0 8px 10px -6px rgba(0,0,0,0.02);
            overflow: hidden;
            padding: 20px 18px 28px 18px;
        }

        /* Header */
        .main-header {
            background: linear-gradient(135deg, #0f2b3d 0%, #1a3a4f 100%);
            color: white;
            padding: 20px 28px;
            border-radius: 24px 24px 0 0;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }

        .main-header h1 {
            font-size: 1.7rem;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .main-header h1::before {
            content: "📦";
            font-size: 1.8rem;
        }

        .main-header p {
            font-size: 0.85rem;
            opacity: 0.85;
            margin-top: 6px;
        }

        /* Kartu Form */
        .form-card {
            background: white;
            padding: 24px 28px;
            border-radius: 0 0 20px 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
            margin-bottom: 28px;
        }

        .form-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
            gap: 18px;
        }

        .input-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .input-group label {
            font-weight: 600;
            font-size: 0.8rem;
            color: #1e293b;
            letter-spacing: 0.3px;
        }

        .input-group input,
        .input-group select {
            padding: 10px 14px;
            border: 1.5px solid #e2e8f0;
            border-radius: 14px;
            font-size: 0.9rem;
            transition: all 0.2s;
            background: #fafcff;
        }

        .input-group input:focus,
        .input-group select:focus {
            outline: none;
            border-color: #2c6e9e;
            box-shadow: 0 0 0 3px rgba(44,110,158,0.15);
        }

        .btn-group {
            display: flex;
            gap: 12px;
            margin-top: 22px;
            flex-wrap: wrap;
        }

        .btn {
            padding: 10px 22px;
            font-weight: 600;
            border: none;
            border-radius: 40px;
            cursor: pointer;
            font-size: 0.85rem;
            transition: all 0.2s;
        }

        .btn-primary {
            background: #0f2b3d;
            color: white;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .btn-primary:hover {
            background: #1a4a65;
            transform: translateY(-1px);
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
            background: white;
            border-radius: 24px;
            padding: 20px 24px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
        }

        .section-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 16px;
            margin-bottom: 20px;
            padding-bottom: 12px;
            border-bottom: 2px solid #eef2f9;
        }

        .title-badge {
            display: flex;
            align-items: baseline;
            gap: 12px;
        }

        .title-badge h2 {
            font-size: 1.3rem;
            color: #0f2b3d;
        }

        .badge {
            background: #e2e8f0;
            padding: 4px 12px;
            border-radius: 30px;
            font-size: 0.7rem;
            font-weight: 700;
            color: #1e293b;
        }

        .aksi-buttons {
            display: flex;
            gap: 10px;
        }

        .small-icon-btn {
            background: #f8fafc;
            border: 1px solid #cbd5e1;
            padding: 6px 14px;
            border-radius: 30px;
            font-size: 0.75rem;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
        }

        .small-icon-btn:hover {
            background: #eef2ff;
        }

        /* Filter Panel */
        .filter-panel {
            background: #f9fbfd;
            padding: 16px 20px;
            border-radius: 20px;
            margin-bottom: 24px;
            display: flex;
            flex-wrap: wrap;
            gap: 14px;
            align-items: flex-end;
            border: 1px solid #eef2f8;
        }

        .filter-item {
            flex: 1;
            min-width: 160px;
        }

        .filter-item label {
            font-size: 0.7rem;
            font-weight: 600;
            color: #4b5563;
            display: block;
            margin-bottom: 4px;
        }

        .filter-item input,
        .filter-item select {
            width: 100%;
            padding: 8px 12px;
            border: 1px solid #d1d9e8;
            border-radius: 30px;
            font-size: 0.8rem;
        }

        .btn-reset-filter {
            background: white;
            border: 1px solid #cbd5e1;
            padding: 8px 20px;
            border-radius: 30px;
            font-weight: 500;
            cursor: pointer;
            font-size: 0.75rem;
        }

        /* Tabel */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 18px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.8rem;
        }

        th {
            text-align: left;
            padding: 12px 10px;
            background: #f1f5f9;
            color: #1e293b;
            font-weight: 700;
            border-bottom: 2px solid #e2e8f0;
        }

        td {
            padding: 11px 10px;
            border-bottom: 1px solid #edf2f7;
            vertical-align: middle;
            color: #1f2a44;
        }

        .delete-btn {
            background: none;
            border: none;
            font-size: 1.2rem;
            cursor: pointer;
            color: #b91c1c;
            padding: 4px 8px;
            border-radius: 30px;
            transition: all 0.1s;
        }

        .delete-btn:hover {
            background: #fee2e2;
            transform: scale(1.03);
        }

        .empty-row td {
            text-align: center;
            padding: 40px;
            color: #94a3b8;
            font-style: italic;
        }

        .info-search {
            font-size: 0.7rem;
            background: #eef2ff;
            padding: 5px 12px;
            border-radius: 30px;
            display: inline-block;
            margin-bottom: 12px;
        }

        footer {
            margin-top: 20px;
            text-align: center;
            font-size: 0.7rem;
            color: #5b6e8c;
        }

        @media (max-width: 640px) {
            body { padding: 12px; }
            .form-card { padding: 18px; }
            .btn-group .btn { flex: 1; text-align: center; }
            .filter-panel { flex-direction: column; align-items: stretch; }
            .section-header { flex-direction: column; align-items: flex-start; }
        }
    </style>
</head>
<body>
<div class="app-container">
    <!-- HEADER -->
    <div class="main-header">
        <h1>Sistem Pencatatan Barang Masuk</h1>
        <p>Kelola penerimaan barang dari supplier</p>
    </div>

    <!-- FORM TAMBAH BARANG -->
    <div class="form-card">
        <form id="barangForm">
            <div class="form-grid">
                <div class="input-group">
                    <label>🏭 Nama Supplier *</label>
                    <input type="text" id="supplier" placeholder="Contoh: PT, CV" required>
                </div>
                <div class="input-group">
                    <label>📦 Nama Barang *</label>
                    <input type="text" id="namaBarang" placeholder="Nama produk / material" required>
                </div>
                <div class="input-group">
                    <label>🏷️ Kategori *</label>
                    <select id="kategori" required>
                        <option value="" disabled selected>-- Pilih --</option>
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
                    <label>🔢 Jumlah *</label>
                    <input type="number" id="jumlah" placeholder="0" min="1" required>
                </div>
                <div class="input-group">
                    <label>📏 Satuan *</label>
                    <input type="text" id="unit" placeholder="pcs, box, kg, roll" required>
                </div>
                <div class="input-group">
                    <label>📅 Tanggal Masuk *</label>
                    <input type="date" id="tanggalMasuk" required>
                </div>
                <div class="input-group">
                    <label>📝 Catatan / No. PO</label>
                    <input type="text" id="catatan" placeholder="Opsional: PO-123 / INV-xxx">
                </div>
            </div>
            <div class="btn-group">
                <button type="submit" class="btn btn-primary">Tambah Barang</button>
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
                <button id="printBtn" class="small-icon-btn">🖨️ Cetak</button>
                <button id="exportCsvBtn" class="small-icon-btn">📎 Export CSV</button>
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

        <div id="infoFilter" class="info-search"></div>

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
                        <th>Aksi</th>
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
    <footer>📌 Data disimpan secara lokal di perangkat Anda. Pencarian berlaku pada Nama Barang, Supplier & Kategori.</footer>
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
            } catch(e) { inventory = []; }
        } else {
            // Data contoh jika kosong (opsional)
            inventory = [];
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

    // Escape HTML khusus untuk keamanan
    function escapeHtml(str) {
        if (!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if (m === '&') return '&amp;';
            if (m === '<') return '&lt;';
            if (m === '>') return '&gt;';
            return m;
        });
    }

    // ---------- Proses Filter (Nama Barang, Supplier, Kategori) ----------
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

    // Menampilkan info jumlah filter
    function updateInfoFilter() {
        const totalAll = inventory.length;
        const totalFiltered = filteredInventory.length;
        if (filterNama.value || filterSupplier.value || filterKategori.value) {
            infoFilter.textContent = `🔎 Menampilkan ${totalFiltered} dari ${totalAll} data (filter aktif)`;
        } else {
            infoFilter.textContent = `📋 Total semua data: ${totalAll}`;
        }
        totalDataCount.textContent = `${totalAll} item`;
    }

    // ---------- Render Tabel dengan Data yang sudah difilter ----------
    function renderTable() {
        if (!tableBody) return;

        if (filteredInventory.length === 0) {
            let emptyMessage = inventory.length === 0 ? "Belum ada data. Silakan tambah barang masuk." : "Tidak ada data yang sesuai dengan pencarian.";
            tableBody.innerHTML = `<tr class="empty-row"><td colspan="8">${emptyMessage}</td></tr>`;
            return;
        }

        tableBody.innerHTML = filteredInventory.map((item, idx) => {
            // Mencari index asli untuk kebutuhan hapus via id (lebih aman)
            return `
                <tr data-id="${item.id}">
                    <td>${escapeHtml(item.supplier)}</td>
                    <td>${escapeHtml(item.namaBarang)}</td>
                    <td>${escapeHtml(item.kategori)}</td>
                    <td style="text-align:right">${Number(item.jumlah).toLocaleString()}</td>
                    <td>${escapeHtml(item.unit)}</td>
                    <td>${formatDate(item.tanggalMasuk)}</td>
                    <td>${escapeHtml(item.catatan) || '-'}</td>
                    <td><button class="delete-btn" data-id="${item.id}" title="Hapus">🗑️</button></td>
                </tr>
            `;
        }).join('');

        // Attach event listener ke semua tombol hapus setelah render
        document.querySelectorAll('.delete-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                e.stopPropagation();
                const id = btn.getAttribute('data-id');
                if (id && confirm('Yakin ingin menghapus data ini ?')) {
                    deleteItemById(id);
                }
            });
        });
    }

    // Hapus berdasarkan ID unik
    function deleteItemById(id) {
        const newInventory = inventory.filter(item => item.id !== id);
        inventory = newInventory;
        saveData();
        applyFilters();  // refresh tampilan dan filter
    }

        // ---------- Tambah Data Baru ----------
    function addItem(event) {
        event.preventDefault();

        // Ambil nilai dari form
        const supplier = supplierInput.value.trim();
        const namaBarang = namaBarangInput.value.trim();
        const kategori = kategoriSelect.value;
        const jumlah = jumlahInput.value;
        const unit = unitInput.value.trim();
        const tanggalMasuk = tglMasukInput.value;
        const catatan = catatanInput.value.trim();

        // Validasi sederhana
        if (!supplier || !namaBarang || !kategori || !jumlah || !unit || !tanggalMasuk) {
            alert('⚠️ Semua field yang bertanda * harus diisi!');
            return;
        }

        if (parseInt(jumlah) <= 0) {
            alert('Jumlah harus lebih dari 0');
            return;
        }

        // Buat ID unik (timestamp + random)
        const id = Date.now() + '-' + Math.random().toString(36).substr(2, 6);

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

        inventory.unshift(newItem); // Tambahkan di awal array
        saveData();
        
        // Reset form setelah sukses
        resetForm();
        
        // Terapkan filter (tetap menampilkan data terbaru)
        applyFilters();
        
        // Tampilkan notifikasi sukses
        const infoFilterDiv = document.getElementById('infoFilter');
        const originalText = infoFilterDiv.textContent;
        infoFilterDiv.style.transition = '0.2s';
        infoFilterDiv.style.background = '#dcfce7';
        infoFilterDiv.textContent = '✅ Barang berhasil ditambahkan!';
        setTimeout(() => {
            infoFilterDiv.style.background = '';
            updateInfoFilter();
        }, 1500);
    }

    // Reset semua field form
    function resetForm() {
        supplierInput.value = '';
        namaBarangInput.value = '';
        kategoriSelect.value = '';
        jumlahInput.value = '';
        unitInput.value = '';
        catatanInput.value = '';
        
        // Set tanggal default hari ini
        const today = new Date();
        const yyyy = today.getFullYear();
        const mm = String(today.getMonth() + 1).padStart(2, '0');
        const dd = String(today.getDate()).padStart(2, '0');
        tglMasukInput.value = `${yyyy}-${mm}-${dd}`;
        
        // Fokus ke supplier
        supplierInput.focus();
    }

    // Reset semua filter
    function resetFilters() {
        filterNama.value = '';
        filterSupplier.value = '';
        filterKategori.value = '';
        applyFilters();
    }

    // Cetak data (print window)
    function printData() {
        const printWindow = window.open('', '_blank');
        if (!printWindow) {
            alert('Harap izinkan popup untuk mencetak');
            return;
        }

        let tableHtml = `
            <!DOCTYPE html>
            <html>
            <head>
                <title>Laporan Barang Masuk</title>
                <style>
                    body { font-family: Arial, sans-serif; margin: 40px; }
                    h1 { color: #0f2b3d; border-bottom: 2px solid #ccc; }
                    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
                    th, td { border: 1px solid #ddd; padding: 8px 12px; text-align: left; }
                    th { background: #f2f2f2; }
                    .footer { margin-top: 30px; font-size: 12px; color: #666; text-align: center; }
                </style>
            </head>
            <body>
                <h1>📋 Laporan Barang Masuk</h1>
                <p>Tanggal cetak: ${new Date().toLocaleString('id-ID')}</p>
        `;

        if (filteredInventory.length === 0) {
            tableHtml += `<p><em>Tidak ada data ditemukan</em></p>`;
        } else {
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
                <p><strong>Total data: ${filteredInventory.length}</strong></p>
            `;
        }

        tableHtml += `
                <div class="footer">Sistem Pencatatan Barang Masuk</div>
            </body>
            </html>
        `;

        printWindow.document.write(tableHtml);
        printWindow.document.close();
        printWindow.print();
    }

    // Export ke CSV
    function exportToCSV() {
        if (filteredInventory.length === 0) {
            alert('Tidak ada data untuk diekspor');
            return;
        }

        // Header CSV
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
    }

    // ---------- Event Listeners ----------
    form.addEventListener('submit', addItem);
    resetFormBtn.addEventListener('click', resetForm);
    printBtn.addEventListener('click', printData);
    exportCsvBtn.addEventListener('click', exportToCSV);
    clearFilterBtn.addEventListener('click', resetFilters);
    
    // Filter dengan debounce untuk performa
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
