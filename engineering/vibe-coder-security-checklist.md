# Vibe Coder Pre-Launch Security Checklist

> "If you launch without security, privacy, and abuse checks... you didn't ship a product. you shipped a liability."
> — @PrajwalTomar_, 2026-05-15

## Context

Vibe coding (AI-assisted rapid development) makes it easy to ship fast — but also easy to ship with critical gaps that can kill a product or create legal liability. Most vibe coders skip the "boring stuff" that keeps a product safe and sustainable.

This checklist is for AI builders launching products with real users.

## The Checklist

### Privacy & Legal
- [ ] Privacy policy (required if you collect any user data)
- [ ] Know exactly where user data is stored (jurisdiction matters)
- [ ] Cookie consent / GDPR compliance if serving EU users

### Security Headers & Scanning
- [ ] Check security headers (CSP, X-Frame-Options, HSTS, etc.)
- [ ] Scan against OWASP Top 10 basics
- [ ] Test for SQL injection and XSS vulnerabilities
- [ ] Verify authentication is solid (no broken auth patterns)

### Secrets & API Keys
- [ ] `.env` values are NOT leaking in client-side code
- [ ] API responses don't expose sensitive data by default
- [ ] Secrets removed from logs (no API keys in server logs)
- [ ] API keys server-side only — never in frontend code
- [ ] Keys behind a proxy or environment variable, not hardcoded

### Rate Limiting & Abuse
- [ ] Rate limits in place before someone burns your API bill
- [ ] Input validation on all user-facing endpoints
- [ ] File upload validation if applicable

## Related Concepts
- [[lean-engineering]] — Both about not skipping the "boring" essentials. The checklist is essentially lean engineering applied to pre-launch: delete the security debt before it becomes a liability.
- [[elon-musk-5-step]] — Step 2 (Delete) and step 3 (Simplify) are the core moves. The checklist enforces "don't skip the boring stuff" which aligns with Musk's philosophy that automation without deletion just makes failure faster.
- [[physics-first-principles]] — First-principles thinking says: don't inherit security assumptions from other projects, reason from first principles about what your specific product actually needs.

## Sources https://x.com/prajwaltomar_/status/2055294397475148123
- Author: @PrajwalTomar_ (Prajwal Tomar)
- Engagement: 5,600 likes, 540 retweets, 126 replies
- Date: 2026-05-15