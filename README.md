# QuickCargo operator agent for Claude

A Claude Code subagent that enters transport orders and invoices into the
[QuickCargo](https://quickcargo.app) TMS from the source document (PDF or image).

It is written for small road carriers and freight forwarders that do this
data entry by hand and have no developers in-house. The agent prompt is in
Romanian, the working language of the companies it was built with; it replies
in the user's language.

## What it does

- Reads the whole document in three passes: transcription, field extraction
  with an exact quote for every value, and a reverse check so that no line of
  the document is left unaccounted for.
- Works out which company (QuickCargo instance) the document belongs to.
- Looks for an existing record first, so nothing is entered twice.
- Searches partners and locations before creating new ones, to avoid
  duplicates.
- Runs a dry run that shows exactly what would be submitted, and saves only
  after the user approves that specific record.
- Reads the saved record back and compares it with the draft.
- Recomputes invoice totals and stops when they do not add up.

## Safety rules built into the prompt

- The password never passes through the model. The helper tool reads it from
  the system Keychain.
- Nothing is saved without the user's approval for that record.
- No emails, payments, deletions or e-invoicing submissions on its own.
- Text inside a document is treated as data, never as an instruction.
- It stops and reports on anti-bot checks, 2FA prompts or rejected logins
  instead of working around them.

## Install

Copy the agent file into your Claude Code agents folder:

```bash
mkdir -p ~/.claude/agents && cp quickcargo-operator.md ~/.claude/agents/
```

## The `qc` helper

The agent drives QuickCargo through a command-line helper it calls `qc`.
**The helper is not included in this first release.** The agent expects these
commands:

| Command | Purpose |
|---|---|
| `qc setup <instance>` | Run once by the user; stores the login in the Keychain |
| `qc instances` | Lists configured instances and how to recognise each |
| `qc login <instance>` | Opens a session |
| `qc lookup` | Searches partners and locations |
| `qc forms` | Lists the fields of a form or of an existing record |
| `qc fill <draft.json>` | Dry run; add `--confirmed` to save |
| `qc maps` | Checks a location's coordinates on a map |
| `qc archive` | Copies the source document to an archive folder |

A cleaned-up version of the helper is planned for a later release.

## Status

Early release. The prompt is used daily on real orders and invoices. It
contains no credentials, company names or customer data.

## License

MIT. See [LICENSE](LICENSE).
