---
title: "Thread lanes"
description: "pytest-threadlanes is a pytest plugin that runs pytest-xdist's own schedulers on thread lanes, so long I/O-bound tests run hundreds-wide without a process per test."
pageType: landing
owner: "morphine"
sidebar:
  label: Overview
  order: 0
---

pytest-threadlanes runs tests on **lanes**: threads that each behave like a
pytest-xdist worker, scheduled by xdist's own schedulers. Long, I/O-bound
suites can then run hundreds or thousands of tests at once without the 150–500
MB of memory that each xdist worker process costs.

## Where to start

- [Run a suite on thread lanes](/threadlanes/how-to/run-on-lanes/)
- [Find tests that are unsafe on lanes](/threadlanes/how-to/find-shared-state/), before the first real run.
- [Lanes](/threadlanes/concepts/lanes/): what is shared, what is isolated, and the limits.
- [Options](/threadlanes/reference/options/)
- In Sahara UI, each lane is a worker: [The live worker view](/sahara/ui/concepts/live-worker-view/).
