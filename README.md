[nurse_retention_calculator.html](https://github.com/user-attachments/files/34283736/nurse_retention_calculator.html)
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Nurse Turnover & Retention Calculator</title>
  <style>
    :root {
      font-family: system-ui, -apple-system, Segoe UI, sans-serif;
      color: #172235;
      background: #f5f7fa;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      padding: 24px;
    }

    main {
      max-width: 980px;
      margin: auto;
    }

    h1 {
      font-size: clamp(1.5rem, 4vw, 2.4rem);
      margin-bottom: 8px;
    }

    p {
      line-height: 1.5;
    }

    .sub {
      color: #546174;
      margin-top: 0;
    }

    .panel {
      background: white;
      border: 1px solid #dce2e9;
      border-radius: 14px;
      padding: 22px;
      margin-top: 18px;
      box-shadow: 0 3px 12px #17223509;
    }

    .layout {
      display: grid;
      grid-template-columns: minmax(250px, 1fr) minmax(320px, 1.4fr);
      gap: 18px;
    }

    .control {
      margin: 0 0 24px;
    }

    .control-head {
      display: flex;
      justify-content: space-between;
      gap: 12px;
      font-weight: 600;
    }

    .control output {
      font-variant-numeric: tabular-nums;
      color: #175c7a;
    }

    input[type=range] {
      width: 100%;
      accent-color: #176d91;
      min-height: 36px;
    }

    .limits {
      display: flex;
      justify-content: space-between;
      font-size: .75rem;
      color: #6d7887;
    }

    .metrics {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
    }

    .metric {
      background: #f1f6fa;
      padding: 14px;
      border-radius: 10px;
    }

    .metric strong {
      font-size: clamp(1.25rem, 3vw, 1.9rem);
      display: block;
      margin-top: 6px;
      font-variant-numeric: tabular-nums;
    }

    .metric small {
      color: #4d6072;
    }

    .bars {
      margin-top: 24px;
    }

    .bar-row {
      margin: 18px 0;
    }

    .bar-label {
      display: flex;
      justify-content: space-between;
      gap: 8px;
      font-size: .9rem;
      margin-bottom: 7px;
    }

    .track {
      height: 27px;
      border-radius: 6px;
      background: #e8edf2;
      overflow: hidden;
    }

    .fill {
      height: 100%;
      transition: width .12s ease;
    }

    .current {
      background: #b95a5a;
    }

    .target {
      background: #33896d;
    }

    .note {
      font-size: .85rem;
      color: #536274;
    }

    button {
      background: #fff;
      border: 1px solid #aeb9c5;
      border-radius: 8px;
      padding: 10px 15px;
      cursor: pointer;
      font-weight: 600;
    }

    button:hover {
      background: #edf4f8;
    }

    footer {
      font-size: .8rem;
      color: #536274;
      margin: 20px 0;
    }

    @media (max-width: 740px) {
      body { padding: 12px; }
      .layout { grid-template-columns: 1fr; }
      .panel { padding: 17px; }
      .metrics { grid-template-columns: 1fr 1fr; }
    }
  </style>
</head>
<body>
  <main>
    <h1>Nurse Turnover & Retention Calculator</h1>
    <p class="sub">Explore how reducing annual RN turnover can lower replacement costs. Move the sliders to model your own hospital.</p>

    <div class="layout">
      <section class="panel" aria-label="Calculator controls">
        <h2>Adjust assumptions</h2>

        <div class="control">
          <div class="control-head">
            <label for="staff">Employed RNs</label>
            <output id="staffOut" for="staff"></output>
          </div>
          <input id="staff" type="range" min="100" max="2000" step="50" value="500">
          <div class="limits">
            <span>100</span>
            <span>2,000</span>
          </div>
        </div>

        <div class="control">
          <div class="control-head">
            <label for="turnover">Current annual turnover</label>
            <output id="turnoverOut" for="turnover"></output>
          </div>
          <input id="turnover" type="range" min="5" max="35" step="1" value="20">
          <div class="limits">
            <span>5%</span>
            <span>35%</span>
          </div>
        </div>

        <div class="control">
          <div class="control-head">
            <label for="target">Target annual turnover</label>
            <output id="targetOut" for="target"></output>
          </div>
          <input id="target" type="range" min="0" max="20" step="1" value="10">
          <div class="limits">
            <span>0%</span>
            <span id="targetMax">20%</span>
          </div>
        </div>

        <div class="control">
          <div class="control-head">
            <label for="cost">Cost per RN departure</label>
            <output id="costOut" for="cost"></output>
          </div>
          <input id="cost" type="range" min="10000" max="120000" step="1000" value="60000">
          <div class="limits">
            <span>$10,000</span>
            <span>$120,000</span>
          </div>
        </div>

        <button id="reset" type="button">Reset sliders</button>
      </section>

      <section class="panel" aria-label="Calculated results">
        <h2>Annual financial impact</h2>

        <div class="metrics">
          <div class="metric">
            <small>Avoided RN departures</small>
            <strong id="avoided" aria-live="polite"></strong>
          </div>

          <div class="metric">
            <small>Estimated annual savings</small>
            <strong id="savings" aria-live="polite"></strong>
          </div>
        </div>

        <div class="bars">
          <h3>Annual turnover costs</h3>

          <div class="bar-row">
            <div class="bar-label">
              <span>Current turnover</span>
              <strong id="currentCost"></strong>
            </div>
            <div class="track">
              <div class="fill current" id="currentBar"></div>
            </div>
          </div>

          <div class="bar-row">
            <div class="bar-label">
              <span>Target turnover</span>
              <strong id="targetCost"></strong>
            </div>
            <div class="track">
              <div class="fill target" id="targetBar"></div>
            </div>
          </div>
        </div>

        <p id="interpretation"></p>
        <p class="note">
          Illustrative gross savings only. The model assumes constant RN staffing and full replacement of departing nurses.
          It does not subtract retention intervention costs or establish that a specific policy will achieve the target.
        </p>
      </section>
    </div>

    <footer>Educational calculator · Cost per departure is an editable assumption; default $60,000 is illustrative, not a verified current national estimate.</footer>
  </main>

  <script>
    const ids = ['staff', 'turnover', 'target', 'cost'];
    const el = id => document.getElementById(id);
    const usd = new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD', maximumFractionDigits: 0 });
    const num = new Intl.NumberFormat('en-US', { maximumFractionDigits: 1 });

    function update() {
      const staff = +el('staff').value;
      const turnover = +el('turnover').value;

      el('target').max = turnover;
      if (+el('target').value > turnover) el('target').value = turnover;

      const target = +el('target').value;
      const cost = +el('cost').value;

      el('staffOut').textContent = num.format(staff);
      el('turnoverOut').textContent = turnover + '%';
      el('targetOut').textContent = target + '%';
      el('targetMax').textContent = turnover + '%';
      el('costOut').textContent = usd.format(cost);

      const currentCost = staff * turnover / 100 * cost;
      const targetCost = staff * target / 100 * cost;
      const avoided = staff * (turnover - target) / 100;

      el('avoided').textContent = num.format(avoided);
      el('savings').textContent = usd.format(currentCost - targetCost);
      el('currentCost').textContent = usd.format(currentCost);
      el('targetCost').textContent = usd.format(targetCost);

      el('currentBar').style.width = '100%';
      el('targetBar').style.width = (currentCost ? targetCost / currentCost * 100 : 0) + '%';

      el('interpretation').textContent =
        `Reducing turnover from ${turnover}% to ${target}% among ${num.format(staff)} RNs would avoid approximately ${num.format(avoided)} departures and ${usd.format(currentCost - targetCost)} in gross annual turnover costs.`;
    }

    ids.forEach(id => el(id).addEventListener('input', update));

    el('reset').addEventListener('click', () => {
      Object.entries({
        staff: 500,
        turnover: 20,
        target: 10,
        cost: 60000
      }).forEach(([id, value]) => el(id).value = value);

      update();
    });

    update();
  </script>
</body>
</html>
