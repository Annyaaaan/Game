<!DOCTYPE html><html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Game Matematika</title>
  <style>
    body { font-family: sans-serif; text-align: center; background: #e3f2fd; }
    h1 { color: #1976d2; }
    .level-grid { display: flex; flex-wrap: wrap; justify-content: center; gap: 10px; }
    .level-button {
      padding: 10px 20px; background: #64b5f6; border: none; border-radius: 8px;
      font-size: 18px; color: white; cursor: pointer; min-width: 100px;
    }
    .locked { background: #b0bec5; cursor: not-allowed; }
    .hidden { display: none; }
    .question { font-size: 24px; margin: 20px 0; }
    .answer-button {
      margin: 5px; padding: 10px 20px; font-size: 18px; cursor: pointer;
      border: none; background: #81c784; color: white; border-radius: 8px;
    }
    .success { color: green; font-weight: bold; }
  </style>
</head>
<body>  <h1>🔢 Game Matematika</h1>
  <div id="menu">
    <p>Pilih level yang sudah terbuka:</p>
    <div class="level-grid" id="levelButtons"></div>
  </div>  <div id="game" class="hidden">
    <h2 id="levelTitle"></h2>
    <div class="question" id="question"></div>
    <div id="answers"></div>
    <div id="feedback"></div>
  </div>  <script>
    const totalLevels = 10;
    let currentLevel = 1;
    let currentQuestion = 0;
    let score = 0;
    let questions = [];

    function loadUnlockedLevels() {
      const unlocked = JSON.parse(localStorage.getItem("unlockedLevels")) || [1];
      const levelButtons = document.getElementById("levelButtons");
      levelButtons.innerHTML = "";
      for (let i = 1; i <= totalLevels; i++) {
        const btn = document.createElement("button");
        btn.innerText = `Level ${i} ${unlocked.includes(i) ? '' : '🔒'}`;
        btn.className = "level-button" + (unlocked.includes(i) ? "" : " locked");
        if (unlocked.includes(i)) btn.onclick = () => startLevel(i);
        levelButtons.appendChild(btn);
      }
    }

    function generateQuestions(level) {
      const ops = level <= 3 ? ["+"] : level <= 6 ? ["-"] : ["+", "-"];
      const max = level <= 3 ? 20 : level <= 6 ? 20 : 50;
      let q = [];
      for (let i = 0; i < 5; i++) {
        const a = Math.floor(Math.random() * max);
        const b = Math.floor(Math.random() * max);
        const op = ops[Math.floor(Math.random() * ops.length)];
        const question = `${a} ${op} ${b}`;
        const answer = eval(question);
        q.push({ q: question, a: answer });
      }
      return q;
    }

    function startLevel(level) {
      currentLevel = level;
      currentQuestion = 0;
      score = 0;
      questions = generateQuestions(level);
      document.getElementById("menu").classList.add("hidden");
      document.getElementById("game").classList.remove("hidden");
      document.getElementById("levelTitle").innerText = `Level ${level}`;
      showQuestion();
    }

    function showQuestion() {
      const q = questions[currentQuestion];
      document.getElementById("question").innerText = q.q;
      const answers = document.getElementById("answers");
      answers.innerHTML = "";
      const correct = q.a;
      let options = [correct];
      while (options.length < 3) {
        let rand = correct + Math.floor(Math.random() * 10 - 5);
        if (!options.includes(rand)) options.push(rand);
      }
      options.sort(() => Math.random() - 0.5);
      options.forEach(opt => {
        const btn = document.createElement("button");
        btn.innerText = opt;
        btn.className = "answer-button";
        btn.onclick = () => checkAnswer(opt);
        answers.appendChild(btn);
      });
    }

    function checkAnswer(ans) {
      const q = questions[currentQuestion];
      const feedback = document.getElementById("feedback");
      if (ans === q.a) {
        feedback.innerText = "✅ Benar!";
        score++;
      } else {
        feedback.innerText = "❌ Salah. Jawaban yang benar: " + q.a;
      }
      currentQuestion++;
      if (currentQuestion < questions.length) {
        setTimeout(() => { feedback.innerText = ""; showQuestion(); }, 1000);
      } else {
        setTimeout(() => {
          feedback.innerText = "";
          document.getElementById("game").classList.add("hidden");
          document.getElementById("menu").classList.remove("hidden");
          if (score === 5 && currentLevel < totalLevels) unlockNextLevel(currentLevel + 1);
          alert(`Selesai! Skor kamu: ${score}/5`);
          loadUnlockedLevels();
        }, 1000);
      }
    }

    function unlockNextLevel(level) {
      const unlocked = JSON.parse(localStorage.getItem("unlockedLevels")) || [1];
      if (!unlocked.includes(level)) {
        unlocked.push(level);
        localStorage.setItem("unlockedLevels", JSON.stringify(unlocked));
      }
    }

    loadUnlockedLevels();
  </script></body>
</html>
