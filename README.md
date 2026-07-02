<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1.0,viewport-fit=cover"/>
<meta name="apple-mobile-web-app-capable" content="yes"/>
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent"/>
<meta name="apple-mobile-web-app-title" content="UE BD追蹤"/>
<meta name="mobile-web-app-capable" content="yes"/>
<meta name="theme-color" content="#06C167"/>
<title>UberEats BD 上線追蹤系統</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css"/>
<style>
/* ── DESIGN TOKENS ─────────────────────────────────────────────────────────── */
:root{
  --ue-green:#06C167;
  --ue-green-dark:#049a52;
  --ue-green-dim:#e6f9f0;
  --ue-black:#000000;
  --ue-charcoal:#1a1a1a;
  --ue-slate:#2d2d2d;
  --surface:#ffffff;
  --surface-2:#f7f8fa;
  --surface-3:#f0f2f5;
  --border:#e4e8ed;
  --border-strong:#c8cdd6;
  --text-primary:#111318;
  --text-secondary:#5a6270;
  --text-muted:#97a0ae;
  --warn:#f59e0b;
  --warn-bg:#fffbeb;
  --danger:#ef4444;
  --danger-bg:#fef2f2;
  --info:#3b82f6;
  --info-bg:#eff6ff;
  --radius-sm:6px;
  --radius:10px;
  --radius-lg:14px;
  --radius-xl:20px;
  --shadow-sm:0 1px 3px rgba(0,0,0,.07),0 1px 2px rgba(0,0,0,.05);
  --shadow:0 4px 16px rgba(0,0,0,.08),0 1px 4px rgba(0,0,0,.05);
  --shadow-lg:0 12px 40px rgba(0,0,0,.12),0 4px 12px rgba(0,0,0,.06);
  --header-h:56px;
  --tab-h:48px;
  --safe-bottom:env(safe-area-inset-bottom,0px);
}
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent}
html{scroll-behavior:smooth}
body{font-family:-apple-system,BlinkMacSystemFont,'SF Pro Text','PingFang TC','Microsoft JhengHei','Segoe UI',sans-serif;background:var(--surface-2);color:var(--text-primary);font-size:14px;line-height:1.5;min-height:100vh;overflow-x:hidden}
button,input,select,textarea{font-family:inherit;font-size:inherit}
a{color:inherit;text-decoration:none}
input:focus,select:focus,textarea:focus,button:focus-visible{outline:2px solid var(--ue-green);outline-offset:1px}

/* ── LAYOUT ──────────────────────────────────────────────────────────────── */
#app{display:flex;flex-direction:column;min-height:100vh}

/* ── HEADER ──────────────────────────────────────────────────────────────── */
.header{background:var(--ue-black);height:var(--header-h);display:flex;align-items:center;padding:0 18px;position:sticky;top:0;z-index:300;gap:12px;padding-top:env(safe-area-inset-top,0)}
.header-logo{display:flex;align-items:center;gap:9px;font-weight:800;font-size:15px;color:#fff;letter-spacing:-.02em}
.logo-pill{background:var(--ue-green);color:#000;font-weight:900;font-size:10px;padding:4px 8px;border-radius:6px;letter-spacing:.04em}
.header-spacer{flex:1}
.header-month{font-size:12px;color:rgba(255,255,255,.55);font-weight:600;white-space:nowrap}
.header-actions{display:flex;gap:8px}
.icon-btn{width:34px;height:34px;border-radius:8px;border:none;background:rgba(255,255,255,.1);color:#fff;display:flex;align-items:center;justify-content:center;cursor:pointer;transition:background .15s;font-size:14px}
.icon-btn:hover{background:rgba(255,255,255,.2)}

/* ── TAB BAR ─────────────────────────────────────────────────────────────── */
.tab-bar{background:var(--ue-charcoal);display:flex;border-bottom:1px solid rgba(255,255,255,.06);position:sticky;top:var(--header-h);z-index:200;overflow-x:auto;scrollbar-width:none}
.tab-bar::-webkit-scrollbar{display:none}
.tab-btn{flex:none;padding:0 18px;height:var(--tab-h);display:flex;align-items:center;gap:7px;font-size:12.5px;font-weight:600;color:rgba(255,255,255,.45);cursor:pointer;border-bottom:2px solid transparent;white-space:nowrap;transition:all .2s;border:none;background:none}
.tab-btn:hover{color:rgba(255,255,255,.75)}
.tab-btn.active{color:var(--ue-green);border-bottom-color:var(--ue-green)}
.tab-btn i{font-size:13px}

/* ── VIEWS ───────────────────────────────────────────────────────────────── */
.view{display:none;padding:18px;max-width:1400px;margin:0 auto;width:100%;padding-bottom:calc(18px + var(--safe-bottom))}
.view.active{display:block}

/* ── DASHBOARD ───────────────────────────────────────────────────────────── */
.kpi-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:18px}
.kpi-card{background:var(--surface);border-radius:var(--radius-lg);padding:16px;box-shadow:var(--shadow-sm);border:1px solid var(--border);position:relative;overflow:hidden}
.kpi-card::before{content:"";position:absolute;top:0;left:0;right:0;height:3px}
.kpi-card.kpi-green::before{background:var(--ue-green)}
.kpi-card.kpi-warn::before{background:var(--warn)}
.kpi-card.kpi-danger::before{background:var(--danger)}
.kpi-card.kpi-info::before{background:var(--info)}
.kpi-num{font-size:28px;font-weight:800;letter-spacing:-.03em;line-height:1;margin-bottom:4px}
.kpi-label{font-size:11px;color:var(--text-secondary);font-weight:600;text-transform:uppercase;letter-spacing:.04em}
.kpi-sub{font-size:11px;color:var(--text-muted);margin-top:3px}
.kpi-icon{position:absolute;right:14px;top:14px;font-size:20px;opacity:.12}

.goal-banner{background:var(--ue-black);border-radius:var(--radius-lg);padding:18px 20px;margin-bottom:18px;display:flex;align-items:center;gap:18px;flex-wrap:wrap}
.goal-left{flex:1;min-width:180px}
.goal-title{font-size:13px;font-weight:800;color:#fff;margin-bottom:3px}
.goal-sub{font-size:11px;color:rgba(255,255,255,.45)}
.goal-bar-wrap{flex:2;min-width:120px}
.goal-bar-track{background:rgba(255,255,255,.1);border-radius:20px;height:8px;overflow:hidden;margin-bottom:6px}
.goal-bar-fill{height:100%;background:var(--ue-green);border-radius:20px;transition:width .5s cubic-bezier(.25,.46,.45,.94)}
.goal-bar-label{font-size:11px;color:rgba(255,255,255,.5);display:flex;justify-content:space-between}
.goal-count{font-size:26px;font-weight:800;color:var(--ue-green);white-space:nowrap;line-height:1}
.goal-count span{font-size:13px;color:rgba(255,255,255,.4);font-weight:600}

.section-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:12px}
.section-title{font-size:13px;font-weight:800;color:var(--text-primary);text-transform:uppercase;letter-spacing:.04em}

/* ── ALERTS LIST ─────────────────────────────────────────────────────────── */
.alert-list{display:flex;flex-direction:column;gap:8px;margin-bottom:18px}
.alert-item{background:var(--surface);border-radius:var(--radius);padding:12px 14px;border:1px solid var(--border);display:flex;align-items:center;gap:12px;box-shadow:var(--shadow-sm)}
.alert-item.warn{border-left:3px solid var(--warn);background:var(--warn-bg)}
.alert-item.danger{border-left:3px solid var(--danger);background:var(--danger-bg)}
.alert-dot{width:8px;height:8px;border-radius:50%;flex-shrink:0}
.alert-dot.warn{background:var(--warn)}
.alert-dot.danger{background:var(--danger)}
.alert-text{flex:1;font-size:12.5px;font-weight:600}
.alert-meta{font-size:11px;color:var(--text-muted)}

/* ── STORE LIST TABLE ────────────────────────────────────────────────────── */
.toolbar{display:flex;gap:8px;margin-bottom:14px;flex-wrap:wrap;align-items:center}
.search-wrap{position:relative;flex:1;min-width:180px}
.search-wrap i{position:absolute;left:10px;top:50%;transform:translateY(-50%);color:var(--text-muted);font-size:13px;pointer-events:none}
.search-input{width:100%;padding:9px 10px 9px 32px;border:1.5px solid var(--border);border-radius:var(--radius);background:var(--surface);font-size:13px;color:var(--text-primary);transition:border-color .2s}
.search-input:focus{border-color:var(--ue-green);background:#fff}
.filter-sel{padding:9px 10px;border:1.5px solid var(--border);border-radius:var(--radius);background:var(--surface);font-size:12.5px;color:var(--text-primary);cursor:pointer;min-width:110px}
.filter-sel:focus{border-color:var(--ue-green)}

.btn{display:inline-flex;align-items:center;gap:6px;padding:9px 15px;border:none;border-radius:var(--radius);font-size:12.5px;font-weight:700;cursor:pointer;transition:all .18s;white-space:nowrap}
.btn-primary{background:var(--ue-green);color:#000}
.btn-primary:hover{background:var(--ue-green-dark)}
.btn-outline{background:var(--surface);color:var(--text-primary);border:1.5px solid var(--border)}
.btn-outline:hover{border-color:var(--border-strong);background:var(--surface-3)}
.btn-sm{padding:6px 11px;font-size:11.5px}
.btn-danger{background:var(--danger-bg);color:var(--danger);border:1px solid #fca5a5}
.btn-danger:hover{background:var(--danger);color:#fff}

.card{background:var(--surface);border-radius:var(--radius-lg);box-shadow:var(--shadow-sm);border:1px solid var(--border);overflow:hidden}
.table-wrap{overflow-x:auto;-webkit-overflow-scrolling:touch}
table{width:100%;border-collapse:collapse;font-size:12.5px;min-width:900px}
thead th{background:var(--ue-charcoal);color:rgba(255,255,255,.8);padding:10px 12px;text-align:left;font-weight:700;font-size:11px;text-transform:uppercase;letter-spacing:.04em;white-space:nowrap;position:sticky;top:0}
thead th:first-child{border-radius:0}
tbody tr{border-bottom:1px solid var(--border);transition:background .12s}
tbody tr:hover{background:var(--surface-2)}
tbody tr:last-child{border-bottom:none}
tbody td{padding:10px 12px;vertical-align:middle}
.store-name{font-weight:700;color:var(--text-primary);white-space:nowrap}
.store-tag{display:inline-block;font-size:9.5px;padding:2px 6px;border-radius:4px;font-weight:700;background:var(--surface-3);color:var(--text-secondary);margin-top:2px;white-space:nowrap}
.store-tag.red{background:#fef2f2;color:#dc2626}

/* ── STATUS BADGES ───────────────────────────────────────────────────────── */
.badge{display:inline-flex;align-items:center;gap:3px;padding:3px 9px;border-radius:20px;font-size:10.5px;font-weight:700;white-space:nowrap}
.badge-green{background:var(--ue-green-dim);color:#047444}
.badge-warn{background:#fffbeb;color:#b45309}
.badge-danger{background:#fef2f2;color:#dc2626}
.badge-gray{background:var(--surface-3);color:var(--text-secondary)}
.badge-info{background:#eff6ff;color:#1d4ed8}
.badge-black{background:var(--ue-charcoal);color:#fff}

/* ── TIMER CHIP ──────────────────────────────────────────────────────────── */
.timer{display:inline-flex;align-items:center;gap:4px;font-size:10.5px;font-weight:700;padding:2px 7px;border-radius:5px;white-space:nowrap}
.timer.ok{background:#dcfce7;color:#15803d}
.timer.warn{background:#fef9c3;color:#854d0e;animation:pulse-warn 2s infinite}
.timer.over{background:#fee2e2;color:#b91c1c;animation:pulse-danger 1.5s infinite}
@keyframes pulse-warn{0%,100%{opacity:1}50%{opacity:.6}}
@keyframes pulse-danger{0%,100%{opacity:1}50%{opacity:.5}}

/* ── PIPELINE MINI ───────────────────────────────────────────────────────── */
.pipe{display:flex;align-items:center;gap:0;min-width:200px}
.pipe-step{width:22px;height:22px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:9px;font-weight:800;border:2px solid var(--border);background:var(--surface);color:var(--text-muted);position:relative;z-index:1;flex-shrink:0;transition:all .2s;cursor:default}
.pipe-step.done{background:var(--ue-green);border-color:var(--ue-green);color:#000}
.pipe-step.active{background:var(--warn);border-color:var(--warn);color:#fff;box-shadow:0 0 0 3px rgba(245,158,11,.2)}
.pipe-step.over{background:var(--danger);border-color:var(--danger);color:#fff;box-shadow:0 0 0 3px rgba(239,68,68,.2)}
.pipe-line{flex:1;height:2px;background:var(--border);min-width:6px}
.pipe-line.done{background:var(--ue-green)}

/* ── OPP LINK ────────────────────────────────────────────────────────────── */
.opp-link{color:var(--info);font-size:10.5px;display:inline-flex;align-items:center;gap:3px}
.opp-link:hover{text-decoration:underline}

/* ── ROW ACTIONS ─────────────────────────────────────────────────────────── */
.row-actions{display:flex;gap:4px;opacity:0;transition:opacity .15s}
tbody tr:hover .row-actions{opacity:1}

/* ── MODAL ───────────────────────────────────────────────────────────────── */
.overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.55);z-index:800;align-items:center;justify-content:center;padding:16px;backdrop-filter:blur(2px)}
.overlay.show{display:flex}
.modal{background:var(--surface);border-radius:var(--radius-xl);width:100%;max-width:640px;max-height:92vh;display:flex;flex-direction:column;box-shadow:var(--shadow-lg);animation:modal-in .22s cubic-bezier(.25,.46,.45,.94)}
@keyframes modal-in{from{opacity:0;transform:translateY(20px) scale(.97)}to{opacity:1;transform:none}}
.modal-head{background:var(--ue-black);color:#fff;padding:18px 20px;border-radius:var(--radius-xl) var(--radius-xl) 0 0;display:flex;align-items:center;justify-content:space-between;flex-shrink:0}
.modal-head h2{font-size:14px;font-weight:800;display:flex;align-items:center;gap:8px}
.modal-close{background:rgba(255,255,255,.1);border:none;color:#fff;width:28px;height:28px;border-radius:6px;cursor:pointer;display:flex;align-items:center;justify-content:center;font-size:14px;transition:background .15s}
.modal-close:hover{background:rgba(255,255,255,.2)}
.modal-pipeline{padding:14px 20px;background:var(--surface-2);border-bottom:1px solid var(--border);flex-shrink:0}
.modal-pipeline-title{font-size:10px;font-weight:800;color:var(--text-muted);text-transform:uppercase;letter-spacing:.06em;margin-bottom:10px}
.modal-pipe{display:flex;gap:6px;flex-wrap:wrap}
.modal-pipe-step{display:flex;align-items:center;gap:5px;padding:5px 10px;border-radius:7px;background:var(--surface);border:1.5px solid var(--border);font-size:11px}
.modal-pipe-step.done{border-color:#86efac;background:#f0fdf4}
.modal-pipe-step.active{border-color:#fcd34d;background:#fffbeb}
.modal-pipe-step.over{border-color:#fca5a5;background:#fef2f2}
.modal-pipe-step .step-name{font-weight:700}
.modal-pipe-step .step-date{font-size:10px;color:var(--text-muted)}
.modal-body{overflow-y:auto;padding:20px;flex:1}
.form-grid{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.form-full{grid-column:1/-1}
.form-sep{grid-column:1/-1;border-top:1.5px solid var(--border);padding-top:12px;font-size:10.5px;font-weight:800;color:var(--text-muted);text-transform:uppercase;letter-spacing:.06em;margin-top:4px}
.form-group{display:flex;flex-direction:column;gap:5px}
.form-label{font-size:11.5px;font-weight:700;color:var(--text-secondary)}
.form-input{padding:8px 11px;border:1.5px solid var(--border);border-radius:var(--radius);background:var(--surface-2);font-size:13px;color:var(--text-primary);transition:border-color .2s;width:100%}
.form-input:focus{border-color:var(--ue-green);background:#fff}
textarea.form-input{resize:vertical;min-height:60px}
.form-check{display:flex;align-items:center;gap:8px;padding:8px 11px;border:1.5px solid var(--border);border-radius:var(--radius);background:var(--surface-2);cursor:pointer}
.form-check input[type=checkbox]{width:16px;height:16px;accent-color:var(--ue-green);cursor:pointer}
.modal-foot{padding:14px 20px;border-top:1px solid var(--border);display:flex;gap:8px;justify-content:flex-end;flex-shrink:0;background:var(--surface-2);border-radius:0 0 var(--radius-xl) var(--radius-xl)}

/* ── CALENDAR ────────────────────────────────────────────────────────────── */
.cal-nav{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px;gap:12px}
.cal-title{font-size:16px;font-weight:800}
.cal-legend{display:flex;gap:12px;flex-wrap:wrap;font-size:11px}
.cal-legend-item{display:flex;align-items:center;gap:4px;color:var(--text-secondary)}
.cal-legend-dot{width:8px;height:8px;border-radius:50%}
.cal-grid-labels{display:grid;grid-template-columns:repeat(7,1fr);gap:2px;margin-bottom:4px}
.cal-day-label{text-align:center;font-size:10.5px;font-weight:700;color:var(--text-muted);padding:4px 0}
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr);gap:3px}
.cal-cell{background:var(--surface);border-radius:var(--radius);padding:7px 6px;min-height:76px;border:1.5px solid var(--border);cursor:pointer;transition:border-color .15s,background .15s}
.cal-cell:hover{border-color:var(--ue-green)}
.cal-cell.today{border-color:var(--ue-green);background:var(--ue-green-dim)}
.cal-cell.other-month{opacity:.35;background:var(--surface-3)}
.cal-cell.goal-zone{background:linear-gradient(135deg,#f0fdf4 0%,#fff 100%)}
.cal-date{font-size:11.5px;font-weight:700;margin-bottom:3px}
.cal-date.today-num{color:var(--ue-green)}
.cal-event{font-size:9.5px;font-weight:700;padding:1px 5px;border-radius:4px;margin-bottom:2px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;line-height:1.5}
.cal-event.ev-photo{background:#dbeafe;color:#1e40af}
.cal-event.ev-aml{background:#f3e8ff;color:#6b21a8}
.cal-event.ev-live{background:var(--ue-green-dim);color:#047444}
.cal-event.ev-contract{background:#fef9c3;color:#854d0e}
.cal-more{font-size:9px;color:var(--text-muted);margin-top:1px}
.goal-zone-label{grid-column:1/-1;text-align:center;font-size:10.5px;color:var(--ue-green);font-weight:700;padding:4px 0;letter-spacing:.04em}

/* ── GANTT ───────────────────────────────────────────────────────────────── */
.gantt-legend{display:flex;gap:12px;flex-wrap:wrap;margin-bottom:12px;font-size:11px}
.gantt-legend-item{display:flex;align-items:center;gap:5px;color:var(--text-secondary)}
.gantt-legend-bar{width:20px;height:8px;border-radius:3px}
.gantt-wrap{overflow-x:auto;-webkit-overflow-scrolling:touch}
.gantt-inner{min-width:800px}
.gantt-header{display:flex;background:var(--ue-charcoal);color:rgba(255,255,255,.7);font-size:10px;font-weight:700;border-radius:var(--radius) var(--radius) 0 0}
.gantt-store-col{width:160px;flex-shrink:0;padding:8px 12px;border-right:1px solid rgba(255,255,255,.08)}
.gantt-days-header{flex:1;display:flex}
.gantt-day-label{flex:1;text-align:center;padding:8px 0;border-right:1px solid rgba(255,255,255,.05)}
.gantt-day-label.today-col{color:var(--ue-green)}
.gantt-day-label.weekend{opacity:.3}
.gantt-row{display:flex;border-bottom:1px solid var(--border);background:var(--surface);position:relative;min-height:46px;align-items:center}
.gantt-row:hover{background:var(--surface-2)}
.gantt-row:last-child{border-bottom:none;border-radius:0 0 var(--radius) var(--radius)}
.gantt-store-name{width:160px;flex-shrink:0;padding:8px 12px;font-size:12px;font-weight:700;border-right:1px solid var(--border);overflow:hidden;text-overflow:ellipsis;white-space:nowrap;height:100%;display:flex;align-items:center}
.gantt-days-row{flex:1;display:flex;position:relative;height:100%}
.gantt-day-cell{flex:1;border-right:1px solid var(--border);height:100%}
.gantt-day-cell.today-col{background:rgba(6,193,103,.05)}
.gantt-day-cell.weekend{background:rgba(0,0,0,.02)}
.gantt-bar{position:absolute;height:14px;border-radius:4px;top:50%;transform:translateY(-50%);display:flex;align-items:center;padding:0 5px;font-size:9px;font-weight:700;white-space:nowrap;overflow:hidden;color:#fff;z-index:2;pointer-events:none}
.gantt-bar.b-contract{background:#06C167;color:#000}
.gantt-bar.b-menu{background:#3b82f6}
.gantt-bar.b-photo{background:#f59e0b;color:#000}
.gantt-bar.b-aml{background:#8b5cf6}
.gantt-bar.b-tablet{background:#ef4444}

/* ── TOAST ───────────────────────────────────────────────────────────────── */
.toast{position:fixed;bottom:calc(20px + var(--safe-bottom));left:50%;transform:translateX(-50%);background:var(--ue-charcoal);color:#fff;padding:10px 18px;border-radius:var(--radius-lg);font-size:13px;font-weight:600;box-shadow:var(--shadow-lg);z-index:1000;animation:toast-in .3s ease;display:flex;align-items:center;gap:8px;white-space:nowrap}
@keyframes toast-in{from{opacity:0;transform:translateX(-50%) translateY(10px)}to{opacity:1;transform:translateX(-50%) translateY(0)}}

/* ── EMPTY STATE ─────────────────────────────────────────────────────────── */
.empty-state{text-align:center;padding:52px 20px;color:var(--text-muted)}
.empty-state i{font-size:36px;margin-bottom:12px;opacity:.4}
.empty-state p{font-size:13px}

/* ── RESPONSIVE ──────────────────────────────────────────────────────────── */
@media(max-width:768px){
  .kpi-grid{grid-template-columns:repeat(2,1fr)}
  .form-grid{grid-template-columns:1fr}
  .form-full{grid-column:auto}
  .view{padding:12px;padding-bottom:calc(12px + var(--safe-bottom))}
  .goal-banner{flex-direction:column;gap:10px}
  .goal-count{font-size:20px}
  .header{padding:0 12px}
  .tab-btn{padding:0 13px;font-size:11.5px}
  .gantt-store-col,.gantt-store-name{width:110px}
}
@media(max-width:480px){
  .kpi-grid{grid-template-columns:repeat(2,1fr);gap:8px}
  .kpi-num{font-size:22px}
  .row-actions{opacity:1}
}
</style>
</head>
<body>
<div id="app">
  <!-- HEADER -->
  <header class="header">
    <div class="header-logo">
      <div class="logo-pill">UE</div>
      BD 追蹤系統
    </div>
    <div class="header-spacer"></div>
    <div class="header-month" id="headerMonth"></div>
    <div class="header-actions">
      <button class="icon-btn" onclick="exportJSON()" title="匯出備份"><i class="fa fa-download"></i></button>
      <label class="icon-btn" title="匯入備份" style="cursor:pointer"><i class="fa fa-upload"></i><input type="file" accept=".json" style="display:none" onchange="importJSON(event)"/></label>
      <button class="icon-btn btn-primary" onclick="openAdd()" title="新增店家" style="background:var(--ue-green);color:#000;width:auto;padding:0 12px;gap:5px;font-weight:700;font-size:12px"><i class="fa fa-plus"></i><span class="hide-sm">新增店家</span></button>
    </div>
  </header>

  <!-- TAB BAR -->
  <nav class="tab-bar">
    <button class="tab-btn active" onclick="switchTab('dashboard')" id="tab-dashboard"><i class="fa fa-chart-line"></i> 儀表板</button>
    <button class="tab-btn" onclick="switchTab('stores')" id="tab-stores"><i class="fa fa-store"></i> 店家列表</button>
    <button class="tab-btn" onclick="switchTab('gantt')" id="tab-gantt"><i class="fa fa-bars-staggered"></i> 甘特圖</button>
    <button class="tab-btn" onclick="switchTab('calendar')" id="tab-calendar"><i class="fa fa-calendar-days"></i> 行事曆</button>
  </nav>

  <!-- DASHBOARD VIEW -->
  <div class="view active" id="view-dashboard">
    <!-- KPI Cards -->
    <div class="kpi-grid" id="kpiGrid"></div>
    <!-- Goal Banner -->
    <div class="goal-banner">
      <div class="goal-left">
        <div class="goal-title">📦 本月前兩週目標進度</div>
        <div class="goal-sub" id="goalSub">截止：<span id="goalDeadline">—</span>｜需 16 間完成並收到平板</div>
      </div>
      <div class="goal-bar-wrap">
        <div class="goal-bar-track"><div class="goal-bar-fill" id="goalBar" style="width:0%"></div></div>
        <div class="goal-bar-label"><span id="goalBarLeft">還差 16 間</span><span>目標 16 間</span></div>
      </div>
      <div class="goal-count"><span id="goalDone">0</span><span>/16</span></div>
    </div>
    <!-- Alerts -->
    <div class="section-header"><div class="section-title"><i class="fa fa-triangle-exclamation" style="color:var(--warn);margin-right:5px"></i>待處理警示</div></div>
    <div class="alert-list" id="alertList"></div>
    <!-- Recent activity mini table -->
    <div class="section-header"><div class="section-title"><i class="fa fa-clock-rotate-left" style="color:var(--ue-green);margin-right:5px"></i>近期進行中店家</div><button class="btn btn-sm btn-outline" onclick="switchTab('stores')">查看全部 <i class="fa fa-arrow-right"></i></button></div>
    <div class="card"><div class="table-wrap"><table><thead><tr><th>店名</th><th>合約狀態</th><th>流程進度</th><th>預計上線</th><th>時效</th></tr></thead><tbody id="dashboardTableBody"></tbody></table></div></div>
  </div>

  <!-- STORES VIEW -->
  <div class="view" id="view-stores">
    <div class="toolbar">
      <div class="search-wrap"><i class="fa fa-magnifying-glass"></i><input class="search-input" id="searchInput" placeholder="搜尋店名…" oninput="renderStores()"/></div>
      <select class="filter-sel" id="filterContract" onchange="renderStores()">
        <option value="">合約：全部</option>
        <option value="pending">已寄出</option>
        <option value="signed">已回簽</option>
        <option value="abnormal">異常</option>
      </select>
      <select class="filter-sel" id="filterAML" onchange="renderStores()">
        <option value="">AML：全部</option>
        <option value="none">未上傳</option>
        <option value="uploaded">已上傳</option>
        <option value="abnormal">異常</option>
        <option value="manual_approval">人工審核</option>
        <option value="approved">通過</option>
      </select>
      <select class="filter-sel" id="filterWarn" onchange="renderStores()">
        <option value="">全部狀態</option>
        <option value="overdue">逾期警示</option>
        <option value="7d">合約7天內</option>
      </select>
      <button class="btn btn-primary" onclick="openAdd()"><i class="fa fa-plus"></i> 新增</button>
    </div>
    <div class="card"><div class="table-wrap">
      <table>
        <thead><tr>
          <th>注意</th><th>店名 / OPP</th><th>合約寄出</th><th>合約狀態</th>
          <th>菜單</th><th>拍照時間</th><th>AML狀態</th><th>缺件</th>
          <th>CW</th><th>流程進度</th><th>預計上線</th><th>操作</th>
        </tr></thead>
        <tbody id="storesTableBody"></tbody>
      </table>
    </div></div>
  </div>

  <!-- GANTT VIEW -->
  <div class="view" id="view-gantt">
    <div class="gantt-legend">
      <div class="gantt-legend-item"><div class="gantt-legend-bar" style="background:#06C167"></div>合約/回簽</div>
      <div class="gantt-legend-item"><div class="gantt-legend-bar" style="background:#3b82f6"></div>菜單製作</div>
      <div class="gantt-legend-item"><div class="gantt-legend-bar" style="background:#f59e0b"></div>拍攝</div>
      <div class="gantt-legend-item"><div class="gantt-legend-bar" style="background:#8b5cf6"></div>AML審核</div>
      <div class="gantt-legend-item"><div class="gantt-legend-bar" style="background:#ef4444"></div>平板寄出</div>
    </div>
    <div class="card gantt-wrap"><div class="gantt-inner" id="ganttInner"></div></div>
  </div>

  <!-- CALENDAR VIEW -->
  <div class="view" id="view-calendar">
    <div class="cal-nav">
      <button class="btn btn-outline btn-sm" onclick="calPrev()"><i class="fa fa-chevron-left"></i></button>
      <div class="cal-title" id="calTitle"></div>
      <button class="btn btn-outline btn-sm" onclick="calNext()"><i class="fa fa-chevron-right"></i></button>
    </div>
    <div class="cal-legend">
      <div class="cal-legend-item"><div class="cal-legend-dot" style="background:#3b82f6"></div>拍照</div>
      <div class="cal-legend-item"><div class="cal-legend-dot" style="background:#8b5cf6"></div>AML</div>
      <div class="cal-legend-item"><div class="cal-legend-dot" style="background:#06C167"></div>開通/上線</div>
      <div class="cal-legend-item"><div class="cal-legend-dot" style="background:#f59e0b"></div>合約回簽</div>
      <div class="cal-legend-item" style="background:linear-gradient(90deg,#f0fdf4,#fff);padding:2px 7px;border-radius:5px;font-size:10.5px;color:var(--ue-green);font-weight:700">🎯 前兩週目標區</div>
    </div>
    <div class="cal-grid-labels">
      <div class="cal-day-label">日</div><div class="cal-day-label">一</div><div class="cal-day-label">二</div>
      <div class="cal-day-label">三</div><div class="cal-day-label">四</div><div class="cal-day-label">五</div><div class="cal-day-label">六</div>
    </div>
    <div class="cal-grid" id="calGrid"></div>
  </div>
</div>

<!-- MODAL -->
<div class="overlay" id="storeModal" onclick="if(event.target===this)closeModal()">
  <div class="modal">
    <div class="modal-head">
      <h2 id="modalTitle"><i class="fa fa-store"></i> 新增店家</h2>
      <button class="modal-close" onclick="closeModal()"><i class="fa fa-xmark"></i></button>
    </div>
    <div class="modal-pipeline" id="modalPipeline" style="display:none">
      <div class="modal-pipeline-title">流程進度一覽</div>
      <div class="modal-pipe" id="modalPipeSteps"></div>
    </div>
    <div class="modal-body">
      <div class="form-grid">
        <div class="form-full form-group">
          <label class="form-label">店名 *</label>
          <input class="form-input" id="f-name" placeholder="店家名稱"/>
        </div>
        <div class="form-group">
          <label class="form-label">需注意事項</label>
          <select class="form-input" id="f-tag">
            <option value="">—</option>
            <option value="6月ft">6月 ft</option>
            <option value="6月ft sunmi">6月 ft sunmi</option>
            <option value="6月ft(推薦)">6月 ft（推薦）</option>
            <option value="違規">違規</option>
            <option value="其他">其他</option>
          </select>
        </div>
        <div class="form-group">
          <label class="form-label">OPP 連結</label>
          <input class="form-input" id="f-opp" placeholder="https://…" type="url"/>
        </div>
        <div class="form-group">
          <label class="form-label">店家網頁 / 菜單連結</label>
          <input class="form-input" id="f-web" placeholder="https://…" type="url"/>
        </div>
        <!-- 合約 -->
        <div class="form-sep form-full"><i class="fa fa-file-contract" style="color:var(--ue-green)"></i> 合約流程</div>
        <div class="form-group">
          <label class="form-label">合約寄出日期</label>
          <input class="form-input" id="f-contractSent" type="date" onchange="updateModalPipeline()"/>
        </div>
        <div class="form-group">
          <label class="form-label">合約狀態</label>
          <select class="form-input" id="f-contractStatus" onchange="updateModalPipeline()">
            <option value="">—</option>
            <option value="pending">已寄出（待回簽）</option>
            <option value="signed">已回簽 ✓</option>
            <option value="abnormal">異常</option>
          </select>
        </div>
        <div class="form-group">
          <label class="form-label">回簽日期</label>
          <input class="form-input" id="f-contractSigned" type="date" onchange="updateModalPipeline()"/>
        </div>
        <!-- 菜單拍攝 -->
        <div class="form-sep form-full"><i class="fa fa-camera" style="color:#f59e0b"></i> 菜單 & 拍攝</div>
        <div class="form-group">
          <label class="form-label">菜單是否完成</label>
          <select class="form-input" id="f-menuDone" onchange="updateModalPipeline()">
            <option value="">—</option>
            <option value="yes">是 ✓</option>
            <option value="no">否</option>
            <option value="making">製作中</option>
          </select>
        </div>
        <div class="form-group">
          <label class="form-label">菜單完成日期</label>
          <input class="form-input" id="f-menuDate" type="date" onchange="updateModalPipeline()"/>
        </div>
        <div class="form-group">
          <label class="form-label">預約拍照時間</label>
          <input class="form-input" id="f-photoDate" placeholder="日期時間 或 AI / 缺照片"/>
        </div>
        <div class="form-group">
          <label class="form-label">拍攝完成日期</label>
          <input class="form-input" id="f-photoDone" type="date" onchange="updateModalPipeline()"/>
        </div>
        <!-- AML -->
        <div class="form-sep form-full"><i class="fa fa-shield-halved" style="color:#8b5cf6"></i> AML 審核（身分證 / 帳戶）</div>
        <div class="form-group">
          <label class="form-label">資料繳交日期（AML time）</label>
          <input class="form-input" id="f-amlDate" type="date" onchange="updateModalPipeline()"/>
        </div>
        <div class="form-group">
          <label class="form-label">AML 上傳狀態</label>
          <select class="form-input" id="f-amlUpload" onchange="updateModalPipeline()">
            <option value="none">未上傳</option>
            <option value="uploaded">已上傳</option>
            <option value="abnormal">異常</option>
          </select>
        </div>
        <div class="form-group">
          <label class="form-label">AML 審核結果</label>
          <select class="form-input" id="f-amlStatus" onchange="updateModalPipeline()">
            <option value="">—（審核中）</option>
            <option value="manual_approval">manual_approval</option>
            <option value="identity_not_verified">identity_not_verified</option>
            <option value="approved">approved ✓</option>
            <option value="rejected">rejected</option>
          </select>
        </div>
        <div class="form-group">
          <label class="form-label">AML 審核完成日</label>
          <input class="form-input" id="f-amlDoneDate" type="date" onchange="updateModalPipeline()"/>
        </div>
        <div class="form-full form-group">
          <label class="form-label">缺件備註</label>
          <input class="form-input" id="f-missing" placeholder="缺身分證、銀行帳戶名稱不符…"/>
        </div>
        <!-- 平板開通 -->
        <div class="form-sep form-full"><i class="fa fa-tablet-screen-button" style="color:#ef4444"></i> 平板 & 開通</div>
        <div class="form-group">
          <label class="form-label">CW 編號 <input type="checkbox" id="f-cw" style="margin-left:6px;accent-color:var(--ue-green);width:14px;height:14px"> <span style="font-weight:400;color:var(--text-muted)">已確認</span></label>
          <input class="form-input" id="f-cwNum" placeholder="shunfeng-SF519… / NOVO-UETW…"/>
        </div>
        <div class="form-group">
          <label class="form-label">平板寄出日期</label>
          <input class="form-input" id="f-tabletSent" type="date" onchange="updateModalPipeline()"/>
        </div>
        <div class="form-group">
          <label class="form-label">平板收到日期</label>
          <input class="form-input" id="f-tabletReceived" type="date" onchange="updateModalPipeline()"/>
        </div>
        <div class="form-group">
          <label class="form-label">開通日期</label>
          <input class="form-input" id="f-liveDate" type="date" onchange="updateModalPipeline()"/>
        </div>
        <div class="form-group">
          <label class="form-label">預計上線 / 進度備註</label>
          <input class="form-input" id="f-eta" placeholder="月中、隨時、缺照片、已開通…"/>
        </div>
        <div class="form-full form-group">
          <label class="form-label">備註</label>
          <textarea class="form-input" id="f-notes" placeholder="其他說明…"></textarea>
        </div>
      </div>
    </div>
    <div class="modal-foot">
      <button class="btn btn-outline" onclick="closeModal()">取消</button>
      <button class="btn btn-primary" onclick="saveStore()"><i class="fa fa-floppy-disk"></i> 儲存</button>
    </div>
  </div>
</div>

<script>
// ─── DATA ─────────────────────────────────────────────────────────────────────
const KEY = 'ue_bd_v4';
let stores = [];
let editIdx = -1;
let calYear, calMonth;

const todayRaw = new Date(); todayRaw.setHours(0,0,0,0);
const TODAY = todayRaw;

function loadData(){
  try{ stores = JSON.parse(localStorage.getItem(KEY)||'[]'); }
  catch{ stores = []; }
  if(!stores.length) loadSample();
}
function saveData(){ localStorage.setItem(KEY, JSON.stringify(stores)); }

// ─── DATES ────────────────────────────────────────────────────────────────────
function pd(s){ if(!s) return null; const d=new Date(s); d.setHours(0,0,0,0); return isNaN(d)?null:d; }
function iso(d){ return d?d.toISOString().split('T')[0]:null; }
function fmtD(s){ if(!s) return '—'; const d=pd(s); if(!d) return s; return `${d.getMonth()+1}/${d.getDate()}`; }
function fmtDFull(s){ if(!s) return '—'; const d=pd(s); if(!d) return s; return `${d.getFullYear()}/${d.getMonth()+1}/${d.getDate()}`; }
function addWD(d,n){
  let dt=new Date(d); let a=0;
  while(a<n){ dt.setDate(dt.getDate()+1); const w=dt.getDay(); if(w!==0&&w!==6) a++; }
  return dt;
}
function wdDiff(from,to){
  let d=new Date(from); let cnt=0;
  while(d<to){ d.setDate(d.getDate()+1); const w=d.getDay(); if(w!==0&&w!==6) cnt++; }
  return cnt;
}
function calDiff(a,b){ return Math.round((b-a)/86400000); }

// ─── PIPELINE ─────────────────────────────────────────────────────────────────
function getPipeline(s){
  const cs    = pd(s.contractSent);
  const csig  = pd(s.contractSigned);
  const menu  = pd(s.menuDate);
  const photo = pd(s.photoDone);
  const amlD  = pd(s.amlDate);
  const amlOK = pd(s.amlDoneDate);
  const tab   = pd(s.tabletSent);
  const live  = pd(s.liveDate);

  const menuDue   = csig ? addWD(csig,3) : null;
  const photoDue  = csig ? addWD(csig,3) : null;
  const amlDue    = amlD ? addWD(amlD,3) : null;
  const prereqs   = [amlOK, s.menuDone==='yes'?menu:null, photo].filter(Boolean);
  const lastPre   = prereqs.length ? new Date(Math.max(...prereqs.map(d=>d.getTime()))) : null;
  const tabDue    = lastPre ? addWD(lastPre,4) : null;

  const wd  = (done,due,active)=>{ if(done) return 'done'; if(!active) return 'pending'; if(due&&TODAY>due) return 'over'; return 'active'; };
  // contract signed: calendar 7 days
  const csDue = cs ? new Date(cs.getTime()+7*86400000) : null;
  let sigSt='pending';
  if(s.contractStatus==='signed') sigSt='done';
  else if(cs && TODAY>csDue) sigSt='over';
  else if(cs) sigSt='active';

  return [
    { key:'contract', icon:'📄', label:'合約寄出', doneDate:cs, dueDate:null, status:cs?'done':'pending', note:'' },
    { key:'signed',   icon:'✍️', label:'已回簽',   doneDate:csig, dueDate:csDue, status:sigSt, note:cs&&!csig?`已寄${calDiff(cs,TODAY)}天`:''},
    { key:'menu',     icon:'🍽', label:'菜單完成', doneDate:s.menuDone==='yes'?menu:null, dueDate:menuDue, status:wd(s.menuDone==='yes',menuDue,csig), note:'' },
    { key:'photo',    icon:'📸', label:'拍攝完成', doneDate:photo, dueDate:photoDue, status:wd(photo,photoDue,csig), note:'' },
    { key:'aml',      icon:'🔐', label:'AML審核',  doneDate:amlOK, dueDate:amlDue,   status:wd(amlOK,amlDue,amlD), note:'' },
    { key:'tablet',   icon:'📦', label:'平板寄出', doneDate:tab,  dueDate:tabDue,   status:wd(tab,tabDue,lastPre), note:'' },
    { key:'live',     icon:'🟢', label:'已開通',   doneDate:live, dueDate:null, status:live?'done':tab?'active':'pending', note:'' },
  ];
}
function isOverdue(s){ return getPipeline(s).some(x=>x.status==='over'); }

// ─── TIMER CHIP ───────────────────────────────────────────────────────────────
function timerChip(s){
  const pipe = getPipeline(s);
  // Find most urgent overdue or active step with deadline
  const over = pipe.find(p=>p.status==='over'&&p.dueDate);
  if(over){
    const days = wdDiff(over.dueDate, TODAY);
    return `<span class="timer over"><i class="fa fa-circle-exclamation"></i>${over.label}超時${days}天</span>`;
  }
  const active = pipe.filter(p=>p.status==='active'&&p.dueDate);
  if(active.length){
    const a = active[0];
    const remain = wdDiff(TODAY, a.dueDate);
    if(remain<=1) return `<span class="timer warn"><i class="fa fa-clock"></i>${a.label}剩${remain}工作日</span>`;
    return `<span class="timer ok"><i class="fa fa-clock"></i>${a.label}剩${remain}天</span>`;
  }
  if(pipe[6].status==='done') return `<span class="badge badge-green"><i class="fa fa-check"></i>已完成</span>`;
  if(!s.contractSent) return `<span class="badge badge-gray">尚未開始</span>`;
  return `<span class="badge badge-gray">進行中</span>`;
}

// ─── RENDER PIPELINE MINI ─────────────────────────────────────────────────────
function renderPipe(s){
  const steps = getPipeline(s);
  let html = '<div class="pipe">';
  steps.forEach((st,i)=>{
    if(i>0) html+=`<div class="pipe-line${steps[i-1].status==='done'?' done':''}"></div>`;
    html+=`<div class="pipe-step ${st.status}" title="${st.label}${st.doneDate?' ✓'+fmtD(iso(st.doneDate)):''}${st.dueDate&&st.status!=='done'?' 期:'+fmtD(iso(st.dueDate)):''}">
      ${st.status==='done'?'✓':i+1}
    </div>`;
  });
  return html+'</div>';
}

// ─── CONTRACT STATUS BADGE ────────────────────────────────────────────────────
function contractBadge(s){
  if(s.contractStatus==='signed') return '<span class="badge badge-green"><i class="fa fa-check"></i> 已回簽</span>';
  if(s.contractStatus==='abnormal') return '<span class="badge badge-danger">異常</span>';
  if(s.contractStatus==='pending'){
    const cs=pd(s.contractSent);
    if(cs){
      const diff=calDiff(cs,TODAY);
      if(diff>7) return `<span class="badge badge-danger"><i class="fa fa-triangle-exclamation"></i> 逾${diff}天</span>`;
      if(diff>=5) return `<span class="badge badge-warn"><i class="fa fa-clock"></i> 已寄${diff}天</span>`;
      return `<span class="badge badge-info">已寄${diff}天</span>`;
    }
  }
  return '<span class="badge badge-gray">—</span>';
}

function amlBadge(s){
  const v=s.amlStatus||'';
  if(v==='approved'||v==='manual_approval') return `<span class="badge badge-green">${v==='approved'?'通過':'人工審'}</span>`;
  if(v==='identity_not_verified'||v==='rejected') return `<span class="badge badge-danger">${v==='rejected'?'拒絕':'未驗證'}</span>`;
  if(s.amlUpload==='uploaded') return '<span class="badge badge-info">審核中</span>';
  if(s.amlUpload==='abnormal') return '<span class="badge badge-danger">異常</span>';
  return '<span class="badge badge-gray">未上傳</span>';
}

function menuBadge(m){
  if(m==='yes') return '<span class="badge badge-green"><i class="fa fa-check"></i> 完成</span>';
  if(m==='making') return '<span class="badge badge-warn">製作中</span>';
  if(m==='no') return '<span class="badge badge-danger">否</span>';
  return '<span class="badge badge-gray">—</span>';
}

// ─── DASHBOARD ────────────────────────────────────────────────────────────────
function renderDashboard(){
  const m=TODAY.getMonth(), yr=TODAY.getFullYear();
  const fw=new Date(yr,m,14);
  const goalDone = stores.filter(s=>{
    const d=pd(s.tabletReceived||s.liveDate);
    return d&&d.getMonth()===m&&d.getFullYear()===yr&&d<=fw;
  }).length;

  // KPI
  const total = stores.length;
  const done = stores.filter(s=>s.liveDate).length;
  const inAML = stores.filter(s=>s.amlUpload==='uploaded'&&!s.amlDoneDate).length;
  const warnings = stores.filter(isOverdue).length;
  const contractPending = stores.filter(s=>s.contractStatus==='pending'&&s.contractSent&&calDiff(pd(s.contractSent),TODAY)>7).length;

  const kpis=[
    {n:total,l:'本月店家總數',sub:`已開通 ${done} 間`,cls:'kpi-green',icon:'fa-store'},
    {n:goalDone,l:'目標達成（前兩週）',sub:`還差 ${Math.max(0,16-goalDone)} 間`,cls:'kpi-info',icon:'fa-trophy'},
    {n:warnings+contractPending,l:'逾期 / 待追蹤',sub:`逾期${warnings}、合約逾7天${contractPending}`,cls:'kpi-danger',icon:'fa-triangle-exclamation'},
    {n:inAML,l:'AML 審核中',sub:'資料已上傳等審核',cls:'kpi-warn',icon:'fa-shield-halved'},
  ];
  document.getElementById('kpiGrid').innerHTML=kpis.map(k=>`
    <div class="kpi-card ${k.cls}">
      <i class="fa ${k.icon} kpi-icon"></i>
      <div class="kpi-num">${k.n}</div>
      <div class="kpi-label">${k.l}</div>
      <div class="kpi-sub">${k.sub}</div>
    </div>`).join('');

  // Goal banner
  document.getElementById('goalDone').textContent=goalDone;
  document.getElementById('goalBar').style.width=Math.min(100,goalDone/16*100)+'%';
  document.getElementById('goalDeadline').textContent=`${yr}/${m+1}/14`;
  const diff16=Math.max(0,16-goalDone);
  document.getElementById('goalBarLeft').textContent=diff16?`還差 ${diff16} 間`:'🎉 已達成！';

  // Alerts
  const alerts=[];
  stores.forEach(s=>{
    const cs=pd(s.contractSent);
    if(s.contractStatus==='pending'&&cs){
      const d=calDiff(cs,TODAY);
      if(d>7) alerts.push({type:'danger',text:`${s.name}`,meta:`合約寄出已 ${d} 天未回簽 ⚠️`});
      else if(d>=5) alerts.push({type:'warn',text:`${s.name}`,meta:`合約寄出 ${d} 天，即將逾期`});
    }
    const pipe=getPipeline(s);
    pipe.filter(p=>p.status==='over').forEach(p=>{
      alerts.push({type:'danger',text:`${s.name}`,meta:`${p.label} 超過時限`});
    });
  });
  const al=document.getElementById('alertList');
  if(!alerts.length){
    al.innerHTML='<div class="alert-item" style="background:var(--ue-green-dim);border-left:3px solid var(--ue-green)"><i class="fa fa-circle-check" style="color:var(--ue-green);font-size:16px"></i><span style="font-size:12.5px;font-weight:600;color:#047444">全部店家目前都在時限內 🎉</span></div>';
  } else {
    al.innerHTML=alerts.slice(0,8).map(a=>`
      <div class="alert-item ${a.type}">
        <div class="alert-dot ${a.type}"></div>
        <div class="alert-text">${esc(a.text)}</div>
        <div class="alert-meta">${a.meta}</div>
      </div>`).join('');
  }

  // Mini table
  const inprog=stores.filter(s=>s.contractSent&&!s.liveDate).slice(0,8);
  const tb=document.getElementById('dashboardTableBody');
  if(!inprog.length){
    tb.innerHTML='<tr><td colspan="5" style="text-align:center;padding:24px;color:var(--text-muted)">無進行中店家</td></tr>';
  } else {
    tb.innerHTML=inprog.map(s=>`<tr>
      <td class="store-name">${esc(s.name)}${s.tag?`<br><span class="store-tag${s.tag==='違規'?' red':''}">${esc(s.tag)}</span>`:''}</td>
      <td>${contractBadge(s)}</td>
      <td>${renderPipe(s)}</td>
      <td style="font-size:12px">${esc(s.eta||'—')}</td>
      <td>${timerChip(s)}</td>
    </tr>`).join('');
  }
}

// ─── STORES TABLE ─────────────────────────────────────────────────────────────
function renderStores(){
  const q=(document.getElementById('searchInput').value||'').toLowerCase();
  const fc=document.getElementById('filterContract').value;
  const fa=document.getElementById('filterAML').value;
  const fw=document.getElementById('filterWarn').value;

  const list=stores.map((s,i)=>({s,i})).filter(({s})=>{
    if(q&&!s.name.toLowerCase().includes(q)) return false;
    if(fc&&s.contractStatus!==fc) return false;
    if(fa){
      if(fa==='none'&&s.amlUpload!=='none'&&s.amlUpload) return false;
      if(fa==='uploaded'&&s.amlUpload!=='uploaded') return false;
      if(fa==='abnormal'&&s.amlUpload!=='abnormal') return false;
      if(fa==='manual_approval'&&s.amlStatus!=='manual_approval') return false;
      if(fa==='approved'&&s.amlStatus!=='approved') return false;
    }
    if(fw==='overdue'&&!isOverdue(s)) return false;
    if(fw==='7d'){
      const cs=pd(s.contractSent);
      if(!cs||s.contractStatus==='signed') return false;
      if(calDiff(cs,TODAY)>7) return false;
    }
    return true;
  });

  const tb=document.getElementById('storesTableBody');
  if(!list.length){
    tb.innerHTML='<tr><td colspan="12"><div class="empty-state"><i class="fa fa-store-slash"></i><p>沒有符合的店家</p></div></td></tr>';
    return;
  }
  tb.innerHTML=list.map(({s,i})=>`<tr>
    <td>${s.tag?`<span class="store-tag${s.tag==='違規'?' red':''}">${esc(s.tag)}</span>`:'—'}</td>
    <td>
      <div class="store-name">${esc(s.name)}</div>
      ${s.opp?`<a href="${esc(s.opp)}" target="_blank" class="opp-link"><i class="fa fa-arrow-up-right-from-square"></i> OPP</a>`:''}
    </td>
    <td style="white-space:nowrap">${fmtD(s.contractSent)||'—'}</td>
    <td>${contractBadge(s)}</td>
    <td>${menuBadge(s.menuDone)}</td>
    <td style="font-size:11.5px;max-width:100px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">${esc(s.photoDate||'—')}</td>
    <td>${amlBadge(s)}</td>
    <td style="font-size:11.5px;color:${s.missing?'var(--danger)':'var(--text-muted)'};max-width:90px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">${esc(s.missing||'—')}</td>
    <td><label style="display:flex;align-items:center;gap:4px;cursor:pointer"><input type="checkbox" ${s.cw?'checked':''} onchange="toggleCW(${i},this.checked)" style="accent-color:var(--ue-green)"/><span style="font-size:10px;color:var(--text-muted)">${esc(s.cwNum||'')}</span></label></td>
    <td>${renderPipe(s)}</td>
    <td style="font-size:12px">${esc(s.eta||'—')}</td>
    <td>
      <div class="row-actions">
        <button class="btn btn-sm btn-outline" onclick="openEdit(${i})" title="編輯"><i class="fa fa-pen"></i></button>
        <button class="btn btn-sm btn-danger" onclick="deleteStore(${i})" title="刪除"><i class="fa fa-trash"></i></button>
      </div>
    </td>
  </tr>`).join('');
}

function toggleCW(i,v){ stores[i].cw=v; saveData(); renderAll(); }

// ─── GANTT ────────────────────────────────────────────────────────────────────
function renderGantt(){
  const m=TODAY.getMonth(), yr=TODAY.getFullYear();
  const dim=new Date(yr,m+1,0).getDate();
  const COL=24, SW=150;

  const dayOf=s=>{ const d=pd(s); return d&&d.getMonth()===m&&d.getFullYear()===yr?d.getDate()-1:-1; };

  let html=`<div class="gantt-header" style="min-width:${SW+dim*COL}px">
    <div class="gantt-store-col">店名</div>
    <div class="gantt-days-header">`;
  for(let d=0;d<dim;d++){
    const dd=new Date(yr,m,d+1);
    const isT=dd.getTime()===TODAY.getTime();
    const isW=dd.getDay()===0||dd.getDay()===6;
    html+=`<div class="gantt-day-label${isT?' today-col':''}${isW?' weekend':''}">${d+1}</div>`;
  }
  html+=`</div></div><div style="min-width:${SW+dim*COL}px">`;

  if(!stores.length){ html+='<div class="empty-state"><i class="fa fa-chart-gantt"></i><p>尚無資料</p></div>'; }

  stores.forEach((s,si)=>{
    const cs=dayOf(s.contractSent), csig=dayOf(s.contractSigned);
    const menu=dayOf(s.menuDate), photo=dayOf(s.photoDone);
    const aml=dayOf(s.amlDoneDate), tab=dayOf(s.tabletSent);

    function bar(cls,start,end,label){
      if(start<0&&end<0) return '';
      const s2=Math.max(0,start<0?end:start);
      const e2=Math.min(dim-1,end<0?start:end);
      if(s2>dim-1||e2<0) return '';
      const left=(s2*COL)+'px', width=((e2-s2+1)*COL-2)+'px';
      return `<div class="gantt-bar ${cls}" style="left:${left};width:${width}">${label}</div>`;
    }

    html+=`<div class="gantt-row" style="min-width:${SW+dim*COL}px">
      <div class="gantt-store-name" style="width:${SW}px">${esc(s.name)}</div>
      <div class="gantt-days-row" style="position:relative">`;
    for(let d=0;d<dim;d++){
      const dd=new Date(yr,m,d+1);
      const isT=dd.getTime()===TODAY.getTime();
      const isW=dd.getDay()===0||dd.getDay()===6;
      html+=`<div class="gantt-day-cell${isT?' today-col':''}${isW?' weekend':''}" style="width:${COL}px;flex-shrink:0"></div>`;
    }
    html+=bar('b-contract',cs,csig>=0?csig:cs,'合約');
    html+=bar('b-menu',menu>=0?menu:csig>=0?csig+1:-1,menu,'菜單');
    html+=bar('b-photo',photo>=0?photo:-1,photo,'拍攝');
    html+=bar('b-aml',aml>=0?aml:-1,aml,'AML');
    html+=bar('b-tablet',tab>=0?tab:-1,tab,'平板');
    html+=`</div></div>`;
  });
  html+='</div>';
  document.getElementById('ganttInner').innerHTML=html;
}

// ─── CALENDAR ─────────────────────────────────────────────────────────────────
function renderCalendar(){
  if(calYear===undefined){ calYear=TODAY.getFullYear(); calMonth=TODAY.getMonth(); }
  const m=calMonth, yr=calYear;
  document.getElementById('calTitle').textContent=`${yr} 年 ${m+1} 月`;

  const dim=new Date(yr,m+1,0).getDate();
  const first=new Date(yr,m,1).getDay();
  const prevDim=new Date(yr,m,0).getDate();

  // Build event map
  const evMap={};
  const addEv=(ds,ev)=>{
    const d=pd(ds); if(!d||d.getMonth()!==m||d.getFullYear()!==yr) return;
    const k=d.getDate(); if(!evMap[k]) evMap[k]=[]; evMap[k].push(ev);
  };
  stores.forEach(s=>{
    if(s.photoDate){ const dt=s.photoDate.split(' ')[0]; addEv(dt,{label:'📸 '+s.name,cls:'ev-photo'}); }
    if(s.amlDate) addEv(s.amlDate,{label:'🔐 '+s.name,cls:'ev-aml'});
    if(s.liveDate) addEv(s.liveDate,{label:'🟢 '+s.name,cls:'ev-live'});
    if(s.contractSigned) addEv(s.contractSigned,{label:'✍️ '+s.name,cls:'ev-contract'});
    if(s.tabletSent) addEv(s.tabletSent,{label:'📦 '+s.name,cls:'ev-live'});
  });

  let html='';
  for(let i=0;i<first;i++){
    html+=`<div class="cal-cell other-month"><div class="cal-date">${prevDim-first+i+1}</div></div>`;
  }
  for(let d=1;d<=dim;d++){
    const dd=new Date(yr,m,d);
    const isT=dd.getTime()===TODAY.getTime();
    const isGoal=d<=14;
    const evs=evMap[d]||[];
    html+=`<div class="cal-cell${isT?' today':''}${isGoal?' goal-zone':''}">
      <div class="cal-date${isT?' today-num':''}">${d}</div>
      ${evs.slice(0,3).map(e=>`<div class="cal-event ${e.cls}">${e.label}</div>`).join('')}
      ${evs.length>3?`<div class="cal-more">+${evs.length-3} 筆</div>`:''}
    </div>`;
  }
  const trail=(7-(first+dim)%7)%7;
  for(let i=1;i<=trail;i++) html+=`<div class="cal-cell other-month"><div class="cal-date">${i}</div></div>`;

  document.getElementById('calGrid').innerHTML=html;
}
function calPrev(){ if(calMonth===0){calMonth=11;calYear--;}else calMonth--; renderCalendar(); }
function calNext(){ if(calMonth===11){calMonth=0;calYear++;}else calMonth++; renderCalendar(); }

// ─── MODAL ────────────────────────────────────────────────────────────────────
const FIELDS=['name','tag','opp','web','contractSent','contractStatus','contractSigned','menuDone','menuDate','photoDate','photoDone','amlDate','amlUpload','amlStatus','amlDoneDate','missing','cw','cwNum','tabletSent','tabletReceived','liveDate','eta','notes'];
const FCWCHECK=['cw'];

function openAdd(){
  editIdx=-1;
  document.getElementById('modalTitle').innerHTML='<i class="fa fa-plus"></i> 新增店家';
  document.getElementById('modalPipeline').style.display='none';
  FIELDS.forEach(f=>{
    const el=document.getElementById('f-'+f);
    if(!el) return;
    if(el.type==='checkbox') el.checked=false;
    else el.value='';
  });
  document.getElementById('storeModal').classList.add('show');
}
function openEdit(i){
  editIdx=i;
  const s=stores[i];
  document.getElementById('modalTitle').innerHTML=`<i class="fa fa-pen"></i> ${esc(s.name)}`;
  FIELDS.forEach(f=>{
    const el=document.getElementById('f-'+f);
    if(!el) return;
    if(el.type==='checkbox') el.checked=!!s[f];
    else el.value=s[f]||'';
  });
  document.getElementById('modalPipeline').style.display='block';
  updateModalPipeline();
  document.getElementById('storeModal').classList.add('show');
}
function closeModal(){ document.getElementById('storeModal').classList.remove('show'); }

function updateModalPipeline(){
  const tmp={};
  FIELDS.forEach(f=>{
    const el=document.getElementById('f-'+f);
    if(el) tmp[f]=el.type==='checkbox'?el.checked:el.value;
  });
  const steps=getPipeline(tmp);
  const st2cls={done:'done',active:'active',over:'over',pending:''};
  const st2col={done:'#059a52',active:'#b45309',over:'#dc2626',pending:'var(--text-muted)'};
  document.getElementById('modalPipeSteps').innerHTML=steps.map(st=>`
    <div class="modal-pipe-step ${st2cls[st.status]}">
      <span>${st.icon}</span>
      <span class="step-name" style="color:${st2col[st.status]||'inherit'}">${st.label}</span>
      ${st.doneDate?`<span class="step-date">✓${fmtD(iso(st.doneDate))}</span>`:''}
      ${!st.doneDate&&st.dueDate?`<span class="step-date">期:${fmtD(iso(st.dueDate))}</span>`:''}
    </div>`).join('');
}

function saveStore(){
  const name=(document.getElementById('f-name').value||'').trim();
  if(!name){ alert('請填寫店名！'); return; }
  const obj={};
  FIELDS.forEach(f=>{
    const el=document.getElementById('f-'+f);
    if(el) obj[f]=el.type==='checkbox'?el.checked:el.value;
  });
  if(editIdx>=0) stores[editIdx]=obj;
  else stores.unshift(obj);
  saveData(); renderAll(); closeModal();
  showToast(editIdx>=0?'✅ 已儲存':'✅ 店家已新增！');
}

function deleteStore(i){
  if(!confirm(`確定刪除「${stores[i].name}」？`)) return;
  stores.splice(i,1); saveData(); renderAll();
  showToast('🗑 已刪除');
}

// ─── IMPORT / EXPORT ──────────────────────────────────────────────────────────
function exportJSON(){
  const blob=new Blob([JSON.stringify({ver:4,exported:new Date().toISOString(),stores},null,2)],{type:'application/json'});
  const a=document.createElement('a'); a.href=URL.createObjectURL(blob);
  a.download=`UE_BD_備份_${new Date().toISOString().split('T')[0]}.json`; a.click();
  showToast('📤 已匯出 JSON 備份');
}
function importJSON(e){
  const f=e.target.files[0]; if(!f) return;
  const r=new FileReader();
  r.onload=ev=>{
    try{
      const d=JSON.parse(ev.target.result);
      const arr=Array.isArray(d)?d:d.stores;
      if(!Array.isArray(arr)) throw new Error();
      if(!confirm(`確定匯入 ${arr.length} 筆資料？（將覆蓋現有資料）`)) return;
      stores=arr; saveData(); renderAll();
      showToast(`✅ 匯入 ${arr.length} 筆成功`);
    }catch{ alert('JSON 格式有誤，請確認檔案。'); }
  };
  r.readAsText(f,'UTF-8');
  e.target.value='';
}

// ─── TOAST ────────────────────────────────────────────────────────────────────
let toastTimer;
function showToast(msg){
  let t=document.querySelector('.toast'); if(t) t.remove();
  t=document.createElement('div'); t.className='toast'; t.textContent=msg;
  document.body.appendChild(t);
  clearTimeout(toastTimer); toastTimer=setTimeout(()=>t.remove(),2800);
}

// ─── TABS ─────────────────────────────────────────────────────────────────────
function switchTab(tab){
  ['dashboard','stores','gantt','calendar'].forEach(v=>{
    document.getElementById('view-'+v).classList.toggle('active',v===tab);
    document.getElementById('tab-'+v).classList.toggle('active',v===tab);
  });
  if(tab==='gantt') renderGantt();
  if(tab==='calendar') renderCalendar();
}

// ─── RENDER ALL ───────────────────────────────────────────────────────────────
function renderAll(){
  renderDashboard();
  renderStores();
  if(document.getElementById('view-gantt').classList.contains('active')) renderGantt();
  if(document.getElementById('view-calendar').classList.contains('active')) renderCalendar();
}

function esc(s){ return String(s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;'); }

// ─── SAMPLE DATA ──────────────────────────────────────────────────────────────
function loadSample(){
  stores=[
    {name:"澄味堂豆乳雞",tag:"6月ft",opp:"",web:"",contractSent:"2026-04-21",contractStatus:"signed",contractSigned:"2026-04-28",menuDone:"yes",menuDate:"2026-05-03",photoDate:"缺照片",photoDone:"",amlDate:"2026-05-04",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"",missing:"缺照片",cw:false,cwNum:"eng-SF51905995",tabletSent:"",tabletReceived:"",liveDate:"",eta:"缺照片",notes:""},
    {name:"棠以商行-日式鹽滷豆花",tag:"6月ft",opp:"",web:"",contractSent:"2026-04-18",contractStatus:"signed",contractSigned:"2026-04-25",menuDone:"yes",menuDate:"2026-05-01",photoDate:"2026-05-15",photoDone:"2026-05-15",amlDate:"2026-05-11",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"2026-05-18",missing:"",cw:true,cwNum:"eng-SF51911810",tabletSent:"2026-05-22",tabletReceived:"2026-05-25",liveDate:"2026-05-28",eta:"已開通",notes:""},
    {name:"里好餐桌",tag:"6月ft",opp:"",web:"",contractSent:"2026-05-19",contractStatus:"signed",contractSigned:"2026-05-26",menuDone:"making",menuDate:"",photoDate:"",photoDone:"",amlDate:"2026-05-22",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"",missing:"傳的銀行帳戶名稱",cw:false,cwNum:"",tabletSent:"",tabletReceived:"",liveDate:"",eta:"月中",notes:""},
    {name:"小豆堡",tag:"6月ft",opp:"",web:"",contractSent:"2026-05-17",contractStatus:"signed",contractSigned:"2026-05-24",menuDone:"yes",menuDate:"2026-05-29",photoDate:"",photoDone:"",amlDate:"2026-05-22",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"",missing:"",cw:false,cwNum:"",tabletSent:"",tabletReceived:"",liveDate:"",eta:"隨時",notes:""},
    {name:"咖啡施",tag:"6月ft",opp:"",web:"",contractSent:"2026-05-18",contractStatus:"pending",contractSigned:"",menuDone:"no",menuDate:"",photoDate:"",photoDone:"",amlDate:"",amlUpload:"none",amlStatus:"",amlDoneDate:"",missing:"",cw:false,cwNum:"",tabletSent:"",tabletReceived:"",liveDate:"",eta:"",notes:""},
    {name:"泰泰喜歡",tag:"6月ft",opp:"",web:"",contractSent:"2026-05-19",contractStatus:"signed",contractSigned:"2026-05-26",menuDone:"no",menuDate:"",photoDate:"",photoDone:"",amlDate:"2026-05-22",amlUpload:"uploaded",amlStatus:"identity_not_verified",amlDoneDate:"",missing:"",cw:false,cwNum:"",tabletSent:"",tabletReceived:"",liveDate:"",eta:"",notes:""},
    {name:"忠孝夜市|烤霸傳統生烤玉米",tag:"6月ft",opp:"",web:"",contractSent:"2026-05-26",contractStatus:"signed",contractSigned:"2026-06-02",menuDone:"making",menuDate:"",photoDate:"",photoDone:"",amlDate:"2026-05-26",amlUpload:"uploaded",amlStatus:"identity_not_verified",amlDoneDate:"",missing:"",cw:false,cwNum:"",tabletSent:"",tabletReceived:"",liveDate:"",eta:"6/15",notes:""},
    {name:"九丹煮奶-台南國華店",tag:"6月ft",opp:"",web:"",contractSent:"2026-06-01",contractStatus:"signed",contractSigned:"2026-06-08",menuDone:"making",menuDate:"",photoDate:"",photoDone:"",amlDate:"2026-06-05",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"",missing:"",cw:false,cwNum:"NOVO-UETW201",tabletSent:"",tabletReceived:"",liveDate:"",eta:"",notes:""},
    {name:"鶴立雞群 HO-LI 炸雞",tag:"6月ft",opp:"",web:"",contractSent:"2026-06-03",contractStatus:"signed",contractSigned:"2026-06-10",menuDone:"making",menuDate:"",photoDate:"2026-06-04",photoDone:"2026-06-04",amlDate:"2026-06-04",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"",missing:"",cw:false,cwNum:"NOVO-UETW205",tabletSent:"",tabletReceived:"",liveDate:"",eta:"",notes:""},
    {name:"YaLo鴨嚼",tag:"6月ft",opp:"",web:"",contractSent:"2026-06-04",contractStatus:"signed",contractSigned:"2026-06-11",menuDone:"making",menuDate:"",photoDate:"2026-06-11 15:30",photoDone:"2026-06-11",amlDate:"2026-06-13",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"",missing:"",cw:false,cwNum:"shunfeng-SF519",tabletSent:"",tabletReceived:"",liveDate:"",eta:"",notes:""},
    {name:"洋行炸雞 國華店",tag:"6月ft sunmi",opp:"",web:"",contractSent:"2026-06-12",contractStatus:"pending",contractSigned:"",menuDone:"no",menuDate:"",photoDate:"AI",photoDone:"",amlDate:"2026-06-13",amlUpload:"uploaded",amlStatus:"identity_not_verified",amlDoneDate:"",missing:"",cw:false,cwNum:"SF51957552555",tabletSent:"",tabletReceived:"",liveDate:"",eta:"",notes:""},
    {name:"林香檸手打檸檬茶-一中店",tag:"6月ft",opp:"",web:"",contractSent:"2026-06-01",contractStatus:"signed",contractSigned:"2026-06-08",menuDone:"making",menuDate:"",photoDate:"2026-06-05",photoDone:"2026-06-05",amlDate:"2026-06-04",amlUpload:"uploaded",amlStatus:"manual_approval",amlDoneDate:"",missing:"",cw:false,cwNum:"shunfeng-SF513-",tabletSent:"",tabletReceived:"",liveDate:"",eta:"月中",notes:""},
    {name:"石原省太郎",tag:"違規",opp:"",web:"",contractSent:"2026-06-02",contractStatus:"signed",contractSigned:"2026-06-09",menuDone:"no",menuDate:"",photoDate:"",photoDone:"",amlDate:"",amlUpload:"none",amlStatus:"",amlDoneDate:"",missing:"",cw:false,cwNum:"",tabletSent:"",tabletReceived:"",liveDate:"",eta:"",notes:""},
  ];
  saveData();
}

// ─── INIT ─────────────────────────────────────────────────────────────────────
document.getElementById('headerMonth').textContent=
  TODAY.toLocaleDateString('zh-TW',{year:'numeric',month:'long',day:'numeric',weekday:'short'});
calYear=TODAY.getFullYear(); calMonth=TODAY.getMonth();
loadData();
renderAll();
</script>
</body>
</html>
