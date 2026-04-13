<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ABC 聽力超級挑戰</title>
    <style>
        body { margin: 0; display: flex; flex-direction: column; justify-content: center; align-items: center; height: 100vh; background: #FFDE59; font-family: 'Arial', sans-serif; transition: 0.2s; overflow: hidden; }
        
        #question { font-size: 48px; margin-bottom: 30px; color: #333; font-weight: bold; text-shadow: 2px 2px 0 rgba(255,255,255,0.5); text-align: center; }
        
        .options-container { display: flex; gap: 25px; }
        
        .option { 
            width: 160px; height: 160px; 
            background: white; border-radius: 25px; 
            display: flex; justify-content: center; align-items: center; 
            font-size: 100px; font-weight: bold; color: #333;
            box-shadow: 0 12px 0 #ccc; cursor: pointer; transition: 0.1s;
            user-select: none; -webkit-tap-highlight-color: transparent;
        }
        
        .option:active { transform: translateY(10px); box-shadow: 0 2px 0 #ccc; }
        
        /* 答對時的瘋狂旋轉動畫 */
        @keyframes correct-spin {
            0% { transform: scale(1) rotate(0deg); }
            50% { transform: scale(1.5) rotate(720deg); background: #7ED957; color: white;}
            100% { transform: scale(1) rotate(1440deg); }
        }
        .correct-animate { animation: correct-spin 1s ease-out; }
        
        /* 答錯時的震動動畫 */
        @keyframes wrong-shake {
            0% { transform: translateX(0); }
            25% { transform: translateX(-15px); }
            50% { transform: translateX(15px); }
            75% { transform: translateX(-15px); }
            100% { transform: translateX(0); }
        }
        .wrong-animate { animation: wrong-shake 0.4s; }

        #msg-btn { margin-top: 60px; padding: 18px 40px; font-size: 28px; border-radius: 50px; border: none; background: #5271FF; color: white; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.2); }
    </style>
</head>
<body>

    <div id="question">準備好了嗎？</div>
    
    <div class="options-container">
        <div class="option" onclick="checkAnswer(0)" id="opt0">?</div>
        <div class="option" onclick="checkAnswer(1)" id="opt1">?</div>
        <div class="option" onclick="checkAnswer(2)" id="opt2">?</div>
    </div>

    <button id="msg-btn" onclick="speakQuestion()">再聽一次題目</button>

    <script>
        const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split("");
        let correctAnswer = "";
        let isCelebrating = false; // 防止連續點擊

        // 誇張的讚美詞清單
        const praises = [
            "Unbelievable! You are a genius!",
            "Wow! Spectacular! Absolutely correct!",
            "Bingo! You are the champion!",
            "Amazing! Your brain is super powerful!",
            "Yes! You did it! So proud of you!",
            "Perfect! 100 points for you!"
        ];

        function nextRound() {
            if (isCelebrating) return;
            isCelebrating = false;
            
            // 重設所有樣式
            document.body.style.background = "#FFDE59";
            document.getElementById('question').innerText = "Where is...?";
            document.getElementById('question').style.color = "#333";
            
            for(let i=0; i<3; i++) {
                const opt = document.getElementById('opt' + i);
                opt.classList.remove('correct-animate', 'wrong-animate');
                opt.style.background = "white";
                opt.style.color = "#333";
                opt.style.display = "flex"; // 重新顯示
            }
            
            const shuffled = [...letters].sort(() => 0.5 - Math.random());
            const choices = shuffled.slice(0, 3);
            correctAnswer = choices[Math.floor(Math.random() * 3)];
            
            document.getElementById('opt0').innerText = choices[0];
            document.getElementById('opt1').innerText = choices[1];
            document.getElementById('opt2').innerText = choices[2];

            speakQuestion();
        }

        function speakQuestion() {
            if (isCelebrating) return;
            window.speechSynthesis.cancel();
            const msg = new SpeechSynthesisUtterance(`Can you find letter... ${correctAnswer}?`);
            msg.lang = 'en-US';
            msg.rate = 0.7; // 慢速清晰
            window.speechSynthesis.speak(msg);
        }

        function checkAnswer(index) {
            if (isCelebrating) return; // 正在慶祝時不能點擊
            
            const selectedOpt = document.getElementById('opt' + index);
            const selectedText = selectedOpt.innerText;
            
            if (selectedText === correctAnswer) {
                isCelebrating = true; // 開始慶祝
                
                // --- 1. 視覺誇張特效 ---
                // 隱藏錯的，只留下對的
                for(let i=0; i<3; i++) {
                    if (i !== index) document.getElementById('opt' + i).style.display = "none";
                }
                
                // 答對的按鈕瘋狂旋轉
                selectedOpt.classList.add('correct-animate');
                
                // 題目文字變彩虹
                document.getElementById('question').innerText = "⭐ EXCELLENT! ⭐";
                document.getElementById('question').style.color = "#8C52FF";

                // 背景彩虹閃爍 (快速變色 3 次)
                let rainbowColors = ['#FF5757', '#7ED957', '#5271FF', '#FF66C4'];
                let colorIdx = 0;
                let rainbowTimer = setInterval(() => {
                    document.body.style.background = rainbowColors[colorIdx % rainbowColors.length];
                    colorIdx++;
                }, 1500); // 1.5 秒換一次色

                // --- 2. 聽覺誇張特效 ---
                window.speechSynthesis.cancel();
                // 隨機選一個誇張讚美詞
                const praiseText = praises[Math.floor(Math.random() * praises.length)];
                
                const praise = new SpeechSynthesisUtterance(praiseText + " ... The answer is " + correctAnswer);
                praise.lang = 'en-US';
                praise.rate = 1.0; // 語速正常，聽起來興奮
                praise.pitch = 1.3; // 音調調高，聽起來更開心
                window.speechSynthesis.speak(praise);
                
                // 3.5 秒後自動下一題 (等慶祝完)
                setTimeout(() => {
                    clearInterval(rainbowTimer); // 停止閃爍
                    nextRound();
                }, 3500);

            } else {
                // --- 答錯的反應 ---
                selectedOpt.classList.add('wrong-animate'); // 震動
                document.body.style.background = "#FF5757"; // 變紅
                
                window.speechSynthesis.cancel();
                const fail = new SpeechSynthesisUtterance("Oops! Try again!");
                fail.lang = 'en-US';
                window.speechSynthesis.speak(fail);
                
                // 0.5 秒後恢復背景顏色，讓小孩繼續嘗試
                setTimeout(() => {
                    document.body.style.background = "#FFDE59";
                    selectedOpt.classList.remove('wrong-animate');
                }, 500);
            }
        }

        // 頁面載入後自動開始
        window.onload = () => {
            // 第一次需要使用者點擊才能發聲，所以把題目文字改一下
            document.getElementById('question').innerText = "點擊這裡開始！";
            document.body.onclick = function() {
                document.body.onclick = null; // 取消原本的點擊事件
                nextRound();
            }
        };
    </script>
</body>
</html>
