---
title: "Lanes"
description: "How pytest-threadlanes lanes work: threads that behave like pytest-xdist workers, what they share, what is isolated per lane, and the limits."
pageType: explanation
owner: "morphine"
---

A lane is a thread that behaves like a pytest-xdist worker. Scheduling comes
from xdist's real scheduler, fixtures of every scope are cached per lane, and
reporters see the hook stream they would see under `-n`: for the same suite,
report output is the same in all three modes (`-n`, `--lanes`, and both).

## Why threads

With tests that take an hour or two and mostly wait on I/O, xdist's one
process per concurrent test costs 150–500 MB each, so memory caps
concurrency. Threads share one process's memory. The infrastructure under the
tests is synchronous, so converting it to async is not an option.

## What is isolated per lane

pytest's setup state, fixture caches, output and log capture, `caplog`, the
temporary directory (`<root>/ln3`), the worker identity (`worker_id`,
`workerinput`) and the per-lane environment variables (`PYTEST_CURRENT_TEST`,
`PYTEST_XDIST_WORKER`). Each lane is named `ln<n>` in one process or
`gw<n>.ln<n>` across xdist processes; exclusive tests run on `ln-serial`.

## What is shared

Anything process-global: `mock.patch` and monkeypatching of modules,
`os.environ`, `chdir`, signals, logging levels, `random.seed`,
`socket.setdefaulttimeout`, `locale.setlocale`. Tests that change these must
be marked `lanes_exclusive` or fixed. A **patch guard** fails a non-exclusive
test that patches process-wide state, and the run fails closed if pytest or
xdist internals it relies on have changed.

## Limits

- A hung thread cannot be killed. pytest-timeout is refused in one process
  and works with `-n`.
- A crash takes down every lane in its process.
- `--pdb` is unsupported.
- Before Python 3.14, `pytest.warns`, `deprecated_call` and `recwarn` need
  `lanes_exclusive`.

## In Sahara

With [pytest-failure-instrumentation](/failure-instrumentation/) 0.14.0 or
later, each lane is a worker in Sahara UI's Workers view. A lane with no test
in flight is **idle**; its memory is its whole process's. See
[The live worker view](/sahara/ui/concepts/live-worker-view/).
