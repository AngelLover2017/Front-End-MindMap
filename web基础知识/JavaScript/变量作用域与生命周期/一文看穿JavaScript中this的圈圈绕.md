## 导文目录
<hr>

- 为什么说JavaScript中 `this` 指针圈圈绕？
- JavaScript 中 `this` 绑定作用域的四种情况
    - 先搞清Node环境中和浏览器环境中全局对象的异同
    - 默认绑定
    - 隐式绑定
    - 硬绑定（或者说 显示绑定）
    - `new`操作符绑定
    - 四种绑定的优先级
- ES6中引入箭头函数对`this`的绑定产生了什么影响？
- 附上前面程序的输出答案


## 为什么说JavaScript中 `this` 指针圈圈绕？
<hr>

相比C++或者Java中的`this`指针的概念而言，JavaScript中的`this`指针更为 "灵活" ，C++或Java中的 `this`在类定义时便成为了一个指向特定类实例的指针，但是JavaScript中的`this`指针是可以动态绑定的，也就是说是依据**上下文环境**来指定`this`到底指向谁。这样的机制给编程带来了很大的灵活性（以及趣味性），但这也导致了在JavaScript编程中若不明白`this`指针的作用机制而滥用`this`指针的话，常常会引发一些 "莫名其妙" 的问题。比如说，下面这段程序：

```javascript
1. let num = 10;
2. console.log(global.num); 
3. global.num = 20;
4. console.log(this.num);
5. console.log(this === global); 
6. function A(){
7.    console.log(this.num++);
8. }
9. let obj = {
10.     num : 30,
11.     B : function(){
12.         console.log(this.num++);
13.         return () => console.log(this.num++);
14.      }
15. }
16. A();
17. let b = obj.B; 
18. b()();  
19. obj.B();   
20. b.apply(obj);
21. new A(); 
22. console.log(global.num); 
```
你能列出最终所有的输出吗？你可以先尝试着写一下，不要复制到VSCode中运行哦~ ，手动写出答案！写完先看一下最后面的答案，看你是否写对了。如果写对了说明你已经基本掌握了JavaScript中`this`指针的机制 ***（PS：设定这里运行环境是`node环境`）*** ；如果没有写对，那看完本文相信就可以对`this`有一个基本清楚的认识了。

相信你肯定忍不住去看了答案了，或许答案看起来杂乱无章，这也是为什么`this`作为JavaScript中最复杂的机制之一经常被拿到面试中去考察JS的功底。以下内容可能需要花费**8-10**分钟时间，但是会让读者你受益匪浅的，你的疑问也可以在下面的内容中得到解答！

## JavaScript 中 `this` 绑定作用域的四种情况
<hr>

### 先搞清Node环境中和浏览器环境中全局对象的异同
在讲解`this`绑定作用域的四种情况之前，我们先要弄清楚一个问题。Node环境中的全局作用域和浏览器环境下的全局作用域有什么不同？

这个问题很重要，因为这个异同，会导致同样的代码在Node环境和浏览器环境下的表现不尽相同。就比如我们这里要讲的`this`指针的指向会因为环境不同而不同。这个不同体现在以下三点:
- 浏览器的全局作用域的全局对象是`window` ; 而Node中这个"等价"的全局对象是`global`
- 浏览器环境下全局作用域下的`this`指向的就是`window`对象; 但是Node环境下全局作用域中的`this`和`global`是分离的，`this`指针指向一个空对象
- 浏览器环境下全局作用域中声明的变量会被认为是全局对象`window`的属性；但是Node下全局作用域下的声明的变量不属于`global`。

由此，你便可知，上面代码中**1-5**的输出了，就像下面这样：
```javascript
undefined  // 1 
undefined  // 2
false      // 3
```
为了方便讲解，我给每个输出编了号，我们依次来看：
1. 第一个`undefined`是因为Node的全局作用域上的变量并不会作为`global`的属性，此时`global.num`尚未赋值，所以是`undefined`
2. 第二个`undefined`是因为Node中全局作用域中的`this`并不指向`global`,所以此时`this.num`尚未赋值，所以也是`undefined`
3. 第三个`false`也更加应证了 **2** 中的结论，`Node`中全局作用域的`this`与`global`风马牛不相及

【PS】上面我一直强调是**全局作用域下的`this`** ,为什么呢？因为Node中在子作用域中的`this`的行为和浏览器中是相仿的，基本一致
### 默认绑定
下面我们来讲解JavaScript中`this`绑定作用域的四种情况。

先来说第一种——**默认绑定** ，我们可以这样理解 默认绑定 ，`this`指针在作用域内没有认领的对象时就会默认绑定到全局作用域的全局对象中去，在Node中就是会绑定到global对象上去。这是一个很形象的说法，虽然不严谨但是好理解，我们看下面这几个例子，来说明什么情况下，`this`没有对象认领。
```javascript
global.name = 'javascript';
(function A(){
    this.name += 'this';
    console.log(this.name);//输出 javascriptthis
})();
console.log(global.name);//输出 javascriptthis
```
在函数A的作用域内，`this`并没有可以依靠的对象，所以`this`指针便开启默认绑定模式，此时指向的是`global`。

这里我们有必要明确一个概念，有别于JavaScript中"一切皆为对象"的概念，虽然`A`确实是一个`Function`类型的对象 , 下面的例子可证明确实如此
```javascript
function A(){}
console.log(A instanceof Function); //输出 true
console.log(A instanceof Object);   //输出 true
```
但是`function A(){}`只是一个函数的声明，并没有实例对象的产生，而`this`是需要依托于一个存在的实例对象 ， 如果使用`new A()`则便有了实例对象，`this`也就有了依托，有关`new操作符绑定`在后面说。

明白了这一点，我们来看一个更为复杂的例子：
```javascript
global.name = 'javascript';
(function A(){
    this.name += 'this';
    return function(){
        console.log(this.name);//输出 javascriptthis
    }
})()();
console.log(global.name);//输出 javascriptthis
```
这个例子中`函数A`返回了一个匿名函数也可以叫闭包，我们发现`this`照样绑定在了`global`上。这个例子是想告诉读者，默认绑定和作用域层级没有关系，只要是在作用域内`this`找不到认领的实例对象，那就会启用默认绑定。

由此，你是不是可以知道开篇的例子中 **6，7，8，16**行的输出结果了？
```javascript
20  //这里是后置自增运算，所以先输出后加一
```
### 隐式绑定
隐式绑定顾名思义没有显式的表明`this`的指向，但是已经绑定的某个实例对象上去了。举个简单的例子，这个用法其实是我们最常用的：
```javascript
global.name = 'javascript' ;
let obj = {
    name : 'obj',
    A    : function(){
        this.name += 'this';
        console.log(this.name); 
    }
}
obj.A();//输出 objthis
console.log(global.name);//输出 javascript
```
这个例子中`函数A`的作用域内，`this`总算是有对象认领了，这个对象就是`obj`，所以`this.name`指向的就是`obj`中的`name` ，这种情况就叫做**隐式绑定**

隐式绑定虽然是我们最常用的，也是相对好理解的一种绑定方式，但是确是四种绑定中最坑的一种，为什么呢？因为，这种情况下`this`一不小心就会找不到认领她的对象了，我们称之为"丢失"。而在"丢失"的情况下，`this`的指向会启用默认绑定。我们看下面的例子；
```javascript
global.name = 'javascript' ;
let obj = {
    name : 'obj',
    A    : function(){
        this.name += 'this';
        console.log(this.name)
    },
    B    : function(f){
        this.name += 'this';
        f();
    },
    C    : function(){
      setTimeout(function(){
          console.log(this.name);
      },1000);
    }
}
let a = obj.A;              // 1
a();                        // 2
obj.B(function(){           // 3
    console.log(this.name); // 4
});                         // 5
obj.C();                    // 6
console.log(global.name);   // 7
```
这里列出了三种"丢失"的情况：
1. **1-2**行中`obj的A函数`赋值给了`a`，然后调用`a()`，这时候函数的执行上下文发生了变化，相当于是全局作用域下的一个函数的执行，所以承接我们上面所说，此时启用了默认绑定
2. **3-5**行中给`obj.B`传递一个`Function`参数，并且在`B`中`f()`执行，这相当于一个`B`中的立即执行函数，此时在`this`所在作用域找不到认领的对象，于是启用默认绑定
3. **6**行是最有意思的一行，为什么呢？因为这一行在`Node环境`和`浏览器环境`下的结果是不一样的，按照常理来说，回调函数中的`this`同样会因为丢失而启用默认绑定，在浏览器环境下确实如此。但是在`node`中事情好像没那么简单，我们先看看输出的结果，在做分析

```javascript
javascriptthis // 1-2行执行结果
javascriptthis // 3-5行执行结果
javascriptthis // 7行执行结果
undefined      // 6行执行结果
```
你会发现有一个值很扎眼，没错，就是`undefined`，那为什么`setTimeout()`回调中的`this`没有启用默认绑定呢？这里根据这篇博客做了一个猜想 : [NodeJS 回调函数中的this](https://blog.csdn.net/w_vc_love/article/details/51012252?utm_source=blogxgwz6) ，我建议你看一看这篇博客

亦如`fs.open()`回调一样，`setTimeout()`函数会先初始化自己，那么此时回调函数作用域上就是存在实例对象了，只是这个对象我们看不到而已，所以此时`this.name`并未初始化，所以输出`undefined`。为此我做了一个实验来证明，`setTimeout()`的`this`指向不等于`global` 
```javascript
function A(){
    console.log(this === global);
}
A();  //输出  true
setTimeout(function(){
    console.log(global === this);
},1000);  // 输出  false
```

由此，我们可以知道，开篇例子中**18,19**行的输出便是：
```javascript
21 // 隐式绑定丢失
22 // 箭头函数绑定上级作用域this指针，这个后面会讲
30 //隐式绑定
```
### 硬绑定（显式绑定）
接下来要讲的是**硬绑定** ， 这个比较简单，是通过JS的三个常用API来显式的实现绑定特定作用域，这三个API为
- apply
- call
- bind

> 这三个API之间的关系非本篇关键，可以自行了解，本篇以`apply`为例

我们知道JS函数的一大特点就是有 ***定义时上下文*** 、***运行时上下文*** 以及 ***上下文可变*** 的概念，而`apply`就是帮助我们改变函数运行时上下文的环境，这种通过API显式指定某个函数执行上下文环境的绑定方式就是 **硬绑定**

我们来看下面这个例子：
```javascript
global.name = 'global';
function A(){
    console.log(this.name);
}
let obj = {
    name : 'obj'
}
A.apply(obj); //输出 obj
A.apply(global); //输出 global
```
对，你应该懂了，什么叫硬绑定。就是不管函数你定义在哪里，这样使用了我这个API，你就可以为所欲为，绑定到任意作用域的对象上去，哪怕是`global`都不带怕的，硬核API **!!!**

由此，你也可以得到开篇例子中**20**行输出结果应该是：
```javascript
31  //obj.name在此之前被加了一次1，所以这里是31
```
### `new`操作符绑定
最后一种绑定方式是`new`操作符绑定，这个也是JS中最常用的用法之一了，简单来说就是通过`new`操作符来实例化一个对象的过程中发生了`this`指针的绑定，这个过程是不可见的，是后台帮你完成了这一绑定过程。具体是什么过程呢？这里我们就已开篇的例子为例吧
```javascript
function A(){
    console.log(this.num++);
}
new A(); //输出为 NaN
```
`NaN`是JS`Number`对象上的一个静态属性，意如其名"not a number"，表示不是数字。这里`new A()`实例化了一个对象，此时在`A`的作用域里就用对象认领`this`指针了，所以此时`this`指向实例化对象，但是这个对象中`num`属性并没有初始化，因此是`undefined`，而`undefined`非数字却使用了`++`运算，因此最终输出了`NaN`

### 四种绑定的优先级
既然`this`的绑定有四种机制，那必定会出现机制冲突的情况，没关系，其实从上面的讲解中你应该已经能隐约感觉到这四种机制是有优先级存在的。比如，在`new`操作符绑定的时候，就是因为`new`绑定优先级高于默认绑定，所以`this`指针指向的是新实例化的对象而不是全局对象`global`。这里给出这四种绑定的优先级 ：

<pre> new 操作符绑定   >    硬绑定   >    隐式绑定   >    默认绑定</pre>
这个关系还是挺明显的，故不作例子阐述了。
## ES6中引入箭头函数对`this`的绑定产生了什么影响？

<hr>

快要结束了，再坚持一下，最后有必要说明以下ES6中的箭头函数对于`this`指针绑定的影响，ES6中引入箭头函数是为了更优雅的书写函数，对于那些简单的函数我们使用箭头函数代替原来的函数写法可以大大简化代码量而且看上去更加整洁优雅。也正是因为箭头函数的设计是为了简洁优雅，所以箭头函数除了简化代码表示以外，还简化了函数的行为。
- 箭头函数不能声明的方式定义，只能通过函数表达式
- 箭头函数不能通过`new`来实例化对象
- 也是因为上面的原因，箭头函数中并没有自己的`this`指针，这不代表不能使用`this`，箭头函数中的`this`是继承自父级作用域上的`this`，也就是说箭头函数中的`this`绑定的是父级作用域内`this`所指向的对象

举个例子来讲：
```javascript
name = 'global';
this.name = 'this';
let obj = {
    name : 'obj',
    A    : function(){
        ()=>{
            console.log(this.name)
        }
    },
    B    :() => {
        console.log(this.name)
    }
}
obj.A(); //输出 obj
obj.B(); //输出 this 
```
这里或许`obj.B()`输出让你疑惑，其实我们开篇也讲了，全局作用域下`this`和`global`风马牛不相及，所以这里对应到父级作用域中`this`对应的对象就是`this`本身或者`export`

由此，开篇示例中**18**行的输出便可知晓了 ：
```javascript
21 // b() 所输出
22 // (b())()所输出
```
这里有些绕，之所以最终`this`绑定到了`global`上，是分了两步
- 首先，因为是箭头函数，所以`this`继承父级`this`绑定到了`obj`上
- 因为隐式调用的"丢失"，导致父级`this`默认绑定到了`global`上
## 附上前面程序的输出答案

```javascript
undefined
undefined
false
20
21
22
30
31
NaN
23
```

总算是写完了，写作的过程，笔者收获也很大，就比如Node中回调函数的`this`指向问题我也没有想到，是通过实验才印证Node中回调函数中`this`指向的是自身实例化的对象，这个工作同样不可见，后台完成了，就像`new`一样。
希望读者也可以得到收获！

下面是我的微信公众号，如果觉得本篇文章你收获很大，可以关注我的微信公众号，我会同步文章，这样可以RSS订阅方便阅读，感谢支持！


![](https://user-gold-cdn.xitu.io/2019/4/1/169d8578d5bec6a3?w=258&h=258&f=jpeg&s=28063)