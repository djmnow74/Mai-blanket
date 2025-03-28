<!--
版权所有 © 2025 [djm]
此代码实现均为友人Mai,
希望你可以在这里自由呼吸
-->

<!DOCTYPE html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>文字治愈互动</title>
    <style>
        /* 加载动画样式 */
        #loading-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #e6f7ff;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            transition: opacity 0.5s ease-out;
        }

        .spinner {
            width: 50px;
            height: 50px;
            border: 5px solid rgba(44, 130, 201, 0.2);
            border-top: 5px solid #2c82c9;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-bottom: 20px;
        }

        @keyframes spin {
            0% {
                transform: rotate(0deg);
            }

            100% {
                transform: rotate(360deg);
            }
        }

        .loading-text {
            color: #2c82c9;
            font-size: 18px;
            font-family: 'Helvetica Neue', Arial, sans-serif;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {

            0%,
            100% {
                opacity: 0.6;
            }

            50% {
                opacity: 1;
            }
        }

        /* 主游戏样式 */
        body {
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background-color: #e6f7ff;
            font-family: 'Helvetica Neue', Arial, sans-serif;
            color: #333;
            line-height: 1.6;
        }

        #game-container {
            width: 90%;
            max-width: 600px;
            text-align: center;
            padding: 30px;
            background-color: rgba(255, 255, 255, 0.9);
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            display: none;
            /* 初始隐藏 */
        }

        h1 {
            color: #2c82c9;
            margin-bottom: 30px;
        }

        .option {
            display: inline-block;
            margin: 15px;
            padding: 15px 25px;
            background-color: #2c82c9;
            color: white;
            border-radius: 50px;
            cursor: pointer;
            font-size: 18px;
            transition: all 0.3s;
            box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1);
        }

        .option:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 10px rgba(0, 0, 0, 0.2);
            background-color: #3a9bdc;
        }

        #encouragement-screen,
        #selfFeel-screen,
        #meditation-screen,
        #breathing-screen,
        #journal-screen {
            display: none;
        }

        .encouragement-word {
            position: absolute;
            font-size: 18px;
            color: #2c82c9;
            cursor: pointer;
            transition: all 0.3s;
            opacity: 0;
            animation: fadeIn 0.5s forwards;
        }

        @keyframes fadeIn {
            to {
                opacity: 1;
            }
        }

        .selected-word {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 28px;
            color: #e74c3c;
            text-align: center;
            background-color: rgba(255, 255, 255, 0.9);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
            z-index: 100;
            animation: pulse 2s infinite;
        }

        .question {
            margin: 20px 0;
            font-size: 20px;
            color: #2c3e50;
        }

        .answer {
            display: inline-block;
            margin: 10px;
            padding: 10px 20px;
            background-color: #3498db;
            color: white;
            border-radius: 30px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .answer:hover {
            background-color: #2980b9;
        }

        .followup-option {
            display: inline-block;
            margin: 15px;
            padding: 15px 25px;
            background-color: #48cfad;
            color: white;
            border-radius: 50px;
            cursor: pointer;
            font-size: 18px;
            transition: all 0.3s;
            box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1);
            width: 150px;
        }

        .followup-option:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 10px rgba(0, 0, 0, 0.2);
            background-color: #37bc9b;
        }

        #back-btn,
        #back-btn-2,
        #back-btn-3,
        #back-btn-4,
        #back-btn-5 {
            margin-top: 20px;
            padding: 10px 20px;
            background-color: #95a5a6;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s;
        }

        #back-btn:hover,
        #back-btn-2:hover,
        #back-btn-3:hover,
        #back-btn-4:hover,
        #back-btn-5:hover {
            background-color: #7f8c8d;
        }

        /* 冥想引导样式 */
        .meditation-step {
            margin: 20px 0;
            padding: 15px;
            background-color: rgba(255, 255, 255, 0.7);
            border-radius: 10px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }

        .meditation-timer {
            font-size: 24px;
            margin: 20px 0;
            color: #2c82c9;
        }

        /* 呼吸练习样式 */
        .breathing-circle {
            width: 150px;
            height: 150px;
            border-radius: 50%;
            background-color: #48cfad;
            margin: 30px auto;
            display: flex;
            justify-content: center;
            align-items: center;
            color: white;
            font-size: 24px;
            animation: breathe 8s infinite;
        }

        @keyframes breathe {

            0%,
            100% {
                transform: scale(0.8);
                background-color: #48cfad;
            }

            50% {
                transform: scale(1.2);
                background-color: #37bc9b;
            }
        }

        .breathing-instruction {
            font-size: 20px;
            margin: 20px 0;
        }

        /* 情绪日记样式 */
        #journal-textarea {
            width: 90%;
            height: 200px;
            margin: 20px 0;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 10px;
            font-family: 'Helvetica Neue', Arial, sans-serif;
            font-size: 16px;
            resize: none;
        }

        #save-journal {
            padding: 12px 25px;
            background-color: #5d9cec;
            color: white;
            border: none;
            border-radius: 30px;
            font-size: 18px;
            cursor: pointer;
            transition: all 0.3s;
        }

        #save-journal:hover {
            background-color: #4a89dc;
        }

        .followup-options-container {
            margin-top: 30px;
        }

        /* 响应式设计 */
        @media (max-width: 600px) {

            .option,
            .followup-option {
                display: block;
                margin: 10px auto;
                width: 80%;
            }

            #game-container {
                width: 95%;
                padding: 15px;
            }

            .breathing-circle {
                width: 120px;
                height: 120px;
            }
        }
    </style>
</head>

<body>
    <!-- 加载动画 -->
    <div id="loading-overlay">
        <div class="spinner"></div>
        <div class="loading-text">加载中，请稍候...</div>
    </div>

    <!-- 主游戏容器 -->
    <div id="game-container">
        <div id="home-screen">
            <h1>文字治愈互动</h1>
            <div class="option" id="encouragement-option">言语的力量</div>
            <div class="option" id="selfFeel-option">感受你自己</div>
        </div>

        <div id="encouragement-screen">
            <h2>让这些话语给你力量</h2>
            <h3>来自《好东西》，《妇女参政论者》</h3>
            <p>点击你喜欢的话语</p>
            <div id="words-container"></div>
            <button id="back-btn">返回</button>
        </div>

        <div id="selfFeel-screen">
            <div id="questions-container"></div>
            <div id="followup-options" class="followup-options-container" style="display: none;">
                <h3>你想尝试哪种方式调节情绪？</h3>
                <div class="followup-option" id="meditation-option">冥想引导</div>
                <div class="followup-option" id="breathing-option">呼吸练习</div>
                <div class="followup-option" id="journal-option">情绪日记</div>
            </div>
            <button id="back-btn-2">返回</button>
        </div>

        <!-- 冥想引导屏幕 -->
        <div id="meditation-screen">
            <h2>冥想引导</h2>
            <div id="meditation-content">
                <div class="meditation-step">
                    <h3>准备阶段</h3>
                    <p>找一个安静舒适的地方坐下，背部挺直但不要僵硬，双手自然放在膝盖上。</p>
                </div>
                <div class="meditation-step">
                    <h3>呼吸调整</h3>
                    <p>闭上眼睛，慢慢深呼吸3次：吸气4秒，屏息2秒，呼气6秒。</p>
                </div>
                <div class="meditation-step">
                    <h3>身体扫描</h3>
                    <p>从头顶开始，慢慢感受身体的每个部位，放松任何紧张的肌肉。</p>
                </div>
                <div class="meditation-step">
                    <h3>专注呼吸</h3>
                    <p>将注意力集中在呼吸上，感受空气进出鼻腔的感觉。</p>
                </div>
                <div class="meditation-step">
                    <h3>结束冥想</h3>
                    <p>慢慢睁开眼睛，感受此刻的平静与放松。</p>
                </div>
            </div>
            <div class="meditation-timer" id="meditation-timer">准备开始</div>
            <button id="start-meditation">开始冥想</button>
            <button id="back-btn-3">返回</button>
        </div>

        <!-- 呼吸练习屏幕 -->
        <div id="breathing-screen">
            <h2>呼吸练习</h2>
            <div class="breathing-instruction">跟随圆圈呼吸：</div>
            <div class="breathing-circle" id="breathing-circle">准备开始</div>
            <div class="breathing-instruction" id="breathing-instruction">
                点击"开始练习"按钮开始
            </div>
            <button id="start-breathing">开始练习</button>
            <button id="back-btn-4">返回</button>
        </div>

        <!-- 情绪日记屏幕 -->
        <div id="journal-screen">
            <h2>情绪日记</h2>
            <p>写下你此刻的感受和想法：</p>
            <textarea id="journal-textarea" placeholder="今天我感觉..."></textarea>
            <button id="save-journal">保存记录</button>
            <button id="view-history">查看历史日记</button>
            <button id="back-btn-5">返回</button>
        </div>
    </div>

    <script>
        // 游戏数据
        const positiveWords = [
            "为什么什么都要做好？裁判是谁啊？为什么就不能搞砸？”",
            "能让你开心的就是好东西",
            "那我们不要玩他们的游戏了。",
            "如果你没有准备好，你可以不上台的",
            "我正直勇敢有阅读量",
            "never surrender.never give up the fight",
            "我会好好活着，等你们长大，建立一个新的游戏",
            "我的价值不多，但也不比你少",
            "我们都是我们自己道路上的一个士兵",
            "我不会背叛我的",
            "we will win",
            "你的眼泪也很好吃",

        ];

        const questions = [
            "你觉得好吗？",
            "你的脑袋是什么感受？",
            "你的身体是什么感受？"
        ];

        const answers = [
            ["好", "一般", "不太好"],
            ["清晰", "混沌", "沉重"],
            ["轻松", "紧张", "疲惫"]
        ];

        // DOM元素
        const homeScreen = document.getElementById('home-screen');
        const encouragementScreen = document.getElementById('encouragement-screen');
        const selfFeelScreen = document.getElementById('selfFeel-screen');
        const meditationScreen = document.getElementById('meditation-screen');
        const breathingScreen = document.getElementById('breathing-screen');
        const journalScreen = document.getElementById('journal-screen');
        const wordsContainer = document.getElementById('words-container');
        const questionsContainer = document.getElementById('questions-container');
        const followupOptions = document.getElementById('followup-options');
        const meditationTimer = document.getElementById('meditation-timer');
        const startMeditationBtn = document.getElementById('start-meditation');
        const breathingCircle = document.getElementById('breathing-circle');
        const breathingInstruction = document.getElementById('breathing-instruction');
        const startBreathingBtn = document.getElementById('start-breathing');
        const journalTextarea = document.getElementById('journal-textarea');
        const saveJournalBtn = document.getElementById('save-journal');
        const viewHistoryBtn = document.getElementById('view-history');
        const loadingOverlay = document.getElementById('loading-overlay');
        const gameContainer = document.getElementById('game-container');

        // 游戏状态
        let currentQuestion = 0;
        let userResponses = [];
        let currentSelectedWord = null;
        let meditationInterval;
        let currentStep = 0;
        let breathingInterval;
        const meditationSteps = [
            { text: "准备阶段", duration: 5 },
            { text: "呼吸调整", duration: 30 },
            { text: "身体扫描", duration: 60 },
            { text: "专注呼吸", duration: 90 },
            { text: "结束冥想", duration: 10 }
        ];

        // 初始化游戏
        function init() {
            // 设置事件监听
            setupEventListeners();

            // 模拟资源加载
            setTimeout(() => {
                loadingOverlay.style.opacity = '0';
                setTimeout(() => {
                    loadingOverlay.style.display = 'none';
                    gameContainer.style.display = 'block';
                }, 500);
            }, 1500);
        }

        // 设置事件监听
        function setupEventListeners() {
            document.getElementById('encouragement-option').addEventListener('click', showEncouragementScreen);
            document.getElementById('selfFeel-option').addEventListener('click', showSelfFeelScreen);
            document.getElementById('back-btn').addEventListener('click', goBackToHome);
            document.getElementById('back-btn-2').addEventListener('click', goBackToHome);
            document.getElementById('back-btn-3').addEventListener('click', goBackToHome);
            document.getElementById('back-btn-4').addEventListener('click', goBackToHome);
            document.getElementById('back-btn-5').addEventListener('click', goBackToHome);
            startMeditationBtn.addEventListener('click', startMeditation);
            document.getElementById('meditation-option').addEventListener('click', showMeditationScreen);
            document.getElementById('breathing-option').addEventListener('click', showBreathingScreen);
            document.getElementById('journal-option').addEventListener('click', showJournalScreen);
            startBreathingBtn.addEventListener('click', startBreathingExercise);
            saveJournalBtn.addEventListener('click', saveJournal);
            viewHistoryBtn.addEventListener('click', showJournalHistory);
        }

        // 显示鼓励话语屏幕
        function showEncouragementScreen() {
            homeScreen.style.display = 'none';
            encouragementScreen.style.display = 'block';
            wordsContainer.innerHTML = '';

            // 创建漂浮的鼓励话语
            positiveWords.forEach(word => {
                const wordElement = document.createElement('div');
                wordElement.className = 'encouragement-word';
                wordElement.textContent = word;

                // 随机位置
                const left = Math.random() * 80 + 10;
                const top = Math.random() * 50 + 20;

                wordElement.style.left = `${left}%`;
                wordElement.style.top = `${top}%`;

                // 点击事件
                wordElement.addEventListener('click', function () {
                    showSelectedWord(word);
                });

                wordsContainer.appendChild(wordElement);

                // 添加漂浮动画
                animateWord(wordElement);
            });
        }

        // 动画效果：单词漂浮
        function animateWord(element) {
            let x = Math.random() * 10 - 5;
            let y = Math.random() * 10 - 5;

            function move() {
                const rect = element.getBoundingClientRect();
                const left = parseFloat(element.style.left);
                const top = parseFloat(element.style.top);

                // 边界检测
                if (left + x < 5 || left + x > 95) x = -x;
                if (top + y < 5 || top + y > 95) y = -y;

                element.style.left = (left + x * 0.1) + '%';
                element.style.top = (top + y * 0.1) + '%';

                requestAnimationFrame(move);
            }

            move();
        }

        // 显示选中的单词
        function showSelectedWord(word) {
            // 移除之前选中的单词
            if (currentSelectedWord) {
                currentSelectedWord.remove();
            }

            const selectedWord = document.createElement('div');
            selectedWord.className = 'selected-word';
            selectedWord.textContent = word;
            document.body.appendChild(selectedWord);

            currentSelectedWord = selectedWord;
        }

        // 显示自我感受屏幕
        function showSelfFeelScreen() {
            homeScreen.style.display = 'none';
            selfFeelScreen.style.display = 'block';
            questionsContainer.innerHTML = '';
            followupOptions.style.display = 'none';
            currentQuestion = 0;
            userResponses = [];

            showQuestion();
        }

        // 显示当前问题
        function showQuestion() {
            questionsContainer.innerHTML = '';

            const questionElement = document.createElement('div');
            questionElement.className = 'question';
            questionElement.textContent = questions[currentQuestion];
            questionsContainer.appendChild(questionElement);

            // 添加回答选项
            answers[currentQuestion].forEach((answer, index) => {
                const answerElement = document.createElement('div');
                answerElement.className = 'answer';
                answerElement.textContent = answer;

                answerElement.addEventListener('click', function () {
                    userResponses.push(index);
                    currentQuestion++;

                    if (currentQuestion < questions.length) {
                        showQuestion();
                    } else {
                        showFollowupOptions();
                    }
                });

                questionsContainer.appendChild(answerElement);
            });
        }

        // 显示后续选项
        function showFollowupOptions() {
            questionsContainer.style.display = 'none';
            followupOptions.style.display = 'block';
        }

        // 显示冥想引导屏幕
        function showMeditationScreen() {
            selfFeelScreen.style.display = 'none';
            meditationScreen.style.display = 'block';
            meditationTimer.textContent = "准备开始";
            currentStep = 0;
        }

        // 显示呼吸练习屏幕
        function showBreathingScreen() {
            selfFeelScreen.style.display = 'none';
            breathingScreen.style.display = 'block';
            breathingCircle.textContent = "准备开始";
            breathingCircle.style.animation = "none";
            breathingCircle.style.transform = "scale(1)";
            breathingInstruction.textContent = "点击'开始练习'按钮开始";
        }

        // 显示情绪日记屏幕
        function showJournalScreen() {
            selfFeelScreen.style.display = 'none';
            journalScreen.style.display = 'block';
            journalTextarea.value = "";
        }

        // 开始冥想
        function startMeditation() {
            startMeditationBtn.style.display = 'none';
            runMeditationStep();
        }

        // 执行冥想步骤
        function runMeditationStep() {
            if (currentStep >= meditationSteps.length) {
                meditationTimer.textContent = "冥想完成";
                startMeditationBtn.style.display = 'block';
                return;
            }

            const step = meditationSteps[currentStep];
            meditationTimer.textContent = `${step.text} (${step.duration}秒)`;

            let timeLeft = step.duration;
            meditationInterval = setInterval(() => {
                timeLeft--;
                meditationTimer.textContent = `${step.text} (${timeLeft}秒)`;

                if (timeLeft <= 0) {
                    clearInterval(meditationInterval);
                    currentStep++;
                    runMeditationStep();
                }
            }, 1000);
        }

        // 开始呼吸练习
        function startBreathingExercise() {
            startBreathingBtn.style.display = 'none';
            let cycle = 0;
            const totalCycles = 5; // 5次完整呼吸循环

            const updateBreathing = () => {
                const step = cycle % 12;

                if (step < 4) {
                    breathingCircle.textContent = `吸气 (${4 - step}秒)`;
                    breathingCircle.style.animation = "breathe 4s infinite";
                    breathingInstruction.textContent = "吸气4秒 → 屏息2秒 → 呼气6秒";
                } else if (step < 6) {
                    breathingCircle.textContent = `屏息 (${6 - step}秒)`;
                    breathingCircle.style.animation = "none";
                    breathingCircle.style.transform = "scale(1.2)";
                    breathingInstruction.textContent = "屏息 → 准备呼气";
                } else {
                    breathingCircle.textContent = `呼气 (${12 - step}秒)`;
                    breathingCircle.style.animation = "breathe 6s infinite";
                    breathingInstruction.textContent = "缓慢呼气，放松身体";
                }

                cycle++;
                if (cycle >= totalCycles * 12) {
                    clearInterval(breathingInterval);
                    breathingCircle.textContent = "练习完成";
                    breathingCircle.style.animation = "none";
                    breathingInstruction.textContent = "你已经完成了5次深呼吸循环";
                    startBreathingBtn.style.display = 'block';
                    startBreathingBtn.textContent = "重新开始";
                }
            };

            updateBreathing();
            breathingInterval = setInterval(updateBreathing, 1000);
        }

        // 保存情绪日记
        function saveJournal() {
            const entry = journalTextarea.value.trim();
            if (entry) {
                // 获取当前日期时间
                const now = new Date();
                const timestamp = now.toLocaleString();

                // 保存到localStorage
                let entries = JSON.parse(localStorage.getItem('journalEntries') || '[]');
                entries.unshift({
                    date: timestamp,
                    content: entry
                });
                localStorage.setItem('journalEntries', JSON.stringify(entries));

                // 显示保存成功提示
                const savedMsg = document.createElement('div');
                savedMsg.textContent = "✓ 日记已保存";
                savedMsg.style.cssText = `
                    color: #27ae60;
                    margin: 10px 0;
                    font-size: 16px;
                `;

                saveJournalBtn.parentNode.insertBefore(savedMsg, saveJournalBtn.nextSibling);

                setTimeout(() => {
                    savedMsg.remove();
                }, 3000);
            } else {
                alert("请先写下你的感受");
            }
        }

        // 显示日记历史
        function showJournalHistory() {
            const entries = JSON.parse(localStorage.getItem('journalEntries') || []);
            if (entries.length > 0) {
                const historyHTML = entries.map(entry => `
                    <div style="margin-bottom:15px;padding:15px;background:rgba(255,255,255,0.7);border-radius:8px;">
                        <div style="color:#2c82c9;font-size:14px;margin-bottom:5px;">${entry.date}</div>
                        <div>${entry.content}</div>
                    </div>
                `).join('');

                // 显示历史记录弹窗
                const historyPopup = document.createElement('div');
                historyPopup.innerHTML = `
                    <div style="position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.5);z-index:1000;display:flex;justify-content:center;align-items:center;">
                        <div style="background:white;padding:20px;border-radius:10px;max-width:90%;max-height:80%;overflow:auto;width:500px;">
                            <h3 style="color:#2c82c9;margin-top:0;">你的情绪日记历史</h3>
                            <div style="max-height:400px;overflow-y:auto;padding-right:10px;">${historyHTML}</div>
                            <button onclick="this.parentNode.parentNode.remove()" style="margin-top:20px;padding:8px 16px;background:#95a5a6;color:white;border:none;border-radius:5px;">关闭</button>
                        </div>
                    </div>
                `;
                document.body.appendChild(historyPopup);
            } else {
                alert("你还没有保存过日记");
            }
        }

        // 返回主页
        function goBackToHome() {
            homeScreen.style.display = 'block';
            encouragementScreen.style.display = 'none';
            selfFeelScreen.style.display = 'none';
            meditationScreen.style.display = 'none';
            breathingScreen.style.display = 'none';
            journalScreen.style.display = 'none';
            questionsContainer.style.display = 'block';
            followupOptions.style.display = 'none';

            // 重置冥想状态
            if (startMeditationBtn) startMeditationBtn.style.display = 'block';
            if (meditationInterval) clearInterval(meditationInterval);

            // 重置呼吸练习状态
            if (startBreathingBtn) {
                startBreathingBtn.style.display = 'block';
                startBreathingBtn.textContent = "开始练习";
            }
            if (breathingInterval) clearInterval(breathingInterval);

            // 移除所有漂浮的单词和选中的单词
            document.querySelectorAll('.encouragement-word').forEach(word => word.remove());
            if (currentSelectedWord) {
                currentSelectedWord.remove();
                currentSelectedWord = null;
            }
        }

        // 启动游戏
        init();
    </script>
</body>

</html>
