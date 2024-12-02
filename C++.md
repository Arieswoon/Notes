<font style="background-color:yellow;">多态：</font>

1. <font style="background-color:yellow;">虚函数表和虚函数表指针</font>

对于含有虚函数的类，编译器会在编译阶段创建一个虚函数表vftable（按照虚函数的声明顺序保存虚函数的地址），并在创建类对象时插入一个虚函数表指针vfptr，该指向vftable的首地址（也就是第一个虚函数的地址）。 虚函数表是静态的，供该类的所有对象共享，在编译期确定，存放在全局（静态）变量区的.rodata段。 虚函数表指针是动态的，该类的每个对象都有一个，在运行期确定，存放位置和对象的存放位置相同（堆区/栈区）。 继承时，子类会深拷贝一份父类的虚函数表，如果子类重写了虚函数，那么子类的虚函数表中对应的虚函数地址会被覆盖为重写的虚函数地址。

2. <font style="background-color:yellow;">多态为什么函数传参是传指针或者引用</font>，按值传大概率不行

按值传递会导致切片问题，会导致丢失派生类的信息，从而无法实现多态；

按值传递需要拷贝副本，性能开销大；

3. <font style="background-color:yellow;">构造函数和析构函数可以定义为虚函数</font>吗

在继承中，先构造基类再构造子类。如果构造函数是虚函数，则需要通过虚函数表调用，但是此时对象还没有实例化，无法访问虚函数表若父类不是虚析构，使用父类指针指向子类，就只会调用父类的析构函数（子类的析构函数在父类中不存在，父类不能够调用子类的析构函数）没法按照子类->父类的顺序进行析构 

4. 哪些函数<font style="background-color:yellow;">不能是虚函数</font>

构造函数、静态成员函数（编译器确定，不能动态绑定）、友元函数（不能继承）、内联函数（编译器展开，不能动态绑定）

5. <font style="background-color:yellow;">静态多态</font>

<font style="background-color:yellow;">函数重载</font>：c++中编译器会根据函数参数数量、类型和顺序的不同，生成不同的函数签名，在调用该函数时，编译器会根据传递给函数的参数类型和数量来选择匹配的函数。<font style="background-color:yellow;">泛型</font>：包括类模版，函数模版等，一份抽象的模版代码可以根据参数类型实例化出多份不同的代码。

6. <font style="background-color:yellow;">拷贝构造/赋值函数</font>

默认构造函数、有参构造函数、拷贝构造函数（传参必须是引用方式，否则会无穷递归，因为按值传递时候会继续调用拷贝构造函数）

7. <font style="background-color:yellow;">移动构造</font>/<font style="background-color:yellow;">赋值函数</font>

移动构造函数（必须要加noexcept,否则如vector之类的在扩容时不会调用移动构造，只会调用拷贝构造）

将资源的所有权从一个对象转移到另一个对象

8. <font style="background-color:yellow;">菱形继承</font>

虚继承，关键字virtual

9. <font style="background-color:yellow;">overload和override</font>的区别

overload是同一作用域内有多个具有相同名称但参数列表不同的函数，编译时多态或静态多态。override是派生类函数重写基类的虚函数，运行时多态或动态多态

10. <font style="background-color:yellow;">深拷贝与浅拷贝</font>

浅拷贝：仅复制对象的基本数据类型和指针成员的值，但是不会赋值指针指向的内存，可能导致两个对象共享相同内存；

深拷贝：不仅拷贝对象的基本数据类型和指针成员的值，还会拷贝指针所指向的内存。

11. <font style="background-color:yellow;">this指针</font>

this指针是指向当前对象的一个隐式指针，可以在成员函数内部使用它来引用对象的成员。

<font style="background-color:yellow;">关键字：</font>

12. <font style="background-color:yellow;">const</font>

修饰变量：只读变量，不能修改

修饰函数：修饰函数的返回值，表示函数的返回值只读，不能被修改；

修饰指针：常量指针（int* const）、指向常量的指针（const int*）

修饰成员函数：不能修改任何成员变量，因此也只能调用常成员函数

13. <font style="background-color:yellow;">sizeof</font>

sizeof 是一个用于获取数据类型或对象的大小（占用的字节数）的运算符。

14. <font style="background-color:yellow;">volatile</font>

<font style="background-color:yellow;">防止编译器优化</font>

<font style="background-color:yellow;">指示变量可能被外部因素更改</font>

场景：多任务环境下各任务间共享的标志应该加 volatile；存储器映射的硬件寄存器通常也要加 volatile 说明，因为每次对它的读写都可能有不同意义

15. <font style="background-color:yellow;">define</font>和<font style="background-color:yellow;">inline</font>

define宏定义只在预处理阶段起作用，仅仅是简单的文本替换，没有类型检查

inline在编译阶段进行替换，有类型检查

16. 为什么使用内联函数<font style="background-color:yellow;">inline</font>

减少函数调用开销，函数调用涉及压栈、传参、弹栈，内联展开避免了这些开销，直接在调用点插入函数体；

减少跳转和缓存不命中

17. <font style="background-color:yellow;">递归函数</font>在<font style="background-color:yellow;">栈使用</font>上可能出现什么问题

如果递归函数没有终止条件或者深度过大，会导致栈溢出

18. <font style="background-color:yellow;">extern</font>

声明外部变量或函数，使得不同的源文件共享相同的变量或者函数

extern “C”：可以让 C++ 来调用 C 语言当中的变量或函数。

19. 4种<font style="background-color:yellow;">类型转换</font>

<font style="background-color:yellow;">static_cast：</font>基本数据类型间转换，如int转为double，也可以用于基类和派生类间的上行转换（派生类指针—>基类指针）

<font style="background-color:yellow;">dynamic_cast：</font>主要用于基类和派生类间的安全类型转换，运行时执行安全检查，要求基类必须有虚函数。流程：首先，dynamic_cast 通过查询对象的 vptr 来获取其 RTTI；然后，dynamic_cast 比较请求的目标类型与从 RTTI 获得的实际类型。如果目标类型是实际类型或其基类，则转换成功。如果目标类型是派生类，dynamic_cast 会检查类层次结构，以确定转换是否合法。如果在类层次结构中找到了目标类型，则转换成功；否则，转换失败。<font style="background-color:yellow;">const_cast：</font>去const属性，常量指针转换为非常量指针，并且仍然指向原来的对象

<font style="background-color:yellow;">reinterpreter_cast：</font>仅仅重新解释类型，但没有进行二进制的转换

20. <font style="background-color:yellow;">struct和union区别</font>

struct：每个成员有自己的内存空间，结构体的总大小是各成员大小之和，可以安全地访问所有成员；

union：所有成员共享同一块内存空间，大小等于最大成员的大小。

21. <font style="background-color:yellow;">struct和class区别</font>

class 中类中的成员以及继承默认都是 private 属性的。而在 struct 中结构体中的成员和继承默认都是 public 属性的。 class 可以用于定义模板参数，struct 不能用于定义模板参数。

22. <font style="background-color:yellow;">static</font>

修饰全局变量：将变量作用域限制在当前文件中，其他文件无法访问；

修饰局部变量：静态局部变量在函数调用结束后并不会被销毁，而是保留其值，并在下一次调用该函数时继续使用；

修饰成员变量：成为静态成员变量，属于整个类，在所有实例之间共享；

修饰成员函数：静态成员函数，可以直接通过类名来访问，而无需创建对象，没有this指针。

23. <font style="background-color:yellow;">New/delete和malloc/free</font>区别 

语法不同：malloc/free 是一个 C 语言的函数，而 new/delete 是 C++ 的运算符。分配内存的方式不同：malloc 只分配内存，而 new 会分配内存并且调用对象的构造函数来初始化对象。返回值不同：malloc 返回一个 void 指针，需要自己强制类型转换，而 new 返回一个指向对象类型的指针。malloc 需要传入分配的大小，而 new 编译器会自动计算所构造对象的大小

24. <font style="background-color:yellow;">noexcept</font>

表示函数内部不会抛出异常，有助于简化调用该函数的代码，而且编译器确认函数不会抛出异常，它就能执行某些特殊的优化操作。

25. <font style="background-color:yellow;">mutable</font>

mutable只能用来修饰类的成员变量，表示可以在const成员函数中修改。<font style="background-color:yellow;">STL：</font>

26. <font style="background-color:yellow;">stl容器</font>及其实现：

<font style="background-color:yellow;">array</font>：静态数组，不可扩充，内存连续，栈上存储，大小需要在编译期确定<font style="background-color:yellow;">vector</font>：动态数组，空间不够时会重新分配内存；内存连续，堆上数组<font style="background-color:yellow;">deque</font>：双端数组，可对头部和尾部进行插入和删除的操作，也是动态数组；内部有个中控器，维护每个缓冲区的地址，真实的数据存在缓冲区内，缓冲区内部是连续的，但是缓冲区之间是分散的，所以访问速度慢； 

<font style="background-color:yellow;">list</font>：双向循环链表，可以对任意位置进行快速插入或删除元素，动态存储分配，遍历速度较慢。

27. <font style="background-color:yellow;">vector扩容</font>

申请2倍于现有大小的内存空间，将现有元素调用拷贝构造函数到新内存上，对原空间上的元素调用析构函数，并释放原有空间

28. <font style="background-color:yellow;">push_back()</font>与<font style="background-color:yellow;">emplace_back()</font>的区别

push_back()先创建临时对象，然后将临时对象拷贝到容器中，最后销毁临时对象;emplace_back()直接在容器中就地构造对象

29. <font style="background-color:yellow;">set/map</font>

用红黑树实现，插入时自动排序。红黑树是一种自平衡的二叉搜索树，兼顾了插入时和搜索时的效率，插入、删除查找的复杂度都是O(logn),相比严格平衡的AVL树，红黑树是弱平衡的，插入最多旋转2次，删除最多旋转3次。

30. <font style="background-color:yellow;">unordered_set/unordered_map</font>

哈希表/散列表，插入的元素是无序的，不允许容器中有重复的元素；查找的复杂度是O(1)；数据插入和查找的时间复杂度很低，而代价是占用比较多的内存；

31. <font style="background-color:yellow;">哈希表</font>内部原理

哈希桶(buckets)：存放key值不同，但哈希值相同的容器（一般是链表）；如何避免单个哈希桶的元素过多？当元素大于阈值后可以rehash，或者把链表换成红黑树or哈希表 ；哈希桶向量(buckets vector)：用于存放哈希桶的指针 节点(node)：用于存放key-value 桶大小，节点数量：表示当前一共有几个桶，一共有几个节点 哈希冲突处理方法：开放寻址，链地址法，拉链法

32. <font style="background-color:yellow;">stl线程安全</font>

不是线程安全的，单线程写和多线程读，写的时候可能会导致读的迭代器失效。多线程写更有可能

<font style="background-color:yellow;">C++11新特性</font>

33. <font style="background-color:yellow;">auto</font>与<font style="background-color:yellow;">decltype</font>

auto：编译器在编译期自动推导变量类型decltype：编译器在编译期自动推导表达式类型

34. <font style="background-color:yellow;">lambda</font>表达式

lambda表达式本质就是一个仿函数。它通过编译器生成一个匿名类的对象来实现，这个匿名类重载了函数调用运算符 operator()，使得我们可以像调用普通函数一样来调用lambda表达式。 

[capture list] (params list) mutable exception -> return_type { function body }

35. <font style="background-color:yellow;">右值引用、移动语义、完美转发</font>

可以取地址的、有名字的是左值，不能取地址、没有名字的是右值；

移动语义：允许资源（如动态内存）从一个对象转移到另一个对象的机制 move（将左值强制转化为右值引用）完美转发：允许函数将参数以几乎完全相同的形式转发给其他函数，保证参数的左值或右值特性被保留 forword

36. <font style="background-color:yellow;">智能指针</font>

RAII：资源获取即初始化，资源获取应该在构造函数中进行，资源释放应该在析构函数中进行。

智能指针：一种资源管理工具，用于管理动态分配的内存，避免内存泄漏。

<font style="background-color:yellow;">unique_ptr</font>：对一个对象独占所有权，不与其他unique_ptr共享，不支持拷贝

<font style="background-color:yellow;">shared_ptr</font>：多个shared_ptr可以共享同一个对象，通过引用计数来跟踪有多少个 shared_ptr 指向该资源，引用计数为0时自动释放。

底层实现：shared_ptr 的实现是采用引用计数的原理，它除了有一个指向数据的原始指针外，还有一个指向资源控制块（包括引用计数值，次级引用计数，删除器等）的指针。

<font style="background-color:yellow;">weak_ptr</font>：弱引用的智能指针，不参与管理对象的生命周期，解决循环引用的问题。

37. <font style="background-color:yellow;">shared_ptr是否线程安全</font>

多线程对同一个引用计数增加或减少是安全的，原子性

多线程对shared_ptr指向的资源进行读写不是线程安全的，需要额外的同步机制

<font style="background-color:yellow;">内存管理：</font>

38. <font style="background-color:yellow;">内存模型</font>，地址由低到高：

<font style="background-color:yellow;">代码段：</font>存放程序的二进制机器码

<font style="background-color:yellow;">数据段data：</font>存放初始化的全局变量、静态变量和常量

<font style="background-color:yellow;">BSS段：</font>存放未初始化的全局变量和静态变量

<font style="background-color:yellow;">堆区：</font>由程序员分配和释放，所有malloc/free，new/delete都在堆上进行操作。会造成内存泄漏。由于堆区存在内存管理、地址映射等复杂操作，故效率较低。此外还会产生内存碎片影响效率。但空间大

<font style="background-color:yellow;">栈区：</font>存放局部变量、函数参数值、形参等。由编译器分配和释放，栈区只需要改变栈指针的位置，因此效率高。但栈区空间小

39. C++<font style="background-color:yellow;">代码到程序执行</font>过程

<font style="background-color:yellow;">预处理</font>：主要处理#include指令，宏替换，条件编译等，生成.i文件

<font style="background-color:yellow;">编译</font>：对源代码进行语法分析、词法分析，生成汇编代码，产生.s文件，

<font style="background-color:yellow;">汇编</font>：将汇编代码翻译成机器码，生成.o目标文件

<font style="background-color:yellow;">链接</font>：将目标文件和库文件链接在一起，生成最终的可执行文件

40. <font style="background-color:yellow;">动态库</font>与<font style="background-color:yellow;">静态库</font>

<font style="background-color:yellow;">动态库</font>中的代码和数据是运行时加载到进程中的，而不是链接时静态编译到可执行文件中的。当动态库被关闭时（动态库代码从内存中释放），对应的实例无法继续使用。<font style="background-color:yellow;">静态库</font>中的代码和数据是链接时静态编译到可执行文件中的，而不是运行时加载到进程中的。

41. <font style="background-color:yellow;">内存泄漏</font>

申请了一块内存空间，使用完毕后没有释放掉。

检测方法：采用RAII思想。

使用GDB，执行某个函数前后，call malloc_stats()，对比in use bytes大小是否变化

使用valgrind，valgrind --leak-check=yes program arg1 arg2

42. <font style="background-color:yellow;">指针</font>和<font style="background-color:yellow;">引用</font>

指针是一个变量，它保存了另一个变量的内存地址；引用是另一个变量的别名，与原变量共享内存地址。 指针可以被重新赋值，指向不同的变量；引用在初始化后不能更改，始终指向同一个变量。 指针可以为 nullptr，表示不指向任何变量；引用必须绑定到一个变量，不能为 nullptr。 使用指针需要对其进行解引用以获取或修改其指向的变量的值；引用可以直接使用，无需解引用。

43. <font style="background-color:yellow;">空指针</font>可以<font style="background-color:yellow;">访问成员函数</font>吗

如果用到this指针（即访问成员变量），则不可以，否则可以

