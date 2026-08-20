# 4:1 Multiplexer

# EXP NO: 1.A SIMULATION AND IMPLEMENTATION OF 4:1 MULTIPLEXER
# AIM
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

# APPARATUS REQUIRED
Vivado 2023.1

# Procedure
1.Open Vivado 2023.1.
2.Create a New RTL Project and give a name (e.g., Mux4_to_1).
3.Add/create your Verilog files and testbench.
4.Select an FPGA part (e.g., xc7a35ticsg324-1L).
5.Run Synthesis to check for errors.
6.Run Simulation → Run Behavioral Simulation.
7.Observe the waveforms of inputs and outputs.
8.Adjust simulation time if needed (e.g., 1000ns).
9.Save the project and take screenshots of results.
10.Close simulation.

# Logic Diagram

<img width="614" height="424" alt="Screenshot 2026-02-11 195225" src="https://github.com/user-attachments/assets/03cabe3f-914c-4163-bea7-2ba5257ed5a7" />


# Truthtable 

<img width="496" height="376" alt="Screenshot 2026-02-11 194234" src="https://github.com/user-attachments/assets/3530b20d-cc75-480c-bc14-d10b21a36376" />


# Verilog Code
4:1 MUX Gate-Level Implementation
```
// Gate Level Modelling - 4:1 Multiplexer
module mux4_gate (
    input  wire I0, I1, I2, I3,
    input  wire S0, S1,
    output wire Y
);

    // Declare internal wires
    wire S0_bar, S1_bar;
    wire W0, W1, W2, W3;

    // NOT gates
    not (S0_bar, S0);
    not (S1_bar, S1);

    // AND gates
    and (W0, I0, S1_bar, S0_bar);  // S1S0 = 00
    and (W1, I1, S1_bar, S0);      // S1S0 = 01
    and (W2, I2, S1, S0_bar);      // S1S0 = 10
    and (W3, I3, S1, S0);          // S1S0 = 11

    // OR gate
    or (Y, W0, W1, W2, W3);

endmodule
```
4:1 MUX Gate-Level Implementation- Testbench
```
// Testbench for 4:1 Multiplexer
`timescale 1ns/1ps

module tb_mux4_gate;

    // Declare testbench signals
    reg I0, I1, I2, I3;
    reg S0, S1;
    wire Y;

    // Instantiate DUT
    mux4_gate uut (
        .I0(I0), .I1(I1), .I2(I2), .I3(I3),
        .S0(S0), .S1(S1),
        .Y(Y)
    );

    initial begin
        // Initialize inputs
        I0 = 0;
        I1 = 0;
        I2 = 0;
        I3 = 0;
        S0 = 0;
        S1 = 0;

        // Apply test case: S1S0 = 00 -> Y = I0
        I0 = 1; I1 = 0; I2 = 0; I3 = 0;
        S1 = 0; S0 = 0;
        #10;

        // Apply test case: S1S0 = 01 -> Y = I1
        I0 = 0; I1 = 1; I2 = 0; I3 = 0;
        S1 = 0; S0 = 1;
        #10;

        // Apply test case: S1S0 = 10 -> Y = I2
        I0 = 0; I1 = 0; I2 = 1; I3 = 0;
        S1 = 1; S0 = 0;
        #10;

        // Apply test case: S1S0 = 11 -> Y = I3
        I0 = 0; I1 = 0; I2 = 0; I3 = 1;
        S1 = 1; S0 = 1;
        #10;

        // Additional test cases
        I0 = 1; I1 = 0; I2 = 1; I3 = 0;
        S1 = 0; S0 = 0;
        #10;

        I0 = 1; I1 = 1; I2 = 0; I3 = 0;
        S1 = 0; S0 = 1;
        #10;

        I0 = 0; I1 = 1; I2 = 1; I3 = 0;
        S1 = 1; S0 = 0;
        #10;

        I0 = 0; I1 = 0; I2 = 1; I3 = 1;
        S1 = 1; S0 = 1;
        #10;

        // Display final message
        $display("All test cases completed.");

        // Stop simulation
        $stop;
    end

    // Monitor input and output changes
    initial begin
        $monitor("Time=%0t | S1=%b S0=%b | I0=%b I1=%b I2=%b I3=%b | Y=%b",
                 $time, S1, S0, I0, I1, I2, I3, Y);
    end

endmodule
```
# Simulated Output Gate Level Modelling

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/85e39f64-f225-406e-b412-ab9456386258" />


4:1 MUX Data flow Modelling
```
`timescale 1ns/1ps

module mux4_dataflow (
    input I0, I1, I2, I3,
    input S0, S1,
    output Y
);

    assign Y = (~S1 & ~S0 & I0) |
               (~S1 &  S0 & I1) |
               ( S1 & ~S0 & I2) |
               ( S1 &  S0 & I3);

endmodule
```
4:1 MUX Data flow Modelling- Testbench
```
`timescale 1ns/1ps

module tb_mux4_dataflow;

    // Declare testbench signals
    reg I0, I1, I2, I3;
    reg S0, S1;
    wire Y;

    // Instantiate DUT
    mux4_dataflow uut (
        .I0(I0),
        .I1(I1),
        .I2(I2),
        .I3(I3),
        .S0(S0),
        .S1(S1),
        .Y(Y)
    );

    initial begin
        // Initialize inputs
        I0 = 0;
        I1 = 0;
        I2 = 0;
        I3 = 0;
        S0 = 0;
        S1 = 0;

        // Apply test cases

        // S1S0 = 00 -> Y = I0
        I0 = 1; I1 = 0; I2 = 0; I3 = 0;
        S1 = 0; S0 = 0;
        #1;

        // S1S0 = 01 -> Y = I1
        I0 = 0; I1 = 1; I2 = 0; I3 = 0;
        S1 = 0; S0 = 1;
        #1;

        // S1S0 = 10 -> Y = I2
        I0 = 0; I1 = 0; I2 = 1; I3 = 0;
        S1 = 1; S0 = 0;
        #1;

        // S1S0 = 11 -> Y = I3
        I0 = 0; I1 = 0; I2 = 0; I3 = 1;
        S1 = 1; S0 = 1;
        #1;

        // Additional test cases

        // I0 selected
        I0 = 0; I1 = 1; I2 = 1; I3 = 1;
        S1 = 0; S0 = 0;
        #1;

        // I1 selected
        I0 = 1; I1 = 0; I2 = 1; I3 = 1;
        S1 = 0; S0 = 1;
        #1;

        // I2 selected
        I0 = 1; I1 = 1; I2 = 0; I3 = 1;
        S1 = 1; S0 = 0;
        #1;

        // I3 selected
        I0 = 1; I1 = 1; I2 = 1; I3 = 0;
        S1 = 1; S0 = 1;
        #1;

        // Display final values
        $display("Simulation completed.");

        // Stop simulation
        #10 $stop;
    end

endmodule
```
# Simulated Output Dataflow Modelling
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cb0b5c6f-b66d-4374-9a56-c8436c222648" />


4:1 MUX Behavioral Implementation
```
`timescale 1ns/1ps

module mux4_to_1_behavioral (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output reg Y
);

    always @(*) begin
        case ({S1, S0})
            2'b00: Y = A;
            2'b01: Y = B;
            2'b10: Y = C;
            2'b11: Y = D;
            default: Y = 1'b0;
        endcase
    end

endmodule
```
#4:1 MUX Behavioral Modelling- Testbench
```
`timescale 1ns/1ps

module tb_mux4_behavioral;

    reg I0, I1, I2, I3;
    reg S0, S1;
    wire Y;

    // DUT
    mux4_to_1_behavioral uut (
        .A(I0),
        .B(I1),
        .C(I2),
        .D(I3),
        .S0(S0),
        .S1(S1),
        .Y(Y)
    );

    initial begin

        // Test 1: S1S0 = 00, I0 selected
        I0 = 1; I1 = 0; I2 = 0; I3 = 0;
        S1 = 0; S0 = 0;
        #1;
        $display("S1=%b S0=%b | I0=%b I1=%b I2=%b I3=%b | Y=%b",
                 S1, S0, I0, I1, I2, I3, Y);

        // Test 2: S1S0 = 01, I1 selected
        I0 = 0; I1 = 1; I2 = 0; I3 = 0;
        S1 = 0; S0 = 1;
        #1;
        $display("S1=%b S0=%b | I0=%b I1=%b I2=%b I3=%b | Y=%b",
                 S1, S0, I0, I1, I2, I3, Y);

        // Test 3: S1S0 = 10, I2 selected
        I0 = 0; I1 = 0; I2 = 1; I3 = 0;
        S1 = 1; S0 = 0;
        #1;
        $display("S1=%b S0=%b | I0=%b I1=%b I2=%b I3=%b | Y=%b",
                 S1, S0, I0, I1, I2, I3, Y);

        // Test 4: S1S0 = 11, I3 selected
        I0 = 0; I1 = 0; I2 = 0; I3 = 1;
        S1 = 1; S0 = 1;
        #1;
        $display("S1=%b S0=%b | I0=%b I1=%b I2=%b I3=%b | Y=%b",
                 S1, S0, I0, I1, I2, I3, Y);

        $display("Simulation completed!");

        #10;
        $finish;
    end

endmodule
```
# Simulated Output Behavioral Modelling
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d2748e85-5616-4fb5-b0c4-d151ac1d5f4c" />


#4:1 MUX Structural Implementation
```
`timescale 1ns/1ps

// 2:1 MUX
module mux2_to_1 (
    input wire A,
    input wire B,
    input wire S,
    output wire Y
);

    assign Y = S ? B : A;

endmodule


// 4:1 MUX using three 2:1 MUXes
module mux4_to_1_structural (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);

    wire Y1;
    wire Y2;

    // First stage
    mux2_to_1 MUX1 (
        .A(A),
        .B(B),
        .S(S0),
        .Y(Y1)
    );

    mux2_to_1 MUX2 (
        .A(C),
        .B(D),
        .S(S0),
        .Y(Y2)
    );

    // Second stage
    mux2_to_1 MUX3 (
        .A(Y1),
        .B(Y2),
        .S(S1),
        .Y(Y)
    );

endmodule
```
# Testbench Implementation
```
`timescale 1ns/1ps

module mux4_to_1_tb;

    reg A, B, C, D;
    reg S0, S1;

    wire Y_structural;

    // Instantiate structural 4:1 MUX
    mux4_to_1_structural uut (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y_structural)
    );

    initial begin

        // Initialize inputs
        A = 0;
        B = 0;
        C = 0;
        D = 0;
        S0 = 0;
        S1 = 0;

        // Test 1: S1S0 = 00 -> A selected
        A = 1;
        B = 0;
        C = 0;
        D = 0;
        S1 = 0;
        S0 = 0;
        #10;
        $display("S1=%b S0=%b | A=%b B=%b C=%b D=%b | Y=%b",
                 S1, S0, A, B, C, D, Y_structural);

        // Test 2: S1S0 = 01 -> B selected
        A = 0;
        B = 1;
        C = 0;
        D = 0;
        S1 = 0;
        S0 = 1;
        #10;
        $display("S1=%b S0=%b | A=%b B=%b C=%b D=%b | Y=%b",
                 S1, S0, A, B, C, D, Y_structural);

        // Test 3: S1S0 = 10 -> C selected
        A = 0;
        B = 0;
        C = 1;
        D = 0;
        S1 = 1;
        S0 = 0;
        #10;
        $display("S1=%b S0=%b | A=%b B=%b C=%b D=%b | Y=%b",
                 S1, S0, A, B, C, D, Y_structural);

        // Test 4: S1S0 = 11 -> D selected
        A = 0;
        B = 0;
        C = 0;
        D = 1;
        S1 = 1;
        S0 = 1;
        #10;
        $display("S1=%b S0=%b | A=%b B=%b C=%b D=%b | Y=%b",
                 S1, S0, A, B, C, D, Y_structural);

        $display("Simulation completed!");

        #10;
        $finish;

    end

endmodule
```
# Simulated Output Structural Modelling
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/88e1cafe-9e12-4d17-ab10-a64164141bb5" />


# CONCLUSION
In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural.The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.



endmodule
