# Sharp PC-1600 Error Code Reference

When a runtime error occurs the display shows `ERROR n IN linenum` (or just `ERROR n` in direct
mode). Intercept errors with `ON ERROR GOTO`; read the code with `ERN` and the line with `ERL`;
continue with `RESUME`. Source: PC-1600 Operation Manual, Appendix F.

The PC-1600 keeps the PC-1500 codes 1–39 (with minor wording changes) and adds three-digit codes
for its new subsystems. In **MODE 1** with PC-1500 peripherals (CE-150 / CE-158 / CE-162E) the
PC-1500 peripheral codes (40–80) also apply — see the [PC-1500 Error Codes](Error-Codes.md).

---

## Program errors

| Code | Cause |
|------|-------|
| **1** | Syntax error — statement breaks Sharp BASIC rules; program command used in direct mode or vice versa with PC-1500 peripherals in MODE 1; line number exceeds 65279 |
| **2** | `NEXT` without `FOR` — missing/improperly nested loop, `NEXT` variable ≠ `FOR` variable, or two `NEXT` for one `FOR` |
| **4** | `READ` without `DATA`, or no more data items left to read |
| **5** | Array variable declared twice |
| **6** | Undeclared array variable — used without a `DIM` |
| **7** | Type mismatch — numeric value assigned to a string variable or vice versa |
| **8** | More than 2 dimensions declared for an array |
| **9** | Subscript out of the range declared in `DIM` |
| **10** | Memory variable area full — no room for more variables |
| **11** | Undefined line number in `GOTO`, `ON..GOSUB` or `ON..GOTO` |
| **12** | Incorrect format string in `USING` |
| **13** | Memory full — not enough user memory for program size or string-variable storage |
| **14** | Stack full — `FOR`/`NEXT` nested too deeply, or stack full while computing a function |
| **15** | Stack full — subroutines nested too deeply; or string length longer than 80 bytes |
| **16** | Exponent greater than 99 |
| **17** | Illegal data types in an arithmetic statement — arithmetic on a string variable |
| **18** | Incorrect function parameters |
| **19** | Parameter out of range |
| **20** | Incorrect fixed-variable name |
| **21** | Numeric expression used where a variable is required |
| **22** | Memory full — can't load program |
| **23** | Incorrect time specification in a `TIME` string |
| **24** | Attempt to save, list or edit a program protected with `PASS` |
| **25** | Invalid address specified with `NEW` |
| **26** | Invalid mode — command cannot execute in the current mode |
| **27** | Illegal command — the peripheral addressed by this command is not connected |
| **28** | `INPUT`/`AREAD` received illegal string data duplicating a command or function name |
| **30** | Line number exceeds 65539 when used in MODE 1 with PC-1500 peripherals |
| **32** | `GCURSOR` position prevents display of data entered for the current `INPUT` |
| **35** | PC-1500 peripheral not connected |
| **36** | Cannot display data as specified by the `USING` format string |
| **37** | Overflow — result of computation exceeds 9.999999999E99 |
| **38** | Division by zero, or undefined variable used as a divisor |
| **39** | Illegal function call — negative argument to `SQR` or `LN`; non-integer where an integer is required |

---

## System / editing errors (100–131)

| Code | Cause |
|------|-------|
| **100** | Renumbering unsuccessful with `RENUM` |
| **101** | Invalid device name in `TITLE` or `NEW` |
| **102** | Invalid device selection — device not connected |
| **103** | RAM module full — `INIT` parameters cannot be set |
| **104** | More than eight software interrupts (`ON..GOSUB` statements) specified |
| **105** | Too many software-interrupt levels |
| **106** | `RETI` with no corresponding `ON..GOSUB` |
| **107** | `RESUME` with no corresponding `ON ERROR GOSUB` |
| **108** | Password cannot be cleared |
| **109** | Illegal variable name in an `ERASE` statement |
| **110** | Cannot set MODE 1 (PC-1500 mode); command invalid in MODE 0 — PC-1500-peripheral commands work only in MODE 1 |
| **111** | Invalid mode on the PC-1600 |
| **112** | Line too long — logical line exceeds 80 characters during editing |
| **131** | String data preceded by a `+` or `-` sign |

---

## Cassette tape errors

| Code | Cause |
|------|-------|
| **40** | Syntax error in a tape command |
| **42** | Insufficient memory to load the program |
| **43** | `CLOAD?` verification failed (possible tape misread); or `INPUT#` type mismatch (string ↔ numeric) |
| **44** | Tape error — incompatible recorder, read error, or tone/volume needs adjustment |

---

## Printer errors

| Code | Cause |
|------|-------|
| **70** | Pen outside the range −2048 to 2047 for X or Y |
| **71** | Reverse form feed more than 10.24 cm in TEXT mode (CE-150 only) |
| **72** | Incorrect `TAB` parameter |
| **73** | Illegal command in the current mode — graphics command in TEXT mode or vice versa |
| **74** | Too many parameters in `LLINE` / `RLINE` |
| **76** | Output line longer than the length set with `PCONSOLE`, or a numeric result too long for one line in TEXT mode |
| **78** | Cannot execute `LPRINT`/`LLINE` — pen not in place, or printer locked by low battery |
| **79** | Colour signals not output to the printer (CE-150 only) |
| **80** | Low battery — printer locked |

---

## Serial I/O port errors (`COM1:` and `COM2:`)

| Code | Cause |
|------|-------|
| **140** | Invalid parameters in `SETCOM` |
| **141** | Receive buffer in `INIT` too large (> 16383 bytes or > available memory) |
| **142** | Data-reception error on a serial port (parity, overrun, framing, or receive-buffer-full) |
| **143** | Timeout — no response within the value set by `RCVSTAT` |
| **144** | Serial port in `SETDEV` is already open |

---

## File errors

| Code | Cause |
|------|-------|
| **150** | Too many files specified in `MAXFILES` |
| **151** | File already exists — use another name |
| **152** | File not found; or disk full when saving with `SAVE` |
| **153** | Incorrect file number — file with that number is not open |
| **154** | File already open — close and reopen in the new mode |
| **155** | Illegal drive name, or drive not connected |
| **156** | Incorrect parameter in a `SET` statement |
| **157** | Illegal file name or incorrect specification |
| **158** | Command not supported for the disk drive |
| **159** | Attempt to write to a write-protected disk |
| **160** | No disk in the specified drive |
| **161** | Disk not formatted with `INIT` |
| **162** | Disk read or write error |
| **163** | Wrong disk in drive — disks changed while a file was open |
| **164** | Disk full |
| **165** | End of file reached with `INPUT#` — all data read |
| **166** | Not enough free memory for the disk drive's internal work area |
| **167** | Fatal disk error — contents destroyed or corrupted |
| **168** | CE-1600F Disk Drive not functioning — low battery or hardware fault |

---

## See also
- [PC-1600 BASIC Reference](PC-1600-BASIC-Reference.md)
- [PC-1500 Error Codes](Error-Codes.md) — codes 40–80 for PC-1500 peripherals in MODE 1
