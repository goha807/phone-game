<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smartphone Tycoon: ONLINE</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        :root {
            --bg-dark: #09090b;
            --bg-panel: #18181b;
            --primary: #3b82f6; /* Blue for tech */
            --accent: #ef4444;  /* Red for errors/costs */
            --success: #22c55e; /* Green for money */
            --text-main: #e4e4e7;
            --text-muted: #a1a1aa;
            --border: #27272a;
            --chat-bg: #101012;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background: var(--bg-dark); color: var(--text-main); height: 100vh; display: flex; overflow: hidden; }

        /* --- LOGIN SCREEN --- */
        #login-screen { position: fixed; inset: 0; background: var(--bg-dark); z-index: 999; display: flex; align-items: center; justify-content: center; flex-direction: column; }
        .login-box { background: var(--bg-panel); padding: 40px; border-radius: 12px; border: 1px solid var(--border); width: 100%; max-width: 400px; text-align: center; box-shadow: 0 0 50px rgba(59, 130, 246, 0.1); }
        .input-group { margin-bottom: 15px; text-align: left; }
        .input-group label { font-size: 0.8rem; color: var(--text-muted); margin-bottom: 5px; display: block; }
        input { width: 100%; padding: 12px; background: #000; border: 1px solid var(--border); color: white; border-radius: 6px; outline: none; transition: 0.3s; }
        input:focus { border-color: var(--primary); box-shadow: 0 0 0 2px rgba(59, 130, 246, 0.2); }
        .avatar-grid { display: flex; gap: 10px; justify-content: center; margin: 20px 0; }
        .avatar-option { width: 50px; height: 50px; border-radius: 50%; border: 2px solid transparent; cursor: pointer; background-size: cover; transition: 0.2s; opacity: 0.6; }
        .avatar-option:hover { opacity: 1; }
        .avatar-option.selected { border-color: var(--primary); opacity: 1; transform: scale(1.1); }

        /* --- MAIN LAYOUT --- */
        #app { display: flex; width: 100%; height: 100%; opacity: 0; transition: opacity 1s; }
        
        /* Sidebar */
        aside { width: 260px; background: var(--bg-panel); border-right: 1px solid var(--border); display: flex; flex-direction: column; }
        .profile-section { padding: 20px; border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: 10px; }
        .profile-avatar { width: 45px; height: 45px; border-radius: 50%; background: var(--primary); display: flex; align-items: center; justify-content: center; font-weight: bold; font-size: 1.2rem; }
        .nav-menu { padding: 10px; flex: 1; }
        .nav-btn { width: 100%; padding: 12px 15px; margin-bottom: 5px; background: transparent; border: none; color: var(--text-muted); text-align: left; cursor: pointer; border-radius: 8px; transition: 0.2s; display: flex; align-items: center; gap: 10px; }
        .nav-btn:hover { background: rgba(255,255,255,0.05); color: white; }
        .nav-btn.active { background: var(--primary); color: white; }
        
        /* Chat Section */
        .chat-container { height: 35%; border-top: 1px solid var(--border); display: flex; flex-direction: column; background: var(--chat-bg); }
        .chat-header { padding: 10px; font-size: 0.8rem; border-bottom: 1px solid var(--border); color: var(--success); display: flex; justify-content: space-between; }
        .chat-messages { flex: 1; overflow-y: auto; padding: 10px; font-size: 0.85rem; scrollbar-width: thin; }
        .chat-msg { margin-bottom: 8px; line-height: 1.4; animation: fadeIn 0.3s; }
        .msg-sys { color: var(--text-muted); font-style: italic; font-size: 0.75rem; text-align: center; margin: 10px 0; }
        .msg-user { color: var(--primary); font-weight: bold; }
        .msg-text { color: var(--text-main); }
        .chat-input-area { padding: 10px; display: flex; gap: 5px; border-top: 1px solid var(--border); }
        
        /* Main Content */
        main { flex: 1; background: #000; overflow-y: auto; padding: 20px; position: relative; }
        header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px; background: var(--bg-panel); padding: 15px 25px; border-radius: 12px; border: 1px solid var(--border); }
        .stat-box { text-align: center; }
        .stat-val { font-size: 1.4rem; font-weight: 800; font-family: monospace; }
        .money { color: var(--success); }

        /* Cards & Grid */
        .card { background: var(--bg-panel); border: 1px solid var(--border); border-radius: 10px; padding: 20px; margin-bottom: 20px; }
        .grid-tech { display: grid; grid-template-columns: repeat(auto-fill, minmax(130px, 1fr)); gap: 10px; margin-top: 10px; }
        .tech-item { background: rgba(255,255,255,0.03); border: 1px solid var(--border); padding: 10px; border-radius: 8px; cursor: pointer; text-align: center; transition: 0.2s; font-size: 0.85rem; position: relative; }
        .tech-item:hover { border-color: var(--text-muted); background: rgba(255,255,255,0.08); }
        .tech-item.selected { border-color: var(--primary); background: rgba(59, 130, 246, 0.1); box-shadow: 0 0 10px rgba(59, 130, 246, 0.2); }
        .tech-item.locked { opacity: 0.3; pointer-events: none; filter: grayscale(1); }

        /* Phones List */
        .phone-row { display: flex; justify-content: space-between; padding: 12px; background: rgba(255,255,255,0.03); margin-bottom: 8px; border-radius: 6px; border-left: 3px solid var(--border); align-items: center; }
        .phone-row.success { border-left-color: var(--success); background: linear-gradient(90deg, rgba(34, 197, 94, 0.1), transparent); }
        .phone-row.flop { border-left-color: var(--accent); }

        /* Leaderboard */
        .leaderboard-row { display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid #ffffff10; font-size: 0.9rem; }
        .leaderboard-row:first-child { color: #fbbf24; font-weight: bold; }

        /* Buttons */
        .btn { border: none; padding: 10px 20px; border-radius: 6px; cursor: pointer; font-weight: 600; transition: 0.2s; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-primary:hover { background: #2563eb; }
        .btn-green { background: var(--success); color: white; }
        .btn-red { background: var(--accent); color: white; }
        .btn-full { width: 100%; }

        /* Utils */
        .hidden { display: none !important; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        /* Toast */
        #toast-area { position: fixed; bottom: 20px; right: 20px; display: flex; flex-direction: column; gap: 10px; z-index: 1000; pointer-events: none; }
        .toast { background: var(--bg-panel); color: white; padding: 12px 20px; border-radius: 6px; border: 1px solid var(--border); box-shadow: 0 10px 30px rgba(0,0,0,0.5); pointer-events: auto; min-width: 250px; display: flex; align-items: center; gap: 10px; animation: slideIn 0.3s; }
        .toast i { font-size: 1.2rem; }
        .toast.success i { color: var(--success); }
        .toast.error i { color: var(--accent); }
        @keyframes slideIn { from { transform: translateX(100%); } to { transform: translateX(0); } }

        /* Responsive */
        @media (max-width: 768px) {
            #app { flex-direction: column; }
            aside { width: 100%; height: auto; flex-direction: row; flex-wrap: wrap; }
            .chat-container { display: none; /* Hide chat on mobile to save space, or make it a tab */ }
            header { padding: 10px; flex-wrap: wrap; gap: 10px; justify-content: center; }
            .nav-menu { display: flex; overflow-x: auto; }
            .nav-btn { flex-shrink: 0; width: auto; }
        }
    </style>
</head>
<body>

    <div id="login-screen">
        <div class="login-box">
            <h2 style="color: var(--primary); margin-bottom: 5px;"><i class="fas fa-globe"></i> TYCOON ONLINE</h2>
            <p style="color: var(--text-muted); margin-bottom: 20px;">Підключення до сервера...</p>
            
            <div id="login-form" class="hidden">
                <div class="input-group">
                    <label>Нікнейм (CEO)</label>
                    <input type="text" id="inp-nick" placeholder="Введіть нікнейм" maxlength="12">
                </div>
                <div class="input-group">
                    <label>Назва корпорації</label>
                    <input type="text" id="inp-comp" placeholder="Напр. Banana Inc." maxlength="15">
                </div>
                
                <label style="font-size: 0.8rem; color: var(--text-muted);">Оберіть аватар</label>
                <div class="avatar-grid">
                    <div class="avatar-option selected" onclick="Login.selectAvatar(this, '👨‍💼')">👨‍💼</div>
                    <div class="avatar-option" onclick="Login.selectAvatar(this, '👩‍💼')">👩‍💼</div>
                    <div class="avatar-option" onclick="Login.selectAvatar(this, '🤖')">🤖</div>
                    <div class="avatar-option" onclick="Login.selectAvatar(this, '👽')">👽</div>
                </div>

                <button class="btn btn-primary btn-full" onclick="Login.submit()">УВІЙТИ В ГРУ</button>
                <button class="btn btn-full" style="background: transparent; color: #666; margin-top: 10px;" onclick="Login.load()">Завантажити сейв</button>
            </div>
            
            <div id="loader">
                <i class="fas fa-circle-notch fa-spin" style="font-size: 2rem; color: var(--primary);"></i>
            </div>
        </div>
    </div>

    <div id="app" class="hidden">
        <aside>
            <div class="profile-section">
                <div class="profile-avatar" id="ui-avatar">👨‍💼</div>
                <div style="overflow: hidden;">
                    <div style="font-weight: bold; white-space: nowrap;" id="ui-nick">Player</div>
                    <div style="font-size: 0.8rem; color: var(--text-muted);" id="ui-company-name">Inc.</div>
                </div>
            </div>
            
            <nav class="nav-menu">
                <button class="nav-btn active" onclick="UI.tab('office')"><i class="fas fa-building"></i> Офіс</button>
                <button class="nav-btn" onclick="UI.tab('lab')"><i class="fas fa-microchip"></i> Розробка</button>
                <button class="nav-btn" onclick="UI.tab('research')"><i class="fas fa-flask"></i> Дослідження</button>
                <button class="nav-btn" onclick="UI.tab('market')"><i class="fas fa-chart-line"></i> Ринок & Топ</button>
            </nav>

            <div class="chat-container">
                <div class="chat-header">
                    <span><i class="fas fa-circle" style="font-size: 8px;"></i> Global Chat</span>
                    <span id="online-count">1,240 on</span>
                </div>
                <div class="chat-messages" id="chat-box">
                    <div class="msg-sys">Ви підключилися до каналу #general</div>
                </div>
                <div class="chat-input-area">
                    <input type="text" id="chat-input" placeholder="Повідомлення..." onkeypress="if(event.key==='Enter') Chat.send()">
                    <button class="btn btn-primary" style="padding: 5px 10px;" onclick="Chat.send()"><i class="fas fa-paper-plane"></i></button>
                </div>
            </div>
        </aside>

        <main>
            <header>
                <div class="stat-box">
                    <div style="font-size: 0.8rem; color: var(--text-muted);">БАЛАНС</div>
                    <div class="stat-val money" id="ui-money">$0</div>
                </div>
                <div class="stat-box">
                    <div style="font-size: 0.8rem; color: var(--text-muted);">ФАНИ</div>
                    <div class="stat-val" id="ui-fans">0</div>
                </div>
                <div class="stat-box">
                    <div style="font-size: 0.8rem; color: var(--text-muted);">ДЕНЬ</div>
                    <div class="stat-val" id="ui-day">1</div>
                </div>
            </header>

            <div id="content">
                <div id="tab-office" class="view-tab">
                    <div class="card">
                        <div style="display:flex; justify-content:space-between;">
                            <h3><i class="fas fa-warehouse"></i> Виробництво</h3>
                            <button class="btn btn-primary" style="font-size:0.8rem;" onclick="Game.save()">💾 Зберегти</button>
                        </div>
                        <p style="color:var(--text-muted); margin-top:5px;">Активні продажі телефонів.</p>
                        <div id="phones-list" style="margin-top: 15px;"></div>
                    </div>
                </div>

                <div id="tab-lab" class="view-tab hidden">
                    <div class="card">
                        <h3><i class="fas fa-drafting-compass"></i> Створити смартфон</h3>
                        <div style="margin-top: 15px;">
                            <input type="text" id="new-phone-name" placeholder="Введіть назву моделі...">
                            
                            <h4 style="margin: 15px 0 5px 0; color: var(--primary);">Екран</h4>
                            <div class="grid-tech" id="grid-screen"></div>

                            <h4 style="margin: 15px 0 5px 0; color: var(--primary);">Процесор</h4>
                            <div class="grid-tech" id="grid-cpu"></div>

                            <h4 style="margin: 15px 0 5px 0; color: var(--primary);">Камера</h4>
                            <div class="grid-tech" id="grid-cam"></div>

                            <div style="background: #ffffff05; padding: 15px; border-radius: 8px; margin-top: 20px;">
                                <div style="display:flex; justify-content: space-between; margin-bottom: 10px;">
                                    <span>Ціна: <b id="ui-price-val">$500</b></span>
                                    <span>Собівартість: <b id="ui-cost-val" style="color:var(--accent)">$0</b></span>
                                </div>
                                <input type="range" id="price-slider" min="100" max="3000" value="500" style="width:100%;" oninput="UI.updatePreview()">
                                <button class="btn btn-success btn-full" style="margin-top: 15px;" onclick="Game.createPhone()">🚀 ЗАПУСТИТИ У ПРОДАЖ</button>
                            </div>
                        </div>
                    </div>
                </div>

                <div id="tab-research" class="view-tab hidden">
                    <div class="card">
                        <h3><i class="fas fa-flask"></i> Технологічне дерево</h3>
                        <p style="color:var(--text-muted)">Витрачайте гроші на нові патенти.</p>
                        <div id="research-container" style="margin-top: 20px; display: flex; flex-direction: column; gap: 10px;"></div>
                    </div>
                </div>

                <div id="tab-market" class="view-tab hidden">
                    <div class="card">
                        <h3><i class="fas fa-globe-americas"></i> Глобальний Ринок</h3>
                        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-top: 15px;">
                            
                            <div style="display: flex; flex-direction: column; gap: 10px;">
                                <div style="background: rgba(255,255,255,0.03); padding: 10px; border-radius: 6px; display: flex; justify-content: space-between; align-items: center;">
                                    <div>
                                        <div style="font-weight: bold;">Маркетинг кампанія</div>
                                        <div style="font-size: 0.8rem; color: var(--text-muted);">+50% продажів на тиждень</div>
                                    </div>
                                    <button class="btn btn-primary" onclick="Game.buyMarketing()">$5,000</button>
                                </div>

                                <div style="background: rgba(255,255,255,0.03); padding: 10px; border-radius: 6px; display: flex; justify-content: space-between; align-items: center;">
                                    <div>
                                        <div style="font-weight: bold;">Кредит МВФ</div>
                                        <div style="font-size: 0.8rem; color: var(--text-muted);">Отримати $50k (втрата фанів)</div>
                                    </div>
                                    <button class="btn btn-red" onclick="Game.takeLoan()">Взяти</button>
                                </div>
                            </div>

                            <div style="background: rgba(0,0,0,0.3); padding: 15px; border-radius: 8px;">
                                <h4 style="margin-bottom: 10px; color: var(--gold, #fbbf24);">🏆 TOP 5 CEO</h4>
                                <div id="leaderboard"></div>
                            </div>

                        </div>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <div id="toast-area"></div>

    <script>
        /* --- GAME CONFIG --- */
        const DB = {
            techs: {
                screen: [
                    { name: 'LCD Basic', cost: 20, score: 10, req: 0 },
                    { name: 'IPS HD', cost: 50, score: 25, req: 5000 },
                    { name: 'AMOLED', cost: 90, score: 50, req: 20000 },
                    { name: 'Retina 4K', cost: 160, score: 90, req: 75000 },
                    { name: 'Holographic', cost: 300, score: 200, req: 300000 }
                ],
                cpu: [
                    { name: 'Single Core', cost: 15, score: 10, req: 0 },
                    { name: 'Quad Core', cost: 40, score: 30, req: 5000 },
                    { name: 'Octa Dragon', cost: 80, score: 60, req: 20000 },
                    { name: 'AI Neural', cost: 150, score: 120, req: 75000 },
                    { name: 'Quantum Chip', cost: 400, score: 300, req: 300000 }
                ],
                cam: [
                    { name: '2 MP', cost: 10, score: 5, req: 0 },
                    { name: '12 MP', cost: 35, score: 20, req: 5000 },
                    { name: 'Triple Lens', cost: 70, score: 55, req: 20000 },
                    { name: '108 MP Pro', cost: 130, score: 100, req: 75000 },
                    { name: 'Eye Bionic', cost: 250, score: 250, req: 300000 }
                ]
            }
        };

        let State = {
            nick: "Player",
            company: "Company",
            avatar: "👨‍💼",
            money: 25000,
            fans: 100,
            day: 1,
            phones: [],
            techLevel: { screen: 0, cpu: 0, cam: 0 },
            marketing: 0
        };

        let Selected = { screen: 0, cpu: 0, cam: 0 };
        let GameLoop = null;

        /* --- LOGIN SYSTEM --- */
        const Login = {
            avatar: "👨‍💼",
            init: () => {
                setTimeout(() => {
                    document.getElementById('loader').classList.add('hidden');
                    document.getElementById('login-form').classList.remove('hidden');
                }, 1500); // Simulate server connection
            },
            selectAvatar: (el, icon) => {
                document.querySelectorAll('.avatar-option').forEach(e => e.classList.remove('selected'));
                el.classList.add('selected');
                Login.avatar = icon;
            },
            submit: () => {
                const nick = document.getElementById('inp-nick').value;
                const comp = document.getElementById('inp-comp').value;
                if(!nick || !comp) return alert("Заповніть всі поля!");
                
                State.nick = nick;
                State.company = comp;
                State.avatar = Login.avatar;
                
                Login.enterGame();
            },
            enterGame: () => {
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('app').classList.remove('hidden');
                setTimeout(() => document.getElementById('app').style.opacity = 1, 100);
                
                UI.updateProfile();
                UI.renderTech();
                Game.start();
                Chat.init();
            },
            load: () => {
                const save = localStorage.getItem('tycoonOnlineSave');
                if(save) {
                    State = JSON.parse(save);
                    Login.enterGame();
                    UI.toast("Збереження завантажено", "success");
                } else alert("Збережень не знайдено");
            }
        };

        /* --- GAME LOGIC --- */
        const Game = {
            start: () => {
                UI.updateHeader();
                UI.renderResearch();
                if(GameLoop) clearInterval(GameLoop);
                GameLoop = setInterval(Game.tick, 1000); // 1 sec = 1 day
            },

            tick: () => {
                State.day++;
                
                // Sales logic
                let totalProfit = 0;
                State.phones.forEach(p => {
                    if(!p.active) return;
                    
                    const ageFactor = Math.max(0, 1 - (p.age / 100)); // Phone dies after 100 days
                    const techFactor = p.score / 10;
                    const market = (1 + State.marketing);
                    
                    // Simple sales formula
                    let dailySales = Math.floor(Math.random() * 10 * ageFactor * market * (State.fans / 200));
                    
                    if(p.price > p.score * 5) dailySales = 0; // Too expensive
                    
                    if(dailySales > 0) {
                        const revenue = dailySales * (p.price - p.cost);
                        p.sold += dailySales;
                        p.revenue += revenue;
                        totalProfit += revenue;
                    }

                    p.age++;
                    if(p.age > 100) {
                        p.active = false;
                        UI.toast(`Продажі ${p.name} зупинено (застарів)`, "error");
                    }
                });

                State.money += totalProfit;
                if(State.marketing > 0) State.marketing -= 0.02;

                // Random events
                if(Math.random() < 0.05) Chat.botSpeak(); 
                if(State.day % 10 === 0) UI.updateLeaderboard();

                UI.updateHeader();
                UI.updatePhones();
            },

            createPhone: () => {
                const s = DB.techs.screen[Selected.screen];
                const c = DB.techs.cpu[Selected.cpu];
                const m = DB.techs.cam[Selected.cam];
                
                const unitCost = s.cost + c.cost + m.cost;
                const name = document.getElementById('new-phone-name').value || `Phone-${State.phones.length+1}`;
                const price = parseInt(document.getElementById('price-slider').value);
                const score = s.score + c.score + m.score;
                
                const devCost = 5000 * (1 + State.phones.length * 0.2); // Each phone harder to make

                if(State.money < devCost) return UI.toast(`Треба $${devCost.toLocaleString()} на розробку!`, "error");

                State.money -= devCost;
                const newPhone = {
                    id: Date.now(),
                    name, price, cost: unitCost, score,
                    sold: 0, revenue: 0, age: 0, active: true
                };
                
                State.phones.unshift(newPhone);
                State.fans += Math.floor(score / 5);

                UI.toast(`Смартфон ${name} випущено!`, "success");
                UI.tab('office');
                
                // Chat reaction
                setTimeout(() => Chat.reactionToRelease(name, price, score), 2000);
            },

            unlock: (cat, idx) => {
                const item = DB.techs[cat][idx];
                if(State.money >= item.req) {
                    State.money -= item.req;
                    State.techLevel[cat] = idx;
                    UI.toast(`${item.name} досліджено!`, "success");
                    UI.renderTech();
                    UI.renderResearch();
                    UI.updateHeader();
                } else {
                    UI.toast("Недостатньо коштів!", "error");
                }
            },

            buyMarketing: () => {
                if(State.money >= 5000) {
                    State.money -= 5000;
                    State.marketing += 0.5;
                    UI.toast("Маркетинг запущено!", "success");
                } else UI.toast("Бракує грошей", "error");
            },

            takeLoan: () => {
                State.money += 50000;
                State.fans = Math.max(0, State.fans - 50);
                UI.toast("Кредит взято. Фани розчаровані.", "error");
            },

            save: () => {
                localStorage.setItem('tycoonOnlineSave', JSON.stringify(State));
                UI.toast("Прогрес збережено на сервері (local)", "success");
            }
        };

        /* --- UI & RENDER --- */
        const UI = {
            tab: (id) => {
                document.querySelectorAll('.view-tab').forEach(e => e.classList.add('hidden'));
                document.getElementById(`tab-${id}`).classList.remove('hidden');
                document.querySelectorAll('.nav-btn').forEach(e => e.classList.remove('active'));
                event.currentTarget.classList.add('active');
            },

            updateProfile: () => {
                document.getElementById('ui-nick').innerText = State.nick;
                document.getElementById('ui-company-name').innerText = State.company;
                document.getElementById('ui-avatar').innerText = State.avatar;
            },

            updateHeader: () => {
                document.getElementById('ui-money').innerText = `$${Math.floor(State.money).toLocaleString()}`;
                document.getElementById('ui-fans').innerText = Math.floor(State.fans).toLocaleString();
                document.getElementById('ui-day').innerText = State.day;
            },

            renderTech: () => {
                ['screen', 'cpu', 'cam'].forEach(cat => {
                    const container = document.getElementById(`grid-${cat}`);
                    container.innerHTML = DB.techs[cat].map((t, i) => {
                        const unlocked = i <= State.techLevel[cat];
                        return `<div class="tech-item ${!unlocked ? 'locked' : ''} ${Selected[cat] === i ? 'selected' : ''}"
                                onclick="if(${unlocked}){ Selected['${cat}']=${i}; UI.renderTech(); UI.updatePreview(); }">
                                <b>${t.name}</b><br>$${t.cost}
                                </div>`;
                    }).join('');
                });
                UI.updatePreview();
            },

            updatePreview: () => {
                const s = DB.techs.screen[Selected.screen];
                const c = DB.techs.cpu[Selected.cpu];
                const m = DB.techs.cam[Selected.cam];
                const cost = s.cost + c.cost + m.cost;
                const price = document.getElementById('price-slider').value;
                
                document.getElementById('ui-cost-val').innerText = `$${cost}`;
                document.getElementById('ui-price-val').innerText = `$${price}`;
            },

            updatePhones: () => {
                const list = document.getElementById('phones-list');
                const active = State.phones.filter(p => p.active);
                
                if(active.length === 0) return list.innerHTML = `<div style="text-align:center; padding:20px; color:var(--text-muted)">Ви ще не випустили телефонів.</div>`;

                list.innerHTML = active.map(p => `
                    <div class="phone-row ${p.revenue > p.cost*p.sold ? 'success' : 'flop'}">
                        <div>
                            <div style="font-weight:bold; color:var(--primary)">${p.name}</div>
                            <div style="font-size:0.8rem; color:var(--text-muted)">Sold: ${p.sold} | Ціна: $${p.price}</div>
                        </div>
                        <div style="text-align:right">
                            <div style="color:var(--success)">+$${Math.floor(p.revenue).toLocaleString()}</div>
                        </div>
                    </div>
                `).join('');
            },

            renderResearch: () => {
                const box = document.getElementById('research-container');
                let html = "";
                ['screen', 'cpu', 'cam'].forEach(cat => {
                    const next = State.techLevel[cat] + 1;
                    if(next < DB.techs[cat].length) {
                        const t = DB.techs[cat][next];
                        html += `
                        <div style="display:flex; justify-content:space-between; align-items:center; background:#ffffff05; padding:10px; border-radius:6px;">
                            <div><b>${t.name}</b> (${cat.toUpperCase()})<br><small class="text-muted">Tech Score: +${t.score}</small></div>
                            <button class="btn btn-primary" onclick="Game.unlock('${cat}', ${next})">$${t.req.toLocaleString()}</button>
                        </div>`;
                    }
                });
                box.innerHTML = html || "<div class='text-muted'>Все досліджено!</div>";
            },

            updateLeaderboard: () => {
                // Generate fake competitors close to player money
                const playerMoney = State.money;
                const fake = [
                    { name: "TechGiant", money: playerMoney * 1.5 },
                    { name: "iSteve", money: playerMoney * 1.2 },
                    { name: State.company, money: playerMoney }, // Player
                    { name: "MicroSoft", money: playerMoney * 0.8 },
                    { name: "NoobCorp", money: playerMoney * 0.4 }
                ];
                
                fake.sort((a,b) => b.money - a.money);
                
                document.getElementById('leaderboard').innerHTML = fake.map((c, i) => `
                    <div class="leaderboard-row" style="${c.name === State.company ? 'color:var(--primary)' : ''}">
                        <span>#${i+1} ${c.name}</span>
                        <span>$${Math.floor(c.money/1000)}k</span>
                    </div>
                `).join('');
            },

            toast: (msg, type) => {
                const el = document.createElement('div');
                el.className = `toast ${type}`;
                el.innerHTML = `<i class="fas fa-${type === 'success' ? 'check-circle' : 'exclamation-circle'}"></i> <span>${msg}</span>`;
                document.getElementById('toast-area').appendChild(el);
                setTimeout(() => el.remove(), 4000);
            }
        };

        /* --- CHAT SIMULATION --- */
        const Chat = {
            bots: ['ElonMask', 'SteveJobs_Ghost', 'AndroidFan', 'TechReviewer', 'CryptoBro', 'SamsungLover'],
            phrases: [
                "Ринок сьогодні шалений!",
                "Хтось бачив акції Apple?",
                "Купую золото, продаю крипту.",
                "Коли новий айфон?",
                "Ця гра топ.",
                "Потрібен інвестор в стартап.",
            ],
            
            init: () => {
                Chat.addMsg("System", `Ласкаво просимо, ${State.nick}!`, "sys");
                UI.updateLeaderboard();
            },

            addMsg: (name, text, type = 'text') => {
                const box = document.getElementById('chat-box');
                const div = document.createElement('div');
                div.className = 'chat-msg';
                if(type === 'sys') div.innerHTML = `<div class="msg-sys">${text}</div>`;
                else div.innerHTML = `<span class="msg-user">${name}:</span> <span class="msg-text">${text}</span>`;
                
                box.appendChild(div);
                box.scrollTop = box.scrollHeight;
            },

            send: () => {
                const inp = document.getElementById('chat-input');
                if(!inp.value.trim()) return;
                Chat.addMsg(State.nick, inp.value);
                inp.value = '';
                
                // Bot reaction to player chat
                if(Math.random() > 0.7) {
                    setTimeout(() => {
                        const bot = Chat.bots[Math.floor(Math.random() * Chat.bots.length)];
                        Chat.addMsg(bot, "lol true");
                    }, 1500);
                }
            },

            botSpeak: () => {
                const bot = Chat.bots[Math.floor(Math.random() * Chat.bots.length)];
                const text = Chat.phrases[Math.floor(Math.random() * Chat.phrases.length)];
                Chat.addMsg(bot, text);
            },

            reactionToRelease: (phoneName, price, score) => {
                const bot = Chat.bots[Math.floor(Math.random() * Chat.bots.length)];
                let msg = "";
                if(score > 100) msg = `Wow! ${phoneName} виглядає потужно!`;
                else if(price > 1000) msg = `${phoneName} занадто дорогий для таких характеристик...`;
                else msg = `Хмм, ${phoneName} непоганий бюджетник.`;
                
                Chat.addMsg(bot, msg);
            }
        };

        // Start
        Login.init();

    </script>
</body>
</html>
