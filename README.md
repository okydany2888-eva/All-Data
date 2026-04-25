<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Barcode Inventory | Auto Scan Cepat + Portrait</title>
    <!-- Library QR Code -->
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <!-- SheetJS untuk export -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <!-- html2canvas + jspdf -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <!-- Scanner kamera dengan performa tinggi -->
    <script src="https://unpkg.com/html5-qrcode@2.3.8/html5-qrcode.min.js"></script>
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
            min-width: 400px;
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
            width: 50px;
            height: 50px;
            cursor: pointer;
            background: white;
            border-radius: 10px;
            border: 1px solid #e2e8f0;
        }
        .scanner-area {
            background: #0f172a;
            border-radius: 24px;
            padding: 16px;
            color: white;
        }
        .scan-result {
            background: #1e293b;
            border-radius: 16px;
            padding: 10px;
            margin-top: 12px;
            font-family: monospace;
            font-size: 0.75rem;
            word-break: break-all;
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
        .btn-micro { padding: 4px 8px; font-size: 0.6rem; border-radius: 30px; }
        .download-btns { display: flex; gap: 4px; flex-wrap: wrap; }
        button, .tab-btn, .btn, .barcode-img { cursor: pointer; touch-action: manipulation; }
        input, select, button { font-size: 16px; }
        .permission-prompt {
            background: #fef3c7;
            border-left: 4px solid #f59e0b;
            padding: 10px;
            border-radius: 16px;
            margin-bottom: 12px;
            font-size: 0.7rem;
        }
        .scanner-status {
            font-size: 0.7rem;
            background: #1e293b;
            padding: 6px 12px;
            border-radius: 40px;
            display: inline-block;
        }
        #reader video {
            border-radius: 20px;
            width: 100%;
            max-width: 100%;
        }
    </style>
</head>
<body>
<div class="container">
    <!-- Header -->
    <div class="card" style="background: linear-gradient(135deg, #1e293b, #0f172a); color:white;">
        <div class="flex-between">
            <div><h1>📦 Barcode Pro</h1><p style="font-size: 0.7rem;">Auto Scan Cepat • Kamera Langsung Baca QR</p></div>
            <div class="badge" style="background:#ffffff30;">⚡ Real-time Scan</div>
        </div>
    </div>

    <!-- Form Tambah Stok Masuk -->
    <div class="card">
        <h3>➕ Tambah Stok Masuk</h3>
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
    </div>

    <!-- Daftar Barang & Stok -->
    <div class="card">
        <div class="flex-between"><h3>📋 Stok Barang</h3><button class="btn-success btn-sm" id="printAllBarcodeBtn" style="width: auto;">🖨️ Cetak</button></div>
        <div class="table-wrapper">
            <table id="barangTable"><thead><tr><th>No</th><th>Nama</th><th>Stok (PCS)</th><th>Barcode</th><th>Download</th></tr></thead><tbody id="barangBody"></tbody></table>
        </div>
    </div>

    <!-- Tab Transaksi -->
    <div class="card">
        <div class="tab-buttons"><div class="tab-btn active" data-tab="masuk">📥 Masuk</div><div class="tab-btn" data-tab="keluar">📤 Keluar</div></div>
        <div id="masukTab" class="transaction-table"><table><thead><tr><th>Tgl</th><th>Barang</th><th>Jml (PCS)</th><th>Unit</th></tr></thead><tbody id="masukTableBody"></tbody></table></div>
        <div id="keluarTab" style="display:none;" class="transaction-table"><table><thead><tr><th>Tgl</th><th>Barang</th><th>Jml (PCS)</th><th>Sumber</th></tr></thead><tbody id="keluarTableBody"></tbody></table></div>
    </div>

    <!-- Scanner Kamera - AUTO SCAN CEPAT (Begitu QR terlihat langsung terbaca) -->
    <div class="scanner-area">
        <div class="flex-between">
            <strong>📷 Kamera Aktif (Auto Scan)</strong>
            <div style="display: flex; gap: 8px;">
                <button id="startScanBtn" class="btn-primary btn-sm">▶ Nyalakan Kamera</button>
                <button id="stopScanBtn" class="btn-danger btn-sm" disabled>⏹ Matikan</button>
            </div>
        </div>
        <div id="permissionStatus" class="permission-prompt" style="display: none;"></div>
        <div id="reader" style="width:100%; margin-top: 12px;"></div>
        <div class="scan-result" id="scanInfo">🔍 Status: <span id="scanMessage">Tekan 'Nyalakan Kamera' dan izinkan akses. QR akan langsung terbaca otomatis.</span></div>
    </div>

    <!-- Rekap Stok -->
    <div class="card rekapan-card">
        <div class="flex-between"><h3>📊 REKAP STOK TOTAL</h3><button class="btn-secondary btn-sm" id="exportRekapExcel" style="width: auto;">📎 Export</button></div>
        <div class="table-wrapper"><table id="rekapTable"><thead><tr><th>Nama Barang</th><th>Masuk</th><th>Keluar</th><th>Stok</th></tr></thead><tbody id="rekapBody"></tbody></table></div>
    </div>
    <div class="info-footer">💡 Arahkan kamera ke barcode QR (format OUT|KODE|NAMA) → otomatis kurangi stok 1 PCS dalam 1 detik. Scan cepat tanpa tombol tambahan.</div>
</div>
<div id="printArea" style="display: none;"></div>

<script>
    // ==================== DATA & FUNGSI UTAMA ====================
    let items = [];
    let transactions = [];
    const unitToPcs = { 'pcs': 1, 'box': 1000, 'display': 1000, 'dus': 500, 'karton': 2000 };

    function generateUniqueCode(name) {
        const base = name.toLowerCase().replace(/[^a-z0-9]/g, '');
        const time = Date.now().toString().slice(-6);
        return `INV/${base.substring(0,8)}/${time}`;
    }

    async function generateQR(text, size=120) {
        return new Promise((resolve) => {
            const div = document.createElement('div');
            div.style.width = `${size}px`; div.style.height = `${size}px`;
            new QRCode(div, { text: text, width: size, height: size, colorDark: "#000000", colorLight: "#ffffff", correctLevel: QRCode.CorrectLevel.M });
            setTimeout(() => {
                let canvas = div.querySelector('canvas');
                if (!canvas) { canvas = document.createElement('canvas'); canvas.width = size; canvas.height = size; }
                resolve(canvas.toDataURL('image/png'));
            }, 60);
        });
    }

    async function createItem(name) {
        const code = generateUniqueCode(name);
        const rawValue = `OUT|${code}|${name}`;
        const qr = await generateQR(rawValue, 120);
        return { id: Date.now() + Math.random(), name, uniqueCode: code, qrDataURL: qr, rawValueOUT: rawValue };
    }

    function getTotalMasuk(itemId) { return transactions.filter(t => t.itemId === itemId && t.type === 'masuk').reduce((s, t) => s + t.quantityPcs, 0); }
    function getTotalKeluar(itemId) { return transactions.filter(t => t.itemId === itemId && t.type === 'keluar').reduce((s, t) => s + t.quantityPcs, 0); }
    function getStok(itemId) { return getTotalMasuk(itemId) - getTotalKeluar(itemId); }

    async function addMasukTransaction(itemName, unitType, unitQty, date) {
        if (!itemName) return false;
        const pcs = unitQty * (unitToPcs[unitType] || 1);
        if (pcs <= 0) return false;
        let item = items.find(i => i.name.toLowerCase() === itemName.toLowerCase());
        if (!item) { item = await createItem(itemName); items.push(item); }
        transactions.push({ id: Date.now()+Math.random(), itemId: item.id, type: 'masuk', date, quantityPcs: pcs, unitRaw: `${unitQty} ${unitType}`, note: 'Tambah stok' });
        saveToLocal(); renderAll();
        document.getElementById('scanMessage').innerHTML = `✅ +${pcs} PCS ${item.name}`;
        return true;
    }

    async function addKeluarByScan(itemId, date) {
        const item = items.find(i => i.id === itemId);
        if (!item) return false;
        if (getStok(itemId) <= 0) { document.getElementById('scanMessage').innerHTML = `⚠️ Stok ${item.name} habis!`; return false; }
        transactions.push({ id: Date.now()+Math.random(), itemId: itemId, type: 'keluar', date, quantityPcs: 1, unitRaw: '1 PCS', note: 'Auto scan keluar', source: 'kamera' });
        saveToLocal(); renderAll();
        document.getElementById('scanMessage').innerHTML = `✅ [OTOMATIS] Keluar 1 PCS ${item.name} | Sisa: ${getStok(itemId)} PCS`;
        return true;
    }

    function processScan(decodedText) {
        if (!decodedText || !decodedText.includes('|')) { 
            document.getElementById('scanMessage').innerHTML = `❌ Barcode tidak dikenal: ${decodedText?.substring(0,40)}`; 
            return; 
        }
        const parts = decodedText.split('|');
        if (parts.length < 3) return;
        const tipe = parts[0], code = parts[1];
        const item = items.find(i => i.uniqueCode === code);
        if (!item) { document.getElementById('scanMessage').innerHTML = `❌ Kode ${code} tidak ditemukan`; return; }
        if (tipe === 'OUT') {
            const today = new Date().toISOString().slice(0,10);
            addKeluarByScan(item.id, today);
        } else { 
            document.getElementById('scanMessage').innerHTML = `ℹ️ Barcode ${tipe} untuk ${item.name} (tidak mengurangi stok)`;
        }
    }

    // Render semua komponen UI
    function renderAll() {
        renderBarangTable();
        renderRekap();
        renderTransactions();
        updateDropdown();
        updateConversion();
    }

    function renderBarangTable() {
        const tbody = document.getElementById('barangBody');
        if (!items.length) { tbody.innerHTML = '<tr><td colspan="5">Kosong</td></tr>'; return; }
        tbody.innerHTML = '';
        items.forEach((item, idx) => {
            const stok = getStok(item.id);
            const row = tbody.insertRow();
            row.setAttribute('data-item-id', item.id);
            row.insertCell(0).innerText = idx+1;
            row.insertCell(1).innerText = item.name;
            row.insertCell(2).innerHTML = stok >= 0 ? `<b class="stok-masuk">${stok.toLocaleString()}</b>` : `<b class="stok-keluar">${stok.toLocaleString()}</b>`;
            const imgCell = row.insertCell(3);
            const img = document.createElement('img');
            img.src = item.qrDataURL;
            img.className = 'barcode-img';
            img.onclick = () => { const w = window.open(); w.document.write(`<img src="${item.qrDataURL}" style="width:200px;"><p>${item.rawValueOUT}</p>`); w.document.close(); };
            imgCell.appendChild(img);
            const downCell = row.insertCell(4);
            const div = document.createElement('div'); div.className = 'download-btns';
            const png = document.createElement('button'); png.innerText = 'PNG'; png.className = 'btn-micro btn-secondary'; png.onclick = () => downloadImg(item.qrDataURL, item.name, 'png');
            const pdf = document.createElement('button'); pdf.innerText = 'PDF'; pdf.className = 'btn-micro btn-secondary'; pdf.onclick = () => downloadPDF(item.qrDataURL, item.name, item.uniqueCode);
            div.appendChild(png); div.appendChild(pdf);
            downCell.appendChild(div);
        });
    }

    function renderRekap() {
        const tbody = document.getElementById('rekapBody');
        if (!items.length) { tbody.innerHTML = '<tr><td colspan="4">Kosong</td></tr>'; return; }
        tbody.innerHTML = '';
        items.forEach(item => {
            const masuk = getTotalMasuk(item.id), keluar = getTotalKeluar(item.id), stok = masuk - keluar;
            const row = tbody.insertRow();
            row.insertCell(0).innerText = item.name;
            row.insertCell(1).innerHTML = `<span class="stok-masuk">${masuk.toLocaleString()}</span>`;
            row.insertCell(2).innerHTML = `<span class="stok-keluar">${keluar.toLocaleString()}</span>`;
            row.insertCell(3).innerHTML = stok >= 0 ? `<b style="color:#059669;">${stok.toLocaleString()}</b>` : `<b style="color:#dc2626;">${stok.toLocaleString()}</b>`;
        });
    }

    function renderTransactions() {
        const masukBody = document.getElementById('masukTableBody');
        const keluarBody = document.getElementById('keluarTableBody');
        masukBody.innerHTML = ''; keluarBody.innerHTML = '';
        [...transactions].sort((a,b)=>b.date.localeCompare(a.date)).forEach(tr => {
            const item = items.find(i => i.id === tr.itemId);
            if (!item) return;
            if (tr.type === 'masuk') {
                const row = masukBody.insertRow();
                row.insertCell(0).innerText = tr.date;
                row.insertCell(1).innerText = item.name;
                row.insertCell(2).innerText = tr.quantityPcs.toLocaleString();
                row.insertCell(3).innerText = tr.unitRaw || '-';
            } else {
                const row = keluarBody.insertRow();
                row.insertCell(0).innerText = tr.date;
                row.insertCell(1).innerText = item.name;
                row.insertCell(2).innerText = tr.quantityPcs.toLocaleString();
                row.insertCell(3).innerText = tr.source === 'kamera' ? 'AutoScan' : 'Manual';
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

    async function downloadImg(dataURL, name, format) {
        const link = document.createElement('a');
        if (format === 'png') { link.download = `${name}_barcode.png`; link.href = dataURL; }
        else if (format === 'jpg') {
            const img = new Image(); img.src = dataURL;
            await new Promise(r => { img.onload = r; });
            const canvas = document.createElement('canvas'); canvas.width = img.width; canvas.height = img.height;
            const ctx = canvas.getContext('2d'); ctx.fillStyle = 'white'; ctx.fillRect(0,0,canvas.width,canvas.height); ctx.drawImage(img,0,0);
            link.download = `${name}_barcode.jpg`; link.href = canvas.toDataURL('image/jpeg',0.9);
        }
        link.click();
    }
    async function downloadPDF(dataURL, name, code) {
        const { jsPDF } = window.jspdf;
        const img = new Image(); img.src = dataURL;
        await new Promise(r => { img.onload = r; });
        const pdf = new jsPDF({ unit: 'mm', format: 'a4', orientation: 'portrait' });
        const w = 70, h = (img.height * w) / img.width;
        pdf.text(`Barcode: ${name}`, 105, 20, { align: 'center' });
        pdf.addImage(img, 'PNG', (210-w)/2, 30, w, h);
        pdf.text(`Kode: ${code}`, 105, 30+h+5, { align: 'center' });
        pdf.save(`${name}_barcode.pdf`);
    }

    // ================ SCANNER DENGAN AUTO DETEKSI CEPAT ================
    let html5QrCode = null;
    let isScanningActive = false;
    const startBtn = document.getElementById('startScanBtn');
    const stopBtn = document.getElementById('stopScanBtn');
    const readerDiv = document.getElementById('reader');
    const permissionDiv = document.getElementById('permissionStatus');

    async function startAutoScanner() {
        permissionDiv.style.display = 'block';
        permissionDiv.innerHTML = '📷 Meminta akses kamera (wajib izinkan) dan memulai auto-scan...';
        try {
            if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
                throw new Error('Browser tidak mendukung kamera. Gunakan Chrome/Edge di Android.');
            }
            const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
            stream.getTracks().forEach(track => track.stop()); // test izin berhasil
            
            if (html5QrCode && isScanningActive) {
                await html5QrCode.stop();
                html5QrCode.clear();
                html5QrCode = null;
            }
            readerDiv.innerHTML = "";
            html5QrCode = new Html5Qrcode("reader");
            const config = {
                fps: 15,              // frame rate tinggi untuk deteksi cepat
                qrbox: { width: 280, height: 280 },
                aspectRatio: 1.0,
                disableFlip: false,
                experimentalFeatures: { useBarCodeDetectorIfSupported: true }
            };
            await html5QrCode.start(
                { facingMode: "environment" },
                config,
                (decodedText) => {
                    // Ketika QR terdeteksi, langsung proses tanpa jeda
                    processScan(decodedText);
                    // Vibrasi singkat jika support (opsional)
                    if (navigator.vibrate) navigator.vibrate(100);
                },
                (errorMsg) => { 
                    // Abaikan error frame (biasa terjadi karena tidak ada QR)
                    // Tapi tidak mengganggu
                }
            );
            isScanningActive = true;
            startBtn.disabled = true;
            stopBtn.disabled = false;
            permissionDiv.innerHTML = '✅ Kamera aktif - Arahkan ke barcode QR, akan terbaca otomatis!';
            setTimeout(() => permissionDiv.style.display = 'none', 2500);
            document.getElementById('scanMessage').innerHTML = "📷 Kamera menyala - QR akan langsung terbaca saat terlihat";
        } catch (err) {
            console.error(err);
            let errMsg = err.message;
            if (err.name === 'NotAllowedError') errMsg = 'Izin kamera ditolak. Buka pengaturan dan izinkan kamera untuk browser ini.';
            permissionDiv.innerHTML = `❌ Gagal: ${errMsg}`;
            permissionDiv.style.background = '#dc2626';
            permissionDiv.style.color = 'white';
            startBtn.disabled = false;
            stopBtn.disabled = true;
            isScanningActive = false;
        }
    }

    async function stopScanner() {
        if (html5QrCode && isScanningActive) {
            try {
                await html5QrCode.stop();
                html5QrCode.clear();
            } catch(e) {}
            html5QrCode = null;
            isScanningActive = false;
        }
        readerDiv.innerHTML = "";
        startBtn.disabled = false;
        stopBtn.disabled = true;
        document.getElementById('scanMessage').innerHTML = "⏹ Kamera dimatikan. Tekan 'Nyalakan Kamera' untuk memulai auto-scan.";
        permissionDiv.style.display = 'none';
    }

    startBtn.addEventListener('click', startAutoScanner);
    stopBtn.addEventListener('click', stopScanner);

    // Cetak & Export
    document.getElementById('printAllBarcodeBtn').addEventListener('click', () => {
        if (!items.length) { alert("Tidak ada barcode"); return; }
        const printDiv = document.getElementById('printArea');
        let html = `<div style="text-align:center;"><h2>Barcode</h2><p>${new Date().toLocaleString()}</p></div><div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(150px,1fr));gap:12px;">`;
        items.forEach(item => {
            html += `<div style="border:1px solid #aaa;border-radius:16px;padding:10px;text-align:center;"><strong>${item.name}</strong><br><img src="${item.qrDataURL}" width="100"><br><span style="font-size:8px;">${item.rawValueOUT}</span><div>Stok: ${getStok(item.id)} PCS</div></div>`;
        });
        html += `</div>`;
        printDiv.innerHTML = html;
        window.print();
    });

    document.getElementById('exportRekapExcel').addEventListener('click', () => {
        const wsData = [["Nama Barang", "Total Masuk (PCS)", "Total Keluar (PCS)", "Stok Total (PCS)"]];
        items.forEach(i => { wsData.push([i.name, getTotalMasuk(i.id), getTotalKeluar(i.id), getStok(i.id)]); });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Rekap");
        XLSX.writeFile(wb, `rekap_stok_${new Date().toISOString().slice(0,10)}.xlsx`);
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
        localStorage.setItem('autoScanInventory', JSON.stringify(items.map(i => ({ id: i.id, name: i.name, uniqueCode: i.uniqueCode, qrDataURL: i.qrDataURL, rawValueOUT: i.rawValueOUT }))));
        localStorage.setItem('autoScanTransactions', JSON.stringify(transactions));
    }
    async function loadFromLocal() {
        const storedItems = localStorage.getItem('autoScanInventory');
        const storedTrans = localStorage.getItem('autoScanTransactions');
        if (storedItems) items = JSON.parse(storedItems);
        if (storedTrans) transactions = JSON.parse(storedTrans);
        if (items.length === 0) {
            const demo1 = await createItem("Speaker JBL");
            const demo2 = await createItem("Mouse Logitech");
            items = [demo1, demo2];
            transactions = [
                { id: Date.now()+1, itemId: demo1.id, type: 'masuk', date: '2025-01-15', quantityPcs: 5000, unitRaw: '5 Box', note: 'Awal' },
                { id: Date.now()+2, itemId: demo2.id, type: 'masuk', date: '2025-01-16', quantityPcs: 3000, unitRaw: '3 Box', note: 'Awal' }
            ];
            saveToLocal();
        }
        renderAll();
    }

    document.getElementById('masukDate').value = new Date().toISOString().slice(0,10);
    document.getElementById('unitSelect').addEventListener('change', updateConversion);
    document.getElementById('unitQty').addEventListener('input', updateConversion);
    updateConversion();
    loadFromLocal();
</script>
</body>
</html>
