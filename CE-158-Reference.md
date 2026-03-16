# Sharp CE-158 Interface Reference

## Overview

The **Sharp CE-158** is an optional interface module for the PC-1500. It provides an **RS-232C serial port** and a **parallel printer port**, along with a set of new BASIC statements and functions for controlling them.

The CE-158 has its own rechargeable Ni-Cad battery. Its power switch must be **ON** before executing any CE-158 command; executing a CE-158 command with the power off causes **ERROR 50**. The battery lasts approximately 3 hours; charge with the EA-21A AC adaptor (15 hours to full charge).

The CE-158 must also be physically connected to the PC-1500 when programs using CE-158 commands are **created** or **executed**.

→ [Command Index](Command-Index.md) · [Error Code Reference](Error-Codes.md)

### RS-232C Specifications

- **Transmission**: Asynchronous
- **Baud rates**: 50, 100, 110, 200, 300, 600, 1200, 2400 bps
- **Data bits**: 5, 6, 7, 8
- **Parity**: N (none), E (even), O (odd)
- **Stop bits**: 1 or 2 (actual 1.5 when data bits = 5)
- **Signals**: TD, RD (data); RTS, DTR (outputs); DSR, CTS, CD (inputs); SG (ground)
- **Connector**: DB-25W

### Parallel Port Specifications

- **Output**: 8-bit parallel, ASCII, BUSY handshake, TTL level
- **Applicable statements**: LPRINT, LLIST, FEED, CONSOLE, PRINT#-9,
- **Connector**: DB-25M (use EA-158C cable to connect to Centronics 36-pin printers)

### Initial State after Power-On

| Item | Value | Notes |
|------|-------|-------|
| SETCOM | 300, 8, N, 1 | |
| SETDEV | (cleared) | All assignments cleared |
| OUTSTAT | 3 | DTR and RTS = OFF (MARK) |
| CONSOLE (RS-232C) | 0, 0 | Unlimited digits/line, CR end code |
| CONSOLE (parallel) | 80, 1 | 80 digits/line, LF end code |
| ZONE | 13 | |
| TAB | 0 | |

### Handshake Behaviour

RS-232C I/O command execution suspends while any of the input signals **DSR, CD, or CTS** is in the MARK state (logic 1 = low voltage). Execution resumes when all three go to SPACE (logic 0 = high voltage). Check signal state with INSTAT before executing I/O.

**RTS and DTR are OFF at power-on.** Use `OUTSTAT 0` to assert both before starting data transfer.

---

## Configuration

### SETCOM

- **Syntax**: `SETCOM [BR [,WL [,PR [,ST]]]]`
- **Syntax**: `SETCOM nonnumeric-variable`

Sets communication parameters: baud rate (BR), word length (WL), parity (PR), and stop bits (ST). `SETCOM` with no arguments resets all parameters to the power-on defaults (300, 8, N, 1).

| Parameter | Values |
|-----------|--------|
| BR (baud rate) | 50, 100, 110, 200, 300, 600, 1200, 2400 |
| WL (word length) | 5, 6, 7, 8 |
| PR (parity) | N (none), E (even), O (odd) |
| ST (stop bits) | 1, 2 (actual 1.5 when WL = 5) |

Omitting a parameter leaves it unchanged. You can pass all four values as a nonnumeric variable string in the form `"BR,WL,PR,ST"`.

```
10 SETCOM 1200,,E,2      : REM 1200 baud, 8 bits (unchanged), even, 2 stop
20 A$="300,7,E,1"
30 SETCOM A$             : REM set from string variable
40 SETCOM                : REM reset to defaults (300,8,N,1)
```

See also: `COM$`, `SETDEV`

---

### SETDEV

- **Syntax**: `SETDEV [KI][,DO][,PO][,CI][,CO]`
- **Syntax**: `SETDEV nonnumeric-variable`

Assigns which BASIC I/O commands route through the RS-232C port. Each assignment is a keyword; any combination may be given in any order.

| Assignment | Direction | Commands affected |
|------------|-----------|-------------------|
| KI | Input | INPUT, INPUTS, INPUT% |
| DO | Output | PRINT |
| PO | Output | LPRINT, LLIST |
| CI | Input | CLOAD, CLOADr, INPUT#, MERGE |
| CO | Output | CSAVE, CSAVEa, CSAVEr, PRINT# |

`SETDEV` alone (or with a null nonnumeric variable) clears all assignments. Each new SETDEV call **replaces** all previous assignments entirely.

> **Note**: Clear all SETDEV assignments before using the CE-150 TAB feature; an active SETDEV assignment causes **ERROR 27** when `TAB expression` is executed.

```
10 SETDEV DO,CI          : REM PRINT→RS-232C, CLOAD←RS-232C
20 SETDEV PO             : REM LPRINT/LLIST→RS-232C
30 SETDEV               : REM clear all assignments
```

See also: `DEV$`, `OPN`

---

### OUTSTAT

- **Syntax**: `OUTSTAT expression`

Sets the RS-232C output handshake signals RTS and DTR. The expression value controls the two low-order bits:

| Bit | Signal | 0 (SPACE = ON = high) | 1 (MARK = OFF = low) |
|-----|--------|-----------------------|----------------------|
| 2¹ | RTS | RTS active | RTS inactive |
| 2⁰ | DTR | DTR active | DTR inactive |

Both RTS and DTR are MARK (inactive) at power-on. Execute `OUTSTAT 0` to activate both before data transfer.

The higher-order bits (2² through 2⁴) reflect the state of the input signals CTS, CD, and DSR respectively (same encoding as INSTAT).

```
10 OUTSTAT 0             : REM activate both RTS and DTR
20 OUTSTAT 3             : REM deactivate both (MARK state)
```

See also: `INSTAT`

---

### CONSOLE

- **Syntax**: `CONSOLE n`
- **Syntax**: `CONSOLE n, type`
- **Syntax**: `CONSOLE n, type, type2`

Specifies the line width and end code for RS-232C output (LPRINT, PRINT, LLIST, FEED).

- `n`: digits per line. 0 = unlimited; 16–255 = fixed width.
- `type` (expression 2): 0 = CR, 1 = LF (selects the end code).
- `type2` (expression 3): combined with `type` to produce two-character end codes:

| type | type2 | End code |
|------|-------|----------|
| 0 | 0 | CR + CR |
| 0 | 1 | CR + LF |
| 1 | 0 | LF + CR |
| 1 | 1 | LF + LF |

Default at power-on: 0, 0 (unlimited, CR).

When CONSOLE is used after `OPN "LPRT"`, it controls the parallel port instead (default: 80, 1).

```
10 CONSOLE 80, 0, 1      : REM 80-column output with CR+LF
20 CONSOLE 0             : REM unlimited line length
```

See also: `ZONE`, `FEED`, `LPRINT`

---

### ZONE

- **Syntax**: `ZONE n`

Sets the column block width (1–31) used by comma-separated items in LPRINT. When a comma separates expressions in LPRINT, output is padded with spaces to the next zone boundary. Default at power-on: 13.

```
10 ZONE 16
20 LPRINT "A","B","C"    : REM A at col 0, B at col 16, C at col 32
```

See also: `LPRINT`, `CONSOLE`

---

## I/O Statements and Functions

### INSTAT

- **Syntax**: `n = INSTAT`

Returns the current state of the RS-232C handshake signals as an integer (0–31). Bit encoding:

| Bit | Signal | I/O | 0 = SPACE (high) | 1 = MARK (low) |
|-----|--------|-----|------------------|----------------|
| 2⁴ | DSR | In | Ready | Not ready |
| 2³ | CD | In | Carrier | No carrier |
| 2² | CTS | In | Clear to send | Not clear |
| 2¹ | RTS | Out | Active | Inactive |
| 2⁰ | DTR | Out | Active | Inactive |

RS-232C I/O execution suspends while any of DSR, CD, or CTS bits is 1 (MARK). Resumes when all three return to 0 (SPACE).

```
10 OUTSTAT 0
20 IF INSTAT AND 4 THEN GOTO 20   : REM wait until CTS active (bit 2=0)
30 LPRINT A$
```

See also: `OUTSTAT`

---

### INPUT (RS-232C)

- **Syntax**: `INPUT [prompt;] var [, var ...]`

Effective only when KI has been declared by SETDEV. Receives ASCII data from the RS-232C port and assigns it to the variables. A `?` prompt (or the specified prompt string) is shown on the display. Data is read until a CR code is received (maximum 80 digits per field). A comma in the received data is treated as a field separator.

If fewer variables are listed than data fields received, **ERROR 65** occurs. If more, excess data is discarded and the last value is shown on the display.

> **Note**: Double-quote `"` and comma `,` cannot be received into nonnumeric variables (ERROR 65). If the first byte from the RS-232C port is CR, that program line is skipped.

```
10 SETDEV KI
20 OUTSTAT 0
30 INPUT "VALUE=";A
```

See also: `INPUTS`, `INPUT%`, `SETDEV`, `RINKEY$`

---

### INPUTS

- **Syntax**: `INPUTS [prompt;] var [, var ...]`

Same as INPUT but received data is **not** translated to intermediate computer language. Use INPUTS when the data may contain strings that would otherwise be parsed as BASIC keywords or function names (e.g., the string `SIN30` would be evaluated by INPUT but passed literally by INPUTS).

See also: `INPUT`, `INPUT%`

---

### INPUT%

- **Syntax**: `INPUT% A$(*)`

Effective only when KI declared by SETDEV. Receives data into the specified character array, clearing the array first. Terminates when a CR code is received or the array is full.

```
10 SETDEV KI
20 OUTSTAT 0
30 CLEAR : DIM A$(3)*10
40 INPUT% A$(*)
```

See also: `INPUT`, `INPUTS`

---

### INPUT#-8,

- **Syntax**: `INPUT#-8, variable`
- **Syntax**: `INPUT#-8,$ variable`
- **Syntax**: `INPUT#-8,9 A$(*)`

Receives RS-232C data without requiring a prior SETDEV KI declaration. Equivalent to:

```
INPUT#-8,  →  SETDEV KI : INPUT
INPUT#-8,$ →  SETDEV KI : INPUTS
INPUT#-8,9 →  SETDEV KI : INPUT%
```

See also: `INPUT`, `SETDEV`

---

### PRINT (RS-232C)

- **Syntax**: `PRINT [expression [; expression ...]]`

Effective only when DO declared by SETDEV. Sends data in ASCII via RS-232C. Differences from LPRINT:

1. USING format is not supported.
2. TAB is not permitted.
3. Both comma and semicolon behave as semicolons (no zone padding).
4. Cannot end with a comma.
5. Sign code is not prefixed to positive numeric values.

See also: `LPRINT`, `SETDEV`

---

### LPRINT (RS-232C)

- **Syntax**: `LPRINT [expression [; expression ...]]`
- **Syntax**: `LPRINT [expression [, expression ...]]`
- **Syntax**: `LPRINT USING "fmt"; expression ...`
- **Syntax**: `LPRINT TAB(n); expression`
- **Syntax**: `LPRINT`

Effective only when PO declared by SETDEV (otherwise executes to CE-150 or causes ERROR 27). Sends data in ASCII via RS-232C.

- **Semicolon** between items: data sent consecutively, no end code between them. A trailing semicolon suppresses the final end code.
- **Comma** between items: pads with spaces to the next zone boundary (see ZONE).
- **USING**: format specifier supported.
- **TAB(n)**: pads with spaces to column n, then outputs next item.
- **LPRINT** alone: sends only the end code.

The end code character is defined by CONSOLE (default: CR).

> **Note**: To send a NUL byte, use `LPRINT CHR$(0)`.

```
10 SETDEV PO
20 OUTSTAT 0
30 CONSOLE 80,0,1
40 ZONE 16
50 LPRINT "A","B","C"    : REM zone-aligned columns
60 LPRINT USING "###.#";X
70 LPRINT TAB(10);"X="
```

See also: `PRINT`, `LLIST`, `ZONE`, `CONSOLE`, `SETDEV`

---

### LLIST (RS-232C)

- **Syntax**: `LLIST`
- **Syntax**: `LLIST n`
- **Syntax**: `LLIST m, n`
- **Syntax**: `LLIST , n`
- **Syntax**: `LLIST n,`

Effective only when PO declared by SETDEV. Sends program lines via RS-232C in ASCII. Line numbers are right-justified in a 5-character field; a space is appended before the line text. Range syntax is the same as LIST.

```
10 SETDEV PO
20 OUTSTAT 0
30 LLIST              : REM send entire program
40 LLIST 100,200      : REM send lines 100 to 200
```

See also: `LPRINT`, `SETDEV`

---

### PRINT#-8,

- **Syntax**: `PRINT#-8, expression`

Sends output to the RS-232C port without requiring a prior SETDEV PO declaration. Equivalent to `SETDEV PO : LPRINT expression`.

See also: `LPRINT`, `SETDEV`

---

### RINKEY$

- **Syntax**: `var$ = RINKEY$`

Returns the last one byte that was present on the RS-232C port immediately before this function executes. Returns NUL (CHR$(0)) if no byte was received. Non-blocking — does not wait for input.

```
10 OUTSTAT 0
20 WAIT 0
30 A$=RINKEY$
40 IF A$ THEN PRINT A$;
50 GOTO 30
```

See also: `INSTAT`, `INPUT`

---

### TRANSMIT

- **Syntax**: `TRANSMIT BREAK, n`

Sends a LONG SPACE (continuous space signal, used as padding) on the RS-232C port. `n` is 1–255; the duration is approximately `INT(n)/64` seconds.

```
10 TRANSMIT BREAK, 10   : REM send ~156ms of LONG SPACE
```

---

### FEED

- **Syntax**: `FEED`
- **Syntax**: `FEED n`

Sends one end code (or `n` end codes, where n = 1–65535) via RS-232C. The end code character is defined by CONSOLE (default: CR).

> **Note**: If the previous output statement already ended with an end code, the first FEED end code will be replaced by a space followed by an end code.

See also: `CONSOLE`, `LPRINT`

---

## Program Transfer

### CSAVE (RS-232C)

- **Syntax**: `CSAVE ["filename" [; range]]`

Effective only when CO declared by SETDEV. Sends the program in internal (binary) code via RS-232C with a header. In RESERVE mode, sends the reserve program. Range syntax is the same as regular CSAVE.

Word length must be 8 bits (set with SETCOM).

See also: `CSAVEa`, `CSAVEr`, `CLOAD`

---

### CSAVEa

- **Syntax**: `CSAVEa ["filename" [; range]]`

Effective only when CO declared. Sends the program in ASCII text format via RS-232C (no header). All internal codes are converted to ASCII. One CR code is sent after the program. File name up to 16 characters; shorter names are padded with NUL (00H).

> **Note**: The special characters √ and π are output as `SQR` and `PI` respectively.

```
10 SETCOM 1200,8,N,1
20 SETDEV CO
30 OUTSTAT 0
40 CSAVEa "MYPROG"
```

See also: `CSAVE`, `CLOADa`, `MERGEa`

---

### CSAVEr

- **Syntax**: `CSAVEr ["filename"]`

Effective only when CO declared. Sends the reserve program in internal code via RS-232C (same format as CSAVE in RESERVE mode). Word length must be 8 bits.

See also: `CSAVE`, `CLOADr`

---

### CLOAD (RS-232C)

- **Syntax**: `CLOAD ["filename"]`

Effective only when CI declared. Manual operation only (cannot be used in program execution). Receives a program in internal code from RS-232C. The header must match the specified filename. Word length must be 8 bits. Program is cleared (NEW) if an error or break occurs during loading.

See also: `CLOADa`, `CLOADr`, `CSAVE`

---

### CLOADa

- **Syntax**: `CLOADa`

Effective only when CI declared. Manual operation only. Receives an ASCII-format program from RS-232C.

- Each line: maximum 160 ASCII codes (must be within 80 codes when converted to internal format).
- Requires 2 seconds of all-MARK (no signal) between program lines as an intergap.
- A CR code at the start of a received line terminates loading.
- On error or break, the portion already loaded remains in memory.

See also: `CLOAD`, `CSAVEa`, `MERGEa`

---

### CLOADr

- **Syntax**: `CLOADr ["filename"]`

Effective only when CI declared. Manual operation only. Receives a reserve program in internal code from RS-232C. Word length must be 8 bits. Same function as CLOAD in RESERVE mode.

See also: `CLOAD`, `CSAVEr`

---

### MERGE (RS-232C)

- **Syntax**: `MERGE ["filename"]`

Effective only when CI declared. Manual operation only, in RUN or PRO mode. Merges an internal code program received from RS-232C with the current program in memory. Word length must be 8 bits. On error or break, only the loaded portion is cleared.

See also: `MERGEa`, `CLOAD`

---

### MERGEa

- **Syntax**: `MERGEa ["filename"]`

Effective only when CI declared. Manual operation only, in RUN or PRO mode. Merges an ASCII-format program from RS-232C with the current program. Same format constraints as CLOADa.

See also: `MERGE`, `CLOADa`

---

### PRINT#

- **Syntax**: `PRINT# ["filename";] variable`

Effective only when CO declared. Sends a variable's contents via RS-232C in internal code, with a header. Variable name forms:

- `A$(*)` — entire array A$
- `@(*)` — all fixed numeric variables A through Z
- `@$(*)` — all fixed string variables A$ through Z$

Cannot send individual array elements (ERROR 1). Requires a ~4-second MARK-state intergap between the header and the variable data, and between successive variables. Word length must be 8 bits.

```
10 SETCOM 1200,8,N,1
20 SETDEV CO
30 OUTSTAT 0
40 PRINT# "DATA"; A$(*)
```

See also: `INPUT#`, `CSAVE`

---

### INPUT#

- **Syntax**: `INPUT# ["filename";] variable`

Effective only when CI declared. Receives a variable's contents from RS-232C in internal code. If the variable is an array it must be previously declared with DIM. Two-letter variable names are defined automatically. Word length must be 8 bits.

```
10 SETCOM 1200,8,N,1
20 SETDEV CI
30 OUTSTAT 0
40 DIM B$(10)
50 INPUT# "DATA"; B$(*)
```

See also: `PRINT#`, `CLOAD`

---

## Terminal Program Mode

Executing `TERMINAL` or `DTE` switches the PC-1500 from BASIC program mode to **terminal program mode**. In this mode the PC-1500 operates as an interactive serial terminal: keystrokes are sent to the RS-232C port and received characters are displayed on screen.

At least **570 bytes of free memory** are required (free area = STATUS 3 − STATUS 2). **ERROR 51** if insufficient. Use CLEAR or NEW to free memory.

Press the **ON key** to exit to the Menu Select mode, where terminal parameters and software keys can be configured.

---

### TERMINAL

- **Syntax**: `TERMINAL`

Enters terminal program mode using the current SETCOM parameters. Initial settings:

- XON/XOFF flow control: **ON** (XOFF sent when halting, XON when ready)
- Echo: **OFF** (keystrokes not shown on display)

When TERMINAL executes, RTS and DTR go active. On return to BASIC (via Menu → Quit), RTS and DTR revert to the state set by the last OUTSTAT.

See also: `DTE`

---

### DTE

- **Syntax**: `DTE`

Enters terminal program mode with fixed communication parameters: **300 baud, 7-bit, even parity, 1 stop bit**, regardless of the current SETCOM settings. Initial settings:

- XON/XOFF: **OFF**
- Echo: **ON** (keystrokes shown on display)
- CL key: sends ETX (03H)
- SHIFT+CL: sends LONG SPACE (~240 ms padding)

**Differences between TERMINAL and DTE:**

| | TERMINAL | DTE |
|---|---|---|
| Baud rate | Current SETCOM | 300 |
| Word length | Current SETCOM | 7 |
| Parity | Current SETCOM | Even |
| Stop bits | Current SETCOM | 1 |
| XON/XOFF | ON | OFF |
| Echo | OFF | ON |
| CL key | No function | ETX (03H) |
| SHIFT+CL | No function | LONG SPACE |

See also: `TERMINAL`

---

## Parallel Interface

The parallel port uses LPRINT, LLIST, FEED, CONSOLE, and PRINT#-9, statements. Activate it with `OPN "LPRT"`.

### OPN

- **Syntax**: `OPN "LPRT"`
- **Syntax**: `OPN`

Assigns the I/O port for output statements. All SETDEV assignments are cleared when OPN executes.

- `OPN "LPRT"`: directs LPRINT, LLIST, FEED, and CONSOLE to the parallel port.
- `OPN` (alone): clears device assignment. LPRINT and LLIST revert to CE-150 (ERROR 27 if not connected); FEED and CONSOLE revert to RS-232C.

```
10 OPN "LPRT"
20 CONSOLE 80,0,1        : REM 80 columns, CR+LF (parallel port)
30 LPRINT "Hello"
40 FEED
50 OPN                   : REM release parallel port
```

See also: `SETDEV`, `LPRINT`, `PRINT#-9,`

---

### PRINT#-9,

- **Syntax**: `PRINT#-9, expression`

Sends output to the parallel port regardless of whether `OPN "LPRT"` has been declared. Equivalent to LPRINT directed to the parallel port.

See also: `OPN`, `LPRINT`

---

## Error Codes

See the [Error Code Reference](Error-Codes.md) for all CE-158 error codes (50–53, 58, 61, 65, 67, 69).

---

## See Also

- [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md) — Core BASIC commands
- [CE-150 Printer/Cassette Reference](CE-150-Reference.md) — Plotter and cassette interface
- [Error Code Reference](Error-Codes.md) — All error codes
