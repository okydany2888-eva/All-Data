<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Sistem Barcode - Barang Masuk & Keluar</title>
    <!-- Library JsBarcode untuk generate barcode otomatis -->
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
    <!-- HTML5 QR Code untuk scan barcode via kamera HP Android (opsional demo scanner) -->
    <script src="https://unpkg.com/html5-qrcode/minified/html5-qrcode.min.js"></script>
    <style>
        * {
            box-sizing: border-box;
            font-family: 'Segoe UI', Roboto, 'Helvetica Neue', sans-serif;
        }

        body {
            background: #f0f2f5;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        /* Container utama */
        .app-container {
            max-width: 750px;
            width: 100%;
            background: white;
            border-radius: 32px;
            box-shadow: 0 20px 35px -12px rgba(0,0,0,0.2);
            overflow: hidden;
            padding: 24px 20px 32px;
            transition: all 0.2s;
        }

        h1 {
            font-size: 1.8rem;
            margin-top: 0;
            margin-bottom: 0.25rem;
            color: #1e2a3e;
            display: flex;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
            justify-content: space-between;
        }

        h1 small {
            font-size: 0.8rem;
            font-weight: normal;
            background: #eef2ff;
            padding: 4px 12px;
            border-radius: 40px;
            color: #2563eb;
        }

        .sub {
            color: #4b5563;
            border-left: 4px solid #3b82f6;
            padding-left: 14px;
            margin: 8px 0 20px 0;
            font-weight: 500;
        }

        /* Panel form */
        .form-panel {
            background: #f9fafb;
            border-radius: 28px;
            padding: 20px;
            margin-bottom: 32px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.02);
            border: 1px solid #e5e7eb;
        }

        .form-row {
            display: flex;
            flex-wrap: wrap;
            gap: 16px;
            margin-bottom: 20px;
            align-items: flex-end;
        }

        .input-group {
            flex: 1;
            min-width: 160px;
        }

        .input-group label {
            display: block;
            font-weight: 600;
            font-size: 0.75rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            color: #6b7280;
            margin-bottom: 6px;
        }

        .input-group input, .input-group select {
            width: 100%;
            padding: 12px 14px;
            border-radius: 24px;
            border: 1.5px solid #e2e8f0;
            background: white;
            font-size: 0.95rem;
            transition: 0.2s;
            outline: none;
        }

        .input-group input:focus, .input-group select:focus {
            border-color: #3b82f6;
            box-shadow: 0 0 0 3px rgba(59,130,246,0.2);
        }

        .btn-generate {
            background: #1f2937;
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 40px;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
            font-size: 0.9rem;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }

        .btn-generate:hover {
            background: #0f172a;
            transform: scale(0.97);
        }

        /* Preview barcode dengan warna dinamis */
        .barcode-preview {
            text-align: center;
            background: #ffffff;
            padding: 24px 16px 20px;
            border-radius: 32px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.05);
            margin-bottom: 32px;
            transition: all 0.2s;
            border: 1px solid #f1f5f9;
        }

        .barcode-header {
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            flex-wrap: wrap;
            margin-bottom: 16px;
            padding: 0 12px;
        }

        .status-badge {
            font-size: 1rem;
            font-weight: bold;
            padding: 6px 18px;
            border-radius: 60px;
            letter-spacing: 0.5px;
        }

        .status-masuk {
            background: #dcfce7;
            color: #15803d;
            border-left: 5px solid #22c55e;
        }

        .status-keluar {
            background: #fee2e2;
            color: #b91c1c;
            border-left: 5px solid #ef4444;
        }

        .barcode-canvas {
            margin: 20px auto;
            display: flex;
            justify-content: center;
            background: white;
            padding: 20px;
            border-radius: 20px;
        }

        canvas {
            max-width: 100%;
            height: auto;
            background: white;
        }

        .barcode-value {
            font-family: monospace;
            font-size: 1.15rem;
            background: #f8fafc;
            display: inline-block;
            padding: 6px 16px;
            border-radius: 28px;
            margin-top: 15px;
            letter-spacing: 1px;
            word-break: break-all;
        }

        /* Scanner area */
        .scanner-section {
            background: #1e293b;
            border-radius: 32px;
            padding: 16px;
            margin-top: 20px;
            color: white;
        }

        .scanner-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            margin-bottom: 12px;
            padding: 0 8px;
        }

        .scanner-title {
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-scan {
            background: #3b82f6;
            border: none;
            padding: 8px 18px;
            border-radius: 40px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: 0.2s;
        }

        .btn-scan-stop {
            background: #ef4444;
        }

        .qr-reader {
            width: 100%;
            border-radius: 24px;
            overflow: hidden;
        }

        #reader {
            width: 100%;
            background: black;
            border-radius: 20px;
        }

        .scan-result {
            background: #0f172a;
            padding: 14px;
            border-radius: 24px;
            margin-top: 16px;
            font-size: 0.9rem;
            word-break: break-all;
        }

        .scan-log {
            font-size: 0.85rem;
            color: #cbd5e1;
            border-top: 1px solid #334155;
            margin-top: 12px;
            padding-top: 12px;
        }

        button {
            cursor: pointer;
        }

        @media (max-width: 550px) {
            .app-container {
                padding: 16px;
            }
            .form-row {
                flex-direction: column;
            }
            .btn-generate {
                width: 100%;
                justify-content: center;
            }
        }
        footer {
            text-align: center;
            font-size: 0.7rem;
            margin-top: 28px;
            color: #94a3b8;
        }
    </style>
</head>
<body>

<div class="app-container">
    <h1>
        📦 Barcode Inventory
        <small>Masuk █ Hijau | Keluar █ Merah</small>
    </h1>
    <div class="sub">✨ Generate barcode otomatis & scan via kamera HP Android</div>

    <!-- Form generate barcode otomatis -->
    <div class="form-panel">
        <div class="form-row">
            <div class="input-group">
                <label>📝 Nama / Kode Item</label>
                <input type="text" id="itemName" placeholder="Contoh: Laptop AX-101 / Sku-882" value="BRG-001">
            </div>
            <div class="input-group">
                <label>🎯 Tipe Transaksi</label>
                <select id="transactionType">
                    <option value="masuk">🟢 BARANG MASUK (Hijau)</option>
                    <option value="keluar">🔴 BARANG KELUAR (Merah)</option>
                </select>
            </div>
            <div class="input-group">
                <button class="btn-generate" id="generateBtn">⚡ Buat Barcode Otomatis</button>
            </div>
        </div>
        <div class="form-row">
            <div class="input-group" style="flex:2">
                <label>🔖 ID Unik / Serial (akan di-enkode)</label>
                <input type="text" id="customId" placeholder="Isi manual atau otomatis dibuat" value="INV-2410-01">
            </div>
        </div>
        <div style="font-size:12px; color:#475569; margin-top: 8px;">
            💡 Tips: Barcode akan dibuat berdasarkan ID unik + tipe transaksi. Klik tombol untuk generate ulang, warna menyesuaikan (hijau = masuk, merah = keluar).
        </div>
    </div>

    <!-- Preview Barcode Dinamis -->
    <div class="barcode-preview" id="barcodePreviewCard">
        <div class="barcode-header">
            <span class="status-badge" id="statusBadge">🟢 BARANG MASUK</span>
            <span style="font-size:0.7rem; background:#f1f5f9; padding:4px 12px; border-radius:20px;">📱 scan-friendly</span>
        </div>
        <div class="barcode-canvas">
            <canvas id="barcodeCanvas"></canvas>
        </div>
        <div class="barcode-value" id="barcodeTextDisplay">-</div>
        <div style="margin-top: 12px; font-size: 12px; color: #4b5563;">
            ⚡ Barcode otomatis ter-update setiap klik generate atau perubahan item
        </div>
    </div>

    <!-- Scanner Kamera HP (untuk demo scan barcode) -->
    <div class="scanner-section">
        <div class="scanner-header">
            <div class="scanner-title">
                📷 Kamera Scanner (Android/iOS)
                <span style="font-size: 0.7rem;">scan barcode yang dibuat</span>
            </div>
            <div>
                <button id="startScanBtn" class="btn-scan">▶ Mulai Scan</button>
                <button id="stopScanBtn" class="btn-scan btn-scan-stop" style="background:#475569; margin-left:6px;">⏹ Stop</button>
            </div>
        </div>
        <div id="reader" style="width:100%;"></div>
        <div class="scan-result" id="scanResultArea">
            🔍 Hasil scan akan muncul disini...<br>
            <span id="scannedCode">—</span>
            <div class="scan-log" id="scanLog">
                💡 Arahkan kamera ke barcode (Code128) yang ditampilkan di atas.
            </div>
        </div>
    </div>
    <footer>
        ⚙️ Barcode format CODE128 | Warna hijau untuk transaksi MASUK, merah untuk KELUAR | Otomatis generate berdasarkan ID + tipe
    </footer>
</div>

<script>
    // DOM elements
    const itemNameInput = document.getElementById('itemName');
    const transactionTypeSelect = document.getElementById('transactionType');
    const customIdInput = document.getElementById('customId');
    const generateBtn = document.getElementById('generateBtn');
    const barcodeCanvas = document.getElementById('barcodeCanvas');
    const statusBadge = document.getElementById('statusBadge');
    const barcodeTextDisplay = document.getElementById('barcodeTextDisplay');

    // Variabel untuk menyimpan data barcode terakhir
    let currentBarcodeRaw = "";
    let currentType = "masuk";   // 'masuk' atau 'keluar'

    // --- Helper: Generate barcode otomatis (warna dinamis) ---
    function generateBarcode() {
        // Ambil nilai ID (jika customId kosong, buat berdasarkan nama + timestamp sederhana)
        let baseId = customIdInput.value.trim();
        if (baseId === "") {
            // fallback: generate ID otomatis dari nama + timestamp
            const nameSlug = itemNameInput.value.trim() || "item";
            const timeStamp = Date.now().toString().slice(-6);
            baseId = `${nameSlug}-${timeStamp}`;
            customIdInput.value = baseId; // isi otomatis agar konsisten
        }

        const transactionType = transactionTypeSelect.value; // "masuk" atau "keluar"
        currentType = transactionType;

        // Membuat string barcode unik yang merepresentasikan: TIPE|ID|NAMA (agar scan bisa mengetahui info)
        // Namun kita ingin barcode tetap bisa discan dan memuat informasi transaksi + identifier.
        // Format: "IN|MASUK|IDxxx|ItemName" atau "OUT|KELUAR|IDxxx|ItemName"
        // Biar lebih rapi kita buat: [TIPE] - ID:xxxx - NAMA
        let prefix = (transactionType === "masuk") ? "IN" : "OUT";
        const itemName = itemNameInput.value.trim() || "NoName";
        // Bersihkan karakter khusus untuk barcode agar stabil (tidak terlalu panjang)
        const cleanId = baseId.replace(/[^a-zA-Z0-9\-_]/g, '');
        const cleanName = itemName.replace(/[^a-zA-Z0-9\s\-_]/g, '').substring(0, 20);
        // String untuk barcode: misal "IN|BRG-001|Laptop"
        const barcodeValue = `${prefix}|${cleanId}|${cleanName}`;
        currentBarcodeRaw = barcodeValue;

        // Update tampilan teks barcode (potong jika panjang)
        barcodeTextDisplay.innerText = barcodeValue.length > 40 ? barcodeValue.substring(0, 38) + "..." : barcodeValue;

        // Atur warna dan style badge sesuai tipe
        if (transactionType === "masuk") {
            statusBadge.innerHTML = "🟢 BARANG MASUK (Hijau)";
            statusBadge.className = "status-badge status-masuk";
            // generate barcode dengan warna HIJAU (garis: #2e7d32, teks hijau gelap)
            JsBarcode(barcodeCanvas, barcodeValue, {
                format: "CODE128",
                lineColor: "#2e7d32",       // hijau gelap untuk barang masuk
                background: "#ffffff",
                width: 2,
                height: 70,
                displayValue: true,
                fontSize: 14,
                textMargin: 4,
                textColor: "#166534"
            });
        } else {
            statusBadge.innerHTML = "🔴 BARANG KELUAR (Merah)";
            statusBadge.className = "status-badge status-keluar";
            // warna MERAH untuk barang keluar
            JsBarcode(barcodeCanvas, barcodeValue, {
                format: "CODE128",
                lineColor: "#b91c1c",       // merah terang/keluar
                background: "#ffffff",
                width: 2,
                height: 70,
                displayValue: true,
                fontSize: 14,
                textMargin: 4,
                textColor: "#991b1b"
            });
        }

        // Optional: tambahkan tooltip
        console.log(`Barcode generated : ${barcodeValue} (${transactionType})`);
    }

    // Fungsi untuk update otomatis jika ada perubahan input penting
    function autoRefreshBarcode() {
        generateBarcode();
    }

    // Event listener untuk tombol generate / perubahan field
    generateBtn.addEventListener('click', () => {
        // jika customId kosong, kita trigger isi sedikit lebih rapi
        if (customIdInput.value.trim() === "") {
            const timestamp = new Date().getTime().toString().slice(-8);
            customIdInput.value = `AUTO-${timestamp}`;
        }
        autoRefreshBarcode();
    });

    // Ketika select atau input berubah, refresh barcode secara realtime (user friendly)
    transactionTypeSelect.addEventListener('change', autoRefreshBarcode);
    itemNameInput.addEventListener('input', autoRefreshBarcode);
    customIdInput.addEventListener('input', autoRefreshBarcode);

    // Inisialisasi pertama kali halaman: buat barcode default
    // Set nilai awal agar lebih representatif
    window.addEventListener('DOMContentLoaded', () => {
        // default awal yang menarik
        customIdInput.value = "BRG-2410-001";
        itemNameInput.value = "Laptop Pro X";
        transactionTypeSelect.value = "masuk";
        generateBarcode();
    });

    // ---------- SCANNER KAMERA HP ANDROID ----------
    // Menggunakan library html5-qrcode untuk scan via kamera
    let html5QrCode = null;
    const startScanBtn = document.getElementById('startScanBtn');
    const stopScanBtn = document.getElementById('stopScanBtn');
    const readerDiv = document.getElementById('reader');
    const scannedCodeSpan = document.getElementById('scannedCode');
    const scanLogDiv = document.getElementById('scanLog');

    // Fungsi untuk memulai scanner
    function startScanner() {
        if (html5QrCode) {
            // jika sudah ada, stop dulu biar bersih
            stopScanner().then(() => {
                initNewScanner();
            });
        } else {
            initNewScanner();
        }
    }

    async function initNewScanner() {
        // Bersihkan area reader
        if (readerDiv) readerDiv.innerHTML = "";
        // Buat instance baru
        html5QrCode = new Html5Qrcode("reader");
        const config = {
            fps: 10,
            qrbox: { width: 280, height: 200 },
            aspectRatio: 1.0,
            showTorchButtonIfSupported: true,
        };
        // Mulai scan menggunakan kamera belakang (environment)
        try {
            await html5QrCode.start(
                { facingMode: "environment" },  // pilih kamera belakang untuk HP
                config,
                (decodedText, decodedResult) => {
                    // Success callback: ketika scan berhasil
                    scannedCodeSpan.innerText = decodedText;
                    // Tambahkan ke log dengan timestamp
                    const time = new Date().toLocaleTimeString();
                    const logMsg = `[${time}] ✅ Scan: ${decodedText}`;
                    // Tampilkan juga info detail parsing jika barcode sesuai format IN/OUT
                    let extraInfo = "";
                    if (decodedText.startsWith("IN|") || decodedText.startsWith("OUT|")) {
                        const parts = decodedText.split('|');
                        if (parts.length >= 2) {
                            const tipe = parts[0] === "IN" ? "🟢 BARANG MASUK" : "🔴 BARANG KELUAR";
                            extraInfo = ` → ${tipe} | ID: ${parts[1] || '?'}`;
                        }
                        scanLogDiv.innerHTML = `<span style="color:#a5f3c3;">✅ Barcode valid: ${decodedText}${extraInfo}</span><br>${scanLogDiv.innerHTML.substring(0,300)}`;
                    } else {
                        scanLogDiv.innerHTML = `<span style="color: #facc15;">📌 Terbaca: ${decodedText} (barcode custom)</span><br>${scanLogDiv.innerHTML.substring(0,300)}`;
                    }
                    // Tambahkan juga hasil utama
                    document.getElementById('scanResultArea').style.background = "#0f172a";
                    // Opsional: beri notifikasi getar (hanya jika secure context & vibrate tersedia)
                    if (navigator.vibrate) navigator.vibrate(200);
                },
                (errorMessage) => {
                    // error scanning biasanya frame kosong, abaikan untuk tidak membanjiri log
                    // bisa tampilkan jika ingin debug
                    // console.log(errorMessage);
                }
            ).catch(err => {
                scanLogDiv.innerHTML = `<span style="color:#f87171;">❌ Gagal akses kamera: ${err}</span><br>${scanLogDiv.innerHTML}`;
                console.error(err);
                html5QrCode = null;
            });
            scanLogDiv.innerHTML = `<span style="color:#86efac;">📷 Kamera aktif, arahkan ke barcode ...</span><br>${scanLogDiv.innerHTML}`;
            startScanBtn.innerText = "🔄 Scan Aktif";
            startScanBtn.style.background = "#10b981";
        } catch (err) {
            scanLogDiv.innerHTML = `<span style="color:#f87171;">⚠️ Izin kamera ditolak atau error: ${err}</span><br>${scanLogDiv.innerHTML}`;
            html5QrCode = null;
        }
    }

    async function stopScanner() {
        if (html5QrCode && html5QrCode.isScanning) {
            try {
                await html5QrCode.stop();
                html5QrCode.clear(); // hapus instance
            } catch (e) {
                console.warn("stop error", e);
            }
            html5QrCode = null;
            readerDiv.innerHTML = ""; // bersihkan view
            scanLogDiv.innerHTML = `<span style="color:#94a3b8;">⏹ Scanner dihentikan. Tekan Mulai Scan lagi.</span><br>${scanLogDiv.innerHTML.substring(0,200)}`;
            startScanBtn.innerText = "▶ Mulai Scan";
            startScanBtn.style.background = "#3b82f6";
        } else {
            if (html5QrCode) {
                // jika ada instance tapi tidak scanning? force clear
                try { await html5QrCode.clear(); } catch(e) {}
                html5QrCode = null;
                readerDiv.innerHTML = "";
            }
            startScanBtn.innerText = "▶ Mulai Scan";
            startScanBtn.style.background = "#3b82f6";
        }
    }

    startScanBtn.addEventListener('click', () => {
        // memulai scan
        if (html5QrCode && html5QrCode.isScanning) {
            scanLogDiv.innerHTML = `<span style="color:yellow;">⚠️ Scanner sudah berjalan. Stop dulu jika ingin ganti kamera.</span><br>${scanLogDiv.innerHTML}`;
            return;
        }
        startScanner();
    });

    stopScanBtn.addEventListener('click', () => {
        stopScanner();
    });

    // Saat halaman ditutup/unload bersihkan scanner
    window.addEventListener('beforeunload', () => {
        if (html5QrCode && html5QrCode.isScanning) {
            html5QrCode.stop().catch(e=>{});
        }
    });

    // Optional: Untuk demo, jika ingin langsung menscan barcode yang digenerate,
    // user cukup mengarahkan kamera ke barcode pada widget di atas. Kode sudah support.
    // Juga barcode yang dihasilkan berwarna hijau/merah tetap terbaca dengan baik oleh library.
    // Tambahan: tampilkan pesan bahwa barcode mudah dibaca hp android.
    // add small notice interaction
    const styleNotif = document.createElement('style');
    styleNotif.textContent = `#reader video { border-radius: 20px; object-fit: cover; }`;
    document.head.appendChild(styleNotif);
</script>
</body>
</html>
