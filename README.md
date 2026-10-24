# Büro-Management
Büro Management Web Anwendung Offline Frontend

<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Büro Management</title>
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'%3E%3Crect width='32' height='32' rx='4' fill='%232b579a'/%3E%3Ctext x='16' y='23' font-family='Segoe UI' font-size='19' font-weight='700' fill='white' text-anchor='middle'%3EB%3C/text%3E%3C/svg%3E">
<!-- Einzige externe Abhängigkeit: Tesseract.js (OCR) -->
<script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
<style>
/* ==========================================================================
   BÜRO MANAGEMENT – Office 2010 Stil
   ========================================================================== */
:root{
  --blue-dark:#1f3a5f; --blue:#2b579a; --blue-mid:#3d78c9; --blue-line:#93a9c7;
  --ink:#1c2e45; --ink-soft:#4c607a; --panel-bd:#c1cfdf;
  --hov-a:#fdf5dd; --hov-b:#fae391; --hov-bd:#e2bd5e;
  --ok:#3f9c47; --warn:#e2922c; --err:#d24b45;
  --font:"Segoe UI","Segoe UI Web (West European)",-apple-system,"Helvetica Neue",Arial,sans-serif;
}
*{box-sizing:border-box;margin:0;padding:0}
html,body{height:100%}
body{font-family:var(--font);font-size:13px;color:var(--ink);background:#cdd8e8;overflow:hidden}
button{font-family:inherit}
::-webkit-scrollbar{width:11px;height:11px}
::-webkit-scrollbar-track{background:#e8eef6}
::-webkit-scrollbar-thumb{background:#b3c3d8;border:2px solid #e8eef6;border-radius:6px}
::-webkit-scrollbar-thumb:hover{background:#93a9c7}

/* ---------- App-Rahmen ---------- */
#app{height:100vh;display:flex;flex-direction:column}

/* ---------- Titelleiste ---------- */
#titlebar{
  height:36px;display:flex;align-items:center;gap:10px;padding:0 12px;color:#fff;
  background:linear-gradient(180deg,#5a7fb5 0%,#3a639f 45%,#2b579a 55%,#234a86 100%);
  border-bottom:1px solid #1c3c6e;text-shadow:0 1px 1px rgba(0,0,0,.35);flex:none;
}
.tb-orb{width:24px;height:24px;border-radius:50%;flex:none;display:flex;align-items:center;justify-content:center;
  background:radial-gradient(circle at 32% 28%,#9ec1ea,#3a6db3 55%,#22487f);border:1px solid rgba(255,255,255,.55);
  font-weight:700;font-size:13px;color:#fff;box-shadow:0 1px 3px rgba(0,0,0,.35)}
.tb-title{font-size:13.5px;font-weight:600;letter-spacing:.2px}
.tb-title small{font-weight:400;opacity:.85;margin-left:8px;font-size:11.5px}
.tb-right{margin-left:auto;display:flex;align-items:center;gap:10px;font-size:11.5px}
.tb-badge{background:rgba(255,255,255,.16);border:1px solid rgba(255,255,255,.35);border-radius:9px;padding:2px 9px;text-shadow:none}

/* ---------- Menüband (Ribbon) ---------- */
#ribbon{flex:none;border-bottom:1px solid var(--blue-line);background:linear-gradient(180deg,#fdfeff 0%,#e6eef8 55%,#d5e2f2 100%);box-shadow:0 1px 3px rgba(31,58,95,.12)}
.tabs{display:flex;align-items:flex-end;gap:2px;padding:7px 10px 0;background:linear-gradient(180deg,#e3ecf7,#cfdef0);overflow-x:auto}
.tab{border:1px solid transparent;border-bottom:none;background:transparent;border-radius:4px 4px 0 0;
  padding:6px 15px 7px;font-size:13px;color:#204066;cursor:pointer;white-space:nowrap}
.tab:hover{background:rgba(255,255,255,.6)}
.tab.active{background:linear-gradient(#ffffff,#f0f5fc);border-color:var(--blue-line);font-weight:600;position:relative;top:1px}
.tab.t-datei{background:linear-gradient(#4c77b2,#2b579a);color:#fff;border-radius:3px 3px 0 0;margin-bottom:2px;
  border-color:#22487f;font-weight:600;text-shadow:0 1px 1px rgba(0,0,0,.3)}
.tab.t-datei:hover{background:linear-gradient(#5a86c0,#35619f)}
.rpanel{display:none;height:126px;padding:4px 8px 2px;overflow-x:auto}
.rpanel.active{display:flex;animation:fadeUp .22s ease}
.group{display:flex;flex-direction:column;flex:none;padding:2px 10px 0}
.group + .group{border-left:1px solid #c7d5e8}
.gbody{flex:1;display:flex;align-items:flex-start;gap:3px}
.glabel{font-size:11px;color:#5b7194;text-align:center;padding:4px 0 3px;border-top:1px solid transparent}
.vstack{display:flex;flex-direction:column;gap:2px;justify-content:center}
.rbtn{display:flex;flex-direction:column;align-items:center;gap:4px;padding:7px 9px 5px;border:1px solid transparent;
  border-radius:3px;background:transparent;cursor:pointer;color:#1e3350;font-size:12px;min-width:58px}
.rbtn:hover{background:linear-gradient(var(--hov-a),var(--hov-b));border-color:var(--hov-bd)}
.rbtn:active,.rbtn.on{background:linear-gradient(#fce49c,#f6c24e);border-color:#d8a32c;box-shadow:inset 0 1px 2px rgba(0,0,0,.18)}
.rbtn.small{flex-direction:row;min-width:0;gap:6px;padding:4px 8px;font-size:12px}
#ribbon.backstage .rpanel{display:none!important}

/* ---------- Arbeitsbereich ---------- */
#workspace{flex:1;overflow:auto;padding:22px 26px 30px;
  background:
   radial-gradient(rgba(43,87,154,.045) 1px,transparent 1.4px),
   radial-gradient(circle at 12% -5%,rgba(91,155,213,.16),transparent 42%),
   radial-gradient(circle at 92% 112%,rgba(255,153,0,.08),transparent 42%),
   linear-gradient(#eef3fa,#e2eaf4);
  background-size:22px 22px,auto,auto,auto;
}
.view{display:none;max-width:1240px;margin:0 auto}
.view.active{display:block;animation:fadeUp .28s ease}
@keyframes fadeUp{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:none}}
.page-head{margin-bottom:16px}
.page-head h1{font-size:27px;font-weight:300;color:#23406b;letter-spacing:.2px}
.page-head p{color:var(--ink-soft);font-size:12.5px;margin-top:2px}

/* ---------- Karten & allgemeine Bausteine ---------- */
.card{background:#fff;border:1px solid var(--panel-bd);border-radius:4px;box-shadow:0 1px 3px rgba(31,58,95,.09)}
.card-h{padding:9px 14px;border-bottom:1px solid #dbe4ef;background:linear-gradient(#fdfeff,#eef3fa);
  font-weight:600;font-size:13px;color:#28425f;border-radius:4px 4px 0 0;display:flex;align-items:center;gap:8px}
.card-b{padding:14px}
.btn{font-size:13px;padding:6px 16px;border-radius:3px;border:1px solid #8ba3c0;
  background:linear-gradient(#fdfefe,#dfe8f3);color:var(--ink);cursor:pointer}
.btn:hover{background:linear-gradient(var(--hov-a),var(--hov-b));border-color:var(--hov-bd)}
.btn:active{background:linear-gradient(#f9df9e,#f3c65d)}
.btn.primary{background:linear-gradient(#5b8fc9,#2f66ad);border-color:#27568f;color:#fff;text-shadow:0 1px 1px rgba(0,0,0,.25)}
.btn.primary:hover{background:linear-gradient(#6d9dd4,#3a74bd)}
.btn.green{background:linear-gradient(#63b35b,#3f8f3d);border-color:#35762f;color:#fff;text-shadow:0 1px 1px rgba(0,0,0,.2)}
.btn.green:hover{background:linear-gradient(#74c16c,#4a9d47)}
.btn:disabled{opacity:.55;cursor:default}
input[type=text],input[type=number],input[type=date],input[type=time],input[type=email],select,textarea{
  font-family:inherit;font-size:13px;padding:6px 8px;border:1px solid #a9bad0;border-radius:2px;background:#fff;color:var(--ink)}
input:focus,select:focus,textarea:focus{border-color:#4f83c4;box-shadow:0 0 0 2px rgba(61,120,201,.22);outline:none}
.ibtn{display:inline-flex;align-items:center;justify-content:center;width:26px;height:26px;border:1px solid transparent;
  background:transparent;border-radius:3px;cursor:pointer}
.ibtn:hover{background:linear-gradient(var(--hov-a),var(--hov-b));border-color:var(--hov-bd)}
.badge{display:inline-block;padding:2px 9px;border-radius:9px;font-size:11px;font-weight:600;border:1px solid}
.b-green{background:#dff3dd;border-color:#7cc57a;color:#1e6b1e}
.b-orange{background:#fdeeda;border-color:#e2a23b;color:#8a5a00}
.b-red{background:#fde3e1;border-color:#e07a72;color:#8a1f1a}
.b-blue{background:#e0ecfa;border-color:#7fa8d9;color:#1a4e8a}
.b-grey{background:#eef1f5;border-color:#b9c4d2;color:#4c5b6e}

/* ---------- Tabellen ---------- */
.tbl-wrap{overflow-x:auto}
.tbl{width:100%;border-collapse:collapse;font-size:13px;background:#fff}
.tbl th{background:linear-gradient(#fdfeff,#e2ebf6);border:1px solid #c8d5e6;padding:7px 9px;text-align:left;
  font-weight:600;color:#28425f;white-space:nowrap}
.tbl td{border:1px solid #dbe4ef;padding:6px 9px;vertical-align:middle}
.tbl tbody tr:nth-child(even) td{background:#f5f8fc}
.tbl tbody tr:hover td{background:#e8f1fb}

/* ---------- Statusleiste ---------- */
#statusbar{height:27px;flex:none;display:flex;align-items:center;gap:14px;padding:0 12px;color:#fff;font-size:12px;
  background:linear-gradient(#4a79b0,#2d5688);border-top:1px solid #1c3c6e;text-shadow:0 1px 1px rgba(0,0,0,.25)}
#statusbar .spacer{flex:1}
.sb-seg{opacity:.95;white-space:nowrap}
#sb-db::before{content:"●";margin-right:5px;color:#9fe39a}

/* ---------- Startseite (Backstage) ---------- */
.home-band{background:linear-gradient(120deg,#24487f,#2b579a 55%,#3a6db3);color:#fff;border-radius:6px;
  padding:26px 30px;margin-bottom:20px;position:relative;overflow:hidden;box-shadow:0 2px 8px rgba(31,58,95,.3)}
.home-band::after{content:"";position:absolute;right:-40px;top:-60px;width:260px;height:260px;border-radius:50%;
  background:radial-gradient(circle,rgba(255,255,255,.18),transparent 70%)}
.home-band h1{font-size:30px;font-weight:300;letter-spacing:.4px}
.home-band p{opacity:.9;margin-top:5px;font-size:13.5px}
.tile-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(248px,1fr));gap:14px}
.tile{background:linear-gradient(#ffffff,#f2f7fc);border:1px solid #b9c9dd;border-radius:5px;padding:16px 16px 14px;
  cursor:pointer;position:relative;overflow:hidden;transition:transform .16s ease,box-shadow .16s ease,border-color .16s ease}
.tile::before{content:"";position:absolute;left:0;top:0;bottom:0;width:4px;background:var(--tc,#2b579a)}
.tile:hover{transform:translateY(-3px);border-color:#6f96c8;box-shadow:0 6px 16px rgba(31,58,95,.18)}
.tile .t-ic{margin-bottom:8px}
.tile h3{font-size:16.5px;font-weight:600;color:#23406b}
.tile p{font-size:12px;color:var(--ink-soft);margin:4px 0 10px;min-height:30px}
.tile .t-stat{font-size:11.5px;font-weight:600;color:#1a4e8a;background:#e0ecfa;border:1px solid #b9d0ec;
  border-radius:9px;padding:2px 9px;display:inline-block}

/* ---------- Dashboard ---------- */
.stat-row{display:grid;grid-template-columns:repeat(auto-fit,minmax(168px,1fr));gap:12px;margin-bottom:18px}
.stat{background:#fff;border:1px solid var(--panel-bd);border-left:4px solid var(--sc,#2b579a);border-radius:4px;
  padding:12px 14px;box-shadow:0 1px 3px rgba(31,58,95,.08);transition:transform .15s}
.stat:hover{transform:translateY(-2px)}
.stat .num{font-size:31px;font-weight:300;color:#23406b;line-height:1.05}
.stat .lbl{font-size:11px;text-transform:uppercase;letter-spacing:.7px;color:var(--ink-soft);margin-top:3px}
.dash-grid{display:grid;grid-template-columns:1.25fr 1fr;gap:14px}
.mini-list{list-style:none}
.mini-list li{display:flex;align-items:center;gap:9px;padding:8px 6px;border-bottom:1px solid #e6edf5;cursor:pointer;border-radius:3px}
.mini-list li:hover{background:#eef5fd}
.mini-list li:last-child{border-bottom:none}
.mini-list .ml-main{flex:1;min-width:0}
.mini-list .ml-main b{display:block;font-size:13px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.mini-list .ml-main small{color:var(--ink-soft);font-size:11.5px}
.dot{width:9px;height:9px;border-radius:50%;flex:none}

/* ---------- Dokumente ---------- */
.dropzone{border:2px dashed #9db8d8;border-radius:6px;background:#f6faff;padding:26px;text-align:center;
  color:var(--ink-soft);cursor:pointer;transition:all .18s ease;margin-bottom:16px}
.dropzone:hover{border-color:#6f96c8;background:#f0f7ff}
.dropzone.drag{border-color:var(--blue);background:#e3f0ff;transform:scale(1.008)}
.dropzone b{color:#23406b}
.toolbar{display:flex;flex-wrap:wrap;gap:10px;align-items:center;margin-bottom:12px}
.toolbar input[type=text]{min-width:230px}

/* ---------- OCR ---------- */
.ocr-grid{display:grid;grid-template-columns:1fr 1.15fr;gap:14px}
.ocr-imgbox{min-height:210px;display:flex;align-items:center;justify-content:center;background:
  repeating-conic-gradient(#f2f6fb 0% 25%,#fff 0% 50%) 0 0/22px 22px;border:1px solid #dbe4ef;border-radius:4px;overflow:auto;padding:8px}
.ocr-imgbox img{max-width:100%;max-height:340px;box-shadow:0 2px 8px rgba(0,0,0,.2)}
.progress{height:16px;background:#dfe8f3;border:1px solid #b9c9dd;border-radius:3px;overflow:hidden}
.progress .fill{height:100%;width:0;background:linear-gradient(#6fb1e8,#2b6cb0);position:relative;transition:width .2s}
.progress .fill::after{content:"";position:absolute;inset:0;background:repeating-linear-gradient(45deg,rgba(255,255,255,.25) 0 8px,transparent 8px 16px);animation:stripes 1s linear infinite}
@keyframes stripes{to{background-position:22px 0}}
#ocr-result{width:100%;min-height:230px;resize:vertical;font-family:Consolas,monospace;font-size:13px}

/* ---------- Rechnungsformular ---------- */
.fgrid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;margin-bottom:12px}
.fgrid label,.flabel{display:block;font-size:11.5px;color:var(--ink-soft);margin-bottom:3px;font-weight:600}
.fgrid input,.fgrid select,.fw input,.fw select,.fw textarea{width:100%}
.fw{margin-bottom:10px}
.inv-row{display:grid;grid-template-columns:1fr 76px 104px 84px 96px 30px;gap:6px;align-items:center;margin-bottom:6px}
.inv-row .i-sum{text-align:right;font-weight:600;white-space:nowrap}
.inv-head{display:grid;grid-template-columns:1fr 76px 104px 84px 96px 30px;gap:6px;font-size:11px;font-weight:700;
  color:var(--ink-soft);text-transform:uppercase;letter-spacing:.4px;border-bottom:2px solid #c8d5e6;padding-bottom:5px;margin-bottom:8px}
.inv-totals{margin-top:12px;margin-left:auto;width:330px;font-size:13px}
.inv-totals div{display:flex;justify-content:space-between;padding:4px 6px;border-bottom:1px solid #e6edf5}
.inv-totals .grand{font-size:15px;font-weight:700;color:#23406b;border-bottom:none;background:#eef4fb;border-radius:3px;margin-top:4px}

/* ---------- Tools ---------- */
.tool-nav{display:flex;gap:4px;border-bottom:1px solid var(--blue-line);margin-bottom:16px;flex-wrap:wrap}
.tool-nav button{border:1px solid transparent;border-bottom:none;background:transparent;padding:8px 16px;cursor:pointer;
  font-size:13px;color:#204066;border-radius:4px 4px 0 0}
.tool-nav button:hover{background:rgba(255,255,255,.65)}
.tool-nav button.on{background:#fff;border-color:var(--blue-line);font-weight:600;position:relative;top:1px}
.tool-panel{display:none}
.tool-panel.active{display:block;animation:fadeUp .22s ease}
.tools-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(330px,1fr));gap:14px;align-items:start}
.calc{width:298px;background:#fff;border:1px solid var(--panel-bd);border-radius:6px;padding:12px;box-shadow:0 2px 8px rgba(31,58,95,.12)}
#calc-display{width:100%;text-align:right;font-size:23px;padding:9px 10px;margin-bottom:10px;background:#f4f9ee;
  border:1px solid #b9c9a2;font-family:Consolas,monospace;border-radius:3px}
.keypad{display:grid;grid-template-columns:repeat(4,1fr);gap:6px}
.keypad button{padding:12px 0;font-size:15px;border:1px solid #b6c3d6;border-radius:3px;cursor:pointer;
  background:linear-gradient(#fdfeff,#e8eef7)}
.keypad button:hover{background:linear-gradient(var(--hov-a),var(--hov-b));border-color:var(--hov-bd)}
.keypad button:active{background:linear-gradient(#f9df9e,#f3c65d)}
.keypad .k-op{background:linear-gradient(#eaf1fa,#d3e1f2);font-weight:700;color:#2b579a}
.keypad .k-eq{background:linear-gradient(#5b8fc9,#2f66ad);color:#fff;border-color:#27568f;font-weight:700}
.keypad .k-eq:hover{background:linear-gradient(#6d9dd4,#3a74bd)}
.task-row{display:flex;align-items:center;gap:9px;padding:7px 6px;border-bottom:1px solid #e6edf5}
.task-row:hover{background:#f2f7fd}
.task-row.done .t-title{text-decoration:line-through;color:#8796a8}
.task-row.overdue .t-due{color:var(--err);font-weight:700}
.note-item{padding:8px 10px;border-bottom:1px solid #e6edf5;cursor:pointer;border-radius:3px}
.note-item:hover{background:#eef5fd}
.note-item.sel{background:#e0ecfa;outline:1px solid #9dbde2}
.note-item b{display:block;font-size:13px}
.note-item small{color:var(--ink-soft)}

/* ---------- Kalender & Wiedervorlagen ---------- */
.term-grid{display:grid;grid-template-columns:1.35fr 1fr;gap:14px;align-items:start}
.cal-head{display:grid;grid-template-columns:repeat(7,1fr);gap:3px;margin-bottom:3px}
.cal-head div{text-align:center;font-size:11px;font-weight:700;color:var(--ink-soft);text-transform:uppercase}
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr);gap:3px}
.cal-cell{min-height:74px;background:#fbfdff;border:1px solid #dbe4ef;border-radius:3px;padding:4px 5px;cursor:pointer;
  overflow:hidden;transition:background .15s}
.cal-cell:hover{background:#eef5fd;border-color:#9dbde2}
.cal-cell.other{background:#f2f5f9;color:#9aa8b8}
.cal-cell.today{border-color:var(--blue);box-shadow:inset 0 0 0 1px var(--blue)}
.cal-cell.sel{background:#e0ecfa;border-color:#5b8fc9}
.cal-num{font-size:12px;font-weight:600;display:block;margin-bottom:2px}
.cal-cell.today .cal-num{color:#fff;background:var(--blue);display:inline-block;padding:0 6px;border-radius:8px}
.cal-chip{display:block;background:#dcebfd;border:1px solid #a9c8ec;color:#1a4e8a;font-size:10.5px;border-radius:2px;
  padding:1px 4px;margin-bottom:2px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.cal-more{font-size:10px;color:var(--ink-soft)}
.cal-bar{display:flex;align-items:center;gap:8px;margin-bottom:10px}
.cal-bar h2{font-size:19px;font-weight:300;color:#23406b;flex:1}

/* ---------- Leere Zustände ---------- */
.empty{text-align:center;padding:38px 20px;color:var(--ink-soft)}
.empty .e-ic{opacity:.5;margin-bottom:10px}
.empty b{display:block;font-size:15px;color:#41598a;margin-bottom:4px}

/* ---------- Modals ---------- */
.m-ov{position:fixed;inset:0;background:rgba(20,36,61,.46);display:flex;align-items:center;justify-content:center;
  z-index:1000;opacity:0;transition:opacity .18s ease;padding:18px}
.m-ov.in{opacity:1}
.m-box{background:#fff;border:1px solid #6f8bb0;border-radius:5px;box-shadow:0 10px 34px rgba(10,25,45,.4);
  width:520px;max-width:100%;max-height:92vh;display:flex;flex-direction:column;transform:translateY(10px);transition:transform .18s ease}
.m-ov.in .m-box{transform:none}
.m-wide{width:880px}
.m-title{background:linear-gradient(#fdfeff,#dfe9f6);border-bottom:1px solid #c3d1e2;padding:9px 14px;
  font-weight:600;font-size:14px;color:#23406b;display:flex;align-items:center;border-radius:5px 5px 0 0}
.m-x{margin-left:auto;border:none;background:transparent;font-size:15px;cursor:pointer;color:#5b7194;
  width:26px;height:26px;border-radius:3px}
.m-x:hover{background:linear-gradient(#fde0dd,#f6b1ab);color:#8a1f1a}
.m-body{padding:16px;overflow:auto}
.m-foot{padding:11px 14px;border-top:1px solid #dbe4ef;background:#f6f9fc;display:flex;justify-content:flex-end;gap:8px;border-radius:0 0 5px 5px}
.pick-list{display:flex;flex-direction:column;gap:6px}
.pick-item{display:flex;align-items:center;gap:8px;text-align:left;padding:9px 12px;border:1px solid #c8d5e6;
  border-radius:3px;background:#fff;cursor:pointer;font-size:13px}
.pick-item:hover{background:linear-gradient(var(--hov-a),var(--hov-b));border-color:var(--hov-bd)}
.pick-item small{margin-left:auto;color:var(--ink-soft)}

/* ---------- Toasts ---------- */
#toasts{position:fixed;right:18px;bottom:38px;z-index:2000;display:flex;flex-direction:column;gap:8px}
.toast{background:#fff;border:1px solid #b9c9dd;border-left:4px solid var(--blue-mid);border-radius:3px;
  box-shadow:0 4px 14px rgba(10,25,45,.25);padding:10px 16px;font-size:13px;min-width:230px;max-width:340px;
  animation:toastIn .25s ease}
.toast.t-ok{border-left-color:var(--ok)}
.toast.t-err{border-left-color:var(--err)}
.toast.out{opacity:0;transform:translateX(20px);transition:all .35s ease}
@keyframes toastIn{from{opacity:0;transform:translateX(30px)}to{opacity:1;transform:none}}

/* ---------- Druckansicht ---------- */
#print-area{display:none}
@media print{
  body{background:#fff;overflow:visible}
  body > *:not(#print-area){display:none!important}
  #print-area{display:block;font-family:var(--font);color:#000;padding:6mm}
  @page{margin:18mm}
}
.p-head{display:flex;justify-content:space-between;border-bottom:2.5px solid #2b579a;padding-bottom:10px;margin-bottom:22px}
.p-head h1{font-size:21px;color:#2b579a;font-weight:600}
.p-head p{font-size:11px;color:#444;margin-top:3px}
.p-meta{text-align:right;font-size:12px;line-height:1.55}
.p-meta b{font-size:16px;color:#2b579a}
.p-to{margin:0 0 20px;font-size:13px;line-height:1.5}
.p-tbl{width:100%;border-collapse:collapse;font-size:12px;margin-bottom:14px}
.p-tbl th{background:#eef3fa;border-bottom:2px solid #2b579a;padding:6px 8px;text-align:left}
.p-tbl td{border-bottom:1px solid #ccd6e4;padding:6px 8px}
.p-tbl .r{text-align:right;white-space:nowrap}
.p-totals{margin-left:auto;width:300px;font-size:12.5px}
.p-totals div{display:flex;justify-content:space-between;padding:3px 6px}
.p-totals .grand{font-weight:700;font-size:14px;border-top:2px solid #2b579a;margin-top:4px;padding-top:6px}
.p-foot{margin-top:34px;padding-top:10px;border-top:1px solid #bbb;font-size:10.5px;color:#444;line-height:1.6}

/* ---------- Fataler Fehler ---------- */
#fatal{position:fixed;inset:0;background:#eef3fa;z-index:3000;display:none;align-items:center;justify-content:center;padding:20px}
#fatal .f-box{max-width:480px;background:#fff;border:1px solid #e07a72;border-top:5px solid var(--err);
  border-radius:5px;padding:26px;box-shadow:0 8px 30px rgba(0,0,0,.2)}

/* ---------- Responsive ---------- */
@media (max-width:980px){
  .dash-grid,.ocr-grid,.term-grid{grid-template-columns:1fr}
  .fgrid{grid-template-columns:repeat(2,1fr)}
  #workspace{padding:16px 12px 24px}
}
@media (max-width:640px){
  .tb-title small{display:none}
  .inv-row,.inv-head{grid-template-columns:1fr 62px 80px 70px 78px 26px;font-size:12px}
  .inv-row input,.inv-row select{padding:4px}
  .inv-totals{width:100%}
  .cal-cell{min-height:56px}
  #statusbar{font-size:11px;gap:8px}
  #sb-view{display:none}
}
</style>
</head>
<body>
<noscript><div style="padding:30px;font-family:Segoe UI,Arial">Bitte aktivieren Sie JavaScript, um „Büro Management" zu verwenden.</div></noscript>

<div id="app">

  <!-- ======================= Titelleiste ======================= -->
  <div id="titlebar">
    <div class="tb-orb">B</div>
    <div class="tb-title">Büro Management<small>Lokale Arbeitsumgebung · Daten verbleiben im Browser</small></div>
    <div class="tb-right">
      <span id="tb-date" class="tb-badge"></span>
      <span class="tb-badge">Office-Stil 2010</span>
    </div>
  </div>

  <!-- ======================= Menüband ======================= -->
  <div id="ribbon">
    <div class="tabs" id="tabbar">
      <button class="tab t-datei" data-tab="datei">Datei</button>
      <button class="tab" data-tab="dashboard">Dashboard</button>
      <button class="tab" data-tab="docs">Dokumente</button>
      <button class="tab" data-tab="ocr">Texterkennung</button>
      <button class="tab" data-tab="inv">Rechnungen</button>
      <button class="tab" data-tab="contacts">Kontakte</button>
      <button class="tab" data-tab="tools">Tools</button>
      <button class="tab" data-tab="terms">Termine</button>
    </div>

    <div class="rpanel" data-panel="dashboard">
      <div class="group"><div class="gbody">
        <button class="rbtn" data-act="refresh-all"><i class="ic" data-ic="refresh" data-size="30"></i>Aktualisieren</button>
      </div><div class="glabel">Ansicht</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="new-doc"><i class="ic" data-ic="upload" data-size="17"></i>Neues Dokument</button>
        <button class="rbtn small" data-act="new-invoice"><i class="ic" data-ic="inv" data-size="17"></i>Neue Rechnung</button>
      </div><div class="glabel">Schnellzugriff</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="new-contact"><i class="ic" data-ic="contacts" data-size="17"></i>Neuer Kontakt</button>
        <button class="rbtn small" data-act="new-task"><i class="ic" data-ic="task" data-size="17"></i>Neue Aufgabe</button>
      </div><div class="glabel">Neu</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="open-settings"><i class="ic" data-ic="gear" data-size="17"></i>Einstellungen</button>
        <button class="rbtn small" data-act="export-backup"><i class="ic" data-ic="export" data-size="17"></i>Datenexport</button>
      </div><div class="glabel">Verwaltung</div></div>
    </div>

    <div class="rpanel" data-panel="docs">
      <div class="group"><div class="gbody">
        <button class="rbtn" data-act="doc-upload"><i class="ic" data-ic="upload" data-size="30"></i>Hochladen</button>
      </div><div class="glabel">Dateien</div></div>
      <div class="group"><div class="gbody vstack">
        <div style="display:flex;gap:2px">
          <button class="rbtn small on" data-docfilter="all">Alle</button>
          <button class="rbtn small" data-docfilter="bild">Bilder</button>
          <button class="rbtn small" data-docfilter="pdf">PDF</button>
        </div>
        <div style="display:flex;gap:2px">
          <button class="rbtn small" data-docfilter="text">Text</button>
          <button class="rbtn small" data-docfilter="sonst">Sonstige</button>
        </div>
      </div><div class="glabel">Filter</div></div>
      <div class="group"><div class="gbody">
        <button class="rbtn" data-act="render-docs"><i class="ic" data-ic="refresh" data-size="30"></i>Aktualisieren</button>
      </div><div class="glabel">Ansicht</div></div>
    </div>

    <div class="rpanel" data-panel="ocr">
      <div class="group"><div class="gbody vstack">
        <button class="rbtn" data-act="ocr-pick"><i class="ic" data-ic="preview" data-size="30"></i>Bild öffnen</button>
      </div><div class="glabel">Quelle</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="ocr-from-docs"><i class="ic" data-ic="docs" data-size="17"></i>Aus Dokumenten…</button>
        <button class="rbtn small" data-act="ocr-reset"><i class="ic" data-ic="refresh" data-size="17"></i>Zurücksetzen</button>
      </div><div class="glabel">Import</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small on" data-ocrlang="deu">Deutsch</button>
        <button class="rbtn small" data-ocrlang="eng">Englisch</button>
      </div><div class="glabel">Sprache</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn" data-act="ocr-run"><i class="ic" data-ic="ocr" data-size="30"></i>Erkennen</button>
      </div><div class="glabel">Erkennung</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="ocr-copy"><i class="ic" data-ic="copy" data-size="17"></i>Kopieren</button>
      </div><div class="glabel">Ergebnis</div></div>
    </div>

    <div class="rpanel" data-panel="inv">
      <div class="group"><div class="gbody">
        <button class="rbtn" data-act="new-invoice"><i class="ic" data-ic="plus" data-size="30"></i>Neue Rechnung</button>
      </div><div class="glabel">Rechnung</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="inv-csv"><i class="ic" data-ic="export" data-size="17"></i>CSV-Export</button>
        <button class="rbtn small" data-act="render-inv"><i class="ic" data-ic="refresh" data-size="17"></i>Aktualisieren</button>
      </div><div class="glabel">Verwalten</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="open-settings"><i class="ic" data-ic="gear" data-size="17"></i>Firmendaten</button>
      </div><div class="glabel">Einstellungen</div></div>
    </div>

    <div class="rpanel" data-panel="contacts">
      <div class="group"><div class="gbody">
        <button class="rbtn" data-act="new-contact"><i class="ic" data-ic="plus" data-size="30"></i>Neuer Kontakt</button>
      </div><div class="glabel">Kontakt</div></div>
      <div class="group"><div class="gbody vstack">
        <div style="display:flex;gap:2px">
          <button class="rbtn small on" data-cfilter="alle">Alle</button>
          <button class="rbtn small" data-cfilter="Kunde">Kunden</button>
        </div>
        <button class="rbtn small" data-cfilter="Lieferant">Lieferanten</button>
      </div><div class="glabel">Ansicht</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="render-contacts"><i class="ic" data-ic="refresh" data-size="17"></i>Aktualisieren</button>
      </div><div class="glabel">Daten</div></div>
    </div>

    <div class="rpanel" data-panel="tools">
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-tool="calc"><i class="ic" data-ic="tools" data-size="17"></i>Taschenrechner</button>
        <button class="rbtn small" data-tool="conv"><i class="ic" data-ic="conv" data-size="17"></i>Umrechner</button>
      </div><div class="glabel">Rechner</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-tool="vat"><i class="ic" data-ic="percent" data-size="17"></i>MwSt &amp; Prozent</button>
      </div><div class="glabel">Finanzen</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-tool="notes"><i class="ic" data-ic="note" data-size="17"></i>Notizen</button>
        <button class="rbtn small" data-tool="tasks"><i class="ic" data-ic="task" data-size="17"></i>Aufgaben</button>
      </div><div class="glabel">Organisation</div></div>
    </div>

    <div class="rpanel" data-panel="terms">
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="cal-today"><i class="ic" data-ic="calendar" data-size="17"></i>Heute</button>
        <button class="rbtn small" data-act="new-event"><i class="ic" data-ic="plus" data-size="17"></i>Neuer Termin</button>
      </div><div class="glabel">Kalender</div></div>
      <div class="group"><div class="gbody vstack">
        <button class="rbtn small" data-act="new-reminder"><i class="ic" data-ic="clock" data-size="17"></i>Neue Wiedervorlage</button>
        <button class="rbtn small" data-act="render-terms"><i class="ic" data-ic="refresh" data-size="17"></i>Aktualisieren</button>
      </div><div class="glabel">Wiedervorlagen</div></div>
    </div>
  </div>

  <!-- ======================= Arbeitsbereich ======================= -->
  <main id="workspace">

    <!-- ===== Startseite (Backstage) ===== -->
    <section class="view" data-view="datei">
      <div class="home-band">
        <h1>Büro Management</h1>
        <p>Ihre lokale Schaltzentrale: Dokumente, Rechnungen, Kontakte, Texterkennung und Organisation – alles an einem Ort, alles auf Ihrem Rechner.</p>
      </div>
      <div class="tile-grid">
        <div class="tile" style="--tc:#3d78c9" data-goto="dashboard">
          <div class="t-ic"><i class="ic" data-ic="dashboard" data-size="40"></i></div>
          <h3>Dashboard</h3><p>Schnellzugriff, Statistiken und zuletzt geöffnete Dokumente.</p>
          <span class="t-stat" id="hm-dash">Übersicht öffnen</span>
        </div>
        <div class="tile" style="--tc:#e8b64c" data-goto="docs">
          <div class="t-ic"><i class="ic" data-ic="docs" data-size="40"></i></div>
          <h3>Dokumente</h3><p>Upload per Knopfdruck oder Drag &amp; Drop, Vorschau, Download.</p>
          <span class="t-stat" id="hm-docs">0 Dateien</span>
        </div>
        <div class="tile" style="--tc:#2b6cb0" data-goto="ocr">
          <div class="t-ic"><i class="ic" data-ic="ocr" data-size="40"></i></div>
          <h3>Texterkennung (OCR)</h3><p>Text aus Bildern und Scans erkennen und in die Zwischenablage übernehmen.</p>
          <span class="t-stat" id="hm-ocr">Tesseract.js</span>
        </div>
        <div class="tile" style="--tc:#3f9c47" data-goto="inv">
          <div class="t-ic"><i class="ic" data-ic="inv" data-size="40"></i></div>
          <h3>Rechnungen</h3><p>Fortlaufende Nummern, Positionen, MwSt-Berechnung und Druckansicht.</p>
          <span class="t-stat" id="hm-inv">0 Rechnungen</span>
        </div>
        <div class="tile" style="--tc:#7c5cc4" data-goto="contacts">
          <div class="t-ic"><i class="ic" data-ic="contacts" data-size="40"></i></div>
          <h3>Kontakte &amp; Lieferanten</h3><p>Kunden und Lieferanten anlegen, bearbeiten und durchsuchen.</p>
          <span class="t-stat" id="hm-contacts">0 Kontakte</span>
        </div>
        <div class="tile" style="--tc:#e2922c" data-goto="tools">
          <div class="t-ic"><i class="ic" data-ic="tools" data-size="40"></i></div>
          <h3>Kalkulation &amp; Tools</h3><p>Taschenrechner, Umrechner, MwSt-Rechner, Notizen und Aufgaben.</p>
          <span class="t-stat" id="hm-tools">5 Werkzeuge</span>
        </div>
        <div class="tile" style="--tc:#d24b45" data-goto="terms">
          <div class="t-ic"><i class="ic" data-ic="calendar" data-size="40"></i></div>
          <h3>Termine &amp; Wiedervorlagen</h3><p>Monatskalender mit Terminen und fristbezogene Wiedervorlagen.</p>
          <span class="t-stat" id="hm-terms">0 Termine</span>
        </div>
        <div class="tile" style="--tc:#5a708e" data-act-tile="open-settings">
          <div class="t-ic"><i class="ic" data-ic="gear" data-size="40"></i></div>
          <h3>Einstellungen</h3><p>Firmendaten, Rechnungsnummern-Kreis und Voreinstellungen.</p>
          <span class="t-stat">Konfiguration</span>
        </div>
        <div class="tile" style="--tc:#0f7b8b" data-act-tile="backup">
          <div class="t-ic"><i class="ic" data-ic="export" data-size="40"></i></div>
          <h3>Datensicherung</h3><p>Alle Daten als Backup-Datei exportieren oder aus einem Backup importieren.</p>
          <span class="t-stat">Export / Import</span>
        </div>
      </div>
    </section>

    <!-- ===== Dashboard ===== -->
    <section class="view" data-view="dashboard">
      <div class="page-head"><h1>Dashboard</h1><p id="dash-sub">Willkommen zurück – hier ist Ihr Überblick.</p></div>
      <div class="stat-row">
        <div class="stat" style="--sc:#3d78c9"><div class="num" id="st-docs">0</div><div class="lbl">Dokumente</div></div>
        <div class="stat" style="--sc:#3f9c47"><div class="num" id="st-invoices">0</div><div class="lbl">Rechnungen gesamt</div></div>
        <div class="stat" style="--sc:#e2922c"><div class="num" id="st-open">0,00 €</div><div class="lbl">Offene Forderungen</div></div>
        <div class="stat" style="--sc:#7c5cc4"><div class="num" id="st-contacts">0</div><div class="lbl">Kontakte</div></div>
        <div class="stat" style="--sc:#d24b45"><div class="num" id="st-tasks">0</div><div class="lbl">Offene Aufgaben</div></div>
        <div class="stat" style="--sc:#0f7b8b"><div class="num" id="st-today">0</div><div class="lbl">Heute fällig / Termine</div></div>
      </div>
      <div class="dash-grid">
        <div class="card">
          <div class="card-h"><i class="ic" data-ic="docs" data-size="17"></i>Zuletzt geöffnete Dokumente</div>
          <div class="card-b"><ul class="mini-list" id="dash-recent"></ul></div>
        </div>
        <div style="display:flex;flex-direction:column;gap:14px">
          <div class="card">
            <div class="card-h"><i class="ic" data-ic="clock" data-size="17"></i>Heute fällig</div>
            <div class="card-b"><ul class="mini-list" id="dash-due"></ul></div>
          </div>
          <div class="card">
            <div class="card-h"><i class="ic" data-ic="inv" data-size="17"></i>Offene Rechnungen</div>
            <div class="card-b"><ul class="mini-list" id="dash-openinv"></ul></div>
          </div>
        </div>
      </div>
    </section>

    <!-- ===== Dokumente ===== -->
    <section class="view" data-view="docs">
      <div class="page-head"><h1>Dokumentenverwaltung</h1><p>Dateien lokal ablegen, durchsuchen, prüfen und wieder herunterladen.</p></div>
      <div class="dropzone" id="dropzone">
        <div style="margin-bottom:6px"><i class="ic" data-ic="upload" data-size="34"></i></div>
        <b>Dateien hier ablegen</b> – oder klicken, um Dateien auszuwählen.<br>
        <small>Mehrfachauswahl möglich · Bilder, PDF, Texte u.&nbsp;v.&nbsp;m.</small>
      </div>
      <div class="toolbar">
        <input type="text" id="doc-search" placeholder="Suche nach Dateiname…">
        <span id="doc-count" style="color:var(--ink-soft)"></span>
      </div>
      <div class="card"><div class="tbl-wrap">
        <table class="tbl">
          <thead><tr><th style="width:38%">Name</th><th>Typ</th><th>Größe</th><th>Hinzugefügt</th><th style="width:118px">Aktionen</th></tr></thead>
          <tbody id="doc-list-body"></tbody>
        </table>
      </div></div>
      <div class="empty" id="doc-empty" hidden>
        <div class="e-ic"><i class="ic" data-ic="docs" data-size="58"></i></div>
        <b>Noch keine Dokumente vorhanden</b>
        Laden Sie über die Fläche oben Ihre ersten Dateien hoch.
      </div>
    </section>

    <!-- ===== OCR ===== -->
    <section class="view" data-view="ocr">
      <div class="page-head"><h1>Texterkennung (OCR)</h1><p>Erkennt Text in Bildern und Scans – direkt im Browser, dank Tesseract.js.</p></div>
      <div class="toolbar"><span class="badge b-blue" id="ocr-avail">OCR-Modul wird geprüft…</span></div>
      <div class="ocr-grid">
        <div class="card">
          <div class="card-h"><i class="ic" data-ic="preview" data-size="17"></i>Quelle</div>
          <div class="card-b">
            <div class="ocr-imgbox" id="ocr-img"><span style="color:var(--ink-soft)">Noch kein Bild geladen</span></div>
            <p id="ocr-fname" style="margin-top:9px;color:var(--ink-soft);font-size:12px"></p>
            <div style="margin-top:10px" hidden id="ocr-progress">
              <div style="display:flex;justify-content:space-between;font-size:12px;margin-bottom:4px">
                <span id="ocr-label">Bereit</span><span id="ocr-pct"></span>
              </div>
              <div class="progress"><div class="fill" id="ocr-fill"></div></div>
            </div>
          </div>
        </div>
        <div class="card">
          <div class="card-h"><i class="ic" data-ic="ocr" data-size="17"></i>Erkannter Text
            <span style="margin-left:auto;font-weight:400;font-size:11.5px;color:var(--ink-soft)" id="ocr-count">0 Zeichen</span>
          </div>
          <div class="card-b">
            <textarea id="ocr-result" placeholder="Der erkannte Text erscheint hier. Sie können ihn direkt bearbeiten."></textarea>
            <div style="margin-top:10px;display:flex;gap:8px">
              <button class="btn primary" data-act="ocr-copy"><i class="ic" data-ic="copy" data-size="15" style="vertical-align:-2px"></i> In Zwischenablage kopieren</button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ===== Rechnungen ===== -->
    <section class="view" data-view="inv">
      <div class="page-head"><h1>Rechnungen</h1><p>Rechnungen erstellen, nummernieren, berechnen und drucken.</p></div>
      <div class="toolbar">
        <input type="text" id="inv-search" placeholder="Suche: Nummer oder Empfänger…">
        <select id="inv-status">
          <option value="alle">Alle Status</option>
          <option value="offen">Offen</option>
          <option value="überfällig">Überfällig</option>
          <option value="bezahlt">Bezahlt</option>
        </select>
        <span id="inv-count" style="color:var(--ink-soft)"></span>
        <span style="flex:1"></span>
        <button class="btn primary" data-act="new-invoice">+ Neue Rechnung</button>
      </div>
      <div class="card"><div class="tbl-wrap">
        <table class="tbl">
          <thead><tr><th>Nummer</th><th>Datum</th><th>Empfänger</th><th>Fällig</th><th style="text-align:right">Betrag</th><th>Status</th><th style="width:140px">Aktionen</th></tr></thead>
          <tbody id="inv-list-body"></tbody>
        </table>
      </div></div>
      <div class="empty" id="inv-empty" hidden>
        <div class="e-ic"><i class="ic" data-ic="inv" data-size="58"></i></div>
        <b>Noch keine Rechnungen vorhanden</b>
        Erstellen Sie Ihre erste Rechnung über „Neue Rechnung".
      </div>
    </section>

    <!-- ===== Kontakte ===== -->
    <section class="view" data-view="contacts">
      <div class="page-head"><h1>Kontakte &amp; Lieferanten</h1><p>Adressen pflegen und für Rechnungen wiederverwenden.</p></div>
      <div class="toolbar">
        <input type="text" id="c-search" placeholder="Suche: Name, Firma, E-Mail, Telefon…">
        <span id="c-count" style="color:var(--ink-soft)"></span>
        <span style="flex:1"></span>
        <button class="btn primary" data-act="new-contact">+ Neuer Kontakt</button>
      </div>
      <div class="card"><div class="tbl-wrap">
        <table class="tbl">
          <thead><tr><th>Typ</th><th>Name</th><th>Firma</th><th>E-Mail</th><th>Telefon</th><th style="width:96px">Aktionen</th></tr></thead>
          <tbody id="c-list-body"></tbody>
        </table>
      </div></div>
      <div class="empty" id="c-empty" hidden>
        <div class="e-ic"><i class="ic" data-ic="contacts" data-size="58"></i></div>
        <b>Noch keine Kontakte vorhanden</b>
        Legen Sie Ihren ersten Kunden oder Lieferanten an.
      </div>
    </section>

    <!-- ===== Tools ===== -->
    <section class="view" data-view="tools">
      <div class="page-head"><h1>Kalkulation &amp; Tools</h1><p>Praktische Helfer für den Büroalltag.</p></div>
      <div class="tool-nav" id="tool-nav">
        <button data-tool="calc" class="on">Taschenrechner</button>
        <button data-tool="conv">Umrechner</button>
        <button data-tool="vat">MwSt &amp; Prozent</button>
        <button data-tool="notes">Notizen</button>
        <button data-tool="tasks">Aufgaben</button>
      </div>

      <!-- Taschenrechner -->
      <div class="tool-panel active" id="tool-calc">
        <div class="calc" id="calc-box" tabindex="0" title="Klicken, um Tastatureingabe zu aktivieren">
          <input id="calc-display" readonly value="0">
          <div class="keypad">
            <button data-k="C">C</button><button data-k="⌫">⌫</button><button data-k="(" class="k-op">(</button><button data-k=")" class="k-op">)</button>
            <button data-k="7">7</button><button data-k="8">8</button><button data-k="9">9</button><button data-k="/" class="k-op">÷</button>
            <button data-k="4">4</button><button data-k="5">5</button><button data-k="6">6</button><button data-k="*" class="k-op">×</button>
            <button data-k="1">1</button><button data-k="2">2</button><button data-k="3">3</button><button data-k="-" class="k-op">−</button>
            <button data-k="0">0</button><button data-k=",">,</button><button data-k="=" class="k-eq">=</button><button data-k="+" class="k-op">+</button>
          </div>
          <p style="margin-top:8px;font-size:11px;color:var(--ink-soft)">Tastatur wird unterstützt, wenn der Rechner fokussiert ist.</p>
        </div>
      </div>

      <!-- Umrechner -->
      <div class="tool-panel" id="tool-conv">
        <div class="card" style="max-width:560px">
          <div class="card-h"><i class="ic" data-ic="conv" data-size="17"></i>Einheiten umrechnen</div>
          <div class="card-b">
            <div class="fw"><label class="flabel">Kategorie</label>
              <select id="conv-cat"></select></div>
            <div class="fgrid">
              <div><label class="flabel">Wert</label><input type="text" id="conv-val" value="1" inputmode="decimal"></div>
              <div><label class="flabel">Von</label><select id="conv-from"></select></div>
              <div><label class="flabel">Nach</label><select id="conv-to"></select></div>
              <div style="align-self:end"><button class="btn" id="conv-swap" title="Einheiten tauschen">⇄ Tauschen</button></div>
            </div>
            <div style="background:#eef4fb;border:1px solid #c3d5ea;border-radius:4px;padding:12px 14px;font-size:16px">
              Ergebnis: <b id="conv-result" style="color:#23406b">–</b>
            </div>
          </div>
        </div>
      </div>

      <!-- MwSt & Prozent -->
      <div class="tool-panel" id="tool-vat">
        <div class="tools-grid">
          <div class="card">
            <div class="card-h"><i class="ic" data-ic="percent" data-size="17"></i>MwSt-Rechner</div>
            <div class="card-b">
              <div class="fgrid" style="grid-template-columns:1fr 1fr">
                <div><label class="flabel">Nettobetrag (€)</label><input type="text" id="vat-net" value="100" inputmode="decimal"></div>
                <div><label class="flabel">Steuersatz</label><select id="vat-rate"><option value="19">19&nbsp;%</option><option value="7">7&nbsp;%</option><option value="0">0&nbsp;%</option></select></div>
              </div>
              <p style="margin-top:8px">MwSt-Betrag: <b id="vat-amt">19,00 €</b></p>
              <p>Bruttobetrag: <b id="vat-gross" style="color:#23406b">119,00 €</b></p>
            </div>
          </div>
          <div class="card">
            <div class="card-h"><i class="ic" data-ic="percent" data-size="17"></i>Prozentrechnung</div>
            <div class="card-b">
              <div class="fgrid" style="grid-template-columns:1fr 1fr">
                <div><label class="flabel">Grundwert</label><input type="text" id="pct-base" value="200" inputmode="decimal"></div>
                <div><label class="flabel">Prozentsatz (%)</label><input type="text" id="pct-val" value="15" inputmode="decimal"></div>
              </div>
              <p style="margin-top:8px">Prozentwert: <b id="pct-res">30</b></p>
              <p>Grundwert + Prozent: <b id="pct-plus">230</b></p>
            </div>
          </div>
        </div>
      </div>

      <!-- Notizen -->
      <div class="tool-panel" id="tool-notes">
        <div class="tools-grid" style="grid-template-columns:300px 1fr">
          <div class="card">
            <div class="card-h">Notizen <span style="flex:1"></span><button class="btn" id="note-new" style="padding:3px 10px">+ Neu</button></div>
            <div class="card-b" style="padding:6px"><div id="notes-list"></div></div>
          </div>
          <div class="card">
            <div class="card-h">Editor <span id="note-meta" style="margin-left:auto;font-weight:400;font-size:11.5px;color:var(--ink-soft)"></span></div>
            <div class="card-b">
              <div class="fw"><input type="text" id="note-title" placeholder="Titel der Notiz"></div>
              <div class="fw"><textarea id="note-body" rows="12" placeholder="Ihre Notiz…"></textarea></div>
              <div style="display:flex;gap:8px">
                <button class="btn primary" id="note-save">Speichern</button>
                <button class="btn" id="note-del">Löschen</button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Aufgaben -->
      <div class="tool-panel" id="tool-tasks">
        <div class="card" style="max-width:860px">
          <div class="card-h"><i class="ic" data-ic="task" data-size="17"></i>Aufgabenliste</div>
          <div class="card-b">
            <div style="display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px">
              <input type="text" id="task-input" placeholder="Neue Aufgabe…" style="flex:1;min-width:200px">
              <input type="date" id="task-due">
              <select id="task-prio"><option value="normal">Normal</option><option value="hoch">Hoch</option><option value="niedrig">Niedrig</option></select>
              <button class="btn primary" id="task-add">Hinzufügen</button>
            </div>
            <div style="margin-bottom:10px">
              <button class="btn on" data-tf="offen" style="padding:3px 10px">Offen</button>
              <button class="btn" data-tf="alle" style="padding:3px 10px">Alle</button>
              <button class="btn" data-tf="erledigt" style="padding:3px 10px">Erledigt</button>
            </div>
            <div id="tasks-list"></div>
          </div>
        </div>
      </div>
    </section>

    <!-- ===== Termine & Wiedervorlagen ===== -->
    <section class="view" data-view="terms">
      <div class="page-head"><h1>Termine &amp; Wiedervorlagen</h1><p>Monatsübersicht und Fristen im Blick behalten.</p></div>
      <div class="term-grid">
        <div class="card">
          <div class="card-b">
            <div class="cal-bar">
              <button class="btn" id="cal-prev" title="Vormonat">◀</button>
              <h2 id="cal-title"></h2>
              <button class="btn" id="cal-next" title="Nächster Monat">▶</button>
              <button class="btn" id="cal-today">Heute</button>
            </div>
            <div id="cal-wrap"></div>
          </div>
        </div>
        <div style="display:flex;flex-direction:column;gap:14px">
          <div class="card">
            <div class="card-h"><i class="ic" data-ic="calendar" data-size="17"></i><span id="day-title">Termine</span>
              <span style="flex:1"></span><button class="btn" id="btn-new-event" style="padding:3px 10px">+ Termin</button>
            </div>
            <div class="card-b" id="day-events" style="padding:8px 14px"></div>
          </div>
          <div class="card">
            <div class="card-h"><i class="ic" data-ic="clock" data-size="17"></i>Wiedervorlagen
              <span style="flex:1"></span>
              <label style="font-weight:400;font-size:11.5px;display:flex;align-items:center;gap:4px">
                <input type="checkbox" id="rem-show-done"> Erledigte zeigen</label>
              <button class="btn" id="btn-new-rem" style="padding:3px 10px">+ Neu</button>
            </div>
            <div class="card-b" id="rem-list" style="padding:8px 14px"></div>
          </div>
        </div>
      </div>
    </section>

  </main>

  <!-- ======================= Statusleiste ======================= -->
  <div id="statusbar">
    <span class="sb-seg" id="sb-msg">Bereit</span>
    <span class="spacer"></span>
    <span class="sb-seg" id="sb-view">Start</span>
    <span class="sb-seg" id="sb-date"></span>
    <span class="sb-seg" id="sb-db">Datenbank wird verbunden…</span>
  </div>
</div>

<!-- Versteckte Eingaben -->
<input type="file" id="file-input" multiple hidden>
<input type="file" id="ocr-input" accept="image/*" hidden>
<input type="file" id="import-input" accept="application/json,.json" hidden>

<div id="modal-root"></div>
<div id="toasts"></div>
<div id="print-area"></div>

<div id="fatal">
  <div class="f-box">
    <h2 style="color:var(--err);margin-bottom:10px">Speicher nicht verfügbar</h2>
    <p>IndexedDB konnte nicht geöffnet werden. Dies kann passieren, wenn der Browser den privaten Modus stark einschränkt oder der Speicher blockiert wird.</p>
    <p style="margin-top:10px"><button class="btn primary" onclick="location.reload()">Erneut versuchen</button></p>
  </div>
</div>

<script>
/* ==========================================================================
   BÜRO MANAGEMENT – Anwendungslogik
   Abschnitte:
    1) Helfer & UI-Bausteine   5) Texterkennung (OCR)
    2) Icons                   6) Rechnungen
    3) Datenbank (IndexedDB)   7) Kontakte
    4) Navigation & Start      8) Tools, Kalender, Wiedervorlagen, Backup
   ========================================================================== */
"use strict";

/* ---------------- 1) HELFER ---------------- */
const $  = s => document.querySelector(s);
const $$ = s => Array.from(document.querySelectorAll(s));

function esc(s){
  return String(s==null?"":s).replace(/[&<>"']/g, c =>
    ({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
}
function pad2(n){ return String(n).padStart(2,"0"); }
function toISO(d){ return d.getFullYear()+"-"+pad2(d.getMonth()+1)+"-"+pad2(d.getDate()); }
function todayISO(){ return toISO(new Date()); }
function addDays(n){ const d=new Date(); d.setDate(d.getDate()+n); return toISO(d); }
function fmtDate(s){
  if(!s) return "–";
  const d = new Date(s+"T00:00:00");
  return isNaN(d) ? String(s) : d.toLocaleDateString("de-DE");
}
function fmtDateTime(ts){
  const d = new Date(ts);
  return isNaN(d) ? "–" : d.toLocaleDateString("de-DE") + ", " + d.toLocaleTimeString("de-DE",{hour:"2-digit",minute:"2-digit"});
}
function fmtMoney(v){
  return (isFinite(v)?v:0).toLocaleString("de-DE",{style:"currency",currency:"EUR"});
}
function fmtBytes(b){
  if(!isFinite(b)) return "–";
  if(b < 1024) return b+" B";
  const u=["KB","MB","GB","TB"]; let i=-1;
  do{ b/=1024; i++; }while(b>=1024 && i<u.length-1);
  return b.toLocaleString("de-DE",{maximumFractionDigits:1})+" "+u[i];
}
function deNum(str){
  const v = parseFloat(String(str).replace(",","."));
  return isFinite(v) ? v : NaN;
}
function status(msg){ const el=$("#sb-msg"); if(el) el.textContent=msg; }

function toast(msg, type){
  try{
    const t=document.createElement("div");
    t.className="toast t-"+(type||"info");
    t.textContent=(type==="ok"?"✔ ":type==="err"?"⚠ ":"ℹ ")+msg;
    $("#toasts").appendChild(t);
    setTimeout(()=>{ t.classList.add("out"); setTimeout(()=>t.remove(),400); }, 3600);
  }catch(e){ /* Toasts sind nicht kritisch */ }
}

function countUp(el, val){
  const start=performance.now(), dur=520;
  function step(t){
    const p=Math.min(1,(t-start)/dur);
    el.textContent=Math.round(val*p).toLocaleString("de-DE");
    if(p<1) requestAnimationFrame(step);
  }
  requestAnimationFrame(step);
}

/* ---- Modal-System ---- */
const Modal = {
  open(o){
    const ov=document.createElement("div");
    ov.className="m-ov"+(o.stay?" m-stay":"");
    const box=document.createElement("div");
    box.className="m-box"+(o.wide?" m-wide":"");
    box.innerHTML='<div class="m-title">'+esc(o.title||"")+
      '<button class="m-x" title="Schließen">✕</button></div>'+
      '<div class="m-body"></div><div class="m-foot"></div>';
    const body=box.querySelector(".m-body");
    if(o.body instanceof Node) body.appendChild(o.body);
    else body.innerHTML=o.body||"";
    const foot=box.querySelector(".m-foot");
    const btns=o.buttons||[];
    if(!btns.length) foot.remove();
    btns.forEach(b=>{
      const btn=document.createElement("button");
      btn.className="btn"+(b.primary?" primary":"");
      btn.textContent=b.label;
      btn.addEventListener("click", async ()=>{
        try{
          const r = b.onClick ? await b.onClick() : undefined;
          if(r!==false && !o.stay) Modal.close(ov);
        }catch(err){ console.warn(err); toast("Aktion fehlgeschlagen","err"); }
      });
      foot.appendChild(btn);
    });
    box.querySelector(".m-x").addEventListener("click",()=>Modal.close(ov));
    ov.addEventListener("mousedown",e=>{ if(e.target===ov && !o.stay) Modal.close(ov); });
    ov.appendChild(box);
    $("#modal-root").appendChild(ov);
    requestAnimationFrame(()=>ov.classList.add("in"));
    if(o.mount) o.mount(body, ov);
    return ov;
  },
  close(ov){
    if(!ov){ const all=$$(".m-ov"); ov=all[all.length-1]; }
    if(!ov) return;
    ov.classList.remove("in");
    setTimeout(()=>ov.remove(),180);
  }
};
function confirmBox(msg, cb){
  Modal.open({
    title:"Bitte bestätigen",
    body:'<p style="font-size:14px;line-height:1.5">'+esc(msg)+"</p>",
    buttons:[
      {label:"Abbrechen"},
      {label:"OK", primary:true, onClick:cb}
    ]
  });
}

/* ---------------- 2) ICONS ---------------- */
const ICONS = {
dashboard:'<svg viewBox="0 0 32 32"><rect x="3" y="3" width="12" height="12" rx="1.5" fill="#3d78c9"/><rect x="17" y="3" width="12" height="12" rx="1.5" fill="#7db440"/><rect x="3" y="17" width="12" height="12" rx="1.5" fill="#f5a431"/><rect x="17" y="17" width="12" height="12" rx="1.5" fill="#e05252"/></svg>',
docs:'<svg viewBox="0 0 32 32"><path d="M2 8c0-1.1.9-2 2-2h8l3 3h13c1.1 0 2 .9 2 2v13c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V8z" fill="#e8b64c"/><path d="M2 13h28l-2.5 11.2c-.2 1-1 1.8-2 1.8H6.5c-1 0-1.8-.8-2-1.8L2 13z" fill="#ffd35c"/></svg>',
ocr:'<svg viewBox="0 0 32 32"><rect x="5" y="3" width="16" height="24" rx="1.5" fill="#fff" stroke="#8aa4c8"/><path d="M8.5 8h9M8.5 12h9M8.5 16h5" stroke="#b7c8dd" stroke-width="1.6"/><circle cx="20" cy="20" r="6" fill="rgba(61,120,201,.25)" stroke="#2b6cb0" stroke-width="2.2"/><path d="M24.5 24.5L29 29" stroke="#2b6cb0" stroke-width="2.6" stroke-linecap="round"/></svg>',
inv:'<svg viewBox="0 0 32 32"><path d="M7 2h13l5 5v23H7V2z" fill="#fff" stroke="#8aa4c8"/><path d="M20 2v5h5" fill="#dce8f5" stroke="#8aa4c8"/><path d="M11 11h11M11 14.5h11M18 25.5h4" stroke="#c2d2e6" stroke-width="1.6"/><circle cx="13.5" cy="21.5" r="4.8" fill="#7db440"/><text x="13.5" y="24.6" font-size="8.5" text-anchor="middle" fill="#fff" font-family="Segoe UI" font-weight="700">€</text></svg>',
contacts:'<svg viewBox="0 0 32 32"><circle cx="12" cy="11" r="5" fill="#3d78c9"/><path d="M3.5 27c.6-5 4-8 8.5-8s7.9 3 8.5 8H3.5z" fill="#3d78c9"/><circle cx="22.5" cy="12" r="4" fill="#8fb0d8"/><path d="M17 26.5c.5-3.8 2.8-6.3 5.5-6.3 3.3 0 5.4 2.6 6 6.3H17z" fill="#8fb0d8"/></svg>',
tools:'<svg viewBox="0 0 32 32"><rect x="6" y="2" width="20" height="28" rx="2.5" fill="#3a5f8f"/><rect x="9" y="5" width="14" height="6" rx="1" fill="#d7e7f5"/><g fill="#fff"><rect x="9" y="14" width="3.6" height="3.2" rx=".7"/><rect x="14.2" y="14" width="3.6" height="3.2" rx=".7"/><rect x="19.4" y="14" width="3.6" height="3.2" rx=".7"/><rect x="9" y="19" width="3.6" height="3.2" rx=".7"/><rect x="14.2" y="19" width="3.6" height="3.2" rx=".7"/><rect x="19.4" y="19" width="3.6" height="3.2" rx=".7"/><rect x="9" y="24" width="8.8" height="3.2" rx=".7"/><rect x="19.4" y="24" width="3.6" height="3.2" rx=".7"/></g></svg>',
calendar:'<svg viewBox="0 0 32 32"><rect x="3" y="5" width="26" height="24" rx="2" fill="#fff" stroke="#9db2cc"/><rect x="3" y="5" width="26" height="7" fill="#d24b45"/><rect x="8" y="2.5" width="3" height="6" rx="1.2" fill="#5a708e"/><rect x="21" y="2.5" width="3" height="6" rx="1.2" fill="#5a708e"/><g fill="#7e93ad"><rect x="7" y="15" width="4" height="3"/><rect x="14" y="15" width="4" height="3"/><rect x="21" y="15" width="4" height="3"/><rect x="7" y="21" width="4" height="3"/><rect x="21" y="21" width="4" height="3"/></g><rect x="14" y="21" width="4" height="3" fill="#2b6cb0"/></svg>',
clock:'<svg viewBox="0 0 32 32"><circle cx="16" cy="16" r="13" fill="#fff" stroke="#e09a2b" stroke-width="2.5"/><path d="M16 8v8l6 4" stroke="#c07818" stroke-width="2.4" fill="none" stroke-linecap="round"/></svg>',
upload:'<svg viewBox="0 0 32 32"><path d="M4 20v6c0 1.1.9 2 2 2h20c1.1 0 2-.9 2-2v-6" fill="none" stroke="#5a708e" stroke-width="2.4"/><path d="M16 5v14" stroke="#3d9c47" stroke-width="3" stroke-linecap="round"/><path d="M9.5 11L16 4.5 22.5 11" fill="none" stroke="#3d9c47" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/></svg>',
download:'<svg viewBox="0 0 32 32"><path d="M4 20v6c0 1.1.9 2 2 2h20c1.1 0 2-.9 2-2v-6" fill="none" stroke="#5a708e" stroke-width="2.4"/><path d="M16 5v14" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round"/><path d="M9.5 12.5L16 19l6.5-6.5" fill="none" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/></svg>',
trash:'<svg viewBox="0 0 32 32"><path d="M7 9h18l-1.6 19c-.1 1-.9 2-2 2H10.6c-1.1 0-1.9-1-2-2L7 9z" fill="#e05252"/><rect x="5" y="5.5" width="22" height="3.6" rx="1.2" fill="#c23d3d"/><path d="M12 13v13M16 13v13M20 13v13" stroke="#fff" stroke-width="1.8" opacity=".75"/></svg>',
preview:'<svg viewBox="0 0 32 32"><circle cx="14" cy="14" r="9" fill="rgba(61,120,201,.2)" stroke="#2b6cb0" stroke-width="2.6"/><path d="M21 21l8 8" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round"/></svg>',
plus:'<svg viewBox="0 0 32 32"><circle cx="16" cy="16" r="13" fill="#5aa952"/><path d="M16 9v14M9 16h14" stroke="#fff" stroke-width="3.2" stroke-linecap="round"/></svg>',
print:'<svg viewBox="0 0 32 32"><rect x="7" y="3" width="18" height="9" fill="#fff" stroke="#8aa4c8"/><rect x="3" y="11" width="26" height="12" rx="1.6" fill="#6d87a8"/><rect x="23" y="13.5" width="3.4" height="2.4" rx="1" fill="#a8f0a0"/><rect x="8" y="18" width="16" height="11" fill="#fff" stroke="#8aa4c8"/></svg>',
copy:'<svg viewBox="0 0 32 32"><rect x="10" y="3" width="17" height="21" rx="1.5" fill="#fff" stroke="#8aa4c8"/><rect x="5" y="8" width="16" height="21" rx="1.5" fill="#eaf2fb" stroke="#8aa4c8"/><path d="M8.5 14h9M8.5 18h9M8.5 22h6" stroke="#9fb6d2" stroke-width="1.6"/></svg>',
gear:'<svg viewBox="0 0 32 32"><circle cx="16" cy="16" r="10.5" fill="none" stroke="#6d87a8" stroke-width="4.5" stroke-dasharray="5.4 3.6"/><circle cx="16" cy="16" r="5" fill="#eaf2fb" stroke="#6d87a8" stroke-width="2"/></svg>',
export:'<svg viewBox="0 0 32 32"><path d="M8 14v12c0 1.1.9 2 2 2h12c1.1 0 2-.9 2-2V14" fill="none" stroke="#5a708e" stroke-width="2.4"/><path d="M16 20V3" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round"/><path d="M10 8.5L16 3l6 5.5" fill="none" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/></svg>',
import:'<svg viewBox="0 0 32 32"><path d="M8 14v12c0 1.1.9 2 2 2h12c1.1 0 2-.9 2-2V14" fill="none" stroke="#5a708e" stroke-width="2.4"/><path d="M16 3v17" stroke="#3d9c47" stroke-width="3" stroke-linecap="round"/><path d="M10 14.5L16 20l6-5.5" fill="none" stroke="#3d9c47" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/></svg>',
check:'<svg viewBox="0 0 32 32"><circle cx="16" cy="16" r="13" fill="#5aa952"/><path d="M9.5 16.5l4.5 4.5 8.5-9" fill="none" stroke="#fff" stroke-width="3.2" stroke-linecap="round" stroke-linejoin="round"/></svg>',
edit:'<svg viewBox="0 0 32 32"><path d="M4 28l1.2-4.8L22 6.4l3.6 3.6L8.8 26.8 4 28z" fill="#f2b63c"/><path d="M22 6.4l2-2c.8-.8 2-.8 2.8 0l.8.8c.8.8.8 2 0 2.8l-2 2-3.6-3.6z" fill="#e05252"/></svg>',
refresh:'<svg viewBox="0 0 32 32"><path d="M24.5 9.5A10.3 10.3 0 1 0 26.3 17" fill="none" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round"/><path d="M27.2 3.6l1 8.2-8-1.9z" fill="#2b6cb0"/></svg>',
note:'<svg viewBox="0 0 32 32"><path d="M4 4h24v17l-7 7H4V4z" fill="#ffe98a" stroke="#d9b93f"/><path d="M21 28v-7h7" fill="#f5d45a" stroke="#d9b93f"/><path d="M8 10h16M8 14h16M8 18h9" stroke="#b99a2e" stroke-width="1.6"/></svg>',
task:'<svg viewBox="0 0 32 32"><rect x="5" y="4" width="22" height="26" rx="2" fill="#fff" stroke="#8aa4c8"/><rect x="11" y="2" width="10" height="5" rx="1.5" fill="#6d87a8"/><path d="M10 16l3.5 3.5L22 12" fill="none" stroke="#5aa952" stroke-width="2.6" stroke-linecap="round" stroke-linejoin="round"/><path d="M10 24h12" stroke="#c2d2e6" stroke-width="2"/></svg>',
conv:'<svg viewBox="0 0 32 32"><path d="M4 11h20" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round"/><path d="M19 5.5L24.5 11 19 16.5" fill="none" stroke="#2b6cb0" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/><path d="M28 21H8" stroke="#3d9c47" stroke-width="3" stroke-linecap="round"/><path d="M13 15.5L7.5 21l5.5 5.5" fill="none" stroke="#3d9c47" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/></svg>',
percent:'<svg viewBox="0 0 32 32"><circle cx="16" cy="16" r="13" fill="#3d78c9"/><path d="M11 21L21 11" stroke="#fff" stroke-width="2.8" stroke-linecap="round"/><circle cx="12" cy="12" r="2.6" fill="none" stroke="#fff" stroke-width="2.2"/><circle cx="20" cy="20" r="2.6" fill="none" stroke="#fff" stroke-width="2.2"/></svg>'
};
function hydrateIcons(root){
  (root||document).querySelectorAll("[data-ic]").forEach(el=>{
    const name=el.dataset.ic, svg=ICONS[name];
    if(!svg){ el.innerHTML=""; return; }
    el.innerHTML=svg;
    const s=el.querySelector("svg");
    if(s){ const px=(parseInt(el.dataset.size,10)||20)+"px"; s.setAttribute("width",px); s.setAttribute("height",px); s.style.display="block"; }
  });
}

/* ---------------- 3) DATENBANK (IndexedDB) ---------------- */
const DB = {
  db:null, name:"buero_management", version:1,
  open(){
    return new Promise((res,rej)=>{
      if(!("indexedDB" in window)) return rej(new Error("IndexedDB wird nicht unterstützt"));
      let req;
      try{ req = indexedDB.open(this.name, this.version); }
      catch(e){ return rej(e); }
      req.onupgradeneeded = e=>{
        const db=e.target.result;
        const mk=(n)=>{ if(!db.objectStoreNames.contains(n)) db.createObjectStore(n,{keyPath:"id",autoIncrement:true}); };
        ["documents","invoices","contacts","tasks","notes","reminders","events"].forEach(mk);
        if(!db.objectStoreNames.contains("settings")) db.createObjectStore("settings",{keyPath:"key"});
      };
      req.onsuccess = e=>{ this.db=e.target.result; res(); };
      req.onerror   = ()=> rej(req.error || new Error("Datenbankfehler"));
      req.onblocked = ()=> rej(new Error("Datenbank blockiert"));
    });
  },
  store(s,mode){ return this.db.transaction(s, mode||"readonly").objectStore(s); },
  prom(r){ return new Promise((res,rej)=>{ r.onsuccess=()=>res(r.result); r.onerror=()=>rej(r.error); }); },
  getAll(s){ return this.prom(this.store(s).getAll()); },
  get(s,k){ return this.prom(this.store(s).get(k)); },
  add(s,v){ return this.prom(this.store(s,"readwrite").add(v)); },
  put(s,v){ return this.prom(this.store(s,"readwrite").put(v)); },
  del(s,k){ return this.prom(this.store(s,"readwrite").delete(k)); },
  clear(s){ return this.prom(this.store(s,"readwrite").clear()); }
};

/* ---- Einstellungen (Cache S) ---- */
const S = {};
async function getSetting(k, def){
  try{ const r=await DB.get("settings",k); return (r && r.value!==undefined)?r.value:def; }
  catch(e){ return def; }
}
async function setSetting(k,v){
  try{ await DB.put("settings",{key:k,value:v}); S[k]=v; }
  catch(e){ toast("Einstellung konnte nicht gespeichert werden","err"); }
}
async function loadSettings(){
  S.company   = await getSetting("company","Mein Unternehmen");
  S.street    = await getSetting("street","");
  S.zipCity   = await getSetting("zipCity","");
  S.phone     = await getSetting("phone","");
  S.email     = await getSetting("email","");
  S.taxId     = await getSetting("taxId","");
  S.vatId     = await getSetting("vatId","");
  S.iban      = await getSetting("iban","");
  S.bic       = await getSetting("bic","");
  S.prefix    = await getSetting("prefix","RE-");
  S.invCounter= await getSetting("invCounter",1);
}
function nextNumber(){
  return (S.prefix||"RE-")+new Date().getFullYear()+"-"+String(S.invCounter||1).padStart(4,"0");
}

/* ---------------- 4) NAVIGATION & START ---------------- */
const state = { tab:"datei" };
const TAB_NAMES = {datei:"Start",dashboard:"Dashboard",docs:"Dokumente",ocr:"Texterkennung (OCR)",
                   inv:"Rechnungen",contacts:"Kontakte",tools:"Kalkulation & Tools",terms:"Termine & Wiedervorlagen"};

function setTab(t){
  state.tab=t;
  $$(".tab").forEach(b=>b.classList.toggle("active",b.dataset.tab===t));
  $$(".rpanel").forEach(p=>p.classList.toggle("active",p.dataset.panel===t));
  $$(".view").forEach(v=>v.classList.toggle("active",v.dataset.view===t));
  $("#ribbon").classList.toggle("backstage",t==="datei");
  $("#sb-view").textContent=TAB_NAMES[t]||t;
  status(TAB_NAMES[t]+" geöffnet");
  try{
    if(t==="dashboard") renderDashboard();
    else if(t==="docs") renderDocs();
    else if(t==="inv") renderInvoices();
    else if(t==="contacts") renderContacts();
    else if(t==="terms"){ renderCalendar(); renderReminders(); }
    else if(t==="datei") renderHome();
  }catch(e){ console.warn(e); toast("Ansicht konnte nicht geladen werden","err"); }
}

function renderAll(){
  // Jede Ansicht einzeln absichern, damit ein Fehler nicht alles blockiert.
  const jobs=[renderHome,renderDashboard,renderDocs,renderInvoices,renderContacts,
              renderNotes,renderTasks,renderCalendar,renderReminders];
  return jobs.reduce((p,fn)=>p.then(()=>Promise.resolve().then(fn).catch(e=>console.warn(e))),Promise.resolve());
}

/* ---------------- Startseite ---------------- */
async function renderHome(){
  try{
    const [docs,invs,contacts,tasks,evs]=await Promise.all([
      DB.getAll("documents"),DB.getAll("invoices"),DB.getAll("contacts"),DB.getAll("tasks"),DB.getAll("events")]);
    $("#hm-docs").textContent=docs.length+" Datei"+(docs.length===1?"":"en");
    $("#hm-inv").textContent=invs.length+" Rechnung"+(invs.length===1?"":"en");
    $("#hm-contacts").textContent=contacts.length+" Kontakt"+(contacts.length===1?"":"e");
    $("#hm-terms").textContent=evs.length+" Termin"+(evs.length===1?"":"e")+" · "+tasks.filter(t=>!t.done).length+" offene Aufgaben";
    $("#hm-ocr").textContent=(typeof Tesseract!=="undefined")?"OCR-Modul bereit":"OCR offline – CDN nötig";
  }catch(e){ console.warn(e); }
}

/* ---------------- Dashboard ---------------- */
function calcTotals(items){
  let net=0; const vat={};
  (items||[]).forEach(it=>{
    const n=(it.qty||0)*(it.price||0);
    net+=n;
    const r=it.vat||0;
    vat[r]=(vat[r]||0)+n*r/100;
  });
  const vatSum=Object.values(vat).reduce((a,b)=>a+b,0);
  return {net,vat,vatSum,gross:net+vatSum};
}

async function renderDashboard(){
  try{
    const [docs,invs,contacts,tasks,rems,evs]=await Promise.all([
      DB.getAll("documents"),DB.getAll("invoices"),DB.getAll("contacts"),
      DB.getAll("tasks"),DB.getAll("reminders"),DB.getAll("events")]);
    const today=todayISO();
    const openInv=invs.filter(i=>!i.paid);
    const openSum=openInv.reduce((a,i)=>a+calcTotals(i.items).gross,0);
    const dueTasks=tasks.filter(t=>!t.done&&t.due&&t.due<=today);
    const dueRems=rems.filter(r=>!r.done&&r.date&&r.date<=today);
    const todayEvs=evs.filter(e=>e.date===today);

    countUp($("#st-docs"),docs.length);
    countUp($("#st-invoices"),invs.length);
    $("#st-open").textContent=fmtMoney(openSum);
    countUp($("#st-contacts"),contacts.length);
    countUp($("#st-tasks"),tasks.filter(t=>!t.done).length);
    countUp($("#st-today"),dueTasks.length+dueRems.length+todayEvs.length);

    // Zuletzt geöffnete/hinzugefügte Dokumente
    const recent=[...docs].sort((a,b)=>(b.addedAt||0)-(a.addedAt||0)).slice(0,6);
    $("#dash-recent").innerHTML = recent.length ? recent.map(d=>
      '<li data-doc-id="'+d.id+'"><span class="dot" style="background:'+( (d.type||"").startsWith("image/")?"#e2922c":(d.type==="application/pdf"?"#d24b45":"#3d78c9"))+'"></span>'+
      '<div class="ml-main"><b>'+esc(d.name)+'</b><small>'+fmtBytes(d.size)+' · '+fmtDateTime(d.addedAt)+'</small></div>'+
      '<i class="ic" data-ic="preview" data-size="15"></i></li>').join("")
      : '<li style="cursor:default"><div class="ml-main"><small>Noch keine Dokumente vorhanden.</small></div></li>';
    hydrateIcons($("#dash-recent"));
    $("#dash-recent").querySelectorAll("[data-doc-id]").forEach(li=>
      li.addEventListener("click",()=>previewDoc(+li.dataset.docId)));

    // Heute fällig
    const dueItems=[
      ...dueTasks.map(t=>({t:"Aufgabe",x:t.title,d:t.due,c:"#e2922c"})),
      ...dueRems.map(r=>({t:"Wiedervorlage",x:r.title,d:r.date,c:"#d24b45"})),
      ...todayEvs.map(e=>({t:"Termin",x:e.title,d:e.date,c:"#3d78c9"}))
    ].sort((a,b)=>(a.d||"").localeCompare(b.d||""));
    $("#dash-due").innerHTML = dueItems.length ? dueItems.map(i=>
      '<li style="cursor:default"><span class="dot" style="background:'+i.c+'"></span>'+
      '<div class="ml-main"><b>'+esc(i.x)+'</b><small>'+i.t+' · '+fmtDate(i.d)+'</small></div></li>').join("")
      : '<li style="cursor:default"><div class="ml-main"><small>Alles erledigt – keine fälligen Punkte. ✔</small></div></li>';

    // Offene Rechnungen
    const openList=[...openInv].sort((a,b)=>(a.due||"").localeCompare(b.due||"")).slice(0,5);
    $("#dash-openinv").innerHTML = openList.length ? openList.map(i=>{
      const overdue=i.due&&i.due<today;
      return '<li style="cursor:default"><span class="dot" style="background:'+(overdue?"#d24b45":"#e2922c")+'"></span>'+
        '<div class="ml-main"><b>'+esc(i.number)+'</b><small>'+esc((i.customer&&i.customer.name)||"")+' · fällig '+fmtDate(i.due)+'</small></div>'+
        '<b>'+fmtMoney(calcTotals(i.items).gross)+'</b></li>';
    }).join("") : '<li style="cursor:default"><div class="ml-main"><small>Keine offenen Rechnungen.</small></div></li>';
  }catch(e){ console.warn(e); toast("Dashboard konnte nicht geladen werden","err"); }
}

/* ---------------- 4b) DOKUMENTE ---------------- */
const docsState={filter:"all",q:""};
let DOCS_CACHE=[];

function docCat(t){
  t=t||"";
  if(t.startsWith("image/")) return "bild";
  if(t==="application/pdf") return "pdf";
  if(t.startsWith("text/")) return "text";
  return "sonst";
}
const CAT_BADGE={bild:'<span class="badge b-orange">Bild</span>',pdf:'<span class="badge b-red">PDF</span>',
                 text:'<span class="badge b-blue">Text</span>',sonst:'<span class="badge b-grey">Sonstige</span>'};

async function uploadFiles(list){
  const files=Array.from(list||[]);
  if(!files.length) return;
  let ok=0, fail=0;
  for(const f of files){
    try{
      await DB.add("documents",{name:f.name,type:f.type||"application/octet-stream",size:f.size,addedAt:Date.now(),blob:f});
      ok++;
    }catch(e){ console.warn(e); fail++; toast("Fehler bei „"+f.name+"“","err"); }
  }
  if(ok){ toast(ok+" Datei"+(ok>1?"en":"")+" gespeichert","ok"); status(ok+" Datei(en) hochgeladen"); renderDocs(); renderHome(); }
  if(fail) toast(fail+" Datei(en) konnten nicht gespeichert werden","err");
}

async function renderDocs(){
  try{
    DOCS_CACHE=await DB.getAll("documents");
    const q=docsState.q.toLowerCase();
    const list=[...DOCS_CACHE]
      .filter(d=>docsState.filter==="all"||docCat(d.type)===docsState.filter)
      .filter(d=>!q||(d.name||"").toLowerCase().includes(q))
      .sort((a,b)=>(b.addedAt||0)-(a.addedAt||0));
    $("#doc-count").textContent=list.length+" von "+DOCS_CACHE.length+" Dateien";
    const tb=$("#doc-list-body");
    tb.innerHTML=list.map(d=>
      '<tr><td><b>'+esc(d.name)+'</b></td><td>'+CAT_BADGE[docCat(d.type)]+'</td><td>'+fmtBytes(d.size)+'</td>'+
      '<td>'+fmtDateTime(d.addedAt)+'</td><td style="white-space:nowrap">'+
      '<button class="ibtn" title="Vorschau" data-a="view" data-id="'+d.id+'"><i class="ic" data-ic="preview" data-size="15"></i></button>'+
      '<button class="ibtn" title="Download" data-a="dl" data-id="'+d.id+'"><i class="ic" data-ic="download" data-size="15"></i></button>'+
      '<button class="ibtn" title="Löschen" data-a="del" data-id="'+d.id+'"><i class="ic" data-ic="trash" data-size="15"></i></button>'+
      '</td></tr>').join("");
    hydrateIcons(tb);
    $("#doc-empty").hidden = DOCS_CACHE.length>0;
    tb.querySelectorAll("[data-a]").forEach(b=>b.addEventListener("click",()=>{
      const id=+b.dataset.id;
      if(b.dataset.a==="view") previewDoc(id);
      else if(b.dataset.a==="dl") downloadDoc(id);
      else deleteDoc(id);
    }));
  }catch(e){ console.warn(e); toast("Dokumentenliste konnte nicht geladen werden","err"); }
}

async function previewDoc(id){
  try{
    const d=await DB.get("documents",id);
    if(!d||!d.blob){ toast("Dokument nicht gefunden","err"); return; }
    const url=URL.createObjectURL(d.blob);
    const t=d.type||"";
    let html;
    if(t.startsWith("image/")) html='<div style="text-align:center"><img src="'+url+'" style="max-width:100%;max-height:62vh" alt=""></div>';
    else if(t==="application/pdf") html='<iframe src="'+url+'" style="width:100%;height:62vh;border:1px solid #cdd8e6;border-radius:3px" title="PDF-Vorschau"></iframe>';
    else if(t.startsWith("text/")){
      let txt="";
      try{ txt=(await d.blob.text()).slice(0,20000); }catch(e2){ txt="(Inhalt konnte nicht gelesen werden)"; }
      html='<pre style="white-space:pre-wrap;max-height:62vh;overflow:auto;font-family:Consolas,monospace;font-size:12.5px">'+esc(txt)+"</pre>";
    }else{
      html='<p>Für diesen Dateityp ('+esc(t||"unbekannt")+') ist keine Vorschau möglich. Nutzen Sie bitte den Download.</p>';
    }
    const ov=Modal.open({title:"Vorschau – "+d.name,wide:true,body:html,
      buttons:[{label:"Schließen"},{label:"Download",primary:true,onClick:()=>downloadDoc(id)}]});
    ov.addEventListener("remove",()=>{ try{ URL.revokeObjectURL(url); }catch(e3){} });
  }catch(e){ console.warn(e); toast("Vorschau fehlgeschlagen","err"); }
}

async function downloadDoc(id){
  try{
    const d=await DB.get("documents",id);
    if(!d||!d.blob){ toast("Datei nicht gefunden","err"); return; }
    const url=URL.createObjectURL(d.blob);
    const a=document.createElement("a");
    a.href=url; a.download=d.name||"datei";
    document.body.appendChild(a); a.click(); a.remove();
    setTimeout(()=>URL.revokeObjectURL(url),5000);
    status("Download gestartet: "+d.name);
    toast("Download gestartet","ok");
  }catch(e){ console.warn(e); toast("Download fehlgeschlagen","err"); }
}

function deleteDoc(id){
  const d=DOCS_CACHE.find(x=>x.id===id);
  confirmBox("Soll „"+((d&&d.name)||"diese Datei")+"“ endgültig gelöscht werden?", async ()=>{
    try{ await DB.del("documents",id); toast("Dokument gelöscht","ok"); renderDocs(); renderHome(); }
    catch(e){ toast("Löschen fehlgeschlagen","err"); }
  });
}

/* ---------------- 5) TEXT-ERKENNUNG (OCR) ---------------- */
const ocr={file:null,url:null,lang:"deu",busy:false};

function ocrSetFile(f, nameOverride){
  if(!f) return;
  const type=f.type||"";
  if(!type.startsWith("image/") && type!=="application/pdf"){
    toast("Bitte eine Bilddatei wählen (JPG, PNG, BMP …)","err"); return;
  }
  if(ocr.url){ try{ URL.revokeObjectURL(ocr.url); }catch(e){} }
  ocr.file=f;
  ocr.url=URL.createObjectURL(f);
  $("#ocr-img").innerHTML='<img src="'+ocr.url+'" alt="Vorschau">';
  $("#ocr-fname").textContent=(nameOverride||f.name||"Bild")+" · "+fmtBytes(f.size);
  $("#ocr-result").value="";
  $("#ocr-count").textContent="0 Zeichen";
  status("OCR bereit: "+(nameOverride||f.name||"Bild"));
}

async function runOCR(){
  if(ocr.busy) return;
  if(!ocr.file){ toast("Bitte zuerst ein Bild laden","err"); return; }
  if(typeof Tesseract==="undefined"){
    toast("OCR-Bibliothek nicht geladen. Bitte Internetverbindung prüfen und die Seite neu laden.","err");
    return;
  }
  ocr.busy=true;
  $("#ocr-progress").hidden=false;
  $("#ocr-label").textContent="Erkennung läuft …";
  $("#ocr-pct").textContent="";
  $("#ocr-fill").style.width="0%";
  try{
    const res=await Tesseract.recognize(ocr.file, ocr.lang, {logger:m=>{
      if(m && m.status==="recognizing text"){
        const p=Math.round((m.progress||0)*100);
        $("#ocr-fill").style.width=p+"%";
        $("#ocr-pct").textContent=p+" %";
      }
    }});
    const txt=((res&&res.data&&res.data.text)||"").trim();
    $("#ocr-result").value=txt;
    $("#ocr-count").textContent=txt.length+" Zeichen";
    toast(txt?"Text erfolgreich erkannt":"Kein Text erkannt", txt?"ok":"info");
    status("OCR abgeschlossen");
  }catch(e){
    console.warn(e);
    toast("Texterkennung fehlgeschlagen: "+((e&&e.message)?e.message:"unbekannter Fehler"),"err");
  }finally{
    ocr.busy=false;
    $("#ocr-label").textContent="Bereit";
    setTimeout(()=>{ $("#ocr-progress").hidden=true; },900);
  }
}

function fallbackCopy(ta){
  try{ ta.focus(); ta.select(); document.execCommand("copy"); toast("In Zwischenablage kopiert","ok"); }
  catch(e){ toast("Kopieren nicht möglich – bitte Text manuell markieren","err"); }
}
function copyOCR(){
  const ta=$("#ocr-result");
  if(!ta.value){ toast("Kein Text zum Kopieren vorhanden","err"); return; }
  if(navigator.clipboard && navigator.clipboard.writeText){
    navigator.clipboard.writeText(ta.value)
      .then(()=>toast("In Zwischenablage kopiert","ok"))
      .catch(()=>fallbackCopy(ta));
  }else fallbackCopy(ta);
}

async function ocrFromDocs(){
  try{
    const docs=(await DB.getAll("documents")).filter(d=>(d.type||"").startsWith("image/"))
      .sort((a,b)=>(b.addedAt||0)-(a.addedAt||0));
    if(!docs.length){ toast("Keine Bilddokumente in der Verwaltung vorhanden","info"); return; }
    Modal.open({title:"Bild aus der Dokumentenverwaltung wählen",
      body:'<div class="pick-list">'+docs.map(d=>
        '<button class="pick-item" data-id="'+d.id+'"><i class="ic" data-ic="preview" data-size="16"></i> '+esc(d.name)+
        '<small>'+fmtBytes(d.size)+"</small></button>").join("")+'</div>',
      mount:(body,ov)=>{
        hydrateIcons(body);
        body.querySelectorAll(".pick-item").forEach(b=>b.addEventListener("click",async()=>{
          try{
            const d=await DB.get("documents",+b.dataset.id);
            if(d&&d.blob){ ocrSetFile(d.blob,d.name); Modal.close(ov); setTab("ocr"); }
            else toast("Datei nicht gefunden","err");
          }catch(e){ toast("Auswahl fehlgeschlagen","err"); }
        }));
      }});
  }catch(e){ console.warn(e); toast("Dokumentenliste konnte nicht geladen werden","err"); }
}

/* ---------------- 6) RECHNUNGEN ---------------- */
const invState={q:"",status:"alle"};
let INV_CACHE=[];

function invStatusOf(inv){
  if(inv.paid) return "bezahlt";
  if(inv.due && inv.due < todayISO()) return "überfällig";
  return "offen";
}
const STATUS_BADGE={"offen":'<span class="badge b-orange">Offen</span>',
                    "überfällig":'<span class="badge b-red">Überfällig</span>',
                    "bezahlt":'<span class="badge b-green">Bezahlt</span>'};

async function renderInvoices(){
  try{
    INV_CACHE=await DB.getAll("invoices");
    const q=invState.q.toLowerCase();
    const list=[...INV_CACHE]
      .filter(i=>{
        const st=invStatusOf(i);
        if(invState.status!=="alle" && st!==invState.status) return false;
        if(q){
          const name=((i.customer&&i.customer.name)||"").toLowerCase();
          if(!(i.number||"").toLowerCase().includes(q) && !name.includes(q)) return false;
        }
        return true;
      })
      .sort((a,b)=>String(b.number).localeCompare(String(a.number)));
    $("#inv-count").textContent=list.length+" von "+INV_CACHE.length+" Rechnungen";
    const tb=$("#inv-list-body");
    tb.innerHTML=list.map(i=>{
      const t=calcTotals(i.items), st=invStatusOf(i);
      return '<tr><td><b>'+esc(i.number)+'</b></td><td>'+fmtDate(i.date)+'</td>'+
        '<td>'+esc((i.customer&&i.customer.name)||"–")+'</td><td>'+fmtDate(i.due)+'</td>'+
        '<td style="text-align:right;font-weight:600">'+fmtMoney(t.gross)+'</td><td>'+STATUS_BADGE[st]+'</td>'+
        '<td style="white-space:nowrap">'+
        '<button class="ibtn" title="Druckansicht" data-a="print" data-id="'+i.id+'"><i class="ic" data-ic="print" data-size="15"></i></button>'+
        '<button class="ibtn" title="Bearbeiten" data-a="edit" data-id="'+i.id+'"><i class="ic" data-ic="edit" data-size="15"></i></button>'+
        '<button class="ibtn" title="'+(i.paid?"Als offen markieren":"Als bezahlt markieren")+'" data-a="paid" data-id="'+i.id+'"><i class="ic" data-ic="check" data-size="15"></i></button>'+
        '<button class="ibtn" title="Löschen" data-a="del" data-id="'+i.id+'"><i class="ic" data-ic="trash" data-size="15"></i></button>'+
        '</td></tr>';
    }).join("");
    hydrateIcons(tb);
    $("#inv-empty").hidden=INV_CACHE.length>0;
    tb.querySelectorAll("[data-a]").forEach(b=>b.addEventListener("click",()=>{
      const id=+b.dataset.id, inv=INV_CACHE.find(x=>x.id===id);
      if(!inv) return;
      if(b.dataset.a==="print") printInvoice(inv);
      else if(b.dataset.a==="edit") openInvoiceModal(inv);
      else if(b.dataset.a==="paid"){
        inv.paid=!inv.paid;
        DB.put("invoices",inv).then(()=>{ toast(inv.paid?"Als bezahlt markiert":"Als offen markiert","ok"); renderInvoices(); renderDashboard(); })
          .catch(()=>toast("Speichern fehlgeschlagen","err"));
      }else{
        confirmBox("Rechnung "+inv.number+" endgültig löschen?", async()=>{
          try{ await DB.del("invoices",id); toast("Rechnung gelöscht","ok"); renderInvoices(); renderDashboard(); }
          catch(e){ toast("Löschen fehlgeschlagen","err"); }
        });
      }
    }));
  }catch(e){ console.warn(e); toast("Rechnungsliste konnte nicht geladen werden","err"); }
}

function invRowHTML(it){
  it=it||{desc:"",qty:1,price:0,vat:19};
  return '<div class="inv-row">'+
    '<input class="i-desc" type="text" placeholder="Leistung / Artikel" value="'+esc(it.desc)+'">'+
    '<input class="i-qty" type="number" min="0" step="0.01" value="'+(isFinite(it.qty)?it.qty:1)+'">'+
    '<input class="i-price" type="number" min="0" step="0.01" value="'+(isFinite(it.price)?it.price:0)+'">'+
    '<select class="i-vat"><option value="19"'+(it.vat===19?" selected":"")+'>19 %</option>'+
    '<option value="7"'+(it.vat===7?" selected":"")+'>7 %</option>'+
    '<option value="0"'+(it.vat===0?" selected":"")+'>0 %</option></select>'+
    '<span class="i-sum">'+fmtMoney((it.qty||0)*(it.price||0))+'</span>'+
    '<button class="ibtn i-del" title="Zeile entfernen" type="button"><i class="ic" data-ic="trash" data-size="14"></i></button>'+
    '</div>';
}
function recalcInv(body){
  let net=0; const vat={};
  body.querySelectorAll(".inv-row").forEach(r=>{
    const qty=parseFloat(r.querySelector(".i-qty").value)||0;
    const price=parseFloat(r.querySelector(".i-price").value)||0;
    const rate=parseFloat(r.querySelector(".i-vat").value)||0;
    const sum=qty*price;
    r.querySelector(".i-sum").textContent=fmtMoney(sum);
    net+=sum;
    vat[rate]=(vat[rate]||0)+sum*rate/100;
  });
  const vatSum=Object.values(vat).reduce((a,b)=>a+b,0);
  const parts=Object.entries(vat).filter(([,v])=>v>0.004).map(([k,v])=>k+" %: "+fmtMoney(v));
  body.querySelector("#inv-t-net").textContent=fmtMoney(net);
  body.querySelector("#inv-t-vat").textContent=fmtMoney(vatSum)+(parts.length?"  ("+parts.join(" · ")+")":"");
  body.querySelector("#inv-t-gross").textContent=fmtMoney(net+vatSum);
}

function openInvoiceModal(existing){
  const isNew=!existing;
  const contactsForSel=[];
  DB.getAll("contacts").then(cs=>{
    contactsForSel.push(...cs);
    const sel=$("#inv-contact");
    if(sel) cs.forEach(c=>{
      const o=document.createElement("option");
      o.value=c.id; o.textContent=(c.company||c.name)+(c.name&&c.company?" – "+c.name:"");
      sel.appendChild(o);
    });
  }).catch(()=>{});

  const inv=existing||{number:nextNumber(),date:todayISO(),due:addDays(14),paid:false,
    items:[{desc:"",qty:1,price:0,vat:19}],notes:"",customer:{name:"",company:"",address:""}};
  const proposed=isNew?nextNumber():null;

  const bodyHTML=
    '<div class="fgrid">'+
      '<div><label class="flabel">Rechnungsnummer</label><input type="text" id="inv-number" value="'+esc(inv.number)+'"></div>'+
      '<div><label class="flabel">Rechnungsdatum</label><input type="date" id="inv-date" value="'+esc(inv.date)+'"></div>'+
      '<div><label class="flabel">Fällig am</label><input type="date" id="inv-due" value="'+esc(inv.due||"")+'"></div>'+
      '<div><label class="flabel">Kontakt übernehmen</label><select id="inv-contact"><option value="">— frei eingeben —</option></select></div>'+
    '</div>'+
    '<div class="fgrid" style="grid-template-columns:1fr 1fr 1.4fr">'+
      '<div><label class="flabel">Empfänger / Firma *</label><input type="text" id="inv-cname" value="'+esc((inv.customer&&inv.customer.name)||"")+'"></div>'+
      '<div><label class="flabel">Ansprechpartner</label><input type="text" id="inv-ccompany" value="'+esc((inv.customer&&inv.customer.company)||"")+'"></div>'+
      '<div><label class="flabel">Adresse</label><input type="text" id="inv-caddress" value="'+esc((inv.customer&&inv.customer.address)||"")+'"></div>'+
    '</div>'+
    '<div class="inv-head"><span>Position</span><span>Menge</span><span>Einzelpreis €</span><span>MwSt</span><span style="text-align:right">Summe</span><span></span></div>'+
    '<div id="inv-items"></div>'+
    '<button class="btn" id="inv-addrow" type="button">+ Position hinzufügen</button>'+
    '<div class="inv-totals">'+
      '<div><span>Netto</span><b id="inv-t-net">0,00 €</b></div>'+
      '<div><span>MwSt</span><b id="inv-t-vat">0,00 €</b></div>'+
      '<div class="grand"><span>Rechnungsbetrag</span><span id="inv-t-gross">0,00 €</span></div>'+
    '</div>'+
    '<div class="fw" style="margin-top:12px"><label class="flabel">Bemerkungen (z. B. Zahlungsziel, Grußformel)</label>'+
    '<textarea id="inv-notes" rows="2" placeholder="Zahlbar innerhalb von 14 Tagen ohne Abzug.">'+esc(inv.notes||"")+"</textarea></div>";

  Modal.open({title:isNew?"Neue Rechnung erstellen":"Rechnung "+inv.number+" bearbeiten",wide:true,body:bodyHTML,
    mount:(body)=>{
      const itemsBox=body.querySelector("#inv-items");
      function addRow(it){
        const tpl=document.createElement("div");
        tpl.innerHTML=invRowHTML(it);
        const row=tpl.firstElementChild;
        itemsBox.appendChild(row);
        hydrateIcons(row);
        row.querySelectorAll("input,select").forEach(inp=>inp.addEventListener("input",()=>recalcInv(body)));
        row.querySelector(".i-del").addEventListener("click",()=>{
          if(itemsBox.querySelectorAll(".inv-row").length<=1){ toast("Mindestens eine Position erforderlich","err"); return; }
          row.remove(); recalcInv(body);
        });
      }
      (inv.items&&inv.items.length?inv.items:[{desc:"",qty:1,price:0,vat:19}]).forEach(addRow);
      recalcInv(body);
      body.querySelector("#inv-addrow").addEventListener("click",()=>{ addRow({desc:"",qty:1,price:0,vat:19}); recalcInv(body); });
      body.querySelector("#inv-contact").addEventListener("change",e=>{
        const c=contactsForSel.find(x=>String(x.id)===e.target.value);
        if(c){
          body.querySelector("#inv-cname").value=c.company||c.name||"";
          body.querySelector("#inv-ccompany").value=(c.company&&c.name)?c.name:"";
          body.querySelector("#inv-caddress").value=[c.street,c.zipCity].filter(Boolean).join(", ");
        }
      });
    },
    buttons:[
      {label:"Abbrechen"},
      {label:isNew?"Rechnung speichern":"Änderungen speichern",primary:true,
       onClick:async()=>{
         const number=$("#inv-number").value.trim();
         if(!number){ toast("Bitte Rechnungsnummer angeben","err"); return false; }
         const cname=$("#inv-cname").value.trim();
         if(!cname){ toast("Bitte Empfänger angeben","err"); return false; }
         const items=[];
         document.querySelectorAll("#inv-items .inv-row").forEach(r=>{
           const desc=r.querySelector(".i-desc").value.trim();
           const qty=parseFloat(r.querySelector(".i-qty").value)||0;
           const price=parseFloat(r.querySelector(".i-price").value)||0;
           const vat=parseFloat(r.querySelector(".i-vat").value)||0;
           if(desc&&(qty>0||price>0)) items.push({desc,qty,price,vat});
         });
         if(!items.length){ toast("Bitte mindestens eine gültige Position erfassen","err"); return false; }
         const rec={
           number, date:$("#inv-date").value||todayISO(), due:$("#inv-due").value||"",
           customer:{name:cname,company:$("#inv-ccompany").value.trim(),address:$("#inv-caddress").value.trim()},
           items, notes:$("#inv-notes").value.trim(), paid:!!(inv.paid)
         };
         try{
           if(isNew){
             rec.createdAt=Date.now();
             await DB.add("invoices",rec);
             if(proposed && number===proposed) await setSetting("invCounter",(S.invCounter||1)+1);
             toast("Rechnung "+number+" gespeichert","ok");
           }else{
             rec.id=inv.id; rec.createdAt=inv.createdAt||Date.now();
             await DB.put("invoices",rec);
             toast("Rechnung aktualisiert","ok");
           }
           renderInvoices(); renderDashboard(); renderHome();
         }catch(e){ console.warn(e); toast("Speichern fehlgeschlagen","err"); return false; }
       }}
    ]});
}

function printInvoice(inv){
  try{
    const c=inv.customer||{}, t=calcTotals(inv.items);
    const vatRows=Object.entries(t.vat).filter(([,v])=>v>0.004)
      .map(([k,v])=>'<div><span>MwSt '+k+' %</span><span>'+fmtMoney(v)+"</span></div>").join("");
    const comp=[S.company,S.street,S.zipCity,S.phone,S.email].filter(Boolean).join(" · ");
    $("#print-area").innerHTML=
      '<div class="p-head"><div><h1>'+esc(S.company||"Mein Unternehmen")+'</h1>'+
      '<p>'+esc([S.street,S.zipCity].filter(Boolean).join(" · "))+(S.phone?"<br>Tel.: "+esc(S.phone):"")+(S.email?"<br>"+esc(S.email):"")+'</p></div>'+
      '<div class="p-meta"><b>RECHNUNG '+esc(inv.number)+'</b><br>Rechnungsdatum: '+fmtDate(inv.date)+
      '<br>Fällig am: '+fmtDate(inv.due)+(inv.paid?'<br><i>Als bezahlt markiert</i>':"")+'</div></div>'+
      '<div class="p-to">'+esc(c.name||"")+(c.company?"<br>"+esc(c.company):"")+
      ((c.address)?("<br>"+esc(c.address).replace(/\n/g,"<br>")):"")+'</div>'+
      '<table class="p-tbl"><thead><tr><th>Pos.</th><th>Bezeichnung</th><th class="r">Menge</th>'+
      '<th class="r">Einzelpreis</th><th class="r">MwSt</th><th class="r">Summe</th></tr></thead><tbody>'+
      (inv.items||[]).map((it,i)=>'<tr><td>'+(i+1)+'</td><td>'+esc(it.desc)+'</td><td class="r">'+
      String(it.qty).replace(".",",")+'</td><td class="r">'+fmtMoney(it.price)+'</td><td class="r">'+(it.vat||0)+' %</td>'+
      '<td class="r">'+fmtMoney((it.qty||0)*(it.price||0))+"</td></tr>").join("")+
      '</tbody></table><div class="p-totals"><div><span>Nettobetrag</span><span>'+fmtMoney(t.net)+'</span></div>'+
      vatRows+'<div class="grand"><span>Gesamtbetrag</span><span>'+fmtMoney(t.gross)+'</span></div></div>'+
      (inv.notes?'<p style="margin-top:18px;font-size:12px">'+esc(inv.notes).replace(/\n/g,"<br>")+"</p>":"")+
      '<div class="p-foot">'+esc(comp||"Bitte Firmendaten unter Datei → Einstellungen pflegen.")+
      (S.iban?"<br>Bankverbindung: IBAN "+esc(S.iban)+(S.bic?" · BIC "+esc(S.bic):""):"")+
      (S.taxId?"<br>Steuernummer: "+esc(S.taxId):"")+(S.vatId?" · USt-IdNr.: "+esc(S.vatId):"")+'</div>';
    status("Druckansicht: "+inv.number);
    setTimeout(()=>window.print(),80);
  }catch(e){ console.warn(e); toast("Druckansicht fehlgeschlagen","err"); }
}

async function exportInvoicesCSV(){
  try{
    const list=await DB.getAll("invoices");
    if(!list.length){ toast("Keine Rechnungen vorhanden","info"); return; }
    const de=v=>Number(v||0).toFixed(2).replace(".",",");
    let csv="Nummer;Datum;Faellig;Empfaenger;Netto;MwSt;Brutto;Status\n";
    list.sort((a,b)=>String(a.number).localeCompare(String(b.number))).forEach(i=>{
      const t=calcTotals(i.items);
      const cell=s=>'"'+String(s==null?"":s).replace(/"/g,'""')+'"';
      csv+=[cell(i.number),fmtDate(i.date),fmtDate(i.due),cell((i.customer&&i.customer.name)||""),
            de(t.net),de(t.vatSum),de(t.gross),invStatusOf(i)].join(";")+"\n";
    });
    downloadBlob("rechnungen-"+todayISO()+".csv", new Blob(["\uFEFF"+csv],{type:"text/csv;charset=utf-8"}));
    toast("CSV exportiert","ok");
  }catch(e){ console.warn(e); toast("CSV-Export fehlgeschlagen","err"); }
}

function downloadBlob(name, blob){
  const url=URL.createObjectURL(blob);
  const a=document.createElement("a");
  a.href=url; a.download=name;
  document.body.appendChild(a); a.click(); a.remove();
  setTimeout(()=>URL.revokeObjectURL(url),5000);
}

/* ---------------- 7) KONTAKTE ---------------- */
const cState={q:"",type:"alle"};
let CONTACTS_CACHE=[];
const TYPE_BADGE={"Kunde":'<span class="badge b-blue">Kunde</span>',"Lieferant":'<span class="badge b-orange">Lieferant</span>',
                   "Dienstleister":'<span class="badge b-green">Dienstleister</span>',"Sonstige":'<span class="badge b-grey">Sonstige</span>'};

async function renderContacts(){
  try{
    CONTACTS_CACHE=await DB.getAll("contacts");
    const q=cState.q.toLowerCase();
    const list=[...CONTACTS_CACHE]
      .filter(c=>cState.type==="alle"||c.type===cState.type)
      .filter(c=>!q||[c.name,c.company,c.email,c.phone].some(v=>(v||"").toLowerCase().includes(q)))
      .sort((a,b)=>(a.name||"").localeCompare(b.name||"","de"));
    $("#c-count").textContent=list.length+" von "+CONTACTS_CACHE.length+" Kontakten";
    const tb=$("#c-list-body");
    tb.innerHTML=list.map(c=>
      '<tr><td>'+(TYPE_BADGE[c.type]||TYPE_BADGE["Sonstige"])+'</td><td><b>'+esc(c.name||"–")+'</b></td>'+
      '<td>'+esc(c.company||"–")+'</td><td>'+(c.email?'<a href="mailto:'+esc(c.email)+'">'+esc(c.email)+"</a>":"–")+'</td>'+
      '<td>'+esc(c.phone||"–")+'</td><td style="white-space:nowrap">'+
      '<button class="ibtn" title="Bearbeiten" data-a="edit" data-id="'+c.id+'"><i class="ic" data-ic="edit" data-size="15"></i></button>'+
      '<button class="ibtn" title="Löschen" data-a="del" data-id="'+c.id+'"><i class="ic" data-ic="trash" data-size="15"></i></button>'+
      '</td></tr>').join("");
    hydrateIcons(tb);
    $("#c-empty").hidden=CONTACTS_CACHE.length>0;
    tb.querySelectorAll("[data-a]").forEach(b=>b.addEventListener("click",()=>{
      const c=CONTACTS_CACHE.find(x=>x.id===+b.dataset.id);
      if(!c) return;
      if(b.dataset.a==="edit") openContactModal(c);
      else confirmBox("Kontakt „"+(c.name||"")+"“ endgültig löschen?", async()=>{
        try{ await DB.del("contacts",c.id); toast("Kontakt gelöscht","ok"); renderContacts(); renderHome(); }
        catch(e){ toast("Löschen fehlgeschlagen","err"); }
      });
    }));
  }catch(e){ console.warn(e); toast("Kontaktliste konnte nicht geladen werden","err"); }
}

function openContactModal(existing){
  const isNew=!existing;
  const c=existing||{type:"Kunde",name:"",company:"",email:"",phone:"",street:"",zipCity:"",notes:""};
  const bodyHTML=
    '<div class="fgrid" style="grid-template-columns:1fr 1fr">'+
      '<div><label class="flabel">Typ</label><select id="ct-type">'+
        ["Kunde","Lieferant","Dienstleister","Sonstige"].map(t=>'<option'+(c.type===t?" selected":"")+'>'+t+"</option>").join("")+'</select></div>'+
      '<div><label class="flabel">Name *</label><input type="text" id="ct-name" value="'+esc(c.name)+'"></div>'+
      '<div><label class="flabel">Firma</label><input type="text" id="ct-company" value="'+esc(c.company||"")+'"></div>'+
      '<div><label class="flabel">Telefon</label><input type="text" id="ct-phone" value="'+esc(c.phone||"")+'"></div>'+
      '<div><label class="flabel">E-Mail</label><input type="email" id="ct-email" value="'+esc(c.email||"")+'"></div>'+
      '<div><label class="flabel">Straße / Nr.</label><input type="text" id="ct-street" value="'+esc(c.street||"")+'"></div>'+
      '<div><label class="flabel">PLZ Ort</label><input type="text" id="ct-zip" value="'+esc(c.zipCity||"")+'"></div>'+
    '</div>'+
    '<div class="fw"><label class="flabel">Notizen</label><textarea id="ct-notes" rows="3">'+esc(c.notes||"")+'</textarea></div>';
  Modal.open({title:isNew?"Neuen Kontakt anlegen":"Kontakt bearbeiten",body:bodyHTML,
    buttons:[
      {label:"Abbrechen"},
      {label:isNew?"Kontakt anlegen":"Speichern",primary:true,onClick:async()=>{
        const name=$("#ct-name").value.trim();
        if(!name){ toast("Bitte einen Namen angeben","err"); return false; }
        const rec={type:$("#ct-type").value,name,company:$("#ct-company").value.trim(),
          email:$("#ct-email").value.trim(),phone:$("#ct-phone").value.trim(),
          street:$("#ct-street").value.trim(),zipCity:$("#ct-zip").value.trim(),notes:$("#ct-notes").value.trim()};
        try{
          if(isNew){ rec.createdAt=Date.now(); await DB.add("contacts",rec); toast("Kontakt angelegt","ok"); }
          else{ rec.id=c.id; rec.createdAt=c.createdAt||Date.now(); await DB.put("contacts",rec); toast("Kontakt gespeichert","ok"); }
          renderContacts(); renderHome();
        }catch(e){ toast("Speichern fehlgeschlagen","err"); return false; }
      }}
    ]});
}

/* ---------------- 8) TOOLS ---------------- */
function setTool(t){
  $$("#tool-nav button").forEach(b=>b.classList.toggle("on",b.dataset.tool===t));
  $$(".tool-panel").forEach(p=>p.classList.toggle("active",p.id==="tool-"+t));
}

/* ---- Taschenrechner ---- */
const calc={expr:""};
function calcKey(k){
  if(k==="C") calc.expr="";
  else if(k==="⌫") calc.expr=calc.expr.slice(0,-1);
  else if(k==="="){
    try{
      const s=calc.expr.replace(/,/g,".");
      if(!s || !/^[\d+\-*/(). ]*$/.test(s)) throw new Error("ungültig");
      const v=Function('"use strict";return ('+s+');')();
      if(typeof v!=="number"||!isFinite(v)) throw new Error("ungültig");
      calc.expr=String(Math.round(v*1e10)/1e10).replace(".",",");
    }catch(e){ toast("Ungültiger Ausdruck","err"); return; }
  }else{
    if(calc.expr==="" && "+*/),".includes(k)) return;
    if(k==="," && /\,\d*$/.test(calc.expr.split(/[+\-*/()]/).pop()||"")) return; // nur ein Komma pro Zahl
    calc.expr+=k;
  }
  $("#calc-display").value=calc.expr||"0";
}

/* ---- Umrechner ---- */
const UNITS={
  "Länge":{m:1,km:1000,cm:0.01,mm:0.001,"Meile (mi)":1609.344,"Fuß (ft)":0.3048,"Zoll (in)":0.0254},
  "Gewicht":{kg:1,g:0.001,t:1000,"Pfund (lb)":0.45359237,"Unze (oz)":0.028349523},
  "Temperatur":{"°C":"T","°F":"T","K":"T"},
  "Daten":{B:1,KB:1024,MB:1048576,GB:1073741824,TB:1099511627776},
  "Geschwindigkeit":{"m/s":1,"km/h":1/3.6,"mph":0.44704,"Knoten":0.514444},
  "Zeit":{s:1,min:60,h:3600,Tag:86400,Woche:604800}
};
function tempToC(v,u){ return u==="°C"?v:(u==="°F"?(v-32)*5/9:v-273.15); }
function tempFromC(v,u){ return u==="°C"?v:(u==="°F"?v*9/5+32:v+273.15); }
function fillConvUnits(){
  const cat=$("#conv-cat").value, u=UNITS[cat];
  const opts=Object.keys(u).map(k=>'<option>'+esc(k)+"</option>").join("");
  $("#conv-from").innerHTML=opts; $("#conv-to").innerHTML=opts;
  const keys=Object.keys(u);
  if(keys.length>1) $("#conv-to").selectedIndex=1;
  convCalc();
}
function convCalc(){
  try{
    const cat=$("#conv-cat").value, u=UNITS[cat];
    const from=$("#conv-from").value, to=$("#conv-to").value;
    const val=deNum($("#conv-val").value);
    if(isNaN(val)){ $("#conv-result").textContent="–"; return; }
    let res;
    if(cat==="Temperatur") res=tempFromC(tempToC(val,from),to);
    else res=val*u[from]/u[to];
    $("#conv-result").textContent=res.toLocaleString("de-DE",{maximumFractionDigits:6})+" "+to;
  }catch(e){ $("#conv-result").textContent="–"; }
}
function vatCalc(){
  const net=deNum($("#vat-net").value), rate=parseFloat($("#vat-rate").value)||0;
  if(isNaN(net)){ $("#vat-amt").textContent="–"; $("#vat-gross").textContent="–"; return; }
  const amt=net*rate/100;
  $("#vat-amt").textContent=fmtMoney(amt);
  $("#vat-gross").textContent=fmtMoney(net+amt);
}
function pctCalc(){
  const b=deNum($("#pct-base").value), p=deNum($("#pct-val").value);
  if(isNaN(b)||isNaN(p)){ $("#pct-res").textContent="–"; $("#pct-plus").textContent="–"; return; }
  const w=b*p/100;
  $("#pct-res").textContent=w.toLocaleString("de-DE",{maximumFractionDigits:4});
  $("#pct-plus").textContent=(b+w).toLocaleString("de-DE",{maximumFractionDigits:4});
}

/* ---- Notizen ---- */
let NOTES_CACHE=[], selNote=null;
async function renderNotes(){
  try{
    NOTES_CACHE=(await DB.getAll("notes")).sort((a,b)=>(b.updatedAt||0)-(a.updatedAt||0));
    $("#notes-list").innerHTML=NOTES_CACHE.length?NOTES_CACHE.map(n=>
      '<div class="note-item'+(selNote===n.id?" sel":"")+'" data-id="'+n.id+'"><b>'+esc(n.title||"Ohne Titel")+'</b>'+
      '<small>'+fmtDateTime(n.updatedAt||n.createdAt)+"</small></div>").join("")
      :'<div class="empty" style="padding:20px"><b>Keine Notizen</b>Legen Sie mit „+ Neu“ die erste Notiz an.</div>';
    $("#notes-list").querySelectorAll(".note-item").forEach(el=>el.addEventListener("click",()=>{
      selNote=+el.dataset.id;
      const n=NOTES_CACHE.find(x=>x.id===selNote);
      if(n){ $("#note-title").value=n.title||""; $("#note-body").value=n.body||"";
             $("#note-meta").textContent="Zuletzt gespeichert: "+fmtDateTime(n.updatedAt||n.createdAt); }
      renderNotesListSel();
    }));
    if(selNote===null && NOTES_CACHE.length===0){ $("#note-title").value=""; $("#note-body").value=""; $("#note-meta").textContent=""; }
  }catch(e){ console.warn(e); }
}
function renderNotesListSel(){
  $$("#notes-list .note-item").forEach(el=>el.classList.toggle("sel",+el.dataset.id===selNote));
}
async function saveNote(){
  try{
    const title=$("#note-title").value.trim()||"Ohne Titel";
    const bodyTxt=$("#note-body").value;
    if(selNote){
      const n=NOTES_CACHE.find(x=>x.id===selNote);
      if(!n){ toast("Notiz nicht gefunden","err"); return; }
      n.title=title; n.body=bodyTxt; n.updatedAt=Date.now();
      await DB.put("notes",n);
    }else{
      const id=await DB.add("notes",{title,body:bodyTxt,createdAt:Date.now(),updatedAt:Date.now()});
      selNote=id;
    }
    toast("Notiz gespeichert","ok");
    renderNotes();
  }catch(e){ toast("Speichern fehlgeschlagen","err"); }
}

/* ---- Aufgaben ---- */
let TASKS_CACHE=[], taskFilter="offen";
async function renderTasks(){
  try{
    TASKS_CACHE=await DB.getAll("tasks");
    const today=todayISO();
    let list=[...TASKS_CACHE];
    if(taskFilter==="offen") list=list.filter(t=>!t.done);
    else if(taskFilter==="erledigt") list=list.filter(t=>t.done);
    list.sort((a,b)=>(a.done-b.done)||String(a.due||"9999").localeCompare(String(b.due||"9999")));
    const PRIO={hoch:'<span class="badge b-red">Hoch</span>',normal:'<span class="badge b-blue">Normal</span>',niedrig:'<span class="badge b-grey">Niedrig</span>'};
    $("#tasks-list").innerHTML=list.length?list.map(t=>{
      const overdue=t.due&&t.due<today&&!t.done;
      return '<div class="task-row'+(t.done?" done":"")+(overdue?" overdue":"")+'">'+
        '<input type="checkbox" data-tg="'+t.id+'"'+(t.done?" checked":"")+'> '+
        '<span class="t-title" style="flex:1">'+esc(t.title)+'</span>'+
        (PRIO[t.prio]||"")+' <span class="t-due" style="font-size:11.5px;color:var(--ink-soft)">'+(t.due?fmtDate(t.due):"kein Termin")+'</span>'+
        '<button class="ibtn" data-tdel="'+t.id+'" title="Löschen"><i class="ic" data-ic="trash" data-size="14"></i></button></div>';
    }).join(""):'<div class="empty" style="padding:18px"><b>Keine Aufgaben</b>'+(taskFilter==="erledigt"?"Noch nichts erledigt.":"Alles erledigt – großartig! ✔")+'</div>';
    hydrateIcons($("#tasks-list"));
    $("#tasks-list").querySelectorAll("[data-tg]").forEach(cb=>cb.addEventListener("change",async()=>{
      const t=TASKS_CACHE.find(x=>x.id===+cb.dataset.tg);
      if(t){ t.done=cb.checked; try{ await DB.put("tasks",t); renderTasks(); renderDashboard(); }catch(e){ toast("Speichern fehlgeschlagen","err"); } }
    }));
    $("#tasks-list").querySelectorAll("[data-tdel]").forEach(b=>b.addEventListener("click",async()=>{
      try{ await DB.del("tasks",+b.dataset.tdel); toast("Aufgabe gelöscht","ok"); renderTasks(); renderDashboard(); }
      catch(e){ toast("Löschen fehlgeschlagen","err"); }
    }));
  }catch(e){ console.warn(e); }
}
async function addTask(){
  const title=$("#task-input").value.trim();
  if(!title){ toast("Bitte Aufgabentext eingeben","err"); return; }
  try{
    await DB.add("tasks",{title,due:$("#task-due").value||"",prio:$("#task-prio").value,done:false,createdAt:Date.now()});
    $("#task-input").value=""; $("#task-due").value="";
    toast("Aufgabe hinzugefügt","ok");
    renderTasks(); renderDashboard();
  }catch(e){ toast("Speichern fehlgeschlagen","err"); }
}

/* ---------------- KALENDER & WIEDERVORLAGEN ---------------- */
let calView=new Date(); calView.setDate(1);
let selDay=todayISO();

async function renderCalendar(){
  try{
    const evs=await DB.getAll("events");
    const map={};
    evs.forEach(e=>{ (map[e.date]=map[e.date]||[]).push(e); });
    const y=calView.getFullYear(), m=calView.getMonth();
    $("#cal-title").textContent=calView.toLocaleDateString("de-DE",{month:"long",year:"numeric"});
    const off=(new Date(y,m,1).getDay()+6)%7;
    const start=new Date(y,m,1-off);
    const today=todayISO();
    let html='<div class="cal-head">'+["Mo","Di","Mi","Do","Fr","Sa","So"].map(d=>"<div>"+d+"</div>").join("")+'</div><div class="cal-grid">';
    for(let i=0;i<42;i++){
      const d=new Date(start); d.setDate(start.getDate()+i);
      const iso=toISO(d);
      const cls=["cal-cell"];
      if(d.getMonth()!==m) cls.push("other");
      if(iso===today) cls.push("today");
      if(iso===selDay) cls.push("sel");
      const list=(map[iso]||[]).sort((a,b)=>String(a.time||"").localeCompare(String(b.time||"")));
      html+='<div class="'+cls.join(" ")+'" data-date="'+iso+'"><span class="cal-num">'+d.getDate()+'</span>'+
        list.slice(0,2).map(e=>'<span class="cal-chip" title="'+esc(e.title)+'">'+(e.time?esc(e.time)+" ":"")+esc(e.title)+"</span>").join("")+
        (list.length>2?'<span class="cal-more">+'+(list.length-2)+" weitere</span>":"")+'</div>';
    }
    html+='</div>';
    $("#cal-wrap").innerHTML=html;
    $("#cal-wrap").querySelectorAll(".cal-cell").forEach(c=>c.addEventListener("click",()=>{
      selDay=c.dataset.date;
      renderCalendar();
    }));
    renderDayEvents();
  }catch(e){ console.warn(e); }
}

async function renderDayEvents(){
  try{
    const d=new Date(selDay+"T00:00:00");
    $("#day-title").textContent="Termine am "+d.toLocaleDateString("de-DE",{weekday:"long",day:"numeric",month:"long"});
    const evs=(await DB.getAll("events")).filter(e=>e.date===selDay)
      .sort((a,b)=>String(a.time||"").localeCompare(String(b.time||"")));
    $("#day-events").innerHTML=evs.length?evs.map(e=>
      '<div class="task-row"><span class="badge b-blue" style="min-width:52px;text-align:center">'+esc(e.time||"–:–")+'</span>'+
      '<div style="flex:1"><b>'+esc(e.title)+'</b>'+(e.notes?'<br><small style="color:var(--ink-soft)">'+esc(e.notes)+"</small>":"")+'</div>'+
      '<button class="ibtn" data-edel="'+e.id+'" title="Termin löschen"><i class="ic" data-ic="trash" data-size="14"></i></button></div>').join("")
      :'<p style="color:var(--ink-soft);padding:8px 0">Keine Termine an diesem Tag.</p>';
    hydrateIcons($("#day-events"));
    $("#day-events").querySelectorAll("[data-edel]").forEach(b=>b.addEventListener("click",async()=>{
      try{ await DB.del("events",+b.dataset.edel); toast("Termin gelöscht","ok"); renderCalendar(); renderHome(); }
      catch(e){ toast("Löschen fehlgeschlagen","err"); }
    }));
  }catch(e){ console.warn(e); }
}

function openEventModal(dateISO){
  Modal.open({title:"Neuen Termin anlegen",
    body:'<div class="fw"><label class="flabel">Titel *</label><input type="text" id="ev-title" style="width:100%"></div>'+
      '<div class="fgrid" style="grid-template-columns:1fr 1fr">'+
      '<div><label class="flabel">Datum</label><input type="date" id="ev-date" value="'+esc(dateISO||selDay)+'"></div>'+
      '<div><label class="flabel">Uhrzeit</label><input type="time" id="ev-time"></div></div>'+
      '<div class="fw"><label class="flabel">Notizen</label><textarea id="ev-notes" rows="2" style="width:100%"></textarea></div>',
    buttons:[{label:"Abbrechen"},{label:"Termin speichern",primary:true,onClick:async()=>{
      const title=$("#ev-title").value.trim();
      if(!title){ toast("Bitte Titel angeben","err"); return false; }
      try{
        await DB.add("events",{title,date:$("#ev-date").value||todayISO(),time:$("#ev-time").value||"",notes:$("#ev-notes").value.trim()});
        selDay=$("#ev-date").value||selDay;
        toast("Termin gespeichert","ok");
        renderCalendar(); renderHome(); renderDashboard();
      }catch(e){ toast("Speichern fehlgeschlagen","err"); return false; }
    }}]});
}

async function renderReminders(){
  try{
    const showDone=$("#rem-show-done").checked;
    const today=todayISO();
    let list=(await DB.getAll("reminders")).filter(r=>showDone||!r.done)
      .sort((a,b)=>(a.done-b.done)||String(a.date||"").localeCompare(String(b.date||"")));
    $("#rem-list").innerHTML=list.length?list.map(r=>{
      const overdue=r.date&&r.date<today&&!r.done;
      const isToday=r.date===today&&!r.done;
      return '<div class="task-row'+(r.done?" done":"")+'">'+
        '<input type="checkbox" data-rtg="'+r.id+'"'+(r.done?" checked":"")+'> '+
        '<div style="flex:1"><b>'+esc(r.title)+'</b>'+(r.notes?'<br><small style="color:var(--ink-soft)">'+esc(r.notes)+"</small>":"")+'</div>'+
        (overdue?'<span class="badge b-red">Überfällig</span>':isToday?'<span class="badge b-orange">Heute</span>':'<span class="badge b-grey">'+fmtDate(r.date)+'</span>')+
        ' <button class="ibtn" data-rdel="'+r.id+'" title="Löschen"><i class="ic" data-ic="trash" data-size="14"></i></button></div>';
    }).join(""):'<div class="empty" style="padding:18px"><b>Keine Wiedervorlagen</b>Legen Sie Fristen und Erinnerungen mit „+ Neu“ an.</div>';
    hydrateIcons($("#rem-list"));
    $("#rem-list").querySelectorAll("[data-rtg]").forEach(cb=>cb.addEventListener("change",async()=>{
      try{
        const r=await DB.get("reminders",+cb.dataset.rtg);
        if(r){ r.done=cb.checked; await DB.put("reminders",r); renderReminders(); renderDashboard(); }
      }catch(e){ toast("Speichern fehlgeschlagen","err"); }
    }));
    $("#rem-list").querySelectorAll("[data-rdel]").forEach(b=>b.addEventListener("click",async()=>{
      try{ await DB.del("reminders",+b.dataset.rdel); toast("Wiedervorlage gelöscht","ok"); renderReminders(); }
      catch(e){ toast("Löschen fehlgeschlagen","err"); }
    }));
  }catch(e){ console.warn(e); }
}

function openReminderModal(){
  Modal.open({title:"Neue Wiedervorlage",
    body:'<div class="fw"><label class="flabel">Betreff *</label><input type="text" id="rm-title" style="width:100%" placeholder="z. B. Rückruf Kunde Meier"></div>'+
      '<div class="fw"><label class="flabel">Datum</label><input type="date" id="rm-date" value="'+todayISO()+'" style="width:100%"></div>'+
      '<div class="fw"><label class="flabel">Notiz</label><textarea id="rm-notes" rows="2" style="width:100%"></textarea></div>',
    buttons:[{label:"Abbrechen"},{label:"Anlegen",primary:true,onClick:async()=>{
      const title=$("#rm-title").value.trim();
      if(!title){ toast("Bitte Betreff angeben","err"); return false; }
      try{
        await DB.add("reminders",{title,date:$("#rm-date").value||todayISO(),notes:$("#rm-notes").value.trim(),done:false,createdAt:Date.now()});
        toast("Wiedervorlage angelegt","ok");
        renderReminders(); renderDashboard(); renderHome();
      }catch(e){ toast("Speichern fehlgeschlagen","err"); return false; }
    }}]});
}

/* ---------------- EINSTELLUNGEN & BACKUP ---------------- */
function openSettings(){
  Modal.open({title:"Einstellungen – Firmendaten & Nummernkreis",wide:true,
    body:'<div class="fgrid">'+
      '<div><label class="flabel">Firma / Name</label><input type="text" id="set-company" value="'+esc(S.company)+'"></div>'+
      '<div><label class="flabel">Straße / Nr.</label><input type="text" id="set-street" value="'+esc(S.street)+'"></div>'+
      '<div><label class="flabel">PLZ Ort</label><input type="text" id="set-zip" value="'+esc(S.zipCity)+'"></div>'+
      '<div><label class="flabel">Telefon</label><input type="text" id="set-phone" value="'+esc(S.phone)+'"></div>'+
      '<div><label class="flabel">E-Mail</label><input type="text" id="set-email" value="'+esc(S.email)+'"></div>'+
      '<div><label class="flabel">Steuernummer</label><input type="text" id="set-tax" value="'+esc(S.taxId)+'"></div>'+
      '<div><label class="flabel">USt-IdNr.</label><input type="text" id="set-vatid" value="'+esc(S.vatId)+'"></div>'+
      '<div><label class="flabel">IBAN</label><input type="text" id="set-iban" value="'+esc(S.iban)+'"></div>'+
      '<div><label class="flabel">BIC</label><input type="text" id="set-bic" value="'+esc(S.bic)+'"></div>'+
      '<div><label class="flabel">Rechnungs-Präfix</label><input type="text" id="set-prefix" value="'+esc(S.prefix)+'"></div>'+
    '</div>'+
    '<p style="font-size:11.5px;color:var(--ink-soft)">Die nächste Rechnungsnummer lautet: <b>'+esc(nextNumber())+'</b>. '+
    'Dokumente (Dateien) sind aufgrund ihrer Größe nicht Teil des Backups.</p>',
    buttons:[{label:"Abbrechen"},{label:"Speichern",primary:true,onClick:async()=>{
      try{
        await setSetting("company",$("#set-company").value.trim());
        await setSetting("street",$("#set-street").value.trim());
        await setSetting("zipCity",$("#set-zip").value.trim());
        await setSetting("phone",$("#set-phone").value.trim());
        await setSetting("email",$("#set-email").value.trim());
        await setSetting("taxId",$("#set-tax").value.trim());
        await setSetting("vatId",$("#set-vatid").value.trim());
        await setSetting("iban",$("#set-iban").value.trim());
        await setSetting("bic",$("#set-bic").value.trim());
        await setSetting("prefix",$("#set-prefix").value.trim()||"RE-");
        toast("Einstellungen gespeichert","ok");
      }catch(e){ toast("Speichern fehlgeschlagen","err"); return false; }
    }}]});
}

async function exportBackup(){
  try{
    status("Backup wird erstellt…");
    const data={app:"buero-management",exportedAt:new Date().toISOString(),settings:{}};
    const sets=await DB.getAll("settings");
    sets.forEach(s=>data.settings[s.key]=s.value);
    for(const st of ["invoices","contacts","tasks","notes","reminders","events"]) data[st]=await DB.getAll(st);
    downloadBlob("buero-management-backup-"+todayISO()+".json",new Blob([JSON.stringify(data,null,2)],{type:"application/json"}));
    toast("Backup exportiert","ok");
    status("Backup exportiert");
  }catch(e){ console.warn(e); toast("Backup konnte nicht erstellt werden","err"); }
}

async function importBackup(file){
  try{
    const txt=await file.text();
    const data=JSON.parse(txt);
    if(!data || typeof data!=="object" || data.app!=="buero-management"){
      toast("Ungültige Backup-Datei","err"); return;
    }
    confirmBox("Beim Import werden Kontakte, Rechnungen, Aufgaben, Notizen, Termine und Einstellungen durch das Backup ersetzt. Fortfahren?", async()=>{
      try{
        for(const st of ["invoices","contacts","tasks","notes","reminders","events"]) await DB.clear(st);
        const sets=await DB.getAll("settings");
        for(const s of sets) await DB.del("settings",s.key);
        for(const k of Object.keys(data.settings||{})) await DB.put("settings",{key:k,value:data.settings[k]});
        for(const st of ["invoices","contacts","tasks","notes","reminders","events"]){
          for(const rec of (data[st]||[])){ const c=Object.assign({},rec); delete c.id; await DB.add(st,c); }
        }
        await loadSettings();
        await renderAll();
        toast("Import erfolgreich abgeschlossen","ok");
      }catch(e){ console.warn(e); toast("Import fehlgeschlagen","err"); }
    });
  }catch(e){ toast("Datei konnte nicht gelesen werden (kein gültiges JSON?)","err"); }
}

/* ---------------- VERDRAHTUNG & INIT ---------------- */
function wireUI(){
  // Tabs
  $$(".tab").forEach(t=>t.addEventListener("click",()=>setTab(t.dataset.tab)));
  // Kacheln Startseite
  $$("[data-goto]").forEach(el=>el.addEventListener("click",()=>setTab(el.dataset.goto)));
  $$("[data-act-tile]").forEach(el=>el.addEventListener("click",()=>{
    if(el.dataset.actTile==="open-settings") openSettings();
    else{ exportBackup(); }
  }));

  // Ribbon-Aktionen (zentral)
  $$("[data-act]").forEach(b=>b.addEventListener("click",()=>{
    const a=b.dataset.act;
    switch(a){
      case "refresh-all": renderAll().then(()=>toast("Ansichten aktualisiert","ok")); break;
      case "new-doc": $("#file-input").click(); break;
      case "doc-upload": $("#file-input").click(); break;
      case "new-invoice": openInvoiceModal(null); break;
      case "new-contact": openContactModal(null); break;
      case "new-task": setTab("tools"); setTool("tasks"); $("#task-input").focus(); break;
      case "open-settings": openSettings(); break;
      case "export-backup": exportBackup(); break;
      case "render-docs": renderDocs(); break;
      case "render-inv": renderInvoices(); break;
      case "inv-csv": exportInvoicesCSV(); break;
      case "render-contacts": renderContacts(); break;
      case "render-terms": renderCalendar(); renderReminders(); break;
      case "ocr-pick": $("#ocr-input").click(); break;
      case "ocr-from-docs": ocrFromDocs(); break;
      case "ocr-reset":
        if(ocr.url){ try{ URL.revokeObjectURL(ocr.url); }catch(e){} }
        ocr.file=null; ocr.url=null;
        $("#ocr-img").innerHTML='<span style="color:var(--ink-soft)">Noch kein Bild geladen</span>';
        $("#ocr-fname").textContent=""; $("#ocr-result").value=""; $("#ocr-count").textContent="0 Zeichen";
        break;
      case "ocr-run": runOCR(); break;
      case "ocr-copy": copyOCR(); break;
      case "cal-today": calView=new Date(); calView.setDate(1); selDay=todayISO(); renderCalendar(); break;
      case "new-event": openEventModal(selDay); break;
      case "new-reminder": openReminderModal(); break;
    }
  }));

  // Dokumenten-Filter
  $$("[data-docfilter]").forEach(b=>b.addEventListener("click",()=>{
    docsState.filter=b.dataset.docfilter;
    $$("[data-docfilter]").forEach(x=>x.classList.toggle("on",x===b));
    renderDocs();
  }));
  // OCR-Sprache
  $$("[data-ocrlang]").forEach(b=>b.addEventListener("click",()=>{
    ocr.lang=b.dataset.ocrlang;
    $$("[data-ocrlang]").forEach(x=>x.classList.toggle("on",x===b));
    status("OCR-Sprache: "+(ocr.lang==="deu"?"Deutsch":"Englisch"));
  }));
  // Kontakte-Filter
  $$("[data-cfilter]").forEach(b=>b.addEventListener("click",()=>{
    cState.type=b.dataset.cfilter;
    $$("[data-cfilter]").forEach(x=>x.classList.toggle("on",x===b));
    renderContacts();
  }));
  // Tools-Umschalter
  $$("[data-tool]").forEach(b=>b.addEventListener("click",()=>{
    setTab("tools"); setTool(b.dataset.tool);
  }));

  // Datei-Upload
  $("#file-input").addEventListener("change",e=>{ uploadFiles(e.target.files); e.target.value=""; });
  $("#ocr-input").addEventListener("change",e=>{ if(e.target.files[0]) ocrSetFile(e.target.files[0]); e.target.value=""; });
  $("#import-input").addEventListener("change",e=>{ if(e.target.files[0]) importBackup(e.target.files[0]); e.target.value=""; });

  // Dropzone (gesamte Dokumentenansicht)
  const dz=$("#dropzone"), docsView=$('[data-view="docs"]');
  ["dragenter","dragover"].forEach(ev=>docsView.addEventListener(ev,e=>{ e.preventDefault(); dz.classList.add("drag"); }));
  ["dragleave","drop"].forEach(ev=>docsView.addEventListener(ev,e=>{ e.preventDefault(); if(ev==="drop"||e.target===docsView) dz.classList.remove("drag"); }));
  docsView.addEventListener("drop",e=>{ e.preventDefault(); dz.classList.remove("drag"); uploadFiles(e.dataTransfer.files); });
  dz.addEventListener("click",()=>$("#file-input").click());

  // Suche
  $("#doc-search").addEventListener("input",e=>{ docsState.q=e.target.value; renderDocs(); });
  $("#inv-search").addEventListener("input",e=>{ invState.q=e.target.value; renderInvoices(); });
  $("#inv-status").addEventListener("change",e=>{ invState.status=e.target.value; renderInvoices(); });
  $("#c-search").addEventListener("input",e=>{ cState.q=e.target.value; renderContacts(); });

  // Taschenrechner
  $$("#tool-calc .keypad button").forEach(b=>b.addEventListener("click",()=>calcKey(b.dataset.k)));
  $("#calc-box").addEventListener("keydown",e=>{
    const map={Enter:"=",Backspace:"⌫",Escape:"C",".":",","x":"*","X":"*",":":"/",",":","};
    let k=null;
    if(/^[0-9+\-*/()]$/.test(e.key)) k=e.key;
    else if(map[e.key]!==undefined) k=map[e.key];
    if(k!==null){ e.preventDefault(); calcKey(k); }
  });

  // Umrechner / MwSt / Prozent
  $("#conv-cat").innerHTML=Object.keys(UNITS).map(k=>"<option>"+esc(k)+"</option>").join("");
  $("#conv-cat").addEventListener("change",fillConvUnits);
  ["conv-val","conv-from","conv-to"].forEach(id=>$("#"+id).addEventListener("input",convCalc));
  ["conv-from","conv-to"].forEach(id=>$("#"+id).addEventListener("change",convCalc));
  $("#conv-swap").addEventListener("click",()=>{
    const f=$("#conv-from").selectedIndex;
    $("#conv-from").selectedIndex=$("#conv-to").selectedIndex;
    $("#conv-to").selectedIndex=f;
    convCalc();
  });
  ["vat-net","vat-rate"].forEach(id=>$("#"+id).addEventListener("input",vatCalc));
  ["pct-base","pct-val"].forEach(id=>$("#"+id).addEventListener("input",pctCalc));

  // Notizen
  $("#note-new").addEventListener("click",()=>{ selNote=null; $("#note-title").value=""; $("#note-body").value=""; $("#note-meta").textContent="Neue Notiz"; renderNotesListSel(); $("#note-title").focus(); });
  $("#note-save").addEventListener("click",saveNote);
  $("#note-del").addEventListener("click",()=>{
    if(!selNote){ toast("Keine Notiz ausgewählt","info"); return; }
    confirmBox("Diese Notiz endgültig löschen?", async()=>{
      try{ await DB.del("notes",selNote); selNote=null; $("#note-title").value=""; $("#note-body").value=""; $("#note-meta").textContent=""; toast("Notiz gelöscht","ok"); renderNotes(); }
      catch(e){ toast("Löschen fehlgeschlagen","err"); }
    });
  });

  // Aufgaben
  $("#task-add").addEventListener("click",addTask);
  $("#task-input").addEventListener("keydown",e=>{ if(e.key==="Enter") addTask(); });
  $$("[data-tf]").forEach(b=>b.addEventListener("click",()=>{
    taskFilter=b.dataset.tf;
    $$("[data-tf]").forEach(x=>x.classList.toggle("on",x===b));
    renderTasks();
  }));

  // Kalender
  $("#cal-prev").addEventListener("click",()=>{ calView.setMonth(calView.getMonth()-1); renderCalendar(); });
  $("#cal-next").addEventListener("click",()=>{ calView.setMonth(calView.getMonth()+1); renderCalendar(); });
  $("#cal-today").addEventListener("click",()=>{ calView=new Date(); calView.setDate(1); selDay=todayISO(); renderCalendar(); });
  $("#btn-new-event").addEventListener("click",()=>openEventModal(selDay));
  $("#btn-new-rem").addEventListener("click",openReminderModal);
  $("#rem-show-done").addEventListener("change",renderReminders);

  // Escape schließt Modals
  document.addEventListener("keydown",e=>{
    if(e.key==="Escape"){
      const ovs=$$(".m-ov");
      for(let i=ovs.length-1;i>=0;i--){
        if(!ovs[i].classList.contains("m-stay")){ Modal.close(ovs[i]); break; }
      }
    }
  });
}

/* ---------------- INIT ---------------- */
(async function init(){
  try{
    hydrateIcons();
    wireUI();
    const now=new Date();
    $("#tb-date").textContent=now.toLocaleDateString("de-DE",{weekday:"short",day:"2-digit",month:"2-digit",year:"numeric"});
    $("#sb-date").textContent=now.toLocaleDateString("de-DE");
    $("#dash-sub").textContent="Heute ist "+now.toLocaleDateString("de-DE",{weekday:"long",day:"numeric",month:"long",year:"numeric"})+" – hier ist Ihr Überblick.";

    try{
      await DB.open();
      $("#sb-db").textContent="Datenbank verbunden";
    }catch(e){
      console.warn(e);
      $("#fatal").style.display="flex";
      $("#sb-db").textContent="Datenbankfehler";
      return;
    }

    await loadSettings();

    // OCR-Verfügbarkeit anzeigen
    $("#ocr-avail").textContent=(typeof Tesseract!=="undefined")?"OCR-Modul bereit (Tesseract.js)":"OCR-Modul nicht geladen – Internetverbindung nötig";
    $("#ocr-avail").className="badge "+((typeof Tesseract!=="undefined")?"b-green":"b-orange");

    setTab("datei");
    await renderAll();
    fillConvUnits();
    vatCalc(); pctCalc();
    status("Bereit");
  }catch(e){
    console.warn(e);
    try{ toast("Anwendung konnte nicht gestartet werden","err"); }catch(e2){}
    $("#fatal").style.display="flex";
  }
})();
</script>
</body>
</html>
