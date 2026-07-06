<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Board AI Diamond</title>

<style>
body {
  font-family: Arial;
  background:#111;
  color:#fff;
  text-align:center;
}

/* 棋盤容器 */
.board {
  width: 320px;
  height: 320px;
  margin: 20px auto;
  position: relative;
}

/* 菱形格子 */
.cell {
  width: 45px;
  height: 45px;
  background:#222;
  position:absolute;
  display:flex;
  align-items:center;
  justify-content:center;
  border-radius:8px;
  cursor:pointer;
  font-size:12px;
  transform: rotate(45deg);
}

/* 文字反轉 */
.cell span {
  transform: rotate(-45deg);
  display:block;
}

/* 起點 */
.start {
  background:#4caf50 !important;
}

/* 選取 */
.active {
  background:#ffc107 !important;
}

.result {
  margin-top:10px;
  background:#222;
  padding:10px;
  border-radius:8px;
  width:90%;
  margin-left:auto;
  margin-right:auto;
  text-align:left;
}

.good { color:#4caf50; }
.mid { color:#ffc107; }
.bad { color:#f44336; }
</style>
</head>

<body>

<h2>🧠 Board AI 菱形棋盤</h2>

<div class="board" id="board"></div>

<div class="result" id="result">點選棋格開始分析</div>

<script>

// ===== 骰子機率 =====
const dice={2:1,3:2,4:3,5:4,6:5,7:6,8:5,9:4,10:3,11:2,12:1};

// ===== 獎勵格 =====
const jackpot=[5,15,25,35];
const double=[10,30];

function typeOf(p){
if(jackpot.includes(p)) return "JACKPOT";
if(double.includes(p)) return "DOUBLE";
return "NONE";
}

function prob(r){ return dice[r]/36; }

function getMult(type,step){
if(type==="JACKPOT"){
if(step===7) return 20000;
if(step===6||step===8) return 6000;
if(step>=5&&step<=9) return 1500;
}
if(type==="DOUBLE"){
if(step===7) return 6000;
if(step===6||step===8) return 1500;
}
return 1;
}

// ===== 40格菱形座標（起點在下方）=====
const coords = [
[4,7],[3,6],[5,6],[2,5],[4,5],[6,5],[1,4],[3,4],[5,4],[7,4],
[0,3],[2,3],[4,3],[6,3],[8,3],
[1,2],[3,2],[5,2],[7,2],
[2,1],[4,1],[6,1],
[3,0],[5,0],
[4,-1],
[5,0],[7,0],[9,0],
[6,1],[8,1],
[7,2],[9,2],
[8,3],[10,3],
[9,4],[11,4],
[10,5],[12,5],
[11,6],[13,6]
];

// ===== 建立棋盤 =====
let board=document.getElementById("board");

coords.forEach((c,i)=>{

let div=document.createElement("div");
div.className="cell";

div.style.left=(c[0]*22)+"px";
div.style.top=(c[1]*22+120)+"px";

if(i===0) div.classList.add("start");

div.innerHTML=`<span>${i}</span>`;

div.onclick=()=>select(i,div);

board.appendChild(div);
});

let last=null;

// ===== 點選 =====
function select(pos,el){

document.querySelectorAll(".cell").forEach(c=>c.classList.remove("active"));
el.classList.add("active");

calc(pos);
}

// ===== 分析 =====
function calc(pos){

let html=`📍 目前位置：${pos}<br><br>`;

let best="";

for(let step=2;step<=12;step++){

let next=(pos+step)%40;
let t=typeOf(next);
let p=prob(step);
let mult=getMult(t,step);

let color="bad";
if(mult>=6000) color="good";
else if(mult>=1500) color="mid";

if(mult>1 && !best){
best=`👉 建議 ${mult}倍（+${step} → ${next}｜${t}）`;
}

html+=`
➜ +${step} → ${next} ｜ ${t} ｜ ${(p*100).toFixed(2)}% ｜ ${mult}倍<br>
`;
}

html+=`<br><b>${best||"建議1倍"}</b>`;

document.getElementById("result").innerHTML=html;
}

</script>

</body>
</html>