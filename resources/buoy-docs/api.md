---
title: "BuildKit API"
summary: "Public types and operations supplied by BuildKit."
category: "Reference"
order: 20
keywords: ["build", "diagnostics", "compiler", "logs"]
symbols: ["BuildSeverity", "BuildDiagnostic", "ParseBuoyDiagnostics", "ParseBuildLine", "StripAnsiCSI", "BuildLogDecoder", "BuildLogDecoder.Feed", "BuildLogDecoder.Finish", "BuildLogDecoder.Reset"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

## Diagnostic records

| Symbol | Contract |
| --- | --- |
| `BuildSeverity` | `Error`, `Warning`, and `Note`; fatal errors map to `Error`. |
| `BuildDiagnostic` | Mutable record with `FileName`, `Line`, `Character`, `Severity`, `Message`, `Code`, and `RawText`. |
| `ParseBuoyDiagnostics(source)` | Parse a complete object containing `errors` and `warnings`; return errors first, then warnings. |
| `ParseBuildLine(source)` | Return a recognized diagnostic or `Nil` for an ordinary line. |
| `StripAnsiCSI(source)` | Remove CSI sequences; other escape families remain. An incomplete trailing CSI sequence is dropped. |

Buoy JSON entries require string `file` and `message` and nonnegative integral `line` and `character` up to 2,147,483,647. Optional string `code` is retained. `RawText` is the original colored single line or the serialized JSON entry.

## Stream decoding

| Symbol | Contract |
| --- | --- |
| `BuildLogDecoder(maxLineBytes = 1048576)` | Create a bounded incomplete-line buffer. |
| `Feed(chunk As Bytes)` | Return complete color-stripped lines; preserve empty lines. |
| `Finish()` | Emit a remaining unterminated line once. |
| `Reset()` | Drop pending bytes and clear the failed state. |

## Supported formats

Text parsing recognizes `file:line[:column]: severity: message` and `file(line[,column]): severity [code]: message`. It does not interpret include stacks, caret diagrams, leading MSBuild project prefixes, OSC hyperlinks, terminal cursor behavior, or arbitrary multiline compiler prose. A plain linker line without coordinates may remain ordinary log output.

[Getting started](getting-started.md) · [Package overview](index.md)


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
