# Sharp PC-1600 BASIC Reference

→ [Command Index](Command-Index.md) · [PC-1600 Error Codes](PC-1600-Error-Codes.md) · [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md)

> **Status:** This document is being built section by section from the PC-1600 Operation Manual
> (English, `PC-1600_Operation_Manual.pdf`; the German *Bedienungsanleitung* has identical content
> but poorer OCR and is used only to resolve ambiguities), with hardware detail from chapter 4 of
> the *PC-1600 Systemhandbuch* (Holtkötter). Sections still marked **TODO** have not been written yet.
> The source manual's structure is Part IV, chapters 8–14, plus Appendices.

---

## Overview

The **Sharp PC-1600** (1986) is the successor to the PC-1500. It keeps the pocket form factor and
runs an enhanced version of the same SHARP pocket-computer BASIC, but adds a four-line display, a
real-time clock with alarm/wake, three built-in I/O ports, a RAM-disk-capable module system, and an
optional 2.5″ floppy drive. It remains **backward compatible with the PC-1500 and its peripherals**.

### Hardware at a glance

| Item | Value |
|------|-------|
| CPU | CMOS 8-bit, instruction-set equivalent to **Z-80A** (Sharp LH5803 core plus the PC-1500's LH5801 in MODE 1) |
| RAM | **16 KB** standard, expandable to **80 KB** via two module slots |
| Display | **26 columns × 4 lines** LCD, 5×7 character matrix; **156 × 32 dots** graphics, all dots addressable |
| Clock | Real-time clock with **wake-up** and **alarm** functions |
| Keyboard | 69 keys, QWERTY layout with numeric keypad; 6 function keys (F1–F6); auto-repeat |
| Built-in ports | **RS-232C**, **optical serial (SIO)**, **analog input** |
| System bus | Connects the CE-1600P printer/cassette unit and PC-1500-series interface units |
| Power | 4 × AA batteries, or **EA-160** / **EA-150** AC adapter |
| Free user area | ~11834 bytes on the base machine (the manual's 12090 figure was corrected on the errata sheet) |

### Operating modes

| Key state | Mode | Purpose |
|-----------|------|---------|
| **MODE** toggles | **RUN** | Run programs, direct commands, calculator |
| **MODE** toggles | **PRO** (PROGRAM) | Enter and edit program lines |
| **SHIFT + MODE** | **RESERVE** | Assign function-key strings |

Use `LOCK` / `UNLOCK` to disable/enable the MODE key.

### MODE 0 vs MODE 1

The `MODE` **command** (not the key) selects the interpreter personality:

- **MODE 0** — native PC-1600 mode. Full 4-line screen, PC-1600 character set, all new commands.
- **MODE 1** — PC-1500 emulation. Only the bottom display line is active, PC-1500 character set,
  screen behaves as on the PC-1500. Required for commands tied to PC-1500 peripherals (CE-150,
  CE-158, CE-162E) and for running PC-1500 tape programs unchanged.

`TIME = 0` cannot be set on the PC-1600 (many PC-1500 programs use it to reset the time counter).

### Optional accessories

| Model | Function |
|-------|----------|
| **CE-1600M** | 32 KB battery-backed RAM module — program storage, RAM expansion, or RAM disk |
| **CE-1600P** | Four-colour printer with cassette interface; also carries the system bus and provides the cassette recorder connection |
| **CE-1600F** | 2.5″ Pocket Disk drive, double-sided, ~64 KB per side |
| **CE-161** | Program module (RAM disk capable) |
| PC-1500 modules | CE-151, CE-155, CE-159, CE-161 etc. usable (≤ 16 KB) for MODE 1 program space |

---

## Part IV — BASIC Reference Section

### 8. Basic Programming Concepts

PC-1600 BASIC has roughly 200 commands, statements and functions. Below the interpreter is Z-80A
machine code; advanced users can program that directly.

#### Direct mode vs program mode

- **Direct mode** — a command typed without a line number executes immediately on `ENTER`. Used
  for calculations and for issuing commands to peripherals (e.g. the disk drive). Only one command
  per line; entering several before `ENTER` gives **ERROR 1** (or only the first is accepted). Some
  commands are not available in direct mode.
- **Program mode** — a line typed with a leading number is stored, not executed, until `RUN`. The
  computer switches between the two modes automatically based on whether a line number is present.
  Editing happens in **PRO** mode; a program runs only in **RUN** mode. The ready prompt is `>`.

After each program line is entered the computer inserts a colon after the line number
(`10:PRINT ...`). Number lines in steps of 10 to leave room for insertions.

#### Running a program

- `RUN` — execute from the lowest line number.
- `CONT`, `GOTO`, `ARUN` — resume or start from a particular point (see the Command Dictionary).
- **DEF key + label** — if the first line carries a label in quotes immediately after the number
  (`10:"A":PRINT ...`), pressing **DEF** then the label key runs from that label. Labels are a
  single upper-case letter from `A`–`Z` (bottom two keyboard rows plus shift). This lets several
  independently labelled programs coexist in memory; labels also work in `LIST`, `DELETE`, `MERGE`.

#### Memory allocation

Commands that allocate or clear user memory: `CLEAR`, `ERASE`, `MEM`, `NEW`, `STATUS`, `TITLE`
(see also the memory maps, Appendix D).

The internal RAM gives the user a maximum of **11834 bytes** (~12 KB) for BASIC or machine-language
programs. Layout of the user area, top to bottom:

| Area | Notes |
|------|-------|
| **Reserve Program Area** | Stores programmable function-key contents (RESERVE mode). Fixed size. |
| **Machine Program Area** | Optional; sized by `NEW`. `NEW 0` (after a reset) clears it to zero, leaving exactly 11834 bytes of user area. |
| **BASIC Program Area** | Program lines typed in or loaded from disk/tape. |
| **Free Area** | Reported by `MEM`. |
| **Variables Area** | Grows as the program runs; clear it with `CLEAR`. |
| **Work Area** | System scratch; not user-accessible. |

A RAM module in slot 1 or 2 (as memory expansion) extends the user area out into the module.
`MEM` reports only the *free* user area, so it reflects the size of any stored programs.
Connecting the **CE-1600F** disk drive reserves part of the user area for disk I/O, reducing the
available program space to **10810 bytes**.

#### AUTORUN files

Two independent auto-execute mechanisms:

- **`ARUN`** — the program is already in internal memory, may have any name.
- **AUTORUN function** — a program saved as **`AUTORUN.BAS`** on floppy disk or RAM disk runs at
  power-on. Setup: save as `AUTORUN.BAS`; set RUN mode; make sure `SMALL` and the shift indicator
  are **not** lit on the status line (otherwise it will not fire); turn the power off normally. At
  the next power-on the computer displays and executes `LOAD"X:AUTORUN.BAS",R` (or `S1:` / `S2:`
  for RAM disk).

  Priority: floppy (`X:` then `Y:`) over RAM disk, and RAM disk `S1:` over `S2:`. The file loads
  into whichever memory area was selected when the power was turned off.

### 9. Operating Modes under BASIC

#### Key operations

**Alphanumeric keys** — typewriter layout; numeric keypad on the right. `KEYSTAT` turns
auto-repeat on/off and enables an optional key "click".

**Switch keys** (eight in total):

| Key | Function |
|-----|----------|
| **MODE** | Toggles RUN ↔ PRO. RUN is the direct execution mode; PRO is where programs are entered, listed and edited. |
| **SHIFT + MODE** | Enter RESERVE mode (function-key programming). Press **MODE** alone to leave it. |
| **SHIFT** | One-shot toggle — `SHIFT` shows on the status line until the next key, which then gives its orange (shifted) function. Press before *each* key for consecutive shifted entries. |
| **CTRL** | One-shot toggle like SHIFT (`CTRL` on the status line); used with the cursor/edit keys for the edit-mode functions below. |
| **MENU** | Cycles the three RESERVE sets I → II → III → I. Each set has its own six function-key strings (18 total). |
| **SML** | Toggles upper/lower case (`SMALL` on the status line). In lower-case mode, SHIFT gives capitals. |
| **DEF** | (i) In RUN mode, runs a program from a quoted label on its first line. (ii) In any mode, `DEF` + a top-row letter key (`Q W E R T Y U I O P`) or a function key inserts a pre-assigned keyword at the cursor. |
| **RCL** | Shows/hides the function-key menu for the current RESERVE set (I, II or III). |
| **CLICK** (KB II) | After SHIFT, toggles the key click; alone, selects keyboard II (international character template). |

**Special keys** (below the keypad):

| Key | Function |
|-----|----------|
| **↑** | (i) PRO mode, empty screen: show the program's first line, then scroll forward line by line. (ii) After an error / BREAK / STOP / INPUT, switch to PRO and press to show the last-executed line for editing. (iii) In trace (`TRON`) mode or after BREAK/STOP, execute the next line. |
| **↓** | (i) PRO mode, empty screen: show the program's last line, then scroll backward. (ii) While held after a `BREAK IN <line#>`, shows the halt line. (iii) After an error / BREAK / STOP / INPUT, same as **↑**. (iv) In trace mode, shows the full contents of the line currently executing. |
| **SHIFT + ↑** | MODE 1: enters the `π` symbol. MODE 0: same as **↑**. |
| **SHIFT + ↓** | MODE 1: enters the `√` symbol. MODE 0: same as **↓**. |
| **ENTER** | Ends an entry — characters stay in the keyboard buffer until `ENTER` is pressed. |
| **BS** (backspace) | Deletes the character left of the cursor and closes up the line, moving leftward across physical lines to the start of the logical line. |
| **OFF** / **ON** | Power off / on. At power-on the status line restores the modes active before the last power-off; `ON` then also acts as **BREAK**. |
| **BREAK** (`ON` while running) | Interrupts execution — `BREAK IN <line#>`. Enable/disable with `BREAK ON/OFF`. |
| **CL** (clear) | Deletes the line the cursor is on (before `ENTER`). In RUN mode after an error, clears the error message. |
| **SHIFT + CL** | Clears the screen and resets internal flags to the "ready to execute" state. |
| **→** | (i) PRO mode: cursor right one position (no effect at end of logical line). (ii) Direct-calculation mode: recalls the entered expression; if it had a syntax error, recalls it with the cursor flashing at the error. |
| **←** | (i) PRO mode: cursor left one position. (ii) Direct-calculation mode: same as **→**. |
| **DEL** (SHIFT + …) | Deletes the character at the cursor and closes up the space. |
| **INS** (SHIFT + …) | Opens a one-character space left of the cursor. Press before each character to insert a run. |

#### Screen modes

Set with the `MODE` **command** (distinct from the RUN/PRO/RESERVE operating modes). Default
after ALL RESET is **MODE 0**.

| | MODE 0 | MODE 1 |
|--|--------|--------|
| Text | 26 columns × **4 lines**, 5×7 matrix; char coords `(0,0)`–`(25,3)` | 26 columns × **1 line** (bottom line active), for PC-1500 compatibility |
| Behaviour | full-screen | `PRINT`/`GPRINT` use the bottom line only; PRO mode and `INPUT` scroll up one line at a time |
| Characters | PC-1600 set | PC-1500 set; `π` (code `&5D`) and `√` (code `&5B`) shown as `π` / `√` |
| Graphics | 156 × 32 dots, X `0–155`, Y `0–31`, coords `(0,0)`–`(155,31)` | same 156 × 32 graphic matrix |

Screen graphics statements: `GCURSOR`, `GPRINT`, `LINE`, `POINT`, `PRESET`, `PSET`.

#### Edit mode

A BASIC line ("logical line") is up to **80 characters** and may wrap over several 26-character
screen ("physical") lines. Program lines are edited only in PRO mode. The editing functions do
not distinguish physical lines.

To edit: retype the whole logical line with the same number, or use `LIST` to bring the line up,
position the cursor and change it — the old line is replaced only when `ENTER` is pressed. After
an error / BREAK / STOP / INPUT, switch to PRO and press **↑**/**↓** to show the last line at the
break position; the state is cancelled with `SHIFT + CL`. `GOTO "label"` plus `LIST` / `RENUM` /
`DELETE` lets you edit just one of several labelled programs.

**CTRL + cursor/edit key** combinations (the exact keys are printed on the keyboard and the
supplied template):

| Combination | Effect |
|-------------|--------|
| CTRL + ↑ | Clear screen, show the program's first line, cursor at its start |
| CTRL + ↓ | Clear screen, show the program's last line, cursor at its start |
| CTRL + → | Move cursor to just after the last character of the current logical line |
| CTRL + ← | Move cursor to the beginning of the current logical line |
| CTRL + INS | Toggle **INSERT** / **OVERWRITE** (default OVERWRITE; cursor blinks faster in INSERT) |
| CTRL + (delete-to-head) | Delete all characters from the cursor back to the head of the line, including the line number |
| CTRL + (delete-to-end) | Delete all characters from the cursor to the end of the logical line |
| CTRL + (next word) | Move cursor forward to the first character of the next word on the line |
| CTRL + BS | Delete the character left of the cursor (same as the **BS** key) |
| CTRL + (repeat toggle) | Turn keyboard auto-repeat on/off (same as `KEYSTAT`) |
| CTRL + (line delete) | Delete all characters on the logical line (same as the **CL** key) |

#### Reserve mode

RESERVE mode edits the strings assigned to the six function keys (`! " # $ % &` = F1–F6). Enter
with `SHIFT + MODE` (`RESERVE` on the status line); leave with `MODE`. The **MENU** key cycles the
three sets I / II / III — 18 strings total.

**Setting the strings:**
1. `SHIFT + MODE`, then optionally type `NEW` `ENTER` to clear all strings in all three sets
   (skip to keep/modify existing ones).
2. Select set I with **MENU**; press F1 and type the string, then `ENTER`; repeat for F2–F6.
3. **MENU** to set II, repeat; **MENU** to set III, repeat.

A string may hold up to **110 bytes**. A BASIC keyword/function token = 2 bytes; a letter, digit
or symbol = 1 byte — so a key can hold a whole phrase, e.g. `FOR I=1 TO 100 STEP 5`.

**Recall:** in RUN or PRO mode, select the set with **MENU** and press the function key — its
contents appear at the cursor.

> The storage for **set II** is shared with the optional `ALARM$` message. Setting an alarm
> message overwrites the set-II function-key strings in memory.

**Function-key menus:** a 26-character label string per set, shown with the **RCL** key, purely as
an on-screen reminder of what the keys output. Create it in RESERVE mode: select the set, type
the string in quotes (≤ 26 chars; keep each item ≤ 4 chars to align above its key), `ENTER`.
Example for set III: `"PRT LST AUT LD  SVE LLST"`.

**Saving / loading:** function-key string sets save and load **only via cassette** (`CSAVE` /
`CLOAD`), like a program — floppy and RAM disk cannot store them. The computer **must** be in
RESERVE mode when loading; loading in PRO mode dumps the data into the BASIC program area and
corrupts any program there.

#### Pre-assigned keywords

Retrieved in any mode with **DEF** + key (engraved on the supplied template):

| Key | Keyword | | Key | Keyword |
|-----|---------|-|-----|---------|
| Q | `INPUT` | | U | `CSAVE` |
| W | `PRINT` | | I | `CLOAD` |
| E | `USING` | | O | `MERGE` |
| R | `GOTO`  | | P | `LIST` |
| T | `GOSUB` | | Y | `RETURN` |

`CSAVE` / `CLOAD` / `MERGE` need a cassette recorder on the CE-1600P. **DEF + function key**
gives six more: `RUN`+ENTER, `AUTO`, `LOAD"`, `SAVE"`, `FILES"`, `"COM1:"`.

### 10. Data Representation

Data is handled in **bytes** (8 bits, 0–255). Hexadecimal is written with a leading `&`
(`&27` = 39 decimal).

#### Types of data

- **Text / character sets** — the PC-1600 uses a character set close to the IBM PC. It differs
  from the PC-1500 set, so some displayed characters change between screen MODE 0 and MODE 1 —
  chiefly `&5B` (√), `&5D` (π) and some bracket types. See Appendix C.
- **Character strings** — up to 80 characters handled as a unit; no arithmetic, but strings can
  be concatenated and sliced.
- **Numeric data** — stored in binary; accepted in decimal, floating-point, or (with `HEX$`)
  hexadecimal. Internally floating point: 12-digit mantissa × power of 10, **rounded to 10 digits**
  on output. Range **±9.999999999×10⁻⁹⁹ to ±9.999999999×10⁺⁹⁹**; operational error ±1 in the
  10th digit.

#### Constants

- **String constant** — characters in double quotes; `""` is the null string (zero length, see
  `INKEY$`). Maximum length **30 characters**.
- **Numeric constants** — four kinds:

| Kind | Range / form | Examples |
|------|--------------|----------|
| Integer | −32768 to +32767, optional `+`/`-` sign | `123`, `+2`, `-57`, `1024` |
| Fixed point | signed number with a decimal fraction | `12.7`, `+1.345`, `-67.9888` |
| Floating point | `<fixed>E<exponent>`, either part signed | `2.43E9`, `+1.99E-3`, `+50.23E+2` |
| Hexadecimal | `&0` to `&FFFF` (0–65535); no sign, no fraction | `&23`, `&8000`, `&FFFF` |

#### Variables

Two data classes — **numeric** and **string** (`$` suffix). A numeric variable is `0` until set;
a string variable is null.

**Names:** first character an upper-case `A`–`Z`; optional second character `A`–`Z` or `0`–`9`;
string names end in `$`. Extra characters are allowed but **ignored** — `TOTAL` and `TOP` are the
same variable (but `A` and `AB` differ). Examples: `A`, `AB`, `C1`, `D9`, `CC$`, `NO$(4)`.

**Three storage types:**

| Type | Numeric names | String names | Storage |
|------|---------------|--------------|---------|
| **Fixed** | `A`–`Z` (26) | `A$`–`Z$` | Dedicated fixed-variable memory area; always allocated. Addressable as a 1-D array via `@(1)`–`@(26)` / `@$(1)`–`@$(26)` (`@(1)` = `A`). Cleared by `CLEAR` (not by `ERASE`). |
| **Simple** | `A0`–`Z9`, `AA`–`ZZ` | `+ $` | In the BASIC program area; the more used, the less program room (add a RAM module if it runs out). Cleared by `CLEAR` and `ERASE`. |
| **Array** | `A0(0)`–`Z9(255)`, `AA(0)`–`ZZ(255)` | `+ $` | Declared with `DIM`. |

**Fixed / simple string variables** hold up to **16 characters**.

**Arrays:** 1-D (list) or 2-D (table); subscripts are integers and may themselves be variables,
starting at **0** (`A(0,0)` is the first element). A name cannot be both 1-D and 2-D at once.
Max **65535 members** (2-D: 255 × 255); max array size **64 KB**. Cleared by `ERASE` (leaves
fixed variables intact). For **string arrays**, `DIM` should also declare each member's maximum
length — otherwise 16 characters are reserved per member (wasteful for short strings).

#### Expressions and operators

**Arithmetic**, in decreasing precedence:

| Operator | Operation |
|----------|-----------|
| `^` | Exponentiation |
| `-` (unary) | Negation |
| `*` `/` | Multiplication, division |
| `MOD` | Modulus |
| `\` | Integer division |
| `+` `-` | Addition, subtraction |

Parentheses override precedence. Consecutive exponentiation and power/negation combinations
evaluate **right to left** (`3^4^2` = `3^(4^2)`). Arithmetic takes priority over relational and
logical operators.

**Relational** — `=`, `<>`, `<`, `>`, `<=`, `>=`; result is `1` (true) or `0` (false). Numeric
compares to numeric, string to string (not mixed). Strings compare character-by-character by
character code (Appendix C); on a mismatch the higher code is "greater"; if one string is a
prefix of the other the longer is greater. `=` also serves assignment (`LET`) — different meaning.

**Logical** — `AND`, `OR`, `NOT`, evaluated after arithmetic and relational operators. Used for
multi-condition `IF` (`IF A<=32 AND B>=90 THEN 150`). On numbers −32768..32767 the operation is
bitwise on 16-bit two's-complement integers (`41 AND 27` = `9`; `41 OR 27` = `59`; `NOT 3` = `-4`).
In general `NOT X = -(X + 1)`.

**Functional** — built-in functions taking one operand: `ABS`, `ACS`, `ASN`, `ATN`, `COS`, `DEG`,
`DMS`, `EXP`, `INT`, `LN`, `LOG`, `PI`, `RND`, `SGN`, `SIN`, `SQR`, `TAN`. Angular unit set by
`DEGREE` / `RADIAN` / `GRAD`. The manual states **exponentiation has priority over functions**, so
parentheses are sometimes needed. Its worked examples:

| Algebraic | PC-1600 expression |
|-----------|--------------------|
| sin²30° | `(SIN 30)^2` |
| (sin 30°)² | `SIN 30^2` |
| cos⁴(A+B) | `(COS(A+B))^4` |

### 11. Files

All PC-1600 files are **sequential** — data is read/written from first item to last; no random
access. A file holds character-code text (including BASIC program lines) or binary data, on RAM
disk, cassette or floppy, and can also pass over the serial ports.

#### File descriptor

Format `d:filename.ext`.

**Device names** (each followed by `:`):

| Name | Device |
|------|--------|
| `S1:` / `S2:` | Memory module in slot 1 / slot 2 (RAM disk) |
| `COM1:` | RS-232C serial port |
| `COM2:` | Optical serial port |
| `COM:` | Currently accessed serial port |
| `CAS:` | Cassette recorder on the CE-1600P |
| `X:` / `Y:` | 2.5″ floppy disk drive (CE-1600F) |

**File name** — up to 8 characters from `A–Z 0–9 # $ % & ' ( ) - / < > { } @`. Required for every
saved file except cassette files saved in PC-1500-compatible mode with `CSAVE`.

**Extension** — up to 3 characters after a `.`, user-chosen (e.g. `.FIN`, `.BIN`). `SAVE`
automatically appends **`.BAS`** to BASIC programs; `LOAD` assumes `.BAS` if no extension is given;
`FILES` / `LFILES` show `.BAS` unless another extension was set; `COPY` **requires** the `.BAS` to
be given explicitly.

#### Storage & retrieval

**Cassette** — files sit on the tape in save order; `LOAD` winds the tape (via remote control) to
the label bearing the requested name. Slow. No directory. Commands: `BLOAD`, `BSAVE`, `CHAIN`,
`CLOAD`, `CLOAD?`, `CLOAD M`, `CLOSE`, `CSAVE`, `CSAVE M`, `INPUT#`, `LOAD`, `MAXFILES`, `MERGE`,
`OPEN`, `PRINT#`, `RMT ON/OFF`, `SAVE`. The `CLOAD*` / `CSAVE*` set exists only for PC-1500
compatibility.

**Floppy / RAM disk** — sectored, direct access, near-instant load. A **directory** (shown by
`FILES` on screen, `LFILES` on the printer) lists `filename.ext,P  MM/DD HH:MM` — name, `P` if
write-protected (`SET`), date last written, time. Commands: `BLOAD`, `BSAVE`, `CLOSE`, `COPY`,
`FILES`, `INIT`, `INPUT#`, `KILL`, `LFILES`, `LOAD`, `MAXFILES`, `NAME`, `OPEN`, `PRINT#`, `SAVE`,
`SET`. Directory capacity: **48** files on a floppy, **48** on the CE-1600M RAM disk.

> **CE-159** program modules hold a single program, not a named file — not loaded with `LOAD`.
> Make the slot active with `TITLE`, then `RUN`.

**Wildcards** — `?` matches any run of single characters, `*` matches a whole name and/or whole
extension. `*.*` lists everything. Usable with `FILES` / `LFILES`, and `?` also with `ALARM$`
times.

Examples: `?ET` → `SET MET RET …`; `????.B??` → `REP1.BAS DAT1.BIN …`; `*.*` → any name, any ext.

#### Protection

- **`SET`** — per-file write protection on floppy / RAM disk (cannot be written or destroyed).
- **Hardware** — floppy: a write-protect switch per disk side. RAM module: a front write-protect
  switch, set with the computer **OFF**.
- **`PASS`** — password on internal memory; it cannot be erased, edited or listed without the
  password.

#### Creating a file

1. `MAXFILES` — set the number of files that may be open.
2. `OPEN "d:name" FOR OUTPUT AS #n` — name it, give it a file number, open for output.
3. `PRINT#n, …` — write data.
4. `CLOSE #n` — must close before it can be reopened for input.

```
 5 MAXFILES = 1
10 OPEN "X:ADDRESS" FOR OUTPUT AS #1
20 INPUT "ENTER NAME?";N$
30 IF N$ = "END" THEN 100
40 INPUT "ENTER CITY?";C$
50 INPUT "ENTER TEL. NUMBER?";T$
60 PRINT#1,N$;",";C$;",";T$
70 PRINT
80 GOTO 20
100 CLOSE #1
110 END
```

> Reopening a file `FOR OUTPUT` **erases** its previous contents. To keep them, open
> `FOR APPEND AS #n` (floppy / RAM disk only) — data can only be added at the **end** (a sequential
> file cannot have items inserted mid-list; read all, re-order in memory, rewrite).

#### Accessing a file

1. `MAXFILES`.
2. `OPEN "d:name" FOR INPUT AS #n`.
3. `INPUT #n, var…` — each call reads the next item; reading past the end without a guard gives
   **ERROR 165**. Test with `EOF(n)`.
4. `CLOSE #n` before reopening for output/append.

```
 5 MAXFILES = 1
10 OPEN "X:ADDRESS" FOR INPUT AS #1
20 PRINT "NEW YORK"
30 PRINT:PRINT "NAME","TEL. NUMBER"
40 IF EOF(1) THEN 100
50 INPUT #1,N$,C$,T$
60 IF C$ = "NEW YORK" THEN 80
70 GOTO 40
80 PRINT N$,T$
90 GOTO 40
100 CLOSE #1
110 END
```

String comparison in line 60 needs exact spelling, spacing and case.

`DSKF`, `EOF`, `LOC`, `LOF`, `MAXFILES` help advanced programs check sizes and avoid
device-full / file errors.

### 12. Access to Serial Ports

The two ports — **`COM1:`** (RS-232C) and **`COM2:`** (optical) — are treated by BASIC as
sequential I/O devices; **`COM:`** is whichever is currently accessed. Control commands:
`COMn ON/OFF/STOP`, `PCONSOLE`, `INIT`, `INSTAT`, `ON COMn GOSUB`, `ON PHONE GOSUB`, `OUTSTAT`,
`PHONE ON/OFF/STOP`, `RCVSTAT`, `SETCOM`, `SETDEV`, `SNDBRK`, `SNDSTAT`, `PZONE`.

#### Specifying the port

**`SETDEV`** selects `COM1:` or `COM2:` for all subsequent communication commands until the next
`SETDEV`. It also redirects the printer commands (`LLIST`, `LFILES`, `LPRINT` — normally the
printer at power-on) to the chosen port, and directs them back to the printer (closing both ports).
`DEV$` shows the current `SETDEV` settings. `SAVE` / `LOAD` transfer whole files over a port.

#### Protocol options

Handshake protocol: **`SNDSTAT`** for sending, **`RCVSTAT`** for receiving. Individual outgoing
control-signal states: **`OUTSTAT`** (leaves the machine ready for simple transmission if no
parameters given). **`INSTAT`** shows current receive-protocol settings.

#### Communication parameters (`SETCOM`, read back with `COM$`)

1. Baud rate 50–38400
2. Word length 5–8 bits
3. Parity even / odd / none
4. Stop bits 1 or 2
5. XON/XOFF on/off
6. Shift in/out on/off

Baud-rate ceilings by use: `SAVE`/`LOAD`/`BSAVE`/`BLOAD` and `COPY` to/from a port — **9600** on
`COM1:`, **38400** on `COM2:`. `INPUT`/`INPUT#`/`PRINT#`/`LLIST`/`LPRINT` — **4800** on both.

#### Receive buffer

Incoming data lands in a receive buffer whose size is set with **`INIT`**.

#### Output to a port

Usable commands: `CHR$`, `LFILES`, `LLIST`, `LPRINT` / `LPRINT USING`, `OPEN`, `PRINT#` /
`PRINT# USING`, `PZONE`, `SAVE`.

- **Programs / data via printer commands:** `SETDEV` (with the port-output option) → `PZONE` sets
  `LPRINT` format → `PCONSOLE` sets line length and EOL code → `LLIST` sends the listing.
- **As a file:** `MAXFILES` → `OPEN` the port for output with a number → `PRINT#` / `PRINT# USING`
  → `CLOSE`.
- **A whole file:** `SAVE` the file directly to the port.
- **Control codes:** `CHR$` — e.g. `10 LPRINT CHR$(4)` sends ASCII EOT after the port is opened.

#### Input from a port

Usable commands: `INIT`, `INPUT#`, `LOAD`, `OPEN`, `PCONSOLE`, `RXD$`, `SNDBRK`.

- **Data:** `MAXFILES` → `INIT` (buffer size) → `OPEN` the port `FOR INPUT` as a file → `INPUT#`
  → `CLOSE`.
- **A whole file:** `LOAD` from the port, as from disk.

### 13. Debugging

#### Syntax errors

On the first run, syntax and most other errors are caught line by line as execution reaches them.
Execution stops at the first error, showing the error code and line number (see
[PC-1600 Error Codes](PC-1600-Error-Codes.md)). For a syntax error (**ERROR 1**): `LIST <line>`,
or press **CL** to clear the message, **MODE** to PRO, then **↑** to show the line with the cursor
at the fault; edit and `RUN` again. Repeat for each successive error.

#### Trace mode

`TRON` / `TROFF` follow execution line by line. With trace on, the computer executes one line,
then pauses **0.5 s** with the line number shown at the right of the screen before the next line.
It can also be set to execute one line and wait for the **↓** key before continuing. Trace runs
until `TROFF`.

Place `STOP` statements at checkpoints to halt and inspect interim results; resume with `CONT`.

#### Error-processing routines

`ON ERROR GOTO <line>` at the start of the program redirects any subsequent error to a handler
instead of stopping execution — typically to correct the fault or re-prompt the user. Inside the
handler, `ERN` gives the error code and `ERL` the line; `RESUME` continues execution (or the
handler ends with a message). See the `ON ERROR GOTO` dictionary entry for an example.

---

## 14. BASIC Command Dictionary

Command pages in the manual use these mode/device markers:

| Marker | Meaning |
|--------|---------|
| **PRO** | Can be entered directly in PRO mode |
| **RUN** | Can be entered directly in RUN mode |
| **RESERVE** | Usable for function-key programming in RESERVE mode |
| **PROGRAM** | Can be used as a program line |
| Floppy disk | CE-1600F Pocket Disk Drive must be connected |
| Cassette | Cassette recorder connected via the CE-1600P interface |
| Printer | PC-1600 mounted in the CE-1600P (or CE-150) printer unit |
| Comms | For use with one of the two serial ports |
| **RAM** | RAM-disk command; CE-1600M or CE-161 module installed and initialised in slot 1 or 2 |

Format-description notation: `[ ]` optional, `< >` a value you supply, `( )` literal parentheses to type.

> The entries below are the complete command set (Appendix G). Each links to its detailed section
> once written. Commands marked **(new vs PC-1500)** did not exist on the PC-1500; commands marked
> **(MODE 1)** exist only for PC-1500 compatibility.

### Program control
| Command | Purpose |
|---------|---------|
| ARUN | Start program execution automatically at power-on |
| AUTO | Turn on automatic line numbering |
| CHAIN | Load and execute a BASIC program from tape |
| CONT | Resume execution after STOP or BREAK |
| DELETE | Delete program lines |
| END | Stop execution of the current program |
| FOR..NEXT | Repeated execution of program lines |
| GOSUB..RETURN | Jump to a subroutine |
| GOTO | Unconditional jump |
| IF..THEN | Conditional jump |
| LIST | List a program to the screen |
| NEW | Clear memory / assign space for machine code |
| ON..GOSUB / ON..GOTO | Multiple conditional jump |
| REM | Comment |
| RENUM | Renumber program lines |
| RUN | Start execution of a program |
| STOP | Halt execution during debugging |
| TRON / TROFF | Set / cancel program trace |

### Interrupts (event handling) — mostly **(new vs PC-1500)**
| Command | Purpose |
|---------|---------|
| ON ADIN GOSUB | Jump on an analog interrupt |
| ON COMn GOSUB | Jump on a serial-port interrupt |
| ON ERROR GOTO | Jump to error-processing routine |
| ONKEY GOSUB | Jump on a function-key input |
| ON PHONE GOSUB | Jump on a telephone-modem input |
| ON TIME$ GOSUB | Jump at a specified time |
| RESUME | Resume execution after an error routine |
| RETI | Return from an interrupt subroutine |
| KEY ON/OFF/STOP | Enable/disable function keys |
| ADIN ON/OFF/STOP | Enable/disable analog interrupts |
| COMn ON/OFF/STOP | Enable/disable communication interrupts |
| PHONE ON/OFF/STOP | Enable/disable RS-232C interrupts |
| TIME$ ON/OFF/STOP | Enable/disable clock interrupts |
| BREAK ON/OFF | Enable/disable the BREAK key |

### Input / output (screen & keyboard)
| Command | Purpose |
|---------|---------|
| AREAD | Read a variable from the screen |
| CLS | Clear the display screen |
| CURSOR | Position the cursor on the screen |
| GCURSOR | Position the graphics cursor on the screen |
| GPRINT | Draw bit-image graphics on the display |
| INKEY$ | Read a character from the keyboard buffer |
| INPUT | Input data from the keyboard |
| KBUFF$ | Write characters into the keyboard buffer |
| KEYSTAT | Set key-repeat / key-click functions |
| LINE | Draw a line between points on the screen **(new vs PC-1500)** |
| PAUSE | Display data on screen for a fixed time |
| POINT | Return the dot setting at a screen point |
| PRESET | Reset a dot at a screen point |
| PRINT | Output data to the display screen |
| PSET | Set/reset a dot at a screen point |
| WAIT | Set wait time after a PRINT statement |

### Data, variables, arrays
| Command | Purpose |
|---------|---------|
| CLEAR | Erase all variables in memory |
| DATA | List data items for READ |
| DIM | Reserve memory for variables / arrays |
| ERASE | Erase specified variables and arrays |
| LET | Assign a value to a variable |
| READ..DATA | Read data into the program from DATA lines |
| RESTORE | Re-read data from DATA lines |

### Numeric functions
| Command | Purpose |
|---------|---------|
| ABS | Absolute value |
| ACS | Arc cosine |
| ASN | Arc sine |
| ATN | Arctangent |
| COS | Cosine |
| DEG | Convert deg/min/sec form to decimal degrees |
| DEGREE | Set DEGREE angular mode |
| DMS | Convert decimal degrees to deg/min/sec |
| EXP | e raised to the power X |
| GRAD | Set GRAD (gradient) angular mode |
| INT | Truncate the decimal part |
| LN | Natural logarithm |
| LOG | Common (base-10) logarithm |
| MOD | Remainder of division |
| RADIAN | Set RADIAN angular mode |
| RANDOM | Seed random-number generation |
| RND | Generate a random number |
| SGN | Sign of an expression |
| SIN | Sine |
| SQR | Square root |
| TAN | Tangent |

### String functions
| Command | Purpose |
|---------|---------|
| ASC | Character code of a string's first character |
| CHR$ | Character from an ASCII code |
| HEX$ | Hexadecimal string for a number |
| INSTR | Search for a character within a string |
| LEFT$ | Characters from the left end of a string |
| LEN | Number of characters in a string |
| MID$ | Substring from inside a string |
| RIGHT$ | Characters from the right end of a string |
| STR$ | Convert numeric data to a string |
| VAL | Convert a numeric string to a value |

### Clock, alarm, power
| Command | Purpose |
|---------|---------|
| ALARM$ | Set alarm time and message |
| DATE$ | Return / set the date |
| POWER | Set auto power-off |
| TIME | Set / return the built-in clock time (numeric) |
| TIME$ | Return the clock time as a string |
| WAKE$ | Set time / command string for auto power-on |

### Memory & machine language
| Command | Purpose |
|---------|---------|
| BLOAD | Load a machine-language program from disk or tape |
| BSAVE | Save a machine-language program to disk or tape |
| CALL | Call a machine-language program (PC-1600 addressing) |
| INP | Return data from a microprocessor port |
| MEM | Unused memory space in the user area |
| OUT | Write data to a microprocessor port |
| PEEK | Return a memory byte **(PC-1600 mode)** |
| POKE | Write a memory byte **(PC-1600 mode)** |
| STATUS | Amount of free space in memory areas |
| XCALL | Call a machine-language program **(MODE 1 / PC-1500 addressing)** |
| XPEEK / XPEEK# | Return a memory byte **(MODE 1)** |
| XPOKE / XPOKE# | Write a memory byte **(MODE 1)** |

### Files, disk, RAM disk — mostly **(new vs PC-1500)**
| Command | Purpose |
|---------|---------|
| CLOSE | Close a device file |
| COPY | Copy a file on disk or tape |
| DSKF | Free space on a disk device |
| EOF | End-of-file indicator |
| FILES | Show disk directory on screen |
| INIT | Initialise a module / disk; set receive buffer size |
| INPUT# | Read records from a file |
| KILL | Erase a file on disk |
| LFILES | Print disk directory to the printer |
| LOAD | Load a file from disk or tape to memory |
| LOC | Number of records accessed in a file |
| LOF | Size of a file on disk |
| MAXFILES | Set maximum number of open files for a program |
| MERGE | Merge a program from tape into memory |
| NAME | Rename a file on disk |
| OPEN | Open a file for access |
| PRINT# | Write data to a file |
| SAVE | Save a BASIC program to disk or tape |
| SET | Set write protection on a disk file |
| TITLE | Select the computer's memory area |

### Cassette (MODE 1 tape commands)
| Command | Purpose |
|---------|---------|
| CLOAD | Load a BASIC program from tape **(MODE 1)** |
| CLOAD? | Verify loading from tape **(MODE 1)** |
| CLOAD M | Load a machine-language program from tape **(MODE 1)** |
| CSAVE | Save a BASIC program to tape **(MODE 1)** |
| CSAVE M | Save a machine-language program to tape **(MODE 1)** |
| RMT ON/OFF | Enable/disable tape remote control |

### Serial communications — **(new vs PC-1500)**
| Command | Purpose |
|---------|---------|
| COM$ | Return the communication parameters |
| INSTAT | Return control-signal states for a serial port |
| OUTSTAT | Set control-signal states for serial ports |
| PCONSOLE | Set print format / EOL code for printer or ports |
| PZONE | Set the print zone for printer or serial port |
| RCVSTAT | Set receive protocol / timeout for a serial port |
| RXD$ | Return current data from a serial port |
| SETCOM | Set communication protocol for the serial ports |
| SETDEV | Select a serial port for output |
| SNDBRK | Send break characters to a serial port |
| SNDSTAT | Set send protocol / timeout for a serial port |
| LPRINT | Output data to the printer or a serial port |

### Printer / plotter (CE-1600P, or CE-150 in MODE 1)
| Command | Purpose |
|---------|---------|
| COLOR | Set the pen colour |
| CSIZE | Set printer character size |
| GLCURSOR | Position the printer pen in graphics mode |
| GRAPH | Set the printer to graphics mode |
| LCURSOR | Move the printer pen to a position |
| LF | Feed paper in the printer |
| LINE (LLINE) | Draw a line between points on the printer |
| LLIST | List a program to the printer |
| PAPER | Set paper type and vertical print range |
| PITCH | Set character pitch / line spacing |
| RLINE (RUNE) | Draw a line in relative coordinates on the printer |
| ROTATE | Set print orientation / print-head travel |
| SORGN | Set the current pen position as origin |
| TAB | Move the printer pen to a column |
| TEST | Run a printer self-test |

### Modem / misc
| Command | Purpose |
|---------|---------|
| BEEP | Generate sound through the internal speaker |
| BEEP ON/OFF | Enable/disable sound generation |
| ERL | Line number of the last error |
| ERN | Error code of the last error |
| LOCK / UNLOCK | Enable/disable the MODE key |
| MODE | Select screen mode for PC-1500 compatibility |

---

## Appendices — **TODO**
- A. Replacing the batteries
- B. Replacing the RAM modules
- C. Character code tables
- D. Memory maps
- E. Machine-language programs
- F. Error codes — see **[PC-1600 Error Codes](PC-1600-Error-Codes.md)**
- G. BASIC command list — folded into the dictionary index above
- H. Compatibility with the PC-1500 model and peripherals — see [Compatibility](#part-iv--basic-reference-section) note in Overview; details TODO
- I. Care & troubleshooting
- J. Specifications
- K. Syntax diagrams (German edition only)

---

## See also
- [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md)
- [CE-150 Reference](CE-150-Reference.md) · [CE-158 Reference](CE-158-Reference.md)
- [Command Index](Command-Index.md)
