---
title: ( Cyber-EDU ) file-crawler
published: 2024-08-08
description: ''
image: ''
tags: [Writeup, CTF, Web]
category: 'WriteUps'
draft: false 
---

# file-crawler ( Write Up )

[file-crawler](https://app.cyber-edu.co/challenges/981dca90-04bf-11ec-920e-fd2fbb634a4c?tenant=cyberedu)

## 🔻 Step 1. 觀察與測試

1. 在網頁原始碼中，發現路徑為 `/local?image_name=static/path.jpg` 的圖片。
2. 基本的觀察後，可以發現 `image_name` 後面接著的是一個檔案路徑。
3. 根據題目的說明 `The flag is located in a temporary folder`，可以判斷 flag 藏在暫存資料夾中。

## 🔻 Step 2. 修改與嘗試

1. 已知 Linux 主機的暫存資料夾位於 `/tmp/`。
2. 嘗試幾次將路徑修改為 `/local?image_name=/tmp/flag.txt` 及 `/local?image_name=/tmp/flag.php` 等。
3. 最後在路徑為 `/local?image_name=/tmp/flag` 時，獲得了名為 local 的檔案，取得本題 flag。

## CWE 資訊

CWE-22: Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal')
CWE-36: Absolute Path Traversal
CWE-37: Path Traversal: '/absolute/pathname/here'

## 漏洞修補建議

- 使用其他方法讀取檔案
- 限制程式所能讀取的內容與範圍

## 總結

根據題目的提示以及對 Linux 基礎的了解，反覆的嘗試後即可利用路徑遍歷完成題目，是很典型的漏洞之一。