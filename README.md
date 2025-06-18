<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Conversations With Strangers</title>
  <link href="https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Roboto Mono', monospace;
      background: linear-gradient(to bottom right, #e0f7fa, #ffe0f0);
      margin: 0;
      padding: 40px 20px;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-height: 100vh;
    }

    h1 {
      font-size: 2.2em;
      color: #222;
      margin-bottom: 8px;
      text-shadow: 1px 1px 2px rgba(255, 255, 255, 0.7);
    }

    p {
      color: #444;
      font-size: 14px;
      margin-bottom: 25px;
      text-shadow: 1px 1px 2px rgba(255, 255, 255, 0.5);
    }

    .card {
      background: rgba(255, 255, 255, 0.3);
      backdrop-filter: blur(15px);
      -webkit-backdrop-filter: blur(15px);
      padding: 25px;
      border-radius: 20px;
      box-shadow: 0 8px 30px rgba(0, 0, 0, 0.15);
      max-width: 500px;
      width: 100%;
      margin-top: 20px;
      display: flex;
      flex-direction: column;
      align-items: center;
      transition: transform 0.2s ease, box-shadow 0.2s ease;
    }

    .card:focus-within, .card:hover {
      transform: scale(1.015);
      box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
    }

    textarea, select {
      width: 100%;
      padding: 15px;
      margin-top: 10px;
      border: 1px solid #ccc;
      border-radius: 10px;
      resize: none;
      font-size: 16px;
      transition: border-color 0.3s ease, box-shadow 0.3s ease;
      background-color: rgba(255, 255, 255, 0.85);
    }

    textarea:focus, select:focus {
      border-color: #00796b;
      box-shadow: 0 0 8px rgba(0, 121, 107, 0.5);
      outline: none;
    }

    button {
      margin-top: 15px;
      padding: 12px 20px;
      background-color: #00796b;
      color: white;
      border: none;
      border-radius: 10px;
      font-size: 16px;
      cursor: pointer;
      transition: background-color 0.3s ease, transform 0.2s ease;
    }

    button:hover, button:focus {
      background-color: #004d40;
      transform: translateY(-2px);
      outline: none;
    }

    #randomMessage {
      margin-top: 30px;
      font-style: italic;
      color: #222;
      font-size: 16px;
      max-width: 500px;
      text-align: center;
    }

    .footer {
      margin-top: 50px;
      font-size: 14px;
      color: #444;
      text-align: center;
      opacity: 0.85;
      transition: transform 0.3s;
    }

    .footer:hover {
      transform: scale(1.05);
      color: #000;
    }

    .emoji {
      font-size: 20px;
    }
  </style>
</head>
<body>

  <h1>Conversations With Strangers <span class="emoji">💬</span></h1>
  <p>Write something anonymously. Someone, somewhere will read it. <span class="emoji">🌍</span></p>

  <div class="card">
    <textarea id="messageInput" rows="5" placeholder="Type your message here..."></textarea>
    <select id="categoryInput">
      <option value="general">General</option>
      <option value="funny">Funny</option>
      <option value="sad">Sad</option>
      <option value="advice">Advice</option>
      <option value="confession">Confession</option>
    </select>
    <button onclick="submitMessage()">Submit ✉️</button>
  </div>

  <div id="randomMessage">Loading a message...</div>

  <div class="footer">
    Made with ❤️ by <strong>Sushant Markad</strong>
  </div>

  <!-- Firebase SDK -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
    import { getFirestore, collection, addDoc, getDocs } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

    const firebaseConfig = {
      apiKey: "AIzaSyBq6AmRwD0d5pWOjxh4wLyo_tXqZ4ax_VQ",
      authDomain: "conversations-with-strangers.firebaseapp.com",
      projectId: "conversations-with-strangers",
      storageBucket: "conversations-with-strangers.firebasestorage.app",
      messagingSenderId: "434852695814",
      appId: "1:434852695814:web:9e1bf5796de9cfd4965887",
      measurementId: "G-JFVZBYB8LY"
    };

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    window.submitMessage = async function () {
      const message = document.getElementById("messageInput").value.trim();
      const category = document.getElementById("categoryInput").value;

      if (!message) {
        alert("Please enter a message.");
        return;
      }

      try {
        await addDoc(collection(db, "messages"), {
          text: message,
          category: category,
          timestamp: new Date()
        });
        alert("✅ Your message has been submitted!");
        document.getElementById("messageInput").value = "";
        loadRandomMessage();
      } catch (error) {
        console.error("Error writing to Firestore", error);
        alert("❌ Something went wrong. Try again.");
      }
    };

    async function loadRandomMessage() {
      try {
        const snapshot = await getDocs(collection(db, "messages"));
        const allMessages = snapshot.docs.map(doc => doc.data());

        if (allMessages.length > 0) {
          const random = allMessages[Math.floor(Math.random() * allMessages.length)];
          document.getElementById("randomMessage").innerText = `[${random.category}] "${random.text}"`;
        } else {
          document.getElementById("randomMessage").innerText = "No messages yet. Be the first to share something.";
        }
      } catch (error) {
        console.error("Error loading messages", error);
        document.getElementById("randomMessage").innerText = "Couldn't load messages.";
      }
    }

    loadRandomMessage();
  </script>
</body>
</html>
