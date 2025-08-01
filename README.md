# 🛰️ Gensyn Telegram Monitor Bot

A lightweight Python bot that monitors your Gensyn node log files in real-time and sends Telegram alerts for crashes, memory errors, and other failures. Ideal for node operators who want to stay informed without constant manual checking.

---

## 🚀 Features

- ✅ Real-time monitoring of Gensyn node logs
- 📬 Telegram alerts for:
  - Crashes
  - Out of memory errors
  - Runtime exceptions
- 🔧 Customizable error keywords
- 💡 Easy setup with `.env` configuration

---

## 📦 Installation & Setup

### 1️⃣ Create a Telegram Bot

#### 1.1 Get Your Bot Token
- Open Telegram and search for `@BotFather`
- Send `/newbot` and follow instructions
- Copy your `BOT_TOKEN`

#### 1.2 Get Your Chat ID
- Send any message to your new bot
- Open this URL (replace with your actual token):  
  `https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates`
- Find your chat ID in the response JSON under:  
  `"chat": { "id": YOUR_CHAT_ID }`

---

### 2️⃣ Prepare Your Environment

#### Install dependencies:
```bash
pip install requests python-dotenv
