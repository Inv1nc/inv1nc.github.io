---
title: More EVM Puzzles Writeup
layout: post
date: 2024-07-25
permalink: more-evm-puzzles-writeup
category: web3
description: My Solutions to More EVM Puzzles
author: Inv1nc
---

Hello Friend,

This is writeup containing notes while i am solving more evm puzzles. i am not interested in explaining every opcode, where you can find them in [EVM Code](http://evm.codes/) in detail.

### Intial Setup

```
git clone https://github.com/daltyboy11/more-evm-puzzles.git
npm install
npx hardhat play
```

### Puzzle 1

```
00      36      CALLDATASIZE
01      34      CALLVALUE
02      0A      EXP
03      56      JUMP
04      FE      INVALID
05      FE      INVALID
06      FE      INVALID
07      FE      INVALID
08      FE      INVALID
09      FE      INVALID
0A      FE      INVALID
0B      FE      INVALID
0C      FE      INVALID
0D      FE      INVALID
0E      FE      INVALID
0F      FE      INVALID
10      FE      INVALID
11      FE      INVALID
12      FE      INVALID
13      FE      INVALID
14      FE      INVALID
15      FE      INVALID
16      FE      INVALID
17      FE      INVALID
18      FE      INVALID
19      FE      INVALID
1A      FE      INVALID
1B      FE      INVALID
1C      FE      INVALID
1D      FE      INVALID
1E      FE      INVALID
1F      FE      INVALID
20      FE      INVALID
21      FE      INVALID
22      FE      INVALID
23      FE      INVALID
24      FE      INVALID
25      FE      INVALID
26      FE      INVALID
27      FE      INVALID
28      FE      INVALID
29      FE      INVALID
2A      FE      INVALID
2B      FE      INVALID
2C      FE      INVALID
2D      FE      INVALID
2E      FE      INVALID
2F      FE      INVALID
30      FE      INVALID
31      FE      INVALID
32      FE      INVALID
33      FE      INVALID
34      FE      INVALID
35      FE      INVALID
36      FE      INVALID
37      FE      INVALID
38      FE      INVALID
39      FE      INVALID
3A      FE      INVALID
3B      FE      INVALID
3C      FE      INVALID
3D      FE      INVALID
3E      FE      INVALID
3F      FE      INVALID
40      5B      JUMPDEST
41      58      PC
42      36      CALLDATASIZE
43      01      ADD
44      56      JUMP
45      FE      INVALID
46      FE      INVALID
47      5B      JUMPDEST
48      00      STOP
```

JUMPDEST is located at `0x47` (71 in decimal).  

From `00` to `03`, we understand that `CALLVALUE` ** `CALLDATASIZE` must to equal to `71`.  

To achieve that, let us take `CALLDATASIZE` equal to `1`

```
CALLVALUE ** 1 = 71

CALLVALUE = 71
```

#### Solution

Enter the value to send: 71  

Enter the calldata: 0x00  

Puzzle solved!

---

### Puzzle 2

```
00      36        CALLDATASIZE
01      6000      PUSH1 00
03      6000      PUSH1 00
05      37        CALLDATACOPY
06      36        CALLDATASIZE
07      6000      PUSH1 00
09      6000      PUSH1 00
0B      F0        CREATE
0C      6000      PUSH1 00
0E      80        DUP1
0F      80        DUP1
10      80        DUP1
11      80        DUP1
12      94        SWAP5
13      5A        GAS
14      F1        CALL
15      3D        RETURNDATASIZE
16      600A      PUSH1 0A
18      14        EQ
19      601F      PUSH1 1F
1B      57        JUMPI
1C      FE        INVALID
1D      FE        INVALID
1E      FE        INVALID
1F      5B        JUMPDEST
20      00        STOP
```

From `00` to `05`, the bytecode is just copying the calldata into memory.  

From `06` to `0B`, it creates a contract with the calldata.  

From `0C` to `15`, it calling the contract that was created with the given calldata.  

Inorder to solve this puzzle, we need to create a contract of size `0A`.  

Let us create it,  

runtime code

```
push1 0x0a	// return data size
push1 0x00	// offset
return
```

contract creation code

```
push5 0x600a6000f3	// push runtime code opcodes
push1 0x00			
mstore				// store runtime code at 0x00
push1 0x0a			// length of runtime code
push1 0x1b			// offset
return 
```

#### Solution

Enter the calldata: 0x64600a6000f3600052600a601bf3  

Puzzle solved!  

---

### Puzzle 3

```
00      36        CALLDATASIZE
01      6000      PUSH1 00
03      6000      PUSH1 00
05      37        CALLDATACOPY
06      36        CALLDATASIZE
07      6000      PUSH1 00
09      6000      PUSH1 00
0B      F0        CREATE
0C      6000      PUSH1 00
0E      80        DUP1
0F      80        DUP1
10      80        DUP1
11      93        SWAP4
12      5A        GAS
13      F4        DELEGATECALL
14      6005      PUSH1 05
16      54        SLOAD
17      60AA      PUSH1 AA
19      14        EQ
1A      601E      PUSH1 1E
1C      57        JUMPI
1D      FE        INVALID
1E      5B        JUMPDEST
1F      00        STOP
```

```
3660006000373660006000F06000808080935AF460055460aa14601e57fe5b00 // bytecode
```

From `00` to `0B`, it creating a contract with the calldata we have given.

From `0C` to `013`, the contract delegate calling the contract that is deployed with the given calldata.

From `14` to `19`, we observe it checking the storage slot having `AA` (170 in decimal).  

To solve this challange we need to create a contract that store `AA` at storage slot 5.

let us create the run time code.

```
push1 0xAA	// value to be stored
push1 0x05	// key where the value to store
sstore		// store the value in the key slot
stop
		// hault the execution
```

contract creation code

```
PUSH6 0x60aa60055500
PUSH1 0x00
MSTORE
PUSH1 0x06
PUSH1 0x1a
RETURN
```

#### Solution

Enter the calldata: 0x6560aa600555006000526006601af3   

Puzzle solved!  

---

### Puzzle 4

```
00      30        ADDRESS
01      31        BALANCE
02      36        CALLDATASIZE
03      6000      PUSH1 00
05      6000      PUSH1 00
07      37        CALLDATACOPY
08      36        CALLDATASIZE
09      6000      PUSH1 00
0B      30        ADDRESS
0C      31        BALANCE
0D      F0        CREATE
0E      31        BALANCE
0F      90        SWAP1
10      04        DIV
11      6002      PUSH1 02
13      14        EQ
14      6018      PUSH1 18
16      57        JUMPI
17      FD        REVERT
18      5B        JUMPDEST
19      00        STOP
```

From `00` to `01`, byte code push the callvalue onto the stack.  

From `02` to `0D` the bytecode creating the contract with callvalue.  

From `0E` to `11`, it is checking the balance of create contract, the balance must be 1/2 the of the callvalue.  

Let us create the contract

```
push1 0x00
dup1
dup1
dup1
push1 0x01	// as i am sending callvalue as 2, 1/2 of 2 is 1
dup2
gas
call
stop
```

#### Solution

Enter the value to send: 2  

Enter the calldata: 0x60008080806001815af100  

[Works](https://www.evm.codes/playground?callValue=2&unit=Wei&callData=0x60008080806001815af100&codeType=Bytecode&code='30313660006000373660003031F0319004600214601857FD5B00'_)

---

### Puzzle 5

```
00      6020      PUSH1 20
02      36        CALLDATASIZE
03      11        GT
04      6008      PUSH1 08
06      57        JUMPI
07      FD        REVERT
08      5B        JUMPDEST
09      36        CALLDATASIZE
0A      6000      PUSH1 00
0C      6000      PUSH1 00
0E      37        CALLDATACOPY
0F      36        CALLDATASIZE
10      59        MSIZE
11      03        SUB
12      6003      PUSH1 03
14      14        EQ
15      6019      PUSH1 19
17      57        JUMPI
18      FD        REVERT
19      5B        JUMPDEST
1A      00        STOP
```

calldatasize > 32 bytes  

msize which is multiple of 32 bytes  

msize - calldatasize = 3  

```
64 - calldatasize = 3
calldatasize = 64 - 3
calldatasize = 61
```

```python
'0x'+('00'*61)  // python code to generate calldata
```

### Solution

Enter the calldata: 0x00000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000  

Puzzle solved!  

----


### Puzzle 6

```
00      7F FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF0      PUSH32 FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF0
21      34                                                                      CALLVALUE
22      01                                                                      ADD
23      6001                                                                    PUSH1 01
25      14                                                                      EQ
26      602A                                                                    PUSH1 2A
28      57                                                                      JUMPI
29      FD                                                                      REVERT
2A      5B                                                                      JUMPDEST
2B      00                                                                      STOP
```

In this puzzle, a number of 32 bytes is push onto stack.  

we need to find a callvalue to add with that 32 bytes ((2**256)-16) where that result should be equal to 1.  

by adding 0xf(16) to 32 bytes on the stack can overflow and result will become 0.  

to get the result 1 we need to provide callvalue of 17.

### Solution

Enter the value to send: 17  

Puzzle solved!  

---

### Puzzle 7

```
00      5A        GAS				// 2	|	2 gas
01      34        CALLVALUE			// 2	|	2 gas
02      5B        JUMPDEST			// 1	|----
03      6001      PUSH1 01			// 3	|	|
05      90        SWAP1				// 3	|	|
06      03        SUB				// 3	|	|
07      80        DUP1				// 3	|	|	loop 43 gas
08      6000      PUSH1 00			// 3	|	|
0A      14        EQ				// 3	|	|
0B      6011      PUSH1 11			// 3	|	|
0D      57        JUMPI				// 10	|--------
0E      6002      PUSH1 02			// 3	|	|	|
10      56        JUMP				// 8	----	|
11      5B        JUMPDEST			// 1 	--------	1 gas
12      5A        GAS				// 2
13      90        SWAP1				// 3
14      91        SWAP2				// 3
15      03        SUB				// 3	
16      60A6      PUSH1 A6			// 3
18      14        EQ				// 3
19      601D      PUSH1 1D			// 3	
1B      57        JUMPI				// 8
1C      FD        REVERT			// 0
1D      5B        JUMPDEST			// 1
1E      00        STOP				// 0
```


Out of loop 5 gas.  

Inside loop 43 gas.  

Final loop 43 - 11 = 32 gas, skipping PUSH, JUMP.  

```
5 + 32 + (43 * (CALLVALUE - 1)) = 166

(CALLVALUE - 1) * 43 = 129

CALLVALUE - 1 = 3

CALLVALUE = 4
```

#### Solution

Enter the value to send: 4  

Puzzle solved!  


---

### Puzzle 8

```
00      34        CALLVALUE
01      15        ISZERO
02      19        NOT
03      6007      PUSH1 07
05      57        JUMPI
06      FD        REVERT
07      5B        JUMPDEST
08      36        CALLDATASIZE
09      6000      PUSH1 00
0B      6000      PUSH1 00
0D      37        CALLDATACOPY
0E      36        CALLDATASIZE
0F      6000      PUSH1 00
11      6000      PUSH1 00
13      F0        CREATE
14      47        SELFBALANCE
15      6000      PUSH1 00
17      6000      PUSH1 00
19      6000      PUSH1 00
1B      6000      PUSH1 00
1D      47        SELFBALANCE
1E      86        DUP7
1F      5A        GAS
20      F1        CALL
21      6001      PUSH1 01
23      14        EQ
24      6028      PUSH1 28
26      57        JUMPI
27      FD        REVERT
28      5B        JUMPDEST
29      47        SELFBALANCE
2A      14        EQ
2B      602F      PUSH1 2F
2D      57        JUMPI
2E      FD        REVERT
2F      5B        JUMPDEST
30      00        STOP
```

looks like complicate but not. let us break it.

From `00` to `05`, we observe to move further the callvalue must be 0.  

From `08` to `13`, the bytecode copy the calldata into memory and create a contract with it.  

From `14` to `20`, calling the contract that was deployed with calldata.  

From `29` to `2D` checking balance before and after are same or not.  

Inorder to solve this we challenge, just creation code is enough without need of runtime code.

let us create a contract that returns nothing.

```
push1 00
push1 00
return
```

#### Solution

Enter the calldata: 0x60006000f3  

Puzzle solved!  

---

### Puzzle 9

```
00      34        CALLVALUE
01      6000      PUSH1 00
03      52        MSTORE
04      6020      PUSH1 20
06      6000      PUSH1 00
08      20        SHA3
09      60F8      PUSH1 F8
0B      1C        SHR
0C      60A8      PUSH1 A8
0E      14        EQ
0F      6016      PUSH1 16
11      57        JUMPI
12      FD        REVERT
13      FD        REVERT
14      FD        REVERT
15      FD        REVERT
16      5B        JUMPDEST
17      00        STOP
```

From `00` to `08`, computing the keccak256 hash of the callvalue.  

From `09` to `0B`, right shifting `0xF8` (31 bytes) the keccak256 hash.  

we left with the left most 1 bytes and that must be `A8`.  

a bruteforce approach. 

```
keccak256(abi.encode(47))
```

### Solution

Enter the value to send: 47  

Puzzle solved!  

---

### Puzzle 10

```
00      6020                                                                    PUSH1 20
02      6000                                                                    PUSH1 00
04      6000                                                                    PUSH1 00
06      37                                                                      CALLDATACOPY
07      6000                                                                    PUSH1 00
09      51                                                                      MLOAD
0A      7F F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0     PUSH32 F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0F0
2B      16                                                                      AND
2C      6020                                                                    PUSH1 20
2E      6020                                                                    PUSH1 20
30      6000                                                                    PUSH1 00
32      37                                                                      CALLDATACOPY
33      6000                                                                    PUSH1 00
35      51                                                                      MLOAD
36      17                                                                      OR
37      7F ABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABAB     PUSH32 ABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABABAB
58      14                                                                      EQ
59      605D                                                                    PUSH1 5D
5B      57                                                                      JUMPI
5C      FD                                                                      REVERT
5D      5B                                                                      JUMPDEST
5E      00                                                                      STOP

```

From `00` to `2B`, the bytecode loads the first 32 bytes of the calldata, done `AND` operation with `F0F0...`  

From `2c` to `36`, the bytecode just loads second 32 bytes from the calldata and done `OR` operation with previous result.  

To solve this puzzle, we need to provide 64 bytes of calldata, such that after two operation the result must be in `ABAB..`.  

```
	FO
AND	?? 		// probably (A,anything less than B)'s
-----------
	?? - A0
OR	?? - 0B
-----------
	AB
```

```python
"0x" + "A4"*32 + "0B"*32	// python code to create calldata
```

#### Solution

Enter the calldata: 0xA4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A4A40B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B0B  

Puzzle solved!  

---

Do you want to play the next puzzle? Yes  

All puzzles are solved!  

---

Thanks you.
