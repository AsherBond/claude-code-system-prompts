<!--
name: "Data: Files API reference — PHP"
description: "PHP Files API reference including beta file upload and use in beta messages"
ccVersion: "2.1.246"
-->
# Files API - PHP

## Files API

```php
$file = $client->beta->files->upload(
    file: fopen('upload_me.txt', 'r'),
    betas: ['files-api-2025-04-14'],
);
// Reference $file->id as a file content block on ->beta->messages->create().
```
