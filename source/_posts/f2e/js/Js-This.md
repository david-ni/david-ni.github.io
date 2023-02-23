# Js中的this

> `this`是在运行时进行绑定的，this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式

## 绑定规则

### 默认绑定

函数在独立调用时，`this`将被绑定到全局对象（浏览器环境指向`Window`），在严格模式下，`this`指向`undefined`

```js
function foo() {
    console.log( this );
}
foo(); // 输出 Window对象
```

### 隐式绑定

如果被调用函数存在上下文对象，即该函数被某个对象用`.`调用，则适用于隐式绑定。

```js
function foo() {
    console.log( this.a );
}
let obj = {
    a: 1,
    foo
};
obj.foo(); // output 1
```

对象引用链中只有最后一层会影响`this`的绑定

```js
function foo() {
    console.log( this.a );
}
let obj2 = {
    a: 2,
    foo
};
let obj1 = {
    a: 1,
    obj2
};
obj1.obj2.foo(); // output 2
```

### 显式绑定

通过`call`、`apply`和`bind`将函数绑定到特定对象上的方式，称之为显示绑定

```js
function foo() {
    console.log(this.a);
}
let obj = {
    a: 2
};
foo.call(obj); // 2
```

如果你把`null`或者`undefined`作为`this`的绑定对象传入`call`、`apply`或`bind`时，实际应用的时默认绑定规则

```js
function foo() {
    console.log(this.a);
}
foo.call(null); // undefined
```

### `new`绑定

使用`new`来实例化一个函数时，会执行以下步骤：

- 创建一个全新的对象
- 这个新对象会挂载`[[Prototype]]`连接
- 这个新对象会绑定到函数调用的`this`
- 如果函数没有返回其他对象，那么`new`表达式中的函数调用会自动返回这个新对象。

### 绑定优先级

new绑定 > 显示绑定 > 隐式绑定 > 默认绑定

## 隐式丢失

当进行隐式绑定时，如果进行引用赋值或者传参操作，会造成`this`的丢失，使`this`绑定到全局对象中

```js
function foo() {
    console.log( this.a );
}
let obj = {
    a: 1,
    foo
};
// 此时 func 引用的是 foo 函数本身
let func = obj.foo();

func(); // output undefined
```

`隐式丢失`的问题，可以通过`bind`来解决

```js
function foo() {
    console.log( this.a );
}
let obj = {
    a: 1,
    foo
};
// 此时 func 引用的是 foo 函数本身
let func = foo.bind(obj);

func(); // output 1
```

## `bind`的实现

```js
// mdn 的实现
if (!Function.prototype.bind) (function(){
  var ArrayPrototypeSlice = Array.prototype.slice;
  Function.prototype.bind = function(otherThis) {
    if (typeof this !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }

    var baseArgs= ArrayPrototypeSlice.call(arguments, 1),
        baseArgsLength = baseArgs.length,
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
          baseArgs.length = baseArgsLength; // reset to default base arguments
          baseArgs.push.apply(baseArgs, arguments);
          return fToBind.apply(
                 fNOP.prototype.isPrototypeOf(this) ? this : otherThis, baseArgs
          );
        };

    if (this.prototype) {
      // Function.prototype doesn't have a prototype property
      fNOP.prototype = this.prototype; 
    }
    fBound.prototype = new fNOP();

    return fBound;
  };
})();
```

## 参考

- 《你不知道的Javascript(上)》