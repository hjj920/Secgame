# Secgame
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>시크릿게임 팬사이트</title>
  <link href="https://fonts.googleapis.com/css2?family=Roboto&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Roboto', sans-serif;
    }

    body {
      background-color: #0e0e1f;
      color: #ffffff;
      display: flex;
      min-height: 100vh;
    }

    #sidebar {
      width: 200px;
      background: linear-gradient(180deg, #151531, #1c1c3f);
      padding: 20px;
      display: flex;
      flex-direction: column;
      gap: 20px;
      border-right: 1px solid #444;
    }

    #sidebar button {
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid #3a3aff;
      padding: 12px;
      color: #3a90ff;
      font-size: 15px;
      border-radius: 8px;
      cursor: pointer;
      transition: 0.3s;
    }

    #sidebar button:hover {
      background-color: rgba(58, 144, 255, 0.1);
      color: white;
    }

    #main {
      flex: 1;
      padding: 30px;
      background-color: #121226;
      position: relative;
    }

    h2 {
      margin: 20px 0;
      color: #58aaff;
    }

    img {
      max-width: 100%;
      border-radius: 12px;
      margin-top: 20px;
    }

    .hidden {
      display: none;
    }

    #chat-box {
      height: 300px;
      border: 1px solid #3a90ff;
      padding: 10px;
      overflow-y: auto;
      background: rgba(255, 255, 255, 0.05);
      margin-bottom: 10px;
      border-radius: 8px;
    }

    #message-input {
      width: 70%;
      padding: 10px;
      border-radius: 6px;
      border: none;
      background-color: #1f1f35;
      color: white;
    }

    #send-btn {
      padding: 10px;
      background-color: #3a90ff;
      color: white;
      border: none;
      border-radius: 6px;
      margin-left: 10px;
      cursor: pointer;
    }

    #nickname-overlay {
      position: fixed;
      top: 0; left: 0;
      width: 100vw; height: 100vh;
      background: rgba(0, 0, 0, 0.9);
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      z-index: 1000;
    }

    #nickname-input {
      padding: 10px;
      font-size: 16px;
      margin-top: 10px;
      border-radius: 6px;
      border: none;
    }

    #nickname-overlay button {
      margin-top: 10px;
      background-color: #3a90ff;
      color: white;
      padding: 10px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    }

    #game-popup {
      position: fixed;
      top: 50%; left: 50%;
      transform: translate(-50%, -50%);
      background: #1e1e40;
      border: 2px solid #3a90ff;
      padding: 20px;
      border-radius: 10px;
      display: none;
      z-index: 999;
      color: white;
    }

    #game-popup input, #game-popup button {
      margin-top: 10px;
    }

    #game-popup input {
      background: #2c2c4d;
      border: none;
      padding: 10px;
      color: white;
      border-radius: 6px;
    }

    #game-popup button {
      background-color: #3a90ff;
      color: white;
      padding: 8px 12px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    }
  </style>
</head>
<body>

  <!-- 닉네임 설정 -->
  <div id="nickname-overlay">
    <h2>닉네임을 설정해주세요</h2>
    <p>(한 번만 설정 가능하며 중복 불가)</p>
    <input id="nickname-input" placeholder="닉네임 입력" />
    <button onclick="setNickname()">설정</button>
    <p id="nickname-warning" style="color: red; margin-top: 10px;"></p>
  </div>

  <!-- 왼쪽 사이드바 -->
  <div id="sidebar">
    <button onclick="showSection('home')">🏠 홈</button>
    <button onclick="showSection('chat')">💬 팬톡방</button>
    <button onclick="showSection('channel')">📺 채널 링크</button>
    <button onclick="openGame()">🎮 미니게임</button>
    <button disabled>🔧 준비중</button>
  </div>

  <!-- 메인 콘텐츠 -->
  <div id="main">
    <div id="home-section">
      <img src="fansite_logo.png" alt="로고" />
      <h2>시크릿게임 팬사이트</h2>
      <p>게임 고수 <strong>시크릿게임</strong>의 팬이라면 여기 모여!</p>
    </div>

    <div id="chat-section" class="hidden">
      <h2>팬톡방</h2>
      <div id="chat-box"></div>
      <input id="message-input" placeholder="메시지 입력..." />
      <button id="send-btn">전송</button>
    </div>

    <div id="channel-section" class="hidden">
      <h2>시크릿게임 유튜브 채널</h2>
      <a href="https://m.youtube.com/@Secretroblox-kimsiknoldang" target="_blank" style="color:#3a90ff;">👉 바로가기</a>
      <img src="secretgame_channel.png" alt="채널 미리보기" />
    </div>
  </div>

  <!-- 미니게임 팝업 -->
  <div id="game-popup">
    <h3>1~10 숫자 맞추기</h3>
    <input id="guess" placeholder="숫자 입력" />
    <button onclick="checkGuess()">확인</button>
    <p id="result"></p>
    <button onclick="closeGame()">❌ 닫기</button>
  </div>

  <script>
    const usedNicknames = JSON.parse(localStorage.getItem('usedNicknames') || '[]');

    function setNickname() {
      const nickname = document.getElementById('nickname-input').value.trim();
      if (!nickname) return;
      if (usedNicknames.includes(nickname)) {
        document.getElementById('nickname-warning').innerText = "이미 사용 중인 닉네임입니다.";
        return;
      }
      localStorage.setItem('nickname', nickname);
      usedNicknames.push(nickname);
      localStorage.setItem('usedNicknames', JSON.stringify(usedNicknames));
      document.getElementById('nickname-overlay').style.display = 'none';
    }

    function showSection(section) {
      document.getElementById('home-section').classList.add('hidden');
      document.getElementById('chat-section').classList.add('hidden');
      document.getElementById('channel-section').classList.add('hidden');
      document.getElementById(section + '-section').classList.remove('hidden');
    }

    const sendBtn = document.getElementById('send-btn');
    const chatBox = document.getElementById('chat-box');
    sendBtn.addEventListener('click', () => {
      const nickname = localStorage.getItem('nickname') || "익명";
      const message = document.getElementById('message-input').value;
      if (!message.trim()) return;
      const msg = document.createElement('div');
      msg.innerText = `${nickname}: ${message}`;
      chatBox.appendChild(msg);
      document.getElementById('message-input').value = "";
      chatBox.scrollTop = chatBox.scrollHeight;
    });

    let answer = Math.floor(Math.random() * 10) + 1;
    function openGame() {
      document.getElementById('game-popup').style.display = 'block';
      document.getElementById('result').innerText = "";
    }

    function closeGame() {
      document.getElementById('game-popup').style.display = 'none';
    }

    function checkGuess() {
      const guess = parseInt(document.getElementById('guess').value);
      const result = document.getElementById('result');
      if (guess === answer) {
        result.innerText = "🎉 정답입니다!";
        answer = Math.floor(Math.random() * 10) + 1;
      } else {
        result.innerText = "❌ 틀렸어요! 다시 시도!";
      }
    }

    window.onload = () => {
      const nickname = localStorage.getItem('nickname');
      if (nickname) document.getElementById('nickname-overlay').style.display = 'none';
    };
  </script>

</body>
</html>
