
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ABC 莫蘭迪五選一</title>
    <style>
        :root {
            --bg-base: #E2E2D5;     /* 莫蘭迪底色 */
            --bg-correct: #84A59D;  /* 答對：青石綠 */
            --bg-wrong: #D4A373;    /* 答錯：秋葉黃 */
            --card: #F4F1DE;        /* 卡片顏色 */
            --text: #5E6472;        /* 深灰藍文字 */
        }

        body { 
            margin: 0; display: flex; flex-direction: column; justify-content: center; align-items: center; 
            height: 100vh; background: var(--bg-base); font-family: 'Arial', sans-serif; 
            transition: background 0.5s; overflow: hidden;
        }
        
        #question { font-size: 32px; margin-bottom: 30px; color: var(--text); font-weight: bold; }
        
        .options-container { display: flex; gap: 12px; flex-wrap: wrap; justify-content: center; max-width: 90vw; }
        
        .option { 
            width: 110px; height: 110px; 
            background: var(--card); border-radius: 20px; 
            display: flex; justify-content: center; align-items: center; 
            font-size: 60px; font-weight: bold; color: var(--text);
            box-shadow: 0 6px 0 rgba(0,0,0,0.05); cursor: pointer; transition: 0.2s;
            user-select: none;
        }

        /* 答對時的亮色特效 */
        .correct-active { background: #A3B18A !important; color: white !important; transform: scale(1.1); box-shadow: 0 0 20px rgba(163, 177, 138, 0.6); }

        #msg-btn { 
            margin-top: 40px; padding: 10px 25px; font-size: 18px; border-radius: 50px; 
            border: 2px solid var(--text); background: transparent; color: var(--text); 
            cursor: pointer; opacity: 0.7;
        }
    </style>
</head>
<body>

    <div id="question">點擊畫面開始</div>
    
    <div class="options-container">
        <div class="option" onclick="checkAnswer(0)" id="opt0">?</div>
        <div class="option" onclick="checkAnswer(1)" id="opt1">?</div>
        <div class="option" onclick="checkAnswer(2)" id="opt2">?</div>
        <div class="option" onclick="checkAnswer(3)" id="opt3">?</div>
        <div class="option" onclick="checkAnswer(4)" id="opt4">?</div>
    </div>

    <button id="msg-btn" onclick="speakQuestion()">🔊 重聽一次</button>

    <script>
        const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split("");
        let correctAnswer = "";
        let isCelebrating = false;
        let hasStarted = false;

        // 初始化：第一次點擊畫面啟動遊戲與音訊
        document.body.addEventListener('click', function() {
            if (!hasStarted) {
                hasStarted = true;
                nextRound();
            }
        }, { once: true });

        function nextRound() {
            isCelebrating = false;
            document.body.style.background = "var(--bg-base)";
            document.getElementById('question').innerText = "Find the letter...";
            
            const shuffled = [...letters].sort(() => 0.5 - Math.random());
            const choices = shuffled.slice(0, 5); // 改為 5 個選項
            correctAnswer = choices[Math.floor(Math.random() * 5)];
            
            for(let i=0; i<5; i++) {
                const opt = document.getElementById('opt' + i);
                opt.innerText = choices[i];
                opt.classList.remove('correct-active');
                opt.style.visibility = "visible";
            }
            speakQuestion();
        }

        function speakQuestion() {
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(`Can you find... ${correctAnswer}?`);
            msg.lang = 'en-US';
            msg.rate = 0.6; // 語速調慢
            window.speechSynthesis.speak(msg);
        }

        function checkAnswer(index) {
            if (isCelebrating || !hasStarted) return;
            
            const selectedOpt = document.getElementById('opt' + index);
            if (selectedOpt.innerText === correctAnswer) {
                isCelebrating = true;
                selectedOpt.classList.add('correct-active');
                document.body.style.background = "var(--bg-correct)";
                
                window.speechSynthesis.cancel();
                const praise = new SpeechSynthesisUtterance("Good job! " + correctAnswer);
                praise.lang = 'en-US';
                praise.rate = 0.7;
                window.speechSynthesis.speak(praise);
                
                setTimeout(nextRound, 2000);
            } else {
                document.body.style.background = "var(--bg-wrong)";
                window.speechSynthesis.cancel();
                const fail = new SpeechSynthesisUtterance("Try again");
                fail.lang = 'en-US';
                fail.rate = 0.8;
                window.speechSynthesis.speak(fail);
                
                setTimeout(() => {
                    if(!isCelebrating) document.body.style.background = "var(--bg-base)";
                }, 600);
            }
        }
    </script>
</body>
</html>
