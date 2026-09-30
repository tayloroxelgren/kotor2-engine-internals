# kotor2-engine-internals

Notes on the Steam build of *Star Wars: Knights of the Old Republic II*
(`swkotor2.exe`, image base `0x00400000`): identified functions, classes, engine architecture
and the load-time analysis behind the [kotor2loadmod](https://github.com/tayloroxelgren/kotor2loadmod)
`dinput8.dll` proxy.

## Contents

| Path | What it holds |
|---|---|
| [`functions.md`](functions.md) | Table of identified functions: address, name, description, whether the mod's profiler times it |
| [`classes.md`](classes.md), [`classes/`](classes/) | Identified vtables and per-class notes |
| [`architecture/`](architecture/) | Resource manager object graph, client/server packet code paths |
| [`load-pipeline/`](load-pipeline/) | One-page outline of how a load works and where the time goes |
| [`investigations/`](investigations/) | Open bug hunts (e.g. stuck after combat at high fps) |
| [`tools/`](tools/) | Log parsers used on the mod's `kotor2_log.txt` |
| [`data/`](data/) | Reference timing logs |

## Where to start

- How a load works and where the time goes: [`load-pipeline/`](load-pipeline/README.md)
- The engine's root object: [`architecture/resource-manager.md`](architecture/resource-manager.md)
