# 📊 Options Data Harvester

This project collects historical options data for a list of tickers using the Yahoo Finance API via `yfinance`, filters it based on user-defined parameters (moneyness, DTE, etc.), and saves the data to an Excel file. The resulting file can optionally be uploaded to Google Drive using a service account.

---

## 🔧 Features

- Pulls filtered call or put chains from Yahoo Finance  
- Filters by  
  - Moneyness bounds (e.g., ±20 % around current price)  
  - Maximum days to expiration  
- Writes each run into a new Excel worksheet  
- Safe to run repeatedly (auto-overwrites matching sheet names)  
- Can upload results directly to Google Drive  

---

## 📁 File Structure

```
.
├── options_test_run.py      # Main script to fetch and write options data
├── gdrive_uploader.py       # Handles uploading to Google Drive
├── requirements.txt         # Python dependencies
└── README.md                # Project documentation
```

---

## ⚙️ Requirements

Install all required packages:

```bash
pip install -r requirements.txt
```

---

## How to Use

1. **Edit Ticker List and Parameters**  
   Open `options_test_run.py` and modify the values under **User settings**.

2. **Run the script**

   ```bash
   python options_test_run.py
   ```

   The command will:

   - Download and filter options data for the defined tickers  
   - Save the data to `opts.xlsx`  
   - Optionally upload the file to Google Drive if `gdrive_uploader.py` is configured and invoked  

---

## ☁️ Google Drive Integration (Optional)

1. **Enable the Drive API** in Google Cloud Console.  
2. **Create a service account**, download the JSON key, and share your Drive folder with the service-account email.  
3. **Save** the key locally as `gdrive_key.json`.  
4. **Set** the folder ID in `gdrive_uploader.py` and call `upload_to_drive()` after writing the workbook.

```python
from google.oauth2 import service_account
from googleapiclient.discovery import build
from googleapiclient.http import MediaFileUpload

KEYFILE = "gdrive_key.json"          # Path to JSON key
FOLDER_ID = "your_drive_folder_id"   # From Drive URL

def upload_to_drive(local_path: str):
    creds = service_account.Credentials.from_service_account_file(
        KEYFILE,
        scopes=["https://www.googleapis.com/auth/drive.file"]
    )
    drive = build("drive", "v3", credentials=creds)

    metadata = {"name": local_path.split("/")[-1], "parents": [FOLDER_ID]}
    media = MediaFileUpload(
        local_path,
        mimetype="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
    )
    drive.files().create(body=metadata, media_body=media).execute()
```

---

## 🧪 Next Steps

- Automate runs with Render, GitHub Actions, or another scheduler  
- Schedule hourly execution from 10 AM – 4:30 PM ET  
- Push results to cloud storage or a database for long-term analysis  
- Expand filtering logic or add new analytics columns  

---

## 📄 License

MIT License — freely use, modify, and distribute.
