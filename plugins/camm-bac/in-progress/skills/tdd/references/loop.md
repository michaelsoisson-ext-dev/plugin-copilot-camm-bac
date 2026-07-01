# [title:loop spec]▸

LOOP SPEC GOAL: every test in /tests/auth passes, lint is clean, no type errors.

EACH ITERATION: 1. run the test suite and read every failure 2. pick the single highest-impact failure 3. write the smallest change that fixes it 4. re-run the tests, lint, and type checker

VERIFY: green tests + zero lint warnings + zero type errors

STOP WHEN: verify passes, OR 8 iterations reached

ON STOP: summarize what changed and what still fails
