# Sharp PC-1500 BASIC Reference

→ [README](README.md) · [Command Index](PC-1500-Command-Index.md) · [Error Code Reference](PC-1500-Error-Codes.md) · [PC-1600 BASIC Reference](PC-1600-BASIC-Reference.md)

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

→ [Error Code Reference](PC-1500-Error-Codes.md)

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

Function that returns the **error number** of the most recent error. Use inside an `ON ERROR GOTO` handler. See the [Error Code Reference](PC-1500-Error-Codes.md) for a full list of codes.

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

> **Warning**: When no key is pressed, `INKEY$` returns NUL (CHR$(0)), which is the same as ""

To wait for any key:
```
10 IF INKEY$ <> "" GOTO 10
```

To wait for a specific key:
```
10 LET K$ = INKEY$
20 IF K$ = "" GOTO 10
30 IF K$ = "A" GOSUB 200
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

## CE-150 Printer/Plotter/Cassette Interface

The **Sharp CE-150** is a Printer/Cassette Interface for the Sharp PC-1500 pocket computer. It provides a 4-color pen plotter/printer and a cassette tape interface for saving and loading programs and data.

**All CE-150 commands require the CE-150 to be physically connected to the PC-1500.** They will not work on the standalone PC-1500.

### Key Features

- **4-color pen plotter/printer**: Black, Blue, Green, and Red pens (conventional assignment)
- **Two printer modes**: TEXT mode for character printing; GRAPH mode for X-Y plotting
- **Cassette tape interface**: Save and load programs and data; chain programs together
- **Roll paper**: 56mm wide paper roll (continuous tape; the tape can also be rolled back)

### Hardware Specifications

| Specification | Value |
|---------------|-------|
| Paper width | 56mm roll paper (continuous tape, no page length; feeds forward and back) |
| Printable width (X) | X = 0 to 216 plotter units ≈ 42.75mm (0.198mm per unit); the pen clips at this edge |
| Pen colors | 4 pens (slots 0-3 clockwise from position-detecting magnet) |
| Character sizes | 9 sizes (CSIZE 1-9) |
| Characters per line | 4, 5, 6, 7, 9, 12, 18, or 36 depending on CSIZE |
| Character height range | 1.2mm (CSIZE 1) to 10.8mm (CSIZE 9) |
| Maximum print speed | 11 characters/second at smallest size |
| Rotation directions | 4 (0=normal, 1=downward, 2=upside-down, 3=upward) |
| Coordinate range | -2048 to +2047 (X and Y) |

### Pen Colors

The four pen slots are numbered 0-3 clockwise from the position-detecting magnet. The conventional assignment is:

| Slot | Color |
|------|-------|
| 0 | Black |
| 1 | Blue |
| 2 | Green |
| 3 | Red |

The actual color depends on which pens are physically loaded. Use the `TEST` command to draw a sample box in each color and verify the assignment.

---

## CE-150 — Printer Modes

The CE-150 is always in one of two modes:

- **TEXT mode**: For printing text and numbers. The paper is divided into columns based on the current CSIZE setting. Use `LPRINT`, `LCURSOR`, `TAB`, and `LF`.
- **GRAPH mode**: For X-Y plotting and drawing. Use `GLCURSOR`, `LINE`, `RLINE`, and `SORGN`.

Switch modes with `TEXT` (enters TEXT mode) or `GRAPH` (enters GRAPH mode, moves pen to far left).

Some commands cause automatic mode switching — for example, `LPRINT` forces TEXT mode.

---

## CE-150 — Setup

1. Always turn the PC-1500 **OFF** before connecting or disconnecting the CE-150.
2. After connecting, turn ON. If **ERROR 80** or **ERROR 78** appears, the CE-150 battery needs charging.
3. Load four pens into the pen carousel. Operating with missing pens may cause color errors.
4. Use the `TEST` command to verify which physical pen corresponds to each color number.

---

## CE-150 — Command Reference

### TEXT

- **Syntax**: `TEXT`
- **Abbreviation**: TEX.

Switches the CE-150 to TEXT mode for character printing. TEXT mode divides the paper into columns based on the current CSIZE setting.

---

### GRAPH

- **Syntax**: `GRAPH`
- **Abbreviation**: GRAP.

Switches the CE-150 to GRAPH mode for X-Y plotting. Moves the pen to the far left side of the paper. The pen coordinate origin is wherever `SORGN` was last set.

---

### CSIZE — Character Size

- **Syntax**: `CSIZE n` where n is 1-9
- **Abbreviations**: CSI. CSIZ.

Sets the character size for all subsequent TEXT mode printing. Valid in either mode, but only affects TEXT mode output.

| CSIZE | Chars/line | Height | Width |
|-------|-----------|--------|-------|
| 1     | 36        | 1.2mm  | 0.8mm |
| 2     | 18        | 2.4mm  | 1.6mm |
| 3     | 12        | 3.6mm  | 2.4mm |
| 4     | 9         | 4.8mm  | 3.2mm |
| 5     | 7         | 6.0mm  | 4.0mm |
| 6     | 6         | 7.2mm  | 4.8mm |
| 7     | 5         | 8.4mm  | 5.6mm |
| 8     | 4         | 9.6mm  | 6.4mm |
| 9     | 4         | 10.8mm | 7.2mm |

---

### ROTATE

- **Syntax**: `ROTATE n` where n is 0-3
- **Abbreviations**: RO. ROT. ROTA. ROTAT.
- **GRAPH mode only**

Sets the direction for subsequent printing and drawing.

| Value | Direction |
|-------|-----------|
| 0 | Left to right (normal) |
| 1 | Downward |
| 2 | Right to left (upside down) |
| 3 | Upward |

Has no effect in TEXT mode.

---

### COLOR

- **Syntax**: `COLOR n` where n is 0-3
- **Abbreviations**: COL. COLO.

Selects the pen for subsequent printing and drawing. Values 0-3 correspond to pen slots (clockwise from position-detecting magnet).

- Non-integer values 0-3 are truncated to integer.
- Values outside 0-3 cause **ERROR 19**.
- In TEXT mode: issuing COLOR resets the pen to the left side of the paper.
- In GRAPH mode: pen returns to its previous position after the color change.
- After power-on: pen 0 (Color 0) is selected.

---

### TEST

- **Syntax**: `TEST`
- **Abbreviations**: TE. TES.

Draws four 5mm×5mm boxes using pens 0, 1, 2, and 3 (from left to right). Use this to verify which physical pen corresponds to each color number.

---

### LF — Line Feed

- **Syntax**: `LF n`
- **Abbreviation**: none (must type LF in full)
- **TEXT mode only**

Moves the paper forward (positive n) or backward (negative n) by n lines. The distance moved per line depends on the current CSIZE setting.

Maximum backward movement is 10.24 cm (about 4 inches). If this is exceeded, **ERROR 71** occurs.

**Note**: Do not insert paper while the paper feed mechanism is operating.

---

### LPRINT

- **Syntax**: `LPRINT [item [, item] [; item] ...]` or `LPRINT` alone for a blank line
- **Abbreviations**: LP. LPR. LPRI. LPRIN.

The main command for printing text and numbers to the CE-150. Similar to `PRINT` but output goes to the printer. Forces TEXT mode.

- **Semicolons (`;`)** between items: minimum spacing, items grouped on the same line.
- **Commas (`,`)** between items: separates into left and right halves of the line.
- **`LPRINT` alone**: carriage return + single line feed (does not reset GRAPH mode coordinates).
- Numeric item too wide for current CSIZE: **ERROR 76**.
- String items that overflow the line are wrapped to the next line.

Supports a `USING` clause for formatted output:

```
LPRINT USING "###.##"; value
```

---

### LCURSOR — Line Cursor

- **Syntax**: `LCURSOR n`
- **Abbreviations**: LCU. LCUR. LCURS. LCURSO.
- **TEXT mode only**

Positions the pen at character column n on the current print line. The maximum column depends on the current CSIZE (e.g., CSIZE 1 allows columns 0-35). Analogous to the `CURSOR` command on the PC-1500 display.

---

### TAB

- **Syntax**: `LPRINT TAB n; item-list`
- **Abbreviation**: none (must type TAB in full)
- **TEXT mode only**

Positions the pen at column n within an `LPRINT` statement. Similar to `LCURSOR` but used inside an `LPRINT`. If the item-list is empty, the result is a line feed.

**ERROR 72** if n is invalid for the current CSIZE.

---

### LLIST

- **Syntax**: Multiple forms (same line range syntax as the `LIST` command, but output goes to the CE-150 printer):
  - `LLIST` — prints entire program
  - `LLIST n` — prints only line n
  - `LLIST ,n` — prints all lines up to and including line n
  - `LLIST n,` — prints all lines from line n to end
  - `LLIST n1, n2` — prints lines n1 through n2
  - `LLIST "label"` — prints the line containing that label
  - `LLIST "label",` — prints from labeled line to end
- **Abbreviations**: LL. LLI. LLIS.

Forces TEXT mode. Non-existent label causes **ERROR 11**.

---

### GLCURSOR — Graphics Cursor

- **Syntax**: `GLCURSOR (x, y)`
- **Abbreviations**: GL. GLC. GLCU. GLCUR. GLCURS. GLCURSO.
- **GRAPH mode only**

Moves the pen to position (x, y) without drawing a line. Coordinates are relative to the current origin (set by `SORGN`). Both x and y must be in the range -2047 to +2047.

If the destination is outside the drawable area, the pen moves as far as it can (the line is "cut off" at the edge), but the internal counters continue tracking toward the goal.

**ERROR 70** if coordinates exceed the range -2048 to +2047.

---

### SORGN — Set Origin

- **Syntax**: `SORGN`
- **Abbreviations**: SO. SOR. SORG.
- **GRAPH mode only** — takes no parameters

Sets the **current pen position** as the new origin (0, 0) for subsequent graphing commands. Typically used after `GLCURSOR` or `LINE` to establish a new reference point.

**Important**: If the pen has moved outside the drawable area, `SORGN` will set the origin at the imaginary position. Subsequent commands will have no effect until the pen returns to the drawable area.

Example:

```
10 GRAPH
20 LINE (0,0)-(100,100), 9       : REM move pen to (100,100) with pen up
30 SORGN                          : REM set new origin at (100,100)
40 LINE (0,0)-(10,10), 0, 0, B   : REM draw 10x10 box at new origin
50 TEXT
```

---

### LINE

- **Syntax**:
  - `LINE (X1,Y1)-(X2,Y2) [, line-type [, color [, B]]]`
  - Multi-point: `LINE (X1,Y1)-(X2,Y2)-...-(X6,Y6) [, line-type [, color]]`
- **Abbreviation**: LIN.
- **GRAPH mode only**

Draws a line from (X1,Y1) to (X2,Y2). All coordinates must be in the range -2048 to +2047.

**line-type** (0-9):

| Value | Effect |
|-------|--------|
| 0 | Solid line |
| 1-8 | Dashed lines (increasing dash length: 0.4mm to 1.8mm) |
| 9 | Pen up — moves without drawing |

**color**: 0-3, selects the pen. If omitted, uses the current COLOR setting.

**B**: draws a Box using the two points as diagonal corners. Cannot be used with the multi-point form.

Omitted parameters use the previous values.

The multi-point form connects up to 6 points. **ERROR 74** if more than 6 points are specified.

**ERROR 70** if any coordinate is out of range.

Examples:

```
LINE (0,0)-(100,50)              : REM solid line, current color
LINE (0,0)-(100,50), 2, 1        : REM 0.6mm dashes, pen 1
LINE (50,50)-(100,100), 0, 0, B  : REM solid box, pen 0
LINE (0,0)-(9,9), 9              : REM pen-up move (no line drawn)
```

---

### RLINE — Relative Line

- **Syntax**: Same as LINE but all coordinates are relative to the current pen position:
  - `RLINE (dX1,dY1)-(dX2,dY2) [, line-type [, color [, B]]]`
- **Abbreviations**: RL. RLI. RLIN.
- **GRAPH mode only**

All coordinate pairs are offsets from the current pen position, not absolute coordinates. If the pen goes off the paper edge, it is lifted. Otherwise, same parameters and behavior as `LINE`.

---

### CSAVE — Cassette Save

- **Syntax**: `CSAVE ["filename"]` or `CSAVE-1 ["filename"]`
- **Abbreviations**: CS. CSA. CSAV.

Saves the current program to cassette tape. The filename may be up to 16 characters; excess characters are ignored. `CSAVE` with no filename saves without a filename.

`CSAVE-1` saves to a second tape recorder connected to the REM 1 terminal.

The "BUSY" indicator lights during the save operation. Note the tape counter number before saving so you can find the program again. Always verify the save with `CLOAD?` before relying on it.

---

### CLOAD — Cassette Load

- **Syntax**: `CLOAD ["filename"]` or `CLOAD-1 ["filename"]`
- **Abbreviations**: CLO. CLOA.

Loads a program from cassette tape into memory, replacing the current program. Searches the tape for a matching filename. `CLOAD` with no filename loads the first program found.

`CLOAD-1` loads from a second tape recorder.

---

### CLOAD? — Cassette Load Verify

- **Syntax**: `CLOAD? ["filename"]` or `CLOAD?-1 ["filename"]`
- **Abbreviations**: CLO.? CLOA.?

Compares the tape contents with the program currently in memory without loading. Use this after `CSAVE` to confirm the save was successful.

- If they match: displays the filename and ends normally.
- If they differ: **ERROR 43**.
- Checksum error: **ERROR 44**.

---

### MERGE

- **Syntax**: `MERGE ["filename"]` or `MERGE-1 ["filename"]`
- **Abbreviations**: MER. MERG.

Loads a program from tape without clearing the current program. Lines from tape are added to memory. If the tape contains duplicate line numbers, both copies will exist in memory. Useful for combining program sections.

---

### CHAIN

- **Syntax**: `CHAIN "filename" [, linenumber]` or `CHAIN-1 "filename" [, linenumber]`
- **Abbreviations**: CHA. CHAI.
- **Statement only** — cannot be used as a direct command

When executed, loads the named program from tape and begins execution immediately. If a line number is specified, execution starts at that line in the loaded program. **Variables are preserved** (not cleared).

Use `CHAIN` for programs too large to fit in memory at once: divide the program into sections, each ending with `CHAIN` to load the next section.

`CHAIN-1` uses a second tape recorder.

Example of chained programs:

```
1000 CHAIN "PROG2", 1010   : REM end of first section; loads PROG2 starting at line 1010
```

---

### PRINT# — Print Variables to Tape

- **Syntax**:
  - `PRINT # [; "filename"] ; variable [, variable ...]`
  - `PRINT #-1 [; "filename"] ; variable [, variable ...]`
  - `PRINT # "filename" ; B(*)` — saves all varieties of B including arrays
- **Abbreviations**: P.# PR.# PRI.# PRIN.#

Saves variable values (not a program) to cassette tape. Different from `CSAVE`, which saves programs. The `(*)` wildcard saves all variations of a variable name.

---

### INPUT# — Input Variables from Tape

- **Syntax**:
  - `INPUT # [; "filename"] ; variable [, variable ...]`
  - `INPUT #-1 [; "filename"] ; variable [, variable ...]`
- **Abbreviations**: I.# IN.# INP.# INPU.#

Reads variable values from cassette tape that were previously saved with `PRINT#`.

- If the command specifies more variables than are on tape: extra variables receive the value 0.
- If the command specifies fewer variables than are on tape: extra tape values are ignored.

---

### CSAVE M — Save Machine Language

- **Syntax**: `CSAVE M address1, address2 [, address3]` or `CSAVE M-1 address1, address2 [, address3]`
- **Abbreviations**: CS. CSA. CSAV. (for CSAVE)

Saves a block of raw memory bytes to cassette tape. `address1` is the start address, `address2` is the end address. If `address3` is given, the block will auto-execute at that address when loaded with `CLOAD M`.

`CSAVE M-1` uses the second tape recorder connected to the REM 1 terminal.

```
CSAVE M &4700, &47FF, &4700   : REM save and set auto-run address
```

---

### CLOAD M — Load Machine Language

- **Syntax**: `CLOAD M [address]` or `CLOAD M-1 [address]`
- **Abbreviations**: CLO. CLOA. (for CLOAD)

Loads a machine language block from tape back into the same memory addresses used when it was saved. If `address` is specified, loads starting at that address instead. If the saved file included an auto-run address (set via the third argument of `CSAVE M`), execution jumps there automatically after loading — unless a load address override is specified.

`CLOAD M-1` uses the second tape recorder.

---

### RMT ON / RMT OFF

- **Syntax**: `RMT ON` or `RMT OFF`
- **Abbreviations**: RM.O. RMTO. (for ON); RM.OF. RMTOF. (for OFF)

Controls the remote function of the REM 1 terminal for a second tape recorder.

- `RMT ON`: enables remote control of the second tape recorder.
- `RMT OFF`: disables remote control of the second tape recorder.

Used with `CSAVE-1` and `CLOAD-1` operations.

---

## CE-150 — Coordinate System for GRAPH Mode

- The origin (0,0) is set by `SORGN` at the current pen position.
- X axis is horizontal (positive = right).
- Y axis is vertical (positive = up).
- Valid range: -2048 to +2047 for both X and Y (`ERROR 70` outside this; it is a coordinate range check, not the paper edge).
- Physical drawable area: X spans **0 to 216 plotter units ≈ 42.75mm** across the 56mm tape. The scale is **0.198mm per unit** (= 190mm / 960 units, taken from the CE-1600P, whose wider carriage gives a more accurate ruler reading; a crude ruler check of the CE-150 itself gave ~43mm, consistent). Y is unbounded — the paper is continuous tape, not a page — the only limit being the ~10.24cm maximum backward feed in TEXT mode (`ERROR 71`).
- If the pen is commanded outside the drawable area, it stops at the edge. The internal counters continue tracking the commanded position, so the pen will resume drawing correctly once it returns within bounds.

---

## CE-150 — Using Cassette Tape

Follow these steps for reliable saves:

1. Turn the "remote" switch on the CE-150 **OFF** before starting.
2. Insert the tape and advance past the leader to a blank section.
3. Set the volume to about 3/4 level (use automatic volume if available).
4. Turn the remote switch back **ON**.
5. Press **RECORD** and **PLAY** simultaneously on the tape recorder.
6. Then type the `CSAVE` command on the PC-1500.
7. After saving, always verify with `CLOAD?` before relying on the save.

---

## CE-150 — Example Programs

### Drawing a Triangle

```
10 GRAPH
15 LINE (0,0)-(100,0), 9 : SORGN    : REM move pen and set new origin
20 LINE (0,0)-(50,50)-(-50,50)-(0,0), 0, 0
30 TEXT
40 END
```

### Drawing a Box with RLINE

```
10 GRAPH
20 GLCURSOR (100, 100)             : REM move to starting position
30 RLINE -(100,50),,, B            : REM draw 100x50 box (relative)
40 TEXT
```

### Printing a Formatted Report

```
10 TEXT
20 CSIZE 2
30 LPRINT "SALES REPORT"
40 LF 1
50 CSIZE 1
60 FOR I=1 TO 5
70   LPRINT "ITEM "; I; TAB 20; I*100
80 NEXT I
90 LF 3
100 END
```

### Saving and Loading Data

```
10 A=42 : B=100 : C$="HELLO"
20 PRINT# "MYDATA"; A, B, C$     : REM save variables to tape
...
100 INPUT# "MYDATA"; A, B, C$    : REM read variables back from tape
```

### Four-Color Demo

```
10 GRAPH
20 COLOR 0 : LINE (0,0)-(50,50), 0         : REM black line
30 COLOR 1 : LINE (50,50)-(100,0), 0       : REM blue line
40 COLOR 2 : LINE (100,0)-(150,50), 0      : REM green line
50 COLOR 3 : LINE (150,50)-(200,0), 0      : REM red line
60 TEXT
```

---

## CE-150 — Internal Technical Reference

This section contains low-level details from the ROM disassembly. It is not needed for BASIC programming.

### CE-150 Working Registers (0x79E0-0x79F9)

| Address | Name | Size | Description |
|---------|------|------|-------------|
| 0x79E0-0x79E1 | USER_CTRX | 2 | User counter X (pen X coordinate) |
| 0x79E2-0x79E3 | USER_CTRY | 2 | User counter Y (pen Y coordinate) |
| 0x79E4-0x79E5 | SCIS_CTRY | 2 | Scissoring counter Y direction |
| 0x79E6 | ABS_POSX | 1 | Absolute position X counter |
| 0x79E7-0x79E8 | SCIS_EXTY | 2 | Scissoring counter X direction |
| 0x79E9 | PEN_UPDOWN | 1 | Pen up/down state |
| 0x79EA | LINE_TYPE | 1 | Line type (0-9) for GRAPH mode |
| 0x79EB | DOT_LINE_CTR | 1 | Dotted line counter |
| 0x79EC | CURR_PEN | 1 | Current pen position (00=up, 01=down) |
| 0x79ED | XMTR_HLD_CTR | 1 | X-motor hold counter |
| 0x79EE | MTR_PHASE | 1 | Motor phase (stored in Port C) |
| 0x79EF | YMTR_HLD_CTR | 1 | Y-motor hold counter |
| 0x79F0 | PRNT_MODE | 1 | Print mode (00=TEXT, FF=GRAPH) |
| 0x79F1 | PRNT_DISABLE | 1 | Printer disable flag |
| 0x79F2 | PRNT_ROTATE | 1 | ROTATE setting (0-3) |
| 0x79F3 | PRNT_COLOR | 1 | COLOR setting (0-3) |
| 0x79F4 | PRNT_CSIZE | 1 | CSIZE setting (1-9) |
| 0x79F5 | PRNT_LLPARAM | 1 | LPRINT/LLIST parameter |
| 0x79F6 | PRNT_TEMPM | 1 | LINE dir. param/LLIST LF/COLOR pen location |
| 0x79F7 | PRNT_DTYPE | 1 | Data type (00=numeric, FF=string) |
| 0x79F8 | PRNT_TEMPP | 1 | Temp storage pen location during feed |
| 0x79F9 | PRNT_PWRINT | 1 | Power up/interrupt in progress flag |

### I/O Ports (0xB00A-0xB00F)

| Address | Name | Access | Description |
|---------|------|--------|-------------|
| 0xB00A | CE150_MSK_REG | R/W | Mask register (ME1) |
| 0xB00B | CE150_IF_REG | R/W | Interrupt flag register (ME1) |
| 0xB00C | CE150_PRT_A_DIR | R/W | Port A direction register (ME1) |
| 0xB00D | CE150_PRT_B_DIR | R/W | Port B direction register (ME1) |
| 0xB00E | CE150_PRT_A | R/W | Port A data register (ME1) |
| 0xB00F | CE150_PRT_B | R/W | Port B data register (ME1) |

### ROM Function Addresses

#### Character and Graphics Functions

| Address | Function | Description |
|---------|----------|-------------|
| $A000-$A28A | PRNT_VEC | Character vectors (651 bytes) |
| $A28B | MGP1_150 | Start of MGP 1 program block |
| $A519 | COLDES | Color designation routine |
| $A769 | MOTOFF | Printer motor OFF |
| $A781 | PRINT_150 | Print ASCII character (no LF) |
| $A8DD | MOTDRV | Motor drive — move pen |
| $A951 | LFEED | Single line feed |
| $AA04 | NLFEED | Multiple line feeds (n times) |
| $AAE3 | PENUPDOWN | Pen up/down control |
| $ABEF | GRPHPREP | Switch from text to graphics mode |
| $ACA6 | TEXT | TEXT mode handler |
| $ACD3 | GRAPH | GRAPH mode handler |

#### Plotting and Graphics

| Address | Function | Description |
|---------|----------|-------------|
| $B153 | SORGN | SORGN command handler |
| $B15A | ROTATE | ROTATE command handler |
| $B16A | COLOR | COLOR command handler |
| $B180 | CSIZE | CSIZE command handler |
| $B191 | GLCURSOR | GLCURSOR command handler |
| $B1B4 | LF | LF command handler |
| $B222 | LINE | LINE command handler |
| $B224 | RLINE | RLINE command handler |
| $B2EC | LPRINT_150 | LPRINT command handler |
| $B754 | LLIST_150 | LLIST command handler |

#### Cassette Interface Functions

| Address | Function | Description |
|---------|----------|-------------|
| $B888 | SBRA4 | Subroutine A4 — CMT block 2 start |
| $B88B | SBRA8 | Subroutine A8 |
| $B88E | SBRAA | Subroutine AA |
| $B891 | SBRAE | Subroutine AE |
| $B894 | SBRB0 | Subroutine B0 |
| $B897 | SBRB2 | Subroutine B2 |
| $B89A | SBRB4 | Subroutine B4 |
| $B89D | SBRB6 | Subroutine B6 |
| $B8A0 | SBRB8 | Subroutine B8 |
| $B8A3 | PCJUMP01 | Direct PC load from $E524 |
| $B8A6 | CSAVE_150 | CSAVE handler |
| $B8F9 | CLOAD_150 | CLOAD handler |
| $B994 | MERGE_150 | MERGE handler |
| $BB6A | CHAIN_150 | CHAIN handler |
| $BBD6 | HEADERCREATE | Write tape sync header |
| $BBF5 | TERMCMTIO | Finalize tape I/O control |
| $BCE8 | HEADERIO | Read tape sync header / search filename |
| $BD3C | FILETRSF | Read/write file to tape |
| $BDCC | SAVEONECHR | Send character to tape |
| $BDF0 | LOADONECHR | Read character from tape |
| $BEF9 | RMT | RMT command handler |
| $BF11 | REMOTEON | Remote motor ON |
| $BF43 | REMOTEOFF | Remote motor OFF |

### Token Assignments

| Command | Token |
|---------|-------|
| CSIZE | 0xE680 |
| GRAPH | 0xE681 |
| GLCURSOR | 0xE682 |
| LCURSOR | 0xE683 |
| SORGN | 0xE684 |
| ROTATE | 0xE685 |
| TEXT | 0xE686 |
| RMT | 0xE7A9 |
| CHAIN | 0xF0B2 |
| COLOR | 0xF0B5 |
| LF | 0xF0B6 |
| LINE | 0xF0B7 |
| LLIST | 0xF0B8 |
| LPRINT | 0xF0B9 |
| RLINE | 0xF0BA |
| TAB | 0xF0BB |
| TEST | 0xF0BC |

---

## CE-158 Serial/Parallel Interface

The **Sharp CE-158** is an optional interface module for the PC-1500. It provides an **RS-232C serial port** and a **parallel printer port**, along with a set of new BASIC statements and functions for controlling them.

The CE-158 has its own rechargeable Ni-Cad battery. Its power switch must be **ON** before executing any CE-158 command; executing a CE-158 command with the power off causes **ERROR 50**. The battery lasts approximately 3 hours; charge with the EA-21A AC adaptor (15 hours to full charge).

The CE-158 must also be physically connected to the PC-1500 when programs using CE-158 commands are **created** or **executed**.

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

## CE-158 — Configuration

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

## CE-158 — I/O Statements and Functions

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

## CE-158 — Program Transfer

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

## CE-158 — Terminal Program Mode

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

## CE-158 — Parallel Interface

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

See the [PC-1500 Error Code Reference](PC-1500-Error-Codes.md) for all error codes, including the CE-150 errors (11, 19, 40–44, 70–74, 76, 78–80) and the CE-158 errors (50–53, 58, 61, 65, 67, 69).
