---
title: "Find tests that are unsafe on lanes"
description: "Run pytest-threadlanes' detector to find the tests and fixtures that change process-wide state concurrent lanes would share."
pageType: how-to
owner: "morphine"
---

Before a suite's first run on lanes, or when tests that pass alone fail on
lanes.

## Before you start

- pytest-threadlanes is installed.

## Steps

1. Run the suite under the detector, without `--lanes` and without `-n`:

   ```bash
   pytest --lanes-detect --lanes-detect-report=shared-state.json
   ```

   Tests run one at a time. Around each, the detector compares process state
   and everything reachable from your modules' globals, and records every
   patch, environment write and `chdir`.
2. Read the report at the end of the output, headed
   `pytest-threadlanes shared-state report`, and act on each finding:

   | Kind | Action |
   | --- | --- |
   | `UNSAFE per-test` | Make it per lane (a contextvar), or mark the tests `lanes_exclusive` |
   | `UNSAFE patched` | Mark the test `lanes_exclusive` |
   | `CHECK grows` | Check the container is thread-safe |
   | `OK set-once` | Nothing, if it is safe to share between threads |
3. List state that is shared on purpose in `lanes_detect_ignore`, and add an
   installed infrastructure package to inspect with `lanes_detect_modules`.
4. Run the detector again.

## Check it worked

The report reads "nothing unsafe found."

A value set and restored inside one test body (a context manager) is not
seen unless it goes through mock or monkeypatch, and state inside C
extensions or external resources (ports, files, database rows) is not
watched at all.
