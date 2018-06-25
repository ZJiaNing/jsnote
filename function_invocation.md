# 函数调用模式

在Javascript中，一共有四种调用模式，这些模式在如何初始化this上是存在差异的

* 方法调用模式
* 函数调用模式
* 构造器调用模式
* apply调用模式

## 方法调用模式 method invocation pattern
当一个函数被保存为一个对象的属性时，称其为方法。当这个方法被调用时，this被绑定到该对象上，即指向该对象。

```js
var user = {
    name: "Ada",
    age: "25",
    setName: function(name) {
        this.name = name;
    },
    getName: function() {
        return this.name;
    }
}

user.setName('Bob');
console.log(user.name); //Bob
```

## 函数调用模式 function invocation pattern
当一个函数并非一个对象的属性时，那么它被当作一个函数来调用。而此时this会被绑定到全局对象上，即window。但是严格模式下是undefined。

## 构造器调用模式 constructor invocation pattern


## apply调用模式 apply invocation pattern
也即环境调用模式，在不同环境的不同调用模式

### apply
> fun.apply(thisArg, [argsArray])

**thisArg** fun运行时指定的this<br/>
**argsArray** 可选的。一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 fun 函数


### call
> fun.call(thisArg, arg1, arg2, ...)<br/>

**thisArg** fun运行时指定的this<br/>
**arg1,arg2,...**  指定的参数列表

emmmm...继承，不喜欢这样子的形式
```js
function Car(name, price) {
    this.name = name;
    this.price = price;
}

function AutoCar(name, price, country) {
    // 原型链结构不会变化，同时实现了继承的效果
    Car.call(this, name, price); // 借用Car构造方法
    this.country = country;
}

var car = new AutoCar('little car', '1000', 'England');
console.log(car);   // {name: "little car", price: "1000", country: "England"}
```

下面的这个例子(来自MDN)能更直观的体现出，call改变了函数运行时this的指向
```js
function greet() {
  var reply = [this.person, 'Is An Awesome', this.role].join(' ');
  console.log(reply);
}

var i = {
  person: 'Douglas Crockford', role: 'Javascript Developer'
};

greet.call(i); // Douglas Crockford Is An Awesome Javascript Developer
```

>  哈哈，代码还是需要花点时间，慢慢阅读并辅之思考，才能贯通全文，最终理解掌握的。
