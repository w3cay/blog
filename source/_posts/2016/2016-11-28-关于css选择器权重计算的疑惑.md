---
layout: "post"
title: "关于CSS选择器权重计算的疑惑"
date: "2016-11-28 16:43"
---

今天注意到这样一个问题❓
> 下面代码背景色仍然是灰色，所以id权重为100，class权重为10，是不正确的吧？

```html
<p id="testId" class="class1 class2 class3 class4 class5 class6 class7 class8 class9 class10 class11">
</p>
```
```css
#testId {
    background-color: grey;
}
.class1.class2.class3.class4.class5.class6.class7.class8.class9.class10.class11 {
    background-color: red;
}
```
效果
![效果](/blog/images/2016/11/csstest.png)


刚看到这个问题，自己也有点蒙，不是说 Id 选择器权重代表 100 ，class 为 10 吗，为什么还是灰色？当我试了一下才发现，确实是这样的。后来我把代码改成这样

```html
<div class="class1">
  <div class="class2">
    <div class="class3">
      <div  class="class4">
        <div class="class5">
          <div class="class6">
            <div class="class7">
              <div class="class8">
                  <div class="class9">
                    <div class="class10">
                      <div id="testId" class="class11">
                        测试
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

```

```css
#testId {
    background-color: grey;
}
.class1 .class2 .class3 .class4 .class5 .class6 .class7 .class8 .class9 .class10 .class11 {
    background-color: red;
}
```
效果没变
![效果](/blog/images/2016/11/csstest.png)

好奇心驱使我找资料查了下，才恍然大悟，原来样式选择器的权重值没有 10 进制这个规则，我猜测应该是我们把规范中的 0，0，1，0 理解成了 0010，形成了思维定式 ，使我们误以为这是可以满 10 进 1 的。资料上是这样说的：
> Unfortunately, specificity is not calculated in base 10 but a high, unspecified, base number. This is to ensure that a highly specific selector, such as an ID selector, is never overridden by lots of less specific selectors, such as type selectors. However, if you have fewer than 10 selectors in a specific selector, you can calculate specificity in base 10 for simplicity’s sake.

_参考《Css.Mastery.Advanced.Web.Standards.Solutions.(2nd.Edition)》_

这个问题使我意识到，在平常学习的时候，还是要多注重细节问题，书读百遍其义自见。
