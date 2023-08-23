# pes_asic_class
# VLSI PHYSICAL DESIGN FOR ASICs

![image](https://github.com/VardhanSuroshi/pes_asic_class/assets/132068498/33403244-c9dd-4aef-a022-da52e2eef51c)

This repository contains all the notes, lab work and assignments for the course VLSI Physical Design for ASICs, a special topic offered in PES University, Electronic City Campus.

This repo will be divided into different days of the course which will consist of the important points of the lecture as well as the lab work.

<details> <summary>
  Introduction
</summary>
  
  ## Lecture 1
In order to run a C Program on an hardware chip we need to follow the following steps :
* The C program is first compiled and converted to an assembly language program(hexadecimal), we use the RISCV ISA for this course.
* This assembly language program is further converted into a machine language (binary) program.
* This RISCV specifications need to be implemented using a Hardware Descriptive Language (HDL) and an RTL is generated
* The RTL is then intgrated with the hardware and the required output is generated.
  
[![Screenshot-from-2023-08-20-21-11-59.png](https://i.postimg.cc/4xwtTFSL/Screenshot-from-2023-08-20-21-11-59.png)](https://postimg.cc/nXjM4TZB)

## Lecture 2

Application software --> Operating System --> Compiler --> Assembler --> Hardware 

Compiler converts the C program to assembly level program which consists of instructions in the form of .exe file. These instructions act as an abstract interface between the C program and the hardware, it is called Instruction Set Architecture (ISA). (PART 1 OF THE COURSE)

The instruction set is taken into account and a corresponding HDL code is written for it which when synthesized gives us a Gate Level RTL netlist, the physical design implementation of the netlist is created which gives us the layout of the hardware. (PART 2 OF THE COURSE)

</details>

<details><summary> 
	Day 1
</summary>
	
## Lecture 4

### C program to find sum of n natural numbers:
```#include<stdio.h>

int main() 
{
	int i, sum=0, n=15;
	for (i=1 ; i <=n; ++i)
	{
		sum +=i;
	}
	printf("sum of numbers from 1 to %d = %d\n", n, sum);
	return 0;
}

```
### RESULT : 
[![Screenshot-from-2023-08-20-22-27-51.png](https://i.postimg.cc/NF6vq2B1/Screenshot-from-2023-08-20-22-27-51.png)](https://postimg.cc/64pmC373)

## Lecture 5

For compiling our C program using a RISC V GCC compiler, we will use the following command on the terminal. 

```riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c```

lp => long pointer
march => the architecture to be used which in this case is risc v 64 

In order to find the assembly level code for our C program we will run the following command on the terminal.

```riscv64-unknown-elf-objdump -d sum1ton.o | less```

we will then find main from the bunch of hexadecimal code.

[![Screenshot-from-2023-08-21-15-06-26.png](https://i.postimg.cc/QMdGPYrr/Screenshot-from-2023-08-21-15-06-26.png)](https://postimg.cc/v4kNxzBP)

We can see that there are 13 instruction under the main branch. 

Now we are going to compare the number of instructions under main when we run a different command on the terminal.

```riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c```

Just as we did before, we need to find the assembly level code for our program for which we will run deassmble command again.

```riscv64-unknown-elf-objdump -d sum1ton.o | less```

[![Screenshot-from-2023-08-21-15-29-04.png](https://i.postimg.cc/DwGsbhjx/Screenshot-from-2023-08-21-15-29-04.png)](https://postimg.cc/k2nDk0dS)

We notice that there are 12 instructions in the main branch.

Hence, we can conclude that using Ofast instead of O1 reduces the number of instructions generated.

## Lecture 6

To run the C program on a RISC V compiler we use enter the following command on the terminal.

```spike pk sum1ton.o```

[![Screenshot-from-2023-08-21-15-49-41.png](https://i.postimg.cc/tJrNRHR9/Screenshot-from-2023-08-21-15-49-41.png)](https://postimg.cc/rRrrg3tH)

In the above screenshot, we have run the C program using both gcc compiler as well as RISC V compiler and we can conclude that we get the same result in both the cases.

In order to debug in spike we use the command :

```spike -d pk sum1ton.o```

If we want our Program Counter to run till a certain instruction at 100b0 after which we want to run it manually we run the following command 

```until pc 0 100b0```

The instruction at 100b(lui a2,0x1) will change the value at register a2. The following screenshot shows the value in reg a2 before and after the execution of the instruction at the address 100b0.

[![Screenshot-from-2023-08-21-16-07-10.png](https://i.postimg.cc/KvsvBc0t/Screenshot-from-2023-08-21-16-07-10.png)](https://postimg.cc/dLrYKFP0)

Hence, we can say that the value in register a2 is changed.

Similarly if we run the next command we load an immediate value of 21 into the register a0 ( lui a0,0x21)

[![Screenshot-from-2023-08-21-16-11-17.png](https://i.postimg.cc/DyYL0TsM/Screenshot-from-2023-08-21-16-11-17.png)](https://postimg.cc/ykckp2Lm)

The next command is (addi sp, sp, -16) to notice the changes in the value stored in the stack pointer (sp) we will first find the value in the stack pointer before execution of the instruction and then again find the value in it after the exectution of the instruction.

[![Screenshot-from-2023-08-21-16-17-24.png](https://i.postimg.cc/qB2QYgPx/Screenshot-from-2023-08-21-16-17-24.png)](https://postimg.cc/6T5d4658)

(-16) is in decimal form which when converted to hexadecimal form gives (-10), we notice a difference of 10 in the addresss stored in stack pointer before and after the exectuion of the instruction further proving our point.

## Lecture 7

Humans understand decimal numbers and computers understand binary numbers, we need an interface or technique to convert decimal numbers to binary and vice versa. 

Basic RISC V keywords :
* double word (dw) : 64 bit binary values
* word (w) : 32 bit binary values
* byte (b) : 8 bit binary values

Using 2 bits we can represent only 4 numbers (0,1,2,3) in binary form.

Using 3 bits we can represent 7 numbers in binary form.

Using 4 bits we can represent 15 numbers in binary form.

Using 64 bits we can represent (2^64 - 1) unsigned positive numbers in binary form.

In case of 64 bit binary number, we multiply the LSB with 2^0 and keep multiplying each digit by 2^n where n increases from 0 to 64 ( MSB being multiplied 2^64) all of this is added and a decimal number is produced.

Range of numbers in RV64 


[![Screenshot-from-2023-08-21-20-53-50.png](https://i.postimg.cc/tRFk2Jdp/Screenshot-from-2023-08-21-20-53-50.png)](https://postimg.cc/xqTLd0F4)

## Lecture 8 

We use two's complement representation to represent negative numbers, i.e. given a decimal number with a negative sign, we convert it to its binary form invert all the bits and add 1. This represents negative number.

For positive numbers, the MSB is 0.
For negative numbers, the MSB is 1.

Finiding the decimal form of a signed binary number is similar to that of unsigned binary number just that the MSB will be multiplied with (-2^63) and added to the rest.

Or you can do reverse of two's component.

Range is (0 to 2^63-1) positive numbers and -1 to -2^63 in negative numbers.

## Lecture 9 

To find max value of unsigned long long int (double word), we use the following code:

```
#include<stdio.h>
#include<math.h>
int main()
{
unsigned long long int max = (unsigned long long int) (pow(2,64)-1);
printf("highest number represented by unsigned long long int is %llu\n", max);
return 0;
}
```


If we run the above program using riscv gcc compiler we get the following result 

[![Screenshot-from-2023-08-21-21-44-56.png](https://i.postimg.cc/B6KKqJxJ/Screenshot-from-2023-08-21-21-44-56.png)](https://postimg.cc/phPTCbGS)

Similarly when we change the assigned unsigned long long int to a negative number and run the above code we get the result as 0.

C Program to find the maximum and minimum value of signed numbers

```
#include<stdio.h>
#include<math.h>
int main()
{
long long int max = (long long int) (pow(2,63)-1);
long long int min = (long long int) (pow(2,63)*(-1));
printf("max signed long long int is %lld\n", max);
printf("min signed long long int is %lld\n", min);
return 0;
}
```

[![Screenshot-from-2023-08-21-21-59-12.png](https://i.postimg.cc/3xv9cQqQ/Screenshot-from-2023-08-21-21-59-12.png)](https://postimg.cc/qNp8NS01)

</details>

<details> <summary> Day 2</summary>

## Lecture 10:

In order to write an application software we need to write a program using standard libraries, the interface between these two is called API (application program interface). The interface between the operating system and the assembly level code is called as ISA ( instruction set architecture). In order for the user to access this assembly level code directly we use system calles this system call is called APPLICATION BINARY INTERFACE (ABI).

ABI accesses the system via registers.

IN riscv32 there are 32, 32 bit registers and in risv64 there are 32, 64 bit registers.

## Labwork for Day 2

In this lab we re wrote the sum of n natural numbers C program in ASM language using ABI and check if we still get the same result.

[![Screenshot-from-2023-08-21-22-36-13.png](https://i.postimg.cc/XvnYXFtH/Screenshot-from-2023-08-21-22-36-13.png)](https://postimg.cc/ZWDSM9bp)

### C Program for sum from 1 to 9

```
#include<stdio.h>

extern int load(int x, int y);

int main(){
	int result = 0;
	int count = 9;
	result = load(0x0, count+1);
	printf("sum of numbers from 1 to %d is %d\n", count, result);
}
```
### Assembly level program

```
.section .text
.global load
.type load, @function

load:
	add a4, a0, zero
	add a2, a0, a1
	add a3, a0, zero
loop:	add a4, a3, a4
	addi a3, a3, 1
	blt a3, a2, loop
	add a0, a4, zero
	ret
```

## Simulation Result 

[![Screenshot-from-2023-08-21-22-53-17.png](https://i.postimg.cc/sXrnSf3g/Screenshot-from-2023-08-21-22-53-17.png)](https://postimg.cc/4KWbRgKD)

 



</details>
