# Automated Video Upload & Website Integration System

## Overview
This system automates the end-to-end handling of video uploads from a website to YouTube.
It removes backend bandwidth pressure by uploading directly to external storage and processing asynchronously.
Automation is used to publish videos and return metadata for seamless website embedding.

The approach favors simple state transitions, observable processing, and minimal infrastructure.

## Architecture
Website
↓
Google Drive (Storage + Queue)
↓
GitHub Actions (Automation)
↓
YouTube (Private / Unlisted)
↓
Website Embed

Videos flow from the website to storage, where automation claims and processes them.
YouTube handles streaming, while the website only consumes metadata and embed links.

## Repository / Resource Structure
/video-system
- pending/        New uploads waiting for processing
- processing/     Videos currently being handled
- uploaded/       Successfully completed uploads
- failed/         Failed uploads requiring review

Folder placement represents system state.
A file exists in only one folder at a time, making state explicit and traceable.

## Workflow
1. The website uploads a video directly to Google Drive.
2. The file is placed into the pending folder as a queue entry.
3. GitHub Actions detects and claims the next pending file.
4. The file is moved to processing and uploaded to YouTube.
5. Metadata is generated and sent back to the website.
6. The file is finalized into uploaded or failed based on the result.

## Automation Logic
Automation is triggered by scheduled runs or manual dispatch.
Each run processes a limited number of items to control rate limits.
Folder transitions act as atomic state changes to maintain consistency.

## External Integrations
- Google Drive  
  Used for upload storage, queue management, and visual state tracking.
- GitHub Actions  
  Executes automation logic without requiring a persistent server.
- YouTube Data API  
  Publishes videos and provides streaming and embed capabilities.

## State & Metadata Handling
State is tracked through folder placement rather than database flags.
Duplicate processing is avoided by immediately moving files out of the pending state.
Metadata such as video ID and URLs is generated after upload and treated as authoritative.

## Security Considerations
Secrets and OAuth tokens are never exposed to the frontend.
All credentials are stored securely using GitHub Secrets.
API scopes are minimized to only what is required for uploads and metadata access.

## Tech Stack
- GitHub Actions
- Python
- Google Drive API
- YouTube Data API

## Roadmap
- Retry and backoff handling for failed uploads
- Persistent metadata storage
- Basic access control
- Monitoring and logging improvements

## Final Notes
The system prioritizes reliability through clear state transitions.
Each component is loosely coupled and replaceable without redesigning the workflow.
