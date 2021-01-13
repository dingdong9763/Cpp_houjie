## Composition（复合），表示has-a ##
adapter适配器 queue对deque来说相当于一个适配器，queue改造了deque，deque完全包含了queue所需要的功能，queue只需要用其中的几个接口

Composition关系下的构造和析构：

Container拥有Component。

构造由内而外，Container的构造函数首先调用Component的default默认构造函数之后才执行自己。

```Container::Container(...) : Component() {...};```

析构由外而内，Container的析构函数首先执行自己，然后才调用Component里面的析构函数。
```Container::~Container(...) { ... ~Component() };```

## Delegation(委托）.Composition by reference ##
类里有一个指针指向另一个类，用指针相连，这两个类的关系叫做Delegation（委托）。pImpl，Pointer to Implementation。

Copy On Write,写时复制，读时共享

## Inheritance（继承），表示is-a ##
```struct _List_node : public _List_node_base { ... };```//公有继承
父类的数据是可以完全被子类继承的，包括指针之类的。

父类Base，子类Derived，子类的对象有父类的成分。

构造由内而外，析构由外而内。

基类base class的dtor（析构函数）必须是virtual，否则会出现undefined behavior。

## Inheritance（继承） with virtual functions（虚函数） ##
non-virtual函数（非虚函数）：你不希望derived class（子类）重新定义（override，覆写）它。 //override只能用于虚函数

virtual函数：你希望derived class（子类）重新定义（override，覆写）它，且你对它已有默认定义。

pure virtual函数（纯虚函数）：你希望derived class（子类）一定要重新定义（override，覆写）它，你对它没有默认定义。类似于父类的抽象方法，子类必须要实现。

e.g：
```
virtual void draw() const = 0; //pure virtual，子类必须重新定义。纯虚函数搭配virtual ... = 0 
virtual void error(const std::string& msg); //impure virtual，子类可以有更精细的定义方式，可以重新定义
int objectID() const; //non-virtual，不需要下面的子类重新定义
```

## C++中的explicit ##
C++中， 一个参数的构造函数(或者除了第一个参数外其余参数都有默认值的多参构造函数)， 承担了两个角色。 1 是个构造器 ，2 是个默认且隐含的类型转换操作符。

所以， 有时候在我们写下如 AAA = XXX， 这样的代码， 且恰好XXX的类型正好是AAA单参数构造器的参数类型， 这时候编译器就自动调用这个构造器， 创建一个AAA的对象。

这样看起来好象很酷， 很方便。 但在某些情况下（见下面权威的例子）， 却违背了我们（程序员）的本意。 这时候就要在这个构造器前面加上explicit修饰， 指定这个构造器只能被明确的调用/使用， 不能作为类型转换操作符被隐含的使用。

explicit构造函数是用来防止隐式转换的。请看下面的代码：

	class Test1
	{
	public:
	    Test1(int n)
	    {
	        num=n;
	    }//普通构造函数
	private:
	    int num;
	};
	class Test2
	{
	public:
	    explicit Test2(int n)
	    {
	        num=n;
	    }//explicit(显式)构造函数
	private:
	    int num;
	};
	int main()
	{
	    Test1 t1=12;//隐式调用其构造函数,成功
	    Test2 t2=12;//编译错误,不能隐式调用其构造函数
	    Test2 t2(12);//显式调用成功
	    return 0;
	}

Test1的构造函数带一个int型的参数，代码23行会隐式转换成调用Test1的这个构造函数。而Test2的构造函数被声明为explicit（显式），这表示不能通过隐式转换来调用这个构造函数，因此代码24行会出现编译错误。

普通构造函数能够被隐式调用。而explicit构造函数只能被显式调用。

## 2.虚函数 ##
![](https://i.imgur.com/rLgBzuh.png)
