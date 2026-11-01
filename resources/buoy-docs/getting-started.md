---
title: "BuildKit getting started"
summary: "Install BuildKit and try a complete usage example."
category: "Guides"
order: 10
keywords: ["build", "diagnostics", "compiler", "logs"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Copy `buildkit-0.1.0.buipkg` into your project's `packages/` directory and import `buildkit`.

## Parse a build stream

This example models one chunk from an existing process pipe. It sends ordinary output to the transcript and recognized locations to the Problems panel. Always call `Finish()` at EOF so an unterminated last line is retained.

```buoy
Import buildkit

Private Sub DisplayLine(line As String)
    Dim diagnostic As BuildDiagnostic = ParseBuildLine(line)
    If diagnostic = Nil Then
        Print line
    Else
        Print diagnostic.FileName + ":" + String.From(diagnostic.Line) + ": " + diagnostic.Message
    End If
End Sub

Sub Main()
    Dim decoder As New BuildLogDecoder()
    Dim chunk As Bytes = Bytes.FromString("src/main.bui:12:4: error: expected expression\nBuild stopped.", Text.Encodings.UTF8)
    For Each line As String In decoder.Feed(chunk)
        DisplayLine(line)
    Next
    For Each line As String In decoder.Finish()
        DisplayLine(line)
    Next
End Sub
```

## Handle failures

Malformed diagnostic JSON throws `JSONException`; invalid JSON schema or exceeded limits throw `InvalidArgumentException`. Log decoding can also throw `EncodingException` for invalid UTF-8. After a feed error, discard that call's output and reset only for a new stream. Previously returned lines remain valid.

Buoy JSON is limited to 16 MiB and 128 nesting levels; decoded NUL escapes reject. The default log line limit is 1 MiB, including escape bytes but excluding line endings. A lone CR ends a line rather than updating a progress display in place.

[Package overview](index.md) · [API reference](api.md)
