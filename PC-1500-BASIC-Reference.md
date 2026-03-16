# Sharp PC-1500 BASIC Reference

→ [Command Index](Command-Index.md) · [Error Code Reference](Error-Codes.md)

## Overview

The **Sharp PC-1500** is a pocket computer with a built-in BASIC interpreter. It fits in a shirt pocket and runs on two AA batteries, yet it supports floating-point math, string handling, graphics, and a clock.

### Display

The display is a **7×156 dot LCD**. In text mode it shows **26 characters** across. In graphics mode all 156 dot columns are available, each 7 dots tall.

### Numbers

- Range: approximately ±1×10⁻⁹⁹ to ±9.999999999×10⁹⁹
- Precision: 10 significant digits
- Integer arithmetic is exact; floating-point results are rounded to 10 digits

### Program Memory

- Line numbers: **1 through 65279**
- Program capacity: **~1850 steps** on the base 3.5KB RAM configuration; expands with memory modules (CE-151, CE-1638, CE-163F, and others). Maximum configuration is a PC-1500A with a 16KB module (~21KiB total usable RAM).
- Check free space with `MEM` or `STATUS 0`

### Operating Modes

The **MODE** key switches between two primary modes:

| Mode | Purpose |
|------|---------|
| **RUN** | Run programs, enter direct commands |
| **PRO** | Enter and edit program lines |

Press **SHIFT+MODE** to enter the **RESERVE** mode (see [Reserve Keys](#reserve-keys) below).

Use `LOCK` to disable the MODE key and `UNLOCK` to re-enable it.

→ [Error Code Reference](Error-Codes.md)

### Hex Literals

Prefix a hexadecimal number with `&`: `&F` = 15, `&FF` = 255, `&7ECA` = 32458.

---

## Variables and Data Types

### Numeric Variables

Variable names are a single letter (A–Z) or a letter followed by one letter or digit (A1, AB, X9, etc.).

Fixed memory variables are the **26 single-letter names A through Z**. They are never cleared by `RUN` — only by `CLEAR` or `NEW`. Use them to preserve state across program runs.

All other numeric variables (two-character names like AB, X1) and all array elements are **main memory** variables. `RUN` clears them.

### String Variables

Append `$` to any variable name: `A$`, `B$`, `N1$`, `TM$`.

The 26 single-letter string variables **A$ through Z$** are fixed memory — same persistence rules as A–Z.

Two-character string variables and string array elements are main memory.

### The @ Array

`@(n)` is an alias for the nth letter variable: `@(1)` = A, `@(2)` = B, ..., `@(26)` = Z.
`@$(n)` is an alias for the nth string variable: `@$(1)` = A$, ..., `@$(26)` = Z$.

No `DIM` is required for `@` or `@$`. This lets you loop over all letter variables by index.

```
FOR I = 1 TO 26 : LET @(I) = 0 : NEXT I    : REM clear A through Z
```

### Reserved Names

These names cannot be used as variables because they conflict with keywords or built-in constants:

`IF`, `LF`, `LN`, `PI`, `TO` — and their string variants `IF$`, `LF$`, `LN$`, `PI$`, `TO$`.

---

## Program Control

### GOTO

- **Syntax**: `GOTO line` or `GOTO "label"`
- **Abbreviation**: G. GO. GOT.

Transfers control to the given line number or single-character label.

Used as a **direct command** (not inside a program): clears the display, moves the cursor to column 0, and clears the FOR-NEXT/GOSUB stack. Variables are not cleared.

```
10 GOTO 100
20 GOTO "X"
```

See also: `GOSUB`, `ON GOTO`

---

### GOSUB

- **Syntax**: `GOSUB line` or `GOSUB "label"`
- **Abbreviation**: GOS. GOSU.

Jumps to a subroutine, saving the return address. Execution continues from the next statement after the `GOSUB` when `RETURN` is reached.

**ERROR 15** if subroutines are nested too deeply.

```
100 GOSUB 500
110 PRINT "BACK"
...
500 PRINT "IN SUB"
510 RETURN
```

---

### RETURN

- **Syntax**: `RETURN`
- **Abbreviation**: RE. RET. RETU. RETUR.

Returns from a subroutine to the statement after the `GOSUB` that called it.

**ERROR 2** if `RETURN` is reached with no active `GOSUB`.

---

### IF / THEN

- **Syntax**: `IF condition THEN statement`
- **IF abbreviation**: none
- **THEN abbreviation**: T. TH. THE.

Evaluates the condition. If true (nonzero), executes the statement after `THEN`. If false (zero), skips to the next line.

There is no `ELSE` clause.

> **Warning**: After `THEN`, if you are assigning a value, `LET` is **required**. Writing `IF A>5 THEN B=1` causes **ERROR 19**. You must write `IF A>5 THEN LET B=1`.

```
IF X > 10 THEN PRINT "BIG"
IF X > 10 THEN LET Y = 1
IF A$ = "YES" THEN GOSUB 500
IF X THEN GOTO 100             : REM any nonzero value is true
```

---

### FOR / NEXT

- **Syntax**: `FOR var = start TO end [STEP n]`
- **FOR abbreviation**: F. FO.
- **NEXT abbreviation**: N. NE. NEX.
- **STEP abbreviation**: STE.

Repeats the body until the counter variable passes the end value. Default step is 1.

The counter and STEP value are **integers in the range -32768 to 32767**. A non-integer STEP is truncated to integer before use.

The loop body executes at least once when start equals end.

**ERROR 2** if `NEXT` is reached without a matching `FOR`.
**ERROR 14** if FOR-NEXT loops are nested too deeply.

```
FOR I = 1 TO 10 : PRINT I : NEXT I
FOR I = 10 TO 1 STEP -1 : PRINT I : NEXT I
```

---

### ON GOTO / ON GOSUB

- **Syntax**: `ON expr GOTO line1, line2, ...`
- **Syntax**: `ON expr GOSUB line1, line2, ...`
- **ON abbreviation**: O.

Evaluates `expr` and branches to the corresponding line. If `expr` = 1, go to line1; if `expr` = 2, go to line2; and so on. If `expr` is 0 or exceeds the number of lines listed, execution continues to the next statement.

```
ON X GOTO 100, 200, 300
ON CHOICE GOSUB 500, 600, 700
```

---

### ON ERROR GOTO

- **Syntax**: `ON ERROR GOTO line`
- **ON abbreviation**: O.
- **ERROR abbreviation**: ER. ERR. ERRO.

When any runtime error occurs, jumps to `line` instead of halting. Inside the error handler, use `ERN` to get the error code and `ERL` to get the line number where it occurred.

`ON ERROR GOTO` is cancelled when `RUN` starts a program.

```
ON ERROR GOTO 900
...
900 PRINT "ERROR "; ERN; " AT LINE "; ERL
910 END
```

To trigger an error deliberately (for testing), use `ERROR n`.

---

### ERL

- **Syntax**: `ERL`
- **Abbreviation**: none

Function that returns the **line number** where the most recent error occurred. Use inside an `ON ERROR GOTO` handler.

```
ON ERROR GOTO 900
...
900 IF ERN = 9 THEN PRINT "SUBSCRIPT ERR AT "; ERL : END
910 PRINT "ERROR "; ERN; " IN LINE "; ERL
920 END
```

---

### ERN

- **Syntax**: `ERN`
- **Abbreviation**: none

Function that returns the **error number** of the most recent error. Use inside an `ON ERROR GOTO` handler. See the [Error Code Reference](Error-Codes.md) for a full list of codes.

---

### ERROR

- **Syntax**: `ERROR n`
- **Abbreviation**: ER. ERR. ERRO.

Generates error number `n` deliberately. Useful for testing error handlers or for signalling application-defined error conditions from within a program.

```
IF X < 0 THEN ERROR 19   : REM raise "value out of range"
```

---

### END

- **Syntax**: `END`
- **Abbreviation**: E. EN.

Terminates the program normally. No message is displayed. All variables are retained. Cannot be resumed with `CONT`.

---

### STOP

- **Syntax**: `STOP`
- **Abbreviation**: S. ST. STO.

Suspends execution and displays `BREAK IN n` where n is the line number. All variables retain their values. You can inspect or change variables in direct mode, then resume with `CONT`.

---

### CONT

- **Syntax**: `CONT` (direct mode only)
- **Abbreviation**: C. CO. CON.

Resumes execution from the point where `STOP` halted the program. Variables retain their values.

---

### RUN

- **Syntax**: `RUN`, `RUN n`, or `RUN "label"`
- **Abbreviation**: R. RU.

Starts the program from the beginning, from line n, or from the line with the given label.

`RUN` clears: display, cursor to column 0, main memory variables, FOR-NEXT/GOSUB stack, `ON ERROR GOTO` setting, `DATA` pointer, `USING` format.

`RUN` does **not** clear: fixed memory variables (A–Z, A$–Z$), `WAIT` setting, TRON/TROFF state.

---

### NEW

- **Syntax**: `NEW` or `NEW 0`
- **Abbreviation**: none

Erases the program **and** clears all variables including fixed memory (A–Z, A$–Z$). Use `CLEAR` to reset variables without erasing the program.

---

### LIST

- **Syntax**: `LIST`, `LIST n`, `LIST ,n`, `LIST n,`, `LIST n1, n2`, `LIST "label"`, `LIST "label",`
- **Abbreviation**: L. LI. LIS.

Lists program lines to the display.

| Form | Effect |
|------|--------|
| `LIST` | All lines |
| `LIST n` | Only line n |
| `LIST ,n` | All lines up to and including line n |
| `LIST n,` | Lines from n to end |
| `LIST n1, n2` | Lines n1 through n2 |
| `LIST "X"` | Line containing label "X" |
| `LIST "X",` | From labeled line to end |

---

### ARUN

- **Syntax**: `ARUN` (must be the first statement in program memory)
- **Abbreviation**: ARU.

Causes the program to start automatically when the PC-1500 is switched on, provided the machine was turned off while in RUN mode with no errors pending. If `ARUN` is not the very first statement in memory, it is ignored.

---

## Input and Output

### PRINT

- **Syntax**: `PRINT [item [; item] [, item] ...]`
- **Abbreviation**: P. PR. PRI. PRIN.

Prints expressions, strings, and variable values to the display.

- **Semicolon (`;`)** between items: items are printed adjacent to each other with minimum spacing.
- **Comma (`,`)** between items: advances to the next print zone.
- **Trailing semicolon**: suppresses the carriage return — the next `PRINT` continues on the same line.
- `PRINT` alone: outputs a blank line.

The display is 26 characters wide. Output that exceeds the line length scrolls the display.

```
PRINT "X = "; X
PRINT A; B; C
PRINT "NAME: "; N$
PRINT                        : REM blank line
```

See also: `USING`, `PAUSE`, `CURSOR`, `CLS`

---

### INPUT

- **Syntax**: `INPUT [prompt;] var [, var ...]` or `INPUT [prompt,] var ...`
- **Abbreviation**: I. IN. INP. INPU.

Displays the prompt (if given) and waits for the user to type a value followed by ENTER. For multiple variables, the user separates values with commas.

**ERROR 32** if the graphics cursor (`GCURSOR`) is at columns 152–155 — there is not enough display space to show input there. Keep `GCURSOR` at 0–151 before calling `INPUT`.

**ERROR 0** or **ERROR 224–241** if the user types non-numeric data when a number is expected.

```
INPUT A
INPUT "ENTER VALUE"; X
INPUT "X, Y"; X, Y
```

---

### INKEY$

- **Syntax**: `var$ = INKEY$`
- **Abbreviation**: INK. INKE. INKEY.

Non-blocking keyboard read. Returns the character of the key currently held down.

> **Warning**: When no key is pressed, `INKEY$` returns a **space character** `" "` — not an empty string. Checking `IF INKEY$ = ""` will never be true.

To wait for any key:
```
10 IF INKEY$ = " " THEN GOTO 10
```

To wait for a specific key:
```
10 LET K$ = INKEY$
20 IF K$ = " " THEN GOTO 10
30 IF K$ = "A" THEN GOSUB 200
```

---

### CLS

- **Syntax**: `CLS`
- **Abbreviation**: none

Clears the display.

---

### CURSOR

- **Syntax**: `CURSOR n` or `CURSOR`
- **Abbreviation**: CU. CUR. CURS. CURSO.

`CURSOR n`: moves the text cursor to column n (0 = leftmost, 25 = rightmost).
`CURSOR` with no argument: cancels any previous cursor setting and returns to sequential output.

**ERROR 19** if n is outside 0–25.

```
CURSOR 0 : PRINT "LEFT"
CURSOR 13 : PRINT "MID"
```

---

### WAIT

- **Syntax**: `WAIT n` or `WAIT`
- **Abbreviation**: W. WA. WAI.

Sets the display duration for subsequent `PRINT` statements. Once set, `WAIT` persists across `RUN` — it is not reset when a program starts.

| Value | Approximate duration |
|-------|---------------------|
| `WAIT 0` | Nearly instant |
| `WAIT 64` | ~1 second |
| `WAIT 3840` | ~1 minute |
| `WAIT 65535` | ~17 minutes |

`WAIT` with no argument: waits indefinitely for ENTER before advancing. Also cancels any previously set duration.

> **Note**: `WAIT` sets a persistent per-print delay. `PAUSE` is a one-shot fixed display (~0.85 sec). They serve different purposes — do not confuse them.

---

### PAUSE

- **Syntax**: Same forms as `PRINT`
- **Abbreviation**: PA. PAU. PAUS.

Displays content for approximately 0.85 seconds, then automatically continues.

> **Warning**: `PAUSE` does **not** wait for a key press. It is a fixed-time display only. To pause until the user presses ENTER, use `WAIT` with no argument before a `PRINT`.

```
PAUSE "LOADING..."
PAUSE "SCORE = "; SC
```

---

### USING

- **Syntax**: `USING "format"` or inline: `PRINT USING "format"; expr`
- **Abbreviation**: U. US. USI. USIN.

Formats numeric or string output. Once set with `USING "format"`, the format applies to all subsequent `PRINT` statements until changed or cancelled. Cancelled by `RUN`.

**Format characters:**

| Character | Meaning |
|-----------|---------|
| `#` | Numeric field digit; right-justified; leading zeros become spaces; the field must be at least 1 wider than the number of digits to leave room for the sign |
| `*` | Like `#` but fills leading spaces with asterisks |
| `.` | Decimal point |
| `,` | Insert a comma every 3 digits (requires an extra `#` for each comma) |
| `^^^^` | Scientific notation (exactly 4 carets required) |
| `+` | Always show sign |
| `&` | String field; left-justified; truncated if value is wider than the field |

**ERROR 36** if a numeric value overflows the field.
**ERROR 12** if the format string is invalid.

```
USING "###.##" : PRINT 3.14159        : REM "  3.14"
PRINT USING "##,###"; 12345           : REM "12,345"
PRINT USING "+###.##"; -1.5           : REM "  -1.50"
PRINT USING "&&&&&&&&"; "HI"          : REM "HI      "
PRINT USING "###.##^^^^"; 12345.6     : REM " 1.23+04"
```

---

### ZONE

- **Syntax**: `ZONE n`
- **Abbreviation**: none

Sets the column-field width used when printing multiple items separated by commas in a `PRINT` statement. Each comma advances the cursor to the next multiple of `n`. The default zone width is 8 characters.

```
ZONE 10
PRINT A, B, C       : REM columns at positions 0, 10, 20
```

---

## Display Graphics

The PC-1500 display has 156 dot columns numbered 0–155. Each column is 7 dots tall; the dot rows have bit weights 1 (bottom), 2, 4, 8, 16, 32, 64 (top). A value of 127 lights all 7 dots; 0 is blank.

Text and graphics share the same display. `CLS` clears everything.

---

### GCURSOR

- **Syntax**: `GCURSOR n` where n is 0–155
- **Abbreviation**: GC. GCU. GCUR. GCURS. GCURSO.

Moves the graphics cursor to dot column n.

**ERROR 19** if n is outside 0–155.

> **Warning**: Keep `GCURSOR` at 0–151 before using `INPUT`. Columns 152–155 leave insufficient display space for user input, causing **ERROR 32**.

---

### GPRINT

- **Syntax** (three equivalent forms):
  1. `GPRINT "HH HH ..."` — hex string: each pair of hex digits defines one column's dot pattern
  2. `GPRINT expr [; expr ...]` — decimal values 0–127
  3. `GPRINT &H [; &H ...]` — hex values with `&` prefix
- **Abbreviation**: GP. GPR. GPRI. GPRIN.

Outputs dot patterns to the display starting at the current graphics cursor position, then advances the cursor. A comma between items inserts a blank column.

```
GPRINT "7F7F7F"             : REM three solid columns
GPRINT 127; 0; 127          : REM solid, gap, solid
GPRINT 8; 4; 2; 1           : REM diagonal pattern (top to bottom)
GPRINT &7F, &7F             : REM two solid columns with gap between
```

Bit weights (for constructing patterns):

| Bit weight | Row position |
|-----------|-------------|
| 1 | Bottom |
| 2 | |
| 4 | |
| 8 | Middle |
| 16 | |
| 32 | |
| 64 | Top |

---

### POINT

- **Syntax**: `POINT n` where n is 0–155
- **Abbreviation**: POI. POIN.

Returns the dot pattern (0–127) currently shown at display column n. Use this to read back the display contents.

```
P = POINT 10       : REM read pattern at column 10
IF POINT 50 > 0 THEN PRINT "COLUMN 50 HAS DOTS"
```

---

## Mathematical Functions

### ABS

- **Syntax**: `ABS(expr)`
- **Abbreviation**: AB.

Returns the absolute value.

```
ABS(-5)    : REM 5
ABS(3.7)   : REM 3.7
```

---

### SGN

- **Syntax**: `SGN(expr)`
- **Abbreviation**: SG.

Returns the sign of a number: -1 if negative, 0 if zero, +1 if positive.

---

### INT

- **Syntax**: `INT(expr)`
- **Abbreviation**: none

Truncates to the next lower integer (floor function).

```
INT(3.9)    : REM 3
INT(-3.1)   : REM -4
```

---

### SQR

- **Syntax**: `SQR(expr)`
- **Abbreviation**: SQ.

Returns the square root. `expr` must be ≥ 0.

**ERROR 39** if `expr` is negative.

---

### RND

- **Syntax**: `RND n` where n is a positive integer
- **Abbreviation**: RN.

> **Warning**: On the PC-1500, `RND n` returns a random **integer from 1 to n** — not a 0-to-1 float as in many other BASICs.

Call `RANDOM` before first use to seed the generator.

```
A = RND 6           : REM simulates a die roll, result 1-6
A = RND 100         : REM random integer 1-100
```

---

### RANDOM

- **Syntax**: `RANDOM` or `RANDOM n`
- **Abbreviation**: RA. RAN. RAND. RANDO.

Seeds the random number generator.

`RANDOM` (no argument): seeds from the system clock — produces a different sequence each time.
`RANDOM n`: seeds with integer n — the same seed always produces the same sequence (useful for testing).

```
RANDOM              : REM seed from clock (typical use)
RANDOM 42           : REM reproducible sequence
```

---

### SIN, COS, TAN

- **Syntax**: `SIN(angle)`, `COS(angle)`, `TAN(angle)`
- **SIN abbreviation**: SI.  **COS**: none.  **TAN**: TA.

Trigonometric functions. The angle is in the current angle mode (`DEGREE`, `RADIAN`, or `GRAD`).

**ERROR 39** if `TAN` is called at 90°, 270°, or their equivalents in other modes.

---

### ASN, ACS, ATN

- **Syntax**: `ASN(expr)`, `ACS(expr)`, `ATN(expr)`
- **ASN abbreviation**: AS.  **ACS**: AC.  **ATN**: AT.

Inverse trigonometric functions. Results are in the current angle mode.

`ASN` and `ACS` require -1 ≤ expr ≤ 1.
**ERROR 39** if `expr` is out of range for `ASN` or `ACS`.

---

### EXP

- **Syntax**: `EXP(expr)`
- **Abbreviation**: EX.

Returns e^x. `expr` must be ≤ 230.2585092.

---

### LOG

- **Syntax**: `LOG(expr)`
- **Abbreviation**: LO.

Base-10 logarithm. `expr` must be > 0.

---

### LN

- **Syntax**: `LN(expr)`
- **Abbreviation**: none

Natural logarithm (base e). `expr` must be > 0.

> **Note**: `LN` is a reserved name and cannot be used as a variable.

---

### PI

- **Syntax**: `PI`
- **Abbreviation**: none

Returns π = 3.141592654.

> **Note**: `PI` is a reserved name and cannot be used as a variable.

---

### DEG

- **Syntax**: `DEG(radians)`
- **Abbreviation**: none (for the function form)

Converts a radian value to decimal degrees, regardless of the current angle mode.

> **Warning**: `DEG` without parentheses is the `DEGREE` mode-setting command. `DEG(x)` with parentheses is the conversion function. These are different.

---

### DMS

- **Syntax**: `DMS(decimal_degrees)`
- **Abbreviation**: DM.

Converts decimal degrees to degrees-minutes-seconds format. The result is encoded as DDMMSS.ss (degrees, minutes, seconds).

---

### Exponentiation

- **Syntax**: `x ^ y`

Raises x to the power y.

> **Note**: Unary minus binds loosely around `^`. The expression `-5^4` is evaluated as `-(5^4)` = -625, **not** `(-5)^4` = 625. Use parentheses when in doubt.

Valid range: -1×10¹⁰⁰ < result < 1×10¹⁰⁰.

---

## Angle Modes

### DEGREE

- **Syntax**: `DEGREE`
- **Abbreviation**: DE. DEG. DEGR. DEGRE.

Sets the angle mode to degrees (360° = full circle). Affects `SIN`, `COS`, `TAN`, `ASN`, `ACS`, `ATN`.

---

### RADIAN

- **Syntax**: `RADIAN`
- **Abbreviation**: RAD. RADI. RADIA.

Sets the angle mode to radians (2π = full circle).

---

### GRAD

- **Syntax**: `GRAD`
- **Abbreviation**: GR. GRA.

Sets the angle mode to gradians (400 grad = full circle).

---

## String Functions

### LEN

- **Syntax**: `LEN(string$)`
- **Abbreviation**: none

Returns the number of characters in the string.

```
LEN("HELLO")    : REM 5
LEN(A$)
```

---

### LEFT$

- **Syntax**: `LEFT$(string$, n)`
- **Abbreviation**: LEF. LEFT.

Returns the leftmost n characters.

```
LEFT$("ABCDE", 3)    : REM "ABC"
```

---

### RIGHT$

- **Syntax**: `RIGHT$(string$, n)`
- **Abbreviation**: RI. RIG. RIGH. RIGHT.

Returns the rightmost n characters.

```
RIGHT$("ABCDE", 3)    : REM "CDE"
```

---

### MID$

- **Syntax**: `MID$(string$, start [, length])`
- **Abbreviation**: MI. MID.

Returns a substring starting at position `start` (1-based). If `length` is omitted, returns from `start` to the end of the string.

```
MID$("ABCDE", 2, 3)    : REM "BCD"
MID$("ABCDE", 3)       : REM "CDE"
```

---

### CHR$

- **Syntax**: `CHR$(n)`
- **Abbreviation**: CH. CHR.

Returns the character with ASCII code n.

```
CHR$(65)    : REM "A"
CHR$(13)    : REM carriage return
```

---

### ASC

- **Syntax**: `ASC(string$)`
- **Abbreviation**: none

Returns the ASCII code of the first character of the string.

```
ASC("A")       : REM 65
ASC("HELLO")   : REM 72 (code of "H")
```

---

### STR$

- **Syntax**: `STR$(expr)`
- **Abbreviation**: STR.

Converts a number to its string representation.

```
STR$(42)      : REM "42"
STR$(3.14)    : REM "3.14"
```

---

### SPACE$

- **Syntax**: `SPACE$(n)`
- **Abbreviation**: none

Returns a string of `n` space characters. `n` must be 0–80.

```
PRINT "NAME:" ; SPACE$(5) ; "AGE"
A$ = LEFT$(B$, 10) + SPACE$(10 - LEN(B$))   : REM pad to 10 chars
```

---

### VAL

- **Syntax**: `VAL(string$)`
- **Abbreviation**: V. VA.

Converts a string to a number. Returns 0 if the string does not begin with a numeric character.

```
VAL("42")       : REM 42
VAL("3.14X")    : REM 3.14
VAL("HELLO")    : REM 0
```

---

### String Concatenation

Use `+` to join strings:

```
A$ = "HEL" + "LO"    : REM "HELLO"
N$ = F$ + " " + L$
```

String operations use an 80-character temporary buffer. **ERROR 15** if the result exceeds 80 characters.

---

### String Comparison

Strings can be compared with `=`, `<>`, `<`, and `>`.

> **Warning**: There is **no** `<=` or `>=` for strings. Use `NOT (A$ > B$)` to test ≤.

When comparing strings of unequal length, the shorter string is padded with NUL (ASCII 0) for comparison purposes.

```
IF A$ = "YES" THEN GOSUB 100
IF A$ > B$ THEN PRINT "A COMES AFTER B"
```

---

## Data and Arrays

### LET

- **Syntax**: `LET var = expr`
- **Abbreviation**: LE.

Assigns a value to a variable. The keyword `LET` is optional in most contexts.

> **Warning**: After `THEN` in an `IF` statement, `LET` is **required** for assignments. `IF X>5 THEN B=1` causes **ERROR 19**. Write `IF X>5 THEN LET B=1`.

---

### DIM

- **Syntax**: Various forms:
  - `DIM name(size)` — 1D numeric array; elements 0 through size (size must be 0–255)
  - `DIM name$(size)` — 1D string array; default 16 characters per element
  - `DIM name$(size)*length` — 1D string array with explicit max length per element (1–80)
  - `DIM name(rows, cols)` — 2D numeric array (maximum 2 dimensions)
  - `DIM name$(rows, cols)*length` — 2D string array
- **Abbreviation**: D. DI.

A scalar variable and an array with the same base name are separate: `A` and `A(0)` are distinct variables.

**ERROR 5**: attempting to re-DIM an already-declared array.
**ERROR 6**: using array notation on a name that has not been DIMensioned.
**ERROR 8**: more than 2 dimensions.
**ERROR 9**: subscript out of range.

```
DIM A(10)               : REM elements A(0) through A(10)
DIM B$(5)*20            : REM 6 string elements, max 20 chars each
DIM M(4, 4)             : REM 5x5 matrix
```

---

### DATA

- **Syntax**: `DATA value1 [, value2 ...]`
- **Abbreviation**: DA. DAT.

Stores constant values in the program to be read by `READ`. `DATA` lines are not executed — they are only accessed when `READ` runs.

`DATA` lines can carry a single-character label for use with `RESTORE`:

```
20 "A" : DATA 10, 20, 30
```

---

### READ

- **Syntax**: `READ var1 [, var2 ...]`
- **Abbreviation**: REA.

Reads the next value(s) from `DATA` statements into variables. The pointer advances with each `READ`. `RESTORE` resets the pointer.

**ERROR 4** if there are no more `DATA` values to read.

```
10 DATA 1, 2, 3, 4, 5
20 FOR I = 1 TO 5
30   READ X : PRINT X
40 NEXT I
```

---

### RESTORE

- **Syntax**: `RESTORE`, `RESTORE line`, or `RESTORE "label"`
- **Abbreviation**: RES. REST. RESTO. RESTOR.

Resets the `DATA` pointer so subsequent `READ` statements start from the specified location.

| Form | Effect |
|------|--------|
| `RESTORE` | Reset to the first `DATA` statement |
| `RESTORE 100` | Reset to `DATA` at line 100 |
| `RESTORE "A"` | Reset to `DATA` labeled "A" |

---

### CLEAR

- **Syntax**: `CLEAR`
- **Abbreviation**: CL. CLE. CLEA.

Clears all variable values including fixed memory (A–Z, A$–Z$) and all arrays. Does not erase the program. Use this to reset variables without losing your program.

---

## System Functions

### TIME

- **Syntax (read)**: `T = TIME`
- **Syntax (set)**: `TIME = MMDDHH.MMSS`
- **Abbreviation**: TI. TIM.

Reads or sets the real-time clock. The set format packs date and time into a single number:

| Digits | Field |
|--------|-------|
| MM | Month (01–12) |
| DD | Day (01–31) |
| HH | Hour (00–23) |
| .MM | Minutes (00–59) |
| SS | Seconds (00–59) |

**ERROR 23** if any field is out of range.

```
TIME = 031014.3000    : REM March 10, 14:30:00
PRINT TIME            : REM e.g., 310145.0000
```

---

### BEEP

- **Syntax**: `BEEP count [, frequency [, duration]]` or `BEEP ON` / `BEEP OFF`
- **Abbreviation**: B. BE. BEE.

Sounds the built-in speaker.

| Parameter | Range | Meaning |
|-----------|-------|---------|
| count | 0–65535 | Number of beeps (0 = silence) |
| frequency | 0–255 | Pitch |
| duration | 0–65279 | Length of each beep |

`BEEP ON`: enables the speaker.
`BEEP OFF`: disables the speaker.

```
BEEP 1              : REM single beep
BEEP 3, 100, 500    : REM three beeps at medium pitch
BEEP OFF            : REM silence all beeps
```

---

### STATUS

- **Syntax**: `STATUS n`
- **Abbreviation**: STA. STAT. STATU.

Returns memory and execution information depending on the argument:

| Argument | Returns |
|----------|---------|
| `STATUS 0` | Free program steps remaining (same as `MEM`) |
| `STATUS 1` | Program steps currently used |
| `STATUS 2` | Address of the byte immediately following the last BASIC program byte |
| `STATUS 3` | Starting address of the variable data area |
| `STATUS 4` | Line number most recently executed; 0 if program is not running |

`STATUS 0` and `STATUS 1` are the most useful day-to-day. `STATUS 2` and `STATUS 3` are used together with `PEEK`, `POKE`, and `CALL` for machine-language work. `STATUS 4` inside an `ON ERROR GOTO` handler returns the same value as `ERL`.

```
PRINT STATUS 0    : REM free steps
PRINT STATUS 1    : REM used steps
IF STATUS 0 < 50 THEN PRINT "LOW MEMORY"
```

---

### OFF

- **Syntax**: `OFF`
- **Abbreviation**: none

Powers off the PC-1500. Memory contents are preserved (CMOS battery backup). The unit resumes in RUN mode when switched on again.

```
10 PRINT "SHUTTING DOWN" : PAUSE
20 OFF
```

---

### MEM

- **Syntax**: `MEM`
- **Abbreviation**: M. ME.

Returns the number of free program steps. Equivalent to `STATUS 0`.

---

### LOCK / UNLOCK

- **LOCK** — disables the MODE key, locking the computer in the current mode (RUN/PRO/RESERVE). **Abbreviation**: LOC.
- **UNLOCK** — re-enables the MODE key. **Abbreviation**: UN. UNL. UNLO. UNLOC.

Useful in programs to prevent accidental mode changes.

```
LOCK
...program runs here without risk of MODE key...
UNLOCK
```

---

### TRON / TROFF

- **TRON** — enables trace mode. Each line number is displayed as it executes. **Abbreviation**: TR. TRO.
- **TROFF** — disables trace mode. **Abbreviation**: TROF.

Use `TRON` while debugging to watch execution flow. TRON/TROFF state is not reset by `RUN`.

---

## DEF Key and Labeled Programs

### Labels

Any line can carry a single-character label:

```
10 "X" : PRINT "HELLO"
```

In RUN mode, pressing DEF + a key starts execution from the line with that label.

Valid label keys: `A S D F G H J K L Z X C V B N M SPACE =`

**ERROR 11** if the label does not exist.

### AREAD

- **Syntax**: `AREAD variable` (must appear on the same line as the label)
- **Abbreviation**: A. AR. ARE. AREA.

When the user types a value and then presses DEF + the label key, `AREAD` captures that value into the variable before execution continues.

```
10 "T" : AREAD TM : TIME = TM
```

The user types a time value (e.g., `031014.3000`), then presses DEF+T, and the program sets the clock.

### Reserve Keys

RESERVE mode lets you assign frequently typed phrases or commands to the six **Reserve keys** in the top row of the keyboard, labeled `!`, `"`, `#`, `$`, `%`, and `&`.

Each Reserve key can hold **three stored phrases** (groups I, II, and III), for a total of 18 stored phrases. The **Reserve Select key** (`÷`, lower-left corner) cycles through the three groups; the currently active group is shown as a Roman numeral (I, II, or III) at the top of the display.

**Entering RESERVE mode**: press **SHIFT+MODE**. The display shows `RESERVE`. To exit, press MODE.

**Assigning a phrase to a key**:
1. Press SHIFT+MODE to enter RESERVE mode.
2. Press the Reserve Select key to choose group I, II, or III.
3. Press the desired Reserve key (`!`, `"`, `#`, `$`, `%`, or `&`). The display shows a prompt like `F6 :`.
4. Type the phrase and press ENTER.

The phrase is now stored. Return to RUN mode (press MODE) and press that Reserve key — the phrase appears on the display ready to execute. Press ENTER to run it.

**Auto-execute with `@`**: If the stored phrase ends with `@`, pressing the Reserve key in RUN mode executes the phrase immediately without needing a separate ENTER press. Example: storing `GOTO 100@` causes the program to jump to line 100 the moment the key is pressed.

**Templates**: Each group can have a label string (up to 26 characters) that reminds you which function is on which key. To create a template, enter RESERVE mode, select the group, then type the label text and press ENTER (without pressing a Reserve key first). To display the template in RUN mode, press the **RCL** key; press RCL again to dismiss it.

**Clearing all reserve memories**: In RESERVE mode, press N, E, W, ENTER.

---

## Logic and Bitwise Operations

### AND, OR, NOT

- `expr1 AND expr2` — bitwise AND. **Abbreviation**: AN.
- `expr1 OR expr2` — bitwise OR. **Abbreviation**: none.
- `NOT expr` — bitwise complement. **Abbreviation**: NO.

> **Warning**: These are **bitwise** operations on integers, not boolean short-circuit operators. Both sides of `AND`/`OR` are always evaluated. Values must be integers in the range -32768 to 32767.

**ERROR 19** if either operand is outside -32768 to 32767.

```
10 OR &F        : REM 1010 OR 1111 = 1111 = 15
NOT 0           : REM -1
NOT 55          : REM -56
12 AND 10       : REM 1100 AND 1010 = 1000 = 8
```

Because `AND` and `OR` are bitwise, use them for flag masking, not for combining `IF` conditions. To test two conditions together:

```
IF X > 0 THEN IF Y > 0 THEN PRINT "BOTH POSITIVE"
```

---

## Low-Level Access

### PEEK / POKE

- **Syntax**: `PEEK(address)` — reads a byte from a memory address
- **Syntax**: `POKE address, value` — writes a byte to a memory address
- **Abbreviation**: none (for both)

Provides direct access to RAM locations.

---

### PEEK# / POKE#

- **Syntax**: `PEEK#(port)` — reads a byte from an I/O port. **Abbreviation**: PE.
- **Syntax**: `POKE# port, value` — writes a byte to an I/O port. **Abbreviation**: PO.

Provides direct access to hardware I/O ports.

---

### CALL

- **Syntax**: `CALL address [, param ...]`
- **Abbreviation**: CA.

Calls a machine language subroutine at the given address. Parameters are passed as specified by the subroutine's calling convention.

---

## Cassette and Printer (CE-150 Required)

All cassette and printer commands require the **Sharp CE-150 Printer/Plotter/Cassette Interface** to be physically connected. See [CE-150-Reference.md](CE-150-Reference.md) for the complete command reference.

---

## Appendix A: Operator Precedence

Operators are evaluated in this order (highest to lowest). Same-priority operators evaluate left to right.

| Priority | Operators |
|----------|-----------|
| 1 (highest) | Parentheses — innermost first |
| 2 | Variable/constant retrieval: `TIME`, `PI`, `MEM`, `INKEY$` |
| 3 | Functions: `SIN`, `COS`, `LOG`, `EXP`, `LEN`, `LEFT$`, etc. |
| 4 | Exponentiation: `^` |
| 5 | Unary sign: `+` and `-` (note: `-5^4` = `-(5^4)` = -625) |
| 6 | Multiplication, Division: `*`, `/` |
| 7 | Addition, Subtraction: `+`, `-` |
| 8 | Comparison: `<`, `<=`, `=`, `>=`, `>`, `<>` |
| 9 (lowest) | Bitwise/Logical: `AND`, `OR`, `NOT` |

---

## Appendix B: Program Initiation Comparison

| Effect | `RUN` | `GOTO` | DEF+key |
|--------|:-----:|:------:|:-------:|
| Display cleared | Yes | Yes | No |
| Text cursor to column 0 | Yes | No | No |
| Fixed memory (A–Z, A$–Z$) cleared | No | No | No |
| Main memory variables cleared | Yes | No | No |
| FOR-NEXT / GOSUB stack cleared | Yes | Yes | Yes |
| ON ERROR GOTO cancelled | Yes | No | No |
| DATA pointer reset to start | Yes | No | No |
| USING format cancelled | Yes | No | No |

---

## Appendix C: Command Abbreviations

Type the portion before the period, then press the abbreviation key (the first letter of what follows the period acts as a shortcut in the PC-1500 entry system).

| Command | Abbreviations |
|---------|--------------|
| ABS | AB. |
| ACS | AC. |
| AND | AN. |
| AREAD | A. AR. ARE. AREA. |
| ARUN | ARU. |
| ASN | AS. |
| ATN | AT. |
| BEEP | B. BE. BEE. |
| CALL | CA. |
| CHAIN | CHA. CHAI. |
| CHR$ | CH. CHR. |
| CLEAR | CL. CLE. CLEA. |
| CLOAD | CLO. CLOA. |
| CLOAD? | CLO.? CLOA.? |
| CONT | C. CO. CON. |
| CSAVE | CS. CSA. CSAV. |
| CURSOR | CU. CUR. CURS. CURSO. |
| DATA | DA. DAT. |
| DEGREE | DE. DEG. DEGR. DEGRE. |
| DIM | D. DI. |
| DMS | DM. |
| END | E. EN. |
| ERROR | ER. ERR. ERRO. |
| EXP | EX. |
| FOR | F. FO. |
| GCURSOR | GC. GCU. GCUR. GCURS. GCURSO. |
| GOSUB | GOS. GOSU. |
| GOTO | G. GO. GOT. |
| GPRINT | GP. GPR. GPRI. GPRIN. |
| GRAD | GR. GRA. |
| IF | none |
| INPUT | I. IN. INP. INPU. |
| INKEY$ | INK. INKE. INKEY. |
| LEFT$ | LEF. LEFT. |
| LEN | none |
| LET | LE. |
| LIST | L. LI. LIS. |
| LOCK | LOC. |
| LOG | LO. |
| MEM | M. ME. |
| MERGE | MER. MERG. |
| MID$ | MI. MID. |
| NEW | none |
| NEXT | N. NE. NEX. |
| NOT | NO. |
| ON | O. |
| PAUSE | PA. PAU. PAUS. |
| PEEK# | PE. |
| POINT | POI. POIN. |
| POKE# | PO. |
| PRINT | P. PR. PRI. PRIN. |
| RADIAN | RAD. RADI. RADIA. |
| RANDOM | RA. RAN. RAND. RANDO. |
| READ | REA. |
| RESTORE | RES. REST. RESTO. RESTOR. |
| RETURN | RE. RET. RETU. RETUR. |
| RIGHT$ | RI. RIG. RIGH. RIGHT. |
| RMT OFF | RM.OF. RMTOF. |
| RMT ON | RM.O. RMTO. |
| RND | RN. |
| RUN | R. RU. |
| SGN | SG. |
| SIN | SI. |
| SQR | SQ. |
| STATUS | STA. STAT. STATU. |
| STEP | STE. |
| STOP | S. ST. STO. |
| STR$ | STR. |
| TAN | TA. |
| THEN | T. TH. THE. |
| TIME | TI. TIM. |
| TROFF | TROF. |
| TRON | TR. TRO. |
| UNLOCK | UN. UNL. UNLO. UNLOC. |
| USING | U. US. USI. USIN. |
| VAL | V. VA. |
| WAIT | W. WA. WAI. |

---

## Appendix D: ASCII Character Chart

The PC-1500 uses a standard ASCII character set for codes 32–126, plus a small set of special characters at higher codes.

### Printable ASCII (codes 32–126)

| Code | Char | Code | Char | Code | Char | Code | Char |
|------|------|------|------|------|------|------|------|
| 32 | (space) | 56 | 8 | 80 | P | 104 | h |
| 33 | ! | 57 | 9 | 81 | Q | 105 | i |
| 34 | " | 58 | : | 82 | R | 106 | j |
| 35 | # | 59 | ; | 83 | S | 107 | k |
| 36 | $ | 60 | < | 84 | T | 108 | l |
| 37 | % | 61 | = | 85 | U | 109 | m |
| 38 | & | 62 | > | 86 | V | 110 | n |
| 39 | ' | 63 | ? | 87 | W | 111 | o |
| 40 | ( | 64 | @ | 88 | X | 112 | p |
| 41 | ) | 65 | A | 89 | Y | 113 | q |
| 42 | * | 66 | B | 90 | Z | 114 | r |
| 43 | + | 67 | C | 91 | [ | 115 | s |
| 44 | , | 68 | D | 92 | \ | 116 | t |
| 45 | - | 69 | E | 93 | ] | 117 | u |
| 46 | . | 70 | F | 94 | ^ | 118 | v |
| 47 | / | 71 | G | 95 | _ | 119 | w |
| 48 | 0 | 72 | H | 96 | ` | 120 | x |
| 49 | 1 | 73 | I | 97 | a | 121 | y |
| 50 | 2 | 74 | J | 98 | b | 122 | z |
| 51 | 3 | 75 | K | 99 | c | 123 | { |
| 52 | 4 | 76 | L | 100 | d | 124 | \| |
| 53 | 5 | 77 | M | 101 | e | 125 | } |
| 54 | 6 | 78 | N | 102 | f | 126 | ~ |
| 55 | 7 | 79 | O | 103 | g | | |

### PC-1500 Special Characters

| Code | Character | Description |
|------|-----------|-------------|
| 127 | (DEL) | Delete / rubout |
| 128 | √ | Square root symbol |
| 129 | π | Pi symbol |
| 130 | ¥ | Yen sign |
| 131 | ▪ | Filled square / bullet |

Use `CHR$(n)` to produce special characters in strings and `ASC(s$)` to retrieve the code of a character.

```
PRINT CHR$(128)    : REM displays √
PRINT CHR$(129)    : REM displays π
```

---

## Error Codes

See the [Error Code Reference](Error-Codes.md) for all error codes, including CE-150 and CE-158 errors.

---

## CE-158 RS-232C Interface Commands

All CE-158 commands require the **Sharp CE-158** RS-232C/parallel interface to be physically connected. See [CE-158-Reference.md](CE-158-Reference.md) for the complete command reference.
