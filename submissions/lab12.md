# Lab 12 submissions

### `main.go`:
```
package main

import (
	"fmt"
	"net/http"
	"time"

	spinhttp "github.com/spinframework/spin-go-sdk/v2/http"
)

func init() {
	spinhttp.Handle(func(w http.ResponseWriter, r *http.Request) {
		moscowTime := time.Now().UTC().Add(3 * time.Hour)

		w.Header().Set("Content-Type", "application/json")
		w.WriteHeader(http.StatusOK)

		fmt.Fprintf(w, `{"unix": %d, "iso": %q, "hour_minute": %q}`,
			moscowTime.Unix(),
			moscowTime.Format(time.RFC3339),
			moscowTime.Format("15:04"))
	})
}

func main() {}
```

### `spin.toml`:
```
#:schema https://schemas.spinframework.dev/spin/manifest-v2/latest.json

spin_manifest_version = 2

[application]
name = "moscow-time"
version = "0.1.0"
authors = ["long1tail <m.shulaev@innopolis.university>"]
description = ""

[[trigger.http]]
route = "/time"
component = "moscow-time"

[component.moscow-time]
source = "main.wasm"
allowed_outbound_hosts = []
[component.moscow-time.build]
command = "tinygo build -target=wasip1 -buildmode=c-shared -gc=leaking -no-debug -o main.wasm main.go"
```

### `spin build`:
```
Building component moscow-time with `tinygo build -target=wasip1 -buildmode=c-shared -gc=leaking -no-debug -o main.wasm main.go`
Finished building all Spin components
```

### `curl -s http://127.0.0.1:3000/time | python3 -m json.tool`:
```
{
    "unix": 1784152724,
    "iso": "2026-07-15T21:58:44Z",
    "hour_minute": "21:58"
}
```

| Dimention | Lab 6 Docker | Lab 12 WASM/Spin |
|-----------|--------------|------------------|
| Artifact size | 299K | 14.8MB |
| Cold start (p50) | 0.125s | 5.4 ms ±   0.5 ms |
| Warm latency p50 | 5.1 ms ±   0.4 ms | 5.2 ms ±   0.4 ms |
| Warm latency p95 | 5.2 ms ±   0.5 ms | 5.3 ms ±   0.3 ms |

- a. `js/wasm` targets the browser and relies on Javascript environment bindings (DOM, Web APIs). `wasip1` targets the server using WASI (WebAssembly System Interface), replacing JS APIs with secure, standardized syscall-like capabilities to interact with the host OS (files, clocks, random numbers) without needing a browser engine.

- b. Spin uses the Component Model/WASI-HTTP. It expects the WASM module to export specific C-style function pointers (like memory allocators and HTTP handlers) so the Spin host can invoke them. Without it, TinyGo builds a standalone executable with a `_start` entry point, which Spin cannot hook into.

- c. Docker uses Linux network namespaces to isolate network stacks at the OS level. WASI uses capability-based security. The WASM module literally does not possess the function handles (capabilities) to open a socket unless the runtime explicitly passes that specific capability to the module upon instantiation.

-d. TinyGo heavily restricts the `reflect` package, making standard `encoding/json` serialization of dynamic types like `map[string]any` impossible. It also does not embed the large `tzdata` database, making functions like `time.LoadLocation("Europe/Moscow")` fail.

-e. For Docker, cold start is dominated by kernel operations: setting up Linux namespaces (network, PID), cgroups, and mounting overlayfs layers. For Spin, cold start is dominated by the WASM runtime (like Wasmtime) instantiating the engine, verifying the WASM bytecode, and loading it into memory.

-f. WASM is significantly better for highly-dense, event-driven, scale-to-zero workloads (like Edge functions or serverless) due to its near-instant cold start and tiny memory footprint. Docker is still right for legacy applications, heavy frameworks, daemon processes, and apps requiring deep POSIX compliance or multi-threading.

-g. WASM makes OS-level sandbox escapes (like kernel exploits, dirty pipe, or namespace breakout attacks) much harder because the WASM code never interacts with the Linux kernel. It runs inside an isolated virtual machine, communicating only through tightly controlled WASI interfaces.