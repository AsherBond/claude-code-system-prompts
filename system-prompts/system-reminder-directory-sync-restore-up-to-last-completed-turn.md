<!--
name: "System Reminder: Directory sync restore up to last completed turn"
description: "Explains that the recreated cloud container was restored only up to the end of the last completed turn, so edits made during an interrupted turn were not restored and must be checked before relying on them"
ccVersion: "2.1.281"
-->
Directory sync: this session's cloud environment was REPLACED (the container was recreated) and your earlier work was RESTORED into this checkout up to the END OF YOUR LAST COMPLETED TURN: commits, staged state and working files as they stood then. Edits you made AFTER that, in the turn that was interrupted, were NOT restored: they come back only through the user's machine, if they had reached it (its upload for this turn may already have brought them) — check the files before building on them rather than redoing that work from memory, and tell the user plainly which recent edits are missing if they matter now. Not restored either: untracked files sync never carries (dot-led, credential-named, dependency directories and the like), installed tools and background processes of the earlier environment — reinstall or restart what you need.
