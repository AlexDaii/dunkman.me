---
layout: blog
title: JavaScript’s parseInt() at the Extremes
summary: Misuse of parseInt in JavaScript can lead to weird behavior if the number being parsed is large or small enough to be represented in E notation.
category: blog
redirect_from:
  - /articles/javascripts-parseint-at-the-extremes.html
---

<blockquote class="twitter-tweet" lang="en"><p><a href="https://twitter.com/adunkman">@adunkman</a> look what I found today! years = parseInt(this.seconds / 31556926); can you guess what&#39;s wrong? :)</p>&mdash; Scott Smerchek (@smerchek) <a href="https://twitter.com/smerchek/statuses/429292734977802242">January 31, 2014</a></blockquote>

I received that tweet from an ex-coworker of mine this morning. Well, can you guess what’s wrong?

<blockquote class="twitter-tweet" lang="en"><p><a href="https://twitter.com/smerchek">@smerchek</a> <a href="https://twitter.com/adunkman">@adunkman</a> Is this javascript? That small a number is represented in &#39;e&#39; notation. :/</p>&mdash; Make the World Dance (@dustyburwell) <a href="https://twitter.com/dustyburwell/statuses/429295164201238528">January 31, 2014</a></blockquote>

It has to do with how JavaScript represents extremely small and extremely large numbers: by using [E notation](http://en.wikipedia.org/wiki/Scientific_notation#E_notation).

```javascript
35 / 31556926 = 0.0000011091067615394477
32 / 31556926 = 0.0000010140404676932093
31 / 31556926 = 9.823517030777965e-7
```

Because `parseInt()` is a function for converting strings to integers, it first converts the number to a string, and then an integer.

```javascript
var number = 31 / 31556926;
> 9.823517030777965e-7

var str = number.toString()
> "9.823517030777965e-7"

parseInt(str);
> 9
```

So, how do we fix it? Well, stop using `parseInt()`. We should be using `Math.round()` here, which doesn’t convert the number to a string first.

Additionally, when using `parseInt()` a radix should always be supplied as a second argument, becuase it doesn’t always assume you’re using base-10. From [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt):

- If the input `string` begins with "0x" or "0X", radix is 16 (hexadecimal) and the remainder of the string is parsed.
- If the input `string` begins with "0", radix is eight (octal) or 10 (decimal).  Exactly which radix is chosen is implementation-dependent. ECMAScript 5 specifies that 10 (decimal) is used, but not all browsers support this yet.
- If the input `string` begins with any other value, the radix is 10 (decimal).

Just something to look out for!

<blockquote class="twitter-tweet" lang="en"><p><a href="https://twitter.com/smerchek">@smerchek</a> <a href="https://twitter.com/adunkman">@adunkman</a> The answer is &quot;everything.&quot; Everything is wrong with that.</p>&mdash; David Poeschl (@dpoeschl) <a href="https://twitter.com/dpoeschl/statuses/429302936737304576">January 31, 2014</a></blockquote>
