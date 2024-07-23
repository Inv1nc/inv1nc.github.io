---
title: EVM Puzzles Solutions
layout: post
date: 2024-07-23
permalink: evm-puzzles-writeup
category: web3
description: My Solutions to EVM Puzzles
author: Inv1nc
---

Hello Friend,

From past few days i am learning how evm works under the hood.  

In this writeup, I'll share the solutions of evm puzzles which i have solved while understanding [EVM Opcodes](https://www.evm.codes/)  

For a visual representation and interactive experience, be sure to checkout [Playground](https://www.evm.codes/playground).  

### Introduction

Each puzzle consists on sending a successful transaction to a contract.   

The bytecode of the contract is provided, and we need to fill the transaction data that won't revert the execution.   

In some puzzles we only need to provide the value that will be sent to the contract, in others the calldata, and in others both values.  

### Initial Setup

```shell
git clone https://github.com/fvictorio/evm-puzzles.git
cd evm-puzzles/
npm install
npx hardhat play
```

### Puzzle 1

```
00      34      CALLVALUE	// push call value in wei
01      56      JUMP		// jump to position on stack 
02      FD      REVERT		// revert execution
03      FD      REVERT		// revert execution
04      FD      REVERT		// revert execution
05      FD      REVERT		// revert execution
06      FD      REVERT		// revert execution
07      FD      REVERT		// revert execution
08      5B      JUMPDEST	// mark a valid jump destination
09      00      STOP		// halt execution
```

```
3456FDFDFDFDFDFD5B00 // bytecode
```

`CALLVALUE` pushes callvalue in wei onto the stack.  

`JUMPDEST` is marked at `0x08` to solve the challenge we need to provide a `CALLVALUE` equal to 8.  

#### Solution

Enter the value to send: 8  

Puzzle solved!  

---

### Puzzle 2

```
00      34      CALLVALUE	// push call value in wei
01      38      CODESIZE	// push code size
02      03      SUB			// subtract top two values on stack
03      56      JUMP		// jump to position on stack
04      FD      REVERT		// revert execution
05      FD      REVERT		// revert execution
06      5B      JUMPDEST	// mark a valid jump destination
07      00      STOP		// halt execution
08      FD      REVERT		// revert execution
09      FD      REVERT		// revert execution
```

```
34380356FDFD5B00FDFD // bytecode
```

To successfully jump to JUMPDEST at position `0x06`, the top of the stack must equal 6.  

`CODESIZE` is 10 (since the bytecode is 20 hex characters, which is 10 bytes).  

```python
len("34380356FDFD5B00FDFD")/2	#10
```

We need the result of CODESIZE - CALLVALUE to be 6.  

```
10 - CALLVALUE = 6
CALLVALUE = 10 - 6
CALLVALUE = 4
```

#### Solution

Enter the value to send: 4  

Puzzle solved!  

---

### Puzzle 3

```
00      36      CALLDATASIZE	// push size of calldata onto the stack
01      56      JUMP			// jump to position on stack 
02      FD      REVERT			// revert execution
03      FD      REVERT			// revert execution
04      5B      JUMPDEST		// mark a valid jump destination
05      00      STOP			// halt execution
```

```
3656FDFD5B00 // bytecode
```

To solve this, we need to send 4 bytes of calldata.  

This will make the bytecode execution jump to the `JUMPDEST` at position 0x04 and then stop the execution, thereby solving the challenge.  

An example of such calldata is 0x01010101.  

#### Solution

Enter the calldata: 0x01010101

Puzzle solved!  

---

### Puzzle 4

```
00      34      CALLVALUE	// push call value in wei
01      38      CODESIZE	// push code size
02      18      XOR			// bitwise XOR of top two values on stack
03      56      JUMP		// jump to position on stack 
04      FD      REVERT		// revert execution
05      FD      REVERT		// revert execution
06      FD      REVERT		// revert execution
07      FD      REVERT		// revert execution
08      FD      REVERT		// revert execution
09      FD      REVERT		// revert execution
0A      5B      JUMPDEST	// mark a valid jump destination
0B      00      STOP		// hault execution
```

```
34381856FDFDFDFDFDFD5B00 // bytecode
```

`CODESIZE` is 12 (since the bytecode is 24 hex characters, which is 12 bytes).  

To solve the challenge, the result of CODESIZE XOR CALLVALUE must be `0x0A`.  

```
	1100 (12)
XOR	1010 (0x0A)
----------------
	0110 (6)
```


#### Solution: 

Enter the value to send: 6

Puzzle solved!  

---

### Puzzle 5

```
00      34          CALLVALUE	// push call value in wei
01      80          DUP1		// duplicate the top stack item
02      02          MUL			// multiply the top two values on the stack
03      610100      PUSH2 0100	// push 0x0100 to the stack
06      14          EQ			// check if the top two values are equal
07      600C        PUSH1 0C	// push 0x0C to the stack
09      57          JUMPI		// conditional jump to the location on top of the stack if the second item on the stack is non-zero
0A      FD          REVERT		// revert execution
0B      FD          REVERT		// revert execution
0C      5B          JUMPDEST	// mark a valid jump destinatin
0D      00          STOP		// hault execution
0E      FD          REVERT		// revert execution
0F      FD          REVERT		// revert execution
```

```
34800261010014600C57FDFD5B00FDFD // bytecode
```

To solve the challenge, the result of CALLVALUE * CALLVALUE must equal 0x0100 (256).  

To find CALLVALUE, solve the equation CALLVALUE * CALLVALUE = 256.  

```
CALLVALUE = sqrt(256) = 16
```

#### Solution:

Enter the value to send: 16

Puzzle solved!  

---

### Puzzle 6

```
00      6000      PUSH1 00		// push 0x00 onto the stack 
02      35        CALLDATALOAD	// load 32 bytes of calldata starting at position specified by top of the stack
03      56        JUMP			// jump to position on stack
04      FD        REVERT		// revert execution
05      FD        REVERT		// revert execution
06      FD        REVERT		// revert execution
07      FD        REVERT		// revert execution
08      FD        REVERT		// revert execution
09      FD        REVERT		// revert execution
0A      5B        JUMPDEST		// mark a valid jump destination
0B      00        STOP			// halt execution
```

```
60003556FDFDFDFDFDFD5B00 // bytecode
```

To solve the challenge, the value at the start of the calldata must be 0x0A (decimal 10).  

As the CALLDATALOAD load the 32 bytes of calldata we need the pad left with 31 bytes of zeros.  

#### Solution

Enter the calldata: 0x000000000000000000000000000000000000000000000000000000000000000a

Puzzle solved!  

---

### Puzzle 7

```
00      36        CALLDATASIZE	// pushes size of calldata onto the stack
01      6000      PUSH1 00		// push 0x00 onto the stack
03      80        DUP1			// duplicate the top stack item
04      37        CALLDATACOPY	// copy calldata to memory
05      36        CALLDATASIZE	// pushes size of calldata onto the stack
06      6000      PUSH1 00		// push 0x00 onto the stack
08      6000      PUSH1 00		// push 0x00 onto the stack
0A      F0        CREATE		// create a new contract
0B      3B        EXTCODESIZE	// check size of the code at the address
0C      6001      PUSH1 01		// push 0x01 onto the stack
0E      14        EQ			// check if the top two values are equal
0F      6013      PUSH1 13		// push 0x13 onto the stack
11      57        JUMPI			// conditional jump to the location on top of the stack if the second item on the stack is non-zero
12      FD        REVERT		// revert execution
13      5B        JUMPDEST		// mark a valid jump destination
14      00        STOP			// hault execution
```

```
36600080373660006000F03B600114601357FD5B00 // bytecode
```


To solve the challenge, the we needs to create a valid contract whose size is 1 byte.  

The runtime code is the code that is returned from the contract creation code.  

Return data is taken from memory, so push one byte into memory

```
push1 01 - push 0x01 into stack
push1 00 - push 0x00 into stack
mstore8   - store 0x01 at offset of 0x00 into the memory
push1 01 - // return size
push1 00 - // from where in the memory returning data start
return
```
#### Solution

Enter the calldata: 0x600160005360016000f3

Puzzle solved!  

---

### Puzzle 8

```
00      36        CALLDATASIZE	// pushes size of calldata onto the stack
01      6000      PUSH1 00		// push 0x00 onto the stack
03      80        DUP1			// duplicate the top stack item
04      37        CALLDATACOPY	// copy calldata to memory
05      36        CALLDATASIZE	// pushes size of calldata onto the stack
06      6000      PUSH1 00		// push 0x00 onto the stack
08      6000      PUSH1 00		// push 0x00 onto the stack
0A      F0        CREATE		// create a new contract
0B      6000      PUSH1 00		// push 0x00 onto the stack
0D      80        DUP1			// duplicate the top stack item
0E      80        DUP1			// duplicate the top stack item
0F      80        DUP1			// duplicate the top stack item
10      80        DUP1			// duplicate the top stack item
11      94        SWAP5			// swap the top stack item with the sixth item
12      5A        GAS			// push remaining gas onto the stack 
13      F1        CALL			// call another contract
14      6000      PUSH1 00		// push 0x00 onto the stack
16      14        EQ			// check if the top two values are equal
17      601B      PUSH1 1B		// push 0x1B onto the stack
19      57        JUMPI			// conditional jump to the location on top of the stack if the second item on the stack is non-zero
1A      FD        REVERT		// revert execution
1B      5B        JUMPDEST		// mark a valid jump destination
1C      00        STOP			// halt execution
```

```
36600080373660006000F0600080808080945AF1600014601B57FD5B00 // bytecode
```

From `00` to `0A` the bytecode load the calldata into memory and create a contract with that calldata.  

From `0B` to `13` it call the contract that was created with the calldata.  

To solve the return data of the called contract must be equal to zero.  

The contract return zero if any revert happen but wait it also revert the whole execution.  

For that we need to just return the `REVERT`(FD) opcode.

```
push1 0xfd  // push revert opcode into stack
push1 0x00	
mstore8		// store 1byte data into memory
push1 0x01	
push1 0x00
return	// returning 1 byte from memory
```
#### Solution

Enter the calldata: 0x60fd60005360016000f3

---

### Puzzle - 9

```
00      36        CALLDATASIZE	// pushes size of calldata onto the stack
01      6003      PUSH1 03		// push `0x03` onto the stack
03      10        LT			// less than comparison
04      6009      PUSH1 09		// push `0x09` onto the stack 
06      57        JUMPI			// conditional jump
07      FD        REVERT		// reverts execution
08      FD        REVERT		// reverts execution
09      5B        JUMPDEST		// marks a valid jump destination
0A      34        CALLVALUE		// pushes call value onto the stack
0B      36        CALLDATASIZE	// pushes size of calldata onto the stack
0C      02        MUL			// multiplication
0D      6008      PUSH1 08		// push `0x08` onto the stack
0F      14        EQ			// equality comparison
10      6014      PUSH1 14		// push `0x14` onto the stack
12      57        JUMPI			// conditional jump
13      FD        REVERT		// reverts execution
14      5B        JUMPDEST		// marks a valid jump destination
15      00        STOP			// halt execution
```

```
36600310600957FDFD5B343602600814601457FD5B00 // bytecode
```

From `00` to `06` CALLDATASIZE must be greater that 3.  

From `0A` to `10`, we understand the multiplication of CALLDATA and CALLDATASIZE must be 8.  

To solve the challenge,  

If we take CALLDATASIZE of size 4 bytes the CALLVALUE must be 2.  

If we take CALLDATASIZE of size 8 bytes the CALLVALUE must to 1.  

#### Solution

Enter the value to send: 2  

Enter the calldata: 0x00000000  
 
---

### Puzzle 10

```
00      38          CODESIZE		// pushes the size of the code onto the stack
01      34          CALLVALUE		// pushes the call value onto the stack
02      90          SWAP1			// swaps the top two stack elements
03      11          GT				// greater than comparison
04      6008        PUSH1 08		// push `0x08` onto the stack
06      57          JUMPI			// marks a valid jump destination
07      FD          REVERT			// reverts execution
08      5B          JUMPDEST		// marks a valid jump destination
09      36          CALLDATASIZE	// pushes the size of the calldata onto the stack
0A      610003      PUSH2 0003		// push `0x0003` onto the stack
0D      90          SWAP1			// swaps the top two stack elements
0E      06          MOD				// modulus operation
0F      15          ISZERO			// checks if the top stack element is zero
10      34          CALLVALUE		// pushes the call value onto the stack
11      600A        PUSH1 0A		// push `0x0A` onto the stack
13      01          ADD				// adds the top two stack elements
14      57          JUMPI			// conditional jump
15      FD          REVERT			// reverts execution
16      FD          REVERT			// reverts execution
17      FD          REVERT			// reverts execution
18      FD          REVERT			// reverts execution
19      5B          JUMPDEST		// marks a valid jump destination
1A      00          STOP			// hault execution
```

```
38349011600857FD5B3661000390061534600A0157FDFDFDFD5B00 // bytecode
```

From `00` to `06`, we understand that CODESIZE must be greater than CALLVALUE.  

From `09` to `0F`, we understand that CALLDATASIZE modulus 3 must be equal to 0.  

To need to satisfy the modulus operation, let us take CALLDATA of size 3 bytes.

From `10` to `14`, we understand `CALLVALUE + 0x0A ` need to be equal to `0x19`(25 in decimal)

```
CALLVALUE + 0x0A = 0x19
CALLVALUE = 0x19 - 0x0A
CALLVALUE = 15
```

#### Solution

Enter the value to send: 15  

Enter the calldata: 0x000000  

Puzzle solved!

---

Do you want to play the next puzzle? Yes

**All puzzles are solved!**

Thanks you!


