# Sharp PC-1500 Error Code Reference

When a runtime error occurs during program execution the display shows `ERROR n IN linenum`. In direct (RUN) mode it shows just `ERROR n`. Use `ON ERROR GOTO` to intercept errors; read the code with `ERN` and the line number with `ERL`.

Errors 40–44 require the **CE-150** cassette/printer interface.
Errors 50–69 require the **CE-158** RS-232C/parallel interface.
Errors 70–80 require the **CE-150** printer/plotter interface.

---

## General Errors

| Code | Cause |
|------|-------|
| **1** | Syntax error — missing operand, invalid command name, or command not valid in this context (e.g. `NEW` inside a running program) |
| **2** | `NEXT` without a matching `FOR`, or `RETURN` without a matching `GOSUB` |
| **4** | `READ` ran out of `DATA` values |
| **5** | `DIM` names an array that has already been dimensioned |
| **6** | Array element referenced without a prior `DIM` |
| **7** | Invalid variable name — reserved word used as a variable (`IF`, `TO`, `LN`, `PI`, `LF`, or their `$` variants) |
| **8** | `DIM` specifies more than two dimensions |
| **9** | Array subscript exceeds the size declared in `DIM` |
| **10** | Out of memory — no space to create additional variables |
| **11** | `GOTO`, `GOSUB`, or `RUN` specifies a line number or label that does not exist; or non-existent label in `LLIST` (CE-150) |
| **12** | Invalid `USING` format string |
| **13** | Program exceeds memory capacity, or Reserve key storage is full |
| **14** | `FOR`-`NEXT` loops nested too deeply, or expression parser buffer overflow |
| **15** | `GOSUB` calls nested too deeply, or string buffer overflow during expression parsing |
| **16** | Numeric literal out of range (greater than 1×10¹⁰⁰ or less than −1×10¹⁰⁰), or hex literal exceeds 65535 |
| **17** | Type mismatch — e.g. adding a number to a string |
| **18** | Wrong number of arguments to a function |
| **19** | Numeric value out of permitted range — e.g. `DIM A(256)`, bitwise operand outside −32768 to 32767, or invalid `COLOR` value (CE-150) |
| **20** | `@` or `@$` used without a subscript |
| **21** | A variable is required but a literal was given — e.g. `FOR 1 = 0 TO 10` |
| **22** | No memory available to load a cassette file |
| **23** | Invalid `TIME` value — e.g. month 13, hour 25 |
| **26** | Command not valid in the current mode — e.g. `NEW` entered in RUN mode |
| **27** | `DEF`+key pressed but no labelled program matches, or CE-150 not connected when required; also caused by active SETDEV assignment during CE-150 `TAB` (CE-158) |
| **28** | BASIC keyword or function token embedded inside a string literal, or `INPUT`/`AREAD` used with wrong variable type |
| **30** | Line number entered exceeds 65279 |
| **32** | `INPUT` attempted while the graphic cursor is at columns 152–155 (prompt cannot be displayed) |

---

## Cassette Errors (CE-150)

| Code | Cause |
|------|-------|
| **40** | Invalid expression in a cassette command |
| **41** | `CSAVE`/`CLOAD` targeted the ROM area |
| **42** | Cassette file is too large to load into available memory |
| **43** | `CLOAD?` verification failed — file on tape does not match the loaded program |
| **44** | Checksum error — data read from tape is corrupted |

---

## RS-232C / Parallel Interface Errors (CE-158)

| Code | Cause |
|------|-------|
| **50** | CE-158 power switch is off, or NiCd battery needs recharging |
| **51** | (1) Invalid `SETCOM` parameter; (2) Invalid `SETDEV` assignment; (3) Less than 570 bytes free for `TERMINAL`/`DTE` entry |
| **52** | Error in received data (parity or overrun error) |
| **53** | Invalid `TAB` in `LPRINT` — TAB position exceeds digits/line set by `CONSOLE` |
| **58** | Received data contents are not appropriate |
| **61** | Header mismatch during `CLOAD`, `CLOADr`, `MERGE`, or `INPUT#` execution |
| **65** | Fewer `INPUT` variables than data fields received via RS-232C |
| **67** | (1) No line number at start of `CLOADa`/`MERGEa` line; (2) `CLOADa`/`MERGEa` line exceeded 160 ASCII codes or 80 internal codes; (3) Data before CR exceeded 80 codes in `INPUT` or `INPUTS` |
| **69** | Parallel port BUSY signal held high for more than 10 seconds |

---

## Printer / Plotter Errors (CE-150)

| Code | Cause |
|------|-------|
| **70** | Pen coordinate out of range (valid: −2048 to +2047 for both X and Y) |
| **71** | Paper would back up more than 10.24 cm in TEXT mode |
| **72** | Invalid `TAB` value for the current `CSIZE` setting |
| **73** | Command used in the wrong printer mode (e.g. a GRAPH command in TEXT mode or vice versa) |
| **74** | Too many coordinate pairs in a `LINE` or `RLINE` command (maximum 6 points) |
| **76** | `LPRINT` numeric value too wide to fit on one line at the current `CSIZE` |
| **78** | Pen is being changed, or CE-150 battery is low |
| **79** | Color signal error |
| **80** | CE-150 battery low |

---

## Memory and Input Errors

| Code | Cause |
|------|-------|
| **177–181** | Program and variable data areas overlap — the program has grown into the variable storage area |
| **0, 224–241** | Invalid data entered at an `INPUT` or `AREAD` prompt (e.g. text entered when a number was expected) |

---

## See Also

- [PC-1500 BASIC Reference](README.md) — Core language, `ON ERROR GOTO`, `ERN`, `ERL`, `ERROR`
- [CE-150 Reference](CE-150-Reference.md) — Printer, plotter, and cassette commands
- [CE-158 Reference](CE-158-Reference.md) — RS-232C and parallel interface commands
