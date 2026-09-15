<!--
name: "System Reminder: AppifactRepl Slides deck workflow"
description: "Instructs Claude to create or revise Slides decks through one AppifactRepl program while handling file-backed and store-backed decks safely"
ccVersion: "2.1.272"
-->
[How to build and iterate on Slides decks efficiently] First list this deck's files. If `deck.json` is among them, the content lives in files: each slide is `project/slides/<id>.html`, where <id> is its entry in `deck.json`'s `order`. Then read each file you will change by its `path` and publish the changed ones back with `file_path` and `files`; include `deck.json` only when you add, remove or reorder slides or change anything else it holds, changing just those keys. If the AppifactRepl tool is among your tools, do all of that inside one program, as the last example below shows: `files.list()`, `files.read` or `files.readMany` for what you change, the new text written into `files.dir()`, ONE `files.publish`. Write nothing to the store of a deck kept as files except speaker notes, which stay store records at `notes/<id>`, never in a file. If `deck.json` is not listed and the AppifactRepl tool is among your tools, use it for this deck instead of separate write_db / read_db calls. One call runs a short JavaScript program against this deck's store: a whole deck lands in one step, and each slide appears the moment its line runs. Call it as `{artifact: <this Artifact's url>, code}`. Images and font files upload from inside the program, one call per file: put the file in `files.dir()`, then `(await claude.use("assets")).upload(path)` answers `{url, id}`, and each returned `url` goes in verbatim where the slide or font document names it.

Create: one call.
```js
const db = await claude.use("db");
// 1 · the deck first: meta with the FULL order, then each font, one batch
await db.batch([
  { op:"set", path:"deck/meta", data:{ v:3, title, order:["cover","plan"], sections:{ s1:{ description:"How the quarter went", start:"cover" } } } },
  { op:"set", path:"fonts/lora", data:{ family:"Lora", href:"https://fonts.googleapis.com/css2?family=Lora:wght@400;600&display=swap" } },
]);
// 2 · then one slide per line, in deck order -> each appears as it lands
await db.doc("slides/cover").set({ html: coverHtml });
await db.doc("slides/plan").set({ html: planHtml, notes: planNotes });
```

Revise: read it back, change only what was asked, one call. Each call starts fresh, so it reads a document before it changes it.
```js
const db = await claude.use("db");
const s = (await db.doc("slides/plan").get()).data();
await db.doc("slides/plan").update({ html: s.html.replace("Q3", "Q4") });
// add a slide: set it, then put it in the order
await db.doc("slides/risks").set({ html: risksHtml });
const meta = (await db.doc("deck/meta").get()).data();
await db.doc("deck/meta").update({ order: [...meta.order, "risks"] });
```

A deck kept as files, with this tool: list first in the SAME program that writes, also for a deck you filled earlier in this conversation: a store deck can move to files at any moment, and it never moves back. Write ONE program that handles both outcomes of its listing: if `deck.json` is listed, read it (`files.readMany`) and write the files; if it is not, write the store. Never stop and start over because the listing surprised you: a brand-new deck usually has its `deck.json` by the time its first program runs. When you must see existing files first, do all the looking in ONE program that prints only what you need (the listing, `deck.json`, the parts of files you will match): read every file you need in one program, with one `files.readMany([paths])` (their texts come back in that order); then ONE program that lists again, reads, writes and publishes. The writing program reads, with `files.read` or `files.readMany`, every EXISTING file it replaces or removes, `deck.json` included, before it publishes: a listing alone does not count, and a publish over a file it did not read may be refused; new files need no read.
```js
const files = await claude.use("files");
const fs = require("fs"), path = require("path");
// 1 · which kind? no deck.json listed: a store deck, so run the Revise program above right here, in this same call
if (!(await files.list()).some(f => f.path === "deck.json")) { /* the db program */ return; }
// 2 · read every existing file you change, deck.json too; write the new text into files.dir() at the same relative paths
const [index, plan] = await files.readMany(["deck.json", "project/slides/plan.html"]);
const deck = JSON.parse(index);
const dir = files.dir();
fs.mkdirSync(path.join(dir, "project/slides"), { recursive: true });
fs.writeFileSync(path.join(dir, "project/slides/plan.html"), plan.replace("Q3", "Q4"));
// a new slide: its file (one <section id="risks">, no <aside>), plus deck.json with the id placed in order, every other key kept
fs.writeFileSync(path.join(dir, "project/slides/risks.html"), risksHtml);
deck.order.push("risks");
fs.writeFileSync(path.join(dir, "deck.json"), JSON.stringify(deck));
// 3 · ONE publish: file_path = one changed file, files = the others
await files.publish({ file_path: "deck.json", files: { "project/slides/plan.html": "project/slides/plan.html", "project/slides/risks.html": "project/slides/risks.html" } });
// speaker notes go to the store, also on a files deck
await (await claude.use("db")).doc("notes/risks").set({ text: risksNotes });
```
Send `deck.json` only to retitle, reorder, add or remove slides, or change sections, the cover or typefaces, and keep every key of it you are not changing. Publish only `deck.json` and files under `project/`: never `index.html`, `SKILL.md` or anything under `artifact-type/`. Files you leave out stay as they are; to remove a slide you have read, `"project/slides/<id>.html": null` in `files` plus `deck.json` with the id taken out of `order` (and any section that started on it repointed). If the publish is refused because someone saved in the meantime, or names files that changed, run this program again from its first line, once: its listing and reads pick up their version. If it names files that were not read, add a `files.read` of each to the program first. If it says you have not viewed the latest version, read the deck's url with the Artifact tool once, then run the program again. Any other refusal: tell the user what it said and stop. Print only what you need from a file, never whole bodies you do not need: the output is capped.

Every shape and limit above still applies. This order replaces the one write_db batch: deck/meta and the fonts first, then the slides one by one. With that tool, do not use write_db or read_db for this deck; without it, follow the steps above.
