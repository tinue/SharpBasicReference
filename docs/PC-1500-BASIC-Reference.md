# Sharp PC-1500 BASIC Command Reference

## Overview

This document provides a comprehensive reference for the Sharp PC-1500 BASIC programming language, including all standard commands and those provided by the CE-150 plotter/printer and CE-158 communications interface peripherals.

The PC-1500 BASIC interpreter uses tokenized commands stored as 16-bit hexadecimal values. Each command is assigned a unique token that the interpreter looks up in ROM-based dispatch tables to execute the corresponding handler routine.

**Total Commands**: 122
- **PC-1500 Core**: 97 commands (0xF000-0xF1B6)
- **CE-150 Extension**: 14 commands (0xE680-0xE7A9, 0xF0B0-0xF0BC)
- **CE-158 Extension**: 11 commands (0xE857-0xE886)

## Quick Reference Table

| Command | Abbr. | Token | Type | Category | Handler |
|---------|-------|-------|------|----------|---------|
| ABS | AB | 0xF170 | Function | Math | $F597 |
| ACS | AC | 0xF174 | Function | Math | $F492 |
| AND | AN | 0xF150 | Operator | Logic | $CD89 |
| AREAD | A | 0xF180 | Statement | I/O | $C684 |
| ARUN | ARU | 0xF181 | Statement | Program | $C684 |
| ASC | - | 0xF160 | Function | String | $D9DD |
| ASN | AS | 0xF173 | Function | Math | $F49A |
| ATN | AT | 0xF175 | Function | Math | $F496 |
| BEEP | B | 0xF182 | Statement | Sound | $E5C1 |
| BREAK | - | 0xF0B3 | Statement | Debug | $CD89 |
| CALL | CA | 0xF18A | Statement | System | $C863 |
| CHAIN | CHA | 0xF0B2 | Statement | File | - |
| CHR$ | CH | 0xF163 | Function | String | $D9B1 |
| CLEAR | CL | 0xF187 | Statement | Memory | $C85F |
| CLOAD | CLO | 0xF089 | Statement | File | $82EC |
| CLS | - | 0xF088 | Statement | Display | $E865 |
| COLOR | COL | 0xF0B5 | Statement | Graphics | $B16A |
| COM$ | - | 0xE858 | Function | CE-158 | $82D3 |
| CONSOLE | - | 0xF0B1 | Statement | CE-158 | $82DE |
| CONT | C | 0xF183 | Statement | Program | $C8C7 |
| COS | - | 0xF17E | Function | Math | $F391 |
| CSAVE | CS | 0xF095 | Statement | File | $82DD |
| CSIZE | CSI | 0xE680 | Statement | CE-150 | $B180 |
| CURSOR | CU | 0xF084 | Statement | Display | $E846 |
| DATA | DA | 0xF18D | Statement | Data | $C684 |
| DEG | - | 0xF165 | Function | Math | $F531 |
| DEGREE | DE | 0xF18C | Statement | Config | $C697 |
| DEV$ | - | 0xE857 | Function | CE-158 | $82D4 |
| DIM | D | 0xF18B | Statement | Memory | $C988 |
| DMS | DM | 0xF166 | Function | Math | $F564 |
| DTE | - | 0xE884 | Statement | CE-158 | $82D1 |
| END | E | 0xF18E | Statement | Program | $C50D |
| ERL | - | 0xF053 | Function | Debug | $82E0 |
| ERN | - | 0xF052 | Function | Debug | $82DF |
| ERROR | ER | 0xF1B4 | Statement | Debug | $CD89 |
| EXP | EX | 0xF178 | Function | Math | $F1CB |
| FEED | - | 0xF0B0 | Statement | CE-150 | $82E1 |
| FOR | F | 0xF1A5 | Statement | Program | $C711 |
| GCURSOR | GCU | 0xF093 | Statement | Graphics | $E83E |
| GLCURSOR | GL | 0xE682 | Statement | CE-150 | $B191 |
| GOSUB | GOS | 0xF194 | Statement | Program | $C64E |
| GOTO | G | 0xF192 | Statement | Program | $C515 |
| GPRINT | GP | 0xF09F | Statement | Graphics | $E7AC |
| GRAD | GR | 0xF186 | Statement | Config | $C6A8 |
| GRAPH | GRAP | 0xE681 | Statement | CE-150 | $ACD3 |
| IF | - | 0xF196 | Statement | Program | $C5B4 |
| INKEY$ | INK | 0xF15C | Function | I/O | $D9AA |
| INPUT | I | 0xF091 | Statement | I/O | $82E2 |
| INSTAT | - | 0xE859 | Function | CE-158 | $82E3 |
| INT | - | 0xF171 | Function | Math | $F5BE |
| LCURSOR | LCU | 0xE683 | Statement | CE-150 | - |
| LEFT$ | LEF | 0xF17A | Function | String | $D9F3 |
| LEN | - | 0xF164 | Function | String | $D9DD |
| LET | LE | 0xF198 | Statement | Variable | $C458 |
| LF | - | 0xF0B6 | Statement | CE-150 | $B1B4 |
| LINE | LIN | 0xF0B7 | Statement | CE-150 | $B222 |
| LIST | L | 0xF090 | Statement | Program | $C96E |
| LLIST | LL | 0xF0B8 | Statement | CE-150 | $82E5 |
| LN | - | 0xF176 | Function | Math | $F161 |
| LOCK | LOC | 0xF1B5 | Statement | System | $C968 |
| LOG | LO | 0xF177 | Function | Math | $F165 |
| LPRINT | LP | 0xF0B9 | Statement | CE-150 | $82E4 |
| MEM | M | 0xF158 | Function | Memory | $DA5D |
| MERGE | MER | 0xF08F | Statement | File | $82C0 |
| MID$ | MI | 0xF17B | Function | String | $D9F3 |
| NEW | - | 0xF19B | Statement | Program | $C80A |
| NEXT | N | 0xF19A | Statement | Program | $C705 |
| NOT | NO | 0xF16D | Operator | Logic | $599E |
| OFF | - | 0xF19E | Statement | System | $CD89 |
| ON | O | 0xF19C | Statement | Program | $C5E0 |
| OPN | - | 0xF19D | Statement | I/O | $E457 |
| OR | - | 0xF151 | Operator | Logic | $CD89 |
| OUTSTAT | - | 0xE880 | Function | CE-158 | $82E6 |
| PAUSE | PA | 0xF1A2 | Statement | Program | $E6A5 |
| PEEK | - | 0xF16F | Function | Memory | $D993 |
| PEEK# | PE | 0xF16E | Function | Memory | $D993 |
| PI | - | 0xF15D | Function | Math | $F5B5 |
| POINT | POI | 0xF168 | Function | Graphics | $EECB |
| POKE | - | 0xF1A1 | Statement | Memory | $C777 |
| POKE# | PO | 0xF1A0 | Statement | Memory | $C778 |
| PRINT | P | 0xF097 | Statement | I/O | $82E7 |
| PROTOCOL | - | 0xE881 | Statement | CE-158 | $82E8 |
| RADIAN | RAD | 0xF1AA | Statement | Config | $C6A4 |
| RANDOM | RA | 0xF1A8 | Statement | Math | $F641 |
| READ | REA | 0xF1A6 | Statement | Data | $C7B8 |
| REM | - | 0xF1AB | Statement | Program | $C676 |
| RESTORE | RES | 0xF1A7 | Statement | Data | $C7A2 |
| RETURN | RE | 0xF199 | Statement | Program | $C6AC |
| RIGHT$ | RI | 0xF172 | Function | String | $D9F3 |
| RINKEY$ | - | 0xE85A | Function | CE-158 | $82E9 |
| RLINE | RL | 0xF0BA | Statement | CE-150 | $B224 |
| RMT | RM | 0xE7A9 | Statement | CE-150 | $BEF9 |
| RND | RN | 0xF17C | Function | Math | $F5DD |
| ROTATE | RO | 0xE685 | Statement | CE-150 | $B15A |
| RUN | R | 0xF1A4 | Statement | Program | $C8B4 |
| SETCOM | - | 0xE882 | Statement | CE-158 | $82D6 |
| SETDEV | - | 0xE886 | Statement | CE-158 | $82D5 |
| SGN | SG | 0xF179 | Function | Math | $F59D |
| SIN | SI | 0xF17D | Function | Math | $F3A2 |
| SORGN | SO | 0xE684 | Statement | CE-150 | $B153 |
| SPACE$ | - | 0xF061 | Function | String | $82EA |
| SQR | SQ | 0xF16B | Function | Math | $F0E9 |
| STATUS | STA | 0xF167 | Function | I/O | $5A44 |
| STEP | STE | 0xF1AD | Keyword | Program | $CD89 |
| STOP | S | 0xF1AC | Statement | Program | $C4B6 |
| STR$ | STR | 0xF161 | Function | String | $D9CE |
| TAB | - | 0xF0BB | Function | I/O | $880D |
| TAN | TA | 0xF17F | Function | Math | $F39E |
| TERMINAL | - | 0xE883 | Statement | CE-158 | $82D2 |
| TEST | TE | 0xF0BC | Function | Graphics | - |
| TEXT | TEX | 0xE686 | Statement | CE-150 | $ACA6 |
| THEN | T | 0xF1AE | Keyword | Program | $CD89 |
| TIME | TI | 0xF15B | Function | System | $DE82 |
| TO | - | 0xF1B1 | Keyword | Program | $CD89 |
| TRANSMIT | - | 0xE885 | Statement | CE-158 | $82D7 |
| TROFF | TROF | 0xF1B0 | Statement | Debug | $C693 |
| TRON | TR | 0xF1AF | Statement | Debug | $C68C |
| UNLOCK | UN | 0xF1B6 | Statement | System | $C96A |
| USING | U | 0xF085 | Statement | I/O | $C67C |
| VAL | V | 0xF162 | Function | String | $D9D7 |
| WAIT | W | 0xF1B3 | Statement | System | $E86A |
| ZONE | - | 0xF0B4 | Statement | I/O | $82EB |

---

## Command Categories

### 1. Program Control

#### **GOTO**
- **Token**: 0xF192
- **Abbreviation**: G
- **Handler**: $C515
- **Type**: Statement
- **Description**: Unconditional branch to specified line number.
- **Syntax**: `GOTO line`
- **Related**: GOSUB, ON

#### **GOSUB**
- **Token**: 0xF194
- **Abbreviation**: GOS
- **Handler**: $C64E
- **Type**: Statement
- **Description**: Call subroutine at specified line number.
- **Syntax**: `GOSUB line`
- **Related**: RETURN, ON

#### **RETURN**
- **Token**: 0xF199
- **Abbreviation**: RE
- **Handler**: $C6AC
- **Type**: Statement
- **Description**: Return from subroutine called by GOSUB.
- **Syntax**: `RETURN`
- **Related**: GOSUB

#### **IF**
- **Token**: 0xF196
- **Abbreviation**: -
- **Handler**: $C5B4
- **Type**: Statement
- **Description**: Conditional execution based on expression.
- **Syntax**: `IF condition THEN statement [ELSE statement]`
- **Related**: THEN

#### **THEN**
- **Token**: 0xF1AE
- **Abbreviation**: T
- **Handler**: $CD89
- **Type**: Keyword
- **Description**: Used with IF to specify action when condition is true.
- **Syntax**: Part of IF statement
- **Related**: IF

#### **ON**
- **Token**: 0xF19C
- **Abbreviation**: O
- **Handler**: $C5E0
- **Type**: Statement
- **Description**: Conditional GOTO or GOSUB based on numeric expression.
- **Syntax**: `ON expr GOTO|GOSUB line1[,line2,...]`
- **Related**: GOTO, GOSUB

#### **FOR**
- **Token**: 0xF1A5
- **Abbreviation**: F
- **Handler**: $C711
- **Type**: Statement
- **Description**: Begin loop with counter variable.
- **Syntax**: `FOR var=start TO end [STEP increment]`
- **Related**: NEXT, STEP, TO

#### **NEXT**
- **Token**: 0xF19A
- **Abbreviation**: N
- **Handler**: $C705
- **Type**: Statement
- **Description**: End of FOR loop, increment counter and test.
- **Syntax**: `NEXT [var]`
- **Related**: FOR

#### **STEP**
- **Token**: 0xF1AD
- **Abbreviation**: STE
- **Handler**: $CD89
- **Type**: Keyword
- **Description**: Specifies increment value in FOR loop.
- **Syntax**: Part of FOR statement
- **Related**: FOR, TO

#### **TO**
- **Token**: 0xF1B1
- **Abbreviation**: -
- **Handler**: $CD89
- **Type**: Keyword
- **Description**: Specifies end value in FOR loop.
- **Syntax**: Part of FOR statement
- **Related**: FOR, STEP

#### **END**
- **Token**: 0xF18E
- **Abbreviation**: E
- **Handler**: $C50D
- **Type**: Statement
- **Description**: Terminate program execution.
- **Syntax**: `END`
- **Related**: STOP

#### **STOP**
- **Token**: 0xF1AC
- **Abbreviation**: S
- **Handler**: $C4B6
- **Type**: Statement
- **Description**: Pause program execution (can be continued with CONT).
- **Syntax**: `STOP`
- **Related**: CONT, END

#### **CONT**
- **Token**: 0xF183
- **Abbreviation**: C
- **Handler**: $C8C7
- **Type**: Statement
- **Description**: Continue execution after STOP or BREAK.
- **Syntax**: `CONT`
- **Related**: STOP, BREAK

#### **RUN**
- **Token**: 0xF1A4
- **Abbreviation**: R
- **Handler**: $C8B4
- **Type**: Statement
- **Description**: Execute program from beginning or specified line.
- **Syntax**: `RUN [line]`
- **Related**: ARUN, CHAIN

#### **ARUN**
- **Token**: 0xF181
- **Abbreviation**: ARU
- **Handler**: $C684
- **Type**: Statement
- **Description**: Auto-run program after loading.
- **Syntax**: `ARUN`
- **Related**: RUN, AREAD

#### **PAUSE**
- **Token**: 0xF1A2
- **Abbreviation**: PA
- **Handler**: $E6A5
- **Type**: Statement
- **Description**: Pause execution and wait for key press.
- **Syntax**: `PAUSE [message]`
- **Related**: WAIT

#### **REM**
- **Token**: 0xF1AB
- **Abbreviation**: -
- **Handler**: $C676
- **Type**: Statement
- **Description**: Comment/remark (ignored during execution).
- **Syntax**: `REM comment text`
- **Related**: None

#### **NEW**
- **Token**: 0xF19B
- **Abbreviation**: -
- **Handler**: $C80A
- **Type**: Statement
- **Description**: Clear program and variables from memory.
- **Syntax**: `NEW`
- **Related**: CLEAR

### 2. Input/Output

#### **INPUT**
- **Token**: 0xF091
- **Abbreviation**: I
- **Handler**: $82E2 (CE-158), $C8FA (PC-1500)
- **Type**: Statement
- **Description**: Accept user input and assign to variables.
- **Syntax**: `INPUT [prompt;] var1[,var2,...]`
- **Related**: PRINT, AREAD

#### **PRINT**
- **Token**: 0xF097
- **Abbreviation**: P
- **Handler**: $82E7 (CE-158), $E4EB (PC-1500)
- **Type**: Statement
- **Description**: Output data to display or device.
- **Syntax**: `PRINT [expr1][;|,][expr2]...`
- **Related**: LPRINT, GPRINT, USING

#### **LPRINT**
- **Token**: 0xF0B9
- **Abbreviation**: LP
- **Handler**: $82E4 (CE-158), $B2EC (CE-150)
- **Type**: Statement
- **Description**: Print to CE-150 plotter/printer.
- **Syntax**: `LPRINT [expr1][;|,][expr2]...`
- **Hardware**: Requires CE-150
- **Related**: PRINT, LLIST

#### **GPRINT**
- **Token**: 0xF09F
- **Abbreviation**: GP
- **Handler**: $E7AC
- **Type**: Statement
- **Description**: Print to graphics display.
- **Syntax**: `GPRINT [expr1][;|,][expr2]...`
- **Related**: PRINT, GCURSOR

#### **CURSOR**
- **Token**: 0xF084
- **Abbreviation**: CU
- **Handler**: $E846
- **Type**: Statement
- **Description**: Set text cursor position.
- **Syntax**: `CURSOR column`
- **Related**: GCURSOR, LCURSOR

#### **GCURSOR**
- **Token**: 0xF093
- **Abbreviation**: GCU
- **Handler**: $E83E
- **Type**: Statement
- **Description**: Set graphics cursor position.
- **Syntax**: `GCURSOR x, y`
- **Related**: CURSOR, GPRINT

#### **USING**
- **Token**: 0xF085
- **Abbreviation**: U
- **Handler**: $C67C
- **Type**: Statement
- **Description**: Format numeric output.
- **Syntax**: `USING format; expr`
- **Related**: PRINT, LPRINT

#### **ZONE**
- **Token**: 0xF0B4
- **Abbreviation**: -
- **Handler**: $82EB (CE-158)
- **Type**: Statement
- **Description**: Set print zone width.
- **Syntax**: `ZONE width`
- **Related**: PRINT, TAB

#### **TAB**
- **Token**: 0xF0BB
- **Abbreviation**: -
- **Handler**: $880D (CE-158)
- **Type**: Function
- **Description**: Move to specified column position.
- **Syntax**: `TAB(column)`
- **Related**: ZONE, PRINT

#### **INKEY$**
- **Token**: 0xF15C
- **Abbreviation**: INK
- **Handler**: $D9AA
- **Type**: Function
- **Description**: Read keyboard without waiting (non-blocking).
- **Syntax**: `var$ = INKEY$`
- **Related**: INPUT

#### **AREAD**
- **Token**: 0xF180
- **Abbreviation**: A
- **Handler**: $C684
- **Type**: Statement
- **Description**: Auto-read mode for INPUT.
- **Syntax**: `AREAD`
- **Related**: INPUT, ARUN

#### **CLS**
- **Token**: 0xF088
- **Abbreviation**: -
- **Handler**: $E865
- **Type**: Statement
- **Description**: Clear display screen.
- **Syntax**: `CLS`
- **Related**: None

#### **OPN**
- **Token**: 0xF19D
- **Abbreviation**: -
- **Handler**: $E457
- **Type**: Statement
- **Description**: Open output device.
- **Syntax**: `OPN device`
- **Related**: OFF

#### **OFF**
- **Token**: 0xF19E
- **Abbreviation**: -
- **Handler**: $CD89
- **Type**: Statement
- **Description**: Close output device.
- **Syntax**: `OFF`
- **Related**: OPN

#### **STATUS**
- **Token**: 0xF167
- **Abbreviation**: STA
- **Handler**: $5A44
- **Type**: Function
- **Description**: Return device status.
- **Syntax**: `STATUS(device)`
- **Related**: OPN, OFF

### 3. Mathematical Functions

#### **ABS**
- **Token**: 0xF170
- **Abbreviation**: AB
- **Handler**: $F597
- **Type**: Function
- **Description**: Return absolute value.
- **Syntax**: `ABS(expr)`
- **Related**: SGN

#### **SIN**
- **Token**: 0xF17D
- **Abbreviation**: SI
- **Handler**: $F3A2
- **Type**: Function
- **Description**: Return sine of angle.
- **Syntax**: `SIN(angle)`
- **Related**: COS, TAN, ASN

#### **COS**
- **Token**: 0xF17E
- **Abbreviation**: -
- **Handler**: $F391
- **Type**: Function
- **Description**: Return cosine of angle.
- **Syntax**: `COS(angle)`
- **Related**: SIN, TAN, ACS

#### **TAN**
- **Token**: 0xF17F
- **Abbreviation**: TA
- **Handler**: $F39E
- **Type**: Function
- **Description**: Return tangent of angle.
- **Syntax**: `TAN(angle)`
- **Related**: SIN, COS, ATN

#### **ATN**
- **Token**: 0xF175
- **Abbreviation**: AT
- **Handler**: $F496
- **Type**: Function
- **Description**: Return arctangent (inverse tangent).
- **Syntax**: `ATN(expr)`
- **Related**: TAN, ASN, ACS

#### **ASN**
- **Token**: 0xF173
- **Abbreviation**: AS
- **Handler**: $F49A
- **Type**: Function
- **Description**: Return arcsine (inverse sine).
- **Syntax**: `ASN(expr)`
- **Related**: SIN, ATN, ACS

#### **ACS**
- **Token**: 0xF174
- **Abbreviation**: AC
- **Handler**: $F492
- **Type**: Function
- **Description**: Return arccosine (inverse cosine).
- **Syntax**: `ACS(expr)`
- **Related**: COS, ATN, ASN

#### **EXP**
- **Token**: 0xF178
- **Abbreviation**: EX
- **Handler**: $F1CB
- **Type**: Function
- **Description**: Return e raised to power (e^x).
- **Syntax**: `EXP(expr)`
- **Related**: LOG, LN

#### **LOG**
- **Token**: 0xF177
- **Abbreviation**: LO
- **Handler**: $F165
- **Type**: Function
- **Description**: Return common logarithm (base 10).
- **Syntax**: `LOG(expr)`
- **Related**: LN, EXP

#### **LN**
- **Token**: 0xF176
- **Abbreviation**: -
- **Handler**: $F161
- **Type**: Function
- **Description**: Return natural logarithm (base e).
- **Syntax**: `LN(expr)`
- **Related**: LOG, EXP

#### **SQR**
- **Token**: 0xF16B
- **Abbreviation**: SQ
- **Handler**: $F0E9
- **Type**: Function
- **Description**: Return square root.
- **Syntax**: `SQR(expr)`
- **Related**: None

#### **INT**
- **Token**: 0xF171
- **Abbreviation**: -
- **Handler**: $F5BE
- **Type**: Function
- **Description**: Return integer part (floor function).
- **Syntax**: `INT(expr)`
- **Related**: None

#### **SGN**
- **Token**: 0xF179
- **Abbreviation**: SG
- **Handler**: $F59D
- **Type**: Function
- **Description**: Return sign of number (-1, 0, or +1).
- **Syntax**: `SGN(expr)`
- **Related**: ABS

#### **RND**
- **Token**: 0xF17C
- **Abbreviation**: RN
- **Handler**: $F5DD
- **Type**: Function
- **Description**: Return random number between 0 and 1.
- **Syntax**: `RND` or `RND(expr)`
- **Related**: RANDOM

#### **RANDOM**
- **Token**: 0xF1A8
- **Abbreviation**: RA
- **Handler**: $F641
- **Type**: Statement
- **Description**: Initialize random number generator with seed.
- **Syntax**: `RANDOM [seed]`
- **Related**: RND

#### **PI**
- **Token**: 0xF15D
- **Abbreviation**: -
- **Handler**: $F5B5
- **Type**: Function
- **Description**: Return value of π (pi).
- **Syntax**: `PI`
- **Related**: None

#### **DEG**
- **Token**: 0xF165
- **Abbreviation**: -
- **Handler**: $F531
- **Type**: Function
- **Description**: Convert radians to degrees.
- **Syntax**: `DEG(radians)`
- **Related**: DMS, DEGREE, RADIAN

#### **DMS**
- **Token**: 0xF166
- **Abbreviation**: DM
- **Handler**: $F564
- **Type**: Function
- **Description**: Convert decimal degrees to degrees-minutes-seconds.
- **Syntax**: `DMS(degrees)`
- **Related**: DEG, DEGREE

### 4. String Functions

#### **CHR$**
- **Token**: 0xF163
- **Abbreviation**: CH
- **Handler**: $D9B1
- **Type**: Function
- **Description**: Convert ASCII code to character.
- **Syntax**: `CHR$(code)`
- **Related**: ASC

#### **ASC**
- **Token**: 0xF160
- **Abbreviation**: -
- **Handler**: $D9DD
- **Type**: Function
- **Description**: Return ASCII code of first character.
- **Syntax**: `ASC(string$)`
- **Related**: CHR$

#### **STR$**
- **Token**: 0xF161
- **Abbreviation**: STR
- **Handler**: $D9CE
- **Type**: Function
- **Description**: Convert number to string.
- **Syntax**: `STR$(expr)`
- **Related**: VAL

#### **VAL**
- **Token**: 0xF162
- **Abbreviation**: V
- **Handler**: $D9D7
- **Type**: Function
- **Description**: Convert string to number.
- **Syntax**: `VAL(string$)`
- **Related**: STR$

#### **LEFT$**
- **Token**: 0xF17A
- **Abbreviation**: LEF
- **Handler**: $D9F3
- **Type**: Function
- **Description**: Extract leftmost characters from string.
- **Syntax**: `LEFT$(string$, n)`
- **Related**: RIGHT$, MID$

#### **RIGHT$**
- **Token**: 0xF172
- **Abbreviation**: RI
- **Handler**: $D9F3
- **Type**: Function
- **Description**: Extract rightmost characters from string.
- **Syntax**: `RIGHT$(string$, n)`
- **Related**: LEFT$, MID$

#### **MID$**
- **Token**: 0xF17B
- **Abbreviation**: MI
- **Handler**: $D9F3
- **Type**: Function
- **Description**: Extract substring from middle of string.
- **Syntax**: `MID$(string$, start[, length])`
- **Related**: LEFT$, RIGHT$

#### **LEN**
- **Token**: 0xF164
- **Abbreviation**: -
- **Handler**: $D9DD
- **Type**: Function
- **Description**: Return length of string.
- **Syntax**: `LEN(string$)`
- **Related**: None

#### **SPACE$**
- **Token**: 0xF061
- **Abbreviation**: -
- **Handler**: $82EA (CE-158)
- **Type**: Function
- **Description**: Return string of spaces.
- **Syntax**: `SPACE$(n)`
- **Related**: None

### 5. Data & Variables

#### **DATA**
- **Token**: 0xF18D
- **Abbreviation**: DA
- **Handler**: $C684
- **Type**: Statement
- **Description**: Define data for READ statement.
- **Syntax**: `DATA value1[,value2,...]`
- **Related**: READ, RESTORE

#### **READ**
- **Token**: 0xF1A6
- **Abbreviation**: REA
- **Handler**: $C7B8
- **Type**: Statement
- **Description**: Read values from DATA statements.
- **Syntax**: `READ var1[,var2,...]`
- **Related**: DATA, RESTORE

#### **RESTORE**
- **Token**: 0xF1A7
- **Abbreviation**: RES
- **Handler**: $C7A2
- **Type**: Statement
- **Description**: Reset DATA pointer to beginning.
- **Syntax**: `RESTORE [line]`
- **Related**: DATA, READ

#### **DIM**
- **Token**: 0xF18B
- **Abbreviation**: D
- **Handler**: $C988
- **Type**: Statement
- **Description**: Declare array dimensions.
- **Syntax**: `DIM array(size1[,size2,...])`
- **Related**: CLEAR

#### **LET**
- **Token**: 0xF198
- **Abbreviation**: LE
- **Handler**: $C458
- **Type**: Statement
- **Description**: Assign value to variable (optional keyword).
- **Syntax**: `[LET] var = expr`
- **Related**: None

#### **CLEAR**
- **Token**: 0xF187
- **Abbreviation**: CL
- **Handler**: $C85F
- **Type**: Statement
- **Description**: Clear all variables and arrays.
- **Syntax**: `CLEAR`
- **Related**: NEW, DIM

### 6. Memory & System

#### **PEEK**
- **Token**: 0xF16F
- **Abbreviation**: -
- **Handler**: $D993
- **Type**: Function
- **Description**: Read byte from memory address.
- **Syntax**: `PEEK(address)`
- **Related**: POKE, PEEK#

#### **PEEK#**
- **Token**: 0xF16E
- **Abbreviation**: PE
- **Handler**: $D993
- **Type**: Function
- **Description**: Read byte from I/O port.
- **Syntax**: `PEEK#(port)`
- **Related**: PEEK, POKE#

#### **POKE**
- **Token**: 0xF1A1
- **Abbreviation**: -
- **Handler**: $C777
- **Type**: Statement
- **Description**: Write byte to memory address.
- **Syntax**: `POKE address, value`
- **Related**: PEEK, POKE#

#### **POKE#**
- **Token**: 0xF1A0
- **Abbreviation**: PO
- **Handler**: $C778
- **Type**: Statement
- **Description**: Write byte to I/O port.
- **Syntax**: `POKE# port, value`
- **Related**: POKE, PEEK#

#### **MEM**
- **Token**: 0xF158
- **Abbreviation**: M
- **Handler**: $DA5D
- **Type**: Function
- **Description**: Return amount of free memory.
- **Syntax**: `MEM`
- **Related**: CLEAR

#### **CALL**
- **Token**: 0xF18A
- **Abbreviation**: CA
- **Handler**: $C863
- **Type**: Statement
- **Description**: Call machine language subroutine.
- **Syntax**: `CALL address[,param1,...]`
- **Related**: None

#### **TIME**
- **Token**: 0xF15B
- **Abbreviation**: TI
- **Handler**: $DE82
- **Type**: Function
- **Description**: Return or set system time.
- **Syntax**: `TIME` or `TIME=value`
- **Related**: WAIT

#### **WAIT**
- **Token**: 0xF1B3
- **Abbreviation**: W
- **Handler**: $E86A
- **Type**: Statement
- **Description**: Delay execution for specified time.
- **Syntax**: `WAIT duration`
- **Related**: PAUSE, TIME

#### **BEEP**
- **Token**: 0xF182
- **Abbreviation**: B
- **Handler**: $E5C1
- **Type**: Statement
- **Description**: Generate audible tone.
- **Syntax**: `BEEP duration[,frequency]`
- **Related**: None

#### **LOCK**
- **Token**: 0xF1B5
- **Abbreviation**: LOC
- **Handler**: $C968
- **Type**: Statement
- **Description**: Protect program from modification.
- **Syntax**: `LOCK`
- **Related**: UNLOCK

#### **UNLOCK**
- **Token**: 0xF1B6
- **Abbreviation**: UN
- **Handler**: $C96A
- **Type**: Statement
- **Description**: Remove program protection.
- **Syntax**: `UNLOCK`
- **Related**: LOCK

### 7. File Operations

#### **CLOAD**
- **Token**: 0xF089
- **Abbreviation**: CLO
- **Handler**: $82EC (CE-158)
- **Type**: Statement
- **Description**: Load program from cassette tape.
- **Syntax**: `CLOAD ["filename"]`
- **CE-158 Variants**:
  - `CLOADa` - Load program in ASCII text format from RS-232C
  - `CLOADr` - Load reserve program from RS-232C
- **Related**: CSAVE, MERGE

#### **CSAVE**
- **Token**: 0xF095
- **Abbreviation**: CS
- **Handler**: $82DD (CE-158)
- **Type**: Statement
- **Description**: Save program to cassette tape.
- **Syntax**: `CSAVE ["filename"]`
- **CE-158 Variants**:
  - `CSAVEa` - Save program in ASCII text format to RS-232C (human-readable)
  - `CSAVEr` - Save reserve program to RS-232C
- **Related**: CLOAD

#### **MERGE**
- **Token**: 0xF08F
- **Abbreviation**: MER
- **Handler**: $82C0 (CE-158)
- **Type**: Statement
- **Description**: Load and merge program from tape.
- **Syntax**: `MERGE ["filename"]`
- **CE-158 Variant**:
  - `MERGEa` - Merge program in ASCII text format from RS-232C
- **Related**: CLOAD, CHAIN

#### **CHAIN**
- **Token**: 0xF0B2
- **Abbreviation**: CHA
- **Handler**: - (CE-150)
- **Type**: Statement
- **Description**: Load and execute program from tape.
- **Syntax**: `CHAIN "filename"`
- **Related**: RUN, MERGE

#### **LIST**
- **Token**: 0xF090
- **Abbreviation**: L
- **Handler**: $C96E
- **Type**: Statement
- **Description**: Display program listing.
- **Syntax**: `LIST [start[-end]]`
- **Related**: LLIST

#### **LLIST**
- **Token**: 0xF0B8
- **Abbreviation**: LL
- **Handler**: $82E5 (CE-158), $B754 (CE-150)
- **Type**: Statement
- **Description**: Print program listing to CE-150 printer.
- **Syntax**: `LLIST [start[-end]]`
- **Hardware**: Requires CE-150
- **Related**: LIST, LPRINT

### 8. Graphics (CE-150)

#### **GRAPH**
- **Token**: 0xE681
- **Abbreviation**: GRAP
- **Handler**: $ACD3 (CE-150)
- **Type**: Statement
- **Description**: Switch to graphics mode on CE-150.
- **Syntax**: `GRAPH`
- **Hardware**: Requires CE-150
- **Related**: TEXT

#### **TEXT**
- **Token**: 0xE686
- **Abbreviation**: TEX
- **Handler**: $ACA6 (CE-150)
- **Type**: Statement
- **Description**: Switch to text mode on CE-150.
- **Syntax**: `TEXT`
- **Hardware**: Requires CE-150
- **Related**: GRAPH

#### **GLCURSOR**
- **Token**: 0xE682
- **Abbreviation**: GL
- **Handler**: $B191 (CE-150)
- **Type**: Statement
- **Description**: Set graphics cursor position on CE-150.
- **Syntax**: `GLCURSOR x, y`
- **Hardware**: Requires CE-150
- **Related**: LCURSOR, GCURSOR

#### **LCURSOR**
- **Token**: 0xE683
- **Abbreviation**: LCU
- **Handler**: - (CE-150)
- **Type**: Statement
- **Description**: Set line cursor on CE-150.
- **Syntax**: `LCURSOR`
- **Hardware**: Requires CE-150
- **Related**: GLCURSOR

#### **LINE**
- **Token**: 0xF0B7
- **Abbreviation**: LIN
- **Handler**: $B222 (CE-150)
- **Type**: Statement
- **Description**: Draw absolute line on CE-150.
- **Syntax**: `LINE x, y`
- **Hardware**: Requires CE-150
- **Related**: RLINE

#### **RLINE**
- **Token**: 0xF0BA
- **Abbreviation**: RL
- **Handler**: $B224 (CE-150)
- **Type**: Statement
- **Description**: Draw relative line on CE-150.
- **Syntax**: `RLINE dx, dy`
- **Hardware**: Requires CE-150
- **Related**: LINE

#### **POINT**
- **Token**: 0xF168
- **Abbreviation**: POI
- **Handler**: $EECB
- **Type**: Function
- **Description**: Test if point is set in graphics.
- **Syntax**: `POINT(x, y)`
- **Related**: None

#### **SORGN**
- **Token**: 0xE684
- **Abbreviation**: SO
- **Handler**: $B153 (CE-150)
- **Type**: Statement
- **Description**: Set origin point for graphics on CE-150.
- **Syntax**: `SORGN x, y`
- **Hardware**: Requires CE-150
- **Related**: None

#### **ROTATE**
- **Token**: 0xE685
- **Abbreviation**: RO
- **Handler**: $B15A (CE-150)
- **Type**: Statement
- **Description**: Set rotation angle (0-3) on CE-150.
- **Syntax**: `ROTATE angle`
- **Hardware**: Requires CE-150
- **Related**: None

#### **COLOR**
- **Token**: 0xF0B5
- **Abbreviation**: COL
- **Handler**: $B16A (CE-150)
- **Type**: Statement
- **Description**: Select pen color (0-3) on CE-150.
- **Syntax**: `COLOR pen`
- **Hardware**: Requires CE-150
- **Related**: None

#### **CSIZE**
- **Token**: 0xE680
- **Abbreviation**: CSI
- **Handler**: $B180 (CE-150)
- **Type**: Statement
- **Description**: Set character size (0-3) on CE-150.
- **Syntax**: `CSIZE size`
- **Hardware**: Requires CE-150
- **Related**: None

#### **LF**
- **Token**: 0xF0B6
- **Abbreviation**: -
- **Handler**: $B1B4 (CE-150)
- **Type**: Statement
- **Description**: Line feed on CE-150 printer.
- **Syntax**: `LF [n]`
- **Hardware**: Requires CE-150
- **Related**: FEED

#### **FEED**
- **Token**: 0xF0B0
- **Abbreviation**: -
- **Handler**: $82E1 (CE-158)
- **Type**: Statement
- **Description**: Paper feed on CE-150.
- **Syntax**: `FEED`
- **Hardware**: Requires CE-150
- **Related**: LF

#### **RMT**
- **Token**: 0xE7A9
- **Abbreviation**: RM
- **Handler**: $BEF9 (CE-150)
- **Type**: Statement
- **Description**: Remote control cassette motor.
- **Syntax**: `RMT 0|1`
- **Related**: None

#### **TEST**
- **Token**: 0xF0BC
- **Abbreviation**: TE
- **Handler**: -
- **Type**: Function
- **Description**: Graphics test function.
- **Syntax**: `TEST(x, y)`
- **Related**: POINT

### 9. Communications (CE-158)

#### **SETCOM**
- **Token**: 0xE882
- **Abbreviation**: -
- **Handler**: $82D6 (CE-158)
- **Type**: Statement
- **Description**: Configure RS-232C parameters.
- **Syntax**: `SETCOM baud, parity, data, stop`
- **Hardware**: Requires CE-158
- **Related**: SETDEV, PROTOCOL

#### **SETDEV**
- **Token**: 0xE886
- **Abbreviation**: -
- **Handler**: $82D5 (CE-158)
- **Type**: Statement
- **Description**: Select communication device.
- **Syntax**: `SETDEV device`
- **Hardware**: Requires CE-158
- **Related**: SETCOM, DEV$

#### **PROTOCOL**
- **Token**: 0xE881
- **Abbreviation**: -
- **Handler**: $82E8 (CE-158)
- **Type**: Statement
- **Description**: Set communication protocol.
- **Syntax**: `PROTOCOL type`
- **Hardware**: Requires CE-158
- **Related**: SETCOM

#### **DEV$**
- **Token**: 0xE857
- **Abbreviation**: -
- **Handler**: $82D4 (CE-158)
- **Type**: Function
- **Description**: Return current device setting.
- **Syntax**: `DEV$`
- **Hardware**: Requires CE-158
- **Related**: SETDEV

#### **COM$**
- **Token**: 0xE858
- **Abbreviation**: -
- **Handler**: $82D3 (CE-158)
- **Type**: Function
- **Description**: Return communication parameters.
- **Syntax**: `COM$`
- **Hardware**: Requires CE-158
- **Related**: SETCOM

#### **INSTAT**
- **Token**: 0xE859
- **Abbreviation**: -
- **Handler**: $82E3 (CE-158)
- **Type**: Function
- **Description**: Return input buffer status.
- **Syntax**: `INSTAT`
- **Hardware**: Requires CE-158
- **Related**: OUTSTAT, RINKEY$

#### **OUTSTAT**
- **Token**: 0xE880
- **Abbreviation**: -
- **Handler**: $82E6 (CE-158)
- **Type**: Function
- **Description**: Return output buffer status.
- **Syntax**: `OUTSTAT`
- **Hardware**: Requires CE-158
- **Related**: INSTAT

#### **RINKEY$**
- **Token**: 0xE85A
- **Abbreviation**: -
- **Handler**: $82E9 (CE-158)
- **Type**: Function
- **Description**: Read character from serial port (non-blocking).
- **Syntax**: `RINKEY$`
- **Hardware**: Requires CE-158
- **Related**: INKEY$, INSTAT

#### **TERMINAL**
- **Token**: 0xE883
- **Abbreviation**: -
- **Handler**: $82D2 (CE-158)
- **Type**: Statement
- **Description**: Enter terminal mode for direct communication.
- **Syntax**: `TERMINAL`
- **Hardware**: Requires CE-158
- **Related**: TRANSMIT

#### **TRANSMIT**
- **Token**: 0xE885
- **Abbreviation**: -
- **Handler**: $82D7 (CE-158)
- **Type**: Statement
- **Description**: Transmit data over serial port.
- **Syntax**: `TRANSMIT data`
- **Hardware**: Requires CE-158
- **Related**: TERMINAL

#### **DTE**
- **Token**: 0xE884
- **Abbreviation**: -
- **Handler**: $82D1 (CE-158)
- **Type**: Statement
- **Description**: Configure DTE/DCE mode.
- **Syntax**: `DTE mode`
- **Hardware**: Requires CE-158
- **Related**: SETCOM

#### **CONSOLE**
- **Token**: 0xF0B1
- **Abbreviation**: -
- **Handler**: $82DE (CE-158)
- **Type**: Statement
- **Description**: Set console device.
- **Syntax**: `CONSOLE device`
- **Hardware**: Requires CE-158
- **Related**: SETDEV

### 10. Configuration

#### **DEGREE**
- **Token**: 0xF18C
- **Abbreviation**: DE
- **Handler**: $C697
- **Type**: Statement
- **Description**: Set angle mode to degrees.
- **Syntax**: `DEGREE`
- **Related**: RADIAN, GRAD

#### **RADIAN**
- **Token**: 0xF1AA
- **Abbreviation**: RAD
- **Handler**: $C6A4
- **Type**: Statement
- **Description**: Set angle mode to radians.
- **Syntax**: `RADIAN`
- **Related**: DEGREE, GRAD

#### **GRAD**
- **Token**: 0xF186
- **Abbreviation**: GR
- **Handler**: $C6A8
- **Type**: Statement
- **Description**: Set angle mode to grads.
- **Syntax**: `GRAD`
- **Related**: DEGREE, RADIAN

### 11. Debugging

#### **TRON**
- **Token**: 0xF1AF
- **Abbreviation**: TR
- **Handler**: $C68C
- **Type**: Statement
- **Description**: Enable program trace mode.
- **Syntax**: `TRON`
- **Related**: TROFF

#### **TROFF**
- **Token**: 0xF1B0
- **Abbreviation**: TROF
- **Handler**: $C693
- **Type**: Statement
- **Description**: Disable program trace mode.
- **Syntax**: `TROFF`
- **Related**: TRON

#### **ERROR**
- **Token**: 0xF1B4
- **Abbreviation**: ER
- **Handler**: $CD89
- **Type**: Statement
- **Description**: Generate error condition.
- **Syntax**: `ERROR code`
- **Related**: ERL, ERN

#### **ERL**
- **Token**: 0xF053
- **Abbreviation**: -
- **Handler**: $82E0 (CE-158)
- **Type**: Function
- **Description**: Return line number of last error.
- **Syntax**: `ERL`
- **Related**: ERN, ERROR

#### **ERN**
- **Token**: 0xF052
- **Abbreviation**: -
- **Handler**: $82DF (CE-158)
- **Type**: Function
- **Description**: Return error code of last error.
- **Syntax**: `ERN`
- **Related**: ERL, ERROR

#### **BREAK**
- **Token**: 0xF0B3
- **Abbreviation**: -
- **Handler**: $CD89
- **Type**: Statement
- **Description**: Set breakpoint.
- **Syntax**: `BREAK`
- **Related**: CONT

### 12. Logical Operators

#### **AND**
- **Token**: 0xF150
- **Abbreviation**: AN
- **Handler**: $CD89
- **Type**: Operator
- **Description**: Logical AND operation.
- **Syntax**: `expr1 AND expr2`
- **Related**: OR, NOT

#### **OR**
- **Token**: 0xF151
- **Abbreviation**: -
- **Handler**: $CD89
- **Type**: Operator
- **Description**: Logical OR operation.
- **Syntax**: `expr1 OR expr2`
- **Related**: AND, NOT

#### **NOT**
- **Token**: 0xF16D
- **Abbreviation**: NO
- **Handler**: $599E
- **Type**: Operator
- **Description**: Logical NOT operation.
- **Syntax**: `NOT expr`
- **Related**: AND, OR

---

## Appendix A: Token Table (Sorted by Token Value)

| Token | Command | Type | Device |
|-------|---------|------|--------|
| 0xE680 | CSIZE | Statement | CE-150 |
| 0xE681 | GRAPH | Statement | CE-150 |
| 0xE682 | GLCURSOR | Statement | CE-150 |
| 0xE683 | LCURSOR | Statement | CE-150 |
| 0xE684 | SORGN | Statement | CE-150 |
| 0xE685 | ROTATE | Statement | CE-150 |
| 0xE686 | TEXT | Statement | CE-150 |
| 0xE7A9 | RMT | Statement | CE-150 |
| 0xE857 | DEV$ | Function | CE-158 |
| 0xE858 | COM$ | Function | CE-158 |
| 0xE859 | INSTAT | Function | CE-158 |
| 0xE85A | RINKEY$ | Function | CE-158 |
| 0xE880 | OUTSTAT | Function | CE-158 |
| 0xE881 | PROTOCOL | Statement | CE-158 |
| 0xE882 | SETCOM | Statement | CE-158 |
| 0xE883 | TERMINAL | Statement | CE-158 |
| 0xE884 | DTE | Statement | CE-158 |
| 0xE885 | TRANSMIT | Statement | CE-158 |
| 0xE886 | SETDEV | Statement | CE-158 |
| 0xF052 | ERN | Function | PC-1500 |
| 0xF053 | ERL | Function | PC-1500 |
| 0xF061 | SPACE$ | Function | String |
| 0xF084 | CURSOR | Statement | PC-1500 |
| 0xF085 | USING | Statement | PC-1500 |
| 0xF088 | CLS | Statement | PC-1500 |
| 0xF089 | CLOAD | Statement | PC-1500 |
| 0xF08F | MERGE | Statement | PC-1500 |
| 0xF090 | LIST | Statement | PC-1500 |
| 0xF091 | INPUT | Statement | PC-1500 |
| 0xF093 | GCURSOR | Statement | PC-1500 |
| 0xF095 | CSAVE | Statement | PC-1500 |
| 0xF097 | PRINT | Statement | PC-1500 |
| 0xF09F | GPRINT | Statement | PC-1500 |
| 0xF0B0 | FEED | Statement | CE-150 |
| 0xF0B1 | CONSOLE | Statement | CE-158 |
| 0xF0B2 | CHAIN | Statement | CE-150 |
| 0xF0B3 | BREAK | Statement | PC-1500 |
| 0xF0B4 | ZONE | Statement | PC-1500 |
| 0xF0B5 | COLOR | Statement | CE-150 |
| 0xF0B6 | LF | Statement | CE-150 |
| 0xF0B7 | LINE | Statement | CE-150 |
| 0xF0B8 | LLIST | Statement | CE-150 |
| 0xF0B9 | LPRINT | Statement | CE-150 |
| 0xF0BA | RLINE | Statement | CE-150 |
| 0xF0BB | TAB | Function | PC-1500 |
| 0xF0BC | TEST | Function | PC-1500 |
| 0xF150 | AND | Operator | PC-1500 |
| 0xF151 | OR | Operator | PC-1500 |
| 0xF158 | MEM | Function | PC-1500 |
| 0xF15B | TIME | Function | PC-1500 |
| 0xF15C | INKEY$ | Function | PC-1500 |
| 0xF15D | PI | Function | PC-1500 |
| 0xF160 | ASC | Function | PC-1500 |
| 0xF161 | STR$ | Function | PC-1500 |
| 0xF162 | VAL | Function | PC-1500 |
| 0xF163 | CHR$ | Function | PC-1500 |
| 0xF164 | LEN | Function | PC-1500 |
| 0xF165 | DEG | Function | PC-1500 |
| 0xF166 | DMS | Function | PC-1500 |
| 0xF167 | STATUS | Function | PC-1500 |
| 0xF168 | POINT | Function | PC-1500 |
| 0xF16B | SQR | Function | PC-1500 |
| 0xF16D | NOT | Operator | PC-1500 |
| 0xF16E | PEEK# | Function | PC-1500 |
| 0xF16F | PEEK | Function | PC-1500 |
| 0xF170 | ABS | Function | PC-1500 |
| 0xF171 | INT | Function | PC-1500 |
| 0xF172 | RIGHT$ | Function | PC-1500 |
| 0xF173 | ASN | Function | PC-1500 |
| 0xF174 | ACS | Function | PC-1500 |
| 0xF175 | ATN | Function | PC-1500 |
| 0xF176 | LN | Function | PC-1500 |
| 0xF177 | LOG | Function | PC-1500 |
| 0xF178 | EXP | Function | PC-1500 |
| 0xF179 | SGN | Function | PC-1500 |
| 0xF17A | LEFT$ | Function | PC-1500 |
| 0xF17B | MID$ | Function | PC-1500 |
| 0xF17C | RND | Function | PC-1500 |
| 0xF17D | SIN | Function | PC-1500 |
| 0xF17E | COS | Function | PC-1500 |
| 0xF17F | TAN | Function | PC-1500 |
| 0xF180 | AREAD | Statement | PC-1500 |
| 0xF181 | ARUN | Statement | PC-1500 |
| 0xF182 | BEEP | Statement | PC-1500 |
| 0xF183 | CONT | Statement | PC-1500 |
| 0xF186 | GRAD | Statement | PC-1500 |
| 0xF187 | CLEAR | Statement | PC-1500 |
| 0xF18A | CALL | Statement | PC-1500 |
| 0xF18B | DIM | Statement | PC-1500 |
| 0xF18C | DEGREE | Statement | PC-1500 |
| 0xF18D | DATA | Statement | PC-1500 |
| 0xF18E | END | Statement | PC-1500 |
| 0xF192 | GOTO | Statement | PC-1500 |
| 0xF194 | GOSUB | Statement | PC-1500 |
| 0xF196 | IF | Statement | PC-1500 |
| 0xF198 | LET | Statement | PC-1500 |
| 0xF199 | RETURN | Statement | PC-1500 |
| 0xF19A | NEXT | Statement | PC-1500 |
| 0xF19B | NEW | Statement | PC-1500 |
| 0xF19C | ON | Statement | PC-1500 |
| 0xF19D | OPN | Statement | PC-1500 |
| 0xF19E | OFF | Statement | PC-1500 |
| 0xF1A0 | POKE# | Statement | PC-1500 |
| 0xF1A1 | POKE | Statement | PC-1500 |
| 0xF1A2 | PAUSE | Statement | PC-1500 |
| 0xF1A4 | RUN | Statement | PC-1500 |
| 0xF1A5 | FOR | Statement | PC-1500 |
| 0xF1A6 | READ | Statement | PC-1500 |
| 0xF1A7 | RESTORE | Statement | PC-1500 |
| 0xF1A8 | RANDOM | Statement | PC-1500 |
| 0xF1AA | RADIAN | Statement | PC-1500 |
| 0xF1AB | REM | Statement | PC-1500 |
| 0xF1AC | STOP | Statement | PC-1500 |
| 0xF1AD | STEP | Keyword | PC-1500 |
| 0xF1AE | THEN | Keyword | PC-1500 |
| 0xF1AF | TRON | Statement | PC-1500 |
| 0xF1B0 | TROFF | Statement | PC-1500 |
| 0xF1B1 | TO | Keyword | PC-1500 |
| 0xF1B3 | WAIT | Statement | PC-1500 |
| 0xF1B4 | ERROR | Statement | PC-1500 |
| 0xF1B5 | LOCK | Statement | PC-1500 |
| 0xF1B6 | UNLOCK | Statement | PC-1500 |

---

## Appendix B: Abbreviation Quick Reference

| Abbreviation | Command | Abbreviation | Command |
|--------------|---------|--------------|---------|
| A | AREAD | LEF | LEFT$ |
| AB | ABS | LIN | LINE |
| AC | ACS | LL | LLIST |
| AN | AND | LO | LOG |
| ARU | ARUN | LOC | LOCK |
| AS | ASN | LP | LPRINT |
| AT | ATN | LCU | LCURSOR |
| B | BEEP | M | MEM |
| C | CONT | MER | MERGE |
| CA | CALL | MI | MID$ |
| CH | CHR$ | N | NEXT |
| CHA | CHAIN | NO | NOT |
| CL | CLEAR | O | ON |
| CLO | CLOAD | P | PRINT |
| COL | COLOR | PA | PAUSE |
| CS | CSAVE | PE | PEEK# |
| CSI | CSIZE | PO | POKE# |
| CU | CURSOR | POI | POINT |
| D | DIM | R | RUN |
| DA | DATA | RA | RANDOM |
| DE | DEGREE | RAD | RADIAN |
| DM | DMS | RE | RETURN |
| E | END | REA | READ |
| ER | ERROR | RES | RESTORE |
| EX | EXP | RI | RIGHT$ |
| F | FOR | RL | RLINE |
| G | GOTO | RM | RMT |
| GCU | GCURSOR | RN | RND |
| GL | GLCURSOR | RO | ROTATE |
| GOS | GOSUB | S | STOP |
| GP | GPRINT | SG | SGN |
| GR | GRAD | SI | SIN |
| GRAP | GRAPH | SO | SORGN |
| I | INPUT | SQ | SQR |
| INK | INKEY$ | STA | STATUS |
| L | LIST | STE | STEP |
| LE | LET | STR | STR$ |

---

## Appendix C: Handler Addresses (PC-1500 ROM)

Handlers are listed by ROM address for PC-1500 core commands:

| Address | Command(s) |
|---------|------------|
| $599E | NOT |
| $5A44 | STATUS |
| $C458 | LET |
| $C4B6 | STOP |
| $C50D | END |
| $C515 | GOTO |
| $C5B4 | IF |
| $C5E0 | ON |
| $C64E | GOSUB |
| $C676 | REM |
| $C67C | USING |
| $C684 | AREAD, ARUN, DATA |
| $C68C | TRON |
| $C693 | TROFF |
| $C697 | DEGREE |
| $C6A4 | RADIAN |
| $C6A8 | GRAD |
| $C6AC | RETURN |
| $C705 | NEXT |
| $C711 | FOR |
| $C777 | POKE |
| $C778 | POKE# |
| $C7A2 | RESTORE |
| $C7B8 | READ |
| $C80A | NEW |
| $C85F | CLEAR |
| $C863 | CALL |
| $C8B4 | RUN |
| $C8C7 | CONT |
| $C8FA | INPUT |
| $C968 | LOCK |
| $C96A | UNLOCK |
| $C96E | LIST |
| $C988 | DIM |
| $CD89 | AND, OR, ERROR, BREAK, OFF, STEP, THEN, TO |
| $D993 | PEEK, PEEK# |
| $D9AA | INKEY$ |
| $D9B1 | CHR$ |
| $D9CE | STR$ |
| $D9D7 | VAL |
| $D9DD | ASC, LEN |
| $D9F3 | LEFT$, MID$, RIGHT$ |
| $DA5D | MEM |
| $DE82 | TIME |
| $E457 | OPN |
| $E5C1 | BEEP |
| $E83E | GCURSOR |
| $E846 | CURSOR |
| $E865 | CLS |
| $E86A | WAIT |
| $E6A5 | PAUSE |
| $E7AC | GPRINT |
| $EECB | POINT |
| $F0E9 | SQR |
| $F161 | LN |
| $F165 | LOG |
| $F1CB | EXP |
| $F391 | COS |
| $F39E | TAN |
| $F3A2 | SIN |
| $F492 | ACS |
| $F496 | ATN |
| $F49A | ASN |
| $F531 | DEG |
| $F564 | DMS |
| $F597 | ABS |
| $F59D | SGN |
| $F5B5 | PI |
| $F5BE | INT |
| $F5DD | RND |
| $F641 | RANDOM |

---

## Appendix D: Memory Map Reference

### PC-1500 Core System

**RAM Organization** (0x4000-0x47FF without expansion):
- 0x4000-0x4007: RAM start
- 0x4008-0x40C4: Reserved memory area
- 0x40C5+: Program memory start

**Display and String Variables** (0x7600-0x77FF):
- 0x7600-0x774F: Display buffer (336 bytes)
- 0x7650-0x77AF: Fixed string variables

**System Stack and Registers** (0x7800-0x79FF):
- 0x7800-0x784F: CPU stack (80 bytes)
- 0x7860-0x78BF: System pointers and program counters
- 0x78C0-0x79CF: Fixed variables (272 bytes)
- 0x79D0-0x79FF: System configuration registers

**Floating-Point Registers** (0x7A00-0x7A37):
- 0x7A00-0x7A07: ARX accumulator
- 0x7A08-0x7A0F: ARZ scratch
- 0x7A10-0x7A17: ARY second operand
- 0x7A18-0x7A1F: ARU scratch
- 0x7A20-0x7A27: ARV scratch
- 0x7A28-0x7A2F: ARW scratch
- 0x7A30-0x7A37: ARS temporary storage

**BASIC Stack and Buffers** (0x7A38-0x7BFF):
- 0x7A38-0x7AFF: BASIC stack (200 bytes)
- 0x7B00-0x7B07: Random number value
- 0x7B10-0x7B5F: String buffer (80 bytes)
- 0x7B60-0x7BAF: Output buffer (80 bytes)
- 0x7BB0-0x7BFF: Input buffer (128 bytes)

**I/O Ports** (0xF004-0xF00F):
- 0xF004: Divider reset
- 0xF005: U register output
- 0xF006: Serial transfer
- 0xF007: F register load/divide
- 0xF008: Port C
- 0xF009: G register
- 0xF00A: Mask register
- 0xF00B: Interrupt flag register
- 0xF00C-0xF00D: Port direction registers
- 0xF00E-0xF00F: Ports A and B

---

## See Also

- [CE-150 Graphics/Printer Reference](CE-150-Reference.md)
- [CE-158 Communications Reference](CE-158-Reference.md)
- [Token Mapping Analysis](analysis/Token-Mapping-Analysis.md)

---

*Document Version 1.0 - Generated from ROM disassemblies and source code analysis*
