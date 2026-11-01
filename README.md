# Decode compiler diagnostics and streaming build logs

Build output support for Buoy IDEs: typed Problems-panel diagnostics, common
compiler location parsing, and bounded byte-stream log decoding. Pure Buoy,
without native dependencies or shell execution.

Copy `buildkit-0.1.0.buipkg` into a project's `packages/`, then `Import buildkit`.
See [the console example](examples/buildkit_demo.bui).

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add buildkit --git https://github.com/BuoyBay/buildkit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer buildkit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```

The generated agent files and editor settings stay local and are ignored by Git.

## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
