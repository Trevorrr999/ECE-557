# Project: Fib_circuit
**Name:** Trevor(Yuzhe) Zhang 
**NetID:** yz972
**course:** ECE 557
**date:** 1/23/2025

## Project introduction:
This Chisel code implements a Fibonacci Sequence circuit.
Below is a summary of the main structure and functionality of the code.

**Inputs and Outputs declaration**
**Port Name**|**Input/output**|**Description**
| ---------- | -------------- | --------------- | 
|in|Input|The index of the Fibonacci sequence(start from 0th)|
|out|Output|Data of the index|
|done|Output|a signal indication for computaion's end|
## Contents:

1. Port Declaration

2. Finite State Machine

3. Design Validation

### Port Declaration:
io.in (DeqIO(UInt(7.W))) ------ A 7-bit input using a ready/valid handshake for receiving data.

io.out (Output(Valid(UInt(64.W)))) ------ A 64-bit output with a valid signal to indicate result availability.

io.done (Output(UInt(1.W))) ------ A 1-bit signal indicating the completion of the operation.
### Finite State Machine:
The FSM consists of three states defined using Chisel's Enum function:
```
val sIDLE :: sN :: s_finish :: Nil = Enum(3)
```
sIDLE: The initial state where the system waits for input.
sN: The state where the Fibonacci computation occurs.
s_finish: The state indicating the computation is complete.

the register cnt is used to record which index is currently calculated.If cnt equals num, sets output_flag to true and transitions to s_finish; otherwise, remains in sN.
```
elsewhen(state === sN)
  {
    when(cnt === 0.U)
    {
      x:= 0.U
      x_1 := 0.U
    }.elsewhen(cnt === 1.U)
    {
      x:= 1.U
      x_1 := 1.U
    }.elsewhen(cnt === 2.U){
      x:= 1.U
      x_1 := 1.U
    }.otherwise{
      x := x + x_1
      x_1 := x
    }
    cnt := cnt +1.U
    when(num === cnt)
    {
      state := s_finish
      output_flag := true.B
    }.otherwise
    {
      state := sN
    }
  }
```
### Design Validation:
the test file incorperate some corner cases and the target index 70th
the test result are show as below:

![image](https://github.com/user-attachments/assets/51de0e28-eb1e-44f2-b385-2ae46020f082)

The figure shows the result passed testcases in this design, which covers the required index 70th. Furthermore, the testbench incorporates more corner cases to validate the circuit's functionality. It turns out that the design is correct. 

```
val my_test_list = Seq(0,1,2,3,4,5,6,7,8,9,10,15,20,68,69,70,71,72)
  for(i <- my_test_list)
    {
      poke(c.io.in.valid, 1)
      poke(c.io.in.bits, i)
      step(1)

      while(peek(c.io.out.valid) == 0)
      {
        step(1)
      }

      val result = peek(c.io.out.bits)
      val expected_result = fib_cal(i)
      expect(c.io.out.valid, 1, s"the valid signal is not activated")
      expect(c.io.done, 1, s"the done signal is not activated")
      println(s"the input n is: $i, the result is: $result, the expected result is: $expected_result")
      expect(c.io.out.bits, expected_result, s"Fibonacci($i) should be $expected_result")
    }
    println("test success")
```
