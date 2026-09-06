# Web Scraping Notebooks: README

This repository contains five Jupyter notebooks. Each one scrapes a Malaysian news outlet's archive for the years 2015 to 2025. The outlets covered are Malay Mail, Malaysiakini, New Straits Times (NST), and Bernama.

## Files in this collection

| File | Site | Method used | Static or dynamic |
|---|---|---|---|
| `malay_part1.ipynb` | Malay Mail (Parts 1 to 3) | requests, cloudscraper, BeautifulSoup | Static |
| `malay_part2.ipynb` | Malay Mail (Parts 4 to 5) | requests, cloudscraper, BeautifulSoup | Static |
| `malaysiakini.ipynb` | Malaysiakini | Selenium, BeautifulSoup | Dynamic (JS rendered) |
| `NST.ipynb` | New Straits Times | requests, BeautifulSoup, sitemap XML | Static |
| `bernama.ipynb` | Bernama | requests, BeautifulSoup | Static |

The Malay Mail notebook was split into two files (`malay_part1.ipynb` and `malay_part2.ipynb`) purely to keep file size manageable for version control. Together they form one continuous scraper covering Parts 1 through 5.

## Common pattern across all notebooks

Every notebook follows the same two stage pattern, even though the exact tools differ:

1. **Indexing stage.** Collect the list of article URLs first, without scraping full article content. This is usually the cheaper, faster step, since it only touches listing pages, archive pages, or sitemap files, not every individual article.
2. **Scraping stage.** Read the URL list produced by the indexing stage and visit each article page one at a time to extract the actual content (title, date, author, body text, images, and so on).

This separation matters because indexing and scraping fail differently and at different rates. Keeping them apart makes it much easier to resume a broken run, retry only the failed URLs, or rebuild the index without redoing the expensive part.

## File by file structure

**Malay Mail**: collects article links first, then opens each link and saves the title, date, text, and image. Runs again later to pick up where it stopped, and to fix any links that failed the first time.

**Malaysiakini**: uses a real browser to open pages, since the content only shows up after the page loads. First collects links, then opens each one to get the full article. Failed links are tried again separately.

**NST**: downloads a list of links from the site's own sitemap files, then someone manually picked the useful links out of that list. Only those picked links are opened and saved.

**Bernama**: simplest one. It already has a list of links to start with, so it just opens each one and saves the article.

## What to keep when building a new scraper

If you are building a new scraper based on this code, these are the parts worth keeping:

- **Split link collection from content collection.** First just gather the links. Then, in a separate step, open each link and get the actual content. Do not mix these two jobs together.
- **Save your links to a file before opening them.** This way, if something goes wrong later, you do not lose the list of links and have to find them again.
- **Save your results often, not just at the end.** Write results to a file every so often (for example every 50 or 100 pages), so a crash does not lose everything.
- **Keep track of what worked and what failed.** For every page, save whether it succeeded or failed. This makes it easy to find and retry only the failed ones later, instead of redoing everything.
- **Skip pages you already did.** Before opening a page, check if you already saved it. This lets you stop and restart the scraper without repeating work.
- **Wait a little between requests.** Add a short random pause before each request so you do not overload the website or get blocked.
- **Try again if a request fails.** If a page does not load, try it two or three more times before giving up on it.
- **Check if you really need a browser.** Most sites show their content in plain HTML, so a simple request is enough and much faster. Only use a real browser (like Selenium) when the content does not show up without one.
