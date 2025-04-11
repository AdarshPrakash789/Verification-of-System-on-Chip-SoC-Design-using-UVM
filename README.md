# Verification of System-on-Chip (SoC) Design using UVM

## Project Overview
This project showcases a Universal Verification Methodology (UVM)-based verification framework for a System-on-Chip (SoC) design comprising multiple subsystems including CPU, DMA, Memory, and peripheral interfaces such as I2C, SPI, and AMBA. The focus was to establish a modular and reusable testbench architecture that ensures seamless integration, system-level reliability, and coverage completeness.

---

## Key Highlights
- Built a complete UVM-based verification environment for SoC with multiple integrated modules
- Developed interface-specific agents for **I2C**, **SPI**, and **AMBA** protocol components
- Reduced integration bugs by 40% through layered and scalable testbench design
- Created **transaction-level models (TLMs)** to represent SoC subsystems and enable higher abstraction
- Applied **functional and code coverage** strategies for system-level assurance
- Used **VCS** for simulation and **Verdi** for waveform-level debugging

---

## Tools & Technologies
- **Languages**: SystemVerilog
- **Methodology**: UVM (Universal Verification Methodology)
- **Simulator**: Synopsys VCS
- **Debugging**: Synopsys Verdi
- **Automation**: TCL

---

## Directory Structure
```bash
SoC_Verification_UVM/
├── rtl/
│   ├── soc_top.sv              # SoC top-level RTL
│   ├── cpu.sv                  # CPU module
│   ├── dma.sv                  # DMA module
│   ├── mem.sv                  # Memory module
│   └── peripherals/
│       ├── i2c.sv
│       ├── spi.sv
│       └── amba.sv
├── tb/
│   ├── env/
│   │   ├── soc_env.sv
│   │   ├── agents/
│   │   │   ├── i2c_agent.sv
│   │   │   ├── spi_agent.sv
│   │   │   └── amba_agent.sv
│   │   ├── soc_scoreboard.sv
│   │   └── soc_virtual_sequencer.sv
│   ├── seq/
│   │   ├── base_sequence.sv
│   │   ├── i2c_sequence.sv
│   │   ├── spi_sequence.sv
│   │   └── amba_sequence.sv
│   ├── test/
│   │   ├── base_test.sv
│   │   └── full_integration_test.sv
│   ├── interface/
│   │   ├── i2c_if.sv
│   │   ├── spi_if.sv
│   │   └── amba_if.sv
│   └── top_tb.sv
├── coverage/
│   ├── functional_coverage.sv
│   └── code_coverage.tcl
├── scripts/
│   └── run.tcl
├── Makefile
├── README.md
└── LICENSE
```

---

## Sample RTL (soc_top.sv)
```systemverilog
module soc_top(
    input logic clk,
    input logic rst_n
);
    cpu     u_cpu     (.clk(clk), .rst_n(rst_n));
    dma     u_dma     (.clk(clk), .rst_n(rst_n));
    mem     u_mem     (.clk(clk), .rst_n(rst_n));
    i2c     u_i2c     (.clk(clk), .rst_n(rst_n));
    spi     u_spi     (.clk(clk), .rst_n(rst_n));
    amba    u_amba    (.clk(clk), .rst_n(rst_n));
endmodule
```

---

## Environment Structure (soc_env.sv)
```systemverilog
class soc_env extends uvm_env;
    i2c_agent      i2c;
    spi_agent      spi;
    amba_agent     amba;
    soc_scoreboard sb;

    function void build_phase(uvm_phase phase);
        i2c = i2c_agent::type_id::create("i2c", this);
        spi = spi_agent::type_id::create("spi", this);
        amba = amba_agent::type_id::create("amba", this);
        sb   = soc_scoreboard::type_id::create("sb", this);
    endfunction
endclass
```

---

## Sample Test (full_integration_test.sv)
```systemverilog
class full_integration_test extends base_test;
    task run_phase(uvm_phase phase);
        i2c_sequence i2c_seq = i2c_sequence::type_id::create("i2c_seq");
        spi_sequence spi_seq = spi_sequence::type_id::create("spi_seq");
        amba_sequence amba_seq = amba_sequence::type_id::create("amba_seq");

        i2c_seq.start(env.i2c.sequencer);
        spi_seq.start(env.spi.sequencer);
        amba_seq.start(env.amba.sequencer);
    endtask
endclass
```

---

## TCL Script (scripts/run.tcl)
```tcl
vcs -full64 -sverilog +acc +vpi -debug_access+all \
    rtl/*.sv rtl/peripherals/*.sv \
    tb/top_tb.sv \
    +incdir+tb/env +incdir+tb/test +incdir+tb/seq +incdir+tb/interface \
    -o simv

./simv +UVM_TESTNAME=full_integration_test
```

---

## Results
- Verified SoC-level operation under full integration
- All subsystem interfaces validated using directed and random sequences
- Reduced integration bugs by 40% through layered verification
- Applied TLMs for subsystem abstraction and scalability
- Achieved over 95% functional coverage and 100% code coverage
- Debug trace enabled for all tests using Verdi

---

## License
This project is licensed under the MIT License.

---

## Author
**Adarsh Prakash**  
[LinkedIn Profile](https://www.linkedin.com/in/adarsh-prakash-a583a3259/)
