---
layout: post
title: scriptaculous学习笔记
author: Alvin
date: !binary |-
  MjAwNi0xMS0wNiAyMDoxMDo1OSArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0wNiAxMjoxMDo1OSArMDgwMA==
---
scriptaculous学习笔记(三) 
 
Slider滑动杆
滑动杆是通过两个Div嵌套而成的。一个是滑动槽，一个是滑动杆。将这两个div注册为滑动杆就可以了。
 
<div ID="track1" STYLE="width:200px;background-color:#aaa;height:5px;">
   
<div ID="handle1" STYLE="width:5px;height:10px;background-color:#f00;"> </div>
</div>
<script>
 new Control.Slider('handle1','track1',{
      sliderValue:0.5,
      onSlide:function(v){$('debug1').innerHTML='slide: '+v},
      onChange:function(v){$('debug1').innerHTML='changed! '+v}}); 
</script>
 
程序通过new Control.Slider( )来注册滑动杆。第一个id是滑动杆，可以以[&lsquo;id1&rsquo;,&rsquo;id2&rsquo;,…]的方式来注册多个滑动杆
第二个id是滑动槽。
后面花括号{}中的参数：
sliderValue:滑动杆初始值,当有多个滑动杆的时候，用 [v1,v2,…]的方式指定多个缺省值
range: $R(2,15) 滑动槽的值的上下限范围。默认是0~1
axis: 'vertical', 可以获得一个上下拖动的滑动杆.缺省为水平.
restricted:true, 多个滑杆时,初始值大的滑杆与初始值小的滑杆不能互相倒置大小.
values: [1,2,3…] 滑动槽的多个预设值。滑杆只能滑动到其中的某个值。
onSlide:function(v) 事件：滑动时触发。V是滑杆当前值。
onChange:function(v) 事件：值改变时触发。V是滑杆当前值。
滑杆区域：(多个滑杆之间围成的上下界区域)
spans:['span7-1','span7-2'], 
startSpan:'span7-start',
endSpan:'span7-end',
这个地方还没有仔细看，不是非常明白。
注意：当存在多个滑杆时,滑动槽设为relative,滑动杆应设为display:absolute;left:0 ;top:0;
 
scriptaculous学习笔记(二) 
 
Effect效果对象
下拉效果&上拉效果
 
<div id="d1">
 aaaaaaa
bbbbbbbbbbbbbbbbb
ccccccccccccc
</div>
 <a href="#" onclick="Effect.BlindDown('d1',{});; return false;">BlindDown()</a>
  <a href="#" onclick="Effect.BlindUp('d1',{});; return false;">BlindUp()</a>
Effect.BlindDown('d1',{})函数的花括号里面{}可以跟参数：
 duration:1.0; 这个数字表示动作持续时间。
delay: 0.5   延迟0.5秒再启动效果
如果想让一个Div开始的时候隐藏，点击下拉的时候才拉下，那么只需要将此Div的属性设为：display: none
 
上滚&下滚效果
这一组函数:
Effect.SlideUp('d1',{});
Effect.SlideDown('d1',{});
这组函数效果与Blind那一组基本一样，效果粗看起来差不多……我也是细心比较才发现的。原来Blind这一对内容是不会随着上拉或下拉而动的。而Slide中的内容会被拉上或拉下。
 
变色闪动效果
Effect.Highlight('d1',{duration:1.5})
此元素将会改变几次颜色并最终返回原来的颜色。
 
渐变显示效果
Effect.Appear(&lsquo;d1&rsquo;,{})
原来的元素初始CSS为display:none,用此效果后渐渐显示，渐变的alpha滤镜效果。
膨胀消失效果
Effect.Puff(&lsquo;d1&rsquo;,{})
 
消失后可以使用Element.show('d1') 再次将元素显示出来。
 
渐渐消失效果
Effect.Fade('d1')
渐渐显示效果
Effect.Appear('test_img')
震动效果
Effect.Shake(&lsquo;d1&rsquo;,{})
此元素将会左右震动
闪烁效果
Effect.Pulsate('d1',{})
此函数通过alpha滤镜来进行闪烁。
 
长大效果
 Effect.Grow("d1",
{duration:5.0, direction: 'bottom-right', opacityTransition: Effect.Transitions.linear});
其中:direction 是指的元素从什么方向进入。
如果不指定后面的参数，元素缺省是从下面的中间开始变大。没有alpha效果。
萎缩效果
Effect.Shrink(“d1”,{})
长大效果Grow()的相反效果。
 
Toggle各种效果
汉语里面不知道怎么翻译Toggle，大体意思是：当某物打开的时候触发就关闭，而关闭的时候触发就打开 的一种像”乒乓开关”的行为。这种行为在做页面时特别有用。
Effect.toggle('d2','BLIND')
Effect.toggle('d2',&rsquo;appear&rsquo;)
Effect.toggle('d2',&rsquo;slide&rsquo;)
…
似乎所有这种有着相反效果的函数都可以在这里设置Toggle, &rsquo;BLIND&rsquo;中的效果名大小写不敏感。
 
 
取消效果函数
这几个函数真是乏善可陈……唯一要提的就是关于中止这几个动画过程的函数：cancel()
例如：
effect1=new Effect.SlideUp(&lsquo;d1&rsquo;,{duration:10.0});
想要在这10秒钟中止动画过程： effect1.cancel()
 
效果队列
这个神秘的queue属性，还有待进一步学习……
  function startTimeline() {
    // 3x highlight in front
    for(var i=0; i<3; i++)
      new Effect.Highlight('d3', { duration: 1.0, queue: 'front' });
    
    // insert scale at very beginning
    new Effect.Scale('d1', 75, { scaleContent: true, duration: 1.0, queue: 'front' });
    
    // parallel implied, delay 0.5 sec
    new Effect.Highlight('d1', { delay: 0.5 }); 
    
    // puff at end
    new Effect.Puff('d2', { duration: 4.0, queue: 'end' });
  }
 
scriptaculous学习笔记(一) 
 
这两篇文章是我昨天一天的成果。scriptaculous真的是一个很好的类库，不过国内的资料似乎很少(起码我没有搜索到)，正好是在学习这个咚咚，写个笔记来记录一下吧。
准备
包含库文件：
<script src="../prototype.js" type="text/javascript"></script>
  <script src="../scriptaculous.js" type="text/javascript"></script>
可排序对象
例子：
以下代码将创建一个列表，并且可以拖动排序，每次移动户都将触发一个可以返回列表顺序的函数，并且已经序列化，可以通过Ajax传给服务器端。
//建立列表：
<ul id="x">
<li id="item_1">1</li>
<li id="items_2">2</li>
<li d="items_3">3</li>
<li d="items_4">4</li>
</ul>
//开始建立可排序组件
<script type="text/javascript" language="javascript" charset="utf-8">
// <![CDATA[
   Sortable.create('x',     
{overlap:'horizontal',
ghosting:true,
constraint:false,
    onUpdate:function(sortable){alert(Sortable.serialize(sortable))},
    onChange:function(element){$('state').innerHTML = Sortable.serialize(element.parentNode)}
  });
这样一个可以排序的列表就作好啦~~恭喜~!
 
讲解：
其中，Sortable.create()的作用是将这个id=”x”的<UL>转化为可排序控件。第一个参数&rsquo;x&rsquo;便是此控件的ID。 花括号{ }内的属性列表是一些预置属性和各种动作的触发函数：
各个属性的意义：
l         overlap : horizontal | vertical指明了列表是水平方向还是垂直方向排列。(在constraint属性中会和此属性有关)
l         ghosting: true | false这个属性指明了拖动行时是否会在原来位置显示虚影占位。
l         constraint: 'vertical' | 'horizontal' | false 这个属性指明了是否会被约束拖动方向。
l         onUpdate:function(sortable){} ：update事件将会在完成一次排序行为时(拖动后鼠标松开时)被触发。”sortable”参数中将会传入被绑定的<UL>对象。
l         onChange:function(element){} ：此事件将会在鼠标拖动时被触发，每移动一下都将触发此事件。注意：此事件传入的element参数是<UL>下被拖动的<LI>而非整个<UL>
 
构造函数中的参数列表: 
 
      
参数名          初始值                     说明
element:          element
tag:                'li',                // assumes li children,标签内可被拖动的子标签名
      dropOnEmpty:       false,               ??
      tree:               false,               // fixme: unimplemented ??
      overlap:            'vertical',            // one of 'vertical', 'horizontal'
      constraint:          'vertical',            // one of 'vertical', 'horizontal', false
      handle:            false,               // or a CSS class ,CSS样式是此handle指定样式的标签部分可拖动如为false则整体可拖.       
      only:              false,                ???
      hoverclass:         null,                //被拖入位置的CSS 
      ghosting:          false,                //显示残影占位
      format:            null,                 //???
      onChange:         Prototype.emptyFunction, 
      onUpdate:         Prototype.emptyFunction
     dropOnEmpty:       true              //or false 指定此<UL>等可排序区域可否接受其他<UL>中的元素
      containment:        ["list1","list2"]      当dropOnEmpty设为true,在此参数中设置可接受的列表id
 
 
除了构造函数,其余的常用方法：
 
l          Sortable.serialize(sortable)  静态方法。返回一个当前sortable对象的按照排序顺序先后排列序号的字符串：如x[]=1& x[]=2& x[]=3，每一个<Li>的序号通过
<li id="item_1">1</li>的下滑线后面的数字指定。下滑线前面的单词在一组排序中应使用一个相同的前缀。不同的组，前缀应该不同。
l          Sortable .destroy(sortable)  静态方法。撤销此对象的排序属性。
 
 
常见问题:
当我们给<UL>外面加上DIV，比如
<div style=”overflow-y:scroll;height:100px;”>
我们会发现页面一团糟了，UL溢出了DIV, 页面乱七八糟。
不用急，在Div的Style中加入 “position:relative;”就解决了<UL>不听指挥的问题。
现在再用一下，拖动有点问题……我们会发现定位不准确了，这是因为没有考虑到滚动条的偏移量。
我们在Sortable的构造函数前加上一句：
Position.includeScrollOffsets = true; 
此问题便会迎刃而解~~！哈。
