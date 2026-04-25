<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Barcode Inventory | Smart URL Barcode + Auto Reduce Stock</title>
    <!-- Library QR Code -->
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <!-- SheetJS untuk export -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <!-- html2canvas + jspdf -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }
        body {
            font-family: system-ui, -apple-system, 'Segoe UI', 'Roboto', sans-serif;
            background: #f1f5f9;
            padding: 12px;
            max-width: 500px;
            margin: 0 auto;
        }
        .container { width: 100%; }
        .card {
            background: white;
            border-radius: 24px;
            padding: 16px;
            margin-bottom: 16px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            border: 1px solid #e2e8f0;
        }
        .flex-between {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
        }
        h1 { font-size: 1.5rem; }
        h3 { font-size: 1.2rem; margin-bottom: 8px; }
        .badge {
            background: #eef2ff;
            padding: 4px 12px;
            border-radius: 40px;
            font-size: 0.7rem;
        }
        .form-grid {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-bottom: 12px;
        }
        .input-group { width: 100%; }
        .input-group label {
            font-size: 0.7rem;
            font-weight: 600;
            text-transform: uppercase;
            color: #475569;
            margin-bottom: 4px;
            display: block;
        }
        .input-group input, .input-group select {
            width: 100%;
            padding: 12px 14px;
            border-radius: 60px;
            border: 1.5px solid #e2e8f0;
            font-size: 0.9rem;
            background: white;
        }
        .btn {
            padding: 12px 18px;
            border-radius: 60px;
            border: none;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            font-size: 0.85rem;
            width: 100%;
        }
        .btn-primary { background: #2563eb; color: white; }
        .btn-success { background: #059669; color: white; }
        .btn-danger { background: #dc2626; color: white; }
        .btn-secondary { background: #475569; color: white; }
        .btn-warning { background: #eab308; color: white; }
        .btn-sm { padding: 8px 12px; font-size: 0.7rem; width: auto; }
        .table-wrapper {
            overflow-x: auto;
            -webkit-overflow-scrolling: touch;
            margin: 0 -4px;
            padding: 0 4px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.7rem;
            min-width: 500px;
        }
        th, td {
            padding: 10px 6px;
            text-align: left;
            border-bottom: 1px solid #e2e8f0;
            vertical-align: middle;
        }
        th { background: #f8fafc; font-weight: 700; }
        .stok-masuk { color: #059669; font-weight: bold; }
        .stok-keluar { color: #dc2626; }
        .barcode-img {
            width: 65px;
            height: 65px;
            cursor: pointer;
            background: white;
            border-radius: 12px;
            border: 1px solid #cbd5e1;
            box-shadow: 0 1px 2px rgba(0,0,0,0.05);
        }
        .rekapan-card { background: linear-gradient(135deg, #fef9e3, #fef3c7); }
        .info-footer {
            text-align: center;
            font-size: 11px;
            color: #64748b;
            margin-top: 16px;
        }
        .unit-hint { font-size: 10px; color: #6b7280; margin-top: 4px; }
        .tab-buttons {
            display: flex;
            gap: 10px;
            margin-bottom: 16px;
        }
        .tab-btn {
            flex: 1;
            text-align: center;
            padding: 10px 0;
            border-radius: 40px;
            background: #e2e8f0;
            cursor: pointer;
            font-weight: 600;
            font-size: 0.8rem;
        }
        .tab-btn.active { background: #2563eb; color: white; }
        .transaction-table { max-height: 350px; overflow-y: auto; }
        .btn-micro { padding: 4px 8px; font-size: 0.6rem; border-radius: 30px; cursor: pointer; }
        .download-btns { display: flex; gap: 5px; flex-wrap: wrap; }
        .url-preview {
            background: #f1f5f9;
            border-radius: 16px;
            padding: 8px;
            font-size: 0.65rem;
            word-break: break-all;
            font-family: monospace;
            margin-top: 8px;
        }
        .search-box {
            margin-bottom: 12px;
            width: 100%;
            padding: 10px 14px;
            border-radius: 60px;
            border: 1.5px solid #e2e8f0;
            font-size: 0.85rem;
        }
        .month-nav {
            display: flex;
            gap: 10px;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 12px;
            flex-wrap: wrap;
        }
        .month-nav select, .month-nav input {
            padding: 8px 12px;
            border-radius: 40px;
            border: 1px solid #cbd5e1;
        }
        .detail-link {
            color: #2563eb;
            cursor: pointer;
            text-decoration: underline;
            font-size: 0.7rem;
        }
        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(0,0,0,0.5);
            justify-content: center;
            align-items: center;
            z-index: 10000;
        }
        .modal-content {
            background: white;
            border-radius: 28px;
            padding: 20px;
            width: 90%;
            max-width: 400px;
            margin: 20px;
            max-height: 80vh;
            overflow-y: auto;
        }
        .action-btns { display: flex; gap: 8px; justify-content: flex-end; margin-top: 16px; }
        .detail-list { font-size: 0.75rem; }
        .detail-list-item { padding: 8px 0; border-bottom: 1px solid #e2e8f0; }
    </style>
</head>
<body>
<div class="container">
    <div class="card" style="background: linear-gradient(135deg, #1e293b, #0f172a); color:white;">
        <div class="flex-between">
            <div><h1>📦 Smart Barcode</h1><p style="font-size: 0.7rem;">URL Barcode • Auto Redirect & Kurangi Stok</p></div>
            <div class="badge" style="background:#ffffff30;">🌐 Scan → Buka Web → Stock -1</div>
        </div>
    </div>

    <!-- Form Tambah Stok Masuk -->
    <div class="card">
        <h3>➕ Tambah Barang / Stok</h3>
        <div class="form-grid">
            <div class="input-group">
                <label>Nama Barang</label>
                <select id="itemNameSelect"><option value="">-- Pilih barang --</option></select>
                <input type="text" id="newItemName" placeholder="Atau ketik nama baru" style="margin-top: 8px;">
            </div>
            <div class="input-group">
                <label>Satuan</label>
                <select id="unitSelect">
                    <option value="pcs">PCS (1 pcs = 1)</option>
                    <option value="box">BOX (1 box = 1000 pcs)</option>
                    <option value="display">DISPLAY (1 display = 1000 pcs)</option>
                    <option value="dus">DUS (1 dus = 500 pcs)</option>
                    <option value="karton">KARTON (1 karton = 2000 pcs)</option>
                </select>
            </div>
            <div class="input-group">
                <label>Jumlah Unit</label>
                <input type="number" id="unitQty" value="1" min="0.5" step="0.5">
                <div class="unit-hint" id="conversionHint">→ 0 pcs</div>
            </div>
            <div class="input-group">
                <label>Tanggal Masuk</label>
                <input type="date" id="masukDate" value="">
            </div>
            <button class="btn btn-primary" id="addStockBtn">📦 Tambah Stok Masuk</button>
        </div>
        <div class="url-preview" id="urlExamplePreview">💡 Barcode akan berisi URL</div>
    </div>

    <!-- Daftar Barang & Barcode URL -->
    <div class="card">
        <div class="flex-between"><h3>📋 Barang & Barcode (URL)</h3><button class="btn-success btn-sm" id="printAllBarcodeBtn" style="width: auto;">🖨️ Cetak</button></div>
        <input type="text" id="searchBarang" class="search-box" placeholder="🔍 Cari barang berdasarkan nama...">
        <div class="table-wrapper">
            <table id="barangTable">
                <thead><tr><th>No</th><th>Nama Barang</th><th>Stok (PCS)</th><th>QR Code (URL)</th><th>Download</th><th>Aksi</th></tr></thead>
                <tbody id="barangBody"></tbody>
            </table>
        </div>
    </div>

    <!-- Tab Transaksi -->
    <div class="card">
        <div class="tab-buttons"><div class="tab-btn active" data-tab="masuk">📥 Masuk</div><div class="tab-btn" data-tab="keluar">📤 Keluar</div></div>
        <div id="masukTab" class="transaction-table"><table style="width:100%"><thead><tr><th>Tgl</th><th>Barang</th><th>Jml (PCS)</th><th>Unit</th></tr></thead><tbody id="masukTableBody"></tbody></table></div>
        <div id="keluarTab" style="display:none;" class="transaction-table"><table style="width:100%"><thead><tr><th>Tgl</th><th>Barang</th><th>Jml (PCS)</th><th>Sumber</th></tr></thead><tbody id="keluarTableBody"></tbody></table></div>
    </div>

    <!-- REKAP STOK TOTAL DENGAN PAGINASI BULAN -->
    <div class="card rekapan-card">
        <div class="flex-between"><h3>📊 REKAP STOK PER BULAN</h3><button class="btn-secondary btn-sm" id="exportRekapExcel">📎 Export Bulan Ini</button></div>
        
        <!-- Filter Bulan & Pencarian -->
        <div class="month-nav">
            <button class="btn-secondary btn-sm" id="prevMonthBtn">◀ Prev</button>
            <div class="flex-between" style="gap: 8px;">
                <select id="monthSelect">
                    <option value="1">Januari</option><option value="2">Februari</option><option value="3">Maret</option>
                    <option value="4">April</option><option value="5">Mei</option><option value="6">Juni</option>
                    <option value="7">Juli</option><option value="8">Agustus</option><option value="9">September</option>
                    <option value="10">Oktober</option><option value="11">November</option><option value="12">Desember</option>
                </select>
                <input type="number" id="yearSelect" min="2020" max="2030" value="2025" style="width: 80px;">
            </div>
            <button class="btn-secondary btn-sm" id="nextMonthBtn">Next ▶</button>
        </div>
        <input type="text" id="searchRekap" class="search-box" placeholder="🔍 Cari barang di rekap...">
        
        <div class="table-wrapper">
            <table id="rekapTable">
                <thead><tr><th>Nama Barang</th><th>Masuk (PCS)</th><th>Keluar (PCS)</th><th>Stok Akhir</th><th>Detail Keluar</th></tr></thead>
                <tbody id="rekapBody"></tbody>
            </table>
        </div>
        <div class="info-footer" style="margin-top: 8px;">📅 Menampilkan transaksi pada bulan yang dipilih</div>
    </div>
    <div class="info-footer">🌐 Setiap barcode berisi URL ke halaman ini dengan parameter action=out&id=... Saat discan pemindai HP → terbuka browser → stok otomatis berkurang 1.</div>
</div>
<div id="printArea" style="display: none;"></div>

<!-- Modal Edit Barang -->
<div id="editModal" class="modal">
    <div class="modal-content">
        <h3 style="margin-bottom: 16px;">✏️ Edit Barang</h3>
        <input type="hidden" id="editItemId">
        <div class="input-group" style="margin-bottom: 12px;">
            <label>Nama Barang</label>
            <input type="text" id="editItemName" placeholder="Nama barang">
        </div>
        <div class="input-group" style="margin-bottom: 12px;">
            <label>Sesuaikan Stok (opsional, PCS)</label>
            <input type="number" id="editStockAdjust" value="0" step="1">
            <small style="font-size:10px;">Isi dengan angka positif untuk tambah stok, negatif untuk kurangi stok</small>
        </div>
        <div class="action-btns">
            <button class="btn-secondary btn-sm" id="closeModalBtn">Batal</button>
            <button class="btn-primary btn-sm" id="saveEditBtn">Simpan</button>
        </div>
    </div>
</div>

<!-- Modal Detail Keluar -->
<div id="detailModal" class="modal">
    <div class="modal-content">
        <h3 style="margin-bottom: 16px;">📋 Detail Transaksi Keluar</h3>
        <div id="detailContent" class="detail-list"></div>
        <div class="action-btns"><button class="btn-secondary btn-sm" id="closeDetailBtn">Tutup</button></div>
    </div>
</div>

<script>
    // ==================== DATA & KONFIGURASI ====================
    let items = [];
    let transactions = [];
    let currentSearchTerm = '';
    let currentRekapSearch = '';
    let currentMonth = new Date().getMonth() + 1;
    let currentYear = new Date().getFullYear();
    const unitToPcs = { 'pcs': 1, 'box': 1000, 'display': 1000, 'dus': 500, 'karton': 2000 };
    
    const BASE_URL = window.location.origin + window.location.pathname;
    
    function generateOutgoingURL(itemId, itemName) {
        const url = new URL(BASE_URL);
        url.searchParams.set('action', 'out');
        url.searchParams.set('id', itemId);
        url.searchParams.set('name', itemName);
        return url.toString();
    }
    
    async function generateQRCodeWithURL(url, size = 160) {
        return new Promise((resolve) => {
            const container = document.createElement('div');
            container.style.width = `${size}px`;
            container.style.height = `${size}px`;
            container.style.backgroundColor = 'white';
            container.style.padding = '8px';
            new QRCode(container, {
                text: url,
                width: size,
                height: size,
                colorDark: "#000000",
                colorLight: "#ffffff",
                correctLevel: QRCode.CorrectLevel.H
            });
            setTimeout(() => {
                let canvas = container.querySelector('canvas');
                if (!canvas) {
                    const fallback = document.createElement('canvas');
                    fallback.width = size; fallback.height = size;
                    const ctx = fallback.getContext('2d');
                    ctx.fillStyle = 'white';
                    ctx.fillRect(0, 0, size, size);
                    ctx.fillStyle = 'black';
                    ctx.fillText("URL QR", size/2-15, size/2);
                    canvas = fallback;
                }
                const finalCanvas = document.createElement('canvas');
                const padding = 12;
                finalCanvas.width = canvas.width + padding * 2;
                finalCanvas.height = canvas.height + padding * 2;
                const ctxFinal = finalCanvas.getContext('2d');
                ctxFinal.fillStyle = '#FFFFFF';
                ctxFinal.fillRect(0, 0, finalCanvas.width, finalCanvas.height);
                ctxFinal.drawImage(canvas, padding, padding, canvas.width, canvas.height);
                resolve(finalCanvas.toDataURL('image/png'));
            }, 100);
        });
    }
    
    async function createItem(name) {
        const itemId = Date.now() + Math.random();
        const itemObject = {
            id: itemId,
            name: name,
            uniqueCode: `INV/${Date.now()}`,
            qrDataURL: null,
            outgoingURL: null
        };
        const url = generateOutgoingURL(itemId, encodeURIComponent(name));
        itemObject.outgoingURL = url;
        const qr = await generateQRCodeWithURL(url, 160);
        itemObject.qrDataURL = qr;
        return itemObject;
    }
    
    async function updateItemQR(item) {
        const newUrl = generateOutgoingURL(item.id, encodeURIComponent(item.name));
        item.outgoingURL = newUrl;
        item.qrDataURL = await generateQRCodeWithURL(newUrl, 160);
    }
    
    function getTotalMasuk(itemId, month = null, year = null) {
        let filtered = transactions.filter(t => t.itemId === itemId && t.type === 'masuk');
        if (month && year) {
            filtered = filtered.filter(t => {
                const [y, m] = t.date.split('-');
                return parseInt(y) === year && parseInt(m) === month;
            });
        }
        return filtered.reduce((s, t) => s + t.quantityPcs, 0);
    }
    
    function getTotalKeluar(itemId, month = null, year = null) {
        let filtered = transactions.filter(t => t.itemId === itemId && t.type === 'keluar');
        if (month && year) {
            filtered = filtered.filter(t => {
                const [y, m] = t.date.split('-');
                return parseInt(y) === year && parseInt(m) === month;
            });
        }
        return filtered.reduce((s, t) => s + t.quantityPcs, 0);
    }
    
    function getKeluarDetails(itemId, month, year) {
        return transactions.filter(t => t.itemId === itemId && t.type === 'keluar' && {
            const [y, m] = t.date.split('-');
            return parseInt(y) === year && parseInt(m) === month;
        });
    }
    
    function getStok(itemId) { return getTotalMasuk(itemId) - getTotalKeluar(itemId); }
    
    async function addMasukTransaction(itemName, unitType, unitQty, date) {
        if (!itemName) return false;
        const pcs = unitQty * (unitToPcs[unitType] || 1);
        if (pcs <= 0) return false;
        let item = items.find(i => i.name.toLowerCase() === itemName.toLowerCase());
        if (!item) { 
            item = await createItem(itemName); 
            items.push(item); 
        }
        transactions.push({ 
            id: Date.now()+Math.random(), 
            itemId: item.id, 
            type: 'masuk', 
            date, 
            quantityPcs: pcs, 
            unitRaw: `${unitQty} ${unitType}`, 
            note: 'Tambah stok' 
        });
        saveToLocal(); 
        renderAll();
        showToast(`✅ +${pcs} PCS ${item.name}`);
        return true;
    }
    
    async function processOutgoing(itemId, source = 'url_scan') {
        const item = items.find(i => i.id == itemId);
        if (!item) {
            showToast(`❌ Barang tidak ditemukan`, true);
            return false;
        }
        if (getStok(item.id) <= 0) {
            showToast(`⚠️ Stok ${item.name} habis!`, true);
            return false;
        }
        const today = new Date().toISOString().slice(0,10);
        transactions.push({
            id: Date.now()+Math.random(),
            itemId: item.id,
            type: 'keluar',
            date: today,
            quantityPcs: 1,
            unitRaw: '1 PCS',
            note: 'Keluar via scan URL barcode',
            source: source
        });
        saveToLocal();
        renderAll();
        showToast(`✅ KELUAR: ${item.name} -1 PCS | Sisa: ${getStok(item.id)} PCS`);
        return true;
    }
    
    function showToast(msg, isError = false) {
        const toastDiv = document.createElement('div');
        toastDiv.innerText = msg;
        toastDiv.style.position = 'fixed';
        toastDiv.style.bottom = '20px';
        toastDiv.style.left = '50%';
        toastDiv.style.transform = 'translateX(-50%)';
        toastDiv.style.backgroundColor = isError ? '#dc2626' : '#059669';
        toastDiv.style.color = 'white';
        toastDiv.style.padding = '10px 20px';
        toastDiv.style.borderRadius = '60px';
        toastDiv.style.fontSize = '0.8rem';
        toastDiv.style.zIndex = '9999';
        toastDiv.style.boxShadow = '0 2px 10px rgba(0,0,0,0.2)';
        document.body.appendChild(toastDiv);
        setTimeout(() => toastDiv.remove(), 2500);
    }
    
    async function handleIncomingURL() {
        const urlParams = new URLSearchParams(window.location.search);
        const action = urlParams.get('action');
        const itemId = urlParams.get('id');
        
        if (action === 'out' && itemId) {
            const newUrl = window.location.pathname;
            window.history.replaceState({}, document.title, newUrl);
            await processOutgoing(itemId, 'url_scan_direct');
        }
    }
    
    // ==================== EDIT & DELETE ====================
    function openEditModal(itemId) {
        const item = items.find(i => i.id == itemId);
        if (!item) return;
        document.getElementById('editItemId').value = item.id;
        document.getElementById('editItemName').value = item.name;
        document.getElementById('editStockAdjust').value = 0;
        document.getElementById('editModal').style.display = 'flex';
    }
    
    async function saveEditItem() {
        const itemId = parseFloat(document.getElementById('editItemId').value);
        const newName = document.getElementById('editItemName').value.trim();
        const stockAdjust = parseInt(document.getElementById('editStockAdjust').value) || 0;
        
        if (!newName) { showToast("Nama barang tidak boleh kosong!", true); return; }
        
        const itemIndex = items.findIndex(i => i.id == itemId);
        if (itemIndex === -1) return;
        
        const duplicate = items.some((i, idx) => idx !== itemIndex && i.name.toLowerCase() === newName.toLowerCase());
        if (duplicate) { showToast(`Nama "${newName}" sudah ada!`, true); return; }
        
        const oldName = items[itemIndex].name;
        items[itemIndex].name = newName;
        await updateItemQR(items[itemIndex]);
        
        if (stockAdjust !== 0) {
            const today = new Date().toISOString().slice(0,10);
            if (stockAdjust > 0) {
                transactions.push({ id: Date.now()+Math.random(), itemId: items[itemIndex].id, type: 'masuk', date: today, quantityPcs: stockAdjust, unitRaw: `${stockAdjust} PCS (penyesuaian)`, note: 'Penyesuaian stok' });
                showToast(`📦 Stok +${stockAdjust} PCS`);
            } else if (stockAdjust < 0) {
                const currentStok = getStok(items[itemIndex].id);
                if (currentStok + stockAdjust < 0) {
                    showToast(`⚠️ Stok tidak cukup! Stok saat ini ${currentStok} PCS.`, true);
                    saveToLocal(); renderAll(); document.getElementById('editModal').style.display = 'none';
                    return;
                }
                transactions.push({ id: Date.now()+Math.random(), itemId: items[itemIndex].id, type: 'keluar', date: today, quantityPcs: Math.abs(stockAdjust), unitRaw: `${Math.abs(stockAdjust)} PCS (penyesuaian)`, note: 'Penyesuaian stok', source: 'edit_manual' });
                showToast(`📤 Stok ${stockAdjust} PCS`);
            }
        }
        saveToLocal(); renderAll(); document.getElementById('editModal').style.display = 'none';
        showToast(`✅ ${oldName} → ${newName}`);
    }
    
    function deleteItem(itemId) {
        if (!confirm("⚠️ Hapus barang & semua riwayatnya?")) return;
        const itemIndex = items.findIndex(i => i.id == itemId);
        if (itemIndex === -1) return;
        const itemName = items[itemIndex].name;
        transactions = transactions.filter(t => t.itemId != itemId);
        items.splice(itemIndex, 1);
        saveToLocal(); renderAll();
        showToast(`🗑️ "${itemName}" telah dihapus.`);
    }
    
    function showKeluarDetail(itemName, keluarTransactions) {
        const detailContent = document.getElementById('detailContent');
        if (!keluarTransactions.length) {
            detailContent.innerHTML = '<p>Tidak ada transaksi keluar pada bulan ini.</p>';
        } else {
            let html = `<p><strong>${itemName}</strong> - Transaksi Keluar:</p>`;
            keluarTransactions.forEach(tr => {
                html += `<div class="detail-list-item">📅 ${tr.date} | ${tr.quantityPcs.toLocaleString()} PCS | ${tr.source === 'url_scan_direct' ? 'Scan URL' : (tr.source === 'edit_manual' ? 'Edit Manual' : 'Manual')}</div>`;
            });
            detailContent.innerHTML = html;
        }
        document.getElementById('detailModal').style.display = 'flex';
    }
    
    // ==================== RENDER UI ====================
    function renderAll() {
        renderBarangTable();
        renderRekapPerBulan();
        renderTransactions();
        updateDropdown();
        updateConversion();
    }
    
    function renderBarangTable() {
        const tbody = document.getElementById('barangBody');
        let filteredItems = items;
        if (currentSearchTerm) filteredItems = items.filter(item => item.name.toLowerCase().includes(currentSearchTerm.toLowerCase()));
        if (!filteredItems.length) { tbody.innerHTML = `<td><td colspan="6" style="text-align:center;">Tidak ada barang</td></tr>`; return; }
        tbody.innerHTML = '';
        filteredItems.forEach((item, idx) => {
            const stok = getStok(item.id);
            const row = tbody.insertRow();
            row.insertCell(0).innerText = idx+1;
            row.insertCell(1).innerText = item.name;
            row.insertCell(2).innerHTML = stok >= 0 ? `<b class="stok-masuk">${stok.toLocaleString()}</b>` : `<b class="stok-keluar">${stok.toLocaleString()}</b>`;
            const imgCell = row.insertCell(3);
            const img = document.createElement('img'); img.src = item.qrDataURL; img.className = 'barcode-img';
            img.onclick = () => { const win = window.open(); win.document.write(`<html><body style="display:flex;justify-content:center;align-items:center;min-height:100vh;"><div style="text-align:center;"><img src="${item.qrDataURL}" width="280"><br><small>${item.outgoingURL}</small></div></body></html>`); win.document.close(); };
            imgCell.appendChild(img);
            const downCell = row.insertCell(4);
            const div = document.createElement('div'); div.className = 'download-btns';
            const png = document.createElement('button'); png.innerText = 'PNG'; png.className = 'btn-micro btn-secondary'; png.onclick = () => downloadImg(item.qrDataURL, item.name);
            const pdf = document.createElement('button'); pdf.innerText = 'PDF'; pdf.className = 'btn-micro btn-secondary'; pdf.onclick = () => downloadPDF(item.qrDataURL, item.name, item.outgoingURL);
            div.appendChild(png); div.appendChild(pdf);
            downCell.appendChild(div);
            const actionCell = row.insertCell(5);
            const editBtn = document.createElement('button'); editBtn.innerText = '✏️'; editBtn.className = 'btn-micro btn-warning'; editBtn.style.marginRight = '5px'; editBtn.onclick = () => openEditModal(item.id);
            const delBtn = document.createElement('button'); delBtn.innerText = '🗑️'; delBtn.className = 'btn-micro btn-danger'; delBtn.onclick = () => deleteItem(item.id);
            actionCell.appendChild(editBtn); actionCell.appendChild(delBtn);
        });
    }
    
    function renderRekapPerBulan() {
        const tbody = document.getElementById('rekapBody');
        let filteredItems = items;
        if (currentRekapSearch) filteredItems = items.filter(item => item.name.toLowerCase().includes(currentRekapSearch.toLowerCase()));
        if (!filteredItems.length) { tbody.innerHTML = `<tr><td colspan="5" style="text-align:center;">Tidak ada data</td></tr>`; return; }
        tbody.innerHTML = '';
        filteredItems.forEach(item => {
            const masuk = getTotalMasuk(item.id, currentMonth, currentYear);
            const keluar = getTotalKeluar(item.id, currentMonth, currentYear);
            const stokAwal = getTotalMasuk(item.id) - getTotalKeluar(item.id) - (masuk - keluar);
            const stokAkhir = stokAwal + masuk - keluar;
            const keluarTransaksi = transactions.filter(t => t.itemId === item.id && t.type === 'keluar' && {
                const [y, m] = t.date.split('-');
                return parseInt(y) === currentYear && parseInt(m) === currentMonth;
            });
            const row = tbody.insertRow();
            row.insertCell(0).innerText = item.name;
            row.insertCell(1).innerHTML = `<span class="stok-masuk">${masuk.toLocaleString()}</span>`;
            row.insertCell(2).innerHTML = `<span class="stok-keluar">${keluar.toLocaleString()}</span>`;
            row.insertCell(3).innerHTML = stokAkhir >= 0 ? `<b style="color:#059669;">${stokAkhir.toLocaleString()}</b>` : `<b style="color:#dc2626;">${stokAkhir.toLocaleString()}</b>`;
            const detailCell = row.insertCell(4);
            if (keluar > 0) {
                const link = document.createElement('span'); link.className = 'detail-link'; link.innerText = `📋 ${keluar} transaksi`; link.onclick = () => showKeluarDetail(item.name, keluarTransaksi);
                detailCell.appendChild(link);
            } else { detailCell.innerText = '-'; }
        });
    }
    
    function renderTransactions() {
        const masukBody = document.getElementById('masukTableBody'); const keluarBody = document.getElementById('keluarTableBody');
        masukBody.innerHTML = ''; keluarBody.innerHTML = '';
        [...transactions].sort((a,b)=>b.date.localeCompare(a.date)).forEach(tr => {
            const item = items.find(i => i.id === tr.itemId);
            if (!item) return;
            if (tr.type === 'masuk') {
                const row = masukBody.insertRow();
                row.insertCell(0).innerText = tr.date; row.insertCell(1).innerText = item.name;
                row.insertCell(2).innerText = tr.quantityPcs.toLocaleString(); row.insertCell(3).innerText = tr.unitRaw || '-';
            } else {
                const row = keluarBody.insertRow();
                row.insertCell(0).innerText = tr.date; row.insertCell(1).innerText = item.name;
                row.insertCell(2).innerText = tr.quantityPcs.toLocaleString();
                row.insertCell(3).innerText = tr.source === 'url_scan_direct' ? 'Scan URL' : (tr.source === 'edit_manual' ? 'Edit' : 'Manual');
            }
        });
    }
    
    function updateDropdown() {
        const select = document.getElementById('itemNameSelect');
        const old = select.value;
        select.innerHTML = '<option value="">-- Pilih barang --</option>';
        items.forEach(i => { const opt = document.createElement('option'); opt.value = i.name; opt.textContent = `${i.name} (${getStok(i.id)} PCS)`; select.appendChild(opt); });
        if (old && items.some(i=>i.name===old)) select.value = old;
    }
    
    function updateConversion() {
        const unit = document.getElementById('unitSelect').value;
        const qty = parseFloat(document.getElementById('unitQty').value) || 0;
        const pcs = qty * (unitToPcs[unit]||1);
        document.getElementById('conversionHint').innerHTML = `→ ${pcs.toLocaleString()} pcs`;
    }
    
    async function downloadImg(dataURL, name) { const a = document.createElement('a'); a.download = `${name}_barcode.png`; a.href = dataURL; a.click(); }
    async function downloadPDF(dataURL, name, url) {
        if (typeof window.jspdf === 'undefined') { showToast("PDF lib gagal", true); return; }
        const { jsPDF } = window.jspdf;
        const img = new Image(); img.src = dataURL;
        await new Promise(r => { img.onload = r; });
        const pdf = new jsPDF({ unit: 'mm', format: 'a4', orientation: 'portrait' });
        const w = 70, h = (img.height * w) / img.width;
        pdf.setFontSize(14); pdf.text(`Barcode: ${name}`, 105, 20, { align: 'center' });
        pdf.addImage(img, 'PNG', (210-w)/2, 30, w, h);
        pdf.setFontSize(8); pdf.text(url, 105, 30+h+8, { align: 'center', maxWidth: 180 });
        pdf.save(`${name}_barcode.pdf`);
    }
    
    document.getElementById('printAllBarcodeBtn').addEventListener('click', () => {
        if (!items.length) { alert("Tidak ada barcode"); return; }
        const printDiv = document.getElementById('printArea');
        let html = `<div style="text-align:center;"><h2>Daftar Barcode URL</h2><p>${new Date().toLocaleString()}</p></div><div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:12px;">`;
        items.forEach(item => { html += `<div style="border:1px solid #aaa;border-radius:16px;padding:12px;text-align:center;"><strong>${item.name}</strong><br><img src="${item.qrDataURL}" width="120"><br><span style="font-size:8px;">${item.outgoingURL}</span><div>Stok: ${getStok(item.id)} PCS</div></div>`; });
        html += `</div>`; printDiv.innerHTML = html; window.print();
    });
    
    document.getElementById('exportRekapExcel').addEventListener('click', () => {
        const wsData = [[`Rekap Stok Bulan ${currentMonth}/${currentYear}`, "", "", ""], ["Nama Barang", "Total Masuk (PCS)", "Total Keluar (PCS)", "Stok Akhir (PCS)"]];
        let filteredItems = items;
        if (currentRekapSearch) filteredItems = items.filter(item => item.name.toLowerCase().includes(currentRekapSearch.toLowerCase()));
        filteredItems.forEach(item => {
            const masuk = getTotalMasuk(item.id, currentMonth, currentYear);
            const keluar = getTotalKeluar(item.id, currentMonth, currentYear);
            const stokAwal = getTotalMasuk(item.id) - getTotalKeluar(item.id) - (masuk - keluar);
            const stokAkhir = stokAwal + masuk - keluar;
            wsData.push([item.name, masuk, keluar, stokAkhir]);
        });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, `Rekap_${currentMonth}_${currentYear}`);
        XLSX.writeFile(wb, `rekap_stok_${currentYear}_${currentMonth}.xlsx`);
    });
    
    document.getElementById('addStockBtn').addEventListener('click', async () => {
        let selected = document.getElementById('itemNameSelect').value;
        let newName = document.getElementById('newItemName').value.trim();
        let finalName = newName || selected;
        if (!finalName) { alert("Pilih atau isi nama barang"); return; }
        const unit = document.getElementById('unitSelect').value;
        let qty = parseFloat(document.getElementById('unitQty').value);
        if (isNaN(qty) || qty <= 0) qty = 1;
        let date = document.getElementById('masukDate').value;
        if (!date) date = new Date().toISOString().slice(0,10);
        await addMasukTransaction(finalName, unit, qty, date);
        document.getElementById('newItemName').value = ''; document.getElementById('unitQty').value = '1';
    });
    
    document.getElementById('searchBarang').addEventListener('input', (e) => { currentSearchTerm = e.target.value; renderBarangTable(); });
    document.getElementById('searchRekap').addEventListener('input', (e) => { currentRekapSearch = e.target.value; renderRekapPerBulan(); });
    
    function changeMonth(delta) {
        let newMonth = currentMonth + delta;
        let newYear = currentYear;
        if (newMonth < 1) { newMonth = 12; newYear--; }
        if (newMonth > 12) { newMonth = 1; newYear++; }
        currentMonth = newMonth; currentYear = newYear;
        document.getElementById('monthSelect').value = currentMonth;
        document.getElementById('yearSelect').value = currentYear;
        renderRekapPerBulan();
    }
    document.getElementById('prevMonthBtn').addEventListener('click', () => changeMonth(-1));
    document.getElementById('nextMonthBtn').addEventListener('click', () => changeMonth(1));
    document.getElementById('monthSelect').addEventListener('change', (e) => { currentMonth = parseInt(e.target.value); renderRekapPerBulan(); });
    document.getElementById('yearSelect').addEventListener('change', (e) => { currentYear = parseInt(e.target.value); renderRekapPerBulan(); });
    
    // Modal handlers
    document.getElementById('closeModalBtn').addEventListener('click', () => { document.getElementById('editModal').style.display = 'none'; });
    document.getElementById('saveEditBtn').addEventListener('click', saveEditItem);
    document.getElementById('closeDetailBtn').addEventListener('click', () => { document.getElementById('detailModal').style.display = 'none'; });
    window.addEventListener('click', (e) => { if (e.target === document.getElementById('editModal')) document.getElementById('editModal').style.display = 'none'; if (e.target === document.getElementById('detailModal')) document.getElementById('detailModal').style.display = 'none'; });
    
    document.querySelectorAll('.tab-btn').forEach(btn => {
        btn.addEventListener('click', () => {
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            const tab = btn.getAttribute('data-tab');
            document.getElementById('masukTab').style.display = tab === 'masuk' ? 'block' : 'none';
            document.getElementById('keluarTab').style.display = tab === 'keluar' ? 'block' : 'none';
        });
    });
    
    function saveToLocal() {
        localStorage.setItem('urlBarcodeInventory', JSON.stringify(items.map(i => ({ id: i.id, name: i.name, uniqueCode: i.uniqueCode, qrDataURL: i.qrDataURL, outgoingURL: i.outgoingURL }))));
        localStorage.setItem('urlBarcodeTransactions', JSON.stringify(transactions));
    }
    
    async function loadFromLocal() {
        const storedItems = localStorage.getItem('urlBarcodeInventory');
        const storedTrans = localStorage.getItem('urlBarcodeTransactions');
        if (storedItems) items = JSON.parse(storedItems);
        if (storedTrans) transactions = JSON.parse(storedTrans);
        let needRecreate = false;
        for (let i = 0; i < items.length; i++) { if (!items[i].qrDataURL || !items[i].outgoingURL) { needRecreate = true; break; } }
        if (items.length === 0 || needRecreate) {
            const demo1 = await createItem("Speaker JBL"); const demo2 = await createItem("Mouse Logitech");
            items = [demo1, demo2];
            transactions = [
                { id: Date.now()+1, itemId: demo1.id, type: 'masuk', date: '2025-01-15', quantityPcs: 5000, unitRaw: '5 Box', note: 'Awal' },
                { id: Date.now()+2, itemId: demo2.id, type: 'masuk', date: '2025-01-16', quantityPcs: 3000, unitRaw: '3 Box', note: 'Awal' },
                { id: Date.now()+3, itemId: demo1.id, type: 'keluar', date: '2025-01-20', quantityPcs: 2, unitRaw: '2 PCS', source: 'url_scan_direct' },
                { id: Date.now()+4, itemId: demo2.id, type: 'keluar', date: '2025-02-05', quantityPcs: 1, unitRaw: '1 PCS', source: 'manual' }
            ];
            saveToLocal();
        }
        renderAll();
    }
    
    document.getElementById('masukDate').value = new Date().toISOString().slice(0,10);
    document.getElementById('unitSelect').addEventListener('change', updateConversion);
    document.getElementById('unitQty').addEventListener('input', updateConversion);
    updateConversion();
    
    loadFromLocal().then(() => { handleIncomingURL(); });
</script>
</body>
</html>
