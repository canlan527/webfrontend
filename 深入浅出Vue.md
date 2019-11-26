# 深入浅出Vue 

## 引入

一开始，使用CDN的方式，引入Vue脚本链接

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

## 插值表达式

```html
<div id="app">
    {{ msg }}
</div>
```

定义全局Vue

```js
const vm = new Vue({
    el: "#app",
    data: {
        msg: 'Hello World'
    }
})
```
### 知识点1    el

使用`el`就可以绑定元素，对应的`vm`会有方法`$el()`方法选中指定元素，到了项目后期，我们一般会使用`$mount()`

### 知识点2   数组变异方法

如果在`data`中定义数组，想在页面随意操作数组元素，不能使用`vm.arr[0]='xxx'的方法，不顶用 `需要使用数组方法，在`vue`里就是数组变异方法，`push()`、`shift`、 `pop()`、 `unshift()、` `sort()、` `reverse()、` `splice()；`

### 知识点3 $set() 修改数组值

一般使用直接赋值的方式不能修改页面上对象中的值，可以新增一个键值对，但是这样太耗性能，可以使用

```js
vm.$set(obj,'name','canlan')
```

来修改对象

### 知识点4  nextTick()

我们定义好了一个全局的`vue`实例，在定义好之后，修改`data`里的值，页面虽然修改了，但是此时，`console`一下，会发现`js`里还是没变，因为`js`是一个单线程运行机制，`vm`修改的都是异步操作，引擎会先走`console`语句，再走`vm`异步流，所以无法更新，使用`nextTick()`方法可以让`console`在异步线程里等待执行，与修改后的值同步。

```js
const vm = new Vue({
    // el: "#app",
    data: {
        msg: 'Hello World',
        log:'today',
        arr: [1,2,3],
        obj: {
            name: 'xiaonan',
            age: '23'
        }
    }
})
vm.$mount('#app')//挂载
//修改
vm.msg = "肖楠肖楠"
vm.arr = [1,2,3,4]
vm.log = 'tomorrow'
vm.log = 'yesterday'
vm.msg = '加油'
vm.msg = 'nuli'

// console.log(vm.$el.innerText)//输出
vm.$nextTick(()=>{
    console.log(vm.$el.innerText)//异步输出
})
```

## 用的不多的指令

### 1.v-pre

`v-pre`跳过绑定元素内的编译过程，不采用vue语法渲染，如下 ，此时页面会显示 {{  ‘a’ }}

```html
<div id="app" v-pre>
    {{ 'a' }}
</div>

<script>
    const vm = new Vue ({
        el:'#app'
    })
</script>
```

### 2.v-cloak

页面会先渲染dom树，然后渲染js代码，在js代码中渲染vue，在这个过程中，页面如果网速不好资源加载不过来的话，会有大量编译前的字符显示在页面上，用户体验不好。

所以我们在标签上绑定`v-cloak`指令，通过css，让其元素为空，优化用户体验。

```html
<style>
[v-cloak]{
    display: none;
}
</style>
<body>
    
    <div id="app" v-cloak>
        {{ 'ppp' }}
    </div>

    <script>
        const vm = new Vue ({
            el:'#app'
        })
    </script>
</body>
```

### 3.v-once 

当页面上需要显示 第一次获取回来的值，只渲染这一次。指令所在的标签，会让这个值不能被修改。

原因：加入了v-once的值，会被存进缓存，然后直接从缓存拿值，不从实例里取。

```html
    <div id="app" v-cloak>
        <div v-once> {{ name }}</div>
        {{ age }}
    </div>

    <script>
        const vm = new Vue ({
            el:'#app',
            data:{
                name:'xiaonan',
                age:23
			}
        })
        vm.name = 'canlan' //无法改变name值
    </script>
```

### 4.v-html 和v-text

同`js`中的`innerHtml`和`innerText`作用一样

## 常用的指令

### 1.v-if   v-else  v-else-if

场景：用一个变量来控制多个标签的显示与隐藏

知识点：使用template标签来判断不会多渲染一个div出来，减少判断次数，减少dom添加

```html
<div id="app" v-cloak>
    <img v-bind:src="imgUrl" alt="" v-if="count==3">
    <h2 v-else-if="count==2">哈哈 没有图片了...</h2>
    <img v-else="count==1" :src="img2" alt="">
</div>
<script>
    const vm = new Vue ({
        el:'#app',
        data: {
            imgUrl: 'https://img1.mukewang.com/szimg/5baf3f0a000180df06000338.jpg',
            img2:'https://img1.mukewang.com/szimg/5d5377660922e54c12000676.jpg',
            flag: !true,
            count:2
        }
    })
	//来回切换显示v-if/else绑定指令的标签
    setInterval(()=>{
        vm.flag = !vm.flag
    }, 500)
</script>
```

### 2.v-show

通过改变`css:display:none`来控制元素显示与隐藏,**不支持template标签**

### 3.v-bind:  绑定属性  简写   ：冒号

当我们需要对标签里的属性进行操作时，需要使用`v-bind:`指令，简写`:`表示，**不需要**花括号。

```html
<div id="app" v-cloak>
    <img v-bind:src="imgUrl" alt="">
</div>

<script>
    const vm = new Vue ({
        el:'#app',
        data: {
            imgUrl: 'https://img1.mukewang.com/szimg/5baf3f0a000180df06000338.jpg'
        }
    })
</script>
```

#### :class  / : style 绑定类名

`:class` 是给标签添加data里定义的属性类名，命名不会和原有的`class`属性冲突，绑定多个可以放进数组或对象里。

```html
<div id="app" >
    //此时就绑定了gree类名
    <div :class="green">加油！</div>
</div>
<script>
    const vm = new Vue ({
        el:'#app',
        data: {
            green: 'green',
            red:'red'
        }
    })
</script>
```

`：style`绑定样式

```html
 <div id="app" >
     <div :class="color" :style="divStyle">this is a div</div>
</div>

<script>
    const vm = new Vue ({
        el:'#app',
        data: {
            color: 'red',
            divStyle: {width:'100px', height: '100px', background:'red'}//定义样式
        },
        methods: {
            handleColor(color){
                this.color = color
            }
        }
    })
</script>
```

注意：其实我们给标签绑定的方法，会固定写在methods里面，data里如果出现同名的变量，vue引擎会先挂在data里的变量，这是执行顺序。但是，data里的函数和methods里的函数他们的this指向不一样，methods中，this指向vue实例；data中，this指向window。

PS:我们写在methods里的方法，如果要用到this，函数最好是不要使用箭头函数，在箭头函数中，this指向window。

### 4.v-for /  :key

遍历循环标签，key值必须是唯一的。

```html
 <div v-for="(item,index) in list" :key="index">{{ item }}</div>
 <script>
     const vm = new Vue ({
         el:'#app',
         data: {
             list:['apple','pear','orange']

         },
     })
</script>
```

key值的作用：我们不建议那index当key值，因为这样再来一个数组循环的话，key值就不是唯一的，渲染会出错。

还有一个更重要的原因，就是key是会记录我们一开始每一项的内容，然后当我们重新赋值时，会进行比对dom，如果当前item不是上一次记录的值，就会替换成新的值，重新渲染dom。

一般我们在遍历dom的时候，每一项都会有**固定的id**，我们可以使用item.id来充当key值。

遍历对象

```html
<div v-for="(value, key) in objStu">{{ value}}</div>
<script>
    const vm = new Vue ({
        el:'#app',
        data: {
            objStu:{
                name: 'xiao',
                age: '23'
            }
        },
    })
</script>
```

遍历数字/ 遍历字符串

```html
//遍历1-8
 <div v-for="item in 8">{{ item }}</div>
//遍历字符串
 <div v-for="item in 'xiaonan' ">{{ item }}</div>
```

### 5.v-model 双向数据绑定

v-model实现数据双向绑定,在input框里，是input+value语法糖，textarea同理。

```html
<!-- 使用v-model -->
<div id="app">
    <input type="text" v-model="content">
    <div>{{content}}</div>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            content: 'xiaonan'
        },

    });
</script>

<!-- 使用input+value方法 -->
<div id="app">
    <input type="text" :value="content" @input="handleInput">
    <div>{{content}}</div>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            content: 'xiaonan'
        },
        methods: {
            handleInput(e){
                this.content = e.target.value
            }
        },
    });
</script>
```

此外 还有复选框，多选框，单选框，下拉列表。

```html
 <input type="checkbox" v-model="checked">
```

## 自定义指令

需要对普通 DOM 元素进行底层操作，这时候就会用到自定义指令。

在全局中，使用directive来表示，实现自己的自定义指令。

```js
//全局自定义指令
Vue.directive('slice', (el, bindings, vnode)=>{
     // console.log(el)
     console.log(bindings)
     console.log(vnode)
     const val = bindings.value.slice(0,5)
     vnode.context.content = val
 })

//局部自定义指令
const vm = new Vue({
    el: '#app',
    directives:{
        slice:{
             bindings(el,bindings,vnode){
                 //一加载就执行的函数
             },
             update(el,bindings,vnode){
                //当值更改时执行的函数
             }
        }   
    },
    data: {
        content:'xiaonan'
    }
})
```



## 过滤器  filter

在不改变原有数据内容的基础上，改变它的展现形式，利于用户阅读。

```html
<div id="app">
    {{money | toMoney(2)}}
</div>
<script>
    //全局过滤器 value是管道符之前的数值  
    Vue.filter('toMoney',(value,times)=>{
        return (value * times).toLocaleString() //return的值会是money最后展现的值
    })
    Vue.filter('ceshi',(value)=>{//连用 
        reteurn 'ceshi'
    })
    const vm = new Vue({
        el:'#app',
        data:{
            money:10000000
        },
        filters:{//局部过滤器
            toMoney: ()=>{
            	//return ...          
            }
        }
    })
</script>
```



--------------------



## 修饰符

`.`就是要增加修饰符的意思，免去我们做逻辑。

在**todolist**案例中，在键盘抬起事件中，加入修饰符`.enter`或者是keycode`.13`，这样，在键盘按下回车键的时候，也能触发该事件。

```html
<input type="text" v-model="content" @keyup.enter="handleClick"/>
```

**给修饰符起别名** 

还可以通过全局 `config.keyCodes` 对象[自定义按键修饰符别名](https://cn.vuejs.org/v2/api/#keyCodes)：选择keycode

```html
<div id="app">
    <input type="text" v-model="content" @keyup.f1-key="handleClick"/>
</div>
<script>
    Vue.config.keyCode={
        'f1-key':112
    }
    //
    Vue.config.keyCodes.f1 = 112
</script>
```

### 常用修饰符

- `.lazy`，在`v-model`后加入`.lazy`修饰符，就是相当与绑定input+change事件。
- `.number`可以将该输入框内输入的数字有默认的`string`型转成`number`。
- `.trim`可以去掉输入框内输入字符两端的空格。

#### 事件修饰符

- `.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`

- `.stop` 阻止单击事件继续传播 

- `.capture` 添加事件监听器时使用事件捕获模式

- `.self` 只当在 event.target 是当前元素自身时触发处理函数 ,即事件不是从内部元素触发的 

- `.once `点击事件将只会触发一次

- `.passive `滚动事件的默认行为 (即滚动行为) 将会立即触发

  ```html
  <!-- 修饰符可以串联 -->
  <a v-on:click.stop.prevent="doThat"></a>
  
  <!-- 只有修饰符 -->
  <form v-on:submit.prevent></form>
  
  <!-- 添加事件监听器时使用事件捕获模式 -->
  <!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
  <div v-on:click.capture="doThis">...</div>
  
  <!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
  <!-- 即事件不是从内部元素触发的 -->
  <div v-on:click.self="doThat">...</div>
  
  <!-- 点击事件将只会触发一次 -->
  <a v-on:click.once="doThis"></a>
  不像其它只能对原生的 DOM 事件起作用的修饰符，.once 修饰符还能被用到自定义的组件事件上
  
  <!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
  <!-- 而不会等待 `onScroll` 完成  -->
  <!-- 这其中包含 `event.preventDefault()` 的情况 -->
  <div v-on:scroll.passive="onScroll">...</div>
  ```

  > 使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 `v-on:click.prevent.self` 会阻止**所有的点击**，而 `v-on:click.self.prevent` 只会阻止对元素自身的点击。

  > 不要把 `.passive` 和 `.prevent` 一起使用，因为 `.prevent` 将会被忽略，同时浏览器可能会向你展示一个警告。请记住，`.passive` 会告诉浏览器你*不*想阻止事件的默认行为.

#### 键盘修饰符

- `.enter`
- `.tab`
- `.delete` (捕获“删除”和“退格”键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

#### 系统修饰符

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

请注意修饰键与常规按键不同，在和 `keyup` 事件一起用时，事件触发时修饰键必须处于按下状态。换句话说，只有在按住 `ctrl` 的情况下释放其它按键，才能触发 `keyup.ctrl`。而单单释放 `ctrl` 也不会触发事件。如果你想要这样的行为，请为 `ctrl` 换用 `keyCode`：`keyup.17`。

#### .exact修饰符

`.exact` 修饰符允许你控制由精确的系统修饰符组合触发的事件。

```html
<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button @click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button @click.exact="onClick">A</button>
```

# 挂载

```html
<div id="app">{{content}}</div>
<script>
    const vm = new Vue({
        el: '#app',
        data: {
            content: 'Hello World'
        },
        template:{
            
        }
    })
    $mount()
</script>
```



当我们在写入一个div，并在Vue中挂载和使用的时候，发生了什么。

或者说，我们在Vue实例中，使用了el绑定了#app,就能在app的div里使用vue语法了呢？

答案：在Vue实例中，会首先找有没有el，如果有 ，就到Dom里找对应的元素，比如找id为#app的元素，然后拿到这个元素的outerHTML，比如：div#app.outerHTML，就是`<div id="app">{{content}}</div>`,以这个作为模板，生成一个抽象语法树AST({}),在通过AST抽象语法树去利用vue中的render函数生成虚拟节点vnode，通过这个vnode生成一个真实Dom，这个真实Dom最终会替换掉一开始的outerHTML模板`<div id="app">Hello World</div>`。 

​	如果没有实例中没有el属性，它会找有没有`$mount`，如果有的话，通过` $mount`来寻找你的dom中对应的元素，然后走上面一套流程。

​	如果没有el属性，也没有$mount,就什么也不干了，就搁那儿放着，不解析了。

## template

真正的流程：在Vue实例中，首先看你有没有el，再看你有没有template，如果有templa就用template里的dom结构，然后生成AST，再渲染，再做以下的操作。

​	当没有template的时候，会拿到el下的outerHTML。

![挂载流程](F:\7D\VUE\vue所需资料\11. el、template、render\挂载流程.jpg)

----------------

## render

真正的流程：看你有没有el，再看你有没有template，渲染时发现实例中已经有render函数，就走实例中渲染的函数的流程，不走dom默认的渲染了。

```html
<div id="app">{{content}}</div>
<script>
    const vm = new Vue({
        el:'#app',
        template:'<h1>hello World<h1>',
        render(createElement){  //返回创建的dom元素
            return createElement('h1');
        },
        data:{
            content:'Hello World'
        },
    })
</script>
```

render函数里，不仅能写直接生成dom的形式，还能写JSX语法

```js
render(createElement){  
    return createElement('h1',{
        class:'title',//给创建的h1标签添加类名
        style:{
            color:'tan',
            fontSize:'30px'
        }
    },'我是一个标题');
},
```

在已创建的标签里再创建子标签

```js
//在h1标签内创建P标签 ，P标签会继承父元素的类样式
render(createElement){  
    return createElement('h1',{
        class:'title',
        style:{
            color:'tan',
            fontSize:'30px'
        }
    },[
        '我是一个标题',
        createElement('p','我是一个P标签')
    ]);
},
```

### JSX语法

上面生成dom的写法过于复杂，我们可以使用jsx语法。

```js
render(createElement){  
    return <h1><p>HAHA</p></h1>

},
```

更多的操作，添加样式和事件

```js
render(createElement){  
    return (<h1 class="haha" style="{{color:'red',fontSize:'12px'}}"
            	on-click={()=>{console.log('此标签的点击事件')}}
            ><p>HAHA</p></h1>)

},
```

## template和render为什么能同时存在

我们之后搭建脚手架，组件之间来回传值，如果需要动态生成某个标签，使用template肯定是不行的。

举个栗子：外部会传来一个tag标签，如果使用template的话，就需要很多判断，判断是哪个标签，但是在render函数里的话，只需要接收一个tag，这个tag标签会渲染成接收到的标签。

```js
render(createElement){  
    const tag="a";
    return (
        <tag>
        //...
        </tag>
    )
},
```



---------------------------------------

# 生命周期

Vue从被创建出来，到被销毁的周期。

![Vue å®ä¾çå½å¨æ](https://cn.vuejs.org/images/lifecycle.png)

```html
<div id="app">{{name}}</div>
<script>
    const vm = new Vue({
        el:'#app',
        data:{
            name:'xiaonan',
            age:20
        },
        beforeCreate() {
            console.log(this.name)//undefined
            console.log('beforeCreate')
        },
        created(){
            console.log(this.name)//xiaonan
            console.log('created')
        },
        beforeMount() {
            console.log(this.$el, 'beforeMount')//<div id="app">{{name}}</div>
        },
        mounted() {
            console.log(this.$el,'mounted')//<div id="app">xiaonan</div>
        },
        beforeDestroy() {
            console.log('beforeDestory')
        },
        destroyed() {
            console.log('destroyed')
        },// vm.$destroy()
		  // beforeDestory
		  // destroyed
    })
</script>
```

------------------------------------------

# 计算属性和侦听器

## 计算属性

对于任何复杂逻辑，你都应当使用**计算属性**。

```js
computed: {
    person(){
        console.log('computed')
        return `姓名：${this.name},年龄：${this.age}`
    }
},
```

放在`computed`里的属性与值，都会存放都缓存里，再次复用的时候，会直接从内存里取，如果值更改了，也会进行相应的修改。

另一种写法：

```js
computed: {
    person:{
        get(){ //一开始就执行
            return `姓名：${this.name},年龄：${this.age}`
        },
        set(val){ //数据更改时执行 但是不会真的修改成功，只是会触发此函数
            console.log(val)
        }
    }
},
```





## 侦听器

Vue 提供了一种更通用的方式来观察和响应 Vue 实例上的数据变动：**侦听属性**。当你有一些数据需要随着其它数据变动而变动时，你很容易滥用 `watch`——特别是如果你之前使用过 AngularJS。然而，通常更好的做法是使用计算属性而不是命令式的 `watch` 回调。

侦听器的使用：

```html
<div id="app"> {{person}} </div>

<script>
    const vm = new Vue({
      el:'#app',
      data:{
        name:'xiaonan',
        age:20,
        person:''
      },
      watch:{//侦听器
        name(newVal){
          this.person= `姓名：${this.name},年龄：${this.age}`
        },
        age(){
          this.person= `姓名：${this.name},年龄：${this.age}`
        }
      }
    })
  </script>
```

使用了侦听器，一开始并不会渲染页面，页面会出现空白，只有观察她的值更改了才会执行。	

解决这个问题，可以配合`methods`里的方法在钩子函数`mounted`挂载后自动执行。

```js
mounted() {
    this.getPerson()
},
methods:{
    getPerson(){
        this.person= `姓名：${this.name},年龄：${this.age}`
    }
},
watch:{
    name(){
        this.person= `姓名：${this.name},年龄：${this.age}`
    },
    age(){
        this.person= `姓名：${this.name},年龄：${this.age}`
    }
}    
```

侦听器的另一种写法：

```js
watch:{
    name:{
        handler(){
            this.person= `姓名：${this.name},年龄：${this.age}`
        },
        immediate:true //立即渲染页面
    },
}
```

在实例之外，使用vm.$watch，也能修改实例中name的值。

```js
vm.$watch('name',()=>{
	console.log('name')
},{
	immediate:true
})
```

## 计算属性和侦听器的区别

- 计算属性computed可以一次监听多个属性，侦听器就只有一个属性，一个属性的定义才能监听。
- computed可以生成新的数据，在一开始渲染页面，watch观察的是本身存在的数据，需要额外配置immediate值才行。
- computed里，无法进行异步操作，因为会在一开始就把属性存进缓存里，无法在单线程里返回执行异步。

---------------------------------

# 变量查找顺序

![1574331772761](C:\Users\Shaw\AppData\Roaming\Typora\typora-user-images\1574331772761.png)



# 组件

## 初步认识组件

### 全局组件

```html
<div id="app">
    <hello-world></hello-world>
    <kim></kim>
</div>
<script>
    Vue.component('hello-world',{ //也可以使用HelloWorld驼峰命名法
        data(){
            return{
                msg:'Hello World!!'
            }
        },
        template:'<h1>{{msg}}</h1>'
    })

    const vm = new Vue({
        el:'#app',
    })
</script>
```

### 局部组件

```js
 const vm = new Vue({
     el:'#app',
     components:{
         Kim:{
             data(){
                 return {
                     content:'局部组件',
                 }
             },
             template: '<h1>{{content}}</h1>'
         }
     }
 })
```



## 组件间的传值

### 组件数据传递&属性校验

首先来问一个问题：我们写好了一个组件，这个组件内部要展示的数据，是由谁来决定的？  是由这个组件来决定的吗？ 其实不是。 有的时候是我们在这个组件内部生命周期函数发起了一次请求，然后把数据拿回来放到自己的组件上进行展示。但是更多的 时候，都是在自己 的父组件上，进行数据请求，拿回来大部分的数据，然后再一个个的分给自己的子组件来进行数据的展示 。

### 父传子

![1574579433426](C:\Users\Shaw\AppData\Roaming\Typora\typora-user-images\1574579433426.png)

如何在父组件当中，把数据传递给子组件，子组件是如何使用这个数据，来进行数据展示的？

在Vue当中利用属性来传递。

举个栗子：这是html的内容

```html
<div id="app">
      <!-- 使用子组件 绑定父组件传来的属性 title和content -->
    <my-article :title="title" :content="content"></my-article>
  </div>
```

> 没有注册就直接使用的属性 会显示在页面的element上

这是js的内容

```js
const vm = new Vue({
      el: '#app',
      data: {
        title: '父组件的title',//父组件的数据，要在子组件中使用
        content: '父组件的content xxxxxxxxxx'
      },
      components:{
        myArticle:{//组件的名字
          props:['content','title'],//注册从父组件传过来的属性
          data(){
            return {
				//子组件的data数据存放	
            }
          },
          //子组件的dom结构 最外层一定要有一个标签包裹，不能很多标签并列
          template:`<div>
                      <h1>{{title}}</h1>
                      <p>{{content}}</p>
                    </div>`
        }
      }
    })
```

当然，我们可以规定一个属性是必须要有的，并规定其类型，可以将属性注册成对象。

```js
 props:{
     title:{
         type:String,
         required: true //父组件必须传入的
     },
 },
```

此时，如果传过来的值不是规定的值， 或者不传这个值，就会报警告。

还可以在子组件注册属性的时候设置默认的值：default

```js
content:{
    type:String,
    default:'这是子组件默认的content'
}
```

如果我们选择type为引用类型的值，又定义其默认值的时候，default需要是一个函数，最终显示的值是函数返回的值。

```js
arr:{
    type: Array,
    default: () => [1,2,3] //返回的值，就是arr变量在模板里最终显示的值 （通过自己的作用域返回一个数组）
}
```

属性校验：对传入的值进行验证

```js
validator(value){ //接收一个参数就是要校验的值
    return // 如果返回得结果是true，就是校验成功 false就是校验失败
}
```

比如：

```js
validator(value){ 
    return value >= 10 // 如果value>=10的话才会验证成功
}
```

#### $attrs

我们通过父组件传递值给子组件，子组件并不一定全部使用，会有一部分继续传递给孙子组件，但是如果当前子组件并不需要多余的属性，可以不进行接收注册，我们在会使用到这些继续传递的数据的孙子组件身上，通过`$attrs`方法来使用未注册的属性。

举个例子：

```js
const vm = new Vue({
      el:'#app',
      data:{
        title:'this is title',
        content:`contentxxxxxxxxxxxxxxxxxxxxxxxxx` //来自父组件的content
      },
      components:{
        myContent:{
          props:['title',],//子组件注册title，不适用content
          created(){
            console.log(this.$attrs) //查看子组件未注册的、来自父组件的属性 
          },
          template:`<div>
                      <h1>{{title}}</h1>
                      <my-p :content="$attrs.content"></my-p>   //孙子使用$.attrs中的未注册属性
                    </div>`,
          components:{
            myP:{
              props:['content'],//注册属性content
              template:`<p>{{content}}</p>`//使用content
            },
          }
        }
      }
    })  
```

这里就在子组件的模板里，找到孙子组件`<my-p :content="$attrs.content"></my-p>`，通过`$attrs.content`使用为注册的属性。

这个方法比较麻烦，我们可以使用`v-bind`来将所有未注册的属性绑定 到该组件身上。

```html
<my-p v-bind="$attrs"></my-p>
```

但是这样会遇到一个问题，就是未注册的属性和值会在dom标签上

这个问题我们可以使用`inheritAttrs`，来解决。

```js
inheritAttrs:false,
```

这样没有注册的属性也不会显示在行间了。

我们可以通过`$parent`和`$children`来相互让组件进行传值。但是官方不建议我们这么使用，效率也比较低，可以应急。

#### provide和inject

父组件通过provide提供给组件需要的值，子组件可以通过inject去注册使用

```js
 const vm = new Vue({
      el:'#app',
      provide:{//父组件提供
        title:'this is title',
        content:`contentxxxxxxxxxxx`
      },
      components:{
        myContent:{
          inject:['title'],//子组件注册使用
          template:`<div>
                      <h1>{{title}}</h1>
                      <my-p></my-p>
                    </div>`,
          components:{
            myP:{
              inject:['content'],//孙子组件注册使用
              template:`<p>{{content}}</p>`
            },
          }
        }
      }
    })  
```

不过呢，还是不推荐这种方式来进行传值，我们只有在进行封装库的时候，才会用provide和inject，因为我们不知道它是哪来的。

### 子传父

子组件如何向父组件传递数据？也就是，父组件如何获取子组件里的数据？

通过$children

```html
 <div id="app">
    <my-cmp></my-cmp>
  </div>
  <script>
    const  vm = new Vue({
      el:'#app',
      mounted() {
       this.msg= this.$children[0].msg;//父组件通过$children拿到子组件集合，[0]是当前子组件
       this.$children[0].cmpFunc()
       console.log(this.msg)//输出子组件的hello world
      },
      components:{
        myCmp:{
          data(){
            return {
              msg:'hello world'//子组件的值
            }
          },
          methods:{
            cmpFunc(){
              console.log( 'cmp')
            }
          },
          template:`<div>i am a cmp</div>`
        }
      }
    })
  </script>
```

不过我们说了，我们不推荐这个$children来查找子组件。

#### 引用 ref

我们可以把引用使用到dom或者组件上，然后我们通过`this.$refs`，这个方法使用。

使用再dom上

```html
<div id="app">
    <div ref="dom">i am a div</div>
  </div>
  <script>
    const  vm = new Vue({
      el:'#app',
      mounted() {
        console.log(this.$refs.dom)
      },
    })
  </script>
```

使用在组件上

```html
<div id="app">
   <my-cmp ref="cmp"></my-cmp>
  </div>
  <script>
    const  vm = new Vue({
      el:'#app',
      mounted() {
        console.log(this.$refs.cmp)
      },
      components:{
        myCmp:{
          template:`<div>i am a cmp</div>`
        }
      }
    })
  </script>
```

使用ref需要注意的地方

一个dom或者组件只能使用一个ref值，重复的ref值会导致覆盖。

比如：下面两个重复的div上绑定了相同的`ref="dom"`，这样页面上只显示第二个div

```html
<div id="app">
    <div ref="dom">i am a div</div>
    <div ref="dom">i am a div</div>
  </div>
```

但是设置for循环的话将会放到一个数组里。

```html
<div id="app">
    <div ref="dom" v-for="item in 5">i am a div</div>
  </div>
```

#### 绑定属性

**子组件使用父组件的方法**，通过绑定属性来获取。

```html
 <div id="app">
    <my-cmp :func="func"></my-cmp> 
     <!-- 将属性绑定在子组件上 -->
  </div>
  <script>
    const  vm = new Vue({
      el:'#app',
      methods:{
        func(data){//父组件的func方法
          console.log(data)
        }
      },
      components:{
        myCmp:{
          props:['func'],
          data(){
            return {
              msg:'hello world'
            }
          },
          methods:{
              handleClick(){
                this.func(this.msg)//子组件使用父组件的func方法
              }
          },
          template:`<div>i am a cmp
                      <button @click="handleClick">点击</button>
                    </div>`
        }
      }
    })
  </script>
```

那如果不通过绑定属性的方式呢？

组件可以通过使用修饰符`.native`来获取原生对象。

```html
 <div id="app">
    <my-cmp @click="func"></my-cmp> 
  </div>
```

$listeners可以获取所有通过v-on绑定的属性









## 安装V-CLI

```js
npm install -g @vue/cli
```

## 创建一个项目

```
vue create 项目名
```

然后

```js
//进入目录
cd 项目名
//运行项目
npm run dev
```







