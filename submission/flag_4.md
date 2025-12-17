# Flag 4 – Non-Linear Control Transfer Abuse

## Invariant
Control flow should follow valid, intended execution paths.

## Telemetry
Control-flow tracing was enabled to detect unexpected non-linear
execution transfers.

Telemetry Points:
- src/control_flow.c : indirect_jump()
- src/control_flow.c : computed_goto()
- tools/trace_cfg.c : TRACE_JUMP

No invalid or unintended control-flow transfers were observed.


## Observation
Not Observed.
No abnormal non-linear control transfers were detected during execution.

## Security Impact
Abuse of non-linear control flow can bypass checks and violate
program invariants.

## Limitations
Highly input-specific or timing-based control transfers
may not be captured in this analysis.
