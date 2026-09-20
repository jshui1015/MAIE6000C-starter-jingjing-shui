# Week 3 Submission — Individual Readiness Lab

## Student information

- Name: SHUI Jingjing
- Student ID: 21340647
- Repository: https://github.com/jshui1015/MAIE6000C-starter-jingjing-shui
- Checkpoint tag: `w03-readiness`
- Commit SHA: b4915fd1637cf64a3f25697eab0a30e4caca9819

## 1. What I changed

I added an integration test for `GET /cases/{case_id}`. The test verifies that
requesting an unknown case ID returns HTTP 404 with the response detail
`Case not found`.

## 2. Files touched

- `tests/integration/test_api_case_flow.py` — added the unknown-case test
- `submissions/week03/README.md` — documented the change and verification


## 3. How I verified it

- Ran the targeted integration tests:
  `docker compose run --rm --no-deps api pytest -q tests/integration/test_api_case_flow.py`
  Result: 3 passed, 2 warnings in 0.12s.
- Ran the complete unit and integration test suite:
  `docker compose run --rm --no-deps api pytest -q tests/unit tests/integration`
  Result: 5 passed, 2 warnings in 0.12s.
- The warnings were deprecation warnings from third-party dependencies
  (`starlette` and `python-json-logger`) and did not cause any test failures.

## 4. Known limitations or notes

This change adds regression coverage for the existing 404 behavior. It does not
modify the API implementation or cover other API error responses.

## 5. AI Use Statement

- Tool used: OpenAI Codex.
- It was used to interpret the assignment requirements and help structure the
  submission documentation.
- I checked the suggestions against the course materials. I personally reviewed
  the final changes and ran all verification commands reported above.
