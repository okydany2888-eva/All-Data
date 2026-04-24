<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>Sistem Input Barang Masuk | Rekap Barang + Riwayat</title>
    <!-- SheetJS library untuk export Excel -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Roboto, 'Helvetica Neue', sans-serif;
            background: #eef2f7;
            padding: 20px;
            min-height: 100vh;
        }

        .app-container {
            max-width: 1400px;
            margin: 0 auto;
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

        /* Tab Navigation */
        .tab-navigation {
            display: flex;
            background: white;
            border-radius: 16px 16px 0 0;
            overflow: hidden;
            margin-top: 0;
            box-shadow: 0 -2px 10px rgba(0,0,0,0.05);
        }
        .tab-btn {
            flex: 1;
            padding: 14px 20px;
            background: #f1f5f9;
            border: none;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            color: #475569;
            border-bottom: 3px solid transparent;
        }
        .tab-btn.active {
            background: white;
            color: #0f2b3d;
            border-bottom-color: #0f2b3d;
        }
        .tab-btn:hover:not(.active) {
            background: #e2e8f0;
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
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .input-group label span {
            background: #e2e8f0;
            padding: 2px 8px;
            border-radius: 20px;
            font-size: 0.65rem;
            font-weight: normal;
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

        .select-with-add {
            display: flex;
            gap: 8px;
            align-items: center;
        }
        .select-with-add select {
            flex: 1;
        }
        .btn-add-option {
            background: #f1f5f9;
            border: 1.5px solid #e2e8f0;
            border-radius: 14px;
            padding: 0 12px;
            font-size: 1.2rem;
            cursor: pointer;
            transition: all 0.2s;
            color: #0f2b3d;
            font-weight: bold;
        }
        .btn-add-option:hover {
            background: #e2e8f0;
            border-color: #2c6e9e;
        }
        .inline-input {
            margin-top: 8px;
            display: flex;
            gap: 8px;
            animation: fadeIn 0.2s ease;
        }
        .inline-input input {
            flex: 1;
            padding: 8px 12px;
            font-size: 0.8rem;
        }
        .btn-small {
            padding: 6px 14px;
            font-size: 0.7rem;
            border-radius: 30px;
            border: none;
            cursor: pointer;
            font-weight: 600;
        }
        .btn-save-option {
            background: #0f2b3d;
            color: white;
        }
        .btn-cancel-option {
            background: #e2e8f0;
            color: #334155;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-5px); }
            to { opacity: 1; transform: translateY(0); }
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

        .btn-edit-mode {
            background: #eab308;
            color: #1e293b;
            border: none;
        }
        .btn-edit-mode:hover {
            background: #ca8a04;
            color: white;
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
            flex-wrap: wrap;
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
            min-width: 150px;
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
            font-size: 0.75rem;
        }

        th {
            text-align: left;
            padding: 10px 8px;
            background: #f1f5f9;
            color: #1e293b;
            font-weight: 700;
            border-bottom: 2px solid #e2e8f0;
            white-space: nowrap;
        }

        td {
            padding: 9px 8px;
            border-bottom: 1px solid #edf2f7;
            vertical-align: middle;
            color: #1f2a44;
        }

        .month-group {
            background: #eef2ff;
            font-weight: 700;
            font-size: 0.8rem;
            border-top: 2px solid #cbd5e1;
            border-bottom: 2px solid #cbd5e1;
        }
        .month-group td {
            background: #eef2ff;
            padding: 8px 12px;
            color: #0f2b3d;
        }

        /* Tabel Rekap */
        .rekap-table th, .rekap-table td {
            text-align: left;
        }
        .rekap-table td:first-child,
        .rekap-table th:first-child {
            text-align: left;
        }
        .total-row {
            background: #f1f5f9;
            font-weight: bold;
            border-top: 2px solid #cbd5e1;
        }
        .total-row td {
            font-weight: bold;
            background: #f1f5f9;
        }

        .action-buttons {
            display: flex;
            gap: 6px;
            align-items: center;
            flex-wrap: nowrap;
        }
        .edit-btn, .delete-btn {
            background: none;
            border: none;
            cursor: pointer;
            padding: 4px 6px;
            border-radius: 20px;
            font-size: 1rem;
            transition: all 0.1s;
        }
        .edit-btn {
            color: #0f3b5c;
        }
        .edit-btn:hover {
            background: #e6f0fa;
        }
        .delete-btn {
            color: #b91c1c;
        }
        .delete-btn:hover {
            background: #fee2e2;
        }

        .pagination {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 8px;
            margin-top: 24px;
            flex-wrap: wrap;
        }
        .page-btn {
            background: white;
            border: 1px solid #cbd5e1;
            padding: 6px 12px;
            border-radius: 30px;
            cursor: pointer;
            font-size: 0.75rem;
            font-weight: 500;
            transition: all 0.2s;
        }
        .page-btn:hover:not(:disabled) {
            background: #e2e8f0;
        }
        .page-btn.active {
            background: #0f2b3d;
            color: white;
            border-color: #0f2b3d;
        }
        .page-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        .page-info {
            font-size: 0.75rem;
            color: #475569;
            margin: 0 8px;
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

        .toast-msg {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: #0f2b3d;
            color: white;
            padding: 10px 18px;
            border-radius: 40px;
            font-size: 0.8rem;
            z-index: 1000;
            opacity: 0;
            transition: opacity 0.3s;
            pointer-events: none;
            box-shadow: 0 4px 12px rgba(0,0,0,0.2);
        }
        footer {
            margin-top: 20px;
            text-align: center;
            font-size: 0.7rem;
            color: #5b6e8c;
        }

        /* Tab content */
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }

        @media (max-width: 640px) {
            body { padding: 12px; }
            .form-card { padding: 18px; }
            .btn-group .btn { flex: 1; text-align: center; }
            .filter-panel { flex-direction: column; align-items: stretch; }
            .section-header { flex-direction: column; align-items: flex-start; }
            th, td { font-size: 0.7rem; padding: 6px 4px; }
            .select-with-add { flex-wrap: wrap; }
            .btn-add-option { padding: 0 15px; }
            .tab-btn { font-size: 0.8rem; padding: 10px 12px; }
        }
    </style>
</head>
<body>
<div class="app-container">
    <div class="main-header">
        <h1>Sistem Pencatatan Barang Masuk</h1>
        <p>Input dengan dropdown | Rekap Barang (total per nama barang) | Export Excel</p>
    </div>

    <!-- FORM TAMBAH / EDIT -->
    <div class="form-card">
        <form id="barangForm">
            <div class="form-grid">
                <div class="input-group">
                    <label>🏭 Nama Supplier </label>
                    <div class="select-with-add">
                        <select id="supplierSelect" required>
                            <option value="" disabled selected>-- Pilih Supplier --</option>
                        </select>
                        <button type="button" class="btn-add-option" id="addSupplierBtn" title="Tambah baru">+</button>
                    </div>
                    <div id="supplierAddContainer" style="display:none;" class="inline-input">
                        <input type="text" id="newSupplierInput" placeholder="Nama supplier baru">
                        <button type="button" class="btn-small btn-save-option" id="saveSupplierBtn">Simpan</button>
                        <button type="button" class="btn-small btn-cancel-option" id="cancelSupplierBtn">Batal</button>
                    </div>
                </div>

                <div class="input-group">
                    <label>📦 Nama Barang </label>
                    <div class="select-with-add">
                        <select id="barangSelect" required>
                            <option value="" disabled selected>-- Pilih Barang --</option>
                        </select>
                        <button type="button" class="btn-add-option" id="addBarangBtn" title="Tambah Barang">+</button>
                    </div>
                    <div id="barangAddContainer" style="display:none;" class="inline-input">
                        <input type="text" id="newBarangInput" placeholder="Nama barang baru">
                        <button type="button" class="btn-small btn-save-option" id="saveBarangBtn">Simpan</button>
                        <button type="button" class="btn-small btn-cancel-option" id="cancelBarangBtn">Batal</button>
                    </div>
                </div>

                <div class="input-group">
                    <label>🏷️ Kategori</label>
                    <select id="kategoriSelect" required>
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
                    <label>🔢 Jumlah</label>
                    <input type="number" id="jumlah" placeholder="0" min="1" required>
                </div>

                <div class="input-group">
                    <label>📏 Satuan </label>
                    <div class="select-with-add">
                        <select id="unitSelect" required>
                            <option value="" disabled selected>-- Pilih Satuan --</option>
                            <option>pcs</option>
                            <option>box</option>
                            <option>kg</option>
                            <option>roll</option>
                            <option>set</option>
                            <option>pack</option>
                            <option>lembar</option>
                            <option>meter</option>
                            <option>liter</option>
                        </select>
                        <button type="button" class="btn-add-option" id="addUnitBtn" title="Tambah Satuan">+</button>
                    </div>
                    <div id="unitAddContainer" style="display:none;" class="inline-input">
                        <input type="text" id="newUnitInput" placeholder="Satuan baru (contoh: dus)">
                        <button type="button" class="btn-small btn-save-option" id="saveUnitBtn">Simpan</button>
                        <button type="button" class="btn-small btn-cancel-option" id="cancelUnitBtn">Batal</button>
                    </div>
                </div>

                <div class="input-group">
                    <label>📅 Tanggal Masuk</label>
                    <input type="date" id="tanggalMasuk" required>
                </div>
                <div class="input-group">
                    <label>📝 Catatan / No. PO</label>
                    <input type="text" id="catatan" placeholder="Opsional: PO-123 / INV-xxx">
                </div>
            </div>
            <div class="btn-group">
                <button type="submit" class="btn btn-primary" id="submitBtn">Tambah Barang</button>
                <button type="button" id="resetFormBtn" class="btn btn-secondary">🗑️ Reset Form</button>
                <button type="button" id="cancelEditBtn" class="btn btn-secondary" style="display:none;">Batalkan Edit</button>
            </div>
        </form>
    </div>

    <!-- TAB NAVIGATION -->
    <div class="tab-navigation">
        <button class="tab-btn active" data-tab="riwayat">📋 Riwayat Barang Masuk</button>
        <button class="tab-btn" data-tab="rekap">📊 Rekap Barang (Total per Nama)</button>
    </div>

    <!-- TAB 1: RIWAYAT BARANG MASUK -->
    <div id="tab-riwayat" class="tab-content active">
        <div class="data-section">
            <div class="section-header">
                <div class="title-badge">
                    <h2>📋 Riwayat Barang Masuk</h2>
                    <span class="badge" id="totalDataCount">0 item</span>
                </div>
                <div class="aksi-buttons">
                    <button id="printBtn" class="small-icon-btn">🖨️ Cetak</button>
                    <button id="exportExcelBtn" class="small-icon-btn">📎 Export Excel</button>
                </div>
            </div>

            <div class="filter-panel">
                <div class="filter-item">
                    <label>🔍 Cari Barang / Supplier</label>
                    <input type="text" id="filterText" placeholder="Ketik nama barang atau supplier...">
                </div>
                <div class="filter-item">
                    <label>📂 Filter Kategori</label>
                    <select id="filterKategori">
                        <option value="">Semua Kategori</option>
                        <option>Booklet 1 1/4</option><option>Booklet 1 1/4 tips</option>
                        <option>Booklet Kss</option><option>Booklet Kss tips</option>
                        <option>Display box</option><option>Filter tips 21</option>
                        <option>Filter tips 26</option><option>Filter tips 30</option>
                        <option>Trapezoid</option><option>Sticker</option><option>Lainnya</option>
                    </select>
                </div>
                <div class="filter-item">
                    <label>🗓️ Filter Bulan</label>
                    <input type="month" id="filterBulan">
                </div>
                <button id="clearFilterBtn" class="btn-reset-filter">Reset Filter</button>
            </div>

            <div id="infoFilter" class="info-search"></div>

            <div class="table-wrapper">
                <table id="mainTable">
                    <thead>
                        <tr><th>Supplier</th><th>Nama Barang</th><th>Kategori</th><th>Jml</th><th>Satuan</th><th>Tgl Masuk</th><th>Catatan</th><th>Aksi</th></tr>
                    </thead>
                    <tbody id="tableBody">
                        <tr class="empty-row"><td colspan="8">⚡ Belum ada data. Silakan tambah barang masuk.</td></tr>
                    </tbody>
                </table>
            </div>
            <div class="pagination" id="paginationContainer"></div>
        </div>
    </div>

    <!-- TAB 2: REKAP BARANG (TOTAL PER NAMA) -->
    <div id="tab-rekap" class="tab-content">
        <div class="data-section">
            <div class="section-header">
                <div class="title-badge">
                    <h2>📊 Rekap Barang</h2>
                    <span class="badge" id="rekapCount">0 item</span>
                </div>
                <div class="aksi-buttons">
                    <button id="exportRekapExcelBtn" class="small-icon-btn">📎 Export Rekap Excel</button>
                </div>
            </div>

            <div class="filter-panel">
                <div class="filter-item">
                    <label>🔍 Cari Nama Barang</label>
                    <input type="text" id="rekapFilterText" placeholder="Ketik nama barang...">
                </div>
                <div class="filter-item">
                    <label>📂 Filter Kategori</label>
                    <select id="rekapFilterKategori">
                        <option value="">Semua Kategori</option>
                        <option>Booklet 1 1/4</option><option>Booklet 1 1/4 tips</option>
                        <option>Booklet Kss</option><option>Booklet Kss tips</option>
                        <option>Display box</option><option>Filter tips 21</option>
                        <option>Filter tips 26</option><option>Filter tips 30</option>
                        <option>Trapezoid</option><option>Sticker</option><option>Lainnya</option>
                    </select>
                </div>
                <button id="resetRekapFilterBtn" class="btn-reset-filter">Reset Filter</button>
            </div>

            <div id="infoRekap" class="info-search"></div>

            <div class="table-wrapper">
                <table class="rekap-table" id="rekapTable">
                    <thead>
                        <tr><th>No</th><th>Nama Barang</th><th>Kategori</th><th>Satuan</th><th>Total Jumlah Masuk</th><th>Detail Penerimaan</th></tr>
                    </thead>
                    <tbody id="rekapBody">
                        <tr class="empty-row"><td colspan="6">⚡ Belum ada data barang.学</tr>
                    </tbody>
                 </table>
            </div>
        </div>
    </div>
    
    <footer>📌 Supplier, Nama Barang, dan Satuan menggunakan dropdown select. Klik tombol + untuk menambah opsi baru. Tab Rekap menampilkan total per nama barang.</footer>
    <div id="toastMessage" class="toast-msg"></div>
</div>

<script>
    // ======================= DATA GLOBAL ========================
    let inventory = [];           
    let filteredInventory = [];   
    let editMode = false;
    let editingId = null;
    
    let currentPage = 1;
    const rowsPerPage = 10;

    // Master data untuk dropdown
    let masterSuppliers = [];
    let masterBarang = [];
    let masterUnits = ['pcs', 'box', 'kg', 'roll', 'set', 'pack', 'lembar', 'meter', 'liter'];
    
    // DOM Elements
    const form = document.getElementById('barangForm');
    const supplierSelect = document.getElementById('supplierSelect');
    const barangSelect = document.getElementById('barangSelect');
    const kategoriSelect = document.getElementById('kategoriSelect');
    const jumlahInput = document.getElementById('jumlah');
    const unitSelect = document.getElementById('unitSelect');
    const tglMasukInput = document.getElementById('tanggalMasuk');
    const catatanInput = document.getElementById('catatan');
    const resetFormBtn = document.getElementById('resetFormBtn');
    const cancelEditBtn = document.getElementById('cancelEditBtn');
    const submitBtn = document.getElementById('submitBtn');
    const tableBody = document.getElementById('tableBody');
    const totalDataCount = document.getElementById('totalDataCount');
    const printBtn = document.getElementById('printBtn');
    const exportExcelBtn = document.getElementById('exportExcelBtn');
    const filterText = document.getElementById('filterText');
    const filterKategori = document.getElementById('filterKategori');
    const filterBulan = document.getElementById('filterBulan');
    const clearFilterBtn = document.getElementById('clearFilterBtn');
    const infoFilter = document.getElementById('infoFilter');
    const paginationContainer = document.getElementById('paginationContainer');
    const toastMsgDiv = document.getElementById('toastMessage');

    // Rekap DOM
    const rekapBody = document.getElementById('rekapBody');
    const rekapCount = document.getElementById('rekapCount');
    const rekapFilterText = document.getElementById('rekapFilterText');
    const rekapFilterKategori = document.getElementById('rekapFilterKategori');
    const resetRekapFilterBtn = document.getElementById('resetRekapFilterBtn');
    const infoRekap = document.getElementById('infoRekap');
    const exportRekapExcelBtn = document.getElementById('exportRekapExcelBtn');

    // Elemen untuk tambah opsi
    const addSupplierBtn = document.getElementById('addSupplierBtn');
    const supplierAddContainer = document.getElementById('supplierAddContainer');
    const newSupplierInput = document.getElementById('newSupplierInput');
    const saveSupplierBtn = document.getElementById('saveSupplierBtn');
    const cancelSupplierBtn = document.getElementById('cancelSupplierBtn');

    const addBarangBtn = document.getElementById('addBarangBtn');
    const barangAddContainer = document.getElementById('barangAddContainer');
    const newBarangInput = document.getElementById('newBarangInput');
    const saveBarangBtn = document.getElementById('saveBarangBtn');
    const cancelBarangBtn = document.getElementById('cancelBarangBtn');

    const addUnitBtn = document.getElementById('addUnitBtn');
    const unitAddContainer = document.getElementById('unitAddContainer');
    const newUnitInput = document.getElementById('newUnitInput');
    const saveUnitBtn = document.getElementById('saveUnitBtn');
    const cancelUnitBtn = document.getElementById('cancelUnitBtn');

    // Tab Navigation
    const tabBtns = document.querySelectorAll('.tab-btn');
    const tabContents = document.querySelectorAll('.tab-content');

    function showToast(message, isError = false) {
        toastMsgDiv.textContent = message;
        toastMsgDiv.style.backgroundColor = isError ? '#b91c1c' : '#0f2b3d';
        toastMsgDiv.style.opacity = '1';
        setTimeout(() => { toastMsgDiv.style.opacity = '0'; }, 2000);
    }

    function setDefaultDate() {
        if (!tglMasukInput.value) {
            const today = new Date();
            const yyyy = today.getFullYear();
            const mm = String(today.getMonth() + 1).padStart(2, '0');
            const dd = String(today.getDate()).padStart(2, '0');
            tglMasukInput.value = `${yyyy}-${mm}-${dd}`;
        }
    }

    function updateAllDropdowns() {
        let supplierHtml = '<option value="" disabled selected>-- Pilih Supplier --</option>';
        masterSuppliers.forEach(sup => { supplierHtml += `<option value="${escapeHtml(sup)}">${escapeHtml(sup)}</option>`; });
        supplierSelect.innerHTML = supplierHtml;

        let barangHtml = '<option value="" disabled selected>-- Pilih Barang --</option>';
        masterBarang.forEach(brg => { barangHtml += `<option value="${escapeHtml(brg)}">${escapeHtml(brg)}</option>`; });
        barangSelect.innerHTML = barangHtml;

        let unitHtml = '<option value="" disabled selected>-- Pilih Satuan --</option>';
        masterUnits.forEach(unit => { unitHtml += `<option value="${escapeHtml(unit)}">${escapeHtml(unit)}</option>`; });
        unitSelect.innerHTML = unitHtml;
    }

    function loadMasterData() {
        const storedSuppliers = localStorage.getItem('master_suppliers');
        masterSuppliers = storedSuppliers ? JSON.parse(storedSuppliers) : ['PT. Maju Jaya', 'CV. Sumber Rezeki', 'UD. Berkah Abadi', 'PT. Indo Makmur'];
        
        const storedBarang = localStorage.getItem('master_barang');
        masterBarang = storedBarang ? JSON.parse(storedBarang) : ['Kertas A4', 'Tinta Printer', 'Box Kardus', 'Plastik Kemasan', 'Stiker Label'];
        
        const storedUnits = localStorage.getItem('master_units');
        if (storedUnits) masterUnits = JSON.parse(storedUnits);
        
        updateAllDropdowns();
    }

    function saveMasterData() {
        localStorage.setItem('master_suppliers', JSON.stringify(masterSuppliers));
        localStorage.setItem('master_barang', JSON.stringify(masterBarang));
        localStorage.setItem('master_units', JSON.stringify(masterUnits));
    }

    function addNewSupplier() {
        const newSupplier = newSupplierInput.value.trim();
        if (!newSupplier) { alert('Masukkan nama supplier'); return; }
        if (!masterSuppliers.includes(newSupplier)) {
            masterSuppliers.push(newSupplier);
            saveMasterData();
            updateAllDropdowns();
            supplierSelect.value = newSupplier;
            showToast(`✅ Supplier "${newSupplier}" berhasil ditambahkan`);
        } else {
            supplierSelect.value = newSupplier;
            showToast(`⚠️ Supplier sudah ada`);
        }
        supplierAddContainer.style.display = 'none';
        newSupplierInput.value = '';
    }

    function addNewBarang() {
        const newBarang = newBarangInput.value.trim();
        if (!newBarang) { alert('Masukkan nama barang'); return; }
        if (!masterBarang.includes(newBarang)) {
            masterBarang.push(newBarang);
            saveMasterData();
            updateAllDropdowns();
            barangSelect.value = newBarang;
            showToast(`✅ Barang "${newBarang}" berhasil ditambahkan`);
        } else {
            barangSelect.value = newBarang;
            showToast(`⚠️ Barang sudah ada`);
        }
        barangAddContainer.style.display = 'none';
        newBarangInput.value = '';
    }

    function addNewUnit() {
        const newUnit = newUnitInput.value.trim().toLowerCase();
        if (!newUnit) { alert('Masukkan satuan'); return; }
        if (!masterUnits.includes(newUnit)) {
            masterUnits.push(newUnit);
            saveMasterData();
            updateAllDropdowns();
            unitSelect.value = newUnit;
            showToast(`✅ Satuan "${newUnit}" berhasil ditambahkan`);
        } else {
            unitSelect.value = newUnit;
            showToast(`⚠️ Satuan sudah ada`);
        }
        unitAddContainer.style.display = 'none';
        newUnitInput.value = '';
    }

    function loadData() {
        const stored = localStorage.getItem('inventory_barang_masuk');
        inventory = stored ? JSON.parse(stored) : [];
        applyFilters();
        renderRekap();
    }

    function saveData() {
        localStorage.setItem('inventory_barang_masuk', JSON.stringify(inventory));
        renderRekap();
    }

    function formatDate(dateStr) {
        if (!dateStr) return '-';
        const parts = dateStr.split('-');
        return parts.length === 3 ? `${parts[2]}/${parts[1]}/${parts[0]}` : dateStr;
    }

    function getMonthYear(dateStr) {
        if (!dateStr) return '';
        const parts = dateStr.split('-');
        if (parts.length !== 3) return '';
        const bulan = new Date(dateStr).toLocaleString('id-ID', { month: 'long' });
        return `${bulan} ${parts[0]}`;
    }

    function escapeHtml(str) {
        if (!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if (m === '&') return '&amp;';
            if (m === '<') return '&lt;';
            if (m === '>') return '&gt;';
            return m;
        });
    }

    // ========== RIWAYAT ==========
    function applyFilters() {
        const searchText = filterText.value.trim().toLowerCase();
        const kategoriValue = filterKategori.value;
        const bulanValue = filterBulan.value;

        filteredInventory = inventory.filter(item => {
            let match = true;
            if (searchText) {
                const inNama = item.namaBarang.toLowerCase().includes(searchText);
                const inSupplier = item.supplier.toLowerCase().includes(searchText);
                if (!inNama && !inSupplier) match = false;
            }
            if (kategoriValue && item.kategori !== kategoriValue) match = false;
            if (bulanValue && item.tanggalMasuk.substring(0,7) !== bulanValue) match = false;
            return match;
        });
        filteredInventory.sort((a, b) => new Date(b.tanggalMasuk) - new Date(a.tanggalMasuk));
        currentPage = 1;
        renderTableWithPagination();
        updateInfoFilter();
    }

    function updateInfoFilter() {
        const totalAll = inventory.length;
        const totalFiltered = filteredInventory.length;
        infoFilter.textContent = (filterText.value || filterKategori.value || filterBulan.value) 
            ? `🔎 Menampilkan ${totalFiltered} dari ${totalAll} data (filter aktif)`
            : `📋 Total semua data: ${totalAll}`;
        totalDataCount.textContent = `${totalAll} item`;
    }

    function renderTableWithPagination() {
        if (filteredInventory.length === 0) {
            tableBody.innerHTML = `<tr class="empty-row"><td colspan="8">${inventory.length === 0 ? 'Belum ada data.' : 'Tidak ada data sesuai pencarian.'}</td></tr>`;
            paginationContainer.innerHTML = '';
            return;
        }

        const grouped = {};
        filteredInventory.forEach(item => {
            const key = item.tanggalMasuk.substring(0, 7);
            const display = getMonthYear(item.tanggalMasuk);
            if (!grouped[key]) grouped[key] = { display, items: [] };
            grouped[key].items.push(item);
        });

        let flatRows = [];
        Object.keys(grouped).sort().reverse().forEach(key => {
            flatRows.push({ type: 'month-header', monthDisplay: grouped[key].display, count: grouped[key].items.length });
            grouped[key].items.forEach(item => flatRows.push({ type: 'data', item }));
        });

        const totalPages = Math.ceil(flatRows.length / rowsPerPage);
        const paginatedRows = flatRows.slice((currentPage-1)*rowsPerPage, currentPage*rowsPerPage);

        let html = '';
        for (const row of paginatedRows) {
            if (row.type === 'month-header') {
                html += `<tr class="month-group"><td colspan="8"><strong>📅 ${escapeHtml(row.monthDisplay)}</strong> (${row.count} item)</td></tr>`;
            } else {
                const item = row.item;
                html += `<tr data-id="${item.id}">
                    <td>${escapeHtml(item.supplier)}</td>
                    <td>${escapeHtml(item.namaBarang)}</td>
                    <td>${escapeHtml(item.kategori)}</td>
                    <td style="text-align:right">${Number(item.jumlah).toLocaleString()}</td>
                    <td>${escapeHtml(item.unit)}</td>
                    <td>${formatDate(item.tanggalMasuk)}</td>
                    <td>${escapeHtml(item.catatan) || '-'}</td>
                    <td class="action-buttons">
                        <button class="edit-btn" data-id="${item.id}" title="Edit">✏️</button>
                        <button class="delete-btn" data-id="${item.id}" title="Hapus">🗑️</button>
                    </td>
                </tr>`;
            }
        }
        tableBody.innerHTML = html;

        document.querySelectorAll('.edit-btn').forEach(btn => {
            btn.addEventListener('click', (e) => { loadItemToForm(btn.getAttribute('data-id')); });
        });
        document.querySelectorAll('.delete-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                if (confirm('Yakin ingin menghapus data ini ?')) deleteItemById(btn.getAttribute('data-id'));
            });
        });

        renderPaginationControls(totalPages);
    }

    function renderPaginationControls(totalPages) {
        if (totalPages <= 1) { paginationContainer.innerHTML = ''; return; }
        let html = `<button class="page-btn" id="firstPage" ${currentPage === 1 ? 'disabled' : ''}>« Pertama</button>
                    <button class="page-btn" id="prevPage" ${currentPage === 1 ? 'disabled' : ''}>‹ Sebelum</button>`;
        for (let i = Math.max(1, currentPage-2); i <= Math.min(totalPages, currentPage+2); i++) {
            html += `<button class="page-btn ${i === currentPage ? 'active' : ''}" data-page="${i}">${i}</button>`;
        }
        html += `<button class="page-btn" id="nextPage" ${currentPage === totalPages ? 'disabled' : ''}>Berikut ›</button>
                 <button class="page-btn" id="lastPage" ${currentPage === totalPages ? 'disabled' : ''}>Terakhir »</button>
                 <span class="page-info">Halaman ${currentPage} dari ${totalPages}</span>`;
        paginationContainer.innerHTML = html;
        
        document.getElementById('firstPage')?.addEventListener('click', () => { currentPage = 1; renderTableWithPagination(); });
        document.getElementById('prevPage')?.addEventListener('click', () => { if (currentPage > 1) { currentPage--; renderTableWithPagination(); } });
        document.getElementById('nextPage')?.addEventListener('click', () => { if (currentPage < totalPages) { currentPage++; renderTableWithPagination(); } });
        document.getElementById('lastPage')?.addEventListener('click', () => { currentPage = totalPages; renderTableWithPagination(); });
        document.querySelectorAll('.page-btn[data-page]').forEach(btn => {
            btn.addEventListener('click', () => { currentPage = parseInt(btn.dataset.page); renderTableWithPagination(); });
        });
    }

    function deleteItemById(id) {
        inventory = inventory.filter(item => item.id !== id);
        saveData();
        if (editMode && editingId === id) cancelEdit();
        applyFilters();
        renderRekap();
        showToast('✅ Data berhasil dihapus');
    }

    function loadItemToForm(id) {
        const item = inventory.find(i => i.id === id);
        if (!item) return;
        editMode = true;
        editingId = id;
        supplierSelect.value = item.supplier;
        barangSelect.value = item.namaBarang;
        kategoriSelect.value = item.kategori;
        jumlahInput.value = item.jumlah;
        unitSelect.value = item.unit;
        tglMasukInput.value = item.tanggalMasuk;
        catatanInput.value = item.catatan || '';
        submitBtn.textContent = '✏️ Simpan Perubahan';
        submitBtn.classList.add('btn-edit-mode');
        cancelEditBtn.style.display = 'inline-block';
        showToast('✏️ Mode Edit aktif', false);
        document.querySelector('.form-card').scrollIntoView({ behavior: 'smooth' });
    }

    function cancelEdit() {
        editMode = false;
        editingId = null;
        resetFormFields();
        submitBtn.textContent = 'Tambah Barang';
        submitBtn.classList.remove('btn-edit-mode');
        cancelEditBtn.style.display = 'none';
        showToast('Mode Edit dibatalkan', false);
    }

    function addOrUpdateItem(event) {
        event.preventDefault();
        const supplier = supplierSelect.value;
        const namaBarang = barangSelect.value;
        const kategori = kategoriSelect.value;
        const jumlah = parseInt(jumlahInput.value);
        const unit = unitSelect.value;
        const tanggalMasuk = tglMasukInput.value;
        const catatan = catatanInput.value.trim();

        if (!supplier || !namaBarang || !kategori || !jumlah || !unit || !tanggalMasuk) {
            alert('⚠️ Harap lengkapi semua field wajib');
            return;
        }
        if (isNaN(jumlah) || jumlah < 1) { alert('Jumlah minimal 1'); return; }

        if (editMode && editingId) {
            const index = inventory.findIndex(i => i.id === editingId);
            if (index !== -1) {
                inventory[index] = { ...inventory[index], supplier, namaBarang, kategori, jumlah, unit, tanggalMasuk, catatan, updatedAt: new Date().toISOString() };
                saveData();
                applyFilters();
                renderRekap();
                showToast('✅ Data berhasil diperbarui!');
                cancelEdit();
            } else { cancelEdit(); }
        } else {
            const newItem = { id: Date.now() + '-' + Math.random().toString(36).substring(2, 8), supplier, namaBarang, kategori, jumlah, unit, tanggalMasuk, catatan: catatan || '', createdAt: new Date().toISOString() };
            inventory.unshift(newItem);
            saveData();
            resetFormFields();
            applyFilters();
            renderRekap();
            showToast('📦 Barang berhasil ditambahkan!');
        }
    }

    function resetFormFields() {
        supplierSelect.value = '';
        barangSelect.value = '';
        kategoriSelect.value = '';
        jumlahInput.value = '';
        unitSelect.value = '';
        catatanInput.value = '';
        setDefaultDate();
    }

    function resetAllFilters() {
        filterText.value = '';
        filterKategori.value = '';
        filterBulan.value = '';
        applyFilters();
    }

    function printTableData() {
        if (filteredInventory.length === 0) { alert('Tidak ada data untuk dicetak'); return; }
        let rowsHtml = '';
        filteredInventory.forEach(item => {
            rowsHtml += `<tr><td>${escapeHtml(item.supplier)}</td><td>${escapeHtml(item.namaBarang)}</td><td>${escapeHtml(item.kategori)}</td><td style="text-align:right">${item.jumlah}</td><td>${escapeHtml(item.unit)}</td><td>${formatDate(item.tanggalMasuk)}</td><td>${escapeHtml(item.catatan) || '-'}</td></tr>`;
        });
        const printWindow = window.open('', '_blank');
        printWindow.document.write(`<html><head><title>Laporan Barang Masuk</title><style>body{font-family:Arial;margin:20px}table{border-collapse:collapse;width:100%}th,td{border:1px solid #aaa;padding:8px;text-align:left}th{background:#eef2f5}</style></head><body><h2>📋 Laporan Penerimaan Barang</h2><p>Tanggal cetak: ${new Date().toLocaleString('id-ID')} | Total: ${filteredInventory.length}</p><table><thead><tr><th>Supplier</th><th>Nama Barang</th><th>Kategori</th><th>Jumlah</th><th>Satuan</th><th>Tgl Masuk</th><th>Catatan</th></tr></thead><tbody>${rowsHtml}</tbody></table></body></html>`);
        printWindow.document.close();
        printWindow.print();
    }

    function exportToExcel() {
        if (filteredInventory.length === 0) { alert('Tidak ada data untuk diekspor'); return; }
        const sheetData = [['Supplier', 'Nama Barang', 'Kategori', 'Jumlah', 'Satuan', 'Tanggal Masuk', 'Catatan']];
        filteredInventory.forEach(item => { sheetData.push([item.supplier, item.namaBarang, item.kategori, item.jumlah, item.unit, formatDate(item.tanggalMasuk), item.catatan || '']); });
        const worksheet = XLSX.utils.aoa_to_sheet(sheetData);
        worksheet['!cols'] = [{wch:25},{wch:30},{wch:20},{wch:12},{wch:10},{wch:15},{wch:25}];
        const workbook = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(workbook, worksheet, 'Barang Masuk');
        XLSX.writeFile(workbook, `Laporan_Barang_Masuk_${new Date().toISOString().slice(0,19).replace(/:/g, '-')}.xlsx`);
        showToast(`📎 Ekspor ${filteredInventory.length} data ke Excel`);
    }

    // ========== REKAP BARANG ==========
    function renderRekap() {
        // Group by namaBarang + satuan (karena satuan bisa berbeda)
        const rekapData = new Map(); // key: "namaBarang||satuan"
        
        inventory.forEach(item => {
            const key = `${item.namaBarang}||${item.unit}`;
            if (!rekapData.has(key)) {
                rekapData.set(key, {
                    namaBarang: item.namaBarang,
                    kategori: item.kategori,
                    satuan: item.unit,
                    total: 0,
                    details: []
                });
            }
            const data = rekapData.get(key);
            data.total += item.jumlah;
            data.details.push({
                tanggal: item.tanggalMasuk,
                jumlah: item.jumlah,
                supplier: item.supplier,
                catatan: item.catatan
            });
        });

        // Konversi ke array dan filter
        let rekapArray = Array.from(rekapData.values());
        
        const searchText = rekapFilterText.value.trim().toLowerCase();
        const kategoriValue = rekapFilterKategori.value;
        
        if (searchText) {
            rekapArray = rekapArray.filter(r => r.namaBarang.toLowerCase().includes(searchText));
        }
        if (kategoriValue) {
            rekapArray = rekapArray.filter(r => r.kategori === kategoriValue);
        }
        
        // Urutkan berdasarkan nama barang
        rekapArray.sort((a, b) => a.namaBarang.localeCompare(b.namaBarang));
        
        rekapCount.textContent = `${rekapArray.length} jenis barang`;
        infoRekap.textContent = `📊 Menampilkan ${rekapArray.length} jenis barang dari ${rekapData.size} total`;
        
        if (rekapArray.length === 0) {
            rekapBody.innerHTML = '<tr class="empty-row"><td colspan="6">⚡ Tidak ada data barang.</td></tr>';
            return;
        }
        
        let html = '';
        rekapArray.forEach((item, idx) => {
            // Buat detail penerimaan
            const detailList = item.details.map(d => {
                return `${formatDate(d.tanggal)}: ${d.jumlah.toLocaleString()} ${item.satuan} (${d.supplier})${d.catatan ? ' - ' + d.catatan : ''}`;
            }).join('<br>');
            
            html += `<tr>
                <td>${idx + 1}</td>
                <td><strong>${escapeHtml(item.namaBarang)}</strong></td>
                <td>${escapeHtml(item.kategori)}</td>
                <td>${escapeHtml(item.satuan)}</td>
                <td style="text-align:right; font-weight:bold; color:#0f2b3d;">${item.total.toLocaleString()}</td>
                <td style="font-size:0.7rem; max-width:250px;">${detailList || '-'}</td>
            </tr>`;
        });
        
        // Tambah baris total keseluruhan
        const grandTotal = rekapArray.reduce((sum, item) => sum + item.total, 0);
        html += `<tr class="total-row"><td colspan="4" style="text-align:right; font-weight:bold;">GRAND TOTAL</td>
                 <td style="text-align:right; font-weight:bold;">${grandTotal.toLocaleString()}</td>
                 <td></td></tr>`;
        
        rekapBody.innerHTML = html;
    }
    
    function resetRekapFilters() {
        rekapFilterText.value = '';
        rekapFilterKategori.value = '';
        renderRekap();
    }
    
    function exportRekapToExcel() {
        if (inventory.length === 0) { alert('Tidak ada data untuk diekspor'); return; }
        
        // Rekalkulasi untuk export
        const rekapData = new Map();
        inventory.forEach(item => {
            const key = `${item.namaBarang}||${item.unit}`;
            if (!rekapData.has(key)) {
                rekapData.set(key, { namaBarang: item.namaBarang, kategori: item.kategori, satuan: item.unit, total: 0 });
            }
            rekapData.get(key).total += item.jumlah;
        });
        
        const sheetData = [['No', 'Nama Barang', 'Kategori', 'Satuan', 'Total Jumlah Masuk']];
        let idx = 1;
        let grandTotal = 0;
        Array.from(rekapData.values()).sort((a,b) => a.namaBarang.localeCompare(b.namaBarang)).forEach(item => {
            sheetData.push([idx++, item.namaBarang, item.kategori, item.satuan, item.total]);
            grandTotal += item.total;
        });
        sheetData.push(['', '', '', 'GRAND TOTAL', grandTotal]);
        
        const worksheet = XLSX.utils.aoa_to_sheet(sheetData);
        worksheet['!cols'] = [{wch:6},{wch:35},{wch:25},{wch:12},{wch:18}];
        const workbook = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(workbook, worksheet, 'Rekap Barang');
        XLSX.writeFile(workbook, `Rekap_Barang_${new Date().toISOString().slice(0,19).replace(/:/g, '-')}.xlsx`);
        showToast(`📎 Ekspor rekap barang ke Excel`);
    }

    // ========== TAB NAVIGATION ==========
    tabBtns.forEach(btn => {
        btn.addEventListener('click', () => {
            const tabId = btn.getAttribute('data-tab');
            tabBtns.forEach(b => b.classList.remove('active'));
            tabContents.forEach(c => c.classList.remove('active'));
            btn.classList.add('active');
            document.getElementById(`tab-${tabId}`).classList.add('active');
            if (tabId === 'rekap') renderRekap();
        });
    });

    // Event Listeners
    form.addEventListener('submit', addOrUpdateItem);
    resetFormBtn.addEventListener('click', () => { if (editMode) cancelEdit(); else resetFormFields(); });
    cancelEditBtn.addEventListener('click', cancelEdit);
    printBtn.addEventListener('click', printTableData);
    exportExcelBtn.addEventListener('click', exportToExcel);
    clearFilterBtn.addEventListener('click', resetAllFilters);
    filterText.addEventListener('input', applyFilters);
    filterKategori.addEventListener('change', applyFilters);
    filterBulan.addEventListener('change', applyFilters);
    
    rekapFilterText.addEventListener('input', renderRekap);
    rekapFilterKategori.addEventListener('change', renderRekap);
    resetRekapFilterBtn.addEventListener('click', resetRekapFilters);
    exportRekapExcelBtn.addEventListener('click', exportRekapToExcel);
    
    // Tambah opsi
    addSupplierBtn.addEventListener('click', () => { supplierAddContainer.style.display = 'flex'; newSupplierInput.focus(); });
    cancelSupplierBtn.addEventListener('click', () => { supplierAddContainer.style.display = 'none'; newSupplierInput.value = ''; });
    saveSupplierBtn.addEventListener('click', addNewSupplier);
    newSupplierInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') addNewSupplier(); });

    addBarangBtn.addEventListener('click', () => { barangAddContainer.style.display = 'flex'; newBarangInput.focus(); });
    cancelBarangBtn.addEventListener('click', () => { barangAddContainer.style.display = 'none'; newBarangInput.value = ''; });
    saveBarangBtn.addEventListener('click', addNewBarang);
    newBarangInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') addNewBarang(); });

    addUnitBtn.addEventListener('click', () => { unitAddContainer.style.display = 'flex'; newUnitInput.focus(); });
    cancelUnitBtn.addEventListener('click', () => { unitAddContainer.style.display = 'none'; newUnitInput.value = ''; });
    saveUnitBtn.addEventListener('click', addNewUnit);
    newUnitInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') addNewUnit(); });

    setDefaultDate();
    loadMasterData();
    loadData();
</script>
</body>
</html>
