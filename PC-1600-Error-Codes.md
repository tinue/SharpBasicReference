# Sharp PC-1600 Error Code Reference

→ [README](README.md) · [Command Index](PC-1600-Command-Index.md) · [BASIC Reference](PC-1600-BASIC-Reference.md)

When a runtime error occurs the display shows `ERROR n IN linenum` (or just `ERROR n` in direct
mode). Intercept errors with `ON ERROR GOTO`; read the code with `ERN` and the line with `ERL`;
continue with `RESUME`. Source: PC-1600 Operation Manual, Appendix F.

The PC-1600 keeps the PC-1500 codes 1–39 (with minor wording changes) and adds three-digit codes
for its new subsystems. In **MODE 1** with PC-1500 peripherals (CE-150 / CE-158 / CE-162E) the
PC-1500 peripheral codes (40–80) also apply — see the [PC-1500 Error Codes](PC-1500-Error-Codes.md).

Codes shown as links have a note in [Notes on individual codes](#notes-on-individual-codes). The
table keeps the manual's wording; the note explains where that wording is incomplete or wrong.

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

Details and causes: [PC-1600 BASIC Reference, chapter 12](PC-1600-BASIC-Reference.md#errors).

| Code | Cause |
|------|-------|
| **140** | Invalid parameters in `SETCOM` |
| **141** | Not enough free memory for the receive buffer requested with `INIT "COMn:"` (a size outside 80–16383 gives ERROR 19) |
| **142** | Data-reception error on a serial port: parity, overrun, framing, receive buffer full (data lost), or a break received; for `INPUT#`/`LOAD`/`COPY` also the `RCVSTAT` timeout. Reported on the next read. |
| **143** | Timeout: the lines required by `SNDSTAT` did not come on, or no XON followed an XOFF, within the `SNDSTAT` time; for `INPUT` via `SETDEV …,KI` also the `RCVSTAT` timeout |
| **144** | A serial port file is already open (at `OPEN "COMn:"` or `SETDEV`) — `CLOSE` it first |

---

## File errors

| Code | Cause |
|------|-------|
| [**150**](#error-150) | Too many files specified in `MAXFILES` |
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

## Notes on individual codes

<a id="error-150"></a>
### Error 150: no free file buffer

**Manual:** "Too many files specified in `MAXFILES`". This is misleading: a `MAXFILES` value that is
too large (more than 15) gives [error 19](#program-errors), not 150.

**What it actually means:** `OPEN` found no free file buffer. Either all the files that `MAXFILES`
reserved are already open, or `MAXFILES` is 0 (the power-on value). `OPEN` checks this before it
even looks at the file name.

`MAXFILES = n` itself can also raise 150:
- Not enough free memory for n file buffers (313 bytes each).
- `MAXFILES` run inside an unfinished `FOR` loop. The ROM first sets error 109, then overwrites it
  with 150 (confirmed on the emulator).

**Related codes:**
- `MAXFILES` greater than 15 → error 19.
- Changing `MAXFILES` while a file is open → error 154.
- `OPEN … AS #n` with n = 0 or n greater than `MAXFILES` → error 153. With `MAXFILES = 2`,
  `OPEN … AS #3` gives 153, not 150: error 150 is about how many files are open at once, not which
  `#n` you use.

*Source: [NEW ROM bank 3b](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm). `OPEN` ([667DH](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6822)) calls `FREEFCB` ([77BFH](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L9592)),
error set at [6683H](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6830); `MAXFILES` ([6618H](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6741)): out of memory at [664FH](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6780),
`FOR` check at [6636H](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6763) / [6662H](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6791), n > 15 at [6629H](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6756), file open at [6632H](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6761);
`OPEN` file number check at [672EH](https://github.com/tinue/PC-1600-ROM/blob/main/disasm/new/PC1600-P1-B3B.asm#L6918).*

[↑ back to File errors](#file-errors)

---

## See also

- [PC-1600 BASIC Reference](PC-1600-BASIC-Reference.md) — `ON ERROR GOTO`, `ERN`, `ERL`, `RESUME`
- [PC-1500 Error Codes](PC-1500-Error-Codes.md) — codes 40–80 for PC-1500 peripherals in MODE 1
