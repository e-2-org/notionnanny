# Session: moving Notion Nanny to docs/ - summary

**Date:** 19 October 2026
**Branch:** `jekyll-port` (from `main`), merged into `main`

The transcript is in [transcript.md](transcript.md).

The repo held an uncommitted HTTrack scrape of notionnanny.net (Allison Smith, 2005–2007), produced by e-2. The site was one page whose news and press sections were Blogger blogs shown in iframes, with photos hosted on Flickr. This session checked the scrape for anything sensitive, anonymised it, and rebuilt the site as a Jekyll site in `docs/` like the other e-2-org repos.

## Checking the scrape

Before anything was committed, the scrape was checked for credentials and personal details:

- **No credentials.** Nothing matched passwords, keys, tokens, sessions or logins. The three `www.blogger.com/dyn-css/authorization*.css` files are empty (1 byte each), and the `zx=` value in their URLs is a cache-buster, not a session token.
- **No image metadata** beyond colour profiles and comments like "AppleMark". No GPS, camera or owner data.
- **No server paths, IP addresses, hidden files or HTTrack logs.** The only email addresses are `noreply@blogger.com` and `tech@e-2.org`.
- **Public Blogger IDs** (blog, profile, post IDs) remain. They were public on the live blog.

## Changes to the scrape

The scrape at the repo root is otherwise byte-for-byte as archived. These changes were made at the user's request, because the repo will eventually be public:

- **Blogger admin links removed:** the edit, delete and email links on every post and comment, the `EditURI` and `service.post` links in page headers, and `rel='edit'` links in the Atom feeds.
- **Spam comment removed:** on `news/2005/09/rps-tois.html`. Its count is now 0 on that page and on the index and archive pages. This also removed the only commenter with a Blogger profile.
- **Commenters anonymised:** a signature and a sign-off were taken out of two comments. All 4 remaining comments are "Anonymous".

The Windows (CRLF) line endings and the Latin-1 encoding of `notionnanny.net/index.html` were kept.

## What the site does now

- **`index.html`** is the original home page, converted to UTF-8, with the news and press iframes and in-page navigation. A couple of HTML errors were fixed (`arget=`, a missing `<p>`). On phones it zooms out to its 800px width.
- **`news/`** is the blog, generated from `_posts/`:
  - The index lists every post, newest first, with an "All Posts" sidebar and month archives.
  - Each post keeps its original address, e.g. `news/2005/08/spinning.html`, with its comments and "Previous Posts" sidebar.
  - The month archives are at `news/YYYY/MM/`.
- **`press/`** lists the 7 press items from `_press/`. The original "Read more..." pages were never archived, so "Read more..." now shows the rest of the article in place.
- **Links to other sites** in the iframe pages open in a new tab, because Flickr refuses to be framed.
- **Flickr thumbnails** are in `images/flickr/` and still link to their pages on flickr.com/photos/notionnanny, which is still live.

## Main files

| File | Role |
|---|---|
| `docs/_config.yml` | Jekyll settings and an overview of where things live |
| `docs/_layouts/blog.html` | Wrapper for the pages shown in iframes |
| `docs/_layouts/posts.html` | A list of posts (the index, or one month if `month` is set) |
| `docs/_layouts/post.html` | A post's own page, with comments and previous posts |
| `docs/_includes/post.html`, `docs/_includes/archives.html` | One post; the month list |
| `docs/_posts/` | 162 posts, original HTML; comments in front matter |
| `docs/_press/` | 7 press items, sorted by `order` |
| `docs/css/notionnanny.css`, `docs/css/navbar.css` | The original stylesheets, plus rules for "Read more" |

## Checks

- Every internal link and image resolves when the site is served at `/notionnanny/`.
- The index's post order, date headers and titles match the original. Two posts shared the same minute, so "I should do a Shire book" was given 30 seconds to keep Blogger's order.
- Home, post and press pages were screenshotted in headless Chrome and match the original.

## Known gaps

- **"Previous Posts"** differs on 12 post pages. Blogger fixed each list when the page was published; the new site lists the 10 posts before each one by date.
- **Month archives** list every post in the month. The archived originals were incomplete (August 2005 showed only 2 posts).
- **Not in `docs/`:** the RSS/Atom feeds, Blogger's admin icons and stylesheets, and the unused "About the makers" button and title graphics. All are still in the scrape.
- **Not tested:** clicking "Read more..." on the press page, phones, Safari and Firefox.
- **Publishing:** the repo is private and Pages isn't switched on. `_config.yml` assumes `e-2-org.github.io/notionnanny`.

## Icons (added later in the session)

The user added `docs/icons/notionnanny.png`, a 44px crop of the face from `images/header.gif`. Enlarging it to 512px looked blurry, so every icon is now made from a 134×134 crop of the header's picture panel (`docs/icons/notionnanny-large.png`). The crop shows the head, cloak and basket, and stops just inside the red frame. The files match the other e-2-org sites: `favicon.ico` (48/32/16), 96px, apple-touch (180px), 192 and 512px manifest icons, an SVG holding the PNG, and `site.webmanifest`. The tags are in `docs/_includes/icons.html`, used by `index.html` and `_layouts/blog.html`. `docs/favicon.ico` is a copy of the `.ico`.

## Data sources

- **Scrape date:** HTTrack's "Mirrored from" comments, all dated Wed, 17 Mar 2010. The newest post is 13 June 2009.
- **Posts:** extracted from the 162 individual post pages, which hold the full post and its comments. The date comes from the date header and the time from the permalink. The body was kept verbatim, except that image paths now point to `images/flickr/` and Flickr links use https.
- **Press items:** extracted from `press/index.html`.
