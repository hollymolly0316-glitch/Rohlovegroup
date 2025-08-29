<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>KF21 비행기 게임</title>
<style>
    body {
        margin: 0;
        background-color: #0a0a0a;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        color: white;
        font-family: Arial, sans-serif;
    }

    #gameContainer {
        position: relative;
    }

    canvas {
        background-color: #111;
        border: 2px solid #fff;
    }
</style>
</head>
<body>
<div id="gameContainer">
    <canvas id="gameCanvas" width="600" height="800"></canvas>
</div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const gameWidth = canvas.width;
const gameHeight = canvas.height;

// 플레이어 (KF21)
const player = {
    x: gameWidth / 2 - 25,
    y: gameHeight - 100,
    width: 50,
    height: 50,
    speed: 7,
    bullets: []
};

// 적기
const enemies = [];
const enemySpeed = 3;
let enemySpawnInterval = 2000;

// 키 입력
const keys = {};

document.addEventListener('keydown', e => { keys[e.key] = true; });
document.addEventListener('keyup', e => { keys[e.key] = false; });

// 총알 발사
function shoot() {
    player.bullets.push({ x: player.x + 22, y: player.y, width: 6, height: 12, speed: 10 });
}

// 적 생성
function spawnEnemy() {
    const x = Math.random() * (gameWidth - 50);
    enemies.push({ x, y: -50, width: 50, height: 50 });
}

// 충돌 체크
function isColliding(a, b) {
    return a.x < b.x + b.width &&
           a.x + a.width > b.x &&
           a.y < b.y + b.height &&
           a.y + a.height > b.y;
}

// 게임 루프
function update() {
    // 플레이어 이동
    if (keys['ArrowLeft'] && player.x > 0) player.x -= player.speed;
    if (keys['ArrowRight'] && player.x < gameWidth - player.width) player.x += player.speed;
    if (keys['ArrowUp'] && player.y > 0) player.y -= player.speed;
    if (keys['ArrowDown'] && player.y < gameHeight - player.height) player.y += player.speed;
    if (keys[' ']) shoot();

    // 총알 이동
    player.bullets.forEach((bullet, i) => {
        bullet.y -= bullet.speed;
        if (bullet.y < 0) player.bullets.splice(i, 1);
    });

    // 적 이동
    enemies.forEach((enemy, i) => {
        enemy.y += enemySpeed;
        // 적 충돌 체크
        player.bullets.forEach((bullet, j) => {
            if (isColliding(bullet, enemy)) {
                enemies.splice(i, 1);
                player.bullets.splice(j, 1);
            }
        });
        if (enemy.y > gameHeight) enemies.splice(i, 1);
    });
}

// 게임 그리기
function draw() {
    ctx.clearRect(0, 0, gameWidth, gameHeight);

    // 플레이어
    ctx.fillStyle = 'blue';
    ctx.fillRect(player.x, player.y, player.width, player.height);

    // 총알
    ctx.fillStyle = 'yellow';
    player.bullets.forEach(bullet => ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height));

    // 적기
    ctx.fillStyle = 'red';
    enemies.forEach(enemy => ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height));
}

// 게임 반복
function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

// 적 생성 반복
setInterval(spawnEnemy, enemySpawnInterval);

// 게임 시작
gameLoop();
</script>
</body>
</html>
