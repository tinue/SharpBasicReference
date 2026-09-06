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
