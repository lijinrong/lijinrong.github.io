## 1.层级关系
让这个box范围内的全部包进来，这样的话就完美的进行调节，再也不用到处找第几行第几个，我刚才在哪个位置给覆盖了。一看便知！

```
.box{
	width: 100%;
	height: 300px;
	p{
		margin: 10px;
		span{
			padding: 10px;
			a{
				list-style: none;
				&:nth-child(1){
						
				}
			}
		}
	}
}
```
 
## 2.主色调的使用
小米诺基亚等公司，都有自己的主色调。如果每次加一个#e23615太麻烦，而且如果诺基亚要搞活动！过年要换成红色！你完了。。。你做了无数个background和font-color。此时less解决了这个问题

```
@bg-color: #000;
@ft-color: #e1e1e1;

.bg-color {
	background: @bg-color;
	color: @ft-color;
	padding: 8px 25px;
}
```
 
## 3.拿他当函数用
比如说我在box1中用了很多漂亮的样式，在box2中想使用，但必须把他们的10行样式全复制过来，多次使用很麻烦。怎么办？这次拿他当函数，
第一种：最简单，放进去就行

```
.x{
	background: #000;
	width: 300px;
	height: 100px;
}

.box {
	.x;
	border:1px solid #ccc
}

//相当于这样，而且能多次使用！
//再也不用担心我的学习，步步高打火机，哪里不会点哪里
.box {
	background: #000;
	width: 300px;
	height: 100px;
	border:1px solid #ccc
}
```

第二种：当函数来回调，自己这个颜色我不确定怎么办，木有关系
*@color 就是 function(a) 里面的a，可以瞎起名
*@color 可以放默认值懒得动，也可以放全新的颜色。

```
.x(@color){
	background:@color;
	border:1px solid @color
}

.x(@color:#ccc){
	background:@color;
	border:1px solid @color
}

.box {
	.x(#000);
	display:flex;
}
//相当于到一个地方，换一个主题。再也不用担心我的学习
```
 
## 4.一个class有N个方案
比如说这个class叫kings
我给他做出了4种主题，各种大小完全不同。此时我总不能起名叫
kings1，kings2，kings3吧
首先把所有方案排列出来！然后box来显示。
相当于电视机，你放N个台自己选哪个电视剧

@_ 其实是默认的意思，你什么都不加空着就这样
你要加a或b或c就另一种方案

```
.king(@_, @width:1px, @height:1p, @bg:#fff)
{
	width:@width;
	height:@height;
	background:@bg;
}

.king(a, @width:100px, @height:100p, @bg:#000)
{
	width:@width;
	height:@height;
	background:@bg;
}

.king(b, @width:200px, @height:200p, @bg:#f88)
{
	width:@width;
	height:@height;
	background:@bg;
}

.king(c, @width:300px, @height:300p, @bg:#0000CC;)
{
	width:@width;
	height:@height;
	background:@bg;
}

.box{
	.king(a)
}
```
 
## 5.简单的计算器
基本的加减乘除在这里可以使用
而且过程中不用担心用不用加px rem
在a里加就行了

```
@a:100px;

.box{
 	width:(a/2)+3-5*2;
}
```
 
## 6.arguments的使用
其实他就是用来 五马分尸的。
比如border的分布:由数字，样式，颜色拼出来。
border: 1px solid #ccc
有没有什么办法用一个就KO掉？那就是arguments

```
.bor(@a:1px, @b:solid, @c:#000)
{
  border:@arguments 
}

.box1{
  .bor();
}

.box2{
  .bor(20px, dashed, #ccc);
}
```