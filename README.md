# AI Submission Pre-flight Checker

## Project overview

This proposed system helps university students check whether a programming assignment package is complete and ready for review before submission. A student uploads a package for one predefined Python assignment and receives an evidence-based readiness report covering missing files, naming and page-limit rules, README instructions, basic dependency consistency, and potential documentation gaps.

The checker supports preparation for submission. It does not grade academic quality, detect plagiarism, execute uploaded code, or submit work to a learning-management system.

## Proposed Week 7 workflow

1. A student uploads a PDF report, README, source code, and `requirements.txt` for a predefined assignment specification.
2. The API validates the package, records the submission and file inventory, and queues a check job.
3. A background worker runs deterministic checks and a bounded AI review of documentation.
4. The system stores findings, supporting evidence, and a readiness score.
5. The student retrieves the job status and readiness report, then decides what to correct.

## Project scope

The first vertical slice supports one Python assignment structure and one fixed checklist. AI findings are advisory and require supporting evidence. If AI review is unavailable, deterministic results remain available and the report identifies the skipped review.

## Documentation

- [Architecture and data model](docs/architecture.md)
- [Week 4 proposal and milestone plan](submissions/week04/README.md)

## Current implementation status

This repository currently contains the MAIE 6000C starter implementation, which processes cases through an API, PostgreSQL, a worker, and an internal AI service. The pre-flight checker described above is the Week 4 **proposal**, not an implemented upload workflow. Its planned schema, endpoints, worker checks, and AI review are documented in the architecture draft.

The starter's existing Docker Compose and test instructions remain applicable to the starter workflow until the proposed features are implemented and verified.
