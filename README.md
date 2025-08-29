<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>크롬 공룡게임 🦖</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      background: #f7f7f7;
      font-family: Arial, sans-serif;
      overflow: hidden;
      flex-direction: column;
    }

    h1 {
      margin-bottom: 10px;
    }

    #game {
      position: relative;
      width: 800px;
      height: 200px;
      background: white;
      border: 2px solid #555;
      overflow: hidden;
    }

    #dino {
      position: absolute;
      bottom: 0;
      left: 50px;
      width: 44px;
      height: 47px;
      background: url("https://i.imgur.com/8QfS3FF.png") no-repeat center/contain; /* 작은 티라노 */
    }

    .cactus {
      position: absolute;
      bottom: 0;
      width: 20px;
      height: 40px;
      background: green;
    }

    #score {
      margin-top: 10px;
      font-size: 1.2rem;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>🦖 크롬 공룡게임</h1>
  <div id="game">
    <div id="dino"></div>
  </div>
  <div id="score">점수: 0</div>

  <script>
    const dino = document.getElementById("dino");
    const game = document.getElementById("game");
    const scoreDisplay = document.getElementById("score");

    let isJumping = false;
    let jumpHeight = 0;
    let gravity = 2.6; // 기존 2 → 1.3배 빠르게 (점프/낙하 속도)
    let score = 0;
    let gameOver = false;

    // 점프
    document.addEventListener("keydown", (e) => {
      if (e.code === "Space" && !isJumping) {
        isJumping = true;
        jump();
      }
    });

    function jump() {
      let upInterval = setInterval(() => {
        if (jumpHeight >= 100) {
          clearInterval(upInterval);

          // 내려오기
          let downInterval = setInterval(() => {
            if (jumpHeight <= 0) {
              clearInterval(downInterval);
              isJumping = false;
            }
            jumpHeight -= gravity;
            dino.style.bottom = jumpHeight + "px";
          }, 20);
        }
        jumpHeight += gravity;
        dino.style.bottom = jumpHeight + "px";
      }, 20);
    }

    // 선인장 생성
    function createCactus() {
      if (gameOver) return;
      let cactus = document.createElement("div");
      cactus.classList.add("cactus");
      cactus.style.left = "800px";
      game.appendChild(cactus);

      let moveInterval = setInterval(() => {
        if (gameOver) {
          clearInterval(moveInterval);
          cactus.remove();
          return;
        }

        let cactusLeft = parseInt(window.getComputedStyle(cactus).getPropertyValue("left"));
        let dinoBottom = parseInt(window.getComputedStyle(dino).getPropertyValue("bottom"));

        if (cactusLeft > 40 && cactusLeft < 90 && dinoBottom < 40) {
          clearInterval(moveInterval);
          gameOver = true;
          alert("게임 오버! 최종 점수: " + score);
          return;
        }

        cactus.style.left = cactusLeft - 5 + "px";
      }, 20);

      // 다음 선인장 랜덤 생성
      setTimeout(createCactus, Math.random() * 2000 + 1000);
    }

    // 점수 증가
    function increaseScore() {
      if (gameOver) return;
      score++;
      scoreDisplay.textContent = "점수: " + score;
      setTimeout(increaseScore, 200);
    }

    createCactus();
    increaseScore();
  </script>
</body>
</html>
