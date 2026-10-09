# Resume Tailor

Paste a company name and job description, and Claude writes a tailored LaTeX resume. The PDF renders live next to a chat where you ask for changes. Every version is kept, and you can open the result in Overleaf or download it.

Runs entirely in the browser as a static page, so it hosts on GitHub Pages with no backend.

## How it works

| Step | What happens |
|---|---|
| Generate | Your master resume, rules, the job description and a LaTeX template go to the Anthropic API. Claude returns a complete `.tex` document. |
| Preview | The `.tex` is posted to the free TeX Live compile service at texlive.net, and the PDF loads in the preview pane. |
| Chat | Each request sends the current document plus a short history. Claude returns the full updated document, the preview recompiles, and a new version is saved. |
| Export | **Open in Overleaf** creates a new Overleaf project from the current LaTeX. **Open PDF** opens the compiled PDF in a new tab (use the viewer's download button). **Download .tex** saves the source. |

Why the preview isn't Overleaf itself: Overleaf blocks being embedded in other sites and has no API that returns a PDF, so the app compiles on texlive.net for the live view and hands off to Overleaf in one click.

## Deploy to GitHub Pages

```bash
git clone https://github.com/saimanjunathk/resume-tailor.git   # after creating the empty repo on GitHub
cp index.html README.md resume-tailor/
cd resume-tailor
git add . && git commit -m "Resume Tailor" && git push
```

Then on GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch → `main` / `root` → Save**.
The app is live at `https://saimanjunathk.github.io/resume-tailor/` within a minute or two.

## First run

1. Get an API key at console.anthropic.com.
2. Open the app. Settings opens automatically.
3. Paste the key, your **master resume** (everything you've done, plain text or LaTeX), and adjust **Your rules**.
4. Optional: paste your own LaTeX template. Claude keeps its preamble and styling and fills the body.

## Security and privacy

- The API key is stored in this browser's localStorage only. It is never in the repo. Anyone else who opens your Pages URL sees an empty app and must use their own key.
- Don't use the app on a shared computer, or clear the key in Settings afterwards.
- Your resume and the job description are sent to Anthropic (generation) and texlive.net (compiling).
- If you later want to share the app without users bringing keys, put a small proxy (for example a Cloudflare Worker holding the key as a secret) in front of `api.anthropic.com` and change the `fetch` URL in `callClaude`.

## Tuning

| Want | Change in `index.html` |
|---|---|
| Different default model | `model` default in `settings`, or pick in Settings |
| Longer documents | `max_tokens` in `callClaude` |
| Stricter writing behavior | `BASE_PROMPT` |
| Different look | the `default-template` script block, or paste a template in Settings |

## Troubleshooting

| Symptom | Fix |
|---|---|
| Preview shows a log instead of a PDF | The LaTeX has an error. Copy the `!` error line into the chat and ask Claude to fix it. |
| `invalid x-api-key` | Re-enter the key in Settings. |
| `model not found` | Check the model name in Settings against the models your key can use. |
| PDF downloads as `document.pdf` | Rename it, or use Overleaf, which names the project `Resume_<Company>`. |
