# Gastroscopy Image Annotation Workstation

A browser-based annotation tool for gastroscopy images. It helps clinicians load an image folder, review images one by one, assign an anatomical location, select visual findings, and export the final annotations as an Excel file or a complete ZIP package.

The app is built with FastAPI and is designed to run locally, in Docker, or on a third-party hosting platform.

## Features

- Select an image folder directly from the browser.
- Review gastroscopy images one by one.
- Label each image with one anatomical location.
- Select one or more visual findings for each image.
- Automatically enforce mutual exclusion between strong and weak versions of the same finding.
- Save an annotation and move to the next image automatically.
- Go back to the previous image and undo its saved result.
- Move low-quality or unwanted images to the deletion candidate group.
- Jump between images from the status list.
- Generate `图片信息.xlsx` automatically.
- Download either the Excel file or a complete annotation result ZIP package.

## Supported Images

The upload workflow accepts:

- `.png`
- `.jpg`
- `.jpeg`

Nested folders are supported. Relative paths are preserved in the session workspace and exported result folders.

## Local Development

1. Install Python dependencies:

```bash
pip install -r requirements.txt
```

2. Start the development server:

```bash
uvicorn app:app --host 127.0.0.1 --port 8000 --reload
```

3. Open the app in your browser:

```text
http://127.0.0.1:8000
```

## Docker

1. Build the image:

```bash
docker build -t gastro-labeler:latest .
```

2. Run the container:

```bash
docker run --rm -p 8000:8000 gastro-labeler:latest
```

3. Open the app in your browser:

```text
http://127.0.0.1:8000
```

## Annotation Workflow

1. Click **Select image folder** and choose a folder in the browser dialog.
2. Click **Load to workspace** to upload the images into the current session.
3. For each image:
   - Choose one anatomical location.
   - Select one or more visual findings.
   - Click **Save and next**.
4. If an image should be excluded from the selected set, click **Move out of batch**.
5. Use **Previous** to go back and undo the saved result for the previous image.
6. After all images are processed, download:
   - **Excel file**
   - **Result ZIP package**

## Output Structure

During runtime, the app creates a session workspace under `session_data/`.

Each session may contain:

```text
session_data/
  <session-id>/
    原始图片/
    拟入选/
    拟删除/
    图片信息.xlsx
    标注结果.zip
```

The generated Excel file stores:

- Image sequence number
- Image name
- Anatomical location
- Finding scores

Finding scores are encoded as:

- `0`: not selected
- `1`: weak finding selected
- `2`: strong finding selected

## Hosting Notes

- The app uses a browser upload workflow and does not require desktop-only file dialogs such as `tkinter`.
- It can be hosted with Docker, a PaaS provider, or a self-managed server.
- Runtime session files are written to `session_data/`.
- `session_data/`, `__pycache__/`, and Python bytecode files are excluded from Git and Docker build contexts.
- Uploaded images are kept in the server-side session workspace until the session is reset or the workspace is cleaned up.

## Health Check

A simple health endpoint is available:

```text
GET /health
```

It returns:

```json
{"ok": true}
```
