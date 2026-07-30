# lochin-wilde.github.io

*[Русская версия](README.ru.md)*

Personal site of Lochin Wilde — composer, sound designer and mixing engineer.
Live at **[lochin-wilde.github.io](https://lochin-wilde.github.io)**.

Three files, no build step, no dependencies:

| File | Role |
|---|---|
| `index.html` | The whole page. Also carries the Russian copy as static markup |
| `styles.css` | Styling, responsive down to 375px |
| `main.js` | RU/EN dictionaries, scroll reveal, the narrow-screen menu |

## Editing the text

Every translatable string lives **twice**: in the `translations` object in
`main.js`, and as fallback text inside the element carrying the matching
`data-i18n` attribute.

Both have to be changed together. The dictionary is what visitors read; the
markup is what search engines index and what shows before the script runs. They
drifted apart once — the page described a web developer's services while the
title described a composer — and nothing about that is visible in a browser,
because the script overwrites it a few milliseconds after load.

## Language

The page opens in Russian for a `ru-*` browser locale and in English otherwise,
and the RU/EN switch overrides that for the session. `index.html` ships with
`lang="ru"` and Russian fallback text; `applyLanguage()` updates both.

## Security

GitHub Pages serves static files and cannot set response headers, so the Content
Security Policy travels in a `<meta>` tag. It is `default-src 'none'` with
same-origin CSS and JS allowed and nothing else — there is no CDN, no analytics
and no embedded content to make room for.

Two protections cannot be delivered this way: `frame-ancestors` and
`X-Frame-Options` are ignored inside a meta tag, so framing is not blocked.
There is no form, no login and no stored state on this page, so there is nothing
to capture by framing it; closing that gap properly would mean moving to a host
that can set headers.

## Local preview

```bash
python3 -m http.server 8000
```

Then open `http://127.0.0.1:8000/`.

## Deployment

Pushing to `main` publishes. GitHub Pages builds from the branch root, so there
is nothing to configure and nothing to run.
