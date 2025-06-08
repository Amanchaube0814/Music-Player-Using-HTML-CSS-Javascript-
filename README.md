# Music-Player-Using-HTML-CSS-Javascript-

HTML

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Music Player</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <div class="player">
    <h2 id="title">Song Title</h2>
    <h4 id="artist">Artist</h4>

    <audio id="audio" src=""></audio>

    <div class="controls">
      <button onclick="prevSong()">⏮️</button>
      <button onclick="togglePlay()" id="play-btn">▶️</button>
      <button onclick="nextSong()">⏭️</button>
    </div>

    <div class="progress-container" onclick="setProgress(event)">
      <div class="progress" id="progress"></div>
    </div>
    <div class="time">
      <span id="current-time">0:00</span>
      <span id="duration">0:00</span>
    </div>

    <input type="range" id="volume" min="0" max="1" step="0.01" onchange="setVolume(this.value)" />

    <div class="playlist">
      <h3>Playlist</h3>
      <ul id="playlist"></ul>
    </div>
  </div>

  <script src="script.js"></script>
</body>
</html>


CSS

body {
  font-family: sans-serif;
  background: #222;
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.player {
  width: 320px;
  padding: 20px;
  background: #333;
  border-radius: 15px;
  text-align: center;
}

h2, h4 {
  margin: 0.5em 0;
}

.controls button {
  font-size: 1.5rem;
  background: none;
  border: none;
  color: white;
  margin: 0 10px;
  cursor: pointer;
}

.progress-container {
  background: #555;
  height: 8px;
  border-radius: 5px;
  margin: 1em 0;
  cursor: pointer;
  width: 100%;
}

.progress {
  background: #0f0;
  height: 8px;
  width: 0%;
  border-radius: 5px;
}

.time {
  display: flex;
  justify-content: space-between;
  font-size: 0.9rem;
}

#volume {
  width: 100%;
  margin-top: 1em;
}

.playlist {
  margin-top: 20px;
  text-align: left;
}

.playlist ul {
  padding: 0;
  list-style: none;
  max-height: 150px;
  overflow-y: auto;
}

.playlist li {
  padding: 5px;
  cursor: pointer;
}

.playlist li:hover {
  background: #444;
}

JS

const songs = [
  {
    title: "Song One",
    artist: "Artist A",
    src: "song1.mp3"
  },
  {
    title: "Song Two",
    artist: "Artist B",
    src: "song2.mp3"
  },
  {
    title: "Song Three",
    artist: "Artist C",
    src: "song3.mp3"
  }
];

let currentSong = 0;
const audio = document.getElementById("audio");
const title = document.getElementById("title");
const artist = document.getElementById("artist");
const playBtn = document.getElementById("play-btn");
const progress = document.getElementById("progress");
const currentTimeEl = document.getElementById("current-time");
const durationEl = document.getElementById("duration");
const playlistEl = document.getElementById("playlist");

function loadSong(index) {
  const song = songs[index];
  title.textContent = song.title;
  artist.textContent = song.artist;
  audio.src = song.src;
  highlightCurrent();
}

function togglePlay() {
  if (audio.paused) {
    audio.play();
    playBtn.textContent = "⏸️";
  } else {
    audio.pause();
    playBtn.textContent = "▶️";
  }
}

function nextSong() {
  currentSong = (currentSong + 1) % songs.length;
  loadSong(currentSong);
  audio.play();
  playBtn.textContent = "⏸️";
}

function prevSong() {
  currentSong = (currentSong - 1 + songs.length) % songs.length;
  loadSong(currentSong);
  audio.play();
  playBtn.textContent = "⏸️";
}

function updateProgress() {
  const { duration, currentTime } = audio;
  const percent = (currentTime / duration) * 100;
  progress.style.width = `${percent}%`;

  currentTimeEl.textContent = formatTime(currentTime);
  durationEl.textContent = formatTime(duration);
}

function setProgress(e) {
  const width = e.currentTarget.clientWidth;
  const clickX = e.offsetX;
  const duration = audio.duration;
  audio.currentTime = (clickX / width) * duration;
}

function setVolume(val) {
  audio.volume = val;
}

function formatTime(t) {
  if (isNaN(t)) return "0:00";
  const minutes = Math.floor(t / 60);
  const seconds = Math.floor(t % 60).toString().padStart(2, "0");
  return `${minutes}:${seconds}`;
}

function highlightCurrent() {
  const items = playlistEl.querySelectorAll("li");
  items.forEach((item, i) => {
    item.style.background = i === currentSong ? "#444" : "none";
  });
}

// Playlist rendering
songs.forEach((song, index) => {
  const li = document.createElement("li");
  li.textContent = `${song.title} - ${song.artist}`;
  li.onclick = () => {
    currentSong = index;
    loadSong(currentSong);
    audio.play();
    playBtn.textContent = "⏸️";
  };
  playlistEl.appendChild(li);
});

// Event Listeners
audio.addEventListener("timeupdate", updateProgress);
audio.addEventListener("ended", nextSong); // autoplay next
window.addEventListener("load", () => loadSong(currentSong));
