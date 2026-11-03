# Raven Write Recipe

Read this before a skill creates or updates a complete document in Raven. The
shared-vault writer skills use this recipe so identity, retries, shell errors,
and secret handling stay consistent.

## Resolve IDs and target vault

- Use the `data.id` returned by Raven for the repository and document. Do not
  derive IDs from `data.file` or filenames.
- For a `ref` field, pass the canonical ID as a plain value, such as
  `repository=repositories/tracs_flow`. Do **not** wrap a `--field` ref value
  in `[[...]]`. Use `[[canonical-id]]` in Markdown body references and RQL
  expressions.
- Select the vault explicitly. If supplying both `--vault` and `--vault-path`,
  verify they identify the same vault; `--vault-path` takes precedence.
  Never change active/default vault routing as a side effect of a document
  write.

## Preferred: idempotent full-body write

`rvn write` is Raven's idempotent, full-body write. It creates an object if it
does not exist and updates it in place if its type/title identity already
exists. `--content-file` replaces the complete body, so repeated writes
converge instead of appending duplicate content.

For a complete code map, stage the body as UTF-8 Markdown and use a short,
single-line CLI command:

```powershell
rvn write repo-doc "<title>" --field kind=code-map --field "repository=<canonical-repository-id>" --content-file "C:\Users\pauhil\AppData\Local\Temp\opencode\<map-file>.md" --json --vault "<vault>" --vault-path "<vault-path>"
```

The example's placeholders are not literal values. The repository value is the
canonical `data.id` returned by Raven. The command's type/title determine the
object identity; `data.id` in the write response is the ID to use afterward.
Before replacing an existing object, read it and confirm the title resolves to
the intended document. Preserve any content that should remain in the full
replacement body.

Prefer `--content-file` to `--content` for a complete map or other substantial
document. Do not place a long Markdown body in an inline PowerShell payload.

## Windows shell-scanner recovery

The harness may reject long or complex inline PowerShell before `rvn` is
started, with an error such as:

```text
Portable shell scanner cannot analyze command: invalid-structure
```

That is a **shell-scanner rejection**, not an `rvn write` failure: Raven never
ran. Do not retry the same inline payload with more quoting or escaping. Write
the body to a temporary file using an available file-writing tool, preferably
under the approved directory
`C:\Users\pauhil\AppData\Local\Temp\opencode`, then run the short
ASCII-only `rvn write ... --content-file ...` command. Do not stage the body in
the repository. Remove the temporary file after a successful write; keep it
only if needed to diagnose a failed Raven operation.

If the short CLI command itself returns an `rvn` error, report that separately
and follow the error details; do not confuse it with the scanner message.

## Fallback: build the document section by section

If the harness cannot supply a complete body file, use Raven's native section
operations:

1. Create the approved typed object with `rvn write` and the required fields.
   Keep the returned canonical object ID.
2. Read the object and inspect its existing headings before creating anything.
3. Create each missing heading with `rvn section create <document-id> "<title>"
   --level <n> --json`. Use the canonical section ID returned by Raven.
4. Append text with `rvn add "<text>" --to <canonical-section-id> --json`.

`rvn add` is append-only and cannot create Markdown headings. `rvn section
create` creates a heading but no body. Before retrying either operation, read
the object/section again: blindly retrying `section create` can duplicate or
collide with a heading, and retrying `add` can duplicate appended text.

## Secrets and credentials

- A code map may document configuration key names, configuration file paths,
  and where values are loaded. Never copy credential values, tokens, private
  keys, passwords, or secret-bearing environment values into Raven.
- If tracked credentials are discovered, omit the values and flag the finding
  separately using only the file path and credential category/key name. Do not
  print the secret in the map, shell output, or user-facing report.
