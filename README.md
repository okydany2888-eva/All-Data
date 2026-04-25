<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Barcode Inventory | Rekap Stok + Tanggal Keluar</title>
    <!-- QR Code Library -->
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <!-- SheetJS -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <!-- html2canvas & jsPDF -->
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
            width: 55px;
            height: 55px;
            cursor: pointer;
            background: white;
            border-radius: 12px;
            border: 1px solid #cbd5e1;
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
    </style>
</head>
<body>
<div class="container">
    <div class="card" style="background: linear-gradient(135deg, #1e293b, #0f172a); color:white;">
        <div class="flex-between">
            <div><h1>📦 Barcode Pro</h1><p style="font-size: 0.7rem;">Rekap Stok + Tanggal Keluar</p></div>
            <div class="badge" style="background:#ffffff30;">📅 Setiap keluar tercatat tanggal</div>
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
        <div class="url-preview">💡 Barcode berisi URL untuk keluar otomatis. Scan dengan HP → buka halaman → stok berkurang & tercatat tanggal.</div>
    </div>

    <!-- Daftar Barang & Barcode -->
    <div class="card">
        <div class="flex-between"><h3>📋 Barang & Barcode</h3><button class="btn-success btn-sm" id="printAllBarcodeBtn" style="width: auto;">🖨️ Cetak</button></div>
        <div class="table-wrapper"><table id="barangTable"><thead><tr><th>No</th><th>Nama</th><th>Stok (PCS)</th><th>QR Barcode</th><th>Download</th></tr></thead><tbody id="barangBody"></tbody></table></div>
    </div>

    <!-- TAB Transaksi (dengan tanggal keluar detail) -->
    <div class="card">
        <div class="tab-buttons"><div class="tab-btn active" data-tab="masuk">📥 Riwayat Masuk</div><div class="tab-btn" data-tab="keluar">📤 Riwayat Keluar (dengan Tanggal)</div></div>
        <div id="masukTab" class="transaction-table"><table style="width:100%"><thead><tr><th>Tgl Masuk</th><th>Barang</th><th>Jml (PCS)</th><th>Unit</th></tr></thead><tbody id="masukTableBody"></tbody>｜DSML｜</div>
        <div id="keluarTab" style="display:none;" class="transaction-table"><table style="width:100%"><thead><tr><th>Tgl Keluar</th><th>Barang</th><th>Jml (PCS)</th><th>Sumber</th><th>Keterangan</th></tr></thead><tbody id="keluarTableBody"></tbody></table></div>
    </div>

    <!-- REKAP STOK + TANGGAL KELUAR (menampilkan daftar keluar per barang) -->
    <div class="card rekapan-card">
        <div class="flex-between"><h3>📊 REKAP STOK & DAFTAR TANGGAL KELUAR</h3><button class="btn-secondary btn-sm" id="exportRekapExcel">📎 Export Excel</button></div>
        <div class="table-wrapper">
            <table id="rekapTable">
                <thead>
                    <tr><th>Nama Barang</th><th>Total Masuk</th><th>Total Keluar</th><th>Stok Akhir</th><th>Tanggal Keluar (Terakhir / Riwayat)</th></tr>
                </thead>
                <tbody id="rekapBody"></tbody>
            </table>
        </div>
        <div class="info-footer" style="margin-top:8px;">📅 Kolom Tanggal Keluar menampilkan semua tanggal transaksi keluar (urutan terbaru).</div>
    </div>
    <div class="info-footer">✨ Setiap scan barcode (URL) akan otomatis mencatat tanggal keluar hari ini dan mengurangi stok.</div>
</div>
<div id="printArea" style="display: none;"></div>

<script>
    // ==================== DATA MODEL ====================
    let items = [];
    let transactions = [];
    const unitToPcs = { 'pcs': 1, 'box': 1000, 'display': 1000, 'dus': 500, 'karton': 2000 };
    const BASE_URL = window.location.origin + window.location.pathname;

    // Generate URL untuk keluar
    function generateOutgoingURL(itemId, itemName) {
        const url = new URL(BASE_URL);
        url.searchParams.set('action', 'out');
        url.searchParams.set('id', itemId);
        url.searchParams.set('name', encodeURIComponent(itemName));
        return url.toString();
    }

    // Generate QR optimal
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

    // Create item baru
    async function createItem(name) {
        const itemId = Date.now() + Math.random();
        const url = generateOutgoingURL(itemId, name);
        const qr = await generateQRCodeWithURL(url, 160);
        return {
            id: itemId,
            name: name,
            uniqueCode: `INV_${itemId}`,
            qrDataURL: qr,
            outgoingURL: url
        };
    }

    // Hitung stok
    function getTotalMasuk(itemId) { return transactions.filter(t => t.itemId === itemId && t.type === 'masuk').reduce((s, t) => s + t.quantityPcs, 0); }
    function getTotalKeluar(itemId) { return transactions.filter(t => t.itemId === itemId && t.type === 'keluar').reduce((s, t) => s + t.quantityPcs, 0); }
    function getStok(itemId) { return getTotalMasuk(itemId) - getTotalKeluar(itemId); }
    
    // Ambil semua tanggal keluar untuk suatu barang (urut terbaru)
    function getOutgoingDates(itemId) {
        const keluarTrans = transactions.filter(t => t.itemId === itemId && t.type === 'keluar');
        return keluarTrans.sort((a,b) => b.date.localeCompare(a.date)).map(t => t.date);
    }

    // Tambah transaksi masuk
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

    // Proses keluar (panggil dari URL / manual)
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
            note: `Keluar via ${source}`,
            source: source
        });
        saveToLocal();
        renderAll();
        showToast(`✅ KELUAR: ${item.name} -1 PCS | Sisa: ${getStok(item.id)} PCS | Tgl: ${today}`);
        return true;
    }

    // Handle URL parameter (dari scan)
    function handleIncomingURL() {
        const urlParams = new URLSearchParams(window.location.search);
        const action = urlParams.get('action');
        const itemId = urlParams.get('id');
        if (action === 'out' && itemId) {
            const newUrl = window.location.pathname;
            window.history.replaceState({}, document.title, newUrl);
            processOutgoing(itemId, 'scan_url');
        }
    }

    // Toast notifikasi
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

    // ==================== RENDER UI ====================
    function renderAll() {
        renderBarangTable();
        renderRekapDenganTanggalKeluar();
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
            img.title = `Scan untuk keluar otomatis\n${item.outgoingURL}`;
            img.onclick = () => {
                const win = window.open();
                win.document.write(`<html><body style="display:flex;justify-content:center;align-items:center;min-height:100vh;"><div><img src="${item.qrDataURL}" style="width:250px;"><p>${item.outgoingURL}</p></div></body></html>`);
                win.document.close();
            };
            imgCell.appendChild(img);
            const downCell = row.insertCell(4);
            const div = document.createElement('div'); div.className = 'download-btns';
            const png = document.createElement('button'); png.innerText = 'PNG'; png.className = 'btn-micro btn-secondary'; 
            png.onclick = () => downloadImg(item.qrDataURL, item.name, 'png');
            const pdf = document.createElement('button'); pdf.innerText = 'PDF'; pdf.className = 'btn-micro btn-secondary'; 
            pdf.onclick = () => downloadPDF(item.qrDataURL, item.name, item.outgoingURL);
            div.appendChild(png); div.appendChild(pdf);
            downCell.appendChild(div);
        });
    }

    // REKAP DENGAN TANGGAL KELUAR (menampilkan daftar tanggal)
    function renderRekapDenganTanggalKeluar() {
        const tbody = document.getElementById('rekapBody');
        if (!items.length) { tbody.innerHTML = '<tr><td colspan="5">Kosong</td></tr>'; return; }
        tbody.innerHTML = '';
        items.forEach(item => {
            const masuk = getTotalMasuk(item.id);
            const keluar = getTotalKeluar(item.id);
            const stok = masuk - keluar;
            const dates = getOutgoingDates(item.id);
            let tanggalKeluarStr = dates.length ? dates.join(', ') : 'Belum ada keluar';
            if (dates.length > 3) tanggalKeluarStr = dates.slice(0,3).join(', ') + ` (+${dates.length-3} lainnya)`;
            const row = tbody.insertRow();
            row.insertCell(0).innerText = item.name;
            row.insertCell(1).innerHTML = `<span class="stok-masuk">${masuk.toLocaleString()}</span>`;
            row.insertCell(2).innerHTML = `<span class="stok-keluar">${keluar.toLocaleString()}</span>`;
            row.insertCell(3).innerHTML = stok >= 0 ? `<b style="color:#059669;">${stok.toLocaleString()}</b>` : `<b style="color:#dc2626;">${stok.toLocaleString()}</b>`;
            row.insertCell(4).innerHTML = `<span style="font-size:0.65rem;">📅 ${tanggalKeluarStr}</span>`;
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
                row.insertCell(3).innerText = tr.source === 'scan_url' ? 'Scan URL' : 'Manual';
                row.insertCell(4).innerText = tr.note || '-';
            }
        });
    }

    function updateDropdown() {
        const select = document.getElementById('itemNameSelect');
        const old = select.value;
        select.innerHTML = '<option value="">-- Pilih barang --</option>';
        items.forEach(i => { 
            const opt = document.createElement('option'); 
            opt.value = i.name; 
            opt.textContent = `${i.name} (${getStok(i.id)} PCS)`; 
            select.appendChild(opt); 
        });
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
    
    async function downloadPDF(dataURL, name, url) {
        const { jsPDF } = window.jspdf;
        const img = new Image(); img.src = dataURL;
        await new Promise(r => { img.onload = r; });
        const pdf = new jsPDF({ unit: 'mm', format: 'a4', orientation: 'portrait' });
        const w = 70, h = (img.height * w) / img.width;
        pdf.setFontSize(14);
        pdf.text(`Barcode: ${name}`, 105, 20, { align: 'center' });
        pdf.addImage(img, 'PNG', (210-w)/2, 30, w, h);
        pdf.setFontSize(8);
        pdf.text(url, 105, 30+h+8, { align: 'center', maxWidth: 180 });
        pdf.save(`${name}_barcode.pdf`);
    }

    // Cetak semua barcode
    document.getElementById('printAllBarcodeBtn').addEventListener('click', () => {
        if (!items.length) { alert("Tidak ada barcode"); return; }
        const printDiv = document.getElementById('printArea');
        let html = `<div style="text-align:center;"><h2>Daftar Barcode</h2><p>${new Date().toLocaleString()}</p></div><div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:12px;">`;
        items.forEach(item => {
            html += `<div style="border:1px solid #aaa;border-radius:16px;padding:12px;text-align:center;">
                        <strong>${item.name}</strong><br>
                        <img src="${item.qrDataURL}" width="120"><br>
                        <span style="font-size:8px;word-break:break-all;">${item.outgoingURL}</span>
                        <div>Stok: ${getStok(item.id)} PCS</div>
                    </div>`;
        });
        html += `</div>`;
        printDiv.innerHTML = html;
        window.print();
    });

    // Export Excel (rekap + tanggal keluar)
    document.getElementById('exportRekapExcel').addEventListener('click', () => {
        const wsData = [["Nama Barang", "Total Masuk (PCS)", "Total Keluar (PCS)", "Stok Akhir (PCS)", "Riwayat Tanggal Keluar (Terbaru)"]];
        items.forEach(item => {
            const dates = getOutgoingDates(item.id);
            const tanggalStr = dates.length ? dates.join(", ") : "Tidak ada";
            wsData.push([item.name, getTotalMasuk(item.id), getTotalKeluar(item.id), getStok(item.id), tanggalStr]);
        });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(wb, ws, "Rekap_Stok_Tanggal");
        XLSX.writeFile(wb, `rekap_stok_tanggal_${new Date().toISOString().slice(0,10)}.xlsx`);
    });

    // Tombol tambah stok
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
        localStorage.setItem('urlBarcodeWithDate', JSON.stringify(items.map(i => ({ id: i.id, name: i.name, uniqueCode: i.uniqueCode, qrDataURL: i.qrDataURL, outgoingURL: i.outgoingURL }))));
        localStorage.setItem('urlBarcodeTransWithDate', JSON.stringify(transactions));
    }
    
    async function loadFromLocal() {
        const storedItems = localStorage.getItem('urlBarcodeWithDate');
        const storedTrans = localStorage.getItem('urlBarcodeTransWithDate');
        if (storedItems) items = JSON.parse(storedItems);
        if (storedTrans) transactions = JSON.parse(storedTrans);
        if (items.length === 0) {
            const demo1 = await createItem("Speaker JBL");
            const demo2 = await createItem("Mouse Logitech");
            items = [demo1, demo2];
            transactions = [
                { id: Date.now()+1, itemId: demo1.id, type: 'masuk', date: '2025-03-10', quantityPcs: 5000, unitRaw: '5 Box', note: 'Awal' },
                { id: Date.now()+2, itemId: demo2.id, type: 'masuk', date: '2025-03-11', quantityPcs: 3000, unitRaw: '3 Box', note: 'Awal' },
                { id: Date.now()+3, itemId: demo1.id, type: 'keluar', date: '2025-03-15', quantityPcs: 1, unitRaw: '1 PCS', note: 'Contoh keluar', source: 'demo' },
                { id: Date.now()+4, itemId: demo1.id, type: 'keluar', date: '2025-03-16', quantityPcs: 1, unitRaw: '1 PCS', note: 'Contoh keluar lagi', source: 'demo' }
            ];
            saveToLocal();
        }
        renderAll();
    }

    document.getElementById('masukDate').value = new Date().toISOString().slice(0,10);
    document.getElementById('unitSelect').addEventListener('change', updateConversion);
    document.getElementById('unitQty').addEventListener('input', updateConversion);
    updateConversion();
    
    loadFromLocal().then(() => {
        handleIncomingURL();
    });
</script>
</body>
</html>
