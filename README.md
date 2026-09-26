
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Daily Sales Counter</title>
  <style>
    :root {
      --bg: #f8fafc;
      --surface: #ffffff;
      --header-bg: #0f172a;
      --text: #0f172a;
      --text-muted: #64748b;
      --border: #e2e8f0;
      --primary: #059669;
      --primary-hover: #047857;
      --danger: #e11d48;
      --danger-bg: #ffe4e6;
      --radius: 10px;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; }
    body { background: var(--bg); color: var(--text); padding-bottom: 40px; }
    header { background: var(--header-bg); color: #fff; padding: 14px 16px; position: sticky; top: 0; z-index: 20; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
    .header-inner { max-width: 900px; margin: 0 auto; display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 10px; }
    .title-group h1 { font-size: 1.15rem; font-weight: 700; }
    .badge { font-size: 0.72rem; color: #34d399; font-weight: 600; display: inline-flex; align-items: center; gap: 4px; }
    .badge::before { content: ''; width: 6px; height: 6px; background: #34d399; border-radius: 50%; }
    .container { max-width: 900px; margin: 18px auto; padding: 0 16px; display: flex; flex-direction: column; gap: 16px; }
    .card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 16px; box-shadow: 0 1px 3px rgba(0,0,0,0.04); }
    .metrics-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 12px; }
    .metric-card { background: #fff; border: 1px solid var(--border); border-radius: var(--radius); padding: 14px; }
    .metric-card.highlight { background: #0f172a; color: #fff; border-color: #1e293b; }
    .metric-label { font-size: 0.75rem; text-transform: uppercase; font-weight: 700; letter-spacing: 0.5px; color: var(--text-muted); margin-bottom: 6px; }
    .metric-card.highlight .metric-label { color: #94a3b8; }
    .metric-value { font-size: 1.7rem; font-weight: 800; font-variant-numeric: tabular-nums; }
    .metric-card.highlight .metric-value { color: #34d399; }
    .form-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(140px, 1fr)); gap: 12px; margin-top: 10px; }
    label { display: block; font-size: 0.75rem; font-weight: 700; text-transform: uppercase; color: #334155; margin-bottom: 4px; }
    input, select { width: 100%; padding: 10px 12px; border: 1px solid #cbd5e1; border-radius: 8px; font-size: 0.9rem; background: #fff; }
    input:focus, select:focus { outline: none; border-color: var(--primary); box-shadow: 0 0 0 3px rgba(5,150,105,0.15); }
    .btn { cursor: pointer; border: none; padding: 10px 16px; border-radius: 8px; font-size: 0.85rem; font-weight: 700; display: inline-flex; align-items: center; justify-content: center; gap: 6px; transition: background 0.15s; }
    .btn-primary { background: var(--primary); color: #fff; }
    .btn-primary:hover { background: var(--primary-hover); }
    .btn-dark { background: #1e293b; color: #fff; }
    .btn-dark:hover { background: #0f172a; }
    .btn-outline { background: #fff; border: 1px solid var(--border); color: #334155; }
    .btn-outline:hover { background: #f1f5f9; }
    .btn-danger { background: var(--danger-bg); color: var(--danger); }
    .btn-danger:hover { background: #fecdd3; }
    .line-total-banner { background: #ecfdf5; border: 1px solid #a7f3d0; padding: 10px 14px; border-radius: 8px; display: flex; align-items: center; justify-content: space-between; font-weight: 700; color: #065f46; }
    .table-container { overflow-x: auto; margin-top: 10px; }
    table { width: 100%; border-collapse: collapse; text-align: left; font-size: 0.88rem; }
    th { background: #f8fafc; padding: 10px 12px; font-size: 0.72rem; text-transform: uppercase; color: #64748b; border-bottom: 2px solid var(--border); }
    td { padding: 10px 12px; border-bottom: 1px solid #f1f5f9; vertical-align: middle; }
    tr:hover td { background: #f8fafc; }
    .unit-badge { background: #f1f5f9; padding: 2px 6px; border-radius: 4px; font-size: 0.78rem; font-weight: 600; color: #475569; }
    .toast { position: fixed; top: 16px; right: 16px; background: #0f172a; color: #fff; padding: 10px 16px; border-radius: 8px; font-size: 0.85rem; font-weight: 600; z-index: 100; box-shadow: 0 4px 12px rgba(0,0,0,0.15); }
    @media (max-width: 600px) {
      .form-grid { grid-template-columns: 1fr; }
      .metrics-grid { grid-template-columns: 1fr; }
    }
  </style>
</head>
<body>
  <header>
    <div class="header-inner">
      <div class="title-group">
        <h1>Daily Sales Counter</h1>
        <div class="badge">Offline LocalStorage Active</div>
      </div>
      <div style="display:flex; gap: 8px;">
        <button id="btnExportCSV" class="btn btn-primary" style="padding: 6px 12px; font-size: 0.8rem;">Export CSV</button>
      </div>
    </div>
  </header>

  <div class="container">
    <!-- Date Navigation -->
    <div class="card" style="display:flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px;">
      <div style="display:flex; align-items:center; gap: 8px;">
        <button id="btnPrevDay" class="btn btn-outline" style="padding: 6px 10px;">&larr;</button>
        <input type="date" id="datePicker" style="width: auto; padding: 6px 10px; font-weight: 700;">
        <button id="btnNextDay" class="btn btn-outline" style="padding: 6px 10px;">&rarr;</button>
      </div>
      <div style="display:flex; gap: 6px;">
        <button id="btnToday" class="btn btn-outline" style="padding: 6px 12px; font-size: 0.8rem;">Today</button>
        <button id="btnYesterday" class="btn btn-outline" style="padding: 6px 12px; font-size: 0.8rem;">Yesterday</button>
      </div>
    </div>

    <!-- Real-time Metric Cards -->
    <div class="metrics-grid">
      <div class="metric-card highlight">
        <div class="metric-label">Grand Total Revenue</div>
        <div class="metric-value" id="cardGrandTotal">$0.00</div>
        <div style="font-size: 0.75rem; color: #94a3b8; margin-top: 4px;">Sales for selected date</div>
      </div>
      <div class="metric-card">
        <div class="metric-label">Total Entries</div>
        <div class="metric-value" id="cardTotalEntries">0</div>
        <div style="font-size: 0.75rem; color: var(--text-muted); margin-top: 4px;">Transactions logged</div>
      </div>
      <div class="metric-card">
        <div class="metric-label">Total Units Sold</div>
        <div class="metric-value" id="cardTotalUnits">0</div>
        <div id="cardUnitBreakdown" style="font-size: 0.75rem; color: var(--text-muted); margin-top: 4px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap;">0 units</div>
      </div>
    </div>

    <!-- New Entry Form -->
    <div class="card">
      <h2 style="font-size: 0.95rem; font-weight: 700; margin-bottom: 12px;">Add New Sale Entry</h2>
      <form id="saleForm">
        <div class="form-grid">
          <div style="grid-column: span 2;">
            <label for="itemName">1) Item Name</label>
            <input type="text" id="itemName" required placeholder="e.g. Milk, Apples, T-Shirt" autocomplete="off" />
          </div>
          <div>
            <label for="unitSelect">2) Unit</label>
            <select id="unitSelect">
              <option value="pcs">pcs (pieces)</option>
              <option value="kg">kg</option>
              <option value="gm">gm</option>
              <option value="liter">liter</option>
              <option value="meter">meter</option>
              <option value="packet">packet</option>
              <option value="box">box</option>
              <option value="custom">Custom Entry...</option>
            </select>
            <input type="text" id="customUnitInput" placeholder="Enter custom unit" style="display: none; margin-top: 6px;" />
          </div>
          <div>
            <label for="quantity">3) Quantity</label>
            <input type="number" id="quantity" step="any" min="0.001" value="1" required />
          </div>
          <div>
            <label for="unitPrice">4) Price / Unit ($)</label>
            <input type="number" id="unitPrice" step="any" min="0" placeholder="0.00" required />
          </div>
        </div>

        <div style="display:flex; justify-content: space-between; align-items: center; margin-top: 16px; flex-wrap: wrap; gap: 10px;">
          <div class="line-total-banner">
            <span>Line Total: &nbsp;</span>
            <span id="liveLineTotal" style="font-size: 1.15rem;">$0.00</span>
          </div>
          <button type="submit" class="btn btn-primary" style="padding: 10px 24px;">+ Add Sale</button>
        </div>
      </form>
    </div>

    <!-- Interactive Table -->
    <div class="card">
      <div style="display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px; margin-bottom: 10px;">
        <h2 style="font-size: 0.95rem; font-weight: 700;">Sales Register Table</h2>
        <input type="text" id="tableSearch" placeholder="Search items..." style="max-width: 200px; padding: 6px 10px; font-size: 0.8rem;" />
      </div>
      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th>Time</th>
              <th>Item Name</th>
              <th>Unit</th>
              <th style="text-align: right;">Qty</th>
              <th style="text-align: right;">Price</th>
              <th style="text-align: right;">Line Total</th>
              <th style="text-align: center;">Action</th>
            </tr>
          </thead>
          <tbody id="salesTableBody"></tbody>
        </table>
      </div>
      <div id="emptyMessage" style="text-align: center; padding: 30px 10px; color: var(--text-muted); font-size: 0.9rem; display: none;">
        No sales recorded for this date.
      </div>
    </div>
  </div>

  <div id="toast" class="toast" style="display: none;"></div>

  <script>
    const STORAGE_KEY = 'daily_sales_entries_v1';
    let sales = [];

    function initData() {
      const saved = localStorage.getItem(STORAGE_KEY);
      if (saved) {
        try { sales = JSON.parse(saved); } catch(e) { sales = []; }
      }
      if (!sales || sales.length === 0) {
        const today = getTodayStr();
        sales = [
          { id: '1', date: today, time: '09:30', itemName: 'Fresh Milk', unit: 'liter', quantity: 2, pricePerUnit: 2.50, lineTotal: 5.00 },
          { id: '2', date: today, time: '10:15', itemName: 'Apples', unit: 'kg', quantity: 1.5, pricePerUnit: 3.20, lineTotal: 4.80 }
        ];
        saveData();
      }
    }

    function saveData() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(sales));
    }

    function getTodayStr() {
      const d = new Date();
      return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
    }

    let activeDate = getTodayStr();

    function showToast(msg) {
      const t = document.getElementById('toast');
      t.textContent = msg;
      t.style.display = 'block';
      setTimeout(() => { t.style.display = 'none'; }, 2800);
    }

    // Elements
    const datePicker = document.getElementById('datePicker');
    const unitSelect = document.getElementById('unitSelect');
    const customUnitInput = document.getElementById('customUnitInput');
    const quantityInput = document.getElementById('quantity');
    const unitPriceInput = document.getElementById('unitPrice');
    const liveLineTotal = document.getElementById('liveLineTotal');
    const saleForm = document.getElementById('saleForm');
    const tableBody = document.getElementById('salesTableBody');
    const emptyMessage = document.getElementById('emptyMessage');
    const tableSearch = document.getElementById('tableSearch');

    function updateLiveTotal() {
      const q = parseFloat(quantityInput.value) || 0;
      const p = parseFloat(unitPriceInput.value) || 0;
      liveLineTotal.textContent = '$' + (q * p).toFixed(2);
    }

    unitSelect.addEventListener('change', () => {
      customUnitInput.style.display = unitSelect.value === 'custom' ? 'block' : 'none';
      if (unitSelect.value === 'custom') customUnitInput.focus();
    });

    quantityInput.addEventListener('input', updateLiveTotal);
    unitPriceInput.addEventListener('input', updateLiveTotal);

    function render() {
      datePicker.value = activeDate;
      const filtered = sales.filter(s => s.date === activeDate);
      const query = tableSearch.value.trim().toLowerCase();
      const rows = query ? filtered.filter(s => s.itemName.toLowerCase().includes(query)) : filtered;

      // Calculate Metrics
      let totalRevenue = 0;
      let totalUnits = 0;
      const breakdown = {};

      filtered.forEach(s => {
        totalRevenue += s.lineTotal;
        totalUnits += s.quantity;
        breakdown[s.unit] = (breakdown[s.unit] || 0) + s.quantity;
      });

      document.getElementById('cardGrandTotal').textContent = '$' + totalRevenue.toFixed(2);
      document.getElementById('cardTotalEntries').textContent = filtered.length;
      document.getElementById('cardTotalUnits').textContent = Math.round(totalUnits * 100) / 100;
      
      const bKeys = Object.keys(breakdown);
      document.getElementById('cardUnitBreakdown').textContent = bKeys.length ? bKeys.map(k => `${breakdown[k]} ${k}`).join(' · ') : '0 units';

      // Render Table
      tableBody.innerHTML = '';
      if (rows.length === 0) {
        emptyMessage.style.display = 'block';
      } else {
        emptyMessage.style.display = 'none';
        rows.forEach(s => {
          const tr = document.createElement('tr');
          tr.innerHTML = `
            <td style="color:#64748b; font-size:0.8rem;">${s.time}</td>
            <td style="font-weight:600;">${escapeHtml(s.itemName)}</td>
            <td><span class="unit-badge">${escapeHtml(s.unit)}</span></td>
            <td style="text-align: right; font-weight:600;">${s.quantity}</td>
            <td style="text-align: right;">$${s.pricePerUnit.toFixed(2)}</td>
            <td style="text-align: right; font-weight:700; color:#059669;">$${s.lineTotal.toFixed(2)}</td>
            <td style="text-align: center;">
              <button class="btn btn-danger" style="padding:4px 8px; font-size:0.75rem;" onclick="deleteEntry('${s.id}')">Delete</button>
            </td>
          `;
          tableBody.appendChild(tr);
        });
      }
    }

    function escapeHtml(text) {
      const div = document.createElement('div');
      div.textContent = text;
      return div.innerHTML;
    }

    window.deleteEntry = function(id) {
      sales = sales.filter(s => s.id !== id);
      saveData();
      render();
      showToast('Entry deleted');
    };

    saleForm.addEventListener('submit', (e) => {
      e.preventDefault();
      const name = document.getElementById('itemName').value.trim();
      const unit = unitSelect.value === 'custom' ? (customUnitInput.value.trim() || 'unit') : unitSelect.value;
      const q = parseFloat(quantityInput.value) || 0;
      const p = parseFloat(unitPriceInput.value) || 0;

      if (!name || q <= 0 || p < 0) return;

      const d = new Date();
      const time = `${String(d.getHours()).padStart(2,'0')}:${String(d.getMinutes()).padStart(2,'0')}`;
      const total = Math.round(q * p * 100) / 100;

      sales.unshift({
        id: Date.now().toString(),
        date: activeDate,
        time,
        itemName: name,
        unit,
        quantity: q,
        pricePerUnit: p,
        lineTotal: total
      });

      saveData();
      saleForm.reset();
      unitSelect.value = 'pcs';
      customUnitInput.style.display = 'none';
      quantityInput.value = '1';
      updateLiveTotal();
      render();
      showToast(`Added: ${name} ($${total.toFixed(2)})`);
      document.getElementById('itemName').focus();
    });

    datePicker.addEventListener('change', (e) => {
      activeDate = e.target.value;
      render();
    });

    document.getElementById('btnPrevDay').addEventListener('click', () => {
      const cur = new Date(activeDate);
      cur.setDate(cur.getDate() - 1);
      activeDate = cur.toISOString().split('T')[0];
      render();
    });

    document.getElementById('btnNextDay').addEventListener('click', () => {
      const cur = new Date(activeDate);
      cur.setDate(cur.getDate() + 1);
      activeDate = cur.toISOString().split('T')[0];
      render();
    });

    document.getElementById('btnToday').addEventListener('click', () => {
      activeDate = getTodayStr();
      render();
    });

    document.getElementById('btnYesterday').addEventListener('click', () => {
      const cur = new Date();
      cur.setDate(cur.getDate() - 1);
      activeDate = cur.toISOString().split('T')[0];
      render();
    });

    tableSearch.addEventListener('input', render);

    document.getElementById('btnExportCSV').addEventListener('click', () => {
      const forDate = sales.filter(s => s.date === activeDate);
      if (!forDate.length) return showToast('No entries to export for this date');

      const headers = ['Date', 'Time', 'Item Name', 'Unit', 'Quantity', 'Price Per Unit', 'Line Total'];
      const rows = forDate.map(s => [
        `"${s.date}"`, `"${s.time}"`, `"${s.itemName.replace(/"/g, '""')}"`,
        `"${s.unit.replace(/"/g, '""')}"`, s.quantity, s.pricePerUnit, s.lineTotal
      ]);
      const csv = 'data:text/csv;charset=utf-8,' + [headers.join(','), ...rows.map(r => r.join(','))].join('\n');
      const link = document.createElement('a');
      link.href = encodeURI(csv);
      link.download = `Daily_Sales_${activeDate}.csv`;
      link.click();
      showToast('CSV downloaded');
    });

    initData();
    render();
    updateLiveTotal();
  </script>
</body>
</html>
