# libc

Generated LLGo bindings for 50 commonly used C library headers, covering files, processes, signals, sockets, threads, and memory mapping. The selection uses common C/POSIX headers and includes the widely used `flock()` extension.

| Target | Header baseline | Generated files |
| --- | --- | --- |
| Linux amd64 | Bootlin glibc 2.44 | 181 |
| macOS arm64 | macOS 14.0 SDK | 187 |

The Linux bindings target glibc. Each platform's Go files have explicit build constraints.

## Build

Use LLGo:

```sh
llgo build ./...
llgo test -v ./...
```

Both targets passed these commands. There are currently no runtime test cases.

The module path is `github.com/MeteorsLiu/libc`.

## Coverage

This is a partial binding. `Flock` and `Wait` are included. The locking structure is named `FlockT`; macOS's legacy wait union is named `WaitStatus`.

Unsupported or conflicting declarations are skipped, including `stat`, `sigaction`, some string functions, variadic-list APIs, inline functions, and macOS Blocks APIs. See the [header selection](_generate/headers.json) and [complete skip records](_generate/skipped.json).

## Generation inputs

The bindings were generated with llcppg commit `0204f71b7441956a16511c0389dae8c8a2234060`.

- [Linux configuration](_generate/linux_amd64/llcppg.cfg)
- [macOS configuration](_generate/darwin_arm64/llcppg.cfg)

Each configuration uses a captured `preprocessed.h` input. The neighboring `libc.h` lists the selected headers, and `preparation.json` records the preprocessing command and adjustments. The snapshots rename the colliding C type tags for `FlockT` and `WaitStatus` while preserving function names and signatures; unavailable global declarations are omitted.

The configuration and snapshot line markers currently contain the SDK/compiler paths used during generation. Regeneration on another machine requires rebuilding the snapshots and updating those paths. macOS preprocessing disables Blocks; `glob_t` size, alignment, and C callback field offsets were verified unchanged.

The root `llcppg.pub` combines the platform type mappings for use by dependent bindings.
