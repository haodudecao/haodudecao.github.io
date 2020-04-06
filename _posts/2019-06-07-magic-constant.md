---
layout: post
title: "Magic Constants"
subtitle: ""
author: "HaoDu"
tags:
  - PHP
---

魔术常量
```
<?php
namespace myMac;

class c
{
    /**
     * Created by PhpStorm.
     * User: lijunjie
     * Date: 2018/11/21
     * Time: 10:45 PM
     */
    function magicConstants()
    {
        echo '__LINE__:' . __LINE__ . "\n";
        echo '__FILE__:' . __FILE__ . "\n";
        echo '__DIR__:' . __DIR__ . "\n";
        echo '__FUNCTION__:' . __FUNCTION__ . "\n";
        echo '__CLASS__:' . __CLASS__ . "\n";
        echo '__TRAIT__:' . __TRAIT__ . "\n";
        echo '__METHOD__:' . __METHOD__ . "\n";
        echo '__NAMESPACE__:' . __NAMESPACE__ . "\n";
        echo testClass::class . "\n";
    }
}

(new testClass())->magicConstants();
```
结果
```
__LINE__:250
__FILE__:/Users/lijunjie/project/testpro/test.php
__DIR__:/Users/lijunjie/project/testpro
__FUNCTION__:magicConstants
__CLASS__:myMac\testClass
__TRAIT__:
__METHOD__:myMac\testClass::magicConstants
__NAMESPACE__:myMac
myMac\testClass
```

