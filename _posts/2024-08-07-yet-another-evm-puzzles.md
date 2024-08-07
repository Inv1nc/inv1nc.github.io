---
title: Yet Another EVM Puzzles Writeup
layout: post
date: 2024-08-07
permalink: yet-another-evm-puzzles-writeup
category: web3
description: My Solutions to Yet Another EVM Puzzles
author: Inv1nc
---

Hello Friend, 

This is writeup that contain solutions for Yet Another EVM Puzzles. I am not interested in explaining opcode by opcode, where you can find them in [EVM Code](http://evm.codes/) in detail. you can able to easily understand the bytecode by looking to the stack written in comments. Hope you like this writeup.

### Intial Setup

```
git clone https://github.com/mattaereal/yet-another-evm-puzzle/
npm install
npx hardhat play
```

### Puzzle 1

ByteCode  

`3415600736111760225760003560005236600034f080156022573115602357600080fd5b00`

```
00	34	CALLVALUE	// [callvalue]
01	15	ISZERO		// [iszero(callvalue)]
02	6007	PUSH1 07	// [0x07, iszero(callvalue)]
04	36	CALLDATASIZE	// [calldatasize, 0x07, iszero(callvalue)]
05	11	GT		// [calldatasize > 0x07, iszero(callvalue)]
06	17	OR		// [(calldatasize > 0x07) OR (iszero(callvalue))]
07	6022	PUSH1 22	// [0x22, (calldatasize > 0x07) OR (iszero(callvalue))]
09	57	JUMPI		// jump to 0x22 if (calldatasize > 0x07) OR (iszero(callvalue))
```

if callvalue is 0 or calldatasize greater than 7, execution flow jump to `0x22` then automatically revert.

```
0A      6000      PUSH1 00	// [0x00]
0C      35        CALLDATALOAD	// [calldata]
0D      6000      PUSH1 00	// [0x00, calldata]
0F      52        MSTORE	// store calldata into memory
```
store calldata into memory at `0x00` offset.

```
10      36        CALLDATASIZE	// [calldatasize]
11      6000      PUSH1 00	// [0x00, calldatasize]
13      34        CALLVALUE	// [callvalue, 0x00, calldatasize]
14      F0        CREATE	// creating a contract with [callvalue, 0x00, calldatasize]
```

loads calldata which already stored in memory at offset `0x00` and creating contract with callvalue.

```
15      80        DUP1		// [address]
16      15        ISZERO	// [iszero(address)]
17      6022      PUSH1 22	// [0x22, iszero(address)]
19      57        JUMPI		// jump to 0x22 if iszero(address)
```

checking whether if contract created succesfully with the calldata or not. if not it will revert.

```
1A      31        BALANCE	// balance of the creating contract
1B      15        ISZERO	// [iszero(balance(address))]
1C      6023      PUSH1 23	//  [0x23, iszero(balance(address))]
1E      57        JUMPI		// jump to 0x23 if balance(address) is zero
1F      6000      PUSH1 00	// [0x00]
21      80        DUP1		// [0x00, 0x00]
22      FD        REVERT	// revert
23      5B        JUMPDEST	// jumpdestination
24      00        STOP		// hault execution
```

`stop` opcode will be executed, if balance of contract the was created with calldata and callvalue must be zero.  

To solve this puzzles we need create a contract to transfer amount received and that contract bytecode must be withnin 7 bytes.

let create a contract which gone selfdestruct.  

```
push0		// push 0x00 to stack
selfdestruct	// self destruct by sending balance to zero address
```

#### Solution

Enter the value to send: 1  

Enter the calldata: 0x59ff  

Puzzle solved!  

---

### Puzzle 2

ByteCode  

 `7faaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa60cc60005b90808253601001906001018060201160255750506000516000351814604957600080fd5b00`

```
00      7F AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA     PUSH32 AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
```
```
21	60CC	PUSH1 CC	// [0xCC, 32 A's]
23	6000	PUSH1 00	// [0x00, 0xCC, 32 A's]
25	5B	JUMPDEST	// jump dest
26	90	SWAP1		// [0xCC, 0x00, 32 A's]
27	80	DUP1		// [0xCC,0xCC, 0x00, 32 A's]
28	82	DUP3		// [0x00, 0xCC, 0xCC, 0x00, 32 A's]
29	53	MSTORE8		// store 1 byte into memory
```
`mstore8` store `0xCC` at `0x00` in the memory.


```
2A	6010	PUSH1 10	// [0x10, 0xCC, 0x00, 32 A's]
2C	01	ADD		// [0xdc, 0x00, 32 A's]
2D	90	SWAP1		// [0x00, 0xdc, 32 A's]
2E	6001	PUSH1 01	// [0x01, 0x00, 0xdc, 32 A's]
30	01	ADD		// [0x01, 0xdc, 32 A's]
31	80	DUP1		// [0x01, 0x01, 0xdc, 32 A's]
32	6020	PUSH1 20	// [0x20, 0x01, 0x01, 0xdc, 32 A's]
34	11	GT		// [0x20 > 0x01, 0x01, 0xdc, 32 A's]
35	6025	PUSH1 25	// [0x25, 0x020 > 0x01, 0x01, 0xdc, 32 A's]
37	57	JUMPI		// jump to 0x25 if 0x20 > 0x01
```

the loop from 0x37 to 0x25 will happen 31 (0x1f) times and memory ended up with `ccdcecfc0c1c2c3c4c5c6c7c8c9cacbcccdcecfc0c1c2c3c4c5c6c7c8c9cacbc`

```
38	50 	POP		// [0xbc, 32 A's]
39	50	POP		// [32 A's]
3A	6000	PUSH1 00	// [0x00, 32 A's]
3C	51	MLOAD		// [data stored in memory, 32 A's]
3D	6000	PUSH1 00	// [0x00, data stored in memory , 32 A's]
3F	35	CALLDATALOAD	// [calldata, data stored in memory , 32 A's]
40	18	XOR		// [xor(datastored in memory, calldata), 32 A's]
41	14	EQ		/ [xor(datastored in memory, calldata) ==  32 A's]
42	6049	PUSH1 49	// [0x, xor(datastored in memory, calldata) ==  32 A's]
44	57	JUMPI		// jump to 0x49 if xor(datastored in memory, calldata) ==  32 A's
45	6000	PUSH1 00	// [0x00]
47	80	DUP1		// [0x00, 0x00]
48	FD	REVERT		// revert
49	5B	JUMPDEST	// jump destination
4A	00	STOP		// hault execution
```

In order to solve this puzzles we need provide with a callvalue where xor of data stored in memory and callvalue must be equal to 32 A's.  

```
ccdcecfc0c1c2c3c4c5c6c7c8c9cacbcccdcecfc0c1c2c3c4c5c6c7c8c9cacbc 	// value in memory
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa	// 32 A's
```


```python
val1 = 0xccdcecfc0c1c2c3c4c5c6c7c8c9cacbcccdcecfc0c1c2c3c4c5c6c7c8c9cacbc
val2 = 0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
callvalue = val1 ^ val2
print(hex(callvalue))	#0x66764656a6b68696e6f6c6d62636061666764656a6b68696e6f6c6d626360616
```

#### Solution

Enter the calldata: 0x66764656a6b68696e6f6c6d62636061666764656a6b68696e6f6c6d626360616  

Puzzle solved!  

---

### Puzzle 3

ByteCode  

`34156021576000356000523660006000f0600060006000346000945af1156026575b600080fd5b00`

```
00      34        CALLVALUE	// [callvalue]
01      15        ISZERO	// [iszero(callvalue)]
02      6021      PUSH1 21	// [0x21, iszero(callvalue)]
04      57        JUMPI		// jump to 0x21 if callvalue is zero
```

The contract will revert if the callvalue is zero.  

```
05      6000      PUSH1 00	// [0x00]
07      35        CALLDATALOAD	// loads 32 bytes of calldata
08      6000      PUSH1 00	// [0x00, calldata[:32]]
0A      52        MSTORE	// mstore
```

Store first 32 bytes of calldata into memory.   


```
0B      36        CALLDATASIZE	// [calldatasize]
0C      6000      PUSH1 00	// [0x00, calldatasize]
0E      6000      PUSH1 00	// [0x00, 0x00, calldatasize]
10      F0        CREATE	// [address]
```

loading calldata from memory and creating contract with that.  

```
11      6000      PUSH1 00	// [0x00, address]
13      6000      PUSH1 00	// [0x00, 0x00, address]
15      6000      PUSH1 00	// [0x00, 0x00, 0x00, address]
17      34        CALLVALUE	// [callvalue, 0x00, 0x00, 0x00, address]
18      6000      PUSH1 00	// [0x00, callvalue, 0x00, 0x00, 0x00, address]
1A      94        SWAP5		// [address, 0x00, callvalue, 0x00, 0x00, 0x00, address]
1B      5A        GAS		// [gas, address, 0x00, callvalue, 0x00, 0x00, 0x00, address]
1C      F1        CALL		// 1 for succes, 0 for revert
1D      15        ISZERO	// [iszero(call success or not)]
1E      6026      PUSH1 26	// [0x26, iszero(call success or not)]
20      57        JUMPI		// jump to 0x26 if the call made is revert 
21      5B        JUMPDEST	// jump destination
22      6000      PUSH1 00	// [0x00]
24      80        DUP1		// [0x00, 0x00]
25      FD        REVERT	// revert
26      5B        JUMPDEST	// jump destination
27      00        STOP		// hault execution
```

In order to solve this puzzle we need to create a contract that will revert.  

runtime code to revert
```
push0
push0
revert

// runtime bytecode: 5f5ffd
```

contract creation code which return the runtime code
```
push3 0x5f5ffd
push0
mstore	// store runtime code in memory
push1 0x03	// size
push1 0x1d	// offset 0x20-0x03
return // return runtime code

// contract creation code: 0x625f5ffd5f526003601df3
```

### Solution

Enter the value to send: 1  

Enter the calldata: 0x625f5ffd5f526003601df3  


---

### Puzzle 4

ByteCode  

`60203611602157366000366020033736806020032060e01c63890d6908146026575b600080fd5b00`

```
00      6020            PUSH1 20	// [0x20]
02      36              CALLDATASIZE	// [calldatasize , 0x20]
03      11              GT		// [calldatasize > 0x20]
04      6021            PUSH1 21	// [0x21, calldatasize > 0x20]
06      57              JUMPI		// jump to 0x21 if calldatasize greater than 32 bytes
```

The contract revert is the calldatasize is greater than 32 bytes.  

```
07      36              CALLDATASIZE	// [calldatasize]
08      6000            PUSH1 00	// [0x00, calldatasize]
0A      36              CALLDATASIZE	// [calldatasize, 0x00, calldatasize]
0B      6020            PUSH1 20	// [0x20, calldatasize, 0x00, calldatasize]
0D      03              SUB		// [0x20 - calldatasize, 0x00, calldatasize]
0E      37              CALLDATACOPY	// copy calldata into memory
```
the above code store calldata into memory at offset `0x00`.  

```
0F      36              CALLDATASIZE	// [calldatasize]
10      80              DUP1		// [calldatasize, calldatasize]
11      6020            PUSH1 20	// [0x20, calldatasize, calldatasize]
13      03              SUB		// [0x20 - calldatasize, calldatasize]
14      20              SHA3		// compute keccakhash of calldata
```
the above code computing the keccak256 hash of the calldata.

```
15      60E0            PUSH1 E0	// [0xe0, keccak256(calldata)]
17      1C              SHR		// keccak256(calldata)[:4]
18      63890D6908      PUSH4 890D6908	// [0x890D6908 , keccak256(calldata)[:4]]
1D      14              EQ		// [0x890D6908 == keccak256(calldata)[:4]]
1E      6026            PUSH1 26	// [0x26, 0x890D6908 == keccak256(calldata)[:4]]
20      57              JUMPI		// jump to 0x26 if 0x890D6908 == keccak256(calldata)[:4]
21      5B              JUMPDEST	// jump destination
22      6000            PUSH1 00	// [0x00, 0x00]
24      80              DUP1		// [0x00]
25      FD              REVERT		// revert
26      5B              JUMPDEST	// jump destination
27      00              STOP		// hault execution
```

we need to find a function whose signautre is `0x890d6908`.  

just gone to 4byte Ethereum Signature Database and searched for `0x890d6908`.  

Found that function signature of `0x890d6908` is `solve()`.

```
abi.encodePacked("solve()")	// 0x736f6c76652829
```

#### Solution

Enter the calldata: 0x736f6c76652829  

Puzzle solved!  

---

### Puzzle 5

ByteCode  

`6004361160385760003560e01c6000600034f57f00000000000000000000000034d5cbd8a2b5e1bb6952581ae65b47ed2bd5ef2d14603d575b600080fd5b00`

```
00      6004	PUSH1 04		// [0x04]
02      36	CALLDATASIZE	// [calldatasize, 0x04]
03      11	GT			// [calldatasize > 0x04]
04      6038	PUSH1 38 	// [0x38, calldatasize > 0x04]
06      57	JUMPI		// jump to 0x38 if calldatasize > 0x04
```
The contract will revert if the calldatasize greater than `4`.

```
07      6000	PUSH1 00	// [0x00]
09      35	CALLDATALOAD	// [calldata]
0A      60E0	PUSH1 E0	// [0xe0, calldata]
0C      1C	SHR				// [calldata[:4]]
0D      6000	PUSH1 00	// [0x00]
0F      6000	PUSH1 00	// [0x00, 0x00, calldata[:4]]
11      34	CALLVALUE		// [callvalue, 0x00, 0x00, calldata[:4]]
12      F5	CREATE2		// create contract
```

creating contract with the given value.

```
13      7F 00000000000000000000000034D5CBD8A2B5E1BB6952581AE65B47ED2BD5EF2D     PUSH32 00000000000000000000000034D5CBD8A2B5E1BB6952581AE65B47ED2BD5EF2D
```
```
34      14	EQ		// [push32ed value == created contract address]]
35      603D	PUSH1 3D	//[0x3d, push32ed value == created contract address]
37      57	JUMPI		// jump to 0x3d if push32ed value == created contract address
38      5B	JUMPDEST	// jump destination
39      6000	PUSH1 00	// [0x00]
3B      80	DUP1		// [0x00, 0x00]
3C      FD	REVERT		// revert
3D      5B	JUMPDEST	// jumpt destination
3E      00	STOP		// hault destination
```

we have to give a calldatasize with 4 bytes, contract created with given calldata is equal to `00000000000000000000000034D5CBD8A2B5E1BB6952581AE65B47ED2BD5EF2D`

I am not interested in bruteforce attack with my delicated laptop.  

so i search for writeup over internet and found from notonlyowner.  

answer is 0xdeadbeef but only works in evm.code's playground for first time only.

### Solution

? Enter the calldata: 0xdeadbeef

---


### Puzzle 6

ByteCode  

`346001116014573060003560601c1832146019575b600080fd5b00`

```
00	34        CALLVALUE	// [callvalue]
01	6001      PUSH1 01	// [0x01, callvalue]
03	11        GT		// [0x01 > callvalue]
04	6014      PUSH1 14	// [0x14, 0x01 > callvalue]
06	57        JUMPI		// jump to 0x14 if callvalue less than 1
```

The contract will revert if the callvalue is less than 1.  

```
07	30        ADDRESS	// [address(this)]
08	6000      PUSH1 00	// [0x00, address(this)]
0A	35        CALLDATALOAD	// [calldata, address(this)]
0B	6060      PUSH1 60	// [0x60, calldata, address(this)]
0D	1C        SHR		// [calldata[12:], address(this)]
0E	18        XOR		// [xor(calldata[12:], address(this))]
0F	32        ORIGIN	// [origin, xor(calldata[12:], address(this))]
10	14        EQ		// [origin == xor(calldata[12:], address(this))]
11	6019      PUSH1 19	// [0x19, origin == xor(calldata[12:], address(this))]
13	57        JUMPI		// jump to 0x19 if origin == xor(calldata[12:], address(this))
14	5B        JUMPDEST	// jump destination
15	6000      PUSH1 00	// [0x00]
17	80        DUP1		// [0x00, 0x00]
18	FD        REVERT	// revert
19	5B        JUMPDEST	// jump destination
1A	00        STOP		// hault execution
```
  
the ultimate way to solve the puzzle to find a address such that `xor(address, calldata) == origin`.  

below values in python code are taken from evm.code's playground.  

```python
address = 0x9bbfed6889322e016e0a02ee459d306fc19545d8
origin = 0xbe862ad9abfe6f22bcb087716c7d89a26051f74c
solution = address ^ origin
print(hex(solution))	// 0x2539c7b122cc4123d2ba859f29e0b9cda1c4b294
```

### Solution

Enter the value to send: 1  

Enter the calldata: 0x2539c7b122cc4123d2ba859f29e0b9cda1c4b294  

Puzzle solved!

---

### Puzzle 7

ByteCode  

`600160003504600190037fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff14603057fd5b00`

```
00	6001	PUSH1 01	// [0x01]
02	6000	PUSH1 00	// [0x00, 0x01]
04	35	CALLDATALOAD	// [calldata, 0x01]
05	04	DIV		// [calldata]
06	6001	PUSH1 01	// [0x01, calldata]
08	90	SWAP1		// [calldata, 0x01]
09	03	SUB		// [calldata - 0x01]
```

above code, subtracting 1 from calldata.

```
0A	7F FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF	PUSH32 FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
```
```
2B	14	EQ		// [2**256 == calldata - 0x01]
2C	6030	PUSH1 30	// [0x30, 2**256 == calldata - 0x01]
2E	57	JUMPI		// jump to 0x30 if 2**256 == calldata - 0x01
2F	FD	REVERT		// revert
30	5B	JUMPDEST	// jump destination
31	00	STOP		// hault execution
```

In order to solve this puzzle, we need to find a callvalue the wil leaves `2**256` when subtracted by 1.  

`callvalue - 1 == 2**256` => `callvalue = 0`  

we know `0 - 1 == 2**256`, underflow 

### Solution

Enter the calldata: 0x00  

Puzzle solved!  

---

### Puzzle 8

ByteCode  

`600060005260003560081b60f81c6001015b600f6000510160005280156026573490036011565b600051905060e114603657600080fd5b00`

```
00	6000      PUSH1 00			----
02	6000      PUSH1 00				|
04	52        MSTORE				|
05	6000      PUSH1 00				|
07	35        CALLDATALOAD			|
08	6008      PUSH1 08				|
0A	1B        SHL					|
0B	60F8      PUSH1 F8				|
0D	1C        SHR				----
0E	6001      PUSH1 01				| 2nd byte of calldata + 1
10	01        ADD					|
11	5B        JUMPDEST			------------
12	600F      PUSH1 0F				|		|
14	6000      PUSH1 00				|		|	add 0x0f + 32 byte of memory
16	51        MLOAD					|		|
17	01        ADD					|		|	and store it to memory
18	6000      PUSH1 00				|		|
1A	52        MSTORE	-------------		|
1B	80        DUP1							|
1C	15        ISZERO						|
1D	6026      PUSH1 26		--------|		|
1F	57        JUMPI					|		|
20	34        CALLVALUE				|		|
21	90        SWAP1					|		|
22	03        SUB					|		|
23	6011      PUSH1 11	-------------------
25	56        JUMP
26	5B        JUMPDEST
27	6000      PUSH1 00				|
29	51        MLOAD					| value in the memory should be 0xe1 (255)
2A	90        SWAP1					|
2B	50        POP
2C	60E1      PUSH1 E1
2E	14        EQ
2F	6036      PUSH1 36
31	57        JUMPI
32	6000      PUSH1 00
34	80        DUP1
35	FD        REVERT
36	5B        JUMPDEST
37	00        STOP
```

From `00` to `04`, the bytecode fill the 32 bytes of memory with zero's.  

From `05` to `0D`, calldata is loaded into stack and shifted 1 byte to left, 31 bit data with 1 byte zero, then that will be right shifted with 31 bytes then the stack will only contain `2nd byte` of the calldata.  

For each iteration b/w `11` and `23` value in the memory is increased with `0x0f` (15). to get that value to be `0xe1` (225), we need to iterate over 15 times.  

For each iteration the (2nd byte of calldata+1) is decreased by callvalue, at where the calldata-callvalue = 0, that must be after 15 iterations.

after the difference of calldata-callvalue = 0, also 1 more addition of 0x0f takes place, so we have to calculate to decrease 14 times.

let us take callvalue as 1.  

```
callvalue = 1
calldata = 0
for i in range(14):
	calldata = calldata + callvalue

print(calldata - 1) // 2nd byte of calldata + 1 is decreased	
```

#### Solution

Enter the value to send: 1  

Enter the calldata: 0x000d  

Puzzle solved!

---

Thank you for reading!
