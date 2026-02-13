# Sharp PC-1500 BASIC Reference Documentation

Comprehensive reference documentation for the Sharp PC-1500 pocket computer BASIC programming language and peripheral interfaces.

## Overview

This documentation project provides detailed technical reference materials for the Sharp PC-1500 BASIC interpreter, including all 122 built-in commands, memory maps, token structures, and hardware interfaces for the CE-150 plotter/printer and CE-158 communications peripherals.

The documentation is extracted from ROM disassemblies, source code analysis, and official technical manuals, providing developers and enthusiasts with accurate, comprehensive reference materials for programming and understanding the PC-1500 system.

## Documentation Files

### Core References

- **[PC-1500-BASIC-Reference.md](docs/PC-1500-BASIC-Reference.md)** (1,605 lines)
  - Complete reference for all 122 BASIC commands
  - Quick reference table with tokens, abbreviations, and handlers
  - Commands organized by functional categories
  - Memory maps and system registers
  - Appendices with complete token tables and cross-references

- **[CE-150-Reference.md](docs/CE-150-Reference.md)** (544 lines)
  - CE-150 4-color plotter/printer peripheral
  - Graphics and text mode commands
  - Memory maps and hardware registers
  - Programming examples and troubleshooting
  - System function addresses

- **[CE-158-Reference.md](docs/CE-158-Reference.md)** (719 lines)
  - CE-158 RS-232C serial and parallel printer interface
  - Serial communication configuration
  - Dual ROM bank architecture
  - UART hardware and I/O ports
  - Baud rate tables and connector pinouts

### Analysis

- **[analysis/Token-Mapping-Analysis.md](docs/analysis/Token-Mapping-Analysis.md)** (654 lines)
  - Token system architecture and classification
  - Device-specific token allocation
  - ROM dispatcher tables structure
  - Handler address distribution analysis
  - Complete cross-reference tables

## Quick Start

### Finding a Command

1. **By Name**: Check the alphabetical quick reference table in [PC-1500-BASIC-Reference.md](docs/PC-1500-BASIC-Reference.md#quick-reference-table)
2. **By Category**: Browse functional categories (Program Control, I/O, Math, String, Graphics, etc.)
3. **By Token**: See [Appendix A: Token Table](docs/PC-1500-BASIC-Reference.md#appendix-a-token-table-sorted-by-token-value)
4. **By Abbreviation**: See [Appendix B: Abbreviation Quick Reference](docs/PC-1500-BASIC-Reference.md#appendix-b-abbreviation-quick-reference)

### Understanding Device Requirements

Commands are marked with their hardware requirements:
- **PC-1500**: Base system commands (no peripherals needed)
- **CE-150**: Requires CE-150 plotter/printer
- **CE-158**: Requires CE-158 communications interface
- **Shared**: Enhanced when peripherals present, basic function without

### Finding Memory Addresses

- **System RAM**: [PC-1500 Memory Map](docs/PC-1500-BASIC-Reference.md#appendix-d-memory-map-reference)
- **CE-150 Registers**: [CE-150 Memory Map](docs/CE-150-Reference.md#memory-map)
- **CE-158 Registers**: [CE-158 Memory Map](docs/CE-158-Reference.md#memory-map)

### Token Analysis

For understanding the token system architecture:
- [Token Range Classification](docs/analysis/Token-Mapping-Analysis.md#token-range-classification)
- [Device-Specific Allocation](docs/analysis/Token-Mapping-Analysis.md#device-specific-token-allocation)
- [Complete Token Cross-Reference](docs/analysis/Token-Mapping-Analysis.md#cross-reference-tables)

## Key Statistics

| Metric | Value |
|--------|-------|
| Total Commands | 122 |
| PC-1500 Core Commands | 97 |
| CE-150 Specific | 14 |
| CE-158 Specific | 11 |
| Token Range | 0xE680-0xF1B6 |
| Total Documentation | 3,744 lines |
| Documentation Files | 5 |

## Command Categories

### PC-1500 Core (97 commands)

- **Program Control** (17): GOTO, GOSUB, IF, THEN, FOR, NEXT, ON, RETURN, RUN, END, STOP, CONT, PAUSE, NEW, REM, ARUN, AREAD
- **Input/Output** (14): INPUT, PRINT, CURSOR, GCURSOR, GPRINT, CLS, OPN, OFF, STATUS, USING, ZONE, TAB, INKEY$, AREAD
- **Mathematical Functions** (24): ABS, SIN, COS, TAN, ATN, ASN, ACS, EXP, LOG, LN, SQR, INT, SGN, RND, RANDOM, PI, DEG, DMS
- **String Functions** (10): CHR$, ASC, STR$, VAL, LEFT$, RIGHT$, MID$, LEN, SPACE$
- **Data & Variables** (6): DATA, READ, RESTORE, DIM, LET, CLEAR
- **Memory & System** (8): PEEK, POKE, PEEK#, POKE#, MEM, CALL, LOCK, UNLOCK, TIME, WAIT, BEEP
- **File Operations** (6): CLOAD, CSAVE, MERGE, CHAIN, LIST, LLIST
- **Configuration** (3): DEGREE, RADIAN, GRAD
- **Debugging** (5): TRON, TROFF, ERROR, ERL, ERN, BREAK
- **Logical Operators** (3): AND, OR, NOT
- **Keywords** (3): STEP, THEN, TO

### CE-150 Graphics/Printer (14 commands)

- **Graphics Mode**: GRAPH, TEXT, GLCURSOR, LCURSOR, LINE, RLINE, POINT, TEST
- **Display Control**: COLOR, CSIZE, ROTATE, SORGN
- **Printing**: LPRINT, LLIST, LF, FEED
- **Cassette**: RMT, CHAIN

### CE-158 Communications (11 commands + format variants)

- **Configuration**: SETCOM, SETDEV, PROTOCOL, DTE, CONSOLE
- **Status/Info**: DEV$, COM$, INSTAT, OUTSTAT
- **Data Transfer**: RINKEY$, TRANSMIT, TERMINAL
- **Program Transfer**: CSAVE/CSAVEa/CSAVEr, CLOAD/CLOADa/CLOADr, MERGE/MERGEa
- **File I/O**: PRINT#, INPUT#

**Note**: The 'a' suffix (CSAVEa, CLOADa, MERGEa) enables ASCII format for human-readable program transfer and editing on external computers. The 'r' suffix saves/loads reserve (protected) programs.

## Source Materials

This documentation was compiled from:

- **SharpPc1500BasicKeywords.java** - Primary token definitions (122 keywords)
- **PC-1500_ROM-A0x.lh5801.asm** - PC-1500 ROM disassembly (14,366 lines)
- **CE-158_ROM_LOW.lh5801.asm** - CE-158 Low Bank ROM disassembly
- **CE-158_ROM_HIGH.lh5801.asm** - CE-158 High Bank ROM disassembly
- **PC-1500.lib** - PC-1500 memory map definitions (151 lines)
- **CE-150.lib** - CE-150 memory map and function addresses (81 lines)
- **CE-158.lib** - CE-158 memory map and hardware registers (40 lines)
- **Sharp PC-1500 Technical Reference Manual** - Official abbreviations (page 115)
- **Sharp CE-158 Instruction Manual** - ASCII transfer format documentation

## Standards and Conventions

### Markdown Format

- **Headers**: # for title, ## for sections, ### for subsections, #### for commands
- **Token Values**: Code format with 0x prefix (e.g., `0xF192`)
- **ROM Addresses**: Code format with $ prefix (e.g., `$C515`)
- **Command Names**: Bold in headers (e.g., **GOTO**)
- **Tables**: GitHub-flavored markdown with proper alignment
- **Code Blocks**: Language-specific formatting (```basic for BASIC syntax)
- **Internal Links**: Markdown anchor format (#section-name)

### Abbreviations

Abbreviations follow the Sharp PC-1500 Technical Reference Manual (page 115):
- Single-letter for common commands (G for GOTO, P for PRINT)
- Shortest unique prefix for others
- No abbreviation for 3-letter commands (ASC, COS, INT)

## Use Cases

### For Developers

- **BASIC Programming**: Complete command reference with syntax and examples
- **System Programming**: Memory maps, registers, and handler addresses for machine code
- **Peripheral Integration**: Hardware interface documentation for CE-150 and CE-158
- **Emulator Development**: Token tables and ROM dispatcher architecture
- **Tool Development**: Token mappings for syntax highlighters and IDE plugins

### For Enthusiasts

- **Learning BASIC**: Organized by category with clear explanations
- **Hardware Understanding**: Memory layout and peripheral functionality
- **Troubleshooting**: Error codes, hardware specs, and common issues
- **Historical Research**: Token evolution and ROM architecture

### For Collectors

- **Device Identification**: Token ranges identify which peripherals are needed
- **Functionality Assessment**: Complete feature list for each configuration
- **Restoration**: Memory maps and hardware specifications

## Contributing

This documentation is based on reverse engineering and analysis. If you find errors or have additional information:

1. **Corrections**: Submit issues with specific references to source files
2. **Additions**: Additional ROM variants, undocumented features, or examples
3. **Clarifications**: Improved explanations or additional context

## Version History

- **Version 1.0** (2025-02-13)
  - Initial release
  - Complete documentation of 122 commands
  - Memory maps for PC-1500, CE-150, CE-158
  - Token mapping analysis
  - Cross-reference tables
  - ASCII format transfer documentation (CSAVEa, CLOADa, MERGEa)

## Related Projects

- **SharpBasicPlugin**: Java-based BASIC keyword reference and IDE support
- **PC-1500 Emulators**: Software emulation of PC-1500 hardware
- **ROM Disassembly Projects**: Reverse engineering of PC-1500 firmware

## License

This documentation is released under the MIT License. See [docs/LICENSE](docs/LICENSE) for details.

The Sharp PC-1500, CE-150, CE-158, and related trademarks are property of Sharp Corporation. This is an independent documentation project and is not affiliated with or endorsed by Sharp Corporation.

## Acknowledgments

- Sharp Corporation for creating the PC-1500 system
- Reverse engineering community for ROM disassemblies
- Technical Reference Manual authors
- Contributors to the SharpBasicPlugin project

---

**Last Updated**: 2025-02-13
**Documentation Version**: 1.0
**Total Lines**: 3,744
**Coverage**: 122/122 commands (100%)
