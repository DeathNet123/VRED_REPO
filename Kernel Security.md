### What is Kernel ?

An operating system kernel is the central part of an operating system that manages the resources of the computer and provides a platform for other software to run. It is the lowest level of the operating system, and it communicates directly with the computer's hardware.

The kernel is responsible for managing the interactions between processes and coordinating their access to resources such as memory, input/output devices, and other hardware. It performs tasks such as scheduling processes, allocating memory, and managing input/output operations.

Consider Pakistan-Centric View, In this context, individual processes can be thought of as provincial governments, while the kernel acts as the federal government, responsible for managing relations between the processes and coordinating their actions. The kernel is a critical component of the operating system, as it is responsible for ensuring that the processes running on the system are able to function correctly and efficiently.

### Kernel Resources

Kernel Manages Resources between processes. The Kernel determines whether code can access these resources based on the privileges granted to the process running the code. The kernel controls access to these resources and grants permission to processes as needed. For example, a process may need to request access to a hardware peripheral through the kernel, which will then determine whether to allow the access based on the privileges of the process and the availability of the resource.

>[!info]
>Granting permission here means kernel perform the task asked by user process. Such as reading the bytes from the file via read syscall

But few Resources are only available to kernel and
Examples of kernel-only resources:
-   The hlt instruction, which shuts off CPU computation.
-   The in and out instructions for interacting with hardware peripherals.
-   Special registers such as cr3 (Control Register 3) and MSR_LSTAR (Model-Specific Register, Long Syscall Target Address Register).

### What makes a kernel special?

In a computer system, the CPU (central processing unit) tracks a privilege level that controls access to resources. This privilege level is generally split into "rings" of access, with higher rings having more privileges and lower rings having fewer privileges.

![[Pasted image 20221228193811.png|240]]

-   Ring 3: This is the lowest privilege level and is often referred to as "userspace." Processes running at this level are typically those that are initiated by user applications and have very restricted access to system resources.
  
-   Rings 2 and 1: These rings are generally unused in modern operating systems.
  
-   Ring 0: This is the highest privilege level and is referred to as "kernel mode" or "supervisor
   mode." It is reserved for the operating system kernel and other system-level processes. Processes running at this level have unrestricted access to all system resources and can perform tasks such as modifying memory and accessing hardware peripherals.

The CPU tracks the current privilege level to ensure that processes only have access to the resources they are authorized to use. When a process running at a lower privilege level (such as Ring 3) needs to access a restricted resource, it must request access through the kernel. The kernel will then determine whether to grant the request based on the privileges of the process and the availability of the resource.

Supervisor mode (also known as kernel mode or Ring 0) can pose a security risk when running multiple operating systems on a single physical machine using virtualization technology.

In the early 2000s, one solution to this issue was to force the kernel of the virtual machine (VM) into Ring 1, which has fewer privileges than Ring 0. This prevented the VM kernel from having unrestricted access to the host's physical hardware, but it also required the use of costly and complex emulation methods to simulate some Ring 0 actions of the guest operating system.

A more modern solution is to use a special privilege level known as hypervisor mode, or Ring -1. This level is used by the hypervisor, which is a software layer that sits between the hardware and the operating systems and manages the virtualization of the hardware. When a VM kernel attempts to perform a sensitive Ring 0 action, the hypervisor is able to intercept the request and handle it in the host operating system, rather than allowing the VM kernel to execute the action directly.

This allows the VM kernel to operate in a more isolated environment, while still providing the necessary resources and support for the guest operating system to function. It also helps to improve security by limiting the access of the VM kernel to sensitive system resources and preventing it from potentially damaging the host operating system or other VMs running on the same physical machine.

>[!Note]
>The Ring system is true for "amd64" architecture and may not be true for others.


### Different OS Models

An operating system kernel can be classified into one of three categories based on its design: monolithic, microkernel, or hybrid.

-   **Monolithic kernel**: In this type of kernel, there is a single, unified kernel binary that handles all OS-level tasks. Drivers, which are responsible for interacting with hardware devices, are implemented as libraries that are loaded into this kernel binary. Examples of operating systems that use a monolithic kernel include Linux and FreeBSD.
  
-   **Microkernel**: In a microkernel-based operating system, there is a small "core" kernel binary that provides basic inter-process communication and interacts with the hardware. Drivers are implemented as normal userspace programs that have slightly special privileges. Examples of operating systems that use a microkernel include Minix and seL4.
  
-   **Hybrid kernel**: A hybrid kernel is a combination of a microkernel and a monolithic component. It combines the benefits of both designs, allowing for a more flexible and modular kernel architecture. Examples of operating systems that use a hybrid kernel include Windows NT and macOS.

### Linux Model

In a Linux operating system, drivers are typically implemented as kernel modules, which are loadable pieces of code that can be added to or removed from the kernel at runtime. Kernel modules provide a way to extend the functionality of the kernel without having to rebuild it each time a new device or feature is added.

When a device is connected to a Linux system, the kernel will scan for a matching driver module that is capable of interacting with the device. If a matching driver is found, it will be loaded into the kernel and used to communicate with the device. If no matching driver is found, the device may not be functional until a suitable driver is installed.

Kernel modules can be loaded or unloaded dynamically as needed, allowing for a more flexible and modular approach to device support. This is one of the key features of the Linux operating system that enables it to support a wide range of hardware devices and configurations.

### Drivers in Linux

In a Linux operating system, drivers are implemented as kernel modules and run in Ring 0, which is the highest privilege level and is reserved for the operating system kernel and other system-level processes. This means that drivers have unrestricted access to all system resources and can perform tasks such as modifying memory and accessing hardware peripherals.

This is necessary because drivers are responsible for interacting with hardware devices, which often require access to low-level system resources. However, it is important to note that not all kernel modules are drivers; some may provide other types of functionality such as filesystem support, networking support, or security features.

It's also worth noting that the concept of privilege levels and rings is not specific to Linux; it is a general feature of modern operating systems that is used to control access to resources and protect the system from unauthorized access or misuse. In other operating systems, the names and specific details of these privilege levels may vary, but the basic concept is similar.

![[Pasted image 20221228194602.png]]

### Switching between rings 

The syscall instruction is a mechanism for allowing processes running in userspace (Ring 3) to request services or resources from the kernel (Ring 0). The process of calling a syscall can be summarized as follows:

1.  At bootup, the kernel sets the MSR_LSTAR (Model-Specific Register, Long Syscall Target Address Register) to point to the address of the syscall handler routine.                                                                           
   
2.  When a userspace process needs to access a kernel resource, it calls the syscall instruction. This causes a privilege level switch from Ring 3 to Ring 0.
   
3.  The control flow jumps to the address stored in MSR_LSTAR, which is the starting address of the syscall handler routine. The return address (the address to which control should be returned after the syscall handler routine completes) is saved to the rcx register.
   
4.  The syscall handler routine performs the requested service or resource access and returns control to the calling process when it is finished.
   
5.  When the kernel is ready to return control to the userspace process, it calls the appropriate return instruction (such as sysret for syscall). This causes a privilege level switch from Ring 0 back to Ring 3.
   
6.  The control flow then jumps to the address stored in rcx, which is the return address saved earlier. This completes the syscall process and returns control to the userspace process.

### Kernel-Userspace Relationship

In a modern operating system, each process has its own virtual memory space, which is a mapping of virtual addresses to physical memory addresses. Virtual memory allows processes to use more memory than is physically available by temporarily storing some of the data on disk and swapping it in and out as needed.

In many operating systems, including Linux, userspace processes (processes running in Ring 3) are typically assigned virtual memory addresses in the lower range of the address space, while the kernel (which runs in Ring 0) is assigned virtual memory addresses in the upper range of the address space. This separation helps to protect the kernel from unauthorized access by user processes and to ensure that kernel memory is only accessible from Ring 0.

When a system call is made, the privilege level is switched from Ring 3 to Ring 0, but the virtual memory mapping is not changed. This means that the process can still access its own virtual memory, but it does not have access to the kernel's virtual memory space. The kernel's memory is only accessible from Ring 0, so the process must make a system call to request access to kernel resources.

![[Pasted image 20221228195958.png|500]]

### Kernel Vulnerabilities

Code in the kernel is just code and is subject to the same types of vulnerabilities as code running in other contexts. It is also important to note that the kernel has access to a wide range of system resources and can perform tasks such as modifying memory and accessing hardware peripherals. This means that vulnerabilities in the kernel can potentially allow an attacker to gain access to sensitive system data or to execute arbitrary code with the privileges of the kernel. As a result, it is important to take steps to secure the kernel and to prevent unauthorized access or misuse.

### Attack life Cycle

kernel exploits can come from a variety of sources and can be used to achieve a range of malicious objectives. Here is a summary of the attack lifecycle for kernel exploits:

1.  Attack sources: Kernel exploits can come from a few different directions, including:
-   From the network: Remotely-triggered exploits, such as packets of death, can be used to attack the kernel over the network. These types of attacks are relatively rare, but can be particularly dangerous if successful.

-   From userspace: Vulnerabilities in syscall and ioctl handlers (functions that allow processes to request services or resources from the kernel) can be exploited from within a userspace process. This can be done through a sandboxed environment, such as a web browser, or through a compromised application.

-   From devices: Kernel exploits can also be launched from attached devices, such as USB hardware. These types of attacks can be difficult to detect and can allow an attacker to gain access to the kernel even if the system is otherwise secure.

2.  Attack objectives: Once an attacker has gained access to the kernel, they can use the exploit to achieve a number of malicious objectives, including:
-   Act on userspace: Kernel exploits can be used to perform privilege escalation, which allows an attacker to gain elevated privileges within the system. They can also be used to install rootkits, which are malicious software programs that allow an attacker to maintain control of the system even after the initial exploit has been discovered and removed.

-   Get more access: Kernel exploits can also be used to gain access to other parts of the system, such as trusted execution environments, which can allow the attacker to further escalate their privileges or to attack other systems on the network.

### Kernel Modules

A kernel module is a piece of code that can be loaded into the kernel of an operating system at runtime. It is similar to a userspace library, in that it is a compiled binary file that contains code that can be accessed and executed by other programs. However, there are some key differences between kernel modules and userspace libraries:

-   File format: Kernel modules are typically compiled as ELF (Executable and Linkable Format) files, just like userspace libraries. However, kernel modules use the ".ko" file extension instead of ".so" (shared object).

-   Address space: When a kernel module is loaded, it is placed into the address space of the kernel, rather than into the address space of a userspace process. This means that the code in the module runs with the same privileges as the kernel, and has unrestricted access to all system resources.

-   Functionality: Kernel modules are used to implement a wide range of functionality in an operating system, including device drivers (which allow the kernel to communicate with hardware devices), filesystems (which provide a way to organize and access files on the system), networking functionality (such as support for network protocols and device drivers), and various other types of functionality.

>[!warning]
>Kernel Modules in Linux run with Ring 0 Privilage level so be careful writing them and exploiting them on real system because they can cause your hardware rather useless

### Interacting with Modules

Historically, it was possible for kernel modules to add system call entries to the kernel's system call table, which is a data structure that contains a list of all the system calls that are supported by the kernel. 

Modifying the system call table allowed kernel modules to add new system calls to the kernel, which could then be used by userspace processes to access new functionality or resources. However, this approach had some significant drawbacks, including:

-   Unsupported: Modifying the system call table is explicitly unsupported in modern kernels, and can cause instability or other problems if done incorrectly.
   
-   Security risks: Adding new system calls to the kernel can potentially introduce security vulnerabilities, as it allows processes running in userspace to access kernel resources that may not have been adequately tested or secured.

-   Compatibility issues: Adding new system calls to the kernel can also cause compatibility issues with other software that expects the kernel to support a certain set of system calls.   

As a result, modern kernels generally do not allow kernel modules to modify the system call table, and other approaches are typically used to add functionality to the kernel. These approaches may include using other types of kernel interfaces, such as ioctl (input/output control) or sysfs (system file system), or by implementing new system calls as part of the kernel itself.

#### Modules and Interupts

Theoretically it is possible for a kernel module to register an interrupt handler by using the LIDT (Load Interrupt Descriptor Table) and LGDT (Load Global Descriptor Table) instructions, and to be triggered by an interrupt instruction, such as int 3 or int 1. 

>[!info]
>Interrupts are signals that are sent to the processor to request immediate attention, and can be used to perform various types of processing or to handle exceptions or other events.

One-byte interrupt instructions, such as int 3 (0xcc) and int 1 (0xf1), can be useful for hooking because they can be easily placed in code to trigger an interrupt without consuming a lot of space. Int 3 is normally used to cause a SIGTRAP signal, which is used for debugging purposes, but it can be hooked by a kernel module to perform other actions. Int 1 is normally used for hardware debugging, but it can also be hooked by a kernel module.

A kernel module can also hook the Invalid Opcode Exception interrupt, which is triggered when the processor encounters an invalid instruction in the instruction stream. This can be used to implement custom instructions in software, or to retrofit security measures onto an existing system. 

It is worth noting that these approaches to hooking interrupts and implementing custom instructions are generally considered to be bespoke interaction methods, meaning that they are specific to a particular system or use case, and may not be widely supported or recommended

#### Modules and Files

The most common way of interacting with kernel modules is via file, using special file interfaces that are provided by the operating system. There are several common locations for these interfaces, including:

-   /dev: This directory contains mostly traditional devices, such as block devices (e.g., hard drives, CDs) and character devices (e.g., serial ports, audio devices). Kernel modules that implement device drivers will often register a file in this directory to allow userspace programs to access the device.
   
-   /proc: This directory started out in System V Unix as a way to provide information about running processes. In Linux, it has been expanded to include a wide range of kernel interfaces, including virtual files that provide information about system statistics, process details, and other types of data.

-   /sys: This directory was introduced as a more organized way to provide non-process information about the kernel and the system. It is intended to be a more structured and consistent interface for interacting with the kernel, and is used to expose a variety of system parameters and statistics.   

A kernel module can register a file in one of these locations to provide an interface for userspace programs to interact with the module. Userspace code can use the standard file API (e.g., open(), read(), write(), close()) to access and manipulate the file, which in turn allows the module to perform the desired actions or provide the requested information.

##### File read() and write()

One interaction mode for kernel modules is to handle the read() and write() system calls for a file that is exposed by the module. This allows userspace programs to access and manipulate the file using the standard file API.

From kernel space, the module can define functions such as "device_read()" and "device_write()" to handle these system calls when they are made on the exposed file. These functions typically have the following signature:
```c
static ssize_t device_read(struct file *filp, char *buffer, size_t length, loff_t *off) 
static ssize_t device_write(struct file *filp, const char *buf, size_t len, loff_t *off)
```


The "filp" parameter is a pointer to a "struct file" object that represents the open file, and the "buffer" and "buf" parameters are pointers to the memory locations where the data being read or written is stored. The "length" and "len" parameters indicate the number of bytes to be read or written, and the "offset" and "off" parameters are pointers to variables that track the current position within the file.

From user space, a program can use the read() and write() system calls to access and manipulate the file exposed by the module. For example:

```c
fd = open("/dev/arslan_mod", 0) read(fd, buffer, 128);
```

This code would open the file "/dev/pwn-college" in read-only mode, and then use the read() system call to read up to 128 bytes of data into the "buffer" memory location.

This interaction mode is useful for modules that deal with streams of data, such as audio or video data, as it allows the module to handle the data in a continuous stream rather than in discrete blocks. It is also useful for modules that need to support non-blocking I/O, as the read() and write() functions can return a partial count of the requested data if the operation cannot be completed immediately

#### Ioctl Handlers

Input/Output Control provides a much more flexible interface.

```c
/*From kernel space:*/
static long device_ioctl(struct file *filp, unsigned int ioctl_num, unsigned long ioctl_param)
```

```c
/*From user space:*/

int fd = open("/dev/pwn-college", 0);

ioctl(fd, COMMAND_CODE, &custom_data_structure);
```

Useful for setting and querying non-stream data (i.e., webcam  resolution settings as opposed to webcam video stream).

[Follow this link to learn how to compile the kernel modules](https://tldp.org/LDP/lkmpg/2.6/html/index.html)

