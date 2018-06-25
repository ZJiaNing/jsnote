# Promise 对象
promise这个东西吧，它一直困扰我很久很久了，但是之前未能静下心来好好研究下，现在终于有时间好好鼓捣一下了。<br/>
顺带记录下吧~~

>* 特点
>* then的链式调用
>* Promise.all()

> The Promise object represents the eventual completion (or failure) of an asynchronous operation, and its resulting value.

> **一种异步编程的解决方案**<br/>
> 有了Promise对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数<br/>
> then方式是定义在Promise.prototype上的，所以Promise实例就能调用这个方法，添加状态改变时的回调函数了

## 特点

* 对象的状态不受外界影响<br/>
`Pending => Resolve`<br/>
`Pending => Fulfilled`
* 一旦状态改变就不会再变，任何时候都可以得到这个结果

## then 的 链式调用

### 第一个回调函数返回正常值

使用Mock模拟接口，定义了两个接口
```js
Mock.mock('http://g.cn', {
    'data|0-10': [{
        'id|+1': 1,
        'thscode|1-100.2': 1,
        'zqjc': Mock.Random.csentence(5)
    }]
});

Mock.mock('http://q.cn', {
    'data|0-10': [{
        'id|+1': 1,
        'jgcg': Mock.Random.csentence(5)
    }]
});
```

获取ajax的通用函数，接收url地址作为参数
```js
function getAjax(url){
  return new Promise(function(resolve, reject) {
    axios.get(url)
    .then(function(response){
      resolve(response);
      console.log('ajax 1:', response);
    })
    .catch(function(err){
      reject(err);
      console.log(err);
    });
  });
}
```

下面的代码使用then方法依次指定了两个回调函数。<br/>
第一个回调函数完成后，会将返回结果<br/>
作为第二个回调函数的参数传入<br/>
通过运行结果即可知
```js
function getIndexData(){
  getAjax('http://g.cn').then(function(res) {
    console.log('第一个then: ', res);

    return res.data;
  })
  .then(function(res) {
    console.log('第二个then: ', res);
  }).catch(err => {
    console.log(err);
  })
}

getIndexData()
```

### 第一个回调函数返回的仍旧是一个promise
如果是这样，也就是还存在异步，则第二个回调的执行就需等待第一个执行完毕，不管是resolve还是reject
```js
function getAjax2(url){
  return new Promise(function(resolve, reject) {
    axios.get(url)
    .then(function(response){
      setTimeout(() => {
        resolve(response);
      }, 2000);

      console.log('ajax 2:', response);
    })
    .catch(function(err){
      reject(err);
      console.log(err);
    });
  });
}

function getIndex2Data() {
  getAjax('http://g.cn').then(res => {
    return getAjax2('http://q.cn');
  })
  .then(res => {
    console.log('in the data: ', res);
  })
  .catch(err => {
    console.log(err);
  });
}

getIndex2Data();
```

## Promise.all()

如下代码，定义了p1、p2， 和p<br/>
**p的状态是由p1和p2决定的**
- 只有p1和shup2的状态都变成了Fulfilled，p的状态才会变成Fulfilled，此时p1和p2的返回值组成一个数组，传递给p的回调函数；
- 只要p1、p2的状态中有一个被Rejected，p的状态就变成Rejected，此时第一个被Rejected的实例的返回值会传递给p的回调函数

### #1
均为p1和p2增加了catch函数，则，如果p1或者p2发生reject或者其它异常，那么会由自己的catch函数捕获，等到执行完catch，实例的状态也变成了resovled
```js
var p1 = new Promise((resolve, reject) => {
  resolve('one corgi');
})
.then(res => res)
.catch(err => err)

var p2 = new Promise((resolve, reject) => {
  resolve('two corgies');
})
.then(res => res)
.catch(err => err)

var p = Promise.all([p1, p2]);

p.then(res => {
  console.log(res);
})
.catch((err) => {
  console.log('all:', err);
});
// 输出： ["one corgi", "two corgies"]
```

### #2
增加p3，不带catch，则最终的就会被p的catch捕获了
 ```js
 var p3 = new Promise((resolve, reject) => {
   reject('Error');
 })
 .then(res => res)

 var p = Promise.all([p1, p2, p3]);

 p.then(res => {
   console.log(res);
 })
 .catch((err) => {
   console.log('all:', err);
 });

 //输出： ["all: Error]
 ```
