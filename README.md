[index.html](https://github.com/user-attachments/files/29507943/index.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>InvestWise — Learn Before You Leap</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

<!-- ===== Firebase (Auth + Firestore) =====
     Replace firebaseConfig below with YOUR project's config from the
     Firebase Console (Project settings → General → Your apps → SDK setup).
     Step-by-step instructions are in the chat reply. -->
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
  import {
    getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword,
    onAuthStateChanged, signOut
  } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";
  import {
    getFirestore, doc, setDoc, getDoc, collection, addDoc, query,
    orderBy, limit, getDocs, serverTimestamp
  } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

  const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
  };

  const fbApp = initializeApp(firebaseConfig);
  const auth = getAuth(fbApp);
  const db = getFirestore(fbApp);

  window.Firebase = {
    auth, db,
    createUserWithEmailAndPassword, signInWithEmailAndPassword,
    onAuthStateChanged, signOut,
    doc, setDoc, getDoc, collection, addDoc, query, orderBy, limit, getDocs, serverTimestamp
  };
  window.dispatchEvent(new Event('firebase-ready'));
</script>
<style>
:root{
  --blue:#1b5fff;
  --red:#ff3b3b;
  --green:#1fcf6c;
  --orange:#ff9d2f;
  --ink:#0b0d1a;
  --paper:#11142b;
  --card:#171b3a;
  --line:rgba(255,255,255,0.08);
  --text:#f1f1f7;
  --muted:#9aa0c3;
  --font-display:'Space Grotesk', 'Trebuchet MS', sans-serif;
  --font-body:'Inter', 'Segoe UI', sans-serif;
}
@import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;700&family=Inter:wght@400;500;700&display=swap');
*{box-sizing:border-box;margin:0;padding:0;}
html,body{height:100%;}
body{
  font-family:var(--font-body);
  background:var(--ink);
  color:var(--text);
  overflow-x:hidden;
}
#bg-canvas{position:fixed;inset:0;z-index:0;opacity:0.55;}

/* ---------- watermark ---------- */
.watermark{
  position:fixed;top:8px;left:10px;z-index:9999;
  font-family:var(--font-display);font-weight:700;font-size:11px;
  letter-spacing:1px;color:rgba(255,255,255,0.35);
  pointer-events:none;user-select:none;
}

/* ---------- gate (name/age/policy) ---------- */
.gate{
  position:fixed;inset:0;z-index:1000;
  background:radial-gradient(circle at 30% 20%, #1b2150 0%, #0b0d1a 70%);
  display:flex;align-items:center;justify-content:center;padding:24px;
}
.gate-card{
  width:100%;max-width:460px;background:var(--card);
  border:1px solid var(--line);border-radius:18px;padding:32px;
  box-shadow:0 30px 80px rgba(0,0,0,0.5);
}
.gate-card h1{
  font-family:var(--font-display);font-size:26px;margin-bottom:6px;
  background:linear-gradient(90deg,var(--blue),var(--green),var(--orange),var(--red));
  -webkit-background-clip:text;background-clip:text;color:transparent;
}
.gate-card p.sub{color:var(--muted);font-size:13px;margin-bottom:22px;}
.field{margin-bottom:16px;}
.field label{display:block;font-size:12px;color:var(--muted);margin-bottom:6px;}
.field input{
  width:100%;padding:11px 12px;border-radius:10px;border:1px solid var(--line);
  background:#0e1130;color:var(--text);font-size:14px;
}
.field input:focus{outline:2px solid var(--blue);}
.policy-box{
  background:#0e1130;border:1px solid var(--line);border-radius:10px;
  padding:12px;font-size:12px;color:var(--muted);max-height:120px;overflow-y:auto;margin-bottom:12px;
}
.check-row{display:flex;gap:8px;align-items:flex-start;font-size:12.5px;color:var(--muted);margin-bottom:18px;}
.check-row input{margin-top:3px;}
.btn{
  border:none;border-radius:10px;padding:12px 18px;font-weight:700;cursor:pointer;
  font-family:var(--font-display);font-size:14px;letter-spacing:0.3px;
  background:linear-gradient(90deg,var(--blue),var(--green));color:#06160c;
  width:100%;transition:transform .15s ease;
}
.btn:hover{transform:translateY(-2px);}
.btn:disabled{opacity:0.4;cursor:not-allowed;transform:none;}
.error-msg{color:var(--red);font-size:12px;margin-top:8px;display:none;}

/* ---------- app shell ---------- */
.app{display:none;position:relative;z-index:1;min-height:100vh;}
header.topbar{
  display:flex;align-items:center;justify-content:space-between;
  padding:16px 26px;border-bottom:1px solid var(--line);
  background:rgba(11,13,26,0.7);backdrop-filter:blur(8px);
  position:sticky;top:0;z-index:50;
}
.brand{font-family:var(--font-display);font-weight:700;font-size:18px;display:flex;align-items:center;gap:10px;}
.brand .dot{width:10px;height:10px;border-radius:50%;background:conic-gradient(var(--blue),var(--green),var(--orange),var(--red));}
.userpill{display:flex;align-items:center;gap:10px;font-size:13px;color:var(--muted);}
.userpill b{color:var(--text);}
.history-toggle{
  background:var(--card);border:1px solid var(--line);color:var(--text);
  padding:8px 12px;border-radius:8px;font-size:12px;cursor:pointer;
}

.layout{display:flex;}
aside.history{
  width:260px;flex-shrink:0;border-right:1px solid var(--line);
  min-height:calc(100vh - 60px);padding:18px;background:rgba(13,16,36,0.6);
  transition:margin-left .25s ease;
}
aside.history.collapsed{margin-left:-260px;}
aside.history h3{font-size:12px;text-transform:uppercase;letter-spacing:1px;color:var(--muted);margin-bottom:12px;}
.hist-item{
  font-size:13px;padding:9px 10px;border-radius:8px;margin-bottom:6px;cursor:pointer;
  border:1px solid transparent;color:var(--text);
}
.hist-item:hover{background:var(--card);border-color:var(--line);}
.hist-empty{font-size:12px;color:var(--muted);}

main{flex:1;padding:30px clamp(16px,4vw,50px) 80px;}

/* options grid */
.options-row{display:flex;align-items:flex-start;gap:26px;}
.options-main{flex:1;min-width:0;}
.gold-side{
  width:280px;flex-shrink:0;background:var(--card);border:1px solid var(--line);
  border-radius:16px;padding:18px;position:sticky;top:90px;
}
.gold-side h4{font-family:var(--font-display);font-size:14px;margin-bottom:4px;color:var(--orange);}
.gold-side p{font-size:11px;color:var(--muted);margin-bottom:12px;}
.gold-side input{
  width:100%;padding:9px 10px;border-radius:8px;border:1px solid var(--line);
  background:#0e1130;color:var(--text);margin-bottom:10px;font-size:13px;
}
.gold-result{font-size:18px;font-family:var(--font-display);color:var(--green);}

h2.section-title{font-family:var(--font-display);font-size:26px;margin-bottom:6px;}
p.section-sub{color:var(--muted);font-size:13px;margin-bottom:26px;max-width:640px;}

.grid-options{display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:16px;margin-bottom:10px;}
.opt-card{
  background:var(--card);border:1px solid var(--line);border-radius:16px;padding:20px;
  cursor:pointer;transition:transform .2s ease, box-shadow .2s ease;position:relative;overflow:hidden;
}
.opt-card:hover{transform:translateY(-4px) scale(1.01);}
.opt-card.active{border-color:var(--blue);box-shadow:0 0 0 2px var(--blue) inset;}
.opt-card .tag{
  display:inline-block;font-size:10px;letter-spacing:1px;text-transform:uppercase;
  padding:3px 8px;border-radius:20px;margin-bottom:10px;font-weight:700;
}
.opt-card h3{font-family:var(--font-display);font-size:17px;margin-bottom:6px;}
.opt-card p{font-size:12px;color:var(--muted);}
.tag.sip{background:rgba(27,95,255,0.18);color:#7da3ff;}
.tag.mf{background:rgba(31,207,108,0.18);color:#6cf0a4;}
.tag.stock{background:rgba(255,157,47,0.18);color:#ffc278;}
.tag.crypto{background:rgba(255,59,59,0.18);color:#ff9090;}
.tag.gold{background:rgba(255,210,80,0.18);color:#ffe199;}

.detail{
  margin-top:28px;background:var(--card);border:1px solid var(--line);border-radius:18px;
  padding:28px;display:none;
}
.detail.show{display:block;animation:fadein .3s ease;}
@keyframes fadein{from{opacity:0;transform:translateY(8px);}to{opacity:1;transform:translateY(0);}}
.detail h2{font-family:var(--font-display);font-size:22px;margin-bottom:14px;}
.pros-cons{display:grid;grid-template-columns:1fr 1fr;gap:18px;margin-bottom:22px;}
.pc-box{border-radius:12px;padding:16px;font-size:13px;}
.pc-box.pros{background:rgba(31,207,108,0.08);border:1px solid rgba(31,207,108,0.25);}
.pc-box.cons{background:rgba(255,59,59,0.08);border:1px solid rgba(255,59,59,0.25);}
.pc-box h4{font-family:var(--font-display);font-size:13px;margin-bottom:10px;}
.pc-box.pros h4{color:var(--green);}
.pc-box.cons h4{color:var(--red);}
.pc-box ul{padding-left:18px;color:var(--text);}
.pc-box li{margin-bottom:6px;}

.subblock{margin-bottom:22px;}
.subblock h4{font-family:var(--font-display);font-size:14px;margin-bottom:10px;color:var(--blue);}
.pill-list{display:flex;flex-wrap:wrap;gap:8px;}
.pill{background:#0e1130;border:1px solid var(--line);padding:6px 12px;border-radius:20px;font-size:12px;}

.live-box{
  display:flex;gap:14px;flex-wrap:wrap;background:#0e1130;border:1px solid var(--line);
  border-radius:12px;padding:14px;margin-bottom:18px;
}
.live-stat{flex:1;min-width:140px;}
.live-stat .lbl{font-size:11px;color:var(--muted);}
.live-stat .val{font-family:var(--font-display);font-size:18px;}
.demo-note{font-size:11px;color:var(--muted);font-style:italic;margin-top:6px;}

.calc-box{background:#0e1130;border:1px solid var(--line);border-radius:14px;padding:20px;}
.calc-row{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:14px;}
.calc-row label{font-size:12px;color:var(--muted);display:block;margin-bottom:6px;}
.calc-row input[type=range]{width:100%;}
.calc-row input[type=number]{
  width:100%;padding:8px 10px;border-radius:8px;border:1px solid var(--line);
  background:var(--card);color:var(--text);font-size:13px;
}
.calc-out{display:flex;gap:24px;flex-wrap:wrap;margin-top:14px;}
.calc-out div{font-size:12px;color:var(--muted);}
.calc-out div b{display:block;font-family:var(--font-display);font-size:18px;color:var(--green);}

.stock-search{display:flex;gap:10px;margin-bottom:16px;}
.stock-search input{flex:1;padding:10px 12px;border-radius:8px;border:1px solid var(--line);background:#0e1130;color:var(--text);}
.stock-search button{padding:10px 16px;border-radius:8px;border:none;background:var(--orange);color:#1a0e00;font-weight:700;cursor:pointer;}
canvas.chart{width:100%;height:260px;background:#0e1130;border-radius:12px;border:1px solid var(--line);}

footer{
  text-align:center;padding:30px;font-size:11px;color:var(--muted);border-top:1px solid var(--line);
}
@media(max-width:900px){
  .options-row{flex-direction:column;}
  .gold-side{width:100%;position:static;}
  aside.history{position:fixed;left:0;top:60px;bottom:0;z-index:60;}
}
</style>
</head>
<body>

<div class="watermark">M.KARTHE</div>
<canvas id="bg-canvas"></canvas>

<!-- ===================== GATE ===================== -->
<div class="gate" id="gate">
  <div class="gate-card" id="gateCard">
    <h1>InvestWise</h1>
    <p class="sub">A free, informative space to understand investing — before you risk real money.</p>

    <div id="step1">
      <div class="field">
        <label>Your name</label>
        <input id="inName" type="text" placeholder="e.g. Karthik">
      </div>
      <div class="field">
        <label>Your age</label>
        <input id="inAge" type="number" placeholder="e.g. 22">
      </div>
      <div class="field">
        <label>Email (used to log in on other devices)</label>
        <input id="inEmail" type="email" placeholder="you@example.com">
      </div>
      <div class="field">
        <label>Password (min 6 characters)</label>
        <input id="inPassword" type="password" placeholder="••••••••">
      </div>
      <p class="error-msg" id="ageError">You must be 16 or older to use this website.</p>
      <button class="btn" id="toStep2">Continue</button>
      <p class="demo-note" style="margin-top:10px;">Already registered? <a href="#" id="loginInstead" style="color:var(--blue);">Log in instead</a></p>
    </div>

    <div id="step2" style="display:none;">
      <div class="policy-box">
        This website and its owner have <b>no intention or responsibility</b> to make any user invest
        money, and accept no responsibility for any losses arising from trading or investment decisions.
        All content here — SIP, Mutual Funds, Stock Market, Crypto Trading, and Gold ETFs — is provided
        <b>strictly for informative and awareness purposes only</b>. Nothing on this site is financial
        advice. Always consult a licensed financial advisor before investing real money.
      </div>
      <label class="check-row">
        <input type="checkbox" id="policyCheck">
        I have read and accept this policy.
      </label>
      <button class="btn" id="enterSite" disabled>Enter InvestWise</button>
    </div>
  </div>
</div>

<!-- ===================== APP ===================== -->
<div class="app" id="app">
  <header class="topbar">
    <div class="brand"><span class="dot"></span> InvestWise</div>
    <div class="userpill">
      <button class="history-toggle" id="historyToggle">☰ History</button>
      <span>Welcome, <b id="userNameTag"></b></span>
    </div>
  </header>

  <div class="layout">
    <aside class="history" id="historyPanel">
      <h3>Your activity</h3>
      <div id="historyList"><p class="hist-empty">Nothing viewed yet.</p></div>
    </aside>

    <main>
      <div class="options-row">
        <div class="options-main">
          <h2 class="section-title">Choose what you want to learn about</h2>
          <p class="section-sub">Pick an option below. Each one explains the upside, the risk, and gives you a tool to try the maths yourself — no money involved.</p>

          <div class="grid-options">
            <div class="opt-card" data-opt="sip"><span class="tag sip">Steady</span><h3>SIP</h3><p>Systematic Investment Plans — small, regular amounts over time.</p></div>
            <div class="opt-card" data-opt="mf"><span class="tag mf">Diversified</span><h3>Mutual Funds</h3><p>Pooled money managed by professionals across many assets.</p></div>
            <div class="opt-card" data-opt="stock"><span class="tag stock">Direct</span><h3>Stock Market</h3><p>Buying ownership in companies directly, India & global.</p></div>
            <div class="opt-card" data-opt="crypto"><span class="tag crypto">High Risk</span><h3>Crypto Trading</h3><p>Digital, decentralized assets — high reward, high volatility.</p></div>
            <div class="opt-card" data-opt="gold"><span class="tag gold">Safe Haven</span><h3>Gold ETF</h3><p>Paper gold that tracks the price of physical gold.</p></div>
          </div>

          <!-- SIP -->
          <div class="detail" id="detail-sip">
            <h2>SIP — Systematic Investment Plan</h2>
            <div class="pros-cons">
              <div class="pc-box pros"><h4>Advantages</h4><ul>
                <li>Builds discipline — fixed small amount invested monthly</li>
                <li>Rupee-cost averaging smooths out market ups and downs</li>
                <li>Power of compounding over long horizons</li>
                <li>Low entry amount — start from ₹500/month</li>
              </ul></div>
              <div class="pc-box cons"><h4>Disadvantages</h4><ul>
                <li>Returns aren't guaranteed — still market-linked</li>
                <li>Short-term volatility can be unsettling</li>
                <li>Exit load / lock-in on some ELSS SIPs</li>
                <li>Easy to forget the "long term" part and panic-sell</li>
              </ul></div>
            </div>
            <div class="subblock">
              <h4>Commonly discussed SIP categories in India (informational only)</h4>
              <div class="pill-list">
                <span class="pill">Large-cap index funds</span>
                <span class="pill">Flexi-cap funds</span>
                <span class="pill">Mid-cap funds</span>
                <span class="pill">ELSS (tax-saving) funds</span>
                <span class="pill">Nifty 50 / Sensex index funds</span>
              </div>
              <p class="demo-note">Names of specific schemes are deliberately not ranked here — fund performance changes constantly. Check AMFI / your fund house before deciding.</p>
            </div>
            <div class="subblock">
              <h4>SIP Calculator</h4>
              <div class="calc-box">
                <div class="calc-row">
                  <div><label>Monthly investment (₹)</label><input type="number" id="sipAmt" value="5000"></div>
                  <div><label>Expected annual return (%)</label><input type="number" id="sipRate" value="12"></div>
                </div>
                <div class="calc-row">
                  <div><label>Time period (years)</label><input type="number" id="sipYears" value="10"></div>
                  <div></div>
                </div>
                <div class="calc-out">
                  <div>Invested amount<b id="sipInvested">₹0</b></div>
                  <div>Estimated returns<b id="sipReturns">₹0</b></div>
                  <div>Total value<b id="sipTotal">₹0</b></div>
                </div>
              </div>
            </div>
          </div>

          <!-- MUTUAL FUND -->
          <div class="detail" id="detail-mf">
            <h2>Mutual Funds</h2>
            <div class="pros-cons">
              <div class="pc-box pros"><h4>Advantages</h4><ul>
                <li>Professional fund management</li>
                <li>Instant diversification across many companies/sectors</li>
                <li>Wide range of risk levels to choose from</li>
                <li>Regulated by SEBI, relatively transparent</li>
              </ul></div>
              <div class="pc-box cons"><h4>Disadvantages</h4><ul>
                <li>Expense ratio / management fees reduce returns</li>
                <li>No control over individual stock selection</li>
                <li>Past performance ≠ future results</li>
                <li>Exit loads on early redemption for some funds</li>
              </ul></div>
            </div>
            <div class="subblock">
              <h4>Market snapshot (demo data)</h4>
              <div class="live-box" id="mfLive"></div>
              <p class="demo-note">This panel needs a live data feed (e.g. AMFI NAV API) to auto-refresh daily — see setup notes below the page.</p>
            </div>
          </div>

          <!-- STOCK -->
          <div class="detail" id="detail-stock">
            <h2>Stock Market</h2>
            <div class="pros-cons">
              <div class="pc-box pros"><h4>Advantages</h4><ul>
                <li>Direct ownership and full control over picks</li>
                <li>Highly liquid — buy/sell almost instantly</li>
                <li>Potential for high long-term growth</li>
                <li>Dividend income on top of price growth</li>
              </ul></div>
              <div class="pc-box cons"><h4>Disadvantages</h4><ul>
                <li>Requires research, time, and emotional discipline</li>
                <li>High volatility, especially short-term</li>
                <li>Single-stock risk if not diversified</li>
                <li>Brokerage, STT, and tax costs on each trade</li>
              </ul></div>
            </div>
            <div class="subblock">
              <h4>Indices snapshot (demo data)</h4>
              <div class="live-box" id="stockLive"></div>
            </div>
            <div class="subblock">
              <h4>Search a stock — 2-year trend (illustrative demo chart)</h4>
              <div class="stock-search">
                <input id="stockSearchInput" placeholder="e.g. RELIANCE, TCS, INFY">
                <button id="stockSearchBtn">Show chart</button>
              </div>
              <canvas class="chart" id="stockChart"></canvas>
              <p class="demo-note">Demo chart uses a simulated random-walk price series to illustrate trend reading — it is NOT real price history. Wire in a real API for live data (see notes below).</p>
            </div>
          </div>

          <!-- CRYPTO -->
          <div class="detail" id="detail-crypto">
            <h2>Crypto Trading</h2>
            <div class="pros-cons">
              <div class="pc-box pros"><h4>Advantages</h4><ul>
                <li>24/7 global market, decentralized, no single owner</li>
                <li>Potential for very high returns in short windows</li>
                <li>Low barrier to entry — fractional buying possible</li>
                <li>Transparent blockchain transaction records</li>
              </ul></div>
              <div class="pc-box cons"><h4>Disadvantages</h4><ul>
                <li>Extremely volatile — prices can swing 20%+ in a day</li>
                <li>Largely unregulated, scam and rug-pull risk</li>
                <li>No intrinsic cash flow (no dividends/interest)</li>
                <li>Irreversible transactions — lost keys = lost funds</li>
              </ul></div>
            </div>
            <div class="subblock">
              <h4>How people try to earn from it</h4>
              <p style="font-size:13px;color:var(--muted);line-height:1.6;">
                Common approaches discussed publicly include long-term "HODLing," active day-trading,
                staking coins to earn yield, and providing liquidity on decentralized exchanges.
                Every one of these carries real risk of loss — none is guaranteed income, and most
                short-term traders underperform simply holding.
              </p>
            </div>
            <div class="subblock">
              <h4>Common crypto types</h4>
              <div class="pill-list">
                <span class="pill">Bitcoin (BTC) — store of value</span>
                <span class="pill">Ethereum (ETH) — smart contracts</span>
                <span class="pill">Stablecoins (USDT/USDC) — pegged to fiat</span>
                <span class="pill">Altcoins — smaller, higher risk</span>
                <span class="pill">Memecoins — speculative, very high risk</span>
              </div>
            </div>
          </div>

          <!-- GOLD -->
          <div class="detail" id="detail-gold">
            <h2>Gold ETF</h2>
            <div class="pros-cons">
              <div class="pc-box pros"><h4>Advantages</h4><ul>
                <li>No storage/security worries like physical gold</li>
                <li>Highly liquid, traded like a stock</li>
                <li>Good hedge against inflation and market crashes</li>
                <li>Small ticket size — buy as little as 1 unit (~1 gram)</li>
              </ul></div>
              <div class="pc-box cons"><h4>Disadvantages</h4><ul>
                <li>No physical gold in hand</li>
                <li>Expense ratio and brokerage costs</li>
                <li>Returns depend purely on gold price, no extra yield</li>
                <li>Needs a demat account to trade</li>
              </ul></div>
            </div>
            <div class="subblock">
              <h4>Gold price (demo data)</h4>
              <div class="live-box" id="goldLive"></div>
              <p class="demo-note">Connect a live gold-price API to replace this demo value (see notes below).</p>
            </div>
            <div class="subblock">
              <h4>Apps commonly used to invest in gold safely</h4>
              <div class="pill-list">
                <span class="pill">SGB via RBI/your bank (Sovereign Gold Bonds)</span>
                <span class="pill">Gold ETFs via any SEBI-registered broker</span>
                <span class="pill">Digital gold via licensed platforms</span>
              </div>
            </div>
          </div>

        </div>

        <!-- gold quick calculator sidebar -->
        <div class="gold-side">
          <h4>💰 Quick Gold Calculator</h4>
          <p>Enter grams to estimate value at today's demo rate.</p>
          <input type="number" id="quickGoldGrams" placeholder="Grams of gold">
          <div class="gold-result" id="quickGoldResult">₹0</div>
        </div>
      </div>
    </main>
  </div>

  <footer>
    © <span id="yearNow"></span> InvestWise — All rights reserved by M.Karthekeyan. For informational and awareness purposes only. Not financial advice.
  </footer>
</div>

<script>
/* ---------------- 3D background (three.js) ---------------- */
(function(){
  const canvas = document.getElementById('bg-canvas');
  const renderer = new THREE.WebGLRenderer({canvas, alpha:true, antialias:true});
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(60, window.innerWidth/window.innerHeight, 0.1, 1000);
  camera.position.z = 26;

  const colors = [0x1b5fff,0xff3b3b,0x1fcf6c,0xff9d2f];
  const group = new THREE.Group();
  for(let i=0;i<60;i++){
    const geo = Math.random()>0.5
      ? new THREE.IcosahedronGeometry(Math.random()*0.8+0.3,0)
      : new THREE.OctahedronGeometry(Math.random()*0.8+0.3,0);
    const mat = new THREE.MeshBasicMaterial({color:colors[i%colors.length], wireframe:true, transparent:true, opacity:0.55});
    const mesh = new THREE.Mesh(geo,mat);
    mesh.position.set((Math.random()-0.5)*40,(Math.random()-0.5)*30,(Math.random()-0.5)*30);
    mesh.userData.speed = (Math.random()*0.4+0.1) * (Math.random()>0.5?1:-1);
    group.add(mesh);
  }
  scene.add(group);

  function animate(){
    requestAnimationFrame(animate);
    group.children.forEach(m=>{
      m.rotation.x += 0.002*m.userData.speed;
      m.rotation.y += 0.003*m.userData.speed;
    });
    group.rotation.y += 0.0006;
    renderer.render(scene,camera);
  }
  animate();
  window.addEventListener('resize', ()=>{
    camera.aspect = window.innerWidth/window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
})();

/* ---------------- user storage: Firebase (primary) + localStorage (fallback/cache) ---------------- */
const Store = {
  getUsers(){ return JSON.parse(localStorage.getItem('iw_users')||'{}'); },
  saveUsers(u){ localStorage.setItem('iw_users', JSON.stringify(u)); },
  getCurrent(){ return JSON.parse(localStorage.getItem('iw_current')||'null'); },
  setCurrent(u){ localStorage.setItem('iw_current', JSON.stringify(u)); }
};

let firebaseReady = false;
window.addEventListener('firebase-ready', ()=>{ firebaseReady = true; });

document.getElementById('yearNow').textContent = new Date().getFullYear();

let isLoginMode = false;
document.getElementById('loginInstead').addEventListener('click', (e)=>{
  e.preventDefault();
  isLoginMode = true;
  document.querySelector('.gate-card h1').textContent = 'Welcome back';
  document.getElementById('toStep2').textContent = 'Log in';
});

document.getElementById('toStep2').addEventListener('click', async ()=>{
  const name = document.getElementById('inName').value.trim();
  const age = parseInt(document.getElementById('inAge').value,10);
  const email = document.getElementById('inEmail').value.trim();
  const password = document.getElementById('inPassword').value;
  const err = document.getElementById('ageError');

  if(isLoginMode){
    if(!email || !password){ err.style.display='block'; err.textContent='Enter your email and password.'; return; }
    await loginExisting(email, password);
    return;
  }

  if(!name || !age || !email || password.length < 6){
    err.style.display='block';
    err.textContent='Fill in name, age, email and a password of at least 6 characters.';
    return;
  }
  if(age < 16){ err.style.display='block'; err.textContent='You must be 16 or older to use this website.'; return; }
  err.style.display='none';
  document.getElementById('step1').style.display='none';
  document.getElementById('step2').style.display='block';
});

async function loginExisting(email, password){
  const err = document.getElementById('ageError');
  if(!firebaseReady){ err.style.display='block'; err.textContent='Connecting… try again in a second.'; return; }
  try{
    const F = window.Firebase;
    const cred = await F.signInWithEmailAndPassword(F.auth, email, password);
    const snap = await F.getDoc(F.doc(F.db, 'users', cred.user.uid));
    const profile = snap.exists() ? snap.data() : { name: email.split('@')[0] };
    enterApp(cred.user.uid, profile.name);
  }catch(e){
    err.style.display='block';
    err.textContent = 'Login failed: ' + e.message.replace('Firebase: ','');
  }
}

document.getElementById('policyCheck').addEventListener('change', (e)=>{
  document.getElementById('enterSite').disabled = !e.target.checked;
});

document.getElementById('enterSite').addEventListener('click', async ()=>{
  const name = document.getElementById('inName').value.trim();
  const age = parseInt(document.getElementById('inAge').value,10);
  const email = document.getElementById('inEmail').value.trim();
  const password = document.getElementById('inPassword').value;
  const err = document.getElementById('ageError');

  // Always keep a local fallback copy so the site still works offline / before Firebase is configured
  const users = Store.getUsers();
  const key = (email || name).toLowerCase();
  if(!users[key]) users[key] = { name, age, registeredOn: new Date().toISOString(), history: [] };
  Store.saveUsers(users);
  Store.setCurrent({key, name});

  if(firebaseReady && window.Firebase){
    try{
      const F = window.Firebase;
      const cred = await F.createUserWithEmailAndPassword(F.auth, email, password);
      await F.setDoc(F.doc(F.db, 'users', cred.user.uid), {
        name, age, registeredOn: F.serverTimestamp(), policyAccepted: true
      });
      enterApp(cred.user.uid, name);
      return;
    }catch(e){
      err.style.display='block';
      err.textContent = 'Account creation issue: ' + e.message.replace('Firebase: ','') + ' — continuing in local-only mode.';
    }
  }
  // Fallback: no Firebase configured yet, just continue locally
  enterApp(null, name);
});

function enterApp(uid, name){
  window.__uid = uid;
  document.getElementById('gate').style.display='none';
  document.getElementById('app').style.display='block';
  document.getElementById('userNameTag').textContent = name;
  renderHistory();
}

/* ---------------- option cards + detail panels ---------------- */
const optCards = document.querySelectorAll('.opt-card');
optCards.forEach(card=>{
  card.addEventListener('click', ()=>{
    const opt = card.dataset.opt;
    optCards.forEach(c=>c.classList.remove('active'));
    card.classList.add('active');
    document.querySelectorAll('.detail').forEach(d=>d.classList.remove('show'));
    document.getElementById('detail-'+opt).classList.add('show');
    document.getElementById('detail-'+opt).scrollIntoView({behavior:'smooth', block:'start'});
    logHistory(opt);
  });
});

function logHistory(opt){
  const cur = Store.getCurrent();
  const labelMap = {sip:'SIP', mf:'Mutual Funds', stock:'Stock Market', crypto:'Crypto Trading', gold:'Gold ETF'};
  const entry = { label: labelMap[opt], time: new Date().toLocaleString() };

  if(window.__uid && firebaseReady){
    const F = window.Firebase;
    F.addDoc(F.collection(F.db, 'users', window.__uid, 'history'), {
      label: entry.label, time: F.serverTimestamp()
    }).then(renderHistory).catch(()=>{});
    return;
  }
  if(!cur) return;
  const users = Store.getUsers();
  users[cur.key].history.unshift(entry);
  users[cur.key].history = users[cur.key].history.slice(0,20);
  Store.saveUsers(users);
  renderHistory();
}

async function renderHistory(){
  const list = document.getElementById('historyList');

  if(window.__uid && firebaseReady){
    try{
      const F = window.Firebase;
      const q = F.query(F.collection(F.db, 'users', window.__uid, 'history'), F.orderBy('time','desc'), F.limit(20));
      const snap = await F.getDocs(q);
      if(snap.empty){ list.innerHTML = '<p class="hist-empty">Nothing viewed yet.</p>'; return; }
      list.innerHTML = '';
      snap.forEach(d=>{
        const v = d.data();
        const t = v.time && v.time.toDate ? v.time.toDate().toLocaleString() : '';
        list.innerHTML += `<div class="hist-item">${v.label}<br><span style="color:var(--muted);font-size:11px;">${t}</span></div>`;
      });
      return;
    }catch(e){ /* fall through to local */ }
  }

  const cur = Store.getCurrent();
  if(!cur) return;
  const users = Store.getUsers();
  const hist = users[cur.key]?.history || [];
  if(hist.length===0){ list.innerHTML = '<p class="hist-empty">Nothing viewed yet.</p>'; return; }
  list.innerHTML = hist.map(h=>`<div class="hist-item">${h.label}<br><span style="color:var(--muted);font-size:11px;">${h.time}</span></div>`).join('');
}

document.getElementById('historyToggle').addEventListener('click', ()=>{
  document.getElementById('historyPanel').classList.toggle('collapsed');
});

/* ---------------- SIP calculator ---------------- */
function calcSIP(){
  const P = parseFloat(document.getElementById('sipAmt').value)||0;
  const annual = parseFloat(document.getElementById('sipRate').value)||0;
  const years = parseFloat(document.getElementById('sipYears').value)||0;
  const n = years*12;
  const r = annual/100/12;
  const fv = r===0 ? P*n : P * ((Math.pow(1+r,n)-1)/r) * (1+r);
  const invested = P*n;
  const returns = fv - invested;
  document.getElementById('sipInvested').textContent = '₹'+Math.round(invested).toLocaleString('en-IN');
  document.getElementById('sipReturns').textContent = '₹'+Math.round(returns).toLocaleString('en-IN');
  document.getElementById('sipTotal').textContent = '₹'+Math.round(fv).toLocaleString('en-IN');
}
['sipAmt','sipRate','sipYears'].forEach(id=>document.getElementById(id).addEventListener('input', calcSIP));
calcSIP();

/* ---------------- demo live data (replace with real APIs) ---------------- */
function renderLive(){
  const goldPricePerGram = 6850 + Math.round((Math.random()-0.5)*40); // demo
  document.getElementById('goldLive').innerHTML = `
    <div class="live-stat"><div class="lbl">24K Gold (per gram, demo)</div><div class="val">₹${goldPricePerGram}</div></div>
    <div class="live-stat"><div class="lbl">Last updated</div><div class="val">${new Date().toLocaleTimeString()}</div></div>`;
  window.__goldRate = goldPricePerGram;

  document.getElementById('mfLive').innerHTML = `
    <div class="live-stat"><div class="lbl">Avg large-cap fund 1Y (demo)</div><div class="val">+14.2%</div></div>
    <div class="live-stat"><div class="lbl">Avg flexi-cap fund 1Y (demo)</div><div class="val">+16.8%</div></div>
    <div class="live-stat"><div class="lbl">Avg debt fund 1Y (demo)</div><div class="val">+7.1%</div></div>`;

  document.getElementById('stockLive').innerHTML = `
    <div class="live-stat"><div class="lbl">NIFTY 50 (demo)</div><div class="val">24,${Math.floor(Math.random()*900+100)}</div></div>
    <div class="live-stat"><div class="lbl">SENSEX (demo)</div><div class="val">81,${Math.floor(Math.random()*900+100)}</div></div>
    <div class="live-stat"><div class="lbl">S&P 500 (demo)</div><div class="val">6,${Math.floor(Math.random()*100+100)}</div></div>
    <div class="live-stat"><div class="lbl">NASDAQ (demo)</div><div class="val">21,${Math.floor(Math.random()*900+100)}</div></div>`;
}
renderLive();
setInterval(renderLive, 60000);

/* ---------------- REAL mutual fund data from AMFI (India) ----------------
   AMFI publishes a free daily NAV file at:
   https://www.amfiindia.com/spages/NAVAll.txt
   Browsers can't fetch it directly (no CORS headers), so this uses a
   public CORS proxy as a quick demo. For production, replace this with
   your own tiny serverless function (Netlify/Vercel) that fetches AMFI
   server-side and returns JSON — instructions given in chat. */
async function loadRealMFData(){
  const sampleSchemes = [
    { name: 'HDFC Flexi Cap Fund', code: '118534' },
    { name: 'SBI Bluechip Fund', code: '118673' },
    { name: 'Parag Parikh Flexi Cap Fund', code: '122639' }
  ];
  try{
    const res = await fetch('https://api.allorigins.win/raw?url=' + encodeURIComponent('https://www.amfiindia.com/spages/NAVAll.txt'));
    const text = await res.text();
    const lines = text.split('\n');
    const results = [];
    sampleSchemes.forEach(s=>{
      const line = lines.find(l=>l.startsWith(s.code+';'));
      if(line){
        const parts = line.split(';');
        results.push({ name: s.name, nav: parts[4], date: parts[5] });
      }
    });
    if(results.length){
      document.getElementById('mfLive').innerHTML = results.map(r=>`
        <div class="live-stat"><div class="lbl">${r.name} — NAV (₹)</div><div class="val">${r.nav}</div></div>
      `).join('') + `<div class="live-stat"><div class="lbl">As of</div><div class="val">${results[0].date}</div></div>`;
      const noteEl = document.querySelector('#detail-mf .demo-note');
      if(noteEl) noteEl.textContent = 'Live NAV data from AMFI (India), fetched via a public CORS proxy for this demo.';
    }
  }catch(e){
    console.warn('AMFI live fetch failed, showing demo data instead.', e);
  }
}
loadRealMFData();

/* ---------------- quick gold sidebar calculator ---------------- */
document.getElementById('quickGoldGrams').addEventListener('input', (e)=>{
  const grams = parseFloat(e.target.value)||0;
  const rate = window.__goldRate || 6850;
  document.getElementById('quickGoldResult').textContent = '₹'+Math.round(grams*rate).toLocaleString('en-IN');
});

/* ---------------- stock demo chart (random walk, illustrative only) ---------------- */
function drawStockChart(symbol){
  const canvas = document.getElementById('stockChart');
  const ctx = canvas.getContext('2d');
  const w = canvas.width = canvas.clientWidth * devicePixelRatio;
  const h = canvas.height = canvas.clientHeight * devicePixelRatio;
  ctx.clearRect(0,0,w,h);

  // generate 24 months of pseudo data (seeded by symbol)
  let seed = symbol.split('').reduce((a,c)=>a+c.charCodeAt(0),0) || 1;
  function rnd(){ seed = (seed*9301+49297)%233280; return seed/233280; }
  let price = 500 + rnd()*1500;
  const points = [];
  for(let i=0;i<24;i++){
    price += (rnd()-0.45)*price*0.06;
    price = Math.max(price, 20);
    points.push(price);
  }
  const min = Math.min(...points), max = Math.max(...points);
  const pad = 20*devicePixelRatio;

  ctx.strokeStyle = 'rgba(255,255,255,0.08)';
  ctx.lineWidth = 1;
  for(let i=0;i<=4;i++){
    const y = pad + (h-2*pad)*i/4;
    ctx.beginPath(); ctx.moveTo(pad,y); ctx.lineTo(w-pad,y); ctx.stroke();
  }

  ctx.beginPath();
  points.forEach((p,i)=>{
    const x = pad + (w-2*pad)*i/(points.length-1);
    const y = h-pad - (h-2*pad)*((p-min)/(max-min||1));
    if(i===0) ctx.moveTo(x,y); else ctx.lineTo(x,y);
  });
  const grad = ctx.createLinearGradient(0,0,w,0);
  grad.addColorStop(0,'#1b5fff'); grad.addColorStop(0.4,'#1fcf6c');
  grad.addColorStop(0.7,'#ff9d2f'); grad.addColorStop(1,'#ff3b3b');
  ctx.strokeStyle = grad;
  ctx.lineWidth = 2.5*devicePixelRatio;
  ctx.stroke();

  ctx.fillStyle = '#9aa0c3';
  ctx.font = (11*devicePixelRatio)+'px Inter';
  ctx.fillText(symbol.toUpperCase()+' · 24-month illustrative trend (demo data)', pad, pad-6);
}
document.getElementById('stockSearchBtn').addEventListener('click', ()=>{
  const v = document.getElementById('stockSearchInput').value.trim() || 'DEMO';
  drawStockChart(v);
});
drawStockChart('NIFTY');
</script>
</body>
</html>
