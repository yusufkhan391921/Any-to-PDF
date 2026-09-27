# AnyToPDF

AnyToPDF is a local-first file conversion workbench. Add files, arrange their order, choose page settings, then create one merged PDF or separate PDFs for each source file.

## Supported formats

- Images: JPG, PNG, WEBP, GIF, BMP, TIFF, SVG
- Documents: DOCX, TXT, RTF, HTML, Markdown
- Spreadsheets: XLSX, CSV
- PDFs: validate and merge existing PDFs

JPG, PNG, WEBP, BMP, and SVG conversion and PDF merging happen in the browser. Other supported formats are sent to the local FastAPI service. The server renders document text and tables into PDFs; document styling and embedded media may not be preserved exactly.

## Run locally

Requires Python 3.9 or newer and a browser with JavaScript enabled. The browser loads `pdf-lib`, `JSZip`, Lucide icons, and fonts from their public CDNs, so the first page load needs an internet connection. Uploaded files are not stored by the server.

```sh
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
cp .env.example .env
uvicorn server.main:app --reload
```

Open <http://127.0.0.1:8000>.

## Android APK

The Android WebView wrapper source is in `android/`. With JDK 17 and Android SDK 35 installed, build a debug APK from the project root:

```sh
cd android
./gradlew assembleDebug
```

Then enter the address of a running AnyToPDF server when the app opens. For a phone on the same trusted Wi-Fi, start the server with `uvicorn server.main:app --host 0.0.0.0 --port 8000` and enter the computer's LAN address, such as `http://192.168.1.10:8000`. Use HTTPS for an internet-hosted server. The APK uses Android's document picker and saves finished PDFs or ZIPs to `Downloads/AnyToPDF`.

The per-file upload limit defaults to 25 MB. Set `ANYTOPDF_MAX_UPLOAD_MB` in the environment to change it. Files converted in the browser have the same limit.

## Tests

```sh
python -m unittest discover -s tests -v
```

The tests cover PDF validity, image page orientation, DOCX/XLSX conversion, and invalid input handling.

## Notes

- Animated GIF and multi-page TIFF input produces one PDF page per image frame.
- GIF, TIFF, office documents, text/markup, and spreadsheets use the server conversion endpoint.
- Existing PDFs retain their original page size and orientation; page settings apply to newly converted files.
- Choosing **Per file** for multiple uploads downloads a ZIP containing one PDF for each source file.