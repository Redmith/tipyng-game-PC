<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Velocidad de Escritura</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .container {
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            padding: 40px;
            max-width: 800px;
            width: 100%;
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 30px;
            font-size: 2.5em;
            background: linear-gradient(45deg, #667eea, #764ba2);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .stat-card {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
            padding: 20px;
            border-radius: 15px;
            text-align: center;
            box-shadow: 0 10px 20px rgba(0,0,0,0.1);
        }

        .stat-value {
            font-size: 2em;
            font-weight: bold;
            margin-bottom: 5px;
        }

        .stat-label {
            font-size: 0.9em;
            opacity: 0.9;
        }

        .text-display {
            background: #f8f9fa;
            padding: 30px;
            border-radius: 15px;
            margin-bottom: 20px;
            font-size: 1.4em;
            line-height: 1.8;
            border: 3px solid #e9ecef;
            min-height: 120px;
            max-height: 200px;
            overflow: hidden;
            position: relative;
            display: flex;
            flex-wrap: wrap;
            align-content: flex-start;
        }

        .word {
            margin-right: 12px;
            margin-bottom: 5px;
            position: relative;
            padding: 4px 8px;
            border-radius: 6px;
            transition: all 0.3s ease;
            white-space: nowrap;
        }

        .word.correct {
            background-color: #d4edda;
            color: #155724;
        }

        .word.incorrect {
            background-color: #f8d7da;
            color: #721c24;
        }

        .word.current {
            background-color: #d1ecf1;
            border: 2px solid #17a2b8;
            transform: scale(1.05);
            box-shadow: 0 4px 12px rgba(23, 162, 184, 0.3);
            color: #0c5460;
            font-weight: bold;
        }

        .word.current.typing-correct {
            background-color: #d4edda;
            border-color: #28a745;
            color: #155724;
        }

        .word.current.typing-incorrect {
            background-color: #f8d7da;
            border-color: #dc3545;
            color: #721c24;
        }

        .word.upcoming {
            background-color: transparent;
            color: #6c757d;
        }

        .word.fade-out {
            opacity: 0;
            transform: scale(0.8);
            transition: all 0.5s ease;
        }

        .input-area {
            margin-bottom: 30px;
        }

        #textInput {
            width: 100%;
            padding: 20px;
            font-size: 1.2em;
            border: 3px solid #dee2e6;
            border-radius: 15px;
            outline: none;
            transition: all 0.3s ease;
        }

        #textInput:focus {
            border-color: #667eea;
            box-shadow: 0 0 20px rgba(102, 126, 234, 0.3);
        }

        .controls {
            display: flex;
            gap: 15px;
            justify-content: center;
            flex-wrap: wrap;
            align-items: center;
        }

        .toggle-container {
            display: flex;
            align-items: center;
            gap: 10px;
            background: rgba(255,255,255,0.8);
            padding: 10px 15px;
            border-radius: 20px;
            margin: 10px;
        }

        .toggle {
            position: relative;
            width: 50px;
            height: 25px;
            background: #ccc;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .toggle.active {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }

        .toggle-slider {
            position: absolute;
            top: 2px;
            left: 2px;
            width: 21px;
            height: 21px;
            background: white;
            border-radius: 50%;
            transition: all 0.3s ease;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }

        .toggle.active .toggle-slider {
            transform: translateX(25px);
        }

        .btn {
            padding: 15px 30px;
            font-size: 1.1em;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: bold;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .btn-secondary {
            background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%);
            color: #333;
        }

        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 15px 30px rgba(0,0,0,0.2);
        }

        .btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none;
        }

        .results {
            margin-top: 30px;
            padding: 20px;
            background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
            border-radius: 15px;
            text-align: center;
            display: none;
        }

        .results.show {
            display: block;
            animation: fadeIn 0.5s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .progress-bar {
            width: 100%;
            height: 8px;
            background: #e9ecef;
            border-radius: 4px;
            margin-bottom: 20px;
            overflow: hidden;
        }

        .progress {
            height: 100%;
            background: linear-gradient(90deg, #667eea, #764ba2);
            width: 0%;
            transition: width 0.3s ease;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 Velocidad de Escritura</h1>
        
        <div class="stats">
            <div class="stat-card">
                <div class="stat-value" id="wpm">0</div>
                <div class="stat-label">PPM</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="accuracy">100%</div>
                <div class="stat-label">Precisión</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="timer">60</div>
                <div class="stat-label">Segundos</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="errors">0</div>
                <div class="stat-label">Errores</div>
            </div>
        </div>

        <div class="progress-bar">
            <div class="progress" id="progress"></div>
        </div>

        <div class="text-display" id="textDisplay"></div>

        <div class="input-area">
            <input type="text" id="textInput" placeholder="Haz clic en 'Iniciar' y comienza a escribir..." disabled>
        </div>

        <div class="controls">
            <button class="btn btn-primary" id="startBtn">Preparar Juego</button>
            <button class="btn btn-secondary" id="resetBtn">Reiniciar</button>
            <div class="toggle-container">
                <span>Sin tildes:</span>
                <div class="toggle" id="noAccentsToggle">
                    <div class="toggle-slider"></div>
                </div>
            </div>
        </div>

        <div class="results" id="results">
            <h3>🎉 ¡Juego Terminado!</h3>
            <p id="finalStats"></p>
        </div>
    </div>

    <script>
        class TypingGame {
            constructor() {
                this.words = [
                    'casa', 'perro', 'gato', 'agua', 'fuego', 'tierra', 'cielo', 'amor', 'vida', 'tiempo',
                    'mundo', 'persona', 'trabajo', 'escuela', 'familia', 'amigo', 'comida', 'música', 'libro', 'coche',
                    'ciudad', 'país', 'montaña', 'playa', 'bosque', 'río', 'mar', 'sol', 'luna', 'estrella',
                    'flor', 'árbol', 'pájaro', 'color', 'blanco', 'negro', 'rojo', 'azul', 'verde', 'amarillo',
                    'grande', 'pequeño', 'alto', 'bajo', 'rápido', 'lento', 'fuerte', 'débil', 'caliente', 'frío',
                    'nuevo', 'viejo', 'bueno', 'malo', 'feliz', 'triste', 'fácil', 'difícil', 'importante', 'diferente',
                    'primero', 'último', 'mejor', 'peor', 'más', 'menos', 'mucho', 'poco', 'todo', 'nada',
                    'siempre', 'nunca', 'aquí', 'allí', 'cerca', 'lejos', 'dentro', 'fuera', 'arriba', 'abajo',
                    'corazón', 'canción', 'niño', 'año', 'mañana', 'español', 'papá', 'mamá', 'educación', 'comunicación'
                ];
                
                this.currentText = [];
                this.currentIndex = 0;
                this.startTime = null;
                this.timeLeft = 60;
                this.timer = null;
                this.isPlaying = false;
                this.gameReady = false;
                this.correctWords = 0;
                this.totalWords = 0;
                this.errors = 0;
                this.noAccents = false;
                this.correctChars = 0;

                this.initElements();
                this.initEventListeners();
                this.generateText();
            }

            initElements() {
                this.textDisplay = document.getElementById('textDisplay');
                this.textInput = document.getElementById('textInput');
                this.startBtn = document.getElementById('startBtn');
                this.resetBtn = document.getElementById('resetBtn');
                this.noAccentsToggle = document.getElementById('noAccentsToggle');
                this.wpmDisplay = document.getElementById('wpm');
                this.accuracyDisplay = document.getElementById('accuracy');
                this.timerDisplay = document.getElementById('timer');
                this.errorsDisplay = document.getElementById('errors');
                this.results = document.getElementById('results');
                this.progress = document.getElementById('progress');
            }

            initEventListeners() {
                this.startBtn.addEventListener('click', () => this.prepareGame());
                this.resetBtn.addEventListener('click', () => this.resetGame());
                this.noAccentsToggle.addEventListener('click', () => this.toggleAccents());
                this.textInput.addEventListener('input', (e) => this.handleInput(e));
                this.textInput.addEventListener('keydown', (e) => {
                    if (e.key === ' ') {
                        e.preventDefault();
                        this.handleSpace();
                    }
                });
            }

            generateText() {
                this.currentText = [];
                for (let i = 0; i < 50; i++) {
                    const randomWord = this.words[Math.floor(Math.random() * this.words.length)];
                    this.currentText.push(randomWord);
                }
                this.displayText();
            }

            displayText() {
                this.textDisplay.innerHTML = '';
                
                const wordsToShow = Math.min(15, this.currentText.length - this.currentIndex);
                
                for (let i = 0; i < wordsToShow; i++) {
                    const word = this.currentText[this.currentIndex + i];
                    const span = document.createElement('span');
                    const displayWord = this.noAccents ? this.removeAccents(word) : word;
                    span.textContent = displayWord;
                    span.className = 'word';
                    
                    if (i === 0) {
                        span.className += ' current';
                    } else {
                        span.className += ' upcoming';
                    }
                    
                    this.textDisplay.appendChild(span);
                }
            }

            removeAccents(text) {
                return text.normalize("NFD").replace(/[\u0300-\u036f]/g, "");
            }

            toggleAccents() {
                this.noAccents = !this.noAccents;
                this.noAccentsToggle.classList.toggle('active');
                if (!this.isPlaying) {
                    this.displayText();
                }
            }

            prepareGame() {
                this.gameReady = true;
                this.textInput.disabled = false;
                this.textInput.focus();
                this.textInput.placeholder = 'Comienza a escribir para iniciar el cronómetro...';
                this.startBtn.disabled = true;
                this.startBtn.textContent = 'Listo - Escribe para empezar';
                this.results.classList.remove('show');
            }

            startGame() {
                if (this.isPlaying) return;
                
                this.isPlaying = true;
                this.startTime = Date.now();
                this.startBtn.textContent = 'Jugando...';
                
                this.timer = setInterval(() => {
                    this.timeLeft--;
                    this.timerDisplay.textContent = this.timeLeft;
                    this.updateProgress();
                    
                    if (this.timeLeft <= 0) {
                        this.endGame();
                    }
                }, 1000);
            }

            handleInput(e) {
                if (!this.gameReady) return;
                
                if (!this.isPlaying && e.target.value.length === 1) {
                    this.startGame();
                }
                
                if (!this.isPlaying) return;

                const inputValue = e.target.value.trim();
                const currentWord = this.currentText[this.currentIndex];
                const targetWord = this.noAccents ? this.removeAccents(currentWord) : currentWord;
                
                const currentWordElement = this.textDisplay.querySelector('.word.current');
                if (currentWordElement) {
                    currentWordElement.classList.remove('typing-correct', 'typing-incorrect');
                    
                    if (inputValue === targetWord) {
                        currentWordElement.classList.add('typing-correct');
                        this.textInput.style.backgroundColor = '#d4edda';
                        this.textInput.style.borderColor = '#28a745';
                    } else if (targetWord.startsWith(inputValue) && inputValue.length > 0) {
                        this.textInput.style.backgroundColor = '#d1ecf1';
                        this.textInput.style.borderColor = '#17a2b8';
                    } else if (inputValue.length > 0) {
                        currentWordElement.classList.add('typing-incorrect');
                        this.textInput.style.backgroundColor = '#f8d7da';
                        this.textInput.style.borderColor = '#dc3545';
                    } else {
                        this.textInput.style.backgroundColor = '';
                        this.textInput.style.borderColor = '#667eea';
                    }
                }
            }

            handleSpace() {
                if (!this.isPlaying) return;

                const inputValue = this.textInput.value.trim();
                const currentWord = this.currentText[this.currentIndex];
                const targetWord = this.noAccents ? this.removeAccents(currentWord) : currentWord;

                const currentWordElement = this.textDisplay.querySelector('.word.current');
                
                this.totalWords++;
                
                if (inputValue === targetWord) {
                    this.correctWords++;
                    this.correctChars += targetWord.length;
                    if (currentWordElement) {
                        currentWordElement.classList.remove('current', 'typing-correct', 'typing-incorrect');
                        currentWordElement.classList.add('correct');
                    }
                } else {
                    this.errors++;
                    if (currentWordElement) {
                        currentWordElement.classList.remove('current', 'typing-correct', 'typing-incorrect');
                        currentWordElement.classList.add('incorrect');
                    }
                }

                this.textInput.value = '';
                this.textInput.style.backgroundColor = '';
                this.textInput.style.borderColor = '#667eea';
                
                this.currentIndex++;
                
                if (this.currentIndex >= this.currentText.length - 10) {
                    for (let i = 0; i < 20; i++) {
                        const randomWord = this.words[Math.floor(Math.random() * this.words.length)];
                        this.currentText.push(randomWord);
                    }
                }
                
                this.displayText();
                this.updateStats();
            }

            updateStats() {
                if (!this.startTime) return;
                
                const timeElapsed = (Date.now() - this.startTime) / 1000 / 60;
                const wpm = timeElapsed > 0 ? Math.round(this.correctWords / timeElapsed) : 0;
                const accuracy = this.totalWords > 0 ? Math.round((this.correctWords / this.totalWords) * 100) : 100;

                this.wpmDisplay.textContent = Math.max(0, wpm);
                this.accuracyDisplay.textContent = Math.max(0, Math.min(100, accuracy)) + '%';
                this.errorsDisplay.textContent = this.errors;
            }

            updateProgress() {
                const progressPercent = ((60 - this.timeLeft) / 60) * 100;
                this.progress.style.width = progressPercent + '%';
            }

            endGame() {
                this.isPlaying = false;
                clearInterval(this.timer);
                this.textInput.disabled = true;
                this.startBtn.disabled = false;
                this.startBtn.textContent = 'Preparar Juego';
                
                const finalWPM = this.wpmDisplay.textContent;
                const finalAccuracy = this.accuracyDisplay.textContent;
                
                this.results.querySelector('#finalStats').innerHTML = `
                    <strong>Velocidad:</strong> ${finalWPM} PPM<br>
                    <strong>Precisión:</strong> ${finalAccuracy}<br>
                    <strong>Errores:</strong> ${this.errors}<br>
                    <strong>Palabras correctas:</strong> ${this.correctWords}/${this.totalWords}<br>
                    ${this.getPerformanceMessage(parseInt(finalWPM))}
                `;
                
                this.results.classList.add('show');
            }

            getPerformanceMessage(wpm) {
                if (wpm >= 50) return '<br>🏆 ¡Excelente! Eres muy rápido.';
                if (wpm >= 35) return '<br>👍 ¡Muy bien! Buen ritmo.';
                if (wpm >= 20) return '<br>👌 Bien, sigue practicando.';
                return '<br>💪 ¡Sigue practicando para mejorar!';
            }

            resetGame() {
                this.isPlaying = false;
                this.gameReady = false;
                clearInterval(this.timer);
                this.currentIndex = 0;
                this.timeLeft = 60;
                this.correctWords = 0;
                this.totalWords = 0;
                this.errors = 0;
                this.correctChars = 0;
                
                this.textInput.value = '';
                this.textInput.disabled = true;
                this.textInput.style.backgroundColor = '';
                this.textInput.style.borderColor = '';
                this.textInput.placeholder = 'Haz clic en "Preparar Juego" para comenzar...';
                this.startBtn.disabled = false;
                this.startBtn.textContent = 'Preparar Juego';
                
                this.timerDisplay.textContent = '60';
                this.wpmDisplay.textContent = '0';
                this.accuracyDisplay.textContent = '100%';
                this.errorsDisplay.textContent = '0';
                this.progress.style.width = '0%';
                
                this.results.classList.remove('show');
                this.generateText();
            }
        }

        document.addEventListener('DOMContentLoaded', () => {
            new TypingGame();
        });
    </script>
</body>
</html>
