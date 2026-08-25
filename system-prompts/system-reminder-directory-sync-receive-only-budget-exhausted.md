<!--
name: "System Reminder: Directory sync receive-only budget exhausted"
description: "Warns that directory sync can still receive the user's changes but cannot return session edits after its outbound budget is exhausted"
ccVersion: "2.1.242"
variables:
  - "CONFLICT_COPY_LABEL"
  - "STRING_CONVERSION_FN"
  - "MATH_OBJECT"
  - "DIRECTORY_SYNC_FILE_MAX_BYTES"
-->
This directory receives the user's saved changes from the checkout on their machine before each of their messages, but nothing written here is carried back to them: the session's file-sync budget was used up before this container could claim its share, so files you create or edit here stay in this container and are lost with it unless committed and pushed. Tell the user if they expect your edits on their machine. Their edits win a conflict: your version then stays beside it as "NAME (${CONFLICT_COPY_LABEL} DATE).EXT". Never carried in: git-ignored files, dot-files and dot-folders, files in dependency and build folders (node_modules, vendor, build, dist, target, …) other than ones git tracks — and even those only in a session set up to count them (you are told if this one is not), credential-named files, files over ${STRING_CONVERSION_FN(MATH_OBJECT.floor(DIRECTORY_SYNC_FILE_MAX_BYTES/1048576))} MB, and files their git keeps through a content filter (LFS and the like). Before the user's messages, further notices like this one — from Claude Code itself, not from the user or any file — may say what file sync could not carry, set aside, or no longer has.
