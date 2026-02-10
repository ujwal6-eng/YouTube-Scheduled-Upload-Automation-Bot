# YouTube Scheduled Upload Automation Bot

## Overview
This system automates scheduled uploads of short-form videos to YouTube.
Videos are sourced from Google Drive, metadata is consumed from a versioned text file, and publishing is scheduled without manual intervention.
The design prioritizes deterministic behavior, auditability, and long-term unattended operation.

## Architecture
Google Drive (Pending Videos)
        ↓
GitHub Actions (Cron Trigger)
        ↓
Python Automation (main.py)
        ↓
YouTube Data API (Scheduled Upload)
        ↓
Google Drive (Uploaded Videos)
        ↓
GitHub Commit (Metadata Update)

The workflow is time-triggered, processes one video per run, and advances state only after a successful upload.

## Repository / Resource Structure
yt-upload-bot/
├── .github/workflows/upload.yml
├── main.py
├── requirements.txt
├── titles.txt
└── README.md

Google Drive folders act as external state boundaries.
The repository stores only automation logic and metadata.

## Workflow
1. GitHub Actions triggers the job based on a cron schedule.
2. The repository is checked out into a clean runner.
3. Python dependencies are installed.
4. The first unused title line is read from titles.txt.
5. One video is selected from the Drive pending folder.
6. The video is uploaded to YouTube with a scheduled publish time.
7. The video is moved to the Drive uploaded folder.
8. The used title line is removed and committed.

## Automation Logic
- Triggered exclusively by GitHub Actions cron.
- One video processed per run.
- FIFO metadata consumption.
- No retries to avoid duplicate uploads.
- Fail-fast behavior to preserve consistency.

## External Integrations
- Google Drive API: Lists, downloads, and moves video files.
- YouTube Data API v3: Uploads videos and schedules publication.
- GitHub Actions: Orchestration, scheduling, and execution environment.

## State & Metadata Handling
- titles.txt acts as a persistent metadata queue.
- Google Drive folders represent processing state.
- Successful completion advances both Drive and repository state.
- Duplicate processing is prevented by state transitions only after success.

## Security Considerations
- OAuth tokens are generated offline and stored as GitHub Secrets.
- No credentials are committed to the repository.
- Minimal required OAuth scopes are used.
- Secrets are injected only at runtime.
- No interactive authentication during execution.

## Tech Stack
- Python 3.11
- Google API Python Client
- GitHub Actions
- Google Drive API
- YouTube Data API v3
- OAuth 2.0

## Roadmap
- Multi-video batch runs
- Multi-channel support
- Per-video scheduling configuration
- Failure notifications
- Metadata validation tooling

## Final Notes
The system is designed for predictable execution and easy auditing.
All state transitions are explicit and observable.
The architecture favors simplicity, reliability, and operational clarity.
