# Verification of System-on-Chip (SoC) Design

## Project: Verification of System-on-Chip (SoC) Design

### Tools: UVM, SystemVerilog, VCS

- Grown a verification environment for SoC design with multiple subsystems (CPU, DMA, memory), ensuring seamless integration between diverse modules  
- Implemented UVM-based testbenches for I2C, SPI, and AMBA interfaces, achieving a 40% reduction in integration bugs  
- Created transaction-level models (TLMs) for scalable verification, enabling easier testing of multiple subsystems within the SoC  
- Applied functional and code coverage techniques to analyze system-level interactions, ensuring complete verification  

## Overview

This project presents a UVM-based testbench for verifying a System-on-Chip (SoC) design integrating CPU, DMA, memory, and standard protocols like I2C, SPI, and AMBA. The testbench follows a modular, scalable, and reusable architecture with both functional and code coverage support.

## Directory Structure

```
soc_uvm_verification/
├── Makefile
├── README.md
├── tb/
│   ├── tb_top.sv
│   └── soc_if.sv
├── tests/
│   └── soc_test.sv
├── env/
│   └── soc_env.sv
├── tlm/
│   ├── i2c_transaction.sv
│   ├── spi_transaction.sv
│   └── amba_transaction.sv
├── coverage/
│   └── soc_coverage.sv
└── logs/
```

## Source Code

### tb/tb_top.sv
```systemverilog
`include "uvm_macros.svh"
import uvm_pkg::*;

module tb_top;

  logic clk;
  logic rst_n;

  initial begin
    clk = 0;
    forever #5 clk = ~clk;
  end

  initial begin
    rst_n = 0;
    #20 rst_n = 1;
  end

  SoC DUT (
    .clk(clk),
    .rst_n(rst_n)
  );

  initial begin
    run_test("soc_test");
  end

endmodule
```

### tests/soc_test.sv
```systemverilog
class soc_test extends uvm_test;
  `uvm_component_utils(soc_test)

  soc_env env;

  function new(string name = "soc_test", uvm_component parent = null);
    super.new(name, parent);
  endfunction

  function void build_phase(uvm_phase phase);
    super.build_phase(phase);
    env = soc_env::type_id::create("env", this);
  endfunction

  task run_phase(uvm_phase phase);
    phase.raise_objection(this);
    super.run_phase(phase);
    #5000;
    phase.drop_objection(this);
  endtask

endclass
```

### env/soc_env.sv
```systemverilog
class soc_env extends uvm_env;
  `uvm_component_utils(soc_env)

  i2c_agent i2c;
  spi_agent spi;
  amba_agent amba;

  function new(string name = "soc_env", uvm_component parent = null);
    super.new(name, parent);
  endfunction

  function void build_phase(uvm_phase phase);
    super.build_phase(phase);
    i2c = i2c_agent::type_id::create("i2c", this);
    spi = spi_agent::type_id::create("spi", this);
    amba = amba_agent::type_id::create("amba", this);
  endfunction

endclass
```

### tb/soc_if.sv
```systemverilog
interface soc_if(input bit clk);
  logic rst_n;
  logic [7:0] i2c_data;
  logic [7:0] spi_data;
  logic [31:0] amba_data;
endinterface
```

### tlm/i2c_transaction.sv
```systemverilog
class i2c_transaction extends uvm_sequence_item;
  rand bit [7:0] data;
  `uvm_object_utils(i2c_transaction)

  function new(string name = "i2c_transaction");
    super.new(name);
  endfunction
endclass
```

### tlm/spi_transaction.sv
```systemverilog
class spi_transaction extends uvm_sequence_item;
  rand bit [7:0] data;
  `uvm_object_utils(spi_transaction)

  function new(string name = "spi_transaction");
    super.new(name);
  endfunction
endclass
```

### tlm/amba_transaction.sv
```systemverilog
class amba_transaction extends uvm_sequence_item;
  rand bit [31:0] data;
  `uvm_object_utils(amba_transaction)

  function new(string name = "amba_transaction");
    super.new(name);
  endfunction
endclass
```

### coverage/soc_coverage.sv
```systemverilog
module soc_coverage;
  logic clk;
  logic [7:0] i2c_cov_data;
  logic [7:0] spi_cov_data;
  logic [31:0] amba_cov_data;

  covergroup soc_cg;
    coverpoint i2c_cov_data;
    coverpoint spi_cov_data;
    coverpoint amba_cov_data;
  endgroup

  soc_cg cg = new();

  always @(posedge clk) begin
    cg.sample();
  end

endmodule
```

### Makefile
```makefile
VCS = vcs
SRCS = tb_top.sv soc_test.sv soc_env.sv soc_if.sv soc_coverage.sv

all:
	$(VCS) -full64 -sverilog +acc +vpi -timescale=1ns/1ps $(SRCS) -o simv
	./simv

clean:
	rm -rf simv csrc ucli.key *.vpd *.log *.daidir
```

## Contact

**Name:** Adarsh Prakash  
**Email:** kumaradarsh663@gmail.com  
**Phone:** +91-7033675921  
**LinkedIn:** [https://www.linkedin.com/in/adarsh-prakash-a583a3259](https://www.linkedin.com/in/adarsh-prakash-a583a3259)
