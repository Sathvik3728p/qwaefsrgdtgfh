<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>FinSight — Smart Finance Dashboard</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <style>
    :root{
      --bg:#0a0f1c;
      --card:#0f1629;
      --card-2:#121a30;
      --accent:#7c5cff;
      --accent-2:#29d3c2;
      --warning:#f59e0b;
      --danger:#ef4444;
      --success:#22c55e;
      --muted:#8ea0c7;
      --grid: rgba(255,255,255,0.04);
    }
    html,body { height:100%; }
    body { font-family: 'Inter', system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, sans-serif; background: radial-gradient(1200px 800px at 80% -10%, rgba(124,92,255,0.12), transparent 60%), radial-gradient(900px 600px at -10% 20%, rgba(41,211,194,0.10), transparent 55%), var(--bg); color: #e6ecff; }
    /* Subtle animated grid backdrop */
    .grid-bg:before{
      content:""; position:absolute; inset:0;
      background-image: linear-gradient(to right, var(--grid) 1px, transparent 1px),
                        linear-gradient(to bottom, var(--grid) 1px, transparent 1px);
      background-size: 40px 40px;
      mask-image: radial-gradient(ellipse at center, rgba(0,0,0,.6) 0%, rgba(0,0,0,.2) 60%, rgba(0,0,0,0) 100%);
      pointer-events:none;
    }
    /* Glass cards */
    .card{ background: linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.02)); border:1px solid rgba(255,255,255,0.08); box-shadow: 0 10px 30px rgba(0,0,0,0.35), inset 0 1px 0 rgba(255,255,255,0.05); backdrop-filter: blur(6px); }
    .card-strong{ background: linear-gradient(180deg, rgba(255,255,255,0.06), rgba(255,255,255,0.03)); border:1px solid rgba(255,255,255,0.10); }
    /* Pills and chips */
    .pill{ border:1px solid rgba(255,255,255,0.12); background: rgba(255,255,255,0.04); }
    /* Sparkle accent */
    .sparkle{
      position:absolute; width:10px;height:10px; border-radius:9999px; background: radial-gradient(circle at 30% 30%, #fff, rgba(255,255,255,0.5) 40%, transparent 70%);
      box-shadow: 0 0 20px rgba(124,92,255,0.6);
      animation: float 6s ease-in-out infinite;
      opacity:.6;
    }
    .sparkle.s2{ animation-delay: -2s; left: 60%; top: 20%;}
    .sparkle.s1{ left: 15%; top: 35%;}
    .sparkle.s3{ left: 85%; top: 65%; animation-delay: -4s;}
    @keyframes float {
      0%,100% { transform: translateY(0) }
      50% { transform: translateY(-10px) }
    }
    /* Tiny bars for categorical distribution */
    .bar { height:10px; border-radius:9999px; }
    /* Simple tooltip */
    .tt{ position:relative }
    .tt:hover .ttc{ opacity:1; transform: translateY(0); pointer-events:auto }
    .ttc{ transition: all .18s ease; opacity:0; transform: translateY(4px); pointer-events:none; position:absolute; bottom:125%; left:50%; translate: -50% 0; white-space:nowrap; background:#0b1120; border:1px solid rgba(255,255,255,.08); border-radius:.5rem; padding:.35rem .5rem; font-size:.75rem; color:#cfe1ff; z-index:30 }
    /* Progress ring */
    .ring-bg{ stroke: rgba(255,255,255,0.08) }
    .ring-fg{ stroke: url(#grad); filter: drop-shadow(0 0 10px rgba(124,92,255,.35)) }
    /* Tables */
    .table-sticky thead th{ position: sticky; top:0; background: rgba(15,22,41,.85); backdrop-filter: blur(4px); }
    /* Buttons */
    .btn{ transition: all .15s ease; }
    .btn:active{ transform: translateY(1px) scale(0.99); }
    /* Alert pulse */
    .pulse{ position:relative }
    .pulse:before{
      content:""; position:absolute; inset:-2px; border-radius:12px;
      background: radial-gradient(120px 40px at 20% 0%, rgba(239,68,68,.25), transparent 60%);
      opacity:.45; filter: blur(10px); z-index:-1;
      animation: glow 2.4s ease-in-out infinite;
    }
    @keyframes glow {
      0%,100% { opacity:.35 }
      50% { opacity:.75 }
    }
  </style>
</head>
<body class="min-h-screen grid-bg relative">
  <!-- Decorative sparkles -->
  <span class="sparkle s1"></span>
  <span class="sparkle s2"></span>
  <span class="sparkle s3"></span>

  <!-- Topbar -->
  <header class="px-6 md:px-10 pt-6">
    <div class="max-w-7xl mx-auto flex items-center justify-between gap-4">
      <div class="flex items-center gap-3">
        <div class="w-9 h-9 rounded-xl grid place-items-center bg-gradient-to-br from-[var(--accent)] to-[var(--accent-2)] shadow-lg shadow-[rgba(124,92,255,0.25)]">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" aria-hidden="true">
            <path d="M3 11h18M6 7h12M4 15h10" stroke="white" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
        </div>
        <div>
          <div class="text-xl md:text-2xl font-extrabold tracking-tight">FinSight</div>
          <div class="text-xs text-[var(--muted)]">AI-inspired personal finance</div>
        </div>
      </div>
      <div class="flex items-center gap-2">
        <button id="importBtn" class="btn px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10 hover:bg-white/5 text-sm">
          Import CSV
        </button>
        <label for="monthPicker" class="sr-only">Select month</label>
        <input id="monthPicker" type="month" class="px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10 text-sm focus:outline-none focus:ring-2 focus:ring-[var(--accent)]/50" />
        <button id="addExpenseBtn" class="btn px-3 py-2 rounded-lg bg-gradient-to-r from-[var(--accent)] to-[var(--accent-2)] hover:opacity-95 text-sm font-semibold shadow-md shadow-[rgba(41,211,194,0.25)]">
          + Add Expense
        </button>
      </div>
    </div>
  </header>

  <!-- Hero KPIs -->
  <section class="px-6 md:px-10 mt-6">
    <div class="max-w-7xl mx-auto grid md:grid-cols-4 gap-4">
      <!-- Balance -->
      <div class="card rounded-2xl p-5">
        <div class="flex items-center justify-between">
          <span class="text-sm text-[var(--muted)]">Current Balance</span>
          <span class="pill text-[10px] px-2 py-1 rounded-full text-[var(--muted)]">Linked</span>
        </div>
        <div class="mt-2 text-2xl md:text-3xl font-extrabold tracking-tight" id="kpiBalance">$12,480</div>
        <div class="mt-1 text-xs text-[var(--muted)]" id="kpiBalanceDelta">+ $320 vs last month</div>
      </div>
      <!-- Monthly Spend -->
      <div class="card rounded-2xl p-5">
        <div class="flex items-center justify-between">
          <span class="text-sm text-[var(--muted)]">This Month Spend</span>
          <span class="text-[10px] px-2 py-1 rounded-full bg-white/5 border border-white/10">AI Forecast</span>
        </div>
        <div class="mt-2 text-2xl md:text-3xl font-extrabold tracking-tight" id="kpiSpend">$2,140</div>
        <div class="mt-1 flex items-center gap-2">
          <span class="text-xs text-[var(--muted)]">Projected: </span>
          <span class="text-xs font-semibold text-[var(--accent-2)]" id="kpiSpendForecast">$2,630</span>
        </div>
      </div>
      <!-- Savings rate -->
      <div class="card rounded-2xl p-5">
        <div class="flex items-center justify-between">
          <span class="text-sm text-[var(--muted)]">Savings Rate</span>
          <div class="tt">
            <span class="text-[10px] px-2 py-1 rounded-full bg-white/5 border border-white/10">Tip</span>
            <div class="ttc">Based on income - expenses</div>
          </div>
        </div>
        <div class="mt-2 text-2xl md:text-3xl font-extrabold tracking-tight" id="kpiSavings">18%</div>
        <div class="mt-1 text-xs text-[var(--muted)]">Goal: 20%</div>
      </div>
      <!-- Budget alert -->
      <div id="budgetAlertCard" class="card rounded-2xl p-5 pulse">
        <div class="flex items-center justify-between">
          <span class="text-sm text-[var(--muted)]">Budget Alerts</span>
          <span class="w-2 h-2 rounded-full bg-[var(--danger)] shadow-[0_0_0_6px_rgba(239,68,68,.15)]"></span>
        </div>
        <div class="mt-2 text-base md:text-lg font-semibold" id="budgetAlertText">Dining is 82% of its budget</div>
        <div class="mt-3 w-full bg-white/10 rounded-full overflow-hidden">
          <div id="budgetAlertBar" class="h-2 bg-gradient-to-r from-[var(--warning)] via-[var(--danger)] to-[var(--danger)]" style="width:82%"></div>
        </div>
        <div class="mt-2 text-xs text-[var(--muted)]">We’ll notify you if it crosses 90%</div>
      </div>
    </div>
  </section>

  <!-- Main content -->
  <main class="px-6 md:px-10 mt-6 pb-24">
    <div class="max-w-7xl mx-auto grid lg:grid-cols-3 gap-6">
      <!-- Left: Predictive Analytics -->
      <section class="lg:col-span-2 space-y-6">
        <!-- Spend trend -->
        <div class="card rounded-2xl p-5">
          <div class="flex flex-wrap items-center justify-between gap-3">
            <div>
              <h2 class="text-lg md:text-xl font-bold tracking-tight">Predictive Spending Analysis</h2>
              <p class="text-xs text-[var(--muted)]">Next 6 weeks forecast with confidence band</p>
            </div>
            <div class="flex items-center gap-2">
              <select id="viewToggle" class="px-3 py-2 rounded-lg bg-[var(--card-2)] border border-white/10 text-sm">
                <option value="weekly">Weekly view</option>
                <option value="monthly">Monthly view</option>
              </select>
              <button id="recalcBtn" class="btn px-3 py-2 rounded-lg bg-white/10 hover:bg-white/15 border border-white/10 text-sm">Recalculate</button>
            </div>
          </div>

          <!-- Trend canvas -->
          <div class="mt-5">
            <canvas id="trendChart" height="180" class="w-full rounded-lg bg-[var(--card-2)] border border-white/5"></canvas>
          </div>

          <!-- Legend -->
          <div class="mt-4 flex flex-wrap items-center gap-3 text-xs">
            <span class="flex items-center gap-2"><span class="w-3 h-3 rounded-full" style="background:linear-gradient(90deg,#7c5cff,#29d3c2)"></span> Actual</span>
            <span class="flex items-center gap-2"><span class="w-3 h-3 rounded-full bg-white/40"></span> Projection</span>
            <span class="flex items-center gap-2"><span class="w-5 h-3 rounded-sm bg-white/20"></span> Confidence</span>
          </div>
        </div>

        <!-- Category breakdown -->
        <div class="card rounded-2xl p-5">
          <div class="flex items-center justify-between">
            <h2 class="text-lg md:text-xl font-bold tracking-tight">Automated Expense Categorization</h2>
            <div class="flex items-center gap-2">
              <select id="categoryFilter" class="px-3 py-2 rounded-lg bg-[var(--card-2)] border border-white/10 text-sm">
                <option value="all">All</option>
              </select>
              <button id="autoCategorizeBtn" class="btn px-3 py-2 rounded-lg bg-gradient-to-r from-[var(--accent)] to-[var(--accent-2)] hover:opacity-95 text-sm font-semibold">Auto-Categorize</button>
            </div>
          </div>

          <div class="mt-5 grid sm:grid-cols-2 lg:grid-cols-3 gap-4" id="categoryCards">
            <!-- Filled dynamically -->
          </div>
        </div>

        <!-- Transactions -->
        <div class="card rounded-2xl p-5">
          <div class="flex items-center justify-between">
            <h2 class="text-lg md:text-xl font-bold tracking-tight">Transactions</h2>
            <div class="flex items-center gap-2">
              <input id="searchTx" type="text" placeholder="Search merchant or note..." class="px-3 py-2 rounded-lg bg-[var(--card-2)] border border-white/10 text-sm w-52">
              <select id="txSort" class="px-3 py-2 rounded-lg bg-[var(--card-2)] border border-white/10 text-sm">
                <option value="dateDesc">Newest first</option>
                <option value="dateAsc">Oldest first</option>
                <option value="amountDesc">Amount high → low</option>
                <option value="amountAsc">Amount low → high</option>
              </select>
            </div>
          </div>

          <div class="mt-4 overflow-auto rounded-xl border border-white/10">
            <table class="min-w-full table-sticky">
              <thead>
                <tr class="text-left text-[11px] uppercase tracking-widest text-[var(--muted)]">
                  <th class="px-4 py-3">Date</th>
                  <th class="px-4 py-3">Merchant</th>
                  <th class="px-4 py-3">Category</th>
                  <th class="px-4 py-3">Amount</th>
                  <th class="px-4 py-3">Note</th>
                  <th class="px-4 py-3"></th>
                </tr>
              </thead>
              <tbody id="txBody" class="text-sm">
                <!-- Rows injected -->
              </tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- Right: Goals & Budgets -->
      <aside class="space-y-6">
        <!-- Goals -->
        <div class="card rounded-2xl p-5">
          <div class="flex items-center justify-between">
            <h2 class="text-lg md:text-xl font-bold tracking-tight">Goal Tracking</h2>
            <button id="addGoalBtn" class="btn px-3 py-2 rounded-lg bg-white/10 hover:bg-white/15 border border-white/10 text-sm">+ Add Goal</button>
          </div>

          <svg width="0" height="0">
            <defs>
              <linearGradient id="grad" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stop-color="#7c5cff"/>
                <stop offset="100%" stop-color="#29d3c2"/>
              </linearGradient>
            </defs>
          </svg>

          <div id="goalsList" class="mt-4 space-y-4">
            <!-- Goals injected -->
          </div>
        </div>

        <!-- Budgets -->
        <div class="card rounded-2xl p-5">
          <div class="flex items-center justify-between">
            <h2 class="text-lg md:text-xl font-bold tracking-tight">Budgets</h2>
            <button id="addBudgetBtn" class="btn px-3 py-2 rounded-lg bg-white/10 hover:bg-white/15 border border-white/10 text-sm">+ Add Budget</button>
          </div>

          <div id="budgetsList" class="mt-4 space-y-3">
            <!-- Budgets injected -->
          </div>
        </div>

        <!-- Quick insights -->
        <div class="card rounded-2xl p-5">
          <h2 class="text-lg md:text-xl font-bold tracking-tight">Insights</h2>
          <ul id="insightsList" class="mt-3 space-y-2 text-sm text-[var(--muted)]">
            <!-- Insights injected -->
          </ul>
        </div>
      </aside>
    </div>
  </main>

  <!-- Modals -->
  <div id="modalBackdrop" class="fixed inset-0 bg-black/60 hidden z-40"></div>

  <!-- Add Expense Modal -->
  <div id="expenseModal" class="fixed z-50 inset-0 items-center justify-center hidden p-4">
    <div class="card-strong rounded-2xl w-full max-w-md p-5">
      <div class="flex items-center justify-between">
        <h3 class="text-lg font-bold">Add Expense</h3>
        <button class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10" data-close-expense>✕</button>
      </div>
      <form id="expenseForm" class="mt-4 space-y-3">
        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="text-xs text-[var(--muted)]">Date</label>
            <input name="date" type="date" required class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
          </div>
          <div>
            <label class="text-xs text-[var(--muted)]">Amount</label>
            <input name="amount" type="number" step="0.01" min="0" required class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
          </div>
        </div>
        <div>
          <label class="text-xs text-[var(--muted)]">Merchant</label>
          <input name="merchant" type="text" required placeholder="e.g., Starbucks" class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
        </div>
        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="text-xs text-[var(--muted)]">Category</label>
            <select name="category" class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10"></select>
          </div>
          <div>
            <label class="text-xs text-[var(--muted)]">Note</label>
            <input name="note" type="text" placeholder="Optional" class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
          </div>
        </div>
        <div class="flex items-center justify-end gap-2 pt-2">
          <button type="button" data-close-expense class="btn px-3 py-2 rounded-lg bg-white/10 hover:bg-white/15 border border-white/10">Cancel</button>
          <button type="submit" class="btn px-3 py-2 rounded-lg bg-gradient-to-r from-[var(--accent)] to-[var(--accent-2)] font-semibold">Add</button>
        </div>
      </form>
    </div>
  </div>

  <!-- Add Goal Modal -->
  <div id="goalModal" class="fixed z-50 inset-0 items-center justify-center hidden p-4">
    <div class="card-strong rounded-2xl w-full max-w-md p-5">
      <div class="flex items-center justify-between">
        <h3 class="text-lg font-bold">Add Goal</h3>
        <button class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10" data-close-goal>✕</button>
      </div>
      <form id="goalForm" class="mt-4 space-y-3">
        <div>
          <label class="text-xs text-[var(--muted)]">Goal Name</label>
          <input name="name" type="text" required placeholder="e.g., Emergency Fund" class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
        </div>
        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="text-xs text-[var(--muted)]">Target Amount</label>
            <input name="target" type="number" step="0.01" min="0" required class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
          </div>
          <div>
            <label class="text-xs text-[var(--muted)]">Monthly Save</label>
            <input name="monthly" type="number" step="0.01" min="0" required class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
          </div>
        </div>
        <div class="flex items-center justify-end gap-2 pt-2">
          <button type="button" data-close-goal class="btn px-3 py-2 rounded-lg bg-white/10 hover:bg-white/15 border border-white/10">Cancel</button>
          <button type="submit" class="btn px-3 py-2 rounded-lg bg-gradient-to-r from-[var(--accent)] to-[var(--accent-2)] font-semibold">Save</button>
        </div>
      </form>
    </div>
  </div>

  <!-- Add Budget Modal -->
  <div id="budgetModal" class="fixed z-50 inset-0 items-center justify-center hidden p-4">
    <div class="card-strong rounded-2xl w-full max-w-md p-5">
      <div class="flex items-center justify-between">
        <h3 class="text-lg font-bold">Add Budget</h3>
        <button class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10" data-close-budget>✕</button>
      </div>
      <form id="budgetForm" class="mt-4 space-y-3">
        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="text-xs text-[var(--muted)]">Category</label>
            <select name="category" class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10"></select>
          </div>
          <div>
            <label class="text-xs text-[var(--muted)]">Monthly Limit</label>
            <input name="limit" type="number" step="0.01" min="0" required class="w-full px-3 py-2 rounded-lg bg-[var(--card)] border border-white/10">
          </div>
        </div>
        <div class="flex items-center justify-end gap-2 pt-2">
          <button type="button" data-close-budget class="btn px-3 py-2 rounded-lg bg-white/10 hover:bg-white/15 border border-white/10">Cancel</button>
          <button type="submit" class="btn px-3 py-2 rounded-lg bg-gradient-to-r from-[var(--accent)] to-[var(--accent-2)] font-semibold">Save</button>
        </div>
      </form>
    </div>
  </div>

  <script>
    // --- Demo data and state ---
    const defaultCategories = [
      { id:'groceries', name:'Groceries', color:'#7c5cff' },
      { id:'dining', name:'Dining', color:'#29d3c2' },
      { id:'transport', name:'Transport', color:'#f59e0b' },
      { id:'utilities', name:'Utilities', color:'#ef4444' },
      { id:'entertainment', name:'Entertainment', color:'#22c55e' },
      { id:'other', name:'Other', color:'#8ea0c7' },
    ];

    const state = {
      incomeMonthly: 2600,
      transactions: [
        { id: crypto.randomUUID(), date:'2025-09-02', merchant:'WholeFoods', category:'groceries', amount:84.12, note:'Weekly shop' },
        { id: crypto.randomUUID(), date:'2025-09-03', merchant:'Uber', category:'transport', amount:18.40, note:'' },
        { id: crypto.randomUUID(), date:'2025-09-03', merchant:'Spotify', category:'entertainment', amount:9.99, note:'Family plan' },
        { id: crypto.randomUUID(), date:'2025-09-04', merchant:'Starbucks', category:'dining', amount:6.85, note:'Latte' },
        { id: crypto.randomUUID(), date:'2025-09-05', merchant:'Electric Co', category:'utilities', amount:92.30, note:'Bill' },
        { id: crypto.randomUUID(), date:'2025-09-06', merchant:'Chipotle', category:'dining', amount:12.15, note:'Lunch' },
        { id: crypto.randomUUID(), date:'2025-09-07', merchant:'Target', category:'groceries', amount:42.70, note:'Household' },
        { id: crypto.randomUUID(), date:'2025-09-08', merchant:'Cinema', category:'entertainment', amount:15.00, note:'Movie night' },
      ],
      budgets: [
        { id: crypto.randomUUID(), category:'dining', limit:150 },
        { id: crypto.randomUUID(), category:'groceries', limit:300 },
        { id: crypto.randomUUID(), category:'transport', limit:120 },
      ],
      goals: [
        { id: crypto.randomUUID(), name:'Emergency Fund', target:3000, monthly:250, saved:1200 },
      ],
      view: 'weekly',
      month: new Date().toISOString().slice(0,7)
    };

    // Category map
    const catMap = Object.fromEntries(defaultCategories.map(c=>[c.id,c]));
    // Simple "AI-inspired" categorization rules with fuzzy matching
    const aiRules = [
      { key:/uber|lyft|bus|metro|shell|gas/i, category:'transport' },
      { key:/grocery|whole|aldi|trader|target|walmart/i, category:'groceries' },
      { key:/coffee|cafe|starbucks|peet|dunkin|restaurant|chipotle|mcd|kfc|pizza/i, category:'dining' },
      { key:/netflix|hulu|spotify|cinema|game|steam/i, category:'entertainment' },
      { key:/water|electric|power|utility|gas bill|internet|comcast|verizon|att/i, category:'utilities' },
    ];

    // --- Helpers ---
    const $ = sel => document.querySelector(sel);
    const $$ = sel => document.querySelectorAll(sel);
    const fmt = (n) => n.toLocaleString(undefined,{style:'currency', currency:'USD'});
    const pct = (n) => (n*100).toFixed(0)+'%';

    function groupByCategory(transactions, month) {
      const g = {};
      for (const t of transactions) {
        if (!t.date.startsWith(month)) continue;
        g[t.category] = (g[t.category]||0)+Number(t.amount||0);
      }
      return g;
    }

    function sumMonth(transactions, month){
      return transactions.filter(t=>t.date.startsWith(month)).reduce((a,b)=>a+Number(b.amount),0);
    }

    function forecastSpend(history) {
      // Simple linear trend + variance band
      // history = [{x: idx, y: amount}]
      const n = history.length;
      if (n === 0) return {projection:[], band:[]};
      let sumX=0,sumY=0,sumXY=0,sumXX=0;
      history.forEach(p=>{ sumX+=p.x; sumY+=p.y; sumXY+=p.x*p.y; sumXX+=p.x*p.x; });
      const slope = (n*sumXY - sumX*sumY) / Math.max(1,(n*sumXX - sumX*sumX));
      const intercept = (sumY - slope*sumX)/n;
      const residuals = history.map(p=> p.y - (slope*p.x+intercept));
      const stdev = Math.sqrt(residuals.reduce((a,b)=>a+b*b,0)/Math.max(1,n-1));
      const out = [];
      const band = [];
      const future = 6;
      const lastX = history[n-1].x;
      for(let i=1;i<=future;i++){
        const x = lastX+i;
        const y = Math.max(0, slope*x + intercept);
        out.push({x,y});
        band.push({x, lo: Math.max(0,y-1.2*stdev), hi: y+1.2*stdev });
      }
      return {projection: out, band};
    }

    function buildWeeklyHistory(transactions, month){
      // Build 8-weeks history around selected month using mock spread from current data
      const baseTotal = sumMonth(transactions, month) || 1200;
      const rand = (min,max)=> Math.random()*(max-min)+min;
      const weeks = 8;
      const hist = [];
      for(let i=0;i<weeks;i++){
        const jitter = baseTotal/4 * (0.8 + rand(-0.15, 0.15));
        hist.push({x:i, y: Math.max(200, jitter)});
      }
      return hist;
    }

    // --- Rendering: Category Cards ---
    function renderCategoryCards(){
      const container = $("#categoryCards");
      container.innerHTML = "";
      const totals = groupByCategory(state.transactions, state.month);
      const grand = Object.values(totals).reduce((a,b)=>a+b,0) || 0;

      // Ensure filter options
      const filter = $("#categoryFilter");
      filter.innerHTML = '<option value="all">All</option>' + defaultCategories.map(c=>`<option value="${c.id}">${c.name}</option>`).join('');

      const entries = defaultCategories.map(c=>{
        const spent = totals[c.id] || 0;
        const share = grand ? spent/grand : 0;
        return { ...c, spent, share };
      }).sort((a,b)=>b.spent-a.spent);

      entries.forEach(c=>{
        const card = document.createElement('div');
        card.className = "rounded-xl p-4 border border-white/10 bg-[var(--card-2)]";
        card.innerHTML = `
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <span class="w-3 h-3 rounded-full" style="background:${c.color}"></span>
              <h3 class="font-semibold">${c.name}</h3>
            </div>
            <div class="text-sm font-semibold">${fmt(c.spent)}</div>
          </div>
          <div class="mt-3 w-full bg-white/10 rounded-full overflow-hidden">
            <div class="bar" style="width:${Math.min(100,c.share*100)}%; background: linear-gradient(90deg, ${c.color}, rgba(255,255,255,.35))"></div>
          </div>
          <div class="mt-2 text-xs text-[var(--muted)]">${pct(c.share||0)} of total this month</div>
        `;
        container.appendChild(card);
      });
    }

    // --- Rendering: Transactions Table ---
    function renderTransactions(){
      const tbody = $("#txBody");
      const q = ($("#searchTx").value||"").toLowerCase();
      const filter = $("#categoryFilter").value;
      const sort = $("#txSort").value;

      let list = state.transactions.filter(t=> t.date.startsWith(state.month));
      if (filter !== 'all') list = list.filter(t=> t.category === filter);
      if (q) list = list.filter(t=> (t.merchant+" "+(t.note||"")).toLowerCase().includes(q));

      list.sort((a,b)=>{
        if (sort==='dateDesc') return b.date.localeCompare(a.date);
        if (sort==='dateAsc') return a.date.localeCompare(b.date);
        if (sort==='amountDesc') return b.amount - a.amount;
        if (sort==='amountAsc') return a.amount - b.amount;
        return 0;
      });

      tbody.innerHTML = "";
      list.forEach(t=>{
        const tr = document.createElement('tr');
        tr.className = "border-t border-white/5 hover:bg-white/5";
        tr.innerHTML = `
          <td class="px-4 py-3 whitespace-nowrap">${t.date}</td>
          <td class="px-4 py-3">${t.merchant}</td>
          <td class="px-4 py-3">
            <select data-tid="${t.id}" class="px-2 py-1 rounded-md bg-[var(--card)] border border-white/10 text-xs">
              ${defaultCategories.map(c=>`<option value="${c.id}" ${t.category===c.id?'selected':''}>${c.name}</option>`).join('')}
            </select>
          </td>
          <td class="px-4 py-3 font-semibold">${fmt(t.amount)}</td>
          <td class="px-4 py-3">${t.note||''}</td>
          <td class="px-4 py-3 text-right">
            <button data-del="${t.id}" class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10 text-xs">Delete</button>
          </td>
        `;
        tbody.appendChild(tr);
      });

      // Bind change/delete
      tbody.querySelectorAll('select[data-tid]').forEach(sel=>{
        sel.addEventListener('change', e=>{
          const id = e.target.getAttribute('data-tid');
          const t = state.transactions.find(x=>x.id===id);
          if (t){ t.category = e.target.value; refresh(); }
        });
      });
      tbody.querySelectorAll('button[data-del]').forEach(btn=>{
        btn.addEventListener('click', e=>{
          const id = e.currentTarget.getAttribute('data-del');
          state.transactions = state.transactions.filter(x=>x.id!==id);
          refresh();
        });
      });
    }

    // --- Rendering: Budgets ---
    function renderBudgets(){
      const wrap = $("#budgetsList");
      wrap.innerHTML = "";
      const totals = groupByCategory(state.transactions, state.month);
      state.budgets.forEach(b=>{
        const spent = totals[b.category]||0;
        const perc = b.limit ? Math.min(100, Math.round(spent/b.limit*100)) : 0;
        const cat = catMap[b.category]||{name:b.category,color:'#8ea0c7'};
        const row = document.createElement('div');
        row.className = "rounded-xl p-4 border border-white/10 bg-[var(--card-2)]";
        row.innerHTML = `
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <span class="w-3 h-3 rounded-full" style="background:${cat.color}"></span>
              <span class="font-semibold">${cat.name}</span>
            </div>
            <div class="text-sm">${fmt(spent)} / <span class="font-semibold">${fmt(b.limit)}</span></div>
          </div>
          <div class="mt-3 w-full bg-white/10 rounded-full overflow-hidden">
            <div class="h-2" style="width:${perc}%; background: linear-gradient(90deg, ${cat.color}, ${perc>90?'#ef4444':'#7c5cff'})"></div>
          </div>
          <div class="mt-2 flex justify-between text-xs text-[var(--muted)]">
            <span>${perc}% used</span>
            <div class="flex items-center gap-2">
              <button data-edit-budget="${b.id}" class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10">Edit</button>
              <button data-del-budget="${b.id}" class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10">Remove</button>
            </div>
          </div>
        `;
        wrap.appendChild(row);
      });

      // Alerts (top card)
      let worst = null;
      state.budgets.forEach(b=>{
        const spent = (groupByCategory(state.transactions, state.month)[b.category]||0);
        const ratio = b.limit ? spent/b.limit : 0;
        if (!worst || ratio>worst.ratio) worst = {b, ratio, spent};
      });
      if (worst){
        $("#budgetAlertText").textContent = `${(catMap[worst.b.category]||{name:'Budget'}).name} is ${Math.round(worst.ratio*100)}% of its budget`;
        $("#budgetAlertBar").style.width = Math.min(100, Math.round(worst.ratio*100))+'%';
        $("#budgetAlertCard").classList.toggle('pulse', worst.ratio>=0.9);
      }

      // Bind buttons
      wrap.querySelectorAll('[data-del-budget]').forEach(btn=>{
        btn.addEventListener('click', ()=>{
          const id = btn.getAttribute('data-del-budget');
          state.budgets = state.budgets.filter(x=>x.id!==id);
          refresh();
        });
      });
      wrap.querySelectorAll('[data-edit-budget]').forEach(btn=>{
        btn.addEventListener('click', ()=>{
          const id = btn.getAttribute('data-edit-budget');
          const b = state.budgets.find(x=>x.id===id);
          if (!b) return;
          // Open budget modal prefilled
          openModal('budget');
          const form = $("#budgetForm");
          form.category.value = b.category;
          form.limit.value = b.limit;
          form.onsubmit = (e)=>{
            e.preventDefault();
            b.category = form.category.value;
            b.limit = Number(form.limit.value||0);
            closeModal('budget');
            refresh();
          };
        });
      });
    }

    // --- Rendering: Goals ---
    function ringPath(percent){
      const r = 38, c = 2*Math.PI*r;
      const dash = Math.max(0, Math.min(1, percent)) * c;
      return { c, dash };
    }
    function monthsToGoal(target, saved, monthly){
      if (monthly<=0) return Infinity;
      const remaining = Math.max(0, target - saved);
      return Math.ceil(remaining / monthly);
    }
    function renderGoals(){
      const wrap = $("#goalsList");
      wrap.innerHTML = "";
      state.goals.forEach(g=>{
        const progress = g.target ? g.saved/g.target : 0;
        const { c, dash } = ringPath(progress);
        const months = monthsToGoal(g.target, g.saved, g.monthly);
        const item = document.createElement('div');
        item.className = "rounded-xl p-4 border border-white/10 bg-[var(--card-2)] flex items-center gap-4";
        item.innerHTML = `
          <svg width="90" height="90" viewBox="0 0 90 90">
            <circle cx="45" cy="45" r="38" class="ring-bg" stroke-width="10" fill="none"></circle>
            <circle cx="45" cy="45" r="38" class="ring-fg" stroke-width="10" fill="none"
              stroke-dasharray="${c}" stroke-dashoffset="${c-dash}" stroke-linecap="round"></circle>
          </svg>
          <div class="flex-1">
            <div class="flex items-center justify-between">
              <div class="font-semibold">${g.name}</div>
              <div class="text-sm">${fmt(g.saved)} / <span class="font-semibold">${fmt(g.target)}</span></div>
            </div>
            <div class="text-xs text-[var(--muted)] mt-1">${isFinite(months)? months+' months to goal':'Add monthly amount to forecast timing'}</div>
            <div class="mt-3 flex items-center gap-2">
              <button data-add="${g.id}" class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10 text-xs">+ Add $</button>
              <button data-edit="${g.id}" class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10 text-xs">Edit</button>
              <button data-del="${g.id}" class="btn px-2 py-1 rounded-md bg-white/10 hover:bg-white/15 border border-white/10 text-xs">Remove</button>
            </div>
          </div>
        `;
        wrap.appendChild(item);
      });

      // Bind actions
      wrap.querySelectorAll('button[data-add]').forEach(btn=>{
        btn.addEventListener('click', ()=>{
          const id = btn.getAttribute('data-add');
          const g = state.goals.find(x=>x.id===id);
          const amount = prompt('Add amount to savings:', '50');
          if (amount!=null){
            g.saved = Math.max(0, Number(g.saved)+Number(amount||0));
            refresh();
          }
        });
      });
      wrap.querySelectorAll('button[data-edit]').forEach(btn=>{
        btn.addEventListener('click', ()=>{
          const id = btn.getAttribute('data-edit');
          const g = state.goals.find(x=>x.id===id);
          openModal('goal');
          const form = $("#goalForm");
          form.name.value = g.name;
          form.target.value = g.target;
          form.monthly.value = g.monthly;
          form.onsubmit = (e)=>{
            e.preventDefault();
            g.name = form.name.value.trim();
            g.target = Number(form.target.value||0);
            g.monthly = Number(form.monthly.value||0);
            closeModal('goal');
            refresh();
          };
        });
      });
      wrap.querySelectorAll('button[data-del]').forEach(btn=>{
        btn.addEventListener('click', ()=>{
          const id = btn.getAttribute('data-del');
          state.goals = state.goals.filter(x=>x.id!==id);
          refresh();
        });
      });
    }

    // --- Rendering: Insights ---
    function renderInsights(){
      const ul = $("#insightsList");
      ul.innerHTML = "";
      const totals = groupByCategory(state.transactions, state.month);
      const entries = Object.entries(totals).sort((a,b)=>b[1]-a[1]);
      const top = entries[0];
      const spend = sumMonth(state.transactions, state.month);
      const rate = state.incomeMonthly>0 ? Math.max(0, state.incomeMonthly - spend)/state.incomeMonthly : 0;
      const li = (t)=>{ const el = document.createElement('li'); el.textContent = t; ul.appendChild(el); };

      if (top){
        const name = (catMap[top[0]]||{name:'Category'}).name;
        li(`You're spending the most on ${name} this month.`);
      }
      li(`Projected month-end spend is ${fmt(projectedMonthEnd())}.`);
      li(`Savings rate is around ${pct(rate)} this month.`);
      const risk = budgetRisk();
      if (risk) li(`${risk.name} might exceed its budget. Consider reducing by ${fmt(risk.over)}.`);
    }

    function projectedMonthEnd(){
      // naive projection: current average daily spend * days in month
      const month = state.month;
      const tx = state.transactions.filter(t=>t.date.startsWith(month));
      if (tx.length===0) return 0;
      const sum = tx.reduce((a,b)=>a+b.amount,0);
      const daysSpent = new Set(tx.map(t=>t.date)).size;
      const dailyAvg = sum / Math.max(1, daysSpent);
      const totalDays = new Date(month.slice(0,4), Number(month.slice(5))+1, 0).getDate();
      return dailyAvg * totalDays;
    }

    function budgetRisk(){
      let worst = null;
      const totals = groupByCategory(state.transactions, state.month);
      state.budgets.forEach(b=>{
        const spent = totals[b.category]||0;
        const name = (catMap[b.category]||{name:'Budget'}).name;
        const over = spent - b.limit;
        const ratio = b.limit? spent/b.limit: 0;
        if (!worst || ratio>worst.ratio) worst = { name, over: Math.max(0, over), ratio };
      });
      return worst;
    }

    // --- Trend Chart (canvas, no external libs) ---
    let trendCtx, trendState;
    function drawTrend(){
      const canvas = $("#trendChart");
      const dpr = Math.min(2, window.devicePixelRatio||1);
      const cssW = canvas.clientWidth, cssH = canvas.height;
      canvas.width = cssW * dpr; canvas.height = cssH * dpr;
      const ctx = canvas.getContext('2d');
      ctx.scale(dpr,dpr);

      // Build data
      const hist = buildWeeklyHistory(state.transactions, state.month);
      const { projection, band } = forecastSpend(hist);
      trendState = { hist, projection, band };

      // Padding
      const pad = { l: 36, r: 12, t: 10, b: 22 };
      const W = cssW - pad.l - pad.r;
      const H = cssH - pad.t - pad.b;

      // Scales
      const allY = [...hist.map(d=>d.y), ...projection.map(d=>d.y), ...band.flatMap(b=>[b.lo,b.hi])];
      const yMax = Math.max(300, Math.ceil(Math.max(...allY)*1.25));
      const xMax = hist[hist.length-1].x + projection.length;

      const x = v => pad.l + (v/xMax) * W;
      const y = v => pad.t + H - (v/yMax) * H;

      // Background grid
      const stepsY = 4;
      ctx.strokeStyle = 'rgba(255,255,255,0.08)';
      ctx.lineWidth = 1;
      for(let i=0;i<=stepsY;i++){
        const yy = pad.t + (H/stepsY)*i;
        ctx.beginPath(); ctx.moveTo(pad.l, yy); ctx.lineTo(pad.l+W, yy); ctx.stroke();
        const val = Math.round(yMax*(1-i/stepsY));
        ctx.fillStyle = 'rgba(200,220,255,0.6)';
        ctx.font = '11px Inter';
        ctx.fillText('$'+val, 6, yy+4);
      }

      // Confidence band
      if (band.length){
        const grad = ctx.createLinearGradient(0, y(yMax), 0, y(0));
        grad.addColorStop(0, 'rgba(255,255,255,0.02)');
        grad.addColorStop(1, 'rgba(255,255,255,0.10)');
        ctx.fillStyle = grad;
        ctx.beginPath();
        ctx.moveTo(x(hist[hist.length-1].x), y(band[0].lo));
        band.forEach((p,i)=> ctx.lineTo(x(p.x), y(p.hi)));
        for(let i=band.length-1;i>=0;i--) ctx.lineTo(x(band[i].x), y(band[i].lo));
        ctx.closePath(); ctx.fill();
      }

      // Actual line
      const gradLine = ctx.createLinearGradient(0, 0, cssW, 0);
      gradLine.addColorStop(0, '#7c5cff');
      gradLine.addColorStop(1, '#29d3c2');
      ctx.lineWidth = 2;
      ctx.strokeStyle = gradLine;
      ctx.beginPath();
      hist.forEach((p,i)=>{ if(i===0) ctx.moveTo(x(p.x), y(p.y)); else ctx.lineTo(x(p.x), y(p.y)); });
      ctx.stroke();

      // Projection line (dashed)
      if (projection.length){
        ctx.setLineDash([5,4]); ctx.strokeStyle = 'rgba(255,255,255,0.6)';
        ctx.beginPath();
        const start = hist[hist.length-1];
        ctx.moveTo(x(start.x), y(start.y));
        projection.forEach((p)=> ctx.lineTo(x(p.x), y(p.y)));
        ctx.stroke();
        ctx.setLineDash([]);
      }

      // Dots
      ctx.fillStyle = 'rgba(255,255,255,0.9)';
      hist.forEach(p=>{
        ctx.beginPath(); ctx.arc(x(p.x), y(p.y), 2.5, 0, Math.PI*2); ctx.fill();
      });

      // Hover handling
      if (!trendCtx){
        canvas.addEventListener('mousemove', (e)=>{
          const rect = canvas.getBoundingClientRect();
          const mx = (e.clientX - rect.left);
          const nearest = [...hist, ...projection].reduce((acc,p)=>{
            const dx = Math.abs(x(p.x) - mx);
            return dx < acc.dx ? {p, dx} : acc;
          }, {p:null, dx:1e9});
          drawTrend(); // redraw
          if (nearest.p){
            ctx.save();
            ctx.strokeStyle = 'rgba(255,255,255,0.2)';
            ctx.beginPath(); ctx.moveTo(x(nearest.p.x), pad.t); ctx.lineTo(x(nearest.p.x), pad.t+H); ctx.stroke();
            ctx.fillStyle = '#0b1120';
            ctx.strokeStyle = 'rgba(255,255,255,0.15)';
            const boxW=140, boxH=44, bx = Math.min(pad.l+W-boxW-4, Math.max(pad.l+4, x(nearest.p.x)-boxW/2)), by = pad.t+8;
            ctx.beginPath(); ctx.roundRect(bx, by, boxW, boxH, 8); ctx.fill(); ctx.stroke();
            ctx.fillStyle = 'rgba(200,220,255,0.85)';
            ctx.font = '11px Inter';
            const label = nearest.p.x<=hist[hist.length-1].x ? 'Actual' : 'Projection';
            ctx.fillText(`${label}: ${fmt(nearest.p.y)}`, bx+10, by+18);
            ctx.fillText(`Week ${nearest.p.x+1}`, bx+10, by+34);
            ctx.restore();
          }
        });
      }
      trendCtx = true;

      // Update KPI projection
      const monthEnd = projectedMonthEnd();
      $("#kpiSpend").textContent = fmt(sumMonth(state.transactions, state.month));
      $("#kpiSpendForecast").textContent = fmt(Math.round(monthEnd));
      const savingsRate = state.incomeMonthly>0 ? Math.max(0, state.incomeMonthly - monthEnd)/state.incomeMonthly : 0;
      $("#kpiSavings").textContent = pct(savingsRate);
    }

    // --- Modal helpers ---
    function openModal(which){
      $("#modalBackdrop").classList.remove('hidden');
      const map = { expense:'#expenseModal', goal:'#goalModal', budget:'#budgetModal' };
      const el = document.querySelector(map[which]);
      el.classList.remove('hidden');
      el.classList.add('flex');
    }
    function closeModal(which){
      $("#modalBackdrop").classList.add('hidden');
      const map = { expense:'#expenseModal', goal:'#goalModal', budget:'#budgetModal' };
      const el = document.querySelector(map[which]);
      el.classList.add('hidden');
      el.classList.remove('flex');
    }

    // --- Populate selects ---
    function populateCategorySelects(){
      // Expense form
      const sel = document.querySelector('#expenseForm select[name="category"]');
      sel.innerHTML = defaultCategories.map(c=>`<option value="${c.id}">${c.name}</option>`).join('');
      // Budget form
      const selB = document.querySelector('#budgetForm select[name="category"]');
      selB.innerHTML = defaultCategories.map(c=>`<option value="${c.id}">${c.name}</option>`).join('');
    }

    // --- Auto-categorization ---
    function autoCategorize(){
      state.transactions.forEach(t=>{
        const name = (t.merchant||'').toString();
        const match = aiRules.find(r=> r.key.test(name));
        if (match) t.category = match.category;
        else if (!t.category) t.category = 'other';
      });
    }

    // --- CSV Import (demo: parse basic CSV columns) ---
    function importCSV(){
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = '.csv,text/csv';
      input.onchange = async (e)=>{
        const file = e.target.files[0];
        if (!file) return;
        const text = await file.text();
        // Expect columns: date,merchant,amount,category?,note?
        const lines = text.split(/\r?\n/).filter(Boolean);
        const [header, ...rows] = lines;
        const cols = header.split(',').map(s=>s.trim().toLowerCase());
        const idx = {
          date: cols.indexOf('date'),
          merchant: cols.indexOf('merchant'),
          amount: cols.indexOf('amount'),
          category: cols.indexOf('category'),
          note: cols.indexOf('note'),
        };
        rows.forEach(line=>{
          const parts = line.split(',');
          const obj = {
            id: crypto.randomUUID(),
            date: (parts[idx.date]||'').slice(0,10),
            merchant: parts[idx.merchant]||'',
            amount: Number(parts[idx.amount]||0),
            category: idx.category>=0 ? (parts[idx.category]||'other').toLowerCase() : '',
            note: idx.note>=0 ? (parts[idx.note]||'') : ''
          };
          state.transactions.push(obj);
        });
        autoCategorize();
        refresh();
      };
      input.click();
    }

    // --- Refresh all UI ---
    function refresh(){
      renderCategoryCards();
      renderTransactions();
      renderBudgets();
      renderGoals();
      renderInsights();
      drawTrend();
    }

    // --- Event bindings ---
    window.addEventListener('resize', ()=> drawTrend());
    $("#importBtn").addEventListener('click', importCSV);
    $("#addExpenseBtn").addEventListener('click', ()=>{
      openModal('expense');
      const form = $("#expenseForm");
      form.reset();
      form.date.value = new Date().toISOString().slice(0,10);
      form.onsubmit = (e)=>{
        e.preventDefault();
        const data = Object.fromEntries(new FormData(form).entries());
        state.transactions.push({
          id: crypto.randomUUID(),
          date: data.date,
          merchant: data.merchant,
          amount: Number(data.amount||0),
          category: data.category||'other',
          note: data.note||''
        });
        closeModal('expense');
        refresh();
      };
    });
    $$('[data-close-expense]').forEach(b=> b.addEventListener('click', ()=> closeModal('expense')));

    $("#addGoalBtn").addEventListener('click', ()=>{
      openModal('goal');
      const form = $("#goalForm");
      form.reset();
      form.onsubmit = (e)=>{
        e.preventDefault();
        const data = Object.fromEntries(new FormData(form).entries());
        state.goals.push({
          id: crypto.randomUUID(),
          name: (data.name||'Goal').trim(),
          target: Number(data.target||0),
          monthly: Number(data.monthly||0),
          saved: 0
        });
        closeModal('goal');
        refresh();
      };
    });
    $$('[data-close-goal]').forEach(b=> b.addEventListener('click', ()=> closeModal('goal')));

    $("#addBudgetBtn").addEventListener('click', ()=>{
      openModal('budget');
      const form = $("#budgetForm");
      form.reset();
      form.onsubmit = (e)=>{
        e.preventDefault();
        const data = Object.fromEntries(new FormData(form).entries());
        state.budgets.push({
          id: crypto.randomUUID(),
          category: data.category,
          limit: Number(data.limit||0),
        });
        closeModal('budget');
        refresh();
      };
    });
    $$('[data-close-budget]').forEach(b=> b.addEventListener('click', ()=> closeModal('budget')));

    $("#autoCategorizeBtn").addEventListener('click', ()=>{ autoCategorize(); refresh(); });
    $("#categoryFilter").addEventListener('change', renderTransactions);
    $("#txSort").addEventListener('change', renderTransactions);
    $("#searchTx").addEventListener('input', ()=>{
      // Debounce simple
      clearTimeout(window.__searchDeb);
      window.__searchDeb = setTimeout(renderTransactions, 120);
    });
    $("#recalcBtn").addEventListener('click', drawTrend);
    $("#viewToggle").addEventListener('change', (e)=>{ state.view = e.target.value; drawTrend(); });
    $("#monthPicker").addEventListener('change', (e)=>{ state.month = e.target.value || state.month; refresh(); });

    // Initial mount
    (function init(){
      populateCategorySelects();
      $("#monthPicker").value = state.month;
      autoCategorize();
      refresh();
    })();
  </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'97e4e03ea2c79f44',t:'MTc1NzczNzQ3Ny4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
