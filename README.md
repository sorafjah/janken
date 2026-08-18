<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<title>じゃんけんアプリ</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=BIZ+UDPGothic:wght@400;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg: #FFF8EE;
    --panel: #FFFFFF;
    --ink: #3A3226;
    --sub: #8A7F6E;
    --line: #EFE4CF;
    --accent: #FF9B4A;
    --accent-deep: #E8792C;
    --win: #4CAF7D;
    --win-bg: #E7F6EE;
    --lose: #E8654B;
    --lose-bg: #FDEAE5;
    --draw: #4F8FE8;
    --draw-bg: #EAF2FD;
    --gu: #6B8FE0;
    --choki: #E0A75E;
    --pa: #7BC17E;
    --shadow: 0 10px 26px rgba(58,50,38,0.10);
    --radius: 26px;
  }
  *{ box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html, body{
    margin:0; padding:0; width:100%; height:100%;
    overscroll-behavior: none;
  }
  body{
    background: var(--bg);
    color: var(--ink);
    font-family: 'BIZ UDPGothic', 'Hiragino Maru Gothic ProN', sans-serif;
    touch-action: manipulation;
    -webkit-user-select: none;
    user-select: none;
    display: flex;
    flex-direction: column;
    min-height: 100vh;
    min-height: 100dvh;
  }
  ruby rt{ font-size: 0.5em; color: var(--sub); }

  .screen{
    flex: 1;
    display: none;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: clamp(16px, 3vw, 36px);
    width: 100%;
  }
  .screen.active{ display: flex; }

  /* ===== 共通ボタン ===== */
  button{
    font-family: inherit;
    border: none;
    cursor: pointer;
    touch-action: manipulation;
  }
  .btn-main{
    background: var(--accent);
    color: #fff;
    font-size: clamp(22px, 3.6vw, 30px);
    font-weight: 700;
    padding: 20px 56px;
    border-radius: 999px;
    box-shadow: 0 8px 0 var(--accent-deep);
    min-height: 64px;
    transition: transform .08s ease;
  }
  .btn-main:active{
    transform: translateY(6px);
    box-shadow: 0 2px 0 var(--accent-deep);
  }
  .btn-sub{
    background: var(--panel);
    color: var(--ink);
    border: 3px solid var(--line);
    font-size: clamp(16px, 2.4vw, 20px);
    font-weight: 700;
    padding: 14px 28px;
    border-radius: 999px;
    min-height: 52px;
  }
  .icon-btn{
    width: 52px; height: 52px;
    border-radius: 50%;
    background: var(--panel);
    border: 3px solid var(--line);
    font-size: 24px;
    display: flex; align-items: center; justify-content: center;
  }

  /* ===== スタート画面 ===== */
  #screen-start{ text-align: center; gap: 18px; }
  .title-wrap{ margin-bottom: 6px; }
  .title-emoji{ font-size: clamp(50px, 9vw, 78px); line-height: 1; }
  .title-text{
    font-size: clamp(30px, 5vw, 46px);
    font-weight: 700;
    margin: 6px 0 0;
  }
  .lead-card{
    background: var(--panel);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    padding: clamp(20px, 3.5vw, 32px);
    max-width: 560px;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 14px;
  }
  .lead-card p{
    font-size: clamp(16px, 2.2vw, 19px);
    line-height: 1.9;
    margin: 0;
    color: var(--ink);
  }
  .cam-badge{
    display: flex; align-items: center; gap: 10px;
    background: var(--bg);
    border-radius: 16px;
    padding: 12px 18px;
    font-size: clamp(14px, 1.9vw, 17px);
    color: var(--sub);
  }
  #start-status{
    font-size: 16px;
    color: var(--sub);
    min-height: 24px;
  }
  #start-status.err{ color: var(--lose); font-weight: 700; }

  .settings-toggle-area{
    position: fixed;
    top: 14px; right: 14px;
    display: flex; gap: 10px;
    z-index: 20;
  }

  /* ===== 設定パネル ===== */
  #settings-overlay{
    position: fixed; inset: 0;
    background: rgba(58,50,38,0.35);
    display: none;
    align-items: center; justify-content: center;
    z-index: 100;
    padding: 16px;
  }
  #settings-overlay.show{ display: flex; }
  .settings-panel{
    background: var(--panel);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    padding: 28px;
    max-width: 480px;
    width: 100%;
    max-height: 88vh;
    overflow-y: auto;
  }
  .settings-panel h2{
    font-size: 22px;
    margin: 0 0 18px;
    display: flex; align-items: center; gap: 8px;
  }
  .set-row{
    margin-bottom: 22px;
  }
  .set-row-head{
    display: flex; justify-content: space-between; align-items: center;
    margin-bottom: 8px;
  }
  .set-row-head label{ font-weight: 700; font-size: 16px; }
  .set-desc{ font-size: 13px; color: var(--sub); margin: 0 0 10px; line-height: 1.6; }
  .stepper{
    display: flex; align-items: center; gap: 10px;
    background: var(--bg);
    border-radius: 14px;
    padding: 6px;
  }
  .stepper button{
    width: 44px; height: 44px;
    border-radius: 10px;
    background: var(--panel);
    border: 2px solid var(--line);
    font-size: 20px;
    font-weight: 700;
    color: var(--accent-deep);
  }
  .stepper input{
    flex: 1;
    text-align: center;
    font-size: 18px;
    font-family: inherit;
    font-weight: 700;
    border: none;
    background: transparent;
    color: var(--ink);
    width: 40px;
  }
  .toggle-row{
    display: flex; justify-content: space-between; align-items: center;
    background: var(--bg);
    border-radius: 14px;
    padding: 12px 16px;
  }
  .switch{
    position: relative;
    width: 54px; height: 32px;
    flex-shrink: 0;
  }
  .switch input{ opacity: 0; width: 0; height: 0; }
  .slider-toggle{
    position: absolute; inset: 0;
    background: #D8CCB4;
    border-radius: 999px;
    transition: .2s;
    cursor: pointer;
  }
  .slider-toggle::before{
    content: "";
    position: absolute;
    width: 26px; height: 26px;
    left: 3px; top: 3px;
    background: #fff;
    border-radius: 50%;
    transition: .2s;
    box-shadow: 0 2px 4px rgba(0,0,0,0.2);
  }
  input:checked + .slider-toggle{ background: var(--win); }
  input:checked + .slider-toggle::before{ transform: translateX(22px); }
  .seg{
    display: flex;
    background: var(--bg);
    border-radius: 14px;
    padding: 5px;
    gap: 5px;
  }
  .seg button{
    flex: 1;
    background: transparent;
    border-radius: 10px;
    padding: 12px 4px;
    font-size: 14px;
    font-weight: 700;
    color: var(--sub);
  }
  .seg button.active{
    background: var(--accent);
    color: #fff;
  }
  .settings-close{
    display: block;
    width: 100%;
    margin-top: 6px;
    background: var(--accent);
    color: #fff;
    font-weight: 700;
    font-size: 17px;
    padding: 16px;
    border-radius: 14px;
  }

  /* ===== 対戦画面 ===== */
  #screen-battle{
    width: 100%;
    max-width: 1100px;
    gap: 14px;
    padding-top: 8px;
  }
  .score-bar{
    width: 100%;
    display: flex;
    justify-content: center;
    gap: clamp(14px, 3vw, 30px);
    margin-bottom: 4px;
  }
  .score-chip{
    background: var(--panel);
    border-radius: 16px;
    padding: 10px 20px;
    box-shadow: var(--shadow);
    text-align: center;
    min-width: 78px;
  }
  .score-chip .num{ font-size: clamp(22px, 3vw, 28px); font-weight: 700; }
  .score-chip .lbl{ font-size: 12px; color: var(--sub); }
  .score-chip.win .num{ color: var(--win); }
  .score-chip.lose .num{ color: var(--lose); }
  .score-chip.draw .num{ color: var(--draw); }

  .battle-stage{
    width: 100%;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: clamp(12px, 2.4vw, 24px);
  }
  @media (max-width: 640px){
    .battle-stage{ grid-template-columns: 1fr; }
  }
  .stage-card{
    background: var(--panel);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    padding: 16px;
    display: flex;
    flex-direction: column;
    align-items: center;
    position: relative;
    overflow: hidden;
  }
  .stage-card h3{
    margin: 0 0 10px;
    font-size: clamp(15px, 2vw, 18px);
    color: var(--sub);
  }
  .cam-wrap{
    position: relative;
    width: 100%;
    aspect-ratio: 4/3;
    border-radius: 18px;
    overflow: hidden;
    background: #222;
  }
  #video{
    width: 100%; height: 100%;
    object-fit: cover;
    transform: scaleX(-1);
  }
  #overlay{
    position: absolute; inset: 0;
    width: 100%; height: 100%;
    transform: scaleX(-1);
    pointer-events: none;
  }
  .cam-status{
    position: absolute;
    left: 10px; bottom: 10px; right: 10px;
    background: rgba(58,50,38,0.72);
    color: #fff;
    font-size: 14px;
    padding: 8px 12px;
    border-radius: 10px;
    text-align: center;
    line-height: 1.5;
    display: none;
  }
  .cam-status.show{ display: block; }
  .cpu-hand-area{
    width: 100%;
    aspect-ratio: 4/3;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .hand-emoji{
    font-size: clamp(80px, 14vw, 150px);
    line-height: 1;
    transition: transform .15s ease;
  }
  .shake{
    animation: shakeAnim .5s infinite;
  }
  @keyframes shakeAnim{
    0%,100%{ transform: rotate(-8deg); }
    50%{ transform: rotate(8deg); }
  }

  .countdown-overlay{
    position: fixed; inset: 0;
    background: rgba(255,248,238,0.94);
    display: none;
    align-items: center; justify-content: center;
    z-index: 50;
  }
  .countdown-overlay.show{ display: flex; }
  #countdown-text{
    font-size: clamp(70px, 16vw, 160px);
    font-weight: 700;
    color: var(--accent-deep);
  }

  .result-banner{
    width: 100%;
    text-align: center;
    padding: 18px;
    border-radius: 20px;
    font-size: clamp(24px, 4vw, 36px);
    font-weight: 700;
    min-height: 84px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: var(--panel);
    color: var(--sub);
    box-shadow: var(--shadow);
  }
  .result-banner.win{ background: var(--win-bg); color: var(--win); }
  .result-banner.lose{ background: var(--lose-bg); color: var(--lose); }
  .result-banner.draw{ background: var(--draw-bg); color: var(--draw); }

  .battle-controls{
    display: flex;
    gap: 14px;
    width: 100%;
    justify-content: center;
    flex-wrap: wrap;
  }

  /* ===== 結果画面 ===== */
  #screen-result{ text-align: center; gap: 22px; }
  #screen-result h2{
    font-size: clamp(26px, 4vw, 36px);
    margin: 0;
  }
  .result-grid{
    display: flex;
    gap: 18px;
    flex-wrap: wrap;
    justify-content: center;
  }
  .result-card{
    background: var(--panel);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    padding: 26px 34px;
    min-width: 130px;
  }
  .result-card .num{ font-size: clamp(36px, 6vw, 54px); font-weight: 700; }
  .result-card .lbl{ font-size: 15px; color: var(--sub); margin-top: 4px; }
  .result-card.win .num{ color: var(--win); }
  .result-card.lose .num{ color: var(--lose); }
  .result-card.draw .num{ color: var(--draw); }
  .result-msg{
    font-size: clamp(18px, 2.6vw, 22px);
    color: var(--ink);
  }

  .footer-note{
    font-size: 12px;
    color: var(--sub);
    text-align: center;
    margin-top: 10px;
    max-width: 480px;
  }
</style>
</head>
<body>

<div class="settings-toggle-area">
  <button class="icon-btn" id="btn-open-settings" aria-label="設定">⚙️</button>
</div>

<!-- ================= スタート画面 ================= -->
<section class="screen active" id="screen-start">
  <div class="title-wrap">
    <div class="title-emoji">✊✌️✋</div>
    <p class="title-text"><ruby>手<rt>て</rt></ruby>で<ruby>勝負<rt>しょうぶ</rt></ruby>！じゃんけんアプリ</p>
  </div>
  <div class="lead-card">
    <p>カメラの<ruby>前<rt>まえ</rt></ruby>に<ruby>立<rt>た</rt></ruby>って、<br>ぐー・ちょき・ぱーの<ruby>手<rt>て</rt></ruby>を<ruby>出<rt>だ</rt></ruby>してね。</p>
    <div class="cam-badge">📷 <span>はじめる<ruby>前<rt>まえ</rt></ruby>にカメラの<ruby>許可<rt>きょか</rt></ruby>をおしてね</span></div>
    <button class="btn-main" id="btn-start">はじめる</button>
    <div id="start-status"></div>
  </div>
  <p class="footer-note">このアプリはiPadのカメラを<ruby>使<rt>つか</rt></ruby>います。カメラの<ruby>映像<rt>えいぞう</rt></ruby>は<ruby>保存<rt>ほぞん</rt></ruby>・<ruby>送信<rt>そうしん</rt></ruby>されません。</p>
</section>

<!-- ================= 対戦画面 ================= -->
<section class="screen" id="screen-battle">
  <div class="score-bar">
    <div class="score-chip win"><div class="num" id="score-win">0</div><div class="lbl"><ruby>勝<rt>か</rt></ruby>ち</div></div>
    <div class="score-chip lose"><div class="num" id="score-lose">0</div><div class="lbl"><ruby>負<rt>ま</rt></ruby>け</div></div>
    <div class="score-chip draw"><div class="num" id="score-draw">0</div><div class="lbl">あいこ</div></div>
  </div>

  <div class="battle-stage">
    <div class="stage-card">
      <h3>アプリの<ruby>手<rt>て</rt></ruby></h3>
      <div class="cpu-hand-area">
        <div class="hand-emoji" id="cpu-hand">✊</div>
      </div>
    </div>
    <div class="stage-card">
      <h3>あなたの<ruby>手<rt>て</rt></ruby></h3>
      <div class="cam-wrap">
        <video id="video" autoplay playsinline muted></video>
        <canvas id="overlay"></canvas>
        <div class="cam-status" id="cam-status"></div>
      </div>
    </div>
  </div>

  <div class="result-banner" id="result-banner">カメラの<ruby>前<rt>まえ</rt></ruby>で<ruby>手<rt>て</rt></ruby>を<ruby>出<rt>だ</rt></ruby>してね</div>

  <div class="battle-controls">
    <button class="btn-main" id="btn-play">せーの、で<ruby>勝負<rt>しょうぶ</rt></ruby>！</button>
    <button class="btn-sub" id="btn-finish"><ruby>終<rt>お</rt></ruby>わる</button>
  </div>
</section>

<!-- ================= 結果画面 ================= -->
<section class="screen" id="screen-result">
  <h2><ruby>結果<rt>けっか</rt></ruby>だよ！</h2>
  <div class="result-grid">
    <div class="result-card win"><div class="num" id="final-win">0</div><div class="lbl"><ruby>勝<rt>か</rt></ruby>ち</div></div>
    <div class="result-card lose"><div class="num" id="final-lose">0</div><div class="lbl"><ruby>負<rt>ま</rt></ruby>け</div></div>
    <div class="result-card draw"><div class="num" id="final-draw">0</div><div class="lbl">あいこ</div></div>
  </div>
  <p class="result-msg" id="result-msg"></p>
  <button class="btn-main" id="btn-again">もう<ruby>一回<rt>いっかい</rt></ruby></button>
</section>

<!-- カウントダウン演出 -->
<div class="countdown-overlay" id="countdown-overlay">
  <div id="countdown-text">さいしょは</div>
</div>

<!-- ================= 設定パネル ================= -->
<div id="settings-overlay">
  <div class="settings-panel">
    <h2>⚙️ <ruby>先生<rt>せんせい</rt></ruby>の<ruby>設定<rt>せってい</rt></ruby></h2>

    <div class="set-row">
      <div class="set-row-head"><label>はんていのゆるさ</label></div>
      <p class="set-desc"><ruby>手<rt>て</rt></ruby>の<ruby>形<rt>かたち</rt></ruby>がきれいに<ruby>出<rt>だ</rt></ruby>せなくても<ruby>認識<rt>にんしき</rt></ruby>しやすくするよ。<ruby>生徒<rt>せいと</rt></ruby>さんに<ruby>合<rt>あ</rt></ruby>わせてえらんでね。</p>
      <div class="seg" id="seg-difficulty">
        <button data-val="strict">きびしめ</button>
        <button data-val="normal" class="active">ふつう</button>
        <button data-val="loose">ゆるめ</button>
      </div>
    </div>

    <div class="set-row">
      <div class="set-row-head"><label>カウントダウンの<ruby>速<rt>はや</rt></ruby>さ（<ruby>秒<rt>びょう</rt></ruby>）</label></div>
      <p class="set-desc">「さいしょは・ぐー・じゃんけん・ぽん」の1つずつの<ruby>間隔<rt>かんかく</rt></ruby>だよ。</p>
      <div class="stepper">
        <button id="cd-minus">−</button>
        <input type="number" id="cd-value" value="1.0" step="0.1" min="0.5" max="3.0" readonly>
        <button id="cd-plus">＋</button>
      </div>
    </div>

    <div class="set-row">
      <div class="toggle-row">
        <label for="sfx-toggle"><ruby>効果音<rt>こうかおん</rt></ruby></label>
        <label class="switch">
          <input type="checkbox" id="sfx-toggle" checked>
          <span class="slider-toggle"></span>
        </label>
      </div>
    </div>

    <div class="set-row">
      <div class="toggle-row">
        <label for="guide-toggle">カメラに<ruby>枠<rt>わく</rt></ruby>を<ruby>表示<rt>ひょうじ</rt></ruby></label>
        <label class="switch">
          <input type="checkbox" id="guide-toggle" checked>
          <span class="slider-toggle"></span>
        </label>
      </div>
    </div>

    <button class="settings-close" id="btn-close-settings">とじる</button>
  </div>
</div>

<script type="module">
import { HandLandmarker, FilesetResolver } from "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.14";

/* ===================== 状態管理 ===================== */
const state = {
  difficulty: "normal",     // strict / normal / loose
  countdownSec: 1.0,
  sfxOn: true,
  guideOn: true,
  scores: { win: 0, lose: 0, draw: 0 },
  handLandmarker: null,
  video: null,
  overlayCtx: null,
  stream: null,
  detecting: false,
  lastVideoTime: -1,
  currentGesture: null,     // "gu" | "choki" | "pa" | null
  gestureStableFrames: 0,
  roundActive: false,
  animFrameId: null,
};

const HAND_EMOJI = { gu: "✊", choki: "✌️", pa: "✋" };
const HAND_NAME  = { gu: "ぐー", choki: "ちょき", pa: "ぱー" };

/* ===================== 要素取得 ===================== */
const el = (id) => document.getElementById(id);
const screens = {
  start: el("screen-start"),
  battle: el("screen-battle"),
  result: el("screen-result"),
};
function showScreen(name){
  Object.values(screens).forEach(s => s.classList.remove("active"));
  screens[name].classList.add("active");
}

/* ===================== 設定パネル ===================== */
el("btn-open-settings").addEventListener("click", () => {
  el("settings-overlay").classList.add("show");
});
el("btn-close-settings").addEventListener("click", () => {
  el("settings-overlay").classList.remove("show");
});

// 判定の緩さ
const segButtons = document.querySelectorAll("#seg-difficulty button");
segButtons.forEach(btn => {
  btn.addEventListener("click", () => {
    segButtons.forEach(b => b.classList.remove("active"));
    btn.classList.add("active");
    state.difficulty = btn.dataset.val;
  });
});

// カウントダウン速さ
const cdInput = el("cd-value");
function clampCd(v){ return Math.min(3.0, Math.max(0.5, Math.round(v * 10) / 10)); }
el("cd-minus").addEventListener("click", () => {
  state.countdownSec = clampCd(state.countdownSec - 0.1);
  cdInput.value = state.countdownSec.toFixed(1);
});
el("cd-plus").addEventListener("click", () => {
  state.countdownSec = clampCd(state.countdownSec + 0.1);
  cdInput.value = state.countdownSec.toFixed(1);
});

// 効果音
el("sfx-toggle").addEventListener("change", (e) => { state.sfxOn = e.target.checked; });
// 枠表示
el("guide-toggle").addEventListener("change", (e) => { state.guideOn = e.target.checked; });

/* ===================== 効果音（WebAudio） ===================== */
let audioCtx = null;
function ensureAudioCtx(){
  if (!audioCtx) {
    try { audioCtx = new (window.AudioContext || window.webkitAudioContext)(); }
    catch(e){ audioCtx = null; }
  }
  return audioCtx;
}
function playTone(freq, duration, type = "sine", gainVal = 0.18){
  if (!state.sfxOn) return;
  const ctx = ensureAudioCtx();
  if (!ctx) return;
  const osc = ctx.createOscillator();
  const gain = ctx.createGain();
  osc.type = type;
  osc.frequency.value = freq;
  gain.gain.value = gainVal;
  osc.connect(gain).connect(ctx.destination);
  const now = ctx.currentTime;
  gain.gain.setValueAtTime(gainVal, now);
  gain.gain.exponentialRampToValueAtTime(0.001, now + duration);
  osc.start(now);
  osc.stop(now + duration);
}
function sfxCountBeep(){ playTone(520, 0.12, "square", 0.12); }
function sfxPon(){ playTone(700, 0.18, "square", 0.16); }
function sfxWin(){
  playTone(660, 0.14, "sine", 0.18);
  setTimeout(() => playTone(880, 0.22, "sine", 0.18), 130);
}
function sfxLose(){ playTone(220, 0.35, "sawtooth", 0.14); }
function sfxDraw(){ playTone(440, 0.25, "triangle", 0.14); }

/* ===================== カメラ & MediaPipe 初期化 ===================== */
let handLandmarker = null;
let initPromise = null;

async function initHandLandmarker(){
  if (handLandmarker) return handLandmarker;
  const vision = await FilesetResolver.forVisionTasks(
    "https://cdn.jsdelivr.net/npm/@mediapipe/tasks-vision@0.10.14/wasm"
  );
  handLandmarker = await HandLandmarker.createFromOptions(vision, {
    baseOptions: {
      modelAssetPath: "https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/1/hand_landmarker.task",
      delegate: "GPU"
    },
    runningMode: "VIDEO",
    numHands: 4,
    minHandDetectionConfidence: 0.5,
    minHandPresenceConfidence: 0.5,
    minTrackingConfidence: 0.5
  });
  return handLandmarker;
}

async function startCamera(){
  const video = el("video");
  const stream = await navigator.mediaDevices.getUserMedia({
    video: { facingMode: "user", width: { ideal: 1280 }, height: { ideal: 960 } },
    audio: false
  });
  video.srcObject = stream;
  state.stream = stream;
  state.video = video;
  await new Promise((resolve) => {
    video.onloadedmetadata = () => resolve();
  });
  await video.play();
}

function stopCamera(){
  if (state.stream) {
    state.stream.getTracks().forEach(t => t.stop());
    state.stream = null;
  }
}

/* ===================== はじめるボタン ===================== */
el("btn-start").addEventListener("click", async () => {
  const statusEl = el("start-status");
  statusEl.classList.remove("err");
  statusEl.textContent = "じゅんびちゅう…すこしまってね";
  el("btn-start").disabled = true;

  try {
    if (!initPromise) initPromise = initHandLandmarker();
    await Promise.all([startCamera(), initPromise]);
    statusEl.textContent = "";
    el("btn-start").disabled = false;
    setupOverlayCanvas();
    showScreen("battle");
    resetRoundUI();
    startDetectionLoop();
  } catch (err) {
    console.error(err);
    statusEl.classList.add("err");
    statusEl.textContent = "カメラが使えなかったよ。カメラの許可を確認してもう一度おしてね。";
    el("btn-start").disabled = false;
  }
});

/* ===================== キャンバス準備 ===================== */
function setupOverlayCanvas(){
  const video = el("video");
  const canvas = el("overlay");
  const wrap = video.parentElement;
  const resize = () => {
    const rect = wrap.getBoundingClientRect();
    canvas.width = rect.width;
    canvas.height = rect.height;
  };
  resize();
  window.addEventListener("resize", resize);
  state.overlayCtx = canvas.getContext("2d");
}

/* ===================== じゃんけん判定ロジック =====================
   MediaPipe HandLandmarker の21点座標から、各指が「伸びている」か
   「曲がっている」かをおおよそで判定し、ぐー/ちょき/ぱーに分類する。
   difficulty によって指ごとのしきい値を調整する。
====================================================================*/

// ランドマークのインデックス
const LM = {
  WRIST: 0,
  THUMB_CMC: 1, THUMB_MCP: 2, THUMB_IP: 3, THUMB_TIP: 4,
  INDEX_MCP: 5, INDEX_PIP: 6, INDEX_DIP: 7, INDEX_TIP: 8,
  MIDDLE_MCP: 9, MIDDLE_PIP: 10, MIDDLE_DIP: 11, MIDDLE_TIP: 12,
  RING_MCP: 13, RING_PIP: 14, RING_DIP: 15, RING_TIP: 16,
  PINKY_MCP: 17, PINKY_PIP: 18, PINKY_DIP: 19, PINKY_TIP: 20,
};

function dist(a, b){
  return Math.hypot(a.x - b.x, a.y - b.y, (a.z||0) - (b.z||0));
}

// 手のひらサイズ（正規化用のスケール基準）: 手首〜中指付け根
function palmScale(lm){
  return Math.max(dist(lm[LM.WRIST], lm[LM.MIDDLE_MCP]), 0.001);
}

// 難易度ごとの「指が伸びている」判定しきい値（tip-mcp距離 / palmScale の倍率）
// 値が大きいほど「伸びている」とみなされやすい＝緩い判定
const EXTEND_RATIO = {
  strict: 1.55,
  normal: 1.30,
  loose:  1.05,
};

function isFingerExtended(lm, mcpIdx, pipIdx, tipIdx, scale, ratio){
  // 指先が付け根から見てどれだけ遠いか
  const tipToMcp = dist(lm[tipIdx], lm[mcpIdx]);
  const pipToMcp = dist(lm[pipIdx], lm[mcpIdx]);
  // 伸びていれば tip は pip よりずっと mcp から遠くなる
  return (tipToMcp / scale) > ratio && tipToMcp > pipToMcp * 1.05;
}

function isThumbExtended(lm, scale, ratio){
  const tipToMcp = dist(lm[LM.THUMB_TIP], lm[LM.THUMB_MCP]);
  return (tipToMcp / scale) > ratio * 0.85;
}

// 21点ランドマークから ぐー/ちょき/ぱー/不明 を判定
function classifyGesture(lm, difficulty){
  const scale = palmScale(lm);
  const ratio = EXTEND_RATIO[difficulty] ?? EXTEND_RATIO.normal;

  const thumb  = isThumbExtended(lm, scale, ratio);
  const index  = isFingerExtended(lm, LM.INDEX_MCP,  LM.INDEX_PIP,  LM.INDEX_TIP,  scale, ratio);
  const middle = isFingerExtended(lm, LM.MIDDLE_MCP, LM.MIDDLE_PIP, LM.MIDDLE_TIP, scale, ratio);
  const ring   = isFingerExtended(lm, LM.RING_MCP,   LM.RING_PIP,   LM.RING_TIP,   scale, ratio);
  const pinky  = isFingerExtended(lm, LM.PINKY_MCP,  LM.PINKY_PIP,  LM.PINKY_TIP,  scale, ratio);

  const extendedCount = [index, middle, ring, pinky].filter(Boolean).length;

  // ぱー: 4本(+親指)がほぼ全部伸びている
  if (extendedCount >= (difficulty === "loose" ? 3 : 4)) {
    return "pa";
  }
  // ちょき: 人差し指・中指が伸びていて、薬指・小指は曲がっている
  if (index && middle && !ring && !pinky) {
    return "choki";
  }
  // ゆるめ設定では人差し指・中指のどちらかだけでもちょき扱いにする
  if (difficulty === "loose" && (index || middle) && extendedCount <= 2 && !ring && !pinky) {
    return "choki";
  }
  // ぐー: 伸びている指がほぼ無い
  if (extendedCount === 0 && !thumb) {
    return "gu";
  }
  if (difficulty !== "strict" && extendedCount <= 0) {
    return "gu";
  }
  if (difficulty === "loose" && extendedCount <= 1 && !index && !middle) {
    return "gu";
  }
  return null; // 判定できない
}

// 手の大きさ（bounding box の面積）を返す。複数の手がある場合の選別に使う
function handBoxArea(lm){
  let minX = 1, maxX = 0, minY = 1, maxY = 0;
  for (const p of lm) {
    if (p.x < minX) minX = p.x;
    if (p.x > maxX) maxX = p.x;
    if (p.y < minY) minY = p.y;
    if (p.y > maxY) maxY = p.y;
  }
  return (maxX - minX) * (maxY - minY);
}

/* ===================== 検出ループ ===================== */
function startDetectionLoop(){
  const video = el("video");
  const camStatus = el("cam-status");
  let noHandFrames = 0;

  async function loop(){
    if (!screens.battle.classList.contains("active")) {
      state.animFrameId = requestAnimationFrame(loop);
      return;
    }
    if (video.readyState >= 2 && video.currentTime !== state.lastVideoTime) {
      state.lastVideoTime = video.currentTime;
      const nowMs = performance.now();
      let result;
      try {
        result = handLandmarker.detectForVideo(video, nowMs);
      } catch(e) {
        result = null;
      }

      const ctx = state.overlayCtx;
      if (ctx) ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);

      if (result && result.landmarks && result.landmarks.length > 0) {
        noHandFrames = 0;
        camStatus.classList.remove("show");

        // 画面内で一番大きく映っている手を選ぶ
        let bestIdx = 0, bestArea = -1;
        result.landmarks.forEach((lm, i) => {
          const area = handBoxArea(lm);
          if (area > bestArea) { bestArea = area; bestIdx = i; }
        });
        const lm = result.landmarks[bestIdx];

        if (state.guideOn && ctx) drawHandGuide(ctx, lm);

        const gesture = classifyGesture(lm, state.difficulty);
        if (gesture && gesture === state.currentGesture) {
          state.gestureStableFrames++;
        } else {
          state.currentGesture = gesture;
          state.gestureStableFrames = gesture ? 1 : 0;
        }
      } else {
        noHandFrames++;
        state.currentGesture = null;
        state.gestureStableFrames = 0;
        if (noHandFrames > 20 && state.roundActive) {
          camStatus.textContent = "手が見えないよ。カメラの前で手を出してね。";
          camStatus.classList.add("show");
        }
      }
    }
    state.animFrameId = requestAnimationFrame(loop);
  }
  state.animFrameId = requestAnimationFrame(loop);
}

function drawHandGuide(ctx, lm){
  const w = ctx.canvas.width, h = ctx.canvas.height;
  let minX = 1, maxX = 0, minY = 1, maxY = 0;
  for (const p of lm) {
    if (p.x < minX) minX = p.x;
    if (p.x > maxX) maxX = p.x;
    if (p.y < minY) minY = p.y;
    if (p.y > maxY) maxY = p.y;
  }
  const pad = 0.06;
  const x = (minX - pad) * w;
  const y = (minY - pad) * h;
  const bw = (maxX - minX + pad * 2) * w;
  const bh = (maxY - minY + pad * 2) * h;
  ctx.strokeStyle = "rgba(255,155,74,0.9)";
  ctx.lineWidth = 4;
  ctx.beginPath();
  const r = 18;
  ctx.roundRect ? ctx.roundRect(x, y, bw, bh, r) : ctx.rect(x, y, bw, bh);
  ctx.stroke();

  // 関節を点で表示（軽め）
  ctx.fillStyle = "rgba(255,155,74,0.9)";
  for (const p of lm) {
    ctx.beginPath();
    ctx.arc(p.x * w, p.y * h, 3, 0, Math.PI * 2);
    ctx.fill();
  }
}

/* ===================== ラウンド進行 ===================== */
function resetRoundUI(){
  el("cpu-hand").textContent = "✊";
  el("cpu-hand").classList.remove("shake");
  const banner = el("result-banner");
  banner.className = "result-banner";
  banner.innerHTML = "カメラの<ruby>前<rt>まえ</rt></ruby>で<ruby>手<rt>て</rt></ruby>を<ruby>出<rt>だ</rt></ruby>してね";
  el("btn-play").disabled = false;
  state.roundActive = false;
}

function updateScoreUI(){
  el("score-win").textContent = state.scores.win;
  el("score-lose").textContent = state.scores.lose;
  el("score-draw").textContent = state.scores.draw;
}

function judge(player, cpu){
  if (player === cpu) return "draw";
  const winMap = { gu: "choki", choki: "pa", pa: "gu" };
  return winMap[player] === cpu ? "win" : "lose";
}

el("btn-play").addEventListener("click", async () => {
  if (state.roundActive) return;
  state.roundActive = true;
  el("btn-play").disabled = true;
  el("cam-status").classList.remove("show");

  const banner = el("result-banner");
  banner.className = "result-banner";
  banner.textContent = "";

  const cdOverlay = el("countdown-overlay");
  const cdText = el("countdown-text");
  const steps = ["さいしょは", "ぐー", "じゃんけん", "ぽん！"];

  cdOverlay.classList.add("show");
  const cpuHandEl = el("cpu-hand");
  cpuHandEl.classList.add("shake");

  for (let i = 0; i < steps.length; i++) {
    cdText.textContent = steps[i];
    sfxCountBeep();
    await sleep(state.countdownSec * 1000);
  }
  cdOverlay.classList.remove("show");
  cpuHandEl.classList.remove("shake");

  // 「ぽん」の瞬間の生徒の手を判定
  let playerGesture = state.gestureStableFrames >= 2 ? state.currentGesture : null;

  // もう少しだけ待って再確認（ぶれ対策：判定できなければ最大0.6秒リトライ）
  let retryCount = 0;
  while (!playerGesture && retryCount < 6) {
    await sleep(100);
    if (state.gestureStableFrames >= 2 && state.currentGesture) {
      playerGesture = state.currentGesture;
    }
    retryCount++;
  }

  if (!playerGesture) {
    banner.className = "result-banner";
    banner.innerHTML = "手が見えなかったよ。もう<ruby>一度<rt>いちど</rt></ruby><ruby>手<rt>て</rt></ruby>を<ruby>出<rt>だ</rt></ruby>してね！";
    el("btn-play").disabled = false;
    state.roundActive = false;
    return;
  }

  const cpuGesture = ["gu", "choki", "pa"][Math.floor(Math.random() * 3)];
  cpuHandEl.textContent = HAND_EMOJI[cpuGesture];
  sfxPon();

  const result = judge(playerGesture, cpuGesture);
  state.scores[result]++;
  updateScoreUI();

  if (result === "win") {
    banner.className = "result-banner win";
    banner.innerHTML = `あなたの${HAND_NAME[playerGesture]} の<ruby>勝<rt>か</rt></ruby>ち！やったね！`;
    sfxWin();
  } else if (result === "lose") {
    banner.className = "result-banner lose";
    banner.innerHTML = `アプリの${HAND_NAME[cpuGesture]} の<ruby>勝<rt>か</rt></ruby>ち。また<ruby>挑戦<rt>ちょうせん</rt></ruby>してね！`;
    sfxLose();
  } else {
    banner.className = "result-banner draw";
    banner.innerHTML = `あいこ！もう<ruby>一回<rt>いっかい</rt></ruby>やってみよう`;
    sfxDraw();
  }

  el("btn-play").disabled = false;
  state.roundActive = false;
});

function sleep(ms){ return new Promise(r => setTimeout(r, ms)); }

/* ===================== 終わる／結果画面 ===================== */
el("btn-finish").addEventListener("click", () => {
  el("final-win").textContent = state.scores.win;
  el("final-lose").textContent = state.scores.lose;
  el("final-draw").textContent = state.scores.draw;

  const total = state.scores.win + state.scores.lose + state.scores.draw;
  let msg = "たくさん<ruby>遊<rt>あそ</rt></ruby>んでくれてありがとう！";
  if (total > 0 && state.scores.win >= state.scores.lose && state.scores.win > 0) {
    msg = "たくさん<ruby>勝<rt>か</rt></ruby>てたね！すごいよ！";
  } else if (total === 0) {
    msg = "また<ruby>今度<rt>こんど</rt></ruby><ruby>遊<rt>あそ</rt></ruby>ぼうね！";
  }
  el("result-msg").innerHTML = msg;

  showScreen("result");
});

el("btn-again").addEventListener("click", () => {
  state.scores = { win: 0, lose: 0, draw: 0 };
  updateScoreUI();
  resetRoundUI();
  showScreen("battle");
});

/* ===================== 画面離脱時のカメラ停止（安全対策） ===================== */
window.addEventListener("beforeunload", () => {
  stopCamera();
  if (state.animFrameId) cancelAnimationFrame(state.animFrameId);
});
</script>
</body>
</html>
