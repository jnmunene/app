---
title: "Buffer Overflow - The Basics"
date: 2023-03-08
---
### Introduction
![buffer-overflow](/images/buffer-overflow.png)
* In this blog we'll explore the basics of buffer overflow, then we'll gradually move to more advanced concepts of buffer overflow.
* Buckle up! Let's explore the fascinating world of buffer overflow.
### Buffer Overflow
* A buffer is a temporary storage area used by programs to hold data before it is processed. Buffers are essential to many types of programs, from web browsers to media players. 
* In essence, buffer overflow is a type of vulnerability where an attacker can inject more data into a buffer than it can handle, causing it to overflow and overwrite adjacent memory locations. The attacker can exploit this vulnerability to execute malicious code, bypass security measures, and gain unauthorized access to the system.

* There are various types of buffer overflow. In this blog, I'll be touching on stack and heap based buffer overflows.
### Common Real World Examples
* The Heartbleed bug in OpenSSL is a coomon example of stack based buffer overflow.
* Another example is the Wannacry ransomware attack, which exploited a vulnerability in the Windows SMB protocol.
* A common example of a heap-based buffer overflow is the CVE-2017-11882 vulnerability in Microsoft Office. 
### Stack Based Buffer Overflow
* This type of buffer overflow occurs when a program writes more data into a buffer on the stack than it can hold.
* The stack is a region of memory used to store local variables and function call data.
* Attackers can exploit this vulnerability by overwriting the return address stored on the stack with the address of their own code, which will then be executed when the function returns.
* Consider the following vulnerable C function that can be exploited via a stack-based buffer overflow:
{{<highlight C>}}
void vulnerable_function(char *input) {
    char buffer[16];
    strcpy(buffer, input);
}
{{</highlight>}}
* In this code, the function vulnerable_function() accepts a string input as an argument and copies it into a buffer buffer of size 16 using the strcpy() function.
* However, if the length of input is greater than 16, then strcpy() will copy the excess data into adjacent memory locations, potentially overwriting other important data on the stack, such as the return address.
#### Mitigation Technique
To mitigate this vulnerability, the function can be rewritten to perform bounds checking before copying the input string into the buffer. For example
{{<highlight C>}}
void safe_function(char *input) {
    char buffer[16];
    size_t input_len = strlen(input);
    if (input_len >= sizeof(buffer)) {
        printf("Input too long\n");
        return;
    }
    strcpy(buffer, input);
}

{{</highlight>}}
* In this code, the function `safe_function()` first checks the length of the input string and compares it to the size of the buffer. 
* If the input string is too long, the function prints an error message and returns without copying the input string into the buffer.


### Heap Based Buffer Overflow
* This type of buffer overflow occurs when a program writes more data into a buffer on the heap than it can hold.
* The heap is a region of memory used to dynamically allocate memory for data structures.
* Attackers can exploit this vulnerability by corrupting the heap metadata or by overwriting the data in adjacent memory locations.
* Here's an example of a vulnerable C function that can be exploited via a heap-based buffer overflow:
{{<highlight C>}}
void vulnerable_function(int size) {
    char *buffer = malloc(size);
    strcpy(buffer, "Hello, world!");
}

{{</highlight>}}
* In this code, the function vulnerable_function() allocates a buffer of size size on the heap using the malloc() function and copies the string "Hello, world!" into it using the strcpy() function.
*  However, if size is not large enough to hold the string, then strcpy() will copy the excess data into adjacent memory locations, potentially corrupting the heap metadata or overwriting other important data.
#### Mitigation Technique
To mitigate this vulnerability, the function can be rewritten to use a safer function for copying the input string, such as strncpy(), which takes a maximum number of bytes to copy as an argument. For example:
{{<highlight C>}}
void safe_function(int size) {
    char *buffer = malloc(size);
    strncpy(buffer, "Hello, world!", size);
    buffer[size - 1] = '\0'; // ensure null termination
}
{{</highlight>}}
In this code, the function safe_function() uses strncpy() to copy the string "Hello, world!" into the buffer, taking the maximum number of bytes to copy as an argument. It also ensures that the string is null-terminated by setting the last character of the buffer to '\0'.

### Detection Mechanisms
There are several mechanisms that can be used to detect buffer overflow vulnerabilities, including:

1. Static analysis tools:
* Involves examining the source code of a program to identify potential buffer overflow vulnerabilities.
* Tools such as the Clang Static Analyzer and Coverity can be used to automatically detect buffer overflow vulnerabilities during the development process.
2. Dynamic analysis tools: 
* Monitor the execution of a program to detect buffer overflow attempts at runtime.
* Tools such as AddressSanitizer and Valgrind can be used to detect memory errors, including buffer overflows, during runtime.

3. Stack canaries: a value placed on the stack that is checked before the function returns, to detect overwrites of the return address.
### Other Mitigation Techniques
* Use software-based mitigation techniques, such as Address Space Layout Randomization (ASLR) and Data Execution Prevention (DEP), to make it more difficult for attackers to exploit buffer overflow vulnerabilities.
* Use hardware-based mitigation techniques, such as Intel Processor Trace and Control-Flow Enforcement Technology (CET), to provide additional protection against buffer overflow vulnerabilities.
### Conclusion
* Buffer overflow is a common vulnerability used by hackers to gain unauthorized access to computer systems.
* Understanding the different types of buffer overflow vulnerabilities and the techniques used to exploit and mitigate them is crucial for software developers and cybersecurity professionals.
