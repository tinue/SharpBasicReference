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
