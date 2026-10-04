<!DOCTYPE html> (This is unfamiliar and  it's not perfect, but this is our gardern letters, lovely) 
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Letters</title>
<style>
  body {
    background-color: forestgreen;
    color: pink;
    font-family: Arial, sans-serif;
    padding: 20px;
    /* Keeps the fixed footer from covering form contents when scrolling to the bottom */
    margin-bottom: 270px; 
  }
  #letterBox {
    width: 100%;
    height: 120px;
  }
  #pinInput {
    width: 120px;
  }
  
  /* Persistent Bottom Bar Styling */
  .letters-footer {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    background-color: #ffe4e1; /* Slightly adjusted for better text contrast if needed */
    border-top: 4px solid pink;
    padding: 15px 20px;
    box-shadow: 0 -4px 15px rgba(0, 0, 0, 0.3);
    z-index: 9999;
    max-height: 220px;
    box-sizing: border-box;
    overflow-y: auto; /* Allows scrolling inside the container if there are many letters */
  }

  .letters-footer h2 {
    margin-top: 0;
    margin-bottom: 10px;
    color: forestgreen;
    font-size: 1.5rem;
  }

  .letter {
    background: pink;
    color: forestgreen;
    padding: 10px;
    margin: 8px 0;
    border-radius: 6px;
  }
  .loading {
    color: darkgoldenrod;
    font-style: italic;
  }
</style>
</head>
<body>

<main>
  <h1>Send a Letter</h1>

  <label for="pinInput">PIN:</label><br>
  <input id="pinInput" type="password" placeholder="Enter PIN">
  <br><br>

  <label for="letterBox">Your Letter:</label><br>
  <textarea id="letterBox" placeholder="Write your letter..."></textarea><br><br>

  <button onclick="submitLetter()">Submit Letter</button>
</main>

<!-- Semantically correct & accessible landmarks for screen readers -->
<footer class="letters-footer" aria-labelledby="letters-heading">
  <h2 id="letters-heading">Letters</h2>
  <div id="letters" class="loading">Loading letters...</div>
</footer>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js"></script>

<script>
const PIN = "03250904";

// Firebase configuration
const firebaseConfig = {
  apiKey: "AIzaSyC5XqI_nKNrlHqKqR_K9xJ84AqUVdGLarU",
  authDomain: "letters-7a9ef.firebaseapp.com",
  databaseURL: "https://letters-7a9ef-default-rtdb.firebaseio.com",
  projectId: "letters-7a9ef",
  storageBucket: "letters-7a9ef.appspot.com",
  messagingSenderId: "696088747559",
  appId: "1:696088747559:web:1f3f8a5c5e8b2d4c9f6e1a"
};

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

// Load and listen for letters in real-time
db.ref("letters").on("value", (snapshot) => {
  const data = snapshot.val();
  let letters = [];
  
  if (data) {
    // Convert Firebase object to array
    letters = Object.keys(data).map(key => ({
      id: key,
      ...data[key]
    }));
    // Sort by timestamp (newest first)
    letters.sort((a, b) => new Date(b.time) - new Date(a.time));
  }
  
  renderLetters(letters);
});

function submitLetter() {
  const pin = document.getElementById("pinInput").value;
  if (pin !== PIN) {
    alert("Incorrect PIN.");
    return;
  }

  const text = document.getElementById("letterBox").value.trim();
  if (!text) {
    alert("Please write a letter.");
    return;
  }

  // Push letter to Firebase with timestamp
  db.ref("letters").push({
    text: text,
    time: new Date().toLocaleString(),
    timestamp: firebase.database.ServerValue.TIMESTAMP
  }).then(() => {
    renderNotification("Letter submitted!");
    document.getElementById("letterBox").value = "";
    document.getElementById("pinInput").value = "";
  }).catch((error) => {
    alert("Error submitting letter: " + error.message);
  });
}

function renderLetters(letters) {
  const container = document.getElementById("letters");
  container.className = ""; // Remove loading class
  container.innerHTML = "";
  
  if (letters.length === 0) {
    container.innerHTML = "<p style='color: forestgreen;'>No letters yet...</p>";
    return;
  }
  
  letters.forEach(l => {
    const div = document.createElement("div");
    div.className = "letter";
    div.textContent = `${l.time}: ${l.text}`;
    container.appendChild(div);
  });
}

// Browser notifications
function renderNotification(message) {
  if ("Notification" in window && Notification.permission === "granted") {
    new Notification(message);
  }
}

if ("Notification" in window && Notification.permission !== "granted") {
  Notification.requestPermission();
}
</script>

</body>
</html>
