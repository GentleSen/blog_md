#### call, apply, bind 的实现

- call 的实现：
```javascript
Function.prototype.myCall = function() {
    let [ obj, [...params] ] = arguments;
    obj = obj ? obj : window;
    obj.fun = this;
    obj.fun(...params);
    delete obj.fun;
}
```

- apply 的实现：
```javascript
Function.prototype.myApply = function(obj, paramsArr) {
    obj = obj || window;
    obj.fun = this;
    obj.fun(...paramsArr);
    delete obj.fun;
}
```

- bind 方法的实现：
```javascript
// bind 方法实现
Function.prototype.myBind = function(obj) {
    if (typeof this !== 'function') {
        throw new Error('必须使用函数来调用 myBind 方法');
    }
    var args = Array.prototype.slice.call(arguments, 1);
    var that = this;
    var noop = function(){};
    var bindFun = function() {
        // this instanceof bindFun 判断是否是使用 new 调用 bindFun 函数
        // 此处的 arguments 指得是调用 bindFun 时传入的参数
        return that.apply(this instanceof bindFun ? this : obj, args.concat(Array.prototype.slice.call(arguments)));
    }
    // 若调用 myBind 的函数有 prototype 属性, 则生成的 bindFun 函数也应该有原来的 prototype 属性
    // 维护原型关系, 实现方法跟继承相同
    if (this.prototype) {
        noop.prototype = this.prototype;
    }
    bindFun.prototype = new noop();
    return bindFun;
}
```

> bind 方法实现参考 MDN 上 bind 方法的 [Polyfill](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 部分