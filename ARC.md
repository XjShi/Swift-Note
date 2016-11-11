##Resolving Strong Reference Cycles Between Class Instance
Swift提供两种方式来解决强引用环：weak reference、unowned reference(这个词就不翻译了，在后天的讨论中，我直接简称为“UR”)。

任何时候，如果一个引用在它的生命周期内可能为nil，那么就是用弱引用。相反，如果从来不可能为nil，就是用UR。

###Weak Reference
因为弱引用不保持它指向的实例的strong hold，所以有可能在扔有弱引用指向实例时，实例被释放。因此，ARC在弱引用指向的对象被释放时，自动把弱引用设置为nil。

**注意：当ARC把弱引用设置成nil的时候，属性观察者不会被调用。**

###Unowned Reference
跟弱引用类似，UR不持有它指向对象的strong hold，但是，UR被认为一直有值。因此，一个UR总是被定义为nonoptional type。

ARC在UR指向的实例被释放时，不能自动把UR设置成nil。

###Unowned References and Implicitly Unwrapped Optional Properties
两个引用在初始化完成后都应该一直有值的情况。

~~~swift
class Country {
    let name: String
    var capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}
 
class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}
~~~

##Strong Reference Cycles for Closures
如果你把闭包赋值给一个类实例的属性，闭包体中又捕获了这个类实例，那么就会有强引用环存在。

Swift提供了一个优雅的方式来解决这个问题，叫做*closure capture list*。

##Resolving Strong Reference Cycles for Closures
解决类实例与闭包之间强引用环的方法是把capture list作为闭包定义的一部分。一个capture list定义当在闭包内捕获一个或多个引用类型时使用的规则。

###Defining a Capture List
Each item in a capture list is a pairing of the `weak` or `unowned` keyword with a reference to a class reference (such as `self`) or a variable initiazlied with some value (such as delegate = self.delegate!). These pairings are written within a pair of square braces, separated by commas.

Place the capture list before a closure's parameter list and return type if they are provided:

~~~swift
lazy var someClosure: (Int, String) -> String = {
    [unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
    // closure body goes here
}
~~~

If a closure does not specify a parameter list or return type because they can be inferred from context, place the capture list as the very start of the closure, followed by the `in` keyword:

~~~swift
lazy var someClosure: () -> String = {
    [unowned self, weak delegate = self.delegate!] in
    // closure body goes here
}
~~~

###Weak and Unowned Reference
Define a capture in a closure as an unowned reference when the closure and the instance it captures will always refer to each other, and will always be deallocate at the same time.

Conversely, define a capture as a weak reference when the captured reference may become nil at some point in the future. Weak references are always of an optional type, and automatically become nil when the instance they reference is deallocated. This enables you to check for their existence within the closure’s body.

~~~swift
class HTMLElement {
    
    let name: String
    let text: String?
    
    lazy var asHTML: () -> String = {
        [unowned self] in
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }
    
    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
    
}
~~~