
# Button Debounce

A switch debouncer is a circuit used in digital electronics to remove the bouncing effect that occurs when a mechanical switch or button is pressed. When a switch is pressed, the metal contacts inside it can make and break contact multiple times in a short period of time due to the physical properties of the switch, resulting in a rapid series of ON-OFF signals known as switch bounce or contact chatter.

Switch bounce can result in failures, inaccurate readings, or erroneous triggering of digital logic gates in digital circuits. This effect is removed with a switch debouncer circuit, which filters the switch output signal to create a clear and steady logic signal.


<p align="center">
  <img src="https://github.com/alopez099/Switch_Debounce/files/10988619/README.1.md)" />
</p>

## Usage/Examples on GO-Board FPGA

```verilog
// This module is used to debounce any switch or button coming into the FPGA.
// Does not allow the output of the switch to change unless the switch is
// steady for enough time (not toggling).
///////////////////////////////////////////////////////////////////////////////
module Debounce_Switch (input i_Clk, input i_Switch, output o_Switch);
 
  parameter c_DEBOUNCE_LIMIT = 250000;  // 10 ms at 25 MHz
   
  reg [17:0] r_Count = 0;
  reg r_State = 1'b0;
 
  always @(posedge i_Clk)
  begin
    // Switch input is different than internal switch value, so an input is
    // changing.  Increase the counter until it is stable for enough time.  
    if (i_Switch !== r_State && r_Count < c_DEBOUNCE_LIMIT)
      r_Count <= r_Count + 1;
 
    // End of counter reached, switch is stable, register it, reset counter
    else if (r_Count == c_DEBOUNCE_LIMIT)
    begin
      r_State <= i_Switch;
      r_Count <= 0;
    end 
 
    // Switches are the same state, reset the counter
    else
      r_Count <= 0;
  end
 
  // Assign internal register to output (debounced!)
  assign o_Switch = r_State;
 
endmodule
```

