# ACM常用C++stl总结


### 一、前言

- 本博客对ACM的常用的函数和c++STL做一个简单的总结
- 显然善用这些函数，可以对代码做大幅度地简化
- 本文主要对每个头文件/函数分**常用**和**不常用**做总结
- 注意：本博客只是针对编程竞赛中比较常用的用法，只是浅谈（因为用来打比赛够了），想要深入同学可以移步直接看c++官方提供的详细用法
- 用法：自己根据目录导航到自己想看的地方

### 二、一些非STL的常用函数

#### 1. cstdio头文件

##### 1.1 scanf函数（常用）

- 一个输入函数

- 基本形式为(以输入整数int型为例)，一般传入两个参数，**第一个是格式化字符串，第二个是变量引用**

  ```c++
  int a, b, c;
  scanf("%d", &a);//输入一个
  scanf("%d%d%d", &a, &b, &c);//输入多个
  ```

- 其中需要注意的是，逗号后面的必须传入的是需要输入的**变量的地址**，固一般在变量名前加一个&（取地址符）

- 由于需要传入的是地址，固我们在输入数组时也可以这样做

  ```c++
  int arr[10010];
  for (int i = 0; i < 10000; ++i) scanf("%d", &arr[i]);//常规输入
  for (int i = 0; i < 10000; ++i) scanf("%d", arr + i);//和上面的是同理的
  ```

- 当然，还有一个特别需要注意的就是输入**字符串**

  ```c++
  char str[110];
  scanf("%s", str);//下标从0开始的字符串
  scanf("%s", &str[1]);//下标从1开始的字符串
  ```

- 一些小技巧

  - 刷题的时候可能有些新手对下面类似这种输入很头痛

    > 3
    >
    > a 7
    >
    > b 8
    >
    > k 100

  - 常规的做法就是开一个char数组，将字符当初字符串输入

    ```c++
    char q[2];
    int t, n;
    scanf("%d", t);
    while (t--) {
        scanf("%s%d", q, &n);
    }
    ```

  - 这种做法，在某些情况下可能还需要一个getchar()函数进行辅助（例如吃掉换行符），但下面这种做法，可以发挥出强大的scanf的优势。做法就是，他怎么样，在输入是就当成自己输出时会怎么样

    ```c++
    char q;
    int t, n;
    scanf("%d\n", &t);//加个换行符
    while (t--) {
        scanf("%c %d\n", &q, &n); // 加个空格和回车符
    }
    ```

  - scanf的强大还远远不止这些，例如下面这个时间输入，做题时用到的只有数字，但题目偏偏有其他字符

    > 12:30:22

  - 和上面的同理，我们可以这样输入

    ```c++
    int h, m, s;
    scanf("%d:%d:%d", &h, &m, &s);
    ```

- scanf函数返回的是输入变量的个数，若是无输入，则返回-1，固我们在遇到不定组输入时，scanf可以这样

  ```c++
  int n;
  while (scanf("%d", &n) != -1) {
      //循环体
  }
  ```

- 其余细节见printf函数内容

##### 1.2 printf函数（常用）

- 与scanf相对，printf是c的一个输出函数
- 基本形式为(以整数int型为例)，**第一个参数是格式化字符串，第二个是需要输出的变量**

   ```c++
   printf("%d", n);//注意，没有取地址符号
   ```

- 关于printf的一般形式起始和scanf差不多，下面具体要扩展的是不同类型的格式化字符串（引号里的东东）和常见的输出格式

- 关于不同类型的格式化字符串（常用）

| 类型 | int  | float | double | char | char * | long long |
| ---- | ---- | ----- | ------ | ---- | ------ | --------- |
| 字符 | %d   | %f    | %lf    | %c   | %s     | %lld      |

- 关于不同类型的格式化字符串（不常用）

| 类型 | unsigned int | unsigned long long | long double | size_t | 无符型8进制 | 无符型16进制 |
| ---- | ------------ | ------------------ | ----------- | ------ | ----------- | ------------ |
| 字符 | %u           | %llu               | %Lf         | %zu    | %o          | %x或%X       |

- 一些需要注意的

  - 关于long long 好像对与不同的编译器使用的是不同的，但本人用%lld很少出问题
  - double 如果使用printf函数输出%lf和%f都是可以的，但是**scanf必须严格区分**
  - 注意long double 的%Lf是大写的
  - 对于不用的编译器size_t对应不同的类型
  - 如果要输出%则打%%就行了
  - 其实以上问题用c++中的cout和cin都可以解决

- 关于转义字符只需要知道换行符 **\n** 便可以了，需要注意的是如果要输入反斜杠或者引号只需要在符号前多加一个反斜杠便可，例如 **\\\\**

- 常用的输出格式

  - 输出精度，自动四舍五入（常用）

    ```c++
    double x = 3.141592653;
    printf("%.3f\n", x); //输出3.142
    printf("%.5f\n", x); //输出3.14159
    ```

  - 输出格式每个整数占w格，右对齐，多得补0或者空格（默认是空格）（不常用）

    ```c++
    int x = 123;
    printf("%5d\n", x);  //输出  123，默认补空格
    printf("%05d\n", x); //输出00123, 补0
    ```

##### 1.3 sscanf函数（不常用）

- sscanf函数虽然不怎么常用，但其功能也是十分黑科技，此函数在刷UVA的题时用得比较多，但依然这个函数不重要，知道以下用法便好

- 首先要知道的是，此函数的功能时将字符串中的数字读入至自己声明变量中去，使用方式和scanf十分类似，**第一个参数是需要读的字符串，第二个参数是格式化字符串，第三个参数是需要写的变量引用**

  ```c++
  char str[] = "x:200 y:100";
  int x, y;
  sscanf(str, "x:%d y:%d", &x, &y);
  printf("%d %d\n", x, y); // 输出200 100
  ```
  
##### 1.4 sprintf函数（不常用）

- 和sscanf同理，用法和printf十分相似，**第一个参数是需要写入的字符串，第二个是参数是格式化字符串，第三个参数是需要读的变量**，看下面的例子

  ```c++
  double PI = 3.1415926;
  char str[100];
  sprintf(str, "PI = %.3f", PI);
  printf("%s", str); // 输出 PI = 3.142
  ```

##### 1.5 gets函数（部分不常用）

- 此函数由于其安全性低被淘汰了（读取一行字符串，不管自己申请的char字符串空间长度够不够都会一直读，直至遇到\n换行符，还有就是其读取至换行符\n停止，但好像Linux和Windows的换行符是不一样的），但是这些工程上的东西关我们打比赛的什么事呢？

- 之所以说部分不常用是因为有些oj不给使用该函数了（例如洛谷），但还是有很多oj网站支持的，此函数读取字符串不仅速度快，而且能读一行，还是挺有用的

- 使用形式，**传入需要读入的字符串头指针**

  ```c++
  char str[100];
  gets(str); //读入字符串下标从0开始
  gets(&str[1]); //下标从1开始
  gets(str + 1); //下标从1开始
  ```

- 还有一件事，此函数若读取成功则返回读入的字符串，否则返回NULL（不定组输入有用）

##### 1.6 fgets函数（不常用）

- 据说此函数比较适合替代gets函数（个人觉得如果可以用gets用也是可以的），可能由于此函数需要的参数比较多，所以许多人不怎么喜欢在做题时使用他

- 常见形式，**第一个参数是需要读入的字符串，第二个参数是需要读入的字符串的长度，第三个参数若没有特殊（读入文件）一般写stdin**

  ```c++
  char str[110];
  fgets(str, 110, stdin);
  ```

- 此函数的返回值和gets一样

#### 2. cstring头文件

- 这个头文件中其实主要知道常用的函数便可以了，其余的想什么处理字符串拼接之类的，说实话如果不卡常，使用stl中的string巨香

##### 2.1 memset函数（常用）

- memset通常用来**初始化数组**，需要注意的是他是按字节赋值的（下面会解释），时间复杂度为$O(n)$

- 常见形式，**第一个参数是需要初始化的数组首地址，第二个参数是初始化字节值，第三个参数是需要初始化的字节大小**

  ```c++
  memset(arr, val, sizeof arr);
  ```

- 对于上面的代码，若arr的类型为int，则它将会对arr进行字节赋值，就拿此arr来说，每一个arr元素都是一个int类型的变量，即每个元素占4个字节，memset后其每个元素就会变成val val val val的二进制形式，换句话说，若val = 1，则每个元素的二进制形式就会被初始化成 00000001 00000001 00000001 00000001 ，其最终结果就是16843009而不是1

- 固推荐刚入手的同学可以只将val设置成0(0), -1(-1)对常见的int、long long赋值，其中括号内表示memset后每个元素的值，对于bool类型，val可以设置成0(false),非0即其他数(true)

- 对于memset的其他进阶操作

  - 首先要知道0x3f = 63，0x7f = 127，0x表示16进制

  - int类型（long long类型同理）

    ```c++
    memset(arr, 0x3f, sizeof arr);//每个元素赋值成0x3f3f3f3f，即1061109567，通常看成int的无穷大
    memset(arr, 0x7f, sizeof arr);//每个元素赋值成0x7f7f7f7f，即2139062143，通常也看成int的无穷大，但没有上一条常用，原因在于2139062143过大，再做加法时容易爆int
    ```

  - double类型（float类型同理）

    ```c++
    memset(arr, 0, sizeof arr);//每个元素都为0
    memset(arr, 0x3f, sizeof arr); //每个元素都为0.0047,由于没有用处，固没用
    memset(arr, 0x7f, sizeof arr); //浮点数的正无穷大，这个有用
    ```

- 那么怎么对一维数组指定个数进行memset呢，如下

  ```c++
  memset(arr, val, sizeof(type) * num);//type为数组的类型，num为初始化的个数
  ```

##### 2.2 strlen函数（常用）

- 此函数是计算char型字符串的长度，其原理是从指定的头开始遍历char字符串，知道碰到'\0'字符停止，固其时间复杂度为$O(n)$，所以要把他放到for循环体的判断函数内，不然你怎么tle的都不知道

- 常见形式(第三行)，**传入char*字符串的头指针**

  ```c++
  char str[100];
  scanf("%s", str);
  int len = strlen(str); // 传入需要知道长度的字符串的头指针
  ```

##### 2.3 memcpy（不常用）

- 此函数用于复制数组a给数组b，即b = a

- 常见形式，**第一个参数是目标数组，第二个参数是被复制的数组，第三个参数形式和memset相同，即从数组a中拷贝（sizeof a)个字节到数组b**

  ```c++
  int a[100], b[100];
  // 相当于b = a
  memcpy(b, a, sizeof a); 
  ```

##### 2.4 strcmp（不常用）

- 此函数用于对比两个字符串的**字典序**大小，具体看代码

- 常见形式，**传入两个需要比较的字符串头指针**

  ```c++
  char a[10] = "abc", b[10] = "abd";
  int k = strcmp(a, b); // 传入需要比较的两个字符串的头指针
  // 若 a > b，则 k > 0
  // 若a == b，则 k = 0
  // 若 b < a，则 k < 0
  ```

##### 2.5 strcpy（不常用）

- 此函数用于拷贝字符串的，功能和memcpy差不多

- 常见形式，**第一个参数是被写入的字符串头指针，第二个参数是读的字符串头指针**

  ```c++
  char a[10] = "aaaaaa";
  char b[10] = "bbbbbb";
  strcpy(b, a); // 传入两个需要操作的字符串头指针，相当于b = a
  ```

##### 2.6 strcat（不常用）

- 此函数用于拼接两个字符串

- 常见形式，**第一个参数是需要写入的字符串头指针，第二个是读的字符串头指针**

  ```c++
  char a[10] = "aaa";
  char b[10] = "bbb";
  strcat(b, a); //传入需要操作的两个字符串的头指针， 相当于b += a
  puts(b); //输出 bbbaaa
  strcat(b, &a[2]); //相当于b += &a[2]
  puts(b); //输出 bbbaaaa
  ```

##### 2.7 strchr（不常用）

- 此函数用于查找字符串中某字符第一次出现的位置，返回查找到的字符串指针，若找不到返回NULL

- 常见形式，**第一个参数是字符串头指针，第二个参数是需要查找的字符**

  ```c++
  char a[10] = "abcdegf";
  char *b = strchr(a, 'c'); // 第一个传入的参数是需要查找的字符串指针（开始指针便可以的）
  puts(b); //输出 cdegf
  ```

#### 3. cmath头文件

##### 3.1 sqrt（常用）

- 计算正实数的算术平方根

- **double sqrt(double x)**

- **传参浮点数，返回值也是浮点数**

- 常见形式

  ```c++
  double x = 2;// 
  double y = sqrt(x);
  cout << y << endl; // 输出1.41421
  ```

##### 3.2 pow（常用）

- **double pow(double a, double b)**

- 返回$a^b$的浮点数

- 常见形式

  ```c++
  double a = 2, b = 0.5;
  double res = pow(a, b);
  cout << res << endl;// 输出1.41421
  ```

#####  3.3 acos（不常用）

- **double acos(double a)**

- 返回$arccos(a)$的浮点数值

- 当然类似的函数还有asin, atan, sin, cos, tan……

- 常见用法，获取$π$

  ```c++
  const double PI = acos(-1);
  cout << PI << endl; // 输出3.14159
  ```

##### 3.4 ceil、floor、round（不常用）

- **double ceil(double x)**  返回x的向上取整的值

- **double floor(double x)** 返回x的向下取整的值

- **double round(double x)** 返回x四舍五入的值

- 例如

  ```cpp
  double x = 1.65;
  cout << ceil(x) << endl;  // 2
  cout << floor(x) << endl; // 1
  cout << round(x) << endl; // 2
  ```

##### 3.5 log、log2、log10（不常用）

- **double log(double x)** 返回$log_e{x}$的值

- **double log2(double x)** 返回$log_2{x}$的值

- **double log10(double x)** 返回$log_{10}{x}$的值

- 例如

  ```cpp
  cout << log(10) << endl;  // 输出2.30259
  cout << log2(4) << endl;  // 输出2
  cout << log10(1000) << endl;//输出3
  ```

#### 4. 其它

##### 4.1 abs（常用）与fabs（不常用）

- 由于本人不知道这两个函数到底是来自哪个头文件只能放到其他类型了

- 两个函数都是对一个数取绝对值

- 经过自己的测试，两个函数的区别不大，貌似两个都支持整数和浮点数，但听说后者（fabs）是专门对付double的

- **传入需要取绝对值的参数**

- 下面用abs做为例子，fabs同理

  ```cpp
  int x = -5;
  cout < abs(x) << endl; //输出5
  ```

##### 4.2 isdigit（不常用）

- 头文件`#include <cctype>` 

- **int isdigit(int x)** 

- 检查传入的ASCII码对应的字符是否是十进制数字字符

- **传入一个ASCII**

- 常见形式

  ```cpp
  char a = '9', b = 'a';
  if (isdigit(a)) cout << "YES" << endl;
  else cout << "NO" << endl;
  // 输出YES
  if (isdigit(c)) cout << "YES" << endl;
  else cout << "NO" << endl;
  //输出NO
  ```

- 与之类似的还有isalnum、islower、isupper，分别检查是否是字母数字、是否是小写字母、是否是大写字母

##### 4.3 atoi（不常用）

- 传入`char*`字符串，将其转换成int类型，注意他的机制是从做到右扫描，直到字符串结尾或者遇到非数字字符
- 头文件`#include <cstdlib>`
- 与其类似的还有**atol, atoll, atof**等,返回值分别为**long， long long, float**

```cpp
char s[] = "123";
printf("%d\n", atoi(s)); // 123
```

##### 4.4 tolower、toupper（不常用）

- 头文件 **#include\<cctype\>**

-  **int tolower(int c)**
-  **int toupper(int c)**

- 两者都是将字母字符转化为大小写，前者是小写，后者是大写，注意：tolower当遇到小写字母时，则不变，toupper遇到大写不变

- 常见形式，以tolower为例

  ```cpp
  char a[7] = "AbcDEF";
  for (int i = 0; i < 6; ++i) {
      a[i] = tolower(a[i]);
  }
  cout << a << endl; // 输出 abcdef
  ```

##### 4.5 assert(不常用)

- 头文件 **#include \<cassert\>**

- 一个断言语句，貌似不是函数，而是一个宏，用来**调试的利器**

- 传入一个判断语句，若正确，程序照常运行，若错误程序停止运行

- 一般在找bug或者提交时，可以在某些地方加上此函数，这样若是在函数此行报错，则说明我们判断的是错误的

- 常见形式

  ```cpp
  int n;
  scanf("%d", &n);
  assert(n >= 0);
  // 若上一行正确则将会继续运行，否则程序停止
  ```

### 三、STL

#### 1. 一些需要提前知道的知识

##### 1.1 迭代器

- 迭代器是用来访问STL中的容器的工具，初学者把它理解成指针也可以
- 迭代器一般是c++11前用得比较多，但c++11更新了auto的用法后，迭代器逐渐没用了起来
- 这里提前利用后面要说到的set容器来演示迭代器的基础使用

```cpp
#include <cstdio>
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> a = {4, 2, 3, 1, 5};
    for (set<int>::iterator it = a.begin(); it != a.end(); ++it) {
        cout << *it << ' ';
    }
    return 0;
}
/*
输出：
1 2 3 4 5
*/
```

##### 1.2 auto

- 神器
- 利用它可以大大简化你遍历STL容器的代码量
- 他的机制是由编译器自行判断用户定义的变量类型，由于过于香，本文后续访问、遍历、赋值会大量使用到auto，而且不难理解
- 基础用法，可以将上述迭代器的代码改进一下

```cpp
#include <cstdio>
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> a = {4, 2, 3, 1, 5};
    for (auto it = a.begin(); it != a.end(); ++it) {
        cout << *it << ' ';
    }
    return 0;
}
/*
输出：
1 2 3 4 5
*/
```

- 当然对于c++11，对于容器的遍历还提供了一种更加简便的方式（前提是该容器封装了begin和end）
- 还是上面的set

```cpp
#include <cstdio>
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> a = {4, 2, 3, 1, 5};
    for (auto x : a) {
        cout << x << ' ';
    }
}
/*
输出：
1 2 3 4 5
*/
```

##### 1.3 Lambda函数

- 前言：Lambda即匿名函数，对于匿名函数可能玩python比较熟悉，当然c++也有自己的一套Lambda玩法

- 一般形式：

  ```c++
  [] () -> return_type {}
  ```

- 常见用处：最常见是用在sort的函数内（sort用法详见下文algorithm），对sort进行制定如何排序

  ```c++
  int arr[5] = {3, 4, 2, 1, 7};
  sort(arr, arr + 5, [] (int a, int b) {
      return a > b; //从大到小排序
  });
  ```
  
- 下面介绍lambda的基础（其实会上面的就够了）

- 直接看先代码

```cpp
#include <cstdio>
using namespace std;

int main() {
    int a = 0, b = 1, c = 2, d = 3, e = 4;
    
    auto func = [&, a, c](int x, int &y)->int {
        b = 5, x = 6, y = 7;
        return a + b + c + x + y;
    };

    puts("函数运行前：");
    printf("a = %d\nb = %d\nc = %d\nd = %d\ne = %d\n", a, b, c, d, e);
    printf("func(d, e) = %d\n", func(d, e));
    puts("运行前：");
    printf("a = %d\nb = %d\nc = %d\nd = %d\ne = %d\n", a, b, c, d, e);
    return 0;
}
/*
输出：

函数运行前：
a = 0
b = 1
c = 2
d = 3
e = 4
func(d, e) = 20
运行后：
a = 0
b = 5
c = 2
d = 3
e = 7
*/
```

- 下面解释每个符号的含义
  - **[]** 是捕捉列表，用于捕捉父级的变量
    1. [var]表示值传递方式捕捉变量var；
    2. [=]表示值传递方式捕捉所有父作用域的变量（包括this）
    3. [&var]表示引用传递捕捉变量var
    4. [&]表示引用传递方式捕捉所有父作用域的变量（包括this）
    5. [this]表示值传递方式捕捉当前的this指针
    6. [=,&a,&b]表示以引用传递的方式捕捉变量a和b，以值传递方式捕捉其它所有变量
    7. [&,a,this]表示以值传递的方式捕捉变量a和this，引用传递方式捕捉其它所有变量
  - **()** 是传参列表，传入的参数规则和普通函数一样，值得注意的是如果什么都不传则可以省略这个传参列表
  - **->return-type** 是函数的返回类型，如果无返回或让编译器自己判断则可以省略
  - **{}** 是函数体，没什么好说的

#### 2. vector

- 最常用的STl容器之一，对于新手你可以把他理解成一个数组，他能使用下标访问，能在尾部$O(1)$时间插入元素，固他是动态开辟空间的
- 头文件为`#include <vector>`

##### 2.1 基础声明方式

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 直接声明元素类型为int的vector
    vector<int> v1;

    // 利用构造函数声明一个长度为5的vector，
    vector<int> v2(5);

    // 利用构造函数声明一个长度为5，所有值为1的vector
    vector<int> v3(5, 1);

    // 另一种声明方式
    vector<int> v4 = {4, 5, 6, 7};
    
    // 也可以这样
    int b[4] = {1, 2, 3, 4};
    vector<int> v5(b + 1, b + 4); // v5元素为 2， 3， 4
    return 0;
}
```

##### 2.2 访问方式/遍历方式

- 最常用的访问方式就是下标访问

```cp
int main() {
    vector<int> v = {4, 5, 6, 7};
    cout << v[2] << endl; // 输出6
    return 0;
}
```

- 遍历方式一般可以直接for

```cpp
int main() {
    vector<int> v = {4, 5, 6, 7};
    for(int i = 0; i < 4; ++i) {
        cout << v[i] << ' ';
    }
    // 输出为 4 5 6 7
    return 0;
}
```

- 当然也可以使用迭代器，这里推荐使用c++11提供的遍历方式，缺点就是没办法只得当前元素是第几个元素，但是方便

```cpp
int main() {
    vector<int> v = {4, 5, 6, 7};
    for (int x : v) { // 这里将 int 换成 auto是一样的，只是auto可以自动识别变量类型罢了
        cout << x << ' ';
    }
    // 输出为 4 5 6 7
    return 0;
}
```

##### 2.3 push_back和pop_back函数（常用）

- 分别用于在vector的尾部插入元素和删除与元素的函数，基本用法如下

```cpp
int main() {
    vector<int> v = {4, 5, 6, 7};

    v.push_back(3); // 往尾部插入3
    v.push_back(2); // 往尾部插入2

    for (int x : v) cout << x << ' '; // 输出：4 5 6 7 3 2
    cout << endl;

    v.pop_back(); // 删除最后一个元素
    
    for (int x : v) cout << x << ' '; // 输出 4 5 6 7 3
    return 0;
}
```

##### 2.4 size函数（常用）

- 返回当前vector的大小（长度）

```cpp
int main() {
    vector<int> v = {4, 5, 6, 7};

    cout << v.size() << endl; // 输出4

    v.push_back(3); 

    cout << v.size(); // 输出5

    return 0;
}
```

##### 2.5 clear函数（常用）

- 将vector清空，时间为$O(1)$

```cpp
int main() {
    vector<int> v = {4, 5, 6, 7};

    cout << v.size() << endl; // 输出4

    v.clear(); // 清空

    cout << v.size(); // 输出0

    return 0;
}
```

##### 2.6 empty函数（不常用）

- 写法为`v.empty()`，若该vector为空则返回true，否则返回false

##### 2.7 front和back函数（不常用）

- 分别返回vector头部和尾部元素的值
- 很多人不知道的是，其实他返回的是引用类型，这就意味着我们是可以通过该函数修改值的

```cpp
vector<int> v = {4, 5, 6, 7};

cout << v.front() << ' ' << v.back() << endl; // 输出为4 7

v.front() = 1;  // 将头部的元素修改为1
v.back() = 2;   // 将尾部的元素修改为2

cout << v.front() << ' ' << v.back() << endl; // 输出为1 2
```

##### 2.8 erase函数（不常用）

- 删除vector内部的元素

- 传入需要删除的起始指针和结束指针的下一个位置
- 时间复杂度$O(n)$，因为他的原理就是删除后将后面的元素往前移动

```cpp
vector<int> v = {0, 1, 2, 3, 4, 5, 6};

v.erase(v.begin() + 1, v.begin() + 4); // 删除[1, 4)范围的下标，主要左闭右开即[1, 3]

for (int x : v) cout << x << ' '; // 输出为：0 4 5 6
```

##### 2.9 insert函数（不常用）

- 插入元素，传参方式一般有三种
- 时间复杂度和erase同理，$O(n)$

```cpp
vector<int> v = {0, 1, 2, 3, 4};
int b[4] = {9, 8, 7, 6};

v.insert(v.begin() + 1, 5); // 往第2个元素后面后插入一个5
// 此时v为 0, 5, 1, 2, 3, 4

v.insert(v.begin() + 2, 2, 6); // 往第3个元素后面插入两个6
// 此时v为 0, 5, 6, 6, 1, 2, 3, 4

v.insert(v.begin() + 3, b + 1, b + 3); // 往第4个元素后面插入数组b中下标从1~2的数
// 此时v为 0, 5, 6, 6, 8, 7, 1, 2, 3, 4
```

##### 2.10 resize函数（不常用）

- 重新设置vector，详见代码
- 时间复杂度$O(n)$

```cpp
vector<int> v = {0, 1, 2, 3, 4};

v.resize(10); // 重置v的大小为10，其值是什么自己探索

v.resize(5, 1); // 重置v大小为5，全部值为1的
```

##### 2.11 swap函数（不常用）

- 交换两个vector

```cpp
vector<int> a = {0, 1, 2, 3, 4};
vector<int> b = {5, 6, 7};

a.swap(b); // 交换 a 和 b
```

##### 2.12 各种比较运算符（不常用）

- vector内部重写了`< <=  > >= == !=`六个比较运算符
- 用来比较两个vector的关系
- 其判断机制有点类似于两个字符串大小的判断，但对于每个元素的比较是真正意义上的值的比较（是什么类型元素就怎么比较）

##### 2.13 begin函数和end函数（不常用）

- begin返回vector起始的迭代器，end是vector最后一个元素的下一个位置的迭代器
- 注意：这两个函数虽然不常用，但在**排序的时候非常常用**

```cpp
vector<int> v = {4, 5, 6, 7};
cout << *v.begin() << endl; // 输出4
cout << *(v.end() - 1) << endl; // 输出 7
```

#### 3. string

- 最常用的stl容器之一，新手可以这么理解：从形式上看是一个字符串，从功能上看可以把它当做`vector<char>`，当然他比vector多了`+`和`+=`的操作，用于合并string，同时支持用`cin`输入`cout`输出
- 头文件：`#include <stirng>`
- 需要值得记住的是，string内子串的切割一般第一个参数是下标其实位置，第二个参数是长度，详情参考substr、replace等函数

##### 3.1 基础声明方式

```cpp
// 方式一
string s1;

// 方式二
string s2 = "123";

// 方式三，初始化为由3个c组成的字符串
string s3(3, 'c');

// 方式四 相当于方式二
string s4("123");

// 方式五，和方式四是等价的
char chr[] = "abc";
string s5(chr);
```

##### 3.2 访问遍历方式

- 和vector几乎一样

```cpp
string s = "abcdef";

cout << s[3] << endl; // 输出：d

// 遍历
for (int i = 0; i < 6; ++i) cout << s[i] << ' ';
cout << endl;

// c++11方式
for (char c : s) cout << c << ' ';
```

##### 3.3 和vector几乎用法一样的函数

- **push_back**和**pop_back**函数（常用）
- **size**函数，注意string还另外封装了**length**函数，其效果和用法和size一样
- **clear**函数（常用）
- **empty**函数（常用）
- **begin**和**end**函数
- 各种比较运算符，需要注意的是string还多了`+`和`+=`的操作（常用）
- **swap**函数（不常用）
- **resize**函数（不常用）
- **front**和**back**函数（不常用）

##### 3.4 关于string需要知道的一个常量string::npos

- `stirng::npos`是一个常量，由于stl容器中下标和容器大小的返回值一般都是`size_t`（一个无符型整数），固在string的一些函数返回值为了表示无或者是不存在等含义，则返回此常量`string::npos`，其值可以理解为-1，但由于它是无符型，固真正意义表示的是无穷大

##### 3.5 find函数（常用）

- 用于从前往后查找子串或者字符串第一次出现的下标位置，若找到则返回`stirng::npos`，与其相对的从后往前找的是**rfind**函数，但个人觉得除了方便几乎没什么用，大不了自己写个KMP
- 时间复杂度$O(n)$

```cpp
string s = "abcdef";

// 从0开始找第一个出现的字符'c'
cout << s.find('c') << endl; // 输出为 2

// 从下标3开始找第一个字符'c'
// 此时，由于查找不到，如返回的是stirng::npos
// 这个输出值是size_t的最大值，对不同系统输出不一
cout << s.find('c', 3) << endl; 

// 但在判断是否找到可以用这种方式
if (s.find('3', 3) == string::npos) {
    cout << "not find!!!" << endl;
}

// 从下标0开始找字符串"bc"
cout << s.find("bc") << endl; // 输出为 1

// 从下标1开始找
cout << s.find("ef", 1) << endl; // 输出为 4
```

##### 3.6 substr函数（常用）

- 用于截取string的一个子串，返回值为string
- 时间复杂度$O(n)$，n为子串的长度
- 用法详情看代码片段，

```cpp
string s = "abcdef";

// 截取 s 串中下标从2开始以后的子串
cout << s.substr(2) << endl; // cdef

// 截取 s 串中下标从2开始长度为2的子串
cout << s.substr(2, 2) << endl; // cd

// 截取 s 串中下标从3开始长度为无穷的子串，下面两句话是等价的
cout << s.substr(3, -1) << endl; // def
cout << s.substr(3, string::npos) << endl; // def
```

##### 3.7 replace函数（常用）

- 用于将字符串中的指定范围的子串来替换指定的子串，并返回替换后的结果
- 时间复杂度$O(1)$

```cpp
string s = "abcdef";

// 将s串中的下标从3开始长度为2的子串替换成"123456"
s.replace(3, 2, "123456");
cout << s << endl; // abc123456f

s = "abcdef";
// 将 s 串中从下标3开始长度为2的子串替换成 "123456" 的下标从2开始长度为3的子串
s.replace(3, 2, "123456", 2, 3); // abc345f
cout << s << endl;

s = "abcdef";
// 将 s 串中下标从3开始长度为1的子串替换成 5个 ’#‘
s.replace(3, 1, 5, '#');
cout << s << endl; // abc#####ef
```

##### 3.8 append函数（不常用）

- 一个用来在string串尾部添加字符串的函数，并返回最终的字符串
- 个人感觉有`+=`这个函数就没必要了，加上两者复杂度一样

```cpp
string s = "abcdef";

// 在 s 串尾部插入 "123"
s.append("123");
cout << s << endl; // abcdef123

string x = "123456";
s = "abcdef";

// 此方法和上一种写法是等价的
s.append(x);
cout << s << endl; // abcdef123456

// 在上一种方法的基础上加个范围， 即 取 x 串下标从3开始长度为2的子串添加到 s 尾部
s = "abcdef";
s.append(x, 3, 2);
cout << s << endl; // abcdef45
```

##### 3.9 compare函数（不常用）

- 用于比较两个字符串的大小关系，其返回值和strcmp差不多，大于0则说明前者大于后者，以此类推
- 个人感觉在敲代码时有封装好的比较运算符就够了
- 他的传参和replace函数的一种传参很像

```cpp
string a = "abcdef";
string b = "abdddd";
// 直接对比
if (a.compare(b) < 0) {
    cout << "a < b" << endl; // a < b
} else {
    cout << "a >= b" << endl; 
}

// 添加范围，注意传参意义第一数字是开始下标，第二个是长度
if (a.compare(2, 1, b, 3, 2) < 0) {
    cout << "\"c\" < \"dd\"" << endl; // "c" < "dd"
} else {
    cout << "\"c\" >= \"dd\"" << endl; 
}
```

##### 3.10 insert函数（不常用）

- 如果说**append**函数只能在尾部插入，则此函数能在指定位置插入，并返回结果string的值
- 复杂度$O(n)$

```cpp
string a = "abcdef";
string b = "123456";

// 从 a 串中下标2开始后面插入b串
a.insert(2, b);
cout << a << endl; // ab123456cdef

a = "abcdef";
// 从 a 串中下标2开始后面插入b串中下标从3开始长度为2的子串
a.insert(2, b, 3, 2);
cout << a << endl; // ab45cdef
```

##### 3.11 erase函数（不常用）

- 删除指定范围的子串，并返回结果string值

```cpp
string a = "abcdef";
string b = "123456";

// 删除 a 串中下标从3开始以后
a.erase(3);
cout << a << endl; // abc

// 删除 b 串下标从4开始长度为1的子串
b.erase(4, 1);
cout << b << endl; //12346
```

##### 3.12 c_str函数（不常用）

- 其作用就是将字符串转换成`char*`类型的字符串

##### 3.13 关于string的其他骚操作

###### 3.13.1 to_string函数（常用）

- 用于将整数类型转换成string类型
- 可传入的参数有int、long long、double等
- 注意此函数为c++11提供的函数

```cpp
int a = 1;
long long b = 2;
double c = 3.4;

string x = to_string(a);
string y = to_string(b);
string z = to_string(c); // 一般不用，比较精度不好控制
```

###### 3.13.2 stoi函数（不常用）

- 有点类似**atoi**函数的功能，但此函数传入的是string参数，即将string 转换成int类型
- 返回类型为int类型
- 与其类似的还有**stol、stof、stod、stoll、stold、stoull**等，分别对应返回值为**long、double、long long、long double、unsigned long long**

```cpp
string s = "123456";
cout << stoi(s) << endl; // 123456
```

###### 3.13.3 整行读入string

- 一般我们整行读入string用的都是以下方式，值得注意的是他和cin的返回值一样，固可以处理不定组输入写进while判断内

```cpp
string s;
// 值得注意的是，如果此前有换行符需要用getchar吃掉
while (getline(cin, s)) {
    cout << s << endl; // 实现一行一行地读入
}
```

###### 3.13.4 sstream的使用

- 用于对string的流控制，缺点是速度慢，好处就是大模拟时可以帮你省很多事
- 具体功能直接看代码

```cpp
#include <string>
#include <iostream>
#include <sstream> // 需要导入此头文件
using namespace std;

int main() {
    string str = "what are you doing now", sub;
    stringstream ss1(str); // 将流初始化
    while (ss1 >> sub) { // 想流一样读入到sub里面
        cout << sub << endl;
    }
    /*
    输出结果
    what
    are
    you
    doing
    now
    */

    str = "123 45.6";
    stringstream ss2;
    ss2 << str; // 像流一样读入到ss2
    int x;
    double y;
    ss2 >> x >> y; // 像流一样读入到 x 和 y 里面
    cout << x << ' ' << y; // 123 45.6
    
    return 0;
}
```

#### 4. stack

- STL中封装好的栈，也挺常用的
- 头文件`#include <stack>`
- 初始化的话直接声明`stack<int> st`便可
- 遍历方式：由于他的意义是栈，固只能访问栈顶元素

##### 4.1 size函数（常用）

- 返回栈的大小，用法和vector里的size函数一样

##### 4.2 push和pop函数（常用）

- push用于将元素压入栈中，pop则是弹出栈顶元素，注意返回值都是`void`

```cpp
stack<int> st;
st.push(1);     // 将1压入栈中
st.push(2);     // 将2压入栈中
st.pop();   // 弹出栈顶元素
```

##### 4.3 top函数（常用）

- 返回栈顶元素的**引用**变量

```cpp
stack<int> st;
st.push(1);     // 将1压入栈中
st.push(2);     // 将2压入栈中
cout << st.top() << endl; // 2
st.top() = 3;
cout << st.top() << endl; // 3
```

##### 4.4 empty函数（常用）

- 判断栈是否为空，为空返回`true`，否则返回`false`

##### 4.5 关于stack需要注意的事

- stack中没有封装**clear**函数，固需要清空时可以考虑用pop函数暴力清空（一般都是声明为局部变量，用完就不要的）
- stack在**pop**时如果没有把握，需要提前先判断栈是否为空，否则如果栈为空，则会报错

#### 5. queue

- STL中封装好的队列，也挺常用的
- 头文件`#include <queue>`
- 声明规则和stack一样直接声明便可
- 遍历规则也和stack差不多，由于是队列，只能从队头访问遍历，但很多人不知道的是，queue是可以访问队尾元素的，并且修改队尾元素

##### 5.1 和栈相同用法的函数

- **push**和**pop**函数，值得注意的是，这两个函数和stack的用法一样，但意义不同，queue的push是将元素压入队列尾部，pop是取出队尾元素
- **size**函数
- **empty**函数

##### 5.2 front函数和back函数

- 这两个函数和vector中的是一样的，分别返回队头和队尾的**引用**元素

#### 6. priority_queue

- 一个STL封装的优先队列，通常我们将它当做堆来使用，其默认为大顶堆
- 头文件`#include <queue>`

##### 6.1 声明方式

- 一般直接声明就好了

```cpp
priority_queue<int> heap;
```

- 但如果想要改变成小顶堆，则需要这样声明

```cpp
priority_queue<int, vector<int>, greater<int> > heap1; // 小顶堆
priority_queue<int, vector<int>, less<int> > heap2;   // 大顶堆，即默认情况
```

- 若是自己定义的结构体则需要重写小于符号

```cpp
struct pii {
    int x;
    // 注意比的是谁的优先级小
    bool operator< (const pii& j) const { // 两个const必须加
        if (this->x > j.x) {
            return true; // 只有当this的x比j的x要大时，this的优先级才比j的优先级要小，即x越大，优先级越小
        }
        return false;
    }
};
int main() {
    priority_queue<pii> heap;
    heap.push({.x = 1});
    heap.push({.x = 2});
    cout << heap.top().x << endl; // 1
    return 0;
}
```

##### 6.2 遍历方式

- 只能从堆顶访问元素

##### 6.3 size函数（常用）

- 用于返回堆中元素的个数

##### 6.4 push和pop函数（常用）

- 分别用于插入元素和取出堆顶元素

##### 6.5 top函数（常用）

- 返回堆顶元素的**引用**

##### 6.6 empty函数（常用）

- 判断堆中是否为空

#### 7. pair

- 一个将两个类型的数捆绑在一起
- 头文件`#include <utility>`或者`#include <map>`
- 对于新手准备两种容易理解的仿内部实现原理参考，通过代码理解pair到底是什么(注意由于名字重了，所以建议读者自己吧结构名、类名改了再运行)

```cpp
struct pair {
    int first;
    double second;

    // 排序的时候
    bool operator< (const pair &j) const {
        if (this->first == j.first) { // 如果两者的first一样，则
            return this->second < j.second; // 对比second的大小
        }
        return this->first < j.first; // 不一样则对比first的大小
        // 总结就是先如果first不一样，则对比first，否则对比second的大小
    }
}p;
// 此时的p相当于是 pair<int, double> p的声明方式
```

```cpp
template<class T1, class T2>
class pair {
public:
    T1 first;
    T2 second;
        // 排序的时候
private:
    bool operator< (const pair &j) const {
        if (this->first == j.first) { // 如果两者的first一样，则
            return this->second < j.second; // 对比second的大小
        }
        return this->first < j.first; // 不一样则对比first的大小
        // 总结就是先如果first不一样，则对比first，否则对比second的大小
    }
};
```

##### 7.1 声明

- 直接声明就好了
- 例如，我想将int和string捆绑在一起

```cpp
pair<int, string> p;
```

##### 7.2 遍历方式

- 直接向类一样的点运算符访问就好了
- 例如我想知道first的值`p.first`就好

##### 7.3 make_pair函数

- 此函数是用来给pair赋值的

```cpp
pair<int, string> p;
p = make_pair(3, "123");
```

#### 8. set

- 一个STL封装的集合的容器，其机制正如我说的集合一样，他保证了内部装元素是唯一的，即便你重复插入了
- 其在你插入时自动排序了，固插入的时间复杂度为$O(logn)$,你可以把他想象成封装好的平衡树（内部实现原理好像就是红黑树）,注意到，如果你要插入自己定义的结构体或者类，需要重写小于号，但一般遇到这种题可能真要你自己手写平衡树了
- 头文件`#include <set>`

##### 8.1 声明方式

- 一般声明是直接声明的
- 也可以用数组形式初始化

```cpp
// 方式一
set<int> ass1;

// 方式二
set<int> ass2 = {3, 4, 2, 3};

// 方式三
int arr[] = {3, 4, 3, 2};
set<int> ass3(arr, arr + 4);
```

##### 8.2 遍历方式

- set是不能用下标遍历的，本人一般在可以用c++11的情况下是用以下的遍历方式的

```cpp
set<int> ass = {3, 4, 3, 2, 5};
for (int x : ass) {
    cout << x << ' ';
}
// 输出为： 2 3 4 5 
```

##### 8.3 insert函数（常用）

- 用于插入元素的函数，用法很简单
- 其返回值为一个pair类型，具体是`pair<set<T>::iterator, bool>`

```cpp
set<int> ass = {3, 4, 3, 2, 5};
ass.insert(1); // 插入一个 1
if (ass.insert(3).second == false) {
    cout << "插入失败，set里面有这个元素了" << endl; // 这句话会被执行
}
```

##### 8.4 erase函数（常用）

- 用于删除元素，返回值为`size_t`，一般删除成功返回1，失败返回0
- 也可以传入迭代器

```cpp
set<int> ass = {3, 4, 3, 2, 5, 1};
ass.erase(3);
ass.erase(ass.begin()); // 删除指点迭代器的元素
ass.erasse(ass.begin(), ass.begin() + 2); // 删除连个迭代器范围内的数， 几乎用不上，不多解释
```

##### 8.5 size函数（常用）

- 返回容器内部元素的个数，注意由于set内部保证元素唯一，已有的相同元素无论插入几次都是不会改变size的

##### 8.6 empty函数（常用）

- 判断容器是否为空

##### 8.7 count函数（常用）

- 返回某个元素在set内的个数，由于保证唯一，所以我们可以把它理解成判断某个元素是否存在

```cpp
set<int> ass = {3, 4, 3, 2, 5};
cout << ass.count(3) << endl; // 1
cout << ass.count(9) << endl; // 0
```

##### 8.8 clear函数（常用）

- 用于清空set容器，不用传参，时间复杂度$O(1)$

##### 8.9 lower_bound函数（常用）

- $O(logn)$的时间复杂度内第一个大于等于指定元素的迭代器，找不到返回`end()`

```cpp
set<int> ass = {8, 6, 4, 2};
cout << *ass.lower_bound(3) << endl; // 4
cout << *ass.lower_bound(6) << endl; // 6
if (ass.lower_bound(9) == ass.end()) {
    cout << "not found" << endl; // 被执行
}
```

##### 8.10 upper_bound函数（常用）

- $O(logn)$的时间复杂度内第一个大于等于指定元素的迭代器，找不到返回`end()`

```cpp
set<int> ass = {8, 6, 4, 2};
cout << *ass.upper_bound(3) << endl; // 4
cout << *ass.upper_bound(6) << endl; // 8
if (ass.upper_bound(9) == ass.end()) {
    cout << "not found" << endl; // 被执行
}
```

##### 8.11 find函数（不常用）

- 返回查找元素的迭代器，找不到返回`end()`

#### 9. unordered_set

- 如果说你只是希望存放元素，而不希望他帮你自动排序，则可以考虑用这个容器
- 由于少了插入后排序的功能，固他的速度比set快不少
- set内部原理是红黑树，unordered_set的内部原理是hash
- 头文件`#include <unordered_set>`'
- 除了几个`lowwer_bound`函数和`upper_bound`函数不提供外，其他几乎和set一样

#### 10. multiset

- 如果说set是不能重复元素的话，那这个multiset就是可以重复了
- 在这里不在对此容器多废话了，毕竟要我重复我还不如用map呢

#### 11. map

- map是STL中提供的关联容器
- 如果说我们平时数组遍历是 `arr[int] = T`的话（[]内是整数），map就可做到`map[T1] = T2`的形式([]内的自己指定的数据类型，T2也是)
- 其内部也是用红黑树来实现的，固他会根据'T1'（key）的值来排序的，固插入查找修改的时间复杂度都是$O(logn)$
- 头文件`#include <map>`

##### 11.1 声明方式

- 一般我们在做题中，声明后是不会手动初始化的

```cpp
// 方式一
map<double, long long> mp1;
// 方式二，用得少
map<string, int> mp2 = {
    {"123", 4},
    {"abc", 5},
    {"ha", 99999999},
    {"##", -100}
};
```

##### 11.2 插入方式

- 如果传统的插入方式我们是使用**insert**函数进行插入的

```cpp
map<string, int> mp = {
    {"123", 4},
    {"abc", 5},
    {"ha", 99999999},
    {"##", -100}
};
// 方式一
mp.insert({"222", 5});

//方式二
mp.insert(pair<string, int>("333", 6));

// 方式三，和方式二等价
mp.insert(make_pair("222", 6)); // 但是插入失败了，因为之前有"222"的健值，固mp["222"]依旧等于5

// 方式四,多个插入，几乎没用
mp.insert({ {"555", 6}, {"666", 7} });
```

- 最常用的就是利用map的特性直接用`[]`运算符，如果key值存在，则修改其对应的value值，否则插入

```cpp
map<string, int> mp = {
    {"123", 4},
    {"abc", 5},
    {"ha", 99999999},
    {"##", -100}
};
mp["222"] = 5; // 由于原本没有"222"，固插入
mp["222"] = 6; // 由于有了"222"，固改变其对应的值
```

##### 11.3 遍历/访问方式

- 如果是单个遍历的的话我们直接利用`[]`符号便可
- 注意，使用这种遍历方式，如果访问量不存在的key值，则会返回其默认值好像，不同类型不同，固一般在不确定是否存在的情况下，先判断其是否存在，可以使用下面要讲的**count**函数判断，这里不再演示

```cpp
map<string, int> mp = {
    {"123", 4},
    {"abc", 5},
    {"ha", 99999999},
    {"##", -100}
};
cout << mp["ha"] << endl; // 99999999
```

- 如果是内部遍历，本人一般使用的是c++11的变量方式

```cpp
map<string, int> mp = {
    {"123", 4},
    {"abc", 5},
    {"ha", -99999999},
    {"##", 100}
};

// 下面两种形式是等价的
for (pair<string, int> x : mp) {
    cout << x.first << ':' << x.second << endl;
}
cout << "---------------\n";
for (auto x : mp) {
    cout << x.first << ':' << x.second << endl;
}
/*
输出：
##:100
123:4
abc:5
ha:-99999999
---------------
##:100
123:4
abc:5
ha:-99999999
*/
```

- 当然也可以用c++17的for的结构化能力，更加方便

```cpp
map<string, int> mp = {
    {"123", 4},
    {"abc", 5},
    {"ha", -99999999},
    {"##", 100}
};

for (auto [a, b] : mp) {
    cout << a << ":" << b << endl;
}
/*
输出：
##:100
123:4
abc:5
ha:-99999999
*/
```

##### 11.4 erase函数（常用）

- 该函数传参一般有两种形式，一种是传入key值，一种是传入相应的迭代器，返回`size_t`类型，一般是删除成成功返回1，否则返回0

```cpp
map<string, int> mp = {
    {"123", 4},
    {"abc", 5},
    {"ha", -99999999},
    {"##", 100}
};
mp.erase("abc"); // 删除健值为"abc"的
mp.erase(mp.begin()); // 传入头部迭代器
```

##### 11.5 count函数（常用）

- 作用和用法同set里的差不多

##### 11.6 size函数（常用）

- 返回map的大小

##### 11.7 empty函数（常用）

- 判断map是否为空

##### 11.8 clear函数（常用）

- 用于清空map，时间复杂度好像是$O(1)$

##### 11.9 lower_bound函数（常用）

- 一般是传入健值查找第一个大于等于他的，返回查找到的迭代器，查找失败则返回`end()`

```cpp
map<int, string> mp = {
    {1, "a"},
    {-10, "b"},
    {100, "c"},
    {101, "d"},
    {-4, "e"}
};
cout << mp.lower_bound(100)->second << endl; // 找到是mp[100] = c,固输出 c
if (mp.lower_bound(9999) == mp.end()) {
    cout << "not found" << endl; // 被执行
}
```

##### 11.10 upper_bound函数（不常用）

- 一般是传入健值查找第一个大于他的，返回查找到的迭代器，查找失败则返回`end()`

```cpp
map<int, string> mp = {
    {1, "a"},
    {-10, "b"},
    {100, "c"},
    {101, "d"},
    {-4, "e"}
};
cout << mp.upper_bound(100)->second << endl; // 找到是mp[101] = d,固输出 d
if (mp.upper_bound(9999) == mp.end()) {
    cout << "not found" << endl; // 被执行
}
```

##### 11.11 find函数（不常用）

- 传入健值，返回查找到的迭代器，几乎没用

```cpp
map<int, string> mp = {
    {1, "a"},
    {-10, "b"},
    {100, "c"},
    {101, "d"},
    {-4, "e"}
};
cout << mp.find(-4)->second << endl; // 等价于 mp[-4]，几乎没用
```

#### 12. unordered_map

- 他和map的关系就像unordered_set和set的关系一样，挺有用的一个容器
- 头文件`#incldue <unordered_map>`

#### 13. bitset

- 一个存放位的容器，挺常用的，一般用来优化一些算法，主要优化时间和空间，可以节省$1/64$常数好像
- 比较神奇的是你开多少位，他就几乎占多少位
- c++还支持用`cout`对他输出

- 这玩意好像是c++11的产品，~~忘记了~~

##### 13.1 声明方式

- 声明方式中需要注意的是`<>`里放的数字必须是常量，而且其表示的是你声明的长度

```cpp
bitset<10> b1; //声明 10 位，每位都是0

bitset<10> b2(20); // 传入 unsigned long 类型值为20的参数，存放10位其二进制的值
cout << b2 << endl; // 0000010100

bitset<10> b3("1010"); // 传入二进制字符串
cout << b3 << endl; // 0000001010
```

##### 13.2 访问方式

- 一般我们直接把他当做bool数组看就好了，固我们可以用`[]`来访问其对应位数的值

##### 13.3 运算

- 位运算支持的运算他都支持（自己探索吧）

##### 13.4 其他函数

- **b.any()** : b中是否存在置为1的二进制位
- **b.none()** ：b中是否不存在为1的二进制位
- **b.count()** : 返回1的个数
- **b.test(pos)**：等价于`b[pos]`
- **b.set()**：将b中所有位都置1
- **b.set(pos)**：等价于`b[pos] = 1`
- **b.reset()**：将b中的所有位都置0
- **b.reset(pos)**：等价于`b[pos] = 0`
- **b.filp()**：等价于`b = ~b`
- **b.filp(pos)**：等价于`b[pos] = !b[pos]`
- **b.to_string()**：将其转换成string类型
- **b.to_ulong()**：返回其二进制对应的unsigned long类型
- **b.to_ullong()**：返回其二进制对应的unsigned long long类型

#### 14. deque

- 一个双端队列，可以在头部和尾部插入和删除
- 和queue不同的是，为了区分头部尾部插入删除，头部插入删除用`push_front`和`pop_front`，尾部用`push_back`和`pop_back`
- 头文件`#include <deque>`

#### 15. list

- 一个STL提供的双向链表容器
- 说实话不怎么常用，真考到一般我都是用链式前向星模拟的
- 学到这里的一般都能对STL中容器提供的函数其实差不多的
- 还有一点需要注意的是，它不支持随机访问，毕竟链表嘛
- 固这里直接贴常用函数的作用吧

##### 15.1 声明方式

- 只写常用的声明方式

```cpp
// 方式一
list<int> l1;

// 方式二，声明长度为5，值为其默认，不同类型不同默认
list<int> l2(5);

// 方式三， 声明长度为3，每个值都为2的
list<int> l3(3, 2);
```

##### 15.2 遍历方式

- 直接c++11里for就好了

```cpp
for(auto x : l) {
    cout << x << endl;
}
```

##### 15.3 常用函数

- **push_front(val)**：在头部插入
- **pop_front()**：在头部删除
- **push_back(val)**：尾部插入
- **pop_back()**：尾部删除
- **insert(iterator, val)**：在迭代器iterator前插入val
- **insert(iterator, count, val)**：在迭代器iterator前插入count个val
- **front()**、**back()**：返回头元素、尾元素的**引用**
- **begin()**、**end()**：返回头部和尾部下一位置的迭代器
- **size()**：大小
- **sort()**：升序排序
- **clear()**：清空
- **reverse()**：翻转链表
- **merge(list2)** ：用第二个有序的 list 合并一个有序 list
- **splice(list.iterator, list2, list2.iterator_start, list2.iterator_end)**：在本list的 `iterator`后插入list2的从 `iterator_start` 到 `iterator_end`， 后面两个可填可以不填，当填了`iterator_start`，可不填最后一个，时间复杂度O(1)
- **erase(iterator)**：删除iterator，返回删除前的下一个的迭代器
- **erase(iterator_start, iterator_end)**：删除[iterator_start, iterator_end)范围内的元素，返回删除前的iterator_end

### 四、algorithm

#### 1. sort

- 最最最常用的算法函数之一
- 其传参有两种
  - 第一种：传入需要排序的数组的头的位置和尾的下一个位置指针
  - 第二种：在第一种的前提下多一个比较函数
- 具体看代码

```cpp
bool cmp(int a, int b) {
    return a > b;
}

int main() {
    int arr[10] = {8, 7, 4, 2, 1, 3, 9, 10, 5, 6};
    // 形式一
    sort(arr, arr + 10); // 排序下标范围 [0, 10)
    // 形式二
    sort(arr + 1, arr + 9, cmp); // 排序下标范围[1, 9), 用cmp函数比较，即降序排序

    // 形式三 和形式二等价，只是用来lambda表达式罢了
    sort(arr + 1, arr + 9, [](int a, int b) {
        return a > b;
    });

    // 形式四 和形式二等价，只是用了c++封装好的函数
    sort(arr + 1, arr + 9, greater<int>()); // 降序

    // 对于vector需要传入迭代器
    vector<int> vt(arr, arr + 10); // 赋值为arr的元素
    
    // 和上面的其实差不多
    sort(vt.begin(), vt.end());
    
    sort(vt.begin() + 1, vt.end() - 1, cmp);
    
    sort(vt.begin(), vt.end(), [](int a, int b) {
        return a > b;
    });
    
    sort(vt.begin(), vt.end(), less<int>()); //升序
    
    return 0;
}
```

#### abs

- 返回一个数的绝对值（int、long long、 double）

#### 2. min和max

- 用于比较两个同类型（注意是同类型）的最小值和最大值
- 在c++11中还提供了多个数一起比较大小

```cpp
cout << max({100, 99, 10, 1000}) << endl; // 1000
cout << min({100, 99, 10, 1000}) << endl; // 10
```

#### 3. swap

- 用于交换两个同类型（注意是同类型）的变量

#### 4. reserve

- 用来翻转指定范围的数组，传参规则和sort第一种传参形式一样

#### 5. lower_bound

- 用于查找一个有序序列指定范围内的第一个大于等于给定值的位置，返回的是指针或者迭代器，即如果是数组，找不到返回范围的尾部，容器则返回`end()`
- 时间复杂度为$O(logn)$
- 如果是自己定义的类型，记得重写小于符号（~~那还不如自己手写二分呢~~）

```cpp
vector<int> vt = {0,2,4,6,8,10,12};
cout << *lower_bound(vt.begin(), vt.end(), 3) << endl; // 4
cout << *lower_bound(vt.begin(), vt.end(), 4) << endl; // 4
```

#### 6. upper_bound

- 用于查找一个有序序列指定范围内的第一个大于给定值的位置，返回的是指针或者迭代器，即如果是数组，找不到返回范围的尾部，容器则返回`end()`
- 时间复杂度为$O(logn)$

```cpp
vector<int> vt = {0,2,4,6,8,10,12};
cout << *upper_bound(vt.begin(), vt.end(), 3) << endl; // 4
cout << *upper_bound(vt.begin(), vt.end(), 4) << endl; // 6
```

#### 7. unique

- 用于将去除一个有序序列中指定范围内的重复元素（实际没有去除）,返回去除后的尾指针

```cpp
vector<int> vt = {0,0,2,2,4,4,6,6,8,8,10,10,10,12,12};
int index = unique(vt.begin(), vt.end()) - vt.begin();
cout << index << endl; // 7
```

#### 8. next_permutation

- 用于将序列指定范围的序列排成下一个排列
- 具体用法比较模板化

```cpp
int arr[3] = {1, 2, 3};
do {
    for (int i = 0; i < 3; ++i) {
        cout << arr[i];
    }
    cout << endl;
} while (next_permutation(arr, arr + 3));
/*
输出：
123
132
213
231
312
321
*/
```

#### 9. nth_element

- 一般用于获取序列（有序无序都可以）第n小的数
- 用法比较奇葩
- 时间复杂度接近$O(n)$

```cpp
    int arr[10] = {8, 7, 4, 2, 1, 3, 9, 10, 5, 6};
    nth_element(arr, arr + 6, arr + 10); // 让 arr + 6这个位置的数就绪，即将第7大的数放到arr[6]
    // 即执行完后，arr + 6位置前的数都比他小，后面的数都不低于他
    for (int x : arr) {
        cout << x << ' ';
    }
    cout << endl;
    // 输出 6 5 4 2 1 3 7 8 10 9 
    cout << arr[6] << endl; // 获取排第7即位置6的数 输出 7
```

#### 10. find

- 暴力查找一个序列中指定范围内的某个值第一次出现的位置，返回其位置的指针，若找不到返回尾指针
- 时间复杂度$O(n)$
- 不重要，不细说

#### 11. random_shuffle

- 一个用于随机打乱给定范围的序列
- 传入头指针和尾指针










