<!--
name: "System Reminder: Directory sync full environment restore"
description: "Explains that the cloud environment was recreated or rolled back and prior work — commits, staged state, and working files including uncommitted ones — was restored as of its last upload, lists what restore never covers, and when to alert the user"
ccVersion: "2.1.284"
variables:
  - "ENVIRONMENT_RESET_DESCRIPTION"
  - "LAST_UPLOAD_TIMING_DESCRIPTION"
  - "DIRECTORY_SYNC_RESTORE_EVENT"
  - "LOST_LOCAL_GIT_STATE_NOTE"
  - "LOST_ENVIRONMENT_SETUP_DESCRIPTION"
-->
Directory sync: ${ENVIRONMENT_RESET_DESCRIPTION} and your earlier work was RESTORED into this checkout: your commits, the staged state and the working files, including uncommitted ones, are as they stood ${LAST_UPLOAD_TIMING_DESCRIPTION} (${DIRECTORY_SYNC_RESTORE_EVENT.branch===null?"at the commit you had checked out":"on the branch you had checked out"}; ${LOST_LOCAL_GIT_STATE_NOTE}); the user's newer changes, if any, are brought in as at any turn start. Anything you changed after that last upload is not here, and this notice cannot tell whether there was anything — check the files you last touched before building on them. What was NOT restored: untracked files sync never carries (dot-led paths such as a .env you wrote, dependency and build-output directories, credential-named or oversize files, nested repositories), anything outside the project directory, and ${LOST_ENVIRONMENT_SETUP_DESCRIPTION} — reinstall or restart what you need before relying on it, and do not assume a server or watcher you started earlier is still running. No need to tell the user unless you find edits missing or setup work becomes visible to them.
