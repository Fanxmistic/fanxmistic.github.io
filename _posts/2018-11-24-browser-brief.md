---
layout: post
title: "浅淡浏览器发展简史"
date: 2018-11-24
categories: 前端
cover: "/assets/img/web.png"
tags: 浏览器
---

> 随着各大浏览器对 `HTML5` 新特性的支持，网页对图形以及多媒体方面能力空前增强。随之而来的网络优化，更是让越来越多的『跨界应用』思考如何来开垦 Web 平台。Jeff Atwood 曾经说过：Any application that can be written in JavaScript, will eventually be written in JavaScript. 如果没有浏览器对 JavaScript 的支持，这些都是无稽之谈。但你知道吗？浏览器并非生来就这么强大和友好。今天，我们就来简单地聊聊主流浏览器的发展吧。

### 一、世界上第一个浏览器

提到浏览器，就不得不提 [Tim Berners-Lee](https://en.wikipedia.org/wiki/Tim_Berners-Lee), 他是 [W3C 组织]([World Wide Web Consortium - Wikipedia](https://en.wikipedia.org/wiki/World_Wide_Web_Consortium)) 的理事，并在 1990 年左右发明了 `WorldWideWeb浏览器`，后来为了避免和 [World Wide Web]([World Wide Web - Wikipedia](https://en.wikipedia.org/wiki/World_Wide_Web)) 命名冲突而改叫 `Nexus`。这时浏览器的功能很简单，只支持文本、简单的样式表和有限格式的图片和声音。所以，那时的网页也很简单，承载的信息大多以文本为主。

[image:8081F2DE-C91F-424B-B735-7BD8DA0C14D4-1988-00005F72111D8F36/600px-WorldWideWeb_FSF_GNU.png]

### 二、第一次浏览器大战

1993 年，网景公司。[Marc Andreessen]([Marc Andreessen - Wikipedia](https://en.wikipedia.org/wiki/Marc_Andreessen)) 领导的团队推出了 `Mosaic浏览器` ，而后又改名为 `Netscape浏览器`。一经推出，便大受欢迎，可以说是第一款真正意义上被用户广泛使用的浏览器。虽说如此，它所支持的功能也是特别有限，没有 CSS，没有 JavaScript，只能显示一些简单的 HTML 标签，更别说现在这些丰富的 HTML5 的功能了。但也满足了那个时代上网用户的需求，同时也取得了可观的市场份额。

然而，好景不长。1995 年，微软公司看到 `Netscape浏览器` 如此的受欢迎，就顺势推出 `Internet Explorer浏览器`（IE），并与自家的 `Windows` 操作系统强制捆绑。很快取得了更高的市场份额，获得了空前的成功，把 `Netscape浏览器` 挤出了市场直至消亡。自此，第一次浏览器大战，以 `IE` 大获全胜而告终。

### 三、第二次浏览器大战

前文说到，由于微软推出的 `IE` 的影响， 网景公司的 `Netscape` 每况日下。但网景公司并没有坐以待毙，在 1998 年 成立了 Mozilla 基金会，并准备开发新的浏览器 。

2003 年，苹果公司搭配自家的 Mac OS X Panther 推出了 `Safari浏览器`，受限于 Mac 的用户量，Safari 并未产生多大的影响；2005 年，苹果开源了 Safari 的内核（渲染引擎）—— 大名鼎鼎的 Webkit，意义之重大，不言而喻。

2004 年，网景公司发布了全新的浏览器 `Firefox`，并搭配了 Gecko 内核。它功能丰富，支持用户拓展，一推出就深受大家喜爱，市场份额也稳步上升。

2008 年，Google 公司以苹果的开源项目 Webkit 作为内核，创建了一个全新的项目 `Chromium浏览器`。它被谷歌拿来做 Web 技术试验场，尝试一些大胆创新的技术。同年，推出了面向用户的浏览器 —— Chrome，它会选择 Chromium 的稳定版本为基础，集成一些私有的编码解码器。或许你会疑惑，既然推出了 Chromium，为什么还要推出 Chrome？其实很简单，Chromium 是面向开发者玩的，Chrome 是选取 Chromium 的稳定版面向用户的，二者并不冲突。

期间还有其他公司也推出了浏览器，相较而言，不太重要，故不再赘述。市面上那么多浏览器，并且内核（渲染引擎）也不一样：IE 的内核是 Trident; 火狐是 Gecko；Safari 和 Chrome 是 Webkit。这就会导致同一个网页在不同的浏览器下访问，会出现不同的展示效果；并且不同的浏览器对新技术的支持力度也不一样。所以，公司会根据用户浏览器的不同而返回不同的网页内容，那它是如何鉴别用户浏览器的呢？答案就是 User-Agent。

Firefox 设置了自己的 User-Agent，假如就叫 `{ User-Agent: "firefox" }`，当开发者知道你是 Firefox 时，就会给你返回新页面（因为火狐浏览器可以支持很多新特性），其他浏览器就会返回老页面。IE 看到过后，也赶紧更新浏览器版本支持新特性，但仍然看到的是老页面。这时，IE 不服了，凭什么你给 Firefox 新页面，给我老页面啊？于是它便想到更新自己的 User-Agent，假如就叫 `{ User-Agent: "ie firefox" }` ，当开发者发现 User-Agent 字段中有 firefox 时，同样也会返回新页面了，这是 IE 想要的结果。

没想到的是，Google 和 苹果公司也同样效仿，依次往复，导致各家浏览器的 User-Agent 字段越来越冗长。不信的话，你打开浏览器的控制台，在 Network 下查看请求的 Request Headers ，里面有 User-Agent 字段。我目前用浏览器是 Google Chrome [ Version 70.0.3538.102 (Official Build) (64-bit) ]，它的 User-Agent 字段是：

```textile
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36
```

自此，微软 IE 依靠 Windows 系统依然占据重要地位，Mozilla 火狐 和 Google Chrome 二者也拥有大量粉丝，逐步谗食 IE 的市场份额，形成三足鼎立之势。

### 第四、2010 ~ 现在

2010 年，苹果宣布了 Webkit2 ，改为多进程的结构模型。

2013 年，Google 和 苹果 出现了对内核发展的分歧。于是 4 月份，Google 宣布了全新的内核项目 —— Blink。该内核早期是完全复制了 Webkit ，之后删除了与 Chromium 无关的代码，并开始大刀阔斧地对内核进行改革。

2015 年，随着 Windows 10 的发布，微软公司宣布了全新的浏览器 —— Microsoft Edge，用来取代 IE 浏览器，一代王者逐步退出舞台。

2017 年，Mozilla 宣布了为 Firefox 全新的打造的 [Quantum]([Quantum (Mozilla) - Wikipedia](<https://en.wikipedia.org/wiki/Quantum_(Mozilla)>)) 版本，号称新时代的最快浏览器。

目前为止，Google Chrome 以绝对的优势远远领先于其他浏览器，并且对 Web 技术 依然充满热情，以优秀的特性和性能吸引了绝大部分开发者。

### 第五、结尾

写这篇文章的初衷是对各家浏览器及其内核的迷惑，就想把它们的来龙去脉理清楚。希望它能对你有些帮助，如果你发现有不正确的地方，欢迎指正。
