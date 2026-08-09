
<html lang="it">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Chat Simulata Interattiva</title>
  <!-- Icone FontAwesome per tasti Play e Social -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
    }

    body {
      background-color: #799ba9; /* Colore di sfondo della chat */
      display: flex;
      justify-content: center;
      min-height: 100vh;
    }

    .chat-container {
      width: 100%;
      max-width: 480px;
      display: flex;
      flex-direction: column;
      height: 100vh;
      background-color: #8daebb;
      position: relative;
    }

    /* Intestazione */
    .chat-header {
      background-color: #2b3a42;
      color: white;
      padding: 15px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      position: sticky;
      top: 0;
      z-index: 10;
    }

    .user-info {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .avatar {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      background-color: #fff;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #2b3a42;
      font-weight: bold;
    }

    /* Area Messaggi */
    .chat-messages {
      flex: 1;
      padding: 15px;
      overflow-y: auto;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    /* Bubble dei messaggi */
    .message {
      max-width: 75%;
      padding: 10px 14px;
      border-radius: 15px;
      font-size: 14px;
      line-height: 1.4;
      position: relative;
      opacity: 0;
      transform: translateY(10px);
      animation: fadeIn 0.3s forwards ease-in-out;
    }

    @keyframes fadeIn {
      to {
        opacity: 1;
        transform: translateY(0);
      }
    }

    .message.received {
      align-self: flex-start;
      background-color: #ffffff;
      color: #000;
      border-bottom-left-radius: 2px;
    }

    .message.sent {
      align-self: flex-end;
      background-color: #f7e665; /* Giallo stile LINE */
      color: #000;
      border-bottom-right-radius: 2px;
    }

    .timestamp {
      font-size: 10px;
      color: #666;
      margin-top: 4px;
      text-align: right;
    }

    /* Player Audio nei Messaggi */
    .audio-player {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .play-btn {
      background-color: #000;
      color: #fff;
      border: none;
      width: 30px;
      height: 30px;
      border-radius: 50%;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    /* Banner Finale e Pulsanti Social */
    .final-banner {
      display: none;
      flex-direction: column;
      align-items: center;
      gap: 15px;
      margin-top: 20px;
      padding-bottom: 30px;
      animation: fadeIn 0.5s forwards;
    }

    .social-icons {
      display: flex;
      gap: 15px;
    }

    .social-btn {
      width: 45px;
      height: 45px;
      border-radius: 50%;
      background: white;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #000;
      text-decoration: none;
      font-size: 18px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.2);
    }

    .card-banner {
      background: white;
      border-radius: 12px;
      padding: 15px;
      width: 90%;
      text-align: center;
      box-shadow: 0 4px 10px rgba(0,0,0,0.15);
    }

    .card-banner a {
      display: block;
      margin-top: 10px;
      font-weight: bold;
      color: #000;
      text-decoration: none;
    }
  </style>
</head>
<body>

  <div class="chat-container">
    <!-- Intestazione Chat -->
    <div class="chat-header">
      <div class="user-info">
        <i class="fa-solid fa-chevron-left"></i>
        <div class="avatar">KF</div>
        <span>Koharu Fuyutsuki</span>
      </div>
      <div>
        <i class="fa-solid fa-magnifying-glass" style="margin-right: 10px;"></i>
        <i class="fa-solid fa-phone"></i>
      </div>
    </div>

    <!-- Contenitore Messaggi -->
    <div class="chat-messages" id="chatBox"></div>

    <!-- Banner Finale (Appare alla fine della sequenza) -->
    <div class="final-banner" id="finalBanner">
      <div class="social-icons">
        <a href="#" class="social-btn"><i class="fa-brands fa-x-twitter"></i></a>
        <a href="#" class="social-btn"><i class="fa-solid fa-comment"></i></a>
        <a href="#" class="social-btn"><i class="fa-brands fa-facebook-f"></i></a>
      </div>
      <div class="card-banner">
        <p><i>"Unisciti a noi e scopri la storia..."</i></p>
        <a href="#">Vai al sito ufficiale &rarr;</a>
      </div>
    </div>
  </div>

  <script>
    // 1. CONFIGURA I TUOI MESSAGGI E I TEMPI (in millisecondi)
    const messagesData = [
      { type: 'text', sender: 'received', text: 'Grazie per oggi!', time: '20:45', delay: 1000 },
      { type: 'text', sender: 'sent', text: 'Di cosa parli?', time: '21:00', delay: 2500 },
      { type: 'text', sender: 'received', text: 'Sì, esatto', time: '21:12', delay: 4000 },
      { type: 'text', sender: 'sent', text: 'Come si fa?', time: '21:13', delay: 5500 },
      // Messaggio vocale con link file MP3 di test
      { 
        type: 'audio', 
        sender: 'sent', 
        audioUrl: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3', 
        time: '21:13', 
        delay: 7000 
      },
      { type: 'text', sender: 'received', text: 'Hai mandato un vocale!', time: '21:15', delay: 9000 },
      { type: 'text', sender: 'sent', text: 'Posso cancellarlo?', time: '21:15', delay: 10500 },
      { 
        type: 'audio', 
        sender: 'received', 
        audioUrl: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3', 
        time: '21:16', 
        delay: 12000 
      }
    ];

    const chatBox = document.getElementById('chatBox');
    const finalBanner = document.getElementById('finalBanner');

    // Funzione per creare i messaggi a schermo
    function addMessage(msg) {
      const msgDiv = document.createElement('div');
      msgDiv.classList.add('message', msg.sender);

      if (msg.type === 'text') {
        msgDiv.innerHTML = `
          <div>${msg.text}</div>
          <div class="timestamp">${msg.time}</div>
        `;
      } else if (msg.type === 'audio') {
        msgDiv.innerHTML = `
          <div class="audio-player">
            <button class="play-btn" onclick="playAudio('${msg.audioUrl}', this)">
              <i class="fa-solid fa-play"></i>
            </button>
            <span>Vocale</span>
          </div>
          <div class="timestamp">${msg.time}</div>
        `;
      }

      chatBox.appendChild(msgDiv);
      chatBox.scrollTop = chatBox.scrollHeight; // Scroll automatico verso il basso
    }

    // Gestione della riproduzione Audio
    let currentAudio = null;
    let currentBtn = null;

    function playAudio(url, btn) {
      if (currentAudio) {
        currentAudio.pause();
        if (currentBtn) currentBtn.innerHTML = '<i class="fa-solid fa-play"></i>';
      }

      if (currentBtn === btn && !currentAudio.paused) {
        return;
      }

      currentAudio = new Audio(url);
      currentBtn = btn;
      btn.innerHTML = '<i class="fa-solid fa-pause"></i>';
      currentAudio.play();

      currentAudio.onended = () => {
        btn.innerHTML = '<i class="fa-solid fa-play"></i>';
      };
    }

    // Sequenza temporizzata dei messaggi
    messagesData.forEach((msg, index) => {
      setTimeout(() => {
        addMessage(msg);
        
        // Se è l'ultimo messaggio, mostra il banner finale
        if (index === messagesData.length - 1) {
          setTimeout(() => {
            finalBanner.style.display = 'flex';
            chatBox.scrollTop = chatBox.scrollHeight;
          }, 1500);
        }
      }, msg.delay);
    });
  </script>
</body>
</html>
