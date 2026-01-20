## Oako AI (Experimental Build)

**Status**: Experimental project to test front-end compilation security

This repository contains a compiled production build of an OpenAI-powered AI assistant web app.

---

## Experimental Finding: API Keys Are NOT Safe in Compiled Front-End Code

**What we tested:**
- Embedded an OpenAI API key directly in the React source code
- Compiled the app using Vite (production build with minification)
- Checked if the key was still visible in the compiled bundle

**Result:**
❌ **The API key was still visible in the compiled JavaScript**, even after minification and bundling.

**Lesson learned:**
- **Never store API keys in front-end code** (React, Vue, Angular, etc.)
- Compilation and minification do NOT hide secrets
- Anything shipped to the browser is public and can be extracted

---

## The Right Way to Handle API Keys

✅ **Keep all API keys on the backend:**
- Create a backend API (Node.js/Express, Python/Flask, etc.)
- Store the OpenAI key in **server-side environment variables** (`.env` file that's gitignored)
- Your React app calls **your backend**, which then calls OpenAI with the key

**Example flow:**
```
User → React App → Your Backend API (has the key) → OpenAI API
```

This way, the key never touches the browser/front-end code.

---

## How to Check Your Own Project for Exposed Secrets

If you have a compiled front-end build, you can search for common API key patterns in the bundled JavaScript:

**Common patterns to search for:**
```bash
# PowerShell (Windows)
Select-String -Path .\static\js\*.js -Pattern "sk-|bearer|apikey|api_key|token"

# Bash/Terminal (Mac/Linux)
grep -r "sk-\|bearer\|apikey\|api_key\|token" static/js/
```

**What to look for:**
- OpenAI keys: `sk-...`
- Bearer tokens: `Bearer ...`
- Generic API keys: `apiKey`, `api_key`, `API_KEY`
- AWS keys: `AKIA...`
- Any hardcoded credentials or tokens

If you find any matches, **immediately revoke those keys** and move them to backend environment variables.

---

## Note

- Original OpenAI API key in this project has been **revoked**
- This repo serves as a demonstration of why client-side secrets are unsafe
