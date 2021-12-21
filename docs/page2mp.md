# 基于Node.js的Github文章同步微信公众号实战（持续更新）

作者：Nebula

邮箱：admin@liuxinyumo.cn

## 起因

本来想在Github Pages里开一个个人的blog，平时分享下技术，但是发现发布的文章并不能直接分享到公众号里，打开网页再点击分享后就变成了纯文字的朋友圈消息，这体验也太差了。一开始以为是微信只能分享公众号文章，不过发现部分的外链网站也是可以正常分享的，那大概就是分享策略问题了，必须是接入微信SDK的网页，配置了分享策略才能分享，然而Github Pages我无权干预这一项配置，所以我还在想难道要两边各写一份？谁料晚间的时候一篇“博客文章自动同步微信公众号实践”，这我可就来了兴趣，为什么我不实现一个基于Github的文章同步脚本？

简单预想了一下，这件事也不是一天两天能搞完的，先说明下这块内容持续更新，毕竟自己平时还是比较忙，理论问题应该不大，不会烂尾，朋友们可以跟踪一下。

文章地址：[https://liuxinyumocn.github.io/blog/page2mp](https://liuxinyumocn.github.io/blog/page2mp)

脚本仓库地址：[https://github.com/liuxinyumocn/page2mp](https://github.com/liuxinyumocn/page2mp)