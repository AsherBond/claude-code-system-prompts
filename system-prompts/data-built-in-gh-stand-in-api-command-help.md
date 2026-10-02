<!--
name: "Data: Built-in gh stand-in api command help"
description: "Usage text for Claude Code's built-in gh stand-in, which supports only gh api REST requests to GitHub hosts through the session's GitHub proxy, listing its flags and how {owner}, {repo} and {branch} placeholders are filled in"
ccVersion: "2.1.287"
variables:
  - "NO_GITHUB_CLI_REASON"
  - "GITHUB_API_HOST_LIST"
  - "GITHUB_CLI_LATER_INSTALL_NOTE"
  - "HAS_ADDITIONAL_GITHUB_HOSTS"
  - "DEFAULT_GITHUB_HOST"
-->
Usage: gh api <endpoint> [flags]

This gh is Claude Code's built-in stand-in, because ${NO_GITHUB_CLI_REASON}.
It makes GitHub REST API requests to ${GITHUB_API_HOST_LIST} through this session's GitHub
proxy and supports no other gh command. ${GITHUB_CLI_LATER_INSTALL_NOTE}

Flags:${HAS_ADDITIONAL_GITHUB_HOSTS?`
      --hostname <host>     GitHub host of the request, as GH_HOST (default ${DEFAULT_GITHUB_HOST}, or the
                            host of the repository that fills {owner}, {repo} in the endpoint)`:""}
  -X, --method <method>     HTTP method (default GET, or POST with parameters or --input)
  -f, --raw-field key=value String parameter
  -F, --field key=value     Typed parameter: true, false, null and integers become JSON
                            values, {owner} {repo} {branch} are filled in, @file reads a
                            file, @- reads standard input
  -H, --header 'Name: v'    Request header
      --input <file>        File to send as the request body (- for standard input)
  -i, --include             Print the response status line and headers
      --paginate            Follow the response's rel="next" links (GET only)
  -q, --jq <expression>     Filter the response with jq (needs jq on PATH)
      --silent              Do not print the response body

{owner}, {repo} and {branch} (or :owner, :repo, :branch) in the endpoint and in
-F values are filled in from GH_REPO, else from the ${HAS_ADDITIONAL_GITHUB_HOSTS?"remotes on those hosts":`${DEFAULT_GITHUB_HOST} remotes`} of the
current repository: upstream, github, origin, then the others by name.

Example: gh api repos/{owner}/{repo}/pulls -f title='Fix' -f head='my-branch' -f base='main'
