Extension可以给一个已经存在类、结构体、枚举或者协议添加新的功能。

Swift中的Extension可以：

* 添加computed instance property和computed type property；
* 定义isntance method和type mothod；
* 提供新的构造函数；
* 定义subscripts；
* 定义并使用新的nested type；
* 让一个已存在的类型遵从协议；

注意，extension可以给一个类型添加新功能，但是不能覆盖已经存在的功能。

##Extension Syntax
声明extension：

~~~swift
extension SomeType {
	//
}
~~~

使用extension让原来的类型遵从协议。

~~~swift
extension SomeType: SomeProtocol, AnotherProtocol {
    //implementation of protocol requirements goes here
}
~~~

##Computed Property
利用extension可以添加computed instance property和computed type property，看个例子：

~~~swift
extension Double {
    var km: Double {
        return self * 1_000.0
    }
    var m: Double {
        return self
    }
    var cm: Double {
        return self / 100.0
    }
    var mm: Double {
        return self / 1_000.0
    }
    var ft: Double {
        return self / 3.28084
    }
}

let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
~~~

##Initializers
extension可以给已存在的类型添加新的构造方法。

extension可以给一个类类型添加便利构造方法，但是不能给类类型添加指定构造方法和析构方法。指定构造方法和析构方法必须由类本身的实现来提供。

**如果一个值类型本身已经给自己所有的存储属性提供了默认值，而且没有任何自定义的构造方法，那么如果你使用extension给类添加了构造方法，你可以在扩展中定义的构造方法中调用默认构造方法和逐成员初始化构造方法。**我们来改写一下构造方法那一章的例子：

~~~swift
struct Size {
    var width = 0.0, height = 0.0
}

struct Point {
    var x = 0.0, y = 0.0
}

struct Rect {
    var origin = Point()
    var size = Size()
}

extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - size.width / 2
        let originY = center.y - size.height / 2
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
~~~

##Methods
extension可以给一个已经存在的类型添加实例方法和类方法。看个例子：

~~~swift
extension Int {
    func repetitions(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}

3.repetitions {
	print("Hello!")
}
~~~

###Mutating Instance Methods
在extension中添加的实例方法可以修改实例本身。对于结构体和枚举类型，如果实例方法要修改实例本身，需要用`mutating`修饰。看个例子：

~~~swift
extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
~~~

##Subscript
extension可以给一个已存在的类型添加新的下标。看个例子：

~~~swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}
~~~

##Nested Types
extension可以给已经存在类、结构体、枚举添加新的嵌套类型。看个例子：

~~~swift
extension Int {
    enum Kind {
        case negative, zero, positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .zero
        case let x where x > 0:
            return .positive
        default:
            return .negative
        }
    }
}

func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
~~~