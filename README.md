<p align="center">
  <img src="https://img.shields.io/badge/AdmitDE-Germany%20Admissions-0D0F12?style=for-the-badge&labelColor=1D4ED8&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0id2hpdGUiPjxwYXRoIGQ9Ik0xMiAzTDEgOWwxMSA2IDktNS0xIDkgMTEgNiA5LTUtMS05IDExIDZWMTdsLTEgLjVWMTd6Ii8+PC9zdmc+" alt="AdmitDE Badge" />
</p>

<h1 align="center">🇩🇪 AdmitDE</h1>
<p align="center">
  <strong>AI-Powered Germany University Admissions Consultant</strong><br/>
  <em>Tell us your grades. We'll tell you exactly where you stand in Germany.</em>
</p>

<p align="center">
  <a href="https://ais-eekho.vercel.app"><img src="https://img.shields.io/badge/🌐_Live_Demo-ais--eekho.vercel.app-3B82F6?style=for-the-badge" alt="Live Demo" /></a>
  <img src="https://img.shields.io/badge/AI-Groq_LLaMA_3.3-22C55E?style=for-the-badge" alt="AI Model" />
  <img src="https://img.shields.io/badge/Cost-100%25_Free-F59E0B?style=for-the-badge" alt="Free" />
</p>

---

## ✨ What is AdmitDE?

AdmitDE is a **single-page AI-powered admissions consultant** that helps students from South Asia, the Middle East, and beyond determine their eligibility for **Computer Science, AI, and Data Science programs** at **public German universities** — all in under 3 minutes.

### 🎯 How It Works

1. **Enter your academic profile** — GPA, IELTS, German level, nationality
2. **Set your preferences** — target cities, semester intake, instruction language
3. **Get instant results** — AI-powered analysis with per-university gap breakdown

### 🏫 Universities Covered

| University | City | QS CS Rank |
|------------|------|------------|
| TU Munich (TUM) | Munich | ~50 |
| RWTH Aachen | Aachen | ~100 |
| TU Berlin | Berlin | ~150 |
| KIT Karlsruhe | Karlsruhe | ~130 |
| FAU Erlangen-Nürnberg | Erlangen | — |
| LMU Munich | Munich | — |
| University of Stuttgart | Stuttgart | — |
| Saarland University | Saarbrücken | — |
| TU Dresden | Dresden | — |
| University of Bonn | Bonn | — |
| University of Freiburg | Freiburg | — |
| University of Hamburg | Hamburg | — |

> All data sourced from official university pages, DAAD, and uni-assist (mid-2026).

---

## 🚀 Live Demo

**👉 [https://ais-eekho.vercel.app](https://ais-eekho.vercel.app)**

---

## 🛠️ Tech Stack

- **Frontend:** React 18 (CDN, no build step)
- **Styling:** Tailwind CSS + Custom CSS
- **AI Engine:** Groq API — `llama-3.3-70b-versatile`
- **Hosting:** Vercel (static deployment)
- **No backend required** — all matching logic runs client-side

---

## ⚡ Quick Start (Local)

1. Clone the repo:
   ```bash
   git clone https://github.com/uzair187C/AI-CONSULTANT.git
   cd AI-CONSULTANT
   ```

2. Create a `config.js` file in the root:
   ```js
   // Get your free API key at https://console.groq.com
   const GROQ_API_KEY = 'your_groq_api_key_here';
   ```

3. Open `index.html` in your browser — that's it!

> **Note:** The `config.js` file is `.gitignored` to keep your API key safe.

---

## 📋 Key Features

- ✅ **GPA Conversion Engine** — Percentage / 4.0 / German scale auto-conversion
- ✅ **3-Tier Matching** — Strong Match / Borderline / Not Eligible
- ✅ **APS Certificate Detection** — Auto-flags for Pakistan, India, China, Vietnam, Mongolia
- ✅ **AI Consultation** — Personalized 2-3 sentence analysis via Groq LLaMA 3.3
- ✅ **BS Language Warning** — Alerts that BS programs are almost exclusively German-taught
- ✅ **Universal Checklist** — Blocked account, visa, health insurance, documents
- ✅ **Responsive Design** — Works on mobile (375px+) through desktop
- ✅ **Graceful Fallback** — Shows pre-computed results if AI is unavailable

---

## 📄 License

MIT — Built for the community. Use it, fork it, improve it.

---

<p align="center">
  Made with ☕ and ambition by <a href="https://github.com/uzair187C">@uzair187C</a>
</p>
