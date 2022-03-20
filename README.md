# C6 -- 600 行的 C 語言編譯器

* 原作者 -- Robert Swierczek, https://github.com/rswier/
* 原專案 -- https://github.com/rswier/c4
* 修改者 -- 陳鍾誠

## Homework
* do while

```c
else if(tk == Do){
    next();
    a = e + 1;
    stmt();
    if(tk == While){
       next();
       if (tk == '(') next(); else { printf("%d: open paren expected\n", line); exit(-1); }
       expr(Assign);
        if (tk == ')') next(); else { printf("%d: close paren expected\n", line); exit(-1); }
        if(tk == ';')next();else {printf("%d: semicolon expected\n", line); exit(-1);}
        *++e = BNZ; *++e = (int)a;
    }else{	printf("%d: synatx error\n",line); exit(-1);}
  }

```
* for

```c
 else if(tk == For){
	int *c, *d;
	next();
 	if (tk == '(') next(); else { printf("%d: open paren expected\n", line); exit(-1); }
	expr(Assign);
	if(tk == ';')next();else {printf("%d: semicolon expected\n", line); exit(-1);}
	a = e+1;	
	expr(Assign);
	*++e = BZ; b = ++e;
	if(tk == ';')next();else {printf("%d: semicolon expected\n", line); exit(-1);}
	*++e = JMP; c = ++e;
	d = e+1;
	expr(Assign);
	*++e = JMP; *++e = (int)a;
	if (tk == ')') next(); else { printf("%d: close paren expected\n", line); exit(-1); }
	*c = (int)(e+1);
	stmt();
	*++e = JMP; *++e = (int)d;
    	*b = (int)(e+1);
  }
```
* test
```
#include<stdio.h>

int main(){
	int s;
	int i;
	i = 0;
	s = 1;
	do {
		s = i + s;
		i = i + 1;
		printf("do while: s=%d i=%d\n",s,i);
	} while(i < 10);
	printf("s=%d i=%d\n",s,i);
	
	for (i = 0; i < 10; i = i + 1){
		s = i + s;
		i = i + 1;
		printf("for: s=%d i=%d\n",s,i);
	}
	
	return 0;
}
```
### result
```
./c6 test/doWhileAndFor.c
do while: s=1 i=1
do while: s=2 i=2
do while: s=4 i=3
do while: s=7 i=4
do while: s=11 i=5
do while: s=16 i=6
do while: s=22 i=7
do while: s=29 i=8
do while: s=37 i=9
do while: s=46 i=10
s=46 i=10
for: s=46 i=1
for: s=48 i=3
for: s=52 i=5
for: s=58 i=7
for: s=66 i=9
exit(0) cycle = 583

```
## 使用方式

```
$ gcc -w c6.c -o c6 // 用 -w 可以避免警告，因為 c6 的原始碼太精簡使用了危險語法

$ ./c6 test/hello.c
hello, world
exit(0) cycle = 9

$ ./c6 test/sum.c
sum(10)=55
exit(0) cycle = 303

$ ./c6 test/fib.c
f(7)=13
exit(8) cycle = 920
```

## 自我編譯

```
$ ./c6 c6.c test/fib.c
f(7)=13
exit(8) cycle = 920
exit(8) cycle = 147464

$ ./c6 c6.c c6.c test/fib.c
f(7)=13
exit(8) cycle = 920
exit(8) cycle = 147464
exit(8) cycle = 24476550
```

## 印出組合語言 (堆疊機)

```
$ ./c6 -s test/hello.c
1: #include <stdio.h>
2:
3: int main()
4: {
5:   printf("hello, world\n");
    1:930098     ENT  0       
    3:9300A8     ADDR 0:9700A0
    5:9300B8     PSH
    6:9300C0     PRTF
    7:9300C8     ADJ  1
6:   return 0;
    9:9300D8     IMM  0
    B:9300E8     LEV
7: }
    C:9300F0     LEV
```

## 印出執行過程

```
$ ./c6 -d test/hello.c
    1:A30098     ENT  0       
    3:A300A8     ADDR 0:A700A0
    5:A300B8     PSH
    6:A300C0     PRTF
hello, world
    7:A300C8     ADJ  1
    9:A300D8     IMM  0
    B:A300E8     LEV
 1FFFC:B30070     PSH
 1FFFD:B30078     EXIT
exit(0) cycle = 9
```
