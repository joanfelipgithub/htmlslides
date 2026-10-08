# SCORM Step Marker & Quiz Builder

Turn a folder of screenshots into an interactive tutorial with numbered step pins and quiz questions, and export it as a **SCORM 1.2 package**, designed for Moodle (other SCORM 1.2 LMSs should work too).

Everything runs **in your browser**. There is no server, no account and no tracking: your images are read locally and never uploaded anywhere. The page is the *engine*; your slides stay on your computer.

## Quick start

1. Open the page (GitHub Pages URL), or download this repository and open `index.html` in a browser. It also works offline.
2. Click **➕ Add Images** and select the screenshots, in any order.
3. Click a slide's ✏️ to edit it. Click on the image to place a numbered pin, add a tooltip, and optionally a quiz question.
4. Check the result with **▶ Presentation & Quiz Mode**.
5. Click **💾 Export SCORM Package (.zip)**.
6. In Moodle: *Add an activity or resource → SCORM package*, upload the `.zip`, and save. (Menu names vary a little between Moodle versions and languages.)

Use **📄 Save Project (.html)** to keep your work; open it later with **📂 Load Saved HTML**. A saved project is a single file with the images embedded.

## What you can do

| Area | Details |
|---|---|
| **Slides** | Add several images at once, drag tiles to reorder (or use ◄ ►), delete. |
| **Pins** | Click to place, drag to move. Step number, rotation, fill/border colour and a hover tooltip. |
| **Quiz** | Per slide: none, multiple choice (A–D) or true/false, with the correct answer. Answers lock after the first click. |
| **Data table** | All pins and questions of the project as a spreadsheet. Edit cells, copy the table, or paste/import rows from Excel. |
| **Print / PDF** | **Ctrl+P** (or 🖨️) prints every slide on its own page, with tooltips and quiz questions listed under the image. Choose *Save as PDF* as the printer. |
| **Export** | SCORM 1.2 `.zip` with `imsmanifest.xml`, `index.html` and an `images/` folder. |

### Keyboard and mouse

| Where | Action |
|---|---|
| Editor | `←` `→` previous / next slide. Mouse wheel over the image does the same when no pin is selected. |
| Editor, pin selected | Mouse wheel rotates the pin · `Delete` removes it · `Esc` deselects. |
| Data table | Click a cell, then arrow keys move the selection · `Enter`, `F2` or double-click edits · `Enter` confirms · `Esc` cancels. |
| Data table | `Ctrl+V` pastes rows copied from Excel. |
| Anywhere | `Ctrl+P` prints all slides, one per page. |

## Importing from Excel

Open **📥 Import Quiz / Pins** (or click the data table and press `Ctrl+V`) and paste tab-separated rows. See [`examples/quiz-import-example.tsv`](examples/quiz-import-example.tsv).

Quiz rows: `Slide | MC or TF | Question | A | B | C | D | Correct`

```
Slide 1	TF	A voltmeter is connected in parallel with the component.					True
Slide 3	MC	Which unit measures electric current?	Volt	Ampere	Ohm	Watt	Ampere
```

The table produced by **📋 Copy Table** can be edited in Excel and pasted back. Its columns are: Slide, Pin #, Tooltip / Question Text, Quiz Type, Correct Answer, Outline (HEX), BG Color (HEX), Rel X %, Rel Y %, Rotation, Opt A–D and Pin ID. Pins are matched by Pin ID, so changing a pin number never creates a duplicate. A header row is ignored. Slides that do not exist yet are created as blank placeholders; images cannot come from Excel.

## What is reported to the LMS (SCORM 1.2)

| SCORM element | Content |
|---|---|
| `cmi.core.score.raw` (min 0, max 100) | Percentage of correct quiz answers. Unanswered questions count as wrong. |
| `cmi.core.lesson_status` | `incomplete` while running; `passed` / `failed` when the learner reaches the last slide (pass mark **70 %**). A status from an earlier session is never downgraded. |
| `cmi.core.session_time` | Active time in the slides. Time with the browser tab in the background is not counted. |
| `cmi.core.lesson_location` | Current slide number. |
| `cmi.suspend_data` | Per-slide time, visits and first-opened time of day, plus the learner's answers, so a re-launch resumes where it stopped. |
| `cmi.interactions.n.*` | One record per answered question: id, type, correct response, learner response, result, latency and time of day. Shown by Moodle's *Interactions* report. |

`suspend_data` looks like this:

```
v1|c=7|i=3|a=1:True,2:True,7:D|t=1,4,2,13:47:19;2,2,2,13:47:21
```

`c` current slide · `i` interaction records sent · `a` answers (`slide:answer`) · `t` one entry per visited slide: `slide,seconds,visits,first-opened time`. Times of day come from the learner's computer clock.

Data is sent on every slide change, every 30 seconds, when the tab is hidden and when the window closes.

## Notes and limitations

- SCORM **1.2** only. The pass mark (70 %) is fixed in the page.
- One quiz question per slide.
- Project files embed the images as base64, so large decks make large files.
- The interface is in English.
- Use a test student account in your own Moodle to check scoring and the timing data before using a package with a class: a teacher's *Preview* typically does not record tracking data.
- Developed and tested in Chromium-based browsers (Chrome, Edge). Current Firefox and Safari should work but are less tested.

## Repository layout

```
index.html                  the whole application (HTML + CSS + JavaScript, no build step)
vendor/jszip.min.js         JSZip 3.10.1, used to create the .zip (loaded locally; the CDN is only a fallback)
vendor/JSZIP-LICENSE.markdown
examples/quiz-import-example.tsv
LICENSE
```

To run it locally, open `index.html` in a browser. To publish it, enable **GitHub Pages** (Settings → Pages → deploy from the `main` branch, root folder).

## Development

There is nothing to build: edit `index.html` and reload. Exported packages contain a copy of this page with the slide data embedded, which is why the engine is a single self-contained file. To update JSZip, replace `vendor/jszip.min.js` and its license file.

## License

[MIT](LICENSE). JSZip is bundled under its own license (MIT or GPLv3), see [`vendor/JSZIP-LICENSE.markdown`](vendor/JSZIP-LICENSE.markdown).
