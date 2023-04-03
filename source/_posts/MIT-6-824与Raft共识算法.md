---
title: MIT 6.824与Raft共识算法
date: 2023-04-03 07:25:53
tags: ["Raft","分布式"]
---

## Raft 共识算法
Raft是非常出名的一个共识算法，关于Raft的介绍网络上已经有很多帖子了，但我建议你还是直接看原版论文：https://ramcloud.atlassian.net/wiki/download/attachments/6586375/raft.pdf，如果缺失英语不行，先看中文翻译版：https://github.com/maemual/raft-zh_cn/blob/master/raft-zh_cn.md

了解的差不多了以后，基本上就要开始动手去写一个Raft了，这里推荐直接使用MIT 6.824的实验来做，比较容易上手，注意这个实验是使用Go语言做的，如果不熟悉Go语言，可以快速学习一下


## MIT 6.824
MIT 6.824是一个关于分布式算法的公开课，一般和数据库内核相关的课程一起学习（例如CMU15-445）,这个课可以再下面的地址看到翻译版本：
<iframe src="//player.bilibili.com/player.html?aid=87684880&bvid=BV1R7411t71W&cid=155852756&page=2" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

我并没有把这个课程看完，我的重点在于这个课程的第二个Lab，用Go语言做了一个Raft，这是入门Raft的一个好材料，该Lab的地址在这里：http://nil.csail.mit.edu/6.824/2022/labs/lab-raft.html，提交作业的地址在：https://6824.scripts.mit.edu/2022/handin.py/

由于时间关系，我也没有把这个实验做完，只做了前两部，即：选举和日志同步，我的代码在：https://github.com/eddyli1989/6.824

下面是一些建议：
- 在自己动手编写之前，不建议你在网上看别人的答案
- 不要着急写代码，在写代码之前先想好程序的主要结构是什么样子，需要什么数据结构，如何驱动整个程序，甚至每个场景下应该如何处理，都先想好，如果实在想不出来，可以画流程图
- 搞清楚每个状态下的每个变量是干啥用的，否则你只能在一次次莫名其妙的失败中去定位，非常消耗时间(例如我)
- 
