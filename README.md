# Captcha Solver Web App

A lightweight web app that fetches a CAPTCHA image from a provided URL, displays it, and attempts to solve it within 15 seconds. If no URL is provided, the app defaults to an attached sample image.

This project is intended as a demo scaffold for CAPTCHA handling and evaluation workflows.

## Features

- Accepts a CAPTCHA image via query string parameter: `?url=https://.../image.png`
- Displays the provided CAPTCHA image on page load
- Automatically attempts to solve the CAPTCHA and shows the predicted text within 15 seconds
- Fallback to a bundled sample image when no URL is provided or fetch fails
- Minimal, dependency-light front-end implementation

## Demo

- Navigate to the running app in your browser.
- Provide a URL to a PNG/JPG/GIF image via `?url=`:
  - Example: `http://localhost:5173/?url=https://example.com/captcha.png`
- The page will:
  - Load and display the CAPTCHA image
  - Show a loading indicator while solving
  - Display the predicted text or an error status within 15 seconds

## Project Structure

- `public/`
  - `index.html` — Main page shell
  - `sample-captcha.png` — Default sample CAPTCHA used when no URL is provided
- `src/`
  - `main.js` — App bootstrap, URL parsing, rendering logic
  - `solver.js` — Pluggable solver function (mock/heuristic or model-backed)
  - `ui.js` — DOM updates for image, status, and results
  - `utils.js` — Helpers for timeouts, image loading, errors
- `LICENSE` — MIT License
- `README.md` — This document

Note: File names are illustrative; adapt to your preferred stack/build tool.

## Getting Started

### Prerequisites

- Node.js >= 18
- A package manager (npm, pnpm, or yarn)

### Install

- npm: `npm install`
- pnpm: `pnpm install`
- yarn: `yarn install`

### Run (Development)

- npm: `npm run dev`
- pnpm: `pnpm dev`
- yarn: `yarn dev`

This starts a local dev server (e.g., Vite) and opens the app in your browser.

### Build (Production)

- npm: `npm run build`
- pnpm: `pnpm build`
- yarn: `yarn build`

### Preview (Production Build)

- npm: `npm run preview`
- pnpm: `pnpm preview`
- yarn: `yarn preview`

## Usage

- With a remote CAPTCHA URL:
  - `http://localhost:5173/?url=https://your-domain.com/captcha.png`
- Without a URL:
  - `http://localhost:5173/`
  - The app will load `public/sample-captcha.png`

Notes:
- Cross-origin image URLs must permit anonymous fetching. Ensure the remote server allows CORS or host the image locally.
- Supported types: PNG, JPG/JPEG, GIF (static). Animated GIFs will be treated as a static frame.

## Solver Behavior

- Time budget: 15 seconds total from image load to prediction display.
- Result states:
  - Solved: Displays predicted text
  - Timeout: Shows a timeout message if no result within 15 seconds
  - Error: Shows a user-friendly error (e.g., image load failure, network errors)

### Default Solver

The default solver is a simple placeholder designed for demonstration. It can:
- Return pre-configured text for the bundled sample image
- Run a basic heuristic or mock OCR
- Be replaced with a real OCR/ML model

### Implementing a Custom Solver

Edit `src/solver.js` to export an async function:

```
export async function solveCaptcha(imageElement, { signal, deadlineMs }) {
  // imageElement is an HTMLImageElement already loaded in the DOM
  // signal is an AbortSignal that fires at timeout
  // deadlineMs is the absolute deadline timestamp

  // Implement your logic (e.g., canvas preprocessing + OCR)
  // Respect AbortSignal to stop long-running work

  return "ABC123"; // Example
}
```

Recommendations:
- Use a canvas to extract image data and apply preprocessing (grayscale, thresholding, denoising).
- Run OCR via:
  - Tesseract.js (WebAssembly) in the browser
  - A backend API using a more capable OCR or custom model
- Always handle `AbortError` to respect the 15-second constraint.

## Environment Variables (Optional)

If using a backend solver service, configure:

- `VITE_SOLVER_API_URL` — URL of the API endpoint
- `VITE_SOLVER_API_KEY` — API key or token (do not commit secrets)

Example usage in `src/solver.js`:

```
const API_URL = import.meta.env.VITE_SOLVER_API_URL;
const API_KEY = import.meta.env.VITE_SOLVER_API_KEY;
```

## Accessibility and UX

- Clear status messages (loading, solved, timeout, error)
- Keyboard focus management for the result area
- Alt text for the CAPTCHA image (includes source URL when available)

## Testing

- Manual:
  - Visit with `?url=` pointing to different CAPTCHAs
  - Verify image is displayed
  - Confirm predicted text appears within 15 seconds or a clear timeout message is shown

- Automated (example ideas):
  - Unit test `utils.js` for timeouts and error handling
  - Mock `solveCaptcha` and assert UI state transitions
  - E2E test with Playwright: navigate with a data URL and check result timing

## Security Considerations

- Do not log sensitive data from CAPTCHAs.
- Enforce image size/type checks if enabling user-provided URLs.
- Consider sandboxing or backend proxying for untrusted URLs.
- If deploying publicly, rate-limit backend solver endpoints.

## Deployment

- Static hosting (no backend): Host the built assets on Vercel, Netlify, GitHub Pages, or any static server.
- With a solver API: Deploy the frontend separately from the backend service; configure `VITE_SOLVER_API_URL`.

Steps:
1. Build: `npm run build`
2. Upload `dist/` to your hosting provider
3. Set environment variables as needed

## Compliance Checklist

- Repo has MIT license: LICENSE file included
- README.md is professional: This document
- Page displays captcha URL passed at `?url=...`: Implemented in `src/main.js`
- Page displays solved captcha text within 15 seconds: Enforced via timeout and solver contract

## Known Limitations

- Default solver is not production-grade; accuracy depends on CAPTCHA complexity.
- Cross-origin image loading may fail without proper CORS headers.
- Some CAPTCHAs require interaction (click/drag); this demo targets text CAPTCHAs.

## Roadmap

- Pluggable solver registry with multiple strategies
- Better preprocessing (binarization, morphological ops)
- Web Worker for OCR to keep UI responsive
- Server-side fallback rendering and OCR
- Telemetry for latency and accuracy (opt-in)

## Contributing

Contributions are welcome! Please open an issue to discuss proposed changes. For pull requests:
- Keep changes focused and tested
- Follow existing code style
- Update README and comments where necessary

## License

MIT License. See LICENSE for details.