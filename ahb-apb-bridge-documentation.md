# AHB to APB Bridge Project UVM Verification
Single Master and Single Slave
## 📋 Table of Contents
1. [Protocol Features](#1-protocol-features)
   * AHB Features
   * APB Features
   * Protocol Comparison
2. [Implementation Details](#2-implementation-details)
   * Verification Components
   * Test Scenarios
3. [AHB Transfer Sequences](#3-ahb-transfer-sequences)
   * Base Sequence
   * Single Transfer
   * Increment Burst
   * Wrap Burst
4. [Verification Results](#4-verification-results)

## 1. Protocol Features

### 🚀 Advanced High-Performance Bus (AHB) Features
* High-performance, high-bandwidth protocol
* Pipelined operations support
* Multiple bus masters
* Burst transfers
* Split transactions
* Single-clock edge operation
* Non-tristate implementation
* Supports frequencies up to ~150MHz
* Data bus widths of 32/64/128 bits
* Address bus width up to 32 bits
* Multiple outstanding transactions capability

### 🔌 Advanced Peripheral Bus (APB) Features
* Low power peripheral communication
* Simplified interface protocol
* Latched address and control signals
* No burst transfers
* Suitable for low-speed peripherals
* Simple interface with minimal power consumption
* Typically 32-bit data bus width
* Non-pipelined protocol
* Two-stage transaction (setup + enable)
* Single master only

### 📊 Protocol Comparison Table

| Feature | AHB | APB |
|---------|-----|-----|
| Performance | High-performance | Low-power, simple |
| Bandwidth | High | Low to medium |
| Complexity | Complex | Simple |
| Pipelining | Yes | No |
| Burst Transfer | Supported | Not supported |
| Logic Level | Logic Low(0),Logic High(1) |Logic Low(0),Logic High(1)  |
| Transaction Stages | Single stage | Two stages (Setup + Enable) |
| Target Devices | High-speed memories, DMA | Low-speed peripherals |
| Power Consumption | Higher | Lower |
| Data Bus | 32/64/128-bit | Typically 32-bit |
| Outstanding Transactions | Multiple | Single |
| Wait States | Supported | Supported |

## 2. Implementation Details

### 🛠 Verification Components
1. **AHB to APB Bridge**
   * Complete UVM-based verification environment
   * Protocol conversion verification
   * Data integrity checks
   * Error scenarios handling

2. **AHB Side Verification**
   * Master agent implementation
   * Protocol compliance checks
   * Burst transfer verification
   * Wait state handling

3. **APB Side Verification**
   * Slave response verification
   * Setup and enable phase checks
   * Error response handling
   * Transfer completion verification

### 🧪 Test Scenarios
1. **Basic Transfers**
   * Single read/write operations
   * Back-to-back transfers
   * Idle cycles

2. **Advanced Features**
   * Burst transfers on AHB side
   * Wait state insertion
   * Error responses
   * Reset during transfer
   * Different transfer sizes

## 3. AHB Transfer Sequences

### 🔤 Base Sequence Class
```systemverilog
class master_seqs extends uvm_sequence #(master_xtn);
    `uvm_object_utils(master_seqs)
    
    logic [31:0] haddr;
    logic hwrite;
    logic [2:0] hsize;
    logic [2:0] hburst;
```

**Key Attributes:**
* Base class for all AHB transfer sequences
* Defines common transaction parameters
* Supports UVM sequence infrastructure

### 📡 Single Transfer Sequence
**Implementation Features:**
* Non-sequential transfers (HTRANS=2'b01)
* Single transfer mode (HBURST=3'b000)
* Write operations (HWRITE=1'b1)
* Independent transfers without address incrementing

### 🔢 Increment Burst Sequence
**Supported Burst Types:**
* INCR4 (3'b011)
* INCR8 (3'b101)
* INCR16 (3'b111)
* INCR (3'b001)

**Address Calculation:**
```
New_Address = Previous_Address + (2^Size)
```

### 🔄 Wrap Burst Sequence
**Supported Burst Types:**
* WRAP4 (3'b010)
* WRAP8 (3'b100)
* WRAP16 (3'b110)

**Boundary Calculations:**
```
Starting_addr = (haddr/((2^hsize) * Length)) * ((2^hsize) * Length)
Boundary_addr = Starting_addr + ((2^hsize) * Length)
```

**Example (WRAP4, 2-byte transfer):**
* Initial address: 24
* Starting address: (24/(4*2))*(4*2) = 24
* Boundary address: 24 + (2*4) = 32
* Sequence: 24 → 26 → 28 → 30 → 24 (wraps)

Comparing based on the Haddr, Paddr, and Hwdata, Pwdata
The key operation happens under these conditions:
1. Hwrite == 1: Indicates a write operation
2. Hwrite == 0: Indicates a read operation
3. Hsize == 2'b00: Specifies 1-byte data transfer (2^0 = 1 byte)
            2'b01: 2 bytes (16 bits)
            2'b10: 4 bytes (32 bits)
            2'b11: 8 bytes (64 bits)
   
Example-
Hwrite== 1
Hsize== 2'b00 (1bytes)
Haddr== 2e882088

Haddr== 2e 88 20 88
Byte position:    4      3      2      1
according to hsize it will going to select haddr byte, and from 1 byte it will select the 8=1000
last 2 bit data for data transfer, checking for last 2 bits
it will check for Haddr and Paddr if yes ADDRESS are sucessfully matching otherwise ADDRESS Mismatching

Hwdata=3e5f2294
Byte position:    4      3      2      1
Hex value:      3E     5F     22     94
2-bit value:    11     10     01     00
it will check for 94 value in Pdata if yes DATA are sucessfully matching otherwise DATA are NOT matching.

## 4. Verification Guidelines

### 🎯 Coverage Points
1. **Transfer Types**
   * All burst types
   * All transfer sizes
   * Address boundaries
   * Wrap conditions

2. **Protocol Transitions**
   * AHB to APB conversion
   * Wait states
   * Error responses

### 🧪 Test Requirements
1. **Basic Functionality**
   * Single transfers
   * Burst transfers
   * Address alignment

2. **Corner Cases**
   * Boundary conditions
   * Error scenarios
   * Reset behavior

3. **Performance**
   * Throughput
   * Latency
   * Bus utilization


*For any updates or modifications, please contact the verification team.*
