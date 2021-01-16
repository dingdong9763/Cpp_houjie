## 1.转换函数 ##
![](https://i.imgur.com/D2CbMvC.png)
任何Fraction需要被转换为double类型的时候，自动调用double()函数进行转换。如上图所示，编译器在分析double d = 4 + f过程中判断4为整数，然后继续判断f，观察到f提供了double()函数，然后会对f进行double()操作，计算得到0.6，再与4相加，最后得到double类型的4.6。
## 2.explicit与隐式转换 ##
![](https://i.imgur.com/6hPhBVv.png)
上图中定义了一个类，叫Fraction，类里面重载了“+”运算符，在f+4操作过程中，“4”被编译器隐式转换（构造函数）为Fraction对象，然后通过Fraction重载的“+”运算符参与运算。

![](https://i.imgur.com/CJMA6oJ.jpg)
如上图所示，在Fraction中增加了double()函数，将Fraction的两个成员变量进行除法运算，然后强制转化为double类型并返回结果，在f+4重载过程中编译器将报错，可以做出如下分析：

1、首先4被隐式转化（构造函数）为Fraction对象，然后通过重载的“+”运算符与“f”进行运算返回一个Frction对象；

2、首先4被隐式转化（构造函数）为Fraction对象，然后通过重载的“+”运算符与“f”运算后对进行double运算，最返回一个Frction对象；

3、。。。

所以编译器有至少两条路可以走，于是产生了二义性，报错。
![](https://i.imgur.com/fZU3aYV.png)
如上图所示，在构造函数Franction前加入explict关键字，隐式转换将取消，所以在执行d2 = f + 4过程中，f将调用double函数转换为0.6，然后与4相加变成4.6，由于构造函数取消隐公式转换，4.6无法转换为Fraction，于是将报错。

下图为C++ stl中操作符重载和转换函数的一个应用：
![](https://i.imgur.com/01jFo9x.png)

## 3.智能指针 ##
下面这张图很好地说明了智能指针的内部结构和使用方法：
![](https://i.imgur.com/UenFAl5.jpg)
智能指针在语法上有三个很关键的地方，第一个是保存的外部指针，对应于上图的T* px，这个指针将代替传入指针进行相关传入指针的操作；第二个是重载“*”运算符，解引用，返回一个指针所指向的对象；第三个是重载“->”运算符，返回一个指针，对应于上图就是px。

迭代器也是一种智能指针，这里也存在上面提到的智能指针的三个要素，分别对应于下图的红色字体和黄色标注部分：
![](https://i.imgur.com/Gsty5Hz.jpg)

下面将仔细分析迭代器重载的“*”和“->”重载符：

![](https://i.imgur.com/8TiNyqF.jpg)

容器本身一定带有迭代器，迭代器也是一种智能指针，代表容器里的一个元素，要处理很多个符号，比如++--，用于指针的移动，可以遍历容器。最关键的还是 * 以及 -> ，星号取出了这个node指针的data数据，->则是利用了 * 操作符重新取回原地址来实现的。

创建一个list迭代器对象，list<Foo>::iterator ite;这里的list用于保存Foo对象，也就是list模板定义里的class T，operator\*()返回的是一个(\*node).data对象，node是\_\_link\_type类型，然而\_\_link\_type又是\_\_list\_node<T\>\*类型，这里的T是Foo，所以node是\_\_list\_node<Foo\>*类型,所以\(\*node\).data得到的是Foo类型的一个对象，而&\(operator\*\(\)\)最终得到的是该Foo对象的一个地址，即返回Foo\* 类型的一个指针。

## 4.仿函数 ##
![](https://i.imgur.com/DhX59Kd.jpg)
从上图可以看到，每个仿函数都是某个类重载“()”运算符，然后变成了“仿函数”，实质还是一个类，但看起来具有函数的属性。每个仿函数其实在背后都集成了一个奇怪的类，如下图所示，这个类不用程序员手动显式声明。
![](https://i.imgur.com/EKpcd2X.jpg)
标准库中的仿函数也同样继承了一个奇怪的类：
![](https://i.imgur.com/GqWy1Yz.jpg)
这个类的内容如下图所示，只是声明了一些东西，里面没有实际的变量或者函数，具体的内容将在STL中讨论。
![](https://i.imgur.com/G5iodMK.png)


每个类重载了“()”运算符，看起来像一个函数，因为所做出来的对象可以接受小括号。第一个小括号是为了创建临时对象，第二个小括号调用```operator() (const Pair & x) const {return x.first}```这个函数。
```
select1st <pair> () ()
```
## 5.namespace ##
![](https://i.imgur.com/J2k7KGj.png)

## 6.类模板 ##
![](https://i.imgur.com/j1zJLWl.png)

## 7.函数模板 ##
![](https://i.imgur.com/J5MLXNd.png)
与类模板不同的是，函数模板在使用是不需要显式地声明传入参数类型，编译器将自动推导类型。

## 8.成员模板 ##
![](https://i.imgur.com/Y9FBR6k.png)
成员模板在泛型编程里用得较多，为了有更好的可扩展性，以上图为例，T1往往是U1的基类，T2往往是U2的基类，可以看下面这个例子：
![](https://i.imgur.com/FVN4ww1.jpg)
通过这种方法，只要传入的U1和U2的父类或者祖类是T1和T2，那么通过这样的方式可以实现继承和多态的巧妙利用，但反之就不行了。这样的方式在STL中用得很多：
![](https://i.imgur.com/7ig5lAg.png)

## 9.模板偏化 ##
正如其名，模板偏化指的是模板中指定特定的数据类型，这和泛化是不同的：
![](https://i.imgur.com/maGjkzC.png)
当然，模板偏化也有程度之分，可以部分类型指定，称之为偏特化：
![](https://i.imgur.com/1qfrg4O.png)
![](https://i.imgur.com/ixantUB.png)

## 10.模板模板参数 ##
![](https://i.imgur.com/TUIdZpP.png)
![](https://i.imgur.com/wulBSIu.png)
![](https://i.imgur.com/sUNnTCc.png)

## 11.可变数目模板参数 Variadic Templates##
![](https://i.imgur.com/YqS87h4.jpg)
过多内容将在C++11课程中讲解，这里暂时只做介绍。

模板参数的个数可变化，分为一个和后部分一个包，用…表示，以递归的形式进行，让一个包递归，这个包的第一个参数就会输出，… 指定包的个数为0，就会调用void print（）函数，所以一定有一个这样的空函数。

…在这里表示一个pack(包)，这里的代码就是打印出输入的参数，最后参数为0的时候，为了防止程序错误，给出一个空参数的print。
如果想知道这一个包有几个，可以使用sizeof…(args);


## 12.auto关键字和增强型for循环
![](https://i.imgur.com/C3Gmlaa.png)
![](https://i.imgur.com/HwUMzfD.png)
过多内容将在C++11课程中讲解，这里暂时只做介绍

for的新形式

decl是一个变量，coll表示容器，编译器从coll这个容器里抓出变量赋值给decl。

在以前可以使用foreach来遍历容器，可以使用迭代器来遍历容器。现在c++11增加了这一种新的方式。

这里是pass by value,如果在里面修改elem的大小，也不会改变原来容器里变量的大小。如果需要修改的话，需要pass by reference。 

reference,其实内部就是由指针实现的。只是表现的形式不同。

## 13.reference ##
reference可以看做是某个被引用变量的别名。
![](https://i.imgur.com/pYlpmg2.png)
![](https://i.imgur.com/3rfj4at.png)
![](https://i.imgur.com/iVYlytf.jpg)

```
int x = 0; //变量，占用4个字节
int* p = &x; // 指针变量，占用4个字节（32位）。pointer to int。指向整数x，初值是x的地址，&表示取地址。地址就是指针的一种形式，指针也是地址的一种形式，是互通的。
int& r = x; // r从逻辑上来说不是指针，代表整数，但是编译器会用指针的方式实现r，reference底部是一个指针，指向x。r is a reference to x. r代表x.现在r,x都是0。 
            // sizeof(r) == sizeof(x)，x多大r就多大。所以r的大小是4。
            // 引用要设初值。指针可以变化，但是reference不可以变化，指向后不可以改变。
            // &x == &r; object和其reference的大小相同，地址也相同（但是全都是假象）
int x2 = 5;
r = x2; // r不能重新代表其他物体，但是可以重新设值。所以现在r,x的值都是5。
int& r2 = r; // 现在r2是5（r2代表r；亦相当于代表x）

```

**reference的常见用途**

我们在写代码的时候很少这样用reference去代表一个变量，我们一般把reference用在参数传递上，主要还是为了让reference更像传递进来了一个对象，而不是一个指针，在写法上，其底层是表示一个指针。

reference通常不用于声明变量，而用于参数类型和返回类型的描述。

```
void func1 (Cls* pobj) { pobj->xxx(); } // pass by pointer 被调用端写法不同
void func2 (Cls obj) { obj.xxx(); } // pass by value
void func3(Cls& obj) { obj.xxx(); } // pass by reference 与上面的被调用端写法相同是很好的

......

Cls obj;
func1(&obj); // 接口不同，会造成困扰
func2(obj); // 传递过程比较慢
func3(obj); // 调用端接口相同，表述相同，也很好。而且传引用比较快。

```

以下被视为“same signature” （所以二者不能同时存在）：签名指的是```imag(...) const``` 这一部分。因为这两个函数是同名函数，但是如果是像上面的func2/func3名字不相同就没问题。
```
double imag(const double& im) const { ... }
double imag(const double  im) const { ... } //Ambiguity二义
```

Q：const是不是函数签名的一部分？

A：是一部分。所以上面两个函数如果一个有const一个没有，是可以共存的。

## 14.虚指针和虚函数表 ##
![](https://i.imgur.com/nIkKZhP.jpg)
如上图所示，定义了三个类，A、B和C，B继承于A,C继承于B，A中有两个虚函数，B中有一个，C中也有一个。编译器将A的对象a在内存中分配如上图所示，只有两个成员变量m\_data1和m\_data2，与此同时，由于A类有虚函数，编译器将给a对象分配一个空间用于保存虚函数表，这张表维护着该类的虚函数地址（动态绑定），由于A类有两个虚函数，于是a的虚函数表中有两个空间（黄蓝空间）分别指向A::vfunc1()和A::vfunc2()；同样的，b是B类的一个对象，由于B类重写了A类的vfunc1()函数，所以B的虚函数表（青色部分）将指向B::vfunc1()，同时B继承了A类的vfunc2()，所以B的虚函数表（蓝色部分）将指向父类A的A::vfunc2()函数；同样的，c是C类的一个对象，由于C类重写了父类的vfunc1()函数，所以C的虚函数表（黄色部分）将指向C::vfunc1()，同时C继承了超类A的vfunc2()，所以B的虚函数表（蓝色部分）将指向A::vfunc2()函数。同时上图也用C语言代码说明了编译器底层是如何调用这些函数的，这便是面向对象继承多态的本质。

## 15.this指针 ##
![](https://i.imgur.com/PH9T0ss.jpg)
this指针其实可以认为是指向当前对象内存地址的一个指针，如上图所示，由于基类和子类中有虚函数，this->Serialize()将动态绑定，等价于(*(this->vptr)[n])(this)。可以结合上节虚指针和虚函数表来理解，至于最后为什么这样写是正确的，下面小结将会解释。

## 16.动态绑定 ##
![](https://i.imgur.com/C97NUFQ.jpg)
![](https://i.imgur.com/lDi3V3I.jpg)

## 17.重载delete和new操作符 ##
![](https://i.imgur.com/h5tWgeY.png)
![](https://i.imgur.com/EYF1OCI.png)
![](https://i.imgur.com/XZg8al9.png)
![](https://i.imgur.com/rqX4a3j.jpg)
![](https://i.imgur.com/WtkG1Kv.jpg)
![](https://i.imgur.com/EUhdjmM.jpg)

## 18.重载delete()和new()操作符 ##
![](https://i.imgur.com/c8jAMdu.png)
![](https://i.imgur.com/Sd2vMWM.jpg)
![](https://i.imgur.com/3Mgirvx.jpg)
