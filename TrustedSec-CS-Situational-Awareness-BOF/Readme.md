# TrustedSec SA: custom tables and context menus

This version of [TrustedSec's SA.cna](https://github.com/trustedsec/CS-Situational-Awareness-BOF/blob/master/SA/SA.cna) adds Cobalt Strike custom result tables and right-click actions while retaining the original commands.

## Quick start

Use Cobalt Strike 4.13 or later with `openUserDefinedBrowser` support. Build or obtain the [upstream BOFs](https://github.com/trustedsec/CS-Situational-Awareness-BOF), replace their `SA/SA.cna` with [this script](SA/SA.cna), then load it through Script Manager. Keep the compiled objects beside it at `SA/<command>/<command>.<arch>.o`; this example does not include them. Load only one copy of `SA.cna`.

## Changes from upstream

- **LDAP table:** `ldapsearch` still logs its output to the Beacon console and also opens an **LDAP Query** tab, with returned attributes as columns.
- **Right-click context menu:** select LDAP rows containing a `cn` attribute, then choose **Query CN** to run a follow-up query through the original Beacon. Results open in a named tab, reducing manual copying and command entry.
- **ADCS table:** `adcs_enum [domain]` keeps console output and displays CA/template details in an **ADCS Enum** tab, including flags, validity, certificates, and permissions. Its parser buffers partial output lines until completion.
- **Reusable table menus:** the `ldap-query` and `adcs-enum` table IDs expose selected rows and callback context to other scripts. Loading and configuring the [BloodHound example](../bloodhound-example/README.md) adds LDAP object and ADCS template lookup menus; these are separate from SA's built-in **Query CN** action.

Only `ldapsearch` and `adcs_enum` gain custom tables; other commands retain their usual console output.