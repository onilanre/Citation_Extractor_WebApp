# Citation Extractor Web App

A lightweight **FastAPI** web app that uploads a **.docx** file and extracts **in-text citations**:
- **APA (parenthetical)** – e.g., `(Fawcett, 2006)`, `(Hinton & Salakhutdinov, 2006; Smith et al., 2021)`
- **APA (narrative)** – e.g., `Fawcett (2006)`, `Hinton & Salakhutdinov (2006)`
- **IEEE numeric** – e.g., `[1]`, `[2, 3]`, `[4–6]`

Results are shown in a modern UI (dark/light theme) and can be **downloaded as CSV**.

---

## ✨ Features

- Drag-and-drop **DOCX** upload
- Extracts **APA parenthetical**, **APA narrative**, and **IEEE numeric** citations
- **CSV export** (`Type, Citation, Count`)
- Responsive, modern UI with **dark/light toggle**
- No persistent storage of document contents (files are processed in memory)

---

## 🧰 Tech Stack

- **Backend:** FastAPI (ASGI), Uvicorn  
- **Templating:** Jinja2  
- **Parsing:** `python-docx`  
- **Upload handling:** `python-multipart`  
- **Frontend:** Plain HTML/CSS/JS (no framework)

---

## 📁 Project Structure

```
citations_web/
├─ app.py
├─ requirements.txt
├─ templates/
│  ├─ base.html
│  ├─ index.html
│  └─ results.html
└─ static/
   ├─ styles.css
   └─ script.js
```

- `templates/base.html` includes the footer.
- `app.py` exposes routes and the citation extraction logic.

---

## 🚀 Quick Start

> Requires **Python 3.9+**.

### Windows (PowerShell)
```powershell
cd .\citations_web\
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt

# Run the app
python -m uvicorn app:app --reload --port 8001
```
Open: **http://127.0.0.1:8001**

### macOS / Linux
```bash
cd citations_web
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Run the app
python -m uvicorn app:app --reload --port 8001
```
Open: **http://127.0.0.1:8001**

---

## 🖱️ Using the App

1. Visit **/** (home page).  
2. Drag & drop or choose a **.docx** file.  
3. Click **“Extract citations”**.  
4. See lists for each style + a consolidated table.  
5. Click **Download CSV** to save `citations_<yourfile>.csv`.

> Don’t browse directly to `/extract` — it’s a **POST** endpoint that expects a file upload.

---

## 🔌 API Endpoints

| Method | Path               | Description                                  |
|-------:|--------------------|----------------------------------------------|
|  GET   | `/`                | Home page (upload form)                      |
|  POST  | `/extract`         | Accepts `multipart/form-data` with `.docx`   |
|  GET   | `/download/{name}` | Download generated CSV                       |
|  GET   | `/static/*`        | Static assets (CSS/JS)                       |

---

## 🧠 How Extraction Works

- **APA (parenthetical)**: grabs text inside parentheses that includes a year (`(19xx|20xx[a]?)`), splits multiple citations by `;`, removes page refs (e.g., `p. 23`), normalizes `and` → `&`, and trims after the year.  
- **APA (narrative)**: matches `Author (Year)` or `Author & Author (Year)` and `Author et al. (Year)`.  
- **IEEE numeric**: extracts bracketed references, expanding ranges like `[4–6]` into `[4], [5], [6]`.

All results are deduplicated and summarized as:
```
Type, Citation, Count
APA parenthetical, Fawcett, 2006, 1
APA narrative, Hinton & Salakhutdinov, 2006, 1
IEEE numeric, [3], 1
...
```

---

## 🧪 Example (cURL)

```bash
curl -F "file=@/path/to/your.docx" http://127.0.0.1:8001/extract
```
This returns the rendered HTML page. CSV is available via the **Download** button.

---

## 🐛 Troubleshooting

- **500 Internal Server Error after upload**
  - Ensure the dependencies are installed in the **same environment**:
    ```bash
    pip install python-docx python-multipart jinja2 pandas fastapi uvicorn
    ```
  - Check Jinja syntax in templates; blocks must end with `'%}'` (not `'%>'`).

- **“Method Not Allowed” on `/extract`**  
  `/extract` expects **POST** from the form; use the home page to upload.

- **Nothing detected**  
  The extractor looks for **standard APA/IEEE patterns**. Non-standard formatting or scanned PDFs converted to DOCX may not parse cleanly.

---

## 🔐 Privacy Note

Documents are parsed in memory; the app does not persist or transmit your content. Generated CSVs are stored locally under `exports/` for download.

---

## 🐳 Optional: Docker

```dockerfile
# Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY . /app
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 8001
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8001"]
```

Build & run:
```bash
docker build -t citation-extractor .
docker run -p 8001:8001 citation-extractor
```
Open: http://127.0.0.1:8001

---

## 📄 License

MIT — feel free to reuse and adapt (keep the credit line in the footer).

---

## 🙌 Credits

UI & implementation: Design by ©John Oni — [LinkedIn](https://www.linkedin.com/in/johnoni4/)
