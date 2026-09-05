# Sharp CE-150 Printer/Plotter/Cassette Interface Reference

## Overview

The **Sharp CE-150** is a Printer/Cassette Interface for the Sharp PC-1500 pocket computer. It provides a 4-color pen plotter/printer and a cassette tape interface for saving and loading programs and data.

**All CE-150 commands require the CE-150 to be physically connected to the PC-1500.** They will not work on the standalone PC-1500.

→ [Command Index](Command-Index.md) · [Error Code Reference](Error-Codes.md)

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

## Printer Modes

The CE-150 is always in one of two modes:

- **TEXT mode**: For printing text and numbers. The paper is divided into columns based on the current CSIZE setting. Use `LPRINT`, `LCURSOR`, `TAB`, and `LF`.
- **GRAPH mode**: For X-Y plotting and drawing. Use `GLCURSOR`, `LINE`, `RLINE`, and `SORGN`.

Switch modes with `TEXT` (enters TEXT mode) or `GRAPH` (enters GRAPH mode, moves pen to far left).

Some commands cause automatic mode switching — for example, `LPRINT` forces TEXT mode.

---

## Setting Up the CE-150

1. Always turn the PC-1500 **OFF** before connecting or disconnecting the CE-150.
2. After connecting, turn ON. If **ERROR 80** or **ERROR 78** appears, the CE-150 battery needs charging.
3. Load four pens into the pen carousel. Operating with missing pens may cause color errors.
4. Use the `TEST` command to verify which physical pen corresponds to each color number.

---

## Command Reference

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

## Coordinate System for GRAPH Mode

- The origin (0,0) is set by `SORGN` at the current pen position.
- X axis is horizontal (positive = right).
- Y axis is vertical (positive = up).
- Valid range: -2048 to +2047 for both X and Y (`ERROR 70` outside this; it is a coordinate range check, not the paper edge).
- Physical drawable area: X spans **0 to 216 plotter units ≈ 42.75mm** across the 56mm tape. The scale is **0.198mm per unit** (= 190mm / 960 units, taken from the CE-1600P, whose wider carriage gives a more accurate ruler reading; a crude ruler check of the CE-150 itself gave ~43mm, consistent). Y is unbounded — the paper is continuous tape, not a page — the only limit being the ~10.24cm maximum backward feed in TEXT mode (`ERROR 71`).
- If the pen is commanded outside the drawable area, it stops at the edge. The internal counters continue tracking the commanded position, so the pen will resume drawing correctly once it returns within bounds.

---

## Using Cassette Tape

Follow these steps for reliable saves:

1. Turn the "remote" switch on the CE-150 **OFF** before starting.
2. Insert the tape and advance past the leader to a blank section.
3. Set the volume to about 3/4 level (use automatic volume if available).
4. Turn the remote switch back **ON**.
5. Press **RECORD** and **PLAY** simultaneously on the tape recorder.
6. Then type the `CSAVE` command on the PC-1500.
7. After saving, always verify with `CLOAD?` before relying on the save.

---

## Error Codes

See the [Error Code Reference](Error-Codes.md) for all CE-150 error codes (11, 19, 40–44, 70–74, 76, 78–80).

---

## Example Programs

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

## See Also

- [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md) — Core BASIC commands
- [CE-158 Communications Reference](CE-158-Reference.md) — Serial interface
- [Error Code Reference](Error-Codes.md) — All error codes

---

## Internal Technical Reference

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
