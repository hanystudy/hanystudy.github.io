---
layout: post
status: publish
published: true
title: JNI技术初步(五)
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 105
wordpress_url: http://www.hanyi.name/blog/?p=105
date: 2008-10-18 16:59:48.000000000 +08:00
categories:
- 转载
- java
tags:
- Java
- jni
comments:
- id: 7544
  author: next day delivery viagra
  author_email: pawrfhjh@pfgjeccj.com
  author_url: http://medecinesupply.socialgo.com/magazine/read/viagra-with-no-prescription_14.html
  date: !binary |-
    MjAxMC0wNi0wNCAwNTozODozOSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMTozODozOSArMDgwMA==
  content: <a href="http://medecinesupply.socialgo.com/magazine/read/cialis-without-rx_15.html"
    rel="nofollow">headaches heartburn ciali</a> , [url="http://medecinesupply.socialgo.com/magazine/read/cialis-without-rx_15.html"]headaches
    heartburn ciali[/url] , http://medecinesupply.socialgo.com/magazine/read/cialis-without-rx_15.html
    headaches heartburn ciali ,
- id: 7601
  author: phentermine causes acne
  author_email: gyvbposf@yucplvdg.com
  author_url: http://www.esnips.com/user/angelinajordany
  date: !binary |-
    MjAxMC0wNi0wNCAwNjoyNDoxMCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMjoyNDoxMCArMDgwMA==
  content: <a href="http://forum.ywcasudbury.ca/members/generic-Phentermine.aspx"
    rel="nofollow">lannett phentermine pictures</a> , [url="http://forum.ywcasudbury.ca/members/generic-Phentermine.aspx"]lannett
    phentermine pictures[/url] , http://forum.ywcasudbury.ca/members/generic-Phentermine.aspx
    lannett phentermine pictures ,
- id: 7630
  author: qualitative quantitative analysis tramadol
  author_email: tduptkux@ydbvxzfz.com
  author_url: http://community.miragespeakers.com/members/Tramadol-online-without-prescription.aspx
  date: !binary |-
    MjAxMC0wNi0wNCAwNzoxMTo0MSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMzoxMTo0MSArMDgwMA==
  content: <a href="http://technorati.com/people/adipexp126m" rel="nofollow">adipex
    alturnatives</a> , [url="http://technorati.com/people/adipexp126m"]adipex alturnatives[/url]
    , http://technorati.com/people/adipexp126m adipex alturnatives ,
- id: 8069
  author: tramadol hydrochloride xja pl
  author_email: dogesbal@nxejyrsw.com
  author_url: http://connect.gulflive.com/user/Tramadol-Online-9138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMjowOTo1NSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNDowOTo1NSArMDgwMA==
  content: Perfect site, i like it!
- id: 8242
  author: phentermine no rx federal express
  author_email: wdywvewn@ftnyzvch.com
  author_url: http://connect.al.com/user/phenterminev138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMzozNjo0OSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNTozNjo0OSArMDgwMA==
  content: Very interesting site. Hope it will always be alive!
- id: 8358
  author: tramadol overnight daily
  author_email: akbzziti@qkbiyxog.com
  author_url: http://connect.cleveland.com/user/Tramadol-Online-e138/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMDozNDowMyArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNjozNDowMyArMDgwMA==
  content: Great. Now i can say thank you!
- id: 8623
  author: how is valium made
  author_email: pfzamspc@urtravfe.com
  author_url: http://connect.masslive.com/user/Valium-Online-z138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMjo0MzoxMiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxODo0MzoxMiArMDgwMA==
  content: Incredible site!
- id: 8715
  author: green xanax bars mg
  author_email: pefetann@njvomzpo.com
  author_url: http://connect.gulflive.com/user/Xanax-Online-z138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMzoyNjozMCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxOToyNjozMCArMDgwMA==
  content: I want to say - thank you for this!
---
 总的来说，JNI应当是java初学者须掌握的内容。尽管教科书中涉及的极少，JNI也破坏了java重要的可移植性，但JNI并未因此被弃用，反而被Sun列入JDK不可缺少的一部分——这至少说明了java并非像一些人说得那么神通广大。

 正如原文作者最初所说的，JNI适用于许多程序设计语言，甚至包括汇编程序。但理解了java内涵的人都会小心翼翼去使用JNI，而非将其当成是万能的解决方案。

 感谢本系列第1-4篇文章的原作者，很多是被原文采纳，其余内容也做了详细的参考，在此一并感谢。

 cnweblog.com <span style="color: #000000;">刘正伟 </span><span style="color: #000000;">sundy26@126.com</span>

 blog.edu.cn  <span class="style3">xdsamuel</span>

<span class="style3"> gceclub.sun.com.cn Sun中国技术社区 陈健明 华中师范大学网络与通讯研究所 <span style="color: #000000;">chenjm2000@hotmail.com</span></span>
