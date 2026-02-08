<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Clinic Queue MVP</title>
<style>
body { font-family: Arial; background:#f2f4f8; text-align:center; }
.card { background:white; padding:20px; margin:10px; border-radius:12px; }
.big { font-size:60px; }
.btn { padding:20px; margin:8px; font-size:22px; border:none; border-radius:10px; width:140px;}
.add{background:#2ecc71;color:white;}
.next{background:#3498db;color:white;}
.skip{background:#f39c12;color:white;}
.done{background:#e74c3c;color:white;}
.start{background:#7f8c8d;color:white;width:300px;}
.recall{background:#9b59b6;color:white;}
input{padding:12px;font-size:18px;width:220px;border-radius:8px;border:1px solid #ccc;}
</style>
</head>

<body>

<h1>Clinic Queue System</h1>

<div class="card">
<h2 id="status">STATUS: HOLD</h2>
</div>

<div class="card">
<h3>NOW SERVING</h3>
<div class="big" id="now">--</div>
</div>

<div class="card">
<h3>NEXT TOKENS</h3>
<div id="next"></div>
</div>

<div class="card">
<h3>SKIPPED</h3>
<div id="skipped"></div>
</div>

<input id="phoneInput" placeholder="Patient Phone">

<br><br>

<button class="btn add" onclick="addToken()">ADD</button>
<button class="btn next" onclick="nextToken()">NEXT</button>
<button class="btn skip" onclick="skipToken()">SKIP</button>
<button class="btn done" onclick="doneToken()">DONE</button>
<br>
<button class="btn recall" onclick="recallToken()">RECALL</button>
<br>
<button class="btn start" onclick="startQueue()">START QUEUE</button>

<script>
let queue = [];
let skipped = [];
let tokenNum = 1;
let running = false;

function render(){
  document.getElementById("now").innerText = queue[0]?.token || "--";
  document.getElementById("next").innerText = queue.slice(1,4).map(x=>x.token).join(", ");
  document.getElementById("skipped").innerText = skipped.map(x=>x.token).join(", ");
}

function fakeSMS(phone,msg){
  console.log("SMS to",phone,msg);
  alert("SMS to "+phone+" : "+msg);
}

function addToken(){
  let phone = document.getElementById("phoneInput").value;
  if(!phone) return alert("Enter phone");
  let token = "A"+String(tokenNum++).padStart(2,"0");
  let obj = {token,phone};
  queue.push(obj);
  fakeSMS(phone,"Your token is "+token);
  render();
}

function startQueue(){
  running = true;
  document.getElementById("status").innerText="STATUS: RUNNING";
  render();
}

function nextToken(){
  if(!running) return alert("Queue on HOLD");
  if(queue.length>1){
    fakeSMS(queue[1].phone,"Your turn is coming soon");
  }
  render();
}

function doneToken(){
  if(queue.length){
    queue.shift();
    render();
  }
}

function skipToken(){
  if(queue.length){
    let t = queue.shift();
    skipped.push(t);
    queue.splice(3,0,t);
    render();
  }
}

function recallToken(){
  if(skipped.length==0) return alert("No skipped tokens");
  let t = skipped.shift();
  queue.splice(1,0,t);
  fakeSMS(t.phone,"You are recalled. Please come.");
  render();
}
</script>

</body>
</html>
