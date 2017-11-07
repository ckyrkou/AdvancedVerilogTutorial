**Advanced Verilog Tutorial**

These short tutorial includes small tips for hardware design using Verilog HDL ®. The concepts discussed assume that you have some familiarity with Verilog design.

* ***Signed and Unsigned Numbers***

Many applications require using signed numbers as well as unsigned numbers. Verilog has built in support for signed numbers which makes it easier to build circuits for signed number processing. But first in order to appreciate the built-in support lets consider how would we would implement an adder without signed number support. When working with signed numbers  the first thing that you will need to do is to always check the signs of the numbers you are going to add together. If one of them is negative we would need to perform subtraction instead of addition. In the case where both are negative then an addition of their positive counterparts would suffice, however, the result must be negated. You can see that the circuit design becomes complicated and for large designs it can be difficult to manage. Thankfully by using the *signed* keyword in verilog the synthesis tool performs all these operations automatically and implements an optimized circuit without unnecessary hardware that may result with the previous approach. Below you can find an example on how to use the signed keyword to implement a signed multiply-accumulator. In this example the input B is signed and the input A is unsigned. Hence the accumulation result P is also signed. Since A is unsigned we use the Verilog built-in function **$signed** to set the sign of input A as 0. No special treatment is needed for signals B and P which are defined as signed. 

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

