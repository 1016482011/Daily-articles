# 如何使用纯 JS 实现一个倒计时器

原文章：[HOW TO BUILD A COUNTDOWN TIMER IN PURE JAVASCRIPT](https://www.developerdrive.com/2019/02/build-countdown-timer-pure-javascript/)

使用 jQuery 给你的网站添加倒计时器是没必要的。我们只需要几行 js 代码就可以创建一个可定制化可以倒数到预设时间的计时器。你能够将此定时器嵌入像即将维护的页面、电商商城、每日优惠网站和注册页面。

在本教程中，我们将一步步的创建倒计时器，这样你就能够完全理解了。你能够在文章的结尾处找到完整的 JavaScript 代码。

## 创建 HTML

让我们首先从 HTML 开始，倒计时器有四个数字：天、小时、分钟、秒。为它们分别创建爱你一个`<span>`元素以便之后你可以用 JavaScript 得到它们。

另外`<body>`标签闭合之前添加一个`<script>`标签写上文件路径。

```js
<div class="container">
    <p id="timer">
        <span id="timer-days"></span>
        <span id="timer-hours"></span>
        <span id="timer-mins"></span>
        <span id="timer-secs"></span>
    </p>
</div>
<script src="script.js"></script>
```

## 设置一个结束时间

为倒计时器时间过期创建一个全局变量。你可以通过创建一个 `Date`对象和调用他的`getTime()`方法来定义这个变量。

这个结束时间变量将保存着时间戳格式的过期时间，改格式显示的是从 1970 年 1 月 1 日 0 点到现在的毫秒数，例如，2019 年 5 月 15 日 12 点整的时间戳的值是 1552647600000。

```js
var endDate = new Date('Mar 15, 2019 12:00:00').getTime()
```

## 定义定时器

为定时器创建另一个全局变量。下面的例子使用了一个函数表达式，这样的话你就不需要单独的去调用函数了。不过，你也想的话你也可以将其定义为一个函数语句。

全局函数`setInterval()`会重复执行同一个函数。他的第一个参数是会被重复执行的函数，第二个参数是执行之间的间隔。你需要使用毫秒来声明这个延迟，这里设置的 1000，也就是你想的每 1 秒更新一次。

```js
var timer = setInterval(function() {
  /* 这里是js代码 */
}, 1000)
```

## 计算剩下的时间

在`time()`函数里，定义两个新的局部变量。第一个返回当前时间，另一个计算剩余的时间。

在 JavaScript 里，一个空的`Date()`对象返回当前的时间，`now`变量调用它自身的`getTime()`方法并且以时间戳的格式存了下来。`t`变量通过当前时间减去结束时间计算出剩余时间。

```js
let now = new Date().getTime()
let t = endDate - now
```

## 将时间戳转换为天、小时、分和秒

创建一个`if`语句块用来保存时间指令。这段代码将会一直执行，直到剩余时间小于 0。通过使用`if`语句块，当倒数结束的时候你不必去手动停止时间器。

```js
if (t >= 0) {
  let days = Math.floor(t / (1000 * 60 * 60 * 24))
  let hours = Math.floor((t % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
  let mins = Math.floor((t % (1000 * 60 * 60)) / (1000 * 60))
  let secs = Math.floor((t % (1000 * 60)) / 1000)
}
```

使用 JavaScript 内置的`Math.floor()`函数，你能够将任何小数向下取整。

## 输出时间

现在你已经有了合适格式的数据，现在能够将他们输出到屏幕上了。`document`对象的`getElementById()`方法允许你选取在第一步创建的 HTML 元素，分别通过`#timer-days`, `#timer-hours`, `#timer-mins`, 和 `#timer-secs`，你能够在通过`innerHTML`属性在 HTML 中设置合适的内容。

将以下代码添加到 if (t >= 0)块中，就放在时间转换之下。

```js
document.getElementById('timer-days').innerHTML =
  days + "<span class='label'>DAY(S)</span>"

document.getElementById('timer-hours').innerHTML =
  ('0' + hours).slice(-2) + "<span class='label'>HR(S)</span>"

document.getElementById('timer-mins').innerHTML =
  ('0' + mins).slice(-2) + "<span class='label'>MIN(S)</span>"

document.getElementById('timer-secs').innerHTML =
  ('0' + secs).slice(-2) + "<span class='label'>SEC(S)</span>"
```

上面的这段代码在小时、分钟和秒在前添加了一个 0 当他们的值小于 10 时。虽然这不是必要的，不过屏幕上时间格式保持一直不变用户体验更好。

这个可以通过使用`slice()`的格式化小技巧来完成，slice 用来返回数据一部分。例如，`slice(1)`将会裁剪掉第一个数据并返回剩余的数据。相似的，`slice(2)`将会移除开头的两个字符并返回剩下的。而一个负值将会返回指定区间的字符而不是裁减掉他们。所以，`slice(-2)`将会返回字符串的最后两个字符。

在这段代码里，你需要给每个数字前拼上一个 0，然后通过`slice(2)`返回最后两个字符。JavaScript 通过这种方法将会在每个一位数前加上一个 0 但是两位数原封不动。例如：

- ("05").slice(-2) = "05"
- ("018").slice(-2) = "18"

## 通知用户当计时结束的时候

如果你愿意的话你可以在倒计时结束的时候通知用户，创建一个`else`语句块并且通过`getElementById()`方法选取`#timeer`元素。你能够通过`innerHTML`添加任意你想在倒计时过期时展示给用户的文本。

```js
else {

    document.getElementById("timer").innerHTML = "The countdown is over!";

}
```

或者，你可以省略掉`else`语句块，这样的话当倒计时结束的时候计时器将会从屏幕上直接消失。

## 将他们放到一起

这也就是所有的内容了！这里是你用来创建倒计时器完整的代了：

```js
var endDate = new Date('Mar 15, 2019 12:00:00').getTime()

var timer = setInterval(function() {
  let now = newDate().getTime()
  let t = endDate - now

  if (t >= 0) {
    let days = Math.floor(t / (1000 * 60 * 60 * 24))
    let hours = Math.floor((t % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
    let mins = Math.floor((t % (1000 * 60 * 60)) / (1000 * 60))
    let secs = Math.floor((t % (1000 * 60)) / 1000)

    document.getElementById('timer-days').innerHTML =
      days + "<span class='label'>DAY(S)</span>"

    document.getElementById('timer-hours').innerHTML =
      ('0' + hours).slice(-2) + "<span class='label'>HR(S)</span>"

    document.getElementById('timer-mins').innerHTML =
      ('0' + mins).slice(-2) + "<span class='label'>MIN(S)</span>"

    document.getElementById('timer-secs').innerHTML =
      ('0' + secs).slice(-2) + "<span class='label'>SEC(S)</span>"
  } else {
    document.getElementById('timer').innerHTML = 'The countdown is over!'
  }
}, 1000)
```

## 添加一些样式

你可以给定时器添加任何你想要的样式。以下的 css 使用了`flexbox`来将其定位到屏幕的正中央。

```js
body {
    margin: 0;
    padding: 0;
}
.container {
    background: #222;
    width: 100%;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}
#timer {
    color: #fff;
    font-size: 4rem;
}
.label {
    font-size: 2.5rem;
    padding-left: 0.25rem;
}
```

## 最终成果

下图就是使用上述的 css 所得到的样子，当计时器运行的时候，它将展示天、小时、分钟和秒：

![javascript-timer-on](/img/CountDownTimer/javascript-timer-on.jpg)

当倒计时结束时，它会向用户显示一个通知:

![javascript-timer-off](/img/CountDownTimer/javascript-timer-off.jpg)

## 总结

你能够使用 JavaScript 实现很多非常炫酷的东西-有时只需要几行代码就可以。
