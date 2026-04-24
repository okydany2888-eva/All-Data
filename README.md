<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Sistem Barcode Persegi | Barang Masuk & Keluar + Cetak & Excel</title>
    <!-- Library JsBarcode untuk generate barcode linear (opsional untuk pelengkap) -->
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
    <!-- Library QRCode.js untuk membuat barcode persegi (QR Code) -->
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <!-- SheetJS (XLSX) untuk ekspor ke Excel -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <!-- HTML5 QR Code scanner untuk demo scan -->
    <script src="https://unpkg.com/html5-qrcode/minified/html5-qrcode.min.js"></script>
    <style>
        * {
            box-sizing: border-box;
            font-family: 'Segoe UI', 'Poppins', system-ui, sans-serif;
        }
        body {
            background: #eef2f7;
            margin: 0;
            padding: 24px 16px;
        }
        .container {
            max-width: 1400px;
            margin: 0 auto;
        }
        /* Kartu */
        .card {
            background: white;
            border-radius: 32px;
            padding: 20px 24px;
            margin-bottom: 28px;
            box-shadow: 0 12px 28px rgba(0,0,0,0.08);
            transition: all 0.2s;
        }
        .card-title {
            font-size: 1.5rem;
            font-weight: 700;
            margin-bottom: 1rem;
            display: flex;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
            justify-content: space-between;
            border-left: 5px solid #3b82f6;
            padding-left: 18px;
        }
        .badge-type {
            font-size: 0.75rem;
            background: #f1f5f9;
            padding: 4px 14px;
            border-radius: 30px;
        }
        /* Form input */
        .form-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 18px;
            margin-bottom: 24px;
            align-items: flex-end;
        }
        .input-field {
            flex: 1;
            min-width: 160px;
        }
        .input-field label {
            display: block;
            font-weight: 600;
            font-size: 0.7rem;
            text-transform: uppercase;
            color: #475569;
            margin-bottom: 6px;
        }
        .input-field input, .input-field select {
            width: 100%;
            padding: 12px 14px;
            border-radius: 28px;
            border: 1.5px solid #e2e8f0;
            background: white;
            font-size: 0.9rem;
            outline: none;
            transition: 0.2s;
        }
        .input-field input:focus, .input-field select:focus {
            border-color: #3b82f6;
            box-shadow: 0 0 0 3px rgba(59,130,246,0.2);
        }
        .btn {
            background: #1e293b;
            color: white;
            border: none;
            padding: 10px 24px;
            border-radius: 40px;
            font-weight: 600;
            cursor: pointer;
            transition: 0.15s;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }
        .btn-primary {
            background: #2563eb;
        }
        .btn-primary:hover { background: #1d4ed8; transform: scale(0.97);}
        .btn-success { background: #10b981; }
        .btn-success:hover { background: #059669; }
        .btn-warning { background: #eab308; color: #1e293b; }
        .btn-danger { background: #ef4444; }
        .btn-secondary { background: #475569; }
        
        /* Tabel daftar barcode */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 24px;
            border: 1px solid #eef2ff;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.85rem;
        }
        th, td {
            padding: 14px 10px;
            text-align: left;
            border-bottom: 1px solid #e2e8f0;
            vertical-align: middle;
        }
        th {
            background: #f8fafc;
            font-weight: 700;
            color: #1e293b;
        }
        .barcode-preview-img {
            width: 64px;
            height: 64px;
            background: #f9f9ff;
            border-radius: 14px;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
        }
        .qr-preview {
            width: 60px;
            height: 60px;
        }
        .action-btns {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
        }
        .btn-sm {
            padding: 5px 12px;
            font-size: 0.7rem;
            border-radius: 30px;
        }
        /* print area khusus cetak barcode */
        .print-area {
            display: none;
        }
        @media print {
            body * {
                visibility: hidden;
            }
            .print-area, .print-area * {
                visibility: visible;
            }
            .print-area {
                display: block;
                position: absolute;
                top: 0;
                left: 0;
                width: 100%;
                padding: 20px;
                background: white;
            }
            .print-barcode-grid {
                display: grid;
                grid-template-columns: repeat(4, 1fr);
                gap: 20px;
            }
            .print-card {
                border: 1px solid #ccc;
                padding: 12px;
                text-align: center;
                page-break-inside: avoid;
            }
            .no-print {
                display: none;
            }
        }
        .qr-container {
            display: inline-block;
            background: white;
            padding: 4px;
            border-radius: 12px;
        }
        .flex-between {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 12px;
        }
        .scanner-preview {
            margin-top: 18px;
            background: #0f172a;
            border-radius: 28px;
            padding: 16px;
            color: white;
        }
        footer {
            text-align: center;
            margin-top: 30px;
            color: #64748b;
            font-size: 0.7rem;
        }
    </style>
</head>
<body>
<div class="container">
    <!-- HEADER -->
    <div class="card" style="background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); color:white;">
        <div class="flex-between">
            <div>
                <h1 style="margin:0">📦 Barcode Persegi (QR Code)</h1>
                <p style="margin:6px 0 0; opacity:0.8;">Barang Masuk (Hijau) / Keluar (Merah) | Cetak & Ekspor Excel</p>
            </div>
            <div class="badge-type" style="background:#ffffff20;">✔️ Scan Kamera HP</div>
        </div>
    </div>

    <!-- FORM INPUT BARANG + BARCODE PERSEGI -->
    <div class="card">
        <div class="card-title">
            <span>➕ Tambah Item & Barcode Persegi</span>
            <span style="font-size:0.7rem;">*Warna border QR menyesuaikan tipe</span>
        </div>
        <div class="form-grid">
            <div class="input-field">
                <label>📛 Nama Barang</label>
                <input type="text" id="itemName" placeholder="Cth: Headset Bluetooth" value="Speaker JBL Go">
            </div>
            <div class="input-field">
                <label>🆔 Kode Unik / SKU</label>
                <input type="text" id="skuCode" placeholder="SKU-001" value="SKU-2025-01">
            </div>
            <div class="input-field">
                <label>📌 Tipe Transaksi</label>
                <select id="transType">
                    <option value="masuk">🟢 BARANG MASUK (Hijau)</option>
                    <option value="keluar">🔴 BARANG KELUAR (Merah)</option>
                </select>
            </div>
            <div class="input-field">
                <button class="btn btn-primary" id="addBarcodeBtn">✨ Generate & Simpan</button>
            </div>
        </div>
        <div style="font-size: 12px; background:#f1f5f9; padding: 8px 16px; border-radius: 24px;">
            💡 Barcode persegi (QR Code) akan memuat: [TIPE]|SKU|Nama Barang. Warna border QR = Hijau (Masuk) / Merah (Keluar)
        </div>
    </div>

    <!-- TABEL DAFTAR BARCODE + GAMBAR & AKSI -->
    <div class="card">
        <div class="flex-between">
            <div class="card-title" style="margin-bottom:0;">📋 Daftar Barcode Tersimpan</div>
            <div style="display: flex; gap: 12px;">
                <button class="btn btn-success" id="printAllBtn">🖨️ Cetak Semua Barcode</button>
                <button class="btn btn-secondary" id="exportExcelBtn">📎 Export ke Excel (XLSX)</button>
            </div>
        </div>
        <div class="table-wrapper">
            <table id="barcodeTable">
                <thead>
                    <tr><th>No</th><th>Nama Barang</th><th>SKU / Kode</th><th>Tipe</th><th>Gambar Barcode (Persegi)</th><th>Value Barcode</th><th>Aksi</th></tr>
                </thead>
                <tbody id="tableBody">
                    <tr><td colspan="7" style="text-align:center;">Belum ada data, silahkan tambah barcode.</td></tr>
                </tbody>
            </table>
        </div>
        <div style="margin-top: 16px; font-size: 12px; color:#475569;">
            ✅ Klik gambar barcode untuk memperbesar pratinjau | Tiap barcode bisa dihapus / cetak satuan.
        </div>
    </div>

    <!-- Scanner kamera (demo scan barcode persegi) -->
    <div class="card scanner-preview" style="background: #1e293b;">
        <div class="flex-between">
            <div><strong>📷 Scan QR Code dengan Kamera HP (Android/iOS)</strong></div>
            <div><button id="startScanBtn" class="btn btn-primary btn-sm">▶ Mulai Scan</button>
            <button id="stopScanBtn" class="btn btn-danger btn-sm">⏹ Stop</button></div>
        </div>
        <div id="reader" style="width:100%; max-width:500px; margin:12px auto;"></div>
        <div id="scanResult" style="background:#0f172a; border-radius: 24px; padding: 12px; margin-top: 12px; font-size:0.8rem;">
            🔍 Hasil Scan: <span id="scannedText">—</span>
        </div>
    </div>
    <footer>© Barcode Persegi - Warna hijau/merah untuk identifikasi cepat. Klik cetak untuk print stiker tempel.</footer>
</div>

<!-- Area print tersembunyi untuk cetak massal -->
<div class="print-area" id="printArea"></div>

<script>
    // ======================= DATA STORAGE =======================
    let barcodeList = []; // menyimpan object: { id, name, sku, type, qrDataURL, rawValue }
    
    // Fungsi generate QR Code persegi (warna border disesuaikan: hijau/merah)
    // Karena library QRCode.js standar tidak support warna border langsung, kita buat canvas wrapper dan beri border berwarna.
    function generateSquareQRCodeWithColor(text, type, size = 180) {
        return new Promise((resolve) => {
            // Buat container sementara
            const div = document.createElement('div');
            div.style.width = `${size}px`;
            div.style.height = `${size}px`;
            div.style.position = 'relative';
            div.style.background = 'white';
            // generate QR menggunakan qrcodejs (hanya support element)
            const qr = new QRCode(div, {
                text: text,
                width: size,
                height: size,
                colorDark: "#000000",
                colorLight: "#ffffff",
                correctLevel: QRCode.CorrectLevel.M
            });
            setTimeout(() => {
                // Dapatkan canvas dari dalam div (library membuat canvas?)
                let canvas = div.querySelector('canvas');
                if (!canvas) {
                    // fallback: buat canvas sendiri
                    const tempCanvas = document.createElement('canvas');
                    const tempCtx = tempCanvas.getContext('2d');
                    tempCanvas.width = size;
                    tempCanvas.height = size;
                    tempCtx.fillStyle = 'white';
                    tempCtx.fillRect(0,0,size,size);
                    tempCtx.fillStyle = 'black';
                    tempCtx.font = "12px monospace";
                    tempCtx.fillText("QR Error", 20, size/2);
                    canvas = tempCanvas;
                }
                // Tambahkan border berwarna sesuai tipe (hijau/merah)
                const wrapperCanvas = document.createElement('canvas');
                const borderSize = 12;
                wrapperCanvas.width = size + borderSize*2;
                wrapperCanvas.height = size + borderSize*2;
                const ctx = wrapperCanvas.getContext('2d');
                // warna border
                const borderColor = (type === 'masuk') ? '#22c55e' : '#ef4444';
                ctx.fillStyle = borderColor;
                ctx.fillRect(0, 0, wrapperCanvas.width, wrapperCanvas.height);
                // gambar QR di tengah
                ctx.drawImage(canvas, borderSize, borderSize, size, size);
                // Tambahkan teks kecil di bawah border opsional
                ctx.fillStyle = '#1f2937';
                ctx.font = 'bold 12px "Segoe UI"';
                ctx.fillText(type === 'masuk' ? 'MASUK' : 'KELUAR', borderSize+5, wrapperCanvas.height-5);
                const finalDataURL = wrapperCanvas.toDataURL('image/png');
                resolve(finalDataURL);
            }, 50);
        });
    }
    
    // Render ulang tabel
    async function renderTable() {
        const tbody = document.getElementById('tableBody');
        if (!barcodeList.length) {
            tbody.innerHTML = '<tr><td colspan="7" style="text-align:center;">📭 Kosong, tambah barcode persegi terlebih dahulu.</td></tr>';
            return;
        }
        tbody.innerHTML = '';
        for (let i = 0; i < barcodeList.length; i++) {
            const item = barcodeList[i];
            const row = tbody.insertRow();
            row.setAttribute('data-id', item.id);
            // No
            row.insertCell(0).innerText = i+1;
            // Nama
            row.insertCell(1).innerText = item.name;
            // SKU
            row.insertCell(2).innerText = item.sku;
            // Tipe
            const typeCell = row.insertCell(3);
            typeCell.innerHTML = item.type === 'masuk' ? '<span style="background:#dcfce7; padding:4px 12px; border-radius:40px; color:#15803d;">🟢 MASUK</span>' : '<span style="background:#fee2e2; padding:4px 12px; border-radius:40px; color:#b91c1c;">🔴 KELUAR</span>';
            // Gambar barcode persegi
            const imgCell = row.insertCell(4);
            const imgPreview = document.createElement('img');
            imgPreview.src = item.qrDataURL;
            imgPreview.style.width = '68px';
            imgPreview.style.height = '68px';
            imgPreview.style.borderRadius = '12px';
            imgPreview.style.border = `3px solid ${item.type === 'masuk' ? '#22c55e' : '#ef4444'}`;
            imgPreview.style.cursor = 'pointer';
            imgPreview.title = 'Klik untuk pratinjau besar';
            imgPreview.onclick = () => {
                const w = window.open();
                w.document.write(`<img src="${item.qrDataURL}" style="width:300px;height:auto;display:block;margin:auto;"><p>${item.rawValue}</p>`);
            };
            imgCell.appendChild(imgPreview);
            // Value barcode
            row.insertCell(5).innerHTML = `<span style="font-family:monospace; font-size:11px;">${item.rawValue.substring(0, 40)}</span>`;
            // Aksi
            const actionCell = row.insertCell(6);
            actionCell.className = 'action-btns';
            const printOneBtn = document.createElement('button');
            printOneBtn.innerText = '🖨️ Cetak';
            printOneBtn.className = 'btn-sm btn-secondary';
            printOneBtn.onclick = () => printSingleBarcode(item);
            const delBtn = document.createElement('button');
            delBtn.innerText = '🗑️ Hapus';
            delBtn.className = 'btn-sm btn-danger';
            delBtn.onclick = () => {
                barcodeList = barcodeList.filter(b => b.id !== item.id);
                renderTable();
                saveToLocal();
            };
            actionCell.appendChild(printOneBtn);
            actionCell.appendChild(delBtn);
        }
    }
    
    // Simpan ke localStorage
    function saveToLocal() {
        localStorage.setItem('barcodePersegiList', JSON.stringify(barcodeList.map(item => ({
            id: item.id, name: item.name, sku: item.sku, type: item.type, qrDataURL: item.qrDataURL, rawValue: item.rawValue
        }))));
    }
    
    function loadFromLocal() {
        const data = localStorage.getItem('barcodePersegiList');
        if (data) {
            barcodeList = JSON.parse(data);
            renderTable();
        } else {
            // default contoh data
            addExampleData();
        }
    }
    
    async function addExampleData() {
        // contoh agar tidak kosong
        const sample1 = await createBarcodeObject("Laptop ASUS", "LAP-101", "masuk");
        const sample2 = await createBarcodeObject("Mouse Logitech", "MOU-202", "keluar");
        if(sample1) barcodeList.push(sample1);
        if(sample2) barcodeList.push(sample2);
        renderTable();
        saveToLocal();
    }
    
    async function createBarcodeObject(name, sku, type) {
        if (!name || !sku) return null;
        const rawValue = `${type === 'masuk' ? 'IN' : 'OUT'}|${sku}|${name}`;
        const qrDataURL = await generateSquareQRCodeWithColor(rawValue, type, 160);
        return {
            id: Date.now() + Math.random() * 10000,
            name: name,
            sku: sku,
            type: type,
            qrDataURL: qrDataURL,
            rawValue: rawValue
        };
    }
    
    // Event tambah barcode
    document.getElementById('addBarcodeBtn').addEventListener('click', async () => {
        const name = document.getElementById('itemName').value.trim();
        const sku = document.getElementById('skuCode').value.trim();
        const type = document.getElementById('transType').value;
        if (!name || !sku) {
            alert("Nama Barang dan SKU wajib diisi!");
            return;
        }
        const newBarcode = await createBarcodeObject(name, sku, type);
        if (newBarcode) {
            barcodeList.push(newBarcode);
            renderTable();
            saveToLocal();
            // reset sedikit
            document.getElementById('itemName').value = "";
            document.getElementById('skuCode').value = "";
            // Optional isi default
            document.getElementById('itemName').placeholder = "Item berhasil ditambah";
        }
    });
    
    // CETAK SATUAN
    function printSingleBarcode(item) {
        const printWindow = window.open('', '_blank');
        printWindow.document.write(`
            <html><head><title>Cetak Barcode ${item.name}</title>
            <style>
                body { font-family: sans-serif; display: flex; justify-content: center; align-items: center; height: 100vh; margin:0; background:white; }
                .card { border: 2px solid #ccc; padding: 20px; text-align: center; border-radius: 24px; width: 320px; }
                img { width: 220px; height: 220px; margin: 20px auto; }
                .info { margin-top: 12px; font-weight: bold; }
                .type-badge { color: ${item.type === 'masuk' ? '#15803d' : '#b91c1c'}; background: #f1f5f9; padding:4px; border-radius:20px; }
            </style>
            </head><body>
            <div class="card">
                <h3>${item.name}</h3>
                <p>SKU: ${item.sku}</p>
                <p class="type-badge">${item.type === 'masuk' ? '🟢 BARANG MASUK' : '🔴 BARANG KELUAR'}</p>
                <img src="${item.qrDataURL}" />
                <p style="font-size:12px;">${item.rawValue}</p>
                <small>Scan untuk verifikasi</small>
            </div>
            <script>window.onload = () => { window.print(); setTimeout(()=>window.close(),800); }<\/script>
            </body></html>
        `);
        printWindow.document.close();
    }
    
    // CETAK SEMUA BARCODE (print area)
    document.getElementById('printAllBtn').addEventListener('click', () => {
        const printArea = document.getElementById('printArea');
        if (barcodeList.length === 0) {
            alert("Tidak ada barcode untuk dicetak.");
            return;
        }
        let html = `<div style="text-align:center;"><h2>Daftar Barcode Persegi</h2><p>${new Date().toLocaleString()}</p></div><div class="print-barcode-grid">`;
        barcodeList.forEach(item => {
            html += `
                <div class="print-card" style="border:2px solid ${item.type === 'masuk' ? '#22c55e' : '#ef4444'}; border-radius: 20px; padding: 12px; text-align:center; page-break-inside:avoid;">
                    <strong>${item.name}</strong><br>
                    <small>${item.sku}</small><br>
                    <span style="background:${item.type === 'masuk' ? '#dcfce7' : '#fee2e2'}; padding:2px 8px; border-radius:20px;">${item.type === 'masuk' ? 'MASUK' : 'KELUAR'}</span><br>
                    <img src="${item.qrDataURL}" width="130" height="130" style="margin:8px auto; display:block;" /><br>
                    <span style="font-size:10px;">${item.rawValue}</span>
                </div>
            `;
        });
        html += `</div><div style="margin-top:30px; text-align:center;">Gunting dan tempel pada barang</div>`;
        printArea.innerHTML = html;
        window.print();
    });
    
    // Export ke Excel (SheetJS) - berisi nama, SKU, tipe, value barcode, dan gambar (dataURL sebagai teks / embed)
    document.getElementById('exportExcelBtn').addEventListener('click', () => {
        const wsData = [["No", "Nama Barang", "SKU", "Tipe Transaksi", "Text Barcode (Raw)", "URL Gambar QR DataURL"]];
        barcodeList.forEach((item, idx) => {
            wsData.push([
                idx+1, item.name, item.sku, item.type === 'masuk' ? "Masuk (Hijau)" : "Keluar (Merah)",
                item.rawValue, item.qrDataURL
            ]);
        });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Barcode_List");
        XLSX.writeFile(wb, `barcode_inventory_${new Date().toISOString().slice(0,19)}.xlsx`);
    });
    
    // ---------- SCANNER KAMERA (QR) ----------
    let html5QrCode = null;
    const startScan = document.getElementById('startScanBtn');
    const stopScan = document.getElementById('stopScanBtn');
    const readerDiv = document.getElementById('reader');
    const scannedTextSpan = document.getElementById('scannedText');
    
    async function initScanner() {
        if (html5QrCode) {
            await stopScannerInstance();
        }
        readerDiv.innerHTML = "";
        html5QrCode = new Html5Qrcode("reader");
        try {
            await html5QrCode.start(
                { facingMode: "environment" },
                { fps: 10, qrbox: { width: 280, height: 280 }, aspectRatio: 1 },
                (decodedText) => {
                    scannedTextSpan.innerText = decodedText;
                    // cek apakah sesuai format 
                    if(decodedText.includes("|")){
                        scannedTextSpan.style.background = "#10b98120";
                    } else {
                        scannedTextSpan.style.background = "#eab30820";
                    }
                },
                (err) => { console.log("scanning..."); }
            );
        } catch(e) {
            scannedTextSpan.innerText = "Gagal akses kamera: "+e;
        }
    }
    async function stopScannerInstance() {
        if (html5QrCode && html5QrCode.isScanning) {
            await html5QrCode.stop();
            html5QrCode.clear();
            html5QrCode = null;
        }
        readerDiv.innerHTML = "";
    }
    startScan.addEventListener('click', initScanner);
    stopScan.addEventListener('click', stopScannerInstance);
    
    // Load awal
    window.addEventListener('DOMContentLoaded', async () => {
        loadFromLocal();
        // tambahkan fallback demo jika kosong
        if(barcodeList.length === 0){
            await addExampleData();
        }
    });
    
    // tambahkan style grid print
    const style = document.createElement('style');
    style.textContent = `
        .print-barcode-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 20px; margin-top: 20px; }
        @media print { .print-barcode-grid { grid-template-columns: repeat(4,1fr); } .print-card { border: 1px solid #aaa; break-inside: avoid; } }
    `;
    document.head.appendChild(style);
</script>
</body>
</html>
