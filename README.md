<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>무한열차 - WASD+마우스 전투</title>
<style>
body {margin:0; background:#111; color:white; font-family:Arial, sans-serif; display:flex; flex-direction:column; align-items:center;}
h1 {margin:10px; font-size:2rem; text-shadow:2px 2px 4px black;}
canvas {border:3px solid white; background:#222;}
#hp {margin-top:10px; font-size:1.3rem;}
</style>
</head>
<body>
<h1>🔥 무한열차 - WASD + 마우스</h1>
<canvas id="gameCanvas" width="2000" height="800"></canvas>
<div id="hp">체력: ❤️❤️❤️❤️❤️ (5/5)</div>

<script>
const canvas=document.getElementById("gameCanvas");
const ctx=canvas.getContext("2d");

const rengoku={x:150, y:700, width:60, height:90, hp:5, defense:false};
const akaza={x:1800, y:700, width:60, height:90, hp:5};
const fireballs=[];
const akazaAttacks=[];
let gameOver=false;

const keys={};

document.addEventListener("keydown", e=>{ keys[e.key.toLowerCase()]=true; });
document.addEventListener("keyup", e=>{ keys[e.key.toLowerCase()]=false; });

canvas.addEventListener("mousedown", e=>{
  if(e.button===0){ // 좌클릭 공격
    fireballs.push({x:rengoku.x+rengoku.width, y:rengoku.y+20, width:30, height:30, type:"attack"});
  } else if(e.button===2){ // 우클릭 방어막
    rengoku.defense=true;
    setTimeout(()=>rengoku.defense=false,300);
  }
});

canvas.addEventListener("contextmenu", e=>e.preventDefault()); // 우클릭 메뉴 제거

// 아카자 AI
function akazaAI(){
  if(gameOver) return;
  const r=Math.random();
  if(r<0.33) akazaAttacks.push({x:akaza.x, y:akaza.y+30, width:25, height:25, type:"ranged"});
  else if(r<0.66) akazaAttacks.push({x:akaza.x-50, y:akaza.y, width:50, height:50, type:"melee"});
  else akazaAttacks.push({x:akaza.x, y:akaza.y, width:35, height:35, type:"special"});
  setTimeout(akazaAI, Math.random()*2000+1000);
}
akazaAI();

function drawCharacter(char,color){ ctx.fillStyle=color; ctx.fillRect(char.x,char.y,char.width,char.height); }

function draw(){
  ctx.clearRect(0,0,canvas.width,canvas.height);

  // 이동
  const speed=8;
  if(keys['w']) rengoku.y=Math.max(0,rengoku.y-speed);
  if(keys['s']) rengoku.y=Math.min(canvas.height-rengoku.height,rengoku.y+speed);
  if(keys['a']) rengoku.x=Math.max(0,rengoku.x-speed);
  if(keys['d']) rengoku.x=Math.min(canvas.width-rengoku.width,rengoku.x+speed);

  // 캐릭터
  drawCharacter(rengoku,rengoku.defense?"#FFD700":"#FF4500");
  drawCharacter(akaza,"#00FFFF");

  // 공격
  fireballs.forEach((f,i)=>{
    ctx.fillStyle="#FF8C00";
    ctx.fillRect(f.x,f.y,f.width,f.height);
    f.x += 15;
    if(f.x>=akaza.x && f.x<=akaza.x+akaza.width && f.y+f.height>=akaza.y && f.y<=akaza.y+akaza.height){
      fireballs.splice(i,1); akaza.hp--; 
      if(akaza.hp<=0){ gameOver=true; alert("렌고쿠 승리!"); }
    }
    if(f.x>canvas.width) fireballs.splice(i,1);
  });

  // 아카자 공격
  akazaAttacks.forEach((a,i)=>{
    ctx.fillStyle=a.type==="ranged"?"cyan":a.type==="melee"?"blue":"magenta";
    ctx.fillRect(a.x,a.y,a.width,a.height);
    a.x -= a.type==="ranged"?10:a.type==="melee"?6:7;
    if(a.x < rengoku.x+rengoku.width && a.x+a.width>rengoku.x && a.y+a.height>rengoku.y && a.y<rengoku.y+rengoku.height){
      if(!rengoku.defense){ rengoku.hp--; updateHP(); akazaAttacks.splice(i,1); if(rengoku.hp<=0){ gameOver=true; alert("아카자 승리..."); } }
    }
  });

  if(!gameOver) requestAnimationFrame(draw);
}

function updateHP(){ const hearts="❤️".repeat(rengoku.hp)+"🖤".repeat(5-rengoku.hp); document.getElementById("hp").innerText=`체력: ${hearts} (${rengoku.hp}/5)`;}

draw();
</script>
</body>
</html>
