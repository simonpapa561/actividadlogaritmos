<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Actividad de Logaritmos</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: Arial, sans-serif;
    background: #f0f4f0;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    padding: 2rem 1rem;
  }
  .app { width: 100%; max-width: 620px; }
  h1 { font-size: 24px; font-weight: bold; color: #1a1a1a; margin-bottom: 1.5rem; text-align: center; }
  .header { display: flex; align-items: center; gap: 12px; margin-bottom: 1.5rem; }
  .progress-bg { flex: 1; height: 10px; background: #ddd; border-radius: 99px; overflow: hidden; }
  .progress-fill { height: 100%; background: #1D9E75; border-radius: 99px; transition: width 0.4s; width: 0%; }
  .lives { font-size: 16px; }
  .counter { text-align: center; font-size: 13px; color: #777; margin-bottom: 1rem; }
  .card { background: white; border: 1px solid #ddd; border-radius: 12px; padding: 1.5rem; margin-bottom: 1rem; }
  .tag { display: inline-block; background: #d4f0e6; color: #0a6645; font-size: 12px; font-weight: bold; padding: 4px 12px; border-radius: 99px; margin-bottom: 1rem; }
  .question-text { font-size: 18px; line-height: 2; color: #1a1a1a; }
  input.blank {
    border: none;
    border-bottom: 3px solid #1D9E75;
    font-size: 18px;
    font-family: Arial, sans-serif;
    background: transparent;
    text-align: center;
    padding: 0 4px 2px;
    outline: none;
    color: #1a1a1a;
    min-width: 70px;
  }
  input.blank.correct { border-bottom-color: #1D9E75; color: #0a6645; }
  input.blank.wrong { border-bottom-color: #e74c3c; color: #c0392b; }
  .hint { margin-top: 1rem; font-size: 13px; color: #999; font-style: italic; }
  .feedback { border-radius: 10px; padding: 1rem; font-size: 14px; font-weight: bold; margin-bottom: 1rem; display: none; }
  .feedback.correct { background: #d4f0e6; color: #0a6645; display: block; }
  .feedback.wrong { background: #fde8e8; color: #c0392b; display: block; }
  button { width: 100%; padding: 14px; border-radius: 10px; font-size: 16px; font-weight: bold; cursor: pointer; border: none; margin-bottom: 0.5rem; }
  #btn-check { background: #1D9E75; color: white; }
  #btn-check:hover { background: #158a62; }
  #btn-check:disabled { background: #ccc; color: #888; cursor: default; }
  #btn-next { background: white; border: 1px solid #ccc; color: #1a1a1a; display: none; }
  #btn-next:hover { background: #f5f5f5; }
  .finish { text-align: center; padding: 2rem 1rem; display: none; }
  .finish-emoji { font-size: 56px; margin-bottom: 1rem; }
  .score-num { font-size: 40px; font-weight: bold; color: #1D9E75; }
  .stars { font-size: 28px; margin: 0.5rem 0 1rem; }
  .finish h2 { font-size: 22px; margin-bottom: 0.5rem; }
  .finish p { color: #555; margin-bottom: 1.5rem; font-size: 15px; }
  #btn-restart { background: #1D9E75; color: white; width: 100%; padding: 14px; border-radius: 10px; font-size: 16px; font-weight: bold; cursor: pointer; border: none; }
</style>
</head>
<body>
<div class="app">

  <h1>Actividad de Logaritmos</h1>

  <div class="header">
    <div class="progress-bg">
      <div class="progress-fill" id="progress"></div>
    </div>
    <div class="lives" id="lives">&#10084;&#10084;&#10084;</div>
  </div>

  <div id="activity">
    <div class="counter" id="counter">Pregunta 1 de 7</div>
    <div class="card">
      <div class="tag" id="tag"></div>
      <div class="question-text" id="question"></div>
      <div class="hint" id="hint"></div>
    </div>
    <div class="feedback" id="feedback"></div>
    <button id="btn-check" disabled onclick="checkAnswer()">Comprobar</button>
    <button id="btn-next" onclick="nextQuestion()">Siguiente</button>
  </div>

  <div class="finish" id="finish">
    <div class="finish-emoji">&#127881;</div>
    <div class="score-num" id="score-num"></div>
    <div class="stars" id="stars"></div>
    <h2>Actividad completada!</h2>
    <p id="finish-msg"></p>
    <button id="btn-restart" onclick="restart()">Reintentar</button>
  </div>

</div>

<script>
var questions = [
  {
    tag: "Definicion",
    parts: ["Si 2 elevado a ", " es igual a 8, entonces log base 2 de 8 = ", ""],
    answers: ["3", "3"],
    hint: "log_a(b) = c significa que a^c = b",
    explanation: "2^3 = 8, por lo tanto log2(8) = 3"
  },
  {
    tag: "Base 10",
    parts: ["log(1000) = ", " porque 10 elevado a ", " es igual a 1000"],
    answers: ["3", "3"],
    hint: "Cuantas veces multiplicas 10 para llegar a 1000?",
    explanation: "10^3 = 1000, entonces log(1000) = 3"
  },
  {
    tag: "Logaritmo natural",
    parts: ["El logaritmo natural se escribe como ", "(x) y su base es el numero ", ""],
    answers: ["ln", "e"],
    hint: "e es aproximadamente 2.718, el numero de Euler",
    explanation: "ln(x) = log_e(x), con base e = 2.718..."
  },
  {
    tag: "Propiedad - producto",
    parts: ["log(A * B) = log(A) ", " log(B)"],
    answers: ["+"],
    hint: "El log del producto se convierte en una operacion entre logaritmos",
    explanation: "El log de un producto es la SUMA de los logaritmos"
  },
  {
    tag: "Propiedad - cociente",
    parts: ["log(A / B) = log(A) ", " log(B)"],
    answers: ["-"],
    hint: "La division dentro del log se convierte en...",
    explanation: "El log de un cociente es la RESTA de los logaritmos"
  },
  {
    tag: "Propiedad - potencia",
    parts: ["log(x^n) = ", " * log(x)"],
    answers: ["n"],
    hint: "El exponente puede salir del log como un factor que multiplica",
    explanation: "El exponente pasa a multiplicar fuera del logaritmo"
  },
  {
    tag: "Valor especial",
    parts: ["Para cualquier base valida: log(", ") = 0"],
    answers: ["1"],
    hint: "Cualquier numero elevado a 0 da como resultado...?",
    explanation: "a^0 = 1 siempre, por eso log(1) = 0"
  }
];

var current = 0;
var lives = 3;
var score = 0;
var checked = false;

function renderQuestion() {
  var q = questions[current];
  checked = false;
  document.getElementById('counter').textContent = "Pregunta " + (current + 1) + " de " + questions.length;
  document.getElementById('tag').textContent = q.tag;
  document.getElementById('hint').textContent = "Pista: " + q.hint;
  document.getElementById('progress').style.width = ((current / questions.length) * 100) + "%";
  document.getElementById('feedback').className = 'feedback';
  document.getElementById('btn-check').style.display = 'block';
  document.getElementById('btn-check').disabled = true;
  document.getElementById('btn-next').style.display = 'none';
  document.getElementById('btn-next').textContent = "Siguiente";
  document.getElementById('btn-next').onclick = nextQuestion;

  var html = "";
  for (var i = 0; i < q.parts.length; i++) {
    html += q.parts[i];
    if (i < q.answers.length) {
      var w = Math.max(70, q.answers[i].length * 20 + 30);
      html += '<input class="blank" id="b' + i + '" style="width:' + w + 'px" autocomplete="off" spellcheck="false" oninput="onInput()" onkeydown="onKey(event)">';
    }
  }
  document.getElementById('question').innerHTML = html;

  var first = document.getElementById('b0');
  if (first) first.focus();
}

function onInput() {
  var q = questions[current];
  var allFilled = true;
  for (var i = 0; i < q.answers.length; i++) {
    var el = document.getElementById('b' + i);
    if (!el || el.value.trim() === '') { allFilled = false; break; }
  }
  document.getElementById('btn-check').disabled = !allFilled;
}

function onKey(e) {
  if (e.key === 'Enter') {
    if (!checked && !document.getElementById('btn-check').disabled) checkAnswer();
    else if (checked) nextQuestion();
  }
}

function checkAnswer() {
  if (checked) return;
  checked = true;
  var q = questions[current];
  var allCorrect = true;
  for (var i = 0; i < q.answers.length; i++) {
    var inp = document.getElementById('b' + i);
    var user = inp.value.trim().toLowerCase();
    var correct = q.answers[i].trim().toLowerCase();
    if (user === correct) {
      inp.classList.add('correct');
    } else {
      inp.classList.add('wrong');
      inp.value = q.answers[i];
      allCorrect = false;
    }
  }
  var fb = document.getElementById('feedback');
  if (allCorrect) {
    score++;
    fb.className = 'feedback correct';
    fb.textContent = "Correcto! " + q.explanation;
  } else {
    lives--;
    updateLives();
    fb.className = 'feedback wrong';
    fb.textContent = "Incorrecto. " + q.explanation;
  }
  document.getElementById('btn-check').style.display = 'none';
  document.getElementById('btn-next').style.display = 'block';
  if (lives <= 0 && current < questions.length - 1) {
    document.getElementById('btn-next').textContent = "Ver resultado";
    document.getElementById('btn-next').onclick = showFinish;
  }
}

function updateLives() {
  var h = "";
  for (var i = 0; i < lives; i++) h += "&#10084;";
  if (lives <= 0) h = "&#128128;";
  document.getElementById('lives').innerHTML = h;
}

function nextQuestion() {
  if (current >= questions.length - 1 || lives <= 0) { showFinish(); return; }
  current++;
  renderQuestion();
}

function showFinish() {
  document.getElementById('activity').style.display = 'none';
  document.getElementById('finish').style.display = 'block';
  document.getElementById('progress').style.width = '100%';
  document.getElementById('score-num').textContent = score + " / " + questions.length;
  var pct = score / questions.length;
  var starCount = pct >= 0.85 ? 3 : pct >= 0.57 ? 2 : 1;
  var s = "";
  for (var i = 0; i < starCount; i++) s += "&#11088;";
  document.getElementById('stars').innerHTML = s;
  var msgs = [
    "Sigue practicando, tu puedes! Repasa las propiedades.",
    "Buen trabajo! Ya dominas los conceptos basicos.",
    "Excelente! Eres un maestro de los logaritmos."
  ];
  document.getElementById('finish-msg').textContent = msgs[starCount - 1];
}

function restart() {
  current = 0; lives = 3; score = 0; checked = false;
  updateLives();
  document.getElementById('activity').style.display = 'block';
  document.getElementById('finish').style.display = 'none';
  renderQuestion();
}

renderQuestion();
</script>
</body>
</html>
