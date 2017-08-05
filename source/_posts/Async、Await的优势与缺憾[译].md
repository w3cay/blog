---
title: Async/Await的优势与缺憾[译]
date: 2017-08-05 17:16:51
tags: [Async,Await]
---
这可能会存在一些争议，据我所知 async/await 这个特性受到很多开发者的青睐。对于不熟悉的同学，简单讲，其实它就是一种让开发者处理 Promise 更漂亮点儿的原生语法。

async/await 写法：
```javascript
async function doSomethingCool() {
  let someValue = await getSomePromise();
  console.log(someValue + '!!!');
}
```

Promise 写法：
```javascript
function doSomethingCool() {
  getSomePromise.then(someValue => someValue + '!!!');
}
```

在上面的例子中，假定 `getSomePromise` 就是一个能够返回 Promise 的函数。

## 回忆过去
多年以前， C# 迎来了 async、await 特性，让很多人兴奋不已，并号称『让异步编程更简单易懂』（至少我的同事们是这样认为的），因为它的『简洁』导致很多人喜欢。现在 async、await 特性在 Javascript 也落地了，我也听到了同样的声音。但是我对此带来的好处心存疑虑。

## 优势
1. 之前需要很多函数组成的功能，现在只需要一个函数。从视觉上就能感觉到复杂度降低了，之前需要 2n 个大括号的代码，现在只需要2个足以。无论如何，会让人感觉更简单些。
2. 语法后面不用再加 `then` 链式回调函数。对于大多数人来说，Promise 和函数式编程有个巨大的转变就是链式回调函数。说实话，有些人很难处理这些回调。
3. try/catch 使 async 语法的异常捕获更加好用。这可能是 async、await 带来我最喜欢的特性。使得 Promise 的异常错误处理像『常规 javascript』一样，例如：
```javascript
let bad = () => Promise.reject('bad');
async function coolWay() {
  try {
    let result = await bad();
    doSomething(result);
  } catch (err) {
    console.error('An error!');
  }
}
```

对比
```javascript
let bad = () => Promise.reject('bad');
function lameOldWay() {
  bad()
    .then(doSomething)
    .then(null, err => console.error('An error!'));
}
```

在我看来，第一个示例很明显的表示了 catch 块会捕获 `bad()` 或 `doSomething()` 的任何异常，然而在第二个示例中，除非你非常熟悉 Promise 的语法，不然你会很难发现在哪里捕获异常。尽管如此，这在一些人眼里并不算一个很大的问题。

## 劣势

1. 之前需要很多函数组成的功能，现在只需要一个函数。（你可能注意到这个在上面优势提到过）当多个函数合并成一个函数之后，会很难区分函数的事件。然而当函数分开使用的时候，会清晰的看到单个函数触发的事件。
2. 经过编译的代码臃肿并丑陋。在当前这个时期，如果你想使用 async/await，你就需要通过 Babel 来进行编译才能执行。问题就是，当你写好了几行代码，编译过后代码将会是几倍的代码量。

## 用或者不用

你可以同时使用基于链式调用的 Promise ，也可以使用 async/await，这都并没有什么。我不确定哪个更适合你，但是只要适应场景并且能使代码更可读，你就用吧！

## 总结

最好去深入了解 async/await，我敢打赌在大部分的应用场景中你并不需要区分到底哪种方案语法更好，最重要的是去权衡哪个写法更能让你的代码更『好看』，如果两者都 ok ，那么我个人推荐使用 Promise 的链式调用的方法，因为它更简单直接，性能更好，编译的出的代码更少。然而，强大的 async/await 能让你更加自然的使用 try/catch。

> 翻译原文: [《 async/await: It’s Good and Bad 》 —— Ben Lesh ](https://medium.com/@benlesh/async-await-it-s-good-and-bad-15cf121ade40)