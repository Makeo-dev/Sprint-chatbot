# AI Guest Assistant Widget

A lightweight, embeddable AI chat assistant built for hotels and hospitality websites. It combines a dependency-free vanilla HTML/CSS/JS floating chat widget with a FastAPI backend powered by Google Gemini.

Live server: https://sprint-chatbot.onrender.com (health check at `/health`, test page at `/docs`)

## Features
- **Plug-and-Play Chat Widget (`widget.html`):** Drop-in floating chat interface with XSS-safe text rendering, responsive mobile layout, and conversation history memory.
- **FastAPI Backend (`train.py`):** Clean REST API (`/ai/chat` and `/health`) with automatic interactive Swagger docs at `/docs`.
- **Custom Knowledge Grounding:** Answers guest inquiries about check-in/checkout, Wi-Fi, breakfast, and payments using local hotel data (`faq.txt`). If the answer is not in the FAQ, the bot tells the guest to contact reception.

## Project Structure
```text
├── train.py             # FastAPI server and Gemini AI integration
├── widget.html          # Embeddable frontend chat widget
├── faq.txt              # Hotel knowledge base for answering guest FAQs
├── requirements.txt     # Python libraries needed
├── README.md            # This file
└── .env                 # Environment variables (GEMINI_API_KEY, not tracked in Git)
```

## Local Setup & Running

### 0. Get the code
```bash
git clone https://github.com/Makeo-dev/Sprint-chatbot.git
cd Sprint-chatbot
```

### 1. Create/Activate the Virtual Environment & Install Dependencies
```bash
# Windows PowerShell (first time only: create the environment)
python -m venv sprint

# Windows PowerShell (every time)
Set-ExecutionPolicy -Scope Process RemoteSigned
.\sprint\Scripts\Activate.ps1
pip install -r requirements.txt
```
On Mac/Linux, activate with `source sprint/bin/activate` instead.

### 2. Configure Environment Variables
Get your **own** free Gemini key from Google AI Studio. Do not share keys, and never paste one into the code.
Create a `.env` file in the root directory and add your key:
```env
GEMINI_API_KEY=your_api_key_here
```

### 3. Start the Backend Server (Terminal 1)
```bash
uvicorn train:app --reload --port 8000
```
API docs will be available at `http://localhost:8000/docs`. Click `/ai/chat`, then "Try it out", and send a question.

### 4. Serve the Frontend Widget (Terminal 2)
```bash
python -m http.server 3000
```
Open `http://localhost:3000/widget.html` in your browser to test the assistant. The widget's `API_URL` must point at `http://localhost:8000` for local testing and at the Render address for the live site.

## Check That It Works
1. Open `https://sprint-chatbot.onrender.com/health`. You should see `{"status": "ok"}`.
2. Open `https://sprint-chatbot.onrender.com/docs`, click `/ai/chat`, then "Try it out", and send a question such as `what time is checkout?`.
3. Open the widget on a page and chat with it. If every reply is the "please contact reception" message, check the key, the model name, and the Render logs.

## Settings (Environment Variables)

| Name | Meaning |
|---|---|
| `GEMINI_API_KEY` | Your Gemini key (required) |
| `ALLOWED_ORIGIN` | Website addresses allowed to call the bot, comma separated, https and no trailing slash. Default is `http://localhost:3000` |
| `CHAT_MODEL` | Optional. Gemini model name shown in AI Studio |

## Deployment (Render)
- Start command: `uvicorn train:app --host 0.0.0.0 --port $PORT`
- Set `GEMINI_API_KEY` and `ALLOWED_ORIGIN` in Render's Environment tab (the `.env` file is not uploaded).
- Render redeploys automatically on every push to `main`.

## Team Rules
- Do not push straight to `main`. Work on a branch and ask the AI/ML lead to merge.
- To change what the bot knows, edit `faq.txt` only, then test a few questions on the `/docs` page.
- To change how it behaves (tone, rules), edit the system prompt in `train.py`, then test again.
- Never commit API keys or `.env` files.
- Report every wrong answer you find to the AI/ML lead, with the exact question you asked.

## Known Limits
- The free Render server sleeps after about 15 minutes idle, so the first reply can take 30 to 50 seconds.
- The Gemini free tier has a shared rate limit, so heavy testing can trigger the fallback message.
- Demo only. Never send real guest data.

## Restarting After a Shutdown
The local servers stop when the computer shuts down or restarts. The live bot on Render keeps running.
To use your local copy again:
1. Open a terminal in the project folder and activate the environment:
   Set-ExecutionPolicy -Scope Process RemoteSigned
   .\sprint\Scripts\Activate.ps1
2. Terminal 1: uvicorn train:app --reload --port 8000
3. Terminal 2: python -m http.server 3000
The key loads automatically from the .env file.