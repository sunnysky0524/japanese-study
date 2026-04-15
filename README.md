<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>日語單字 50 音練習</title>
    <style>
        body { font-family: -apple-system, sans-serif; background: #fdf2f8; margin: 0; padding: 0; display: flex; flex-direction: column; height: 100vh; }
        .header { background: #d63384; color: white; padding: 15px; text-align: center; font-weight: bold; }
        .tabs { display: flex; background: white; border-bottom: 1px solid #ddd; }
        .tab { flex: 1; padding: 12px; text-align: center; cursor: pointer; color: #666; font-weight: bold; }
        .tab.active { color: #d63384; border-bottom: 3px solid #d63384; }
        .content { flex: 1; overflow-y: auto; padding: 15px; display: none; }
        .content.active { display: block; }
        
        /* 瀏覽模式樣式 */
        .list-card { background: white; border-radius: 12px; padding: 10px; margin-bottom: 10px; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; justify-content: space-between; align-items: center; }
        .list-jp { font-size: 1.2rem; font-weight: bold; color: #d63384; }
        .list-zh { color: #666; font-size: 0.9rem; }

        /* 測驗模式樣式 */
        .quiz-card { background: white; width: 100%; max-width: 450px; padding: 25px; border-radius: 20px; box-shadow: 0 5px 15px rgba(0,0,0,0.1); text-align: center; margin: 20px auto; }
        .question { font-size: 1.8rem; font-weight: bold; margin-bottom: 25px; color: #333; min-height: 80px; display: flex; align-items: center; justify-content: center; }
        .options { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; width: 100%; }
        button.opt-btn { padding: 15px; font-size: 1rem; border: 2px solid #fbcfe8; background: white; border-radius: 12px; color: #d63384; font-weight: 600; cursor: pointer; }
        button.next-btn { background: #d63384; color: white; border: none; width: 100%; margin-top: 20px; padding: 15px; border-radius: 12px; font-size: 1.1rem; display: none; }
        #result { margin-top: 15px; font-weight: bold; font-size: 1.2rem; min-height: 1.5em; }
    </style>
</head>
<body>

    <div class="header">日語基礎學習</div>

    <div class="tabs">
        <div class="tab active" onclick="switchTab('learn')">📖 瀏覽單字</div>
        <div class="tab" onclick="switchTab('quiz')">📝 進行測驗</div>
    </div>

    <div id="learn-content" class="content active"></div>

    <div id="quiz-content" class="content">
        <div class="quiz-card">
            <div id="q-text" class="question">準備好了嗎？</div>
            <div id="options-box" class="options"></div>
            <div id="result"></div>
            <button id="next-btn" class="next-btn" onclick="renderQuestion()">開始測驗</button>
        </div>
    </div>

    <script>
        const wordData = [
            // 清音單字
            { j: "いえ", z: "家" }, { j: "えき", z: "車站" }, { j: "かき", z: "柿子" },
            { j: "きく", z: "菊花" }, { j: "いけ", z: "池塘" }, { j: "こい", z: "鯉魚" },
            { j: "あき", z: "秋天" }, { j: "うえ", z: "上面" }, { j: "かお", z: "臉" },
            { j: "なし", z: "梨子" }, { j: "さかな", z: "魚" }, { j: "いぬ", z: "狗" },
            { j: "ねこ", z: "貓" }, { j: "はな", z: "花" }, { j: "はと", z: "鴿子" },
            // 片假名單字
            { j: "スマホ", z: "智慧型手機" }, { j: "コンビニ", z: "便利商店" },
            { t: "K", j: "タクシー", z: "計程車" }, { j: "デパート", z: "百貨公司" },
            { j: "トイレ", z: "洗手間" }, { j: "アイスクリーム", z: "冰淇淋" },
            { j: "ケーキ", z: "蛋糕" }, { j: "コーヒー", z: "咖啡" }
        ];

        // 初始化瀏覽列表
        const learnDiv = document.getElementById('learn-content');
        wordData.forEach(item => {
            const card = document.createElement('div');
            card.className = 'list-card';
            card.innerHTML = `<span class="list-jp">${item.j}</span><span class="list-zh">${item.z}</span>`;
            learnDiv.appendChild(card);
        });

        // 頁籤切換
        function switchTab(tab) {
            document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
            document.querySelectorAll('.content').forEach(c => c.classList.remove('active'));
            if(tab === 'learn') {
                document.querySelector('.tab:nth-child(1)').classList.add('active');
                document.getElementById('learn-content').classList.add('active');
            } else {
                document.querySelector('.tab:nth-child(2)').classList.add('active');
                document.getElementById('quiz-content').classList.add('active');
                renderQuestion();
            }
        }

        // 測驗邏輯
        let currentIdx = 0;
        let shuffled = [];

        function renderQuestion() {
            if (currentIdx === 0) shuffled = [...wordData].sort(() => Math.random() - 0.5);
            
            const item = shuffled[currentIdx];
            document.getElementById('q-text').innerText = item.j;
            const box = document.getElementById('options-box');
            box.innerHTML = "";
            document.getElementById('result').innerText = "";
            document.getElementById('next-btn').style.display = "none";

            // 生成選項
            let choices = [item.z];
            while(choices.length < 4) {
                let r = wordData[Math.floor(Math.random() * wordData.length)].z;
                if(!choices.includes(r)) choices.push(r);
            }
            choices.sort(() => Math.random() - 0.5).forEach(choice => {
                const btn = document.createElement('button');
                btn.className = "opt-btn";
                btn.innerText = choice;
                btn.onclick = () => {
                    const res = document.getElementById('result');
                    if (choice === item.z) {
                        res.innerText = "⭕ 正確！";
                        res.style.color = "#059669";
                    } else {
                        res.innerText = "❌ 正確是：" + item.z;
                        res.style.color = "#dc2626";
                    }
                    Array.from(box.children).forEach(b => b.onclick = null);
                    document.getElementById('next-btn').style.display = "block";
                    document.getElementById('next-btn').innerText = "下一題 ❯";
                    currentIdx = (currentIdx + 1) % shuffled.length;
                };
                box.appendChild(btn);
            });
        }
    </script>
</body>
</html>
