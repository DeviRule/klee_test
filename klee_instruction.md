
add the code snippet at the end of the re.c 
```C

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
```

use the commend below to start klee
```
clang -emit-llvm -g -c -O0 -Xclang -disable-O0-optnone re.c
time klee --libc=uclibc --optimize re.bc
```