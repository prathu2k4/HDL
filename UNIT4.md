## **UNIT IV**

### **1. Mention the differences between tasks and functions**

| Feature                  | **Task**                                                                                                                    | **Function**                                                                                              |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **Purpose**              | Used to execute a set of Verilog statements, usually for **complex operations** like file I/O, delays, or multiple outputs. | Used for **computational purposes** that return a single value and are typically used within expressions. |
| **Return Value**         | Does **not return a value** explicitly (can use output or inout ports to pass data).                                        | Always **returns a single value** (must be assigned inside the function).                                 |
| **Time Control**         | Can contain **time-controlling statements** like `#`, `@`, `wait`.                                                          | Cannot contain **any time-controlling statements** (pure combinational behavior).                         |
| **Number of Outputs**    | Can have **zero, one, or multiple outputs** via `output` or `inout` arguments.                                              | Can only **return a single value** (no output ports).                                                     |
| **Usage**                | Generally used for **modeling complex behaviors**, testbenches, or controlling events.                                      | Used for **pure computations**, combinatorial logic, or simplifying expressions.                          |
| **Invocation**           | Invoked using **task call** syntax: `task_name();`.                                                                         | Invoked like a **function call** within an expression: `result = function_name();`.                       |
| **Declaration Keywords** | Declared with `task` and `endtask` keywords.                                                                                | Declared with `function` and `endfunction` keywords.                                                      |
| **Example Use Case**     | File operations, multi-cycle operations, stimulus generation.                                                               | Parity checking, encoding, decoding, simple combinatorial calculations.                                   |

---

### **2. What are the conditions where tasks can be implemented? How it is declared and invoked?**

#### **Conditions to use tasks:**

* When **multiple outputs** are needed.
* When **timing controls** (such as `#delay`, `@posedge`, `wait`) are required inside the block.
* When the operation needs to perform **sequential actions** or complex behaviors (such as driving signals in a testbench or synthesizable sequential logic).
* When **side effects** (like file operations or changing global states) are required.

#### **Declaration of Task:**

```verilog
task task_name;
   // Declare inputs, outputs, inouts (optional)
   input  [31:0] in1;
   output [31:0] out1;
   begin
       // Task body
   end
endtask
```

#### **Example:**

```verilog
task display_sum;
   input [3:0] a, b;
   output [4:0] sum;
   begin
      sum = a + b;
      $display("Sum = %d", sum);
   end
endtask
```

#### **Invocation of Task:**

```verilog
reg [3:0] a, b;
reg [4:0] result;
initial begin
   a = 4'd7;
   b = 4'd8;
   display_sum(a, b, result);
end
```

---

### **3. What are the conditions where functions can be implemented? How it is declared and invoked?**

#### **Conditions to use functions:**

* When **only one value** needs to be returned.
* When the functionality involves **pure combinational logic** (no timing control allowed).
* When it is necessary to use the result within an expression or assignment.
* When **no file I/O or delays** are needed.
* Functions must **not modify global variables** (should be side-effect-free).

#### **Declaration of Function:**

```verilog
function [31:0] function_name;
   input [31:0] input_var;
   begin
       function_name = // function body
   end
endfunction
```

#### **Example:**

```verilog
function [3:0] max_value;
   input [3:0] a, b;
   begin
      if (a > b)
         max_value = a;
      else
         max_value = b;
   end
endfunction
```

#### **Invocation of Function:**

```verilog
reg [3:0] result;
result = max_value(4'd5, 4'd9);
```

#### **Key Points:**

* Functions are ideal for creating reusable **combinational logic** blocks.
* Cannot include **`@` events, delays, or wait** statements.
* Return value is assigned via the function name inside the body.

---

### **4. With an example, illustrate disabling a named block which contains non-blocking statements.**

####  Explanation:

* Verilog allows disabling named blocks using the `disable` statement.
* Useful to **abort or terminate** a procedural block prematurely.
* Can be applied to **named blocks** (`begin : block_name ... end`).
* Works even if the block contains **non-blocking statements (`<=`)**.

#### Syntax:

```verilog
disable block_name;
```

#### Example:

```verilog
module disable_example;
  reg clk, rst;
  reg [3:0] counter;
  
  initial begin
    clk = 0;
    forever #5 clk = ~clk;
  end
  
  initial begin
    rst = 0;
    #20 rst = 1;
    #10 rst = 0;
  end

  always @(posedge clk) begin : counter_block
    if (rst)
      disable counter_block; // disables current block execution
    else
      counter <= counter + 1; // non-blocking statement
  end
  
  initial begin
    counter = 0;
    #100 $finish;
  end

endmodule
```

####  Explanation:

* `disable counter_block` immediately terminates the named block upon reset.
* Even though there are non-blocking statements (`<=`), `disable` halts execution safely.
* This is useful for aborting operations like counters or loops under certain conditions.

---

### **5. Define a module that contains the function to calculate the parity of a 32-bit address and returns the value.**

####  Explanation:

Parity is a simple **error-detection mechanism**:

* **Even Parity:** Result is `1` if the total number of `1`s is odd (to make even parity).
* **Odd Parity:** Result is `1` if total `1`s count is odd.

####  Verilog Module with Parity Function:

```verilog
module parity_generator(
    input  [31:0] address,
    output parity
);
  assign parity = calculate_parity(address);

  // Function to calculate even parity
  function calculate_parity;
    input [31:0] addr;
    integer i;
    begin
      calculate_parity = 0;
      for (i = 0; i < 32; i = i + 1) begin
        calculate_parity = calculate_parity ^ addr[i]; // XOR each bit
      end
    end
  endfunction

endmodule
```

####  Explanation:

* The function `calculate_parity` loops through all bits of the 32-bit address and calculates parity using XOR.
* Returns **even parity** (`1` if odd number of `1`s).
* Simple, synthesizable logic.

---

### **6. With an example each, illustrate the usage of:** 
**(i) Opening a file
 (ii) Writing to a file
(iii) Closing of a file**



### (i) Opening a File: `$fopen` or `$fopenr`, `$fopenw`, `$fopena` (SystemVerilog)

#### Syntax:

```verilog
integer file_descriptor;
file_descriptor = $fopen("filename", "mode");
```

#### Modes (especially in **SystemVerilog**):

| Mode  | Description            |
| ----- | ---------------------- |
| `"r"` | Read mode              |
| `"w"` | Write mode (overwrite) |
| `"a"` | Append mode            |

In Verilog-2001, only `$fopen("filename")` is used and it defaults to write mode.

#### Explanation:

* Returns an integer (called **file descriptor**) which is used to reference the file in future operations.
* If the file fails to open, `$fopen` returns **zero**.

#### Example:

```verilog
integer fd;
fd = $fopen("output.txt", "w");
if (fd == 0)
  $display("Error: File not opened.");
```


---

### (ii) Writing to a File: `$fdisplay`, `$fwrite`, `$fstrobe`, `$fmonitor`

These are **file-based equivalents** of console output tasks like `$display`, `$write`, etc.

#### Syntax:

```verilog
$fdisplay(file_descriptor, "format string", vars);
```

#### Common File Writing Tasks:

| Task        | Description                            |
| ----------- | -------------------------------------- |
| `$fdisplay` | Writes with newline at the end         |
| `$fwrite`   | Writes without newline                 |
| `$fstrobe`  | Writes when signal changes             |
| `$fmonitor` | Continuously monitors and logs changes |

#### Formatting:

You can use formatting codes similar to C:

* `%d`, `%b`, `%h`, `%s`, `%0t`, etc.

#### Example:

```Verilog
integer fd1,fd2;
fd1 = $fopen("out1.txt", "w");
fd1 = $fopen("out2.txt", "w");

$fdisplay(fd1, "Display 1");
$fdisplay(fd2, "Display 2");
```


---

###  (iii) Closing a File: `$fclose`

####  Syntax:

```verilog
$fclose(file_descriptor);
```

####  Explanation:

* Frees up resources.
* Ensures that data is flushed and file is properly saved.
* Always recommended at end of simulation or after writing is done.

#### Example:

```Verilog
$fclose(fd);
```

---


### **7. Write a Verilog code for bitwise operation which performs AND, OR, and XOR for two 16-bit numbers, using input and output arguments in task.**

####  Explanation:

* We'll write a **task** to perform **AND, OR, and XOR** operations on two 16-bit numbers.
* The task will have:

  * **Inputs:** Two 16-bit numbers.
  * **Outputs:** Three 16-bit results for AND, OR, and XOR.

####  Verilog Code:

```verilog
module bitwise_operations;

  reg [15:0] a, b;
  reg [15:0] and_result, or_result, xor_result;

  // Task Declaration
  task bitwise_task;
    input [15:0] in1, in2;
    output [15:0] and_out, or_out, xor_out;
    begin
      and_out = in1 & in2;  // Bitwise AND
      or_out  = in1 | in2;  // Bitwise OR
      xor_out = in1 ^ in2;  // Bitwise XOR
    end
  endtask

  // Testbench
  initial begin
    // Example inputs
    a = 16'hA5A5;
    b = 16'h0F0F;

    // Call task
    bitwise_task(a, b, and_result, or_result, xor_result);

    // Display Results
    $display("Input A        = %h", a);
    $display("Input B        = %h", b);
    $display("AND Result     = %h", and_result);
    $display("OR Result      = %h", or_result);
    $display("XOR Result     = %h", xor_result);
  end

endmodule
```

####  Explanation:

* Task `bitwise_task` accepts two inputs (`in1`, `in2`) and produces three outputs.
* Inside task:

  * `&` → Bitwise AND.
  * `|` → Bitwise OR.
  * `^` → Bitwise XOR.
* Demonstrated with sample values `16'hA5A5` and `16'h0F0F`.
* Displays results on simulation console.

---

### **8. Define a module that contains the function to shift a 32-bit value to the left or right by one bit, based on a control signal.**

####  Explanation:

We’ll create a function inside a module that:

* Shifts a **32-bit input** either **left or right by one bit**.
* Uses a **control signal** to decide direction:

  * `0` → Shift left.
  * `1` → Shift right.

####  Verilog Code:

```verilog
module shift_operation(
    input  [31:0] data_in,
    input shift_dir,  // 0 = left, 1 = right
    output [31:0] data_out
);

  assign data_out = shift_function(data_in, shift_dir);

  // Function to shift based on direction
  function [31:0] shift_function;
    input [31:0] in_data;
    input dir;
    begin
      if (dir == 1'b0)
        shift_function = in_data << 1;  // Left shift by 1
      else
        shift_function = in_data >> 1;  // Right shift by 1
    end
  endfunction

endmodule
```

####  Testbench Example (Optional):

```verilog
module test_shift;
  reg [31:0] data;
  reg direction;
  wire [31:0] result;

  // Instantiate shift module
  shift_operation uut (
    .data_in(data),
    .shift_dir(direction),
    .data_out(result)
  );

  initial begin
    data = 32'hA5A5A5A5;

    // Test left shift
    direction = 0;
    #10 $display("Left Shift:  %h", result);

    // Test right shift
    direction = 1;
    #10 $display("Right Shift: %h", result);

    $finish;
  end
endmodule
```

####  Explanation:

* Function `shift_function` checks `dir` signal:

  * If `dir = 0`, left shift by 1 (`<<`).
  * If `dir = 1`, right shift by 1 (`>>`).
* Synthesizable, clean shifting operation for hardware modeling.

---

### **9. Write the Verilog description to define a factorial with a recursive function**

####  Explanation:

* Verilog-2001 and later allow **recursive functions**.
* Recursive functions are functions that **call themselves** to solve smaller instances of a problem.
* **Factorial (`n!`)** is a typical example of recursion:

  * **n! = n × (n−1)!**
  * Base case: **0! = 1**

####  Verilog Code:

```verilog
module factorial_recursive_example;

  reg [7:0] num;
  reg [31:0] result;

  // Recursive function for factorial
  function [31:0] factorial;
    input [7:0] n;
    begin
      if (n == 0)
        factorial = 1;  // Base case: 0! = 1
      else
        factorial = n * factorial(n - 1);  // Recursive call
    end
  endfunction

  // Testbench
  initial begin
    num = 5; // Example: 5! = 120
    result = factorial(num);
    $display("Factorial of %0d is %0d", num, result);

    num = 7;
    result = factorial(num);
    $display("Factorial of %0d is %0d", num, result);

    $finish;
  end

endmodule
```

####  Explanation:

* Function `factorial` recursively computes factorial.
* Base case stops recursion at **n = 0**.
* This is a **pure combinational function**.
* Suitable for simulation and synthesis for **small `n`** (deep recursion can cause stack issues in simulation).

---

### **10. Define a module that contains the function to calculate the parity of a 32-bit address and returns the value.**

> **Note:** This is same as **Question 5**, but here’s the full answer again for completeness:

####  Explanation:

* The function will compute the **even parity** of a **32-bit address**:

  * Even parity: XOR all bits together.
  * Result = `1` → Odd number of 1’s; `0` → Even number of 1’s.

####  Verilog Code:

```verilog
module parity_calculator (
  input  [31:0] address,
  output        parity
);

  assign parity = calculate_parity(address);

  // Function to calculate even parity
  function calculate_parity;
    input [31:0] addr;
    integer i;
    begin
      calculate_parity = 0;
      for (i = 0; i < 32; i = i + 1) begin
        calculate_parity = calculate_parity ^ addr[i];
      end
    end
  endfunction

endmodule
```

####  Testbench Example (Optional):

```verilog
module test_parity;

  reg [31:0] addr;
  wire       parity_result;

  parity_calculator uut (
    .address(addr),
    .parity(parity_result)
  );

  initial begin
    addr = 32'hAAAAAAAA; // Example value
    #10 $display("Parity of %h is %b", addr, parity_result);

    addr = 32'h12345678;
    #10 $display("Parity of %h is %b", addr, parity_result);

    $finish;
  end

endmodule
```

####  Explanation:

* `calculate_parity` function loops over all bits and applies XOR to compute parity.
* Synthesizable and testbench-friendly.

---

### **11. Define a function to multiply two 8-bit numbers a and b. The output is a 16-bit value. Invoke the function by using stimulus and check the results.**

####  Explanation:

* We’ll define a **function** to multiply two **8-bit numbers** and return a **16-bit result**.
* Multiplication is a **combinational operation** that suits a function perfectly.
* We’ll also write a simple **testbench (stimulus)** to invoke the function and verify results.

####  Verilog Code:

```verilog
module multiplier_function_example;

  reg [7:0] a, b;
  reg [15:0] result;

  // Function to multiply two 8-bit numbers
  function [15:0] multiply;
    input [7:0] in1, in2;
    begin
      multiply = in1 * in2;  // Multiplication
    end
  endfunction

  // Testbench (Stimulus)
  initial begin
    // Test Case 1
    a = 8'd15;
    b = 8'd10;
    result = multiply(a, b);
    $display("Multiplication of %d and %d = %d", a, b, result);  // 15 * 10 = 150

    // Test Case 2
    a = 8'd255;
    b = 8'd2;
    result = multiply(a, b);
    $display("Multiplication of %d and %d = %d", a, b, result);  // 255 * 2 = 510

    // Test Case 3
    a = 8'd100;
    b = 8'd25;
    result = multiply(a, b);
    $display("Multiplication of %d and %d = %d", a, b, result);  // 100 * 25 = 2500

    $finish;
  end

endmodule
```

####  Explanation:

* Function `multiply` takes two 8-bit inputs and returns a 16-bit result.
* Called inside `initial` block for testing.
* Simulation displays the results.

---

### **12. Define a module to generate an N-bit adder using Generate blocks.**

####  Explanation:

* We’ll use **Verilog generate blocks** to create a **parameterized N-bit ripple carry adder**.
* This technique allows scalable hardware for **any N-bit** addition by setting the `parameter N`.
* We’ll define **full adders** and connect them using generate-for loop.

####  Verilog Code:

```verilog
module n_bit_adder #(
  parameter N = 8  // Default: 8-bit adder, can be changed during instantiation
)(
  input  [N-1:0] a, b,
  input          cin,
  output [N-1:0] sum,
  output         cout
);

  wire [N:0] carry;
  assign carry[0] = cin;

  // Generate block to instantiate N full adders
  genvar i;
  generate
    for (i = 0; i < N; i = i + 1) begin : adder_stage
      full_adder FA (
        .a(a[i]),
        .b(b[i]),
        .cin(carry[i]),
        .sum(sum[i]),
        .cout(carry[i+1])
      );
    end
  endgenerate

  assign cout = carry[N];

endmodule

// Full Adder Module
module full_adder(
  input  a, b, cin,
  output sum, cout
);
  assign {cout, sum} = a + b + cin;
endmodule
```

####  Testbench Example (Stimulus for N-bit Adder):

```verilog
module test_n_bit_adder;

  reg  [7:0] a, b;
  reg        cin;
  wire [7:0] sum;
  wire       cout;

  // Instantiate 8-bit adder
  n_bit_adder #(.N(8)) uut (
    .a(a),
    .b(b),
    .cin(cin),
    .sum(sum),
    .cout(cout)
  );

  initial begin
    a = 8'd25;
    b = 8'd75;
    cin = 0;
    #10 $display("Sum: %d, Carry Out: %b", sum, cout);  // 25 + 75 = 100

    a = 8'd255;
    b = 8'd1;
    cin = 0;
    #10 $display("Sum: %d, Carry Out: %b", sum, cout);  // Overflow case

    $finish;
  end

endmodule
```

####  Explanation:

* Parameterized N-bit ripple-carry adder using `generate` loop.
* Uses `full_adder` module.
* Highly scalable: Change `N` to create 16-bit, 32-bit, etc. adders.
* Testbench demonstrates correct summation and carry.

---
