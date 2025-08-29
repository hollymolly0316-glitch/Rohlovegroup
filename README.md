<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>KF-21 vs 일본 전투기</title>
<style>
    body {
        margin: 0;
        overflow: hidden;
        background-color: #87ceeb;
        font-family: Arial, sans-serif;
    }
    #gameCanvas {
        background-color: #87ceeb;
        display: block;
        margin: 0 auto;
        border: 2px solid black;
    }
    #scoreboard {
        position: absolute;
        top: 10px;
        left: 10px;
        font-size: 20px;
        color: white;
        font-weight: bold;
        text-shadow: 2px 2px 2px black;
    }
</style>
</head>
<body>
<canvas id="gameCanvas" width="800" height="600"></canvas>
<div id="scoreboard">점수: 0</div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const playerImg = new Image();
playerImg.src = 'https://upload.wikimedia.org/wikipedia/commons/7/7f/KF-21_RAID_2.png'; // KF-21 이미지

const enemyImg = new Image();
enemyImg.src = 'https://upload.wikimedia.org/wikipedia/commons/0/0e/Mitsubishi_F-2_%28JASDF%29.png'; // 일본 전투기 이미지

let player = { x: 360, y: 500, width: 80, height: 80, speed: 5 };
let bullets = [];
let enemies = [];
let score = 0;
let gameOver = false;
let mousePressed = false;

// 마우스 클릭 공격
canvas.addEventListener('mousedown', () => mousePressed = true);
canvas.addEventListener('mouseup', () => mousePressed = false);

// 마우스 위치 추적
let mouseX = 0, mouseY = 0;
canvas.addEventListener('mousemove', (e) => {
    const rect = canvas.getBoundingClientRect();
    mouseX = e.clientX - rect.left;
    mouseY = e.clientY - rect.top;
});

// WASD 키 입력
let keys = {};
document.addEventListener('keydown', (e) => keys[e.key.toLowerCase()] = true);
document.addEventListener('keyup', (e) => keys[e.key.toLowerCase()] = false);

// 총알 발사
function shoot() {
    bullets.push({ x: player.x + player.width/2 - 5, y: player.y, width: 10, height: 20, speed: 7 });
}

// 적기 생성
function spawnEnemy() {
    let x = Math.random() * (canvas.width - 80);
    enemies.push({ x: x, y: -80, width: 80, height: 80, speed: 2 + Math.random() * 2 });
}

// 충돌 체크
function isCollide(a,b){
    return a.x < b.x + b.width &&
           a.x + a.width > b.x &&
           a.y < b.y + b.height &&
           a.y + a.height > b.y;
}

// 게임 업데이트
function update() {
    if(gameOver) return;

    // 플레이어 이동
    if(keys['w'] && player.y > 0) player.y -= player.speed;
    if(keys['s'] && player.y + player.height < canvas.height) player.y += player.speed;
    if(keys['a'] && player.x > 0) player.x -= player.speed;
    if(keys['d'] && player.x + player.width < canvas.width) player.x += player.speed;

    // 마우스 클릭 시 총알 발사
    if(mousePressed){
        shoot();
        mousePressed = false; // 클릭마다 1발 발사
    }

    // 총알 이동
    bullets.forEach((b,i)=>{
        b.y -= b.speed;
        if(b.y < -20) bullets.splice(i,1);
    });

    // 적기 이동 및 충돌 체크
    enemies.forEach((e,i)=>{
        e.y += e.speed;

        if(isCollide(player,e)){
            gameOver = true;
            alert('게임 종료! 최종 점수: '+score);
        }

        bullets.forEach((b,j)=>{
            if(isCollide(b,e)){
                bullets.splice(j,1);
                enemies.splice(i,1);
                score += 10;
                document.getElementById('scoreboard').textContent = '점수: ' + score;
            }
        });

        if(e.y > canvas.height) enemies.splice(i,1);
    });
}

// 게임 그리기
function draw(){
    ctx.clearRect(0,0,canvas.width,canvas.height);
    ctx.drawImage(playerImg, player.x, player.y, player.width, player.height);
    bullets.forEach(b=>ctx.fillStyle='yellow',ctx.fillRect(b.x,b.y,b.width,b.height));
    enemies.forEach(e=>ctx.drawImage(enemyImg,e.x,e.y,e.width,e.height));
}

// 게임 루프
function gameLoop(){
    update();
    draw();
    if(!gameOver) requestAnimationFrame(gameLoop);
}

// 적기 생성 주기
setInterval(()=>{ if(!gameOver) spawnEnemy(); }, 1500);

gameLoop();
</script>

</body>
</html>
