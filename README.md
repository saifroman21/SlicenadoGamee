

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width,initial-scale=1.0"/>
  <title>Slicenado</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Knewave&family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    :root{
        --shelf-light: #4a3422;
        --shelf-dark: #3a281a;
        --text-light: #f0f0f0;
        --outline-color: #ffffff;
        --button-hover-bg: rgba(255, 255, 255, 0.15);
        --card-bg: rgba(0, 0, 0, 0.4);
        --gold-color: #ffd700;
        --vignette: rgba(0,0,0,0.7);
        --knife-size:52px;
    }
    *{margin:0;padding:0;box-sizing:border-box;user-select:none;-webkit-tap-highlight-color:transparent;}
    html,body{
        width:100%;
        height:100%;
        font-family: 'Poppins', sans-serif;
        color: var(--text-light);
        display:flex;
        justify-content:center;
        align-items:center;
        background-image:
            radial-gradient(circle at center, transparent 0%, var(--vignette) 100%),
            repeating-linear-gradient(to bottom, var(--shelf-light) 0 140px, var(--shelf-dark) 140px 155px);
        overflow:hidden;
        background-size:cover;
        background-position:center;
        transition:background-image .3s;
    }
    #wrap{position:relative;width:100%;height:100%;transition:transform .15s;}
    canvas{display:block;width:100%;height:100%;touch-action:none;}
    .overlay{position:absolute;inset:0;display:flex;flex-direction:column;justify-content:center;align-items:center;background:rgba(0,0,0,0.55);backdrop-filter:blur(5px);gap:1.2rem;z-index:10;padding: 1rem;}
    .overlay.hidden{display:none;}
    h1{font-size:clamp(2.5rem,8vw,4rem);font-weight: 700;letter-spacing:2px;text-shadow:0 0 12px #000; margin:0;}
    .btn{padding:.8rem 1.5rem;font-size:1.1rem;font-weight: 600;border:2px solid var(--outline-color);background:transparent;color: var(--text-light);border-radius:50px;cursor:pointer;transition: background-color 0.2s ease, transform .2s;text-align: center;width: 80%;max-width: 300px; margin: 0;}
    .btn:hover:not(:disabled){ background-color: var(--button-hover-bg); }
    .btn:active:not(:disabled){ transform:scale(.95); }
    .btn:disabled{ cursor:not-allowed; opacity: 0.6; }
    #start .btn { margin-bottom: 0.5rem; }
    #start .btn:last-child { margin-top: 1rem; }
    #start h1 { margin-bottom: 1rem; }
    #start #loading-status { font-size: 1.2rem; color: var(--gold-color); }
    #hud{position:absolute;top:1rem;left:1rem;display:flex;flex-direction:column;gap:.25rem;font-weight:600;font-size:1.1rem;text-shadow: 1px 1px 3px #000; z-index:5;}
    #shop, #achievements-screen { overflow-y: auto; justify-content: flex-start; gap: 1rem; }
    #shop h1, #achievements-screen h1 { margin-bottom: 0; font-size: 2.5rem; }
    .shop-header {width: 100%; max-width: 500px; display: flex; align-items: center;position: relative; justify-content: center; margin-top: 1rem;}
    .back-btn {position: absolute !important; left: 0;padding: 0.5rem 1.2rem !important; font-size: 1rem !important;width: auto !important; max-width: none !important;}
    #shop .coin-display { font-size: 1.75rem; font-weight: 700; color: var(--gold-color); margin-bottom: 1rem; }
    .shop-section { width: 90%; max-width: 500px; margin-bottom: 2rem; }
    .shop-section h2 { font-size: 1.5rem; margin-bottom: 1rem; border-bottom: 2px solid #fff5; padding-bottom: .5rem; }
    .item-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(100px, 1fr)); gap: 1rem; }
    .item-card {background: var(--card-bg); border-radius: 12px; padding: 1rem;display: flex; flex-direction: column; align-items: center;justify-content: space-between; gap: .75rem; text-align: center;}
    .item-card .item-preview { font-size: 2.5rem; line-height: 1; height: 60px; display:flex; align-items:center; justify-content:center;}
    .item-card .effect-preview { width: 100%; height: 50px; border-radius: 8px; border: 2px solid #fff5; }
    .item-card .item-name { font-size: 0.9rem; font-weight: 600; }
    .item-card .btn {font-size: 0.9rem; padding: .4rem 1rem; width: 100%;border-radius: 8px; max-width: none;}
    .item-card .btn.selected {background-color: var(--text-light); color: #000;border-color: var(--text-light);}
    .item-card .btn.price {background-color: var(--gold-color); color: #000;border: none; font-weight: 700;}
    .item-card .btn.price:disabled {background-color: #a18a09;cursor: not-allowed;opacity: 0.7;}
    #achievements-screen h1 { margin-top: 3.5rem; text-align: center; }
    #achievements-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(120px, 1fr)); gap: 1rem; width: 100%; padding: 1rem; max-width: 500px;}
    .achievement-card { opacity: 0.5; background: var(--card-bg); border: 2px solid #555; text-align: center; border-radius: 10px; padding: 1rem; }
    .achievement-card.unlocked { opacity: 1; background: rgba(255,193,7,0.2); border-color: #ffc107; }
    .achievement-card .icon { font-size: 3rem; }
    .achievement-card .name { font-weight: bold; font-size: 1rem; }
    .achievement-card .desc { font-size: 0.8rem; opacity: 0.8; }
    #knife{position:absolute;width:var(--knife-size);height:var(--knife-size);pointer-events:none;z-index:6;font-size:46px;transform:translate(-50%,-50%) rotate(0deg);transition:transform .05s, filter .1s;display:none;filter:drop-shadow(0 0 4px #fff);}
    @keyframes shake-anim{0%{transform:translateX(0)}25%{transform:translateX(-8px)}50%{transform:translateX(8px)}75%{transform:translateX(-8px)}100%{transform:translateX(0)}}
    .shake{animation:shake-anim .2s ease-in-out;}
    .juice{position:absolute;width:12px;height:12px;border-radius:50%;pointer-events:none;animation:splatter .4s ease-out forwards;z-index:3;}
    @keyframes splatter{0%{transform:translate(0,0) scale(1);opacity:1}100%{transform:translate(var(--dx),var(--dy)) scale(.3);opacity:0}}
    .knife-glow{filter:drop-shadow(0 0 8px #fff) drop-shadow(0 0 16px #00d9ff);}
    .touch-feedback{position:absolute;width:40px;height:40px;border-radius:50%;background:rgba(255,255,255,0.5);transform:translate(-50%,-50%) scale(0);animation:fade-out .3s ease-out;}
    .combo{position:absolute;top:40%;left:50%;transform:translateX(-50%);font-size:36px;font-weight:bold;color:#ffc107;text-shadow:2px 2px 4px #000;animation:fade-out .8s ease-in-out;z-index:20;}
    @keyframes fade-out{0%{transform:translate(-50%,-50%) scale(1);opacity:1}100%{transform:translate(-50%,-150%) scale(1.2);opacity:0}}
    .screen-flash { position: absolute; inset: 0; background: #fff; opacity: 0; animation: screen-flash-anim 0.2s ease-out; z-index: 2; pointer-events: none; }
    @keyframes screen-flash-anim { from { opacity: 0.3; } to { opacity: 0; } }
    .achievement-toast { position: absolute; top: 20px; left: 50%; transform: translateX(-50%); background: linear-gradient(45deg, #ffc107, #ff9800); color: #000; padding: 10px 20px; border-radius: 50px; z-index: 100; font-weight: 600; box-shadow: 0 4px 15px rgba(0,0,0,0.4); display: flex; align-items: center; gap: 10px; animation: toast-anim .5s ease forwards, toast-anim .5s ease reverse 4s forwards; }
    .achievement-toast span:first-child { font-size: 24px; }
    @keyframes toast-anim { from { top: -100px; opacity: 0; } to { top: 20px; opacity: 1; } }
    #intro-overlay { background: #111; z-index: 999; cursor: pointer; }
    #intro-title { font-family: 'Knewave', cursive; font-size: clamp(3rem, 15vw, 7rem); color: #fff; text-shadow: 0 0 5px #ff8c00, 0 0 10px #ff8c00, 0 0 20px #ff8c00, 0 0 40px #ffc107; position: relative; opacity: 0; transform: scale(0.8); animation: title-appear 1s ease-out forwards; }
    @keyframes title-appear { to { opacity: 1; transform: scale(1); } }
    #tap-prompt { font-size: 1.5rem; font-weight: 600; margin-top: 3rem; color: var(--gold-color); opacity: 0; animation: blink-anim 1.5s infinite; animation-delay: 1.8s; }
    @keyframes blink-anim { 0%, 100% { opacity: 1; } 50% { opacity: 0.3; } }
    .critical-text, .slice-feedback-text {position:absolute;top:50%;left:50%;transform:translateX(-50%);font-family:'Knewave',cursive;font-size:3rem;color:#fff;text-shadow:0 0 5px #ffc107,0 0 10px #ffc107,0 0 20px #ffc107;animation:fade-out .7s ease-in-out;z-index:21;}
    .slice-feedback-text { font-size: 2.8rem; color: #fff; top: 35%; }
    .smoke-effect{position:absolute;width:150px;height:150px;background:radial-gradient(circle,rgba(40,40,40,0.8) 0%,rgba(40,40,40,0) 70%);border-radius:50%;transform:translate(-50%,-50%) scale(0);animation:smoke-anim .5s ease-out;z-index:4;}
    @keyframes smoke-anim{0%{transform:translate(-50%,-50%) scale(0);opacity:1;}100%{transform:translate(-50%,-50%) scale(1.5);opacity:0;}}
    #rating-stars span { transition: transform 0.1s, color 0.2s; display: inline-block; }
    #rating-stars span:hover { transform: scale(1.2); }
    #rating-stars span.selected { color: var(--gold-color); }
    #daily-reward-overlay {z-index: 101;}
    #reward-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; width: 95%; max-width: 400px; }
    .reward-card { background: var(--card-bg); border-radius: 8px; padding: 10px; text-align: center; border: 2px solid #555; display: flex; flex-direction: column; justify-content: center; align-items: center; aspect-ratio: 1/1; }
    .reward-card .day { font-size: 0.9rem; font-weight: 600; opacity: 0.8; }
    .reward-card .reward { font-size: 1.5rem; font-weight: 700; margin: 5px 0; }
    .reward-card.claimed { background: #0005; border-color: #777; }
    .reward-card.today { border-color: var(--gold-color); box-shadow: 0 0 15px var(--gold-color); transform: scale(1.05); }
    .reward-card.locked { opacity: 0.6; }
    #reward-grid .reward-card:nth-child(7) { grid-column: 2 / span 2; }
    #level-container { margin-top: 5px; border-top: 1px solid #fff5; padding-top: 5px; }
    #xp-bar-container { width: 120px; height: 10px; background-color: #0005; border-radius: 5px; border: 1px solid #fff5; overflow: hidden; margin-top: 2px; }
    #xp-bar-fill { width: 0%; height: 100%; background-color: var(--gold-color); border-radius: 5px; transition: width 0.5s ease; }
    #level-up-toast { position: absolute; inset: 0; z-index: 102; background: rgba(0,0,0,0.7); backdrop-filter: blur(8px); display: flex; flex-direction: column; justify-content: center; align-items: center; color: var(--gold-color); text-shadow: 0 0 10px var(--gold-color); opacity: 0; pointer-events: none; transition: opacity 0.5s ease; }
    #level-up-toast.show { opacity: 1; }
    #level-up-toast h1 { font-size: 5rem; animation: level-up-anim 1s ease-out forwards; }
    #level-up-toast span { font-size: 1.5rem; font-weight: 600; opacity: 0; animation: fade-in-up 1s ease-out 0.5s forwards; }
    @keyframes level-up-anim { 0% { transform: scale(0.5); opacity: 0; } 60% { transform: scale(1.2); opacity: 1; } 100% { transform: scale(1); opacity: 1; } }
    @keyframes fade-in-up { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
  </style>
</head>
<body>

  <div id="intro-overlay" class="overlay">
    <div id="intro-title">Slicenado</div>
    <div id="tap-prompt" class="hidden">Tap to Start</div>
  </div>

  <div id="daily-reward-overlay" class="overlay hidden">
      <h1>Daily Reward</h1>
      <div id="reward-grid"></div>
      <button id="claimRewardBtn" class="btn">Claim Reward</button>
  </div>

  <div id="wrap">
    <canvas id="canvas"></canvas>
    <div id="hud">
        <button id="pauseBtn" class="hud-btn">⚙️</button>
      <span id="scoreLbl">Score: 0</span><span id="hiLbl">High Score: 0</span><span id="livesLbl"></span><span id="timerLbl"></span>
      <div id="level-container">
          <span id="playerLevelLbl">Level: 1</span>
          <div id="xp-bar-container">
              <div id="xp-bar-fill"></div>
          </div>
      </div>
    </div>
    <div id="start" class="overlay">
      <h1>Slicenado</h1>
      <div id="loading-status">Connecting...</div>
      <button id="playClassic" class="btn" disabled>Classic</button>
      <button id="playTimeAttack" class="btn" disabled>Time Attack</button>
      <button id="achievementsBtn" class="btn" disabled>🏆 Achievements</button>
      <button id="shopBtn" class="btn" disabled>🛒 Shop</button>
    </div>
    
    <div id="over" class="overlay hidden">
      <h1>Game Over</h1>
      <p id="gameOverMessage" style="font-size: 1.1rem; text-align: center; max-width: 90%; color: #fff;"></p>
      <span id="final" style="font-size:1.35rem"></span>
      <span id="coinsEarned" style="font-size:1.2rem; color:#ffd700;"></span>
      <button id="rewardBtn" class="btn">Watch Ad for +50 Coins 💰</button>
      <button id="again" class="btn">Restart</button>
    </div>

    <div id="shop" class="overlay hidden">
      <div class="shop-header">
        <button id="shop-back-btn" class="btn back-btn">← Back</button>
        <h1>Shop</h1>
      </div>
      <div class="coin-display">💰 <span id="totalCoins">0</span></div>
      <div class="shop-section"><h2>Knives</h2><div id="knife-grid" class="item-grid"></div></div>
      <div class="shop-section"><h2>Slice Effects</h2><div id="effect-grid" class="item-grid"></div></div>
    </div>
    <div id="achievements-screen" class="overlay hidden">
       <div class="shop-header" style="margin-top:0;">
          <button id="achievements-back-btn" class="btn back-btn">← Back</button>
          <h1>Achievements</h1>
       </div>
      <div id="achievements-grid"></div>
    </div>
    
    <div id="rating-overlay" class="overlay hidden">
        <h1>Rate Our Game</h1>
        <p>Your feedback helps us to improve the game.</p>
        <div id="rating-stars" style="font-size: 3rem; cursor: pointer; margin: 1rem 0;">
            <span>☆</span><span>☆</span><span>☆</span><span>☆</span><span>☆</span>
        </div>
        <button id="submit-rating-btn" class="btn" disabled>Submit Rating</button>
        <button id="close-rating-btn" class="btn" style="background: transparent; border: none; margin-top: 0.5rem;">Maybe Later</button>
    </div>

    <div id="rate-on-store-overlay" class="overlay hidden">
        <h1>Thank You!</h1>
        <p>Your rating encourages us. Would you like to rate us on the Oppo App Market as well?</p>
        <button id="rate-on-store-btn" class="btn">Yes, Rate Now!</button>
        <button id="close-store-btn" class="btn" style="background: transparent; border: none; margin-top: 0.5rem;">No, Thanks</button>
    </div>

  </div>
  <div id="knife">🔪</div>

  <div id="level-up-toast" class="hidden">
    <h1>LEVEL UP!</h1>
    <span id="levelUpRewardLbl"></span>
  </div>

  <audio id="sliceSfx" src="https://raw.githubusercontent.com/saifroman21/Slice-sound/main/50471708_fruit-slice_by_tibasfx_preview_182329972.mp3" preload="auto"></audio>
  <audio id="bombSfx" src="https://raw.githubusercontent.com/saifroman21/Bomb-Explosion/main/animated-cartoon-explosion-impact-352744.mp3" preload="auto"></audio>
  <audio id="bgm" src="https://assets.codepen.io/210284/bgm.mp3" preload="auto" loop></audio>
  <audio id="clickSfx" src="https://assets.codepen.io/210284/click.mp3" preload="auto"></audio>
  <audio id="openMenuSfx" src="https://assets.codepen.io/210284/whoosh.mp3" preload="auto"></audio>
  <audio id="buySfx" src="https://assets.codepen.io/210284/coin.mp3" preload="auto"></audio>
  <audio id="errorSfx" src="https://assets.codepen.io/210284/error.mp3" preload="auto"></audio>

  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    (()=>{
      const oppoMarketLink = "market://details?id=slicenado.max";
      
      const firebaseConfig = {
        apiKey: "AIzaSyDaSWYhgfgjaL6bCs3gc51mWPZ9dtbpn98",
        authDomain: "slicenado.firebaseapp.com",
        databaseURL: "https://slicenado-default-rtdb.firebaseio.com",
        projectId: "slicenado",
        storageBucket: "slicenado.firebasestorage.app",
        messagingSenderId: "649131427602",
        appId: "1:649131427602:web:d5e7c0588afe9cfbeb55cb",
        measurementId: "G-76Y3RKNPC0"
      };

      firebase.initializeApp(firebaseConfig);
      const auth = firebase.auth();
      const db = firebase.database();
      let user_uid = null;

      const $=id=>document.getElementById(id);
      const introOverlay = $('intro-overlay'); // ERROR WALA VARIABLE YAHAN ADD KIYA GAYA
      const canvas=$("canvas"),ctx=canvas.getContext("2d");
      const wrap=$("wrap"),start=$("start"),over=$("over"),playClassic=$("playClassic"),playTimeAttack=$("playTimeAttack"),again=$("again"),knifeEl=$("knife"),scoreLbl=$("scoreLbl"),hiLbl=$("hiLbl"),finalLbl=$("final"),livesLbl=$("livesLbl"),timerLbl=$("timerLbl"), loadingStatus=$("loading-status"), rewardBtn=$("rewardBtn");
      const shopBtn=$("shopBtn"), shop=$("shop"), shopBackBtn=$("shop-back-btn"), totalCoinsLbl=$("totalCoins"), knifeGrid=$("knife-grid"), coinsEarnedLbl=$("coinsEarned"), effectGrid=$("effect-grid");
      const achievementsBtn = $("achievementsBtn"), achievementsScreen = $("achievements-screen"), achievementsBackBtn = $("achievements-back-btn"), achievementsGrid = $("achievements-grid");
      const allButtons = [playClassic, playTimeAttack, achievementsBtn, shopBtn];
      const gameOverMessage = $('gameOverMessage'); // YAHAN PASTE KAREIN
      const ratingOverlay = $('rating-overlay'), ratingStars = $('rating-stars'), submitRatingBtn = $('submit-rating-btn'), closeRatingBtn = $('close-rating-btn');
      const rateOnStoreOverlay = $('rate-on-store-overlay'), rateOnStoreBtn = $('rate-on-store-btn'), closeStoreBtn = $('close-store-btn');
      const dailyRewardOverlay = $('daily-reward-overlay'), rewardGrid = $('reward-grid'), claimRewardBtn = $('claimRewardBtn');
      const playerLevelLbl = $('playerLevelLbl'), xpBarFill = $('xp-bar-fill'), levelUpToast = $('level-up-toast'), levelUpRewardLbl = $('levelUpRewardLbl');
      const pauseBtn = $('pauseBtn'), pauseMenu = $('pause-menu'), resumeBtn = $('resumeBtn'), restartBtnPause = $('restartBtnPause'), mainMenuBtn = $('mainMenuBtn');

      const allSounds = Array.from(document.querySelectorAll("audio"));
      const [sliceSfx, bombSfx, bgm, clickSfx, openMenuSfx, buySfx, errorSfx] = allSounds;
      let isAudioUnlocked = false;
      const unlockAudio = () => { if (isAudioUnlocked) return; allSounds.forEach(sound => { sound.play().then(() => sound.pause()).catch(() => {}); }); isAudioUnlocked = true; };
      document.body.addEventListener('pointerdown', unlockAudio, { once: true });

      let playerData = {};
      const achievementsList = [ { id: 'slice_10', name: "Novice Slicer", desc: "Slice 10 fruits", stat: 'totalFruitsSliced', target: 10, icon: '🗡️' }, { id: 'slice_100', name: "Fruit Assassin", desc: "Slice 100 fruits", stat: 'totalFruitsSliced', target: 100, icon: '⚔️' }, { id: 'slice_500', name: "Sensei of Slicing", desc: "Slice 500 fruits", stat: 'totalFruitsSliced', target: 500, icon: '🥷' }, { id: 'combo_5', name: "Combo Kid", desc: "Get a 5x combo", stat: 'highestCombo', target: 5, icon: '💥' }, { id: 'combo_10', name: "Super Combo!", desc: "Get a 10x combo", stat: 'highestCombo', target: 10, icon: '☄️' }, { id: 'coins_500', name: "Getting Rich", desc: "Earn 500 total coins", stat: 'totalCoins', target: 500, icon: '💰' }, { id: 'no_bombs_classic', name: "Bomb Dodger", desc: "Win a Classic game with 2 lives", stat: 'flawlessVictory', target: 1, icon: '🛡️' }, ];
      const defaultData = { hiClassic: 0, hiTimeAttack: 0, totalCoins: 0, lastLoginDate: '', loginStreak: 0, playerLevel: 1, playerXP: 0, equippedKnife: '🔪', equippedSliceEffect: 'default_trail', knives: [ { id: '🔪', unlocked: true, price: 0 }, { id: '🗡️', unlocked: false, price: 400 }, { id: '⚔️', unlocked: false, price: 850 }, { id: '⚡️', unlocked: false, price: 1500 }, ], sliceEffects: [ { id: 'default_trail', name: 'Classic Blue', unlocked: true, price: 0, color1: 'rgba(173, 216, 230, 0.5)', color2: '#ffffff' }, { id: 'fire_trail', name: 'Fire Trail 🔥', unlocked: false, price: 500, color1: 'rgba(255, 165, 0, 0.6)', color2: '#ff4500' }, { id: 'sakura_trail', name: 'Sakura 🌸', unlocked: false, price: 750, color1: 'rgba(255, 182, 193, 0.6)', color2: '#ffc0cb' } ], stats: { totalFruitsSliced: 0, highestCombo: 0, flawlessVictory: 0 }, achievements: {}, hasRated: false };
      achievementsList.forEach(a => defaultData.achievements[a.id] = false);
      const sliceFeedbackWords = ['Nice', 'Good', 'Fabulous', 'Awesome', 'Killer', 'Master', 'Insane', 'Legend', 'Unstoppable'];
      
      const dailyRewards = [10, 20, 50, 75, 100, 150, 500];

      function getXPForNextLevel(level) {
    // 1.2 ko 1.3 se badal diya gaya hai
    return Math.floor(100 * Math.pow(1.3, level - 1));
}
      function addXP(amount) {
          playerData.playerXP += amount;
          let xpNeeded = getXPForNextLevel(playerData.playerLevel);
          while (playerData.playerXP >= xpNeeded) {
              playerData.playerXP -= xpNeeded;
              playerData.playerLevel++;
              let rewardAmount = 10
             * playerData.playerLevel;
let rewardText = `+${rewardAmount} Coins! 💰`;
if (playerData.playerLevel % 5 === 0) {
    rewardText = "You got a Treasure Chest! 🎁";
    rewardAmount += 250; // Treasure chest ka bonus bhi kam kar diya
}
playerData.totalCoins += rewardAmount;

              showLevelUpAnimation(playerData.playerLevel, rewardText);
              xpNeeded = getXPForNextLevel(playerData.playerLevel);
          }
          updateLevelDisplay();
          savePlayerData();
      }
      function updateLevelDisplay() {
          const xpNeeded = getXPForNextLevel(playerData.playerLevel);
          const xpPercentage = (playerData.playerXP / xpNeeded) * 100;
          playerLevelLbl.textContent = `Level: ${playerData.playerLevel}`;
          xpBarFill.style.width = `${xpPercentage}%`;
          totalCoinsLbl.textContent = playerData.totalCoins;
      }
      function showLevelUpAnimation(newLevel, rewardText) {
          playSound(buySfx);
          levelUpToast.querySelector('h1').textContent = `LEVEL ${newLevel}!`;
          levelUpRewardLbl.textContent = rewardText;
          levelUpToast.classList.remove('hidden');
          levelUpToast.classList.add('show');
          setTimeout(() => {
              levelUpToast.classList.remove('show');
              setTimeout(() => levelUpToast.classList.add('hidden'), 500);
          }, 2500);
      }

      function loadPlayerData(uid) {
        db.ref('users/' + uid).once('value').then(snapshot => {
            let loadedData = snapshot.val();
            if (loadedData) {
                playerData = { ...JSON.parse(JSON.stringify(defaultData)), ...loadedData };
                playerData.stats = { ...defaultData.stats, ...(loadedData.stats || {}) };
                playerData.achievements = { ...defaultData.achievements, ...(loadedData.achievements || {}) };
                playerData.knives = loadedData.knives || defaultData.knives;
                playerData.sliceEffects = loadedData.sliceEffects || defaultData.sliceEffects;
            } else {
                playerData = JSON.parse(JSON.stringify(defaultData));
                savePlayerData();
            }
            console.log("Player data loaded from Firebase.");
            finishLoading();
        }).catch(error => { console.error("Firebase read failed: " + error.message); loadingStatus.textContent = "Error loading data."; });
      }
      function savePlayerData() { if (!user_uid) return; db.ref('users/' + user_uid).set(playerData).catch(error => { console.error("Firebase write failed: " + error.message); }); }
      
      function finishLoading() {
        totalCoinsLbl.textContent = playerData.totalCoins;
        applyEquippedItems();
        loadingStatus.style.display = 'none';
        allButtons.forEach(btn => btn.disabled = false);
        checkDailyReward();
        updateLevelDisplay();
      }

      function applyEquippedItems() { knifeEl.textContent = playerData.equippedKnife; }
      function runIntro() {
        const tapPrompt = $('tap-prompt');
        start.classList.add('hidden');
        setTimeout(() => { tapPrompt.classList.remove('hidden'); }, 1800);
        const enterGame = () => {
            playSound(clickSfx);
            introOverlay.style.opacity = 0;
            introOverlay.style.pointerEvents = 'none';
            start.classList.remove('hidden');
            introOverlay.removeEventListener('click', enterGame);
        };
        introOverlay.addEventListener('click', enterGame);
      }
      
      auth.onAuthStateChanged(user => {
        if (user) { user_uid = user.uid; loadPlayerData(user.uid); } 
        else { auth.signInAnonymously().catch(error => { console.error("Anonymous sign-in failed:", error); }); }
      });
      
      function checkDailyReward() {
          const today = new Date().toISOString().slice(0, 10);
          if (playerData.lastLoginDate !== today) {
              let currentStreak = playerData.loginStreak || 0;
              if (currentStreak >= 7) { currentStreak = 0; }
              playerData.loginStreak = currentStreak;
              showDailyRewardPopup(currentStreak);
          } else {
              start.classList.remove('hidden');
          }
      }

      function showDailyRewardPopup(streak) {
          rewardGrid.innerHTML = '';
          dailyRewards.forEach((reward, index) => {
              const card = document.createElement('div');
              card.className = 'reward-card';
              if (index < streak) {
                  card.classList.add('claimed');
                  card.innerHTML = `<div class="day">Day ${index + 1}</div><div class="reward">✅</div>`;
              } else if (index === streak) {
                  card.classList.add('today');
                  card.innerHTML = `<div class="day">Day ${index + 1}</div><div class="reward">💰${reward}</div>`;
              } else {
                  card.classList.add('locked');
                  card.innerHTML = `<div class="day">Day ${index + 1}</div><div class="reward">💰${reward}</div>`;
              }
              rewardGrid.appendChild(card);
          });
          dailyRewardOverlay.classList.remove('hidden');
          start.classList.add('hidden');
      }

      claimRewardBtn.addEventListener('click', () => {
          const today = new Date().toISOString().slice(0, 10);
          const currentReward = dailyRewards[playerData.loginStreak];
          playerData.totalCoins += currentReward;
          playerData.lastLoginDate = today;
          playerData.loginStreak += 1;
          savePlayerData();
          playSound(buySfx);
          totalCoinsLbl.textContent = playerData.totalCoins;
          dailyRewardOverlay.classList.add('hidden');
          start.classList.remove('hidden');
      });

      let W,H,dpr=window.devicePixelRatio||1;
      const resize=()=>{W=innerWidth;H=innerHeight;canvas.width=W*dpr;canvas.height=H*dpr;ctx.setTransform(dpr,0,0,dpr,0,0);};
      window.addEventListener("resize",resize);resize();
      const GRAVITY=0.28,BASE_SPAWN=1100,MIN_SPAWN=500;
      const BASE_EMOJIS = ['🍎','🍐','🍊','🍋','🍌','🍉','🍇','🍓','🍒','🍍','🥭','🥝','🥑','🥥','🥦','🥕','🌽','🥒','🥬','🍆','🥔','🧄','🧅','🌶️','🍄'];
      const TIER2_EMOJIS = ['🍑', '🍈', '🍏'];
      const TIER3_EMOJIS = ['⭐', '🫐'];
      let objs=[], slicedHalves=[], spawnTimer=0,spawnRate=BASE_SPAWN,running=false,score=0,lives=0;
      let slicePoints=[],glowTimer=null,gameMode='classic',gameTimer=0;
      let newHighScoreAchieved = false;

      function playSound(sound){if (!isAudioUnlocked) return; sound.currentTime=0; sound.play().catch(()=>{});}
      function checkAchievements() { let changed = false; achievementsList.forEach(ach => { if (!playerData.achievements[ach.id] && playerData.stats[ach.stat] >= ach.target) { playerData.achievements[ach.id] = true; showAchievementToast(ach); changed = true; } }); if(changed) savePlayerData(); }
      function showAchievementToast(achievement) { const toast = document.createElement('div'); toast.className = 'achievement-toast'; toast.innerHTML = `<span>${achievement.icon}</span> <div><b>${achievement.name}</b><br>Achievement Unlocked!</div>`; wrap.appendChild(toast); setTimeout(() => toast.remove(), 4500); }
      function halves(o) { for (const sign of [-1, 1]) { slicedHalves.push({ x: o.x, y: o.y, emoji: o.emoji, dx: o.dx / 2 + (4 * sign) * (Math.random() * 0.5 + 0.5), dy: o.dy, rot: o.rot, rotSpeed: (Math.random() - 0.5) * 0.5, lifetime: 120 }); } }
      let combo=0,comboTimer=null;
      function addCombo(){
          combo++;
          if(comboTimer) clearTimeout(comboTimer);
          comboTimer=setTimeout(()=>{
              if(combo>1){
                  showCombo(combo);
                  if(combo > playerData.stats.highestCombo) {
                      playerData.stats.highestCombo = combo;
                      checkAchievements();
                  }
              }
              combo=0; 
              comboTimer=null;
          },800); 
      }
      let down=false;canvas.addEventListener("pointerdown",e=>{if(!running) return; down=true;knifeEl.style.display="block";slicePoints=[];touchFeedback(e.clientX,e.clientY);slice(e);});canvas.addEventListener("pointermove",e=>{if(down)slice(e);});
      ["pointerup","pointercancel","pointerleave","pointerout"].forEach(ev=>canvas.addEventListener(ev,()=>{down=false;knifeEl.style.display="none";}));
      function slice(e){ const rect=canvas.getBoundingClientRect();const px=e.clientX-rect.left,py=e.clientY-rect.top;moveKnife(e.clientX,e.clientY);slicePoints.push({x:px,y:py,lifetime:12}); for(const o of objs){ if(o.dead || o.isCharred)continue; const dist = Math.hypot(o.x-px,o.y-py); if(dist<o.r+15){ o.dead=true; if(o.isBomb){ triggerBombExplosion(o.x,o.y);playSound(bombSfx);screenShake(); if(gameMode === 'classic') {lives--;updateLivesDisplay();if(lives<=0){gameOver();}} else {score = Math.max(0, score-10); scoreLbl.textContent=`Score: ${score}`;} combo=0;}else{ if (dist < 8) { showCriticalText(); playSound(buySfx); playerData.totalCoins += 10; addXP(20); } playSound(sliceSfx); score++; addXP(10); playerData.stats.totalFruitsSliced++; checkAchievements();scoreLbl.textContent=`Score: ${score}`;halves(o);juice(o.x,o.y,o.emoji); addCombo(); showSliceFeedback(combo); triggerScreenFlash(); triggerKnifeImpact(); if(gameMode==='classic')spawnRate=Math.max(MIN_SPAWN,BASE_SPAWN-score*10);}}}}
      let lastTime=performance.now();
      function loop(now){ if(!running)return; const dt=now-lastTime;lastTime=now; ctx.clearRect(0,0,canvas.width,canvas.height); if(gameMode==='timeAttack'){const newTime = gameTimer - dt / 1000; gameTimer = Math.max(0, newTime); timerLbl.textContent=`Time: ${Math.ceil(gameTimer)}`; if(gameTimer<=0){ gameOver(); }} if(slicePoints.length>1){const equippedEffect = playerData.sliceEffects.find(e => e.id === playerData.equippedSliceEffect) || playerData.sliceEffects[0];ctx.lineCap="round"; ctx.lineJoin="round"; ctx.strokeStyle = equippedEffect.color1; ctx.lineWidth=12;ctx.beginPath(); ctx.moveTo(slicePoints[0].x,slicePoints[0].y);for(let i=1;i<slicePoints.length;i++) ctx.lineTo(slicePoints[i].x,slicePoints[i].y);ctx.stroke();ctx.strokeStyle = equippedEffect.color2; ctx.lineWidth=5;ctx.beginPath(); ctx.moveTo(slicePoints[0].x,slicePoints[0].y);for(let i=1;i<slicePoints.length;i++) ctx.lineTo(slicePoints[i].x,slicePoints[i].y);ctx.stroke();} slicePoints=slicePoints.filter(p=>(p.lifetime--)>0);spawnTimer+=dt;if(spawnTimer>spawnRate){spawn();spawnTimer=0;} objs = objs.filter(o => { if (o.dead) { return false; } o.x += o.dx; o.y += o.dy; o.dy += GRAVITY; o.rot += o.dx * 0.05; return o.y <= H + o.r; }); for(const o of objs){ctx.save();ctx.translate(o.x,o.y);ctx.rotate(o.rot);ctx.font=`${o.r*1.8}px serif`;ctx.textAlign="center";ctx.textBaseline="middle";ctx.fillText(o.emoji,0,3);ctx.restore();} slicedHalves = slicedHalves.filter(h => {h.x += h.dx;h.y += h.dy;h.dy += GRAVITY;h.rot += h.rotSpeed;h.lifetime--;return h.lifetime > 0;}); for (const h of slicedHalves) {ctx.save();ctx.translate(h.x, h.y);ctx.rotate(h.rot);ctx.font = `${24 * 1.8}px serif`;ctx.textAlign = "center";ctx.textBaseline = "middle";ctx.fillText(h.emoji, 0, 0);ctx.restore();} requestAnimationFrame(loop);}
      function startGame(mode){ playSound(clickSfx);gameMode=mode;objs=[];slicedHalves=[];score=0;spawnRate=BASE_SPAWN;spawnTimer=0;combo=0;newHighScoreAchieved = false; if(gameMode==='classic'){ lives=2;updateLivesDisplay();livesLbl.style.display='block';timerLbl.style.display='none';hiLbl.textContent=`High Score: ${playerData.hiClassic}`; } else { gameTimer=60; lives=0;livesLbl.style.display='none';timerLbl.style.display='block';hiLbl.textContent=`High Score: ${playerData.hiTimeAttack}`; } scoreLbl.textContent="Score: 0";running=true;start.classList.add("hidden");over.classList.add("hidden");shop.classList.add("hidden");achievementsScreen.classList.add("hidden");ratingOverlay.classList.add("hidden");rateOnStoreOverlay.classList.add('hidden');knifeEl.style.display="none";lastTime=performance.now();bgm.currentTime=0; playSound(bgm);loop(lastTime); }
      // IS POORE FUNCTION KO NAYE CODE SE REPLACE KAREIN
function gameOver(){
    running=false;
    pauseBtn.style.display = 'none';
    rewardBtn.disabled = false;
    rewardBtn.textContent = 'Watch Ad for +50 Coins 💰';
    
    const coinsGained = Math.ceil(score / 2);
    playerData.totalCoins += coinsGained;
    coinsEarnedLbl.textContent = `+${coinsGained} Coins!`;
    
    // THEMATIC MESSAGES LOGIC (YAHAN NAYA LOGIC HAI)
    let message = "";
    if (newHighScoreAchieved) {
        message = "A NEW LEGEND IS BORN! You set a new High Score! 🔥";
    } else if (score < 10) {
        message = "A true master never gives up. Try again!";
    } else if (score > 50 && score < 100) {
        message = "Incredible! You are getting closer to mastery.";
    } else if (score >= 100) {
        message = "Amazing! You are a true Slicenado warrior!";
    } else {
        message = "Good effort. Every slice makes you stronger.";
    }
    gameOverMessage.textContent = message;

    if (gameMode === 'classic' && lives === 2) { 
        playerData.stats.flawlessVictory = 1; 
    }
    
    if(gameMode==='classic'){ 
        if(score > 0 && score > playerData.hiClassic){ 
            playerData.hiClassic = score; 
            hiLbl.textContent=`High Score: ${playerData.hiClassic}`; 
            newHighScoreAchieved = true;
        }
    } else { 
        if(score > 0 && score > playerData.hiTimeAttack){ 
            playerData.hiTimeAttack = score; 
            hiLbl.textContent=`High Score: ${playerData.hiTimeAttack}`; 
            newHighScoreAchieved = true;
        } 
    }
    
    checkAchievements();
    savePlayerData();
    finalLbl.textContent=`Your Score: ${score}`;
    over.classList.remove("hidden");
    bgm.pause();
}

      function spawn(){ let currentEmojis = [...BASE_EMOJIS];if (score >= 50) currentEmojis.push(...TIER2_EMOJIS);if (score >= 100) currentEmojis.push(...TIER3_EMOJIS);const isBomb = Math.random() < 0.12;const r=28;const x=50+Math.random()*(W-100);const y=H+r;const diff=1+score/70;const dy=-(10+Math.random()*4)*diff;const dx=(Math.random()-.5)*3*diff;const emoji=isBomb?'💣':currentEmojis[Math.random()*currentEmojis.length|0];objs.push({x,y,dx,dy,r,isBomb,emoji,rot:0,dead:false, isCharred: false}); }
      function juice(x,y,e){const base={'🍎':'#ff3030','🍐':'#b2ff59','🍊':'#ffa726','🍋':'#ffee58','🍌':'#ffe082','🍉':'#f44336','🍇':'#ba68c8','🍓':'#ff1744','🍒':'#d50000','🍍':'#ffca28','🥭':'#ff9100','🥝':'#8bc34a','🥑':'#66bb6a','🥥':'#8d6e63','🥦':'#76ff03','🥕':'#ff8f00','🌽':'#ffd54f','🥒':'#aed581','🥬':'#8bc3a','🍆':'#8e24aa','🥔':'#d7ccc8','🧄':'#f3e5ab','🧅':'#ff7043','🌶️':'#ef5350','🍄':'#d32f2f', '🍑':'#ffb266', '🍈':'#98FB98', '🍏':'#8db600', '⭐':'#FFD700', '🫐':'#464196'}[e]||'#ff5252';for(let i=0;i<10;i++){const d=document.createElement("div");d.className="juice";d.style.background=base;const ang=Math.random()*Math.PI*2,dist=60+Math.random()*40;d.style.setProperty('--dx',Math.cos(ang)*dist+'px');d.style.setProperty('--dy',Math.sin(ang)*dist+'px');d.style.left=x+"px";d.style.top=y+"px";wrap.append(d);d.addEventListener("animationend",()=>d.remove(),{once:true});}}
      function showCombo(n){const d=document.createElement("div");d.className="combo";d.textContent=`+${n} COMBO`;wrap.append(d);d.addEventListener("animationend",()=>d.remove(),{once:true});const bonusPoints=n*2;score+=bonusPoints;addXP(n * 5);scoreLbl.textContent=`Score: ${score}`;const bonusText=document.createElement("div");bonusText.className="combo";bonusText.textContent=`+${bonusPoints} Bonus!`;bonusText.style.top="46%";bonusText.style.fontSize="28px";bonusText.style.color="#ffd700";wrap.append(bonusText);bonusText.addEventListener("animationend",()=>bonusText.remove(),{once:true});}
      let lastX=0,lastY=0;function moveKnife(x,y){const ang=Math.atan2(y-lastY,x-lastX)*180/Math.PI;const dist=Math.hypot(x-lastX,y-lastY);knifeEl.style.transform = `translate(-50%, -50%) rotate(${ang+90}deg)`; knifeEl.style.left=x+"px";knifeEl.style.top=y+"px";if(dist>20){knifeEl.classList.add('knife-glow');if(glowTimer)clearTimeout(glowTimer);glowTimer=setTimeout(()=>knifeEl.classList.remove('knife-glow'),150);}lastX=x;lastY=y;}
      function touchFeedback(x,y){const f=document.createElement("div");f.className="touch-feedback";f.style.left=x+"px";f.style.top=y+"px";wrap.appendChild(f);f.addEventListener("animationend",()=>f.remove(),{once:true});}
      function screenShake(){wrap.classList.add("shake");wrap.addEventListener("animationend",()=>wrap.classList.remove("shake"),{once:true});}
      function triggerScreenFlash() { const flash = document.createElement('div'); flash.className = 'screen-flash'; wrap.appendChild(flash); setTimeout(() => flash.remove(), 200); }
      function triggerKnifeImpact() { knifeEl.style.filter = 'brightness(1.8) drop-shadow(0 0 10px #fff)'; setTimeout(() => { knifeEl.style.filter = 'drop-shadow(0 0 4px #fff)'; }, 80); }
      function updateLivesDisplay(){livesLbl.textContent='Lives: '+'❤️'.repeat(lives);}
      function showCriticalText(){const d=document.createElement("div");d.className="critical-text";d.textContent="CRITICAL!";wrap.append(d);d.addEventListener("animationend",()=>d.remove(),{once:true});}
      function showSliceFeedback(comboCount) { if (comboCount < 1) return; const feedbackText = sliceFeedbackWords[Math.min(comboCount - 1, sliceFeedbackWords.length - 1)]; const d = document.createElement("div"); d.className = "slice-feedback-text"; d.textContent = feedbackText; wrap.append(d); d.addEventListener("animationend", () => d.remove(), { once: true }); }
      function triggerBombExplosion(bombX, bombY){ const smoke = document.createElement('div'); smoke.className = 'smoke-effect'; smoke.style.left = bombX + 'px'; smoke.style.top = bombY + 'px'; wrap.appendChild(smoke); smoke.addEventListener('animationend', () => smoke.remove(), {once: true}); for (const o of objs) { if (o.dead || o.isBomb) continue; const dist = Math.hypot(o.x - bombX, o.y - bombY); if (dist < 150) { o.isCharred = true; o.emoji = '⚫'; } } }
      function renderShop() { knifeGrid.innerHTML = ''; totalCoinsLbl.textContent = playerData.totalCoins; playerData.knives.forEach(k => { const card = document.createElement('div'); card.className = 'item-card'; let buttonHtml; if (k.unlocked) { if (k.id === playerData.equippedKnife) { buttonHtml = `<button class="btn selected" disabled>Selected</button>`; } else { buttonHtml = `<button class="btn" data-type="equip-knife" data-id="${k.id}">Select</button>`; } } else { buttonHtml = `<button class="btn price" data-type="buy-knife" data-id="${k.id}">${k.price} 💰</button>`; } card.innerHTML = `<div class="item-preview">${k.id}</div>${buttonHtml}`; knifeGrid.appendChild(card); }); effectGrid.innerHTML = ''; playerData.sliceEffects.forEach(s => { const card = document.createElement('div'); card.className = 'item-card'; let buttonHtml; if (s.unlocked) { if (s.id === playerData.equippedSliceEffect) { buttonHtml = `<button class="btn selected" disabled>Selected</button>`; } else { buttonHtml = `<button class="btn" data-type="equip-effect" data-id="${s.id}">Select</button>`; } } else { buttonHtml = `<button class="btn price" data-type="buy-effect" data-id="${s.id}">${s.price} 💰</button>`; } const previewStyle = `background-image: linear-gradient(45deg, ${s.color1}, ${s.color2});`; card.innerHTML = `<div class="effect-preview" style="${previewStyle}"></div><div class="item-name">${s.name}</div>${buttonHtml}`; effectGrid.appendChild(card); });}
      shop.addEventListener('click', e => { const target = e.target.closest('.btn'); if (!target || target.disabled) return; const { type, id } = target.dataset; let item; switch (type) { case 'buy-knife': item = playerData.knives.find(k => k.id === id); if (playerData.totalCoins >= item.price) { playerData.totalCoins -= item.price; item.unlocked = true; playSound(buySfx); savePlayerData(); renderShop(); } else { playSound(errorSfx); } break; case 'equip-knife': playerData.equippedKnife = id; playSound(clickSfx); savePlayerData(); applyEquippedItems(); renderShop(); break; case 'buy-effect': item = playerData.sliceEffects.find(s => s.id === id); if (playerData.totalCoins >= item.price) { playerData.totalCoins -= item.price; item.unlocked = true; playSound(buySfx); savePlayerData(); renderShop(); } else { playSound(errorSfx); } break; case 'equip-effect': playerData.equippedSliceEffect = id; playSound(clickSfx); savePlayerData(); renderShop(); break; }});
      function renderAchievements() { achievementsGrid.innerHTML = ''; achievementsList.forEach(ach => { const unlocked = playerData.achievements[ach.id]; const card = document.createElement('div'); card.className = `achievement-card ${unlocked ? 'unlocked' : ''}`; card.innerHTML = ` <div class="icon">${unlocked ? ach.icon : '❓'}</div> <div class="name">${unlocked ? ach.name : '??????'}</div> <div class="desc">${unlocked ? ach.desc : 'Keep playing to unlock!'}</div> `; achievementsGrid.appendChild(card); }); }
      window.onRewardGranted = function() { console.log("Reward granted!");const rewardAmount = 50;playerData.totalCoins += rewardAmount;savePlayerData();totalCoinsLbl.textContent = playerData.totalCoins; rewardBtn.disabled = true;rewardBtn.textContent = 'Reward Claimed!';}
      function showRewardAd() { if (typeof appcreator24 !== 'undefined' && appcreator24.showRewardAd) { console.log("Attempting to show rewarded ad...");rewardBtn.disabled = true;rewardBtn.textContent = 'Loading Ad...';appcreator24.showRewardAd();} else { console.log("Test mode: Reward granted directly.");window.onRewardGranted();} }
      function showInterstitialAd() { if (typeof appcreator24 !== 'undefined' && appcreator24.showInterstitialAd) { console.log("Attempting to show interstitial ad...");appcreator24.showInterstitialAd();} else { console.log("Test mode: Interstitial ad not shown.");} }
      
      playClassic.addEventListener("click",()=>startGame('classic'));
      playTimeAttack.addEventListener("click",()=>startGame('timeAttack'));
      again.addEventListener("click", () => { playSound(clickSfx); showInterstitialAd(); if (newHighScoreAchieved && !playerData.hasRated) { showRatingPopup(); } else { startGame(gameMode); } });
      shopBtn.addEventListener('click', () => { playSound(openMenuSfx); start.classList.add('hidden'); shop.classList.remove('hidden'); renderShop(); });
      shopBackBtn.addEventListener('click', () => { playSound(clickSfx); shop.classList.add('hidden'); start.classList.remove('hidden'); });
      achievementsBtn.addEventListener('click', () => { playSound(openMenuSfx); start.classList.add('hidden'); achievementsScreen.classList.remove('hidden'); renderAchievements(); });
      achievementsBackBtn.addEventListener('click', () => { playSound(clickSfx); achievementsScreen.classList.add('hidden'); start.classList.remove('hidden'); });
      rewardBtn.addEventListener('click', showRewardAd);
      
      let currentRating = 0;
      function showRatingPopup() { over.classList.add('hidden'); ratingOverlay.classList.remove('hidden'); playSound(openMenuSfx); }
      ratingStars.addEventListener('click', e => { if (e.target.tagName === 'SPAN') { currentRating = Array.from(ratingStars.children).indexOf(e.target) + 1; updateStars(currentRating); submitRatingBtn.disabled = false; } });
      ratingStars.addEventListener('mouseover', e => { if (e.target.tagName === 'SPAN') { const hoverRating = Array.from(ratingStars.children).indexOf(e.target) + 1; updateStars(hoverRating, true); } });
      ratingStars.addEventListener('mouseout', () => { updateStars(currentRating); });
      function updateStars(rating) { Array.from(ratingStars.children).forEach((star, index) => { if (index < rating) { star.classList.add('selected'); star.textContent = '★'; } else { star.classList.remove('selected'); star.textContent = '☆'; } }); }
      submitRatingBtn.addEventListener('click', () => { if (currentRating > 0) { playSound(buySfx); db.ref(`ratings/${user_uid}`).set({ stars: currentRating, timestamp: new Date().toISOString() }); playerData.hasRated = true; savePlayerData(); ratingOverlay.classList.add('hidden'); if (currentRating >= 4) { rateOnStoreOverlay.classList.remove('hidden'); } else { startGame(gameMode); } } });
      closeRatingBtn.addEventListener('click', () => { ratingOverlay.classList.add('hidden'); startGame(gameMode); playSound(clickSfx); });
      rateOnStoreBtn.addEventListener('click', () => { window.open(oppoMarketLink, '_blank'); rateOnStoreOverlay.classList.add('hidden'); startGame(gameMode); playSound(clickSfx); });
      closeStoreBtn.addEventListener('click', () => { rateOnStoreOverlay.classList.add('hidden'); startGame(gameMode); playSound(clickSfx); });

      runIntro();
    })();
  </script>
</body>
</html>

