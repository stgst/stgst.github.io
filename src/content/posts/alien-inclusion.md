---
title: ( Cyber-EDU ) alien-inclusion
published: 2024-08-08
description: ''
image: ''
tags: [Writeup, CTF, Web]
category: 'WriteUps'
draft: false 
---

# alien-inclusion ( Write Up )

[alien-inclusion](https://app.cyber-edu.co/challenges/82a11560-3621-11eb-861a-3777b029f577?tenant=cyberedu)

## 🔻 Step 1. 觀察與測試

網站後端原始碼
```php
<?php

if (!isset($_GET['start'])){
    show_source(__FILE__);
    exit;
} 

include ($_POST['start']);
echo $secret;
```
1. 這段程式碼必須同時接收到 `$_GET['start']` 和 `$_POST['start']` 才能正常運行。
2. 已知 flag 存放的檔案路徑，故 `$_POST['start']` 應為 `/var/www/html/flag.php`。

## 🔻 Step 2. 修改與嘗試

1. 嘗試在網址後面加上 `/?start=123`，成功繞過判斷式，沒有顯示任何原始碼。
2. 使用 Postman 工具執行 POST 請求，在 Body 中使用 form-data 型態，key 輸入 `start`，value 輸入 `/var/www/html/flag.php`。
3. 送出後即獲得 flag。

## CWE 資訊

（本題應為練習，沒有適合的 CWE 資訊）

## 漏洞修補建議

- 修改程式碼，避免 GET 與 POST 讀取的資料相關聯。

## 總結

本題運用的是 PHP 對於讀取 GET 請求的特性，同時可以提過 POST 與 GET 方法傳遞資料給相同的對象，整體而言是有難度的，需要尋找較多關於 PHP 語法的資源。