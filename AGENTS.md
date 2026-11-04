# Project context for AI assistants

This file gives Cursor, or any AI coding assistant, the full background on this project so you can pick up where the last session left off. Read it before making changes. The README covers how to use the tool. This file covers why things are the way they are.

This repo is public. Don't add secrets, tokens, internal-only URLs, or private StrongMind details to it.

## Who you're working with

- Court is a curriculum developer at StrongMind, not an engineer. She builds and ships interactive HTML learning content for K–8 and links it into Course Builder, and eventually Course Pro, with an iframe.
- She is comfortable with git basics: branch, commit, push, PR, merge, then Dev Deploy and Prod Deploy through GitHub Actions.
- Explain technical terms in plain English.
- When a decision is hers to make, offer a few clear options instead of an open-ended question.
- Make targeted edits to existing files rather than rewriting them.
- Text written in her voice, like emails or form answers she will send, should be plain sentences. Avoid arrows, slashes, parentheses, and inline numbered lists in those drafts.
- She reviews closely for visual polish. Check layout in a real browser at desktop, tablet, and phone widths before saying something is done.

## The goal

Court wants a set of authoring studios so anyone on the curriculum team can build interactives without code. Each interactive type gets its own studio and its own student player:

1. Sort and match. Built, in this repo.
2. Venn diagram. Not started.
3. Branching scenario. Not started.

Authors type text, add optional media, and set how many items or scenarios there are. The long-term goal is a Publish button that runs the activity through the pipeline and hands the author a link to paste into Course Builder, and eventually Course Pro.

## Where things stand

- The sort player and the Sort Studio are done and tested.
- The demo is live on Court's personal GitHub, username CourtnieStrongmind, with GitHub Pages. GitHub Pages addresses include the username, so if it changes again the links change too. The sample uses three behaviors from a Domains of Development lesson.
  - Landing page: https://courtniestrongmind.github.io/Sort_Interactive_Studio/
  - Studio: https://courtniestrongmind.github.io/Sort_Interactive_Studio/studio/sort/index.html
  - Sample: https://courtniestrongmind.github.io/Sort_Interactive_Studio/sort/index.html?set=sample-developmental-domains
- Court can't create repos in the StrongMind GitHub org yet. She is asking her decision maker for permission. The plan is to move this into a StrongMind repo named `sort-interactive` and deploy it to interactives.strongmind.com like her other interactives.
- Publishing is not turned on. Authors use Export for now.

## How it works

### The player: `sort/index.html`

- One self-contained HTML file with inline CSS and JS. The only outside resources are Google Fonts and iframe-resizer 4.2.11 from cdnjs, which every StrongMind interactive uses for embedding.
- It loads an activity from `sort/sets/<slug>/content.json` using the URL parameter `?set=<slug>`. Media paths in the JSON are relative to that set's folder.
- With `?preview=1`, or when loaded through `srcdoc`, it skips the fetch and waits for the studio to send content with `postMessage`. Message types are `sort-preview`, `sort-ping`, and `sort-player-ready`.
- Screens are Start, Activity, and End. Start and End are frosted cards centered vertically in the frame. The Activity screen has a frosted, centered title card at the top, then the groups, then a "Cards to sort" area, then a status line and a Check button.
- Students can drag cards with a mouse or touch, tap a card then tap a group, or use the keyboard by pressing Enter on a card and then Enter on a group. Screen reader announcements go through an `aria-live` region.
- Check is only enabled once every card is placed. Correct cards lock with a green check. Wrong cards shake, then move back to "Cards to sort." When everything is right, the End screen shows how many tries it took.
- Cards are shuffled each time.

### The studio: `studio/sort/index.html`

- One self-contained HTML file. It loads JSZip 3.10.1 from cdnjs for export and open.
- Sections:
  1. Basics: title, activity name used in the link, directions, and optional directions audio.
  2. Groups: 2 to 4.
  3. Cards: 2 to 24.
  4. Feedback messages.
  5. A "Ready to publish?" checklist.
- The live preview on the right is the real player in an iframe. If the player file can't be found, for example when the studio is opened straight from inside a zip, it falls back to a built-in copy of the player stored in the `<script type="text/plain" id="playerSource">` block.
- After any change to `sort/index.html`, run `python tools/build_studio.py` to refresh that built-in copy. Don't hand-edit the block.
- Directions have six starter options plus "Write my own." Two starters fill in the group names automatically. Editing the text switches the dropdown to "Write my own."
- Media is stored in the browser as data URLs, keyed by `media/<filename>`. Big PNG, JPEG, and WebP files are scaled down to 800 pixels on the longest side when added. SVG and GIF files are kept as they are. Images must be 2 MB or less, and audio 5 MB or less.
- Every picture needs alt text, and validation blocks export without it.
- The draft autosaves to `localStorage` under the key `sort-studio-draft-v1`. It lives only in that person's browser. Clear all erases it, with a two-click confirm.
- Export downloads `<slug>.zip` containing `<slug>/content.json` and `<slug>/media/...`. "Open saved" loads that zip back in for editing.
- Publish is wired but off. When `CONFIG.publishUrl` is set, the studio POSTs form data with `slug` and `bundle`, which is the export zip. It expects `{ "url": "..." }` back and then shows the link with a Copy button. `CONFIG.liveBase` is `https://interactives.strongmind.com/sort-interactive/sort/index.html`.
- The student link is only shown after publishing. An always-visible link preview was removed because it made the form feel busy.

### Content format: `content.json`

```json
{
  "version": 1,
  "type": "sort",
  "slug": "sample-developmental-domains",
  "title": "Try It Yourself: Classify by Domain",
  "stem": "custom",
  "directions": "Drag each card to the group where it belongs.",
  "directionsAudio": "",
  "categories": [
    { "id": "c1", "label": "Living", "image": "media/tree.svg", "imageAlt": "A green tree", "audio": "" }
  ],
  "items": [
    { "id": "i1", "text": "dog", "image": "", "imageAlt": "", "audio": "", "category": "c1" }
  ],
  "feedback": { "correct": "...", "tryAgain": "..." },
  "endMessage": "..."
}
```

`stem` is the directions starter the author picked, or `custom`. The player ignores it.

## Design rules

The look follows StrongMind's design system, Fanny Pack. The token values are copied into the `:root` block at the top of each file.

- Fonts: Poppins for headings and buttons, at weights 500 and 600. Nunito Sans for body text.
- Background: Pearl Brush 50, `#F9F6F3`, a flat warm cream. Court removed the colored background glows, so don't add color or gradients behind the page.
- Primary buttons are Lilac Bush 600, `#8F5CB4`, and hover to 700, `#7E4BA3`. Buttons are pill-shaped.
- Correct feedback uses Highland green. Errors and wrong answers use Cinnabar. Never use those two palettes for anything else.
- Group colors, in order: Lilac Bush, Breaker Bay, Pearl Brush, Cello. Pearl Brush comes third so a three-group sort never has two teal-looking groups side by side. They avoid green and red so a group never looks like right or wrong feedback.
- Frosted cards for the title and directions, and for the Start and End screens: white at 72% opacity, a 16px backdrop blur, a 2px Lilac Bush 200 border (`#D0B4E4`), 20px corners, and a soft floating shadow.
- Group titles and the activity title are centered.
- Every card picture sits in the same 124 by 96 frame, and every group picture in the same 52 by 52 frame, using `object-fit: contain`. That keeps mixed image sizes looking even.
- No emoji. Icons are inline SVG. The Start screen icon is two stacked cards with a check. The End screen icon is a star.
- Copy uses sentence case and no exclamation marks, following Fanny Pack's writing rules. Authors can edit the feedback text.
- Text needs at least 4.5 to 1 contrast.

## Testing

Playwright with Chromium was used for every change. Serve the folder with `python -m http.server`, then check:

- Player: drag, tap, keyboard, Check with a wrong card, the End screen, Play again, a 375px phone width with no sideways scroll, and a bad `?set=` value showing a friendly error.
- Studio: editing, adding media, alt text validation, the group limit, preview updating, Export, Open saved, draft surviving a reload, Clear all, and the built-in preview fallback when the studio file is opened by itself.

Local testing needs a local server because browsers block `file://` fetches. Court uses VS Code Live Server.

## Publishing plan and estimates

These are estimates shared with Court's decision maker. Engineering hasn't confirmed them yet.

What Court could do herself once she can create StrongMind repos, about two days total:

- Create the `sort-interactive` repo and connect it to the existing interactives deploy, about an hour. Someone may need to add the Azure deploy settings if they aren't shared across repos.
- Add a GitHub Action that watches an `inbox/` folder. An author uploads their exported zip there through GitHub's website, and the Action unzips it into `sort/sets/<slug>/` and deploys. About half a day with testing. This isn't built yet.
- Test with the NVDA screen reader, about two hours. It hasn't been tested with a real screen reader yet, so don't claim full screen reader support.
- Pilot with two or three teammates, about half a day.

What still needs engineering, about one to two days:

- A small publish helper, likely an Azure Function or Azure Static Web Apps. It takes the export zip, writes it to the interactives storage, and returns the link.
- StrongMind login in front of the studio and the helper, so only staff can publish.
- Permission for the helper to write to storage, with its key stored safely. Never put keys in the studio page.

## Related StrongMind context

- Another engineer at StrongMind built a hosted Readalong Studio. It publishes to a separate CDN instead of interactives.strongmind.com, and those pieces get placed through the GeoGebra embed instead of the normal interactive placement. Court chose to build her own separate studios so links stay on interactives.strongmind.com and use the normal placement. Its publish service is Readalong-specific and tied to its own login, so it can't be reused directly.
- Older StrongMind interactives came from a Gatsby starter template, where each interactive was copied into its own repo. This project replaces that pattern with one player and many content files.
- Learnosity already has scored classification and match question types. Use those when an activity needs a grade, since these interactives don't report scores.

## Ideas not built yet

- An "Edit a published activity" option that loads a live activity by name instead of from a zip.
- An instant link for text-only activities that packs the content into the URL, with no deploy.
- The Venn diagram and branching scenario studios. They should reuse the same patterns: one player plus a studio, content JSON, Export and Open, the built-in preview fallback, and the same publish helper.
