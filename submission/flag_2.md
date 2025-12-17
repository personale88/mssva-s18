# Flag 2 – Return Address Integrity Violation

## Invariant
Function return addresses on the stack must remain unmodified between function entry and exit. The return address pushed during function call must match the address used when returning.

## Telemetry
Implemented shadow stack using `-finstrument-functions` compiler flag to hook function entry/exit. At entry, recorded return address via `__builtin_return_address(0)`. At exit, compared actual return address against recorded value. Used GDB watchpoints on stack frame return addresses during execution.

## Observation
**Observed**

- Makefile shows `CFLAGS=-Wall -Wextra -fno-omit-frame-pointer -O0 -g`
- Missing `-fstack-protector-strong` (no stack canaries)
- Missing `-fcf-protection` (no control-flow enforcement)
- `read()` in `server.c:25` reads directly into stack-allocated `authz_request_t`
- Frame pointer preserved but no return address protection

## Security Impact
Attackers can overwrite return addresses via stack buffer overflow to hijack control flow. This enables ROP/JOP attacks that bypass DEP/NX protections, allowing arbitrary code execution without injecting new code.

## Limitations
- Shadow stack adds performance overhead
- Only monitors instrumented functions, not libc
- Multi-threaded execution requires thread-local shadow stacks
- Some legitimate patterns (exception handling) may modify return addresses

