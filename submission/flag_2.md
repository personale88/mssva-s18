# Flag 2 – Return Address Integrity Violation

## Invariant
Function return addresses must not be altered during execution.

## Telemetry
Runtime execution was observed around function call and return boundaries
to detect unexpected control-flow changes.

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
