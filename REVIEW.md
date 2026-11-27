> ⚠️ Delete this file before publishing / archiving the repository — it is a pre-publication review checklist and is not meant to ship. (Add `REVIEW.md` to `.gitignore`.)

# Repository review

_Context: GitHub Copilot (agentic, filesystem)._
_Checks reviewability and completeness — it does NOT run the code and does NOT verify the analysis reproduces._

**What I understood this repo to be:** This is a scientific research compendium for an individual-based model of salinity tolerance trade-offs in salt-marsh plant communities. It contains pyMANGA inputs and outputs, analysis scripts, figure data, and manuscript figures.

_Legend: ✅ pass · ❌ needs fixing · ⚠️ couldn't verify · ➖ not applicable_

## Do this first

No must-fix items open — nice.

## Before you publish

These steps can't be checked from the files in the repository — tick them off yourself before you publish.

- [ ] Ask a colleague to test the setup and rerun instructions from a fresh copy.

- [ ] Add a data/code availability statement that links to and cites the archived repository DOI.

- [ ] Deposit a fixed release in Zenodo, Dryad, Figshare, or a domain repository and record its DOI.

## Checklist

### Data

✅ The repository states whether and how the data can be obtained

✅ Included data uses open formats where a suitable open format exists

✅ Included data files follow a consistent naming scheme

✅ Column and variable names avoid characters that hinder automated processing

✅ Raw and processed data are stored separately when the workflow creates derived data

✅ Scripts do not overwrite raw input data

✅ The format required to run the analysis with new input data is documented

✅ The required preprocessing state of new input data is documented

✅ No obvious indicators of unintentionally shared sensitive data are present

➖ Established domain-specific data standards are used where applicable — not applicable: the repository contains simulated ecological model outputs and no established domain standard is evident for these data.

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

➖ A ready-to-pull container image is provided when container distribution is appropriate — not applicable: this repository documents a standard Python and pyMANGA setup and does not require container distribution.

➖ Compute resources and job submission are documented when the analysis requires HPC — not applicable: the README describes local parallel simulation runs and does not indicate an HPC requirement.

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

❌ The software citation contains authors, title, year, version, and an archive DOI when available (should-fix) — add these details to the README citation section: `README.md:120-122`.

⚠️ The repository links to the accompanying paper with its DOI when a paper exists (should-fix) — couldn't verify: the manuscript is listed as submitted, but the repository does not establish whether a DOI has been assigned: `README.md:6`, `README.md:120-122`.

✅ The repository states how to cite the code

✅ A licence file (or full licence text) exists

✅ The licence is available as plain text rather than only in a binary document

✅ The code licence is OSI-approved and is not a data licence misapplied to code

✅ Included data carries its own licence (only when data is present)

---
_Checks passing: 49 of 51 applicable._
_criteria dev (compiled 2026-11-27) · model: Auto · date: 2026-11-27_
