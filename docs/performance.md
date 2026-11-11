# Execution profiles and performance

PurgeTSS 7.19.0 adds execution profiles and reduces repeated work during generation. The class syntax, utility catalog, font priorities and generated-output contracts remain unchanged. This page explains the measurements and file behavior you will see when running the CLI.

## Save an execution profile

Use the global `--profile <file.json>` option with a command or alias:

```bash
purgetss --profile purge-profile.json
purgetss build --profile build-profile.json
purgetss bf --module --profile fonts-profile.json
purgetss --all --profile all-profile.json
```

The destination is relative to your current directory unless you provide an absolute path. Its parent directory must exist. Running the command again with the same destination replaces the previous profile.

Profiles use `schemaVersion: 1`:

| Field | Meaning |
| --- | --- |
| `command` | Canonical command name, even when you invoke an alias; `--all` identifies the combined run. |
| `status` | `completed`, `skipped`, or `failed`. |
| `durationMs` | Elapsed milliseconds from profile creation to the final snapshot. |
| `cpu.user`, `cpu.system` | CPU time used by the CLI process, in microseconds. |
| `memory` | Process memory in bytes. `peakRssBytes` is the process's peak resident memory. |
| `phases` | Timings for command work. A phase can also identify skipped or failed work. |
| `counters` | Counts collected during the run. Available counters depend on the command and work performed. |
| `skipReason` | Present when the run records a reason for skipping work. The LiveView guard uses `liveview-2s`. |

File counters cover attempted reads, writes and copies during command execution. Copies also count as writes. `sourceReads`, `xmlParses`, and `jsParses` identify work in the shared source analyzer. These counts include different scopes: total file reads include more than XML and controller sources.

The normal `Finished purging in` message measures purge work. An external process timer also includes Node startup. Profile and phase durations have their own start points, so compare the same measurement across runs.

Use `--debug` to print timings in the terminal:

```bash
purgetss --debug
purgetss --debug --profile purge-profile.json
```

Normal progress messages remain available when profiling. Profiles are also saved for controlled failures. If the destination cannot be written, PurgeTSS reports the problem to stderr and preserves the command's original exit code.

## LiveView and skipped work

The two-second LiveView guard remains in place. If `app.tss` was purged less than two seconds ago, the purge is skipped and the profile records `liveview-2s`.

Every completed purge writes `app.tss` and renews its modification time, even when its content is identical. A skipped purge leaves that file untouched. This timestamp is part of the guard.

With `--all`, other phases can complete while the purge is skipped. The overall status can therefore be `completed` with a skipped purge phase. An empty font folder can also produce a skipped `build-fonts` phase without failing the combined run.

When comparing full purges, allow the guard interval to pass. Keep initial runs, subsequent completed runs and skipped runs separate.

## Generated-file messages

The CLI compares generated text with the existing file before writing it:

| Message | What happened |
| --- | --- |
| `file created!` | The file did not exist and was written. |
| `file updated!` | The file existed and its bytes changed. |
| `unchanged (not written)` | The bytes were identical. The file and its modification time were preserved. |

This applies to generated text such as `utilities.tss`, `definitions.css`, `fonts.tss` and generated font modules. An explicit `purgetss build` or `purgetss build-fonts` still processes its inputs and computes the output. It can finish successfully without rewriting those files. If a generated file is missing, the command creates it.

Messages such as `Copying font` and `Processing` describe those operations; they do not imply that the resulting TSS changed. `app.tss` follows the [LiveView timestamp rule](#liveview-and-skipped-work) instead of skipping identical writes.

## Configuration signatures

PurgeTSS evaluates `config.cjs` for each invocation and builds from an independent copy. A signature covers the effective configuration and generator inputs, including generator sources and relevant font/style inputs. Changes made within the same second are detected.

The signature is stored in `purgetss/.cache/generator-signature.json`, using less than 4 KiB of metadata. Add `purgetss/.cache/` to `.gitignore`.

An existing project without this metadata rebuilds its utility catalog once to establish the signature. The first invocation can therefore take longer than a subsequent one. Configurations with values that cannot be signed reliably rebuild conservatively.

Rebuild messages state the reason, for example:

```text
Generator signature missing; rebuilding utilities.tss...
Generator signature unreadable; rebuilding utilities.tss...
Generator signature invalid; rebuilding utilities.tss...
Generator inputs changed; rebuilding utilities.tss...
Generator inputs cannot be signed reliably; rebuilding utilities.tss...
```

A rebuild can happen without an edit to `config.cjs`: the metadata may be missing, or generator inputs may have changed. `build` continues to produce the full catalog, definitions and requested glossary.

## Work reused within a run

During a purge, extraction, missing-class diagnostics and SVG reference detection share source text, XML parsing and controller ASTs. The context is released when the invocation ends, including when it fails. The next invocation reads the sources again; XML, JavaScript and AST results are not cached between invocations.

The generator uses indexes for larger class selections and exact `apply` lookups while preserving ordering, duplicates, first matches, font priorities and prefix searches. Help and version load without evaluating project configuration.

CSS and codepoint inputs are shared between paired TSS and JavaScript font generators. Image generation reuses metadata and identical renders for each image, with a queue capped at two jobs. Output order, codecs, dimensions, alpha, supersampling, compression and optional quantization are preserved. The [SVG pipeline's PNG cache](./app-assets/3-svg-pipeline.md#the-png-cache) is separate from source-analysis reuse.

## Measured results

During optimization validation, both variants ran on the same Mac with Node 22.23.2, directly in three real projects and a minimal fixture. Each variant had a separate initial run and five subsequent samples in alternating order. The table shows median process times for completed purges; LiveView skips were measured separately.

| Project | XML parses | JS parses | Before | After | Reduction |
| --- | ---: | ---: | ---: | ---: | ---: |
| Real project A | 70 | 54 | 1140.2 ms | 471.9 ms | 58.6% |
| Real project B | 10 | 10 | 680.5 ms | 448.8 ms | 34.1% |
| Real project C | 198 | 36 | 1591.3 ms | 547.1 ms | 65.6% |
| Minimal fixture | 1 | 0 | 480.3 ms | 388.4 ms | 19.1% |

XML and JS counts are parse operations per completed purge after optimization, included to give a reference for each workload. They do not measure class counts or overall application complexity.

Generated project outputs matched byte for byte; signature metadata was checked separately. The minimal fixture's first call increased from 398.3 to 481.2 ms because it needed to establish the signature. The table describes subsequent runs. Results apply to the tested projects and machine; other projects may see different timings.

Across image codecs, all 56 outputs matched in bytes, decoded pixels, dimensions and alpha, including the Sharp 0.35.3 to 0.35.5 update. The two-job image queue was accepted after its measured maximum RSS stayed within 20% of the reference. Image quality settings were retained.

For the fixtures, methodology and reproduction commands, see the [optimization implementation report](https://github.com/macCesar/purgeTSS/blob/v7.19.0/docs/project/optimization.md).
