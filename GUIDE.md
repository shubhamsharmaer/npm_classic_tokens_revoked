
<img width="1536" height="1024" alt="ChatGPT Image Dec 19, 2025, 10_35_43 PM" src="https://github.com/user-attachments/assets/c685d3ef-9726-4ae8-b5d2-d9826209151a" />

# 🚨 NPM Classic Tokens Are Permanently Revoked — Migration Guide (Dec 2025)

**Stay ahead of npm security changes and keep your workflows running smoothly.**

On **December 9, 2025**, npm permanently revoked all **classic authentication tokens** and introduced modern, more secure authentication flows including **session tokens** and **granular access tokens**. This affects how developers install private packages locally, and how CI/CD pipelines authenticate with npm. :contentReference[oaicite:0]{index=0}

---

## 🔍 What Changed (npm Security Update Overview)

- ❌ **Classic tokens are permanently revoked.** They no longer work for authentication, publishing, or `npm install`.  
- 🔑 New **session-based authentication** replaces classic tokens for local workflows — created via `npm login` and valid for a limited time (~2 hours).  
- 🛠️ **Granular access tokens** are the new standard for CI/CD workflows and automation, with enforced 2FA and expiry limits.  
- 🔐 2FA enforcement and new CLI tools for granular token management are part of the security hardening plan to protect the npm ecosystem.

---

## 🔄 Old vs New Authentication (Quick Comparison)

| Feature | Old Setup (Classic Tokens) | New Setup (Post-Dec 2025) |
|--------|----------------------------|----------------------------|
| Token Type | Long-lived classic tokens | Session tokens + granular access tokens |
| Local dev auth | Classic tokens or saved auth | `npm login` → 2-hour session |
| CI / Docker auth | Classic tokens in env | Granular access token |
| Token creation | `npm token create` generated classic tokens | Granular tokens via CLI or npmjs.com |
| 2FA | Optional | Enforced on write tokens |
| Expiry | Unlimited | Session (~2h) / Granular (max 90 days) |

---

## ✅ What You MUST Do?

### 🧑‍💻 Local Development

To install private/public packages and run your app locally:

```bash
npm login
# Enter you npm profile password
npm install
npm run dev
```
 - `npm login` creates a temporary session token
 - Valid for ~2 hours
 - No token storage required
 - Best for local development
This creates a session token valid for a short time — enough for local workflows.

`💡 If npm install suddenly fails locally — you likely just need to log in again.`

### 🐳 Production / CI & Docker Setup

1. Create a Granular Access Token
  - Go to your npm account “Access Tokens” section.
  - Generate a Granular Access Token.
  - Assign read or read/write scope as needed.
  - Optionally enable Bypass 2FA for noninteractive automation.
  
2. Dockerfile & docker-compose.yml file changes
  - Dockerfile Changes
    
     ```yml
      ARG NPM_TOKEN
      RUN echo "//registry.npmjs.org/:_authToken=${NPM_TOKEN}" > .npmrc
      
      RUN rm -f .npmrc # To prevent your token from leaking 
     ```
  - docker-compose.yml Changes
    
      ```yml
      services:
        your_service:
          build:
            context: .
            args:
              - NPM_TOKEN=${NPM_GRANULAR_TOKEN}
          environment:
            - NODE_AUTH_TOKEN=${NPM_GRANULAR_TOKEN}
       ```
    `🔐 Always inject tokens via environment variables or secrets — never hardcode.`

## 🧩 Summary
 - Local development: Use `npm login` → session token
 - CI/CD / Docker: Migrate to granular access tokens
 - Builds: Inject tokens in Docker builds via build args, remove after use

These changes protect your projects and the entire npm ecosystem from token leakage and supply chain attacks.

## 📌 Reference
- [Official npm blog: npm classic tokens revoked, session-based auth and CLI token management now available (Dec 9, 2025)](https://github.blog/changelog/2025-12-09-npm-classic-tokens-revoked-session-based-auth-and-cli-token-management-now-available/)

## 👋🏻 Keep Coding Thank You [Shubham Sharma](https://www.linkedin.com/in/shubhamsharmaer/)
