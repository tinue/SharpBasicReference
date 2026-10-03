# Sharp PC-1600 BASIC Reference

→ [README](README.md) · [Command Index](PC-1600-Command-Index.md) · [Error Code Reference](PC-1600-Error-Codes.md) · [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md)

> **Source:** the PC-1600 Operation Manual — English `PC-1600_Operation_Manual.pdf` as the primary
> text (the German *Bedienungsanleitung* has identical content but poorer OCR and was used only to
> resolve ambiguities). Manual structure: Part IV, chapters 8–14, plus Appendices. Detailed
> per-command entries are in [chapter 14](#14-basic-command-dictionary); the
> [PC-1600 Command Index](PC-1600-Command-Index.md) groups them by topic.
> Keyboard-glyph images could not be OCR'd from either edition; key names in chapter 9 are
> reconstructed from the surrounding prose.

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

The full list of differences is in [MODE 0 and MODE 1 in detail](#mode-0-and-mode-1-in-detail).

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

**Conventions.** `[ ]` optional · `< >` a value you supply · `( )` literal parentheses to type.
Entries are alphabetical; the [PC-1600 Command Index](PC-1600-Command-Index.md) groups them by topic.
Mode/device applicability is noted per entry. Commands marked **(PC-1600)** are new relative to
the PC-1500; **(MODE 1)** exist only for PC-1500 compatibility; **(LH-5803)** run PC-1500 code on the LH-5803 in either MODE. Where behaviour matches the
PC-1500, see also the [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md).

Example listings are transcribed from the manual and lightly corrected for OCR damage; treat them
as illustrative.

### A

#### ABS
- **Format:** `ABS(<X>)` — **Abbr.** `AB.`
- **Purpose:** Absolute value of `X` (any numeric expression).

```
10:PRINT ABS(-2)    → 2
```

#### ACS
- **Format:** `ACS(<X>)` — **Abbr.** `AC.` — **See also:** ASN, ATN, COS
- **Purpose:** Arc cosine of `X`, `-1 <= X <= 1`. Result unit follows `DEGREE` / `RADIAN` / `GRAD`.
- **Remarks:** DEG: `0°`–`180°`. RAD: `0`–`π`. GRAD: `0`–`200`.

```
10:DEGREE
20:PRINT "ARC COS OF 0.5 IS";ACS(0.5)     → 60
30:PRINT "ARC COS OF 0 IS";ACS(0)         → 90
```

#### ADIN ON / OFF / STOP  **(PC-1600)**
- **Format:** `ADIN ON` | `ADIN OFF` | `ADIN STOP` — **Abbr.** `AD.` — **See also:** AIN, ON ADIN GOSUB
- **Purpose:** Enable/disable interrupts from the analog input jack (triggered when the input reaches a defined voltage level — see AIN / Chapter 6).
- **Remarks:**
  - `ADIN ON` — accept analog interrupts; branch with `ON ADIN GOSUB`.
  - `ADIN OFF` — ignore them.
  - `ADIN STOP` — ignore them but latch the most recent request; a later `ADIN ON` services it immediately. **Default.**

#### AIN  **(PC-1600)**
- **Format:** `AIN` — **Abbr.** `AI.` — **See also:** ADIN ON/OFF/STOP, ON ADIN GOSUB
- **Purpose:** System variable holding the current analog input converted to an integer **0–255** (0–2.495 V; higher voltages return 255).

```
>PRINT AIN     → 43
```

#### ALARM$  **(PC-1600)**
- **Format:** `ALARM$ = "MM/DD/HH/mm[;<message>]"` | `ALARM$ = ""` | `ALARM$` — **Abbr.** `AL.` — **See also:** TIME$, DATE$, POWER
- **Purpose:** Set the alarm time and an optional message.
- **Remarks:** `MM` 01–12, `DD` 01–31, `HH` 00–23, `mm` 00–59, slash-separated. At the alarm time the buzzer beeps for 1 s. `?` wildcards are allowed in month/day for recurring alarms (`"??/??/13/30"` = daily 13:30). Message ≤ 26 chars, shown on screen at the alarm time. `ALARM$ = ""` clears it; `ALARM$` as a value returns the set time.
- **Note:** the message string **overwrites the RESERVE mode II** function-key strings (Chapter 9).

```
>ALARM$="12/25/08/00;HAPPY CHRISTMAS!"
```

#### AREAD
- **Format:** `<label>[:]AREAD <variable>` — **Abbr.** `A.` — **See also:** RUN
- **Purpose:** Read the item currently displayed on the screen into a variable.
- **Remarks:** Valid **only** immediately after the label on a program's first line, and only when the program was started with **DEF** + label; ignored otherwise. Numeric display → numeric variable (up to 10 digits + 2 exponent digits); character display → string variable (length as `DIM`, default 16). If only the `>` prompt is showing, the variable is cleared to 0.

```
10:"A":AREAD N
```

#### ARUN
- **Format:** `ARUN` — **Abbr.** `ARU.` — **See also:** RUN, [AUTORUN files](#8-basic-programming-concepts)
- **Purpose:** Auto-run the program at power-on.
- **Remarks:** Must be the lowest-numbered statement; the computer must have been switched off in RUN mode. With program modules fitted, the search order for an `ARUN` is **slot 2 → slot 1 → internal RAM**. Unlike `RUN`, `ARUN` does **not** clear variables — use `CLEAR` in the program. If peripherals changed while off, a "not ready" error may block it; start with `RUN` instead.

```
10:ARUN:CLS
20:PRINT "THE TIME IS NOW ";TIME$
30:PRINT "YOU HAVE ";MEM;" BYTES FREE"
40:END
```

#### ASC
- **Format:** `ASC(<string variable>)` | `ASC("<string>")` — **See also:** CHR$
- **Purpose:** ASCII code of the first character of the string (rest ignored). See Appendix C.

```
10:INPUT "ENTER A CHARACTER ";A$
20:PRINT "THE ASCII CODE IS ";ASC(A$)
```

#### ASN
- **Format:** `ASN(<X>)` — **Abbr.** `AS.` — **See also:** ACS, ATN, SIN
- **Purpose:** Arc sine of `X`, `-1 <= X <= 1`. DEG: `-90°`–`90°`. RAD: `-π/2`–`π/2`. GRAD: `-100`–`100`.

#### ATN
- **Format:** `ATN(<X>)` — **Abbr.** `AT.` — **See also:** ACS, ASN, TAN
- **Purpose:** Arc tangent of `X`. DEG: `-90°`–`90°`. RAD: `-π/2`–`π/2`. GRAD: `-100`–`100`.

#### AUTO
- **Format:** `AUTO` | `AUTO <line#>` | `AUTO <line#>,<increment>` | `AUTO ,<increment>` — **Abbr.** `AU.` — **See also:** RENUM
- **Purpose:** Automatic line-number generation in PRO mode; after each `ENTER` the next number is offered. Default start and increment are 10. Type over an offered number to change it; press `CL`/break out to stop.

---

### B

#### BEEP
- **Format:** `BEEP <number>` | `BEEP <number>[,<tone>[,<duration>]]` — **Abbr.** `B.` — **See also:** BEEP ON/OFF
- **Purpose:** Sound the internal speaker `<number>` times (0–65535).
- **Remarks:** `<tone>` 255–0 = rising pitch (255 ≈ 230 Hz, 0 ≈ 7 kHz, default ≈ 4 kHz). `<duration>` default 160; a given value sounds relatively longer at lower frequencies.

#### BEEP ON / OFF
- **Format:** `BEEP ON` | `BEEP OFF` — **Abbr.** `B.` — **See also:** BEEP
- **Purpose:** `BEEP OFF` disables `BEEP` **and** the cassette-read monitor tones; `BEEP ON` re-enables them.

#### BLOAD  **(PC-1600)**
- **Format:** `BLOAD "<d:filename>"[,#<bank>,<address>]` — **Abbr.** `BL.` — **See also:** BSAVE, CLOAD, NEW, SET
- **Purpose:** Load a machine-language program from floppy (`X:`/`Y:`), RAM disk (`S1:`/`S2:`), serial port (`COM1:`/`COM2:`) or cassette (`CAS:`).
- **Remarks:** `<bank>` 0–7, `<address>` hex load address. Omit both to reload to the address it was saved from. If saved (by `BSAVE`) with an auto-start address, it loads **and runs** from there. See Appendix D.

```
>BLOAD"X:RXOUT"
```

#### BREAK ON / OFF
- **Format:** `BREAK ON` | `BREAK OFF` — **Abbr.** `BR.` — **See also:** CONT
- **Purpose:** Disable / re-enable the BREAK key. With BREAK OFF a running program cannot be interrupted from the keyboard (an infinite loop then needs RESET). Interrupt shows `BREAK IN <line#>`; resume with `CONT`. Convention: `BREAK OFF` at the top of long compute-only programs, `BREAK ON` at the end.

#### BSAVE  **(PC-1600)**
- **Format:** `BSAVE "<d:filename>",#<bank>,<start address>,<end address>[,<auto-start address>]` — **Abbr.** `BS.` — **See also:** BLOAD, CSAVE M
- **Purpose:** Save a machine-language program (memory `<start>`–`<end>` in `<bank>`) to floppy, RAM disk, serial port or cassette. Errors if the device is write-protected.
- **Remarks:** `<auto-start address>` is where `BLOAD` will auto-run after reloading; default `&FFFF` = no auto-start. **No** file extension is added (unlike `SAVE`). See Appendix D.

```
>BSAVE"S1:SORT",#1,&8000,&8AFF
```

---

### C

#### CALL
- **Format:** `CALL [#<bank>,]<address>[,<variable>]` — **Abbr.** `CA.` — **See also:** NEW, POKE, XPOKE
- **Purpose:** Call a machine-language routine (Z-80A / PC-1600 address space) from BASIC.
- **Remarks:** `<bank>` 0–7 (default 0), `<address>` `&0`–`&FFFF`. One `<variable>` is passed both ways: a numeric value (integer −32768..32767) goes in the **DE** register pair and comes back as a BCD value in the same variable if the carry flag is set on exit; for a string variable, **DE** = start address of the string, **B** = its length. The routine must already be in memory (`POKE` / `XPOKE`). See Appendix D. (PC-1500's `CALL` is `XCALL` here.)

```
400:CALL #3,&8000,X
410:PRINT "THE VALUE OF X RETURNED IS ";X
```

#### CHAIN  **(MODE 1)**
- **Format:** `CHAIN` | `CHAIN ["<filename>"][,<line#>]` — **Abbr.** `CHA.` — **See also:** CSAVE, MERGE
- **Purpose:** Load and run another BASIC program from **cassette** (PC-1500 mode). `CHAIN` runs the first program on tape from its first line; `CHAIN ,<line#>` from that line; `CHAIN "name"` searches for the named program. Lets an over-large program be split into sequential parts. Errors if a `PASS` password is set.

#### CHR$
- **Format:** `CHR$(<integer expression>)` — **Abbr.** `CH.` — **See also:** ASC
- **Purpose:** The character whose code is `<integer expression>`. Used to emit control codes to a printer / serial port or non-keyboard graphics characters. See Appendix C.

```
10:FOR X=33 TO 126:PRINT CHR$(X);:NEXT X
```

#### CLEAR
- **Format:** `CLEAR` — **Abbr.** `CL.` — **See also:** DIM, ERASE, TITLE
- **Purpose:** Erase **all** variables — including the fixed `A`–`Z`, `A$`–`Z$`, `@()` — resetting numbers to 0 and strings to null. Usable mid-program to reclaim variable space, or at the start when several programs share memory. (`ERASE` clears only simple/array variables.)

#### CLOAD  **(MODE 1)**
- **Format:** `CLOAD ["<filename>"][,A]` — **Abbr.** `CLO.` — **See also:** CSAVE, CLOAD?, MERGE
- **Purpose:** Load a BASIC program from **cassette** in PC-1500 mode (CE-150 / CE-162E; not the CE-1600P). `CLOAD` loads the next program on tape; `CLOAD "name"` searches for it. In RESERVE mode it loads a function-key string set. Errors if a password is set.

#### CLOAD?  **(MODE 1)**
- **Format:** `CLOAD? ["<filename>"]` — **Abbr.** `CLO.?`
- **Purpose:** Verify a tape program against the copy in memory (PC-1500 mode); mismatch → **ERROR 43**.

#### CLOAD M  **(MODE 1)**
- **Format:** `CLOAD M ["<filename>"][,#<bank>,<address>]` — **Abbr.** `CLO. M` — **See also:** BLOAD, CALL, CSAVE M, NEW
- **Purpose:** Load a machine-language program from cassette (PC-1500 mode) — a different memory area and tape format from `CLOAD`. `<bank>` 0–7, `<address>` hex; omit both to reload where it was saved from. If saved (by `CSAVE M`) with an auto-start address, it loads and runs from there.

#### CLOSE
- **Format:** `CLOSE` | `CLOSE #<file#>[,#<file#>…]` — **Abbr.** `CLOS.` — **See also:** END, OPEN
- **Purpose:** Close files on the current device. `CLOSE` alone closes all open files. A file must be closed before reopening in another mode (reopening an open file → error). `END`, `NEW`, `RUN`, `LOAD`, editing, and power-off all close every file automatically.

#### CLS
- **Format:** `CLS` — **Purpose:** Clear the whole screen; cursor to home `(0,0)` (MODE 0).

#### COLOR
- **Format:** `COLOR <number>` — **Abbr.** `COL.`
- **Purpose:** Printer pen colour: `0` black, `1` blue, `2` green, `3` red. Default at power-on `0`.

#### COM$  **(PC-1600)**
- **Format:** `COM$ "COMn:"` — **Abbr.** `COM.` — **See also:** SETCOM
- **Purpose:** String of the communication parameters set by the last `SETCOM` for that port, in `SETCOM` order (`<BR>,<WL>,<PR>,<ST>,<XO>,<SI>`). `"COM:"` = currently opened port.

```
10:SETCOM "COM1:",300,8,N,1,X,S
20:PRINT COM$"COM1:"
```

#### COMn ON / OFF / STOP  **(PC-1600)**
- **Format:** `COMn ON` | `COMn OFF` | `COMn STOP` — **See also:** ON COMn GOSUB, SETCOM
- **Purpose:** Enable/disable interrupts from port `n` (`1` = RS-232C, `2` = optical). `ON` + `ON COMn GOSUB` to branch; `OFF` ignores; `STOP` ignores but latches the last request for a later `ON`. **Default STOP.**

#### CONT
- **Format:** `CONT` — **Abbr.** `C.` — **See also:** RESUME, RUN, STOP, WAIT
- **Purpose:** Resume after `STOP`, a paused `PRINT`, or BREAK — provided the program was not edited (`GOTO <line#>` to resume elsewhere). Does **not** resume after `END` or an error (use `RESUME`).

#### COPY  **(PC-1600)**
- **Format:** `COPY "<d1:name1.ext>" TO "<[d2:]name2.ext>"` — **Abbr.** `COP.` — **See also:** SET
- **Purpose:** Copy a file between devices. The extension must always be given. If `d2:` is omitted the source device is used; fails if `name2` already exists on the destination. Also used to rename the floppy drive (`X:` ↔ `Y:`) for single-drive disk-to-disk backup (see Chapter 6). Tape/serial ports cannot be copy destinations for disk sources and vice-versa in some combinations (manual has the full matrix).

```
>COPY"S1:RICH.BAS" TO "S2:RICH.BAS"
>COPY"X:HAC.BAS" TO "HACOPY.BAS"
```

#### COS
- **Format:** `COS(<X>)` — **See also:** ACS, SIN, TAN
- **Purpose:** Cosine of `X`; unit per `DEGREE` / `RADIAN` / `GRAD`.

#### CSAVE  **(MODE 1)**
- **Format:** `CSAVE ["<filename>"][,A][;<line#>[,<line#>]]` — **Abbr.** `CS.` — **See also:** CLOAD, CLOAD?, LLIST, MERGE
- **Purpose:** Save a program (or line range, as `LLIST`) to cassette in PC-1500 mode. In RESERVE mode saves the function-key string set. `,A` = ASCII format (else binary). Blocked by a `PASS` password. **Not** usable with the CE-1600P — only the CE-150 / CE-162E.

```
>CSAVE"PROG01";200,380
```

#### CSAVE M  **(MODE 1)**
- **Format:** `CSAVE M "<filename>";#<bank>,<start address>,<end address>[,<auto-start address>]` — **Abbr.** `CS. M` — **See also:** CLOAD M, CALL, BSAVE
- **Purpose:** Save a machine-language program to cassette (PC-1500 mode). Bank/address range stored with the file; `CLOAD M` reads them back if not given. `<auto-start address>` default `&FFFF` = off.

#### CSIZE
- **Format:** `CSIZE <size>` — **Abbr.** `CSI.` — **See also:** PCONSOLE
- **Purpose:** Printer character size 1–9:

| size | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|------|---|---|---|---|---|---|---|---|---|
| chars/line | 160 | 80 | 53 | 40 | 32 | 26 | 22 | 20 | 17 |
| height mm | 1.2 | 2.4 | 3.6 | 4.8 | 6.0 | 7.2 | 8.4 | 9.6 | 10.8 |
| width mm | 0.8 | 1.6 | 2.4 | 3.2 | 4.0 | 4.8 | 5.6 | 6.4 | 7.2 |

  chars/line values assume `PCONSOLE` `<length>=0` (infinite). `LLIST` resets any size > 2 back to 2.

#### CURSOR
- **Format:** `CURSOR <column>[,<line>]` — **Abbr.** `CU.` — **See also:** PRINT
- **Purpose:** Move the cursor to `<column>` 0–25, `<line>` 0–3 (default: current line).

```
40:CURSOR 12,1
50:PRINT A$
```

---

### D

#### DATA
- **Format:** `DATA <list of constants>` — **Abbr.** `DA.` — **See also:** READ, RESTORE
- **Purpose:** Supply constants for `READ`. Numeric or quoted-string constants, comma-separated. Non-executable — place anywhere; read in line-number order; `RESTORE` to re-read.

```
10:FOR J=1 TO 4:READ A$,B:PRINT A$,B:NEXT J
60:DATA "MICHAEL",23,"DAVID",38,"WENDY",-24,"BRIAN",34
```

#### DATE$  **(PC-1600)**
- **Format:** `DATE$ = "MM/DD"` | `DATE$` — **Abbr.** `DATE.` — **See also:** TIME$, ALARM$
- **Purpose:** Real-time-clock date. As a statement, sets it (`MM` 01–12, `DD` 01–31). As a value, returns `MM/DD`. The day advances when `TIME$` rolls `23:59:59` → `00:00:00`.

```
>DATE$="12/25"
450:PRINT "TODAY'S DATE IS ";DATE$
```

#### DEG
- **Format:** `DEG <dd.mmssrr>` — **See also:** DMS
- **Purpose:** Convert an angle given as `dd.mmssrr` (degrees `.` minutes `ss` seconds `rr` hundredths; `mm`,`ss` 00–59) to decimal degrees (10 significant digits).

```
10:X=DEG 50.300000 : PRINT X     → 50.5
```

#### DEGREE
- **Format:** `DEGREE` — **Abbr.** `DE.` — **See also:** RADIAN, GRAD
- **Purpose:** Set the angular unit to degrees (default at power-on).

#### DELETE
- **Format:** `DELETE <line#>` | `DELETE <line#>,` | `DELETE <line#>,<line#>` | `DELETE ,<line#>` — **Abbr.** `DEL.` — **See also:** NEW
- **Purpose:** Delete program lines: one line; from a line to the end; an inclusive range; or from the start up to a line. Whole program → `NEW`.

#### DIM
- **Format:** `DIM <name>(<size>)` · `DIM <name$>(<size>)[*<length>]` · `DIM <name>(<rows>,<cols>)` · `DIM <name$>(<rows>,<cols>)[*<length>]` — **Abbr.** `D.` — **See also:** CLEAR, ERASE
- **Purpose:** Reserve storage for arrays (and 1-D "simple" subscripted variables). All except `A`–`Z`, `A$`–`Z$`, `@()`, `@$()` must be dimensioned before use.
- **Remarks:** Subscripts 0–255; a subscript starts at **0**, so `DIM A(2,3)` = 3 rows × 4 cols = 12 elements. `*<length>` (string element length) 1–80, default 16. Cannot re-dimension until `CLEAR` / `NEW` / `RUN` / `ERASE`. Numeric elements start at 0, string elements at null. Errors: undeclared array use, re-declaration, subscript over the DIM value.

```
10:DIM C(13)          'numeric, 14 elements
20:DIM F$(10)         'string, 11 elements
30:DIM H(4,6)         '5x7 numeric, 35 elements
40:DIM B$(7,5)*25     '8x6 string, 25 chars each
```

#### DMS
- **Format:** `DMS <angle>` — **Abbr.** `DM.` — **See also:** DEG
- **Purpose:** Convert decimal degrees (`dd.` with a decimal point) to `dd.mmssrr` (deg/min/sec/hundredths).

```
10:X=DMS 50.5 : PRINT X     → 50.3
```

#### DSKF  **(PC-1600)**
- **Format:** `DSKF "d:"` — **Abbr.** `DS.`
- **Purpose:** Free space in bytes on `S1:`, `S2:`, `X:` or `Y:`.

#### DEV$  **(PC-1600)**
- **Format:** `DEV$` — **See also:** SETDEV
- **Purpose:** String showing the current `SETDEV` output-routing settings.

---

### E

#### END
- **Format:** `END` — **Abbr.** `E.` — **See also:** STOP
- **Purpose:** Stop the program, close all files and the serial interface. Need not be the last line — place it before subroutine blocks so execution does not fall through. If omitted, the program ends when it runs out of lines (files still closed).

#### EOF  **(PC-1600)**
- **Format:** `EOF(<file#>)` — **Abbr.** `EO.`
- **Purpose:** `1` if the end of the sequential input file `<file#>` has been reached, else `0`.

```
10:IF EOF(1) THEN 100
```

#### ERASE
- **Format:** `ERASE <list of variable names>` — **Abbr.** `ERA.` — **See also:** CLEAR
- **Purpose:** Erase the named simple/array variables (not the fixed `A`–`Z` / `A$`–`Z$` / `@()`). An array is named with empty parentheses: `Z$()`; elements cannot be erased individually.

```
10:ERASE AB,Z$()
```

#### ERL
- **Format:** `ERL` — **See also:** ERN, ON ERROR GOTO, RESUME
- **Purpose:** Line number where the last error occurred (set only for errors during program execution).

#### ERN
- **Format:** `ERN` — **See also:** ERL, ON ERROR GOTO, RESUME
- **Purpose:** Error code of the last execution error.

```
10:ON ERROR GOTO 100
...
100:IF ERL=30 AND ERN=4 THEN PRINT "YOU HAVEN'T GOT A DATA LINE"
110:STOP
```

#### EXP
- **Format:** `EXP(<X>)` — **Abbr.** `EX.` — **See also:** LN
- **Purpose:** eˣ (e held as 2.718281828). `X` must be `-227.9559242 … +230.2585092`; below that returns 0. For other bases use `^`.

```
>PRINT EXP(10)     → 22026.46579
```

---

### F

#### FILES  **(PC-1600)**
- **Format:** `FILES "<d:>"` | `FILES "<d:filename>"` | `FILES "<d:ambiguous filename>"` — **Abbr.** `FI.` — **See also:** LFILES, SET
- **Purpose:** Show floppy / RAM-disk directory entries (name, `.BAS`, `P` protection, date, time) on screen.
- **Remarks:** No name → all files, one entry at a time (**↓** to scroll, any other key aborts except SHIFT/DEF/RCL/SML). Single name → that file. Wildcards: `*` = any run of characters (incl. none), `?` = one character.

```
>FILES"X:"
>FILES"S2:???1"
```

#### FOR … NEXT
- **Format:** `FOR <counter> = <initial> TO <final> [STEP <increment>]` … `NEXT <counter>` — **Abbr.** `F.` / `N.`
- **Purpose:** Repeat the lines between `FOR` and `NEXT`. `<final>` and `<increment>` are numeric (−32768..32767 as integers in format 1, or any expressions in format 2). Loops may nest; `NEXT` may list its counter. After a normal exit the counter holds `<final> + <increment>` (see the PC-1500A compatibility note in the reference).

---

### G

#### GCURSOR
- **Format:** `GCURSOR <X>[,<Y>]` — **Abbr.** `GC.` — **See also:** GPRINT
- **Purpose:** Position the graphics cursor at dot `(X,Y)` for a following `GPRINT`. Normal range X `0–155`, Y `0–31` (any value −32768..32767 accepted, off-screen just not visible). Y omitted = current Y. In MODE 1 the Y parameter is meaningless — do not give it.

#### GLCURSOR
- **Format:** `GLCURSOR (<X>,<Y>)` — **Abbr.** `GL.` — **See also:** LCURSOR
- **Purpose:** In printer graphics mode, move the pen (lifted) to graphics coordinate `X,Y`, measured from the current origin, range −2048..2047.

#### GOSUB … RETURN
- **Format:** `GOSUB <line#/label>` … `RETURN` — **Abbr.** `GOS.` / `RE.` — **See also:** GOTO, ON..GOSUB
- **Purpose:** Call / return from a subroutine. `GOSUB` remembers the return point; the subroutine ends with `RETURN`, which resumes after the `GOSUB`. Subroutines may be called repeatedly and nested.

#### GOTO
- **Format:** `GOTO <line#/label>` — **Abbr.** `G.` — **See also:** GOSUB..RETURN, ON..GOTO
- **Purpose:** Unconditional jump (no return memory). If the target line is non-executable (`DATA`, `REM`) execution starts at the next executable line; a non-existent line → error. `GOTO` alone restarts from the first line; `GOTO <line#>` also resumes after a BREAK (cf. `CONT`).

#### GPRINT
- **Format:** `GPRINT [SET/OR/XOR,] <bit-image>;<bit-image>;…` | `GPRINT [SET/OR/XOR,] "<hex bit-image string>"` | `GPRINT` — **Abbr.** `GP.` — **See also:** GCURSOR
- **Purpose:** Draw columns of bit-image dots on the screen from the graphics cursor. Each item = 8 bits = one 7-dot-tall column (bit 1 top). Decimal or `&`-prefixed hex items separated by `;`, or one hex string (odd last char ignored). `SET` (default) writes the pattern; `OR` / `XOR` combine with existing dots. `GPRINT` alone moves the graphics cursor down one line without clearing.

```
GPRINT &0;&1C;&1C;&1C;&1C;&1C;&1C;&1C;&1C;&1C;&7F;&3E;&1C;&08
GPRINT 16;40;18;253;18;40;16
GPRINT "102812F0122810"
```

#### GRAD
- **Format:** `GRAD` — **Abbr.** `GR.` — **See also:** DEGREE, RADIAN
- **Purpose:** Set the angular unit to grads (`GRAD` on the status line).

#### GRAPH
- **Format:** `GRAPH` — **Abbr.** `GRAP.` — **See also:** TEXT
- **Purpose:** Put the printer in graphics mode; resets the `PAPER` print-range limits to their defaults.

---

### H

#### HEX$
- **Format:** `HEX$(<X>)` | `HEX$(&<X>)` — **Abbr.** `H.` — **See also:** VAL
- **Purpose:** Hex string (`&0`–`&FFFF`) for a value 0–65535 (rounded to integer). `<X>` must be a simple variable or number, **not** an expression — evaluate first into a variable.

```
50:D$=HEX$(X)
```

---

### I

#### IF … THEN [… ELSE]
- **Format:** `IF <condition> THEN <line#/label/statement>` | `… THEN … ELSE <line#/label/statement>` — **Abbr.** `IF` / `T.` / `EL.`
- **Purpose:** Branch on `<condition>`. `THEN` runs if true; if false, `ELSE` runs (or execution falls to the next line if there is no `ELSE`). `THEN`/`ELSE` may name a line, a label, or any statement. May be nested within the 80-character line limit.

#### INIT  **(PC-1600)**
- **Format:** `INIT "Sn:",{"F"|"M"|"P"}` | `INIT "X:"` | `INIT "COMn:",<buffer size>` — **Abbr.** `INI.` — **See also:** SETCOM, SETDEV, TITLE
- **Purpose:**
  1. **Module in slot 1/2** (MODE 0 only): `"F"` format as RAM disk; `"M"` add to internal user area (bigger programs); `"P"` program-storage area (one battery-backed program, survives removal). Fails on a module holding programs/files (clear with `KILL` / `NEW` first), a write-protected module, or a `TITLE`-selected program module. A CE-159 (8K) must be all-program or all-expansion.
  2. **`INIT "X:"`** — format a floppy (required for new disks; **erases** any existing contents).
  3. **`INIT "COMn:",<size>`** — receive-buffer size 80–16383 bytes, or `0` = minimum 40 bytes (also the power-on/reset default). Typical 256. Fails on insufficient memory or while a file is open `FOR APPEND`. Cannot be used inside a `FOR…NEXT` loop.

```
>INIT"S1:","F"
>INIT"X:"
```

#### INKEY$
- **Format:** `<string var> = INKEY$` | `= INKEY$(0)` | `= INKEY$(1)` — **Abbr.** `INK.`
- **Purpose:** Read one key code from the keyboard buffer without echoing. `INKEY$` / `INKEY$(0)` = the latest key; `INKEY$(1)` = the oldest buffered key. No key → null string. Only the keys in the manual's INKEY$ character table are returned.

```
300:A$=INKEY$
310:IF A$="" THEN 300
320:IF A$="Y" THEN 500
330:GOTO 300
```

#### INP  **(PC-1600)**
- **Format:** `INP(<port address>)` — **See also:** OUT
- **Purpose:** Read one byte directly from a Z-80A input port, `<port address>` `&0`–`&FFFF`.

#### INPUT
- **Format:** `INPUT [<message>;|,] <list of variables>` | `INPUT <variable>[,<variable>…]` (from RS-232C) — **Abbr.** `I.`
- **Purpose:** Read keyboard values into variables (or from a serial port designated by `SETDEV`). Multiple variables comma-separated; the user enters values one per `ENTER`. No message → `?` prompt; message + `;` → no `?`, cursor after the message; message + `,` → cursor on the next line. The serial-port form shows no prompt; via the CE-158 only fixed/simple variables (no arrays).

```
10:PRINT "VOLUME OF SOLID"
20:INPUT "ENTER L,B,H ",L,B,H
30:V=L*B*H
40:PRINT "VOLUME IS ";V
```

#### INPUT#  **(PC-1600)**
- **Format:** `INPUT#<file#>,<variable list>` | `INPUT#["<filename>";]<variable list>` — **Abbr.** `I.#` — **See also:** DIM, INPUT, OPEN, PRINT#, SET
- **Purpose:** Read items from a sequential file (disk / RAM disk by `<file#>` from `OPEN`; cassette by name, default = next file). Variable order and type must match the file; string variables must be long enough; arrays need `DIM`. Delimiters: comma / space / CR+LF for numbers, comma / CR+LF for strings; leading spaces ignored; a quote inside a string truncates it unless the whole item is quoted. Too few items in the file → waits (press BREAK); excess items are left unread. For cassette (format 2) arrays are given as `A(*)`.

#### INSTAT  **(PC-1600)**
- **Format:** `INSTAT "COM1:"` — **Abbr.** `INSTA.` — **See also:** OUTSTAT
- **Purpose:** RS-232C control-signal states as an 8-bit value. Bit (from bit 1): 1 = DTR, 2 = RTS, 3 = CTS, 4 = CD, 5 = DSR, 6 = CI; `0` = signal high, `1` = signal low; bits 7–8 always 0. (`63` / `&3F` = all six low, the default.)

#### INSTR
- **Format:** `INSTR([<col>,]X$,Y$)` | `INSTR([<col>,]"<string>","<char>")` — **Abbr.** `INS.`
- **Purpose:** Position of the first occurrence of `Y$` in `X$` (from `<col>`, default 1); `0` if not found or `X$` null.

```
20:N=INSTR(A$,"A")
```

#### INT
- **Format:** `INT(<X>)`
- **Purpose:** Largest integer `<= X` (rounds **down**, toward −∞): `INT(-3.3)` = `-4`, `INT(1.6)` = `1`.

---

### K

#### KBUFF$  **(PC-1600)**
- **Format:** `KBUFF$ = <string>` — **Abbr.** `KB.` — **See also:** INKEY$
- **Purpose:** Write up to 32 characters into the keyboard buffer (overwriting it) so they execute as if typed — the basis of "batch" command files. No CR is added unless included (e.g. `+CHR$(13)`).

```
200:KBUFF$="45"+CHR$(13)
210:INPUT A
```

#### KEY ON / OFF / STOP  **(PC-1600)**
- **Format:** `KEY(<key#>) ON` | `KEY(<key#>) OFF` | `KEY(<key#>) STOP` — **See also:** ON KEY GOSUB
- **Purpose:** Enable / disable a function key as a run-time branch trigger (with `ON KEY GOSUB`). `STOP` latches the last press for a later `ON`.

#### KEYSTAT  **(PC-1600)**
- **Format:** `KEYSTAT,[<repeat>][,<click>]` — **Abbr.** `KE.` — **See also:** INKEY$
- **Purpose:** `<repeat>` 0/1 = key auto-repeat off/on; `<click>` 0/1 = key click off/on. Power-on keeps the last values; ALL RESET default is `KEYSTAT,0,0`.

#### KILL  **(PC-1600)**
- **Format:** `KILL "<d:filename>"` — **Abbr.** `K.` — **See also:** SAVE, SET
- **Purpose:** Delete a file on floppy / RAM disk (`.BAS` must be given for BASIC files). Errors if `SET`-protected, if the RAM module write-protect switch is on, or if the file is open.

---

### L

#### LCURSOR
- **Format:** `LCURSOR <column>` — **Abbr.** `LC.` — **See also:** GLCURSOR, PCONSOLE, TAB
- **Purpose:** In printer TEXT mode, move the pen to a print column 0…(line length set by `PCONSOLE`). (PC-1500's `LCURSOR` is `TAB` here.)

#### LEFT$
- **Format:** `LEFT$(<X$>,<N>)` — **Abbr.** `LEF.` — **See also:** MID$, RIGHT$
- **Purpose:** Leftmost `N` characters of `X$` (`N` 0–80, truncated; `N<1` → null; `N` over the length → whole string).

#### LEN
- **Format:** `LEN(<X$>)`
- **Purpose:** Number of characters in `X$`, including spaces and control codes.

#### LET
- **Format:** `[LET] <variable> = <expression>[,<variable> = <expression>…]` — **Abbr.** `LE.`
- **Purpose:** Assignment. `LET` is optional except inside a `THEN`/`ELSE` clause. Expression type must match the variable type. Multiple assignments comma-separated.

#### LF
- **Format:** `LF [<lines>]` — **See also:** CSIZE, PITCH, PAPER
- **Purpose:** Feed printer paper. Bare `LF` = one line. `<lines>` positive = forward, negative = reverse, within the range set by `PAPER`. Line height depends on `CSIZE`.

#### LFILES  **(PC-1600)**
- **Format:** `LFILES "<d:>"` | `LFILES "<d:filename>"` | `LFILES "<d:ambiguous filename>"` — **Abbr.** `LF.` — **See also:** FILES, SETDEV
- **Purpose:** As `FILES`, but the directory goes to the printer or, if `SETDEV` selected a port, to `COM1:`/`COM2:`. On the CE-1600P it prints at character size 2 regardless of `CSIZE`.

#### LINE  **(PC-1600)**
- **Format:** `LINE [(X1,Y1)]-(X2,Y2)[,<dot toggle>][,<pattern>][,B|,BF]` — **Abbr.** `LIN.` — **See also:** LLINE
- **Purpose:** Draw a line on the **graphics screen** from `(X1,Y1)` (or the graphics cursor) to `(X2,Y2)`, coords relative to `(0,0)` top-left.
- **Remarks:** `<dot toggle>`: `S` (default) 1-bits set dots on; `R` 1-bits clear dots (inverse video); `X` inverts dots along the line. `<pattern>` 0–65535 / `&0000`–`&FFFF` is the 16-bit repeating dot pattern (`&FFFF` solid, `&AAAA` dotted, `&6666` dashed). `B` draws a box on the diagonal, `BF` a filled box. Coords accept −32768..32767 but only X 0–155 / Y 0–31 show.

```
40:LINE (N,10)-(M,20),,BF
```

#### LIST
- **Format:** `LIST` | `LIST <line#>` — **Abbr.** `L.` — **See also:** LLIST
- **Purpose:** List the program on screen (one screenful from the lowest line; **↓** scrolls). `LIST <line#>` shows one line; a too-high number → error.

#### LLINE
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

#### LLIST / LLIST*
- **Format:** `LLIST[*]` | `LLIST[*] <line#>` | `LLIST[*] <line#>,<line#>` | `LLIST[*] <line#>,` | `LLIST[*] ,<line#>` — **Abbr.** `LL.` — **See also:** LIST
- **Purpose:** As `LIST` but to the printer (or to the serial port if `SETDEV` opened one with the port-output option). Line ranges as `DELETE`.
- **Remarks:** Output wraps per the `PCONSOLE` line length; length 16–17 with an over-long line → **ERROR 76**, no output. Line terminator (CR / LF / LF+CR) per `PCONSOLE`. Ignored if a `PASS` password is set. `LLIST*` prints **only** apostrophe-comment lines that start at the head of a line, suppressing the line number and `'` (a trailing `;` continues the comment on the listing) — useful for printing a program's documentation. To a serial port, keep line numbers ≤ 99 (2 digits) for complete `LLIST*` output. `CSIZE 1` lists at size 1, otherwise size 2; text mode is selected automatically.

#### LN
- **Format:** `LN(<X>)` — **See also:** EXP
- **Purpose:** Natural logarithm (base e) of `X > 0`.

#### LOAD / LOAD*
- **Format:** `LOAD "<d:filename>"[,R]` | `LOAD* "<d:filename>"` — **Abbr.** `LOA.` — **See also:** CHAIN, LLIST*, MERGE, REM, RUN, SAVE
- **Purpose:** Load a file from `S1:`/`S2:`, `X:`/`Y:`, `COMn:` or `CAS:` into memory. `,R` = run it afterwards (as `RUN`; used by AUTORUN); non-BASIC content → execution error. Open files are closed on `LOAD` unless `,R`.
- **`LOAD*`** prefixes each loaded line with a number (from 10, step 10) and an apostrophe, turning an ASCII text file into BASIC comment lines — the only way the PC-1600 handles raw text at file level (list it back with `LLIST*`). From a serial port, CR+LF = end of line, `&1A` = end of file.

```
>LOAD"BIOCALC",R
```

#### LOC  **(PC-1600)**
- **Format:** `LOC(<file#>)`
- **Purpose:** Records read/written since the file was opened (floppy / slot modules only). One record = 256 bytes.

#### LOCK / UNLOCK
- **Format:** `LOCK` | `UNLOCK` — **Abbr.** `LOC.` / `UN.`
- **Purpose:** Disable / re-enable the MODE key (locks the current PRO or RUN mode; cannot lock RESERVE).

#### LOF  **(PC-1600)**
- **Format:** `LOF(<file#>)` — **See also:** DSKF
- **Purpose:** Size in bytes of an open file on floppy / slot modules.

#### LOG
- **Format:** `LOG(<X>)` — **Abbr.** `LO.`
- **Purpose:** Common (base-10) logarithm. Other base: `LOG(X)/LOG(B)`. Antilog: `10^x`.

#### LPRINT / LPRINT USING
- **Format:** `LPRINT [<list>][;]` | `LPRINT USING <format>;<list>` | `LPRINT TAB <col>;<expr>;…` — **Abbr.** `LP.` — **See also:** PCONSOLE, PRINT, PZONE, TAB
- **Purpose:** As `PRINT` / `PRINT USING` but to the printer (or a serial port per `SETDEV`). Bare `LPRINT` = blank line / feed. `;` between items = adjacent; `,` = next print zone (numbers right-justified if at a zone start, else left in the next zone); `TAB <col>` sets the column (over the `PCONSOLE` width → error). Trailing `;` keeps the next output on the same line. In graphics mode, lines are not terminated with CR/LF.

---

### M

#### MAXFILES  **(PC-1600)**
- **Format:** `MAXFILES = <number of files>` — **Abbr.** `MA.` — **See also:** CLOSE, OPEN
- **Purpose:** Maximum simultaneously open files, 0–15 (0 at power-on, so it must be set before any `OPEN`). All files must be closed when it runs. Each file reserves 313 bytes (grows as needed). Cannot be used inside a `FOR…NEXT` loop.

#### MEM
- **Format:** `MEM` — **Abbr.** `M.` — **See also:** STATUS
- **Purpose:** Unused user-area memory in bytes, including the variable area (= `STATUS 0`).
- **Remarks:** Always the **S0** area (internal RAM plus any module folded into it), whatever `TITLE` selects: it is computed from the S0 program end and the variable area only. For a program module in slot 1 / slot 2 use `STATUS 259` / `STATUS 260`. (ROM: the LH-5803 `STATUS 0` code, $CBF8/$CC30.)

#### MERGE  **(MODE 1)**
- **Format:** `MERGE` | `MERGE "<filename>"` — **Abbr.** `MER.` — **See also:** CLOAD
- **Purpose:** Load a cassette program alongside the one in memory (PC-1500 mode). `MERGE` alone takes the next tape program; `"filename"` searches for it.
- **Remarks:** Merged programs keep their own line numbers in separate memory areas; move between them only with `GOTO "label"` / `LIST "label"` (plain `LIST` shows only the last-merged program), so give every merged program a first-line label. With `READ`/`DATA`, label the `DATA` line and put `RESTORE "label"` before the `READ`.

#### MID$
- **Format:** `MID$(<X$>,<N>,<M>)` — **Abbr.** `MI.` — **See also:** LEFT$, RIGHT$
- **Purpose:** `M` characters of `X$` from position `N` (`N` 1–80, `M` 0–80; `N` out of range → null).

```
20:Y$=MID$(Z$,3,4)     'Z$="ABCDEFG" → "CDEF"
```

#### MOD
- **Format:** `<n> MOD <m>` — **See also:** INT
- **Purpose:** Remainder of `n / m` (both rounded to nearest integer first).

#### MODE
- **Format:** `MODE [0]` | `MODE 1` — **Abbr.** `MO.`
- **Purpose:** Select the screen personality. **Direct mode only** — cannot appear on a program line.
- **Remarks:** `MODE` / `MODE 0` — all four lines active, PC-1600 character set, CE-1600P and PC-1600 peripherals; `PRINT` fills lines then scrolls, wrapping > 26-char items. `MODE 1` — PC-1500 mode: only the bottom line active (scrolls only in PRO mode / for `INPUT`), 26-char fixed line, PC-1500 character set and peripherals; `PRINT` overwrites the bottom line and truncates > 26 chars. PC-1500 programs must run in MODE 1. After a mode change the screen is not cleared; the prompt/cursor go to home. Everything else `MODE 1` changes: [MODE 0 and MODE 1 in detail](#mode-0-and-mode-1-in-detail).

### N

#### NAME  **(PC-1600)**
- **Format:** `NAME "<d:oldname>" AS "<d:newname>"` — **Abbr.** `NA.` — **See also:** COPY, FILES
- **Purpose:** Rename a file on floppy / RAM disk (same drive for both names; `newname` must be unused). Fails if the disk is write-protected, the file is `SET`-protected, the file is open, or the RAM module write-protect switch is on.

#### NEW
- **Format:** `NEW` | `NEW "Sn:"[,<address>]` | `NEW <address>` | `NEW 0` — **See also:** DELETE, STATUS, TITLE
- **Purpose:** Delete all program lines and/or allocate the machine-language program area. In RESERVE mode, clears all function-key assignments. Always use `NEW` before typing a replacement program so stray old lines are not left behind.
- **Remarks:** Addresses are relative to top-of-memory 0; the machine-language area is `197`…`<address>`. `NEW` clears the `TITLE`-selected memory, keeping the current ML allocation. `NEW "Sn:"` targets a specific area (`S0:` main, `S1:`/`S2:` slot modules). `NEW "Sn:",<address>` also sets the ML upper address. `NEW 0` clears everything and sets the ML area to 0 (address 197). MODE 1 forms (`NEW`, `NEW <address>`, `NEW 0`) act on the PC-1500 user area.

```
>NEW 1001     'reserve 197–1000 for machine language
```

### O

#### ON ADIN GOSUB  **(PC-1600)**
- **Format:** `ON ADIN (<level1>,<level2>) GOSUB <line#/label>` — **Abbr.** `O. AD. GOS.` — **See also:** ADIN ON/OFF/STOP, AIN, RETI
- **Purpose:** Branch when the analog input leaves the range `level1`…`level2` (`0 <= level1 < level2 <= 255`). Subroutine must end with `RETI`. Max 8 interrupts per program. Default after the statement is `ADIN STOP` unless `ADIN ON` follows.
- **Note:** first execute `POKE &F12C,(PEEK &F12C) OR 1`.

#### ON COMn GOSUB  **(PC-1600)**
- **Format:** `ON COMn GOSUB <line#/label>` — **Abbr.** `O. COM GOS.` — **See also:** COMn ON/OFF/STOP, RETI
- **Purpose:** Branch on an interrupt at port `n` (`1` RS-232C, `2` optical). `RETI` to return; max 8 interrupts; default `COMn STOP` unless `COMn ON` follows.

#### ON ERROR GOTO
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

#### ON … GOSUB / ON … GOTO
- **Format:** `ON <numeric expression> GOTO <list of line#s/labels>` | `ON <numeric expression> GOSUB <list…>` — **Abbr.** `O. G.` / `O. GOS.` — **See also:** GOSUB..RETURN, GOTO
- **Purpose:** Jump to the *n*-th target in the list, where *n* is the (truncated) value of the expression. Out of range (`<1` or `>` list length) → fall through to the next line. For `ON…GOSUB` each target must start a subroutine.

#### ON KEY GOSUB  **(PC-1600)**
- **Format:** `ON KEY GOSUB <list of line#s/labels>` — **Abbr.** `O. KEY GOS.` — **See also:** KEY ON/OFF/STOP, RETI
- **Purpose:** Branch when F1–F6 is pressed while running — F1 → first target … F6 → sixth (extra items unused; missing items = no effect). Each subroutine ends with `RETI`. Cleared by `RUN` / `END`; default `KEY STOP` unless `KEY(n) ON` follows.

#### ON PHONE GOSUB  **(PC-1600)**
- **Format:** `ON PHONE GOSUB <line#/label>` — **Abbr.** `O. PH. GOS.` — **See also:** PHONE ON/OFF/STOP, RETI
- **Purpose:** Branch on a modem input on the RS-232C **CI** signal (pin 9). `RETI` to return; max 8 interrupts; default `PHONE STOP` unless `PHONE ON` follows.

#### ON TIME$ GOSUB  **(PC-1600)**
- **Format:** `ON TIME$ = "MM/DD/HH/mm" GOSUB <line#/label>` — **Abbr.** `O. TI. GOS.` — **See also:** RETI, TIME$, TIME$ ON/OFF/STOP
- **Purpose:** Branch when the real-time clock reaches the given time. `RETI` to return; max 8 interrupts; default `TIME$ STOP` unless `TIME$ ON` follows.

#### OPEN  **(PC-1600)**
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

#### OUT  **(PC-1600)**
- **Format:** `OUT <port address>,<list of expressions>` — **See also:** INP
- **Purpose:** Write bytes (values 0–255) directly to consecutive Z-80A output ports from `<port address>` (`&0`–`&FFFF`).

```
>OUT 80,187
```

#### OUTSTAT  **(PC-1600)**
- **Format:** `OUTSTAT "COM1:"[,<setting>]` — **Abbr.** `OU.` — **See also:** INSTAT
- **Purpose:** Set RS-232C **RTS**/**DTR**: `<setting>` 0 = both high, 1 = RTS high/DTR low, 2 = RTS low/DTR high, 3 = both low. With no setting, both stay high during serial commands / while receiving and low otherwise; RTS drops low automatically when the receive buffer fills.

### P

#### PAPER
- **Format:** `PAPER <type>[,<limit from>][,<limit to>]` — **Abbr.** `PAP.` — **See also:** GRAPH, TEXT
- **Purpose:** Printer paper type (`C` cut sheet, `R` roll; ALL RESET default `R`) and vertical print range in 0.2 mm units. `<limit from>` (reverse) 30–2047, default 30 (cut) / 999 (roll); `<limit to>` (forward) 30–2047, default 1354 (cut) / 999 (roll); in TEXT mode with roll paper `<limit to>` defaults to infinite. `TEXT`, `GRAPH`, `PAPER`, the printer feed key and power-on reset the limits from the current paper/pen position. In graphics mode, a pen move beyond the limits is reflected back at the limit.

#### PASS
- **Format:** `PASS "<string>"` — **Abbr.** `PAS.` — **See also:** CLOAD, CSAVE
- **Purpose:** Set/clear a program password (≤ 8 characters, any keyboard characters except `"`). While set, the machine stays in RUN mode: `LIST`, `LLIST`, `(C)SAVE`, `(C)LOAD`, `NEW`, `TITLE`, `MERGE`, `CHAIN` and the ↑/↓ keys are disabled, and lines can't be added/deleted. Protects **all** programs in memory. Remove by issuing `PASS` again with the same string; change by old then new. Needs a program in memory.

#### PAUSE
- **Format:** `PAUSE [<list of expressions>][;]` | `PAUSE USING <format>;<list>` — **Abbr.** `PA.` — **See also:** PRINT, WAIT
- **Purpose:** Like `PRINT`, but in MODE 1 the data shows for a fixed **0.85 s** then scrolls (≈ `WAIT` + `PRINT`). MODE 1 list max 2 items.

#### PCONSOLE  **(PC-1600)**
- **Format:** `PCONSOLE "LPT1:",[<line length>],[<EOL code>],[<offset>]` | `PCONSOLE "COMn:",[<line length>],[<EOL code>]` — **Abbr.** `PCONS.`
- **Purpose:** Print format / end-of-line for the printer (`LPT1:`) or a serial port (`COMn:`).
- **Remarks:** `<line length>` 16–255, or `0` = unlimited. `<EOL code>` 0 = CR, 1 = LF, 2 = CR+LF (other values → error); **ignored on the CE-1600P** (all CR → CR+LF). `<offset>` = blank columns at the line head, `<= line length - 4`. Defaults all 0. For file transfers via a port (`SAVE`/`LOAD`/`PRINT#`/`INPUT#`) the EOL is fixed CR+LF. Settings persist until changed; `CSIZE` scales `<line length>` proportionally (offset unchanged).

```
>PCONSOLE "LPT1:",42,2,3
```

#### PEEK
- **Format:** `PEEK(<address>)` | `PEEK #<bank>,<address>` — **Abbr.** `PE.` — **See also:** POKE, XPEEK, XPOKE
- **Purpose:** One byte from memory (Z-80A space). `<address>` `&0`–`&FFFF`, `<bank>` 0–7. Format 1 with `&C000`–`&FFFF` reads bank-0 internal RAM; use format 2 for any other area. See Appendix D. (PC-1500's `PEEK` is `XPEEK` here.)

```
>PEEK#(0,100)     → 17
```

#### PHONE ON / OFF / STOP  **(PC-1600)**
- **Format:** `PHONE ON` | `PHONE OFF` | `PHONE STOP` — **Abbr.** `PH.` — **See also:** ON PHONE GOSUB
- **Purpose:** Enable/disable modem interrupts on the RS-232C port. `ON` + `ON PHONE GOSUB` to branch; `STOP` latches the last request for a later `ON`. **Default STOP.**

#### PITCH
- **Format:** `PITCH [<char pitch>][,<line spacing>]` — **Abbr.** `PI.` — **See also:** CSIZE
- **Purpose:** Printer character pitch and line spacing (TEXT mode). Char pitch: default `6 × CSIZE × 0.2 mm`, else `<char pitch> × 0.2 mm` (4–240). Line spacing: default `12 × CSIZE × 0.2 mm`, else `<line spacing> × 0.2 mm` (4–255). Reset to defaults by `TEXT`, `GRAPH`, `CSIZE`, `LLIST`, `TEST`, `PCONSOLE`. In graphics mode `<line spacing>` is ignored.

#### POINT
- **Format:** `POINT (<X>,<Y>)` | `POINT (<Xcol>)` — **Abbr.** `POI.` — **See also:** GPRINT, PRESET, PSET
- **Purpose:** Format 1: `1`/`0` — is the dot at `(X,Y)` set? Format 2: the 0–255 bit-image value of the 8-dot column `<Xcol>` on the current line. Normal range X 0–155, Y 0–31 (values −32768..32767 accepted, off-screen returns 0).

#### POKE
- **Format:** `POKE [#<bank>,]<address>,<integer list>` — **Abbr.** `PO.` — **See also:** PEEK, XPEEK, XPOKE
- **Purpose:** Write bytes (0–255) to consecutive addresses from `<address>` (`&0`–`&FFFF`) in `<bank>` 0–7 (default: the bank holding the running program's header). Insufficient free memory → error. See Appendix D. (PC-1500's `POKE` is `XPOKE` here.)

```
>POKE #2,&FF00,255,255
```

#### POWER  **(PC-1600)**
- **Format:** `POWER OFF` | `POWER AOFF [(<n>)]` — **Abbr.** `POW.` — **See also:** ALARM$, ARUN, WAKE$
- **Purpose:** `POWER OFF` — switch off immediately (direct or in-program). `POWER AOFF (0)` (or no arg) — auto power-off 10 min after the last key/step; `POWER AOFF (1)` — disable auto power-off. ALL RESET default: auto-off after 10 min, auto-on from modem or at a set time (`WAKE$`).

#### PRESET
- **Format:** `PRESET(<X>,<Y>)` — **Abbr.** `PRE.` — **See also:** PSET, LINE
- **Purpose:** Turn **off** the screen dot at `(X,Y)` (range 0–155 × 0–31; wider values accepted, no effect off-screen).

#### PRINT
- **Format:** `PRINT [<list of expressions>][;]` — **Abbr.** `P.` — **See also:** MODE, PRINT USING, WAIT
- **Purpose:** Output to the screen. `;` between items = adjacent; `,` = next 13-column print zone (numbers right-justified, strings left-justified). Trailing `;` keeps the next output on the same line. `PRINT` alone = blank line. In MODE 1, execution waits for `ENTER` after each `PRINT` and the list is max 2 comma-separated items; in MODE 0 the display holds for the last `WAIT` time. No `TAB` (use `CURSOR` or `USING`).

```
50:PRINT "THE VALUES: ";A;B;C
60:PRINT "THE VALUES: ",A,B,C
```

#### PRINT# / PRINT# USING  **(PC-1600)**
- **Format:** `PRINT#<file#>,<list of expressions>` | `PRINT#<file#>,USING <format>;<list>` | `PRINT#["<filename>";]<list of variables>` — **Abbr.** `P.#` / `P.#U.` — **See also:** INPUT#, OPEN, PRINT USING
- **Purpose:** Write to a sequential output file (disk / RAM disk by `<file#>` from `OPEN`; cassette by name, default = current tape position, no name). `;` = items adjacent; `,` = a blank between items, and a literal delimiting comma must be written as its own quoted `","` item. Cassette form (3) allows only `,` as separator; arrays as `A(*)`, no single elements.

#### PRINT USING / USING
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

#### PSET
- **Format:** `PSET(<X>,<Y>)[,<function code>]` — **Abbr.** `PS.` — **See also:** PRESET, LINE
- **Purpose:** Turn **on** the screen dot at `(X,Y)` (0–155 × 0–31). Function code `X` = invert the dot instead. Wider coordinates accepted, no effect off-screen.

#### PZONE  **(PC-1600)**
- **Format:** `PZONE "COMn:",<width>` | `PZONE "LPT1:",<width>` — **Abbr.** `PZ.` — **See also:** LPRINT
- **Purpose:** Width of the comma-delimited print zones for `LPRINT` to the printer (`LPT1:`, width 8–80) or a serial port (`COMn:`, width 8–255). Numbers right-justified, strings left-justified in each zone. Default 20.

```
40:PZONE "LPT1:",10
50:LPRINT A,B,C
```

---

### R

#### RADIAN
- **Format:** `RADIAN` — **Abbr.** `RAD.` — **See also:** DEGREE, GRAD
- **Purpose:** Set the angular unit to radians (`RADIAN` on the status line).

#### RANDOM
- **Format:** `RANDOM` — **Abbr.** `RA.` — **See also:** RND
- **Purpose:** Reseed `RND` so a different sequence is produced from each power-on. Put at the start of a program.

#### RCVSTAT  **(PC-1600)**
- **Format:** `RCVSTAT "COMn:",<protocol>[,<timeout>]` — **Abbr.** `RC.` — **See also:** INSTAT, SNDSTAT
- **Purpose:** RS-232C receive handshake and serial timeout. `<protocol>` is an 8-bit value; bit 3 = CTS must be high, bit 4 = CD must be high, bit 5 = DSR must be high (bit = 0 means "must be high", 1 means "don't care"); bits 1,2,6,7,8 unused (set 0). No meaning for the optical port. `<timeout>` 0–255 in 0.5 s units; `0` = infinite (default).

#### READ … DATA
- **Format:** `READ <list of variables>` … `DATA <list of constants>` — **Abbr.** `REA.` / `DA.` — **See also:** DATA, RESTORE
- **Purpose:** Assign successive `DATA` constants to the `READ` variables (types must match). Too many variables → error; extra constants stay unread; re-read with `RESTORE`. On first execution with no preceding `RESTORE`, the search order for the first `DATA` is `S2:` → `S1:` → `S0:`. After a `GOTO`/`DEF` restart following a `READ`, the next `READ` continues at the next `DATA` line.

```
10:DIM B(10)
20:FOR I=1 TO 10:READ B(I):PRINT B(I):NEXT I
60:DATA 10,20,30,40,50
70:DATA 60,70,80,90,100
```

#### REM  /  '
- **Format:** `REM <remark>` | `'<remark>` — **See also:** LIST, LLIST
- **Purpose:** Comment; ignored at run time, shown by `LIST`. An apostrophe replaces `REM`. A jump to a `REM` line resumes at the next executable line. Append with `:REM …`; nothing may follow `REM` on the line.

```
20:DIM A$(3,5):REM RESERVING SPACE FOR A 3 X 5 ARRAY
30:'THIS LINE HAS NO EFFECT EITHER
```

#### RENUM
- **Format:** `RENUM [<new line#>][,<old line#>][,<increment>]` — **Abbr.** `REN.` — **See also:** DELETE, LIST
- **Purpose:** Renumber from `<old line#>` onward to start at `<new line#>` (default 10) in `<increment>` (default 10). Results > 65279 → error. `GOTO`/`GOSUB`/etc. targets are updated; a missing target aborts the renumber with `undefined in <line#>`; a computed `GOTO A*5` aborts with an error. Cannot renumber a program loaded in MODE 1 — save it ASCII (`,A`) and reload in MODE 0.

#### RESTORE
- **Format:** `RESTORE` | `RESTORE <line#/label>` — **Abbr.** `RES.` — **See also:** READ, DATA
- **Purpose:** Reset the `DATA` read pointer to the first item of the first (or specified) `DATA` line — if that line has no `DATA`, to the first `DATA` line after it.

```
60:RESTORE     'lets the same DATA be READ again
```

#### RESUME
- **Format:** `RESUME [<line#>]` | `RESUME NEXT` — **Abbr.** `RESU.` — **See also:** ON ERROR GOTO, ERL, ERN
- **Purpose:** Leave an error handler and continue: `RESUME` at the statement that erred, `RESUME <line#>` at a given line, `RESUME NEXT` at the line after the error. Only valid inside an error-processing routine.

#### RETI  **(PC-1600)**
- **Format:** `RETI` — **See also:** ON ADIN GOSUB, ON COMn GOSUB, ON KEY GOSUB, ON PHONE GOSUB, ON TIME$ GOSUB
- **Purpose:** Return from an **interrupt** subroutine (the `ON … GOSUB` family). Like `RETURN`, but on execution it also services the last interrupt that arrived while the handler was running.

#### RIGHT$
- **Format:** `RIGHT$(<X$>,<N>)` — **Abbr.** `RI.` — **See also:** LEFT$, MID$
- **Purpose:** Rightmost `N` characters of `X$` (`N` 0–80, truncated; `<1` → null; over the length → whole string).

#### RLINE
- **Format:** `RLINE [(X1,Y1)]-(X2,Y2)[-(X3,Y3)…][,<type>][,<color>][,B]` — **Abbr.** `RL.` — **See also:** COLOR, LLINE
- **Purpose:** Like `LLINE`, but printer coordinates are **relative** — each point is measured from the previous point as origin. Up to five further segments. `<type>` 0–9, `<color>` 0–3 (defaults = current). `B` = rectangle on the `(X1,Y1)`–`(X2,Y2)` diagonal.

```
 5:PAPER R
10:GRAPH
20:GLCURSOR (40,40)
30:RLINE -(100,0)-(-100,100)-(0,-100)
40:TEXT
```

#### RMT ON / OFF
- **Format:** `RMT ON` | `RMT OFF` — **Abbr.** `RM.`
- **Purpose:** Enable / disable the computer's remote-control of cassette-recorder power during tape I/O.

#### RND
- **Format:** `RND(<X>)` — **Abbr.** `RN.` — **See also:** RANDOM
- **Purpose:** Random number, 10 significant digits. `X < 0` — restart the same sequence each call. `0 < X < 1` — a value in `[0,1)`. `X > 1` — an integer in `1…X`. Without `RANDOM` the same sequence recurs from each power-on.

#### ROTATE
- **Format:** `ROTATE <position>` — **Abbr.** `RO.`
- **Purpose:** Printer character orientation / head-travel direction, `<position>` 0–3 (0 normal, 1 down, 2 upside-down, 3 up). Default = last `ROTATE` value.

#### RUN
- **Format:** `RUN` | `RUN <line#/label>` — **Abbr.** `R.` — **See also:** CONT, GOTO, LOAD, MERGE
- **Purpose:** Execute from the lowest line (or the given line/label). Clears all variables and arrays and resets the `DATA` pointer.

#### RXD$  **(PC-1600)**
- **Format:** `RXD$`
- **Purpose:** Hex string of the byte currently arriving at the `SETDEV`-selected serial port. No data → `&20 &20`; communication error → `&3F &20 &20`.

---

### S

#### SAVE / SAVE*
- **Format:** `SAVE "<d:filename>"[,A]` | `SAVE* "<d:filename>"` — **Abbr.** `S.` — **See also:** LOAD, MERGE
- **Purpose:** Save a program/data file to `S1:`/`S2:`, `X:`/`Y:`, `CAS:` or a serial port. `,A` = ASCII (else compressed binary). `SAVE*` saves **only** comment lines (`REM` / `'`) — a simple text store (retrieve with `LOAD*`). No extension → `.BAS` added. Overwrites an existing file unless `SET`-P or the medium is write-protected. To a port, only `COM2:` with `,A`; CR+LF = end of line, `&1A` = end of file.

```
>SAVE"X:CHESS"
>SAVE*"S2:INFO"
```

#### SET  **(PC-1600)**
- **Format:** `SET "<d:filename>","P"` | `SET "<d:filename>"," "` — **See also:** KILL, NAME
- **Purpose:** File write-protection on floppy / RAM disk. With `"P"` set: no writing (`OPEN` for `OUTPUT`/`APPEND` fails), no `KILL`, no `NAME`. Release with a space in place of `"P"`.

#### SETCOM  **(PC-1600)**
- **Format:** `SETCOM "COMn:",[<BR>],[<WL>],[<PR>],[<ST>],[<XO>],[<SI>]` — **Abbr.** `SETC.` — **See also:** COM$, SETDEV
- **Purpose:** Serial-port protocol. `<BR>` 50–38400; `<WL>` 5–8; `<PR>` `E`/`O`/`N`; `<ST>` 1/2; `<XO>` `X`/`N` (XON/XOFF); `<SI>` `S`/`N` shift in/out (7-bit data only). Defaults: RS-232C `1200,8,N,1,X,S`; optical `38400,7,E,2,X,S`.
- **Note:** for `SAVE`/`LOAD`/`BSAVE`/`BLOAD` over a port, `<WL>` must be 8 and `<SI>` = `N` (unrestricted for ASCII `SAVE`/`LOAD` and for `PRINT#`/`INPUT#`).

```
>SETCOM "COM1:",,,E
```

#### SETDEV  **(PC-1600)**
- **Format:** `SETDEV "COMn:"[,PO][,KI]` — **Abbr.** `SE.`
- **Purpose:** Open a serial port for I/O. `PO` = send `LPRINT`/`LLIST`/`LFILES` output to the port; `KI` = take `INPUT` data from the port. `SETDEV` with no parameters reverts output to the printer and input to the keyboard.

#### SGN
- **Format:** `SGN(<X>)` — **Abbr.** `SG.`
- **Purpose:** `1` if `X > 0`, `0` if `X = 0`, `-1` if `X < 0`.

#### SIN
- **Format:** `SIN(<X>)` — **Abbr.** `SI.` — **See also:** ASN, COS, TAN
- **Purpose:** Sine of `X`; unit per `DEGREE` / `RADIAN` / `GRAD`.

#### SNDBRK  **(PC-1600)**
- **Format:** `SNDBRK "COMn:",<number>` — **Abbr.** `SNDB.`
- **Purpose:** Send `<number>` (1–255) continuous break characters to a port to halt the other end's transmission (send several — the first may be missed).

#### SNDSTAT  **(PC-1600)**
- **Format:** `SNDSTAT "COMn:",<protocol>[,<timeout>]` — **Abbr.** `SN.` — **See also:** RCVSTAT
- **Purpose:** RS-232C send handshake + serial timeout. `<protocol>` 0–255: bit 3 = CTS must be high, bit 4 = CD must be high, bit 5 = DSR must be high (0 = "must be high", 1 = "don't care"); bits 1,2,6,7,8 = 0. No meaning for the optical port. `<timeout>` 0–255 × 0.5 s, `0` = infinite (default).

#### SORGN
- **Format:** `SORGN` — **Abbr.** `SO.` — **See also:** LLINE, GLCURSOR
- **Purpose:** Make the current pen position the graphics origin `(0,0)`. Default origin: X left home, Y the current paper position.

#### SQR
- **Format:** `SQR(<X>)` — **Abbr.** `SQ.`
- **Purpose:** Square root; negative `X` → error.

```
>PRINT SQR(5)     → 2.236067977
```

#### STATUS
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

**Which program area.** 0–4 and 256–257 always describe the **S0** area, whatever `TITLE` selects (they read the S0 program pointers). 259 / 260 describe the slot-1 / slot-2 program module: the area limit minus the program end (0 or nothing if the slot holds no program module). An empty 32 KB program module reads 32571 = 32768 − 197 (the header and reserve area in front of the program).

#### STOP
- **Format:** `STOP` — **Abbr.** `ST.` — **See also:** CONT, END
- **Purpose:** Halt for debugging — shows `BREAK IN <line#>`; resume with `CONT` (if unedited). Unlike `END`, files are **not** closed.

#### STR$
- **Format:** `STR$(<numeric value>)` — **Abbr.** `STR.` — **See also:** VAL
- **Purpose:** Numeric → string (same digits, treated as characters; leading `-` if negative; floating-point form if it won't fit). Inverse of `VAL`.

---

### T

#### TAB
- **Format:** `TAB <column>` — **See also:** LCURSOR, LPRINT
- **Purpose:** In printer TEXT mode, move the pen to a print column 0…(`PCONSOLE` line length); columns scale with `CSIZE`. Usable inside `LPRINT`. (This is the PC-1600 name for the PC-1500's `LCURSOR`.)

#### TAN
- **Format:** `TAN(<X>)` — **Abbr.** `TA.` — **See also:** ATN, COS, SIN
- **Purpose:** Tangent of `X`; unit per `DEGREE` / `RADIAN` / `GRAD`. `X = 90°` → error.

#### TEST
- **Format:** `TEST` — **Abbr.** `TE.`
- **Purpose:** Printer self-test — draws four boxes in colour order black → blue → green → red, then sets text mode. Interrupt with BREAK.

#### TEXT
- **Format:** `TEXT` — **Abbr.** `TEX.` — **See also:** GRAPH
- **Purpose:** Set the printer to text mode: character size 2, origin at the pen's left position; resets the `PAPER` `<limit from>` / `<limit to>` to defaults.

#### TIME
- **Format:** `TIME` | `TIME = MMDDHH.mmss` — **See also:** TIME$
- **Purpose:** Read / set the clock as `MMDDHH.mmss` (month, day, hour, minute, seconds). Survives power-off; no leap-year handling. `TIME = 0` is not allowed on the PC-1600 (cf. PC-1500).

```
>TIME=031220.3215     'March 12, 20:32:15
```

#### TIME$  **(PC-1600)**
- **Format:** `TIME$ = "HH:mm:ss"` | `TIME$` — **Abbr.** `TI.` — **See also:** DATE$, TIME$ ON/OFF/STOP, ON TIME$ GOSUB
- **Purpose:** Real-time-clock time. As a statement sets it (`HH` 00–23, `mm`/`ss` 00–59, colon-separated); as a value returns `HH:mm:ss`.

#### TIME$ ON / OFF / STOP  **(PC-1600)**
- **Format:** `TIME$ ON` | `TIME$ OFF` | `TIME$ STOP` — **Abbr.** `TI.` — **See also:** ON TIME$ GOSUB
- **Purpose:** Enable/disable all `ON TIME$ GOSUB`. `STOP` disables but, on a later `TIME$ ON`, jumps immediately if the set time passed meanwhile. **Default STOP.**

#### TITLE  **(PC-1600)**
- **Format:** `TITLE "S0:"` | `TITLE "S1:"` | `TITLE "S2:"` | `TITLE?` — **Abbr.** `TIT.`
- **Purpose:** Select the active memory: `S0:` internal RAM (ALL RESET default), `S1:`/`S2:` the program module in that slot; `TITLE?` returns 0/1/2. Error if the slot holds a RAM-disk module rather than a program module. After `TITLE` during an execution halt, ↑/↓ may not list the module's program — use `LIST`.

#### TRON / TROFF
- **Format:** `TRON` | `TROFF` — **Abbr.** `TR.` / `TROF.` — **See also:** CONT, STOP
- **Purpose:** Turn the debugging trace on/off. With trace on, after each line the computer pauses 0.5 s with the line number at the right of the screen. Press **↑** while the number shows to switch to single-step (**↑** for each next line; hold **↑** to run without numbers; **←**/**→** scan the executed line; **↓** back to continuous). At a `PRINT`/`INPUT` halt, `ENTER` resumes. After a `STOP`/BREAK, **↑** resumes continuous trace, **↓** single-step. Trace stays on until `TROFF`.

---

### V

#### VAL
- **Format:** `VAL(<X$>)` — **Abbr.** `V.` — **See also:** STR$
- **Purpose:** String of numeric characters → value (inverse of `STR$`). Accepts `0`–`9`, one decimal point, sign; conversion stops at the first illegal character.

---

### W

#### WAIT
- **Format:** `WAIT <duration>[,P]` | `WAIT <duration>[,S]` | `WAIT` — **Abbr.** `W.` — **See also:** GPRINT, LINE, PRINT
- **Purpose:** Hold the display for a fixed time after `PRINT` / `GPRINT` / `LINE`. `<duration>` 0–65535 in ≈ 1/64 s units (`WAIT 64` ≈ 1 s). `P` (default) waits after the statement; `S` applies the wait to screen scrolling (both MODE 0 only). Bare `WAIT` = wait forever (until `ENTER`). `RUN` defaults: MODE 0 no pause, MODE 1 infinite.

```
20:WAIT 200
30:PRINT "READ THIS QUICKLY!"
40:WAIT
50:PRINT "NOW ITS GONE"
```

#### WAKE$  **(PC-1600)**
- **Format:** `WAKE$(0) = "<time>:<command string>"` | `WAKE$(1) = "<command string>"` | `WAKE$(0) = ""` | `WAKE$(1) = ""` — **Abbr.** `WAK.` — **See also:** KBUFF$, POWER
- **Purpose:** Auto power-on. Format 0 — turn on at `<time>` (`MM/DD/HH/mm`) and run `<command string>`. Format 1 — turn on and run the command when the RS-232C **CI** line (pin 9) goes high. The command string must end with `CHR$(&0D)` and be ≤ 26 characters. `= ""` releases the setting.

```
>WAKE$(0)="12/25/07/30:RUN"+CHR$(&0D)
```

---

### X

#### XCALL  **(LH-5803)**
- **Format:** `XCALL <address>[,<variable>]` — **Abbr.** `XC.` — **See also:** NEW, POKE, XPOKE
- **Purpose:** As `CALL`, but PC-1500-compatible (LH-5801/3 sub-processor address space). One variable is passed both ways via the **A** and **X** registers: a numeric value (integer −32768..32767) in **X**, returned in the same variable if carry is set on exit; a string variable → **X** = string address, **A** = length. Two-character variable names must be `DIM`ed. The routine must be in memory via `XPOKE`.
- **Remarks:** Works in both MODEs, but the LH-5803 sees `&0`–`&3FFF` as the program module only in MODE 1, and in MODE 0 the variable must be `A`–`Z` / `A$`–`Z$` (else ERROR 110). See [MODE 0 and MODE 1 in detail](#mode-0-and-mode-1-in-detail).

```
400:XCALL 57405,X
```

#### XPEEK / XPEEK#  **(LH-5803)**
- **Format:** `XPEEK <address>` | `XPEEK# <address>` — **Abbr.** `XP.` — **See also:** PEEK, POKE, XPOKE
- **Purpose:** As `PEEK`, PC-1500-compatible. `XPEEK` reads memory area 0 (ME 0); `XPEEK#` reads memory area 1 (ME 1), currently accessed bank. See Appendix D.
- **Remarks:** Addresses are LH-5803 addresses. Works in both MODEs. `&4000`–`&7FFF` is always internal RAM (= `PEEK &C000`–`&FFFF`); `&0`–`&3FFF` holds the program module only in MODE 1. See [MODE 0 and MODE 1 in detail](#mode-0-and-mode-1-in-detail).

```
>XPEEK 100     → 37
```

#### XPOKE / XPOKE#  **(LH-5803)**
- **Format:** `XPOKE <start address>,<integer list>` | `XPOKE# <start address>,<integer list>` — **Abbr.** `XPO.` — **See also:** PEEK, POKE, XPEEK
- **Purpose:** As `POKE`, PC-1500-compatible. `XPOKE` writes to memory area 0 (ME 0); `XPOKE#` to memory area 1 (ME 1), currently accessed bank. `<integer list>` bytes 0–255, comma-separated, into consecutive addresses. See Appendix D.
- **Remarks:** Same address rules as `XPEEK`. In MODE 0 only `A`–`Z` may appear in the list (else ERROR 110). See [MODE 0 and MODE 1 in detail](#mode-0-and-mode-1-in-detail).

```
>XPOKE 100,255,255
```

---

## Appendices

### A. Replacing the batteries

Two low-battery warnings: the **BATT** status-line symbol (also lit for a low printer battery) and,
during execution, an ERROR code (see [PC-1600 Error Codes](PC-1600-Error-Codes.md)). The **Memory
Safe Guard** does **not** hold memory when the batteries are dead or removed — internal RAM *and*
memory modules are lost (program modules keep their own backup battery).

To preserve data across a battery change:
- **AC adapter method:** power off → connect the adapter (outlet first, then computer) → swap
  batteries → unplug adapter → power on. Memory intact, no reset needed.
- **Save method:** `SAVE` programs/data to disk, tape or RAM disk → replace batteries → ALL RESET
  → `LOAD` back.

Otherwise: replace batteries, then ALL RESET to initialise.

### B. Replacing the RAM modules

Two module types: **program modules** (battery-backed, contents survive removal, hot-swappable)
and **memory modules** (not backed up — contents lost on removal).

Installing a memory module shows `NEW 0?:CHECK` at power-on (the computer noticed the memory-size
change). To clear all memory (internal + module): `CL`, set PRO mode, `MODE` if needed, then
`NEW 0` `ENTER`. `NEW` in PRO mode does **not** clear RESERVE function-key strings — run `NEW` in
RESERVE mode as well. Program modules stay independent of internal RAM until selected with
`TITLE`. `MEM` reports free user memory (11834 bytes with no modules). Expansion text fills
`S2:` → `S1:` → internal RAM.

### C. Character code tables

- **MODE 0** — a set close to the **IBM PC** character set: upper/lower case, digits, symbols,
  plus graphic characters, Greek letters and international characters. Look up a code by column
  header (high nibble) then row label (low nibble); e.g. `a` = `&61`. Any code can be shown with
  `CHR$(<code>)`.
- **International set** — codes `&80`–`&A8` map onto the alphabetic keys when the **KB II** button
  is pressed (template supplied).
- **MODE 1** — the set is trimmed for PC-1500 compatibility; codes `&27`, `&5B`–`&5F`, `&60`,
  `&7B`–`&7F` differ from MODE 0 (notably `&5B` = √, `&5D` = π shown PC-1500-style).

*(The manual's printed grid is not reproduced here — the OCR of it is unreliable. Consult
Appendix C of the manual, or the PC-1500 character tables, for exact glyphs.)*

### D. Memory maps

- The PC-1600 addresses **8 banks (0–7)**. Banks **0–3** are RAM; banks **4–6** hold internal
  system ROMs and peripheral memory (e.g. CE-1600P, CE-158, CE-150 ROMs); bank **7** is unused
  but addressable. Two views exist: from the **Z-80A** main processor and from the **LH-5803**
  sub-processor.
- **Bank 0 internal RAM** (Z-80A view), `C000H`–`FFFFH`: header (`C000`), Reserve Program Area
  (`C008`), Machine Program Area (allocatable, from `C0C5`), BASIC Program Area, Free Area,
  Variables Area, Work Area (`F000`–`FFFF`). With no expansion modules the **user area is 11834
  bytes**.
- Machine-language area addresses are relative to top-of-memory 0; lower bound `197`, upper bound
  set by `NEW <address>` (max 65535). `NEW 0` sets it to zero (→ MEM = 11834).
- `STATUS 0/2/3/256/257/258` report the free-area bounds and banks; `259`/`260` the slot module
  free space (see the `STATUS` dictionary entry).
- Expansion RAM in slots occupies `8000H`–`FFFFH` of banks 1–3; a `CE-1600M` can be split between
  program storage and free-area expansion.

### E. Machine-language programs

The PC-1600 can be programmed directly in **Z-80A** assembler. BASIC commands for machine code —
some address the main Z-80A, some the **LH-5803** sub-processor:

| Main Z-80A | LH-5803 sub-processor (PC-1500 set) |
|-----------|-------------------------------------|
| `CALL`, `PEEK`, `POKE`, `INP`, `OUT`, `BLOAD`, `BSAVE` | `XCALL` (= PC-1500 `CALL`), `XPEEK`/`XPEEK#` (= `PEEK`/`PEEK#`), `XPOKE`/`XPOKE#` (= `POKE`/`POKE#`), `CLOAD M`, `CSAVE M` |

Allocate space with `NEW` (sets the lower address of the BASIC program area). System utilities and
peripheral ROMs are reachable but need memory-map knowledge beyond the manual (Appendix D).

### F. Error codes

See **[PC-1600 Error Codes](PC-1600-Error-Codes.md)**.

### G. BASIC command list

Folded into the [command dictionary above](#14-basic-command-dictionary) and, grouped by topic,
the [PC-1600 Command Index](PC-1600-Command-Index.md).

### H. Compatibility with the PC-1500 model and peripherals

PC-1500 BASIC generally runs on the PC-1600 in **MODE 1**. Most command names match; disk/RAM-disk
and serial-interrupt commands are PC-1600-only. PC-1500 tape programs load and run unchanged.
What MODE 1 changes, and why, is in [MODE 0 and MODE 1 in detail](#mode-0-and-mode-1-in-detail).

**Renamed commands** (PC-1600 → PC-1500):

| PC-1600 | PC-1500 |
|---------|---------|
| `TAB` | `LCURSOR` |
| `LLINE` | `LINE` |
| `LINE` | *(no equivalent — screen line)* |
| `XCALL` | `CALL` |
| `CALL` | *(no equivalent — Z-80A)* |
| `XPOKE` / `XPEEK` / `XPOKE#` / `XPEEK#` | `POKE` / `PEEK` / `POKE#` / `PEEK#` |
| `POKE` / `PEEK` | *(no equivalent — Z-80A space)* |

**Notes:**
- `TIME = 0` cannot be set on the PC-1600 (many PC-1500 programs use it to reset a counter).
- **Running a PC-1500 program:** fit a module (CE-151/155/159/161, < 16 KB) in a slot, `MODE 1`,
  then `RUN`.
- **Typing one in:** change every `LINE` to `LLINE`; add a comment line marking it as PC-1500.
- **Tape:** PC-1500 CE-150 tapes load into the PC-1600 (MODE 1) unmodified; PC-1600 CE-1600P tapes
  do **not** load on a PC-1500 (different tape format and memory allocation).
- **Printing to the CE-1600P** like a CE-150: `CSIZE 2` · `PZONE "LPT1:",18` · `PCONSOLE "LPT1:",18,0,0`.
  Listing a PC-1600 program via CE-150/CE-158/CE-162E prints PC-1600 command names as their PC-1500
  equivalents (e.g. `LLIST` → `LIST`); `LCURSOR` from a PC-1500 tape lists as `-` on the CE-1600P
  (replace with `TAB`).
- **PC-1500A is *not* compatible:** its free user area `&7C00`–`&7FFF` is used by the PC-1600 system.
- **Early PC-1500s:** `FOR…NEXT` leaves the counter 1 higher than the PC-1600, and `IF…THEN` treats
  only `> 0` as true (PC-1600: `≠ 0`). Detect with `PEEK &C5C0` = 6.

### I. Care & troubleshooting

Keep the glass LCD in its case; avoid temperature/humidity extremes and direct sun; beware winter
static (never touch module pins); clean with a dry cloth only; remove batteries before long
storage; use an authorised service centre.

| Symptom | Try |
|---------|-----|
| Powered on, screen blank | Press `ON` again; check the low-battery symbol; check the AC adapter; adjust the contrast dial |
| Display OK, keys dead | `CL`; power off/on; press RESET alone (simple reset); `CL` + RESET (ALL RESET) |
| Calculation shows as a BASIC line with `:` after the first number | Press `MODE` to switch PRO → RUN |

### J. Specifications

| | |
|--|--|
| Main CPU | SC7852 — Z-80A-compatible CMOS, 3.58 MHz |
| Slave CPU | LH5803 — PC-1500 compatible, 1.3 MHz |
| Sub CPU | LU57813P — 307.2 kHz |
| Display | LCD, adjustable contrast; 26 × 4 characters; 156 × 32 dot graphics |
| Keyboard | 63 alphanumeric + 6 function keys |
| ROM | 96 KB |
| RAM | 16 KB, expandable to 80 KB; user area 11834 bytes; 2 rear expansion slots |
| Interfaces | RS-232C serial, optical serial, analog input |
| Features | Low-battery display, real-time clock, alarm / auto power on-off, external interrupts, comms |
| Power | 6 V DC — 4 × AA (SUM-3 / R6), or AC adapter EA-160 / EA-150; 0.48 W |
| Battery life | ≈ 25 h at 20 °C (10 min processing + 50 min display per hour) |
| Dimensions | 195 × 86 × 25.5 mm |
| Weight | ≈ 390 g with batteries |
| Operating temp | 0–40 °C |

### K. Syntax diagrams

Present only in the German *Bedienungsanleitung* (Appendix K, "Syntax-Diagramme"); not in the
English manual.

---

## MODE 0 and MODE 1 in detail

*Taken from the PC-1600 ROMs themselves: the Z-80 banks, the LH-5803's own ROM and the
CE-1600P ROM. The manuals only describe the visible effects. Points marked ✔ were also checked
by running the real ROMs in an emulator.*

### The model: one interpreter, two CPUs

The BASIC interpreter runs on the Z-80 in **both** modes: line editing, the statement loop,
variables, `PRINT`, `GOTO` and file commands. `MODE 1` does **not** pass BASIC over to the
LH-5803 or to a separate PC-1500 interpreter.

The LH-5803 runs a modified copy of the PC-1500 ROM. The Z-80 hands it one statement at a time
whenever a keyword has no Z-80 handler of its own. That happens in **both** modes, for two
groups of keywords:

- **The PC-1500 keywords kept for compatibility.** `XCALL`, `XPOKE`, `XPOKE#`, `XPEEK` and
  `XPEEK#` have exactly the token values of the PC-1500's `CALL`, `POKE`, `POKE#`, `PEEK` and
  `PEEK#`. The PC-1600 only gave them new names. Their code is the PC-1500's own, running on the
  LH-5803.
- **The CE-150 and CE-158 keywords.** If no PC-1600 ROM module (such as the CE-1600P) takes
  them, they run in the peripheral's own ROM on the LH-5803.

What `MODE 1` really switches is everything **around** these calls:

1. the memory the LH-5803 sees while it runs them;
2. which variables PC-1500 code may use;
3. a handful of commands that are refused in one mode or the other;
4. how some native PC-1600 commands lay out the screen and edit lines;
5. how program lines are stored (the tokenizer);
6. the tape format on the CE-1600P.

### Why `XPEEK` / `XPOKE` / `XCALL` behave differently

The command code is the same in both modes. Two things around it change:

**1. The LH-5803's 0000H–3FFFH window.** The LH-5803 has a fixed 64 KB map:

| LH-5803 address | Contents | Z-80 address |
|---|---|---|
| `0000H`–`3FFFH` | one 16 KB module bank (the PC-1500's module area) | whatever bank is in `8000H`–`BFFFH` |
| `4000H`–`7FFFH` | internal RAM (top part = system work area, `7C00H`–`7FFFH` included) | `C000H`–`FFFFH`, bank 0 |
| `8000H`–`BFFFH` | CE-158 / CE-150 ROM | — |
| `C000H`–`FFFFH` | LH-5803 ROM (the adapted PC-1500 ROM) | — |

- **MODE 1:** before each of these commands the LH-5803 switches the bank that holds the
  BASIC program into `0000H`–`3FFFH`. MODE 1 allows only one program area of at most one bank,
  so this works out. The address space then looks like a PC-1500 with that module fitted. An
  `XPEEK`/`XPOKE` address or an `XCALL` target means what it meant on the PC-1500, and PC-1500
  machine code finds the BASIC program and its pointers where it expects them.
- **MODE 0:** nothing is switched. `0000H`–`3FFFH` shows whichever bank the Z-80 last left in
  its `8000H`–`BFFFH` page. With a single plain module that is usually the program module ✔.
  With banked modules (CE-1600M, CE-1601M), a `TITLE`-selected slot or no module, it can be
  another bank, or nothing at all (reads `255`) ✔. Only `4000H`–`7FFFH` is reliable in MODE 0:
  LH-5803 `4000H + n` is Z-80 `C000H + n` (so `XPEEK &4100` = `PEEK &C100`) ✔. For Z-80
  addresses, use the native `PEEK`/`POKE`/`CALL` with `#bank`.

**2. Which variables PC-1500 code may use.** In MODE 0 the variables are in the PC-1600's own
layout, and the PC-1500 code can reach only the fixed variables `A`–`Z` and `A$`–`Z$`. Any
other name (`A1`, `AB`, arrays, …) in a statement that runs on the LH-5803 is refused with
**ERROR 110**. MODE 1 allows all names. ✔ (no module fitted):

| Typed | MODE 0 | MODE 1 |
|---|---|---|
| `XPOKE &4100,B` | ✓ | ✓ |
| `XPOKE &4100,A1` | ERROR 110 | ✓ |
| `XCALL &4100,A1` | ERROR 110 | ✓ |
| `X=XPEEK A1` | ✓: the Z-80 evaluates the argument before handing over | ✓ |

### CE-150 and CE-158 commands

**How a keyword finds its handler.** The PC-1600 looks for a handler in a fixed order:

1. If `OPN` has selected a PC-1500 device, that device's tables are searched first.
2. Next come the PC-1600's built-in commands. Several of these share a token value with a
   PC-1500 peripheral keyword. Each one hands the token on when the statement isn't for it:
   - `SETCOM`, `SETDEV`, `OUTSTAT`, `INSTAT`: without a `"COM…:"` device they pass to the
     CE-158. `SETCOM 1200,8,N,1` (CE-158 form) reaches the CE-158;
     `SETCOM "COM1:",…` stays native.
   - `LPRINT`, `LLIST`: unless output is redirected to a COM port, they pass to the printer:
     a CE-1600P if one is fitted, else the CE-150.
   - `LINE`: the PC-1500 token value that the PC-1600 uses for its *screen* `LINE`. A
     PC-1500 program's plotter line is token `LLINE`.
3. Then the ROM modules, such as the CE-1600P's plotter and cassette code.
4. Last, the CE-150 / CE-158 ROM on the LH-5803. This is where these end up when no CE-1600P
   takes them: `CSIZE`, `GRAPH`, `TEXT`, `SORGN`, `ROTATE`, `GLCURSOR`, `COLOR`, `LLINE`,
   `TAB`, `LCURSOR`, `CONSOLE`, `RMT`, the CE-158's `TERMINAL`, `DTE`, `TRANSMIT`, and the tape
   keywords `CLOAD`, `CSAVE`, `MERGE`, `CHAIN`.

The search order is the same in both modes. **The peripheral's code is the same in both modes,
too.** The differences are:

| | MODE 0 | MODE 1 |
|---|---|---|
| Variables in CE-150/CE-158 statements | `A`–`Z`, `A$`–`Z$` only; else ERROR 110 (`LPRINT A1` fails; use `A=A1:LPRINT A`) | all |
| `CSAVE`, `CLOAD`, `MERGE`, `CHAIN`, `LLIST` on a CE-150 / CE-158 | ERROR 110 (checked by the LH-5803 ROM) | run |
| CE-158 `TERMINAL`, `DTE` | ERROR 110 | run |
| Program memory as the peripheral code sees it | not mapped (see above) | the PC-1500 picture |
| Expressions using PC-1600-only features (`LPRINT TIME$`) | — | fail: PC-1500 BASIC doesn't know them (TRM) |

**Listing on a CE-150 needs the MODE 1 line form.** A program typed in MODE 0 stores the line
numbers after `GOTO`/`GOSUB`/`THEN` in a binary form that the CE-150's `LLIST` can't print (see
the tokenizer below).

**CE-1600P.** The plotter commands (`LPRINT`, `LLINE`, `COLOR`, `CSIZE`, `GRAPH`, …) never test
the MODE, so they work the same in both modes. Only the CE-1600P's **cassette** part follows the
MODE:

| CE-1600P cassette | MODE 0 | MODE 1 |
|---|---|---|
| Tape format | PC-1600 | PC-1500 (bit encoding, sync, 32-byte header) |
| `CLOAD`, `CLOAD?`, `MERGE`, `CHAIN`, `CLOAD M` | ✓ PC-1600 tapes | ✓ PC-1500 tapes |
| `CSAVE`, `CSAVE M`, `PRINT#-1` | ✓ | ERROR 110 |

### Native commands that behave differently

| Command | MODE 0 | MODE 1 |
|---|---|---|
| `MODE` | — | Sets up the PC-1500 character set, the one-line, 26-column screen and the memory layout (below) |
| `PRINT` | 4-line screen, 13-column zones, wrap and scroll | PC-1500 style on the bottom line: `,` splits the line into two halves; long output is cut off |
| `WAIT` default after `RUN` | no pause | wait for **ENTER** (as on the PC-1500) |
| `INPUT A;` (trailing `;`) | allowed, cursor stays after the input | ERROR 1 ✔ |
| `INPUT` prompt, `CONT`, direct `GOTO` | normal 4-line flow | scroll the single line up first |
| `CURSOR` | `CURSOR x,y` on the 4-line screen | `x` = column 0–25 of the one line |
| `GCURSOR` | graphics cursor `(x,y)` | PC-1500 meaning: dot column 0–155 for the next `GPRINT` |
| `GPRINT` | draws at the graphics cursor | PC-1500 style: into the text line, carrying on from the last column |
| `CLS` | cursor home | cursor to the bottom line |
| `FILES` | 3 entries per screen | 1 entry per screen |
| `NEW <address>` | ERROR 25 ✔ | sets the program start the PC-1500 way ✔ |
| `INIT "S1:"` / `"S2:"` | ✓ | ERROR 110 ✔ (format RAM disks in MODE 0) |
| `TITLE "Sx:"` | selects S0/S1/S2 | MODE 1 has picked the area itself; other program slots are hidden (ERROR 101) |
| `RENUM` | ✓ | can't renumber a program in the MODE 1 line form (save as ASCII, reload in MODE 0) |
| Display | PC-1600 character set, 4 × 26 | PC-1500 character set (`&27`, `&5B`, `&5D`, …), bottom line only. The Z-80 also keeps a PC-1500 display-RAM image up to date, which PC-1500 machine code may read |

**Tokenizer.** In MODE 0 a line number after `GOTO`, `GOSUB`, `THEN` and the like is stored in
binary. In MODE 1 it stays as ASCII digits, as on the PC-1500. A line is tokenized when it is
typed or loaded, so the form follows the MODE that was active at that moment, not the MODE the
program later runs in.

**Two-byte (kanji) character handling** is off in MODE 1. This matters only for the Japanese
character modes.

### Entering and leaving MODE 1

- **When `MODE 1` is refused (ERROR 110):** the ROM refuses only when the main area S0 spans
  more than one module bank, e.g. a CE-1600M/CE-1601M folded into S0. Contrary to the
  manual, it does **not** require a module to be fitted. Without one, the program uses
  internal RAM only. See Appendix H for the configurations Sharp lists.
- **Which program area MODE 1 uses:** it ignores the previous `TITLE` and chooses for itself:
  - a one-bank **program module in S1**, else one in **S2**, else **S0**;
  - if S0 already contains one module bank, S0 is chosen when that bank fills the whole
    16 KB window.

  The slots it doesn't choose are hidden until `MODE 0`. A program module that spans several
  banks is hidden as well; it doesn't block MODE 1.
- **Back to `MODE 0`:** the slot table is rebuilt and `TITLE` goes back to S0.
- **MODE 1 is kept** across power off/on if the memory configuration still allows it. A
  start-up check message (`NEW0? :CHECK …`) switches back to MODE 0.
- **ERROR 110 has two meanings:** "cannot set MODE 1", and "this PC-1500-peripheral command
  needs MODE 1".

### Forcing MODE 1 with `POKE &F1BC,PEEK(&F1BC) OR 64`

Hints from the time suggest this trick when `MODE 1` is refused, typically with a CE-1600M
added to the main memory, to load PC-1500 programs larger than 12 KB from tape. The POKE
sets bit 6 of `F1BCH`, which is the MODE 1 flag. Most of what MODE 1 does just tests that
flag. But the `MODE` command also does three things that the POKE skips:

- It sets up the memory layout: one program area of at most one bank, other slots hidden,
  and the PC-1500 program pointers at `F860H`–`F863H` filled in.
- It switches to the PC-1500 character set.
- It sets the screen to 26 columns.

The result is a partial MODE 1:

| | Under the POKE |
|---|---|
| BASIC itself (Z-80) | Runs a program across all module banks. This is what the trick is for ✔ |
| `PRINT`, `INPUT`, `CURSOR`, `GPRINT`, `WAIT` default, tokenizer, … | MODE 1 behaviour, as in the tables above ✔ (`RUN` waits for ENTER after `PRINT`) |
| CE-1600P cassette | PC-1500 tape format. The loader is Z-80 code and fills the whole multi-bank area, which is why large PC-1500 tapes can load (not tested) |
| `INIT "Sx:"` | refused (ERROR 110) ✔ |
| Characters | **PC-1600 set**: `CHR$ &5B` shows `[`, not `√` ✔ |
| Variables in PC-1500 code | all names accepted (the MODE 0 restriction is gone). Whether the LH-5803 actually reaches variables stored outside the first bank is not tested |
| LH-5803 `0000H`–`3FFFH` | Only the **first** bank of the program is switched in ✔. The rest of the program can't be reached with `XPEEK`/`XPOKE`/`XCALL` |
| PC-1500 program pointers | In PC-1500 form they make no sense for a program over two banks: with a 17 KB program, start `00C5H` and end `060FH` ✔. Any LH-5803 code that walks the program (CE-150 `LLIST`, `CSAVE`, `CLOAD`, `MERGE`, PC-1500 machine code) will see the wrong program, or overwrite memory |
| `MODE 1` typed again | silently does nothing: the flag already says MODE 1 ✔ |
| `MODE 0` | returns cleanly; the full memory layout is restored ✔ |
| Power off/on | The start-up code tries to set up MODE 1 properly, fails and clears the flag. The machine comes back in MODE 0 |

Safe use under the POKE: load and run the program with BASIC and the CE-1600P, and avoid
CE-150/CE-158 tape and listing commands and PC-1500 machine code that touches the program.

### What is the same in both modes

- Arithmetic, functions and comparisons. Some of these run on the LH-5803 in either mode.
- `MEM`, variables and arrays (on the Z-80 side).
- `LOAD`/`SAVE`/`BLOAD`/`BSAVE`, RAM-disk and floppy files, and the PC-1600 serial commands
  with a `"COM…:"` device.
- The CE-1600P plotter.
- `TIME = 0` is an error in both modes.

---

## Internal data representation

*From the* PC-1600 Systemhandbuch *(Holtkötter), chapter 4 — firmware detail, not in the Operation
Manual. Addresses are as seen from the SC-7852 (Z-80).*

### Arithmetic registers

IOCS arithmetic routines take their operands in seven 8-byte registers in the BASIC work area
(operand normally in **X**):

| Reg | Address | Reg | Address |
|-----|---------|-----|---------|
| X | `FA00H`–`FA07H` | U | `FA18H`–`FA1FH` |
| Z | `FA08H`–`FA0FH` | V | `FA20H`–`FA27H` |
| Y | `FA10H`–`FA17H` | W | `FA28H`–`FA2FH` |
| | | S | `FA30H`–`FA37H` |

### Numeric value — decimal (BCD) form

8 bytes: exponent, mantissa sign, then the BCD mantissa. Covers ±9.999999999×10⁹⁹.

- **Byte 0 — exponent:** a signed binary byte (negative as two's complement).
- **Byte 1 — mantissa sign:** `00H` = `+`, `80H` = `-`.
- **Bytes 2–6 — mantissa:** 10 BCD digits. Byte 7 = `00H`.

`123` (= 1.23×10²) in X: `02 00 12 30 00 00 00 00`.
`-0.0123` (= -1.23×10⁻²) in Y: `FE 80 12 30 00 00 00 00`.

### Numeric value — binary form

8 bytes (5 unused); covers −32768…32767. Bytes 4 = `B2H` tag, bytes 5–6 = the 16-bit value
(high, low), rest ignored.

`123` (`007BH`) in X: `00 00 00 00 B2 00 7B 00`.
`-123` (`FF85H`) in Y: `00 00 00 00 B2 FF 85 00`.

### String descriptor

8 bytes (4 unused): a length byte (`01H`–`50H`, i.e. max 80 chars), then the start address as
`ADDH ADDL` — **with the MSB of `ADDH` inverted** (start `FB10H` is stored as `7B 10`). The
descriptor points at where the actual character data lives.

---

## See also
- [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md)
- [PC-1600 Command Index](PC-1600-Command-Index.md) · [PC-1600 Error Codes](PC-1600-Error-Codes.md)
