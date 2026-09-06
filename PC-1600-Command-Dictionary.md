# Sharp PC-1600 BASIC Command Dictionary

→ [PC-1600 BASIC Reference](PC-1600-BASIC-Reference.md) · [PC-1600 Error Codes](PC-1600-Error-Codes.md) · [Command Index](Command-Index.md)

Detailed entries for every PC-1600 BASIC command, statement and function (manual chapter 14).
For the categorised overview see the
[command index in the reference](PC-1600-BASIC-Reference.md#14-basic-command-dictionary).

**Conventions.** `[ ]` optional · `< >` a value you supply · `( )` literal parentheses.
Mode/device applicability is noted per entry. Commands marked **(PC-1600)** are new relative to
the PC-1500; **(MODE 1)** exist only for PC-1500 compatibility. Where behaviour matches the
PC-1500, see also the [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md).

Example listings are transcribed from the manual and lightly corrected for OCR damage; treat them
as illustrative.

---

## A

### ABS
- **Format:** `ABS(<X>)` — **Abbr.** `AB.`
- **Purpose:** Absolute value of `X` (any numeric expression).

```
10:PRINT ABS(-2)    → 2
```

### ACS
- **Format:** `ACS(<X>)` — **Abbr.** `AC.` — **See also:** ASN, ATN, COS
- **Purpose:** Arc cosine of `X`, `-1 <= X <= 1`. Result unit follows `DEGREE` / `RADIAN` / `GRAD`.
- **Remarks:** DEG: `0°`–`180°`. RAD: `0`–`π`. GRAD: `0`–`200`.

```
10:DEGREE
20:PRINT "ARC COS OF 0.5 IS";ACS(0.5)     → 60
30:PRINT "ARC COS OF 0 IS";ACS(0)         → 90
```

### ADIN ON / OFF / STOP  **(PC-1600)**
- **Format:** `ADIN ON` | `ADIN OFF` | `ADIN STOP` — **Abbr.** `AD.` — **See also:** AIN, ON ADIN GOSUB
- **Purpose:** Enable/disable interrupts from the analog input jack (triggered when the input reaches a defined voltage level — see AIN / Chapter 6).
- **Remarks:**
  - `ADIN ON` — accept analog interrupts; branch with `ON ADIN GOSUB`.
  - `ADIN OFF` — ignore them.
  - `ADIN STOP` — ignore them but latch the most recent request; a later `ADIN ON` services it immediately. **Default.**

### AIN  **(PC-1600)**
- **Format:** `AIN` — **Abbr.** `AI.` — **See also:** ADIN ON/OFF/STOP, ON ADIN GOSUB
- **Purpose:** System variable holding the current analog input converted to an integer **0–255** (0–2.495 V; higher voltages return 255).

```
>PRINT AIN     → 43
```

### ALARM$  **(PC-1600)**
- **Format:** `ALARM$ = "MM/DD/HH/mm[;<message>]"` | `ALARM$ = ""` | `ALARM$` — **Abbr.** `AL.` — **See also:** TIME$, DATE$, POWER
- **Purpose:** Set the alarm time and an optional message.
- **Remarks:** `MM` 01–12, `DD` 01–31, `HH` 00–23, `mm` 00–59, slash-separated. At the alarm time the buzzer beeps for 1 s. `?` wildcards are allowed in month/day for recurring alarms (`"??/??/13/30"` = daily 13:30). Message ≤ 26 chars, shown on screen at the alarm time. `ALARM$ = ""` clears it; `ALARM$` as a value returns the set time.
- **Note:** the message string **overwrites the RESERVE mode II** function-key strings (Chapter 9).

```
>ALARM$="12/25/08/00;HAPPY CHRISTMAS!"
```

### AREAD
- **Format:** `<label>[:]AREAD <variable>` — **Abbr.** `A.` — **See also:** RUN
- **Purpose:** Read the item currently displayed on the screen into a variable.
- **Remarks:** Valid **only** immediately after the label on a program's first line, and only when the program was started with **DEF** + label; ignored otherwise. Numeric display → numeric variable (up to 10 digits + 2 exponent digits); character display → string variable (length as `DIM`, default 16). If only the `>` prompt is showing, the variable is cleared to 0.

```
10:"A":AREAD N
```

### ARUN
- **Format:** `ARUN` — **Abbr.** `ARU.` — **See also:** RUN, [AUTORUN files](PC-1600-BASIC-Reference.md#8-basic-programming-concepts)
- **Purpose:** Auto-run the program at power-on.
- **Remarks:** Must be the lowest-numbered statement; the computer must have been switched off in RUN mode. With program modules fitted, the search order for an `ARUN` is **slot 2 → slot 1 → internal RAM**. Unlike `RUN`, `ARUN` does **not** clear variables — use `CLEAR` in the program. If peripherals changed while off, a "not ready" error may block it; start with `RUN` instead.

```
10:ARUN:CLS
20:PRINT "THE TIME IS NOW ";TIME$
30:PRINT "YOU HAVE ";MEM;" BYTES FREE"
40:END
```

### ASC
- **Format:** `ASC(<string variable>)` | `ASC("<string>")` — **See also:** CHR$
- **Purpose:** ASCII code of the first character of the string (rest ignored). See Appendix C.

```
10:INPUT "ENTER A CHARACTER ";A$
20:PRINT "THE ASCII CODE IS ";ASC(A$)
```

### ASN
- **Format:** `ASN(<X>)` — **Abbr.** `AS.` — **See also:** ACS, ATN, SIN
- **Purpose:** Arc sine of `X`, `-1 <= X <= 1`. DEG: `-90°`–`90°`. RAD: `-π/2`–`π/2`. GRAD: `-100`–`100`.

### ATN
- **Format:** `ATN(<X>)` — **Abbr.** `AT.` — **See also:** ACS, ASN, TAN
- **Purpose:** Arc tangent of `X`. DEG: `-90°`–`90°`. RAD: `-π/2`–`π/2`. GRAD: `-100`–`100`.

### AUTO
- **Format:** `AUTO` | `AUTO <line#>` | `AUTO <line#>,<increment>` | `AUTO ,<increment>` — **Abbr.** `AU.` — **See also:** RENUM
- **Purpose:** Automatic line-number generation in PRO mode; after each `ENTER` the next number is offered. Default start and increment are 10. Type over an offered number to change it; press `CL`/break out to stop.

---

## B

### BEEP
- **Format:** `BEEP <number>` | `BEEP <number>[,<tone>[,<duration>]]` — **Abbr.** `B.` — **See also:** BEEP ON/OFF
- **Purpose:** Sound the internal speaker `<number>` times (0–65535).
- **Remarks:** `<tone>` 255–0 = rising pitch (255 ≈ 230 Hz, 0 ≈ 7 kHz, default ≈ 4 kHz). `<duration>` default 160; a given value sounds relatively longer at lower frequencies.

### BEEP ON / OFF
- **Format:** `BEEP ON` | `BEEP OFF` — **Abbr.** `B.` — **See also:** BEEP
- **Purpose:** `BEEP OFF` disables `BEEP` **and** the cassette-read monitor tones; `BEEP ON` re-enables them.

### BLOAD  **(PC-1600)**
- **Format:** `BLOAD "<d:filename>"[,#<bank>,<address>]` — **Abbr.** `BL.` — **See also:** BSAVE, CLOAD, NEW, SET
- **Purpose:** Load a machine-language program from floppy (`X:`/`Y:`), RAM disk (`S1:`/`S2:`), serial port (`COM1:`/`COM2:`) or cassette (`CAS:`).
- **Remarks:** `<bank>` 0–7, `<address>` hex load address. Omit both to reload to the address it was saved from. If saved (by `BSAVE`) with an auto-start address, it loads **and runs** from there. See Appendix D.

```
>BLOAD"X:RXOUT"
```

### BREAK ON / OFF
- **Format:** `BREAK ON` | `BREAK OFF` — **Abbr.** `BR.` — **See also:** CONT
- **Purpose:** Disable / re-enable the BREAK key. With BREAK OFF a running program cannot be interrupted from the keyboard (an infinite loop then needs RESET). Interrupt shows `BREAK IN <line#>`; resume with `CONT`. Convention: `BREAK OFF` at the top of long compute-only programs, `BREAK ON` at the end.

### BSAVE  **(PC-1600)**
- **Format:** `BSAVE "<d:filename>",#<bank>,<start address>,<end address>[,<auto-start address>]` — **Abbr.** `BS.` — **See also:** BLOAD, CSAVE M
- **Purpose:** Save a machine-language program (memory `<start>`–`<end>` in `<bank>`) to floppy, RAM disk, serial port or cassette. Errors if the device is write-protected.
- **Remarks:** `<auto-start address>` is where `BLOAD` will auto-run after reloading; default `&FFFF` = no auto-start. **No** file extension is added (unlike `SAVE`). See Appendix D.

```
>BSAVE"S1:SORT",#1,&8000,&8AFF
```

---

## C

### CALL
- **Format:** `CALL [#<bank>,]<address>[,<variable>]` — **Abbr.** `CA.` — **See also:** NEW, POKE, XPOKE
- **Purpose:** Call a machine-language routine (Z-80A / PC-1600 address space) from BASIC.
- **Remarks:** `<bank>` 0–7 (default 0), `<address>` `&0`–`&FFFF`. One `<variable>` is passed both ways: a numeric value (integer −32768..32767) goes in the **DE** register pair and comes back as a BCD value in the same variable if the carry flag is set on exit; for a string variable, **DE** = start address of the string, **B** = its length. The routine must already be in memory (`POKE` / `XPOKE`). See Appendix D. (PC-1500's `CALL` is `XCALL` here.)

```
400:CALL #3,&8000,X
410:PRINT "THE VALUE OF X RETURNED IS ";X
```

### CHAIN  **(MODE 1)**
- **Format:** `CHAIN` | `CHAIN ["<filename>"][,<line#>]` — **Abbr.** `CHA.` — **See also:** CSAVE, MERGE
- **Purpose:** Load and run another BASIC program from **cassette** (PC-1500 mode). `CHAIN` runs the first program on tape from its first line; `CHAIN ,<line#>` from that line; `CHAIN "name"` searches for the named program. Lets an over-large program be split into sequential parts. Errors if a `PASS` password is set.

### CHR$
- **Format:** `CHR$(<integer expression>)` — **Abbr.** `CH.` — **See also:** ASC
- **Purpose:** The character whose code is `<integer expression>`. Used to emit control codes to a printer / serial port or non-keyboard graphics characters. See Appendix C.

```
10:FOR X=33 TO 126:PRINT CHR$(X);:NEXT X
```

### CLEAR
- **Format:** `CLEAR` — **Abbr.** `CL.` — **See also:** DIM, ERASE, TITLE
- **Purpose:** Erase **all** variables — including the fixed `A`–`Z`, `A$`–`Z$`, `@()` — resetting numbers to 0 and strings to null. Usable mid-program to reclaim variable space, or at the start when several programs share memory. (`ERASE` clears only simple/array variables.)

### CLOAD  **(MODE 1)**
- **Format:** `CLOAD ["<filename>"][,A]` — **Abbr.** `CLO.` — **See also:** CSAVE, CLOAD?, MERGE
- **Purpose:** Load a BASIC program from **cassette** in PC-1500 mode (CE-150 / CE-162E; not the CE-1600P). `CLOAD` loads the next program on tape; `CLOAD "name"` searches for it. In RESERVE mode it loads a function-key string set. Errors if a password is set.

### CLOAD?  **(MODE 1)**
- **Format:** `CLOAD? ["<filename>"]` — **Abbr.** `CLO.?`
- **Purpose:** Verify a tape program against the copy in memory (PC-1500 mode); mismatch → **ERROR 43**.

### CLOAD M  **(MODE 1)**
- **Format:** `CLOAD M ["<filename>"][,#<bank>,<address>]` — **Abbr.** `CLO. M` — **See also:** BLOAD, CALL, CSAVE M, NEW
- **Purpose:** Load a machine-language program from cassette (PC-1500 mode) — a different memory area and tape format from `CLOAD`. `<bank>` 0–7, `<address>` hex; omit both to reload where it was saved from. If saved (by `CSAVE M`) with an auto-start address, it loads and runs from there.

### CLOSE
- **Format:** `CLOSE` | `CLOSE #<file#>[,#<file#>…]` — **Abbr.** `CLOS.` — **See also:** END, OPEN
- **Purpose:** Close files on the current device. `CLOSE` alone closes all open files. A file must be closed before reopening in another mode (reopening an open file → error). `END`, `NEW`, `RUN`, `LOAD`, editing, and power-off all close every file automatically.

### CLS
- **Format:** `CLS` — **Purpose:** Clear the whole screen; cursor to home `(0,0)` (MODE 0).

### COLOR
- **Format:** `COLOR <number>` — **Abbr.** `COL.`
- **Purpose:** Printer pen colour: `0` black, `1` blue, `2` green, `3` red. Default at power-on `0`.

### COM$  **(PC-1600)**
- **Format:** `COM$ "COMn:"` — **Abbr.** `COM.` — **See also:** SETCOM
- **Purpose:** String of the communication parameters set by the last `SETCOM` for that port, in `SETCOM` order (`<BR>,<WL>,<PR>,<ST>,<XO>,<SI>`). `"COM:"` = currently opened port.

```
10:SETCOM "COM1:",300,8,N,1,X,S
20:PRINT COM$"COM1:"
```

### COMn ON / OFF / STOP  **(PC-1600)**
- **Format:** `COMn ON` | `COMn OFF` | `COMn STOP` — **See also:** ON COMn GOSUB, SETCOM
- **Purpose:** Enable/disable interrupts from port `n` (`1` = RS-232C, `2` = optical). `ON` + `ON COMn GOSUB` to branch; `OFF` ignores; `STOP` ignores but latches the last request for a later `ON`. **Default STOP.**

### CONT
- **Format:** `CONT` — **Abbr.** `C.` — **See also:** RESUME, RUN, STOP, WAIT
- **Purpose:** Resume after `STOP`, a paused `PRINT`, or BREAK — provided the program was not edited (`GOTO <line#>` to resume elsewhere). Does **not** resume after `END` or an error (use `RESUME`).

### COPY  **(PC-1600)**
- **Format:** `COPY "<d1:name1.ext>" TO "<[d2:]name2.ext>"` — **Abbr.** `COP.` — **See also:** SET
- **Purpose:** Copy a file between devices. The extension must always be given. If `d2:` is omitted the source device is used; fails if `name2` already exists on the destination. Also used to rename the floppy drive (`X:` ↔ `Y:`) for single-drive disk-to-disk backup (see Chapter 6). Tape/serial ports cannot be copy destinations for disk sources and vice-versa in some combinations (manual has the full matrix).

```
>COPY"S1:RICH.BAS" TO "S2:RICH.BAS"
>COPY"X:HAC.BAS" TO "HACOPY.BAS"
```

### COS
- **Format:** `COS(<X>)` — **See also:** ACS, SIN, TAN
- **Purpose:** Cosine of `X`; unit per `DEGREE` / `RADIAN` / `GRAD`.

### CSAVE  **(MODE 1)**
- **Format:** `CSAVE ["<filename>"][,A][;<line#>[,<line#>]]` — **Abbr.** `CS.` — **See also:** CLOAD, CLOAD?, LLIST, MERGE
- **Purpose:** Save a program (or line range, as `LLIST`) to cassette in PC-1500 mode. In RESERVE mode saves the function-key string set. `,A` = ASCII format (else binary). Blocked by a `PASS` password. **Not** usable with the CE-1600P — only the CE-150 / CE-162E.

```
>CSAVE"PROG01";200,380
```

### CSAVE M  **(MODE 1)**
- **Format:** `CSAVE M "<filename>";#<bank>,<start address>,<end address>[,<auto-start address>]` — **Abbr.** `CS. M` — **See also:** CLOAD M, CALL, BSAVE
- **Purpose:** Save a machine-language program to cassette (PC-1500 mode). Bank/address range stored with the file; `CLOAD M` reads them back if not given. `<auto-start address>` default `&FFFF` = off.

### CSIZE
- **Format:** `CSIZE <size>` — **Abbr.** `CSI.` — **See also:** PCONSOLE
- **Purpose:** Printer character size 1–9:

| size | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|------|---|---|---|---|---|---|---|---|---|
| chars/line | 160 | 80 | 53 | 40 | 32 | 26 | 22 | 20 | 17 |
| height mm | 1.2 | 2.4 | 3.6 | 4.8 | 6.0 | 7.2 | 8.4 | 9.6 | 10.8 |
| width mm | 0.8 | 1.6 | 2.4 | 3.2 | 4.0 | 4.8 | 5.6 | 6.4 | 7.2 |

  chars/line values assume `PCONSOLE` `<length>=0` (infinite). `LLIST` resets any size > 2 back to 2.

### CURSOR
- **Format:** `CURSOR <column>[,<line>]` — **Abbr.** `CU.` — **See also:** PRINT
- **Purpose:** Move the cursor to `<column>` 0–25, `<line>` 0–3 (default: current line).

```
40:CURSOR 12,1
50:PRINT A$
```

---

## D

### DATA
- **Format:** `DATA <list of constants>` — **Abbr.** `DA.` — **See also:** READ, RESTORE
- **Purpose:** Supply constants for `READ`. Numeric or quoted-string constants, comma-separated. Non-executable — place anywhere; read in line-number order; `RESTORE` to re-read.

```
10:FOR J=1 TO 4:READ A$,B:PRINT A$,B:NEXT J
60:DATA "MICHAEL",23,"DAVID",38,"WENDY",-24,"BRIAN",34
```

### DATE$  **(PC-1600)**
- **Format:** `DATE$ = "MM/DD"` | `DATE$` — **Abbr.** `DATE.` — **See also:** TIME$, ALARM$
- **Purpose:** Real-time-clock date. As a statement, sets it (`MM` 01–12, `DD` 01–31). As a value, returns `MM/DD`. The day advances when `TIME$` rolls `23:59:59` → `00:00:00`.

```
>DATE$="12/25"
450:PRINT "TODAY'S DATE IS ";DATE$
```

### DEG
- **Format:** `DEG <dd.mmssrr>` — **See also:** DMS
- **Purpose:** Convert an angle given as `dd.mmssrr` (degrees `.` minutes `ss` seconds `rr` hundredths; `mm`,`ss` 00–59) to decimal degrees (10 significant digits).

```
10:X=DEG 50.300000 : PRINT X     → 50.5
```

### DEGREE
- **Format:** `DEGREE` — **Abbr.** `DE.` — **See also:** RADIAN, GRAD
- **Purpose:** Set the angular unit to degrees (default at power-on).

### DELETE
- **Format:** `DELETE <line#>` | `DELETE <line#>,` | `DELETE <line#>,<line#>` | `DELETE ,<line#>` — **Abbr.** `DEL.` — **See also:** NEW
- **Purpose:** Delete program lines: one line; from a line to the end; an inclusive range; or from the start up to a line. Whole program → `NEW`.

### DIM
- **Format:** `DIM <name>(<size>)` · `DIM <name$>(<size>)[*<length>]` · `DIM <name>(<rows>,<cols>)` · `DIM <name$>(<rows>,<cols>)[*<length>]` — **Abbr.** `D.` — **See also:** CLEAR, ERASE
- **Purpose:** Reserve storage for arrays (and 1-D "simple" subscripted variables). All except `A`–`Z`, `A$`–`Z$`, `@()`, `@$()` must be dimensioned before use.
- **Remarks:** Subscripts 0–255; a subscript starts at **0**, so `DIM A(2,3)` = 3 rows × 4 cols = 12 elements. `*<length>` (string element length) 1–80, default 16. Cannot re-dimension until `CLEAR` / `NEW` / `RUN` / `ERASE`. Numeric elements start at 0, string elements at null. Errors: undeclared array use, re-declaration, subscript over the DIM value.

```
10:DIM C(13)          'numeric, 14 elements
20:DIM F$(10)         'string, 11 elements
30:DIM H(4,6)         '5x7 numeric, 35 elements
40:DIM B$(7,5)*25     '8x6 string, 25 chars each
```

### DMS
- **Format:** `DMS <angle>` — **Abbr.** `DM.` — **See also:** DEG
- **Purpose:** Convert decimal degrees (`dd.` with a decimal point) to `dd.mmssrr` (deg/min/sec/hundredths).

```
10:X=DMS 50.5 : PRINT X     → 50.3
```

### DSKF  **(PC-1600)**
- **Format:** `DSKF "d:"` — **Abbr.** `DS.`
- **Purpose:** Free space in bytes on `S1:`, `S2:`, `X:` or `Y:`.

### DEV$  **(PC-1600)**
- **Format:** `DEV$` — **See also:** SETDEV
- **Purpose:** String showing the current `SETDEV` output-routing settings.

---

## E

### END
- **Format:** `END` — **Abbr.** `E.` — **See also:** STOP
- **Purpose:** Stop the program, close all files and the serial interface. Need not be the last line — place it before subroutine blocks so execution does not fall through. If omitted, the program ends when it runs out of lines (files still closed).

### EOF  **(PC-1600)**
- **Format:** `EOF(<file#>)` — **Abbr.** `EO.`
- **Purpose:** `1` if the end of the sequential input file `<file#>` has been reached, else `0`.

```
10:IF EOF(1) THEN 100
```

### ERASE
- **Format:** `ERASE <list of variable names>` — **Abbr.** `ERA.` — **See also:** CLEAR
- **Purpose:** Erase the named simple/array variables (not the fixed `A`–`Z` / `A$`–`Z$` / `@()`). An array is named with empty parentheses: `Z$()`; elements cannot be erased individually.

```
10:ERASE AB,Z$()
```

### ERL
- **Format:** `ERL` — **See also:** ERN, ON ERROR GOTO, RESUME
- **Purpose:** Line number where the last error occurred (set only for errors during program execution).

### ERN
- **Format:** `ERN` — **See also:** ERL, ON ERROR GOTO, RESUME
- **Purpose:** Error code of the last execution error.

```
10:ON ERROR GOTO 100
...
100:IF ERL=30 AND ERN=4 THEN PRINT "YOU HAVEN'T GOT A DATA LINE"
110:STOP
```

### EXP
- **Format:** `EXP(<X>)` — **Abbr.** `EX.` — **See also:** LN
- **Purpose:** eˣ (e held as 2.718281828). `X` must be `-227.9559242 … +230.2585092`; below that returns 0. For other bases use `^`.

```
>PRINT EXP(10)     → 22026.46579
```

---

## F

### FILES  **(PC-1600)**
- **Format:** `FILES "<d:>"` | `FILES "<d:filename>"` | `FILES "<d:ambiguous filename>"` — **Abbr.** `FI.` — **See also:** LFILES, SET
- **Purpose:** Show floppy / RAM-disk directory entries (name, `.BAS`, `P` protection, date, time) on screen.
- **Remarks:** No name → all files, one entry at a time (**↓** to scroll, any other key aborts except SHIFT/DEF/RCL/SML). Single name → that file. Wildcards: `*` = any run of characters (incl. none), `?` = one character.

```
>FILES"X:"
>FILES"S2:???1"
```

### FOR … NEXT
- **Format:** `FOR <counter> = <initial> TO <final> [STEP <increment>]` … `NEXT <counter>` — **Abbr.** `F.` / `N.`
- **Purpose:** Repeat the lines between `FOR` and `NEXT`. `<final>` and `<increment>` are numeric (−32768..32767 as integers in format 1, or any expressions in format 2). Loops may nest; `NEXT` may list its counter. After a normal exit the counter holds `<final> + <increment>` (see the PC-1500A compatibility note in the reference).

---

## G

### GCURSOR
- **Format:** `GCURSOR <X>[,<Y>]` — **Abbr.** `GC.` — **See also:** GPRINT
- **Purpose:** Position the graphics cursor at dot `(X,Y)` for a following `GPRINT`. Normal range X `0–155`, Y `0–31` (any value −32768..32767 accepted, off-screen just not visible). Y omitted = current Y. In MODE 1 the Y parameter is meaningless — do not give it.

### GLCURSOR
- **Format:** `GLCURSOR (<X>,<Y>)` — **Abbr.** `GL.` — **See also:** LCURSOR
- **Purpose:** In printer graphics mode, move the pen (lifted) to graphics coordinate `X,Y`, measured from the current origin, range −2048..2047.

### GOSUB … RETURN
- **Format:** `GOSUB <line#/label>` … `RETURN` — **Abbr.** `GOS.` / `RE.` — **See also:** GOTO, ON..GOSUB
- **Purpose:** Call / return from a subroutine. `GOSUB` remembers the return point; the subroutine ends with `RETURN`, which resumes after the `GOSUB`. Subroutines may be called repeatedly and nested.

### GOTO
- **Format:** `GOTO <line#/label>` — **Abbr.** `G.` — **See also:** GOSUB..RETURN, ON..GOTO
- **Purpose:** Unconditional jump (no return memory). If the target line is non-executable (`DATA`, `REM`) execution starts at the next executable line; a non-existent line → error. `GOTO` alone restarts from the first line; `GOTO <line#>` also resumes after a BREAK (cf. `CONT`).

### GPRINT
- **Format:** `GPRINT [SET/OR/XOR,] <bit-image>;<bit-image>;…` | `GPRINT [SET/OR/XOR,] "<hex bit-image string>"` | `GPRINT` — **Abbr.** `GP.` — **See also:** GCURSOR
- **Purpose:** Draw columns of bit-image dots on the screen from the graphics cursor. Each item = 8 bits = one 7-dot-tall column (bit 1 top). Decimal or `&`-prefixed hex items separated by `;`, or one hex string (odd last char ignored). `SET` (default) writes the pattern; `OR` / `XOR` combine with existing dots. `GPRINT` alone moves the graphics cursor down one line without clearing.

```
GPRINT &0;&1C;&1C;&1C;&1C;&1C;&1C;&1C;&1C;&1C;&7F;&3E;&1C;&08
GPRINT 16;40;18;253;18;40;16
GPRINT "102812F0122810"
```

### GRAD
- **Format:** `GRAD` — **Abbr.** `GR.` — **See also:** DEGREE, RADIAN
- **Purpose:** Set the angular unit to grads (`GRAD` on the status line).

### GRAPH
- **Format:** `GRAPH` — **Abbr.** `GRAP.` — **See also:** TEXT
- **Purpose:** Put the printer in graphics mode; resets the `PAPER` print-range limits to their defaults.

---

## H

### HEX$
- **Format:** `HEX$(<X>)` | `HEX$(&<X>)` — **Abbr.** `H.` — **See also:** VAL
- **Purpose:** Hex string (`&0`–`&FFFF`) for a value 0–65535 (rounded to integer). `<X>` must be a simple variable or number, **not** an expression — evaluate first into a variable.

```
50:D$=HEX$(X)
```

---

## I

### IF … THEN [… ELSE]
- **Format:** `IF <condition> THEN <line#/label/statement>` | `… THEN … ELSE <line#/label/statement>` — **Abbr.** `IF` / `T.` / `EL.`
- **Purpose:** Branch on `<condition>`. `THEN` runs if true; if false, `ELSE` runs (or execution falls to the next line if there is no `ELSE`). `THEN`/`ELSE` may name a line, a label, or any statement. May be nested within the 80-character line limit.

### INIT  **(PC-1600)**
- **Format:** `INIT "Sn:",{"F"|"M"|"P"}` | `INIT "X:"` | `INIT "COMn:",<buffer size>` — **Abbr.** `INI.` — **See also:** SETCOM, SETDEV, TITLE
- **Purpose:**
  1. **Module in slot 1/2** (MODE 0 only): `"F"` format as RAM disk; `"M"` add to internal user area (bigger programs); `"P"` program-storage area (one battery-backed program, survives removal). Fails on a module holding programs/files (clear with `KILL` / `NEW` first), a write-protected module, or a `TITLE`-selected program module. A CE-159 (8K) must be all-program or all-expansion.
  2. **`INIT "X:"`** — format a floppy (required for new disks; **erases** any existing contents).
  3. **`INIT "COMn:",<size>`** — receive-buffer size 80–16383 bytes, or `0` = minimum 40 bytes (also the power-on/reset default). Typical 256. Fails on insufficient memory or while a file is open `FOR APPEND`. Cannot be used inside a `FOR…NEXT` loop.

```
>INIT"S1:","F"
>INIT"X:"
```

### INKEY$
- **Format:** `<string var> = INKEY$` | `= INKEY$(0)` | `= INKEY$(1)` — **Abbr.** `INK.`
- **Purpose:** Read one key code from the keyboard buffer without echoing. `INKEY$` / `INKEY$(0)` = the latest key; `INKEY$(1)` = the oldest buffered key. No key → null string. Only the keys in the manual's INKEY$ character table are returned.

```
300:A$=INKEY$
310:IF A$="" THEN 300
320:IF A$="Y" THEN 500
330:GOTO 300
```

### INP  **(PC-1600)**
- **Format:** `INP(<port address>)` — **See also:** OUT
- **Purpose:** Read one byte directly from a Z-80A input port, `<port address>` `&0`–`&FFFF`.

### INPUT
- **Format:** `INPUT [<message>;|,] <list of variables>` | `INPUT <variable>[,<variable>…]` (from RS-232C) — **Abbr.** `I.`
- **Purpose:** Read keyboard values into variables (or from a serial port designated by `SETDEV`). Multiple variables comma-separated; the user enters values one per `ENTER`. No message → `?` prompt; message + `;` → no `?`, cursor after the message; message + `,` → cursor on the next line. The serial-port form shows no prompt; via the CE-158 only fixed/simple variables (no arrays).

```
10:PRINT "VOLUME OF SOLID"
20:INPUT "ENTER L,B,H ",L,B,H
30:V=L*B*H
40:PRINT "VOLUME IS ";V
```

### INPUT#  **(PC-1600)**
- **Format:** `INPUT#<file#>,<variable list>` | `INPUT#["<filename>";]<variable list>` — **Abbr.** `I.#` — **See also:** DIM, INPUT, OPEN, PRINT#, SET
- **Purpose:** Read items from a sequential file (disk / RAM disk by `<file#>` from `OPEN`; cassette by name, default = next file). Variable order and type must match the file; string variables must be long enough; arrays need `DIM`. Delimiters: comma / space / CR+LF for numbers, comma / CR+LF for strings; leading spaces ignored; a quote inside a string truncates it unless the whole item is quoted. Too few items in the file → waits (press BREAK); excess items are left unread. For cassette (format 2) arrays are given as `A(*)`.

### INSTAT  **(PC-1600)**
- **Format:** `INSTAT "COM1:"` — **Abbr.** `INSTA.` — **See also:** OUTSTAT
- **Purpose:** RS-232C control-signal states as an 8-bit value. Bit (from bit 1): 1 = DTR, 2 = RTS, 3 = CTS, 4 = CD, 5 = DSR, 6 = CI; `0` = signal high, `1` = signal low; bits 7–8 always 0. (`63` / `&3F` = all six low, the default.)

### INSTR
- **Format:** `INSTR([<col>,]X$,Y$)` | `INSTR([<col>,]"<string>","<char>")` — **Abbr.** `INS.`
- **Purpose:** Position of the first occurrence of `Y$` in `X$` (from `<col>`, default 1); `0` if not found or `X$` null.

```
20:N=INSTR(A$,"A")
```

### INT
- **Format:** `INT(<X>)`
- **Purpose:** Largest integer `<= X` (rounds **down**, toward −∞): `INT(-3.3)` = `-4`, `INT(1.6)` = `1`.

---

## K

### KBUFF$  **(PC-1600)**
- **Format:** `KBUFF$ = <string>` — **Abbr.** `KB.` — **See also:** INKEY$
- **Purpose:** Write up to 32 characters into the keyboard buffer (overwriting it) so they execute as if typed — the basis of "batch" command files. No CR is added unless included (e.g. `+CHR$(13)`).

```
200:KBUFF$="45"+CHR$(13)
210:INPUT A
```

### KEY ON / OFF / STOP  **(PC-1600)**
- **Format:** `KEY(<key#>) ON` | `KEY(<key#>) OFF` | `KEY(<key#>) STOP` — **See also:** ON KEY GOSUB
- **Purpose:** Enable / disable a function key as a run-time branch trigger (with `ON KEY GOSUB`). `STOP` latches the last press for a later `ON`.

### KEYSTAT  **(PC-1600)**
- **Format:** `KEYSTAT,[<repeat>][,<click>]` — **Abbr.** `KE.` — **See also:** INKEY$
- **Purpose:** `<repeat>` 0/1 = key auto-repeat off/on; `<click>` 0/1 = key click off/on. Power-on keeps the last values; ALL RESET default is `KEYSTAT,0,0`.

### KILL  **(PC-1600)**
- **Format:** `KILL "<d:filename>"` — **Abbr.** `K.` — **See also:** SAVE, SET
- **Purpose:** Delete a file on floppy / RAM disk (`.BAS` must be given for BASIC files). Errors if `SET`-protected, if the RAM module write-protect switch is on, or if the file is open.

---

## L

### LCURSOR
- **Format:** `LCURSOR <column>` — **Abbr.** `LC.` — **See also:** GLCURSOR, PCONSOLE, TAB
- **Purpose:** In printer TEXT mode, move the pen to a print column 0…(line length set by `PCONSOLE`). (PC-1500's `LCURSOR` is `TAB` here.)

### LEFT$
- **Format:** `LEFT$(<X$>,<N>)` — **Abbr.** `LEF.` — **See also:** MID$, RIGHT$
- **Purpose:** Leftmost `N` characters of `X$` (`N` 0–80, truncated; `N<1` → null; `N` over the length → whole string).

### LEN
- **Format:** `LEN(<X$>)`
- **Purpose:** Number of characters in `X$`, including spaces and control codes.

### LET
- **Format:** `[LET] <variable> = <expression>[,<variable> = <expression>…]` — **Abbr.** `LE.`
- **Purpose:** Assignment. `LET` is optional except inside a `THEN`/`ELSE` clause. Expression type must match the variable type. Multiple assignments comma-separated.

### LF
- **Format:** `LF [<lines>]` — **See also:** CSIZE, PITCH, PAPER
- **Purpose:** Feed printer paper. Bare `LF` = one line. `<lines>` positive = forward, negative = reverse, within the range set by `PAPER`. Line height depends on `CSIZE`.

### LFILES  **(PC-1600)**
- **Format:** `LFILES "<d:>"` | `LFILES "<d:filename>"` | `LFILES "<d:ambiguous filename>"` — **Abbr.** `LF.` — **See also:** FILES, SETDEV
- **Purpose:** As `FILES`, but the directory goes to the printer or, if `SETDEV` selected a port, to `COM1:`/`COM2:`. On the CE-1600P it prints at character size 2 regardless of `CSIZE`.

### LINE  **(PC-1600)**
- **Format:** `LINE [(X1,Y1)]-(X2,Y2)[,<dot toggle>][,<pattern>][,B|,BF]` — **Abbr.** `LIN.` — **See also:** LLINE
- **Purpose:** Draw a line on the **graphics screen** from `(X1,Y1)` (or the graphics cursor) to `(X2,Y2)`, coords relative to `(0,0)` top-left.
- **Remarks:** `<dot toggle>`: `S` (default) 1-bits set dots on; `R` 1-bits clear dots (inverse video); `X` inverts dots along the line. `<pattern>` 0–65535 / `&0000`–`&FFFF` is the 16-bit repeating dot pattern (`&FFFF` solid, `&AAAA` dotted, `&6666` dashed). `B` draws a box on the diagonal, `BF` a filled box. Coords accept −32768..32767 but only X 0–155 / Y 0–31 show.

```
40:LINE (N,10)-(M,20),,BF
```

### LIST
- **Format:** `LIST` | `LIST <line#>` — **Abbr.** `L.` — **See also:** LLIST
- **Purpose:** List the program on screen (one screenful from the lowest line; **↓** scrolls). `LIST <line#>` shows one line; a too-high number → error.

### LLINE
- **Format:** `LLINE [(X1,Y1)]-(X2,Y2)[-(X3,Y3)…][,<type>][,<color>][,B]` — **Abbr.** `LLIN.` — **See also:** COLOR, RLINE, SORGN
- **Purpose:** Draw line segments on the **printer** in absolute coordinates (relative to the `SORGN` origin), X/Y −2048..2047; up to five further contiguous segments.
- **Remarks:** `<type>` 0–9 (0 solid … 8 progressively dashed, 9 blank/pen-move-only). `<color>` 0–3 (see `COLOR`). Defaults = current values, **except** immediately after an `LPRINT` in graphics mode (and for programs ported from the PC-1500's `LINE`), where `<type>` must be given explicitly. `B` draws a rectangle on the `(X1,Y1)`–`(X2,Y2)` diagonal.

```
10:GRAPH
20:GLCURSOR (40,40)
30:SORGN
40:LLINE -(100,0)-(0,100)-(0,0)
50:TEXT
```

### LLIST / LLIST*
- **Format:** `LLIST[*]` | `LLIST[*] <line#>` | `LLIST[*] <line#>,<line#>` | `LLIST[*] <line#>,` | `LLIST[*] ,<line#>` — **Abbr.** `LL.` — **See also:** LIST
- **Purpose:** As `LIST` but to the printer (or to the serial port if `SETDEV` opened one with the port-output option). Line ranges as `DELETE`.
- **Remarks:** Output wraps per the `PCONSOLE` line length; length 16–17 with an over-long line → **ERROR 76**, no output. Line terminator (CR / LF / LF+CR) per `PCONSOLE`. Ignored if a `PASS` password is set. `LLIST*` prints **only** apostrophe-comment lines that start at the head of a line, suppressing the line number and `'` (a trailing `;` continues the comment on the listing) — useful for printing a program's documentation. To a serial port, keep line numbers ≤ 99 (2 digits) for complete `LLIST*` output. `CSIZE 1` lists at size 1, otherwise size 2; text mode is selected automatically.

### LN
- **Format:** `LN(<X>)` — **See also:** EXP
- **Purpose:** Natural logarithm (base e) of `X > 0`.

### LOAD / LOAD*
- **Format:** `LOAD "<d:filename>"[,R]` | `LOAD* "<d:filename>"` — **Abbr.** `LOA.` — **See also:** CHAIN, LLIST*, MERGE, REM, RUN, SAVE
- **Purpose:** Load a file from `S1:`/`S2:`, `X:`/`Y:`, `COMn:` or `CAS:` into memory. `,R` = run it afterwards (as `RUN`; used by AUTORUN); non-BASIC content → execution error. Open files are closed on `LOAD` unless `,R`.
- **`LOAD*`** prefixes each loaded line with a number (from 10, step 10) and an apostrophe, turning an ASCII text file into BASIC comment lines — the only way the PC-1600 handles raw text at file level (list it back with `LLIST*`). From a serial port, CR+LF = end of line, `&1A` = end of file.

```
>LOAD"BIOCALC",R
```

### LOC  **(PC-1600)**
- **Format:** `LOC(<file#>)`
- **Purpose:** Records read/written since the file was opened (floppy / slot modules only). One record = 256 bytes.

### LOCK / UNLOCK
- **Format:** `LOCK` | `UNLOCK` — **Abbr.** `LOC.` / `UN.`
- **Purpose:** Disable / re-enable the MODE key (locks the current PRO or RUN mode; cannot lock RESERVE).

### LOF  **(PC-1600)**
- **Format:** `LOF(<file#>)` — **See also:** DSKF
- **Purpose:** Size in bytes of an open file on floppy / slot modules.

### LOG
- **Format:** `LOG(<X>)` — **Abbr.** `LO.`
- **Purpose:** Common (base-10) logarithm. Other base: `LOG(X)/LOG(B)`. Antilog: `10^x`.

### LPRINT / LPRINT USING
- **Format:** `LPRINT [<list>][;]` | `LPRINT USING <format>;<list>` | `LPRINT TAB <col>;<expr>;…` — **Abbr.** `LP.` — **See also:** PCONSOLE, PRINT, PZONE, TAB
- **Purpose:** As `PRINT` / `PRINT USING` but to the printer (or a serial port per `SETDEV`). Bare `LPRINT` = blank line / feed. `;` between items = adjacent; `,` = next print zone (numbers right-justified if at a zone start, else left in the next zone); `TAB <col>` sets the column (over the `PCONSOLE` width → error). Trailing `;` keeps the next output on the same line. In graphics mode, lines are not terminated with CR/LF.

---

## M

### MAXFILES  **(PC-1600)**
- **Format:** `MAXFILES = <number of files>` — **Abbr.** `MA.` — **See also:** CLOSE, OPEN
- **Purpose:** Maximum simultaneously open files, 0–15 (0 at power-on, so it must be set before any `OPEN`). All files must be closed when it runs. Each file reserves 313 bytes (grows as needed). Cannot be used inside a `FOR…NEXT` loop.

### MEM
- **Format:** `MEM` — **Abbr.** `M.` — **See also:** STATUS
- **Purpose:** Unused user-area memory in bytes, including the variable area (= `STATUS 0`).

### MERGE  **(MODE 1)**
- **Format:** `MERGE` | `MERGE "<filename>"` — **Abbr.** `MER.` — **See also:** CLOAD
- **Purpose:** Load a cassette program alongside the one in memory (PC-1500 mode). `MERGE` alone takes the next tape program; `"filename"` searches for it.
- **Remarks:** Merged programs keep their own line numbers in separate memory areas; move between them only with `GOTO "label"` / `LIST "label"` (plain `LIST` shows only the last-merged program), so give every merged program a first-line label. With `READ`/`DATA`, label the `DATA` line and put `RESTORE "label"` before the `READ`.

### MID$
- **Format:** `MID$(<X$>,<N>,<M>)` — **Abbr.** `MI.` — **See also:** LEFT$, RIGHT$
- **Purpose:** `M` characters of `X$` from position `N` (`N` 1–80, `M` 0–80; `N` out of range → null).

```
20:Y$=MID$(Z$,3,4)     'Z$="ABCDEFG" → "CDEF"
```

### MOD
- **Format:** `<n> MOD <m>` — **See also:** INT
- **Purpose:** Remainder of `n / m` (both rounded to nearest integer first).

### MODE
- **Format:** `MODE [0]` | `MODE 1` — **Abbr.** `MO.`
- **Purpose:** Select the screen personality. **Direct mode only** — cannot appear on a program line.
- **Remarks:** `MODE` / `MODE 0` — all four lines active, PC-1600 character set, CE-1600P and PC-1600 peripherals; `PRINT` fills lines then scrolls, wrapping > 26-char items. `MODE 1` — PC-1500 mode: only the bottom line active (scrolls only in PRO mode / for `INPUT`), 26-char fixed line, PC-1500 character set and peripherals; `PRINT` overwrites the bottom line and truncates > 26 chars. PC-1500 programs must run in MODE 1. After a mode change the screen is not cleared; the prompt/cursor go to home.

### NAME  **(PC-1600)**
- **Format:** `NAME "<d:oldname>" AS "<d:newname>"` — **Abbr.** `NA.` — **See also:** COPY, FILES
- **Purpose:** Rename a file on floppy / RAM disk (same drive for both names; `newname` must be unused). Fails if the disk is write-protected, the file is `SET`-protected, the file is open, or the RAM module write-protect switch is on.

### NEW
- **Format:** `NEW` | `NEW "Sn:"[,<address>]` | `NEW <address>` | `NEW 0` — **See also:** DELETE, STATUS, TITLE
- **Purpose:** Delete all program lines and/or allocate the machine-language program area. In RESERVE mode, clears all function-key assignments. Always use `NEW` before typing a replacement program so stray old lines are not left behind.
- **Remarks:** Addresses are relative to top-of-memory 0; the machine-language area is `197`…`<address>`. `NEW` clears the `TITLE`-selected memory, keeping the current ML allocation. `NEW "Sn:"` targets a specific area (`S0:` main, `S1:`/`S2:` slot modules). `NEW "Sn:",<address>` also sets the ML upper address. `NEW 0` clears everything and sets the ML area to 0 (address 197). MODE 1 forms (`NEW`, `NEW <address>`, `NEW 0`) act on the PC-1500 user area.

```
>NEW 1001     'reserve 197–1000 for machine language
```

### ON ADIN GOSUB  **(PC-1600)**
- **Format:** `ON ADIN (<level1>,<level2>) GOSUB <line#/label>` — **Abbr.** `O. AD. GOS.` — **See also:** ADIN ON/OFF/STOP, AIN, RETI
- **Purpose:** Branch when the analog input leaves the range `level1`…`level2` (`0 <= level1 < level2 <= 255`). Subroutine must end with `RETI`. Max 8 interrupts per program. Default after the statement is `ADIN STOP` unless `ADIN ON` follows.
- **Note:** first execute `POKE &F12C,(PEEK &F12C) OR 1`.

### ON COMn GOSUB  **(PC-1600)**
- **Format:** `ON COMn GOSUB <line#/label>` — **Abbr.** `O. COM GOS.` — **See also:** COMn ON/OFF/STOP, RETI
- **Purpose:** Branch on an interrupt at port `n` (`1` RS-232C, `2` optical). `RETI` to return; max 8 interrupts; default `COMn STOP` unless `COMn ON` follows.

### ON ERROR GOTO
- **Format:** `ON ERROR GOTO <line#/label>` — **Abbr.** `O. ER. G.` — **See also:** RESUME, ERL, ERN
- **Purpose:** Divert errors to a handler (which must end with `RESUME`, `STOP` or `END`). An error inside the handler returns to `ON ERROR GOTO`, prints the code and stops. Any number allowed; the last executed one wins. `ON ERROR GOTO 0` restores normal handling. Released by `RUN` / `END` / ALL CLEAR, but **not** by starting with `GOTO` or `DEF`.

```
5:ON ERROR GOTO 100
10:SAVE "X:DEMO"
...
100:IF ERN=160 THEN PRINT "NO DISK IN DRIVE X:"
130:IF A$="Y" THEN RESUME
140:STOP
```

### ON … GOSUB / ON … GOTO
- **Format:** `ON <numeric expression> GOTO <list of line#s/labels>` | `ON <numeric expression> GOSUB <list…>` — **Abbr.** `O. G.` / `O. GOS.` — **See also:** GOSUB..RETURN, GOTO
- **Purpose:** Jump to the *n*-th target in the list, where *n* is the (truncated) value of the expression. Out of range (`<1` or `>` list length) → fall through to the next line. For `ON…GOSUB` each target must start a subroutine.

### ON KEY GOSUB  **(PC-1600)**
- **Format:** `ON KEY GOSUB <list of line#s/labels>` — **Abbr.** `O. KEY GOS.` — **See also:** KEY ON/OFF/STOP, RETI
- **Purpose:** Branch when F1–F6 is pressed while running — F1 → first target … F6 → sixth (extra items unused; missing items = no effect). Each subroutine ends with `RETI`. Cleared by `RUN` / `END`; default `KEY STOP` unless `KEY(n) ON` follows.

### ON PHONE GOSUB  **(PC-1600)**
- **Format:** `ON PHONE GOSUB <line#/label>` — **Abbr.** `O. PH. GOS.` — **See also:** PHONE ON/OFF/STOP, RETI
- **Purpose:** Branch on a modem input on the RS-232C **CI** signal (pin 9). `RETI` to return; max 8 interrupts; default `PHONE STOP` unless `PHONE ON` follows.

### ON TIME$ GOSUB  **(PC-1600)**
- **Format:** `ON TIME$ = "MM/DD/HH/mm" GOSUB <line#/label>` — **Abbr.** `O. TI. GOS.` — **See also:** RETI, TIME$, TIME$ ON/OFF/STOP
- **Purpose:** Branch when the real-time clock reaches the given time. `RETI` to return; max 8 interrupts; default `TIME$ STOP` unless `TIME$ ON` follows.

### OPEN  **(PC-1600)**
- **Format:** `OPEN "<d:filename>" FOR {INPUT|OUTPUT|APPEND} AS #<file#>` — **Abbr.** `OP.` — **See also:** CLOSE, INPUT#, MAXFILES, PRINT#
- **Purpose:** Open a file and bind it to `<file#>` (1…`MAXFILES`). `INPUT` reads with `INPUT#`; `OUTPUT` writes a **new** file with `PRINT#` (overwrites any existing file of that name); `APPEND` adds to an existing file with `PRINT#`. A file cannot be open for input and output at once — close and reopen. Fails: `OUTPUT` on a `SET`-P file; write-protected floppy/RAM module; `APPEND` on `COM1:`/`COM2:`/`CAS:`.

```
 5:MAXFILES=1
10:OPEN "X:DATA" FOR OUTPUT AS #1
30:PRINT #1,J
50:CLOSE #1
60:OPEN "X:DATA" FOR INPUT AS #1
70:IF EOF(1) THEN 110
80:INPUT #1,J
```

### OUT  **(PC-1600)**
- **Format:** `OUT <port address>,<list of expressions>` — **See also:** INP
- **Purpose:** Write bytes (values 0–255) directly to consecutive Z-80A output ports from `<port address>` (`&0`–`&FFFF`).

```
>OUT 80,187
```

### OUTSTAT  **(PC-1600)**
- **Format:** `OUTSTAT "COM1:"[,<setting>]` — **Abbr.** `OU.` — **See also:** INSTAT
- **Purpose:** Set RS-232C **RTS**/**DTR**: `<setting>` 0 = both high, 1 = RTS high/DTR low, 2 = RTS low/DTR high, 3 = both low. With no setting, both stay high during serial commands / while receiving and low otherwise; RTS drops low automatically when the receive buffer fills.

### PAPER
- **Format:** `PAPER <type>[,<limit from>][,<limit to>]` — **Abbr.** `PAP.` — **See also:** GRAPH, TEXT
- **Purpose:** Printer paper type (`C` cut sheet, `R` roll; ALL RESET default `R`) and vertical print range in 0.2 mm units. `<limit from>` (reverse) 30–2047, default 30 (cut) / 999 (roll); `<limit to>` (forward) 30–2047, default 1354 (cut) / 999 (roll); in TEXT mode with roll paper `<limit to>` defaults to infinite. `TEXT`, `GRAPH`, `PAPER`, the printer feed key and power-on reset the limits from the current paper/pen position. In graphics mode, a pen move beyond the limits is reflected back at the limit.

### PASS
- **Format:** `PASS "<string>"` — **Abbr.** `PAS.` — **See also:** CLOAD, CSAVE
- **Purpose:** Set/clear a program password (≤ 8 characters, any keyboard characters except `"`). While set, the machine stays in RUN mode: `LIST`, `LLIST`, `(C)SAVE`, `(C)LOAD`, `NEW`, `TITLE`, `MERGE`, `CHAIN` and the ↑/↓ keys are disabled, and lines can't be added/deleted. Protects **all** programs in memory. Remove by issuing `PASS` again with the same string; change by old then new. Needs a program in memory.

### PAUSE
- **Format:** `PAUSE [<list of expressions>][;]` | `PAUSE USING <format>;<list>` — **Abbr.** `PA.` — **See also:** PRINT, WAIT
- **Purpose:** Like `PRINT`, but in MODE 1 the data shows for a fixed **0.85 s** then scrolls (≈ `WAIT` + `PRINT`). MODE 1 list max 2 items.

### PCONSOLE  **(PC-1600)**
- **Format:** `PCONSOLE "LPT1:",[<line length>],[<EOL code>],[<offset>]` | `PCONSOLE "COMn:",[<line length>],[<EOL code>]` — **Abbr.** `PCONS.`
- **Purpose:** Print format / end-of-line for the printer (`LPT1:`) or a serial port (`COMn:`).
- **Remarks:** `<line length>` 16–255, or `0` = unlimited. `<EOL code>` 0 = CR, 1 = LF, 2 = CR+LF (other values → error); **ignored on the CE-1600P** (all CR → CR+LF). `<offset>` = blank columns at the line head, `<= line length - 4`. Defaults all 0. For file transfers via a port (`SAVE`/`LOAD`/`PRINT#`/`INPUT#`) the EOL is fixed CR+LF. Settings persist until changed; `CSIZE` scales `<line length>` proportionally (offset unchanged).

```
>PCONSOLE "LPT1:",42,2,3
```

### PEEK
- **Format:** `PEEK(<address>)` | `PEEK #<bank>,<address>` — **Abbr.** `PE.` — **See also:** POKE, XPEEK, XPOKE
- **Purpose:** One byte from memory (Z-80A space). `<address>` `&0`–`&FFFF`, `<bank>` 0–7. Format 1 with `&C000`–`&FFFF` reads bank-0 internal RAM; use format 2 for any other area. See Appendix D. (PC-1500's `PEEK` is `XPEEK` here.)

```
>PEEK#(0,100)     → 17
```

### PHONE ON / OFF / STOP  **(PC-1600)**
- **Format:** `PHONE ON` | `PHONE OFF` | `PHONE STOP` — **Abbr.** `PH.` — **See also:** ON PHONE GOSUB
- **Purpose:** Enable/disable modem interrupts on the RS-232C port. `ON` + `ON PHONE GOSUB` to branch; `STOP` latches the last request for a later `ON`. **Default STOP.**

### PITCH
- **Format:** `PITCH [<char pitch>][,<line spacing>]` — **Abbr.** `PI.` — **See also:** CSIZE
- **Purpose:** Printer character pitch and line spacing (TEXT mode). Char pitch: default `6 × CSIZE × 0.2 mm`, else `<char pitch> × 0.2 mm` (4–240). Line spacing: default `12 × CSIZE × 0.2 mm`, else `<line spacing> × 0.2 mm` (4–255). Reset to defaults by `TEXT`, `GRAPH`, `CSIZE`, `LLIST`, `TEST`, `PCONSOLE`. In graphics mode `<line spacing>` is ignored.

### POINT
- **Format:** `POINT (<X>,<Y>)` | `POINT (<Xcol>)` — **Abbr.** `POI.` — **See also:** GPRINT, PRESET, PSET
- **Purpose:** Format 1: `1`/`0` — is the dot at `(X,Y)` set? Format 2: the 0–255 bit-image value of the 8-dot column `<Xcol>` on the current line. Normal range X 0–155, Y 0–31 (values −32768..32767 accepted, off-screen returns 0).

### POKE
- **Format:** `POKE [#<bank>,]<address>,<integer list>` — **Abbr.** `PO.` — **See also:** PEEK, XPEEK, XPOKE
- **Purpose:** Write bytes (0–255) to consecutive addresses from `<address>` (`&0`–`&FFFF`) in `<bank>` 0–7 (default: the bank holding the running program's header). Insufficient free memory → error. See Appendix D. (PC-1500's `POKE` is `XPOKE` here.)

```
>POKE #2,&FF00,255,255
```

### POWER  **(PC-1600)**
- **Format:** `POWER OFF` | `POWER AOFF [(<n>)]` — **Abbr.** `POW.` — **See also:** ALARM$, ARUN, WAKE$
- **Purpose:** `POWER OFF` — switch off immediately (direct or in-program). `POWER AOFF (0)` (or no arg) — auto power-off 10 min after the last key/step; `POWER AOFF (1)` — disable auto power-off. ALL RESET default: auto-off after 10 min, auto-on from modem or at a set time (`WAKE$`).

### PRESET
- **Format:** `PRESET(<X>,<Y>)` — **Abbr.** `PRE.` — **See also:** PSET, LINE
- **Purpose:** Turn **off** the screen dot at `(X,Y)` (range 0–155 × 0–31; wider values accepted, no effect off-screen).

### PRINT
- **Format:** `PRINT [<list of expressions>][;]` — **Abbr.** `P.` — **See also:** MODE, PRINT USING, WAIT
- **Purpose:** Output to the screen. `;` between items = adjacent; `,` = next 13-column print zone (numbers right-justified, strings left-justified). Trailing `;` keeps the next output on the same line. `PRINT` alone = blank line. In MODE 1, execution waits for `ENTER` after each `PRINT` and the list is max 2 comma-separated items; in MODE 0 the display holds for the last `WAIT` time. No `TAB` (use `CURSOR` or `USING`).

```
50:PRINT "THE VALUES: ";A;B;C
60:PRINT "THE VALUES: ",A,B,C
```

### PRINT# / PRINT# USING  **(PC-1600)**
- **Format:** `PRINT#<file#>,<list of expressions>` | `PRINT#<file#>,USING <format>;<list>` | `PRINT#["<filename>";]<list of variables>` — **Abbr.** `P.#` / `P.#U.` — **See also:** INPUT#, OPEN, PRINT USING
- **Purpose:** Write to a sequential output file (disk / RAM disk by `<file#>` from `OPEN`; cassette by name, default = current tape position, no name). `;` = items adjacent; `,` = a blank between items, and a literal delimiting comma must be written as its own quoted `","` item. Cassette form (3) allows only `,` as separator; arrays as `A(*)`, no single elements.

### PRINT USING / USING
- **Format:** `PRINT USING <format string>;<list of expressions>` | `USING <format string>` — **Abbr.** `P. U.` / `U.` — **See also:** PRINT, LPRINT USING
- **Purpose:** Formatted output. `#` builds numeric fields, `&` string fields; expressions semicolon-separated. Standalone `USING` sets a format for all following `PRINT`s until the next `USING`; `USING` / `PRINT USING` with no string clears it. Field-character limit: 11 (`#`/`*`) per string, 14 for the thousands format.
- **Numeric format characters:**

| Format | Meaning |
|--------|---------|
| `###` | integer (leading `-` shown, `+` not) |
| `###.` | integer with trailing decimal point |
| `###.##` | fixed point, fractional digits |
| `##.##^` | floating point — mantissa + `E` + ≥2-column exponent |
| `###,###.` | integer with thousands comma |
| `+###` | signed integer (leading `+` or `-`) |
| `*####` | integer, leading spaces filled with `*` |
| `&&&&&&` | string field, left-justified |

```
40:USING "###.##"
60:PRINT A;B;C
```

### PSET
- **Format:** `PSET(<X>,<Y>)[,<function code>]` — **Abbr.** `PS.` — **See also:** PRESET, LINE
- **Purpose:** Turn **on** the screen dot at `(X,Y)` (0–155 × 0–31). Function code `X` = invert the dot instead. Wider coordinates accepted, no effect off-screen.

### PZONE  **(PC-1600)**
- **Format:** `PZONE "COMn:",<width>` | `PZONE "LPT1:",<width>` — **Abbr.** `PZ.` — **See also:** LPRINT
- **Purpose:** Width of the comma-delimited print zones for `LPRINT` to the printer (`LPT1:`, width 8–80) or a serial port (`COMn:`, width 8–255). Numbers right-justified, strings left-justified in each zone. Default 20.

```
40:PZONE "LPT1:",10
50:LPRINT A,B,C
```

---

## R

### RADIAN
- **Format:** `RADIAN` — **Abbr.** `RAD.` — **See also:** DEGREE, GRAD
- **Purpose:** Set the angular unit to radians (`RADIAN` on the status line).

### RANDOM
- **Format:** `RANDOM` — **Abbr.** `RA.` — **See also:** RND
- **Purpose:** Reseed `RND` so a different sequence is produced from each power-on. Put at the start of a program.

### RCVSTAT  **(PC-1600)**
- **Format:** `RCVSTAT "COMn:",<protocol>[,<timeout>]` — **Abbr.** `RC.` — **See also:** INSTAT, SNDSTAT
- **Purpose:** RS-232C receive handshake and serial timeout. `<protocol>` is an 8-bit value; bit 3 = CTS must be high, bit 4 = CD must be high, bit 5 = DSR must be high (bit = 0 means "must be high", 1 means "don't care"); bits 1,2,6,7,8 unused (set 0). No meaning for the optical port. `<timeout>` 0–255 in 0.5 s units; `0` = infinite (default).

### READ … DATA
- **Format:** `READ <list of variables>` … `DATA <list of constants>` — **Abbr.** `REA.` / `DA.` — **See also:** DATA, RESTORE
- **Purpose:** Assign successive `DATA` constants to the `READ` variables (types must match). Too many variables → error; extra constants stay unread; re-read with `RESTORE`. On first execution with no preceding `RESTORE`, the search order for the first `DATA` is `S2:` → `S1:` → `S0:`. After a `GOTO`/`DEF` restart following a `READ`, the next `READ` continues at the next `DATA` line.

```
10:DIM B(10)
20:FOR I=1 TO 10:READ B(I):PRINT B(I):NEXT I
60:DATA 10,20,30,40,50
70:DATA 60,70,80,90,100
```

### REM  /  '
- **Format:** `REM <remark>` | `'<remark>` — **See also:** LIST, LLIST
- **Purpose:** Comment; ignored at run time, shown by `LIST`. An apostrophe replaces `REM`. A jump to a `REM` line resumes at the next executable line. Append with `:REM …`; nothing may follow `REM` on the line.

```
20:DIM A$(3,5):REM RESERVING SPACE FOR A 3 X 5 ARRAY
30:'THIS LINE HAS NO EFFECT EITHER
```

### RENUM
- **Format:** `RENUM [<new line#>][,<old line#>][,<increment>]` — **Abbr.** `REN.` — **See also:** DELETE, LIST
- **Purpose:** Renumber from `<old line#>` onward to start at `<new line#>` (default 10) in `<increment>` (default 10). Results > 65279 → error. `GOTO`/`GOSUB`/etc. targets are updated; a missing target aborts the renumber with `undefined in <line#>`; a computed `GOTO A*5` aborts with an error. Cannot renumber a program loaded in MODE 1 — save it ASCII (`,A`) and reload in MODE 0.

### RESTORE
- **Format:** `RESTORE` | `RESTORE <line#/label>` — **Abbr.** `RES.` — **See also:** READ, DATA
- **Purpose:** Reset the `DATA` read pointer to the first item of the first (or specified) `DATA` line — if that line has no `DATA`, to the first `DATA` line after it.

```
60:RESTORE     'lets the same DATA be READ again
```

### RESUME
- **Format:** `RESUME [<line#>]` | `RESUME NEXT` — **Abbr.** `RESU.` — **See also:** ON ERROR GOTO, ERL, ERN
- **Purpose:** Leave an error handler and continue: `RESUME` at the statement that erred, `RESUME <line#>` at a given line, `RESUME NEXT` at the line after the error. Only valid inside an error-processing routine.

### RETI  **(PC-1600)**
- **Format:** `RETI` — **See also:** ON ADIN GOSUB, ON COMn GOSUB, ON KEY GOSUB, ON PHONE GOSUB, ON TIME$ GOSUB
- **Purpose:** Return from an **interrupt** subroutine (the `ON … GOSUB` family). Like `RETURN`, but on execution it also services the last interrupt that arrived while the handler was running.

### RIGHT$
- **Format:** `RIGHT$(<X$>,<N>)` — **Abbr.** `RI.` — **See also:** LEFT$, MID$
- **Purpose:** Rightmost `N` characters of `X$` (`N` 0–80, truncated; `<1` → null; over the length → whole string).

### RLINE
- **Format:** `RLINE [(X1,Y1)]-(X2,Y2)[-(X3,Y3)…][,<type>][,<color>][,B]` — **Abbr.** `RL.` — **See also:** COLOR, LLINE
- **Purpose:** Like `LLINE`, but printer coordinates are **relative** — each point is measured from the previous point as origin. Up to five further segments. `<type>` 0–9, `<color>` 0–3 (defaults = current). `B` = rectangle on the `(X1,Y1)`–`(X2,Y2)` diagonal.

```
 5:PAPER R
10:GRAPH
20:GLCURSOR (40,40)
30:RLINE -(100,0)-(-100,100)-(0,-100)
40:TEXT
```

### RMT ON / OFF
- **Format:** `RMT ON` | `RMT OFF` — **Abbr.** `RM.`
- **Purpose:** Enable / disable the computer's remote-control of cassette-recorder power during tape I/O.

### RND
- **Format:** `RND(<X>)` — **Abbr.** `RN.` — **See also:** RANDOM
- **Purpose:** Random number, 10 significant digits. `X < 0` — restart the same sequence each call. `0 < X < 1` — a value in `[0,1)`. `X > 1` — an integer in `1…X`. Without `RANDOM` the same sequence recurs from each power-on.

### ROTATE
- **Format:** `ROTATE <position>` — **Abbr.** `RO.`
- **Purpose:** Printer character orientation / head-travel direction, `<position>` 0–3 (0 normal, 1 down, 2 upside-down, 3 up). Default = last `ROTATE` value.

### RUN
- **Format:** `RUN` | `RUN <line#/label>` — **Abbr.** `R.` — **See also:** CONT, GOTO, LOAD, MERGE
- **Purpose:** Execute from the lowest line (or the given line/label). Clears all variables and arrays and resets the `DATA` pointer.

### RXD$  **(PC-1600)**
- **Format:** `RXD$`
- **Purpose:** Hex string of the byte currently arriving at the `SETDEV`-selected serial port. No data → `&20 &20`; communication error → `&3F &20 &20`.

---

## S

### SAVE / SAVE*
- **Format:** `SAVE "<d:filename>"[,A]` | `SAVE* "<d:filename>"` — **Abbr.** `S.` — **See also:** LOAD, MERGE
- **Purpose:** Save a program/data file to `S1:`/`S2:`, `X:`/`Y:`, `CAS:` or a serial port. `,A` = ASCII (else compressed binary). `SAVE*` saves **only** comment lines (`REM` / `'`) — a simple text store (retrieve with `LOAD*`). No extension → `.BAS` added. Overwrites an existing file unless `SET`-P or the medium is write-protected. To a port, only `COM2:` with `,A`; CR+LF = end of line, `&1A` = end of file.

```
>SAVE"X:CHESS"
>SAVE*"S2:INFO"
```

### SET  **(PC-1600)**
- **Format:** `SET "<d:filename>","P"` | `SET "<d:filename>"," "` — **See also:** KILL, NAME
- **Purpose:** File write-protection on floppy / RAM disk. With `"P"` set: no writing (`OPEN` for `OUTPUT`/`APPEND` fails), no `KILL`, no `NAME`. Release with a space in place of `"P"`.

### SETCOM  **(PC-1600)**
- **Format:** `SETCOM "COMn:",[<BR>],[<WL>],[<PR>],[<ST>],[<XO>],[<SI>]` — **Abbr.** `SETC.` — **See also:** COM$, SETDEV
- **Purpose:** Serial-port protocol. `<BR>` 50–38400; `<WL>` 5–8; `<PR>` `E`/`O`/`N`; `<ST>` 1/2; `<XO>` `X`/`N` (XON/XOFF); `<SI>` `S`/`N` shift in/out (7-bit data only). Defaults: RS-232C `1200,8,N,1,X,S`; optical `38400,7,E,2,X,S`.
- **Note:** for `SAVE`/`LOAD`/`BSAVE`/`BLOAD` over a port, `<WL>` must be 8 and `<SI>` = `N` (unrestricted for ASCII `SAVE`/`LOAD` and for `PRINT#`/`INPUT#`).

```
>SETCOM "COM1:",,,E
```

### SETDEV  **(PC-1600)**
- **Format:** `SETDEV "COMn:"[,PO][,KI]` — **Abbr.** `SE.`
- **Purpose:** Open a serial port for I/O. `PO` = send `LPRINT`/`LLIST`/`LFILES` output to the port; `KI` = take `INPUT` data from the port. `SETDEV` with no parameters reverts output to the printer and input to the keyboard.

### SGN
- **Format:** `SGN(<X>)` — **Abbr.** `SG.`
- **Purpose:** `1` if `X > 0`, `0` if `X = 0`, `-1` if `X < 0`.

### SIN
- **Format:** `SIN(<X>)` — **Abbr.** `SI.` — **See also:** ASN, COS, TAN
- **Purpose:** Sine of `X`; unit per `DEGREE` / `RADIAN` / `GRAD`.

### SNDBRK  **(PC-1600)**
- **Format:** `SNDBRK "COMn:",<number>` — **Abbr.** `SNDB.`
- **Purpose:** Send `<number>` (1–255) continuous break characters to a port to halt the other end's transmission (send several — the first may be missed).

### SNDSTAT  **(PC-1600)**
- **Format:** `SNDSTAT "COMn:",<protocol>[,<timeout>]` — **Abbr.** `SN.` — **See also:** RCVSTAT
- **Purpose:** RS-232C send handshake + serial timeout. `<protocol>` 0–255: bit 3 = CTS must be high, bit 4 = CD must be high, bit 5 = DSR must be high (0 = "must be high", 1 = "don't care"); bits 1,2,6,7,8 = 0. No meaning for the optical port. `<timeout>` 0–255 × 0.5 s, `0` = infinite (default).

### SORGN
- **Format:** `SORGN` — **Abbr.** `SO.` — **See also:** LLINE, GLCURSOR
- **Purpose:** Make the current pen position the graphics origin `(0,0)`. Default origin: X left home, Y the current paper position.

### SQR
- **Format:** `SQR(<X>)` — **Abbr.** `SQ.`
- **Purpose:** Square root; negative `X` → error.

```
>PRINT SQR(5)     → 2.236067977
```

### STATUS
- **Format:** `STATUS <value>` — **Abbr.** `STA.` — **See also:** MEM, NEW
- **Purpose:** Memory-area information:

| value | Returns |
|-------|---------|
| 0 | Free user memory (free area + variables area) in bytes |
| 1 | Size of the loaded program(s) in bytes |
| 2 | Lower address of the free user area |
| 3 | Last address of the free user area + 1 |
| 4–255 | Last line number executed for the current program |
| 256 | Bank number holding the lower address of the free user area |
| 257 | Bank number of the last bank of free user area in expansion RAM (0 if none) |
| 258 | Unallocated user memory (free area) in bytes |
| 259 | Unused memory in the slot-1 program module in bytes |
| 260 | Unused memory in the slot-2 program module in bytes |

### STOP
- **Format:** `STOP` — **Abbr.** `ST.` — **See also:** CONT, END
- **Purpose:** Halt for debugging — shows `BREAK IN <line#>`; resume with `CONT` (if unedited). Unlike `END`, files are **not** closed.

### STR$
- **Format:** `STR$(<numeric value>)` — **Abbr.** `STR.` — **See also:** VAL
- **Purpose:** Numeric → string (same digits, treated as characters; leading `-` if negative; floating-point form if it won't fit). Inverse of `VAL`.

---

## T

### TAB
- **Format:** `TAB <column>` — **See also:** LCURSOR, LPRINT
- **Purpose:** In printer TEXT mode, move the pen to a print column 0…(`PCONSOLE` line length); columns scale with `CSIZE`. Usable inside `LPRINT`. (This is the PC-1600 name for the PC-1500's `LCURSOR`.)

### TAN
- **Format:** `TAN(<X>)` — **Abbr.** `TA.` — **See also:** ATN, COS, SIN
- **Purpose:** Tangent of `X`; unit per `DEGREE` / `RADIAN` / `GRAD`. `X = 90°` → error.

### TEST
- **Format:** `TEST` — **Abbr.** `TE.`
- **Purpose:** Printer self-test — draws four boxes in colour order black → blue → green → red, then sets text mode. Interrupt with BREAK.

### TEXT
- **Format:** `TEXT` — **Abbr.** `TEX.` — **See also:** GRAPH
- **Purpose:** Set the printer to text mode: character size 2, origin at the pen's left position; resets the `PAPER` `<limit from>` / `<limit to>` to defaults.

### TIME
- **Format:** `TIME` | `TIME = MMDDHH.mmss` — **See also:** TIME$
- **Purpose:** Read / set the clock as `MMDDHH.mmss` (month, day, hour, minute, seconds). Survives power-off; no leap-year handling. `TIME = 0` is not allowed on the PC-1600 (cf. PC-1500).

```
>TIME=031220.3215     'March 12, 20:32:15
```

### TIME$  **(PC-1600)**
- **Format:** `TIME$ = "HH:mm:ss"` | `TIME$` — **Abbr.** `TI.` — **See also:** DATE$, TIME$ ON/OFF/STOP, ON TIME$ GOSUB
- **Purpose:** Real-time-clock time. As a statement sets it (`HH` 00–23, `mm`/`ss` 00–59, colon-separated); as a value returns `HH:mm:ss`.

### TIME$ ON / OFF / STOP  **(PC-1600)**
- **Format:** `TIME$ ON` | `TIME$ OFF` | `TIME$ STOP` — **Abbr.** `TI.` — **See also:** ON TIME$ GOSUB
- **Purpose:** Enable/disable all `ON TIME$ GOSUB`. `STOP` disables but, on a later `TIME$ ON`, jumps immediately if the set time passed meanwhile. **Default STOP.**

### TITLE  **(PC-1600)**
- **Format:** `TITLE "S0:"` | `TITLE "S1:"` | `TITLE "S2:"` | `TITLE?` — **Abbr.** `TIT.`
- **Purpose:** Select the active memory: `S0:` internal RAM (ALL RESET default), `S1:`/`S2:` the program module in that slot; `TITLE?` returns 0/1/2. Error if the slot holds a RAM-disk module rather than a program module. After `TITLE` during an execution halt, ↑/↓ may not list the module's program — use `LIST`.
