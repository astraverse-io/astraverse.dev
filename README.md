# astraverse.dev

The Astraverse company site. One static page, no build step, no dependencies —
edit `index.html` and push.

Hosted on **Netlify**. `netlify.toml` sets the publish directory, security
headers, and the `www` → apex redirect.

## Why not GitHub Pages

It was on GitHub Pages first, and the certificate never issued. Over 24 hours
at `https_certificate.state: "authorization_pending"` with everything on our
side verified correct:

- exactly four Pages A records on the apex, confirmed against both
  authoritative nameservers
- `www` CNAMEd to `astraverse-io.github.io`
- no CAA records restricting any CA
- `http://astraverse.dev/.well-known/acme-challenge/…` returning 404 **from a
  GitHub IP**, so the challenge path was reachable
- Certificate Transparency showing no certificate ever issued for the apex

Two full remove-and-re-add cycles (including one with the domain unset for
three minutes, API-confirmed `cname: null`) each returned to
`authorization_pending` within a minute. A support ticket was closed with no
action. This matches a run of open community reports from mid-2026, one of them
stuck three weeks.

`.dev` is on the HSTS preload list, so this is not cosmetic: browsers refuse
plain HTTP, and a site with no certificate is unreachable rather than merely
insecure.

## DNS

**DNS stays at Namecheap.** That is the point of choosing Netlify over
Cloudflare Pages — no nameserver delegation, so the Private Email `MX` records
are never touched. Mail gates Apple enrolment; it does not get risked to fix a
website.

| Type | Host | Value |
|---|---|---|
| ALIAS (preferred, if Namecheap offers it) | `@` | `apex-loadbalancer.netlify.com` |
| A (fallback) | `@` | `75.2.60.5` |
| CNAME | `www` | *your-site*`.netlify.app` |

**Do not touch the MX records** (`mx1`/`mx2.privateemail.com`) or the SPF TXT
record. Changing the apex A record does not affect mail; deleting the wrong row
does.

Verify the apex resolves to Netlify and nothing else:

```bash
dig +short A astraverse.dev @dns1.registrar-servers.com
```

A leftover record here is what broke the previous host: Namecheap's parking
entry is a **URL Redirect Record**, not an A record, so it resolves to
`162.255.119.113` and looks like a stray A record in `dig` output while being
invisible in the A-record section of the UI. If certificate provisioning stalls
again, count the answers first.

## After Netlify is serving

Only once the site is live on Netlify, retire the old host so the domain has
one owner:

1. Delete `CNAME` from this repo (it is GitHub Pages-specific).
2. Disable Pages: **Settings → Pages → unset the custom domain**.

Doing this before the cutover would take down the working HTTP version for
nothing.

## Local preview

```bash
python3 -m http.server 4321
```
