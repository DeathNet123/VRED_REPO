In von Neumann architecture, the code and data are stored in the same memory unit and processed by the same processor. This architecture was proposed by mathematician and physicist John von Neumann in 1945. It has been widely adopted since then, due to its simplicity and flexibility. The basic idea behind von Neumann architecture is that all instructions are stored as data and all data can be used as instructions. This allows for a much more efficient use of memory than storing code and data separately, as was done previously.

But it lead to many security issues. Since data and code are stored in same memory, hackers can use the data to be used as code. In other words they can send the carefully crafted data that can be directly executed by the processor. 

### How does it get Injected?
Usually, programmers make mistakes and we use those mistakes to inject our shellcode. There are many ways to inject shellcode. In this post, we'll learn how can we write carefully crafted input data that can execute directly and can gain access to different system resource. For example:
```c
void bye1() { puts("Goodbye!"); }
void bye2() { puts("Farewell!"); }
void hello(char *name, void (*bye_func)())
{
	printf("Hello %s!\n", name);
	bye_func();
}
int main(int argc, char **argv)
{
	char name[1024];
	gets(name);
	srand(time(0));
	if (rand() % 2) hello(bye1, name);
	else hello(name, bye2);
}
```
Compile with:  `gcc -z execstack -o hello hello.c`

In the above code, when the value of `rand()` is even, the `hello()` function will be called with the arguments `name` and `bye2`. But when the value of `rand()` is odd, the `hello()` function will be called with the arguments `bye1` and `name`, which is not correct way to call the hello function. It takes the second parameter as a function pointer, but we are passing a string. This is a classic example of shellcode injection. We can simply use the gets() function to inject our shellcode in the form of crafted input data.

### Why "shell"code?
Usually, the goal of an exploit is to achieve arbitrary command execution. This is done by injecting a shellcode that will spawn a shell. The shellcode is usually written in assembly language. The shellcode is then converted to machine code and injected into the vulnerable program. The shellcode is then executed by the processor and the shell is spawned.