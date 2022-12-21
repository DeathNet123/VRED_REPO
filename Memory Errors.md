<hr>

In the early days of computing, programmers would write machine code by hand, using a series of switches or a punched card or paper tape to represent the ones and zeros. This was a time-consuming and error-prone process, and it required a deep understanding of the underlying hardware and architecture of the computer.

Over time, higher-level programming languages were developed that allowed programmers to write code in a more abstract, human-readable form. These languages are translated into machine code by a compiler or interpreter before they can be run on a computer.

Dennis Ritchie developed C in 1972 with the goal of creating a programming language that was portable across different computer architectures and provided developers with low-level control over memory access. C is known for its close mapping to assembly language and its ability to effectively execute without many runtime surprises.

### Memory Corruptions

<hr>

Memory corruption occurs when a program writes data to memory in an unintended way, often resulting in errors or vulnerabilities in the system. The idea of memory isolation between processes, as proposed by Graham et al., was an important step in addressing these concerns by limiting the ability of one process to access or modify the memory of another process. This helps to prevent malicious or poorly written programs from causing unintended consequences on the system. Memory isolation is an important concept in modern operating systems and is used to improve security and prevent data corruption.

### Problems with C

<hr>

##### Trusting the Developers

In Python:

```python
a = [ 1, 2, 3 ]
print a[10] = 0x41;
#IndexError: list index out of range
```

In C:

```c
int a[3] = { 1, 2, 3 };
 a[10] = 0x41;
  // no problem!
```

In the Python code snippet, when the developer attempts to access the 11th element in the list `a` using the index `10`, they receive an `IndexError` because the list only has 3 elements and the index is out of range. This is a built-in safety mechanism in Python that prevents developers from accidentally accessing or modifying elements that do not exist in the list.

On the other hand, in the C code snippet, the developer is able to assign the value `0x41` to the 11th element of the array `a` without any error or exception being thrown. This is because C does not have built-in bounds checking for arrays, and the developer is responsible for ensuring that they do not access or modify elements that are out of range.

If the developer attempts to access or modify an element that is out of range in C, the program will continue to execute and the result will depend on the specific behavior of the underlying hardware and operating system. This can lead to unpredictable or unintended consequences, such as data corruption or a crash.

Therefore, one issue with using C is that developers must be careful to ensure that they do not access or modify elements that are out of range, as there is no built-in protection against this type of error. In contrast, languages like Python provide built-in mechanisms to prevent such errors and protect against memory corruption.

#### Mixing the Control Information with User Data.

<hr>

When a program starts up, it may have data that has been influenced by the user already present. This data may come from a variety of sources, such as user input, configuration files, or command line arguments. This user-influenced data is often stored in variables and used by the program during execution.

![[Pasted image 20221221221845.png]]

As the program executes, this user data can spread throughout the program and be used in various parts of the code. However, it is important to ensure that user data does not directly control program execution. This is because user data is considered "non-control" data and should not be used to determine the flow of the program.

![[Pasted image 20221221221928.png]]

However, user data is often stored in the same memory as "control" data, which is data that determines the flow of the program. This can create vulnerabilities if the user data is not properly validated or sanitized before being used. For example, if user data is used to determine the size of an array, an attacker could potentially manipulate the user data to create a buffer overflow or other type of memory corruption.

To prevent these types of vulnerabilities, it is important to properly validate and sanitize user data before using it in the program and to carefully separate control data from non-control data in memory.

![[Pasted image 20221221221949.png]]


In C, the stack is a region of memory that stores various types of data related to the current function and its calling functions. This data can include local variables, pointers to other locations in the stack or in memory, and pointers to code (return addresses). All of this data is stored together in the stack and treated in the same way. If a user is able to overwrite control data, such as a return address, they can use this to redirect the control flow of the program elsewhere or on the stack containing our shellcode.

```c
int a[3] = { 1, 2, 3 };
a[10] = 0x41;
```

For example, in the C code snippet provided, the array `a` and its elements are stored on the stack along with other data, such as local variables and pointers. When the developer attempts to assign the value `0x41` to the 11th element of the array `a`, this operation is performed on the stack without any bounds checking. As a result, it is possible for the developer to access or modify elements that are out of range, which can lead to unpredictable or unintended consequences.

#### Mixing Data with Meta Data

<hr>

In C, strings are typically represented as arrays of characters that are terminated with a NULL byte. For example, the code snippet declares a character array `name` of size 10 and initializes it with the string "Yan". This means that the `name` array will hold 10 bytes in memory, with the first 3 bytes containing the characters 'Y', 'a', and 'n', and the remaining 7 bytes initialized to NULL. The NULL byte at the end of the string serves as a terminator and implicitly encodes the length of the string data.

```c
char name[10] = "Yan";
read(0, name, sizeof(name));
```

If the `read` function is called with the third argument being `sizeof(name)`, it will read up to 10 bytes from the input and store them in the `name` array. If there are NULL bytes in the input, they will be stored in the `name` array along with the other characters and will not terminate the string.

On the other hand, if there are no NULL bytes in the input, the `read` function will continue to read and store characters until it reaches the end of the input or the maximum number of bytes specified (in this case, 10). If the input is longer than 10 bytes, the `read` function will start storing characters starting from the address where the name is stored in memory and eventually overwrite the NULL byte required in string by C standards.

#### Cleanup Problem

<hr>

In C, it is the responsibility of the developer to manage memory and other resources during the execution of a program. This includes properly initializing and releasing resources to prevent resource leaks. If these tasks are not performed correctly, the program may suffer from performance degradation or even failure.

```c
void my_function()  
{
	char my_variable[8];
}
// what is the value of my_variable here?
```
For example, in the code snippet provided, the variable `my_variable` is declared as an array of characters with size 8. However, the value of `my_variable` is not initialized at the time it is declared. This means that the contents of the `my_variable` array are undefined and may contain any values. It is the responsibility of the developer to initialize the `my_variable` array to a known state before using it in the program.
In general, it is important to carefully consider resource management in C and to properly initialize and release resources to prevent resource leaks and ensure the proper functioning of the program.


