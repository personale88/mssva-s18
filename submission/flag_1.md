# Flag 1 – Unauthorized Policy Handler Execution

## Invariant
Authorization must be validated before policy handlers execute.

## Telemetry
Instrumentation was added at the policy dispatch boundary to observe
authorization state before handler execution.

Telemetry Points:
- src/policy_dispatch.c : dispatch_policy()
- src/authz_context.c : authz_context_init()
- tools/trace_exec.c : TRACE_POLICY_ENTRY

These points record handler entry with the current authorization state.


## Observation
Observed.

## Security Impact
Unauthorized execution can lead to privilege escalation.

## Limitations
Analysis does not prove exploitability.
