<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NEBULA STRIKE PRO MAX | Улучшенная версия</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
            font-family: 'Arial', sans-serif;
        }

        body {
            background: #000;
            overflow: hidden;
            color: white;
        }

        /* Основные контейнеры */
        #loadingScreen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #000;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 10000;
        }

        #authScreen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle at center, 
                rgba(0, 20, 40, 0.95) 0%, 
                rgba(0, 0, 20, 0.98) 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9000;
            backdrop-filter: blur(10px);
        }

        #mainMenu {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle at center, 
                rgba(0, 20, 40, 0.9) 0%, 
                rgba(0, 0, 20, 0.95) 100%);
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 8000;
            backdrop-filter: blur(10px);
        }

        #gameContainer {
            position: relative;
            width: 100vw;
            height: 100vh;
            overflow: hidden;
            display: none;
        }

        /* Canvas */
        canvas {
            position: absolute;
            top: 0;
            left: 0;
        }

        #gameCanvas {
            z-index: 1;
        }

        #effectsCanvas {
            z-index: 2;
        }

        #uiCanvas {
            z-index: 3;
            pointer-events: none;
        }

        /* Заголовки */
        .title {
            font-size: 4.5rem;
            background: linear-gradient(45deg, #00ffff, #0080ff, #ff00ff);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            margin-bottom: 2rem;
            text-shadow: 0 0 50px rgba(0, 255, 255, 0.5);
            animation: pulse 2s infinite, glow 3s infinite alternate;
            text-align: center;
            font-weight: 900;
            letter-spacing: 2px;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        @keyframes glow {
            0% { filter: hue-rotate(0deg); }
            100% { filter: hue-rotate(360deg); }
        }

        /* Кнопки */
        .btn {
            background: linear-gradient(45deg, #0066ff, #00ccff);
            border: none;
            color: white;
            padding: 1rem 2rem;
            font-size: 1.2rem;
            border-radius: 12px;
            margin: 0.8rem;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 1px;
            min-width: 280px;
            position: relative;
            overflow: hidden;
            font-weight: bold;
            border: 2px solid rgba(0, 255, 255, 0.3);
        }

        .btn:hover {
            transform: translateY(-5px) scale(1.05);
            box-shadow: 0 15px 30px rgba(0, 255, 255, 0.4);
            border-color: #00ffff;
        }

        .btn:active {
            transform: translateY(-2px) scale(1.02);
        }

        .btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
            transition: 0.5s;
        }

        .btn:hover::before {
            left: 100%;
        }

        .btn-admin {
            background: linear-gradient(45deg, #ff00ff, #ff0088);
            border-color: rgba(255, 0, 255, 0.3);
        }

        .btn-admin:hover {
            box-shadow: 0 15px 30px rgba(255, 0, 255, 0.4);
            border-color: #ff00ff;
        }

        /* Формы */
        .form-container {
            background: rgba(0, 20, 40, 0.95);
            padding: 2.5rem;
            border-radius: 20px;
            border: 3px solid #00ffff;
            width: 90%;
            max-width: 450px;
            backdrop-filter: blur(5px);
            box-shadow: 0 20px 50px rgba(0, 255, 255, 0.2);
        }

        .form-group {
            margin-bottom: 1.5rem;
        }

        .form-group label {
            display: block;
            margin-bottom: 0.5rem;
            color: #88aaff;
            font-size: 1.1rem;
        }

        .form-control {
            width: 100%;
            padding: 1rem;
            background: rgba(0, 30, 60, 0.8);
            border: 2px solid #00ffff;
            border-radius: 10px;
            color: white;
            font-size: 1.1rem;
            transition: all 0.3s;
        }

        .form-control:focus {
            outline: none;
            border-color: #00ccff;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
        }

        /* HUD */
        .hud {
            position: absolute;
            top: 20px;
            left: 20px;
            background: linear-gradient(135deg, 
                rgba(0, 10, 30, 0.9) 0%,
                rgba(0, 20, 40, 0.8) 100%);
            padding: 1.5rem;
            border-radius: 15px;
            border: 3px solid #00ffff;
            z-index: 100;
            backdrop-filter: blur(10px);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }

        .hud-item {
            margin: 1rem 0;
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .hud-icon {
            width: 30px;
            height: 30px;
            background: #00ffff;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
        }

        .stat-bar {
            width: 250px;
            height: 20px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            overflow: hidden;
            border: 2px solid rgba(255, 255, 255, 0.3);
            position: relative;
        }

        .stat-fill {
            height: 100%;
            transition: width 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
            position: relative;
            overflow: hidden;
        }

        .stat-fill::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(90deg, 
                transparent 0%, 
                rgba(255, 255, 255, 0.3) 50%, 
                transparent 100%);
            animation: shimmer 2s infinite;
        }

        @keyframes shimmer {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        .health-bar .stat-fill {
            background: linear-gradient(90deg, #ff0000, #ff5500, #ffff00);
        }

        .shield-bar .stat-fill {
            background: linear-gradient(90deg, #0066ff, #00aaff, #00ffff);
        }

        .energy-bar .stat-fill {
            background: linear-gradient(90deg, #00ff00, #aaff00, #ffff00);
        }

        /* Модальные окна */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.95);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 10000;
            backdrop-filter: blur(10px);
        }

        .modal-content {
            background: linear-gradient(135deg, 
                rgba(0, 20, 40, 0.95) 0%,
                rgba(0, 10, 30, 0.98) 100%);
            padding: 2.5rem;
            border-radius: 25px;
            border: 4px solid #00ffff;
            max-width: 900px;
            width: 90%;
            max-height: 85vh;
            overflow-y: auto;
            position: relative;
            box-shadow: 0 30px 80px rgba(0, 255, 255, 0.3);
        }

        /* Меню паузы */
        #pauseMenu {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, 
                rgba(0, 0, 50, 0.95) 0%,
                rgba(0, 20, 40, 0.98) 100%);
            padding: 3rem;
            border-radius: 25px;
            border: 4px solid #00ffff;
            display: none;
            z-index: 9000;
            backdrop-filter: blur(20px);
            text-align: center;
        }

        /* Уведомления */
        .notification {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: linear-gradient(135deg, 
                rgba(0, 20, 40, 0.95) 0%,
                rgba(0, 10, 30, 0.98) 100%);
            padding: 1.5rem 3rem;
            border-radius: 15px;
            border: 3px solid #00ffff;
            z-index: 10000;
            animation: slideDown 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
            font-size: 1.2rem;
            min-width: 400px;
            text-align: center;
            box-shadow: 0 10px 40px rgba(0, 255, 255, 0.3);
        }

        @keyframes slideDown {
            from { 
                top: -100px; 
                opacity: 0; 
                transform: translateX(-50%) scale(0.9);
            }
            to { 
                top: 20px; 
                opacity: 1; 
                transform: translateX(-50%) scale(1);
            }
        }

        .notification-success {
            border-color: #00ff88;
            background: linear-gradient(135deg, 
                rgba(0, 40, 20, 0.95) 0%,
                rgba(0, 30, 10, 0.98) 100%);
        }

        .notification-error {
            border-color: #ff0066;
            background: linear-gradient(135deg, 
                rgba(40, 0, 0, 0.95) 0%,
                rgba(30, 0, 0, 0.98) 100%);
        }

        .notification-admin {
            border-color: #ff00ff;
            background: linear-gradient(135deg, 
                rgba(40, 0, 40, 0.95) 0%,
                rgba(30, 0, 30, 0.98) 100%);
        }

        /* Загрузчик */
        .loader {
            width: 80px;
            height: 80px;
            border: 6px solid transparent;
            border-top: 6px solid #00ffff;
            border-right: 6px solid #0066ff;
            border-bottom: 6px solid #ff00ff;
            border-left: 6px solid #00ff88;
            border-radius: 50%;
            animation: spin 1.5s linear infinite;
            margin-bottom: 2rem;
            box-shadow: 0 0 30px rgba(0, 255, 255, 0.5);
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Аудио контролы */
        .audio-controls {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: rgba(0, 20, 40, 0.9);
            padding: 1rem;
            border-radius: 15px;
            border: 2px solid #00ffff;
            z-index: 100;
            display: flex;
            align-items: center;
            gap: 10px;
            backdrop-filter: blur(10px);
        }

        .audio-btn {
            background: none;
            border: none;
            color: #00ffff;
            font-size: 1.5rem;
            cursor: pointer;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s;
        }

        .audio-btn:hover {
            background: rgba(0, 255, 255, 0.2);
            transform: scale(1.1);
        }

        .volume-slider {
            width: 100px;
            height: 5px;
            -webkit-appearance: none;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 3px;
            outline: none;
        }

        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 20px;
            height: 20px;
            background: #00ffff;
            border-radius: 50%;
            cursor: pointer;
            box-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
        }

        /* Промокоды */
        .promo-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 1rem;
            margin: 2rem 0;
        }

        .promo-card {
            background: rgba(0, 30, 60, 0.7);
            border: 2px solid #ffff00;
            border-radius: 15px;
            padding: 1.5rem;
            text-align: center;
            transition: all 0.3s;
        }

        .promo-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(255, 255, 0, 0.3);
            border-color: #ffaa00;
        }

        .promo-code {
            font-size: 1.8rem;
            color: #ffff00;
            font-weight: bold;
            margin: 1rem 0;
            text-shadow: 0 0 10px rgba(255, 255, 0, 0.5);
        }

        /* Админ панель */
        .admin-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1rem;
            margin: 2rem 0;
        }

        .admin-card {
            background: rgba(255, 0, 255, 0.1);
            border: 2px solid #ff00ff;
            border-radius: 15px;
            padding: 1.5rem;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s;
        }

        .admin-card:hover {
            background: rgba(255, 0, 255, 0.2);
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(255, 0, 255, 0.3);
        }

        /* Планеты на фоне */
        .planet {
            position: absolute;
            border-radius: 50%;
            z-index: 0;
        }

        /* Адаптивность */
        @media (max-width: 768px) {
            .title {
                font-size: 2.5rem;
            }
            
            .hud {
                left: 10px;
                right: 10px;
                top: 10px;
            }
            
            .stat-bar {
                width: 180px;
            }
            
            .notification {
                min-width: 300px;
                padding: 1rem 2rem;
            }
        }
    </style>
</head>
<body>
    <!-- Экраны -->
    <div id="loadingScreen">
        <div class="loader"></div>
        <h2 style="color: #00ffff; margin-top: 2rem; font-size: 1.5rem;">Загрузка NEBULA STRIKE PRO MAX...</h2>
        <div style="color: #88aaff; margin-top: 1rem; text-align: center;">
            <div>Инициализация звуковой системы...</div>
            <div>Загрузка графики...</div>
            <div>Подготовка игрового мира...</div>
        </div>
    </div>

    <div id="authScreen">
        <h1 class="title">NEBULA STRIKE</h1>
        <h2 style="color: #88aaff; margin-bottom: 2rem; text-align: center;">PRO MAX EDITION</h2>
        <div class="form-container">
            <div style="display: flex; margin-bottom: 1.5rem; border-radius: 15px; overflow: hidden; border: 2px solid #00ffff;">
                <button class="btn" id="loginTabBtn" style="flex: 1; border-radius: 0; margin: 0; border: none;">Вход</button>
                <button class="btn" id="registerTabBtn" style="flex: 1; border-radius: 0; margin: 0; border: none;">Регистрация</button>
            </div>
            
            <form id="loginForm">
                <div class="form-group">
                    <label><i class="fas fa-user"></i> Логин:</label>
                    <input type="text" class="form-control" id="loginUsername" value="admin" placeholder="Введите логин">
                </div>
                <div class="form-group">
                    <label><i class="fas fa-lock"></i> Пароль:</label>
                    <input type="password" class="form-control" id="loginPassword" value="admin205" placeholder="Введите пароль">
                </div>
                <button type="submit" class="btn" style="width: 100%; margin-top: 1rem; font-size: 1.3rem;">
                    <i class="fas fa-rocket"></i> Войти в игру
                </button>
            </form>
            
            <form id="registerForm" style="display: none;">
                <div class="form-group">
                    <label><i class="fas fa-user-plus"></i> Логин:</label>
                    <input type="text" class="form-control" id="registerUsername" placeholder="Придумайте логин">
                </div>
                <div class="form-group">
                    <label><i class="fas fa-key"></i> Пароль:</label>
                    <input type="password" class="form-control" id="registerPassword" placeholder="Придумайте пароль">
                </div>
                <div class="form-group">
                    <label><i class="fas fa-key"></i> Повторите пароль:</label>
                    <input type="password" class="form-control" id="registerPasswordConfirm" placeholder="Повторите пароль">
                </div>
                <button type="submit" class="btn" style="width: 100%; margin-top: 1rem; font-size: 1.3rem;">
                    <i class="fas fa-user-check"></i> Зарегистрироваться
                </button>
            </form>
        </div>
        <div style="color: #88aaff; margin-top: 2rem; text-align: center;">
            <div>Для доступа к админ-панели используйте:</div>
            <div style="color: #00ffff; font-weight: bold;">Логин: admin | Пароль: admin205</div>
        </div>
    </div>

    <div id="mainMenu">
        <h1 class="title">NEBULA STRIKE</h1>
        <h2 style="color: #88aaff; margin-bottom: 2rem; text-align: center;">PRO MAX EDITION</h2>
        
        <div style="position: absolute; top: 20px; right: 20px; background: rgba(0,20,40,0.9); padding: 1rem; border-radius: 10px; border: 2px solid #00ffff;">
            <div style="color: #00ffff; font-size: 1.2rem; font-weight: bold;" id="menuUsername">Гость</div>
            <div style="color: #88aaff; font-size: 0.9rem;">Уровень: <span id="menuLevel">1</span></div>
            <div style="color: #88aaff; font-size: 0.9rem;">Кредиты: <span id="menuCredits">0</span></div>
        </div>
        
        <button class="btn" id="startGameBtn">
            <i class="fas fa-play"></i> Начать игру
        </button>
        <button class="btn" id="continueBtn" style="display: none;">
            <i class="fas fa-redo"></i> Продолжить
        </button>
        <button class="btn" id="promocodesBtn">
            <i class="fas fa-gift"></i> Промокоды
        </button>
        <button class="btn" id="shopBtn">
            <i class="fas fa-shopping-cart"></i> Магазин
        </button>
        <button class="btn btn-admin" id="adminPanelBtn" style="display: none;">
            <i class="fas fa-crown"></i> Админ панель
        </button>
        <button class="btn" id="logoutBtn" style="background: linear-gradient(45deg, #ff0066, #ff0000);">
            <i class="fas fa-sign-out-alt"></i> Выйти
        </button>
        
        <div style="color: #88aaff; margin-top: 3rem; text-align: center; max-width: 800px;">
            <div style="font-size: 1.1rem; margin-bottom: 1rem;">Управление:</div>
            <div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 1rem; text-align: left;">
                <div><i class="fas fa-arrow-up"></i> W/Стрелка вверх - Движение вверх</div>
                <div><i class="fas fa-arrow-down"></i> S/Стрелка вниз - Движение вниз</div>
                <div><i class="fas fa-arrow-left"></i> A/Стрелка влево - Движение влево</div>
                <div><i class="fas fa-arrow-right"></i> D/Стрелка вправо - Движение вправо</div>
                <div><i class="fas fa-space-shuttle"></i> Пробел/ЛКМ - Стрельба</div>
                <div><i class="fas fa-pause"></i> ESC/P - Пауза</div>
                <div><i class="fas fa-music"></i> M - Музыка вкл/выкл</div>
                <div><i class="fas fa-crown"></i> Ctrl+Alt+A - Админ панель</div>
            </div>
        </div>
    </div>

    <!-- Игровой контейнер -->
    <div id="gameContainer">
        <canvas id="gameCanvas"></canvas>
        <canvas id="effectsCanvas"></canvas>
        <canvas id="uiCanvas"></canvas>
        
        <!-- HUD -->
        <div class="hud">
            <div class="hud-item">
                <div class="hud-icon" style="background: #00ffff;">👤</div>
                <span style="font-size: 1.2rem; color: #00ffff;" id="hudUsername">Гость</span>
            </div>
            <div class="hud-item">
                <div class="hud-icon" style="background: #ffff00;">⭐</div>
                <div>
                    <div style="color: #ffff00;">Очки: <span id="hudScore" style="font-weight: bold;">0</span></div>
                    <div style="color: #88aaff; font-size: 0.9rem;">Уровень: <span id="hudLevel">1</span></div>
                </div>
            </div>
            <div class="hud-item">
                <div class="hud-icon" style="background: #ff0000;">❤️</div>
                <div>
                    <div style="color: #ff0000;">Здоровье</div>
                    <div class="stat-bar health-bar">
                        <div class="stat-fill" id="healthBar" style="width: 100%"></div>
                    </div>
                </div>
            </div>
            <div class="hud-item">
                <div class="hud-icon" style="background: #0066ff;">🛡️</div>
                <div>
                    <div style="color: #0066ff;">Щит</div>
                    <div class="stat-bar shield-bar">
                        <div class="stat-fill" id="shieldBar" style="width: 100%"></div>
                    </div>
                </div>
            </div>
            <div class="hud-item">
                <div class="hud-icon" style="background: #00ff00;">⚡</div>
                <div>
                    <div style="color: #00ff00;">Энергия</div>
                    <div class="stat-bar energy-bar">
                        <div class="stat-fill" id="energyBar" style="width: 100%"></div>
                    </div>
                </div>
            </div>
            <div class="hud-item">
                <div class="hud-icon" style="background: #ffaa00;">💰</div>
                <div style="color: #ffaa00; font-size: 1.2rem; font-weight: bold;">
                    Кредиты: <span id="hudCredits">0</span>
                </div>
            </div>
        </div>
        
        <!-- Контролы звука -->
        <div class="audio-controls">
            <button class="audio-btn" id="prevTrackBtn">⏮️</button>
            <button class="audio-btn" id="playPauseBtn">▶️</button>
            <button class="audio-btn" id="nextTrackBtn">⏭️</button>
            <input type="range" class="volume-slider" id="volumeSlider" min="0" max="100" value="70">
            <button class="audio-btn" id="muteBtn">🔊</button>
        </div>
    </div>

    <!-- Меню паузы -->
    <div id="pauseMenu">
        <h2 style="color: #00ffff; margin-bottom: 2rem; font-size: 3rem; text-shadow: 0 0 20px #00ffff;">ПАУЗА</h2>
        <button class="btn" id="resumeBtn" style="font-size: 1.5rem; padding: 1.5rem 3rem;">
            <i class="fas fa-play"></i> Продолжить игру
        </button>
        <button class="btn" id="toMainMenuBtn" style="font-size: 1.5rem; padding: 1.5rem 3rem; margin-top: 2rem;">
            <i class="fas fa-home"></i> Главное меню
        </button>
    </div>

    <!-- Модальные окна -->
    <div id="adminModal" class="modal">
        <div class="modal-content">
            <h2 style="color: #ff00ff; margin-bottom: 1.5rem; text-align: center; font-size: 2.5rem;">
                <i class="fas fa-crown"></i> АДМИН ПАНЕЛЬ
            </h2>
            <div style="color: #ffaaff; margin-bottom: 2rem; text-align: center; font-size: 1.2rem;">
                Добро пожаловать в админ панель! Здесь вы можете управлять игрой
            </div>
            
            <div class="admin-grid">
                <div class="admin-card" id="godModeBtn">
                    <div style="font-size: 2rem;">👑</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">Бессмертие</div>
                    <div style="color: #ffaaff;">ВКЛ/ВЫКЛ</div>
                </div>
                
                <div class="admin-card" id="unlimitedAmmoBtn">
                    <div style="font-size: 2rem;">∞</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">Беск. патроны</div>
                    <div style="color: #ffaaff;">ВКЛ/ВЫКЛ</div>
                </div>
                
                <div class="admin-card" id="addCreditsBtn">
                    <div style="font-size: 2rem;">💰</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">+1000 кредитов</div>
                    <div style="color: #ffaaff;">Начислить</div>
                </div>
                
                <div class="admin-card" id="addScoreBtn">
                    <div style="font-size: 2rem;">⭐</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">+1000 очков</div>
                    <div style="color: #ffaaff;">Начислить</div>
                </div>
                
                <div class="admin-card" id="spawnBossBtn">
                    <div style="font-size: 2rem;">👾</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">Вызвать босса</div>
                    <div style="color: #ffaaff;">Спавн босса</div>
                </div>
                
                <div class="admin-card" id="clearEnemiesBtn">
                    <div style="font-size: 2rem;">🧹</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">Очистить врагов</div>
                    <div style="color: #ffaaff;">Удалить всех</div>
                </div>
                
                <div class="admin-card" id="healPlayerBtn">
                    <div style="font-size: 2rem;">❤️</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">Полное лечение</div>
                    <div style="color: #ffaaff;">Восстановить</div>
                </div>
                
                <div class="admin-card" id="maxStatsBtn">
                    <div style="font-size: 2rem;">⚡</div>
                    <div style="color: #ff00ff; font-weight: bold; margin: 0.5rem 0;">Макс. статы</div>
                    <div style="color: #ffaaff;">Установить</div>
                </div>
            </div>
            
            <div style="margin: 2rem 0;">
                <div style="color: #00ffff; margin-bottom: 0.5rem;">Командная строка:</div>
                <input type="text" class="form-control" id="adminCommand" placeholder="Введите команду (например: spawn enemy 10)">
                <button class="btn btn-admin" id="executeCommandBtn" style="width: 100%; margin-top: 1rem; font-size: 1.2rem;">
                    <i class="fas fa-terminal"></i> Выполнить команду
                </button>
            </div>
            
            <button class="btn" id="closeAdminBtn" style="width: 100%; font-size: 1.2rem;">
                <i class="fas fa-times"></i> Закрыть админ панель
            </button>
        </div>
    </div>

    <div id="promocodesModal" class="modal">
        <div class="modal-content">
            <h2 style="color: #ffff00; margin-bottom: 1.5rem; text-align: center; font-size: 2.5rem;">
                <i class="fas fa-gift"></i> ПРОМОКОДЫ
            </h2>
            <div style="color: #ffaa00; margin-bottom: 2rem; text-align: center; font-size: 1.2rem;">
                Введите промокод для получения бонусов. Каждый промокод можно использовать один раз
            </div>
            
            <div style="display: flex; gap: 1rem; margin-bottom: 2rem;">
                <input type="text" class="form-control" id="promoInput" placeholder="Введите промокод..." style="flex: 1;">
                <button class="btn" id="activatePromoBtn" style="min-width: 150px;">
                    <i class="fas fa-bolt"></i> Активировать
                </button>
            </div>
            
            <div id="promoMessage" style="margin: 1rem 0; padding: 1rem; border-radius: 10px; display: none; font-size: 1.2rem;"></div>
            
            <h3 style="color: #00ffff; margin: 2rem 0 1rem 0; text-align: center;">Доступные промокоды:</h3>
            <div class="promo-grid" id="promoList"></div>
            
            <button class="btn" id="closePromoBtn" style="width: 100%; margin-top: 2rem; font-size: 1.2rem;">
                <i class="fas fa-times"></i> Закрыть
            </button>
        </div>
    </div>

    <div id="shopModal" class="modal">
        <div class="modal-content">
            <h2 style="color: #00ff88; margin-bottom: 1.5rem; text-align: center; font-size: 2.5rem;">
                <i class="fas fa-shopping-cart"></i> КОСМИЧЕСКИЙ МАГАЗИН
            </h2>
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 2rem; padding: 1.5rem; background: rgba(0,30,60,0.5); border-radius: 15px; border: 2px solid #00ff88;">
                <div style="font-size: 1.5rem; color: #00ff88;">
                    <i class="fas fa-coins"></i> Ваши кредиты: 
                    <span id="shopCredits" style="color: #ffff00; font-weight: bold;">0</span>
                </div>
                <button class="btn" id="buyCreditsBtn" style="background: linear-gradient(45deg, #ffaa00, #ffff00);">
                    <i class="fas fa-plus"></i> Купить кредиты
                </button>
            </div>
            
            <div class="promo-grid" id="shopItems"></div>
            
            <button class="btn" id="closeShopBtn" style="width: 100%; margin-top: 2rem; font-size: 1.2rem;">
                <i class="fas fa-times"></i> Закрыть магазин
            </button>
        </div>
    </div>

    <!-- Аудио элементы -->
    <audio id="backgroundMusic" loop>
        <source src="https://assets.mixkit.co/music/preview/mixkit-game-level-music-689.mp3" type="audio/mpeg">
    </audio>
    
    <!-- Звуковые эффекты -->
    <audio id="laserSound" preload="auto"></audio>
    <audio id="explosionSound" preload="auto"></audio>
    <audio id="powerupSound" preload="auto"></audio>
    <audio id="damageSound" preload="auto"></audio>
    <audio id="bossSound" preload="auto"></audio>
    <audio id="clickSound" preload="auto"></audio>

    <!-- Font Awesome -->
    <script src="https://kit.fontawesome.com/a076d05399.js" crossorigin="anonymous"></script>

    <script>
        // ==================== ГЛОБАЛЬНЫЕ ПЕРЕМЕННЫЕ ====================
        let canvas, ctx, effectsCanvas, effectsCtx, uiCanvas, uiCtx;
        let gameState = 'loading';
        let currentUser = null;
        let isAdmin = false;
        
        // Игровые данные
        let score = 0;
        let credits = 100;
        let health = 100;
        let shield = 100;
        let energy = 100;
        let level = 1;
        let xp = 0;
        let xpToNextLevel = 100;
        
        // Игровые объекты
        let player = null;
        let enemies = [];
        let bullets = [];
        let powerups = [];
        let particles = [];
        let stars = [];
        let nebulas = [];
        let planets = [];
        let boss = null;
        
        // Управление
        let keys = {};
        let mouse = { x: 0, y: 0, down: false };
        
        // Аудио
        let audioContext;
        let musicVolume = 0.7;
        let sfxVolume = 0.8;
        let isMusicMuted = false;
        let isSfxMuted = false;
        let currentTrack = 0;
        const musicTracks = [
            'https://assets.mixkit.co/music/preview/mixkit-game-level-music-689.mp3',
            'https://assets.mixkit.co/music/preview/mixkit-space-game-668.mp3',
            'https://assets.mixkit.co/music/preview/mixkit-driving-ambition-32.mp3'
        ];
        
        // Читы
        let godMode = false;
        let unlimitedAmmo = false;
        let oneShotKill = false;
        
        // Промокоды
        const promoCodes = [
            { code: 'WELCOME2024', credits: 500, xp: 100, health: 50, description: 'Добро пожаловать в игру!' },
            { code: 'SPACEPOWER', credits: 1000, score: 1000, shield: 100, description: 'Космическая сила' },
            { code: 'NEBULASTAR', credits: 2000, level: 2, energy: 100, description: 'Звездная мощь' },
            { code: 'GALAXYHERO', credits: 5000, health: 100, shield: 100, description: 'Герой галактики' },
            { code: 'ADMINLOVE', credits: 10000, score: 10000, description: 'Секретный код админа' }
        ];
        
        let usedPromoCodes = [];
        
        // Магазин
        const shopItems = [
            { id: 1, name: 'Улучшение лазера', price: 500, type: 'weapon', description: '+20% урона' },
            { id: 2, name: 'Усиленный щит', price: 800, type: 'shield', description: '+30% защиты' },
            { id: 3, name: 'Энергореактор', price: 1200, type: 'energy', description: '+50% энергии' },
            { id: 4, name: 'Турбоускоритель', price: 1500, type: 'speed', description: '+25% скорости' },
            { id: 5, name: 'Авто-ремонт', price: 2000, type: 'repair', description: 'Автоматическое лечение' },
            { id: 6, name: 'Квантовая пушка', price: 5000, type: 'weapon', description: 'Супер-оружие' }
        ];
        
        // ==================== ИНИЦИАЛИЗАЦИЯ ====================
        function init() {
            console.log('🚀 Инициализация NEBULA STRIKE PRO MAX...');
            
            // Инициализация Canvas
            canvas = document.getElementById('gameCanvas');
            ctx = canvas.getContext('2d');
            effectsCanvas = document.getElementById('effectsCanvas');
            effectsCtx = effectsCanvas.getContext('2d');
            uiCanvas = document.getElementById('uiCanvas');
            uiCtx = uiCanvas.getContext('2d');
            
            resizeCanvas();
            
            // Создание фона
            createBackground();
            
            // Загрузка данных
            loadGameData();
            
            // Инициализация аудио
            initAudioSystem();
            
            // Показываем экран авторизации
            setTimeout(() => {
                document.getElementById('loadingScreen').style.display = 'none';
                document.getElementById('authScreen').style.display = 'flex';
                gameState = 'auth';
                showNotification('Добро пожаловать в NEBULA STRIKE PRO MAX!', 'success');
            }, 1500);
            
            // Запуск игрового цикла
            gameLoop();
        }
        
        function resizeCanvas() {
            const width = window.innerWidth;
            const height = window.innerHeight;
            
            canvas.width = width;
            canvas.height = height;
            effectsCanvas.width = width;
            effectsCanvas.height = height;
            uiCanvas.width = width;
            uiCanvas.height = height;
        }
        
        function createBackground() {
            // Создание звезд
            stars = [];
            for (let i = 0; i < 300; i++) {
                stars.push({
                    x: Math.random() * canvas.width,
                    y: Math.random() * canvas.height,
                    size: Math.random() * 4 + 1,
                    speed: Math.random() * 0.8 + 0.2,
                    brightness: Math.random() * 0.8 + 0.2,
                    twinkle: Math.random() * 0.05
                });
            }
            
            // Создание туманностей
            nebulas = [];
            for (let i = 0; i < 3; i++) {
                nebulas.push({
                    x: Math.random() * canvas.width,
                    y: Math.random() * canvas.height,
                    radius: Math.random() * 200 + 100,
                    color: `rgba(${Math.floor(Math.random() * 100)}, ${Math.floor(Math.random() * 100)}, ${Math.floor(Math.random() * 255)}, 0.1)`,
                    pulse: Math.random() * 0.02
                });
            }
            
            // Создание планет
            planets = [];
            for (let i = 0; i < 2; i++) {
                planets.push({
                    x: Math.random() * canvas.width,
                    y: Math.random() * canvas.height,
                    radius: Math.random() * 80 + 40,
                    color: getRandomPlanetColor(),
                    speed: Math.random() * 0.3 + 0.1,
                    orbit: Math.random() * 100
                });
            }
        }
        
        function getRandomPlanetColor() {
            const colors = [
                '#ff6b6b', '#4ecdc4', '#45b7d1', '#96ceb4', '#feca57',
                '#ff9ff3', '#54a0ff', '#5f27cd', '#00d2d3', '#ff9f43'
            ];
            return colors[Math.floor(Math.random() * colors.length)];
        }
        
        function loadGameData() {
            // Загрузка пользователей
            if (!localStorage.getItem('nebulaUsers')) {
                localStorage.setItem('nebulaUsers', JSON.stringify({}));
            }
            
            // Загрузка использованных промокодов
            if (localStorage.getItem('usedPromoCodes')) {
                usedPromoCodes = JSON.parse(localStorage.getItem('usedPromoCodes'));
            }
            
            // Загрузка настроек
            const settings = localStorage.getItem('gameSettings');
            if (settings) {
                const saved = JSON.parse(settings);
                musicVolume = saved.musicVolume || 0.7;
                sfxVolume = saved.sfxVolume || 0.8;
            }
        }
        
        // ==================== АУДИО СИСТЕМА ====================
        function initAudioSystem() {
            try {
                audioContext = new (window.AudioContext || window.webkitAudioContext)();
                
                // Инициализация звуковых эффектов
                initSoundEffects();
                
                // Настройка фоновой музыки
                const music = document.getElementById('backgroundMusic');
                music.volume = musicVolume;
                
                // Автовоспроизведение
                music.play().catch(e => {
                    console.log("Автовоспроизведение заблокировано, музыка запустится при взаимодействии");
                });
                
                console.log('✅ Аудио система инициализирована');
            } catch (e) {
                console.log('❌ Web Audio API не поддерживается');
            }
        }
        
        function initSoundEffects() {
            // Создание звуковых эффектов через Web Audio API
            // Лазерный выстрел
            createLaserSound();
            
            // Взрыв
            createExplosionSound();
            
            // Бонус
            createPowerupSound();
            
            // Урон
            createDamageSound();
            
            // Босс
            createBossSound();
        }
        
        function createLaserSound() {
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);
            
            oscillator.frequency.setValueAtTime(800, audioContext.currentTime);
            oscillator.frequency.exponentialRampToValueAtTime(200, audioContext.currentTime + 0.1);
            
            gainNode.gain.setValueAtTime(0.3 * sfxVolume, audioContext.currentTime);
            gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.1);
            
            oscillator.start();
            oscillator.stop(audioContext.currentTime + 0.1);
        }
        
        function createExplosionSound() {
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);
            
            oscillator.frequency.setValueAtTime(100, audioContext.currentTime);
            oscillator.frequency.exponentialRampToValueAtTime(50, audioContext.currentTime + 0.5);
            
            gainNode.gain.setValueAtTime(0.5 * sfxVolume, audioContext.currentTime);
            gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.5);
            
            oscillator.start();
            oscillator.stop(audioContext.currentTime + 0.5);
        }
        
        function createPowerupSound() {
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);
            
            // Мелодия powerup
            oscillator.frequency.setValueAtTime(523.25, audioContext.currentTime); // До
            oscillator.frequency.setValueAtTime(659.25, audioContext.currentTime + 0.1); // Ми
            oscillator.frequency.setValueAtTime(783.99, audioContext.currentTime + 0.2); // Соль
            
            gainNode.gain.setValueAtTime(0, audioContext.currentTime);
            gainNode.gain.linearRampToValueAtTime(0.3 * sfxVolume, audioContext.currentTime + 0.05);
            gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.3);
            
            oscillator.start();
            oscillator.stop(audioContext.currentTime + 0.3);
        }
        
        function createDamageSound() {
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);
            
            oscillator.frequency.setValueAtTime(300, audioContext.currentTime);
            oscillator.frequency.exponentialRampToValueAtTime(150, audioContext.currentTime + 0.2);
            
            gainNode.gain.setValueAtTime(0.4 * sfxVolume, audioContext.currentTime);
            gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.2);
            
            oscillator.start();
            oscillator.stop(audioContext.currentTime + 0.2);
        }
        
        function createBossSound() {
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);
            
            oscillator.frequency.setValueAtTime(80, audioContext.currentTime);
            oscillator.frequency.exponentialRampToValueAtTime(60, audioContext.currentTime + 1);
            
            gainNode.gain.setValueAtTime(0.6 * sfxVolume, audioContext.currentTime);
            gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 1);
            
            oscillator.start();
            oscillator.stop(audioContext.currentTime + 1);
        }
        
        function playSound(type) {
            if (isSfxMuted || !audioContext) return;
            
            try {
                switch(type) {
                    case 'laser':
                        createLaserSound();
                        break;
                    case 'explosion':
                        createExplosionSound();
                        break;
                    case 'powerup':
                        createPowerupSound();
                        break;
                    case 'damage':
                        createDamageSound();
                        break;
                    case 'boss':
                        createBossSound();
                        break;
                }
            } catch (e) {
                console.log('Ошибка воспроизведения звука:', e);
            }
        }
        
        // ==================== ГРАФИКА И МОДЕЛИ ====================
        function drawPlayer(x, y, width, height) {
            // Сохраняем контекст
            ctx.save();
            
            // Центрируем
            const centerX = x + width / 2;
            const centerY = y + height / 2;
            
            // Основной корпус
            ctx.fillStyle = '#00ffff';
            ctx.beginPath();
            ctx.moveTo(centerX, y);
            ctx.lineTo(x + width, y + height * 0.7);
            ctx.lineTo(centerX, y + height);
            ctx.lineTo(x, y + height * 0.7);
            ctx.closePath();
            ctx.fill();
            
            // Детали корпуса
            ctx.fillStyle = '#00aaff';
            ctx.beginPath();
            ctx.ellipse(centerX, y + height * 0.3, width * 0.3, height * 0.2, 0, 0, Math.PI * 2);
            ctx.fill();
            
            // Кабина
            ctx.fillStyle = '#88eeff';
            ctx.beginPath();
            ctx.arc(centerX, y + height * 0.4, width * 0.2, 0, Math.PI * 2);
            ctx.fill();
            
            // Двигатели
            ctx.fillStyle = '#ffff00';
            ctx.beginPath();
            ctx.rect(x + width * 0.2, y + height * 0.8, width * 0.6, height * 0.2);
            ctx.fill();
            
            // Анимация двигателей
            if (keys['ArrowUp'] || keys['w'] || keys['W']) {
                effectsCtx.fillStyle = '#ffaa00';
                effectsCtx.beginPath();
                effectsCtx.ellipse(centerX, y + height, width * 0.4, height * 0.3, 0, 0, Math.PI * 2);
                effectsCtx.fill();
                
                effectsCtx.fillStyle = '#ffff00';
                effectsCtx.beginPath();
                effectsCtx.ellipse(centerX, y + height + 20, width * 0.3, height * 0.4, 0, 0, Math.PI * 2);
                effectsCtx.fill();
            }
            
            ctx.restore();
        }
        
        function drawEnemy(x, y, width, height, type = 0) {
            ctx.save();
            
            const centerX = x + width / 2;
            const centerY = y + height / 2;
            
            // Разные типы врагов
            switch(type) {
                case 0: // Базовый враг
                    ctx.fillStyle = '#ff4444';
                    ctx.beginPath();
                    ctx.moveTo(centerX, y);
                    ctx.lineTo(x + width, centerY);
                    ctx.lineTo(centerX, y + height);
                    ctx.lineTo(x, centerY);
                    ctx.closePath();
                    ctx.fill();
                    break;
                    
                case 1: // Быстрый враг
                    ctx.fillStyle = '#44ff44';
                    ctx.beginPath();
                    ctx.rect(x, y, width, height);
                    ctx.fill();
                    
                    // Детали
                    ctx.fillStyle = '#00ff00';
                    ctx.beginPath();
                    ctx.arc(centerX, centerY, width * 0.3, 0, Math.PI * 2);
                    ctx.fill();
                    break;
                    
                case 2: // Танк
                    ctx.fillStyle = '#4444ff';
                    ctx.beginPath();
                    ctx.arc(centerX, centerY, width / 2, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // Броня
                    ctx.strokeStyle = '#8888ff';
                    ctx.lineWidth = 4;
                    ctx.beginPath();
                    ctx.arc(centerX, centerY, width / 2 - 2, 0, Math.PI * 2);
                    ctx.stroke();
                    break;
            }
            
            ctx.restore();
        }
        
        function drawBoss(x, y, width, height) {
            ctx.save();
            
            const centerX = x + width / 2;
            const centerY = y + height / 2;
            
            // Основное тело
            const gradient = ctx.createRadialGradient(centerX, centerY, 0, centerX, centerY, width / 2);
            gradient.addColorStop(0, '#ff00ff');
            gradient.addColorStop(1, '#990099');
            
            ctx.fillStyle = gradient;
            ctx.beginPath();
            ctx.arc(centerX, centerY, width / 2, 0, Math.PI * 2);
            ctx.fill();
            
            // Глаза
            ctx.fillStyle = '#ffff00';
            ctx.beginPath();
            ctx.arc(centerX - width * 0.2, centerY - height * 0.1, width * 0.1, 0, Math.PI * 2);
            ctx.arc(centerX + width * 0.2, centerY - height * 0.1, width * 0.1, 0, Math.PI * 2);
            ctx.fill();
            
            // Рот
            ctx.fillStyle = '#ff0000';
            ctx.beginPath();
            ctx.arc(centerX, centerY + height * 0.2, width * 0.15, 0, Math.PI);
            ctx.fill();
            
            // Оружие
            ctx.fillStyle = '#ff4444';
            ctx.beginPath();
            ctx.rect(centerX - width * 0.4, centerY - height * 0.3, width * 0.8, height * 0.1);
            ctx.fill();
            
            // Анимация пульсации
            const pulse = Math.sin(Date.now() * 0.005) * 5;
            ctx.strokeStyle = `rgba(255, 0, 255, ${0.5 + Math.sin(Date.now() * 0.01) * 0.5})`;
            ctx.lineWidth = 4 + pulse;
            ctx.beginPath();
            ctx.arc(centerX, centerY, width / 2 + pulse, 0, Math.PI * 2);
            ctx.stroke();
            
            ctx.restore();
        }
        
        function drawPowerup(x, y, width, height, type) {
            ctx.save();
            
            const centerX = x + width / 2;
            const centerY = y + height / 2;
            
            // Мерцание
            const alpha = 0.7 + Math.sin(Date.now() * 0.01) * 0.3;
            
            // Внешний круг
            ctx.fillStyle = type === 'health' ? `rgba(255, 0, 0, ${alpha})` :
                            type === 'shield' ? `rgba(0, 100, 255, ${alpha})` :
                            `rgba(255, 255, 0, ${alpha})`;
            
            ctx.beginPath();
            ctx.arc(centerX, centerY, width / 2, 0, Math.PI * 2);
            ctx.fill();
            
            // Внутренний круг
            ctx.fillStyle = type === 'health' ? `rgba(255, 100, 100, ${alpha})` :
                            type === 'shield' ? `rgba(100, 200, 255, ${alpha})` :
                            `rgba(255, 255, 100, ${alpha})`;
            
            ctx.beginPath();
            ctx.arc(centerX, centerY, width / 3, 0, Math.PI * 2);
            ctx.fill();
            
            // Иконка
            ctx.fillStyle = '#ffffff';
            ctx.font = `${width / 2}px Arial`;
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            
            const icon = type === 'health' ? '❤️' : 
                        type === 'shield' ? '🛡️' : '💰';
            
            ctx.fillText(icon, centerX, centerY);
            
            ctx.restore();
        }
        
        function drawBullet(x, y, width, height) {
            ctx.save();
            
            // Тело пули
            const gradient = ctx.createLinearGradient(x, y, x, y + height);
            gradient.addColorStop(0, '#ffff00');
            gradient.addColorStop(1, '#ffaa00');
            
            ctx.fillStyle = gradient;
            ctx.fillRect(x - width / 2, y, width, height);
            
            // Свечение
            effectsCtx.fillStyle = 'rgba(255, 255, 0, 0.3)';
            effectsCtx.beginPath();
            effectsCtx.arc(x, y, width * 2, 0, Math.PI * 2);
            effectsCtx.fill();
            
            ctx.restore();
        }
        
        function drawParticle(x, y, size, color, life) {
            effectsCtx.save();
            
            effectsCtx.globalAlpha = life;
            
            // Внешнее свечение
            const gradient = effectsCtx.createRadialGradient(x, y, 0, x, y, size * 2);
            gradient.addColorStop(0, color);
            gradient.addColorStop(1, 'transparent');
            
            effectsCtx.fillStyle = gradient;
            effectsCtx.beginPath();
            effectsCtx.arc(x, y, size * 2, 0, Math.PI * 2);
            effectsCtx.fill();
            
            // Ядро частицы
            effectsCtx.fillStyle = '#ffffff';
            effectsCtx.beginPath();
            effectsCtx.arc(x, y, size / 2, 0, Math.PI * 2);
            effectsCtx.fill();
            
            effectsCtx.restore();
        }
        
        // ==================== ИГРОВАЯ ЛОГИКА ====================
        function startGame() {
            if (!currentUser) {
                showNotification('Сначала войдите в систему!', 'error');
                return;
            }
            
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('gameContainer').style.display = 'block';
            gameState = 'playing';
            
            // Инициализация игрока
            player = {
                x: canvas.width / 2,
                y: canvas.height - 150,
                width: 60,
                height: 80,
                speed: 6,
                lastShot: 0,
                shotDelay: 150
            };
            
            // Сброс игровых объектов
            enemies = [];
            bullets = [];
            powerups = [];
            particles = [];
            boss = null;
            
            // Сброс статистики
            health = 100;
            shield = 100;
            energy = 100;
            score = 0;
            
            // Обновление HUD
            updateHUD();
            
            showNotification('🚀 ИГРА НАЧАЛАСЬ! Удачи в космосе!', 'success');
            playSound('powerup');
        }
        
        function gameLoop() {
            // Очистка canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            effectsCtx.clearRect(0, 0, effectsCanvas.width, effectsCanvas.height);
            uiCtx.clearRect(0, 0, uiCanvas.width, uiCanvas.height);
            
            // Обновление в зависимости от состояния
            switch(gameState) {
                case 'playing':
                    updateBackground();
                    updateGame();
                    renderGame();
                    break;
                case 'menu':
                    renderMenu();
                    break;
                case 'paused':
                    renderGame();
                    break;
            }
            
            requestAnimationFrame(gameLoop);
        }
        
        function updateBackground() {
            // Обновление звезд
            for (let star of stars) {
                star.y += star.speed;
                if (star.y > canvas.height) {
                    star.y = 0;
                    star.x = Math.random() * canvas.width;
                }
                star.brightness += star.twinkle;
                if (star.brightness > 1 || star.brightness < 0.2) {
                    star.twinkle = -star.twinkle;
                }
            }
            
            // Обновление планет
            for (let planet of planets) {
                planet.y += planet.speed;
                planet.x += Math.sin(Date.now() * 0.001 + planet.orbit) * 0.5;
                
                if (planet.y > canvas.height + planet.radius) {
                    planet.y = -planet.radius;
                    planet.x = Math.random() * canvas.width;
                }
            }
        }
        
        function updateGame() {
            // Обновление игрока
            updatePlayer();
            
            // Спавн врагов
            if (Math.random() < 0.03) {
                spawnEnemy();
            }
            
            // Спавн бонусов
            if (Math.random() < 0.01) {
                spawnPowerup();
            }
            
            // Обновление врагов
            updateEnemies();
            
            // Обновление пуль
            updateBullets();
            
            // Обновление бонусов
            updatePowerups();
            
            // Обновление частиц
            updateParticles();
            
            // Обновление босса
            if (boss) {
                updateBoss();
            }
            
            // Проверка столкновений
            checkCollisions();
            
            // Восстановление энергии
            if (energy < 100) {
                energy += 0.1;
                if (energy > 100) energy = 100;
                updateHUD();
            }
        }
        
        function updatePlayer() {
            const speed = player.speed;
            
            if (keys['ArrowLeft'] || keys['a'] || keys['A']) {
                player.x = Math.max(player.width / 2, player.x - speed);
            }
            if (keys['ArrowRight'] || keys['d'] || keys['D']) {
                player.x = Math.min(canvas.width - player.width / 2, player.x + speed);
            }
            if (keys['ArrowUp'] || keys['w'] || keys['W']) {
                player.y = Math.max(player.height / 2, player.y - speed);
            }
            if (keys['ArrowDown'] || keys['s'] || keys['S']) {
                player.y = Math.min(canvas.height - player.height / 2, player.y + speed);
            }
            
            // Стрельба
            if ((keys[' '] || mouse.down) && Date.now() - player.lastShot > player.shotDelay && energy >= 10) {
                shoot();
                player.lastShot = Date.now();
                energy -= 10;
                updateHUD();
            }
        }
        
        function shoot() {
            if (!unlimitedAmmo && credits < 1) return;
            
            if (!unlimitedAmmo) {
                credits--;
                updateHUD();
            }
            
            bullets.push({
                x: player.x,
                y: player.y - player.height / 2,
                width: 8,
                height: 25,
                speed: 12,
                damage: oneShotKill ? 999 : 35
            });
            
            playSound('laser');
            
            // Эффект выстрела
            for (let i = 0; i < 5; i++) {
                particles.push({
                    x: player.x,
                    y: player.y - player.height / 2,
                    vx: (Math.random() - 0.5) * 5,
                    vy: -Math.random() * 10,
                    size: Math.random() * 4 + 2,
                    color: '#ffff00',
                    life: 1.0,
                    decay: 0.05
                });
            }
        }
        
        function spawnEnemy() {
            const type = Math.floor(Math.random() * 3);
            const size = 40 + Math.random() * 30;
            
            enemies.push({
                x: Math.random() * (canvas.width - size),
                y: -size,
                width: size,
                height: size,
                health: type === 0 ? 50 : type === 1 ? 30 : 100,
                maxHealth: type === 0 ? 50 : type === 1 ? 30 : 100,
                speed: type === 1 ? 4 : type === 0 ? 2.5 : 1.5,
                type: type,
                score: type === 0 ? 100 : type === 1 ? 150 : 200
            });
        }
        
        function updateEnemies() {
            for (let i = enemies.length - 1; i >= 0; i--) {
                const enemy = enemies[i];
                enemy.y += enemy.speed;
                
                if (enemy.y > canvas.height + 100) {
                    enemies.splice(i, 1);
                }
            }
        }
        
        function spawnPowerup() {
            const types = ['health', 'shield', 'credits'];
            const type = types[Math.floor(Math.random() * types.length)];
            
            powerups.push({
                x: Math.random() * (canvas.width - 40),
                y: -40,
                width: 40,
                height: 40,
                speed: 3,
                type: type,
                value: type === 'credits' ? 100 : 30
            });
        }
        
        function updatePowerups() {
            for (let i = powerups.length - 1; i >= 0; i--) {
                const powerup = powerups[i];
                powerup.y += powerup.speed;
                
                if (powerup.y > canvas.height + 50) {
                    powerups.splice(i, 1);
                }
            }
        }
        
        function updateBullets() {
            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                bullet.y -= bullet.speed;
                
                if (bullet.y < -50) {
                    bullets.splice(i, 1);
                }
            }
        }
        
        function updateParticles() {
            for (let i = particles.length - 1; i >= 0; i--) {
                const p = particles[i];
                p.x += p.vx;
                p.y += p.vy;
                p.life -= p.decay;
                
                if (p.life <= 0) {
                    particles.splice(i, 1);
                }
            }
        }
        
        function checkCollisions() {
            // Пули с врагами
            for (let i = bullets.length - 1; i >= 0; i--) {
                for (let j = enemies.length - 1; j >= 0; j--) {
                    if (checkCollision(bullets[i], enemies[j])) {
                        enemies[j].health -= bullets[i].damage;
                        bullets.splice(i, 1);
                        
                        // Эффект попадания
                        createExplosion(enemies[j].x + enemies[j].width/2, 
                                      enemies[j].y + enemies[j].height/2, 
                                      20, '#ff0000');
                        
                        playSound('damage');
                        
                        if (enemies[j].health <= 0) {
                            // Уничтожение врага
                            score += enemies[j].score;
                            credits += Math.floor(enemies[j].score / 5);
                            
                            // Большой взрыв
                            createExplosion(enemies[j].x + enemies[j].width/2, 
                                          enemies[j].y + enemies[j].height/2, 
                                          50, '#ff4444');
                            
                            playSound('explosion');
                            
                            enemies.splice(j, 1);
                            
                            updateHUD();
                            
                            // Шанс выпадения бонуса
                            if (Math.random() < 0.3) {
                                spawnPowerupAt(enemies[j].x + enemies[j].width/2, 
                                             enemies[j].y + enemies[j].height/2);
                            }
                        }
                        break;
                    }
                }
            }
            
            // Игрок с врагами
            for (let i = enemies.length - 1; i >= 0; i--) {
                if (checkCollision(player, enemies[i])) {
                    if (!godMode) {
                        if (shield > 0) {
                            shield -= 20;
                        } else {
                            health -= 20;
                        }
                    }
                    
                    createExplosion(player.x, player.y, 30, '#ff0000');
                    enemies.splice(i, 1);
                    
                    playSound('damage');
                    
                    if (health <= 0) {
                        gameOver();
                    }
                    
                    updateHUD();
                }
            }
            
            // Игрок с бонусами
            for (let i = powerups.length - 1; i >= 0; i--) {
                if (checkCollision(player, powerups[i])) {
                    applyPowerup(powerups[i]);
                    createExplosion(powerups[i].x + powerups[i].width/2, 
                                  powerups[i].y + powerups[i].height/2, 
                                  30, '#00ff00');
                    powerups.splice(i, 1);
                    playSound('powerup');
                }
            }
            
            // Игрок с боссом
            if (boss && checkCollision(player, boss)) {
                if (!godMode) {
                    health -= 50;
                }
                
                createExplosion(player.x, player.y, 50, '#ff00ff');
                playSound('boss');
                
                if (health <= 0) {
                    gameOver();
                }
                
                updateHUD();
            }
        }
        
        function checkCollision(a, b) {
            const aRadius = a.width ? a.width / 2 : 10;
            const bRadius = b.width ? b.width / 2 : 10;
            
            const dx = a.x - (b.x + (b.width || 0) / 2);
            const dy = a.y - (b.y + (b.height || 0) / 2);
            const distance = Math.sqrt(dx * dx + dy * dy);
            
            return distance < (aRadius + bRadius);
        }
        
        function applyPowerup(powerup) {
            switch(powerup.type) {
                case 'health':
                    health = Math.min(100, health + powerup.value);
                    showNotification(`+${powerup.value} здоровья!`, 'success');
                    break;
                case 'shield':
                    shield = Math.min(100, shield + powerup.value);
                    showNotification(`+${powerup.value} щита!`, 'success');
                    break;
                case 'credits':
                    credits += powerup.value;
                    showNotification(`+${powerup.value} кредитов!`, 'success');
                    break;
            }
            updateHUD();
        }
        
        function createExplosion(x, y, count, color) {
            for (let i = 0; i < count; i++) {
                particles.push({
                    x: x,
                    y: y,
                    vx: (Math.random() - 0.5) * 15,
                    vy: (Math.random() - 0.5) * 15,
                    size: Math.random() * 8 + 4,
                    color: color,
                    life: 1.0,
                    decay: Math.random() * 0.03 + 0.02
                });
            }
        }
        
        function spawnPowerupAt(x, y) {
            const types = ['health', 'shield', 'credits'];
            const type = types[Math.floor(Math.random() * types.length)];
            
            powerups.push({
                x: x - 20,
                y: y - 20,
                width: 40,
                height: 40,
                speed: 0,
                type: type,
                value: type === 'credits' ? 50 : 20
            });
        }
        
        // ==================== РЕНДЕРИНГ ====================
        function renderGame() {
            // Фон
            drawBackground();
            
            // Бонусы
            for (const powerup of powerups) {
                drawPowerup(powerup.x, powerup.y, powerup.width, powerup.height, powerup.type);
            }
            
            // Враги
            for (const enemy of enemies) {
                drawEnemy(enemy.x, enemy.y, enemy.width, enemy.height, enemy.type);
                
                // Полоска здоровья
                if (enemy.health < enemy.maxHealth) {
                    drawHealthBar(enemy.x, enemy.y - 15, enemy.width, 8, enemy.health / enemy.maxHealth);
                }
            }
            
            // Босс
            if (boss) {
                drawBoss(boss.x, boss.y, boss.width, boss.height);
                drawHealthBar(boss.x, boss.y - 40, boss.width, 15, boss.health / boss.maxHealth);
            }
            
            // Пули
            for (const bullet of bullets) {
                drawBullet(bullet.x, bullet.y, bullet.width, bullet.height);
            }
            
            // Частицы
            for (const particle of particles) {
                drawParticle(particle.x, particle.y, particle.size, particle.color, particle.life);
            }
            
            // Игрок
            drawPlayer(player.x - player.width/2, player.y - player.height/2, player.width, player.height);
        }
        
        function drawBackground() {
            // Градиентный космический фон
            const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#000011');
            gradient.addColorStop(0.5, '#000033');
            gradient.addColorStop(1, '#000022');
            
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Туманности
            for (const nebula of nebulas) {
                ctx.fillStyle = nebula.color;
                ctx.beginPath();
                ctx.arc(nebula.x, nebula.y, nebula.radius, 0, Math.PI * 2);
                ctx.fill();
            }
            
            // Планеты
            for (const planet of planets) {
                ctx.fillStyle = planet.color;
                ctx.beginPath();
                ctx.arc(planet.x, planet.y, planet.radius, 0, Math.PI * 2);
                ctx.fill();
                
                // Тени
                ctx.fillStyle = 'rgba(0, 0, 0, 0.3)';
                ctx.beginPath();
                ctx.arc(planet.x - planet.radius * 0.3, planet.y + planet.radius * 0.3, planet.radius * 0.9, 0, Math.PI * 2);
                ctx.fill();
            }
            
            // Звезды
            for (const star of stars) {
                ctx.fillStyle = `rgba(255, 255, 255, ${star.brightness})`;
                ctx.beginPath();
                ctx.arc(star.x, star.y, star.size / 2, 0, Math.PI * 2);
                ctx.fill();
            }
        }
        
        function drawHealthBar(x, y, width, height, percent) {
            ctx.save();
            
            // Фон
            ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
            ctx.fillRect(x, y, width, height);
            
            // Заполнение
            const fillWidth = width * Math.max(0, percent);
            const gradient = ctx.createLinearGradient(x, y, x + fillWidth, y);
            gradient.addColorStop(0, '#ff0000');
            gradient.addColorStop(0.5, '#ffff00');
            gradient.addColorStop(1, '#00ff00');
            
            ctx.fillStyle = gradient;
            ctx.fillRect(x, y, fillWidth, height);
            
            // Рамка
            ctx.strokeStyle = '#ffffff';
            ctx.lineWidth = 2;
            ctx.strokeRect(x, y, width, height);
            
            ctx.restore();
        }
        
        function renderMenu() {
            drawBackground();
        }
        
        // ==================== ИНТЕРФЕЙС ====================
        function updateHUD() {
            document.getElementById('hudUsername').textContent = currentUser.username;
            document.getElementById('hudScore').textContent = score;
            document.getElementById('hudLevel').textContent = level;
            document.getElementById('hudCredits').textContent = credits;
            
            document.getElementById('healthBar').style.width = `${health}%`;
            document.getElementById('shieldBar').style.width = `${shield}%`;
            document.getElementById('energyBar').style.width = `${energy}%`;
            
            // Обновление меню
            document.getElementById('menuUsername').textContent = currentUser.username;
            document.getElementById('menuLevel').textContent = level;
            document.getElementById('menuCredits').textContent = credits;
            
            // Обновление магазина
            document.getElementById('shopCredits').textContent = credits;
        }
        
        function showNotification(text, type = 'info') {
            const notification = document.createElement('div');
            notification.className = `notification notification-${type}`;
            notification.innerHTML = `
                <div style="display: flex; align-items: center; justify-content: center; gap: 10px;">
                    <div style="font-size: 1.5rem;">
                        ${type === 'success' ? '✅' : 
                          type === 'error' ? '❌' : 
                          type === 'admin' ? '👑' : 'ℹ️'}
                    </div>
                    <div>${text}</div>
                </div>
            `;
            
            document.body.appendChild(notification);
            
            setTimeout(() => {
                notification.style.opacity = '0';
                notification.style.transform = 'translateX(-50%) translateY(-100px) scale(0.9)';
                setTimeout(() => notification.remove(), 500);
            }, 3000);
        }
        
        // ==================== ИГРОВЫЕ СОБЫТИЯ ====================
        function gameOver() {
            gameState = 'menu';
            document.getElementById('gameContainer').style.display = 'none';
            document.getElementById('mainMenu').style.display = 'flex';
            
            // Сохранение прогресса
            if (currentUser && !currentUser.isAdmin) {
                const users = JSON.parse(localStorage.getItem('nebulaUsers'));
                if (users[currentUser.username]) {
                    users[currentUser.username].score = Math.max(users[currentUser.username].score, score);
                    users[currentUser.username].credits = credits;
                    users[currentUser.username].level = level;
                    localStorage.setItem('nebulaUsers', JSON.stringify(users));
                }
            }
            
            showNotification(`ИГРА ОКОНЧЕНА! Ваш счет: ${score}`, 'error');
            playSound('explosion');
        }
        
        // ==================== АДМИН ФУНКЦИИ ====================
        function spawnBoss() {
            boss = {
                x: canvas.width / 2 - 150,
                y: -300,
                width: 300,
                height: 300,
                health: 2000,
                maxHealth: 2000,
                speed: 1
            };
            
            playSound('boss');
            showNotification('⚠️ БОСС ПОЯВИЛСЯ! Будьте осторожны!', 'admin');
        }
        
        function updateBoss() {
            if (!boss) return;
            
            boss.y += boss.speed;
            
            // Движение по синусоиде
            boss.x += Math.sin(Date.now() * 0.002) * 2;
            
            if (boss.y > canvas.height + 200) {
                boss = null;
            }
        }
        
        // ==================== ОБРАБОТЧИКИ СОБЫТИЙ ====================
        function initEventListeners() {
            // Изменение размера окна
            window.addEventListener('resize', resizeCanvas);
            
            // Управление клавиатурой
            window.addEventListener('keydown', (e) => {
                keys[e.key] = true;
                
                // ESC - пауза/меню
                if (e.key === 'Escape') {
                    e.preventDefault();
                    
                    if (gameState === 'playing') {
                        gameState = 'paused';
                        document.getElementById('pauseMenu').style.display = 'flex';
                        showNotification('Игра на паузе', 'info');
                    } else if (gameState === 'paused') {
                        gameState = 'playing';
                        document.getElementById('pauseMenu').style.display = 'none';
                    } else {
                        // Закрыть все модальные окна
                        document.querySelectorAll('.modal').forEach(modal => {
                            modal.style.display = 'none';
                        });
                    }
                }
                
                // Альтернативная пауза
                if (e.key === 'p' || e.key === 'P') {
                    if (gameState === 'playing') {
                        gameState = 'paused';
                        document.getElementById('pauseMenu').style.display = 'flex';
                    } else if (gameState === 'paused') {
                        gameState = 'playing';
                        document.getElementById('pauseMenu').style.display = 'none';
                    }
                }
                
                // Админ панель (Ctrl+Alt+A)
                if (e.ctrlKey && e.altKey && e.key === 'a') {
                    if (isAdmin) {
                        document.getElementById('adminModal').style.display = 'flex';
                    }
                }
                
                // Музыка (M)
                if (e.key === 'm' || e.key === 'M') {
                    const music = document.getElementById('backgroundMusic');
                    if (music.paused) {
                        music.play();
                        document.getElementById('playPauseBtn').textContent = '⏸️';
                    } else {
                        music.pause();
                        document.getElementById('playPauseBtn').textContent = '▶️';
                    }
                }
                
                // Чит-коды для админа
                if (isAdmin && gameState === 'playing') {
                    // Бессмертие (Ctrl+I)
                    if (e.ctrlKey && e.key === 'i') {
                        godMode = !godMode;
                        showNotification(`Бессмертие: ${godMode ? 'ВКЛ' : 'ВЫКЛ'}`, 'admin');
                    }
                    
                    // +1000 очков (Ctrl+S)
                    if (e.ctrlKey && e.key === 's') {
                        score += 1000;
                        updateHUD();
                        showNotification('+1000 очков!', 'admin');
                    }
                    
                    // Один выстрел - одно убийство (Ctrl+O)
                    if (e.ctrlKey && e.key === 'o') {
                        oneShotKill = !oneShotKill;
                        showNotification(`Один выстрел - одно убийство: ${oneShotKill ? 'ВКЛ' : 'ВЫКЛ'}`, 'admin');
                    }
                }
            });
            
            window.addEventListener('keyup', (e) => {
                keys[e.key] = false;
            });
            
            // Управление мышью
            window.addEventListener('mousemove', (e) => {
                mouse.x = e.clientX;
                mouse.y = e.clientY;
            });
            
            window.addEventListener('mousedown', () => {
                mouse.down = true;
            });
            
            window.addEventListener('mouseup', () => {
                mouse.down = false;
            });
            
            // ==================== КНОПКИ ====================
            
            // Аутентификация
            document.getElementById('loginTabBtn').addEventListener('click', () => {
                document.getElementById('loginForm').style.display = 'block';
                document.getElementById('registerForm').style.display = 'none';
                document.getElementById('loginTabBtn').style.background = 'linear-gradient(45deg, #0066ff, #00ccff)';
                document.getElementById('registerTabBtn').style.background = 'rgba(0, 30, 60, 0.8)';
            });
            
            document.getElementById('registerTabBtn').addEventListener('click', () => {
                document.getElementById('loginForm').style.display = 'none';
                document.getElementById('registerForm').style.display = 'block';
                document.getElementById('registerTabBtn').style.background = 'linear-gradient(45deg, #0066ff, #00ccff)';
                document.getElementById('loginTabBtn').style.background = 'rgba(0, 30, 60, 0.8)';
            });
            
            document.getElementById('loginForm').addEventListener('submit', (e) => {
                e.preventDefault();
                const username = document.getElementById('loginUsername').value;
                const password = document.getElementById('loginPassword').value;
                loginUser(username, password);
            });
            
            document.getElementById('registerForm').addEventListener('submit', (e) => {
                e.preventDefault();
                const username = document.getElementById('registerUsername').value;
                const password = document.getElementById('registerPassword').value;
                const confirmPass = document.getElementById('registerPasswordConfirm').value;
                
                if (password !== confirmPass) {
                    showNotification('Пароли не совпадают!', 'error');
                    return;
                }
                
                registerUser(username, password);
            });
            
            // Главное меню
            document.getElementById('startGameBtn').addEventListener('click', startGame);
            document.getElementById('promocodesBtn').addEventListener('click', showPromocodes);
            document.getElementById('shopBtn').addEventListener('click', showShop);
            document.getElementById('logoutBtn').addEventListener('click', logoutUser);
            
            // Меню паузы
            document.getElementById('resumeBtn').addEventListener('click', () => {
                document.getElementById('pauseMenu').style.display = 'none';
                gameState = 'playing';
            });
            
            document.getElementById('toMainMenuBtn').addEventListener('click', () => {
                if (confirm('Выйти в главное меню? Текущий прогресс будет сохранен.')) {
                    document.getElementById('pauseMenu').style.display = 'none';
                    document.getElementById('gameContainer').style.display = 'none';
                    document.getElementById('mainMenu').style.display = 'flex';
                    gameState = 'menu';
                    saveGameProgress();
                }
            });
            
            // Аудио контролы
            document.getElementById('playPauseBtn').addEventListener('click', () => {
                const music = document.getElementById('backgroundMusic');
                if (music.paused) {
                    music.play();
                    document.getElementById('playPauseBtn').textContent = '⏸️';
                } else {
                    music.pause();
                    document.getElementById('playPauseBtn').textContent = '▶️';
                }
            });
            
            document.getElementById('prevTrackBtn').addEventListener('click', () => {
                const music = document.getElementById('backgroundMusic');
                currentTrack = (currentTrack - 1 + musicTracks.length) % musicTracks.length;
                music.src = musicTracks[currentTrack];
                music.play();
                showNotification('Предыдущий трек', 'info');
            });
            
            document.getElementById('nextTrackBtn').addEventListener('click', () => {
                const music = document.getElementById('backgroundMusic');
                currentTrack = (currentTrack + 1) % musicTracks.length;
                music.src = musicTracks[currentTrack];
                music.play();
                showNotification('Следующий трек', 'info');
            });
            
            document.getElementById('volumeSlider').addEventListener('input', (e) => {
                musicVolume = e.target.value / 100;
                const music = document.getElementById('backgroundMusic');
                music.volume = musicVolume;
            });
            
            document.getElementById('muteBtn').addEventListener('click', () => {
                isMusicMuted = !isMusicMuted;
                const music = document.getElementById('backgroundMusic');
                music.muted = isMusicMuted;
                document.getElementById('muteBtn').textContent = isMusicMuted ? '🔇' : '🔊';
            });
            
            // Админ панель
            document.getElementById('adminPanelBtn').addEventListener('click', () => {
                document.getElementById('adminModal').style.display = 'flex';
            });
            
            document.getElementById('closeAdminBtn').addEventListener('click', () => {
                document.getElementById('adminModal').style.display = 'none';
            });
            
            // Чит-кнопки
            document.getElementById('godModeBtn').addEventListener('click', () => {
                godMode = !godMode;
                showNotification(`Бессмертие: ${godMode ? 'ВКЛ' : 'ВЫКЛ'}`, 'admin');
            });
            
            document.getElementById('unlimitedAmmoBtn').addEventListener('click', () => {
                unlimitedAmmo = !unlimitedAmmo;
                showNotification(`Бесконечные патроны: ${unlimitedAmmo ? 'ВКЛ' : 'ВЫКЛ'}`, 'admin');
            });
            
            document.getElementById('addCreditsBtn').addEventListener('click', () => {
                credits += 1000;
                updateHUD();
                showNotification('+1000 кредитов!', 'admin');
            });
            
            document.getElementById('addScoreBtn').addEventListener('click', () => {
                score += 1000;
                updateHUD();
                showNotification('+1000 очков!', 'admin');
            });
            
            document.getElementById('spawnBossBtn').addEventListener('click', spawnBoss);
            
            document.getElementById('clearEnemiesBtn').addEventListener('click', () => {
                enemies = [];
                showNotification('Все враги уничтожены!', 'admin');
            });
            
            document.getElementById('healPlayerBtn').addEventListener('click', () => {
                health = 100;
                shield = 100;
                energy = 100;
                updateHUD();
                showNotification('Полное лечение!', 'admin');
            });
            
            document.getElementById('maxStatsBtn').addEventListener('click', () => {
                health = 100;
                shield = 100;
                energy = 100;
                score = 9999;
                credits = 9999;
                level = 99;
                updateHUD();
                showNotification('Максимальные характеристики!', 'admin');
            });
            
            // Промокоды
            document.getElementById('activatePromoBtn').addEventListener('click', activatePromo);
            document.getElementById('closePromoBtn').addEventListener('click', () => {
                document.getElementById('promocodesModal').style.display = 'none';
            });
            
            // Магазин
            document.getElementById('closeShopBtn').addEventListener('click', () => {
                document.getElementById('shopModal').style.display = 'none';
            });
            
            // Закрытие модальных окон по клику на фон
            document.querySelectorAll('.modal').forEach(modal => {
                modal.addEventListener('click', (e) => {
                    if (e.target === modal) {
                        modal.style.display = 'none';
                    }
                });
            });
        }
        
        // ==================== СИСТЕМА АВТОРИЗАЦИИ ====================
        function loginUser(username, password) {
            const users = JSON.parse(localStorage.getItem('nebulaUsers'));
            
            // Проверка на админа
            if (username === 'admin' && password === 'admin205') {
                currentUser = {
                    username: 'admin',
                    isAdmin: true,
                    score: 0,
                    credits: 999999,
                    level: 100
                };
                isAdmin = true;
                showNotification('👑 АДМИН РЕЖИМ АКТИВИРОВАН!', 'admin');
                showMainMenu();
                return;
            }
            
            if (users[username] && users[username].password === password) {
                currentUser = {
                    username: username,
                    ...users[username]
                };
                showNotification(`Добро пожаловать, ${username}!`, 'success');
                showMainMenu();
            } else {
                showNotification('Неверный логин или пароль!', 'error');
            }
        }
        
        function registerUser(username, password) {
            const users = JSON.parse(localStorage.getItem('nebulaUsers'));
            
            if (users[username]) {
                showNotification('Пользователь уже существует!', 'error');
                return;
            }
            
            users[username] = {
                password: password,
                score: 0,
                credits: 500,
                level: 1,
                health: 100
            };
            
            localStorage.setItem('nebulaUsers', JSON.stringify(users));
            showNotification('Регистрация успешна! Вам начислено 500 кредитов.', 'success');
            loginUser(username, password);
        }
        
        function showMainMenu() {
            document.getElementById('authScreen').style.display = 'none';
            document.getElementById('mainMenu').style.display = 'flex';
            gameState = 'menu';
            
            // Обновляем информацию в меню
            document.getElementById('menuUsername').textContent = currentUser.username;
            
            // Показываем кнопку админ панели для админа
            if (isAdmin) {
                document.getElementById('adminPanelBtn').style.display = 'block';
            }
            
            // Загружаем данные игрока
            score = currentUser.score || 0;
            credits = currentUser.credits || 500;
            health = currentUser.health || 100;
            level = currentUser.level || 1;
            
            updateHUD();
        }
        
        function logoutUser() {
            if (confirm('Выйти из аккаунта?')) {
                saveGameProgress();
                currentUser = null;
                isAdmin = false;
                document.getElementById('mainMenu').style.display = 'none';
                document.getElementById('authScreen').style.display = 'flex';
                gameState = 'auth';
            }
        }
        
        function saveGameProgress() {
            if (currentUser && !currentUser.isAdmin) {
                const users = JSON.parse(localStorage.getItem('nebulaUsers'));
                if (users[currentUser.username]) {
                    users[currentUser.username].score = score;
                    users[currentUser.username].credits = credits;
                    users[currentUser.username].health = health;
                    users[currentUser.username].level = level;
                    localStorage.setItem('nebulaUsers', JSON.stringify(users));
                }
            }
        }
        
        // ==================== ПРОМОКОДЫ ====================
        function showPromocodes() {
            updatePromoList();
            document.getElementById('promocodesModal').style.display = 'flex';
        }
        
        function updatePromoList() {
            const list = document.getElementById('promoList');
            list.innerHTML = '';
            
            promoCodes.forEach(promo => {
                const used = usedPromoCodes.includes(promo.code);
                list.innerHTML += `
                    <div class="promo-card" style="opacity: ${used ? '0.6' : '1'}">
                        <div class="promo-code">${promo.code}</div>
                        <div style="color: #88aaff; margin-bottom: 1rem;">${promo.description}</div>
                        <div style="color: #ffff00;">
                            ${promo.credits ? `💰 ${promo.credits} кредитов<br>` : ''}
                            ${promo.xp ? `⭐ ${promo.xp} опыта<br>` : ''}
                            ${promo.health ? `❤️ ${promo.health} здоровья<br>` : ''}
                            ${promo.shield ? `🛡️ ${promo.shield} щита<br>` : ''}
                            ${promo.score ? `🏆 ${promo.score} очков<br>` : ''}
                            ${promo.level ? `📈 Уровень ${promo.level}<br>` : ''}
                            ${promo.energy ? `⚡ ${promo.energy} энергии` : ''}
                        </div>
                        ${used ? '<div style="color: #ff6666; margin-top: 1rem;">(уже использован)</div>' : ''}
                    </div>
                `;
            });
        }
        
        function activatePromo() {
            const code = document.getElementById('promoInput').value.toUpperCase();
            const message = document.getElementById('promoMessage');
            
            if (!code) {
                message.textContent = 'Введите промокод!';
                message.style.display = 'block';
                message.style.background = 'rgba(255,0,0,0.1)';
                message.style.color = '#ff6666';
                return;
            }
            
            if (usedPromoCodes.includes(code)) {
                message.textContent = 'Этот промокод уже использован!';
                message.style.display = 'block';
                message.style.background = 'rgba(255,0,0,0.1)';
                message.style.color = '#ff6666';
                return;
            }
            
            const promo = promoCodes.find(p => p.code === code);
            
            if (!promo) {
                message.textContent = 'Неверный промокод!';
                message.style.display = 'block';
                message.style.background = 'rgba(255,0,0,0.1)';
                message.style.color = '#ff6666';
                return;
            }
            
            // Активация промокода
            usedPromoCodes.push(code);
            localStorage.setItem('usedPromoCodes', JSON.stringify(usedPromoCodes));
            
            if (promo.credits) {
                credits += promo.credits;
                showNotification(`+${promo.credits} кредитов!`, 'success');
            }
            if (promo.health) {
                health = Math.min(100, health + promo.health);
                showNotification(`+${promo.health} здоровья!`, 'success');
            }
            if (promo.shield) {
                shield = Math.min(100, shield + promo.shield);
                showNotification(`+${promo.shield} щита!`, 'success');
            }
            if (promo.score) {
                score += promo.score;
                showNotification(`+${promo.score} очков!`, 'success');
            }
            if (promo.level) {
                level += promo.level;
                showNotification(`+${promo.level} уровня!`, 'success');
            }
            
            updateHUD();
            updatePromoList();
            
            message.textContent = `🎉 Промокод "${code}" успешно активирован!`;
            message.style.display = 'block';
            message.style.background = 'rgba(0,255,0,0.1)';
            message.style.color = '#66ff66';
            
            document.getElementById('promoInput').value = '';
            playSound('powerup');
        }
        
        // ==================== МАГАЗИН ====================
        function showShop() {
            updateShopItems();
            document.getElementById('shopModal').style.display = 'flex';
        }
        
        function updateShopItems() {
            const container = document.getElementById('shopItems');
            container.innerHTML = '';
            
            shopItems.forEach(item => {
                const canBuy = credits >= item.price;
                container.innerHTML += `
                    <div class="promo-card" style="opacity: ${canBuy ? '1' : '0.6'}; cursor: ${canBuy ? 'pointer' : 'not-allowed'}" 
                         onclick="${canBuy ? `buyItem(${item.id})` : ''}">
                        <div style="font-size: 2rem; margin-bottom: 1rem;">
                            ${item.type === 'weapon' ? '🔫' : 
                              item.type === 'shield' ? '🛡️' : 
                              item.type === 'energy' ? '⚡' : 
                              item.type === 'speed' ? '🚀' : 
                              item.type === 'repair' ? '🔧' : '🎁'}
                        </div>
                        <div style="color: #00ff88; font-size: 1.3rem; font-weight: bold; margin-bottom: 0.5rem;">
                            ${item.name}
                        </div>
                        <div style="color: #88aaff; margin-bottom: 1rem;">
                            ${item.description}
                        </div>
                        <div style="color: ${canBuy ? '#ffff00' : '#ff6666'}; font-size: 1.2rem; font-weight: bold;">
                            ${item.price} кредитов
                        </div>
                        ${!canBuy ? '<div style="color: #ff6666; margin-top: 0.5rem;">Недостаточно кредитов</div>' : ''}
                    </div>
                `;
            });
        }
        
        function buyItem(itemId) {
            const item = shopItems.find(i => i.id === itemId);
            
            if (!item || credits < item.price) {
                showNotification('Недостаточно кредитов!', 'error');
                return;
            }
            
            credits -= item.price;
            updateHUD();
            
            // Применение улучшения
            switch(item.type) {
                case 'weapon':
                    player.shotDelay = Math.max(50, player.shotDelay - 20);
                    showNotification(`Лазер улучшен! Скорострельность +20%`, 'success');
                    break;
                case 'shield':
                    shield = Math.min(100, shield + 30);
                    showNotification(`Щит усилен! +30% защиты`, 'success');
                    break;
                case 'energy':
                    energy = Math.min(100, energy + 50);
                    showNotification(`Энергореактор установлен! +50% энергии`, 'success');
                    break;
                case 'speed':
                    player.speed += 2;
                    showNotification(`Турбоускоритель активирован! +25% скорости`, 'success');
                    break;
                case 'repair':
                    health = 100;
                    shield = 100;
                    showNotification(`Система авто-ремонта активирована!`, 'success');
                    break;
            }
            
            updateShopItems();
            playSound('powerup');
        }
        
        // ==================== ЗАПУСК ИГРЫ ====================
        window.addEventListener('load', () => {
            init();
            initEventListeners();
            
            console.log('🚀 NEBULA STRIKE PRO MAX запущен!');
            console.log('🎮 Управление:');
            console.log('   WASD/Стрелки - движение');
            console.log('   Пробел/ЛКМ - стрельба');
            console.log('   ESC/P - пауза');
            console.log('   M - музыка вкл/выкл');
            console.log('   Ctrl+Alt+A - админ панель');
            console.log('   Ctrl+I - бессмертие (админ)');
            console.log('   Ctrl+S - +1000 очков (админ)');
            console.log('');
            console.log('🔑 Админ доступ: admin / admin205');
            console.log('🎁 Промокоды: WELCOME2024, SPACEPOWER, NEBULASTAR, GALAXYHERO');
        });
    </script>
</body>
</html># nebula-strike
