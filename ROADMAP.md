# Roadmap: The Forever Compendiums library

Decided on 14 November 2026. Each phase runs in its own fresh session. Read this file first.

## How we work

- Make a plan and run the grilling rounds, then wait for "go".
- Test, review your own work, then run the `reviewer` subagent. Fix what it finds and test again.
- Commit only after approval. Work on a branch and merge into `main` only after approval.
- Each phase starts with its own grilling for the points marked "Open" below.

## Phase 1: Sort the content (next)

Goal: each class page reads from general to specific.

Chapter order:

1. Opening
2. Class mechanics, including extra mechanics chapters (for example the Rogue chapters on the energy economy, weapons and stealth)
3. The three specs
4. Leveling, gear, endgame and PvP
5. Forever's rules (the "live changes" chapter, which is chapter 2 on most pages today)
6. The nine builds (dossiers)
7. Encounter lab ("one encounter, nine answers")
8. Comparison (atlas or dashboard)
9. Sources

Rules:

- Move whole chapters. Do not rewrite text.
- Exception: fix text that points to a chapter position ("see chapter 2", "the rules above", the "00 /" numbers on the Hunter page) with the smallest wording change. List each fix for review.
- Update tables of contents, in-page links and chapter numbers. Keep every id.
- Druid first. The other 8 pages only after the Druid page is reviewed.

Checks:

- The same chapters and the same text before and after. Only the order changes.
- Every in-page link still finds its target.
- The interactive parts still work, and the console shows no errors.

Facts from the inventory on 14 November 2026:

- All 9 pages share nearly the same chapter plan: opening, live rules, mechanics, three specs, nine dossiers, encounter lab, leveling and gear, comparison, sources.
- Paladin and Warlock start with the live rules and have no opening chapter heading.
- Shaman has two headings for each spec.
- Rogue chapter titles are sentences, so its chapter mapping needs a closer read.
- In-page links: Druid 67, Hunter 79, Paladin 5. The other pages were not counted.

## Phase 2: Volumes

- One page per class. Primer, Advanced and Complete are views of the same page. No copied text.
- Primer: chapters 1 to 4. Advanced: chapters 5 to 8. Complete: all chapters.
- Without JavaScript, the page shows the complete book.
- The Primer uses existing text only.
- Open: the volume names, where the sources appear, the volume links on the front page shelf, and the volume switch on the class page.

## Phase 3: Librarian quiz

- On the front page: a short conversation with the librarian, 4 to 6 questions.
- Result: a class, a spec and a volume, and the matching book on the shelf.
- The shelf stays usable without JavaScript. The quiz works with keyboard and screen readers.
- Base the scoring on what the class pages say, and show it for review.
- Open: the questions, the look of the librarian, and where the quiz sits on the page.

## Phase 4: Book redesign of the class pages

- A full book redesign of each of the 9 class pages. This replaces the original rule to keep their visual design.
- Keep all content and all interactive functions.
- One page first, then the rest.
- Open: one shared design or a different book style for each class, and how the dashboards and labs look as book pages.

## Later

- New beginner text: its own phase. It needs sources and review, because Claude cannot check game facts.
- Print style for the front page: today it prints light text on white paper when background graphics are off.
- Delete the merged branches `audit-fixes`, `claude/magical-swanson-f5774b` and `fantasy-landing` (needs approval).

## Testing notes

- Serve the project with `py -I -m http.server 8765 --bind 127.0.0.1`. The browser pane cannot load `file://` pages with images.
- Add `?v=N` to the URL after each edit, because the browser pane caches pages.
- The browser pane is narrow (about 393px), so wide screenshots come out blurry. For sharp desktop screenshots, use headless Edge:
  `"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --headless=new --disable-gpu --hide-scrollbars --user-data-dir=<temp folder> --virtual-time-budget=5000 --window-size=1280,<height> --screenshot=<file> <url>`
  The file can appear a moment after the command returns.
- Do not open PDFs in the browser pane. It offers them as a download.
