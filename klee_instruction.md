
add the code snippet at the end of the re.c 
```C
/* test re_match function */
int main(int argc, char* argv[])
{
  /* test input - a regex-pattern and a text string to search in */
  char pat[7];
  char txt[3];

  /* make input symbolic, to search all paths through the code */
  /* i.e. the input is checked for all possible ten-char combinations */
  klee_make_symbolic(pat, sizeof(pat), "pat"); 
  klee_make_symbolic(txt, sizeof(txt), "txt"); 

  /* assume proper NULL termination */
  klee_assume(pat[sizeof(pat) - 1] == 0);
  klee_assume(txt[sizeof(txt) - 1] == 0);

  /* verify abscence of run-time errors - go! */
  int l;
  re_match(pat, txt, &l);

  return 0;
}

/* or test re_compile function*/
int main(int argc, char* argv[])
{
  /* test input - ten chars used as a regex-pattern input */
  char arr[10];

  /* make input symbolic, to search all paths through the code */
  /* i.e. the input is checked for all possible ten-char combinations */
  klee_make_symbolic(arr, sizeof(arr), "arr"); 

  /* assume proper NULL termination */
  klee_assume(arr[sizeof(arr) - 1] == 0);

  /* verify abscence of run-time errors - go! */
  re_compile(arr);

  return 0;
}
```

use the commend below to start klee
```
clang -emit-llvm -g -c -O0 -Xclang -disable-O0-optnone re.c
time klee --libc=uclibc --optimize re.bc
```

```
KLEE: WARNING: Module and host target triples do not match: 'x86_64-pc-linux-gnu' != 'x86_64-unknown-linux-gnu'
This may cause unexpected crashes or assertion violations.
KLEE: NOTE: Using klee-uclibc : /tmp/klee_build110stp_z3/runtime/lib/klee-uclibc.bca
KLEE: output directory is "/home/klee/klee-out-2"
KLEE: Using STP solver backend
KLEE: WARNING: undefined reference to function: __syscall_rt_sigaction
KLEE: WARNING: undefined reference to function: close
KLEE: WARNING: undefined reference to function: fcntl
KLEE: WARNING: undefined reference to function: fstat
KLEE: WARNING: undefined reference to function: ioctl
KLEE: WARNING: undefined reference to function: lseek64
KLEE: WARNING: undefined reference to function: mkdir
KLEE: WARNING: undefined reference to function: open
KLEE: WARNING: undefined reference to function: open64
KLEE: WARNING: undefined reference to function: read
KLEE: WARNING: undefined reference to function: sigprocmask
KLEE: WARNING: undefined reference to function: stat
KLEE: WARNING: undefined reference to function: write
KLEE: WARNING: undefined reference to function: kill (UNSAFE)!
KLEE: WARNING: executable has module level assembly (ignoring)
KLEE: WARNING ONCE: calling external: ioctl(0, 21505, 94031791578768) at libc/termios/tcgetattr.c:43 12
KLEE: WARNING ONCE: calling __user_main with extra arguments.
KLEE: WARNING ONCE: skipping fork (memory cap exceeded)
KLEE: WARNING: killing 10150 states (over memory cap: 2101MB)

KLEE: done: total instructions = 211227319
KLEE: done: completed paths = 591698
KLEE: done: partially completed paths = 10150
KLEE: done: generated tests = 601848
real    16m5.243s
user    11m41.009s
sys     4m24.232s

```
