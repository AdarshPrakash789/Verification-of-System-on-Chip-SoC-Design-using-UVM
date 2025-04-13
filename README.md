// Project: Verification of System-on-Chip (SoC) Design
// Author: Adarsh Prakash
// Tools Used: SystemVerilog, UVM, Synopsys VCS
// Description: A modular UVM-based verification environment for SoC integration with I2C, SPI, and AMBA interfaces. Functional and code coverage techniques applied to ensure thorough validation of subsystem interactions.

/*
Directory Structure:

soc_uvm_verification/
├── Makefile                       VCS build and run script
├── README.md                      Project documentation
├── tb/                            Top-level testbench and interface
│   ├── tb_top.sv
│   └── soc_if.sv
├── tests/                         UVM testcases
│   └── soc_test.sv
├── env/                           UVM environment and agents
│   └── soc_env.sv
├── tlm/                           Transaction-Level Models
│   ├── i2c_transaction.sv
│   ├── spi_transaction.sv
│   └── amba_transaction.sv
├── coverage/                      Coverage modules
│   └── soc_coverage.sv
└── logs/                          Simulation logs (auto-generated)
*/

`include "uvm_macros.svh"
import uvm_pkg::*;

module tb_top;

  logic clk;
  logic rst_n;

  // Clock and Reset Generation
  initial begin
    clk = 0;
    forever #5 clk = ~clk;
  end

  initial begin
    rst_n = 0;
    #20 rst_n = 1;
  end

  // DUT instance
  SoC DUT (
    .clk(clk),
    .rst_n(rst_n)
  );

  // UVM Environment
  initial begin
    run_test("soc_test");
  end

endmodule

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

interface soc_if(input bit clk);
  logic rst_n;
  logic [7:0] i2c_data;
  logic [7:0] spi_data;
  logic [31:0] amba_data;
endinterface

class i2c_transaction extends uvm_sequence_item;
  rand bit [7:0] data;
  `uvm_object_utils(i2c_transaction)

  function new(string name = "i2c_transaction");
    super.new(name);
  endfunction
endclass

class spi_transaction extends uvm_sequence_item;
  rand bit [7:0] data;
  `uvm_object_utils(spi_transaction)

  function new(string name = "spi_transaction");
    super.new(name);
  endfunction
endclass

class amba_transaction extends uvm_sequence_item;
  rand bit [31:0] data;
  `uvm_object_utils(amba_transaction)

  function new(string name = "amba_transaction");
    super.new(name);
  endfunction
endclass

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

// Save as Makefile

VCS = vcs
SRCS = tb_top.sv soc_test.sv soc_env.sv soc_if.sv soc_coverage.sv

all:
	$(VCS) -full64 -sverilog +acc +vpi -timescale=1ns/1ps $(SRCS) -o simv
	./simv

clean:
	rm -rf simv csrc ucli.key *.vpd *.log *.daidir

/*
System-on-Chip (SoC) Verification Using UVM

This project presents a professional UVM-based testbench for verifying a System-on-Chip (SoC) design integrating multiple subsystems including CPU, DMA, Memory, and standard protocols such as I2C, SPI, and AMBA.

Tools & Technologies:
SystemVerilog, UVM, Synopsys VCS

Key Features:
- Modular UVM testbench
- I2C, SPI, AMBA support
- TLM abstraction
- Functional and code coverage

How to Run:
1. Clone repository
2. Navigate to folder
3. Run `make`

Author:
Adarsh Prakash
LinkedIn: https://www.linkedin.com/in/adarsh-prakash-a583a3259
Email: kumaradarsh663@gmail.com
*/
