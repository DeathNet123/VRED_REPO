### Heap

In C and C++, the heap is a region of memory that can be dynamically allocated during the execution of a program. To request memory from the heap, programmers use functions such as malloc. The allocated memory, known as an "allocation," can be used, modified, or referenced by the programmer until it is no longer needed and is returned to the heap manager using the free function.

Example

```c
#include<stdio.h>
#include<stdlib.h>

int main(void)
{
	char *hello = (char * )malloc(8);
	free(hello);
	return 0;
}
```

Read further at : 
- https://azeria-labs.com/heap-exploitation-part-1-understanding-the-glibc-heap-implementation/
- https://azeria-labs.com/heap-exploitation-part-2-glibc-heap-free-bins/

These two links have  well defined details about heap and there are no useless information in them which makes it difficult to cut them and make short version out of them these blogs missed the detail implementation of the arenas so if i got chance to understand it then i'll add the Arenas sections here. 
Same thing but with different details =>  [Heap analysis with radare2 - Hackliza](https://hackliza.gal/en/posts/r2heap/)
This article contains how the meta data is changed during the chunks being free and allocated
	https://sourceware.org/glibc/wiki/MallocInternals

### Arenas

- [ ] Add the Details of Arenas



