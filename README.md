# gesnyn-telegram-monitor-bot
A lightweight Telegram bot that monitors Gensyn node logs in real-time and sends alerts for errors, crashes, or memory issues. Ideal for staying updated on your node’s health without constant manual checks.
## 🚀 Features

- ✅ Real-time monitoring of node logs
- 📬 Telegram alerts for:
  - Crashes
  - Out of memory errors
  - Custom error keywords
- 🔧 Easy setup and configuration

## 📦 Installation
### 1️⃣ Create a Telegram Bot

📲 Telegram Bot Setup (Quick Guide)
1️⃣ Create a Bot

Chat with @BotFather

Send /newbot → follow instructions → save the bot token

2️⃣ Get Your Chat ID

Send a message to your new bot

Visit: https: //api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates

Look for "chat": { "id": YOUR_CHAT_ID } in the response

```bash
pip install requests
```

🔹 2. Create Python Script
Create a file called ```log_alert_bot.py:```

```bash
nano log_alert_bot.py
```

Paste this into your terminal and add your BOT_TOKEN in the ```"YOUR_BOT_TOKEN_HERE"```  and ```"YOUR_CHAT_ID_HERE"```
and change ```Your_peer_name``` to your peer name

```bash
import time
import requests
import threading

BOT_TOKEN = "YOUR_BOT_TOKEN_HERE"
CHAT_ID = "YOUR_CHAT_ID_HERE"

LOG_FILES = [
    "/root/rl-swarm/logs/training_<your_peer_name>.log",
]

KEYWORDS = [
    "CUDA out of memory",
    "RuntimeError",
    "error",
    "exited with code",
]

def check_logs():
    last_lines = {file: "" for file in LOG_FILES}
    while True:
        for file in LOG_FILES:
            try:
                with open(file, "r") as f:
                    lines = f.readlines()
                    if lines:
                        last_line = lines[-1]
                        if last_line != last_lines[file]:
                            last_lines[file] = last_line
                            if any(keyword in last_line for keyword in KEYWORDS):
                                send_alert(f"⚠️ Alert from {file}:\n{last_line}")
            except Exception as e:
                send_alert(f"Error reading log file {file}:\n{e}")
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

After this, check the file exists:
``` bash 
ls -l /root/rl-swarm/log_alert_bot.py
```

Then start your bot:
``` bash 
nohup python3 /root/rl-swarm/log_alert_bot.py &
```
If it runs without error, stop it with ```Ctrl + C```, then run it in background like this:
``` bash
nohup python3 /root/rl-swarm/log_alert_bot.py > /root/rl-swarm/nohup.log 2>&1 &
```

Then check output with:
``` bash
tail -f /root/rl-swarm/nohup.log
```

If you ran the script directly and it didn’t show any output or error, that means it started successfully and is now running the monitoring loops silently (which is expected).

What to do next:

Open another terminal session or press ```Ctrl+Z``` and type bg to put the script running in the background (if you want).

To stop the script running in foreground, press ```Ctrl + C```.

If you want to run it in the background properly, use:

``` bash 
nohup python3 /root/rl-swarm/log_alert_bot.py > /root/rl-swarm/nohup.log 2>&1 &
```

Then check logs/output anytime with:
``` bash
tail -f /root/rl-swarm/nohup.log
```

Test alert:
``` bash
echo "Test error: out of memory" >> /root/rl-swarm/logs/training_<YOUR_PEER_NAME>.log
```
