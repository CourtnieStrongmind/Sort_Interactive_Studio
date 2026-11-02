# Sort Interactive

A reusable sorting activity. Students drag cards into 2 to 4 groups, press Check, and try again on any card that's wrong. Authors build activities in the Sort Studio, with no code.

## Style

Both the player and the Studio use StrongMind's Fanny Pack design system: Poppins for headings and buttons, Nunito Sans for body text, Lilac Bush pill buttons, Pearl Brush backgrounds, Highland for correct, and Cinnabar for errors. Group colors use Lilac Bush, Breaker Bay, Cello, and Pearl Brush, so they never look like right or wrong feedback. Icons are inline SVG, with no emoji.

## Folder layout

```
sort/
  index.html                  The player students see
  sets/
    <activity-name>/          One folder per activity
      content.json            Text, groups, cards, and media file names
      media/                  Pictures and audio for that activity
studio/
  sort/
    index.html                The Sort Studio, where authors build activities
tools/
  build_studio.py             Copies the player into the Studio for its preview
```

## Links

These assume the repo is named `sort-interactive`, following the usual `https://interactives.strongmind.com/[repo-name]/[file-name].html` pattern. If the repo gets a different name, change `CONFIG.liveBase` near the top of the script in `studio/sort/index.html`.

- Player: `https://interactives.strongmind.com/sort-interactive/sort/index.html?set=<activity-name>`
- Studio: `https://interactives.strongmind.com/sort-interactive/studio/sort/index.html`
- Sample: `https://interactives.strongmind.com/sort-interactive/sort/index.html?set=sample-living-nonliving`

The Studio shows the student link, with a Copy button, after an activity is published.

Embed the player link in Helios the same way as other interactives. It includes iframe-resizer 4.2.11, so the frame grows to fit.

## How authors make an activity (for now)

1. Open the Studio and fill in Basics, Groups, Cards, and Feedback. The preview updates as you type. For directions, pick one of the starters or choose Write my own. Starters fill in the group names automatically.
2. Fix anything listed under "Ready to publish?"
3. Click **Export**. You get `<activity-name>.zip`.
4. Unzip it into `sort/sets/`, then branch, commit, open a PR, merge, and deploy as usual.
5. The link is the full student link shown in the Studio, for example `https://interactives.strongmind.com/sort-interactive/sort/index.html?set=<activity-name>`.

To edit an activity later, click **Open saved** in the Studio and choose the zip. The Studio also keeps a draft in the browser automatically. That draft lives only in that person's browser on that computer. Other people never see it, and clearing browser data removes it, so Export is the real save.

## One-click publishing (later)

The Publish button is already wired up. It's off until a publish helper exists. When it does, set `CONFIG.publishUrl` near the top of the script in `studio/sort/index.html`. The Studio will POST a form with:

- `slug`: the activity name
- `bundle`: the same zip that Export makes

It expects JSON back: `{ "url": "https://..." }`. The helper's job is to unzip the bundle into `sort/sets/<slug>/`, either in blob storage or as a commit, and return the link.

## Changing the player

The Studio keeps a built-in copy of the player so its preview works anywhere, even if the Studio file is opened on its own. After any change to `sort/index.html`, run:

```
python tools/build_studio.py
```

## Content rules

- 2 to 4 groups. Each needs a name, a picture, or both.
- 2 to 24 cards. Each needs text, a picture, audio, or any mix, plus a group.
- Every picture needs alt text.
- Images up to 2 MB. Large photos are scaled down to 800 pixels on the longest side when added. Every card picture shows in the same 124 by 96 frame, and every group picture in the same 52 by 52 frame, so mixed sizes still look even.
- Audio up to 5 MB (MP3 recommended).
- Activity names use only lowercase letters, numbers, and dashes.
