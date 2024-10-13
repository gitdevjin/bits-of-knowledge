### Q. the cpu is 64bits processor but they have a special register or registers for vectorization?

Even though a CPU may be a 64-bit processor in terms of its general-purpose registers and architecture (which means it processes data in chunks of 64 bits for general instructions), it also includes special-purpose vector registers designed specifically for handling SIMD (Single Instruction, Multiple Data) operations.

In the case of AVX-512, these special registers (like ZMM0, ZMM1, etc.) are 512 bits wide, which allows the CPU to process much larger data chunks in parallel. These vector registers are part of the SIMD instruction set extensions, such as AVX (Advanced Vector Extensions) or SSE (Streaming SIMD Extensions).

So, while normal operations work on 64-bit chunks of data (like general-purpose calculations), when performing vectorized operations (such as in multimedia, scientific computing, or machine learning), the CPU can use these wider vector registers to handle multiple elements simultaneously, thereby increasing performance for these specific tasks.

In summary:

General-purpose operations: 64-bit registers for normal instructions.
SIMD/vectorized operations: Larger, special-purpose registers (like 512-bit ZMM registers in AVX-512) for parallel processing of multiple data elements at once.


### aarch64 assembly

x0: First argument
x1: Second argument
x2: Third argument
x3: Fourth argument
x4: Fifth argument
x5: Sixth argument
x6: Seventh argument
x7: Eighth argument (usually used for the syscall number)
svc 0 : call system call

### Hot to start
run the following to compile in the folder where there is a `hello.s` file

```bash
make
```

then, run the compiled file `hello`
```bash
./hello
```