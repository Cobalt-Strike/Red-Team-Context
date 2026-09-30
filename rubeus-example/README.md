# Rubeus Module

This module runs Rubeus through Cobalt Strike `bexecute_assembly`, parses the plain-text output, and adds credential material to the Cobalt Strike credential store.

The parser is self-contained and does not depend on the shared REST core library. Its `/json` output mode uses a private compact JSON serializer embedded in `rubeus_proxy.cna`.

## Files

- `rubeus_proxy.cna`: Rubeus command alias, output parser, credential extraction, and credential-store publication logic.
- `rubeus_extension.cna`: Credential-store popup actions that build Rubeus commands from selected credential rows.

## Load

Load directly:

```c
include(script_resource("modules/rubeus/rubeus_proxy.cna"));
```

From Script Manager, load `modules/rubeus/rubeus_proxy.cna`.

If you want right-click credential actions for Rubeus, load `modules/credentials/credentials_extension.cna` or `modules/rubeus/rubeus_extension.cna`.

## Configuration

Copy `local.example.cna` to `local.cna` and set:

```c
$rubeus_path = "../tools/ghostpack/Rubeus.exe";
```

Use a path that is valid from the Cobalt Strike client environment.

## Commands

```text
rubeus <arguments>
```

Examples:

```text
rubeus kerberoast
rubeus asktgt /user:alice /password:Password123 /domain:corp.local
rubeus renew /user:alice /ticket:<base64kirbi> /dc:corp.local
```

## Parser Behavior

- Automatically appends `/nowrap` for commands where wrapped base64 or hashes break parsing.
- `/json` prints parsed JSON to the Beacon log.
- `/nojson` keeps normal output behavior. This is the default.
- Parsed results are available through `rubeus_parser_result($bid)`.
- Captured text can be parsed directly with `rubeus_parse_text($text, $command)`.
- `triage` output is displayed in a `Rubeus Triage` custom table when `openUserDefinedBrowser` is available.
- If `/outfile` keeps roast material out of stdout, the parser logs why no credential was added.

## Credential Store

The parser adds supported credential material such as Kerberos tickets, roast hashes, and key material when username and realm context can be resolved from output.

Ticket credentials keep related metadata in notes, including service, realm, flags, ticket times, key type, base64 session key, and ASREP key where present. Kerberoast and ASREP material is stored as hash material rather than being treated as an `asktgt` secret.

## Credential Popup Actions

`rubeus_extension.cna` adds a `Rubeus` submenu to credential rows:

- `asktgt`: Available for rows with user and realm context plus password, RC4/NTLM, AES, or certificate material.
- `Renew ticket`: Available for ticket/kirbi/ccache rows.

The `asktgt` builder handles free-form credential type values and also infers common formats from the secret itself:

- 64 hex characters -> `/aes256`
- 32 hex characters -> `/rc4`
- `.pfx`, `.p12`, or common base64 certificate prefixes -> `/certificate`
- other non-ticket secrets -> `/password`
