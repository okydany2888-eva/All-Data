<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>Smart Stock</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Segoe UI', Roboto, 'Helvetica Neue', system-ui, -apple-system, sans-serif;
            background: #f5f7fb;
            padding: 16px;
            min-width: 480px;
            width: 100%;
            color: #1e293b;
        }

        /* Container utama untuk portrait */
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
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
            margin-bottom: 16px;
            padding-bottom: 12px;
            border-bottom: 2px solid #eef2f6;
        }
        h1 {
            font-size: 1.5rem;
            font-weight: 700;
            color: #0f3b2c;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .global-total {
            background: #e9f4e8;
            padding: 6px 14px;
            border-radius: 40px;
            font-size: 0.8rem;
            font-weight: 600;
            color: #2b6e3c;
        }

        /* Form input - full width untuk portrait */
        .form-card {
            background: #f8fafc;
            border-radius: 24px;
            padding: 18px;
            margin: 16px 0 20px 0;
            border: 1px solid #e2e8f0;
        }
        .form-group {
            margin-bottom: 14px;
        }
        .form-group label {
            display: block;
            font-size: 0.7rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            color: #5b6e8c;
            margin-bottom: 5px;
        }
        .form-group input,
        .form-group select {
            width: 100%;
            padding: 12px 14px;
            font-size: 0.95rem;
            border: 1px solid #cbd5e1;
            border-radius: 18px;
            background: white;
            transition: all 0.2s;
        }
        .form-group input:focus,
        .form-group select:focus {
            border-color: #2c7a4d;
            outline: none;
            box-shadow: 0 0 0 3px rgba(44,122,77,0.15);
        }
        .double-row {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
        }
        .double-row .form-group {
            flex: 1;
            min-width: 120px;
        }
        button {
            background: #2c7a4d;
            border: none;
            color: white;
            font-weight: 600;
            padding: 12px 20px;
            border-radius: 40px;
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.2s;
            width: 100%;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        button:active {
            transform: scale(0.97);
        }

        /* Action bar portrait */
        .action-bar {
            background: white;
            border-radius: 28px;
            padding: 12px 16px;
            margin: 16px 0;
            border: 1px solid #e9edf2;
        }
        .search-section {
            margin-bottom: 12px;
        }
        .search-section input {
            width: 100%;
            padding: 12px 16px;
            border-radius: 40px;
            border: 1px solid #cbd5e1;
            font-size: 0.9rem;
            background: #f8fafc;
        }
        .stats-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin: 12px 0;
            padding: 8px 0;
            border-top: 1px solid #eef2f6;
            border-bottom: 1px solid #eef2f6;
        }
        .total-filter {
            font-weight: 700;
            background: #eef2ff;
            padding: 6px 14px;
            border-radius: 30px;
            font-size: 0.85rem;
        }
        .btn-group {
            display: flex;
            gap: 10px;
            margin-top: 8px;
        }
        .btn-group button {
            flex: 1;
            padding: 10px 12px;
            font-size: 0.8rem;
        }
        .btn-outline {
            background: white;
            border: 1px solid #cbd5e1;
            color: #1e293b;
        }
        .btn-outline:active {
            background: #f1f5f9;
        }
        .btn-danger {
            background: #dc2626;
        }

        /* Tabel dengan scroll horizontal */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 20px;
            border: 1px solid #eef2f6;
            background: white;
            margin: 12px 0;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.8rem;
            min-width: 520px;
        }
        th {
            background: #eef2f9;
            padding: 12px 8px;
            font-weight: 700;
            color: #1f3a3f;
            text-align: center;
            border-bottom: 1px solid #dce3ec;
        }
        td {
            padding: 10px 6px;
            text-align: center;
            border-bottom: 1px solid #f0f4f9;
        }
        .delete-btn {
            background: none;
            border: none;
            font-size: 1.2rem;
            padding: 5px 10px;
            cursor: pointer;
            color: #b45309;
            width: auto;
        }
        .delete-btn:active {
            background: #fee2e2;
            border-radius: 30px;
        }
        .empty-row td {
            text-align: center;
            padding: 40px;
            color: #94a3b8;
        }
        .badge-unit {
            background: #e2eaf1;
            padding: 3px 8px;
            border-radius: 30px;
            font-size: 0.7rem;
        }

        /* Footer */
        .footer-note {
            font-size: 0.65rem;
            color: #6b7280;
            text-align: center;
            margin-top: 16px;
            padding-top: 12px;
            border-top: 1px solid #eef2f6;
        }

        /* Print styling */
        @media print {
            .no-print, .form-card, .action-bar, .btn-group, .delete-btn {
                display: none !important;
            }
            body {
                background: white;
                padding: 0;
                margin: 0;
                min-width: auto;
            }
            .app-container {
                max-width: 100%;
                padding: 0;
                box-shadow: none;
            }
            table {
                border: 1px solid #ccc;
            }
            th {
                background: #f1f1f1;
            }
        }
    </style>
</head>
<body>
<div class="app-container">
    <div class="header">
        <h1>📦 Smart Stock</h1>
        <span class="global-total" id="globalTotal">Total: 0</span>
    </div>

    <!-- Form Input - Portrait friendly -->
    <div class="form-card no-print">
        <div class="form-group">
            <label>📅 Tanggal</label>
            <input type="date" id="tanggalInput">
        </div>
        <div class="form-group">
            <label>👤 Nama PIC</label>
            <input type="text" id="namaInput" placeholder="Masukkan nama..." maxlength="50">
        </div>
        <div class="form-group">
            <label>📦 Nama Barang</label>
            <input type="text" id="barangInput" placeholder="Contoh: Plastik, Magnet, dll" maxlength="60">
        </div>
        <div class="double-row">
            <div class="form-group">
                <label>🔢 Quantity</label>
                <input type="number" id="qtyInput" value="0" min="0" step="0">
            </div>
            <div class="form-group">
                <label>📏 Satuan</label>
                <input type="text" id="unitInput" placeholder="pcs, box, kg" maxlength="20">
            </div>
        </div>
        <div class="form-group">
            <label>✏️ Keterangan</label>
            <input type="text" id="keteranganInput" placeholder="Opsional" maxlength="80">
        </div>
        <button id="btnTambah">➕ Tambah Data</button>
    </div>

    <!-- Action & Search Bar -->
    <div class="action-bar no-print">
        <div class="search-section">
            <input type="text" id="searchInput" placeholder="🔍 Cari nama barang...">
        </div>
        <div class="stats-row">
            <span>📊 Hasil filter:</span>
            <span class="total-filter" id="filterTotalDisplay">Total: 0</span>
        </div>
        <div class="btn-group">
            <button id="printBtn" class="btn-outline">🖨️ Print</button>
            <button id="downloadBtn" class="btn-outline">📁 CSV</button>
            <button id="resetBtn" class="btn-danger">🗑️ Reset</button>
        </div>
    </div>

    <!-- Tabel Data -->
    <div class="table-wrapper">
        <table id="dataTable">
            <thead>
                <tr>
                    <th>Tanggal</th>
                    <th>PIC</th>
                    <th>Nama Barang</th>
                    <th>Qty</th>
                    <th>Satuan</th>
                    <th>Keterangan</th>
                    <th class="no-print">Aksi</th>
                </tr>
            </thead>
            <tbody id="tableBody">
                <tr class="empty-row"><td colspan="7">📭 Belum ada data. Silakan tambah barang.</td></tr>
            </tbody>
        </table>
    </div>

    <div class="footer-note no-print">
        ⚡ Minimal lebar layar: 480px (optimal untuk Android Portrait)<br>
        Data tersimpan otomatis di perangkat Anda
    </div>
</div>

<script>
    // Data storage
    let stockData = [];
    let nextId = 1;

    // DOM elements
    const tanggalInput = document.getElementById('tanggalInput');
    const namaInput = document.getElementById('namaInput');
    const barangInput = document.getElementById('barangInput');
    const qtyInput = document.getElementById('qtyInput');
    const unitInput = document.getElementById('unitInput');
    const keteranganInput = document.getElementById('keteranganInput');
    const btnTambah = document.getElementById('btnTambah');
    const searchInput = document.getElementById('searchInput');
    const tableBody = document.getElementById('tableBody');
    const filterTotalDisplay = document.getElementById('filterTotalDisplay');
    const globalTotal = document.getElementById('globalTotal');
    const printBtn = document.getElementById('printBtn');
    const downloadBtn = document.getElementById('downloadBtn');
    const resetBtn = document.getElementById('resetBtn');

    // Set default tanggal hari ini
    function setDefaultDate() {
        if (!tanggalInput.value) {
            const today = new Date();
            const yyyy = today.getFullYear();
            const mm = String(today.getMonth() + 1).padStart(2, '0');
            const dd = String(today.getDate()).padStart(2, '0');
            tanggalInput.value = `${yyyy}-${mm}-${dd}`;
        }
    }

    // Render tabel dengan filter
    function renderTable() {
        const keyword = searchInput.value.trim().toLowerCase();
        let filteredData = stockData;
        if (keyword !== "") {
            filteredData = stockData.filter(item => item.barang.toLowerCase().includes(keyword));
        }

        // Hitung total quantity filter
        let filterTotalQty = 0;
        filteredData.forEach(item => { filterTotalQty += item.quantity; });
        filterTotalDisplay.innerText = `Total: ${filterTotalQty}`;

        // Hitung global total
        let globalTotalQty = 0;
        stockData.forEach(item => { globalTotalQty += item.quantity; });
        globalTotal.innerText = `Total: ${globalTotalQty}`;

        if (filteredData.length === 0) {
            tableBody.innerHTML = `<tr class="empty-row"><td colspan="7">🔍 Tidak ada data / kosong</td></tr>`;
            return;
        }

        let html = '';
        filteredData.forEach(item => {
            html += `
                <tr>
                    <td>${escapeHtml(item.tanggal)}</td>
                    <td>${escapeHtml(item.nama)}</td>
                    <td style="font-weight:500;">${escapeHtml(item.barang)}</td>
                    <td><strong>${item.quantity}</strong></td>
                    <td><span class="badge-unit">${escapeHtml(item.unit)}</span></td>
                    <td>${escapeHtml(item.keterangan || '-')}</td>
                    <td class="no-print">
                        <button class="delete-btn" data-id="${item.id}" title="Hapus">🗑️</button>
                    </td>
                </tr>
            `;
        });
        tableBody.innerHTML = html;

        // Event listener untuk tombol hapus
        document.querySelectorAll('.delete-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const id = parseInt(btn.getAttribute('data-id'));
                deleteById(id);
            });
        });
    }

    function deleteById(id) {
        if (confirm("Yakin ingin menghapus data ini?")) {
            stockData = stockData.filter(item => item.id !== id);
            if (stockData.length === 0) nextId = 1;
            renderTable();
            saveToLocal();
        }
    }

    function addTransaction() {
        const tanggal = tanggalInput.value.trim();
        const nama = namaInput.value.trim();
        const barang = barangInput.value.trim();
        let quantity = parseInt(qtyInput.value);
        const unit = unitInput.value.trim();
        const keterangan = keteranganInput.value.trim();

        if (!tanggal) { alert("Tanggal wajib diisi!"); return; }
        if (!nama) { alert("Nama PIC harus diisi!"); return; }
        if (!barang) { alert("Nama barang harus diisi!"); return; }
        if (isNaN(quantity) || quantity <= 0) { alert("Quantity harus angka > 0"); return; }
        if (!unit) { alert("Satuan harus diisi!"); return; }

        const newItem = {
            id: nextId++,
            tanggal: tanggal,
            nama: nama,
            barang: barang,
            quantity: quantity,
            unit: unit,
            keterangan: keterangan || ""
        };
        stockData.push(newItem);
        
        // Reset form
        resetForm();
        renderTable();
        saveToLocal();
        alert(`✅ "${barang}" berhasil ditambahkan!`);
    }

    function resetForm() {
        const today = new Date();
        const yyyy = today.getFullYear();
        const mm = String(today.getMonth() + 1).padStart(2, '0');
        const dd = String(today.getDate()).padStart(2, '0');
        tanggalInput.value = `${yyyy}-${mm}-${dd}`;
        namaInput.value = '';
        barangInput.value = '';
        qtyInput.value = '1';
        unitInput.value = 'pcs';
        keteranganInput.value = '';
        namaInput.focus();
    }

    function resetAllData() {
        if (confirm("⚠️ PERINGATAN: Semua data akan dihapus permanen! Lanjutkan?")) {
            stockData = [];
            nextId = 1;
            resetForm();
            renderTable();
            saveToLocal();
            alert("Semua data telah direset.");
        }
    }

    function printData() {
        const keyword = searchInput.value.trim().toLowerCase();
        let dataToPrint = stockData;
        if (keyword !== "") {
            dataToPrint = stockData.filter(item => item.barang.toLowerCase().includes(keyword));
        }
        
        const printWindow = window.open('', '_blank');
        let rowsHtml = '';
        let totalQtyPrint = 0;
        
        dataToPrint.forEach(item => {
            totalQtyPrint += item.quantity;
            rowsHtml += `
                <tr>
                    <td>${escapeHtml(item.tanggal)}</td>
                    <td>${escapeHtml(item.nama)}</td>
                    <td>${escapeHtml(item.barang)}</td>
                    <td style="text-align:center">${item.quantity}</td>
                    <td>${escapeHtml(item.unit)}</td>
                    <td>${escapeHtml(item.keterangan || '-')}</td>
                </tr>
            `;
        });
        
        printWindow.document.write(`
            <html>
            <head>
                <title>Laporan Stock Barang</title>
                <style>
                    body { font-family: system-ui, sans-serif; margin: 20px; }
                    table { border-collapse: collapse; width: 100%; margin-top: 15px; }
                    th, td { border: 1px solid #aaa; padding: 8px; text-align: left; }
                    th { background: #eef2f5; }
                    h2 { color: #2c7a4d; }
                    .header { margin-bottom: 20px; }
                </style>
            </head>
            <body>
                <div class="header">
                    <h2>📋 Laporan Stok Barang</h2>
                    <p>Tanggal cetak: ${new Date().toLocaleString()}</p>
                    <p><strong>Filter pencarian:</strong> ${keyword || 'Semua data'}</p>
                    <p><strong>Total item:</strong> ${dataToPrint.length} | <strong>Total quantity:</strong> ${totalQtyPrint}</p>
                </div>
                <table>
                    <thead>
                        <tr><th>Tanggal</th><th>PIC</th><th>Nama Barang</th><th>Qty</th><th>Satuan</th><th>Keterangan</th></tr>
                    </thead>
                    <tbody>${rowsHtml}</tbody>
                </table>
            </body>
            </html>
        `);
        printWindow.document.close();
        printWindow.print();
    }

    function downloadCSV() {
        const keyword = searchInput.value.trim().toLowerCase();
        let dataToExport = stockData;
        if (keyword !== "") {
            dataToExport = stockData.filter(item => item.barang.toLowerCase().includes(keyword));
        }
        
        const headers = ["Tanggal", "Nama PIC", "Nama Barang", "Quantity", "Satuan", "Keterangan"];
        const rows = dataToExport.map(item => [
            item.tanggal, item.nama, item.barang, item.quantity, item.unit, item.keterangan || ''
        ]);
        
        let csv = headers.join(",") + "\n";
        rows.forEach(row => {
            csv += row.map(cell => `"${String(cell).replace(/"/g, '""')}"`).join(",") + "\n";
        });
        
        const blob = new Blob(["\uFEFF" + csv], {type: "text/csv;charset=utf-8;"});
        const link = document.createElement('a');
        const url = URL.createObjectURL(blob);
        link.href = url;
        link.setAttribute("download", "stock_data_export.csv");
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        URL.revokeObjectURL(url);
    }

    function saveToLocal() {
        localStorage.setItem("smartStockApp", JSON.stringify({ data: stockData, nextId: nextId }));
    }

    function loadFromLocal() {
        const saved = localStorage.getItem("smartStockApp");
        if (saved) {
            try {
                const parsed = JSON.parse(saved);
                if (parsed.data && Array.isArray(parsed.data)) {
                    stockData = parsed.data;
                    nextId = parsed.nextId || (stockData.length > 0 ? Math.max(...stockData.map(i => i.id), 0) + 1 : 1);
                } else {
                    initSampleData();
                }
            } catch(e) { initSampleData(); }
        } else {
            initSampleData();
        }
        renderTable();
    }

    function initSampleData() {
      
