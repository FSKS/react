# ReactJS Introduction

## 玩转ReactJS还仅仅是HTML、CSS、JS吗？NO！！！
  1. ES6、ES7
  2. Immutable-js
  3. JS函数式编程
  4. Redux
  5. Android
  6. iOS
  7. ....  
  

## ReactJS是神马？

  React不是框架，仅仅是一个MVC中的View，通过改变自己的state或父组件改变它的属性来触发更新，基于虚拟DOM更新相应的真实DOM。
   
   1. JUST THE UI
   2. 虚拟DOM
   3. 数据流
   
  
## 主要模块
  1. 组件化
  2. 虚拟DOM
  3. 数据流
  4. 生命周期
  
## 组件化

  - React之美于组件化。
  
## 虚拟DOM

  - 天下武功唯`快`不破。
  
  - 虚拟DOM是一个纯粹的JS对象。组件的DOM结构映射到虚拟DOM上面，React通过自己的Diff算法找到需要更新的DOM节点
  ，再把这个修改更新到实际的DOM节点上面。
  

## 数据流

  - `单向数据绑定、单向数据绑定、单向数据绑定` 重要的事情说3遍。（－>逗我的吧！你确定不是双向绑定？）
  自己项目中慢慢体悟吧。

## 生命周期

 ````
    // 简单DEMO
    class Counter extends Component {
      constructor(props) {
        super(props);
        this.state = { count: props.initialCount };
      }
    
      render() {
        // ...
      }
      
      componentWillReceiveProps: function(nextProps) {
          this.setState({
            likesIncreasing: nextProps.likeCount > this.props.likeCount
          });
      }
    }
    
    Counter.defaultProps = { initialCount: 0 };
 
 ````
  
 - *getInitialState*
 
     - 参考上面代码中的构造函数你很容易发现，该方法只有在初始化方法的时候调用，之后的任何属性或状态
     的变化都不会再调用该方法。
 
 - *getDefaultProps*
     - Counter.defaultProps 原来是个`静态变量`，每个实例对象共用的。这个组件创建的时候就有了，都不需要等到初始化。
 
 - render
 
     - 必不可少的
     - 可以返回null 或者 false

 - componentWillMount（装载组件）

    - 又名beforeRender
    - 只会执行一次
    - `改变state不会增加一次render`
    
 - componentDidMount（装载组件）
     
    - 又名afterRender
    - 只会执行一次
    - 可以进行DOM操作
    - 可以发送请求获取数据
 
 - **componentWillReceiveProps**（更新组件）
     - `this.props`获取旧的props
     - this.setState()不会增加一次新的渲染
 
 - **shouldComponentUpdate**（更新组件）
 
     - 返回布尔类型。true：重新渲染， false：不重新渲染。发现没有，你可以控制渲染。是不是有点小鸡冻，你想做些什么的呢？
     - 切记，决定组件的性能
     
 - componentWillUpdate（更新组件）
 
     - 更新组件之前执行
     - **不要改变state或props**
 
 - componentDidUpdate（更新组件）

     - 更新组件之前执行
     - **不要改变state或props**      
 - componentWillUnmount
 
     - 销毁组件触发
     - 你可以执行清理工作，解除事件绑定，clearTIme

## References

  1. [React 入门教程](https://hulufei.gitbooks.io/react-tutorial/content/)
  2. [ImmutableJS 官网](https://facebook.github.io/immutable-js/)
  3. [ECMAScript 6 入门 阮一峰](http://es6.ruanyifeng.com/)
  4. [JS函数式编程思想](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/)
