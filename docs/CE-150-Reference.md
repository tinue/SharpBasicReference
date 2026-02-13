# Sharp CE-150 4-Color Plotter/Printer Reference

## Overview

The **Sharp CE-150** is a compact 4-color plotter and printer peripheral for the Sharp PC-1500 pocket computer. It extends the PC-1500's capabilities with graphics output, text printing, and cassette tape storage functions.

### Key Features
- **4-color plotting**: Black, Red, Blue, Green pens
- **Text mode**: Character printing with variable sizes
- **Graphics mode**: X-Y plotter with programmable control
- **Paper feed**: Mechanical paper advance system
- **Cassette interface**: Enhanced tape operations with remote control
- **Compact design**: Portable unit matching PC-1500 form factor

### Hardware Specifications
- **Resolution**: Approximately 0.2mm per step
- **Paper width**: 40mm plain paper
- **Pen colors**: 0=Black, 1=Red, 2=Blue, 3=Green (ink pens)
- **Character sizes**: 4 sizes (0-3)
- **Rotation angles**: 4 orientations (0-3, 90° increments)
- **Ports**: B00A-B00F (ME1 expansion bus)

## Command Summary

### CE-150 Specific Commands (Token Range 0xE680-0xE7A9)

| Command | Token | Handler | Description |
|---------|-------|---------|-------------|
| CSIZE | 0xE680 | $B180 | Set character size (0-3) |
| GRAPH | 0xE681 | $ACD3 | Switch to graphics mode |
| GLCURSOR | 0xE682 | $B191 | Set graphics cursor position |
| LCURSOR | 0xE683 | - | Set line cursor |
| SORGN | 0xE684 | $B153 | Set origin point |
| ROTATE | 0xE685 | $B15A | Set rotation angle (0-3) |
| TEXT | 0xE686 | $ACA6 | Switch to text mode |
| RMT | 0xE7A9 | $BEF9 | Remote cassette motor control |

### Enhanced Commands (0xF0B0-0xF0BC)

These commands are part of the PC-1500 token space but specifically enhanced for CE-150:

| Command | Token | Handler | Description |
|---------|-------|---------|-------------|
| FEED | 0xF0B0 | $82E1 | Paper feed |
| CHAIN | 0xF0B2 | - | Load and execute from tape |
| COLOR | 0xF0B5 | $B16A | Select pen color (0-3) |
| LF | 0xF0B6 | $B1B4 | Line feed |
| LINE | 0xF0B7 | $B222 | Draw absolute line |
| LLIST | 0xF0B8 | $B754 | List program to printer |
| LPRINT | 0xF0B9 | $B2EC | Print to plotter |
| RLINE | 0xF0BA | $B224 | Draw relative line |
| TAB | 0xF0BB | - | Tab to column |
| TEST | 0xF0BC | - | Graphics test function |

---

## Detailed Command Reference

### Graphics Mode Commands

#### **GRAPH**
- **Token**: 0xE681
- **Abbreviation**: GRAP
- **Handler**: $ACD3
- **Type**: Statement
- **Description**: Switch CE-150 to graphics mode. In graphics mode, all output is directed to the plotter with coordinates interpreted as X-Y positions.
- **Syntax**: `GRAPH`
- **Effects**:
  - Enables X-Y coordinate plotting
  - Disables text character mode
  - Initializes plotter motor system
  - Sets default origin at (0,0)
- **Related**: TEXT, GLCURSOR, SORGN

#### **TEXT**
- **Token**: 0xE686
- **Abbreviation**: TEX
- **Handler**: $ACA6
- **Type**: Statement
- **Description**: Switch CE-150 to text mode for character printing.
- **Syntax**: `TEXT`
- **Effects**:
  - Enables character printing
  - Disables X-Y coordinate mode
  - Resets cursor to text position
- **Related**: GRAPH, CSIZE, ROTATE

#### **GLCURSOR**
- **Token**: 0xE682
- **Abbreviation**: GL
- **Handler**: $B191
- **Type**: Statement
- **Description**: Set graphics cursor position in plotter coordinates.
- **Syntax**: `GLCURSOR x, y`
- **Parameters**:
  - `x`: X coordinate (integer)
  - `y`: Y coordinate (integer)
- **Notes**: Coordinates are affected by SORGN origin setting and ROTATE angle
- **Related**: GRAPH, LINE, RLINE

#### **LCURSOR**
- **Token**: 0xE683
- **Abbreviation**: LCU
- **Handler**: -
- **Type**: Statement
- **Description**: Set line cursor for text mode printing.
- **Syntax**: `LCURSOR`
- **Related**: TEXT, GLCURSOR

#### **LINE**
- **Token**: 0xF0B7
- **Abbreviation**: LIN
- **Handler**: $B222
- **Type**: Statement
- **Description**: Draw line from current position to absolute coordinates.
- **Syntax**: `LINE x, y`
- **Parameters**:
  - `x`: Target X coordinate
  - `y`: Target Y coordinate
- **Effects**:
  - Draws line using current COLOR pen
  - Updates cursor position to endpoint
  - Respects rotation and origin settings
- **Related**: RLINE, GLCURSOR, COLOR

#### **RLINE**
- **Token**: 0xF0BA
- **Abbreviation**: RL
- **Handler**: $B224
- **Type**: Statement
- **Description**: Draw line relative to current cursor position.
- **Syntax**: `RLINE dx, dy`
- **Parameters**:
  - `dx`: X offset from current position
  - `dy`: Y offset from current position
- **Related**: LINE, GLCURSOR, COLOR

#### **SORGN**
- **Token**: 0xE684
- **Abbreviation**: SO
- **Handler**: $B153
- **Type**: Statement
- **Description**: Set origin point for coordinate system. All subsequent coordinates are relative to this origin.
- **Syntax**: `SORGN x, y`
- **Parameters**:
  - `x`: New origin X coordinate
  - `y`: New origin Y coordinate
- **Default**: (0, 0) at power-on
- **Related**: GLCURSOR, LINE, RLINE

### Display Control Commands

#### **COLOR**
- **Token**: 0xF0B5
- **Abbreviation**: COL
- **Handler**: $B16A
- **Type**: Statement
- **Description**: Select pen color for plotting and drawing.
- **Syntax**: `COLOR pen`
- **Parameters**:
  - `pen`: Pen number (0-3)
    - 0 = Black
    - 1 = Red
    - 2 = Blue
    - 3 = Green
- **Default**: 0 (Black)
- **Related**: LINE, RLINE, LPRINT

#### **CSIZE**
- **Token**: 0xE680
- **Abbreviation**: CSI
- **Handler**: $B180
- **Type**: Statement
- **Description**: Set character size for text mode printing.
- **Syntax**: `CSIZE size`
- **Parameters**:
  - `size`: Size code (0-3)
    - 0 = Normal
    - 1 = Double width
    - 2 = Double height
    - 3 = Double width and height
- **Default**: 0 (Normal)
- **Related**: TEXT, ROTATE, LPRINT

#### **ROTATE**
- **Token**: 0xE685
- **Abbreviation**: RO
- **Handler**: $B15A
- **Type**: Statement
- **Description**: Set rotation angle for text and graphics output.
- **Syntax**: `ROTATE angle`
- **Parameters**:
  - `angle`: Rotation code (0-3)
    - 0 = 0° (normal)
    - 1 = 90° clockwise
    - 2 = 180°
    - 3 = 270° clockwise (90° counter-clockwise)
- **Default**: 0
- **Applies to**: Both text characters and graphics drawing
- **Related**: TEXT, GRAPH, CSIZE

### Printing Commands

#### **LPRINT**
- **Token**: 0xF0B9
- **Abbreviation**: LP
- **Handler**: $B2EC (CE-150), $82E4 (CE-158)
- **Type**: Statement
- **Description**: Print output to CE-150 plotter/printer.
- **Syntax**: `LPRINT [expr1][;|,][expr2]...`
- **Behavior**:
  - Same format as PRINT command
  - Output directed to CE-150 instead of display
  - Respects CSIZE, ROTATE, COLOR settings
  - Semicolon (;) suppresses line feed
  - Comma (,) advances to next print zone
- **Related**: PRINT, LLIST, LF

#### **LLIST**
- **Token**: 0xF0B8
- **Abbreviation**: LL
- **Handler**: $B754 (CE-150), $82E5 (CE-158)
- **Type**: Statement
- **Description**: List BASIC program to CE-150 printer.
- **Syntax**: `LLIST [start[-end]]`
- **Parameters**:
  - `start`: First line number to list (optional)
  - `end`: Last line number to list (optional)
- **Examples**:
  - `LLIST` - List entire program
  - `LLIST 100` - List from line 100 to end
  - `LLIST 100-200` - List lines 100 through 200
- **Related**: LIST, LPRINT

#### **LF**
- **Token**: 0xF0B6
- **Abbreviation**: -
- **Handler**: $B1B4
- **Type**: Statement
- **Description**: Generate line feed on CE-150 printer.
- **Syntax**: `LF [n]`
- **Parameters**:
  - `n`: Number of line feeds (optional, default=1)
- **Related**: FEED, LPRINT

#### **FEED**
- **Token**: 0xF0B0
- **Abbreviation**: -
- **Handler**: $82E1 (CE-158 handler)
- **Type**: Statement
- **Description**: Advance paper in CE-150 printer.
- **Syntax**: `FEED`
- **Related**: LF

### Cassette Control

#### **RMT**
- **Token**: 0xE7A9
- **Abbreviation**: RM
- **Handler**: $BEF9
- **Type**: Statement
- **Description**: Remote control of cassette tape motor. The CE-150 provides automatic motor control during CLOAD/CSAVE operations.
- **Syntax**: `RMT flag`
- **Parameters**:
  - `flag`: Motor control
    - 0 = Motor OFF
    - 1 = Motor ON
- **Note**: Normally controlled automatically by tape I/O commands
- **Related**: CLOAD, CSAVE

#### **CHAIN**
- **Token**: 0xF0B2
- **Abbreviation**: CHA
- **Handler**: - (CE-150)
- **Type**: Statement
- **Description**: Load BASIC program from tape and immediately execute it.
- **Syntax**: `CHAIN "filename"`
- **Effects**:
  - Loads program from cassette
  - Clears current program
  - Begins execution of loaded program
  - Variables are preserved
- **Related**: CLOAD, RUN, MERGE

---

## Memory Map

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
| 0x79F4 | PRNT_CSIZE | 1 | CSIZE setting (0-3) |
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

---

## System Functions (ROM Addresses)

### Character and Graphics Functions

| Address | Function | Description |
|---------|----------|-------------|
| $A000-$A28A | PRNT_VEC | Character vectors (651 bytes) |
| $A28B | MGP1_150 | Start of MGP 1 program block |
| $A519 | COLDES | Color designation routine |
| $A769 | MOTOFF | Printer motor OFF |
| $A781 | PRINT_150 | Print ASCII character (no LF) |
| $A8DD | MOTDRV | Motor drive - move pen |
| $A951 | LFEED | Single line feed |
| $AA04 | NLFEED | Multiple line feeds (n times) |
| $AAE3 | PENUPDOWN | Pen up/down control |
| $ABEF | GRPHPREP | Switch from text to graphics mode |
| $ACA6 | TEXT | TEXT mode handler |
| $ACD3 | GRAPH | GRAPH mode handler / Text mode preparation |
| $ACDЗ | TEXTPREP | Text mode preparation routine |

### Plotting and Graphics

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

### Cassette Interface Functions

| Address | Function | Description |
|---------|----------|-------------|
| $B888 | SBRA4 | Subroutine A4 - CMT block 2 start |
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

---

## Programming Examples

### Basic Graphics

```basic
10 REM Circle drawing example
20 GRAPH
30 SORGN 500, 500
40 COLOR 0
50 FOR A=0 TO 360 STEP 10
60 X=COS(A)*200
70 Y=SIN(A)*200
80 LINE X, Y
90 NEXT A
100 TEXT
```

### Multi-Color Plot

```basic
10 REM Four-color spiral
20 GRAPH
30 SORGN 400, 400
40 FOR I=0 TO 720 STEP 5
50 C=I/180
60 COLOR C AND 3
70 R=I/4
80 X=COS(I)*R
90 Y=SIN(I)*R
100 LINE X, Y
110 NEXT I
120 TEXT
```

### Text Printing

```basic
10 REM Print formatted report
20 TEXT
30 CSIZE 3
40 LPRINT "SALES REPORT"
50 LF 2
60 CSIZE 0
70 LPRINT "Item", "Qty", "Price"
80 LF
90 LPRINT "Widget", 100, 5.99
100 LPRINT "Gadget", 50, 12.50
110 LF 3
```

### Rotated Text

```basic
10 REM Rotated text demo
20 TEXT
30 FOR R=0 TO 3
40 ROTATE R
50 CSIZE R
60 LPRINT "ANGLE ";R*90
70 LF 3
80 NEXT R
90 ROTATE 0
100 CSIZE 0
```

---

## Hardware Notes

### Pen Mechanism
- The CE-150 uses a 4-pen carousel mechanism
- Pen changes are automatic when COLOR command is issued
- Physical pen switching takes approximately 1-2 seconds
- Pen wear affects line quality - replace pens as needed

### Motor Control
- Stepper motors control X and Y pen movement
- Minimum step size approximately 0.2mm
- Motor hold time prevents skipping on rapid movements
- Power consumption increases during motor operation

### Paper Specifications
- Type: Plain paper, 40mm width (for use with ink pens)
- Roll length: Approximately 8 meters standard
- Paper advance: Controlled by LF and FEED commands
- Paper out detection: Monitor for error conditions

### Cassette Interface
- Enhanced CMT control through CE-150
- Remote motor control via RMT command
- Improved signal quality for reliable tape operations
- Compatible with standard cassette tapes

---

## Troubleshooting

### Common Issues

**Pen Not Drawing**
- Check pen down state (CURR_PEN register at 0x79EC)
- Verify correct COLOR selection (0-3)
- Ensure pen carousel has moved to selected pen
- Check pen ink level

**Motor Errors**
- Verify power supply voltage
- Check for mechanical obstructions
- Allow motor hold time between rapid movements
- Reset with TEXT/GRAPH mode switch if stalled

**Print Quality Issues**
- Adjust paper pressure
- Clean pen tips
- Verify paper type (plain paper for ink pens)
- Check ink levels in pens
- Check CSIZE and ROTATE settings

**Coordinate Problems**
- Reset origin with SORGN 0,0
- Check ROTATE setting (should be 0 for normal)
- Verify coordinate values are within plotter range
- Consider scissoring limits in registers

---

## Technical Specifications Summary

| Specification | Value |
|---------------|-------|
| Plotting Resolution | ~0.2mm per step |
| Paper Width | 40mm plain paper |
| Pen Colors | 4 (Black, Red, Blue, Green - ink pens) |
| Character Sizes | 4 (0-3) |
| Rotation Angles | 4 (0°, 90°, 180°, 270°) |
| I/O Port Range | 0xB00A-0xB00F |
| Register Range | 0x79E0-0x79F9 |
| ROM Function Range | 0xA000-0xBF43 |
| Power Consumption | 4×AA batteries or AC adapter |
| Dimensions | ~150mm × 95mm × 35mm |

---

## See Also

- [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md) - Core BASIC commands
- [CE-158 Communications Reference](CE-158-Reference.md) - Serial interface
- [Token Mapping Analysis](analysis/Token-Mapping-Analysis.md) - Token structure

---

*Document Version 1.0 - Generated from ROM disassemblies and library definitions*
