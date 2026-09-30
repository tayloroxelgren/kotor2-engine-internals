# Resource manager object graph

`GameMain` allocates the root object with `InitResourceManager`, stores it in `DAT_00a1b4a4`, and the main loop then repeatedly consults its `+0x4`, `+0x8`, and `+0x14` children during loading.

Known root layout:

| Offset | Meaning | Evidence |
|---|---|---|
| `+0x0` | 0x40000-byte root scratch/queue buffer | `InitResourceManager` clears it, then `ResourceRoot_EnsureScratchBuffer` allocates it if null. |
| `+0x4` | `CClientExoApp*` | Allocated as 8 bytes by `InitClientExoApp`; vtable is `CClientExoApp::vftable` at `0x99d684`, child core object at `+0x4`. |
| `+0x8` | Loading/resource manager pointer | Starts null in `InitResourceManager`; later `loadingscreen`, `Engine`, and `EnqueueStreamingRequest` use it heavily. It owns loading-state helpers and an embedded queue at `+0x10040`. |
| `+0xc` | 0x184-byte resource table/cache | Allocated by `FUN_0051b830`, which zeroes 0x60 dwords and clears `+0x180`. |
| `+0x10` | Second 0x184-byte resource table/cache | Same constructor as `+0xc`; likely paired active/secondary resource table. |
| `+0x14` | 0x3c-byte load-state object | Constructed by `FUN_004018c0`; fields drive load state, module indexes, resource names, and completion flag checks. |
| `+0x18` | `GetTickCount()` value | Stored during root construction. |
| `+0x1c` | Temporary graphics/loading flag | `FUN_0040bc40` stores the previous state of flag `2` here and `FUN_0040bcf0` restores/toggles it. |

`CClientExoApp` vtable entries that matter for the queue path:

| Vtable offset | Function | Meaning |
|---|---|---|
| `+0x4` | `FUN_0073f930` | Wrapper around `ResourceQueue_UnpackAndTrace`. This is the generic packet-dispatch target reached from `ProcessResourceQueue` through the queue object's handler pointer. |
| `+0x10` | `FUN_0073f7f0` | Returns `*(client + 0x4) + 0x10`, the queue object that `LoadingScreenUpdateFrame` passes to `ProcessResourceQueue`. |
