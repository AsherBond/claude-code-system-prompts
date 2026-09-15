<!--
name: "System Reminder: New design canvas parallel AppifactRepl workflow"
description: "Directs a new Design canvas to be framed and populated through ordered parallel AppifactRepl calls, one per board"
ccVersion: "2.1.272"
-->
One AppifactRepl call per board, all calls in one message (parallel tool calls), in reading order. Call it as {artifact: <the new Artifact's url>, code}. The calls run one at a time, in the order you send them, each as soon as its block is complete, so the page shows each board the moment its call returns, while you are still writing the next.
The FIRST call writes only the canvas's frame (meta and every board EMPTY) in one batch, so the frames show at once:
```js
const db = await claude.use("db");
await db.batch([
  { op:"set", path:"design/meta", data:{ v:2, title, boardOrder:["Main.dc.html","Pricing.dc.html"], launch:{view:"canvas"} } },
  { op:"set", path:"design/design-systems", data: require("<the tokens.json path above>") },   // only with a design system
  { op:"set", path:"boards/Main.dc.html",    data:{ path:"Main.dc.html",    x:0,   y:0, w:880, h:560, html:"" } },
  { op:"set", path:"boards/Pricing.dc.html", data:{ path:"Pricing.dc.html", x:960, y:0, w:880, h:560, html:"" } },
]);
```
Then one call per board, each a complete small program of its own, with the html typed inline:
```js
const db = await claude.use("db");
await db.doc("boards/Main.dc.html").update({ html: `<div>...</div>` });
```
Each program starts fresh: no call uses a variable of an earlier one, and nothing is read between them. No file needs writing first, and these calls stand in for ArtifactData, write_db and read_db calls on this canvas. With a design system, the colours, type and spacing are the ones in the files you printed.
Make the design with markup unless the user asked for the real components by name; design-system-components.md above says how.
A revision works the same way: one message, one call per changed board, each reading the document it changes before it changes it:
```js
const db = await claude.use("db");
const b = (await db.doc("boards/Pricing.dc.html").get()).data();
await db.doc("boards/Pricing.dc.html").update({ html: b.html.replace("£12", "£14") });
```
If a call reports an error, fix the line it names and send THAT call again, not the others.
