在浏览器解析 html 的时候，如果遇到 script 元素则不能继续构建 dom 树，只有等到 js 脚本执行结束后才会继续解析 html 构建 dom 树，由于 js 文件很大，往往下载时间比较长，会造成页面的解析阻塞，浪费性能

# defer 的作用

告诉浏览器不需要等待脚本下载（类似于开一个新的线程同步进行）而继续解析 html,构建 dom-Tree，如果脚本提前下好，会等待 domtree 构建完成，domContentLoaded 事件之前执行 defer 中的代码，即 domContentLoaded 执行在 defer 代码之后

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>

    <script defer src="./js/defer.js"></script>

    <script>
      window.addEventListener("DOMContentLoaded", () => {
        console.log("DOMContentLoaded");
      });
    </script>
  </head>
  <body></body>
</html>
```
