---
layout: post
title:  "7行Javascript代码的异步函数库!"
date:   2015-07-01 10:48:14
categories: javascript async
---

假设我们有两个函数，一个接着一个调用。这两个函数都操作同一个变量，第一个函数赋值给变量， 第二个函数读取打印出来。

{%highlight javascript %}

var value;
var A = function() {
  setTimeout(function() {
    value = 10;
  }, 200);
}
var B = function() {
  console.log(value);
}
{% endhighlight %}

当我们调用 `A();B();`时，控制台打印出来是 `undefined`。 这是由于我们A函数里面赋值语句是异步的。因此我们传递一个回调函数B给A函数， 当A函数真正执行完成后，在调用callback B函数。

{%highlight javascript %}

var value;
var A = function(callback) {
  setTimeout(function() {
    value = 10;
    callback();
  }, 200);
};
var B = function() {
  console.log(value);
};

A(function() {
  B();
});

{% endhighlight %}

显然这样能够达到我们的目的， 但是当我们有很多的回调函数需要处理时，嵌套的结构就会导致代码可读性很差。


为了解决这个问题， 我们先从简单的开始，定义一个函数接收用户的所有处理函数。
{%highlight javascript %}

var queue = function(funcs) {
      // magic here
}
{% endhighlight %}


然后，我们把A，B两个函数构造一个数组传递进来， 在queue函数里面第一个函数A并执行它。

{%highlight javascript %}
var queue = function(funcs) {
      var f = funcs.shift();
      f();
}

{% endhighlight %}

当你上面的代码运行时，你会看到程序发生了一个错误 `TypeError: undefined is not a function`。 这是由于我们的函数A调用时，没有传递一个回调函数的参数。因此我们改一下，传递一个空函数给它。

{%highlight javascript %}
var queue = function(funcs) {
    var next = function() {
      // ...
    };
    var f = funcs.shift();
    f(next);
};

{% endhighlight %}

这样当A函数执行完成后next函数就会接着执行，我们改动下程序，使之能够处理整个数组里面的所有函数。


{%highlight javascript %}
var queue = function(funcs) {
  var next = function() {
    var f = funcs.shift();
    f(next);
  };
  next();
};

{% endhighlight %}

我们先来分析下，函数A调用后接着是B调用函数打印value变量。这里的关键其实就是这个`shift`方法，它总是移除数组的第一个元素并且把它返回回来。随着next函数的调用，最后funcs数组将会变成空。继续调用肯定会导致发生错误。 为了验证我们的想法是否正确。假设我们还是运行这两个函数，不管他们的运行顺序，他们都接受一个回调函数并执行它。

{%highlight javascript %}
var A = function(callback) {
  setTimeout(function() {
      value = 10;
      callback();
      }, 200);
};
var B = function(callback) {
  console.log(value);
  callback();
};

{% endhighlight %}

结果和我们预期的一样都发生了错误`undefined is not a function` 。 我们改下在调用之前先判断数组里面是否为空

{%highlight javascript %}
var queue = function(funcs) {
  (function next() {
    if(funcs.length > 0) {
      var f = funcs.shift();
      f(next);
    }
   })();
};

{% endhighlight %}

我把next函数改成了自调用的方式，这样看上去更简洁了。



让我们完善下这个函数， 考虑下数组里的函数执行时，当前函数的上下文。`this` 关键字指向的应该是全局`windows` 对象。更新下让this指向我们自定义设置的值。

{%highlight javascript %}
var queue = function(funcs, scope) {
  (function next() {
    if(funcs.length > 0) {
      var f = funcs.shift();
      f.apply(scope, [next]);
    }
   })();
};

{% endhighlight %}

添加了一个scope参数， 并且使用 `apply`函数代替了`f(next)`直接调用。 把`scope` 和 `next`  当作参数传递到`apply`函数。


还有一个功能我们需要考虑，就是需要传递参数到处理函数。 显然我们没法确定每个函数传递的参数具体有多少。所以我们要使用 `arguments`这个奇特的变量。这个变量代表每个函数实际调用传递过来的参数列表。它是一个类数组的对象。所以我们需要是稍微处理下。转换成一个真正的数组。


{%highlight javascript %}
var queue = function(funcs, scope) {
  (function next() {
    if(funcs.length > 0) {
      var f = funcs.shift();
      f.apply(scope, [next].concat(Array.prototype.slice.call(arguments, 0)));
    }
  })();
};
{% endhighlight %}


 下面的完整示例来测试我们的程序

{%highlight javascript %}
var obj = {
  value: null
};

queue([
    function(callback) {
      var self = this;
      setTimeout(function() {
        self.value = 10;
        callback(20);
      }, 200);
    },
    function(callback, add) {
      console.log(this.value + add);
      callback();
    },
    function() {
      console.log(obj.value);
    }
  ], obj);

{% endhighlight %}

运行代码，显示：

{%highlight bash %}
 30
 10
{% endhighlight %}

最终7行代码的异步处理，大功告成~


{%highlight bash %}
  var queue = function(funcs, scope) {
    (function next() {
      if(funcs.length > 0) {
        funcs.shift().apply(scope || {}, [next].concat(Array.prototype.slice.call(arguments, 0)));
      }
    })();
  };
{% endhighlight %}

<a class="jsbin-embed" href="http://jsbin.com/AhirAlOV/5/embed?js,console">JS Bin on jsbin.com</a>
<script src="http://static.jsbin.com/js/embed.min.js?3.31.0"></script>

#### 翻译

> [http://krasimirtsonev.com/blog/article/7-lines-JavaScript-library-for-calling-asynchronous-functions](http://krasimirtsonev.com/blog/article/7-lines-JavaScript-library-for-calling-asynchronous-functions)
