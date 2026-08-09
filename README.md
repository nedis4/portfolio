Mariya Benovska — architecture portfolio.
Static site. Do not enable image optimization (it strips HDR gain maps).

## Custom domain

Live at **maria.archi**, set by the `CNAME` file in this directory. GitHub Pages
re-reads that file on every push, so editing it here *is* the domain change —
there is no separate step in the repo settings.

Changed from `mariya.my` on 2026-08-10.

**The domain is not pointed straight at Pages.** Both hostnames resolve to
Cloudflare (`104.21.x` / `172.67.x`, not the `185.199.x` Pages addresses), and
Cloudflare proxies through to GitHub — responses come back with
`server: cloudflare` *and* an `x-github-request-id`. Two things follow:

- Browser-facing TLS is Cloudflare's, so a domain change does not take HTTPS
  down. `https://maria.archi` already served a valid certificate while Pages
  was still 404-ing it. GitHub's own certificate only covers the Cloudflare →
  Pages leg; it re-provisions on its own and "Enforce HTTPS" may need
  re-ticking once it reports approved:

  ```
  gh api repos/nedis4/portfolio/pages --jq '{cname, cert: .https_certificate.state, enforced: .https_enforced}'
  ```

- Debug at the right layer. A 404 on the new domain while DNS resolves fine
  means Pages doesn't recognise the hostname (i.e. `CNAME` is stale or
  unpushed), *not* a DNS problem. Check `curl -sSI https://maria.archi/` for
  which server answered before touching DNS.

`mariya.my` is no longer served: Pages allows exactly one custom domain and
does not redirect the old one. Its DNS still resolves and still proxies to
Pages, so it now returns 404 rather than going dark. If it should keep
working, add a redirect rule in Cloudflare — this repo cannot do it.
`www.maria.archi` already resolves alongside the apex.

Nothing else in the site refers to the domain: `index.html` and
`index-v2.html` use relative links throughout, and there is no sitemap,
canonical tag, or `og:url` to update. If the domain ever changes again,
`CNAME` is the only file to touch.
