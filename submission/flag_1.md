# Flag 1 – Unauthorized Policy Handler Execution

## Invariant
Only authorized policy functions ([default_policy](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/policies/default_policy.c:2:0-6:1), [audit_policy](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/audit.c:3:0-8:1)) should be invoked through the `policy_table[]` dispatch mechanism. Function pointers in the policy table must point to legitimate handler addresses.

## Telemetry
Instrumented the `policy_table[]` array to monitor stored function pointer values. Used LD_PRELOAD to wrap [get_policy()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/policy.c:15:0-19:1) and validate that returned function pointers match known policy handler addresses before execution. Set GDB watchpoints on policy_table memory locations to detect unauthorized writes.

## Observation
**Observed**

- The `policy_table[3]` array in [policy.c](cci:7://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/policy.c:0:0-0:0) stores function pointers in writable `.data` segment
- [get_policy()](cci:1://file:///c:/boot%20camp/mssva-s18/mssva-s18/authz_bridge/src/policy.c:15:0-19:1) returns pointers without validating they point to legitimate handlers
- `server.c:28` calls `fn(&req)` without verifying function pointer integrity
- No memory protection prevents overwriting policy_table entries

## Security Impact
An attacker with arbitrary write capability can overwrite `policy_table` entries to redirect authorization decisions to malicious code. This enables complete authorization bypass (always ALLOW) or arbitrary code execution while the service continues running normally without crashes.

## Limitations
- Static analysis cannot detect runtime memory corruption
- ASLR may require address leak before exploitation
- TOCTOU attacks may occur between validation and use
- Deep call chain validation not performed
