<!--
name: "Tool Description: Artifact type file-backed content update guidance"
description: "Explains how to detect file-backed Artifact type content, read every changed file, republish it to the same URL, and preserve the type's index metadata"
ccVersion: "2.1.271"
variables:
  - "ARTIFACT_ACTION_NAMES"
  - "FORMAT_ARTIFACT_INDEX_FILENAMES_FN"
  - "ARTIFACT_TYPE_FILE_STORAGE_CONFIG"
  - "ARTIFACT_FILE_STORAGE_MARKER"
  - "FORMAT_ARTIFACT_FILE_UPDATE_PUBLISH_INSTRUCTIONS_FN"
  - "ARTIFACT_URL"
-->
List its files first, before any other call (${ARTIFACT_ACTION_NAMES.list}). If ${FORMAT_ARTIFACT_INDEX_FILENAMES_FN(ARTIFACT_TYPE_FILE_STORAGE_CONFIG.indexes)} is among them (`${ARTIFACT_TYPE_FILE_STORAGE_CONFIG.indexes[0]}` if both are), read it (${ARTIFACT_ACTION_NAMES.read}): when it holds ${ARTIFACT_FILE_STORAGE_MARKER}, this Artifact's content lives in its own files, and what the instructions below say about ${ARTIFACT_TYPE_FILE_STORAGE_CONFIG.storeDocs} does not apply to its content: ${ARTIFACT_TYPE_FILE_STORAGE_CONFIG.path}. Read each file you will change and ${FORMAT_ARTIFACT_FILE_UPDATE_PUBLISH_INSTRUCTIONS_FN(ARTIFACT_URL)}. Include that index only when you ${ARTIFACT_TYPE_FILE_STORAGE_CONFIG.indexEdits}, changing just those keys and keeping every other key and that marker as read. ${ARTIFACT_TYPE_FILE_STORAGE_CONFIG.store}${ARTIFACT_TYPE_FILE_STORAGE_CONFIG.olderForm} Otherwise (${ARTIFACT_TYPE_FILE_STORAGE_CONFIG.storeWhen}): 
