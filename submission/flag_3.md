# Flag 3 – Runtime Function Resolution Tampering

## Invariant
Global Offset Table (GOT) entries must point to their intended library functions after dynamic linking. GOT entries for `read()`, `write()`, `socket()`, `printf()` should remain constant throughout execution.

## Telemetry
Recorded baseline GOT entry values after program startup using `dlsym()`. Implemented periodic GOT validation comparing current entries against baseline. Used `readelf -r authz_bridge` to identify relocations and GDB watchpoints on GOT addresses to detect unauthorized modifications.

## Observation
**Observed**

- Makefile shows `LDFLAGS=` (empty)
- Missing `-z relro -z now` linker flags
- GOT remains writable after startup (no RELRO protection)
- Security-critical functions use lazy binding: `read()`, `write()`, `printf()`, `socket()`, `bind()`, `listen()`, `accept()`
- Attacker can overwrite GOT entries to redirect function calls

## Security Impact
Overwriting GOT[write] allows attacker to change authorization responses (DENY→ALLOW). Overwriting GOT[read] enables input manipulation. All calls to hijacked functions are transparently redirected, and the service appears functional while executing malicious logic.

## Limitations
- Full RELRO would make GOT read-only after startup
- ASLR requires address disclosure before exploitation
- Initial lazy binding updates may trigger false positives
- Continuous validation adds performance overhead
