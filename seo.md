# 什么是 seo

搜索引擎优化

获取流量，提升自然排名

## 日常工作中采取哪些措施实现 seo

给钱，百度

方式一：ssr 服务端渲染
大部分元素是通过 js 动态生成的，很多搜索引擎爬取数据的时候只能抓取静态 html 源码，因此动态生成的无法被爬虫索引。

ssr 通过服务器上执行 js 并渲染出完整的 html 页面，将其发送给客户端，当是开发初期就计划引入 seo 的话，选择比较成熟的 ssr 旷课，nuxt.js 或 next.js

方式二：tdk 描述
title,description,keywords

方式三： 语义化的 html 元素，图片 alt,h1,h2 的使用
语义化指的是有明确含义的 html 元素，搜索引擎在爬取网站时会更容易理解并进行收录，如 header,nav，aside,footer

方式四： 编写 robots.txt
存放在网站目录中的文本文件，可以通知引擎爬取内容的控制，让其专注更重要的内容爬取，如果不编写则会降低网站的 seo 效率，如：https://www.zhihu.com/robots.txt

方式四：https 比 http 有更好的排名

方式五：内部链接和外部链接

其他：插件@prerenderer/renderer-puppeteer ，sitmap 文件，网站导航，响应式处理
