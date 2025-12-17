# Flag 5 – Unobserved Execution Path Activation

## Invariant
All executable paths should be observable through logging or telemetry.

## Telemetry
Execution tracing was used to identify code paths that execute
without producing observable logs.

Telemetry Points:
- src/main.c : process_request()
- src/error_paths.c : handle_error()
- tools/trace_exec.c : TRACE_EXEC_PATH

Certain execution paths completed without emitting telemetry events.


## Observation
Observed.
Certain execution paths were activated without producing
any telemetry or logs.

## Security Impact
Unobserved execution paths reduce detection capability and
can hide malicious or unintended behavior.

## Limitations
Telemetry coverage may be incomplete and could miss
short-lived or rare execution paths.
