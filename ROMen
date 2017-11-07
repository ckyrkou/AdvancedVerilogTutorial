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
