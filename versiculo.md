```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="ROARRadio - O rugido do Leão que toca o coração" />
  <meta name="theme-color" content="#111827" />
  <title>ROARRadio</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Font Awesome -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />

  <!-- Favicon -->
  <link rel="icon" type="image/x-icon" href="/favicon.ico" sizes="32x32" />
  <link rel="apple-touch-icon" href="/favicon.ico" />

  <!-- Manifest for PWA -->
  <link rel="manifest" href="/manifest.json" />

  <style>
    body::before {
      content: "";
      background: url('ROARRadio-logo.png') center/contain no-repeat;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      opacity: 0.05;
      z-index: -1;
      filter: blur(1px);
    }
  </style>
</head>
<body class="font-sans text-yellow-100 bg-[#111827] flex flex-col items-center justify-center p-4 min-h-screen">

  <div class="rounded-xl p-8 player-container w-full max-w-md">
    <div class="text-center mb-8">
      <h1 class="text-4xl font-bold text-yellow-400 mb-2">ROARRadio</h1>
      <p class="text-yellow-200">O rugido do Leão que toca o coração</p>
    </div>

    <div class="flex flex-col items-center mb-8 space-y-4">
      <button id="playButton" class="bg-yellow-600 hover:bg-yellow-700 text-white rounded-full w-16 h-16 flex items-center justify-center shadow-lg transition-all duration-200">
        <i id="playIcon" class="fas fa-play text-2xl"></i>
      </button>
      <p id="statusText" class="text-sm text-yellow-300">Carregando áudio...</p>

      <div class="w-full max-w-xs">
        <div class="flex items-center space-x-2">
          <i class="fas fa-volume-down text-yellow-300"></i>
          <input type="range" id="volumeControl" class="w-full h-1 accent-yellow-500 bg-gray-700 outline-none" min="0" max="1" step="0.01" value="0.1" />
          <i class="fas fa-volume-up text-yellow-300"></i>
        </div>
      </div>
    </div>

    <div class="rounded-lg p-4 mb-8">
      <h3 class="font-semibold text-center text-yellow-300 mb-2">Versículo do dia</h3>
      <p id="bibleVerse" class="text-center italic text-yellow-200">Carregando versículo...</p>
    </div>

    <div class="text-xs text-yellow-300 text-center mb-4">
      <p>Esta rádio é um projeto pessoal, sem fins lucrativos. Os louvores são usados apenas com o propósito de tocar no coração de amigos, familiares e quem mais ouvir. Caso você seja o artista ou detentor dos direitos e deseje que o conteúdo seja removido, entre em contato e será retirado imediatamente com respeito e gratidão.</p>
    </div>

    <div class="flex justify-center space-x-4">
      <a href="https://wa.me/5547992280371" target="_blank" class="text-yellow-300 hover:text-yellow-100 transition-colors duration-200">
        <i class="fab fa-whatsapp text-xl"></i>
      </a>
      <a href="https://www.instagram.com/azzor1337x/" target="_blank" class="text-yellow-300 hover:text-yellow-100 transition-colors duration-200">
        <i class="fab fa-instagram text-xl"></i>
      </a>
    </div>
  </div>

  <audio id="introStream" preload="auto">
    <source src="/intro.mp3" type="audio/mpeg" />
  </audio>

  <audio id="radioStream" preload="none">
    <source src="https://icecast.roarradio.site/radio.mp3" type="audio/mpeg" />
  </audio>

  <script>
    const introStream = document.getElementById('introStream');
    const radioStream = document.getElementById('radioStream');
    const playButton = document.getElementById('playButton');
    const playIcon = document.getElementById('playIcon');
    const volumeControl = document.getElementById('volumeControl');
    const statusText = document.getElementById('statusText');

    let isPlaying = false;
    let hasInteracted = false;

    const resetAndPlay = async () => {
      try {
        introStream.currentTime = 0;
        radioStream.pause();
        introStream.play();

        statusText.textContent = "Tocando introdução...";
        playIcon.className = 'fas fa-pause text-2xl';
        isPlaying = true;

        introStream.onended = async () => {
          radioStream.currentTime = 0;
          await radioStream.play();
          statusText.textContent = "Transmitindo ao vivo!";
        };
      } catch (error) {
        console.error("Erro ao tocar áudio:", error);
        alert("Não foi possível tocar o áudio. Verifique sua conexão.");
      }
    };

    document.body.addEventListener('click', () => {
      if (!hasInteracted) {
        hasInteracted = true;
        resetAndPlay();
      }
    });

    playButton.addEventListener('click', () => {
      if (isPlaying) {
        introStream.pause();
        radioStream.pause();
        playIcon.className = 'fas fa-play text-2xl';
        statusText.textContent = "Pausado";
        isPlaying = false;
      } else {
        resetAndPlay();
      }
    });

    volumeControl.addEventListener('input', function () {
      introStream.volume = this.value;
      radioStream.volume = this.value;
    });

    introStream.volume = volumeControl.value;
    radioStream.volume = volumeControl.value;

    radioStream.addEventListener('canplay', () => {
      if (!isPlaying) statusText.textContent = "Pronto para tocar!";
    });

    radioStream.addEventListener('error', () => {
      statusText.textContent = "Erro ao carregar o áudio.";
      playIcon.className = 'fas fa-play text-2xl';
      isPlaying = false;
    });

    async function getDailyVerseFromAPI() {
      try {
        const versesResponse = await fetch('versiculos.json');
        const verses = await versesResponse.json();

        const randomVerse = verses[Math.floor(Math.random() * verses.length)];
        const url = `https://bible-api.com/${encodeURIComponent(randomVerse)}?translation=almeida`;

        const response = await fetch(url);
        const data = await response.json();

        if (data && data.text) {
          document.getElementById('bibleVerse').textContent = `${data.text.trim()} — ${data.reference}`;
        } else {
          throw new Error("Resposta inválida da API.");
        }
      } catch (error) {
        console.error("Erro ao carregar versículo:", error);
        document.getElementById('bibleVerse').textContent = "Não foi possível carregar o versículo.";
      }
    }

    getDailyVerseFromAPI();
  </script>
</body>
</html>
```
