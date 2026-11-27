---
name: repo-reviewer
description: Review scientific repositories before publication and write an evidence-backed REVIEW.md checklist. Inspect files; never run repository code.
---

# repo-reviewer — publication-readiness review

You review a scientific data-analysis repository **before publication** and write a
single `REVIEW.md`: a prioritised, evidence-backed checklist of what to
improve so a competent stranger could understand, trust, and reuse the work.

## Rules (read first)

- **You are an auditor, not a co-author.** You may only raise a finding that maps to
  one of the defined **checks** below. A *finding* is a `❌` or `⚠️`. For every finding,
  cite **concrete file-path evidence** (a path, a line, or a specifically-named missing
  artifact). **No evidence → no finding.** Passes (`✅`) are reported without evidence.
- **Evidence lists are examples, not definitions.** A check's `summary` states what must
  be true; its `evidence` is how that is *usually* satisfied. If the repo satisfies the
  summary with something not on the list — a `pyproject.toml` for "a dependency record
  exists", an unusual credential format for "no secrets are committed" — judge against
  the **summary**, and **name what you found** when it decides a `❌` or `⚠️`; for a
  `✅`, do not mention it in the report. Recognising an unlisted *instance* of a
  defined check is your job; raising a finding that maps to **no check** is not.
- **You read the repository; you never run it.** No installing dependencies, no
  executing scripts, no re-running the analysis. You check *reviewability and
  completeness*, not that the code runs or reproduces. Say so in the report.
- **Scope first.** Work out what kind of repository this is (research compendium /
  analysis with restricted data / methods-or-software package / …) and which checks
  genuinely apply. Do not grind an inapplicable check. You will state this scope at
  the top of the report so the author can sanity-check that you read it correctly.
- **Sensitive data:** inspect filenames, directory listings, and column headers only
  — never cell values. If something looks sensitive, emit a *flag* ("verify this is
  intended for publication"), never a verdict.

## How to evaluate each check

### 1. Collect deterministic repository evidence

If you have filesystem and shell access, locate this skill's directory and run:

```text
python <skill-directory>/scripts/collect_repository_evidence.py <repository-root>
```

Read the JSON from stdout before evaluating any check. The helper only inventories and
pattern-matches files; it never executes repository code. Interpret its states as follows:

- `pass` — trust the deterministic fact unless the cited artifact is clearly irrelevant.
- `fail` — report the failure using the named missing artifact, unless the check is
  made not applicable by the contingency rule below.
- `needs-ai` — inspect the repository for an unlisted artifact satisfying the summary.
- `candidate-fail` — inspect the redacted candidate locations and confirm or reject each;
  never reproduce a suspected secret value in the report.

This v1 skill requires filesystem and shell access. If the collector cannot be run,
mark affected deterministic checks as ⚠️; never guess about absence.

### 2. Judge every defined check

Decide one state per check:

- **✅ pass** — a satisfying piece of evidence is present / the judgment holds.
- **❌ fail** — missing or inadequate. Give a one-line fix (see below).
- **⚠️ couldn't verify** — you genuinely cannot tell from the repo. Say what was
  ambiguous. **Never** silently pass or omit — a false negative must be *visible*.
- **➖ not applicable** — does not apply to this repo (wrong repo type, or a
  precondition is absent — see the contingency rule). One-line reason. Not counted as
  a pass or a fail.

By `mode`: **deterministic** — decide from the listed evidence artifacts, but if none is
found **do not fail automatically**: first check whether an *unlisted* artifact satisfies
the summary. **ai** — judgment over content. **none** — do not evaluate, list under
*Before you publish*. When the long-tail case is genuinely unclear, ⚠️ is the honest
answer — better than guessing either way.

**Polarity (`pass_when`).** Default is `pass_when: present` — the check passes when a
satisfying piece of evidence is **found**. A check marked **`pass_when: absent`**
(secrets, absolute paths, generated junk) is the reverse: it **passes when the
evidence is NOT found**. If you *find* the listed pattern, that is a ❌ — cite the
offending path/line, and the fix is to remove/replace it to meet the summary.

**Deriving the fix (❌ only).** Take the fix from the check's `evidence`: recommend
adding/adopting the **first** entry that fits this repo's language, phrased as an
action (e.g. an R repo failing "environment recorded" → "run `renv::snapshot()` and
commit `renv.lock`"). **Do not invent recommendations beyond the evidence.** One line,
no prose. (For an *absence* check — where the evidence is a violation to find, not a
thing to add — the fix is "remove/replace the flagged item".)

**Contingent checks (avoid double-counting).** Within a criterion, if a
`deterministic` presence check fails (e.g. there is no README or licence at all), mark
every later check whose answer depends on that artifact as ➖ not-applicable,
regardless of its mode. You cannot assess the content, format, or quality of something
absent. Report the underlying gap **once**, as the presence failure.

## The criteria (checks)

Internal reference. Do **not** print check ids or `mode` in the report — they are for
your use only. Group headings below are the human-readable report section names, listed
in working order (the order an author would tackle them); keep this order in the report.

### Data

**Data availability is documented and access is facilitated where possible** (`data-availability`)
- [data-access-instructions] (ai, should-fix) The repository states whether and how the data can be obtained
    evidence — any: a data-availability statement identifying data as public, restricted, unavailable, or available on request | a URL or DOI for public data | an application process for restricted data | contact information for data available on request
- [data-open-format] (ai, should-fix) Included data uses open formats where a suitable open format exists
    evidence — any: open formats such as CSV, TSV, TXT, JSON, Parquet, or an appropriate domain standard | no unexplained reliance on proprietary formats such as XLSX, SAV, or DTA

**Data files and variables are named consistently and machine-readably** (`data-naming`)
- [data-naming-consistent] (ai, should-fix) Included data files follow a consistent naming scheme
    evidence — any: consistent sample, date, or version identifiers | consistent delimiter and casing conventions
- [domain-naming-standard-used] (ai, polish) Established domain-specific data standards are used where applicable
    evidence — any: BIDS for neuroimaging | CDISC for clinical trials | Darwin Core for biodiversity data | NetCDF conventions or GenBank formats where relevant
- [data-machine-readable] (ai, should-fix, pass_when=absent) Column and variable names avoid characters that hinder automated processing
    evidence — any: whitespace or special characters in column names | duplicate column names

**Raw data is separated from processed or derived data** (`data-separation`)
- [data-separation-by-folder] (ai, should-fix) Raw and processed data are stored separately when the workflow creates derived data
    evidence — any: separate locations such as data/raw and data/processed | preprocessing scripts write outputs outside the raw-data location
- [raw-data-read-only] (ai, must-fix, pass_when=absent) Scripts do not overwrite raw input data
    evidence — any: a script reads a raw input and writes changes back to the same file

**Input-data requirements are documented** (`input-data-requirements`)
- [input-data-format] (ai, should-fix) The format required to run the analysis with new input data is documented
    evidence — any: supported file types, required variables, units, missing-value codes, and long or wide shape | an example input or visualisation plus any required transformation steps
- [input-data-processing-level] (ai, should-fix) The required preprocessing state of new input data is documented
    evidence — any: whether input must be raw, cleaned, normalised, aggregated, transformed, or feature-engineered

**Obvious indicators of sensitive data are surfaced for author confirmation** (`sensitive-data`)
- [sensitive-data-check] (ai, must-fix, pass_when=absent) No obvious indicators of unintentionally shared sensitive data are present
    evidence — any: filenames or column headers suggesting names, personal email addresses, medical records, or government identifiers | notebook outputs that may expose personal information

### Code quality

**Comments help a reader understand non-obvious code** (`code-is-commented`)
- [comments-explain-why] (ai, should-fix) Comments explain the reasons for non-obvious choices rather than restating code
    evidence — any: reasoning behind a threshold, transformation, workaround, or domain-specific choice | do not reward comments that merely narrate a self-evident operation
- [comments-sufficient] (ai, polish) Non-obvious parts of the analysis are explained well enough to follow
    evidence — any: complex stretches, magic numbers, or unusual choices have enough context | do not require a comment on every line or a fixed comment density

**Code is organised into manageable pieces without large duplicated blocks** (`code-is-modular`)
- [no-duplicated-code] (ai, should-fix, pass_when=absent) No large blocks of logic are copied and repeated
    evidence — any: near-identical logic whose bug fixes would have to be repeated in several places | do not treat plotting layers, data-wrangling pipelines, or configuration blocks as duplication merely because they look similar
- [code-in-manageable-pieces] (ai, polish) The analysis is divided into readable stages rather than one monolithic script

**Variables and functions are named for what they hold or do** (`meaningful-names`)
- [meaningful-names] (ai, should-fix) Variable and function names communicate what they hold or do
    evidence — any: flag names that hide meaning, such as df2, tmp, data_final, res, foo, or a1 | allow conventional short names when their meaning is clear in context, such as loop counters and standard mathematical symbols

### Reproducibility-supporting practices

**Code execution is facilitated** (`execution-facilitation`)
- [workflow-automation] (ai, polish) The workflow can be run through a master script, workflow tool, or single command
    evidence — any: a main, master, run-all, or pipeline-controller script | a Makefile, Snakefile, targets or drake workflow, or Nextflow pipeline | single-command execution documented in the README

**The folder structure required to rerun the analysis is documented** (`folder-structure-documented`)
- [folder-structure-documented] (ai, should-fix) The rerun instructions specify the required folder structure
    evidence — any: a directory tree in the main README or reproduction guide | instructions identifying where externally obtained inputs must be placed
- [inputs-outputs-documented] (ai, should-fix) The rerun instructions specify where inputs come from and where outputs go
    evidence — any: the source and expected location of input data | the destination of generated results | input and output documentation for each major script | a data-flow diagram

**Published outputs are traceable to the code that generated them** (`output-traceability`)
- [outputs-mapped-to-code] (ai, polish) Published figures, tables, and reported numbers are mapped to their generating code
    evidence — any: a README mapping such as Figure 1 -> scripts/create-figure-1.R | script headers or workflow comments naming the publication outputs they generate

**The code is portable across machines and operating systems** (`portable-code`)
- [no-absolute-paths] (deterministic, must-fix, pass_when=absent) The code contains no hard-coded absolute paths that tie it to one machine
    evidence — any: file-access paths beginning /Users/, /home/, /mnt/, or /Volumes/ | Windows drive paths such as C:\ or D:/ | home-relative paths beginning ~/
- [no-setwd-or-chdir] (deterministic, should-fix, pass_when=absent) The code does not pin the process to a machine-specific working directory
    evidence — r: setwd(...); python: os.chdir(...)
- [os-independent-code] (ai, should-fix, pass_when=absent) The code avoids operating-system-specific assumptions that prevent execution elsewhere
    evidence — any: path construction with hard-coded separators or string concatenation | OS checks without corresponding implementations for supported systems | reliance on Desktop, Documents, or another machine-specific location

**Randomness is controlled where it affects results** (`randomness-is-reproducible`)
- [seed-is-set] (ai, should-fix) A fixed seed covers randomness that affects analysis results (only when such randomness is used)
    evidence — r: set.seed(...) before the relevant random operations; python: random.seed(...), numpy.random.seed(...), or numpy.random.default_rng(seed) | framework-specific seeds such as torch.manual_seed(...) or tf.random.set_seed(...)

**Instructions for reproducing the analysis are provided** (`reproduction-instructions`)
- [code-execution-documented] (ai, must-fix) The main README explains how to run or reproduce the analysis
    evidence — any: execution steps, commands, or a workflow description in the root README | a root README link to a dedicated reproduction guide
- [run-order-clear] (ai, should-fix) The order in which code must run is apparent or documented
    evidence — any: numbered scripts where the language permits them | ordered execution steps or a workflow diagram in the README | a documented master script that calls the other scripts
- [quick-start] (ai, polish) Complex projects provide a quick start for running the main analysis with minimal setup
    evidence — any: a Quick start or Getting started section in the main README

### Environment & dependencies

**The computational environment is recorded** (`environment-captured`)
- [env-file-present] (deterministic, must-fix) A dependency/environment record exists
    evidence — r: renv.lock | DESCRIPTION with Imports/Depends | committed sessionInfo() / sessioninfo::session_info() output; python: requirements.txt | pyproject.toml with dependencies | environment.yml | poetry.lock / pdm.lock / uv.lock | setup.py with install_requires; matlab: list of required toolboxes + MATLAB version (README or ver output); any: Dockerfile | apptainer.def / Singularity
- [env-versions-pinned] (ai, should-fix) Dependencies are pinned to exact versions, not just named
    evidence — r: renv.lock | DESCRIPTION with versioned Imports/Depends; python: requirements.txt using == (not >= or unpinned) | poetry.lock / pdm.lock / uv.lock | pyproject.toml with pinned versions
- [additional-toolboxes-specified] (ai, should-fix) Required toolboxes and system-level dependencies outside the package manager are named
    evidence — r: non-CRAN packages or system libraries such as GDAL or JAGS documented in the README; python: non-pip dependencies such as system libraries, CUDA, or cuDNN documented in the README; matlab: required MATLAB toolboxes listed by name; any: required compilers, drivers, or external binaries are documented
- [installation-instructions-exist] (ai, should-fix) The README explains how to install dependencies and set up the environment
    evidence — any: an Installation, Setup, or Getting started section with commands | a documented install target such as make install
- [env-language-version] (ai, should-fix) The language version itself is recorded
    evidence — r: R version recorded in renv.lock; python: .python-version | requires-python in pyproject.toml | Python version stated in README; matlab: MATLAB version stated in README; any: language version stated in a configuration file or README
- [container-image-provided] (ai, polish) A ready-to-pull container image is provided when container distribution is appropriate
    evidence — any: docker pull and docker run commands for a published image | CI that builds and publishes an image | devcontainer.json that references a pre-built image | an Apptainer or Singularity definition
- [compute-resources-specified] (ai, should-fix) Compute resources and job submission are documented when the analysis requires HPC
    evidence — any: CPU, memory, GPU, and wall-time requirements in the README | a SLURM, PBS, or LSF submission script | module, queue, and submission instructions

### Repository hygiene

**Installable third-party packages are not copied into the repository** (`no-external-packages`)
- [no-external-packages] (ai, should-fix, pass_when=absent) Third-party dependencies are not bundled when they can be installed and cited separately
    evidence — any: Toolboxes, external, third-party, lib, packages, or library folders containing copied dependencies | complete MATLAB toolboxes, Python packages, R libraries, or external distributions | large groups of files unrelated to the project's own source code

**No machine-generated or throwaway files are present** (`no-junk-files`)
- [no-junk-files] (deterministic, should-fix, pass_when=absent) No machine-generated, operating-system, or editor junk files are present
    evidence — python: __pycache__/, *.pyc, .ipynb_checkpoints/, .pytest_cache/, or *.egg-info/; r: .Rhistory, .RData, .Ruserdata, or .Rproj.user/; any: .DS_Store, Thumbs.db, node_modules/, unneeded build/ or dist/ output | editor backup files such as *.bak, *~, or *.orig | user-specific editor metadata; intentionally shared editor configuration is not junk
- [no-hand-versioned-duplicates] (ai, polish, pass_when=absent) No ambiguous hand-versioned or final-copy duplicates are present
    evidence — any: neighbouring files such as analysis.R, analysis-v2.R, and analysis-FINAL.R | files named Copy of ... or ... copy | near-identical files whose canonical version is unclear

**No secrets or credentials are present** (`no-secrets-present`)
- [no-secrets-present] (deterministic, must-fix, pass_when=absent) No secrets or credentials are present
    evidence — any: .env or .Renviron files | private key files such as id_rsa, *.pem, *.ppk, or *.key | hard-coded API keys, tokens, client secrets, or passwords | cloud credential or service-account files

### Structure & orientation

**Contact information is provided** (`contact-info`)
- [contact-info-present] (ai, should-fix) The repository names a contact person or a way to ask questions
    evidence — any: a README contact section with an email address or account handle | maintainer details in CITATION.cff | the issue tracker identified as the place for questions

**Files and folders are named meaningfully and organised clearly** (`file-naming-and-structure`)
- [repo-name-meaningful] (ai, should-fix) The repository name reflects what the project is about
    evidence — any: a project- or paper-specific name rather than a placeholder such as new-repo, test, or project1
- [meaningful-file-and-folder-names] (ai, should-fix) File and folder names describe their contents
    evidence — any: descriptive names rather than generic names such as script1.R or Untitled.ipynb | a consistent, machine-readable naming convention
- [conventional-folder-structure] (ai, should-fix) Files are organised into a clear, conventional folder structure
    evidence — any: separate top-level areas for code, data, and results where those artifacts are present | a structure resembling an established project template

**The README orients a newcomer** (`readme-informative`)
- [readme-present] (deterministic, must-fix) A README exists at the repository root
    evidence — any: README.md | README.rst | README.txt | README
- [readme-has-content] (deterministic, must-fix) The README contains substantive content rather than a placeholder
    evidence — any: README contains more than a title line | README is not an unedited hosting-platform stub
- [readme-states-what-and-why] (ai, must-fix) The README says what the project is and why it exists
    evidence — any: a title and a 1–3 sentence description of the project / research question | what the analysis addresses or produces
- [readme-structure-overview] (ai, should-fix) The README explains how the repository is organised
    evidence — any: a directory tree or prose description of the top-level folders | an established layout whose parts are explained
- [readme-matches-repo] (ai, should-fix) The README's description of files and folders matches the actual repository
    evidence — any: folders and scripts named in the README actually exist in the repo | no references to renamed, moved, or deleted files
- [readme-nice-to-haves] (ai, polish) The README includes useful orientation aids for the kind of project being reviewed
    evidence — any: a table of contents for a long README | an example command and expected outputs | versioned releases or relevant status badges

### Licensing & citation

**The repository explains how to cite the code** (`code-citation-present`)
- [code-citation-present] (deterministic, should-fix) The repository states how to cite the code
    evidence — any: CITATION.cff | codemeta.json | CITATION | CITATION.txt | a How to cite or Citation section in the README
- [code-citation-complete] (ai, should-fix) The software citation contains authors, title, year, version, and an archive DOI when available

**The repository has a clear, suitable licence** (`license-present-and-suitable`)
- [license-present] (deterministic, must-fix) A licence file (or full licence text) exists
    evidence — any: LICENSE | LICENCE | LICENSE.md | LICENCE.md | LICENSE.txt | LICENCE.txt | LICENSE-<name> or LICENCE-<name> for a named or dual licence | full licence text present in README
- [license-machine-readable] (deterministic, should-fix) The licence is available as plain text rather than only in a binary document
    evidence — any: LICENSE or LICENCE with no extension or a .md, .txt, or .rst extension | full licence text present in README
- [code-license-osi] (ai, should-fix) The code licence is OSI-approved and is not a data licence misapplied to code
    evidence — any: MIT, GPL, LGPL, Apache-2.0, or another licence on the OSI list
- [data-license] (ai, should-fix) Included data carries its own licence (only when data is present)
    evidence — any: a licence file inside the data folder | a data licence (e.g. CC-BY-4.0, CC0) named with its text | a README statement that clearly scopes a licence to the data

**The repository is linked to its accompanying publication** (`linked-to-publication`)
- [publication-linked] (ai, should-fix) The repository links to the accompanying paper with its DOI when a paper exists
    evidence — any: a paper reference and DOI in the README | a CITATION.cff preferred-citation entry for the paper | a Publication section naming the paper and DOI

### Archive & release

**Installation and rerun instructions have been tested independently** (`installation-instructions-verified`)
- [installation-instructions-verified] (none, polish) Ask a colleague to test the setup and rerun instructions from a fresh copy

**The manuscript links to the archived repository** (`manuscript-links-repository`)
- [manuscript-links-repository] (none, should-fix) Add a data/code availability statement that links to and cites the archived repository DOI

**A fixed release is deposited in a durable archive** (`repository-archived`)
- [archive-doi-created] (none, should-fix) Deposit the release in Zenodo, Dryad, Figshare, or a domain repository and record its DOI

## Write and return `REVIEW.md`

Write the complete report as `REVIEW.md`.

- In a local workspace, save it at the reviewed repository's root.
- In a browser or cloud session, provide `REVIEW.md` as a downloadable attachment if
  supported.
- If attachments are unavailable, return the complete report in a Markdown code
  block so the user can save it.

Do not commit, push, create a pull request, or modify any other repository file
unless the user explicitly requests it.

Use exactly this structure. Show **every** check as one line, with a **blank line
between every item** so each renders on its own line. `mode: none` checks appear only
under *Before you publish*, never in the checklist or the count; omit a checklist
section that has no other checks left. Tag `❌`/`⚠️` with their severity
in plain text; do not tag `✅`/`➖`.

A `✅` line is the check summary **and nothing else** — no evidence, no file paths,
no examples, no praise (not "e.g. see `analysis.R`"). Evidence and locations belong
only on `❌` and `⚠️` lines.

**Locations (`❌`/`⚠️` only).** If the problem sits in specific places, append them:
1–3 places → list every one (`path` or `path:line`); more than 3 → two examples
plus the count ("e.g. `a.R:12`, `b.R:40` (+5 more)"). Omit locations when the
finding is a missing artifact. Never quote a secret value.

**Ordering** (three levels):

1. **Sections** — in the order the criteria groups are listed above (working order).
2. **Within a section: to-do before done.** First every `❌` and `⚠️` (the author has to
   act on or verify these), then every `✅`, then every `➖`. No sub-headings or dividers.
3. **Within the to-do block: by importance.** must-fix → should-fix → polish; at equal
   severity, `❌` before `⚠️`. Keep `✅` and `➖` in the order the checks are listed
   above, so reports stay stable between runs.

```
> ⚠️ Delete this file before publishing / archiving the repository — it lists the
> repo's own open issues and is not meant to ship. (Add REVIEW.md to .gitignore.)

# Repository review

_Context: <AI tool and access mode, e.g. "Claude Code (agentic, filesystem)">._
_Checks reviewability and completeness —
it does NOT run the code and does NOT verify the analysis reproduces._

**What I understood this repo to be:** <1–2 sentences — the repo type and what it does,
so you can sanity-check that I read the scope correctly.>

_Legend: ✅ pass · ❌ needs fixing · ⚠️ couldn't verify · ➖ not applicable_

## Do this first
<Numbered list of AT MOST 5 open must-fix ❌ items — the blockers that stop a stranger
from safely using the repo at all — each as its one-line fix. One entry per underlying
problem (if several checks fail for the same cause, list it once). Order: legal/safety
risks (secrets, sensitive data, licence) → can't get started (README, how to run,
environment) → everything else. Never include ⚠️, should-fix or polish items; further
must-fix items stay in the checklist only. If there are none, write: "No must-fix items
open — nice.">

## Before you publish
These steps can't be checked from the files in the repository — tick them off
yourself before you publish.

<The `mode: none` checks as a plain to-do list, e.g.>
- [ ] Deposit on Zenodo / a domain repository to get a DOI
- [ ] Ask a colleague unfamiliar with this tool to run the analysis

## Checklist

### <Human section name>

❌ <check summary> (must-fix) — <one-line fix>[: <locations>]

⚠️ <check summary> (must-fix) — couldn't verify: <what was ambiguous>[: <locations>]

❌ <check summary> (should-fix) — <one-line fix>[: <locations>]

⚠️ <check summary> (polish) — couldn't verify: <what was ambiguous>[: <locations>]

✅ <check summary>

➖ <check summary> — not applicable: <one-line reason>

<...one line per check, a blank line between items, every check shown; per section: to-do (❌/⚠️ by severity) → ✅ → ➖...>

---
_Checks passing: X of Y applicable._  <!-- factual count only; never a score, grade, or badge -->
_criteria dev (compiled 2026-11-27) · model: <your model id> · date: <today's date>_
```

Notes:
- The **count** is a factual tally over *applicable* checks (exclude ➖), never a score,
  percentage, grade, or badge.
- A genuinely good repo comes back **all ✅** with an empty "Do this first" section. That
  is a valid, expected outcome — do not invent findings to fill space.
