# Sharp PC-1500 Command Index

→ [README](README.md) · [BASIC Reference](PC-1500-BASIC-Reference.md) · [Error Code Reference](PC-1500-Error-Codes.md)

Every PC-1500 BASIC command, statement and function, grouped by topic, including the commands added by the
**CE-150** printer/plotter/cassette interface and the **CE-158** RS-232C/parallel interface.
Each command links to its entry in the [PC-1500 BASIC Reference](PC-1500-BASIC-Reference.md).

### Program control
| Command | Purpose |
|---------|---------|
| [ARUN](PC-1500-BASIC-Reference.md#arun) | Start the program automatically at power-on |
| [CONT](PC-1500-BASIC-Reference.md#cont) | Resume execution after STOP or BREAK |
| [END](PC-1500-BASIC-Reference.md#end) | End program execution |
| [FOR / NEXT](PC-1500-BASIC-Reference.md#for--next) | Counted loop |
| [GOSUB](PC-1500-BASIC-Reference.md#gosub) | Jump to a subroutine |
| [GOTO](PC-1500-BASIC-Reference.md#goto) | Jump to a line number or label |
| [IF / THEN](PC-1500-BASIC-Reference.md#if--then) | Conditional execution |
| [LIST](PC-1500-BASIC-Reference.md#list) | List program lines to the display |
| [NEW](PC-1500-BASIC-Reference.md#new) | Erase the program and all variables |
| [ON GOTO / ON GOSUB](PC-1500-BASIC-Reference.md#on-goto--on-gosub) | Multi-way jump on an expression value |
| [RETURN](PC-1500-BASIC-Reference.md#return) | Return from a subroutine |
| [RUN](PC-1500-BASIC-Reference.md#run) | Start a program (optionally at a line or label) |
| [STOP](PC-1500-BASIC-Reference.md#stop) | Halt execution (resumable with CONT) |
| [TRON / TROFF](PC-1500-BASIC-Reference.md#tron--troff) | Set / cancel program trace |

### Error handling
| Command | Purpose |
|---------|---------|
| [ERL](PC-1500-BASIC-Reference.md#erl) | Line number of the last error |
| [ERN](PC-1500-BASIC-Reference.md#ern) | Error code of the last error |
| [ERROR](PC-1500-BASIC-Reference.md#error) | Raise an error deliberately |
| [ON ERROR GOTO](PC-1500-BASIC-Reference.md#on-error-goto) | Jump to an error-handling routine |

### Input / output (screen & keyboard)
| Command | Purpose |
|---------|---------|
| [CLS](PC-1500-BASIC-Reference.md#cls) | Clear the display |
| [CURSOR](PC-1500-BASIC-Reference.md#cursor) | Position the text cursor on the display |
| [INKEY$](PC-1500-BASIC-Reference.md#inkey) | Read the key currently pressed (non-blocking) |
| [INPUT](PC-1500-BASIC-Reference.md#input) | Input data from the keyboard |
| [PAUSE](PC-1500-BASIC-Reference.md#pause) | Display data for about 0.85 s, then continue |
| [PRINT](PC-1500-BASIC-Reference.md#print) | Output data to the display |
| [USING](PC-1500-BASIC-Reference.md#using) | Set the output format for numbers and strings |
| [WAIT](PC-1500-BASIC-Reference.md#wait) | Set display time after PRINT |
| [ZONE](PC-1500-BASIC-Reference.md#zone) | Set the column width for comma-separated PRINT items |

### Display graphics
| Command | Purpose |
|---------|---------|
| [GCURSOR](PC-1500-BASIC-Reference.md#gcursor) | Position the graphics cursor on the display |
| [GPRINT](PC-1500-BASIC-Reference.md#gprint) | Draw bit-image graphics on the display |
| [POINT](PC-1500-BASIC-Reference.md#point) | Return the dot pattern of a display column |

### Numeric functions
| Command | Purpose |
|---------|---------|
| [ABS](PC-1500-BASIC-Reference.md#abs) | Absolute value |
| [ASN / ACS / ATN](PC-1500-BASIC-Reference.md#asn-acs-atn) | Arc sine, arc cosine, arctangent |
| [DEG](PC-1500-BASIC-Reference.md#deg) | Convert deg/min/sec form to decimal degrees |
| [DEGREE](PC-1500-BASIC-Reference.md#degree) | Set DEGREE angular mode |
| [DMS](PC-1500-BASIC-Reference.md#dms) | Convert decimal degrees to deg/min/sec |
| [EXP](PC-1500-BASIC-Reference.md#exp) | e raised to the power X |
| [GRAD](PC-1500-BASIC-Reference.md#grad) | Set GRAD (gradient) angular mode |
| [INT](PC-1500-BASIC-Reference.md#int) | Largest integer not greater than the value |
| [LN](PC-1500-BASIC-Reference.md#ln) | Natural logarithm |
| [LOG](PC-1500-BASIC-Reference.md#log) | Common (base-10) logarithm |
| [PI](PC-1500-BASIC-Reference.md#pi) | The constant π |
| [RADIAN](PC-1500-BASIC-Reference.md#radian) | Set RADIAN angular mode |
| [RANDOM](PC-1500-BASIC-Reference.md#random) | Seed random-number generation |
| [RND](PC-1500-BASIC-Reference.md#rnd) | Generate a random number |
| [SGN](PC-1500-BASIC-Reference.md#sgn) | Sign of an expression |
| [SIN / COS / TAN](PC-1500-BASIC-Reference.md#sin-cos-tan) | Sine, cosine, tangent |
| [SQR](PC-1500-BASIC-Reference.md#sqr) | Square root |
| [`^`](PC-1500-BASIC-Reference.md#exponentiation) | Exponentiation (x to the power y) |

### String functions
| Command | Purpose |
|---------|---------|
| [ASC](PC-1500-BASIC-Reference.md#asc) | Character code of a string's first character |
| [CHR$](PC-1500-BASIC-Reference.md#chr) | Character from a character code |
| [LEFT$](PC-1500-BASIC-Reference.md#left) | Characters from the left end of a string |
| [LEN](PC-1500-BASIC-Reference.md#len) | Number of characters in a string |
| [MID$](PC-1500-BASIC-Reference.md#mid) | Substring from inside a string |
| [RIGHT$](PC-1500-BASIC-Reference.md#right) | Characters from the right end of a string |
| [SPACE$](PC-1500-BASIC-Reference.md#space) | String of n spaces |
| [STR$](PC-1500-BASIC-Reference.md#str) | Convert numeric data to a string |
| [String comparison](PC-1500-BASIC-Reference.md#string-comparison) | Compare strings with relational operators |
| [String concatenation](PC-1500-BASIC-Reference.md#string-concatenation) | Join strings with + |
| [VAL](PC-1500-BASIC-Reference.md#val) | Convert a numeric string to a value |

### Data, variables, arrays
| Command | Purpose |
|---------|---------|
| [CLEAR](PC-1500-BASIC-Reference.md#clear) | Erase all variables and arrays |
| [DATA](PC-1500-BASIC-Reference.md#data) | List data items for READ |
| [DIM](PC-1500-BASIC-Reference.md#dim) | Declare arrays |
| [LET](PC-1500-BASIC-Reference.md#let) | Assign a value to a variable |
| [READ](PC-1500-BASIC-Reference.md#read) | Read data from DATA lines |
| [RESTORE](PC-1500-BASIC-Reference.md#restore) | Reset the DATA pointer |

### Clock, sound, power, memory
| Command | Purpose |
|---------|---------|
| [BEEP](PC-1500-BASIC-Reference.md#beep) | Sound the built-in speaker |
| [LOCK / UNLOCK](PC-1500-BASIC-Reference.md#lock--unlock) | Disable / enable the MODE key |
| [MEM](PC-1500-BASIC-Reference.md#mem) | Free program steps |
| [OFF](PC-1500-BASIC-Reference.md#off) | Power off |
| [STATUS](PC-1500-BASIC-Reference.md#status) | Return memory and execution information |
| [TIME](PC-1500-BASIC-Reference.md#time) | Set / return the built-in clock |

### DEF key and reserve keys
| Command | Purpose |
|---------|---------|
| [AREAD](PC-1500-BASIC-Reference.md#aread) | Read the value on the display into a variable at DEF-key start |
| [Labels](PC-1500-BASIC-Reference.md#labels) | Label a line for DEF-key start and jumps |
| [Reserve keys](PC-1500-BASIC-Reference.md#reserve-keys) | Assign phrases to the reserve keys (RESERVE mode) |

### Logic and bitwise operations
| Command | Purpose |
|---------|---------|
| [AND / OR / NOT](PC-1500-BASIC-Reference.md#and-or-not) | Logical / bitwise operators |

### Memory & machine language
| Command | Purpose |
|---------|---------|
| [CALL](PC-1500-BASIC-Reference.md#call) | Call a machine-language routine |
| [PEEK / POKE](PC-1500-BASIC-Reference.md#peek--poke) | Read / write a memory byte |
| [PEEK# / POKE#](PC-1500-BASIC-Reference.md#peek--poke-1) | Read / write a byte in the hardware I/O area |

### Printer / plotter (CE-150)
| Command | Purpose |
|---------|---------|
| [COLOR](PC-1500-BASIC-Reference.md#color) | Select the pen colour |
| [CSIZE](PC-1500-BASIC-Reference.md#csize--character-size) | Set printer character size |
| [GLCURSOR](PC-1500-BASIC-Reference.md#glcursor--graphics-cursor) | Move the pen to (x, y) in graphics mode |
| [GRAPH](PC-1500-BASIC-Reference.md#graph) | Set the printer to graphics mode |
| [LCURSOR](PC-1500-BASIC-Reference.md#lcursor--line-cursor) | Move the pen to a column |
| [LF](PC-1500-BASIC-Reference.md#lf--line-feed) | Feed paper forward or backward |
| [LINE](PC-1500-BASIC-Reference.md#line) | Draw lines / boxes between absolute points |
| [LLIST](PC-1500-BASIC-Reference.md#llist) | List a program to the printer |
| [LPRINT](PC-1500-BASIC-Reference.md#lprint) | Output data to the printer |
| [RLINE](PC-1500-BASIC-Reference.md#rline--relative-line) | Draw lines / boxes in relative coordinates |
| [ROTATE](PC-1500-BASIC-Reference.md#rotate) | Set print direction |
| [SORGN](PC-1500-BASIC-Reference.md#sorgn--set-origin) | Set the current pen position as origin |
| [TAB](PC-1500-BASIC-Reference.md#tab) | Move the pen to a column within LPRINT |
| [TEST](PC-1500-BASIC-Reference.md#test) | Draw a pen test pattern |
| [TEXT](PC-1500-BASIC-Reference.md#text) | Set the printer to text mode |

### Cassette (CE-150)
| Command | Purpose |
|---------|---------|
| [CHAIN](PC-1500-BASIC-Reference.md#chain) | Load and run a program from tape |
| [CLOAD](PC-1500-BASIC-Reference.md#cload--cassette-load) | Load a program from tape |
| [CLOAD M](PC-1500-BASIC-Reference.md#cload-m--load-machine-language) | Load a memory block from tape |
| [CLOAD?](PC-1500-BASIC-Reference.md#cload--cassette-load-verify) | Verify a tape against memory |
| [CSAVE](PC-1500-BASIC-Reference.md#csave--cassette-save) | Save a program to tape |
| [CSAVE M](PC-1500-BASIC-Reference.md#csave-m--save-machine-language) | Save a memory block to tape |
| [INPUT#](PC-1500-BASIC-Reference.md#input--input-variables-from-tape) | Load variables from tape |
| [MERGE](PC-1500-BASIC-Reference.md#merge) | Merge a program from tape into memory |
| [PRINT#](PC-1500-BASIC-Reference.md#print--print-variables-to-tape) | Save variables to tape |
| [RMT ON / RMT OFF](PC-1500-BASIC-Reference.md#rmt-on--rmt-off) | Control the remote terminal of a second recorder |

### Serial configuration (CE-158)
| Command | Purpose |
|---------|---------|
| [CONSOLE](PC-1500-BASIC-Reference.md#console) | Set line width and end code for RS-232C output |
| [OUTSTAT](PC-1500-BASIC-Reference.md#outstat) | Set the RTS / DTR handshake lines |
| [SETCOM](PC-1500-BASIC-Reference.md#setcom) | Set baud rate, word length, parity, stop bits |
| [SETDEV](PC-1500-BASIC-Reference.md#setdev) | Route BASIC I/O commands to the RS-232C port |
| [ZONE (CE-158)](PC-1500-BASIC-Reference.md#zone-1) | Set the column width for comma-separated LPRINT items |

### Serial I/O (CE-158)
| Command | Purpose |
|---------|---------|
| [FEED](PC-1500-BASIC-Reference.md#feed) | Send end codes |
| [INPUT (RS-232C)](PC-1500-BASIC-Reference.md#input-rs-232c) | Input data from the RS-232C port |
| [INPUT#-8,](PC-1500-BASIC-Reference.md#input-8) | Input from RS-232C without SETDEV |
| [INPUT%](PC-1500-BASIC-Reference.md#input-1) | Receive data into a character array |
| [INPUTS](PC-1500-BASIC-Reference.md#inputs) | Input data from RS-232C without tokenising |
| [INSTAT](PC-1500-BASIC-Reference.md#instat) | Return the RS-232C handshake line states |
| [LLIST (RS-232C)](PC-1500-BASIC-Reference.md#llist-rs-232c) | List a program to the RS-232C port |
| [LPRINT (RS-232C)](PC-1500-BASIC-Reference.md#lprint-rs-232c) | Output printer data to the RS-232C port |
| [PRINT (RS-232C)](PC-1500-BASIC-Reference.md#print-rs-232c) | Output data to the RS-232C port |
| [PRINT#-8,](PC-1500-BASIC-Reference.md#print-8) | Output to RS-232C without SETDEV |
| [RINKEY$](PC-1500-BASIC-Reference.md#rinkey) | Return the last byte received (non-blocking) |
| [TRANSMIT](PC-1500-BASIC-Reference.md#transmit) | Send a long space (break) signal |

### Program transfer via RS-232C (CE-158)
| Command | Purpose |
|---------|---------|
| [CLOAD (RS-232C)](PC-1500-BASIC-Reference.md#cload-rs-232c) | Receive a program in internal code |
| [CLOADa](PC-1500-BASIC-Reference.md#cloada) | Receive an ASCII program |
| [CLOADr](PC-1500-BASIC-Reference.md#cloadr) | Receive a reserve program |
| [CSAVE (RS-232C)](PC-1500-BASIC-Reference.md#csave-rs-232c) | Send a program in internal code |
| [CSAVEa](PC-1500-BASIC-Reference.md#csavea) | Send a program as ASCII text |
| [CSAVEr](PC-1500-BASIC-Reference.md#csaver) | Send the reserve program |
| [INPUT# (RS-232C)](PC-1500-BASIC-Reference.md#input-2) | Receive variables in internal code |
| [MERGE (RS-232C)](PC-1500-BASIC-Reference.md#merge-rs-232c) | Merge a received program (internal code) |
| [MERGEa](PC-1500-BASIC-Reference.md#mergea) | Merge a received ASCII program |
| [PRINT# (RS-232C)](PC-1500-BASIC-Reference.md#print-1) | Send variables in internal code |

### Terminal mode (CE-158)
| Command | Purpose |
|---------|---------|
| [DTE](PC-1500-BASIC-Reference.md#dte) | Enter terminal mode at 300 baud, 7E1 |
| [TERMINAL](PC-1500-BASIC-Reference.md#terminal) | Enter terminal mode with the SETCOM parameters |

### Parallel interface (CE-158)
| Command | Purpose |
|---------|---------|
| [OPN](PC-1500-BASIC-Reference.md#opn) | Assign the output port (parallel / RS-232C) |
| [PRINT#-9,](PC-1500-BASIC-Reference.md#print-9) | Output to the parallel port |
