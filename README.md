# AI Guest Assistant Widget

A lightweight, embeddable AI chat assistant built for hotels and hospitality websites. It combines a dependency-free vanilla HTML/CSS/JS floating chat widget with a FastAPI backend powered by Google Gemini.

## Features
- **Plug-and-Play Chat Widget (`widget.html`):** Drop-in floating chat interface with XSS-safe text rendering, responsive mobile layout, and conversation history memory.
- **FastAPI Backend (`train.py`):** Clean REST API (`/ai/chat` and `/health`) with automatic interactive Swagger docs at `/docs`.
- **Custom Knowledge Grounding:** Answers guest inquiries about check-in/checkout, Wi-Fi, breakfast, and payments using local hotel data (`faq.txt`).

## Project Structure
```text
├── train.py          # FastAPI server and Gemini AI integration
├── widget.html       # Embeddable frontend chat widget
├── faq.txt           # Hotel knowledge base for answering guest FAQs
└── .env              # Environment variables (GEMINI_API_KEY - not tracked in Git)
```

## Local Setup & Running

### 1. Activate Virtual Environment & Install Dependencies
```bash
# Windows PowerShell
Set-ExecutionPolicy -Scope Process RemoteSigned
.\sprint\Scripts\Activate.ps1
pip install fastapi uvicorn google-genai python-dotenv
```

### 2. Configure Environment Variables
Create a `.env` file in the root directory and add your Gemini API key:
```env
GEMINI_API_KEY=your_api_key_here
```

### 3. Start the Backend Server (Terminal 1)
```bash
uvicorn train:app --reload --port 8000
```
API docs will be available at `http://localhost:8000/docs`.

### 4. Serve the Frontend Widget (Terminal 2)
```bash
python -m http.server 3000
```
Open `http://localhost:3000/widget.html` in your browser to test the assistant.
