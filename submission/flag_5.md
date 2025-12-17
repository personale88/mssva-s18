# Flag 5 – Unobserved Execution Path Activation

## Invariant
Only expected code paths should execute during normal operation. Functions defined but never called during normal execution (dead code) should remain dormant. Unexpected activation indicates control-flow manipulation.

## Telemetry
Compiled with `-finstrument-functions` to track all executed functions. Built execution profile during normal operation to identify expected call graph. Set GDB breakpoints on dormant functions to detect unexpected activation. Compared actual execution against expected [main()→init_policies()→start_server()→get_policy()→policy_fn()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/main.c:6:0-10:1) flow.

## Observation
**Observed**

- [recovery.c](cci:7://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:0:0-0:0) defines [setup_recovery()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:5:0-7:1) and [trigger_recovery()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:9:0-11:1)
- Neither function is called from [main.c](cci:7://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/main.c:0:0-0:0), [server.c](cci:7://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/server.c:0:0-0:0), or any other source file
- Makefile includes [src/recovery.c](cci:7://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:0:0-0:0) in build: functions exist in binary
- Normal execution flow never reaches these functions
- Dead code provides pre-existing exploitation primitives (longjmp capability)

## Security Impact
Attackers can leverage existing dead code instead of injecting new code ("living-off-the-land"). The dormant [trigger_recovery()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/recovery.c:9:0-11:1) provides a dangerous `longjmp` primitive that bypasses normal control flow. Dead code also provides additional ROP/JOP gadgets for exploitation chains.

## Limitations
- Some code paths may execute only under rare error conditions
- Library functions have their own unreachable code
- Complete path analysis requires symbolic execution
- Compiler optimization levels affect dead code presence
