# Week 04 Submission Folder

# Week 4 Submission — Team Architecture Proposal and Project Plan


## Team information

- Team name: Group 9
- Repository: https://github.com/jshui1015/MAIE6000C-starter-jingjing-shui
- Checkpoint tag: `w04-proposal`
- Team members: SHUI Jingjing, WANG Chenxi, YANG Yixuan, ZHOU Tao

## 1. Problem context

Students preparing a programming assignment may overlook missing files, unclear setup instructions, inconsistent documentation, or undeclared dependencies. These problems make a submission difficult to review or reproduce. Our primary user is a university student preparing to submit a Python assignment. The proposed checker gives the student an evidence-based readiness report before submission. It does not assign a grade or guarantee that every academic requirement is met.

## 2. User workflow

1. A student uploads a supported package containing a PDF report, README, Python source code, and `requirements.txt` for a predefined assignment checklist.
2. The API validates required files, accepted types, and package limits; it returns a specific error for an invalid package.
3. For an accepted package, the API creates a `Submission` and a `SubmissionFile` inventory.
4. The API creates a pending `CheckJob`, marks the submission queued, and returns submission and job IDs.
5. A worker claims the job and runs fixed checks for files, names, PDF page limit, README instructions, and basic dependency consistency.
6. A bounded AI function reviews the README and relevant report text against the checklist for potentially missing, unclear, or inconsistent documentation.
7. The worker stores a `CheckResult`, individual findings with evidence, and a readiness score, then marks the job and submission completed.
8. The student retrieves the status and report, reviews findings, and may upload a corrected package for another check.

**Exception path:** If processing fails, the worker stores the error and marks the job failed so the student can see the problem and retry. If only AI review is unavailable, deterministic results remain retrievable and the AI check is marked unavailable.

## 3. Success criteria

- **Product KPI:** In the Week 7 demonstration, a student can upload one supported package and retrieve a report that identifies at least one deliberately missing or inconsistent item with supporting evidence.
- **Operational KPI:** The job's persisted status reaches `completed` or `failed`; an unavailable AI review is visible while deterministic check results remain retrievable.

## 4. Architecture summary

The [architecture proposal](../../docs/architecture.md) contains the workflow, component and schema diagrams, state-transition table, worker and AI plan, API draft, risks, and scope cuts. The API handles intake and retrieval; PostgreSQL stores workflow state and reports; controlled file storage holds the package during checking; and a background worker performs deterministic checks and calls the AI review service.

## 5. Data model and persistence plan

The proposed entities are `AssignmentSpec`, `Submission`, `SubmissionFile`, `CheckJob`, `CheckResult`, and `Finding`. One assignment specification can have many submissions. Each submission has a file inventory and may have multiple check jobs. A completed job produces one result containing multiple findings. A finding may refer to a submitted file.

The system persists statuses, timestamps, checklist version, file metadata, check outcomes, severity, evidence, AI availability, and readiness score. Corrected files create a new submission, preserving previous results. New tables and columns require Alembic migrations; the starter's current `Case` and `Job` tables do not yet implement this proposal.

## 6. API / service plan

Proposed endpoint | Purpose |
|---|---|
| `POST /submissions` | Validate and accept a package; return submission and job IDs. |
| `GET /submissions/{submission_id}` | Return submission status and file inventory. |
| `GET /jobs/{job_id}` | Return processing status and a safe error message if the job failed. |
| `GET /submissions/{submission_id}/report` | Return the latest completed report and its findings. |

These endpoints are proposals, not routes already implemented in the starter. The API writes to PostgreSQL and file storage; the worker calls the internal AI service for documentation review.

## 7. Async / worker plan

The upload request validates and queues a job without waiting for document extraction or AI review. The worker claims the job, reads the stored package, extracts relevant text, runs fixed checks, requests the bounded AI review, calculates readiness, and persists the report. It records completion times and failures. A completed report with critical findings is distinct from a failed job.

## 8. AI-enabled component

AI reviews extracted README and relevant report text against a predefined checklist. It may identify unclear setup or execution instructions, inconsistencies between the README and report, and requirements that appear unaddressed. This bounded review helps with documentation questions that fixed file checks cannot fully answer. Each AI finding should include an explanation and supporting evidence; uncertain findings remain advisory for student review.

AI does not grade academic quality, detect plagiarism, execute code, or block submission. If the service is unavailable or its output is invalid, the system marks AI review unavailable and still returns deterministic results.

## 9. Milestone plan

| Week | Minimum credible outcome |
|---|---|
| 5 | API contract and first persistent submission-to-job path. |
| 7 | Complete vertical slice for one supported Python package. |
| 9 | Reproducible Docker Compose deployment. |
| 11 | Improved reliability, logging, tests, and AI-unavailable behavior. |
| 12 | Tested final workflow and demonstration. |
| 13 | Final submission review if required by the course schedule. |

## 10. Risks and scope control

- **Top risk 1 — excessive input scope:** Many languages, formats, and assignment structures would make validation unreliable. Week 7 targets one predefined Python package and fixed checklist.
- **Top risk 2 — unreliable AI findings:** AI may miss valid instructions or report false problems. Findings will show evidence, remain separate from deterministic checks, and allow student review.
- **Top risk 3 — unsafe uploaded code:** Executing student code creates security and isolation risks. The first version inspects files and documentation without executing code.
- **First scope cut:** Defer support for multiple assignment formats and languages. Accounts, dashboards, and LMS integration can also be deferred while preserving the upload-to-report workflow.

## 11. Artifact index

- [Project overview](../../README.md)
- [Architecture proposal](../../docs/architecture.md)
- [Schema diagram and entity table](../../docs/architecture.md#4-persistent-entities-and-schema)
- [Architecture flow diagram](../../docs/architecture.md#3-architecture-and-responsibilities)
- [API draft](../../docs/architecture.md#7-proposed-api-contract)
- [State-transition table](../../docs/architecture.md#5-state-transitions-and-ownership)
- [Risks, scope cuts, and milestones](../../docs/architecture.md#8-risks-and-scope-control)

## 12. AI Use Statement

**Tool used:** OpenAI Codex.

**Purpose and affected work:** Codex helped interpret the Week 4 lab instructions and draft the wording and structure of `README.md`, `docs/architecture.md`, and this Week 4 submission document. 

**Team verification:** We compared the proposal with our selected AI Submission Pre-flight Checker brief and the Week 4 rubric. We reviewed the relationships among `AssignmentSpec`, `Submission`, `SubmissionFile`, `CheckJob`, `CheckResult`, and `Finding`; checked that submission and job statuses are used consistently; and traced the proposed API → database → worker → report flow. We also checked that the Week 7 scope is limited to one predefined Python assignment package and that the readiness score uses completed deterministic checks only.

**Changes made after review:** No material changes were needed

**Suggestions rejected or deferred:** We deferred support for multiple programming languages because it would put the Week 7 vertical slice at risk.
