<!doctype html>
<html lang="pl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Serce Gotowe — Księga (lista 2 w rzędzie)</title>

<link href="https://fonts.googleapis.com/css2?family=Pacifico&family=Roboto:wght@400;700&display=swap" rel="stylesheet">

<style>
  :root{
    --bg: #9fc6e2;
    --bg2: #cfe8f8;
    --navy: #1e3a8a;
    --quote-grad-start: #3b5998;
    --quote-grad-end: #5a8dee;
    --point-blue: #2b7bd6;
  }
  html,body{height:100%;margin:0;font-family:Roboto,system-ui,-apple-system,"Segoe UI",Arial;background:linear-gradient(180deg,var(--bg) 0%,var(--bg2) 100%);color:#05203f;-webkit-font-smoothing:antialiased;}
  .container{max-width:520px;margin:18px auto;padding:18px;}
  header{text-align:center;margin-bottom:32px;} 
  .title{font-family:Pacifico,cursive;font-size:36px;color:var(--navy);margin:0;line-height:1}
  .subtitle{color:rgba(30,58,138,0.85);font-weight:700;font-size:13px;margin-top:6px}

  .card{background:rgba(255,255,255,0.95);border-radius:16px;padding:16px;box-shadow:0 8px 20px rgba(6,21,52,0.12);border:1px solid rgba(30,58,138,0.06)}
  .draw-area{display:flex;flex-direction:column;gap:12px;align-items:center}

  .preview{width:100%;height:200px;border-radius:12px;display:flex;align-items:center;justify-content:center;background:linear-gradient(180deg,rgba(255,255,255,0.9),rgba(255,255,255,0.88));border:1px solid rgba(30,58,138,0.04);overflow:hidden;position:relative}
  .preview img{max-width:60%;max-height:80%;object-fit:contain;border-radius:8px;transition:transform .3s ease,filter .45s ease}
  .preview img.dimmed{filter:brightness(0.35) grayscale(0.15)}
  .preview .placeholder{font-weight:700;color:var(--navy);text-align:center;padding:12px}
  .preview .preview-overlay{position:absolute;opacity:0;display:flex;align-items:center;justify-content:center;width:88px;height:88px;border-radius:50%;background:rgba(0,0,0,0.45);color:white;font-weight:900;font-size:36px;text-shadow:0 6px 10px rgba(0,0,0,0.45);pointer-events:none;transition:opacity .45s ease}
  .preview.show-overlay .preview-overlay{display:flex;opacity:1}

  .controls{display:flex;width:100%;gap:10px;align-items:center}
  .btn{flex:1;border:none;padding:12px 14px;border-radius:12px;font-weight:700;cursor:pointer;background:linear-gradient(90deg,var(--quote-grad-start),var(--quote-grad-end));color:#fff}
  .btn.ghost{background:#fff;border:2px solid rgba(30,58,138,0.12);color:var(--navy);box-shadow:none}
  .btn.inactive{background: #cbd5e1; color: rgba(5,32,63,0.7); cursor:default}

  /* nice book button */
  .btn.book{background:linear-gradient(90deg,var(--quote-grad-start),var(--quote-grad-end));border-radius:14px;padding:12px 16px;border:1px solid rgba(0,0,0,0.06);display:inline-flex;align-items:center;gap:10px;justify-content:center;box-shadow:0 10px 24px rgba(30,58,138,0.08);color:#fff;font-weight:900;transition:transform .16s ease,box-shadow .16s ease}
  .btn.book .icon{font-size:18px;color:#fff}
  .btn.book:hover{transform:translateY(-3px);box-shadow:0 18px 40px rgba(30,58,138,0.16)}
  @media (max-width:420px){ .btn.book{width:100%} }

  /* countdown cards */
  .countdowns-grid{display:flex;gap:10px}
  .countdown-card{flex:1;background:linear-gradient(180deg,#ffffff,#f7fbff);border-radius:12px;padding:10px;border:1px solid rgba(30,58,138,0.06);box-shadow:0 6px 14px rgba(6,21,52,0.06);text-align:center}
  .countdown-label{font-size:12px;font-weight:700;color:rgba(5,32,63,0.7)}
  .countdown-value{font-variant-numeric:tabular-nums;font-weight:900;color:var(--navy);font-size:18px;margin-top:6px}
  .countdown-sub{font-size:12px;color:rgba(5,32,63,0.6);margin-top:6px}
  @media (max-width:420px){ .countdowns-grid{flex-direction:column} }

  .caption{font-weight:700;color:var(--navy);text-align:center;min-height:48px;display:flex;align-items:center;justify-content:center;padding:8px 6px}
  .cooldowns{display:flex;flex-direction:column;gap:8px;margin-top:8px}
  .tiny{font-size:12px;font-weight:700;color:rgba(5,32,63,0.7)}
  .book-area{margin-top:12px;display:flex;gap:8px;align-items:center;justify-content:center}

  /* Modal */
  .modal-backdrop{position:fixed;inset:0;display:none;align-items:center;justify-content:center;z-index:2000;padding:14px;background:rgba(0,0,0,0.45)}
  .modal-backdrop.show{display:flex}
  .modal{
    width:100%;
    max-width:560px;
    background:#fff;
    border-radius:14px;
    padding:0;
    box-shadow:0 20px 40px rgba(6,21,52,0.16);
    border:1px solid rgba(30,58,138,0.06);
    overflow:visible;
    position:relative;
    /* make room for external custom scrollbar so it doesn't overlap content */
    padding-right:72px; /* reserve space on the right for the scrollbar */
  }
  .modal-inner{padding:14px}
  .modal-header{display:flex;justify-content:space-between;align-items:center;gap:12px}
  .modal-title{font-family:Pacifico;color:var(--navy);font-size:22px;margin:0}
  .modal-hint{font-size:13px;color:rgba(5,32,63,0.8);margin-top:6px;font-weight:700;text-align:center}
  /* mission mini card in header */
  .mission{display:flex;flex-direction:column;align-items:flex-end;gap:6px}
  .mission-title{font-weight:700;font-size:13px;color:var(--navy)}
  .mission-row{display:flex;align-items:center;gap:8px}
  .mission-count{font-weight:900;color:var(--navy);background:linear-gradient(90deg,var(--quote-grad-start),var(--quote-grad-end));color:#fff;padding:6px 10px;border-radius:12px;font-size:13px;box-shadow:0 8px 18px rgba(30,58,138,0.12)}
  .mission-badge{font-size:18px;margin-left:4px}
  .mission-small{font-size:12px;color:rgba(5,32,63,0.65);margin-top:2px}
  
  .close{border:none;background:transparent;font-size:20px;cursor:pointer;color:var(--navy)}

  /* list (NO pages) */
  .modal-body{
    width:100%;
    height:64vh;
    overflow-y:auto;
    overflow-x:hidden;
    padding:12px 18px 18px 18px;
    background:linear-gradient(180deg,rgba(255,255,255,0.98),rgba(247,251,255,0.98));
    box-sizing:border-box;
    scroll-behavior:smooth;
    /* hide native scrollbar visuals but keep ability to scroll */
    scrollbar-width: none;
    -ms-overflow-style: none;
  }
  .modal-body::-webkit-scrollbar{display:none}

  .grid{display:flex;flex-wrap:wrap;gap:12px;justify-content:space-between;align-content:flex-start;max-width:520px;margin:0 auto}
  .cardItem{flex:0 0 calc(50% - 6px);box-sizing:border-box;background:#fff;border-radius:10px;border:1px solid rgba(30,58,138,0.04);display:flex;flex-direction:column;align-items:center;justify-content:flex-start;gap:8px;padding:10px;overflow:visible}
  .imgWrap{width:100%;aspect-ratio:1/1;position:relative;overflow:hidden;border-radius:8px;background:linear-gradient(90deg,var(--quote-grad-start),var(--quote-grad-end));display:flex;align-items:center;justify-content:center;padding:6px;box-sizing:border-box}
  .cardItem img{width:100%;height:100%;object-fit:contain;border-radius:6px;transition:filter .18s ease,transform .18s ease;background:transparent}
  .cardItem.dimmed img{filter:brightness(0.35) grayscale(0.15)}
  .imgWrap .overlay{position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);font-weight:900;font-size:48px;color:rgba(255,255,255,0.95);pointer-events:none;display:none;align-items:center;justify-content:center;text-shadow:0 6px 10px rgba(0,0,0,0.45)}
  .cardItem.dimmed .imgWrap .overlay{display:flex;background:rgba(0,0,0,0.45);width:72px;height:72px;border-radius:50%;font-size:36px}
  .cardItem .name{font-weight:900;color:var(--navy);text-align:center;font-size:14px;margin-top:6px}
  .cardItem .desc{font-size:12px;color:rgba(5,32,63,0.8);text-align:center;padding:6px 4px}

  /* custom vertical scrollbar positioned outside main content area (in reserved modal padding) */
  .side-scrollbar{
    position:absolute;
    right:16px; /* sits in the reserved padding area */
    z-index:2200;
    pointer-events:auto;
    display:flex;
    align-items:flex-start;
    justify-content:center;
  }
  .side-scrollbar .track{
    width:8px;
    border-radius:6px;
    background:rgba(5,32,63,0.06);
    position:relative;
    height:100%;
    box-sizing:border-box;
    padding:4px 0;
  }
  .side-scrollbar .thumb{
    position:absolute;
    left:50%;
    transform:translateX(-50%);
    width:28px;
    border-radius:20px;
    height:48px;
    background:#fff;
    border:3px solid var(--quote-grad-start);
    box-shadow:0 4px 10px rgba(6,21,52,0.12);
    cursor:pointer;
    touch-action:none;
    box-sizing:border-box;
  }
  .side-scrollbar[aria-hidden="true"]{display:none}

  @media (max-width:420px){
    .modal{max-width:100%}
    .side-scrollbar{display:none} /* hide custom scrollbar on very small screens */
  }
</style>
</head>
<body>
  <div class="container">
    <header>
      <h1 class="title">Serce Gotowe</h1>
      
    </header>

    <section class="card draw-area" aria-labelledby="drawLabel">
      <div class="preview" id="preview">
        <img id="centralImg" src="" alt="Czekoladka" draggable="false" style="display:none">
        <div class="placeholder" id="placeholderText">Wylosuj Czekoladkę na dziś</div>
        <div class="preview-overlay" id="previewOverlay">?</div>
      </div>

      <div class="controls" style="width:100%;">
        <button id="drawBtn" class="btn">Wylosuj</button>
      </div>

      <div class="caption" id="caption">Kliknij „Wylosuj”, aby otrzymać dzisiejszą misję.</div>

      <div class="cooldowns" style="width:100%;">
        <div class="countdowns-grid">
          <div class="countdown-card" id="drawCooldownCard" aria-live="polite">
            <div class="countdown-label">Następne losowanie</div>
            <div class="countdown-value" id="drawCountdown">—</div>
            <div class="countdown-sub tiny">możesz losować co 24h</div>
          </div>

          <div class="countdown-card" id="dec24Card" aria-live="polite">
            <div class="countdown-label">Do 24 grudnia</div>
            <div class="countdown-value" id="dec24">—</div>
            <div class="countdown-sub tiny">świętujemy 🎄</div>
          </div>
        </div>
      </div>
<div class="book-area">
        <button id="openBook" class="btn book" style="width:100%;"><span class="icon">📖</span>Księga Czekoladek</button>
      </div>
    </section>
  </div>

  <!-- Modal -->
  <div class="modal-backdrop" id="modalBackdrop" role="dialog" aria-modal="true" aria-hidden="true">
    <div class="modal" role="document">
      <div class="modal-inner">
        <div class="modal-header">
          <div style="flex:1;">
            <h3 class="modal-title">Księga Czekoladek</h3>
            <div class="modal-hint">Przesuń w dół, aby zobaczyć inne czekoladki</div>
          </div>
          <div style="display:flex;gap:10px;align-items:center;">
            <div class="mission" style="min-width:150px;">
              <div class="mission-title">Zdobądź wszystkie</div>
              <div class="mission-row">
                <div class="mission-count"><span id="foundCount">0</span>/17</div>
                <div class="mission-badge" aria-hidden="true">🎁</div>
              </div>
            </div>
            <button class="close" id="closeModal" aria-label="Zamknij">✕</button>
          </div>
        </div>
      </div>

      <div class="modal-body" id="modalBody" aria-live="polite">
        <!-- grid wypełniany przez JS -->
      </div>

      <!-- nowy boczny pasek przewijania (track + thumb) -->
      <div class="side-scrollbar" id="sideScrollbar" aria-hidden="false">
        <div class="track" id="scrollTrack" aria-hidden="true">
          <div class="thumb" id="scrollThumb" tabindex="0" role="scrollbar" aria-label="Pasek przewijania czekoladek"></div>
        </div>
      </div>

      <div class="modal-inner" style="display:flex;flex-direction:column;gap:8px;">
        <!-- bottom controls removed; use top ✕ button to close modal -->
      </div> 
    </div>
  </div>

<script>
/* Zmiany:
   - usunięto środkowy/rotowany input range
   - dodano własny boczny pasek (track + thumb) po prawej stronie modal
   - pasek klikalny, przeciągany (pointer events), oraz aktualizowany przy przewijaniu/resize
*/

const captions = [
  "Czekoladowy aniołek – odmów „Zdrowaś Maryjo”.",
  "Czekoladowa gwiazdka – uśmiechnij się do kogoś dziś.",
  "Czekoladowe serduszko – powiedz komuś coś miłego.",
  "Czekoladowy dzwoneczek – odmów „Ojcze nasz”.",
  "Czekoladowy renifer – pomódl się za kolegę lub koleżankę.",
  "Czekoladowy krzyżyk – pomódl się za osobę, której nie lubisz.",
  "Czekoladowa choinka – podziękuj Bogu za dzisiejszy dzień.",
  "Czekoladowe dzieciątko Jezus – odmów „Aniele Boży”.",
  "Czekoladowy baranek – powiedz komuś „dziękuję”.",
  "Czekoladowa gwiazdka spadająca – nie kłóć się dziś z nikim.",
  "Czekoladowy Mikołaj – zrób komuś drobną przysługę.",
  "Czekoladowy anioł z trąbką – pomódl się za swoją rodzinę.",
  "Czekoladowy domek – pomóż w domu w jednej rzeczy.",
  "Czekoladowy płatek śniegu – postaraj się dziś być spokojny i miły.",
  "Czekoladowy pastuszek – odmów „Chwała Ojcu”.",
  "Czekoladowa świeca – pomódl się za zmarłych.",
  "Czekoladowe serce z napisem „Miłość” – powiedz komuś „kocham cię”."
];
const names = captions.map(c=>c.split('–')[0].trim());
const ITEMS_COUNT = 17;

const drawBtn = document.getElementById('drawBtn');
const centralImg = document.getElementById('centralImg');
const placeholderText = document.getElementById('placeholderText');
const captionEl = document.getElementById('caption');
const drawCountdownEl = document.getElementById('drawCountdown');
const dec24El = document.getElementById('dec24');
const openBook = document.getElementById('openBook');
const modalBackdrop = document.getElementById('modalBackdrop');
const modalBody = document.getElementById('modalBody');
const closeModal = document.getElementById('closeModal');
const foundCountEl = document.getElementById('foundCount');
const verticalScrollbar = document.getElementById('sideScrollbar');
const track = document.getElementById('scrollTrack');
const thumb = document.getElementById('scrollThumb');
const previewEl = document.getElementById('preview');

const DISC_KEY = 'sg_discovered_v1';
const LAST_KEY = 'sg_lastDraw_v1';
const LAST_IDX_KEY = 'sg_lastIndex_v1';

function loadDiscovered(){
  const raw = localStorage.getItem(DISC_KEY);
  if(!raw){ const arr = Array(ITEMS_COUNT).fill(false); localStorage.setItem(DISC_KEY, JSON.stringify(arr)); return arr; }
  try{ const p = JSON.parse(raw); if(Array.isArray(p) && p.length === ITEMS_COUNT) return p; }catch(e){}
  const arr = Array(ITEMS_COUNT).fill(false); localStorage.setItem(DISC_KEY, JSON.stringify(arr)); return arr;
}
function saveDiscovered(){ localStorage.setItem(DISC_KEY, JSON.stringify(discovered)); }
function loadLastDraw(){ const raw = localStorage.getItem(LAST_KEY); if(!raw) return null; const d = new Date(raw); return isNaN(d)?null:d; }
function saveLastDraw(d){ localStorage.setItem(LAST_KEY, d.toISOString()); }
function loadLastIndex(){ const raw = localStorage.getItem(LAST_IDX_KEY); if(!raw) return null; const i = parseInt(raw,10); return Number.isInteger(i) && i>=0 && i<ITEMS_COUNT ? i : null; }
function saveLastIndex(i){ localStorage.setItem(LAST_IDX_KEY, String(i)); }

let discovered = loadDiscovered();
let lastDraw = loadLastDraw();
let lastIndex = loadLastIndex();

// ensure shuffle state vars exist
let shuffleInterval = null;
let isShuffling = false;
function randomInt(max){ return Math.floor(Math.random()*max); }

// image fallback
function tryImageSources(imgEl, index){
  const candidates = [
    `zdjęcie_${index+1}.png`,
    `zdjecie_${index+1}.png`,
    `zdjecie-${index+1}.png`,
    `images/zdjęcie_${index+1}.png`,
    `images/zdjecie_${index+1}.png`
  ];
  let tried = 0;
  imgEl.onerror = function(){
    tried++;
    if(tried < candidates.length) imgEl.src = candidates[tried];
    else {
      imgEl.src = 'data:image/svg+xml;utf8,'+encodeURIComponent(`<svg xmlns="http://www.w3.org/2000/svg" width="400" height="300"><rect width="100%" height="100%" fill="#f3f9ff"/><text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" fill="#2b7bd6" font-family="Roboto,Arial" font-size="20">Brak obrazka</text></svg>`);
      imgEl.onerror=null;
    }
  };
  imgEl.src = candidates[0];
}

// preview helpers
function showPlaceholder(){ centralImg.style.display='none'; placeholderText.style.display='block'; previewEl.classList.remove('show-overlay'); }
function showImage(idx){
  tryImageSources(centralImg, idx);
  centralImg.alt = names[idx] || `Czekoladka ${idx+1}`;
  centralImg.style.display='block';
  placeholderText.style.display='none';
  if(!discovered[idx]){ centralImg.classList.add('dimmed'); previewEl.classList.add('show-overlay'); }
  else { centralImg.classList.remove('dimmed'); previewEl.classList.remove('show-overlay'); }
}

// countdowns & cooldowns (same logic)
function getNextDec24(){ const now=new Date(); const y=now.getFullYear(); const cand=new Date(y,11,24,0,0,0); return cand>now?cand:new Date(y+1,11,24,0,0,0); }
function updateDec24(){ const t=getNextDec24(); const now=new Date(); let diff=Math.max(0,t.getTime()-now.getTime()); const days=Math.floor(diff/(1000*60*60*24)); diff-=days*(1000*60*60*24); const hours=Math.floor(diff/(1000*60*60)); diff-=hours*(1000*60*60); const minutes=Math.floor(diff/(1000*60)); diff-=minutes*(1000*60); const seconds=Math.floor(diff/1000); dec24El.textContent=`${days} dni ${String(hours).padStart(2,'0')}:${String(minutes).padStart(2,'0')}:${String(seconds).padStart(2,'0')}`; }
function secondsUntilNextDraw(){ if(!lastDraw)return 0; const now=new Date(); const target=new Date(lastDraw.getTime()+24*60*60*1000); return Math.max(0,Math.floor((target.getTime()-now.getTime())/1000)); }
function formatHMS(sec){ if(sec<=0) return '0:00:00'; const h=Math.floor(sec/3600); const m=Math.floor((sec%3600)/60); const s=sec%60; return `${h}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`; }
function updateDrawCooldownUI(){ const sec=secondsUntilNextDraw(); if(sec>0){ drawCountdownEl.textContent=formatHMS(sec); drawBtn.textContent='Już losowałeś dziś'; drawBtn.disabled=true; drawBtn.classList.add('inactive'); } else { drawCountdownEl.textContent='Możesz losować'; drawBtn.textContent='Wylosuj'; drawBtn.disabled=false; drawBtn.classList.remove('inactive'); } }

function startShuffle(duration=1400){
  if(isShuffling) return;
  isShuffling = true;
  drawBtn.disabled = true;
  drawBtn.classList.add('inactive');
  drawBtn.textContent = 'Losuję…';
  captionEl.textContent = 'Losowanie trwa — oglądaj migawki czekoladek!';

  // szybkie migawki
  shuffleInterval = setInterval(()=>{
    const r = randomInt(ITEMS_COUNT);
    showImage(r);
    centralImg.style.transform = `scale(${1+Math.random()*0.03}) rotate(${(Math.random()-0.5)*5}deg)`;
  }, 70);

  setTimeout(()=>{
    // zatrzymaj migawki
    clearInterval(shuffleInterval);
    shuffleInterval = null;

    // wybór finalny
    const final = randomInt(ITEMS_COUNT);

    // pokaż finalny obraz od razu, ale w stanie PRZYCIEMNIONYM z overlayem
    showImage(final);
    centralImg.style.transform = 'scale(1) rotate(0deg)';
    centralImg.classList.add('dimmed');        // przyciemniony (CSS ma transition)
    previewEl.classList.add('show-overlay');  // overlay (ma transition opacity)
    captionEl.textContent = '...';

    // po krótkim czasie stopniowo odsłoń obraz
    const REVEAL_DELAY = 700; // ms
    setTimeout(()=>{
      // animacja transition -> usunięcie klasy spowoduje płynne przejście w CSS
      previewEl.classList.remove('show-overlay');
      centralImg.classList.remove('dimmed');

      // ustaw finalny tekst i zapisz odkrycie
      captionEl.textContent = captions[final];
      if(!discovered[final]){
        discovered[final]=true; saveDiscovered(); renderGrid();
      }

      // zapisz ostatnie losowanie & cooldown
      lastDraw=new Date(); lastIndex=final; saveLastDraw(lastDraw); saveLastIndex(final);
      updateDrawCooldownUI();

      // przywróć przycisk do stanu aktywnego (cooldown UI go zaktualizuje)
      isShuffling=false;
    }, REVEAL_DELAY);

  }, duration);
}

// render full grid (ALL items, no pagination)
function renderGrid(){
  modalBody.innerHTML = '';
  const grid = document.createElement('div');
  grid.className = 'grid';

  for(let idx=0; idx<ITEMS_COUNT; idx++){
    const item = document.createElement('div');
    item.className = 'cardItem';
    item.setAttribute('data-index', idx);
    const imgWrap = document.createElement('div');
    imgWrap.className = 'imgWrap';
    const img = document.createElement('img');
    tryImageSources(img, idx);
    img.alt = names[idx] || `Czekoladka ${idx+1}`;
    const overlay = document.createElement('div');
    overlay.className = 'overlay';
    overlay.setAttribute('aria-hidden','true');
    overlay.textContent = '?';
    imgWrap.appendChild(img);
    imgWrap.appendChild(overlay);

    const name = document.createElement('div');
    name.className = 'name';
    name.textContent = discovered[idx] ? names[idx] : '???';

    const desc = document.createElement('div');
    desc.className = 'desc';
    desc.textContent = discovered[idx] ? captions[idx] : 'Odkryj tę czekoladkę, aby poznać misję.';

    if(!discovered[idx]) item.classList.add('dimmed');

    item.appendChild(imgWrap);
    item.appendChild(name);
    item.appendChild(desc);

    item.addEventListener('click', ()=>{
      showImage(idx);
      captionEl.textContent = discovered[idx] ? captions[idx] : 'Wciąż możesz odkryć tę czekoladkę ponownie.';
      lastIndex = idx; saveLastIndex(idx);
      modalBackdrop.classList.remove('show'); modalBackdrop.setAttribute('aria-hidden','true');
    });

    grid.appendChild(item);
  }

  modalBody.appendChild(grid);
  foundCountEl.textContent = discovered.filter(Boolean).length;
  // after rendering, update custom scrollbar
  updateSideScrollbar();
}

// CUSTOM SIDEBAR: synchronizuje się z modalBody.scrollTop
function updateSideScrollbar(){
  // align side scrollbar to modal body (top/height)
  positionSidebar();

  const contentH = modalBody.scrollHeight;
  const visibleH = modalBody.clientHeight;
  const trackH = track.clientHeight;
  const maxScroll = Math.max(0, contentH - visibleH);

  if(maxScroll <= 0){ verticalScrollbar.setAttribute('aria-hidden','true'); return; }
  verticalScrollbar.setAttribute('aria-hidden','false');

  const thumbH = Math.max(40, Math.round((visibleH / contentH) * trackH));
  const maxThumbTop = trackH - thumbH;
  const pct = modalBody.scrollTop / maxScroll;
  const top = Math.round(pct * maxThumbTop);

  thumb.style.height = thumbH + 'px';
  thumb.style.top = top + 'px';
}

// clicking on track jumps to position
track.addEventListener('click', (e)=>{
  if(e.target === thumb) return;
  const rect = track.getBoundingClientRect();
  const clickY = e.clientY - rect.top;
  const thumbH = thumb.clientHeight || 40;
  const maxTop = track.clientHeight - thumbH;
  const newTop = Math.min(Math.max(0, clickY - thumbH/2), maxTop);
  const pct = newTop / (maxTop || 1);
  modalBody.scrollTop = Math.round(pct * (modalBody.scrollHeight - modalBody.clientHeight));
  updateSideScrollbar();
});

// pointer drag on thumb
let dragging = false;
let dragStartY = 0;
let startTop = 0;
function onThumbMove(e){
  if(!dragging) return;
  const dy = e.clientY - dragStartY;
  const trackH = track.clientHeight;
  const thumbH = thumb.clientHeight;
  const maxTop = Math.max(0, trackH - thumbH);
  const newTop = Math.min(Math.max(0, startTop + dy), maxTop);
  const pct = newTop / (maxTop || 1);
  modalBody.scrollTop = Math.round(pct * (modalBody.scrollHeight - modalBody.clientHeight));
  updateSideScrollbar();
}
function onThumbUp(e){
  dragging = false;
  document.removeEventListener('pointermove', onThumbMove);
  document.removeEventListener('pointerup', onThumbUp);
}
thumb.addEventListener('pointerdown', (e)=>{
  dragging = true;
  dragStartY = e.clientY;
  startTop = parseInt(thumb.style.top || '0', 10) || 0;
  thumb.setPointerCapture && thumb.setPointerCapture(e.pointerId);
  document.addEventListener('pointermove', onThumbMove);
  document.addEventListener('pointerup', onThumbUp);
  e.preventDefault();
});

// keyboard control for accessibility
thumb.addEventListener('keydown', (e)=>{
  if(e.key==='ArrowDown') modalBody.scrollBy({top:40,behavior:'smooth'});
  if(e.key==='ArrowUp') modalBody.scrollBy({top:-40,behavior:'smooth'});
  if(e.key==='PageDown') modalBody.scrollBy({top: modalBody.clientHeight, behavior:'smooth'});
  if(e.key==='PageUp') modalBody.scrollBy({top: -modalBody.clientHeight, behavior:'smooth'});
});

modalBody.addEventListener('scroll', () => {
  if (typeof window.__sideTimer !== 'undefined') clearTimeout(window.__sideTimer);
  window.__sideTimer = setTimeout(()=>{ updateSideScrollbar(); }, 30);
});

// ustawiamy top i height suwaka tak aby zawsze wyrównywał się do modalBody
function positionSidebar(){
  const parentModal = verticalScrollbar.parentElement; // .modal
  const modalRect = parentModal.getBoundingClientRect();
  const bodyRect = modalBody.getBoundingClientRect();

  const top = bodyRect.top - modalRect.top; // distance from top of modal
  const height = modalBody.clientHeight;

  verticalScrollbar.style.top = Math.max(8, top) + 'px';
  verticalScrollbar.style.height = (height) + 'px';
}

// resize handler
window.addEventListener('resize', () => {
  positionSidebar();
  updateSideScrollbar();
});

// events
drawBtn.addEventListener('click', ()=>{ if(secondsUntilNextDraw()>0){ updateDrawCooldownUI(); return; } startShuffle(1400); });
openBook.addEventListener('click', ()=>{ renderGrid(); modalBackdrop.classList.add('show'); modalBackdrop.setAttribute('aria-hidden','false'); setTimeout(()=>{ positionSidebar(); updateSideScrollbar(); }, 80); });
closeModal.addEventListener('click', ()=>{ modalBackdrop.classList.remove('show'); modalBackdrop.setAttribute('aria-hidden','true'); });

modalBackdrop.addEventListener('click',(e)=>{ if(e.target===modalBackdrop){ modalBackdrop.classList.remove('show'); modalBackdrop.setAttribute('aria-hidden','true'); } });




// keyboard: Arrow keys/PageUp/PageDown for convenience
document.addEventListener('keydown', (e)=>{
  if(modalBackdrop.classList.contains('show')){
    if(e.key==='ArrowDown') modalBody.scrollBy({top:120,behavior:'smooth'});
    if(e.key==='ArrowUp') modalBody.scrollBy({top:-120,behavior:'smooth'});
    if(e.key==='PageDown') modalBody.scrollBy({top: modalBody.clientHeight, behavior:'smooth'});
    if(e.key==='PageUp') modalBody.scrollBy({top:-modalBody.clientHeight,behavior:'smooth'});
    if(e.key==='Escape'){ modalBackdrop.classList.remove('show'); modalBackdrop.setAttribute('aria-hidden','true'); }
  }
});

// init
(function init(){
  discovered = loadDiscovered();
  lastDraw = loadLastDraw();
  lastIndex = loadLastIndex();

  if(Number.isInteger(lastIndex) && lastIndex>=0 && lastIndex<ITEMS_COUNT){
    showImage(lastIndex);
    captionEl.textContent = discovered[lastIndex] ? captions[lastIndex] : 'Wciąż możesz odkryć tę czekoladkę ponownie.';
  } else {
    showPlaceholder();
  }

  updateDec24();
  updateDrawCooldownUI();
  setInterval(updateDec24,1000);
  setInterval(updateDrawCooldownUI,1000);
  foundCountEl.textContent = discovered.filter(Boolean).length;

  // ensure scrollbar positioned initially
  setTimeout(()=>{ positionSidebar(); updateSideScrollbar(); }, 120);
})();
</script>
</body>
</html>
