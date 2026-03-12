<!DOCTYPE html>
<html lang="ka">
<head>
<meta charset="UTF-8">
<title>Sanda Wushu Judge Panel</title>

<style>

body{
font-family:Arial;
background:#0f172a;
color:white;
padding:20px;
}

h1{text-align:center}

.line{
display:flex;
justify-content:center;
gap:40px;
background:#1e293b;
padding:15px;
margin:10px 0;
border-radius:10px;
}

.fighter{
width:260px;
}

input{
width:100%;
padding:6px;
margin:3px 0;
}

button{
padding:6px 12px;
margin:3px;
border:none;
border-radius:6px;
cursor:pointer;
}

.win{background:#22c55e}
.lose{background:#ef4444}

.winner{
border:3px solid gold;
}

#leaderboard{
margin-top:40px;
background:#111827;
padding:20px;
border-radius:10px;
}

.regionLine{
display:flex;
justify-content:space-between;
padding:6px 0;
border-bottom:1px solid #333;
}

#fightersList{
margin-top:20px;
}

</style>
</head>

<body>

<h1>🥊 Sanda Wushu Judge Panel</h1>

<div id="fights"></div>

<div id="leaderboard">

<h2>🏆 რეგიონების შედეგები</h2>
<div id="regions"></div>

<h2 id="cupWinner"></h2>

<h2>🥇 გამარჯვებული მებრძოლები</h2>
<div id="fightersList"></div>

</div>

<script>

let fights = JSON.parse(localStorage.getItem("sandaFights"))

if(!fights){

fights=[]

for(let i=0;i<84;i++){

fights.push({
fighter1:{name:"",region:"",result:null},
fighter2:{name:"",region:"",result:null}
})

}

save()

}

function save(){
localStorage.setItem("sandaFights",JSON.stringify(fights))
}

function setWin(line,fighter){

if(fighter===1){
fights[line].fighter1.result="win"
fights[line].fighter2.result="lose"
}else{
fights[line].fighter2.result="win"
fights[line].fighter1.result="lose"
}

save()
render()

}

function setLose(line,fighter){

if(fighter===1){
fights[line].fighter1.result="lose"
fights[line].fighter2.result="win"
}else{
fights[line].fighter2.result="lose"
fights[line].fighter1.result="win"
}

save()
render()

}

function updateName(line,fighter,val){

if(fighter===1){
fights[line].fighter1.name=val
}else{
fights[line].fighter2.name=val
}

save()

}

function updateRegion(line,fighter,val){

if(fighter===1){
fights[line].fighter1.region=val
}else{
fights[line].fighter2.region=val
}

save()

}

function render(){

let container=document.getElementById("fights")
container.innerHTML=""

let regionWins={}
let winners=[]

fights.forEach((fight,i)=>{

let div=document.createElement("div")
div.className="line"

div.innerHTML=`

<div class="fighter ${fight.fighter1.result==='win'?'winner':''}">

<input placeholder="სახელი გვარი"
value="${fight.fighter1.name}"
oninput="updateName(${i},1,this.value)">

<input placeholder="რეგიონი"
value="${fight.fighter1.region}"
oninput="updateRegion(${i},1,this.value)">

<button class="win" onclick="setWin(${i},1)">მოგება</button>
<button class="lose" onclick="setLose(${i},1)">წაგება</button>

</div>

⚔️

<div class="fighter ${fight.fighter2.result==='win'?'winner':''}">

<input placeholder="სახელი გვარი"
value="${fight.fighter2.name}"
oninput="updateName(${i},2,this.value)">

<input placeholder="რეგიონი"
value="${fight.fighter2.region}"
oninput="updateRegion(${i},2,this.value)">

<button class="win" onclick="setWin(${i},2)">მოგება</button>
<button class="lose" onclick="setLose(${i},2)">წაგება</button>

</div>
`

container.appendChild(div)

if(fight.fighter1.result==="win"){
let r=fight.fighter1.region
if(r){
regionWins[r]=(regionWins[r]||0)+1
winners.push(fight.fighter1.name+" ("+r+")")
}
}

if(fight.fighter2.result==="win"){
let r=fight.fighter2.region
if(r){
regionWins[r]=(regionWins[r]||0)+1
winners.push(fight.fighter2.name+" ("+r+")")
}
}

})

renderRegions(regionWins)
renderWinners(winners)

}

function renderRegions(regionWins){

let div=document.getElementById("regions")
div.innerHTML=""

let sorted=Object.entries(regionWins).sort((a,b)=>b[1]-a[1])

let winner=""

sorted.forEach((r,i)=>{

if(i===0){
winner=r[0]
}

let line=document.createElement("div")
line.className="regionLine"

line.innerHTML=`
<span>${r[0]}</span>
<span>${r[1]} გამარჯვება</span>
`

div.appendChild(line)

})

if(winner){
document.getElementById("cupWinner").innerText="🏆 თასის ლიდერი: "+winner
}

}

function renderWinners(list){

let div=document.getElementById("fightersList")
div.innerHTML=""

list.forEach(p=>{

let d=document.createElement("div")
d.innerText=p
div.appendChild(d)

})

}

render()

</script>

</body>
</html>

