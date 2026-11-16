# Roadmap: The Forever Compendiums library

Decided on 14 November 2026. Each phase runs in its own fresh session. Read this file first.

## How we work

- Make a plan and run the grilling rounds, then wait for "go".
- Test, review your own work, then run the `reviewer` subagent. Fix what it finds and test again.
- Commit only after approval. Work on a branch from `dev`, and merge into `dev` only after approval.
- The repo is public at https://github.com/TheKillerTomatot/Forever-Compendiums. GitHub Pages serves `main`, so `main` is the live site: it gets `dev` only when the user says so. Push only after the user's approval.
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

## Phase 2: Volumes (done 15 November 2026)

- Runs before the user's text work (see below). Change tags (for example `<section>`) and add CSS and JS. Avoid changing text lines. Where phase 2 must change one, list it, because it can conflict with the user's text work.
- One page per class. Primer, Advanced and Complete are views of the same page. No copied text.
- Primer: chapters 1 to 4. Advanced: chapters 5 to 8. Complete: all chapters.
- "Chapters 1 to 4" here means steps 1 to 4 of the phase 1 chapter order, not the chapter numbers printed on the pages.
- Without JavaScript, the page shows the complete book.
- The Primer uses existing text only.

Decided on 15 November 2026:

- Names: Primer, Advanced and Complete. No group word shows on screen, because "Volume" already names the class books on the shelf and the covers. Screen readers hear the switch as "Reading level".
- The cover, the Sources chapter and the footer show in all three views.
- The URL picks the view: `?volume=primer` or `?volume=advanced`. Without it, the page shows Complete. The page does not remember the choice between visits.
- A link or URL hash into a hidden chapter shows the complete book and goes to the target.
- Class page: three buttons at the top of the chapter menu, built by JavaScript. The menu lists only the shown chapters. Chapter numbers do not change.
- Front page: three ribbon bookmarks under each book, green Primer, crimson Advanced and gold Complete. The book itself still opens the complete book.

Result:

- All 9 class pages and the front page are done on the branch `claude/volumes-e0cdd7`: the Warrior pilot, one commit per other page, a Warrior follow-up and one front page commit. Merged into `main`.
- Each class page has a new script right after `<body>` and new CSS lines at the end of its style block. The script is the same on all 9 pages. Only the chapter lists (`CHAPTERS`), the menu (`MENU`) and the switch position (`SWITCH_BEFORE`) differ.
- Changed lines on the class pages: one code line each on Druid, Rogue and Warrior. The menu highlight loop now skips hidden chapters. No text line changed.
- Front page: the 9 books are now one element per line (only whitespace changed), plus the ribbons and their CSS.
- Tests: the Complete view is identical to the page before phase 2 (element positions, text, print, no JavaScript, and a run over every control and link). The views, the switch, the links into hidden chapters and the menu highlight work at 375 and 1280px. The test scripts are not in the repo.
- Known limits: in the Complete view, the Shaman chapter bar overflows at 1440px and wider; the hidden links are still reachable by scrolling the bar (phase 4 redesigns it). On Mage phones, the switch sits in the chapter strip at the top of the page.

## Text work by the user (done 15 November 2026)

The user did the text work outside the project with an external editor and sent it back on 15 November 2026 as a zip: `%USERPROFILE%\Downloads\Forever_Class_Compendiums_COMPLETE_All_Editorial_Revisions.zip`. Keep the zip until this work is merged.

Review on 15 November 2026, against the phase 1.5 pages (`bcff272`):

- Intact: the scripts are identical, all ids are the same and in the same order, the images are identical, no in-page link is broken, no tag is badly nested, and the Warlock sort works.
- The editor rewrote every line (attribute order, `/>`, indentation, SVG end tags). The real content changes are about 600: new titles, subtitles and cover text; per page 5 field notes, 3 field tests, an orientation box, a lead-in to the dossiers and 3 hotfix notes; spec cards rewritten without build names; 36 "Open dossier" links removed; 3 Shaman tables moved to the dossier chapter; about 3 KB of CSS per page.
- Must fix:
  1. Phone covers: at 375px the Druid cover line sits under the menu bar and the Paladin cover line above the page top. At 320px also Hunter and Warlock, and Shaman scrolls sideways by 8px. The cover content is anchored to the bottom, so the extra lines push it up.
  2. Druid: a spec card presents Lifebloom as usable. The book's own "Lifebloom provenance warning" says no live way to get it is confirmed.
  3. Paladin: new notes make Judgement of Command a melee attack, but older lines still say "Judgement is ranged".
  4. Mage: a new note says Blizzard damage breaks Frost Nova, but the dossiers still teach "Frost Nova gives room for Blizzard".
- New game facts to check, each with a Blizzard link: the dungeon XP 12-level rule; Wild Growth (no cast while moving, 6.6% from 3.3%); Hunter Volley, 11% mana and pet health 115%; Paladin Judgement of Command; Priest The Quick and the Dead; Rogue and Shaman Windfury off-hand strikes; Warlock Siphon Life while silenced; Warrior Berserker Rage removes Sap; Mage Frost Nova and Blizzard. Also the Shaman "class deep dive" link: it is the link the Mage page cites, and the old Shaman page never cited it.
- Polish:
  - 21 em dashes; the pages had none.
  - Old names left: the Druid side menu ("The Living Archive · Vol. II"), the Rogue source footer ("Nine Paths of the Assassin") and the front page shelf subtitles.
  - Odd labels: "51 POINTS · hunter field notes", "51 POINTS × talent dossier research", Priest "One encounter, Different answers", Mage "Arcane Solutions", Shaman "external validation pending".
  - Unused CSS classes `compendium-cover-subtitle` and `reader-build-comparisons`, 9 `!important` per page, and the `compendium-revision` meta tag.
  - The dungeon XP note refers to a "November 13 restriction" that the books never describe.
  - The Hunter note dates the pet-health fix differently from the book's own list.
  - On Shaman, the moved tables come before the paragraph that introduces them.

Decisions on 15 November 2026:

- Start after phase 2 is merged, in a fresh session, on its own branch from `main`.
- Port, do not copy: a script applies the zip's content changes to the current pages and skips the formatting noise. Proof: a formatting-blind diff between each ported page and its zip page is empty.
- Two commits: first the edits as delivered, then the fixes.
- Leave the 36 removed "Open dossier" links out.
- Phone covers: fix the layout so the cover shows all its text. Keep the text.
- Content fixes (Lifebloom, Judgement, Frost Nova): propose the smallest wording change for each, and the user approves it. Judgement and Frost Nova follow the fact check: if the new rule is correct, update the older lines; if it is wrong, remove the new note.
- Fact check: give the user one checklist (claim, sentence, link). The user answers each item with "correct", "wrong" or "changed to ...". Claude cannot check game facts.
- Polish: fix every polish item with the smallest change and list each one for review. An em dash becomes a colon, a comma or a full stop.
- Front page: update the shelf titles and subtitles to match the new books.
- Tests: the cover text is visible at 320, 375, 768 and 1280px; no sideways scroll; in-page links; the console; the interactive parts; the phase 2 views. Then the `reviewer`, then a merge after approval.
- Review tools from the planning session (scratchpad, may be cleared): `semdiff.py` (formatting-blind HTML diff) and `zipreview/REVIEW.md` in `%USERPROFILE%\AppData\Local\Temp\claude\c7df57030140bfb9\1f4627ae-15fa-496f-aa00-361b9f27051d\scratchpad\`. If they are gone, rebuild the diff from the zip and `bcff272`.

Result:

- Two commits on the branch `claude/editorial-port-97c643`, fast-forwarded onto `main`: dd055c4 (the edits as delivered, ported) and 4fe34a7 (the fixes). Not pushed.
- Proof for the port: a formatting-blind diff of each ported page against its zip page shows only phase 2's additions. In headless Edge, each ported page matches its zip page: element boxes and text at every media-query width, print, no JavaScript, and every control and in-page link.
- Fact check: the user confirmed all 12 items. On the user's answers, the wording changed for Druid Wrath, Paladin Judgement of Command (the older lines too), Mage Frost Nova and Blizzard (the dossier lines too) and Shaman Windfury. The Lifebloom card no longer presents Lifebloom as usable. The class deep dives are paired articles (Hunter and Druid, Mage and Shaman, Priest and Warrior, Rogue and Warlock), so all nine links are right.
- Covers: the Druid, Hunter, Paladin, Warlock and Rogue covers grow when their text needs room (`min-height` with the old heights). Paladin and Warlock phone padding clears the menu button. The Rogue badge sits below its menu button (this overlap was older than the text work). Shaman: below 760px the top-bar title gets its own row, so the chapter bar and the volume switch keep their room.
- Polish as listed above, plus the old book names on the Hunter, Mage, Paladin and Priest pages and the Priest chapter 08 label (found in review). The Hunter pet-health date needed no change: the book's table gives the fix date, the source list the posting date. Druid's old `.hero-kicker` rule lost its `!important` too, so the unflagged cover CSS still wins.
- Front page: each book shows the short name and the second phrase of its new cover line.
- Tests: static checks; cover text visible from 320 to 1920px on all 9 pages with no sideways scroll; the fixes change the layout only on the faulty covers and the Shaman top bar; the phase 2 view tests and the interaction run pass on all 9 pages; front page ribbons, focus and console. The test scripts are not in the repo.
- Left as it was: the volume numbers on the pages still differ from the shelf (open since phase 2). The front page still says "nine 51-point builds" and "Research snapshot: 13 November 2026".

## Phase 3: Librarian quiz (done 16 November 2026)

- On the front page: a short conversation with the librarian, 4 to 6 questions.
- Phase 2 gives each book three ribbon links: `<class>.html?volume=primer`, `?volume=advanced` and the plain page. A spec can be added as a hash, for example `druid.html?volume=primer#balance`.
- Result: a class, a spec and a volume, and the matching book on the shelf.
- The shelf stays usable without JavaScript. The quiz works with keyboard and screen readers.
- Base the scoring on what the class pages say, and show it for review.

Decided on 15 November 2026:

- Four questions: role, then class (only the classes that have that role), then style (picks the spec), then experience (picks the volume). In 13 of the 19 role and class paths only one spec is left, so there the style answer does not change the result.
- Each answer shows a phrase and the name in brackets, for example "Nature, in many forms (Druid)".
- Experience picks the volume: new to the game, Primer; knows the basics, Advanced; wants everything, Complete.
- Links: Primer `<class>.html?volume=primer#<spec>`, Advanced `<class>.html?volume=advanced#<first dossier of the spec>`, Complete `<class>.html#<spec>`. Added on 16 November 2026: for Feral tanks the Advanced link goes to the Bear dossier (`#dossier-bear`), because the first Feral dossier is the Cat.
- Scoring: each spec has style tags from its own chapter text (for Rogue, also the page's own chooser). When no tag matches, the spec whose three builds have the lowest average Complexity or Difficulty in its page's atlas wins; on a tie, the first in the book. The atlases rate builds only within one page, so no rating compares classes.
- Place and look: a leather panel with a gilt frame between the header and the shelf, closed at the start, with a portrait of the librarian that the user supplied. Earlier questions and answers stay as a transcript. "Start again", no "Back". The result quotes one sentence from the spec's chapter.
- Shelf mark: the gold ring, a longer ribbon for the chosen volume, and a slip "The librarian's choice" that screen readers read as part of the book link. The panel and the slip do not print.

Result:

- Commits on the branch `claude/librarian-quiz-7fb4a7`: d23dd6d (`index.html` and `images/librarian.webp`), 134aba7 (Feral tanks link to the Bear dossier) and 4be9691 (a rule in the project `CLAUDE.md`: no `sed -i` on CRLF files in Git Bash, because it writes LF). Fast-forwarded onto `main`.
- The panel has the `hidden` attribute and its script shows it. Changed lines: the hover and ribbon selectors also cover the chosen book, each book `<li>` has an id (`book-<class>`), and the colophon says "Art" instead of "Cover art", because the portrait is not a cover.
- Tests: all 285 answer paths with real mouse clicks, checked against a separate model; all 81 result links open the right view at their target; keyboard at 375 and 1280px; the screen reader tree; without JavaScript the page is pixel-identical to the page before phase 3 at 320, 375, 768 and 1280px (apart from the colophon word); no sideways scroll; print; the console. The test scripts are not in the repo.
- Test pitfalls: the class pages scroll smoothly to a hash (about 1.5 s), and two links into the same page that differ only in the hash fire no load event.

## Phase 4: Book redesign of the class pages

- A full book redesign of each of the 9 class pages. This replaces the original rule to keep their visual design.
- Keep all content and all interactive functions.
- One page first, then the rest.

Decided on 16 November 2026:

- Look: an open book on the library desk. Each chapter is a sheet of cream paper with dark ink on the front page's dark wood. Gilt rules, IM Fell English SC headings and Georgia body text. No other fonts. No dark mode.
- One shared design in `book.css`, linked from all 9 pages. Each page keeps a small block for its class color, its ornament and its own special parts. The old page CSS goes.
- The class color marks the kicker, the chapter numbers, the drop caps, the note rules, the plate headers and the rating dots. Gilt and ink do the rest.
- Cover: a title page with the full picture in a gilt frame, not cropped.
- Interactive parts (dashboards, labs, calculators, filters) sit on framed plates, like tables glued into a book: a dark plate header in the class color, dropdowns on ledger lines and brass buttons. On phones, wide tables scroll sideways inside their plate.
- Navigation, the same on all 9 pages: a leather contents panel beside the page on wide screens, and a top bar with a "Contents" button on phones. The Primer, Advanced and Complete switch becomes the three ribbons from the front page. This also ends the Shaman chapter bar overflow.
- One long scrolling page. All links, the phase 2 views and the quiz links keep working.
- Print: only the paper, with every plate in full. No wood, panel or ribbons.
- Inline `style` attributes move into classes where they block the shared design (Warrior has 232, Warlock 46, Priest 41).
- Meta text goes: every line about how the file is built (offline, embedded, standalone HTML, file formats, the images folder). Art credits shrink to one line per page in the sources chapter. Research dates, no-JavaScript notes, the Blizzard notice and method notes stay. Every removed or shortened line is listed for review. No other text changes.
- Pilot: Mage, because it has the most kinds of parts. Test it, run the `reviewer`, then the user reviews it before the commit. Then the other 8 pages in one run without the user's input. A page with a problem waits, uncommitted, with a note. One commit per page after the user's review.
- Proof for each page: the same text (apart from the listed meta lines) and the same ids; every control, link and calculator gives the same results; the phase 2 views and the quiz links land on their targets; no sideways scroll from 320 to 1920px; print and no JavaScript work; text contrast meets WCAG AA; no console errors.
- Branch: `phase4-book` from `dev`.
- The approved mockup (Mage cover, chapter 00, the Atlas plate and two dossier cards) is not in the repo.

## Later

- New beginner text: now part of the user's text work (see above). It needs sources, because Claude cannot check game facts.
- Print style for the front page: today it prints light text on white paper when background graphics are off.
- Title images cut off at the top (reported 15 November 2026): on the front page, the Shaman cover crops its top. The class page covers are part of phase 4.
- The front page still says "nine 51-point builds" and "Research snapshot: 13 November 2026". Decide whether they need new words; the phase 3 check found nothing wrong with them.

## Testing notes

- Serve the project with `py -I -m http.server 8765 --bind 127.0.0.1`. The browser pane cannot load `file://` pages with images.
- Add `?v=N` to the URL after each edit, because the browser pane caches pages.
- The browser pane is narrow (about 393px), so wide screenshots come out blurry. For sharp desktop screenshots, use headless Edge:
  `"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --headless=new --disable-gpu --hide-scrollbars --user-data-dir=<temp folder> --virtual-time-budget=5000 --window-size=1280,<height> --screenshot=<file> <url>`
  The file can appear a moment after the command returns.
- Do not open PDFs in the browser pane. It offers them as a download.
