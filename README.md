<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>부엉이 잡기 게임</title>
<style>
    body {
        margin: 0;
        font-family: Arial, sans-serif;
        background-color: #a3d2ca;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        overflow: hidden;
    }
    #game-container {
        position: relative;
        width: 800px;
        height: 600px;
        background-color: #f7fff7;
        border: 3px solid #000;
        border-radius: 15px;
        overflow: hidden;
    }
    #owl {
        position: absolute;
        width: 80px;
        height: 80px;
        cursor: pointer;
        transition: transform 0.1s;
    }
    #scoreboard {
        position: absolute;
        top: 10px;
        left: 10px;
        font-size: 20px;
        font-weight: bold;
    }
    #timer {
        position: absolute;
        top: 10px;
        right: 10px;
        font-size: 20px;
        font-weight: bold;
    }
    #start-btn {
        position: absolute;
        bottom: 20px;
        left: 50%;
        transform: translateX(-50%);
        padding: 10px 20px;
        font-size: 18px;
        cursor: pointer;
        background-color: #1976D2;
        color: white;
        border: none;
        border-radius: 5px;
    }
    #start-btn:hover {
        background-color: #0D47A1;
    }
</style>
</head>
<body>

<div id="game-container">
    <div id="scoreboard">점수: 0</div>
    <div id="timer">시간: 30</div>
    <img src="https://upload.wikimedia.org/wikipedia/commons/1/12/Cartoon_owl.png" id="owl" alt="부엉이" style="top: 100px; left: 100px;">
    <button id="start-btn">게임 시작</button>
</div>

<script>
    const owl = document.getElementById('owl');
    const scoreboard = document.getElementById('scoreboard');
    const timerDisplay = document.getElementById('timer');
    const startBtn = document.getElementById('start-btn');
    const container = document.getElementById('game-container');

    let score = 0;
    let timeLeft = 30;
    let gameInterval;
    let timerInterval;

    function moveOwl() {
        const maxX = container.clientWidth - owl.clientWidth;
        const maxY = container.clientHeight - owl.clientHeight;
        const x = Math.random() * maxX;
        const y = Math.random() * maxY;
        owl.style.left = x + 'px';
        owl.style.top = y + 'px';
    }

    owl.addEventListener('click', () => {
        if(timeLeft > 0){
            score++;
            scoreboard.textContent = '점수: ' + score;
            moveOwl();
        }
    });

    function startGame() {
        score = 0;
        timeLeft = 30;
        scoreboard.textContent = '점수: 0';
        timerDisplay.textContent = '시간: 30';
        moveOwl();
        owl.style.display = 'block';
        startBtn.style.display = 'none';

        timerInterval = setInterval(() => {
            timeLeft--;
            timerDisplay.textContent = '시간: ' + timeLeft;
            if(timeLeft <= 0){
                clearInterval(timerInterval);
                alert('게임 종료! 최종 점수: ' + score);
                startBtn.style.display = 'block';
            }
        }, 1000);
    }

    startBtn.addEventListener('click', startGame);
</script>

</body>
</html>
