## 浏览器渲染原理上[简介]
```
1 浏览器内核 = 浏览器渲染引擎 + JS引擎 
 一般而言,内核就倾向于只指浏览器渲染引擎。
-----------------------------------------------
2 主流浏览器的内核(渲染引擎):
  Chrome(Blink)
  Safari(Webkit)
  IE(Trident)
  FireFox(Gecko)
  Opera(Blink)
-----------------------------------------------
 五大主流浏览器目前都是单内核的模式,即只有一个渲染引擎;国内的不少浏览器具有多个内核,也就是有多个渲染引擎.
 Blink是基于Webkit派生而来,是Webkit的一个分支.
 所以根本来说,只有Trident,Gecko,Webkit三大内核(渲染引擎)

 3 从浏览器输入url到页面返回内容,发生了什么
    域名解析->请求发送->请求响应->内容显示
-----------------------------------------------
    3.1 浏览器根据DNS服务器得到域名的ip地址(解析)
    3.2 向这个ip地址发送http请求(请求)
    3.3 服务器接收,处理并返回这个http请求(响应)
    3.4 浏览器得的返回内容并显示.(显示)

 4 详解"3.4浏览器得到返回内容并显示的详细过程" [三件套处理->渲染树构造->操作系统绘制页面]
   "控制权在GUI渲染线程与JS引擎线程的更迭"
    4.1 三件套处理
    1)解析html得到html DOM树;
    2)解析css得到css规则树
    3)js加载完毕后,js通过相应的对象模型API(DOM/CSSOM)操作DOM树(DOM Tree)和 css规则树(CSS Rule Tree).控制权的交接
    4.2 渲染树构造
     Rendering Tree  < = DOM Tree + CSS Rule Tree
        渲染树只会包括需要渲染的节点和相应节点的样式.
     CSS Rule Tree:
        CSS Rule Tree的作用是进行元素匹配和样式应用.
    4.3 回流和重绘
      1)回流(重排) reflow:在正常文档流中的元素空间属性改变引起的元素重新渲染.
        文档流中的元素 位置属性 发生了改变.浏览器会重新渲染发生位置改变的元素. 
        回流发生的场景:元素位置属性发生变化 / 页面首次渲染.
        
      2)重绘 repaint:在正常文档流中的元素装饰属性改变引起的元素重新绘制
        文档流中的元素 装饰属性 发生了改变.不会重新渲染元素,而是将变化后的装饰属性直接应用到元素上并重新绘制.
        重绘发生的场景:元素装饰属性变更引起的元素重新绘制.

      3)回流/重绘对页面性能的影响
        当前元素发生回流时会关联其父元素和子孙元素也发生回流.性能影响较重绘大.
        现代浏览器会在允许条件下合并回流和重绘,将多次回流或重绘合并成一次,这是浏览器默认的优化机制.(浏览器默认的回流重绘性能优化机制)

      4)(回流和重绘关联的)性能优化,降低影响
         1 减少回流/重绘,不回流
           i)将动画效果应用到position属性为absolute或fixed的元素上(脱离文档流,不引起父元素和子孙元素回流,元素本身回流)
           ii)避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性。
           iii)避免频繁操作DOM，创建一个documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中。
           iv)也可以先为元素设置display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘。

    4.4 JS和CSS是否会阻塞页面的显示(为什么讲css放头部,js放底部能提高页面性能)
        结合页面渲染的完整过程来说
        解析html,生成DOM Tree
        解析css生成CSSOM Tree

        js可以操作DOM Tree 和 CSSOM Tree
        将DOM Tree 和 CSSOM Tree 结合成Rendering Tree
        Rendering Tree 渲染绘制
        i)Css不会阻塞DOM解析,但是会阻塞页面渲染.因为页面渲染需要在DOM 和 CSSOM 组合成 Rendering Tree之后进行.
        DOM解析和CSS解析是并行的(解析是并行的).[link标签引入css资源时在火狐浏览器中是异步加载的，在谷歌浏览器中是同步加载的。但如果是通过style标签引入样式，则不论何种浏览器，均为同步加载。]

        DOM 解析 DOM Tree 
                            -- Rendering Tree -- 页面渲染绘制
        CSS 解析 CSSOM Tree 

        ii)Js会阻塞DOM解析(默认script标签行为). 因为js可以操作DOM,浏览器预下载后执行完js,才会继续去解析DOM.这也是js通常放在body结束标签的原因,这时候DOM解析已经完成了.script 添加了 async 或者 defer 属性会改变script这样的默认行为.

        GUI渲染线程与JS引擎线程是互斥的
        JavaScript的加载、解析与执行会阻塞DOM的构建.
        script async 与 defer的区别于联系
            联系:脚本下载都是异步的,不会影响到DOM解析
            区别:async 加载完立即将控制权交给js引擎线程,执行js,有可能影响dom解析,load之前执行.
                 defer 加载完后延迟执行,DOMcontentLoaded 之前执行,不影响dom解析

        小结:异步加载,加载完立即执行的是async
             异步加载,延迟(视dom解析和脚本加载完成需要时间)执行的是defer

        iii)css会阻塞js执行.css和js的加载是并行的,由于js 是操作CSSOM Tree的,因此js执行需要等待CSSOM的生成.

        iv)js会触发页面渲染.由于js是操作DOM Tree的,所以需要先有操作对象
           浏览器在解析时,如果遇到了script标签,会先渲染一次这个script标签之前的DOM,然后再去加载和执行js

        小结:
        css->js->html(css直接影响js执行,(默认情况下)js下载解析执行会直接影响html解析,从而导致css也间接影响了html解析)
        -------------
        css->会阻塞DOM渲染,不会阻塞DOM解析
        css->会阻塞js执行
        -------------
        js->会阻塞DOM解析,自然会阻塞DOM渲染(解析完毕再渲染)
        js(script标签)会触发页面渲染.在script标签之前的渲染,在script标签对之后的阻塞.
        -------------
        浏览器的预加载支持
        1 显式预加载
          <link rel="preload" href="style.css" as="style" />
          <link rel="preload" href="main.js" as="script" />
          <link rel="preload" href="http://km.midea.com/?/font.woff" as="font" crossorigin>
        2 默认预加载
        浏览器解析DOM时，会一行一行向下解析，但是它可以预先加载具有引用属性的外部资源(script,link,img),解析到此类标签时,则无需再去加载,直接运行,以此提高运行效率

        解析HTML,生成DOM Tree,会触发DOMContentLoaded事件.
        所有静态资源加载完毕(html,css,js)会触发Load事件.
        同步脚本优先于异步脚本执行,涉及到宏微任务和事件循环.
        
    4.5 操作系统API绘制页面内容(GUI)
 ```
  ## 浏览器运行原理,JS运行机制[下]
  ```
  1 进程和线程
  
  ```
  
