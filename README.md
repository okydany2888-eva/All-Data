<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Barcode Inventory | Rekap Stok di Bawah</title>
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
        }
        .input-group input, .input-group select {
            width: 100%;
            padding: 12px 14px;
            border-radius: 60px;
            border: 1.5px solid #e2e8f0;
            margin-top: 4px;
            font-size: 0.9rem;
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
            font-size: 0.8rem;
        }
        th, td {
            padding: 12px 8px;
            text-align: left;
            border-bottom: 1px solid #e2e8f0;
        }
        th {
            background: #f8fafc;
        }
        .stok-masuk { color: #059669; font-weight: bold; }
        .stok-keluar { color: #dc2626; }
        .barcode-img {
            width: 60px;
            height: 60px;
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
            .action-group { display: flex; flex-direction: column; gap: 8px; }
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
    </style>
</head>
<body>
<div class="container">
    <!-- Header -->
    <div class="card" style="background: linear-gradient(135deg, #1e293b, #0f172a); color:white;">
        <div class="flex-between">
            <div>
                <h1>📦 Barcode Inventory Pro</h1>
                <p>Barang Masuk/Keluar • Scan Otomatis Kurangi Stok • Rekap Stok di Bawah</p>
            </div>
            <div class="badge" style="background:#ffffff30;">🔍 Scan = keluar otomatis</div>
        </div>
    </div>

    <!-- Form Tambah Barang (Barcode otomatis) -->
    <div class="card">
        <h3>Tambah Barang & Barcode (QR Persegi)</h3>
        <div class="form-grid">
            <div class="input-group">
                <label>Nama Barang</label>
                <input type="text" id="itemName" placeholder="Contoh: Headset, Mouse" value="Speaker Bluetooth">
            </div>
            <div class="input-group">
                <label>Kode Unik (Otomatis)</label>
                <input type="text" id="autoCode" readonly>
            </div>
            <div class="input-group">
                <label>Stok Awal (Masuk)</label>
                <input type="number" id="initialStock" value="1" min="1">
            </div>
            <div class="input-group">
                <button class="btn btn-primary" id="addItemBtn" style="margin-top: 22px;">✨ Generate & Simpan</button>
            </div>
        </div>
        <div class="badge" style="background:#e6f0ff;">🔑 Kode unik otomatis: INV/YYMMDD/XXXXX | Barcode QR berisi IN/OUT|KODE|NAMA</div>
    </div>

    <!-- Tabel Detail Barang + Barcode + Download -->
    <div class="card">
        <div class="flex-between">
            <h3>📋 Daftar Barang & Barcode</h3>
            <div style="display: flex; gap: 8px; flex-wrap: wrap;">
                <button class="btn btn-success btn-sm" id="printAllBarcodeBtn">🖨️ Cetak Semua Barcode</button>
            </div>
        </div>
        <div style="overflow-x: auto;">
            <table id="barangTable">
                <thead>
                    <tr><th>No</th><th>Nama Barang</th><th>Kode Unik</th><th>Total Masuk</th><th>Total Keluar</th><th>Barcode QR</th><th>Download Barcode</th><th>Aksi</th></tr>
                </thead>
                <tbody id="barangBody"></tbody>
            </table>
        </div>
        <div class="info-footer" style="margin-top: 12px;">💡 Klik gambar barcode untuk preview besar | Tombol "+ Masuk" untuk tambah stok manual</div>
    </div>

    <!-- Scanner Kamera (Otomatis mengurangi stok jika scan barang keluar) -->
    <div class="scanner-area">
        <div class="flex-between">
            <div><strong>📷 Scan Barcode QR (Kamera HP)</strong><br><span style="font-size:12px;">Barang keluar → stok otomatis berkurang</span></div>
            <div>
                <button id="startScanBtn" class="btn btn-primary btn-sm">▶ Mulai Scan</button>
                <button id="stopScanBtn" class="btn btn-danger btn-sm">⏹ Stop</button>
            </div>
        </div>
        <div id="reader" style="width:100%; max-width:500px; margin:16px auto;"></div>
        <div class="scan-result" id="scanInfo">
            🔍 Hasil scan akan muncul di sini. <br> <span id="scanMessage">—</span>
        </div>
    </div>

    <!-- Tabel Rekap Stok (DIPINDAHKAN KE BAWAH) -->
    <div class="card rekapan-card">
        <div class="flex-between">
            <h3>📊 REKAP AKHIR STOK BARANG</h3>
            <button class="btn btn-secondary btn-sm" id="exportRekapExcel">📎 Export Rekap Excel</button>
        </div>
        <div style="overflow-x: auto;">
            <table id="rekapTable">
                <thead>
                    <tr><th>Nama Barang</th><th>Total Masuk</th><th>Total Keluar</th><th>Stok Akhir</th></tr>
                </thead>
                <tbody id="rekapBody"></tbody>
            </table>
        </div>
        <div class="info-footer" style="margin-top: 12px;">📌 Stok Akhir = Total Masuk - Total Keluar</div>
    </div>

    <div class="info-footer">
        💡 Scan barcode dengan format [OUT|KODE_UNIK|NAMA] akan otomatis mengurangi stok keluar. Barcode format [IN|...] hanya untuk info.
    </div>
</div>

<!-- Area print tersembunyi -->
<div id="printArea" style="display: none;"></div>

<script>
    // ==================== DATA MODEL ====================
    // items: { id, name, uniqueCode, totalMasuk, totalKeluar, qrDataURL, rawValue }
    let items = [];

    // Helper generate kode unik
    function generateUniqueCode() {
        const now = new Date();
        const yy = now.getFullYear().toString().slice(-2);
        const mm = (now.getMonth()+1).toString().padStart(2,'0');
        const dd = now.getDate().toString().padStart(2,'0');
        const rand = Math.floor(Math.random()*100000).toString().padStart(5,'0');
        return `INV/${yy}${mm}${dd}/${rand}`;
    }

    // Generate QR Code (Persegi hitam putih)
    function generateQRCode(text, size=160) {
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

    // Tambah item baru (barcode otomatis dengan prefix IN)
    async function addNewItem(name, uniqueCode, initialMasuk) {
        if (!name || !uniqueCode) return null;
        const rawValue = `IN|${uniqueCode}|${name}`;
        const qrDataURL = await generateQRCode(rawValue);
        const newItem = {
            id: Date.now() + Math.random(),
            name: name,
            uniqueCode: uniqueCode,
            totalMasuk: initialMasuk,
            totalKeluar: 0,
            qrDataURL: qrDataURL,
            rawValue: rawValue
        };
        return newItem;
    }

    // Fungsi untuk mengurangi stok (keluar) berdasarkan scan barcode rawValue
    function processScanForOutgoing(scannedText) {
        if (!scannedText || !scannedText.includes('|')) {
            document.getElementById('scanMessage').innerHTML = `❌ Format barcode tidak valid: ${scannedText.substring(0, 50)}`;
            return false;
        }
        const parts = scannedText.split('|');
        if (parts.length < 3) {
            document.getElementById('scanMessage').innerHTML = `❌ Format tidak sesuai (butuh TIPE|KODE|NAMA)`;
            return false;
        }
        const tipe = parts[0];
        const scannedCode = parts[1];
        const scannedName = parts[2];
        
        // cari item berdasarkan uniqueCode
        const itemIndex = items.findIndex(i => i.uniqueCode === scannedCode);
        if (itemIndex === -1) {
            document.getElementById('scanMessage').innerHTML = `❌ Barang dengan kode ${scannedCode} tidak ditemukan.`;
            return false;
        }
        
        const item = items[itemIndex];
        if (tipe === 'OUT') {
            // Proses BARANG KELUAR: kurangi stok (totalKeluar +1)
            if (item.totalMasuk - item.totalKeluar <= 0) {
                document.getElementById('scanMessage').innerHTML = `⚠️ Stok ${item.name} habis! Tidak bisa keluar.`;
                return false;
            }
            item.totalKeluar += 1;
            saveToLocal();
            renderAllTables();
            document.getElementById('scanMessage').innerHTML = `✅ BARANG KELUAR: ${item.name} | Stok tersisa: ${item.totalMasuk - item.totalKeluar}`;
            return true;
        } 
        else if (tipe === 'IN') {
            document.getElementById('scanMessage').innerHTML = `ℹ️ Barcode MASUK (${item.name}) — Scan ini hanya info, tidak mengubah stok. Gunakan tombol "+ Masuk" untuk menambah stok.`;
            return false;
        } 
        else {
            document.getElementById('scanMessage').innerHTML = `❌ Tipe tidak dikenal: ${tipe}. Harus IN/OUT.`;
            return false;
        }
    }

    // Render semua tabel (rekap + daftar barang)
    async function renderAllTables() {
        renderRekap();
        renderBarangTable();
    }

    function renderRekap() {
        const tbody = document.getElementById('rekapBody');
        if (!items.length) {
            tbody.innerHTML = '<tr><td colspan="4" style="text-align:center;">Belum ada data</td></tr>';
            return;
        }
        tbody.innerHTML = '';
        items.forEach(item => {
            const stokAkhir = item.totalMasuk - item.totalKeluar;
            const row = tbody.insertRow();
            row.insertCell(0).innerText = item.name;
            row.insertCell(1).innerHTML = `<span class="stok-masuk">${item.totalMasuk}</span>`;
            row.insertCell(2).innerHTML = `<span class="stok-keluar">${item.totalKeluar}</span>`;
            row.insertCell(3).innerHTML = stokAkhir >= 0 ? `<b style="color:#059669;">${stokAkhir}</b>` : `<b style="color:#dc2626;">${stokAkhir} (minus)</b>`;
        });
    }

    // Fungsi untuk menambah stok masuk manual
    function addStockInManual(itemId, amount) {
        const item = items.find(i => i.id === itemId);
        if (item && amount > 0) {
            item.totalMasuk += amount;
            saveToLocal();
            renderAllTables();
            alert(`✅ Stok ${item.name} bertambah ${amount}, total masuk sekarang ${item.totalMasuk}`);
        } else if (amount <= 0) {
            alert("Jumlah harus lebih dari 0");
        }
    }

    function renderBarangTable() {
        const tbody = document.getElementById('barangBody');
        if (!items.length) {
            tbody.innerHTML = '<tr><td colspan="8" style="text-align:center;">Kosong, tambah barang terlebih dahulu</td></tr>';
            return;
        }
        tbody.innerHTML = '';
        items.forEach((item, idx) => {
            const row = tbody.insertRow();
            row.insertCell(0).innerText = idx+1;
            row.insertCell(1).innerText = item.name;
            row.insertCell(2).innerHTML = `<code style="background:#f1f5f9; padding:4px 8px; border-radius:12px;">${item.uniqueCode}</code>`;
            row.insertCell(3).innerHTML = `<span class="stok-masuk">${item.totalMasuk}</span>`;
            row.insertCell(4).innerHTML = `<span class="stok-keluar">${item.totalKeluar}</span>`;
            
            // gambar barcode
            const imgCell = row.insertCell(5);
            const img = document.createElement('img');
            img.src = item.qrDataURL;
            img.className = 'barcode-img';
            img.title = 'Klik untuk preview besar';
            img.onclick = () => {
                const win = window.open();
                win.document.write(`<html><head><title>Barcode ${item.name}</title></head><body style="display:flex;justify-content:center;align-items:center;min-height:100vh;background:#f0f2f5;"><div style="background:white;padding:30px;border-radius:32px;text-align:center;"><img src="${item.qrDataURL}" style="width:280px;height:auto;"><p style="margin-top:16px;font-family:monospace;">${item.rawValue}</p></div></body></html>`);
                win.document.close();
            };
            imgCell.appendChild(img);
            
            // tombol download PNG, JPG, PDF
            const downloadCell = row.insertCell(6);
            const btnGroup = document.createElement('div');
            btnGroup.style.display = 'flex';
            btnGroup.style.gap = '6px';
            btnGroup.style.flexWrap = 'wrap';
            
            const btnPng = document.createElement('button');
            btnPng.innerText = 'PNG';
            btnPng.className = 'btn-sm btn-secondary';
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
            
            // Tombol tambah stok masuk manual
            const actionCell = row.insertCell(7);
            const plusBtn = document.createElement('button');
            plusBtn.innerText = '+ Masuk';
            plusBtn.className = 'btn-sm btn-success';
            plusBtn.style.background = '#059669';
            plusBtn.onclick = () => {
                let addQty = prompt(`Tambah stok masuk untuk "${item.name}" (Jumlah):`, "1");
                if (addQty && !isNaN(parseInt(addQty))) {
                    addStockInManual(item.id, parseInt(addQty));
                }
            };
            actionCell.appendChild(plusBtn);
        });
    }

    // Download PNG / JPG
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
        let y = 40;
        pdf.setFontSize(16);
        pdf.text(`Barcode: ${name}`, 105, 20, { align: 'center' });
        pdf.setFontSize(10);
        pdf.text(`Kode Unik: ${code}`, 105, 32, { align: 'center' });
        pdf.addImage(img, 'PNG', x, y, imgWidth, imgHeight);
        pdf.text(`Scan untuk transaksi keluar`, 105, y + imgHeight + 8, { align: 'center' });
        pdf.save(`${name}_barcode.pdf`);
    }

    // Cetak semua barcode (print)
    document.getElementById('printAllBarcodeBtn').addEventListener('click', () => {
        if (!items.length) { alert("Tidak ada barcode untuk dicetak"); return; }
        const printDiv = document.getElementById('printArea');
        let html = `<div style="text-align:center; padding:20px;"><h2>Daftar Barcode Inventaris</h2><p>${new Date().toLocaleString()}</p></div><div style="display:grid; grid-template-columns:repeat(auto-fill,minmax(200px,1fr)); gap:20px;">`;
        items.forEach(item => {
            html += `<div style="border:1px solid #aaa; border-radius: 16px; padding: 12px; text-align:center; page-break-inside:avoid;">
                        <strong>${item.name}</strong><br>
                        <small>${item.uniqueCode}</small><br>
                        <img src="${item.qrDataURL}" width="130" height="130" style="margin:8px auto; background:white;"><br>
                        <span style="font-size:9px;">${item.rawValue}</span>
                    </div>`;
        });
        html += `</div>`;
        printDiv.innerHTML = html;
        window.print();
    });

    // Export rekap excel
    document.getElementById('exportRekapExcel').addEventListener('click', () => {
        const wsData = [["Nama Barang", "Total Masuk", "Total Keluar", "Stok Akhir"]];
        items.forEach(item => {
            wsData.push([item.name, item.totalMasuk, item.totalKeluar, item.totalMasuk - item.totalKeluar]);
        });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Rekap_Stok");
        XLSX.writeFile(wb, `rekap_stok_${new Date().toISOString().slice(0,19)}.xlsx`);
    });

    // Tambah item
    document.getElementById('addItemBtn').addEventListener('click', async () => {
        const name = document.getElementById('itemName').value.trim();
        let autoCode = document.getElementById('autoCode').value.trim();
        const initialStock = parseInt(document.getElementById('initialStock').value) || 1;
        if (!name) { alert("Nama barang wajib diisi"); return; }
        if (!autoCode) autoCode = generateUniqueCode();
        // cek duplikat kode
        if (items.some(i => i.uniqueCode === autoCode)) {
            autoCode = generateUniqueCode();
            document.getElementById('autoCode').value = autoCode;
        }
        const newItem = await addNewItem(name, autoCode, initialStock);
        if (newItem) {
            items.push(newItem);
            saveToLocal();
            renderAllTables();
            document.getElementById('itemName').value = '';
            document.getElementById('initialStock').value = '1';
            document.getElementById('autoCode').value = generateUniqueCode();
        }
    });
    
    // local storage
    function saveToLocal() {
        localStorage.setItem('barcodeStockItemsBottom', JSON.stringify(items.map(i => ({
            id: i.id, name: i.name, uniqueCode: i.uniqueCode, totalMasuk: i.totalMasuk, totalKeluar: i.totalKeluar, qrDataURL: i.qrDataURL, rawValue: i.rawValue
        }))));
    }
    
    function loadFromLocal() {
        const stored = localStorage.getItem('barcodeStockItemsBottom');
        if (stored && stored.length > 2) {
            items = JSON.parse(stored);
            renderAllTables();
        } else {
            initDemoData();
        }
    }
    
    async function initDemoData() {
        const sampleCode = generateUniqueCode();
        const demo = await addNewItem("Speaker JBL", sampleCode, 5);
        if (demo) items.push(demo);
        const sampleCode2 = generateUniqueCode();
        const demo2 = await addNewItem("Mouse Wireless", sampleCode2, 3);
        if (demo2) items.push(demo2);
        const sampleCode3 = generateUniqueCode();
        const demo3 = await addNewItem("Keyboard Mekanik", sampleCode3, 2);
        if (demo3) items.push(demo3);
        saveToLocal();
        renderAllTables();
    }
    
    // Scanner kamera (otomatis keluar)
    let scanner = null;
    const startScan = document.getElementById('startScanBtn');
    const stopScan = document.getElementById('stopScanBtn');
    const readerDiv = document.getElementById('reader');
    
    startScan.addEventListener('click', async () => {
        if (scanner) await stopScanHandler();
        readerDiv.innerHTML = "";
        scanner = new Html5Qrcode("reader");
        try {
            await scanner.start(
                { facingMode: "environment" }, 
                { fps: 10, qrbox: { width: 280, height: 280 } }, 
                (decoded) => {
                    processScanForOutgoing(decoded);
                }, 
                (err) => { console.log("scan error", err); }
            );
            document.getElementById('scanMessage').innerHTML = "📷 Kamera aktif, arahkan ke barcode QR...";
        } catch(e) { 
            document.getElementById('scanMessage').innerHTML = "❌ Gagal akses kamera: "+e;
        }
    });
    
    async function stopScanHandler() {
        if (scanner && scanner.isScanning) { 
            await scanner.stop(); 
            scanner.clear(); 
            scanner = null; 
        }
        readerDiv.innerHTML = "";
        document.getElementById('scanMessage').innerHTML = "⏹ Scanner dihentikan.";
    }
    
    stopScan.addEventListener('click', stopScanHandler);
    
    // refresh kode unik pada load
    function refreshAutoCode() {
        document.getElementById('autoCode').value = generateUniqueCode();
    }
    
    window.addEventListener('DOMContentLoaded', () => {
        refreshAutoCode();
        loadFromLocal();
    });
</script>
</body>
</html>
