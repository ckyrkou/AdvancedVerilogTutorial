**Advanced Verilog Tutorial**

These short tutorial includes small tips for hardware design using Verilog HDL ®. The concepts discussed assume that you have some familiarity with Verilog design.

* [***Signed and Unsigned Numbers***](MAC.v)

Many applications require using signed numbers as well as unsigned numbers. Verilog has built in support for signed numbers which makes it easier to build circuits for signed number processing. But first in order to appreciate the built-in support lets consider how would we would implement an adder without signed number support. When working with signed numbers  the first thing that you will need to do is to always check the signs of the numbers you are going to add together. If one of them is negative we would need to perform subtraction instead of addition. In the case where both are negative then an addition of their positive counterparts would suffice, however, the result must be negated. You can see that the circuit design becomes complicated and for large designs it can be difficult to manage. Thankfully by using the _**signed**_ keyword in verilog the synthesis tool performs all these operations automatically and implements an optimized circuit without unnecessary hardware that may result with the previous approach. Below you can find an example on how to use the signed keyword to implement a signed multiply-accumulator. In this example the input B is signed and the input A is unsigned. Hence the accumulation result *P* is also signed. Since *A* is unsigned we use the Verilog built-in function **$signed** to set the sign of input *A* as **0**. No special treatment is needed for signals *B* and *P* which are defined as signed. 

```verilog
module MAC(clock,reset,enable,A,B,P);

parameter Abitwidth=8;
parameter Bbitwidth=12;
parameter Pbitwidth=62;

input clock,reset,enable;

input [Abitwidth-1:0] A;
input signed[Bbitwidth-1:0] B;

output reg signed[Pbitwidth-1:0] P=0;

always@(posedge clock)begin
    if(reset)
        P <= 0;
    else
    if(enable)
        P <= P + $signed({1'b0,A})*B;
end

endmodule
```

* ***Initializing Block Ram Memories***

Block Ram initialization is a very important function provided by Verilog as it enables the design of Look-Up Tables used to implement one-to-one functions and hold processing data. The best way to initialize a memory in Verilog is by using the built-in function **$readmemb(filename,ram)** or **$readmemh(filename,ram)** which initializes the memory array *ram* using a text file with name *filename* of binary and hexadecimal data respectively. Each row of the text file represents one block in the memory either in binary and hexadecimal format. Other ways to initialize a memory include using a for loop to set the memory array locations to specific values, however, in my own personal experience I found that this may not be the best practice as the synthesizer may ignore this initialization. Hence, using the built-in functions is the preferred option. An example code using the **$readmemb** functions follows next. 

```verilog
/*
Example format of the text file for eight bit data. First line corresponds to the zero array position 

10100011
10101101
00110111
00000000
00000111
.
.
.
.
.
01010101

*/

module ROMen(clock, address, enable, dataOut);

parameter blockLength = 8;
parameter memDepth = 400;
parameter file = "data.txt";
parameter addressBitWidth=9;

    input  clock,enable; 
    input  [addressBitWidth-1:0] address; 
    output reg [blockLength-1:0] dataOut=0; 
    reg [blockLength-1:0] ram [0:memDepth-1]; 

    initial
    begin
        $readmemb(file,ram);
    end

    always @(posedge clock) 
    begin 
        if(enable)
            dataOut <= ram[address];
        else
            dataOut <= 0;
    end

endmodule
```

* ***Use Functions to Make Modules Generic***

Generic modules are key to managing large designs and facilitating code re-usability. An important aspect of making modules generic and reusable is calculate signal bitwidths dynamically as parameters change. For example we would like to automatically compute the bitwidth for a memory address signal by only specifying the number of elements in the memory. To do so we need to compute the logarithm with base 2 of the number of elements. This function is not built inside Verilog but the language provides us with the tools to create our own functions. Hence we implement  **log2()** function that gets a number and returns the number of bits needed to represent the number thus making it easier to make our modules generic. Custom functions can reside in a separate Verilog file which can be included to the design so all modules can use those functions. An example of the **log2()** function is show below.

``` verilog
function integer log2;
    input integer value;
    begin
        value = value-1;
        for (log2=0; value>0; log2=log2+1)
           value = value>>1;
    end
endfunction
```
