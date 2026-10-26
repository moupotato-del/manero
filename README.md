<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>モビロー販売管理システム - 株式会社ベルアビエ</title>
    <style>
        :root {
            --bg-color: #f1f5f9;
            --card-bg: #ffffff;
            --primary-color: #1e293b;
            --border-color: #cbd5e1;
        }
        body {
            font-family: 'Helvetica Neue', Arial, 'MS Gothic', monospace;
            background: var(--bg-color);
            color: #0f172a;
            margin: 0;
            padding: 10px;
        }

        /* 起動時スプラッシュ画面 (1.5秒・白黒無骨ゴシック・スワイプ隠しゲーム対応) */
        #splash-screen {
            position: fixed;
            top: 0; left: 0; width: 100vw; height: 100vh;
            background: #ffffff;
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            z-index: 9999; color: #000000;
            animation: fadeOut 1.5s ease forwards;
            animation-delay: 1.5s;
        }
        .splash-logo { 
            font-family: 'Arial Black', Impact, sans-serif;
            font-size: 3rem; font-weight: 900; letter-spacing: 2px; 
            text-transform: uppercase; border-bottom: 4px solid #000; padding-bottom: 5px;
        }
        .splash-sub { 
            font-size: 1rem; margin-top: 15px; font-weight: bold; letter-spacing: 1px;
        }
        .splash-hint {
            font-size: 0.75rem; margin-top: 30px; color: #64748b;
        }
        @keyframes fadeOut { 
            0% { opacity: 1; visibility: visible; } 
            100% { opacity: 0; visibility: hidden; } 
        }

        /* 隠し暇つぶしゲーム（ブロック崩し）モーダル */
        #hiddenGameModal {
            display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
            background: rgba(0,0,0,0.85); z-index: 20000; justify-content: center; align-items: center; flex-direction: column; color: white;
        }
        #gameCanvas { background: #000; border: 2px solid #fff; box-shadow: 0 0 20px rgba(255,255,255,0.2); }

        .container { max-width: 1200px; margin: 0 auto; }
        
        /* ハンディターミナル風専用筐体ボックス */
        .handy-terminal {
            max-width: 420px;
            margin: 20px auto;
            background: #ffffff;
            border: 5px solid #2b3440;
            border-radius: 12px;
            box-shadow: 0 8px 25px rgba(0,0,0,0.25);
            overflow: hidden;
        }

        /* 上部タブメニュー (実機そっくりな赤茶系) */
        .terminal-top-tabs {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            background: #a83232;
            border-bottom: 3px solid #7c2424;
        }
        .top-tab-btn {
            background: #a83232;
            color: #ffffff;
            border: none;
            border-right: 2px solid #8d2a2a;
            padding: 12px 0;
            font-size: 0.95rem;
            font-weight: bold;
            cursor: pointer;
            text-align: center;
        }
        .top-tab-btn:hover { background: #942b2b; }
        .top-tab-btn.active-tab { background: #7c2424; border-bottom: 3px solid #fff; }

        /* 中央メイン画面エリア */
        .terminal-screen-body {
            padding: 20px;
            background: #ffffff;
        }
        .terminal-banner {
            display: flex;
            align-items: center;
            justify-content: space-between;
            border-bottom: 3px solid #1e293b;
            padding-bottom: 12px;
            margin-bottom: 15px;
        }
        .banner-face-box {
            width: 90px; height: 50px;
            border: 2px solid #cbd5e1;
            display: flex; align-items: center; justify-content: center;
            font-size: 0.85rem; font-weight: bold; color: #475569; background: #f8fafc; border-radius: 6px;
        }
        .banner-title {
            font-size: 2.8rem;
            font-weight: 900;
            color: #1e293b;
            letter-spacing: 6px;
            line-height: 1;
        }

        .terminal-sys-name {
            font-size: 0.95rem;
            font-weight: bold;
            color: #334155;
            margin-bottom: 12px;
            border-left: 4px solid #a83232;
            padding-left: 8px;
        }

        .terminal-info-table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 15px;
            font-size: 0.95rem;
        }
        .terminal-info-table td {
            border: 1px solid #94a3b8;
            padding: 10px 12px;
        }
        .terminal-info-table td.label-col {
            background: #f1f5f9;
            font-weight: bold;
            width: 35%;
            color: #334155;
        }
        .terminal-info-table select {
            width: 100%;
            border: none;
            background: transparent;
            font-family: inherit;
            font-size: 1rem;
            font-weight: bold;
            outline: none;
        }

        /* 下部ボタン群 (実機そっくりな赤茶系) */
        .terminal-bottom-buttons {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            background: #a83232;
            border-top: 3px solid #7c2424;
        }
        .bottom-action-btn {
            background: #a83232;
            color: #ffffff;
            border: none;
            border-right: 2px solid #8d2a2a;
            padding: 14px 0;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            text-align: center;
        }
        .bottom-action-btn:hover { background: #942b2b; }

        /* 各種ツール・サブ画面用 */
        .tool-view { display: none; }
        .tool-view.active { display: block; }
        
        .sub-panel {
            background: #ffffff;
            padding: 15px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            margin-bottom: 15px;
        }
        .back-btn {
            background: #475569;
            color: white;
            border: none;
            padding: 6px 14px;
            border-radius: 4px;
            font-size: 0.85rem;
            cursor: pointer;
            margin-bottom: 10px;
        }
        .back-btn:hover { background: #334155; }

        /* 伝票発行・店舗一覧用グリッド */
        .main-grid { display: grid; grid-template-columns: 260px 1fr; gap: 15px; }
        @media(max-width: 768px) { .main-grid { grid-template-columns: 1fr; } }
        
        .store-item {
            padding: 6px 10px; margin-bottom: 4px; border-radius: 4px; cursor: pointer;
            display: flex; justify-content: space-between; align-items: center; font-size: 0.85rem; background: #f8fafc; border: 1px solid var(--border-color);
        }
        .store-item:hover { background: #f1f5f9; }
        .store-item.active { background: #0f172a; color: white; font-weight: bold; border-color: #0f172a; }
        .store-actions { display: flex; gap: 4px; }
        .store-actions button { padding: 2px 6px; font-size: 0.7rem; }

        .input-control-box { background: #f8fafc; padding: 10px; border-radius: 6px; border: 1px solid var(--border-color); margin-bottom: 10px; font-size: 0.85rem; }
        .item-table { width: 100%; border-collapse: collapse; margin-bottom: 8px; font-size: 0.85rem; }
        .item-table th, .item-table td { border: 1px solid var(--border-color); padding: 4px; text-align: center; }
        .item-table th { background: #f1f5f9; }
        .item-table input { width: 90%; text-align: center; border: none; background: transparent; font-family: inherit; }

        .preview-container { background: white; padding: 20px; border-radius: 6px; border: 1px solid var(--border-color); }
        .mode-a4 { width: 100%; max-width: 650px; margin: 0 auto; font-family: monospace; font-size: 0.85rem; }
        .mode-mpb20 { width: 360px; margin: 0 auto; font-family: monospace; font-size: 0.75rem; }
        .denpyo-header { border-bottom: 2px solid #000; padding-bottom: 6px; margin-bottom: 10px; }
        .denpyo-title { font-size: 1.2rem; font-weight: bold; text-align: center; letter-spacing: 3px; margin-bottom: 6px; }
        table.denpyo-table { width: 100%; border-collapse: collapse; margin-bottom: 8px; }
        table.denpyo-table th, table.denpyo-table td { border: 1px solid #000; padding: 3px; text-align: center; font-size: 0.75rem; }
        table.denpyo-table th { background: #eee; }

        /* 商品選択・追加モーダル */
        #itemSelectModal { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(0,0,0,0.5); justify-content: center; align-items: center; z-index: 15000; }
        .modal-box { background: white; padding: 20px; border-radius: 8px; width: 450px; max-width: 90vw; max-height: 85vh; overflow-y: auto; border: 2px solid #0f172a; }
        
        /* テンキーモーダル */
        #numpadModal { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(0,0,0,0.5); justify-content: center; align-items: center; z-index: 20000; }
        .numpad-content { background: white; padding: 20px; border-radius: 8px; width: 240px; border: 2px solid #0f172a; text-align: center; }
        .numpad-screen { width: 100%; height: 36px; font-size: 1.3rem; text-align: right; margin-bottom: 10px; padding: 4px 8px; box-sizing: border-box; border-radius: 4px; border: 1px solid var(--border-color); background: #f8fafc; font-family: inherit; }
        .numpad-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 6px; }
        .numpad-grid button { padding: 12px; font-size: 1.1rem; background: #fff; color: #0f172a; border: 1px solid var(--border-color); border-radius: 4px; font-weight: bold; }
        .numpad-grid button:hover { background: #f1f5f9; }

        /* 印刷選択モーダル */
        #printModal { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(0,0,0,0.5); justify-content: center; align-items: center; z-index: 10000; }
        .modal-btn { display: block; width: 100%; padding: 10px; margin: 8px 0; font-size: 0.95rem; border-radius: 4px; cursor: pointer; font-weight: bold; }
    </style>
</head>
<body>

    <!-- 起動時スプラッシュ画面 -->
    <div id="splash-screen">
        <div class="splash-logo">MOBITAROU</div>
        <div class="splash-sub">株式会社ベルアビエ 総合システム</div>
        <div class="splash-hint">▼ 起動中に下スワイプで隠しゲーム起動</div>
    </div>

    <!-- 隠し暇つぶしゲームモーダル -->
    <div id="hiddenGameModal">
        <h2 style="margin:5px; font-size:1.1rem; font-family:monospace;">HIDDEN MINI GAME (暇つぶし用)</h2>
        <canvas id="gameCanvas" width="320" height="400"></canvas>
        <p style="font-size:0.8rem; margin:8px;">左右タップ/ドラッグでバー移動</p>
        <button onclick="closeHiddenGame()" style="padding:6px 15px; font-size:0.85rem;">ゲーム終了してシステムへ</button>
    </div>

    <!-- 商品選択・追加モーダル -->
    <div id="itemSelectModal">
        <div class="modal-box">
            <h3 style="margin-top:0; color:#0f172a;">📦 商品マスタから選択または新規登録</h3>
            <div style="margin-bottom:10px;">
                <input type="text" id="itemSearchInput" placeholder="🔍 商品名や価格で検索..." oninput="filterMasterItems()" style="width:100%; padding:6px; box-sizing:border-box; border:1px solid #cbd5e1; border-radius:4px;">
            </div>
            <div id="masterListContainer" style="max-height:200px; overflow-y:auto; border:1px solid #cbd5e1; border-radius:4px; margin-bottom:15px; background:#f8fafc;"></div>
            
            <hr style="border:0; border-top:1px dashed #cbd5e1; margin:10px 0;">
            
            <h4 style="margin:5px 0; color:#0f172a; font-size:0.9rem;">＋ 新規商品を登録して追加</h4>
            <div style="display:flex; gap:6px; margin-bottom:6px;">
                <input type="text" id="newMasterCode" placeholder="コード(例:003800)" style="flex:1; padding:6px; font-size:0.85rem;">
                <input type="text" id="newMasterName" placeholder="品名(例:衣類380円売)" style="flex:2; padding:6px; font-size:0.85rem;">
            </div>
            <div style="display:flex; gap:6px; margin-bottom:10px;">
                <select id="newMasterPrice" style="flex:1; padding:6px; font-size:0.85rem;">
                    <option value="380">380円</option><option value="500">500円</option><option value="700">700円</option>
                    <option value="1000" selected>1,000円</option><option value="1200">1,200円</option><option value="1500">1,500円</option>
                    <option value="1900">1,900円</option><option value="2000">2,000円</option><option value="2900">2,900円</option>
                    <option value="3000">3,000円</option><option value="3900">3,900円</option><option value="4900">4,900円</option>
                    <option value="5900">5,900円</option><option value="6900">6,900円</option><option value="7900">7,900円</option>
                    <option value="8900">8,900円</option><option value="9900">9,900円</option><option value="11900">11,900円</option>
                    <option value="12900">12,900円</option><option value="14900">14,900円</option><option value="17900">17,900円</option>
                    <option value="19900">19,900円</option><option value="24900">24,900円</option><option value="27900">27,900円</option>
                    <option value="29900">29,900円</option>
                </select>
                <button onclick="addNewMasterAndSelect()" style="background:#16a34a; color:white; border:none; padding:6px 12px; border-radius:4px; font-weight:bold; cursor:pointer;">登録して追加</button>
            </div>
            <button onclick="closeItemSelectModal()" style="width:100%; background:#64748b; color:white; border:none; padding:8px; border-radius:4px; font-weight:bold; cursor:pointer;">キャンセル</button>
        </div>
    </div>

    <!-- テンキーモーダル -->
    <div id="numpadModal">
        <div class="numpad-content">
            <div style="font-size: 0.85rem; font-weight: bold; color: #0f172a; margin-bottom: 6px;" id="numpadTitle">数値を入力</div>
            <input type="text" id="numpadScreen" class="numpad-screen" readonly value="0">
            <div class="numpad-grid">
                <button onclick="npPress('7')">7</button>
                <button onclick="npPress('8')">8</button>
                <button onclick="npPress('9')">9</button>
                <button onclick="npPress('4')">4</button>
                <button onclick="npPress('5')">5</button>
                <button onclick="npPress('6')">6</button>
                <button onclick="npPress('1')">1</button>
                <button onclick="npPress('2')">2</button>
                <button onclick="npPress('3')">3</button>
                <button onclick="npPress('0')">0</button>
                <button onclick="npClear()">C</button>
                <button onclick="npBack()">←</button>
            </div>
            <div style="display:flex; gap:6px; margin-top:10px;">
                <button onclick="npClose()" style="flex:1; background:#64748b; color:white; padding:8px; font-size:0.85rem;">キャンセル</button>
                <button onclick="npConfirm()" style="flex:1; background:#0f172a; color:white; padding:8px; font-size:0.85rem;">確定</button>
            </div>
        </div>
    </div>

    <!-- 印刷選択モーダル -->
    <div id="printModal">
        <div class="modal-box" style="width:300px; text-align:center;">
            <h3 style="color:#0f172a; margin-top:0;">🖨️ 印刷形式の選択</h3>
            <p style="font-size:0.85rem; color:#666;">出力する用紙・プリンターを選んでください</p>
            <button class="modal-btn" style="background:#0f172a; color:white;" onclick="executePrint('A4')">📄 A4用紙で印刷</button>
            <button class="modal-btn" style="background:#475569; color:white;" onclick="executePrint('MPB20')">🧾 セイコー MP-B20 (サーマル)</button>
            <button class="modal-btn" style="background:#e2e8f0; color:#334155;" onclick="closePrintModal()">キャンセル</button>
        </div>
    </div>

<div class="container">
    
    <!-- 端末ホーム（営業画面） -->
    <div id="app-home" class="tool-view active">
        <div class="handy-terminal">
            <div class="terminal-top-tabs">
                <button class="top-tab-btn active-tab" id="tabSeisan" onclick="clickSeisanNav()">精算委託</button>
                <button class="top-tab-btn" id="tabNyuko" onclick="switchMainTab('nyuko')">入出庫</button>
                <button class="top-tab-btn" id="tabNippo" onclick="switchMainTab('nippo')">日報</button>
            </div>

            <div class="terminal-screen-body">
                <div class="terminal-banner">
                    <div class="banner-face-box">
                        <span>(^_^)v<br>📡送信中</span>
                    </div>
                    <div class="banner-title" id="terminalTitleLabel">営業</div>
                </div>

                <div class="terminal-sys-name" id="terminalSubLabel">モビロー販売管理システム</div>

                <table class="terminal-info-table">
                    <tr>
                        <td class="label-col">端末No.</td>
                        <td>07</td>
                    </tr>
                    <tr>
                        <td class="label-col">担当者</td>
                        <td>
                            <select id="tantoSelect">
                                <option value="大和地豊" selected>大和地豊</option>
                                <option value="スタッフA">スタッフA</option>
                                <option value="スタッフB">スタッフB</option>
                            </select>
                        </td>
                    </tr>
                    <tr>
                        <td class="label-col">未送信データ</td>
                        <td style="color:#b91c1c; font-weight:bold; text-align:center;">【有】</td>
                    </tr>
                </table>
            </div>

            <div class="terminal-bottom-buttons">
                <button class="bottom-action-btn" onclick="clickShokai()">照会</button>
                <button class="bottom-action-btn" onclick="clickTanaoroshi()">棚卸</button>
                <button class="bottom-action-btn" onclick="openOtherMenu()">その他</button>
            </div>
        </div>
    </div>

    <!-- 精算委託サブメニューモーダル選択画面 -->
    <div id="app-seisan-menu" class="tool-view">
        <button class="back-btn" onclick="goHome()">⬅ 端末ホームに戻る</button>
        <div class="sub-panel" style="max-width:500px; margin:20px auto; text-align:center;">
            <h3>📋 精算委託・納品・返品メニュー</h3>
            <p style="font-size:0.85rem; color:#64748b;">行いたい作業を選択してください</p>
            <div style="display:flex; flex-direction:column; gap:12px; margin-top:15px;">
                <button onclick="openDenpyoMode('nohin')" style="padding:14px; font-size:1rem; background:#1e293b; color:white; border:none; border-radius:6px; font-weight:bold; cursor:pointer;">📄 委託納品書の発行・作成</button>
                <button onclick="openDenpyoMode('henpin')" style="padding:14px; font-size:1rem; background:#b91c1c; color:white; border:none; border-radius:6px; font-weight:bold; cursor:pointer;">🔄 返品伝票の作成</button>
                <button onclick="openDenpyoMode('seikyu')" style="padding:14px; font-size:1rem; background:#475569; color:white; border:none; border-radius:6px; font-weight:bold; cursor:pointer;">💰 精算・請求書確認</button>
            </div>
        </div>
    </div>

    <!-- ① 伝票発行・返品・店舗管理システム画面 -->
    <div id="app-denpyo" class="tool-view">
        <button class="back-btn" onclick="switchApp('seisan-menu')">⬅ 精算委託メニューに戻る</button>
        <div style="float:right;">
            <button onclick="openPrintModal()" id="printButton" style="padding:6px 12px; background:#0f172a; color:white; border:none; border-radius:4px; font-weight:bold; cursor:pointer;">🖨️ 伝票を印刷する</button>
        </div>
        <div style="clear:both; margin-bottom:10px;"></div>

        <div class="main-grid">
            <div class="sub-panel">
                <h3 style="margin-top:0; color:#0f172a; font-size:0.95rem;">🏪 店舗選択</h3>
                <input type="text" id="searchInput" class="search-box" placeholder="🔍 店舗名や住所検索..." oninput="filterStores()" style="width:100%; box-sizing:border-box; padding:6px; margin-bottom:8px; border:1px solid #cbd5e1; border-radius:4px;">
                <button style="width:100%; margin-bottom:10px; padding:6px; font-size:0.85rem;" onclick="openNewStoreForm()">＋ 新規店舗登録</button>
                <div id="storeListContainer" style="max-height:55vh; overflow-y:auto;"></div>
            </div>

            <div class="sub-panel">
                <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:10px;">
                    <h3 id="selectedStoreName" style="margin:0; color:#0f172a; font-size:1rem;">店舗を選択</h3>
                    <div>
                        <button onclick="switchTab('nohin')" id="btnTabNohin" style="font-size:0.8rem; padding:4px 8px;">委託納品書</button>
                        <button onclick="switchTab('henpin')" id="btnTabHenpin" style="font-size:0.8rem; padding:4px 8px;">返品伝票</button>
                        <button onclick="switchTab('seikyu')" id="btnTabSeikyu" style="font-size:0.8rem; padding:4px 8px;">請求書</button>
                    </div>
                </div>

                <div class="input-control-box">
                    <div style="display:flex; justify-content:space-between; margin-bottom:8px; align-items:center; flex-wrap:wrap; gap:5px;">
                        <div>
                            <label>発行日:</label>
                            <input type="text" id="inputDate" value="26/10/26 21:30" style="width:110px;" oninput="renderPreview()">
                            <label>伝票No:</label>
                            <input type="text" id="inputDenpyoNo" value="0726100111" style="width:90px;" oninput="renderPreview()">
                        </div>
                        <div>
                            <label style="font-weight:bold;">掛率:</label>
                            <select id="selectRate" onchange="changeStoreRate(this.value)" style="font-weight:bold; color:#0f172a; border:1px solid #cbd5e1; padding:3px; border-radius:4px;">
                                <option value="50TAX">50TAX</option>
                                <option value="55TAX">55TAX</option>
                                <option value="60TAX" selected>60TAX</option>
                                <option value="65TAX">65TAX</option>
                                <option value="70TAX">70TAX</option>
                            </select>
                        </div>
                    </div>
                    <div style="font-size:0.8rem; font-weight:bold; margin-bottom:4px; color:#0f172a;">明細データ (在庫ゼロから選択・追加)</div>
                    <table class="item-table" id="itemTable">
                        <thead>
                            <tr><th>コード</th><th>品名</th><th>数量</th><th>定価(円)</th><th>単価</th><th>操作</th></tr>
                        </thead>
                        <tbody id="itemRowsBody"></tbody>
                    </table>
                    <button onclick="openItemSelectModal()" style="font-size:0.75rem; padding:5px 12px; background:#16a34a; color:white; border:none; border-radius:4px; font-weight:bold; cursor:pointer;">＋ リストから商品を選択・追加</button>
                </div>

                <div id="previewArea" class="preview-container mode-a4"></div>

                <div class="sub-panel" id="storeEditSection" style="display:none; margin-top:10px;">
                    <h4 style="margin-top:0; color:#0f172a; font-size:0.9rem;" id="storeEditTitle">店舗情報の編集・登録</h4>
                    <input type="hidden" id="editIndex">
                    <div style="margin-bottom:6px;"><label>店舗名</label><input type="text" id="editName" style="width:100%; box-sizing:border-box;"></div>
                    <div style="margin-bottom:6px;"><label>住所</label><input type="text" id="editAddress" style="width:100%; box-sizing:border-box;"></div>
                    <div style="margin-bottom:6px;"><label>電話番号</label><input type="text" id="editTel" style="width:100%; box-sizing:border-box;"></div>
                    <div style="margin-bottom:6px;"><label>郵便番号</label><input type="text" id="editZip" style="width:100%; box-sizing:border-box;"></div>
                    <div style="margin-bottom:6px;"><label>掛率設定</label><input type="text" id="editRate" value="60TAX" style="width:100%; box-sizing:border-box;"></div>
                    <button onclick="saveStoreEdit()" style="margin-top:6px; width:100%; padding:6px; font-size:0.85rem; background:#0f172a; color:white;">💾 保存する</button>
                </div>
            </div>
        </div>
    </div>

    <!-- ② 棚卸画面 -->
    <div id="app-tanaoroshi" class="tool-view">
        <button class="back-btn" onclick="goHome()">⬅ 端末ホームに戻る</button>
        <div class="sub-panel" style="max-width:600px; margin:20px auto;">
            <h3 style="margin-top:0; color:#0f172a;">📦 棚卸・在庫スキャンシステム</h3>
            <p style="font-size:0.85rem; color:#64748b;">現在の倉庫・車載在庫の棚卸しカウントを行います。</p>
            <div class="input-control-box">
                <div style="margin-bottom:8px;"><label>商品コード / バーコードスキャン:</label></div>
                <div style="display:flex; gap:8px;">
                    <input type="text" id="tanaCode" placeholder="例: 001000" style="flex:1; padding:6px;">
                    <button onclick="alert('バーコードデータを読み込みました')" style="padding:6px 12px; background:#0f172a; color:white; border:none; border-radius:4px;">追加</button>
                </div>
            </div>
            <table class="item-table">
                <thead><tr><th>コード</th><th>品名</th><th>実査数</th><th>操作</th></tr></thead>
                <tbody>
                    <tr><td>001000</td><td>衣類 1,000円売</td><td><input type="text" value="0" style="width:60px; text-align:center;"></td><td><button style="background:#991b1b; color:white; border:none; padding:2px 6px;">削除</button></td></tr>
                </tbody>
            </table>
            <button onclick="alert('棚卸データを正常に送信しました！')" style="width:100%; padding:10px; background:#16a34a; color:white; border:none; border-radius:4px; font-weight:bold; margin-top:10px;">💾 棚卸データを送信・確定する</button>
        </div>
    </div>

    <!-- ③ その他メニュー画面 -->
    <div id="app-other" class="tool-view">
        <button class="back-btn" onclick="goHome()">⬅ 端末ホームに戻る</button>
        <div class="sub-panel" style="max-width:500px; margin:20px auto; text-align:center;">
            <h3>🛠️ その他のツール選択</h3>
            <div style="display:flex; flex-direction:column; gap:10px; margin-top:15px;">
                <button onclick="switchApp('history')" style="padding:12px; font-size:1rem; background:#0f172a; color:white; border:none; border-radius:6px; font-weight:bold; cursor:pointer;">📜 発行・操作履歴の確認</button>
                <button onclick="switchApp('calendar')" style="padding:12px; font-size:1rem;">📅 スケジュール・カレンダー</button>
                <button onclick="switchApp('memo')" style="padding:12px; font-size:1rem;">📝 クイックメモ書き</button>
                <button onclick="switchApp('calc')" style="padding:12px; font-size:1rem;">🔢 ミニ電卓ツール</button>
            </div>
        </div>
    </div>

    <!-- 履歴確認画面 -->
    <div id="app-history" class="tool-view">
        <button class="back-btn" onclick="switchApp('other')">⬅ その他メニューに戻る</button>
        <div class="sub-panel" style="max-width:700px; margin:20px auto;">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:10px;">
                <h3 style="margin:0; color:#0f172a;">📜 ローカル保存されたアクション履歴</h3>
                <button onclick="clearActionLogs()" style="background:#b91c1c; color:white; border:none; padding:5px 10px; font-size:0.8rem; border-radius:4px;">履歴をすべて消去</button>
            </div>
            <p style="font-size:0.85rem; color:#64748b;">この端末（ブラウザ）に記憶された伝票発行などの操作ログ一覧です。</p>
            <div id="historyListContainer" style="max-height:50vh; overflow-y:auto; border:1px solid #cbd5e1; border-radius:6px; background:#f8fafc; padding:10px;"></div>
        </div>
    </div>

    <!-- カレンダー画面 -->
    <div id="app-calendar" class="tool-view">
        <button class="back-btn" onclick="switchApp('other')">⬅ その他メニューに戻る</button>
        <div class="sub-panel" style="text-align:center; padding: 30px;">
            <h3 style="color:#0f172a;">📅 スケジュール・カレンダー</h3>
            <p style="font-size:0.85rem;">2026年10月 / 納品スケジュール</p>
            <div style="font-size: 1rem; background: #f1f5f9; padding: 15px; border-radius: 6px; border: 1px solid var(--border-color); display: inline-block; margin-top: 10px;">
                <strong>本日: 2026年10月26日 (月)</strong>
            </div>
        </div>
    </div>

    <!-- メモ画面 -->
    <div id="app-memo" class="tool-view">
        <button class="back-btn" onclick="switchApp('other')">⬅ その他メニューに戻る</button>
        <div class="sub-panel">
            <h3 style="margin-top:0; color:#0f172a; font-size:0.95rem;">📝 クイックメモ書き</h3>
            <textarea id="quickMemo" style="width:100%; height:250px; padding:10px; box-sizing:border-box;" placeholder="メモを入力..." oninput="saveMemo()"></textarea>
        </div>
    </div>

    <!-- 電卓画面 -->
    <div id="app-calc" class="tool-view">
        <button class="back-btn" onclick="switchApp('other')">⬅ その他メニューに戻る</button>
        <div class="sub-panel" style="display:flex; justify-content:center;">
            <div style="max-width:280px; width:100%; background:#f8fafc; padding:15px; border-radius:8px; border:1px solid var(--border-color); text-align:center;">
                <h3 style="margin-top:0; color:#0f172a; font-size:1rem;">🔢 ミニ電卓</h3>
                <input type="text" id="calcScreen" style="width:100%; height:36px; font-size:1.4rem; text-align:right; margin-bottom:12px; padding:4px 8px; box-sizing:border-box; background:white;" readonly value="0">
                <div style="display:grid; grid-template-columns: repeat(4, 1fr); gap:6px;">
                    <button onclick="calcClear()">C</button>
                    <button onclick="calcAppend('/')">÷</button>
                    <button onclick="calcAppend('*')">×</button>
                    <button onclick="calcAppend('-')">-</button>
                    <button onclick="calcAppend('7')">7</button>
                    <button onclick="calcAppend('8')">8</button>
                    <button onclick="calcAppend('9')">9</button>
                    <button onclick="calcAppend('+')">+</button>
                    <button onclick="calcAppend('4')">4</button>
                    <button onclick="calcAppend('5')">5</button>
                    <button onclick="calcAppend('6')">6</button>
                    <button onclick="calcEqual()" style="grid-row: span 2; background:#0f172a; color:white;">=</button>
                    <button onclick="calcAppend('1')">1</button>
                    <button onclick="calcAppend('2')">2</button>
                    <button onclick="calcAppend('3')">3</button>
                    <button onclick="calcAppend('0')" style="grid-column: span 2;">0</button>
                    <button onclick="calcAppend('.')">.</button>
                </div>
            </div>
        </div>
    </div>

</div>

<script>
let stores = [
    {name: "しゅーずとと乃", address: "茨城県猿島郡境町松岡町122", tel: "0280-87-3610", zip: "306-0433", rate: "55TAX"},
    {name: "思い出浪漫館", address: "茨城県久慈郡大子町袋田978", tel: "0295-72-3111", zip: "319-3523", rate: "50TAX"},
    {name: "森林の温泉", address: "茨城県久慈郡大子町矢田15-12", tel: "0295-72-3200", zip: "319-3552", rate: "60TAX"},
    {name: "大子温泉やみぞ", address: "茨城県久慈郡大子町矢田524-2", tel: "0295-72-1511", zip: "319-3552", rate: "60TAX"},
    {name: "パリエブティック（閉店）", address: "茨城県結城市8586-1", tel: "0296-32-6228", zip: "307-0001", rate: "60TAX"},
    {name: "レマ", address: "茨城県結城市中央町1-1-1", tel: "090-4741-9173", zip: "320-0015", rate: "55TAX"},
    {name: "キュッセ古河", address: "茨城県古河市下辺見223-1", tel: "0280-33-7456", zip: "306-0235", rate: "55TAX"},
    {name: "金砂の湯", address: "茨城県常陸太田市上宮河内町336", tel: "0294-76-9919", zip: "313-0101", rate: "60TAX"},
    {name: "竜っちゃん乃湯", address: "茨城県常陸太田市天下野町1629", tel: "0294-87-0543", zip: "313-0351", rate: "60TAX"},
    {name: "三太の湯", address: "茨城県常陸大宮市諸沢5071", tel: "0295-57-4121", zip: "319-3105", rate: "60TAX"},
    {name: "ぬく森の湯", address: "茨城県常陸大宮市太田市大中町2076-6", tel: "0294-82-3366", zip: "311-0505", rate: "60TAX"},
    {name: "四季彩館", address: "茨城県常陸大宮市長倉407-2", tel: "0295-55-2626", zip: "311-4613", rate: "60TAX"},
    {name: "ささの湯", address: "茨城県常陸大宮市氷之沢3139", tel: "0295-58-2682", zip: "319-2513", rate: "60TAX"},
    {name: "アイビー大観", address: "岩手県盛岡市繋湯ノ舘37-1", tel: "019-691-7466", zip: "020-0055", rate: "60TAX"},
    {name: "アイビー紫苑", address: "岩手県盛岡市繋湯ノ舘74-2", tel: "019-691-7603", zip: "020-0055", rate: "60TAX"},
    {name: "ホテルセキア", address: "熊本県玉名郡南関町関村1556", tel: "0968-69-6116", zip: "861-0804", rate: "60TAX"},
    {name: "ニーマ館林", address: "群馬県館林市楠木町3648-1", tel: "0276-71-0707", zip: "374-0004", rate: "60TAX"},
    {name: "アゼリアモール", address: "群馬県館林市楠木町3648-1", tel: "0276-71-0522", zip: "374-0004", rate: "55TAX"},
    {name: "AMIRI 桶川店", address: "埼玉県桶川市若宮1-5-2 桶川マイン2F", tel: "090-8847-0620", zip: "363-0022", rate: "55TAX"},
    {name: "山口呉服店", address: "埼玉県桶川市寿2-4-1", tel: "048-771-1105", zip: "363-0016", rate: "60TAX"},
    {name: "ホテルヘリテイジ四季の湯", address: "埼玉県熊谷市小江川228", tel: "048-536-1212", zip: "360-0103", rate: "60TAX"},
    {name: "バレンザポー鴻巣店", address: "埼玉県鴻巣市本町1-1-2 エルミこうのす2F", tel: "048-501-5242", zip: "365-0038", rate: "55TAX"},
    {name: "松代病院", address: "新潟県十日町市松代3592-2", tel: "025-597-2100", zip: "942-1526", rate: "60TAX"},
    {name: "くわどり湯ったり村", address: "新潟県上越市皆口601", tel: "025-541-2611", zip: "949-1738", rate: "60TAX"},
    {name: "松孝商店", address: "千葉県鴨川市小湊183", tel: "04-7095-3010", zip: "299-5501", rate: "60TAX"},
    {name: "亀の井ホテル喜速川", address: "栃木県さくら市喜速川5296-1", tel: "0286-86-6266", zip: "329-1412", rate: "60TAX"},
    {name: "花子", address: "栃木県宇都宮市宇都宮駅ビル1F", tel: "0286-33-5353", zip: "320-0802", rate: "55TAX"},
    {name: "ディアーキッズ", address: "栃木県宇都宮市今泉1-2-9", tel: "070-6475-7667", zip: "326-0814", rate: "60TAX"},
    {name: "マダムミツコ", address: "栃木県宇都宮市今泉町522", tel: "028-600-5488", zip: "321-0962", rate: "60TAX"},
    {name: "レディースショップぽすと", address: "栃木県宇都宮市若松原2-18-14", tel: "028-653-6071", zip: "321-0139", rate: "60TAX"},
    {name: "ファーマーズフォレスト", address: "栃木県宇都宮市新里町丙254", tel: "028-665-8800", zip: "321-2118", rate: "60TAX"},
    {name: "てんぴょう", address: "栃木県下野市小金井789", tel: "0285-43-1238", zip: "329-0414", rate: "60TAX"},
    {name: "渡辺商店", address: "栃木県河内郡上三川梁275", tel: "0285-53-6004", zip: "329-0523", rate: "60TAX"},
    {name: "もてぎ化粧品", address: "栃木県佐野市植下町1090-1-103", tel: "0283-22-4852", zip: "327-0835", rate: "60TAX"},
    {name: "ミウラ", address: "栃木県佐野市高萩町1324-1", tel: "0283-20-4195", zip: "327-0821", rate: "60TAX"},
    {name: "㈱小山ブランド・思川", address: "栃木県小山市大字下国府塚25-1", tel: "0285-38-0201", zip: "323-0065", rate: "60TAX"},
    {name: "ACLOSET", address: "栃木県足利市朝倉町245-5", tel: "090-2407-2970", zip: "326-0823", rate: "60TAX"},
    {name: "モードサロンにしむら", address: "栃木県足利市通三丁目", tel: "0284-21-7488", zip: "326-0814", rate: "60TAX"},
    {name: "アップワード美容室", address: "栃木県足利市堀北町2923-14", tel: "0284-72-1548", zip: "326-0831", rate: "70TAX"},
    {name: "ROZA", address: "栃木県足利市葉鹿町1-24-1", tel: "090-1811-0643", zip: "326-0814", rate: "55TAX"},
    {name: "メディカルセンターとちのき", address: "栃木県栃木市大町39-5", tel: "0282-88-1243", zip: "328-0071", rate: "60TAX"},
    {name: "メディカルセンターしもつが", address: "栃木県栃木市大平町川連420-1", tel: "0282-20-6488", zip: "329-4498", rate: "60TAX"},
    {name: "塩原グリーンビレッジ", address: "栃木県那須塩原市塩原1230", tel: "0287-32-2751", zip: "329-2921", rate: "60TAX"},
    {name: "一萬亭", address: "栃木県那須塩原市塩原630-1", tel: "0287-48-6995", zip: "329-2921", rate: "60TAX"},
    {name: "高砂屋", address: "栃木県那須塩原市塩原678-3", tel: "0287-32-2242", zip: "329-2921", rate: "60TAX"},
    {name: "布屋", address: "栃木県那須塩原市塩原803-1", tel: "0287-32-4138", zip: "329-2921", rate: "60TAX"},
    {name: "乃木温泉ホテル", address: "栃木県那須塩原市下永田1-993-11", tel: "0287-37-4126", zip: "329-2712", rate: "60TAX"},
    {name: "レディースマリ", address: "栃木県那須塩原市三島2-8", tel: "0287-37-1434", zip: "329-2752", rate: "60TAX"},
    {name: "幸の湯温泉", address: "栃木県那須塩原市百村3536-1", tel: "0287-69-1126", zip: "325-0115", rate: "60TAX"},
    {name: "三河屋", address: "栃木県那須郡那珂川町馬頭馬頭1885 ポニィタウン内", tel: "0287-92-2003", zip: "324-0613", rate: "60TAX"},
    {name: "シマムラ", address: "栃木県那須郡那須町寺子丙3-142", tel: "0287-72-0133", zip: "329-3222", rate: "60TAX"},
    {name: "オリエンタルガーデン", address: "栃木県那須郡那須町湯本", tel: "0287-76-4700", zip: "325-0301", rate: "60TAX"},
    {name: "サンバレー本館", address: "栃木県那須郡那須町湯本203", tel: "0287-76-3800", zip: "325-0392", rate: "60TAX"},
    {name: "愛寿荘", address: "栃木県那須郡那須町湯本206", tel: "0287-76-3060", zip: "325-0301", rate: "65TAX"},
    {name: "山楽", address: "栃木県日光市鬼怒川温泉大原1060", tel: "0288-76-2211", zip: "321-2522", rate: "60TAX"},
    {name: "鬼怒川パークホテルズ", address: "栃木県日光市鬼怒川温泉大原1409", tel: "0288-77-1289", zip: "321-2522", rate: "60TAX"},
    {name: "東急リゾーツ＆ステイ株式会社", address: "栃木県日光市鬼怒川温泉大原14-10", tel: "0288-76-0105", zip: "321-2522", rate: "55TAX"},
    {name: "仁王尊プラザ", address: "栃木県日光市鬼怒川温泉大原371-1", tel: "0288-76-2721", zip: "321-2522", rate: "60TAX"},
    {name: "万葉亭", address: "栃木県日光市鬼怒川温泉大原390-3", tel: "0287-77-3900", zip: "321-2522", rate: "60TAX"},
    {name: "晃陽苑", address: "栃木県日光市瀬尾1640-14", tel: "0288-53-1150", zip: "321-1264", rate: "60TAX"},
    {name: "正喜屋呉服店", address: "栃木県矢板市本町6-6", tel: "0287-43-0073", zip: "329-2164", rate: "60TAX"},
    {name: "牛岳温泉健康センター", address: "富山県富山市山田赤目谷31-28", tel: "076-457-2131", zip: "930-2117", rate: "60TAX"},
    {name: "ひまわり", address: "福島県郡山市安積2-46-4", tel: "024-945-5317", zip: "963-0107", rate: "60TAX"},
    {name: "なりた温泉", address: "福島県郡山市安積町車川原字付田27", tel: "024-947-2626", zip: "963-0107", rate: "60TAX"},
    {name: "オンリー・ドゥー", address: "福島県郡山市桑野2-14-8", tel: "024-935-6191", zip: "963-8025", rate: "60TAX"},
    {name: "モアリッチ", address: "福島県郡山市虎丸町2-19", tel: "024-922-7211", zip: "963-8014", rate: "60TAX"},
    {name: "まねきの湯", address: "福島県郡山市向河原町4-40", tel: "024-941-4126", zip: "963-8801", rate: "60TAX"},
    {name: "丸正呉服店", address: "福島県石川郡玉川村", tel: "0247-57-2025", zip: "963-6312", rate: "60TAX"},
    {name: "江戸屋", address: "福島県田村郡三春町字中町22", tel: "0247-62-3956", zip: "963-7766", rate: "60TAX"},
    {name: "三春の里", address: "福島県田村郡三春町大字西方字石畑487-1", tel: "0247-62-8010", zip: "963-7722", rate: "65TAX"},
    {name: "三ツ美屋旅館", address: "福島県田村郡三春町尼ヶ屋126", tel: "0247-62-2504", zip: "963-7745", rate: "65TAX"},
    {name: "スーパーブックス新白河", address: "福島県田村市新白河新高山3", tel: "0248-31-1811", zip: "961-0853", rate: "60TAX"},
    {name: "シャイニー", address: "福島県白河市本町3", tel: "0248-24-8227", zip: "961-0908", rate: "60TAX"},
    {name: "リンデン", address: "兵庫県神戸市中央区相生町3-2-1", tel: "078-361-8256", zip: "650-0025", rate: "60TAX"}
];

let masterItems = [
    { code: "000380", name: "衣類 380円売", price: 380 },
    { code: "000500", name: "衣類 500円売", price: 500 },
    { code: "000700", name: "衣類 700円売", price: 700 },
    { code: "001000", name: "衣類 1,000円売", price: 1000 },
    { code: "001200", name: "衣類 1,200円売", price: 1200 },
    { code: "001500", name: "衣類 1,500円売", price: 1500 },
    { code: "001900", name: "衣類 1,900円売", price: 1900 },
    { code: "002000", name: "衣類 2,000円売", price: 2000 },
    { code: "002900", name: "衣類 2,900円売", price: 2900 },
    { code: "003000", name: "衣類 3,000円売", price: 3000 },
    { code: "003900", name: "衣類 3,900円売", price: 3900 },
    { code: "004900", name: "衣類 4,900円売", price: 4900 },
    { code: "005900", name: "衣類 5,900円売", price: 5900 },
    { code: "006900", name: "衣類 6,900円売", price: 6900 },
    { code: "007900", name: "衣類 7,900円売", price: 7900 },
    { code: "008900", name: "衣類 8,900円売", price: 8900 },
    { code: "009900", name: "衣類 9,900円売", price: 9900 },
    { code: "011900", name: "衣類 11,900円売", price: 11900 },
    { code: "012900", name: "衣類 12,900円売", price: 12900 },
    { code: "014900", name: "衣類 14,900円売", price: 14900 },
    { code: "017900", name: "衣類 17,900円売", price: 17900 },
    { code: "019900", name: "衣類 19,900円売", price: 19900 },
    { code: "024900", name: "衣類 24,900円売", price: 24900 },
    { code: "027900", name: "衣類 27,900円売", price: 27900 },
    { code: "029900", name: "衣類 29,900円売", price: 29900 }
];

let currentStoreIndex = 0;
let currentTab = 'nohin';
let nohinItems = [];
let henpinItems = [];

let activeInputCallback = null;
let touchStartY = 0;

window.onload = function() {
    renderStoreList(stores);
    selectStore(0);
    renderItemTable();
    const savedMemo = localStorage.getItem('mobitarou_memo');
    if (savedMemo) document.getElementById('quickMemo').value = savedMemo;

    const splash = document.getElementById('splash-screen');
    splash.addEventListener('touchstart', e => { touchStartY = e.touches[0].clientY; });
    splash.addEventListener('touchend', e => {
        let touchEndY = e.changedTouches[0].clientY;
        if (touchEndY - touchStartY > 50) {
            splash.style.display = 'none';
            openHiddenGame();
        }
    });
    splash.addEventListener('mousedown', e => { touchStartY = e.clientY; });
    splash.addEventListener('mouseup', e => {
        let touchEndY = e.clientY;
        if (touchEndY - touchStartY > 50) {
            splash.style.display = 'none';
            openHiddenGame();
        }
    });
};

/* --- 隠しブロック崩しゲーム --- */
let gameInterval = null;
function openHiddenGame() {
    document.getElementById('hiddenGameModal').style.display = 'flex';
    initGame();
}
function closeHiddenGame() {
    document.getElementById('hiddenGameModal').style.display = 'none';
    clearInterval(gameInterval);
}

function initGame() {
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    let x = canvas.width / 2, y = canvas.height - 30, dx = 2, dy = -2, ballRadius = 6;
    let paddleHeight = 10, paddleWidth = 60, paddleX = (canvas.width - paddleWidth) / 2;
    let brickRowCount = 3, brickColumnCount = 5, brickWidth = 50, brickHeight = 15, brickPadding = 10, brickOffsetTop = 30, brickOffsetLeft = 20;
    
    let bricks = [];
    for(let c=0; c<brickColumnCount; c++) {
        bricks[c] = [];
        for(let r=0; r<brickRowCount; r++) { bricks[c][r] = { x: 0, y: 0, status: 1 }; }
    }
    
    canvas.addEventListener('mousemove', e => {
        let rect = canvas.getBoundingClientRect();
        let rx = e.clientX - rect.left;
        if(rx > 0 && rx < canvas.width) paddleX = rx - paddleWidth/2;
    });
    canvas.addEventListener('touchmove', e => {
        let rect = canvas.getBoundingClientRect();
        let rx = e.touches[0].clientX - rect.left;
        if(rx > 0 && rx < canvas.width) paddleX = rx - paddleWidth/2;
    });

    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        for(let c=0; c<brickColumnCount; c++) {
            for(let r=0; r<brickRowCount; r++) {
                let b = bricks[c][r];
                if(b.status == 1) {
                    let bx = (c*(brickWidth+brickPadding))+brickOffsetLeft;
                    let by = (r*(brickHeight+brickPadding))+brickOffsetTop;
                    b.x = bx; b.y = by;
                    ctx.beginPath(); ctx.rect(bx, by, brickWidth, brickHeight); ctx.fillStyle = "#fff"; ctx.fill(); ctx.closePath();
                    if(x > bx && x < bx+brickWidth && y > by && y < by+brickHeight) { dy = -dy; b.status = 0; }
                }
            }
        }
        ctx.beginPath(); ctx.arc(x, y, ballRadius, 0, Math.PI*2); ctx.fillStyle = "#fff"; ctx.fill(); ctx.closePath();
        ctx.beginPath(); ctx.rect(paddleX, canvas.height-paddleHeight, paddleWidth, paddleHeight); ctx.fillStyle = "#fff"; ctx.fill(); ctx.closePath();

        if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) dx = -dx;
        if(y + dy < ballRadius) dy = -dy;
        else if(y + dy > canvas.height-ballRadius) {
            if(x > paddleX && x < paddleX + paddleWidth) dy = -dy;
            else { y = canvas.height - 30; x = canvas.width / 2; }
        }
        x += dx; y += dy;
    }
    if(gameInterval) clearInterval(gameInterval);
    gameInterval = setInterval(draw, 10);
}

function clickSeisanNav() {
    switchApp('seisan-menu');
}

function switchMainTab(tabName) {
    document.querySelectorAll('.top-tab-btn').forEach(b => b.classList.remove('active-tab'));
    const titleLabel = document.getElementById('terminalTitleLabel');
    const subLabel = document.getElementById('terminalSubLabel');

    if(tabName === 'seisan') {
        document.getElementById('tabSeisan').classList.add('active-tab');
        titleLabel.innerText = "営業";
        subLabel.innerText = "モビロー販売管理システム (精算委託モード)";
    } else if(tabName === 'nyuko') {
        document.getElementById('tabNyuko').classList.add('active-tab');
        titleLabel.innerText = "入出庫";
        subLabel.innerText = "モビロー在庫・ハンディ入出庫管理";
    } else if(tabName === 'nippo') {
        document.getElementById('tabNippo').classList.add('active-tab');
        titleLabel.innerText = "日報";
        subLabel.innerText = "本日の売上・訪問日報データ確認";
    }
}

function clickShokai() {
    openDenpyoMode('nohin');
}

function clickTanaoroshi() {
    switchApp('tanaoroshi');
}

function openOtherMenu() {
    switchApp('other');
}

function openDenpyoMode(mode) {
    currentTab = mode;
    switchApp('denpyo');
    highlightActiveTabButton();
    renderItemTable();
}

function switchApp(appId) {
    document.querySelectorAll('.tool-view').forEach(v => v.classList.remove('active'));
    document.getElementById('app-' + appId).classList.add('active');
    if(appId === 'history') {
        renderHistoryList();
    }
}

function goHome() {
    switchApp('home');
}

function getPrefecture(address) {
    const prefs = ['茨城県', '岩手県', '熊本県', '群馬県', '埼玉県', '新潟県', '千葉県', '栃木県', '富山県', '福島県', '兵庫県'];
    for (let p of prefs) { if (address.includes(p)) return p; }
    return "その他";
}

function renderStoreList(storeArray) {
    const container = document.getElementById('storeListContainer');
    container.innerHTML = '';
    const grouped = {};
    storeArray.forEach(store => {
        const pref = getPrefecture(store.address);
        store._originalIndex = stores.indexOf(store);
        if (!grouped[pref]) grouped[pref] = [];
        grouped[pref].push(store);
    });

    for (const pref in grouped) {
        const prefDiv = document.createElement('div');
        prefDiv.style.marginBottom = "8px";
        prefDiv.innerHTML = `<div style="font-weight:bold; font-size:0.8rem; color:#0f172a; border-bottom:1px dashed #cbd5e1; padding-bottom:2px; margin-bottom:4px;">📍 ${pref}</div>`;
        grouped[pref].forEach(store => {
            const item = document.createElement('div');
            item.className = `store-item ${store._originalIndex === currentStoreIndex ? 'active' : ''}`;
            item.innerHTML = `
                <span onclick="selectStore(${store._originalIndex})" style="flex-grow:1; cursor:pointer;">${store.name}</span>
                <div class="store-actions">
                    <button onclick="editStore(${store._originalIndex}); event.stopPropagation();">編集</button>
                    <button style="background:#991b1b; color:white; border:none;" onclick="deleteStore(${store._originalIndex}); event.stopPropagation();">削除</button>
                </div>
            `;
            prefDiv.appendChild(item);
        });
        container.appendChild(prefDiv);
    }
}

function filterStores() {
    const keyword = document.getElementById('searchInput').value.toLowerCase();
    const filtered = stores.filter(s => s.name.toLowerCase().includes(keyword) || s.address.toLowerCase().includes(keyword));
    renderStoreList(filtered);
}

function selectStore(index) {
    currentStoreIndex = index;
    const store = stores[index];
    document.getElementById('selectedStoreName').innerText = "🏢 " + store.name;
    if (store.rate) document.getElementById('selectRate').value = store.rate;
    document.getElementById('storeEditSection').style.display = 'none';
    renderPreview();
    renderStoreList(stores);
}

function changeStoreRate(newRate) {
    stores[currentStoreIndex].rate = newRate;
    renderItemTable();
}

function getRateMultiplier() {
    const rateStr = document.getElementById('selectRate').value;
    const num = parseInt(rateStr, 10);
    return !isNaN(num) ? num / 100 : 0.6;
}

function openItemSelectModal() {
    document.getElementById('itemSelectModal').style.display = 'flex';
    renderMasterList(masterItems);
}

function closeItemSelectModal() {
    document.getElementById('itemSelectModal').style.display = 'none';
}

function renderMasterList(items) {
    const container = document.getElementById('masterListContainer');
    container.innerHTML = '';
    if(items.length === 0) {
        container.innerHTML = `<div style="padding:10px; text-align:center; font-size:0.85rem; color:#64748b;">該当する商品がありません</div>`;
        return;
    }
    items.forEach(m => {
        const div = document.createElement('div');
        div.style.cssText = "padding:8px 10px; border-bottom:1px solid #e2e8f0; display:flex; justify-content:space-between; align-items:center; cursor:pointer; font-size:0.85rem;";
        div.innerHTML = `<span><strong>[${m.code}]</strong> ${m.name} (定価:${m.price.toLocaleString()}円)</span> <button style="background:#1e293b; color:white; border:none; padding:3px 8px; border-radius:4px; font-size:0.75rem;">選択</button>`;
        div.onmouseover = () => div.style.background = '#f1f5f9';
        div.onmouseout = () => div.style.background = '#f8fafc';
        div.onclick = () => selectMasterItem(m);
        container.appendChild(div);
    });
}

function filterMasterItems() {
    const kw = document.getElementById('itemSearchInput').value.toLowerCase();
    const filtered = masterItems.filter(m => m.name.toLowerCase().includes(kw) || m.code.includes(kw) || String(m.price).includes(kw));
    renderMasterList(filtered);
}

function selectMasterItem(m) {
    const targetList = (currentTab === 'henpin') ? henpinItems : nohinItems;
    targetList.push({ code: m.code, name: m.name, qty: 0, listPrice: m.price });
    closeItemSelectModal();
    renderItemTable();
}

function addNewMasterAndSelect() {
    const code = document.getElementById('newMasterCode').value.trim();
    const name = document.getElementById('newMasterName').value.trim();
    const price = parseInt(document.getElementById('newMasterPrice').value, 10);
    
    if(!code || !name) {
        alert("商品コードと品名を入力してください。");
        return;
    }
    const newItem = { code, name, price };
    masterItems.push(newItem);
    selectMasterItem(newItem);
    
    document.getElementById('newMasterCode').value = '';
    document.getElementById('newMasterName').value = '';
}

function openNumpad(title, currentVal, callback) {
    document.getElementById('numpadTitle').innerText = title;
    document.getElementById('numpadScreen').value = currentVal;
    activeInputCallback = callback;
    document.getElementById('numpadModal').style.display = 'flex';
}
function npPress(char) {
    const screen = document.getElementById('numpadScreen');
    screen.value = (screen.value === '0') ? char : screen.value + char;
}
function npClear() { document.getElementById('numpadScreen').value = '0'; }
function npBack() {
    const screen = document.getElementById('numpadScreen');
    screen.value = screen.value.length > 1 ? screen.value.slice(0, -1) : '0';
}
function npClose() {
    document.getElementById('numpadModal').style.display = 'none';
    activeInputCallback = null;
}
function npConfirm() {
    const val = Number(document.getElementById('numpadScreen').value);
    if (activeInputCallback) activeInputCallback(val);
    npClose();
}

function highlightActiveTabButton() {
    document.getElementById('btnTabNohin').style.background = (currentTab === 'nohin') ? '#1e293b' : '#e2e8f0';
    document.getElementById('btnTabNohin').style.color = (currentTab === 'nohin') ? '#fff' : '#0f172a';
    document.getElementById('btnTabHenpin').style.background = (currentTab === 'henpin') ? '#b91c1c' : '#e2e8f0';
    document.getElementById('btnTabHenpin').style.color = (currentTab === 'henpin') ? '#fff' : '#0f172a';
    document.getElementById('btnTabSeikyu').style.background = (currentTab === 'seikyu') ? '#1e293b' : '#e2e8f0';
    document.getElementById('btnTabSeikyu').style.color = (currentTab === 'seikyu') ? '#fff' : '#0f172a';
}

function renderItemTable() {
    highlightActiveTabButton();
    const multiplier = getRateMultiplier();
    const tbody = document.getElementById('itemRowsBody');
    tbody.innerHTML = '';
    
    const targetItems = (currentTab === 'henpin') ? henpinItems : nohinItems;
    
    if(targetItems.length === 0) {
        tbody.innerHTML = `<tr><td colspan="6" style="padding:15px; color:#64748b; font-size:0.85rem;">商品が追加されていません（在庫ゼロ）下のボタンから選択してください。</td></tr>`;
        renderPreview();
        return;
    }

    targetItems.forEach((item, idx) => {
        const calculatedUnitPrice = Math.round(item.listPrice * multiplier);
        item.currentUnitPrice = calculatedUnitPrice;

        const tr = document.createElement('tr');
        tr.innerHTML = `
            <td>${item.code}</td>
            <td style="text-align:left; padding-left:6px;">${item.name}</td>
            <td><input type="text" readonly value="${item.qty}" onclick="openNumpad('数量を入力', ${item.qty}, (val)=>{updateItem(${idx}, 'qty', val);})"></td>
            <td>${item.listPrice.toLocaleString()} 円</td>
            <td style="font-weight:bold; color:#0f172a;">${calculatedUnitPrice.toLocaleString()} 円</td>
            <td><button style="background:#991b1b; color:white; border:none; padding:2px 5px; font-size:0.7rem;" onclick="removeItem(${idx})">✕</button></td>
        `;
        tbody.appendChild(tr);
    });
    renderPreview();
}

function updateItem(idx, field, val) {
    const targetItems = (currentTab === 'henpin') ? henpinItems : nohinItems;
    targetItems[idx][field] = Number(val);
    renderItemTable();
}

function removeItem(idx) {
    const targetItems = (currentTab === 'henpin') ? henpinItems : nohinItems;
    targetItems.splice(idx, 1);
    renderItemTable();
}

function renderPreview() {
    const store = stores[currentStoreIndex];
    const area = document.getElementById('previewArea');
    const dateVal = document.getElementById('inputDate').value;
    const noVal = document.getElementById('inputDenpyoNo').value;
    const currentRateStr = document.getElementById('selectRate').value;
    const multiplier = getRateMultiplier();
    
    if (currentTab === 'nohin') {
        let totalAmount = 0;
        let rowsHtml = '';
        nohinItems.forEach(item => {
            const unitP = Math.round(item.listPrice * multiplier);
            const amount = item.qty * unitP;
            totalAmount += amount;
            rowsHtml += `<tr><td>${item.code}</td><td>${item.name}</td><td>${item.qty}</td><td>${unitP.toLocaleString()}</td><td>${amount.toLocaleString()}</td></tr>`;
        });

        area.innerHTML = `
            <div class="denpyo-title">委 託 納 品 書</div>
            <div class="denpyo-header" style="display:flex; justify-content:space-between;">
                <div>
                    <strong>${store.name} 様</strong>
                </div>
                <div style="text-align:right;">
                    伝票No. ${noVal}<br>
                    日付: ${dateVal}<br>
                    掛率: ${currentRateStr}<br>
                    担当: 大和地豊 (080-5562-4600)
                </div>
            </div>
            <table class="denpyo-table">
                <tr><th>コード</th><th>品名</th><th>数量</th><th>単価</th><th>金額</th></tr>
                ${rowsHtml || '<tr><td colspan="5">明細なし</td></tr>'}
                <tr><td colspan="4" style="text-align:right; font-weight:bold;">計</td><td>${totalAmount.toLocaleString()}</td></tr>
            </table>
            <div style="font-size:0.75rem; margin-top:10px;">
                株式会社ベルアビエ デコレ事業部 | 福島県田村市船引町船引字下扇田52<br>
                TEL 0247-81-2215 / 振替: 02210-7-78715
            </div>
        `;
    } else if (currentTab === 'henpin') {
        let totalAmount = 0;
        let rowsHtml = '';
        henpinItems.forEach(item => {
            const unitP = Math.round(item.listPrice * multiplier);
            const amount = item.qty * unitP;
            totalAmount += amount;
            rowsHtml += `<tr><td>${item.code}</td><td>${item.name}</td><td>${item.qty}</td><td>${unitP.toLocaleString()}</td><td>-${amount.toLocaleString()}</td></tr>`;
        });

        area.innerHTML = `
            <div class="denpyo-title" style="color:#b91c1c;">返 品 伝 票</div>
            <div class="denpyo-header" style="display:flex; justify-content:space-between;">
                <div>
                    <strong>${store.name} 様</strong>
                </div>
                <div style="text-align:right;">
                    伝票No. ${noVal}<br>
                    日付: ${dateVal}<br>
                    掛率: ${currentRateStr}<br>
                    担当: 大和地豊 (080-5562-4600)
                </div>
            </div>
            <table class="denpyo-table">
                <tr><th>コード</th><th>品名</th><th>数量</th><th>単価</th><th>返品金額</th></tr>
                ${rowsHtml || '<tr><td colspan="5">明細なし</td></tr>'}
                <tr><td colspan="4" style="text-align:right; font-weight:bold;">返品合計</td><td>-${totalAmount.toLocaleString()}</td></tr>
            </table>
            <div style="font-size:0.75rem; margin-top:10px;">
                株式会社ベルアビエ デコレ事業部 (返品処理分)<br>
                TEL 0247-81-2215
            </div>
        `;
    } else {
        area.innerHTML = `
            <div class="denpyo-title">請 求 書 (精算)</div>
            <div class="denpyo-header" style="display:flex; justify-content:space-between;">
                <div>
                    <strong>${store.name} 様</strong>
                </div>
                <div style="text-align:right;">
                    伝票No. ${noVal}<br>
                    日付: ${dateVal}<br>
                    掛率: ${currentRateStr}
                </div>
            </div>
            <table class="denpyo-table">
                <tr><th>コード</th><th>品名</th><th>前在</th><th>本在</th><th>売数</th><th>単価</th><th>金額</th></tr>
                <tr><td>001000</td><td>衣類 1,000円売</td><td>0</td><td>0</td><td>0</td><td>600</td><td>0</td></tr>
                <tr><td colspan="6" style="text-align:right; font-weight:bold;">合計金額</td><td>0</td></tr>
            </table>
            <div style="font-size:0.75rem; margin-top:10px;">
                前回残高: 0 | お買上額: 0 | ご請求額: 0<br>
                <strong>請求残高: 0 円</strong>
            </div>
        `;
    }
}

function switchTab(tab) {
    currentTab = tab;
    renderItemTable();
}

/* --- 伝票データをローカルストレージへ保存する関数 --- */
function saveActionLog() {
    const store = stores[currentStoreIndex];
    const dateVal = document.getElementById('inputDate').value;
    const noVal = document.getElementById('inputDenpyoNo').value;
    const tantoVal = document.getElementById('tantoSelect').value;

    let typeText = "委託納品書";
    if(currentTab === 'henpin') typeText = "返品伝票";
    if(currentTab === 'seikyu') typeText = "請求書";

    const logData = {
        date: dateVal,
        denpyoNo: noVal,
        storeName: store.name,
        type: typeText,
        tanto: tantoVal,
        timestamp: new Date().toLocaleString()
    };

    // 既存の履歴を読み込んで追加
    let history = JSON.parse(localStorage.getItem('mobitarou_action_logs') || '[]');
    history.unshift(logData); // 新しいものを先頭に追加
    localStorage.setItem('mobitarou_action_logs', JSON.stringify(history));
}

function openPrintModal() { document.getElementById('printModal').style.display = 'flex'; }
function closePrintModal() { document.getElementById('printModal').style.display = 'none'; }
function executePrint(mode) {
    closePrintModal();
    
    // 印刷実行時に自動でアクションを記憶
    saveActionLog();

    const area = document.getElementById('previewArea');
    area.className = mode === 'A4' ? 'preview-container mode-a4' : 'preview-container mode-mpb20';
    window.print();
}

function renderHistoryList() {
    const container = document.getElementById('historyListContainer');
    let history = JSON.parse(localStorage.getItem('mobitarou_action_logs') || '[]');
    
    if(history.length === 0) {
        container.innerHTML = `<div style="padding:15px; text-align:center; color:#64748b; font-size:0.85rem;">保存されたアクション履歴はありません。</div>`;
        return;
    }

    let html = `<table class="item-table" style="background:white;"><thead><tr><th>日時</th><th>担当</th><th>種別</th><th>店舗名</th><th>伝票No</th></tr></thead><tbody>`;
    history.forEach(h => {
        html += `<tr><td>${h.date}</td><td>${h.tanto}</td><td><span style="font-weight:bold; color:${h.type==='返品伝票'?'#b91c1c':'#1e293b'}">${h.type}</span></td><td style="text-align:left; padding-left:6px;">${h.storeName}</td><td>${h.denpyoNo}</td></tr>`;
    });
    html += `</tbody></table>`;
    container.innerHTML = html;
}

function clearActionLogs() {
    if(confirm("⚠️ 保存されているすべての操作・発行履歴を消去してもよろしいですか？")) {
        localStorage.removeItem('mobitarou_action_logs');
        renderHistoryList();
    }
}

function deleteStore(index) {
    const storeName = stores[index].name;
    if (confirm(`⚠️ 本当に店舗「${storeName}」を削除してもよろしいですか？`)) {
        stores.splice(index, 1);
        if (currentStoreIndex >= stores.length) currentStoreIndex = 0;
        renderStoreList(stores);
        if (stores.length > 0) selectStore(currentStoreIndex);
    }
}

function openNewStoreForm() {
    document.getElementById('storeEditSection').style.display = 'block';
    document.getElementById('storeEditTitle').innerText = "新規店舗登録";
    document.getElementById('editIndex').value = '';
    document.getElementById('editName').value = '';
    document.getElementById('editAddress').value = '';
    document.getElementById('editTel').value = '';
    document.getElementById('editZip').value = '';
    document.getElementById('editRate').value = '60TAX';
}

function editStore(index) {
    const store = stores[index];
    document.getElementById('storeEditSection').style.display = 'block';
    document.getElementById('storeEditTitle').innerText = "店舗情報の編集";
    document.getElementById('editIndex').value = index;
    document.getElementById('editName').value = store.name;
    document.getElementById('editAddress').value = store.address;
    document.getElementById('editTel').value = store.tel || '';
    document.getElementById('editZip').value = store.zip || '';
    document.getElementById('editRate').value = store.rate || '60TAX';
}

function saveStoreEdit() {
    const index = document.getElementById('editIndex').value;
    const name = document.getElementById('editName').value;
    const address = document.getElementById('editAddress').value;
    const tel = document.getElementById('editTel').value;
    const zip = document.getElementById('editZip').value;
    const rate = document.getElementById('editRate').value;
    if (!name || !address) { alert("店舗名と住所を入力してください。"); return; }
    if (index === "") {
        stores.push({name, address, tel, zip, rate});
        selectStore(stores.length - 1);
    } else {
        stores[index] = {name, address, tel, zip, rate};
        selectStore(index);
    }
    document.getElementById('storeEditSection').style.display = 'none';
    renderStoreList(stores);
}

function saveMemo() { localStorage.setItem('mobitarou_memo', document.getElementById('quickMemo').value); }
function calcAppend(val) {
    const s = document.getElementById('calcScreen');
    s.value = (s.value === '0' || s.value === 'エラー') ? val : s.value + val;
}
function calcClear() { document.getElementById('calcScreen').value = '0'; }
function calcEqual() {
    const s = document.getElementById('calcScreen');
    try { s.value = eval(s.value); } catch { s.value = 'エラー'; }
}
</script>

</body>
</html>
