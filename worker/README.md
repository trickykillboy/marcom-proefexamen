# MARCOM study chatbot — Cloudflare Worker

This Worker connects the public MARCOM study page to OpenAI without exposing an API key in the page source. It only accepts requests from `https://trickykillboy.github.io`, limits each client to 8 requests per minute (Cloudflare's edge rate limit is approximate), caps message sizes, and sends `store: false` to the Responses API. Chat messages stay in the browser's current session; the page does not save the conversation.

## Deploy

1. Install Node.js if needed, then open a terminal in this folder.
2. Sign in to Cloudflare:
   ```sh
   npx wrangler login
   ```
3. Add your OpenAI API key as an encrypted Cloudflare secret. Enter it only at the prompt; do not paste it into this repository or the website:
   ```sh
   npx wrangler secret put OPENAI_API_KEY
   ```
4. Deploy the Worker:
   ```sh
   npx wrangler deploy
   ```
5. Copy the `workers.dev` URL printed after deployment. On the study page, open the chatbot, choose its connection settings, and paste that URL. The page stores only the public Worker URL in this browser.

The Worker uses OpenAI's `gpt-6-luna` model via the Responses API. OpenAI API usage is billed separately from a ChatGPT subscription and may incur usage charges. Check the [current model prices](https://developers.openai.com/api/docs/pricing) before enabling it. The Worker limits each answer to 500 output tokens.

## Cloudflare setup

The Wrangler configuration creates a per-client rate-limit binding at 8 requests per 60 seconds. Cloudflare describes these limits as approximate and local to each Cloudflare location. If you later make the page available to other people, review the limit and consider adding Turnstile or a stricter abuse-control layer. The CORS allowlist is intentionally restricted to the published GitHub Pages origin.

## Local development

For local browser development, temporarily add your local origin (for example `http://localhost:8788`) to `ALLOWED_ORIGINS` in `src/index.js`. Do not deploy that extra origin unless you intend to support it.
