---
title: "buildkit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["BuildDiagnostic", "BuildLogDecoder", "BuildSeverity"]
dependencies: []
symbols: ["BuildDiagnostic", "BuildLogDecoder", "BuildSeverity"]
related: ["api.md", "getting-started.md", "index.md"]
---

# buildkit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## BuildSeverity

`Public Enum BuildSeverity As Integer`

### Values

| Name | Type | Value / Default | Description |
| --- | --- | --- | --- |
| `Error` | `BuildSeverity` | `1` | — |
| `Warning` | `BuildSeverity` | `2` | — |
| `Note` | `BuildSeverity` | `3` | — |

## BuildDiagnostic

`Public Class BuildDiagnostic`

Compiler coordinates are preserved exactly; absent locations/columns use zero.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `FileName` | `String` | No | No | — | — |
| `Line` | `Integer` | No | No | — | — |
| `Character` | `Integer` | No | No | — | — |
| `Severity` | `BuildSeverity` | No | No | — | — |
| `Message` | `String` | No | No | — | — |
| `Code` | `String` | No | No | — | — |
| `RawText` | `String` | No | No | — | — |

### Constructor

`Public Sub Constructor(fileName As String, lineNumber As Integer, columnNumber As Integer, severity As BuildSeverity, message As String, code As String = "", rawText As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `fileName` | `String` | `ByVal` | Required |
| `lineNumber` | `Integer` | `ByVal` | Required |
| `columnNumber` | `Integer` | `ByVal` | Required |
| `severity` | `BuildSeverity` | `ByVal` | Required |
| `message` | `String` | `ByVal` | Required |
| `code` | `String` | `ByVal` | `""` |
| `rawText` | `String` | `ByVal` | `""` |

Returns: no value.

### ParseBuoyDiagnostics

`Public Function ParseBuoyDiagnostics(source As String) As Array Of BuildDiagnostic`

Parse the compiler's complete JSON object. Malformed schemas throw; no entries are silently lost.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `source` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Array Of BuildDiagnostic` |

### StripAnsiCSI

`Public Function StripAnsiCSI(source As String) As String`

Strip ANSI CSI sequences, including SGR colors. Other escape families remain untouched.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `source` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `String` |

### ParseBuildLine

`Public Function ParseBuildLine(source As String) As BuildDiagnostic`

Recognize common GCC/Clang/MSVC location lines. Unrecognized text returns Nil.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `source` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `BuildDiagnostic` |

## BuildLogDecoder

`Public Class BuildLogDecoder`

Feed raw bytes from a process stream; each returned string is one complete, color-stripped line.

### Constructor

`Public Sub Constructor(maxLineBytes As Integer = 1048576)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `maxLineBytes` | `Integer` | `ByVal` | `1048576` |

Returns: no value.

### Reset

`Public Sub Reset()`

No parameters.

Returns: no value.

### Feed

`Public Function Feed(chunk As Bytes) As Array Of String`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `chunk` | `Bytes` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Array Of String` |

### Finish

`Public Function Finish() As Array Of String`

Emit a trailing unterminated line; empty trailing buffers emit no line.

No parameters.

#### Returns

| Type |
| --- |
| `Array Of String` |
