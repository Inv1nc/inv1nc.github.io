---
title: Decipher EVM Puzzles Solutions
layout: post
date: 2024-08-09
permalink: decipher-evm-puzzles-writeup
category: web3
description: My Solutions to Decipher EVM Puzzles
author: Inv1nc
---

Hello Friend,

This is writeup containing notes while i am solving Decipher EVM Puzzles which were featured in week in ethereum news-April 2023, I am not interested in explaining opcode by opcode, where you can find them in [EVM Codes](https://www.evm.codes/) in detail.  

The coolest thing is I understand everything while writing stack items in comments without any tools. For better visualization of how evm opcode work under the hood, i highly recommend you to paste bytecode into [EVM Codes - Playground](https://www.evm.codes/playground) and play with it. I hope you like this writeup.  

![](assets/images/2024/24080901.png)

Solutions to puzzles can have more a formulae instead of one specific value.

### Initial Setup

```
git clone https://github.com/zaryab2000/decipher_EVM_Puzzles.git
npm install
npx hardhat play
```

## Easy

### Puzzle 1

bytecode  

`34366005011460100156FDFDFDFDFDFDFD5b00`

```
00      34        CALLVALUE	// [callvalue]
01      36        CALLDATASIZE	// [calldatasize, callvalue]
02      6005      PUSH1 05	// [0x05, calldatasize, callvalue]
04      01        ADD		// [0x05 + calldatasize, callvalue]
05      14        EQ		// [0x05 + calldatasize == callvalue]
06      6010      PUSH1 10	// [0x10, 0x05 + calldatasize == callvalue]
08      01        ADD		// [0x10 + (0x05 + calldatasize == callvalue)]
09      56        JUMP		// jump to [0x10 + (0x05 + calldatasize == callvalue)]
0A      FD        REVERT
0B      FD        REVERT
0C      FD        REVERT
0D      FD        REVERT
0E      FD        REVERT
0F      FD        REVERT
10      FD        REVERT
11      5B        JUMPDEST
12      00        STOP
```

Inorder to solve this puzzle we need to make sure that  

`0x10 + (0x05 + calldatasize == callvalue) == 0x11`.  

`(0x05 + calldatasize == callvalue)` must be satisfied to jump to `0x11`.  

let us take calldata of size `0x00`, then

```python
0x05 + 0x00 = callvalue
callvalue = 0x05
```

**Solution**

? Enter the value to send: 5  

? Enter the calldata: 0x  

Puzzle solved!  

### Puzzle 2

bytecode  

`36600455600a6004540156FDFDFD6002600aFD5b00`

```
00      36        CALLDATASIZE	// [calldatasize]
01      6004      PUSH1 04	// [0x04, calldatasize]
03      55        SSTORE	// store calldatasize at 0x04 in storage slot
04      600A      PUSH1 0A	// [0x0A]
06      6004      PUSH1 04	// [0x04, 0x0A]
08      54        SLOAD		// [calldatasize, 0x0A]
09      01        ADD		// [calldatasize + 0x0A]
0A      56        JUMP		// jump to instruction `calldatasize + 0x0A`
0B      FD        REVERT
0C      FD        REVERT
0D      FD        REVERT
0E      6002      PUSH1 02
10      600A      PUSH1 0A
12      FD        REVERT
13      5B        JUMPDEST
14      00        STOP
```

Inorder to solve this puzzle, we need to provide calldata of size that satisfy condition   

`calldatasize + 0x0A == 0x13`

```python
calldatasize = 0x13 - 0x0A
calldata = "0x" + ("00" * calldatasize)
print(calldata) # 0x000000000000000000
```

**Solution**

? Enter the calldata: 0x000000000000000000  

Puzzle solved!  

### Puzzle 3

bytecode  

`34600a053401340156fdfdfdfdfdfd5b00`

```
00      34        CALLVALUE	// [callvalue]
01      600A      PUSH1 0A	// [0x0A, callvalue]
03      05        SDIV		// [0x0A / callvalue]
04      34        CALLVALUE	// [callvalue, (0x0A / callvalue)]
05      01        ADD		// [callvalue + (0x0A / callvalue)]
06      34        CALLVALUE	// [callvalue, callvalue + (0x0A / callvalue)]
07      01        ADD		// [callvalue + callvalue + (0x0A / callvalue)]
08      56        JUMP		// jump to [callvalue + callvalue + (0x0A / callvalue)]
09      FD        REVERT
0A      FD        REVERT
0B      FD        REVERT
0C      FD        REVERT
0D      FD        REVERT
0E      FD        REVERT
0F      5B        JUMPDEST
10      00        STOP
```

Inorder to solve this puzzle we need to provide with a callvalue that must satisfy condition  

`callvalue + callvalue + (0x0A / callvalue) == 0x0F`.  

`2 * callvalue + (0x0A / callvalue) == 0x0F`,  

let us consider `0x0A / callvalue == 1`. then `2 * callvalue = 0x0E`  

```python
2 * callvalue = 0x0E
callvalue = 7
```

**Solution**

? Enter the value to send: 7  

Puzzle solved!  

#### Puzzle 4

bytecode  

`600634600008340156fdfdfdfdfd5b00`

```
00      6006      PUSH1 06	// [0x06]
02      34        CALLVALUE	// [callvalue, 0x06]
03      6000      PUSH1 00	// [0x00, callvalue, 0x06]
05      08        ADDMOD	// [callvalue / 0x06]
06      34        CALLVALUE	// [callvalue, (callvalue / 0x06)]
07      01        ADD		// [callvalue + (callvalue / 0x06)]
08      56        JUMP		// jump to [callvalue + (callvalue / 0x06)]
09      FD        REVERT
0A      FD        REVERT
0B      FD        REVERT
0C      FD        REVERT
0D      FD        REVERT
0E      5B        JUMPDEST
0F      00        STOP
```

Inorder to solve this puzzle, we need to provide with a callvalue that must satisfy  

`callvalue + (callvalue / 0x06) == 0x0E`.  

let us consider `callvalue / 0x06 = 1`, then

```python
callvalue + 1 = 0x0E
callvalue = 0x0E - 1
callvalue  = 0x0D	# 13
```

**Solution**

? Enter the value to send: 13  

Puzzle solved!  

### Puzzle 5

bytecode

`602e361c56fdfdfdfdfdfd5b00`

```
00      602E      PUSH1 2E	// [0x2E]
02      36        CALLDATASIZE	// [calldatasize, 0x2E]
03      1C        SHR		// 0x2E >> calldatasize 
04      56        JUMP		// jump to 0x2E >> calldatasize
05      FD        REVERT
06      FD        REVERT
07      FD        REVERT
08      FD        REVERT
09      FD        REVERT
0A      FD        REVERT
0B      5B        JUMPDEST
0C      00        STOP
```

Inorder to solve this puzzle we need to provide with calldata of size that satisfy  

`0x2E >> calldatasize == 0x0B`  

```python
0x0B - 1011
0x2E - 101110	# we need to shit 2 bits
```

we need to provide calldata of size 2.  

**Solution**

? Enter the calldata: 0x0000  

Puzzle solved!  

---

## Medium

### Puzzle 1

`600235345234360435345114601357fdfdfdfd5b00`

```
00      6002      PUSH1 02	// [0x02]
02      35        CALLDATALOAD	// [calldata[2:2+32]]
03      34        CALLVALUE	// [callvalue, calldata[2:2+32]]
04      52        MSTORE	// store calldata[2:2+32] in memory at offset callvalue 
05      34        CALLVALUE	// [callvalue]
06      36        CALLDATASIZE	// [calldatasize, callvalue]
07      04        DIV		// [calldatasize / callvalue]
08      35        CALLDATALOAD	// [calldata[calldatasize / callvalue: ]]  load 32 bytes
09      34        CALLVALUE	// [callvalue, calldata[calldatasize / callvalue: ]]
0A      51        MLOAD		// [calldata[2:2+32]]
0B      14        EQ		// [calldata[2:2+32] == calldata[calldatasize / callvalue: ]]
0C      6013      PUSH1 13	// [0x13, eq above]
0E      57        JUMPI		// jump to 0x13 if [calldata[2:2+32] == calldata[calldatasize / callvalue: ]
0F      FD        REVERT
10      FD        REVERT
11      FD        REVERT
12      FD        REVERT
13      5B        JUMPDEST
14      00        STOP
```

In order to solve this puzzle we need provide calldata and callvalue that satisfy  

`calldata[2 : 2+32] == calldata[(calldatasize / callvalue) : (calldatasize / callvalue) + 32]`  

let use take callvalue is 0.

`calldata[2 : 2+32] == calldata[ : 32]`

then take calldata nothing, it will load as 0's  

**Solution**

? Enter the value to send: 0  

? Enter the calldata: 0x  

Puzzle solved!

### Puzzle 2

bytecode  

`3634553436553654345401600a14601757FDFDFDFDFDFD5b00`

```
00      36        CALLDATASIZE	// [calldatasize]
01      34        CALLVALUE	// [callvalue, calldatasize]
02      55        SSTORE	// store calldatasize at callvalue storage slot
03      34        CALLVALUE	// [callvalue]
04      36        CALLDATASIZE	// [calldatasize, callvalue]
05      55        SSTORE	// store callvalue at calldatasize storage slot
06      36        CALLDATASIZE	// [calldatasize]
07      54        SLOAD		// [callvalue]
08      34        CALLVALUE	// [callvalue, callvalue]
09      54        SLOAD		// [calldatasize, callvalue]
0A      01        ADD		// [calldatasize + callvalue]
0B      600A      PUSH1 0A	// [0x0A, calldatasize + callvalue]
0D      14        EQ		// [0x0A == calldatasize + callvalue]
0E      6017      PUSH1 17	// [0x17, 0x0A == calldatasize + callvalue]
10      57        JUMPI		// jump to 0x17 if 0x0A == calldatasize + callvalue
11      FD        REVERT
12      FD        REVERT
13      FD        REVERT
14      FD        REVERT
15      FD        REVERT
16      FD        REVERT
17      5B        JUMPDEST
18      00        STOP
```

In order to solve this puzzle, we need to provide calldata and callvalue that satisfy   

`0x0A == calldatasize + callvalue`

let us take calldata of size 0.  

```python
calldatasize = 0
callvalue = 0x0A
print(callvalue)	# 10
```

**Solution**

? Enter the value to send: 10  

? Enter the calldata: 0x  

Puzzle solved!

### Puzzle 3

bytecode  

`3634600a033455503634600b03553454600136540314601c57FDFDFD5b00`

```
00      36        CALLDATASIZE	// [calldatasize]
01      34        CALLVALUE	// [callvalue, calldatasize]
02      600A      PUSH1 0A	// [0x0A, callvalue, calldatasize]
04      03        SUB		// [0x0A - callvalue, calldatasize]
05      34        CALLVALUE	// [callvalue, 0x0A - callvalue, calldatasize]
06      55        SSTORE	// store 0x0A - callvalue at callvalue storage slot
07      50        POP		// []
08      36        CALLDATASIZE	// [calldatasize]
09      34        CALLVALUE	// [callvalue, calldatasize]
0A      600B      PUSH1 0B	// [0x0B, callvalue, calldatasize]
0C      03        SUB		// [0x0B - callvalue, calldatasize]
0D      55        SSTORE	// store 0x0B - callvalue at calldatasize storage slot
0E      34        CALLVALUE	// [callvalue]
0F      54        SLOAD		// [0x0A - callvalue]
10      6001      PUSH1 01	// [0x01, 0x0A - callvalue]
12      36        CALLDATASIZE	// [calldatasize, 0x01, 0x0A - callvalue]
13      54        SLOAD		// [0x0B - callvalue, 0x01, 0x0A - callvalue]
14      03        SUB		// [0x0A - callvalue , 0x0A - callvalue]
15      14        EQ		// [1]
16      601C      PUSH1 1C	// [0x1C]
18      57        JUMPI		// jump to 0x1C
19      FD        REVERT
1A      FD        REVERT
1B      FD        REVERT
1C      5B        JUMPDEST
1D      00        STOP
```

The conditions to solve this puzzle is that callvalue should be less than 0x0A rather `0x0A - callvalue` will underflow.  

calldatasize and callvalue should be different.  

let us take calldata of size `0` and callvalue of `1`.  

**Solution**

? Enter the value to send: 1  

? Enter the calldata: 0x  

Puzzle solved!

### Puzzle 4

bytecode

`6032341c600516800256fdfdfdfdfdfd5b00`

```
00      6032      PUSH1 32	// [0x32]
02      34        CALLVALUE	// [callvalue, 0x32]
03      1C        SHR		// [0x32 >> callvalue]
04      6005      PUSH1 05	// [0x05, 0x32 >> callvalue]
06      16        AND		// [0x05 & (0x32 >> callvalue)]
07      80        DUP1		// [0x05 & (0x32 >> callvalue), 0x05 & (0x32 >> callvalue)]
08      02        MUL		// [0x05 & (0x32 >> callvalue) * 0x05 & (0x32 >> callvalue)]
09      56        JUMP		// jump to 0x05 & (0x32 >> callvalue) ** 2
0A      FD        REVERT
0B      FD        REVERT
0C      FD        REVERT
0D      FD        REVERT
0E      FD        REVERT
0F      FD        REVERT
10      5B        JUMPDEST
11      00        STOP
```

the jump destination is 0x10, i.e 16.  

To solve this puzzle we need to satisfy following condition  

`0x05 & (0x32 >> callvalue) ** 2 == 0x10`  

`0x05 & (0x32 >> callvalue) == 0x04`

```python
0x05 -  101
0x32 - 110010	# shift two values, results 0x0c
```

```python
12 - 1100 
5  -  101
&
------
4  - 100
```

**Solution**

? Enter the value to send: 2  

Puzzle solved!  

### Puzzle 5

bytecode  

`60063681810280026012900456fdfdfdfdfd5b505000`

```
00      6006      PUSH1 06	// [0x06]
02      36        CALLDATASIZE	// [calldatasize, 0x06]
03      81        DUP2		// [0x06, calldatasize, 0x06]
04      81        DUP2		// [calldatasize ,0x06, calldatasize, 0x06]
05      02        MUL		// [calldatasize * 0x06, calldatasize, 0x06]
06      80        DUP1		// [calldatasize * 0x06, calldatasize * 0x06, calldatasize, 0x06]
07      02        MUL		// [(calldatasize * 0x06) ** 2, calldatasize, 0x06]
08      6012      PUSH1 12	// [0x12, (calldatasize * 0x06) ** 2, calldatasize, 0x06]
0A      90        SWAP1		// [(calldatasize * 0x06) ** 2, 0x12, calldatasize, 0x06]
0B      04        DIV		// [((calldatasize * 0x06) ** 2) / 0x12, calldatasize, 0x06]]
0C      56        JUMP		// jump to ((calldatasize * 0x06) ** 2) / 0x12
0D      FD        REVERT
0E      FD        REVERT
0F      FD        REVERT
10      FD        REVERT
11      FD        REVERT
12      5B        JUMPDEST
13      50        POP
14      50        POP
15      00        STOP
```

Inorder to solve this puzzle we need to satisfy below eqn  

`((calldatasize * 0x06) ** 2) / 0x12 == 0x12`

i.e `(calldatasize * 0x06) ** 2` should be equal than `0x12 ** 2`  

take square root on both sides.  

```python
(calldatasize * 0x06)  == 0x12
calldatasize = 3
calldata = "0x" + "00" * calldatasize
print(calldata)	# 0x000000
```

**Solution**

? Enter the value to send: 0  

? Enter the calldata: 0x000000  

Puzzle solved!

---

## Hard

### Puzzle 1

bytecode

`60008035905236600034f0808031903b14908031903b020156fdfdfdfdfdfdfdfdfdfdfdfd5b00`

```
00      6000      PUSH1 00	// [0x00]
02      80        DUP1		// [0x00, 0x00]
03      35        CALLDATALOAD	// [calldataload, 0x00]
04      90        SWAP1	// [0x00, calldataload]
05      52        MSTORE	// store first 32 bytes of calldata at 0x00 in memory
06      36        CALLDATASIZE	// [calldatasize]
07      6000      PUSH1 00	// [0x00, calldatasize]
09      34        CALLVALUE	// [callvalue, 0x00, calldatasize]
0A      F0        CREATE	// create contract
0B      80        DUP1		// [address, address]
0C      80        DUP1		// [address, address, address]
0D      31        BALANCE	// [balance, address, address]
0E      90        SWAP1		// [address, balance, address]
0F      3B        EXTCODESIZE	// [codesize, balance, address]
10      14        EQ		// [codesize == balance, address]
11      90        SWAP1		// [address, codesize == balance]
12      80        DUP1		// [address, address, codesize == balance]
13      31        BALANCE 	// [balance, address, codesize == balance]
14      90        SWAP1		// [address, balance, codesize == balance]
15      3B        EXTCODESIZE	// [codesize, balance , codesize == balance]
16      02        MUL		// [codesize * balance, codesize == balance]
17      01        ADD		// [(codesize * balance) + (codesize == balance)]
18      56        JUMP		// jump to [(codesize * balance) + (codesize == balance)]
19      FD        REVERT
1A      FD        REVERT
1B      FD        REVERT
1C      FD        REVERT
1D      FD        REVERT
1E      FD        REVERT
1F      FD        REVERT
20      FD        REVERT
21      FD        REVERT
22      FD        REVERT
23      FD        REVERT
24      FD        REVERT
25      5B        JUMPDEST
26      00        STOP
```

Inorder to solve this puzzle we need to satisfy below condition  

`(codesize * balance) + (codesize == balance) == 0x25`


let us consider `codesize == balance`, then

```python
(codesize * balance) + (codesize == balance) == 0x25
(balance * balance) + (1) == 0x25
balance ** 2 = 0x24
balance = 0x06
```

```python
push6 0x111111111111
push1 0x00
mstore
push1 0x06	// size to return
push1 0x1a	// offset 0x20 - 0x06
return
# 0x651111111111115f526006601af3
```


**Solution**

? Enter the value to send: 6  

? Enter the calldata: 0x651111111111116000526006601af3  

Puzzle solved!

### Puzzle 2

bytecode  

`363411600857fdfd5b3634061534901c3515601b57fdfdfdfdfdfd5b00`

```
00      36        CALLDATASIZE	// [calldatasize]
01      34        CALLVALUE	// [callvalue, calldatasize]
02      11        GT		// [callvalue > calldatasize]
03      6008      PUSH1 08	// [0x08, callvalue > calldatasize]
05      57        JUMPI		// jump to 0x08 if callvalue > calldatasize
06      FD        REVERT
07      FD        REVERT
08      5B        JUMPDEST	
09      36        CALLDATASIZE	// [calldatasize]
0A      34        CALLVALUE	// [callvalue, calldatasize]
0B      06        MOD		// [callvalue % calldatasize]
0C      15        ISZERO	// [1]
0D      34        CALLVALUE	// [callvalue, 1]
0E      90        SWAP1		// [1, callvalue]
0F      1C        SHR		// [callvalue >> 1]
10      35        CALLDATALOAD	// [calldata, callvalue >> 1]
11      15        ISZERO	// [iszero(calldata), callvalue >> 1]
12      601B      PUSH1 1B	// [0x1B, iszero(calldata), callvalue >> 1]
14      57        JUMPI		// jump to 0x1B
15      FD        REVERT
16      FD        REVERT
17      FD        REVERT
18      FD        REVERT
19      FD        REVERT
1A      FD        REVERT
1B      5B        JUMPDEST
1C      00        STOP
```

To solve this puzzle we need to provide callvalue that must be greater than calldatasize.  

**Solution**

? Enter the value to send: 1  

? Enter the calldata: 0x  

Puzzle solved!  

### Puzzle 3

bytecode  

`36600a14600957fdfd5b6000803590523660006000f03b3515602057fdfdfdfd5b00`

```
00      36        CALLDATASIZE	// [calldatasize]
01      600A      PUSH1 0A	// [0x0A, calldatasize]
03      14        EQ		// [0x0A == calldatasize]
04      6009      PUSH1 09	// [0x09, 0x0A == calldatasize] 
06      57        JUMPI		// jump to 0x09 if calldatasize == 0x0A
07      FD        REVERT
08      FD        REVERT
09      5B        JUMPDEST
0A      6000      PUSH1 00	// [0x00]
0C      80        DUP1		// [0x00, 0x00]
0D      35        CALLDATALOAD	// [calldata, 0x00, 0x00]
0E      90        SWAP1		// [0x00, calldata, 0x00]
0F      52        MSTORE	// store calldata in memory at offset 0x00
10      36        CALLDATASIZE	// [calldatasize, 0x00]
11      6000      PUSH1 00	// [0x00, calldatasize, 0x00]
13      6000      PUSH1 00	// [0x00, 0x00, calldatasize, 0x00]
15      F0        CREATE	// [address, 0x00]
16      3B        EXTCODESIZE	// [codesize, 0x00]
17      35        CALLDATALOAD	// [calldata, codesize, 0x00]
18      15        ISZERO	// [iszero(calldata), codesize, 0x00]
19      6020      PUSH1 20	// [0x20, iszero(calldata), codesize, 0x00]
1B      57        JUMPI
1C      FD        REVERT
1D      FD        REVERT
1E      FD        REVERT
1F      FD        REVERT
20      5B        JUMPDEST
21      00        STOP
```

In order to solve this puzzle we need to provide with a calldata of size `0x0A`.

```python
calldatasize = 0x0a
calldata = "0x" + "00" * calldatasize
print(calldata)	# 0x00000000000000000000
```

**Solution**

? Enter the calldata: 0x00000000000000000000  

Puzzle solved!  

### Puzzle 4

bytecode  

`363410600114600b57fdfd5b60058034106001146022573411600114602d57fdfdfd5b3436163614602d57fdfd5b3634173614603a57fdfdfdfd5b00`

```
00      36        CALLDATASIZE	// [calldatasize]
01      34        CALLVALUE	// [callvalue, calldatasize]
02      10        LT		// [callvalue < calldatasize]
03      6001      PUSH1 01	// [0x01, (callvalue < calldatasize)]
05      14        EQ		// [0x01 == (callvalue < calldatasize)]
06      600B      PUSH1 0B 	// [0x0B, 0x01 == (callvalue < calldatasize)]
08      57        JUMPI		// jump to 0x0B if 0x01 == (callvalue < calldatasize)
09      FD        REVERT
0A      FD        REVERT
0B      5B        JUMPDEST
0C      6005      PUSH1 05	// [0x05]
0E      80        DUP1		// [0x05, 0x05]
0F      34        CALLVALUE	// [callvalue, 0x05, 0x05]
10      10        LT		// [callvalue < 0x05, 0x05]
11      6001      PUSH1 01	// [0x01, callvalue < 0x05, 0x05]
13      14        EQ		// [0x01 == callvalue < 0x05, 0x05]
14      6022      PUSH1 22	// [0x22, 0x01 == callvalue < 0x05, 0x05]
16      57        JUMPI		// jump to 0x22 if callvalue is less than 0x05
17      34        CALLVALUE	// [callvalue, 0x05, 0x05]
18      11        GT		// [callvalue > 0x05, 0x05]
19      6001      PUSH1 01	// [0x01, callvalue > 0x05, 0x05]
1B      14        EQ		// [0x01 == callvalue > 0x05, 0x05]
1C      602D      PUSH1 2D	// [0x2D, 0x01 == callvalue > 0x05, 0x05]
1E      57        JUMPI		// jump to 0x2D if callvalue is greater than 0x05
1F      FD        REVERT
20      FD        REVERT
21      FD        REVERT
22      5B        JUMPDEST	// [0x05]
23      34        CALLVALUE	// [callvalue, 0x05]
24      36        CALLDATASIZE	// [calldatasize, callvalue, 0x05]
25      16        AND		// [calldatasize & callvalue, 0x05]
26      36        CALLDATASIZE	// [calldatasize, calldatasize & callvalue, 0x05]
27      14        EQ		// [calldatasize == calldatasize & callvalue, 0x05]
28      602D      PUSH1 2D	// [0x2D, calldatasize == calldatasize & callvalue, 0x05]
2A      57        JUMPI		// jump to 0x2D if calldatasize == calldatasize & callvalue
2B      FD        REVERT
2C      FD        REVERT
2D      5B        JUMPDEST	// [0x05]
2E      36        CALLDATASIZE	// [calldatasize, 0x05]
2F      34        CALLVALUE	// [callvalue, calldatasize, 0x05]
30      17        OR		// [callvalue or calldatasize, 0x05]
31      36        CALLDATASIZE	// [calldatasize, callvalue or calldatasize, 0x05]
32      14        EQ		// [calldatasize == callvalue or calldatasize, 0x05]
33      603A      PUSH1 3A	// [0x3A, calldatasize == callvalue or calldatasize, 0x05]
35      57        JUMPI
36      FD        REVERT
37      FD        REVERT
38      FD        REVERT
39      FD        REVERT
3A      5B        JUMPDEST
3B      00        STOP
```

In order to solve this puzzle we need to meet following conditions.  

1. callvalue is less than calldatasize.
2. calldatasize == (callvalue or calldatasize)
3. from line `0x1B` i will take callvalue greater than 0x5 to jump directly to `0x2D`.

let us take callvalue `6`.  

as calldatasize should be greater than callvalue. we can take calldata of 7.  

```python
callvalue = 6
calldatasize = callvalue + 1
calldata = "0x" + "00" * calldatasize
print(calldata)	# 0x00000000000000
```
**Solution**

? Enter the value to send: 6  

? Enter the calldata: 0x00000000000000  

Puzzle solved!

### Puzzle 5

bytecode  

`60023604600514600c57fdfd5b60008035905236600034f08031600511600114602657fdfdfd5b36341b360156fdfdfdfdfd5b00`

```
00      6002      PUSH1 02	// [0x02]
02      36        CALLDATASIZE	// [calldatasize, 0x02]
03      04        DIV		// [calldatasize / 0x02]
04      6005      PUSH1 05	// [0x5, calldatasize / 0x02]
06      14        EQ		// [0x5 == (calldatasize / 0x02)]
07      600C      PUSH1 0C	// [0x0C, 0x5 == (calldatasize / 0x02)]
09      57        JUMPI		// jump to 0x0C if 0x5 == (calldatasize / 0x02)
0A      FD        REVERT
0B      FD        REVERT
0C      5B        JUMPDEST
0D      6000      PUSH1 00	// [0x00]
0F      80        DUP1		// [0x00, 0x00]
10      35        CALLDATALOAD	// [calldata, 0x00, 0x00]
11      90        SWAP1		// [0x00, calldata, 0x00]
12      52        MSTORE	// store calldata into memory at offset 0x00
13      36        CALLDATASIZE	// [calldatasize, 0x00]
14      6000      PUSH1 00	// [0x00, calldatasize, 0x00]
16      34        CALLVALUE	// [callvalue, 0x00, calldatasize, 0x00]
17      F0        CREATE	// [address, 0x00]
18      80        DUP1		// [address, address, 0x00]
19      31        BALANCE	// [balance, address, 0x00]
1A      6005      PUSH1 05	// [0x05, balance, address, 0x00]
1C      11        GT		// [0x05 > balance, address, 0x00]
1D      6001      PUSH1 01	// [0x01, 0x05 > balance, address, 0x00]
1F      14        EQ		// [0x01 == 0x05 > balance, address, 0x00]
20      6026      PUSH1 26	// [0x26, x01 == 0x05 > balance, address, 0x00]
22      57        JUMPI		// jump to 0x26 if balance less than 0x05
23      FD        REVERT
24      FD        REVERT
25      FD        REVERT
26      5B        JUMPDEST
27      36        CALLDATASIZE	// [calldatasize]
28      34        CALLVALUE	// [callvalue, calldatasize]
29      1B        SHL		// [calldatasize << callvalue]
2A      36        CALLDATASIZE	// [calldatasize, calldatasize << callvalue]
2B      01        ADD		// [calldatasize + (calldatasize << callvalue)]
2C      56        JUMP		// jump to calldatasize + (calldatasize << callvalue)
2D      FD        REVERT
2E      FD        REVERT
2F      FD        REVERT
30      FD        REVERT
31      FD        REVERT
32      5B        JUMPDEST
33      00        STOP
```


`0x5 == (calldatasize / 0x02)`, calldatasize should be equal to 10

callvalue less than 5  

In order to solve this puzzle `calldatasize + (calldatasize << callvalue) == 0x32`  

```python
calldatasize = 10 (0x0a)
calldatasize << callvalue = 0x28
```

```python
0x0a - 1010
0x28 - 101000  # we need to left shift 2 bits
```

callvalue = 2  


let us create a calldata with 10 bytes.

```
push1 0x00
push1 0x00
mstore
push1 0x00
push1 0x00
return
// 0x600060005260006000f3
```

**Solution**

? Enter the value to send: 2  

? Enter the calldata: 0x600060005260006000f3  

Puzzle solved!

---

? Do you want to play the next puzzle? Yes  

You are a Rockstar 🔥! You Solved all Puzzles  

---

Thanks for reading!
