# Address Variables — Question Bank

Trace every snippet **on paper**, using the rules from the worksheet:

- an address variable holds an **address** (`int *p; p = &a[2];` makes `p` hold the address of `a[2]`);
- `*(p)` on the **right** of `=` **reads** the box, on the **left** **writes** the box;
- `*(p + k)` is the box `k` slots away, and `a[i]` is the same as `*(a + i)`;
- assigning to the address variable itself (`p = p + 2;`, `p = &y;`, or `q = p;`) changes **where it points** — it does **not** change any data box.

Answers are **not** in this file.

---

# Part A — Final values

For each block, write the **final value of every array box** and **which slot `p` (or `q`) points to** at the end (write it as `&a[i]`).

**A1.**

```c
int a[4] = {10, 20, 30, 40};
int *p;
p = &a[1];
*(p) = 99;
*(p + 2) = 77;
```

**A2.**

```c
int a[5] = {1, 2, 3, 4, 5};
int *p;
p = &a[3];
*(p - 1) = *(p) + 10;
*(p - 2) = *(p - 3);
```

**A3.**

```c
char c[4] = {'a', 'b', 'c', 'd'};
char *q;
q = &c[0];
*(q + 1) = 'X';
*(q + 3) = *(q);
```

**A4.**

```c
int a[4] = {2, 4, 6, 8};
int *p;
p = &a[0];
*(p + 1) = *(p + 1) + *(p + 3);
*(p + 3) = *(p);
```

**A5.**

```c
int a[5] = {0, 0, 0, 0, 0};
int *p;
p = &a[2];
*(p) = 5;
*(p - 1) = *(p) * 2;
*(p + 1) = *(p - 1) + *(p);
```

**A6.**

```c
int a[4] = {1, 1, 1, 1};
int *p;
p = &a[0];
*(p) = 10;
p = p + 2;
*(p) = 20;
*(p - 1) = 30;
```

---

# Part B — Output and final state

For each block, write **the output line by line** (what each `printf` prints), and then the **final state** — every array box, and where the address variable points.

**B1.**

```c
int a[4] = {5, 6, 7, 8};
int *p;
p = &a[2];
printf("%i\n", *(p));
printf("%i\n", *(p - 1));
*(p) = *(p - 2) + *(p - 1);
printf("%i\n", *(p));
```

**B2.**

```c
int a[5] = {3, 1, 4, 1, 5};
int *p;
p = &a[0];
printf("%i\n", *(p + 2));
*(p + 2) = *(p) + *(p + 4);
printf("%i\n", *(p + 2));
printf("%i\n", a[2]);
```

**B3.**

```c
char c[5] = {'H', 'e', 'l', 'l', 'o'};
char *q;
q = &c[1];
printf("%c\n", *(q));
*(q) = *(q + 2);
printf("%c\n", *(q));
printf("%c\n", c[1]);
```

**B4.**

```c
int a[4] = {10, 20, 30, 40};
int *p;
p = &a[3];
printf("%i\n", *(p));
p = p - 2;
printf("%i\n", *(p));
*(p) = *(p + 1);
printf("%i\n", *(p));
```

**B5.**

```c
int a[4] = {1, 2, 3, 4};
int *p;
p = &a[0];
int total = *(p) + *(p + 1) + *(p + 2) + *(p + 3);
printf("%i\n", total);
*(p) = total;
printf("%i\n", a[0]);
```

---

# Part C — Plain variables (no stepping)

No arrays and no `+`/`-` on the address variable here — just address variables pointing at ordinary variables, and changing those variables' values **through** the address variable. For each block, write the **final contents of every variable**, and where each address variable points at the end (as `&name`).

**C1** to **C5** are the basics. **C6** onward use **four or more** plain variables, re-point the address variables between them in the middle of the block, and even copy one address variable into another (`q = p;`) — trace slowly, one line at a time. Remember: `p = &y;` and `q = p;` only change *where* an address variable points; `*(p) = ...` changes the variable it points to right now.

**C1.**

```c
int x = 5;
int *p;
p = &x;
*(p) = 20;
```

**C2.**

```c
int a = 7;
int b = 3;
int *p;
p = &a;
b = *(p);
*(p) = b + 1;
```

**C3.**

```c
int x = 1;
int y = 2;
int *p;
p = &x;
*(p) = 10;
p = &y;
*(p) = 20;
```

**C4.**

```c
int n = 4;
int *p;
int *q;
p = &n;
q = &n;
*(p) = 9;
*(q) = *(q) + 1;
```

**C5.**

```c
int a = 5;
int b = 8;
int *p;
int *q;
p = &a;
q = &b;
*(p) = *(q);
```

**C6.**

```c
int a = 1;
int b = 2;
int c = 3;
int d = 4;
int *p;
int *q;
p = &a;
q = &b;
*(p) = 10;
*(q) = *(p) + 5;
p = &c;
q = p;
*(q) = d;
*(p) = *(q) + 1;
```

**C7.**

```c
int w = 5;
int x = 6;
int y = 7;
int z = 8;
int *p;
int *q;
p = &w;
q = &z;
*(p) = *(q);
*(q) = *(q) + *(p);
p = &x;
*(p) = y;
q = p;
*(q) = *(q) * 2;
```

**C8.**

```c
int a = 0;
int b = 1;
int c = 2;
int d = 3;
int *p;
int *q;
p = &a;
q = &b;
*(p) = 5;
*(q) = 6;
*(p) = *(q) + 1;
p = &c;
*(p) = *(q);
q = &d;
*(q) = *(p) + a;
p = q;
*(p) = *(p) + 1;
```

**C9.**

```c
char e = 'a';
char f = 'b';
char g = 'c';
char h = 'd';
char *p;
char *q;
p = &e;
q = &g;
*(p) = 'X';
*(q) = *(p);
p = &f;
q = p;
*(q) = 'Y';
*(p) = h;
```

**C10.**

```c
int a = 2;
int b = 4;
int c = 6;
int d = 8;
int *p;
int *q;
p = &a;
q = &b;
*(p) = *(q) + *(p);
q = &c;
*(q) = *(p);
p = &d;
*(p) = *(q) + b;
q = p;
*(q) = *(q) - a;
*(p) = *(p) + c;
```

**C11.**

```c
int m = 1;
int n = 2;
int o = 3;
int s = 4;
int *p;
int *q;
p = &m;
q = &n;
*(p) = 100;
*(q) = *(p) - 90;
p = &o;
*(p) = *(q) + n;
q = &s;
*(q) = *(p);
p = q;
*(p) = *(p) + m;
*(q) = *(q) - o;
```

**C12.**

```c
int a = 10;
int b = 20;
int c = 30;
int d = 40;
int *p;
int *q;
p = &a;
q = &d;
*(p) = *(q);
*(q) = *(p) + b;
p = &b;
q = &c;
*(p) = *(q);
*(q) = *(p) + a;
p = q;
*(p) = *(p) - d;
q = &a;
*(q) = *(p) + *(q);
```

---

When you finish, check the answer key — check your *reasoning* (which variable each address variable points to at each moment, and whether `*(...)` was a read or a write), not only the final numbers.
