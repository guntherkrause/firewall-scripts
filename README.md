# firewall-scripts

Small, self-contained tools for firewall administration and auditing. Each tool lives in its own folder with its own README, runs locally, and has no install step.

| Tool | Platform | What it does |
| --- | --- | --- |
| [rulebase-audit](palo-alto/rulebase-audit/) | Palo Alto PAN-OS / Panorama | Reads a config export in the browser, reports security-rule best-practice findings, and generates the `set` commands to fix them. |
| [tsf-review](palo-alto/tsf-review/) | Palo Alto PAN-OS | Opens a tech support file offline in the browser, flags crashes, resource pressure, commit, HA, VPN and routing problems, and lets you browse, search and annotate every log. |

## Conventions

- One folder per vendor, one subfolder per tool.
- Tools run on the user's machine. Configs are never uploaded anywhere.
- Do not commit real configuration exports or tech support files. `.gitignore` blocks `*.xml` outside `samples/`, plus `*.tgz` and `*.tar.gz`, to make that harder to do by accident. Sanitize anything you add to `samples/`.
- Commands a tool generates are suggestions. Review them and test in a lab or with a validated commit before use.

## License

MIT. See [LICENSE](LICENSE). This project is not affiliated with Palo Alto Networks; product names are trademarks of their owners.
