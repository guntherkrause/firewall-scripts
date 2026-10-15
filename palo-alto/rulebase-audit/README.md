# PAN-OS Rulebase Audit

A single HTML page that reads a PAN-OS XML configuration export and reports security-rule best-practice findings. Every finding includes the `set`, `delete` or `move` commands to fix it, and a consolidated script collects them in rule order. Everything runs in the browser and the config is never uploaded.

## Use

1. Open `index.html` in a browser. There is no build step, server or dependency. The font loads from Google Fonts when online and falls back to system fonts offline.
2. Drop a config export onto the page or paste the XML. The page opens with an example config so you can see the output first.
3. Filter by severity, open a rule to see its findings, and copy commands per finding or as one script.

Supported inputs are firewall running or candidate configs (single or multi-vsys) and Panorama exports (shared and device-group pre/post rulebases). Export one from Device > Setup > Operations (Export named configuration snapshot) or with `scp export configuration`.

Names for the profile group, log forwarding profile and cleanup rule used in generated commands are set in the settings panel, so they match the objects in your environment.

## Checks

| ID | Severity | Check |
| --- | --- | --- |
| R01 | Critical | Allow rule matches any application |
| R02 | Critical | Allow rule is any source, any destination, any application |
| R03 | High | Allow rule uses service any with specific applications |
| R04 | High | Allow rule without security profiles |
| R05 | Medium | Allow rule missing one or more profile types |
| R06 | High | Logging at session end is disabled |
| R07 | Medium | No log forwarding profile |
| R08 | Low | Log at session start enabled |
| R09 | Medium | Zone set to any |
| R10 | Medium | Allow rule from any source address to any destination |
| R11 | Medium | Negated source or destination |
| R12 | Low | Server response inspection disabled (DSRI) |
| R13 | Low | Rule is disabled |
| R14 | Low | Missing description or tags |
| R15 | Low | Placeholder-style rule name |
| R16 | High | Rule shadowed by an earlier rule with a different action |
| R17 | Medium | Rule redundant with an earlier rule (same action) |
| R18 | Medium | Deny rule without logging |
| R19 | Low | Allow rule without User-ID match |
| R20 | Medium | Audit comment is not a bare ITSM ticket number (INC / CRQ / WO / TSK) |
| R21 | Medium | Required ownership / audit tags missing or malformed |
| R22 | Low | Rule review is stale |
| R23 | Low | Description contains a ticket number instead of the rule's purpose |
| B01 | Medium | No explicit logged cleanup (deny all) rule at the end of the rulebase |
| B02 | Medium | Default interzone rule does not log |
| B03 | Low | Default intrazone rule allows without profiles or logging |
| B04 | Low | Export carries no audit comments; ticket compliance not verified |

## Organization-specific checks

R20 to R23 encode one team's change-control convention:

- The audit comment holds an ITSM ticket number (INC, CRQ, WO or TSK) and nothing else.
- The description states what the rule is intended to do and carries no ticket number.
- Every rule carries `team:<email>`, `month:<Month>` and `year:<YYYY>` tags, and the month and year are used to flag rules overdue for review.

The ticket pattern, review window and team address are editable in the settings panel, and both the ticket and tag enforcement can be switched off.

## Limitations

- Shadow and redundancy detection compares object names. Address and service groups are not expanded, so confirm results with the firewall's own shadow-rule check.
- Audit comments are kept in the audit comment archive, not the running config. R20 can only evaluate pending comments in a candidate config. B04 notes when an export has none.
- Generated commands are suggestions. Review them before applying.
