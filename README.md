<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>LOCHI AI</title>
<style>
  body {
    margin: 0;
    background: #000;
    color: #fff;
    font-family: sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 100vh;
  }
  h1 {
    color: #ff007f;
    margin: 20px;
    font-size: 2rem;
  }
  #messages {
    flex: 1;
    width: 100%;
    max-width: 600px;
    overflow-y: auto;
    padding: 10px;
  }
  .msg {
    border: 1px solid #ff007f;
    border-radius: 12px;
    padding: 10px;
    margin: 5px 0;
    background: #111;
  }
  .user {
    text-align: right;
    border-color: #888;
  }
  .input-bar {
    display: flex;
    width: 100%;
    max-width: 600px;
    margin: 10px;
    border: 1px solid #ff007f;
    border-radius: 12px;
    overflow: hidden;
  }
  .input-bar input {
    flex: 1;
    padding: 10px;
    border: none;
    outline: none;
    background: #000;
    color: #fff;
  }
  .input-bar button {
    background: #ff007f;
    color: #000;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
  }
  footer {
    font-size: 0.8rem;
    color: #ff007f;
    margin-bottom: 5px;
  }
</style>
</head>
<body>

<h1>LOCHI AI</h1>
<div id="messages">
  <div class="msg">Hi! I am LOCHI AI. How can I help you today?</div>
</div>
<div class="input-bar">
  <input id="userInput" type="text" placeholder="Type your message..." />
  <button onclick="sendMessage()">Send</button>
</div>
<footer>Developed By Rush Dewon</footer>

<script>
  const apiKey = "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";
  let messages = [
    { role: "assistant", content: "Hi! I am LOCHI AI. How can I help you today?" }
  ];

  async function sendMessage() {
    const input = document.getElementById("userInput");
    const text = input.value.trim();
    if (!text) return;

    addMessage(text, "user");
    input.value = "";

    messages.push({ role: "user", content: text });

    try {
      const res = await fetch("https://api.openai.com/v1/chat/completions", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "Authorization": "Bearer " + apiKey
        },
        body: JSON.stringify({
          model: "gpt-4o-mini",
          messages: messages
        })
      });
      const data = await res.json();
      const aiReply = data.choices[0].message.content;
      addMessage(aiReply, "ai");
      messages.push({ role: "assistant", content: aiReply });
    } catch (e) {
      console.error(e);
      addMessage("Error connecting to AI.", "ai");
    }
  }

  function addMessage(text, sender) {
    const div = document.createElement("div");
    div.className = "msg" + (sender === "user" ? " user" : "");
    div.textContent = text;
    document.getElementById("messages").appendChild(div);
    document.getElementById("messages").scrollTop = document.getElementById("messages").scrollHeight;
  }

  document.getElementById("userInput").addEventListener("keydown", e => {
    if (e.key === "Enter") sendMessage();
  });
</script>

</body>
</html>
