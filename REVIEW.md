> ⚠️ Delete this file before publishing / archiving the repository — it lists the
> repo's own open issues and is not meant to ship. (Add REVIEW.md to .gitignore.)

# Repository review

_Context: GitHub Copilot (agentic, filesystem)._
_Checks reviewability and completeness —
it does NOT run the code and does NOT verify the analysis reproduces._

**What I understood this repo to be:** A research compendium for a submitted manuscript: pyMANGA (individual-based model) simulation setups, simulation output, and Python analysis/figure code for a study of salinity tolerance trade-offs in salt-marsh plant communities. All inputs, outputs, and figure scripts are included; figures and tables can be recreated without rerunning the model.

_Legend: ✅ pass · ❌ needs fixing · ⚠️ couldn't verify · ➖ not applicable_

## Do this first

No must-fix items open — nice.

## Before you publish

These steps can't be checked from the files in the repository — tick them off
yourself before you publish.

- [ ] Deposit a fixed release on Zenodo / a domain repository and record its DOI
- [ ] Add a data/code availability statement to the manuscript that links to and cites the archived repository DOI
- [ ] Ask a colleague to test the setup and rerun instructions from a fresh copy

## Checklist

### Data

❌ Included data files follow a consistent naming scheme (should-fix) — harmonise the column headers across the salinity scenario CSVs (`t_step,V1,V27` in the 35/70 ppt files vs `t_step,salinity_1,salinity_2` in the 105 ppt files): e.g. `model_input/salinity/35_V1.csv:1`, `model_input/salinity/105_V1.csv:1` (all 6 salinity CSVs affected)

❌ The format required to run the analysis with new input data is documented (should-fix) — add a `model_input/README.md` documenting the salinity CSV columns and units, the species-parameter files, and the plant-distribution CSV: `model_input/`

✅ The repository states whether and how the data can be obtained

✅ Included data uses open formats where a suitable open format exists

✅ Column and variable names avoid characters that hinder automated processing

✅ Raw and processed data are stored separately when the workflow creates derived data

✅ Scripts do not overwrite raw input data

✅ The required preprocessing state of new input data is documented

✅ No obvious indicators of unintentionally shared sensitive data are present

➖ Established domain-specific data standards are used where applicable — not applicable: no established data standard for individual-based salt-marsh model output

### Code quality

✅ Comments explain the reasons for non-obvious choices rather than restating code

✅ Non-obvious parts of the analysis are explained well enough to follow

✅ No large blocks of logic are copied and repeated

✅ The analysis is divided into readable stages rather than one monolithic script

✅ Variable and function names communicate what they hold or do

### Reproducibility-supporting practices

✅ The workflow can be run through a master script, workflow tool, or single command

✅ The rerun instructions specify the required folder structure

✅ The rerun instructions specify where inputs come from and where outputs go

✅ Published figures, tables, and reported numbers are mapped to their generating code

✅ The code contains no hard-coded absolute paths that tie it to one machine

✅ The code does not pin the process to a machine-specific working directory

✅ The code avoids operating-system-specific assumptions that prevent execution elsewhere

✅ A fixed seed covers randomness that affects analysis results (only when such randomness is used)

✅ The main README explains how to run or reproduce the analysis

✅ The order in which code must run is apparent or documented

✅ Complex projects provide a quick start for running the main analysis with minimal setup

### Environment & dependencies

✅ A dependency/environment record exists

✅ Dependencies are pinned to exact versions, not just named

✅ Required toolboxes and system-level dependencies outside the package manager are named

✅ The README explains how to install dependencies and set up the environment

✅ The language version itself is recorded

➖ A ready-to-pull container image is provided when container distribution is appropriate — not applicable: lightweight pip-installable workflow, container distribution not warranted

➖ Compute resources and job submission are documented when the analysis requires HPC — not applicable: no HPC required; simulations run on a workstation (runtime and parallelism documented in the README)

### Repository hygiene

✅ Third-party dependencies are not bundled when they can be installed and cited separately

✅ No machine-generated, operating-system, or editor junk files are present

✅ No ambiguous hand-versioned or final-copy duplicates are present

✅ No secrets or credentials are present

### Structure & orientation

✅ The repository names a contact person or a way to ask questions

✅ The repository name reflects what the project is about

✅ File and folder names describe their contents

✅ Files are organised into a clear, conventional folder structure

✅ A README exists at the repository root

✅ The README contains substantive content rather than a placeholder

✅ The README says what the project is and why it exists

✅ The README explains how the repository is organised

✅ The README's description of files and folders matches the actual repository

✅ The README includes useful orientation aids for the kind of project being reviewed

### Licensing & citation

❌ The software citation contains authors, title, year, version, and an archive DOI when available (should-fix) — complete the "How to cite" section (currently a placeholder) with authors, title, year, version and the archive DOI once archived, e.g. via a CITATION.cff: `README.md:120`

✅ The repository states how to cite the code

✅ A licence file (or full licence text) exists

✅ The licence is available as plain text rather than only in a binary document

✅ The code licence is OSI-approved and is not a data licence misapplied to code

✅ Included data carries its own licence (only when data is present)

➖ The repository links to the accompanying paper with its DOI when a paper exists — not applicable: manuscript is submitted, no DOI exists yet (full citation is given in the README)

---
_Checks passing: 47 of 50 applicable._  <!-- factual count only; never a score, grade, or badge -->
_criteria dev (compiled 2026-11-27) · model: kimi-k3 · date: 2026-11-27_
