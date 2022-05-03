# NDK基础__C语言复习

#### 数据类型

C 语言中数据类型分有符号和无符号，默认是有符号的。

| 类型              | 同义词                                                 | 存储空间     |
| --------------- | --------------------------------------------------- | -------- |
| signed char     | 无                                                   | 1 字节     |
| int             | signed int、signed                                   | 2 或 4 字节 |
| short           | signed short、short int、signed short int             | 2 字节     |
| long            | signed long、long int、signed long int                | 4 字节     |
| long long (C99) | signed long long、long long int、signed long long int | 8 字节     |

| 类型                       | 同义词                    | 存储空间     |
| ------------------------ | ---------------------- | -------- |
| unsigned char            | 无                      | 1 字节     |
| unsigned int             | unsigned               | 2 或 4 字节 |
| unsigned short           | unsigned short int     | 2 字节     |
| unsigned long            | unsigned long int      | 4 字节     |
| unsigned long long (C99) | unsigned long long int | 8 字节     |

> C 语言定义的 int 长度是不比 short 短，不比 long 长。具体长度取决于编译时指定的目标平台。

#### 宏

**宏** 是「名称 - 替换文本」的映射，预处理时会将源码中出现宏名称的地方展开为指定的替换文本；

```
宏定义：
#define ARRAY_SIZE 100

使用宏：
double data[ARRAY_SIZE]

```

**带参数的宏** 注意宏名称和左括号之间不能有空格，否则会变成无参数的宏。

```
#define DISTANCE(x, y) ((x)>=(y) ? (x)-(y) : (y)-(x))
int d = DISTANCE(1,2

```

#### 变量

| 变量类型   | 作用域   | 生命周期 | 内存区域 |
| ------ | ----- | ---- | ---- |
| 局部变量   | 函数内部  | 函数   | 栈区   |
| 全局变量   | 整个项目  | 进程   | 数据区  |
| 静态局部变量 | 函数内部  | 进程   | 数据区  |
| 静态全局变量 | 源文件内部 | 进程   | 数据区  |

#### 函数

| 函数类型 | 作用域   | 存储位置 |
| ---- | ----- | ---- |
| 全局函数 | 整个项目  | 代码区  |
| 静态函数 | 源文件内部 | 代码区  |

- **函数声明** 必须放在函数调用之前，函数声明可以省略形参声明，这依赖于 C语言没有函数重载（C++有函数重载）。**函数定义** 不一定要放在函数调用之前。形参可以不带参数名。
- **printf 占位符**

| 占位符   | 含义                  |
| ----- | ------------------- |
| %d、%i | 有符号整数               |
| %u    | 无符号整数，最高位当作值的一部分    |
| %o    | 有符号整数（八进制）          |
| %x    | 有符号整数（八进制）          |
| %X    | 有符号整数（十六进制），字母按大写显示 |
| %c    | 字符                  |
| %s    | 字符串                 |

- **inline 内联函数：** 函数体在编译时直接嵌入函数调用位置，运行时不会再执行函数调用指令，省去了函数调用的开销。内联是以代码膨胀为代价的，因为每一处内联函数的调用的位置都复制了一份代码，程序总代码量增大，程序会占用更大的内存空间。所以内联函数只适合简短的函数，另外内联函数不能直接递归调用。

```
inline void infun(); // 无效
void infun() {
}
--------------------------
void infun();
inline void infun() { // 这样子才有效
}
```

注意：inline 需要应用在 **函数定义** 才有效，应用在 **函数定义** 是无效的。

#### 关键词

**static 关键字的作用**

- **静态存储类型：** 用于修饰变量，使之成为静态局部变量或静态全局变量；
- **静态函数：** 用于修饰函数，使之成为静态函数。

**const 关键字的作用**

- **修饰变量 / 形参：** 变量值不能修改

```
const int a = 1;
a = 2; （X）// Cannot assign to variable 'a' with const-qualified type 'const int'

注意：const 和 类型 int 可以互换位置，以下两条语句是等价的。
const int a = 1;
int const a = 1;
```

- **修饰指针：** const 可以配合指针变量使用，记忆方法如下：
  - 1、const 离变量名近是修饰指针变量本身，指针指向不可变；
  - 2、const 离变量名远是修饰指针指向的变量，指针指向的变量不可变；

```
const int * p; // 指针指向的变量不可变
int const * p; // 指针指向的变量不可变
int * const p; // 指针指向不可变

const int * const p; // 都不可变
int const * const p; // 都不可变

int a = 1;
const int * p = &a;
*p = 2; （X）// Read-only variable is not assignable

```

- **修饰函数返回值：** 一般用于返回指针的函数，表示不允许修改返回指针指向的数据

```
const * int func(); // const 离变量名远，上面提到的 const int * p 的情况。

```

> **const 和 define 的对比**
> 
> const 和 define 有类似之处，它们都定义了一个不允许修改的值，如果只从功能上看，它们都是 “常量”。**但是从实现原理上看，它们并不是常量。**
> 
> - define 是预编译指令，define 定义的宏在预处理阶段就展开为指定的文本，define 定义的宏在编译后就不存在了。
> - const 是变量修饰符，本质上是一个 **只读变量**，一般所谓 “const 常量”，是从功能上对 const 的描述。

**extern 关键字的作用**

- **修饰变量或函数：** 表示该变量或函数是在外部文件中定义的全局变量或全局函数，提示编译器在外部文件中查找。

`main.c`

```
// 如果不使用 extern 修饰，会提示找不到变量。
extern int g ;
g = 1;

extern int g = 1; （X）// 这种写法不允许：'extern' variable cannot have an initializer

```

`other.c`

```
int g;

```

- **extern "C" ：** 实现 C++ 代码调用 C 语言代码，该代码将按照 C 语言的方式进行编译链接。

```
// 如果处于 C++ 环境，全部使用 C 的标准
#ifdef __cplusplus
extern "C" {
#endif

// all of your legacy C code here

#ifdef __cplusplus
}
#endif

```

**volatile 关键字的作用**

用于修饰变量，提醒编译器该变量随时可能发生改变，程序每次需要读取变量值的时候，都需要直接从变量地址中读取，而不应该使用寄存器中的缓存。

```
volatile int a;

```

**include 关键字的作用**

```
#include <系统资源>
#include "项目资源"

```

#### 数组

#### 字符串

- 字符串本质上是以`\0`为结束符的字符数组，C 风格字符串自动在数组末尾增加`\0`结束符。

```
char *p = "xurui";
p[2] = '1'; 不能修改全局区域
printf("%s", p);

```

- **遍历字符串**

```
char *str = "xurui";
while (*str) {
    ...
    str++
}

```

- **字符串转换**

**atoi：** 将字符串转换为长整型

```
函数原型：参数是指向字符串的指针，返回值是长整形
int atoi(const char *str)

调用函数：
#include <stdlib.h>
int result = atoi("1");

类似函数：atol、atof、strtod

```

> 怎么判断转换成功？

- **字符串比较**

**strcmp：** 比较两个字符串 **strncmp：** 比较两个字符串，最多比较前 n 个字符

```
函数原型：参数是两个字符串指针
int strcmp(const char *str1, const char *str2)
int strncmp(const char *str1, const char *str2, size_t n)

该函数返回值如下：
- 如果返回值小于 0，则表示 str1 小于 str2。
- 如果返回值大于 0，则表示 str1 大于 str2。
- 如果返回值等于 0，则表示 str1 等于 str2。

调用函数：
#include <string.h>
int result = strcmp("1", "2")

```

- **字符查找**

**strstr：** 查找第一次出现的位置

```
函数原型：
char *strstr(const char *haystack, const char *needle)

调用函数：
char *text = "peng xurui";
char *subtext = "x";

char *pop = strstr(text, subtext);
printf("%s\n", pop); // 输出 xurui

int index = pop - text;
printf("索引：%d", index); // 输出 5

```

- **字符串拼接**

**strcat：** 拼接两个字符串

```
函数原型：把 src 字符串追加到 dest 字符串的结尾
char *strcat(char *dest, const char *src)

```

- **大小写转换**

**tolower：** 转换为小写字符 **toupper** ：转换为大写字符

```
函数原型：把 c 字符转换为小写字符
int tolower(int c);

调用函数：
char c = 'A';
tolower(c);

```

```
函数原型：把 c 字符转换为大写字符
int toupper(int c);

调用函数：
char c = 'a';
tolower(c);

```

#### 指针

- & 取地址，* 取地址中的值，%p 地址的占位符。
- **指针存放的是内存地址，不过指针本身也有内存地址。**
- **指针的大小** 与指向的地址存储的变量类型无关，只与操作系统的字长有关。sizeof(int*) 在32 位系统中指针为 4 字节，64 位系统中指针为 8 字节。
- **野指针** 是指向不可用内存地址的指针，例如指针指向超出数组范围的地址。野指针不是空指针。
- **函数指针** 是指向函数的指针。函数的地址是函数存储空间的首地址。函数指针的定义方式为：**函数返回值类型 (*指针变量名) (函数参数列表);**

```
int Func(int x); 声明一个函数
int (*p) (int x); 定义一个函数指针
p = Func; 将 Func 函数的首地址赋给指针变量 p
(*p)(9); 调用函数
(p)(9); 可以省略 *，这里是函数指针特例

```

- **行指针**
- **列指针**

#### 堆内存管理

- **malloc：** 在堆上分配一块新的连续空间

```
函数原型：参数是分配的字节数
void *malloc(sizt_t size);

调用函数：
#include <stdlib.h>

int *p = (int *) malloc(sizeof int);
if (!p) {
    printf("申请失败");
}

```

- **calloc：** 在堆上分配一块新的连续空间，并初始化为`0`

```
函数原型：参数是元素个数 + 元素占用字节数
void* calloc(size_t num_elements,size_t element_size);

调用函数：
#include <stdlib.h>

int *p = (int *) calloc(5, sizeof(int));
if (!p) {
    printf("申请失败");
}

```

- **realloc：** 调整一块已经分配的内存空间（扩大或缩小）

```
函数原型：参数是原地址的指针 + 新的内存大小

void realloc(void *ptr, size_t new_size);

调用函数：
#include <stdlib.h>

int *p = (int *) malloc(20 * sizeof(int));
int *p1 = (int *) realloc(p, 21 * sizeof(int));
int *p2 = (int *) realloc(p, 2000 * sizeof(int));

printf("%x\n", p); // 输出 ce4054c0
printf("%x\n", p1); // 输出 ce4054c0
printf("%x\n", p2); // 输出 ce809800（新开辟了一块内存空间）

```

如果是将分配的内存扩大，则有以下情况：
1）如果当前内存段后面有足够内存空间，则直接扩展这段内存空间，realloc() 返回原指针；
2）否则，在堆中新开辟一块内存，并释放原来的内存块，返回新内存块地址；
2）否则，分配失败返回 NULL。

- **free：** 释放动态分配的内存块

```
函数原型：参数是内存块的指针
free(void* pointer);

调用函数：
#include <stdlib.h>

if (p) {
    free(*p);
    p = NULL; // 防止存现悬空指针
}

```

1）使用 malloc，calloc，realloc 分配的内存，必须通过 free 释放；
2）不能重复释放同一个内存块，因为释放后操作系统可能会把该空间分配给其他用途。

#### 结构体

- **声明结构体**

```
struct Student {
    char *name;
    int age;
    char sex;
}; // 必须加分号

```

- **初始化结构体**

```
// 方式 1：
struct Student mark;
mark.name = "mark";
mark.age = 10;
mark.sex = '1';

// 方式 2：
struct Student mark = {"mark", 10, '1'};

```

**注意：结构体中的成员变量初始化为系统值，而不像 Java 会给成员变量初始化为零值。**

- **声明时初始化**

```
struct Student {
    char name[10];
    int age;
    char sex;
} mark = {"mark", 10, '1'},
tom, amy;

```

- **结构体嵌套**

```
声明结构体：
struct Student {
    char name[10];
    int age;
    char sex;

    struct Sport {
        char *name;
    } sport;
};

使用结构体：
struct Student mark = {"mark", 10, '1'};
struct Student tom = {"tom", 1, '1'};

mark.sport.name = "basketball";
tom.sport.name = "football";

printf("%s\n", mark.sport.name); // basketball
printf("%s\n", tom.sport.name); // football

```

**注意：** 嵌套的 sport 结构体是独立的两个结构体，是独立的两块内存。

- **结构体指针**

结构体指针是指向结构体的指针。

```
struct Student *p = &mark;
printf("%s\n", p->name); // mark

```

其中，`->`是调用指针的一级成员变量。

- **结构体的静态开辟和动态开辟**

```
静态开辟：栈
struct Student mark;
mark.name = "mark";

动态开辟：堆
struct Student *markP = malloc(sizeof(struct Student));
markP->name = "mark";

printf("%s\n", markP->name); // mark

```

- **结构体数组**

结构体数组是元素为结构体的数组。

```
静态开辟：栈
struct Student student[3] = {
            {"mark", 10, '1'},
            {"tom",  1,  '1'},
            {},
};
struct Student *p = &student;

printf("%s\n", student[0].name); // mark
printf("%s\n", (p + 1)->name); // tom

动态开辟：堆
struct Student *p = malloc(sizeof(struct Student) * 3);
p->name = "mark";
(p + 1)->name = "tom";

printf("%s\n", p->name); // mark
printf("%s\n", (p + 1)->name); // tom

free(p);

```

- **结构体别名**

```
定义别名：
typedef struct Student Student_;
typedef struct Student * pStudent;

使用别名：
Student *p = malloc(sizeof(Student) * 3);
pStudent p = malloc(sizeof(Student) * 3);

```

这里需要注意 Clion 和 VS 两个编辑器的差异化：

```
Clion 要求使用 struct Student
struct Student *p = malloc(sizeof(struct Student) * 3);
free(p);

VS 可以省略 struct 关键字
Student *p = malloc(sizeof(Student) * 3);
free(p);

```

为了避免源码在不同编译器不统一，可以定义一个相同的别名：

```
定义一个相同的别名
typedef struct Student Student;

使用别名：
Student *p = malloc(sizeof(Student) * 3);

```

> **提示：** 你可以在源码中看到很多结构体都定义了别名，就是为了保持代码统一。C 语言枚举也有类似的问题。

- **匿名结构体**

```
struct { // 匿名结构体
    char *name;
}; // 不能引用，没有任何意义

struct { // 匿名结构体
    char *name;
} a1, a2, a3;

```

#### 文件操作

- **fopen** ：打开文件

```
函数原型：使用指定 mode 打开文件，返回 FILE 指针
FILE *fopen(const char *filename, const char *mode)

模式：
1）r：读取
2）w：写入
3）a：在文件后追加
4）r+
5）w+
6）a+

使用函数：
FILE *file = fopen("文件名", "r");
if (!file) {
    // 文件打开失败
}

char buffer[10];
while (fgets(buffer, 10, file) {
    printf("%s", buffer);
}

```

- **fputs：** 写入文件

```
函数原型：
int fputs(const char *str, FILE *stream)

使用函数：
FILE *file = fopen("文件名", "");
if (!file) {
    // 文件打开失败
}

fputs("xurui", file);
fclose(file);

```

- **EOF：** 文件的末尾（End of File）

---

#### 内存区域

- 从低地址到高地址，依次为：代码区、数据区、堆区和栈区。
- **代码区：** 可执行文件的二进制代码
- **数据区：**
  - **data 段：** 已经初始化的静态变量和全局变量
  - **bss 段：** 未初始化的静态变量和全局变量
  - **rodata 段：** 常量值（如字符串常量）
- **栈区：** 存储函数调用对应的栈帧
- **堆区：** 动态分配的内存

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/01abc26a1a2441f6849220a10d815488~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

---

链接：https://juejin.cn/post/6939477843088769032  
来源：稀土掘金

