# 🛡️ JobShield — AI-Powered Job & Internship Scam Detector

[![Deploy to Render](https://render.com/images/deploy-to-render.svg)](https://render.com/deploy?repo=https://github.com/Prudhvi2206/JobShield)

JobShield (originally **FakeJobPostDetector**) is a modern, multi-layered cybersecurity defense system designed to protect students and entry-level job seekers from fraudulent employment postings. It integrates **Natural Language Processing (NLP)**, **live webpage HTML scraping**, **domain reputation auditing**, and **interactive behavioral risk profiling** into a unified, secure Flask-based web application.

---

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-3.0+-darkgreen.svg?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![Scikit-Learn](https://img.shields.io/badge/scikit--learn-%23F7931E.svg?style=for-the-badge&logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![SQLite](https://img.shields.io/badge/SQLite-%2307405e.svg?style=for-the-badge&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

---

## 🎯 The Problem & Our Solution

Job scams are at an all-time high. Threat actors frequently list fraudulent remote internships and highly-paid entry-level jobs on popular channels to harvest candidates' sensitive bank information, charge "registration fees," or force them into work-from-home MLM schemes. Traditional spam filters fail because the written content of fake ads mimics legitimate job descriptions perfectly.

**JobShield** addresses this gap by implementing a **Four-Layered Verification Engine** that doesn't just read the job description — it vets the **host domain's reputation**, crawls the website for **corporate legitimacy signals**, and provides candidates with an **interactive behavioral self-assessment quiz**.

---

## 🏗️ System Architecture & Logic Flow

When a user submits a job posting URL or job text, JobShield triggers a synchronized, multi-tiered analysis:

```
                           ┌──────────────────────────────┐
                           │      Submit Job Post URL     │
                           └──────────────┬───────────────┘
                                          │
                                          ▼
                             Live Webpage HTML Scraper
                                          │
                 ┌────────────────────────┼────────────────────────┐
                 ▼                        ▼                        ▼
           Domain Check             Company Info              Page Content
        ───────────────────      ───────────────────       ───────────────────
         • TLD Analysis           • Parse Meta Tags         • Custom Feature
         • Free-Email Checks      • JSON-LD schemas           Engineering
         • Trust Lists            • Legitimacy Heuristics   • TF-IDF Vectorizer
         • Risk Boosting          • Scam Red-Flags          • Naive Bayes Model
                 │                        │                        │
                 └────────────────────────┼────────────────────────┘
                                          │
                                          ▼
                             Weighted Scoring & Penalties
                                          │
                                          ▼
                          ┌──────────────────────────────┐
                          │       Final Verdict:         │
                          │     GENUINE or FAKE Job      │
                          └──────────────────────────────┘
```

---

## 🧠 Core Features & Multilevel Detection Heuristics

### 1. Layer 1: Machine Learning Text Engine (NLP)
At the core of JobShield is a **Multinomial Naive Bayes Model** trained on a curated dataset of genuine and fraudulent postings. 
* **Feature Engineering:** Before feeding text to the vectorizer, the system injects explicit high-weight indicator tokens based on regular expression patterns:
  * ❌ **Scam Flags (`FAKE_INDICATOR`):** WhatsApp-only hiring, upfront application/training fees, unrealistic promises (100% job guarantees), and high-pressure urgency tactics.
  * ✅ **Trust Flags (`GENUINE_INDICATOR`):** Official application career portals, explicit "no fees" declarations, corporate domain career emails (e.g. `careers@company.com`), and founding history citations.
* **Model Parameters:** Capped `TfidfVectorizer` (1 to 2 N-gram range, English stopwords removed, maximum 500 features) combined with `MultinomialNB(alpha=0.1)`.

### 2. Layer 2: Live HTML Scraper & Structured Metadata Auditing
The URL scanner crawls the live webpage using customized request headers (mimicking a standard browser) and parses the page using `BeautifulSoup`.
* **JSON-LD Schema Extraction:** Evaluates structured data (`application/ld+json`) to locate `JobPosting` and `Organization` entities to confirm the true hiring organization and official job details.
* **OpenGraph Vetting:** Inspects `og:site_name`, `og:description`, and page titles to resolve the company's identity.

### 3. Layer 3: Heuristic Company Legitimacy Verification
The engine scores companies based on the presence of verified business indicators vs. known scam patterns:
* **Legitimacy Indicators (+):** Mentions of founding year, detailed "About Us" background, office address, corporate awards/certifications, privacy policy links, and customer reference stories.
* **Scam Indicators (-):** Communication restricted exclusively to messaging apps (Telegram, WhatsApp), requested investments, and unrealistic requirements (e.g., highly technical roles requiring zero qualifications).
* **Domain Alignment Check:** Cross-references the company name against the registered domain name (e.g., checking if domain matches the company name to avoid phishing).

### 4. Layer 4: Domain Reputation Vetting
Audits host domains against blacklists and structural reputation markers:
* Flags high-risk Top-Level Domains (TLDs) such as `.tk`, `.ml`, `.ga`, `.cf`, `.xyz`, `.top`, and `.click`.
* Catches standard domains using free-email hosts (like `gmail.com` or `yahoo.com`) pretending to represent corporate career sites.
* Fast-tracks recognized enterprise applicant systems like `greenhouse.io`, `lever.co`, and `myworkdayjobs.com`.

### 5. Interactive Behavioral Red-Flag Quiz
Provides students with an interactive, weighted self-assessment dashboard evaluating the hiring workflow behaviors:
* Upfront charging of background checks/training materials (**28% weight**).
* Messaging-only interviewing via Telegram or WhatsApp (**22% weight**).
* Early collection of personal banking details (**26% weight**).

---

## 📁 Project Structure

```text
fake_job_post_detector/
├── backend/
│   └── app.py                # Flask Backend & Routing (Auth, APIs, SQLite DB Models)
├── data/
│   └── job_posts.csv         # Curated ML training dataset (63 samples)
├── frontend/                 # Legacy static-only frontend (kept for reference)
├── static/                   # Assets served by the Flask app
│   ├── jobshield_banner.png  # Project visual banner
│   ├── script.js             # API request orchestration & UI dynamic rendering
│   └── styles.css            # Custom CSS with responsive layouts & modern aesthetics
├── templates/                # Jinja2 HTML templates
│   ├── base.html             # Shell navbar, footer, and styling base
│   ├── home.html             # Landing portal page
│   ├── login.html            # CSRF-protected User login form
│   ├── signup.html           # Secure User registration page
│   └── detector.html         # Protected scanner dashboard (ML, URL, Quiz interfaces)
├── tests/
│   └── test_api_basics.py    # Pytest unit & schema verification tests
├── .env.example              # Sample environment config file
├── .gitignore                # Production-grade Git ignore patterns
├── IMPROVEMENTS.md           # Heuristic design, metrics, & v2.0 improvement notes
├── model.py                  # ML training script (TF-IDF + Naive Bayes pipeline)
├── test_cognifyz.py          # Integration test suite running live scans against URLs
├── pyproject.toml            # Code quality and testing tool configs
└── requirements.txt          # Production application dependencies
```

---

## ⚙️ Environment Configuration

JobShield uses environment variables for secure production configuration. Copy `.env.example` to create your own configuration:

| Variable | Description | Default | Allowed Values |
| :--- | :--- | :--- | :--- |
| `JOBSHIELD_ENV` | Target running environment | `development` | `development`, `production` |
| `JOBSHIELD_DEBUG` | Verbose debug logs | `0` | `1` (Enabled), `0` (Disabled) |
| `JOBSHIELD_SECRET` | Flask Session secret key | Dev Fallback | Cryptographically strong random string (Required in Production) |
| `JOBSHIELD_CORS_ORIGINS`| Permitted API origins | Disabled | Comma-separated list of hosts |
| `JOBSHIELD_MAX_BODY_BYTES`| Maximum payload body size | `65536` (64KB) | Size in bytes |

---

## 🚀 Getting Started

### 1. Prerequisites
Ensure you have **Python 3.8+** installed on your system.

### 2. Set Up a Virtual Environment & Dependencies
Initialize a virtual environment to manage dependencies:
```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows PowerShell:
.\venv\Scripts\Activate.ps1
# On Linux / macOS:
source venv/bin/activate

# Install required libraries
pip install -r requirements.txt
```

### 3. Train the ML Model
Generate the model and vectorizer binary assets (`model.pkl` and `vectorizer.pkl`) by training the Naive Bayes engine on the custom dataset:
```bash
python model.py
```
*This will analyze the dataset, inject regex indicator features, build the vocabulary, and log classification performance metrics (expecting 100% precision and recall on the curated training corpus).*

### 4. Run the Web Application
Start the Flask local development server:
```bash
python backend/app.py
```
* The application will spin up at `http://127.0.0.1:5000`
* Secure user authentication records are managed automatically via SQLite in `instance/jobshield.db`.

---

## 🧪 Testing & Verification

### Unit & API Verification
Execute `pytest` to run automated assertions against API health, CSRF states, and schema compliance:
```bash
python -m pytest
```

### Live URL Scraping Integration Tests
Verify live webpage crawling and heuristic evaluation against active URLs (like Google, LinkedIn, and flagged domains):
1. With your backend server running (`python backend/app.py`), open a second terminal.
2. Execute the integration suite:
   ```bash
   python test_cognifyz.py
   ```

---

## 📡 Core API Reference (JSON)

### 1. Check API Health
* **Endpoint:** `GET /api/health`
* **Response (`200 OK`):**
  ```json
  {
    "service": "JobShield API",
    "version": "1.0.0",
    "status": "ready"
  }
  ```

### 2. Analyze Job Posting Text (ML Model)
* **Endpoint:** `POST /predict`
* **Request Header:** `Content-Type: application/json`
* **Request Body:**
  ```json
  {
    "text": "URGENT hiring! Work-from-home customer service representatives. No experience or qualifications required. Earn Rs. 50,000/week guaranteed. Mandatory registration fee of Rs. 1,000. Apply via WhatsApp only!"
  }
  ```
* **Response (`200 OK`):**
  ```json
  {
    "result": "FAKE",
    "confidence": 1.0,
    "risk_score": 0
  }
  ```

### 3. Scan Webpage URL (Deep Multi-Layer Vetting)
* **Endpoint:** `POST /scan-url`
* **Request Header:** `Content-Type: application/json`
* **Request Body:**
  ```json
  {
    "url": "https://cognifyz.com/internships/"
  }
  ```
* **Response (`200 OK`):**
  ```json
  {
    "mode": "url_scan",
    "url": "https://cognifyz.com/internships/",
    "result": "FAKE",
    "risk_score": 0,
    "confidence": 1.0,
    "company_info": {
      "name": "Cognifyz Technologies",
      "title": "Cognifyz Internships & Careers",
      "description": "Information on technical roles..."
    },
    "company_verification": {
      "name": "Cognifyz Technologies",
      "is_verified": false,
      "signals": [
        "Company name matches known suspicious fake company patterns",
        "Scam indicator present; company should not be trusted",
        "Communication only through messaging apps",
        "Domain 'cognifyz' matches company 'cognifyz technologies'"
      ],
      "legitimate_indicators": 1,
      "scam_indicators": 2
    },
    "domain": {
      "hostname": "cognifyz.com",
      "is_trusted": false,
      "signals": [
        "Domain looks standard; still verify company + careers page."
      ],
      "risk_boost": 0
    },
    "text_preview": "Welcome to Cognifyz Technologies Internships page...",
    "note": "Deep analysis: ML score + domain analysis + company legitimacy verification. Always verify on official career sites."
  }
  ```

### 4. Interactive Quiz Evaluation
* **Endpoint:** `POST /quiz`
* **Request Header:** `Content-Type: application/json`
* **Request Body:**
  ```json
  {
    "answers": {
      "upfront_fee": true,
      "telegram_whatsapp_only": true,
      "unrealistic_pay": false,
      "bank_details_early": false,
      "no_company_identity": false,
      "urgent_pressure": true
    }
  }
  ```
* **Response (`200 OK`):**
  ```json
  {
    "mode": "quiz",
    "risk_score": 38,
    "label": "HIGH RISK",
    "triggered_flags": [
      "upfront_fee",
      "telegram_whatsapp_only",
      "urgent_pressure"
    ],
    "max_possible": 100
  }
  ```

---

## 🛡️ Production Deployment Checklist

Before deploying JobShield to public production environments:
- [ ] Turn off development logging and Flask debug modes: `JOBSHIELD_DEBUG=0`.
- [ ] Set `JOBSHIELD_ENV=production` to enforce secure flags on session cookies (`Secure`, `SameSite=Lax`, `HTTPOnly`).
- [ ] Assign a cryptographically strong secret string to the `JOBSHIELD_SECRET` environment variable.
- [ ] Set `JOBSHIELD_CORS_ORIGINS` to accept API requests *only* from verified domain names.
- [ ] Relocate SQLite database storage (`instance/jobshield.db`) to a persistent, highly available filesystem volume.
- [ ] Bind Flask behind a robust WSGI HTTP server (such as `waitress` on Windows, or `gunicorn` on Linux).
- [ ] Set up an NGINX or Apache reverse proxy to serve SSL certificates (HTTPS) and manage client request rate-limiting.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
