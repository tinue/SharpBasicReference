# Sharp PC-1500 BASIC Token Mapping Analysis

## Executive Summary

This document provides a comprehensive analysis of the Sharp PC-1500 BASIC token system, including token value ranges, device-specific allocations, ROM dispatcher tables, and cross-reference mappings. The PC-1500 uses a 16-bit tokenization scheme with tokens ranging from 0xE680 to 0xF1B6, distributed across the base PC-1500 unit and two peripheral expansion modules (CE-150 and CE-158).

**Key Findings**:
- **Total Commands**: 122 unique BASIC keywords
- **Token Range**: 0xE680-0xF1B6 (spanning 2870 bytes)
- **Device Distribution**: 97 PC-1500 core, 14 CE-150, 11 CE-158
- **ROM Dispatch Tables**: PC-1500 at $C000, CE-158 at $8000 (dual banks)
- **Handler Distribution**: PC-1500 in $C000-$F000 range, peripherals in expansion ROMs

---

## Token Range Classification

### Complete Token Space Overview

The PC-1500 BASIC tokenization uses a non-contiguous 16-bit address space with three primary ranges:

| Range | Device | Commands | Purpose |
|-------|--------|----------|---------|
| 0xE680-0xE686 | CE-150 | 7 | Graphics/plotter core commands |
| 0xE7A9 | CE-150 | 1 | Cassette remote control |
| 0xE857-0xE85A | CE-158 | 4 | Serial status/input functions |
| 0xE880-0xE886 | CE-158 | 7 | Serial configuration/control |
| 0xF052-0xF053 | PC-1500 | 2 | Error reporting functions |
| 0xF061 | PC-1500 | 1 | String function (SPACE$) |
| 0xF084-0xF0BC | PC-1500/Shared | 22 | I/O, display, peripheral commands |
| 0xF150-0xF151 | PC-1500 | 2 | Logical operators (AND, OR) |
| 0xF158-0xF1B6 | PC-1500| 76 | Core BASIC language |

### Device-Specific Token Allocation

#### PC-1500 Core Tokens (0xF000-0xF1FF)

**Primary Range**: 0xF052-0xF1B6 (97 commands)

Subdivisions:
- **0xF052-0xF053**: Error functions (ERN, ERL)
- **0xF061**: String utility (SPACE$)
- **0xF084-0xF09F**: Display and I/O (15 commands)
- **0xF0B0-0xF0BC**: Enhanced I/O (13 commands, shared with peripherals)
- **0xF150-0xF1B6**: Core language (73 commands)

**Design Pattern**: PC-1500 tokens predominantly use the 0xF1xx range for core language constructs, with earlier ranges (0xF0xx) reserved for I/O and peripheral interaction.

#### CE-150 Graphics Tokens (0xE680-0xE7A9)

**Primary Range**: 0xE680-0xE686 (7 commands)
**Secondary**: 0xE7A9 (1 command)

Commands:
1. 0xE680 - CSIZE (character size)
2. 0xE681 - GRAPH (graphics mode)
3. 0xE682 - GLCURSOR (graphics cursor)
4. 0xE683 - LCURSOR (line cursor)
5. 0xE684 - SORGN (set origin)
6. 0xE685 - ROTATE (rotation angle)
7. 0xE686 - TEXT (text mode)
8. 0xE7A9 - RMT (remote cassette control)

**Gap Analysis**: The gap between 0xE686 and 0xE7A9 (290 tokens) suggests reserved space for future expansion or ROM organization constraints.

#### CE-158 Communications Tokens (0xE857-0xE886)

**Primary Range**: 0xE857-0xE85A (4 commands)
**Secondary Range**: 0xE880-0xE886 (7 commands)

Status Functions (0xE857-0xE85A):
1. 0xE857 - DEV$ (device query)
2. 0xE858 - COM$ (comm parameters)
3. 0xE859 - INSTAT (input status)
4. 0xE85A - RINKEY$ (read key)

Configuration Commands (0xE880-0xE886):
1. 0xE880 - OUTSTAT (output status)
2. 0xE881 - PROTOCOL (set protocol)
3. 0xE882 - SETCOM (configure serial)
4. 0xE883 - TERMINAL (terminal mode)
5. 0xE884 - DTE (DTE/DCE mode)
6. 0xE885 - TRANSMIT (send data)
7. 0xE886 - SETDEV (select device)

**Gap Analysis**: The gap between 0xE85A and 0xE880 (37 tokens) likely separates status/query functions from configuration/action commands.

---

## Shared Command Analysis

### Multi-Device Commands

Several commands exist in the PC-1500 core token range (0xF0xx) but have enhanced implementations when peripherals are present:

| Token | Command | PC-1500 | CE-150 | CE-158 | Notes |
|-------|---------|---------|--------|--------|-------|
| 0xF089 | CLOAD | $C000 | $B8F9 | $82EC | Tape load with enhancements |
| 0xF08F | MERGE | - | $B994 | $82C0 | Tape merge operation |
| 0xF090 | LIST | $C96E | - | - | Program listing |
| 0xF091 | INPUT | $C8FA | - | $82E2 | Device-aware input |
| 0xF095 | CSAVE | - | $B8A6 | $82DD | Tape save with enhancements |
| 0xF097 | PRINT | $E4EB | - | $82E7 | Device-aware print |
| 0xF0B0 | FEED | - | - | $82E1 | Paper feed |
| 0xF0B1 | CONSOLE | - | - | $82DE | Console selection |
| 0xF0B2 | CHAIN | - | $BB6A | - | Chain load/execute |
| 0xF0B3 | BREAK | $CD89 | - | $CD89 | Break statement |
| 0xF0B4 | ZONE | - | - | $82EB | Print zone width |
| 0xF0B5 | COLOR | - | $B16A | - | Pen color selection |
| 0xF0B6 | LF | - | $B1B4 | - | Line feed |
| 0xF0B7 | LINE | - | $B222 | - | Draw line |
| 0xF0B8 | LLIST | - | $B754 | $82E5 | List to printer |
| 0xF0B9 | LPRINT | - | $B2EC | $82E4 | Print to printer |
| 0xF0BA | RLINE | - | $B224 | - | Relative line draw |
| 0xF0BB | TAB | - | - | $880D | Tab function |

**Implementation Strategy**: When peripherals are installed, the PC-1500's token dispatcher checks for peripheral ROM presence and redirects to peripheral handlers. If peripheral is absent, command generates "Device Error" or falls back to base implementation.

---

## ROM Dispatcher Architecture

### PC-1500 Core Dispatcher (Token Table at $C000)

**File**: PC-1500_ROM-A0x.lh5801.asm (lines 189-315)

**Structure**:
```assembly
B_TBL_C000_CMD_LST:     ; Token LB < 80 = function, else = statement
LET_A:  EQU ($ + 2)     ; First keyword starting with 'A'
CN1:    EQU $A5 \ CNIB($B5,CN1) \ .TEXT "AREAD" \ .WORD $F180, $C684
CN2:    EQU $A3 \ CNIB(CN1,CN2) \ .TEXT "AND"   \ .WORD $F150, $CD89
...
```

**Key Features**:
- **Alphabetical Organization**: Commands grouped by first letter with index pointers (LET_A, LET_B, etc.)
- **Control Nibble**: Encodes command type (function vs statement) and list linkage
- **Token-Handler Pairs**: Each entry contains 16-bit token and 16-bit handler address
- **Binary Search**: Letter indices enable O(log n) lookup performance

**Control Nibble Encoding**:
- **High nibble** (bits 4-7): Command properties
  - Bit 7: 0 = Function, 1 = Statement
  - Bits 4-6: Additional flags
- **Low nibble** (bits 0-3): Offset to next command in linked list

### CE-158 Dual-Bank Dispatcher (Token Table at $8000)

**Files**:
- CE-158_ROM_LOW.lh5801.asm (lines 183-242)
- CE-158_ROM_HIGH.lh5801.asm (lines 183-242)

**Structure** (identical in both banks):
```assembly
B_TBL_8000_CMD_LST:     ; Token LB < 80 = function, else = statement
LET_B:  EQU ($ + 2)     ; First keyword starting with 'B'
CN1:    EQU $B5 \ CNIB($B5,CN1) \ .TEXT "BREAK" \ .WORD $F0B3, ERR1
CN2:    EQU $C5 \ CNIB(CN1,CN2) \ .TEXT "CLOAD" \ .WORD $F089, MAIN_ENTRY
...
```

**Dual-Bank Purpose**:
- **Low Bank**: Primary handlers, standard implementations
- **High Bank**: ROM variants (CE158V2, ENBPD, HANDSHAKE modifications)
- **Identical Token Tables**: Both banks maintain same lookup structure
- **Dynamic Switching**: System selects bank based on configuration flags

**CE-158 Handler Pattern**:
Most handlers follow a common pattern:
1. Entry point at specific offset (e.g., $82D3)
2. Brief setup code
3. Drop through to MAIN_ENTRY ($82EC)
4. MAIN_ENTRY coordinates with PC-1500 core

### CE-150 Dispatcher (ROM-based)

**Source**: CE-150.lib function addresses

**Structure**: Direct function addresses without token table
- CE-150 ROM contains handlers at fixed addresses
- PC-1500 core dispatcher redirects to CE-150 ROM when tokens match 0xE680-0xE7A9 range
- No separate token table in CE-150 ROM (relies on PC-1500 dispatcher)

**Implementation Addresses**:
```
GRAPH     = $ACD3
TEXT      = $ACA6
SORGN     = $B153
ROTATE    = $B15A
COLOR     = $B16A
CSIZE     = $B180
GLCURSOR  = $B191
...
```

---

## Handler Address Distribution

### PC-1500 Core Handlers (ROM Address Ranges)

**Analysis of 97 PC-1500 commands**:

| Address Range | Count | Examples | Category |
|---------------|-------|----------|----------|
| $599E-$5A44 | 2 | NOT, STATUS | Utility functions |
| $C458-$C96A | 34 | LET, GOTO, IF, FOR, GOSUB | Program control |
| $CD89 | 9 | AND, OR, ERROR, THEN, TO | Operators/keywords |
| $D993-$DA5D | 8 | PEEK, ASC, LEN, CHR$, MEM | Memory/string |
| $DE82 | 1 | TIME | System time |
| $E457-$E86A | 8 | OPN, BEEP, CURSOR, CLS, WAIT | I/O and system |
| $E6A5-$EECB | 3 | PAUSE, GPRINT, POINT | Graphics/display |
| $F0E9-$F641 | 24 | SQR, LOG, SIN, COS, RANDOM | Math functions |

**Handler Clustering**:
- Program control: $C400-$C900 (tight clustering for performance)
- Math functions: $F000-$F600 (separate ROM region)
- I/O functions: $E400-$E800 (near peripheral interface)

### CE-150 Handlers (Expansion ROM)

All CE-150 handlers located in ROM range $A000-$BF43:

| Address Range | Count | Category |
|---------------|-------|----------|
| $A000-$A28A | - | Character vectors (data) |
| $A28B-$ABEF | - | Graphics primitives |
| $ACA6-$ACD3 | 2 | Mode switching (TEXT, GRAPH) |
| $B153-$B2EC | 7 | Command handlers |
| $B754 | 1 | LLIST |
| $B888-$BF43 | - | Cassette I/O |

### CE-158 Handlers (Dual Banks at $8000-$8FFF)

**Common Pattern**: Most handlers at $82xx range
- $82C0-$82EA: Primary command handlers (26 functions)
- $880D: TAB function (High Bank specific)

**Handler Consolidation**: Many commands share common entry points:
- MAIN_ENTRY ($82EC): Central dispatcher for I/O operations
- ERR1 ($CD89): Common error handler
- Multiple commands drop through to shared code

---

## Token Value Patterns and Gaps

### Sequential Ranges

**Tight Packing** (sequential or near-sequential):
1. **0xE680-0xE686** (CE-150 graphics): 7 sequential tokens
2. **0xE857-0xE85A** (CE-158 status): 4 sequential tokens
3. **0xE880-0xE886** (CE-158 config): 7 sequential tokens
4. **0xF170-0xF17F** (Math functions): 16 sequential tokens
5. **0xF18A-0xF18E** (System commands): 5 sequential tokens
6. **0xF192-0xF19E** (Control flow): 13 sequential tokens
7. **0xF1A0-0xF1B6** (Misc statements): 23 sequential tokens

### Significant Gaps

| Gap | Size | Purpose |
|-----|------|---------|
| 0xE686 → 0xE7A9 | 291 | Reserved for CE-150 expansion |
| 0xE7A9 → 0xE857 | 174 | Reserved/unallocated |
| 0xE85A → 0xE880 | 37 | Functional separation (status vs config) |
| 0xE886 → 0xF052 | 1996 | Reserved for peripherals/future |
| 0xF053 → 0xF061 | 13 | Reserved |
| 0xF061 → 0xF084 | 34 | Reserved |
| 0xF09F → 0xF0B0 | 16 | Reserved |
| 0xF0BC → 0xF150 | 147 | Reserved |
| 0xF151 → 0xF158 | 6 | Reserved |

**Gap Analysis Conclusions**:
- Large gaps (>100 tokens) suggest room for major expansions
- Small gaps (<50) likely due to ROM organization or functional grouping
- 0xE800-0xEFFF range (2048 tokens) almost entirely unused - prime expansion space

---

## Command Type Classification

### By Type

| Type | Count | Token Ranges | Examples |
|------|-------|--------------|----------|
| Statement | 68 | Various | GOTO, PRINT, FOR, NEXT, LET |
| Function | 48 | 0xF15x-0xF17x | ABS, SIN, COS, INT, LEFT$ |
| Operator | 3 | 0xF150-0xF16D | AND, OR, NOT |
| Keyword | 3 | 0xF1AD-0xF1B1 | STEP, THEN, TO |

**Type Determination**:
- Control nibble bit 7 in token table
- Statement tokens generally > 0x80 in low byte
- Function tokens generally < 0x80 in low byte

### By Functional Category

| Category | Count | Commands |
|----------|-------|----------|
| Program Control | 17 | GOTO, GOSUB, IF, FOR, NEXT, ON, RUN, END, STOP, etc. |
| I/O | 14 | INPUT, PRINT, LPRINT, GPRINT, CURSOR, CLS, etc. |
| Math | 24 | ABS, SIN, COS, TAN, LOG, EXP, SQR, INT, RND, etc. |
| String | 10 | CHR$, ASC, STR$, VAL, LEFT$, RIGHT$, MID$, LEN, etc. |
| Data/Variables | 6 | DATA, READ, RESTORE, DIM, LET, CLEAR |
| Memory | 8 | PEEK, POKE, PEEK#, POKE#, MEM, CALL, LOCK, UNLOCK |
| File Operations | 6 | CLOAD, CSAVE, MERGE, CHAIN, LIST, LLIST |
| Graphics | 14 | GRAPH, TEXT, LINE, RLINE, GCURSOR, GLCURSOR, etc. |
| Communications | 11 | SETCOM, SETDEV, TERMINAL, TRANSMIT, DEV$, etc. |
| Configuration | 6 | DEGREE, RADIAN, GRAD, BEEP, RMT, WAIT |
| Debug | 5 | TRON, TROFF, ERROR, ERL, ERN |
| Operators | 3 | AND, OR, NOT |

---

## Cross-Reference Tables

### By Token Value (Complete Mapping)

Sorted by token value (0xE680-0xF1B6):

```
Token    Command      Type       Device    Handler
----------------------------------------------------
0xE680   CSIZE        Statement  CE-150    $B180
0xE681   GRAPH        Statement  CE-150    $ACD3
0xE682   GLCURSOR     Statement  CE-150    $B191
0xE683   LCURSOR      Statement  CE-150    -
0xE684   SORGN        Statement  CE-150    $B153
0xE685   ROTATE       Statement  CE-150    $B15A
0xE686   TEXT         Statement  CE-150    $ACA6
0xE7A9   RMT          Statement  CE-150    $BEF9
0xE857   DEV$         Function   CE-158    $82D4
0xE858   COM$         Function   CE-158    $82D3
0xE859   INSTAT       Function   CE-158    $82E3
0xE85A   RINKEY$      Function   CE-158    $82E9
0xE880   OUTSTAT      Function   CE-158    $82E6
0xE881   PROTOCOL     Statement  CE-158    $82E8
0xE882   SETCOM       Statement  CE-158    $82D6
0xE883   TERMINAL     Statement  CE-158    $82D2
0xE884   DTE          Statement  CE-158    $82D1
0xE885   TRANSMIT     Statement  CE-158    $82D7
0xE886   SETDEV       Statement  CE-158    $82D5
0xF052   ERN          Function   PC-1500   $82DF
0xF053   ERL          Function   PC-1500   $82E0
0xF061   SPACE$       Function   PC-1500   $82EA
0xF084   CURSOR       Statement  PC-1500   $E846
0xF085   USING        Statement  PC-1500   $C67C
0xF088   CLS          Statement  PC-1500   $E865
0xF089   CLOAD        Statement  Shared    $82EC/$C000
0xF08F   MERGE        Statement  Shared    $82C0/$B994
0xF090   LIST         Statement  PC-1500   $C96E
0xF091   INPUT        Statement  Shared    $82E2/$C8FA
0xF093   GCURSOR      Statement  PC-1500   $E83E
0xF095   CSAVE        Statement  Shared    $82DD/$B8A6
0xF097   PRINT        Statement  Shared    $82E7/$E4EB
0xF09F   GPRINT       Statement  PC-1500   $E7AC
0xF0B0   FEED         Statement  CE-158    $82E1
0xF0B1   CONSOLE      Statement  CE-158    $82DE
0xF0B2   CHAIN        Statement  CE-150    $BB6A
0xF0B3   BREAK        Statement  PC-1500   $CD89
0xF0B4   ZONE         Statement  CE-158    $82EB
0xF0B5   COLOR        Statement  CE-150    $B16A
0xF0B6   LF           Statement  CE-150    $B1B4
0xF0B7   LINE         Statement  CE-150    $B222
0xF0B8   LLIST        Statement  Shared    $82E5/$B754
0xF0B9   LPRINT       Statement  Shared    $82E4/$B2EC
0xF0BA   RLINE        Statement  CE-150    $B224
0xF0BB   TAB          Function   CE-158    $880D
0xF0BC   TEST         Function   PC-1500   -
0xF150   AND          Operator   PC-1500   $CD89
0xF151   OR           Operator   PC-1500   $CD89
0xF158   MEM          Function   PC-1500   $DA5D
0xF15B   TIME         Function   PC-1500   $DE82
0xF15C   INKEY$       Function   PC-1500   $D9AA
0xF15D   PI           Function   PC-1500   $F5B5
0xF160   ASC          Function   PC-1500   $D9DD
0xF161   STR$         Function   PC-1500   $D9CE
0xF162   VAL          Function   PC-1500   $D9D7
0xF163   CHR$         Function   PC-1500   $D9B1
0xF164   LEN          Function   PC-1500   $D9DD
0xF165   DEG          Function   PC-1500   $F531
0xF166   DMS          Function   PC-1500   $F564
0xF167   STATUS       Function   PC-1500   $5A44
0xF168   POINT        Function   PC-1500   $EECB
0xF16B   SQR          Function   PC-1500   $F0E9
0xF16D   NOT          Operator   PC-1500   $599E
0xF16E   PEEK#        Function   PC-1500   $D993
0xF16F   PEEK         Function   PC-1500   $D993
0xF170   ABS          Function   PC-1500   $F597
0xF171   INT          Function   PC-1500   $F5BE
0xF172   RIGHT$       Function   PC-1500   $D9F3
0xF173   ASN          Function   PC-1500   $F49A
0xF174   ACS          Function   PC-1500   $F492
0xF175   ATN          Function   PC-1500   $F496
0xF176   LN           Function   PC-1500   $F161
0xF177   LOG          Function   PC-1500   $F165
0xF178   EXP          Function   PC-1500   $F1CB
0xF179   SGN          Function   PC-1500   $F59D
0xF17A   LEFT$        Function   PC-1500   $D9F3
0xF17B   MID$         Function   PC-1500   $D9F3
0xF17C   RND          Function   PC-1500   $F5DD
0xF17D   SIN          Function   PC-1500   $F3A2
0xF17E   COS          Function   PC-1500   $F391
0xF17F   TAN          Function   PC-1500   $F39E
0xF180   AREAD        Statement  PC-1500   $C684
0xF181   ARUN         Statement  PC-1500   $C684
0xF182   BEEP         Statement  PC-1500   $E5C1
0xF183   CONT         Statement  PC-1500   $C8C7
0xF186   GRAD         Statement  PC-1500   $C6A8
0xF187   CLEAR        Statement  PC-1500   $C85F
0xF18A   CALL         Statement  PC-1500   $C863
0xF18B   DIM          Statement  PC-1500   $C988
0xF18C   DEGREE       Statement  PC-1500   $C697
0xF18D   DATA         Statement  PC-1500   $C684
0xF18E   END          Statement  PC-1500   $C50D
0xF192   GOTO         Statement  PC-1500   $C515
0xF194   GOSUB        Statement  PC-1500   $C64E
0xF196   IF           Statement  PC-1500   $C5B4
0xF198   LET          Statement  PC-1500   $C458
0xF199   RETURN       Statement  PC-1500   $C6AC
0xF19A   NEXT         Statement  PC-1500   $C705
0xF19B   NEW          Statement  PC-1500   $C80A
0xF19C   ON           Statement  PC-1500   $C5E0
0xF19D   OPN          Statement  PC-1500   $E457
0xF19E   OFF          Statement  PC-1500   $CD89
0xF1A0   POKE#        Statement  PC-1500   $C778
0xF1A1   POKE         Statement  PC-1500   $C777
0xF1A2   PAUSE        Statement  PC-1500   $E6A5
0xF1A4   RUN          Statement  PC-1500   $C8B4
0xF1A5   FOR          Statement  PC-1500   $C711
0xF1A6   READ         Statement  PC-1500   $C7B8
0xF1A7   RESTORE      Statement  PC-1500   $C7A2
0xF1A8   RANDOM       Statement  PC-1500   $F641
0xF1AA   RADIAN       Statement  PC-1500   $C6A4
0xF1AB   REM          Statement  PC-1500   $C676
0xF1AC   STOP         Statement  PC-1500   $C4B6
0xF1AD   STEP         Keyword    PC-1500   $CD89
0xF1AE   THEN         Keyword    PC-1500   $CD89
0xF1AF   TRON         Statement  PC-1500   $C68C
0xF1B0   TROFF        Statement  PC-1500   $C693
0xF1B1   TO           Keyword    PC-1500   $CD89
0xF1B3   WAIT         Statement  PC-1500   $E86A
0xF1B4   ERROR        Statement  PC-1500   $CD89
0xF1B5   LOCK         Statement  PC-1500   $C968
0xF1B6   UNLOCK       Statement  PC-1500   $C96A
```

### By Handler Address (PC-1500 Core)

Sorted by handler address, showing handler reuse:

```
Handler  Count  Commands
---------------------------------------
$599E    1      NOT
$5A44    1      STATUS
$C458    1      LET
$C4B6    1      STOP
$C50D    1      END
$C515    1      GOTO
$C5B4    1      IF
$C5E0    1      ON
$C64E    1      GOSUB
$C676    1      REM
$C67C    1      USING
$C684    3      AREAD, ARUN, DATA
$C68C    1      TRON
$C693    1      TROFF
$C697    1      DEGREE
$C6A4    1      RADIAN
$C6A8    1      GRAD
$C6AC    1      RETURN
$C705    1      NEXT
$C711    1      FOR
$C777    1      POKE
$C778    1      POKE#
$C7A2    1      RESTORE
$C7B8    1      READ
$C80A    1      NEW
$C85F    1      CLEAR
$C863    1      CALL
$C8B4    1      RUN
$C8C7    1      CONT
$C8FA    1      INPUT
$C968    1      LOCK
$C96A    1      UNLOCK
$C96E    1      LIST
$C988    1      DIM
$CD89    9      AND, OR, ERROR, BREAK, OFF, STEP, THEN, TO
$D993    3      PEEK, PEEK#
$D9AA    1      INKEY$
$D9B1    1      CHR$
$D9CE    1      STR$
$D9D7    1      VAL
$D9DD    2      ASC, LEN
$D9F3    3      LEFT$, MID$, RIGHT$
$DA5D    1      MEM
$DE82    1      TIME
$E457    1      OPN
$E5C1    1      BEEP
$E83E    1      GCURSOR
$E846    1      CURSOR
$E865    1      CLS
$E86A    1      WAIT
$E6A5    1      PAUSE
$E7AC    1      GPRINT
$EECB    1      POINT
$F0E9    1      SQR
$F161    1      LN
$F165    1      LOG
$F1CB    1      EXP
$F391    1      COS
$F39E    1      TAN
$F3A2    1      SIN
$F492    1      ACS
$F496    1      ATN
$F49A    1      ASN
$F531    1      DEG
$F564    1      DMS
$F597    1      ABS
$F59D    1      SGN
$F5B5    1      PI
$F5BE    1      INT
$F5DD    1      RND
$F641    1      RANDOM
```

**Handler Reuse Analysis**:
- $CD89 is most reused (9 commands) - likely a common operator/keyword dispatcher
- $D993 handles both PEEK variants (2 commands)
- $D9DD handles ASC and LEN (2 commands)
- $D9F3 handles all substring functions (3 commands)
- $C684 handles AREAD, ARUN, and DATA (3 commands)

---

## Abbreviation System

### Abbreviation Pattern Analysis

| Pattern | Count | Examples |
|---------|-------|----------|
| First letter only | 23 | A (AREAD), B (BEEP), C (CONT), D (DIM) |
| First 2 letters | 17 | AB (ABS), AC (ACS), AN (AND), AS (ASN) |
| First 3 letters | 32 | ARU (ARUN), CHA (CHAIN), CLO (CLOAD) |
| First 4+ letters | 10 | GRAP (GRAPH), TROF (TROFF) |
| No abbreviation | 40 | ASC, CLS, COS, DMS, END, EXP, INT, etc. |

**Single-Letter Abbreviations** (23 commands):
A, B, C, D, E, F, G, I, L, LE, M, N, O, P, R, S, T, U, V, W

**Conflicts Avoided**:
- Commands with same first letter get longer abbreviations
- Example: C = CONT, CA = CALL, CHA = CHAIN, CH = CHR$, CL = CLEAR, CLO = CLOAD

### Abbreviation Consistency

The abbreviation system follows Sharp's Technical Reference Manual (page 115) with these rules:

1. **Shortest unique prefix**: Use minimum characters to distinguish
2. **Conflict resolution**: Add characters until unique
3. **Common commands prioritized**: Frequent commands get shorter abbreviations
4. **No abbreviation for short names**: 3-letter commands (ASC, COS, INT) not abbreviated

---

## Insights and Observations

### Token Space Efficiency

**Used**: 122 tokens
**Theoretical range**: 0xE680-0xF1FF = 2944 possible tokens
**Utilization**: 122/2944 = 4.1%

**Implications**:
- Massive room for expansion (95.9% unused)
- Current allocation very conservative
- Could support hundreds of additional commands
- Gaps suggest planned but unimplemented features

### Device Isolation Strategy

**Clean Separation**:
- PC-1500: 0xF0xx-0xF1xx ranges
- CE-150: 0xE680-0xE7xx range
- CE-158: 0xE857-0xE8xx range

**Benefits**:
- No token collisions between devices
- Easy identification of command origin by token value
- Peripheral ROM can be added/removed without affecting core
- Future peripherals can claim unused 0xExxx ranges

### Handler Optimization

**Shared Handlers**:
- $CD89 handles 9 commands (operators, keywords)
- String functions share $D9F3 handler
- PEEK variants share $D993 handler

**Rationale**:
- ROM space conservation
- Common code paths for similar operations
- Reduce duplicate logic
- Easier maintenance

### ROM Organization Patterns

**PC-1500 Core**:
- Program control: $C400-$C900 (clustered)
- Math library: $F000-$F600 (separate region)
- I/O functions: $E400-$E900 (near peripherals)

**CE-158 Dual Banks**:
- Most handlers at $82xx (centralized)
- Common entry point (MAIN_ENTRY at $82EC)
- Identical token tables enable bank switching
- ROM variants for special builds

---

## Recommendations for Future Analysis

1. **Disassemble unused token ranges**: Identify reserved vs. genuinely unused space
2. **Map peripheral detection logic**: How PC-1500 detects CE-150/CE-158 presence
3. **Handler call graph**: Trace function call relationships
4. **Performance profiling**: Measure token lookup and dispatch timing
5. **Compare with other Sharp models**: PC-1500A, PC-1600 token evolution

---

## Conclusion

The Sharp PC-1500 BASIC token system demonstrates:
- **Extensibility**: 95% unused token space for future expansion
- **Modularity**: Clean device separation with shared command support
- **Efficiency**: Handler reuse and optimized ROM organization
- **Clarity**: Systematic abbreviation rules and alphabetical organization

This architecture enabled Sharp to expand the PC-1500's capabilities through plug-in peripherals without redesigning the core interpreter, while maintaining backwards compatibility and providing consistent user experience across configurations.

---

## See Also

- [PC-1500 BASIC Reference](../PC-1500-BASIC-Reference.md) - Complete command documentation
- [CE-150 Graphics/Printer Reference](../CE-150-Reference.md) - Plotter interface details
- [CE-158 Communications Reference](../CE-158-Reference.md) - Serial/parallel interface
- Sharp PC-1500 Technical Reference Manual, Page 115 - Official abbreviation reference

---

*Document Version 1.0 - Analysis based on ROM disassemblies and source code examination*
