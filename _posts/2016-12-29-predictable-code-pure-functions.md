---
layout: post
title: 'Predictable code: Pure functions'
date: 2016-12-29 04:45:40.000000000 +13:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:  [Functional-Programming]
tags: [F#, Functional-Programming]
meta:
  _edit_last: '1'
  _mytory_markdown_etag: '"0b36abd74b4da18a9c80c867f5d824e75b08eb65"'
  mytory_md_path: https://raw.githubusercontent.com/willsam100/CodingWithSam/master/pureFunctions.md
  mytory_md_text: ''
  mytory_md_mode: url
  mashsb_timestamp: '1589412278'
  mashsb_shares: '0'
  mashsb_jsonshares: '{"total":0,"error":"","facebook_shares":0}'
  mashsb_shorturl: http://www.codingwithsam.com/predictable-code-pure-functions/
  dsq_thread_id: '6139184236'
author:
  login: sam
  email: willsam100@gmail.com
  display_name: Sam Williams
  first_name: Sam
  last_name: Williams
permalink: "/2016/12/29/predictable-code-pure-functions/"
---

This is another post focusing on the basic understanding of functional programming: pure functions. Despite the number of posts that already explain pure functions I hope this post still provides value.

## prerequisites
- A simple understanding of an OOP language such as C# or Java

## Methods and inputs
<table class="pre">
<tr>
<td class="lines">
<pre class="fssnip"><span class="l"> 1: </span>
<span class="l"> 2: </span>
<span class="l"> 3: </span>
<span class="l"> 4: </span>
<span class="l"> 5: </span>
<span class="l"> 6: </span>
<span class="l"> 7: </span>
<span class="l"> 8: </span>
<span class="l"> 9: </span>
<span class="l">10: </span>
</pre>
</td>
<td class="snippet">
<pre class="fssnip highlighted"><code lang="csharp"><span class="k">public</span> <span class="k">class</span> Simple
{
    <span class="k">public</span> <span class="k">int</span> _factor <span class="o">=</span> <span class="n">2</span>;
    <span class="k">public</span> <span class="k">int</span> Double(<span class="k">int</span> a)
    {
        <span class="k">return</span> a <span class="o">*</span> <span class="k">this</span>._factor;
    }

    <span class="c">//...rest of implementation</span>
}
</code></pre>
</td>
</tr>
</table>

With the above method ```Double``` how many inputs does it take? The answer in programming test would be one. However, if you were writing a unit test for this method, then it would be closer to 2 since you must test the ```_factor``` field. Therefore, this method really takes two inputs, the ```int a``` and field ```_factor```. We can generalize this a little bit more. For any method, the number of inputs it takes is: input count = parameters + 1. The plus one represents ```this``` for the given class. For a function in the context of functional programming, all parameters should be explicit.

## Methods and outputs
<table class="pre">
<tr>
<td class="lines">
<pre class="fssnip"><span class="l">1: </span>
<span class="l">2: </span>
<span class="l">3: </span>
<span class="l">4: </span>
<span class="l">5: </span>
<span class="l">6: </span>
<span class="l">7: </span>
<span class="l">8: </span>
<span class="l">9: </span>
</pre>
</td>
<td class="snippet">
<pre class="fssnip highlighted"><code lang="csharp"><span class="k">public</span> <span class="k">class</span> Simple
{
    <span class="k">public</span> <span class="k">void</span> PrintDouble(<span class="k">int</span> a)
    {
        Console.WriteLine(a <span class="o">*</span> <span class="n">2</span>);
    }

    <span class="c">//...rest of implementation</span>
}
</code></pre>
</td>
</tr>
</table>
I've written about this before with my post on 	[immutability]({{ "2016/12/14/predictable-code/" | relative_url }}), so i will be brief. A method that returns void is hard to reason about. The developer must look at the method to know what it is doing. There is no way to understand it by just looking at the types and name. As above, to make methods more functional, always return a value. For C# this is not always possible (or really hard) since C# is missing some essential data types. See my [post]({{ "/2016/12/03/net-none-none-none-or-exceptions/" | relative_url }}) on exceptions for more details.

## Being explicit
When writing methods, what if we changed the rules, and said that every parameter must be explicit, and that every method must have an output? Such a method (function) is called a pure function. The proof for a pure function is 'a function that always the same result given the same two inputs' meaning the function as no state. Describing pure functions another way: 'once run there is no way of telling that it has been called in the context of a system' meaning the function does not affect the state of the system in any way. This is because nothing has been changed, no variables, no  graphics, no printing, nothing. In C#, the best easiest way of knowing that you method is pure is mark it as static and don't create any static fields. If this sounds like madness keep reading! (I'm not proposing to write like this in C# or Java, those languages were not made for this).

## What does this give me
I will now refer to these static methods as functions. Writing an entire program like this gives us a few benefits:
- Unit testing is easy: no mocking (since everything is explicit). An output is returned so assertion is straight forward as well.
- It's easy to read the program since every developer knows that each function takes in the data it requires and returns some new representation of that data
- There is no mutation of any object passed in, the objects passed in are all immutable.
- Side effects are now hard, so are reduced to what is essential and placed in appropriate parts of the program.

## What's that about side effects?
Let's go over that last point. Since all methods are pure functions, it's really hard to have any kind of side effect. Actually I lied a bit; it's impossible. To get around this, we need impure functions, or functions that have side effects, hmm, or dare i say it: methods :(. This is of course so that our program does something, e.g. making a network call, writing to disk etc, and so is not a bad thing. If we make the default to write pure functions and only use impure functions (methods) when they are needed then the core of the program will be easier to understand, and also easier to test. The pure functions should not call the impure functions or they will no longer be pure as side effects would occur.

## It's not so scary
A pure function is just a method that has all parameters explicitly passed in and always returns value. Pretty simple really! Now it's time to write all your C# programs using only static methods and no static variables! Just joking, use F#! In F# writing pure functions is easy as the language supports it and the language also has data types for structuring the program according to these rules.

## Extra reading
The F# complier does not enforce pure functions, so a developer could place some side effects in the middle of a pure function and the function would no longer be pure. There are functional programming languages that restrict side effects, forcing them to the edge of the application. Complier errors will result if the function attempts to write to disk without the correct function signature. Haskell is the most well known language for this. Because of this strictness, it's possible to read the types of the functions and have a pretty good idea what it's going to do.
<table class="pre">
<tr>
<td class="lines">
<pre class="fssnip"><span class="l">1: </span>
<span class="l">2: </span>
</pre>
</td>
<td class="snippet">
<pre class="fssnip highlighted"><code lang="fsharp"><span class="k">let</span> <span onmouseout="hideTip(event, 'fs1', 1)" onmouseover="showTip(event, 'fs1', 1)" class="id">simple</span> <span class="o">=</span> <span class="s">&quot;test&quot;</span>
<span class="k">let</span> <span onmouseout="hideTip(event, 'fs2', 2)" onmouseover="showTip(event, 'fs2', 2)" class="fn">double</span> <span onmouseout="hideTip(event, 'fs3', 3)" onmouseover="showTip(event, 'fs3', 3)" class="id">x</span> <span class="o">=</span> <span onmouseout="hideTip(event, 'fs3', 4)" onmouseover="showTip(event, 'fs3', 4)" class="id">x</span> <span class="o">*</span> <span onmouseout="hideTip(event, 'fs3', 5)" onmouseover="showTip(event, 'fs3', 5)" class="id">x</span>
</code></pre>
</td>
</tr>
</table>