---
layout: post
title: "arXiv submit 定时脚本"
date: 2018-05-16
excerpt: "单纯记录下如何睡着觉踩点提交文章"
tags: [web, physics]
comments: true
---

## 引言

众所周知，arXiv 网站上的文章每日更新一次，当对于不同 field 点击后边的 new 时，出现的当日更新文章是按 submit 的时间排列的。而经常刷 arXiv 的人也知道，面对动辄几十上百篇的每日更新，前边可能还有兴趣读个 abstract，后面的可能连 title 是啥都没耐心读下去了。因此就有那么些人相信，只要 submit 的时间恰好是新一天 submit 时间窗口的初始位置，也就是文章会出现在第二天 new list 中的第一个，这样内容可能或多或少会引起更多的关注。于是踩着点 submit arXiv paper 这事，就像抢火车票一样，流行在一些小圈子中。如果时间正常也就算了，可人家服务器在美国，每天 submit 窗口的更新时间是在北京的凌晨，为了提交一篇文章熬夜显得颇没性价比，然而这种无脑的工作，怎么可能需要人做呢。

所以我决定写这一篇手把手的内容，主要是针对 physics 领域提交的诸位， cs 领域不知道有没有抢头彩这恶习，即使有，随便一个人也知道怎么自动化。因此这篇文章也没啥干货，纯粹为非 cs 领域提交文章又有抢头彩恶习的同学提供一个不需要熬夜的方式而已。

其实曾经年少的我，也用过按键精灵提交。这都不需要教学了，直接定好时间，暴力点击指定的屏幕像素点即可。不过这个方式有很多缺点：需要额外下载一个软件，而且是否有 mac 版本还未知；一旦有任何弹窗或者内容在指定时间之前挡住了浏览器的 submit 按钮，那你就惨了，因为按键精灵只是无脑模拟点击操作。所以这次我用了在 console 里直接注入几句 js 的方式操作。

## Step by Step

首先当然是在 arXiv 网站上把文件都提交好，选项都填好，直到来到最后一步，有 submit 提交按钮的地方。这网站良心的地方是，点一下这按钮就提交成功了，不跳转网页，甚至连确认对话框都没，大大减轻了我们的自动化工作量。

这里我们以 chrome 浏览器为例操作，其他浏览器的开发者模式操作起来也都大同小异。首先当然是要获取提交按钮元素的选择器，直接在按钮上右击，选择 inspect，开发者模式就自动弹出来了，对应元素内容已经高亮。你当然可以根据 class 等内容来进行定位，不过这个 input 标签没有 id，为了方便我们直接在高亮处右击 add attribute，加一个 `id='subarx'`的id属性更省心点。

之后在 chrome 的 console 里输入

```javascript
function check(){
var now = new Date();
if (now.getHours()===2 &&now.getMinutes()===00 && now.getSeconds()<=3 && now.getSeconds()>=1){
   	document.querySelector("#subarx").click();
	}
}; // when the now time is 2:00:1 to 2:00:3, click the submit button
var autosubmit=setInterval(function(){
    check();
},1000*1); // check the time every 1000ms=1s
```

并且回车，大功告成，可以放心睡觉了。不过可别关了电脑再睡，对于mac，我的 caffeine 是常开的，所以不需要担心电脑自动休眠。当然，你关了这页浏览器界面也不行。

上面的 js 代码很简单，连 jQuery 都不需要，不懂代码的人应该也能看懂。需要说明一下的就是提交的时间那里，我这里设的是凌晨2点。这一设定有两个前提，第一， arxiv submit 窗口每日更新的时间是北京时间凌晨两点。由于 arXiv 有修改提交时间窗口的前科，同时美国夏令时的实行会使得中美时差变化，在提交文章时，一定要对这一时间进行确认。不然提交得再精确，没卡在门槛上也没什么用。第二，你还要确保自己电脑的系统时间和 arXiv 服务器时间完全一致，经测试 arXiv 服务器的时间和 google 或百度提供的时间，精确到秒应该是完全一致的。(本来你可以在 arxiv.org/localtime 来对这个时间进行直接的确认，但现在这一时间不显示秒数。结合 arXiv 曾经搞过一阵每日文章的随机而非按提交时间排列，可以确认 arXiv 本身是很讨厌这种无聊的抢票式提交的。) 千万不要因为自己电脑是联网授时，就以为高枕无忧，我的 mac 就和标准时间相差半分钟，至今无解。因此你需要和 google 标准时钟校对过时间，如果不想修改本地时间的话，那就可以修改上面代码里的时间判断部分，调整一下就好。

如果你对以上做法有疑虑，你也可以直接改变代码中的提交时间判断部分，进行模拟测试，看是否可以准时提交。反正 arXiv 提交之后还可以 unsubmit 反悔，就像什么都没发生一样，你可以反复测试调整，直到自己可以放心的去睡觉。对了如果你想取消一直在后台循环的 `setInterval` 函数的话，可以用 `clearInterval(autosubmit)`来实现。

Good Night and 别玩坏了。

EOF

