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

## 14.复合&继承关系下的构造和析构 ##

**Inheritance（继承）关系下的构造和析构**
base class的dtor（析构函数）必须是virtual，否则会出现undefined behavior。

构造由内而外，子类的构造函数首先调用父类的default默认构造函数，然后才执行自己。
析构由外而内，子类的析构函数首先执行自己，然后调用父类的析构函数。

**Composition（复合）关系下的构造和析构**
构造由内而外，Container的构造函数首先调用Component的默认构造函数，然后才执行自己。
析构由外而内，Container的析构函数首先执行自己，然后才调用Component的析构函数。

**Inheritance+Composition关系下的构造和析构**
构造由内而外，子类的构造函数首先调用父类的默认构造函数，然后调用Component的默认构造函数，然后才执行自己。
析构由外而内，子类的析构函数首先执行自己，然后调用Component的析构函数，然后调用父类的析构函数。

## 15.虚指针和虚函数表 ##
![](https://i.imgur.com/nIkKZhP.jpg)
如上图所示，定义了三个类，A、B和C，B继承于A,C继承于B，A中有两个虚函数，B中有一个，C中也有一个。编译器将A的对象a在内存中分配如上图所示，只有两个成员变量m\_data1和m\_data2，与此同时，由于A类有虚函数，编译器将给a对象分配一个空间用于保存虚函数表，这张表维护着该类的虚函数地址（动态绑定），由于A类有两个虚函数，于是a的虚函数表中有两个空间（黄蓝空间）分别指向A::vfunc1()和A::vfunc2()；同样的，b是B类的一个对象，由于B类重写了A类的vfunc1()函数，所以B的虚函数表（青色部分）将指向B::vfunc1()，同时B继承了A类的vfunc2()，所以B的虚函数表（蓝色部分）将指向父类A的A::vfunc2()函数；同样的，c是C类的一个对象，由于C类重写了父类的vfunc1()函数，所以C的虚函数表（黄色部分）将指向C::vfunc1()，同时C继承了超类A的vfunc2()，所以B的虚函数表（蓝色部分）将指向A::vfunc2()函数。同时上图也用C语言代码说明了编译器底层是如何调用这些函数的，这便是面向对象继承多态的本质。


这里设置了3个class，A,B,C之间是继承的关系，A有之间的data1和2,B继承了A，有了A的数据，然后加上自己的数据，C也是。

如果一个类里面有虚函数，这个对象里面就会多了一个指针，称为虚指针，指向虚函数表，那有多少个虚函数，虚函数表里面就会有多少个指针指向虚函数

如果父类有虚函数，子类一定有，会继承父类虚函数的调用权，这时候以B为例子，B推翻了vfunc1()重载了，留下了vfunc2().

vptr关联了vtbl(里面都是函数指针)然后关联了虚函数。

通过new c可以得到p,通过p要调用vfunc1()，因为vfunc1是虚函数，所以编译器不能通过老式的call一个地址(静态绑定),就只能通过动态绑定去寻找这个函数，如

```
(* (p-vptr)[n]) (p );

(* p->vptr[n]) (p );
```
n表示表的第几个。


如果想设计一个能够画出各个图形的程序，首先为了让一个容器可以容纳各个对象，每个对象的大小肯定不一样，所以不好放入容器，只能通过指针指向同类，这个指针必须是父类，因为所有的东西都是它的一种。我们通过这个指针去调用draw，各自的指针就会调用自己的重写的虚函数去画自己，如果没有重写，就会默认用父类的

以前我们会对各种类型使用enum来判断这是什么图形，然后通过转接表的编号判断去调用哪个函数，在C语言中如果我现在多了几个新的图形，又得重新修改代码，现在在C++中就不用了。

多态的解释：
比如上面的一个父类被子类继承，子类可以重写父类的虚函数，使自己有自己的形态，父类又可以访问到他，如 A* p = new C; new 后面不一样，就让父类有的不一样的形态，从而实现多态。

符合下列3个条件（动态绑定）：

1、是指针

2、向上转型

3、调用的是虚函数

动态绑定就是要看指针指向哪一个块，然后通过虚指针找到虚表，取出第n个，把他当成指针去调用相应的虚函数。


## 16.this指针 ##
![](https://i.imgur.com/PH9T0ss.jpg)
this指针其实可以认为是指向当前对象内存地址的一个指针，如上图所示，由于基类和子类中有虚函数，this->Serialize()将动态绑定，等价于(*(this->vptr)[n])(this)。可以结合上节虚指针和虚函数表来理解，至于最后为什么这样写是正确的，下面小结将会解释。

其实每个类里面都会有一个默认的this指针，子类创建一个对象myDoc，这个对象调用父类的方法，&myDoc就相当于OnfileOpen函数里面的this指针了，再通过这个this指针找到被子类重写的virtual Serialize 函数。

## 17.动态绑定 ##
![](https://i.imgur.com/C97NUFQ.jpg)
![](https://i.imgur.com/lDi3V3I.jpg)

## 18.重载delete和new操作符 ##
![](https://i.imgur.com/h5tWgeY.png)
![](https://i.imgur.com/EYF1OCI.png)
![](https://i.imgur.com/XZg8al9.png)
![](https://i.imgur.com/rqX4a3j.jpg)
![](https://i.imgur.com/WtkG1Kv.jpg)
![](https://i.imgur.com/EUhdjmM.jpg)

## 19.重载delete()和new()操作符 ##
![](https://i.imgur.com/c8jAMdu.png)
![](https://i.imgur.com/Sd2vMWM.jpg)
![](https://i.imgur.com/3Mgirvx.jpg)
