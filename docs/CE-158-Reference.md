# Sharp CE-158 RS-232C/Parallel Interface Reference

## Overview

The **Sharp CE-158** is a dual-interface expansion module for the Sharp PC-1500 pocket computer, providing both RS-232C serial communication and parallel printer interfaces. It enables the PC-1500 to communicate with external devices, modems, printers, and other computers.

### Key Features
- **RS-232C Serial Interface**: Full-duplex asynchronous communication
- **Parallel Printer Port**: Centronics-compatible output
- **Dual ROM Banks**: 8KB total ROM (4KB × 2 banks at $8000)
- **Hardware UART**: Dedicated serial controller
- **Programmable Configuration**: Baud rate, parity, data bits, stop bits
- **Protocol Support**: Handshaking and flow control
- **Device Switching**: Runtime selection between serial and parallel

### Hardware Specifications
- **Baud Rates**: 75, 150, 300, 600, 1200, 2400, 4800, 9600 bps
- **Data Bits**: 7 or 8
- **Parity**: None, Odd, Even
- **Stop Bits**: 1 or 2
- **Flow Control**: XON/XOFF, RTS/CTS
- **UART**: Dedicated hardware at 0xD200-0xD203
- **Expansion Bus**: ME1 interface (0xD000-0xDFFF)

---

## Command Summary

### CE-158 Communications Commands (Token Range 0xE857-0xE886)

| Command | Token | Handler (Low/High) | Type | Description |
|---------|-------|-------------------|------|-------------|
| DEV$ | 0xE857 | $82D4 | Function | Return current device |
| COM$ | 0xE858 | $82D3 | Function | Return comm parameters |
| INSTAT | 0xE859 | $82E3 | Function | Input buffer status |
| RINKEY$ | 0xE85A | $82E9 | Function | Read serial char (non-blocking) |
| OUTSTAT | 0xE880 | $82E6 | Function | Output buffer status |
| PROTOCOL | 0xE881 | $82E8 | Statement | Set protocol type |
| SETCOM | 0xE882 | $82D6 | Statement | Configure serial params |
| TERMINAL | 0xE883 | $82D2 | Statement | Enter terminal mode |
| DTE | 0xE884 | $82D1 | Statement | Set DTE/DCE mode |
| TRANSMIT | 0xE885 | $82D7 | Statement | Transmit data |
| SETDEV | 0xE886 | $82D5 | Statement | Select device |

### Enhanced/Shared Commands

These PC-1500 core commands have enhanced functionality with CE-158:

| Command | Token | Handler | Description |
|---------|-------|---------|-------------|
| CONSOLE | 0xF0B1 | $82DE | Set console device |
| INPUT | 0xF091 | $82E2 | Input (device-aware) |
| PRINT | 0xF097 | $82E7 | Print (device-aware) |
| CLOAD | 0xF089 | $82EC | Load from tape (enhanced) |
| CLOADa | 0xF089 | $82EC | Load from serial in ASCII format |
| CLOADr | 0xF089 | $82EC | Load reserve program from serial |
| CSAVE | 0xF095 | $82DD | Save to tape (enhanced) |
| CSAVEa | 0xF095 | $82DD | Save to serial in ASCII format |
| CSAVEr | 0xF095 | $82DD | Save reserve program to serial |
| MERGE | 0xF08F | $82C0 | Merge from tape |
| MERGEa | 0xF08F | $82C0 | Merge from serial in ASCII format |
| LPRINT | 0xF0B9 | $82E4 | Print to printer |
| LLIST | 0xF0B8 | $82E5 | List to printer |
| FEED | 0xF0B0 | $82E1 | Paper feed |
| ZONE | 0xF0B4 | $82EB | Set zone width |
| TAB | 0xF0BB | $880D | Tab function |
| SPACE$ | 0xF061 | $82EA | Space string |
| ERL | 0xF053 | $82E0 | Error line number |
| ERN | 0xF052 | $82DF | Error number |
| BREAK | 0xF0B3 | $CD89 | Break statement |
| PRINT# | - | - | Output data to serial with filename |
| INPUT# | - | - | Input data from serial with filename |

---

## Detailed Command Reference

### Configuration Commands

#### **SETCOM**
- **Token**: 0xE882
- **Abbreviation**: -
- **Handler**: $82D6 (both ROM banks)
- **Type**: Statement
- **Description**: Configure RS-232C serial communication parameters.
- **Syntax**: `SETCOM baud, parity, data, stop`
- **Parameters**:
  - `baud`: Baud rate code (0-7)
    - 0 = 75 bps
    - 1 = 150 bps
    - 2 = 300 bps
    - 3 = 600 bps
    - 4 = 1200 bps
    - 5 = 2400 bps
    - 6 = 4800 bps
    - 7 = 9600 bps
  - `parity`: Parity code (0-2)
    - 0 = None
    - 1 = Odd
    - 2 = Even
  - `data`: Data bits (7 or 8)
  - `stop`: Stop bits (1 or 2)
- **Examples**:
  - `SETCOM 4,0,8,1` - 1200 baud, no parity, 8 data bits, 1 stop bit
  - `SETCOM 7,2,7,1` - 9600 baud, even parity, 7 data bits, 1 stop bit
- **Storage**: Configuration saved in register $7858 (SETCOM_REG)
- **Related**: COM$, PROTOCOL, SETDEV

#### **SETDEV**
- **Token**: 0xE886
- **Abbreviation**: -
- **Handler**: $82D5 (both ROM banks)
- **Type**: Statement
- **Description**: Select active input/output device.
- **Syntax**: `SETDEV device`
- **Parameters**:
  - `device`: Device code
    - 0 = Console (PC-1500 display/keyboard)
    - 1 = RS-232C (serial port)
    - 2 = Parallel printer
- **Storage**: Device code saved in register $7857 (SETDEV_REG)
- **Effects**: Redirects INPUT, PRINT, and other I/O commands
- **Related**: DEV$, CONSOLE

#### **PROTOCOL**
- **Token**: 0xE881
- **Abbreviation**: -
- **Handler**: $82E8 (both ROM banks)
- **Type**: Statement
- **Description**: Set communication protocol and handshaking.
- **Syntax**: `PROTOCOL type`
- **Parameters**:
  - `type`: Protocol code
    - 0 = None (no handshaking)
    - 1 = XON/XOFF software flow control
    - 2 = RTS/CTS hardware flow control
- **Default**: 0 (None)
- **Related**: SETCOM, DTE

#### **DTE**
- **Token**: 0xE884
- **Abbreviation**: -
- **Handler**: $82D1 (both ROM banks)
- **Type**: Statement
- **Description**: Configure DTE (Data Terminal Equipment) or DCE (Data Communication Equipment) mode.
- **Syntax**: `DTE mode`
- **Parameters**:
  - `mode`: Operating mode
    - 0 = DCE mode
    - 1 = DTE mode (default)
- **Note**: Affects pin assignments and handshaking signals
- **Related**: SETCOM, PROTOCOL

#### **CONSOLE**
- **Token**: 0xF0B1
- **Abbreviation**: -
- **Handler**: $82DE (CE-158)
- **Type**: Statement
- **Description**: Set console device for system messages and input.
- **Syntax**: `CONSOLE device`
- **Parameters**: Same as SETDEV
- **Related**: SETDEV, INPUT, PRINT

### Status and Information Commands

#### **DEV$**
- **Token**: 0xE857
- **Abbreviation**: -
- **Handler**: $82D4 (both ROM banks)
- **Type**: Function
- **Description**: Return string indicating current active device.
- **Syntax**: `var$ = DEV$`
- **Returns**:
  - "CON" = Console
  - "RS232C" = RS-232C serial
  - "PARALLEL" = Parallel printer
- **Related**: SETDEV, COM$

#### **COM$**
- **Token**: 0xE858
- **Abbreviation**: -
- **Handler**: $82D3 (both ROM banks)
- **Type**: Function
- **Description**: Return string with current communication parameters.
- **Syntax**: `var$ = COM$`
- **Returns**: Format: "BBBB,P,D,S"
  - BBBB = Baud rate (75, 150, 300, etc.)
  - P = Parity (N=None, O=Odd, E=Even)
  - D = Data bits (7 or 8)
  - S = Stop bits (1 or 2)
- **Example**: "1200,N,8,1"
- **Related**: SETCOM, DEV$

#### **INSTAT**
- **Token**: 0xE859
- **Abbreviation**: -
- **Handler**: $82E3 (both ROM banks)
- **Type**: Function
- **Description**: Return number of characters in input buffer.
- **Syntax**: `n = INSTAT`
- **Returns**: Integer (0 if buffer empty)
- **Use**: Check before RINKEY$ to avoid blocking
- **Related**: OUTSTAT, RINKEY$, INPUT

#### **OUTSTAT**
- **Token**: 0xE880
- **Abbreviation**: -
- **Handler**: $82E6 (both ROM banks)
- **Type**: Function
- **Description**: Return status of output buffer.
- **Syntax**: `n = OUTSTAT`
- **Returns**:
  - 0 = Buffer full or busy
  - Non-zero = Ready for output
- **Use**: Check before PRINT/TRANSMIT to avoid buffer overflow
- **Related**: INSTAT, PRINT, TRANSMIT

### Data Transfer Commands

#### **RINKEY$**
- **Token**: 0xE85A
- **Abbreviation**: -
- **Handler**: $82E9 (both ROM banks)
- **Type**: Function
- **Description**: Read single character from serial input (non-blocking).
- **Syntax**: `char$ = RINKEY$`
- **Returns**:
  - Single character string if data available
  - Empty string "" if no data
- **Note**: Similar to INKEY$ but for serial port
- **Related**: INKEY$, INSTAT, INPUT

#### **TRANSMIT**
- **Token**: 0xE885
- **Abbreviation**: -
- **Handler**: $82D7 (both ROM banks)
- **Type**: Statement
- **Description**: Transmit data through current device.
- **Syntax**: `TRANSMIT data`
- **Parameters**:
  - `data`: String or numeric expression
- **Behavior**:
  - Sends data immediately
  - No automatic line termination
  - Respects PROTOCOL flow control
- **Related**: PRINT, OUTSTAT

#### **TERMINAL**
- **Token**: 0xE883
- **Abbreviation**: -
- **Handler**: $82D2 (both ROM banks)
- **Type**: Statement
- **Description**: Enter terminal emulation mode for interactive communication.
- **Syntax**: `TERMINAL`
- **Behavior**:
  - Keyboard input sent to serial port
  - Serial input displayed on screen
  - Exit with BREAK key
  - Full duplex operation
- **Related**: TRANSMIT, RINKEY$

### Program Transfer Commands (ASCII and Binary Formats)

The CE-158 extends CLOAD, CSAVE, and MERGE with format variants for data interchange.

#### **CSAVEa - Save Program in ASCII Format**
- **Token**: 0xF095 (same as CSAVE)
- **Handler**: $82DD
- **Type**: Statement
- **Description**: Save BASIC program to RS-232C port in human-readable ASCII format.
- **Syntax**: `CSAVEa ["filename"]`
- **Requirements**:
  - SETDEV must declare CO (communications output)
  - Word length must be 8 bits (set via SETCOM)
- **Format**:
  - Program sent in ASCII text format
  - One CR code issued after sending program
  - File name (up to 16 chars) converted to ASCII before transmission
  - Blank portions padded with NULL codes (0x00) if filename < 16 chars
- **Use Cases**:
  - Transfer programs to other computers
  - Create human-readable program backups
  - Edit programs on PC before re-loading
- **Example**:
  ```basic
  SETCOM 1200,N,8,1
  SETDEV CO
  CSAVEa "PROG1"
  ```
- **Related**: CSAVE, CLOADa, MERGEa

#### **CSAVEr - Save Reserve Program**
- **Token**: 0xF095 (same as CSAVE)
- **Handler**: $82DD
- **Type**: Statement
- **Description**: Save reserve (protected) program to RS-232C in internal binary format.
- **Syntax**: `CSAVEr ["filename"]`
- **Requirements**:
  - SETDEV must declare CO (communications output)
  - Word length must be 8 bits
- **Format**: Same as CSAVE in RESERVE mode (binary/internal code)
- **Related**: CSAVE, CLOADr

#### **CLOADa - Load Program in ASCII Format**
- **Token**: 0xF089 (same as CLOAD)
- **Handler**: $82EC
- **Type**: Statement
- **Description**: Load BASIC program from RS-232C port in ASCII text format.
- **Syntax**: `CLOADa`
- **Requirements**:
  - SETDEV must declare CI (communications input)
  - Manual operation only (not in program execution)
- **Format Constraints**:
  - Maximum 160 ASCII codes per program line
  - Must be within 80 codes when converted to internal format
  - Requires 2 seconds of all MARKs (no signal) between program lines
  - CR code terminates program loading operation
- **Behavior**:
  - Loads program immediately upon execution
  - Does not require header intervention
  - 100ms all-mark signal required between header and contents
- **Error Handling**:
  - If error/break occurs during loading, portion already loaded stays in memory
  - Program will be NEW when error or break is met
- **Example**:
  ```basic
  SETCOM 1200,N,8,1
  SETDEV CI
  CLOADa
  ```
- **Related**: CLOAD, CSAVEa, MERGEa

#### **CLOADr - Load Reserve Program**
- **Token**: 0xF089 (same as CLOAD)
- **Handler**: $82EC
- **Type**: Statement
- **Description**: Load reserve program from RS-232C in internal binary format.
- **Syntax**: `CLOADr ["filename"]`
- **Requirements**:
  - SETDEV must declare CI (communications input)
  - Manual operation only
  - Word length must be 8 bits
- **Format**: Same as CLOAD in RESERVE mode
- **Related**: CLOAD, CSAVEr

#### **MERGEa - Merge Program in ASCII Format**
- **Token**: 0xF08F (same as MERGE)
- **Handler**: $82C0
- **Type**: Statement
- **Description**: Merge ASCII format program from RS-232C with existing program in memory.
- **Syntax**: `MERGEa ["filename"]`
- **Requirements**:
  - SETDEV must declare CI (communications input)
  - Manual operation only (RUN or PRO mode)
- **Format**: Same constraints as CLOADa
- **Behavior**:
  - ASCII code program merged with currently existing program
  - If error/break occurs, only loaded portion is linked
  - Cannot correct/insert program during MERGEa (before transfer completes)
- **Special Conditions**:
  - After interruption by ⊕ or ★ key (PRO mode)
  - After error during command execution
  - Immediately after starting MERGEa (before transfer)

  If program is not on display in these cases, either:
  - Push ★ key after depressing CL key, or
  - Call desired line by means of LIST statement
- **Related**: MERGE, CLOADa, CSAVEa

#### **PRINT# - Output Data with Filename**
- **Type**: Statement
- **Description**: Send variable contents to RS-232C port in internal binary format with filename header.
- **Syntax**: `PRINT# "filename"; variable`
- **Requirements**:
  - SETDEV must declare CO (communications output)
  - Word length must be 8 bits
- **Format**:
  - Header sent prior to variable transfer
  - If filename not specified, headers must be identical for INPUT#/PRINT# pairs
  - Requires 4-second intergap (MARK state) between header and variable data, and between variables
- **Usage**:
  ```basic
  PRINT# "DATA"; A$(*)    ' Send entire array A$
  PRINT# "VARS"; @(*)     ' Send all fixed variables
  PRINT# "VALS"; @$(*)    ' Send all string variables A$ to Z$
  ```
- **Related**: INPUT#, TRANSMIT

#### **INPUT# - Input Data with Filename**
- **Type**: Statement
- **Description**: Receive variable contents from RS-232C port in internal binary format.
- **Syntax**: `INPUT# "filename"; variable`
- **Requirements**:
  - SETDEV must declare CI (communications input)
  - Word length must be 8 bits
- **Format**:
  - Data received in internal code mode
  - Substituted with specified variable
  - If variable is array, it must be previously declared by DIM
  - Two-letter variable names defined automatically
- **Usage**:
  ```basic
  INPUT# "DATA"; A$(*)    ' Receive into array A$
  INPUT# "VARS"; @(*)     ' Receive into fixed variables A-Z
  ```
- **Related**: PRINT#, RINKEY$

### Enhanced I/O Commands

#### **INPUT**
- **Token**: 0xF091
- **Handler**: $82E2 (CE-158), $C8FA (PC-1500)
- **Description**: Input with device awareness. Reads from device set by SETDEV.
- **Related**: SETDEV, PRINT

#### **PRINT**
- **Token**: 0xF097
- **Handler**: $82E7 (CE-158), $E4EB (PC-1500)
- **Description**: Print with device awareness. Outputs to device set by SETDEV.
- **Related**: SETDEV, LPRINT, TRANSMIT

---

## Memory Map

### Configuration Registers (0x7850-0x785A)

| Address | Name | Description |
|---------|------|-------------|
| 0x7850 | OUTSTAT_REG | OUTSTAT return value |
| 0x7851 | RS232C | Console 1 (RS232C) configuration |
| 0x7852 | CONSOLE2 | Console 2 (parallel) configuration |
| 0x7853 | CE158_UNDEF1 | Undefined/reserved |
| 0x7854 | CE158_UNDEF2 | Undefined/reserved |
| 0x7855 | CRLF_REG | CR/LF codes storage |
| 0x7856 | ZONE_REG | ZONE width setting |
| 0x7857 | SETDEV_REG | SETDEV device code (0=CON, 1=RS232C, 2=PAR) |
| 0x7858 | SETCOM_REG | SETCOM configuration bytes |
| 0x7859 | CE158_7859_UNDEF | Undefined/reserved |
| 0x785A | CE158_785A_UNDEF | Undefined/reserved |

### Working Registers (0x79DD-0x79FE, 0x7B08)

| Address | Name | Description |
|---------|------|-------------|
| 0x79DD | CE158_REG_79DD | BPD command flags (ROM modification) |
| 0x79DE | CE158_REG_79DE | Unknown use |
| 0x79DF | CE158_REG_79DF | CLOAD/CSAVE BUSY annunciator blink counter |
| 0x79FA | CE158_REG_79FA | CE-158 working register |
| 0x79FB | CE158_REG_79FB | CE-158 working register |
| 0x79FC | CE158_REG_79FC | CE-158 working register |
| 0x79FD | CE158_REG_79FD | CE-158 working register |
| 0x79FE | CE158_REG_79FE | CE-158 working register |
| 0x7B08 | CE158_7B08 | Used in Low Bank ROM |

### UART Hardware Registers (0xD200-0xD203)

| Address | Name | Access | Description |
|---------|------|--------|-------------|
| 0xD200 | CE158_UART_DATAW | Write | UART data write |
| 0xD201 | CE158_UART_REGW | Write | UART register write |
| 0xD202 | CE158_UART_DATAR | Read | UART data read |
| 0xD203 | CE158_UART_REGR | Read | UART register read |

### I/O Port Registers (0xD004-0xD00F)

| Address | Name | Access | Description |
|---------|------|--------|-------------|
| 0xD004 | CE158_DIV_RESET | R/W | Divider reset (ME1) |
| 0xD005 | CE158_UREG_OUTP | R/W | U register output (ME1) |
| 0xD006 | CE158_SER_XFR | R/W | Serial transfer (ME1) |
| 0xD007 | CE158_FREG_LDIV | R/W | F register load/divide (ME1) |
| 0xD008 | CE158_PRT_C | R/W | Port C (ME1) |
| 0xD009 | CE158_G_REG | R/W | G register (ME1) |
| 0xD00A | CE158_MSK_REG | R/W | Mask register (ME1) |
| 0xD00B | CE158_IF_REG | R/W | Interrupt flag register (ME1) |
| 0xD00C | CE158_PRT_A_DIR | R/W | Port A direction (ME1) |
| 0xD00D | CE158_PRT_B_DIR | R/W | Port B direction (ME1) |
| 0xD00E | CE158_PRT_A | R/W | Port A data (ME1) |
| 0xD00F | CE158_PRT_B | R/W | Port B data (ME1) |

### Interrupt Registers

| Address | Name | Access | Description |
|---------|------|--------|-------------|
| 0xDC00 | CE158_IR_PING | R/W | ISR ping address (no operation) |
| 0xDE00 | CE158_IR_REG | Read | Interrupt ID register (bit 7: 0=INT, 1=No INT) |

---

## ROM Architecture

### Dual Bank System

The CE-158 contains two 4KB ROM banks that occupy the same address space ($8000-$8FFF):

1. **Low Bank ROM** (CE-158_ROM_LOW.lh5801.asm)
   - Default bank at power-on
   - Contains most command handlers
   - Token table at $8000
   - Primary I/O routines

2. **High Bank ROM** (CE-158_ROM_HIGH.lh5801.asm)
   - Alternate bank
   - Duplicate token table at $8000
   - Additional routines and variants
   - ROM modification support (CE158V2, ENBPD)

### Token Tables

Both ROM banks contain identical token tables starting at $8000 (B_TBL_8000_CMD_LST). The token table uses a linked-list structure organized alphabetically by command name.

**Token Table Structure** (lines 183-242 in both ROM files):
- Control nibble encoding for command properties
- Command name as ASCII text
- 16-bit token value (0xE857-0xF0BB range)
- 16-bit handler vector address
- Alphabetical organization with letter index pointers

---

## ROM Modifications and Variants

### CE158V2 Build

Modified ROM variant with enhanced features:
- Alternative UART access methods
- Different MSR (Modem Status Register) handling
- Conditional compilation flag: `#IFDEF CE158V2`

### ENBPD Build

Enhanced ROM with BPD (Bidirectional Parallel Data) command support:
- Adds BPD command for parallel port bidirectional communication
- Uses register $79DD for BPD flags
- Modified CHAR2LPT function relocated to free space
- Conditional compilation flag: `#IFDEF ENBPD`

### HANDSHAKE Build

ROM variant with alternative handshaking:
- Modified flow control implementation
- Enhanced RTS/CTS handling
- Conditional compilation flag: `#IFDEF HANDSHAKE`

---

## Programming Examples

### Basic Serial Communication

```basic
10 REM Simple terminal program
20 SETCOM 4,0,8,1
30 SETDEV 1
40 PRINT "Connected at 1200 baud"
50 IF INSTAT THEN A$=RINKEY$: PRINT A$;
60 A$=INKEY$
70 IF A$<>"" THEN TRANSMIT A$
80 GOTO 50
```

### File Transfer

```basic
10 REM Send file over serial
20 SETCOM 7,0,8,1
30 SETDEV 1
40 PROTOCOL 1
50 LIST
60 TRANSMIT CHR$(26)
70 SETDEV 0
80 PRINT "Transfer complete"
```

### Status Monitoring

```basic
10 REM Monitor connection status
20 SETCOM 4,0,8,1
30 SETDEV 1
40 PRINT "Device: ";DEV$
50 PRINT "Config: ";COM$
60 PRINT "Input buffer: ";INSTAT
70 PRINT "Output ready: ";OUTSTAT
80 PAUSE
90 GOTO 40
```

### Parallel Printer Output

```basic
10 REM Print to parallel printer
20 SETDEV 2
30 LPRINT "Report Header"
40 LF 2
50 FOR I=1 TO 10
60 LPRINT "Line ";I
70 NEXT I
80 FEED
90 SETDEV 0
```

### ASCII Program Transfer

```basic
10 REM Save program in ASCII format
20 SETCOM 1200,N,8,1
30 SETDEV CO
40 OUTSTAT 0
50 CSAVEa "MYPROG"
60 SETDEV 0
70 PRINT "Program saved in ASCII"
```

```basic
10 REM Load ASCII program from serial
20 SETCOM 1200,N,8,1
30 SETDEV CI
40 CLOADa
50 REM Program now loaded
```

### Binary Program Transfer

```basic
10 REM Save program in binary format
20 SETCOM 9600,N,8,1
30 SETDEV CO
40 OUTSTAT 0
50 CSAVE "FASTPROG"
60 SETDEV 0
```

### Data Exchange with PRINT# and INPUT#

```basic
10 REM Send data file
20 SETCOM 1200,N,8,1
30 SETDEV CO
40 DIM A$(10)
50 FOR I=0 TO 10
60 A$(I)="DATA"+STR$(I)
70 NEXT I
80 PRINT# "DATASET"; A$(*)
90 SETDEV 0
100 PRINT "Data sent"
```

```basic
10 REM Receive data file
20 SETCOM 1200,N,8,1
30 SETDEV CI
40 DIM B$(10)
50 INPUT# "DATASET"; B$(*)
60 SETDEV 0
70 FOR I=0 TO 10
80 PRINT B$(I)
90 NEXT I
```

### Terminal Emulation

```basic
10 REM Auto-configure terminal
20 SETCOM 4,0,8,1
30 PROTOCOL 1
40 DTE 1
50 SETDEV 1
60 PRINT "Entering terminal mode"
70 PRINT "Press BREAK to exit"
80 TERMINAL
90 PRINT "Terminal session ended"
100 SETDEV 0
```

### Data Logging

```basic
10 REM Log serial data to array
20 DIM D$(100)
30 SETCOM 4,0,8,1
40 SETDEV 1
50 N=0
60 IF INSTAT=0 THEN GOTO 60
70 D$(N)=RINKEY$
80 N=N+1
90 IF N<100 THEN GOTO 60
100 SETDEV 0
110 FOR I=0 TO 99
120 PRINT D$(I)
130 NEXT I
```

---

## Program Transfer Format Comparison

### Format Variants Summary

| Command | Format | Use Case | File Size | Compatibility |
|---------|--------|----------|-----------|---------------|
| CSAVE | Binary (internal) | Fast save/load on same PC-1500 | Smallest | PC-1500 only |
| CSAVEa | ASCII text | Transfer to other computers | Larger | Universal |
| CSAVEr | Binary (reserve) | Save protected programs | Small | PC-1500 only |
| CLOAD | Binary (internal) | Fast load on same PC-1500 | - | PC-1500 only |
| CLOADa | ASCII text | Load from other computers | - | Universal |
| CLOADr | Binary (reserve) | Load protected programs | - | PC-1500 only |
| MERGE | Binary (internal) | Combine programs quickly | - | PC-1500 only |
| MERGEa | ASCII text | Merge from other systems | - | Universal |

### When to Use ASCII Format (a suffix)

**Advantages**:
- Human-readable program listing
- Can be edited on PC/terminal before re-loading
- Compatible with text editors and other computers
- Useful for documentation and archiving
- Easier debugging (can read program over serial terminal)

**Disadvantages**:
- Slower transmission (more data to send)
- Larger file size
- Limited to 160 ASCII codes per line (80 when converted)
- Requires 2-second gaps between program lines

**Recommended for**:
- Transferring programs to/from PC
- Creating editable backups
- Sharing programs with other users
- Documentation purposes

### When to Use Binary Format (no suffix)

**Advantages**:
- Faster transmission
- Smaller file size
- Exact representation of internal program
- No line length limitations

**Disadvantages**:
- Not human-readable
- Not compatible with text editors
- PC-1500 specific format

**Recommended for**:
- Quick backup/restore
- PC-1500 to PC-1500 transfer
- When speed is important
- Reserve (protected) programs

### ASCII Format Technical Details

**Line Format**:
- Each program line sent as ASCII text
- Line numbers as decimal ASCII digits
- Spaces padded before line number if < 5 digits
- CR (0x0D) or END code terminates each line
- 2-second all-MARK signal required between lines

**Example Transmission**:
```
   10 REM ***RS-232C***<CR>
   20 OUTSTAT 0<CR>
   30 CLS<CR>
   40 WAIT 0<CR>
```

**Character Set**: Standard ASCII (0x20-0x7E)
**Line Terminator**: CR (0x0D) or END code
**Intergap**: 2 seconds all-MARK (high signal)

---

## Baud Rate Configuration

### Standard Baud Rates

| Code | Baud Rate | Bit Time | Use Case |
|------|-----------|----------|----------|
| 0 | 75 | 13.3ms | Slow teletype |
| 1 | 150 | 6.7ms | Old terminals |
| 2 | 300 | 3.3ms | Acoustic modem |
| 3 | 600 | 1.7ms | Medium speed |
| 4 | 1200 | 0.83ms | Common modems |
| 5 | 2400 | 0.42ms | Fast modems |
| 6 | 4800 | 0.21ms | High speed |
| 7 | 9600 | 0.10ms | Maximum speed |

### Timing Considerations

At higher baud rates (4800+):
- Reduce BASIC program processing between reads
- Use INSTAT to check before RINKEY$
- Enable PROTOCOL for flow control
- Monitor OUTSTAT before transmitting

---

## Connector Pinouts

### RS-232C Serial Port (DB-25 or equivalent)

**DTE Mode (DTE 1)**:
| Pin | Signal | Direction | Description |
|-----|--------|-----------|-------------|
| 2 | TXD | Out | Transmit Data |
| 3 | RXD | In | Receive Data |
| 4 | RTS | Out | Request to Send |
| 5 | CTS | In | Clear to Send |
| 6 | DSR | In | Data Set Ready |
| 7 | GND | - | Signal Ground |
| 20 | DTR | Out | Data Terminal Ready |

**DCE Mode (DTE 0)**: TXD/RXD pins swapped

### Parallel Printer Port (Centronics-compatible)

| Pin | Signal | Direction | Description |
|-----|--------|-----------|-------------|
| 1-8 | D0-D7 | Out | Data bits |
| 9 | STROBE | Out | Data strobe |
| 10 | ACK | In | Acknowledge |
| 11 | BUSY | In | Printer busy |
| 12 | PE | In | Paper end |
| 13 | SELECT | In | Printer select |
| 19-30 | GND | - | Ground returns |

---

## Error Handling

### Common Error Codes

When using CE-158 commands, monitor ERN and ERL:

| Error | Description | Likely Cause |
|-------|-------------|--------------|
| 1 | Syntax Error | Invalid command format |
| 2 | Overflow | Buffer full |
| 3 | Device Error | Hardware not responding |
| 4 | Timeout | No response from device |
| 5 | Parity Error | Serial data corruption |

### Error Handling Example

```basic
10 ON ERROR GOTO 1000
20 SETCOM 4,0,8,1
30 SETDEV 1
40 PRINT "Ready"
50 A$=RINKEY$
60 IF A$<>"" THEN PRINT A$;
70 GOTO 50
1000 PRINT "Error ";ERN;" at line ";ERL
1010 SETDEV 0
1020 END
```

---

## Hardware Compatibility

### Supported Devices

**RS-232C Serial**:
- Modems (300-9600 baud)
- Serial printers
- Other computers
- Data acquisition systems
- GPS receivers
- Barcode scanners

**Parallel Printer**:
- Centronics-compatible printers
- Epson dot-matrix printers
- HP LaserJet (parallel models)
- Generic parallel printers

### Cable Requirements

- **RS-232C**: Standard DB-25 or DB-9 cable
- **Null modem**: Use for computer-to-computer
- **Parallel**: Centronics 36-pin printer cable
- **Maximum length**:
  - Serial: 15m (50ft) at 9600 baud
  - Parallel: 3m (10ft) recommended

---

## Troubleshooting

### Serial Communication Issues

**No Data Received**:
- Verify SETCOM parameters match remote device
- Check cable connections (TXD/RXD may need swapping)
- Try DTE 0 if DTE 1 fails
- Test with TERMINAL mode
- Verify baud rate compatibility

**Garbled Data**:
- Check parity setting
- Verify data bits (7 vs 8)
- Reduce baud rate
- Check cable quality
- Enable PROTOCOL for flow control

**Buffer Overflow**:
- Monitor INSTAT regularly
- Use PROTOCOL 1 or 2
- Reduce data transmission rate
- Increase processing speed in BASIC

### Parallel Printer Issues

**Printer Not Responding**:
- Check SETDEV 2 is active
- Verify cable connections
- Check printer power and online status
- Test with LPRINT "TEST"

**Missing Characters**:
- Monitor OUTSTAT before printing
- Add delays between LPRINT commands
- Check printer buffer status

---

## Performance Optimization

### Maximizing Throughput

1. **Use Direct Commands**: TRANSMIT faster than PRINT for serial
2. **Buffer Management**: Check INSTAT/OUTSTAT frequently
3. **Flow Control**: Enable PROTOCOL for reliable transfers
4. **Minimize Processing**: Keep loops tight during I/O
5. **Assembly Helpers**: CALL machine code for critical sections

### Example: Fast Data Transfer

```basic
10 REM Optimized transfer
20 SETCOM 7,0,8,1
30 PROTOCOL 1
40 SETDEV 1
50 FOR I=1 TO 1000
60 WAIT OUTSTAT>0
70 TRANSMIT D$(I)
80 NEXT I
```

---

## Technical Specifications Summary

| Specification | Value |
|---------------|-------|
| Interface Types | RS-232C serial, Centronics parallel |
| Baud Rates | 75-9600 bps (8 rates) |
| Data Formats | 7/8 data bits, 1/2 stop bits |
| Parity Options | None, Odd, Even |
| Flow Control | None, XON/XOFF, RTS/CTS |
| ROM Size | 8KB (2 × 4KB banks) |
| ROM Address | $8000-$8FFF |
| UART Address | $D200-$D203 |
| Port Address | $D000-$D00F |
| Configuration Registers | $7850-$785A |
| Working Registers | $79DD-$79FE, $7B08 |
| Token Range | 0xE857-0xE886 (11 commands) |
| Power Consumption | Via PC-1500 (minimal) |

---

## See Also

- [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md) - Core BASIC commands
- [CE-150 Graphics/Printer Reference](CE-150-Reference.md) - Plotter interface
- [Token Mapping Analysis](analysis/Token-Mapping-Analysis.md) - Token structure

---

## Appendix: Token Table Structure

### Token Table Organization

Both ROM banks contain identical token tables at $8000 with this structure:

```
B_TBL_8000_CMD_LST:
  CN1: Control_Nibble, Command_Name, Token_Word, Handler_Vector
  CN2: Control_Nibble, Command_Name, Token_Word, Handler_Vector
  ...
  CNn: End_Marker
```

**Control Nibble Encoding**:
- High nibble: Command properties (function vs statement)
- Low nibble: Link to next command in list
- Alphabetical organization with letter indices (LET_B, LET_C, etc.)

**Token Values** (CE-158 specific):
- 0xE857-0xE85A: Status/info functions (DEV$, COM$, INSTAT, RINKEY$)
- 0xE880-0xE886: Configuration/control (OUTSTAT, PROTOCOL, SETCOM, TERMINAL, DTE, TRANSMIT, SETDEV)

---

*Document Version 1.0 - Generated from ROM disassemblies and hardware specifications*
