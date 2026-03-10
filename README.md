<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Westbrook Secondary Radio Communications</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
    body {
        background: #0f0f10;
        font-family: Arial, sans-serif;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
        color: white;
    }

    .radio {
        width: 420px;
        background: #1a1a1d;
        border-radius: 20px;
        padding: 20px;
        box-shadow: 0 0 25px rgba(255, 140, 0, 0.4);
        position: relative;
    }

    .title {
        text-align: center;
        font-size: 1.4rem;
        margin-bottom: 15px;
        font-weight: bold;
        color: #ff8c00;
    }

    .frequency-selector {
        display: flex;
        justify-content: space-between;
        margin-bottom: 15px;
    }

    .freq-btn {
        background: #2a2a2d;
        border: 2px solid #444;
        padding: 10px 15px;
        border-radius: 10px;
        cursor: pointer;
        color: white;
        transition: 0.2s;
    }

    .freq-btn.active {
        background: #ff8c00;
        border-color: #ffae42;
    }

    .screen {
        background: #000;
        border: 2px solid #333;
        padding: 15px;
        height: 120px;
        border-radius: 10px;
        margin-bottom: 15px;
        font-size: 0.9rem;
        overflow-y: auto;
    }

    .input-area {
        display: flex;
        gap: 10px;
    }

    input {
        flex: 1;
        padding: 10px;
        border-radius: 10px;
        border: none;
        outline: none;
        background: #2a2a2d;
        color: white;
    }

    .send-btn {
        background: #ff8c00;
        border: none;
        padding: 10px 15px;
        border-radius: 10px;
        cursor: pointer;
        font-weight: bold;
        color: black;
    }

    .priority-btn {
        width: 100%;
        background: orange;
        padding: 12px;
        border-radius: 10px;
        border: none;
        cursor: pointer;
        font-weight: bold;
        margin-bottom: 15px;
        color: black;
        font-size: 1rem;
    }

    .flash {
        animation: flashAnim 0.4s ease;
    }

    @keyframes flashAnim {
        0% { box-shadow: 0 0 20px red; }
        100% { box-shadow: none; }
    }
</style>
</head>
<body>

<div class="radio" id="radioBox">

    <div class="title">Westbrook Secondary Radio Communications</div>

    <button class="priority-btn" onclick="triggerPriority()">⚠️ Priority Alert</button>

    <div class="frequency-selector">
        <button class="freq-btn active" onclick="setFrequency(1)">Freq 1</button>
        <button class="freq-btn" onclick="setFrequency(2)">Freq 2</button>
        <button class="freq-btn" onclick="setFrequency(3)">Freq 3</button>
        <button class="freq-btn" onclick="setFrequency(4)">Freq 4</button>
    </div>

    <div class="screen" id="screen">Frequency 1 Active…</div>

    <div class="input-area">
        <input type="text" id="messageInput" placeholder="Type message…">
        <button class="send-btn" onclick="sendMessage()">Send</button>
    </div>

</div>

<script>
let currentFrequency = 1;

const webhooks = {
    1: "https://discord.com/api/webhooks/1480770456058335252/iqvbk1P0h7pLX77mM4CtJHxqqAmxCfnVGK6o7UVJrLtKTOKofSwC5L5vFcPKrw3mc_q5",
    2: "https://discord.com/api/webhooks/1480770545699000420/BtSEMxGwR3_pLqV7OycD8tpAuogkKcFjxW-S95OWSjqKLaH0N2agSoWYMmOhOColSmKr",
    3: "https://discord.com/api/webhooks/1480770590817386668/2TI3UHZqEn5fOMdJD7fXKrD02C2aZ_RZBrmCiETC2pVjtCgy9toAiM8qxLXEBol5noLF",
    4: "https://discord.com/api/webhooks/1480770638322077787/iZdwf9-NuR-Ad_ntuVN_oa9iFz9TJsacgeankGnWKjDe0kJtczhbP1bSdV3LxHAwimQ-"
};

function setFrequency(freq) {
    currentFrequency = freq;
    document.getElementById("screen").innerText = `Frequency ${freq} Active…`;

    document.querySelectorAll(".freq-btn").forEach(btn => btn.classList.remove("active"));
    document.querySelectorAll(".freq-btn")[freq - 1].classList.add("active");
}

function sendMessage() {
    const msg = document.getElementById("messageInput").value.trim();
    if (!msg) return;

    fetch(webhooks[currentFrequency], {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ content: `📻 **[Freq ${currentFrequency}]** ${msg}` })
    });

    document.getElementById("screen").innerText += `\nYou: ${msg}`;
    document.getElementById("messageInput").value = "";
}

function triggerPriority() {
    const location = prompt("Enter your exact location in the school:");
    if (!location) return;

    const reason = prompt("Enter the reason:");
    if (!reason) return;

    const alertMsg = `⚠️| Code Yellow, a member of staff has pushed priority their location is **${location}** the reason is **${reason}**=`;

    // Flash animation
    const radio = document.getElementById("radioBox");
    radio.classList.add("flash");
    setTimeout(() => radio.classList.remove("flash"), 400);

    // Send to ALL frequencies
    Object.values(webhooks).forEach(url => {
        fetch(url, {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ content: alertMsg })
        });
    });

    document.getElementById("screen").innerText += `\n⚠️ Priority Alert Sent`;
}
</script>

</body>
</html>
