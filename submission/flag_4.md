# Flag 4 – Non-Linear Control Transfer Abuse

## Invariant
setjmp/longjmp control transfers must only occur between properly paired setup and recovery points. The jump buffer (`jmp_buf`) must not be corrupted, and `longjmp()` should only transfer control to legitimate `setjmp()` locations.

## Telemetry
Tracked valid jump targets by recording `jmp_buf` addresses after each `setjmp()` call. Before `longjmp()`, validated that the target buffer is in the registered set. Computed checksums of `recovery_env` contents to detect corruption. Used LD_PRELOAD wrappers for setjmp/longjmp monitoring.

## Observation
**Observed**

- [recovery.c](cci:7://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:0:0-0:0) declares `static jmp_buf recovery_env` in writable memory
- [setup_recovery()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:5:0-7:1) calls `setjmp(recovery_env)` without integrity protection
- [trigger_recovery()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:9:0-11:1) calls `longjmp(recovery_env, 1)` unconditionally
- No validation of `jmp_buf` contents before jumping
- `jmp_buf` contains critical CPU state (registers, stack pointer, instruction pointer)

## Security Impact
Corrupted `jmp_buf` allows attacker to set arbitrary CPU state - effectively a universal control-flow hijack primitive. Enables stack pivot to attacker-controlled memory for ROP chains. Non-linear jumps can bypass security checks and leave program in inconsistent state.

## Limitations
- `jmp_buf` structure is platform-dependent
- Distinguishing legitimate recovery from abuse requires context
- Nested setjmp creates complex valid state space
- Signal-safe variants (sigsetjmp/siglongjmp) add complexity
