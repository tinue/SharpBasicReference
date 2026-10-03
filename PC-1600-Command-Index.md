# Sharp PC-1600 Command Index

→ [README](README.md) · [BASIC Reference](PC-1600-BASIC-Reference.md) · [Error Code Reference](PC-1600-Error-Codes.md)

Every PC-1600 BASIC command, statement and function (Operation Manual, Appendix G), grouped by topic.
Each command links to its entry in the [command dictionary](PC-1600-BASIC-Reference.md#14-basic-command-dictionary)
of the PC-1600 BASIC Reference. Commands marked **(new vs PC-1500)** did not exist on the PC-1500;
**(MODE 1)** commands exist only for PC-1500 compatibility.

### Program control
| Command | Purpose |
|---------|---------|
| [ARUN](PC-1600-BASIC-Reference.md#arun) | Start program execution automatically at power-on |
| [AUTO](PC-1600-BASIC-Reference.md#auto) | Turn on automatic line numbering |
| [CHAIN](PC-1600-BASIC-Reference.md#chain--mode-1) | Load and execute a BASIC program from tape |
| [CONT](PC-1600-BASIC-Reference.md#cont) | Resume execution after STOP or BREAK |
| [DELETE](PC-1600-BASIC-Reference.md#delete) | Delete program lines |
| [END](PC-1600-BASIC-Reference.md#end) | Stop execution of the current program |
| [FOR..NEXT](PC-1600-BASIC-Reference.md#for--next) | Repeated execution of program lines |
| [GOSUB..RETURN](PC-1600-BASIC-Reference.md#gosub--return) | Jump to a subroutine |
| [GOTO](PC-1600-BASIC-Reference.md#goto) | Unconditional jump |
| [IF..THEN..ELSE](PC-1600-BASIC-Reference.md#if--then--else) | Conditional jump |
| [LIST](PC-1600-BASIC-Reference.md#list) | List a program to the screen |
| [NEW](PC-1600-BASIC-Reference.md#new) | Clear memory / assign space for machine code |
| [ON..GOSUB / ON..GOTO](PC-1600-BASIC-Reference.md#on--gosub--on--goto) | Multiple conditional jump |
| [PASS](PC-1600-BASIC-Reference.md#pass) | Set / clear a program password |
| [REM](PC-1600-BASIC-Reference.md#rem----) | Comment |
| [RENUM](PC-1600-BASIC-Reference.md#renum) | Renumber program lines |
| [RUN](PC-1600-BASIC-Reference.md#run) | Start execution of a program |
| [STOP](PC-1600-BASIC-Reference.md#stop) | Halt execution during debugging |
| [TRON / TROFF](PC-1600-BASIC-Reference.md#tron--troff) | Set / cancel program trace |

### Interrupts (event handling) — mostly **(new vs PC-1500)**
| Command | Purpose |
|---------|---------|
| [ADIN ON/OFF/STOP](PC-1600-BASIC-Reference.md#adin-on--off--stop--pc-1600) | Enable/disable analog interrupts |
| [AIN](PC-1600-BASIC-Reference.md#ain--pc-1600) | Current analog input value (0–255) **(new vs PC-1500)** |
| [BREAK ON/OFF](PC-1600-BASIC-Reference.md#break-on--off) | Enable/disable the BREAK key |
| [COMn ON/OFF/STOP](PC-1600-BASIC-Reference.md#comn-on--off--stop--pc-1600) | Enable/disable communication interrupts |
| [KEY ON/OFF/STOP](PC-1600-BASIC-Reference.md#key-on--off--stop--pc-1600) | Enable/disable function keys |
| [ON ADIN GOSUB](PC-1600-BASIC-Reference.md#on-adin-gosub--pc-1600) | Jump on an analog interrupt |
| [ON COMn GOSUB](PC-1600-BASIC-Reference.md#on-comn-gosub--pc-1600) | Jump on a serial-port interrupt |
| [ON ERROR GOTO](PC-1600-BASIC-Reference.md#on-error-goto) | Jump to error-processing routine |
| [ON PHONE GOSUB](PC-1600-BASIC-Reference.md#on-phone-gosub--pc-1600) | Jump on a telephone-modem input |
| [ON TIME$ GOSUB](PC-1600-BASIC-Reference.md#on-time-gosub--pc-1600) | Jump at a specified time |
| [ONKEY GOSUB](PC-1600-BASIC-Reference.md#on-key-gosub--pc-1600) | Jump on a function-key input |
| [PHONE ON/OFF/STOP](PC-1600-BASIC-Reference.md#phone-on--off--stop--pc-1600) | Enable/disable RS-232C interrupts |
| [RESUME](PC-1600-BASIC-Reference.md#resume) | Resume execution after an error routine |
| [RETI](PC-1600-BASIC-Reference.md#reti--pc-1600) | Return from an interrupt subroutine |
| [TIME$ ON/OFF/STOP](PC-1600-BASIC-Reference.md#time-on--off--stop--pc-1600) | Enable/disable clock interrupts |

### Input / output (screen & keyboard)
| Command | Purpose |
|---------|---------|
| [AREAD](PC-1600-BASIC-Reference.md#aread) | Read a variable from the screen |
| [CLS](PC-1600-BASIC-Reference.md#cls) | Clear the display screen |
| [CURSOR](PC-1600-BASIC-Reference.md#cursor) | Position the cursor on the screen |
| [GCURSOR](PC-1600-BASIC-Reference.md#gcursor) | Position the graphics cursor on the screen |
| [GPRINT](PC-1600-BASIC-Reference.md#gprint) | Draw bit-image graphics on the display |
| [INKEY$](PC-1600-BASIC-Reference.md#inkey) | Read a character from the keyboard buffer |
| [INPUT](PC-1600-BASIC-Reference.md#input) | Input data from the keyboard |
| [KBUFF$](PC-1600-BASIC-Reference.md#kbuff--pc-1600) | Write characters into the keyboard buffer |
| [KEYSTAT](PC-1600-BASIC-Reference.md#keystat--pc-1600) | Set key-repeat / key-click functions |
| [LINE](PC-1600-BASIC-Reference.md#line--pc-1600) | Draw a line between points on the screen **(new vs PC-1500)** |
| [PAUSE](PC-1600-BASIC-Reference.md#pause) | Display data on screen for a fixed time |
| [POINT](PC-1600-BASIC-Reference.md#point) | Return the dot setting at a screen point |
| [PRESET](PC-1600-BASIC-Reference.md#preset) | Reset a dot at a screen point |
| [PRINT](PC-1600-BASIC-Reference.md#print) | Output data to the display screen |
| [PRINT USING / USING](PC-1600-BASIC-Reference.md#print-using--using) | Formatted output to the display |
| [PSET](PC-1600-BASIC-Reference.md#pset) | Set/reset a dot at a screen point |
| [WAIT](PC-1600-BASIC-Reference.md#wait) | Set wait time after a PRINT statement |

### Data, variables, arrays
| Command | Purpose |
|---------|---------|
| [CLEAR](PC-1600-BASIC-Reference.md#clear) | Erase all variables in memory |
| [DATA](PC-1600-BASIC-Reference.md#data) | List data items for READ |
| [DIM](PC-1600-BASIC-Reference.md#dim) | Reserve memory for variables / arrays |
| [ERASE](PC-1600-BASIC-Reference.md#erase) | Erase specified variables and arrays |
| [LET](PC-1600-BASIC-Reference.md#let) | Assign a value to a variable |
| [READ..DATA](PC-1600-BASIC-Reference.md#read--data) | Read data into the program from DATA lines |
| [RESTORE](PC-1600-BASIC-Reference.md#restore) | Re-read data from DATA lines |

### Numeric functions
| Command | Purpose |
|---------|---------|
| [ABS](PC-1600-BASIC-Reference.md#abs) | Absolute value |
| [ACS](PC-1600-BASIC-Reference.md#acs) | Arc cosine |
| [ASN](PC-1600-BASIC-Reference.md#asn) | Arc sine |
| [ATN](PC-1600-BASIC-Reference.md#atn) | Arctangent |
| [COS](PC-1600-BASIC-Reference.md#cos) | Cosine |
| [DEG](PC-1600-BASIC-Reference.md#deg) | Convert deg/min/sec form to decimal degrees |
| [DEGREE](PC-1600-BASIC-Reference.md#degree) | Set DEGREE angular mode |
| [DMS](PC-1600-BASIC-Reference.md#dms) | Convert decimal degrees to deg/min/sec |
| [EXP](PC-1600-BASIC-Reference.md#exp) | e raised to the power X |
| [GRAD](PC-1600-BASIC-Reference.md#grad) | Set GRAD (gradient) angular mode |
| [INT](PC-1600-BASIC-Reference.md#int) | Truncate the decimal part |
| [LN](PC-1600-BASIC-Reference.md#ln) | Natural logarithm |
| [LOG](PC-1600-BASIC-Reference.md#log) | Common (base-10) logarithm |
| [MOD](PC-1600-BASIC-Reference.md#mod) | Remainder of division |
| [RADIAN](PC-1600-BASIC-Reference.md#radian) | Set RADIAN angular mode |
| [RANDOM](PC-1600-BASIC-Reference.md#random) | Seed random-number generation |
| [RND](PC-1600-BASIC-Reference.md#rnd) | Generate a random number |
| [SGN](PC-1600-BASIC-Reference.md#sgn) | Sign of an expression |
| [SIN](PC-1600-BASIC-Reference.md#sin) | Sine |
| [SQR](PC-1600-BASIC-Reference.md#sqr) | Square root |
| [TAN](PC-1600-BASIC-Reference.md#tan) | Tangent |

### String functions
| Command | Purpose |
|---------|---------|
| [ASC](PC-1600-BASIC-Reference.md#asc) | Character code of a string's first character |
| [CHR$](PC-1600-BASIC-Reference.md#chr) | Character from an ASCII code |
| [HEX$](PC-1600-BASIC-Reference.md#hex) | Hexadecimal string for a number |
| [INSTR](PC-1600-BASIC-Reference.md#instr) | Search for a character within a string |
| [LEFT$](PC-1600-BASIC-Reference.md#left) | Characters from the left end of a string |
| [LEN](PC-1600-BASIC-Reference.md#len) | Number of characters in a string |
| [MID$](PC-1600-BASIC-Reference.md#mid) | Substring from inside a string |
| [RIGHT$](PC-1600-BASIC-Reference.md#right) | Characters from the right end of a string |
| [STR$](PC-1600-BASIC-Reference.md#str) | Convert numeric data to a string |
| [VAL](PC-1600-BASIC-Reference.md#val) | Convert a numeric string to a value |

### Clock, alarm, power
| Command | Purpose |
|---------|---------|
| [ALARM$](PC-1600-BASIC-Reference.md#alarm--pc-1600) | Set alarm time and message |
| [DATE$](PC-1600-BASIC-Reference.md#date--pc-1600) | Return / set the date |
| [POWER](PC-1600-BASIC-Reference.md#power--pc-1600) | Set auto power-off |
| [TIME](PC-1600-BASIC-Reference.md#time) | Set / return the built-in clock time (numeric) |
| [TIME$](PC-1600-BASIC-Reference.md#time--pc-1600) | Return the clock time as a string |
| [WAKE$](PC-1600-BASIC-Reference.md#wake--pc-1600) | Set time / command string for auto power-on |

### Memory & machine language
| Command | Purpose |
|---------|---------|
| [BLOAD](PC-1600-BASIC-Reference.md#bload--pc-1600) | Load a machine-language program from disk or tape |
| [BSAVE](PC-1600-BASIC-Reference.md#bsave--pc-1600) | Save a machine-language program to disk or tape |
| [CALL](PC-1600-BASIC-Reference.md#call) | Call a machine-language program (PC-1600 addressing) |
| [INP](PC-1600-BASIC-Reference.md#inp--pc-1600) | Return data from a microprocessor port |
| [MEM](PC-1600-BASIC-Reference.md#mem) | Unused memory space in the user area |
| [OUT](PC-1600-BASIC-Reference.md#out--pc-1600) | Write data to a microprocessor port |
| [PEEK](PC-1600-BASIC-Reference.md#peek) | Return a memory byte **(PC-1600 mode)** |
| [POKE](PC-1600-BASIC-Reference.md#poke) | Write a memory byte **(PC-1600 mode)** |
| [STATUS](PC-1600-BASIC-Reference.md#status) | Amount of free space in memory areas |
| [XCALL](PC-1600-BASIC-Reference.md#xcall--lh-5803) | Call an LH-5803 (PC-1500) machine-language program **(PC-1500 addressing in MODE 1)** |
| [XPEEK / XPEEK#](PC-1600-BASIC-Reference.md#xpeek--xpeek--lh-5803) | Return a byte of the LH-5803 address space |
| [XPOKE / XPOKE#](PC-1600-BASIC-Reference.md#xpoke--xpoke--lh-5803) | Write bytes into the LH-5803 address space |

### Files, disk, RAM disk — mostly **(new vs PC-1500)**
| Command | Purpose |
|---------|---------|
| [CLOSE](PC-1600-BASIC-Reference.md#close) | Close a device file |
| [COPY](PC-1600-BASIC-Reference.md#copy--pc-1600) | Copy a file on disk or tape |
| [DSKF](PC-1600-BASIC-Reference.md#dskf--pc-1600) | Free space on a disk device |
| [EOF](PC-1600-BASIC-Reference.md#eof--pc-1600) | End-of-file indicator |
| [FILES](PC-1600-BASIC-Reference.md#files--pc-1600) | Show disk directory on screen |
| [INIT](PC-1600-BASIC-Reference.md#init--pc-1600) | Initialise a module / disk; set receive buffer size |
| [INPUT#](PC-1600-BASIC-Reference.md#input--pc-1600) | Read records from a file |
| [KILL](PC-1600-BASIC-Reference.md#kill--pc-1600) | Erase a file on disk |
| [LFILES](PC-1600-BASIC-Reference.md#lfiles--pc-1600) | Print disk directory to the printer |
| [LOAD / LOAD*](PC-1600-BASIC-Reference.md#load--load) | Load a file from disk or tape to memory |
| [LOC](PC-1600-BASIC-Reference.md#loc--pc-1600) | Number of records accessed in a file |
| [LOF](PC-1600-BASIC-Reference.md#lof--pc-1600) | Size of a file on disk |
| [MAXFILES](PC-1600-BASIC-Reference.md#maxfiles--pc-1600) | Set maximum number of open files for a program |
| [MERGE](PC-1600-BASIC-Reference.md#merge--mode-1) | Merge a program from tape into memory |
| [NAME](PC-1600-BASIC-Reference.md#name--pc-1600) | Rename a file on disk |
| [OPEN](PC-1600-BASIC-Reference.md#open--pc-1600) | Open a file for access |
| [PRINT# / PRINT# USING](PC-1600-BASIC-Reference.md#print--print-using--pc-1600) | Write data to a file |
| [SAVE / SAVE*](PC-1600-BASIC-Reference.md#save--save) | Save a BASIC program to disk or tape |
| [SET](PC-1600-BASIC-Reference.md#set--pc-1600) | Set write protection on a disk file |
| [TITLE](PC-1600-BASIC-Reference.md#title--pc-1600) | Select the computer's memory area |

### Cassette (MODE 1 tape commands)
| Command | Purpose |
|---------|---------|
| [CLOAD](PC-1600-BASIC-Reference.md#cload--mode-1) | Load a BASIC program from tape **(MODE 1)** |
| [CLOAD M](PC-1600-BASIC-Reference.md#cload-m--mode-1) | Load a machine-language program from tape **(MODE 1)** |
| [CLOAD?](PC-1600-BASIC-Reference.md#cload--mode-1-1) | Verify loading from tape **(MODE 1)** |
| [CSAVE](PC-1600-BASIC-Reference.md#csave--mode-1) | Save a BASIC program to tape **(MODE 1)** |
| [CSAVE M](PC-1600-BASIC-Reference.md#csave-m--mode-1) | Save a machine-language program to tape **(MODE 1)** |
| [RMT ON/OFF](PC-1600-BASIC-Reference.md#rmt-on--off) | Enable/disable tape remote control |

### Serial communications — **(new vs PC-1500)**
| Command | Purpose |
|---------|---------|
| [COM$](PC-1600-BASIC-Reference.md#com--pc-1600) | Return the communication parameters |
| [DEV$](PC-1600-BASIC-Reference.md#dev--pc-1600) | Return the current SETDEV output routing |
| [INSTAT](PC-1600-BASIC-Reference.md#instat--pc-1600) | Return control-signal states for a serial port |
| [LPRINT / LPRINT USING](PC-1600-BASIC-Reference.md#lprint--lprint-using) | Output data to the printer or a serial port |
| [OUTSTAT](PC-1600-BASIC-Reference.md#outstat--pc-1600) | Set control-signal states for serial ports |
| [PCONSOLE](PC-1600-BASIC-Reference.md#pconsole--pc-1600) | Set print format / EOL code for printer or ports |
| [PZONE](PC-1600-BASIC-Reference.md#pzone--pc-1600) | Set the print zone for printer or serial port |
| [RCVSTAT](PC-1600-BASIC-Reference.md#rcvstat--pc-1600) | Set receive protocol / timeout for a serial port |
| [RXD$](PC-1600-BASIC-Reference.md#rxd--pc-1600) | Return current data from a serial port |
| [SETCOM](PC-1600-BASIC-Reference.md#setcom--pc-1600) | Set communication protocol for the serial ports |
| [SETDEV](PC-1600-BASIC-Reference.md#setdev--pc-1600) | Select a serial port for output |
| [SNDBRK](PC-1600-BASIC-Reference.md#sndbrk--pc-1600) | Send break characters to a serial port |
| [SNDSTAT](PC-1600-BASIC-Reference.md#sndstat--pc-1600) | Set send protocol / timeout for a serial port |

### Printer / plotter (CE-1600P, or CE-150 in MODE 1)
| Command | Purpose |
|---------|---------|
| [COLOR](PC-1600-BASIC-Reference.md#color) | Set the pen colour |
| [CSIZE](PC-1600-BASIC-Reference.md#csize) | Set printer character size |
| [GLCURSOR](PC-1600-BASIC-Reference.md#glcursor) | Position the printer pen in graphics mode |
| [GRAPH](PC-1600-BASIC-Reference.md#graph) | Set the printer to graphics mode |
| [LCURSOR](PC-1600-BASIC-Reference.md#lcursor) | Move the printer pen to a position |
| [LF](PC-1600-BASIC-Reference.md#lf) | Feed paper in the printer |
| [LLINE](PC-1600-BASIC-Reference.md#lline) | Draw a line between points on the printer |
| [LLIST / LLIST*](PC-1600-BASIC-Reference.md#llist--llist) | List a program to the printer |
| [PAPER](PC-1600-BASIC-Reference.md#paper) | Set paper type and vertical print range |
| [PITCH](PC-1600-BASIC-Reference.md#pitch) | Set character pitch / line spacing |
| [RLINE](PC-1600-BASIC-Reference.md#rline) | Draw a line in relative coordinates on the printer |
| [ROTATE](PC-1600-BASIC-Reference.md#rotate) | Set print orientation / print-head travel |
| [SORGN](PC-1600-BASIC-Reference.md#sorgn) | Set the current pen position as origin |
| [TAB](PC-1600-BASIC-Reference.md#tab) | Move the printer pen to a column |
| [TEST](PC-1600-BASIC-Reference.md#test) | Run a printer self-test |
| [TEXT](PC-1600-BASIC-Reference.md#text) | Set the printer to text mode |

### Modem / misc
| Command | Purpose |
|---------|---------|
| [BEEP](PC-1600-BASIC-Reference.md#beep) | Generate sound through the internal speaker |
| [BEEP ON/OFF](PC-1600-BASIC-Reference.md#beep-on--off) | Enable/disable sound generation |
| [ERL](PC-1600-BASIC-Reference.md#erl) | Line number of the last error |
| [ERN](PC-1600-BASIC-Reference.md#ern) | Error code of the last error |
| [LOCK / UNLOCK](PC-1600-BASIC-Reference.md#lock--unlock) | Enable/disable the MODE key |
| [MODE](PC-1600-BASIC-Reference.md#mode) | Select screen mode for PC-1500 compatibility |
