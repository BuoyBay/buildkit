---
title: "BuildKit"
summary: "Turn compiler output into diagnostics and bounded UTF-8 log lines."
category: "Overview"
order: 0
keywords: ["build", "diagnostics", "compiler", "logs"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# BuildKit

BuildKit supplies pure Buoy data and parsing helpers for a Problems panel or build transcript. It recognizes Buoy diagnostic JSON, conventional GCC/Clang and MSVC location lines, and ANSI CSI coloring. It never launches a compiler, resolves a path, or moves an editor selection.

Use it when a process transport already supplies raw bytes and the IDE needs typed diagnostic records. Keep the transport and compiler-specific coordinate conversion in the host application. There are no native libraries or package dependencies.

## Coordinate contract

A `BuildDiagnostic` preserves the compiler's line and character values exactly. Missing columns become zero; a zero value does not prove a coordinate base. Relative paths stay relative. An editor must establish the emitting compiler's convention before converting a location to its own document coordinates.

## Stream contract

`BuildLogDecoder` retains one incomplete line. It accepts split UTF-8 and split CRLF, strips CSI sequences only after a line is complete, and returns lines without terminators. Consume results promptly and feed bounded chunks. A decoder error poisons the stream until `Reset()`.

## Read next

- [Getting started](getting-started.md)
- [API reference](api.md)
