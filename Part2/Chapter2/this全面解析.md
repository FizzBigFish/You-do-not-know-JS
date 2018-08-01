调用位置
    > 开发者工具里调试工具，debugger 的第二个元素，即是调用位置
* 调用规则
    1. 默认绑定：独立函数调用。此规则是无法应用其他规则时的默认规则。（this 指向全局对象）
    2. 隐式绑定：会把函数调用中的this绑定到这个上下文对象
        ```
        function foo() {
            console.log(this.a);
        }
        var obj = {
            a:2,
            foo:foo
        };
        obj.foo();

        ```
    > 注 ：对象属性引用链只有上一层或者说最后一层在调用位置中起作用。
    ```
    function foo(){
        console.log(this.a);
    }
    var obj2 = {
        a:3,
        foo:foo
    }
    var obj1 = {
        a:2,
        obj2:obj2
    };
    obj1.obj2.foo();

    ```
    a. 隐式丢失
     最常见的this绑定问题就是被隐式绑定的函数会丢失绑定对象，它会用默认绑定。
     ```
       function foo() {
            console.log(this.a);
        }
        var obj = {
            a:2,
            foo:foo
        };
        var bar = obj.foo;
        var a = 'oops,global';
        bar();
        ```
    更常见的是传入回调函数时，参数传递其实就是一种隐性赋值。还有一种情况是：调用回调函数的函数可能会修改this。比如时间处理器常会把回调函数的this强制绑定到触发事件的DOM元素上。
    3.显式绑定
    JavaScript提供的绝大数函数以及自己创建的所有函数都可以用call()和apply()方法。
    如果传入的第一个参数是原始值（字符串类型、布尔类型、数字类型），就会被转成它的对象形式（new String()、new Boolean()、new Number()），这通常称为装箱。

    4.new 绑定
    使用new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作
    a.创建一个全新的对象
    b.这个新对象会被执行[[Prototype]]连接
    c.这个新对象会绑定到函数调用的this
    d.若这个函数没有返回其它对象，那么new表达式中的函数调用会自动返回这个新对象

*  优先级
     new 显式绑定  隐式绑定 默认绑定 

* 绑定例外
   a. 被忽略的this : 如果把null undefined 作为this的绑定对象传入call apply bind 这些值在调用时会被忽略，实际应用的是默认绑定规则。
```
        function foo(){
            console.log(this.a)
        }
        var a = 3
        foo.call( null);
```
    这种使用null来忽略this 绑定可能会产生副作用。例如某个函数确实使用了this，那么默认绑定会把this绑定到全局对象。
    更安全的做法是传入一个特殊的对象，即Object.create(null)，这个和{}很像，但是不会创建Object.prototype这个委托，所以比{}更空
    b. 间接引用 ：这种情况下应用默认绑定规则。
        间接引用最容易在赋值时发生：
```
        function foo() {
            console.log(this.a)
        }
        var a = 2;
        var o = {
            a:3,
            foo:foo
        }
        var p = {
            a:4
        }
        o.foo();//3
        (p.foo = o.foo)();//2

```
    c. 软绑定：硬绑定可以防止函数调用应用默认绑定规则，但是降低了函数的灵活性，使用硬绑定就无法使用隐式绑定或者显式绑定来修改this
    ```
    if(!Function.prototype.softBind){
        Function.prototype.softBind = function (obj) {
            var fn = this;
            var curried = [].slice.call( arguments, 1 );
            var bound = function (){
                return fn.apply(
                    (!this || this === (window || global))? obj : this,curried.concat.apply( curried, arguments)
                );
            };
            bound.prototype = Object.create(fn.prototype);
            return bound;
        }

    }


    
