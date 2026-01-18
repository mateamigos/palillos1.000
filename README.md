<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>SPACE MATH: LA PANDILLA DE LA REJILLA</title>
    <link href="https://fonts.googleapis.com/css2?family=Bungee&family=Rajdhani:wght@500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #ff9f43;
            --secondary: #ff6b6b;
            --accent: #48dbfb;
            --bg-dark: #1e272e;
            --success: #1dd1a1;
            --fail: #ee5253;
            --white: #ffffff;
            --retro-red: #ff0000;
        }

        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

        body { 
            font-family: 'Rajdhani', sans-serif; 
            background: radial-gradient(circle, #341f97 0%, #1e272e 100%);
            margin: 0; padding: 0;
            height: 100dvh; width: 100vw;
            display: flex; align-items: center; justify-content: center;
            overflow: hidden; 
            color: var(--white);
        }

        #explosion-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle, #ff9f43, #ff0000, transparent);
            opacity: 0; pointer-events: none; z-index: 999;
            transition: opacity 0.1s;
        }

        .container { 
            width: 95%; max-width: 800px;
            height: 95dvh; 
            background: rgba(255, 255, 255, 0.07);
            backdrop-filter: blur(15px);
            padding: 15px; border-radius: 30px; 
            border: 4px solid var(--primary);
            display: flex; flex-direction: column;
            position: relative;
            box-shadow: 0 0 40px rgba(255, 159, 67, 0.4);
        }

        #intro-screen, #final-screen {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: #2c3e50; border-radius: 26px;
            display: flex; flex-direction: column; align-items: center; 
            justify-content: center; z-index: 100; padding: 20px;
            text-align: center;
        }

        #intro-screen h1 { 
            font-family: 'Bungee', cursive; 
            color: var(--retro-red);
            font-size: clamp(1.5rem, 5vh, 2.5rem); 
            margin: 0 0 15px 0;
            text-shadow: 2px 2px 0px #000;
            animation: blinker 0.8s linear infinite;
        }

        @keyframes blinker { 50% { opacity: 0; } }

        .rules-box {
            background: rgba(255,255,255,0.1); 
            padding: 15px; border-radius: 20px;
            border: 2px solid var(--accent); 
            width: 100%; max-width: 450px;
            margin-bottom: 20px;
        }

        .rules-grid { display: flex; justify-content: center; gap: 15px; margin: 15px 0; }
        .rules-item img { height: 60px; filter: drop-shadow(0 0 5px var(--accent)); }
        .rules-item b { display: block; color: var(--accent); font-family: 'Bungee'; margin-top: 5px; }

        #feedback-msg {
            position: absolute;
            top: 50%; left: 50%;
            transform: translate(-50%, -50%);
            font-family: 'Bungee';
            font-size: clamp(2rem, 8vw, 4rem);
            text-align: center;
            z-index: 50;
            pointer-events: none;
            text-shadow: 4px 4px 0px rgba(0,0,0,0.5);
            display: none;
            width: 90%;
        }

        .timer-wrapper { display: flex; align-items: center; gap: 10px; margin: 5px 0; }
        .timer-container { flex: 1; height: 15px; background: #444; border-radius: 10px; position: relative; overflow: hidden; border: 2px solid #666; }
        #timer-bar { width: 100%; height: 100%; background: linear-gradient(90deg, #ff9f43, #ff0000); border-radius: 8px; transition: width 0.1s linear; }
        .bomb-icon { font-size: 1.5rem; filter: drop-shadow(0 0 5px red); }

        .game-board { 
            flex: 1; display: flex; flex-wrap: wrap; 
            justify-content: center; align-items: center;
            background: rgba(0, 0, 0, 0.4); border-radius: 20px;
            margin: 10px 0; overflow-y: auto; padding: 15px;
            position: relative;
        }

        /* TAMAÑO DE LOS PALILLOS AUMENTADO */
        .icon-img { 
            height: clamp(80px, 15vh, 130px); /* Antes era 50px a 80px */
            margin: 6px; 
            filter: drop-shadow(0 4px 6px rgba(0,0,0,0.4));
            animation: emerge 0.3s ease-out;
        }

        @keyframes emerge { from { transform: scale(0); opacity: 0; } to { transform: scale(1); opacity: 1; } }

        .options { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; padding: 5px; }
        button.opt-btn { 
            padding: 15px 5px; font-size: 1.5rem; font-family: 'Bungee'; 
            background: var(--white); border: none; border-radius: 12px; cursor: pointer;
            box-shadow: 0 5px 0px #bdc3c7; transition: 0.1s;
        }
        button.opt-btn.correct { background: var(--success) !important; color: white; box-shadow: 0 5px 0px #10ac84; }
        button.opt-btn.wrong { background: var(--fail) !important; color: white; box-shadow: 0 5px 0px #c0392b; }

        .btn-start {
            padding: 18px 40px; font-size: 1.3rem; font-family: 'Bungee'; color: white;
            background: var(--secondary); border: none; border-radius: 50px; cursor: pointer;
            box-shadow: 0 6px 0px #c0392b;
        }

        .explode-shake { animation: explode 0.5s cubic-bezier(.36,.07,.19,.97) both; }
        @keyframes explode {
            10%, 90% { transform: translate3d(-4px, 0, 0) scale(1.02); }
            20%, 80% { transform: translate3d(8px, 0, 0) scale(1.05); }
            30%, 50%, 70% { transform: translate3d(-12px, 0, 0) scale(1.1); filter: brightness(1.5); }
            40%, 60% { transform: translate3d(12px, 0, 0); }
        }

        .mini-legend { display: flex; justify-content: center; gap: 15px; font-size: 1rem; margin-bottom: 5px; font-family: 'Bungee'; }
        .mini-legend img { height: 25px; vertical-align: middle; }
    </style>
</head>
<body>

<div id="explosion-overlay"></div>

<div class="container" id="game-container">
    <div id="feedback-msg"></div>

    <div id="intro-screen">
        <h1>LA PANDILLA<br>DE LA REJILLA</h1>
        <div class="rules-box">
            <h2 style="font-family: 'Bungee'; font-size: 1rem; color: var(--primary); margin: 0;">DESACTIVA LA BOMBA</h2>
            <div class="rules-grid">
                <div class="rules-item"><img src="https://drive.google.com/thumbnail?id=1YftiIwD_skPJ4sb3GMfoK5-mploW4ePB&sz=200"><b>100</b></div>
                <div class="rules-item"><img src="https://drive.google.com/thumbnail?id=1mMQbdE5XS3MDKaM9t715xYmRjI1x2VYJ&sz=200"><b>10</b></div>
                <div class="rules-item"><img src="https://drive.google.com/thumbnail?id=1r0lqiJTM6OMBY5f38famu0OUxstljFwf&sz=200"><b>1</b></div>
            </div>
            <p style="font-weight: bold; padding: 0 10px;">
                Tienes 60 segundos para desconectar cada bomba.<br>
                Si desactivas la bomba <span style="color:var(--success)">+10</span> y si fallas <span style="color:var(--fail)">-5</span>.<br>
                ¿Cuántos puntos eres capaz de conseguir?
            </p>
        </div>
        <button class="btn-start" onclick="startGame()">INICIAR MISIÓN</button>
    </div>

    <div id="final-screen" style="display:none">
        <h1 id="end-status">MISIÓN FINALIZADA</h1>
        <p style="font-size: 1.2rem;">PUNTUACIÓN:</p>
        <p id="final-points" style="font-size: 5rem; color: var(--primary); font-family: 'Bungee'; margin: 10px 0;">0</p>
        <button class="btn-start" onclick="location.reload()">REINTENTAR</button>
    </div>

    <div class="stats-bar">
        <span>NIVEL: <span id="current-round">1</span>/30</span>
        <span>PTS: <span id="points">0</span></span>
    </div>

    <div class="timer-wrapper">
        <div class="timer-container"><div id="timer-bar"></div></div>
        <div class="bomb-icon">💣</div>
    </div>
    
    <div class="mini-legend">
        <span><img src="https://drive.google.com/thumbnail?id=1YftiIwD_skPJ4sb3GMfoK5-mploW4ePB&sz=50"> = 100</span>
        <span><img src="https://drive.google.com/thumbnail?id=1mMQbdE5XS3MDKaM9t715xYmRjI1x2VYJ&sz=50"> = 10</span>
        <span><img src="https://drive.google.com/thumbnail?id=1r0lqiJTM6OMBY5f38famu0OUxstljFwf&sz=50"> = 1</span>
    </div>

    <div class="game-board" id="board"></div>
    <div class="options" id="options-container"></div>
</div>

<script>
    const items = [
        { val: 100, img: 'https://drive.google.com/thumbnail?id=1YftiIwD_skPJ4sb3GMfoK5-mploW4ePB&sz=250' },
        { val: 10, img: 'https://drive.google.com/thumbnail?id=1mMQbdE5XS3MDKaM9t715xYmRjI1x2VYJ&sz=250' },
        { val: 1, img: 'https://drive.google.com/thumbnail?id=1r0lqiJTM6OMBY5f38famu0OUxstljFwf&sz=250' }
    ];

    let currentResult = 0, score = 0, round = 1, timeLeft = 60, timerInterval;
    const TIME_LIMIT = 60;
    const TOTAL_ROUNDS = 30;

    function startGame() {
        document.getElementById('intro-screen').style.display = 'none';
        initRound();
    }

    function initRound() {
        if (round > TOTAL_ROUNDS) return endGame();
        const board = document.getElementById('board');
        const optionsContainer = document.getElementById('options-container');
        const feedback = document.getElementById('feedback-msg');
        
        feedback.style.display = 'none';
        board.innerHTML = ''; 
        optionsContainer.innerHTML = '';
        currentResult = 0;

        let maxH = round > 15 ? 7 : (round > 5 ? 4 : 2); 
        const counts = {
            100: Math.floor(Math.random() * maxH) + 1,
            10: Math.floor(Math.random() * 10),
            1: Math.floor(Math.random() * 10)
        };

        let images = [];
        Object.keys(counts).forEach(val => {
            const item = items.find(i => i.val == val);
            for(let i = 0; i < counts[val]; i++) {
                currentResult += item.val;
                const img = document.createElement('img');
                img.src = item.img;
                img.className = 'icon-img';
                img.style.transform = `rotate(${Math.random() * 30 - 15}deg)`;
                images.push(img);
            }
        });
        
        images.sort(() => Math.random() - 0.5).forEach(img => board.appendChild(img));
        generateOptions(currentResult);
        resetTimer();
    }

    function generateOptions(correct) {
        let opts = new Set([correct]);
        while(opts.size < 4) {
            let diff = [1, -1, 10, -10, 100, -100, 2, -2][Math.floor(Math.random() * 8)];
            let n = Math.abs(correct + diff);
            if (n !== 0) opts.add(n);
        }
        Array.from(opts).sort((a, b) => a - b).forEach(opt => {
            const btn = document.createElement('button');
            btn.className = 'opt-btn';
            btn.innerText = opt;
            btn.onclick = () => checkAnswer(opt, btn);
            document.getElementById('options-container').appendChild(btn);
        });
    }

    function checkAnswer(sel, btn) {
        clearInterval(timerInterval);
        const allBtns = document.querySelectorAll('.opt-btn');
        const feedback = document.getElementById('feedback-msg');
        allBtns.forEach(b => b.style.pointerEvents = 'none');

        feedback.style.display = 'block';

        if(sel === currentResult) {
            if(btn) btn.classList.add('correct');
            score += 10;
            feedback.innerText = "¡BOMBA DESACTIVADA!";
            feedback.style.color = "var(--success)";
        } else {
            const overlay = document.getElementById('explosion-overlay');
            const container = document.getElementById('game-container');
            overlay.style.opacity = '1';
            container.classList.add('explode-shake');
            
            feedback.innerText = "¡BOOM!";
            feedback.style.color = "var(--retro-red)";

            if(btn) btn.classList.add('wrong');
            score = Math.max(0, score - 5);
            allBtns.forEach(b => { if(parseInt(b.innerText) === currentResult) b.classList.add('correct'); });

            setTimeout(() => {
                overlay.style.opacity = '0';
                container.classList.remove('explode-shake');
            }, 500);
        }

        document.getElementById('points').innerText = score;
        setTimeout(() => {
            round++;
            document.getElementById('current-round').innerText = Math.min(round, TOTAL_ROUNDS);
            initRound();
        }, 1500);
    }

    function resetTimer() {
        clearInterval(timerInterval);
        timeLeft = TIME_LIMIT;
        const bar = document.getElementById('timer-bar');
        bar.style.width = "100%";
        
        timerInterval = setInterval(() => {
            timeLeft -= 0.1;
            let percent = (timeLeft / TIME_LIMIT) * 100;
            bar.style.width = percent + "%";
            if (timeLeft <= 0) { 
                clearInterval(timerInterval); 
                checkAnswer(null, null);
            }
        }, 100);
    }

    function endGame() {
        document.getElementById('final-screen').style.display = 'flex';
        document.getElementById('final-points').innerText = score;
        document.getElementById('end-status').innerText = score > 200 ? "¡HÉROE DE LA REJILLA!" : "MISIÓN FINALIZADA";
    }
</script>

</body>
</html>
