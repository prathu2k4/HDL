### **1. Discuss each component of logic synthesis flow from RTL to gates**
![WhatsApp Image 2025-07-06 at 00 35 32_968decd3](https://github.com/user-attachments/assets/2c0f3e17-9289-4d8d-912e-60ef20c8080b)
![WhatsApp Image 2025-07-06 at 00 35 32_65cde47a](https://github.com/user-attachments/assets/c3ab7dd4-7b42-4306-8ce5-a16dd4173d68)

---

### **2. What are FPGAs and explain its general structure?**

![image](https://github.com/user-attachments/assets/4585ee69-bfd7-476d-ac43-5379c30de031)

* **FPGAs (Field-Programmable Gate Arrays)** are reconfigurable logic devices used for building large and complex digital circuits. Unlike SPLDs and CPLDs that use fixed logic planes, FPGAs use **configurable logic elements** and a flexible routing structure, making them ideal for custom hardware applications like processors, controllers, and signal processing.

* **Architecture**: FPGAs contain three main parts — **logic elements (LEs)** for computation, **I/O blocks** for external communication, and a matrix of **routing wires and programmable switches** that interconnect everything. This modular structure allows complex designs to be mapped efficiently.

* **2D Grid Layout**: Logic elements are placed in a **two-dimensional array**, with **routing channels** running between rows and columns. These channels carry signals between LEs and enable complex circuit interconnections inside the chip.

* **Types of Switches**:

  * **Adjacent switches** connect each logic element to nearby routing wires.
  * **Diagonal switches** connect one interconnect wire to another (e.g., vertical to horizontal), allowing signals to travel across the chip flexibly.

* **I/O Connections**: Programmable connections also link **I/O blocks** with internal routing wires, enabling communication between the FPGA and external devices. The number of wires and switches varies by FPGA model, allowing scalability and customization.



| Component                                                   | Description                                                                                                                              |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Configurable Logic Blocks (CLBs) / Logic Elements (LEs)** | The main building blocks containing look-up tables (LUTs), flip-flops, and multiplexers to implement combinational and sequential logic. |
| **Programmable Interconnects**                              | Wiring channels that allow configurable connections between CLBs and other blocks.                                                       |
| **I/O Blocks (IOBs)**                                       | Interfaces to connect FPGA with external components (GPIO, memory, etc.).                                                                |
| **Clock Management Units (PLL/MMCM)**                       | Handle clock generation, distribution, and conditioning.                                                                                 |
| **Embedded Memory Blocks (BRAM)**                           | On-chip RAM blocks for storing data or implementing FIFOs and buffers.                                                                   |
| **DSP Blocks**                                              | Dedicated multipliers and accumulators for high-speed arithmetic operations (common in signal processing).                               |
| **Configuration Memory**                                    | Stores the programming bitstream that defines the functionality of the FPGA.                                                             |

---


### **3. What are the general guidelines that the designer should consider while designing at the RTL level?**

#### **General RTL Design Guidelines:**

| Guideline                                | Description                                                                         |
| ---------------------------------------- | ----------------------------------------------------------------------------------- |
| **1. Clear Coding Style**                | Use consistent, clean, and readable coding conventions for portability.             |
| **2. Avoid Latches**                     | Always define both **if** and **else** conditions to avoid unintended latches.      |
| **3. Synchronous Design**                | Prefer synchronous logic (edge-triggered) for predictable behavior.                 |
| **4. Reset Logic**                       | Implement proper **asynchronous or synchronous resets** for registers.              |
| **5. One Clock Domain**                  | Avoid crossing between multiple clock domains unless necessary (use synchronizers). |
| **6. Resource Sharing**                  | Share resources (e.g., multipliers, adders) to save area when needed.               |
| **7. Avoid Complex Combinational Paths** | Split complex logic using pipelining or FSMs to avoid long critical paths.          |
| **8. Use FSM for Control Logic**         | Encode control logic as FSMs for better clarity and synthesis.                      |
| **9. Parameterization**                  | Use `parameter` or `localparam` for scalable, reusable designs.                     |
| **10. Testability**                      | Design with testability in mind (e.g., scan chains, accessible signals).            |
| **11. Avoid Unused Signals**             | Eliminate unused signals to reduce clutter and synthesis warnings.                  |
| **12. Proper Reset Values**              | Always initialize registers during reset to known values.                           |

---

### **4. Explain in detail:**

####  i. **PLD (Programmable Logic Device)**

####  ii. **CPLD (Complex Programmable Logic Device)**


####  i. **PLD (Programmable Logic Device)**

**Definition:**

A **PLD** is a general-purpose chip for implementing logic circuits. It contains a collection of logic circuit elements that can be customized in different ways. APLD can be viewed as
a *“black box”* that contains logic gates and programmable switches, as illustrated in Figure The programmable switches allow the logic gates inside the PLD to be connected
together to implement whatever logic circuit is needed.

![image](https://github.com/user-attachments/assets/c07654b9-295b-4ee5-a0d6-16d7cbb82609)


####  **Types of PLDs:**

| Type                               | Description                                                                             |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| **PROM (Programmable ROM)**        | Fixed AND array, programmable OR array; mainly used for memory and combinational logic. |
| **PAL (Programmable Array Logic)** | Programmable AND array, fixed OR array; simple, fast, limited flexibility.              |
| **PLA (Programmable Logic Array)** | Both AND and OR arrays are programmable; more flexible but slower and complex.          |

####  **Key Features:**

* Small-scale integration.
* Suitable for small to medium-sized combinational circuits.
* Configured once, typically not reprogrammable (except some EEPROM-based PLDs).
* Simple to use for **glue logic** and basic control circuits.

####  ii. **CPLD (Complex Programmable Logic Device)**

**Definition:**
A **CPLD** comprises multiple circuit blocks on a single chip, with internal wiring resources
to connect the circuit blocks. Each circuit block is similar to a PLA or a PAL; we
will refer to the circuit blocks as *PAL-like blocks*. This figure includes four *PAL-like blocks* that are connected to a set of *interconnection wires*.
Each PAL-like block is also connected to a subcircuit labeled *I/O block*, which is attached
to a number of the chip’s input and output pins.

![image](https://github.com/user-attachments/assets/ba5f5345-6be3-4ae3-b743-0eccd546ce2c)


#### **Key features:**:

1. **Non-volatile memory** – Retains configuration even after power-off.
2. **Fast and predictable timing** – Offers consistent performance due to fixed architecture.
3. **In-system programmability** – Can be programmed directly on the board.
4. **Quick startup time** – Becomes functional immediately after power-up.

---


### **5. Describe FSM for serial adder using:**

####  i. **Mealy Type FSM**

####  ii. **Moore Type FSM**

**Serial Adder**: The serial adder is a digital circuit in which bits are added a pair at a time.
![image](https://github.com/user-attachments/assets/78c517a2-5f1f-4af9-90bb-9f232ebef019)

Let A and B be two unsigned numbers to be added to produce Sum = A + B. In this we are using three shift registers which are used to hold A, B and Sum. Now in each clock cycle, a pair of bits is added by the adder FSM and at the end of the cycle, the resulting sum is shifted into the Sum register.

####  **i. Mealy FSM for Serial Adder:**

* **States:** **G** and **H** Represent carry value (Carry=0 or Carry=1).
* **Inputs:** A-bit, B-bit. (**a**, **b**)
* **Outputs:** Sum-bit (**s**).

####  **State Transition Table:**

| Current State | Inputs (a b) | Output (s) | Next State | State Change Status |
| ------------- | ------------ | ---------- | ---------- | ------------------- |
| G             | 00           | 0          | G          | Remains in G        |
| G             | 01           | 1          | G          | Remains in G        |
| G             | 10           | 1          | G          | Remains in G        |
| G             | 11           | 0          | H          | Moves to H          |
| H             | 00           | 1          | G          | Moves to G          |
| H             | 01           | 0          | H          | Remains in H        |
| H             | 10           | 0          | H          | Remains in H        |
| H             | 11           | 1          | H          | Remains in H        |





####  **State Diagram:**
![image](https://github.com/user-attachments/assets/25077dd7-2ee8-4f87-8daa-16d4884bbebe)

A single Flip-Flop is needed to represent the two states. The next state and output equations are:

*Y = ab + ay + by* 
*s = a ⊕ b ⊕ y*

####  **State Table:**
![image](https://github.com/user-attachments/assets/df2ef7af-655f-463b-be7e-87fd567d2eb3)


####  **State Assigned Table:**
![image](https://github.com/user-attachments/assets/ecc0abe5-6d12-4173-b0ed-cde5fe8f2b9b)

####  **Circuit Diagram:**
![image](https://github.com/user-attachments/assets/65a4c91b-05ea-4e98-93cd-3d9897dd5e09)

*The flip-flop can be cleared by the Reset signal at the start of the addition operation.*

---

####  **ii. Moore FSM for Serial Adder:**

* Each state represents a unique combination of **carry and sum**.
* More states needed to handle both carry and output conditions separately.



####  **State Diagram:**

![image](https://github.com/user-attachments/assets/2503cfd3-2d6f-4396-a7cb-08937ae8b813)

The next state and output equations are:

*Y1 = a ⊕ b ⊕ y2*
*Y2 = ab + by2 + by2*
*s = y1*

####  **State Table:**
![image](https://github.com/user-attachments/assets/15310024-60a6-48c8-aae1-70fefdb4ab14)

####  **State Assigned Table:**
![image](https://github.com/user-attachments/assets/18bf941e-860b-42c3-944e-fe2ff0f90d8f)

####  **Circuit Diagram:**
![image](https://github.com/user-attachments/assets/44ff55cf-26a4-4ff4-a2d8-a00451f1ec02)

---
