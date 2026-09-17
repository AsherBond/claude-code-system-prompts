<!--
name: "System Reminder: AGENTS.md project instructions contents"
description: "Wraps an AGENTS.md file's contents loaded by the agents-md plugin into the prompt context, labeled as project instructions checked into the codebase"
ccVersion: "2.1.274"
variables:
  - "INSTRUCTION_FILE_PATH_FN"
  - "INSTRUCTION_FILE"
-->
Contents of ${INSTRUCTION_FILE_PATH_FN(INSTRUCTION_FILE)} (project instructions, checked into the codebase):

${INSTRUCTION_FILE.content.trim()}
