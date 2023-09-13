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

<details><summary> 
	RTL Design using Verilog 
</summary>

<details><summary> Day 1 </summary>

#### Design:

A single or a set of verilog code which is written in order meet certain requirement functionalities.

#### Test Bench:

Pieces of code which are written in order to check if our design code meets the requirement and how accurately it performs its functionalities.

#### Simulator:

A simulator checks the RTL design's adherence to a particular spec with the help of a test bench. eg: IVERILOG is the simulator we are using for this course.

How does a simulator work ?

A simulator checks for the change in inputs and evaluates the corresponding change in its output.

### Simulation flow in iverilog 

Both the design code and the testbench code is fed to iverilog which is our simulator in this case and this simulator checks for changes in input and output is generated, this output will bein the form of a value change dump (vcd) format file, this vcd file is viewed using a gtkwave.

# LAB 1

Create a directory called vsd using ``` mkdir vsd ```.

In that directory git clone the following repoistory ``` git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git```

``` my_lib``` -> contains all the library files. It has two folders 
* lib which contains sky130 standard cell library. ( used for syntesis)
* verilog_model which contains all the standard cell verilog models


```verilog_files``` contaisn all the source files which we will be using for lab

[![Screenshot-from-2023-08-27-16-53-05.png](https://i.postimg.cc/28xnwpgp/Screenshot-from-2023-08-27-16-53-05.png)](https://postimg.cc/svvBjLVK)


# LAB 2

Commands used :

``` cd vsd ```

``` cd sky130RTLDesignAndSynthesisWorkshop ```

``` cd verilog_file ```

``` iverilog good_mux.v tb_good_mux.v ```

``` ./a.out ```

``` gtkwave tb_good_mux.vcd ```


[![Screenshot-from-2023-08-27-17-09-57.png](https://i.postimg.cc/yNGdPnVC/Screenshot-from-2023-08-27-17-09-57.png)](https://postimg.cc/bGQpYxDm)

[![Screenshot-from-2023-08-27-17-09-25.png](https://i.postimg.cc/SRkp7zzp/Screenshot-from-2023-08-27-17-09-25.png)](https://postimg.cc/r0nbqsJh)

### Codes in the source files we just ran:

##### Design code

[![Screenshot-from-2023-08-27-17-22-31.png](https://i.postimg.cc/Gm6708xw/Screenshot-from-2023-08-27-17-22-31.png)](https://postimg.cc/YvffYSPb)

##### Test bench code

[![Screenshot-from-2023-08-27-17-22-09.png](https://i.postimg.cc/3wpL38ty/Screenshot-from-2023-08-27-17-22-09.png)](https://postimg.cc/R3S1RB59)


#### Synthesizer
 Yosys is the synthesizer tool used for converting RTL to netlist( representation of the design in terms of the standard cells in .lib)

```  read_verilog ``` is used to for reading the design code.

``` read_liberty ``` used to read the .lib file.

``` write_verilog ``` to write the netlist file.

##### Verifying the netlist generated

We give the netlist file and the testbench file into the iverilog simulator and a vcd file is generator and the output waveform is generated in gtkwave and this waveform should be same as the output waveform of the rtl.


### Logic Synthesis

Behavioural representation of required specifications which is written in verilog HDL language is called as *RTL DESIGN*. RTL to gatelevel transistion is called as Synthesis, this gatelevel synthesis file is called as a netlist. We use .lib for this conversion, .lib consists of all the basic gates like AND, OR , NOT etc.

#### Why do we need different flavors of basic gates ?

In order to make our circuit faster we need the clock frequency to be HIGH, for that we need the time period of the clock to be low and the time period of clock depends on various factors like propogation delay of combinational logic, clock to q delay , setup delay ( in case of flip flops ) and various other delays depending upon the components used. 

This brings a question to our mind that why do we need slow cells at all ?

As we now know that we need faster cells in order to adjust the time period of the clock as low as possible, similarly we need to hold time to prevent hold time issues which again depends on the delays of the components used.

#### Faster vs slower cells

* The digital circuit depends on capacitance.
* Faster charging or discharging of capacitor => Lesser cell delay => we require wide transisters => more power and area consumption.
* More cell delay => narrow transistors => less power and area consumption.

Hence, its always a trade-off when it comes to speed vs power and area and we need to guide the synthesizer in such a way that is optimum for our logic circuits based on our constraints.

# LAB 3

To go to the required directory where all our source files are there, we use the following command :

``` cd vsd sky130RTLDesignAndSynthesisWorkshop verilog_files ``` 

To start yosys (our synthesizer) just type ``` yosys ``` and yosys promt is generated.

For reading the library : ``` read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

For reading the design ``` read_verilog good_mux.v ```

[![Screenshot-from-2023-08-28-15-01-03.png](https://i.postimg.cc/fb9JyKfd/Screenshot-from-2023-08-28-15-01-03.png)](https://postimg.cc/NyQgPRVG)

For synthesizing a design : ``` synth -top good_mux ```

[![Screenshot-from-2023-08-28-15-02-13.png](https://i.postimg.cc/DZNj67bH/Screenshot-from-2023-08-28-15-02-13.png)](https://postimg.cc/zbnFXZ7F)


For generating netlist : ``` abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

[![Screenshot-from-2023-08-28-15-03-15.png](https://i.postimg.cc/fT8KfznK/Screenshot-from-2023-08-28-15-03-15.png)](https://postimg.cc/rKtx8Lp0)

Type ``` show ``` in order to see the netlist pictorically 

[![Screenshot-from-2023-08-28-15-05-21.png](https://i.postimg.cc/pLxmr81c/Screenshot-from-2023-08-28-15-05-21.png)](https://postimg.cc/ykQ1rJK0)

To see the netlist code  : ``` gvim good_mux_netlist.v ```

[![Screenshot-from-2023-08-28-15-18-30.png](https://i.postimg.cc/85Lr5SVM/Screenshot-from-2023-08-28-15-18-30.png)](https://postimg.cc/JD4h6S0z)


</details>


<details><summary> DAY 2 </summary>

# LAB 4

To view the contents in .lib : ``` gvim ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

[![Screenshot-from-2023-08-28-15-39-59.png](https://i.postimg.cc/MGFDCR7H/Screenshot-from-2023-08-28-15-39-59.png)](https://postimg.cc/5H8LvXkW)

The first line in this is the name of the library : ``` library ("sky130_fd_sc_hd__tt_025C_1v80") ```

``` tt ```  => indicates variations due to process and here it indicates Typical Process.

``` 025C ``` => indicates the variations due to temperatures where the silicon will be used.

``` 1v80 ``` => indicates the variations due to the voltage levels where the silicon will be incorporated.

We compare the delay, power and area consumptions of 3 different types of AND gate.


# LAB 5

We will be looking at a module called as multiple_module in the verilog_files directory itself. To access the module file we use the following command.

``` gvim multiple_modules.v ```


And we get the source file of multiple_module.v which contains a simple OR gate as the first submodule and a simple AND gate as the second submodule, a module called multiple_module connects these two.


[![Screenshot-from-2023-08-28-16-02-42.png](https://i.postimg.cc/vTjYVG63/Screenshot-from-2023-08-28-16-02-42.png)](https://postimg.cc/30XQM51v)

Using ``` read_verilog multiple_modules.v ``` and ``` synth -top multiple_modules ``` , we get the following report.

[![Screenshot-from-2023-08-28-16-10-31.png](https://i.postimg.cc/qMyYHkv0/Screenshot-from-2023-08-28-16-10-31.png)](https://postimg.cc/KRcQnhTs)

generate netlist using : ``` abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```

 use ``` show multiple_modules ``` in order to see the netlist pictorically.

 [![Screenshot-from-2023-08-28-16-16-02.png](https://i.postimg.cc/3x6nkVR9/Screenshot-from-2023-08-28-16-16-02.png)](https://postimg.cc/yDmF5f0S)

 Ideally we are expected to see , an AND gate and an OR gate here but we see U1 and U2 instead this shows that this is a hierarchial model.

 ``` write_verilog -noattr multiple_modules_hier.v ```

``` !gvim multiple_modules_hier.v ```

[![Screenshot-from-2023-08-28-16-25-20.png](https://i.postimg.cc/pX6ntnq5/Screenshot-from-2023-08-28-16-25-20.png)](https://postimg.cc/HVbLwjGT)


### Flattened model :
 * Opposite of hierarchal model
 * The modular organization is not preserved.
 * All the modules and the submodules will be flattened out into *ONE* single module.

   ```read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
   
   ```read_verilog multiple_modules.v```

   ```synth -top multiple_modules```

   ```abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```

   ``` flatten ``` to write out a flattened netlist.

   ``` show ```


   [![Screenshot-from-2023-08-28-16-33-24.png](https://i.postimg.cc/13YG3jBB/Screenshot-from-2023-08-28-16-33-24.png)](https://postimg.cc/tsWZrDLn)

 ``` write_verilog -noattr multiple_modules_flat.v ```

``` !gvim multiple_modules_flat.v ```

[![Screenshot-from-2023-08-28-16-37-14.png](https://i.postimg.cc/63H2M7P0/Screenshot-from-2023-08-28-16-37-14.png)](https://postimg.cc/phFLTdRh)


# FLOPS

Each combinational circuit has a certain propgation delay, this propogation delay tends to cause glitches. These glitches can sometimes be ignored but as and when the number of combinational circuit block increases these glitches tend to become a bigger problem. That is when we use flip flops. FF is a sequential circuit whose output Q doesnt change unless clock edge occurs. This means that even if input of FF is glitching the output generated will be stable which acts as a stable input for the next block of combinational circuit block.


# LAB 6

## asynchronous reset on gtk wave 

```iverilog dff_asyncres.v tb_dff_asyncres.v```

```./a.out```

```gtkwave tb_dff_asyncres.vcd ```

[![Screenshot-from-2023-09-02-08-58-42.png](https://i.postimg.cc/gkKBTxtZ/Screenshot-from-2023-09-02-08-58-42.png)](https://postimg.cc/ykkPJ8H1)

when asyncres goes LOW, there is no immediate change in the output q, q waits for the pos edge of the clock in order to change after which the output q depends on the changes in input d and the pos edge of the clock.

[![Screenshot-from-2023-09-02-09-06-35.png](https://i.postimg.cc/SRbkDnTP/Screenshot-from-2023-09-02-09-06-35.png)](https://postimg.cc/Ppygq5kb)


Similarly, when asyncres again goes HIGH, the output q does not depend on the pos edge of the clock or the input d, it automatically goes LOW and stays there until there is a change in asyncres.

[![Screenshot-from-2023-09-02-09-06-35.png](https://i.postimg.cc/SRbkDnTP/Screenshot-from-2023-09-02-09-06-35.png)](https://postimg.cc/Ppygq5kb)

In this code we can see that, the waveform generated in gtkwave coincides with the code written. 

* ```  if(asyncres) q<=0 ```
This makes the output q go *LOW* everytime asyncres is *HIGH*.


* ``` else q<=d; ```
This makes the output q follow d at every pos edge of clock when asyncres is *LOW*.

### Synthesis 

``` yosys ```

``` read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

``` read_verilog dff_asyncres.v ```

``` synth -top dff_asyncres ```

``` dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```  dfflibmap is a keyword for d flip flops.

``` abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

``` show ```

[![Screenshot-from-2023-09-02-10-30-21.png](https://i.postimg.cc/0jS0DyrZ/Screenshot-from-2023-09-02-10-30-21.png)](https://postimg.cc/K3ZTyFPg)



## Asynchronous set on gtk wave 

``` iverilog dff_async_set.v tb_dff_async_set.v ```

``` ./a.out ```

``` gtkwave tb_dff_async_set.vcd ```

[![Screenshot-from-2023-09-02-09-25-03.png](https://i.postimg.cc/W3YTc91Q/Screenshot-from-2023-09-02-09-25-03.png)](https://postimg.cc/dkCz8jdB)

when async_set goes LOW, there is no immediate change in the output q, q waits for the pos edge of the clock in order to change after which the output q depends on the changes in input d and the pos edge of the clock.

[![Screenshot-from-2023-09-02-09-27-41.png](https://i.postimg.cc/Kc7CTpbF/Screenshot-from-2023-09-02-09-27-41.png)](https://postimg.cc/5j0Pd3jk)


Similarly, when async_set again goes HIGH, the output q does not depend on the pos edge of the clock or the input d, it automatically goes HIGH and stays there until there is a change in async_set.

[![Screenshot-from-2023-09-02-09-29-50.png](https://i.postimg.cc/wMx2LV5T/Screenshot-from-2023-09-02-09-29-50.png)](https://postimg.cc/gX53P3Bf)

In this code we can see that, the waveform generated in gtkwave coincides with the code written. 

* ```  if(async_set) q<=1 ```
This makes the output q go *HIGH* everytime async_set is *HIGH*.


* ``` else q<=d; ```
This makes the output q follow d at every pos edge of clock when async_set is *LOW*.

### Synthesis

Use the same commands with a different module name as done in asynchronous reset flip flop synthesis and we get the following netlist.

[![Screenshot-from-2023-09-02-10-36-11.png](https://i.postimg.cc/vTx3hv2r/Screenshot-from-2023-09-02-10-36-11.png)](https://postimg.cc/m1RY27sk)




## Synchronous reset on gtk wave 

``` iverilog dff_syncres.v tb_dff_syncres.v ```

``` ./a.out ```

``` gtkwave tb_dff_syncres.vcd ```


[![Screenshot-from-2023-09-02-09-58-53.png](https://i.postimg.cc/vBmTFxbS/Screenshot-from-2023-09-02-09-58-53.png)](https://postimg.cc/KKdxtzKn)

When sync_reset goes HIGH, output q doesnt immediately go LOW as it did in async reset flip flop, contrastingly it actually waits for the pos edge of clock in order for the output q to go low (does not depend on d).


[![Screenshot-from-2023-09-02-10-05-19.png](https://i.postimg.cc/k4jcqS8V/Screenshot-from-2023-09-02-10-05-19.png)](https://postimg.cc/DJXsc8xF)

When sync_reset goes LOW, the output q waits for the pos edge of the clock in order to follow the input d.


[![Screenshot-from-2023-09-02-10-09-14.png](https://i.postimg.cc/65ZSw969/Screenshot-from-2023-09-02-10-09-14.png)](https://postimg.cc/pp2kJMn7)

This code shows that we go into the always block and check the condition/ value of sync_res only and only if there is a posedge of clock which means that all the changes of output q only happens where there is a pos edge of clock even though changes in reset might happen at a diff time, the output q waits for the pos edge of clock in order to show any changes.

### Synthesis 

Use the same commands with a different module name as done in asynchronous reset flip flop synthesis and we get the following netlist.

[![Screenshot-from-2023-09-02-10-44-41.png](https://i.postimg.cc/FHYCHNVW/Screenshot-from-2023-09-02-10-44-41.png)](https://postimg.cc/FYQVPtq3)


# LAB 7

## mult_2

``` gvim mult_2.v ```

[![Screenshot-from-2023-09-02-10-57-17.png](https://i.postimg.cc/mgr3bjBT/Screenshot-from-2023-09-02-10-57-17.png)](https://postimg.cc/F72kpjY6)

When we write the truth table for the following code, we realise that in order to multiply any number by 2, we can just use a logical left shift operator (append the number with 1'b0) instead of using a multiplier.

``` yosys ```

``` read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

``` read_verilog mult_2.v ```

``` synth -top mul2 ```

[![Screenshot-from-2023-09-02-11-01-35.png](https://i.postimg.cc/43PTzXj6/Screenshot-from-2023-09-02-11-01-35.png)](https://postimg.cc/CZRt0ph5)

As there are no memory, no cells used. We can skip the abc command and directly type ``` show ```.

[![Screenshot-from-2023-09-02-11-02-55.png](https://i.postimg.cc/7YSP5rPV/Screenshot-from-2023-09-02-11-02-55.png)](https://postimg.cc/mt2WnqHz)


## mult_8

``` gvim mult_8.v ```

[![Screenshot-from-2023-09-02-11-23-01.png](https://i.postimg.cc/W419Lcb8/Screenshot-from-2023-09-02-11-23-01.png)](https://postimg.cc/LJGkL7Pg)

The output is multiplied by 9 and not 8 in this , because a(8+1) = (a*8 + a*1).

use the same commands as the previous mult_2.v and the following is generated.

[![Screenshot-from-2023-09-02-11-27-24.png](https://i.postimg.cc/DwLWvSmy/Screenshot-from-2023-09-02-11-27-24.png)](https://postimg.cc/QFNNSxpR)

``` write_verilog -noattr mult8_net.v ```

``` !gvim mult8_net.v ```

[![Screenshot-from-2023-09-02-11-31-21.png](https://i.postimg.cc/65YKdwSL/Screenshot-from-2023-09-02-11-31-21.png)](https://postimg.cc/svQbzF3v)


</details>


<details><summary> DAY 3 </summary>  

# LAB 8

### opt_check 

``` gvim opt_check.v ```

[![Screenshot-from-2023-09-02-12-43-08.png](https://i.postimg.cc/DyHnBG1f/Screenshot-from-2023-09-02-12-43-08.png)](https://postimg.cc/WhGB4h9x)

This is a 2:1 mux, i.e. when a=1 y=b and when a=0 y=0. 
Boolean expression : y=a'0+ab when you simplify it you get y=ab (implementation of a 2 input AND gate using a 2:1 MUX).

```read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

``` read_verilog opt_check.v ```

``` synth -top opt_check ``` 

``` opt_clean -purge ``` : removes all the unused wires and cells

``` abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

``` show ```


[![Screenshot-from-2023-09-02-13-04-21.png](https://i.postimg.cc/MGzksXyh/Screenshot-from-2023-09-02-13-04-21.png)](https://postimg.cc/K1psjGpN)

### opt_check2

[![Screenshot-from-2023-09-02-12-50-08.png](https://i.postimg.cc/P5GTQ3LF/Screenshot-from-2023-09-02-12-50-08.png)](https://postimg.cc/SJGByGfW)

This is a 2:1 mux, i.e. when a=1 y=1 and when a=0 y=b
Boolean expression : y=a1+a'b= a+a'b= a+b (using absorbtion law).

Use the same commands used for displaying the gate level diagram of opt_check but with a different module name.

[![Screenshot-from-2023-09-02-13-07-32.png](https://i.postimg.cc/3xS6Z9xD/Screenshot-from-2023-09-02-13-07-32.png)](https://postimg.cc/QF708Q5X)


### opt_check3


[![Screenshot-from-2023-09-02-13-35-13.png](https://i.postimg.cc/pT0GxJmt/Screenshot-from-2023-09-02-13-35-13.png)](https://postimg.cc/Q9TkJ5g6)

This code is for two 2:1 mux where when a=1 y= output of another mux (where c=1 y=b and c=0 y=0) and when a=0 y=0.

Boolean expression : y= a(cb+c'0)+a'0 = abc (3 input AND gate)

Use the same commands used for displaying the gate level diagram of opt_check but with a different module name.

[![Screenshot-from-2023-09-02-13-45-16.png](https://i.postimg.cc/wjgs98LD/Screenshot-from-2023-09-02-13-45-16.png)](https://postimg.cc/fJqbvPHb)



### opt_check4

[![Screenshot-from-2023-09-02-13-49-30.png](https://i.postimg.cc/Bby9M03k/Screenshot-from-2023-09-02-13-49-30.png)](https://postimg.cc/p5D6L3pJ)

Boolean expression : y= a(b(ac)+b'c)+(a'c') on simplification we get y= ac+a'c' = a xor c.


Use the same commands used for displaying the gate level diagram of opt_check but with a different module name.

[![Screenshot-from-2023-09-02-13-54-17.png](https://i.postimg.cc/x1NHsyv4/Screenshot-from-2023-09-02-13-54-17.png)](https://postimg.cc/xXYc8mFv)

### multiple_module_opt

[![Screenshot-from-2023-09-02-13-56-38.png](https://i.postimg.cc/ryWwYn3W/Screenshot-from-2023-09-02-13-56-38.png)](https://postimg.cc/RqSmWRMV)

Use the same commands used for displaying the gate level diagram of opt_check but with a different module name and ``` flatten ``` .

[![Screenshot-from-2023-09-02-13-59-54.png](https://i.postimg.cc/X7kFt7mw/Screenshot-from-2023-09-02-13-59-54.png)](https://postimg.cc/GH9Hyrc2)


# LAB 9

### dff_const1

``` gvim dff_const1.v ```

[![Screenshot-from-2023-09-02-14-19-46.png](https://i.postimg.cc/BQc3DtwP/Screenshot-from-2023-09-02-14-19-46.png)](https://postimg.cc/TpwBv2xT)

When reset is 1 the output q will become 0 but this happens only at the pos edge of clock, similarly when reset =0 the output 1 will become 1 not immediately but at the pos edge of clock.

#### simulation

``` iverilog dff_const1.v tb_dff_const1.v ```

```./a.out ```

``` gtkwave tb_dff_const1.vcd ```

[![Screenshot-from-2023-09-02-14-24-25.png](https://i.postimg.cc/522wWYhw/Screenshot-from-2023-09-02-14-24-25.png)](https://postimg.cc/jLBWz5d5)


#### synthesis 

```read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```

```read_verilog dff_const1.v```

```synth -top dff_const1```

```dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib ```

```abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```

```show```

[![Screenshot-from-2023-09-02-14-39-49.png](https://i.postimg.cc/8kr2SsRf/Screenshot-from-2023-09-02-14-39-49.png)](https://postimg.cc/w3gf5qb9)



### dff_const2

``` gvim dff_const2.v```

[![Screenshot-from-2023-09-02-14-43-56.png](https://i.postimg.cc/XY0G8Z8f/Screenshot-from-2023-09-02-14-43-56.png)](https://postimg.cc/F7DHFHwz)

The output q will always be 1 irrespective of the clock or reset value.

#### Simulation

[![Screenshot-from-2023-09-02-14-45-51.png](https://i.postimg.cc/26J7G42k/Screenshot-from-2023-09-02-14-45-51.png)](https://postimg.cc/wyXNTRCZ)

#### Synthesis 

[![Screenshot-from-2023-09-02-14-47-46.png](https://i.postimg.cc/K8wcZjqK/Screenshot-from-2023-09-02-14-47-46.png)](https://postimg.cc/Wdmc8NPj)


### dff_const3


```gvim dff_const3.v ```

[![Screenshot-from-2023-09-02-14-51-05.png](https://i.postimg.cc/RZrb4z1S/Screenshot-from-2023-09-02-14-51-05.png)](https://postimg.cc/SJrGf3t3)


#### Simulation

[![Screenshot-from-2023-09-02-14-58-08.png](https://i.postimg.cc/JnPZYjxD/Screenshot-from-2023-09-02-14-58-08.png)](https://postimg.cc/vgx4cgpb)

#### Synthesis

[![Screenshot-from-2023-09-02-14-59-58.png](https://i.postimg.cc/jS3j1ZYr/Screenshot-from-2023-09-02-14-59-58.png)](https://postimg.cc/LJfRYksy)

### dff_const4

[![Screenshot-from-2023-09-03-17-30-49.png](https://i.postimg.cc/43TjrHcG/Screenshot-from-2023-09-03-17-30-49.png)](https://postimg.cc/CRmv81Jr)

#### Simulation

[![Screenshot-from-2023-09-03-17-33-25.png](https://i.postimg.cc/5tSvmD1J/Screenshot-from-2023-09-03-17-33-25.png)](https://postimg.cc/QBVH84HY)


#### Synthesis 

[![Screenshot-from-2023-09-03-17-35-35.png](https://i.postimg.cc/hP8mLfBw/Screenshot-from-2023-09-03-17-35-35.png)](https://postimg.cc/0bQrx5rd)



# LAB 10

### counter_opt

``` gvim counter_opt.v ```

[![Screenshot-from-2023-09-03-17-37-51.png](https://i.postimg.cc/h414MxX9/Screenshot-from-2023-09-03-17-37-51.png)](https://postimg.cc/mhhGgPND)

This is code of a 3 bit counter where when reset is 1, the variable count goes back to 000 and if reset is not equal to 1 the variable count is incremented. The output q is assigned t the zeroeth bit of the variable count which means that the first and the second bit of the variable count is unused. We expect a three flip flop design but only find one flip flop in the design indicating that the count[1] and count[2] flip flops are unused.


    ```read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
    
    ```read_verilog counter_opt.v```
    
    ```synth -top counter_opt```
    
    ```dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
    
    ```abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
    
    ```show```



[![Screenshot-from-2023-09-03-17-48-57.png](https://i.postimg.cc/CxLWYnLp/Screenshot-from-2023-09-03-17-48-57.png)](https://postimg.cc/svqn4XhT)



# counter_opt2

``` gvim counter_opt2 ```

[![Screenshot-from-2023-09-03-17-51-46.png](https://i.postimg.cc/Fs1y495n/Screenshot-from-2023-09-03-17-51-46.png)](https://postimg.cc/2VRbdN54)

In this all the three bits of the variable counter are used hence we can expect three flip flops.

[![Screenshot-from-2023-09-03-17-56-03.png](https://i.postimg.cc/8kZY4Pmy/Screenshot-from-2023-09-03-17-56-03.png)](https://postimg.cc/XXyx7WhF)

</details>

<details><summary> DAY 4 </summary>

# LAB 11

``` gvim ternary_operator.v```

[![Screenshot-from-2023-09-03-19-30-37.png](https://i.postimg.cc/2j9nwT0Z/Screenshot-from-2023-09-03-19-30-37.png)](https://postimg.cc/XXFZNKSj)

``` iverilog ternary_operator_mux.v tb_ternary_operator_mux.v ```

```./a.out ```

``` gtkwave tb_ternary_operator_mux.vcd```

[![Screenshot-from-2023-09-03-19-34-15.png](https://i.postimg.cc/m2Rh79sH/Screenshot-from-2023-09-03-19-34-15.png)](https://postimg.cc/K4qZ2R4G)

Behaviour of a 2:1 mux.


    ```read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
    
    ```read_verilog ternary_operator_mux.v```
    
    ```synth -top ternary_operator_mux```
    
    ```abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib```
    
    ```show```


[![Screenshot-from-2023-09-03-19-40-04.png](https://i.postimg.cc/tCVDnL9T/Screenshot-from-2023-09-03-19-40-04.png)](https://postimg.cc/svs5tHQd)


```iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v```

```./a.out```

``` gtkwave tb_ternary_operator_mux.vcd```

### bad_mux

```gvim bad_mux.v ```

[![Screenshot-from-2023-09-03-19-46-05.png](https://i.postimg.cc/RV736RJf/Screenshot-from-2023-09-03-19-46-05.png)](https://postimg.cc/BXvZR2gQ)

[![Screenshot-from-2023-09-03-19-47-57.png](https://i.postimg.cc/QCkXtfYd/Screenshot-from-2023-09-03-19-47-57.png)](https://postimg.cc/RJhrpLzk)

[![Screenshot-from-2023-09-03-19-50-16.png](https://i.postimg.cc/kGhTxmp1/Screenshot-from-2023-09-03-19-50-16.png)](https://postimg.cc/n9BKJ6ZB)

[![Screenshot-from-2023-09-03-19-51-48.png](https://i.postimg.cc/c4nRDwg4/Screenshot-from-2023-09-03-19-51-48.png)](https://postimg.cc/3d30NyFP)




[![Screenshot-from-2023-09-03-19-44-27.png](https://i.postimg.cc/Z56WNL9p/Screenshot-from-2023-09-03-19-44-27.png)](https://postimg.cc/zyXDZKFG)



Similuator works on activity i.e. only change in input causes change in output.
</details>
</details>

<details><summary>Advanced Physical Design using OPENLANE/SKY130 </summary>

<details><summary>DAY1 </summary></details>

# Introduction to QFN -48 Package, chip, pads, core, die and IPs

* A QFN( Quad Flat No leads )  - 48 package

* PADS (peripheral devices ) act as a connection with which we can send signals inside and outside the chip.

* Core is the functional block of circuitry, it is where all the digital logic of our chip is placed.

* Die is the entire silicon wafer which contains the chip.

* The core consists of SOC, SRAM, ADC, DAC, PLL, SPI blocks.

* The SRAM, ADC, DAC, PLL are called as foundary IPs. A foundary is a factory which manufactures chips. It is also an Intelectual Property.

* The RISC V SOC and SPI blocks are called the macros. Basic difference between the macros and foundary IPs are that macros are purely digital circuits while Foundary IPs need some kind of unique design.


 ![padsdiecore](https://github.com/dishak14/pes_asic_class/assets/92496153/4f456f72-af50-449b-96e2-6b93ee4e0874)


![foundrymacros](https://github.com/dishak14/pes_asic_class/assets/92496153/7f142761-9192-4760-a1e7-4c008299476a)




# Introduction to RISC V

* RISC V is an Instruction Set Architecture.

* It is language of the computer i.e. we can interact with the computer using RISC V.

* C program --> RISC V assembly level code --> HDL --> RTL --> runs on layout.


![riscvss](https://github.com/dishak14/pes_asic_class/assets/92496153/70fa2db2-d2c9-4005-814d-73156995f273)


# From software application to Hardware 

* Application software enters into a block called system software.

* System software consists of various blocks:
 
1. Operatin system : which deals with the input/output operations, handles memory.
2. Compiler : converts the high level language into assembly level instructions.
3. Assembler : converts the assembly level instruction to binary which is known as the machine level code.

* After all this our application software will be ready to run on a hardware.

![astohardware](https://github.com/dishak14/pes_asic_class/assets/92496153/58705226-b6f7-4046-9d3d-eec3df602e0a)


# Simplified RTL2GDS flow


![rtl2gds](https://github.com/dishak14/pes_asic_class/assets/92496153/f62e4831-34f5-442d-be3b-0c2b4cd61c00)







  


















</details>
