const levelSelect = document.getElementById('level-select');
const startButton = document.getElementById('start-button');
const restartButton = document.getElementById('restart-button');
const timerDisplay = document.getElementById('timer');
const cardGrid = document.getElementById('card-grid');
const popup = document.getElementById('popup');
const popupTime = document.getElementById('popup-time');

let level = 'beginner'; // Default level
let cards = [];
let revealedCards = [];
let timerInterval;
let timer = 0;

// Function to shuffle an array
function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
}

// Function to create and initialize cards
function createCards() {
    cards = [];
    revealedCards = [];
    cardGrid.innerHTML = '';

    // Determine the number of cards based on the level
    const totalCards = (level === 'beginner') ? 20 : 30;
    const animals = ['dog', 'cat', 'elephant', 'lion', 'giraffe', 'monkey', 'panda', 'tiger', 'zebra', 'penguin'];

    // Duplicate animals to create pairs
    const animalPairs = animals.slice(0, totalCards / 2);
    const allAnimals = [...animalPairs, ...animalPairs];

    shuffleArray(allAnimals);

    for (const animal of allAnimals) {
        const card = document.createElement('div');
        card.className = 'card';
        card.dataset.animal = animal;
        card.addEventListener('click', () => revealCard(card));
        cardGrid.appendChild(card);
        cards.push(card);
    }
}

// Function to start the timer
function startTimer() {
    timer = 0;
    timerDisplay.textContent = '0 seconds';
    timerInterval = setInterval(() => {
        timer++;
        timerDisplay.textContent = `${timer} seconds`;
    }, 1000);
}

// Function to reveal a card
function revealCard(card) {
    if (revealedCards.length < 2) {
        card.classList.add('revealed');
        revealedCards.push(card);

        if (revealedCards.length === 2) {
            // Check if the revealed cards match
            if (revealedCards[0].dataset.animal === revealedCards[1].dataset.animal) {
                revealedCards.forEach(card => card.classList.add('matched'));
                revealedCards = [];
            } else {
                setTimeout(() => {
                    revealedCards.forEach(card => card.classList.remove('revealed'));
                    revealedCards = [];
                }, 1000);
            }
        }
    }
}

// Function to end the game and show the popup
function endGame() {
    clearInterval(timerInterval);
    popupTime.textContent = `${timer} seconds`;
    popup.style.display = 'block';
}

// Event listeners
levelSelect.addEventListener('change', () => {
    level = levelSelect.value;
    createCards();
});

startButton.addEventListener('click', () => {
    createCards();
    startTimer();
    startButton.disabled = true;
});

restartButton.addEventListener('click', () => {
    clearInterval(timerInterval);
    popup.style.display = 'none';
    startButton.disabled = false;
    createCards();
});

// Initialize the game
createCards();
