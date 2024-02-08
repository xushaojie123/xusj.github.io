---
title: Multiply
date: 2024-02-08 23:48:48
tags:
---
---
## 1 **Spec** ————A 32-bits Multiplier
---
### Input
| Signal | Bits | Function |
| :----:| :----: | :----: |
| clk | 1 | Clock |
| mult_begin | 1 | begin multiply signal |
| mult_op1 | 32 | 32bits multiplier |
| mult_op2 | 32 | 32bits multiplier |

### Output
| Signal | Bits | Function |
| :----:| :----: | :----: |
| product | 64 | multiply product |
| mult_end | 1 | end multiply signal |
---
    
## 2 **Design**
---
### 2.1 *multiply.v*
``` Verilog
`timescale 1ns / 1ps

module multiply(             
    input         clk,        // 时钟
    input         mult_begin, // 乘法开始信号
    input  [31:0] mult_op1,   // 乘法源操作数1
    input  [31:0] mult_op2,   // 乘法源操作数2
    output [63:0] product,    // 乘积
    output        mult_end   // 乘法结束信号
);
    //乘法正在运算信号和结束信号
    reg mult_valid;
    reg  [31:0] multiplier;

    assign mult_end = mult_valid & ~(|multiplier); //乘法结束信号：乘数全0
    always @(posedge clk)   
    begin
        // if (!mult_begin || mult_end)    //如果没有开始或者已经结束了
        if (multiplier == 32'd0)
        begin
            mult_valid <= 1'b0;     //mult_valid 赋值成0，说明现在没有进行有效的乘法运算
        end
        else
        begin
            mult_valid <= 1'b1;
       //     test <= 1'b1;
        end
    end

    //两个源操作取绝对值，正数的绝对值为其本身，负数的绝对值为取反加1
    wire        op1_sign;      //操作数1的符号位
    wire        op2_sign;      //操作数2的符号位
    wire [31:0] op1_absolute;  //操作数1的绝对值
    wire [31:0] op2_absolute;  //操作数2的绝对值
    assign op1_sign = mult_op1[31];
    assign op2_sign = mult_op2[31];
    assign op1_absolute = op1_sign ? (~mult_op1+1) : mult_op1;
    assign op2_absolute = op2_sign ? (~mult_op2+1) : mult_op2;
    //加载被乘数，运算时每次左移一位
    reg  [63:0] multiplicand;
    always @ (posedge clk)  
    begin
        if (mult_valid)
        begin    // 如果正在进行乘法，则被乘数每时钟左移一位
            multiplicand <= {multiplicand[62:0],1'b0};  //被乘数x每次左移一位。
        end
        else if (mult_begin) 
        begin   // 乘法开始，加载被乘数，为乘数1的绝对值
            multiplicand <= {32'd0,op1_absolute};
        end
    end

    //加载乘数，运算时每次右移一位，相当于y
    
    always @ (posedge clk) 
    begin
    if(mult_valid)
    begin       //如果正在进行乘法，则乘数每时钟右移一位
         multiplier <= {1'b0,multiplier[31:1]}; //相当于乘数y右移一位
    end
    else if(mult_begin)
    begin   //乘法开始，加载乘数，为乘数2的绝对值
        multiplier <= op2_absolute;
        end
    end
    // 部分积：乘数末位为1，由被乘数左移得到；乘数末位为0，部分积为0
    wire [63:0] partial_product;
    assign partial_product = multiplier[0] ? multiplicand:64'd0;        //若此时y的最低位为1，则把x赋值给部分积partial_product，否则把0赋值给partial_product
    
    //累加器
    reg [63:0] product_temp;		//临时结果
    always @ (posedge clk)  //
    begin
        if (mult_valid)
        begin
            product_temp <= product_temp + partial_product;
        end      
        else if (mult_begin)
        begin
        product_temp <= 64'd0;
        end
     end
     
    //乘法结果的符号位和乘法结果
    reg product_sign;	//乘积结果的符号
    always @ (posedge clk)  
    begin
        if (mult_valid)
        begin
              product_sign <= op1_sign ^ op2_sign;
        end
    end 
    //若乘法结果为负数，则需要对结果取反+1
    
    assign product = product_sign ? (~product_temp+1) : product_temp;
endmodule
```
### 2.2 *testbench.v*
``` Verilog
`timescale 1ns / 1ps
`define cycles  40
module tb;

	// Inputs
	reg clk;
	reg mult_begin;
	reg [31:0] mult_op1;
	reg [31:0] mult_op2;
	reg [63:0] Data_in_t;
	// Outputs
	wire [63:0] product;
	wire mult_end;

	// Instantiate the Unit Under Test (UUT)
	multiply uut (
		.clk(clk), 
		.mult_begin(mult_begin), 
		.mult_op1(mult_op1), 
		.mult_op2(mult_op2), 
		.product(product), 
		.mult_end(mult_end)
	);
	integer i,errors[0:39],cnt;
	
	integer fd = 0;
	initial begin
		// Initialize Inputs
		clk = 0;
		mult_begin = 0;
		mult_op1 = 0;
		mult_op2 = 0;
		for (i = 0; i < 40; i = i + 1)
			begin
			errors[i] = 0;
			end
		i=0;
		cnt=0;
		$dumpfile("mul.vcd");

		$dumpvars();
		fd = $fopen("./report.txt", "w");
		// if(!fd)
		// 	begin
		// 		$display("Could not open File \r");
		// 		$stop;
		// 	end

		#1000;
		//正数*正数
		mult_begin = 1;
		mult_op1 = 32'H6A98F28D;
		mult_op2 = 32'H7E184AD4;
		Data_in_t = 64'H348164E09FFD9EC4;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H2B661ACE;
		mult_op2 = 32'H6AE5B749;
		Data_in_t = 64'H121F3881A38CE6BE;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H25BD7BEE;
		mult_op2 = 32'H4ADDFFD9;
		Data_in_t = 64'H0B09801E84861EBE;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H0D3A404E;
		mult_op2 = 32'H64B23883;
		Data_in_t = 64'H0533F68AB11BF7EA;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H3F64172B;
		mult_op2 = 32'H60CE4AB1;
		Data_in_t = 64'H17F89DB9878072BB;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H16761A51;
		mult_op2 = 32'H13462A55;
		Data_in_t = 64'H01B0EBF60AAE06E5;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H2026D659;
		mult_op2 = 32'H6D726BE8;
		Data_in_t = 64'H0DBEE81CB76B73A8;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H02618586;
		mult_op2 = 32'H1E83A59C;
		Data_in_t = 64'H0048A717560EBBA8;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H1B77F796;
		mult_op2 = 32'H5B4B942D;
		Data_in_t = 64'H09CBC10E0A2B3D5E;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H5AC08456;
		mult_op2 = 32'H64ED2634;
		Data_in_t = 64'H23C745771E5DA578;
		#50;
		mult_begin = 0;
		#1000;
		//负数*负数
		mult_begin = 1;
		mult_op1 = 32'HDC7F53A9;
		mult_op2 = 32'H9F5CEA89;
		Data_in_t = 64'H0D66DE886A583F71;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'HB7AC2234;
		mult_op2 = 32'HD3FCAAC7;
		Data_in_t = 64'H0C6F5B2E9CB51E6C;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'HDAE39537;
		mult_op2 = 32'HD347F65B;
		Data_in_t = 64'H067B902D3789E48D;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H9F1D7AEA;
		mult_op2 = 32'HFA3E1FC8;
		Data_in_t = 64'H022DCC3B29965CD0;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H9F09D52B;
		mult_op2 = 32'H99A59337;
		Data_in_t = 64'H26C454CFE83B7D3D;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H90E87B84;
		mult_op2 = 32'HEAA39889;
		Data_in_t = 64'H09450737E2CC79A4;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'HEA722216;
		mult_op2 = 32'HF7B63872;
		Data_in_t = 64'H00B2A530D3EBFDCC;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'HB25BF9E7;
		mult_op2 = 32'H9EAEE22C;
		Data_in_t = 64'H1D83C041476EE1B4;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'HE23F69BC;
		mult_op2 = 32'H804F5678;
		Data_in_t = 64'H0ED712A6FC42B820;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H81A763DE;
		mult_op2 = 32'HE857F5F4;
		Data_in_t = 64'H0BACE522E690A598;
		#50;
		mult_begin = 0;
		#1000;
		//正数*负数
		mult_begin = 1;
		mult_op1 = 32'H117B72B5;
		mult_op2 = 32'HFDE88F42;
		Data_in_t = 64'HFFDB6F504BEEADAA;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H2EF80EBF;
		mult_op2 = 32'HDE0A0A5F;
		Data_in_t = 64'HF9C4E5A25416EEE1;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H56FA268C;
		mult_op2 = 32'H86F6EBA9;
		Data_in_t = 64'HD6E0AE135F0DF66C;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H77A17497;
		mult_op2 = 32'HD367D395;
		Data_in_t = 64'HEB29235711D250E3;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H5DE04686;
		mult_op2 = 32'HD29727E2;
		Data_in_t = 64'HEF59213D8FC6AC4C;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H0F50B083;
		mult_op2 = 32'H844E2D5D;
		Data_in_t = 64'HF89997CD13412697;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H671294E9;
		mult_op2 = 32'HE6CCC285;
		Data_in_t = 64'HF5DA8E00A12BEF0D;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H23B50CC7;
		mult_op2 = 32'HCFD68C3B;
		Data_in_t = 64'HF9484575D510C5DD;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H60F5B7D8;
		mult_op2 = 32'HFA36D021;
		Data_in_t = 64'HFDCF0059DC9C32D8;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H4E520906;
		mult_op2 = 32'HC45F3B23;
		Data_in_t = 64'HEDC1E86B8E859DD2;
		#50;
		mult_begin = 0;
		#1000;
		//0*随机
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'H51B64CC8;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'H7BA49B33;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'HF937655D;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'H72061A95;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'HA31321B7;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'H8532E4B1;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'HCC480B3E;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'HB712E17B;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'HFF3BAF67;
		Data_in_t = 64'H0000000000000000;
		#50;
		mult_begin = 0;
		#1000;
		mult_begin = 1;
		mult_op1 = 32'H00000000;
		mult_op2 = 32'H6D0E5A16;
		Data_in_t = 64'H0000000000000001;
		#50;
		mult_begin = 0;

		#500;
		if (cnt == 0)
		begin
			$display("Simulation finished Successfully.");
			$fdisplay(fd, "Simulation finished Successfully.");
		end
		else if (cnt >= 1)
		begin	
			$display("%0d ERROR! See log above for details.",cnt);
			$fdisplay(fd, "%0d ERROR! See log above for details.",cnt) ;
		end
		$fclose(fd);

		// $finish;
	end
   always #5 clk = ~clk;

//比较
	always @ (negedge mult_end)
		begin
		if ( product != Data_in_t )
			begin
			$display(" ------ERROR. A mismatch has occurred-----,ERROR in ", i);
			$fdisplay(fd," ------ERROR. A mismatch has occurred-----,ERROR in ", i);
			errors[i-1] =  1;
			cnt = cnt + 1;
			end
		i = i + 1; 
		
		end
endmodule

```
### 2.3 *q1_error_case1.v*
``` Verilog
module top_module (
    input clk,
    input reset,      
    input data,
    output start_shifting);

    parameter IDLE = 3'd0, S1 = 3'd1, S2 = 3'd2；
    parameter S3 = 3'd3, OUT = 3'd4;
    wire [2:0] current_state, next_state; 

    always @(*) begin
        Case(current_state) 
            IDLE:       next_state = data ? S1 : IDLE 
            S1:         next_state = data ? S2 : IDLE;
            S2:         next_state = data ? S2 : S3;
            S3:         next_state = data ? OUT : IDLE;
            OUT:        next_state = OUT;
            default:    next_state = IDLE;
        endcsae 
    end

    always @(posedge clk) begin
        if(reset) begin
            current_state <= IDLE;
        end
        else begin
            current_state <= next_state;
        end
    end

    assign start_shifting = current_state = OUT;

endmodule```
### 2.4 *q1_error_case2.v*
``` Verilog
module top_module (
    input clk,
    input reset,      // Synchronous reset
    input data,
    output start_shifting);

    parameter IDLE = 3'd0, S1 = 3'd1, S2 = 3'd2;
    parameter S3 = 3'd3, OUT = 3'd4;
    reg [2:0] current_state, next_state;

    always @(*) begin
        case(current_state)
            IDLE:       next_state = data ? S1 : IDLE;
            S1:         next_state = data ? S2 : IDLE;
            S2:         next_state = data ? S2 : S3;
            S3:         next_state = data ? OUT : IDLE;
            OUT:        next_state = OUT;
            default:    next_state = IDLE;
        endcase
    end

    always @(posedge clk) begin
        if(reset) begin
            current_state <= IDLE;
        end
        else begin
            current_state <= next_state;
        end

    assign start_shifting = current_state == OUT;

endmodule
```
### 2.5 *q1_error_case3.v*
``` Verilog
module top_module (
    input clk,
    input reset,    
    input data,
    output start_shifting);

    parameter IDLE = 0, S1 = 1, S2 = 10;
    parameter S3 = 11, OUT = 100;
    reg [2:0] current_state, next_state;

    always @(*) begin
        case(current_state)
            IDLE:       next_state = data ? S1 : IDLE;
            S1:         next_state = data ? S2 : IDLE;
            S2:         next_state = data ? S2 : S3;
            S3:         next_state = data ? OUT : IDLE;
            OUT:        next_state = OUT;
            default:    next_state = IDLE;
        endcase
    end

    always @(posedge clk) begin
        if(reset) begin
            current_state <= IDLE;
        end
        else begin
            current_state <= next_state;
        end
    end

    assign start_shifting = current_state == OUT;

endmodule```
### 2.6 *q1_error_case4.v*
``` Verilog
module top_module (
    input clk,
    input reset,      // Synchronous reset
    input data,
    output start_shifting);

    parameter IDLE = 3`d0, S1 = 3'd1, S2 = 3'd2; 
    parameter S3 = 3'd3, OUT = 3'd4;
    reg [2:0] current_state, next_state;

    always @(*) begin
        case(current_state)
            IDLE:       next_state = data ? S1 : IDLE;
            S1:         next_state = data ? S2 : IDLE;
            S2:         next_state = data ? S2 : S3;
            S3:         next_state = data ? OUT : IDLE;
            OUT:        next_state = OUT;
            default:    next_state = IDLE;
        endcase
    end

    always @(posedge clk) begin
        if(reset) begin
            current_state <= IDLE;
        end
        else begin
            current_state <= next_state;
        end
    end

    assign start_shifting = current_state == OUT;

endmodule```
### 2.7 *q1_error_case5.v*
``` Verilog

module top_module (
    input clk,
    input reset,      // Synchronous reset
    input table,
    output start_shifting);

    parameter IDLE = 3'd0, S1 = 3'd1, S2 = 3'd2;
    parameter S3 = 3'd3, OUT = 3'd4;
    reg [2:0] current_state, next_state;

    always @(*) begin
        case(current_state)
            IDLE:       next_state = table ? S1 : IDLE;
            S1:         next_state = table ? S2 : IDLE;
            S2:         next_state = table ? S2 : S3;
            S3:         next_state = table ? OUT : IDLE;
            OUT:        next_state = OUT;
            default:    next_state = IDLE;
        endcase
    end

    always @(posedge clk) begin
        if(reset) begin
            current_state <= IDLE;
        end
        else begin
            current_state <= next_state;
        end
    end

    assign start_shifting = current_state == OUT;

endmodule```
### 2.8 *q1_error_case6.v*
``` Verilog
module top_module (
    input clk,
    input reset,      // Synchronous reset
    input data,
    output start_shifting);

    parameter IDLE = 3'd0, S1 = 3'd1, S2 = 3'd2;
    parameter S3 = 3'd3, OUT = 3'd4;
    reg [2:0] current_state, next_state;

    always 
    begin
        case(current_state)
            IDLE:       next_state = data ? S1 : IDLE;
            S1:         next_state = data ? S2 : IDLE;
            S2:         next_state = data ? S2 : S3;
            S3:         next_state = data ? OUT : IDLE;
            OUT:        next_state = OUT;
            default:    next_state = IDLE;
        endcase
    end

    always @(posedge clk) begin
        if(reset) begin
            current_state <= IDLE;
        end
        else begin
            current_state <= next_state;
        end
    end

    assign start_shifting = current_state == OUT;

endmodule```
---
## 3 **Report**
---
### 3.1 *Compile Report*<p align="right">**Errors: 0, Warnings: 0**</p>
```
Model Technology ModelSim SE-64 vlog 10.7 Compiler 2017.12 Dec  7 2017
Start time: 23:48:46 on Feb 08,2024
vlog -work work ./design/multiply.v ./design/testbench.v -l vcompile.txt 
-- Compiling module multiply
-- Compiling module tb

Top level modules:
	tb
End time: 23:48:47 on Feb 08,2024, Elapsed time: 0:00:01
Errors: 0, Warnings: 0
```
### 3.2 *Simulation Report*<p align="right">**Errors: 0, Warnings: 0**</p>
```
# vsim -voptargs="+acc" work.tb -l ./vsim.txt -wlf ./vsim.wlf 
# Start time: 23:48:47 on Feb 08,2024
# ** Note: (vsim-8009) Loading existing optimized design _opt
# //  ModelSim SE-64 10.7 Dec  7 2017
# //
# //  Copyright 1991-2017 Mentor Graphics Corporation
# //  All Rights Reserved.
# //
# //  ModelSim SE-64 and its associated documentation contain trade
# //  secrets and commercial or financial information that are the property of
# //  Mentor Graphics Corporation and are privileged, confidential,
# //  and exempt from disclosure under the Freedom of Information Act,
# //  5 U.S.C. Section 552. Furthermore, this information
# //  is prohibited from disclosure under the Trade Secrets Act,
# //  18 U.S.C. Section 1905.
# //
# Loading work.tb(fast)
# Loading work.multiply(fast)
#  ------ERROR. A mismatch has occurred-----,ERROR in          40
# 1 ERROR! See log above for details.
# quit
# End time: 23:48:48 on Feb 08,2024, Elapsed time: 0:00:01
# Errors: 0, Warnings: 0
```
### 3.3 *TestBench Report*
```
 ------ERROR. A mismatch has occurred-----,ERROR in          40
1 ERROR! See log above for details.
```
