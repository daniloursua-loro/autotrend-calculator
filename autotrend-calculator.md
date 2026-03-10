<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Autotrend Autoloan Calculator</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;0,9..40,600;0,9..40,700&family=Outfit:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --red-500:#DC2626;--red-600:#B91C1C;--red-700:#991B1B;
  --silver-50:#FAFAFA;--silver-100:#F4F4F5;--silver-200:#E4E4E7;
  --silver-300:#D4D4D8;--silver-400:#A1A1AA;--silver-500:#71717A;
  --silver-600:#52525B;--silver-700:#3F3F46;
  --grad-red:linear-gradient(135deg,#DC2626 0%,#991B1B 100%);
  --grad-card:linear-gradient(160deg,rgba(255,255,255,.85) 0%,rgba(244,244,245,.7) 100%);
  --radius:16px;--radius-sm:10px;
  --shadow-md:0 4px 20px rgba(63,63,70,.07);
}
html{font-size:16px}
body{
  font-family:'DM Sans',sans-serif;
  background:transparent;
  min-height:100vh;color:var(--silver-700);
  -webkit-font-smoothing:antialiased;
}

.calc-wrap{max-width:640px;margin:0 auto;padding:20px 20px 48px}

/* Header - text only */
.header{text-align:center;padding:24px 20px 18px}
.header h1{
  font-family:'Outfit',sans-serif;font-weight:700;
  font-size:1.5rem;color:var(--silver-700);letter-spacing:-.3px;
}
.header p{font-size:.85rem;color:var(--silver-400);margin-top:3px}

/* ===== MODERN STEPPER ===== */
.stepper-bar{
  display:flex;align-items:center;
  background:rgba(244,244,245,.7);
  backdrop-filter:blur(12px);
  border:1px solid rgba(228,228,231,.5);
  border-radius:50px;padding:4px;
  margin:0 0 20px;
}
.stepper-seg{
  flex:1;display:flex;align-items:center;justify-content:center;gap:7px;
  padding:10px 8px;border-radius:50px;
  font-size:.78rem;font-weight:500;color:var(--silver-400);
  cursor:pointer;transition:all .3s cubic-bezier(.4,0,.2,1);
  position:relative;user-select:none;
}
.stepper-seg.active{
  background:#fff;color:var(--red-600);font-weight:600;
  box-shadow:0 2px 8px rgba(63,63,70,.08);
}
.stepper-seg.done{color:var(--silver-500)}
.stepper-seg .seg-num{
  width:22px;height:22px;border-radius:50%;
  display:flex;align-items:center;justify-content:center;
  font-size:.65rem;font-weight:700;
  background:var(--silver-200);color:var(--silver-400);
  transition:all .3s ease;flex-shrink:0;
}
.stepper-seg.active .seg-num{
  background:var(--grad-red);color:#fff;
  box-shadow:0 2px 8px rgba(220,38,38,.25);
}
.stepper-seg.done .seg-num{
  background:var(--silver-300);color:#fff;
}
.stepper-seg .seg-label{display:none}
@media(min-width:420px){.stepper-seg .seg-label{display:inline}}

/* ===== MODE TOGGLE ===== */
.mode-toggle{
  display:flex;
  background:rgba(244,244,245,.6);
  border:1px solid rgba(228,228,231,.5);
  border-radius:50px;padding:3px;margin-bottom:16px;
}
.mode-btn{
  flex:1;padding:9px;border-radius:50px;border:none;
  font-family:'DM Sans',sans-serif;font-size:.8rem;font-weight:500;
  color:var(--silver-400);background:transparent;cursor:pointer;
  transition:all .25s ease;display:flex;align-items:center;justify-content:center;gap:5px;
}
.mode-btn svg{width:14px;height:14px}
.mode-btn.active{
  background:#fff;color:var(--silver-700);font-weight:600;
  box-shadow:0 1px 6px rgba(63,63,70,.08);
}

/* Card */
.card{
  background:var(--grad-card);border-radius:var(--radius);
  border:1px solid rgba(228,228,231,.45);
  box-shadow:var(--shadow-md);
  padding:26px 24px;margin-bottom:14px;
  animation:cardIn .35s cubic-bezier(.4,0,.2,1);
  backdrop-filter:blur(16px);
}
@keyframes cardIn{
  from{opacity:0;transform:translateY(10px)}
  to{opacity:1;transform:translateY(0)}
}
.card-title{
  font-family:'Outfit',sans-serif;font-size:.95rem;
  font-weight:600;color:var(--silver-700);margin-bottom:16px;
  display:flex;align-items:center;gap:8px;
}
.card-title .icon-wrap{
  width:30px;height:30px;border-radius:50%;
  background:linear-gradient(135deg,rgba(220,38,38,.08),rgba(220,38,38,.14));
  display:flex;align-items:center;justify-content:center;
}
.card-title .icon-wrap svg{width:13px;height:13px;color:var(--red-600)}

/* Form */
label{
  display:block;font-size:.73rem;font-weight:500;
  color:var(--silver-500);margin-bottom:5px;letter-spacing:.2px;
}
select,input[type="text"],input[type="number"]{
  width:100%;padding:11px 14px;border-radius:var(--radius-sm);
  border:1.5px solid var(--silver-200);background:rgba(255,255,255,.8);
  font-family:'DM Sans',sans-serif;font-size:.9rem;
  color:var(--silver-700);outline:none;
  transition:border-color .2s,box-shadow .2s;
  appearance:none;-webkit-appearance:none;
}
input[type="number"]::-webkit-inner-spin-button{-webkit-appearance:none}
select{
  background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='16' height='16' viewBox='0 0 24 24' fill='none' stroke='%23A1A1AA' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Cpath d='m6 9 6 6 6-6'/%3E%3C/svg%3E");
  background-repeat:no-repeat;background-position:right 10px center;
  padding-right:32px;cursor:pointer;
}
select:focus,input:focus{
  border-color:var(--red-500);
  box-shadow:0 0 0 3px rgba(220,38,38,.08);
}
.field{margin-bottom:12px}
.field:last-child{margin-bottom:0}

/* Pills */
.option-pills{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-bottom:14px}
.pill{
  padding:10px 8px;border-radius:var(--radius-sm);
  border:1.5px solid var(--silver-200);background:rgba(255,255,255,.7);
  text-align:center;cursor:pointer;transition:all .25s ease;
}
.pill:hover{border-color:var(--silver-300)}
.pill.active{
  border-color:var(--red-500);
  background:linear-gradient(135deg,rgba(220,38,38,.04),rgba(220,38,38,.08));
}
.pill-label{font-size:.72rem;color:var(--silver-400);font-weight:500}
.pill-value{font-size:.82rem;color:var(--silver-700);font-weight:600;margin-top:2px}
.pill.active .pill-label{color:var(--red-600)}
.pill.active .pill-value{color:var(--red-700)}

/* Term grid */
.term-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:6px}
.term-btn{
  padding:9px 4px;border-radius:9px;border:1.5px solid var(--silver-200);
  background:rgba(255,255,255,.7);text-align:center;cursor:pointer;
  transition:all .2s;font-family:'DM Sans',sans-serif;
}
.term-btn:hover{border-color:var(--silver-300)}
.term-btn.active{
  border-color:var(--red-500);
  background:linear-gradient(135deg,rgba(220,38,38,.04),rgba(220,38,38,.08));
}
.term-btn .yr{font-size:.88rem;font-weight:600;color:var(--silver-600)}
.term-btn .mo{font-size:.65rem;color:var(--silver-400);margin-top:1px}
.term-btn.active .yr{color:var(--red-700)}
.term-btn.active .mo{color:var(--red-500)}

/* Buttons */
.btn-row{display:flex;gap:8px;margin-top:16px}
.btn{
  flex:1;padding:12px;border-radius:var(--radius-sm);
  font-family:'DM Sans',sans-serif;font-size:.85rem;font-weight:600;
  border:none;cursor:pointer;
  display:flex;align-items:center;justify-content:center;gap:5px;
  transition:all .25s cubic-bezier(.4,0,.2,1);
}
.btn svg{width:15px;height:15px}
.btn-primary{
  background:var(--grad-red);color:#fff;
  box-shadow:0 3px 12px rgba(220,38,38,.2);
}
.btn-primary:hover{
  box-shadow:0 5px 18px rgba(220,38,38,.3);
  transform:translateY(-1px);
}
.btn-secondary{
  background:rgba(244,244,245,.8);color:var(--silver-600);
  border:1.5px solid var(--silver-200);
}
.btn-secondary:hover{background:var(--silver-200)}

/* Result */
.result-hero{text-align:center;padding:18px 0 12px}
.result-hero .monthly-label{
  font-size:.7rem;color:var(--silver-400);font-weight:500;
  text-transform:uppercase;letter-spacing:.5px;
}
.result-hero .monthly-amount{
  font-family:'Outfit',sans-serif;font-size:2.3rem;font-weight:700;
  background:var(--grad-red);-webkit-background-clip:text;
  -webkit-text-fill-color:transparent;background-clip:text;
  line-height:1.2;margin-top:4px;
}
.result-hero .monthly-suffix{font-size:.7rem;color:var(--silver-400);margin-top:2px}

.breakdown{margin-top:12px}
.breakdown-row{
  display:flex;justify-content:space-between;align-items:center;
  padding:7px 0;border-bottom:1px solid rgba(228,228,231,.4);
}
.breakdown-row:last-child{border-bottom:none}
.breakdown-row .lbl{font-size:.82rem;color:var(--silver-500)}
.breakdown-row .val{font-size:.86rem;font-weight:600;color:var(--silver-700)}
.breakdown-row.highlight .val{color:var(--red-600)}

.divider{
  height:1px;
  background:linear-gradient(90deg,transparent,var(--silver-200),transparent);
  margin:10px 0;
}

.disclaimer{
  margin-top:14px;padding:10px 12px;
  border-radius:var(--radius-sm);
  background:rgba(244,244,245,.5);
  border:1px solid rgba(228,228,231,.4);
}
.disclaimer p{font-size:.63rem;color:var(--silver-400);line-height:1.5}

.hidden{display:none!important}
.fade-in{animation:cardIn .35s cubic-bezier(.4,0,.2,1)}

@media(max-width:400px){
  .calc-wrap{padding:10px 10px 30px}
  .card{padding:18px 14px}
  .result-hero .monthly-amount{font-size:1.7rem}
  .term-grid{grid-template-columns:repeat(2,1fr)}
}
</style>
</head>
<body>

<div class="calc-wrap">

  <!-- Header -->
  <div class="header">
    <h1>Autotrend Autoloan</h1>
    <p>Vehicle Loan Calculator</p>
  </div>

  <!-- Mode toggle -->
  <div class="mode-toggle" style="display:none">
    <button class="mode-btn active" id="modeCatalog" onclick="switchMode('catalog')">
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="7" height="7"/><rect x="14" y="3" width="7" height="7"/><rect x="3" y="14" width="7" height="7"/><rect x="14" y="14" width="7" height="7"/></svg>
      From Catalog
    </button>
    <button class="mode-btn" id="modeManual" onclick="switchMode('manual')" style="display:none">
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z"/></svg>
      Quick Calculate
    </button>
  </div>

  <!-- Modern Stepper -->
  <div class="stepper-bar" id="stepperBar">
    <div class="stepper-seg active" data-step="1" onclick="clickStep(1)">
      <span class="seg-num">1</span>
      <span class="seg-label">Vehicle</span>
    </div>
    <div class="stepper-seg" data-step="2" onclick="clickStep(2)">
      <span class="seg-num">2</span>
      <span class="seg-label">Plan</span>
    </div>
    <div class="stepper-seg" data-step="3" onclick="clickStep(3)">
      <span class="seg-num">3</span>
      <span class="seg-label">Term</span>
    </div>
    <div class="stepper-seg" data-step="4" onclick="clickStep(4)">
      <span class="seg-num">4</span>
      <span class="seg-label">Result</span>
    </div>
  </div>

  <!-- Step 1: Catalog Vehicle Selection -->
  <div class="card" id="step1">
    <div class="card-title">
      <span class="icon-wrap">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="9" cy="21" r="1"/><circle cx="20" cy="21" r="1"/><path d="M1 1h4l2.68 13.39a2 2 0 0 0 2 1.61h9.72a2 2 0 0 0 2-1.61L23 6H6"/></svg>
      </span>
      Select Your Vehicle
    </div>
    <div class="field">
      <label>Brand</label>
      <select id="brandSelect" onchange="onBrandChange()">
        <option value="">Choose a brand</option>
      </select>
    </div>
    <div class="field">
      <label>Model</label>
      <select id="unitSelect" onchange="onUnitChange()">
        <option value="">Choose brand first</option>
      </select>
    </div>
    <div class="field">
      <label>Variant / Transmission</label>
      <select id="variantSelect">
        <option value="">Choose model first</option>
      </select>
    </div>
    <div class="btn-row">
      <button class="btn btn-primary" onclick="goStep(2)">
        Continue
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
      </button>
    </div>
  </div>

  <!-- Step 1b: Manual Quick Calc -->
  <div class="card hidden" id="step1m">
    <div class="card-title">
      <span class="icon-wrap">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z"/></svg>
      </span>
      Quick Calculate
    </div>
    <div class="field">
      <label>Car Model / Description</label>
      <input type="text" id="manualModel" placeholder="e.g. 2026 Toyota Vios XLE CVT">
    </div>
    <div class="field">
      <label>SRP (Suggested Retail Price)</label>
      <input type="number" id="manualSRP" placeholder="e.g. 1050000" oninput="updateManualPreview()">
    </div>
    <div id="manualPreview" style="margin-top:8px"></div>
    <div class="btn-row">
      <button class="btn btn-primary" onclick="goStep(2)">
        Continue
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
      </button>
    </div>
  </div>

  <!-- Step 2: Payment Option -->
  <div class="card hidden" id="step2">
    <div class="card-title">
      <span class="icon-wrap">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect width="20" height="14" x="2" y="5" rx="2"/><line x1="2" x2="22" y1="10" y2="10"/></svg>
      </span>
      Choose Payment Plan
    </div>
    <div class="option-pills">
      <div class="pill active" data-option="1" onclick="selectOption(1)">
        <div class="pill-label">Option 1</div>
        <div class="pill-value">Pay-All DP</div>
      </div>
      <div class="pill" data-option="2" onclick="selectOption(2)">
        <div class="pill-label">Option 2</div>
        <div class="pill-value">CMF to Monthly</div>
      </div>
    </div>
    <div id="optionSummary"></div>
    <div class="btn-row">
      <button class="btn btn-secondary" onclick="goStep(1)">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m12 19-7-7 7-7"/><path d="M19 12H5"/></svg>
        Back
      </button>
      <button class="btn btn-primary" onclick="goStep(3)">
        Continue
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
      </button>
    </div>
  </div>

  <!-- Step 3: Loan Term -->
  <div class="card hidden" id="step3">
    <div class="card-title">
      <span class="icon-wrap">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>
      </span>
      Select Loan Term
    </div>
    <div class="term-grid">
      <div class="term-btn" data-term="5" onclick="selectTerm(5)">
        <div class="yr">5 Yrs</div><div class="mo">60 mos</div>
      </div>
      <div class="term-btn" data-term="4" onclick="selectTerm(4)">
        <div class="yr">4 Yrs</div><div class="mo">48 mos</div>
      </div>
      <div class="term-btn" data-term="3" onclick="selectTerm(3)">
        <div class="yr">3 Yrs</div><div class="mo">36 mos</div>
      </div>
      <div class="term-btn" data-term="2" onclick="selectTerm(2)">
        <div class="yr">2 Yrs</div><div class="mo">24 mos</div>
      </div>
    </div>
    <div class="btn-row">
      <button class="btn btn-secondary" onclick="goStep(2)">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m12 19-7-7 7-7"/><path d="M19 12H5"/></svg>
        Back
      </button>
      <button class="btn btn-primary" onclick="goStep(4)">
        View Result
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
      </button>
    </div>
  </div>

  <!-- Step 4: Results -->
  <div class="card hidden" id="step4">
    <div class="card-title">
      <span class="icon-wrap">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M6 8h10a4 4 0 0 1 0 8H8V4"/><line x1="5" y1="10" x2="18" y2="10"/><line x1="5" y1="14" x2="18" y2="14"/></svg>
      </span>
      Loan Summary
    </div>

    <div id="vehicleTag" style="font-size:.7rem;color:var(--silver-400);margin-bottom:4px;text-align:center"></div>

    <div class="result-hero">
      <div class="monthly-label">Monthly Amortization</div>
      <div class="monthly-amount" id="monthlyAmt">---</div>
      <div class="monthly-suffix" id="termSuffix"></div>
    </div>

    <div class="divider"></div>
    <div class="breakdown" id="breakdown"></div>
    <div class="divider"></div>

    <div class="cta-section" style="text-align:center;margin-top:16px;padding:16px;background:rgba(220,38,38,.04);border-radius:10px;border:1px solid rgba(220,38,38,.12);">
      <p style="font-size:.82rem;color:var(--silver-600);margin-bottom:10px;line-height:1.5;">Have any questions? Feel free to talk to our sales team!</p>
      <a href="https://m.me/autotrend" target="_blank" rel="noopener noreferrer" style="display:inline-flex;align-items:center;gap:6px;background:var(--grad-red);color:#fff;padding:10px 20px;border-radius:50px;font-family:'DM Sans',sans-serif;font-size:.83rem;font-weight:600;text-decoration:none;box-shadow:0 3px 12px rgba(220,38,38,.2);">
        <svg xmlns="http://www.w3.org/2000/svg" width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/></svg>
        Talk to Agent
      </a>
    </div>

    <div class="disclaimer">
      <p>This quotation is for estimation purposes only and is subject to the final result of the Credit Investigation. All figures are based on the offer provided by the dealership. Autotrend Autoloan Services has no control over the total cashout amount. Only standard Planta discount applies.</p>
    </div>

    <div class="btn-row" style="margin-top:12px">
      <button class="btn btn-secondary" onclick="goStep(1)">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 12a9 9 0 0 1 9-9 9.75 9.75 0 0 1 6.74 2.74L21 8"/><path d="M21 3v5h-5"/><path d="M21 12a9 9 0 0 1-9 9 9.75 9.75 0 0 1-6.74-2.74L3 16"/><path d="M8 16H3v5"/></svg>
        New Calc
      </button>
      <button class="btn btn-primary" onclick="downloadPDF()">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" x2="12" y1="15" y2="3"/></svg>
        Download PDF
      </button>
    </div>
  </div>

</div>

<script>
const DATA = [{"b": "MITSUBISHI", "u": "MIRAGE HB", "v": "GLX", "t": "MT", "s": 720000, "d": 105000, "dp": 144000, "la": 576000, "cmf": 48960, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 112960, "o15": 13920, "o14": 16320, "o13": 20320, "o12": 28320, "o2c": 66050, "o25": 15103, "o24": 17707, "o23": 22047, "o22": 30727}, {"b": "MITSUBISHI", "u": "MIRAGE HB", "v": "GLX", "t": "CVT", "s": 781000, "d": 105000, "dp": 156200, "la": 624800, "cmf": 53108, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 129308, "o15": 15099, "o14": 17703, "o13": 22042, "o12": 30719, "o2c": 78250, "o25": 16383, "o24": 19207, "o23": 23915, "o22": 33330}, {"b": "MITSUBISHI", "u": "MIRAGE G4", "v": "GLX", "t": "MT", "s": 793000, "d": 71000, "dp": 158600, "la": 634400, "cmf": 53924, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 169524, "o15": 15331, "o14": 17975, "o13": 22380, "o12": 31191, "o2c": 117650, "o25": 16634, "o24": 19503, "o23": 24283, "o22": 33843}, {"b": "MITSUBISHI", "u": "MIRAGE G4", "v": "GLX", "t": "CVT", "s": 841000, "d": 104000, "dp": 168200, "la": 672800, "cmf": 57188, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 149388, "o15": 16259, "o14": 19063, "o13": 23735, "o12": 33079, "o2c": 94250, "o25": 17641, "o24": 20683, "o23": 25752, "o22": 35891}, {"b": "MITSUBISHI", "u": "MIRAGE G4", "v": "GLS", "t": "CVT", "s": 937000, "d": 96000, "dp": 187400, "la": 749600, "cmf": 63716, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 183116, "o15": 18115, "o14": 21239, "o13": 26444, "o12": 36855, "o2c": 121450, "o25": 19655, "o24": 23044, "o23": 28692, "o22": 39988}, {"b": "MITSUBISHI", "u": "XPANDER", "v": "GLX", "t": "MT", "s": 1099000, "d": 90000, "dp": 219800, "la": 879200, "cmf": 74732, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 234532, "o15": 21247, "o14": 24911, "o13": 31016, "o12": 43227, "o2c": 161850, "o25": 23053, "o24": 27028, "o23": 33653, "o22": 46902}, {"b": "MITSUBISHI", "u": "XPANDER", "v": "GLX", "t": "AT", "s": 1159000, "d": 110000, "dp": 231800, "la": 927200, "cmf": 78812, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 230612, "o15": 22407, "o14": 26271, "o13": 32710, "o12": 45587, "o2c": 153850, "o25": 24312, "o24": 28504, "o23": 35490, "o22": 49462}, {"b": "MITSUBISHI", "u": "XPANDER", "v": "GLS", "t": "AT", "s": 1259000, "d": 120000, "dp": 251800, "la": 1007200, "cmf": 85612, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 247412, "o15": 24341, "o14": 28537, "o13": 35532, "o12": 49521, "o2c": 163850, "o25": 26410, "o24": 30963, "o23": 38552, "o22": 53730}, {"b": "MITSUBISHI", "u": "XPANDER", "v": "CROSS GLS", "t": "AT", "s": 1378000, "d": 101000, "dp": 275600, "la": 1102400, "cmf": 93704, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 298304, "o15": 26641, "o14": 31235, "o13": 38890, "o12": 54201, "o2c": 206650, "o25": 28906, "o24": 33890, "o23": 42196, "o22": 58808}, {"b": "MITSUBISHI", "u": "XPANDER", "v": "CROSS GLS", "t": "AT - PW", "s": 1393000, "d": 101000, "dp": 278600, "la": 1114400, "cmf": 94724, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 302324, "o15": 26931, "o14": 31575, "o13": 39314, "o12": 54791, "o2c": 209650, "o25": 29220, "o24": 34259, "o23": 42655, "o22": 59449}, {"b": "MITSUBISHI", "u": "L300 VAN", "v": "FB DUAL AC", "t": "MT", "s": 1039000, "d": 100000, "dp": 207800, "la": 831200, "cmf": 70652, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 208452, "o15": 20087, "o14": 23551, "o13": 29323, "o12": 40867, "o2c": 139850, "o25": 21795, "o24": 25552, "o23": 31815, "o22": 44341}, {"b": "MITSUBISHI", "u": "VERSA VAN", "v": "15STRS", "t": "MT", "s": 1649000, "d": 65000, "dp": 329800, "la": 1319200, "cmf": 112132, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 416932, "o15": 31881, "o14": 37377, "o13": 46538, "o12": 64861, "o2c": 306850, "o25": 34591, "o24": 40554, "o23": 50494, "o22": 70374}, {"b": "MITSUBISHI", "u": "XFORCE", "v": "GLS", "t": "CVT", "s": 1367000, "d": 270000, "dp": 273400, "la": 1093600, "cmf": 92956, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 131356, "o15": 26429, "o14": 30985, "o13": 38580, "o12": 53769, "o2c": 40450, "o25": 28675, "o24": 33619, "o23": 41859, "o22": 58339}, {"b": "MITSUBISHI", "u": "XFORCE", "v": "GLS", "t": "CVT - PW", "s": 1382000, "d": 270000, "dp": 276400, "la": 1105600, "cmf": 93976, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 135376, "o15": 26719, "o14": 31325, "o13": 39003, "o12": 54359, "o2c": 43450, "o25": 28990, "o24": 33988, "o23": 42318, "o22": 58979}, {"b": "MITSUBISHI", "u": "XFORCE", "v": "GT", "t": "CVT", "s": 1581000, "d": 270000, "dp": 316200, "la": 1264800, "cmf": 107508, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 188708, "o15": 30566, "o14": 35836, "o13": 44619, "o12": 62186, "o2c": 83250, "o25": 33164, "o24": 38882, "o23": 48412, "o22": 67472}, {"b": "MITSUBISHI", "u": "XFORCE", "v": "GT", "t": "CVT - PW", "s": 1596000, "d": 270000, "dp": 319200, "la": 1276800, "cmf": 108528, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 192728, "o15": 30856, "o14": 36176, "o13": 45043, "o12": 62776, "o2c": 86250, "o25": 33479, "o24": 39251, "o23": 48871, "o22": 68112}, {"b": "MITSUBISHI", "u": "DESTINATOR", "v": "GLX", "t": "CVT", "s": 1389000, "d": 100000, "dp": 277800, "la": 1111200, "cmf": 94452, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 307252, "o15": 26854, "o14": 31484, "o13": 39201, "o12": 54634, "o2c": 214850, "o25": 29137, "o24": 34160, "o23": 42533, "o22": 59278}, {"b": "MITSUBISHI", "u": "DESTINATOR", "v": "GLS", "t": "CVT", "s": 1599000, "d": 100000, "dp": 319800, "la": 1279200, "cmf": 108732, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 363532, "o15": 30914, "o14": 36244, "o13": 45127, "o12": 62894, "o2c": 256850, "o25": 33542, "o24": 39325, "o23": 48963, "o22": 68240}, {"b": "MITSUBISHI", "u": "DESTINATOR", "v": "GT", "t": "CVT - 1T", "s": 1929000, "d": 130000, "dp": 385800, "la": 1543200, "cmf": 131172, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 421972, "o15": 37294, "o14": 43724, "o13": 54441, "o12": 75874, "o2c": 292850, "o25": 40464, "o24": 47441, "o23": 59068, "o22": 82323}, {"b": "MITSUBISHI", "u": "DESTINATOR", "v": "GT", "t": "CVT - 2T", "s": 1939000, "d": 130000, "dp": 387800, "la": 1551200, "cmf": 131852, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 424652, "o15": 37487, "o14": 43951, "o13": 54723, "o12": 76267, "o2c": 294850, "o25": 40674, "o24": 47686, "o23": 59374, "o22": 82750}, {"b": "MITSUBISHI", "u": "MONTERO", "v": "GLX", "t": "MT", "s": 1568000, "d": 265000, "dp": 313600, "la": 1254400, "cmf": 106624, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 195224, "o15": 30315, "o14": 35541, "o13": 44252, "o12": 61675, "o2c": 90650, "o25": 32891, "o24": 38562, "o23": 48014, "o22": 66917}, {"b": "MITSUBISHI", "u": "MONTERO", "v": "GLS 4X2", "t": "AT", "s": 1927000, "d": 100000, "dp": 385400, "la": 1541600, "cmf": 131036, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 456436, "o15": 37255, "o14": 43679, "o13": 54384, "o12": 75795, "o2c": 327450, "o25": 40422, "o24": 47391, "o23": 59007, "o22": 82238}, {"b": "MITSUBISHI", "u": "MONTERO", "v": "GLS 4X2", "t": "AT - PW", "s": 1942000, "d": 100000, "dp": 388400, "la": 1553600, "cmf": 132056, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 460456, "o15": 37545, "o14": 44019, "o13": 54808, "o12": 76385, "o2c": 330450, "o25": 40737, "o24": 47760, "o23": 59466, "o22": 82878}, {"b": "MITSUBISHI", "u": "MONTERO", "v": "BLACK SERIES 4X2", "t": "AT", "s": 2190000, "d": 82000, "dp": 438000, "la": 1752000, "cmf": 148920, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 544920, "o15": 42340, "o14": 49640, "o13": 61807, "o12": 86140, "o2c": 398050, "o25": 45939, "o24": 53859, "o23": 67060, "o22": 93462}, {"b": "MITSUBISHI", "u": "MONTERO", "v": "BLACK SERIES 4X4", "t": "AT", "s": 2530000, "d": 20000, "dp": 506000, "la": 2024000, "cmf": 172040, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 698040, "o15": 48913, "o14": 57347, "o13": 71402, "o12": 99513, "o2c": 528050, "o25": 53071, "o24": 62221, "o23": 77471, "o22": 107972}, {"b": "MITSUBISHI", "u": "TRITON", "v": "GL", "t": "MT", "s": 1266000, "d": 0, "dp": 253200, "la": 1012800, "cmf": 86088, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 379288, "o15": 24476, "o14": 28696, "o13": 35729, "o12": 49796, "o2c": 295250, "o25": 26556, "o24": 31135, "o23": 38766, "o22": 54029}, {"b": "MITSUBISHI", "u": "TRITON", "v": "GX", "t": "AT", "s": 1430000, "d": 130000, "dp": 286000, "la": 1144000, "cmf": 97240, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 293240, "o15": 27647, "o14": 32413, "o13": 40358, "o12": 56247, "o2c": 198050, "o25": 29997, "o24": 35168, "o23": 43788, "o22": 61028}, {"b": "MITSUBISHI", "u": "TRITON", "v": "GLX", "t": "MT", "s": 1649000, "d": 100000, "dp": 329800, "la": 1319200, "cmf": 112132, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 381932, "o15": 31881, "o14": 37377, "o13": 46538, "o12": 64861, "o2c": 271850, "o25": 34591, "o24": 40554, "o23": 50494, "o22": 70374}, {"b": "MITSUBISHI", "u": "TRITON", "v": "GLX", "t": "AT", "s": 1735000, "d": 100000, "dp": 347000, "la": 1388000, "cmf": 117980, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 404980, "o15": 33543, "o14": 39327, "o13": 48966, "o12": 68243, "o2c": 289050, "o25": 36395, "o24": 42669, "o23": 53128, "o22": 74044}, {"b": "MITSUBISHI", "u": "TRITON", "v": "GLS", "t": "AT", "s": 1959000, "d": 140000, "dp": 391800, "la": 1567200, "cmf": 133212, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 425012, "o15": 37874, "o14": 44404, "o13": 55287, "o12": 77054, "o2c": 293850, "o25": 41093, "o24": 48178, "o23": 59987, "o22": 83604}, {"b": "MITSUBISHI", "u": "TRITON", "v": "GL 4X4", "t": "MT", "s": 1292000, "d": 0, "dp": 258400, "la": 1033600, "cmf": 87856, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 386256, "o15": 24979, "o14": 29285, "o13": 36463, "o12": 50819, "o2c": 300450, "o25": 27102, "o24": 31775, "o23": 39562, "o22": 55138}, {"b": "MITSUBISHI", "u": "TRITON", "v": "ATHLETE 4X4", "t": "AT", "s": 2359000, "d": 200000, "dp": 471800, "la": 1887200, "cmf": 160412, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 482212, "o15": 45607, "o14": 53471, "o13": 66576, "o12": 92787, "o2c": 323850, "o25": 49484, "o24": 58016, "o23": 72235, "o22": 100674}, {"b": "MITSUBISHI", "u": "TRITON", "v": "ATHLETE 4X4", "t": "AT - PW", "s": 2374000, "d": 200000, "dp": 474800, "la": 1899200, "cmf": 161432, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 486232, "o15": 45897, "o14": 53811, "o13": 67000, "o12": 93377, "o2c": 326850, "o25": 49799, "o24": 58385, "o23": 72695, "o22": 101314}, {"b": "TOYOTA", "u": "VIOS", "v": "XLE", "t": "MT", "s": 868000, "d": 10000, "dp": 173600, "la": 694400, "cmf": 59024, "pf": 2050, "lto": 8400, "ins": 28000, "o1c": 259024, "o15": 16781, "o14": 19675, "o13": 24497, "o12": 34141, "o2c": 202050, "o25": 18208, "o24": 21347, "o23": 26579, "o22": 37043}, {"b": "TOYOTA", "u": "VIOS", "v": "XLE", "t": "CVT", "s": 908000, "d": 50000, "dp": 181600, "la": 726400, "cmf": 61744, "pf": 2050, "lto": 8400, "ins": 28000, "o1c": 229744, "o15": 17555, "o14": 20581, "o13": 25626, "o12": 35715, "o2c": 170050, "o25": 19047, "o24": 22331, "o23": 27804, "o22": 38750}, {"b": "TOYOTA", "u": "WIGO", "v": "G", "t": "CVT", "s": 735000, "d": 0, "dp": 147000, "la": 588000, "cmf": 49980, "pf": 2050, "lto": 8400, "ins": 25000, "o1c": 230380, "o15": 14210, "o14": 16660, "o13": 20743, "o12": 28910, "o2c": 182450, "o25": 15418, "o24": 18076, "o23": 22507, "o22": 31367}, {"b": "TOYOTA", "u": "WIGO", "v": "E", "t": "CVT", "s": 690000, "d": 0, "dp": 138000, "la": 552000, "cmf": 46920, "pf": 2050, "lto": 8400, "ins": 25000, "o1c": 218320, "o15": 13340, "o14": 15640, "o13": 19473, "o12": 27140, "o2c": 173450, "o25": 14474, "o24": 16969, "o23": 21129, "o22": 29447}, {"b": "TOYOTA", "u": "WIGO", "v": "J", "t": "MT", "s": 615000, "d": 0, "dp": 123000, "la": 492000, "cmf": 41820, "pf": 2050, "lto": 8400, "ins": 25000, "o1c": 198220, "o15": 11890, "o14": 13940, "o13": 17357, "o12": 24190, "o2c": 158450, "o25": 12901, "o24": 15125, "o23": 18832, "o22": 26246}, {"b": "TOYOTA", "u": "VELOZ", "v": "E", "t": "CVT", "s": 1104000, "d": 20000, "dp": 220800, "la": 883200, "cmf": 75072, "pf": 2050, "lto": 11425, "ins": 30000, "o1c": 317297, "o15": 21344, "o14": 25024, "o13": 31157, "o12": 43424, "o2c": 244275, "o25": 23158, "o24": 27151, "o23": 33806, "o22": 47115}, {"b": "TOYOTA", "u": "VELOZ", "v": "G", "t": "CVT", "s": 1222000, "d": 20000, "dp": 244400, "la": 977600, "cmf": 83096, "pf": 2050, "lto": 11425, "ins": 30000, "o1c": 348921, "o15": 23625, "o14": 27699, "o13": 34488, "o12": 48065, "o2c": 267875, "o25": 25633, "o24": 30053, "o23": 37419, "o22": 52151}, {"b": "TOYOTA", "u": "VELOZ", "v": "V", "t": "CVT", "s": 1262000, "d": 20000, "dp": 252400, "la": 1009600, "cmf": 85816, "pf": 2050, "lto": 11425, "ins": 30000, "o1c": 359641, "o15": 24399, "o14": 28605, "o13": 35616, "o12": 49639, "o2c": 275875, "o25": 26473, "o24": 31037, "o23": 38644, "o22": 53858}, {"b": "TOYOTA", "u": "AVANZA", "v": "J", "t": "MT", "s": 844000, "d": 10000, "dp": 168800, "la": 675200, "cmf": 57392, "pf": 2050, "lto": 11000, "ins": 30000, "o1c": 257192, "o15": 16317, "o14": 19131, "o13": 23820, "o12": 33197, "o2c": 201850, "o25": 17704, "o24": 20757, "o23": 25844, "o22": 36019}, {"b": "TOYOTA", "u": "AVANZA", "v": "E", "t": "MT", "s": 959000, "d": 20000, "dp": 191800, "la": 767200, "cmf": 65212, "pf": 2050, "lto": 11000, "ins": 30000, "o1c": 278012, "o15": 18541, "o14": 21737, "o13": 27065, "o12": 37721, "o2c": 214850, "o25": 20117, "o24": 23585, "o23": 29366, "o22": 40927}, {"b": "TOYOTA", "u": "AVANZA", "v": "E", "t": "CVT", "s": 1016000, "d": 50000, "dp": 203200, "la": 812800, "cmf": 69088, "pf": 2050, "lto": 11000, "ins": 0, "o1c": 233288, "o15": 19643, "o14": 23029, "o13": 28674, "o12": 39963, "o2c": 166250, "o25": 21312, "o24": 24987, "o23": 31111, "o22": 43359}, {"b": "TOYOTA", "u": "AVANZA", "v": "G", "t": "CVT", "s": 1071000, "d": 20000, "dp": 214200, "la": 856800, "cmf": 72828, "pf": 2050, "lto": 10000, "ins": 30000, "o1c": 307028, "o15": 20706, "o14": 24276, "o13": 30226, "o12": 42126, "o2c": 236250, "o25": 22466, "o24": 26339, "o23": 32795, "o22": 45707}, {"b": "TOYOTA", "u": "TAMARAW", "v": "FX", "t": "DSL/MT", "s": 1142000, "d": 50000, "dp": 228400, "la": 913600, "cmf": 77656, "pf": 2050, "lto": 10200, "ins": 35000, "o1c": 301256, "o15": 22079, "o14": 25885, "o13": 32230, "o12": 44919, "o2c": 225650, "o25": 23955, "o24": 28086, "o23": 34969, "o22": 48737}, {"b": "TOYOTA", "u": "TAMARAW", "v": "FX", "t": "DSL/AT", "s": 1307000, "d": 55000, "dp": 261400, "la": 1045600, "cmf": 88876, "pf": 2050, "lto": 12000, "ins": 35000, "o1c": 342276, "o15": 25269, "o14": 29625, "o13": 36886, "o12": 51409, "o2c": 255450, "o25": 27417, "o24": 32143, "o23": 40022, "o22": 55778}, {"b": "TOYOTA", "u": "INNOVA", "v": "XE 2.8", "t": "AT", "s": 1381000, "d": 35000, "dp": 276200, "la": 1104800, "cmf": 93908, "pf": 2050, "lto": 10600, "ins": 35000, "o1c": 380708, "o15": 26699, "o14": 31303, "o13": 38975, "o12": 54319, "o2c": 288850, "o25": 28969, "o24": 33963, "o23": 42288, "o22": 58936}, {"b": "TOYOTA", "u": "INNOVA", "v": "J 2.8", "t": "MT", "s": 1267000, "d": 40000, "dp": 253400, "la": 1013600, "cmf": 86156, "pf": 2050, "lto": 11000, "ins": 35000, "o1c": 345556, "o15": 24495, "o14": 28719, "o13": 35758, "o12": 49835, "o2c": 261450, "o25": 26577, "o24": 31160, "o23": 38797, "o22": 54071}, {"b": "TOYOTA", "u": "INNOVA", "v": "E", "t": "AT", "s": 1535000, "d": 20000, "dp": 307000, "la": 1228000, "cmf": 104380, "pf": 2050, "lto": 11000, "ins": 35000, "o1c": 437380, "o15": 29677, "o14": 34793, "o13": 43321, "o12": 60377, "o2c": 335050, "o25": 32199, "o24": 37751, "o23": 47003, "o22": 65509}, {"b": "TOYOTA", "u": "RAIZE", "v": "E", "t": "MT", "s": 757000, "d": 20000, "dp": 151400, "la": 605600, "cmf": 51476, "pf": 2050, "lto": 8000, "ins": 28000, "o1c": 218876, "o15": 14635, "o14": 17159, "o13": 21364, "o12": 29775, "o2c": 169450, "o25": 15879, "o24": 18617, "o23": 23180, "o22": 32306}, {"b": "TOYOTA", "u": "RAIZE", "v": "E", "t": "CVT", "s": 847000, "d": 10000, "dp": 169400, "la": 677600, "cmf": 57596, "pf": 2050, "lto": 12000, "ins": 30000, "o1c": 258996, "o15": 16375, "o14": 19199, "o13": 23904, "o12": 33315, "o2c": 203450, "o25": 17767, "o24": 20831, "o23": 25936, "o22": 36147}, {"b": "TOYOTA", "u": "RAIZE", "v": "G", "t": "CVT", "s": 942000, "d": 35000, "dp": 188400, "la": 753600, "cmf": 64056, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 247456, "o15": 18212, "o14": 21352, "o13": 26585, "o12": 37052, "o2c": 185450, "o25": 19760, "o24": 23167, "o23": 28845, "o22": 40201}, {"b": "TOYOTA", "u": "ZENIX", "v": "2.0 V CVT", "t": "AT", "s": 1676000, "d": 35000, "dp": 335200, "la": 1340800, "cmf": 113968, "pf": 2050, "lto": 14000, "ins": 40000, "o1c": 468168, "o15": 32403, "o14": 37989, "o13": 47300, "o12": 65923, "o2c": 356250, "o25": 35157, "o24": 41218, "o23": 51321, "o22": 71526}, {"b": "TOYOTA", "u": "FORTUNER", "v": "2.4 4X2 G", "t": "AT", "s": 1867000, "d": 50000, "dp": 373400, "la": 1493600, "cmf": 126956, "pf": 2050, "lto": 14400, "ins": 50000, "o1c": 514756, "o15": 36095, "o14": 42319, "o13": 52691, "o12": 73435, "o2c": 389850, "o25": 39163, "o24": 45916, "o23": 57170, "o22": 79677}, {"b": "TOYOTA", "u": "FORTUNER", "v": "2.4 4X2 V", "t": "AT", "s": 2000000, "d": 50000, "dp": 400000, "la": 1600000, "cmf": 136000, "pf": 2050, "lto": 14400, "ins": 50000, "o1c": 550400, "o15": 38667, "o14": 45333, "o13": 56444, "o12": 78667, "o2c": 416450, "o25": 41953, "o24": 49187, "o23": 61242, "o22": 85353}, {"b": "TOYOTA", "u": "FORTUNER", "v": "2.8 4X2 Q", "t": "AT", "s": 2200000, "d": 50000, "dp": 440000, "la": 1760000, "cmf": 149600, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 589600, "o15": 42533, "o14": 49867, "o13": 62089, "o12": 86533, "o2c": 442050, "o25": 46149, "o24": 54105, "o23": 67366, "o22": 93889}, {"b": "TOYOTA", "u": "HI-ACE COMMUTER", "v": "DECONTENT", "t": "MT", "s": 1396000, "d": 40000, "dp": 279200, "la": 1116800, "cmf": 94928, "pf": 2050, "lto": 11000, "ins": 40000, "o1c": 385128, "o15": 26989, "o14": 31643, "o13": 39398, "o12": 54909, "o2c": 292250, "o25": 29283, "o24": 34332, "o23": 42747, "o22": 59577}, {"b": "TOYOTA", "u": "HILUX", "v": "E DSL", "t": "MT", "s": 1582000, "d": 0, "dp": 316400, "la": 1265600, "cmf": 107576, "pf": 2050, "lto": 11000, "ins": 40000, "o1c": 474976, "o15": 30585, "o14": 35859, "o13": 44648, "o12": 62225, "o2c": 369450, "o25": 33185, "o24": 38907, "o23": 48443, "o22": 67514}, {"b": "TOYOTA", "u": "HILUX", "v": "E DSL", "t": "AT", "s": 1662000, "d": 0, "dp": 332400, "la": 1329600, "cmf": 113016, "pf": 2050, "lto": 11000, "ins": 40000, "o1c": 496416, "o15": 32132, "o14": 37672, "o13": 46905, "o12": 65372, "o2c": 385450, "o25": 34863, "o24": 40874, "o23": 50892, "o22": 70929}, {"b": "TOYOTA", "u": "", "v": "", "t": "", "s": 0, "d": 0, "dp": 0, "la": 0, "cmf": 0, "pf": 2050, "lto": 0, "ins": 0, "o1c": 0, "o15": 0, "o14": 0, "o13": 0, "o12": 0, "o2c": 2050, "o25": 0, "o24": 0, "o23": 0, "o22": 0}, {"b": "TOYOTA", "u": "HI-ACE", "v": "SUPER GRANDIA", "t": "LEATHER", "s": 2962000, "d": 0, "dp": 592400, "la": 2369600, "cmf": 201416, "pf": 2050, "lto": 15000, "ins": 50000, "o1c": 858816, "o15": 57265, "o14": 67139, "o13": 83594, "o12": 116505, "o2c": 659450, "o25": 62133, "o24": 72845, "o23": 90700, "o22": 126408}, {"b": "TOYOTA", "u": "HI-ACE", "v": "GL GRANDIA TOURER", "t": "AT", "s": 2547000, "d": 0, "dp": 509400, "la": 2037600, "cmf": 173196, "pf": 2050, "lto": 15000, "ins": 50000, "o1c": 747596, "o15": 49242, "o14": 57732, "o13": 71882, "o12": 100182, "o2c": 576450, "o25": 53428, "o24": 62639, "o23": 77992, "o22": 108697}, {"b": "TOYOTA", "u": "", "v": "", "t": "", "s": 0, "d": 0, "dp": 0, "la": 0, "cmf": 0, "pf": 2050, "lto": 0, "ins": 0, "o1c": 0, "o15": 0, "o14": 0, "o13": 0, "o12": 0, "o2c": 2050, "o25": 0, "o24": 0, "o23": 0, "o22": 0}, {"b": "KIA", "u": "SONET", "v": "SX", "t": "AT", "s": 1178000, "d": 90000, "dp": 235600, "la": 942400, "cmf": 80104, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 250704, "o15": 22775, "o14": 26701, "o13": 33246, "o12": 46335, "o2c": 172650, "o25": 24711, "o24": 28971, "o23": 36072, "o22": 50273}, {"b": "KIA", "u": "SONET", "v": "LX", "t": "MT", "s": 778000, "d": 40000, "dp": 155600, "la": 622400, "cmf": 52904, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 193504, "o15": 15041, "o14": 17635, "o13": 21957, "o12": 30601, "o2c": 142650, "o25": 16320, "o24": 19134, "o23": 23823, "o22": 33202}, {"b": "KIA", "u": "K2500", "v": "4X4", "t": "VAN/MT", "s": 1330000, "d": 80000, "dp": 266000, "la": 1064000, "cmf": 90440, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 311440, "o15": 25713, "o14": 30147, "o13": 37536, "o12": 52313, "o2c": 223050, "o25": 27899, "o24": 32709, "o23": 40726, "o22": 56760}, {"b": "KIA", "u": "", "v": "", "t": "", "s": 0, "d": 0, "dp": 0, "la": 0, "cmf": 0, "pf": 2050, "lto": 0, "ins": 0, "o1c": 0, "o15": 0, "o14": 0, "o13": 0, "o12": 0, "o2c": 2050, "o25": 0, "o24": 0, "o23": 0, "o22": 0}, {"b": "KIA", "u": "", "v": "", "t": "", "s": 0, "d": 0, "dp": 0, "la": 0, "cmf": 0, "pf": 2050, "lto": 0, "ins": 0, "o1c": 0, "o15": 0, "o14": 0, "o13": 0, "o12": 0, "o2c": 2050, "o25": 0, "o24": 0, "o23": 0, "o22": 0}, {"b": "SUZUKI", "u": "SPRESSO", "v": "GL AGS", "t": "AT", "s": 674000, "d": 60000, "dp": 134800, "la": 539200, "cmf": 45832, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 148632, "o15": 13031, "o14": 15277, "o13": 19022, "o12": 26511, "o2c": 104850, "o25": 14138, "o24": 16576, "o23": 20639, "o22": 28764}, {"b": "SUZUKI", "u": "CARRY", "v": "UTILITY VAN", "t": "", "s": 754000, "d": 40000, "dp": 150800, "la": 603200, "cmf": 51272, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 192072, "o15": 14577, "o14": 17091, "o13": 21280, "o12": 29657, "o2c": 142850, "o25": 15816, "o24": 18543, "o23": 23088, "o22": 32178}, {"b": "SUZUKI", "u": "APV", "v": "MINI VAN", "t": "MT", "s": 743000, "d": 40000, "dp": 148600, "la": 594400, "cmf": 50524, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 189124, "o15": 14365, "o14": 16841, "o13": 20969, "o12": 29225, "o2c": 140650, "o25": 15586, "o24": 18273, "o23": 22751, "o22": 31709}, {"b": "HYUNDAI", "u": "VENUE", "v": "1.5GL", "t": "MT", "s": 78900, "d": 0, "dp": 15780, "la": 63120, "cmf": 5365, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 46145, "o15": 1525, "o14": 1788, "o13": 2227, "o12": 3103, "o2c": 42830, "o25": 1655, "o24": 1940, "o23": 2416, "o22": 3367}, {"b": "HYUNDAI", "u": "VENUE", "v": "1.5GL", "t": "AT", "s": 898000, "d": 0, "dp": 179600, "la": 718400, "cmf": 61064, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 268664, "o15": 17361, "o14": 20355, "o13": 25344, "o12": 35321, "o2c": 209650, "o25": 18837, "o24": 22085, "o23": 27498, "o22": 38324}, {"b": "HYUNDAI", "u": "VENUE", "v": "1.5GLS", "t": "AT", "s": 998000, "d": 0, "dp": 199600, "la": 798400, "cmf": 67864, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 295464, "o15": 19295, "o14": 22621, "o13": 28166, "o12": 39255, "o2c": 229650, "o25": 20935, "o24": 24544, "o23": 30560, "o22": 42591}, {"b": "HYUNDAI", "u": "ELANTRA", "v": "1.5GL", "t": "AT STD", "s": 1120000, "d": 0, "dp": 224000, "la": 896000, "cmf": 76160, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 330160, "o15": 21653, "o14": 25387, "o13": 31609, "o12": 44053, "o2c": 256050, "o25": 23494, "o24": 27545, "o23": 34296, "o22": 47798}, {"b": "HYUNDAI", "u": "ELANTRA", "v": "1.5GL", "t": "AT", "s": 1195000, "d": 0, "dp": 239000, "la": 956000, "cmf": 81260, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 350260, "o15": 23103, "o14": 27087, "o13": 33726, "o12": 47003, "o2c": 271050, "o25": 25067, "o24": 29389, "o23": 36592, "o22": 50999}, {"b": "HYUNDAI", "u": "ELANTRA", "v": "1.5 HEV GLS", "t": "DCT", "s": 1480000, "d": 0, "dp": 296000, "la": 1184000, "cmf": 100640, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 431640, "o15": 28613, "o14": 33547, "o13": 41769, "o12": 58213, "o2c": 333050, "o25": 31045, "o24": 36398, "o23": 45319, "o22": 63161}, {"b": "HYUNDAI", "u": "ELANTRA", "v": "1.5 HEV PREMIUM", "t": "DCT", "s": 1680000, "d": 0, "dp": 336000, "la": 1344000, "cmf": 114240, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 490240, "o15": 32480, "o14": 38080, "o13": 47413, "o12": 66080, "o2c": 378050, "o25": 35241, "o24": 41317, "o23": 51443, "o22": 71697}, {"b": "HYUNDAI", "u": "ELANTRA", "v": "1.5T N LINE", "t": "DCT", "s": 1795000, "d": 0, "dp": 359000, "la": 1436000, "cmf": 122060, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 521060, "o15": 34703, "o14": 40687, "o13": 50659, "o12": 70603, "o2c": 401050, "o25": 37653, "o24": 44145, "o23": 54965, "o22": 76605}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5GL", "t": "IVT", "s": 1134000, "d": 0, "dp": 226800, "la": 907200, "cmf": 77112, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 333912, "o15": 21924, "o14": 25704, "o13": 32004, "o12": 44604, "o2c": 258850, "o25": 23788, "o24": 27889, "o23": 34724, "o22": 48395}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5GLS", "t": "IVT", "s": 1254000, "d": 0, "dp": 250800, "la": 1003200, "cmf": 85272, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 366072, "o15": 24244, "o14": 28424, "o13": 35391, "o12": 49324, "o2c": 282850, "o25": 26305, "o24": 30840, "o23": 38399, "o22": 53517}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5 PREMIUM", "t": "IVT", "s": 1333000, "d": 0, "dp": 266600, "la": 1066400, "cmf": 90644, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 392244, "o15": 25771, "o14": 30215, "o13": 37620, "o12": 52431, "o2c": 303650, "o25": 27962, "o24": 32783, "o23": 40818, "o22": 56888}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5T N LINE", "t": "DCT", "s": 1379000, "d": 0, "dp": 275800, "la": 1103200, "cmf": 93772, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 404572, "o15": 26661, "o14": 31257, "o13": 38918, "o12": 54241, "o2c": 312850, "o25": 28927, "o24": 33914, "o23": 42227, "o22": 58851}, {"b": "HYUNDAI", "u": "KONA", "v": "1.5 HEV GLS", "t": "DCT", "s": 1528000, "d": 0, "dp": 305600, "la": 1222400, "cmf": 103904, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 449504, "o15": 29541, "o14": 34635, "o13": 43124, "o12": 60101, "o2c": 347650, "o25": 32052, "o24": 37579, "o23": 46789, "o22": 65210}, {"b": "HYUNDAI", "u": "KONA", "v": "1.5 HEV PREMIUM", "t": "DCT", "s": 1688000, "d": 0, "dp": 337600, "la": 1350400, "cmf": 114784, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 492384, "o15": 32635, "o14": 38261, "o13": 47639, "o12": 66395, "o2c": 379650, "o25": 35409, "o24": 41514, "o23": 51688, "o22": 72038}, {"b": "HYUNDAI", "u": "TUCSON", "v": "2.0 GLS", "t": "AT", "s": 1680000, "d": 0, "dp": 336000, "la": 1344000, "cmf": 114240, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 490240, "o15": 32480, "o14": 38080, "o13": 47413, "o12": 66080, "o2c": 378050, "o25": 35241, "o24": 41317, "o23": 51443, "o22": 71697}, {"b": "HYUNDAI", "u": "TUCSON", "v": "2.0 GLS+", "t": "AT", "s": 1980000, "d": 0, "dp": 396000, "la": 1584000, "cmf": 134640, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 570640, "o15": 38280, "o14": 44880, "o13": 55880, "o12": 77880, "o2c": 438050, "o25": 41534, "o24": 48695, "o23": 60630, "o22": 84500}, {"b": "HYUNDAI", "u": "TUCSON", "v": "1.5T HEV GLS", "t": "AT", "s": 2290000, "d": 0, "dp": 458000, "la": 1832000, "cmf": 155720, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 663720, "o15": 44273, "o14": 51907, "o13": 64629, "o12": 90073, "o2c": 510050, "o25": 48037, "o24": 56319, "o23": 70122, "o22": 97730}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5 GL", "t": "AT 2WD", "s": 1998000, "d": 0, "dp": 399600, "la": 1598400, "cmf": 135864, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 575464, "o15": 38628, "o14": 45288, "o13": 56388, "o12": 78588, "o2c": 441650, "o25": 41911, "o24": 49137, "o23": 61181, "o22": 85268}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5 GLS", "t": "AT 2WD", "s": 2410000, "d": 0, "dp": 482000, "la": 1928000, "cmf": 163880, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 695880, "o15": 46593, "o14": 54627, "o13": 68016, "o12": 94793, "o2c": 534050, "o25": 50554, "o24": 59270, "o23": 73797, "o22": 102851}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5 GLS", "t": "AWD", "s": 2540000, "d": 0, "dp": 508000, "la": 2032000, "cmf": 172720, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 760720, "o15": 49107, "o14": 57573, "o13": 71684, "o12": 99907, "o2c": 590050, "o25": 53281, "o24": 62467, "o23": 77778, "o22": 108399}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5L TURBO CALLIGRAPHY", "t": "AWD&DCT", "s": 3100000, "d": 0, "dp": 620000, "la": 2480000, "cmf": 210800, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 910800, "o15": 59933, "o14": 70267, "o13": 87489, "o12": 121933, "o2c": 702050, "o25": 65028, "o24": 76239, "o23": 94925, "o22": 132298}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "1.5T HEV CALLIGRAPHY", "t": "AT AWD", "s": 3330000, "d": 0, "dp": 666000, "la": 2664000, "cmf": 226440, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 972440, "o15": 64380, "o14": 75480, "o13": 93980, "o12": 130980, "o2c": 748050, "o25": 69852, "o24": 81896, "o23": 101968, "o22": 142113}, {"b": "HYUNDAI", "u": "PALISADE", "v": "2.5T HEV CALLIGRAPHY", "t": "AT AWD", "s": 3980000, "d": 0, "dp": 796000, "la": 3184000, "cmf": 270640, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1146640, "o15": 76947, "o14": 90213, "o13": 112324, "o12": 156547, "o2c": 878050, "o25": 83487, "o24": 97881, "o23": 121872, "o22": 169853}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GL PLUS", "t": "IVT", "s": 1118000, "d": 0, "dp": 223600, "la": 894400, "cmf": 76024, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 329624, "o15": 21615, "o14": 25341, "o13": 31552, "o12": 43975, "o2c": 255650, "o25": 23452, "o24": 27495, "o23": 34234, "o22": 47713}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS", "t": "IVT", "s": 1228000, "d": 0, "dp": 245600, "la": 982400, "cmf": 83504, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 359104, "o15": 23741, "o14": 27835, "o13": 34657, "o12": 48301, "o2c": 277650, "o25": 25759, "o24": 30201, "o23": 37603, "o22": 52407}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "X 1.5 GLS PLUS", "t": "IVT", "s": 1318000, "d": 0, "dp": 263600, "la": 1054400, "cmf": 89624, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 388224, "o15": 25481, "o14": 29875, "o13": 37197, "o12": 51841, "o2c": 300650, "o25": 27647, "o24": 32414, "o23": 40359, "o22": 56248}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "X 1.5 PREMIUM", "t": "IVT", "s": 1378000, "d": 0, "dp": 275600, "la": 1102400, "cmf": 93704, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 404304, "o15": 26641, "o14": 31235, "o13": 38890, "o12": 54201, "o2c": 312650, "o25": 28906, "o24": 33890, "o23": 42196, "o22": 58808}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GL", "t": "IVT", "s": 1068000, "d": 0, "dp": 213600, "la": 854400, "cmf": 72624, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 316224, "o15": 20648, "o14": 24208, "o13": 30141, "o12": 42008, "o2c": 245650, "o25": 22403, "o24": 26266, "o23": 32703, "o22": 45579}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS", "t": "IVT", "s": 1198000, "d": 0, "dp": 239600, "la": 958400, "cmf": 81464, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351064, "o15": 23161, "o14": 27155, "o13": 33810, "o12": 47121, "o2c": 271650, "o25": 25130, "o24": 29463, "o23": 36684, "o22": 51127}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS PREMIUM", "t": "IVT", "s": 1288000, "d": 0, "dp": 257600, "la": 1030400, "cmf": 87584, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 375184, "o15": 24901, "o14": 29195, "o13": 36350, "o12": 50661, "o2c": 289650, "o25": 27018, "o24": 31676, "o23": 39440, "o22": 54968}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS PREMIUM", "t": "X IVT", "s": 1348000, "d": 0, "dp": 269600, "la": 1078400, "cmf": 91664, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 396264, "o15": 26061, "o14": 30555, "o13": 38044, "o12": 53021, "o2c": 306650, "o25": 28277, "o24": 33152, "o23": 41277, "o22": 57528}, {"b": "HYUNDAI", "u": "CUSTIN", "v": "1.5T GLS", "t": "AT", "s": 1770000, "d": 0, "dp": 354000, "la": 1416000, "cmf": 120360, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 514360, "o15": 34220, "o14": 40120, "o13": 49953, "o12": 69620, "o2c": 396050, "o25": 37129, "o24": 43530, "o23": 54199, "o22": 75538}, {"b": "HYUNDAI", "u": "CUSTIN", "v": "1.5T PREMIUM", "t": "AT", "s": 2080000, "d": 0, "dp": 416000, "la": 1664000, "cmf": 141440, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 607440, "o15": 40213, "o14": 47147, "o13": 58702, "o12": 81813, "o2c": 468050, "o25": 43631, "o24": 51154, "o23": 63692, "o22": 88767}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI GLS", "t": "AT 11s", "s": 2320000, "d": 0, "dp": 464000, "la": 1856000, "cmf": 157760, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 671760, "o15": 44853, "o14": 52587, "o13": 65476, "o12": 91253, "o2c": 516050, "o25": 48666, "o24": 57057, "o23": 71041, "o22": 99010}, {"b": "HYUNDAI", "u": "STARIA", "v": "CRDI PREMIUM", "t": "AT AWD 9s", "s": 2830000, "d": 0, "dp": 566000, "la": 2264000, "cmf": 192440, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 838440, "o15": 54713, "o14": 64147, "o13": 79869, "o12": 111313, "o2c": 648050, "o25": 59364, "o24": 69599, "o23": 86658, "o22": 120775}, {"b": "HYUNDAI", "u": "STARIA", "v": "CRDI PREMIUM", "t": "AT AWD 7s", "s": 3030000, "d": 0, "dp": 606000, "la": 2424000, "cmf": 206040, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 892040, "o15": 58580, "o14": 68680, "o13": 85513, "o12": 119180, "o2c": 688050, "o25": 63559, "o24": 74518, "o23": 92782, "o22": 129310}, {"b": "HYUNDAI", "u": "H100", "v": "2.5 CRDI", "t": "MT C&C W/AC", "s": 1136000, "d": 0, "dp": 227200, "la": 908800, "cmf": 77248, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 334448, "o15": 21963, "o14": 25749, "o13": 32060, "o12": 44683, "o2c": 259250, "o25": 23829, "o24": 27938, "o23": 34786, "o22": 48481}, {"b": "HYUNDAI", "u": "H100", "v": "2.5 CRDI", "t": "MT W/AC", "s": 1398000, "d": 0, "dp": 279600, "la": 1118400, "cmf": 95064, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 409664, "o15": 27028, "o14": 31688, "o13": 39455, "o12": 54988, "o2c": 316650, "o25": 29325, "o24": 34381, "o23": 42808, "o22": 59662}, {"b": "SUZUKI", "u": "HATCHBACK", "v": "S-PRESSO 1.0L GL", "t": "MT", "s": 634000, "d": 0, "dp": 126800, "la": 507200, "cmf": 43112, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 194912, "o15": 12257, "o14": 14371, "o13": 17893, "o12": 24937, "o2c": 153850, "o25": 13299, "o24": 15592, "o23": 19414, "o22": 27057}, {"b": "SUZUKI", "u": "HATCHBACK", "v": "S-PRESSO 1.0L GL", "t": "AGS", "s": 674000, "d": 0, "dp": 134800, "la": 539200, "cmf": 45832, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 205632, "o15": 13031, "o14": 15277, "o13": 19022, "o12": 26511, "o2c": 161850, "o25": 14138, "o24": 16576, "o23": 20639, "o22": 28764}, {"b": "SUZUKI", "u": "DZIRE HYBRID", "v": "1.2L GL", "t": "CVT", "s": 920000, "d": 0, "dp": 184000, "la": 736000, "cmf": 62560, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 274560, "o15": 17787, "o14": 20853, "o13": 25964, "o12": 36187, "o2c": 214050, "o25": 19299, "o24": 22626, "o23": 28171, "o22": 39263}, {"b": "SUZUKI", "u": "SEDAN HYBRID", "v": "1.2L GLX", "t": "CVT", "s": 998000, "d": 0, "dp": 199600, "la": 798400, "cmf": 67864, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 295464, "o15": 19295, "o14": 22621, "o13": 28166, "o12": 39255, "o2c": 229650, "o25": 20935, "o24": 24544, "o23": 30560, "o22": 42591}, {"b": "SUZUKI", "u": "CROSSOVER SUV HYBRID", "v": "FRONX 1.5L GL", "t": "AT", "s": 1059000, "d": 0, "dp": 211800, "la": 847200, "cmf": 72012, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 313812, "o15": 20474, "o14": 24004, "o13": 29887, "o12": 41654, "o2c": 243850, "o25": 22214, "o24": 26044, "o23": 32428, "o22": 45195}, {"b": "SUZUKI", "u": "CROSSOVER SUV HYBRID", "v": "FRONX 1.5L GLX", "t": "AT MONOTONE", "s": 1219000, "d": 0, "dp": 243800, "la": 975200, "cmf": 82892, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 356692, "o15": 23567, "o14": 27631, "o13": 34403, "o12": 47947, "o2c": 275850, "o25": 25571, "o24": 29979, "o23": 37327, "o22": 52023}, {"b": "SUZUKI", "u": "CROSSOVER SUV HYBRID", "v": "FRONX 1.5L GLX", "t": "AT TWO-TONE", "s": 1229000, "d": 0, "dp": 245800, "la": 983200, "cmf": 83572, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 359372, "o15": 23761, "o14": 27857, "o13": 34685, "o12": 48341, "o2c": 277850, "o25": 25780, "o24": 30225, "o23": 37633, "o22": 52450}, {"b": "SUZUKI", "u": "CROSSOVER SUV HYBRID", "v": "FRONX 1.5L SGX", "t": "AT TWO-TONE", "s": 1299000, "d": 0, "dp": 259800, "la": 1039200, "cmf": 88332, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 378132, "o15": 25114, "o14": 29444, "o13": 36661, "o12": 51094, "o2c": 291850, "o25": 27249, "o24": 31947, "o23": 39777, "o22": 55437}, {"b": "SUZUKI", "u": "MPV 7 SEATER HYBRID", "v": "ERTIGA 1.5L GLX", "t": "AT", "s": 1178000, "d": 0, "dp": 235600, "la": 942400, "cmf": 80104, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 345704, "o15": 22775, "o14": 26701, "o13": 33246, "o12": 46335, "o2c": 267650, "o25": 24711, "o24": 28971, "o23": 36072, "o22": 50273}, {"b": "SUZUKI", "u": "MPV 7 SEATER HYBRID", "v": "XL7 1.5L GLX", "t": "AT MONOTONE", "s": 1259000, "d": 0, "dp": 251800, "la": 1007200, "cmf": 85612, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 367412, "o15": 24341, "o14": 28537, "o13": 35532, "o12": 49521, "o2c": 283850, "o25": 26410, "o24": 30963, "o23": 38552, "o22": 53730}, {"b": "SUZUKI", "u": "MPV 7 SEATER HYBRID", "v": "XL7 1.5L GLX", "t": "AT TWO-TONE", "s": 1269000, "d": 0, "dp": 253800, "la": 1015200, "cmf": 86292, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 370092, "o15": 24534, "o14": 28764, "o13": 35814, "o12": 49914, "o2c": 285850, "o25": 26619, "o24": 31209, "o23": 38858, "o22": 54157}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 3-DOOR 1.5 GL", "t": "MT", "s": 1258000, "d": 0, "dp": 251600, "la": 1006400, "cmf": 85544, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 367144, "o15": 24321, "o14": 28515, "o13": 35504, "o12": 49481, "o2c": 283650, "o25": 26389, "o24": 30938, "o23": 38521, "o22": 53687}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 3-DOOR 1.5 GLX", "t": "AT MONOTONE", "s": 1320000, "d": 0, "dp": 264000, "la": 1056000, "cmf": 89760, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 388760, "o15": 25520, "o14": 29920, "o13": 37253, "o12": 51920, "o2c": 301050, "o25": 27689, "o24": 32463, "o23": 40420, "o22": 56333}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 3-DOOR 1.5 GLX", "t": "AT TWO-TONE", "s": 1330000, "d": 0, "dp": 266000, "la": 1064000, "cmf": 90440, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 391440, "o15": 25713, "o14": 30147, "o13": 37536, "o12": 52313, "o2c": 303050, "o25": 27899, "o24": 32709, "o23": 40726, "o22": 56760}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 3-DOOR 1.5 GLX", "t": "AT RHINO-EDITION", "s": 1331000, "d": 0, "dp": 266200, "la": 1064800, "cmf": 90508, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 391708, "o15": 25733, "o14": 30169, "o13": 37564, "o12": 52353, "o2c": 303250, "o25": 27920, "o24": 32734, "o23": 40757, "o22": 56803}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 5-DOOR GL", "t": "MT", "s": 1558000, "d": 0, "dp": 311600, "la": 1246400, "cmf": 105944, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 457544, "o15": 30121, "o14": 35315, "o13": 43970, "o12": 61281, "o2c": 353650, "o25": 32682, "o24": 38316, "o23": 47708, "o22": 66490}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 5-DOOR GLX", "t": "AT MONOTONE", "s": 1698000, "d": 0, "dp": 339600, "la": 1358400, "cmf": 115464, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 495064, "o15": 32828, "o14": 38488, "o13": 47921, "o12": 66788, "o2c": 381650, "o25": 35618, "o24": 41759, "o23": 51995, "o22": 72465}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 5-DOOR GLX", "t": "AT TWO-TONE", "s": 1708000, "d": 0, "dp": 341600, "la": 1366400, "cmf": 116144, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 497744, "o15": 33021, "o14": 38715, "o13": 48204, "o12": 67181, "o2c": 383650, "o25": 35828, "o24": 42005, "o23": 52301, "o22": 72892}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 5-DOOR GLX", "t": "AT RHINO-EDITION", "s": 1739000, "d": 0, "dp": 347800, "la": 1391200, "cmf": 118252, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 506052, "o15": 33621, "o14": 39417, "o13": 49078, "o12": 68401, "o2c": 389850, "o25": 36478, "o24": 42768, "o23": 53250, "o22": 74215}, {"b": "SUZUKI", "u": "ALL GRIP 4X4", "v": "JIMNY 5-DOOR GLX", "t": "AT RHINO-EDITION (TWO-TONE)", "s": 1749000, "d": 0, "dp": 349800, "la": 1399200, "cmf": 118932, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 508732, "o15": 33814, "o14": 39644, "o13": 49361, "o12": 68794, "o2c": 391850, "o25": 36688, "o24": 43014, "o23": 53556, "o22": 74641}, {"b": "SUZUKI", "u": "APV 8 SEATER", "v": "APV 1.6L GA", "t": "MT", "s": 743000, "d": 0, "dp": 148600, "la": 594400, "cmf": 50524, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 224124, "o15": 14365, "o14": 16841, "o13": 20969, "o12": 29225, "o2c": 175650, "o25": 15586, "o24": 18273, "o23": 22751, "o22": 31709}, {"b": "SUZUKI", "u": "APV 8 SEATER", "v": "APV 1.6L GLX", "t": "MT", "s": 955000, "d": 0, "dp": 191000, "la": 764000, "cmf": 64940, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 283940, "o15": 18463, "o14": 21647, "o13": 26952, "o12": 37563, "o2c": 221050, "o25": 20033, "o24": 23487, "o23": 29243, "o22": 40756}, {"b": "SUZUKI", "u": "UTILITY VEHICLES", "v": "CARRY 1.5L", "t": "MT CAB", "s": 614000, "d": 0, "dp": 122800, "la": 491200, "cmf": 41752, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 189552, "o15": 11871, "o14": 13917, "o13": 17328, "o12": 24151, "o2c": 149850, "o25": 12880, "o24": 15100, "o23": 18801, "o22": 26203}, {"b": "SUZUKI", "u": "UTILITY VEHICLES", "v": "CARRY 1.5L", "t": "MT U.V", "s": 739000, "d": 0, "dp": 147800, "la": 591200, "cmf": 50252, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 223052, "o15": 14287, "o14": 16751, "o13": 20856, "o12": 29067, "o2c": 174850, "o25": 15502, "o24": 18174, "o23": 22629, "o22": 31538}, {"b": "SUZUKI", "u": "UTILITY VEHICLES", "v": "CARRY 1.5L", "t": "MT C.V", "s": 699000, "d": 0, "dp": 139800, "la": 559200, "cmf": 47532, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 212332, "o15": 13514, "o14": 15844, "o13": 19727, "o12": 27494, "o2c": 166850, "o25": 14663, "o24": 17191, "o23": 21404, "o22": 29831}, {"b": "SUZUKI", "u": "UTILITY VEHICLES", "v": "CARRY 1.5L", "t": "MT L.V", "s": 798000, "d": 0, "dp": 159600, "la": 638400, "cmf": 54264, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 238864, "o15": 15428, "o14": 18088, "o13": 22521, "o12": 31388, "o2c": 186650, "o25": 16739, "o24": 19625, "o23": 24436, "o22": 34056}, {"b": "SUZUKI", "u": "UTILITY VEHICLES", "v": "CARRY 1.5L", "t": "MT TRUCK", "s": 642000, "d": 0, "dp": 128400, "la": 513600, "cmf": 43656, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 197056, "o15": 12412, "o14": 14552, "o13": 18119, "o12": 25252, "o2c": 155450, "o25": 13467, "o24": 15789, "o23": 19659, "o22": 27398}, {"b": "ISUZU", "u": "TRAVIZ", "v": "L", "t": "MT", "s": 1365000, "d": 115000, "dp": 273000, "la": 1092000, "cmf": 92820, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 290820, "o15": 26390, "o14": 30940, "o13": 38523, "o12": 53690, "o2c": 200050, "o25": 28633, "o24": 33570, "o23": 41798, "o22": 58254}, {"b": "ISUZU", "u": "TRAVIZ", "v": "S", "t": "MT", "s": 1365000, "d": 100000, "dp": 273000, "la": 1092000, "cmf": 92820, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 305820, "o15": 26390, "o14": 30940, "o13": 38523, "o12": 53690, "o2c": 215050, "o25": 28633, "o24": 33570, "o23": 41798, "o22": 58254}, {"b": "GACMOTOR", "u": "EMPOW", "v": "1.5 GS", "t": "DCT", "s": 1225000, "d": 0, "dp": 245000, "la": 980000, "cmf": 83300, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 358300, "o15": 23683, "o14": 27767, "o13": 34572, "o12": 48183, "o2c": 277050, "o25": 25696, "o24": 30127, "o23": 37511, "o22": 52279}, {"b": "GACMOTOR", "u": "EMPOW", "v": "1.5 GB", "t": "DCT", "s": 1305000, "d": 0, "dp": 261000, "la": 1044000, "cmf": 88740, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 384740, "o15": 25230, "o14": 29580, "o13": 36830, "o12": 51330, "o2c": 298050, "o25": 27375, "o24": 32094, "o23": 39961, "o22": 55693}, {"b": "GACMOTOR", "u": "EMPOW", "v": "1.5 GE", "t": "DCT", "s": 1348000, "d": 0, "dp": 269600, "la": 1078400, "cmf": 91664, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 396264, "o15": 26061, "o14": 30555, "o13": 38044, "o12": 53021, "o2c": 306650, "o25": 28277, "o24": 33152, "o23": 41277, "o22": 57528}, {"b": "GACMOTOR", "u": "GS3 EMZOOM", "v": "1.5 GS", "t": "7WDCT", "s": 1089000, "d": 0, "dp": 217800, "la": 871200, "cmf": 74052, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 321852, "o15": 21054, "o14": 24684, "o13": 30734, "o12": 42834, "o2c": 249850, "o25": 22844, "o24": 26782, "o23": 33346, "o22": 46475}, {"b": "GACMOTOR", "u": "GS3 EMZOOM", "v": "1.5 GB", "t": "TOURING", "s": 1189000, "d": 0, "dp": 237800, "la": 951200, "cmf": 80852, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 348652, "o15": 22987, "o14": 26951, "o13": 33556, "o12": 46767, "o2c": 269850, "o25": 24941, "o24": 29241, "o23": 36409, "o22": 50743}, {"b": "GACMOTOR", "u": "GS3 EMZOOM", "v": "1.5 GL", "t": "R STYLE", "s": 1289000, "d": 0, "dp": 257800, "la": 1031200, "cmf": 87652, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 375452, "o15": 24921, "o14": 29217, "o13": 36378, "o12": 50701, "o2c": 289850, "o25": 27039, "o24": 31701, "o23": 39471, "o22": 55010}, {"b": "GACMOTOR", "u": "EMKOO", "v": "1.5 GE", "t": "DCT", "s": 1298000, "d": 0, "dp": 259600, "la": 1038400, "cmf": 88264, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 377864, "o15": 25095, "o14": 29421, "o13": 36632, "o12": 51055, "o2c": 291650, "o25": 27228, "o24": 31922, "o23": 39746, "o22": 55394}, {"b": "GACMOTOR", "u": "EMKOO", "v": "1.5 GL", "t": "DCT", "s": 1498000, "d": 0, "dp": 299600, "la": 1198400, "cmf": 101864, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 436464, "o15": 28961, "o14": 33955, "o13": 42277, "o12": 58921, "o2c": 336650, "o25": 31423, "o24": 36841, "o23": 45870, "o22": 63930}, {"b": "GACMOTOR", "u": "EMKOO", "v": "2.0 HYBRID", "t": "HYBRID", "s": 1698000, "d": 0, "dp": 339600, "la": 1358400, "cmf": 115464, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 495064, "o15": 32828, "o14": 38488, "o13": 47921, "o12": 66788, "o2c": 381650, "o25": 35618, "o24": 41759, "o23": 51995, "o22": 72465}, {"b": "GACMOTOR", "u": "M6 PRO", "v": "1.5 GS", "t": "", "s": 1278000, "d": 0, "dp": 255600, "la": 1022400, "cmf": 86904, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 372504, "o15": 24708, "o14": 28968, "o13": 36068, "o12": 50268, "o2c": 287650, "o25": 26808, "o24": 31430, "o23": 39134, "o22": 54541}, {"b": "GACMOTOR", "u": "M6 PRO", "v": "1.5 GL", "t": "", "s": 1598000, "d": 0, "dp": 319600, "la": 1278400, "cmf": 108664, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 468264, "o15": 30895, "o14": 36221, "o13": 45099, "o12": 62855, "o2c": 361650, "o25": 33521, "o24": 39300, "o23": 48933, "o22": 68197}, {"b": "GACMOTOR", "u": "M8", "v": "2.0T GDI GL", "t": "MASTER", "s": 2998000, "d": 0, "dp": 599600, "la": 2398400, "cmf": 203864, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 883464, "o15": 57961, "o14": 67955, "o13": 84610, "o12": 117921, "o2c": 681650, "o25": 62888, "o24": 73731, "o23": 91802, "o22": 127945}, {"b": "GACMOTOR", "u": "M8", "v": "2.0T GDI GX", "t": "MASTER", "s": 3948000, "d": 0, "dp": 789600, "la": 3158400, "cmf": 268464, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1138064, "o15": 76328, "o14": 89488, "o13": 111421, "o12": 155288, "o2c": 871650, "o25": 82816, "o24": 97094, "o23": 120892, "o22": 168487}, {"b": "GACMOTOR", "u": "GS8", "v": "2.0 GT 4X2", "t": "", "s": 2398000, "d": 0, "dp": 479600, "la": 1918400, "cmf": 163064, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 692664, "o15": 46361, "o14": 54355, "o13": 67677, "o12": 94321, "o2c": 531650, "o25": 50302, "o24": 58975, "o23": 73429, "o22": 102339}, {"b": "BAIC", "u": "B30", "v": "DUNE 4X2", "t": "", "s": 1588000, "d": 0, "dp": 317600, "la": 1270400, "cmf": 107984, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 465584, "o15": 30701, "o14": 35995, "o13": 44817, "o12": 62461, "o2c": 359650, "o25": 33311, "o24": 39054, "o23": 48626, "o22": 67771}, {"b": "BAIC", "u": "B30", "v": "DUNE 4X4", "t": "", "s": 1888000, "d": 0, "dp": 377600, "la": 1510400, "cmf": 128384, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 545984, "o15": 36501, "o14": 42795, "o13": 53284, "o12": 74261, "o2c": 419650, "o25": 39604, "o24": 46432, "o23": 57813, "o22": 80574}, {"b": "BAIC", "u": "X55", "v": "VERVE SPORT", "t": "", "s": 1048888, "d": 0, "dp": 209778, "la": 839110, "cmf": 71324, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 311102, "o15": 20279, "o14": 23775, "o13": 29602, "o12": 41256, "o2c": 241828, "o25": 22002, "o24": 25796, "o23": 32118, "o22": 44763}, {"b": "BAIC", "u": "B40", "v": "RAGNAR", "t": "", "s": 2368888, "d": 0, "dp": 473778, "la": 1895110, "cmf": 161084, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 684862, "o15": 45799, "o14": 53695, "o13": 66855, "o12": 93176, "o2c": 525828, "o25": 49691, "o24": 58259, "o23": 72538, "o22": 101096}, {"b": "BAIC", "u": "B40", "v": "PRO TRAIL MASTER", "t": "", "s": 2498000, "d": 0, "dp": 499600, "la": 1998400, "cmf": 169864, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 719464, "o15": 48295, "o14": 56621, "o13": 70499, "o12": 98255, "o2c": 551650, "o25": 52400, "o24": 61434, "o23": 76492, "o22": 106606}, {"b": "BAIC", "u": "B60", "v": "BEAUMOUNT REV", "t": "", "s": 3398000, "d": 0, "dp": 679600, "la": 2718400, "cmf": 231064, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 990664, "o15": 65695, "o14": 77021, "o13": 95899, "o12": 133655, "o2c": 761650, "o25": 71279, "o24": 83568, "o23": 104051, "o22": 145015}, {"b": "BAIC", "u": "B60", "v": "BEAUMONT (LIGHT BLUE)", "t": "", "s": 2998000, "d": 0, "dp": 599600, "la": 2398400, "cmf": 203864, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 883464, "o15": 57961, "o14": 67955, "o13": 84610, "o12": 117921, "o2c": 681650, "o25": 62888, "o24": 73731, "o23": 91802, "o22": 127945}, {"b": "BAIC", "u": "B80", "v": "WAGON", "t": "", "s": 4398000, "d": 0, "dp": 879600, "la": 3518400, "cmf": 299064, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1258664, "o15": 85028, "o14": 99688, "o13": 124121, "o12": 172988, "o2c": 961650, "o25": 92255, "o24": 108161, "o23": 134672, "o22": 187692}, {"b": "VINFAST", "u": "VF3", "v": "", "t": "", "s": 745000, "d": 0, "dp": 149000, "la": 596000, "cmf": 50660, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 227660, "o15": 14403, "o14": 16887, "o13": 21026, "o12": 29303, "o2c": 179050, "o25": 15628, "o24": 18322, "o23": 22813, "o22": 31794}, {"b": "VINFAST", "u": "VF5", "v": "PLUS", "t": "", "s": 1050000, "d": 0, "dp": 210000, "la": 840000, "cmf": 71400, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 311400, "o15": 20300, "o14": 23800, "o13": 29633, "o12": 41300, "o2c": 242050, "o25": 22026, "o24": 25823, "o23": 32152, "o22": 44810}, {"b": "VINFAST", "u": "VF6", "v": "ECO", "t": "", "s": 1419000, "d": 0, "dp": 283800, "la": 1135200, "cmf": 96492, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 415292, "o15": 27434, "o14": 32164, "o13": 40047, "o12": 55814, "o2c": 320850, "o25": 29766, "o24": 34898, "o23": 43451, "o22": 60558}, {"b": "VINFAST", "u": "VF6", "v": "PLUS", "t": "", "s": 1610000, "d": 0, "dp": 322000, "la": 1288000, "cmf": 109480, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 471480, "o15": 31127, "o14": 36493, "o13": 45438, "o12": 63327, "o2c": 364050, "o25": 33772, "o24": 39595, "o23": 49300, "o22": 68709}, {"b": "VINFAST", "u": "VF7", "v": "ECO", "t": "", "s": 1760000, "d": 0, "dp": 352000, "la": 1408000, "cmf": 119680, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 511680, "o15": 34027, "o14": 39893, "o13": 49671, "o12": 69227, "o2c": 394050, "o25": 36919, "o24": 43284, "o23": 53893, "o22": 75111}, {"b": "VINFAST", "u": "VF7", "v": "PLUS", "t": "", "s": 2380000, "d": 0, "dp": 476000, "la": 1904000, "cmf": 161840, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 687840, "o15": 46013, "o14": 53947, "o13": 67169, "o12": 93613, "o2c": 528050, "o25": 49924, "o24": 58532, "o23": 72878, "o22": 101570}, {"b": "VINFAST", "u": "VF9", "v": "PLUS", "t": "", "s": 5390000, "d": 0, "dp": 1078000, "la": 4312000, "cmf": 366520, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1524520, "o15": 104207, "o14": 122173, "o13": 152118, "o12": 212007, "o2c": 1160050, "o25": 113064, "o24": 132558, "o23": 165048, "o22": 230027}, {"b": "CHERRY", "u": "TIGGO", "v": "TOURING", "t": "MT", "s": 698000, "d": 0, "dp": 139600, "la": 558400, "cmf": 47464, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 212064, "o15": 13495, "o14": 15821, "o13": 19699, "o12": 27455, "o2c": 166650, "o25": 14642, "o24": 17166, "o23": 21374, "o22": 29788}, {"b": "CHERRY", "u": "TIGGO 2", "v": "PRO", "t": "CVT", "s": 888000, "d": 0, "dp": 177600, "la": 710400, "cmf": 60384, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 265984, "o15": 17168, "o14": 20128, "o13": 25061, "o12": 34928, "o2c": 207650, "o25": 18627, "o24": 21839, "o23": 27192, "o22": 37897}, {"b": "CHERRY", "u": "TIGGO CROSS", "v": "1.5L", "t": "CVT", "s": 998000, "d": 0, "dp": 199600, "la": 798400, "cmf": 67864, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 295464, "o15": 19295, "o14": 22621, "o13": 28166, "o12": 39255, "o2c": 229650, "o25": 20935, "o24": 24544, "o23": 30560, "o22": 42591}, {"b": "CHERRY", "u": "TIGGO CROSS", "v": "1.5L TURBO", "t": "DCT", "s": 1098000, "d": 0, "dp": 219600, "la": 878400, "cmf": 74664, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 324264, "o15": 21228, "o14": 24888, "o13": 30988, "o12": 43188, "o2c": 251650, "o25": 23032, "o24": 27003, "o23": 33622, "o22": 46859}, {"b": "CHERRY", "u": "TIGGO CROSS", "v": "1.5L", "t": "HEV", "s": 1248000, "d": 0, "dp": 249600, "la": 998400, "cmf": 84864, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 364464, "o15": 24128, "o14": 28288, "o13": 35221, "o12": 49088, "o2c": 281650, "o25": 26179, "o24": 30692, "o23": 38215, "o22": 53260}, {"b": "CHERRY", "u": "TIGGO", "v": "GRAND TOUR", "t": "MPV", "s": 1188000, "d": 0, "dp": 237600, "la": 950400, "cmf": 80784, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 348384, "o15": 22968, "o14": 26928, "o13": 33528, "o12": 46728, "o2c": 269650, "o25": 24920, "o24": 29217, "o23": 36378, "o22": 50700}, {"b": "CHERRY", "u": "TIGGO REV", "v": "HYBRID SUV", "t": "", "s": 1648000, "d": 0, "dp": 329600, "la": 1318400, "cmf": 112064, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 481664, "o15": 31861, "o14": 37355, "o13": 46510, "o12": 64821, "o2c": 371650, "o25": 34570, "o24": 40530, "o23": 50464, "o22": 70331}, {"b": "CHERRY", "u": "TIGGO 7", "v": "PRO-HYBRID", "t": "", "s": 1350000, "d": 0, "dp": 270000, "la": 1080000, "cmf": 91800, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 396800, "o15": 26100, "o14": 30600, "o13": 38100, "o12": 53100, "o2c": 307050, "o25": 28318, "o24": 33201, "o23": 41338, "o22": 57614}, {"b": "CHERRY", "u": "TIGGO 8", "v": "PRO 1.6", "t": "", "s": 1698000, "d": 0, "dp": 339600, "la": 1358400, "cmf": 115464, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 495064, "o15": 32828, "o14": 38488, "o13": 47921, "o12": 66788, "o2c": 381650, "o25": 35618, "o24": 41759, "o23": 51995, "o22": 72465}, {"b": "CHERRY", "u": "TIGGO 8", "v": "PRO 2.0", "t": "AWD", "s": 1998000, "d": 0, "dp": 399600, "la": 1598400, "cmf": 135864, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 563464, "o15": 38628, "o14": 45288, "o13": 56388, "o12": 78588, "o2c": 429650, "o25": 41911, "o24": 49137, "o23": 61181, "o22": 85268}, {"b": "CHERRY", "u": "EQ1", "v": "", "t": "", "s": 1000000, "d": 0, "dp": 200000, "la": 800000, "cmf": 68000, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 296000, "o15": 19333, "o14": 22667, "o13": 28222, "o12": 39333, "o2c": 230050, "o25": 20977, "o24": 24593, "o23": 30621, "o22": 42677}, {"b": "JETOUR", "u": "ICE CREAM EV", "v": "", "t": "", "s": 699000, "d": 0, "dp": 139800, "la": 559200, "cmf": 47532, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 212332, "o15": 13514, "o14": 15844, "o13": 19727, "o12": 27494, "o2c": 166850, "o25": 14663, "o24": 17191, "o23": 21404, "o22": 29831}, {"b": "JETOUR", "u": "X70", "v": "1.5L PLUS", "t": "AT", "s": 1499000, "d": 0, "dp": 299800, "la": 1199200, "cmf": 101932, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 436732, "o15": 28981, "o14": 33977, "o13": 42305, "o12": 58961, "o2c": 336850, "o25": 31444, "o24": 36865, "o23": 45901, "o22": 63972}, {"b": "JETOUR", "u": "X70", "v": "1.5L IDM", "t": "AT", "s": 1523000, "d": 0, "dp": 304600, "la": 1218400, "cmf": 103564, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 448164, "o15": 29445, "o14": 34521, "o13": 42982, "o12": 59905, "o2c": 346650, "o25": 31947, "o24": 37456, "o23": 46636, "o22": 64997}, {"b": "JETOUR", "u": "DASHING", "v": "1.5L TURBO", "t": "AT", "s": 1373000, "d": 0, "dp": 274600, "la": 1098400, "cmf": 93364, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 402964, "o15": 26545, "o14": 31121, "o13": 38749, "o12": 54005, "o2c": 311650, "o25": 28801, "o24": 33767, "o23": 42043, "o22": 58595}, {"b": "JETOUR", "u": "DASHING", "v": "SYMPHONY", "t": "AT", "s": 1523000, "d": 0, "dp": 304600, "la": 1218400, "cmf": 103564, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 448164, "o15": 29445, "o14": 34521, "o13": 42982, "o12": 59905, "o2c": 346650, "o25": 31947, "o24": 37456, "o23": 46636, "o22": 64997}, {"b": "JETOUR", "u": "DASHING", "v": "LIGHTNING IDM", "t": "AT", "s": 1748000, "d": 0, "dp": 349600, "la": 1398400, "cmf": 118864, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 508464, "o15": 33795, "o14": 39621, "o13": 49332, "o12": 68755, "o2c": 391650, "o25": 36667, "o24": 42989, "o23": 53526, "o22": 74599}, {"b": "JETOUR", "u": "X50", "v": "SPORT 1,5L TURBO", "t": "", "s": 1088000, "d": 0, "dp": 217600, "la": 870400, "cmf": 73984, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 321584, "o15": 21035, "o14": 24661, "o13": 30706, "o12": 42795, "o2c": 249650, "o25": 22823, "o24": 26758, "o23": 33316, "o22": 46432}, {"b": "JETOUR", "u": "T2 (HYBRID)", "v": "IDM 4X2", "t": "", "s": 2488000, "d": 0, "dp": 497600, "la": 1990400, "cmf": 169184, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 716784, "o15": 48101, "o14": 56395, "o13": 70217, "o12": 97861, "o2c": 549650, "o25": 52190, "o24": 61188, "o23": 76185, "o22": 106180}, {"b": "JETOUR", "u": "T2 (HYBRID)", "v": "PANDA IDM 4X2", "t": "", "s": 2888000, "d": 0, "dp": 577600, "la": 2310400, "cmf": 196384, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 853984, "o15": 55835, "o14": 65461, "o13": 81506, "o12": 113595, "o2c": 659650, "o25": 60581, "o24": 71026, "o23": 88434, "o22": 123250}, {"b": "JETOUR", "u": "T2 (GAS)", "v": "PANDA EDITION", "t": "", "s": 2898000, "d": 0, "dp": 579600, "la": 2318400, "cmf": 197064, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 856664, "o15": 56028, "o14": 65688, "o13": 81788, "o12": 113988, "o2c": 661650, "o25": 60790, "o24": 71271, "o23": 88740, "o22": 123677}, {"b": "JETOUR", "u": "T2 (GAS)", "v": "BEYOND", "t": "", "s": 2498000, "d": 0, "dp": 499600, "la": 1998400, "cmf": 169864, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 719464, "o15": 48295, "o14": 56621, "o13": 70499, "o12": 98255, "o2c": 551650, "o25": 52400, "o24": 61434, "o23": 76492, "o22": 106606}, {"b": "JETOUR", "u": "T2 (GAS)", "v": "TERRAIN", "t": "", "s": 2598000, "d": 0, "dp": 519600, "la": 2078400, "cmf": 176664, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 776264, "o15": 50228, "o14": 58888, "o13": 73321, "o12": 102188, "o2c": 601650, "o25": 54497, "o24": 63893, "o23": 79554, "o22": 110874}, {"b": "JETOUR", "u": "T2 TERMINATOR", "v": "EXECUTIVE 2.0L", "t": "AT", "s": 2898000, "d": 0, "dp": 579600, "la": 2318400, "cmf": 197064, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 856664, "o15": 56028, "o14": 65688, "o13": 81788, "o12": 113988, "o2c": 661650, "o25": 60790, "o24": 71271, "o23": 88740, "o22": 123677}, {"b": "JETOUR", "u": "T2 TERMINATOR", "v": "OUTDOOR 2.0L", "t": "AT", "s": 2898000, "d": 0, "dp": 579600, "la": 2318400, "cmf": 197064, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 856664, "o15": 56028, "o14": 65688, "o13": 81788, "o12": 113988, "o2c": 661650, "o25": 60790, "o24": 71271, "o23": 88740, "o22": 123677}, {"b": "HONDA", "u": "BRIO", "v": "1.2 V", "t": "CVT", "s": 827000, "d": 0, "dp": 165400, "la": 661600, "cmf": 56236, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 249636, "o15": 15989, "o14": 18745, "o13": 23340, "o12": 32529, "o2c": 195450, "o25": 17348, "o24": 20339, "o23": 25324, "o22": 35294}, {"b": "HONDA", "u": "BRIO", "v": "1.2 RS BLACK TOP", "t": "CVT", "s": 863000, "d": 0, "dp": 172600, "la": 690400, "cmf": 58684, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 259284, "o15": 16685, "o14": 19561, "o13": 24356, "o12": 33945, "o2c": 202650, "o25": 18103, "o24": 21224, "o23": 26426, "o22": 36830}, {"b": "HONDA", "u": "CITY SENSING", "v": "1.5 S", "t": "CVT", "s": 998000, "d": 0, "dp": 199600, "la": 798400, "cmf": 67864, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 295464, "o15": 19295, "o14": 22621, "o13": 28166, "o12": 39255, "o2c": 229650, "o25": 20935, "o24": 24544, "o23": 30560, "o22": 42591}, {"b": "HONDA", "u": "CITY SENSING", "v": "1.5 V", "t": "CVT", "s": 1073000, "d": 0, "dp": 214600, "la": 858400, "cmf": 72964, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 317564, "o15": 20745, "o14": 24321, "o13": 30282, "o12": 42205, "o2c": 246650, "o25": 22508, "o24": 26389, "o23": 32856, "o22": 45792}, {"b": "HONDA", "u": "CITY SENSING", "v": "1.5 RS", "t": "CVT", "s": 1138000, "d": 0, "dp": 227600, "la": 910400, "cmf": 77384, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 334984, "o15": 22001, "o14": 25795, "o13": 32117, "o12": 44761, "o2c": 259650, "o25": 23871, "o24": 27987, "o23": 34847, "o22": 48566}, {"b": "HONDA", "u": "CITY SENSING", "v": "1.5 RS HATCHBACK", "t": "CVT", "s": 1199000, "d": 0, "dp": 239800, "la": 959200, "cmf": 81532, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351332, "o15": 23181, "o14": 27177, "o13": 33838, "o12": 47161, "o2c": 271850, "o25": 25151, "o24": 29487, "o23": 36715, "o22": 51169}, {"b": "HONDA", "u": "CIVIC SENSING", "v": "1.5 V TURBO", "t": "CVT", "s": 1600000, "d": 0, "dp": 320000, "la": 1280000, "cmf": 108800, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 468800, "o15": 30933, "o14": 36267, "o13": 45156, "o12": 62933, "o2c": 362050, "o25": 33563, "o24": 39349, "o23": 48994, "o22": 68283}, {"b": "HONDA", "u": "CIVIC SENSING", "v": "1.5 RS TURBO", "t": "CVT", "s": 1790000, "d": 0, "dp": 358000, "la": 1432000, "cmf": 121720, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 519720, "o15": 34607, "o14": 40573, "o13": 50518, "o12": 70407, "o2c": 400050, "o25": 37548, "o24": 44022, "o23": 54812, "o22": 76391}, {"b": "HONDA", "u": "CIVIC SENSING", "v": "2.0 RS E-HEV", "t": "CVT", "s": 1990000, "d": 0, "dp": 398000, "la": 1592000, "cmf": 135320, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 573320, "o15": 38473, "o14": 45107, "o13": 56162, "o12": 78273, "o2c": 440050, "o25": 41744, "o24": 48941, "o23": 60936, "o22": 84927}, {"b": "HONDA", "u": "BR-V", "v": "1.5 S", "t": "MT", "s": 1090000, "d": 0, "dp": 218000, "la": 872000, "cmf": 74120, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 322120, "o15": 21073, "o14": 24707, "o13": 30762, "o12": 42873, "o2c": 250050, "o25": 22865, "o24": 26807, "o23": 33377, "o22": 46518}, {"b": "HONDA", "u": "BR-V", "v": "1.5 S", "t": "CVT", "s": 1155000, "d": 0, "dp": 231000, "la": 924000, "cmf": 78540, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 339540, "o15": 22330, "o14": 26180, "o13": 32597, "o12": 45430, "o2c": 263050, "o25": 24228, "o24": 28405, "o23": 35367, "o22": 49292}, {"b": "HONDA", "u": "BR-V", "v": "1.5 V", "t": "CVT", "s": 1295000, "d": 0, "dp": 259000, "la": 1036000, "cmf": 88060, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 377060, "o15": 25037, "o14": 29353, "o13": 36548, "o12": 50937, "o2c": 291050, "o25": 27165, "o24": 31848, "o23": 39654, "o22": 55266}, {"b": "HONDA", "u": "BR-V", "v": "1.5 VX", "t": "CVT", "s": 1395000, "d": 0, "dp": 279000, "la": 1116000, "cmf": 94860, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 408860, "o15": 26970, "o14": 31620, "o13": 39370, "o12": 54870, "o2c": 316050, "o25": 29262, "o24": 34308, "o23": 42716, "o22": 59534}, {"b": "HONDA", "u": "HR-V", "v": "1.5 S", "t": "CVT", "s": 1450000, "d": 0, "dp": 290000, "la": 1160000, "cmf": 98600, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 423600, "o15": 28033, "o14": 32867, "o13": 40922, "o12": 57033, "o2c": 327050, "o25": 30416, "o24": 35660, "o23": 44401, "o22": 61881}, {"b": "HONDA", "u": "HR-V", "v": "1.5 V", "t": "CVT", "s": 1519000, "d": 0, "dp": 303800, "la": 1215200, "cmf": 103292, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 447092, "o15": 29367, "o14": 34431, "o13": 42870, "o12": 59747, "o2c": 345850, "o25": 31864, "o24": 37357, "o23": 46513, "o22": 64826}, {"b": "HONDA", "u": "HR-V", "v": "1.5 RS E-HEV", "t": "CVT", "s": 1790000, "d": 0, "dp": 358000, "la": 1432000, "cmf": 121720, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 519720, "o15": 34607, "o14": 40573, "o13": 50518, "o12": 70407, "o2c": 400050, "o25": 37548, "o24": 44022, "o23": 54812, "o22": 76391}, {"b": "HONDA", "u": "CR-V", "v": "1.5 V TURBO", "t": "CVT", "s": 2100000, "d": 0, "dp": 420000, "la": 1680000, "cmf": 142800, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 612800, "o15": 40600, "o14": 47600, "o13": 59267, "o12": 82600, "o2c": 472050, "o25": 44051, "o24": 51646, "o23": 64304, "o22": 89621}, {"b": "HONDA", "u": "CR-V", "v": "1.5 VX TURBO", "t": "CVT", "s": 2280000, "d": 0, "dp": 456000, "la": 1824000, "cmf": 155040, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 661040, "o15": 44080, "o14": 51680, "o13": 64347, "o12": 89680, "o2c": 508050, "o25": 47827, "o24": 56073, "o23": 69816, "o22": 97303}, {"b": "HONDA", "u": "CR-V", "v": "2.0 RS E-HEV", "t": "CVT", "s": 2605000, "d": 0, "dp": 521000, "la": 2084000, "cmf": 177140, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 778140, "o15": 50363, "o14": 59047, "o13": 73519, "o12": 102463, "o2c": 603050, "o25": 54644, "o24": 64066, "o23": 79768, "o22": 111173}, {"b": "MG", "u": "MG5", "v": "1.5L CORE", "t": "MT", "s": 698888, "d": 0, "dp": 139778, "la": 559110, "cmf": 47524, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 212302, "o15": 13512, "o14": 15841, "o13": 19724, "o12": 27490, "o2c": 166828, "o25": 14660, "o24": 17188, "o23": 21401, "o22": 29826}, {"b": "MG", "u": "MG5", "v": "1.5L CORE", "t": "CVT", "s": 738888, "d": 0, "dp": 147778, "la": 591110, "cmf": 50244, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 223022, "o15": 14285, "o14": 16748, "o13": 20853, "o12": 29063, "o2c": 174828, "o25": 15499, "o24": 18172, "o23": 22626, "o22": 31533}, {"b": "MG", "u": "MG5", "v": "1.5L STYLE", "t": "CVT", "s": 878888, "d": 0, "dp": 175778, "la": 703110, "cmf": 59764, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 263542, "o15": 16992, "o14": 19921, "o13": 24804, "o12": 34570, "o2c": 205828, "o25": 18436, "o24": 21615, "o23": 26913, "o22": 37508}, {"b": "MG", "u": "MG3 HATCHBACK", "v": "1.5L", "t": "MT STD", "s": 678888, "d": 0, "dp": 135778, "la": 543110, "cmf": 46164, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 206942, "o15": 13125, "o14": 15388, "o13": 19160, "o12": 26703, "o2c": 162828, "o25": 14241, "o24": 16696, "o23": 20788, "o22": 28973}, {"b": "MG", "u": "MG3 HATCHBACK", "v": "1.5L", "t": "CVT COM", "s": 828888, "d": 0, "dp": 165778, "la": 663110, "cmf": 56364, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 250142, "o15": 16025, "o14": 18788, "o13": 23393, "o12": 32603, "o2c": 195828, "o25": 17387, "o24": 20385, "o23": 25381, "o22": 35374}, {"b": "MG", "u": "MG3 HATCHBACK", "v": "1.5L", "t": "CVT LUX", "s": 898888, "d": 0, "dp": 179778, "la": 719110, "cmf": 61124, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 268902, "o15": 17379, "o14": 20375, "o13": 25369, "o12": 35356, "o2c": 209828, "o25": 18856, "o24": 22107, "o23": 27525, "o22": 38362}, {"b": "MG", "u": "MG3 HATCHBACK", "v": "1.5L EV", "t": "LUX HEV", "s": 1088888, "d": 0, "dp": 217778, "la": 871110, "cmf": 74044, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 321822, "o15": 21052, "o14": 24681, "o13": 30731, "o12": 42830, "o2c": 249828, "o25": 22841, "o24": 26779, "o23": 33343, "o22": 46470}, {"b": "MG", "u": "MG ZS", "v": "1.5L STYLE", "t": "MT", "s": 848888, "d": 0, "dp": 169778, "la": 679110, "cmf": 57724, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 255502, "o15": 16412, "o14": 19241, "o13": 23958, "o12": 33390, "o2c": 199828, "o25": 17807, "o24": 20877, "o23": 25994, "o22": 36228}, {"b": "MG", "u": "MG ZS", "v": "1.5L STLE", "t": "AT", "s": 898888, "d": 0, "dp": 179778, "la": 719110, "cmf": 61124, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 268902, "o15": 17379, "o14": 20375, "o13": 25369, "o12": 35356, "o2c": 209828, "o25": 18856, "o24": 22107, "o23": 27525, "o22": 38362}, {"b": "MG", "u": "MG ZS", "v": "1.5L ALPHA", "t": "AT", "s": 1048888, "d": 0, "dp": 209778, "la": 839110, "cmf": 71324, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 311102, "o15": 20279, "o14": 23775, "o13": 29602, "o12": 41256, "o2c": 241828, "o25": 22002, "o24": 25796, "o23": 32118, "o22": 44763}, {"b": "MG", "u": "MG ZS", "v": "1.3L TROPHY", "t": "", "s": 1198888, "d": 0, "dp": 239778, "la": 959110, "cmf": 81524, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351302, "o15": 23179, "o14": 27175, "o13": 33835, "o12": 47156, "o2c": 271828, "o25": 25149, "o24": 29485, "o23": 36711, "o22": 51165}, {"b": "MG", "u": "MG ZS", "v": "1.5L MCE", "t": "CVT COM", "s": 918888, "d": 0, "dp": 183778, "la": 735110, "cmf": 62484, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 274262, "o15": 17765, "o14": 20828, "o13": 25933, "o12": 36143, "o2c": 213828, "o25": 19275, "o24": 22599, "o23": 28137, "o22": 39215}, {"b": "MG", "u": "MG ZS", "v": "1.5L MCE", "t": "CVT LUX", "s": 1088888, "d": 0, "dp": 217778, "la": 871110, "cmf": 74044, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 321822, "o15": 21052, "o14": 24681, "o13": 30731, "o12": 42830, "o2c": 249828, "o25": 22841, "o24": 26779, "o23": 33343, "o22": 46470}, {"b": "MG", "u": "GT SEDAN", "v": "1.5T ALPHA", "t": "AT", "s": 1015888, "d": 0, "dp": 203178, "la": 812710, "cmf": 69080, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 302258, "o15": 19641, "o14": 23027, "o13": 28671, "o12": 39958, "o2c": 235228, "o25": 21310, "o24": 24984, "o23": 31108, "o22": 43355}, {"b": "MG", "u": "GT SEDAN", "v": "1.5T SPORTS", "t": "AT", "s": 1193888, "d": 0, "dp": 238778, "la": 955110, "cmf": 81184, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 349962, "o15": 23082, "o14": 27061, "o13": 33694, "o12": 46960, "o2c": 270828, "o25": 25044, "o24": 29362, "o23": 36558, "o22": 50951}, {"b": "MG", "u": "ONE SUV", "v": "1.5L ONE", "t": "AT STD", "s": 1298888, "d": 0, "dp": 259778, "la": 1039110, "cmf": 88324, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 378102, "o15": 25112, "o14": 29441, "o13": 36658, "o12": 51090, "o2c": 291828, "o25": 27246, "o24": 31944, "o23": 39773, "o22": 55432}, {"b": "MG", "u": "ONE SUV", "v": "1.5L  ONE", "t": "AT LUX", "s": 1458888, "d": 0, "dp": 291778, "la": 1167110, "cmf": 99204, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 425982, "o15": 28205, "o14": 33068, "o13": 41173, "o12": 57383, "o2c": 328828, "o25": 30603, "o24": 35879, "o23": 44673, "o22": 62260}, {"b": "MG", "u": "G50 MPV", "v": "1.5L PLUS", "t": "MT", "s": 1048888, "d": 0, "dp": 209778, "la": 839110, "cmf": 71324, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 311102, "o15": 20279, "o14": 23775, "o13": 29602, "o12": 41256, "o2c": 241828, "o25": 22002, "o24": 25796, "o23": 32118, "o22": 44763}, {"b": "MG", "u": "G50 MPV", "v": "1.5L PLUS", "t": "AT COM", "s": 1198888, "d": 0, "dp": 239778, "la": 959110, "cmf": 81524, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351302, "o15": 23179, "o14": 27175, "o13": 33835, "o12": 47156, "o2c": 271828, "o25": 25149, "o24": 29485, "o23": 36711, "o22": 51165}, {"b": "MG", "u": "G50 MPV", "v": "1.5L PLUS", "t": "AT LUX", "s": 1288888, "d": 0, "dp": 257778, "la": 1031110, "cmf": 87644, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 375422, "o15": 24919, "o14": 29215, "o13": 36375, "o12": 50696, "o2c": 289828, "o25": 27037, "o24": 31698, "o23": 39467, "o22": 55005}, {"b": "MG", "u": "G50 MPV", "v": "1.5L PLUS", "t": "AT LUX", "s": 1288888, "d": 0, "dp": 257778, "la": 1031110, "cmf": 87644, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 375422, "o15": 24919, "o14": 29215, "o13": 36375, "o12": 50696, "o2c": 289828, "o25": 27037, "o24": 31698, "o23": 39467, "o22": 55005}, {"b": "MG", "u": "EV", "v": "MARVEL R", "t": "RWD", "s": 2588888, "d": 0, "dp": 517778, "la": 2071110, "cmf": 176044, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 773822, "o15": 50052, "o14": 58681, "o13": 73064, "o12": 101830, "o2c": 599828, "o25": 54306, "o24": 63669, "o23": 79275, "o22": 110485}, {"b": "MG", "u": "EV", "v": "MARVEL R", "t": "AWD", "s": 2888888, "d": 0, "dp": 577778, "la": 2311110, "cmf": 196444, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 854222, "o15": 55852, "o14": 65481, "o13": 81531, "o12": 113630, "o2c": 659828, "o25": 60599, "o24": 71047, "o23": 88461, "o22": 123288}, {"b": "MG", "u": "EV", "v": "MG4", "t": "STD", "s": 1468888, "d": 0, "dp": 293778, "la": 1175110, "cmf": 99884, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 428662, "o15": 28399, "o14": 33295, "o13": 41455, "o12": 57776, "o2c": 330828, "o25": 30812, "o24": 36125, "o23": 44979, "o22": 62687}, {"b": "MG", "u": "EV", "v": "MG4", "t": "LUX", "s": 1738888, "d": 0, "dp": 347778, "la": 1391110, "cmf": 118244, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 506022, "o15": 33619, "o14": 39415, "o13": 49075, "o12": 68396, "o2c": 389828, "o25": 36476, "o24": 42765, "o23": 53247, "o22": 74210}, {"b": "MG", "u": "EV", "v": "MG4 XPOWER", "t": "AWD", "s": 2000888, "d": 0, "dp": 400178, "la": 1600710, "cmf": 136060, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 586238, "o15": 38684, "o14": 45353, "o13": 56470, "o12": 78702, "o2c": 452228, "o25": 41972, "o24": 49209, "o23": 61269, "o22": 85391}, {"b": "MG", "u": "EV", "v": "ZS", "t": "LUX", "s": 1838888, "d": 0, "dp": 367778, "la": 1471110, "cmf": 125044, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 532822, "o15": 35552, "o14": 41681, "o13": 51898, "o12": 72330, "o2c": 409828, "o25": 38574, "o24": 45224, "o23": 56309, "o22": 78478}, {"b": "KIA", "u": "SOLUTO", "v": "1.4L LX", "t": "AT", "s": 835000, "d": 0, "dp": 167000, "la": 668000, "cmf": 56780, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 251780, "o15": 16143, "o14": 18927, "o13": 23566, "o12": 32843, "o2c": 197050, "o25": 17516, "o24": 20535, "o23": 25569, "o22": 35635}, {"b": "KIA", "u": "SOLUTO", "v": "1.4L EX", "t": "AT", "s": 865000, "d": 0, "dp": 173000, "la": 692000, "cmf": 58820, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 259820, "o15": 16723, "o14": 19607, "o13": 24412, "o12": 34023, "o2c": 203050, "o25": 18145, "o24": 21273, "o23": 26487, "o22": 36915}, {"b": "KIA", "u": "SOLUTO", "v": "1.4L L", "t": "MT", "s": 698000, "d": 0, "dp": 139600, "la": 558400, "cmf": 47464, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 212064, "o15": 13495, "o14": 15821, "o13": 19699, "o12": 27455, "o2c": 166650, "o25": 14642, "o24": 17166, "o23": 21374, "o22": 29788}, {"b": "KIA", "u": "SOLUTO", "v": "1.4 L", "t": "AT", "s": 748000, "d": 0, "dp": 149600, "la": 598400, "cmf": 50864, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 225464, "o15": 14461, "o14": 16955, "o13": 21110, "o12": 29421, "o2c": 176650, "o25": 15691, "o24": 18396, "o23": 22905, "o22": 31922}, {"b": "KIA", "u": "SOLUTO", "v": "1.4 LX", "t": "AT", "s": 848000, "d": 0, "dp": 169600, "la": 678400, "cmf": 57664, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 255264, "o15": 16395, "o14": 19221, "o13": 23932, "o12": 33355, "o2c": 199650, "o25": 17788, "o24": 20855, "o23": 25967, "o22": 36190}, {"b": "KIA", "u": "SOLUTO", "v": "1.4L LX+", "t": "AT", "s": 888000, "d": 0, "dp": 177600, "la": 710400, "cmf": 60384, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 265984, "o15": 17168, "o14": 20128, "o13": 25061, "o12": 34928, "o2c": 207650, "o25": 18627, "o24": 21839, "o23": 27192, "o22": 37897}, {"b": "KIA", "u": "STONIC", "v": "1.4 EX", "t": "AT", "s": 990000, "d": 0, "dp": 198000, "la": 792000, "cmf": 67320, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 293320, "o15": 19140, "o14": 22440, "o13": 27940, "o12": 38940, "o2c": 228050, "o25": 20767, "o24": 24347, "o23": 30315, "o22": 42250}, {"b": "KIA", "u": "SONET", "v": "1.5L LX", "t": "MT", "s": 778000, "d": 0, "dp": 155600, "la": 622400, "cmf": 52904, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 233504, "o15": 15041, "o14": 17635, "o13": 21957, "o12": 30601, "o2c": 182650, "o25": 16320, "o24": 19134, "o23": 23823, "o22": 33202}, {"b": "KIA", "u": "SONET", "v": "1.5L LX", "t": "AT", "s": 908000, "d": 0, "dp": 181600, "la": 726400, "cmf": 61744, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 271344, "o15": 17555, "o14": 20581, "o13": 25626, "o12": 35715, "o2c": 211650, "o25": 19047, "o24": 22331, "o23": 27804, "o22": 38750}, {"b": "KIA", "u": "SONET", "v": "1.5L EX", "t": "AT", "s": 1018000, "d": 0, "dp": 203600, "la": 814400, "cmf": 69224, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 302824, "o15": 19681, "o14": 23075, "o13": 28730, "o12": 40041, "o2c": 235650, "o25": 21354, "o24": 25036, "o23": 31172, "o22": 43445}, {"b": "KIA", "u": "SONET", "v": "1.5L SX", "t": "AT", "s": 1178000, "d": 0, "dp": 235600, "la": 942400, "cmf": 80104, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 345704, "o15": 22775, "o14": 26701, "o13": 33246, "o12": 46335, "o2c": 267650, "o25": 24711, "o24": 28971, "o23": 36072, "o22": 50273}, {"b": "KIA", "u": "SELTOS", "v": "1.5L LX", "t": "AT", "s": 1198000, "d": 0, "dp": 239600, "la": 958400, "cmf": 81464, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351064, "o15": 23161, "o14": 27155, "o13": 33810, "o12": 47121, "o2c": 271650, "o25": 25130, "o24": 29463, "o23": 36684, "o22": 51127}, {"b": "KIA", "u": "SELTOS", "v": "1.5L EX", "t": "AT", "s": 1298000, "d": 0, "dp": 259600, "la": 1038400, "cmf": 88264, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 377864, "o15": 25095, "o14": 29421, "o13": 36632, "o12": 51055, "o2c": 291650, "o25": 27228, "o24": 31922, "o23": 39746, "o22": 55394}, {"b": "KIA", "u": "SELTOS", "v": "1.4T SX", "t": "AT", "s": 1688000, "d": 0, "dp": 337600, "la": 1350400, "cmf": 114784, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 492384, "o15": 32635, "o14": 38261, "o13": 47639, "o12": 66395, "o2c": 379650, "o25": 35409, "o24": 41514, "o23": 51688, "o22": 72038}, {"b": "KIA", "u": "KARGO", "v": "4X2", "t": "MT", "s": 1305000, "d": 0, "dp": 261000, "la": 1044000, "cmf": 88740, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 384740, "o15": 25230, "o14": 29580, "o13": 36830, "o12": 51330, "o2c": 298050, "o25": 27375, "o24": 32094, "o23": 39961, "o22": 55693}, {"b": "KIA", "u": "KARGO", "v": "4X4", "t": "MT", "s": 1405000, "d": 0, "dp": 281000, "la": 1124000, "cmf": 95540, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 411540, "o15": 27163, "o14": 31847, "o13": 39652, "o12": 55263, "o2c": 318050, "o25": 29472, "o24": 34554, "o23": 43023, "o22": 59961}, {"b": "KIA", "u": "KARGO", "v": "K2500 4X4", "t": "MT", "s": 1238000, "d": 0, "dp": 247600, "la": 990400, "cmf": 84184, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 361784, "o15": 23935, "o14": 28061, "o13": 34939, "o12": 48695, "o2c": 279650, "o25": 25969, "o24": 30447, "o23": 37909, "o22": 52834}, {"b": "KIA", "u": "KARGO", "v": "K2500 4X2", "t": "MT", "s": 1208000, "d": 0, "dp": 241600, "la": 966400, "cmf": 82144, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 353744, "o15": 23355, "o14": 27381, "o13": 34092, "o12": 47515, "o2c": 273650, "o25": 25340, "o24": 29709, "o23": 36990, "o22": 51553}, {"b": "KIA", "u": "CARNIVAL", "v": "2.2L EX", "t": "AT", "s": 2888000, "d": 0, "dp": 577600, "la": 2310400, "cmf": 196384, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 853984, "o15": 55835, "o14": 65461, "o13": 81506, "o12": 113595, "o2c": 659650, "o25": 60581, "o24": 71026, "o23": 88434, "o22": 123250}, {"b": "KIA", "u": "CARNIVAL", "v": "2.2L SX", "t": "AT", "s": 3368000, "d": 0, "dp": 673600, "la": 2694400, "cmf": 229024, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 982624, "o15": 65115, "o14": 76341, "o13": 95052, "o12": 132475, "o2c": 755650, "o25": 70649, "o24": 82830, "o23": 103132, "o22": 143735}, {"b": "KIA", "u": "EV6", "v": "GT LINE", "t": "AT", "s": 3768000, "d": 0, "dp": 753600, "la": 3014400, "cmf": 256224, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1089824, "o15": 72848, "o14": 85408, "o13": 106341, "o12": 148208, "o2c": 835650, "o25": 79040, "o24": 92668, "o23": 115380, "o22": 160806}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GL", "t": "IVT", "s": 1068000, "d": 0, "dp": 213600, "la": 854400, "cmf": 72624, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 316224, "o15": 20648, "o14": 24208, "o13": 30141, "o12": 42008, "o2c": 245650, "o25": 22403, "o24": 26266, "o23": 32703, "o22": 45579}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS", "t": "IVT", "s": 1198000, "d": 0, "dp": 239600, "la": 958400, "cmf": 81464, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351064, "o15": 23161, "o14": 27155, "o13": 33810, "o12": 47121, "o2c": 271650, "o25": 25130, "o24": 29463, "o23": 36684, "o22": 51127}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS PREMIUM", "t": "IVT 7STR", "s": 1288000, "d": 0, "dp": 257600, "la": 1030400, "cmf": 87584, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 375184, "o15": 24901, "o14": 29195, "o13": 36350, "o12": 50661, "o2c": 289650, "o25": 27018, "o24": 31676, "o23": 39440, "o22": 54968}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS PREMIUM", "t": "IVT 6STR", "s": 1296000, "d": 0, "dp": 259200, "la": 1036800, "cmf": 88128, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 377328, "o15": 25056, "o14": 29376, "o13": 36576, "o12": 50976, "o2c": 291250, "o25": 27186, "o24": 31873, "o23": 39685, "o22": 55309}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS X", "t": "IVT", "s": 1348000, "d": 0, "dp": 269600, "la": 1078400, "cmf": 91664, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 386264, "o15": 26061, "o14": 30555, "o13": 38044, "o12": 53021, "o2c": 296650, "o25": 28277, "o24": 33152, "o23": 41277, "o22": 57528}, {"b": "HYUNDAI", "u": "CUSTIN", "v": "1.5T GLS", "t": "8AT", "s": 1770000, "d": 0, "dp": 354000, "la": 1416000, "cmf": 120360, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 514360, "o15": 34220, "o14": 40120, "o13": 49953, "o12": 69620, "o2c": 396050, "o25": 37129, "o24": 43530, "o23": 54199, "o22": 75538}, {"b": "HYUNDAI", "u": "CUSTIN", "v": "1.5T PREMIUM", "t": "8AT", "s": 2080000, "d": 0, "dp": 416000, "la": 1664000, "cmf": 141440, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 607440, "o15": 40213, "o14": 47147, "o13": 58702, "o12": 81813, "o2c": 468050, "o25": 43631, "o24": 51154, "o23": 63692, "o22": 88767}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5 GL", "t": "IVT STD", "s": 1098000, "d": 0, "dp": 219600, "la": 878400, "cmf": 74664, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 324264, "o15": 21228, "o14": 24888, "o13": 30988, "o12": 43188, "o2c": 251650, "o25": 23032, "o24": 27003, "o23": 33622, "o22": 46859}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5 GL", "t": "IVT", "s": 1228000, "d": 0, "dp": 245600, "la": 982400, "cmf": 83504, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 359104, "o15": 23741, "o14": 27835, "o13": 34657, "o12": 48301, "o2c": 277650, "o25": 25759, "o24": 30201, "o23": 37603, "o22": 52407}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5 GLS", "t": "IVT", "s": 1388000, "d": 0, "dp": 277600, "la": 1110400, "cmf": 94384, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 406984, "o15": 26835, "o14": 31461, "o13": 39172, "o12": 54595, "o2c": 314650, "o25": 29116, "o24": 34136, "o23": 42502, "o22": 59235}, {"b": "HYUNDAI", "u": "TUCSON", "v": "2.0 GLS", "t": "AT", "s": 1680000, "d": 0, "dp": 336000, "la": 1344000, "cmf": 114240, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 490240, "o15": 32480, "o14": 38080, "o13": 47413, "o12": 66080, "o2c": 378050, "o25": 35241, "o24": 41317, "o23": 51443, "o22": 71697}, {"b": "HYUNDAI", "u": "TUCSON", "v": "2.0 GLS+", "t": "AT", "s": 1980000, "d": 0, "dp": 396000, "la": 1584000, "cmf": 134640, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 570640, "o15": 38280, "o14": 44880, "o13": 55880, "o12": 77880, "o2c": 438050, "o25": 41534, "o24": 48695, "o23": 60630, "o22": 84500}, {"b": "HYUNDAI", "u": "TUCSON", "v": "1.6T", "t": "HEV", "s": 2290000, "d": 0, "dp": 458000, "la": 1832000, "cmf": 155720, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 663720, "o15": 44273, "o14": 51907, "o13": 64629, "o12": 90073, "o2c": 510050, "o25": 48037, "o24": 56319, "o23": 70122, "o22": 97730}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5 GLS", "t": "8AT 2WD", "s": 2410000, "d": 0, "dp": 482000, "la": 1928000, "cmf": 163880, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 695880, "o15": 46593, "o14": 54627, "o13": 68016, "o12": 94793, "o2c": 534050, "o25": 50554, "o24": 59270, "o23": 73797, "o22": 102851}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5 GLS", "t": "8AT 2WD", "s": 2540000, "d": 0, "dp": 508000, "la": 2032000, "cmf": 172720, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 760720, "o15": 49107, "o14": 57573, "o13": 71684, "o12": 99907, "o2c": 590050, "o25": 53281, "o24": 62467, "o23": 77778, "o22": 108399}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5T CALLIGRAPHY", "t": "AWD 8DCT", "s": 3100000, "d": 0, "dp": 620000, "la": 2480000, "cmf": 210800, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 910800, "o15": 59933, "o14": 70267, "o13": 87489, "o12": 121933, "o2c": 702050, "o25": 65028, "o24": 76239, "o23": 94925, "o22": 132298}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "1.6T CALLIGRAPHY", "t": "HEV", "s": 3330000, "d": 0, "dp": 666000, "la": 2664000, "cmf": 226440, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 972440, "o15": 64380, "o14": 75480, "o13": 93980, "o12": 130980, "o2c": 748050, "o25": 69852, "o24": 81896, "o23": 101968, "o22": 142113}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI", "t": "6MT", "s": 1560000, "d": 0, "dp": 312000, "la": 1248000, "cmf": 106080, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 458080, "o15": 30160, "o14": 35360, "o13": 44027, "o12": 61360, "o2c": 354050, "o25": 32724, "o24": 38366, "o23": 47769, "o22": 66576}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI", "t": "6MT", "s": 1850000, "d": 0, "dp": 370000, "la": 1480000, "cmf": 125800, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 535800, "o15": 35767, "o14": 41933, "o13": 52211, "o12": 72767, "o2c": 412050, "o25": 38807, "o24": 45498, "o23": 56649, "o22": 78952}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI GLS+", "t": "8AT", "s": 2320000, "d": 0, "dp": 464000, "la": 1856000, "cmf": 157760, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 671760, "o15": 44853, "o14": 52587, "o13": 65476, "o12": 91253, "o2c": 516050, "o25": 48666, "o24": 57057, "o23": 71041, "o22": 99010}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI PREMIUM", "t": "AWD 9STR", "s": 2830000, "d": 0, "dp": 566000, "la": 2264000, "cmf": 192440, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 838440, "o15": 54713, "o14": 64147, "o13": 79869, "o12": 111313, "o2c": 648050, "o25": 59364, "o24": 69599, "o23": 86658, "o22": 120775}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI PREMIUM", "t": "AWD 7STR", "s": 3030000, "d": 0, "dp": 606000, "la": 2424000, "cmf": 206040, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 892040, "o15": 58580, "o14": 68680, "o13": 85513, "o12": 119180, "o2c": 688050, "o25": 63559, "o24": 74518, "o23": 92782, "o22": 129310}, {"b": "HYUNDAI", "u": "PALISADE", "v": "2.2 DIESEL", "t": "8AT 4WD", "s": 3780000, "d": 0, "dp": 756000, "la": 3024000, "cmf": 257040, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1093040, "o15": 73080, "o14": 85680, "o13": 106680, "o12": 148680, "o2c": 838050, "o25": 79292, "o24": 92963, "o23": 115748, "o22": 161318}, {"b": "HYUNDAI", "u": "IONIQ 5", "v": "EV GLS", "t": "2WD S", "s": 3068000, "d": 0, "dp": 613600, "la": 2454400, "cmf": 208624, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 902224, "o15": 59315, "o14": 69541, "o13": 86586, "o12": 120675, "o2c": 695650, "o25": 64356, "o24": 75452, "o23": 93946, "o22": 130932}, {"b": "HYUNDAI", "u": "IONIQ 5", "v": "EV GLS", "t": "2WD L", "s": 3698000, "d": 0, "dp": 739600, "la": 2958400, "cmf": 251464, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1071064, "o15": 71495, "o14": 83821, "o13": 104366, "o12": 145455, "o2c": 821650, "o25": 77572, "o24": 90946, "o23": 113237, "o22": 157818}, {"b": "HYUNDAI", "u": "IONIQ 6", "v": "EV GLS", "t": "2WD", "s": 3798000, "d": 0, "dp": 759600, "la": 3038400, "cmf": 258264, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1097864, "o15": 73428, "o14": 86088, "o13": 107188, "o12": 149388, "o2c": 841650, "o25": 79669, "o24": 93405, "o23": 116299, "o22": 162086}, {"b": "HYUNDAI", "u": "H100", "v": "2.5 CRDI", "t": "6MT C&C", "s": 1024000, "d": 0, "dp": 204800, "la": 819200, "cmf": 69632, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 304432, "o15": 19797, "o14": 23211, "o13": 28900, "o12": 40277, "o2c": 236850, "o25": 21480, "o24": 25184, "o23": 31356, "o22": 43701}, {"b": "HYUNDAI", "u": "H101", "v": "2.5 CRDI", "t": "6MT SHUTTLE", "s": 1243000, "d": 0, "dp": 248600, "la": 994400, "cmf": 84524, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 363124, "o15": 24031, "o14": 28175, "o13": 35080, "o12": 48891, "o2c": 280650, "o25": 26074, "o24": 30570, "o23": 38062, "o22": 53047}, {"b": "NISSAN", "u": "ALMERA", "v": "1.0 EL TURBO", "t": "MT", "s": 839000, "d": 0, "dp": 167800, "la": 671200, "cmf": 57052, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 252852, "o15": 16221, "o14": 19017, "o13": 23678, "o12": 33001, "o2c": 197850, "o25": 17599, "o24": 20634, "o23": 25691, "o22": 35806}, {"b": "NISSAN", "u": "ALMERA", "v": "1.0 VE TURBO", "t": "CVT", "s": 1155000, "d": 0, "dp": 231000, "la": 924000, "cmf": 78540, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 339540, "o15": 22330, "o14": 26180, "o13": 32597, "o12": 45430, "o2c": 263050, "o25": 24228, "o24": 28405, "o23": 35367, "o22": 49292}, {"b": "NISSAN", "u": "ALMERA", "v": "1.0 VL TURBO", "t": "CVT", "s": 1195000, "d": 0, "dp": 239000, "la": 956000, "cmf": 81260, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 350260, "o15": 23103, "o14": 27087, "o13": 33726, "o12": 47003, "o2c": 271050, "o25": 25067, "o24": 29389, "o23": 36592, "o22": 50999}, {"b": "NISSAN", "u": "LEAF", "v": "ELECTRIC CAR", "t": "", "s": 1998000, "d": 0, "dp": 399600, "la": 1598400, "cmf": 135864, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 575464, "o15": 38628, "o14": 45288, "o13": 56388, "o12": 78588, "o2c": 441650, "o25": 41911, "o24": 49137, "o23": 61181, "o22": 85268}, {"b": "NISSAN", "u": "KICKS", "v": "VE", "t": "AT", "s": 1279000, "d": 0, "dp": 255800, "la": 1023200, "cmf": 86972, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 372772, "o15": 24727, "o14": 28991, "o13": 36096, "o12": 50307, "o2c": 287850, "o25": 26829, "o24": 31455, "o23": 39164, "o22": 54583}, {"b": "NISSAN", "u": "KICKS", "v": "VE SPORT", "t": "AT", "s": 1369000, "d": 0, "dp": 273800, "la": 1095200, "cmf": 93092, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 401892, "o15": 26467, "o14": 31031, "o13": 38636, "o12": 53847, "o2c": 310850, "o25": 28717, "o24": 33668, "o23": 41920, "o22": 58424}, {"b": "NISSAN", "u": "KICKS", "v": "VL", "t": "AT", "s": 1479000, "d": 0, "dp": 295800, "la": 1183200, "cmf": 100572, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 431372, "o15": 28594, "o14": 33524, "o13": 41741, "o12": 58174, "o2c": 332850, "o25": 31024, "o24": 36374, "o23": 45289, "o22": 63119}, {"b": "NISSAN", "u": "LIVINA", "v": "VE", "t": "AT", "s": 1189000, "d": 0, "dp": 237800, "la": 951200, "cmf": 80852, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 348652, "o15": 22987, "o14": 26951, "o13": 33556, "o12": 46767, "o2c": 269850, "o25": 24941, "o24": 29241, "o23": 36409, "o22": 50743}, {"b": "NISSAN", "u": "LIVINA", "v": "VL", "t": "AT", "s": 1249000, "d": 0, "dp": 249800, "la": 999200, "cmf": 84932, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 364732, "o15": 24147, "o14": 28311, "o13": 35250, "o12": 49127, "o2c": 281850, "o25": 26200, "o24": 30717, "o23": 38246, "o22": 53303}, {"b": "NISSAN", "u": "TERRA", "v": "VE 4X2", "t": "AT", "s": 1969000, "d": 0, "dp": 393800, "la": 1575200, "cmf": 133892, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 567692, "o15": 38067, "o14": 44631, "o13": 55570, "o12": 77447, "o2c": 435850, "o25": 41303, "o24": 48424, "o23": 60293, "o22": 84030}, {"b": "NISSAN", "u": "TERRA", "v": "VL 4X2", "t": "AT", "s": 2119000, "d": 0, "dp": 423800, "la": 1695200, "cmf": 144092, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 617892, "o15": 40967, "o14": 48031, "o13": 59803, "o12": 83347, "o2c": 475850, "o25": 44450, "o24": 52113, "o23": 64886, "o22": 90432}, {"b": "NISSAN", "u": "TERRA", "v": "VL 4X4", "t": "AT", "s": 2409000, "d": 0, "dp": 481800, "la": 1927200, "cmf": 163812, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 695612, "o15": 46574, "o14": 54604, "o13": 67987, "o12": 94754, "o2c": 533850, "o25": 50533, "o24": 59245, "o23": 73766, "o22": 102808}, {"b": "NISSAN", "u": "TERRA", "v": "SPORTS VL 2X2", "t": "AT", "s": 2179000, "d": 0, "dp": 435800, "la": 1743200, "cmf": 148172, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 633972, "o15": 42127, "o14": 49391, "o13": 61496, "o12": 85707, "o2c": 487850, "o25": 45708, "o24": 53589, "o23": 66723, "o22": 92992}, {"b": "NISSAN", "u": "TERRA", "v": "SPORTS 4X4", "t": "AT", "s": 2469000, "d": 0, "dp": 493800, "la": 1975200, "cmf": 167892, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 711692, "o15": 47734, "o14": 55964, "o13": 69681, "o12": 97114, "o2c": 545850, "o25": 51791, "o24": 60721, "o23": 75604, "o22": 105369}, {"b": "NISSAN", "u": "PATROL", "v": "GRANITE BLACK", "t": "AT", "s": 5385000, "d": 0, "dp": 1077000, "la": 4308000, "cmf": 366180, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1523180, "o15": 104110, "o14": 122060, "o13": 151977, "o12": 211810, "o2c": 1159050, "o25": 112959, "o24": 132435, "o23": 164895, "o22": 229814}, {"b": "NISSAN", "u": "PATROL", "v": "GUN METTALIC", "t": "AT", "s": 5405000, "d": 0, "dp": 1081000, "la": 4324000, "cmf": 367540, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1528540, "o15": 104497, "o14": 122513, "o13": 152541, "o12": 212597, "o2c": 1163050, "o25": 113379, "o24": 132927, "o23": 165507, "o22": 230667}, {"b": "NISSAN", "u": "PATROL", "v": "EVEREST WHITE", "t": "AT", "s": 5415000, "d": 0, "dp": 1083000, "la": 4332000, "cmf": 368220, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1531220, "o15": 104690, "o14": 122740, "o13": 152823, "o12": 212990, "o2c": 1165050, "o25": 113589, "o24": 133173, "o23": 165813, "o22": 231094}, {"b": "NISSAN", "u": "PATROL", "v": "EVEREST WHITE W/BLACK ROOF", "t": "AT", "s": 5435000, "d": 0, "dp": 1087000, "la": 4348000, "cmf": 369580, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1536580, "o15": 105077, "o14": 123193, "o13": 153388, "o12": 213777, "o2c": 1169050, "o25": 114008, "o24": 133665, "o23": 166426, "o22": 231948}, {"b": "NISSAN", "u": "NAVARA", "v": "2.5 VE CALIBRE 4X2", "t": "MT", "s": 1320000, "d": 0, "dp": 264000, "la": 1056000, "cmf": 89760, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 388760, "o15": 25520, "o14": 29920, "o13": 37253, "o12": 51920, "o2c": 301050, "o25": 27689, "o24": 32463, "o23": 40420, "o22": 56333}, {"b": "NISSAN", "u": "NAVARA", "v": "2.5 VE CALIBRE 4X2", "t": "AT", "s": 1410000, "d": 0, "dp": 282000, "la": 1128000, "cmf": 95880, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 412880, "o15": 27260, "o14": 31960, "o13": 39793, "o12": 55460, "o2c": 319050, "o25": 29577, "o24": 34677, "o23": 43176, "o22": 60174}, {"b": "NISSAN", "u": "NAVARA", "v": "2.5 VL CALIBRE 4X2", "t": "AT", "s": 1560000, "d": 0, "dp": 312000, "la": 1248000, "cmf": 106080, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 458080, "o15": 30160, "o14": 35360, "o13": 44027, "o12": 61360, "o2c": 354050, "o25": 32724, "o24": 38366, "o23": 47769, "o22": 66576}, {"b": "NISSAN", "u": "NAVARA", "v": "2.5L CALIBRE-X", "t": "", "s": 1630000, "d": 0, "dp": 326000, "la": 1304000, "cmf": 110840, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 476840, "o15": 31513, "o14": 36947, "o13": 46002, "o12": 64113, "o2c": 368050, "o25": 34192, "o24": 40087, "o23": 49912, "o22": 69563}, {"b": "NISSAN", "u": "NAVARA", "v": "2.5 VL 4X4", "t": "MT", "s": 1660000, "d": 0, "dp": 332000, "la": 1328000, "cmf": 112880, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 484880, "o15": 32093, "o14": 37627, "o13": 46849, "o12": 65293, "o2c": 374050, "o25": 34821, "o24": 40825, "o23": 50831, "o22": 70843}, {"b": "NISSAN", "u": "NAVARA", "v": "2.5 VL 4X4", "t": "AT", "s": 1740000, "d": 0, "dp": 348000, "la": 1392000, "cmf": 118320, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 506320, "o15": 33640, "o14": 39440, "o13": 49107, "o12": 68440, "o2c": 390050, "o25": 36499, "o24": 42792, "o23": 53281, "o22": 74257}, {"b": "NISSAN", "u": "NAVARA", "v": "2.5 PRO 4X 4X4", "t": "AT", "s": 1850000, "d": 0, "dp": 370000, "la": 1480000, "cmf": 125800, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 535800, "o15": 35767, "o14": 41933, "o13": 52211, "o12": 72767, "o2c": 412050, "o25": 38807, "o24": 45498, "o23": 56649, "o22": 78952}, {"b": "NISSAN", "u": "URVAN", "v": "CARGO", "t": "MT", "s": 1260000, "d": 20000, "dp": 252000, "la": 1008000, "cmf": 85680, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 347680, "o15": 24360, "o14": 28560, "o13": 35560, "o12": 49560, "o2c": 264050, "o25": 26431, "o24": 30988, "o23": 38583, "o22": 53773}, {"b": "NISSAN", "u": "URVAN", "v": "CARGO CONVERTED", "t": "MT", "s": 1560000, "d": 10000, "dp": 312000, "la": 1248000, "cmf": 106080, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 0, "o15": 0, "o14": 0, "o13": 0, "o12": 0, "o2c": 0, "o25": 0, "o24": 0, "o23": 0, "o22": 0}, {"b": "NISSAN", "u": "URVAN", "v": "15 STR", "t": "STD", "s": 1560001, "d": 30000, "dp": 312000, "la": 1248001, "cmf": 106080, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 428080, "o15": 30160, "o14": 35360, "o13": 44027, "o12": 61360, "o2c": 324050, "o25": 32724, "o24": 38366, "o23": 47769, "o22": 66576}, {"b": "NISSAN", "u": "URVAN", "v": "18 STR", "t": "STD", "s": 1570000, "d": 0, "dp": 314000, "la": 1256000, "cmf": 106760, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 460760, "o15": 30353, "o14": 35587, "o13": 44309, "o12": 61753, "o2c": 356050, "o25": 32933, "o24": 38612, "o23": 48075, "o22": 67002}, {"b": "NISSAN", "u": "URVAN", "v": "PREMIUM", "t": "MT", "s": 2000000, "d": 60000, "dp": 400000, "la": 1600000, "cmf": 136000, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 516000, "o15": 38667, "o14": 45333, "o13": 56444, "o12": 78667, "o2c": 382050, "o25": 41953, "o24": 49187, "o23": 61242, "o22": 85353}, {"b": "NISSAN", "u": "URVAN", "v": "PREMIUM", "t": "AT", "s": 2060000, "d": 100000, "dp": 412000, "la": 1648000, "cmf": 140080, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 502080, "o15": 39827, "o14": 46693, "o13": 58138, "o12": 81027, "o2c": 364050, "o25": 43212, "o24": 50662, "o23": 63079, "o22": 87914}, {"b": "NISSAN", "u": "URVAN", "v": "CX", "t": "MT", "s": 1760000, "d": 100000, "dp": 352000, "la": 1408000, "cmf": 119680, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 0, "o15": 0, "o14": 0, "o13": 0, "o12": 0, "o2c": 0, "o25": 0, "o24": 0, "o23": 0, "o22": 0}, {"b": "NISSAN", "u": "URVAN", "v": "CX", "t": "AT", "s": 1820000, "d": 100000, "dp": 364000, "la": 1456000, "cmf": 123760, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 0, "o15": 0, "o14": 0, "o13": 0, "o12": 0, "o2c": 0, "o25": 0, "o24": 0, "o23": 0, "o22": 0}, {"b": "NISSAN", "u": "NISSAN Z", "v": "3.8 V6", "t": "MT", "s": 3888000, "d": 0, "dp": 777600, "la": 3110400, "cmf": 264384, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1121984, "o15": 75168, "o14": 88128, "o13": 109728, "o12": 152928, "o2c": 859650, "o25": 81557, "o24": 95619, "o23": 119055, "o22": 165927}, {"b": "NISSAN", "u": "NISSAN Z", "v": "3.8 V6", "t": "AT", "s": 3888000, "d": 0, "dp": 777600, "la": 3110400, "cmf": 264384, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1121984, "o15": 75168, "o14": 88128, "o13": 109728, "o12": 152928, "o2c": 859650, "o25": 81557, "o24": 95619, "o23": 119055, "o22": 165927}, {"b": "FORD", "u": "TERRITORY", "v": "TITANIUM X", "t": "AT", "s": 1599000, "d": 0, "dp": 319800, "la": 1279200, "cmf": 108732, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 468532, "o15": 30914, "o14": 36244, "o13": 45127, "o12": 62894, "o2c": 361850, "o25": 33542, "o24": 39325, "o23": 48963, "o22": 68240}, {"b": "FORD", "u": "TERRITORY", "v": "TREND", "t": "AT", "s": 1399000, "d": 0, "dp": 279800, "la": 1119200, "cmf": 95132, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 409932, "o15": 27047, "o14": 31711, "o13": 39483, "o12": 55027, "o2c": 316850, "o25": 29346, "o24": 34406, "o23": 42839, "o22": 59705}, {"b": "FORD", "u": "RAPTOR", "v": "3.0L V6 4X4", "t": "AT", "s": 2799000, "d": 0, "dp": 559800, "la": 2239200, "cmf": 190332, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 830132, "o15": 54114, "o14": 63444, "o13": 78994, "o12": 110094, "o2c": 641850, "o25": 58714, "o24": 68837, "o23": 85708, "o22": 119452}, {"b": "FORD", "u": "RAPTOR", "v": "3.0L V6 4X4", "t": "AT", "s": 2819000, "d": 0, "dp": 563800, "la": 2255200, "cmf": 191692, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 835492, "o15": 54501, "o14": 63897, "o13": 79558, "o12": 110881, "o2c": 645850, "o25": 59133, "o24": 69329, "o23": 86321, "o22": 120306}, {"b": "FORD", "u": "RAPTOR", "v": "2.0L 4X4", "t": "AT", "s": 2645000, "d": 0, "dp": 529000, "la": 2116000, "cmf": 179860, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 788860, "o15": 51137, "o14": 59953, "o13": 74648, "o12": 104037, "o2c": 611050, "o25": 55483, "o24": 65049, "o23": 80993, "o22": 112880}, {"b": "FORD", "u": "RAPTOR", "v": "2.0L 4X4", "t": "AT", "s": 2665000, "d": 0, "dp": 533000, "la": 2132000, "cmf": 181220, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 794220, "o15": 51523, "o14": 60407, "o13": 75212, "o12": 104823, "o2c": 615050, "o25": 55903, "o24": 65541, "o23": 81605, "o22": 113733}, {"b": "FORD", "u": "RANGER", "v": "2.0L BI-TURBO  4X4", "t": "AT", "s": 2390000, "d": 0, "dp": 478000, "la": 1912000, "cmf": 162520, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 690520, "o15": 46207, "o14": 54173, "o13": 67451, "o12": 94007, "o2c": 530050, "o25": 50134, "o24": 58778, "o23": 73184, "o22": 101997}, {"b": "FORD", "u": "RANGER", "v": "2.0L BI-TURBO 4X4", "t": "AT", "s": 2355000, "d": 0, "dp": 471000, "la": 1884000, "cmf": 160140, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 681140, "o15": 45530, "o14": 53380, "o13": 66463, "o12": 92630, "o2c": 523050, "o25": 49400, "o24": 57917, "o23": 72113, "o22": 100504}, {"b": "FORD", "u": "RANGER", "v": "2.0L TURBO 4X2", "t": "AT", "s": 1999000, "d": 0, "dp": 399800, "la": 1599200, "cmf": 135932, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 575732, "o15": 38647, "o14": 45311, "o13": 56416, "o12": 78627, "o2c": 441850, "o25": 41932, "o24": 49162, "o23": 61212, "o22": 85311}, {"b": "FORD", "u": "RANGER", "v": "2.0L TURBO 4X2", "t": "AT", "s": 1964000, "d": 0, "dp": 392800, "la": 1571200, "cmf": 133552, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 566352, "o15": 37971, "o14": 44517, "o13": 55428, "o12": 77251, "o2c": 434850, "o25": 41198, "o24": 48301, "o23": 60140, "o22": 83817}, {"b": "FORD", "u": "RANGER", "v": "2.0L  TURBO 4X4", "t": "AT", "s": 1964000, "d": 0, "dp": 392800, "la": 1571200, "cmf": 133552, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 566352, "o15": 37971, "o14": 44517, "o13": 55428, "o12": 77251, "o2c": 434850, "o25": 41198, "o24": 48301, "o23": 60140, "o22": 83817}, {"b": "FORD", "u": "RANGER", "v": "2.0L  TURBO 4X2", "t": "AT", "s": 1820000, "d": 0, "dp": 364000, "la": 1456000, "cmf": 123760, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 527760, "o15": 35187, "o14": 41253, "o13": 51364, "o12": 71587, "o2c": 406050, "o25": 38178, "o24": 44760, "o23": 55730, "o22": 77672}, {"b": "FORD", "u": "RANGER", "v": "2.0L TURBO 4X2", "t": "AT", "s": 1694000, "d": 0, "dp": 338800, "la": 1355200, "cmf": 115192, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 493992, "o15": 32751, "o14": 38397, "o13": 47808, "o12": 66631, "o2c": 380850, "o25": 35534, "o24": 41661, "o23": 51872, "o22": 72294}, {"b": "FORD", "u": "RANGER", "v": "2.0L TURBO 4X4", "t": "MT", "s": 1523000, "d": 0, "dp": 304600, "la": 1218400, "cmf": 103564, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 448164, "o15": 29445, "o14": 34521, "o13": 42982, "o12": 59905, "o2c": 346650, "o25": 31947, "o24": 37456, "o23": 46636, "o22": 64997}, {"b": "FORD", "u": "EVEREST", "v": "2.0L BI-TURBO 4X4", "t": "AT", "s": 2590000, "d": 0, "dp": 518000, "la": 2072000, "cmf": 176120, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 774120, "o15": 50073, "o14": 58707, "o13": 73096, "o12": 101873, "o2c": 600050, "o25": 54330, "o24": 63697, "o23": 79309, "o22": 110533}, {"b": "FORD", "u": "EVEREST", "v": "2.0L BI-TURBO 4X4", "t": "AT", "s": 2610000, "d": 0, "dp": 522000, "la": 2088000, "cmf": 177480, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 779480, "o15": 50460, "o14": 59160, "o13": 73660, "o12": 102660, "o2c": 604050, "o25": 54749, "o24": 64189, "o23": 79921, "o22": 111386}, {"b": "FORD", "u": "EVEREST", "v": "2.0L TURBO 4X2", "t": "AT", "s": 2294000, "d": 0, "dp": 458800, "la": 1835200, "cmf": 155992, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 664792, "o15": 44351, "o14": 51997, "o13": 64742, "o12": 90231, "o2c": 510850, "o25": 48120, "o24": 56417, "o23": 70245, "o22": 97900}, {"b": "FORD", "u": "EVEREST", "v": "2.0L TURBO 4X2", "t": "AT", "s": 2314000, "d": 0, "dp": 462800, "la": 1851200, "cmf": 157352, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 670152, "o15": 44737, "o14": 52451, "o13": 65306, "o12": 91017, "o2c": 514850, "o25": 48540, "o24": 56909, "o23": 70857, "o22": 98754}, {"b": "FORD", "u": "EVEREST", "v": "2.0L TURBO 4X2", "t": "AT", "s": 2174000, "d": 0, "dp": 434800, "la": 1739200, "cmf": 147832, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 632632, "o15": 42031, "o14": 49277, "o13": 61355, "o12": 85511, "o2c": 486850, "o25": 45603, "o24": 53466, "o23": 66570, "o22": 92779}, {"b": "FORD", "u": "EVEREST", "v": "2.0L TURBO 4X2", "t": "AT", "s": 2194000, "d": 0, "dp": 438800, "la": 1755200, "cmf": 149192, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 637992, "o15": 42417, "o14": 49731, "o13": 61920, "o12": 86297, "o2c": 490850, "o25": 46023, "o24": 53958, "o23": 67183, "o22": 93633}, {"b": "FORD", "u": "EVEREST", "v": "2.0L TURBO 4X2", "t": "AT", "s": 1864000, "d": 100000, "dp": 372800, "la": 1491200, "cmf": 126752, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 439552, "o15": 36037, "o14": 42251, "o13": 52606, "o12": 73317, "o2c": 314850, "o25": 39101, "o24": 45842, "o23": 57078, "o22": 79549}, {"b": "FORD", "u": "EXPLORER", "v": "2.3L LIMITED", "t": "", "s": 3498000, "d": 0, "dp": 699600, "la": 2798400, "cmf": 237864, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1017464, "o15": 67628, "o14": 79288, "o13": 98721, "o12": 137588, "o2c": 781650, "o25": 73376, "o24": 86027, "o23": 107113, "o22": 149283}, {"b": "FORD", "u": "MUSTANG", "v": "5.0L V8", "t": "AT", "s": 4006000, "d": 0, "dp": 801200, "la": 3204800, "cmf": 272408, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1153608, "o15": 77449, "o14": 90803, "o13": 113058, "o12": 157569, "o2c": 883250, "o25": 84033, "o24": 98521, "o23": 122668, "o22": 170963}, {"b": "FORD", "u": "MUSTANG", "v": "2.3L", "t": "AT", "s": 3506000, "d": 0, "dp": 701200, "la": 2804800, "cmf": 238408, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1019608, "o15": 67783, "o14": 79469, "o13": 98947, "o12": 137903, "o2c": 783250, "o25": 73544, "o24": 86224, "o23": 107358, "o22": 149624}, {"b": "FORD", "u": "BRONCO", "v": "2.7L V6", "t": "", "s": 4998000, "d": 0, "dp": 999600, "la": 3998400, "cmf": 339864, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1419464, "o15": 96628, "o14": 113288, "o13": 141055, "o12": 196588, "o2c": 1081650, "o25": 104841, "o24": 122917, "o23": 153044, "o22": 213298}, {"b": "BYD", "u": "SEAGULL", "v": "EV", "t": "", "s": 938000, "d": 0, "dp": 187600, "la": 750400, "cmf": 63784, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 279384, "o15": 18135, "o14": 21261, "o13": 26472, "o12": 36895, "o2c": 217650, "o25": 19676, "o24": 23069, "o23": 28723, "o22": 40031}, {"b": "BYD", "u": "DOLPHIN", "v": "EV", "t": "", "s": 1398000, "d": 0, "dp": 279600, "la": 1118400, "cmf": 95064, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 409664, "o15": 27028, "o14": 31688, "o13": 39455, "o12": 54988, "o2c": 316650, "o25": 29325, "o24": 34381, "o23": 42808, "o22": 59662}, {"b": "BYD", "u": "ATTO", "v": "3 EV", "t": "", "s": 1798000, "d": 0, "dp": 359600, "la": 1438400, "cmf": 122264, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 521864, "o15": 34761, "o14": 40755, "o13": 50744, "o12": 70721, "o2c": 401650, "o25": 37716, "o24": 44219, "o23": 55057, "o22": 76733}, {"b": "BYD", "u": "SEAL", "v": "EV", "t": "", "s": 2548000, "d": 0, "dp": 509600, "la": 2038400, "cmf": 173264, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 762864, "o15": 49261, "o14": 57755, "o13": 71910, "o12": 100221, "o2c": 591650, "o25": 53449, "o24": 62664, "o23": 78023, "o22": 108740}, {"b": "BYD", "u": "HAN", "v": "EV", "t": "", "s": 3113000, "d": 0, "dp": 622600, "la": 2490400, "cmf": 211684, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 914284, "o15": 60185, "o14": 70561, "o13": 87856, "o12": 122445, "o2c": 704650, "o25": 65300, "o24": 76559, "o23": 95324, "o22": 132852}, {"b": "BYD", "u": "TANG", "v": "EV", "t": "", "s": 3321000, "d": 0, "dp": 664200, "la": 2656800, "cmf": 225828, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 970028, "o15": 64206, "o14": 75276, "o13": 93726, "o12": 130626, "o2c": 746250, "o25": 69664, "o24": 81674, "o23": 101693, "o22": 141729}, {"b": "BYD", "u": "SEALION", "v": "6 DM-i", "t": "", "s": 1548000, "d": 0, "dp": 309600, "la": 1238400, "cmf": 105264, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 454864, "o15": 29928, "o14": 35088, "o13": 43688, "o12": 60888, "o2c": 351650, "o25": 32472, "o24": 38070, "o23": 47401, "o22": 66063}, {"b": "BYD", "u": "SEAL", "v": "5 DM-i DYNAMIC", "t": "", "s": 948000, "d": 0, "dp": 189600, "la": 758400, "cmf": 64464, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 282064, "o15": 18328, "o14": 21488, "o13": 26755, "o12": 37288, "o2c": 219650, "o25": 19886, "o24": 23314, "o23": 29029, "o22": 40457}, {"b": "BYD", "u": "SEAL", "v": "5 DM-i PREMIUM", "t": "", "s": 1198000, "d": 0, "dp": 239600, "la": 958400, "cmf": 81464, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351064, "o15": 23161, "o14": 27155, "o13": 33810, "o12": 47121, "o2c": 271650, "o25": 25130, "o24": 29463, "o23": 36684, "o22": 51127}, {"b": "SUZUKI", "u": "FRONX", "v": "GL", "t": "AT", "s": 1059000, "d": 0, "dp": 211800, "la": 847200, "cmf": 72012, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 313812, "o15": 20474, "o14": 24004, "o13": 29887, "o12": 41654, "o2c": 243850, "o25": 22214, "o24": 26044, "o23": 32428, "o22": 45195}, {"b": "SUZUKI", "u": "FRONX", "v": "GLX", "t": "AT", "s": 1219000, "d": 0, "dp": 243800, "la": 975200, "cmf": 82892, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 356692, "o15": 23567, "o14": 27631, "o13": 34403, "o12": 47947, "o2c": 275850, "o25": 25571, "o24": 29979, "o23": 37327, "o22": 52023}, {"b": "SUZUKI", "u": "FRONX", "v": "GLX", "t": "AT (TWO-TONE)", "s": 1229000, "d": 0, "dp": 245800, "la": 983200, "cmf": 83572, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 359372, "o15": 23761, "o14": 27857, "o13": 34685, "o12": 48341, "o2c": 277850, "o25": 25780, "o24": 30225, "o23": 37633, "o22": 52450}, {"b": "SUZUKI", "u": "FRONX", "v": "SGX", "t": "AT", "s": 1299000, "d": 0, "dp": 259800, "la": 1039200, "cmf": 88332, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 378132, "o15": 25114, "o14": 29444, "o13": 36661, "o12": 51094, "o2c": 291850, "o25": 27249, "o24": 31947, "o23": 39777, "o22": 55437}, {"b": "SUZUKI", "u": "DZIRE", "v": "GL", "t": "CVT", "s": 920000, "d": 0, "dp": 184000, "la": 736000, "cmf": 62560, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 274560, "o15": 17787, "o14": 20853, "o13": 25964, "o12": 36187, "o2c": 214050, "o25": 19299, "o24": 22626, "o23": 28171, "o22": 39263}, {"b": "SUZUKI", "u": "DZIRE", "v": "GLX", "t": "CVT", "s": 998000, "d": 0, "dp": 199600, "la": 798400, "cmf": 67864, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 295464, "o15": 19295, "o14": 22621, "o13": 28166, "o12": 39255, "o2c": 229650, "o25": 20935, "o24": 24544, "o23": 30560, "o22": 42591}, {"b": "SUZUKI", "u": "CARRY", "v": "CAB & CHASIS", "t": "", "s": 614000, "d": 0, "dp": 122800, "la": 491200, "cmf": 41752, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 189552, "o15": 11871, "o14": 13917, "o13": 17328, "o12": 24151, "o2c": 149850, "o25": 12880, "o24": 15100, "o23": 18801, "o22": 26203}, {"b": "SUZUKI", "u": "CARRY", "v": "TRUCK", "t": "", "s": 650000, "d": 0, "dp": 130000, "la": 520000, "cmf": 44200, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 199200, "o15": 12567, "o14": 14733, "o13": 18344, "o12": 25567, "o2c": 157050, "o25": 13635, "o24": 15986, "o23": 19904, "o22": 27740}, {"b": "SUZUKI", "u": "CARRY", "v": "CARGO VAN", "t": "", "s": 705000, "d": 0, "dp": 141000, "la": 564000, "cmf": 47940, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 213940, "o15": 13630, "o14": 15980, "o13": 19897, "o12": 27730, "o2c": 168050, "o25": 14789, "o24": 17338, "o23": 21588, "o22": 30087}, {"b": "SUZUKI", "u": "CARRY", "v": "UTILITY VAN", "t": "", "s": 754000, "d": 0, "dp": 150800, "la": 603200, "cmf": 51272, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 227072, "o15": 14577, "o14": 17091, "o13": 21280, "o12": 29657, "o2c": 177850, "o25": 15816, "o24": 18543, "o23": 23088, "o22": 32178}, {"b": "SUZUKI", "u": "APV", "v": "GA 1.6L", "t": "MT", "s": 763000, "d": 0, "dp": 152600, "la": 610400, "cmf": 51884, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 229484, "o15": 14751, "o14": 17295, "o13": 21534, "o12": 30011, "o2c": 179650, "o25": 16005, "o24": 18765, "o23": 23364, "o22": 32562}, {"b": "SUZUKI", "u": "APV", "v": "GLX 1.6L", "t": "MT", "s": 975000, "d": 0, "dp": 195000, "la": 780000, "cmf": 66300, "pf": 2050, "lto": 0, "ins": 28000, "o1c": 289300, "o15": 18850, "o14": 22100, "o13": 27517, "o12": 38350, "o2c": 225050, "o25": 20452, "o24": 23978, "o23": 29856, "o22": 41610}, {"b": "SUZUKI", "u": "JIMNY", "v": "GL 1.5L 4X4", "t": "MT", "s": 1293000, "d": 0, "dp": 258600, "la": 1034400, "cmf": 87924, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 376524, "o15": 24998, "o14": 29308, "o13": 36491, "o12": 50858, "o2c": 290650, "o25": 27123, "o24": 31799, "o23": 39593, "o22": 55181}, {"b": "SUZUKI", "u": "JIMNY", "v": "GLX  1.5L 4X4", "t": "MONOTONE", "s": 1355000, "d": 0, "dp": 271000, "la": 1084000, "cmf": 92140, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 398140, "o15": 26197, "o14": 30713, "o13": 38241, "o12": 53297, "o2c": 308050, "o25": 28423, "o24": 33324, "o23": 41492, "o22": 57827}, {"b": "SUZUKI", "u": "JIMNY", "v": "GLX  1.5L 4X4", "t": "TWO-TONE", "s": 1365000, "d": 0, "dp": 273000, "la": 1092000, "cmf": 92820, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 400820, "o15": 26390, "o14": 30940, "o13": 38523, "o12": 53690, "o2c": 310050, "o25": 28633, "o24": 33570, "o23": 41798, "o22": 58254}, {"b": "SUZUKI", "u": "JIMNY", "v": "GLX 5DR 1.5L 4X4", "t": "MT", "s": 1558000, "d": 0, "dp": 311600, "la": 1246400, "cmf": 105944, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 457544, "o15": 30121, "o14": 35315, "o13": 43970, "o12": 61281, "o2c": 353650, "o25": 32682, "o24": 38316, "o23": 47708, "o22": 66490}, {"b": "SUZUKI", "u": "JIMNY", "v": "GLX 5DR 1.5L 4X4", "t": "AT (MONO)", "s": 1698000, "d": 0, "dp": 339600, "la": 1358400, "cmf": 115464, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 495064, "o15": 32828, "o14": 38488, "o13": 47921, "o12": 66788, "o2c": 381650, "o25": 35618, "o24": 41759, "o23": 51995, "o22": 72465}, {"b": "SUZUKI", "u": "JIMNY", "v": "GLX 5DR 1.5L 4X4", "t": "AT", "s": 1708000, "d": 0, "dp": 341600, "la": 1366400, "cmf": 116144, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 497744, "o15": 33021, "o14": 38715, "o13": 48204, "o12": 67181, "o2c": 383650, "o25": 35828, "o24": 42005, "o23": 52301, "o22": 72892}, {"b": "SUZUKI", "u": "ERTIGA", "v": "GLX 1.5L", "t": "AT", "s": 1178000, "d": 0, "dp": 235600, "la": 942400, "cmf": 80104, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 345704, "o15": 22775, "o14": 26701, "o13": 33246, "o12": 46335, "o2c": 267650, "o25": 24711, "o24": 28971, "o23": 36072, "o22": 50273}, {"b": "SUZUKI", "u": "SPRESSO", "v": "GL", "t": "MT MC", "s": 634000, "d": 0, "dp": 126800, "la": 507200, "cmf": 43112, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 194912, "o15": 12257, "o14": 14371, "o13": 17893, "o12": 24937, "o2c": 153850, "o25": 13299, "o24": 15592, "o23": 19414, "o22": 27057}, {"b": "SUZUKI", "u": "SPRESSO", "v": "GL", "t": "AGS MC", "s": 674000, "d": 0, "dp": 134800, "la": 539200, "cmf": 45832, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 205632, "o15": 13031, "o14": 15277, "o13": 19022, "o12": 26511, "o2c": 161850, "o25": 14138, "o24": 16576, "o23": 20639, "o22": 28764}, {"b": "SUZUKI", "u": "XL7", "v": "GLX", "t": "AT (TWO)", "s": 1269000, "d": 0, "dp": 253800, "la": 1015200, "cmf": 86292, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 370092, "o15": 24534, "o14": 28764, "o13": 35814, "o12": 49914, "o2c": 285850, "o25": 26619, "o24": 31209, "o23": 38858, "o22": 54157}, {"b": "SUZUKI", "u": "XL7", "v": "GLX", "t": "AT (MONO)", "s": 1259000, "d": 0, "dp": 251800, "la": 1007200, "cmf": 85612, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 367412, "o15": 24341, "o14": 28537, "o13": 35532, "o12": 49521, "o2c": 283850, "o25": 26410, "o24": 30963, "o23": 38552, "o22": 53730}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GL", "t": "IVT", "s": 1068000, "d": 0, "dp": 213600, "la": 854400, "cmf": 72624, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 316224, "o15": 20648, "o14": 24208, "o13": 30141, "o12": 42008, "o2c": 245650, "o25": 22403, "o24": 26266, "o23": 32703, "o22": 45579}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS", "t": "IVT", "s": 1198000, "d": 0, "dp": 239600, "la": 958400, "cmf": 81464, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 351064, "o15": 23161, "o14": 27155, "o13": 33810, "o12": 47121, "o2c": 271650, "o25": 25130, "o24": 29463, "o23": 36684, "o22": 51127}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS PREMIUM", "t": "IVT 7STR", "s": 1288000, "d": 0, "dp": 257600, "la": 1030400, "cmf": 87584, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 375184, "o15": 24901, "o14": 29195, "o13": 36350, "o12": 50661, "o2c": 289650, "o25": 27018, "o24": 31676, "o23": 39440, "o22": 54968}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS PREMIUM", "t": "IVT 6STR", "s": 1296000, "d": 0, "dp": 259200, "la": 1036800, "cmf": 88128, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 377328, "o15": 25056, "o14": 29376, "o13": 36576, "o12": 50976, "o2c": 291250, "o25": 27186, "o24": 31873, "o23": 39685, "o22": 55309}, {"b": "HYUNDAI", "u": "STARGAZER", "v": "1.5 GLS X", "t": "IVT", "s": 1348000, "d": 0, "dp": 269600, "la": 1078400, "cmf": 91664, "pf": 2050, "lto": 0, "ins": 25000, "o1c": 386264, "o15": 26061, "o14": 30555, "o13": 38044, "o12": 53021, "o2c": 296650, "o25": 28277, "o24": 33152, "o23": 41277, "o22": 57528}, {"b": "HYUNDAI", "u": "CUSTIN", "v": "1.5T GLS", "t": "8AT", "s": 1770000, "d": 0, "dp": 354000, "la": 1416000, "cmf": 120360, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 514360, "o15": 34220, "o14": 40120, "o13": 49953, "o12": 69620, "o2c": 396050, "o25": 37129, "o24": 43530, "o23": 54199, "o22": 75538}, {"b": "HYUNDAI", "u": "CUSTIN", "v": "1.5T PREMIUM", "t": "8AT", "s": 2080000, "d": 0, "dp": 416000, "la": 1664000, "cmf": 141440, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 607440, "o15": 40213, "o14": 47147, "o13": 58702, "o12": 81813, "o2c": 468050, "o25": 43631, "o24": 51154, "o23": 63692, "o22": 88767}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5 GL", "t": "IVT STD", "s": 1098000, "d": 0, "dp": 219600, "la": 878400, "cmf": 74664, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 324264, "o15": 21228, "o14": 24888, "o13": 30988, "o12": 43188, "o2c": 251650, "o25": 23032, "o24": 27003, "o23": 33622, "o22": 46859}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5 GL", "t": "IVT", "s": 1228000, "d": 0, "dp": 245600, "la": 982400, "cmf": 83504, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 359104, "o15": 23741, "o14": 27835, "o13": 34657, "o12": 48301, "o2c": 277650, "o25": 25759, "o24": 30201, "o23": 37603, "o22": 52407}, {"b": "HYUNDAI", "u": "CRETA", "v": "1.5 GLS", "t": "IVT", "s": 1388000, "d": 0, "dp": 277600, "la": 1110400, "cmf": 94384, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 406984, "o15": 26835, "o14": 31461, "o13": 39172, "o12": 54595, "o2c": 314650, "o25": 29116, "o24": 34136, "o23": 42502, "o22": 59235}, {"b": "HYUNDAI", "u": "TUCSON", "v": "2.0 GLS", "t": "AT", "s": 1680000, "d": 0, "dp": 336000, "la": 1344000, "cmf": 114240, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 490240, "o15": 32480, "o14": 38080, "o13": 47413, "o12": 66080, "o2c": 378050, "o25": 35241, "o24": 41317, "o23": 51443, "o22": 71697}, {"b": "HYUNDAI", "u": "TUCSON", "v": "2.0 GLS+", "t": "AT", "s": 1980000, "d": 0, "dp": 396000, "la": 1584000, "cmf": 134640, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 570640, "o15": 38280, "o14": 44880, "o13": 55880, "o12": 77880, "o2c": 438050, "o25": 41534, "o24": 48695, "o23": 60630, "o22": 84500}, {"b": "HYUNDAI", "u": "TUCSON", "v": "1.6T", "t": "HEV", "s": 2290000, "d": 0, "dp": 458000, "la": 1832000, "cmf": 155720, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 663720, "o15": 44273, "o14": 51907, "o13": 64629, "o12": 90073, "o2c": 510050, "o25": 48037, "o24": 56319, "o23": 70122, "o22": 97730}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5 GLS", "t": "8AT 2WD", "s": 2410000, "d": 0, "dp": 482000, "la": 1928000, "cmf": 163880, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 695880, "o15": 46593, "o14": 54627, "o13": 68016, "o12": 94793, "o2c": 534050, "o25": 50554, "o24": 59270, "o23": 73797, "o22": 102851}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5 GLS", "t": "8AT 2WD", "s": 2540000, "d": 0, "dp": 508000, "la": 2032000, "cmf": 172720, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 760720, "o15": 49107, "o14": 57573, "o13": 71684, "o12": 99907, "o2c": 590050, "o25": 53281, "o24": 62467, "o23": 77778, "o22": 108399}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "2.5T CALLIGRAPHY", "t": "AWD 8DCT", "s": 3100000, "d": 0, "dp": 620000, "la": 2480000, "cmf": 210800, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 910800, "o15": 59933, "o14": 70267, "o13": 87489, "o12": 121933, "o2c": 702050, "o25": 65028, "o24": 76239, "o23": 94925, "o22": 132298}, {"b": "HYUNDAI", "u": "SANTA FE", "v": "1.6T CALLIGRAPHY", "t": "HEV", "s": 3330000, "d": 0, "dp": 666000, "la": 2664000, "cmf": 226440, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 972440, "o15": 64380, "o14": 75480, "o13": 93980, "o12": 130980, "o2c": 748050, "o25": 69852, "o24": 81896, "o23": 101968, "o22": 142113}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI", "t": "6MT", "s": 1560000, "d": 0, "dp": 312000, "la": 1248000, "cmf": 106080, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 458080, "o15": 30160, "o14": 35360, "o13": 44027, "o12": 61360, "o2c": 354050, "o25": 32724, "o24": 38366, "o23": 47769, "o22": 66576}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI", "t": "6MT", "s": 1850000, "d": 0, "dp": 370000, "la": 1480000, "cmf": 125800, "pf": 2050, "lto": 0, "ins": 40000, "o1c": 535800, "o15": 35767, "o14": 41933, "o13": 52211, "o12": 72767, "o2c": 412050, "o25": 38807, "o24": 45498, "o23": 56649, "o22": 78952}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI GLS+", "t": "8AT", "s": 2320000, "d": 0, "dp": 464000, "la": 1856000, "cmf": 157760, "pf": 2050, "lto": 0, "ins": 50000, "o1c": 671760, "o15": 44853, "o14": 52587, "o13": 65476, "o12": 91253, "o2c": 516050, "o25": 48666, "o24": 57057, "o23": 71041, "o22": 99010}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI PREMIUM", "t": "AWD 9STR", "s": 2830000, "d": 0, "dp": 566000, "la": 2264000, "cmf": 192440, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 838440, "o15": 54713, "o14": 64147, "o13": 79869, "o12": 111313, "o2c": 648050, "o25": 59364, "o24": 69599, "o23": 86658, "o22": 120775}, {"b": "HYUNDAI", "u": "STARIA", "v": "2.2 CRDI PREMIUM", "t": "AWD 7STR", "s": 3030000, "d": 0, "dp": 606000, "la": 2424000, "cmf": 206040, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 892040, "o15": 58580, "o14": 68680, "o13": 85513, "o12": 119180, "o2c": 688050, "o25": 63559, "o24": 74518, "o23": 92782, "o22": 129310}, {"b": "HYUNDAI", "u": "PALISADE", "v": "2.2 DIESEL", "t": "8AT 4WD", "s": 3780000, "d": 0, "dp": 756000, "la": 3024000, "cmf": 257040, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1093040, "o15": 73080, "o14": 85680, "o13": 106680, "o12": 148680, "o2c": 838050, "o25": 79292, "o24": 92963, "o23": 115748, "o22": 161318}, {"b": "HYUNDAI", "u": "IONIQ 5", "v": "EV GLS", "t": "2WD S", "s": 3068000, "d": 0, "dp": 613600, "la": 2454400, "cmf": 208624, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 902224, "o15": 59315, "o14": 69541, "o13": 86586, "o12": 120675, "o2c": 695650, "o25": 64356, "o24": 75452, "o23": 93946, "o22": 130932}, {"b": "HYUNDAI", "u": "IONIQ 5", "v": "EV GLS", "t": "2WD L", "s": 3698000, "d": 0, "dp": 739600, "la": 2958400, "cmf": 251464, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1071064, "o15": 71495, "o14": 83821, "o13": 104366, "o12": 145455, "o2c": 821650, "o25": 77572, "o24": 90946, "o23": 113237, "o22": 157818}, {"b": "HYUNDAI", "u": "IONIQ 6", "v": "EV GLS", "t": "2WD", "s": 3798000, "d": 0, "dp": 759600, "la": 3038400, "cmf": 258264, "pf": 2050, "lto": 0, "ins": 80000, "o1c": 1097864, "o15": 73428, "o14": 86088, "o13": 107188, "o12": 149388, "o2c": 841650, "o25": 79669, "o24": 93405, "o23": 116299, "o22": 162086}, {"b": "HYUNDAI", "u": "H100", "v": "2.5 CRDI", "t": "6MT C&C", "s": 1024000, "d": 0, "dp": 204800, "la": 819200, "cmf": 69632, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 304432, "o15": 19797, "o14": 23211, "o13": 28900, "o12": 40277, "o2c": 236850, "o25": 21480, "o24": 25184, "o23": 31356, "o22": 43701}, {"b": "HYUNDAI", "u": "H101", "v": "2.5 CRDI", "t": "6MT SHUTTLE", "s": 1243000, "d": 0, "dp": 248600, "la": 994400, "cmf": 84524, "pf": 2050, "lto": 0, "ins": 30000, "o1c": 363124, "o15": 24031, "o14": 28175, "o13": 35080, "o12": 48891, "o2c": 280650, "o25": 26074, "o24": 30570, "o23": 38062, "o22": 53047}, {"b": "JMC", "u": "JMH", "v": "FB BODY DUAL AC", "t": "MT", "s": 1200000, "d": 202000, "dp": 360000, "la": 840000, "cmf": 71400, "pf": 2050, "lto": 0, "ins": 35000, "o1c": 264400, "o15": 20300, "o14": 23800, "o13": 29633, "o12": 41300, "o2c": 0, "o25": 0, "o24": 0, "o23": 0, "o22": 0}];

const INSURANCE = [
  {min:500000,max:800000,amt:25000},
  {min:800001,max:1000000,amt:28000},
  {min:1000001,max:1300000,amt:30000},
  {min:1300001,max:1500000,amt:35000},
  {min:1500001,max:2000000,amt:40000},
  {min:2000001,max:2500000,amt:50000},
  {min:2500001,max:3000000,amt:80000},
];

// CMF rate constant: ~8.5% per year on loan amount (based on data pattern)
const CMF_RATE = 0.085;
const PF_DEFAULT = 2050;

let currentStep = 1;
let selectedOption = 1;
let selectedTerm = null;
let mode = 'catalog'; // 'catalog' or 'manual'

// Init brands
function init(){
  const brands = [...new Set(DATA.map(v=>v.b))].sort();
  const sel = document.getElementById('brandSelect');
  brands.forEach(b=>{
    const o = document.createElement('option');
    o.value = b; o.textContent = b;
    sel.appendChild(o);
  });
}
init();

// Mode switching
function switchMode(m){
  mode = m;
  document.getElementById('modeCatalog').classList.toggle('active', m==='catalog');
  document.getElementById('modeManual').classList.toggle('active', m==='manual');
  currentStep = 1;
  selectedTerm = null;
  updateStepper();
  hideAllSteps();
  if(m==='catalog'){
    document.getElementById('step1').classList.remove('hidden');
  } else {
    document.getElementById('step1m').classList.remove('hidden');
  }
}

function hideAllSteps(){
  ['step1','step1m','step2','step3','step4'].forEach(id=>{
    document.getElementById(id).classList.add('hidden');
  });
}

// Stepper
function updateStepper(){
  document.querySelectorAll('.stepper-seg').forEach(seg=>{
    const s = parseInt(seg.dataset.step);
    seg.classList.remove('active','done');
    if(s===currentStep) seg.classList.add('active');
    else if(s<currentStep) seg.classList.add('done');
    // Update dot
    const num = seg.querySelector('.seg-num');
    if(s<currentStep){
      num.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>';
    } else {
      num.textContent = s;
    }
  });
}

function clickStep(s){
  // Only allow clicking completed steps or current
  if(s < currentStep) goStep(s);
}

// Catalog helpers
function onBrandChange(){
  const brand = document.getElementById('brandSelect').value;
  const unitSel = document.getElementById('unitSelect');
  const varSel = document.getElementById('variantSelect');
  unitSel.innerHTML = '<option value="">Choose a model</option>';
  varSel.innerHTML = '<option value="">Choose model first</option>';
  if(!brand) return;
  const units = [...new Set(DATA.filter(v=>v.b===brand).map(v=>v.u))].sort();
  units.forEach(u=>{
    const o = document.createElement('option');
    o.value = u; o.textContent = u;
    unitSel.appendChild(o);
  });
}

function onUnitChange(){
  const brand = document.getElementById('brandSelect').value;
  const unit = document.getElementById('unitSelect').value;
  const varSel = document.getElementById('variantSelect');
  varSel.innerHTML = '<option value="">Choose a variant</option>';
  if(!unit) return;
  const variants = DATA.filter(v=>v.b===brand && v.u===unit);
  variants.forEach((v,i)=>{
    const o = document.createElement('option');
    o.value = i;
    o.textContent = `${v.v} ${v.t} — SRP: ${fmt(v.s)}`;
    varSel.appendChild(o);
  });
}

function getCatalogSelected(){
  const brand = document.getElementById('brandSelect').value;
  const unit = document.getElementById('unitSelect').value;
  const idx = document.getElementById('variantSelect').value;
  if(!brand||!unit||idx==='') return null;
  const variants = DATA.filter(v=>v.b===brand && v.u===unit);
  return variants[parseInt(idx)] || null;
}

function getInsurance(srp){
  for(const r of INSURANCE){
    if(srp >= r.min && srp <= r.max) return r.amt;
  }
  return srp > 3000000 ? 80000 : 25000;
}

// Build a virtual vehicle object from manual inputs
function getManualVehicle(){
  const srp = parseFloat(document.getElementById('manualSRP').value) || 0;
  const disc = 0;
  const model = document.getElementById('manualModel').value || 'Custom Vehicle';
  if(srp < 100000) return null;

  const dp = Math.round(srp * 0.2);
  const la = Math.round(srp * 0.8);
  const cmf = Math.round(la * CMF_RATE);
  const pf = PF_DEFAULT;
  const ins = getInsurance(srp);
  const lto = 0;

  // Option 1: Pay-All (CMF included in cashout)
  const o1c = dp + ins + cmf + pf + lto;
  const o1_5 = Math.round(la / 60);
  const o1_4 = Math.round(la / 48);
  const o1_3 = Math.round(la / 36);
  const o1_2 = Math.round(la / 24);

  // Option 2: CMF spread to monthly
  const o2c = dp + ins + pf + lto;
  const cmfMo = cmf; // total CMF spread
  const o2_5 = Math.round(la / 60 + cmfMo / 60);
  const o2_4 = Math.round(la / 48 + cmfMo / 48);
  const o2_3 = Math.round(la / 36 + cmfMo / 36);
  const o2_2 = Math.round(la / 24 + cmfMo / 24);

  return {
    b: '', u: model, v: '', t: '',
    s: srp, d: disc, dp, la, cmf, pf, lto, ins,
    o1c, o15: o1_5, o14: o1_4, o13: o1_3, o12: o1_2,
    o2c, o25: o2_5, o24: o2_4, o23: o2_3, o22: o2_2,
    _manual: true
  };
}

function getSelected(){
  return mode === 'catalog' ? getCatalogSelected() : getManualVehicle();
}

function updateManualPreview(){
  const v = getManualVehicle();
  const el = document.getElementById('manualPreview');
  if(!v){ el.innerHTML=''; return; }
  el.innerHTML = `
    <div style="display:flex;justify-content:space-between;padding:6px 0;font-size:.76rem">
      <span style="color:var(--silver-400)">20% Down Payment</span>
      <span style="color:var(--silver-700);font-weight:600">${fmt(v.dp)}</span>
    </div>
    <div style="display:flex;justify-content:space-between;padding:6px 0;font-size:.76rem">
      <span style="color:var(--silver-400)">80% Loan Amount</span>
      <span style="color:var(--silver-700);font-weight:600">${fmt(v.la)}</span>
    </div>
    <div style="display:flex;justify-content:space-between;padding:6px 0;font-size:.76rem">
      <span style="color:var(--silver-400)">Est. Insurance</span>
      <span style="color:var(--silver-700);font-weight:600">${fmt(v.ins)}</span>
    </div>
  `;
}

function fmt(n){
  return '\u20B1' + Math.round(n).toLocaleString('en-PH');
}
function fmtPdf(n){
  return 'P ' + Math.round(n).toLocaleString('en-PH');
}

function selectOption(opt){
  selectedOption = opt;
  document.querySelectorAll('.pill').forEach(p=>{
    p.classList.toggle('active', parseInt(p.dataset.option)===opt);
  });
  updateOptionSummary();
}

function updateOptionSummary(){
  const v = getSelected();
  if(!v) return;
  const el = document.getElementById('optionSummary');
  const label1 = selectedOption===1 ? 'Includes CMF in cashout' : 'CMF applied to monthly';
  el.innerHTML = `<div style="font-size:.66rem;color:var(--silver-400);margin-top:6px;text-align:center">${label1}</div>`;
}

function selectTerm(t){
  selectedTerm = t;
  document.querySelectorAll('.term-btn').forEach(b=>{
    b.classList.toggle('active', parseInt(b.dataset.term)===t);
  });
}

function getMonthly(v){
  if(!v||!selectedTerm) return 0;
  const key = selectedOption===1
    ? {5:'o15',4:'o14',3:'o13',2:'o12'}[selectedTerm]
    : {5:'o25',4:'o24',3:'o23',2:'o22'}[selectedTerm];
  return v[key] || 0;
}

function showResult(){
  const v = getSelected();
  if(!v) return;
  const monthly = getMonthly(v);
  const cashout = selectedOption===1 ? v.o1c : v.o2c;
  const name = v._manual ? v.u : `${v.b} ${v.u} ${v.v} ${v.t}`;

  document.getElementById('vehicleTag').textContent = name;
  document.getElementById('monthlyAmt').textContent = fmt(monthly);
  document.getElementById('termSuffix').textContent =
    `for ${selectedTerm*12} months (${selectedTerm}-year term)`;

  const optLabel = selectedOption===1 ? 'Pay-All DP' : 'CMF to Monthly';

  document.getElementById('breakdown').innerHTML = `
    <div class="breakdown-row">
      <span class="lbl">SRP</span>
      <span class="val">${fmt(v.s)}</span>
    </div>
    <div class="breakdown-row highlight">
      <span class="lbl">Total Cashout (${optLabel})</span>
      <span class="val">${fmt(cashout)}</span>
    </div>
  `;
}

// Navigation
function goStep(step){
  if(step===2){
    if(!getSelected()){
      alert(mode==='catalog' ? 'Please select a vehicle first.' : 'Please enter a valid SRP (min 100,000).');
      return;
    }
    updateOptionSummary();
  }
  if(step===3 && !getSelected()) return;
  if(step===4){
    if(!selectedTerm){alert('Please select a loan term.');return;}
    showResult();
  }

  currentStep = step;
  hideAllSteps();

  if(step===1){
    if(mode==='catalog') document.getElementById('step1').classList.remove('hidden');
    else document.getElementById('step1m').classList.remove('hidden');
  } else {
    const el = document.getElementById('step'+step);
    el.classList.remove('hidden');
    el.classList.add('fade-in');
  }

  updateStepper();
}

// PDF
function downloadPDF(){
  const v = getSelected();
  if(!v) return;
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF({unit:'mm',format:'a4'});
  const monthly = getMonthly(v);
  const cashout = selectedOption===1 ? v.o1c : v.o2c;
  const optLabel = selectedOption===1 ? 'Option 1: Pay-All DP' : 'Option 2: CMF to Monthly';
  const name = v._manual ? v.u : `${v.b} ${v.u} ${v.v} ${v.t}`;
  const w = 210;
  let y = 0;

  // Header
  doc.setFillColor(220,38,38);
  doc.rect(0,0,w,32,'F');
  doc.setFillColor(185,28,28);
  doc.rect(0,28,w,4,'F');

  doc.setTextColor(255,255,255);
  doc.setFontSize(18);
  doc.setFont('helvetica','bold');
  doc.text('AUTOTREND AUTOLOAN SERVICES',w/2,14,{align:'center'});
  doc.setFontSize(8);
  doc.setFont('helvetica','normal');
  doc.text('#40 Scout Borromeo St. Brgy South Triangle, Quezon City',w/2,21,{align:'center'});
  doc.text('0928 459 9009  |  sales.autotrendphilippines@gmail.com',w/2,26,{align:'center'});

  y = 42;
  doc.setTextColor(63,63,70);
  doc.setFontSize(13);
  doc.setFont('helvetica','bold');
  doc.text('VEHICLE FORMAL QUOTATION',w/2,y,{align:'center'});

  y += 10;
  doc.setFontSize(9);
  doc.setFont('helvetica','normal');
  doc.setTextColor(113,113,122);
  const today = new Date().toLocaleDateString('en-PH',{year:'numeric',month:'long',day:'numeric'});
  doc.text('Date: '+today, 20, y);

  y += 10;
  doc.setFillColor(244,244,245);
  doc.roundedRect(18,y-5,w-36,28,3,3,'F');
  doc.setTextColor(63,63,70);
  doc.setFontSize(10);
  doc.setFont('helvetica','bold');
  doc.text(name, w/2, y+2, {align:'center'});
  doc.setFont('helvetica','normal');
  doc.setFontSize(9);
  doc.setTextColor(113,113,122);
  doc.text(`SRP: ${fmtPdf(v.s)}   |   Discount: ${fmtPdf(v.d)}`, w/2, y+10, {align:'center'});
  doc.text(optLabel + '   |   ' + selectedTerm + '-Year Term', w/2, y+17, {align:'center'});

  y += 36;
  doc.setFillColor(220,38,38);
  doc.roundedRect(18,y-5,w-36,22,3,3,'F');
  doc.setTextColor(255,255,255);
  doc.setFontSize(8);
  doc.text('MONTHLY AMORTIZATION', w/2, y+2, {align:'center'});
  doc.setFontSize(16);
  doc.setFont('helvetica','bold');
  doc.text(fmtPdf(monthly) + ' / month', w/2, y+13, {align:'center'});

  y += 28;
  doc.setTextColor(63,63,70);
  doc.setFont('helvetica','bold');
  doc.setFontSize(10);
  doc.text('Breakdown',20,y);
  y += 6;

  const rows = [
    ['SRP', fmtPdf(v.s)],
    ['Discount', '-'+fmtPdf(v.d)],
    ['20% Down Payment', fmtPdf(v.dp)],
    ['80% Loan Amount', fmtPdf(v.la)],
    ['CMF', fmtPdf(v.cmf)],
    ['Processing Fee', fmtPdf(v.pf)],
    ['LTO / TPL (3yrs)', fmtPdf(v.lto)],
    ['Insurance (1yr est.)', fmtPdf(v.ins)],
    ['Total Cashout', fmtPdf(cashout)],
    ['Monthly Amortization ('+selectedTerm+'yr)', fmtPdf(monthly)],
  ];

  doc.setFont('helvetica','normal');
  doc.setFontSize(9);
  rows.forEach((r,i)=>{
    if(i%2===0){
      doc.setFillColor(250,250,250);
      doc.rect(18,y-3.5,w-36,8,'F');
    }
    doc.setTextColor(113,113,122);
    doc.text(r[0],22,y+1);
    const isBold = r[0]==='Total Cashout'||r[0].startsWith('Monthly');
    if(isBold){
      doc.setFont('helvetica','bold');
      doc.setTextColor(220,38,38);
    } else {
      doc.setTextColor(63,63,70);
    }
    doc.text(r[1],w-22,y+1,{align:'right'});
    doc.setFont('helvetica','normal');
    y += 8;
  });

  y += 8;
  doc.setDrawColor(228,228,231);
  doc.line(18,y,w-18,y);
  y += 6;
  doc.setFontSize(7);
  doc.setTextColor(161,161,170);
  const disc = 'This quotation is for estimation purposes only and is subject to the final result of the Credit Investigation. All figures are based on the offer provided by the dealership. Autotrend Autoloan Services has no control over the total cashout amount. Only standard Planta discount applies.';
  const lines = doc.splitTextToSize(disc, w-40);
  doc.text(lines, 20, y);

  const safeName = name.replace(/[^a-zA-Z0-9]/g,'_');
  doc.save(`Autotrend_Quote_${safeName}.pdf`);
}
</script>
</body>
</html>
