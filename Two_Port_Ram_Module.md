# Dual Port RAM in Verilog

## 1. Explanation

A **Dual Port RAM** is a type of memory that allows **simultaneous access** from two independent ports.  
- Each port has its own **data input, address input, write enable, and data output**.  
- Both ports can perform **read** or **write** operations at the same time, as long as they are not writing to the same address (otherwise, the behavior depends on the implementation).  
- This makes dual-port RAM very useful in applications where two processors or modules need to share memory concurrently.

In this design:  
- Data width = **8 bits**  
- Address width = **6 bits** (64 locations)  
- RAM size = **64 x 8 = 512 bits**  
- Both ports (**Port A** and **Port B**) can access the RAM independently.

---

## 2. Verilog Design Code

```verilog
// Dual Port RAM module design
module dual_port_ram(
    input [7:0] data_a, data_b,      // input data
    input [5:0] addr_a, addr_b,      // Port A and Port B addresses
    input we_a, we_b,                // write enable for Port A and Port B
    input clk,                       // clock
    output reg [7:0] q_a, q_b        // output data at Port A and Port B
);

    reg [7:0] ram [63:0];            // 8*64 bit RAM

    // Port A operations
    always @ (posedge clk) begin
        if (we_a)
            ram[addr_a] <= data_a;
        else
            q_a <= ram[addr_a];
    end

    // Port B operations
    always @ (posedge clk) begin
        if (we_b)
            ram[addr_b] <= data_b;
        else
            q_b <= ram[addr_b];
    end

endmodule

```

## 3. Verilog Testbench Design


```verilog
// Dual Port RAM testbench
module dual_port_ram_tb;
    reg [7:0] data_a, data_b;       // input data
    reg [5:0] addr_a, addr_b;       // Port A and Port B addresses
    reg we_a, we_b;                 // write enable signals
    reg clk;                        // clock
    wire [7:0] q_a, q_b;            // outputs at Port A and Port B

    // Instantiate the Dual Port RAM
    dual_port_ram dpr1(
        .data_a(data_a),
        .data_b(data_b),
        .addr_a(addr_a),
        .addr_b(addr_b),
        .we_a(we_a),
        .we_b(we_b),
        .clk(clk),
        .q_a(q_a),
        .q_b(q_b)
    );

    // Clock generation
    initial begin
        clk = 1'b1;
        forever #5 clk = ~clk;   // 10 time units clock period
    end

    // Dump waveform
    initial begin
        $dumpfile("dump.vcd");
        $dumpvars(1, dual_port_ram_tb);
    end

    // Test sequence
    initial begin
        // Write data simultaneously at different ports
        data_a = 8'h33; addr_a = 6'h01;
        data_b = 8'h44; addr_b = 6'h02;
        we_a = 1'b1; we_b = 1'b1; #10;

        // More writes
        data_a = 8'h55; addr_a = 6'h03;
        data_b = 8'h66; addr_b = 6'h04; #10;

        // Disable write, enable reads
        we_a = 1'b0; we_b = 1'b0;

        // Read back values
        addr_a = 6'h01; addr_b = 6'h02; #10;
        addr_a = 6'h03; addr_b = 6'h04; #10;

        $finish; // End simulation
    end
endmodule
```


## 4. Timing Chart
<img width="1855" height="410" alt="image" src="https://github.com/user-attachments/assets/a3afc903-967f-406c-bf49-e3cf7b8af848" />

 
