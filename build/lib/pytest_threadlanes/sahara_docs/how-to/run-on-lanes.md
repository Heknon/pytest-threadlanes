---
title: "Run a suite on thread lanes"
description: "Run a pytest suite on pytest-threadlanes lanes, in one process or across pytest-xdist processes, with the flags it requires."
pageType: how-to
owner: "morphine"
---

When a suite of long, I/O-bound tests is held back by the memory each
pytest-xdist worker process costs.

## Before you start

- Python 3.12 or later, pytest 8 or 9, pytest-xdist 3.6 or later, and
  pytest-threadlanes installed in the test environment.
- You have run [`--lanes-detect`](/threadlanes/how-to/find-shared-state/)
  and dealt with what it found.

## Steps

1. Pick the mode:

   ```bash
   pytest --lanes 200          # 1 process x 200 lanes
   pytest -n 8 --lanes 25      # 8 xdist processes x 25 lanes each = 200 lanes
   ```

   A reasonable start is 8–16 processes × 25–50 lanes. A crash takes down
   every lane in its process, so `-n` sets how much one crash costs.
2. Add the flags your versions need; the plugin refuses to run without them:

   | Condition | Pass |
   | --- | --- |
   | Python 3.13 or earlier | `-p no:warnings` |
   | Python 3.14 or later | `-X context_aware_warnings=1`, or `PYTHON_CONTEXT_AWARE_WARNINGS=1` (on by default on 3.14t) |
   | pytest 8.3.5 or earlier | `-p no:threadexception -p no:unraisableexception` |
3. Choose a scheduler. Your `pytest_xdist_make_scheduler` hook serves all
   modes. Without one, use `--lanes-dist load|loadscope|loadfile|loadgroup` in
   one process, or xdist's `--dist` with `-n`. `each` and `worksteal` are
   refused.
4. Mark tests that must run alone `@pytest.mark.lanes_exclusive`.

## Check it worked

Test reports name lanes such as `ln3` or `gw2.ln3` (`worker_id`,
`report.lane_id`). A test that patches process-wide state without being
exclusive fails and says what to do.

To debug one test, run it without `--lanes` and without `-n`: `--pdb` does not
work on lanes.
