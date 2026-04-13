<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ABC 莫蘭迪冒險</title>
    <style>
        /* 莫蘭迪色系定義 */
        :root {
            --bg-main: #E2E2D5;     /* 米灰色 */
            --bg-correct: #A3B18A;  /* 莫蘭迪綠 */
            --bg-wrong: #B5838D;    /* 莫蘭迪粉紅 */
            --text-dark: #6B705C;   /* 深橄欖綠文字 */
            --card-white: #F8F9FA;  /* 柔軟白 */
            --btn-blue: #8DA9C4;    /* 灰藍色 */
        }

        body { 
            margin: 0; display: flex; flex-direction: column; justify-content: center; align-items: center; 
            height: 100vh; background: var(--bg-main); font-family: 'Helvetica', 'Arial', sans-serif; 
            transition: background 0.6s; overflow: hidden; cursor: pointer;
        }
        
        #question { font-size: 40px; margin-bottom: 40px; color: var(--text-dark); font-weight: bold; text-align: center; }
        
        .options-container { display: flex; gap: 20px; }
        
        .option { 
            width: 150px; height: 150px; 
            background: var(--card-white); border-radius: 30px; 
            display: flex; justify-content: center; align-items: center; 
            font-size: 90px; font-weight: bold; color: var(--text-dark);
            box-shadow: 0 8px 0 rgba(0,0,0,0.05); transition: 0.2s;
            user-select: none; -webkit-tap-highlight-color: transparent;
        }
        
        /* 答對時的縮放動畫 */
        @keyframes success-pop {
            0% { transform: scale(1); }
            50% { transform: scale(1.2); }
            100% { transform: scale(1); }
        }
        .correct-animate { animation: success-pop 0.5s ease; background: var(--bg-correct) !important; color: white !important; }

        #msg-btn { 
            margin-top: 50px; padding: 12px 30px; font-size: 20px; border-radius: 50px; 
            border: 2px solid var(--btn-blue); background: transparent; color: var(--btn-blue); 
            cursor: pointer; font-weight: bold;
        }
    </style>
</head>
<body onclick="initGame()">

    <div id="question">點擊畫面開始</div>
    
    <div class="options-container">
        <div class="option" onclick="checkAnswer(0, event)" id="opt0">?</div>
        <div class="option" onclick="checkAnswer(1, event)" id="opt1">?</div>
        <div class="option" onclick="checkAnswer(2, event)" id="opt2">?</div>
    </div>

    <button id="msg-btn" onclick="speakQuestion(event)">🔊 再聽一次題目</button>

    <script>
        const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split("");
        let correctAnswer = "";
        let isCelebrating = false;
        let gameStarted = false;

        function initGame() {
            if (!gameStarted) {
                gameStarted = true;
                nextRound();
            }
        }

        function nextRound() {
            isCelebrating = false;
            document.body.style.background = "#E2E2D5"; // 回到米灰色
            document.getElementById('question').innerText = "Where is...?";
            
            // 洗牌並選出三個字母
            const shuffled = [...letters].sort(() => 0.5 - Math.random());
            const choices = shuffled.slice(0, 3);
            correctAnswer = choices[Math.floor(Math.random() * 3)];
            
            for(let i=0; i<3; i++) {
                const opt = document.getElementById('opt' + i);
                opt.innerText = choices[i];
                opt.classList.remove('correct-animate');
                opt.style.visibility = "visible";
            }
            speakQuestion();
        }

        function speakQuestion(e) {
            if(e) e.stopPropagation(); // 防止觸發 body 的點擊
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(`Where is ${correctAnswer}?`);
            msg.lang = 'en-US';
            msg.rate = 0.8;
            window.speechSynthesis.speak(msg);
        }

        function checkAnswer(index, e) {
            e.stopPropagation();
            if (isCelebrating) return;
            
            const selectedOpt = document.getElementById('opt' + index);
            if (selectedOpt.innerText === correctAnswer) {
                isCelebrating = true;
                selectedOpt.classList.add('correct-animate');
                document.body.style.background = "#A3B18A"; // 變莫蘭迪綠
                
                window.speechSynthesis.cancel();
                const praise = new SpeechSynthesisUtterance("Wonderful! " + correctAnswer);
                praise.lang = 'en-US';
                window.speechSynthesis.speak(praise);
                
                // 延長一點時間再進入下一題，避免卡住
                setTimeout(nextRound, 1800);
            } else {
                document.body.style.background = "#B5838D"; // 變莫蘭迪粉
                window.speechSynthesis.cancel();
                const fail = new SpeechSynthesisUtterance("Try again");
                fail.lang = 'en-US';
                window.speechSynthesis.speak(fail);
                
                setTimeout(() => {
                    if(!isCelebrating) document.body.style.background = "#E2E2D5";
                }, 800);
            }
        }
    </script>
</body>
</html>
