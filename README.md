# Ramadhan's SparkCTF
## Reverse Engineering Writeup
### **Passi**

For this task we were given a **Binary**,based on the description it was asking to retrieve a password ! 

![Passi's Description](/Images/Passi1.png)

Running `file Binary` to determine what kind of binary we are dealing with 

![Passi's type](/Images/Passi2.png)

Executing the file to figure out its behaviour , the binary asks for a username input and most likely a comparison occurs to check it 

![Passi's type](/Images/Passi4.png)

We should opt for Ghidra or any decompiler to better understand this binary 

![Passi's type](/Images/Passi3.png)

It seems like the program is performing some kind of shuffle and then encrypt some data . The user input also will be checked first for its length and then compared to the encrypted data , our goal is to figure out this data which is our desired password to solve the task.
You can dig dipper in understanding the shuffle and encrypt function tho .

We will be opting for `gdb` acronym for GNU Debugger a tool that helps to debug the programs written in C .
As we saw in the code earlier, the input string must be 25 char long to pass the first verification before being compared to our wanted password .

![Passi's type](/Images/Passi5.png)

First of all we will be breaking main and running the program , once it reachs our first breakpoint we need to dissassemble the binary .
The assembly code may seem very overwhelming but all we need are basics where you can learn them accross the internet very easily, and the decompiled ghidra code earlier already broke down everything we need.

![Passi's type](/Images/Passi6.png)

The first highlighted line is out user input string length being compared to 25 '0x19' in hex , and in the second line is the strcmp function call between the input and the actual password, we need to set a breakpoint just before that.

![Passi's type](/Images/Passi7.png)

Just a normal behaviour the binary asks for input where I entered a random 25 characters string, after that we arrived at our desired breakpoint , if you look closely at the assembly code from the beginning you can see that the password where stored at '-0x28(%rbp)' which just before the comparison got moved to %rdx, by running x/s $rdx we can print out the password and thats our flag : `Spark{AAKZZBCRAKADSZKABRTZDRKTB}`

### **ASSemblY**

![Assembly](/Images/Ass0.png)

As the task name says , we were provided with an assembly code and an output , our mission is to retrieve the lost ID.
Analyzing the func function :

![Passi's type](/Images/Ass1.png)


-`mov DWORD PTR -20[rbp], edi` indicates that out func is taking int as an argument since DWORD stands for Double Word. It represents a data size of 32 bits or 4 bytes.
- `sal rax, 3` performs a left shift by 3 bits
- `xor QWORD PTR -8[rbp], 5` performs xor with 5
- `movabs rax, 5934314573` moves out const to the rax register
- `cmp QWORD PTR -8[rbp], rax` compares the last xor operation with the const 5934314573, and returns 1 if they are equal, 0 otherwise .

Analyzing the main function :

- It just call the func function with an argument

Now we know everything about our program its time to reverse it.

Note : the output file provided returned 1 so the comparision with the const must return true.

![Passi's type](/Images/Ass3.png)


And thats the flag : `Spark{741789321}`

![Passi's type](/Images/Ass4.png)



