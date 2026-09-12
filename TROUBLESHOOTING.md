# Troubleshooting

Most problems come from the environment Ansible Atlas depends on, not from the plugin itself.
Work through this page before opening an issue — it will usually be faster.

## Nothing happens when I open an Ansible file

**Check your IDE.** Ansible Atlas needs a commercial JetBrains IDE. The LSP API it builds on is
not available in IntelliJ IDEA Community Edition or PyCharm Community Edition. There is no
workaround; the API simply is not present in those builds.

**Check that the file is recognised as Ansible.** Ansible Atlas deliberately does not react to
every YAML file. It activates for:

- files under `roles/`, `tasks/`, `handlers/`, `defaults/`, `vars/`, `meta/`, `playbooks/`
- files under any `group_vars/` or `host_vars/` directory, at any depth
- `site.yml`, `main.yml`, `requirements.yml` **at the project root**
- `ansible.cfg`

A file named `main.yml` deep inside an unrelated project is left alone on purpose.

## "ansible-language-server was not found"

Install it, then restart the IDE:

```
npm install -g @ansible/ansible-language-server
```

It needs Node.js 24 or later. Check with `node --version`.

If it is installed but still not found, your IDE may not see the same `PATH` as your terminal.
Set the full path explicitly in **Settings | Tools | Ansible**. Find it with:

```
which ansible-language-server
```

## "Ansible-lint is not available"

Validation needs `ansible-lint`:

```
pipx install ansible-lint
```

Module documentation and argument completion need `ansible`:

```
pipx install ansible-core
```

Both are optional. Without them the rest of the plugin still works.

## Diagnostics do not appear

`ansible-lint` reports paths relative to where it runs. When a project lives under your home
directory it can abbreviate them to `~/...`, which the IDE cannot resolve. Ansible Atlas expands
these, so this should not happen. If diagnostics are still missing, check that `ansible-lint`
runs successfully from your terminal inside the project directory.

## Cannot open an encrypted vault file

**"No vault password file is configured"** — set one in **Settings | Tools | Ansible**, or add
`vault_password_file` to your `ansible.cfg`.

**"Refused to use the vault password file from ansible.cfg"** — the file is executable.
`ansible-vault` runs executable password files, so a repository could use that to run code on
your machine. Ansible Atlas refuses paths that come from a project's `ansible.cfg` and are
executable. If you trust the file, set its path explicitly in **Settings | Tools | Ansible**.

**"The vault-ids default,default are available to encrypt"** — you should not see this; Ansible
Atlas passes an explicit vault id. If it appears, please open an issue with your `ansible.cfg`.

## Saving a decrypted file does nothing

Pressing Cmd+S / Ctrl+S does not write back to the encrypted file. This is deliberate.
Right-click in the editor and choose **Save to Ansible Vault**.

An implicit write-back on every save is a way to lose secrets: if encryption fails halfway, the
original file is at risk. Ansible Atlas verifies that the output is vault-encrypted before it
touches the original, and asks you to say when you mean it.

## A variable is not found, or the wrong one is shown

Ansible Atlas resolves variables from:

`set_fact` → play and task `vars:` → `roles/<role>/vars/` → `host_vars/` → `group_vars/<group>/`
→ `group_vars/all` → `roles/<role>/defaults/`

It also follows `vars_files:` and `include_vars:`.

It does **not** resolve variables that only exist at runtime: facts gathered from hosts,
`--extra-vars` passed on the command line, or values produced by `register`. Those cannot be
known from the files alone.

When several definitions share the highest precedence — for example the same group in two
inventories — Ansible Atlas lists them all and says so, because which one applies depends on
the inventory you run with.

## It feels slow in a large repository

Ansible Atlas scans the project when you navigate to or hover over a variable. Directories like
`.git`, `node_modules`, `build`, `out`, `target` and `venv` are skipped. If your repository is
large enough that this is noticeable, please open an issue and say roughly how many YAML files
it contains — that is the information needed to fix it properly.

## Something else

Open an issue: https://github.com/no-clown/ansible-atlas/issues

Useful details: your IDE and version, your OS, the output of `node --version`,
`ansible --version`, `ansible-lint --version`, and what you expected to happen.
