# 一、CSS常用背景属性：background

    - ① background-color:背景色

    - ② background-image:背景图,background-image: url(图片地址相对路径);背景图和背景色同时存在时，背景图会覆盖背景色

    - ③ background-repeat：背景图重复方式 ，no-repeat不平铺 repeat平铺(默认) repeat-x水平平铺 repeat-y垂直平铺

    - ④ background-size:背景图大小。

## 指定宽度高度

- 宽高的写法，第一个属性值为宽度，第二个属性值为高度，可以：①直接写像素 ②写百分比（父容器宽高的百分比）
- 当只有一个属性值时，默认为宽度。高度等比缩放。
当有两个属性值时，会按照指定的高度宽度进行压缩/拉伸显示。

## 其他属性值

- contain:图片等比缩放，缩放到宽或高的某一边等于父容器的宽高，另一边按照图片大小缩放（可能导致部分空余区域无法覆盖）
- cover:图片等比缩放，使背景图像完全覆盖背景区域。(可能导致背景图部分区域无法显示)

- background-position：位置坐标、偏移量
指定位置：left/center/right top/center/bottom
当只写一个属性值时，另一个默认居中
- 填写坐标：水平位置 垂直位置 (像素或百分比形式)
① 当只写一个属性值时，默认写的为水平方向，则垂直居中
② 当使用像素时：图片的左上角往各个方向移动的实际距离
水平方向：正数右移 负数左移 垂直方法：正数下移 负数上移
(左负有正 上负下正)
③ 当使用百分数时，一般只能是正数。代表去掉图片后，剩余空白距离的分布比例。例如 background-position：30%; 水平方向去掉图片后，剩余区域3:7分

## 颜色常用单位

- 十六进制：#ffffff
- 颜色名称：red
- RGB颜色：RGB（255，255，255）
- ＲＧＢＡ：第四位数，表示透明度。可选值0~1

# 二、CSS常用文本属性

## 1、字体、字号

- font-weight:字体的粗细，
可选属性值：bold加粗 lighter细体 100~900数值(400正常，700 bold)

- font-size:字体大小 **PX **%（浏览器默认字体大小的百分比，绝大部分默认16px）

- font-family:字体族，设置字体。
  - 多个字体样式间用逗号分隔，浏览器解析时，会从左往右依次解析，选择可用字体。
  - 一般前面使用具体字体名称，最后一个使用字体族类名称。
  （常用字体族名称：衬线体serif 非衬线体sans-serif(常用) 等宽体Monospace）
  - 例如：font-family:Arial, 'Microsoft Yahei', sans-serif;

- font-style:字体样式,正常(normal) 斜体(italic)

- font-variant：small-caps; 将字母转为小型大写字体。

- (了解)font缩写形式：font-style font-variant font-weight font-size line-height font-family
  - 使用注意事项 ：
  ① 顺序必须严格按照上述顺序；
  ② 多个样式之间用空格分割，而且font-size/line-height 必须作为一对用/分割
  ③ font-size和font-family必须指定，其他样式不指定将采用默认样式显示；
  - 例如：font:italic bold 75%/1.8 'Microsoft Yahei', sans-serif;
  斜体 加粗 字号/行高 字体族(微软雅黑，非衬线字体族)

## 2、字体颜色：
- color:字体颜色
- opacity:透明度,0~1之间的数字。 调整时控件以及子控件均会透明，而使用rgba调整时，只会时本控件透明，子控件不会发生透明度变化。

## 3、行距、对齐等：
- line-height:行高 ① 像素单位 48px ② 不带px 正常行高的倍数 ③百分数 同2
  - 典型应用，调整控件中文垂直居中的方式：控件的height=控件的line-height

- text-align:块级元素中行级元素的水平对齐方式 left center right

- letter-spacing:字符间距,字与字之间的间距

- text-decoration:文本修饰 下划线underline、删除线line-through、上划线overline、none


- overflow:控制超出范围文本的显示方式(auto 根据文字多少自动显示滚动条，
scroll始终显示滚动条， hidden 超出范围文本隐藏)
可以通过overflow-x overflow-y分别设置水平垂直方向
- text-overflow:设置多余文字的显示方式 ：clip裁剪掉 ellipsis省略号
    
    **【重点】让每行多余文字显示省略号：**
    - ① white-space: nowrap; 如果是中文，需设置行末不断行
    - ② overflow: hidden; 设置控件超出范围隐藏
    - ③ text-overflow: ellipsis; 设置多余文本省略号显示

- white-space: nowrap; 设置行末不断行显示

- word-break: 浏览器默认在空格处断行，当一个单词长度超出范围时，会不断行显示， - break-all 允许在单词内换行。

- text-shadow:文本阴影，有四个属性值：
    - ①水平阴影距离 必选，数值越大，阴影右移
    - ②垂直阴影距离 必选，数值越大，阴影下移
    - ③阴影模糊距离 可选，数值越大，阴影越模糊。默认为0，不模糊
    - ④阴影颜色 可选，默认为黑色

- text-indent:首行缩进，可用像素值调整缩进大小

- text-stroke: -webkit-text-stroke: 0.2px #008000; 描边的粗细，描边的颜色

 

# 三、CSS常用选择器
## 1、通用选择器
- ① 写法：*{}
- ② 作用：选中页面中的所有的HTML标签
- ③ 优先级最低
  
```{
    background-color:red;
}
```
 

## 2、标签选择器
 - 1、写法：HTML标签名{}
 - 2、作用：选中所有对应的HTML标签，并修改其样式

```li{
color: red;
font-size: 36px;
}
```
 

## 3、类选择器class选择器
 - ① 写法：.class名{}
 - ② 调用：在需要修改样式的HTML标签上，使用class="选择器名"
 - ③ 优先级：当作用于同一层时，class选择器>标签选择器

```.list{
  color: blue;
}
```
**注：选择器命名规范：**
- 1、只能是字母，数字，下划线
- 2、开头不能是数字

 

## 4、ID选择器
- ① 写法：#选择器名{}
- ② 调用：在需要修改样式的HTML标签上，使用id="选择器名"
- ③ 优先级：同一层时，id选择器>class选择器
- ④ ID选择器是唯一的！同一页面严禁出现同名ID！！！

 ``` 
 #first{
   color: green;
 }    
 ```

## 5、后代选择器
- ① 写法：选择器1 选择器2 .....选择器N{}
- ② 生效规则：选择器N必须是选择器N-1的后代

## 6、子代选择器
- ① 写法：选择器1>选择器2>……>选择器N{}
- ② 生效规则：选择器2必须是选择器1的【直接子代】……

```
div>ul>li{
   background-color:yellow;
}
```
## 7、交集选择器
- ① 写法：选择器1  选择器2  ……  选择器N{}
- ② 生效规则：必须同时满足所有选择器，才会生效

```
li.list#first{
    background-color:blueviolet;
}
 ```
 

## 8、并集选择器
- ① 写法：选择器1，选择器2，……，选择器N{}
- ② 生效规则：满足任意一个选择器，均可生效
 ```
 .list#first{
   background-color:greenyellow;
 }
 ```

## 9、伪类选择器
- ① 写法：选择器名：伪类状态{}
- ② 常见伪类状态
  * ：link 未访问
  * ：visitied 已访问
  * ：hover 鼠标指上状态
  * ：active 激活选定状态
    
    注：当超链接同时具有以上四种状态，则选择器必须按照上述序列排列
  * ：focus 获得焦点，常用于input

## [选择器优先级]
- 1、就近原则：近者优先
- 2、当作用于同一层时：可用权重计算
   - 权重划分：标签选择器 1
   - class选择器 10
   - ID选择器 100
   - 行级样式表style="" 1000
   - #ul .li .li2 li{} 优先级权重121
   - li .li1 .li2 .lis2{} 优先级权重31
   - #ul #li li{} 优先级权重201
  
## 【三种使用CSS的方式】
- 1、行内样式表：直接在HTML开始标签中使用`style=""`的方式引用
  
    特点：将CSS代码与HTML代码完全糅杂在一起，
    不符合W3C关于内容与表现分离的要求。不利于样式复用；
    优先级：最高。
- 2、内部样式表：在<head></head>中，
    使用`<style type="textx/css"></style>`方式使用

    特点：将CSS代码与HTML代码分离，但是没有彻底分离CSS文件与HTML文件，
    不利于多页面复用样式。
- 3、外部样式表：使用link标签链接CSS文件。
`<link rel="stylesheet" type="text/caa" href="css/0309CSS"/>`

    特点：实现了ＣＳＳ与ＨＴＭＬ的彻底分离，有利于样式复用及后期维护。




## 【超链接常见的四种状态】
* a:link
* a:visitied
* a:hover
* a:active