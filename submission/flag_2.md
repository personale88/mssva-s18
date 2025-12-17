# Flag 2 – Return Address Integrity Violation

## Invariant
Function return addresses must not be altered during execution.

## Telemetry
Return address integrity was evaluated using execution tracing around
function prologue and epilogue boundaries.

Telemetry Points:
- src/control_flow.c : enter_function()
- src/control_flow.c : exit_function()
- tools/trace_ret.c : TRACE_RETURN

No mismatches between expected and observed return addresses were logged.


## Observation
Not Observed.
No evidence of corrupted or manipulated return addresses was detected
during normal or abnormal execution paths.

## Security Impact
If present, return address corruption could enable control-flow hijacking
and arbitrary code execution.

## Limitations
This observation is limited to exercised paths and may miss
architecture-specific or highly constrained exploits.
