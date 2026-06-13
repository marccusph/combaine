# Yellow Shoes 👟💛

An AI-powered fashion styling assistant. Snap a photo of any fashion item and
get instant outfit suggestions, a color palette, styling tips and shopping
links — in your chosen language, tuned to a style and season.

Built with plain HTML + React (via CDN, no build step) and a single Vercel
serverless function that calls the Anthropic API.

> Previously named *stailized*. The in-app branding is now **Yellow Shoes**
> (black / yellow / white identity). The GitHub repo is still `style-ai` — rename
> it on GitHub if you want the URL to match.

## How it works

1. Take a photo or upload an image of a fashion item.
2. The image is downscaled and re-encoded in the browser (so large phone photos
   don't fail), then sent to `/api/analyze`.
3. The serverless function asks Claude to return outfit suggestions as JSON,
   respecting the selected **language**, **style** and **season**, plus a short
   **search term per store** for the shopping links.
4. The app renders the looks, color palette, tips and "Shop the look" buttons.

## Project structure

```
index.html        # Front-end (React via CDN, image capture/compression, UI)
api/analyze.js    # Vercel serverless function -> Anthropic API
vercel.json       # Function config (memory / max duration)
```

## Deploy to Vercel

**1. Get an Anthropic API key** at https://console.anthropic.com/ →
*API Keys* → *Create Key*.

**2. Push this folder to GitHub** (`index.html`, `api/analyze.js`,
`vercel.json`, `README.md`).

**3. Import the repo on Vercel** → *Add New Project* → select the repository,
then add the environment variables below and click **Deploy**.

## Environment variables

| Name                   | Required | Default     | What it does                                                                 |
| ---------------------- | -------- | ----------- | ---------------------------------------------------------------------------- |
| `ANTHROPIC_API_KEY`    | **Yes**  | —           | Your Anthropic key. Without it the function returns a clear error.           |
| `ALLOWED_ORIGINS`      | No       | *(reflect)* | Comma-separated origins allowed to call the API, e.g. `https://yellow-shoes.vercel.app`. If unset, the request origin is reflected so it works out of the box. **Set this in production** to stop other sites using your key. |
| `RATE_LIMIT_MAX`       | No       | `20`        | Max requests per IP per window.                                              |
| `RATE_LIMIT_WINDOW_MS` | No       | `600000`    | Rate-limit window in milliseconds (default 10 min).                          |

## Security notes

- **CORS** is restricted to `ALLOWED_ORIGINS` when set (otherwise it reflects the
  caller's origin). Lock it to your domain in production.
- **Rate limiting** is best-effort and in-memory, so it limits *per serverless
  instance* and resets on cold starts — enough to deter casual abuse. For
  robust, global limits, back it with **Vercel KV** or **Upstash Redis**.

## Local development

```bash
npm i -g vercel
vercel dev
```

Set `ANTHROPIC_API_KEY` via a `.env` file or `vercel env`. Opening `index.html`
directly previews the UI, but `/api/analyze` needs the function (and the key) to
return suggestions.

## Configuration

- **Model** — `api/analyze.js` (`model: 'claude-sonnet-4-20250514'`).
- **Languages / styles / seasons / stores** — `index.html` (`SHOPS` controls the
  shopping links).
- **Image size** — `MAX_IMAGE_DIM` (1568px) and `JPEG_QUALITY` (0.85) in
  `index.html`.

## Ideas for later

- Translate the static UI, not just the AI output.
- Save / share generated looks.
- Move rate limiting to Vercel KV for global enforcement.

Made with 💛 using Claude.
