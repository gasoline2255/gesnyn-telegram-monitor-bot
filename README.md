# 🛰️ Gensyn Telegram Monitor Bot

A lightweight Python script that monitors your Gensyn node logs in real-time and sends Telegram alerts for crashes, memory issues, or runtime errors. Perfect for staying informed on node status without constant manual checking.

---

## 🚀 Features

- ✅ Real-time log monitoring
- 📬 Telegram alerts for:
  - Crashes
  - Out of memory errors
  - Runtime or custom error keywords
- 🔧 Easy to set up and run in background

---

## 📦 Installation

### 1️⃣ Create a Telegram Bot

#### 1.1 Create the Bot
- Open Telegram and search for `@BotFather`
- Send `/newbot` and follow the instructions
- Save the bot token provided (format: `123456789:ABCdefGhIJK...`)

#### 1.2 Get Your Chat ID
- Send any message to your new bot
- Visit this URL (replace `<YOUR_BOT_TOKEN>` with your token):

```https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates```


- Look for the `chat.id` in the JSON response.

---

### 2️⃣ Install Required Library

On your Gensyn node:

```bash
pip install requests
```

### 3️⃣ Create the Bot Script
Create the file:
```bash
nano /root/rl-swarm/log_alert_bot.py
```
Paste the following code, replacing:

```"YOUR_BOT_TOKEN_HERE"``` with your actual token

```"YOUR_CHAT_ID_HERE"``` with your actual Telegram chat ID

```your_peer_name``` with your actual peer name used in the log filename

```bash
import time
import requests
import threading
import os

BOT_TOKEN = "YOUR_BOT_TOKEN_HERE"
CHAT_ID = "YOUR_CHAT_ID_HERE"

LOG_FILES = [
    "/root/rl-swarm/logs/training_your_peer_name.log",
]

KEYWORDS = [
    "CUDA out of memory",
    "EOFError: Ran out of input"
    "RuntimeError",
    "error",
    "exited with code",
]

def check_logs():
    last_lines = {file: "" for file in LOG_FILES}
    while True:
        for file in LOG_FILES:
            if not os.path.exists(file):
                continue
            try:
                with open(file, "r") as f:
                    lines = f.readlines()
                    if lines:
                        last_line = lines[-1].strip()
                        if last_line != last_lines[file]:
                            last_lines[file] = last_line
                            if any(keyword.lower() in last_line.lower() for keyword in KEYWORDS):
                                send_alert(f"⚠️ [{time.strftime('%Y-%m-%d %H:%M:%S')}] Alert from {os.path.basename(file)}:\n{last_line}")
            except Exception as e:
                send_alert(f"🚫 Error reading log file {file}:\n{e}")
        time.sleep(10)

def send_alert(message):
    url = f"https://api.telegram.org/bot{BOT_TOKEN}/sendMessage"
    data = {"chat_id": CHAT_ID, "text": message}
    try:
        requests.post(url, data=data)
    except Exception as e:
        print(f"Failed to send alert: {e}")

if __name__ == "__main__":
    threading.Thread(target=check_logs).start()
```
After pasting, save and exit by:

Press ```Ctrl + O``` (then press Enter to save)

Press ```Ctrl + X``` to exit nano

Then start your bot with:
``` bash
nohup python3 /root/rl-swarm/log_alert_bot.py &
```

Finally confirm the script exists:
``` bash
ls -l /root/rl-swarm/log_alert_bot.py
```
