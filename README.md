<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Board Adventure AI</title>

<style>
body{
    background:#111;
    color:white;
    font-family:Arial;
    text-align:center;
}

.board{
    position:relative;
    width:360px;
    height:360px;
    margin:20px auto;
}

.cell{
    position:absolute;
    width:42px;
    height:42px;
    background:#333;
    border:1px solid #777;
    border-radius:8px;
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:14px;
    cursor:pointer;
}

.cell:hover{
    transform:scale(1.1);
}

.active{
    outline:3px solid yellow;
}

.jackpot{
    background:#b71c1c;
}

.double{
    background:#1565c0;
}

.safe{
    background:#2e7d32;
}

.start{
    background:#6a1b9a;
}

.result{
    width:90%;
    margin:auto;
    background:#222;
    padding:15px;
    border-radius:10px;
    text-align:left;
}

.good{
    color:#4caf50;
}

.warn{
    color:#ffc107;
}

</style>

</head>

<body>

<h2>🎲 Board Adventure AI</h2>
<p>點選目前所在格</p>

<div class="board" id="board"></div>

<div class="result" id="result">
尚未選擇位置
</div>


<script>

// =================
// 骰子機率
// =================

const dice={
2:1,
3:2,
4:3,
5:4,
6:5,
7:6,
8:5,
9:4,
10:3,
11:2,
12:1
};


// =================
// 獎勵格
// =================

const jackpot=[5,15,25,35];
const double=[10,30];
const safe=[2,8,17,23,33];


// =================
// 40格座標
// 起點0在下面中央
// 順時針
// =================

let pos=[];


// 底部
pos.push([159,318]); //0


// 左下往左
for(let i=1;i<=9;i++){
    pos.push([159-i*35,318]);
}


// 左側往上
for(let i=10;i<=19;i++){
    pos.push([-156,318-(i-9)*35]);
}


// 上方往右
for(let i=20;i<=29;i++){
    pos.push([-156+(i-19)*35,-32]);
}


// 右側往下
for(let i=30;i<=39;i++){
    pos.push([194,3+(i-29)*35]);
}



// 建立棋盤

let board=document.getElementById("board");


for(let i=0;i<40;i++){

    let div=document.createElement("div");

    div.className="cell";

    if(i===0)
        div.classList.add("start");

    if(jackpot.includes(i))
        div.classList.add("jackpot");

    if(double.includes(i))
        div.classList.add("double");

    if(safe.includes(i))
        div.classList.add("safe");


    div.style.left=pos[i][0]+"px";
    div.style.top=pos[i][1]+"px";


    div.innerHTML=i;


    div.onclick=function(){

        document.querySelectorAll(".cell")
        .forEach(x=>x.classList.remove("active"));

        div.classList.add("active");

        analyze(i);

    };


    board.appendChild(div);
}



// =================
// 分析
// =================

function typeOf(p){

    if(jackpot.includes(p))
        return "🔥10倍機會";

    if(double.includes(p))
        return "⭐2倍";

    if(safe.includes(p))
        return "🟢保本";

    return "普通";

}



function analyze(now){

let text="";

text+=`
<h3>目前位置：${now}</h3>
`;

let best="";

for(let step=2;step<=12;step++){

    let next=(now+step)%40;

    let chance=dice[step]/36*100;

    let type=typeOf(next);


    let mult=1;


    if(jackpot.includes(next)){

        if(step===7)
            mult=20000;
        else if(step===6||step===8)
            mult=6000;
        else
            mult=1500;

    }


    if(double.includes(next)){

        if(step===7)
            mult=6000;
        else
            mult=1500;

    }



    if(mult>1 && best==="")
    {
        best=
        `
        <span class="good">
        建議：${mult}倍<br>
        目標：第${next}格<br>
        距離：${step}格
        </span>`;
    }



    text+=`
    <hr>
    ➡ ${step}步 → 第${next}格<br>
    ${type}<br>
    骰點機率：${chance.toFixed(2)}%
    `;
}



text+=`
<hr>
<h3>
${best || "建議維持1倍"}
</h3>
`;


document.getElementById("result").innerHTML=text;


}


</script>

</body>
</html>