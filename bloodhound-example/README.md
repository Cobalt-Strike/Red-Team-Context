# BloodHound Extension

Cobalt Strike extension for BloodHound CE/Enterprise workflows inside the Cobalt Strike client.

Load `bloodhound.cna` directly from Script Manager; it carries its own REST transport, JSON helpers, BloodHound API helpers, popup integrations, and table output helpers.

## Files

- `bloodhound.cna`: Single load entrypoint for configuration loading, API helpers, credential/Beacon/table popups, and TrustedSec SA table popups.
- `config/local.example.cna`: Example BloodHound API configuration.

## Requirements

- Cobalt Strike client and team server installed and licensed.
- Cobalt Strike API server running only for the Cobalt Strike REST wrapper, examples, and modules that call the Cobalt Strike REST API.
- Java 17+ for the Cobalt Strike client.
- The Cobalt Strike client must be launched with these Java module flags:

```text
--add-exports=java.base/sun.net.www.protocol.https=ALL-UNNAMED
--add-exports=java.base/sun.net.www.protocol.http=ALL-UNNAMED
--add-exports=java.base/sun.net.www.http=ALL-UNNAMED
--add-opens=java.base/sun.net.www.protocol.https=ALL-UNNAMED
--add-opens=java.base/sun.net.www.protocol.http=ALL-UNNAMED
--add-opens=java.base/sun.net.www.http=ALL-UNNAMED
```

## Setup

1. Copy `config/local.example.cna` to `config/local.cna`.
2. Set `$bh_url_base`, `$bh_token_id`, and `$bh_token_key` in `config/local.cna`.
3. Load `bloodhound.cna` through **Cobalt Strike > Script Manager > Load**.

## Public Functions

- `bhGET(endpoint)`, `bhPOST(endpoint, body)`, `bhPUT(endpoint, body)`, `bhDELETE(endpoint)`: Signed BloodHound API requests.
- `bhCheckConnection()`: Check the configured BloodHound API endpoint.
- `bhRunCypher(query)`, `bhRunCypherWithProperties(query)`, `bhRunCypherQuiet(query)`, `bhRunCypherWithPropertiesQuiet(query)`: Cypher helpers.
- `bhGetUserInfo(target)`, `bhGetComputerInfo(target)`, `bhGetADCSTemplateInfo(target)`: Node lookup helpers.
- `bhGetUserGroups(target)`, `bhGetComputerGroups(target)`: Group membership helpers.
- `bhAddToOwned(target, selector_name)`: Add an object to the BloodHound Owned asset group tag.

## UI

- Credential rows: BloodHound user info, groups, and Owned marking.
- Beacon rows: user/computer lookup and Owned marking.
- Beacon console selected text: BloodHound lookup and DuckDuckGo search.
- `UserInfoTable` and `UserGroupsTable`: BloodHound lookup actions.
- TrustedSec SA `ldap-query`, `ldapsearch`, `adcs-enum`, and `adcs_enum` tables: BloodHound lookup actions.

## Notes

- The extension signs BloodHound API requests locally in Aggressor Script.
- HTTPS endpoints are supported by the package-local REST transport.
- BloodHound object lookup opens the configured BloodHound web UI when appropriate.
