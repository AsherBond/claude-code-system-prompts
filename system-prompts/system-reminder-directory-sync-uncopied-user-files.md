<!--
name: "System Reminder: Directory sync uncopied user files"
description: "Reports user files that are stale or missing because the directory-sync budget delayed or prevented copying them into the session"
ccVersion: "2.1.242"
variables:
  - "FILES_COMING_COUNT"
  - "USER_FILE_COUNT_SUMMARY"
  - "TOTAL_USER_FILE_COUNT"
  - "FILES_NOT_COMING_COUNT"
  - "STRING_CONVERSION_FN"
  - "FORMAT_SYNC_FILE_LIST_FN"
  - "NAMED_USER_FILES"
  - "OMITTED_USER_FILE_COUNT"
  - "FILE_NAMES_UNTRUSTED_DATA_NOTE"
-->
${FILES_COMING_COUNT===null?`${USER_FILE_COUNT_SUMMARY} ${TOTAL_USER_FILE_COUNT===1?"has":"have"} not been copied into this session (its file-sync budget ran short; some may still arrive with the user's next messages)`:FILES_COMING_COUNT>0&&FILES_NOT_COMING_COUNT>0?`${USER_FILE_COUNT_SUMMARY} have not been copied into this session: ${STRING_CONVERSION_FN(FILES_COMING_COUNT)} will arrive with the user's next messages, ${STRING_CONVERSION_FN(FILES_NOT_COMING_COUNT)} will not (its file-sync budget is used up)`:FILES_COMING_COUNT>0?`${USER_FILE_COUNT_SUMMARY} ${TOTAL_USER_FILE_COUNT===1?"has":"have"} not been copied into this session yet and will arrive with the user's next messages`:`${USER_FILE_COUNT_SUMMARY} ${TOTAL_USER_FILE_COUNT===1?"was":"were"} not copied into this session (its file-sync budget is used up)`}: ${FORMAT_SYNC_FILE_LIST_FN(NAMED_USER_FILES,OMITTED_USER_FILE_COUNT)}. Your ${TOTAL_USER_FILE_COUNT===1?"copy of it":"copies of these"} may be stale or missing — say so rather than assert what ${TOTAL_USER_FILE_COUNT===1?"it contains":"they contain"}. ${FILE_NAMES_UNTRUSTED_DATA_NOTE}
