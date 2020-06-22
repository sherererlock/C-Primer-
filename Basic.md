#### 类型转换

整型提升

#### 引用

1 必须初始化

2 初始化后无法再绑定到另一个对象上

#### 指针

一种存放对象地址的类型

#### const限定符

用于声明值不能改变的变量，这种变量必须初始化。

默认状态下，const对象仅在文件内有效。extern.

#### const的引用

对常量的引用（常量引用）

##### 初始化和对const的引用

1 允许任意表达式作为初始值，只要能转换就可以。

2 可以绑定非常量的对象，字面值，甚至是一个一般表达式。

3 不能给引用绑定临时量

```
double val = 3.14;
const int & ri = val; //error
--->
const int tmp = val;
const int& ri= tmp；
```

#### 指针和const

##### const指针

常量指针，将*放在const之前，意味着指针存放的地址不可改变。

从右向左读取

#### 顶层const

用顶层const表示指针本身是一个常量

用底层const表示指针所指的值是一个常量

注意拷贝时，这两者的区别。

#### constexpr和常量表达式

常量表达式指值不会改变而且可以在编译过程中就能得到计算结果的表达式。

##### constexpr(c++11)

由编译器来验证一个变量是否时常量表达式

##### 指针和constexpr

constexpr仅对指针有效，对指针所指的值无效，而且指针的初始值必须是空或者存储于某个固定地址的对象。

#### auto关键字

编译器在编译时推断变量的类型，变量的定义必须有初始值。

##### 复合类型、常量和auto

1 用引用类型做初始值的变量会被推断为引用对象的类型

```
int i = 0,&r = i;
auto a = r; //a是一个整数
```

2 auto推断时会忽略顶层const，底层const会保留下来

```c++
const int ci = i, &cr = ci;
auto b = ci; // b是一个整数，const被忽略
auto e = &ci; // e是一个const int*类型，保留了底层const
```

#### decltype类型指示说明符

定义一个变量时只需要表达式的类型而不需要其初始值时使用decltype

```
decltype(f()) sum = f();
sum的类型就是f()返回值的类型
```

推断时会保留顶层const,括号中表达式的值是什么，decltype推断出的就是什么。

```c++
const int ci = 0, &cj = ci;
decltype(ci) x = 0;
decltype(cj) y = x;
decltype(cj) z; //error z的类型是const int& ，必须要初始化
```

注意：引用从来都是作为其所指对象的同义词出现，只有用在decltype处是一个例外

##### decltype与引用

1 如果decltype使用的不是变量而是一个表达式，则返回表达式结果对应的类型。

```c++
// decltype的结果可以是引用类型
int i = 42，*p = &i,&r = i;
decltype (r+0) b; // b是一个整数
decltype(*p) c; //error 解引用操作得到指针所指的对象，推断为int&,而不是int,引用必须初始化
```

decltype((variable)) 的结果永远是引用。

#### 函数

##### 数组形参

```
void print(const int*);
void print(const int[]);
void print(const int[10]); // 10只是我们期望传入函数的数组有多个值，实际上不一定
```

##### 数组引用形参

```
void print(int(&array)[10]); // 传递数组引用
void print(int& array[10]);  // 传递了一个包含10个引用的数组
```

##### 传递多维数组

```
void print(int (*mat)[10], int arraysize);
```

##### 返回值

###### 引用返回左值

调用一个返回值为引用的函数得到左值，其他类型则返回右值。

左值的意思是可以直接赋值。

##### 返回数组指针

因为数组无法被拷贝传递，所以函数只能返回数组的指针

###### 使用类型别名返回数组指针

```
type arrayT int[10];
using arrayT = int [10];
arrayT* func(int i );
```

###### 声明一个返回数组指针的函数

```
Type (*func(int i))[dimension];
Type (&func(int i))[dimension];
```

Type表示数组元素的类型，dimension表示数组的大小

###### 使用尾置类型返回数组指针

```
auto func(int i) -> int(*)[10];//注意指针符号必须用括号
```

尾置类型两要素auto ->Type

任何返回类型的函数都可以通过尾置类型返回

###### 使用decltype

```
int odd[6] ={1,1,2,3,4,5};
int new[5] = {1,2,3,4,5};
decltype(odd)* func(int i)
{
	return i==0?odd:new;
}
```

#### 函数匹配

候选函数：同名函数且在调用点可见的

可行函数：形参个数与实参个数相同，且形参匹配实参类型或者能实参能转换成形参类型

最佳匹配，二义性

##### 实参类型转换

确定最佳匹配的顺序

1 精确匹配

2 通过const转换实现的匹配

3 通过类型提升实现的匹配

4 算术匹配

5 通过类类型转换实现的匹配

#### 函数指针

```c++
void (*func) (int); //func 就是一个函数指针
```

decltype

##### 函数指针形参

使用函数类型作为形参时，函数类型会被转换成函数指针类型

##### 返回函数指针

需要返回一个函数时，则声明时必须声明为函数指针类型

可以使用尾置类型声明一个返回指针的函数

