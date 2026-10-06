# Job-Sequence
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Job Sequencing with Deadline and Profit Maximization</title>
<link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:wght@400;600;800&display=swap" rel="stylesheet">
<style>
:root{
  --bg:#f4f6f8;--surface:#ffffff;--ink:#14213d;--muted:#5b6678;--line:#d9dee6;
  --accent:#0f766e;--accent-ink:#ffffff;--slot:#e7ecf2;--fill:#f2b134;--fill-ink:#3a2a00;--bad:#b42318;
  box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px);
}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){
  --bg:#0e1522;--surface:#161f30;--ink:#e8edf5;--muted:#9aa6ba;--line:#2a3550;
  --accent:#2dd4bf;--accent-ink:#06201d;--slot:#222d45;--fill:#f2b134;--fill-ink:#2b1f00;--bad:#ff8a7a;}}
:root[data-theme="dark"]{
  --bg:#0e1522;--surface:#161f30;--ink:#e8edf5;--muted:#9aa6ba;--line:#2a3550;
  --accent:#2dd4bf;--accent-ink:#06201d;--slot:#222d45;--fill:#f2b134;--fill-ink:#2b1f00;--bad:#ff8a7a;}
html{scroll-padding-top:env(safe-area-inset-top,0px);scroll-behavior:smooth}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font-family:"Bricolage Grotesque",system-ui,-apple-system,"Segoe UI",sans-serif;line-height:1.6;font-size:17px}
.wrap{max-width:960px;margin:0 auto;padding:0 20px}
nav{position:sticky;top:env(safe-area-inset-top,0px);background:var(--bg);border-bottom:1px solid var(--line);z-index:5}
nav .wrap{display:flex;gap:18px;align-items:center;padding-top:10px;padding-bottom:10px;overflow-x:auto;white-space:nowrap}
nav a{color:var(--muted);text-decoration:none;font-weight:600;font-size:15px}
nav a:hover,nav a:focus-visible{color:var(--accent);outline:none;text-decoration:underline}
nav button{margin-left:auto}
button{font:inherit;cursor:pointer}
button:focus-visible,input:focus-visible{outline:3px solid var(--accent);outline-offset:2px}
.ghost{background:none;border:1px solid var(--line);color:var(--ink);border-radius:8px;padding:4px 10px;font-size:14px}
header.hero{padding:56px 0 36px}
header.hero h1{font-size:clamp(34px,6vw,60px);line-height:1.05;font-weight:800;margin:0 0 16px;letter-spacing:-.02em;max-width:18ch}
header.hero p{max-width:56ch;color:var(--muted);margin:0 0 24px}
.board{display:flex;gap:6px;margin:8px 0 28px;flex-wrap:wrap}
.board span{width:58px;height:58px;border-radius:8px;background:var(--slot);display:grid;place-items:center;font-weight:800;color:var(--muted);position:relative}
.board span.f{background:var(--fill);color:var(--fill-ink)}
.board span small{position:absolute;bottom:2px;right:5px;font-size:10px;font-weight:600;opacity:.7}
.btn{background:var(--accent);color:var(--accent-ink);border:0;border-radius:10px;padding:11px 20px;font-weight:700}
.btn.alt{background:transparent;color:var(--ink);border:1px solid var(--line)}
section{padding:40px 0;border-top:1px solid var(--line)}
h2{font-size:30px;margin:0 0 14px;letter-spacing:-.01em}
h3{margin:0 0 6px;font-size:19px}
.obj{list-style:none;padding:0;margin:0;display:grid;gap:10px}
.obj li{background:var(--surface);border:1px solid var(--line);border-left:5px solid var(--accent);border-radius:8px;padding:12px 16px}
.steps{display:grid;gap:14px;grid-template-columns:repeat(auto-fit,minmax(210px,1fr));margin-top:16px}
.steps div{background:var(--surface);border:1px solid var(--line);border-radius:10px;padding:16px}
.steps b{display:inline-grid;place-items:center;width:28px;height:28px;border-radius:50%;background:var(--accent);color:var(--accent-ink);margin-bottom:8px}
.steps p{margin:0;color:var(--muted);font-size:15px}
.panel{background:var(--surface);border:1px solid var(--line);border-radius:12px;padding:18px;margin-top:16px}
.tablewrap{overflow-x:auto}
table{border-collapse:collapse;width:100%;min-width:360px}
th,td{padding:8px 10px;text-align:left;border-bottom:1px solid var(--line)}
th{color:var(--muted);font-size:14px;font-weight:600}
td input{width:84px;padding:6px 8px;border:1px solid var(--line);border-radius:6px;background:var(--bg);color:var(--ink);font:inherit}
td.name{font-weight:800}
.row-actions{display:flex;gap:10px;flex-wrap:wrap;margin-top:14px}
.timeline{display:flex;gap:6px;margin:6px 0 14px;overflow-x:auto;padding-bottom:6px}
.slot{min-width:76px;flex:1;border-radius:10px;background:var(--slot);padding:8px;text-align:center;min-height:78px}
.slot .t{font-size:12px;color:var(--muted);font-weight:600}
.slot .j{font-size:22px;font-weight:800;min-height:34px}
.slot .p{font-size:13px}
.slot.f{background:var(--fill);color:var(--fill-ink)}
.slot.f .t{color:var(--fill-ink);opacity:.75}
.log{list-style:none;margin:0;padding:0;display:grid;gap:6px;max-height:280px;overflow:auto}
.log li{padding:8px 12px;border-radius:8px;background:var(--bg);font-size:15px}
.log li.ok{border-left:4px solid var(--accent)}
.log li.no{border-left:4px solid var(--bad)}
.total{font-size:22px;font-weight:800;margin-top:12px}
pre{background:#0e1522;color:#e8edf5;padding:16px;border-radius:10px;overflow-x:auto;font-size:14px;line-height:1.5}
.cols{display:grid;gap:16px;grid-template-columns:repeat(auto-fit,minmax(260px,1fr))}
footer{padding:30px 0 50px;color:var(--muted);font-size:14px;border-top:1px solid var(--line)}
.msg{color:var(--bad);min-height:1.4em;font-size:14px;margin:8px 0 0}
</style>
</head>
<body>
<nav><div class="wrap">
  <a href="#home">Home</a><a href="#objectives">Objectives</a><a href="#how">How it works</a><a href="#demo">Try it</a><a href="#code">Code</a>
  <button class="ghost" id="theme" aria-label="Toggle dark mode">Theme</button>
</div></nav>

<header class="hero wrap" id="home">
  <h1>Job Sequencing with Deadline and Profit Maximization</h1>
  <p>A greedy-algorithm project. Each job takes one unit of time, has a deadline and earns a profit. Pick and place jobs so the total profit is as high as possible.</p>
  <div class="board" id="heroBoard" aria-hidden="true"></div>
  <a href="#demo"><button class="btn">Try the scheduler</button></a>
</header>

<main class="wrap">
<section id="objectives">
  <h2>Objectives</h2>
  <ul class="obj">
    <li>To schedule jobs within their deadlines.</li>
    <li>To maximize the total profit from selected jobs.</li>
    <li>To sort jobs according to their profit.</li>
    <li>To use greedy selection for optimal job placement.</li>
    <li>To demonstrate the practical use of scheduling algorithms.</li>
  </ul>
</section>

<section id="how">
  <h2>How the greedy method works</h2>
  <p>Greedy means taking the best choice available right now. Here, the best choice is the job with the highest profit, placed as late as its deadline allows so earlier slots stay free for other jobs.</p>
  <div class="steps">
    <div><b>1</b><h3>Sort</h3><p>Order all jobs by profit, highest first.</p></div>
    <div><b>2</b><h3>Find a slot</h3><p>For each job, look for a free time slot from its deadline back toward slot 1.</p></div>
    <div><b>3</b><h3>Place or skip</h3><p>Take the latest free slot. If none is free, skip the job.</p></div>
    <div><b>4</b><h3>Add up</h3><p>The total profit is the sum of all placed jobs.</p></div>
  </div>
  <div class="cols panel">
    <div><h3>Time complexity</h3><p>O(n²) with the simple slot search. About O(n log n) when using a disjoint-set structure.</p></div>
    <div><h3>Space complexity</h3><p>O(n) for the slot array.</p></div>
    <div><h3>Where it is used</h3><p>Task scheduling, assignment of orders to delivery windows, and CPU job queues with deadlines.</p></div>
  </div>
</section>

<section id="demo">
  <h2>Try it yourself</h2>
  <p>Edit the jobs, add your own, then run the algorithm all at once or one job at a time.</p>
  <div class="panel">
    <div class="tablewrap"><table>
      <thead><tr><th>Job</th><th>Deadline</th><th>Profit</th><th></th></tr></thead>
      <tbody id="jobs"></tbody>
    </table></div>
    <div class="row-actions">
      <button class="btn alt" id="add">Add job</button>
      <button class="btn alt" id="sample">Reset example</button>
      <button class="btn" id="run">Run all</button>
      <button class="btn alt" id="step">Next step</button>
    </div>
    <p class="msg" id="msg" role="alert"></p>
  </div>
  <div class="panel">
    <h3>Time slots</h3>
    <div class="timeline" id="timeline"><p style="color:var(--muted);margin:0">Run the algorithm to see slots fill up.</p></div>
    <h3>What happened</h3>
    <ul class="log" id="log"></ul>
    <div class="total" id="total"></div>
  </div>
</section>

<section id="code">
  <h2>The algorithm in JavaScript</h2>
<pre>function jobSequencing(jobs) {
  jobs.sort((a, b) =&gt; b.profit - a.profit);       // 1. sort by profit
  const maxD = Math.max(...jobs.map(j =&gt; j.deadline));
  const slots = new Array(maxD + 1).fill(null);   // slots 1..maxD
  let total = 0;

  for (const job of jobs) {
    for (let t = job.deadline; t &gt;= 1; t--) {     // 2. latest free slot
      if (slots[t] === null) {
        slots[t] = job;                           // 3. place it
        total += job.profit;
        break;
      }
    }
  }
  return { slots, total };                        // 4. total profit
}</pre>
</section>
</main>

<footer><div class="wrap">Project: Greedy concept, Job Sequencing. Built with HTML, CSS and JavaScript.</div></footer>

<script>
const SAMPLE=[["a",2,100],["b",1,19],["c",2,27],["d",1,25],["e",3,15]];
let jobs=[],steps=[],shown=0,slots=[],maxD=0;
const $=id=>document.getElementById(id);

function loadSample(){jobs=SAMPLE.map(([n,d,p])=>({n,d,p}));renderJobs();resetRun();}
function renderJobs(){
  $("jobs").innerHTML=jobs.map((j,i)=>`<tr>
    <td class="name">${j.n}</td>
    <td><input type="number" min="1" step="1" value="${j.d}" data-i="${i}" data-k="d" aria-label="Deadline of job ${j.n}"></td>
    <td><input type="number" min="0" step="1" value="${j.p}" data-i="${i}" data-k="p" aria-label="Profit of job ${j.n}"></td>
    <td><button class="ghost" data-del="${i}" aria-label="Remove job ${j.n}">Remove</button></td></tr>`).join("");
}
$("jobs").addEventListener("input",e=>{
  const i=e.target.dataset.i;if(i===undefined)return;
  jobs[i][e.target.dataset.k]=parseInt(e.target.value,10);resetRun();
});
$("jobs").addEventListener("click",e=>{
  const i=e.target.dataset.del;if(i===undefined)return;
  jobs.splice(+i,1);renderJobs();resetRun();
});
$("add").onclick=()=>{
  let n=jobs.length,name;
  do{name=String.fromCharCode(97+(n%26))+(n>=26?Math.floor(n/26):"");n++}while(jobs.some(j=>j.n===name));
  jobs.push({n:name,d:2,p:10});renderJobs();resetRun();
};
$("sample").onclick=loadSample;

function valid(){
  if(!jobs.length){$("msg").textContent="Add at least one job.";return false}
  if(jobs.some(j=>!Number.isInteger(j.d)||j.d<1||!Number.isInteger(j.p)||j.p<0)){
    $("msg").textContent="Every deadline must be a whole number of 1 or more, and every profit a whole number of 0 or more.";return false}
  $("msg").textContent="";return true;
}
function resetRun(){
  steps=[];shown=0;slots=[];$("log").innerHTML="";$("total").textContent="";
  $("timeline").innerHTML='<p style="color:var(--muted);margin:0">Run the algorithm to see slots fill up.</p>';
}
function compute(){
  const sorted=[...jobs].sort((a,b)=>b.p-a.p);
  maxD=Math.max(...jobs.map(j=>j.d));
  const s=new Array(maxD+1).fill(null);steps=[];
  for(const j of sorted){
    let placed=0;
    for(let t=j.d;t>=1;t--){ if(s[t]===null){s[t]=j;placed=t;break} }
    steps.push({j,t:placed,snap:[...s]});
  }
  shown=0;
}
function draw(){
  const cur=shown?steps[shown-1].snap:new Array(maxD+1).fill(null);
  $("timeline").innerHTML=cur.slice(1).map((j,i)=>`<div class="slot ${j?"f":""}">
    <div class="t">Slot ${i+1}</div><div class="j">${j?j.n:"–"}</div><div class="p">${j?"profit "+j.p:"free"}</div></div>`).join("");
  $("log").innerHTML=steps.slice(0,shown).map(({j,t})=>t
    ?`<li class="ok">Job <b>${j.n}</b> (deadline ${j.d}, profit ${j.p}) placed in slot ${t}.</li>`
    :`<li class="no">Job <b>${j.n}</b> (deadline ${j.d}, profit ${j.p}) skipped. Slots 1 to ${j.d} are full.</li>`).join("");
  const sum=steps.slice(0,shown).reduce((a,s)=>a+(s.t?s.j.p:0),0);
  const done=shown===steps.length;
  const names=cur.slice(1).filter(Boolean).map(j=>j.n).join(", ");
  $("total").textContent=shown?`Total profit so far: ${sum}`+(done?`. Scheduled jobs: ${names}.`:""):"";
}
$("run").onclick=()=>{if(!valid())return;compute();shown=steps.length;draw()};
$("step").onclick=()=>{
  if(!valid())return;
  if(!steps.length||shown>=steps.length){compute()}
  shown++;draw();
};

/* hero decoration: sample schedule */
(function(){
  const h=[["c",1],["a",2],["e",3]];
  $("heroBoard").innerHTML=h.map(([n,t])=>`<span class="f">${n}<small>t${t}</small></span>`).join("")+
    '<span>–<small>t4</small></span><span>–<small>t5</small></span>';
})();

/* theme */
$("theme").onclick=()=>{
  const r=document.documentElement,dark=getComputedStyle(r).getPropertyValue("--bg").trim()==="#0e1522";
  r.setAttribute("data-theme",dark?"light":"dark");
};
loadSample();
</script>
</body>
</html>
