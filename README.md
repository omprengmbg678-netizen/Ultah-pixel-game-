<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Happy Birthday Rio!</title>

<style>
*{box-sizing:border-box}
body{
  margin:0;color:white;text-align:center;
  font-family:monospace;background:#030712;
  overflow-x:hidden;
}
#stars{
  position:fixed;inset:0;width:100%;height:100%;
  z-index:-1;pointer-events:none;
}
section{
  width:92%;max-width:450px;margin:24px auto;
  padding:20px 12px;background:#101b48ee;
  border:1px solid #60a5fa;border-radius:18px;
  box-shadow:0 0 22px #2563eb66;
}
h1{color:#67e8f9;text-shadow:0 0 15px #38bdf8}
h2{color:#93c5fd}
p{line-height:1.6}
button{
  padding:11px 14px;margin:5px;
  border:0;border-radius:10px;
  color:white;background:#2563eb;
  font-family:inherit;font-weight:bold;
  cursor:pointer;touch-action:manipulation;
}
button:active{transform:scale(.92)}
input,textarea{
  width:95%;padding:12px;margin:7px 0;
  border-radius:8px;border:1px solid #60a5fa;
  background:#0f172a;color:white;
}
#celebration{
  position:relative;z-index:101;
  margin-top:14px;color:#fde68a;
  font-size:22px;font-weight:bold;
}
#partyCanvas{
  position:fixed;inset:0;width:100%;height:100%;
  pointer-events:none;z-index:100;
}
@keyframes partyPop{
  0%{transform:scale(0) rotate(-12deg);opacity:0}
  70%{transform:scale(1.15);opacity:1}
  100%{transform:scale(1);opacity:1}
}
.party-title{animation:partyPop .6s ease-out}
@keyframes glow{
  from{text-shadow:0 0 4px #38bdf8}
  to{text-shadow:0 0 18px #facc15}
}
.glow{animation:glow 1s infinite alternate}
.info{
  display:flex;justify-content:space-around;
  gap:5px;color:#a5f3fc;margin:12px 0;
}
#gameCanvas{
  width:100%;max-width:320px;
  border:2px solid #38bdf8;border-radius:10px;
  background:#020617;
}
#gameMessage{color:#fde68a}
</style>
</head>

<body>
<canvas id="stars"></canvas>
<canvas id="partyCanvas"></canvas>

<section>
  <h1 class="glow">🎂 HAPPY BIRTHDAY! 🎂</h1>
  <h2>Selamat Ulang Tahun, Rio!</h2>
  <p>🎮 LEVEL UP! Umur baru, petualangan baru!</p>
  <p>Semoga panjang umur, sehat selalu, makin bahagia,
  semua cita-cita tercapai, dan sukses terus!</p>
  <p>✨ Chapter baru dimulai sekarang! ✨</p>

  <button id="musicBtn">🎵 Putar Musik</button>
  <button onclick="celebrate()">🎆 RAYAKAN!</button>
  <div id="celebration"></div>
</section>

<section>
  <h2>🚀 SPACE SHOOTER</h2>
  <p>Hancurkan meteor sebanyak mungkin!</p>
  <div class="info">
    <span>Skor: <b id="score">0</b></span>
    <span>Nyawa: <b id="lives">3</b></span>
    <span>Waktu: <b id="time">30</b>s</span>
  </div>

  <canvas id="gameCanvas" width="320" height="420"></canvas>

  <div>
    <button id="left">⬅️ KIRI</button>
    <button id="right">KANAN ➡️</button>
  </div>
  <button id="start">▶️ MULAI GAME</button>
  <p id="gameMessage">Pesawat siap tempur! 🚀</p>
</section>

<section>
  <h2>💌 KIRIM UCAPAN</h2>
  <input id="sender" placeholder="Nama kamu">
  <textarea id="wish" rows="4"
  placeholder="Tulis ucapan ulang tahun..."></textarea>
  <button onclick="sendWish()">📱 KIRIM VIA WHATSAPP</button>
</section>

<section>
  <h2>🌟 BAGIKAN UNDANGAN</h2>
  <p>Ajak teman-teman ikut merayakan!</p>
  <button onclick="shareInvite()">🔗 BAGIKAN</button>
</section>

<script>
// ===== ANIMASI GALAKSI =====
const starsCanvas=document.getElementById("stars");
const sx=starsCanvas.getContext("2d");
let stars=[];

function resizeStars(){
  starsCanvas.width=innerWidth;
  starsCanvas.height=innerHeight;
  stars=Array.from({length:100},()=>({
    x:Math.random()*innerWidth,
    y:Math.random()*innerHeight,
    r:Math.random()*1.7+.3,
    speed:.3+Math.random()*1.1
  }));
}
function animateStars(){
  sx.clearRect(0,0,starsCanvas.width,starsCanvas.height);
  stars.forEach(s=>{
    s.y+=s.speed;
    if(s.y>innerHeight)s.y=0;
    sx.fillStyle="#fff";
    sx.globalAlpha=.4+Math.random()*.6;
    sx.beginPath();
    sx.arc(s.x,s.y,s.r,0,Math.PI*2);
    sx.fill();
  });
  sx.globalAlpha=1;
  requestAnimationFrame(animateStars);
}
addEventListener("resize",resizeStars);
resizeStars();
animateStars();

// ===== MUSIK ELEKTRONIK =====
let audioCtx=null,musicOn=false,musicTimer=null,note=0;
const notes=[261.63,329.63,392,523.25,392,329.63,293.66,349.23];

function playNote(){
  if(!musicOn||!audioCtx)return;
  const osc=audioCtx.createOscillator();
  const gain=audioCtx.createGain();
  osc.type="square";
  osc.frequency.value=notes[note++%notes.length];
  gain.gain.setValueAtTime(.035,audioCtx.currentTime);
  gain.gain.exponentialRampToValueAtTime(
    .001,audioCtx.currentTime+.22
  );
  osc.connect(gain);
  gain.connect(audioCtx.destination);
  osc.start();
  osc.stop(audioCtx.currentTime+.23);
}

document.getElementById("musicBtn").onclick=async()=>{
  if(!audioCtx){
    audioCtx=new(window.AudioContext||window.webkitAudioContext)();
  }
  if(audioCtx.state==="suspended")await audioCtx.resume();
  musicOn=!musicOn;
  document.getElementById("musicBtn").textContent=
    musicOn?"⏸️ HENTIKAN MUSIK":"🎵 PUTAR MUSIK";

  if(musicOn){
    playNote();
    musicTimer=setInterval(playNote,260);
  }else{
    clearInterval(musicTimer);
  }
};

// ===== PERAYAAN MERIAH =====
let partyId=null;
let partyEnd=0;
let confetti=[];
let fireworks=[];

function celebrate(){
  const pc=document.getElementById("partyCanvas");
  const c=pc.getContext("2d");
  const box=document.getElementById("celebration");
  const dpr=Math.min(devicePixelRatio||1,2);

  pc.width=innerWidth*dpr;
  pc.height=innerHeight*dpr;
  c.setTransform(dpr,0,0,dpr,0,0);

  box.innerHTML=
    "🎆 HAPPY BIRTHDAY RIO! 🎆"+
    "<div style='font-size:16px;margin-top:8px'>"+
    "🎂 Semoga semua impianmu tercapai! 🎂</div>";
  box.classList.remove("party-title");
  void box.offsetWidth;
  box.classList.add("party-title");

  const colors=[
    "#facc15","#fb7185","#38bdf8",
    "#c084fc","#4ade80","#ffffff"
  ];

  confetti=Array.from({length:180},()=>({
    x:Math.random()*innerWidth,
    y:-Math.random()*innerHeight,
    vx:(Math.random()-.5)*4,
    vy:2+Math.random()*4,
    size:3+Math.random()*5,
    angle:Math.random()*6,
    spin:(Math.random()-.5)*.2,
    color:colors[Math.floor(Math.random()*colors.length)]
  }));

  fireworks=[];
  partyEnd=performance.now()+8000;
  cancelAnimationFrame(partyId);

  function burst(x,y){
    for(let i=0;i<40;i++){
      const a=Math.random()*Math.PI*2;
      const speed=1+Math.random()*4;
      fireworks.push({
        x,y,
        vx:Math.cos(a)*speed,
        vy:Math.sin(a)*speed,
        life:1,
        color:colors[Math.floor(Math.random()*colors.length)]
      });
    }
  }

  function frame(now){
    c.clearRect(0,0,innerWidth,innerHeight);

    confetti.forEach(p=>{
      p.x+=p.vx;
      p.y+=p.vy;
      p.angle+=p.spin;
      if(p.y>innerHeight){
        p.y=-10;
        p.x=Math.random()*innerWidth;
      }
      c.save();
      c.translate(p.x,p.y);
      c.rotate(p.angle);
      c.fillStyle=p.color;
      c.fillRect(-p.size/2,-p.size/2,p.size,p.size*1.8);
      c.restore();
    });

    if(Math.random()<.09){
      burst(
        Math.random()*innerWidth,
        Math.random()*innerHeight*.55
      );
    }

    fireworks.forEach(p=>{
      p.x+=p.vx;
      p.y+=p.vy;
      p.vy+=.035;
      p.life-=.018;
      c.globalAlpha=Math.max(0,p.life);
      c.fillStyle=p.color;
      c.beginPath();
      c.arc(p.x,p.y,3,0,Math.PI*2);
      c.fill();
    });

    c.globalAlpha=1;
    fireworks=fireworks.filter(p=>p.life>0);

    if(now<partyEnd){
      partyId=requestAnimationFrame(frame);
    }else{
      c.clearRect(0,0,innerWidth,innerHeight);
      box.innerHTML="✨🎂 SELAMAT ULANG TAHUN! 🎂✨";
    }
  }
  partyId=requestAnimationFrame(frame);
}

// ===== SPACE SHOOTER =====
const canvas=document.getElementById("gameCanvas");
const ctx=canvas.getContext("2d");
const scoreEl=document.getElementById("score");
const livesEl=document.getElementById("lives");
const timeEl=document.getElementById("time");
const msg=document.getElementById("gameMessage");

let ship,bullets,enemies,particles;
let score=0,lives=3,time=30;
let running=false,last=0,elapsed=0,spawn=0,raf=0;
let keys={left:false,right:false};

function resetGame(){
  ship={x:160,y:370,cool:0};
  bullets=[];
  enemies=[];
  particles=[];
  score=0;
  lives=3;
  time=30;
  elapsed=0;
  spawn=0;
  keys={left:false,right:false};
  updateInfo();
}
function updateInfo(){
  scoreEl.textContent=score;
  livesEl.textContent=lives;
  timeEl.textContent=time;
}
function explosion(x,y,color){
  for(let i=0;i<14;i++){
    particles.push({
      x,y,
      vx:(Math.random()-.5)*150,
      vy:(Math.random()-.5)*150,
      life:.6,color
    });
  }
}
function drawBackground(){
  ctx.fillStyle="#020617";
  ctx.fillRect(0,0,320,420);

  for(let i=0;i<35;i++){
    const x=(i*79)%320;
    const y=(i*113+elapsed*35)%420;
    ctx.fillStyle="#c4b5fd";
    ctx.fillRect(x,y,2,2);
  }
}
function drawShip(){
  ctx.save();
  ctx.translate(ship.x,ship.y);

  ctx.fillStyle="#38bdf8";
  ctx.beginPath();
  ctx.moveTo(0,-16);
  ctx.lineTo(13,13);
  ctx.lineTo(0,7);
  ctx.lineTo(-13,13);
  ctx.closePath();
  ctx.fill();

  ctx.fillStyle="#fb923c";
  ctx.beginPath();
  ctx.moveTo(-5,11);
  ctx.lineTo(0,22+Math.random()*5);
  ctx.lineTo(5,11);
  ctx.fill();

  ctx.restore();
}
function finishGame(text){
  running=false;
  cancelAnimationFrame(raf);
  msg.textContent=text+" Skor akhir: "+score;
  document.getElementById("start").textContent="🔄 MAIN LAGI";
}

function gameLoop(now){
  if(!running)return;
  const dt=Math.min((now-last)/1000||0,.04);
  last=now;
  elapsed+=dt;
  spawn+=dt;

  // GERAK KIRI DAN KANAN
  if(keys.left)ship.x-=210*dt;
  if(keys.right)ship.x+=210*dt;
  ship.x=Math.max(15,Math.min(305,ship.x));

  // TEMBAKAN OTOMATIS
  ship.cool-=dt;
  if(ship.cool<=0){
    bullets.push({x:ship.x,y:ship.y-15});
    ship.cool=.22;
  }

  // METEOR MUNCUL
  if(spawn>.65){
    spawn=0;
    enemies.push({
      x:15+Math.random()*290,
      y:-15,
      r:10+Math.random()*5,
      speed:65+Math.random()*60
    });
  }

  bullets.forEach(b=>b.y-=320*dt);
  bullets=bullets.filter(b=>b.y>-10);
  enemies.forEach(e=>e.y+=e.speed*dt);

  // TABRAKAN PESAWAT DAN METEOR
  for(let i=enemies.length-1;i>=0;i--){
    const e=enemies[i];

    if(Math.hypot(e.x-ship.x,e.y-ship.y)<e.r+10){
      explosion(e.x,e.y,"#fb7185");
      enemies.splice(i,1);
      lives--;
      updateInfo();

      if(lives<=0){
        finishGame("💥 GAME OVER!");
        return;
      }
      continue;
    }

    if(e.y>440){
      enemies.splice(i,1);
      continue;
    }

    // PELURU MENGHANCURKAN METEOR
    for(let j=bullets.length-1;j>=0;j--){
      const b=bullets[j];
      if(Math.hypot(e.x-b.x,e.y-b.y)<e.r+4){
        explosion(e.x,e.y,"#fbbf24");
        enemies.splice(i,1);
        bullets.splice(j,1);
        score+=10;
        updateInfo();
        break;
      }
    }
  }

  // PARTIKEL LEDAKAN
  particles.forEach(p=>{
    p.x+=p.vx*dt;
    p.y+=p.vy*dt;
    p.life-=dt;
  });
  particles=particles.filter(p=>p.life>0);

  drawBackground();

  enemies.forEach(e=>{
    ctx.fillStyle="#fb7185";
    ctx.beginPath();
    ctx.arc(e.x,e.y,e.r,0,Math.PI*2);
    ctx.fill();
    ctx.fillStyle="#fef3c7";
    ctx.fillRect(e.x-3,e.y-3,6,6);
  });

  bullets.forEach(b=>{
    ctx.shadowBlur=12;
    ctx.shadowColor="#22d3ee";
    ctx.fillStyle="#67e8f9";
    ctx.fillRect(b.x-2,b.y-8,4,12);
    ctx.shadowBlur=0;
  });

  particles.forEach(p=>{
    ctx.globalAlpha=Math.max(0,p.life/.6);
    ctx.fillStyle=p.color;
    ctx.beginPath();
    ctx.arc(p.x,p.y,3,0,Math.PI*2);
    ctx.fill();
  });

  ctx.globalAlpha=1;
  drawShip();

  const newTime=Math.max(0,30-Math.floor(elapsed));
  if(newTime!==time){
    time=newTime;
    updateInfo();
  }
  if(time<=0){
    finishGame("🌟 WAKTU HABIS!");
    return;
  }
  raf=requestAnimationFrame(gameLoop);
}

document.getElementById("start").onclick=()=>{
  cancelAnimationFrame(raf);
  resetGame();
  running=true;
  last=performance.now();
  msg.textContent="🚀 Pesawat menembak otomatis!";
  document.getElementById("start").textContent="🔄 MULAI ULANG";
  raf=requestAnimationFrame(gameLoop);
};

function holdButton(id,key){
  const b=document.getElementById(id);
  b.addEventListener("pointerdown",e=>{
    e.preventDefault();
    keys[key]=true;
    if(b.setPointerCapture)b.setPointerCapture(e.pointerId);
  });
  ["pointerup","pointercancel","lostpointercapture"].forEach(ev=>{
    b.addEventListener(ev,()=>keys[key]=false);
  });
}
holdButton("left","left");
holdButton("right","right");

drawBackground();
ctx.fillStyle="white";
ctx.textAlign="center";
ctx.fillText("TEKAN MULAI GAME",160,210);

// ===== UCAPAN WHATSAPP =====
function sendWish(){
  const sender=document.getElementById("sender").value.trim();
  const wish=document.getElementById("wish").value.trim();

  if(!sender||!wish){
    alert("Isi nama dan ucapan dulu, ya!");
    return;
  }

  const text="🎂 Ucapan ulang tahun untuk Rio!\n\nDari: "+
    sender+"\n\n"+wish;

  window.open(
    "https://wa.me/6282316901973?text="+encodeURIComponent(text),
    "_blank"
  );
}

// ===== BAGIKAN UNDANGAN =====
async function shareInvite(){
  const data={
    title:"Happy Birthday Rio!",
    text:"🎂 Selamat ulang tahun, Rio!",
    url:location.href
  };

  if(navigator.share){
    try{await navigator.share(data);}catch(e){}
  }else{
    prompt("Salin alamat website ini:",location.href);
  }
}
</script>
</body>
</html>
