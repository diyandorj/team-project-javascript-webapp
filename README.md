# team-project-javascript-webapp
// ===== DATA =====
const questions = {
    javascript: [
        {
            question: "What is JavaScript?",
            options: ["Programming language", "Database", "Server", "Browser"],
            answer: 0,
            difficulty: "easy"
        },
        {
            question: "Which keyword declares a variable?",
            options: ["var", "int", "string", "define"],
            answer: 0,
            difficulty: "easy"
        }
    ],
    html: [
        {
            question: "What does HTML stand for?",
            options: ["Hyper Text Markup Language", "Home Tool Markup Language", "Hyperlinks Text Language", "None"],
            answer: 0,
            difficulty: "easy"
        }
    ],
    react: [
        {
            question: "React is a ____?",
            options: ["Library", "Language", "Database", "OS"],
            answer: 0,
            difficulty: "easy"
        }
    ]
};

// ===== VARIABLES =====
let currentCategory = "";
let currentQuestionIndex = 0;
let score = 0;
let correctAnswers = 0;
let totalQuestions = 0;
let streak = 0;
let bestStreak = 0;
let selectedAnswers = [];

// ===== ELEMENTS =====
const homeScreen = document.getElementById("homeScreen");
const quizScreen = document.getElementById("quizScreen");
const resultsScreen = document.getElementById("resultsScreen");

const questionText = document.getElementById("questionText");
const optionsContainer = document.getElementById("optionsContainer");

const nextBtn = document.getElementById("nextBtn");
const prevBtn = document.getElementById("prevBtn");

const totalScoreEl = document.getElementById("totalScore");
const accuracyEl = document.getElementById("accuracy");
const streakEl = document.getElementById("streak");

// ===== START QUIZ =====
document.querySelectorAll(".category-btn").forEach(btn => {
    btn.addEventListener("click", () => {
        currentCategory = btn.dataset.category;
        startQuiz();
    });
});

function startQuiz() {
    homeScreen.classList.remove("active");
    quizScreen.classList.add("active");

    currentQuestionIndex = 0;
    score = 0;
    correctAnswers = 0;
    streak = 0;
    bestStreak = 0;

    selectedAnswers = new Array(questions[currentCategory].length).fill(null);
    totalQuestions = questions[currentCategory].length;

    loadQuestion();
}

// ===== LOAD QUESTION =====
function loadQuestion() {
    const q = questions[currentCategory][currentQuestionIndex];

    questionText.textContent = q.question;
    optionsContainer.innerHTML = "";

    q.options.forEach((option, index) => {
        const div = document.createElement("div");
        div.classList.add("option");
        div.textContent = option;

        div.addEventListener("click", () => selectOption(index, div));

        optionsContainer.appendChild(div);
    });

    updateProgress();
}

// ===== SELECT ANSWER =====
function selectOption(index, element) {
    const q = questions[currentCategory][currentQuestionIndex];

    // Remove previous selection
    document.querySelectorAll(".option").forEach(opt => {
        opt.classList.remove("selected");
    });

    element.classList.add("selected");
    selectedAnswers[currentQuestionIndex] = index;

    // Check correct
    if (index === q.answer) {
        element.classList.add("correct");
        correctAnswers++;
        streak++;
        bestStreak = Math.max(bestStreak, streak);
        document.getElementById("correctSound").play();
    } else {
        element.classList.add("incorrect");
        streak = 0;
        document.getElementById("wrongSound").play();
    }

    updateStats();
}

// ===== NEXT BUTTON =====
nextBtn.addEventListener("click", () => {
    if (currentQuestionIndex < totalQuestions - 1) {
        currentQuestionIndex++;
        loadQuestion();
    } else {
        showResults();
    }
});

// ===== PREVIOUS BUTTON =====
prevBtn.addEventListener("click", () => {
    if (currentQuestionIndex > 0) {
        currentQuestionIndex--;
        loadQuestion();
    }
});

// ===== UPDATE PROGRESS =====
function updateProgress() {
    const progress = ((currentQuestionIndex + 1) / totalQuestions) * 100;
    document.getElementById("progressFill").style.width = progress + "%";
    document.getElementById("progressText").textContent =
        `Question ${currentQuestionIndex + 1}/${totalQuestions}`;
}

// ===== UPDATE STATS =====
function updateStats() {
    totalScoreEl.textContent = correctAnswers * 10;
    accuracyEl.textContent = Math.round((correctAnswers / (currentQuestionIndex + 1)) * 100) + "%";
    streakEl.textContent = streak;
}

// ===== SHOW RESULTS =====
function showResults() {
    quizScreen.classList.remove("active");
    resultsScreen.classList.add("active");

    const percent = Math.round((correctAnswers / totalQuestions) * 100);

    document.getElementById("finalScore").textContent = percent + "%";
    document.getElementById("resultsCorrect").textContent = correctAnswers;
    document.getElementById("resultsTotal").textContent = totalQuestions;
    document.getElementById("resultsStreak").textContent = bestStreak;
}

// ===== RESTART =====
document.getElementById("restartBtn").addEventListener("click", () => {
    resultsScreen.classList.remove("active");
    homeScreen.classList.add("active");
});
