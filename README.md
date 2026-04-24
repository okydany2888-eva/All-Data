<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Barcode Inventory | Satuan Unit + Dropdown Barang</title>
    <!-- Library QR Code -->
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <!-- SheetJS -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <!-- html2canvas & jspdf -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <!-- Scanner -->
    <script src="https://unpkg.com/html5-qrcode/minified/html5-qrcode.min.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { font-family: system-ui, 'Segoe UI', sans-serif; background: #f1f5f9; padding: 16px; }
        .container { max-width: 1400px; margin: 0 auto; }
        .card { background: white; border-radius: 28px; padding: 20px; margin-bottom: 24px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); border: 1px solid #e2e8f0; }
        .flex-between { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 12px; }
        h2, h3 { font-size: clamp(1.1rem, 5vw, 1.5rem); }
        .badge { background: #eef2ff; padding: 5px 14px; border-radius: 40px; font-size: 0.75rem; }
        .form-grid { display: flex; flex-wrap: wrap; gap: 16px; margin-bottom: 16px; align-items: flex-end; }
        .input-group { flex: 1; min-width: 140px; }
        .input-group label { font-size: 0.7rem; font-weight: 600; text-transform: uppercase; color: #475569; display: block; margin-bottom: 4px; }
        .input-group input, .input-group select { width: 100%; padding: 12px 14px; border-radius: 60px; border: 1.5px solid #e2e8f0; font-size: 0.9rem; }
        .btn { padding: 10px 18px; border-radius: 60px; border: none; font-weight: 600; cursor: pointer; display: inline-flex; align-items: center; gap: 8px; font-size: 0.8rem; }
        .btn-primary { background: #2563eb; color: white; }
        .btn-success { background: #059669; color: white; }
        .btn-secondary { background: #475569; color: white; }
        .btn-sm { padding: 6px 12px; font-size: 0.7rem; }
        table { width: 100%; border-collapse: collapse; font-size: 0.8rem; }
        th, td { padding: 12px 8px; text-align: left; border-bottom: 1px solid #e2e8f0; }
        th { background: #f8fafc; }
        .stok-masuk { color: #059669; font-weight: bold; }
        .stok-keluar { color: #dc2626; }
        .barcode-img { width: 60px; height: 60px; cursor: pointer; border-radius: 12px; border: 1px solid #e2e8f0; background: white; }
        .scanner-area { background: #0f172a; border-radius: 28px; padding: 20px; color: white; }
        .scan-result { background: #1e293b; border-radius: 20px; padding: 12px; margin-top: 12px; font-family: monospace; }
        @media (max-width: 640px) { .btn { width: 100%; justify-content: center; } .flex-between { flex-direction: column; align-items: stretch; } }
        .rekapan-card { background: linear-gradient(135deg, #fef9e3, #fef3c7); margin-top: 24px; }
        .info-footer { text-align: center; font-size: 12px; color: #64748b; margin-top: 20px; }
        .highlight { background: #fef9c3; transition: 0.3s; }
        .inline-group { display: flex; gap: 8px; align-items: center; }
        .modal-add { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); justify-content: center; align-items: center; z-index: 1000; }
        .modal-content { background: white; border-radius: 32px; padding: 24px; width: 90%; max-width: 400px; }
        .modal-content input, .modal-content select { width: 100%; padding: 10px; margin: 8px 0; border-radius: 40px; border: 1px solid #ccc; }
        .modal-buttons { display: flex; gap: 12px; margin-top: 16px; }
    </style>
</head>
<body>
<div class="container">
    <!-- Header -->
    <div class="card" style="background: linear-gradient(135deg, #1e293b, #0f172a); color:white;">
        <div class="flex-between">
            <div><h1>📦 Barcode Inventory Pro</h1><p>✨ Satuan/Unit + Konversi (Box/Doz/Pcs) • Dropdown Barang</p></div>
            <div class="badge" style="background:#ffffff30;">🔍 Scan = keluar otomatis</div>
        </div>
    </div>

    <!-- Form Tambah Stok Masuk dengan Dropdown + Satuan -->
    <div class="card">
        <h3>Tambah Stok Masuk</h3>
        <div class="form-grid">
            <div class="input-group" style="flex:2">
                <label>Nama Barang</label>
                <div class="inline-group">
                    <select id="itemSelect" style="flex:3"></select>
                    <button type="button" class="btn btn-secondary btn-sm" id="openModalBtn" style="white-space:nowrap;">Baru</button>
                </div>
            </div>
            <div class="input-group">
                <label>Satuan/Unit</label>
                <select id="unitSelect">
                    <option value="pcs">PCS</option>
                    <option value="box">BOX (1 Box = 1000 Pcs)</option>
                    <option value="display">DISPLAY (1 Display = 500 Pcs)</option>
                    <option value="pack">PACK (1 Pack = 250 Pcs)</option>
                </select>
                <div style="font-size:10px; color:#6c757d; margin-top:4px;" id="unitHint">1 Box = 1000 Pcs</div>
            </div>
            <div class="input-group">
                <label>Jumlah (dalam satuan)</label>
                <input type="number" id="qtyUnit" value="1" min="1" step="1">
            </div>
            <div class="input-group">
                <label>≈ Total PCS</label>
                <input type="text" id="totalPcsPreview" readonly style="background:#f3f4f6;">
            </div>
            <div class="input-group">
                <button class="btn btn-primary" id="addStockBtn" style="margin-top:22px;">📦 Tambah Stok Masuk</button>
            </div>
        </div>
        <div class="badge" style="background:#e6f0ff;">
            🔑 <strong>Konversi:</strong> 1 Box = 1000 Pcs | 1 Display = 500 Pcs | 1 Pack = 250 Pcs | Stok akhir dihitung dalam PCS.
        </div>
    </div>

    <!-- Tabel Barang + Barcode (Tanpa kolom aksi) -->
    <div class="card">
        <div class="flex-between">
            <h3>📋 Daftar Barang & Barcode Tetap</h3>
            <button class="btn btn-success btn-sm" id="printAllBarcodeBtn">🖨️ Cetak Semua Barcode</button>
        </div>
        <div style="overflow-x: auto;">
            <table id="barangTable">
                <thead><tr><th>No</th><th>Nama Barang</th><th>Kode Unik</th><th>Total Masuk (PCS)</th><th>Total Keluar (PCS)</th><th>Stok Akhir (PCS)</th><th>Barcode QR</th><th>Download</th></tr></thead>
                <tbody id="barangBody"></tbody>
            </table>
        </div>
    </div>

    <!-- Scanner Kamera -->
    <div class="scanner-area">
        <div class="flex-between">
            <div><strong>📷 Scan Barcode QR (Kamera HP)</strong><br><span>Scan format OUT → kurangi stok 1 PCS</span></div>
            <div><button id="startScanBtn" class="btn btn-primary btn-sm">▶ Mulai Scan</button><button id="stopScanBtn" class="btn btn-danger btn-sm" style="background:#dc2626;">⏹ Stop</button></div>
        </div>
        <div id="reader" style="width:100%; max-width:500px; margin:16px auto;"></div>
        <div class="scan-result">🔍 Hasil Scan: <span id="scanMessage">—</span></div>
    </div>

    <!-- Rekap Stok -->
    <div class="card rekapan-card">
        <div class="flex-between"><h3>📊 REKAP AKHIR STOK (PCS)</h3><button class="btn btn-secondary btn-sm" id="exportRekapExcel">📎 Export Excel</button></div>
        <div style="overflow-x: auto;"><table id="rekapTable"><thead><tr><th>Nama Barang</th><th>Total Masuk (PCS)</th><th>Total Keluar (PCS)</th><th>Stok Akhir (PCS)</th></tr></thead><tbody id="rekapBody"></tbody></table></div>
    </div>
    <div class="info-footer">💡 Setiap nama barang memiliki 1 kode barcode tetap. Scan barcode OUT akan mengurangi stok 1 PCS.</div>
</div>

<!-- Modal Tambah Barang Baru -->
<div id="modalAddItem" class="modal-add">
    <div class="modal-content">
        <h3>Tambah Barang Baru</h3>
        <label>Nama Barang</label>
        <input type="text" id="newItemName" placeholder="Contoh: Speaker JBL">
        <div style="margin-top:16px;" class="modal-buttons">
            <button class="btn btn-primary" id="confirmNewItemBtn">Simpan</button>
            <button class="btn btn-secondary" id="closeModalBtn">Batal</button>
        </div>
    </div>
</div>

<div id="printArea" style="display:none;"></div>

<script>
    // Data items: { id, name, uniqueCode, totalMasukPcs, totalKeluarPcs, qrDataURL, rawValueOUT }
    let items = [];
    
    // Konversi satuan ke PCS
    function convertToPcs(unit, qty) {
        const conversion = { pcs: 1, box: 1000, display: 500, pack: 250 };
        return qty * (conversion[unit] || 1);
    }
    
    function getUnitHint(unit) {
        if(unit === 'box') return '1 Box = 1000 Pcs';
        if(unit === 'display') return '1 Display = 500 Pcs';
        if(unit === 'pack') return '1 Pack = 250 Pcs';
        return '1 Pcs = 1 Pcs';
    }
    
    // Update preview total PCS
    function updatePcsPreview() {
        const unit = document.getElementById('unitSelect').value;
        const qty = parseFloat(document.getElementById('qtyUnit').value) || 0;
        const totalPcs = convertToPcs(unit, qty);
        document.getElementById('totalPcsPreview').value = totalPcs + ' PCS';
        document.getElementById('unitHint').innerText = getUnitHint(unit);
    }
    document.getElementById('unitSelect').addEventListener('change', updatePcsPreview);
    document.getElementById('qtyUnit').addEventListener('input', updatePcsPreview);
    
    // Generate kode unik tetap per barang
    function generateUniqueCode(name) {
        const base = name.toLowerCase().replace(/[^a-z0-9]/g, '').substring(0,10);
        const timestamp = Date.now().toString().slice(-6);
        return `INV/${base}/${timestamp}`;
    }
    
    // Generate QR Code (hitam putih)
    function generateQRCode(text, size=160) {
        return new Promise((resolve) => {
            const div = document.createElement('div');
            div.style.width = `${size}px`;
            div.style.height = `${size}px`;
            new QRCode(div, { text, width: size, height: size, colorDark: "#000000", colorLight: "#ffffff", correctLevel: QRCode.CorrectLevel.M });
            setTimeout(() => {
                let canvas = div.querySelector('canvas');
                if (!canvas) { const fake = document.createElement('canvas'); fake.width=size; fake.height=size; const ctx=fake.getContext('2d'); ctx.fillStyle='white'; ctx.fillRect(0,0,size,size); ctx.fillStyle='black'; ctx.fillText("QR",size/2-10,size/2); canvas=fake; }
                resolve(canvas.toDataURL('image/png'));
            }, 80);
        });
    }
    
    // Membuat item baru
    async function createNewItem(name) {
        const uniqueCode = generateUniqueCode(name);
        const rawValueOUT = `OUT|${uniqueCode}|${name}`;
        const qrDataURL = await generateQRCode(rawValueOUT);
        return { id: Date.now()+Math.random(), name, uniqueCode, totalMasukPcs: 0, totalKeluarPcs: 0, qrDataURL, rawValueOUT };
    }
    
    // Tambah stok masuk (otomatis jika barang belum ada, buat baru)
    async function addStockIn(itemName, unit, qtyUnit) {
        if (!itemName) return false;
        const pcsAdded = convertToPcs(unit, qtyUnit);
        if (pcsAdded <= 0) return false;
        
        let existing = items.find(i => i.name.toLowerCase() === itemName.toLowerCase());
        if (existing) {
            existing.totalMasukPcs += pcsAdded;
            saveToLocal();
            renderAllTables();
            highlightRow(existing.id);
            document.getElementById('scanMessage').innerHTML = `✅ Stok ${existing.name} +${pcsAdded} PCS (${qtyUnit} ${unit}) | Total Masuk: ${existing.totalMasukPcs} PCS`;
            return true;
        } else {
            const newItem = await createNewItem(itemName);
            newItem.totalMasukPcs = pcsAdded;
            items.push(newItem);
            saveToLocal();
            renderAllTables();
            document.getElementById('scanMessage').innerHTML = `🆕 Barang baru "${itemName}" dibuat. +${pcsAdded} PCS (${qtyUnit} ${unit})`;
            updateDropdown();
            return true;
        }
    }
    
    // Proses scan keluar (kurangi 1 PCS setiap scan)
    function processScanOutgoing(scannedText) {
        if (!scannedText || !scannedText.includes('|')) { document.getElementById('scanMessage').innerHTML = `❌ Format tidak valid`; return false; }
        const parts = scannedText.split('|');
        if (parts.length < 3) { document.getElementById('scanMessage').innerHTML = `❌ Format harus TIPE|KODE|NAMA`; return false; }
        const tipe = parts[0], scannedCode = parts[1];
        const item = items.find(i => i.uniqueCode === scannedCode);
        if (!item) { document.getElementById('scanMessage').innerHTML = `❌ Barang dengan kode ${scannedCode} tidak ditemukan`; return false; }
        if (tipe === 'OUT') {
            if (item.totalMasukPcs - item.totalKeluarPcs <= 0) { document.getElementById('scanMessage').innerHTML = `⚠️ Stok ${item.name} habis!`; return false; }
            item.totalKeluarPcs += 1;
            saveToLocal();
            renderAllTables();
            highlightRow(item.id);
            document.getElementById('scanMessage').innerHTML = `✅ BARANG KELUAR: ${item.name} (-1 PCS) | Sisa: ${item.totalMasukPcs - item.totalKeluarPcs} PCS`;
            return true;
        } else if (tipe === 'IN') { document.getElementById('scanMessage').innerHTML = `ℹ️ Barcode IN: ${item.name} (tidak mengurangi stok)`; return false; }
        else { document.getElementById('scanMessage').innerHTML = `❌ Tipe tidak dikenal`; return false; }
    }
    
    function highlightRow(itemId) {
        const rows = document.querySelectorAll('#barangBody tr');
        for(let row of rows) if(row.getAttribute('data-id') == itemId) { row.classList.add('highlight'); setTimeout(()=>row.classList.remove('highlight'),800); break; }
    }
    
    // Render Tabel Barang + Rekap
    function renderBarangTable() {
        const tbody = document.getElementById('barangBody');
        if(!items.length) { tbody.innerHTML='<tr><td colspan="8" style="text-align:center;">Belum ada data</td></tr>'; return; }
        tbody.innerHTML = '';
        items.forEach((item, idx) => {
            const stokAkhir = item.totalMasukPcs - item.totalKeluarPcs;
            const row = tbody.insertRow();
            row.setAttribute('data-id', item.id);
            row.insertCell(0).innerText = idx+1;
            row.insertCell(1).innerText = item.name;
            row.insertCell(2).innerHTML = `<code style="background:#f1f5f9; padding:4px 8px; border-radius:12px;">${item.uniqueCode}</code>`;
            row.insertCell(3).innerHTML = `<span class="stok-masuk">${item.totalMasukPcs.toLocaleString()} PCS</span>`;
            row.insertCell(4).innerHTML = `<span class="stok-keluar">${item.totalKeluarPcs.toLocaleString()} PCS</span>`;
            row.insertCell(5).innerHTML = `<b>${stokAkhir.toLocaleString()} PCS</b>`;
            const imgCell = row.insertCell(6);
            const img = document.createElement('img'); img.src = item.qrDataURL; img.className = 'barcode-img'; img.title='Preview QR';
            img.onclick = () => { const w=window.open(); w.document.write(`<html><body style="display:flex;justify-content:center;align-items:center;min-height:100vh;"><div style="background:white;padding:24px;border-radius:32px;text-align:center;"><img src="${item.qrDataURL}" width="280"><p>${item.rawValueOUT}</p></div></body></html>`); w.document.close(); };
            imgCell.appendChild(img);
            const downloadCell = row.insertCell(7);
            const btnGroup = document.createElement('div'); btnGroup.style.display='flex'; btnGroup.style.gap='6px'; btnGroup.style.flexWrap='wrap';
            const btnPng = document.createElement('button'); btnPng.innerText='PNG'; btnPng.className='btn-sm btn-secondary';
            btnPng.onclick = () => downloadBarcodeAsImage(item.qrDataURL, item.name, 'png');
            const btnJpg = document.createElement('button'); btnJpg.innerText='JPG'; btnJpg.className='btn-sm btn-secondary';
            btnJpg.onclick = () => downloadBarcodeAsImage(item.qrDataURL, item.name, 'jpg');
            const btnPdf = document.createElement('button'); btnPdf.innerText='PDF'; btnPdf.className='btn-sm btn-secondary';
            btnPdf.onclick = () => downloadBarcodeAsPDF(item.qrDataURL, item.name, item.uniqueCode);
            btnGroup.appendChild(btnPng); btnGroup.appendChild(btnJpg); btnGroup.appendChild(btnPdf);
            downloadCell.appendChild(btnGroup);
        });
    }
    
    function renderRekap() {
        const tbody = document.getElementById('rekapBody');
        if(!items.length) { tbody.innerHTML='<tr><td colspan="4" style="text-align:center;">Kosong</td></tr>'; return; }
        tbody.innerHTML = '';
        items.forEach(item => {
            const stok = item.totalMasukPcs - item.totalKeluarPcs;
            const row = tbody.insertRow();
            row.insertCell(0).innerText = item.name;
            row.insertCell(1).innerHTML = `<span class="stok-masuk">${item.totalMasukPcs.toLocaleString()} PCS</span>`;
            row.insertCell(2).innerHTML = `<span class="stok-keluar">${item.totalKeluarPcs.toLocaleString()} PCS</span>`;
            row.insertCell(3).innerHTML = stok>=0 ? `<b style="color:#059669;">${stok.toLocaleString()} PCS</b>` : `<b style="color:#dc2626;">${stok.toLocaleString()} PCS (minus)</b>`;
        });
    }
    
    async function renderAllTables() { renderBarangTable(); renderRekap(); }
    
    // Download helpers
    async function downloadBarcodeAsImage(dataURL, fileName, format) {
        const link = document.createElement('a');
        if (format === 'png') { link.download = `${fileName}_barcode.png`; link.href = dataURL; }
        else if (format === 'jpg') {
            const img = new Image(); img.src = dataURL;
            await new Promise(r => img.onload = r);
            const canvas = document.createElement('canvas'); canvas.width = img.width; canvas.height = img.height;
            const ctx = canvas.getContext('2d'); ctx.fillStyle='white'; ctx.fillRect(0,0,canvas.width,canvas.height); ctx.drawImage(img,0,0);
            link.download = `${fileName}_barcode.jpg`; link.href = canvas.toDataURL('image/jpeg',0.9);
        }
        link.click();
    }
    async function downloadBarcodeAsPDF(dataURL, name, code) {
        const { jsPDF } = window.jspdf;
        const img = new Image(); img.src = dataURL;
        await new Promise(r => img.onload = r);
        const pdf = new jsPDF({ unit: 'mm', format: 'a4' });
        const imgWidth = 70, imgHeight = (img.height * imgWidth) / img.width, x = (210 - imgWidth)/2, y = 40;
        pdf.setFontSize(16); pdf.text(`Barcode: ${name}`, 105, 20, { align: 'center' });
        pdf.setFontSize(10); pdf.text(`Kode: ${code}`, 105, 32, { align: 'center' });
        pdf.addImage(img, 'PNG', x, y, imgWidth, imgHeight);
        pdf.text(`Scan untuk transaksi keluar`, 105, y + imgHeight + 8, { align: 'center' });
        pdf.save(`${name}_barcode.pdf`);
    }
    
    // Cetak semua barcode
    document.getElementById('printAllBarcodeBtn').addEventListener('click', () => {
        if(!items.length) { alert("Tidak ada barcode"); return; }
        const printDiv = document.getElementById('printArea');
        let html = `<div style="text-align:center;"><h2>Barcode Inventaris</h2><p>${new Date().toLocaleString()}</p></div><div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:20px;">`;
        items.forEach(item => { html += `<div style="border:1px solid #aaa;border-radius:16px;padding:12px;text-align:center;"><strong>${item.name}</strong><br><small>${item.uniqueCode}</small><br><img src="${item.qrDataURL}" width="130"><br><span style="font-size:9px;">${item.rawValueOUT}</span><br><small>Stok: ${(item.totalMasukPcs-item.totalKeluarPcs).toLocaleString()} PCS</small></div>`; });
        html += `</div>`;
        printDiv.innerHTML = html;
        window.print();
    });
    
    // Export Excel Rekap
    document.getElementById('exportRekapExcel').addEventListener('click', () => {
        const wsData = [["Nama Barang", "Total Masuk (PCS)", "Total Keluar (PCS)", "Stok Akhir (PCS)"]];
        items.forEach(item => { wsData.push([item.name, item.totalMasukPcs, item.totalKeluarPcs, item.totalMasukPcs - item.totalKeluarPcs]); });
        const ws = XLSX.utils.aoa_to_sheet(wsData);
        const wb = XLSX.utils.book_new(); XLSX.utils.book_append_sheet(wb, ws, "Rekap_Stok");
        XLSX.writeFile(wb, `rekap_stok_${new Date().toISOString().slice(0,19)}.xlsx`);
    });
    
    // Dropdown handling
    function updateDropdown() {
        const select = document.getElementById('itemSelect');
        const currentVal = select.value;
        select.innerHTML = '';
        items.forEach(item => { const opt = document.createElement('option'); opt.value = item.name; opt.textContent = item.name; select.appendChild(opt); });
        if(items.length === 0) { const opt = document.createElement('option'); opt.textContent = '-- belum ada barang --'; select.appendChild(opt); }
        else if(currentVal && items.some(i=>i.name===currentVal)) select.value = currentVal;
    }
    
    // Modal tambah barang baru
    const modal = document.getElementById('modalAddItem');
    const openModalBtn = document.getElementById('openModalBtn');
    const closeModalBtn = document.getElementById('closeModalBtn');
    const confirmNewBtn = document.getElementById('confirmNewItemBtn');
    openModalBtn.onclick = () => { modal.style.display = 'flex'; };
    closeModalBtn.onclick = () => { modal.style.display = 'none'; document.getElementById('newItemName').value = ''; };
    confirmNewBtn.onclick = async () => {
        const newName = document.getElementById('newItemName').value.trim();
        if(!newName) { alert("Nama barang harus diisi"); return; }
        if(items.some(i => i.name.toLowerCase() === newName.toLowerCase())) { alert("Barang sudah ada, gunakan dropdown"); modal.style.display='none'; return; }
        const newItem = await createNewItem(newName);
        newItem.totalMasukPcs = 0;
        items.push(newItem);
        saveToLocal();
        renderAllTables();
        updateDropdown();
        document.getElementById('itemSelect').value = newName;
        modal.style.display = 'none';
        document.getElementById('newItemName').value = '';
        document.getElementById('scanMessage').innerHTML = `✅ Barang "${newName}" ditambahkan (stok 0 PCS). Silakan tambah stok masuk.`;
    };
    window.onclick = (e) => { if(e.target === modal) modal.style.display='none'; };
    
    // Tombol tambah stok
    document.getElementById('addStockBtn').addEventListener('click', async () => {
        const selectedName = document.getElementById('itemSelect').value;
        if(!selectedName || (items.length===0 && selectedName === '-- belum ada barang --')) { alert("Pilih nama barang terlebih dahulu atau tambah barang baru"); return; }
        const unit = document.getElementById('unitSelect').value;
        let qty = parseFloat(document.getElementById('qtyUnit').value);
        if(isNaN(qty) || qty<=0) qty=1;
        await addStockIn(selectedName, unit, qty);
        updateDropdown();
    });
    
    // Local storage
    function saveToLocal() { localStorage.setItem('barcodeUnitSystem', JSON.stringify(items.map(i => ({ id:i.id, name:i.name, uniqueCode:i.uniqueCode, totalMasukPcs:i.totalMasukPcs, totalKeluarPcs:i.totalKeluarPcs, qrDataURL:i.qrDataURL, rawValueOUT:i.rawValueOUT })))); }
    function loadFromLocal() {
        const stored = localStorage.getItem('barcodeUnitSystem');
        if(stored && stored.length>2) { items = JSON.parse(stored); renderAllTables(); updateDropdown(); }
        else { initDemoData(); }
    }
    async function initDemoData() {
        const demo1 = await createNewItem("Speaker JBL"); demo1.totalMasukPcs = 5000; demo1.totalKeluarPcs = 1200;
        const demo2 = await createNewItem("Mouse Logitech"); demo2.totalMasukPcs = 3000; demo2.totalKeluarPcs = 500;
        items = [demo1, demo2];
        saveToLocal(); renderAllTables(); updateDropdown();
    }
    
    // Scanner
    let scanner = null;
    const startScanBtn = document.getElementById('startScanBtn'), stopScanBtn = document.getElementById('stopScanBtn'), readerDiv = document.getElementById('reader');
    startScanBtn.addEventListener('click', async () => {
        if(scanner) await stopScanHandler();
        readerDiv.innerHTML = "";
        scanner = new Html5Qrcode("reader");
        try { await scanner.start({ facingMode: "environment" }, { fps:10, qrbox:{ width:280, height:280 } }, (decoded) => processScanOutgoing(decoded), (err)=>{}); document.getElementById('scanMessage').innerHTML = "📷 Kamera aktif, arahkan ke QR"; }
        catch(e) { document.getElementById('scanMessage').innerHTML = "❌ Gagal akses kamera: "+e; }
    });
    async function stopScanHandler() { if(scanner && scanner.isScanning) { await scanner.stop(); scanner.clear(); scanner=null; } readerDiv.innerHTML=""; document.getElementById('scanMessage').innerHTML = "⏹ Scanner dihentikan."; }
    stopScanBtn.addEventListener('click', stopScanHandler);
    
    window.addEventListener('DOMContentLoaded', () => { updatePcsPreview(); loadFromLocal(); });
</script>
</body>
</html>
