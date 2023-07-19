## css 起点
### 一 DOCTYPE回顾
```
<!DOCTYPE>
1 决定解析html文档使用什么方式。（标准模式、怪异模式）
2 影响css和js的解析。（
 2.1 文档类型是标准模式下的盒子模型是content-box;
 2.2 文档类型是怪异模式下的盒子模型是border-box
 ）
3 盒子模型
 3.1 盒子有两种规格类型，但是都由四个模块构成
  3.1.1 content区
  3.1.2 padding区
  3.1.3 border区
  3.1.4 margin区

 3.2 content-box:文档类型是标准模型时的默认盒子模型。
 3.3 border-box:文档类型是怪异模型时（IE模型）的默认盒子模型。
 3.4 两种默认盒子模型都可以通过css属性显式地进行切换：box-sizing属性
 3.5 两种盒子模型的区别在于它对宽度的理解上不同,但是对于高度的理解是相同的。
  3.5.1 标准盒: width === content 
  width 就是元素的content的width,不会因为它的padding,margin,border等元素的调整，而受到影响。
  3.5.2 IE盒:width 是 content + padding + border

 3.6 js获取盒子的宽/高。dom表示元素节点对象
  ###名词形式的API 具有兼容和不准确的局限性
  3.6.1 dom.style.width/height 只能获取行内宽高,其它处定义的样式无法获取.
  3.6.2 dom.currentStyle.width/height 最终渲染后的宽和高，只有IE支持此属性
  ### 动词形式的准确性和兼容性强
  3.6.3 window.getComputedStyle(dom).width/height 最终渲染的宽高，兼容性强。
  3.6.4 window.getBoundingClientRect(dom).widht/height 最终渲染的宽度，兼容性强。
```
### 二 外边距重叠
```
2.1 出现的场景：默认情况下只有在正常文档流下(默认处于相同BFC)的两个垂直方向上的盒子会发生外边距合并。
2.2 具体描述：垂直方向上的外边距取其中的margin大值，作为最终的外边距。称之为外边距重叠或者外边距合并。
行内框，[浮动框，绝对定位 脱离了正常文档流]的外边距不会合并。

父子和兄弟外边距合并，只会在垂直相向的情况下进行。值如果是同符号的，取绝对值大的，如果是相反符号的取
绝对值的差值。


```
### 三 *FC(格式化上下文)
```
*FC:独立的渲染区域，独立的渲染规则，规定了其子元素的定位和与其它元素的关系与相互作用。

BFC 
3.1 默认的BFC
3.2 显式地主动创建BFC:给当前元素包裹上父元素，设置父元素的pdfo属性。
    3.2.1 position属性不设置为static relatvie
    3.2.2 display属性设置为table,table-caption,table-cell,inline-block.
    3.2.3 float属性不设置为none
    3.2.4 oveflow属性不设置为visiblie
3.3 BFC的应用：
    3.3.1 通过设置不同的bfc消除垂直方向外边距合并.同一个bfc的垂直方向上的直接子元素外边距合并，不同bfc的垂直方向上的外边距不合并。
    <div style='overflow:hidden'>
        <p>1</p>
        <div style='overflow:none'>
            <p>2</p>
        </div>
        <p>3</p>
        <p>4</p>
    <div>
    此时 1,3,4 在同一个bfc区域,2在另外的一个bfc区域,2和1,2和3在垂直方向上的边距就不会取两两之间的大值，而是会累加。

    3.3.2  清除浮动元素的影响。（对后继弟弟元素的影响和父元素的影响）
    1)浮动元素对直接后继弟弟元素的影响
    浮动元素高度坍塌(脱离文档流，后面元素内陷到原本属于浮动元素的空间，造成浮动元素飘，弟元素内陷的现象);
    解决：操作后继弟弟元素设置它成为一个新的bfc.独立隔离开来，
    
    2)浮动元素高度大于父容器时默认会溢出父容器
    容器内包含浮动元素，计算容器高度时要想使得浮动元素参与到容器高度的计算中，则容器元素要成为一个独立的bfc
    因为bfc决定了容器内部元素的渲染方式和相互作用。
    解决：设置父元素为bfc，浮动元素也要参与到高度计算中来。不会溢出，而是自动扩充父元素高度。

    3）浮动元素具有inline-block的特性。inline-block 的 inline特性指的是元素默认宽高是元素的宽高，inline-block
    的block特性是可以手动设置宽高;
    inline元素不能设置元素的宽高inline,它的默认宽高是元素自身的宽高，是只读的。
    block元素可以设置元素的宽高，默认宽度是元素的100%,高度是元素的高度。
```    
### 四 选择器
```
单个选择器的优先级
行内>id>class>标签
A>B>C>D 
(A,B,C,D) => ABCD 其中每个位置的值为相应选择器出现的次数。
-----------------
1 标签选择器
2 类选择器
3 id选择器
4 "*"选择器
----------------
5 属性选择器::"匹配属性或者属性名值对满足特定条件的标签"。
  
  [title]{//具有titlte属性的标签，文字样式是蓝色
    color:blue
  }
  [propName = value]{//满足正则匹配的标签应用上样式

  }
6 伪元素选择器:
  ::befor 
  ::after
  ....
7 伪类选择器:"匹配满足特定场景/状态的标签".
  #表示文档结构顺序的伪类
  :root
  :nth-of-type()
  :nth-child()
  :first
  :first-child
  :first-of-type
  :last
  :last-child
  :last-of-type
  ......
  #表示用户网页交互场景的伪类
  :checked
  :active
  :hover
  :visited
  :disabled
  :focus 
  ...
 
8 组合选择器
  8.1 横向关系:a和b平级
  a,b{//a和b平级,a和b都应用这个样式规则

  }
  8.2 纵向关系:a是b的上层
  a b{//满足条件（b是a的后代元素）的b元素应用这个样式规则

  }
  a>b{//满足条件（b是a的直接子元素）的的b元素应用这个样式规则

  }
  a+b{//满足条件(b是a的直接弟弟)的b元素应用这个样式规则

  }
  a~b{//满足条件（b是a的兄弟）的b元素应用这个样式规则

  }
```
### 五 常见的布局方案

#### 水平居中布局
以块级布局容器div为例
```
1) 独立居中,通过借助块级元素自身的"margin:0 auto"样式设定实现居中
<div id='test'>我很独立</div>
#test{
  width:280px;//默认铺满可视区域的宽度
  height:280px;
  margin: 0 auto;
}

2) 依存居中,父子组合
<div class='father'>
  我是父容器
 <div class='son'>
    我是子容器
 </div>
</div>

# 依存1,通过借助flex容器的flex-item排列方式实现居中。 
.father{
  display:flex;
  justify-content:center;
}
.son{
  width:280px;
  height:280px
}
# 依存2,通过借助行内元素text-align:center实现元素居中
.father{
  text-align:center
}
.son{
  dispaly:inline-block;//同时具有两者的特性
  width:280px;
  height:280px;
}
#依存三，abosulte定位是相对于上一个祖先元素（不是static定位的祖先元素）的定位
.father{
  position:relative//相对于自身的定位
}
.son{//水平中垂
  width:280px;
  height:280px;
  position:absolute;
  left:50%;
  top:50%;
  transform:translate(-50%,-50%);
}

```
#### 垂直居中布局
#### 水平垂直居中布局

### 六 CSS重点理解
#### position 定位
```
除了static以外，定位属性都是相对某个参考系的定位,只是存在参考系的不同。

正常文档流:（默认文档流）中，块级元素从上到下纵向排列，行内元素从左到右横向排列。元素都占据文档空间
脱离文档流: 浮动属性（float:left/right）和定位属性（absolute/fixed）
都会使得元素不再受到父元素的限制，实现更精细的有层级的定位。
------------------------------
# float vs position 脱离文档流
float布局具有脱离文档流的特性，但是依旧占据一定的文档空间，使得四周元素环绕浮动元素布局排列。
（不完全脱离文档流）,浮动脱离文档流的元素层级和普通元素的层级相同，定位脱离文档流的元素层级
和普通元素的层级不同
------------------------------
定位属性元素脱离文档流不影响四周元素。不占据文档空间（完全脱离文档流）


6.1 static （top/right/bottom/left z-index）
 默认的定位方式，css的方位属性和层级属性和这个定位的组合无效。

6.2 relative 相对定位，相对于元素自身的定位。相对定位的元素仍然会保留其在文档流中的空间，其他元素不会占据其位置。不脱离正常文档流，占空间。（top/right/bottom/left z-index 组合有效）

6.3 absolute 绝对定位，相对于上一个非static祖先级的元素进行定位，如无此元素,则相对于body进行定位。会脱离文档流，不保留在文档流中的位置。（top/right/bottom/left z-index 有效）

6.4 fixed 固定定位，相对于浏览器窗口（左上角）进行定位，页面滚动，元素在特定位置上显示。会脱离文档流
不保留在文档流中的位置（top/right/bottom/left z-index 有效 。

6.5 sticky(relative/fixed) 元素在正常文档流中占据空间，但也可以在滚动时表现得像固定定位。必须给元素设置
一个方位属性
```
#### float布局（建议使用现代布局方案）


