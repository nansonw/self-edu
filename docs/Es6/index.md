
# Promise
## 一 同步异步，阻塞和非阻塞 
 误区:同步不一定是阻塞，异步也不一定是非阻塞的。他们之间有四种组合，在js中通常只是有以下两种情况
同步阻塞
异步非阻塞
小结：以图书馆借书为例。同步和异步是一种消息通知机制，阻塞和非阻塞是一种
同步（图书管理员【响应者】是被动地进行消息通知）
和异步（图书管理员是主动地进行消息通知）是对"图书管理员的描述"，
阻塞（借阅人【调用者】期间周期性地进行等待和询问）和
非阻塞（借阅人期间可以进行其它活动，接收消息通知后再跟进处理）是对'借书者'的描述。

## 二 Promise 详解
 1 什么是promise:Promise是一个类，它是为了解决回调地狱出现的。它是对异步操作的封装。
 i)它具有三种状态 pending resolved(fufilled) rejected,它的初始状态是pending,一旦它的状态改变了，就不可逆
 ii) resolved 对应 then(), reject 对应catch,pending状态的改变可以由接口响应来决定，也可以手动调用resolve,reject来改变，实例化promise resolve 之后的代码依旧可以执行，resolve,reject 不等同于return
 iii) Promise类一旦实例化，那么在它异步操作前的所有的前序操作会立马执行。
 iv) promise实例的方法。then() catch() finnally()
    入参：成功回调函数，失败回到函数 接收两个函数作为参数
    then(function(res){//成功回调

    },function(err){//失败回调

    });
    返回值:then的调用和返回都一定是promise对象。这为链式调用提供了遍历。
        @1 then的回调函数参数不主动返回, 默认返回一个resolved的promise对象.
        @2 then的回调函数参数返回非promise值，如 return 111,默认也返回一个resolved promise对象
        @3 then的回调函数参数返回promise对象。如 return new Promise(),返回一个promise对象，状态由返回promise决定。
        所以说 then函数的返回值一定是promise对象，promise对象的状态由其函数参数的返回值决定。
        then函数的调用者和返回值都是一个promise对象。
        
 ```
 let p = new Promise((resovle,reject)=>{
    ...//异步操作的前序操作
    //以下是异步操作
 })
 ```


# Vue框架简易
```
简易版mvvm框架
<html>
<head>
    <title>自定义实现简易Vue</title>
    <script src='./Jvue.js'></script>
</head>
<body>
    <div id='app'>
        {{message}}
    </div>
</body>
<script>
    var vm = new Jvue({
        el:'#app'
        data:{
            message:'hello'
        }
    });
</script>
###Jvue.js
/*
事件监听和触发的接口: EventTarget
*/
class Jvue extends EventTarget{
    constructor(options){
        super();//继承事件接口
        //读取配置项保存私有属性
        this.$options = options;
        //匹配差值表达式和占位符，替换为options data里面的实际值等操作。
        this.compile();
        //vm 观察者模式实现数据驱动视图更新。数据需要预定义在 配置项data里面。
        this.observe(this.$options.data);
        //this.observeP(this.$options.data);//or proxy 
    }   
   /*
   @desc:指定作用域
   */
    compile(){
        let el = document.querySelector(this,$options.$el,null);
        this.CompileNode(el); 
    }
    /*
    @desc:mustacle值替换为实际值（递归地进行匹配替换）
    @DOM 属性
    1. node.childNodes 返回当前元素的子节点nodeList 类数组
    2. node.nodeType 返回节点类型的数值映射表示
       元素节点：1;属性节点:2 ; 文本节点：3; 注释节点:8;
    3. node.nodeValue 元素节点的值   
    4. RegExp 可以访问静态分组匹配后的变量。
    5. node.textContent 节点的文本内容。
    6. pattern.test(targetStr) [目标字符串是否通过校验] / targetStr.replace(patternReplaced,toReplace)[用toReplace替换目标字符串中的匹配内容]; 
    7. 当正则表达式中包含变量时，不能用字面量的形式进行创建，需要使用正则表达式的构造函数进行创建。RegExp
    8. EventTarget (Event,CustomEvent(自定义事件的分发:dispatchEvent[vue 语法糖$emit] 和 自定义事件的监听:addEventListener)
    */
    compileNode(el){
        //获取作用域下的所有子节点
        let childNodes = el.childNodes;
        childNodes.forEach(node=>{
            if(node.nodeType === 1){//标签、元素
                if(node.childNodes.length > 0){
                    this.CompileNode(node);
                }
            }
            if(node.nodeType === 3){
              let patternMustacle = /\{\{\s*(\S+)\s*\}\}/mg;
              if(patternMustacle.test(node.textContent)){
                let name = RegExp.$1;
                node.textContent = node.textContent.replace(patternMustacle,this.$options.data[name]);
                this.addEventListener(name,e =>{
                    let oldVaule = this.$options.data[name]
                    let reg = new RegExp(oldValue);//当正则的主体内容是一个变量时，不能用字面量的形式。
                    node.textContent = node.textContent.replace(reg,e.detail);
                })
              }
            }
        })
    }
    /*
    @description :对象代理实现
    不是监听data选项里面的所有属性，而是监听data选项本身。
    */
    /*
    observeP(data){
        let _this = this;
        let newObj = new Proxy(data,{
            get(target,key){
                return target[key]
            },
            set(target,key,newValue){
                target[key] = newValue;
                let customEvent = new CustomEvent(key,{
                    detail:newValue
                });
                _this.dispatchEvent(cusomEvent)
                return true;
            },
        })
        this.$options.data = newObj;
    }
    @description:观察者模型 数据劫持 
     1 自定义事件

     2 Object.defineProperty(targetObj,objProp,{

     }) 
     @用来新增或者修改对象属性。用来新增对象属性时，属性描述器"{}"里面的一些默认属性值得注意
     @ [如configurable的缺省值是false,无法删除操作,enumerable的缺省值是false,无法遍历操作]
    */
    observe(data){
        let keyArray = Object.keys(data);
        let that = this;
        keyArray.forEach(value=>{
           that.definedReactive(data,value,data[value])
        })
    }
    /*
    @desc:数据劫持实现关键
    */
    defineReactive(data,key,value){
        let _this = this;
         Object.defineProperty(data,value,{
                configurable:true,//默认false 无法删除
                enumerable:true,//false时,不可被遍历
                get(){
                    return value;
                },
                set(newValue){//设置新值时传入。
                    value = newValue;
                    //要触发重新渲染
                    let customEvent = new CustomEvent({
                        detail:newValue
                    });
                    _this.dispatch(customEvent);
                }
        })
    }
}
</html>
```
