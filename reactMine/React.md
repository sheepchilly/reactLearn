# 一.jsx

## 1.jsx的语法规则

**1.定义虚拟DOM时，不要写引号**

```js
const VDOM = (
    <h1>Hello world</h1>
)
```

**2.标签中混入JS表达式时要用{}**

```js
const VDOM = (
    <h1 id={myId.toLowerCase()}>
        <span>{myData.toLowerCase()}</span>    
    </h1>
)
```

**3.样式的类名指定不要用class，要用className**

```js
const VDOM = (
    <h1 className="title" id={myId.toLowerCase()}>
        <span>{myData.toLowerCase()}</span>    
    </h1>
)
```

4.内联样式，要用style={{key:value}}的样式去写（注意：{{}}外面没有双引号包裹）****

```js
const VDOM = (
    <h1 className="title" id={myId.toLowerCase()}>
        <span style={{color:'white',fontSize:'29px'}}>{myData.toLowerCase()}</span>    
    </h1>
)
```

**5.虚拟DOM必须只有一个根标签**（只有一个div根标签）

```js
const VDOM = (
    <div>
    <h1 className="title" id={myId.toLowerCase()}>
        <span style={{color:'white',fontSize:'29px'}}>{myData.toLowerCase()}</span>    
    </h1>
    <input type="text" />
    </div>
)
```

**6.标签必须闭合**

**7.标签首字母**

1. 若**小写字母**开头，则将改标签转为html中同名元素，若html中五该标签对应的同名元素，则报错。
2. 若**大写字母**开头，react就去渲染对应的组件，若组件没有定义，则报错。

**8.**注意区分 【js语句(代码)】与【js表达式】

1. 一个表达式会产生一个值，可以放在任何一个需要值的地方:arr.map()  function test(){}
2. 语句（代码）：if(){} for(){} switch(){case:xxx}

## 2.循环的写法

```js
const data = ['Angular','React','Vue']
const VDOM = (
    <div>
    <h1>前端js框架列表</h1>
    <ul>
        {
        data.map((item,index) => {
            return <li key={index}>{item}</li>
        })
    }
    </ul>
    </div>
)
ReactDOM.render(VDOM,document.getElementById('test'))
```

# 二.开发者工具

1.components：组件

2.profiler:性能监视

# 三.React面向对象

## 1.什么是组件？

- 包含代码和资源的集合

## 2.函数式组件

```js
//1.创建函数实组件
function Demo(){
    return <h2>我是用函数定义的组件（适用于[简单组件]的定义）</h2>
}
//2.渲染组件到页面
ReactDOM.render(<Demo/>,document.getElementById('test'))
/*
    1.react解析组件标签，找到了Demo组件
    2.发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实DOM，随后呈现在页面中
*/
```

## 3.类式组件

```js
//1.创建类式组件
class Demo extends React.Component{
    render(){
        //render是放在哪里的？ - Demo的原型对象上，供实例使用
        //render中的this是谁？ - Demo的实例对象
        return <h2>我是类定义的组件（适用于【复杂组件】的定义）</h2>
    }
}
//2.渲染组件到页面
ReactDOM.render(<Demo/>,document.getElementById('test'))
/*
    1.React解析组件标签，找到了Demo组件
    2.发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法
*/
```

# 四.组件实例三大属性

## 1.state

```js
//1.创建组件
class Weather extends React.Component{
    constructor(props){
        super(props)
        //state有很多个变量就写成对象
        this.state = {isHot:false}
    }
    render(){
        console.log(this)
        const {isHot} = this.state
        return <h1>今天天气很{isHot?'炎热':'凉爽'}</h1>
    }
}
ReactDOM.render(<Weather/>,document.getElementById('test'))   
```

## 2.react中如何绑定事件？

> eg.onClick={demo}

```js
//1.创建组件
class Weather extends React.Component{
    constructor(props){
        super(props)
        //state有很多个变量就写成对象
        this.state = {isHot:false,wind:'微风'}
    }
    render(){
        console.log(this)
        const {isHot} = this.state
        //1.如果{}里写的是{demo()} - 把demo的返回值赋值给了onClick作为回调
        //2.如果{}里写的是{demo} - 把demo函数交给onclick作为回调
        return <h1 onClick={demo}>今天天气很{isHot?'炎热':'凉爽'}</h1>
    }
}
ReactDOM.render(<Weather/>,document.getElementById('test'))

function demo(){
    console.log('标题被点击了')
}
```

## 3.如何修改类中state？ - setState

- 使用React.Component身上的原型对象中的setState去更新
- **注意：**使用setState进行更新，是一种合并不是替换（也就是说如果你的state中如果有多个属性，那么setState中会将新的state覆盖以前所有的state）

```js
<script type="text/babel"> /* 此处一定要写babel */
    //1.创建组件
    class Weather extends React.Component{

        //构造器会调用几次？ - 1次
        constructor(props){
            console.log('构造器')
            super(props)
            this.state = {isHot:false,wind:'微风'}
            this.nice = this.changeWeather.bind(this)
        }

        //render会调用几次？ - 1+n次 1是初始化的那次 n是状态更新的次数
        render(){
            console.log('render')
            const {isHot} = this.state
            return <h1 onClick={this.nice}>今天天气很{isHot?'炎热':'凉爽'}</h1>
        }

        //changeWeather会调用几次？ - 点几次调几次
        changeWeather(){
            /*
                changeWeather放在哪里？ - weather的原型对象上，供实例使用
                1.由于changeWeather是作为onClick的回调，所以不是通过实例调用的，是直接调用
                2.类中的方法默认开启了局部的严格模式，所以changeWeather中的this为undefined
            */
            console.log(this)
            const isHot = this.state.isHot 

            //注意：状态(state)不可直接更改，下面这行就是直接更改，要借助一个内置的API去更改
            // this.state.isHot = !isHot <- 错误的写法

            //严重注意：状态必须通过setState进行更新，且更新是一种合并，不是替换
            this.setState({isHot:!isHot})
        }
    }
    ReactDOM.render(<Weather/>,document.getElementById('test'))

    function demo(){
        console.log('标题被点击了')
    }

</script>
```

