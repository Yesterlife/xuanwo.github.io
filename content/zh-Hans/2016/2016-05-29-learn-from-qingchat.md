---
categories: Code
date: 2016-05-29T20:57:00Z
tags:
- Python
- qingchat

- QingCloud
title: 我从Qingchat中学到的教训
toc: true
url: /2016/05/29/learn-from-qingchat/
---

之前的一段时间一直在从事[Qingchat](https://github.com/Xuanwo/qingchat)的相关开发工作，这个项目是一个基于Mojo-Weixin提供的Rest API开发的一个微信机器人，承担了十余个群，上千人的微信直播活动。秉承着ACM训练出来的“暴力加乱搞”风格，我花了大概一个星期的课余时间，搞出了一个可用的版本。期间也迭代了很多次，既欣慰于Coding的力量，也苦恼于当初设计的不完善带来的种种问题。在这个项目即将大规模重构的前夕，我总结一下开发Qingchat的过程中的经验教训，希望自己能成为一个更好更聪明的Coder。

<!--more-->

# 经验教训

## 没有稳定性的世界

从最开始Coding的时候，就有着一个根深蒂固的信念：计算机不会出错。1是1，0是0，只要自己的实现是对的，那最后的结果也一定是对的。然后，真相并非如此：我们存在于一个没有稳定性的世界。
你访问的路径可能是不存在的，你访问的文件可能是不存在或者已经上锁，你期望的服务器响应因为网络环境太差变成了超时。不仅如此，有时候你还会遇到神出鬼没的黑客们，稍有不慎你可能就永远失去了对数据的掌控权（*我测试用的服务器被人暴力尝试登陆34万次*）。
所以我们在编程的时候就应当完全抛弃“这个服务是稳定的”这种前提，学会在万物皆有可能出错的假设下编程，不要轻视程序的鲁棒性。你永远都不会知道用户会以何种方式使用你的程序，同样的，你也不会知道用户到底会输入什么样的数据。灵活运用“防御式编程”和“让它出错”两种思想，让你的代码更健壮。

## 拥抱多线程，走向非阻塞

现在回过头来看，自己当初实现的群发功能就是一个彻头彻尾的失败品：容错性差，效率低下。这些问题其实是由同一个原因导致的：我使用了一个单线程阻塞式的实现，不仅如此，还没有做任何异常的处理。这样导致的结果就是只要某个环节出错，整个群发任务就会直接退出。速度是跟微信群的数量成正比的，群的数量上升之后，群发速度过慢成了我的同事们经常向我吐嘈的问题。
开发的时候不计算网络传输延时绝对是一个重大失误——实际上，网络延时是整个系统的关键瓶颈。为了解决这个问题，Node.js 采用了一种单线程非阻塞的设计（一家之言，轻喷）。对于Python来说也是一样的，适度地拥抱多线程开发以提高并行效率，多采用非阻塞式设计以避免单个任务执行时间过长拖慢运行速度。
在一次去上课的路上，我形象地把这种设计比作“射后不管”，好像是飞毛腿导弹？

## 开源非万能

我原来是一个开源的死忠粉，认为开源可以解决一切问题，那个时候每天思考的问题就是为什么Windows不开源？等到真的进入业界，才明白自己的这种想法有多么幼稚。
除去那些大型的有基金会在背后支撑的项目，绝大多数项目完全依靠开发者个人热情来维持，某些涉及到商业支持的项目更是存在着“人走项目死”的现象。比如我参与管理的[Hexo](https://github.com/hexojs)项目在作者tommy开始实习之后就进展缓慢，再比如我接手但是现状非常差的[staticfile](https://github.com/staticfile/static)（*很抱歉我并没有能够做出什么正面的贡献，反而让状况变得更加糟糕了*）。
这些实践都告诉我，开源并不是万能的，真正想要解决一个问题，需要一个完善的制度以及各方面的激励机制。单纯依赖于开发者个人的热情往往不利于一个项目的长远发展，而在自行开发的项目中依赖于一个开源项目往往会带来更多的风险。开发者不一定会开发你期望的功能，开发者也不一定会按照你期望的进度进行开发。
作为我个人，我依然是一个热爱开源项目，崇尚开源理念的人；然而作为一个产品的开发者，一个服务的提供者，你必须权衡自行开发，采购商业产品以及使用开源项目之间的利弊。不同的选择取决于不同的项目，不同的需求，不同的自主开发能力，不同的后续维护能力。一味的强调开源理念，不顾实际，往往对开源事业的发展有害无益，挫伤人们对开源的信赖。

# 未来展望

一个项目最具活力与激情的时刻就是在设计之初实现之前，等到实现的时候往往寸步难行，到了后续维护的时候时常想就此了断。我希望这个项目能够存活更长的时间，希望自己向同事们提出的构想都能够一一变为现实。本次重构希望能够实现一个更加稳定更加高效的Qingchat，希望能够进一步减少重复劳动，提高效率。
最后感谢同事们的不杀之恩，感谢青云QingCloud的自由空间，可以允许我以自己想要的方式完成工作。最后以青云CTO Reno在某次会上说的话自勉：“你们是青云的Devloper，No excuse。”

# 更新日志

- 2016年05月30日 初稿
