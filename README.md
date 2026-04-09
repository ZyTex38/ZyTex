<!DOCTYPE html>
<html lang="az">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no"/>
<title>Block Blast!</title>
<link href="https://fonts.googleapis.com/css2?family=Fredoka+One&family=Nunito:wght@400;700;900&display=swap" rel="stylesheet"/>
<style>
:root{
  --bg:#0d0d1a;--surface:#1a1a2e;--card:#16213e;
  --y:#f7c948;--r:#ff6b6b;--c:#4ecdc4;--p:#a855f7;--g:#22c55e;--b:#3b82f6;--o:#f97316;--pk:#ec4899;
  --text:#fff;--muted:#a0a0c0;
}
*{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
body{background:var(--bg);font-family:'Nunito',sans-serif;color:var(--text);height:100dvh;overflow:hidden;position:relative;}

#screen-menu,#screen-game,#screen-over{
  position:absolute;inset:0;display:flex;flex-direction:column;align-items:center;
  transition:opacity .35s,transform .35s;
}
#screen-menu{z-index:2;overflow-y:auto;padding:20px 16px 40px;}
#screen-game{z-index:1;opacity:0;pointer-events:none;transform:translateX(100%);}
#screen-over{z-index:3;opacity:0;pointer-events:none;justify-content:center;
  background:rgba(10,8,20,.92);backdrop-filter:blur(8px);}
.show{opacity:1!important;pointer-events:all!important;transform:none!important;}
.hide{opacity:0!important;pointer-events:none!important;}

#bg-canvas{position:fixed;inset:0;z-index:0;pointer-events:none;}
.grid-ov{position:fixed;inset:0;z-index:0;pointer-events:none;
  background-image:linear-gradient(rgba(255,255,255,.03)1px,transparent 1px),
  linear-gradient(90deg,rgba(255,255,255,.03)1px,transparent 1px);
  background-size:40px 40px;}
.wrapper{position:relative;z-index:1;width:100%;display:flex;flex-direction:column;align-items:center;}

.logo-wrap{text-align:center;margin-bottom:10px;animation:floatY 3s ease-in-out infinite;}
@keyframes floatY{0%,100%{transform:translateY(0)}50%{transform:translateY(-8px)}}
.logo-blks{display:flex;justify-content:center;gap:5px;margin-bottom:8px;}
.logo-blk{width:30px;height:30px;border-radius:7px;animation:bpop 1s ease-out both;}
.logo-blk:nth-child(1){background:var(--r);animation-delay:.0s;}
.logo-blk:nth-child(2){background:var(--y);animation-delay:.1s;}
.logo-blk:nth-child(3){background:var(--c);animation-delay:.2s;}
.logo-blk:nth-child(4){background:var(--p);animation-delay:.3s;}
.logo-blk:nth-child(5){background:var(--g);animation-delay:.4s;}
@keyframes bpop{0%{transform:scale(0)rotate(-20deg);opacity:0}70%{transform:scale(1.2)rotate(4deg)}100%{transform:scale(1)rotate(0);opacity:1}}
.logo-title{font-family:'Fredoka One',cursive;font-size:clamp(2.4rem,9vw,4.2rem);
  background:linear-gradient(135deg,var(--y),var(--r),var(--p));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
  filter:drop-shadow(0 3px 18px rgba(247,201,72,.35));letter-spacing:2px;}
.logo-sub{font-size:.75rem;color:var(--muted);letter-spacing:4px;text-transform:uppercase;margin-top:2px;}

.score-row{display:flex;gap:10px;margin-bottom:18px;width:100%;max-width:380px;}
.sc-card{flex:1;background:var(--card);border:1px solid rgba(255,255,255,.08);border-radius:14px;
  padding:12px 10px;text-align:center;position:relative;overflow:hidden;animation:slideUp .6s ease-out both;}
.sc-card:nth-child(1){animation-delay:.5s;}.sc-card:nth-child(2){animation-delay:.65s;}
.sc-card::before{content:'';position:absolute;top:0;left:0;right:0;height:3px;}
.sc-card:nth-child(1)::before{background:var(--y);}.sc-card:nth-child(2)::before{background:var(--c);}
.sc-lbl{font-size:.6rem;color:var(--muted);letter-spacing:2px;text-transform:uppercase;margin-bottom:3px;}
.sc-val{font-family:'Fredoka One',cursive;font-size:1.6rem;color:var(--y);}
.sc-card:nth-child(2) .sc-val{color:var(--c);}
@keyframes slideUp{from{transform:translateY(24px);opacity:0}to{transform:none;opacity:1}}

.btn-group{display:flex;flex-direction:column;gap:12px;width:100%;max-width:300px;margin-bottom:18px;}
.btn{position:relative;width:100%;padding:16px 20px;border:none;border-radius:16px;
  font-family:'Fredoka One',cursive;font-size:1.3rem;letter-spacing:1px;cursor:pointer;overflow:hidden;
  transition:transform .15s,filter .15s;animation:slideUp .5s ease-out both;}
.btn:nth-child(1){animation-delay:.7s;}.btn:nth-child(2){animation-delay:.8s;}.btn:nth-child(3){animation-delay:.9s;}
.btn::after{content:'';position:absolute;inset:0;background:rgba(255,255,255,.18);opacity:0;transition:opacity .2s;}
.btn:hover::after{opacity:1;}.btn:active{transform:scale(.96);filter:brightness(.9);}
.btn-play{background:linear-gradient(135deg,#f7c948,#ff9900);color:#1a0a00;
  box-shadow:0 8px 28px rgba(247,201,72,.4),0 2px 0 #b07a00;}
.btn-classic{background:linear-gradient(135deg,#4ecdc4,#0099cc);color:#001a1a;
  box-shadow:0 6px 20px rgba(78,205,196,.3),0 2px 0 #007799;}
.btn-daily{background:linear-gradient(135deg,#a855f7,#7c3aed);color:#fff;
  box-shadow:0 6px 20px rgba(168,85,247,.3),0 2px 0 #5b21b6;}

.sec-row{display:flex;gap:10px;width:100%;max-width:300px;margin-bottom:20px;animation:slideUp .5s ease-out 1s both;}
.btn-sec{flex:1;padding:12px 6px;background:var(--card);border:1px solid rgba(255,255,255,.1);
  border-radius:14px;color:var(--text);font-family:'Fredoka One',cursive;font-size:.85rem;
  cursor:pointer;transition:transform .15s,background .2s;display:flex;flex-direction:column;align-items:center;gap:5px;}
.btn-sec:hover{background:rgba(255,255,255,.07);transform:translateY(-2px);}
.btn-sec:active{transform:scale(.96);}
.btn-sec .icon{font-size:1.4rem;}.btn-sec .lbl{font-size:.65rem;color:var(--muted);}

.streak{width:100%;max-width:300px;background:linear-gradient(135deg,#1f1040,#2d1060);
  border:1px solid rgba(168,85,247,.3);border-radius:16px;padding:14px 16px;
  display:flex;align-items:center;gap:12px;margin-bottom:18px;animation:slideUp .5s ease-out 1.1s both;}
.s-ico{font-size:2rem;}.s-info{flex:1;}
.s-title{font-family:'Fredoka One',cursive;font-size:.95rem;color:var(--p);}
.s-sub{font-size:.7rem;color:var(--muted);}
.s-dots{display:flex;gap:4px;margin-top:5px;}
.s-dot{width:18px;height:18px;border-radius:5px;background:rgba(255,255,255,.12);
  display:flex;align-items:center;justify-content:center;font-size:.55rem;}
.s-dot.done{background:var(--p);}.s-dot.today{background:var(--y);animation:pd 1s ease-in-out infinite;}
@keyframes pd{0%,100%{transform:scale(1)}50%{transform:scale(1.15)}}
.s-claim{background:var(--p);color:#fff;border:none;border-radius:10px;padding:9px 14px;
  font-family:'Fredoka One',cursive;font-size:.85rem;cursor:pointer;transition:filter .15s,transform .15s;}
.s-claim:hover{filter:brightness(1.15);transform:scale(1.05);}

/* ═══ GAME SCREEN ═══ */
#screen-game{background:var(--bg);justify-content:flex-start;padding:0;}
.game-header{width:100%;display:flex;align-items:center;justify-content:space-between;
  padding:10px 14px 8px;background:rgba(13,13,26,.9);
  border-bottom:1px solid rgba(255,255,255,.06);flex-shrink:0;}
.gh-sc{text-align:center;}
.gh-lbl{font-size:.55rem;color:var(--muted);letter-spacing:2px;text-transform:uppercase;}
.gh-val{font-family:'Fredoka One',cursive;font-size:1.5rem;color:var(--y);}
.gh-sc.best .gh-val{color:var(--c);}
.btn-back{background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.12);
  border-radius:10px;padding:7px 12px;color:var(--text);font-family:'Fredoka One',cursive;
  font-size:.85rem;cursor:pointer;transition:background .2s;}
.btn-back:hover{background:rgba(255,255,255,.15);}

.board-wrap{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;
  padding:8px 10px;gap:8px;width:100%;}

#game-board{display:grid;grid-template-columns:repeat(8,1fr);gap:3px;
  width:min(92vw,400px);aspect-ratio:1;background:rgba(255,255,255,.04);
  border-radius:14px;padding:5px;border:1px solid rgba(255,255,255,.08);flex-shrink:0;}
.cell{border-radius:5px;background:rgba(255,255,255,.05);aspect-ratio:1;
  transition:background .1s,transform .1s;position:relative;}
.cell.filled{box-shadow:inset 0 2px 0 rgba(255,255,255,.25),0 2px 5px rgba(0,0,0,.35);}
.cell.hi{background:rgba(255,255,255,.22)!important;transform:scale(1.07);}
.cell.bad{background:rgba(255,80,80,.25)!important;}
.cell.clearing{animation:clrPop .32s ease-out both;}
@keyframes clrPop{0%{transform:scale(1)}40%{transform:scale(1.3)}100%{transform:scale(0);opacity:0}}

.piece-tray{display:flex;justify-content:space-around;align-items:flex-end;
  width:min(92vw,400px);padding:6px 4px;background:rgba(255,255,255,.03);
  border-radius:14px;border:1px solid rgba(255,255,255,.07);
  flex-shrink:0;min-height:88px;}
.piece-slot{width:31%;display:flex;align-items:center;justify-content:center;
  min-height:78px;cursor:grab;border-radius:10px;transition:opacity .2s;user-select:none;}
.piece-slot:active{cursor:grabbing;}
.piece-slot.src{opacity:.3;}
.piece-grid{display:inline-grid;gap:3px;pointer-events:none;}
.piece-cell{width:22px;height:22px;border-radius:5px;
  box-shadow:inset 0 2px 0 rgba(255,255,255,.3),0 2px 4px rgba(0,0,0,.35);}

/* ghost */
#ghost{position:fixed;z-index:80;pointer-events:none;opacity:.82;display:none;}
#ghost .piece-grid{gap:3px;}
#ghost .piece-cell{width:28px;height:28px;border-radius:6px;}

#combo-banner{position:fixed;top:42%;left:50%;transform:translate(-50%,-50%) scale(0);
  font-family:'Fredoka One',cursive;font-size:2.4rem;color:var(--y);
  text-shadow:0 0 28px var(--y),0 0 60px rgba(247,201,72,.5);
  z-index:50;pointer-events:none;opacity:0;transition:transform .22s cubic-bezier(.34,1.56,.64,1),opacity .22s;}
#combo-banner.pop{transform:translate(-50%,-50%) scale(1);opacity:1;}

/* game over */
.over-card{background:var(--surface);border:1px solid rgba(255,255,255,.1);
  border-radius:24px;padding:34px 26px;width:88%;max-width:320px;text-align:center;
  animation:slideUp .4s ease-out;}
.over-title{font-family:'Fredoka One',cursive;font-size:2.1rem;
  background:linear-gradient(135deg,var(--y),var(--r));
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;margin-bottom:6px;}
.over-sub{color:var(--muted);font-size:.82rem;margin-bottom:18px;}
.over-scores{display:flex;gap:10px;margin-bottom:22px;}
.ov-sc{flex:1;background:var(--card);border-radius:12px;padding:12px 8px;text-align:center;}
.ov-sc .lbl{font-size:.58rem;color:var(--muted);letter-spacing:2px;text-transform:uppercase;margin-bottom:3px;}
.ov-sc .val{font-family:'Fredoka One',cursive;font-size:1.4rem;color:var(--y);}
.ov-sc:nth-child(2) .val{color:var(--c);}
.over-btns{display:flex;flex-direction:column;gap:10px;}
.btn-restart{background:linear-gradient(135deg,var(--y),#ff9900);color:#1a0a00;border:none;
  border-radius:14px;padding:15px;font-family:'Fredoka One',cursive;font-size:1.15rem;cursor:pointer;
  transition:filter .15s,transform .15s;}
.btn-restart:hover{filter:brightness(1.1);transform:scale(1.02);}
.btn-menu-back{background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.12);
  border-radius:14px;padding:13px;color:var(--text);font-family:'Fredoka One',cursive;
  font-size:.95rem;cursor:pointer;transition:background .2s;}
.btn-menu-back:hover{background:rgba(255,255,255,.14);}

.toast{position:fixed;bottom:30px;left:50%;
  transform:translateX(-50%) translateY(70px);
  background:#22c55e;color:#fff;font-family:'Fredoka One',cursive;font-size:.9rem;
  padding:11px 22px;border-radius:999px;z-index:200;
  transition:transform .35s cubic-bezier(.34,1.56,.64,1),opacity .35s;
  opacity:0;white-space:nowrap;pointer-events:none;}
.toast.show{transform:translateX(-50%) translateY(0);opacity:1;}

/* ═══ MUSIC MODAL ═══ */
.modal-bd{position:fixed;inset:0;background:rgba(0,0,0,.75);z-index:100;
  display:flex;align-items:center;justify-content:center;opacity:0;pointer-events:none;transition:opacity .25s;}
.modal-bd.open{opacity:1;pointer-events:all;}
.modal{background:var(--surface);border:1px solid rgba(255,255,255,.1);border-radius:22px;
  padding:28px 22px;width:88%;max-width:340px;transform:scale(.85);transition:transform .25s;text-align:center;}
.modal-bd.open .modal{transform:scale(1);}
.modal-title{font-family:'Fredoka One',cursive;font-size:1.6rem;margin-bottom:4px;}
.modal-sub{color:var(--muted);font-size:.82rem;margin-bottom:20px;}

/* music settings */
.music-section{text-align:left;margin-bottom:18px;}
.music-label{font-size:.65rem;color:var(--muted);letter-spacing:2px;text-transform:uppercase;margin-bottom:10px;display:block;}

.track-list{display:flex;flex-direction:column;gap:6px;margin-bottom:18px;}
.track-item{display:flex;align-items:center;gap:10px;padding:10px 12px;
  background:var(--card);border:1px solid rgba(255,255,255,.08);border-radius:12px;
  cursor:pointer;transition:background .2s,border-color .2s;position:relative;overflow:hidden;}
.track-item:hover{background:rgba(255,255,255,.06);}
.track-item.active{border-color:var(--p);background:rgba(168,85,247,.12);}
.track-item.active::before{content:'';position:absolute;left:0;top:0;bottom:0;width:3px;background:var(--p);border-radius:3px 0 0 3px;}
.track-icon{font-size:1.2rem;width:28px;text-align:center;flex-shrink:0;}
.track-info{flex:1;min-width:0;}
.track-name{font-family:'Fredoka One',cursive;font-size:.9rem;color:var(--text);}
.track-genre{font-size:.65rem;color:var(--muted);margin-top:1px;}
.track-badge{font-size:.6rem;background:var(--p);color:#fff;padding:2px 7px;border-radius:999px;font-family:'Fredoka One',cursive;flex-shrink:0;}
.track-play-ico{font-size:.75rem;color:var(--p);flex-shrink:0;opacity:0;transition:opacity .2s;}
.track-item.active .track-play-ico{opacity:1;}

/* equalizer animation */
.eq{display:flex;align-items:flex-end;gap:2px;height:16px;flex-shrink:0;}
.eq-bar{width:3px;background:var(--p);border-radius:2px;animation:none;}
.track-item.active .eq-bar:nth-child(1){animation:eq1 .5s ease-in-out infinite alternate;}
.track-item.active .eq-bar:nth-child(2){animation:eq1 .4s ease-in-out .1s infinite alternate;}
.track-item.active .eq-bar:nth-child(3){animation:eq1 .6s ease-in-out .2s infinite alternate;}
@keyframes eq1{from{height:4px}to{height:14px}}

/* volume slider */
.vol-row{display:flex;align-items:center;gap:10px;margin-bottom:6px;}
.vol-icon{font-size:1.1rem;flex-shrink:0;}
.vol-slider{flex:1;-webkit-appearance:none;appearance:none;height:6px;border-radius:3px;
  background:rgba(255,255,255,.12);outline:none;cursor:pointer;position:relative;}
.vol-slider::-webkit-slider-thumb{-webkit-appearance:none;appearance:none;
  width:20px;height:20px;border-radius:50%;background:var(--p);cursor:pointer;
  box-shadow:0 0 0 3px rgba(168,85,247,.25),0 2px 6px rgba(0,0,0,.4);}
.vol-slider::-moz-range-thumb{width:20px;height:20px;border-radius:50%;background:var(--p);
  border:none;cursor:pointer;box-shadow:0 0 0 3px rgba(168,85,247,.25);}
.vol-pct{font-family:'Fredoka One',cursive;font-size:.85rem;color:var(--p);width:38px;text-align:right;flex-shrink:0;}

/* mute toggle */
.toggle-row{display:flex;align-items:center;justify-content:space-between;
  padding:10px 12px;background:var(--card);border:1px solid rgba(255,255,255,.08);
  border-radius:12px;margin-bottom:18px;}
.toggle-lbl{font-family:'Fredoka One',cursive;font-size:.9rem;}
.toggle-switch{position:relative;width:44px;height:24px;flex-shrink:0;}
.toggle-switch input{opacity:0;width:0;height:0;}
.toggle-track{position:absolute;inset:0;background:rgba(255,255,255,.12);
  border-radius:24px;cursor:pointer;transition:background .25s;}
.toggle-track::before{content:'';position:absolute;width:18px;height:18px;
  left:3px;top:3px;background:#fff;border-radius:50%;transition:transform .25s;
  box-shadow:0 1px 4px rgba(0,0,0,.35);}
input:checked+.toggle-track{background:var(--p);}
input:checked+.toggle-track::before{transform:translateX(20px);}

.modal-close{background:linear-gradient(135deg,var(--p),#7c3aed);color:#fff;border:none;
  border-radius:12px;padding:12px 28px;width:100%;
  font-family:'Fredoka One',cursive;font-size:1rem;cursor:pointer;transition:filter .15s,transform .15s;}
.modal-close:hover{filter:brightness(1.12);transform:scale(1.02);}

/* generic modal for other buttons */
.modal-bd.generic .modal-generic{display:block;}.modal-bd:not(.generic) .modal-generic{display:none;}
.modal-bd.music .modal-music{display:block;}.modal-bd:not(.music) .modal-music{display:none;}

.score-pop{position:fixed;font-family:'Fredoka One',cursive;font-size:1.25rem;color:var(--y);
  pointer-events:none;z-index:60;animation:sfloat 1s ease-out both;}
@keyframes sfloat{0%{opacity:1;transform:translateY(0)scale(1)}100%{opacity:0;transform:translateY(-55px)scale(.75)}}
</style>
</head>
<body>

<canvas id="bg-canvas"></canvas>
<div class="grid-ov"></div>

<!-- ══════ MENU ══════ -->
<div id="screen-menu">
<div class="wrapper">
  <div class="logo-wrap">
    <div class="logo-blks">
      <div class="logo-blk"></div><div class="logo-blk"></div><div class="logo-blk"></div>
      <div class="logo-blk"></div><div class="logo-blk"></div>
    </div>
    <div class="logo-title">Block Blast!</div>
    <div class="logo-sub">HungryStudio</div>
  </div>

  <div class="score-row">
    <div class="sc-card"><div class="sc-lbl">⭐ Skor</div><div class="sc-val" id="menu-score">0</div></div>
    <div class="sc-card"><div class="sc-lbl">🏆 Rekord</div><div class="sc-val" id="menu-best">0</div></div>
  </div>

  <div class="btn-group">
    <button class="btn btn-play" onclick="goGame()">▶ OYNA</button>
    <button class="btn btn-classic" onclick="showModal('classic')">🟦 KLASİK MOD</button>
    <button class="btn btn-daily" onclick="showModal('daily')">📅 GÜNDƏLİK CHALLENGE</button>
  </div>

  <div class="sec-row">
    <button class="btn-sec" onclick="showModal('lead')"><span class="icon">🏅</span><span class="lbl">LİDERLƏR</span></button>
    <button class="btn-sec" onclick="showModal('shop')"><span class="icon">🛒</span><span class="lbl">MAĞAZA</span></button>
    <button class="btn-sec" onclick="showMusicModal()"><span class="icon">⚙️</span><span class="lbl">AYARLAR</span></button>
    <button class="btn-sec" onclick="showModal('stat')"><span class="icon">📊</span><span class="lbl">STATİSTİKA</span></button>
  </div>

  <div class="streak">
    <div class="s-ico">🔥</div>
    <div class="s-info">
      <div class="s-title">5 Günlük Streak!</div>
      <div class="s-sub">Bugünkü mükafatını al</div>
      <div class="s-dots">
        <div class="s-dot done">✓</div><div class="s-dot done">✓</div>
        <div class="s-dot done">✓</div><div class="s-dot done">✓</div>
        <div class="s-dot today">⭐</div><div class="s-dot"></div><div class="s-dot"></div>
      </div>
    </div>
    <button class="s-claim" onclick="claimReward()">AL!</button>
  </div>
</div>
</div>

<!-- ══════ GAME ══════ -->
<div id="screen-game">
  <div class="game-header">
    <button class="btn-back" onclick="goMenu()">← Menu</button>
    <div class="gh-sc">
      <div class="gh-lbl">⭐ SKOR</div>
      <div class="gh-val" id="g-score">0</div>
    </div>
    <div class="gh-sc best">
      <div class="gh-lbl">🏆 REKORD</div>
      <div class="gh-val" id="g-best">0</div>
    </div>
  </div>
  <div class="board-wrap">
    <div id="game-board"></div>
    <div class="piece-tray" id="piece-tray"></div>
  </div>
</div>

<!-- ══════ GAME OVER ══════ -->
<div id="screen-over">
  <div class="over-card">
    <div class="over-title">Oyun Bitdi! 😅</div>
    <div class="over-sub">Daha çox yer yox — amma əla oynadın!</div>
    <div class="over-scores">
      <div class="ov-sc"><div class="lbl">SKOR</div><div class="val" id="ov-score">0</div></div>
      <div class="ov-sc"><div class="lbl">REKORD</div><div class="val" id="ov-best">0</div></div>
    </div>
    <div class="over-btns">
      <button class="btn-restart" onclick="restartGame()">🔄 YENİDƏN OYNA</button>
      <button class="btn-menu-back" onclick="goMenuFromOver()">← Ana Menyu</button>
    </div>
  </div>
</div>

<!-- GHOST -->
<div id="ghost"><div class="piece-grid" id="ghost-grid"></div></div>

<!-- COMBO -->
<div id="combo-banner"></div>
<!-- TOAST -->
<div class="toast" id="toast"></div>

<!-- ══════ GENERIC MODAL ══════ -->
<div class="modal-bd generic" id="modal" onclick="closeMbd(event)">
  <div class="modal">
    <div class="modal-title" id="m-title"></div>
    <div class="modal-sub" id="m-sub"></div>
    <button class="modal-close" onclick="closeMdirect()">BAĞLA</button>
  </div>
</div>

<!-- ══════ MUSIC MODAL ══════ -->
<div class="modal-bd music" id="music-modal" onclick="closeMusicMbd(event)">
  <div class="modal">
    <div class="modal-title">🎵 Musiqi Ayarları</div>
    <div class="modal-sub">Oyun musiqisini və səs səviyyəsini tənzimlə</div>

    <!-- Track list -->
    <div class="music-section">
      <span class="music-label">🎼 Mahnı seç</span>
      <div class="track-list" id="track-list">

        <div class="track-item active" data-track="0" onclick="selectTrack(0)">
          <div class="track-icon">🌌</div>
          <div class="track-info">
            <div class="track-name">Cosmic Groove</div>
            <div class="track-genre">Electronic · Chill</div>
          </div>
          <div class="eq">
            <div class="eq-bar" style="height:8px"></div>
            <div class="eq-bar" style="height:14px"></div>
            <div class="eq-bar" style="height:6px"></div>
          </div>
        </div>

        <div class="track-item" data-track="1" onclick="selectTrack(1)">
          <div class="track-icon">🔥</div>
          <div class="track-info">
            <div class="track-name">Blaze Beats</div>
            <div class="track-genre">Hip-Hop · Energik</div>
          </div>
          <div class="track-play-ico">▶</div>
        </div>

        <div class="track-item" data-track="2" onclick="selectTrack(2)">
          <div class="track-icon">🌊</div>
          <div class="track-info">
            <div class="track-name">Ocean Drift</div>
            <div class="track-genre">Lofi · Sakit</div>
          </div>
          <div class="track-play-ico">▶</div>
        </div>

        <div class="track-item" data-track="3" onclick="selectTrack(3)">
          <div class="track-icon">⚡</div>
          <div class="track-info">
            <div class="track-name">Thunder Rush</div>
            <div class="track-genre">Drum & Bass · Sürətli</div>
          </div>
          <div class="track-play-ico">▶</div>
        </div>

        <div class="track-item" data-track="4" onclick="selectTrack(4)">
          <div class="track-icon">🌸</div>
          <div class="track-info">
            <div class="track-name">Sakura Dreams</div>
            <div class="track-genre">Ambient · Sakitləşdirici</div>
          </div>
          <div class="track-play-ico">▶</div>
        </div>

      </div>
    </div>

    <!-- Volume -->
    <div class="music-section">
      <span class="music-label">🔊 Səs səviyyəsi</span>
      <div class="vol-row">
        <span class="vol-icon" id="vol-icon">🔊</span>
        <input type="range" class="vol-slider" id="vol-slider" min="0" max="100" value="70" oninput="updateVolume(this.value)">
        <span class="vol-pct" id="vol-pct">70%</span>
      </div>
    </div>

    <!-- Mute toggle -->
    <div class="toggle-row">
      <span class="toggle-lbl">🔕 Səsi kəs</span>
      <label class="toggle-switch">
        <input type="checkbox" id="mute-toggle" onchange="toggleMute(this.checked)">
        <span class="toggle-track"></span>
      </label>
    </div>

    <button class="modal-close" onclick="closeMusicModal()">✓ SAXLA</button>
  </div>
</div>

<script>
/* ── BG CANVAS ── */
const cnv=document.getElementById('bg-canvas'),CX=cnv.getContext('2d');
const BCOLORS=['#f7c948','#ff6b6b','#4ecdc4','#a855f7','#22c55e','#3b82f6','#f97316'];
let bgs=[];
function rsz(){cnv.width=innerWidth;cnv.height=innerHeight;}rsz();
window.addEventListener('resize',rsz);
function mkBg(){return{x:Math.random()*cnv.width,y:-40,sz:16+Math.random()*24,
  sp:.3+Math.random()*.6,col:BCOLORS[Math.random()*BCOLORS.length|0],
  rot:0,rs:(Math.random()-.5)*.012,al:.07+Math.random()*.08,rx:4+Math.random()*5|0};}
for(let i=0;i<14;i++){const b=mkBg();b.y=Math.random()*innerHeight;bgs.push(b);}
(function loop(){
  CX.clearRect(0,0,cnv.width,cnv.height);
  bgs.forEach((b,i)=>{
    b.y+=b.sp;b.rot+=b.rs;
    CX.save();CX.translate(b.x+b.sz/2,b.y+b.sz/2);CX.rotate(b.rot);
    CX.globalAlpha=b.al;CX.fillStyle=b.col;
    CX.beginPath();CX.roundRect(-b.sz/2,-b.sz/2,b.sz,b.sz,b.rx);CX.fill();
    CX.restore();
    if(b.y>cnv.height+50)bgs[i]=mkBg();
  });
  requestAnimationFrame(loop);
})();

/* ── SHAPES ── */
const SHAPES=[
  [[1]],
  [[1,1]],[[1],[1]],
  [[1,1,1]],[[1],[1],[1]],
  [[1,1],[1,1]],
  [[1,0],[1,0],[1,1]],[[0,1],[0,1],[1,1]],
  [[1,1],[1,0],[1,0]],[[1,1],[0,1],[0,1]],
  [[1,1,1],[0,1,0]],[[0,1,0],[1,1,1]],
  [[1,0],[1,1],[0,1]],[[0,1],[1,1],[1,0]],
  [[0,1,1],[1,1,0]],[[1,1,0],[0,1,1]],
  [[1,1,1],[1,1,1]],[[1,1],[1,1],[1,1]],
  [[1,1,1,1]],[[1],[1],[1],[1]],
  [[1,1,1],[1,1,1],[1,1,1]],
];
const PCOLORS=['#f7c948','#ff6b6b','#4ecdc4','#a855f7','#22c55e','#3b82f6','#f97316','#ec4899'];

/* ── MUSIC STATE ── */
let currentTrack=0;
let musicVolume=70;
let isMuted=false;

const TRACKS=[
  {name:'Cosmic Groove',genre:'Electronic · Chill',icon:'🌌'},
  {name:'Blaze Beats',genre:'Hip-Hop · Energik',icon:'🔥'},
  {name:'Ocean Drift',genre:'Lofi · Sakit',icon:'🌊'},
  {name:'Thunder Rush',genre:'Drum & Bass · Sürətli',icon:'⚡'},
  {name:'Sakura Dreams',genre:'Ambient · Sakitləşdirici',icon:'🌸'},
];

function selectTrack(idx){
  currentTrack=idx;
  document.querySelectorAll('.track-item').forEach((el,i)=>{
    el.classList.remove('active');
    // reset eq / play icon
    const eq=el.querySelector('.eq');
    const pi=el.querySelector('.track-play-ico');
    if(eq){}  // CSS handles animation via .active class
    if(i===idx){
      el.classList.add('active');
    }
  });
  try{localStorage.setItem('bb-track',idx);}catch(e){}
  showToast('🎵 '+TRACKS[idx].name+' seçildi','#a855f7');
}

function updateVolume(val){
  musicVolume=parseInt(val);
  document.getElementById('vol-pct').textContent=val+'%';
  // update slider gradient fill
  const slider=document.getElementById('vol-slider');
  slider.style.background=`linear-gradient(to right,var(--p) ${val}%,rgba(255,255,255,.12) ${val}%)`;
  // update icon
  const icon=document.getElementById('vol-icon');
  if(val==0)icon.textContent='🔇';
  else if(val<40)icon.textContent='🔈';
  else if(val<70)icon.textContent='🔉';
  else icon.textContent='🔊';
  try{localStorage.setItem('bb-vol',val);}catch(e){}
}

function toggleMute(checked){
  isMuted=checked;
  const volSlider=document.getElementById('vol-slider');
  volSlider.disabled=checked;
  volSlider.style.opacity=checked?'.4':'1';
  try{localStorage.setItem('bb-mute',checked?'1':'0');}catch(e){}
}

function showMusicModal(){
  // load saved settings
  try{
    const sv=localStorage.getItem('bb-vol');
    if(sv!==null){
      musicVolume=parseInt(sv);
      const s=document.getElementById('vol-slider');
      s.value=musicVolume;
      updateVolume(musicVolume);
    }else{updateVolume(musicVolume);}
    const sm=localStorage.getItem('bb-mute');
    if(sm!==null){
      isMuted=sm==='1';
      document.getElementById('mute-toggle').checked=isMuted;
      toggleMute(isMuted);
    }
    const st=localStorage.getItem('bb-track');
    if(st!==null){selectTrack(parseInt(st));}
  }catch(e){updateVolume(musicVolume);}
  document.getElementById('music-modal').classList.add('open');
}

function closeMusicModal(){
  document.getElementById('music-modal').classList.remove('open');
  showToast('✓ Ayarlar saxlanıldı','#22c55e');
}

function closeMusicMbd(e){
  if(e.target===document.getElementById('music-modal'))closeMusicModal();
}

/* initialize slider fill on load */
window.addEventListener('DOMContentLoaded',()=>{
  updateVolume(musicVolume);
});

/* ── STATE ── */
const R=8,C=8;
let board,score,best,pieces,dragSlot,dragPiece,comboCount,comboTimer,gameOver;

function initBoard(){board=Array.from({length:R},()=>Array(C).fill(null));}
function randPiece(){return{shape:SHAPES[Math.random()*SHAPES.length|0],color:PCOLORS[Math.random()*PCOLORS.length|0]};}
function newPieces(){pieces=[randPiece(),randPiece(),randPiece()];}

/* ── RENDER BOARD ── */
function renderBoard(){
  const bd=document.getElementById('game-board');
  bd.innerHTML='';
  for(let r=0;r<R;r++)for(let c=0;c<C;c++){
    const el=document.createElement('div');
    el.className='cell';el.dataset.r=r;el.dataset.c=c;
    const v=board[r][c];
    if(v){el.classList.add('filled');el.style.background=v;
      el.style.boxShadow=`inset 0 2px 0 rgba(255,255,255,.25),0 2px 5px rgba(0,0,0,.35)`;}
    bd.appendChild(el);
  }
}

/* ── RENDER TRAY ── */
function renderTray(){
  const tray=document.getElementById('piece-tray');tray.innerHTML='';
  pieces.forEach((p,idx)=>{
    const slot=document.createElement('div');slot.className='piece-slot';slot.dataset.idx=idx;
    if(!p){slot.style.opacity='.15';tray.appendChild(slot);return;}
    const pg=makePieceGrid(p,22);slot.appendChild(pg);
    slot.addEventListener('mousedown',e=>{e.preventDefault();startDrag(idx,e.clientX,e.clientY);});
    slot.addEventListener('touchstart',e=>{const t=e.touches[0];startDrag(idx,t.clientX,t.clientY);},{passive:true});
    tray.appendChild(slot);
  });
}

function makePieceGrid(p,cellSize){
  const pg=document.createElement('div');pg.className='piece-grid';
  pg.style.gridTemplateColumns=`repeat(${p.shape[0].length},${cellSize}px)`;
  p.shape.forEach(row=>row.forEach(v=>{
    const pc=document.createElement('div');pc.className='piece-cell';
    pc.style.width=cellSize+'px';pc.style.height=cellSize+'px';
    pc.style.background=v?p.color:'transparent';
    if(!v)pc.style.boxShadow='none';
    pg.appendChild(pc);
  }));
  return pg;
}

/* ── DRAG ── */
function startDrag(idx,mx,my){
  if(!pieces[idx])return;
  dragSlot=idx;dragPiece=pieces[idx];
  document.querySelectorAll('.piece-slot').forEach((s,i)=>{if(i===idx)s.classList.add('src');});
  showGhost(mx,my);
}

function showGhost(x,y){
  const gh=document.getElementById('ghost');
  const gg=document.getElementById('ghost-grid');
  gg.innerHTML='';
  const pg=makePieceGrid(dragPiece,28);
  pg.querySelectorAll('.piece-cell').forEach(c=>{c.style.borderRadius='6px';});
  gg.appendChild(pg);
  gh.style.display='block';
  posGhost(x,y);
}

function posGhost(x,y){
  const gh=document.getElementById('ghost');
  if(!dragPiece){gh.style.display='none';return;}
  const cols=dragPiece.shape[0].length,rows=dragPiece.shape.length;
  gh.style.left=(x-cols*15.5)+'px';
  gh.style.top=(y-rows*31-10)+'px';
}

function hideGhost(){document.getElementById('ghost').style.display='none';}

function getCell(x,y){
  const el=document.elementFromPoint(x,y);if(!el)return null;
  const c=el.closest('.cell');if(!c)return null;
  return{r:+c.dataset.r,c:+c.dataset.c};
}

function doHighlight(r,c){
  clearHi();
  if(!dragPiece)return;
  const ok=canPlace(dragPiece.shape,r,c);
  dragPiece.shape.forEach((row,dr)=>row.forEach((v,dc)=>{
    if(!v)return;
    const el=document.querySelector(`.cell[data-r="${r+dr}"][data-c="${c+dc}"]`);
    if(el)el.classList.add(ok?'hi':'bad');
  }));
}
function clearHi(){document.querySelectorAll('.cell.hi,.cell.bad').forEach(c=>{c.classList.remove('hi','bad');});}

document.addEventListener('mousemove',e=>{
  if(!dragPiece)return;
  posGhost(e.clientX,e.clientY);
  const cell=getCell(e.clientX,e.clientY);
  if(cell)doHighlight(cell.r,cell.c);else clearHi();
});
document.addEventListener('mouseup',e=>{
  if(!dragPiece)return;
  const cell=getCell(e.clientX,e.clientY);
  if(cell)dropPiece(cell.r,cell.c);
  endDrag();
});
document.addEventListener('touchmove',e=>{
  if(!dragPiece)return;
  e.preventDefault();
  const t=e.touches[0];
  posGhost(t.clientX,t.clientY);
  const cell=getCell(t.clientX,t.clientY-35);
  if(cell)doHighlight(cell.r,cell.c);else clearHi();
},{passive:false});
document.addEventListener('touchend',e=>{
  if(!dragPiece)return;
  const t=e.changedTouches[0];
  const cell=getCell(t.clientX,t.clientY-35);
  if(cell)dropPiece(cell.r,cell.c);
  endDrag();
});

function endDrag(){
  clearHi();hideGhost();
  document.querySelectorAll('.piece-slot.src').forEach(s=>s.classList.remove('src'));
  dragPiece=null;dragSlot=null;
}

/* ── LOGIC ── */
function canPlace(shape,r,c){
  for(let dr=0;dr<shape.length;dr++)for(let dc=0;dc<shape[0].length;dc++){
    if(!shape[dr][dc])continue;
    const nr=r+dr,nc=c+dc;
    if(nr<0||nr>=R||nc<0||nc>=C||board[nr][nc])return false;
  }
  return true;
}

function dropPiece(r,c){
  if(!dragPiece||!canPlace(dragPiece.shape,r,c))return;
  dragPiece.shape.forEach((row,dr)=>row.forEach((v,dc)=>{
    if(v)board[r+dr][c+dc]=dragPiece.color;
  }));
  pieces[dragSlot]=null;
  checkLines(r,c,dragPiece.shape);
  if(pieces.every(p=>!p))newPieces();
  renderBoard();renderTray();updateHUD();
  if(!gameOver&&!hasAnyMove()){
    setTimeout(()=>showGameOver(),400);
  }
}

function checkLines(sr,sc,shape){
  const rows=new Set(),cols=new Set();
  shape.forEach((row,dr)=>row.forEach((v,dc)=>{if(v){rows.add(sr+dr);cols.add(sc+dc);}}));
  const cRows=[],cCols=[];
  rows.forEach(r=>{if(board[r].every(v=>v))cRows.push(r);});
  cols.forEach(c=>{if(board.every(row=>row[c]))cCols.push(c);});
  const lines=cRows.length+cCols.length;
  if(!lines){comboCount=0;return;}
  comboCount++;
  const cells=new Set();
  cRows.forEach(r=>{for(let c=0;c<C;c++)cells.add(r+','+c);});
  cCols.forEach(c=>{for(let r=0;r<R;r++)cells.add(r+','+c);});
  cells.forEach(key=>{
    const[r,c]=key.split(',').map(Number);
    const el=document.querySelector(`.cell[data-r="${r}"][data-c="${c}"]`);
    if(el)el.classList.add('clearing');
    board[r][c]=null;
  });
  const pts=lines*100*Math.max(1,comboCount)*cells.size;
  score+=pts;if(score>best){best=score;try{localStorage.setItem('bb-best',best);}catch(e){}}
  spawnPop(pts);
  if(comboCount>1)showCombo(comboCount);
  updateHUD();
}

function hasAnyMove(){
  return pieces.some(p=>{
    if(!p)return true;
    for(let r=0;r<=R-p.shape.length;r++)
      for(let c=0;c<=C-p.shape[0].length;c++)
        if(canPlace(p.shape,r,c))return true;
    return false;
  });
}

function spawnPop(pts){
  const el=document.createElement('div');el.className='score-pop';
  el.textContent='+'+pts.toLocaleString();
  el.style.left=(25+Math.random()*50)+'%';el.style.top=(25+Math.random()*35)+'%';
  document.body.appendChild(el);setTimeout(()=>el.remove(),1000);
}

function showCombo(n){
  const cb=document.getElementById('combo-banner');
  cb.textContent=n+'x COMBO! 🔥';cb.classList.add('pop');
  clearTimeout(comboTimer);comboTimer=setTimeout(()=>cb.classList.remove('pop'),1200);
}

function updateHUD(){
  document.getElementById('g-score').textContent=score.toLocaleString();
  document.getElementById('g-best').textContent=best.toLocaleString();
}

/* ── SCREENS ── */
function goGame(){
  initBoard();score=0;comboCount=0;gameOver=false;
  try{best=parseInt(localStorage.getItem('bb-best'))||0;}catch(e){best=0;}
  newPieces();renderBoard();renderTray();updateHUD();
  document.getElementById('screen-menu').classList.add('hide');
  document.getElementById('screen-game').classList.add('show');
}

function goMenu(){
  document.getElementById('screen-menu').classList.remove('hide');
  document.getElementById('screen-game').classList.remove('show');
  syncMenuScores();
}

function showGameOver(){
  gameOver=true;
  document.getElementById('ov-score').textContent=score.toLocaleString();
  document.getElementById('ov-best').textContent=best.toLocaleString();
  document.getElementById('screen-over').classList.add('show');
}

function restartGame(){
  document.getElementById('screen-over').classList.remove('show');
  initBoard();score=0;comboCount=0;gameOver=false;
  newPieces();renderBoard();renderTray();updateHUD();
}

function goMenuFromOver(){
  document.getElementById('screen-over').classList.remove('show');
  document.getElementById('screen-game').classList.remove('show');
  document.getElementById('screen-menu').classList.remove('hide');
  syncMenuScores();
}

function syncMenuScores(){
  try{best=parseInt(localStorage.getItem('bb-best'))||0;}catch(e){}
  document.getElementById('menu-score').textContent=(score||0).toLocaleString();
  document.getElementById('menu-best').textContent=best.toLocaleString();
}

/* ── GENERIC MODAL / TOAST ── */
const MC={
  classic:{title:'🟦 Klassik Mod',sub:'Sonsuz blok puzzle! Nə qədər uzun oynaya bilərsən?'},
  daily:{title:'📅 Günlük Challenge',sub:'Hər gün yeni tapşırıq. Bugünkü rekoru qır!'},
  lead:{title:'🏅 Liderlik Cədvəli',sub:'Ən yaxşı oyunçularla rəqabət et!'},
  shop:{title:'🛒 Mağaza',sub:'Yeni rənglər, temalar və gücləndiricilər!'},
  stat:{title:'📊 Statistika',sub:'Oyun statistikalarını izlə və inkişaf et!'},
};
function showModal(k){
  const c=MC[k]||{title:'🎮 Tezliklə',sub:'Bu bölüm hazırlanır...'};
  document.getElementById('m-title').textContent=c.title;
  document.getElementById('m-sub').textContent=c.sub;
  document.getElementById('modal').classList.add('open');
}
function closeMbd(e){if(e.target===document.getElementById('modal'))closeMdirect();}
function closeMdirect(){document.getElementById('modal').classList.remove('open');}
function showToast(msg,col='#22c55e'){
  const t=document.getElementById('toast');t.textContent=msg;t.style.background=col;
  t.style.color=(col==='#f7c948')?'#1a0a00':'#fff';
  t.classList.add('show');setTimeout(()=>t.classList.remove('show'),2200);
}
function claimReward(){showToast('🎁 +500 Xal qazandın!','#a855f7');}

syncMenuScores();
</script>
</body>
</html>
