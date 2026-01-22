<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>星際先鋒：重啟能源核心</title>
    <style>
        :root {
            /* 賽博龐克/科幻配色 */
            --space-bg: #050b14;
            --panel-bg: rgba(16, 33, 50, 0.95);
            --neon-cyan: #00f3ff;
            --neon-green: #0aff0a;
            --neon-red: #ff2a2a;
            --neon-yellow: #ffee00;
            --grid-line: rgba(0, 243, 255, 0.2);
            --text-main: #e0f7fa;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        
        body {
            font-family: 'Rajdhani', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--space-bg);
            color: var(--text-main);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            /* 動態星空背景 (模擬) */
            background-image: 
                radial-gradient(white, rgba(255,255,255,.2) 2px, transparent 3px),
                radial-gradient(white, rgba(255,255,255,.15) 1px, transparent 2px),
                radial-gradient(white, rgba(255,255,255,.1) 2px, transparent 3px);
            background-size: 550px 550px, 350px 350px, 250px 250px;
            background-position: 0 0, 40px 60px, 130px 270px;
            overscroll-behavior: none; 
        }

        .game-container {
            background-color: var(--panel-bg);
            border: 2px solid var(--neon-cyan);
            box-shadow: 0 0 20px rgba(0, 243, 255, 0.2), inset 0 0 50px rgba(0, 243, 255, 0.05);
            border-radius: 15px;
            width: 100%;
            max-width: 950px;
            padding: 30px;
            position: relative;
            display: flex;
            flex-direction: column;
            max-height: 95vh;
            overflow-y: auto;
            /* 科幻切角效果 */
            clip-path: polygon(
                20px 0, 100% 0, 
                100% calc(100% - 20px), calc(100% - 20px) 100%, 
                0 100%, 0 20px
            );
        }

        /* 裝飾線條 */
        .game-container::before {
            content: ''; position: absolute; top: 0; left: 0; width: 100%; height: 5px; background: var(--neon-cyan);
        }

        /* Header */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            margin-bottom: 25px;
            border-bottom: 1px solid var(--grid-line);
            padding-bottom: 15px;
        }

        h2 { 
            margin: 0; 
            font-size: 2rem; 
            color: var(--neon-cyan); 
            text-transform: uppercase; 
            letter-spacing: 2px;
            text-shadow: 0 0 10px rgba(0, 243, 255, 0.5);
        }

        .status-panel {
            text-align: right;
            font-family: 'Courier New', monospace;
        }

        .level-indicator {
            background: rgba(0, 243, 255, 0.1);
            border: 1px solid var(--neon-cyan);
            padding: 5px 15px;
            color: var(--neon-cyan);
            font-size: 0.9rem;
            display: inline-block;
            margin-bottom: 5px;
        }

        .energy-bar-container {
            width: 200px; height: 10px; background: #0a1520;
            border: 1px solid #333; margin-top: 5px;
        }
        .energy-fill { 
            height: 100%; background: var(--neon-green); width: 0%; 
            transition: width 0.5s; box-shadow: 0 0 10px var(--neon-green);
        }

        /* Story & Visual Display */
        .story-terminal {
            background: rgba(0, 0, 0, 0.6);
            border-left: 4px solid var(--neon-yellow);
            padding: 20px;
            margin-bottom: 25px;
            font-size: 1.2rem;
            line-height: 1.6;
            color: #d4f0f0;
            font-family: 'Courier New', monospace;
        }

        .visual-screen {
            background: radial-gradient(circle at center, #1a2a3a 0%, #050b14 100%);
            border: 1px solid var(--grid-line);
            border-radius: 5px;
            padding: 30px;
            min-height: 250px;
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 25px;
            position: relative;
            overflow: hidden;
        }
        
        /* 網格背景特效 */
        .visual-screen::after {
            content: ''; position: absolute; top:0; left:0; right:0; bottom:0;
            background-image: linear-gradient(var(--grid-line) 1px, transparent 1px),
            linear-gradient(90deg, var(--grid-line) 1px, transparent 1px);
            background-size: 20px 20px;
            opacity: 0.3;
            pointer-events: none;
        }

        /* Visual Components */
        /* Table Style */
        .tech-table {
            width: 100%; border-collapse: collapse; color: var(--neon-cyan);
            font-family: 'Courier New', monospace; z-index: 2;
        }
        .tech-table th { border-bottom: 2px solid var(--neon-cyan); padding: 10px; text-align: center; }
        .tech-table td { border-bottom: 1px solid var(--grid-line); padding: 15px; text-align: center; font-size: 1.2rem; }

        /* Solar Panel Grid */
        .solar-grid {
            display: grid; grid-template-columns: repeat(6, 45px); gap: 2px;
            z-index: 2; background: #000; padding: 2px; border: 2px solid #555;
        }
        .panel-cell {
            width: 45px; height: 45px; background: #1a237e; /* 深藍色太陽能板 */
            border: 1px solid #3949ab; position: relative;
        }
        .panel-cell.damaged {
            background: #2a1111; /* 受損 */
            border: 1px dashed var(--neon-red);
        }
        .panel-cell.damaged::after {
            content: '⚠️'; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); font-size: 1.2rem;
        }
        
        /* Radar / Angle */
        .radar-circle {
            width: 200px; height: 200px; border: 2px solid var(--neon-green);
            border-radius: 50%; position: relative; z-index: 2;
            background: rgba(10, 255, 10, 0.05);
            box-shadow: 0 0 15px rgba(10, 255, 10, 0.2);
        }
        .radar-line {
            position: absolute; top: 50%; left: 50%; width: 50%; height: 2px;
            background: rgba(10, 255, 10, 0.5); transform-origin: left center;
        }
        .target-beam {
            position: absolute; top: 50%; left: 50%; width: 90px; height: 4px;
            background: var(--neon-red); transform-origin: left center;
            transform: rotate(-135deg); /* 範例角度 */
            border-radius: 2px; box-shadow: 0 0 10px var(--neon-red);
        }
        .angle-arc {
            position: absolute; top: 50%; left: 50%; width: 60px; height: 60px;
            border: 2px dotted var(--neon-yellow); border-radius: 50%;
            clip-path: polygon(50% 50%, 100% 0, 100% 50%); /* 簡單示意 */
            transform: translate(-50%, -50%);
        }

        /* Inputs */
        .input-area { display: flex; gap: 15px; z-index: 5; margin-top: 20px; }
        
        input[type="number"] {
            flex: 1; padding: 15px; border: 2px solid var(--neon-cyan);
            background: rgba(0, 243, 255, 0.1); color: var(--neon-cyan);
            font-size: 1.5rem; text-align: center; font-family: 'Courier New', monospace;
            outline: none; clip-path: polygon(10px 0, 100% 0, 100% calc(100% - 10px), calc(100% - 10px) 100%, 0 100%, 0 10px);
        }
        
        button {
            flex: 1; padding: 15px; border: none; 
            background: var(--neon-cyan); color: #000;
            font-weight: bold; font-size: 1.2rem; cursor: pointer;
            clip-path: polygon(10px 0, 100% 0, 100% calc(100% - 10px), calc(100% - 10px) 100%, 0 100%, 0 10px);
            transition: all 0.2s;
        }
        button:active { transform: scale(0.98); background: #fff; }

        .btn-opt {
            background: transparent; border: 2px solid var(--neon-cyan); color: var(--neon-cyan);
        }
        .btn-opt.selected { background: var(--neon-cyan); color: #000; }

        /* Feedback */
        .feedback-box {
            margin-top: 20px; padding: 15px; border-left: 5px solid;
            display: none; font-family: 'Courier New', monospace;
            animation: slideIn 0.3s;
        }
        .feedback-box.correct { border-color: var(--neon-green); background: rgba(10, 255, 10, 0.1); color: var(--neon-green); }
        .feedback-box.wrong { border-color: var(--neon-red); background: rgba(255, 42, 42, 0.1); color: var(--neon-red); }

        .hidden { display: none !important; }
        @keyframes slideIn { from { opacity: 0; transform: translateX(-20px); } to { opacity: 1; transform: translateX(0); } }

        /* Mobile Adjust */
        @media (max-width: 600px) {
            .solar-grid { grid-template-columns: repeat(6, 35px); }
            .panel-cell { width: 35px; height: 35px; }
            .input-area { flex-direction: column; }
        }
    </style>
</head>
<body>

<div class="game-container">
    <div class="header">
        <h2>🚀 STAR PIONEER</h2>
        <div class="status-panel">
            <div class="level-indicator" id="level-badge">SYSTEM CHECK</div>
            <div class="energy-bar-container"><div class="energy-fill" id="energy-bar"></div></div>
        </div>
    </div>

    <div id="intro-screen">
        <div class="story-terminal">
            > 正在建立連線... 連線成功。<br>
            > 身份確認：首席工程師 (Chief Engineer)<br>
            > 任務簡報：<br>
            太空站 "Alpha-7" 遭遇隕石雨襲擊。主能源核心離線，太陽能板受損，導航系統偏離。<br>
            <br>
            我們需要你的數學運算能力來重新啟動系統。<br>
            請準備好計算燃料、校正角度並修復太陽能板。<br>
            <br>
            > 等待指令...
        </div>
        <div style="text-align: center; margin-top: 30px;">
            <button onclick="startGame()" style="width: 200px; background: var(--neon-green);">啟動修復程序</button>
        </div>
    </div>

    <div id="game-screen" class="hidden">
        <div class="story-terminal" id="q-text">Loading...</div>
        
        <div class="visual-screen" id="q-visual">
            </div>

        <div id="input-number" class="input-area hidden">
            <input type="number" id="user-input" placeholder="INPUT VALUE" inputmode="decimal">
            <button onclick="submitAnswer()">EXECUTE</button>
        </div>

        <div id="input-options" class="input-area hidden">
            </div>

        <div class="feedback-box" id="feedback"></div>

        <div style="margin-top: 20px; display: flex; justify-content: flex-end;">
            <button id="btn-hint" class="btn-opt" style="width: auto; margin-right: 10px; font-size: 0.9rem;" onclick="showHint()">[?] HINT</button>
            <button id="btn-next" class="hidden" style="width: auto; background: var(--neon-yellow);" onclick="nextLevel()">NEXT MODULE >></button>
        </div>
    </div>

    <div id="end-screen" class="hidden" style="text-align: center;">
        <h1 style="color: var(--neon-green); font-size: 3rem; text-shadow: 0 0 20px var(--neon-green);">MISSION ACCOMPLISHED</h1>
        <div class="story-terminal" style="text-align: center; border: 1px solid var(--neon-green);">
            所有系統已上線。<br>
            能源輸出：100%<br>
            通訊連結：穩定<br>
            <br>
            感謝你的協助，工程師。<br>
            太空站 Alpha-7 安全了。
        </div>
        <div style="margin-top: 30px; font-size: 2rem; color: var(--neon-cyan); font-family: 'Courier New';">
            SCORE: <span id="final-score">0</span>
        </div>
        <br>
        <button onclick="location.reload()" style="background: var(--neon-cyan);">重新任務</button>
    </div>
</div>

<script>
    const questions = [
        // Level 1: Multiplication (Fuel & Supplies)
        {
            type: "number",
            title: "模組 1：燃料裝填 (Multiplication)",
            text: "> 警告：備用發電機燃料不足。<br>> 現況：我們有 4 個儲存槽。<br>> 需求：為了啟動核心，每個儲存槽必須注入 12單位的「電漿燃料」。<br>> 請問：總共需要多少單位的電漿燃料？",
            visual: `
                <table class="tech-table">
                    <tr><th>儲存槽 ID</th><th>A-01</th><th>A-02</th><th>A-03</th><th>A-04</th></tr>
                    <tr><td>需求量</td><td>12</td><td>12</td><td>12</td><td>12</td></tr>
                </table>
            `,
            answer: 48,
            hint: "4 個儲存槽，每個都要 12 單位。\n計算式：12 × 4",
            explanation: "系統確認：12 × 4 = 48 單位。\n燃料注入中... 動力回復 30%。"
        },
        // Level 2: Multiplication (Logic/Comparison)
        {
            type: "number",
            title: "模組 2：物資傳送 (Logic)",
            text: "> 狀況：救援無人機最大載重為 100 kg。<br>> 貨物：我們需要傳送 8 箱零件，每箱重 12 kg。<br>> 運算：請問這批貨物的總重量是多少？(如果不超過 100 就能一次傳送)",
            visual: `<div style="font-size: 4rem; text-align: center;">📦 x 8</div><div style="text-align:center; color: var(--neon-cyan);">UNIT WEIGHT: 12 KG</div>`,
            answer: 96,
            hint: "先算出總重量：8 箱 × 12 kg。\n然後看看答案是不是小於 100。",
            explanation: "總重：12 × 8 = 96 kg。\n96 < 100，重量在安全範圍內。\n傳送程序啟動。"
        },
        // Level 3: Angles (Navigation)
        {
            type: "option",
            title: "模組 3：雷達校正 (Angles)",
            options: ["銳角 (Acute)", "直角 (Right)", "鈍角 (Obtuse)"],
            text: "> 警告：偵測到太空垃圾接近。<br>> 雷達顯示：目標物位於紅色射線方向。<br>> 請問：紅色射線與水平綠色基準線之間，形成的是什麼角？",
            visual: `
                <div class="radar-circle">
                    <div class="radar-line" style="transform: rotate(0deg); background:var(--neon-green); height:2px;"></div> <div class="target-beam" style="transform: rotate(-135deg); top:50%; left:50%; width:90px; transform-origin:0 0;"></div>
                    <div style="position:absolute; top:40px; left:40px; color:var(--neon-red);">TARGET</div>
                </div>
            `,
            answer: "鈍角 (Obtuse)",
            hint: "觀察角度張開的大小。\n它比直角(90度)還要大嗎？",
            explanation: "掃描確認：角度大於 90 度且小於 180 度，這是鈍角。\n防禦系統已鎖定目標。"
        },
        // Level 4: Angles (Rotation)
        {
            type: "number",
            title: "模組 4：天線轉向 (Rotation)",
            text: "> 指令：將通訊天線順時針旋轉。<br>> 當前位置：0 度 (正北方)。<br>> 操作：旋轉「3 個直角」。<br>> 請問：旋轉後，天線會指向幾度？(1個直角=90度)",
            visual: `
                <div style="display:flex; justify-content:center; align-items:center; height:100%;">
                    <div style="width:10px; height:100px; background:#333; position:relative;">
                        <div style="position:absolute; bottom:0; left:0; width:100%; height:100%; background:var(--neon-cyan); transform-origin:bottom center; transform: rotate(270deg); transition:1s;"></div>
                    </div>
                </div>
            `,
            answer: 270,
            hint: "1 個直角是 90 度。\n3 個直角是多少度？\n90 × 3 = ?",
            explanation: "校正完畢：90 × 3 = 270 度 (指向正西方)。\n通訊連結已建立。"
        },
        // Level 5: Area (Solar Panels)
        {
            type: "number",
            title: "模組 5：能源板修復 (Area)",
            text: "> 狀況：主太陽能板陣列受損。<br>> 掃描：這是一個 6 x 4 的矩形陣列 (共24片)。<br>> 損壞：其中有 5 片顯示紅色故障。<br>> 請問：目前「運作正常」的太陽能板面積(片數)是多少？",
            visual: `
                <div class="solar-grid" style="grid-template-columns: repeat(6, 40px);">
                    <div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div>
                    <div class="panel-cell"></div><div class="panel-cell damaged"></div><div class="panel-cell damaged"></div><div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div>
                    <div class="panel-cell"></div><div class="panel-cell damaged"></div><div class="panel-cell damaged"></div><div class="panel-cell damaged"></div><div class="panel-cell"></div><div class="panel-cell"></div>
                    <div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div><div class="panel-cell"></div>
                </div>
            `,
            answer: 19,
            hint: "方法一：算出總片數 (6x4)，然後減去壞掉的 5 片。\n方法二：直接數藍色的格子。",
            explanation: "總片數：6 × 4 = 24。\n正常片數：24 - 5 = 19。\n能源效率提升至 80%。"
        },
        // Level 6: Area (Composite Shape)
        {
            type: "number",
            title: "模組 6：船體補丁 (Composite Area)",
            text: "> 危機：外殼破洞需要修補。<br>> 材料：我們有兩塊金屬板。<br>> 甲板：邊長 5 的正方形。<br>> 乙板：長 8 寬 3 的長方形。<br>> 請問：這兩塊板子加起來的總面積是多少？",
            visual: `
                <div style="display:flex; justify-content:center; align-items:flex-end; gap:20px;">
                    <div style="width:50px; height:50px; border:2px solid var(--neon-yellow); display:flex; justify-content:center; align-items:center;">甲</div>
                    <div style="width:80px; height:30px; border:2px solid var(--neon-yellow); display:flex; justify-content:center; align-items:center;">乙</div>
                </div>
            `,
            answer: 49,
            hint: "分別算出面積再相加。\n甲(正方形)：5 × 5\n乙(長方形)：8 × 3",
            explanation: "甲面積：5 × 5 = 25。\n乙面積：8 × 3 = 24。\n總面積：25 + 24 = 49。\n修補完成！氣密性恢復。"
        }
    ];

    let currentIdx = 0;
    let score = 0;

    // Enter key support
    document.getElementById('user-input').addEventListener('keypress', function (e) {
        if (e.key === 'Enter') submitAnswer();
    });

    function startGame() {
        document.getElementById('intro-screen').classList.add('hidden');
        document.getElementById('game-screen').classList.remove('hidden');
        loadQuestion();
    }

    function loadQuestion() {
        const q = questions[currentIdx];
        
        // Reset UI
        document.getElementById('feedback').style.display = 'none';
        document.getElementById('feedback').className = 'feedback-box';
        document.getElementById('btn-next').classList.add('hidden');
        document.getElementById('user-input').value = '';
        document.getElementById('user-input').disabled = false;
        
        // Update Info
        document.getElementById('level-badge').innerText = q.title;
        document.getElementById('energy-bar').style.width = `${(currentIdx / questions.length) * 100}%`;
        
        // Render Text & Visual
        document.getElementById('q-text').innerHTML = q.text;
        document.getElementById('q-visual').innerHTML = q.visual;

        // Render Inputs
        const numInput = document.getElementById('input-number');
        const optInput = document.getElementById('input-options');

        if (q.type === 'number') {
            numInput.classList.remove('hidden');
            optInput.classList.add('hidden');
            // document.getElementById('user-input').focus(); // Optional focus
        } else {
            numInput.classList.add('hidden');
            optInput.classList.remove('hidden');
            optInput.innerHTML = '';
            q.options.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = 'btn-opt';
                btn.innerText = opt;
                btn.onclick = () => checkOption(opt, btn);
                optInput.appendChild(btn);
            });
        }
    }

    function showHint() {
        const q = questions[currentIdx];
        alert("SYSTEM HINT:\n" + q.hint);
    }

    function submitAnswer() {
        const val = document.getElementById('user-input').value;
        if (!val) return;
        checkResult(parseFloat(val) === questions[currentIdx].answer);
    }

    function checkOption(val, btn) {
        // Clear selection
        document.querySelectorAll('.btn-opt').forEach(b => b.classList.remove('selected'));
        btn.classList.add('selected');
        checkResult(val === questions[currentIdx].answer);
    }

    function checkResult(isCorrect) {
        const fb = document.getElementById('feedback');
        fb.style.display = 'block';

        if (isCorrect) {
            score += 100;
            fb.innerHTML = `> 運算正確 (CORRECT)<br>${questions[currentIdx].explanation}`;
            fb.className = 'feedback-box correct';
            
            // Lock UI
            document.getElementById('user-input').disabled = true;
            document.querySelectorAll('.btn-opt').forEach(b => b.disabled = true);
            document.getElementById('btn-next').classList.remove('hidden');
        } else {
            fb.innerHTML = `> 運算錯誤 (ERROR)<br>數據不吻合，請重新計算或使用提示。`;
            fb.className = 'feedback-box wrong';
        }
    }

    function nextLevel() {
        currentIdx++;
        if (currentIdx < questions.length) {
            loadQuestion();
        } else {
            endGame();
        }
    }

    function endGame() {
        document.getElementById('game-screen').classList.add('hidden');
        document.getElementById('end-screen').classList.remove('hidden');
        document.getElementById('energy-bar').style.width = '100%';
        document.getElementById('final-score').innerText = score;
    }
</script>

</body>
</html>
