---
title: ctfshow信息搜集
date: 2025-09-05 14:44:46
tags:
- CTF
- Web
- 信息搜集
categories:
- ctfshow-web入门wp
description: " "
---



# web1

**F12查看源代码**

**ctfshow{003df495-852d-4326-9f57-aee33ebdbb5a}**



<img src="\images\article_images\image-20250402224023049.png" alt="image-20250402224023049" style="zoom:50%;" />

&nbsp;



---

# web2



<img src="\images\article_images\image-20250402224539067.png" alt="image-20250402224539067" style="zoom:35%;" />

&nbsp;



**==题解一==**

**`ctrl + u` 查看网页源代码 / 在URL前加上 `view-source: `**

<img src="\images\article_images\image-20250402224707389.png" alt="image-20250402224707389" style="zoom: 33%;" />

&nbsp;



**==题解二==**

**刷新的同时按F12查看源代码**

<img src="\images\article_images\image-20250406205446977.png" alt="image-20250406205446977" style="zoom: 33%;" />

&nbsp;



---

# web3

**抓包**

**Flag: ctfshow{a2f4a406-c69c-4135-bb09-197a2230b537}**

<img src="\images\article_images\image-20250406203240665.png" alt="image-20250406203240665" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250406202851164.png" alt="image-20250406202851164" style="zoom:33%;" />

&nbsp;



---

# web4

访问 `url/robots.txt` ，根据提示 访问 `url/flagishere.txt`

```
ctfshow{d1fc90c5-0c02-4457-a8fd-446ddf5f6c3f}
```

<img src="\images\article_images\image-20250406203833372.png" alt="image-20250406203833372" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250406203847247.png" alt="image-20250406203847247" style="zoom:33%;" />

&nbsp;

---

# web5

**phps泄露，访问 `url/index.php`**

<img src="\images\article_images\image-20250406204721617.png" alt="image-20250406204721617" style="zoom: 33%;" />

&nbsp;

---

# web6

**访问 `url/www.zip`，然后访问 `url/fl000g.txt`**

<img src="\images\article_images\image-20250406210448625.png" alt="image-20250406210448625" style="zoom:50%;" />

&nbsp;

<img src="\images\article_images\image-20250406210436670.png" alt="image-20250406210436670" style="zoom:33%;" />

&nbsp;



---

# web7

**访问 `url/.git`**

<img src="\images\article_images\image-20250406211902205.png" alt="image-20250406211902205" style="zoom:33%;" />

&nbsp;





---

# web8

**访问 `url/.svn`**

<img src="\images\article_images\image-20250406213018884.png" alt="image-20250406213018884" style="zoom: 33%;" />

&nbsp;

---

# web9

**访问 `url/index.php.swp`**

<img src="\images\article_images\image-20250406213659612.png" alt="image-20250406213659612" style="zoom:50%;" />

&nbsp;



---

# web10

**F12查看cookies 或者 抓包**

**URL解码**

<img src="\images\article_images\image-20250406215028988.png" alt="image-20250406215028988" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250406215252742.png" alt="image-20250406215252742" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250406215009170.png" alt="image-20250406215009170" style="zoom:33%;" />

&nbsp;



---

# web11

**域名查询**

**[域名解析查询 | DNS查询 ](https://ipw.cn/dns/)**

**[阿里云 ](https://zijian.aliyun.com/)**

&nbsp;

---

# web12

**访问 `url/robots.txt`  接着访问 `url/admin`**

**账号：`admin`  密码：`372619038`(帮助热线号码)**

**`ctfshow{e4ded847-4db4-4295-b998-1ac949c90089}`**

<img src="\images\article_images\image-20250406230955467.png" alt="image-20250406230955467" style="zoom: 33%;" />

&nbsp;

<img src="\images\article_images\image-20250406230856053.png" alt="image-20250406230856053" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250406231153154.png" alt="image-20250406231153154" style="zoom:33%;" />

&nbsp;



---

# web13

**拉到最下面，`document`下载，查看用户名和密码，访问 `靶机/system1103/login.php`**

**`ctfshow{16b63f10-2d6f-41fe-affc-9b4588184d47}`**

<img src="\images\article_images\image-20250406231933953.png" alt="image-20250406231933953" style="zoom: 23%;" />

&nbsp;

<img src="\images\article_images\image-20250406231950964.png" alt="image-20250406231950964" style="zoom:50%;" />

&nbsp;

<img src="\images\article_images\image-20250406231858102.png" alt="image-20250406231858102" style="zoom: 25%;" />

&nbsp;



---

# web14

**访问 `url/editor` 在图片上传处可以查看整个目录 找到flag的位置**

**`nothinghere` 和 `editor` 在同一目录下**

**ctfshow{efea91c6-f966-4022-adc4-3f3b87fa8170}**

<img src="\images\article_images\image-20250406234839218.png" alt="image-20250406234839218" style="zoom:99%;" />

&nbsp;

<img src="\images\article_images\image-20250406234743967.png" alt="image-20250406234743967" style="zoom:33%;" />

&nbsp;



---

# web15

**访问 `url/admin` 查询QQ得知现居陕西西安，回答密保问题获得重置密码**

**ctfshow{af4c6156-aac0-4702-b47c-0d44d354d067}**

<img src="\images\article_images\image-20250407002355266.png" alt="image-20250407002355266" style="zoom: 33%;" />

&nbsp;

<img src="\images\article_images\image-20250407002445091.png" alt="image-20250407002445091" style="zoom: 33%;" />

&nbsp;



---

# web16

**访问 `url/tz.php` ,找到 `PHPINFO`，查看php信息**

**ctfshow{1191cce9-d664-40fa-81cc-c0bb07fe2f1c}**

<img src="\images\article_images\image-20250407135131371.png" alt="image-20250407135131371" style="zoom:99%;" />

&nbsp;

<img src="\images\article_images\image-20250407135041938.png" alt="image-20250407135041938" style="zoom: 33%;" />

&nbsp;

<img src="\images\article_images\image-20250407135131371.png" alt="image-20250407135131371" style="zoom: 33%;" />

&nbsp;

---

# web17

**访问 `url/backup.sql`**

**ctfshow{2b4eda41-3d14-4ee7-adf1-c78bb5e867d3}**

<img src="\images\article_images\image-20250407135741167.png" alt="image-20250407135741167" style="zoom: 33%;" />

&nbsp;



---

# web18

**在`Flappy_js.js`看到提示信息，访问 `url/110.php`**

**ctfshow{a48fcacb-d9f4-4e3e-8e20-6f2215fe7cf5}**

<img src="\images\article_images\image-20250409120021668.png" alt="image-20250409120021668" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250409120037458.png" alt="image-20250409120037458" style="zoom:33%;" />

&nbsp;

<img src="\images\article_images\image-20250409120053482.png" alt="image-20250409120053482" style="zoom:50%;" />

&nbsp;





---

# web19

**源代码有账号密码，post得到ctfshow{5d6760a0-d647-4cd7-bed1-076c68fc072c}**

<img src="\images\article_images\image-20250409120921147.png" alt="image-20250409120921147" style="zoom:33%;" />

&nbsp;

---

# web20

**访问 `url /db/db.mdb`**

<img src="\images\article_images\image-20250409121741141.png" alt="image-20250409121741141" style="zoom:33%;" />

&nbsp;
