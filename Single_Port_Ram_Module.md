# Single Port RAM in Verilog

## 1. Explanation

A **Single Port RAM** is a memory block that allows either a **read** or a **write** operation at one address per clock cycle, but not both simultaneously.  
- It has **one data port** which can be used for either input (write) or output (read).  
- The **write enable (we)** signal decides whether the operation is a write or a read:  
  - `we = 1` → Data is written into the memory at the given address.  
  - `we = 0` → Data from the memory at the given address is read out.  
- The memory is synchronous with the **clock (clk)**, meaning operations happen on the rising edge of the clock.

In this design:  
- Data width = **8 bits**  
- Address width = **6 bits** (64 locations)  
- RAM size = **64 x 8 = 512 bits**

---

## 2. Verilog Design Code

```verilog
// Single Port RAM module design
module single_port_ram(
    input [7:0] data,     // input data
    input [5:0] addr,     // address
    input we,             // write enable
    input clk,            // clk
    output [7:0] q        // output data
);

    reg [7:0] ram [63:0];     // 8*64 bit ram
    reg [5:0] addr_reg;       // address register

    always @ (posedge clk)
    begin
        if (we)
            ram[addr] <= data;
        else
            addr_reg <= addr;
    end

    assign q = ram[addr_reg];

endmodule
```

## 3. Testbench design code

```verilog
// Single Port RAM testbench
module single_port_ram_tb;
    reg [7:0] data;    // input data
    reg [5:0] addr;    // address
    reg we;            // write enable
    reg clk;           // clock
    wire [7:0] q;      // output data

    // Instantiate the RAM
    single_port_ram spr1(
        .data(data),
        .addr(addr),
        .we(we),
        .clk(clk),
        .q(q)
    );

    // Clock generation
    initial begin
        clk = 1'b1;
        forever #5 clk = ~clk;   // 10 time units clock period
    end

    // Dump waveform
    initial begin
        $dumpfile("dump.vcd");
        $dumpvars(1, single_port_ram_tb);
    end

    // Test sequence
    initial begin
        // Write operations
        data = 8'h01; addr = 5'd0; we = 1'b1; #10;
        data = 8'h02; addr = 5'd1; we = 1'b1; #10;
        data = 8'h03; addr = 5'd2; we = 1'b1; #10;
        data = 8'h04; addr = 5'd3; we = 1'b1; #10;

        // Read operations
        we = 1'b0; addr = 5'd0; #10;
        addr = 5'd1; #10;
        addr = 5'd2; #10;
        addr = 5'd3; #10;

        $finish; // end simulation
    end
endmodule

```

## 4. Timing Chart:-
