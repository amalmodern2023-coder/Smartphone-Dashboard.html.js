<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Smartphone Supply Chain Simulator - Pro</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
  <style>
    :root {
      --bg-dark: #0a0e14;
      --panel-bg: #111620;
      --panel-inner: #161d29;
      --border-color: #222d3d;
      --text-primary: #f1f5f9;
      --text-secondary: #94a3b8;
      --cyan: #2dd4bf;
      --blue: #38bdf8;
      --orange: #f59e0b;
      --purple: #818cf8;
      --green: #22c55e;
      --red: #ef4444;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      background-color: var(--bg-dark);
      color: var(--text-primary);
      padding: 14px;
      font-size: 13px;
    }

    .dashboard {
      max-width: 1400px;
      margin: 0 auto;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .header {
      background: var(--panel-bg);
      border: 1px solid var(--border-color);
      border-radius: 10px;
      padding: 10px 16px;
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      gap: 10px;
    }

    .header h1 {
      font-size: 1.15rem;
      font-weight: 700;
    }

    .header-actions {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      align-items: center;
    }

    /* Stylish Language Switcher Pill */
    .lang-switch {
      display: inline-flex;
      align-items: center;
      background: var(--panel-inner);
      border: 1px solid var(--border-color);
      border-radius: 999px;
      padding: 3px;
      gap: 2px;
      box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.35);
    }

    .lang-btn {
      border: none;
      background: transparent;
      color: var(--text-secondary);
      padding: 4px 10px;
      border-radius: 999px;
      font-size: 0.75rem;
      font-weight: 600;
      cursor: pointer;
      transition: background-color 0.2s ease, color 0.2s ease, transform 0.16s ease;
    }

    .lang-btn:active {
      transform: scale(0.96);
    }

    .lang-btn.active {
      background: linear-gradient(135deg, rgba(56, 189, 248, 0.25), rgba(45, 212, 191, 0.22));
      color: #f8fafc;
      border: 1px solid rgba(56, 189, 248, 0.45);
    }

    .icon-btn {
      background: var(--panel-inner);
      border: 1px solid var(--border-color);
      color: var(--text-secondary);
      padding: 6px 11px;
      border-radius: 8px;
      cursor: pointer;
      font-size: 0.78rem;
      font-weight: 500;
      transition: transform 0.18s ease, background-color 0.2s ease, border-color 0.2s ease, color 0.2s ease;
    }

    .icon-btn:active { transform: scale(0.97); }
    .icon-btn:hover, .icon-btn.active {
      background: rgba(56, 189, 248, 0.16);
      border-color: var(--blue);
      color: var(--blue);
    }

    /* Smooth Floating Cards */
    .panel {
      background: var(--panel-bg);
      border: 1px solid var(--border-color);
      border-radius: 10px;
      padding: 14px 16px;
      transition: transform 0.24s cubic-bezier(0.22, 1, 0.36, 1), border-color 0.24s ease, box-shadow 0.24s ease;
      will-change: transform;
    }

    .panel:hover {
      transform: translateY(-3px);
      border-color: rgba(56, 189, 248, 0.35);
      box-shadow: 0 12px 26px rgba(0, 0, 0, 0.38);
    }

    .panel-header {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
      gap: 10px;
    }

    .panel-title {
      font-size: 0.96rem;
      font-weight: 700;
    }

    .sub-caption {
      font-size: 0.75rem;
      color: var(--text-secondary);
      margin-top: 2px;
    }

    .top-metrics {
      display: flex;
      flex-wrap: wrap;
      gap: 16px;
      font-size: 0.8rem;
    }

    .metric-inline span { color: var(--text-secondary); }
    .metric-inline strong { margin-inline-start: 4px; }

    /* Economic Assumptions Cards with Expandable Inbox */
    .assumptions-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 12px;
    }

    .assumption-card {
      background: var(--panel-inner);
      border: 1px solid var(--border-color);
      border-radius: 8px;
      padding: 12px;
      cursor: pointer;
      transition: transform 0.22s cubic-bezier(0.22, 1, 0.36, 1), border-color 0.22s ease, box-shadow 0.22s ease;
      position: relative;
      overflow: hidden;
    }

    .assumption-card:hover {
      transform: translateY(-3px);
      border-color: var(--cyan);
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
    }

    .card-header-row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 8px;
    }

    .card-title {
      font-size: 0.84rem;
      font-weight: 600;
      color: var(--text-primary);
    }

    .card-badge {
      font-size: 0.68rem;
      padding: 2px 7px;
      border-radius: 999px;
      background: rgba(45, 212, 191, 0.15);
      color: var(--cyan);
      white-space: nowrap;
    }

    .card-subtitle {
      font-size: 0.75rem;
      color: var(--text-secondary);
      margin-top: 5px;
      line-height: 1.45;
    }

    /* Expandable Inbox */
    .assumption-inbox {
      max-height: 0;
      opacity: 0;
      overflow: hidden;
      transition: max-height 0.35s cubic-bezier(0.22, 1, 0.36, 1), opacity 0.25s ease, margin-top 0.25s ease;
      border-top: 1px solid transparent;
    }

    .assumption-card.expanded .assumption-inbox {
      max-height: 190px;
      opacity: 1;
      margin-top: 10px;
      padding-top: 8px;
      border-top-color: var(--border-color);
    }

    .inbox-content {
      font-size: 0.74rem;
      color: var(--text-secondary);
      display: flex;
      flex-direction: column;
      gap: 6px;
      line-height: 1.45;
    }

    .inbox-equation {
      color: var(--cyan);
      font-family: "Courier New", monospace;
      font-weight: 600;
      background: rgba(10, 14, 20, 0.65);
      padding: 5px 7px;
      border-radius: 5px;
      direction: ltr;
      text-align: left;
    }

    .main-chart-wrap {
      position: relative;
      height: 215px;
      width: 100%;
    }

    .tariff-slider-bar {
      margin-top: 12px;
      padding-top: 10px;
      border-top: 1px solid var(--border-color);
      display: flex;
      align-items: center;
      gap: 14px;
    }

    .tariff-slider-bar label {
      font-weight: 600;
      font-size: 0.82rem;
      white-space: nowrap;
    }

    .slider-container {
      flex: 1;
      display: flex;
      align-items: center;
    }

    input[type="range"] {
      width: 100%;
      height: 6px;
      background: #1e293b;
      border-radius: 999px;
      accent-color: var(--blue);
      cursor: pointer;
    }

    .tariff-pill {
      background: rgba(56, 189, 248, 0.16);
      border: 1px solid var(--blue);
      color: var(--blue);
      font-weight: 700;
      padding: 3px 10px;
      border-radius: 6px;
      min-width: 48px;
      text-align: center;
      direction: ltr;
    }

    .split-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 14px;
    }

    .sub-box {
      background: var(--panel-inner);
      border: 1px solid var(--border-color);
      border-radius: 8px;
      padding: 12px;
      transition: transform 0.22s cubic-bezier(0.22, 1, 0.36, 1), border-color 0.22s ease, box-shadow 0.22s ease;
    }

    .sub-box:hover {
      transform: translateY(-2px);
      border-color: rgba(45, 212, 191, 0.35);
      box-shadow: 0 8px 18px rgba(0, 0, 0, 0.3);
    }

    .sub-box-title {
      font-size: 0.84rem;
      font-weight: 600;
      margin-bottom: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 8px;
    }

    .slice-hint {
      font-size: 0.7rem;
      color: var(--text-secondary);
      font-weight: 400;
    }

    .cost-left-grid {
      display: grid;
      grid-template-columns: 1.1fr 0.95fr 1fr;
      align-items: center;
      gap: 10px;
    }

    .legend-list {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 7px;
      font-size: 0.76rem;
    }

    .legend-list li {
      display: flex;
      align-items: center;
      gap: 7px;
      color: var(--text-secondary);
      cursor: pointer;
      padding: 3px 5px;
      border-radius: 5px;
      transition: background-color 0.18s ease, color 0.18s ease, transform 0.18s ease;
    }

    .legend-list li:hover, .legend-list li.selected {
      background: rgba(255, 255, 255, 0.05);
      color: var(--text-primary);
    }

    .dot { width: 9px; height: 9px; border-radius: 50%; flex-shrink: 0; }

    .mini-donut-wrap {
      position: relative;
      height: 136px;
      width: 136px;
      margin: 0 auto;
      cursor: pointer;
    }

    .mini-pills { display: flex; flex-direction: column; gap: 7px; }

    .info-pill {
      background: #1d2636;
      border: 1px solid var(--border-color);
      padding: 6px 10px;
      border-radius: 6px;
      font-size: 0.75rem;
      display: flex;
      justify-content: space-between;
      gap: 6px;
      color: var(--text-secondary);
    }

    .info-pill strong { color: var(--text-primary); direction: ltr; }

    .levers-right-grid {
      display: grid;
      grid-template-columns: 1.25fr 0.75fr;
      align-items: center;
      gap: 12px;
    }

    .lever-buttons { display: flex; flex-direction: column; gap: 8px; }

    .lever-btn {
      background: #1d2636;
      border: 1px solid var(--border-color);
      color: var(--text-primary);
      padding: 9px 12px;
      border-radius: 7px;
      font-size: 0.78rem;
      text-align: start;
      cursor: pointer;
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 8px;
      transition: transform 0.18s ease, background-color 0.2s ease, border-color 0.2s ease;
    }

    .lever-btn:hover { transform: translateY(-2px); border-color: var(--blue); }
    .lever-btn.active {
      background: rgba(34, 197, 94, 0.16);
      border-color: var(--green);
      color: #dcfce7;
    }

    .opt-donut-area { display: flex; flex-direction: column; align-items: center; text-align: center; }
    .opt-donut-wrap { position: relative; height: 126px; width: 126px; cursor: pointer; }
    .opt-price-tag { margin-top: 6px; font-size: 0.76rem; font-weight: 700; color: var(--green); }

    .bottom-grid {
      display: grid;
      grid-template-columns: 0.85fr 1.25fr 1fr;
      gap: 14px;
    }

    .gauge-area { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 155px; }
    .gauge-svg { width: 190px; height: 105px; }

    #gaugeArc, #gaugeNeedle {
      transition: stroke-dashoffset 0.35s cubic-bezier(0.22, 1, 0.36, 1), stroke 0.25s ease;
    }

    .gauge-label { margin-top: 4px; font-size: 0.86rem; font-weight: 700; color: var(--green); }

    .map-area {
      position: relative;
      height: 155px;
      background: #0d131d;
      border-radius: 8px;
      border: 1px solid #1c2534;
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .map-area svg { width: 100%; height: 100%; }
    .trade-chart-wrap { position: relative; height: 155px; width: 100%; }

    @media (max-width: 1024px) {
      .split-grid, .bottom-grid, .cost-left-grid, .levers-right-grid, .assumptions-grid {
        grid-template-columns: 1fr;
      }
    }
  </style>
</head>
<body>
  <div class="dashboard">
    <header class="header">
      <h1 id="appTitle">Smartphone Supply Chain Simulator</h1>
      <div class="header-actions">
        <div class="lang-switch" role="group" aria-label="Language Selector">
          <button class="lang-btn active" id="langEnBtn" type="button">EN</button>
          <button class="lang-btn" id="langArBtn" type="button">العربية</button>
        </div>
        <button class="icon-btn active" id="fxToggleBtn" type="button">FX: 50 EGP/$</button>
        <button class="icon-btn" id="resetBtn" type="button">Reset</button>
      </div>
    </header>

    <!-- Economic Assumptions Cards with Expandable Inbox -->
    <section class="panel">
      <div class="panel-header">
        <div class="panel-title" id="assumptionsTitle">Economic Hypotheses &amp; Econometric Models</div>
        <div class="sub-caption" id="assumptionsSubtitle">Click any card to inspect the underlying equation and transmission mechanism</div>
      </div>

      <div class="assumptions-grid">
        <div class="assumption-card" onclick="toggleCard(this)">
          <div class="card-header-row">
            <span class="card-title" id="c1Title">Cost Pass-Through</span>
            <span class="card-badge" id="c1Badge">Tariff &amp; FX</span>
          </div>
          <div class="card-subtitle" id="c1Sub">Measures how tariff and exchange rate shocks pass to end-user prices.</div>
          <div class="assumption-inbox">
            <div class="inbox-content">
              <span id="c1Mech"><strong>Mechanism:</strong> Direct transmission of import costs to final market price.</span>
              <span class="inbox-equation">Price = (FOB + Ship) × FX × (1 + Tariff × PassThrough)</span>
            </div>
          </div>
        </div>

        <div class="assumption-card" onclick="toggleCard(this)">
          <div class="card-header-row">
            <span class="card-title" id="c2Title">Price Elasticity of Demand</span>
            <span class="card-badge" id="c2Badge">Volume Impact</span>
          </div>
          <div class="card-subtitle" id="c2Sub">Quantifies the drop in import volume relative to price inflation.</div>
          <div class="assumption-inbox">
            <div class="inbox-content">
              <span id="c2Mech"><strong>Mechanism:</strong> Consumer demand contraction driven by inflationary pressure.</span>
              <span class="inbox-equation">Volume = Base_Vol × (1 - Elasticity × Inflation_Pct)</span>
            </div>
          </div>
        </div>

        <div class="assumption-card" onclick="toggleCard(this)">
          <div class="card-header-row">
            <span class="card-title" id="c3Title">Supply Chain Health Index</span>
            <span class="card-badge" id="c3Badge">Resilience Score</span>
          </div>
          <div class="card-subtitle" id="c3Sub">Composite index reflecting trade friction and policy optimization levers.</div>
          <div class="assumption-inbox">
            <div class="inbox-content">
              <span id="c3Mech"><strong>Mechanism:</strong> Evaluates system resilience under tariff stress and logistics relief.</span>
              <span class="inbox-equation">Health = f(Tariff_Stress, Logistics_Levers, FX_Stability)</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Performance Impact Analysis -->
    <section class="panel">
      <div class="panel-header">
        <div>
          <div class="panel-title" id="impactTitle">Performance Impact Analysis</div>
          <div class="sub-caption" id="impactSubtitle">Average Smartphone Price (EGP) vs. Import Volume (Units) &amp; Tariff Rate (%)</div>
        </div>
        <div class="top-metrics">
          <div class="metric-inline">
            <span id="inflationLabel">Projected Inflation Effect:</span>
            <strong id="inflationVal" style="color: var(--green);">+12.5%</strong>
          </div>
          <div class="metric-inline">
            <span id="revLossLabel">Estimated Revenue Loss:</span>
            <strong id="revLossVal" style="color: var(--orange);">45M EGP</strong>
          </div>
        </div>
      </div>

      <div class="main-chart-wrap">
        <canvas id="impactChart"></canvas>
      </div>

      <div class="tariff-slider-bar">
        <label for="tariffSlider" id="tariffSliderLabel">Tariff Rate Adjustment (%)</label>
        <div class="slider-container">
          <input type="range" id="tariffSlider" min="5" max="45" step="1" value="18" />
        </div>
        <div class="tariff-pill" id="tariffDisplay">18%</div>
      </div>
    </section>

    <!-- Cost Optimization Breakdown -->
    <section class="panel">
      <div class="panel-header">
        <div class="panel-title" id="costSectionTitle">Cost Optimization Breakdown</div>
      </div>

      <div class="split-grid">
        <div class="sub-box">
          <div class="sub-box-title">
            <span id="priceStructTitle">End-User Price Structure (Per Unit)</span>
            <span class="slice-hint" id="popHint1">Click slice to pop out</span>
          </div>
          <div class="cost-left-grid">
            <ul class="legend-list" id="structureLegend">
              <li data-idx="0"><span class="dot" style="background: var(--blue);"></span><span id="leg0">Factory Price ($)</span></li>
              <li data-idx="1"><span class="dot" style="background: var(--cyan);"></span><span id="leg1">Shipping &amp; Logistics (%)</span></li>
              <li data-idx="2"><span class="dot" style="background: var(--orange);"></span><span id="leg2">Customs &amp; Taxes (%)</span></li>
              <li data-idx="3"><span class="dot" style="background: var(--purple);"></span><span id="leg3">Distributor Margin (%)</span></li>
            </ul>

            <div class="mini-donut-wrap">
              <canvas id="structureDonut"></canvas>
            </div>

            <div class="mini-pills">
              <div class="info-pill">
                <span id="pillFactoryLbl">Factory Price</span>
                <strong id="pillFactory">$220</strong>
              </div>
              <div class="info-pill">
                <span id="pillShippingLbl">Shipping</span>
                <strong id="pillShipping">15%</strong>
              </div>
              <div class="info-pill">
                <span id="pillFinalLbl">Final Unit</span>
                <strong id="pillFinal">15,800 EGP</strong>
              </div>
            </div>
          </div>
        </div>

        <div class="sub-box">
          <div class="sub-box-title">
            <span id="leversTitle">Optimization Levers</span>
            <span class="slice-hint" id="popHint2">Click slice to inspect</span>
          </div>
          <div class="levers-right-grid">
            <div class="lever-buttons">
              <button class="lever-btn" id="btnShip" type="button">
                <span id="btnShipText">Reduce Shipping Cost (10%)</span>
                <span id="btnShipState">OFF</span>
              </button>
              <button class="lever-btn" id="btnTariff" type="button">
                <span id="btnTariffText">Negotiate Tariff Exemptions (20%)</span>
                <span id="btnTariffState">OFF</span>
              </button>
            </div>

            <div class="opt-donut-area">
              <div class="opt-donut-wrap">
                <canvas id="optDonut"></canvas>
              </div>
              <div class="opt-price-tag" id="optSavingsText">Optimized End-User Price: 0 EGP</div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Network Health & Trade Balance -->
    <section class="panel">
      <div class="panel-header">
        <div class="panel-title" id="networkSectionTitle">Network Health &amp; Trade Balance</div>
      </div>

      <div class="bottom-grid">
        <div class="sub-box">
          <div class="sub-box-title"><span id="healthBoxTitle">Real-time Supply Chain Health</span></div>
          <div class="gauge-area">
            <svg class="gauge-svg" viewBox="0 0 200 115">
              <path d="M 20 100 A 80 80 0 0 1 180 100" fill="none" stroke="#1e293b" stroke-width="18" stroke-linecap="round" />
              <path id="gaugeArc" d="M 20 100 A 80 80 0 0 1 180 100" fill="none" stroke="#22c55e" stroke-width="18" stroke-linecap="round" stroke-dasharray="251.2" stroke-dashoffset="20" />
              <line id="gaugeNeedle" x1="100" y1="100" x2="155" y2="55" stroke="#f1f5f9" stroke-width="3" stroke-linecap="round" />
              <circle cx="100" cy="100" r="6" fill="#f1f5f9" />
            </svg>
            <div class="gauge-label" id="healthLabel">OPTIMIZED (92%)</div>
          </div>
        </div>

        <div class="sub-box">
          <div class="sub-box-title"><span id="partnersBoxTitle">Top Import Partners</span></div>
          <div class="map-area">
            <svg viewBox="0 0 420 165">
              <path d="M35,45 Q75,30 110,55 T135,95 Q105,120 75,110 T35,45 Z" fill="#1e293b" />
              <path d="M165,35 Q235,20 315,35 T375,75 Q350,115 290,105 T225,130 Q185,125 175,85 Z" fill="#1e293b" />
              <path d="M315,115 Q350,110 365,135 T325,150 Z" fill="#1e293b" />
              <path d="M 310 62 Q 255 35 205 74" fill="none" stroke="#22c55e" stroke-width="2" stroke-dasharray="4 3" />
              <path d="M 322 86 Q 265 65 205 74" fill="none" stroke="#38bdf8" stroke-width="2" />
              <circle cx="205" cy="74" r="4.5" fill="#f59e0b" />
              <text id="mapEgypt" x="175" y="67" fill="#f1f5f9" font-size="10" font-weight="bold">Egypt</text>
              <circle cx="310" cy="62" r="4.5" fill="#22c55e" />
              <text id="mapChina" x="318" y="64" fill="#cbd5e1" font-size="10">China (54%)</text>
              <circle cx="322" cy="86" r="4.5" fill="#38bdf8" />
              <text id="mapVietnam" x="330" y="90" fill="#cbd5e1" font-size="10">Vietnam (28%)</text>
            </svg>
          </div>
        </div>

        <div class="sub-box">
          <div class="sub-box-title"><span id="tradeBoxTitle">Trade Balance Simulation (Import vs. Export)</span></div>
          <div class="trade-chart-wrap">
            <canvas id="tradeBalanceChart"></canvas>
          </div>
        </div>
      </div>
    </section>
  </div>

  <script>
    const I18N = {
      en: {
        appTitle: "Smartphone Supply Chain Simulator",
        reset: "Reset",
        fxPrefix: "FX:",
        fxUnit: "EGP/$",
        assumptionsTitle: "Economic Hypotheses & Econometric Models",
        assumptionsSubtitle: "Click any card to inspect the underlying equation and transmission mechanism",
        c1Title: "Cost Pass-Through",
        c1Badge: "Tariff & FX",
        c1Sub: "Measures how tariff and exchange rate shocks pass to end-user prices.",
        c1Mech: "<strong>Mechanism:</strong> Direct transmission of import costs to final market price.",
        c2Title: "Price Elasticity of Demand",
        c2Badge: "Volume Impact",
        c2Sub: "Quantifies the drop in import volume relative to price inflation.",
        c2Mech: "<strong>Mechanism:</strong> Consumer demand contraction driven by inflationary pressure.",
        c3Title: "Supply Chain Health Index",
        c3Badge: "Resilience Score",
        c3Sub: "Composite index reflecting trade friction and policy optimization levers.",
        c3Mech: "<strong>Mechanism:</strong> Evaluates system resilience under tariff stress and logistics relief.",
        impactTitle: "Performance Impact Analysis",
        impactSubtitle: "Average Smartphone Price (EGP) vs. Import Volume (Units) & Tariff Rate (%)",
        inflationLabel: "Projected Inflation Effect:",
        revLossLabel: "Estimated Revenue Loss:",
        revUnitM: "M EGP",
        tariffSliderLabel: "Tariff Rate Adjustment (%)",
        costSectionTitle: "Cost Optimization Breakdown",
        priceStructTitle: "End-User Price Structure (Per Unit)",
        popHint1: "Click slice to pop out",
        leg0: "Factory Price ($)",
        leg1: "Shipping & Logistics (%)",
        leg2: "Customs & Taxes (%)",
        leg3: "Distributor Margin (%)",
        pillFactoryLbl: "Factory Price",
        pillShippingLbl: "Shipping",
        pillFinalLbl: "Final Unit",
        leversTitle: "Optimization Levers",
        popHint2: "Click slice to inspect",
        btnShipText: "Reduce Shipping Cost (10%)",
        btnTariffText: "Negotiate Tariff Exemptions (20%)",
        on: "ON",
        off: "OFF",
        optSavedPrefix: "Optimized End-User Price: -",
        optPricePrefix: "End-User Price:",
        networkSectionTitle: "Network Health & Trade Balance",
        healthBoxTitle: "Real-time Supply Chain Health",
        healthOpt: "OPTIMIZED",
        healthMod: "MODERATE",
        healthStr: "STRESSED",
        partnersBoxTitle: "Top Import Partners",
        mapEgypt: "Egypt",
        mapChina: "China (54%)",
        mapVietnam: "Vietnam (28%)",
        tradeBoxTitle: "Trade Balance Simulation (Import vs. Export)",
        chartImpVol: "Import Volume (Units)",
        chartAvgPrice: "Average Smartphone Price (EGP)",
        chartTariffRate: "Tariff Rate (%)",
        donutStruct: ["Factory Price", "Shipping", "Customs & Taxes", "Margin"],
        donutOpt: ["Core Cost", "Tariff", "Saved"],
        tradeImpVal: "Import Value ($M)",
        tradeExpVal: "Export / Local Value ($M)"
      },
      ar: {
        appTitle: "محاكي سلسلة توريد الهواتف الذكية",
        reset: "إعادة ضبط",
        fxPrefix: "الصرف:",
        fxUnit: "جنيه/$",
        assumptionsTitle: "الفرضيات الاقتصادية والنماذج القياسية",
        assumptionsSubtitle: "اضغط على أي بطاقة لعرض المعادلة الرياضية وآلية انتقال الأثر",
        c1Title: "انتقال التكلفة (Cost Pass-Through)",
        c1Badge: "الجمارك والصرف",
        c1Sub: "يقيس مدى انتقال صدمات التعريفات الجمركية وسعر الصرف إلى السعر النهائي.",
        c1Mech: "<strong>آلية الانتقال:</strong> انتقال مباشر لتكاليف الاستيراد وسعر الصرف إلى سعر المستهلك.",
        c2Title: "مرونة الطلب السعرية",
        c2Badge: "أثر الكمية",
        c2Sub: "يوضح انخفاض حجم الواردات استجابة لارتفاع الأسعار والتضخم.",
        c2Mech: "<strong>آلية الانتقال:</strong> تراجع الطلب الاستهلاكي مع زيادة الضغط التضخمي على السعر.",
        c3Title: "مؤشر صحة سلسلة التوريد",
        c3Badge: "مؤشر المرونة",
        c3Sub: "مؤشر مركب يعكس احتكاكات التجارة وأثر رافعات التحسين السياساتي واللوجستي.",
        c3Mech: "<strong>آلية الانتقال:</strong> يوازن بين ضغط الجمارك وتخفيف تكلفة الشحن أو الإعفاءات.",
        impactTitle: "تحليل الأثر والأداء الاقتصادي",
        impactSubtitle: "متوسط سعر الهاتف (جنيه) مقابل حجم الواردات (وحدة) ومعدل التعريفة (%)",
        inflationLabel: "الأثر التضخمي المتوقع:",
        revLossLabel: "الإيرادات المتأثرة التقديرية:",
        revUnitM: "مليون جنيه",
        tariffSliderLabel: "تعديل معدل التعريفة الجمركية (%)",
        costSectionTitle: "تحليل هيكل التكلفة والتحسين",
        priceStructTitle: "هيكل سعر المستخدم النهائي (للوحدة)",
        popHint1: "اضغط على الشريحة لإبرازها",
        leg0: "سعر المصنع ($)",
        leg1: "الشحن واللوجستيات (%)",
        leg2: "الجمارك والضرائب (%)",
        leg3: "هامش التوزيع (%)",
        pillFactoryLbl: "سعر المصنع",
        pillShippingLbl: "الشحن",
        pillFinalLbl: "السعر النهائي",
        leversTitle: "رافعات التحسين",
        popHint2: "اضغط على الشريحة للاستكشاف",
        btnShipText: "خفض تكلفة الشحن (10%)",
        btnTariffText: "التفاوض على إعفاءات جمركية (20%)",
        on: "مفعّل",
        off: "متوقف",
        optSavedPrefix: "وفورات السعر النهائي: -",
        optPricePrefix: "السعر النهائي للوحدة:",
        networkSectionTitle: "صحة الشبكة والميزان التجاري",
        healthBoxTitle: "صحة سلسلة التوريد لحظياً",
        healthOpt: "محسّن",
        healthMod: "متوسط",
        healthStr: "تحت ضغط",
        partnersBoxTitle: "أهم شركاء الاستيراد",
        mapEgypt: "مصر",
        mapChina: "الصين (54%)",
        mapVietnam: "فيتنام (28%)",
        tradeBoxTitle: "محاكاة الميزان التجاري (الاستيراد مقابل التصدير)",
        chartImpVol: "حجم الواردات (وحدة)",
        chartAvgPrice: "متوسط سعر الهاتف (جنيه)",
        chartTariffRate: "معدل التعريفة (%)",
        donutStruct: ["سعر المصنع", "الشحن", "الجمارك والضرائب", "الهامش"],
        donutOpt: ["التكلفة الأساسية", "التعريفة", "الموفّر"],
        tradeImpVal: "قيمة الواردات (مليون $)",
        tradeExpVal: "قيمة التصدير / المحلي (مليون $)"
      }
    };

    const CONFIG = {
      factoryUsd: 220,
      marginPct: 12,
      baselineRefPrice: 14000,
      labels12: ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"],
      baseImports: [2050, 2000, 1920, 1750, 1680, 1600, 1520, 1380, 1320, 1410, 1490, 1560],
      basePrices: [13200, 13400, 13650, 13900, 14200, 14600, 15100, 15500, 15800, 16100, 16450, 16800]
    };

    const state = {
      lang: "en",
      tariff: 18,
      fxRate: 50,
      shipLever: false,
      tariffLever: false,
      selectedStructureIdx: null,
      selectedOptIdx: null
    };

    let rafPending = null;

    function clamp(val, min, max) { return Math.max(min, Math.min(max, val)); }
    function buildOffsets(count, activeIdx, popDistance = 12) {
      return Array.from({ length: count }, (_, i) => (i === activeIdx ? popDistance : 0));
    }

    function toggleCard(cardElement) {
      cardElement.classList.toggle("expanded");
    }

    function calculateSimulation(currentState) {
      const tariff = clamp(Number(currentState.tariff) || 18, 5, 45);
      const fxRate = currentState.fxRate === 55 ? 55 : 50;
      const baseShipPct = currentState.shipLever ? 10 : 15;
      const effectiveTariff = currentState.tariffLever ? clamp(+(tariff * 0.8).toFixed(1), 4, 45) : tariff;

      const totalMarkupPct = baseShipPct + effectiveTariff + CONFIG.marginPct;
      const unoptimizedMarkupPct = 15 + tariff + CONFIG.marginPct;

      const rawUnitEgp = CONFIG.factoryUsd * fxRate * (1 + totalMarkupPct / 100);
      const unoptimizedUnitEgp = CONFIG.factoryUsd * fxRate * (1 + unoptimizedMarkupPct / 100);
      const savedEgp = Math.max(0, Math.round(unoptimizedUnitEgp - rawUnitEgp));

      const inflationPct = ((rawUnitEgp - CONFIG.baselineRefPrice) / CONFIG.baselineRefPrice) * 100;
      const revLossM = Math.max(12, Math.round(tariff * 2.5 - (currentState.tariffLever ? 14 : 0)));

      const tariffFactor = 1 + (effectiveTariff - 18) * 0.014;
      const fxFactor = fxRate / 50;
      const volFactor = clamp(1 - (effectiveTariff - 18) * 0.018, 0.45, 1.25);

      const importsSeries = CONFIG.baseImports.map((v) => Math.round(v * volFactor));
      const pricesSeries = CONFIG.basePrices.map((p) => Math.round(p * tariffFactor * fxFactor));
      const tariffsSeries = CONFIG.labels12.map((_, i) => +(effectiveTariff * (0.85 + i * 0.02)).toFixed(1));

      const factoryShare = clamp(100 - (baseShipPct + Math.round(effectiveTariff) + CONFIG.marginPct), 25, 75);
      const savedPct = (currentState.shipLever ? 5 : 0) + (currentState.tariffLever ? Math.round(tariff * 0.2) : 0);

      const health = clamp(Math.round(92 - (effectiveTariff - 18) * 1.4 + (currentState.shipLever ? 4 : 0) + (currentState.tariffLever ? 5 : 0)), 25, 98);
      const projectedImportValueM = Math.round(2100 * volFactor);

      return {
        tariff, fxRate, baseShipPct, effectiveTariff,
        rawUnitEgp: Math.round(rawUnitEgp), savedEgp, inflationPct, revLossM,
        importsSeries, pricesSeries, tariffsSeries,
        structureData: [factoryShare, baseShipPct, Math.round(effectiveTariff), CONFIG.marginPct],
        optData: [clamp(75 - Math.round(effectiveTariff), 40, 85), Math.round(effectiveTariff), savedPct],
        health, projectedImportValueM
      };
    }

    const impactChart = new Chart(document.getElementById("impactChart"), {
      type: "bar",
      data: {
        labels: CONFIG.labels12,
        datasets: [
          { type: "bar", label: "Import Volume (Units)", data: [...CONFIG.baseImports], backgroundColor: "rgba(45, 212, 191, 0.72)", borderRadius: 4, yAxisID: "y" },
          { type: "line", label: "Average Smartphone Price (EGP)", data: [...CONFIG.basePrices], borderColor: "#38bdf8", backgroundColor: "#38bdf8", borderWidth: 2.5, pointRadius: 3, tension: 0.35, yAxisID: "y1" },
          { type: "line", label: "Tariff Rate (%)", data: Array(12).fill(18), borderColor: "#f59e0b", backgroundColor: "#f59e0b", borderWidth: 2, pointRadius: 2.5, tension: 0.3, yAxisID: "y2" }
        ]
      },
      options: {
        responsive: true, maintainAspectRatio: false,
        interaction: { mode: "index", intersect: false },
        plugins: { legend: { labels: { color: "#cbd5e1", boxWidth: 12, font: { size: 11 } } } },
        scales: {
          x: { ticks: { color: "#94a3b8", font: { size: 10 } }, grid: { color: "#1e293b" } },
          y: { position: "left", ticks: { color: "#2dd4bf", font: { size: 10 } }, grid: { color: "#1e293b" }, min: 0, max: 2500 },
          y1: { position: "right", ticks: { color: "#38bdf8", font: { size: 10 } }, grid: { drawOnChartArea: false }, min: 10000, max: 24000 },
          y2: { display: false, min: 0, max: 50 }
        }
      }
    });

    const structureDonut = new Chart(document.getElementById("structureDonut"), {
      type: "doughnut",
      data: {
        labels: ["Factory Price", "Shipping", "Customs & Taxes", "Margin"],
        datasets: [{
          data: [48, 15, 25, 12],
          offset: [0, 0, 0, 0],
          backgroundColor: ["#38bdf8", "#2dd4bf", "#f59e0b", "#818cf8"],
          borderColor: "#161d29",
          borderWidth: 2
        }]
      },
      options: {
        responsive: true, maintainAspectRatio: false, cutout: "60%",
        layout: { padding: 14 }, animation: { duration: 260 },
        onClick: (_, elements) => {
          if (!elements.length) return;
          const idx = elements[0].index;
          state.selectedStructureIdx = state.selectedStructureIdx === idx ? null : idx;
          scheduleUpdate();
        },
        plugins: { legend: { display: false } }
      }
    });

    const optDonut = new Chart(document.getElementById("optDonut"), {
      type: "doughnut",
      data: {
        labels: ["Core Cost", "Tariff", "Saved"],
        datasets: [{
          data: [70, 30, 0],
          offset: [0, 0, 0],
          backgroundColor: ["#2dd4bf", "#f59e0b", "#22c55e"],
          borderColor: "#161d29",
          borderWidth: 2
        }]
      },
      options: {
        responsive: true, maintainAspectRatio: false, cutout: "62%",
        layout: { padding: 14 }, animation: { duration: 260 },
        onClick: (_, elements) => {
          if (!elements.length) return;
          const idx = elements[0].index;
          state.selectedOptIdx = state.selectedOptIdx === idx ? null : idx;
          scheduleUpdate();
        },
        plugins: { legend: { display: false } }
      }
    });

    const tradeBalanceChart = new Chart(document.getElementById("tradeBalanceChart"), {
      type: "bar",
      data: {
        labels: ["Q1", "Q2", "Q3", "Q4", "Proj"],
        datasets: [
          { label: "Import Value ($M)", data: [1450, 1520, 1680, 1890, 2100], backgroundColor: "#38bdf8", borderRadius: 3 },
          { label: "Export / Local Value ($M)", data: [220, 260, 310, 390, 480], backgroundColor: "#f59e0b", borderRadius: 3 }
        ]
      },
      options: {
        responsive: true, maintainAspectRatio: false,
        plugins: { legend: { labels: { color: "#cbd5e1", boxWidth: 10, font: { size: 10 } } } },
        scales: {
          x: { stacked: true, ticks: { color: "#94a3b8", font: { size: 10 } }, grid: { display: false } },
          y: { stacked: true, ticks: { color: "#94a3b8", font: { size: 10 } }, grid: { color: "#1e293b" } }
        }
      }
    });

    function applyLanguage() {
      const t = I18N[state.lang] || I18N.en;
      const isAr = state.lang === "ar";
      document.documentElement.lang = state.lang;
      document.documentElement.dir = isAr ? "rtl" : "ltr";

      document.getElementById("langEnBtn").classList.toggle("active", !isAr);
      document.getElementById("langArBtn").classList.toggle("active", isAr);

      const textMap = {
        appTitle: t.appTitle,
        resetBtn: t.reset,
        assumptionsTitle: t.assumptionsTitle,
        assumptionsSubtitle: t.assumptionsSubtitle,
        c1Title: t.c1Title,
        c1Badge: t.c1Badge,
        c1Sub: t.c1Sub,
        c2Title: t.c2Title,
        c2Badge: t.c2Badge,
        c2Sub: t.c2Sub,
        c3Title: t.c3Title,
        c3Badge: t.c3Badge,
        c3Sub: t.c3Sub,
        impactTitle: t.impactTitle,
        impactSubtitle: t.impactSubtitle,
        inflationLabel: t.inflationLabel,
        revLossLabel: t.revLossLabel,
        tariffSliderLabel: t.tariffSliderLabel,
        costSectionTitle: t.costSectionTitle,
        priceStructTitle: t.priceStructTitle,
        popHint1: t.popHint1,
        leg0: t.leg0,
        leg1: t.leg1,
        leg2: t.leg2,
        leg3: t.leg3,
        pillFactoryLbl: t.pillFactoryLbl,
        pillShippingLbl: t.pillShippingLbl,
        pillFinalLbl: t.pillFinalLbl,
        leversTitle: t.leversTitle,
        popHint2: t.popHint2,
        btnShipText: t.btnShipText,
        btnTariffText: t.btnTariffText,
        networkSectionTitle: t.networkSectionTitle,
        healthBoxTitle: t.healthBoxTitle,
        partnersBoxTitle: t.partnersBoxTitle,
        mapEgypt: t.mapEgypt,
        mapChina: t.mapChina,
        mapVietnam: t.mapVietnam,
        tradeBoxTitle: t.tradeBoxTitle
      };

      Object.entries(textMap).forEach(([id, val]) => {
        const el = document.getElementById(id);
        if (el) el.textContent = val;
      });

      document.getElementById("c1Mech").innerHTML = t.c1Mech;
      document.getElementById("c2Mech").innerHTML = t.c2Mech;
      document.getElementById("c3Mech").innerHTML = t.c3Mech;

      document.getElementById("fxToggleBtn").textContent = `${t.fxPrefix} ${state.fxRate} ${t.fxUnit}`;

      impactChart.data.datasets[0].label = t.chartImpVol;
      impactChart.data.datasets[1].label = t.chartAvgPrice;
      impactChart.data.datasets[2].label = t.chartTariffRate;

      structureDonut.data.labels = t.donutStruct;
      optDonut.data.labels = t.donutOpt;

      tradeBalanceChart.data.datasets[0].label = t.tradeImpVal;
      tradeBalanceChart.data.datasets[1].label = t.tradeExpVal;
    }

    function renderDashboard() {
      applyLanguage();
      const t = I18N[state.lang] || I18N.en;
      const m = calculateSimulation(state);

      document.getElementById("tariffDisplay").textContent = `${m.tariff}%`;
      const infEl = document.getElementById("inflationVal");
      infEl.textContent = `${m.inflationPct >= 0 ? "+" : ""}${m.inflationPct.toFixed(1)}%`;
      infEl.style.color = m.inflationPct > 18 ? "#ef4444" : "#22c55e";
      document.getElementById("revLossVal").textContent = `${m.revLossM} ${t.revUnitM}`;

      impactChart.data.datasets[0].data = m.importsSeries;
      impactChart.data.datasets[0].backgroundColor = m.effectiveTariff > 26 ? "rgba(239, 68, 68, 0.72)" : "rgba(45, 212, 191, 0.72)";
      impactChart.data.datasets[1].data = m.pricesSeries;
      impactChart.data.datasets[2].data = m.tariffsSeries;
      impactChart.update("none");

      structureDonut.data.datasets[0].data = m.structureData;
      structureDonut.data.datasets[0].offset = buildOffsets(4, state.selectedStructureIdx, 11);
      structureDonut.update();

      document.querySelectorAll("#structureLegend li").forEach((li) => {
        const idx = Number(li.dataset.idx);
        li.classList.toggle("selected", state.selectedStructureIdx === idx);
      });

      document.getElementById("pillShipping").textContent = `${m.baseShipPct}%`;
      document.getElementById("pillFinal").textContent = `${m.rawUnitEgp.toLocaleString()} ${state.lang === "ar" ? "جنيه" : "EGP"}`;

      const btnShip = document.getElementById("btnShip");
      btnShip.classList.toggle("active", state.shipLever);
      document.getElementById("btnShipState").textContent = state.shipLever ? t.on : t.off;

      const btnTariff = document.getElementById("btnTariff");
      btnTariff.classList.toggle("active", state.tariffLever);
      document.getElementById("btnTariffState").textContent = state.tariffLever ? t.on : t.off;

      optDonut.data.datasets[0].data = m.optData;
      optDonut.data.datasets[0].offset = buildOffsets(3, state.selectedOptIdx, 10);
      optDonut.update();

      const currLbl = state.lang === "ar" ? "جنيه" : "EGP";
      document.getElementById("optSavingsText").textContent = m.savedEgp > 0
        ? `${t.optSavedPrefix}${m.savedEgp.toLocaleString()} ${currLbl}`
        : `${t.optPricePrefix} ${m.rawUnitEgp.toLocaleString()} ${currLbl}`;

      const arcLen = 251.2;
      const offset = arcLen - (arcLen * m.health) / 100;
      const color = m.health >= 75 ? "#22c55e" : m.health >= 50 ? "#f59e0b" : "#ef4444";
      const arcEl = document.getElementById("gaugeArc");
      arcEl.style.strokeDashoffset = offset;
      arcEl.style.stroke = color;

      const angleRad = Math.PI * (1 - m.health / 100);
      const nx = 100 + 68 * Math.cos(angleRad);
      const ny = 100 - 68 * Math.sin(angleRad);
      const needle = document.getElementById("gaugeNeedle");
      needle.setAttribute("x2", nx.toFixed(1));
      needle.setAttribute("y2", ny.toFixed(1));

      const statusText = m.health >= 75 ? t.healthOpt : m.health >= 50 ? t.healthMod : t.healthStr;
      const healthLbl = document.getElementById("healthLabel");
      healthLbl.textContent = `${statusText} (${m.health}%)`;
      healthLbl.style.color = color;

      tradeBalanceChart.data.datasets[0].data = [1450, 1520, 1680, 1890, m.projectedImportValueM];
      tradeBalanceChart.update("none");
    }

    function scheduleUpdate() {
      if (rafPending) cancelAnimationFrame(rafPending);
      rafPending = requestAnimationFrame(renderDashboard);
    }

    document.getElementById("langEnBtn").addEventListener("click", () => {
      state.lang = "en";
      scheduleUpdate();
    });

    document.getElementById("langArBtn").addEventListener("click", () => {
      state.lang = "ar";
      scheduleUpdate();
    });

    document.getElementById("tariffSlider").addEventListener("input", (e) => {
      state.tariff = Number(e.target.value);
      scheduleUpdate();
    });

    document.querySelectorAll("#structureLegend li").forEach((li) => {
      li.addEventListener("click", () => {
        const idx = Number(li.dataset.idx);
        state.selectedStructureIdx = state.selectedStructureIdx === idx ? null : idx;
        scheduleUpdate();
      });
    });

    document.getElementById("btnShip").addEventListener("click", () => {
      state.shipLever = !state.shipLever;
      scheduleUpdate();
    });

    document.getElementById("btnTariff").addEventListener("click", () => {
      state.tariffLever = !state.tariffLever;
      scheduleUpdate();
    });

    document.getElementById("fxToggleBtn").addEventListener("click", () => {
      state.fxRate = state.fxRate === 50 ? 55 : 50;
      scheduleUpdate();
    });

    document.getElementById("resetBtn").addEventListener("click", () => {
      state.tariff = 18;
      state.fxRate = 50;
      state.shipLever = false;
      state.tariffLever = false;
      state.selectedStructureIdx = null;
      state.selectedOptIdx = null;
      document.getElementById("tariffSlider").value = 18;
      document.querySelectorAll(".assumption-card").forEach((c) => c.classList.remove("expanded"));
      scheduleUpdate();
    });

    renderDashboard();
  </script>
</body>
</html>
