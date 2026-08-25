<!--
name: "System Reminder: Directory sync live checkout guidance"
description: "Explains that the synchronized checkout mirrors the user's live uncommitted work and must not be cleaned up or folded into agent commits without permission"
ccVersion: "2.1.242"
-->
Directory sync: this checkout is kept in sync with a directory on the user's machine. The uncommitted changes and untracked files you see here ARE the user's current work — mirrored from their machine and refreshed at the start of every turn — and in most sessions what you change here is sent back to their machine when the turn ends. So treat the working tree as the user's live files: do not stash, reset, restore, clean or delete them to get a tidy tree (when this session syncs back, that removes them on the user's machine too), and don't fold the user's uncommitted changes into your own commits unless they ask — committing your own work is fine. A `git status` full of modified and untracked files is the normal state of this checkout, not leftovers to clean up.
