# pes_asic_class
# VLSI PHYSICAL DESIGN FOR ASICs

This repository contains all the notes, lab work and assignments for the course VLSI Physical Design for ASICs, a special topic offered in PES University, Electronic City Campus.

This repo will be divided into different days of the course which will consist of the important points of the lecture as well as the lab work.

<details> <summary>
  DAY 1
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


</details>
