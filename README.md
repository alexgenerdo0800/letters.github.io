<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Private Chat Room</title>
<style>
  body {
    background-color: forestgreen;
    color: pink;
    font-family: Arial, sans-serif;
    padding: 20px;
    margin: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
    min-height: 100vh;
    box-sizing: border-box;
  }

  h1 {
    margin-top: 10px;
    font-size: 1.8rem;
    text-align: center;
  }

  /* Entrance Gate Styling */
  #gatekeeperArea {
    background: rgba(0, 0, 0, 0.2);
    padding: 20px;
    border-radius: 8px;
    width: 100%;
    max-width: 400px;
    box-sizing: border-box;
    margin-bottom: 20px;
    border: 1px solid pink;
  }

  .form-group {
    margin-bottom: 15px;
  }

  .form-group label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }

  input[type="text"], input[type="password"] {
    width: 100%;
    padding: 10px;
    border: 2px solid pink;
    border-radius: 6px;
    background: #ffe4e1;
    color: forestgreen;
    font-size: 1rem;
    box-sizing: border-box;
  }

  button {
    background-color: pink;
    color: forestgreen;
    border: none;
    padding: 12px 20px;
    font-size: 1rem;
    font-weight: bold;
    border-radius: 6px;
    cursor: pointer;
    width: 100%;
    transition: background 0.2s;
  }

  button:hover {
    background-color: #ffb6c1;
  }

  /* Modern Chat Interface Box */
  #chatArea {
    display: none; /* Hidden strictly until code matches */
    width: 100%;
    max-width: 500px;
    background: #ffe4e1;
    border-radius: 12px;
    border: 3px solid pink;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
    overflow: hidden;
  }

  .chat-header {
    background-color: pink;
    color: forestgreen;
    padding: 15px;
    font-weight: bold;
    font-size: 1.1rem;
    border-bottom: 2px solid forestgreen;
    text-align: center;
  }

  /* Scrolling Feed Track */
  #messagesContainer {
    height: 350px;
    overflow-y: auto;
    padding: 15px;
    display: flex;
    flex-direction: column;
    gap: 10px;
    background-color: #fff0f5;
  }

  .message-bubble {
    max-width: 75%;
    padding: 10px 14px;
    border-radius: 12px;
    font-size: 0.95rem;
    line-height: 1.4;
    word-wrap: break-word;
  }

  /* Alignment Separation */
  .msg-sent {
    background-color: forestgreen;
    color: pink;
    align-self: flex-end;
    border-bottom-right-radius: 2px;
  }

  .msg-received {
    background-color: pink;
    color: forestgreen;
    align-self: flex-start;
    border-bottom-left-radius: 2px;
  }

  .meta-info {
    font-size: 0.75rem;
    display: block;
    margin-bottom: 3px;
    opacity: 0.8;
    font-weight: bold;
  }

  .timestamp {
    font-size: 0.7rem;
    display: block;
    text-align: right;
    margin-top: 5px;
    opacity: 0.7;
  }

  .input-area {
    display: flex;
    padding: 10px;
    background: #ffe4e1;
    border-top: 2px solid pink;
    gap: 10px;
  }

  #messageBox {
    flex: 1;
    padding: 10px;
    border: 2px solid pink;
    border-radius: 6px;
    font-size: 1rem;
    color: forestgreen;
  }

  .input-area button {
    width: auto;
  }
</style>
</head>
<body>

<main>
  <h1>Private Chat Space</h1>

  <!-- Gatekeeper Verification Block -->
  <div id="gatekeeperArea">
    <div class="form-group">
      <label for="usernameInput">Your Display Name:</label>
      <input id="usernameInput" type="text" placeholder="e.g., Alex">
    </div>
    <div class="form-group">
      <label for="roomCodeInput">Private Chat Key Code:</label>
      <input id="roomCodeInput" type="password" placeholder="Enter specific key code">
    </div>
    <button onclick="unlockAndJoin()">Verify & Open Chat</button>
  </div>

  <!-- The Live Messaging Interface -->
  <div id="chatArea">
    <div class="chat-header">🔒 Protected Chat Room</div>
    <div id="messagesContainer">
      <p style="color: forestgreen; font-style: italic; text-align: center;">Syncing stream logs...</p>
    </div>
    <div class="input-area">
      <input id="messageBox" type="text" placeholder="Type a message..." onkeydown="if(event.key === 'Enter') sendMessage()">
      <button onclick="sendMessage()">Send</button>
    </div>
  </div>
</main>

<script src="https://gstatic.com"></script>
<script src="https://gstatic.com"></script>

<script>
// Hardcoded authorization key requirement
const SECRET_KEY = "03250904";

// Firebase configuration
const firebaseConfig = {
  apiKey: "AIzaSyC5XqI_nKNrlHqKqR_K9xJ84AqUVdGLarU",
  authDomain: "://firebaseapp.com",
  databaseURL: "https://firebaseio.com",
  projectId: "letters-7a9ef",
  storageBucket: "://appspot.com",
  messagingSenderId: "696088747559",
  appId: "1:696088747559:web:1f3f8a5c5e8b2d4c9f6e1a"
};

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

let currentUsername = "";
let chatListenerRef = null;

function unlockAndJoin() {
  const nameInput = document.getElementById("usernameInput").value.trim();
  const codeInput = document.getElementById("roomCodeInput").value.trim();

  if (!nameInput) {
    alert("Please type a display name first.");
    return;
  }

  // Exact lock validation layer check
  if (codeInput !== SECRET_KEY) {
    alert("Invalid Key Code. Access Denied.");
    return;
  }

  currentUsername = nameInput;

  // Visual layout element transitions
  document.getElementById("gatekeeperArea").style.display = "none";
  document.getElementById("chatArea").style.display = "block";

  // Connects data storage explicitly to an isolated private workspace inside your DB named after the code
  chatListenerRef = db.ref("rooms/" + SECRET_KEY + "/messages");
  
  listenForLiveFeed();
}

function listenForLiveFeed() {
  chatListenerRef.on("value", (snapshot) => {
    const data = snapshot.val();
    const container = document.getElementById("messagesContainer");
    container.innerHTML = ""; 

    if (!data) {
      container.innerHTML = "<p style='color: forestgreen; font-style: italic; text-align: center;'>No messages here yet. Type down below to chat!</p>";
      return;
    }

    // Convert data record mapping trees into lists
    const messageList = Object.keys(data).map(key => ({
      id: key,
      ...data[key]
    }));

    // Order timeline oldest down to standard modern newest scroll layout
    messageList.sort((a, b) => a.timestamp - b.timestamp);

    messageList.forEach(msg => {
      const bubble = document.createElement("div");
      const isMe = msg.sender === currentUsername;
      
      bubble.className = "message-bubble " + (isMe ? "msg-sent" : "msg-received");
      bubble.innerHTML = `
        <span class="meta-info">${isMe ? "You" : msg.sender}</span>
        <div>${escapeHTML(msg.text)}</div>
        <span class="timestamp">${msg.time}</span>
      `;
      
      container.appendChild(bubble);
    });

    // Instant viewport bottom anchor scroll lock loop execution
    container.scrollTop = container.scrollHeight;
  });
}

function sendMessage() {
  const inputElement = document.getElementById("messageBox");
  const text = inputElement.value.trim();

  if (!text) return;

  chatListenerRef.push({
    sender: currentUsername,
    text: text,
    time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
    timestamp: firebase.database.ServerValue.TIMESTAMP
  }).then(() => {
    inputElement.value = ""; 
  }).catch((error) => {
    alert("Message failed to send: " + error.message);
  });
}

function escapeHTML(str) {
  return str.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;");
}
</script>

</body>
</html>
