# lochin-wilde.github.io

*[Русская версия](README.ru.md)*

Personal site of Lochin Wilde — composer, sound designer and mixing engineer.
Live at **[lochin-wilde.github.io](https://lochin-wilde.github.io)**.

Three files, no build step, no dependencies:

| File | Role |
|---|---|
| `index.html` | The Russian page, and the source the English one is generated from |
| `en/index.html` | **Generated.** Do not edit — run `tools/build-en.mjs` |
| `styles.css` | Styling, responsive down to 375px |
| `main.js` | RU/EN dictionaries, scroll reveal, narrow-screen menu, the player |
| `sitemap.xml`, `robots.txt` | How the English page gets discovered |

## Editing the text

Change `index.html` and the `translations` object in `main.js`, then run:

```bash
node tools/build-en.mjs
```

**Never edit `en/index.html`** — it is generated, and the first line of the file
says so. The strings live in one place, the dictionary; the Russian markup is
written by hand and the English markup is derived from both.

Text exists twice by necessity: the dictionary is what the script applies, the
markup is what search engines index and what shows before the script runs. They
drifted apart once — the page described a web developer's services while the
title described a composer — and nothing about that is visible in a browser,
because the script overwrites it milliseconds after load. The generator exists
so that the second language cannot drift the same way.

## Language

**One language per address.** `/` is Russian, `/en/` is English, each declaring
the other through `hreflang`, and the RU/EN control is a pair of links.

It used to be one address with a script that rewrote the page from the browser's
locale. That cost the English text its place in search results entirely — a
crawler indexes URLs and does not click — and served Russian on an English URL
to anyone whose browser asked for it. `applyLanguage()` still runs, for the two
things markup cannot carry: the current year in the footer, and aria-labels for
controls with no text of their own.

## Security

GitHub Pages serves static files and cannot set response headers, so the Content
Security Policy travels in a `<meta>` tag: `default-src 'none'` with same-origin
CSS and JS, and one exception, `frame-src https://w.soundcloud.com`.

**That exception costs nothing until it is used.** The SoundCloud embed is not
in the page; a button is, and the iframe replaces it on click. So a visitor who
never presses play is never handed SoundCloud's scripts or cookies, and the page
stays free of third-party code by default rather than by promise. The iframe is
sandboxed to scripts, its own origin and pop-ups when it does arrive.

The button says only "Listen on SoundCloud". It carried a line explaining that
nothing loads until you press it, which is a fact about the implementation and
not about the music — the kind of sentence that reads as written by a machine
rather than by the person whose site this is.

Two protections cannot be delivered this way: `frame-ancestors` and
`X-Frame-Options` are ignored inside a meta tag, so framing is not blocked.
There is no form, no login and no stored state on this page, so there is nothing
to capture by framing it; closing that gap properly would mean moving to a host
that can set headers.

## Hosting

GitHub Pages serves this site, and nothing else does. Asked for its headers it
answers `server: GitHub.com`; the app linked from the Programs section, at
`sortirovator.pages.dev`, answers `server: cloudflare`. Two projects, two hosts,
and only one of them sits behind Cloudflare.

The split follows from what each one needs. The app runs code on every request —
an invite gate, a rate limiter, writes to a database — which is what Cloudflare
Pages Functions are for. This site is files, and files need no request handler.

Cloudflare could not front this site in any case. It works by taking over a
domain's DNS records, and `lochin-wilde.github.io` is a name GitHub owns, not
one that can be pointed elsewhere. That same missing domain is why the app's
rate limiting had to be written into its code rather than configured as a
firewall rule — those rules attach to a domain, and there is none.

What a move would buy is real response headers, closing the framing gap
described above. What it costs is a domain.

## Local preview

```bash
python3 -m http.server 8000
```

Then open `http://127.0.0.1:8000/`.

## Deployment

Pushing to `main` publishes. GitHub Pages builds from the branch root, so there
is nothing to configure and nothing to run.
