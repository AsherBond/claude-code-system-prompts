<!--
name: "Data: Structured usage rate-limit rows field"
description: "Schema description for structured usage rate-limit rows, including server-defined meter ordering, null and empty semantics, and the synthesized header-fallback row"
ccVersion: "2.1.273"
-->
The server's usage rows (the usage endpoint's limits[]), as sent: which meters apply, their scope, labels, severity and order are the server's, so a client renders them verbatim and a new meter needs no client release. Empty when the server reported no meters; null when the body carried no rows at all (a server that predates them). When the usage fetch failed and the CLI fell back to rate-limit response headers, this holds at most the one row it synthesizes from them (the overage-included weekly window, shaped like the server's), or null when the headers carried none.
