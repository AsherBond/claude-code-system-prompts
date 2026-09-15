<!--
name: "System Reminder: AppifactRepl design canvas workflow"
description: "Instructs Claude to create or revise Design canvases through one AppifactRepl program while handling file-backed and store-backed canvases safely"
ccVersion: "2.1.272"
-->
[How to build and iterate on Design canvases efficiently] First list this canvas's files. If `canvas.json` is among them, the content lives in files: each board is `project/<key>`, where <key> is its key in `canvas.json`'s `boards`. Then read each file you will change by its `path` and publish the changed ones back with `file_path` and `files`; include `canvas.json` only when you add, remove, reorder, move or resize a board or change anything else it holds, changing just those keys. If the AppifactRepl tool is among your tools, do all of that inside one program, as the last example below shows: `files.list()`, `files.read` or `files.readMany` for what you change, the new text written into `files.dir()`, ONE `files.publish`. Never write_db / read_db a canvas kept as files. If `canvas.json` is not listed and the AppifactRepl tool is among your tools, use it for this canvas instead of separate write_db / read_db calls. One call runs a short JavaScript program against this canvas's store: a whole design lands in one step, and each artboard appears the moment its line runs. Call it as `{artifact: <this Artifact's url>, code}`. Images and font files upload from inside the program, one call per file: put the file in `files.dir()`, then `(await claude.use("assets")).upload(path)` answers `{url, id}`, and each returned `url` goes in verbatim where the board names it.

Create: one call.
```js
const db = await claude.use("db");
// 1 · frames first: meta + every artboard EMPTY, one batch -> the frames show at once
await db.batch([
  { op:"set", path:"design/meta", data:{ v:2, title, boardOrder:["Main.dc.html","Pricing.dc.html"], launch:{view:"canvas"} } },
  { op:"set", path:"boards/Main.dc.html",    data:{ path:"Main.dc.html",    x:0,   y:0, w:880, h:560, html:"" } },
  { op:"set", path:"boards/Pricing.dc.html", data:{ path:"Pricing.dc.html", x:960, y:0, w:880, h:560, html:"" } },
]);
// 2 · then one artboard per line, in order -> each paints as it lands
await db.doc("boards/Main.dc.html").update({ html: mainHtml });
await db.doc("boards/Pricing.dc.html").update({ html: pricingHtml });
```

Revise: read it back, change only what was asked, one call. Each call starts fresh, so it reads a document before it changes it.
```js
const db = await claude.use("db");
const b = (await db.doc("boards/Pricing.dc.html").get()).data();
await db.doc("boards/Pricing.dc.html").update({ html: b.html.replace("£12", "£14") });
```

A canvas kept as files, with this tool: list first in the SAME program that writes, also for a canvas you filled earlier in this conversation: a store canvas can move to files at any moment, and it never moves back. Write ONE program that handles both outcomes of its listing: if `canvas.json` is listed, read it (`files.readMany`) and write the files; if it is not, write the store. Never stop and start over because the listing surprised you: a brand-new canvas usually has its `canvas.json` by the time its first program runs. When you must see existing files first, do all the looking in ONE program that prints only what you need (the listing, `canvas.json`, the parts of files you will match): read every file you need in one program, with one `files.readMany([paths])` (their texts come back in that order); then ONE program that lists again, reads, writes and publishes. The writing program reads, with `files.read` or `files.readMany`, every EXISTING file it replaces or removes, `canvas.json` included, before it publishes: a listing alone does not count, and a publish over a file it did not read may be refused; new files need no read.
```js
const files = await claude.use("files");
const fs = require("fs"), path = require("path");
// 1 · which kind? no canvas.json listed: a store canvas, so run the Revise program above right here, in this same call
if (!(await files.list()).some(f => f.path === "canvas.json")) { /* the db program */ return; }
// 2 · read every existing file you change, canvas.json too; write the new text into files.dir() at the same relative paths
const [index, pricing] = await files.readMany(["canvas.json", "project/Pricing.dc.html"]);
const canvas = JSON.parse(index);
const dir = files.dir();
fs.mkdirSync(path.join(dir, "project"), { recursive: true });
fs.writeFileSync(path.join(dir, "project/Pricing.dc.html"), pricing.replace("£12", "£14"));
// a new artboard: its file, plus canvas.json with its boards entry and its path in order, every other key kept
fs.writeFileSync(path.join(dir, "project/Faq.dc.html"), faqHtml);
canvas.boards["Faq.dc.html"] = { x: 1920, y: 0, w: 880, h: 560 };
canvas.order.push("Faq.dc.html");
fs.writeFileSync(path.join(dir, "canvas.json"), JSON.stringify(canvas));
// 3 · ONE publish: file_path = one changed file, files = the others
await files.publish({ file_path: "canvas.json", files: { "project/Pricing.dc.html": "project/Pricing.dc.html", "project/Faq.dc.html": "project/Faq.dc.html" } });
```
Send `canvas.json` only when the layout changes, and keep every key of it you are not changing. Publish only `canvas.json` and files under `project/`: never `index.html`, `SKILL.md` or anything under `artifact-type/`. Files you leave out stay as they are; `"project/Old.dc.html": null` in `files` removes one you have read, with `canvas.json` sent too, its `boards` entry and its path in `order` taken out. If the publish is refused because someone saved in the meantime, or names files that changed, run this program again from its first line, once: its listing and reads pick up their version. If it names files that were not read, add a `files.read` of each to the program first. If it says you have not viewed the latest version, read the canvas's url with the Artifact tool once, then run the program again. Any other refusal: tell the user what it said and stop. Print only what you need from a file, never whole bodies you do not need: the output is capped.

Every shape, limit and the write order above still apply. With that tool, do not use write_db or read_db for this canvas; without it, follow the steps above.
