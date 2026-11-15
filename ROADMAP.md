# Roadmap: The Forever Compendiums library

Decided on 14 November 2026. Each phase runs in its own fresh session. Read this file first.

## How we work

- Make a plan and run the grilling rounds, then wait for "go".
- Test, review your own work, then run the `reviewer` subagent. Fix what it finds and test again.
- Commit only after approval. Work on a branch and merge into `main` only after approval.
- Each phase starts with its own grilling for the points marked "Open" below.

## Phase 1: Sort the content (done 15 November 2026)

Goal: each class page reads from general to specific.

Chapter order:

1. Opening
2. Class mechanics, including extra mechanics chapters (for example the Shaman chapter on totems)
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
- In-page links (`href="#..."` in the file, recounted on 15 November 2026): Druid 187, Hunter 357, Mage 102, Paladin 42, Priest 50, Rogue 125, Shaman 57, Warlock 37, Warrior 186. Scripts build a few more at runtime. The first count (Druid 67, Hunter 79, Paladin 5) counted lines with links, not links.

Result:

- All 9 pages are sorted on the branch `claude/sort-content-5ebb39`, one commit per page.
- Pages without an opening chapter (Paladin, Warlock, Shaman): the cover counts as chapter 00, and the chapters start at 01. Shaman now runs from CHAPTER 01 to 11.
- Six position fixes, each listed in the commit message of its page.
- On the Rogue page, the "weapons" and "stealth" chapters are the Combat and Subtlety specs, not extra mechanics chapters.

## Phase 1.5: Break up the lines (done 15 November 2026)

Decided on 15 November 2026.

Goal: one block element per line, so git can merge parallel work line by line. Only whitespace changes. The pages look and work exactly as before.

Why: most pages put whole chapters on one line, and Warrior and Priest put the whole body on one line (about 206,000 and 179,000 characters). A text edit and a phase 2 change on the same line would conflict.

Scope:

- The page body.
- These JSON data blocks in the scripts, and no others: Druid `DATA`, Hunter `B`, Mage `INDEX`, Paladin `SCENES` and `RECS`, Priest `DATA` and `PREFS`, Rogue `<script id="rogue-data">`, Shaman `BDATA` and `TIME`, Warlock `DATA`, Warrior `BUILDS` and `BEATS`. They are strict JSON. All other `const` data in the scripts is under 1 KB, except Shaman's `totemChoices`, which already spans several lines.
- Not CSS and not JS code. Phase 4 rewrites the CSS, and JS code needs a real parser to break safely.

Layout:

- One block element per line, indented 2 spaces per level. A paragraph stays on one line, however long. No wrapping.
- Decide by the computed display, not by the tag name. Many chips, pills and badges are `div` or `p` elements styled `inline-block`, and a line break between two of them adds a visible space. Add a break only where the browser ignores it: inside a flex, grid or table container, or between two block-level boxes. Take the display values from the original page at 375, 768 and 1280px. For an element hidden at load, use the display it gets when shown. Where it is not clear, do not break.
- Never add whitespace inside an element that holds only text and inline elements, and never inside `a`, `button`, `th` or `svg`. Scripts read text from some of them: the Warlock sort compares link text with the build names, and the Priest page copies table header text into buttons.
- JSON data: insert line breaks and indentation into the original text, outside strings only, one value per line, indented 2 spaces. Do not re-serialize it (for example with `json.dumps`), because that changes escapes and non-ASCII characters.
- Keep CRLF line endings and UTF-8 without BOM.

Tool: a small Python script that uses only the standard library. No downloads.

Checks for each page. They are the review, because the diff touches nearly every line. "Before" is the page as committed (`git show HEAD:<page>.html`).

1. Text and tags: outside the JSON data, the old and new files are identical with the whitespace between tags removed. Each JSON block is identical with the whitespace outside strings removed, and it still parses. This check proves text and tags, not looks.
2. Whitespace and layout:
   - Each new whitespace sits where the Layout rules allow it.
   - Before and after, every element has the same `getBoundingClientRect()`, and the page has the same `innerText`, at 375, 768 and 1280px.
3. Screenshots before and after are identical, pixel for pixel, at 375, 768 and 1280px.
4. Before and after, use each interactive part once (menus, filters, tabs, sorting, sliders, calculators) and follow every in-page link. The results are the same, and the console shows no errors. The phase 1 test scripts are not in the repo, so write this test again.

Order and stops:

- Warrior first, as a test. Test it, run the `reviewer` subagent, then stop for the user's review. Commit it after approval.
- Then the other 8 pages without the user's input. Run the `reviewer` once after all 8.
- Apply the rules above without asking. A page needs the user's input only when a check fails and no rule says what to do: for example, screenshots differ for an unclear reason, a check 4 test fails, or a reviewer finding needs a decision. Then leave the changed page in place, uncommitted, so the user can look at it. Write down the problem and what was tried, and go to the next page.
- Stop when every page is done or needs input. Do not commit the 8 pages during the run. After the user's review, make one commit per approved page.

Result:

- All 9 pages are reformatted on the branch `claude/break-lines-0a75b9`, one commit per page plus a Warrior follow-up, and merged into `main`.
- All 9 pages pass the four checks. Check 2 also ran at both sides of each media-query width, in print, and without JavaScript.
- Added rule: content that a script replaces at load shows only without JavaScript, so its breaks follow the display without JavaScript. This split the Mage atlas table and put the Warrior no-JavaScript note on its own line.
- Lines that stay long: `svg` elements, long paragraphs and CSS.
- "Before" comes from `git cat-file --filters HEAD:<page>.html`. Git stores LF, so `git show` does not give the CRLF file.
- The formatter and test scripts are not in the repo.

## Phase 2: Volumes (next)

- Runs in parallel with the user's text work (see below). Change tags (for example `<section>`) and add CSS and JS. Avoid changing text lines. Where phase 2 must change one, list it, because it can conflict with the user's text work.
- One page per class. Primer, Advanced and Complete are views of the same page. No copied text.
- Primer: chapters 1 to 4. Advanced: chapters 5 to 8. Complete: all chapters.
- "Chapters 1 to 4" here means steps 1 to 4 of the phase 1 chapter order, not the chapter numbers printed on the pages.
- Without JavaScript, the page shows the complete book.
- The Primer uses existing text only.
- Open: the volume names, where the sources appear, the volume links on the front page shelf, and the volume switch on the class page.

## Text work by the user (parallel to phase 2)

- After phase 1.5 is merged, the user edits text directly in the HTML: text refinement and new beginner text.
- The user works in a separate folder (a git worktree) on an own branch, made from `main` after phase 1.5.
- No commits until all changes and checks are done (the user's choice). Then the work goes to `main` as one commit. Until that commit, git has no copy of this work.
- The user checks the game facts. Claude can check language, HTML and the tests, but not game facts.
- In the script data, a text sits between quote marks. Inside it, write a quote mark of the same kind with a backslash: `\"` or `\'`.
- When the work comes back: merge it, resolve conflicts with phase 2, and run the tests.

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

- New beginner text: now part of the user's text work (see above). It needs sources, because Claude cannot check game facts.
- Print style for the front page: today it prints light text on white paper when background graphics are off.
- Title images cut off at the top (reported 15 November 2026). Find out where: on the front page only the Shaman cover crops its top; the class page heroes crop by screen width.
- Delete the merged branches `audit-fixes`, `claude/magical-swanson-f5774b` and `fantasy-landing` (needs approval).

## Testing notes

- Serve the project with `py -I -m http.server 8765 --bind 127.0.0.1`. The browser pane cannot load `file://` pages with images.
- Add `?v=N` to the URL after each edit, because the browser pane caches pages.
- The browser pane is narrow (about 393px), so wide screenshots come out blurry. For sharp desktop screenshots, use headless Edge:
  `"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --headless=new --disable-gpu --hide-scrollbars --user-data-dir=<temp folder> --virtual-time-budget=5000 --window-size=1280,<height> --screenshot=<file> <url>`
  The file can appear a moment after the command returns.
- Do not open PDFs in the browser pane. It offers them as a download.
