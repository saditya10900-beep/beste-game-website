
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Aditya Gaming</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{
  background:#080914;color:white;
  font-family:Arial,sans-serif;
}
header{
  display:flex;justify-content:space-between;
  align-items:center;padding:22px 7%;
  background:#101123;border-bottom:1px solid #3932ff;
}
.logo{font-size:25px;font-weight:bold;color:#6c7bff}
nav a{color:white;text-decoration:none;margin-left:18px}
.hero{
  text-align:center;padding:85px 20px;
  background:radial-gradient(ellipse at top,#25216c,#080914 70%);
}
.hero h1{font-size:clamp(38px,7vw,70px);margin-bottom:18px}
.hero h1 span{color:#7c6cff}
.hero p{color:#c8c8db;line-height:1.8;margin-bottom:25px}
button{
  background:#6555ff;color:white;border:0;
  border-radius:8px;padding:13px 22px;
  cursor:pointer;font-weight:bold;
}
button:hover{background:#4333df}
section.games{padding:45px 7%}
h2{margin-bottom:25px}
.grid{
  display:grid;
  grid-template-columns:repeat(auto-fit,minmax(210px,1fr));
  gap:20px;
}
.card{
  background:#151629;border:1px solid #292b4b;
  border-radius:13px;overflow:hidden;
  transition:.25s;
}
.card:hover{transform:translateY(-6px);border-color:#7565ff}
.cover{
  height:145px;display:grid;place-items:center;
  font-size:65px;
  background:linear-gradient(135deg,#22266b,#8c35b9);
}
.card:nth-child(2) .cover{background:linear-gradient(135deg,#00685f,#152d57)}
.card:nth-child(3) .cover{background:linear-gradient(135deg,#8b2635,#31164d)}
.info{padding:19px}
.info p{color:#bbb;margin:12px 0 17px;font-size:14px}
#gameArea{
  display:none;text-align:center;
  padding:25px 12px 40px;background:#0d1020;
}
#gameArea canvas{
  display:block;width:min(100%,600px);
  height:auto;margin:20px auto;
  background:#030511;border:2px solid #5146ff;
  border-radius:8px;touch-action:manipulation;
}
#status{margin:12px}
footer{text-align:center;padding:25px;color:#9292a8;background:#101123}
</style>
</head>
<body>

<header>
  <div class="logo">⚡ ADITYA GAMING</div>
  <nav>
    <a href="#home">Home</a>
    <a href="#games">Games</a>
    <a href="#play">Play</a>
  </nav>
</header>

<section class="hero" id="home">
  <h1>LEVEL UP YOUR <span>GAME!</span></h1>
  <p>Welcome to Aditya Singh's gaming zone.<br>
  Discover games, challenge yourself and beat your high score!</p>
  <button onclick="startGame()">▶ PLAY NOW</button>
</section>

<section class="games" id="games">
  <h2>🔥 Popular Games</h2>
  <div class="grid">
    <div class="card">
      <div class="cover">🚀</div>
      <div class="info">
        <h3>Space Shooter</h3>
        <p>Shoot enemy ships and collect points!</p>
        <button onclick="startGame()">Play Game</button>
      </div>
    </div>
    <div class="card">
      <div class="cover">🏎️</div>
      <div class="info">
        <h3>Racing Zone</h3>
        <p>Ready for a high-speed racing adventure?</p>
        <button onclick="comingSoon('Racing Zone')">Coming Soon</button>
      </div>
    </div>
    <div class="card">
      <div class="cover">⚔️</div>
      <div class="info">
        <h3>Battle Arena</h3>
        <p>Enter the arena and prepare for battle.</p>
        <button onclick="comingSoon('Battle Arena')">Coming Soon</button>
      </div>
    </div>
  </div>
</section>

<section id="gameArea">
  <h2>🚀 Space Shooter</h2>
  <p>Move your ship with ← → or A/D. Press SPACE to shoot.</p>
  <p id="status">Score: 0 | Lives: 3</p>
  <canvas id="game" width="600" height="400"></canvas>
  <button id="startBtn" onclick="startGame()">Start / Restart</button>
  <button onclick="closeGame()" style="background:#41445b">Close Game</button>
  <p style="margin-top:15px;color:#aaa">Mobile: tap left or right side to move and shoot.</p>
</section>

<footer>
  © 2026 Aditya Singh | ADITYA GAMING
</footer>

<script>
const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");
let player,bullets,enemies,score,lives,running,keys,frame,loopId;

function startGame(){
  document.getElementById("gameArea").style.display="block";
  document.getElementById("gameArea").scrollIntoView({behavior:"smooth"});
  cancelAnimationFrame(loopId);
  player={x:280,y:350,w:36,h:24};
  bullets=[];enemies=[];score=0;lives=3;frame=0;
  keys={};running=true;
  document.getElementById("startBtn").textContent="Restart Game";
  loop();
}

function closeGame(){
  running=false;
  cancelAnimationFrame(loopId);
  document.getElementById("gameArea").style.display="none";
}

document.addEventListener("keydown",e=>{
  if(["ArrowLeft","ArrowRight"," ","ArrowUp","ArrowDown"].includes(e.key))e.preventDefault();
  keys[e.key.toLowerCase()]=true;
  if(e.code==="Space")keys.space=true;
});
document.addEventListener("keyup",e=>{
  keys[e.key.toLowerCase()]=false;
  if(e.code==="Space")keys.space=false;
});

canvas.addEventListener("pointerdown",e=>{
  const r=canvas.getBoundingClientRect();
  const x=(e.clientX-r.left)*canvas.width/r.width;
  player.x=x-player.w/2;
  bullets.push({x:player.x+player.w/2-3,y:player.y,w:6,h:12});
});

function loop(){
  if(!running)return;
  frame++;
  ctx.clearRect(0,0,600,400);

  // Stars
  for(let i=0;i<45;i++){
    let x=(i*137)%600,y=(i*79+frame*1.5)%400;
    ctx.fillStyle="#8589c7";
    ctx.fillRect(x,y,2,2);
  }

  if(keys.arrowleft||keys.a)player.x-=5;
  if(keys.arrowright||keys.d)player.x+=5;
  player.x=Math.max(0,Math.min(600-player.w,player.x));

  if(keys.space&&frame%10===0)
    bullets.push({x:player.x+15,y:player.y,w:6,h:12});

  // Player ship
  ctx.fillStyle="#6b7cff";
  ctx.beginPath();
  ctx.moveTo(player.x+18,player.y-8);
  ctx.lineTo(player.x+36,player.y+24);
  ctx.lineTo(player.x,player.y+24);
  ctx.closePath();ctx.fill();

  if(frame%42===0)
    enemies.push({x:Math.random()*560,y:-25,w:28,h:24,s:1.5+Math.random()*1.5});

  bullets.forEach(b=>b.y-=7);
  enemies.forEach(e=>e.y+=e.s);

  bullets=bullets.filter(b=>b.y>-15);
  enemies=enemies.filter(e=>e.y<420);

  // Draw and detect collisions
  for(let i=enemies.length-1;i>=0;i--){
    let e=enemies[i];
    ctx.fillStyle="#ff456b";
    ctx.fillRect(e.x,e.y,e.w,e.h);

    if(e.x<player.x+player.w&&e.x+e.w>player.x&&
       e.y<player.y+player.h&&e.y+e.h>player.y){
      enemies.splice(i,1);lives--;continue;
    }

    if(e.y>380){enemies.splice(i,1);lives--;continue;}

    for(let j=bullets.length-1;j>=0;j--){
      let b=bullets[j];
      if(b.x<e.x+e.w&&b.x+b.w>e.x&&
         b.y<e.y+e.h&&b.y+b.h>e.y){
        enemies.splice(i,1);bullets.splice(j,1);
        score+=10;break;
      }
    }
  }

  ctx.fillStyle="#53edff";
  bullets.forEach(b=>ctx.fillRect(b.x,b.y,b.w,b.h));

  document.getElementById("status").textContent=
    "Score: "+score+" | Lives: "+lives;

  if(lives<=0){
    running=false;
    ctx.fillStyle="white";
    ctx.font="bold 34px Arial";
    ctx.textAlign="center";
    ctx.fillText("GAME OVER",300,185);
    ctx.font="20px Arial";
    ctx.fillText("Final Score: "+score,300,225);
    document.getElementById("startBtn").textContent="Play Again";
    return;
  }
  loopId=requestAnimationFrame(loop);
}

function comingSoon(name){
  alert(name+" is coming soon! Stay tuned, Aditya.");
}
</script>
</body>
</html>
