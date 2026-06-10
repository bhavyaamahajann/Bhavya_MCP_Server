# Context

This repository contains a standalone Model Context Protocol (MCP) server that provides tools for interacting with Google Docs and Gmail.

## Components
- `server.py`: The main MCP server code.
- `authenticate.py`: Handles Google authentication (OAuth / Service Account).
- `Dockerfile`: Defines the Docker image for the project.
- `requirements.txt`: Python dependencies.

## Key Features
- Connects to Google Docs and Gmail APIs.
- Supports Production Mode (real APIs) and Simulation Mode (mock outputs).
- Exposed Tools: `append_to_google_doc`, `create_gmail_draft`.

## Implementation Step-by-Step

The project was developed following these key steps:

1. **Framework Selection and Setup**:
   - Built using the `FastMCP` framework (`mcp.server.fastmcp.FastMCP`), which natively handles routing, tools, and Model Context Protocol (MCP) transport (SSE and Standard I/O).
   - Added a standard `/` HTTP route using `starlette` for basic health checking.

2. **Google Authentication Integration (`authenticate.py` & `server.py`)**:
   - Built a standalone helper (`authenticate.py`) to run a local interactive OAuth InstalledAppFlow. This generates and caches a `token.json` file.
   - Built resilient authentication logic in `get_google_services()` within `server.py` that falls back across multiple credential strategies:
     - Forced mock simulation (`USE_MOCK_GOOGLE`).
     - Google Service Account JSON.
     - OAuth 2.0 `token.json` loading and refreshing.
     - Application Default Credentials (ADC).
     - A final fallback to "Simulation Mode" if no credentials exist.

3. **Google API Initialization**:
   - Configured the Google API Python Client (`googleapiclient.discovery`) to build connections to the Docs API (`v1`) and Gmail API (`v1`).

4. **MCP Tool Implementation (`@mcp.tool()`)**:
   - **`append_to_google_doc`**: 
     - Parses incoming weekly JSON payloads and formats them into a plain-text template.
     - Fetches the target Google Doc to scan its structural elements (paragraphs, tables) to locate start and end index offsets of existing text runs.
     - Idempotently deletes the old section block and inserts the new one via `batchUpdate`, or appends the block to the end of the document if it doesn't exist.
   - **`create_gmail_draft`**: 
     - Uses Python's `EmailMessage` to construct a MIME message.
     - Encodes the message to base64 and pushes it via the `users().drafts().create` Gmail endpoint.

5. **Simulation Mode Mocking**:
   - Built a fallback mechanism to write mock `.md` and `.txt` files directly to a local `data/outputs/` directory when running in Simulation Mode, allowing local testing without hitting rate limits or requiring active credentials.
