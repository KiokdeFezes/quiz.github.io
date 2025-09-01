# quiz.github.io
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Quiz - Artes Gráficas</title>
<style>
    body { font-family: Arial, sans-serif; margin: 20px; background-color: #f5f5f5; display: flex; justify-content: center; }
    .quiz-container { background: white; padding: 20px; border-radius: 10px; max-width: 600px; width: 100%; text-align: center; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
    h2 { color: #333; }
    .answers { display: flex; flex-direction: column; align-items: center; gap: 10px; margin-top: 15px; }
    button.answer-btn { padding: 10px 20px; min-width: 250px; border: none; border-radius: 5px; cursor: pointer; background: #007bff; color: white; }
    button.answer-btn:hover { background: #0056b3; }
    #nextBtn { margin-top: 30px; padding: 10px 20px; border: none; border-radius: 5px; background: #28a745; color: white; cursor: pointer; }
    #nextBtn:hover { background: #1e7e34; }
    .hidden { display: none; }
    .result { font-size: 20px; font-weight: bold; margin-top: 20px; }
    input[type="text"] { padding: 8px; width: 60%; border-radius: 5px; border: 1px solid #ccc; margin-top: 10px; }
    .feedback { margin-top: 10px; font-weight: bold; }
</style>
</head>
<body>

<div class="quiz-container">
    <h2 id="question"></h2>
    <div id="answers" class="answers"></div>
    <button id="nextBtn" class="hidden">Próxima</button>
    <div id="result" class="result"></div>
</div>

<script>
function normalize(text) {
    return text.normalize("NFD").replace(/[̀-ͯ]/g, "").toLowerCase().trim();
}

function similarity(s1, s2) {
    s1 = normalize(s1);
    s2 = normalize(s2);
    let matches = 0;
    const len = Math.max(s1.length, s2.length);
    for (let i = 0; i < Math.min(s1.length, s2.length); i++) {
        if (s1[i] === s2[i]) matches++;
    }
    return matches / len;
}

const quizData = [
    {type: "mc", q: "O que é arte gráfica?", a: ["Tipo de pintura a óleo", "Tipo de arte que usa fontes, cores e imagens para comunicação", "Somente desenho digital", "Escultura moderna"], c: 1},
    {type: "mc", q: "Qual habilidade é importante para o designer gráfico?", a: ["Apenas programação", "Somente pintura", "Desenhar e lidar com cores e fontes", "Esculpir"], c: 2},
    {type: "tf", q: "Fontes são desenhos das letras.", c: true},
    {type: "tf", q: "Lettering é a criação de esculturas.", c: false},
    {type: "open", q: "Quem usou fotografia primeiramente para transmitir ideias?", c: "designers russos"},
    {type: "mc", q: "O que é diagramação?", a: ["Processo de cortar imagens", "Organizar elementos gráficos com harmonia", "Ajustar o volume do áudio", "Criar animações"], c: 1},
    {type: "open", q: "Qual é o objetivo principal dos designers ao criar fontes?", c: "criar novas fontes"},
    {type: "tf", q: "Cores podem ser usadas para atrair a atenção do público.", c: true},
    {type: "mc", q: "O que é comunicação visual?", a: ["Escrita de textos", "Uso de ícones e símbolos para comunicação", "Som de anúncios", "Design de som"], c: 1},
    {type: "open", q: "O que é logo?", c: "ícone ou figura que identifica"}
];

let currentQuestion = 0;
let score = 0;

const questionEl = document.getElementById("question");
const answersEl = document.getElementById("answers");
const nextBtn = document.getElementById("nextBtn");
const resultEl = document.getElementById("result");

function loadQuestion() {
    const q = quizData[currentQuestion];
    questionEl.textContent = q.q;
    answersEl.innerHTML = "";
    nextBtn.classList.add("hidden");

    if (q.type === "mc") {
        q.a.forEach((answer, index) => {
            const btn = document.createElement("button");
            btn.textContent = answer;
            btn.classList.add("answer-btn");
            btn.onclick = () => checkAnswer(index);
            answersEl.appendChild(btn);
        });
    } else if (q.type === "tf") {
        ["Verdadeiro", "Falso"].forEach((val, index) => {
            const btn = document.createElement("button");
            btn.textContent = val;
            btn.classList.add("answer-btn");
            btn.onclick = () => checkAnswer(index === 0);
            answersEl.appendChild(btn);
        });
    } else if (q.type === "open") {
        const input = document.createElement("input");
        input.type = "text";
        input.placeholder = "Digite sua resposta...";
        answersEl.appendChild(input);

        const feedback = document.createElement("div");
        feedback.className = "feedback";
        answersEl.appendChild(feedback);

        const btn = document.createElement("button");
        btn.textContent = "Confirmar";
        btn.classList.add("answer-btn");
        btn.onclick = () => {
            const userAnswer = input.value;
            const sim = similarity(userAnswer, q.c);
            if(sim >= 0.6){
                score++;
                input.style.borderColor = "green";
                feedback.textContent = "Correto!";
                feedback.style.color = "green";
            } else {
                input.style.borderColor = "red";
                feedback.textContent = "Incorreto! Resposta correta: " + q.c;
                feedback.style.color = "red";
            }
            Array.from(answersEl.querySelectorAll("button")).forEach(b => b.disabled = true);
            nextBtn.classList.remove("hidden");
        };
        answersEl.appendChild(btn);
    }
}

function checkAnswer(isCorrect) {
    const q = quizData[currentQuestion];
    let correct = false;
    if (typeof isCorrect === "boolean") {
        correct = isCorrect === q.c;
    } else if (typeof isCorrect === "number") {
        correct = isCorrect === q.c;
    }

    if (correct) score++;

    nextBtn.classList.remove("hidden");

    if (q.type === "mc" || q.type === "tf") {
        Array.from(answersEl.children).forEach((btn, index) => {
            btn.disabled = true;
            if ((q.type === "mc" && index === q.c) || (q.type === "tf" && ((q.c && index === 0) || (!q.c && index === 1)))) {
                btn.style.backgroundColor = "green";
            } else {
                btn.style.backgroundColor = "red";
            }
        });
    }
}

nextBtn.onclick = () => {
    currentQuestion++;
    if (currentQuestion < quizData.length) {
        loadQuestion();
    } else {
        showResult();
    }
};

function showResult() {
    questionEl.classList.add("hidden");
    answersEl.classList.add("hidden");
    nextBtn.classList.add("hidden");
    resultEl.textContent = `Você acertou ${score} de ${quizData.length} perguntas!`;
}

loadQuestion();
</script>

</body>
</html>
