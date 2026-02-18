<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>☁️♡ Tip Tracker ♡ ☁️</title>

<link rel="icon" href="dog-icon.png" type="image/png">
<link rel="apple-touch-icon" href="dog-icon.png">
<link rel="manifest" href="manifest.json">

<style>
body {
  font-family: Arial, sans-serif;
  background: linear-gradient(to bottom, #fef6ff, #ffeef9);
  padding: 20px;
  color: #555;
}

/* New style for the icon logo */
.app-logo {
  width: 80px;
  height: 80px;
  border-radius: 50%; /* Makes it a cute circle */
  border: 4px solid #ffc2e3;
  margin-bottom: 10px;
  object-fit: cover;
}

h1 {
  text-align: center;
  color: #ff8ec7;
  margin-top: 5px;
}

.header-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
}

.section {
  background: #ffffffee;
  padding: 16px;
  margin-top: 16px;
  border-radius: 20px;
  box-shadow: 0 8px 16px rgba(255,182,217,0.25);
}

input, select {
  width: 100%;
  padding: 10px;
  margin: 6px 0;
  border-radius: 14px;
  border: 1px solid #ffd6ec;
  box-sizing: border-box; /* Fixes width issues */
}

button {
  width: 100%;
  padding: 12px;
  margin-top: 8px;
  border-radius: 18px;
  border: none;
  background: #ffc2e3;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

.small-btn {
  width: auto;
  padding: 6px 10px;
  margin-top: 6px;
  margin-right: 6px;
  font-size: 12px;
}

.entry {
  margin-top: 12px;
  padding: 12px;
  background: #fff;
  border-radius: 16px;
  box-shadow: 0 4px 10px rgba(255,182,217,0.15);
}

.entry img {
  width: 100%;
  margin-top: 8px;
  border-radius: 14px;
}

.lock-screen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(to bottom, #fef6ff, #ffeef9);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  z-index: 100;
}
</style>
</head>

<body>

<div id="lockScreen" class="lock-screen">
  <img src="dog-icon.png" class="app-logo" alt="logo">
  <h1>☁️♡ Tip Tracker ♡ ☁️</h1>
  <div style="width: 80%;">
      <input type="password" id="passcodeInput" placeholder="enter passcode">
      <button onclick="unlock()">unlock ☁️</button>
      <button onclick="setPasscode()" style="background: none; color: #ff8ec7; font-size: 12px;">set/change passcode</button>
  </div>
</div>

<div id="appContent" style="display:none;">

<div class="header-container">
    <img src="dog-icon.png" class="app-logo" alt="logo">
    <h1>☁️♡ Tip Tracker ♡ ☁️</h1>
</div>

<div class="section">
<label>total sales</label>
<input type="number" id="totalSalesInput" placeholder="0.00">
<label>sa sales</label>
<input type="number" id="saSalesInput" placeholder="0.00">
<label>house %</label>
<input type="number" id="housePercentInput" value="8">

<label>sa %</label>
<select id="saPercentInput">
  <option value="0">no sa (0%)</option>
  <option value="1">shared sa (1%)</option>
  <option value="2">full sa (2%)</option>
</select>

<label>total tips made</label>
<input type="number" id="tipsMadeInput" placeholder="0.00">
<input type="file" id="photoInput" accept="image/*">

<button onclick="calculate()">calculate ♡</button>
<button onclick="saveEntry()">save shift ☁️</button>

<div style="margin-top:15px; text-align:center; font-weight:bold;">you keep: $<span id="keepAmount">0.00</span></div>
</div>

<div class="section">
<h3>history ♡</h3>
<div id="history"></div>
</div>

</div>

<script>
let editIndex = null;
let lastKeep = 0;

// Tip: Fixed variable names to match IDs to avoid browser bugs
function calculate() {
  const ts = parseFloat(document.getElementById('totalSalesInput').value) || 0;
  const ss = parseFloat(document.getElementById('saSalesInput').value) || 0;
  const hp = parseFloat(document.getElementById('housePercentInput').value) || 0;
  const sp = parseFloat(document.getElementById('saPercentInput').value) || 0;
  const tm = parseFloat(document.getElementById('tipsMadeInput').value) || 0;

  const houseOut = ts * (hp/100);
  const saOut = ss * (sp/100);
  lastKeep = tm - (houseOut + saOut);

  document.getElementById('keepAmount').innerText = lastKeep.toFixed(2);
}

function saveEntry() {
  const file = document.getElementById('photoInput').files[0];
  const reader = new FileReader();

  reader.onload = function() {
    let entries = JSON.parse(localStorage.getItem("tipEntries")) || [];

    const entry = {
      date: new Date().toISOString(),
      keep: lastKeep,
      image: reader.result || null
    };

    if (editIndex !== null) {
      entries[editIndex] = entry;
      editIndex = null;
    } else {
      entries.push(entry);
    }

    localStorage.setItem("tipEntries", JSON.stringify(entries));
    loadEntries();
    alert("Shift saved! ☁️");
  };

  if (file) reader.readAsDataURL(file);
  else reader.onload();
}

function loadEntries() {
  const entries = JSON.parse(localStorage.getItem("tipEntries")) || [];
  const historyDiv = document.getElementById('history');
  historyDiv.innerHTML = "";

  entries.slice().reverse().forEach((e, i) => {
    const d = new Date(e.date);
    const actualIndex = entries.length - 1 - i;

    historyDiv.innerHTML += `
      <div class="entry">
        <strong>${d.toLocaleDateString()}</strong><br>
        kept: $${e.keep.toFixed(2)}
        ${e.image ? `<img src="${e.image}">` : ""}
        <br>
        <button class="small-btn" onclick="editEntry(${actualIndex})">edit</button>
        <button class="small-btn" onclick="deleteEntry(${actualIndex})">delete</button>
      </div>
    `;
  });
}

function editEntry(i) {
  const entries = JSON.parse(localStorage.getItem("tipEntries")) || [];
  editIndex = i;
  document.getElementById('tipsMadeInput').value = entries[i].keep;
  window.scrollTo(0,0);
}

function deleteEntry(i) {
  if(confirm("Delete this entry?")) {
    let entries = JSON.parse(localStorage.getItem("tipEntries")) || [];
    entries.splice(i,1);
    localStorage.setItem("tipEntries", JSON.stringify(entries));
    loadEntries();
  }
}

function setPasscode() {
  const code = prompt("set new passcode:");
  if (code) localStorage.setItem("tipPasscode", code);
}

function unlock() {
  const saved = localStorage.getItem("tipPasscode");
  const input = document.getElementById('passcodeInput').value;
  if (!saved || input === saved) {
    document.getElementById('lockScreen').style.display = "none";
    document.getElementById('appContent').style.display = "block";
  } else {
    alert("wrong passcode ☁️");
  }
}

loadEntries();
</script>

</body>
</html>
