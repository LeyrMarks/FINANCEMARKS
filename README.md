<!DOCTYPE html>
<html lang="pt-BR" data-theme="light">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1.0"/>
<title>Dashboard Financeiro</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Sans:ital,wght@0,300;0,400;0,500;0,600;1,400&display=swap" rel="stylesheet"/>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<style>
/* ============================================================ VARIABLES */
:root {
  --primary:#2563EB; --primary-light:#DBEAFE; --primary-dark:#1D4ED8;
  --secondary:#16A34A; --secondary-light:#DCFCE7;
  --accent:#7C3AED; --accent-light:#EDE9FE;
  --danger:#DC2626; --danger-light:#FEE2E2;
  --warning:#D97706; --warning-light:#FEF3C7;
  --bg:#F3F4F6; --card:#FFFFFF; --text:#111827; --text-muted:#6B7280;
  --border:#E5E7EB;
  --shadow:0 1px 3px rgba(0,0,0,.08),0 4px 16px rgba(0,0,0,.04);
  --shadow-lg:0 8px 32px rgba(0,0,0,.12);
  --radius:14px; --radius-sm:8px;
  --font-d:'Syne',sans-serif; --font-b:'DM Sans',sans-serif;
  --tr:.18s cubic-bezier(.4,0,.2,1);
}
[data-theme="dark"]{
  --bg:#0F172A; --card:#1E293B; --text:#F1F5F9; --text-muted:#94A3B8;
  --border:#334155; --primary-light:#1E3A8A; --secondary-light:#14532D;
  --accent-light:#4C1D95; --danger-light:#7F1D1D; --warning-light:#78350F;
  --shadow:0 1px 3px rgba(0,0,0,.3),0 4px 16px rgba(0,0,0,.2);
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
body{font-family:var(--font-b);background:var(--bg);color:var(--text);min-height:100vh;transition:background var(--tr),color var(--tr);font-size:14px;line-height:1.5;overflow-x:hidden}
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-thumb{background:var(--border);border-radius:99px}

/* ============================================================ TOPBAR */
#app{display:flex;flex-direction:column;min-height:100vh}
.topbar{
  background:var(--card);border-bottom:1px solid var(--border);
  padding:0 20px;height:60px;display:flex;align-items:center;
  justify-content:space-between;position:sticky;top:0;z-index:200;
  box-shadow:var(--shadow);transition:background var(--tr);
}
.topbar-left{display:flex;align-items:center;gap:12px;min-width:0;flex:1}
.logo-wrap{display:flex;align-items:center;gap:8px;flex-shrink:0}
.logo-img{width:32px;height:32px;border-radius:8px;object-fit:cover;display:none}
.logo-emoji{font-size:22px;line-height:1}
#title-input{
  font-family:var(--font-d);font-size:15px;font-weight:700;color:var(--text);
  background:transparent;border:none;outline:none;border-bottom:2px dashed transparent;
  padding:2px 6px;transition:all var(--tr);min-width:140px;max-width:280px;
  white-space:nowrap;overflow:hidden;text-overflow:ellipsis;
}
#title-input:hover,#title-input:focus{border-bottom-color:var(--primary);background:var(--primary-light);border-radius:4px 4px 0 0}
.topbar-right{display:flex;align-items:center;gap:6px;flex-shrink:0}
.icon-btn{
  width:34px;height:34px;border-radius:var(--radius-sm);border:1px solid var(--border);
  background:var(--card);color:var(--text-muted);display:flex;align-items:center;
  justify-content:center;cursor:pointer;transition:all var(--tr);font-size:15px;
  flex-shrink:0;
}
.icon-btn:hover{background:var(--primary-light);color:var(--primary);border-color:var(--primary)}
.icon-btn.active{background:var(--primary);color:#fff;border-color:var(--primary)}

/* ============================================================ BANNER */
#banner-wrap{
  position:relative;width:100%;overflow:hidden;
  background:linear-gradient(135deg,var(--primary) 0%,var(--accent) 100%);
  display:none;
}
#banner-wrap.visible{display:block}
#banner-img{width:100%;height:120px;object-fit:cover;display:block}
.banner-overlay{
  position:absolute;inset:0;background:rgba(0,0,0,.35);
  display:flex;align-items:center;padding:0 24px;gap:16px;
}
.banner-title{
  font-family:var(--font-d);font-size:22px;font-weight:800;color:#fff;
  text-shadow:0 2px 8px rgba(0,0,0,.4);
}
.banner-remove{
  position:absolute;top:8px;right:8px;background:rgba(0,0,0,.5);
  color:#fff;border:none;cursor:pointer;border-radius:6px;padding:4px 8px;
  font-size:11px;transition:all var(--tr);
}
.banner-remove:hover{background:rgba(220,38,38,.8)}

/* ============================================================ BG IMAGE */
#bg-overlay{
  position:fixed;inset:0;z-index:-1;
  background-size:cover;background-position:center;background-attachment:fixed;
  display:none;
}
#bg-overlay.visible{display:block}
#bg-overlay::after{
  content:'';position:absolute;inset:0;
  background:rgba(0,0,0,0);transition:background var(--tr);
}

/* ============================================================ NAV */
.nav-tabs{
  background:var(--card);border-bottom:1px solid var(--border);
  padding:0 20px;display:flex;gap:2px;overflow-x:auto;
  scrollbar-width:none;
}
.nav-tabs::-webkit-scrollbar{display:none}
.tab-btn{
  font-family:var(--font-b);font-size:13px;font-weight:600;color:var(--text-muted);
  background:transparent;border:none;padding:12px 16px;cursor:pointer;
  border-bottom:2px solid transparent;transition:all var(--tr);
  display:flex;align-items:center;gap:6px;white-space:nowrap;
}
.tab-btn:hover{color:var(--primary)}
.tab-btn.active{color:var(--primary);border-bottom-color:var(--primary)}

/* ============================================================ MAIN */
.main{padding:20px;max-width:1440px;margin:0 auto;width:100%;flex:1}
.tab-pane{display:none}
.tab-pane.active{display:block;animation:fadeIn .2s ease}
@keyframes fadeIn{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:none}}

/* ============================================================ PERIOD BAR */
.period-bar{display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;gap:12px;flex-wrap:wrap}
.period-nav{display:flex;align-items:center;gap:8px}
.period-label{font-family:var(--font-d);font-size:17px;font-weight:700;min-width:150px;text-align:center}
.period-btn{
  width:32px;height:32px;border-radius:var(--radius-sm);border:1px solid var(--border);
  background:var(--card);cursor:pointer;display:flex;align-items:center;justify-content:center;
  color:var(--text-muted);transition:all var(--tr);font-size:14px;
}
.period-btn:hover{background:var(--primary);color:#fff;border-color:var(--primary)}

/* ============================================================ CARDS */
.cards-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:14px;margin-bottom:18px}
.summary-card{
  background:var(--card);border-radius:var(--radius);padding:18px 20px;
  box-shadow:var(--shadow);border:1px solid var(--border);position:relative;
  overflow:hidden;transition:transform var(--tr),box-shadow var(--tr);
}
.summary-card:hover{transform:translateY(-2px);box-shadow:var(--shadow-lg)}
.card-accent{position:absolute;top:0;left:0;width:4px;height:100%;border-radius:var(--radius) 0 0 var(--radius)}
.card-income .card-accent{background:var(--secondary)}
.card-expense .card-accent{background:var(--danger)}
.card-balance .card-accent{background:var(--primary)}
.card-misc .card-accent{background:var(--accent)}
.card-label{font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.8px;color:var(--text-muted);margin-bottom:6px}
.card-value{font-family:var(--font-d);font-size:24px;font-weight:800;letter-spacing:-1px;line-height:1}
.card-income .card-value{color:var(--secondary)}
.card-expense .card-value{color:var(--danger)}
.card-balance .card-value.pos{color:var(--secondary)}
.card-balance .card-value.neg{color:var(--danger)}
.card-change{font-size:11px;color:var(--text-muted);margin-top:5px}
.card-change .up{color:var(--secondary)}.card-change .dn{color:var(--danger)}

/* ============================================================ GRID */
.grid-2{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:18px}
@media(max-width:860px){.grid-2{grid-template-columns:1fr}}

/* ============================================================ CHART CARD */
.chart-card{background:var(--card);border-radius:var(--radius);padding:18px;box-shadow:var(--shadow);border:1px solid var(--border)}
.chart-card-hdr{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px}
.chart-card-title{font-family:var(--font-d);font-size:13px;font-weight:700}
.chart-box{position:relative;height:210px}

/* ============================================================ INSIGHTS */
.insights-list{display:flex;flex-direction:column;gap:8px}
.insight-item{display:flex;align-items:flex-start;gap:10px;padding:10px 12px;border-radius:var(--radius-sm);background:var(--bg);border:1px solid var(--border);font-size:13px;line-height:1.4}
.insight-icon{font-size:17px;flex-shrink:0;margin-top:1px}

/* ============================================================ BUTTONS */
.btn{
  display:inline-flex;align-items:center;gap:7px;border:none;border-radius:var(--radius-sm);
  padding:9px 16px;font-family:var(--font-b);font-size:13px;font-weight:600;
  cursor:pointer;transition:all var(--tr);white-space:nowrap;
}
.btn-primary{background:var(--primary);color:#fff;box-shadow:0 2px 8px rgba(37,99,235,.25)}
.btn-primary:hover{background:var(--primary-dark);transform:translateY(-1px)}
.btn-ghost{background:var(--card);color:var(--text);border:1px solid var(--border)}
.btn-ghost:hover{background:var(--bg);transform:translateY(-1px)}
.btn-danger{background:var(--danger);color:#fff;box-shadow:0 2px 8px rgba(220,38,38,.25)}
.btn-danger:hover{filter:brightness(1.1)}
.btn-success{background:var(--secondary);color:#fff;box-shadow:0 2px 8px rgba(22,163,74,.25)}
.btn-sm{padding:6px 12px;font-size:12px}

/* ============================================================ MODAL */
.modal-overlay{
  display:none;position:fixed;inset:0;background:rgba(0,0,0,.55);
  backdrop-filter:blur(5px);z-index:1000;align-items:center;
  justify-content:center;padding:16px;
}
.modal-overlay.open{display:flex}
.modal{
  background:var(--card);border-radius:var(--radius);padding:26px;
  width:100%;max-width:490px;box-shadow:var(--shadow-lg);
  animation:slideUp .22s ease;max-height:90vh;overflow-y:auto;
}
@keyframes slideUp{from{transform:translateY(22px);opacity:0}to{transform:none;opacity:1}}
.modal-hdr{display:flex;align-items:center;justify-content:space-between;margin-bottom:18px}
.modal-title{font-family:var(--font-d);font-size:17px;font-weight:700}
.modal-close{background:none;border:none;font-size:18px;cursor:pointer;color:var(--text-muted);width:30px;height:30px;display:flex;align-items:center;justify-content:center;border-radius:6px;transition:all var(--tr)}
.modal-close:hover{background:var(--bg);color:var(--text)}
.modal-footer{display:flex;gap:8px;margin-top:18px;justify-content:flex-end}

/* ============================================================ FORM */
.fgrp{margin-bottom:13px}
.flabel{display:block;font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.6px;color:var(--text-muted);margin-bottom:5px}
.fctl{
  width:100%;padding:9px 12px;background:var(--bg);border:1.5px solid var(--border);
  border-radius:var(--radius-sm);color:var(--text);font-family:var(--font-b);
  font-size:14px;outline:none;transition:border-color var(--tr);
}
.fctl:focus{border-color:var(--primary);background:var(--card)}
.frow{display:grid;grid-template-columns:1fr 1fr;gap:11px}
.type-toggle{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-bottom:15px}
.type-btn{
  padding:9px;border-radius:var(--radius-sm);border:2px solid var(--border);
  background:var(--card);cursor:pointer;font-family:var(--font-b);font-size:13px;
  font-weight:600;transition:all var(--tr);display:flex;align-items:center;
  justify-content:center;gap:6px;
}
.type-btn.income.active{background:var(--secondary-light);border-color:var(--secondary);color:var(--secondary)}
.type-btn.expense.active{background:var(--danger-light);border-color:var(--danger);color:var(--danger)}

/* "Outros" reveal */
.outros-wrap{overflow:hidden;max-height:0;opacity:0;transition:max-height .25s ease,opacity .2s ease,margin .2s ease;margin-top:0}
.outros-wrap.visible{max-height:80px;opacity:1;margin-top:7px}
.outros-inner{display:flex;flex-direction:column;gap:4px}
.outros-inner .fctl{border-color:var(--accent);background:var(--accent-light)}
.outros-inner .fctl:focus{border-color:var(--accent)}
.outros-hint{font-size:11px;color:var(--accent);font-weight:500}

/* ============================================================ TABLE */
.table-toolbar{display:flex;align-items:center;justify-content:space-between;gap:10px;margin-bottom:12px;flex-wrap:wrap}
.search-box{position:relative;flex:1;min-width:160px;max-width:280px}
.search-box input{width:100%;padding:8px 12px 8px 34px;background:var(--card);border:1.5px solid var(--border);border-radius:var(--radius-sm);color:var(--text);font-family:var(--font-b);font-size:13px;outline:none;transition:border-color var(--tr)}
.search-box input:focus{border-color:var(--primary)}
.si{position:absolute;left:10px;top:50%;transform:translateY(-50%);color:var(--text-muted);font-size:13px;pointer-events:none}
.filter-row{display:flex;gap:7px;flex-wrap:wrap}
.fsel{padding:7px 11px;background:var(--card);border:1.5px solid var(--border);border-radius:var(--radius-sm);color:var(--text);font-family:var(--font-b);font-size:12px;outline:none;cursor:pointer;transition:border-color var(--tr)}
.fsel:focus{border-color:var(--primary)}
.tbl-wrap{background:var(--card);border-radius:var(--radius);box-shadow:var(--shadow);border:1px solid var(--border);overflow:hidden}
.dtable{width:100%;border-collapse:collapse}
.dtable thead tr{background:var(--bg)}
.dtable th{padding:11px 13px;text-align:left;font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.7px;color:var(--text-muted);white-space:nowrap;cursor:pointer;border-bottom:1px solid var(--border);transition:color var(--tr);user-select:none}
.dtable th:hover,.dtable th.sorted{color:var(--primary)}
.dtable td{padding:12px 13px;font-size:13px;border-bottom:1px solid var(--border);transition:background var(--tr)}
.dtable tbody tr:last-child td{border-bottom:none}
.dtable tbody tr:hover td{background:var(--bg)}
.apos{color:var(--secondary);font-weight:600}.aneg{color:var(--danger);font-weight:600}
.cat-badge{display:inline-flex;align-items:center;padding:2px 9px;border-radius:99px;font-size:11px;font-weight:600;background:var(--primary-light);color:var(--primary)}
.person-chip{display:inline-flex;align-items:center;justify-content:center;width:26px;height:26px;border-radius:50%;background:var(--accent-light);color:var(--accent);font-size:11px;font-weight:700;text-transform:uppercase;flex-shrink:0}
.abtns{display:flex;gap:5px}
.rbtn{width:26px;height:26px;border-radius:5px;border:1px solid var(--border);background:var(--card);cursor:pointer;display:flex;align-items:center;justify-content:center;font-size:12px;transition:all var(--tr);color:var(--text-muted)}
.rbtn:hover.edit{background:var(--primary-light);color:var(--primary);border-color:var(--primary)}
.rbtn:hover.del{background:var(--danger-light);color:var(--danger);border-color:var(--danger)}
.empty-st{text-align:center;padding:50px 20px;color:var(--text-muted)}
.empty-st .eico{font-size:36px;margin-bottom:10px}
.empty-st h3{font-family:var(--font-d);font-size:15px;margin-bottom:5px;color:var(--text)}

/* ============================================================ GOALS */
.goals-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(230px,1fr));gap:12px;margin-bottom:18px}
.goal-card{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);padding:15px;box-shadow:var(--shadow);transition:transform var(--tr)}
.goal-card:hover{transform:translateY(-2px)}
.goal-card-hdr{display:flex;justify-content:space-between;align-items:center;margin-bottom:9px}
.goal-cat{font-weight:600;font-size:13px}
.goal-pct{font-family:var(--font-d);font-size:17px;font-weight:800}
.goal-pct.over{color:var(--danger)}.goal-pct.warn{color:var(--warning)}.goal-pct.safe{color:var(--secondary)}
.gbar-bg{height:7px;background:var(--bg);border-radius:99px;overflow:hidden;border:1px solid var(--border)}
.gbar-fill{height:100%;border-radius:99px;transition:width .5s ease}
.gbar-fill.safe{background:var(--secondary)}.gbar-fill.warn{background:var(--warning)}.gbar-fill.over{background:var(--danger)}
.gbar-lbl{display:flex;justify-content:space-between;font-size:11px;color:var(--text-muted);margin-top:4px}

/* ============================================================ ALERTS */
.alert{display:flex;align-items:flex-start;gap:9px;padding:11px 15px;border-radius:var(--radius-sm);border:1px solid transparent;font-size:13px;margin-bottom:9px;animation:fadeIn .3s ease}
.alert.warning{background:var(--warning-light);border-color:var(--warning);color:var(--warning)}
.alert.danger{background:var(--danger-light);border-color:var(--danger);color:var(--danger)}
.alert.success{background:var(--secondary-light);border-color:var(--secondary);color:var(--secondary)}

/* ============================================================ SECTION CARD */
.sec-card{background:var(--card);border-radius:var(--radius);padding:18px;box-shadow:var(--shadow);border:1px solid var(--border);margin-bottom:18px}
.sec-hdr{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px;flex-wrap:wrap;gap:8px}
.sec-title{font-family:var(--font-d);font-size:14px;font-weight:700}
.stats-tbl{width:100%;border-collapse:collapse;font-size:13px}
.stats-tbl th,.stats-tbl td{padding:9px 12px;border-bottom:1px solid var(--border);text-align:left}
.stats-tbl thead th{background:var(--bg);font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.6px;color:var(--text-muted)}
.stats-tbl tbody tr:last-child td{border-bottom:none}
.stats-tbl tbody tr:hover td{background:var(--bg)}

/* ============================================================ TOAST */
#toast-ct{position:fixed;bottom:22px;right:22px;z-index:2000;display:flex;flex-direction:column;gap:7px}
.toast{background:var(--text);color:var(--card);padding:11px 16px;border-radius:var(--radius-sm);font-size:13px;font-weight:500;box-shadow:var(--shadow-lg);animation:toastIn .3s ease;display:flex;align-items:center;gap:8px;max-width:290px}
.toast.success{background:var(--secondary)}.toast.error{background:var(--danger)}.toast.info{background:var(--primary)}
@keyframes toastIn{from{transform:translateX(110%);opacity:0}to{transform:none;opacity:1}}

/* ============================================================ CUSTOMIZE PANEL */
.cpanel-overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.4);z-index:500;backdrop-filter:blur(3px)}
.cpanel-overlay.open{display:block}
.cpanel{
  position:fixed;top:0;right:-420px;width:400px;height:100vh;
  background:var(--card);border-left:1px solid var(--border);
  z-index:501;transition:right .3s cubic-bezier(.4,0,.2,1);
  display:flex;flex-direction:column;box-shadow:-8px 0 40px rgba(0,0,0,.15);
  overflow-y:auto;
}
.cpanel.open{right:0}
.cpanel-hdr{
  padding:18px 20px;border-bottom:1px solid var(--border);
  display:flex;align-items:center;justify-content:space-between;
  position:sticky;top:0;background:var(--card);z-index:1;
}
.cpanel-title{font-family:var(--font-d);font-size:16px;font-weight:700}
.cpanel-body{padding:20px;display:flex;flex-direction:column;gap:22px;flex:1}
.cpanel-section-title{font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.8px;color:var(--text-muted);margin-bottom:10px}
.color-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
.color-item{display:flex;flex-direction:column;gap:5px}
.color-label{font-size:12px;font-weight:500;color:var(--text)}
.color-row{display:flex;align-items:center;gap:8px}
.color-row input[type=color]{
  width:36px;height:32px;border-radius:6px;border:1.5px solid var(--border);
  cursor:pointer;padding:2px;background:var(--card);flex-shrink:0;
}
.color-hex{
  flex:1;padding:7px 10px;background:var(--bg);border:1.5px solid var(--border);
  border-radius:var(--radius-sm);color:var(--text);font-family:'Courier New',monospace;
  font-size:12px;outline:none;transition:border-color var(--tr);
}
.color-hex:focus{border-color:var(--primary)}
.img-upload-area{
  border:2px dashed var(--border);border-radius:var(--radius);padding:20px;
  text-align:center;cursor:pointer;transition:all var(--tr);
  background:var(--bg);
}
.img-upload-area:hover{border-color:var(--primary);background:var(--primary-light)}
.img-upload-area .uico{font-size:24px;margin-bottom:6px}
.img-upload-area p{font-size:12px;color:var(--text-muted);margin-top:4px}
.img-preview{
  position:relative;border-radius:var(--radius-sm);overflow:hidden;
  border:1px solid var(--border);
}
.img-preview img{width:100%;height:70px;object-fit:cover;display:block}
.img-preview-btns{display:flex;gap:6px;padding:6px}
.img-preview-btns .btn{flex:1;justify-content:center}
.divider{height:1px;background:var(--border)}
@media(max-width:440px){.cpanel{width:100%}}

/* ============================================================ MISC */
.tr-count{padding:8px 0 0;color:var(--text-muted);font-size:11px}
.section-row-title{font-family:var(--font-d);font-size:14px;font-weight:700;margin-bottom:12px;display:flex;align-items:center;gap:7px}
@media(max-width:640px){
  .main{padding:12px}.topbar{padding:0 12px}.nav-tabs{padding:0 12px}
  .cards-grid{grid-template-columns:1fr 1fr;gap:10px}
  .frow{grid-template-columns:1fr}
  .period-label{font-size:14px;min-width:120px}
}
@media(max-width:400px){.cards-grid{grid-template-columns:1fr}}
</style>
</head>
<body>
<div id="app">

<!-- BG OVERLAY -->
<div id="bg-overlay"></div>

<!-- TOPBAR -->
<header class="topbar">
  <div class="topbar-left">
    <div class="logo-wrap">
      <img id="logo-img" class="logo-img" alt="Logo"/>
      <span id="logo-emoji" class="logo-emoji">💰</span>
    </div>
    <input id="title-input" type="text" value="Meu Controle Financeiro" maxlength="50" title="Clique para editar o título"/>
  </div>
  <div class="topbar-right">
    <button class="icon-btn" id="btn-import-csv" title="Importar CSV">📥</button>
    <button class="icon-btn" id="btn-export-csv" title="Exportar CSV">📤</button>
    <button class="icon-btn" id="btn-backup" title="Backup JSON">💾</button>
    <button class="icon-btn" id="btn-restore" title="Restaurar JSON">📂</button>
    <button class="icon-btn" id="btn-theme" title="Modo escuro">🌙</button>
    <button class="icon-btn" id="btn-customize" title="Personalizar dashboard">🎨</button>
    <input type="file" id="file-csv" accept=".csv" style="display:none"/>
    <input type="file" id="file-json" accept=".json" style="display:none"/>
    <input type="file" id="file-bg" accept="image/*" style="display:none"/>
    <input type="file" id="file-banner" accept="image/*" style="display:none"/>
    <input type="file" id="file-logo" accept="image/*" style="display:none"/>
  </div>
</header>

<!-- BANNER -->
<div id="banner-wrap">
  <img id="banner-img" alt="Banner"/>
  <div class="banner-overlay">
    <div class="banner-title" id="banner-title-text">Meu Controle Financeiro</div>
  </div>
  <button class="banner-remove" id="btn-remove-banner">✕ Remover banner</button>
</div>

<!-- TABS -->
<nav class="nav-tabs">
  <button class="tab-btn active" data-tab="dashboard">📊 Dashboard</button>
  <button class="tab-btn" data-tab="transactions">💳 Transações</button>
  <button class="tab-btn" data-tab="reports">📈 Relatórios</button>
  <button class="tab-btn" data-tab="goals">🎯 Metas</button>
</nav>

<!-- MAIN -->
<main class="main">

  <!-- ══════════════ DASHBOARD ══════════════ -->
  <div class="tab-pane active" id="tab-dashboard">
    <div class="period-bar">
      <div class="period-nav">
        <button class="period-btn" id="prev-m">◀</button>
        <span class="period-label" id="period-lbl">—</span>
        <button class="period-btn" id="next-m">▶</button>
      </div>
      <button class="btn btn-primary" id="btn-add-tx">+ Nova Transação</button>
    </div>
    <div id="alerts-sec"></div>
    <div class="cards-grid">
      <div class="summary-card card-income"><div class="card-accent"></div><div class="card-label">Entradas</div><div class="card-value" id="c-income">R$ 0,00</div><div class="card-change" id="c-income-ch"></div></div>
      <div class="summary-card card-expense"><div class="card-accent"></div><div class="card-label">Saídas</div><div class="card-value" id="c-expense">R$ 0,00</div><div class="card-change" id="c-expense-ch"></div></div>
      <div class="summary-card card-balance"><div class="card-accent"></div><div class="card-label">Saldo do Mês</div><div class="card-value" id="c-balance">R$ 0,00</div><div class="card-change" id="c-balance-ch"></div></div>
      <div class="summary-card card-misc"><div class="card-accent"></div><div class="card-label">Transações</div><div class="card-value" style="color:var(--accent)" id="c-count">0</div><div class="card-change" id="c-count-ch"></div></div>
    </div>
    <div class="grid-2">
      <div class="chart-card"><div class="chart-card-hdr"><span class="chart-card-title">🍩 Gastos por Categoria</span></div><div class="chart-box"><canvas id="ch-cat"></canvas></div></div>
      <div class="chart-card"><div class="chart-card-hdr"><span class="chart-card-title">📉 Evolução do Saldo</span></div><div class="chart-box"><canvas id="ch-bal"></canvas></div></div>
    </div>
    <div class="grid-2">
      <div class="chart-card"><div class="chart-card-hdr"><span class="chart-card-title">📊 Comparação Mensal</span></div><div class="chart-box"><canvas id="ch-cmp"></canvas></div></div>
      <div class="chart-card"><div class="chart-card-hdr"><span class="chart-card-title">💡 Insights</span></div><div class="insights-list" id="insights-list"><div class="insight-item"><span class="insight-icon">ℹ️</span><span>Adicione transações para ver insights.</span></div></div></div>
    </div>
    <div class="sec-card">
      <div class="sec-hdr"><span class="sec-title">Transações Recentes</span><button class="btn btn-ghost btn-sm" onclick="switchTab('transactions')">Ver todas →</button></div>
      <div id="recent-tx"></div>
    </div>
  </div>

  <!-- ══════════════ TRANSACTIONS ══════════════ -->
  <div class="tab-pane" id="tab-transactions">
    <div class="period-bar">
      <div class="period-nav">
        <button class="period-btn" id="prev-m-tx">◀</button>
        <span class="period-label" id="period-lbl-tx">—</span>
        <button class="period-btn" id="next-m-tx">▶</button>
      </div>
      <button class="btn btn-primary" id="btn-add-tx2">+ Nova Transação</button>
    </div>
    <div class="sec-card" style="padding:14px 0">
      <div class="table-toolbar" style="padding:0 14px">
        <div class="search-box"><span class="si">🔍</span><input id="s-input" type="text" placeholder="Buscar…"/></div>
        <div class="filter-row">
          <select class="fsel" id="f-type"><option value="">Tipo</option><option value="income">Entrada</option><option value="expense">Saída</option></select>
          <select class="fsel" id="f-cat"><option value="">Categoria</option></select>
          <select class="fsel" id="f-person"><option value="">Pessoa</option></select>
          <button class="btn btn-ghost btn-sm" id="btn-clf">✕ Limpar</button>
        </div>
      </div>
      <div class="tbl-wrap" style="margin:12px 14px 0">
        <table class="dtable">
          <thead><tr>
            <th data-sort="date">Data ↕</th>
            <th data-sort="description">Descrição ↕</th>
            <th data-sort="category">Categoria ↕</th>
            <th data-sort="person">Pessoa ↕</th>
            <th data-sort="amount" style="text-align:right">Valor ↕</th>
            <th></th>
          </tr></thead>
          <tbody id="tx-tbody"></tbody>
        </table>
      </div>
      <div class="tr-count" style="padding:8px 14px 0" id="tx-count-lbl"></div>
    </div>
  </div>

  <!-- ══════════════ REPORTS ══════════════ -->
  <div class="tab-pane" id="tab-reports">
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;margin-bottom:18px">
      <h2 style="font-family:var(--font-d);font-size:18px;font-weight:800">Relatórios</h2>
      <button class="btn btn-ghost" id="btn-pdf">📄 Exportar PDF com Gráficos</button>
    </div>
    <div class="sec-card"><div class="section-row-title">📂 Gastos por Categoria</div><div class="tbl-wrap"><table class="stats-tbl" id="rpt-cat"><thead><tr><th>Categoria</th><th>Total Saídas</th><th>Total Entradas</th><th>% das Saídas</th></tr></thead><tbody></tbody></table></div></div>
    <div class="sec-card"><div class="section-row-title">👥 Por Pessoa</div><div class="tbl-wrap"><table class="stats-tbl" id="rpt-person"><thead><tr><th>Pessoa</th><th>Entradas</th><th>Saídas</th><th>Saldo</th></tr></thead><tbody></tbody></table></div></div>
    <div class="sec-card"><div class="section-row-title">📅 Histórico Mensal</div><div class="tbl-wrap"><table class="stats-tbl" id="rpt-month"><thead><tr><th>Mês</th><th>Entradas</th><th>Saídas</th><th>Saldo</th><th>Variação</th></tr></thead><tbody></tbody></table></div></div>
  </div>

  <!-- ══════════════ GOALS ══════════════ -->
  <div class="tab-pane" id="tab-goals">
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;margin-bottom:18px">
      <h2 style="font-family:var(--font-d);font-size:18px;font-weight:800">Metas de Gastos</h2>
      <button class="btn btn-primary" id="btn-add-goal">+ Nova Meta</button>
    </div>
    <div class="goals-grid" id="goals-grid"></div>
    <div id="goals-empty" class="sec-card" style="text-align:center;padding:40px;color:var(--text-muted)">
      <div style="font-size:34px;margin-bottom:10px">🎯</div>
      <div style="font-family:var(--font-d);font-size:14px;font-weight:700;margin-bottom:5px;color:var(--text)">Nenhuma meta definida</div>
      <div style="font-size:13px">Crie metas mensais por categoria para acompanhar seu orçamento.</div>
    </div>
  </div>

</main>
</div>

<!-- ══════════════ CUSTOMIZE PANEL ══════════════ -->
<div class="cpanel-overlay" id="cpanel-overlay"></div>
<div class="cpanel" id="cpanel">
  <div class="cpanel-hdr">
    <span class="cpanel-title">🎨 Personalizar</span>
    <button class="modal-close" id="btn-close-cpanel">✕</button>
  </div>
  <div class="cpanel-body">

    <!-- COLORS -->
    <div>
      <div class="cpanel-section-title">🎨 Cores do Tema</div>
      <div class="color-grid">
        <div class="color-item"><div class="color-label">Cor Primária</div><div class="color-row"><input type="color" id="cp-primary" value="#2563EB"/><input type="text" class="color-hex" id="ch-primary" value="#2563EB" maxlength="7"/></div></div>
        <div class="color-item"><div class="color-label">Cor Positiva</div><div class="color-row"><input type="color" id="cp-secondary" value="#16A34A"/><input type="text" class="color-hex" id="ch-secondary" value="#16A34A" maxlength="7"/></div></div>
        <div class="color-item"><div class="color-label">Destaque</div><div class="color-row"><input type="color" id="cp-accent" value="#7C3AED"/><input type="text" class="color-hex" id="ch-accent" value="#7C3AED" maxlength="7"/></div></div>
        <div class="color-item"><div class="color-label">Fundo</div><div class="color-row"><input type="color" id="cp-bg" value="#F3F4F6"/><input type="text" class="color-hex" id="ch-bg" value="#F3F4F6" maxlength="7"/></div></div>
        <div class="color-item"><div class="color-label">Cards</div><div class="color-row"><input type="color" id="cp-card" value="#FFFFFF"/><input type="text" class="color-hex" id="ch-card" value="#FFFFFF" maxlength="7"/></div></div>
      </div>
      <div style="display:flex;gap:8px;margin-top:12px">
        <button class="btn btn-ghost btn-sm" id="btn-reset-colors" style="flex:1;justify-content:center">↩ Restaurar Padrão</button>
      </div>
    </div>

    <div class="divider"></div>

    <!-- BG IMAGE -->
    <div>
      <div class="cpanel-section-title">🖼️ Imagem de Fundo</div>
      <div id="bg-img-preview" style="display:none" class="img-preview">
        <img id="bg-img-thumb" alt="Fundo"/>
        <div class="img-preview-btns">
          <button class="btn btn-ghost btn-sm" id="btn-change-bg">Trocar</button>
          <button class="btn btn-danger btn-sm" id="btn-remove-bg">Remover</button>
        </div>
      </div>
      <div id="bg-img-upload" class="img-upload-area" id="bg-drop">
        <div class="uico">🖼️</div>
        <strong>Clique para enviar</strong>
        <p>Imagem de fundo do dashboard</p>
      </div>
      <div style="margin-top:8px">
        <label class="color-label" style="margin-bottom:5px;display:block">Opacidade da sobreposição</label>
        <input type="range" id="bg-opacity" min="0" max="80" value="0" style="width:100%;accent-color:var(--primary)"/>
        <div style="display:flex;justify-content:space-between;font-size:11px;color:var(--text-muted)"><span>Nenhuma</span><span id="bg-opacity-val">0%</span><span>80%</span></div>
      </div>
    </div>

    <div class="divider"></div>

    <!-- BANNER -->
    <div>
      <div class="cpanel-section-title">🏷️ Banner / Topo</div>
      <div id="banner-preview" style="display:none" class="img-preview">
        <img id="banner-thumb" alt="Banner"/>
        <div class="img-preview-btns">
          <button class="btn btn-ghost btn-sm" id="btn-change-banner">Trocar</button>
          <button class="btn btn-danger btn-sm" id="btn-remove-banner-cp">Remover</button>
        </div>
      </div>
      <div id="banner-upload" class="img-upload-area">
        <div class="uico">🏷️</div>
        <strong>Clique para enviar</strong>
        <p>Imagem de topo / banner</p>
      </div>
    </div>

    <div class="divider"></div>

    <!-- LOGO -->
    <div>
      <div class="cpanel-section-title">🔷 Logo Personalizada</div>
      <div id="logo-preview" style="display:none" class="img-preview">
        <img id="logo-thumb" alt="Logo" style="height:50px;object-fit:contain"/>
        <div class="img-preview-btns">
          <button class="btn btn-ghost btn-sm" id="btn-change-logo">Trocar</button>
          <button class="btn btn-danger btn-sm" id="btn-remove-logo">Remover</button>
        </div>
      </div>
      <div id="logo-upload" class="img-upload-area">
        <div class="uico">🔷</div>
        <strong>Clique para enviar</strong>
        <p>Logo exibida na barra superior</p>
      </div>
    </div>

  </div>
</div>

<!-- ══════════════ TRANSACTION MODAL ══════════════ -->
<div class="modal-overlay" id="m-tx">
  <div class="modal">
    <div class="modal-hdr"><span class="modal-title" id="m-tx-title">Nova Transação</span><button class="modal-close" onclick="closeModal('m-tx')">✕</button></div>
    <div class="type-toggle">
      <button class="type-btn income active" id="tb-income" onclick="setType('income')">↑ Entrada</button>
      <button class="type-btn expense" id="tb-expense" onclick="setType('expense')">↓ Saída</button>
    </div>
    <div class="fgrp"><label class="flabel">Descrição *</label><input class="fctl" id="tx-desc" type="text" placeholder="Ex: Salário, Supermercado…"/></div>
    <div class="frow">
      <div class="fgrp"><label class="flabel">Valor (R$) *</label><input class="fctl" id="tx-amt" type="number" min="0" step="0.01" placeholder="0,00"/></div>
      <div class="fgrp"><label class="flabel">Data *</label><input class="fctl" id="tx-date" type="date"/></div>
    </div>
    <div class="frow">
      <!-- CATEGORIA -->
      <div class="fgrp">
        <label class="flabel">Categoria</label>
        <select class="fctl" id="tx-cat" onchange="onFieldOtros('cat',this)"></select>
        <div class="outros-wrap" id="ow-cat">
          <div class="outros-inner">
            <input class="fctl" id="tx-cat-outros" type="text" placeholder="Nova categoria…" maxlength="40"/>
            <div class="outros-hint">✏️ Será salva para uso futuro</div>
          </div>
        </div>
      </div>
      <!-- PESSOA -->
      <div class="fgrp">
        <label class="flabel">Pessoa</label>
        <select class="fctl" id="tx-person" onchange="onFieldOtros('person',this)"></select>
        <div class="outros-wrap" id="ow-person">
          <div class="outros-inner">
            <input class="fctl" id="tx-person-outros" type="text" placeholder="Nome da pessoa…" maxlength="50"/>
            <div class="outros-hint">✏️ Será salva para uso futuro</div>
          </div>
        </div>
      </div>
    </div>
    <div class="fgrp"><label class="flabel">Notas (opcional)</label><input class="fctl" id="tx-notes" type="text" placeholder="Observações…"/></div>
    <div class="modal-footer">
      <button class="btn btn-ghost" onclick="closeModal('m-tx')">Cancelar</button>
      <button class="btn btn-primary" id="btn-save-tx">Salvar</button>
    </div>
  </div>
</div>

<!-- GOAL MODAL -->
<div class="modal-overlay" id="m-goal">
  <div class="modal" style="max-width:400px">
    <div class="modal-hdr"><span class="modal-title">Nova Meta de Gasto</span><button class="modal-close" onclick="closeModal('m-goal')">✕</button></div>
    <div class="fgrp">
      <label class="flabel">Categoria</label>
      <select class="fctl" id="goal-cat" onchange="onFieldOtros('goalcat',this)"></select>
      <div class="outros-wrap" id="ow-goalcat">
        <div class="outros-inner">
          <input class="fctl" id="goal-cat-outros" type="text" placeholder="Nova categoria…" maxlength="40"/>
          <div class="outros-hint">✏️ Será salva para uso futuro</div>
        </div>
      </div>
    </div>
    <div class="fgrp"><label class="flabel">Limite Mensal (R$)</label><input class="fctl" id="goal-limit" type="number" min="0" step="0.01" placeholder="0,00"/></div>
    <div class="modal-footer">
      <button class="btn btn-ghost" onclick="closeModal('m-goal')">Cancelar</button>
      <button class="btn btn-primary" id="btn-save-goal">Salvar Meta</button>
    </div>
  </div>
</div>

<div id="toast-ct"></div>

<script>
/* ================================================================
   DEFAULTS & INITIAL STATE
   ================================================================ */
const DEF_CATS  = ['Alimentação','Transporte','Moradia','Saúde','Lazer','Educação','Vestuário','Salário','Freelance','Investimentos','Outros'];
const DEF_PEOPLE= ['Eu','Família','Amigo(a)','Trabalho','Parceiro(a)'];
const DEF_COLORS= {primary:'#2563EB',secondary:'#16A34A',accent:'#7C3AED',bg:'#F3F4F6',card:'#FFFFFF'};
const MONTHS_PT = ['Janeiro','Fevereiro','Março','Abril','Maio','Junho','Julho','Agosto','Setembro','Outubro','Novembro','Dezembro'];

let S = {
  title:'Meu Controle Financeiro',
  transactions:[],
  categories:[...DEF_CATS],
  people:[...DEF_PEOPLE],
  goals:[],
  colors:{...DEF_COLORS},
  bgImage:null, bgOpacity:0,
  bannerImage:null,
  logoImage:null,
  darkMode:false,
  currentYear:new Date().getFullYear(),
  currentMonth:new Date().getMonth(),
  // UI state (not persisted)
  editingTxId:null,
  sortField:'date', sortDir:'desc',
  filterType:'', filterCat:'', filterPerson:'', searchQ:'',
};

/* ================================================================
   PERSISTENCE
   ================================================================ */
function persist(){
  try{
    const toSave={
      title:S.title, transactions:S.transactions, categories:S.categories,
      people:S.people, goals:S.goals, colors:S.colors,
      bgImage:S.bgImage, bgOpacity:S.bgOpacity,
      bannerImage:S.bannerImage, logoImage:S.logoImage,
      darkMode:S.darkMode,
      currentYear:S.currentYear, currentMonth:S.currentMonth,
    };
    localStorage.setItem('finDash_v4',JSON.stringify(toSave));
  }catch(e){toast('Erro ao salvar dados (localStorage cheio?).','error');}
}
function loadState(){
  try{
    const raw=localStorage.getItem('finDash_v4');
    if(raw){const p=JSON.parse(raw);Object.assign(S,p);}
  }catch(e){}
}

/* ================================================================
   UTILS
   ================================================================ */
const BRL=v=>'R$ '+Math.abs(v).toLocaleString('pt-BR',{minimumFractionDigits:2,maximumFractionDigits:2});
const uid=()=>Date.now().toString(36)+Math.random().toString(36).slice(2);
const esc=s=>String(s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
const fmtDate=d=>{const[y,m,dd]=d.split('-');return`${dd}/${m}/${y}`};
const txPeriod=(y,m)=>S.transactions.filter(t=>{const d=new Date(t.date+'T00:00');return d.getFullYear()===y&&d.getMonth()===m});
const sumIncome=txs=>txs.filter(t=>t.type==='income').reduce((s,t)=>s+t.amount,0);
const sumExpense=txs=>txs.filter(t=>t.type==='expense').reduce((s,t)=>s+t.amount,0);
const pctChange=(a,b)=>{if(!b)return null;return((a-b)/Math.abs(b)*100).toFixed(1)};
function hexToRgb(hex){const r=parseInt(hex.slice(1,3),16),g=parseInt(hex.slice(3,5),16),b=parseInt(hex.slice(5,7),16);return`${r},${g},${b}`}
function luminance(hex){const[r,g,b]=hex.slice(1).match(/.{2}/g).map(h=>parseInt(h,16)/255);return .2126*r+.7152*g+.0722*b}

/* ================================================================
   TOAST
   ================================================================ */
function toast(msg,type=''){
  const el=document.createElement('div');
  el.className='toast'+(type?' '+type:'');
  el.textContent=msg;
  document.getElementById('toast-ct').appendChild(el);
  setTimeout(()=>{el.style.opacity='0';el.style.transform='translateX(110%)';el.style.transition='.3s';setTimeout(()=>el.remove(),300)},2700);
}

/* ================================================================
   APPLY COLORS
   ================================================================ */
function applyColors(cols=S.colors){
  const r=document.documentElement.style;
  r.setProperty('--primary',cols.primary);
  r.setProperty('--primary-dark',darken(cols.primary,15));
  r.setProperty('--primary-light',lighten(cols.primary,90));
  r.setProperty('--secondary',cols.secondary);
  r.setProperty('--secondary-light',lighten(cols.secondary,90));
  r.setProperty('--accent',cols.accent);
  r.setProperty('--accent-light',lighten(cols.accent,90));
  if(!S.darkMode){
    r.setProperty('--bg',cols.bg);
    r.setProperty('--card',cols.card);
  }
  // Sync pickers
  ['primary','secondary','accent','bg','card'].forEach(k=>{
    const cp=document.getElementById('cp-'+k);
    const ch=document.getElementById('ch-'+k);
    if(cp)cp.value=cols[k];
    if(ch)ch.value=cols[k];
  });
}
function darken(hex,pct){return adjustColor(hex,-pct)}
function lighten(hex,pct){
  // For light mode: create a very light tint
  const r=parseInt(hex.slice(1,3),16),g=parseInt(hex.slice(3,5),16),b=parseInt(hex.slice(5,7),16);
  const factor=pct/100;
  const nr=Math.round(r+(255-r)*factor);
  const ng=Math.round(g+(255-g)*factor);
  const nb=Math.round(b+(255-b)*factor);
  return`#${nr.toString(16).padStart(2,'0')}${ng.toString(16).padStart(2,'0')}${nb.toString(16).padStart(2,'0')}`;
}
function adjustColor(hex,amount){
  let r=parseInt(hex.slice(1,3),16)+amount;
  let g=parseInt(hex.slice(3,5),16)+amount;
  let b=parseInt(hex.slice(5,7),16)+amount;
  r=Math.min(255,Math.max(0,r));g=Math.min(255,Math.max(0,g));b=Math.min(255,Math.max(0,b));
  return`#${r.toString(16).padStart(2,'0')}${g.toString(16).padStart(2,'0')}${b.toString(16).padStart(2,'0')}`;
}

/* ================================================================
   CUSTOMIZE PANEL
   ================================================================ */
function openCPanel(){
  document.getElementById('cpanel').classList.add('open');
  document.getElementById('cpanel-overlay').classList.add('open');
  document.getElementById('btn-customize').classList.add('active');
}
function closeCPanel(){
  document.getElementById('cpanel').classList.remove('open');
  document.getElementById('cpanel-overlay').classList.remove('open');
  document.getElementById('btn-customize').classList.remove('active');
}
document.getElementById('btn-customize').addEventListener('click',openCPanel);
document.getElementById('btn-close-cpanel').addEventListener('click',closeCPanel);
document.getElementById('cpanel-overlay').addEventListener('click',closeCPanel);

// Color pickers
['primary','secondary','accent','bg','card'].forEach(k=>{
  const cp=document.getElementById('cp-'+k);
  const ch=document.getElementById('ch-'+k);
  cp.addEventListener('input',()=>{
    ch.value=cp.value;
    S.colors[k]=cp.value;
    applyColors();
    persist();
    if(k==='primary'||k==='secondary'||k==='accent') rebuildCharts();
  });
  ch.addEventListener('input',()=>{
    if(/^#[0-9a-fA-F]{6}$/.test(ch.value)){
      cp.value=ch.value;
      S.colors[k]=ch.value;
      applyColors();
      persist();
      if(k==='primary'||k==='secondary'||k==='accent') rebuildCharts();
    }
  });
});
document.getElementById('btn-reset-colors').addEventListener('click',()=>{
  S.colors={...DEF_COLORS};
  applyColors();
  persist();
  rebuildCharts();
  toast('Cores restauradas! ↩','success');
});

/* ================================================================
   IMAGE UPLOADS
   ================================================================ */
// BG
function setupImgUpload(uploadAreaId,fileInputId,previewId,thumbId,key){
  const area=document.getElementById(uploadAreaId);
  const fileInput=document.getElementById(fileInputId);
  area.addEventListener('click',()=>fileInput.click());
  fileInput.addEventListener('change',()=>{
    const f=fileInput.files[0]; if(!f) return;
    if(f.size>5*1024*1024){toast('Imagem muito grande (máx 5MB).','error');return;}
    const reader=new FileReader();
    reader.onload=e=>{
      S[key]=e.target.result;
      applyImage(key);
      persist();
      toast('Imagem aplicada! 🖼️','success');
    };
    reader.readAsDataURL(f);
    fileInput.value='';
  });
  const preview=document.getElementById(previewId);
  const thumb=document.getElementById(thumbId);
  document.getElementById('btn-change-'+key.replace('Image',''))?.addEventListener('click',()=>fileInput.click());
  document.getElementById('btn-remove-'+key.replace('Image',''))?.addEventListener('click',()=>removeImage(key));
}

function applyImage(key){
  if(key==='bgImage'){
    const ov=document.getElementById('bg-overlay');
    const prev=document.getElementById('bg-img-preview');
    const upl=document.getElementById('bg-img-upload');
    const thumb=document.getElementById('bg-img-thumb');
    if(S.bgImage){
      ov.style.backgroundImage=`url(${S.bgImage})`;
      ov.classList.add('visible');
      applyBgOpacity(S.bgOpacity);
      if(thumb)thumb.src=S.bgImage;
      if(prev)prev.style.display='block';
      if(upl)upl.style.display='none';
    } else {
      ov.classList.remove('visible');
      ov.style.backgroundImage='';
      if(prev)prev.style.display='none';
      if(upl)upl.style.display='block';
    }
  }
  if(key==='bannerImage'){
    const wrap=document.getElementById('banner-wrap');
    const img=document.getElementById('banner-img');
    const prev=document.getElementById('banner-preview');
    const upl=document.getElementById('banner-upload');
    const thumb=document.getElementById('banner-thumb');
    if(S.bannerImage){
      img.src=S.bannerImage;
      wrap.classList.add('visible');
      if(thumb)thumb.src=S.bannerImage;
      if(prev)prev.style.display='block';
      if(upl)upl.style.display='none';
    } else {
      wrap.classList.remove('visible');
      if(prev)prev.style.display='none';
      if(upl)upl.style.display='block';
    }
  }
  if(key==='logoImage'){
    const img=document.getElementById('logo-img');
    const emoji=document.getElementById('logo-emoji');
    const prev=document.getElementById('logo-preview');
    const upl=document.getElementById('logo-upload');
    const thumb=document.getElementById('logo-thumb');
    if(S.logoImage){
      img.src=S.logoImage; img.style.display='block';
      emoji.style.display='none';
      if(thumb)thumb.src=S.logoImage;
      if(prev)prev.style.display='block';
      if(upl)upl.style.display='none';
    } else {
      img.style.display='none'; emoji.style.display='block';
      if(prev)prev.style.display='none';
      if(upl)upl.style.display='block';
    }
  }
}

function removeImage(key){
  S[key]=null;
  applyImage(key);
  persist();
  toast('Imagem removida.','');
}

function applyBgOpacity(val){
  const ov=document.getElementById('bg-overlay');
  ov.style.setProperty('--bg-darken',`rgba(0,0,0,${val/100})`);
  ov.style.background=`url(${S.bgImage||''}) center/cover fixed`;
  if(S.bgImage) ov.style.boxShadow=`inset 0 0 0 100vmax rgba(0,0,0,${val/100})`;
}

document.getElementById('bg-opacity').addEventListener('input',function(){
  S.bgOpacity=parseInt(this.value);
  document.getElementById('bg-opacity-val').textContent=S.bgOpacity+'%';
  applyBgOpacity(S.bgOpacity);
  persist();
});

// Wire uploads
['bg-img-upload','banner-upload','logo-upload'].forEach(id=>{
  const el=document.getElementById(id);
  if(!el) return;
  const map={'bg-img-upload':'bgImage','banner-upload':'bannerImage','logo-upload':'logoImage'};
  const fileMap={'bgImage':'file-bg','bannerImage':'file-banner','logoImage':'file-logo'};
  el.addEventListener('click',()=>document.getElementById(fileMap[map[id]]).click());
});
['bg','banner','logo'].forEach(k=>{
  const fi=document.getElementById('file-'+k);
  fi.addEventListener('change',()=>{
    const f=fi.files[0]; if(!f)return;
    if(f.size>5*1024*1024){toast('Imagem grande demais (máx 5MB).','error');return;}
    const reader=new FileReader();
    const keyMap={bg:'bgImage',banner:'bannerImage',logo:'logoImage'};
    reader.onload=e=>{S[keyMap[k]]=e.target.result;applyImage(keyMap[k]);persist();toast('Imagem aplicada! 🖼️','success');};
    reader.readAsDataURL(f);fi.value='';
  });
  document.getElementById('btn-change-'+k)?.addEventListener('click',()=>document.getElementById('file-'+k).click());
  document.getElementById('btn-remove-'+k)?.addEventListener('click',()=>removeImage(k==='bg'?'bgImage':k==='banner'?'bannerImage':'logoImage'));
});
document.getElementById('btn-remove-banner').addEventListener('click',()=>removeImage('bannerImage'));
document.getElementById('btn-remove-banner-cp')?.addEventListener('click',()=>removeImage('bannerImage'));

/* ================================================================
   CHARTS
   ================================================================ */
let chCat=null,chBal=null,chCmp=null;
function isDark(){return document.documentElement.getAttribute('data-theme')==='dark'}
const tColor=()=>isDark()?'#94A3B8':'#6B7280';
const gridColor=()=>isDark()?'#334155':'#E5E7EB';
const CHART_COLORS=['#2563EB','#16A34A','#7C3AED','#F59E0B','#EF4444','#06B6D4','#EC4899','#84CC16','#F97316','#8B5CF6','#14B8A6'];

function rebuildCharts(){
  const txs=txPeriod(S.currentYear,S.currentMonth);
  // Category doughnut
  const catMap={};
  txs.filter(t=>t.type==='expense').forEach(t=>{catMap[t.category]=(catMap[t.category]||0)+t.amount});
  const cLabels=Object.keys(catMap),cData=cLabels.map(k=>catMap[k]);
  if(chCat)chCat.destroy();
  const c1=document.getElementById('ch-cat').getContext('2d');
  chCat=new Chart(c1,{type:'doughnut',data:{labels:cLabels,datasets:[{data:cData,backgroundColor:CHART_COLORS,borderWidth:2,borderColor:isDark()?'#1E293B':'#fff'}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{position:'right',labels:{boxWidth:9,font:{size:11},color:tColor()}}}}});

  // Balance line (6 months)
  const mLabels=[],mInc=[],mExp=[];
  for(let i=5;i>=0;i--){let m=S.currentMonth-i,y=S.currentYear;if(m<0){m+=12;y--;}mLabels.push(MONTHS_PT[m].slice(0,3)+'/'+String(y).slice(2));const t=txPeriod(y,m);mInc.push(sumIncome(t));mExp.push(sumExpense(t));}
  const mBal=mInc.map((v,i)=>+(v-mExp[i]).toFixed(2));
  if(chBal)chBal.destroy();
  const c2=document.getElementById('ch-bal').getContext('2d');
  chBal=new Chart(c2,{type:'line',data:{labels:mLabels,datasets:[{label:'Saldo',data:mBal,borderColor:S.colors.primary||'#2563EB',backgroundColor:(S.colors.primary||'#2563EB')+'22',tension:.4,fill:true,borderWidth:2,pointRadius:3}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{grid:{display:false},ticks:{color:tColor(),font:{size:10}}},y:{ticks:{color:tColor(),font:{size:10},callback:v=>'R$'+v.toFixed(0)},grid:{color:gridColor()}}}}});

  // Monthly comparison
  if(chCmp)chCmp.destroy();
  const c3=document.getElementById('ch-cmp').getContext('2d');
  chCmp=new Chart(c3,{type:'bar',data:{labels:mLabels,datasets:[{label:'Entradas',data:mInc,backgroundColor:(S.colors.secondary||'#16A34A')+'BB',borderRadius:4},{label:'Saídas',data:mExp,backgroundColor:'#DC2626BB',borderRadius:4}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{font:{size:11},color:tColor()}}},scales:{x:{grid:{display:false},ticks:{color:tColor(),font:{size:10}}},y:{ticks:{color:tColor(),font:{size:10},callback:v=>'R$'+v.toFixed(0)},grid:{color:gridColor()}}}}});
}

/* ================================================================
   RENDER DASHBOARD
   ================================================================ */
function renderDashboard(){
  const txs=txPeriod(S.currentYear,S.currentMonth);
  const inc=sumIncome(txs),exp=sumExpense(txs),bal=inc-exp;
  let pm=S.currentMonth-1,py=S.currentYear;if(pm<0){pm=11;py--;}
  const pt=txPeriod(py,pm),pi=sumIncome(pt),pe=sumExpense(pt);

  const lbl=MONTHS_PT[S.currentMonth]+' '+S.currentYear;
  document.getElementById('period-lbl').textContent=lbl;
  document.getElementById('period-lbl-tx').textContent=lbl;
  document.getElementById('banner-title-text').textContent=S.title;

  document.getElementById('c-income').textContent=BRL(inc);
  document.getElementById('c-expense').textContent=BRL(exp);
  const bEl=document.getElementById('c-balance');
  bEl.textContent=BRL(bal); bEl.className='card-value '+(bal>=0?'pos':'neg');
  document.getElementById('c-count').textContent=txs.length;

  const setChg=(id,curr,prev)=>{const el=document.getElementById(id);if(!el)return;const p=pctChange(curr,prev);if(!p){el.innerHTML='';return;}const up=parseFloat(p)>0;el.innerHTML=`vs mês anterior: <span class="${up?'up':'dn'}">${up?'▲':'▼'} ${Math.abs(p)}%</span>`;};
  setChg('c-income-ch',inc,pi); setChg('c-expense-ch',exp,pe); setChg('c-balance-ch',bal,pi-pe); setChg('c-count-ch',txs.length,pt.length);

  rebuildCharts();
  renderInsights(txs,inc,exp,bal,pt,pi,pe);
  renderAlerts(txs);
  renderRecentTx(txs);
}

function renderInsights(txs,inc,exp,bal,ptxs,pi,pe){
  const el=document.getElementById('insights-list');
  const ins=[];
  const catExp={};txs.filter(t=>t.type==='expense').forEach(t=>{catExp[t.category]=(catExp[t.category]||0)+t.amount});
  const topE=Object.entries(catExp).sort((a,b)=>b[1]-a[1])[0];
  if(topE)ins.push({i:'💸',t:`Maior gasto: <strong>${topE[0]}</strong> — ${BRL(topE[1])}`});
  const catInc={};txs.filter(t=>t.type==='income').forEach(t=>{catInc[t.category]=(catInc[t.category]||0)+t.amount});
  const topI=Object.entries(catInc).sort((a,b)=>b[1]-a[1])[0];
  if(topI)ins.push({i:'💰',t:`Principal renda: <strong>${topI[0]}</strong> — ${BRL(topI[1])}`});
  ins.push({i:bal>=0?'✅':'⚠️',t:bal>=0?`Saldo <strong>positivo</strong> de ${BRL(bal)}.`:`Saldo <strong>negativo</strong> de ${BRL(Math.abs(bal))}.`});
  if(pe>0){const v=pctChange(exp,pe);if(v){const up=parseFloat(v)>0;ins.push({i:up?'📈':'📉',t:`Gastos ${up?'<strong style="color:var(--danger)">subiram</strong>':'<strong style="color:var(--secondary)">caíram</strong>'} ${Math.abs(v)}% vs mês anterior.`});}}
  const pm={};txs.forEach(t=>{pm[t.person]=(pm[t.person]||0)+t.amount});
  const topP=Object.entries(pm).sort((a,b)=>b[1]-a[1])[0];
  if(topP)ins.push({i:'👤',t:`Maior movimentação: <strong>${topP[0]}</strong> — ${BRL(topP[1])}`});
  const pCE={};ptxs.filter(t=>t.type==='expense').forEach(t=>{pCE[t.category]=(pCE[t.category]||0)+t.amount});
  Object.entries(catExp).forEach(([c,v])=>{const pv=pCE[c];if(pv&&pv>0){const d=((v-pv)/pv*100).toFixed(0);if(Math.abs(d)>=15){const up=d>0;ins.push({i:up?'⬆️':'⬇️',t:`${c} ${up?'subiu':'caiu'} <strong>${Math.abs(d)}%</strong> vs mês passado.`})}}});
  if(!ins.length)ins.push({i:'ℹ️',t:'Adicione transações para ver insights.'});
  el.innerHTML=ins.slice(0,6).map(x=>`<div class="insight-item"><span class="insight-icon">${x.i}</span><span>${x.t}</span></div>`).join('');
}

function renderAlerts(txs){
  const sec=document.getElementById('alerts-sec');
  const al=[];
  S.goals.forEach(g=>{
    const spent=txs.filter(t=>t.type==='expense'&&t.category===g.category).reduce((s,t)=>s+t.amount,0);
    const p=g.limit>0?spent/g.limit:0;
    if(p>=1)al.push({t:'danger',m:`⛔ Meta ultrapassada: <strong>${g.category}</strong> — ${BRL(spent)} de ${BRL(g.limit)} (${(p*100).toFixed(0)}%)`});
    else if(p>=0.8)al.push({t:'warning',m:`⚠️ <strong>${g.category}</strong> em ${(p*100).toFixed(0)}% da meta (${BRL(spent)} / ${BRL(g.limit)})`});
  });
  sec.innerHTML=al.map(a=>`<div class="alert ${a.t}">${a.m}</div>`).join('');
}

function renderRecentTx(txs){
  const el=document.getElementById('recent-tx');
  const sorted=[...txs].sort((a,b)=>b.date.localeCompare(a.date)).slice(0,5);
  if(!sorted.length){el.innerHTML='<div class="empty-st"><div class="eico">📭</div><h3>Nenhuma transação</h3><p>Adicione sua primeira transação neste mês.</p></div>';return;}
  el.innerHTML='<table class="dtable"><tbody>'+sorted.map(t=>`<tr><td style="width:86px;color:var(--text-muted);font-size:12px">${fmtDate(t.date)}</td><td><b>${esc(t.description)}</b></td><td><span class="cat-badge">${esc(t.category)}</span></td><td class="${t.type==='income'?'apos':'aneg'}">${t.type==='income'?'+':'-'}${BRL(t.amount)}</td></tr>`).join('')+'</tbody></table>';
}

/* ================================================================
   TRANSACTIONS TABLE
   ================================================================ */
function renderTransactions(){
  let txs=txPeriod(S.currentYear,S.currentMonth);
  if(S.filterType)txs=txs.filter(t=>t.type===S.filterType);
  if(S.filterCat)txs=txs.filter(t=>t.category===S.filterCat);
  if(S.filterPerson)txs=txs.filter(t=>t.person===S.filterPerson);
  if(S.searchQ){const q=S.searchQ.toLowerCase();txs=txs.filter(t=>t.description.toLowerCase().includes(q)||t.category.toLowerCase().includes(q)||(t.notes||'').toLowerCase().includes(q));}
  txs.sort((a,b)=>{let va=a[S.sortField],vb=b[S.sortField];if(S.sortField==='amount'){va=a.amount;vb=b.amount;}if(typeof va==='string')return S.sortDir==='asc'?va.localeCompare(vb):vb.localeCompare(va);return S.sortDir==='asc'?va-vb:vb-va;});
  const tbody=document.getElementById('tx-tbody');
  if(!txs.length){tbody.innerHTML='<tr><td colspan="6"><div class="empty-st"><div class="eico">🔍</div><h3>Nenhum resultado</h3><p>Ajuste os filtros ou adicione transações.</p></div></td></tr>';}
  else{tbody.innerHTML=txs.map(t=>`<tr><td style="color:var(--text-muted);font-size:12px;white-space:nowrap">${fmtDate(t.date)}</td><td><b>${esc(t.description)}</b>${t.notes?`<div style="font-size:11px;color:var(--text-muted)">${esc(t.notes)}</div>`:''}</td><td><span class="cat-badge">${esc(t.category)}</span></td><td style="display:flex;align-items:center;gap:6px"><span class="person-chip">${esc(t.person).charAt(0)}</span><span style="font-size:12px">${esc(t.person)}</span></td><td style="text-align:right" class="${t.type==='income'?'apos':'aneg'}">${t.type==='income'?'+':'-'}${BRL(t.amount)}</td><td><div class="abtns"><button class="rbtn edit" onclick="editTx('${t.id}')" title="Editar">✏️</button><button class="rbtn del" onclick="deleteTx('${t.id}')" title="Excluir">🗑️</button></div></td></tr>`).join('');}
  document.getElementById('tx-count-lbl').textContent=`${txs.length} transação(ões) — Entradas: ${BRL(sumIncome(txs))} | Saídas: ${BRL(sumExpense(txs))} | Saldo: ${BRL(sumIncome(txs)-sumExpense(txs))}`;
  document.querySelectorAll('.dtable th[data-sort]').forEach(th=>{th.classList.toggle('sorted',th.dataset.sort===S.sortField)});
}

function populateFilters(){
  const fc=document.getElementById('f-cat'),fp=document.getElementById('f-person');
  fc.innerHTML='<option value="">Categoria</option>'+S.categories.map(c=>`<option value="${esc(c)}">${esc(c)}</option>`).join('');
  fp.innerHTML='<option value="">Pessoa</option>'+S.people.map(p=>`<option value="${esc(p)}">${esc(p)}</option>`).join('');
  if(S.filterCat)fc.value=S.filterCat;
  if(S.filterPerson)fp.value=S.filterPerson;
}

/* ================================================================
   "OUTROS" DYNAMIC FIELD (saves to list)
   ================================================================ */
function populateTxForm(){
  const cat=document.getElementById('tx-cat');
  const per=document.getElementById('tx-person');
  cat.innerHTML=S.categories.map(c=>`<option value="${esc(c)}">${esc(c)}</option>`).join('')+`<option value="__outros__">✏️ Outros (nova categoria…)</option>`;
  per.innerHTML=S.people.map(p=>`<option value="${esc(p)}">${esc(p)}</option>`).join('')+`<option value="__outros__">✏️ Outros (novo nome…)</option>`;
  // Reset outros fields
  ['cat','person'].forEach(k=>{
    document.getElementById('tx-'+k+'-outros').value='';
    document.getElementById('ow-'+k).classList.remove('visible');
  });
}
function populateGoalForm(){
  const cat=document.getElementById('goal-cat');
  cat.innerHTML=S.categories.map(c=>`<option value="${esc(c)}">${esc(c)}</option>`).join('')+`<option value="__outros__">✏️ Outros (nova categoria…)</option>`;
  document.getElementById('goal-cat-outros').value='';
  document.getElementById('ow-goalcat').classList.remove('visible');
}

function onFieldOtros(field,sel){
  // field: 'cat' | 'person' | 'goalcat'
  const owId={cat:'ow-cat',person:'ow-person',goalcat:'ow-goalcat'}[field];
  const inpId={cat:'tx-cat-outros',person:'tx-person-outros',goalcat:'goal-cat-outros'}[field];
  const wrap=document.getElementById(owId);
  const inp=document.getElementById(inpId);
  if(sel.value==='__outros__'){
    wrap.classList.add('visible');
    setTimeout(()=>inp.focus(),60);
  } else {
    wrap.classList.remove('visible');
    inp.value='';
  }
}

// Resolve "Outros" value and optionally save to list
function resolveField(selectId,outrosId,listKey){
  const sel=document.getElementById(selectId);
  const inp=document.getElementById(outrosId);
  if(sel.value==='__outros__'){
    const val=inp.value.trim();
    if(!val)return{error:`Digite um valor no campo "${selectId.includes('cat')?'Categoria':'Pessoa'}"`,value:null};
    // Save to list if not already present
    if(!S[listKey].includes(val)){
      S[listKey].push(val);
    }
    return{error:null,value:val};
  }
  return{error:null,value:sel.value};
}

/* ================================================================
   TRANSACTION CRUD
   ================================================================ */
function openAddModal(){
  S.editingTxId=null;
  document.getElementById('m-tx-title').textContent='Nova Transação';
  document.getElementById('tx-desc').value='';
  document.getElementById('tx-amt').value='';
  document.getElementById('tx-date').value=new Date().toISOString().split('T')[0];
  document.getElementById('tx-notes').value='';
  populateTxForm();
  setType('income');
  openModal('m-tx');
}

function editTx(id){
  const t=S.transactions.find(x=>x.id===id);if(!t)return;
  S.editingTxId=id;
  document.getElementById('m-tx-title').textContent='Editar Transação';
  populateTxForm();
  setType(t.type);
  document.getElementById('tx-desc').value=t.description;
  document.getElementById('tx-amt').value=t.amount;
  document.getElementById('tx-date').value=t.date;
  document.getElementById('tx-notes').value=t.notes||'';
  // Set category
  const catSel=document.getElementById('tx-cat');
  if(S.categories.includes(t.category)){catSel.value=t.category;document.getElementById('ow-cat').classList.remove('visible');}
  else{catSel.value='__outros__';document.getElementById('tx-cat-outros').value=t.category;document.getElementById('ow-cat').classList.add('visible');}
  // Set person
  const perSel=document.getElementById('tx-person');
  if(S.people.includes(t.person)){perSel.value=t.person;document.getElementById('ow-person').classList.remove('visible');}
  else{perSel.value='__outros__';document.getElementById('tx-person-outros').value=t.person;document.getElementById('ow-person').classList.add('visible');}
  openModal('m-tx');
}

function saveTx(){
  const desc=document.getElementById('tx-desc').value.trim();
  const amtRaw=parseFloat(document.getElementById('tx-amt').value);
  const date=document.getElementById('tx-date').value;
  const notes=document.getElementById('tx-notes').value.trim();
  const type=document.getElementById('tb-income').classList.contains('active')?'income':'expense';
  if(!desc||isNaN(amtRaw)||amtRaw<=0||!date){toast('Preencha todos os campos obrigatórios.','error');return;}
  const catRes=resolveField('tx-cat','tx-cat-outros','categories');
  if(catRes.error){toast(catRes.error,'error');return;}
  const perRes=resolveField('tx-person','tx-person-outros','people');
  if(perRes.error){toast(perRes.error,'error');return;}
  const amount=Math.abs(amtRaw);
  const category=catRes.value, person=perRes.value;
  if(S.editingTxId){
    const idx=S.transactions.findIndex(t=>t.id===S.editingTxId);
    if(idx>-1)S.transactions[idx]={...S.transactions[idx],description:desc,amount,date,category,person,notes,type};
    toast('Transação atualizada! ✅','success');
  } else {
    S.transactions.push({id:uid(),description:desc,amount,date,category,person,notes,type});
    toast('Transação adicionada! ✅','success');
  }
  closeModal('m-tx');
  renderAll();
}

function deleteTx(id){
  if(!confirm('Excluir esta transação?'))return;
  S.transactions=S.transactions.filter(t=>t.id!==id);
  toast('Transação removida.','');
  renderAll();
}

let _txType='income';
function setType(t){
  _txType=t;
  document.getElementById('tb-income').classList.toggle('active',t==='income');
  document.getElementById('tb-expense').classList.toggle('active',t==='expense');
}

/* ================================================================
   GOALS CRUD
   ================================================================ */
function openGoalModal(){
  populateGoalForm();
  document.getElementById('goal-limit').value='';
  openModal('m-goal');
}
function saveGoal(){
  const catRes=resolveField('goal-cat','goal-cat-outros','categories');
  if(catRes.error){toast(catRes.error,'error');return;}
  const limit=parseFloat(document.getElementById('goal-limit').value);
  if(isNaN(limit)||limit<=0){toast('Digite um limite válido.','error');return;}
  const category=catRes.value;
  const ex=S.goals.findIndex(g=>g.category===category);
  if(ex>-1)S.goals[ex].limit=limit;
  else S.goals.push({id:uid(),category,limit});
  toast('Meta salva! 🎯','success');
  closeModal('m-goal');
  renderAll();
}
function deleteGoal(id){S.goals=S.goals.filter(g=>g.id!==id);toast('Meta removida.','');renderAll();}

function renderGoals(){
  const grid=document.getElementById('goals-grid');
  const empty=document.getElementById('goals-empty');
  const txs=txPeriod(S.currentYear,S.currentMonth);
  if(!S.goals.length){grid.innerHTML='';empty.style.display='block';return;}
  empty.style.display='none';
  grid.innerHTML=S.goals.map(g=>{
    const spent=txs.filter(t=>t.type==='expense'&&t.category===g.category).reduce((s,t)=>s+t.amount,0);
    const p=g.limit>0?Math.min(spent/g.limit,1.5):0;
    const cls=p>=1?'over':p>=0.8?'warn':'safe';
    const pd=g.limit>0?(spent/g.limit*100).toFixed(0)+'%':'—';
    return`<div class="goal-card"><div class="goal-card-hdr"><span class="goal-cat">${esc(g.category)}</span><span class="goal-pct ${cls}">${pd}</span></div><div class="gbar-bg"><div class="gbar-fill ${cls}" style="width:${Math.min(p*100,100)}%"></div></div><div class="gbar-lbl"><span>${BRL(spent)}</span><span>Limite: ${BRL(g.limit)}</span></div><button onclick="deleteGoal('${g.id}')" style="margin-top:8px;font-size:11px;background:none;border:none;cursor:pointer;color:var(--text-muted)">🗑 Remover</button></div>`;
  }).join('');
}

/* ================================================================
   REPORTS
   ================================================================ */
function renderReports(){
  const all=S.transactions;
  const catM={};all.forEach(t=>{if(!catM[t.category])catM[t.category]={e:0,i:0};catM[t.category][t.type==='expense'?'e':'i']+=t.amount});
  const totE=Object.values(catM).reduce((s,v)=>s+v.e,0);
  document.querySelector('#rpt-cat tbody').innerHTML=Object.entries(catM).sort((a,b)=>b[1].e-a[1].e).map(([c,v])=>`<tr><td>${esc(c)}</td><td class="aneg">-${BRL(v.e)}</td><td class="apos">+${BRL(v.i)}</td><td>${totE>0?(v.e/totE*100).toFixed(1)+'%':'—'}</td></tr>`).join()||'<tr><td colspan="4" style="text-align:center;color:var(--text-muted);padding:20px">Sem dados</td></tr>';
  const perM={};all.forEach(t=>{if(!perM[t.person])perM[t.person]={i:0,e:0};perM[t.person][t.type==='income'?'i':'e']+=t.amount});
  document.querySelector('#rpt-person tbody').innerHTML=Object.entries(perM).sort((a,b)=>(b[1].i+b[1].e)-(a[1].i+a[1].e)).map(([p,v])=>{const b=v.i-v.e;return`<tr><td>${esc(p)}</td><td class="apos">+${BRL(v.i)}</td><td class="aneg">-${BRL(v.e)}</td><td class="${b>=0?'apos':'aneg'}">${b>=0?'+':'-'}${BRL(Math.abs(b))}</td></tr>`}).join()||'<tr><td colspan="4" style="text-align:center;color:var(--text-muted);padding:20px">Sem dados</td></tr>';
  const rows=[];for(let i=11;i>=0;i--){let m=S.currentMonth-i,y=S.currentYear;if(m<0){m+=12;y--;}const t=txPeriod(y,m);const inc=sumIncome(t),exp=sumExpense(t),bal=inc-exp;rows.push({lbl:MONTHS_PT[m]+'/'+y,inc,exp,bal});}
  document.querySelector('#rpt-month tbody').innerHTML=rows.map((r,i)=>{const prev=rows[i-1];const vb=prev?pctChange(r.bal,prev.bal):null;const vH=vb!==null?`<span style="color:${parseFloat(vb)>=0?'var(--secondary)':'var(--danger)'}">${parseFloat(vb)>=0?'▲':'▼'} ${Math.abs(vb)}%</span>`:'—';return`<tr><td style="font-weight:600">${r.lbl}</td><td class="apos">+${BRL(r.inc)}</td><td class="aneg">-${BRL(r.exp)}</td><td class="${r.bal>=0?'apos':'aneg'}">${r.bal>=0?'+':'-'}${BRL(Math.abs(r.bal))}</td><td>${vH}</td></tr>`}).join('');
}

/* ================================================================
   RENDER ALL
   ================================================================ */
function renderAll(){
  renderDashboard();
  renderTransactions();
  renderReports();
  renderGoals();
  populateFilters();
  persist();
}

/* ================================================================
   MODAL HELPERS
   ================================================================ */
function openModal(id){document.getElementById(id).classList.add('open')}
function closeModal(id){document.getElementById(id).classList.remove('open')}
document.querySelectorAll('.modal-overlay').forEach(o=>o.addEventListener('click',e=>{if(e.target===o)o.classList.remove('open')}));

/* ================================================================
   TABS
   ================================================================ */
function switchTab(name){
  document.querySelectorAll('.tab-btn').forEach(b=>b.classList.toggle('active',b.dataset.tab===name));
  document.querySelectorAll('.tab-pane').forEach(p=>p.classList.toggle('active',p.id==='tab-'+name));
  if(name==='transactions')renderTransactions();
  if(name==='reports')renderReports();
  if(name==='goals')renderGoals();
}
document.querySelectorAll('.tab-btn').forEach(b=>b.addEventListener('click',()=>switchTab(b.dataset.tab)));

/* ================================================================
   PERIOD NAVIGATION
   ================================================================ */
function chgPeriod(d){
  S.currentMonth+=d;
  if(S.currentMonth>11){S.currentMonth=0;S.currentYear++;}
  if(S.currentMonth<0){S.currentMonth=11;S.currentYear--;}
  renderAll();
}
['prev-m','prev-m-tx'].forEach(id=>document.getElementById(id)?.addEventListener('click',()=>chgPeriod(-1)));
['next-m','next-m-tx'].forEach(id=>document.getElementById(id)?.addEventListener('click',()=>chgPeriod(1)));

/* ================================================================
   TITLE
   ================================================================ */
document.getElementById('title-input').addEventListener('input',function(){
  S.title=this.value;document.title=this.value||'Dashboard Financeiro';
  document.getElementById('banner-title-text').textContent=S.title;
  persist();
});

/* ================================================================
   ADD / SAVE BUTTONS
   ================================================================ */
document.getElementById('btn-add-tx').addEventListener('click',openAddModal);
document.getElementById('btn-add-tx2').addEventListener('click',openAddModal);
document.getElementById('btn-save-tx').addEventListener('click',saveTx);
document.getElementById('btn-add-goal').addEventListener('click',openGoalModal);
document.getElementById('btn-save-goal').addEventListener('click',saveGoal);

/* SORT */
document.querySelectorAll('.dtable th[data-sort]').forEach(th=>{
  th.addEventListener('click',()=>{
    if(S.sortField===th.dataset.sort)S.sortDir=S.sortDir==='asc'?'desc':'asc';
    else{S.sortField=th.dataset.sort;S.sortDir='desc';}
    renderTransactions();
  });
});

/* FILTERS */
document.getElementById('f-type').addEventListener('change',function(){S.filterType=this.value;renderTransactions()});
document.getElementById('f-cat').addEventListener('change',function(){S.filterCat=this.value;renderTransactions()});
document.getElementById('f-person').addEventListener('change',function(){S.filterPerson=this.value;renderTransactions()});
document.getElementById('btn-clf').addEventListener('click',()=>{
  S.filterType=S.filterCat=S.filterPerson=S.searchQ='';
  ['f-type','f-cat','f-person','s-input'].forEach(id=>{const el=document.getElementById(id);if(el)el.value='';});
  renderTransactions();
});
document.getElementById('s-input').addEventListener('input',function(){S.searchQ=this.value;renderTransactions()});

/* ================================================================
   DARK MODE
   ================================================================ */
document.getElementById('btn-theme').addEventListener('click',()=>{
  S.darkMode=!S.darkMode;
  document.documentElement.setAttribute('data-theme',S.darkMode?'dark':'light');
  document.getElementById('btn-theme').textContent=S.darkMode?'☀️':'🌙';
  persist();
  setTimeout(rebuildCharts,60);
});

/* ================================================================
   CSV EXPORT
   ================================================================ */
document.getElementById('btn-export-csv').addEventListener('click',()=>{
  const rows=[['ID','Data','Descrição','Tipo','Categoria','Pessoa','Valor','Notas']];
  S.transactions.forEach(t=>rows.push([t.id,t.date,t.description,t.type==='income'?'Entrada':'Saída',t.category,t.person,t.amount,t.notes||'']));
  const csv=rows.map(r=>r.map(v=>`"${String(v).replace(/"/g,'""')}"`).join(',')).join('\n');
  dlFile('\uFEFF'+csv,'text/csv;charset=utf-8','financeiro.csv');
  toast('CSV exportado! 📤','success');
});

/* CSV IMPORT */
document.getElementById('btn-import-csv').addEventListener('click',()=>document.getElementById('file-csv').click());
document.getElementById('file-csv').addEventListener('change',function(){
  const f=this.files[0];if(!f)return;
  const r=new FileReader();
  r.onload=e=>{
    const lines=e.target.result.split('\n').slice(1).filter(l=>l.trim());
    let added=0;
    lines.forEach(line=>{
      const c=parseCSV(line);if(c.length<7)return;
      const[id,date,desc,type,cat,person,amt,notes]=c;
      if(!date||!desc||!amt)return;
      if(!S.transactions.find(t=>t.id===id)){
        S.transactions.push({id:id||uid(),date,description:desc,type:type==='Entrada'?'income':'expense',category:cat,person,amount:parseFloat(amt)||0,notes:notes||''});
        added++;
        if(!S.categories.includes(cat))S.categories.push(cat);
        if(!S.people.includes(person))S.people.push(person);
      }
    });
    toast(`${added} transações importadas! 📥`,'success');renderAll();
  };
  r.readAsText(f,'UTF-8');this.value='';
});
function parseCSV(l){const res=[];let cur='',inQ=false;for(let i=0;i<l.length;i++){if(l[i]==='"'){if(inQ&&l[i+1]==='"'){cur+='"';i++;}else inQ=!inQ;}else if(l[i]===','&&!inQ){res.push(cur.trim());cur='';}else cur+=l[i];}res.push(cur.trim());return res;}

/* JSON BACKUP */
document.getElementById('btn-backup').addEventListener('click',()=>{
  const d={title:S.title,transactions:S.transactions,categories:S.categories,people:S.people,goals:S.goals,colors:S.colors};
  dlFile(JSON.stringify(d,null,2),'application/json','backup-financeiro.json');
  toast('Backup gerado! 💾','success');
});
document.getElementById('btn-restore').addEventListener('click',()=>document.getElementById('file-json').click());
document.getElementById('file-json').addEventListener('change',function(){
  const f=this.files[0];if(!f)return;
  const r=new FileReader();
  r.onload=e=>{
    try{
      const d=JSON.parse(e.target.result);
      if(d.transactions)S.transactions=d.transactions;
      if(d.categories)S.categories=d.categories;
      if(d.people)S.people=d.people;
      if(d.goals)S.goals=d.goals;
      if(d.colors){S.colors=d.colors;applyColors();}
      if(d.title){S.title=d.title;document.getElementById('title-input').value=d.title;document.title=d.title;}
      toast('Backup restaurado! 📂','success');renderAll();
    }catch(er){toast('Arquivo inválido.','error');}
  };
  r.readAsText(f);this.value='';
});

function dlFile(content,mime,name){
  const blob=new Blob([content],{type:mime});
  const a=document.createElement('a');a.href=URL.createObjectURL(blob);a.download=name;a.click();
}

/* ================================================================
   PDF EXPORT WITH CHARTS
   ================================================================ */
document.getElementById('btn-pdf').addEventListener('click',exportPDF);
async function exportPDF(){
  toast('Gerando PDF com gráficos…','info');
  try{
    const{jsPDF}=window.jspdf;
    const doc=new jsPDF({orientation:'portrait',unit:'mm',format:'a4'});
    const txs=txPeriod(S.currentYear,S.currentMonth);
    const inc=sumIncome(txs),exp=sumExpense(txs),bal=inc-exp;
    const period=MONTHS_PT[S.currentMonth]+' '+S.currentYear;
    let y=16;

    // Header
    doc.setFillColor(37,99,235);doc.rect(0,0,210,28,'F');
    doc.setFontSize(20);doc.setFont('helvetica','bold');doc.setTextColor(255,255,255);
    doc.text(S.title,15,13);
    doc.setFontSize(10);doc.setFont('helvetica','normal');
    doc.text('Relatório — '+period,15,21);
    y=36;

    // Summary row
    doc.setFontSize(11);doc.setFont('helvetica','bold');
    doc.setFillColor(240,253,244);doc.rect(12,y-5,58,16,'F');
    doc.setTextColor(22,163,74);doc.text('ENTRADAS',14,y);
    doc.setFont('helvetica','normal');doc.setFontSize(13);doc.text(BRL(inc),14,y+7);

    doc.setFillColor(254,242,242);doc.rect(76,y-5,58,16,'F');
    doc.setFontSize(11);doc.setFont('helvetica','bold');doc.setTextColor(220,38,38);doc.text('SAÍDAS',78,y);
    doc.setFont('helvetica','normal');doc.setFontSize(13);doc.text(BRL(exp),78,y+7);

    const bc=bal>=0?[22,163,74]:[220,38,38];
    doc.setFillColor(bal>=0?240:254,bal>=0?253:242,bal>=0?244:242);doc.rect(140,y-5,58,16,'F');
    doc.setFontSize(11);doc.setFont('helvetica','bold');doc.setTextColor(...bc);doc.text('SALDO',142,y);
    doc.setFont('helvetica','normal');doc.setFontSize(13);doc.text(BRL(bal),142,y+7);
    y+=22;

    // Charts
    const chartIds=['ch-cat','ch-bal','ch-cmp'];
    const chartTitles=['Gastos por Categoria','Evolução do Saldo (6 meses)','Comparação Mensal'];
    for(let i=0;i<chartIds.length;i+=2){
      const pairs=chartIds.slice(i,i+2);
      let maxH=0;
      const imgs=[];
      for(const cid of pairs){
        const canvas=document.getElementById(cid);
        if(canvas){
          const png=canvas.toDataURL('image/png',1.0);
          const ratio=canvas.height/canvas.width;
          const w=86,h=w*ratio;
          imgs.push({png,w,h});
          if(h>maxH)maxH=h;
        }
      }
      if(y+maxH>265){doc.addPage();y=15;}
      imgs.forEach((img,j)=>{
        const x=14+j*98;
        doc.setFontSize(10);doc.setFont('helvetica','bold');doc.setTextColor(30,30,30);
        doc.text(chartTitles[i+j]||'',x,y);
        doc.addImage(img.png,'PNG',x,y+3,img.w,img.h);
      });
      y+=maxH+12;
    }

    // Transactions table
    if(y>250){doc.addPage();y=15;}
    doc.setDrawColor(229,231,235);doc.line(12,y,198,y);y+=6;
    doc.setFontSize(12);doc.setFont('helvetica','bold');doc.setTextColor(17,24,39);
    doc.text('Transações — '+period,14,y);y+=8;
    const cols=[14,42,100,135,158,176];
    ['Data','Descrição','Categoria','Pessoa','Tipo','Valor'].forEach((h,i)=>{doc.setFontSize(8);doc.setFont('helvetica','bold');doc.setTextColor(107,114,128);doc.text(h,cols[i],y)});
    y+=4;doc.setDrawColor(229,231,235);doc.line(12,y,198,y);y+=4;
    doc.setFont('helvetica','normal');doc.setFontSize(9);
    txs.sort((a,b)=>b.date.localeCompare(a.date)).forEach(t=>{
      if(y>278){doc.addPage();y=15;}
      doc.setTextColor(30,30,30);
      const row=[fmtDate(t.date),t.description.slice(0,22),t.category.slice(0,14),t.person.slice(0,12),t.type==='income'?'Entrada':'Saída',(t.type==='income'?'+':'-')+BRL(t.amount)];
      row.forEach((v,i)=>{
        if(i===4)doc.setTextColor(t.type==='income'?22:220,t.type==='income'?163:38,t.type==='income'?74:38);
        else if(i===5)doc.setTextColor(t.type==='income'?22:220,t.type==='income'?163:38,t.type==='income'?74:38);
        else doc.setTextColor(30,30,30);
        doc.text(String(v),cols[i],y);
      });
      y+=5.5;
    });

    // Footer
    doc.setFontSize(8);doc.setTextColor(150);
    doc.text('Gerado em '+new Date().toLocaleDateString('pt-BR')+' • '+S.title,14,289);

    doc.save('relatorio-'+period.replace(' ','-')+'.pdf');
    toast('PDF gerado com gráficos! 📄','success');
  }catch(er){console.error(er);toast('Erro ao gerar PDF.','error');}
}

/* ================================================================
   INIT
   ================================================================ */
function init(){
  loadState();
  document.getElementById('title-input').value=S.title||'Meu Controle Financeiro';
  document.title=S.title||'Dashboard Financeiro';
  if(S.darkMode){document.documentElement.setAttribute('data-theme','dark');document.getElementById('btn-theme').textContent='☀️';}
  document.getElementById('bg-opacity').value=S.bgOpacity||0;
  document.getElementById('bg-opacity-val').textContent=(S.bgOpacity||0)+'%';
  applyColors(S.colors||DEF_COLORS);
  ['bgImage','bannerImage','logoImage'].forEach(k=>applyImage(k));

  // Sample data on first load
  if(!S.transactions.length){
    const y=new Date().getFullYear(),m=String(new Date().getMonth()+1).padStart(2,'0');
    const samples=[
      {type:'income',description:'Salário',amount:5500,category:'Salário',person:'Eu',date:`${y}-${m}-05`},
      {type:'expense',description:'Aluguel',amount:1200,category:'Moradia',person:'Eu',date:`${y}-${m}-08`},
      {type:'expense',description:'Supermercado',amount:480,category:'Alimentação',person:'Família',date:`${y}-${m}-12`},
      {type:'expense',description:'Combustível',amount:210,category:'Transporte',person:'Eu',date:`${y}-${m}-14`},
      {type:'income',description:'Projeto freelance',amount:950,category:'Freelance',person:'Eu',date:`${y}-${m}-15`},
      {type:'expense',description:'Restaurante',amount:135,category:'Alimentação',person:'Parceiro(a)',date:`${y}-${m}-18`},
      {type:'expense',description:'Academia',amount:85,category:'Saúde',person:'Eu',date:`${y}-${m}-20`},
      {type:'expense',description:'Streaming',amount:68,category:'Lazer',person:'Eu',date:`${y}-${m}-22`},
    ];
    samples.forEach(s=>S.transactions.push({id:uid(),...s,notes:''}));
    S.goals.push({id:uid(),category:'Alimentação',limit:650});
    S.goals.push({id:uid(),category:'Lazer',limit:200});
  }
  renderAll();
}

init();
</script>
</body>
</html>
