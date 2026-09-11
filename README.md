# Ansible Atlas

Ansible language support for JetBrains IDEs — playbooks, roles, inventories,
`group_vars`, `host_vars`, and encrypted vault files.

**Install:** search for *Ansible Atlas* in your IDE's plugin marketplace
(Settings → Plugins → Marketplace), or visit the JetBrains Marketplace page.

## What it does

- Module name and argument completion, with inline documentation
- Go to definition for **variables** and **roles**, including nested
  `group_vars` directories and custom roles
- Precedence-aware results: when a variable is defined in several places,
  every definition is listed with its value and source
- Hover over a variable to see its value in every environment
- Variable completion with values and sources shown inline
- Open, edit, and re-encrypt Ansible Vault files without plain text ever
  touching disk

## Requirements

- A commercial JetBrains IDE (the LSP API is not available in Community editions)
- Node.js 24 or later, and `ansible-language-server`
  (`npm install -g @ansible/ansible-language-server`)
- `ansible` for module documentation, `ansible-lint` for validation

The plugin checks for these on first use and tells you what is missing.

## Reporting a problem

Open an issue in this repository. Please include:

- IDE name and version
- Plugin version
- Output of `ansible --version` and `ansible-lint --version`
- A minimal example that reproduces the problem, if you can

## License

Ansible Atlas is proprietary software. See [EULA.md](EULA.md).

Ansible is a trademark of Red Hat, Inc. This project is not affiliated with,
endorsed by, or sponsored by Red Hat, Inc. or JetBrains.
