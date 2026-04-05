<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Heart Disease Prediction — ML Notebook</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Mono:wght@400;500&family=Instrument+Sans:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0d0f14;
    --surface: #13161d;
    --surface2: #1a1e28;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.13);
    --text: #e8eaf0;
    --muted: #7a7f8e;
    --accent: #e05c3a;
    --accent2: #4a9eff;
    --accent3: #3ecf8e;
    --accent4: #f5c842;
    --serif: 'DM Serif Display', Georgia, serif;
    --sans: 'Instrument Sans', sans-serif;
    --mono: 'DM Mono', monospace;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    font-size: 15px;
    line-height: 1.75;
    min-height: 100vh;
  }

  /* ── HERO ── */
  .hero {
    position: relative;
    padding: 80px 48px 64px;
    border-bottom: 1px solid var(--border);
    overflow: hidden;
  }
  .hero::before {
    content: '';
    position: absolute;
    inset: 0;
    background:
      radial-gradient(ellipse 60% 50% at 80% 30%, rgba(224,92,58,0.12) 0%, transparent 70%),
      radial-gradient(ellipse 40% 40% at 20% 80%, rgba(74,158,255,0.09) 0%, transparent 60%);
    pointer-events: none;
  }
  .hero-tag {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    font-family: var(--mono);
    font-size: 11px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--accent);
    border: 1px solid rgba(224,92,58,0.3);
    background: rgba(224,92,58,0.08);
    padding: 5px 12px;
    border-radius: 2px;
    margin-bottom: 24px;
  }
  .hero-tag::before { content: '♥'; }
  h1 {
    font-family: var(--serif);
    font-size: clamp(38px, 6vw, 64px);
    font-weight: 400;
    line-height: 1.1;
    letter-spacing: -0.01em;
    margin-bottom: 20px;
    max-width: 700px;
  }
  h1 em {
    font-style: italic;
    color: var(--accent);
  }
  .hero-sub {
    color: var(--muted);
    max-width: 520px;
    font-size: 16px;
    line-height: 1.65;
    margin-bottom: 36px;
  }
  .hero-stats {
    display: flex;
    gap: 0;
    border: 1px solid var(--border2);
    border-radius: 4px;
    width: fit-content;
    overflow: hidden;
  }
  .stat {
    padding: 16px 28px;
    border-right: 1px solid var(--border2);
    text-align: center;
  }
  .stat:last-child { border-right: none; }
  .stat-val {
    font-family: var(--serif);
    font-size: 28px;
    color: var(--text);
    display: block;
    line-height: 1;
    margin-bottom: 4px;
  }
  .stat-lbl {
    font-family: var(--mono);
    font-size: 10px;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--muted);
  }

  /* ── LAYOUT ── */
  .container { max-width: 960px; margin: 0 auto; padding: 0 48px; }

  /* ── SECTIONS ── */
  section { padding: 56px 48px; border-bottom: 1px solid var(--border); }
  .section-label {
    font-family: var(--mono);
    font-size: 10px;
    letter-spacing: 0.14em;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 24px;
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .section-label::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border2);
    max-width: 80px;
  }
  h2 {
    font-family: var(--serif);
    font-size: 30px;
    font-weight: 400;
    margin-bottom: 28px;
    line-height: 1.2;
  }
  h3 {
    font-family: var(--sans);
    font-size: 13px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    color: var(--muted);
    margin: 32px 0 12px;
  }
  p { color: #b0b5c3; margin-bottom: 14px; }

  /* ── FEATURE TABLE ── */
  .feature-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 4px;
    overflow: hidden;
  }
  .feature-item {
    background: var(--surface);
    padding: 14px 18px;
    display: flex;
    align-items: flex-start;
    gap: 12px;
  }
  .feature-item:hover { background: var(--surface2); }
  .f-name {
    font-family: var(--mono);
    font-size: 12px;
    color: var(--accent2);
    min-width: 110px;
    padding-top: 2px;
  }
  .f-desc { font-size: 13px; color: var(--muted); line-height: 1.5; }
  .f-type {
    margin-left: auto;
    font-family: var(--mono);
    font-size: 10px;
    padding: 2px 7px;
    border-radius: 2px;
    white-space: nowrap;
    align-self: center;
  }
  .f-type.num { background: rgba(74,158,255,0.12); color: var(--accent2); }
  .f-type.cat { background: rgba(62,207,142,0.12); color: var(--accent3); }

  /* ── STEPS ── */
  .steps { display: flex; flex-direction: column; gap: 1px; }
  .step {
    display: grid;
    grid-template-columns: 64px 1fr;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    overflow: hidden;
    transition: border-color .2s;
  }
  .step:hover { border-color: var(--border2); }
  .step-num {
    background: var(--surface2);
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: var(--serif);
    font-size: 22px;
    color: var(--muted);
    border-right: 1px solid var(--border);
  }
  .step-content { padding: 18px 22px; }
  .step-title {
    font-weight: 600;
    font-size: 14px;
    color: var(--text);
    margin-bottom: 5px;
  }
  .step-desc { font-size: 13px; color: var(--muted); line-height: 1.55; margin: 0; }
  .step-sub {
    margin-top: 10px;
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
  }
  .chip {
    font-family: var(--mono);
    font-size: 11px;
    padding: 3px 9px;
    border-radius: 2px;
    border: 1px solid var(--border2);
    color: var(--muted);
  }
  .chip.green { border-color: rgba(62,207,142,0.25); color: var(--accent3); background: rgba(62,207,142,0.06); }
  .chip.blue  { border-color: rgba(74,158,255,0.25); color: var(--accent2); background: rgba(74,158,255,0.06); }
  .chip.red   { border-color: rgba(224,92,58,0.25);  color: var(--accent);  background: rgba(224,92,58,0.06); }
  .chip.gold  { border-color: rgba(245,200,66,0.25); color: var(--accent4); background: rgba(245,200,66,0.06); }

  /* ── RESULTS TABLE ── */
  .results-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 14px;
  }
  .results-table th {
    font-family: var(--mono);
    font-size: 10px;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--muted);
    text-align: left;
    padding: 10px 16px;
    border-bottom: 1px solid var(--border2);
    font-weight: 400;
  }
  .results-table td {
    padding: 14px 16px;
    border-bottom: 1px solid var(--border);
    color: #b0b5c3;
  }
  .results-table tr:last-child td { border-bottom: none; }
  .results-table tr:hover td { background: var(--surface2); }
  .results-table tr.best td { background: rgba(62,207,142,0.04); }
  .model-name { font-weight: 500; color: var(--text); font-size: 14px; }
  .score-bar-wrap { display: flex; align-items: center; gap: 10px; }
  .score-bar-bg { flex: 1; max-width: 120px; height: 4px; background: var(--surface2); border-radius: 2px; }
  .score-bar { height: 100%; border-radius: 2px; }
  .score-val { font-family: var(--mono); font-size: 13px; color: var(--text); }
  .badge {
    display: inline-block;
    font-family: var(--mono);
    font-size: 10px;
    padding: 2px 8px;
    border-radius: 2px;
    text-transform: uppercase;
    letter-spacing: 0.05em;
  }
  .badge.yes { background: rgba(224,92,58,0.12); color: var(--accent); }
  .badge.no  { background: rgba(62,207,142,0.10); color: var(--accent3); }
  .badge.best { background: rgba(245,200,66,0.12); color: var(--accent4); }

  /* ── OBSERVATIONS ── */
  .obs-list { display: flex; flex-direction: column; gap: 14px; }
  .obs {
    display: grid;
    grid-template-columns: 36px 1fr;
    gap: 16px;
    align-items: start;
    padding: 18px 20px;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    border-left: 3px solid var(--border2);
    transition: border-left-color .2s;
  }
  .obs:hover { border-left-color: var(--accent); }
  .obs-n {
    font-family: var(--serif);
    font-size: 24px;
    color: var(--border2);
    line-height: 1;
    padding-top: 2px;
    text-align: center;
  }
  .obs-text { font-size: 14px; color: #b0b5c3; line-height: 1.65; }
  .obs-text strong { color: var(--text); font-weight: 600; }
  .obs-text code {
    font-family: var(--mono);
    font-size: 12px;
    background: var(--surface2);
    border: 1px solid var(--border2);
    padding: 1px 6px;
    border-radius: 2px;
    color: var(--accent2);
  }

  /* ── INSTALL ── */
  .code-block {
    background: var(--surface);
    border: 1px solid var(--border2);
    border-radius: 4px;
    padding: 20px 24px;
    font-family: var(--mono);
    font-size: 13px;
    color: var(--accent3);
    position: relative;
    overflow-x: auto;
    line-height: 1.8;
  }
  .code-block .comment { color: var(--muted); }
  .code-block .cmd { color: var(--accent2); }
  .code-block .path { color: var(--accent); }

  /* ── FILE TREE ── */
  .file-tree {
    background: var(--surface);
    border: 1px solid var(--border2);
    border-radius: 4px;
    padding: 20px 24px;
    font-family: var(--mono);
    font-size: 13px;
    line-height: 2;
  }
  .ft-dir { color: var(--accent2); }
  .ft-file { color: var(--text); }
  .ft-note { color: var(--muted); font-size: 12px; }

  /* ── METRICS ── */
  .metrics-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 12px;
  }
  .metric-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 18px;
    border-top: 2px solid var(--border2);
    transition: border-top-color .2s;
  }
  .metric-card:hover { border-top-color: var(--accent); }
  .metric-card:nth-child(2):hover { border-top-color: var(--accent2); }
  .metric-card:nth-child(3):hover { border-top-color: var(--accent3); }
  .metric-card:nth-child(4):hover { border-top-color: var(--accent4); }
  .metric-card:nth-child(5):hover { border-top-color: #b47cff; }
  .m-name { font-weight: 600; font-size: 13px; color: var(--text); margin-bottom: 6px; }
  .m-desc { font-size: 12px; color: var(--muted); line-height: 1.5; }

  /* ── FOOTER ── */
  footer {
    padding: 32px 48px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    color: var(--muted);
    font-size: 12px;
    font-family: var(--mono);
  }
  footer span { opacity: 0.5; }

  @media (max-width: 640px) {
    .hero, section, footer { padding-left: 24px; padding-right: 24px; }
    .feature-grid { grid-template-columns: 1fr; }
    .hero-stats { flex-direction: column; }
    .stat { border-right: none; border-bottom: 1px solid var(--border2); }
    .stat:last-child { border-bottom: none; }
    h1 { font-size: 34px; }
  }
</style>
</head>
<body>

<!-- HERO -->
<header class="hero">
  <div class="hero-tag">Machine Learning · Binary Classification</div>
  <h1>Heart Disease<br><em>Prediction</em></h1>
  <p class="hero-sub">A supervised learning notebook comparing four ML models on clinical heart data — with hyperparameter tuning, cross-validation, and full evaluation metrics.</p>
  <div class="hero-stats">
    <div class="stat">
      <span class="stat-val">918</span>
      <span class="stat-lbl">Patients</span>
    </div>
    <div class="stat">
      <span class="stat-val">11</span>
      <span class="stat-lbl">Features</span>
    </div>
    <div class="stat">
      <span class="stat-val">55%</span>
      <span class="stat-lbl">Positive rate</span>
    </div>
    <div class="stat">
      <span class="stat-val">4</span>
      <span class="stat-lbl">Models</span>
    </div>
  </div>
</header>

<!-- DATASET -->
<section>
  <div class="section-label">01 — Dataset</div>
  <h2>Features & Target</h2>
  <p>The dataset combines five heart disease databases and targets the binary variable <strong style="color:var(--text)">HeartDisease</strong> — whether a patient has heart disease (1) or not (0).</p>

  <div class="feature-grid">
    <div class="feature-item">
      <span class="f-name">Age</span>
      <span class="f-desc">Patient age in years</span>
      <span class="f-type num">numeric</span>
    </div>
    <div class="feature-item">
      <span class="f-name">Sex</span>
      <span class="f-desc">M = male, F = female</span>
      <span class="f-type cat">categorical</span>
    </div>
    <div class="feature-item">
      <span class="f-name">ChestPainType</span>
      <span class="f-desc">ATA, ASY, NAP, or TA</span>
      <span class="f-type cat">categorical</span>
    </div>
    <div class="feature-item">
      <span class="f-name">RestingBP</span>
      <span class="f-desc">Resting blood pressure (mm Hg)</span>
      <span class="f-type num">numeric</span>
    </div>
    <div class="feature-item">
      <span class="f-name">Cholesterol</span>
      <span class="f-desc">Serum cholesterol (mm/dl)</span>
      <span class="f-type num">numeric</span>
    </div>
    <div class="feature-item">
      <span class="f-name">FastingBS</span>
      <span class="f-desc">Blood sugar &gt; 120 mg/dl (1 = true)</span>
      <span class="f-type cat">categorical</span>
    </div>
    <div class="feature-item">
      <span class="f-name">RestingECG</span>
      <span class="f-desc">Normal, ST, or LVH</span>
      <span class="f-type cat">categorical</span>
    </div>
    <div class="feature-item">
      <span class="f-name">MaxHR</span>
      <span class="f-desc">Maximum heart rate achieved</span>
      <span class="f-type num">numeric</span>
    </div>
    <div class="feature-item">
      <span class="f-name">ExerciseAngina</span>
      <span class="f-desc">Exercise-induced angina (Y/N)</span>
      <span class="f-type cat">categorical</span>
    </div>
    <div class="feature-item">
      <span class="f-name">Oldpeak</span>
      <span class="f-desc">ST depression from exercise</span>
      <span class="f-type num">numeric</span>
    </div>
    <div class="feature-item">
      <span class="f-name">ST_Slope</span>
      <span class="f-desc">Slope of peak exercise ST segment</span>
      <span class="f-type cat">categorical</span>
    </div>
    <div class="feature-item">
      <span class="f-name">HeartDisease</span>
      <span class="f-desc">Target — 0 = no disease, 1 = disease</span>
      <span class="f-type num" style="background:rgba(224,92,58,0.12);color:var(--accent)">target</span>
    </div>
  </div>
</section>

<!-- SETUP -->
<section>
  <div class="section-label">02 — Setup</div>
  <h2>Installation & Structure</h2>

  <h3>Requirements</h3>
  <div class="code-block">
    <span class="comment"># Install all dependencies</span><br>
    <span class="cmd">pip install</span> pandas numpy matplotlib scikit-learn<br><br>
    <span class="comment"># Python 3.8+ recommended</span>
  </div>

  <h3>Project files</h3>
  <div class="file-tree">
    <div><span class="ft-dir">heart-disease-ml/</span></div>
    <div>&nbsp;&nbsp;├── <span class="ft-file">heart.csv</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ft-note">← dataset (918 rows)</span></div>
    <div>&nbsp;&nbsp;├── <span class="ft-file">heart_disease_ml.py</span> &nbsp;<span class="ft-note">← main notebook</span></div>
    <div>&nbsp;&nbsp;└── <span class="ft-file">README.html</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ft-note">← this file</span></div>
  </div>

  <h3>How to run</h3>
  <div class="code-block">
    <span class="comment"># Open Jupyter in the project folder</span><br>
    <span class="cmd">jupyter notebook</span><br><br>
    <span class="comment"># Then open heart_disease_ml.py</span><br>
    <span class="comment"># Run all cells: Kernel → Restart &amp; Run All</span>
  </div>
</section>

<!-- NOTEBOOK WALKTHROUGH -->
<section>
  <div class="section-label">03 — Notebook</div>
  <h2>Step-by-step Walkthrough</h2>

  <div class="steps">
    <div class="step">
      <div class="step-num">1</div>
      <div class="step-content">
        <div class="step-title">Load & Explore</div>
        <p class="step-desc">Reads heart.csv, checks shape and missing values, plots target class balance and age distribution by class.</p>
        <div class="step-sub"><span class="chip">No missing values</span><span class="chip green">55% positive class</span></div>
      </div>
    </div>
    <div class="step">
      <div class="step-num">2</div>
      <div class="step-content">
        <div class="step-title">Preprocessing</div>
        <p class="step-desc">Label encodes binary columns (Sex, ExerciseAngina), one-hot encodes multi-class columns (ChestPainType, RestingECG, ST_Slope), splits 80/20 with stratification, then applies StandardScaler only to regression models.</p>
        <div class="step-sub">
          <span class="chip blue">LabelEncoder</span>
          <span class="chip blue">get_dummies()</span>
          <span class="chip green">train_test_split stratify=y</span>
          <span class="chip">StandardScaler</span>
        </div>
      </div>
    </div>
    <div class="step">
      <div class="step-num">3</div>
      <div class="step-content">
        <div class="step-title">Decision Tree — max_depth tuning + cross-validation</div>
        <p class="step-desc">Loops depths 1–20, plots train vs. test accuracy to find the overfitting crossover. Runs 5-fold StratifiedKFold CV with the best depth. Produces a tree plot (top 3 levels), feature importance chart, and confusion matrix.</p>
        <div class="step-sub">
          <span class="chip green">max_depth search 1–20</span>
          <span class="chip green">StratifiedKFold (k=5)</span>
          <span class="chip">plot_tree()</span>
          <span class="chip">No scaling needed</span>
        </div>
      </div>
    </div>
    <div class="step">
      <div class="step-num">4</div>
      <div class="step-content">
        <div class="step-title">Linear Regression — baseline</div>
        <p class="step-desc">Fits a linear regressor and thresholds predictions at 0.5 to produce class labels. Used only as a performance floor — not well-suited for binary classification. Coefficient plot shows feature influence direction.</p>
        <div class="step-sub"><span class="chip red">Baseline only</span><span class="chip">Requires scaling</span></div>
      </div>
    </div>
    <div class="step">
      <div class="step-num">5</div>
      <div class="step-content">
        <div class="step-title">Logistic Regression — GridSearchCV on C</div>
        <p class="step-desc">Searches 15 values of regularisation parameter C on a log scale (10⁻⁵ to 10⁸) using 5-fold CV. Plots mean CV accuracy vs. C with a confidence band. Trains final model with best C, produces odds ratio chart and ROC curve.</p>
        <div class="step-sub">
          <span class="chip blue">GridSearchCV</span>
          <span class="chip blue">np.logspace(−5, 8, 15)</span>
          <span class="chip gold">Best C = 0.006105</span>
          <span class="chip gold">CV score = 0.853</span>
        </div>
      </div>
    </div>
    <div class="step">
      <div class="step-num">6</div>
      <div class="step-content">
        <div class="step-title">Naive Bayes — GaussianNB</div>
        <p class="step-desc">Trains GaussianNB assuming numeric features follow a Gaussian distribution per class. No scaling required. Produces classification report and confusion matrix.</p>
        <div class="step-sub"><span class="chip green">No scaling needed</span><span class="chip">Independence assumption</span></div>
      </div>
    </div>
    <div class="step">
      <div class="step-num">7</div>
      <div class="step-content">
        <div class="step-title">Model Comparison</div>
        <p class="step-desc">Collects accuracy and AUC-ROC for all four models. Prints a ranked table, side-by-side bar charts, and overlaid ROC curves on one plot.</p>
        <div class="step-sub"><span class="chip">Accuracy</span><span class="chip">AUC-ROC</span><span class="chip">ROC curves</span></div>
      </div>
    </div>
  </div>
</section>

<!-- RESULTS -->
<section>
  <div class="section-label">04 — Results</div>
  <h2>Model Performance</h2>

  <table class="results-table">
    <thead>
      <tr>
        <th>Model</th>
        <th>Accuracy</th>
        <th>AUC-ROC</th>
        <th>Scaling</th>
        <th>Best for</th>
      </tr>
    </thead>
    <tbody>
      <tr class="best">
        <td><span class="model-name">Logistic Regression</span></td>
        <td>
          <div class="score-bar-wrap">
            <div class="score-bar-bg"><div class="score-bar" style="width:87%;background:var(--accent3)"></div></div>
            <span class="score-val">~87%</span>
          </div>
        </td>
        <td><span class="score-val" style="font-family:var(--mono);font-size:13px">~0.93</span></td>
        <td><span class="badge yes">Yes</span></td>
        <td style="color:var(--muted);font-size:13px">Accuracy + probabilities</td>
      </tr>
      <tr>
        <td><span class="model-name">Decision Tree</span></td>
        <td>
          <div class="score-bar-wrap">
            <div class="score-bar-bg"><div class="score-bar" style="width:83%;background:var(--accent2)"></div></div>
            <span class="score-val">~83%</span>
          </div>
        </td>
        <td><span class="score-val" style="font-family:var(--mono);font-size:13px">~0.88</span></td>
        <td><span class="badge no">No</span></td>
        <td style="color:var(--muted);font-size:13px">Interpretability</td>
      </tr>
      <tr>
        <td><span class="model-name">Naive Bayes</span></td>
        <td>
          <div class="score-bar-wrap">
            <div class="score-bar-bg"><div class="score-bar" style="width:82%;background:var(--accent4)"></div></div>
            <span class="score-val">~82%</span>
          </div>
        </td>
        <td><span class="score-val" style="font-family:var(--mono);font-size:13px">~0.88</span></td>
        <td><span class="badge no">No</span></td>
        <td style="color:var(--muted);font-size:13px">Speed + small data</td>
      </tr>
      <tr>
        <td><span class="model-name">Linear Regression</span></td>
        <td>
          <div class="score-bar-wrap">
            <div class="score-bar-bg"><div class="score-bar" style="width:80%;background:var(--muted)"></div></div>
            <span class="score-val">~80%</span>
          </div>
        </td>
        <td><span class="score-val" style="font-family:var(--mono);font-size:13px">~0.87</span></td>
        <td><span class="badge yes">Yes</span></td>
        <td style="color:var(--muted);font-size:13px">Baseline only</td>
      </tr>
    </tbody>
  </table>
</section>

<!-- OBSERVATIONS -->
<section>
  <div class="section-label">05 — Observations</div>
  <h2>Key Findings</h2>

  <div class="obs-list">
    <div class="obs">
      <div class="obs-n">1</div>
      <div class="obs-text"><strong>ST_Slope, ChestPainType, and Oldpeak</strong> consistently rank as the most important features — aligning with their clinical significance as ECG and symptom indicators of cardiac stress during exercise.</div>
    </div>
    <div class="obs">
      <div class="obs-n">2</div>
      <div class="obs-text"><strong>Logistic Regression performed best overall.</strong> GridSearchCV identified the optimal regularisation as <code>C = 0.006105</code> with a best cross-validation score of <code>0.853</code> — far below the default C = 1.0, indicating strong regularisation was needed to handle multicollinearity from one-hot encoded columns.</div>
    </div>
    <div class="obs">
      <div class="obs-n">3</div>
      <div class="obs-text">The <strong>Decision Tree is the most clinically useful</strong> model despite slightly lower accuracy — its rules can be read directly by a clinician (e.g. <em>"flat ST slope AND Oldpeak &gt; 1.5 → high risk"</em>), which often matters more than a 2–3% accuracy gap.</div>
    </div>
    <div class="obs">
      <div class="obs-n">4</div>
      <div class="obs-text"><strong>Naive Bayes competed well</strong> despite its independence assumption, confirming that individual features carry enough signal about heart disease risk to be useful even without modelling feature interactions.</div>
    </div>
    <div class="obs">
      <div class="obs-n">5</div>
      <div class="obs-text">For clinical deployment, <strong>Recall for the Disease class should be prioritised</strong> over raw accuracy. A missed diagnosis (false negative) is far more costly than a false alarm, so the decision threshold should be tuned accordingly.</div>
    </div>
  </div>
</section>

<!-- METRICS -->
<section>
  <div class="section-label">06 — Evaluation</div>
  <h2>Metrics Used</h2>
  <div class="metrics-grid">
    <div class="metric-card">
      <div class="m-name">Accuracy</div>
      <div class="m-desc">Overall % correct. Reliable here since classes are near-balanced at 55/45.</div>
    </div>
    <div class="metric-card">
      <div class="m-name">Precision</div>
      <div class="m-desc">Of all predicted disease cases, how many were actually disease?</div>
    </div>
    <div class="metric-card">
      <div class="m-name">Recall</div>
      <div class="m-desc">Of all actual disease cases, how many did the model catch? Critical in medical diagnosis.</div>
    </div>
    <div class="metric-card">
      <div class="m-name">F1 Score</div>
      <div class="m-desc">Harmonic mean of precision and recall — a useful single summary metric.</div>
    </div>
    <div class="metric-card">
      <div class="m-name">AUC-ROC</div>
      <div class="m-desc">Threshold-independent ranking quality across all possible decision boundaries.</div>
    </div>
  </div>
</section>

<footer>
  <span>Heart Disease Prediction — ML Notebook</span>
  <span>scikit-learn · pandas · matplotlib</span>
</footer>

</body>
</html>
