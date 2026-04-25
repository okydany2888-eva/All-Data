<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Barcode Inventory | Satuan & Konversi Unit | Dropdown Barang</title>
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
            min-width: 150px;
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
            th, td { font-size: 0.7rem; padding: 6px 4px; }
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
        .btn-smaller {
            padding: 4px 8px;
            font-size: 0.65rem;
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
                <p>✨ Satuan & Konversi Unit • Dropdown Barang • Stok dalam PCS</p>
            </div>
            <div class="badge" style="background:#ffffff30;">🔍 Scan = keluar otomatis (per pcs)</div>
        </div>
    </div>

    <!-- Form Tambah Stok Barang Masuk dengan Unit -->
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
                <button class="btn btn-primary" id="addStockBtn" style="margin-top: 22px;">📦 Tambah Stok (PCS)</button>
            </div>
        </div>
        <div class="badge" style="background:#e6f0ff;">
            🔑 <strong>Konversi Otomatis:</strong> 1 Box = 1000 pcs, 1 Display = 1000 pcs, 1 Dus = 500 pcs, 1 Karton = 2000 pcs.<br>
            Stok disimpan dalam satuan PCS. Dropdown berisi daftar barang yang sudah ada.
        </div>
    </div>

    <!-- Tabel Detail Barang + Barcode + Download (Tanpa Kolom Aksi) -->
    <div class="card">
        <div class="flex-between">
            <h3>📋 Daftar Barang & Barcode Tetap</h3>
            <div style="display: flex; gap: 8px; flex-wrap: wrap;">
                <button class="btn btn-success btn-sm" id="printAllBarcodeBtn">🖨️ Cetak Semua Barcode</button>
            </div>
        </div>
        <div style="overflow-x: auto;">
            <table id="barangTable">
                <thead>
                    <tr>
                        <th>No</th><th>Nama Barang</th><th>Kode Unik</th><th>Satuan Dasar</th>
                        <th>Total Masuk (PCS)</th><th>Total Keluar (PCS)</th><th>Stok (PCS)</th>
                        <th>Barcode QR</th><th>Download</th>
                    </tr>
                </thead>
                <tbody id="barangBody"></tbody>
            </table>
        </div>
        <div class="info-footer" style="margin-top: 12px;">💡 Stok dalam satuan PCS. Setiap 1 kali scan keluar akan mengurangi 1 PCS. Barcode bersifat tetap per barang.</div>
    </div>

    <!-- Scanner Kamera -->
    <div class="scanner-area">
        <div class="flex-between">
            <div><strong>📷 Scan Barcode QR (Kamera HP)</strong><br><span style="font-size:12px;">Scan 1x = keluar 1 PCS</span></div>
            <div>
                <button id="startScanBtn" class="btn btn-primary btn-sm">▶ Mulai Scan</button>
                <button id="stopScanBtn" class="btn btn-danger btn-sm">⏹ Stop</button>
            </div>
        </div>
        <div id="reader" style="width:100%; max-width:500px; margin:16px auto;"></div>
        <div class="scan-result" id="scanInfo">
            🔍 Hasil scan: <span id="scanMessage">—</span>
        </div>
    </div>

    <!-- Tabel Rekap Stok di Bawah -->
    <div class="card rekapan-card">
        <div class="flex-between">
            <h3>📊 REKAP AKHIR STOK BARANG (dalam PCS)</h3>
            <button class="btn btn-secondary btn-sm" id="exportRekapExcel">📎 Export Rekap Excel</button>
        </div>
        <div style="overflow-x: auto;">
            <table id="rekapTable">
                <thead><tr><th>Nama Barang</th><th>Total Masuk (PCS)</th><th>Total Keluar (PCS)</th><th>Stok Akhir (PCS)</th></tr></thead>
                <tbody id="rekapBody"></tbody>
            </table>
        </div>
    </div>
    <div class="info-footer">💡 Setiap barang memiliki SATU kode barcode tetap. Scan barcode OUT akan mengurangi stok 1 PCS.</div>
</div>

<div id="printArea" style="display: none;"></div>

<script>
    // ==================== DATA MODEL ====================
    // items: { id, name, uniqueCode, totalMasukPcs, totalKeluarPcs, qrDataURL, rawValueOUT, defaultUnit }
    let items = [];

    // Konversi unit ke PCS
    const unitToPcs = {
        'pcs': 1,
        'box': 1000,
        'display': 1000,
        'dus': 500,
        'karton': 2000
    };

    // Helper generate kode unik tetap
    function generateStableUniqueCodeFromName(name) {
        const base = name.toLowerCase().replace(/[^a-z0-9]/g, '');
        const timestamp = Date.now().toString().slice(-6);
        return `INV/${base.substring(0,10)}/${timestamp}`;
    }

    // Generate QR Code
    function generateQRCode(text, size=150) {
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
    async function createNewItem(name, initialPcs) {
        const uniqueCode = generateStableUniqueCodeFromName(name + Date.now());
        const rawValueOUT = `OUT|${uniqueCode}|${name}`;
        const qrDataURL = await generateQRCode(rawValueOUT, 150);
        return {
            id: Date.now() + Math.random(),
            name: name,
            uniqueCode: uniqueCode,
            totalMasukPcs: initialPcs,
            totalKeluarPcs: 0,
            qrDataURL: qrDataURL,
            rawValueOUT: rawValueOUT,
            defaultUnit: 'pcs'
        };
    }

    // Proses scan keluar (kurangi 1 PCS)
    function processScanForOutgoing(scannedText) {
        if (!scannedText || !scannedText.includes('|')) {
            document.getElementById('scanMessage').innerHTML = `❌ Format tidak valid: ${scannedText?.substring(0,50)}`;
            return false;
        }
        const parts = scannedText.split('|');
        if (parts.length < 3) {
            document.getElementById('scanMessage').innerHTML = `❌ Format salah (TIPE|KODE|NAMA)`;
            return false;
        }
        const tipe = parts[0];
        const scannedCode = parts[1];
        const scannedName = parts[2];
        
        const itemIndex = items.findIndex(i => i.uniqueCode === scannedCode);
        if (itemIndex === -1) {
            document.getElementById('scanMessage').innerHTML = `❌ Barang dengan kode ${scannedCode} tidak ditemukan.`;
            return false;
        }
        
        const item = items[itemIndex];
        if (tipe === 'OUT') {
            if (item.totalMasukPcs - item.totalKeluarPcs <= 0) {
                document.getElementById('scanMessage').innerHTML = `⚠️ Stok ${item.name} habis! Tidak bisa keluar.`;
                return false;
            }
            item.totalKeluarPcs += 1; // keluar 1 pcs per scan
            saveToLocal();
            renderAllTables();
            highlightRow(item.id);
            document.getElementById('scanMessage').innerHTML = `✅ KELUAR: ${item.name} (-1 PCS) | Sisa: ${item.totalMasukPcs - item.totalKeluarPcs} PCS`;
            return true;
        } else {
            document.getElementById('scanMessage').innerHTML = `ℹ️ Barcode ${tipe} untuk ${item.name} — hanya info.`;
            return false;
        }
    }

    function highlightRow(itemId) {
        const rows = document.querySelectorAll('#barangBody tr');
        for (let row of rows) {
            if (row.getAttribute('data-item-id') == itemId) {
                row.classList.add('highlight');
                setTimeout(() => row.classList.remove('highlight'), 800);
                break;
            }
        }
    }

    // Tambah Stok Masuk dengan konversi unit
    async function addStockIn(itemName, unitType, unitQuantity) {
        if (!itemName) return false;
        const conversion = unitToPcs[unitType] || 1;
        const addedPcs = unitQuantity * conversion;
        if (addedPcs <= 0) return false;
        
        const existingItem = items.find(i => i.name.toLowerCase() === itemName.toLowerCase());
        if (existingItem) {
            existingItem.totalMasukPcs += addedPcs;
            saveToLocal();
            renderAllTables();
            highlightRow(existingItem.id);
            document.getElementById('scanMessage').innerHTML = `✅ Stok ${existingItem.name} bertambah ${unitQuantity} ${unitType} (${addedPcs} PCS) | Total Masuk: ${existingItem.totalMasukPcs} PCS`;
            return true;
        } else {
            const newItem = await createNewItem(itemName, addedPcs);
            items.push(newItem);
            saveToLocal();
            renderAllTables();
            document.getElementById('scanMessage').innerHTML = `🆕 Barang baru "${itemName}" dibuat. Stok awal: ${addedPcs} PCS (${unitQuantity} ${unitType})`;
            return true;
        }
    }

    // Update dropdown nama barang
    function updateDropdown() {
        const select = document.getElementById('itemNameSelect');
        const currentValue = select.value;
        select.innerHTML = '<option value="">-- Pilih barang yang sudah ada --</option>';
        items.forEach(item => {
            const option = document.createElement('option');
            option.value = item.name;
            option.textContent = `${item.name} (Stok: ${item.totalMasukPcs - item.totalKeluarPcs} PCS)`;
            select.appendChild(option);
        });
        if (currentValue && items.some(i => i.name === currentValue)) select.value = currentValue;
    }

    // Render semua tabel
    async function renderAllTables() {
        renderRekap();
        renderBarangTable();
        updateDropdown();
        updateConversionHint();
    }

    function renderRekap() {
        const tbody = document.getElementById('rekapBody');
        if (!items.length) {
            tbody.innerHTML = '</tr><td colspan="4" style="text-align:center;">Belum ada数据</td></tr>';
            return;
        }
        tbody.innerHTML = '';
        items.forEach(item => {
            const stokAkhir = item.totalMasukPcs - item.totalKeluarPcs;
            const row = tbody.insertRow();
            row.insertCell(0).innerText = item.name;
            row.insertCell(1).innerHTML = `<span class="stok-masuk">${item.totalMasukPcs.toLocaleString()}</span>`;
            row.insertCell(2).innerHTML = `<span class="stok-keluar">${item.totalKeluarPcs.toLocaleString()}</span>`;
            row.insertCell(3).innerHTML = stokAkhir >= 0 ? `<b style="color:#059669;">${stokAkhir.toLocaleString()}</b>` : `<b style="color:#dc2626;">${stokAkhir.toLocaleString()} (minus)</b>`;
        });
    }

    function renderBarangTable() {
        const tbody = document.getElementById('barangBody');
        if (!items.length) {
            tbody.innerHTML = '<tr><td colspan="9" style="text-align:center;">Kosong, tambah stok barang</td></tr>';
            return;
        }
        tbody.innerHTML = '';
        items.forEach((item, idx) => {
            const row = tbody.insertRow();
            row.setAttribute('data-item-id', item.id);
            const stok = item.totalMasukPcs - item.totalKeluarPcs;
            row.insertCell(0).innerText = idx+1;
            row.insertCell(1).innerText = item.name;
            row.insertCell(2).innerHTML = `<code style="background:#f1f5f9; padding:4px 6px; border-radius:10px; font-size:0.7rem;">${item.uniqueCode}</code>`;
            row.insertCell(3).innerText = 'PCS (dasar)';
            row.insertCell(4).innerHTML = `<span class="stok-masuk">${item.totalMasukPcs.toLocaleString()}</span>`;
            row.insertCell(5).innerHTML = `<span class="stok-keluar">${item.totalKeluarPcs.toLocaleString()}</span>`;
            row.insertCell(6).innerHTML = stok >= 0 ? `<b>${stok.toLocaleString()}</b>` : `<b style="color:red;">${stok.toLocaleString()}</b>`;
            
            // barcode
            const imgCell = row.insertCell(7);
            const img = document.createElement('img');
            img.src = item.qrDataURL;
            img.className = 'barcode-img';
            img.title = 'Klik preview - Scan untuk keluar 1 PCS';
            img.onclick = () => {
                const win = window.open();
                win.document.write(`<html><head><title>Barcode ${item.name}</title></head><body style="display:flex;justify-content:center;align-items:center;min-height:100vh;"><div style="background:white;padding:20px;border-radius:28px;text-align:center;"><img src="${item.qrDataURL}" style="width:250px;"><p>${item.rawValueOUT}</p><small>Scan 1x = keluar 1 PCS</small></div></body></html>`);
                win.document.close();
            };
            imgCell.appendChild(img);
            
            // download buttons
            const downloadCell = row.insertCell(8);
            const btnGroup = document.createElement('div');
            btnGroup.style.display = 'flex';
            btnGroup.style.gap = '5px';
            btnGroup.style.flexWrap = 'wrap';
            const btnPng = document.createElement('button');
            btnPng.innerText = 'PNG';
            btnPng.className = 'btn-sm btn-secondary';
            btnPng.style.padding = '4px 8px';
            btnPng.onclick = () => downloadBarcodeAsImage(item.qrDataURL, item.name, 'png');
            const btnJpg = document.createElement('button');
            btnJpg.innerText = 'JPG';
            btnJpg.className = 'btn-sm btn-secondary';
            btnJpg.onclick = () => downloadBarcodeAsImage(item.qrDataURL, item.name, 'jpg');
            const btnPdf = document.createElement('button');
            btnPdf.innerText = 'PDF';
            btnPdf.className = 'btn-sm btn-secondary';
            btnPdf.onclick = () => downloadBarcodeAsPDF(item.qrDataURL, item.name, item.uniqueCode);
            btnGroup.appendChild(btnPng);
            btnGroup.appendChild(btnJpg);
            btnGroup.appendChild(btnPdf);
            downloadCell.appendChild(btnGroup);
        });
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
        if (format === 'png') {
            link.download = `${fileName}_barcode.png`;
            link.href = dataURL;
        } else if (format === 'jpg') {
            const img = new Image();
            img.src = dataURL;
            await new Promise((resolve) => { img.onload = resolve; });
            const canvas = document.createElement('canvas');
            canvas.width = img.width;
            canvas.height = img.height;
            const ctx = canvas.getContext('2d');
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(img, 0, 0);
            const jpgData = canvas.toDataURL('image/jpeg', 0.9);
            link.download = `${fileName}_barcode.jpg`;
            link.href = jpgData;
        }
        link.click();
    }

    async function downloadBarcodeAsPDF(dataURL, name, code) {
        const { jsPDF } = window.jspdf;
        const img = new Image();
        img.src = dataURL;
        await new Promise((resolve) => { img.onload = resolve; });
        const pdf = new jsPDF({ unit: 'mm', format: 'a4', orientation: 'portrait' });
        const imgWidth = 70;
        const imgHeight = (img.height * imgWidth) / img.width;
        const x = (210 - imgWidth) / 2;
        pdf.setFontSize(14);
        pdf.text(`Barcode: ${name}`, 105, 20, { align: 'center' });
        pdf.setFontSize(9);
        pdf.text(`Kode: ${code}`, 105, 30, { align: 'center' });
        pdf.addImage(img, 'PNG', x, 40, imgWidth, imgHeight);
        pdf.text(`Scan untuk keluar 1 PCS`, 105, 40 + imgHeight + 8, { align: 'center' });
        pdf.save(`${name}_barcode.pdf`);
    }

    // Cetak semua barcode
    document.getElementById('printAllBarcodeBtn').addEventListener('click', () => {
        if (!items.length) { alert("Tidak ada barcode"); return; }
        const printDiv = document.getElementById('printArea');
        let html = `<div style="text-align:center;"><h2>Daftar Barcode</h2><p>${new Date().toLocaleString()}</p></div><div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:15px;">`;
        items.forEach(item => {
            html += `<div style="border:1px solid #aaa;border-radius:16px;padding:12px;text-align:center;">
                        <strong>${item.name}</strong><br>
                        <small>${item.uniqueCode}</small><br>
                        <img src="${item.qrDataURL}" width="120" style="margin:8px auto;"><br>
                        <span style="font-size:9px;">${item.rawValueOUT}</span>
                        <div>Stok: ${(item.totalMasukPcs - item.totalKeluarPcs).toLocaleString()} PCS</div>
                    </div>`;
        });
        html += `</div>`;
        printDiv.innerHTML = html;
        window.print();
    });

    // Export Rekap Excel
    document.getElementById('exportRekapExcel').addEventListener('click', () => {
        const wsData = [["Nama Barang", "Total Masuk (PCS)", "Total Keluar (PCS)", "Stok Akhir (PCS)"]];
        items.forEach(item => {
            wsData.push([item.name, item.totalMasukPcs, item.totalKeluarPcs, item.totalMasukPcs - item.totalKeluarPcs]);
        });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Rekap_Stok");
        XLSX.writeFile(wb, `rekap_stok_${new Date().toISOString().slice(0,19)}.xlsx`);
    });

    // Event Tambah Stok
    document.getElementById('addStockBtn').addEventListener('click', async () => {
        let selectedName = document.getElementById('itemNameSelect').value;
        const newNameInput = document.getElementById('newItemName').value.trim();
        let finalName = selectedName;
        if (newNameInput !== "") {
            finalName = newNameInput;
        }
        if (!finalName) {
            alert("Pilih atau ketik nama barang");
            return;
        }
        const unit = document.getElementById('unitSelect').value;
        let unitQty = parseFloat(document.getElementById('unitQty').value);
        if (isNaN(unitQty) || unitQty <= 0) unitQty = 1;
        await addStockIn(finalName, unit, unitQty);
        // reset input baru optional
        document.getElementById('newItemName').value = '';
        document.getElementById('unitQty').value = '1';
    });

    // update hint ketika unit atau qty berubah
    document.getElementById('unitSelect').addEventListener('change', updateConversionHint);
    document.getElementById('unitQty').addEventListener('input', updateConversionHint);

    // Local Storage
    function saveToLocal() {
        localStorage.setItem('barcodeUnitSystem', JSON.stringify(items.map(i => ({
            id: i.id, name: i.name, uniqueCode: i.uniqueCode, totalMasukPcs: i.totalMasukPcs, totalKeluarPcs: i.totalKeluarPcs, qrDataURL: i.qrDataURL, rawValueOUT: i.rawValueOUT
        }))));
    }
    
    function loadFromLocal() {
        const stored = localStorage.getItem('barcodeUnitSystem');
        if (stored && stored.length > 2) {
            items = JSON.parse(stored);
            renderAllTables();
        } else {
            initDemoData();
        }
    }
    
    async function initDemoData() {
        const demo1 = await createNewItem("Speaker JBL", 5000);
        const demo2 = await createNewItem("Mouse Logitech", 3000);
        if (demo1) items.push(demo1);
        if (demo2) items.push(demo2);
        saveToLocal();
        renderAllTables();
    }
    
    // Scanner
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
    
    window.addEventListener('DOMContentLoaded', () => {
        loadFromLocal();
        updateConversionHint();
    });
</script>
</body>
</html>
