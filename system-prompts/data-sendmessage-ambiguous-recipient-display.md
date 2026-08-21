<!--
name: "Data: SendMessage ambiguous recipient display"
description: "Formats the SendMessage failure display when a recipient name is ambiguous or requires confirmation, including unavailable-session, truncated-search, and pinned-identity warnings"
ccVersion: "2.1.239"
variables:
  - "RECIPIENT_RESOLUTION_RESULT"
  - "SEND_MESSAGE_INPUT"
  - "HAS_UNAVAILABLE_SESSION_SOURCES"
  - "UNAVAILABLE_SESSION_SOURCES_SUMMARY"
  - "FIRST_RECIPIENT_CANDIDATE"
  - "RECIPIENT_CANDIDATES_SUMMARY"
  - "SEARCH_TRUNCATION_NOTE"
  - "PINNED_IDENTITY_CLAIM_WARNING"
-->
${RECIPIENT_RESOLUTION_RESULT.total === 1 ? (RECIPIENT_RESOLUTION_RESULT.matchedBy === "prefix" ? `Not sent — no agent is named '${SEND_MESSAGE_INPUT.to}' exactly${HAS_UNAVAILABLE_SESSION_SOURCES ? ` among the sessions that could be checked (${UNAVAILABLE_SESSION_SOURCES_SUMMARY})` : ""}; asked Claude to confirm it means '${FIRST_RECIPIENT_CANDIDATE.name}'.` : RECIPIENT_RESOLUTION_RESULT.pinnedIdentityClaimedLocally ? `Not sent — '${SEND_MESSAGE_INPUT.to}' needs a confirm before this send.` : HAS_UNAVAILABLE_SESSION_SOURCES ? `Not sent yet — '${SEND_MESSAGE_INPUT.to}' matches '${FIRST_RECIPIENT_CANDIDATE.name}', but ${UNAVAILABLE_SESSION_SOURCES_SUMMARY}; asked Claude to confirm.` : `Not sent — '${SEND_MESSAGE_INPUT.to}' needs a one-time confirm before this send; asked Claude to confirm it means '${FIRST_RECIPIENT_CANDIDATE.name}'.`) : RECIPIENT_RESOLUTION_RESULT.matchedBy === "prefix" ? `Not sent — '${SEND_MESSAGE_INPUT.to}' matches ${RECIPIENT_RESOLUTION_RESULT.total} agents by prefix (${RECIPIENT_CANDIDATES_SUMMARY}${RECIPIENT_RESOLUTION_RESULT.total > RECIPIENT_RESOLUTION_RESULT.candidates.length ? ", …" : ""}); asked Claude to pick one.` : `Not sent — ${RECIPIENT_RESOLUTION_RESULT.total} agents are named '${SEND_MESSAGE_INPUT.to}' (${RECIPIENT_CANDIDATES_SUMMARY}${RECIPIENT_RESOLUTION_RESULT.total > RECIPIENT_RESOLUTION_RESULT.candidates.length ? ", …" : ""}); asked Claude to pick one.`}${HAS_UNAVAILABLE_SESSION_SOURCES && RECIPIENT_RESOLUTION_RESULT.total !== 1 ? ` Note: ${UNAVAILABLE_SESSION_SOURCES_SUMMARY}.` : ""}${SEARCH_TRUNCATION_NOTE}${PINNED_IDENTITY_CLAIM_WARNING}
