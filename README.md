# astraverse.dev

The Astraverse company site. One static page, no build step, no dependencies —
edit `index.html` and push.

Served by GitHub Pages from the `main` branch root. `CNAME` binds it to
`astraverse.dev`; deleting that file unbinds the domain, so leave it alone.

## DNS

The apex points at GitHub Pages, and mail stays on Namecheap Private Email.
The two are independent — **changing the A records must not touch the MX
records.**

| Type | Host | Value |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `astraverse-io.github.io.` |

## `.dev` is HSTS-preloaded

Browsers refuse plain HTTP on `.dev` — there is no "ship it now, add TLS
later". The site is unreachable, not merely insecure, until GitHub finishes
issuing the certificate. After the DNS change, wait for **Settings → Pages**
to report the certificate as issued, then turn on *Enforce HTTPS*.

## Local preview

```bash
python3 -m http.server 4321
```
