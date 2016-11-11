A *deinitializer* is called immediately before a class instance is deallocated.

一个类最多只能有一个析构函数。析构函数没有任何的参数，也不需要写小括号。

~~~swift
deinit{
    //perfrom the deinitialization
}
~~~

父类的析构函数会被子类继承，父类的析构函数在子类析构函数实现的结尾被自动调用。不论子类有没有提供自己的析构函数，父类的析构函数都会被调用。
