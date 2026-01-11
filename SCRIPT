const words = [
  "trap", "beat", "flow", "hype", "drip", "flex", "grind", "stack", "lit", "wave",
  "ghost", "flame", "neon", "vibe", "pulse", "glitch", "cyber", "haze", "storm", "peak"
];

let gameState = 'waiting';
let score = 0;
let combo = 0;
let maxCombo = 0;
let totalHits = 0;
let perfectHits = 0;
let currentWordIndex = 0;
let letters = [];
let trackSpeed = 2; // pixels per frame
let gameTrack = [];
let audio = document.getElementById('bg-music');

const elements = {
  trackLine: document.getElementById('track-line'),
  hitZone: document.getElementById('hit-zone'),
  currentLetter: document.getElementById('current-letter'),
  nextWords: document.getElementById('next-words'),
  combo: document.getElementById('combo'),
  scoreEl: document.getElementById('score'),
  accuracy: document.getElementById('accuracy'),
  startBtn: document.getElementById('start-btn'),
  restartBtn: document.getElementById('restart-btn'),
  gameOver: document.getElementById('game-over'),
  finalStats: document.getElementById('final-stats'),
  playAgain: document.getElementById('play-again')
};

function initGame() {
  elements.startBtn.onclick = startGame;
  elements.restartBtn.onclick = newTrack;
  elements.playAgain.onclick = initGame;
  
  // Generate track of 15 words
  gameTrack = [];
  for (let i = 0; i < 15; i++) {
    gameTrack.push(words[Math.floor(Math.random() * words.length)]);
  }
}

function startGame() {
  gameState = 'playing';
  score = 0;
  combo = 0;
  maxCombo = 0;
  totalHits = 0;
  perfectHits = 0;
  currentWordIndex = 0;
  letters = [];
  elements.startBtn.classList.add('hidden');
  elements.restartBtn.classList.remove('hidden');
  audio.play().catch(() => {}); // Autoplay may be blocked
  
  updateDisplay();
  gameLoop();
}

function newTrack() {
  currentWordIndex = 0;
  letters = [];
  updateDisplay();
}

function gameLoop() {
  if (gameState !== 'playing') return;
  
  // Update letter positions
  letters.forEach((letter, index) => {
    letter.x -= trackSpeed;
    letter.el.style.left = letter.x + 'px';
    
    if (letter.x < -50) {
      // Missed letter
      letters.splice(index, 1);
      missLetter();
    }
  });
  
  requestAnimationFrame(gameLoop);
}

function addLetter(char, timingOffset = 0) {
  const letter = document.createElement('div');
  letter.className = 'letter';
  letter.textContent = char.toUpperCase();
  letter.style.left = '100%';
  letter.style.top = '48%';
  
  const letterObj = {
    el: letter,
    x: window.innerWidth * 0.9,
    char: char,
    timing: Date.now() + timingOffset
  };
  
  document.querySelector('.track-container').appendChild(letter);
  letters.push(letterObj);
}

function handleKey(e) {
  if (gameState !== 'playing') return;
  
  const currentChar = letters[0]?.char;
  if (!currentChar || e.key.toLowerCase() !== currentChar) return;
  
  const timing = Date.now();
  const letterTiming = letters[0].timing;
  const diff = Math.abs(timing - letterTiming);
  
  let result = 'miss';
  if (diff < 100) result = 'perfect';
  else if (diff < 300) result = 'good';
  
  letters[0].el.classList.add(result);
  letters.shift();
  
  if (result === 'perfect') {
    perfectHits++;
    score += 100 + combo * 10;
    combo++;
    maxCombo = Math.max(maxCombo, combo);
  } else if (result === 'good') {
    score += 50;
    combo = 0;
  } else {
    score += 10;
    combo = 0;
  }
  
  totalHits++;
  updateDisplay();
  
  if (currentWordIndex >= gameTrack.length) {
    endGame();
  }
}

function missLetter() {
  combo = 0;
  updateDisplay();
}

function updateDisplay() {
  elements.combo.textContent = `Combo: ${combo}`;
  elements.scoreEl.textContent = `Score: ${score}`;
  elements.accuracy.textContent = `${Math.round((perfectHits/totalHits)*100) || 0}%`;
  
  const nextWord = gameTrack[currentWordIndex];
  if (nextWord) {
    elements.nextWords.textContent = `Next: ${nextWord}`;
    elements.currentLetter.textContent = nextWord[0]?.toUpperCase() || '';
  }
}

function endGame() {
  gameState = 'ended';
  audio.pause();
  elements.gameOver.classList.remove('hidden');
  elements.finalStats.innerHTML = `
    Final Score: <strong>${score}</strong><br>
    Max Combo: <strong>${maxCombo}</strong><br>
    Accuracy: <strong>${Math.round((perfectHits/totalHits)*100)}%</strong>
  `;
}

// Add next word letters when current word finishes
setInterval(() => {
  if (gameState !== 'playing' || !gameTrack[currentWordIndex]) return;
  
  const word = gameTrack[currentWordIndex];
  if (letters.length === 0) {
    // Start next word
    for (let i = 0; i < word.length; i++) {
      setTimeout(() => addLetter(word[i]), i * 400);
    }
    currentWordIndex++;
  }
}, 500);

document.addEventListener('keydown', handleKey);

initGame();
