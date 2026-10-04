
<html>
<head>
<meta charset="UTF-8">
<title>Letters</title>
<style>
  body {
    background-color: forestgreen;
    color: pink;
    font-family: Arial, sans-serif;
    padding: 20px;
  }
  #letterBox {
    width: 100%;
    height: 120px;
  }
  #pinInput {
    width: 120px;
  }
  .letter {
    background: pink;
    color: forestgreen;
    padding: 10px;
    margin: 10px 0;
    border-radius: 6px;
  }
</style>
</head>
<body>

<h1>Send a Letter</h1>

<label>PIN:</label>
<input id="pinInput" type="password" placeholder="Enter PIN">
<br><br>

<textarea id="letterBox" placeholder="Write your letter..."></textarea><br><br>

<button onclick="submitLetter()">Submit Letter</button>

<h2>Letters</h2>
<div id="letters"></div>

<script>
const PIN = "03250904";

// Load saved letters
let letters = JSON.parse(localStorage.getItem("letters") || "[]");
renderLetters();

function submitLetter() {
  const pin = document.getElementById("pinInput").value;
  if (pin !== PIN) {
    alert("Incorrect PIN.");
    return;
  }

  const text = document.getElementById("letterBox").value.trim();
  if (!text) return;

  letters.push({ text, time: new Date().toLocaleString() });
  localStorage.setItem("letters", JSON.stringify(letters));

  renderLetters();
  notify();
  document.getElementById("letterBox").value = "";
}

function renderLetters() {
  const container = document.getElementById("letters");
  container.innerHTML = "";
  letters.forEach(l => {
    const div = document.createElement("div");
    div.className = "letter";
    div.textContent = `${l.time}: ${l.text}`;
    container.appendChild(div);
  });
}

// Browser notifications
function notify() {
  if (Notification.permission === "granted") {
    new Notification("A new letter was sent!");
  }
}

if (Notification.permission !== "granted") {
  Notification.requestPermission();
}
</script>

</body>
</html>
