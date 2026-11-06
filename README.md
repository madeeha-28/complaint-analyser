# Cybercrime Complaint Analyzer

A Python prototype that reads online-fraud complaints, pulls out the key details (phone numbers, UPI IDs, links, emails, crypto wallets, amounts), groups complaints that share a detail, and shows the results on an interactive dashboard.

> **Important:** this project uses **synthetic (made-up) data only**. No real complaints, people, phone numbers, or accounts are included. It is a learning prototype, not a tool used on real cases.

## The problem

A single scammer can cheat many people, and each victim files a separate complaint. On paper these look like unrelated cases. But the complaints often share details: the same phone number, the same UPI ID, the same fake website, or the same crypto wallet.

Finding those shared details by hand across hundreds of complaints is slow. This tool does it automatically and shows the connections visually, so an investigator can see which complaints are probably linked.

## What it does

| Step | Script | What happens |
|---|---|---|
| 1. Extract and classify | `extractor.py` | Finds phone numbers, UPI IDs, emails, websites, crypto wallets, and amounts in each complaint, and labels the fraud type |
| 2. Link cases | `linker.py` | Groups complaints that share any detail into clusters |
| 3. Visualize | `dashboard.py` | Builds one web page with a network graph and charts |

**Fraud types detected (keyword rules):** impersonation / KYC fraud, investment / crypto scam, job scam, loan app fraud, sextortion / blackmail, online shopping fraud.

## Demo

On the 12 synthetic complaints in `complaints.txt`, the tool finds two clusters:

- **Cluster 1:** 8 complaints linked through shared phone numbers, UPI IDs, a website, and an email, spanning three fraud types.
- **Cluster 2:** 2 complaints for a crypto scam, linked by a phone number, a website, and a wallet.

Two complaints share nothing with the others and stay unlinked.

![Dashboard](screenshots/dashboard.png)

![Network graph](screenshots/network-graph.png)

## How to run

**Requirements:** Python 3.10 or newer.

```powershell
git clone https://github.com/madeeha-28/complaint-analyzer.git
cd complaint-analyzer
python -m venv venv
venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Run the three scripts in order:

```powershell
python extractor.py
python linker.py
python dashboard.py
start dashboard.html
```

Each script feeds the next: `extractor.py` creates `extracted.json`, `linker.py` creates `clusters.json`, and `dashboard.py` reads both and creates `dashboard.html`.

## Input format

`complaints.txt` has one complaint per line, written as `ID|text`:

```text
C001|A person called from +91 90000 00001 claiming to be from SBI ...
```

To try your own synthetic complaints, add lines in the same format.

## How it works

- **Extraction:** regular expressions describe what each detail looks like (for example, an Indian mobile number is 10 digits starting with 6 to 9). Each detail is crossed out once found, so an email is not mistaken for a UPI ID.
- **Normalizing links:** different versions of the same link (`http://site.example/page` and `www.site.example`) are reduced to the website name so they match.
- **Classification:** each fraud type has a list of keywords, and the type with the most matches wins.
- **Linking:** complaints that share any detail are merged into one group using union-find.
- **Dashboard:** `networkx` builds the graph and `plotly` draws it, together with summary charts, in a single HTML file.

## Limitations

- Built and tested on **synthetic data**. Real complaints are messier (typos, Hindi or Marathi text, mixed formats) and would need extra work.
- The fraud type comes from **simple keyword rules** and can be wrong.
- Linking by shared details can **over-link**, for example if victims mention a bank's real helpline number. A person must verify every link. The tool only suggests leads.
- Bank account numbers and other identifiers are not extracted yet.
- Input is a plain text file, not a spreadsheet or database.

## Next steps

- Read complaints from CSV or Excel files
- Extract bank account numbers and other identifiers
- Improve classification, for example with a trained text model
- Handle Hindi and Marathi text
- Highlight repeat accounts used across different fraud types
- Generate a one-page PDF report for each cluster

## Data and privacy

Do not add real complaint data, personal details, or case information to this repository. Keep any real data on approved systems only.

## Tech stack

Python, regular expressions, networkx, plotly
