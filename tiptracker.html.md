tiptracker.html  
  
<!DOCTYPE html>  
<html>  
<head>  
<meta name="viewport" content="width=device-width, initial-scale=1.0">  
<title>☁️♡ tip tracker ♡ ☁️</title>  
  
<link rel="manifest" href="manifest.json">  
  
<style>  
body {  
  font-family: Arial, sans-serif;  
  background: linear-gradient(to bottom, #fef6ff, #ffeef9);  
  padding: 20px;  
  color: #555;  
}  
  
h1 {  
  text-align: center;  
  color: #ff8ec7;  
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
}  
</style>  
</head>  
  
<body>  
  
<div id="lockScreen" class="lock-screen">  
  <h1>☁️♡ tip tracker ♡ ☁️</h1>  
  <input type="password" id="passcodeInput" placeholder="enter passcode">  
  <button onclick="unlock()">unlock ☁️</button>  
  <button onclick="setPasscode()">set/change passcode</button>  
</div>  
  
<div id="appContent" style="display:none;">  
  
<h1>☁️♡ tip tracker ♡ ☁️</h1>  
  
<div class="section">  
<input type="number" id="totalSales" placeholder="total sales">  
<input type="number" id="saSales" placeholder="sa sales">  
<input type="number" id="housePercent" value="8" placeholder="house %">  
  
<select id="saPercent">  
  <option value="0">no sa (0%)</option>  
  <option value="1">shared sa (1%)</option>  
  <option value="2">full sa (2%)</option>  
</select>  
  
<input type="number" id="tipsMade" placeholder="total tips made">  
<input type="file" id="photoInput" accept="image/*">  
  
<button onclick="calculate()">calculate ♡</button>  
<button onclick="saveEntry()">save shift ☁️</button>  
  
<div>you keep: $<span id="keepAmount">0.00</span></div>  
</div>  
  
<div class="section">  
<h3>history ♡</h3>  
<div id="history"></div>  
</div>  
  
</div>  
  
<script>  
  
let editIndex = null;  
let lastKeep = 0;  
  
function calculate() {  
  const totalSales = parseFloat(totalSales.value) || 0;  
  const saSales = parseFloat(saSales.value) || 0;  
  const housePercent = parseFloat(housePercent.value) || 0;  
  const saPercent = parseFloat(saPercent.value) || 0;  
  const tipsMade = parseFloat(tipsMade.value) || 0;  
  
  const houseOut = totalSales * (housePercent/100);  
  const saOut = saSales * (saPercent/100);  
  lastKeep = tipsMade - (houseOut + saOut);  
  
  keepAmount.innerText = lastKeep.toFixed(2);  
}  
  
function saveEntry() {  
  const file = photoInput.files[0];  
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
  };  
  
  if (file) reader.readAsDataURL(file);  
  else reader.onload();  
}  
  
function loadEntries() {  
  const entries = JSON.parse(localStorage.getItem("tipEntries")) || [];  
  history.innerHTML = "";  
  
  entries.forEach((e, i) => {  
    const d = new Date(e.date);  
  
    history.innerHTML += `  
      <div class="entry">  
        <strong>${d.toLocaleDateString()}</strong><br>  
        kept: $${e.keep.toFixed(2)}  
        ${e.image ? `<img src="${e.image}">` : ""}  
        <br>  
        <button class="small-btn" onclick="editEntry(${i})">edit</button>  
        <button class="small-btn" onclick="deleteEntry(${i})">delete</button>  
      </div>  
    `;  
  });  
}  
  
function editEntry(i) {  
  const entries = JSON.parse(localStorage.getItem("tipEntries")) || [];  
  editIndex = i;  
  tipsMade.value = entries[i].keep;  
}  
  
function deleteEntry(i) {  
  let entries = JSON.parse(localStorage.getItem("tipEntries")) || [];  
  entries.splice(i,1);  
  localStorage.setItem("tipEntries", JSON.stringify(entries));  
  loadEntries();  
}  
  
function setPasscode() {  
  const code = prompt("set new passcode:");  
  if (code) localStorage.setItem("tipPasscode", code);  
}  
  
function unlock() {  
  const saved = localStorage.getItem("tipPasscode");  
  if (!saved || passcodeInput.value === saved) {  
    lockScreen.style.display = "none";  
    appContent.style.display = "block";  
  } else {  
    alert("wrong passcode ☁️");  
  }  
}  
  
loadEntries();  
  
</script>  
  
</body>  
</html>  
