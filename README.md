# Bhavya_MCP_Server

A standalone Model Context Protocol (MCP) server that provides tools for interacting with Google Docs and Gmail.

This server is designed to run in two modes:
1. **Production Mode:** Connects to real Google Docs and Gmail APIs using OAuth or Service Account credentials.
2. **Simulation Mode:** Automatically falls back if credentials are missing or if `USE_MOCK_GOOGLE=true`. Writes outputs locally to `data/outputs/` inside the server context.

## Exposed Tools
* `append_to_google_doc`: Append or update a weekly review pulse entry in a Google Doc.
* `create_gmail_draft`: Create an email draft in Gmail.

## Environment Variables
* `PORT`: The port to run the server on (default: `3001`).
* `HOST`: The host to run the server on (default: `0.0.0.0` if port is present, else `127.0.0.1`).
* `GOOGLE_SERVICE_ACCOUNT_FILE`: Path to Google Service Account JSON file.
* `GOOGLE_TOKEN_JSON_CONTENT`: JSON string content of the Google OAuth `token.json` file (useful for cloud platforms like Railway where files can't easily be uploaded).
* `USE_MOCK_GOOGLE`: Set to `true` to force simulation mode.

## Deploying on Railway
This repository can be deployed directly to Railway. It will automatically build the Docker image and listen on the `$PORT` environment variable injected by Railway.
