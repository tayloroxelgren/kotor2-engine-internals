# Load pipeline: what we learned

Test case: quicksave reload in module 101PER, Steam build, ~170 fps.

## How a reload breaks down

1. **Click to state activation (`work`)**: save read and world restore run as 'P'/'S' packets
   through the resource queue, one stage per coordinator tick.
2. **Finalize and handshake**: `ModuleLoad_FinalizeAndQueueReady`, then the server reaches
   state 2 (`Server_StartGame_State1to2`). A few frames.
3. **Area streaming**: the server sends ~9 object-update messages (~2 KB each) until the
   client acks "area loaded" (P(4,3)). This is the loading-screen tail.
4. **OnEnter and script**: the ack places the player and queues the area's OnEnter script.
5. **Black window**: in 101PER the OnEnter script fades out, waits 2.0 s and fades in over 1.0 s.
   Content, not engine.

## Findings

| Finding | Where | Effect |
|---|---|---|
| The streaming tail is a 200 ms per-client throttle; send work is only ~2 ms | `Server_UpdateClient_Throttle200ms` 0x00537590 | 1.90 s of pure waiting |
| Passing the engine's own `force=1` while the player's area load state is 1 removes the wait | same; `player+0x24 == 1` | stream drops to ~0.5 s when unthrottled |
| Load-bar redraws drain the pending-texture queue, so texture uploads land in the stream window | `LoadingScreenUpdateFrame` -> `TexturePoolCleanupAndRefresh` 0x00427810 -> `Texture_UploadToGL` 0x00433cd0 | ~35-60 ms of uploads per stream gap |
| The hardware-mipmap check fails on every modern driver (`GL_ARB_fragment_program` sets a disqualifying bit) | `GL_CanUseHardwareMipmapGen` 0x00484a60, set up in `GL_DetectExtensions` 0x004317d0 | ~150 ms of CPU mipmapping per reload |
| The black window is an area script, and only 3 of 82 stock modules fade on a save reload | `a_hatchopen.ncs` (101PER), `k_207tel_enter`, `a_261_enter` | scripted, not engine waiting |
| The loading screen is a real per-tick state machine, with no artificial minimum time | `loadingscreen` 0x00533830 | all measured time is real work or waiting on real events |
| GUI rebuild on each load is ~250 ms warm (618 ms cold) | `ModuleChunkLoadCore` 0x007be4c0 | rebuilt from raw GFF on every load |

## Measuring

The mod's profiler (`LOGGING_ENABLED=1`) writes `VisualLoad` (pixel-based timeline),
`LoadPhases` (engine events on the same clock) and `LoadPhaseSamples` (1 ms main-thread stack
samples). `tools/lp_samples.py` groups samples for Ghidra lookup. Engine-side timers alone
miss the ~2.6 s black frame stretch, so trust the pixel timeline.
