# claude-obsidian

## What This Is

This is part of the Swift Tech Co. working stack for product and engineering. It gathers the claude-obsidian notes, examples and fixes that keep delivery fast, and doubles as the reference map we hand to whoever picks up this thread next.

## Images

[![claude-obsidian](images/preview.png)](https://github.com/Swift-Tech-Co/claude-obsidian)

## From source to living knowledge

Most AI note workflows stop after saving text. claude-obsidian is organized
around a repeatable loop: retain the source, ground the claims, connect the
knowledge, then put it back to work.

[![The claude-obsidian compounding knowledge loop](assets/diagrams/knowledge-loop.svg)](https://github.com/Swift-Tech-Co/claude-obsidian)

- **Capture with context.** Bring local sources through a visible inbox and
  preserve immutable, content-addressed copies before synthesis.
- **Ground every important claim.** Source and claim ledgers retain authority,
  freshness, support, contradiction, confidence, and review state.
- **Connect what you learn.** Build linked pages, indexes, Maps of Content,
  methodology-aware structures, and Obsidian Canvas views.
- **Use the vault again.** Query, research, retrieve, lint, and fold what is
  already known instead of starting every conversation from zero.

## See the vault

The output is meant to remain useful with or without an agent: plain Markdown
for portability, Obsidian for navigation and visual exploration.

<p align="center">
  <img src="assets/screenshots/graph-view.png" alt="Example claude-obsidian vault in Obsidian Graph view" width="49%">
  <img src="assets/screenshots/wiki-map-view.png" alt="Example claude-obsidian knowledge map in Obsidian Canvas" width="49%">
</p>

<p align="center">
  <sub>Linked knowledge in Graph view · A visual knowledge map in Obsidian Canvas</sub>
</p>

## Why it feels different

- **Local by default.** The vault is user-owned and works as ordinary files.
  Network egress is a separate, explicit decision.
- **Sources survive the summary.** Notes point back to durable source evidence;
  unsupported and contradictory claims remain visible.
- **Knowledge compounds deliberately.** Ingestion, querying, linting, retrieval,
  research, and rollups share one provenance-aware model.
- **Parallel agents cannot race the vault.** Workers return drafts. One
  orchestrator inspects and applies one recoverable transaction.
- **Capabilities are stated honestly.** Optional tools are detected, maturity
  is declared, and missing adapters degrade clearly instead of being simulated.

This is not an automatic transcript recorder, a cloud sync service, a factual
oracle, or a substitute for backups and source control.

## Quick start

The safest first run uses a source checkout and a separate user vault. Every
mutating setup command previews its exact operation before it can apply.

### 1. Get the product

```bash
git clone https://github.com/Swift-Tech-Co/claude-obsidian
cd claude-obsidian
```

The checkout contains the product. It is not your knowledge vault.

### 2. Initialize a separate vault

```bash
export GENERATED_AT="$(date -u +%Y-%m-%dT%H:%M:%SZ)"
export OPERATION_ID="init-reviewed"

python3 scripts/claude-obsidian.py init "$HOME/Documents/MyKnowledgeVault" \
  --generated-at "$GENERATED_AT" --operation-id "$OPERATION_ID"
```

Review the JSON plan and copy its `approved_plan_sha256`, then apply that exact
operation:

```bash
python3 scripts/claude-obsidian.py init "$HOME/Documents/MyKnowledgeVault" \
  --generated-at "$GENERATED_AT" --operation-id "$OPERATION_ID" \
  --approved-plan-sha256 "<sha256-from-the-plan>" --apply
```

For an existing Obsidian vault, use the non-destructive `adopt` workflow
described in the [installation guide](docs/install-guide.md#adopt-an-existing-vault).

### 3. Start from the vault

Open the new directory in Obsidian, then run Claude Code from that directory
with the local plugin:

```bash
cd "$HOME/Documents/MyKnowledgeVault"
claude --plugin-dir /absolute/path/to/claude-obsidian
```

Start with:

```text
/claude-obsidian:wiki
```

Then place a source in `inbox/` and invoke
`/claude-obsidian:wiki-ingest`. Save an answer explicitly with
`/claude-obsidian:save`; ask the vault with `/claude-obsidian:wiki-query`.

For Codex, OpenCode, Gemini, or ZCode, preview and then apply the portable
skill links from the product checkout:

```bash
bash scripts/setup-multi-agent.sh --host codex
bash scripts/setup-multi-agent.sh --host codex --apply
```

Cursor and Windsurf use workspace-local skill discovery. Marketplace setup,
every supported host, vault adoption, upgrades, and uninstall steps are covered
in the [full installation guide](docs/install-guide.md).

## 15 skills, one system

The skills are small enough to invoke directly and coordinated enough to share
the same evidence, vault-selection, and mutation rules.

### Build and use the wiki

| Skill | What it does |
|---|---|
| `wiki` | Initializes or adopts a vault, diagnoses readiness, and routes work |
| `save` | Saves one scoped answer or insightnever an automatic transcript |
| `wiki-ingest` | Turns captured sources into linked pages and provenance records |
| `wiki-query` | Answers read-only from relevant vault evidence |
| `wiki-lint` | Reports dead links, orphans, metadata gaps, stale indexes, and empty sections |

### Extend the workflow

| Skill | What it adds |
|---|---|
| `autoresearch` | Bounded web research with explicit egress and a separate canonical merge |
| `canvas` | Wiki-scoped Obsidian Canvas creation and maintenance |
| `defuddle` | Clean, readable web content before ingestion |
| `wiki-fold` | Extractive, traceable rollups of the operation log |
| `wiki-mode` | Generic, LYT, PARA, or Zettelkasten filing conventions |
| `wiki-retrieve` | Contextual prefixes, BM25, and optional cosine reranking |
| `wiki-cli` | Obsidian CLI reads and search with transaction-safe writes |

### Reference skills

| Skill | What it provides |
|---|---|
| `obsidian-markdown` | Correct Obsidian Flavored Markdown, links, embeds, and callouts |
| `obsidian-bases` | Native `.base` tables, cards, filters, formulas, and summaries |
| `think` | A structured observe, listen, connect, create, and grow review loop |

Claude Code exposes namespaced invocations such as
`/claude-obsidian:wiki-lint`; other hosts use their native Agent Skills
invocation. Trigger phrases and exact contracts live in each
`skills/<name>/SKILL.md`.

## Trust is part of the architecture

[![The claude-obsidian product and vault trust boundary](assets/diagrams/product-vault-boundary.svg)](https://github.com/Swift-Tech-Co/claude-obsidian)

The product never treats a source checkout, plugin cache, or contributor state
as the default vault. A vault is selected explicitly, through
`CLAUDE_OBSIDIAN_VAULT`, by the nearest `.claude-obsidian.json`, or by one
unambiguous initialized ancestor. If selection is uncertain, the command exits
without writing.

One logical knowledge operation is one recoverable transaction:

1. Read every target and record its expected SHA-256.
2. Let parallel workers return drafts and evidence only.
3. Merge the complete change into one operation bundle.
4. Inspect the bundle, then apply it once.
5. Report the operation ID and exact changed paths.

The core holds one process-lifetime vault lock, journals backups, uses atomic
replacement, and restores the prior state if an apply cannot finish. A changed
target is a conflict, never a silent overwrite. Git checkpoints, destructive
repairs, network egress, and canonical research merges remain explicit
operations.

Read the [transaction contract](skills/wiki/references/operation-transactions.md),
[provenance contract](skills/wiki/references/provenance.md), and
[Compound Vault architecture](docs/compound-vault-guide.md) for the
machine-facing detail.

## Honest capability boundaries

| Input or capability | Current support |
|---|---|
| Local filesystem sources | Implemented bounded, content-addressed byte capture |
| Images | Metadata, hash, size, and bounded dimensions when available |
| PDF and EPUB | Metadata, hash, and size; no built-in semantic extraction |
| URL and YouTube | Validated consent plans; a configured external runner is required |
| OCR | Local-file consent plan; a configured external runner is required |
| BM25 retrieval | Local and deterministic |
| Contextual prefixes or remote models | Optional and gated by explicit egress consent |
| Obsidian CLI | Optional for reads/search; filesystem transport remains available |

High-risk accepted claims require two independent sources. Unsupported or
contradictory evidence stays visible, and a grounded refusal is preferred over
an invented citation. Model-based retrieval falls back to deterministic BM25
when the embedding or reranking stage cannot be trusted.

## Shape the vault to the way you think

`wiki-mode` can route new notes using four methodologies without bulk-moving
existing knowledge:

| Mode | Filing principle |
|---|---|
| Generic | Sources, concepts, entities, and sessions |
| LYT | Maps of Content and linked atomic notes |
| PARA | Projects, Areas, Resources, and Archives |
| Zettelkasten | Stable identifiers, atomic notes, and dense links |

Generic is the default when no mode is configured. Switching modes changes how
new notes are routed; it does not silently reorganize old ones. See the
[methodology modes guide](docs/methodology-modes-guide.md).

## Operator reference

<details>
<summary><strong>Portable CLI</strong></summary>

The wrapper is `python3 scripts/claude-obsidian.py`.

| Command | Effect |
|---|---|
| `doctor --vault PATH` | Show vault selection and readiness |
| `init PATH [--approved-plan-sha256 HASH --apply]` | Plan or create a separate vault |
| `adopt PATH [--approved-plan-sha256 HASH --apply]` | Plan or adopt an existing Obsidian vault |
| `migrate --vault PATH [--approved-plan-sha256 HASH --apply]` | Add v1 ledgers and configuration without rewriting legacy data |
| `transaction inspect BUNDLE --vault PATH` | Validate a write bundle without mutation |
| `transaction apply BUNDLE --vault PATH --approved-plan-sha256 HASH` | Apply one inspected, recoverable operation |
| `transaction recover --vault PATH [--force-stale-lock]` | Restore an interrupted operation |
| `lint --vault PATH [--as-of YYYY-MM-DD] [--exclude GLOB]...` | Emit findings deterministic for the declared UTC date |
| `contracts --verify --vault PATH` | Execute capability readiness contracts |
| `capture plan --vault PATH [SOURCE ...]` | Run a local capture preflight without writes |
| `capture apply --vault PATH [SOURCE ...]` | Plan or create immutable content-addressed copies |
| `checkpoint OPERATION_ID --vault PATH` | Explicitly commit one completed operation |
| `package validate` | Check skills, hooks, manifests, and documentation coherence |
| `release build --output FILE.zip` | Build and self-audit a deterministic public artifact |
| `release audit FILE.zip` | Audit an artifact without extracting or publishing it |

High-level mutating planners emit `approved_plan_sha256`. Pin
`--generated-at` and `--operation-id`, review the JSON operation, and pass that
exact hash with `--apply`. Filesystem or generated-bundle drift fails before a
vault write.

</details>

<details>
<summary><strong>Repository and vault layout</strong></summary>

```text
product repository/                user vault/
├── claude_obsidian/               ├── .gitignore
├── skills/                        ├── .claude-obsidian.json
├── hooks/                         ├── inbox/
├── scripts/                       ├── .raw/
├── templates/vault/               ├── wiki/
├── config/                        ├── .obsidian/
├── assets/                        └── .vault-meta/   # ignored runtime state
└── tests/
```

Public artifacts contain product code, deterministic templates, and reviewed
README assets. They reject contributor hot/log state, root raw sources, runtime
metadata, private paths, recognizable personal email addresses, secrets,
symlinks, unsafe archive entries, and unreviewed binaries.

The private development checkout deliberately has no marketplace catalog. The
release builder injects the reviewed catalog only into the distribution-clean
artifact. A public default branch must be populated from that audited tree,
never by pushing contributor-vault state.

</details>

<details>
<summary><strong>Upgrade, rollback, and uninstall</strong></summary>

Upgrade the product independently from the vault. For an older vault, first
preview the additive, idempotent migration:

```bash
python3 scripts/claude-obsidian.py migrate --vault /path/to/vault \
  --generated-at "$GENERATED_AT" --operation-id migrate-reviewed
```

Review its hash and rerun with `--approved-plan-sha256 HASH --apply`.
Migration preserves the legacy raw manifest byte-for-byte and does not infer
claims from prose.

After an interrupted operation, run:

```bash
python3 scripts/claude-obsidian.py transaction recover --vault /path/to/vault
```

Removing the plugin or host links never removes the vault. Delete only the
integration you installed; user notes, sources, ledgers, and Obsidian settings
remain yours.

</details>

## Requirements

- Python 3.11 or newer for the portable core
- Obsidian for the visual vault experience; plain Markdown remains usable
  without it
- Bash for setup, optional extensions, and shell test suites
- Git only for development, releases, or an explicit knowledge checkpoint
- A current Claude Code release for the Claude Code plugin path: the
  exec-form `args` command hook and the `compact` `SessionStart` matcher used
  by `hooks/hooks.json` need it (see the
  Claude Code hooks contract). Other
  supported hosts and the portable CLI have no Claude Code version
  dependency.

CI exercises Linux and macOS, plus a native-Windows smoke job for the portable
surface. On native Windows (including Git Bash), read-only inspection and
dry-run commands work; vault writes require WSL and fail closed with an
`UNSUPPORTED_PLATFORM` error otherwise. Approval hashes bind to the reviewing
environment, so review inside WSL when the apply will happen there. Platform
details, the support matrix, and evidence-bounded WSL hang troubleshooting
live in the
[Windows and WSL guide](docs/windows-wsl.md). The bash setup scripts and shell
test suites remain POSIX-only. Optional tools such as Obsidian CLI, Ollama,
and defuddle are capability-detected and affect only their dependent workflow.

## Development and release

```bash
make test
```

The test target runs every hermetic Python and shell suite, product and
capability contracts, skill and hook validation, manifest checks, and package
boundaries. CI repeats the suite on supported Linux and macOS/Python
combinations and verifies a byte-reproducible release build.

Build and audit locally without publishing:

```bash
python3 scripts/claude-obsidian.py release build --output dist/claude-obsidian.zip
python3 scripts/claude-obsidian.py release audit dist/claude-obsidian.zip
```

No command pushes, tags, publishes, opens issues, or creates releases
automatically. See [CONTRIBUTING.md](CONTRIBUTING.md),
[SECURITY.md](SECURITY.md), and [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

---

## Swift Tech Co.

Built and maintained by Swift Tech Co.

AI Automation | FinTech Platforms | Custom Software and Mobile Apps | SaaS Platforms | Cloud Infrastructure | Cybersecurity | SEO and Marketing | API Integration

**Join our community on Telegram:** [t.me/+Qt_eFvkb2rliMzNh](https://t.me/+Qt_eFvkb2rliMzNh)

Home | Services | Careers | Referral Program | Influencers | About | Contact

**Connect**

- Website: https://github.com/Swift-Tech-Co/claude-obsidian
- LinkedIn: [linkedin.com/in/stephenoladoke](https://www.linkedin.com/in/stephenoladoke/)
- LinkedIn (Company): [linkedin.com/company/swift-tech-co](https://www.linkedin.com/company/swift-tech-co)
- X: [@MrOladoke](https://x.com/MrOladoke)
- Instagram: [@mr.oladoke](https://www.instagram.com/mr.oladoke/)
- Organization: [github.com/Swift-Tech-Co](https://github.com/Swift-Tech-Co)

## Sources

Compiled and maintained from upstream [AgriciDaniel/claude-obsidian](https://github.com/AgriciDaniel/claude-obsidian) under permissive open-source license.

---

Maintained by [Swift Tech Co.](https://github.com/Swift-Tech-Co) - [Telegram](https://t.me/+Qt_eFvkb2rliMzNh)

## Sources

Compiled and maintained from upstream [AgriciDaniel/claude-obsidian](https://github.com/AgriciDaniel/claude-obsidian) under permissive open-source license.

---

Maintained by [Swift Tech Co.](https://github.com/Swift-Tech-Co) - [Telegram](https://t.me/+Qt_eFvkb2rliMzNh)

## Sources

Compiled and maintained from upstream [AgriciDaniel/claude-obsidian](https://github.com/AgriciDaniel/claude-obsidian) under permissive open-source license.

---

Maintained by [Swift Tech Co.](https://github.com/Swift-Tech-Co) - [Telegram](https://t.me/+Qt_eFvkb2rliMzNh)

## Sources

Compiled and maintained from upstream [AgriciDaniel/claude-obsidian](https://github.com/AgriciDaniel/claude-obsidian) under permissive open-source license.

---

Maintained by [Swift Tech Co.](https://github.com/Swift-Tech-Co) - [Telegram](https://t.me/+Qt_eFvkb2rliMzNh)