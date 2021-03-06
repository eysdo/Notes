# CSS笔记

## 水平居中

+ 父级元素`text-align: center`，这种只对行内元素有效，而且后代元素会对这个元素进行继承，也就是说，所有的后代行内内容都会居中。

+ 元素`margin: 0 auto`，必须定宽，否则并且不能为`auto`值

+ 元素`display: inline-block`，父级元素`text-align: center`

+ 使用定位：注意，父元素和子元素都要定高。

  ```css
  .parent {
      height: 200px;
      width: 200px;  /*定宽*/
      position: relative;  /*父相*/
      background-color: #f00;
  }
  .son {
      position: absolute;  /*子绝*/
      left: 50%;  /*父元素宽度一半,这里等同于left:100px*/
      transform: translateX(-50%);  /*自身宽度一半,等同于margin-left: -50px;*/
      width: 100px;  /*定宽*/
      height: 100px;
      background-color: #00ff00;
  }

  ```

+ 使用`flex`布局：

  ```css
  .parent {
      display: flex;
      justify-content: center;
  }
  ```

## 垂直居中

+ 设置`行高`与`高度`的值相等，这只适用于行内元素

+ 对于图片：

  ```css
  #parent{
      height: 150px;
      line-height: 150px;
      font-size: 0;
  }
  img #son{
    vertical-align: middle;
  } /*默认是基线对齐，改为middle*/
  ```

+ 定位实现：

  ```css
  .parent {
    height: 150px;
    position: relative;
  }

  .son {
    height: 50px;
    position: absolute;
    top: 50%;
    tansform: translateY(-50%);
  }
  ```


## 解析CSS

```css
.contain div .main .left{
    color:red;
}
```

我们在书写CSS的时候，通过选择器给元素添加样式都是从一个比较大的范围来查找，逐渐精确，但是浏览器在解析CSS样式表的时候，顺序是相反的，他会先找到最精确的选择器，也就是大括号左边的选择器`.left`，然后对其他的选择器进行一步一步的验证，比如第二步是验证`.left`外层是否有一个`.main`类选择器，然后第三步再去验证`.main`外部是否有一个`div`元素元素选择器...之所以这么解析，是为了提高性能；当从广泛到精确进行解析时，如果`.contain`内部有很多`div`，那么会查询每个`div`内部是否有一个`.main`，这只是一个元素的样式表，一旦`css`的文件足够庞大，那么浏览器将花费很多时间来解析css，所以<b>浏览器是采取逆向解析验证的方式来解析css样式表的</b>

## 伪类与伪元素

```css
// 伪元素
.title::before {
    ...
}

// 伪类
.title:hover {
    
}
```

伪元素：不会出现在HTML中，也不会出现在DOM树中，但是是在页面中实际存在的，可以显示在页面上，伪元素可以有内容，也可以有样式。

伪类：表示一个元素的状态，不同状态可以设置不同的样式，比如鼠标悬停，或者活跃状态等

## 权重

一般而言，所有的样式会根据下面的规则层叠于一个新的虚拟样式表中，其中数字 4 拥有最高的优先权.

1. 浏览器缺省设置
2. 外部样式表
3. 内部样式表（位于 <head> 标签内部）
4. 内联样式（在 HTML 元素内部）

因此，内联样式（在 HTML 元素内部）拥有最高的优先权，这意味着它将优先于以下的样式声明：<head> 标签中的样式声明，外部样式表中的样式声明，或者浏览器中的样式声明（缺省值）。

!important：权重最大

ID选择器：权重100

类&属性&伪类：权重10

元素&伪元素：1

## 单位

`em`:相对于父元素的字体大小

`rem`:相对于根元素的字体大小

`px`:相对于屏幕分辨率

`vw&vh`:相对于视窗的高度与宽度，视窗的宽度和高度都是`100vw`,`100vh`,所谓的视区就是浏览器内部的可视区域大小。

## 非布局样式

### 字体

字体族(font-family)：表示显示的字体，可以赋值多个，浏览器会从前到后逐一寻找

使用自定义字体：

```css
@font-face {
    font-family: "IF";
    src: url("./IndieFlower.ttf");
}
.class-name {
    font-family: IF;
}
```

### 行高

行高会影响行内元素的样式，默认情况下，文本是按照文字的base-line对其的，一个行内元素的行高会将外边的inline-box撑起来。

### 背景

作为一个容器的铺垫而存在。background可以使用url赋值：

```css
.c1{
    height: 90px;
    background: url(./test.png);
}
```

`background-size:cover`可以让图片覆盖整个容器，然后图片的长宽比不变

渐变：

```css
.c2{
    height: 90px;
    background: linear-gradient(deg, color1, color2)
}
```

参数deg表示渐变方向，0deg表示从下到上渐变，方向是向右变化的。

`clip-path`裁剪：

```css
.container{
    // 方形裁剪
    clip-path: inset(100px 50px);
    // 圆形裁剪
    clip-path: circle(50px at 100px 100px);
}
```



雪碧图的写法：

```css
.class-name {
    width: 40px;
    height: 40px;
    background: url(./test.png) no-repeat;
    bacground-position: -50px -30px;
}
```

## 布局

### 盒子模型

一个盒子模型,包含了元素内容`（content）`、内边距`（padding）`、边框`（border）`、外边距`（margin）`几个要素。通常我们设置的背景显示区域，就是内容、内边距、边框这一块范围。而外边距`margin`是透明的，不会遮挡周边的其他元素。

- 盒子类型属性(box-sizing)

  1. `content-box`,默认值盒子的`width`属性与`height`只是盒子内容的属性,盒子的总高度和总宽需要算上另外的3个属性值.
  2. `border-box`,IE盒子,设置的`width`值其实是除`margin`外的`border`+`padding`+`element`的总宽度。盒子的`width`包含`border`+`padding`+`内容`

- 相邻盒子外边距叠加

  两个上下方向相邻的元素框垂直相遇时，外边距会合并，合并后的外边距的高度等于两个发生合并的外边距中较高的那个边距值.注意:只有普通文档流中块框的垂直外边距才会发生外边距合并。行内框、浮动框或绝对定位之间的外边距不会合并。

### 文档流

所谓的文档流，指的是就是元素排版布局过程中，元素会自动从左往右，从上往下地遵守这种流式排列方式。 如果我们将屏幕的两侧想象成河道，将屏幕的上面作为流的源头，将屏幕的底部作为流的结尾的话，那我们就抽象出来了文档流 ~水流流动的是水,我们这里所说的`HTML`文档流,流动的就是每个`HTML`文档中的元素.

- 块级元素&行内元素

  块级元素:就是个块,默认独占一行,可以设置宽高

  行内元素:不独占一行,挨个排列,一行排不下了,再排下一行,注意:有些行内元素是可以设置宽高的,比如`<input>`,`<img>`,`select`,`<textarea>`等.

- 标准文档那个流的特性:

  1. 无论多少个空格、换行、`tab`，都会折叠为一个空格。
  2. 高矮不齐，底边对齐：行内的各个元素高度不一样时,底边在一条直线上,至于高度,要看所有行内的元素中,高度最高的那一个.
  3. 自动换行,一行写不满,换行写.

我们所说的脱离文档流,就是让这个元素脱离本身的流容器,而在上面飘着.其他盒子在排版的时候会当作没看见它.

### position

- `static`

  `HTML`元素的定位属性的默认值,即,没有定位,元素还是在文档流之中,所以一个默认的盒子模型不会受到`top`,`bottom`,`left`与`right`属性的影响.

- `fixed`

  指元素位置相对于浏览器窗口的固定位置,不随窗口的滚动而滚动,该属性值会让该元素脱离文档流,会覆盖下方的元素.

- `relative`

  相对与该元素自己正常定位的定位,注意:拥有该属性值的元素不脱离文档流,所以移动前的空间会被保留,别的元素不能够占位.

- `absolute`

  相对于文档流中离他最近的一个已经定位父元素进行定位,如果该元素没有已定位的父元素,那么他的位置将相对于`<html>`

- `z-index`

  这个属性顺带提一下,这个属性必须作用在已经定位后的元素上.(这个属性需要更新)

### Flex布局

采用 Flex 布局的元素，称为 Flex 容器，它的所有子元素自动成为容器成员。

主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。容器内的子元素默认沿主轴排列。单个子元素占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。需注意使用flex容器内元素，即`flex item`的`float`，`clear`、`vertical-align`属性将失效。

容器的属性：

1. `flex-direction`：子元素排列方向（水平向左，水平向右，垂直向下，垂直向右）

   ```css
   .box {
     flex-direction: row | row-reverse | column | column-reverse;
   }
   ```

2. `flex-wrap`：子元素排列不下的时候，如何换行（不换行，第一行在上方，第一行在下方）

   ```css
   .box{
     flex-wrap: nowrap | wrap | wrap-reverse;
   }
   ```

3. `flex-flow`：上面两个属性的简写，默认值为`row nowrap`

   ```javascript
   .box {
     flex-flow: <flex-direction> || <flex-wrap>;
   }
   ```

4. `justify-content`：定义了项目在主轴上的对齐方式。（往左挤，往右挤，中间挤，两边靠边中间宽，分散排列）

   ```javascript
   .box {
     justify-content: flex-start | flex-end | center | space-between | space-around;
   }
   ```

5. `align-items`：定义项目在交叉轴上如何对齐（起点对齐，终点对齐，中点对齐，第一行文字对齐，默认值）

   ```css
   .box {
     align-items: flex-start | flex-end | center | baseline | stretch;
   }
   ```

子元素属性：

1. `order`属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
2. `flex-grow`属性定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
3. `flex-shrink`属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
4. `flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。
5. `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

### 网格布局

将属性 `display` 值设为 `grid` 或 `inline-grid` 就创建了一个网格容器，所有容器直接子结点自动成为网格项目。

```css
grid  {
    display: grid;
｝
```

网格项目按行排列，网格项目占用整个容器的宽度。

```css
grid  {  
    display: inline-grid;
｝
```

网格项目按行排列，网格项目宽度由自身宽度决定。

#### 行高与行宽

属性`grid-template-rows`和`grid-template-columns`用于显示定义网格，分别用于定义行轨道和列轨道。

```css
grid  {    
    display: grid;
    grid-template-rows: 50px 100px；
｝
```

属性`grid-template-rows`用于定义行的尺寸，即轨道尺寸。轨道尺寸可以是任何非负的长度值（px，%，em，等）。网格项目1的行高是50px，网格项目2的行高是100px。

```css
grid  {    
    display: grid;
    grid-template-columns: 90px 50px 120px；
｝
```

类似于行的定义，属性`grid-template-columns`用于定义列的尺寸。网格项目的第1列，第2列，第3列的宽度分别是 90px, 50px 和 120px 。

<b>fr</b>

```css
grid  {    
    display: grid; 
    grid-template-columns: 1fr 1fr 2fr；
｝
```

单位`fr`用于表示轨道尺寸配额，表示按配额比例分配可用空间。

本例中，项目1占 1/4 宽度，项目2占 1/4 宽度，项目3占 1/2 宽度。

#### 网格间隙

属性`grid-column-gap` 和 `grid-row-gap`用于定义网格间隙。

网格间隙只创建在行列之间，项目与边界之间无间隙。

```css
.grid1  {    
    display: grid;
    grid-row-gap: 20px;
    grid-column-gap: 5rem;
｝
.grid2  {    
    display: grid;
    grid-gap: 20px 5em;
｝
```

间隙尺寸可以是任何非负的长度值（px，%，em等）。

### 浮动

浮动元素具有的性质:

1. 浮动会使该元素脱离标准流.而且,一个行内元素浮动了,就能够设置宽高了,一个块级元素浮动了,他就不再独占一行了.
2. 浮动的元素互相贴靠.
3. 浮动的元素有"字围"效果.
4. 收缩:一个浮动的元素,如果没有设置width,那么将自动收缩为内容的宽度.

清除浮动:

### 响应式设计

为了使网页在不同的设备上使用，主要解决屏幕大小的问题

主要方法：隐藏+折行+自适应空间，使用rem单位

`viewport`

用户网页的可视区域(视区),手机浏览器是把页面放在一个虚拟的"窗口"（viewport）中，通常这个虚拟的"窗口"（viewport）比屏幕宽.设置:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

`width`:指的是 viewport 的大小，可以指定的一个值，让页面的像素可以跟视口匹配

`device-width` :设备的宽度

`height`：和` width` 相对应，指定高度

`media`媒体查询

```css
@media (max-width: 640px) {
    .left{
        display: none
    }
}
```

指如果屏幕的宽度小于640px，就使用下面这一套样式表

`rem`：html默认字体大小(font-size)是16px

## 动画

```css
.front{
    transform: rotate(25deg) translateX(100px) translateY(10px)
}
```

分别是中心旋转25度后向X轴平移100px，然后向Y轴平移10px

### 补间动画

transition是过渡的意思，后面跟过渡的延迟时间，需要过渡的属性，以及过渡使用的时间

transition-timing-function过渡的时间方程

### 关键帧动画

```css
.container{
    animation: run 1s;
}
@keyframes run{
    0%{
        width: 100px;
    }
    50%{
        width: 200px;
    }
    100%{
        width: 800px;
    }
}
```

`animation-iteration-count:infinite`无限循环

## Bootstrap

Bootstrap 要求使用 `HTML5` 文件类型，所以需要添加` HTML5 doctype `声明。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8"> 
  </head>
</html>
```

为了让 Bootstrap 开发的网站对移动设备友好，确保适当的绘制和触屏缩放，需要在网页的 head 之中添加 `viewport meta` 标签

```html
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
```

### 容器类

+ `.container `类用于固定宽度并支持响应式布局的容器.
+ `.container-fluid` 类用于 100% 宽度，占据全部视口（`viewport`）的容器

### 网格系统

Bootstrap 提供了一套响应式、移动设备优先的流式网格系统，随着屏幕或视口尺寸的增加，系统会自动分为最多 12 列。

- `.col-` 针对所有设备
- `.col-sm- `平板 - 屏幕宽度等于或大于 `576px`
- `.col-md- `桌面显示器 - 屏幕宽度等于或大于 `768px`
- `.col-lg-` 大桌面显示器 - 屏幕宽度等于或大于` 992px`
- `.col-xl- `超大桌面显示器 - 屏幕宽度等于或大于 `1200px`

布局规则:

- 网格每一行需要放在设置了 `.container` (固定宽度) 或 `.container-fluid` (全屏宽度) 类的容器中，这样就可以自动设置一些外边距与内边距。
- 使用行来创建水平的列组。

## Stylus

在 stylus 中可以使用变量、函数、判断、循环一系列 CSS 没有的东西来编写样式文件，执行这一套骚操作之后，这个文件可编译成 CSS 文件。

+ 安装  `npm install stylus`

使用方法：

+ 变量

  ```css
  // 声明了变量 $background-color,并为其赋值 lightblue
  $background-color = lightblue
  ```

  然后使用：

  ```css
  span 
      background-color lightblue
  ```

+ 函数

  ```css
  // add 中调用了 stylus 的内置函数 unit，此处，unit 函数为 a 和 b赋予了单位 px。
  最后将 a 和 b 相加，并返回结果
  add (a, b = a)
      a = unit(a, px)
      b = unit(b, px)
      a + b
  ```

  然后使用：

  ```css
  span
      margin: add(10)
      padding: add(10, 5)
  ```

+ 缩进

  stylus 中以缩进表示父子选择器关系，使用`&`表示对父级的引用

  ```css
  .list-item
  .text-box
      &:hover
          background-color powderblue
  ```

在Vue项目中，我们要提前声明我们编写语言是`stylus`

```vue
<style lang="stylus">
    ...
</style>
```

