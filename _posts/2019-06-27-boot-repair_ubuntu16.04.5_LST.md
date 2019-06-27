---
layout: post
title:  "修复ubuntu启动项"
date:   2019-06-27 08:51:30
categories: Boot
tags: ubuntu bootrepair
mathjax: true
---
* content
{:toc}

针对双系统windows8.1和Ubuntu16.4，一直以来Ubuntu都能正常引导windows系统，但今天不知怎的引导里windows系统不见了，但在BIOS里有，因此可能是ubuntu的引导异常了。




## 启动Ubuntu系统并安装boot-repair

ubuntu系统还是可以正常进入的，在Terminal中安装boot-repair
```bash
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt-get update
sudo apt-get install boot-repair
sudo boot-repair
```
然后就是图形界面了，直接点击recommand repair按钮，等待修复