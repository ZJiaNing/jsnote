
# 闭包-closure

> * 词法作用域
> * 创建闭包
> * 循环中的闭包


## 词法作用域
> 在javascript中，每个函数都有一个自己的词法作用域，也就是说，每个函数在被定义时（而非执行时），都会创建一个属于自己的环境，通过源码阅读就可以知道，并且不会改变。

## 创建闭包

### 1. 升级全局变量
```js
function f() {
  var a = "a";

  return function() {
    return a;
  }
}

var b = f();
b();   // a
```

上述代码将f()的返回值，赋值给了全局变量b，则b()就可以访问f()的私有空间，且同时也是全局函数。

### 2. 直接在函数体内创建一个新的全局函数
```js
var c;
function f(){
  var a = "a";

  c = function() {
    return a;
  }
}

f();
c();   // a
```

### 3. 返回父级函数的参数
```js
function f(arg){
  var a = function() {
    return arg;
  }

  arg++;
  return a;
}

var c = f(12);
c(); // 13
```
由于函数通常会将自身的参互视为局部变量，因此我们创建返回函数时，也可以令其返回父级函数的参数。<br/>
`在一个方法中，同名的实参，形参和变量之间是引用关系，也就是JS引擎的处理是同名变量和形参都引用同一个内存地址`

## 循环中的闭包
```js
function f(){
  var a = [];
  var i;

  for (i=0;i<3;i++){
    a[i] = function() {
      return i;
    }
  }

  return a;
}

var c = f();
c[0]();  // 3
c[1]();  // 3
c[2]();  // 3
```
预期想要依次输出0、1、2， 但是实际运行显然和预期不符呐，为啥呢？<br/>
因为闭包中的变量i，是父亲上的，即f函数的，也即共同的局部变量，执行完for循环后i的值为3，所以大家都是3了。<br/>
`PS: i逐渐递增，直至不小于跳出循环，所以最终i的值是3`

如果这样做,将i传递给一个自调函数，则在函数中，i就会被赋值给局部变量x，每次迭代x就会拥有不同的值了
```js
function f(){
  var a = [];
  var i;

  for(i=0;i<3;i++){
    a[i] = (function(x){
      return function() {
        return x;
      }
    })(i);
  }

  return a;
}

var c = f();
c[0]();  // 0
c[1]();  // 1
c[2]();  // 2
```
