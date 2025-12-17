[my-year.html](https://github.com/user-attachments/files/24210614/my-year.html)
<!doctype html><html lang="zh-Hant"><head>
<meta charset="utf-8"/><meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>我的年度工時｜拾葉設計</title>
<style>
:root{
  --bg:#0b0f14;
  --card:#101826;
  --stroke:rgba(255,255,255,.14);
  --stroke2:rgba(255,255,255,.22);
  --text:#ffffff;
  --muted:rgba(255,255,255,.70);
  --muted2:rgba(255,255,255,.55);
  --accent:rgba(255,255,255,.92);
}
*{box-sizing:border-box}
html,body{height:100%}
body{
  margin:0;
  font-family: ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Noto Sans TC", Arial;
  background: #000;
  color: var(--text);
}
.bg{
  position:fixed; inset:0; z-index:-2;
  background:#000;
  background-position:center;
  background-size:cover;
  background-repeat:no-repeat;
}
.wrap{min-height:100%; padding:54px 22px;}
.container{max-width:1100px;margin:0 auto;}
.hd{
  display:flex; align-items:flex-start; justify-content:space-between;
  gap:16px;
}
.brand h1{margin:0;font-weight:250;font-size:52px;letter-spacing:.06em;text-transform:uppercase}
.brand p{margin:10px 0 0 0;font-size:12px;letter-spacing:.20em;text-transform:uppercase;color:var(--muted2)}
.menu{
  width:40px;height:28px;display:flex;flex-direction:column;gap:6px;justify-content:center;opacity:.9
}
.menu span{height:2px;background:rgba(255,255,255,.85);border-radius:2px}
.card{
  margin-top:26px;
  background: rgba(16,24,38,.55);
  border: 1px solid var(--stroke);
  border-radius: 16px;
  padding: 16px;
  backdrop-filter: blur(10px);
}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:14px}
@media (max-width:860px){.grid{grid-template-columns:1fr}}
.label{font-size:12px;color:var(--muted2);letter-spacing:.14em;text-transform:uppercase;margin-bottom:8px}
.input, select, textarea{
  width:100%;
  border-radius: 12px;
  border: 1px solid var(--stroke2);
  background: rgba(0,0,0,.25);
  color: var(--text);
  padding: 10px 12px;
  outline: none;
}
textarea{min-height:96px;resize:vertical}
.btnRow{display:flex; gap:10px; flex-wrap:wrap; align-items:center; margin-top:14px}
button.btn{
  border-radius: 12px;
  border: 1px solid var(--stroke2);
  background: rgba(0,0,0,.25);
  color: var(--text);
  padding: 10px 14px;
  cursor:pointer;
  letter-spacing:.14em;
  text-transform:uppercase;
  font-size:12px;
}
button.btn:hover{border-color: rgba(255,255,255,.55)}
a.link{
  color:#fff; text-decoration:none; opacity:.75;
  letter-spacing:.14em; text-transform:uppercase; font-size:12px;
}
a.link:hover{opacity:1}
.table{width:100%;border-collapse:collapse;margin-top:10px}
.table th,.table td{border-bottom:1px solid rgba(255,255,255,.12);padding:10px 8px;vertical-align:top}
.table th{font-size:12px;color:var(--muted2);letter-spacing:.14em;text-transform:uppercase;font-weight:500;text-align:left}
.pill{display:inline-flex;align-items:center;gap:8px;padding:6px 10px;border:1px solid rgba(255,255,255,.16);border-radius:999px;background:rgba(0,0,0,.18);font-size:12px;color:var(--muted)}
.small{font-size:12px;color:var(--muted2);line-height:1.7}
.status{font-size:12px;color:var(--muted)}
hr.sep{border:0;border-top:1px solid rgba(255,255,255,.10);margin:14px 0}
</style></head><body>
<div class="bg" id="bg"></div>
<div class="wrap"><div class="container">
  <div class="hd">
    <div class="brand">
      <h1 style="margin:0;font-weight:250;font-size:44px;letter-spacing:.06em;text-transform:uppercase">我的年度工時</h1>
      <p style="margin:10px 0 0 0;font-size:12px;letter-spacing:.20em;text-transform:uppercase;color:rgba(255,255,255,.55)">從每日進度自動彙總（按專案）</p>
    </div>
    <div class="menu" aria-hidden="true"><span></span><span></span></div>
  </div>
  <div class="card">
    
<div class="grid">
  <div>
    <div class="label">同事（選填，不選就顯示自己/全部）</div>
    <input class="input" id="staff" placeholder="輸入姓名（可空）">
  </div>
  <div>
    <div class="label">年度</div>
    <input class="input" id="year" placeholder="2025">
  </div>
</div>
<div class="btnRow">
  <button class="btn" id="loadBtn">載入報表</button>
  <span class="status" id="status"></span>
</div>
<hr class="sep">
<div class="pill" id="summary"></div>
<table class="table" id="tbl" style="margin-top:14px">
  <thead><tr><th>專案</th><th>總時數</th></tr></thead>
  <tbody></tbody>
</table>

  </div>
</div></div>
<script>

const CFG_URL = './config.json';
async function loadCfg(){
  const r = await fetch(CFG_URL + '?v=' + Date.now(), {cache:'no-store'});
  if(!r.ok) throw new Error('config load failed');
  return await r.json();
}
function qs(key){ return new URLSearchParams(location.search).get(key) || ''; }
function setBg(url){
  if(url && String(url).trim()){
    document.getElementById('bg').style.backgroundImage = "url('" + String(url).replace(/'/g,"\\'") + "')";
  }
}
function fmtHours(x){
  const n = Number(x||0);
  if(Number.isNaN(n)) return String(x||'');
  return n % 1 === 0 ? String(n) : n.toFixed(1);
}


(async()=>{
  const cfg = await loadCfg();
  setBg(cfg.ui?.backgroundImageUrl || '');
  const now = new Date();
  year.value = String(now.getFullYear());
  staff.value = qs('staff');
  if(qs('year')) year.value = qs('year');

  async function load(){
    status.textContent='載入中…';
    try{
      const y = year.value.trim() || String(now.getFullYear());
      const s = staff.value.trim();
      if(!cfg.api?.endpointUrl) throw new Error('尚未設定報表 API（reports/config.json endpointUrl）');
      const url = cfg.api.endpointUrl + '?mode=myYear&year=' + encodeURIComponent(y) + '&staff=' + encodeURIComponent(s);
      const r = await fetch(url, {cache:'no-store'});
      const j = await r.json();
      if(j.ok === false) throw new Error(j.error || '讀取失敗');

      summary.textContent = (s? (s+'｜') : '') + y + ' 年度總工時：' + fmtHours(j.totalHours || 0) + ' 小時';
      const tb = tbl.querySelector('tbody'); tb.innerHTML='';
      (j.byProject || []).forEach(row=>{
        const tr = document.createElement('tr');
        tr.innerHTML = '<td>' + (row.project||'') + '</td><td>' + fmtHours(row.hours||0) + '</td>';
        tb.appendChild(tr);
      });
      status.textContent='✅';
    }catch(e){
      status.textContent='❌ ' + (e.message||'失敗');
    }
  }
  loadBtn.onclick = load;
  load(); // auto
})();

</script>
</body></html>
