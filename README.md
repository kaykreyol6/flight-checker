<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flight Compensation Checker</title>
  <link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Outfit:wght@300;400;500;600&display=swap" rel="stylesheet">
  <style>
    :root {
      --sky: #0A1628;
      --sky-mid: #0F2040;
      --sky-light: #1A3A6A;
      --blue: #1E5EFF;
      --blue-bright: #4D82FF;
      --cyan: #00C8FF;
      --gold: #FFB800;
      --gold-light: #FFD055;
      --white: #FFFFFF;
      --off-white: #EEF2FA;
      --muted: rgba(255,255,255,0.45);
      --border: rgba(255,255,255,0.08);
      --font-display: 'Bebas Neue', sans-serif;
      --font-body: 'Outfit', sans-serif;
    }
 
    * { margin: 0; padding: 0; box-sizing: border-box; }
 
    body {
      font-family: var(--font-body);
      background: var(--sky);
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 30px 16px;
      position: relative;
      overflow-x: hidden;
    }
 
    /* BACKGROUND */
    body::before {
      content: '';
      position: fixed;
      inset: 0;
      background:
        radial-gradient(ellipse 80% 60% at 50% -10%, rgba(30,94,255,0.25) 0%, transparent 60%),
        radial-gradient(ellipse 50% 40% at 90% 100%, rgba(0,200,255,0.1) 0%, transparent 50%),
        radial-gradient(ellipse 40% 30% at 10% 80%, rgba(255,184,0,0.06) 0%, transparent 50%);
      pointer-events: none;
      z-index: 0;
    }
 
    /* GRID OVERLAY */
    body::after {
      content: '';
      position: fixed;
      inset: 0;
      background-image:
        linear-gradient(rgba(30,94,255,0.04) 1px, transparent 1px),
        linear-gradient(90deg, rgba(30,94,255,0.04) 1px, transparent 1px);
      background-size: 48px 48px;
      pointer-events: none;
      z-index: 0;
    }
 
    /* CARD */
    .card {
      width: 100%;
      max-width: 520px;
      background: rgba(15,32,64,0.85);
      border: 1px solid rgba(30,94,255,0.3);
      border-radius: 24px;
      overflow: hidden;
      box-shadow:
        0 0 0 1px rgba(255,255,255,0.04),
        0 32px 80px rgba(0,0,0,0.5),
        0 0 80px rgba(30,94,255,0.12);
      position: relative;
      z-index: 1;
      backdrop-filter: blur(20px);
    }
 
    /* CARD HEADER */
    .card-header {
      padding: 36px 36px 28px;
      border-bottom: 1px solid var(--border);
      position: relative;
      overflow: hidden;
    }
 
    .card-header::before {
      content: '✈';
      position: absolute;
      right: -10px;
      top: -20px;
      font-size: 120px;
      opacity: 0.04;
      transform: rotate(15deg);
      pointer-events: none;
    }
 
    .header-badge {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      background: rgba(0,200,255,0.1);
      border: 1px solid rgba(0,200,255,0.2);
      border-radius: 20px;
      padding: 4px 12px;
      font-size: 11px;
      font-weight: 600;
      letter-spacing: 1.5px;
      text-transform: uppercase;
      color: var(--cyan);
      margin-bottom: 14px;
    }
 
    .badge-dot {
      width: 6px; height: 6px;
      background: var(--cyan);
      border-radius: 50%;
      animation: blink 2s infinite;
    }
 
    @keyframes blink {
      0%,100%{opacity:1} 50%{opacity:0.2}
    }
 
    .card-title {
      font-family: var(--font-display);
      font-size: 42px;
      letter-spacing: 1px;
      color: var(--white);
      line-height: 1;
      margin-bottom: 8px;
    }
 
    .card-title span { color: var(--cyan); }
 
    .card-sub {
      font-size: 13px;
      color: var(--muted);
      font-weight: 300;
      line-height: 1.6;
    }
 
    /* CARD BODY */
    .card-body { padding: 28px 36px 36px; display: flex; flex-direction: column; gap: 18px; }
 
    /* FIELD */
    .field { display: flex; flex-direction: column; gap: 7px; }
 
    .field-label {
      font-size: 11px;
      font-weight: 600;
      letter-spacing: 1.5px;
      text-transform: uppercase;
      color: rgba(255,255,255,0.4);
    }
 
    .field-row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
 
    .input-wrap { position: relative; }
 
    .input-icon {
      position: absolute;
      left: 14px;
      top: 50%;
      transform: translateY(-50%);
      font-size: 16px;
      pointer-events: none;
      opacity: 0.4;
    }
 
    input, select {
      width: 100%;
      background: rgba(255,255,255,0.05);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 12px;
      padding: 13px 14px 13px 42px;
      font-family: var(--font-body);
      font-size: 14px;
      font-weight: 500;
      color: var(--white);
      outline: none;
      transition: all 0.2s;
      appearance: none;
      -webkit-appearance: none;
    }
 
    input::placeholder { color: rgba(255,255,255,0.2); }
    input::-webkit-calendar-picker-indicator { filter: invert(1); opacity: 0.4; }
 
    input:focus, select:focus {
      border-color: rgba(30,94,255,0.6);
      background: rgba(30,94,255,0.08);
      box-shadow: 0 0 0 3px rgba(30,94,255,0.12);
    }
 
    select option { background: var(--sky-mid); color: var(--white); }
 
    /* SELECT ARROW */
    .select-wrap { position: relative; }
    .select-wrap::after {
      content: '▾';
      position: absolute;
      right: 14px;
      top: 50%;
      transform: translateY(-50%);
      color: rgba(255,255,255,0.3);
      pointer-events: none;
      font-size: 12px;
    }
 
    /* DELAY SLIDER */
    .delay-display {
      font-family: var(--font-display);
      font-size: 32px;
      color: var(--white);
      letter-spacing: 1px;
      padding: 10px 14px;
      background: rgba(255,255,255,0.05);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 12px;
      display: flex;
      align-items: center;
      gap: 8px;
    }
 
    .delay-display span { font-size: 14px; font-family: var(--font-body); color: var(--muted); font-weight: 300; }
 
    .delay-controls { display: flex; gap: 8px; }
 
    .delay-btn {
      flex: 1;
      background: rgba(255,255,255,0.06);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 10px;
      color: var(--white);
      font-size: 20px;
      font-weight: 300;
      cursor: pointer;
      padding: 8px;
      transition: all 0.15s;
      font-family: var(--font-body);
    }
 
    .delay-btn:hover { background: rgba(30,94,255,0.2); border-color: rgba(30,94,255,0.4); }
    .delay-btn:active { transform: scale(0.95); }
 
    input[type="number"] {
      padding-left: 42px;
    }
 
    /* BUTTON */
    .check-btn {
      width: 100%;
      padding: 16px;
      background: linear-gradient(135deg, var(--blue) 0%, #0A3FCC 100%);
      border: none;
      border-radius: 14px;
      font-family: var(--font-display);
      font-size: 20px;
      letter-spacing: 2px;
      color: white;
      cursor: pointer;
      transition: all 0.22s;
      box-shadow: 0 6px 24px rgba(30,94,255,0.35), 0 0 0 1px rgba(255,255,255,0.06) inset;
      position: relative;
      overflow: hidden;
      margin-top: 4px;
    }
 
    .check-btn::before {
      content: '';
      position: absolute;
      inset: 0;
      background: linear-gradient(135deg, rgba(255,255,255,0.1) 0%, transparent 60%);
      opacity: 0;
      transition: opacity 0.2s;
    }
 
    .check-btn:hover { transform: translateY(-2px); box-shadow: 0 10px 32px rgba(30,94,255,0.5), 0 0 0 1px rgba(255,255,255,0.1) inset; }
    .check-btn:hover::before { opacity: 1; }
    .check-btn:active { transform: translateY(0); }
 
    /* RESULT */
    .result-box {
      border-radius: 16px;
      padding: 22px 24px;
      display: none;
      animation: slideUp 0.4s cubic-bezier(0.34,1.56,0.64,1);
    }
 
    @keyframes slideUp {
      from { opacity: 0; transform: translateY(16px) scale(0.97); }
      to   { opacity: 1; transform: translateY(0) scale(1); }
    }
 
    .result-box.eligible {
      background: linear-gradient(135deg, rgba(0,200,100,0.1), rgba(0,180,80,0.06));
      border: 1px solid rgba(0,200,100,0.25);
      display: block;
    }
 
    .result-box.not-eligible {
      background: linear-gradient(135deg, rgba(255,80,60,0.1), rgba(220,50,30,0.06));
      border: 1px solid rgba(255,80,60,0.2);
      display: block;
    }
 
    .result-top {
      display: flex;
      align-items: center;
      gap: 12px;
      margin-bottom: 14px;
    }
 
    .result-icon {
      width: 42px; height: 42px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
      flex-shrink: 0;
    }
 
    .eligible .result-icon { background: rgba(0,200,100,0.15); }
    .not-eligible .result-icon { background: rgba(255,80,60,0.15); }
 
    .result-label {
      font-size: 11px;
      font-weight: 600;
      letter-spacing: 1.5px;
      text-transform: uppercase;
      margin-bottom: 3px;
    }
 
    .eligible .result-label { color: #4DEBA0; }
    .not-eligible .result-label { color: #FF7060; }
 
    .result-headline {
      font-family: var(--font-display);
      font-size: 22px;
      color: var(--white);
      letter-spacing: 0.5px;
    }
 
    .payout-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: rgba(255,255,255,0.05);
      border-radius: 10px;
      padding: 14px 16px;
    }
 
    .payout-label { font-size: 12px; color: var(--muted); font-weight: 400; }
 
    .payout-amount {
      font-family: var(--font-display);
      font-size: 36px;
      color: var(--gold);
      letter-spacing: 1px;
      text-shadow: 0 0 20px rgba(255,184,0,0.4);
    }
 
    .result-note {
      font-size: 11px;
      color: rgba(255,255,255,0.3);
      margin-top: 10px;
      line-height: 1.6;
    }
 
    /* DIVIDER */
    .divider {
      height: 1px;
      background: var(--border);
      margin: 4px 0;
    }
 
    /* STEP CHIPS */
    .steps {
      display: flex;
      gap: 8px;
      margin-top: 2px;
    }
 
    .step-chip {
      flex: 1;
      background: rgba(255,255,255,0.04);
      border: 1px solid rgba(255,255,255,0.07);
      border-radius: 8px;
      padding: 8px 10px;
      text-align: center;
      font-size: 11px;
      color: rgba(255,255,255,0.3);
      font-weight: 500;
    }
 
    .step-chip strong { display: block; font-size: 18px; margin-bottom: 2px; }
  </style>
</head>
<body>
 
<div class="card">
 
  <!-- HEADER -->
  <div class="card-header">
    <div class="header-badge"><div class="badge-dot"></div>EU Regulation EC 261/2004</div>
    <div class="card-title">FLIGHT <span>COMP</span><br>CHECKER</div>
    <div class="card-sub">Find out if you're owed money for your delayed or cancelled flight — in under 2 minutes.</div>
  </div>
 
  <!-- BODY -->
  <div class="card-body">
 
    <!-- Flight + Date -->
    <div class="field-row">
      <div class="field">
        <div class="field-label">Flight No.</div>
        <div class="input-wrap">
          <div class="input-icon">✈</div>
          <input type="text" id="flight" placeholder="e.g. BA422">
        </div>
      </div>
      <div class="field">
        <div class="field-label">Date</div>
        <div class="input-wrap">
          <div class="input-icon">📅</div>
          <input type="date" id="date">
        </div>
      </div>
    </div>
 
    <!-- Delay -->
    <div class="field">
      <div class="field-label">Delay Length</div>
      <div class="input-wrap">
        <div class="input-icon">⏱</div>
        <input type="number" id="delay" placeholder="Hours delayed" min="0" max="48">
      </div>
    </div>
 
    <!-- Distance -->
    <div class="field">
      <div class="field-label">Route Distance</div>
      <div class="select-wrap">
        <div class="input-wrap">
          <div class="input-icon">🌍</div>
          <select id="distance" style="padding-left:42px">
            <option value="short">Short — under 1,500 km</option>
            <option value="medium">Medium — 1,500 to 3,500 km</option>
            <option value="long">Long — over 3,500 km</option>
          </select>
        </div>
      </div>
    </div>
 
    <div class="divider"></div>
 
    <!-- BUTTON -->
    <button class="check-btn" onclick="checkEligibility()">CHECK MY ELIGIBILITY →</button>
 
    <!-- RESULT -->
    <div class="result-box" id="result">
      <div class="result-top">
        <div class="result-icon" id="resultIcon"></div>
        <div>
          <div class="result-label" id="resultLabel"></div>
          <div class="result-headline" id="resultHeadline"></div>
        </div>
      </div>
      <div class="payout-row" id="payoutRow" style="display:none">
        <div class="payout-label">Estimated compensation<br><span style="font-size:10px;opacity:0.6">under EU Reg. EC 261/2004</span></div>
        <div class="payout-amount" id="payoutAmount"></div>
      </div>
      <div class="result-note" id="resultNote"></div>
    </div>
 
    <!-- FOOTER CHIPS -->
    <div class="steps">
      <div class="step-chip"><strong>2</strong>minutes</div>
      <div class="step-chip"><strong>€600</strong>max payout</div>
      <div class="step-chip"><strong>EC</strong>261/2004</div>
    </div>
 
  </div>
</div>
 
<script>
function checkEligibility() {
  const delay    = parseFloat(document.getElementById('delay').value);
  const distance = document.getElementById('distance').value;
  const box      = document.getElementById('result');
  const icon     = document.getElementById('resultIcon');
  const label    = document.getElementById('resultLabel');
  const headline = document.getElementById('resultHeadline');
  const payRow   = document.getElementById('payoutRow');
  const payout   = document.getElementById('payoutAmount');
  const note     = document.getElementById('resultNote');
 
  // reset
  box.className = 'result-box';
  box.style.display = 'none';
 
  if (!delay && delay !== 0) {
    alert('Please enter your delay in hours.');
    return;
  }
 
  if (delay >= 3) {
    const amounts = { short: '€250', medium: '€400', long: '€600' };
    box.classList.add('eligible');
    icon.textContent = '✅';
    label.textContent = 'Likely Eligible';
    headline.textContent = 'You may be owed compensation';
    payRow.style.display = 'flex';
    payout.textContent = amounts[distance];
    note.textContent = 'This is an estimate based on EU Regulation EC 261/2004. Actual eligibility depends on airline, extraordinary circumstances, and whether the flight departed from an EU airport.';
  } else {
    box.classList.add('not-eligible');
    icon.textContent = '❌';
    label.textContent = 'Not Eligible';
    headline.textContent = 'Delay under 3 hours';
    payRow.style.display = 'none';
    note.textContent = 'Under EU law, compensation is only triggered when your arrival delay exceeds 3 hours. If your flight was cancelled, different rules may apply.';
  }
}
</script>
</body>
