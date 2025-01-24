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
|done|Output|signal indicate|
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


