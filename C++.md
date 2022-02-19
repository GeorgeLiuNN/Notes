c++ tips

# 问题

1. 智能指针，有哪几种智能指针，以及各自的使用场景，智能指针如何作为参数进行传递，传参以及赋值的时候智能指针的生命周期会不会变化
2.  C++ move 语义的使用场景，以及为何引入 move 语义
3. 函数传参的几种方式，传值、传引用、传指针、传智能指针、传右值引用
4. 线程的使用（sleep、yield、join、detach），以及异步调用 async、promise、future、packaged_task 等的使用场景
5. lambda 表达式以及闭包的使用，lambda 表达式中变量的生命周期，如何使用 lambda 外部定义的变量和 this 指针
6. C++ 中时间的使用方法，std::chrono 包中关于当前时间、duration、duration_cast 的使用，system_clock 与 steady_clock 的差异
7.  C++ 中的同步原语，mutex、recursive_mutex、condition_variable、spinlock、atomic 各自的使用场景，锁的管理 lock_guard、scoped_lock、unique_lock 的使用以及各自的差异
8. 标准库中的种类容器的使用，list、vector、queue、map、unordered_map 等，以及各自的线程安全性
9. 字符编码，UTF-8、GB2312、ANSI 编码集、UTF-16 等编码方式，std::string 与 std::wstring 的差异，如何相互转换
10.  几种构造函数的差异，默认构造函数、拷贝构造函数、赋值构造函数、移动构造函数



# 参考

* [C++ reference](https://en.cppreference.com/w/cpp)
* c++ primer
* c++ 并发编程实战
* [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)
* [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
* 共享智能
  * 循环引用导致资源不能释放 -- std::weak_ptr<>
  * 不同的共享指针共享相同的资源导致重复释放 -- 不要混合使用 std::make_shared<>() 与 new
  * std::shared<>(this) 管理 this 指针导致资源重复释放 -- 继承 : public std::enable_shared_from_this<>()；return shared_from_this();
* lambda 表达式
  * 谓词 predicate，一个可调用的表达式，其返回的结果是能用作条件的值
  * 标准库的算法使用一元谓词和二元谓词
  * lambda 表达式的引入可以解决谓词个数的限制
  * 可调用对象包括函数、函数指针、重载了函数调用运算符的类和 lambda 表达式
  * [捕获列表] (参数列表) -> 返回类型 { 函数体 }
  * 捕获只能针对于在创建lambda式的作用域内可见的非静态局部变量(包括形参)
  * 值捕获，与参数不同，**被捕获的变量的值是在lambda创建时拷贝的，而不是调用时拷贝的**
  * 引用捕获，注意悬空引用
  * 隐式捕获，& 和 =
  * 闭包 closure，将函数及其函数调用的环境组成在一起的实体

* 条件变量
  * std::condition_variable，等待中的线程在等待期间必须解锁互斥元，并在之后将其锁定
  * std::unique_lock\<std::mutex>
  * std::lock_guard\<std::mutex>
  * std::mutex


# Expression templates

[wiki](https://en.wikipedia.org/wiki/Expression_templates)



# C Language

* [数组声明](https://en.cppreference.com/w/c/language/array)
  * 定长数组
  * 变长数组
  * 未知长度数组
  
  **If an array type is declared with a const, volatile, or restrict (since C99) qualifier (which is possible through the use of typedef), the array type is not qualified, but its element type is:**
* [Declarations](https://en.cppreference.com/w/c/language/declarations)
* [Type](https://en.cppreference.com/w/c/language/type)
* offsetof
* typeof
* container_of

