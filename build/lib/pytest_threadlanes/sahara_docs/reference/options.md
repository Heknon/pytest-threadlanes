---
title: "Options"
description: "Every pytest-threadlanes command-line option, ini setting and marker, with its default."
pageType: reference
owner: "morphine"
---

Every option, ini setting and marker pytest-threadlanes defines, from
`src/pytest_threadlanes/plugin.py`.

| Name | Kind | Default | Description |
| --- | --- | --- | --- |
| `--lanes N` | command line | — | Run tests on N thread lanes in this process; with `-n`, N lanes per xdist process. `0` turns lanes off. |
| `--lanes-dist MODE` | command line | xdist's `--dist`, else `load` | Built-in scheduler in single-process mode when no `pytest_xdist_make_scheduler` returns one: `load`, `loadscope`, `loadfile`, `loadgroup`. |
| `--lanes-allow-patches` | command line | off | Allow process-wide patches in tests that are not `lanes_exclusive`. |
| `--lanes-detect` | command line | off | Run tests one at a time and report which change process-wide state lanes would share. |
| `--lanes-detect-report PATH` | command line | — | Also write the `--lanes-detect` report as JSON to PATH. |
| `lanes_exclusive_fixtures` | ini | capsys, capsysbinary, capfd, capfdbinary, capteesys, recwarn, no_cover | Fixtures that force a test into the serial phase. |
| `lanes_allow_patches` | ini | false | Same as `--lanes-allow-patches`. |
| `lanes_interrupt_grace` | ini | 30 | Seconds to wait after Ctrl-C for interrupted lanes to run their teardown. |
| `lanes_detect_ignore` | ini | — | `--lanes-detect`: paths shared on purpose (fnmatch patterns), e.g. `module:myinfra.clients._CACHE`. |
| `lanes_detect_modules` | ini | — | `--lanes-detect`: extra packages to inspect besides the modules under the rootdir. |
| `lanes_detect_max_depth` | ini | 12 | `--lanes-detect`: how deep to follow attributes. |
| `lanes_detect_max_nodes` | ini | 200000 | `--lanes-detect`: most values per snapshot. |
| `@pytest.mark.lanes_exclusive` | marker | — | Run this test in the serial phase, alone. |
| `@pytest.mark.lanes_allow_patches` | marker | — | This test's process-wide patches are safe. |
