# jquery 封装备课

### 01-选择器模 (介绍了选择器模块)

```js
Sizzle.js  jquery源码中的部分代码
```

### 02-选择器模块的封装 (通过选择器模块获取到元素对象并添加)

```txt
缺点: 创建的方法的所有方法为私有,浪费内存空间
```

```js
   <script>
        function Sizzle(selector) {
            return document.querySelectorAll(selector);
        }

        function $(selector) {
            var doms = Sizzle(selector);
            doms.html = function () {
                console.log('html')
            }
            doms.css = function () {
                console.log('css')
            }
            return doms;
        }
        console.log(Sizzle('body'))
        $('body').html();
        $('body').css();
        var ele1 = $('body');
        var ele2 = $('body');
        console.log(ele1.html == ele2.html)
        // false -> 说明ele1 和 ele2 方法并不是同一块内存
        // $('body').html();
    </script>
```



### 03-框架结构-1 (引出DOM对象的构造函数)

```txt
缺点: 修改了原生js提供的构造函数(不推荐)
```

```js
<script>
        function Sizzle(selector) {
            return document.querySelectorAll(selector);
        }
        function $(selector) {
            var doms = Sizzle(selector);

            return doms;
        }
        NodeList.prototype.html = function () {
            console.log('html');
        }
        NodeList.prototype.css = function () {
            console.log('css');
        }
        /* 
            共享 -> 原型
            函数有原型

            构造函数 实例对象 和 原型对象的关系
            1 所有的函数都有一个原型对象(prototype)
            2 原型对象上有一个constructor 属性指向该原型所属的构造函数
            3 实例对象上有一个__proto__ 属性(非标准,编程中不要使用),该属性指向创建该实例 的构造函数的原型
        */
        console.log($('body').__proto__);
        console.log($('body').html == $('body').html)
        // console.log([1, 2, 3].max());
        // Array.prototype.max = function () {
        //     return Math.max.apply(null, this);
        // }
        // console.log([1, 2, 3].max());
    </script>
```

### 04-框架结构-2 (用构造函数对DOM对象进行包装)

```js
    <script>
        function Sizzle(selector) {
            return document.querySelectorAll(selector);
        }
        //1 获取dom元素
        function Fn(selector) {
            this.ele = Sizzle(selector);
        }
        //2 为元素添加的方法 
        // 原型的拓展
        // Fn.prototype.html = function () {
        //     console.log('html');
        // }
        // Fn.prototype.css = function () {
        //     console.log('css');
        // }
        // Fn.prototype.attr = function () {
        //     console.log('attr');
        // }
        // 替换原型
        Fn.prototype = {
            html: function () {
                console.log('html')
            },
            css: function () {
                console.log('css')
            },
            attr: function () {
                console.log('attr')
            }
        }
        //3 入口函数
        function $(selector) {
            return new Fn(selector);
        }
        console.log($('body').html == $('body').html)
    </script>
```



```txt
缺点: 全局变量增加,容易被污染
```

### 05-自执行函数(减少全局变量)

```js
       //方法一
       var $ = (function () {
            function Sizzle(selector) {
                return document.querySelectorAll(selector);
            }
            //1 获取dom元素
            function Fn(selector) {
                this.ele = Sizzle(selector);
            }
            //2 为元素添加的方法 

            // 原型的拓展
            // Fn.prototype.html = function () {
            //     console.log('html');
            // }
            // Fn.prototype.css = function () {
            //     console.log('css');
            // }
            // Fn.prototype.attr = function () {
            //     console.log('attr');
            // }
            // 替换原型
            Fn.prototype = {
                html: function () {
                    console.log('html')
                },
                css: function () {
                    console.log('css')
                },
                attr: function () {
                    console.log('attr')
                }
            }
            //3 入口函数
            function $(selector) {
                return new Fn(selector);
            }
            return $;
        })() 
        // 方法二
        (function (window) {
            function Sizzle(selector) {
                return document.querySelectorAll(selector);
            }
            //1 获取dom元素
            function Fn(selector) {
                this.ele = Sizzle(selector);
            }
            //2 为元素添加的方法 
            // 原型的拓展
            // Fn.prototype.html = function () {
            //     console.log('html');
            // }
            // Fn.prototype.css = function () {
            //     console.log('css');
            // }
            // Fn.prototype.attr = function () {
            //     console.log('attr');
            // }
            // 替换原型
            Fn.prototype = {
                html: function () {
                    console.log('html')
                },
                css: function () {
                    console.log('css')
                },
                attr: function () {
                    console.log('attr')
                }
            }
            //3 入口函数
            function $(selector) {
                return new Fn(selector);
            }
            window.$ = window.jquery = $;
        })(window)
        console.log($('body').html == $('body').html)
```

```txt
缺点: 由于没有暴露构造函数的原型,无法添加插件
```

### 06-框架结构-3(将构造函数以及方法放到暴露的$的原型上)

```js
    <script>
        (function (window) {
            function Sizzle(selector) {
                return document.querySelectorAll(selector);
            }
            //1 获取dom元素
            // function Fn(selector) {
            //     this.ele = Sizzle(selector);
            // }
            // 替换原型
            // Fn.prototype = {
            //     html: function () {
            //         console.log('html')
            //     },
            //     css: function () {
            //         console.log('css')
            //     },
            //     attr: function () {
            //         console.log('attr')
            //     }
            // }
            //3 入口函数
            function $(selector) {
                return new $.prototype.Fn(selector);
            }
            $.fn = $.prototype = {
                Fn: function (selector) {
                    this.ele = Sizzle(selector)
                },
                html: function () {
                    console.log('html')
                },
                css: function () {
                    console.log('css')
                },
                attr: function () {
                    console.log('attr')
                }
            }
            // 将Fn 的原型指向 $.prototype
            $.fn.Fn.prototype = $.fn;
            // 这样的实例可以访问到$.prototype上的方法了
            window.$ = window.jquery = $;
        })(window)
        $.prototype.log = function () {
            console.log('log')
        }
        $.fn.log();
        console.log($('body').html == $('body').html)
    </script>
```

### 整体思路

```txt

    01-选择器模块 (介绍了选择器模块) 
    02-选择器模块的封装 (通过选择器模块获取到元素拥有方法)
    缺点:  创建的所有元素的方法为私有的,浪费内存空间
    03-框架结构-1 (引出dom对象的构造函数)
    缺点: 修改了js原生的构造函数(不推荐)
    04-框架结构-2 (用构造函数对dom对象包装)
    缺点: 全局变量增加,容易被污染
    05-自执行函数(减少全局变量)
    缺点: 由于没有暴露构造函数的原型,所以无法给拓展插件
    06-框架结构-3 (将构造函数以及方法放到暴露函数的原型上)
 
```

