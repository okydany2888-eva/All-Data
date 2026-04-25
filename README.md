<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Barcode Inventory | Transaksi per Tanggal + Stok Total</title>
    <!-- Library QR Code -->
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <!-- SheetJS untuk export -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <!-- html2canvas untuk export PNG/JPG -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <!-- jspdf untuk export PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <!-- Scanner kamera -->
    <script src="https://unpkg.com/html5-qrcode/minified/html5-qrcode.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }
        body {
            font-family: system-ui, 'Segoe UI', sans-serif;
            background: #f1f5f9;
            padding: 16px;
        }
        .container {
            max-width: 1400px;
            margin: 0 auto;
        }
        .card {
            background: white;
            border-radius: 28px;
            padding: 20px;
            margin-bottom: 24px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            border: 1px solid #e2e8f0;
        }
        .flex-between {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 12px;
        }
        h2, h3 {
            font-size: clamp(1.1rem, 5vw, 1.5rem);
        }
        .badge {
            background: #eef2ff;
            padding: 5px 14px;
            border-radius: 40px;
            font-size: 0.75rem;
        }
        .form-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 16px;
            margin-bottom: 16px;
            align-items: flex-end;
        }
        .input-group {
            flex: 1;
            min-width: 140px;
        }
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
            padding: 10px 18px;
            border-radius: 60px;
            border: none;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            font-size: 0.8rem;
        }
        .btn-primary { background: #2563eb; color: white; }
        .btn-success { background: #059669; color: white; }
        .btn-danger { background: #dc2626; color: white; }
        .btn-secondary { background: #475569; color: white; }
        .btn-sm { padding: 6px 12px; font-size: 0.7rem; }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.75rem;
        }
        th, td {
            padding: 10px 6px;
            text-align: left;
            border-bottom: 1px solid #e2e8f0;
            vertical-align: middle;
        }
        th {
            background: #f8fafc;
            font-weight: 700;
        }
        .stok-masuk { color: #059669; font-weight: bold; }
        .stok-keluar { color: #dc2626; }
        .barcode-img {
            width: 55px;
            height: 55px;
            cursor: pointer;
            background: white;
            border-radius: 12px;
            border: 1px solid #e2e8f0;
        }
        .scanner-area {
            background: #0f172a;
            border-radius: 28px;
            padding: 20px;
            color: white;
        }
        .scan-result {
            background: #1e293b;
            border-radius: 20px;
            padding: 12px;
            margin-top: 12px;
            font-family: monospace;
        }
        @media (max-width: 640px) {
            .btn { width: 100%; justify-content: center; }
            .flex-between { flex-direction: column; align-items: stretch; }
            th, td { font-size: 0.65rem; padding: 6px 4px; }
        }
        .rekapan-card {
            background: linear-gradient(135deg, #fef9e3, #fef3c7);
            margin-top: 24px;
        }
        .info-footer {
            text-align: center;
            font-size: 12px;
            color: #64748b;
            margin-top: 20px;
        }
        .highlight {
            background: #fef9c3;
            transition: 0.3s;
        }
        .unit-hint {
            font-size: 11px;
            color: #6b7280;
            margin-top: 4px;
        }
        .tab-buttons {
            display: flex;
            gap: 10px;
            margin-bottom: 16px;
            flex-wrap: wrap;
        }
        .tab-btn {
            padding: 8px 16px;
            border-radius: 40px;
            background: #e2e8f0;
            cursor: pointer;
            font-weight: 600;
            font-size: 0.8rem;
        }
        .tab-btn.active {
            background: #2563eb;
            color: white;
        }
        .transaction-table {
            margin-top: 12px;
            max-height: 400px;
            overflow-y: auto;
        }
    </style>
</head>
<body>
<div class="container">
    <!-- Header -->
    <div class="card" style="background: linear-gradient(135deg, #1e293b, #0f172a); color:white;">
        <div class="flex-between">
            <div>
                <h1>📦 Barcode Inventory Pro</h1>
                <p>✨ Transaksi per Tanggal • Stok Total • Satuan & Konversi Unit</p>
            </div>
            <div class="badge" style="background:#ffffff30;">🔍 Scan = keluar otomatis</div>
        </div>
    </div>

    <!-- Form Tambah Stok Masuk -->
    <div class="card">
        <h3>➕ Tambah Stok Barang Masuk</h3>
        <div class="form-grid">
            <div class="input-group">
                <label>📦 Nama Barang</label>
                <select id="itemNameSelect">
                    <option value="">-- Pilih atau ketik baru --</option>
                </select>
                <input type="text" id="newItemName" placeholder="Atau ketik nama barang baru" style="margin-top: 8px;">
            </div>
            <div class="input-group">
                <label>📏 Unit / Satuan</label>
                <select id="unitSelect">
                    <option value="pcs">PCS (1 pcs = 1)</option>
                    <option value="box">BOX (1 box = 1000 pcs)</option>
                    <option value="display">DISPLAY (1 display = 1000 pcs)</option>
                    <option value="dus">DUS (1 dus = 500 pcs)</option>
                    <option value="karton">KARTON (1 karton = 2000 pcs)</option>
                </select>
            </div>
            <div class="input-group">
                <label>🔢 Jumlah Unit</label>
                <input type="number" id="unitQty" value="1" min="0.5" step="0.5">
                <div class="unit-hint" id="conversionHint">→ 0 pcs</div>
            </div>
            <div class="input-group">
                <label>📅 Tanggal Masuk</label>
                <input type="date" id="masukDate" value="">
            </div>
            <div class="input-group">
                <button class="btn btn-primary" id="addStockBtn" style="margin-top: 22px;">📦 Tambah Stok Masuk</button>
            </div>
        </div>
        <div class="badge" style="background:#e6f0ff;">
            🔑 <strong>Konversi:</strong> 1 Box/Display = 1000 pcs, 1 Dus = 500 pcs, 1 Karton = 2000 pcs. Stok total dihitung otomatis dari semua transaksi.
        </div>
    </div>

    <!-- Tabel Daftar Barang & Stok Total -->
    <div class="card">
        <div class="flex-between">
            <h3>📋 Daftar Barang & Stok Total</h3>
            <div><button class="btn btn-success btn-sm" id="printAllBarcodeBtn">🖨️ Cetak Semua Barcode</button></div>
        </div>
        <div style="overflow-x: auto;">
            <table id="barangTable">
                <thead>
                    <tr><th>No</th><th>Nama Barang</th><th>Kode Unik</th><th>Stok Total (PCS)</th><th>Barcode QR</th><th>Download</th></tr>
                </thead>
                <tbody id="barangBody"></tbody>
            </table>
        </div>
    </div>

    <!-- Transaksi Masuk & Keluar per Tanggal (TAB) -->
    <div class="card">
        <div class="tab-buttons">
            <div class="tab-btn active" data-tab="masuk">📥 Transaksi Masuk</div>
            <div class="tab-btn" data-tab="keluar">📤 Transaksi Keluar</div>
        </div>
        <div id="masukTab" class="transaction-table">
            <table style="width:100%">
                <thead><tr><th>Tanggal</th><th>Nama Barang</th><th>Jumlah (PCS)</th><th>Unit Asal</th><th>Keterangan</th></tr></thead>
                <tbody id="masukTableBody"></tbody>
            </table>
        </div>
        <div id="keluarTab" style="display:none;" class="transaction-table">
            <table style="width:100%">
                <thead><tr><th>Tanggal</th><th>Nama Barang</th><th>Jumlah (PCS)</th><th>Sumber Scan</th><th>Keterangan</th></tr></thead>
                <tbody id="keluarTableBody"></tbody>
            </table>
        </div>
    </div>

    <!-- Scanner Kamera untuk Keluar -->
    <div class="scanner-area">
        <div class="flex-between">
            <div><strong>📷 Scan Barcode QR (Kamera HP)</strong><br><span style="font-size:12px;">Scan 1x = keluar 1 PCS (dicatat per tanggal hari ini)</span></div>
            <div>
                <button id="startScanBtn" class="btn btn-primary btn-sm">▶ Mulai Scan</button>
                <button id="stopScanBtn" class="btn btn-danger btn-sm">⏹ Stop</button>
            </div>
        </div>
        <div id="reader" style="width:100%; max-width:500px; margin:16px auto;"></div>
        <div class="scan-result" id="scanInfo">🔍 Hasil scan: <span id="scanMessage">—</span></div>
    </div>

    <!-- Rekap Stok Total -->
    <div class="card rekapan-card">
        <div class="flex-between">
            <h3>📊 REKAP STOK TOTAL (PCS)</h3>
            <button class="btn btn-secondary btn-sm" id="exportRekapExcel">📎 Export Rekap Excel</button>
        </div>
        <div style="overflow-x: auto;">
            <table id="rekapTable">
                <thead><tr><th>Nama Barang</th><th>Total Masuk (PCS)</th><th>Total Keluar (PCS)</th><th>Stok Total (PCS)</th></tr></thead>
                <tbody id="rekapBody"></tbody>
            </table>
        </div>
    </div>
    <div class="info-footer">💡 Setiap transaksi masuk/keluar dicatat per tanggal. Stok total = akumulasi masuk - akumulasi keluar. Scan keluar otomatis mengurangi stok.</div>
</div>
<div id="printArea" style="display: none;"></div>

<script>
    // ==================== DATA MODEL ====================
    let items = []; // { id, name, uniqueCode, qrDataURL, rawValueOUT }
    let transactions = []; // { id, itemId, type, date, quantityPcs, unitRaw, note, source }
    
    // Konversi unit ke PCS
    const unitToPcs = { 'pcs': 1, 'box': 1000, 'display': 1000, 'dus': 500, 'karton': 2000 };
    
    // Helper generate kode unik tetap
    function generateStableUniqueCodeFromName(name) {
        const base = name.toLowerCase().replace(/[^a-z0-9]/g, '');
        const timestamp = Date.now().toString().slice(-6);
        return `INV/${base.substring(0,10)}/${timestamp}`;
    }
    
    // Generate QR Code
    function generateQRCode(text, size=140) {
        return new Promise((resolve) => {
            const div = document.createElement('div');
            div.style.width = `${size}px`;
            div.style.height = `${size}px`;
            new QRCode(div, {
                text: text,
                width: size,
                height: size,
                colorDark: "#000000",
                colorLight: "#ffffff",
                correctLevel: QRCode.CorrectLevel.M
            });
            setTimeout(() => {
                let canvas = div.querySelector('canvas');
                if (!canvas) {
                    const fake = document.createElement('canvas');
                    fake.width = size; fake.height = size;
                    const ctx = fake.getContext('2d');
                    ctx.fillStyle = 'white';
                    ctx.fillRect(0,0,size,size);
                    ctx.fillStyle = 'black';
                    ctx.fillText("QR", size/2-10, size/2);
                    canvas = fake;
                }
                resolve(canvas.toDataURL('image/png'));
            }, 80);
        });
    }
    
    // Membuat item baru
    async function createNewItem(name) {
        const uniqueCode = generateStableUniqueCodeFromName(name + Date.now());
        const rawValueOUT = `OUT|${uniqueCode}|${name}`;
        const qrDataURL = await generateQRCode(rawValueOUT, 140);
        return {
            id: Date.now() + Math.random(),
            name: name,
            uniqueCode: uniqueCode,
            qrDataURL: qrDataURL,
            rawValueOUT: rawValueOUT
        };
    }
    
    // Hitung total masuk per item
    function getTotalMasuk(itemId) {
        return transactions.filter(t => t.itemId === itemId && t.type === 'masuk').reduce((sum, t) => sum + t.quantityPcs, 0);
    }
    function getTotalKeluar(itemId) {
        return transactions.filter(t => t.itemId === itemId && t.type === 'keluar').reduce((sum, t) => sum + t.quantityPcs, 0);
    }
    function getStokTotal(itemId) {
        return getTotalMasuk(itemId) - getTotalKeluar(itemId);
    }
    
    // Tambah transaksi masuk
    async function addMasukTransaction(itemName, unitType, unitQuantity, date) {
        if (!itemName) return false;
        const conversion = unitToPcs[unitType] || 1;
        const addedPcs = unitQuantity * conversion;
        if (addedPcs <= 0) return false;
        
        let item = items.find(i => i.name.toLowerCase() === itemName.toLowerCase());
        if (!item) {
            item = await createNewItem(itemName);
            items.push(item);
        }
        const transaction = {
            id: Date.now() + Math.random(),
            itemId: item.id,
            type: 'masuk',
            date: date,
            quantityPcs: addedPcs,
            unitRaw: `${unitQuantity} ${unitType}`,
            note: `Tambah stok ${unitQuantity} ${unitType} = ${addedPcs} pcs`
        };
        transactions.push(transaction);
        saveToLocal();
        renderAll();
        document.getElementById('scanMessage').innerHTML = `✅ Masuk: ${itemName} +${addedPcs} PCS (${unitQuantity} ${unitType}) tgl ${date}`;
        return true;
    }
    
    // Tambah transaksi keluar (biasanya dari scan, 1 PCS)
    async function addKeluarTransactionByScan(itemId, date, source = 'scan_kamera') {
        const item = items.find(i => i.id === itemId);
        if (!item) return false;
        const currentStok = getStokTotal(itemId);
        if (currentStok <= 0) {
            document.getElementById('scanMessage').innerHTML = `⚠️ Stok ${item.name} habis! Tidak bisa keluar.`;
            return false;
        }
        const transaction = {
            id: Date.now() + Math.random(),
            itemId: itemId,
            type: 'keluar',
            date: date,
            quantityPcs: 1,
            unitRaw: '1 PCS (scan)',
            note: `Scan keluar otomatis via kamera`
        };
        transactions.push(transaction);
        saveToLocal();
        renderAll();
        document.getElementById('scanMessage').innerHTML = `✅ KELUAR: ${item.name} -1 PCS | Sisa stok: ${getStokTotal(itemId)} PCS`;
        return true;
    }
    
    // Proses scan barcode
    function processScanForOutgoing(scannedText) {
        if (!scannedText || !scannedText.includes('|')) {
            document.getElementById('scanMessage').innerHTML = `❌ Format tidak valid`;
            return false;
        }
        const parts = scannedText.split('|');
        if (parts.length < 3) return false;
        const tipe = parts[0];
        const scannedCode = parts[1];
        const scannedName = parts[2];
        const item = items.find(i => i.uniqueCode === scannedCode);
        if (!item) {
            document.getElementById('scanMessage').innerHTML = `❌ Barang dengan kode ${scannedCode} tidak ditemukan.`;
            return false;
        }
        if (tipe === 'OUT') {
            const today = new Date().toISOString().slice(0,10);
            addKeluarTransactionByScan(item.id, today, 'scan_kamera');
            return true;
        } else {
            document.getElementById('scanMessage').innerHTML = `ℹ️ Barcode ${tipe} untuk ${item.name} — hanya info.`;
            return false;
        }
    }
    
    // Render semua UI
    async function renderAll() {
        renderBarangTable();
        renderRekap();
        renderTransactionsTable();
        updateDropdown();
        updateConversionHint();
    }
    
    function renderBarangTable() {
        const tbody = document.getElementById('barangBody');
        if (!items.length) { tbody.innerHTML = '<tr><td colspan="6">Belum ada data</tr>'; return; }
        tbody.innerHTML = '';
        items.forEach((item, idx) => {
            const stok = getStokTotal(item.id);
            const row = tbody.insertRow();
            row.setAttribute('data-item-id', item.id);
            row.insertCell(0).innerText = idx+1;
            row.insertCell(1).innerText = item.name;
            row.insertCell(2).innerHTML = `<code style="background:#f1f5f9; padding:4px 6px; border-radius:10px;">${item.uniqueCode}</code>`;
            row.insertCell(3).innerHTML = stok >= 0 ? `<b class="stok-masuk">${stok.toLocaleString()}</b>` : `<b class="stok-keluar">${stok.toLocaleString()}</b>`;
            const imgCell = row.insertCell(4);
            const img = document.createElement('img');
            img.src = item.qrDataURL;
            img.className = 'barcode-img';
            img.onclick = () => {
                const win = window.open();
                win.document.write(`<html><body style="display:flex;justify-content:center;align-items:center;min-height:100vh;"><div><img src="${item.qrDataURL}" style="width:250px;"><p>${item.rawValueOUT}</p></div></body></html>`);
                win.document.close();
            };
            imgCell.appendChild(img);
            const downCell = row.insertCell(5);
            const btnGroup = document.createElement('div'); btnGroup.style.display = 'flex'; btnGroup.style.gap = '5px';
            const pngBtn = document.createElement('button'); pngBtn.innerText = 'PNG'; pngBtn.className = 'btn-sm btn-secondary';
            pngBtn.onclick = () => downloadBarcodeAsImage(item.qrDataURL, item.name, 'png');
            const jpgBtn = document.createElement('button'); jpgBtn.innerText = 'JPG'; jpgBtn.className = 'btn-sm btn-secondary';
            jpgBtn.onclick = () => downloadBarcodeAsImage(item.qrDataURL, item.name, 'jpg');
            const pdfBtn = document.createElement('button'); pdfBtn.innerText = 'PDF'; pdfBtn.className = 'btn-sm btn-secondary';
            pdfBtn.onclick = () => downloadBarcodeAsPDF(item.qrDataURL, item.name, item.uniqueCode);
            btnGroup.appendChild(pngBtn); btnGroup.appendChild(jpgBtn); btnGroup.appendChild(pdfBtn);
            downCell.appendChild(btnGroup);
        });
    }
    
    function renderRekap() {
        const tbody = document.getElementById('rekapBody');
        if (!items.length) { tbody.innerHTML = '<tr><td colspan="4">Kosong</tr>'; return; }
        tbody.innerHTML = '';
        items.forEach(item => {
            const totalMasuk = getTotalMasuk(item.id);
            const totalKeluar = getTotalKeluar(item.id);
            const stok = totalMasuk - totalKeluar;
            const row = tbody.insertRow();
            row.insertCell(0).innerText = item.name;
            row.insertCell(1).innerHTML = `<span class="stok-masuk">${totalMasuk.toLocaleString()}</span>`;
            row.insertCell(2).innerHTML = `<span class="stok-keluar">${totalKeluar.toLocaleString()}</span>`;
            row.insertCell(3).innerHTML = stok >= 0 ? `<b style="color:#059669;">${stok.toLocaleString()}</b>` : `<b style="color:#dc2626;">${stok.toLocaleString()}</b>`;
        });
    }
    
    function renderTransactionsTable() {
        const masukBody = document.getElementById('masukTableBody');
        const keluarBody = document.getElementById('keluarTableBody');
        masukBody.innerHTML = '';
        keluarBody.innerHTML = '';
        const sorted = [...transactions].sort((a,b) => b.date.localeCompare(a.date));
        for (let tr of sorted) {
            const item = items.find(i => i.id === tr.itemId);
            const itemName = item ? item.name : 'Hapus';
            if (tr.type === 'masuk') {
                const row = masukBody.insertRow();
                row.insertCell(0).innerText = tr.date;
                row.insertCell(1).innerText = itemName;
                row.insertCell(2).innerText = tr.quantityPcs.toLocaleString();
                row.insertCell(3).innerText = tr.unitRaw;
                row.insertCell(4).innerText = tr.note || '-';
            } else {
                const row = keluarBody.insertRow();
                row.insertCell(0).innerText = tr.date;
                row.insertCell(1).innerText = itemName;
                row.insertCell(2).innerText = tr.quantityPcs.toLocaleString();
                row.insertCell(3).innerText = tr.source === 'scan_kamera' ? 'Scan Kamera' : 'Manual';
                row.insertCell(4).innerText = tr.note || '-';
            }
        }
    }
    
    function updateDropdown() {
        const select = document.getElementById('itemNameSelect');
        const currentVal = select.value;
        select.innerHTML = '<option value="">-- Pilih barang --</option>';
        items.forEach(item => {
            const opt = document.createElement('option');
            opt.value = item.name;
            opt.textContent = `${item.name} (Stok: ${getStokTotal(item.id)} PCS)`;
            select.appendChild(opt);
        });
        if (currentVal && items.some(i => i.name === currentVal)) select.value = currentVal;
    }
    
    function updateConversionHint() {
        const unit = document.getElementById('unitSelect').value;
        const qty = parseFloat(document.getElementById('unitQty').value) || 0;
        const pcs = qty * (unitToPcs[unit] || 1);
        document.getElementById('conversionHint').innerHTML = `→ ${pcs.toLocaleString()} pcs`;
    }
    
    // Download functions
    async function downloadBarcodeAsImage(dataURL, fileName, format) {
        const link = document.createElement('a');
        if (format === 'png') { link.download = `${fileName}_barcode.png`; link.href = dataURL; }
        else if (format === 'jpg') {
            const img = new Image(); img.src = dataURL;
            await new Promise(r => { img.onload = r; });
            const canvas = document.createElement('canvas');
            canvas.width = img.width; canvas.height = img.height;
            const ctx = canvas.getContext('2d');
            ctx.fillStyle = 'white'; ctx.fillRect(0,0,canvas.width,canvas.height);
            ctx.drawImage(img,0,0);
            link.download = `${fileName}_barcode.jpg`; link.href = canvas.toDataURL('image/jpeg',0.9);
        }
        link.click();
    }
    async function downloadBarcodeAsPDF(dataURL, name, code) {
        const { jsPDF } = window.jspdf;
        const img = new Image(); img.src = dataURL;
        await new Promise(r => { img.onload = r; });
        const pdf = new jsPDF({ unit: 'mm', format: 'a4' });
        const imgW = 70; const imgH = (img.height * imgW) / img.width;
        pdf.text(`Barcode: ${name}`, 105, 20, { align: 'center' });
        pdf.addImage(img, 'PNG', (210-imgW)/2, 35, imgW, imgH);
        pdf.text(`Kode: ${code}`, 105, 35+imgH+5, { align: 'center' });
        pdf.save(`${name}_barcode.pdf`);
    }
    
    // Cetak semua barcode
    document.getElementById('printAllBarcodeBtn').addEventListener('click', () => {
        if (!items.length) { alert("Tidak ada barcode"); return; }
        const printDiv = document.getElementById('printArea');
        let html = `<div style="text-align:center;"><h2>Barcode Inventory</h2><p>${new Date().toLocaleString()}</p></div><div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:15px;">`;
        items.forEach(item => {
            html += `<div style="border:1px solid #aaa;border-radius:16px;padding:12px;text-align:center;">
                        <strong>${item.name}</strong><br>
                        <img src="${item.qrDataURL}" width="120"><br>
                        <span style="font-size:9px;">${item.rawValueOUT}</span>
                        <div>Stok: ${getStokTotal(item.id)} PCS</div>
                    </div>`;
        });
        html += `</div>`;
        printDiv.innerHTML = html;
        window.print();
    });
    
    // Export Rekap Excel
    document.getElementById('exportRekapExcel').addEventListener('click', () => {
        const wsData = [["Nama Barang", "Total Masuk (PCS)", "Total Keluar (PCS)", "Stok Total (PCS)"]];
        items.forEach(item => {
            wsData.push([item.name, getTotalMasuk(item.id), getTotalKeluar(item.id), getStokTotal(item.id)]);
        });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Rekap_Stok");
        XLSX.writeFile(wb, `rekap_stok_${new Date().toISOString().slice(0,10)}.xlsx`);
    });
    
    // Event tambah stok masuk
    document.getElementById('addStockBtn').addEventListener('click', async () => {
        let selected = document.getElementById('itemNameSelect').value;
        const newName = document.getElementById('newItemName').value.trim();
        let finalName = newName || selected;
        if (!finalName) { alert("Pilih atau ketik nama barang"); return; }
        const unit = document.getElementById('unitSelect').value;
        let qty = parseFloat(document.getElementById('unitQty').value);
        if (isNaN(qty) || qty <= 0) qty = 1;
        let date = document.getElementById('masukDate').value;
        if (!date) date = new Date().toISOString().slice(0,10);
        await addMasukTransaction(finalName, unit, qty, date);
        document.getElementById('newItemName').value = '';
        document.getElementById('unitQty').value = '1';
    });
    
    // Tab switching
    document.querySelectorAll('.tab-btn').forEach(btn => {
        btn.addEventListener('click', () => {
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            const tab = btn.getAttribute('data-tab');
            document.getElementById('masukTab').style.display = tab === 'masuk' ? 'block' : 'none';
            document.getElementById('keluarTab').style.display = tab === 'keluar' ? 'block' : 'none';
        });
    });
    
    // Local storage
    function saveToLocal() {
        localStorage.setItem('barcodeItemsTrans', JSON.stringify(items.map(i => ({ id: i.id, name: i.name, uniqueCode: i.uniqueCode, qrDataURL: i.qrDataURL, rawValueOUT: i.rawValueOUT }))));
        localStorage.setItem('barcodeTransactions', JSON.stringify(transactions));
    }
    function loadFromLocal() {
        const storedItems = localStorage.getItem('barcodeItemsTrans');
        const storedTrans = localStorage.getItem('barcodeTransactions');
        if (storedItems) items = JSON.parse(storedItems);
        if (storedTrans) transactions = JSON.parse(storedTrans);
        if (items.length === 0) initDemoData();
        else renderAll();
    }
    async function initDemoData() {
        const demo1 = await createNewItem("Speaker JBL");
        const demo2 = await createNewItem("Mouse Logitech");
        items.push(demo1, demo2);
        transactions.push({
            id: Date.now()+1, itemId: demo1.id, type: 'masuk', date: '2025-01-15', quantityPcs: 5000, unitRaw: '5 Box', note: 'Initial stok'
        },{
            id: Date.now()+2, itemId: demo2.id, type: 'masuk', date: '2025-01-16', quantityPcs: 3000, unitRaw: '3 Box', note: 'Initial stok'
        });
        saveToLocal();
        renderAll();
    }
    
    // Scanner camera
    let scanner = null;
    const startScan = document.getElementById('startScanBtn');
    const stopScan = document.getElementById('stopScanBtn');
    const readerDiv = document.getElementById('reader');
    startScan.addEventListener('click', async () => {
        if (scanner) await stopScanHandler();
        readerDiv.innerHTML = "";
        scanner = new Html5Qrcode("reader");
        try {
            await scanner.start({ facingMode: "environment" }, { fps: 10, qrbox: { width: 280, height: 280 } }, 
                (decoded) => { processScanForOutgoing(decoded); }, 
                (err) => {}
            );
            document.getElementById('scanMessage').innerHTML = "📷 Kamera aktif, arahkan ke barcode QR...";
        } catch(e) { document.getElementById('scanMessage').innerHTML = "❌ Gagal kamera: "+e; }
    });
    async function stopScanHandler() {
        if (scanner && scanner.isScanning) { await scanner.stop(); scanner.clear(); scanner = null; }
        readerDiv.innerHTML = "";
        document.getElementById('scanMessage').innerHTML = "⏹ Scanner dihentikan.";
    }
    stopScan.addEventListener('click', stopScanHandler);
    
    // Set default date today
    document.getElementById('masukDate').value = new Date().toISOString().slice(0,10);
    document.getElementById('unitSelect').addEventListener('change', updateConversionHint);
    document.getElementById('unitQty').addEventListener('input', updateConversionHint);
    updateConversionHint();
    loadFromLocal();
</script>
</body>
</html>
