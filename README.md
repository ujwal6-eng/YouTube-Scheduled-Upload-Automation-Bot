```markdown
# YouTube Scheduled Upload Automation Bot

## 1. Project Overview

**Project Name:** YouTube Scheduled Upload Automation Bot  

**One-line Goal:**  
Automate the process of uploading short-form videos from Google Drive to YouTube with predefined titles and hashtags, and schedule them for a fixed publish time using GitHub Actions.

This system is designed to run unattended, consume predefined metadata sequentially, and reliably schedule YouTube uploads without manual intervention.

---

## 2. System Architecture

### High-Level Flow

```

Google Drive (Pending Folder)
|
v
GitHub Actions (Scheduled Trigger)
|
v
Python Automation (main.py)
|
|-- Reads title + hashtags from titles.txt
|-- Downloads video from Drive
|-- Uploads to YouTube (Scheduled)
|-- Moves video to Uploaded Folder
|-- Updates titles.txt
v
YouTube (Scheduled Short)

```

---

## 3. Folder / Resource Structure

```

yt-upload-bot/
│
├── .github/
│   └── workflows/
│       └── upload.yml          # GitHub Actions workflow
│
├── main.py                     # Core automation logic
├── requirements.txt            # Python dependencies
├── titles.txt                  # Title + hashtag source (consumed sequentially)
├── README.md                   # Documentation

```

---

## 4. Step-by-Step System Workflow

1. GitHub Actions triggers the workflow at a fixed cron schedule.
2. The repository is checked out in a fresh runner environment.
3. Python dependencies are installed.
4. `main.py` is executed.
5. The script:
   - Reads the first unused line from `titles.txt`
   - Extracts the title (including hashtags)
   - Fetches one video from the Google Drive pending folder
6. The video is uploaded to YouTube as **Private** with a **scheduled publish time**.
7. The uploaded video is moved to the Drive “uploaded” folder.
8. The used title line is removed from `titles.txt`.
9. `titles.txt` is committed back to the repository.

---

## 5. Automation / Background Processing Logic

- Execution is fully controlled by GitHub Actions cron scheduling.
- Only one video is processed per run.
- Metadata consumption is deterministic (FIFO).
- Failures stop execution to prevent inconsistent state.
- No retry loops are used to avoid duplicate uploads.

---

## 6. Integration with External Services

### Google Drive API
- Used for listing, downloading, and moving video files.
- Requires full Drive scope for file movement operations.

### YouTube Data API v3
- Used to upload videos and schedule publish time.
- Videos are uploaded as private and scheduled via `publishAt`.

### GitHub Actions
- Acts as the scheduler and execution environment.
- Handles secrets and state persistence through commits.

---

## 7. Metadata / State Management Strategy

### Title Management
- `titles.txt` is treated as a queue.
- Each line represents one upload.
- Format:
```

Video title text | #hashtag1 #hashtag2 #hashtag3

```
- The first line is consumed per run and removed after success.

### Video State
- Google Drive folders act as state boundaries:
- Pending folder: unprocessed videos
- Uploaded folder: processed videos

This avoids reliance on local or database state.

---

## 8. Security & Best Practices

- OAuth tokens are generated offline and stored as GitHub Secrets.
- No credentials are committed to the repository.
- Minimal required scopes are used:
- Google Drive (full access)
- YouTube upload
- GitHub Actions secrets are injected only at runtime.
- No user input is accepted during execution.

---

## 9. Tech Stack

- Python 3.11
- Google API Python Client
- GitHub Actions (Ubuntu runner)
- Google Drive API
- YouTube Data API v3
- OAuth 2.0

---

## 10. Future Enhancements

- Multi-video batch uploads per run
- Multiple channel support
- Per-video scheduling configuration
- Failure notification via email or webhook
- Optional description templates
- Rate-limit and quota monitoring

---

## 11. Final Notes

This system is production-ready and designed for predictable, repeatable execution.  
It avoids dynamic AI dependencies, minimizes external state, and relies on explicit, auditable workflows suitable for long-term unattended operation.
```
