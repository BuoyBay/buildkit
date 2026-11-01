# BuildKit

Build output support for Buoy IDEs: typed Problems-panel diagnostics, common
compiler location parsing, and bounded byte-stream log decoding. Pure Buoy,
without native dependencies or shell execution.

Copy `buildkit-0.1.0.buipkg` into a project's `packages/`, then `Import buildkit`.
See [the console example](../examples/buildkit_demo.bui).

## API

| API | Purpose |
| --- | --- |
| `ParseBuoyDiagnostics(source)` | Parse the complete `buoy --check --diagnostic-format json` object into `Array Of BuildDiagnostic` |
| `ParseBuildLine(source)` | Recognize GCC/Clang `file:line[:column]: severity: message` and MSVC `file(line[,column]): severity [code]: message`; return `Nil` for other log lines |
| `StripAnsiCSI(source)` | Remove ANSI CSI sequences, including SGR colors; preserve other escape families |
| `BuildLogDecoder(maxLineBytes = 1048576)` | Buffer raw process bytes into UTF-8 lines, handling fragmented UTF-8, split CRLF, lone CR/LF and fragmented CSI color sequences |

`BuildDiagnostic` holds `FileName`, `Line`, `Character`, `Severity`, `Message`,
`Code`, and `RawText`. Severity is `BuildSeverity.Error`, `.Warning`, or `.Note`;
`fatal error` maps to `.Error`. `RawText` is the original colored text for a
single-line parser, or the serialized JSON entry for the Buoy parser.

Coordinates are **preserved exactly as emitted by the compiler**. There is no
implicit base conversion and no assumed UTF-8/UTF-16 mapping. Buoy currently
emits a zero character for some semantic diagnostics. Absent columns in text
logs become zero; zero coordinates therefore do not establish whether a compiler
used a zero-based coordinate or omitted that coordinate. Convert positions only
when the consuming tool knows the specific compiler's coordinate convention.

Buoy JSON must contain `errors` and `warnings` arrays, and each entry must have
string `file`/`message` plus nonnegative integral `line`/`character` fields.
Optional string `code` is retained. Coordinates are bounded to 2,147,483,647.
Malformed JSON throws `JSONException`; malformed schema throws
`InvalidArgumentException`. Entries are ordered errors first, then warnings,
matching the compiler's groups. Relative paths remain relative.

`BuildLogDecoder.Feed(chunk As Bytes)` returns complete color-stripped lines as
`Array Of String`. Newline characters are omitted; empty lines remain. `Finish()`
emits one trailing unterminated line if present, and does not duplicate it on
subsequent calls. Call `Reset()` to start a new stream.

The per-line byte cap includes escape sequences and excludes line terminators.
Exceeding it throws `InvalidArgumentException`; invalid UTF-8 throws
`EncodingException` once a complete line or trailing buffer is decoded. Both
poison the decoder until `Reset()` is called. If a call fails, its returned
lines are unavailable; previously returned lines remain valid. The decoder
retains at most one incomplete line; callers should feed bounded chunks and
consume returned lines promptly.

## Scope

Text formats are conventional compiler location lines, not a universal build-log
parser. Include stacks, caret source snippets, linker messages without file
coordinates, multi-line diagnostics, MSBuild's leading project prefixes, ANSI
OSC hyperlinks and terminal cursor emulation are not interpreted. `StripAnsiCSI`
drops an incomplete trailing CSI sequence; unsupported escape families remain
in the text. BuildLogDecoder treats lone CR as a line ending, rather than an
in-place progress-line update. No process launch, transport, compiler invocation,
path resolution or editor navigation occurs implicitly.

## Verification


Maintainers run the private validation suite before release. See the repository README for public build instructions.

