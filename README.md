<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ツッコミ10000文字カウンター</title>
  <!-- Google Fontsからスタイリッシュなフォントを読み込み -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@700;900&family=M+PLUS+Rounded+1c:wght@700;900&display=swap" rel="stylesheet">
  <style>
    /* DS-DIGIT フォントの読み込み */
    @font-face {
      font-family: 'DS-Digital';
      src: url('https://db.onlinewebfonts.com/t/8e22783d707ad140bffe18b2a3812529.eot');
      src: url('https://db.onlinewebfonts.com/t/8e22783d707ad140bffe18b2a3812529.eot?#iefix') format('embedded-opentype'),
           url('https://db.onlinewebfonts.com/t/8e22783d707ad140bffe18b2a3812529.woff2') format('woff2'),
           url('https://db.onlinewebfonts.com/t/8e22783d707ad140bffe18b2a3812529.woff') format('woff'),
           url('https://db.onlinewebfonts.com/t/8e22783d707ad140bffe18b2a3812529.ttf') format('truetype'),
           url('https://db.onlinewebfonts.com/t/8e22783d707ad140bffe18b2a3812529.svg#DS-Digital') format('svg');
      font-weight: normal;
      font-style: normal;
    }
    
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }
    
    body {
      font-family: 'Hiragino Kaku Gothic ProN', 'Meiryo', sans-serif;
      background-color: #1a202c;
      color: white;
      height: 100vh;
      overflow: hidden;
    }
    
    #app {
      height: 100vh;
      padding: 1rem;
      display: flex;
      flex-direction: column;
    }
    
    /* Operator View Styles */
    .controls {
      display: flex;
      justify-content: space-between;
      margin-bottom: 1rem;
    }
    
    .controls-group {
      display: flex;
      gap: 10px;
    }
    
    .btn {
      background-color: #3182ce;
      color: white;
      padding: 0.5rem 1rem;
      border-radius: 0.25rem;
      font-weight: bold;
      border: none;
      cursor: pointer;
    }
    
    .btn:hover {
      background-color: #2c5282;
    }
    
    .btn-red {
      background-color: #e53e3e;
    }
    
    .btn-red:hover {
      background-color: #c53030;
    }
    
    .count-display {
      text-align: center;
      margin-bottom: 1rem;
    }
    
    .count-text {
      font-size: 3rem;
      font-weight: bold;
      color: #f56565;
      font-family: 'DS-Digital', monospace;
    }
    
    .progress-bar {
      width: 100%;
      height: 0.5rem;
      background-color: #4a5568;
      border-radius: 9999px;
      margin-top: 0.5rem;
      overflow: hidden;
    }
    
    .progress-fill {
      height: 100%;
      background-color: #e53e3e;
      border-radius: 9999px;
      transition: width 0.5s ease-out;
    }
    
    .latest-tsukkomi {
      border: 2px solid #4a5568;
      border-radius: 0.5rem;
      padding: 1rem;
      margin-bottom: 1rem;
      text-align: center;
      background-color: #2d3748;
    }
    
    .latest-label {
      font-size: 0.875rem;
      color: #a0aec0;
      margin-bottom: 0.25rem;
    }
    
    .latest-text {
      font-size: 1.5rem;
      font-weight: bold;
    }
    
    .input-area {
      display: flex;
      margin-bottom: 1rem;
      align-items: flex-start;
    }
    
    .input-field {
      flex-grow: 1;
      padding: 1rem;
      font-size: 1.25rem;
      border: 2px solid white;
      background-color: #2d3748;
      color: white;
      border-top-left-radius: 0.25rem;
      border-bottom-left-radius: 0.25rem;
      max-width: 70%; /* 横幅を短く */
      height: 100px; /* 縦幅を広げる */
      resize: none; /* リサイズを無効化 */
    }
    
    .input-field:focus {
      outline: none;
    }
    
    .submit-btn {
      background-color: #e53e3e;
      color: white;
      padding: 0 1.5rem;
      font-size: 1.25rem;
      font-weight: bold;
      border: none;
      border-top-right-radius: 0.25rem;
      border-bottom-right-radius: 0.25rem;
      cursor: pointer;
      height: 100px; /* 入力フィールドと同じ高さに */
    }
    
    .submit-btn:hover {
      background-color: #c53030;
    }
    
    .real-time-section {
      margin-bottom: 1rem;
      display: flex;
      align-items: center;
    }
    
    .real-time-toggle {
      display: flex;
      align-items: center;
      margin-right: 1rem;
    }
    
    .toggle-switch {
      position: relative;
      display: inline-block;
      width: 60px;
      height: 34px;
      margin-right: 0.5rem;
    }
    
    .toggle-switch input {
      opacity: 0;
      width: 0;
      height: 0;
    }
    
    .toggle-slider {
      position: absolute;
      cursor: pointer;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: #4a5568;
      transition: .4s;
      border-radius: 34px;
    }
    
    .toggle-slider:before {
      position: absolute;
      content: "";
      height: 26px;
      width: 26px;
      left: 4px;
      bottom: 4px;
      background-color: white;
      transition: .4s;
      border-radius: 50%;
    }
    
    input:checked + .toggle-slider {
      background-color: #38a169;
    }
    
    input:checked + .toggle-slider:before {
      transform: translateX(26px);
    }
    
    .history-list {
      flex-grow: 1;
      overflow-y: auto;
      border: 1px solid #4a5568;
      border-radius: 0.25rem;
      padding: 0.5rem;
      background-color: #2d3748;
    }
    
    .history-header {
      font-size: 0.875rem;
      color: #a0aec0;
      margin-bottom: 0.25rem;
    }
    
    .history-item {
      margin-bottom: 0.25rem;
      padding: 0.25rem;
      border-bottom: 1px solid #4a5568;
    }
    
    .history-text {
      color: #e2e8f0;
    }
    
    .history-count {
      color: #a0aec0;
      font-size: 0.75rem;
      margin-left: 0.5rem;
      font-family: 'DS-Digital', monospace;
    }
    
    .instructions {
      margin-top: 1rem;
      padding: 0.5rem;
      background-color: #2d3748;
      border-radius: 0.25rem;
      font-size: 0.875rem;
      color: #a0aec0;
    }
    
    .instructions ol {
      margin-left: 1.25rem;
    }
    
    /* モーダルスタイル */
    .modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(0, 0, 0, 0.7);
      z-index: 100;
      justify-content: center;
      align-items: center;
    }
    
    .modal-content {
      background-color: #2d3748;
      padding: 1.5rem;
      border-radius: 0.5rem;
      width: 90%;
      max-width: 500px;
      text-align: center;
    }
    
    .modal-title {
      font-size: 1.5rem;
      margin-bottom: 1rem;
    }
    
    .modal-buttons {
      display: flex;
      justify-content: center;
      gap: 1rem;
      margin-top: 1.5rem;
    }
    
    .modal-btn {
      padding: 0.5rem 1.5rem;
      border-radius: 0.25rem;
      font-weight: bold;
      cursor: pointer;
      border: none;
    }
    
    .modal-btn-cancel {
      background-color: #4a5568;
      color: white;
    }
    
    .modal-btn-confirm {
      background-color: #e53e3e;
      color: white;
    }
    
    /* Audience View Styles - 高級感アップ版 */
    .audience-view {
      width: 100%;
      height: 100vh;
      background: radial-gradient(ellipse at center, #1e2747 0%, #090a0f 100%);
      color: white;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 3rem;
      position: relative;
      overflow: hidden;
    }
    
    /* 背景エフェクト */
    .background-effect {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><rect width="100" height="100" fill="none" stroke="rgba(255,255,255,0.05)" stroke-width="0.5"/></svg>');
      opacity: 0.5;
      pointer-events: none;
      z-index: 0;
    }
    
    .light-effect {
      position: absolute;
      top: -50%;
      left: -50%;
      width: 200%;
      height: 200%;
      background: radial-gradient(ellipse at center, rgba(100, 150, 255, 0.15) 0%, rgba(0, 0, 0, 0) 70%);
      pointer-events: none;
      z-index: 0;
      animation: light-rotate 60s infinite linear;
      transform-origin: center;
    }
    
    @keyframes light-rotate {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
    
    /* メインコンテンツ */
    .audience-content {
      position: relative;
      z-index: 1;
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }
    
    /* ツッコミ表示エリア - 固定サイズ、自動調整 */
    .audience-tsukkomi-container {
      width: 100%;
      flex-grow: 1;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      margin-bottom: 3rem;
    }
    
    .audience-tsukkomi {
      width: 90%;
      max-width: 1400px;
      height: 50vh; /* 固定高さ */
      padding: 2rem;
      position: relative;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 1rem;
      overflow: hidden;
      background: rgba(0, 0, 0, 0.25);
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5), 
                  0 0 0 1px rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(10px);
    }
    
    .tsukkomi-content {
      width: 100%;
      height: 100%;
      overflow: visible; /* テキストが多い場合、はみ出す */
      display: flex;
      align-items: center;
      justify-content: center;
      text-align: center;
    }
    
    .audience-tsukkomi-text {
      font-size: 4.5rem; /* フォントサイズを小さく */
      font-weight: 900;
      word-break: break-word;
      font-family: 'M PLUS Rounded 1c', 'Noto Sans JP', sans-serif;
      letter-spacing: 0.05em;
      line-height: 1.3;
      background: linear-gradient(to bottom, #ffffff 20%, #adc5ff 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      filter: drop-shadow(0 2px 10px rgba(255, 255, 255, 0.3));
    }
    
    .audience-typing {
      background: linear-gradient(to bottom, #64b5f6 20%, #1e88e5 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      filter: drop-shadow(0 2px 20px rgba(33, 150, 243, 0.4));
    }
    
    /* フッター部分（カウンターとプログレスバー） */
    .audience-footer {
      position: relative;
      width: 100%;
      display: flex;
      flex-direction: column;
      align-items: center;
      z-index: 1;
      background: rgba(0, 0, 0, 0.4);
      backdrop-filter: blur(10px);
      border-radius: 1rem;
      padding: 2rem;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3),
                  0 0 0 1px rgba(255, 255, 255, 0.1);
    }
    
    /* カウンターエリア */
    .audience-count-container {
      display: flex;
      justify-content: space-between;
      align-items: center;
      width: 100%;
      margin-bottom: 2rem;
    }
    
    .audience-counts {
      display: flex;
      align-items: baseline;
      gap: 3rem;
    }
    
    .audience-count-text {
      font-size: 8rem;
      font-weight: bold;
      color: #ff3d00;
      text-shadow: 0 0 20px rgba(255, 61, 0, 0.7);
      font-family: 'DS-Digital', monospace;
      letter-spacing: 6px;
      filter: drop-shadow(0 2px 8px rgba(255, 61, 0, 0.8));
    }
    
    .audience-last-count {
      font-size: 5rem;
      color: #00e5ff;
      font-family: 'DS-Digital', monospace;
      letter-spacing: 4px;
      text-shadow: 0 0 15px rgba(0, 229, 255, 0.7);
      filter: drop-shadow(0 2px 8px rgba(0, 229, 255, 0.8));
    }
    
    /* プログレスバー */
    .audience-progress {
      width: 100%;
      height: 3rem;
      background-color: rgba(0, 0, 0, 0.5);
      border-radius: 9999px;
      overflow: hidden;
      box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.8);
      position: relative;
    }
    
    .audience-progress-fill {
      height: 100%;
      background: linear-gradient(90deg, #ff416c, #ff4b2b);
      border-radius: 9999px;
      transition: width 0.8s cubic-bezier(0.22, 1, 0.36, 1);
      box-shadow: 0 0 20px rgba(255, 65, 108, 0.5);
      position: relative;
      overflow: hidden;
    }
    
    .audience-progress-fill::after {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: linear-gradient(
        90deg,
        rgba(255, 255, 255, 0) 0%,
        rgba(255, 255, 255, 0.4) 50%,
        rgba(255, 255, 255, 0) 100%
      );
      animation: shine 3s infinite;
    }
    
    @keyframes shine {
      0% { transform: translateX(-100%); }
      100% { transform: translateX(100%); }
    }
    
    /* プログレスバーの数字表示 */
    .progress-percentage {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 1.5rem;
      font-weight: bold;
      color: white;
      text-shadow: 0 0 5px rgba(0, 0, 0, 0.8);
      z-index: 2;
    }
    
    /* タイピングインジケーター */
    .typing-indicator {
      display: inline-block;
      margin-left: 15px;
    }
    
    .typing-dot {
      display: inline-block;
      width: 15px;
      height: 15px;
      border-radius: 50%;
      background-color: #00e5ff;
      margin-right: 8px;
      animation: typingAnimation 1.4s infinite ease-in-out;
      box-shadow: 0 0 15px rgba(0, 229, 255, 0.7);
    }
    
    .typing-dot:nth-child(1) {
      animation-delay: 0s;
    }
    
    .typing-dot:nth-child(2) {
      animation-delay: 0.2s;
    }
    
    .typing-dot:nth-child(3) {
      animation-delay: 0.4s;
    }
    
    @keyframes typingAnimation {
      0% { transform: translateY(0); }
      50% { transform: translateY(-15px); }
      100% { transform: translateY(0); }
    }
    
    /* 達成メッセージ */
    .goal-achieved {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      background-color: rgba(0, 0, 0, 0.85);
      z-index: 50;
      backdrop-filter: blur(10px);
    }
    
    .goal-message {
      background: linear-gradient(135deg, #0575E6, #021B79);
      padding: 4rem;
      border-radius: 2rem;
      text-align: center;
      color: white;
      animation: float 3s infinite alternate ease-in-out;
      box-shadow: 0 20px 70px rgba(5, 117, 230, 0.6),
                  0 0 0 1px rgba(255, 255, 255, 0.2);
      max-width: 800px;
      position: relative;
      overflow: hidden;
    }
    
    .goal-message::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><rect width="100" height="100" fill="none" stroke="rgba(255,255,255,0.1)" stroke-width="0.5"/></svg>');
      opacity: 0.3;
    }
    
    @keyframes float {
      0% { transform: translateY(0) scale(1); }
      100% { transform: translateY(-20px) scale(1.05); }
    }
    
    .goal-title {
      font-size: 5rem;
      font-weight: bold;
      margin-bottom: 2rem;
      display: block;
      text-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
    }
    
    .goal-subtitle {
      font-size: 3rem;
      text-shadow: 0 2px 10px rgba(0, 0, 0, 0.3);
    }
    
    /* その他の共通スタイル */
    .hidden {
      display: none;
    }
  </style>
</head>
<body>
  <!-- Operator View -->
  <div id="operatorView" class="operator-view">
    <div class="controls">
      <div class="controls-group">
        <button id="openAudienceBtn" class="btn">観客表示を別ウィンドウで開く</button>
        <button id="resetBtn" class="btn btn-red">カウンターをリセット</button>
      </div>
      <div id="windowStatus" class="window-status">観客表示ウィンドウ: 未表示</div>
    </div>
    
    <div class="count-display">
      <div id="countText" class="count-text">0 / 10000 文字</div>
      <div class="progress-bar">
        <div id="progressFill" class="progress-fill" style="width: 0%"></div>
      </div>
    </div>
    
    <div class="latest-tsukkomi">
      <div class="latest-label">最新のツッコミ:</div>
      <p id="latestTsukkomi" class="latest-text">まだツッコミはありません</p>
    </div>
    
    <div class="real-time-section">
      <div class="real-time-toggle">
        <label class="toggle-switch">
          <input type="checkbox" id="realtimeToggle" checked>
          <span class="toggle-slider"></span>
        </label>
        <span>リアルタイム入力モード</span>
      </div>
      <div id="inputModeStatus">有効（入力中の文字が即座に観客画面に表示されます）</div>
    </div>
    
    <div class="input-area">
      <textarea
        id="tsukkomiInput"
        class="input-field"
        placeholder="ツッコミを入力..."
      ></textarea>
      <button id="addButton" class="submit-btn">追加</button>
    </div>
    
    <div class="history-list">
      <div class="history-header">過去のツッコミ一覧:</div>
      <div id="historyItems">
        <div class="text-center p-4 text-gray-500">
          まだツッコミはありません
        </div>
      </div>
    </div>
    
    <div class="instructions">
      <p><strong>使い方:</strong></p>
      <ol>
        <li>「観客表示を別ウィンドウで開く」ボタンをクリックして観客表示ウィンドウを開きます</li>
        <li>観客表示ウィンドウをプロジェクターやモニターに表示します</li>
        <li>このウィンドウでツッコミを入力すると、観客ウィンドウにリアルタイムで反映されます</li>
        <li>「リアルタイム入力モード」をオンにすると、入力中の文字がそのまま表示されます</li>
        <li>「追加」ボタンまたはEnterキーで文字数をカウントに追加します</li>
        <li>ESCキーで全画面表示を解除できます</li>
        <li>「カウンターをリセット」ボタンでカウントとツッコミ履歴をクリアできます</li>
      </ol>
    </div>
  </div>

  <!-- リセット確認モーダル -->
  <div id="resetModal" class="modal">
    <div class="modal-content">
      <h2 class="modal-title">カウンターをリセットしますか？</h2>
      <p>文字数カウントとツッコミ履歴が全てクリアされます</p>
      <div class="modal-buttons">
        <button id="cancelResetBtn" class="modal-btn modal-btn-cancel">キャンセル</button>
        <button id="confirmResetBtn" class="modal-btn modal-btn-confirm">リセットする</button>
      </div>
    </div>
  </div>

  <!-- Audience View (hidden by default) -->
  <div id="audienceView" class="audience-view hidden">
    <!-- 背景エフェクト -->
    <div class="background-effect"></div>
    <div class="light-effect"></div>
    
    <div class="audience-content">
      <!-- ツッコミ表示エリア - 固定サイズ、自動調整またははみ出し -->
      <div class="audience-tsukkomi-container">
        <div class="audience-tsukkomi">
          <div class="tsukkomi-content">
            <p id="audienceTsukkomiText" class="audience-tsukkomi-text"></p>
            <div id="typingIndicator" class="typing-indicator hidden">
              <div class="typing-dot"></div>
              <div class="typing-dot"></div>
              <div class="typing-dot"></div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- フッター（カウンターとプログレスバー） -->
      <div class="audience-footer">
        <!-- カウンターエリア -->
        <div class="audience-count-container">
          <div class="audience-counts">
            <h1 id="audienceCountText" class="audience-count-text">0 / 10000</h1>
            <div id="audienceLastCount" class="audience-last-count">0</div>
          </div>
        </div>
        
        <!-- プログレスバー -->
        <div class="audience-progress">
          <div id="audienceProgressFill" class="audience-progress-fill" style="width: 0%"></div>
          <div id="progressPercentage" class="progress-percentage">0%</div>
        </div>
      </div>
    </div>
    
    <!-- 達成メッセージ -->
    <div id="goalAchieved" class="goal-achieved hidden">
      <div class="goal-message">
        <span class="goal-title">目標達成！🎉</span>
        <p class="goal-subtitle">10000文字のツッコミ完了！</p>
      </div>
    </div>
  </div>

  <script>
    // Constants
    const GOAL = 10000;
    
    // State variables
    let tsukkomiList = [];
    let totalCharacters = 0;
    let audienceWindow = null;
    let currentInputText = '';
    let isTyping = false;
    let typingTimer = null;
    let lastTsukkomiLength = 0;
    
    // Check if this is audience view based on a URL hash instead of query parameter
    // This works better with local files
    const isAudienceView = window.location.hash === '#audience';
    
    // DOM elements - Operator view
    const operatorView = document.getElementById('operatorView');
    const openAudienceBtn = document.getElementById('openAudienceBtn');
    const resetBtn = document.getElementById('resetBtn');
    const windowStatus = document.getElementById('windowStatus');
    const countText = document.getElementById('countText');
    const progressFill = document.getElementById('progressFill');
    const latestTsukkomi = document.getElementById('latestTsukkomi');
    const tsukkomiInput = document.getElementById('tsukkomiInput');
    const addButton = document.getElementById('addButton');
    const historyItems = document.getElementById('historyItems');
    const realtimeToggle = document.getElementById('realtimeToggle');
    const inputModeStatus = document.getElementById('inputModeStatus');
    
    // DOM elements - Modals
    const resetModal = document.getElementById('resetModal');
    const cancelResetBtn = document.getElementById('cancelResetBtn');
    const confirmResetBtn = document.getElementById('confirmResetBtn');
    
    // DOM elements - Audience view
    const audienceView = document.getElementById('audienceView');
    const audienceTsukkomiText = document.getElementById('audienceTsukkomiText');
    const audienceCountText = document.getElementById('audienceCountText');
    const audienceLastCount = document.getElementById('audienceLastCount');
    const audienceProgressFill = document.getElementById('audienceProgressFill');
    const progressPercentage = document.getElementById('progressPercentage');
    const goalAchieved = document.getElementById('goalAchieved');
    const typingIndicator = document.getElementById('typingIndicator');
    
    // Initialize based on view mode
    function initialize() {
      if (isAudienceView) {
        // Setup audience view - hide operator view, show audience view
        operatorView.classList.add('hidden');
        audienceView.classList.remove('hidden');
        
        // Load initial data
        loadDataFromStorage();
        
        // Set up interval to check for updates
        setInterval(loadDataFromStorage, 250); // Faster polling for real-time
        
        // Request fullscreen with a slight delay
        setTimeout(() => {
          try {
            if (document.documentElement.requestFullscreen) {
              document.documentElement.requestFullscreen();
            }
          } catch (e) {
            console.error("Could not enter fullscreen mode:", e);
          }
        }, 1000);
      } else {
        // Focus input on operator view
        tsukkomiInput.focus();
        
        // Load saved data
        loadDataFromStorage();
      }
    }
    
    // Auto-adjust text size based on content length
    function adjustTextSize(text) {
      if (!isAudienceView) return;
      
      // Reset font size to default first
      audienceTsukkomiText.style.fontSize = '4.5rem';
      
      // Determine text length thresholds for resizing
      const length = text.length;
      
      // Adjust font size based on text length
      if (length > 100) {
        audienceTsukkomiText.style.fontSize = '2.5rem';
      } else if (length > 50) {
        audienceTsukkomiText.style.fontSize = '3rem';
      } else if (length > 30) {
        audienceTsukkomiText.style.fontSize = '3.5rem';
      } else if (length > 20) {
        audienceTsukkomiText.style.fontSize = '4rem';
      }
    }
    
    // Save data to localStorage
    function saveDataToStorage() {
      localStorage.setItem('tsukkomiData', JSON.stringify({
        tsukkomiList,
        totalCharacters,
        timestamp: Date.now(),
        currentInputText: isRealtimeMode() ? currentInputText : '',
        isTyping: isRealtimeMode() ? isTyping : false,
        lastTsukkomiLength
      }));
    }
    
    // Load data from localStorage
    function loadDataFromStorage() {
      try {
        const storedData = localStorage.getItem('tsukkomiData');
        if (storedData) {
          const parsedData = JSON.parse(storedData);
          tsukkomiList = parsedData.tsukkomiList;
          totalCharacters = parsedData.totalCharacters;
          lastTsukkomiLength = parsedData.lastTsukkomiLength || 0;
          
          // Only update currentInputText in audience view
          if (isAudienceView) {
            currentInputText = parsedData.currentInputText || '';
            isTyping = parsedData.isTyping || false;
          }
          
          updateUI();
        }
      } catch (error) {
        console.error('Error loading data from localStorage:', error);
      }
    }
    
    // Check if real-time mode is enabled
    function isRealtimeMode() {
      return !isAudienceView && realtimeToggle.checked;
    }
    
    // Update real-time mode
    function updateRealtimeMode() {
      const isEnabled = realtimeToggle.checked;
      
      if (isEnabled) {
        inputModeStatus.textContent = '有効（入力中の文字が即座に観客画面に表示されます）';
      } else {
        inputModeStatus.textContent = '無効（Enterキーを押した時だけ表示されます）';
        // Clear current input from audience view
        currentInputText = '';
        isTyping = false;
        saveDataToStorage();
      }
    }
    
    // Reset all data
    function resetData() {
      tsukkomiList = [];
      totalCharacters = 0;
      currentInputText = '';
      isTyping = false;
      lastTsukkomiLength = 0;
      updateUI();
      saveDataToStorage();
      
      // Close modal
      resetModal.style.display = 'none';
    }
    
    // Update UI based on current state
    function updateUI() {
      const progressPercentageValue = Math.min((totalCharacters / GOAL) * 100, 100);
      
      if (isAudienceView) {
        // Update audience view
        const latestItem = tsukkomiList.length > 0 ? tsukkomiList[tsukkomiList.length - 1].text : '';
        
        // Handle real-time input
        if (currentInputText && currentInputText.trim() !== '') {
          audienceTsukkomiText.textContent = currentInputText;
          audienceTsukkomiText.classList.add('audience-typing');
          typingIndicator.classList.remove('hidden');
          adjustTextSize(currentInputText);
        } else {
          audienceTsukkomiText.textContent = latestItem || '';
          audienceTsukkomiText.classList.remove('audience-typing');
          typingIndicator.classList.add('hidden');
          
          // Adjust text size based on latest tsukkomi if available
          if (latestItem) {
            adjustTextSize(latestItem);
          }
        }
        
        audienceCountText.textContent = `${totalCharacters} / ${GOAL}`;
        audienceLastCount.textContent = `${lastTsukkomiLength}`;
        audienceProgressFill.style.width = `${progressPercentageValue}%`;
        progressPercentage.textContent = `${Math.floor(progressPercentageValue)}%`;
        
        // Show/hide goal achieved message
        if (totalCharacters >= GOAL) {
          goalAchieved.classList.remove('hidden');
        } else {
          goalAchieved.classList.add('hidden');
        }
      } else {
        // Update operator view
        countText.textContent = `${totalCharacters} / ${GOAL} 文字`;
        progressFill.style.width = `${progressPercentageValue}%`;
        
        const latestItem = tsukkomiList.length > 0 ? tsukkomiList[tsukkomiList.length - 1].text : '';
        latestTsukkomi.textContent = latestItem || 'まだツッコミはありません';
        
        // Update history items
        if (tsukkomiList.length > 0) {
          historyItems.innerHTML = '';
          const reversedList = [...tsukkomiList].reverse();
          
          reversedList.forEach(item => {
            const historyItem = document.createElement('div');
            historyItem.className = 'history-item';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'history-text';
            textSpan.textContent = item.text;
            
            const countSpan = document.createElement('span');
            countSpan.className = 'history-count';
            countSpan.textContent = `(${item.charCount}文字)`;
            
            historyItem.appendChild(textSpan);
            historyItem.appendChild(countSpan);
            historyItems.appendChild(historyItem);
          });
        } else {
          historyItems.innerHTML = '<div class="text-center p-4 text-gray-500">まだツッコミはありません</div>';
        }
      }
    }
    
    // Add a new tsukkomi
    function addTsukkomi() {
      const text = tsukkomiInput.value.trim();
      if (text === '') return;
      
      const newTsukkomi = {
        id: Date.now(),
        text: text,
        charCount: text.length
      };
      
      tsukkomiList.push(newTsukkomi);
      lastTsukkomiLength = text.length; // 直近のツッコミの文字数を保存
      totalCharacters += text.length;
      
      // Clear input and real-time preview
      tsukkomiInput.value = '';
      currentInputText = '';
      isTyping = false;
      
      // Focus input again
      tsukkomiInput.focus();
      
      // Update UI and save data
      updateUI();
      saveDataToStorage();
    }
    
    // Update the current input text for real-time preview
    function updateInputText() {
      if (!isRealtimeMode()) return;
      
      currentInputText = tsukkomiInput.value;
      isTyping = currentInputText.trim() !== '';
      
      // Update UI and save data
      saveDataToStorage();
      
      // Set typing indicator timer
      if (typingTimer) clearTimeout(typingTimer);
      
      if (isTyping) {
        typingTimer = setTimeout(() => {
          isTyping = false;
          saveDataToStorage();
        }, 3000); // Hide typing indicator after 3 seconds of inactivity
      }
    }
    
    // Open audience view in new window
    function openAudienceWindow() {
      // First save current data to localStorage
      saveDataToStorage();
      
      // Open new window with audience mode using hash instead of query parameter
      const newWindow = window.open(`${window.location.pathname}#audience`, 'TsukkomiAudience', 
        'width=1920,height=1080,menubar=no,toolbar=no,location=no');
      
      audienceWindow = newWindow;
      
      // Update window status
      windowStatus.textContent = '観客表示ウィンドウ: 開いています';
      
      // Check if window is closed periodically
      const checkWindow = setInterval(() => {
        if (audienceWindow && audienceWindow.closed) {
          windowStatus.textContent = '観客表示ウィンドウ: 未表示';
          audienceWindow = null;
          clearInterval(checkWindow);
        }
      }, 1000);
    }
    
    // Show reset confirmation modal
    function showResetModal() {
      resetModal.style.display = 'flex';
    }
    
    // Hide reset confirmation modal
    function hideResetModal() {
      resetModal.style.display = 'none';
    }
    
    // Event listeners
    if (!isAudienceView) {
      openAudienceBtn.addEventListener('click', openAudienceWindow);
      resetBtn.addEventListener('click', showResetModal);
      cancelResetBtn.addEventListener('click', hideResetModal);
      confirmResetBtn.addEventListener('click', resetData);
      addButton.addEventListener('click', addTsukkomi);
      
      tsukkomiInput.addEventListener('input', updateInputText);
      tsukkomiInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter' && !e.shiftKey) {
          e.preventDefault();
          addTsukkomi();
        }
      });
      
      realtimeToggle.addEventListener('change', () => {
        updateRealtimeMode();
        
        // Clear current input from audience view if disabled
        if (!realtimeToggle.checked) {
          currentInputText = '';
          isTyping = false;
          saveDataToStorage();
        } else {
          // Update current input if enabled
          updateInputText();
        }
      });
      
      // Close modal when clicking outside
      window.addEventListener('click', (e) => {
        if (e.target === resetModal) {
          hideResetModal();
        }
      });
    }
    
    // Initialize the app
    initialize();
  </script>
</body>
</html>
