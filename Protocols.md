A *protocol* defines a blueprint of methods, properties, and other requirments that suit a particular task or piece of functionality. The protocol can be dopted by a class, structure, or enumeration to provide an actual implementation of those requirements.

In addition to specifying requirements that conforming types must implement, you can extend a protocol to implement some of these requirements or to implement additional functionality that conforming types can take advantage of.

##Protocol Syntax
~~~swift
protocol SomeProtocol {
	//
}
~~~

遵从协议：

~~~swift
struct SomeStructure: FirstProtocol, AnotherProtocol {
	//structure definition goes here
}
~~~

如果一个类有父类，把父类的名字放在协议名字的前边：

~~~swift
class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
~~~

##Property Requirements
一个协议可以要求任何遵从该协议的类型提供指定名称和类型的实例属性或者类型属性。协议不指定这个属性应该是存储属性还是计算属性，它值指定属性的名字和类型。协议也指定每个属性是只读的，还是可读可写的。

需要的属性总是被声明为变量（var），可读写性由`get`和`set`决定。看个例子：

~~~swift
protocol SomeProtocol {
    //可读可写
    var mustBeSettable: Int {
        get set
    }
    //只读
    var doesNotNeedToBeSettable: Int {
        get
    }
}

protocol AnotherProtocol {
    //可读可写类型属性
    static var someTypeProperty: Int {
        get set
    }
}
~~~

##Method Requirements
包括实例方法、类型方法。允许可变参数，不能有默认值。看个例子：

~~~swift
protocol SomeProtocol {
	//类型方法
	static func someTypeMethod()
}

protocol RandomNumberGenerator {
	//实例方法
	func random() -> Double
}
~~~

##Mutating Method Requirements
直接看例子：

~~~swift
protocol Togglable {
    mutating func toggle()
}

enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
            self = .on
        case .on:
            self = .off
        }
    }
}
var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
~~~

##Protocols as Types
协议本身不实现任何功能，