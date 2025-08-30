<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Advanced GST Calculator (India)</title>
  <meta name="description" content="Advanced GST Calculator for India: inclusive/exclusive GST, CGST/SGST vs IGST, multi-item billing, step-by-step breakdown." />
  <style>
    :root{
      --bg:#0b1220;        /* deep navy */
      --card:#111a2f;      /* slightly lighter */
      --muted:#8ea0c2;     /* muted text */
      --text:#e9eefb;      /* main text */
      --accent:#7c9cff;    /* brandy blue */
      --accent-2:#5fe1a1;  /* mint */
      --danger:#ff6b6b;
      --ring: 0 0 0 3px rgba(124,156,255,.35);
      --radius: 16px;
    }
    * { box-sizing: border-box; }
    html, body { height: 100%; }
    body{
      margin:0; background: radial-gradient(1200px 700px at 10% -10%, #182444, transparent 60%),
                       radial-gradient(1000px 600px at 110% 10%, #1a2b52, transparent 60%),
                       var(--bg);
      color: var(--text); font: 16px/1.5 system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, Noto Sans, "Helvetica Neue", Arial, "Apple Color Emoji", "Segoe UI Emoji";
    }
    header{
      position: sticky; top:0; z-index: 10; backdrop-filter: blur(8px);
      background: linear-gradient(180deg, rgba(11,18,32,.9), rgba(11,18,32,.65));
      border-bottom: 1px solid rgba(255,255,255,.06);
    }
    .container{ max-width: 1200px; margin: 0 auto; padding: 20px; }
    .title{ display:flex; align-items:center; gap:12px; }
    .logo{ width:36px; height:36px; border-radius:10px; background:linear-gradient(135deg,var(--accent),var(--accent-2)); box-shadow: 0 8px 40px rgba(124,156,255,.3); }
    h1{ font-size: clamp(20px, 2vw + 12px, 34px); margin:0; }
    p.subtitle{ margin:6px 0 0; color: var(--muted); }

    .grid{ display:grid; grid-template-columns: 1.1fr .9fr; gap: 18px; }
    @media (max-width: 920px){ .grid{ grid-template-columns: 1fr; } }

    .card{
      background: linear-gradient(180deg, rgba(255,255,255,.05), rgba(255,255,255,.02));
      border: 1px solid rgba(255,255,255,.08);
      border-radius: var(--radius);
      padding: 16px;
      box-shadow: 0 10px 50px rgba(0,0,0,.25);
    }
    .card h2{ margin: 0 0 10px; font-size: 18px; letter-spacing:.2px }
    .card .muted{ color: var(--muted); font-size: 13px }

    .row{ display:grid; grid-template-columns: repeat(12, 1fr); gap:10px; margin-bottom:10px }
    .row > * { grid-column: span 12; }
    @media (min-width: 720px){
      .span-6{ grid-column: span 6; }
      .span-4{ grid-column: span 4; }
      .span-3{ grid-column: span 3; }
      .span-2{ grid-column: span 2; }
    }
    label{ display:block; font-size:12px; color: var(--muted); margin: 0 0 6px; }
    input[type="text"], input[type="number"], select{
      width:100%; padding: 12px 12px; border-radius: 12px; border: 1px solid rgba(255,255,255,.12);
      background: rgba(255,255,255,.04); color: var(--text);
      outline: none; transition:.2s border, .2s box-shadow;
    }
    input:focus, select:focus{ border-color: var(--accent); box-shadow: var(--ring); }
    .inline{ display:flex; align-items:center; gap:10px; flex-wrap: wrap; }

    .btn{
      appearance:none; border: 1px solid rgba(255,255,255,.12); background: linear-gradient(180deg, rgba(255,255,255,.08), rgba(255,255,255,.02));
      color: var(--text); padding: 10px 14px; border-radius: 12px;
      cursor:pointer; transition:.15s transform, .15s background, .15s border-color;
    }
    .btn:hover{ transform: translateY(-1px); border-color: rgba(255,255,255,.25); }
    .btn.primary{ background: linear-gradient(180deg, var(--accent), #5678ff); border-color: transparent; color: #0b1020; font-weight: 700; }
    .btn.ghost{ background: transparent; border-color: rgba(255,255,255,.18) }
    .btn.danger{ background: linear-gradient(180deg, #ff8b8b, #ff6b6b); border-color: transparent; color: #1a0b0b; font-weight:700 }

    table{ width:100%; border-collapse: collapse; overflow:hidden; border-radius: 12px; }
    thead th{ text-align:left; font-size:12px; color: var(--muted); background: rgba(255,255,255,.05); padding: 10px; }
    tbody td{ padding: 10px; border-bottom: 1px dashed rgba(255,255,255,.08); }
    tbody tr:last-child td{ border-bottom:none; }
    .right{ text-align:right; }

    .summary{
      display:grid; grid-template-columns: repeat(2,1fr); gap:12px; margin-top: 12px;
    }
    @media (max-width:600px){ .summary{ grid-template-columns: 1fr; } }
    .pill{ display:flex; justify-content:space-between; align-items:center; padding: 10px 12px; background: rgba(255,255,255,.05); border:1px solid rgba(255,255,255,.1); border-radius: 12px; }

    details.breakdown{ margin-top:14px; }
    details.breakdown summary{ cursor:pointer; color: var(--accent-2); }

    .footer-actions{ display:flex; gap:10px; flex-wrap:wrap; margin-top:12px }
    .small{ font-size: 12px; color: var(--muted) }
  </style>
</head>
<body>
  <header>
    <div class="container">
      <div class="title">
        <div class="logo" aria-hidden="true"></div>
        <div>
          <h1>Advanced GST Calculator (India)</h1>
          <p class="subtitle">Inclusive & Exclusive GST • CGST/SGST vs IGST • Multi‑item billing • Step‑by‑step breakdown</p>
        </div>
      </div>
    </div>
  </header>

  <main class="container" id="app">
    <div class="grid">
      <!-- INPUT CARD -->
      <section class="card" aria-labelledby="calcHeading">
        <h2 id="calcHeading">Add Item</h2>
        <p class="muted">Enter price as either <strong>exclusive</strong> or <strong>inclusive</strong> of GST. Choose intra‑state (CGST+SGST) or inter‑state (IGST).</p>

        <div class="row">
          <div class="span-6">
            <label for="name">Item / Service Name</label>
            <input id="name" type="text" placeholder="e.g., Laptop Bag" />
          </div>
          <div class="span-3">
            <label for="qty">Quantity</label>
            <input id="qty" type="number" inputmode="decimal" min="0" step="1" value="1" />
          </div>
          <div class="span-3">
            <label for="price">Unit Price</label>
            <input id="price" type="number" inputmode="decimal" min="0" step="0.01" placeholder="₹" />
          </div>

          <div class="span-4">
            <label for="mode">Price Type</label>
            <select id="mode">
              <option value="exclusive">Exclusive of GST</option>
              <option value="inclusive">Inclusive of GST</option>
            </select>
          </div>
          <div class="span-4">
            <label for="rate">GST Rate</label>
            <select id="rate">
              <option value="0">0%</option>
              <option value="5">5%</option>
              <option value="12">12%</option>
              <option value="18" selected>18%</option>
              <option value="28">28%</option>
              <option value="custom">Custom…</option>
            </select>
          </div>
          <div class="span-4" id="customRateWrap" style="display:none">
            <label for="customRate">Custom Rate (%)</label>
            <input id="customRate" type="number" inputmode="decimal" min="0" step="0.01" placeholder="e.g., 7.5" />
          </div>

          <div class="span-6">
            <label>Supply Type</label>
            <div class="inline" role="group" aria-label="Supply type">
              <button class="btn" id="intraBtn" aria-pressed="true">Intra‑state (CGST + SGST)</button>
              <button class="btn ghost" id="interBtn" aria-pressed="false">Inter‑state (IGST)</button>
            </div>
          </div>
          <div class="span-6" style="display:flex; align-items:flex-end; justify-content:flex-end; gap:10px;">
            <button class="btn ghost" id="clearForm">Clear</button>
            <button class="btn primary" id="addItem">Add Item</button>
          </div>
        </div>

        <details class="breakdown">
          <summary>How calculations work</summary>
          <div class="small">
            <p><strong>Exclusive price:</strong> GST = price × (rate/100). Total = price + GST.</p>
            <p><strong>Inclusive price:</strong> Base = price ÷ (1 + rate/100). GST = price − Base.</p>
            <p><strong>Intra‑state:</strong> CGST = SGST = GST ÷ 2. <strong>Inter‑state:</strong> IGST = GST.</p>
          </div>
        </details>
      </section>

      <!-- OUTPUT CARD -->
      <section class="card" aria-labelledby="billHeading">
        <h2 id="billHeading">Items & Bill</h2>
        <div class="inline" style="justify-content:space-between; margin-bottom:10px">
          <div class="muted" id="supplyLabel">Supply: Intra‑state (CGST + SGST)</div>
          <div class="inline">
            <button class="btn ghost" id="printBill">Print / Save PDF</button>
            <button class="btn danger" id="clearAll">Clear All</button>
          </div>
        </div>

        <div class="tableWrap" style="overflow:auto;">
          <table id="itemsTable" aria-describedby="billHeading">
            <thead>
              <tr>
                <th>Item</th>
                <th class="right">Qty</th>
                <th class="right">Base/Unit</th>
                <th class="right">GST %</th>
                <th class="right">GST Amt</th>
                <th class="right">Total</th>
                <th></th>
              </tr>
            </thead>
            <tbody id="itemsBody">
              <tr><td colspan="7" class="small" style="padding:14px; text-align:center; color: var(--muted)">No items yet. Add your first item ➜</td></tr>
            </tbody>
          </table>
        </div>

        <div class="summary" id="summary" aria-live="polite" aria-atomic="true">
          <div class="pill"><span>Taxable Value</span><strong id="taxableValue">₹0.00</strong></div>
          <div class="pill" id="cgstPill"><span>Total CGST</span><strong id="cgstTotal">₹0.00</strong></div>
          <div class="pill" id="sgstPill"><span>Total SGST</span><strong id="sgstTotal">₹0.00</strong></div>
          <div class="pill" id="igstPill" style="display:none"><span>Total IGST</span><strong id="igstTotal">₹0.00</strong></div>
          <div class="pill" style="grid-column: 1 / -1; background: linear-gradient(90deg, rgba(124,156,255,.25), rgba(95,225,161,.25)); border-color: transparent">
            <span>Grand Total</span><strong id="grandTotal" style="font-size: 22px">₹0.00</strong>
          </div>
        </div>
      </section>
    </div>

    <section class="card" style="margin-top:18px">
      <h2>Quick Examples</h2>
      <div class="inline" style="gap:8px; flex-wrap:wrap">
        <button class="btn ghost" data-demo='{"name":"Notebook","qty":2,"price":100,"mode":"exclusive","rate":12}'>Exclusive: ₹100 @ 12%</button>
        <button class="btn ghost" data-demo='{"name":"Shoes","qty":1,"price":1416,"mode":"inclusive","rate":18}'>Inclusive: ₹1416 @ 18%</button>
        <button class="btn ghost" data-demo='{"name":"Custom Service","qty":1,"price":5000,"mode":"exclusive","rate":"custom","custom":7.5}'>Custom 7.5%</button>
      </div>
    </section>

    <p class="small" style="text-align:center; margin:16px 0 40px">Designed for Indian GST education & estimation. For official compliance, verify rates/HSC codes as applicable.</p>
  </main>

  <script>
    const fmt = new Intl.NumberFormat('en-IN', { style: 'currency', currency: 'INR', maximumFractionDigits: 2 });

    const state = {
      supply: 'intra', // 'intra' or 'inter'
      items: []
    };

    // Elements
    const nameEl = document.getElementById('name');
    const qtyEl = document.getElementById('qty');
    const priceEl = document.getElementById('price');
    const rateEl = document.getElementById('rate');
    const customRateWrap = document.getElementById('customRateWrap');
    const customRateEl = document.getElementById('customRate');
    const modeEl = document.getElementById('mode');

    const intraBtn = document.getElementById('intraBtn');
    const interBtn = document.getElementById('interBtn');
    const supplyLabel = document.getElementById('supplyLabel');

    const addItemBtn = document.getElementById('addItem');
    const clearFormBtn = document.getElementById('clearForm');
    const clearAllBtn = document.getElementById('clearAll');
    const printBtn = document.getElementById('printBill');

    const itemsBody = document.getElementById('itemsBody');
    const taxableValueEl = document.getElementById('taxableValue');
    const cgstTotalEl = document.getElementById('cgstTotal');
    const sgstTotalEl = document.getElementById('sgstTotal');
    const igstTotalEl = document.getElementById('igstTotal');
    const grandTotalEl = document.getElementById('grandTotal');

    const cgstPill = document.getElementById('cgstPill');
    const sgstPill = document.getElementById('sgstPill');
    const igstPill = document.getElementById('igstPill');

    // Helpers
    function getRateValue(){
      const val = rateEl.value;
      if (val === 'custom') {
        const r = parseFloat(customRateEl.value);
        return isFinite(r) ? r : 0;
      }
      return parseFloat(val);
    }

    function computeLine({ qty, unitPrice, rate, mode }){
      qty = Number(qty) || 0; unitPrice = Number(unitPrice) || 0; rate = Number(rate) || 0;
      const r = rate/100;
      let baseUnit, gstUnit, totalUnit;
      if (mode === 'inclusive'){
        baseUnit = unitPrice / (1 + r);
        gstUnit = unitPrice - baseUnit;
        totalUnit = unitPrice;
      } else {
        baseUnit = unitPrice;
        gstUnit = unitPrice * r;
        totalUnit = unitPrice + gstUnit;
      }
      const base = baseUnit * qty;
      const gst = gstUnit * qty;
      const total = totalUnit * qty;

      let cgst=0, sgst=0, igst=0;
      if (state.supply === 'intra'){
        cgst = gst/2; sgst = gst/2;
      } else {
        igst = gst;
      }
      return { baseUnit, gstUnit, totalUnit, base, gst, total, cgst, sgst, igst };
    }

    function resetForm(){
      nameEl.value = '';
      qtyEl.value = '1';
      priceEl.value = '';
      rateEl.value = '18';
      customRateEl.value = '';
      modeEl.value = 'exclusive';
      customRateWrap.style.display = 'none';
      nameEl.focus();
    }

    function render(){
      // Toggle CGST/SGST vs IGST pills
      if (state.supply === 'intra'){
        cgstPill.style.display = '';
        sgstPill.style.display = '';
        igstPill.style.display = 'none';
        supplyLabel.textContent = 'Supply: Intra‑state (CGST + SGST)';
      } else {
        cgstPill.style.display = 'none';
        sgstPill.style.display = 'none';
        igstPill.style.display = '';
        supplyLabel.textContent = 'Supply: Inter‑state (IGST)';
      }

      // Table
      itemsBody.innerHTML = '';
      if (state.items.length === 0){
        const tr = document.createElement('tr');
        tr.innerHTML = '<td colspan="7" class="small" style="padding:14px; text-align:center; color: var(--muted)">No items yet. Add your first item ➜</td>';
        itemsBody.appendChild(tr);
      } else {
        state.items.forEach((it, idx) => {
          const line = computeLine(it);
          const tr = document.createElement('tr');
          const safeName = it.name || `Item ${idx+1}`;
          tr.innerHTML = `
            <td>${escapeHtml(safeName)}</td>
            <td class="right">${it.qty}</td>
            <td class="right" title="Base per unit">${fmt.format(line.baseUnit)}</td>
            <td class="right">${(+it.rate).toFixed(2)}%</td>
            <td class="right">${fmt.format(line.gst)}</td>
            <td class="right">${fmt.format(line.total)}</td>
            <td class="right"><button class="btn ghost" data-del="${idx}" aria-label="Remove ${safeName}">✕</button></td>
          `;
          itemsBody.appendChild(tr);
        });
      }

      // Summary
      let taxable = 0, gst=0, cgst=0, sgst=0, igst=0, grand=0;
      state.items.forEach(it => {
        const l = computeLine(it);
        taxable += l.base;
        gst += l.gst;
        cgst += l.cgst; sgst += l.sgst; igst += l.igst;
        grand += l.total;
      });
      taxableValueEl.textContent = fmt.format(taxable);
      cgstTotalEl.textContent = fmt.format(cgst);
      sgstTotalEl.textContent = fmt.format(sgst);
      igstTotalEl.textContent = fmt.format(igst);
      grandTotalEl.textContent = fmt.format(grand);
    }

    function escapeHtml(str){
      return str.replace(/[&<>"']/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;','\'':'&#39;'}[m]));
    }

    // Event wiring
    rateEl.addEventListener('change', () => {
      customRateWrap.style.display = rateEl.value === 'custom' ? '' : 'none';
    });

    intraBtn.addEventListener('click', () => {
      state.supply = 'intra';
      intraBtn.classList.remove('ghost'); intraBtn.setAttribute('aria-pressed','true');
      interBtn.classList.add('ghost'); interBtn.setAttribute('aria-pressed','false');
      render();
    });
    interBtn.addEventListener('click', () => {
      state.supply = 'inter';
      interBtn.classList.remove('ghost'); interBtn.setAttribute('aria-pressed','true');
      intraBtn.classList.add('ghost'); intraBtn.setAttribute('aria-pressed','false');
      render();
    });

    addItemBtn.addEventListener('click', () => {
      const name = nameEl.value.trim();
      const qty = Math.max(0, Number(qtyEl.value || 0));
      const unitPrice = Math.max(0, Number(priceEl.value || 0));
      const mode = modeEl.value;
      const rate = (rateEl.value === 'custom') ? (Number(customRateEl.value || 0)) : Number(rateEl.value || 0);

      if (!qty || !isFinite(qty)) return alert('Enter a valid quantity');
      if (!isFinite(unitPrice)) return alert('Enter a valid price');
      if (!isFinite(rate)) return alert('Enter a valid GST rate');

      state.items.push({ name, qty, unitPrice, mode, rate });
      resetForm();
      render();
    });

    clearFormBtn.addEventListener('click', resetForm);

    clearAllBtn.addEventListener('click', () => {
      if (state.items.length===0) return;
      const ok = confirm('Clear all items?');
      if (!ok) return;
      state.items = [];
      render();
    });

    itemsBody.addEventListener('click', (e) => {
      const btn = e.target.closest('button[data-del]');
      if (!btn) return;
      const idx = Number(btn.getAttribute('data-del'));
      state.items.splice(idx,1);
      render();
    });

    printBtn.addEventListener('click', () => window.print());

    // Demo buttons
    document.querySelectorAll('[data-demo]').forEach(b => {
      b.addEventListener('click', () => {
        const d = JSON.parse(b.getAttribute('data-demo'));
        nameEl.value = d.name; qtyEl.value = d.qty; priceEl.value = d.price; modeEl.value = d.mode;
        rateEl.value = String(d.rate);
        if (d.rate === 'custom'){ rateEl.value = 'custom'; customRateWrap.style.display = ''; customRateEl.value = d.custom; }
        nameEl.focus();
      });
    });

    // Initial render
    render();
  </script>
</body>
</html>
