<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>東山竹林寺 | 廟務管理系統 (全台地址智慧連動版)</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        :root {
            --bg-main: #f8fafc;         
            --sidebar-gradient: linear-gradient(135deg, #141b2d 0%, #1f293d 100%); 
            --sidebar-active: rgba(255, 255, 255, 0.08);
            --border-red: #7f1d1d;      
            --btn-red: #7f1d1d;
            --btn-red-hover: #991b1b;
            --btn-gold: #b45309;        
            --btn-gold-hover: #d97706;
            --text-dark: #1e293b;       
            --text-muted: #64748b;      
            --card-shadow: 0 4px 20px -2px rgba(15, 23, 42, 0.04), 0 2px 6px -1px rgba(15, 23, 42, 0.02); 
        }

        * { box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Microsoft JhengHei", sans-serif; }
        body { background-color: var(--bg-main); margin: 0; padding: 0; display: flex; height: 100vh; overflow: hidden; color: var(--text-dark); -webkit-font-smoothing: antialiased; }

        /* ================= 獨立登入畫面風格 ================= */
        .login-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            padding: 20px;
        }
        .login-card {
            background-color: white;
            width: 100%;
            max-width: 420px;
            border-radius: 12px;
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.3), 0 10px 10px -5px rgba(0, 0, 0, 0.2);
            border-top: 5px solid var(--border-red);
            overflow: hidden;
            animation: fadeIn 0.4s ease-out;
        }
        .login-header {
            padding: 35px 30px 20px 30px;
            text-align: center;
            background: linear-gradient(to bottom, #fcfcfc, #ffffff);
            border-bottom: 1px solid #f1f5f9;
        }
        .login-header i { font-size: 42px; color: var(--btn-gold); margin-bottom: 12px; }
        .login-header h2 { margin: 0; font-size: 24px; color: #0f172a; letter-spacing: 4px; font-weight: 800; }
        .login-header p { margin: 6px 0 0 0; font-size: 14px; color: var(--text-muted); font-weight: 600; }
        .login-body { padding: 30px; }
        .login-error {
            background-color: #fef2f2; border: 1px solid #fca5a5; color: #ef4444;
            padding: 10px 14px; border-radius: 6px; font-size: 14px; font-weight: 700;
            margin-bottom: 20px; display: none; align-items: center; gap: 8px;
        }
        .login-form-group { display: flex; flex-direction: column; margin-bottom: 20px; }
        .login-form-group label { font-size: 14px; font-weight: 700; color: #475569; margin-bottom: 8px; }
        .login-btn {
            width: 100%; background-color: var(--btn-red); color: white; border: none; border-radius: 6px;
            height: 46px; font-size: 16px; font-weight: 700; cursor: pointer; display: inline-flex;
            align-items: center; justify-content: center; gap: 8px; transition: all 0.2s; margin-top: 10px;
            box-shadow: 0 4px 12px rgba(127,29,29,0.2);
        }
        .login-btn:hover { background-color: var(--btn-red-hover); transform: translateY(-1px); }

        /* ================= 右側主要框架 ================= */
        .main-system-wrapper { display: none; width: 100%; height: 100%; }
        .sidebar { width: 270px; background: var(--sidebar-gradient); display: flex; flex-direction: column; justify-content: space-between; flex-shrink: 0; z-index: 10; box-shadow: 4px 0 24px rgba(15, 23, 42, 0.05); }
        .sidebar-brand { padding: 40px 24px; text-align: center; border-bottom: 1px solid rgba(255,255,255,0.05); position: relative; }
        .sidebar-brand h2 { color: white; margin: 0; font-size: 22px; letter-spacing: 5px; font-weight: 700; }
        
        .sidebar-menu { list-style: none; padding: 0; margin: 25px 0; flex-grow: 1; }
        .sidebar-menu li { padding: 14px 28px; margin: 4px 16px; border-radius: 8px; cursor: pointer; display: flex; align-items: center; gap: 16px; font-size: 16px; color: #94a3b8; font-weight: 600; transition: all 0.25s; }
        .sidebar-menu li i { width: 22px; font-size: 18px; text-align: center; color: #cbd5e1; }
        .sidebar-menu li:hover { color: #f8fafc; background-color: rgba(255,255,255,0.04); }
        .sidebar-menu li.active { background: var(--sidebar-active); color: #fff; box-shadow: inset 0 0 0 1px rgba(255,255,255,0.1); }
        .sidebar-menu li.active i { color: #f59e0b; }
        
        .sidebar-menu li.permission-hidden { display: none !important; }

        .sidebar-footer { display: flex; flex-direction: column; padding: 0 16px 20px 16px; }
        .sidebar-user { padding: 14px 16px; border-radius: 8px; color: white; font-size: 14px; font-weight: 600; background: rgba(255,255,255,0.03); display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; border: 1px solid rgba(255,255,255,0.03); }
        .sidebar-user-info { display: flex; flex-direction: column; gap: 4px; }
        .sidebar-user .user-role { font-size: 11px; color: #f59e0b; background: rgba(245,158,11,0.15); padding: 2px 6px; border-radius: 4px; width: fit-content; }
        .btn-change-pwd { background: transparent; border: none; color: #94a3b8; cursor: pointer; font-size: 16px; padding: 6px; border-radius: 4px; transition: all 0.2s; }
        .btn-change-pwd:hover { color: #f59e0b; background: rgba(255,255,255,0.05); }
        .sidebar-logout { padding: 14px; border-radius: 8px; color: white; background-color: #ef4444; text-align: center; font-size: 15px; font-weight: 700; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; transition: all 0.2s; }
        .sidebar-logout:hover { background-color: #dc2626; }

        .main-content { flex-grow: 1; display: flex; flex-direction: column; overflow: hidden; }
        .top-bar { padding: 20px 40px; display: flex; justify-content: space-between; align-items: center; background-color: white; border-bottom: 1px solid #e2e8f0; }
        .top-time { font-size: 26px; font-weight: 800; color: var(--border-red); letter-spacing: 2px; }
        .top-lunar { font-size: 15px; color: #475569; font-weight: 600; background-color: #f1f5f9; padding: 6px 14px; border-radius: 20px; }

        .content-body { flex-grow: 1; padding: 35px 40px; overflow-y: auto; }

        /* ================= 頂部數據卡片 ================= */
        .stat-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 24px; margin-bottom: 35px; }
        .stat-card { background-color: white; border-radius: 10px; padding: 24px; display: flex; align-items: center; gap: 20px; border: 1px solid #e2e8f0; box-shadow: var(--card-shadow); }
        .stat-icon { font-size: 24px; color: #d97706; width: 54px; height: 54px; background-color: #fef3c7; border-radius: 50%; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
        .stat-card:nth-child(3) .stat-icon, .stat-card:nth-child(4) .stat-icon { color: #15803d; background-color: #dcfce7; }
        .stat-info h3 { margin: 0; font-size: 28px; font-weight: 800; color: #0f172a; font-variant-numeric: tabular-nums; }
        .stat-info p { margin: 4px 0 0 0; font-size: 13px; color: var(--text-muted); font-weight: 700; }

        /* ================= 面板與表單 ================= */
        .system-panel { display: none; }
        .system-panel.active { display: block; animation: fadeIn 0.3s ease-in-out; }
        .card-box { background-color: white; border-radius: 10px; border: 1px solid #e2e8f0; box-shadow: var(--card-shadow); margin-bottom: 35px; overflow: hidden; }
        .card-box.border-red { border-top: 4px solid var(--border-red); }
        .card-box.border-gold { border-top: 4px solid #d97706; }
        .card-header { padding: 20px 28px; border-bottom: 1px solid #f1f5f9; display: flex; justify-content: space-between; align-items: center; }
        .card-header h3 { margin: 0; font-size: 17px; font-weight: 700; color: #0f172a; display: flex; align-items: center; gap: 10px; }
        .card-body { padding: 28px; }

        .form-row { display: flex; flex-wrap: wrap; gap: 20px; margin-bottom: 24px; }
        .form-group { flex: 1; min-width: 160px; display: flex; flex-direction: column; }
        .form-group.btn-align { flex: 0 0 auto; justify-content: flex-end; }
        
        /* 十一宮格智慧流式佈局 */
        .form-row.address-single-line { display: flex; flex-wrap: wrap; align-items: flex-end; gap: 10px; }
        .flex-city { flex: 1.4; min-width: 110px; } 
        .flex-district { flex: 1.4; min-width: 110px; }
        .flex-village { flex: 1.2; min-width: 90px; }
        .flex-neighbor { flex: 0.8; min-width: 70px; }
        .flex-road { flex: 1.8; min-width: 140px; } 
        .flex-section { flex: 0.8; min-width: 65px; }
        .flex-lane { flex: 0.8; min-width: 65px; } 
        .flex-alley { flex: 0.8; min-width: 65px; }
        .flex-number { flex: 0.9; min-width: 75px; } 
        .flex-floor { flex: 0.8; min-width: 65px; }
        .flex-room { flex: 1.0; min-width: 85px; }
        .flex-btn-save { flex: 2.0; min-width: 180px; }
        .flex-btn-save button { width: 100%; height: 44px; }

        label { font-size: 14px; font-weight: 700; color: #334155; margin-bottom: 8px; }
        .input-icon-group { position: relative; display: flex; align-items: center; }
        .input-icon-group i { position: absolute; left: 14px; color: #94a3b8; font-size: 16px; pointer-events: none; }
        .input-icon-group input, .input-icon-group select { padding-left: 40px; }

        input, select { width: 100%; padding: 10px 14px; font-size: 15px; border: 1px solid #cbd5e1; border-radius: 6px; background-color: #fff; height: 44px; outline: none; }
        input:focus, select:focus { border-color: #64748b; box-shadow: 0 0 0 3px rgba(100, 116, 139, 0.15); }

        /* 按鈕系統 */
        .btn-red { background-color: var(--btn-red); color: white; padding: 0 24px; border: none; border-radius: 6px; font-size: 15px; font-weight: 700; cursor: pointer; height: 44px; display: inline-flex; align-items: center; justify-content: center; gap: 8px; box-shadow: 0 4px 12px rgba(127,29,29,0.15); }
        .btn-gold { background-color: var(--btn-gold); color: white; padding: 0 24px; border: none; border-radius: 6px; font-size: 15px; font-weight: 700; cursor: pointer; height: 44px; display: inline-flex; align-items: center; justify-content: center; gap: 8px; box-shadow: 0 4px 12px rgba(180,83,9,0.15); }
        .btn-outline { background-color: #fff; border: 1px solid #cbd5e1; color: #475569; padding: 0 20px; border-radius: 6px; cursor: pointer; height: 40px; font-size: 14px; font-weight: 700; display: inline-flex; align-items: center; gap: 6px; }
        .btn-edit { background-color: #fff; border: 1px solid #cbd5e1; color: #0284c7; padding: 6px 14px; border-radius: 6px; cursor: pointer; font-size: 13px; font-weight: 700; }
        .btn-delete { background-color: #fff; border: 1px solid #fca5a5; color: #ef4444; padding: 6px 14px; border-radius: 6px; cursor: pointer; font-size: 13px; font-weight: 700; }

        .search-box { position: relative; width: 300px; display: flex; align-items: center; }
        .search-box i { position: absolute; left: 14px; color: #94a3b8; }
        .search-box input { height: 38px; padding-left: 38px; font-size: 14px; }

        /* ================= 表格排版 ================= */
        .table-wrapper { width: 100%; overflow-x: auto; border-radius: 8px; border: 1px solid #e2e8f0; }
        table { width: 100%; border-collapse: collapse; text-align: left; font-size: 15px; background: white; }
        th, td { padding: 16px 24px; border-bottom: 1px solid #e2e8f0; vertical-align: middle; }
        th { background-color: #f8fafc; color: #334155; font-weight: 700; }
        tr:nth-child(even) { background-color: #f8fafc; }

        /* ================= 系統日誌與狀態 ================= */
        .setting-row { display: flex; justify-content: space-between; align-items: center; padding: 22px 0; border-bottom: 1px solid #f1f5f9; }
        .setting-row:last-child { border-bottom: none; }
        .setting-info h4 { margin: 0 0 6px 0; font-size: 15px; color: #0f172a; }
        .setting-info p { margin: 0; font-size: 13px; color: var(--text-muted); }
        .status-text { font-size: 15px; font-weight: 700; color: #b45309; margin-bottom: 16px; display: flex; align-items: center; gap: 8px; }
        .log-terminal { background-color: #0f172a; color: #94a3b8; border-radius: 8px; padding: 20px; font-family: monospace; font-size: 13px; height: 180px; overflow-y: auto; }
        .log-time { color: #38bdf8; margin-right: 12px; }
        .log-msg { color: #34d399; }

        /* ================= 密碼修改彈出視窗 Style ================= */
        .modal-overlay { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(4px); z-index: 10000; justify-content: center; align-items: center; }
        .modal-box { background: white; border-radius: 12px; width: 100%; max-width: 400px; box-shadow: 0 20px 25px -5px rgba(0,0,0,0.2); overflow: hidden; border-top: 5px solid var(--btn-gold); animation: fadeIn 0.3s ease-out; }
        .modal-header { padding: 20px 24px; border-bottom: 1px solid #f1f5f9; display: flex; justify-content: space-between; align-items: center; }
        .modal-header h3 { margin: 0; font-size: 18px; font-weight: 700; color: #0f172a; }
        .modal-box button.modal-close { background: transparent; border: none; font-size: 18px; color: #94a3b8; cursor: pointer; }
        .modal-box button.modal-close:hover { color: #ef4444; }
        .modal-body { padding: 24px; }
        .modal-footer { padding: 16px 24px; background: #f8fafc; border-top: 1px solid #f1f5f9; display: flex; justify-content: flex-end; gap: 12px; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(4px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

    <div class="login-container" id="loginPanel">
        <div class="login-card">
            <div class="login-header">
                <i class="fa-solid fa-gopuram"></i>
                <h2>竹林寺管理系統</h2>
                <p>請輸入您的帳號密碼進行權限登入</p>
            </div>
            <div class="login-body">
                <div class="login-error" id="loginError">
                    <i class="fa-solid fa-triangle-exclamation"></i> 帳號或密碼錯誤，請重新確認！
                </div>
                <form id="loginForm" onsubmit="handleLogin(event)">
                    <div class="login-form-group">
                        <label>登入帳號</label>
                        <div class="input-icon-group">
                            <i class="fa-solid fa-user-shield"></i>
                            <input type="text" id="loginUsername" placeholder="使用者帳號" required autocomplete="username">
                        </div>
                    </div>
                    <div class="login-form-group">
                        <label>驗證密碼</label>
                        <div class="input-icon-group">
                            <i class="fa-solid fa-lock"></i>
                            <input type="password" id="loginPassword" placeholder="密碼" required autocomplete="current-password">
                        </div>
                    </div>
                    <button type="submit" class="login-btn">
                        <i class="fa-solid fa-right-to-bracket"></i> 安全驗證並登入
                    </button>
                </form>
            </div>
        </div>
    </div>

    <div class="main-system-wrapper" id="mainSystemWrapper">
        <nav class="sidebar">
            <div class="sidebar-top">
                <div class="sidebar-brand">
                    <h2>竹林寺系統</h2>
                </div>
                <ul class="sidebar-menu">
                    <li id="menu-dashboard" class="active" onclick="switchTab('dashboard')"><i class="fa-solid fa-chart-pie"></i> 數據主控台</li>
                    <li id="menu-believers" onclick="switchTab('believers')"><i class="fa-solid fa-users"></i> 信眾管理</li>
                    <li id="menu-lighting" onclick="switchTab('lighting')"><i class="fa-solid fa-om"></i> 點燈登記</li>
                    <li id="menu-finance" onclick="switchTab('finance')"><i class="fa-solid fa-coins"></i> 財務/香油錢</li>
                    <li id="menu-system" onclick="switchTab('system')"><i class="fa-solid fa-screwdriver-wrench"></i> 系統維護</li>
                </ul>
            </div>
            <div class="sidebar-footer">
                <div class="sidebar-user">
                    <div class="sidebar-user-info">
                        <div><i class="fa-solid fa-circle-user"></i> <span id="sidebar-admin-name">管理員</span></div>
                        <span class="user-role" id="sidebar-user-role">超級管理員</span>
                    </div>
                    <button class="btn-change-pwd" onclick="openPwdModal()" title="修改個人密碼"><i class="fa-solid fa-gear"></i></button>
                </div>
                <div class="sidebar-logout" onclick="triggerLogout()"><i class="fa-solid fa-power-off"></i> 登出系統</div>
            </div>
        </nav>

        <main class="main-content">
            <div class="top-bar">
                <div class="top-time" id="liveClock">00:00:00</div>
                <div class="top-lunar"><i class="fa-solid fa-calendar-days" style="color:#64748b; margin-right:6px;"></i> 丙午年(馬) 四月二十</div>
            </div>

            <div class="content-body">
                <div class="stat-grid">
                    <div class="stat-card">
                        <div class="stat-icon"><i class="fa-solid fa-users"></i></div>
                        <div class="stat-info"><h3 id="card-b-count">0</h3><p>總信眾人數</p></div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon"><i class="fa-solid fa-lightbulb"></i></div>
                        <div class="stat-info"><h3 id="card-l-count">0</h3><p>年度點燈總數</p></div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon"><i class="fa-solid fa-hand-holding-dollar"></i></div>
                        <div class="stat-info"><h3>$<span id="card-revenue">0</span></h3><p>累計總收入</p></div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon"><i class="fa-solid fa-wallet"></i></div>
                        <div class="stat-info"><h3>$<span id="card-balance">0</span></h3><p>目前可用結餘</p></div>
                    </div>
                </div>

                <section id="dashboard" class="system-panel active">
                    <div class="card-box border-red">
                        <div class="card-header"><h3><i class="fa-solid fa-circle-check" style="color:#16a34a;"></i> 歡迎使用廟務整合管理系統</h3></div>
                        <div class="card-body">
                            <p style="margin: 0; color: #475569; font-size: 15px; line-height: 1.7;">請透過左側功能選單切換至對應功能區塊。系統會自動根據您的【帳號權限】調整可見功能，無權限之章節將自動遮蔽以策安全。</p>
                        </div>
                    </div>
                </section>

                <section id="believers" class="system-panel">
                    <div class="card-box border-red">
                        <div class="card-header"><h3 id="believer-form-title"><i class="fa-solid fa-user-plus"></i> 新增信眾檔案</h3></div>
                        <div class="card-body">
                            <form id="believerForm">
                                <input type="hidden" id="b-id">
                                <div class="form-row">
                                    <div class="form-group">
                                        <label>姓名</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-user"></i><input type="text" id="b-name" placeholder="請輸入姓名" required></div>
                                    </div>
                                    <div class="form-group">
                                        <label>聯絡電話</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-phone"></i><input type="tel" id="b-phone" placeholder="如 0912345678" required></div>
                                    </div>
                                    <div class="form-group">
                                        <label>國曆生日</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-calendar-days"></i><input type="date" id="b-birth" onchange="handleBirthChange()" required></div>
                                    </div>
                                    <div class="form-group">
                                        <label>生肖屬相</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-dragon"></i><input type="text" id="b-zodiac" readonly style="background-color: #f8fafc;" placeholder="自動推算"></div>
                                    </div>
                                </div>
                                
                                <div class="form-row address-single-line">
                                    <div class="form-group flex-city">
                                        <label>縣市</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-map-location-dot"></i>
                                            <input type="text" id="b-city" list="city-list" placeholder="手打/選取" oninput="handleCityInput()" required>
                                            <datalist id="city-list"></datalist>
                                        </div>
                                    </div>
                                    <div class="form-group flex-district">
                                        <label>區/鄉/鎮</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-map"></i>
                                            <input type="text" id="b-district" list="district-list" placeholder="手打/選取" oninput="handleDistrictInput()" required>
                                            <datalist id="district-list"></datalist>
                                        </div>
                                    </div>
                                    <div class="form-group flex-village"><label>村/里</label><input type="text" id="b-village" placeholder="如 東山里"></div>
                                    <div class="form-group flex-neighbor"><label>鄰</label><input type="number" id="b-neighbor" placeholder="如 5" min="1"></div>
                                    
                                    <div class="form-group flex-road">
                                        <label>路/街</label>
                                        <input type="text" id="b-road" list="road-list" placeholder="如 中山路">
                                        <datalist id="road-list"></datalist>
                                    </div>
                                    
                                    <div class="form-group flex-section"><label>段</label><input type="text" id="b-section" placeholder="如 二段"></div>
                                    <div class="form-group flex-lane"><label>巷</label><input type="text" id="b-lane" placeholder="如 12巷"></div>
                                    <div class="form-group flex-alley"><label>弄</label><input type="text" id="b-alley" placeholder="如 5弄"></div>
                                    <div class="form-group flex-number"><label>號</label><input type="text" id="b-number" placeholder="如 100號"></div>
                                    <div class="form-group flex-floor"><label>樓</label><input type="text" id="b-floor" placeholder="如 5樓"></div>
                                    <div class="form-group flex-room"><label>室/之</label><input type="text" id="b-room" placeholder="如 B室"></div>
                                    <div class="form-group flex-btn-save">
                                        <button type="submit" id="btn-believer-submit" class="btn-red"><i class="fa-solid fa-floppy-disk"></i> 儲存檔案</button>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>

                    <div class="card-box border-red">
                        <div class="card-header">
                            <h3><i class="fa-solid fa-address-book"></i> 信眾檔案名冊庫</h3>
                            <div class="search-box"><i class="fa-solid fa-magnifying-glass"></i><input type="text" id="search-b" placeholder="輸入關鍵字搜尋..." oninput="searchBelievers()"></div>
                        </div>
                        <div class="card-body">
                            <div class="table-wrapper">
                                <table>
                                    <thead><tr><th style="width:100px;">編號</th><th>姓名</th><th>聯絡電話</th><th>完整聯絡地址</th><th style="width:160px; text-align:center;">操作管理</th></tr></thead>
                                    <tbody id="tbody-believers"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="lighting" class="system-panel">
                    <div class="card-box border-red">
                        <div class="card-header"><h3 id="light-form-title"><i class="fa-solid fa-feather"></i> 線上點燈祈福登記</h3></div>
                        <div class="card-body">
                            <form id="lightForm">
                                <input type="hidden" id="l-id">
                                <div class="form-row" style="align-items: flex-end;">
                                    <div class="form-group" style="flex: 1.2; min-width: 150px;">
                                        <label>登記日期</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-calendar-day"></i><input type="date" id="l-date" required></div>
                                    </div>
                                    <div class="form-group">
                                        <label>祈福信眾姓名 (連動名冊)</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-user-check"></i><select id="l-name-select" required><option value="">-- 請選擇信眾 --</option></select></div>
                                    </div>
                                    <div class="form-group">
                                        <label>點燈項目</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-circle-dot"></i><select id="l-type"><option value="光明燈">光明燈</option><option value="太歲燈">太歲燈</option><option value="文昌燈">文昌燈</option></select></div>
                                    </div>
                                    <div class="form-group">
                                        <label>功德金金額</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-dollar-sign"></i><input type="number" id="l-amount" value="200" required></div>
                                    </div>
                                    <div class="form-group btn-align">
                                        <button type="submit" id="btn-light-submit" class="btn-red"><i class="fa-solid fa-check"></i> 確認登記</button>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>

                    <div class="card-box border-red">
                        <div class="card-header"><h3><i class="fa-solid fa-rectangle-list"></i> 本年度點燈歷史紀錄明細</h3></div>
                        <div class="card-body">
                            <div class="table-wrapper">
                                <table>
                                    <thead><tr><th>登記日期</th><th>信眾姓名</th><th>祈福項目</th><th>收受功德金</th><th style="width:160px; text-align:center;">操作</th></tr></thead>
                                    <tbody id="tbody-lighting"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="finance" class="system-panel">
                    <div class="card-box border-gold">
                        <div class="card-header"><h3 id="finance-form-title"><i class="fa-solid fa-heart" style="color:#d97706;"></i> 香油錢登錄 / 常規收支錄入</h3></div>
                        <div class="card-body">
                            <form id="ioForm">
                                <input type="hidden" id="io-id">
                                <div class="form-row" style="align-items: flex-end;">
                                    <div class="form-group" style="flex: 1.2; min-width: 150px;">
                                        <label>入帳日期</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-calendar-day"></i><input type="date" id="io-date" required></div>
                                    </div>
                                    <div class="form-group" style="flex: 2.5; min-width: 250px;">
                                        <label>摘要說明 / 捐獻大德姓名</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-pen-to-square"></i><input type="text" id="io-note" placeholder="例：王大德隨喜香油錢" required></div>
                                    </div>
                                    <div class="form-group" style="flex: 1.5; min-width: 160px;">
                                        <label>帳目類型</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-layer-group"></i><select id="io-type"><option value="收入">隨喜/常規收入</option><option value="支出">常規廟務支出</option></select></div>
                                    </div>
                                    <div class="form-group" style="flex: 1.2; min-width: 120px;">
                                        <label>金額</label>
                                        <div class="input-icon-group"><i class="fa-solid fa-calculator"></i><input type="number" id="io-amount" required></div>
                                    </div>
                                    <div class="form-group btn-align">
                                        <button type="submit" id="btn-io-submit" class="btn-red"><i class="fa-solid fa-arrow-down-up-lock"></i> 確認記帳</button>
                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>

                    <div class="card-box border-red">
                        <div class="card-header"><h3><i class="fa-solid fa-scale-balanced"></i> 廟務收支財務流水帳明細</h3></div>
                        <div class="card-body">
                            <div class="table-wrapper">
                                <table>
                                    <thead><tr><th>入帳日期</th><th>明細摘要 / 捐獻大德</th><th style="width:200px;">款項異動金額</th><th style="width:160px; text-align:center;">操作</th></tr></thead>
                                    <tbody id="tbody-finance"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                </section>

                <section id="system" class="system-panel">
                    <div class="card-box border-gold">
                        <div class="card-header"><h3><i class="fa-solid fa-users-gear" style="color:#b45309;"></i> 系統帳號多開與權限分配設定</h3></div>
                        <div class="card-body">
                            <form id="userManageForm" onsubmit="handleCreateUser(event)">
                                <div class="form-row">
                                    <div class="form-group">
                                        <label>使用者帳號</label>
                                        <div class="input-icon-group">
                                            <i class="fa-solid fa-user-plus"></i>
                                            <input type="text" id="newUsername" placeholder="請輸入登入帳號" required>
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label>初始密碼</label>
                                        <div class="input-icon-group">
                                            <i class="fa-solid fa-key"></i>
                                            <input type="password" id="newUserPassword" placeholder="請輸入密碼" required>
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label>指定系統權限</label>
                                        <div class="input-icon-group">
                                            <i class="fa-solid fa-user-lock"></i>
                                            <select id="newUserRole">
                                                <option value="admin">超級管理員 (可看全系統、具備權限變更)</option>
                                                <option value="staff">一般廟務人員 (僅信眾、點燈登記，隱藏財務與維護)</option>
                                                <option value="finance">財務會計人員 (僅數據主控台與財務香油錢功能)</option>
                                            </select>
                                        </div>
                                    </div>
                                    <div class="form-group btn-align">
                                        <button type="submit" class="btn-gold"><i class="fa-solid fa-user-check"></i> 新增帳號配給</button>
                                    </div>
                                </div>
                            </form>

                            <div class="table-section-title" style="margin-top: 25px;"><i class="fa-solid fa-id-badge"></i> 目前系統已開立帳號清冊</div>
                            <div class="table-wrapper">
                                <table>
                                    <thead>
                                        <tr>
                                            <th>登入帳號</th>
                                            <th>當前角色權限</th>
                                            <th style="width:160px; text-align:center;">帳戶管理</th>
                                        </tr>
                                    </thead>
                                    <tbody id="tbody-users"></tbody>
                                </table>
                            </div>
                        </div>
                    </div>

                    <div class="card-box border-red">
                        <div class="card-header"><h3><i class="fa-solid fa-shield-halved"></i> 系統安全與核心資料維護機制</h3></div>
                        <div class="card-body" style="padding: 10px 28px 28px 28px;">
                            <div class="setting-row">
                                <div class="setting-info"><h4>安全導出數據備份</h4><p>將目前資料庫完整打包導出為標準安全 JSON 檔案</p></div>
                                <button class="btn-outline" onclick="exportBackup()"><i class="fa-solid fa-cloud-arrow-down"></i> 導出本地備份</button>
                            </div>
                            <div class="setting-row">
                                <div class="setting-info"><h4>外部數據備份還原</h4><p>選取先前導出的 JSON 備份檔，覆蓋系統檔案</p></div>
                                <button class="btn-outline" onclick="document.getElementById('importFile').click()"><i class="fa-solid fa-folder-open"></i> 選擇備份檔案</button>
                                <input type="file" id="importFile" accept=".json" style="display:none;" onchange="importBackup(this)">
                            </div>
                            <div class="setting-row">
                                <div class="setting-info"><h4>全面格式化系統快取</h4><p style="color:#ef4444; margin:0; font-size:13px;">此項操作將抹除本機所有檔案紀錄與開設帳號</p></div>
                                <button class="btn-red" style="background-color:#ef4444;" onclick="resetSystem()"><i class="fa-solid fa-trash-can"></i> 執行系統重置</button>
                            </div>
                        </div>
                    </div>

                    <div class="card-box border-red">
                        <div class="card-header"><h3><i class="fa-solid fa-terminal"></i> 系統核心安全監測狀態日誌</h3></div>
                        <div class="card-body">
                            <div class="status-text">權限稽核模組：安全防護已啟動</div>
                            <div class="log-terminal" id="logTerminal"></div>
                        </div>
                    </div>
                </section>
            </div>
        </main>
    </div>

    <div class="modal-overlay" id="pwdModal">
        <div class="modal-box">
            <div class="modal-header">
                <h3><i class="fa-solid fa-key" style="color:var(--btn-gold); margin-right:6px;"></i> 修改個人登入密碼</h3>
                <button class="modal-close" onclick="closePwdModal()">&times;</button>
            </div>
            <form id="changePwdForm" onsubmit="handleChangePassword(event)">
                <div class="modal-body">
                    <div class="login-form-group" style="margin-bottom:15px;">
                        <label>輸入目前舊密碼</label>
                        <div class="input-icon-group">
                            <i class="fa-solid fa-unlock"></i>
                            <input type="password" id="oldPassword" required placeholder="目前舊密碼">
                        </div>
                    </div>
                    <div class="login-form-group" style="margin-bottom:15px;">
                        <label>設定全新密碼</label>
                        <div class="input-icon-group">
                            <i class="fa-solid fa-lock"></i>
                            <input type="password" id="newPassword" required placeholder="欲變更的新密碼">
                        </div>
                    </div>
                    <div class="login-form-group" style="margin-bottom:5px;">
                        <label>再次確認全新密碼</label>
                        <div class="input-icon-group">
                            <i class="fa-solid fa-shield"></i>
                            <input type="password" id="confirmPassword" required placeholder="再次重複輸入新密碼">
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn-outline" style="height:38px;" onclick="closePwdModal()">取消</button>
                    <button type="submit" class="btn-gold" style="height:38px; padding:0 16px;">變更並儲存</button>
                </div>
            </form>
        </div>
    </div>

    <script>
        // ================= 全台縣市、鄉鎮市區、精選指標路名資料庫 =================
        const taiwanDistrictsAndRoads = {
            "基隆市": {
                "仁愛區": ["愛一路", "愛二路", "愛三路", "仁一路", "仁二路", "忠一路", "孝三路"],
                "信義區": ["信一路", "東信路", "培德路", "深溪路", "深澳坑路"],
                "中正區": ["中正路", "北寧路", "祥豐街", "正濱路", "新豐街"],
                "中山區": ["中山一路", "中山二路", "西定路", "復興路", "文化路"],
                "安樂區": ["基金一路", "基金二路", "麥金路", "安一路", "樂利二街"],
                "暖暖區": ["暖暖街", "源遠路", "東勢街", "過港路"],
                "七堵區": ["明德一路", "光明路", "自治街", "東新街", "俊賢路"]
            },
            "臺北市": {
                "中正區": ["忠孝西路", "中華路一段", "重慶南路", "羅斯福路", "仁愛路一段", "凱達格蘭大道"],
                "大同區": ["民生西路", "南京西路", "重慶北路", "延平北路", "承德路"],
                "中山區": ["中山北路", "民權東路", "南京東路", "建國北路", "松江路", "林森北路"],
                "松山區": ["敦化北路", "民生東路", "光復北路", "八德路", "三民路"],
                "大安區": ["敦化南路", "信義路", "和平東路", "忠孝東路三段", "新生南路"],
                "萬華區": ["康定路", "西園路", "艋舺大道", "萬大路", "西寧南路"],
                "信義區": ["信義路五段", "忠孝東路五段", "基隆路二段", "松仁路", "松高路"],
                "士林區": ["中山北路五段", "中正路", "基河路", "天母東路", "延平北路五段"],
                "北投區": ["中央北路", "光明路", "泉源路", "石牌路", "大業路"],
                "內湖區": ["成功路", "民權東路六段", "瑞光路", "內湖路", "港墘路"],
                "南港區": ["南港路", "忠孝東路七段", "研究院路", "重陽路"],
                "文山區": ["木柵路", "指南路", "興隆路", "景文街", "羅斯福路五段"]
            },
            "新北市": {
                "板橋區": ["文化路", "中山路", "縣民大道", "雙十路", "四川路", "大觀路"],
                "三重區": ["重新路", "正義北路", "三和路", "集賢路", "中正北路"],
                "中和區": ["中正路", "景平路", "連城路", "中山路二段", "員山路"],
                "永和區": ["永和路", "得和路", "中正路", "竹林路", "保平路"],
                "新莊區": ["中正路", "新北大道", "幸福路", "思源路", "化成路"],
                "新店區": ["北新路", "中興路", "安康路", "中央路", "建國路"],
                "汐止區": ["新台五路", "大同路", "康寧街", "福德一路"],
                "土城區": ["金城路", "中央路", "青雲路", "明德路"],
                "蘆洲區": ["三民路", "中山一路", "長榮路", "集賢路"],
                "淡水區": ["中正路", "中山路", "淡金路", "清水街", "學府路"],
                "樹林區": ["中山路", "大安路", "保安街", "中正路"],
                "鶯歌區": ["中山路", "鶯桃路", "建國路", "文化路"],
                "三峽區": ["復興路", "文化路", "民生街", "介壽路"],
                "五股區": ["成泰路", "工商路", "五工路"],
                "泰山區": ["明志路", "泰林路"],
                "林口區": ["文化一路", "文化二路", "文化三路", "仁愛路"],
                "八里區": ["龍米路", "中華路"],
                "深坑區": ["深坑街", "北深路"],
                "石碇區": ["石碇東街", "碇坪路"],
                "坪林區": ["坪林街", "北宜路"],
                "烏來區": ["烏來街", "瀑布路"],
                "瑞芳區": ["明燈路", "汽車路", "海濱路"],
                "平溪區": ["平溪街", "靜安路"],
                "雙溪區": ["中華路", "太平路"],
                "貢寮區": ["朝陽街", "福隆街"],
                "萬里區": ["瑪鋉路"],
                "金山區": ["中山路", "金包里街"],
                "石門區": ["中央路"],
                "三芝區": ["淡金路", "中山路"]
            },
            "桃園市": {
                "桃園區": ["中正路", "中山路", "復興路", "大興西路", "春日路", "經國路"],
                "中壢區": ["中正路", "環北路", "新生路", "元化路", "延平路", "龍東路"],
                "八德區": ["介壽路", "建國路", "興豐路"],
                "平鎮區": ["中豐路", "延平路二段", "金陵路"],
                "羅潭區": ["中正路", "大昌路", "神龍路"],
                "楊梅區": ["中山路", "校前路", "大成路"],
                "大溪區": ["中正路", "員林路", "康莊路"],
                "蘆竹區": ["中正路", "南崁路", "大竹路"],
                "大園區": ["中正路", "民生路", "國際路"],
                "龜山區": ["萬壽路", "復興一路", "文化二路"],
                "龍潭區": ["中正路", "大昌路", "神龍路"],
                "新屋區": ["中山路", "中華路"],
                "觀音區": ["中山路", "大觀路"],
                "復興區": ["中正路", "桃118線"]
            },
            "新竹市": {
                "東區": ["光復路", "新竹科學園區一路", "東門街", "中央路", "寶山路"],
                "北區": ["中正路", "北大路", "西大路", "延平路", "東大路"],
                "香山區": ["中華路四段", "牛埔路", "香大路"]
            },
            "新竹縣": {
                "竹北市": ["光明一路", "自強南路", "文興路", "中華路", "縣政九路"],
                "竹東鎮": ["長春路", "中興路", "東寧路"],
                "新埔鎮": ["中正路", "文山路"],
                "關西鎮": ["正義路", "中山路"],
                "湖口鄉": ["中山路", "中正路", "新興路"],
                "新豐鄉": ["新興路", "建興路"],
                "芎林鄉": ["文山路"],
                "橫山鄉": ["富林路"],
                "北埔鄉": ["北埔街"],
                "寶山鄉": ["雙園路"],
                "峨眉鄉": ["峨眉街"],
                "尖石鄉": ["中豐公路"],
                "五峰鄉": ["大隘村"]
            },
            "苗栗縣": {
                "苗栗市": ["中正路", "中山路", "為公路", "國華路"],
                "頭份市": ["中央路", "中正路", "自強路"],
                "竹南鎮": ["博愛街", "光復路", "中正路"],
                "後龍鎮": ["中山路"],
                "通霄鎮": ["中正路"],
                "苑裡鎮": ["中山路"],
                "卓蘭鎮": ["中山路"],
                "造橋鄉": ["平仁路"],
                "西湖鄉": ["三湖村"],
                "頭屋鄉": ["象山路"],
                "公館鄉": ["近光路"],
                "銅鑼鄉": ["中正路"],
                "三義鄉": ["水美街"],
                "大湖鄉": ["中正路"],
                "獅潭鄉": ["新店村"],
                "三灣鄉": ["中正路"],
                "南庄鄉": ["文化路"],
                "泰安鄉": ["清安村"]
            },
            "臺中市": {
                "西屯區": ["臺灣大道三段", "文心路三段", "逢甲路", "西屯路二段", "黎明路三段"],
                "北屯區": ["崇德路", "北屯路", "太原路三段", "文心路四段"],
                "中區": ["臺灣大道一段", "中山路", "自由路二段", "民權路"],
                "東區": ["復興路四段", "建成路", "旱溪東路"],
                "南區": ["復興路二段", "五權南路", "忠明南路"],
                "西區": ["美村路一段", "公益路", "五權路", "英才路"],
                "北區": ["健行路", "進化北路", "三民路三段", "崇德路一段"],
                "南屯區": ["向上路二段", "黎明路二段", "文心路一段"],
                "太平區": ["中山路一段", "樹德路", "光興路"],
                "大里區": ["中興路二段", "國光路", "德芳南路"],
                "霧峰區": ["中正路", "四德路"],
                "烏日區": ["中山路一段", "光日路"],
                "豐原區": ["中正路", "圓環東路", "豐原大道"],
                "後里區": ["甲后路", "三豐路"],
                "石岡區": ["豐勢路"],
                "東勢區": ["豐勢路", "中正路"],
                "和平區": ["東關路"],
                "新社區": ["興安路"],
                "潭子區": ["中山路二段", "勝利路"],
                "大雅區": ["民生路", "中清路四段"],
                "神岡區": ["神岡路"],
                "大肚區": ["沙田路"],
                "沙鹿區": ["臺灣大道七段", "沙田路"],
                "龍井區": ["新興路", "沙田路六段"],
                "梧棲區": ["臺灣大道八段", "文化路"],
                "清水區": ["中山路", "鰲峰路"],
                "大甲區": ["順天路", "蔣公路"],
                "外埔區": ["甲后路三段"],
                "大安區": ["中山路"]
            },
            "彰化縣": {
                "彰化市": ["中山路二段", "中正路二段", "曉陽路", "金馬路"],
                "員林市": ["中山路二段", "員林大道", "大同路"],
                "鹿港鎮": ["中山路", "民權路", "復興路"],
                "和美鎮": ["彰美路", "道周路"],
                "北斗鎮": ["中華路", "斗苑路"],
                "溪湖鎮": ["彰水路", "員鹿路"],
                "田中鎮": ["中州路"],
                "二林鎮": ["斗苑路"],
                "線西鄉": ["沿海路"],
                "伸港鄉": ["建國路"],
                "福興鄉": ["彰鹿路"],
                "秀水鄉": ["彰水路"],
                "花壇鄉": ["中山路"],
                "芬園鄉": ["芬草路"],
                "大村鄉": ["中山路"],
                "埔鹽鄉": ["彰水路"],
                "埔心鄉": ["員鹿路"],
                "永靖鄉": ["中山路"],
                "社頭鄉": ["員集路"],
                "二水鄉": ["員集路"],
                "田尾鄉": ["中山路"],
                "埤頭鄉": ["彰水路"],
                "芳苑鄉": ["功湖路"],
                "大城鄉": ["中平路"],
                "竹塘鄉": ["竹五路"],
                "溪州鄉": ["中山路"]
            },
            "南投縣": {
                "南投市": ["中興路", "南陽路", "復興路", "彰南路"],
                "埔里鎮": ["中正路", "中山路", "信義路"],
                "草屯鎮": ["中正路", "太平路", "博愛路"],
                "竹山鎮": ["大明路", "集山路"],
                "集集鎮": ["民生路"],
                "名間鄉": ["彰南路"],
                "鹿谷鄉": ["中正路"],
                "中寮鄉": ["永平路"],
                "魚池鄉": ["中正路"],
                "國姓鄉": ["中興路"],
                "水里鄉": ["中山路"],
                "信義鄉": ["玉山路"],
                "仁愛鄉": ["仁和路"]
            },
            "雲林縣": {
                "斗六市": ["大學路", "民生路", "中山路", "鎮南路"],
                "斗南鎮": ["延平路", "中山路"],
                "虎尾鎮": ["林森路", "中山路", "文化路"],
                "西螺鎮": ["延平路", "中山路"],
                "土庫鎮": ["建國路"],
                "北港鎮": ["中山路", "文化路", "民主路"],
                "古坑鄉": ["中山路"],
                "大埤鄉": ["民生路"],
                "莿桐鄉": ["中正路"],
                "林內鄉": ["中正路"],
                "二崙鄉": ["中山路"],
                "崙背鄉": ["中山路"],
                "麥寮鄉": ["中山路"],
                "東勢鄉": ["中正路"],
                "褒忠鄉": ["中正路"],
                "台西鄉": ["中山路"],
                "元長鄉": ["中山路"],
                "四湖鄉": ["中山路"],
                "口湖鄉": ["中正路"],
                "水林鄉": ["水林路"]
            },
            "嘉義市": {
                "東區": ["中山路", "林森東路", "大雅路一段", "大雅路二段", "新生路", "啟明路", "彌陀路", "吳鳳北路", "忠孝路", "芳安路", "宣信街", "體育路", "興業東路", "公明路", "和平路"],
                "西區": ["中山路", "垂楊路", "新民路", "友愛路", "自由路", "博愛路一段", "博愛路二段", "興業西路", "吳鳳南路", "世賢路一段", "世賢路二段", "世賢路三段", "文化路", "金山路", "玉山路", "南京路", "友忠路"]
            },
            "嘉義縣": {
                "太保市": ["中山路一段", "太保一路", "祥和三路", "太子大道"],
                "朴子市": ["山通路", "海通路", "文化路", "大同路"],
                "民雄鄉": ["建國路", "民權路", "中樂路", "文化路", "保生街"],
                "水上鄉": ["中山路二段", "中興路", "正義路"],
                "中埔鄉": ["中山路五段", "阿里山公路"],
                "竹崎鄉": ["中山路", "坑仔內"],
                "梅山鄉": ["中山路", "華山路"],
                "番路鄉": ["阿里山公路"],
                "大林鎮": ["中興路", "中山路"],
                "溪口鄉": ["民生路"],
                "新港鄉": ["中山路", "新民路"],
                "六腳鄉": ["蒜頭村"],
                "東石鄉": ["東石村"],
                "義竹鄉": ["義竹村"],
                "鹿草鄉": ["鹿草村"],
                "布袋鎮": ["中山路"],
                "阿里山鄉": ["中正村"],
                "大埔鄉": ["大埔村"]
            },
            "臺南市": {
                "中西區": ["民權路二段", "府前路一段", "西門路二段", "中正路", "海安路二段", "成功路"],
                "東區": ["東寧路", "長榮路二段", "中華東路", "小東路", "大學路"],
                "南區": ["金華路一段", "健康路二段", "新興路"],
                "北區": ["成功路", "公園路", "海安路三段", "和緯路"],
                "安平區": ["安平路", "慶平路", "永華路二段"],
                "安南區": ["安和路", "海佃路", "安中路"],
                "永康區": ["中正路", "中華路", "中山南路", "大灣路"],
                "歸仁區": ["中山路"],
                "關廟區": ["中山路"],
                "新營區": ["民治路", "中正路", "三民路"],
                "白河區": ["中山路"],
                "麻豆區": ["中山路"],
                "佳里區": ["中山路"],
                "善化區": ["中山路"],
                "鹽水區": ["三福路"],
                "後壁區": ["後壁村"],
                "東山區": ["東山村"],
                "六甲區": ["中正路"],
                "下營區": ["中山路"],
                "柳營區": ["柳營路"],
                "官田區": ["中山路"],
                "西港區": ["中山路"],
                "七股區": ["七股村"],
                "將軍區": ["將軍村"],
                "學甲區": ["中正路"],
                "北門區": ["北門村"],
                "新化區": ["中山路"],
                "左鎮區": ["左鎮村"],
                "玉井區": ["中正路"],
                "楠西區": ["中正路"],
                "南化區": ["南化村"],
                "龍崎區": ["龍崎村"],
                "大內區": ["大內村"],
                "山上區": ["山上村"],
                "仁德區": ["中山路"],
                "新市區": ["中正路"],
                "安定區": ["安定村"]
            },
            "高雄市": {
                "新興區": ["中山一路", "五福二路", "民生一路", "七賢一路", "八德一路"],
                "前金區": ["成功一路", "中正四路", "民生二路"],
                "苓雅區": ["三多三路", "四維三路", "建國一路", "青年一路"],
                "鹽埕區": ["五福四路", "大勇路", "七賢三路"],
                "鼓山區": ["博愛一路", "明誠三路", "臨海一路", "美術東二路"],
                "旗津區": ["旗津一路", "廟前路"],
                "前鎮區": ["中山二路", "一心二路", "瑞隆路", "光華二路"],
                "三民區": ["九如一路", "建國二路", "民族一路", "鼎中路", "聯興路"],
                "楠梓區": ["建楠路", "軍校路", "高楠公路"],
                "小港區": ["沿海一路", "漢民路"],
                "左營區": ["博愛二路", "崇德路", "華夏路", "新庄仔路"],
                "鳳山區": ["中山路", "光遠路", "建國路三段", "青年路二段"],
                "大寮區": ["鳳林三路", "力行路"],
                "林園區": ["林園北路"],
                "鳥松區": ["神農路", "本館路"],
                "大樹區": ["中正路"],
                "旗山區": ["中山路"],
                "美濃區": ["中正路"],
                "岡山區": ["岡山路"],
                "路竹區": ["中山路"],
                "阿蓮區": ["中山路"],
                "田寮區": ["崗安路"],
                "燕巢區": ["中民路"],
                "橋頭區": ["橋頭路"],
                "梓官區": ["梓官路"],
                "彌陀區": ["中正路"],
                "永安區": ["永安路"],
                "湖內區": ["保生路"],
                "六龜區": ["光復路"],
                "內門區": ["內門路"],
                "杉林區": ["山仙路"],
                "甲仙區": ["文化路"],
                "桃源區": ["南橫公路"],
                "那瑪夏區": ["大光巷"],
                "茂林區": ["茂林巷"],
                "茄萣區": ["仁愛路"],
                "仁武區": ["仁雄路"],
                "大社區": ["中山路"]
            },
            "屏東縣": {
                "屏東市": ["民生路", "中正路", "自由路", "廣東路"],
                "潮州鎮": ["延平路", "中山路"],
                "東港鎮": ["中山路", "沿海路"],
                "恆春鎮": ["中山路", "墾丁路"],
                "萬丹鄉": ["萬丹路"],
                "長治鄉": ["中興路"],
                "麟洛鄉": ["中山路"],
                "九如鄉": ["九如路"],
                "里港鄉": ["中山路"],
                "高樹鄉": ["中山路"],
                "鹽埔鄉": ["中正路"],
                "內埔鄉": ["廣濟路"],
                "萬巒鄉": ["民和路"],
                "竹田鄉": ["中正路"],
                "新埤鄉": ["新埤路"],
                "枋寮鄉": ["德興路"],
                "新園鄉": ["仙吉路"],
                "崁頂鄉": ["中正路"],
                "林邊鄉": ["中山路"],
                "南州鄉": ["三民路"],
                "佳冬鄉": ["佳和路"],
                "琉球鄉": ["觀光港路"],
                "車城鄉": ["中山路"],
                "滿州鄉": ["中山路"],
                "枋山鄉": ["中山路"]
            },
            "宜蘭縣": {
                "宜蘭市": ["中山路三段", "神農路一段", "校舍路", "女中路"],
                "羅東鎮": ["公正路", "中正路", "純精路"],
                "蘇澳鎮": ["中山路", "蘇東中路"],
                "頭城鎮": ["開蘭路"],
                "礁溪鄉": ["礁溪路"],
                "冬山鄉": ["冬山路"],
                "五結鄉": ["五結路"],
                "三星鄉": ["三星路"],
                "壯圍鄉": ["壯五路"],
                "員山鄉": ["員山路"],
                "大同鄉": ["神代巷"],
                "南澳鄉": ["蘇花路"]
            },
            "花蓮縣": {
                "花蓮市": ["中山路", "中正路", "中華路", "府前路"],
                "吉安鄉": ["中央路三段", "吉安路"],
                "新城鄉": ["光復路"],
                "壽豐鄉": ["中山路"],
                "鳳林鎮": ["中正路"],
                "光復鄉": ["中山路"],
                "豐濱鄉": ["海岸公路"],
                "瑞穗鄉": ["中山路"],
                "富里鄉": ["中山路"],
                "玉里鎮": ["中山路"],
                "秀林鄉": ["富世村"],
                "萬榮鄉": ["萬榮村"],
                "卓溪鄉": ["卓溪村"]
            },
            "臺東縣": {
                "臺東市": ["更生路", "中華路一段", "新生路", "正氣路"],
                "成功鎮": ["中華路"],
                "關山鎮": ["中山路"],
                "卑南鄉": ["太平路"],
                "大武鄉": ["大武街"],
                "太麻里鄉": ["太麻里街"],
                "東河鄉": ["東河村"],
                "長濱鄉": ["長濱村"],
                "鹿野鄉": ["中華路"],
                "池上鄉": ["中山路"],
                "綠島鄉": ["南寮村"],
                "延平鄉": ["延平村"],
                "海端鄉": ["海端村"],
                "達仁鄉": ["復興路"],
                "金峰鄉": ["排灣村"],
                "蘭嶼鄉": ["紅頭村"]
            },
            "澎湖縣": {
                "馬公市": ["中正路", "民生路", "中華路"],
                "湖西鄉": ["湖西村"],
                "白沙鄉": ["赤崁村"],
                "西嶼鄉": ["池東村"],
                "望安鄉": ["東安村"],
                "七美鄉": ["南港村"]
            },
            "金門縣": {
                "金城鎮": ["民生路", "民權路"],
                "金湖鎮": ["復興路"],
                "金沙鎮": ["沙美街"],
                "金寧鄉": ["頂林路"],
                "烈嶼鄉": ["九井路"],
                "烏坵鄉": ["大坵村"]
            },
            "連江縣": {
                "南竿鄉": ["介壽村", "馬祖村"],
                "北竿鄉": ["塘岐村"],
                "莒光鄉": ["大坪村"],
                "東引鄉": ["樂華村"]
            }
        };

        // 資料庫核心結構
        let db = JSON.parse(localStorage.getItem('templeDataStore')) || { 
            believers: [], 
            lighting: [], 
            finance: [],
            users: [{ username: "admin", password: "123456", role: "admin" }]
        };

        if (!db.users || db.users.length === 0) {
            db.users = [{ username: "admin", password: "123456", role: "admin" }];
        }

        const roleNames = {
            "admin": "超級管理員",
            "staff": "一般廟務人員",
            "finance": "財務會計人員"
        };

        window.onload = function() {
            setInterval(updateClock, 1000);
            updateClock();
            initCityDatalist(); // 初始化縣市下拉清單
            setDefaultDates();
            addLog("智慧地圖整合管理模組已載入，全台地址智慧連動就緒。");
            
            if (sessionStorage.getItem('isLoggedIn') === 'true') {
                showSystem();
            }
        };

        // ================= 登入驗證模組 =================
        function handleLogin(e) {
            e.preventDefault();
            const userVal = document.getElementById('loginUsername').value.trim();
            const passVal = document.getElementById('loginPassword').value;
            const errorBox = document.getElementById('loginError');

            const matchedUser = db.users.find(u => u.username === userVal && u.password === passVal);

            if (matchedUser) {
                errorBox.style.display = 'none';
                sessionStorage.setItem('isLoggedIn', 'true');
                sessionStorage.setItem('currentUser', JSON.stringify(matchedUser));
                showSystem();
                addLog(`使用者 [${matchedUser.username}] (${roleNames[matchedUser.role]}) 登入成功。`);
            } else {
                errorBox.style.display = 'flex';
                document.getElementById('loginPassword').value = '';
            }
        }

        function applyPermissionRules(role) {
            document.querySelectorAll('.sidebar-menu li').forEach(li => li.classList.remove('permission-hidden'));
            if (role === 'staff') {
                document.getElementById('menu-finance').classList.add('permission-hidden');
                document.getElementById('menu-system').classList.add('permission-hidden');
            } else if (role === 'finance') {
                document.getElementById('menu-believers').classList.add('permission-hidden');
                document.getElementById('menu-lighting').classList.add('permission-hidden');
                document.getElementById('menu-system').classList.add('permission-hidden');
            }
        }

        function showSystem() {
            const currentUser = JSON.parse(sessionStorage.getItem('currentUser'));
            if (!currentUser) return;

            document.getElementById('loginPanel').style.display = 'none';
            document.getElementById('mainSystemWrapper').style.display = 'flex';
            
            document.getElementById('sidebar-admin-name').innerText = currentUser.username;
            document.getElementById('sidebar-user-role').innerText = roleNames[currentUser.role];

            applyPermissionRules(currentUser.role);
            if(currentUser.role === 'finance') switchTab('dashboard');

            refreshUI();
        }

        // ================= 個人密碼修改功能 =================
        function openPwdModal() {
            document.getElementById('changePwdForm').reset();
            document.getElementById('pwdModal').style.display = 'flex';
        }

        function closePwdModal() {
            document.getElementById('pwdModal').style.display = 'none';
        }

        function handleChangePassword(e) {
            e.preventDefault();
            const curUser = JSON.parse(sessionStorage.getItem('currentUser'));
            if (!curUser) return;

            const oldPwd = document.getElementById('oldPassword').value;
            const newPwd = document.getElementById('newPassword').value;
            const confPwd = document.getElementById('confirmPassword').value;

            const uIndex = db.users.findIndex(u => u.username === curUser.username);
            if (uIndex === -1) return;

            if (db.users[uIndex].password !== oldPwd) {
                alert('舊密碼輸入錯誤！');
                return;
            }
            if (newPwd !== confPwd) {
                alert('兩次輸入的新密碼不符！');
                return;
            }

            db.users[uIndex].password = newPwd;
            localStorage.setItem('templeDataStore', JSON.stringify(db));
            
            curUser.password = newPwd;
            sessionStorage.setItem('currentUser', JSON.stringify(curUser));

            addLog(`使用者 [${curUser.username}] 密碼修改成功。`);
            closePwdModal();
            alert('密碼修改成功！');
        }

        // ================= 帳號配給管理 =================
        function handleCreateUser(e) {
            e.preventDefault();
            const username = document.getElementById('newUsername').value.trim();
            const password = document.getElementById('newUserPassword').value;
            const role = document.getElementById('newUserRole').value;

            if (db.users.some(u => u.username === username)) {
                alert('該帳號已存在！');
                return;
            }

            db.users.push({ username, password, role });
            localStorage.setItem('templeDataStore', JSON.stringify(db));
            document.getElementById('userManageForm').reset();
            addLog(`[授權] 建立新帳號：${username} (${roleNames[role]})`);
            refreshUI();
            alert('帳號開設成功！');
        }

        function deleteUser(username) {
            if (username === 'admin') { alert('系統預設 admin 帳號不可刪除！'); return; }
            const currentUser = JSON.parse(sessionStorage.getItem('currentUser'));
            if (currentUser && currentUser.username === username) { alert('您無法刪除目前登入中的帳號！'); return; }

            if (confirm(`確定要廢除 [${username}] 帳號嗎？`)) {
                db.users = db.users.filter(u => u.username !== username);
                localStorage.setItem('templeDataStore', JSON.stringify(db));
                addLog(`[授權] 廢除帳號：${username}`);
                refreshUI();
            }
        }

        // ================= 常規系統時鐘與日誌功能 =================
        function updateClock() {
            const now = new Date();
            document.getElementById('liveClock').innerText = `${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}:${String(now.getSeconds()).padStart(2, '0')}`;
        }

        function setDefaultDates() {
            const todayStr = new Date().toISOString().split('T')[0];
            document.getElementById('io-date').value = todayStr;
            document.getElementById('l-date').value = todayStr;
        }

        function addLog(msg) {
            const t = document.getElementById('logTerminal');
            if(!t) return;
            const now = new Date();
            const timeStr = `${String(now.getHours()).padStart(2, '0')}:${String(now.getMinutes()).padStart(2, '0')}:${String(now.getSeconds()).padStart(2, '0')}`;
            t.innerHTML += `<div><span class="log-time">[${timeStr}]</span><span class="log-msg">${msg}</span></div>`;
            t.scrollTop = t.scrollHeight;
        }

        function switchTab(tabId) {
            document.querySelectorAll('.system-panel').forEach(p => p.classList.remove('active'));
            document.querySelectorAll('.sidebar-menu li').forEach(li => li.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
            const targetMenu = document.getElementById(`menu-${tabId}`);
            if (targetMenu) targetMenu.classList.add('active');
            addLog(`切換至功能模組: [${tabId}]`);
        }

        function handleBirthChange() {
            const birthInput = document.getElementById('b-birth').value; 
            if (!birthInput) { document.getElementById('b-zodiac').value = ''; return; }
            const parts = birthInput.split('-');
            const year = parseInt(parts[0]);
            document.getElementById('b-zodiac').value = calculateZodiac(year, parseInt(parts[1]), parseInt(parts[2]));
        }

        function calculateZodiac(year, month, day) {
            if (year < 200) { year = year + 1911; }
            const zodiacs = ["鼠", "牛", "虎", "兔", "龍", "蛇", "馬", "羊", "猴", "雞", "狗", "豬"];
            let zodiacIdx = (year - 4) % 12;
            if (zodiacIdx < 0) zodiacIdx += 12;
            if (month === 1 || (month === 2 && day < 4)) zodiacIdx = (zodiacIdx - 1 + 12) % 12;
            return zodiacs[zodiacIdx];
        }

        function convertToInputDateFormat(dateStr) {
            if (!dateStr) return '';
            if (dateStr.includes('-') && dateStr.split('-')[0].length === 4) return dateStr;
            if (dateStr.includes('/')) {
                const parts = dateStr.split('/');
                let rYear = parseInt(parts[0]);
                if (rYear > 1900) return `${rYear}-${parts[1].padStart(2,'0')}-${parts[2].padStart(2,'0')}`;
                return `${rYear + 1911}-${parts[1].padStart(2,'0')}-${parts[2].padStart(2,'0')}`;
            }
            return '';
        }

        // ================= 核心：全台地址三級深度智慧連動控制模組 =================
        function initCityDatalist() {
            const cityList = document.getElementById('city-list');
            if(!cityList) return;
            cityList.innerHTML = '';
            for (let city in taiwanDistrictsAndRoads) {
                cityList.innerHTML += `<option value="${city}">`;
            }
        }

        // 當輸入或點選「縣市」：自動更新對應的「區域」
        function handleCityInput() {
            const cityVal = document.getElementById('b-city').value.trim();
            const distList = document.getElementById('district-list');
            const roadList = document.getElementById('road-list');
            
            if(!distList) return;
            distList.innerHTML = '';
            if(roadList) roadList.innerHTML = ''; // 清空路名快取
            
            // 如果輸入的縣市存在，拉出所有對應區域
            if (taiwanDistrictsAndRoads[cityVal]) {
                for (let dist in taiwanDistrictsAndRoads[cityVal]) {
                    distList.innerHTML += `<option value="${dist}">`;
                }
            }
        }

        // 當輸入或點選「區/鄉/鎮」：自動連動智慧帶出該區「專屬道路名」
        function handleDistrictInput() {
            const cityVal = document.getElementById('b-city').value.trim();
            const distVal = document.getElementById('b-district').value.trim();
            const roadList = document.getElementById('road-list');
            
            if(!roadList) return;
            roadList.innerHTML = '';
            
            // 雙層結構安全驗證，精準取出路名陣列
            if (taiwanDistrictsAndRoads[cityVal] && taiwanDistrictsAndRoads[cityVal][distVal]) {
                taiwanDistrictsAndRoads[cityVal][distVal].forEach(road => {
                    roadList.innerHTML += `<option value="${road}">`;
                });
            }
        }

        // ================= 信眾、點燈、財務核心資料存取處理 =================
        document.getElementById('believerForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const id = document.getElementById('b-id').value;
            const name = document.getElementById('b-name').value.trim();
            const phone = document.getElementById('b-phone').value.trim();
            const birth = document.getElementById('b-birth').value; 
            
            // 讀取十一宮格分離式欄位資料
            const city = document.getElementById('b-city').value.trim();
            const dist = document.getElementById('b-district').value.trim();
            const village = document.getElementById('b-village').value.trim();
            const neighbor = document.getElementById('b-neighbor').value.trim();
            const road = document.getElementById('b-road').value.trim();
            const section = document.getElementById('b-section').value.trim();
            const lane = document.getElementById('b-lane').value.trim();
            const alley = document.getElementById('b-alley').value.trim();
            const number = document.getElementById('b-number').value.trim();
            const floor = document.getElementById('b-floor').value.trim();
            const room = document.getElementById('b-room').value.trim();

            // 智能地址字尾格式化拼接
            let fullAddress = city + dist;
            if (village) fullAddress += village;
            if (neighbor) fullAddress += neighbor.includes('鄰') ? neighbor : neighbor + '鄰';
            if (road) fullAddress += road;
            if (section) fullAddress += section.includes('段') ? section : section + '段';
            if (lane) fullAddress += lane.includes('巷') ? lane : lane + '巷';
            if (alley) fullAddress += alley.includes('弄') ? alley : alley + '弄';
            if (number) fullAddress += number.includes('號') ? number : number + '號';
            if (floor) fullAddress += floor.includes('樓') ? floor : floor + '樓';
            if (room) fullAddress += room.includes('室') || room.includes('之') ? room : room + '室';

            const payload = { 
                id: id ? Number(id) : (db.believers.length > 0 ? Math.max(...db.believers.map(b => b.id)) + 1 : 1001), 
                name, phone, birth, address: fullAddress, 
                city, dist, village, neighbor, road, section, lane, alley, number, floor, room 
            };

            if (id) {
                const idx = db.believers.findIndex(b => b.id == id);
                if (idx !== -1) db.believers[idx] = payload;
            } else {
                db.believers.push(payload);
            }
            saveAndRefresh(); resetBelieverForm();
        });

        function editBeliever(id) {
            const b = db.believers.find(item => item.id === id); if (!b) return;
            document.getElementById('b-id').value = b.id;
            document.getElementById('b-name').value = b.name;
            document.getElementById('b-phone').value = b.phone;
            document.getElementById('b-birth').value = convertToInputDateFormat(b.birth);
            handleBirthChange();
            
            // 十一宮格完美反填與選單解鎖刷新
            document.getElementById('b-city').value = b.city || '';
            handleCityInput(); // 觸發解鎖區域選單
            document.getElementById('b-district').value = b.dist || '';
            handleDistrictInput(); // 觸發解鎖路名選單
            
            document.getElementById('b-village').value = b.village || '';
            document.getElementById('b-neighbor').value = b.neighbor || '';
            document.getElementById('b-road').value = b.road || '';
            document.getElementById('b-section').value = b.section || '';
            document.getElementById('b-lane').value = b.lane || '';
            document.getElementById('b-alley').value = b.alley || '';
            document.getElementById('b-number').value = b.number || '';
            document.getElementById('b-floor').value = b.floor || '';
            document.getElementById('b-room').value = b.room || '';

            document.getElementById('believer-form-title').innerText = `正在修改 [編號 ${b.id}] 的信眾檔案`;
            document.querySelector('.content-body').scrollTop = 0;
        }

        function resetBelieverForm() {
            document.getElementById('believerForm').reset();
            document.getElementById('b-id').value = '';
            document.getElementById('b-zodiac').value = '';
            if(document.getElementById('district-list')) document.getElementById('district-list').innerHTML = '';
            if(document.getElementById('road-list')) document.getElementById('road-list').innerHTML = '';
            document.getElementById('believer-form-title').innerText = `新增信眾檔案`;
        }

        // ================= 點燈與常規會計收支互動邏輯 =================
        document.getElementById('lightForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const id = document.getElementById('l-id').value;
            const date = document.getElementById('l-date').value;
            const name = document.getElementById('l-name-select').value;
            const type = document.getElementById('l-type').value;
            const amount = parseInt(document.getElementById('l-amount').value);
            if (id) {
                const index = db.lighting.findIndex(l => l.id == id);
                if (index !== -1) db.lighting[index] = { id: Number(id), date, name, type, amount };
            } else {
                db.lighting.push({ id: Date.now(), date, name, type, amount });
                db.finance.push({ id: Date.now()+1, date, note: `信眾 ${name} 登記 [${type}]`, amount, isExpense: false });
            }
            saveAndRefresh(); resetLightForm();
        });

        function editLighting(id) {
            const l = db.lighting.find(item => item.id === id); if (!l) return;
            document.getElementById('l-id').value = l.id;
            document.getElementById('l-date').value = l.date;
            document.getElementById('l-name-select').value = l.name;
            document.getElementById('l-type').value = l.type;
            document.getElementById('l-amount').value = l.amount;
            document.querySelector('.content-body').scrollTop = 0;
        }

        function resetLightForm() { document.getElementById('lightForm').reset(); document.getElementById('l-id').value = ''; setDefaultDates(); }

        document.getElementById('ioForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const id = document.getElementById('io-id').value;
            const date = document.getElementById('io-date').value;
            const note = document.getElementById('io-note').value.trim();
            const type = document.getElementById('io-type').value;
            const amount = parseInt(document.getElementById('io-amount').value);
            if (id) {
                const index = db.finance.findIndex(f => f.id == id);
                if (index !== -1) db.finance[index] = { id: Number(id), date, note, amount, isExpense: (type === "支出") };
            } else {
                db.finance.push({ id: Date.now(), date, note, amount, isExpense: (type === "支出") });
            }
            saveAndRefresh(); resetIOForm();
        });

        function editFinance(id) {
            const f = db.finance.find(item => item.id === id); if (!f) return;
            document.getElementById('io-id').value = f.id;
            document.getElementById('io-date').value = f.date;
            document.getElementById('io-note').value = f.note;
            document.getElementById('io-type').value = f.isExpense ? "支出" : "收入";
            document.getElementById('io-amount').value = f.amount;
            document.querySelector('.content-body').scrollTop = 0;
        }

        function resetIOForm() { document.getElementById('ioForm').reset(); document.getElementById('io-id').value = ''; setDefaultDates(); }

        function deleteItem(type, id) {
            if(confirm("確定要刪除此筆紀錄嗎？")) {
                db[type] = db[type].filter(item => item.id !== id);
                saveAndRefresh();
            }
        }

        function saveAndRefresh() {
            localStorage.setItem('templeDataStore', JSON.stringify(db));
            refreshUI();
        }

        function refreshUI() {
            if (document.getElementById('mainSystemWrapper').style.display === 'none') return;

            document.getElementById('card-b-count').innerText = db.believers.length;
            document.getElementById('card-l-count').innerText = db.lighting.length;
            let inc = db.finance.reduce((s,f)=>s+(f.isExpense?0:f.amount),0);
            let exp = db.finance.reduce((s,f)=>s+(f.isExpense?f.amount:0),0);
            document.getElementById('card-revenue').innerText = inc.toLocaleString();
            document.getElementById('card-balance').innerText = (inc - exp).toLocaleString();

            db.believers.sort((a, b) => a.id - b.id);
            renderBelieversTable(db.believers);
            
            const lSel = document.getElementById('l-name-select');
            if (lSel) {
                const cur = lSel.value;
                lSel.innerHTML = '<option value="">-- 請選擇信眾 --</option>';
                db.believers.forEach(b => lSel.innerHTML += `<option value="${b.name}">${b.name} (編號:${b.id})</option>`);
                lSel.value = cur;
            }

            const tbodyL = document.getElementById('tbody-lighting'); 
            if (tbodyL) {
                tbodyL.innerHTML = '';
                db.lighting.forEach(l => {
                    tbodyL.innerHTML += `<tr><td>${l.date}</td><td>${l.name}</td><td><span>${l.type}</span></td><td>$${l.amount.toLocaleString()}</td><td style="text-align:center;"><button class="btn-edit" onclick="editLighting(${l.id})">修改</button><button class="btn-delete" onclick="deleteItem('lighting', ${l.id})">刪除</button></td></tr>`;
                });
            }

            const tbodyF = document.getElementById('tbody-finance');
            if (tbodyF) {
                tbodyF.innerHTML = '';
                db.finance.forEach(f => {
                    const prefix = f.isExpense ? '-' : '+';
                    tbodyF.innerHTML += `<tr><td>${f.date}</td><td>${f.note}</td><td>${prefix} $${f.amount.toLocaleString()}</td><td style="text-align:center;"><button class="btn-edit" onclick="editFinance(${f.id})">修改</button><button class="btn-delete" onclick="deleteItem('finance', ${f.id})">刪除</button></td></tr>`;
                });
            }

            const tbodyU = document.getElementById('tbody-users');
            if (tbodyU) {
                tbodyU.innerHTML = '';
                db.users.forEach(u => {
                    tbodyU.innerHTML += `<tr><td><strong>${u.username}</strong></td><td>${roleNames[u.role]}</td><td style="text-align:center;"><button class="btn-delete" onclick="deleteUser('${u.username}')">註銷帳號</button></td></tr>`;
                });
            }
        }

        function renderBelieversTable(arr) {
            const tbody = document.getElementById('tbody-believers'); if(!tbody) return;
            tbody.innerHTML = '';
            arr.forEach(b => {
                tbody.innerHTML += `<tr><td><span>${b.id}</span></td><td><strong>${b.name}</strong></td><td>${b.phone}</td><td>${b.address}</td><td style="text-align:center;"><button class="btn-edit" onclick="editBeliever(${b.id})">修改</button><button class="btn-delete" onclick="deleteItem('believers', ${b.id})">移除</button></td></tr>`;
            });
        }

        function searchBelievers() {
            const txt = document.getElementById('search-b').value.toLowerCase();
            renderBelieversTable(db.believers.filter(b => b.name.includes(txt) || b.phone.includes(txt) || b.address.includes(txt)));
        }

        function exportBackup() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(db, null, 4));
            const dl = document.createElement('a'); dl.setAttribute("href", dataStr); dl.setAttribute("download", "temple_backup.json"); dl.click();
        }

        function importBackup(input) {
            const file = input.files[0]; if (!file) return;
            const r = new FileReader();
            r.onload = function(e) {
                try {
                    const parsed = JSON.parse(e.target.result);
                    if (parsed.believers && parsed.lighting && parsed.finance) { 
                        db = parsed; 
                        if(!db.users) db.users = [{ username: "admin", password: "123456", role: "admin" }];
                        saveAndRefresh(); 
                    }
                } catch(err) { alert('備份檔案毀損！'); }
            }; r.readAsText(file); input.value = '';
        }

        function resetSystem() {
            if(confirm('警告：這會清空本機的所有資料與開設帳號！')) { 
                localStorage.removeItem('templeDataStore'); 
                db={ believers:[], lighting:[], finance:[], users:[{ username:"admin", password:"123456", role:"admin" }] }; 
                saveAndRefresh(); 
                triggerLogout();
            }
        }

        function triggerLogout() { 
            sessionStorage.clear();
            document.getElementById('loginUsername').value = '';
            document.getElementById('loginPassword').value = '';
            document.getElementById('mainSystemWrapper').style.display = 'none';
            document.getElementById('loginPanel').style.display = 'flex';
        }
    </script>
</body>
</html>
