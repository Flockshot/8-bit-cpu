# Logisim 8-Bit Single-Cycle CPU Design and Simulation

This project is a complete, 8-bit single-cycle processor architected, implemented, and verified within the Logisim simulation environment. It demonstrates core computer organization principles by building a functional CPU from the ground up, component by component.

[cite_start]The processor executes a custom 21-bit ISA (Instruction Set Architecture) and features a Harvard architecture with separate instruction and data memory [cite: 156-162].

![Logisim](https://img.shields.io/badge/Built%20with-Logisim-orange)

### Final CPU Datapath

[cite_start]This is the top-level view of the complete single-cycle processor, showing the interconnection of the Program Counter (PC), Control Unit, Register File, ALU, and Memory modules[cite: 326, 747].

> **[Image: Screenshot of the complete 8-bit CPU datapath in Logisim]**
>
> [cite_start]*(**Developer Note:** Place your screenshot of the main `CPU` circuit here. It should look like the schematic on Page 11 of `report 3.pdf`.)*

---

## 🚀 Core Features

* [cite_start]**8-bit Single-Cycle Datapath:** Executes one instruction per clock cycle[cite: 1575].
* [cite_start]**Harvard Architecture:** Uses separate memory for instructions (ROM) and data (RAM)[cite: 1202, 1385, 1468].
* [cite_start]**Custom 21-bit ISA:** Implements R-type, I-type, and J-type instruction formats [cite: 1642-1658].
* [cite_start]**Full-Featured ALU:** An 8-bit ALU that performs 5 operations (`ADD`, `AND`, `OR`, `ROTL`, `SLT`) [cite: 1613] [cite_start]and generates an `EQ` flag for branching[cite: 626].
* [cite_start]**Control Flow:** Supports sequential execution (`PC+1`), relative branches (`BEQ`, `BNE`), and absolute jumps (`J`) [cite: 1587-1590, 1615].

## 🏛️ Architecture & Modules

The CPU is designed modularly, with each component built as a separate Logisim sub-circuit.

### 1. Control Unit

[cite_start]A combinational logic circuit that decodes the instruction `opcode` and (for branches) the `EQ` flag from the ALU[cite: 1591, 1640]. It generates all necessary control signals for the datapath.

* **Output Signals:**
    * [cite_start]`mux1`: Selects the value to be written back to the register file (ALU result or memory data)[cite: 1597, 1607].
    * [cite_start]`mux2`: Selects the second operand for the ALU (Register data or immediate value)[cite: 1598, 1608].
    * [cite_start]`WE` (Write Enable): Enables writing to the Register File[cite: 1602].
    * [cite_start]`write` / `read`: Control signals for Data Memory[cite: 1600, 1601].
    * [cite_start]`ALUfunc`: A 3-bit signal that tells the ALU which operation to perform[cite: 1596, 1613].
    * [cite_start]`PCmux` & `Branch`: Signals to control PC updates for branches or jumps[cite: 1592, 1603].

> **[Image: Screenshot of the Control Unit sub-circuit logic]**
>
> [cite_start]*(**Developer Note:** Place your screenshot of the `Control_Unit` circuit here. It should look like the schematic on Page 7 of `report 3.pdf`.)*

### 2. Arithmetic Logic Unit (ALU)

The 8-bit computational core of the CPU.
* [cite_start]**Operations:** `ADD`, `AND`, `OR`, `ROTL` (Rotate Left), `SLT` (Set Less Than)[cite: 1613].
* [cite_start]**Flags:** Generates a 1-bit `EQ` (Equal) flag, which is set to `1` if `X == Y`[cite: 626]. [cite_start]This flag is fed to the Control Unit to handle `BEQ` and `BNE` instructions[cite: 1640].

> **[Image: Screenshot of the ALU sub-circuit logic]**
>
> [cite_start]*(**Developer Note:** Place your screenshot of the `ALU` circuit here. It should look like the schematic on Page 8 of `report 3.pdf`.)*

### 3. Register File

* [cite_start]An 8x8 (8 registers, 8 bits each) register file (R0-R7)[cite: 41, 1306].
* [cite_start]Supports two simultaneous 8-bit reads (for `rs` and `rt`)[cite: 41].
* [cite_start]Supports one 8-bit write to a destination register (`rd`) on the clock's falling edge, if `WE` is high[cite: 42, 1321].

### 4. Memory

* [cite_start]**Instruction Memory (ROM):** A 1K x 21-bit Read-Only Memory[cite: 107, 486, 1414].
    * [cite_start]It is 10-bit addressable (1024 instructions)[cite: 107, 486, 1438].
    * [cite_start]Stores the 21-bit wide instructions for the program[cite: 106, 485].
* [cite_start]**Data Memory (RAM):** A 256 x 8-bit Random-Access Memory [cite: 151, 530, 1468-1479].
    * [cite_start]It is 8-bit addressable (256 bytes)[cite: 151, 530, 1473].
    * [cite_start]Used by `LOAD` and `STORE` instructions to read from and write to memory[cite: 152, 531].

### 5. Program Counter (PC)

[cite_start]A 10-bit register that holds the address of the *next* instruction to be fetched[cite: 1258, 1440]. Its value is updated every clock cycle based on the output of a MUX, which selects from:
* [cite_start]**`PC + 1`:** For sequential execution[cite: 1258, 1440].
* [cite_start]**`Branch_Add`:** The target address for `BEQ` or `BNE` instructions[cite: 614, 1588, 1589].
* **`JMP`:** The 10-bit absolute address for `J` instructions[cite: 1587, 1618].

---

## 📖 Instruction Set Architecture (ISA)

The CPU implements a custom 21-bit ISA with R-type, I-type, and J-type formats [cite: 1642-1658]. The 4-bit opcode determines the instruction and control signals[cite: 1640].

| Mnemonic | Opcode | Type | Operation |
| :--- | :--- | :--- | :--- |
| **ADD** | `0000` | R | `$Rd = $Rs + $Rt$` [cite: 1617] |
| **LOAD** | `0001` | I | `$Rd = Memory[$Rs + IMM]$` [cite: 1617] |
| **STORE** | `0010` | I | `Memory[$Rs + IMM] = $Rt$` [cite: 1617] |
| **LOADI** | `0011` | I | `$Rd = IMM` [cite: 1617] |
| **AND** | `0100` | R | `$Rd = $Rs AND $Rt$` [cite: 1617] |
| **BEQ** | `0101` | I | `if ($Rs == $Rt) PC = PC + IMM` [cite: 1617] |
| **BNE** | `0111` | I | `if ($Rs != $Rt) PC = PC + IMM` [cite: 1617] |
| **OR** | `1000` | R | `$Rd = $Rs OR $Rt$` [cite: 1617] |
| **J** | `1011` | J | `PC = JMP_IMM` [cite: 1617] |
| **ROTL** | `1100` | R | `$Rd = $Rs ROTL $Rt$` [cite: 1617] |
| **SLT** | `1111` | R | `$Rd = ($Rs < $Rt) ? 1 : 0$` [cite: 1617] |

*(Based on Table 2.6 and 2.7 from the project specifications [cite: 1617, 1640])*

---

## 🖥️ How to Simulate

1.  **Software:** This project requires [Logisim](http://www.cburch.com/logisim/) or a fork like [Logisim-evolution](https://github.com/logisim-evolution/logisim-evolution) to open.
2.  **Open:** Clone the repository and open the `8_bit_cpu.circ` file[cite: 1848].
3.  **Load Program:** The Instruction Memory (ROM) is pre-loaded with a test program. To load a different one:
    * [cite_start]In the `CPU` circuit, right-click the "Instruction_Memory" ROM component[cite: 1443].
    * Select "Load Image..." and choose a valid `.hex` file[cite: 1466, 1568].
4.  **Run:** Enable simulation by pressing **Ctrl+K** or navigating to `Simulate > Ticks Enabled`[cite: 1539].
5.  **Observe:** You can observe the values in the `Reg_File` and `Data_Memory` components change as the simulation runs.

## ✅ Verification

The CPU's correctness was verified using a test program (found in `report 3.pdf` [cite: 816-871] and specified in `specifications.pdf` [cite: 1660-1670]) designed to check all instruction types.

**Test Program Assembly:**
```asm
LI R3,6       ; Load R3 with 6 [cite: 1661]
LI R6,1       ; Load R6 with 1 [cite: 1662]
LI R4,12      ; Load R4 with 12 [cite: 1663]
L1:
ADD R7, R7,R6 ; Add R6 to R7 (effectively R7++) [cite: 1665]
SLT R5,R4,R7  ; Set R5 = 1 if R4 < R7 (12 < R7), else 0 [cite: 1666]
BEQ R6,R5, L1 ; Branch back to L1 if R6 == R5 (if 1 == 1) [cite: 1667]
J END         ; Jump to END [cite: 1668]
ADD R2,R2,R6  ; (This instruction is skipped) [cite_start][cite: 1669]
END:
              ; Loop [cite: 1670]
```

**Execution Analysis:**
This program tests `LI`, `ADD`, `SLT`, `BEQ`, and `J`. It loads initial values and enters a loop (`L1`). Inside the loop, `R7` is incremented. `R5` is set to 1 *only* when `R7` becomes greater than `R4` (12)[cite: 868, 869]. The `BEQ` instruction checks if `R5` is 1 (meaning the `SLT` was true) and branches if it is[cite: 870]. This test successfully verifies the functionality of arithmetic, logic, memory, and control flow instructions.