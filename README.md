



<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Application Result Sender</title>
  <style>
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: #05060a;
      color: #f5f5f5;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      margin: 0;
    }
    .card {
      background: rgba(15, 16, 25, 0.9);
      border-radius: 18px;
      padding: 24px 28px;
      width: 100%;
      max-width: 480px;
      box-shadow: 0 18px 45px rgba(0, 0, 0, 0.6);
      backdrop-filter: blur(18px);
      border: 1px solid rgba(255, 255, 255, 0.06);
    }
    h1 {
      font-size: 1.4rem;
      margin: 0 0 4px;
    }
    p.subtitle {
      margin: 0 0 18px;
      font-size: 0.9rem;
      color: #b0b3c0;
    }
    label {
      display: block;
      font-size: 0.85rem;
      margin-bottom: 4px;
      color: #d7d9e4;
    }
    input, textarea, select {
      width: 100%;
      padding: 9px 11px;
      border-radius: 10px;
      border: 1px solid rgba(255, 255, 255, 0.08);
      background: rgba(10, 11, 18, 0.9);
      color: #f5f5f5;
      font-size: 0.9rem;
      outline: none;
      box-sizing: border-box;
    }
    input:focus, textarea:focus, select:focus {
      border-color: #5865f2;
      box-shadow: 0 0 0 1px rgba(88, 101, 242, 0.4);
    }
    textarea {
      resize: vertical;
      min-height: 80px;
    }
    .field {
      margin-bottom: 14px;
    }
    button {
      width: 100%;
      padding: 10px 14px;
      border-radius: 999px;
      border: none;
      background: linear-gradient(135deg, #5865f2, #3b82f6);
      color: #fff;
      font-weight: 600;
      font-size: 0.95rem;
      cursor: pointer;
      margin-top: 6px;
      transition: transform 0.08s ease, box-shadow 0.08s ease, opacity 0.08s ease;
      box-shadow: 0 10px 25px rgba(88, 101, 242, 0.45);
    }
    button:hover {
      transform: translateY(-1px);
      box-shadow: 0 14px 30px rgba(88, 101, 242, 0.6);
    }
    button:active {
      transform: translateY(0);
      box-shadow: 0 8px 18px rgba(88, 101, 242, 0.4);
      opacity: 0.9;
    }
    .notice {
      margin-top: 14px;
      font-size: 0.85rem;
      line-height: 1.4;
      padding: 10px 12px;
      border-radius: 10px;
      background: rgba(22, 163, 74, 0.12);
      border: 1px solid rgba(22, 163, 74, 0.4);
      color: #bbf7d0;
      display: none;
    }
    .error {
      background: rgba(220, 38, 38, 0.12);
      border-color: rgba(220, 38, 38, 0.5);
      color: #fecaca;
    }
  </style>
</head>
<body>
  <div class="card">
    <h1>Application Result</h1>
    <p class="subtitle">Send the final decision to the applicant.</p>

    <form id="resultForm">
      <div class="field">
        <label for="pingUser">Ping User (e.g. @username)</label>
        <input id="pingUser" name="pingUser" placeholder="@User" required />
      </div>

      <div class="field">
        <label for="discordId">Discord ID</label>
        <input id="discordId" name="discordId" placeholder="123456789012345678" required />
      </div>

      <div class="field">
        <label for="result">Result</label>
        <select id="result" name="result" required>
          <option value="Accepted">Accepted</option>
          <option value="Denied">Denied</option>
        </select>
      </div>

      <div class="field">
        <label for="reason">Reason</label>
        <textarea id="reason" name="reason" required></textarea>
      </div>

      <button type="submit">Send Result</button>
    </form>

    <div id="notice" class="notice"></div>
  </div>

  <script>
    const form = document.getElementById("resultForm");
    const notice = document.getElementById("notice");

    const WEBHOOK_URL = "https://discord.com/api/webhooks/1487608021201981570/CaoYdYB7WE_9nVqRybrgN03pnCTabXw8siyCGvl-tX_ZJ3gYM2gOOSdEplTC8dxgEKZj";

    form.addEventListener("submit", async (e) => {
      e.preventDefault();
      notice.style.display = "none";
      notice.classList.remove("error");

      const pingUser = document.getElementById("pingUser").value.trim();
      const discordId = document.getElementById("discordId").value.trim();
      const result = document.getElementById("result").value;
      const reason = document.getElementById("reason").value.trim();

      const embed = {
        title: "Application Result",
        color: result === "Accepted" ? 0x22c55e : 0xef4444,
        fields: [
          { name: "Ping", value: pingUser || "N/A", inline: false },
          { name: "Discord ID", value: discordId || "N/A", inline: false },
          { name: "Result", value: result, inline: false },
          { name: "Reason", value: reason || "N/A", inline: false }
        ],
        footer: {
          text: "Staff Application System"
        },
        timestamp: new Date().toISOString()
      };

      const payload = {
        content: `${pingUser}`,
        embeds: [embed]
      };

      try {
        const res = await fetch(WEBHOOK_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(payload)
        });

        if (!res.ok) throw new Error("Webhook failed");

        form.reset();
        notice.textContent =
          "Result sent successfully! Thank you for completing applying for our staff. Go ahead and open a support ticket for more information.";
        notice.style.display = "block";
      } catch (err) {
        notice.textContent = "There was an error sending the result. Try again or contact support.";
        notice.classList.add("error");
        notice.style.display = "block";
      }
    });
  </script>
</body>
</html>
