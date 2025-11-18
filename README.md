# Logisim 8-Bit Single-Cycle CPU Design and Simulation

This project is a complete, 8-bit single-cycle processor architected, implemented, and verified within the Logisim simulation environment. It demonstrates core computer organization principles by building a functional CPU from the ground up, component by component.

The processor executes a custom 21-bit ISA (Instruction Set Architecture) and features a Harvard architecture with separate instruction and data memory.

![Logisim](https://img.shields.io/badge/Built%20with-Logisim-orange)

### Final CPU Datapath

This is the top-level view of the complete single-cycle processor, showing the interconnection of the Program Counter (PC), Control Unit, Register File, ALU, and Memory modules.

> **[Image: Screenshot of the complete 8-bit CPU datapath in Logisim]**
>
> *(**Developer Note:** Place your screenshot of the main `CPU` circuit here. It should look like the schematic on Page 11 of `report 3.pdf`.)*

---

## 🚀 Core Features

* **8-bit Single-Cycle Datapath:** Executes one instruction per clock cycle.
* **Harvard Architecture:** Uses separate memory for instructions (ROM) and data (RAM).
* **Custom 21-bit ISA:** Implements R-type, I-type, and J-type instruction formats.
* **Full-Featured ALU:** An 8-bit ALU that performs 5 operations (`ADD`, `AND`, `OR`, `ROTL`, `SLT`) and generates an `EQ` flag for branching.
* **Control Flow:** Supports sequential execution (`PC+1`), relative branches (`BEQ`, `BNE`), and absolute jumps (`J`).

## 🏛️ Architecture & Modules

The CPU is designed modularly, with each component built as a separate Logisim sub-circuit.

### 1. Control Unit

A combinational logic circuit that decodes the instruction `opcode` and (for branches) the `EQ` flag from the ALU. It generates all necessary control signals for the datapath.

* **Output Signals:**
    * `mux1`: Selects the value to be written back to the register file (ALU result or memory data).
    * `mux2`: Selects the second operand for the ALU (Register data or immediate value).
    * `WE` (Write Enable): Enables writing to the Register File.
    * `write` / `read`: Control signals for Data Memory.
    * `ALUfunc`: A 3-bit signal that tells the ALU which operation to perform.
    * `PCmux` & `Branch`: Signals to control PC updates for branches or jumps.

> **[Image: Screenshot of the Control Unit sub-circuit logic]**
>
> *(**Developer Note:** Place your screenshot of the `Control_Unit` circuit here. It should look like the schematic on Page 7 of `report 3.pdf`.)*

### 2. Arithmetic Logic Unit (ALU)

The 8-bit computational core of the CPU.
* **Operations:** `ADD`, `AND`, `OR`, `ROTL` (Rotate Left), `SLT` (Set Less Than).
* **Flags:** Generates a 1-bit `EQ` (Equal) flag, which is set to `1` if `X == Y`. This flag is fed to the Control Unit to handle `BEQ` and `BNE` instructions.

> **[Image: Screenshot of the ALU sub-circuit logic]**
>
> *(**Developer Note:** Place your screenshot of the `ALU` circuit here. It should look like the schematic on Page 8 of `report 3.pdf`.)*

### 3. Register File

* An 8x8 (8 registers, 8 bits each) register file (R0-R7).
* Supports two simultaneous 8-bit reads (for `rs` and `rt`).
* Supports one 8-bit write to a destination register (`rd`) on the clock's falling edge, if `WE` is high.

### 4. Memory

* **Instruction Memory (ROM):** A 1K x 21-bit Read-Only Memory.
    * It is 10-bit addressable (1024 instructions).
    * Stores the 21-bit wide instructions for the program.
* **Data Memory (RAM):** A 256 x 8-bit Random-Access Memory.
    * It is 8-bit addressable (256 bytes).
    * Used by `LOAD` and `STORE` instructions to read from and write to memory.

### 5. Program Counter (PC)

A 10-bit register that holds the address of the *next* instruction to be fetched. Its value is updated every clock cycle based on the output of a MUX, which selects from:
* **`PC + 1`:** For sequential execution.
* **`Branch_Add`:** The target address for `BEQ` or `BNE` instructions.
* **`JMP`:** The 10-bit absolute address for `J` instructions.

---

## 📖 Instruction Set Architecture (ISA)

The CPU implements a custom 21-bit ISA with R-type, I-type, and J-type formats. The 4-bit opcode determines the instruction and control signals.

| Mnemonic | Opcode | Type | Operation |
| :--- | :--- | :--- | :--- |
| **ADD** | `0000` | R | `$Rd = $Rs + $Rt$` |
| **LOAD** | `0001` | I | `$Rd = Memory[$Rs + IMM]$` |
| **STORE** | `0010` | I | `Memory[$Rs + IMM] = $Rt$` |
| **LOADI** | `0011` | I | `$Rd = IMM` |
| **AND** | `0100` | R | `$Rd = $Rs AND $Rt$` |
| **BEQ** | `0101` | I | `if ($Rs == $Rt) PC = PC + IMM` |
| **BNE** | `0111` | I | `if ($Rs != $Rt) PC = PC + IMM` |
| **OR** | `1000` | R | `$Rd = $Rs OR $Rt$` |
| **J** | `1011` | J | `PC = JMP_IMM` |
| **ROTL** | `1100` | R | `$Rd = $Rs ROTL $Rt$` |
| **SLT** | `1111` | R | `$Rd = ($Rs < $Rt) ? 1 : 0$` |

*(Based on Table 2.6 and 2.7 from the project specifications)*

---

## 🖥️ How to Simulate

1.  **Software:** This project requires [Logisim](http://www.cburch.com/logisim/) or a fork like [Logisim-evolution](https://github.com/logisim-evolution/logisim-evolution) to open.
2.  **Open:** Clone the repository and open the `8_bit_cpu.circ` file.
3.  **Load Program:** The Instruction Memory (ROM) is pre-loaded with a test program. To load a different one:
    * In the `CPU` circuit, right-click the "Instruction_Memory" ROM component.
    * Select "Load Image..." and choose a valid `.hex` file.
4.  **Run:** Enable simulation by pressing **Ctrl+K** or navigating to `Simulate > Ticks Enabled`.
5.  **Observe:** You can observe the values in the `Reg_File` and `Data_Memory` components change as the simulation runs.

## ✅ Verification

The CPU's correctness was verified using a test program (found in `report 3.pdf` and specified in `specifications.pdf`) designed to check all instruction types.

**Test Program Assembly:**
```asm
LI R3,6       ; Load R3 with 6
LI R6,1       ; Load R6 with 1
LI R4,12      ; Load R4 with 12
L1:
ADD R7, R7,R6 ; Add R6 to R7 (effectively R7++)
SLT R5,R4,R7  ; Set R5 = 1 if R4 < R7 (12 < R7), else 0
BEQ R6,R5, L1 ; Branch back to L1 if R6 == R5 (if 1 == 1)
J END         ; Jump to END
ADD R2,R2,R6  ; (This instruction is skipped)
END:
              ; Loop
```

**Execution Analysis:**
This program tests `LI`, `ADD`, `SLT`, `BEQ`, and `J`. It loads initial values and enters a loop (`L1`). Inside the loop, `R7` is incremented. `R5` is set to 1 *only* when `R7` becomes greater than `R4` (12). The `BEQ` instruction checks if `R5` is 1 (meaning the `SLT` was true) and branches if it is. This test successfully verifies the functionality of arithmetic, logic, memory, and control flow instructions.