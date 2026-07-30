ACCETRA Terminal

<!doctype html>
<html lang="en" data-theme="p1">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="color-scheme" content="dark">
<meta name="description" content="ACCETRA terminal portfolio for Muhammad Ammad Kiyani, front-end developer.">
<title>ACCETRA</title>
<style>
/* ============================================================
   ACCETRA TERMINAL
   Zero dependencies. No fonts, no frameworks, no network calls.
   Everything below is themed through CSS custom properties.
   ============================================================ */

:root{
  --chrome-a:#3a7ce8;
  --chrome-b:#0a56d6;
  --chrome-c:#0733a0;
  --bezel:#b9bcc4;
  --bezel-dark:#5c6069;
  --font: ui-monospace, "SF Mono", SFMono-Regular, Menlo, Consolas, "DejaVu Sans Mono", monospace;
  --tick: 1;
}

/* Phosphor sets. Each is a real CRT tube type, not a random accent pick. */
:root[data-theme="p1"]{ --name:"P1 phosphor"; --screen:#050c08; --deep:#020604; --fg:#86f5a8; --hot:#dcffe8; --dim:#3d7754; --line:#12301f; --glow:0,255,140; }
:root[data-theme="p3"]{ --name:"P3 phosphor"; --screen:#0c0803; --deep:#060401; --fg:#ffb454; --hot:#ffe9c4; --dim:#8a5a1c; --line:#31200a; --glow:255,170,60; }
:root[data-theme="p4"]{ --name:"P4 phosphor"; --screen:#06090d; --deep:#020406; --fg:#b9d6ff; --hot:#eef5ff; --dim:#4f6b92; --line:#182636; --glow:150,190,255; }
:root[data-theme="p7"]{ --name:"P7 phosphor"; --screen:#0b060d; --deep:#050208; --fg:#e69bff; --hot:#f9e6ff; --dim:#7a4a8c; --line:#2a1633; --glow:220,130,255; }

*{ box-sizing:border-box; margin:0; padding:0; }

html,body{ height:100%; min-height:100%; }

body{
  font-family:var(--font);
  background:
    radial-gradient(120% 90% at 50% 0%, #1c2129 0%, #0b0d11 55%, #050608 100%);
  color:var(--fg);
  display:grid;
  place-items:center;
  padding:clamp(8px,2.5vw,32px);
  -webkit-font-smoothing:antialiased;
  overflow:hidden;
  min-height:100dvh;
}

/* ---------- window chrome ---------- */

.machine{
  width:min(920px,100%);
  max-height:100%;
  display:flex;
  flex-direction:column;
  border-radius:8px 8px 4px 4px;
  background:linear-gradient(180deg,var(--bezel) 0%,#93979f 100%);
  padding:3px;
  box-shadow:
    0 0 0 1px #2a2d33,
    0 30px 70px -20px rgba(0,0,0,.9),
    0 2px 0 rgba(255,255,255,.35) inset;
  position:relative;
  transition:transform .35s cubic-bezier(.2,.8,.2,1), opacity .35s ease;
}
.machine.maximized{ width:100%; max-width:none; height:100%; max-height:100%; border-radius:4px; }
.machine.minimized .screen{ display:none; }
.machine.dragging{ transition:none; user-select:none; }
.machine.off{ transform:scaleY(.002) scaleX(.4); opacity:.9; }
.machine.off .screen, .machine.off .titlebar, .machine.off .statusbar{ opacity:0; }

.titlebar{
  display:flex; align-items:center; justify-content:space-between;
  gap:12px;
  padding:5px 6px 5px 10px;
  border-radius:5px 5px 0 0;
  background:linear-gradient(180deg,var(--chrome-a) 0%,var(--chrome-b) 45%,var(--chrome-c) 100%);
  box-shadow:0 1px 0 rgba(255,255,255,.4) inset, 0 -1px 0 rgba(0,0,0,.35) inset;
  cursor:grab;
  touch-action:none;
}
.titlebar:active{ cursor:grabbing; }
.title{
  color:#fff; font-size:12px; font-weight:700; letter-spacing:.04em;
  text-shadow:0 1px 2px rgba(0,0,0,.55);
  white-space:nowrap; overflow:hidden; text-overflow:ellipsis;
}
.title span{ opacity:.72; font-weight:400; }

.controls{ display:flex; gap:3px; flex:none; }
.ctrl{
  width:22px; height:20px; border:1px solid rgba(255,255,255,.85); border-radius:3px;
  font:700 11px/16px var(--font); color:#fff; cursor:pointer;
  background:linear-gradient(180deg,#5f92ea,#2f61c4);
  box-shadow:0 1px 0 rgba(255,255,255,.5) inset;
  display:grid; place-items:center;
}
.ctrl:hover{ filter:brightness(1.18); }
.ctrl:active{ transform:translateY(1px); }
.ctrl.close{ background:linear-gradient(180deg,#e57b6f,#c0392b); }
.ctrl:focus-visible{ outline:2px solid #fff; outline-offset:2px; }

/* ---------- the tube ---------- */

.screen{
  position:relative;
  flex:1;
  min-height:0;
  overflow:hidden;
  background:
    radial-gradient(130% 110% at 50% 40%, var(--screen) 0%, var(--deep) 100%);
  border:1px solid #26292f;
  border-top:none;
  transition:opacity .2s ease;
}

/* scanlines + vignette + bloom, all pure CSS, all pointer-transparent */
.screen::after{
  content:""; position:absolute; inset:0; z-index:5; pointer-events:none;
  background:
    repeating-linear-gradient(0deg, rgba(0,0,0,.26) 0 1px, transparent 1px 3px),
    radial-gradient(120% 100% at 50% 50%, transparent 55%, rgba(0,0,0,.55) 100%);
  mix-blend-mode:multiply;
}
.screen::before{
  content:""; position:absolute; inset:-50% 0; z-index:6; pointer-events:none;
  background:linear-gradient(transparent 42%, rgba(var(--glow),.055) 50%, transparent 58%);
  animation:sweep 7s linear infinite;
}
@keyframes sweep{ from{ transform:translateY(0) } to{ transform:translateY(66%) } }

.viewport{
  position:relative; z-index:2;
  height:100%; overflow-y:auto; overscroll-behavior:contain;
  padding:clamp(14px,3vw,26px);
  font-size:clamp(12px,1.55vw,14px);
  line-height:1.65;
  text-shadow:0 0 6px rgba(var(--glow),.35);
  scrollbar-width:thin; scrollbar-color:var(--line) transparent;
}
.viewport::-webkit-scrollbar{ width:8px; }
.viewport::-webkit-scrollbar-thumb{ background:var(--line); border-radius:4px; }

/* ---------- masthead ---------- */

.banner{
  color:var(--hot);
  font-size:clamp(3.2px,1.42vw,10px);
  line-height:1.02;
  letter-spacing:0;
  text-shadow:0 0 14px rgba(var(--glow),.55);
  margin-bottom:10px;
  white-space:pre;
}
.strap{
  display:flex; flex-wrap:wrap; gap:6px 18px;
  color:var(--dim); font-size:.82em; letter-spacing:.14em; text-transform:uppercase;
  border-bottom:1px solid var(--line); padding-bottom:12px; margin-bottom:14px;
}

/* ---------- mascot ---------- */

.greeting{ display:flex; gap:14px; align-items:flex-start; margin:0 0 16px; }
.mascot{ color:var(--hot); white-space:pre; line-height:1.1; flex:none; font-size:.95em; }
.bubble{
  border:1px solid var(--line); border-left:2px solid var(--fg);
  padding:8px 12px; background:rgba(var(--glow),.035); max-width:52ch;
}
.bubble b{ color:var(--hot); font-weight:700; }

/* ---------- log output ---------- */

.log > *{ white-space:pre-wrap; word-break:break-word; }
.log .cmd{ color:var(--hot); }
.log .cmd::before{ content:"C:\\> "; color:var(--dim); }
.log .dim{ color:var(--dim); }
.log .hot{ color:var(--hot); }
.log .err{ color:#ff6b5f; text-shadow:0 0 6px rgba(255,80,70,.4); }
.log .warn{ color:#ffcf6b; }
.log .rule{ color:var(--line); }
.log .pad{ height:.7em; }
.log a{ color:var(--hot); text-decoration:none; border-bottom:1px dotted var(--dim); }
.log a:hover, .log a:focus-visible{ background:var(--fg); color:var(--screen); border-color:transparent; outline:none; }

.bar{ color:var(--dim); }
.bar i{ color:var(--fg); font-style:normal; }

/* ---------- prompt ---------- */

.promptline{ display:flex; align-items:center; gap:8px; margin-top:6px; }
.promptline label{ color:var(--fg); font-weight:700; flex:none; }
#input{
  flex:1; min-width:0; background:transparent; border:0; outline:0;
  font:inherit; color:var(--hot); caret-color:transparent; padding:2px 0;
}
.caret{
  width:.62em; height:1.15em; background:var(--fg); flex:none;
  box-shadow:0 0 10px rgba(var(--glow),.7);
  animation:blink 1.05s steps(1) infinite;
}
@keyframes blink{ 50%{ opacity:0 } }
.caret.idle{ opacity:.35; animation:none; }

.chips{ display:flex; flex-wrap:wrap; gap:6px; margin-top:14px; }
.chip{
  font:inherit; font-size:.82em; color:var(--fg); cursor:pointer;
  background:rgba(var(--glow),.06); border:1px solid var(--line);
  padding:3px 9px; border-radius:2px;
}
.chip:hover, .chip:focus-visible{ background:var(--fg); color:var(--screen); outline:none; }

/* ---------- status bar ---------- */

.statusbar{
  display:flex; gap:16px; align-items:center; flex-wrap:wrap;
  padding:4px 10px; font-size:11px; letter-spacing:.06em;
  background:linear-gradient(180deg,#dcdfe4,#b6bac2);
  color:#2f333a; border-radius:0 0 3px 3px;
  box-shadow:0 1px 0 rgba(255,255,255,.6) inset;
}
.statusbar b{ font-weight:700; }
.statusbar .spacer{ margin-left:auto; }

/* ---------- power off state ---------- */

.dead{
  position:absolute; inset:0; z-index:9; display:none;
  place-items:center; background:#000; color:#4a4f56;
  font-size:12px; letter-spacing:.2em; text-transform:uppercase; text-align:center;
  cursor:pointer;
}
body.powered-off .dead{ display:grid; }
body.powered-off .machine{
  transform:none; opacity:1;
  box-shadow:0 0 0 1px #2a2d33, 0 20px 60px -30px #000;
}
body.powered-off .machine .screen,
body.powered-off .machine .titlebar,
body.powered-off .machine .statusbar{ opacity:1; }
body.powered-off .viewport, body.powered-off #rain{ visibility:hidden; }

#rain{ position:absolute; inset:0; z-index:4; opacity:.55; pointer-events:none; display:none; }
body.raining #rain{ display:block; }

/* ---------- responsive + accessibility ---------- */

@media (max-width:640px){
  body{ padding:max(6px, env(safe-area-inset-top)) max(6px, env(safe-area-inset-right)) max(6px, env(safe-area-inset-bottom)) max(6px, env(safe-area-inset-left)); }
  .machine{ max-height:100dvh; }
  .greeting{ gap:10px; }
  .mascot{ font-size:.8em; }
  .strap{ gap:4px 12px; font-size:.72em; }
  .statusbar .hide-sm{ display:none; }
}
@media (prefers-reduced-motion:reduce){
  .screen::before{ animation:none; }
  .caret{ animation:none; }
  .machine{ transition:none; }
}
</style>
</head>
<body>

<main class="machine" id="machine">
  <div class="titlebar" id="titlebar">
    <div class="title">C:\ACCETRA\PORTFOLIO.SYS <span>[16 bit MS DOS subsystem]</span></div>
    <div class="controls">
      <button class="ctrl" id="btn-min" title="Collapse the window" aria-label="Collapse the window">_</button>
      <button class="ctrl" id="btn-max" title="Fill the screen" aria-label="Fill the screen">□</button>
      <button class="ctrl close" id="btn-off" title="Cut the power" aria-label="Cut the power">X</button>
    </div>
  </div>

  <div class="screen" id="screen">
    <canvas id="rain"></canvas>
    <div class="dead" id="dead" role="button" tabindex="0">Signal lost. Press any key to power on.</div>

    <div class="viewport" id="viewport">
      <pre class="banner" id="banner" aria-label="ACCETRA"></pre>

      <div class="strap">
        <span>Front end developer</span>
        <span>Islamabad, PK</span>
        <span>Open to work</span>
      </div>

      <div class="greeting">
        <pre class="mascot" id="mascot"></pre>
        <div class="bubble">
          <p id="bubble-text">Booting.</p>
        </div>
      </div>

      <div class="log" id="log" aria-live="polite" aria-atomic="false"></div>

      <div class="promptline">
        <label for="input">C:\&gt;</label>
        <input id="input" type="text" autocomplete="off" autocapitalize="off" autocorrect="off"
               spellcheck="false" aria-label="Terminal command input" disabled>
        <span class="caret" id="caret" aria-hidden="true"></span>
      </div>

      <div class="chips" id="chips" aria-label="Quick commands"></div>
    </div>
  </div>

  <div class="statusbar">
    <span><b id="st-theme">P1 phosphor</b></span>
    <span class="hide-sm">Commands run: <b id="st-count">0</b></span>
    <span class="hide-sm">Uptime <b id="st-clock">00:00</b></span>
    <span class="spacer">Tab completes. Up arrow recalls.</span>
  </div>
</main>

<script>
"use strict";

/* ============================================================
   1. CONTENT. Everything a human needs to edit lives right here.
   ============================================================ */

const ME = {
  name: "Muhammad Ammad Kiyani",
  handle: "accetra",
  role: "Front end developer",
  location: "Islamabad, Pakistan",
  email: "ammadkiyani92@gmail.com",
  site: "muhammadammadkiyani.com",
  github: "github.com/accetra",
  linkedin: "linkedin.com/in/muhammadammadkiyani",
  bio: [
    "I build interfaces that feel engineered rather than assembled. Most of my week is",
    "agency work at Constantine PR, where a brand deck on Monday turns into a shipped",
    "site by Friday. The rest goes into small machines like this one.",
    "",
    "Comfortable owning the whole path: layout, motion, state, build, deploy, and the",
    "copy that holds it together."
  ],
  skills: [
    { n:"TypeScript", v:88 }, { n:"React",  v:86 }, { n:"Astro",   v:84 },
    { n:"CSS",        v:92 }, { n:"Python", v:74 }, { n:"Java",    v:66 },
    { n:"Kotlin",     v:60 }, { n:"Flutter",v:58 }
  ],
  projects: [
    { n:"Repo Tour Guide", s:"React, Express, OpenAI", d:"Walks you through an unfamiliar codebase like a guided tour.", u:"" },
    { n:"Turtle Dash",     s:"Canvas, vanilla JS",     d:"Endless runner in the browser. No engine, no assets, no build step.", u:"" },
    { n:"PakLM",           s:"PyTorch",                d:"A small language model trained from scratch on Urdu, Roman Urdu and English.", u:"" },
    { n:"Portfolio",       s:"Astro, React, GSAP",     d:"Dark editorial site with cinematic motion.", u:"" }
  ],
  experience: [
    { y:"2024 to now", r:"Front end developer", c:"Constantine PR and Communications" },
    { y:"2023 to 2024", r:"Web developer",      c:"Syslab Technologies" }
  ]
};

const BANNER = [
" █████   ██████  ██████ ███████ ████████ ██████   █████  ",
"██   ██ ██      ██      ██         ██    ██   ██ ██   ██ ",
"███████ ██      ██      █████      ██    ██████  ███████ ",
"██   ██ ██      ██      ██         ██    ██   ██ ██   ██ ",
"██   ██  ██████  ██████ ███████    ██    ██   ██ ██   ██ "
].join("\n");

const FACE = (eyes, mouth) => [
  "  ┌────────┐",
  "  │ " + eyes + " │",
  "  │  " + mouth + "  │",
  "  └───┬┬───┘",
  "      ╰╯"
].join("\n");

const MOODS = {
  idle:  FACE("o    o", "────"),
  blink: FACE("-    -", "────"),
  happy: FACE("^    ^", "◡◡◡◡"),
  think: FACE("o    -", "─  ─"),
  cross: FACE("x    x", "▔▔▔▔")
};

/* ============================================================
   2. WIRING
   ============================================================ */

const $ = id => document.getElementById(id);
const el = { log:$("log"), input:$("input"), caret:$("caret"), view:$("viewport"),
             mascot:$("mascot"), bubble:$("bubble-text"), chips:$("chips"),
             machine:$("machine"), dead:$("dead"), banner:$("banner"),
             stTheme:$("st-theme"), stCount:$("st-count"), stClock:$("st-clock") };

const REDUCED = typeof matchMedia === "function"
  && matchMedia("(prefers-reduced-motion: reduce)").matches;
const state = { history:[], hIndex:-1, count:0, started:Date.now(), typing:false, skip:false, tab:null };

let moodTimer = null;
let powerTimer = null;
let rainTimer = null;
let raining = false, rainId = null, columns = [];

el.banner.textContent = BANNER;
setMood("idle");

/* ---- output helpers ---- */

function line(text, cls){
  const p = document.createElement("p");
  if (cls) p.className = cls;
  p.textContent = text;
  el.log.appendChild(p);
  return p;
}
function html(markup, cls){
  const p = document.createElement("p");
  if (cls) p.className = cls;
  p.innerHTML = markup;
  el.log.appendChild(p);
  return p;
}
function gap(){ const d = document.createElement("div"); d.className = "pad"; el.log.appendChild(d); }
function scroll(){ el.view.scrollTop = el.view.scrollHeight; }

function print(rows){
  for (const row of rows){
    if (row === null) { gap(); continue; }
    if (typeof row === "string") { line(row); continue; }
    if (row.html) html(row.html, row.cls); else line(row.text, row.cls);
  }
  scroll();
}

/* Typewriter output for the boot sequence. Any keypress dumps the rest. */
function type(rows, speed){
  speed = REDUCED ? 0 : (speed || 9);
  state.typing = true; state.skip = REDUCED;
  return new Promise(done => {
    let i = 0;
    const next = () => {
      if (i >= rows.length){ state.typing = false; state.skip = false; done(); return; }
      const row = rows[i++];
      if (row === null){ gap(); scroll(); return schedule(60); }
      const obj = typeof row === "string" ? { text:row } : row;
      const p = line("", obj.cls);
      const txt = obj.text || "";
      if (state.skip || speed === 0){ p.textContent = txt; scroll(); return schedule(0); }
      let c = 0;
      const tick = setInterval(() => {
        if (state.skip){ p.textContent = txt; clearInterval(tick); scroll(); return schedule(0); }
        p.textContent = txt.slice(0, ++c);
        if (c >= txt.length){ clearInterval(tick); scroll(); schedule(obj.pause || 90); }
      }, speed);
    };
    const schedule = ms => setTimeout(next, state.skip ? 0 : ms);
    next();
  });
}

/* ---- mascot ---- */

function setMood(mood, revertAfter){
  el.mascot.textContent = MOODS[mood] || MOODS.idle;
  clearTimeout(moodTimer);
  if (revertAfter) moodTimer = setTimeout(() => setMood("idle"), revertAfter);
}
function say(text){ el.bubble.innerHTML = text; }

setInterval(() => {
  if (document.hidden || el.mascot.textContent !== MOODS.idle) return;
  setMood("blink");
  setTimeout(() => { if (el.mascot.textContent === MOODS.blink) setMood("idle"); }, 140);
}, 4200);

/* ============================================================
   3. COMMANDS
   ============================================================ */

const bar = v => {
  const filled = Math.round(v / 5);
  return "█".repeat(filled) + "░".repeat(20 - filled);
};
const pad = (s, n) => (s + " ".repeat(n)).slice(0, n);
const safe = s => s.replace(/[<>&"]/g, c => ({ "<":"&lt;", ">":"&gt;", "&":"&amp;", '"':"&quot;" }[c]));
const link = (label, href) => `<a href="${href}" target="_blank" rel="noopener noreferrer">${safe(label)}</a>`;

const CMD = {

  help: { desc:"list every command", run(){
    const names = Object.keys(CMD).sort();
    print([
      { text:"Available commands", cls:"hot" },
      null,
      ...names.map(n => ({ html:`  <span class="hot">${pad(n,12)}</span><span class="dim">${safe(CMD[n].desc)}</span>` })),
      null,
      { text:"Tab completes a command. Up and down arrows walk your history. Ctrl+L clears.", cls:"dim" }
    ]);
    say("Everything on that list actually does something. <b>neofetch</b> is the good one.");
  }},

  about: { desc:"who is behind this", run(){
    print([{ text:ME.name, cls:"hot" }, { text:ME.role + ", " + ME.location, cls:"dim" }, null,
           ...ME.bio, null]);
    setMood("happy", 2600);
    say("That is the short version. <b>experience</b> has the receipts.");
  }},

  skills: { desc:"stack, with honest numbers", run(){
    print([{ text:"Working proficiency, self assessed and deliberately not all 99", cls:"dim" }, null,
      ...ME.skills.map(s => ({ html:`  ${pad(s.n,12)}<span class="bar"><i>${bar(s.v).slice(0, Math.round(s.v/5))}</i>${bar(s.v).slice(Math.round(s.v/5))}</span>  <span class="dim">${s.v}</span>` })),
      null]);
    say("Numbers are a self assessment, not a certification.");
  }},

  projects: { desc:"things I have built", run(){
    const rows = [];
    ME.projects.forEach((p, i) => {
      const num = String(i + 1).padStart(2, "0");
      rows.push({ html:`  <span class="dim">${num}</span>  <span class="hot">${safe(p.n)}</span>  <span class="dim">${safe(p.s)}</span>` });
      rows.push({ html:`      <span>${safe(p.d)}</span>` });
      rows.push(null);
    });
    print([{ text:"Selected work", cls:"hot" }, null, ...rows,
           { text:"Run  open 1  to launch a project in a new tab.", cls:"dim" }]);
    say("Four picks. Ask me about any of them.");
  }},

  open: { desc:"open project by number", run(args){
    const p = ME.projects[parseInt(args[0], 10) - 1];
    if (!p) return print([{ text:"No project with that number. Run projects first.", cls:"err" }]);
    if (!p.u) return print([{ text:"That project has no public link yet.", cls:"warn" }]);
    window.open(p.u, "_blank", "noopener");
    print([{ text:"Opening " + p.n, cls:"dim" }]);
  }},

  experience: { desc:"where I have worked", run(){
    print([{ text:"Work history", cls:"hot" }, null,
      ...ME.experience.flatMap(e => ([
        { html:`  <span class="hot">${safe(e.r)}</span>  <span class="dim">${safe(e.c)}</span>` },
        { html:`  <span class="dim">${safe(e.y)}</span>` },
        null
      ]))]);
  }},

  contact: { desc:"how to reach me", run(){
    print([{ text:"Reply time is usually under a day.", cls:"dim" }, null,
      { html:`  ${pad("email",10)}${link(ME.email, "mailto:" + ME.email)}` },
      { html:`  ${pad("site",10)}${link(ME.site, "https://" + ME.site)}` },
      { html:`  ${pad("github",10)}${link(ME.github, "https://" + ME.github)}` },
      { html:`  ${pad("linkedin",10)}${link(ME.linkedin, "https://" + ME.linkedin)}` },
      null]);
    setMood("happy", 3000);
    say("Email is fastest. I read all of them.");
  }},

  neofetch: { desc:"system summary", run(){
    const art = MOODS.happy.split("\n");
    const info = [
      { k:"user",     v:ME.handle + "@accetra" },
      { k:"os",       v:"ACCETRA DOS 5.1" },
      { k:"shell",    v:"portfolio.sys" },
      { k:"role",     v:ME.role },
      { k:"location", v:ME.location },
      { k:"uptime",   v:uptime() },
      { k:"tube",     v:getComputedStyle(document.documentElement).getPropertyValue("--name").replace(/"/g,"").trim() },
      { k:"deps",     v:"0" }
    ];
    const rows = [];
    const n = Math.max(art.length, info.length);
    for (let i = 0; i < n; i++){
      const a = pad(art[i] || "", 16);
      const b = info[i] ? `<span class="hot">${pad(info[i].k, 10)}</span><span>${safe(info[i].v)}</span>` : "";
      rows.push({ html:`<span class="dim">${safe(a)}</span>${b}` });
    }
    print([null, ...rows, null]);
    say("Zero dependencies. That number is the one I am proud of.");
  }},

  theme: { desc:"switch phosphor tube", run(args){
    const tubes = ["p1","p3","p4","p7"];
    const root = document.documentElement;
    let next = (args[0] || "").toLowerCase();
    if (!tubes.includes(next)) next = tubes[(tubes.indexOf(root.dataset.theme) + 1) % tubes.length];
    root.dataset.theme = next;
    const label = getComputedStyle(root).getPropertyValue("--name").replace(/"/g,"").trim();
    el.stTheme.textContent = label;
    print([{ text:"Tube swapped to " + label + ". Options are p1, p3, p4, p7.", cls:"dim" }]);
  }},

  ls: { desc:"list the filesystem", run(){
    print([
      { html:`  <span class="hot">${pad("about.txt",16)}</span><span class="dim">2 KB</span>` },
      { html:`  <span class="hot">${pad("skills.dat",16)}</span><span class="dim">1 KB</span>` },
      { html:`  <span class="hot">${pad("projects/",16)}</span><span class="dim">${ME.projects.length} items</span>` },
      { html:`  <span class="hot">${pad("contact.cfg",16)}</span><span class="dim">512 B</span>` },
      { html:`  <span class="hot">${pad("secrets/",16)}</span><span class="dim">access denied</span>` }
    ]);
  }},

  cat: { desc:"read a file", run(args){
    const f = (args[0] || "").toLowerCase();
    if (f.startsWith("about")) return CMD.about.run();
    if (f.startsWith("skills")) return CMD.skills.run();
    if (f.startsWith("contact")) return CMD.contact.run();
    if (f.startsWith("secrets")){ setMood("cross", 2000); return print([{ text:"Access denied. Nice try.", cls:"err" }]); }
    print([{ text:"No such file. Run ls to see what is here.", cls:"err" }]);
  }},

  whoami: { desc:"identify the visitor", run(){
    print([{ text:"guest", cls:"hot" },
           { text:"Signed in as a stranger with good taste. Run contact to fix that.", cls:"dim" }]);
  }},

  history: { desc:"show recent commands", run(){
    if (!state.history.length) return print([{ text:"Nothing yet.", cls:"dim" }]);
    print(state.history.map((h, i) => ({ html:`  <span class="dim">${pad(String(i+1),4)}</span>${safe(h)}` })));
  }},

  echo: { desc:"repeat after me", run(args){ print([args.join(" ") || ""]); } },

  date: { desc:"current time", run(){ print([{ text:new Date().toString(), cls:"dim" }]); } },

  matrix: { desc:"let it rain", run(){
    if (!startRain()) return print([{ text:REDUCED ? "Matrix rain is disabled by reduced-motion preferences." : "Matrix rain is unavailable in this browser.", cls:"warn" }]);
    print([{ text:"Press any key to stop.", cls:"dim" }]);
    say("Purely decorative. No one is being hacked.");
  }},

  sudo: { desc:"try your luck", run(){
    setMood("cross", 2500);
    print([{ text:"guest is not in the sudoers file. This incident has been reported to nobody.", cls:"err" }]);
  }},

  clear: { desc:"wipe the screen", run(){ el.log.innerHTML = ""; } },

  exit: { desc:"cut the power", run(){ powerOff(); } }
};

/* ============================================================
   4. INTERPRETER
   ============================================================ */

function distance(a, b){
  const m = [];
  for (let i = 0; i <= b.length; i++) m[i] = [i];
  for (let j = 0; j <= a.length; j++) m[0][j] = j;
  for (let i = 1; i <= b.length; i++)
    for (let j = 1; j <= a.length; j++)
      m[i][j] = b[i-1] === a[j-1] ? m[i-1][j-1]
        : Math.min(m[i-1][j-1] + 1, m[i][j-1] + 1, m[i-1][j] + 1);
  return m[b.length][a.length];
}

function run(raw){
  const input = raw.trim();
  line(input, "cmd");
  if (!input){ scroll(); return; }

  state.history.push(input);
  state.hIndex = state.history.length;
  el.stCount.textContent = ++state.count;

  const [name, ...args] = input.split(/\s+/);
  const key = name.toLowerCase();

  if (CMD[key]){ CMD[key].run(args); scroll(); return; }

  const near = Object.keys(CMD)
    .map(c => ({ c, d:distance(key, c) }))
    .sort((a, b) => a.d - b.d)[0];

  setMood("think", 2200);
  print([
    { text:`'${input}' is not recognized as a command.`, cls:"err" },
    near && near.d <= 3 ? { text:`Did you mean ${near.c}?`, cls:"dim" } : { text:"Type help for the list.", cls:"dim" }
  ]);
}

/* ---- input handling ---- */

el.input.addEventListener("keydown", e => {
  if (e.key === "Enter"){
    e.preventDefault();
    const v = el.input.value;
    el.input.value = "";
    state.tab = null;
    run(v);
    return;
  }
  if (e.key === "Tab"){
    e.preventDefault();
    const partial = el.input.value.trim().toLowerCase();
    if (!partial) return;
    if (state.tab === null || !state.tab.base || !partial.startsWith(state.tab.base))
      state.tab = { base:partial, i:-1, hits:Object.keys(CMD).filter(c => c.startsWith(partial)) };
    if (!state.tab.hits.length) return;
    state.tab.i = (state.tab.i + 1) % state.tab.hits.length;
    el.input.value = state.tab.hits[state.tab.i];
    return;
  }
  state.tab = null;
  if (e.key === "ArrowUp"){
    e.preventDefault();
    if (!state.history.length) return;
    state.hIndex = Math.max(0, state.hIndex - 1);
    el.input.value = state.history[state.hIndex];
    return;
  }
  if (e.key === "ArrowDown"){
    e.preventDefault();
    state.hIndex = Math.min(state.history.length, state.hIndex + 1);
    el.input.value = state.history[state.hIndex] || "";
    return;
  }
  if (e.key === "l" && e.ctrlKey){ e.preventDefault(); el.log.innerHTML = ""; return; }
  if (e.key === "c" && e.ctrlKey){ e.preventDefault(); line(el.input.value + "^C", "cmd"); el.input.value = ""; scroll(); }
});

el.input.addEventListener("focus", () => el.caret.classList.remove("idle"));
el.input.addEventListener("blur", () => el.caret.classList.add("idle"));

/* Click anywhere on the tube to focus the prompt, unless selecting text. */
el.view.addEventListener("mouseup", e => {
  if (e.target.tagName === "A" || e.target.classList.contains("chip")) return;
  if (String(getSelection())) return;
  el.input.focus();
});

/* Any key skips the boot typing. */
addEventListener("keydown", () => { if (state.typing) state.skip = true; if (raining) stopRain(); }, true);

/* Quick command chips */
["about","skills","projects","experience","contact","neofetch","theme","clear"].forEach(c => {
  const b = document.createElement("button");
  b.className = "chip"; b.type = "button"; b.textContent = c;
  b.addEventListener("click", () => { run(c); el.input.focus(); });
  el.chips.appendChild(b);
});

/* ============================================================
   5. WINDOW CHROME
   ============================================================ */

$("btn-max").addEventListener("click", () => {
  el.machine.classList.toggle("maximized");
  el.machine.style.transform = "";
  drag.x = 0; drag.y = 0;
});

$("btn-min").addEventListener("click", () => {
  const minimized = el.machine.classList.toggle("minimized");
  $("btn-min").setAttribute("aria-pressed", String(minimized));
  if (!minimized) el.input.focus();
});

$("btn-off").addEventListener("click", powerOff);

function powerOff(){
  clearTimeout(powerTimer);
  stopRain();
  el.machine.style.transform = "";
  drag.x = 0; drag.y = 0;
  el.machine.classList.remove("minimized");
  el.machine.classList.add("off");
  el.input.disabled = true;
  powerTimer = setTimeout(() => {
    document.body.classList.add("powered-off");
    el.machine.classList.remove("off");
  }, REDUCED ? 0 : 350);
  setMood("cross");
}
function powerOn(){
  clearTimeout(powerTimer);
  document.body.classList.remove("powered-off");
  el.machine.classList.remove("off");
  el.input.disabled = state.typing;
  setMood("idle");
  say("Back online. Nothing was lost.");
  setTimeout(() => el.input.focus(), 400);
}
el.dead.addEventListener("click", powerOn);
addEventListener("keydown", e => { if (document.body.classList.contains("powered-off")){ e.preventDefault(); powerOn(); } });

/* Drag the window by its title bar, pointer events so it works with a stylus too. */
const drag = { active:false, x:0, y:0, sx:0, sy:0 };
const titlebar = $("titlebar");

titlebar.addEventListener("pointerdown", e => {
  if (e.target.closest(".ctrl") || el.machine.classList.contains("maximized")) return;
  if (innerWidth < 720) return;
  drag.active = true; drag.sx = e.clientX - drag.x; drag.sy = e.clientY - drag.y;
  el.machine.classList.add("dragging");
  titlebar.setPointerCapture(e.pointerId);
});
function clampDrag(x, y){
  const r = el.machine.getBoundingClientRect();
  const baseLeft = r.left - drag.x, baseTop = r.top - drag.y;
  const margin = 24;
  return {
    x:Math.min(innerWidth - margin - baseLeft, Math.max(margin - r.width - baseLeft, x)),
    y:Math.min(innerHeight - margin - baseTop, Math.max(margin - r.height - baseTop, y))
  };
}
titlebar.addEventListener("pointermove", e => {
  if (!drag.active) return;
  const next = clampDrag(e.clientX - drag.sx, e.clientY - drag.sy);
  drag.x = next.x; drag.y = next.y;
  el.machine.style.transform = `translate(${drag.x}px, ${drag.y}px)`;
});
const endDrag = () => { drag.active = false; el.machine.classList.remove("dragging"); };
titlebar.addEventListener("pointerup", endDrag);
titlebar.addEventListener("pointercancel", endDrag);

/* ============================================================
   6. MATRIX RAIN
   ============================================================ */

const canvas = $("rain");
const ctx = canvas && canvas.getContext ? (() => { try { return canvas.getContext("2d"); } catch (e) { return null; } })() : null;

function startRain(){
  if (raining || REDUCED || !ctx) return false;
  clearTimeout(rainTimer);
  raining = true;
  document.body.classList.add("raining");
  sizeRain();
  const step = () => {
    const dpr = Math.min(devicePixelRatio || 1, 2);
    const w = canvas.width / dpr, h = canvas.height / dpr;
    ctx.fillStyle = "rgba(0,0,0,0.08)";
    ctx.fillRect(0, 0, w, h);
    ctx.fillStyle = getComputedStyle(document.documentElement).getPropertyValue("--fg").trim();
    ctx.font = "14px monospace";
    columns.forEach((y, i) => {
      const ch = String.fromCharCode(0x30a0 + Math.random() * 96);
      ctx.fillText(ch, i * 14, y * 14);
      columns[i] = y * 14 > h && Math.random() > 0.975 ? 0 : y + 1;
    });
    rainId = requestAnimationFrame(step);
  };
  step();
  rainTimer = setTimeout(stopRain, 9000);
  return true;
}
function stopRain(){
  if (!raining) return;
  raining = false;
  clearTimeout(rainTimer);
  rainTimer = null;
  cancelAnimationFrame(rainId);
  document.body.classList.remove("raining");
  ctx.clearRect(0, 0, canvas.width, canvas.height);
}
function sizeRain(){
  const r = canvas.getBoundingClientRect();
  const dpr = Math.min(devicePixelRatio || 1, 2);
  canvas.width = Math.max(1, Math.round(r.width * dpr));
  canvas.height = Math.max(1, Math.round(r.height * dpr));
  canvas.style.width = r.width + "px";
  canvas.style.height = r.height + "px";
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  columns = Array(Math.ceil(r.width / 14)).fill(1).map(() => Math.random() * (r.height / 14));
}
addEventListener("resize", () => {
  if (raining) sizeRain();
  if (!drag.active && (drag.x || drag.y)){
    const next = clampDrag(drag.x, drag.y);
    drag.x = next.x; drag.y = next.y;
    el.machine.style.transform = `translate(${drag.x}px, ${drag.y}px)`;
  }
});
canvas.addEventListener("click", stopRain);

/* ============================================================
   7. STATUS BAR + BOOT
   ============================================================ */

function uptime(){
  const s = Math.floor((Date.now() - state.started) / 1000);
  return String(Math.floor(s / 60)).padStart(2,"0") + ":" + String(s % 60).padStart(2,"0");
}
setInterval(() => { el.stClock.textContent = uptime(); }, 1000);

async function boot(){
  say("Booting.");
  await type([
    { text:"ACCETRA BIOS v5.1  (c) 1998 to " + new Date().getFullYear(), cls:"dim" },
    { text:"Memory test: 65536K OK", cls:"dim" },
    { text:"Detecting display adapter: CRT, phosphor P1", cls:"dim" },
    { text:"Loading portfolio.sys", cls:"dim", pause:220 },
    null,
    { text:"Ready.", cls:"hot" },
    null
  ], 7);

  print([
    { html:`Welcome. This is the personal terminal of <span class="hot">${safe(ME.name)}</span>.` },
    { text:"Type a command below, or press one of the buttons. Start with help.", cls:"dim" },
    null
  ]);

  const poweredOff = document.body.classList.contains("powered-off") || el.machine.classList.contains("off");
  el.input.disabled = poweredOff;
  if (!poweredOff) el.input.focus();
  setMood("happy", 2400);
  say("Hi. I am <b>Pinny</b>, the resident daemon. Try <b>neofetch</b> if you like showing off.");
}

boot();
</script>
</body>
</html>
