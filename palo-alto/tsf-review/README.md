# tsf-review

An offline, single-file web app for reviewing logs in a Palo Alto Networks tech support file (TSF).

Open `index.html` in a current browser (Chrome, Edge, Firefox or Safari) and drop a TSF `.tgz` onto the page. The archive is decompressed and searched inside the browser tab. The page has no dependencies, and its Content-Security-Policy blocks all network requests, so the TSF never leaves the machine.

## Getting a TSF

- Web UI: **Device › Support › Generate Tech Support File**, then download it.
- CLI: `scp export tech-support to <user>@<host>:<path>`

## What it does

| View | Purpose |
| --- | --- |
| Overview | Device details parsed from `show system info` (model, PAN-OS, serial, uptime, content versions) plus HA mode and local/peer state. Also shows a triage summary, the largest logs and the files with the most findings. |
| Triage | Runs the checks below across every log. Hits are grouped by check, can be filtered by file or text, and open the source line in the viewer. |
| Timeline | Plots timestamped hits as a stacked severity histogram. Click a bar to zoom in, or set a from/to range and toggle checks on and off. |
| Files | Browses the full archive tree. The viewer handles multi-million-line logs and has a line filter (plain or regex), jump-to-line and download. |
| Search | Searches every file in the archive, with optional regex, case matching and path filtering. |
| Notes | Lists pinned lines with your notes. Export writes Markdown you can paste into a ticket. Pins are kept in browser storage per archive. |

Nested archives such as the dataplane bundle, and rotated `.gz` logs, are opened automatically.

## Triage checks

| Check | Area | Severity |
| --- | --- | --- |
| Core dumps / crash files | Crashes | high |
| Process exit / restart / signal | Processes | high |
| Memory exhaustion | Resources | high |
| Disk space | Resources | high |
| Dataplane health / resource pressure | Dataplane | high |
| Commit / validation failures | Config | high |
| HA state changes / failures | High availability | high |
| IKE / IPsec / GlobalProtect failures | VPN | med |
| Routing adjacency changes | Routing | med |
| Link / interface flaps | Interfaces | med |
| Certificate or license problems | Certificates / licenses | med |
| Panorama / management connectivity | Management | med |
| User-ID / authentication errors | User-ID | low |
| Content / software update failures | Management | low |
| Other errors (CRITICAL / FATAL / ERROR) | Generic | low |

Each check is a path pattern (which files it applies to) plus a line regex. They live in the `RULES` array near the top of the script and are meant to be edited. If a check is noisy or misses something in your logs, adjust its regex there.

## Limitations

- The patterns are heuristics for PAN-OS log wording and will need tuning as you see real files. Log formats vary between PAN-OS versions.
- Timestamps are shown exactly as the device wrote them, with no time zone conversion. Syslog-style lines without a year take it from the file's modification time.
- Each check keeps its first 5,000 hits for display. Counts still include everything.
- The whole archive is held in memory. Very large TSFs (several GB uncompressed) may hit browser memory limits. Files over 400 MB are skipped by triage and search but can still be downloaded.
- Requires a browser with `DecompressionStream`, which every current major browser has.

## Handling TSFs

A tech support file contains the full device configuration, hostnames, addresses and log data. Don't commit one to this repo. `.gitignore` blocks `*.tgz` and `*.tar.gz`, so a TSF is harder to add by accident.
