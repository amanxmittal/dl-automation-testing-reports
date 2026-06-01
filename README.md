# DigiLocker QA Report Portal

A lightweight static website to host and browse daily Selenium/Extent automation test reports. Hosted on **Netlify** from a **GitHub** repository.

---

## 📁 Repository Structure

```
digilocker-reports-portal/
├── index.html              ← Home page (report list + filters)
├── reports.json            ← Manifest: updated manually each day ⚠️
├── netlify.toml            ← Netlify headers & config
├── README.md
└── reports/
    └── YYYY/
        └── MM/
            └── DD/
                └── report.html   ← The Extent report HTML file
```

**Example:**
```
reports/2026/06/01/report.html   → Run on June 1 2026
reports/2026/05/25/report.html   → Run on May 25 2026
```

---

## 🚀 Initial Setup (One-Time)

### 1. Create GitHub Repository

```bash
git init
git remote add origin https://github.com/YOUR_ORG/digilocker-reports.git
git add .
git commit -m "chore: initial portal setup"
git push -u origin main
```

### 2. Deploy to Netlify

1. Go to [netlify.com](https://netlify.com) → **Add new site → Import from Git**
2. Connect your GitHub account and select this repository
3. Build settings:
   - **Build command:** *(leave empty)*
   - **Publish directory:** `.` (the repo root)
4. Click **Deploy site**
5. Optionally: set a custom domain in Site Settings

---

## 📋 Daily Workflow (Adding a New Report)

Follow these steps every time a new test run is complete:

### Step 1 — Place the report file

Create the folder for today's date and copy the report HTML in:

```
reports/YYYY/MM/DD/report.html
```

Example for **June 5 2026**:
```bash
mkdir -p reports/2026/06/05
cp DL_extentReoprtFri_Jun_05_...IST_2026.html reports/2026/06/05/report.html
```

> ⚠️ Always rename the file to `report.html` inside the date folder.

### Step 2 — Update `reports.json`

Open `reports.json` and **prepend a new entry** at the top:

```json
[
  {
    "date": "2026-06-05",
    "label": "Fri, Jun 05 2026",
    "path": "reports/2026/06/05/report.html",
    "pass": 83,
    "fail": 9,
    "skip": 8,
    "total": 100
  },
  ... existing entries ...
]
```

| Field   | Format            | Notes                                  |
|---------|-------------------|----------------------------------------|
| `date`  | `YYYY-MM-DD`      | Used for filtering and sorting         |
| `label` | `Day, Mon DD YYYY`| Displayed in the header                |
| `path`  | relative URL      | Must match the folder you created      |
| `pass`  | number            | Count of `status="pass"` in the report |
| `fail`  | number            | Count of `status="fail"` in the report |
| `skip`  | number            | Count of `status="skip"` in the report |
| `total` | number            | pass + fail + skip                     |

**Quick way to get counts from the report HTML:**
```bash
grep -c 'status="pass"' report.html
grep -c 'status="fail"' report.html
grep -c 'status="skip"' report.html
```

### Step 3 — Commit and push

```bash
git add reports/2026/06/05/ reports.json
git commit -m "report: add 2026-06-05 run (83P/9F/8S)"
git push
```

Netlify will auto-deploy within ~30 seconds. That's it! 🎉

---

## 🎛 Portal Features

- **Report list** — All runs listed with pass/fail/skip badges and a visual progress bar
- **Date filter** — Pick any specific date using the date picker
- **Sort options** — Sort by Newest, Oldest, or Pass Rate
- **Summary cards** — Latest run stats at a glance (top of page)
- **Direct links** — Each card opens the full Extent HTML report in a new tab

---

## 🔍 Tips

- The summary cards at the top always reflect the **most recent** run in `reports.json`
- Failed tests shown in the portal are **expected** — they've been manually verified
- If a report is missing, check that both the file exists AND `reports.json` has been updated
- Keep `reports.json` sorted newest-first (the portal re-sorts anyway, but it's cleaner to read)

---

## 🛠 Tech Stack

| Layer       | Technology                  |
|-------------|-----------------------------|
| Reports     | Selenium + Extent Reports   |
| Portal UI   | Vanilla HTML/CSS/JS (static)|
| Hosting     | Netlify (free tier)         |
| Source      | GitHub                      |
| No build    | Zero build step required    |