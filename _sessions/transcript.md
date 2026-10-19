# Session: moving Notion Nanny to docs/ - transcript

**Date:** 19 October 2026
**Participants:** the user, Claude (Claude Code)

The summary is in [summary.md](summary.md). Tool output is condensed. Names of members of the public who commented on the blog have been left out.

---

## 1. The request

**User:** I have a project at repos/notionnanny which is a scrape from a site from some time ago, which includes images from flickr and a blog from blogger.com. Ideally this would be re-configured to work from docs like the other e-2-org repositories, and tidied up so all the content is in logical directories.

**Claude** looked at the repo:
- Only `README.md` was committed. The scrape was untracked: `notionnanny.net/`, `farm1/farm2.static.flickr.com/` (147 thumbnails), `www.blogger.com/` (Blogger stylesheets and icons), and a root `index.html` that redirected into it.
- The repo was private, with no Pages site.
- The home page was one long page with sections (news, timeline, about, venue, support, press). News and press were Blogger blogs shown in 714px iframes.
- There were 162 post pages, 8 month archives, an index with every post, and RSS/Atom feeds. 5 posts had comments.
- The Flickr pages the thumbnails link to are still live.

Claude compared the other e-2-org repos (Jekyll for 7actsoflove, contact and c-ship; static for adelaide and others). It chose Jekyll, since a blog fits `_posts/` naturally.

Claude created the `jekyll-port` branch and committed the scrape untouched as the first commit. It wrote an extraction script that turned:
- each post page into `docs/_posts/YYYY-MM-DD-slug.html`, with title, date, Blogger ID and comments in the front matter and the body kept verbatim;
- each press item into `docs/_press/NN-slug.html`;
- the thumbnails it referenced into `docs/images/flickr/`.

It then copied the site graphics and stylesheets, and wrote `_config.yml`, the layouts (`blog`, `posts`, `post`), the includes, `news/index.html`, the month pages, and `press/index.html`.

## 2. Checking the scrape before committing

**User:** I decided not to commit this repo until checks have been made for any credentials exposed in the scrape after the last project — but I will want to commit all the scraped files and docs eventually. Can you check the scrape files for anything incriminating?

**Claude** said it had already made one local commit. It hadn't been pushed, and Claude undid it, keeping the files. Claude then checked:
- **Keywords** (passwords, keys, tokens, sessions, logins, FTP/SSH): only blog text matched, such as "love tokens".
- **Blogger "authorization" stylesheets:** empty, 1 byte each.
- **Email addresses:** only `noreply@blogger.com` and `tech@e-2.org`.
- **Query strings:** Blogger admin links (edit post, delete comment, email post), which only work when signed in as the owner.
- **Image metadata (exiftool):** only colour profiles and "AppleMark"-style comments.
- **Server paths, IPs, hidden files or logs:** none.

It reported nothing incriminating. It flagged the public Blogger IDs, the 5 reader comments (one signed with a name, one from a named Blogger user, one spam), and asked whether to drop the spam comment from `docs/`.

## 3. Anonymising

**User:** If there are contributions from members of the public exposed in the scrape, I'd like them to be anonymised. Yes, drop the spam comment and remove the blogger admin links.

**Claude** found that the named Blogger user was the spam poster, so removing the spam removed that name too. It wrote a script for the scrape that removed:
- the admin link spans;
- the `EditURI`/`service.post` header links and the feed `rel='edit'` links;
- the spam comment, with its count set to 0 on the post, index and archive pages;
- a signature and a "-=d=-" sign-off from two comments.

Python's text mode converted the files' CRLF line endings on the first run. Claude restored from a backup and reran the script, reading and writing raw bytes. It also fixed two missed patterns (self-closing feed links, and a check that failed on the second run). After this, a search found none of the admin links, names or spam left in the scrape. Claude re-ran the extraction so `docs/` matched.

Claude then:
- ported the home page to `docs/index.html`: UTF-8, `relative_url` paths, in-page anchors, iframes pointing at `news/` and `press/`, and two HTML fixes;
- built the site with Jekyll and checked it with a script:
  - all internal links and images resolved;
  - day numbers needed zero-padding to match Blogger ("October 04");
  - two posts at the same minute were tie-broken with seconds;
  - the index's post order, dates and titles then matched the original;
  - 12 post pages' "Previous Posts" still differed, because Blogger froze them at publish time;
- escaped titles and added "Notion Nanny: " to post page titles;
- served `_site` at `/notionnanny/` and screenshotted the home, post and press pages in headless Chrome.

## 4. The scrape date

**User:** Can you find the latest date mentioned in the scrape? I can't remember when I scraped this but would like to add a date (year) to the README.

**Claude:** All 361 HTTrack stamps say Wed, 17 Mar 2010, and the latest post is 13 June 2009. The file dates (2020) are from a later copy. Claude added the date and a "Layout" section to the README.

## 5. Wrapping up

**User:** Yes, please prune the commit — everything can be merged to main. Write up the session and commit.

**Claude:**
- expired the reflog and ran `git gc --prune=now`, so the undone commit no longer exists locally;
- wrote this summary and transcript;
- committed the scrape, `docs/`, the README and `_sessions/` on `jekyll-port`, merged it into `main`, and deleted the branch. Nothing was pushed.
