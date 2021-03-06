---
layout: post
title: "leptjson项目总结：我从中学到了些什么？"
categories:
  - Programmer
tags:
  - TDD
  - Valgrind
  - C/C++
  - JSON
excerpt: 跟着Milo Yip的教程，我顺利完成了leptjson项目，这一过程中，除了JSON与C，我还学到了什么？
image: https://pic.imwzk.com/sofiya-levchenko-308460-unsplash.jpg
---

## 简介

先简单地介绍一下背景。大概一个月之前，在日常刷知乎的过程中，偶然刷到了[Milo Yip](https://www.zhihu.com/people/miloyip)的[《从零开始的JSON库教程》](https://zhuanlan.zhihu.com/p/22457315)，写得十分详细，而且在[作者的Github仓库](https://github.com/miloyip/json-tutorial/)中，有完整的代码与解析，因而顿时心动。拖了好一阵子，终于在十多天前决定开始动手。

简单来说，leptjson（即这个项目）使用C语言，从零开始实现了一个JSON的解析器与生成器。虽然教程的作者把它拆分为了一系列的单元练习（每个单元给出部分代码，要求补写部分代码），但是在我的实践中，并没有完全遵照作者的设定，而是把“回合制”改成了模拟实际的灵活开发。

这些天，跟着Milo Yip的教程，我顺利完成了leptjson项目，这一过程中，我学到了什么？

## C与JSON

最基础的，当然是这一项目所涉及的两种“语言”：C与JSON。

C语言是我的编程入门语言。在很久之前，我便用C语言写了不少程序，不过这些程序都是仅供OJ评判的算法题解，和常规意义上的程序，还是有一些差距。当然，后来在大学又继续学习了C++，写了一些更加有实际意义的程序。但不管怎样，leptjson可以说是我写的第一个贴近实际的C/C++程序，让我更加深切地感受了利用C/C++进行程序设计的体验。

比起略显老旧的XML，JSON在近些年应用广泛。在各种场合，我其实已经多次接触并使用JSON进行数据传输，倒也对其毫不陌生，在leptjson的实现过程中，我所学到的，是JSON的一些格式细节和规范。

## 浮点数与Unicode

把浮点数与Unicode并列，似乎有些不太合理，但于我而言，这样的组合却合情合理。

在学习计算机组成课程时，粗略地了解过计算机内部表示数字的方法，但由于浮点数的复杂性，当时只是一掠而过，并没有太深入了解。而在完成leptjson项目时，由于需要解析JSON字符串中的数字，我不得不去了解浮点数的字符表示与二进制表示，更进一步，由于采取测试驱动开发的方法，我还需要为浮点数的解析寻找各种极端测试用例（比如最小的浮点数），“被迫”学习了浮点数表示的IEEE标准。

而Unicode，我对其第一印象建立于学习Python的过程中。最初我使用Python 2，不论是编写爬虫还是处理文件，总是遇到各种奇怪的乱码，而网上搜索解决方案，便是无穷尽的使用`encode`与`decode`对字符串进行GBK、UTF-8等编码转换；而过了几年，则听说在Python 3中，如此的编码问题会少很多，原因则是Python 3使用Unicode。而到了leptjson，由于JSON解析过程中需要应对形如`\uxxxx`的转义字符，便较为详细地学习了Unicode与UTF-8编码。

因此，浮点数与Unicode，二者都是我似乎已然了解，却又一知半解的内容，通过leptjson，廓清了一些模糊的概念。

## 程序的内存管理

内存管理，是一个我之前近乎从未涉及的内容：写算法题，内存管理毫无意义；做实际项目，往往使用Python等更加现代的语言，不需要我操心于此。而用C语言实现leptjson，内存管理便是一道绕不过去的坎。

在leptjson中，我大量使用了C的指针与动态内存分配，这便带来较为严重的内存泄漏隐患。而内存泄漏的BUG，往往难以排除：肉眼看难以发现，程序测试也很难发现其存在。因此，在教程中，作者介绍了两款实用的工具：Windows平台下Visual C++的C Runtime Library与Linux平台的valgrind。借助工具，可以快速发现程序中的内存管理问题，比如内存泄漏、访问非法地址等等。

不过话说回来，即便有了工具的帮助，排查内存相关的BUG依旧是一件十分费力的工作，思路随着指针跳来跳去十分容易把自己弄晕。所以，我现在十分理解，为何在C之后，许许多多的程序语言都加上了自动内存回收等技术，不惜降低程序运行效率，以减轻开发者的负担。

## 测试驱动开发（TDD）

如果一定要说，测试驱动开发（Test-Driven Development, TDD）是我所认为的最大收获。

尽管在这个项目之前，我已经学习过软件工程课程，了解了不少软件开发的模式，但是至此我确是第一次听说TDD。大概是由于课程与教材的过时，TDD作为一种新兴的软件开发模式，我并没有在课堂上学到。

简单来说，按我的理解，TDD主要含义是：先写测试，再写实现，除了满足测试，绝不多写。似乎是一个很简单朴素的过程，但是根据我的实际体验，这样做使得我的开发过程十分愉快：比如下一步的目标是解析数字，那么首先，我需要编写关于解析数字的测试，确保覆盖所有可能的情况，此时尝试运行程序，会提示一系列的测试失败；然后，便针对这些新增的测试，实现对应的功能；再次尝试运行程序，如果通过所有测试，便完成了这一轮开发，否则，由于测试的存在，可以十分方便地定位错误的存在，进行解决。

在[我的Commit记录](https://github.com/keithnull/leptjson/commits/master)中，可以看到多组`tests`与`implementation`的提交记录，二者交替出现，构成了TDD的轮廓。

## 总结

总的来说，虽然leptjson这一项目并不复杂（总计不超过1000行代码且有现成的教程指引），但是作为我第一个贴近实际的C语言项目，从中我切实学到了不少内容：C与JSON语言本身，浮点数表示与Unicode等基础知识，内存管理，以及测试驱动开发的实践。

最后，再次感谢[Milo Yip](https://www.zhihu.com/people/miloyip)花费大量时间与精力编写的教程！
