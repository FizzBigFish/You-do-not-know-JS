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