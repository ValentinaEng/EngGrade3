<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Nouns Game - Grade 3</title>

<!-- Child-friendly font -->
<link href="https://fonts.googleapis.com/css2?family=Comic+Neue:wght@700&display=swap" rel="stylesheet">

<style>
body {
    font-family: 'Comic Neue', cursive;
    text-align: center;
    margin: 0;
    padding: 20px;

    /* Watermark background */
    background: linear-gradient(rgba(255,255,255,0.85), rgba(255,255,255,0.85)),
    url('https://images.unsplash.com/photo-1546182990-dffeafbe841d?auto=format&fit=crop&w=1200&q=60');
    background-size: cover;
    background-position: center;
}

.container {
    max-width: 500px;
    margin: auto;
    background: rgba(255,255,255,0.95);
    padding: 20px;
    border-radius: 20px;
    box-shadow: 0 6px 15px rgba(0,0,0,0.2);
}

h1 {
    color: #ff7043;
    font-size: 28px;
}

.sentence {
    font-size: 22px;
    margin: 20px 0;
}

.options button {
    width: 100%;
    margin: 10px 0;
    padding: 14px;
    font-size: 20px;
    border: none;
    border-radius: 12px;
    background: #4fc3f7;
    color: white;
    cursor: pointer;
    transition: 0.3s;
}

.options button:hover { background: #0288d1; }

.correct { background: #66bb6a !important; }
.wrong { background: #ef5350 !important; }

#restartBtn {
    display: none;
    margin-top: 20px;
    padding: 14px;
    font-size: 20px;
    background: #66bb6a;
    color: white;
    border: none;
    border-radius: 12px;
    cursor: pointer;
}

canvas {
    position: fixed;
    top: 0;
    left: 0;
    pointer-events: none;
}
</style>
</head>

<body>

<div class="container">
    <h1>🌟 Nouns Game 🌟</h1>
    <p><b>Look at the underlined word. Choose the correct answer!</b></p>

    <div class="sentence" id="sentence"></div>

    <div class="options">
        <button id="opt0" onclick="selectAnswer(0)"></button>
        <button id="opt1" onclick="selectAnswer(1)"></button>
    </div>

    <input type="range" id="volume" min="0" max="1" step="0.1" value="1">
    <label>🔊 Volume</label>

    <div id="score"></div>
    <button id="restartBtn" onclick="restartGame()">Restart</button>
</div>

<canvas id="canvas"></canvas>

<script>
// Sounds
const correctSound = new Audio("https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg");
const wrongSound = new Audio("https://actions.google.com/sounds/v1/cartoon/cartoon_boing.ogg");

const volumeControl = document.getElementById("volume");
volumeControl.oninput = () => {
    correctSound.volume = volumeControl.value;
    wrongSound.volume = volumeControl.value;
};

// Questions
const questions = [
{display:"Once, a mighty <u>lion</u> was sleeping under a tree in the jungle.", options:["Masculine","Feminine"], answer:0},
{display:"A tiny mouse named <u>Mimi</u> ran over his paw.", options:["Common","Proper"], answer:1},
{display:"The jungle <u>king</u> Leo woke up with a loud roar.", options:["Masculine","Feminine"], answer:0},
{display:"The little <u>creature</u> Mimi begged, “Please forgive me!”", options:["Common","Proper"], answer:0},
{display:"A kind <u>lioness</u> nearby said, “Let her go.”", options:["Masculine","Feminine"], answer:1},
{display:"<u>Leo</u> showed mercy to the mouse.", options:["Common","Proper"], answer:1},
{display:"The lion king was caught in hunter <u>Ramesh</u>'s net.", options:["Common","Proper"], answer:1},
{display:"<u>Mimi</u> came running to rescue the king.", options:["Common","Proper"], answer:1},
{display:"The sharp <u>mouse</u> Mimi cut the net.", options:["Common","Proper"], answer:0},
{display:"The proud <u>king</u> Leo thanked her and smiled.", options:["Masculine","Feminine"], answer:0}
];

let current = 0, score = 0;

function loadQuestion() {
    let q = questions[current];
    document.getElementById("sentence").innerHTML = q.display;

    let btns = document.querySelectorAll(".options button");
    btns.forEach((b,i)=>{
        b.innerText = q.options[i];
        b.classList.remove("correct","wrong");
        b.disabled = false;
    });
}

function selectAnswer(choice) {
    let q = questions[current];
    let btns = document.querySelectorAll(".options button");

    btns.forEach(b => b.disabled = true);

    if(choice === q.answer){
        btns[choice].classList.add("correct");
        correctSound.play();
        score++;
        launchStars();
    } else {
        btns[choice].classList.add("wrong");
        btns[q.answer].classList.add("correct");
        wrongSound.play();
    }

    setTimeout(()=>{
        current++;
        if(current < questions.length){
            loadQuestion();
        } else {
            endGame();
        }
    },1000);
}

function endGame(){
    document.getElementById("sentence").innerHTML="🎉 Game Over!";
    document.querySelector(".options").style.display="none";
    document.getElementById("score").innerHTML="Score: "+score+"/"+questions.length;
    document.getElementById("restartBtn").style.display="block";
}

function restartGame(){
    current=0; score=0;
    document.querySelector(".options").style.display="block";
    document.getElementById("restartBtn").style.display="none";
    document.getElementById("score").innerHTML="";
    loadQuestion();
}

// ⭐ Star Burst
const canvas=document.getElementById("canvas");
const ctx=canvas.getContext("2d");
canvas.width=window.innerWidth;
canvas.height=window.innerHeight;

let stars=[];

function launchStars(){
    for(let i=0;i<40;i++){
        stars.push({
            x:canvas.width/2,
            y:canvas.height/2,
            size:Math.random()*5+2,
            angle:Math.random()*2*Math.PI,
            speed:Math.random()*4+2
        });
    }
    animateStars();
}

function drawStar(x,y,size){
    ctx.beginPath();
    for(let i=0;i<5;i++){
        ctx.lineTo(
            x + size * Math.cos((18 + i*72) * Math.PI/180),
            y - size * Math.sin((18 + i*72) * Math.PI/180)
        );
        ctx.lineTo(
            x + size/2 * Math.cos((54 + i*72) * Math.PI/180),
            y - size/2 * Math.sin((54 + i*72) * Math.PI/180)
        );
    }
    ctx.closePath();
    ctx.fillStyle=`hsl(${Math.random()*360},100%,60%)`;
    ctx.fill();
}

function animateStars(){
    ctx.clearRect(0,0,canvas.width,canvas.height);

    stars.forEach((s,i)=>{
        s.x += Math.cos(s.angle)*s.speed;
        s.y += Math.sin(s.angle)*s.speed;
        drawStar(s.x,s.y,s.size);
    });

    if(stars.length>0){
        requestAnimationFrame(animateStars);
        stars.splice(0,1);
    }
}

loadQuestion();
</script>

</body>
</html>
