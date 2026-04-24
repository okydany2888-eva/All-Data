<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Sistem Barcode Otomatis | Barang Masuk & Keluar</title>
    <!-- Library JsBarcode untuk barcode linear (opsional pelengkap) -->
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
    <!-- Library QRCode.js untuk barcode persegi (QR Code) hitam putih -->
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <!-- SheetJS (XLSX) untuk ekspor ke Excel -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <!-- HTML5 QR Code scanner untuk demo scan kamera -->
    <script src="https://unpkg.com/html5-qrcode/minified/html5-qrcode.min.js"></script>
    <style>
        * {
            box-sizing: border-box;
            font-family: 'Segoe UI', 'Inter', system-ui, -apple-system, sans-serif;
        }
        body {
            background: #f1f5f9;
            margin: 0;
            padding: 20px 16px;
        }
        .container {
            max-width: 1400px;
            margin: 0 auto;
        }
        /* Kartu */
        .card {
            background: white;
            border-radius: 32px;
            padding: 24px;
            margin-bottom: 28px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.05);
            transition: all 0.2s;
            border: 1px solid #e9eef3;
        }
        .card-title {
            font-size: 1.5rem;
            font-weight: 700;
            margin-bottom: 1.2rem;
            display: flex;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
            justify-content: space-between;
            border-left: 5px solid #2563eb;
            padding-left: 18px;
        }
        .badge-info {
            background: #eef2ff;
            padding: 5px 15px;
            border-radius: 40px;
            font-size: 0.75rem;
            font-weight: 500;
        }
        /* Form Grid */
        .form-grid {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            margin-bottom: 20px;
            align-items: flex-end;
        }
        .input-field {
            flex: 1;
            min-width: 180px;
        }
        .input-field label {
            display: block;
            font-weight: 600;
            font-size: 0.7rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            color: #475569;
            margin-bottom: 6px;
        }
        .input-field input, .input-field select {
            width: 100%;
            padding: 12px 16px;
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
            padding: 10px 26px;
            border-radius: 40px;
            font-weight: 600;
            cursor: pointer;
            transition: 0.15s;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            font-size: 0.85rem;
        }
        .btn-primary { background: #2563eb; }
        .btn-primary:hover { background: #1d4ed8; transform: scale(0.97);}
        .btn-success { background: #10b981; }
        .btn-success:hover { background: #059669; }
        .btn-secondary { background: #475569; }
        .btn-danger { background: #ef4444; }
        .btn-sm {
            padding: 6px 14px;
            font-size: 0.7rem;
            border-radius: 30px;
        }
        /* Tabel */
        .table-wrapper {
            overflow-x: auto;
            border-radius: 24px;
            border: 1px solid #edf2f7;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.85rem;
        }
        th, td {
            padding: 14px 12px;
            text-align: left;
            border-bottom: 1px solid #eef2ff;
            vertical-align: middle;
        }
        th {
            background: #f8fafc;
            font-weight: 700;
            color: #0f172a;
        }
        .barcode-img {
            width: 64px;
            height: 64px;
            background: white;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
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
        /* Print styling */
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
                border-radius: 16px;
            }
            .no-print { display: none; }
        }
        .scanner-section {
            background: #0f172a;
            border-radius: 28px;
            padding: 20px;
            color: white;
        }
        .flex-between {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 12px;
        }
        footer {
            text-align: center;
            margin-top: 30px;
            font-size: 0.7rem;
            color: #64748b;
        }
        .unique-id-hint {
            background: #f1f5f9;
            border-radius: 28px;
            padding: 10px 16px;
            font-size: 0.75rem;
            color: #334155;
            margin-top: 12px;
        }
    </style>
</head>
<body>
<div class="container">
    <!-- HEADER -->
    <div class="card" style="background: linear-gradient(115deg, #0f172a 0%, #1e293b 100%); color:white; border:none;">
        <div class="flex-between">
            <div>
                <h1 style="margin:0; display: flex; align-items: center; gap: 10px;">🔲 Barcode Otomatis <span style="font-size:0.8rem; background:#ffffff30; padding:4px 12px; border-radius: 60px;">Tanpa Warna / Hitam Putih</span></h1>
                <p style="margin:6px 0 0; opacity:0.85;">Kode unik dibuat otomatis • Scan dengan kamera HP • Cetak & tempel barang</p>
            </div>
            <div class="badge-info" style="background:#ffffff20;">📱 Support QR & Kamera</div>
        </div>
    </div>

    <!-- FORM TAMBAH BARANG (KODE UNIK OTOMATIS) -->
    <div class="card">
        <div class="card-title">
            <span>➕ Input Barang & Generate Barcode Persegi</span>
            <span class="badge-info">🔑 Kode Unik Otomatis</span>
        </div>
        <div class="form-grid">
            <div class="input-field">
                <label>📦 Nama Barang</label>
                <input type="text" id="itemName" placeholder="Contoh: Headset Wireless, Mouse, Laptop" value="Meja Kantor">
            </div>
            <div class="input-field">
                <label>🆔 Kode Unik (Otomatis)</label>
                <input type="text" id="autoUniqueCode" readonly style="background:#f3f4f6; font-family:monospace;" value="">
                <div style="font-size:10px; color:#6c757d; margin-top:4px;">✨ Digenerate otomatis, tidak perlu diisi manual</div>
            </div>
            <div class="input-field">
                <label>📌 Tipe Transaksi</label>
                <select id="transType">
                    <option value="masuk">🟢 BARANG MASUK</option>
                    <option value="keluar">🔴 BARANG KELUAR</option>
                </select>
            </div>
            <div class="input-field">
                <button class="btn btn-primary" id="addBarcodeBtn">✨ Generate & Simpan Barcode</button>
            </div>
        </div>
        <div class="unique-id-hint">
            💡 <strong>Sistem otomatis:</strong> Setiap klik "Generate" akan membuat kode unik baru (format: INV/YYYYMMDD/XXXXX). 
            Barcode persegi (QR Code) hitam putih berisi: [TIPE]|KODE_UNIK|NAMA_BARANG. Bisa discan kamera HP.
        </div>
    </div>

    <!-- TABEL DAFTAR BARCODE -->
    <div class="card">
        <div class="flex-between">
            <div class="card-title" style="margin-bottom:0;">📋 Semua Barcode Tersimpan</div>
            <div style="display: flex; gap: 12px;">
                <button class="btn btn-success" id="printAllBtn">🖨️ Cetak Semua Barcode</button>
                <button class="btn btn-secondary" id="exportExcelBtn">📎 Export ke Excel (XLSX)</button>
            </div>
        </div>
        <div class="table-wrapper">
            <table id="barcodeTable">
                <thead>
                    <tr><th>No</th><th>Nama Barang</th><th>Kode Unik</th><th>Tipe</th><th>Barcode (QR Persegi)</th><th>Text Barcode</th><th>Aksi</th></tr>
                </thead>
                <tbody id="tableBody">
                    <tr><td colspan="7" style="text-align:center;">Belum ada data, silahkan tambah barcode</td></tr>
                </tbody>
            </table>
        </div>
        <div style="margin-top: 16px; font-size:12px; color:#475569;">✅ Klik gambar barcode untuk pratinjau besar | Barcode hitam putih (tanpa warna tambahan) tetap mudah dipindai.</div>
    </div>

    <!-- AREA SCANNER KAMERA -->
    <div class="scanner-section">
        <div class="flex-between">
            <div><strong>📷 Scan Barcode (QR) dengan Kamera HP Android / iOS</strong></div>
            <div>
                <button id="startScanBtn" class="btn btn-primary btn-sm">▶ Mulai Scan</button>
                <button id="stopScanBtn" class="btn btn-danger btn-sm">⏹ Stop</button>
            </div>
        </div>
        <div id="reader" style="width:100%; max-width:500px; margin:16px auto;"></div>
        <div id="scanResult" style="background:#0f172a; border-radius: 24px; padding: 14px; margin-top: 12px; font-size:0.85rem; border:1px solid #334155;">
            🔍 Hasil Scan: <span id="scannedText" style="font-family:monospace; word-break:break-all;">—</span>
        </div>
    </div>
    <footer>© Barcode QR Hitam Putih • Kode unik otomatis • Cetak stiker tempel pada barang fisik</footer>
</div>

<!-- Area print tersembunyi -->
<div class="print-area" id="printArea"></div>

<script>
    // ======================== DATA & OTOMATIS KODE UNIK ========================
    let barcodeList = [];  // { id, name, uniqueCode, type, qrDataURL, rawValue }

    // Fungsi generate kode unik otomatis (format: INV/YYMMDD/XXXXX)
    function generateUniqueCode() {
        const now = new Date();
        const year = now.getFullYear().toString().slice(-2);
        const month = (now.getMonth() + 1).toString().padStart(2, '0');
        const day = now.getDate().toString().padStart(2, '0');
        const datePart = `${year}${month}${day}`;
        const randomPart = Math.floor(Math.random() * 100000).toString().padStart(5, '0');
        return `INV/${datePart}/${randomPart}`;
    }

    // Update field kode unik otomatis setiap kali form dimuat atau halaman direfresh
    function refreshAutoUniqueCode() {
        const autoCodeInput = document.getElementById('autoUniqueCode');
        autoCodeInput.value = generateUniqueCode();
    }

    // Fungsi generate QR Code persegi (HITAM PUTIH - tanpa warna tambahan pada kode)
    function generateQRCodeSquare(rawText, size = 180) {
        return new Promise((resolve) => {
            const div = document.createElement('div');
            div.style.width = `${size}px`;
            div.style.height = `${size}px`;
            div.style.position = 'relative';
            div.style.background = 'white';
            // Generate QR code menggunakan library QRCode.js (standar hitam putih)
            const qr = new QRCode(div, {
                text: rawText,
                width: size,
                height: size,
                colorDark: "#000000",
                colorLight: "#ffffff",
                correctLevel: QRCode.CorrectLevel.M
            });
            setTimeout(() => {
                let canvas = div.querySelector('canvas');
                if (!canvas) {
                    const fallbackCanvas = document.createElement('canvas');
                    const ctx = fallbackCanvas.getContext('2d');
                    fallbackCanvas.width = size;
                    fallbackCanvas.height = size;
                    ctx.fillStyle = 'white';
                    ctx.fillRect(0,0,size,size);
                    ctx.fillStyle = 'black';
                    ctx.font = "12px monospace";
                    ctx.fillText("QR Error", size/3, size/2);
                    canvas = fallbackCanvas;
                }
                // Tidak menambahkan border warna, murni hitam putih (kode barcode original)
                // Hanya kembalikan dataURL dari canvas QR asli (tambah sedikit padding putih jika perlu, biarkan natural)
                const finalDataURL = canvas.toDataURL('image/png');
                resolve(finalDataURL);
            }, 80);
        });
    }

    // Membuat object barcode baru dengan kode unik otomatis (setiap klik)
    async function createBarcodeObjectFromForm() {
        const name = document.getElementById('itemName').value.trim();
        let uniqueCode = document.getElementById('autoUniqueCode').value.trim();
        if (!name) {
            alert("Nama barang wajib diisi!");
            return null;
        }
        // Jika kode unik kosong atau sesuatu, generate ulang
        if (!uniqueCode) {
            uniqueCode = generateUniqueCode();
            document.getElementById('autoUniqueCode').value = uniqueCode;
        }
        const type = document.getElementById('transType').value; // "masuk" atau "keluar"
        // Format barcode: [IN/OUT]|KODE_UNIK|NAMA_BARANG
        const prefix = type === 'masuk' ? 'IN' : 'OUT';
        const rawValue = `${prefix}|${uniqueCode}|${name}`;
        // QR hitam putih
        const qrDataURL = await generateQRCodeSquare(rawValue, 170);
        return {
            id: Date.now() + Math.random() * 10000,
            name: name,
            uniqueCode: uniqueCode,
            type: type,
            qrDataURL: qrDataURL,
            rawValue: rawValue
        };
    }

    // Render tabel dari barcodeList
    async function renderTable() {
        const tbody = document.getElementById('tableBody');
        if (!barcodeList.length) {
            tbody.innerHTML = '<tr><td colspan="7" style="text-align:center;">📭 Kosong, klik "Generate & Simpan Barcode" untuk menambah</td></tr>';
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
            // Kode Unik
            row.insertCell(2).innerHTML = `<code style="background:#f1f5f9; padding:4px 10px; border-radius:20px; font-size:0.75rem;">${item.uniqueCode}</code>`;
            // Tipe dengan badge
            const typeCell = row.insertCell(3);
            typeCell.innerHTML = item.type === 'masuk' ? '<span style="background:#dcfce7; color:#15803d; padding:4px 12px; border-radius:40px;">🟢 MASUK</span>' : '<span style="background:#fee2e2; color:#b91c1c; padding:4px 12px; border-radius:40px;">🔴 KELUAR</span>';
            // Gambar barcode (QR hitam putih tanpa warna tambahan)
            const imgCell = row.insertCell(4);
            const img = document.createElement('img');
            img.src = item.qrDataURL;
            img.style.width = '68px';
            img.style.height = '68px';
            img.style.borderRadius = '12px';
            img.style.border = '1px solid #ddd';
            img.style.backgroundColor = 'white';
            img.style.cursor = 'pointer';
            img.title = 'Klik untuk melihat besar (Hitam Putih)';
            img.onclick = () => {
                const w = window.open();
                w.document.write(`<html><head><title>Barcode - ${item.name}</title></head><body style="display:flex; justify-content:center; align-items:center; min-height:100vh;"><div><img src="${item.qrDataURL}" style="width:300px;height:auto;border:1px solid #ccc; padding:10px; background:white;"><p style="font-family:monospace">${item.rawValue}</p></div></body></html>`);
                w.document.close();
            };
            imgCell.appendChild(img);
            // text barcode
            row.insertCell(5).innerHTML = `<span style="font-family:monospace; font-size:10px; word-break:break-all;">${item.rawValue.substring(0, 48)}${item.rawValue.length>48 ? '...' : ''}</span>`;
            // Aksi hapus & cetak satuan
            const actionCell = row.insertCell(6);
            actionCell.className = 'action-btns';
            const printBtn = document.createElement('button');
            printBtn.innerText = '🖨️ Cetak';
            printBtn.className = 'btn-sm btn-secondary';
            printBtn.onclick = () => printSingleBarcode(item);
            const delBtn = document.createElement('button');
            delBtn.innerText = '🗑️ Hapus';
            delBtn.className = 'btn-sm btn-danger';
            delBtn.onclick = () => {
                if(confirm(`Hapus barcode "${item.name}"?`)) {
                    barcodeList = barcodeList.filter(b => b.id !== item.id);
                    renderTable();
                    saveToLocal();
                }
            };
            actionCell.appendChild(printBtn);
            actionCell.appendChild(delBtn);
        }
    }

    // Simpan ke localStorage
    function saveToLocal() {
        localStorage.setItem('barcodeHitamPutihList', JSON.stringify(barcodeList.map(item => ({
            id: item.id, name: item.name, uniqueCode: item.uniqueCode, type: item.type, qrDataURL: item.qrDataURL, rawValue: item.rawValue
        }))));
    }

    function loadFromLocal() {
        const stored = localStorage.getItem('barcodeHitamPutihList');
        if (stored && stored.length > 2) {
            barcodeList = JSON.parse(stored);
            renderTable();
        } else {
            // tambahkan data contoh agar tidak kosong (dengan kode unik otomatis)
            addExampleData();
        }
    }

    async function addExampleData() {
        const sample1 = {
            id: Date.now()+1,
            name: "Meja Kantor Ergonomis",
            uniqueCode: generateUniqueCode(),
            type: "masuk",
            qrDataURL: "",
            rawValue: ""
        };
        const sample2 = {
            id: Date.now()+2,
            name: "Kursi Gaming",
            uniqueCode: generateUniqueCode(),
            type: "keluar",
            qrDataURL: "",
            rawValue: ""
        };
        sample1.rawValue = `IN|${sample1.uniqueCode}|${sample1.name}`;
        sample2.rawValue = `OUT|${sample2.uniqueCode}|${sample2.name}`;
        sample1.qrDataURL = await generateQRCodeSquare(sample1.rawValue);
        sample2.qrDataURL = await generateQRCodeSquare(sample2.rawValue);
        barcodeList.push(sample1, sample2);
        renderTable();
        saveToLocal();
    }

    // Cetak satu barcode
    function printSingleBarcode(item) {
        const printWindow = window.open('', '_blank');
        printWindow.document.write(`
            <html><head><title>Cetak Barcode ${item.name}</title>
            <style>
                body { font-family: 'Segoe UI', sans-serif; display: flex; justify-content: center; align-items: center; height: 100vh; background:white; margin:0; }
                .barcode-card { text-align: center; border: 1px solid #ccc; border-radius: 24px; padding: 30px 20px; width: 320px; background: white; }
                img { width: 200px; height: 200px; margin: 15px auto; display: block; }
                .code { font-family: monospace; font-size: 12px; background:#f3f4f6; padding: 6px; border-radius: 12px; word-break: break-all; }
                .type-badge { display: inline-block; margin-top: 8px; padding: 4px 14px; border-radius: 60px; background: ${item.type === 'masuk' ? '#dcfce7' : '#fee2e2'}; }
            </style>
            </head><body>
            <div class="barcode-card">
                <h3>${item.name}</h3>
                <p><strong>Kode Unik:</strong> ${item.uniqueCode}</p>
                <p class="type-badge">${item.type === 'masuk' ? '🟢 BARANG MASUK' : '🔴 BARANG KELUAR'}</p>
                <img src="${item.qrDataURL}" alt="QR Barcode" />
                <div class="code">${item.rawValue}</div>
                <small style="display:block; margin-top:14px;">Scan dengan kamera HP untuk verifikasi</small>
            </div>
            <script>window.onload = () => { window.print(); setTimeout(()=>window.close(), 600); }<\/script>
            </body></html>
        `);
        printWindow.document.close();
    }

    // Cetak semua barcode (print area)
    document.getElementById('printAllBtn').addEventListener('click', () => {
        if(barcodeList.length === 0) {
            alert("Tidak ada barcode untuk dicetak.");
            return;
        }
        const printArea = document.getElementById('printArea');
        let html = `<div style="text-align:center;"><h2>Laporan Barcode Inventaris</h2><p>Dibuat: ${new Date().toLocaleString()}</p></div><div class="print-barcode-grid">`;
        barcodeList.forEach(item => {
            html += `
                <div class="print-card" style="border:1px solid #aaa; border-radius: 16px; padding: 12px; text-align:center;">
                    <strong>${item.name}</strong><br>
                    <small>${item.uniqueCode}</small><br>
                    <span style="background:${item.type === 'masuk' ? '#dcfce7' : '#fee2e2'}; padding:2px 8px; border-radius: 20px; font-size:0.7rem;">${item.type === 'masuk' ? 'MASUK' : 'KELUAR'}</span><br>
                    <img src="${item.qrDataURL}" width="130" height="130" style="margin:8px auto; display:block; background:white;" /><br>
                    <span style="font-size:9px; word-break:break-all;">${item.rawValue}</span>
                </div>
            `;
        });
        html += `</div><div style="margin-top:30px; text-align:center; font-size:10px;">Gunting dan tempel pada barang fisik</div>`;
        printArea.innerHTML = html;
        window.print();
    });

    // Export ke Excel
    document.getElementById('exportExcelBtn').addEventListener('click', () => {
        const wsData = [["No", "Nama Barang", "Kode Unik (Otomatis)", "Tipe Transaksi", "Isi Barcode (Raw Value)", "QR Code (DataURL)"]];
        barcodeList.forEach((item, idx) => {
            wsData.push([
                idx+1,
                item.name,
                item.uniqueCode,
                item.type === 'masuk' ? "Masuk" : "Keluar",
                item.rawValue,
                item.qrDataURL
            ]);
        });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Barcode_List");
        XLSX.writeFile(wb, `barcode_otomatis_${new Date().toISOString().slice(0,19)}.xlsx`);
    });

    // Event Tambah Barcode (dengan kode unik otomatis baru setiap klik)
    document.getElementById('addBarcodeBtn').addEventListener('click', async () => {
        // Refresh kode unik terbaru sebelum buat
        refreshAutoUniqueCode();
        const newItem = await createBarcodeObjectFromForm();
        if (newItem) {
            barcodeList.push(newItem);
            renderTable();
            saveToLocal();
            // reset nama barang opsional, tapi biarkan agar bisa lanjut nambah cepat, tapi kosongkan kode unik akan di refresh lagi
            document.getElementById('itemName').value = "";
            refreshAutoUniqueCode(); // generate lagi untuk entri berikutnya
            // optional fokus ke nama
            document.getElementById('itemName').focus();
        }
    });

    // ---------- SCANNER KAMERA (HTML5 QR) ----------
    let html5QrCode = null;
    const startScanBtn = document.getElementById('startScanBtn');
    const stopScanBtn = document.getElementById('stopScanBtn');
    const readerDiv = document.getElementById('reader');
    const scannedTextSpan = document.getElementById('scannedText');

    async function startScanner() {
        if (html5QrCode) {
            await stopScanner();
        }
        readerDiv.innerHTML = "";
        html5QrCode = new Html5Qrcode("reader");
        try {
            await html5QrCode.start(
                { facingMode: "environment" },
                { fps: 10, qrbox: { width: 280, height: 280 }, aspectRatio: 1.0 },
                (decodedText) => {
                    scannedTextSpan.innerHTML = decodedText;
                    // tambahkan info singkat
                    if(decodedText.includes("|")) {
                        scannedTextSpan.style.background = "#1e293b";
                    }
                },
                (err) => { /* ignore scan errors */ }
            );
        } catch(e) {
            scannedTextSpan.innerHTML = "❌ Gagal akses kamera: " + e;
        }
    }

    async function stopScanner() {
        if (html5QrCode && html5QrCode.isScanning) {
            await html5QrCode.stop();
            html5QrCode.clear();
            html5QrCode = null;
        }
        readerDiv.innerHTML = "";
    }

    startScanBtn.addEventListener('click', startScanner);
    stopScanBtn.addEventListener('click', stopScanner);

    // Inisialisasi awal
    window.addEventListener('DOMContentLoaded', () => {
        refreshAutoUniqueCode();
        loadFromLocal();
    });
</script>
</body>
</html>
