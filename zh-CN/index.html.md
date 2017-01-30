---

title: BitGo API 参考

language_tabs:
- javascript
- shell

toc_footers:
- <a href="https://www.bitgo.com/" target="_new">BitGo 网站</a>
- <a href="https://www.bitgo.com/terms" target="_new">服务协议</a>
- <a href="https://www.bitgo.com/settings" target="_new">BitGo 设置 (获取 API 访问令牌)</a>
- <a>语言</a>
- <a href="../index.html">- English</a>
- <a href="../ja/index.html">- Japanese 日本語</a>
- <a href="./index.html">- Chinese Simplified 简体中文</a>

---

# 入门 Getting Started

<aside class="info">
我们的开发者平台已经上线。访问 <a href="https://www.bitgo.com/platform">BitGo 平台入口</a>  注册以获取集成支持、访问令牌和更多信息。
</aside>

### 概述 Overview

BitGo 提供易用且强健的 REST-ful API 和易用的客户端 javascript SDK， 可用于集成多重签名技术到你现有的比特币（bitcoin）应用和服务。

BitGo SDK 能进行下列操作：

* 创建P2SH（多重签名）钱包
* 分层确定性钱包（Hierarchical Deterministic Wallet） 的管理 (BIP32)
* 创建交易
* 交易签名
* 花费限额
* 多签名者钱包流

### 多重签名钱包 Multi-Signature Wallets

多重签名钱包的首要好处是，使多台机器及人员可以协同工作一起批准交易。 交易没有多重签名，那么批准交易所需的全部凭据都将存储于某个人的一台机器。 若那个人，或那台机器被攻击者掌控，那你的所有比特币都将危在旦夕。

通常来说，要保护某个人/某台机器系统十分困难，许多提供商都简单的选择“冷存储”并且将比特币全部移到线下。

多重签名钱包提供了你所期待的现代比特币地址的灵活性，不需要将比特币下线。 BitGo API 使你可以在自己的应用程序中使用多重签名特性，让你可以充分利用多用户的灵活性，联署人和应用最先进技术的欺诈检测系统助你防御丢失和盗窃。

若要获取更多信息，请阅读 <a href="https://www.bitgo.com/p2sh_safe_address" target="_new">BitGo 白皮书</a>.

### HD 钱包 HD Wallets

所有BitGo钱包均为分层确定性钱包（hierarchical deterministic wallet）——即“HD 钱包”。 HD 钱包使用比特币的<a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki" target="_new">BIP32 标准</a>实现。 因此，BitGo 的 HD 钱包基于钥匙串建立，而非多个独立的密钥，并提供二种清晰的安全和隐私增强特性：

* 更安全的备份
    
    因为钥匙串可由一个密钥进行备份，一个钱包可以使用许多由同一个备份密钥维护的公钥。

* 区块链隐私
    
    使用HD钱包，应用程序可以在每次交易时创建新的密钥，因此交易不会显示来自同一个钱包。 此举可以保护钱包持有者避免揭露钱包的真实大小。

## 软件开发套件 Software Development Kit

BitGo API 提供给开发者一种可以创建并管理多重签名钱包的手段，在使用比特币网络时避免受政策所累。 不过，一些敏感操作，诸如创建用户私钥和对交易进行签名，必须在客户端进行操作。

因此，我们推荐使用由我们提供的 <a href="https://github.com/BitGo/BitGoJS" target="_new">软件开发套件 (SDK)</a>，使用我们的API实现这些客户端的钱包功能和接口。 事实上，开发者在使用BitGo API时，可能会同时使用BitGo客户端SDK和BitGo REST 服务。

目前，我们提供Javascript语言的SDK，可运行于node.js或浏览器。 若你想要使用尚不支持的编程语言，请看本文的“BitGo Express REST API”章节，以了解如何设置BitGo Express，或联络我们获得进一步帮助。

安装 Javascript SDK (通过 npm)

`npm install bitgo --save`

安装 Javascript SDK (通过 Github)

* <a href="https://github.com/BitGo/BitGoJS" target="_new">访问我们的开源SDK网页</a> 位于 Github。
* 安装 git 和 nodejs/npm (为使用后续范例推荐安装)。
* 执行此命令clone我们的仓库到本地: `git clone git@github.com:BitGo/BitGoJS.git`
* 在 BitGoJS 目录下执行: `npm install` 命令安装依赖。
* 在examples目录下有关于本SDK的范例！在范例目录下，执行

`node auth.js <testusername> <testpassword> 0000000`

### 导入并初始化程序库 Importing and initializing the library

```javascript
// 如果要从包文件导入
var BitGoJS = require('BitGoJS/src/index.js');
var bitgo = new BitGoJS.BitGo();

// 如果要从 npm install bitgo 导入
// var bitgo = require('bitgo');

bitgo.ping({}, function(err, res) {
    // 代码
});
```

要导入程序库，你只需要 `src/index.js` 文件。 随后你可以使用 `BitGoJS.BitGo()` 进行SDK初始化。

| 参数            | 值                    |
| ------------- | -------------------- |
| useproduction | 是否连接到生产环境。默认值 false. |

本Javascript SDK同时支持Promise和Callback。如果你将callback最为最后一个参数传递，将会返回callback格式。反之，返回的将是promise格式。

### 测试环境的重要注意事项 Important notes on test environment

我们BitGo测试环境中的SDK和范例默认是连接到Bitcoin TestNet。 请参考 [测试环境](#bitgo-api-endpoints) 章节以获取更多讯息。

## BitGo API 端点 BitGo API Endpoints

```javascript
var BitGoJS = require('BitGoJS/src/index.js');

var useProduction = false;
var bitgo = new BitGoJS.BitGo(useProduction);

bitgo.ping({}, function(err, res) {
  if (err) {
    console.dir(err);
  } else {
    console.log(JSON.stringify(res, null, 4));
  }
});
```

```shell
TEST_ENDPOINT='https://test.bitgo.com/api/v1'
PROD_ENDPOINT='https://www.bitgo.com/api/v1'

curl "$TEST_ENDPOINT/ping"
```

BitGo 有 2 套独立的环境供开发和生产。出于安全原因，所有 BitGo API 请求均使用 TLS 通过 HTTPS 传输。

全部响应均为 content-type `application/json`

> 响应示例

    {
        "status": "service is ok!",
        "environment": "BitGo Test"
    }
    

### 生产环境 Production Environment

BitGo生产端点已上线，被我们的合作伙伴和我们自己位于www.bitgo.com的网页应用所使用。

* 生产站点: https://www.bitgo.com/
* 生产 API: https://www.bitgo.com/api/v1

### 测试环境 Test Environment

BitGo测试环境被我们的范例和SDK默认使用. 该环境完全独立于BitGo生产环境，数据的账号没有重叠。 你需要前往 <a href="https://test.bitgo.com/" target="_new">test.bitgo.com</a> 创建账号。

* BitGo 测试站点: https://test.bitgo.com/
* 测试环境 API: https://test.bitgo.com/api/v1

在测试环境中，你可以在与BitGo进行验证时使用 `0000000` 作为一次性密码（OTP） (用于自动化测试用途).

该环境与Bitcoin TestNet相连，你可以使用<a href="http://tbtc.blockr.io/" target="_new">Blockr</a> 进行导航。 获取测试币请使用 <a href="http://tpfaucet.appspot.com/" target="_new">faucet</a> 或与我们联系。

## BitGo Express REST API

```shell
./bitgo-express --debug --port 3080 --env test --bind localhost
```

```shell
BITGO_EXPRESS_HOST='localhost'
curl http://$BITGO_EXPRESS_HOST:3080/api/v1/ping
```

BitGo Express REST API 是一个轻量级服务，供想要使用 BitGo 所带来的益处，但使用的开发语言没有原生BitGo SDK的开发者。

BitGo Express 在你自己的数据中心中作为服务运行，用于处理客户端包含你自己的密钥的操作，比如将交易提交到BitGo之前进行部分签名。 这样可以确保你的密钥不会离开你的网络，对BitGo也不可见。 BitGo Express 也可代理标准的 BitGo REST API，通过单一REST API提供到BitGo的统一接口。

要使用 BitGo Express:

* 安装 [BitGoJS](#software-development-kit)
* 在bin目录下执行下列命令:

`./bitgo-express --debug --port 3080 --env test --bind localhost`

* 使 **全部** BitGo REST API 请求都指向运行 bitgo-express 的机器。

## 错误处理 Error Handling

> JSON错误示例

```json
{
  "status":  "400 Bad Request",
  "error": "missing user name"
}
```

所有错误均遵循通用REST原则。错误响应的主体（如非200的状态码）将包含如下格式的错误对象：

| 参数     | 值           |
| ------ | ----------- |
| status | 返回的HTTP错误状态 |
| error  | 详细错误信息      |

# 用户验证 User Authentication

BitGo通过“Authorization”头进行验证，调用者可以指定访问令牌。

访问令牌被用于维护会话，通过密码登录（需要一次性密码）或Oauth登录路径创建。 典型的访问令牌有效期为1个小时并且需要一次性密码进行解锁才可以花费资金。

默认情况下，令牌被绑定到单一IP地址有效期60分钟，过期后用户必须重新验证。

对特定的API请求，有效的会话令牌也不行。 要访问这些API请求，会话必须使用解锁API进行解锁，使用附加的两部验证代码。 每个解锁请求允许用户进行一次任意大小的交易（仍然受钱包策略约束），或者任意数量的不超过BiGo管理的限额的交易。

<aside class="info">
若会话当前为锁定或当前已解锁的会话交易余额不足，需要解锁的API在响应中会包含needsUnlock=true。
</aside>

另外，通过API用途创建的访问令牌可以在达到限额前一直保持解锁，但是必须在创建时被绑定到指定的作用范围。

## API 访问令牌 API Access Tokens

```shell
ACCESS_TOKEN='DeveloperAccessToken'
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{}' \
-H "Content-Type: application/json" \
https://test.bitgo.com/api/v1/user/session
```

```javascript
var bitgo = new BitGoJS.BitGo({accessToken:'DeveloperAccessToken'});
bitgo.session({}, function callback(err, session) {
  if (err) {
    // 错误处理
  }
  console.dir(session);
});
```

出于自动化用途，开发者可以请求长期（long-lived）访问令牌，这种令牌不会在一小时后过期，在指定金额内一直有效。

  1. 访问BitGo仪表盘并转到“设置”页面。
  2. 点击“开发者”标签。
  3. 在此可以创建长期访问令牌。

令牌默认在达到你设定的金额钱是已解锁状态。不要通过API解锁该令牌，否则该令牌将被重置。

### 令牌参数 Token Parameters

| 参数             | 说明                                                |
| -------------- | ------------------------------------------------- |
| Label          | 标签用于识别令牌，使你在以后可以废除它。                              |
| Duration       | 令牌的有效时间（秒）。                                       |
| Spending Limit | 该令牌将在到达指定的BTC支出限额前保持解锁状态。不要尝试通过API解锁此令牌，否则限额将被重置。 |
| IP Addresses   | 限制BitGo仅接受来自指定IP地址对于该令牌的使用。                       |
| Permissions    | 创建令牌的验证范围。                                        |

## 当前用户配置文件 Current User Profile

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/user/me
```

```javascript
bitgo.me({}, function callback(err, user) {
  if (err) {
    // 错误处理
  }
  // 其他
});
```

获取当前已验证用户的信息。

### HTTP 请求 HTTP Request

`GET /api/v1/user/me`

> 用户模型响应示例

```json
{
  "user": {
    "id": "53532a4b43fd69a42f000005f0a2ed87fd8b020040739beb513524b5",
    "isActive": true,
    "name": {
      "first": "Jane",
        "full": "Jane Doe",
        "last": "Doe"
    },
    "username": "janedoe@bitgo.com"
  }
}
```

### 响应 Response

返回一个当前已验证用户的用户模型对象。

## 登录 Login

获取供第三方访问BitGo API的令牌。 第一方访问仅适用于用户访问自己的BitGo账号。 要让另一个用户以第三方的身份访问BitGo API，请参见 **合作伙伴验证**。

```shell
EMAIL="janedoe@bitgo.com"
PASSWORD="mypassword"
HMAC=`echo -n "$PASSWORD" | openssl dgst -sha256 -hmac "$EMAIL" | sed 's/^.* //' `

curl -X POST \
-H "Content-Type: application/json" \
-d "{\"email\": \"$EMAIL\", \"password\": \"$HMAC\", \"otp\": \"0000000\"}" \
https://test.bitgo.com/api/v1/user/login

注: 在余下的 shell 示例中共享变量，假定 shell 变量 ACCESS_TOKEN 包含此访问令牌。
```

```javascript
var useTestnet = false;
var bitgo = new Bitgo(useTestnet);
bitgo.authenticate({ username: user, password: password, otp: otp }, function callback(err, response) {
  if (err) {
    // 错误处理
  }
  var token = response.token;
  var user = response.user;
  // 其他
});
```

### HTTP 请求 HTTP Request

`POST /api/v1/user/login`

### BODY 参数 BODY Parameters BODY Parameters

| 参数         | 类型      | 必需  | 说明                     |
| ---------- | ------- | --- | ---------------------- |
| email      | string  | YES | 用户的电邮地址                |
| password   | string  | YES | 用户的密码                  |
| otp        | string  | YES | 两步验证令牌（Authy令牌）        |
| extensible | boolean | NO  | 若此会话可被延长为大于1小时，则为True。 |

> 响应示例

```json
{
    "access_token": "3fe0bf671c943af5ee3b739d2b17ffced7fdde62",
    "expires_in": 3600,
    "token_type": "bearer",
    "user": {
        "id": "53532a4b43fd69a42f000005f0a2ed87fd8b020040739beb513524b5",
        "isActive": true,
        "name": {
            "first": "Jane",
            "full": "Jane Doe",
            "last": "Doe"
        },
        "username": "janedoe@bitgo.com"
    }
}
```

### 响应 Response

通过API返回可供使用的令牌。

该令牌必须作为 HTTP 头添加到所有 API 请求的HTTP "Authorization" header：

`Authorization: Bearer <你的令牌>`

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |

## 登出 Logout

从 BitGo 服务登出。

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/user/logout
```

```javascript
bitgo.logout({}, function callback(err) {
  if (err) {
    // 错误处理
  }
  // 该用户现已登出
});
```

### HTTP 请求 HTTP Request

`GET /api/v1/user/logout`

### BODY 参数 BODY Parameters

无

### 响应 Response

无

## 会话信息 Session Information

获取当前会话访问令牌的信息

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{}' \
-H "Content-Type: application/json" \
https://test.bitgo.com/api/v1/user/session
```

```javascript
bitgo.session({}, function callback(err, session) {
  if (err) {
    // 错误处理
  }
  console.dir(session);
});
```

> 响应示例

    { "client": "bitgo",
      "user": "5458141599f715232500000530a94fd2",
      "scope":
       [ "user_manage",
         "openid",
         "profile",
         "wallet_create",
         "wallet_manage_all",
         "wallet_approve_all",
         "wallet_spend_all",
         "wallet_edit_all",
         "wallet_view_all" ],
      "expires": "2015-01-21T02:18:11.534Z",
      "origin": "test.bitgo.com",
      "unlock":
          { "time": "2015-01-21T01:20:25.366Z",
            "expires": "2015-01-21T01:30:25.366Z",
            "txCount": 0,
            "txValue": 0
          }
    }
    

### HTTP 请求 HTTP Request

`GET /api/v1/user/session`

### 响应 Response

| 字段      | 说明                         |
| ------- | -------------------------- |
| client  | 获取用户令牌的 OAuth 客户端ID        |
| user    | BitGo 用户 ID                |
| expires | 登录的会话在此时间戳之前有效。            |
| scope   | 列出这个会话令牌所允许的权限             |
| origin  | 若会话是在浏览器中发起的，此处为创建令牌的源主机名  |
| unlock  | 当会话已解锁时可用。显示此解锁的交易数量和过期时间。 |

## 发送一次性密码 Send OTP

发送一次性密码（两步验证）令牌给用户，可被用于登录 / 解锁。

```shell
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{}' \
-H "Content-Type: application/json" \
https://test.bitgo.com/api/v1/user/sendotp
```

```javascript
bitgo.sendOTP({forceSMS: true}, function callback(err) {
  if (err) {
    // 错误处理
  }
  // 其他
});
```

### HTTP 请求 HTTP Request

`POST /api/v1/user/sendotp`

### BODY 参数 BODY Parameters

| 名称       | 类型      | 必需 | 说明                             |
| -------- | ------- | -- | ------------------------------ |
| forceSMS | boolean | NO | 使用SMS将OTP发送给用户，即使用户已经设置过Authy。 |

### 响应 Response

无

## 解锁 Unlock

解锁当前会话，适用于特定的敏感API调用。

```shell
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{"otp": "0000000"}' \
-H "Content-Type: application/json" \
https://test.bitgo.com/api/v1/user/unlock
```

```javascript
bitgo.unlock({otp: otp}, function callback(err) {
  if (err) {
    // 错误处理
  }
  // 其他
});
```

### HTTP 请求 HTTP Request

`POST /api/v1/user/unlock`

### BODY 参数 BODY Parameters

| 参数       | 类型     | 必需  | 说明                          |
| -------- | ------ | --- | --------------------------- |
| otp      | string | YES | 用于此帐户的Authy OTP 密码。         |
| duration | number | NO  | 请求解锁的持续时间秒数（默认=600，最大=3600） |

### 响应 Response

无

### 错误 Errors

| 响应               | 说明             |
| ---------------- | -------------- |
| 400 Bad Request  | 请求参数缺失或有错误。    |
| 401 Unauthorized | 验证参数不符，或OTP错误。 |

## 锁定 Lock

重新锁定当前会话

```shell
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/user/lock \
```

```javascript
bitgo.lock({}, function callback(err) {
  // ...
});
```

### HTTP 请求 HTTP Request

`POST /api/v1/user/lock`

### 响应 Response

无

### 错误 Errors

| 响应               | 说明     |
| ---------------- | ------ |
| 401 Unauthorized | 验证参数不符 |

## 合作伙伴验证 Partner Authentication

第三方使用 BitGo API 的应用程序使用 OAuth 通过 BitGo 进行验证。 请联络 BitGo 以获取合作伙伴 ID 和进一步信息。

# 钥匙串 Keychains

所有 BitGo 钱包均使用钥匙串创建。 钥匙串是一个标准的
<a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki" target="_new">BIP32</a>
扩展HD密钥。 和传统比特币密钥不同，传统密钥代表单个
<a href="http://en.wikipedia.org/wiki/Elliptic_Curve_DSA" target="_new">ECDSA</a> 密钥对， 一个钥匙串可代表多个密钥对，全部源于一个公用私钥。 这让用户可以在保留单个私钥的同时，生成无限数量的公钥。 BitGo 使用扩展密钥来保证你的比特币拥有更多隐私和安全。

为了使创建钱包变得简单，BitGo 为每个用户维护钥匙串列表。每个钥匙串可被用在任意数量的 BitGo 钱包中。

有两种类型的钥匙串：

* 公有钥匙串
    
    由单个 BIP32 扩展公钥（xpub）组成。

* 私有钥匙串
    
    由单个 BIP32 扩展私钥（xprv）组成，始终以加密形式储存。

所有钥匙串均有各自的 xpub 进行区别。为方便起见，每个钥匙串均可设置标签。

在创建你的第一个钱包前，你必须创建供钱包使用的钥匙串。

<aside class="success">
要注意的是私有钥匙串（即使已是加密格式）始终需要两步验证。
</aside>

## 列出钥匙串 List Keychains

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/keychain
```

```javascript
var keychains = bitgo.keychains();
keychains.list({}, function callback(err, keychains) {
  if (err) {
    // 错误处理
  }
  console.dir(keychains);
});
```

获取此用户的公有钥匙串

<aside class="success">
本 API 仅返回公钥，不包括钥匙串的私有数据。
</aside>

### HTTP 请求 HTTP Request

`GET /api/v1/keychain`

> 钥匙串模型响应示例

```json
{
    "keychains": [
        {
            "xpub": "xpub661MyMwAqRbcEnCqci8PwkTJgBPbfU54z5rS44bBFtm23hudhYaf73e5Jt8e3JhYYizyFpFcXb4Ahd7wBHmoDFqf2GTGZFaHSC1JyJoURpw",
            "ethAddress": "0xcc59479226f57543ae99ca47630275d9c899bed0"
        },
        {
            "xpub": "xpub661MyMwAqRbcFHjomRhjJybA7rh75j4Cn9Hz4EPG9FUumurJFVbWVgqeDCkCJtnoB5zBHvqsSoi4ArVUKQszzfzuAk9L3LwLZKKQ4RQe8k3",
            "ethAddress": "0x0ddbe738936e911f0db97bdab76cf731f813b378"
        },
        {
            "xpub": "xpub69gHkWcECsrNhcpkPBvvpHq8uX4AwYWcciVZAkvdaKNEj2Cr2mAkUzyQ2X4f9W8fBTt6jp6rvAwUDym4GypkApJZUgKt81vpjyYVs4RDvE8",
            "ethAddress": "0x848ce3d81e017ace770dbc69cf0c752e267a1509",
            "isBitGo": true
        }
    ],
    "start": 0,
    "count": 3,
    "total": 3
}
```

### QUERY 参数 QUERY Parameters

| 参数    | 类型     | 必需 | 说明                           |
| ----- | ------ | -- | ---------------------------- |
| skip  | number | NO | 列表的初始索引号。默认值为 0。             |
| limit | number | NO | 单次调用返回结果的最大数量（默认=100，最大=500） |

### 响应 Response

返回一个包含钥匙串模型对象的数组。

### 错误 Errors

| 响应               | Required    |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |

## 创建钥匙串 Create Keychain

```shell
仅作为本地方法时可用 (BitGo Express)

curl -X POST http://$BITGO_EXPRESS_HOST:3080/api/v1/keychain/local
```

```javascript
var keychains = bitgo.keychains();
var keychain = keychains.create();

console.dir(keychain);
```

> 响应示例

```json
  {
    "xpub": "xpub661MyMwAqRbcFdEUWdhDhgKKQ9tQzLSuqZzVM2AZf9TiijPMD84tPYmemZcQosg63SZit3jGQpCDsbbeXPv7A3aT1phPaBgAWQWKwFUioPR",
    "xprv": "xprv9s21ZrQH143K39A1QcADLYNar83vasj4UM4tYdkx6ovjqw4CfakdqkTAvFrY8BoR8TFTCYShYJ3XjZCicvyuavmYjPLVbmASmHrz144nVCy",
    "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615"
  }
```

本地客户端功能用以创建新的钥匙串。

根据需要，在使用确定性种子创建你的钥匙串时，可加上参数 'seed'。 种子必须 为一个包含不少于32个元素的数字数组。 使用同一个种子调用此函数将会生成相同的 BIP32 钥匙串。

<aside class="warning">
创建你的钥匙串是一个保障你比特币安全的关键步骤。 当生成性钥匙串时，API 使用遵循工业标准的随机数生成器。 若你使用自己的种子，你必须在创建种子时极度小心。
</aside>

返回一个包含 xprv 和 xpub 的对象，以供新钥匙串使用。创建的钥匙串对 BitGo 服务而言是不可知的。 要在 BitGo 服务中使用，请使用 Keychains.Add API.

处于安全原因，强烈建议你 [加密](#encrypt) 后立刻销毁原始 xprv 以防被盗。

## 添加钥匙串 Add Keychain

```shell
curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{"xpub": "xpub661MyMwAqRbcGn6m3YB7CJ2ToyUJYEsBpCc2UDJP9s3hzFif9dKucLotrJBbLgNqojM4q4Sddweka1WG2NvMccYyo3SpnfRrTvMuXUTpHwC"}' \
https://test.bitgo.com/api/v1/keychain
```

```javascript
var data = {
  "xpub": "xpub.....",
  "encryptedXprv": "<encrypted xprv goes here>" // optional, provide it for the user key
};

bitgo.keychains().add(data, function callback(err, keychain) {
  if (err) {
    // 错误处理
  }
  // 其他
});
```

为用户注册新的钥匙串。你必须至少提供公钥。加密的私钥亦可上传，但对服务器而言是不透明的。

提供加密私钥和地址的目的是使用户在使用 BitGo 时可以安全的访问自己的密钥，不再有自己保存密钥的负担。 强烈建议你用强密码加密存储在服务器上的用户私钥。 加密操作必须在客户端完成。 为方便起见，你可以使用 BitGo 的 [加密/解密函数](#encrypt)，不过你也可以使用任何你想要的加密方式。<aside class="warning"> 若你自己提供加密的 xprv，那么钥匙串的安全强度将由你的加密方式决定。 加密由你全权负责。 </aside> 

### HTTP 请求 HTTP Request

`POST /api/v1/keychain`

### BODY 参数 BODY Parameters

| 参数            | 类型     | 必需  | 说明                   |
| ------------- | ------ | --- | -------------------- |
| xpub          | string | YES | 此钥匙串的 BIP32 xpub     |
| encryptedXprv | string | NO  | 此钥匙串的 BIP32 xprv，已加密 |

> 钥匙串模型响应示例

```json
{
  "xpub": "xpub661MyMwAqRbcGn6m3YB7CJ2ToyUJYEsBpCc2UDJP9s3hzFif9dKucLotrJBbLgNqojM4q4Sddweka1WG2NvMccYyo3SpnfRrTvMuXUTpHwC",
  "encryptedXprv": "<encrypted data here>",
  "path": "m",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615"
}
```

### 响应 Response

返回一个钥匙串模型对象。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |

## 创建 BitGo 钥匙串 Create BitGo Keychain

```shell
curl -X POST \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/keychain/bitgo
```

```javascript
bitgo.keychains().createBitGo({}, function callback(err, keychain) {
  if (err) {
    // 错误处理
  }
  console.dir(keychain);
});
```

在 BitGo 服务器上创建新钥匙串，并返回公有钥匙串给调用者。

### HTTP 请求 HTTP Request

`POST /api/v1/keychain/bitgo`

### BODY 参数 BODY Parameters

无

> 钥匙串模型响应示例

```json
{
  "xpub": "xpub661MyMwAqRbcGzVAChrAGb6MYDrAXndUC7h8T7AF8UhfbjS7Au7UKTXmVXaFasQPdfmnUjccreRTMrW7kTmjzwMqVrTHNAFs8M3CXTJpcnL",
  "isBitGo": true,
  "path": "m",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615"
}
```

### 响应 Response

返回一个钥匙串模型对象。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |

## 创建备份钥匙串 Create Backup Keychain

```shell
curl -X POST \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-H "Content-Type: application/json" \
-d "{ \"provider\": \"lastkeysolutions\" }" \
https://test.bitgo.com/api/v1/keychain/backup
```

```javascript
bitgo.keychains().createBackup({ provider: 'lastkeysolutions' }, function callback(err, keychain) {
  console.dir(keychain);
});
```

使用第三方专业密钥恢复服务创建新的钥匙串备份。 该钥匙串将被保存在第三方服务中，仅可用于恢复用途。

### HTTP 请求 HTTP Request

`POST /api/v1/keychain/backup`

### BODY 参数 BODY Parameters

| 参数     | 类型    | 必需 | 说明                 |
| -------- | ------ | --- | ------------------- |
| provider | string | YES | 使用的 KRS 或密钥备份提供者的名称 |

> 钥匙串模型响应示例

```json
{
  "xpub": "xpub661MyMwAqRbcGzVAChrAGb6MYDrAXndUC7h8T7AF8UhfbjS7Au7UKTXmVXaFasQPdfmnUjccreRTMrW7kTmjzwMqVrTHNAFs8M3CXTJpcnL",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615",
  "path": "m"
}
```

### 响应 Response

返回一个钥匙串模型对象。

## 获取钥匙串 Get Keychain

```shell
XPUB=xpub661MyMwAqRbcGn6m3YB7CJ2ToyUJYEsBpCc2UDJP9s3hzFif9dKucLotrJBbLgNqojM4q4Sddweka1WG2NvMccYyo3SpnfRrTvMuXUTpHwC

curl -X POST \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/keychain/$XPUB
```

```javascript
bitgo.keychains().get({xpub: xpub}, function callback(err, keychain) {
  console.dir(keychain);
});
```

根据 xpub 查找钥匙串

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

### HTTP 请求 HTTP Request

`POST /api/v1/keychain/:xpub`

### URL 参数 URL Parameters

| 参数   | 类型     | 需要  | 说明               |
| ---- | ------ | --- | ---------------- |
| xpub | string | YES | 需要查找的 BIP32 xpub |

> 钥匙串模型响应示例

```json
{
  "xpub": "xpub661MyMwAqRbcGn6m3YB7CJ2ToyUJYEsBpCc2UDJP9s3hzFif9dKucLotrJBbLgNqojM4q4Sddweka1WG2NvMccYyo3SpnfRrTvMuXUTpHwC",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615",
  "encryptedXprv": "<client-encrypted data here>",
  "path": "m"
}
```

### 响应 Response

返回一个钥匙串模型对象。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |
| 404 Not found    | 未找到该 xpub     |

## 更新钥匙串 Update Keychain

```shell
XPUB=xpub661MyMwAqRbcGn6m3YB7CJ2ToyUJYEsBpCc2UDJP9s3hzFif9dKucLotrJBbLgNqojM4q4Sddweka1WG2NvMccYyo3SpnfRrTvMuXUTpHwC

curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{"encryptedXprv": "my encrypted data"}' \
https://test.bitgo.com/api/v1/keychain/$XPUB
```

```javascript
var params = {
  xpub: xpub,
  encryptedXprv: "<encrypted xprv goes here>"
};
bitgo.keychains().update(params, function callback(err, keychain) {
  // handle error, do something with keychain
  console.dir(keychain);
});
```

更新钥匙串。用于当你需要储存一个新版本的 xprv （例如，你更改了用于加密 xprv 的密码）。

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

<aside class="warning"> 若你更改encryptedXprv，那么现有的值 将被覆盖。 若新的值是错误的，或你忘记了新的值的密码， 那你将永远不能使用此钥匙串进行签名。 </aside> 

### HTTP 请求 HTTP Request

`PUT /api/v1/keychain/:xpub`

### BODY 参数 BODY Parameters

| 参数            | 类型     | 必需 | 说明                          |
| ------------- | ------ | -- | --------------------------- |
| encryptedXprv | string | NO | 用于此钥匙串的，一个新的已加密的 BIP32 xprv |

> 钥匙串模型响应示例

```json
{
  "xpub": "xpub661MyMwAqRbcGzVAChrAGb6MYDrAXndUC7h8T7AF8UhfbjS7Au7UKTXmVXaFasQPdfmnUjccreRTMrW7kTmjzwMqVrTHNAFs8M3CXTJpcnL",
  "encryptedXprv": "<encrypted data here>",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615",
  "path": "m"
}
```

### 响应 Response

返回一个钥匙串模型对象。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |
| 404 Not Found    | 未找到该 xpub     |

# 钱包 Wallets

所有 BitGo 钱包均为多重签名的分层确定性钱包。 多重签名钱包由 *N* 个密钥组成，并且需要 *M* 个密钥 对交易进行签名使交易有效。 这称之为 *M-of-N* 钱包。

BitGo 目前仅支持 2-of-3 钱包。我们使用策略层支持 m-of-n 权限模型。

要创建钱包，必需提供 3 个钥匙串。 第 1 和第 2 个钥匙串由用户提供；最后一个必须是 BitGo 的钥匙串。 前两个密钥的公有部分对 BitGo 可见，但 BitGo 对私有部分始终不会具有访问权限，因此没有用户操作是无法进行交易的。 单靠 BitGo 的密钥是无法对交易进行签名的，BitGo 仅依照用户设定的策略使用此密钥。

## 列出钱包 List Wallets

```shell
curl -X GET \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet
```

```javascript
var wallets = bitgo.wallets();
wallets.list({}, function callback(err, wallets) {
// 错误处理，钱包操作
for (id in wallets) {
  var wallet = wallets[id].wallet;
  console.log(JSON.stringify(wallet, null, 4));
}
});
```

获取用户的钱包列表

### HTTP 请求 HTTP Request

`GET /api/v1/wallet`

> 响应示例

```json
{
    "wallets": [
        {
            "admin": {},
            "id": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
            "isActive": true,
            "label": "Example wallet",
            "permissions": "admin,spend,view",
            "private": {
                "keychains": [
                    "... 略 ..."
                ]
            },
            "spendingAccount": true,
            "type": "safehd"
        },
        {
            "admin": {},
            "id": "2MyWgQ3PMAWPenJnHJUPpVjFDLHhaPkZCz5",
            "isActive": true,
            "label": "My wallet",
            "permissions": "admin,spend,view",
            "private": {
                "keychains": [
                    "... 略 ..."
                ]
            },
            "spendingAccount": true,
            "type": "safehd"
        }
        ...
    ]
    "start": 0,
    "count": 25,
    "total": 36
}
```

### QUERY 参数 QUERY Parameters

| 参数    | 类型     | 必需 | 说明                          |
| ----- | ------ | -- | --------------------------- |
| skip  | number | NO | 列表的初始索引号。默认值为 0。            |
| limit | number | NO | 单次调用返回结果的最大数量（默认=25，最大=250） |

### 响应 Response

返回一个包含钱包模型对象的数组。

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |

## 添加钱包 Add Wallet

<aside class="warning">
此功能供高级 API 用户使用，允许手动创建用户密钥和规格以及备份密钥的 xPubs。 在大部分 SDK 场景下，推荐使用较为方便的 
<a href="#create-wallet-with-keychains">使用钥匙串创建钱包</a> SDK 方法从钱包发送比特币。 
</aside> 

```shell
XPUB_USER=xpub661MyMwAqRbcF8BFQAaLnkkDar6uHQZn9cvzPX5qdfUL42gyts7YeYHZgWvNVjcUDP8BEDMduMBhtKLnVAKaT3sW1g14xnv29w5D3ts8LVd

XPUB_BACKUP=xpub661MyMwAqRbcF3MqMfvo7swQwRzEVRyCPry4rmp346Dv6zR4CNFzrtu4bMpeNazCNNa9p3p5z56svzRcnF9LEm3Ha2zSgq2cLrperm7z4oh

XPUB_BITGO=xpub661MyMwAqRbcG96fDEgYGmjKwdQvxG38rr9gTcqszjdU1s5kjLkTD8pTDxYf4ECrkVbGdpTktuc2BwUR1YKvTdNu5U8mxnDp68QAnvdy1WE

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"label\": \"My Test Wallet\", \"m\": 2, \"n\": 3, \"keychains\": [{\"xpub\": \"$XPUB_USER\"}, {\"xpub\": \"$XPUB_BACKUP\"}, {\"xpub\": \"$XPUB_BITGO\"}] }" \
https://test.bitgo.com/api/v1/wallet
```

```javascript
var data = {
  "label": "My Wallet",
  "m": 2,
  "n": 3,
  "keychains": [
    { "xpub": "xPub of user keychain (may be created with keychains.create)"},
    { "xpub": "xPub of backup keychain (may be created with keychains.create or keychains.createBackup)"},
    { "xpub": "xPub of BitGo keychain (created with keychains.createBitGo)"}
  ]
};
bitgo.wallets().add(data, function callback(err, wallet) {
  if (err) {
    // 处理错误
  }
  console.dir(wallet);
});
```

本 API 为用户创建新的钱包。用于新钱包的钥匙串 必需事先使用 API 在 BitGo 进行注册。

BitGo 目前仅支持 2-of-3 （例如 m=2 且 n=3）钱包。 第三个钥匙串，并且 **只有**第三个钥匙串，*必须*是 BitGo 密钥。 按照约定第一个钥匙串是用户密钥，和它**加密的** xpriv 存储于 BitGo。

目前 BitGo 钱包是硬编码的他们的根位于 **m/0/0** 用于全部三个钥匙串 (然而，老式钱包可能使用不同的密钥路径)。 在根之下，钱包支持两个地址链，0 和 1。 **0-链** 是外部接收地址， **1-链** 是内部 (变更) 地址。

钱包的第一个接收地址位于路径 **m/0/0/0/0**， 此 ID 也是在 BitGo 系统中用于引用钱包的地址。 钱包的第一个变更地址位于 **m/0/0/1/0**. 

### HTTP 请求 HTTP Request

`POST /api/v1/wallet`

### BODY 参数 BODY Parameters

| 参数                              | 类型      | 必需  | 说明                                               |
| ------------------------------- | ------- | --- | ------------------------------------------------ |
| label                           | string  | YES | 钱包的标签                                            |
| m                               | number  | YES | 赎回所需的签名数量（必需为 2）                                 |
| n                               | number  | YES | 钱包中的密钥数量（必需为 3）                                  |
| keychains                       | array   | YES | 用于此钱包的一个包含 **n** 个 xpub 钥匙串的数组；最后一个必须是 BitGo 密钥。 |
| enterprise                      | string  | NO  | 创建此钱包时使用的企业 ID。                                  |
| disableTransactionNotifications | boolean | NO  | 设为 true 以阻止钱包交易提醒。                               |

> 响应示例

```json
{
  "id":"2MsajnHwkzQvggkb6Zbi7kaLMcpeCko4BKB",
  "label":"My Test Wallet",
  "isActive":true,
  "private":{
    "keychains":[
      {
        "xpub":"xpub661MyMwAqRbcF8BFQAaLnkkDar6uHQZn9cvzPX5qdfUL42gyts7YeYHZgWvNVjcUDP8BEDMduMBhtKLnVAKaT3sW1g14xnv29w5D3ts8LVd",
        "path":"/0/0"
      },
      {
        "xpub":"xpub661MyMwAqRbcF3MqMfvo7swQwRzEVRyCPry4rmp346Dv6zR4CNFzrtu4bMpeNazCNNa9p3p5z56svzRcnF9LEm3Ha2zSgq2cLrperm7z4oh",
        "path":"/0/0"
      },
      {
        "xpub":"xpub661MyMwAqRbcG96fDEgYGmjKwdQvxG38rr9gTcqszjdU1s5kjLkTD8pTDxYf4ECrkVbGdpTktuc2BwUR1YKvTdNu5U8mxnDp68QAnvdy1WE",
        "path":"/0/0"
      }
    ]
  },
  "permissions":"admin,spend,view",
  "admin":{},
  "spendingAccount":true,
  "confirmedBalance":0,
  "balance":0,
  "pendingApprovals":[]
}
```

### 响应 Response

返回一个钱包模型对象。

### 错误 Errors

| 响应                 | 说明              |
| ------------------ | --------------- |
| 400 Bad Request    | 请求参数缺失或有错误。     |
| 401 Unauthorized   | 验证参数不符。         |
| 406 Not acceptable | 提供的钥匙串中有一个不被接受。 |

## 获取钱包 Get Wallet

```shell
WALLET=2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb

curl -X GET \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET
```

```javascript
var wallets = bitgo.wallets();
var data = {
  "type": "bitcoin",
  "id": "2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb",
};
wallets.get(data, function callback(err, wallet) {
  if (err) {
    // 错误处理
  }
  console.dir(wallet);
});

var wallets = bitgo.wallets();
var data = {
  "type": "bitcoin",
  "id": "2My4uGbvFoBvBtLpADVTTai1w6roxQafJki",
};
wallets.get(data, function callback(err, wallet) {
  if (err) {
    // 错误处理
  }
  // 在此使用钱包对象
  console.dir(wallet);
  console.dir(wallet.balance());
});

```

查找钱包信息，返回的钱包模型包括有余额，权限等信息。钱包的 ID 为它的第一个接收地址 (/0/0)

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:id`

> 响应示例

```json
{
  "_id": "57bb66f07cf7c6da4f2956011df25aa6",
  "id": "2N6GYGJQSLZyXSgviEmdhWd5jWHoDcSKAfJ",
  "label": "TestNet Wallet",
  "isActive": true,
  "type": "safehd",
  "freeze": {},
  "adminCount": 1,
  "disableTransactionNotifications": false,
  "private": {
    "keychains": [
      {
        "xpub": "xpub661MyMwAqRbcGDNuXKbT5Uyeth3yNYQAJY4KksbDSLdHgXdh2q5bjH6aQzSRQ4nPjanfWrkE8LVa1Svet4Nh5EnvA7Rh52tfbC4RjFHAwMZ",
        "path": "/0/0",
        "params": {
          "pubKey": "02b910dc08af71a28648e4e1efd3bb851587aad7b87fd0ba73ffc50467d0b85b75",
          "chainCode": "a7e22c7bb28727f49ccf1c04b4371a8d79cf286ee6a018fb8ea4bc454023e498",
          "depth": 0,
          "index": 0,
          "parentFingerprint": 0
        }
      },
      {
        "xpub": "xpub6GiRC55CRwu5aiyDHeGFdAqUJ5ieMFsEdJ3BrjufgZNarq1FdcB3uGcqYEAxdsiegXypW2RjfBCmcdwJhRbcCNHZFonmasetQdwUNZHbrus",
        "path": "/0/0",
        "params": {
          "pubKey": "03e21d7fc8383ab067771463b55991480d2b133c96233d6182e0fe779118762c3f",
          "chainCode": "d4d37ed900e592dac8f5ac3ea546d4a37ed5fe6854526d482bde2109a6808b5c",
          "depth": 5,
          "index": 176980,
          "parentFingerprint": 2966462100
        }
      },
      {
        "xpub": "xpub661MyMwAqRbcGLsjvczmhk2twZfwfhZx16ai3TXf32QcpMfApjKcaTEnLt4oCz4HTss6CQ8gQfQKSyr8ca4s1Xme8FrsPjsNwEo2XBVdJSQ",
        "path": "/0/0",
        "params": {
          "pubKey": "0256002e463c6cde950d0f8319b8c3d98e85e49ea626fff60760db6ff6b8bade52",
          "chainCode": "b4dddf8762d82896a1c8ee63ae2dcfed45037e73dcb8609bb0baba997fc7e2f1",
          "depth": 0,
          "index": 0,
          "parentFingerprint": 0
        }
      }
    ]
  },
  "canSendInstant": true,
  "permissions": "admin,spend,view",
  "admin": {
    "policy": {
      "id": "57d73dedd1187a4a7b2bdeebedddcd6b",
      "version": 2,
      "date": "2016-09-13T00:08:30.661Z",
      "rules": [
        {
          "id": "my velocity limit",
          "type": "velocityLimit",
          "action": {
            "type": "getApproval"
          },
          "condition": {
            "amount": 10000000,
            "timeWindow": 86400,
            "groupTags": [
              ":tag"
            ],
            "excludeTags": []
          }
        }
      ]
    },
    "users": [
      {
        "user": "5624ade20e86bd04483895223800a967",
        "permissions": "admin,spend,view"
      }
    ]
  },
  "tags": [],
  "approvalsRequired": 1,
  "spendingAccount": true,
  "pendingApprovals": [],
  "balance": 39564772124,
  "instantBalance": 39564772124,
  "spendableConfirmedBalance": 39564772124,
  "confirmedBalance": 39564772124,
  "spendableBalance": 39564772124,
  "sent": 0,
  "received": 39564772124,
  "unconfirmedSends": 0,
  "unconfirmedReceives": 0
}
```

### 响应 Response

返回一个钱包模型对象。

| 字段               | 说明                                          |
| ---------------- | ------------------------------------------- |
| id               | 钱包的 id （亦是第一个接收地址）                          |
| label            | 钱包标签，显示于 UI                                 |
| index            | 链中的地址索引 (0, 1, 2, ...)                      |
| private          | 包含钥匙串的汇总                                    |
| permissions      | 用户在此钱包上的权限                                  |
| admin            | 钱包管理员的策略信息                                  |
| pendingApprovals | 钱包上的未决待批准交易                                 |
| confirmedBalance | 已确认余额                                       |
| balance          | 余额，包括 0 确认的交易。                              |
| canSendInstant   | boolean 表示钱包是否具备资格发送有 BitGo 支持保障的防双倍花费即时交易。 |

### 错误 Errors

| 响应                 | 说明              |
| ------------------ | --------------- |
| 400 Bad Request    | 请求参数缺失或有错误。     |
| 401 Unauthorized   | 验证参数不符。         |
| 404 Not Found      | 该钱包未找到          |
| 406 Not acceptable | 提供的钥匙串中有一个不被接受。 |

## 使用钥匙串创建钱包 Create Wallet With Keychains

```shell
仅作为本地方法可用 (BitGo Express)
高级用户应该考虑创建钱包 API。

WALLETPASSPHRASE='newverylongishsecretivepassword'
LABEL='nicenewpurse'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"passphrase\": \"$WALLETPASSPHRASE\", \"label\": \"$LABEL\", \"backupXpub\": \"$BACKUPXPUB\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallets/simplecreate
```

```javascript
var data = {
  "passphrase": password,
  "label": label,
  "backupXpubProvider": "keyternal"
}

bitgo.wallets().createWalletWithKeychains(data, function(err, result) {
  if (err) { console.dir(err); throw new Error("Could not create wallet!"); }
  console.dir(result.wallet.wallet);
  console.log("User keychain encrypted xPrv: " + result.userKeychain.encryptedXprv);
  console.log("Backup keychain xPub: " + result.backupKeychain.xPub);
});
```

本方法作为一个创建钱包的简易方法，在客户端 SDK 中可用。它的执行过程如下：

  1. 创建用户钥匙串和备份钥匙串
  2. 加密用户钥匙串
  3. 上传加密的用户钥匙串和备份钥匙串到 BitGo
  4. 在服务上创建 BitGo 密钥
  5. 在 BitGo 上使用上述的 3 个公钥创建钱包<aside class="warning"> **非常重要** 的是要让用户打印 / 备份他们的用户和备份密钥。 跳过这步可能会导致资金损失！ </aside> 

### BitGo Instant 钱包 BitGo Instant Wallets

默认情况下，本方法将在本地创建备份钥匙串。 要创建可以使用 BitGo 即时服务的钱包，请使用 **backupXpubProvider** 参数指定 KRS，例如 "keyternal"。

### 方法参数 Method Parameters

| 名称                              | 类型      | 必须  | 说明                                                                               |
| ------------------------------- | ------- | --- | -------------------------------------------------------------------------------- |
| passphrase                      | string  | YES | 钱包的用户密钥在发送至 BitGo 之前使用此密码进行加密。                                                   |
| label                           | string  | YES | 此钱包的标签                                                                           |
| backupXpub                      | string  | NO  | 备份钥匙串的公钥，创建于另一台设备，如此不会出现 2 个私钥存放在同一台机器上的情况。可选的 backupXpubProvider 使你可以将你的密钥远程托管。 |
| backupXpubProvider              | string  | NO  | 在你选择的 KRS 上创建一个备份 xPub，例如 "keyternal"。这将使钱包兼容 BitGo 即时服务。                        |
| enterprise                      | string  | NO  | 创建此钱包时使用的企业 ID。                                                                  |
| disableTransactionNotifications | boolean | NO  | 设为 true 以阻止钱包交易提醒。                                                               |

> 响应示例

```json
{ "id": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
 "label": "testwallet",
 "isActive": true,
 "type": "safehd",
 "private": { "keychains": [{}] },
 "permissions": "admin,spend,view",
 "admin": {},
 "spendingAccount": true,
 "confirmedBalance": 0,
 "balance": 0,
 "pendingApprovals": [] }
```

    User keychain encrypted xPrv: {"iv":"v2aVEG5A8VwnI+ewS..."}
    Backup keychain encrypted xPrv: {"iv":"vNOUQpzUmHNPwKt..."}
    

### 响应 Response

| 响应             | 说明                                 |
| -------------- | ---------------------------------- |
| wallet         | 钱包模型对象                             |
| userKeychain   | 新创建的用户钥匙串，加密的 xprv 被存储于 BitGo - 备份 |
| backupKeychain | 新创建的备份钥匙串 - 备份                     |

### 错误 Errors

| 响应                 | 说明              |
| ------------------ | --------------- |
| 400 Bad Request    | 请求参数缺失或有错误。     |
| 401 Unauthorized   | 验证参数不符。         |
| 406 Not acceptable | 提供的钥匙串中有一个不被接受。 |

# 钱包操作 - 基本 Wallet Operations - Basic

每个钱包均由多个地址组成，每个地址都可被用于接收比特币。 钱包 API 提供了很有用的接口可以和用户的钱包进行交互。

## 创建地址 Create Address

为现有钱包创建新的地址。 BitGo 钱包由 两组独立的地址链组成，指定为 0 和 1。 通常 0-链用于 接收资金，1-链为内部使用 用于在从钱包支取时创建变更。 为每次新的接收交易生成新地址， 被认为是最好的做法， 以便获得最大化的隐私。

```shell
CHAIN=0
WALLETID="3GonoRKFSzcqJCktZPA2NHDd3jJoH4154o"
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLETID/address/$CHAIN
```

```javascript
var id = '2My4uGbvFoBvBtLpADVTTai1w6roxQafJki';
bitgo.wallets().get({ "id": id }, function callback(err, wallet) {
  if (err) {
    throw err;
  }
  wallet.createAddress({ "chain": 0 }, function callback(err, address) {
    console.dir(address);
  });
});
```

### HTTP 请求 HTTP Request

`POST /api/v1/wallet/:walletId/address/:chain`

### URL 参数 URL Parameters

| 参数       | 类型             | 必须  | 说明     |
| -------- | -------------- | --- | ------ |
| walletid | 比特币地址 (string) | YES | 钱包的 ID |
| chain    | number         | YES | 0 或 1  |

> 响应示例

```json
{
  "address": "3GonoRKFSzcqJCktZPA2NHDd3jJoH4154o",
  "chain": 0,
  "index": 42,
  "path": "/0/42",
  "redeemScript": "522102d83bba35a8022c247b645eed6f81ac41b7c1580de550e7e82c75ad63ee9ac2fd2103aeb681df5ac19e449a872b9e9347f1db5a0394d2ec5caf2a9c143f86e232b0d92103d728ad6758d4784effea04d47baafa216cf474866c2d4dc99b1e8e3eb936e73053ae"
}
```

### 响应 Response

返回一个新的比特币地址，该地址与钱包相关联。

| 字段           | 说明                     |
| ------------ | ---------------------- |
| address      | 地址链                    |
| chain        | 链 （0 或 1）              |
| index        | 链中的地址索引 (0, 1, 2, ...) |
| path         | 地址的 BIP32 钱包根相对路径      |
| redeemScript | 地址的 redeemScript       |

### 错误 Errors

| 响应                 | 说明                          |
| ------------------ | --------------------------- |
| 400 Bad Request    | 请求参数缺失或有错误。                 |
| 401 Unauthorized   | 验证参数不符。                     |
| 403 Forbidden      | 该钱包不是多重签名 BIP32 (SafeHD) 钱包 |
| 404 Not Found      | 该钱包未找到                      |
| 406 Not acceptable | 提供的钥匙串中有一个不被接受。             |

## 发送比特币到地址 Send Coins to Address

```javascript
var destinationAddress = '2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD';
var amountSatoshis = 0.1 * 1e8; // send 0.1 bitcoins
var walletPassphrase = 'incorrect horse battery stable' // replace with wallet passphrase

bitgo.wallets().get({id: walletId}, function(err, wallet) {
  if (err) { console.log("Error getting wallet!"); console.dir(err); return process.exit(-1); }
  console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));

  wallet.sendCoins({ address: destinationAddress, amount: amountSatoshis, walletPassphrase: walletPassphrase }, function(err, result) {
    if (err) { console.log("Error sending coins!"); console.dir(err); return process.exit(-1); }

    console.dir(result);
  });
});
```

```shell
仅作为本地方法可用 (BitGo Express)
高级用户应该考虑发送交易 API。

WALLETID='2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa'
DESTINATIONADDRESS='2N9JiEUYgRwKAw6FfnUca54VUeaSYSL9qqG'
AMOUNT=1000000
WALLETPASSPHRASE='password'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"address\": \"$DESTINATIONADDRESS\", \"amount\": $AMOUNT, \"walletPassphrase\": \"$WALLETPASSPHRASE\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/sendcoins
```

最简单的从你的 BitGo 钱包发送比特币到一个比特币地址的方法。

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

本方法将在客户端进行以下操作：

* 通过查询在服务器上存储的指定钱包的钥匙串以获取用户钥匙串（使用加密的私钥）
* 解密用户密钥
* 创建发送至目标地址的交易，变更发送至钱包上新创建的链地址（路径为 /1）
* 使用解密的用户密钥签名交易

这将会发送部分签名的交易到 BitGo 服务器以供处理，我们将：

* 有可能从钱包管理员那里收集额外的批准
* 应用最终签名
* 广播此交易到比特币 P2P 网络

### 参数 Parameters

| 名称                           | 类型      | 必需  | 说明                                                                                                                            |
| ---------------------------- | ------- | --- | ----------------------------------------------------------------------------------------------------------------------------- |
| address                      | string  | YES | 目标比特币地址                                                                                                                       |
| amount                       | number  | YES | 要发送的金额，单位：聪(Satoshi)，例如 0.1 * 1e8 为十分之一个比特币。                                                                                  |
| walletPassphrase             | string  | YES | 钱包的密码，用于在客户端上解密已加密的用户密钥。                                                                                                      |
| fee                          | number  | NO  | 费用（单位：聪），保留空白以供自动生成。除非你知道确实的费用，否则不要指定这个值。                                                                                     |
| message                      | String  | NO  | 用户输入的字符串（此字符串不会到达区块链）                                                                                                         |
| feeTxConfirmTarget           | number  | NO  | 按每千字节计费，目标为设定数量区块的交易确认。默认：2， 最小：2，最大：20.                                                                                      |
| minConfirms                  | number  | NO  | 仅选择有指定数量确认的未花费输入（input）。我们推荐设置为 1 并且 使用 enforceMinConfirmsForChange。                                                          |
| enforceMinConfirms ForChange | boolean | NO  | 默认值为 false。 设为 true 以要求由钱包变更地址发起的未花费币拥有 minConfirms (前文已有说明) 数量的确认。 若设为 false 则 minConfirms 将仅被强制用户在源自非此用户钱包的未花费币上（也就是未变更地址）。 |
| sequenceId                   | String  | NO  | 用户输入的自定义字符串，可用于识别签名前后的交易。                                                                                                     |
| instant                      | boolean | NO  | 设为 true 以要求此交易使用具有 BitGo 防双倍花费保障的即时服务发送（可能会有费用）。                                                                              |
| otp                          | String  | NO  | 7 位数密码用于绕开带有 “getOTP” 动作类型的策略。详见 [钱包策略](#wallet-policy) 。                                                                     |

> 响应示例

```json
{ "tx": "0100000001a366332472cebceabdd541beb582d5dbaaecccdbec639b0a2d2b...",
  "hash": "b3bd8ac76de2340c1159337acdfcaabf08a9470e8157870bd1d84631a0acc67b",
  "fee": 10000,
  "status": "signed",
  "instant": true,
  "instantId": "56651c4c4e82b975616b58647c77fe1dc"
}
```

> 响应示例（由于钱包策略的原因，需要等待批准）

```json
{
  "error": "exceeds a spending limit",
  "pendingApproval": "56050b1368217cde3667fcfe0157556b",
  "triggeredPolicy": "5578defa5e44dbcb20c0caf98b297ad7",
  "status": "pendingApproval"
}
```

### 成功响应 Success Response

| 字段      | 说明                          |
| ------- | --------------------------- |
| tx      | 十六进制格式编码的已签名交易              |
| hash    | 交易 id                       |
| fee     | 作为本次交易的一部分发送给比特币矿工，金额为聪     |
| feeRate | 作为本次交易的一部分发送给比特币矿工，金额为每千字节聪 |

### 策略/失败响应 Policy/Failure Response

| 字段              | 说明                       |
| --------------- | ------------------------ |
| error           | 来自触发本次待批准交易的策略的消息        |
| pendingApproval | 待批准的 id，需要另一名用户进行批准      |
| otp             | 触发的策略是 "getOTP"类型时为 true |
| triggeredPolicy | 触发本次待批准交易的策略 id          |
| status          | 交易状态                     |

## 发送比特币到多个地址 Send Coins to Multiple Addresses

```javascript
var recipients = [];
recipients.push({address: '2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD', amount: 0.1 * 1e8});
recipients.push({address: '2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu', amount: 0.2 * 1e8});

var walletPassphrase = 'incorrect horse battery stable' // 此处替换为钱包密码

bitgo.wallets().get({id: walletId}, function(err, wallet) {
  if (err) { console.log("Error getting wallet!"); console.dir(err); return process.exit(-1); }
  console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));

  wallet.sendMany({ recipients: recipients, walletPassphrase: walletPassphrase }, function(err, result) {
    if (err) { console.log("Error sending coins!"); console.dir(err); return process.exit(-1); }

    console.dir(result);
  });
});
```

```shell
仅作为本地方法可用 (BitGo Express)
高级用户应该考虑发送交易 API。

WALLETID='2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa'
WALLETPASSPHRASE='watashinobitcoin'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"recipients\": [{ \"address\": \"2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD\", \"amount\": 1500000}, { \"address\": \"2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu\", \"amount\": 2500000 }], \"walletPassphrase\": \"$WALLETPASSPHRASE\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/sendmany
```

这个方便的函数，用于在单次交易中发送比特币到多个目标地址。

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

### 参数 Parameters

| 名称                           | 类型      | 必需  | 说明                                                                                            |
| ---------------------------- | ------- | --- | --------------------------------------------------------------------------------------------- |
| recipients                   | string  | YES | 数组包含有接收者对象和发送给每个接收者的金额，例如 [{address: '38BKDNZbPcLogvVbcx2ekJ9E6Vv94DqDqw', amount: 1500}, ..] |
| message                      | string  | NO  | 交易备注                                                                                          |
| fee                          | number  | NO  | 费用（单位：聪），保留空白以供自动生成。除非你知道确实的费用，否则不要指定这个值。                                                     |
| feeTxConfirmTarget           | number  | NO  | 按每千字节计费，目标为设定数量区块的交易确认。默认：2， 最小：2，最大：20.                                                      |
| minConfirms                  | number  | NO  | 仅选择有指定数量确认的未花费输入（input）。我们推荐设置为 1 并且 使用 enforceMinConfirmsForChange。                          |
| enforceMinConfirms ForChange | boolean | NO  | 默认为 false。当创建交易时，minConfirms 将仅被强制用于不是来自这个钱包的未花费币。                                            |
| sequenceId                   | String  | NO  | 用户输入的自定义字符串，可用于识别签名前后的交易。                                                                     |
| otp                          | String  | NO  | 7 位数密码用于绕开带有 “getOTP” 动作类型的策略。详见 [钱包策略](#wallet-policy) 。                                     |

> 响应示例

```json
{ "tx": "0100000001c69d05d3897a25c611324a935d0c688669dc416cb8d8e9ebb36e364fa79547c8000..",
  "hash": "27374a97df2cd8a63640dd7a40fce9a1d8dfeec3cf7a8b2fbf1ef609f4a5370d",
  "fee": 10000 }
```

### 响应 Response

| 字段      | 说明                          |
| ------- | --------------------------- |
| tx      | 十六进制格式编码的已签名交易              |
| hash    | 交易 id                       |
| fee     | 作为本次交易的一部分发送给比特币矿工，金额为聪     |
| feeRate | 作为本次交易的一部分发送给比特币矿工，金额为每千字节聪 |

### 策略/失败响应 Policy/Failure Response

| 字段              | 说明                       |
| --------------- | ------------------------ |
| error           | 来自触发本次待批准交易的策略的消息        |
| pendingApproval | 待批准的 id，需要另一名用户进行批准      |
| otp             | 触发的策略是 "getOTP"类型时为 true |
| triggeredPolicy | 触发本次待批准交易的策略 id          |
| status          | 交易状态                     |

## 列出钱包的交易 List Wallet Transactions

```shell
WALLET=2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/tx
```

```javascript
var walletId = '2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov';
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) { throw err; }
  wallet.transactions({}, function callback(err, transactions) {
    // 处理交易
    console.log(JSON.stringify(transactions, null, 4));
  });
});
```

> 响应示例

```json
{
  "transactions": [
    {
      "blockhash": "00000000000004b6b832552c96f8c88e6da1a99a8411573b702c455ae0f967a1",
      "confirmations": 4842,
      "date": "2015-01-16T01:12:52.000Z",
      "entries": [
        {
          "account": "2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov",
          "value": -10000
        }
      ],
      "fee": 10000,
      "height": 318528,
      "id": "90669b53a2004e0c4efb918f71f60ecbdc63fbdbad53b5c84ef940f31ddef552",
      "inputs": [
        {
          "previousHash": "c9a55f32d4b63d02a617eea58873227e4f011d0dfc836cb2e9cab531c4db0c4a",
          "previousOutputIndex": 1
        }
      ],
      "outputs": [
        {
          "account": "2MupvpwQQ7dqUizKFookiSzkmxxv8HFgnx6",
          "isMine": true,
          "value": 2000000,
          "chain": 0,
          "chainIndex": 15,
          "vout": 0
        },
        {
          "account": "2Mxy4voQQRi81vm8U7EEhKjgLLHu2njS5ia",
          "isMine": true,
          "value": 7990000,
          "chain": 1,
          "chainIndex": 5,
          "vout": 1
        }
      ],
      "pending": false,
      "instant": true,
      "instantId": "56651c4c4e82b975616b58647c788fad",
      "sequenceId": "CustomID1",
      "comment": "test transaction"
    },
    {
      "blockhash": "000000007e190d73d38f1372cb21562405c7ad88fdc3fe6bcba841228dc354c1",
      "confirmations": 16672,
      "date": "2014-11-06T03:22:58.000Z",
      "entries": [
        {
          "account": "monQzkZiHkBSzLJtAJpiDAEF2fQhvyPdeb",
          "value": -36855030260
        },
        {
          "account": "2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov",
          "value": 81000000
        },
        {
          "account": "n1VbwcNawZ2vBTrza3SxbHDPFg9tY1f9eg",
          "value": 36774030260
        }
      ],
      "fee": 0,
      "height": 306698,
      "id": "16c5e8cb01a453382723730afb6ca4621cad84ea759b8727c046b38a9c41193f",
      "inputs": [
        {
          "previousHash": "c9a55f32d4b63d02a617eea58873227e4f011d0dfc836cb2e9cab531c4db0c4a",
          "previousOutputIndex": 1
        }
      ],
      "outputs": [
        {
          "account": "n1VbwcNawZ2vBTrza3SxbHDPFg9tY1f9eg",
          "value": 36774030260,
          "vout": 0
        },
        {
          "account": "2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov",
          "isMine": true,
          "chain": 0,
          "chainIndex": 28,
          "value": 81000000,
          "vout": 1
        }
      ],
      "pending": false,
      "instant": false,
      "sequenceId": "CustomID2",
      "comment": "test transaction"
    }
  ],
  "start": 0,
  "count": 2,
  "total": 2
}
```

获取给定钱包的交易，按照反向区块高度排序（未确认的交易在先）。

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/tx`

### URL 参数 URL Parameters

| 参数       | 类型             | 必需  | 说明     |
| -------- | -------------- | --- | ------ |
| walletId | 比特币地址 (string) | YES | 钱包的 ID |

### QUERY 参数 QUERY Parameters

| 参数      | 类型      | 必需 | 说明                          |
| ------- | ------- | -- | --------------------------- |
| skip    | number  | NO | 列表的初始索引号。默认值为 0。            |
| limit   | number  | NO | 单次调用返回结果的最大数量（默认=25，最大=250） |
| compact | boolean | NO | 略去交易结果中的输入和输出               |

### 响应 Response

返回一个交易对象数组。每个交易的汇总信息 包含有涉及此交易的任何钱包或比特币地址。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |

## 获取钱包的交易 Get Wallet Transaction

```shell
WALLET=2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov
TXID=af867c86000da76df7ddb1054b273ca9e034e8c89d049b5b2795f9f590f67648

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/tx/$TXID
```

```javascript
var walletId = '2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov';
var transactionId = 'af867c86000da76df7ddb1054b273ca9e034e8c89d049b5b2795f9f590f67648';

bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) { throw err; }
  wallet.getTransaction({ "id": transactionId }, function callback(err, transaction) {
    console.log(JSON.stringify(transaction, null, 4));
  });
});
```

> 响应示例

```json
{
    "blockhash": "000000009249e7d725cc087cb781ade1dbfaf2bd777822948d5fccd4044f8299",
    "confirmations": 16661,
    "date": "2014-11-06T02:22:55.000Z",
    "entries": [
        {
            "account": "2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov",
            "value": 84000000
        },
        {
            "account": "msj42CCGruhRsFrGATiUuh25dtxYtnpbTx",
            "value": -85900000
        },
        {
            "account": "mwahoJcaVuy2TiMtGDZV9PaujFeD9z1a1q",
            "value": 1890000
        }
    ],
    "fee": 10000,
    "height": 306695,
    "hex": "0100000001b6e8b36132d351b3d66b5452d8f4601e2271a7bb52b644397db956a4ffe2a053000000006a4730440220127c4adc1cf985cd884c383e69440ce4d48a0c4fdce6bf9d70faa0ee8092acb80220632cb6c99ded7f261814e602fc8fa8e7fe8cb6a95d45c497846b8624f7d19b3c012103df001c8b58ac42b6cbfc2223b8efaa7e9a1911e529bd2c8b7f90140079034e75ffffffff0200bd01050000000017a914c449a7fafb3b13b2952e064f2c3c58e851bb943087d0d61c00000000001976a914b0379374df5eab8be9a21ee96711712bdb781a9588ac00000000",
    "id": "af867c86000da76df7ddb1054b273ca9e034e8c89d049b5b2795f9f590f67648",
    "outputs": [
        {
            "account": "2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov",
            "isMine": true,
            "chain": 0,
            "chainIndex": 28,
            "value": 84000000,
            "vout": 0
        },
        {
            "account": "mwahoJcaVuy2TiMtGDZV9PaujFeD9z1a1q",
            "value": 1890000,
            "vout": 1
        }
    ],
    "pending": false,
    "instant": true,
    "instantId": "56651c4c4e82b975616b58661bad3ac",
    "sequenceId": "Custom1a",
    "comment": "test transaction"
}
```

获取钱包中某笔交易信息

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/tx/:txId`

### URL 参数 URL Parameters

| 参数       | 类型              | 必需  | 说明          |
| -------- | --------------- | --- | ----------- |
| walletId | 比特币地址 (string)  | YES | 钱包的 ID      |
| txId     | 交易的散列值 (string) | YES | 要获取的交易 hash |

### 响应 Response

返回一个交易对象

| 参数            | 类型       | 说明                                                  |
| ------------- | -------- | --------------------------------------------------- |
| id            | String   | 交易的散列值                                              |
| hex           | String   | 交易的原始十六进制值                                          |
| date          | DateTime | 首次见到此交易的日期                                          |
| blockhash     | String   | 若此交易已被确认，则此处为区块的散列值                                 |
| height        | Number   | 曾出现过此交易的区块高度                                        |
| confirmations | Number   | 交易中曾经属于区块链的区块数量                                     |
| entries       | Array    | 此交易的合并记录，包含账号的净输入/输出                                |
| outputs       | Array    | 此交易的输出信息，包括钱包账号，值，vout 索引，isMine，链（0 为普通地址，1 为变更地址） |
| fee           | Number   | 单位为聪的金额支付给矿工的本次交易                                   |
| pending       | Boolean  | 交易尚未在区块链上被确认，则此值为 true                              |
| instant       | Boolean  | 若交易由 BitGo 即时服务发送，则为 true                           |
| instantId     | String   | 即时服务交易的识别 Id，用以使用/获取 BitGo 的保障                      |
| sequenceId    | String   | sequenceId (唯一的自定义数据当发送交易时提供)                       |
| comment       | String   | 交易的注释                                               |

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |
| 404 Not Found    | 钱包里未找到此交易。    |

## 列出钱包的地址 List Wallet Addresses

使用新地址 API 获取钱包的地址列表，这些地址已实例化。

```shell
WALLET=2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/addresses
```

```javascript
var id = '2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb';
bitgo.wallets().get({ "id": id }, function(err, wallet) {
  if (err) { console.log(err); process.exit(-1); }
  wallet.addresses({}, function(err, walletAddress) {
    console.dir(walletAddress);
  });
});
```

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/addresses`

### URL 参数 URL Parameters

| 参数       | 类型             | 必需  | 说明     |
| -------- | -------------- | --- | ------ |
| walletId | 比特币地址 (string) | YES | 钱包的 ID |

### QUERY 参数 QUERY Parameters

| 参数    | 类型     | 必需 | 说明                      |
| ----- | ------ | -- | ----------------------- |
| chain | number | NO | 可选择仅包括 0 链或 1 链         |
| skip  | number | NO | 跳过给定数量的结果               |
| limit | number | NO | 限制返回结果的数量（默认=25，最大=500） |

> 响应示例

```json
{
  "addresses":[
    {
      "chain":0,
      "index":0,
      "path":"/0/0",
      "address":"2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb"
    },
    {
      "chain":0,
      "index":1,
      "path":"/0/1",
      "address":"2NCT7eymRYvAoYALNkcBPuftr5scSpBKLuY"
    },
    {
      "chain":0,
      "index":2,
      "path":"/0/2",
      "address":"2NCngTQw49WEHQD7pvDEAc1LzVVPQcoxEU7"
    }
  ],
  "start":0,
  "count":3,
  "total":28,
  "hasMore":true
}
```

### 响应 Response

返回一个包含钱包地址对象的数组。

| 字段      | 说明                 |
| ------- | ------------------ |
| chain   | 链锁在的地址 (0 或 1, 当前) |
| index   | 链上的 BIP32 索引       |
| path    | 钱包的 BIP32 路径       |
| address | 比特币地址              |

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |

## 获取单个钱包地址 Get Single Wallet Address

获取钱包中一个地址的信息。亦可被用于检查钱包中是否存在某个地址。

```shell
WALLET=2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa
ADDRESS=2NBMGw7K9XiBPfvW3nUQcrANKncmAoLUdDX

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/addresses/$ADDRESS
```

```javascript
var id = '2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa';
var address = '2NBMGw7K9XiBPfvW3nUQcrANKncmAoLUdDX';
bitgo.wallets().get({ "id": id }, function(err, wallet) {
  if (err) { console.log(err); process.exit(-1); }
  wallet.address({ address: address }, function(err, walletAddress) {
    console.dir(walletAddress);
  });
});
```

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/addresses/:address`

### URL 参数 URL Parameters

| 参数       | 类型             | 必需  | 说明          |
| -------- | -------------- | --- | ----------- |
| walletId | 比特币地址 (string) | YES | 钱包的 ID      |
| address  | 比特币地址 (string) | YES | 要获取信息的钱包的地址 |

> 响应示例

```json
{
    "address": "2NBMGw7K9XiBPfvW3nUQcrANKncmAoLUdDX",
    "balance": 0,
    "chain": 1,
    "index": 3,
    "path": "/1/3",
    "received": 879990000,
    "redeemScript": "522102d22299c1bc9fb7b2788ba48241bdd51a83740b4eb10c0e95b28f6fa1121877482102f91a6957b2a230e958396152ae70076b7c031f5aa446d87bb071da5eac158a1d2103025d939cf6b546e3e44c92097c534366cc64747c3a227664a7ff70e2ebeab32653ae",
    "sent": 879990000,
    "txCount": 2
}
```

### 响应 Response

返回一个钱包地址对象

| 字段           | 说明                                |
| ------------ | --------------------------------- |
| address      | 要查找的比特币地址                         |
| balance      | 此地址上当前的余额（聪）                      |
| chain        | 用于生成此地址的 HD 链（0 为用户生成的，1 为变更的）    |
| index        | HD 链中用于生成此地址的索引                   |
| path         | 钱包上地址的 HD 路径                      |
| received     | 此地址上已接收的总额（聪）                     |
| sent         | 此地址上已发送的总额（聪）                     |
| txCount      | 此地址上的交易总笔数                        |
| redeemScript | 此 redeemScript 可被用于花费此 P2SH 地址的资金 |

# 钱包操作 - 高级 Wallet Operations - Advanced

这些功能建议高级用户使用。 使用这些 API 可获得扩充的（也可能更复杂）功能性以及更好的控制交易创建流程。

## 通过流水号获取交易 Get Transaction By Sequence Id

```shell
WALLET=2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa
SEQUENCEID=hello123

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/tx/sequence/$SEQUENCEID
```

```javascript
var walletId = '2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa';
var sequenceId = 'hello123';

bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) { throw err; }
  wallet.getWalletTransactionBySequenceId({ "sequenceId": sequenceId }, function callback(err, transaction) {
    console.log(JSON.stringify(transaction, null, 4));
  });
});
```

> 响应示例

```json
{
    "transaction": {
        "amount": -106215,
        "createdDate": "2015-09-25T08:39:38.897Z",
        "creator": "5458141599f715232500000530a94fd2",
        "date": "2015-09-25T08:39:39.893Z",
        "fee": 6215,
        "history": [
            {
                "action": "unconfirmed",
                "date": "2015-09-25T08:39:39.893Z"
            },
            {
                "action": "signed",
                "date": "2015-09-25T08:39:38.897Z",
                "user": "5458141599f715232500000530a94fd2"
            },
            {
                "action": "created",
                "date": "2015-09-25T08:39:38.897Z",
                "user": "5458141599f715232500000530a94fd2"
            }
        ],
        "id": "5605084a68217cde3667f2ad4a640875",
        "sequenceId": "hello123",
        "signedDate": "2015-09-25T08:39:38.897Z",
        "size": 367,
        "state": "unconfirmed",
        "transactionId": "50430eeffdd1272ff39d0d3667cbc8e60de0a8ea6bb118e6236e0964389e6d19",
        "walletId": "2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa"
    }
}
```

在向外部发送交易时，通过传入的钱包流水号（sequence ID）获取交易（通过 sendCoins 或 sendTransaction）。 此举有助于通过你自己的唯一 ID 跟踪未签名/未确认的交易，因为比特币交易 ID 在共同签名前是未定义的而且在确认前是可锻的。

BitGo 中在等待状态且尚未被共同签名的交易仍然具有流水号。

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/tx/sequence/:sequenceId`

### URL 参数 URL Parameters

| 参数         | 类型             | 必需  | 说明                   |
| ---------- | -------------- | --- | -------------------- |
| walletId   | 比特币地址 (string) | YES | 钱包的 ID               |
| sequenceId | 自定义用户提供的字符串    | YES | 之前和发往外部的交易一起发送的唯一 Id |

### 响应 Response

返回一个 WalletTx 对象，包含此交易在比特币网络上的历史和状态。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |
| 404 Not Found    | 钱包里未找到此交易。    |

## 列出钱包的未花费 List Wallet Unspents

```shell
WALLET=2N91XzUxLrSkfDMaRcwQhe9DauhZMhUoxGr

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/unspents
```

```javascript
  var id = '2N91XzUxLrSkfDMaRcwQhe9DauhZMhUoxGr';
  bitgo.wallets().get({ id: id }, function callback(err, wallet) {
    if (err) {
      throw err;
    }
    wallet.unspents({limit: 4.2}, function callback(err, wallet) {
      // 处理错误，使用未花费币
      console.dir(wallet);
    });
  });
```

获取钱包的未花费输入交易的列表

为了创建比特币交易，交易的创建者需要累积一组比特币“输入(input)”用于创建交易。

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/unspents`

### URL 参数 URL Parameters

| 参数       | 类型             | 必需  | 说明     |
| -------- | -------------- | --- | ------ |
| walletId | 比特币地址 (string) | YES | 钱包的 ID |

### QUERY 参数 QUERY Parameters

| 参数     | 类型     | 必需 | 说明                               |
| ------ | ------ | -- | -------------------------------- |
| target | number | NO | API 将尝试返回足够的未花费币以累积到至少这个值（单位：聪）。 |
| skip   | number | NO | 列表的初始索引号。默认值为 0。                 |
| limit  | number | NO | 单次调用返回结果的最大数量（默认=100，最大=250）     |

> 响应示例

```json
{
    "count": 2,
    "pendingTransactions": false,
    "start": 0,
    "total": 2,
    "unspents": [
        {
            "address": "2N26EdwtVNQe6P9QkVgLHGhoWtU5W98ohNB",
            "blockHeight": 570611,
            "chainPath": "/1/117",
            "confirmations": 3474,
            "date": "2015-09-18T21:58:11.620Z",
            "isChange": true,
            "redeemScript": "522102f90f2bb90f6572af7bf5c7317ebd48311b417b005352ae71c3c79990fea1f60f2102f817f403092d09abbbb955410d1e50fca4d1ee56e145a29dde01e505558dec43210307527a3928d2711212730ef6585d1a82af80d1fe2979e167b7cc1a397c654ba253ae",
            "script": "a9146105ee32b12a94436f19592e18b135d206e5f46987",
            "tx_hash": "3246b59fcec99c81e5f59522327b632f5c54e4da42ccb512550ed91a3f9b5ce6",
            "tx_output_n": 0,
            "value": 78273186932,
            "wallet": "2N91XzUxLrSkfDMaRcwQhe9DauhZMhUoxGr",
            "instant": false
        },
        {
            "address": "2NCB6qVywiBvWmrpcnFJ4jq8m6oZrFTCKDd",
            "blockHeight": 570611,
            "chainPath": "/1/118",
            "confirmations": 3474,
            "date": "2015-09-18T21:58:23.698Z",
            "isChange": true,
            "redeemScript": "522103e48e6f9e7c2f1011b104f3353973e08c8eb83e5d276bea3d274abd1e458700582103ac2554d01691c694683fc2976183afb25492645bc338deb6425371119c163ec7210212d5e3a68be3b78f266e6247cc34d8ce8bba8d964dbe7da3d1101261ffa07af953ae",
            "script": "a914cfa2c19e759f7a3bc58ed7ae1f72d0125965a81a87",
            "tx_hash": "c005f114cbf443c7c0d2fc82bba6b78d0fd677f131467a6d7b17a67ffacd79b9",
            "tx_output_n": 1,
            "value": 1808807240,
            "wallet": "2N91XzUxLrSkfDMaRcwQhe9DauhZMhUoxGr",
            "instant": true
        }
    ]
}
```

### 响应 Response

返回一个未花费输入对象数组。

| 字段            | 说明                                                     |
| ------------- | ------------------------------------------------------ |
| tx_hash       | 未花费输入的散列值                                              |
| tx_output_n | 未花费输入的索引来自 *tx_hash*                                   |
| value         | 未花费输入的值（单位：聪）                                          |
| script        | 输出脚本散列值（十六进制格式）                                        |
| redeemScript  | 赎回脚本                                                   |
| chainPath     | 相对于此钱包的未花费输出的 BIP32 路径                                 |
| confirmations | 在未花费交易包含进区块后所见到的区块数量                                   |
| isChange      | Boolean 值，表示这是一个来自之前支出的输出，源自这个钱包，而且也许可以在 0 确认情况下安全的支出。 |
| instant       | Boolean 值，表示这个未花费是否可以被用于创建有双倍花费保障的 BitGo 即时交易。         |

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |

## 合并未花费币 Consolidate Unspents

```javascript
bitgo.wallets().get({ "id": walletId }, function callback (err, wallet) {
  if (err) { throw err; }
  wallet.consolidateUnspents(
    {
      target: desiredUnspentCount,
      minConfirms: minConfirmCountPerIteration,
      walletPassphrase: passphrase
    },
    function (err, consolidationTransactions) {
      if (err) { throw err; }
      // 全部用于合并的交易，按区块高度逆序
      console.dir(consolidationTransactions);
    }
  );
});
```

```shell
仅作为本地方法可用 (BitGo Express)
WALLETID="2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa"
WALLETPASSPHRASE="mypassword"

curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"walletPassphrase\": \"$WALLETPASSPHRASE\", \"target\": 1, \"minConfirms\": 1 }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/consolidateunspents
```

合并钱包中当前的未花费币以减少数量。 这是一个迭代操作，很大程度上取决于 交易大小限制和签名速度。 每次迭代都需要交易费。

### 参数 Parameters

| 参数                            | 类型       | 必需 | 说明                      |
| ----------------------------- | -------- | -- | ----------------------- |
| target                        | number   | NO | 期望在运行此函数后得到的未花费币数量      |
| maxInputCountPerConsolidation | number   | NO | 每次迭代使用未花费币的最大数量。默认值 85。 |
| minConfirms                   | number   | NO | 仅选择拥有指定数量确认的未花费币输入。     |
| walletPassphrase              | string   | NO | 钱包密码                    |
| progressCallback              | function | NO | 于每次迭代末端调用的函数。可用于监视进度。   |

## 分散未花费币 Fan Out Unspents

```javascript
bitgo.wallets().get({ "id": walletId }, function callback (err, wallet) {
  if (err) { throw err; }
  wallet.fanOutUnspents(
    {
      target: desiredUnspentCount,
      minConfirms: minConfirmCount,
      walletPassphrase: passphrase
    },
    function (err, fanoutTransaction) {
      if (err) { throw err; }
      // 用于分散未花费币的交易
      console.dir(fanoutTransaction);
    }
  );
});
```

```shell
Available only as a local method (BitGo Express)
WALLETID="2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa"
WALLETPASSPHRASE="mypassword"

curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"walletPassphrase\": \"$WALLETPASSPHRASE\", \"target\": 85, \"minConfirms\": 1 }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/fanoutunspents
```

取出钱包的所有未花费币（需要满足选择条件，比如最小确认数）并且将它们分散成更多数量的未花费币。

### 参数 Parameters

| 参数               | 类型     | 必需  | 说明                       |
| ---------------- | ------ | --- | ------------------------ |
| target           | number | YES | 期望在运行此函数后得到的未花费币数量       |
| minConfirms      | number | NO  | 仅选择拥有指定数量确认的未花费币输入。      |
| walletPassphrase | string | NO  | Passphrase of the wallet |

## 创建交易 Create Transaction

```javascript
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) { throw err; }
  var recipients = [];
  recipients.push({address: address, amount: amountSatoshis});
  wallet.createTransaction(
    {
      recipients: recipients,
      fee: fee
    },
    function(err, transaction) {
      // 含有已选择未花费币的交易，准备好使用 signTransaction 进行签名
      console.dir(transaction);
    });
  });
});
```

```shell
仅作为本地方法可用 (BitGo Express)
WALLETID='2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"recipients\": [{ \"address\": \"2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD\", \"amount\": 1500000}, { \"address\": \"2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu\", \"amount\": 2500000 }] }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/createtransaction
```

<aside class="warning">
此方法供高级 API 用户使用。在大多数情况下，推荐使用<a href="#send-coins-to-address">发送比特币到地址</a>方法从钱包发送比特币。
</aside>

使用钱包中地址上的未花费币创建具有多个接收者的交易。这是仅包含于 SDK 中的客户端功能。

通常在 signTransaction 对交易签名之前使用。变更将被发送至钱包中新创建的变更地址（路径 /1）。

这个高级方法让你可以手动指定矿工费用（可以为 0）并且销毁钥匙串。<b>警告</b>: 如果你指定的费用不足，你的交易可能无法被确认并且你的未花费币可能会有一段时间不可使用。

> 响应示例

```json
{
    "changeAddress": {
        "address": "2MvaEJUHmL6wzsxwqW8Xm8Qac4PTa3zMuvE",
        "path": "/1/65"
    },
    "fee": 20000,
    "transactionHex": "0100000003f6a05b9ab9d7c62cb70a662a4016cbd4740d1d6d35d3c903e3a74fd9f943d...",
    "unspents": [
        {
            "chainPath": "/0/10",
            "redeemScript": "522102f96f3d88ca43810306a7fff2e99da9f052d56a26c73a094237c55b5ec72fead62102981092615521d2d6a44a652631309b5136f589f6c625dbf94efa3edae74ddd2f2103b08e8933fc38a3eb2dc0616f336910b884f9ad80b4a7984f2d9f9e19759ff01553ae"
        },
        ...
    ],
    "walletId": "2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa",
    "walletKeychains": [
        {
            "path": "/0/0",
            "xpub": "xpub661MyMwAqRbcGM9kbvxAfLzr9WwqaVKSqw1dEm16mZmPmigQBrqzVQz314kd9jV68JjpRLCtrRWRpEJqe3FCaN4fuMTZNaDa3uMSjxUaZEj"
        },
        ...
    ]
}
```

### 参数 Parameters

| 参数                           | 类型      | 必需  | 说明                                                                                            |
| ---------------------------- | ------- | --- | --------------------------------------------------------------------------------------------- |
| recipients                   | string  | YES | 数组包含有接收者对象和发送给每个接收者的金额，例如 [{address: '38BKDNZbPcLogvVbcx2ekJ9E6Vv94DqDqw', amount: 1500}, ..] |
| fee                          | number  | NO  | 将会付给比特币矿工的绝对费用，单位为聪。设为 'undefined' 以供自动生成。                                                    |
| feeRate                      | number  | NO  | 将会按照交易大小付给比特币矿工的每 KB 费用，单位为聪。设为 'undefined' 以供自动生成。                                           |
| feeTxConfirmTarget           | number  | NO  | 按每千字节计费，目标为设定数量区块的交易确认。默认：2， 最小：2，最大：20.                                                      |
| minConfirms                  | number  | NO  | 仅选择有指定数量确认的未花费输入（input）。我们推荐设置为 1 并且使用 enforceMinConfirmsForChange。                           |
| enforceMinConfirms ForChange | boolean | NO  | 默认为 false。当创建交易时，minConfirms 将仅被强制用于不是来自这个钱包的未花费币。                                            |
| minUnspentSize               | number  | NO  | 至少达到这个数字（单位为聪）的未花费币被视作可用。默认值 5460（为了对抗 tx dust spam）。                                         |
| instant                      | boolean | NO  | 设为 true 以要求此交易使用具有 BitGo 防双倍花费保障的即时服务发送（可能会有费用）。                                              |

## 签名交易 Sign Transaction

```javascript
bitgo.wallets().get({id: walletId}, function(err, wallet) {
  wallet.getEncryptedUserKeychain({}, function(err, keychain) {
    console.log("Got encrypted user keychain");
    // Decrypt the user key with a passphrase
    keychain.xprv = bitgo.decrypt({ password: walletPassphrase, input: keychain.encryptedXprv });
    var transactionHex = "0100000003f6a05b9ab9d7c62cb70a662a4016cbd4740d1d6d35d3c903e3a74fd9f943d09c0000000017a91...";
    // Unspents Can be obtained from the createTransaction method
    var unspents = [
      {
        "chainPath": "/0/10",
        "redeemScript": "522102f96f3d88ca43810306a7fff2e99da9f052d56a26c73a094237c55b5ec72fead6"
      }
    ];

    wallet.signTransaction({
      transactionHex: transactionHex,
      unspents: unspents,
      keychain: keychain
    },
    function (err, transaction) {
      console.dir(transaction);
    });
  });
});
```

```shell
仅作为本地方法可用 (BitGo Express)
WALLETID='2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa'

UNSPENTS='[
    {
    "chainPath": "/0/10",
    "redeemScript": "522102f96f3d88ca43810306a7fff2e99da9f052d56a26c73a094237c55b5ec72fead62102981092615521d2d6a44a652631309b5136f589f6c625dbf94efa3edae74ddd2f2103b08e8933fc38a3eb2dc0616f336910b884f9ad80b4a7984f2d9f9e19759ff01553ae"
    },
    {
    "chainPath": "/0/10",
    "redeemScript": "522102f96f3d88ca43810306a7fff2e99da9f052d56a26c73a094237c55b5ec72fead62102981092615521d2d6a44a652631309b5136f589f6c625dbf94efa3edae74ddd2f2103b08e8933fc38a3eb2dc0616f336910b884f9ad80b4a7984f2d9f9e19759ff01553ae"
    },
    {
    "chainPath": "/0/0",
    "redeemScript": "52210362a0c0e532afd7c72111525ef6157a32550b2308a0afbedef01505ab2f8aa5a5210281c598ce3155665a8578e51ab4033def358644dae5f1c4abb9b9f038867804ef21031414644c53953b0557c4a3a6f9f99c0b033583b67e5c02bf0297906f60d2ee9d53ae"
    }
]'

TRANSACTIONHEX=0100000003f6a05b9ab9d7c62cb70a662a4016cbd4740d1d6d35d3c903e3a74fd9f943d09c0000000017a914b02ad680c6ff1d2fa953720940ff8e3ac6eef37987ffffffff15e66c75b266d2077c45a3370fe0da92c0c20ebe400343bf82ac522fcc8fe60a0000000017a914b02ad680c6ff1d2fa953720940ff8e3ac6eef37987ffffffff3f7d55ff4330a5b8da69abf675c18ff558e9cf04479779a998a00d3029cbe4070100000017a914c38fcf303ad53d925df43d21b45be7b8d0895c9387ffffffff0360e316000000000017a9147bd4d50e34791a6373bfa0175bf2cb9796a08f5587a02526000000000017a914fce3c3bbd6e96dd3b54f689f33fcd12c94e9dd5587308527000000000017a91424808ad9ba7f68ae0460ab3c21f5281ead31814f8700000000

KEYCHAIN='{ "xpub": "xpub661MyMwAqRbcGM9kbvxAfLzr9WwqaVKSqw1dEm16mZmPmigQBrqzVQz314kd9jV68JjpRLCtrRWRpEJqe3FCaN4fuMTZNaDa3uMSjxUaZEj",
            "encryptedXprv": "{\"iv\":\"ToxBefI++Jf7FhRUV7MNFA==\",\"v\":1,\"iter\":10000,\"ks\":256,\"ts\":64,\"mode\":\"ccm\",\"adata\":\"\",\"cipher\":\"aes\",\"salt\":\"X3Ibf2DfHd8=\",\"ct\":\"ri+99W0cLUpNCgjksZJVl425OGVrJaNJVllaldTjXumD8oxySjGALoj630Qf5xe0WD0DMJjVIAhMwlPz4g6eG6n8a2ZeKRdvfWMhp7PBEdGGCywPbtLNTfbjrS9kfM9bedncp9QXud7fERfH63vorzEP0rUOZJ0=\"}",
            "path": "m",
            "xprv": "xprv9s21ZrQH143K3s5HVuRAJD47bV7MB2bbUi62SNbVDEEQtvMFeKXjwcfZ9otQ4vZMtHzumu9LyFriSFzcjEXJp6eAL4muKaahwxAaiDPWt4R" }'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"transactionHex\": \"$TRANSACTIONHEX\", \"unspents\": $UNSPENTS, \"keychain\": $KEYCHAIN }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/signtransaction
```

<aside class="warning">
此方法供高级 API 用户使用。在大多数情况下，推荐使用<a href="#send-coins-to-address">发送比特币到地址</a>方法从钱包发送比特币。
</aside>

使用已创建交易的十六进制码、钥匙串和未花费币信息（派生路径和赎回脚本）进行多重签名交易的签名。

可进行离线操作。

这是仅包含于 SDK 中的客户端功能。

> 响应示例

```json
{"tx":"0100000003f6a05b9ab9d7c62cb70a662a4016cbd4740d1d6d35d3c903e3a74fd9f943d09c00....."}
```

### 参数 Parameters

| 参数             | 类型     | 必需  | 说明                      |
| -------------- | ------ | --- | ----------------------- |
| transactionHex | string | YES | 未签名的交易，十六进制字符串格式        |
| unspents       | array  | YES | 未花费币对象数组，包含链路径和赎回脚本。    |
| keychain       | 钥匙串对象  | YES | 已解密的钥匙串（对象），和可用的 xprv 。 |

## 发送交易 Send Transaction

```shell
TX={raw hex transaction}

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"tx\": \"$TX\", \"otp\": \"0000000\" }" \
https://test.bitgo.com/api/v1/tx/send
```

```javascript
bitgo.wallets().get({id: walletId}, function(err, wallet) {
  console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));
  wallet.getEncryptedUserKeychain({}, function(err, keychain) {
    if (err) {
      console.log("Error getting encrypted keychain!");
      console.dir(err);
      return process.exit(-1);
    }
    console.log("Got encrypted user keychain");

    // 使用密码解密用户密钥
    keychain.xprv = bitgo.decrypt({ password: walletPassphrase, input: keychain.encryptedXprv });

    // 设置接收者
    var recipients = {};
    recipients[destinationAddress] = amountSatoshis;
    console.log("Creating transaction");
    wallet.createTransaction({
      recipients: recipients,
      fee: fee
      },
      function(err, transaction) {
        if (err) {
          console.log("Failed to create transaction!");
          console.dir(err);
          return process.exit(-1);
        }

        console.dir(transaction);
        console.log("Signing transaction");
        wallet.signTransaction({
          transactionHex: transaction.transactionHex,
          unspents: transaction.unspents,
          keychain: keychain
        },
        function (err, transaction) {
          if (err) {
            console.log("Failed to sign transaction!");
            console.dir(err);
            return process.exit(-1);
          }

          console.dir(transaction);
          console.log("Sending transaction");
          wallet.sendTransaction({tx: transaction.tx}, function (err, callback) {
            console.log("Transaction sent: " + callback.tx);
          });
        });
      }
    );
  });
});
```

<aside class="warning">
此方法供高级 API 用户使用。在大多数情况下，推荐使用<a href="#send-coins-to-address">发送比特币到地址</a>方法从钱包发送比特币。
</aside>

发送一个部分签名的交易。 服务器将进行下列操作之一： * 拒绝交易 * 从其他钱包管理员处获取附加批准 * 应用最终签名，并且提交到比特币 P2P 网络<aside class="info"> 此 API 需要使用解锁 API 解锁会话。 单个解锁 API 调用允许任意的单个交易，或者多个不超过内部设定 BitGo 配额的交易（当前设定为 50 BTC）。 </aside> 

### HTTP 请求 HTTP Request

`POST /api/v1/tx/send`

> 响应示例

```json
{
  "tx": "01000000022cbc51a1d73a7e8ef3feaf30ad51b08b53cf91c672ba73..",
  "hash": "b3bd8ac76de2340c1159337acdfcaabf08a9470e8157870bd1d846..",
  "status": "accepted",
  "instant": true,
  "instantId": "56651c4c4e82b975616b58647c77fe1dc"
}
```

### BODY 参数 BODY Parameters

| 参数         | 类型      | 必需  | 说明                                                        |
| ---------- | ------- | --- | --------------------------------------------------------- |
| tx         | 交易对象    | YES | 交易，十六进制字符串格式。                                             |
| sequenceId | String  | NO  | 用户输入的自定义字符串，可用于识别签名前后的交易。                                 |
| message    | String  | NO  | 用户输入的字符串（此字符串不会到达区块链）                                     |
| instant    | boolean | NO  | 设为 true 以要求此交易使用具有 BitGo 防双倍花费保障的即时服务发送（可能会有费用）。          |
| otp        | String  | NO  | 7 位数密码用于绕开带有 “getOTP” 动作类型的策略。详见 [钱包策略](#wallet-policy) 。 |

### 响应 Response

以十六进制编码格式，返回已发送的交易和它的散列值。

### 错误 Errors

| 响应                   | 说明             |
| -------------------- | -------------- |
| 400 Bad Request      | 请求参数缺失或有错误。    |
| 401 Unauthorized     | 验证参数不符，或需要解锁。  |
| 402 Payment Required | 这个请求中的交易费似乎过低。 |

### 策略/失败响应 Policy/Failure Response

| 字段              | 说明                       |
| --------------- | ------------------------ |
| error           | 来自触发本次待批准交易的策略的消息        |
| pendingApproval | 待批准的 id，需要另一名用户进行批准      |
| otp             | 触发的策略是 "getOTP"类型时为 true |
| triggeredPolicy | 触发本次待批准交易的策略 id          |
| status          | 交易状态                     |

## 获取即时交易保障 Get Instant Guarantee

```shell
INSTANTID=564ea1fa95f4344c6db00773d1277160

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/instant/$INSTANTID
```

```javascript
bitgo.instantGuarantee({ id: '56562ee923ab7f3a28d638085ba6955a' }, function(err, result) {
  if (err) {
      console.log("Error getting guarantee!");
      console.dir(err);
      return process.exit(-1);
  }
  console.dir(result);
});
```

BitGo 即时交易服务位于我们的钱包平台顶端，作为 BitGo 防止双倍花费的保障。 作为多重签名钱包上的共同签名者，BitGo 将决不会双重花费输出。 作为承诺我们在每笔交易上使用加密签名保障，使接收者不需要任何区块确认就可以接受资金。

<aside class="info">
任何人都可以接受即时交易。发送即时交易需要即时交易兼容 / KRS BitGo 钱包。
</aside>

### HTTP 请求 HTTP Request

`GET /api/v1/instant/<id>`

> 响应示例

```json
{
    "amount": 600000,
    "createTime": "2015-11-20T04:30:49.894Z",
    "guarantee": "BitGo Inc. guarantees the transaction with hash 8ba08ef2a745246f309ec4eaff5d7652c4fc01e61eebd9aabc1c58996355acd7 or normalized hash 62f76eb48d60a1c46cb74ce42063bd9c0816ca5e17877738a824525c9794ceaf for the USD value of 0.00600000 BTC at Fri Nov 20 2015 04:30:49 GMT+0000 (UTC) until confirmed to a depth of 6 confirms.",
    "id": "564ea1fa95f4344c6db00773d1277160",
    "normalizedHash": "62f76eb48d60a1c46cb74ce42063bd9c0816ca5e17877738a824525c9794ceaf",
    "signature": "1c2ea42ab4b9afdc069441401a1...",
    "state": "open",
    "transactionId": "8ba08ef2a745246f309ec4eaff5d7652c4fc01e61eebd9aabc1c58996355acd7"
}
```

### 响应 Response

返回即时交易保障消息，包含金额和交易 ID。

| 参数             | 类型       | 说明                          |
| -------------- | -------- | --------------------------- |
| amount         | Number   | 即时交易保障的金额，单位为聪              |
| createTime     | DateTime | 交易创建的时间                     |
| guarantee      | String   | BitGo 的即时交易保障消息             |
| id             | String   | BitGo 上的即时交易保障 ID           |
| transactionId  | String   | 受保障交易的散列值                   |
| normalizedHash | String   | 受保障交易不含签名的散列值               |
| signature      | String   | 加密签名的保障，作为审计记录以防万一出现纠纷      |
| state          | String   | 受 BitGo 监控交易的状态（你不需要对此进行操作） |

### 验证 BitGo 的保障 Verifying BitGo's Guarantee

BitGo 的保障使用我们公司的签名密钥进行签名，与公有的比特币地址 1BitGo3gxRZ6mQSEH52dvCKSUgVCAH4Rja 相关联。

要确认这一点，使用我们的签名验证保障。例：

`assert(bitcoin.Message.verify('1BitGo3gxRZ6mQSEH52dvCKSUgVCAH4Rja', signature, guarantee, process.config.bitcoin.network));`

若签名有效，你可以立即接受交易不需要任何区块信息。 你可以在本地保存保障签名作为审计记录以防万一出现纠纷。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |

## 按地址获取钱包 Get Wallet by Address

提供一个地址，返回该地址的信息（包括余额）和此地址关联的钱包。 当某人拥有多个地址 / 钱包，但不知道地址属于哪个钱包时很有用。

```shell
ADDRESS=2NBMGw7K9XiBPfvW3nUQcrANKncmAoLUdDX

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/walletaddress/$ADDRESS
```

```javascript
var address = '2NBMGw7K9XiBPfvW3nUQcrANKncmAoLUdDX';
bitgo.getWalletAddress({ address: address }, function(err, result) {
  if (err) { console.log(err); process.exit(-1); }
  console.dir(result);
});
```

### HTTP 请求 HTTP Request

`GET /api/v1/walletaddress/:address`

### URL 参数 URL Parameters

| 参数      | 类型             | 必需  | 说明        |
| ------- | -------------- | --- | --------- |
| address | 比特币地址 (string) | YES | 需要获取信息的地址 |

> 响应示例

```json
{
    "address": "3JNUVmyjdLySccafwPSGDfS3dCVEz3Rnpj",
    "balance": 0,
    "chain": 0,
    "index": 7,
    "path": "/0/7",
    "received": 0,
    "redeemScript": "52210306d46ec69fdbf36c0eb1c4fe96ac0417d1375490a6811fdfa6f1dc3c78aad50c2102fb38d7ffb3e354a74b3231eeee6b361c7f3c5ddccac15c7f06e5557f4e6a5318210270467cb619033ef9bcb7cc39d4ce45084418229c0e8aa65821345a222a24a2e453ae",
    "sent": 0,
    "txCount": 0,
    "wallet": "32uFQgny5bgj24nywQPo5cgEUGX6a1PuAp"
}
```

### 响应 Response

返回一个钱包地址对象

| 字段           | 说明                                |
| ------------ | --------------------------------- |
| address      | 要查找的比特币地址                         |
| balance      | 此地址上当前的余额（聪）                      |
| chain        | 用于生成此地址的 HD 链（0 为用户生成的，1 为变更的）    |
| index        | HD 链中用于生成此地址的索引                   |
| path         | 钱包上地址的 HD 路径                      |
| received     | 此地址上已接收的总额（聪）                     |
| sent         | 此地址上已发送的总额（聪）                     |
| txCount      | 此地址上的交易总笔数                        |
| redeemScript | 此 redeemScript 可被用于花费此 P2SH 地址的资金 |
| wallet       | 该地址所在钱包的基础地址（钱包 ID）               |

## 冻结钱包 Freeze Wallet

```shell
WALLETID='2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe'
DURATION='4000'
curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"duration\": \"$DURATION\" }" \
https://test.bitgo.com/api/v1/wallet/$WALLETID/freeze
```

```javascript
bitgo.wallets().get({ "id": walletId }, function(err, wallet) {
  bitgo.unlock({ otp: otp }, function(err) {
    if (err) {
      console.dir(err);
      throw new Error("Could not unlock!");
    }
    wallet.freeze({ "duration": 4000 }, function(err, result) {
      console.dir(result);
    });
  });
});
```

> 响应示例

```json
{
  "time" : "2016-04-01T03:51:39.779Z",
  "expires" : "2016-04-01T04:58:19.779Z"
}
```

阻止钱包上的任何花费动作。此调用设计目的是为了万一发生紧急情况时阻止花费，默认值 1 小时。

### 参数 Parameters

| 参数       | 类型     | 必需 | 说明                     |
| -------- | ------ | -- | ---------------------- |
| duration | number | NO | 冻结花费动作的时长秒数。默认值为 1 小时。 |

### 响应 Response

| 字段      | 说明             |
| ------- | -------------- |
| time    | 调用冻结命令的时间      |
| expires | 在此时间之后花费动作将被允许 |

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |

# 钱包共享 Wallet Sharing

一个 BitGo 钱包可以被共享给多个用户。 钱包上的所有用户共享同一个私钥（每个用户可以选择单独加密此私钥）。

BitGo 对共享钱包强制启用安全功能，要求用户们在共同签名前必须登录且进行验证。 钱包权限级别定义了单个用户可以在钱包上进行的操作。

### 钱包权限 Wallet Permissions

| 权限    | 说明                |
| ----- | ----------------- |
| View  | 查看钱包上的交易          |
| Spend | 在钱包上发起交易，受制于钱包策略。 |
| Admin | 修改钱包策略且管理用户和设置    |

## 共享一个钱包 Sharing a wallet

```shell
仅作为本地方法可用 (BitGo Express)

WALLETID='2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe'
PASSPHRASE='walletpassphrase'
RECEIVEREMAIL='test@bitgo.com'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"walletPassphrase\": \"$PASSPHRASE\", \"email\": \"$RECEIVEREMAIL\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/simpleshare
```

```javascript
bitgo.wallets().get({ "id": walletId }, function(err, wallet) {
    wallet.shareWallet({ email: receiverUser, walletPassphrase: senderPassword, permissions: 'view,spend', skipKeychain: false }, function callback(err, result) {
        console.dir(result);
    });
});
```

> 响应示例

```json
{
  "id": "54c594802ebe8510790092958f526f47",
  "walletId": "2MsaMz4tYy5RieZ8qBeW1rhsTpNAXjpofSC",
  "walletLabel": "test2",
  "fromUser": "54c589521758c40e79008e91a7088190",
  "toUser": "5458141599f715232500000530a94fd2",
  "permissions": "view,spend,admin",
  "state": "active",
  "keychain":
   { "xpub": "xpub661MyMwAqRbcFcDmevjhDfSebRy8toH17gJ64a2qAMr5gC4JZRWLUJwPDFeKg6aTMNPa1hYQ3s5kJkDNPMbAw5oQdcjn9N58rYQeUnUozFn",
     "encryptedXprv": "{iv:uoXLSMgfplagVOku...",
     "fromPubKey": "041363164D0521A...",
     "toPubKey": "031D388E7B16FE..",
     "path": "m/999999/26697279/124485569"
   }
}
```

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

共享一个钱包涉及到给予另一个用户权限通过 BitGo 使用此钱包。

为了使接收者可以使用该钱包，我们同样须要和他共享私钥。 为了这个目的，在注册过程中每个 BitGo 用户会创建一个公有-私有密钥对。

BitGo SDK 在客户端进行如下操作以创建一个新的钱包共享：

* 获取接收用户的共享密钥（由此接收者的公钥派生出的路径）
* 解密钱包以在本地共享
* 使用上述公钥重新加密钱包，使接收者可以解密。
* 上传加密的密钥到 BitGo 服务，以通知接收者他们有一个待处理的共享。

### 参数 Parameters

| 参数               | 类型      | 必需  | 说明                                 |
| ---------------- | ------- | --- | ---------------------------------- |
| email            | string  | YES | 要与其进行钱包共享的用户的电子邮件                  |
| permissions      | string  | YES | 用逗号分隔的权限列表，例如 view,spend,admin     |
| walletPassphrase | string  | NO  | 要共享的钱包的密码                          |
| skipKeychain     | boolean | NO  | 设为 true 表示要与其进行钱包共享的用户将通过其它方式接收钥匙串 |
| disableEmail     | boolean | NO  | 设为 true 以阻止发送电子邮件给添加的用户            |

### 响应 Response

| 字段          | 说明                       |
| ----------- | ------------------------ |
| id          | walletShare的 id，用于接受共享   |
| walletId    | 要共享的钱包的 id               |
| walletLabel | 显示给用户的钱包标签               |
| fromUser    | 共享钱包用户的 BitGo ID         |
| toUser      | 接收钱包用户的 BitGo ID         |
| permissions | 用逗号分隔的权限列表，将由钱包共享发送给接收用户 |
| keychain    | 已加密的钥匙串供接收者进行解密（用于获取私钥）  |

## 列出钱包共享 List Wallet Shares

```shell
curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/walletShare
```

```javascript
  bitgo.wallets().listShares({}, function callback(err, result) {
    if (err) {
      throw err;
    }
    console.dir(result);
  });
```

获取已登录帐号的传入和传出钱包共享列表。

### HTTP 请求 HTTP Request

`GET /api/v1/walletShare`

> 响应示例

```json
{
   "incoming":
   [
     { "id": "54c58f8d2ebe851079008fe3bbd9e50f",
       "walletId": "2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe",
       "walletLabel": "test1",
       "fromUser": "54c589521758c40e79008e91a7088190",
       "toUser": "5458141599f715232500000530a94fd2",
       "permissions": "view,spend,admin",
       "state": "active" },
     { "id": "54c594802ebe8510790092958f526f47",
       "walletId": "2MsaMz4tYy5RieZ8qBeW1rhsTpNAXjpofSC",
       "walletLabel": "test2",
       "fromUser": "54c589521758c40e79008e91a7088190",
       "toUser": "5458141599f715232500000530a94fd2",
       "permissions": "view,spend,admin",
       "state": "active" }
   ],
   "outgoing": []
}
```

### 响应 Response

返回的每个钱包共享对象包含下列字段：

| 字段          | 说明                       |
| ----------- | ------------------------ |
| id          | walletShare的 id，用于接受共享   |
| walletId    | 共享的钱包的 id                |
| walletLabel | 显示给用户的钱包标签               |
| fromUser    | 共享钱包用户的 BitGo ID         |
| toUser      | 接收钱包用户的 BitGo ID         |
| permissions | 用逗号分隔的权限列表，将由钱包共享发送给接收用户 |

## 接受钱包共享 Accept Wallet Share

```shell
仅作为本地方法可用 (BitGo Express)

SHAREID='54c594802ebe8510790092958f526f47'
NEWPASSPHRASE='receiverpassphrase'
PASSWORD='sharingkeypassword'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"newWalletPassphrase\": \"$NEWPASSPHRASE\", \"userPassword\": \"$PASSWORD\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/walletshare/$SHAREID/acceptShare
```

```javascript
bitgo.wallets().acceptShare(
    { walletShareId: shareId,
      newWalletPassphrase: 'receiverpassphrase',
      userPassword: 'receiverpassword'
    }, function(err, result) {
        console.dir(result);
    }
)
```

> 响应示例

```json
{ "state": "accepted", "changed": "true" }
```

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

客户端操作用于接受钱包共享。操作步骤如下：

* 获取传入的钱包共享，包括已加密的私有钥匙串。
* 使用该用户的共享私钥和钱包共享 xPub，派生出密钥对私有钥匙串进行解密。
* 使用该用户选择的密码重新加密钱包供日后使用。
* 上传已加密的密钥到 BitGo 服务并设置共享为已接受（accepted），给予该用户权限在 BitGo 上访问该钱包。

### 参数 Parameters

| 参数                    | 类型             | 必需  | 说明                                    |
| --------------------- | -------------- | --- | ------------------------------------- |
| walletShareId         | 比特币地址 (string) | YES | 传入的钱包共享 ID 用以接受                       |
| newWalletPassphrase   | string         | NO  | 要设置到钱包上的密码，供将来花费使用。                   |
| userPassword          | string         | NO  | 用户密码用于解密共享的私钥                         |
| overrideEncryptedXprv | string         | NO  | 设置到另一个加密的 xprv，若你希望保存一个从其他途径接收的 xprv。 |

### 响应 Response

| 字段      | 说明                              |
| ------- | ------------------------------- |
| state   | walletShare的最终状态（应为 'accepted'） |
| changed | 若成功则为 true                      |

## 取消钱包共享 Cancel Wallet Share

```shell
SHAREID='54c594802ebe8510790092958f526f47'

curl -X DELETE \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/walletshare/$SHAREID
```

```javascript
bitgo.wallets().cancelShare({ walletShareId: cancelledWalletShareId }, function(err, result) {
    if (err) { throw err; }
    console.dir(result);
)
```

> 响应示例

```json
{ "state": "canceled", "changed": "true" }
```

可被用于取消一个等待中的传出钱包共享，或者拒绝一个传入共享。该共享必须尚未被接受。

### HTTP 请求 HTTP Request

`DELETE/api/v1/walletshare/:SHAREID`

### 响应 Response

| 字段      | 说明              |
| ------- | --------------- |
| state   | walletShare的新状态 |
| changed | 若成功则为 true      |

## 移除钱包用户 Remove Wallet User

在用户接受了钱包共享后，他们将成为钱包的一份子并且钱包共享被视作“完成”。

要在用户接受后撤销对其共享，你可以从钱包中移除该用户。

当钱包上有多个管理员时，需要得到其他钱包管理员批准。

```javascript
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) {
    throw err;
  }

  wallet.removeUser({ "user": userId }, function callback(err, wallet) {
    if (err) {
      // 处理错误
    }
    // 其他
  });
});
```

```shell
WALLETID=2MvPJ8GMoFFWdUTh6p42FJV6XpmJEVjxv92
USERID=5479ac2bfb1d3e5e71000005d4dc49e3
curl -X DELETE \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLETID/user/$USERID
```

### HTTP 请求 HTTP Request

`DELETE /api/v1/wallet/:wallet/user/:userId`

### URL 参数 URL Parameters

| 参数     | 必需  | 说明                      |
| ------ | --- | ----------------------- |
| wallet | YES | 钱包的 ID                  |
| userId | YES | 需要移除的用户的 id （可在钱包对象中找到） |

### 响应 Response

返回该钱包更新后的钱包模型，或者待批准对象（若需要批准的话）。

### 错误 Errors

| 响应               | 说明            |
| ---------------- | ------------- |
| 400 Bad Request  | 请求参数缺失或有错误。   |
| 401 Unauthorized | 验证参数不符，或需要解锁。 |

# 钱包策略 Wallet Policy

BitGo 钱包具有高级安全特性如多用户、短消息交易批准和花费限制。 要好好利用这些特性，用户/开发者需要添加且修改钱包上的策略规则。 规则会触发一个关联的动作（由用户设置）。 策略引擎会搜集所有已触发规则的结果，并且执行触发动作顺序为`deny`, `getApproval` (从另一个用户处), 或 `getOTP` (发送短消息到指定用户)。

如果钱包有余额且有超过二名“admin”（管理员）用户与此钱包有关联， 任何策略修改在生效前都需要另一位管理员批准（如果没有额外的“admin”用户， 则无需这一步）。 因此强烈推荐在使用[钱包共享操作](#wallet-sharing)创建钱包共享时，使用有至少 2 名管理员的钱包。 这样一来，即使有一名用户出问题策略仍将保持生效。

对带有 `getOTP` 动作类型的策略，要成功发送一笔交易需要在交易被签名和发送前，提供一个 7 位数的一次性密码（OTP）。 此动作类型可以让你对自己的服务启用两步验证，不用自己写这个功能。 第一次尝试发送交易会失败，并向规则中指定的手机发送密码。 当你从用户处获取密码后， 再次使用 API 进行发送交易调用，一次性密码作为参数， 交易将被成功发送。 要了解详情，请查看[发送比特币到地址](#send-coins-to-address)中的 `otp` 参数。

本文档提供的 API 和 SDK 覆盖了涉及单个钱包的基本 BitGo 策略。 进一步的自定义策略可使用 [webhook 策略类型](#set-policy-rule)实现， 这将使 BitGo 调用外部 URL 端点，可以评估任何含有外部状态的自定义策略动作。

要实现含有多个钱包的高级策略，请直接联络 BitGo。

## 获取策略 Get Policy

```shell
WALLET=2NEe9QhKPB2gnQLB3hffMuDcoFKZFjHYJYx

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/policy
```

```javascript
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) {
    throw err;
  }

  wallet.getPolicy({}, function callback(err, policy) {
    if (err) {
      // 处理错误
    }
    console.dir(policy);
  });
});
```

> 响应示例

```json
{
  "id": "57d73dedd1187a4a7b2bdeebedddcd6b",
  "version": 0,
  "date": "2016-09-12T23:44:45.462Z",
  "rules": [
    {
      "id": "my velocity limit",
      "type": "velocityLimit",
      "action": {
        "type": "getOTP",
        "actionParams": {
          "otpType": "sms",
          "phone": "+15417543010",
          "duration": "3600"
        }
      },
      "condition": {
        "amount": 10000000,
        "timeWindow": 86400,
        "groupTags": [
          ":tag"
        ],
        "excludeTags": []
      }
    },
    {
      "action": {
          "type": "getApproval"
      },
      "condition": {
        "amount": 200000000
      },
      "id": "com.bitgo.limit.velocity.day",
      "type": "velocityLimit"
    }
  ]
}
```

获取钱包上生效的策略规则。

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/policy`

### URL 参数 URL Parameters

| 参数       | 类型             | 必需  | 说明     |
| -------- | -------------- | --- | ------ |
| walletId | 比特币地址 (string) | YES | 钱包的 ID |

### 响应 Response

返回一个钱包策略对象，包含设置在上面的规则。

## 获取策略状态 Get Policy Status

```shell
WALLET=2NEe9QhKPB2gnQLB3hffMuDcoFKZFjHYJYx

curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLET/policy/status
```

```javascript
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) {
    throw err;
  }

  wallet.getPolicyStatus({}, function callback(err, policy) {
    if (err) {
      // handle error
    }
    console.dir(policy);
  });
});
```

> 响应示例

```json
{
    "statusResults": [
        {
            "policy": "55380d34d0d3b00364a52285f09e23a4",
            "ruleId": "com.bitgo.limit.velocity.day",
            "status": {
                "remaining": 94982419
            }
        },
        {
            "policy": "55380d34d0d3b00364a52285f09e23a4",
            "ruleId": "com.bitgo.limit.tx",
            "status": {
                "remaining": 200000000
            }
        }
    ]
}
```

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/policy/status`

### URL 参数 URL Parameters

| 参数       | 类型             | 必需  | 说明     |
| -------- | -------------- | --- | ------ |
| walletId | 比特币地址 (string) | YES | 钱包的 ID |

### 响应 Response

返回由钱包策略中活动的规则生成的状态结果，包含使用限量信息。

## 设置策略规则 Set Policy Rule

添加或更新钱包策略的规则。 钱包策略规则控制在何种情况下 BitGo 将使用自己的单独密钥对交易签名。 当策略更新时电子邮件通知会被发送给所有钱包用户。 首次添加策略时不会发送电子邮件。

```shell
WALLETID=2NFj9CHyY8cLKH4UXsivpRa5xkdvAXqqai9

curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{ "action" : { "type" : "getApproval" }, "condition": {"type": "velocity", "amount": 10100000000, "timeWindow": 86400, "groupTags": [":tag"], "excludeTags": [] }, "id": "my velocity limit", "type": "velocityLimit" }' \
https://test.bitgo.com/api/v1/wallet/$WALLETID/policy/rule
```

```javascript
var walletId = '2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2';
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) { throw err; }
  // 设置策略
  var rule = {
    id: "test1",
    type: "velocityLimit"
    action: { type: "getApproval" },
    condition: {
      "type": "velocity",
      "amount": 101*1e8,
      "timeWindow": 24 * 60 * 60,
      "groupTags": [
        ":tag"
      ],
      "excludeTags": []
    }
  };
  wallet.setPolicyRule(rule, function callback(err, wallet) {
    if (err) { throw err; }
    console.dir(wallet.admin.policy);
  });
});
```

### HTTP 请求 HTTP Request

`PUT /api/v1/wallet/:wallet/policy/rule`

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

### BODY 参数 BODY Parameters

策略规则对象具有类型（type）、条件（condition）和动作（action）。策略的类型通常决定了条件的价值。

| 参数        | 类型                | 必需                           | 范例 |
| --------- | ----------------- | ---------------------------- | -- |
| id        | 策略的 id            | "custom1", "anyUniqueRuleId" |    |
| type      | 策略的类型             | *见策略类型*                      |    |
| condition | 策略的条件             | *见策略类型*                      |    |
| action    | 当条件为 false 时采取的动作 | *见策略动作对象*                    |    |

> 响应示例

```json
{
  "policy": {
    "id": "57d73dedd1187a4a7b2bdeebedddcd6b",
    "version": 0,
    "date": "2016-09-13T00:08:30.661Z",
    "rules": [{
      "id": "my velocity limit",
      "type": "velocityLimit",
      "action": {
        "type": "getApproval"
      },
      "condition": {
        "amount": 10100000000,
        "timeWindow": 86400,
        "groupTags": [
          ":tag"
        ],
        "excludeTags": []
      }
    }]
  }
}
```

### 响应 Response

Returns the updated Wallet Model object.

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |

### 策略对象 Policy Object

总体上的钱包策略是一个包含策略规则对象的数组。策略规则对象有类型（type）、条件（condition）和动作（action）。

| 字段        | 说明                | 可能值                                                                       |
| --------- | ----------------- | ------------------------------------------------------------------------- |
| id        | 策略的 id            | "com.bitgo.limit.tx", "custom1", "anyUniqueRuleId"                        |
| type      | 策略的类型             | "transactionLimit", "velocityLimit", "bitcoinAddressWhitelist", "webhook" |
| condition | 策略的条件             | *取决于所使用的策略规则类型*                                                           |
| action    | 当条件为 false 时采取的动作 | *见策略动作对象*                                                                 |

### 策略类型 - velocityLimit（速率限制）, Policy Type - dailyLimit

velocityLimit 策略规则：当花费的比特币金额在指定的时间窗口内超过指定额度时，会被触发。

本规则的条件包括有：

| 字段          | 说明                                                      | 可能值     |
| ----------- | ------------------------------------------------------- | ------- |
| amount      | 在时间窗口内允许发出的交易最大值                                        | 单位为聪的数值 |
| timeWindow  | 用于汇总此时间段内的交易花费金额并与限额相比较                                 | 秒数      |
| groupTags   | 标记指定操作列表， ":tag" 适用于大部分情况                               | 字符串数组   |
| excludeTags | 标记用于定义钱包 id 组，若有花费至所定义的钱包，则花费不计入限额。同样支持用 :tag 包含当前标记上下文 | 字符串数组   |

### 策略类型 - transactionLimit（交易限额）, Policy Type - transactionLimit

transactionLimit 策略规则：当单笔交易超过指定金额时触发。

<aside class="warning">
交易限额几乎总要设定金额为 0，这样意味着每次尝试从钱包发出交易都将触发策略。 交易限额若不是设定为 0 会非常不安全，因为攻击者只需多次发送低于策略规则金额的交易，即可避开策略。
</aside>

本规则的条件包括有：

| 字段     | 说明              | 可能值     |
| ------ | --------------- | ------- |
| amount | 钱包上每笔交易所允许的最大限额 | 单位为聪的数值 |

### 策略类型 - bitcoinAddressWhitelist（比特币地址白名单）, Policy Type - bitcoinAddressWhitelist

比特币地址白名单规则被激活时，当传出交易的任何目标比特币地址（未变更）不在白名单中时将被触发。

本规则的条件包括有：

| 字段     | 说明          | 可能值   |
| ------ | ----------- | ----- |
| add    | 添加比特币地址到白名单 | 比特币地址 |
| remove | 从白名单移除比特币地址 | 比特币地址 |

> Webhook 调用示例（发送至你的服务器，任何非 200 响应都将激活策略规则动作）

```json
{
    "approvalCount": 0,
    "outputs": [
        {
            "outputAddress": "2N9kNR8iS46WuwekvQVaTUa74w2fbvAXHQn",
            "outputWallet": "2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2",
            "value": 24885327
        },
        {
            "outputAddress": "2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe",
            "outputWallet": "2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe",
            "value": 10000000
        }
    ],
    "ruleId": "webhookRule1",
    "spendAmount": 10009060,
    "type": "webhook",
    "unsignedRawTx": "0100000001f8de6273285b13f20b59195c4...",
    "walletId": "2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2"
}
```

### 策略类型 - webhook, Policy Type - webhook

当激活时，webhook 规则将向条件中指定的 HTTPS 端点发起调用。 若 HTTPS 端点返回非 200 （状态）响应本规则将触发一个动作。

本规则的条件：

| 字段  | 说明         | 可能值      |
| --- | ---------- | -------- |
| url | URL 用于发起调用 | HTTPS 端点 |

在调用中发送的 BODY 参数：

| 字段            | 说明                        | 可能值                                                                          |
| ------------- | ------------------------- | ---------------------------------------------------------------------------- |
| walletId      | 发起交易钱包的钱包 ID              | "2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe"                                        |
| ruleId        | 当创建策略规则时由用户提供的自定义规则 ID    | "webhookPolicy1"                                                             |
| outputs       | 输出对象数组包含 outputAddress 和值 | [{ "outputAddress":"2N9kNR8iS46WuwekvQVaTUa74w2fbvAXHQn", "value":24885327}] |
| spendAmount   | 从钱包花费的净额，变动较少（包含费用）       | 10009060                                                                     |
| approvalCount | 钱包上至今为止的用户批准数量            |                                                                              |
| unsignedRawTx | 半签名原始交易的十六进制字符串           | "0100000001... 0794c5382a38700000000"                                        |
| sequenceId    | 由发送者创建交易时提供的自定义流水号        | "custom1"                                                                    |

### 策略动作对象 Policy Action Object

在交易不符合条件时将触发的动作。

| 字段           | 说明                                         | 可能值                             |
| ------------ | ------------------------------------------ | ------------------------------- |
| type         | 动作类型                                       | "deny", "getApproval", "getOTP" |
| actionParams | JSON 对象包含有 phone/otpType/duration          | *参见下文*                          |
| otpType      | 规定了如何发送密码 (必须设置 type === "getOTP")         | "sms"                           |
| phone        | 接收密码的电话号码 (必须设置 type === "getOTP")         | "541-754-3010", "+498963648018" |
| duration     | 一次性密码（OTP）的有效时间秒数 (必须设置 type === "getOTP") | 3600                            |

## 移除策略规则 Remove Policy Rule

```shell
RULEID='test1'
WALLETID='2MvPJ8GMoFFWdUTh6p42FJV6XpmJEVjxv92'

curl -X DELETE \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"id\": \"$RULEID\" }" \
https://test.bitgo.com/api/v1/wallet/$WALLETID/policy/rule
```

```javascript
var walletId = '2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2';
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) { throw err; }
  wallet.removePolicyRule({ "id": ruleId }, function callback(err, wallet) {
    if (err) { throw err; }
    console.dir(wallet.admin.policy);
  });
});
```

> 响应示例

```json
{
  "date": "2015-10-29T23:33:20.166Z",
  "id": "5631d2f48cad5fab2c6abe16682372bb",
  "rules": [ ],
  "version": 5
}
```

根据指定的 id 移除策略规则。若钱包有超过 1 名管理员时，需要第二批准。

### HTTP 请求 HTTP Request

`DELETE /api/v1/wallet/:WALLETID/policy/rule`

<aside class="info">
此操作需要使用解锁 API 解锁会话。
</aside>

### BODY 参数 BODY Parameters

| 参数 | 类型     | 必需  | 说明          |
| -- | ------ | --- | ----------- |
| id | string | YES | 要移除的策略规则 id |

### 响应 Response

返回更新后的钱包模型对象

# 待批准 Pending Approvals

## 列出待批准 List Pending Approvals

列出一个钱包或企业上的待批准项，通过提供钱包 id 或 企业 url。默认情况下，该请求将返回用户下的所有待批准项。

```shell
curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/pendingapprovals?enterprise=$ENTID
```

```javascript
var enterpriseId = '2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2';
bitgo.pendingapprovals().list({
  "enterpriseId": enterpriseId
}, function(err, pendingapprovals) {
  if (err) { throw err; }
  console.dir(pendingapprovals);
});
```

> 响应示例

```json
{
   "pendingApprovals": [
       {
           "createDate": "2015-04-28T21:57:52.747Z",
           "creator": "54c2...0c79",
           "enterprise": "54a2...1e88",
           "id": "5540...0633",
           "info": {
               "policyRuleRequest": {
                   "action": "update",
                   "policyChanged": "553e...84c7",
                   "update": {
                       "action": {
                           "type": "getApproval"
                       },
                       "condition": {
                           "amount": 300000000
                       },
                       "id": "com.bitgo.limit.velocity.day",
                       "type": "velocityLimit"
                   }
               },
               "type": "policyRuleRequest"
           },
           "state": "pending"
       }
   ]
}
```

### HTTP 请求 HTTP Request

`GET /api/v1/pendingapprovals`

### URL 参数 URL Parameters

| 参数         | 类型         | 必需 | 说明       |
| ---------- | ---------- | -- | -------- |
| walletId   | 地址（string） | NO | 钱包的基础地址  |
| enterprise | string     | NO | 企业的公有 ID |

### 响应 Response

返回待批准列表

## 更新待批准 Update Pending Approval

更新待批准项的状态为 'approved'（已批准） 或 'rejected'（已拒绝）。

```shell
curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN_OTHERENTERPRISEUSER" \
-d '{ "state": "approved" }' \
https://test.bitgo.com/api/v1/pendingapprovals/:PENDINGAPPROVALID
```

```javascript
var enterpriseId = '2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2';
bitgo.pendingapprovals().list({
  "enterpriseId": enterpriseId
}, function(err, pendingapprovals) {
  if (err) { throw err; }
  var pendingapproval = pendingapprovals[0];
  pendingapproval.approve({
    "walletPassphrase": "pa55w0rd"
  }, function(err, res) {
    if (err) { throw err; }
    console.dir(res);
  });
});
```

> 响应示例

```json
{
   "createDate": "2015-04-28T21:58:02.416Z",
   "creator": "54c2...0c79",
   "enterprise": "54a2...1e88",
   "id": "5540...10f7",
   "info": {
       "policyRuleRequest": {
           "action": "update",
           "policyChanged": "553e...84c7",
           "update": {
               "action": {
                   "type": "getApproval"
               },
               "condition": {
                   "amount": 300000000
               },
               "id": "com.bitgo.limit.velocity.day",
               "type": "velocityLimit"
           }
       },
       "type": "policyRuleRequest"
   },
   "resolvers": [
       {
           "date": "2015-04-28T22:02:32.395Z",
           "user": "5458...4fd2"
       }
   ],
   "state": "approved"
}
```

### HTTP 请求 HTTP Request

`PUT /api/v1/pendingapprovals/:pendingApprovalId`

### BODY 参数 BODY Parameters

| 参数    | 类型     | 必需  | 说明                                          |
| ----- | ------ | --- | ------------------------------------------- |
| state | string | YES | 待批准项目的新状态: 'approved'（已批准）, 'rejected'（已拒绝） |

### 响应 Response

返回含有新状态的已更新的待批准项。

# 地址标签 Address Labels

标签可以让你以可读的注释追踪地址。 你可以对任何有效地址添加标签；地址不一定需要受钱包控制。 地址标签和钱包标签不同， 不过他们都和钱包相关联，因此当你与他人共享钱包时 他们也能查看标签。

## 列出所有钱包的标签 List Labels For All Wallets

```shell
curl -X GET \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/labels
```

```javascript
bitgo.labels({}, function callback(err, labels) {
  if (err) {
    // 处理错误
  }
  // 标签操作
  console.dir(labels);
});
```

获取用户的标签列表

### HTTP 请求 HTTP Request

`GET /api/v1/labels`

> 响应示例

```json
{
  "labels": [
    {
      "walletId": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
      "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
      "label": "My Favorite Multi-Sig Address"
    },
    {
      "walletId": "2MyWgQ3PMAWPenJnHJUPpVjFDLHhaPkZCz5",
      "address": "msWpfbCKSaz4wvqtqMwfmFkE58mTKxsRFG",
      "label": "Watch-only Address"
    }
  ]
}
```

### 响应 Response

返回一个标签模型数组。

| 字段       | 说明                 |
| -------- | ------------------ |
| walletId | 钱包的 id （亦是第一个接收地址） |
| address  | 要加上标签的比特币地址        |
| label    | 地址标签               |

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |

## 列出指定钱包的标签 List Labels For Specific Wallet

```shell
WALLET=2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf
curl -X GET \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/labels/$WALLET
```

```javascript
var wallets = bitgo.wallets();
var data = {
  "type": "bitcoin",
  "id": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
};
wallets.get(data, function callback(err, wallet) {
  if (err) {
    // 处理钱包
  }
  // 标签相关操作
  wallet.labels({}, function (err, labels) {
    if (err) {
      console.log(err);
      process.exit(-1);
    }
    console.dir(labels);
  });
});
```

获取钱包的标签列表

### HTTP 请求 HTTP Request

`GET /api/v1/labels/:walletId`

> 响应示例

```json
{
  "labels": [
    {
      "walletId": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
      "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
      "label": "My Favorite Multi-Sig Address"
    }
  ]
}
```

### 响应 Response

返回一个标签模型数组。

| 字段       | 说明                 |
| -------- | ------------------ |
| walletId | 钱包的 id （亦是第一个接收地址） |
| address  | 要加上标签的比特币地址        |
| label    | 地址标签               |

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |
| 404 Not Found    | 未能找到指定的钱包。  |

## 设置标签 Set Label

```shell
WALLET=2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf
ADDRESS=2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"label\": \"A Noteworthy Label\" }" \
https://test.bitgo.com/api/v1/labels/$WALLET/$ADDRESS
```

```javascript
var wallets = bitgo.wallets();
var data = {
  "type": "bitcoin",
  "id": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
};
wallets.get(data, function callback(err, wallet) {
  if (err) {
      console.log(err);
      process.exit(-1);
  }
  wallet.setLabel({label: "A Noteworthy Label", address: "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD"}, function (err, label) {
    if (err) {
      console.log(err);
      process.exit(-1);
    }
    console.dir(label);
  });
});
```

对指定地址设置一个标签并且将其和指定钱包关联。 标签的长度限制为 250 个字符。 标签不能被设置在钱包的第一个接收地址上 因为它被保留用做钱包的标签。

### HTTP 请求 HTTP Request

`PUT /api/v1/labels/:walletId/:address`

> 响应示例

```json
{
  "walletId": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
  "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
  "label": "A Noteworthy Label"
}
```

### URL 参数 URL Parameters

| 名称       | 类型             | 必需  | 说明                 |
| -------- | -------------- | --- | ------------------ |
| walletId | 比特币地址 (string) | YES | 钱包的 id （亦是第一个接收地址） |
| address  | 比特币地址 (string) | YES | 要加上标签的比特币地址        |

### PUT 参数 PUT Parameters

| 名称    | 类型     | 必需  | 说明   |
| ----- | ------ | --- | ---- |
| label | string | YES | 地址标签 |

### 响应 Response

返回一个标签模型对象。

| 字段       | 说明                 |
| -------- | ------------------ |
| walletId | 钱包的 id （亦是第一个接收地址） |
| address  | 要加上标签的比特币地址        |
| label    | 地址标签               |

### 错误 Errors

| 响应               | 说明          |
| ---------------- | ----------- |
| 400 Bad Request  | 请求参数缺失或有错误。 |
| 401 Unauthorized | 验证参数不符。     |
| 404 Not Found    | 未能找到指定的钱包。  |

## 删除标签 Delete Label

```shell
WALLET=2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf
ADDRESS=2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
curl -X DELETE \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/labels/$WALLET/$ADDRESS
```

```javascript
wallet.deleteLabel({address: "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD"}, function (err, label) {
  if (err) {
    // 错误处理
  }
  console.dir(label);
});
```

从指定的地址和钱包上删除标签。

### HTTP 请求 HTTP Request

`DELETE /api/v1/labels/:walletId/:address`

> 响应示例

```json
{
  "walletId": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
  "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
  "label": "A Noteworthy Label"
}
```

### URL 参数 URL Parameters

| 名称       | 类型             | 必需  | 说明                 |
| -------- | -------------- | --- | ------------------ |
| walletId | 比特币地址 (string) | YES | 钱包的 id （亦是第一个接收地址） |
| address  | 比特币地址 (string) | YES | 要加上标签的比特币地址        |

### 响应 Response

返回一个已被删除的标签模型对象。

| 字段       | 说明                 |
| -------- | ------------------ |
| walletId | 钱包的 id （亦是第一个接收地址） |
| address  | 要加上标签的比特币地址        |
| label    | 地址标签               |

### 错误 Errors

| 响应               | 说明           |
| ---------------- | ------------ |
| 400 Bad Request  | 请求参数缺失或有错误。  |
| 401 Unauthorized | 验证参数不符。      |
| 404 Not Found    | 未找到指定的钱包或标签。 |

# 标记 Tags

标记是企业客户的高级策略功能。

## 创建标记 Create a tag

### HTTP 请求 HTTP Request

`POST /api/v1/tag`

### BODY 参数 BODY Parameters

标记必须有一个名称并且正确的设置用户、钱包或企业三个参数之一。

| 参数         | 类型     | 必需  | 说明                             | 可能值 |
| ---------- | ------ | --- | ------------------------------ | --- |
| name       | string | YES | 标记的名称                          |     |
| user       | id     | NO  | 拥有标记的用户之用户 id，仅可设为添加此标记用户之 id。 |     |
| wallet     | id     | NO  | 拥有此标记的钱包之 id，不是比特币地址           |     |
| enterprise | id     | NO  | 拥有此标记的企业之 id。                  |     |

### 响应 Response

返回此标记。

```json
{
  "_id": "[id]",
  "name": "name of tag",
  "user": "[id]"
}
```

### 错误 Errors

| 响应              | 说明          |
| --------------- | ----------- |
| 400 Bad Request | 请求参数缺失或有错误。 |

## 为钱包添加标记 Add a tag to a wallet

### HTTP 请求 HTTP Request

`POST /api/v1/wallet/:wallet/tag`

### BODY 参数 BODY Parameters

你必需指定添加到钱包的标记 id，不需要其他参数。

| 参数  | 类型 | 必需  | 说明     | 可能值 |
| --- | -- | --- | ------ | --- |
| tag | id | YES | 标记的 id |     |

### 响应 Response

返回钱包、标记和受影响的 wallettxs 数量。

```json
{
  "wallet": "[id]",
  "tag": "[id]",
  "walletTxsAffected": 0
}
```

### 错误 Errors

| 响应            | 说明     |
| ------------- | ------ |
| 404 Not Found | 未找到该标记 |

# Webhook 通知 Webhook Notifications

> 交易 Webhook 回调示例

    POST http://your.server.com/webhook
    {
      "type": "transaction",
      "walletId": "2MwLxgWaAGmMT9asT4nAdeewWzPEz3Sn5Eg",
      "hash": "c9a55f32d4b63d02a617eea58873227e4f011d0dfc836cb2e9cab531c4db0c4a"
    }
    

> 待批准 webhook 回调示例

    POST http://your.server.com/webhook
    {
      "type": "pendingapproval",
      "pendingApprovalId": "55e79a1b5f9a20da1d3fe5b988a71c93",
      "walletId": "2MwLxgWaAGmMT9asT4nAdeewWzPEz3Sn5Eg",
      "state": "accepted"
    }
    

Webhook 可以已编程方式接收来自 BitGo 的回调（callback）。 这些可以被附加到钱包（在发生交易的情况下），或用户（用于区块通知）。 在指定的事件发生时会触发 Webhook 通知，如有传入交易。

BitGo 服务器会根据 JSON payload 中所定义的 URL 发起一个 POST http 请求，以期得到 `HTTP 200 OK` 响应。 若未能接收到成功响应，BitGo 将重试此 webhook 并将逐步延长每次重试之间的延迟。

开发者必须注意要确保自己的应用程序即使在网络传输发生错误时仍可继续，要不然会由于有问题的确认而二次都收到相同的 webhook 。

### 请求结构 Request Schema

下列 JSON 编码的参数位于 HTTP body中，将通过 Webhook URL 调用进行传输。

| 字段                | 说明                                                                                                                                  |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| type              | Webhook 的类型: 'transaction' (交易), 'transactionExpire' (交易过期), 'transactionRemoved' (交易已移除), 'block' (区块), 和 'pendingapproval' (待批准). |
| walletId          | 与 webhook 事件关联的钱包 ID，当这个为钱包 webhook 时适用。                                                                                            |
| hash              | 交易 ID，当这个是交易 webhook 时适用。                                                                                                           |
| pendingApprovalId | 待批准 ID，当这个是待批准 webhook 时适用。                                                                                                         |
| state             | 待批准的状态 (pending, approved, 或 rejected), 当这个是待批准 webhook 时适用。                                                                        |

### Webhook 类型 Webhook Types

BitGo 目前正致力于 webhook 方面开发。请联络我们以获取更多 webhook 类型。

| 类型                 | 说明                          |
| ------------------ | --------------------------- |
| transaction        | 当钱包的接收地址上有交易出现 / 已确认时激活。    |
| transactionRemoved | 当交易从用户的钱包被移除时激活。            |
| transactionExpire  | 当交易即将过期时激活。                 |
| pendingapproval    | 当与用户钱包有关的待批准交易被创建、批准或拒绝时激活。 |
| block              | 当比特币网络上有新区块出现时激活。           |

## 列出钱包的 Webhook List Wallet Webhooks

```shell
curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet/$WALLETID/webhooks
```

```javascript
bitgo.wallets().get({ "id": walletId }, function(err, wallet) {
    wallet.listWebhooks({}, function callback(err, result) {
        console.dir(result);
    });
});
```

> 响应示例

```json
[
    {
      "walletId": "2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu",
      "type": "transaction",
      "url": "http://www.yoursite.com/partner/webhooks"
    },
    {
      "walletId": "2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu",
      "type": "transaction",
      "url": "http://www.company2.com/api/webhooks",
      "numConfirmations": 3
    }
]
```

获取钱包上设置的 webhook 列表。 目前，只有交易（transaction）和待批准（pendingapproval）webhook 类型通知可被附加到钱包上。

### HTTP 请求 HTTP Request

`GET /api/v1/wallet/:walletId/webhooks`

### 响应 Response

Webhook 对象数组

| 字段       | 说明                         |
| -------- | -------------------------- |
| walletId | 钱包的 id                     |
| type     | Webhook 的类型， 如：transaction |
| url      | 回调请求使用的 http/https url     |

## 为钱包添加 Webhook Add Wallet Webhooks

```shell
WALLETID=2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu
URL='http://www.yoursite.com/partner/webhooks'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"url\": \"$URL\", \"type\": \"transaction\" }" \
https://test.bitgo.com/api/v1/wallet/$WALLETID/webhooks
```

```javascript
bitgo.wallets().get({ "id": walletId }, function(err, wallet) {
    wallet.addWebhook({ url: url, type: 'transaction' }, function callback(err, result) {
        console.dir(result);
    });
});
```

> 响应示例

```json
{
  "walletId": "2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu",
  "type": "transaction",
  "url": "http://www.yoursite.com/partner/webhooks"
}
```

添加 webhook 后当事件被触发时 BitGo 将对指定 URL 发起 HTTP 回调。 每个钱包的同类型 webhook 上限为 10 个。

有 2 种类型的钱包 webhook 可用： 1. 会被钱包上任意交易触发的交易（transaction）webhook。 2. 当传出交易触发钱包政策时会触发待批准（pending approval） webhook。

### HTTP 请求 HTTP Request

`POST /api/v1/wallet/:walletId/webhooks`

### 参数 Parameters

| 参数               | 类型      | 必需  | 说明                                                                   |
| ---------------- | ------- | --- | -------------------------------------------------------------------- |
| type             | string  | YES | Webhook 的类型，例如 transaction 或 pendingapproval                         |
| url              | string  | YES | 有效的 http/https url 供回调请求使用                                           |
| numConfirmations | integer | NO  | 在触发交易 webhook 前需要的确认数量。 若为 0 或未指定，则将在第一次见到此交易以及当此交易被确认时，对回调端点发出调用请求。 |

### 响应 Response

| 字段       | 必需                         |
| -------- | -------------------------- |
| walletId | 钱包的 id                     |
| type     | Webhook 的类型， 如：transaction |
| url      | 回调请求使用的 http/https url     |

## 移除钱包 Webhook Remove Wallet Webhooks

```shell
WALLETID=2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu
URL='http://www.yoursite.com/partner/webhooks'

curl -X DELETE \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"url\": \"$URL\", \"type\": \"transaction\" }" \
https://test.bitgo.com/api/v1/wallet/$WALLETID/webhooks
```

```javascript
bitgo.wallets().get({ "id": walletId }, function(err, wallet) {
    wallet.removeWebhook({ url: url, type: 'transaction' }, function callback(err, result) {
        console.dir(result);
    });
});
```

> 响应示例

```json
{
  "removed": 1
}
```

移除 Webhook 后，当发生指定类型的新事件时将不会再对你的 URL 发起 HTTP 回调。

### HTTP 请求 HTTP Request

`DELETE /api/v1/wallet/:walletId/webhooks`

### 参数 Parameters

| 参数   | 类型     | 必需  | 说明                          |
| ---- | ------ | --- | --------------------------- |
| type | string | YES | Webhook 的类型， 如：transaction  |
| url  | string | YES | 有效的 http/https url 以供回调请求使用 |

### 响应 Response

| 字段       | 说明                         |
| -------- | -------------------------- |
| walletId | 钱包的 id                     |
| type     | Webhook 的类型， 如：transaction |
| url      | 回调请求使用的 http/https url     |

## 列出用户 Webhook List User Webhooks

```shell
curl -X GET \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/webhooks
```

```javascript
bitgo.listWebhooks({}, function callback(err, result) {
  console.dir(result);
});
```

> 响应示例

```json
[
    {
        "type": "block",
        "coin": "bitcoin",
        "url": "https://303fe960.ngrok.com"
    },
    {
        "type": "block",
        "coin": "bitcoin",
        "url": "http://www.yoursite.com/partner/webhooks"
    }
]
```

获取附加于指定用户的 webhook 列表。目前，只有区块通知（block notification）webhook 类型可以被附加到用户上。

### HTTP 请求 HTTP Request

`GET /api/v1/webhooks`

### 响应 Response

Webhook 对象数组

| 字段   | 说明                                                    |
| ---- | ----------------------------------------------------- |
| type | Webhook 的类型，例如 block                                  |
| coin | string | NO | 网络令牌 例如 "bitcoin" 或 "eth" (默认为 bitcoin) |
| url  | 回调请求使用的 http/https url                                |

## 为用户添加 Webhook Add User Webhooks

```shell
URL='https://303fe960.ngrok.com'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"url\": \"$URL\", \"type\": \"block\", \"coin\": \"bitcoin\" }" \
https://test.bitgo.com/api/v1/webhooks
```

```javascript
bitgo.addWebhook({ url: url, type: 'block', coin: 'bitcoin' }, function callback(err, result) {
        console.dir(result);
    });
});
```

> 响应示例

```json
{
  "type": "block",
  "type": "bitcoin",
  "url": "http://www.yoursite.com/partner/webhooks"
}
```

添加 webhook 后，当事件被触发时 BitGo 将对指定 URL 发起 HTTP 回调。Webhook 记录会被附加到用户账号。

### HTTP 请求 HTTP Request

`POST /api/v1/webhooks`

### 参数 Parameters

| 参数   | 类型     | 必需  | 说明                                      |
| ---- | ------ | --- | --------------------------------------- |
| type | string | YES | Webhook 的类型，例如 block                    |
| coin | string | NO  | 网络令牌 例如 "bitcoin" 或 "eth" (默认为 bitcoin) |
| url  | string | YES | 有效的 http/https url 供回调请求使用              |

### 响应 Response

| 字段   | 说明                     |
| ---- | ---------------------- |
| type | Webhook 的类型，例如 block   |
| url  | 回调请求使用的 http/https url |

## 移除用户 Webhook Remove User Webhooks

```shell
URL='http://www.yoursite.com/partner/webhooks'

curl -X DELETE \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"url\": \"$URL\", \"type\": \"block\" }" \
https://test.bitgo.com/api/v1/webhooks
```

```javascript
bitgo.removeWebhook({ url: url, type: 'block' }, function callback(err, result) {
  console.dir(result);
});
```

> 响应示例

```json
{
  "removed": 1
}
```

移除 Webhook 后，当发生指定类型的新事件时将不会再对你的 URL 发起 HTTP 回调。

### HTTP 请求 HTTP Request

`DELETE /api/v1/webhooks`

### 参数 Parameters

| 参数   | 类型     | 必需  | 说明                          |
| ---- | ------ | --- | --------------------------- |
| type | string | YES | Webhook 的类型， 如：transaction  |
| url  | string | YES | 有效的 http/https url 以供回调请求使用 |

### 响应 Response

| 字段   | 说明                         |
| ---- | -------------------------- |
| type | Webhook 的类型， 如：transaction |
| url  | 回调请求使用的 http/https url     |

# 实用程序 Utilities

本节将介绍作为 BitGo API 一部分的实用程序服务。

## 解密 Decrypt

```javascript
var encryptedString = '{"iv":"n4zHXVTi/Go/riCP8fNs/A==","v":1,"iter":10000,"ks":256,"ts":64,"mode":"ccm","adata":"","cipher":"aes","salt":"zvLyve+4AJU=","ct":"gNMqheicMoD8ZmNzRwuQfWGAh+HA933l"}';
var decryptedString = bitgo.decrypt({ password: "password", input: encryptedString });
// decryptedString = "this is a secret"
```

```shell
仅作为本地方法可用 (BitGo Express)

PASSWORD='password'
INPUT='{\"iv\":\"n4zHXVTi/Go/riCP8fNs/A==\",\"v\":1,\"iter\":10000,\"ks\":256,\"ts\":64,\"mode\":\"ccm\",\"adata\":\"\",\"cipher\":\"aes\",\"salt\":\"zvLyve+4AJU=\",\"ct\":\"gNMqheicMoD8ZmNzRwuQfWGAh+HA933l\"}'

curl -X POST \
-H "Content-Type: application/json" \
-d "{ \"password\": \"$PASSWORD\", \"input\": \"$INPUT\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/decrypt

{ "decrypted" : "this is a secret" }
```

客户端函数，用于解密 BitGo API 加密的数据使用。

## 加密 Encrypt

```javascript
var encryptedString = bitgo.encrypt({ password: "password", input: "this is a secret" });
// encyrptedString = "{\"iv\":\"n4zHXVTi/Go/riCP8fNs/A==\",\"v\":1,\"iter\":10000,\"ks\":256,\"ts\":64,\"mode\":\"ccm\",\"adata\":\"\",\"cipher\":\"aes\",\"salt\":\"zvLyve+4AJU=\",\"ct\":\"gNMqheicMoD8ZmNzRwuQfWGAh+HA933l\"}"
```

```shell
仅作为本地方法可用 (BitGo Express)

PASSWORD='password'
INPUT='this is a secret'

curl -X POST \
-H "Content-Type: application/json" \
-d "{ \"password\": \"$PASSWORD\", \"input\": \"$INPUT\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/encrypt

{ "encrypted" : "{\"iv\":\"U4uRz85ytmlCeTe2P3iOmg==\",\"v\":1,\"iter\":10000,\"ks\":256,\"ts\":64,\"mode\":\"ccm\",\"adata\":\"\",\"cipher\":\"aes\",\"salt\":\"zvLyve+4AJU=\",\"ct\":\"/tRnUh9LUyI7L5e5LPqpvnPR7RD1CdUi\"}" }
```

客户端函数用于加密一个字符串。所有存放于 BitGo 的数据均由此 API 进行加密。

## 预估交易费用 Estimate Transaction Fees

```javascript
bitgo.estimateFee({ numBlocks: 6 }, function callback(err, res) {
  console.dir(res);
});
```

```shell
curl -k https://www.bitgo.com/api/v1/tx/fee?numBlocks=6
```

返回建议的每千字节费率以确认目标区块数量的交易。 本功能可在创建交易时使用。 注意：预估算法仅在生产环境（www.bitgo.com）中准确并且需要至少 2 个区块在前。

### HTTP 请求 HTTP Request

`GET /api/v1/tx/fee?numBlocks=6`

> 响应示例

```json
{ "feePerKb": 21949,
    "numBlocks": 6,
    "confidence": 95,
    "multiplier": 1,
    "feeByBlockTarget": {
        "1": 62246,
        "2": 47730,
        "3": 35130,
        "4": 32837,
        "5": 32837,
        "6": 21949,
        "7": 20017,
        "8": 18644,
        "9": 16545,
        "10": 16545
    }
}
```

### 响应 Response

| 字段         | 说明                                     |
| ---------- | -------------------------------------- |
| confidence | BitGo 对此预估的信心。这个值低于 85 时不要使用预估结果。      |
| feePerKb   | 要使用的每千字节费率（单位：聪）                       |
| multiplier | BitGo 用于计算 feePerKb 的倍数值。仅作为信息 - 请勿使用。 |
| numBlocks  | 请求此次预估的目标区块数量                          |

## 市场价格数据 Market Price Data

```javascript
bitgo.markets().latest({}, function callback(err, market) {
  if (err) {
    // 处理错误
  }
  // 其他
});
```

```shell
curl -k https://test.bitgo.com/api/v1/market/latest
```

获取当前市场的信息。

### HTTP 请求 HTTP Request

`GET /api/v1/market/latest`

> 市场模型响应示例

```json
    {  
      "latest":{  
        "_id":"2017-01-03T00:00:00.000Z",
        "currencies":{  
          "cacheTime":1483464065150,
          "ZAR":{  
            "24h_avg":14166.72,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":14233.45,
            "bid":14227.79,
            "ask":14241.92,
            "cacheTime":1483461006210,
            "monthlyLow":10388.73,
            "monthlyHigh":14265.1,
            "prevDayLow":13742.4,
            "prevDayHigh":14265.1,
            "lastHourLow":14126.03,
            "lastHourHigh":14232.02
          },
          "USD":{  
            "24h_avg":1027.58,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":1032.42,
            "bid":1032.01,
            "ask":1033.03,
            "cacheTime":1483461006231,
            "monthlyLow":753.08,
            "monthlyHigh":1037.5,
            "prevDayLow":1003.66,
            "prevDayHigh":1037.5,
            "lastHourLow":1024.91,
            "lastHourHigh":1032.6
          },
          "INR":{  
            "24h_avg":70270.03,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":70601.02,
            "bid":70572.95,
            "ask":70643.03,
            "cacheTime":1483461004821,
            "monthlyLow":51327.43,
            "monthlyHigh":70797.84,
            "prevDayLow":68392.91,
            "prevDayHigh":70797.84,
            "lastHourLow":70104.36,
            "lastHourHigh":70630.36
          },
          "GBP":{  
            "24h_avg":838.49,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":842.44,
            "bid":842.1,
            "ask":842.94,
            "cacheTime":1483461016786,
            "monthlyLow":591.95,
            "monthlyHigh":843.7,
            "prevDayLow":816.89,
            "prevDayHigh":843.7,
            "lastHourLow":836.5,
            "lastHourHigh":842.77
          },
          "EUR":{  
            "24h_avg":986.6,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":991.25,
            "bid":990.86,
            "ask":991.84,
            "cacheTime":1483461001800,
            "monthlyLow":703.68,
            "monthlyHigh":996.2,
            "prevDayLow":959.52,
            "prevDayHigh":996.2,
            "lastHourLow":986.51,
            "lastHourHigh":993.91
          },
          "CNY":{  
            "24h_avg":7152.68,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":7186.37,
            "bid":7183.51,
            "ask":7190.64,
            "cacheTime":1483462551515,
            "monthlyLow":5183.77,
            "monthlyHigh":7207.67,
            "prevDayLow":6970.52,
            "prevDayHigh":7207.67,
            "lastHourLow":7134.19,
            "lastHourHigh":7187.72
          },
          "CAD":{  
            "24h_avg":1381.13,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":1387.63,
            "bid":1387.08,
            "ask":1388.46,
            "cacheTime":1483461014145,
            "monthlyLow":1002.36,
            "monthlyHigh":1392.95,
            "prevDayLow":1348.37,
            "prevDayHigh":1392.95,
            "lastHourLow":1375.99,
            "lastHourHigh":1386.31
          },
          "AUD":{  
            "24h_avg":1421.25,
            "total_vol":68474.29,
            "timestamp":1483464063,
            "last":1427.95,
            "bid":1427.38,
            "ask":1428.8,
            "cacheTime":1483461004781,
            "monthlyLow":1013.22,
            "monthlyHigh":1443.58,
            "prevDayLow":1393.18,
            "prevDayHigh":1443.58,
            "lastHourLow":1418.88,
            "lastHourHigh":1429.53
          }
        },
        "__v":0
      }
    }
```

### 响应 Response

返回一个市场模型对象。所有价格均以用户设置的货币计价。

| 字段          | 说明                |
| ----------- | ----------------- |
| 24h_avg | 最近 24 小时内的平均市场价 |
| total_vol | 24 小时比特币交易量 |
| timestamp | 数据搜集时间的 Unix 时间戳 |
| last        | 最新市场价格            |
| bid         | 当前最高买盘价           |
| ask         | 当前最高卖盘价           |
| cachetime | BitGo 内部使用的值    |
| monthlyLow | 月最低市场价    |
| monthlyHigh | 月最高市场价    |
| prevDayLow | 前一日的最低市场价    |
| prevDayHigh | 前一日的最高市场价    |
| lastHourLow | 最近一小时的最低市场价  |
| lastHourHigh | 最近一小时的最高市场价 |

## 恶意软件地址列表 Malware Address List

```shell
curl https://www.bitgo.com/api/v1/malware/bitcoin
```

BitGo 维护的一个地址列表包含有已知被用于地址交换恶意软件的地址。 BitGo 将拒绝 联合签名发往这些地址的交易。 本 API 将返回当前被 BitGo 屏蔽的地址。 我们建议定期获取这个列表（比如每天一次），用于维护 你自己的屏蔽列表。 不然你可能会意外提交有问题的交易到 BitGo，比如你的 某个用户被恶意软件感染且请求提款。

### HTTP 请求 HTTP Request

`GET /api/v1/malware/bitcoin`

> 响应示例

```json
{
  "readme": "This is a list of addresses known by BitGo to be tied to bitcoin-stealing malware. BitGo will not co-sign transactions going to these addresses. It is recommended to periodically query this API to update your own internal list of bad addresses, in order to prevent transaction failures.",
  "addresses": [
    {
      "address": "19ZM2pjq6U4jVb283GZkCPNukjeyb2YZ2u"
    },
    {
      "address": "1EZV9ghJ4rCvwtVE27ZUxY6she4GzeEzCf"
    }
  ]
}
```

### 响应 Response

| 字段        | 说明                    |
| --------- | --------------------- |
| readme    | 本列表的说明以供阅读            |
| addresses | { address: xxx } 对象列表 |

## 验证比特币地址 Verify Bitcoin Address

```javascript
var isValid = bitgo.verifyAddress({ address: "1AJbsFZ64EpEfS5UAjAfcUG8pH8Jn3rn1F" });
```

```shell
仅作为本地方法可用 (BitGo Express)

curl -X POST \
-H "Content-Type: application/json" \
-d '{ "address": "2NFasKP9nqHHUBvVHvEGkuHpNA3hjdkUhAV" }' \
http://$BITGO_EXPRESS_HOST:3080/api/v1/verifyaddress
```

客户端函数用于验证给定的字符串是否为有效的比特币地址。同时支持 v1 地址 (例如 "1...") 和 P2SH 地址 (例如 "3...").

若地址有效返回 true。

## BitGo 客户端版本 BitGo Client Version

```javascript
var version = bitgo.version();
```

客户端函数用于获取 BitGo SDK 之版本。

返回一个字符串。

# 区块链数据 Blockchain Data

BitGo 提供的公开 API 用于获取地址和交易上的区块链数据。 这些 API 与 BitGo 用户或钱包的概念没有关联。 本 API 端点的用途在于允许 API 客户获取非 BitGo 地址和交易的数据（类似于 Satoshi 客户端中 txindex 和 watchonly 的概念）。

对大部分钱包用例而言，开发者会去使用钱包和区块链 API。 它们支持更多的操作，诸如检查多个 HD 钱包的合并余额，创建地址，发送交易等等。<aside class="success"> 由于仅返回公开的区块链数据，因此本区块链数据 API 不需要验证身份。 不过仍然建议在头部发送访问令牌，以获得更高的频率限制。 </aside> 

## 获取地址 Get Address

查找地址的余额信息。

```shell
ADDRESS=2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
curl https://test.bitgo.com/api/v1/address/$ADDRESS
```

```javascript
var address = "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD";
bitgo.blockchain().getAddress({ address: address }, function(err, response) {
  if (err) { console.log(err); process.exit(-1); }
  console.dir(response);
});
```

### HTTP 请求 HTTP Request

`GET /api/v1/address/:address`

### URL 参数 URL Parameters

| 参数      | 类型             | 必需  | 说明    |
| ------- | -------------- | --- | ----- |
| address | 比特币地址 (string) | YES | 比特币地址 |

> 响应示例

```json
{
  "address": "2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb",
  "balance": 6900000,
  "confirmedBalance": 6900000
}
```

### 响应 Response

返回地址的汇总信息。

| 字段               | 说明             |
| ---------------- | -------------- |
| address          | 地址             |
| balance          | 余额，包括 0 确认的交易。 |
| confirmedBalance | 已确认余额          |

### 错误 Errors

| 响应              | 说明          |
| --------------- | ----------- |
| 400 Bad Request | 请求参数缺失或有错误。 |
| 404 Not Found   | 指定的地址未找到。   |

## 获取地址的交易 Get Address Transactions

获取指定地址的交易，按区块高度逆序排列。

```shell
ADDRESS=2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
curl https://test.bitgo.com/api/v1/address/$ADDRESS/tx
```

```javascript
var address = "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD";
bitgo.blockchain().getAddressTransactions({address: address}, function(err, response) {
  if (err) { console.log(err); process.exit(-1); }
  console.log(JSON.stringify(response, null, 4));
});
```

### HTTP 请求 HTTP Request

`GET /api/v1/address/:address/tx`

### URL 参数 URL Parameters

| 参数      | 类型             | 必需  | 说明    |
| ------- | -------------- | --- | ----- |
| address | 比特币地址 (string) | YES | 比特币地址 |

### QUERY 参数 QUERY Parameters

| 参数    | 类型     | 必需 | 说明                          |
| ----- | ------ | -- | --------------------------- |
| skip  | number | NO | 列表的初始索引号。默认值为 0。            |
| limit | number | NO | 单次调用返回结果的最大数量（默认=25，最大=250） |

> 响应示例

```json
{
    "transactions": [
        {
            "id": "951aea423c6ba55ac4e6aba953c1dc08e4854bcdf07cb505c4c69447a3f9712e",
            "caption": "test sending",
            "date": "2014-11-13T01:47:10.000Z",
            "pending": false,
            "entries": [
                {
                    "account": "2MxrfrSPNhj1yVrC1CqyNRMFR8WtM7XxpS7",
                    "value": 1890000
                },
                {
                    "account": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
                    "value": -6900000
                },
                {
                    "account": "2N91XzUxLrSkfDMaRcwQhe9DauhZMhUoxGr",
                    "value": 5000000
                }
            ]
        },
        {
            "id": "c7e823fe39d1f0a4081bbefe53f67ebf117189b43a92da3225aa2e4247e35c68",
            "date": "2014-11-13T01:07:05.000Z",
            "pending": false,
            "entries": [
                {
                    "account": "muqrFWxvQiN6KTSZcUugxivMre2fCHnFnF",
                    "value": -104350000
                },
                {
                    "account": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
                    "value": 6900000
                },
                {
                    "account": "mrGtgBbfp5jnqK6c4QNcad6WdHYQr67W8N",
                    "value": 97440000
                }
            ]
        }
    ],
    "start": 0,
    "count": 2,
    "total": 2
}
```

### 响应 Response

返回一个交易对象数组。每个交易的汇总信息 包含有本交易所涉及的任何比特币地址的净余额产生的影响。

### 错误 Errors

| 响应              | 说明          |
| --------------- | ----------- |
| 400 Bad Request | 请求参数缺失或有错误。 |

## 获取地址的未花费输出 Get Address Unspent Outputs

获取指定目标地址上的未花费输出。按区块高度降序排列（未确认的交易在先）。

```shell
ADDRESS=2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
curl https://test.bitgo.com/api/v1/address/$ADDRESS/unspents
```

```javascript
var address = "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD";
bitgo.blockchain().getAddressUnspents({address: address}, function(err, response) {
  if (err) { console.log(err); process.exit(-1); }
  console.log(JSON.stringify(response, null, 4));
});
```

### HTTP 请求 HTTP Request

`GET /api/v1/address/:address/unspents`

### URL 参数 URL Parameters

| 参数      | 类型             | 必需  | 说明    |
| ------- | -------------- | --- | ----- |
| address | 比特币地址 (string) | YES | 比特币地址 |

> 响应示例

```json
{
    "pendingTransactions": false,
    "unspents": [
        {
            "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
            "blockHeight": 308023,
            "confirmations": 85670,
            "date": "2015-04-15T23:31:34.918Z",
            "script": "a9147bd4d50e34791a6373bfa0175bf2cb9796a08f5587",
            "tx_hash": "777cb752b4ecaf84fc3716cc5790cd84d7481764b40d496d81d997e04112818d",
            "tx_output_n": 0,
            "value": 3200000
        },
        {
            "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
            "blockHeight": 308749,
            "confirmations": 84944,
            "date": "2015-04-15T23:31:23.919Z",
            "script": "a9147bd4d50e34791a6373bfa0175bf2cb9796a08f5587",
            "tx_hash": "6a75bda7016698abcd903b4b005b9ba306324a8d50bee932237ffde50a5a9eba",
            "tx_output_n": 0,
            "value": 100000
        }
    ]
}
```

### 响应 Response

返回一个未花费交易对象数组。每笔交易包含下列信息

| 名称            | 类型     | 说明              |
| ------------- | ------ | --------------- |
| address       | string | 有未花费输出的地址       |
| tx_hash       | number | 未花费输出的金额（单位：聪）  |
| tx_output_n | number | 未花费输出的金额（单位：聪）  |
| value         | number | 未花费输出的金额（单位：聪）  |
| blockheight   | number | 这个交易出现的高度       |
| confirmations | number | 这个交易的确认数量       |
| date          | date   | 这个交易出现在网络上的日期时间 |
| script        | string | 输出比特币脚本的十六进制格式  |

### 错误 Errors

| 响应              | 说明          |
| --------------- | ----------- |
| 400 Bad Request | 请求参数缺失或有错误。 |

## 获取交易详细信息 Get Transaction Details

```shell
TX=af867c86000da76df7ddb1054b273ca9e034e8c89d049b5b2795f9f590f67648
curl https://test.bitgo.com/api/v1/tx/$TX
```

```javascript
var txId = 'af867c86000da76df7ddb1054b273ca9e034e8c89d049b5b2795f9f590f67648';
bitgo.blockchain().getTransaction({id: txId}, function(err, response) {
  if (err) { console.log(err); process.exit(-1); }
  console.log(JSON.stringify(response, null, 4));
});
```

获取指定交易散列值的详细信息

### HTTP 请求 HTTP Request

`GET /api/v1/tx/:txid`

### URL 参数 URL Parameters

| 参数   | 类型     | 必需  | 说明          |
| ---- | ------ | --- | ----------- |
| txid | string | YES | 交易 ID （散列值） |

> 响应示例

```json
{
  "blockhash": "000000009249e7d725cc087cb781ade1dbfaf2bd777822948d5fccd4044f8299",
  "confirmations": 16679,
  "date": "2014-11-06T02:22:55.000Z",
  "entries": [
    {
      "account": "2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov",
      "value": 84000000
    },
    {
      "account": "msj42CCGruhRsFrGATiUuh25dtxYtnpbTx",
      "value": -85900000
    },
    {
      "account": "mwahoJcaVuy2TiMtGDZV9PaujFeD9z1a1q",
      "value": 1890000
    }
  ],
  "fee": 10000,
  "height": 306695,
  "hex": "0100000001b6e8b36132d351b3d66b5452d8f4601e2271a7bb52b644397db956a4ffe2a053000000006a4730440220127c4adc1cf985cd884c383e69440ce4d48a0c4fdce6bf9d70faa0ee8092acb80220632cb6c99ded7f261814e602fc8fa8e7fe8cb6a95d45c497846b8624f7d19b3c012103df001c8b58ac42b6cbfc2223b8efaa7e9a1911e529bd2c8b7f90140079034e75ffffffff0200bd01050000000017a914c449a7fafb3b13b2952e064f2c3c58e851bb943087d0d61c00000000001976a914b0379374df5eab8be9a21ee96711712bdb781a9588ac00000000",
  "id": "af867c86000da76df7ddb1054b273ca9e034e8c89d049b5b2795f9f590f67648",
  "inputs": [
    {
      "previousHash": "53a0e2ffa456b97d3944b652bba771221e60f4d852546bd6b351d33261b3e8b6",
      "previousOutputIndex": 0
    }
  ],
  "outputs": [
    {
      "account": "2NB96fbwy8eoHttuZTtbwvvhEYrBwz494ov",
      "value": 84000000,
      "vout": 0
    },
    {
      "account": "mwahoJcaVuy2TiMtGDZV9PaujFeD9z1a1q",
      "value": 1890000,
      "vout": 1
    }
  ],
  "pending": false
}
```

### 响应 Response

返回交易的详细信息，包括此交易所涉及的所有比特币地址的净值。

## 获取区块 Get Block

```shell
BLOCK=00000000000000066fff8a67fbb6fac31e9c4ce5b1eabc279ce53218106aa26a
curl https://test.bitgo.com/api/v1/block/$BLOCK
```

```javascript
var blockId = '00000000000000066fff8a67fbb6fac31e9c4ce5b1eabc279ce53218106aa26a';
bitgo.blockchain().getBlock({id: blockId}, function(err, response) {
  if (err) { console.log(err); process.exit(-1); }
  console.log(JSON.stringify(response, null, 4));
});
```

获取比特币区块和该区块的交易。你可以使用 'latest' 以获取比特币网络上最新的区块。

### HTTP 请求 HTTP Request

`GET /api/v1/block/latest`

`GET /api/v1/block/:blockHeight`

`GET /api/v1/block/:blockHash`

### URL 参数 URL Parameters

| 参数       | 类型           | 必需  | 说明                                               |
| -------- | ------------ | --- | ------------------------------------------------ |
| id       | variable（多种） | YES | 区块散列值 (string), 高度 (number) 或 'latest' 以获取最新的区块。 |
| extended | boolean      | NO  | 设为 true 以返回区块内每笔交易的详细信息。                         |

> 响应示例

```json
{
    "chainWork": "60359949399610308617",
    "date": "2015-04-15T23:05:50.139Z",
    "fees": 21226,
    "height": 326945,
    "id": "00000000000000066fff8a67fbb6fac31e9c4ce5b1eabc279ce53218106aa26a",
    "merkleRoot": "0b9c0bf5193fece523780bf92e3ad05025371a3d86987005a7316c35c507dcc3",
    "nonce": 924308913,
    "previous": "00000000eecd159babde9b094c6dbf1f4f63028ba100f6f092cacb65f04afc46",
    "value": 66640872300,
    "version": 2,
    "transactions": [
        "e393422e5a0b4c011f511cf3c5911e9c09defdcadbcf16ceb12a47a80e257aaa",
        "fe429dd68ef56613a038238e81b19e2158ef3ad9d9535d1127018bb78ff83537",
        "e1ee8183626b7854c80563d809f85acc6c7cd878de599bee291d0f759a7b2264",
        "dee33ab621e5409c65948d330f01e1adadb533b0f726d80d48e20bb434b4b542",
        "7d83ee2d59e06bc7efe49c647289d98842a7e9ec3b90c3cc965a81df08c08c8f"
    ]
}
```

### 响应 Response

| 名称           | 类型       | 说明                    |
| ------------ | -------- | --------------------- |
| date         | datetime | 在网络上见到此区块时的时间戳。       |
| id           | string   | 区块的散列值                |
| previous     | string   | 区块链中前一个区块的散列值         |
| transactions | array    | 区块里的交易散列值数组 (strings) |

# BitGo 即时交易 BitGo Instant

通过 BitGo 即时交易发送链上交易可以即时存入接收者账户，带有 BitGo 的防止双重花费财务保障。 任何人均可接收 BitGo 即时交易。 要发送 BitGo 即时 交易，你需要 BitGo KRS 钱包，或者与 BitGo 签署担保协议。

## 接收 Receiving

要使 BitGo 即时交易即时到帐，你需要遵守从 [列出钱包的交易](#list-wallet-transactions) 和 [获取钱包的交易](#get-wallet-transaction) API 返回的交易对象中的 **instant: true** 属性。 即时交易亦会有一个字段 **instantId** 可被用于在交易中 [获取即时交易保障](#get-instant-guarantee)。

## 发送 Sending

首先你需要一个兼容 BitGo 即时交易服务的钱包。 可以通过在网页界面中创建 KRS 钱包 ，或使用 [创建钱包 API](#create-wallet-with-keychains) 并指定 **backupXpubProvider** 参数。 若你已有非 KRS 钱包，可通过与 BitGo 签署担保协议以升级为支持 BitGo 即时服务。

要发送 BitGo 即时交易，使用任何交易 API 中的 **instant: true** 标识， 比如 [发送比特币到地址](#send-coins-to-address) 或 [创建交易](#create-transaction). BitGoD 也具有通过自身的 JSON 接口发送 BitGo 即时交易的能力。

BitGo 即时交易对将要花费的传入深度有更为严格的要求。 也就是说钱包可以被用于发送 BitGo 即时交易的余额可能会低于钱包的总余额。 通过 [获取钱包 API](#get-wallet) 返回的钱包对象的 **instantBalance** 属性会告诉你可用于发送 BitGo 即时交易的可用余额。

当发送 BitGo 即时交易时，若你的钱包中没有足够的已确认未花费币或者此交易将超过你钱包支持的风险限额，则交易可能会失败。 风险限额由担保抵押数额或 BitGo 应用到所有特定 KRS 钱包上的风险限额决定。 当发送 BitGo 即时交易时，你需要自行管理可能发生的失败，也许需要以标准交易进行重试。 

对我们的标准事务性计划用户，包括批量折扣计划用户 BitGo 即时交易无需任何附加费用。

# 合作伙伴 OAuth Partner OAuth

BitGo 的合作伙伴可以通过使用我们的 OAuth 端点以获取访问授权并以第三方 BitGo 账号的身份进行操作。 BitGo 遵循 OAuth 标准以允许安全的访问客户账号同时保证密码的安全。

要使用此功能，合作伙伴必须联络我们以取得 OAuth 应用程序参数。典型的 OAuth 流程如下：

  1. 你将用户重定向到我们的 OAuth 入口 `https://www.bitgo.com/oauth/authorize` 并进行登录。该请求的参数中，由你指定客户端 ID、重定向 uri 和范围。
  2. 用户到达 BitGo OAuth 入口。我们将询问他们是否同意允许你获取所请求范围的访问权限。他们使用自己的密码和两步验证登录并确认。
  3. 我们重定向用户到你的重定向 Uri，并带有代码参数。此验证代码仅由你的客户端 ID 使用时有效。
  4. 你回传验证代码到你的服务器并使用此代码、客户端 ID 和 密钥对 BitGo 服务器发起请求。我们将用此交换访问令牌。
  5. 你在 Authorization 头中使用此访问令牌以该 BitGo 用户的身份进行 API 调用。

### OAuth 变量 OAuth Variables

| 名称            | 说明                                                                       |
| ------------- | ------------------------------------------------------------------------ |
| Client Id     | 需要获取第三方账户访问的 OAuth 应用程序的字符串（名称）。这个是公开的。                                  |
| Client Secret | 一个加密字符串，存储在 OAuth 客户的服务器上，用于转换用户 ID 的验证代码以获取访问令牌。                        |
| Redirect Uris | 一个可被接受的重定向 URI 列表。当你将用户导向到我们的 OAuth 入口进行验证后，我们将把他们重定向回到你网站的 URI 并带有验证代码。 |
| Scope         | OAuth 范围列表。这是你的应用程序将被允许请求的用户权限。                                          |

### 范围取值 Scope Values

范围取值定义了 OAuth 会话中允许的操作。 这些范围将被提供给 BitGo OAuth 入口，因此 BitGo 会告知用户你的目的并指派给你带有所请求范围的合适的验证代码。

指定范围请使用空格分隔，例如 "openid profile wallet_view_enterprise wallet_spend_enterprise"。

注意更强大的范围不包含基本的范围，也就是说 wallet_spend 不包含 wallet_view，所以你可能要同时请求这两个。

| OAuth 范围值                  | 允许的动作说明                      |
| -------------------------- | ---------------------------- |
| openid                     | 验证用户是否已登录并获取其用户 ID           |
| profile                    | 获取用户配置文件，包括电子邮件和电话号码         |
| wallet_create              | 以用户的身份创建钱包                   |
| wallet_view_enterprise   | 查看由你的企业创建的钱包                 |
| wallet_spend_enterprise  | 从你的企业创建的钱包中花费比特币             |
| wallet_manage_enterprise | 管理和修改你的企业创建的钱包的设置            |
| wallet_view:#WALLETID      | 查看钱包的交易和地址                   |
| wallet_spend:#WALLETID     | 从指定钱包花费比特币                   |
| wallet_manage:#WALLETID    | 管理和修改指定钱包的设置                 |
| wallet_view_all          | 查看该用户有权访问的所有钱包的交易和地址         |
| wallet_freeze_#WALLETID  | 冻结指定钱包的全部花费动作一段时间（默认值为 1 小时） |
| wallet_freeze_all        | 冻结该用户所有钱包的全部花费动作             |

## 第三方 BitGo 登录 3rd Party BitGo Login

> OAuth 入口重定向示例以引导你的用户到 BitGo OAuth

    https://test.bitgo.com/oauth/authorize?client_id=FBExchange&redirect_uri=https%3A%2F%2Ffbexchange.com%2Foauth_redirect&scope=openid%20profile%20wallet_view_enterprise&email=test@bitgo.com&signup=false
    

OAuth 流程的第一步是使用客户端 ID 、范围和重定向 Uri 把你的用户重定向到 BitGo OAuth 入口。

* 测试端点： https://test.bitgo.com/oauth/authorize
* 生产环境端点： https://www.bitgo.com/oauth/authorize

参数可通过 GET 或 POST 进行发送。

### OAuth 请求参数 OAuth Request Parameters

> 示例将你的用户重定向回你的网站

    https://fbexchange.com/oauth_redirect?code=440261e26512877b7ebe86e2740da3030d81e88e
    

| 参数           | 必需  | 说明                                        |
| ------------ | --- | ----------------------------------------- |
| client_id    | YES | 需要请求第三方帐号访问权限的 OAuth 应用程序的名称              |
| redirect_uri | YES | 重定向 Uri 供 BitGo 在用户验证后将其跳转回你的网站           |
| scope        | YES | 请求的 OAuth 范围之列表，用空格分隔。你对用户信息的访问权限取决于这些范围。 |
| state        | NO  | 掩码包含有任何你想要提供的自定义信息。返回时作为重定向 Uri 的参数发送。    |
| signup       | NO  | 二进制值用于控制用户访问 BitGo OAuth 入口时的默认值是登录或注册。   |
| email        | NO  | 电子邮件用户名的字符串值，用于预设                         |
| force_email  | NO  | 若设为 true，email 字段（见上方）将对用户的客户端为只读。        |

### 我们的服务器将重定向 Our server will redirect

当用户验证了你的应用程序后，我们将重定向他们回你的 URL。此重定向将包含 URL 参数：

| 参数   | 说明                            |
| ---- | ----------------------------- |
| code | 验证代码字符串你可用于（和你的密钥一起）换取一个访问令牌。 |

## 获取访问令牌 Obtaining Access Tokens

```javascript
var bitgo = new BitGoJS.BitGo({clientId:clientId, clientSecret:clientSecret});
var authorizationCode = '440261e26512877b7ebe86e2740da3030d81e88e';
bitgo.authenticateWithAuthCode({ authCode: authorizationCode }, function(err, result) {
if (err) {
    console.dir(err);
    throw new Error("Could not auth!");
  }
  console.dir(result);
  bitgo.me({}, function(err, response) {
    if (err) {
      console.dir(err);
      throw new Error("Could not get user!");
    }
    console.dir(response);
  });
});
```

```shell
AUTHORIZATION_CODE='440261e26512877b7ebe86e2740da3030d81e88e'
CLIENT_ID='FBExchange'
CLIENT_SECRET='testclientsecret'
curl -X POST https://test.bitgo.com/oauth/token \
-H "Content-Type: application/json" \
-d  "{ \"client_id\": \"$CLIENT_ID\",
       \"client_secret\": \"$CLIENT_SECRET\",
       \"grant_type\": \"authorization_code\",
       \"code\":\"$AUTHORIZATION_CODE\"
    }"
```

当你的用户收到验证代码后，将其发送到你的服务后端用于以该用户身份进行操作。

然后你需要用验证代码换取访问令牌，这样你便可以使用 Api 的其他部分。

* 测试端点：https://test.bitgo.com/oauth/token
* 生产环境端点：https://www.bitgo.com/oauth/token

### OAuth 令牌请求参数 OAuth Token Request Parameters

| 参数            | 说明                                     |
| ------------- | -------------------------------------- |
| client_id     | 需要请求第三方帐号访问权限的 OAuth 应用程序的字符串（名称）      |
| client_secret | 加密字符串，存于 OAuth 应用程序的服务器上，由 BitGo 签发给你。 |
| grant_type    | 应为 'authorization_code'                |
| code          | 你从前述的用户登录步骤中获得的验证代码                    |

> 响应示例

```json
{
    "token_type":"bearer",
    "access_token":"2dba5167e70d4c18679a9775c57b184951a4fa07",
    "expires_in":3600,
    "expires_at":1418059789,
    "refresh_token":"3f8aa90479b4e3f0ea4544ed302e3bfe91968581",
    "id_token":"eyJ0eXAiOiJIUzI1NiJ96js46Ngvk-uC10YjYcEa4CqIAe-1hX2hYgXq6my...."
}
```

### OAuth 响应 OAuth Response

使用此 API 我们的服务器将返回一个访问令牌。

该令牌必须作为 HTTP 头添加到所有 API 请求的HTTP "Authorization" header：

`Authorization: Bearer <你的令牌>`

| 参数            | 说明                                                 |
| ------------- | -------------------------------------------------- |
| token_type    | 令牌的类型 如“bearer”                                    |
| access_token  | 将被用于 Authorization header 中的令牌供随后的 API 请求使用以代表用户身份 |
| expires_in    | 令牌有效的秒数                                            |
| expires_at    | 令牌的过期时间，从 1970 年起的秒数。                              |
| refresh_token | 若你的会话将要过期时，可被用于获取另一个访问令牌                           |
| id_token      | openid jwt 令牌包含有用户配置文件信息，若这个是作为范围请求的。              |

## OpenID JSON Web 令牌 OpenID JSON Web Token

若合作伙伴验证请求有 openid 配置文件空间，则返回的 id_token 将会是 base64 编码格式的 JSON Web 令牌（JWT），以回应之前的 OAuth 访问令牌请求。 你应该使用你的客户端密钥验证这个 JSON web 令牌是否是使用 HS256 算法签名的（以确保其来自 BitGo）。

此令牌将含有用户配置文件信息。你需要确保安全的存放此信息以防被诈欺或网络钓鱼者利用。

> 解密的 id_token 示例（来自上文中的访问令牌响应）

```json
{
    "phone_number": "+14085551234",
    "sub": "54583af457fd213c2d00000532cc8e44e6bdf943559be179e9475cfe",
    "phone_number_verified": true,
    "iss": "http://dev-accounts.bitgo.com",
    "email_verified": false,
    "zoneinfo": "US/Pacific",
    "access_token": "09d3dc7a41c86ceff09e383acda4840840bf056d",
    "exp": 1416882424,
    "iat": 1416878824,
    "email": "tester@something.com",
    "aud": "TESTCLIENTID"
}
```

### ID 令牌索取 ID Token Claims

| 参数                      | 格式         | 说明                             |
| ----------------------- | ---------- | ------------------------------ |
| iat                     | UTC 秒数     | 此令牌创建的时间，从 1970 年起的秒数。         |
| exp                     | UTC 秒数     | 令牌的过期时间（到此时间前有效），从 1970 年起的秒数。 |
| aud                     | string     | 客户端 ID                         |
| iss                     | uri string | 签发识别器                          |
| sub                     | string     | BitGo 的唯一用户 id                 |
| access_token            | string     | 从这个请求接收到的访问令牌，用于验证用途。          |
| email                   | string     | 用户的电子邮件                        |
| email_verified          | boolean    | 用户的电子邮件是否已验证                   |
| phone_number            | string     | 用户电话号码                         |
| phone_number_verified | boolean    | 用户的电话号码是否已验证                   |
| zoneinfo                | TZ         | 时区信息，如 US/pacific              |

## 刷新访问令牌 Refreshing Access Tokens

```javascript
// var refreshToken = 'undefined'; // 若未设置，使用上次验证请求中保存的刷新令牌。
var refreshToken = '3f8aa90479b4e3f0ea4544ed302e3bfe91968581' // from above auth request
bitgo.refreshToken({ refreshToken: refreshToken }, function(err, result) {
  if (err) {
    console.dir(err);
    throw new Error("Could not refresh!");
  }
  console.dir(result);
});
```

```shell
REFRESH_TOKEN='3f8aa90479b4e3f0ea4544ed302e3bfe91968581' #from above request
CLIENT_ID='FBExchange'
CLIENT_SECRET='testclientsecret'
curl -X POST https://test.bitgo.com/oauth/token \
-H "Content-Type: application/json" \
-d  "{ \"client_id\": \"$CLIENT_ID\",
       \"client_secret\": \"$CLIENT_SECRET\",
       \"grant_type\": \"refresh_token\",
       \"refresh_token\":\"$REFRESH_TOKEN\"
    }"
```

从之前步骤获取的访问令牌的典型时间为 1 小时。

要延长用户会话，你可以使用之前步骤中的请求令牌请求另一个访问令牌。

> 响应示例

```json
{
    "token_type":"bearer",
    "access_token":"2dba5167e70d4c18679a9775c57b184951a4fa07",
    "expires_in":3600,
    "expires_at":1418059789,
    "refresh_token":"3f8aa90479b4e3f0ea4544ed302e3bfe91968581"
}
```

* 测试端点：https://test.bitgo.com/oauth/token
* 生产环境端点：https://www.bitgo.com/oauth/token

刷新令牌从创建起有 2 周生命周期，仅在*单次使用*有效。每次使用后会签发给你一个新的刷新令牌。

### OAuth 令牌请求参数 OAuth Token Request Parameters

| 参数            | 说明                                     |
| ------------- | -------------------------------------- |
| client_id     | 需要请求第三方帐号访问权限的 OAuth 应用程序的字符串（名称）      |
| client_secret | 加密字符串，存于 OAuth 应用程序的服务器上，由 BitGo 签发给你。 |
| grant_type    | 应为 'refresh_token'                     |
| refresh_token | 当你使用验证代码换取访问令牌时收到的刷新令牌。                |

# 范例 Examples

BitGo 提供了关于如何使用我们的 SDK 进行多个常用钱包操作的范例。此处详述的为重点。<aside class="info"> 我们的 SDK 和范例默认为连接到 Bitcoin TestNet 的 BitGo 测试环境。 欲知详情请查阅 

<a href="#bitgo-api-endpoints">测试环境</a> 章节。 </aside> 

下文中的范例（还有更多）可在我们 SDK 仓库的 `BitGoJS/examples` 目录中找到。 若范例有问题请通过电子邮件或 Git issues 提交。

### 获取钱包 ID Obtaining the Wallet ID

当你在 BitGo 测试网站上创建钱包时，钱包 id 为第一个接收地址。当你从主菜单点击它时的 URI 中也包含 id。

## 获取钱包余额 Get Wallet Balance

> 代码片段

```javascript
var bitgo = new BitGoJS.BitGo();

// 首先，进行验证
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  console.log("Logged in!" );

  // 获取余额
  bitgo.wallets().get({type: 'bitcoin', id: id}, function(err, wallet) {
    console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));
  });
});
```

    $ node getWalletBalance.js tester@bitgo.com superhardseypassphrase 0000000 2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
    

> 输出示例

    Logged in!
    Balance is: 0.6274
    

此简单范例演示了如何进行验证并获取钱包。 比特币余额可在钱包模型中找到。

### 用法 Usage

`node getWalletBalance.js <user> <pass> <otp> <walletId>`

### 参数 Parameters

| 名称       | 类型     | 必需  | 说明                         |
| -------- | ------ | --- | -------------------------- |
| user     | string | YES | 用户名（你在测试环境中的电子邮件）          |
| pass     | string | YES | BitGo 密码                   |
| otp      | number | YES | 一次性密码（在测试环境中你可以使用 0000000） |
| walletId | string | YES | 钱包的 id （亦是第一个接收地址）         |

## 列出钱包的交易 List Wallet Transactions

> 代码片段

```javascript
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  console.log("Logged in!" );

  // 获取钱包
  bitgo.wallets().get({type: 'bitcoin', id: id}, function(err, wallet) {
    console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));

    // 获取交易
    wallet.transactions({}, function(err, result) {
      for (var index = 0; index < result.transactions.length; ++index) {
        var tx = result.transactions[index];
        var value = 0;
        for (var entriesIndex = 0; entriesIndex < tx.entries.length; ++entriesIndex) {
          if (tx.entries[entriesIndex].account === wallet.id()) {
            value += tx.entries[entriesIndex].value;
          }
        }
        var verb = (value > 0) ? 'Received' : 'Sent';
        console.log(tx.id + ': ' + verb + ' ' + (value / 1e8).toFixed(8) + 'BTC on ' + tx.date);
      }
    });
  });
});
```

    $ node listWalletTransactions.js tester@bitgo.com superhardseypassphrase 0000000 2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
    

> 输出示例

    Logged in!
    Balance is: 0.6274
    b9573e0e1d8f22fbfe314760b9abd0b6942132cfb2bd7f9fee9713b545a62689: Received 0.56000000BTC on 2014-11-17T21:20:59.000Z
    b3bd8ac76de2340c1159337acdfcaabf08a9470e8157870bd1d84631a0acc67b: Received 0.00010000BTC on 2014-11-17T21:00:58.000Z
    7f5d6a27ea832b2d0a9b63ecdbccecaadbd582b5be41d5bdeabcce72243366a3: Received 0.00010000BTC on 2014-11-17T19:57:18.000Z
    690b8a83e1685869f6138d9a74776f5f868ffc1121fa22f2086e65400f14ef78: Received 0.00010000BTC on 2014-11-17T19:57:18.000Z
    

本范例演示了如何获取一个钱包的交易列表。这在校验收到的交易 ID 时很有用。

### 用法 Usage

`node listWalletTransactions.js <user> <pass> <otp> <walletId>`

### 参数 Parameters

| 名称       | 类型             | 必需  | 说明                         |
| -------- | -------------- | --- | -------------------------- |
| user     | string         | YES | 用户名（你在测试环境中的电子邮件）          |
| pass     | string         | YES | BitGo 密码                   |
| otp      | number         | YES | 一次性密码（在测试环境中你可以使用 0000000） |
| walletId | 比特币地址 (string) | YES | 钱包的 id （亦是第一个接收地址）         |

## 地址标签 Address Labels

    $ node addressLabels.js tester@bitgo.com superhardseypassphrase 0000000
    

> 输出示例

    Enter the wallet ID: 2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
    Which label action do you wish to perform? [list, set, delete]: list
     muYhgJrYZHffyGUmuzETjMcBQZJqFo9Vkg    Secret Stash
     2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD   another label
     2MzjET9tYPPZQtsahFvabhaaPWuDdKn3Dh6   descriptive text
     n1wbb1HeZULZwvtcYMLeFz8J9QD7HkG1pa    watch only address
    

本范例演示了如何列出、设置以及删除地址上的标签。

### 用法 Usage

`node addressLabels.js <user> <pass> <otp>`

### 参数 Parameters

| 名称   | 类型     | 必需  | 说明                         |
| ---- | ------ | --- | -------------------------- |
| user | string | YES | 用户名（你在测试环境中的电子邮件）          |
| pass | string | YES | BitGo 密码                   |
| otp  | number | YES | 一次性密码（在测试环境中你可以使用 0000000） |

## 创建钱包 Create Wallet

> 代码片段

```javascript
  bitgo.wallets().createWalletWithKeychains({"passphrase": password, "label": label, "backupXpubProvider": "keyternal"}, function(err, result) {
    if (err) { console.dir(err); throw new Error("Could not create wallet!"); }
    console.log("New Wallet: " + result.wallet.id());
    console.dir(result.wallet.wallet);

    console.log("BACK THIS UP: ");
    console.log("User keychain encrypted xPrv: " + result.userKeychain.encryptedXprv);
    console.log("Backup keychain xPub: " + result.backupKeychain.xpub);
});
```

    $ node createWallet.js tester@bitgo.com superhardseypassphrase 0000000 'My API wallet'
    

> 输出示例

    New Wallet: 2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf
    

```json
{ "id": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
 "label": "testwallet",
 "isActive": true,
 "type": "safehd",
 "private": { "keychains": [{}] },
 "permissions": "admin,spend,view",
 "admin": {},
 "spendingAccount": true,
 "confirmedBalance": 0,
 "balance": 0,
 "pendingApprovals": [] }
```

    BACK THIS UP:
    User keychain encrypted xPrv: {"iv":"v2aVEG5A8VwnI+ewS..."}
    Backup keychain xPub: xpub6GiRC55CRuv2Fx3ihR9EPCr7gauoJcHqvvdSgQkmMmqMQmvQ1KNSBmKPReryBv8E3qJWkHmCx3cWmLGvbDRzCAoV7HUf8A5LUdRhV46u9h5
    

在 BitGo 上创建钱包。本范例演示了如下步骤：

  1. 与 BitGo 进行验证
  2. 解锁账号
  3. 在客户端上创建用户的钥匙串，使用密码加密后发送到服务器。
  4. 在客户端上创建备份钥匙串。
  5. 在 BitGo 服务器上创建 BitGo 钥匙串。
  6. 使用与上述钥匙串对应的公钥创建钱包。<aside class="warning"> **非常重要** 的是要让用户打印 / 备份他们的用户和备份密钥。 跳过这步可能会导致资金损失！ </aside> 

### 用法 Usage

`node createWallet.js <user> <pass> <otp> <label>`

### 参数 Parameters

| 名称    | 类型     | 必需  | 说明                         |
| ----- | ------ | --- | -------------------------- |
| user  | string | YES | 用户名（你在测试环境中的电子邮件）          |
| pass  | string | YES | BitGo 密码                   |
| otp   | number | YES | 一次性密码（在测试环境中你可以使用 0000000） |
| label | string | YES | 在 BitGo 界面显示的钱包名称          |

## 发送比特币到某个地址 Send Bitcoins to an Address

> 代码片段

```javascript
var sendBitcoin = function() {
  // 获取钱包
  bitgo.wallets().get({id: walletId}, function(err, wallet) {
    // 发送比特币
    wallet.sendCoins({ address: destinationAddress, amount: amountSatoshis, walletPassphrase: walletPassphrase }, function(err, result) {
      console.dir(result);
    });
  });
};

// 验证并解锁账号以便发送比特币
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  bitgo.unlock({otp: otp}, function(err) {
    sendBitcoin();
  });
});
```

    $ node sendBitcoin tester@bitgo.com superhardseypassphrase 0000000 2N91XzUxLrSkfDMaRcwQhe9DauhZMhUoxGr superhardseypassphrase 2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD 10000
    

> 输出示例

    { "tx": "01000000017bc6aca03146d8d10b875781...",
      "hash": "101f1f0f2218b0a0ac9aea1c054fbba7d2e75e09fbeeae7acea0254baa9505b7",
      "fee": 10000 }
    

发送比特币到另一个比特币地址。本范例使用如下步骤：

  1. 与 BitGo 进行验证
  2. 解锁账号以便可以发送比特币
  3. 使用钱包 ID （walletId）从服务器获取钱包
  4. 调用 wallet.sendCoins 方法，将会查找用户密钥，进行解密，创建交易并签名然后发送至 BitGo 以供签名。

### 用法 Usage

`node sendBitcoin <user> <pass> <otp> <walletId> <walletPassphrase> <destinationAddress> <amountSatoshis>`

### 参数 Parameters

| 名称                 | 类型             | 必需  | 说明                               |
| ------------------ | -------------- | --- | -------------------------------- |
| user               | string         | YES | 用户名（你在测试环境中的电子邮件）                |
| pass               | string         | YES | BitGo 密码                         |
| otp                | number         | YES | 一次性密码（在测试环境中你可以使用 0000000）       |
| walletId           | 比特币地址 (string) | YES | 在 BitGo 界面显示的钱包名称                |
| walletPassphrase   | string         | YES | 用于加密用户私钥的密码                      |
| destinationAddress | 比特币地址 (string) | YES | 钱包的目标地址                          |
| amountSatoshis     | string         | YES | 要发送的单位为聪的金额, 如 0.1*1e8 即 0.1 比特币 |

## Webhook Oracle 策略 Webhook Oracle Policy

> 代码片段

```javascript
var setUpPolicyAndSendBitcoin = function() {
  console.log("Getting wallet..");
  bitgo.wallets().get({id: walletId}, function(err, wallet) {
    if (err) { console.log("Error getting wallet!"); console.dir(err); return process.exit(-1); }
    console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));
    // 设置策略
    var rule = {
      id: "webhookRule1",
      type: "webhook",
      action: { type: "deny" },
      condition: { "url": url }
    };
    console.dir(rule);
    console.log("Setting webhook policy rule.. ");
    wallet.setPolicyRule(rule, function callback(err, walletAfterPolicyChange) {
      if (err) { throw err; }
      console.log("New policy: ");
      console.dir(walletAfterPolicyChange.admin.policy);
      wallet.sendCoins({ address: destinationAddress, amount: amountSatoshis, walletPassphrase: walletPassphrase },
      function(err, result) {
        // 移除规则
        wallet.removePolicyRule({id: "webhookRule1"}, function(err, res) { console.log("Removed policy rule"); });
        if (err) { console.log("Error sending coins!"); console.dir(err); }
        if (result) {
          console.dir(result);
        }
      });
    });
  });
};

// 验证并解锁账号以供发送比特币
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  bitgo.unlock({otp: otp}, function(err) {
    setUpPolicyAndSendBitcoin();
  });
});
```

```shell
$ node addPolicyWebhookAndSendCoins bencxr@fragnetics.com nicehardpassword 0000000 2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2 https://486d7844.ngrok.com/ walletpasw0rd 2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe 1380000
```

> 输出示例

    Getting wallet..
    Balance is: 1.2582
    { id: 'webhookRule1',
      type: 'webhook',
      action: { type: 'deny' },
      condition: { url: 'https://486d7844.ngrok.com/' } }
    Setting webhook policy rule..
    New policy:
    { id: '5632aa4039cccac60913ea0ed44276c0',
      rules:
       [ { id: 'webhookRule1',
           type: 'webhook',
           action: [Object],
           condition: [Object] } ] }
    { status: 'accepted',
      tx: '0100000001a89f36c0b714878cdd4cddf5...' }
    Removed policy rule
    

> Webhook 回调示例（发送至之前提供的 URL，任何非 200 响应将触发策略拒绝）

```json
{
    "approvalCount": 0,
    "outputs": [
        {
            "outputAddress": "2N9kNR8iS46WuwekvQVaTUa74w2fbvAXHQn",
            "outputWallet": "2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2",
            "value": 24885327
        },
        {
            "outputAddress": "2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe",
            "outputWallet": "2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe",
            "value": 10000000
        }
    ],
    "ruleId": "webhookRule1",
    "spendAmount": 10009060,
    "type": "webhook",
    "unsignedRawTx": "0100000001f8de6273285b13f20b59195c4...",
    "walletId": "2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2"
}
```

本范例演示了如何在钱包上设置一个 webhook 策略以通过 URL 端点（脚本或其他程序）运行任意自定义外部逻辑。 这将允许外部状态被映射到一个多名用户共享单个钱包约束（该 URL 即为 “oracle”）。

当交易被使用，所提供的 URL 会被访问。若返回 200 状态，则交易将被允许。否则，策略规则将被触发交易也将被拒绝。

  1. 与 BitGo 进行验证
  2. 解锁账号使其可以花费比特币并设置策略。
  3. 使用钱包 ID （walletId）从服务器获取钱包
  4. 设置策略使所提供的 URL 会被访问。
  5. 调用 wallet.sendCoins 方法，将会查找用户密钥，进行解密，创建交易并签名然后发送至 BitGo 以供签名。
  6. 移除策略并返回结果。

当在本地测试时，在创建 URL 前可以先设置一个本地服务器（express 或任何 http 服务器都可以）， 然后运行工具比如 ngrok 以获取公开的 facing url。

### 用法 Usage

`node addPolicyWebhookAndSendCoins <user> <pass> <otp> <walletId> <url> <walletPassphrase> <destinationAddress> <amountSatoshis>`

### 参数 Parameters

| 名称                 | 类型               | 必需  | 说明                               |
| ------------------ | ---------------- | --- | -------------------------------- |
| user               | string           | YES | 用户名（你在测试环境中的电子邮件）                |
| pass               | string           | YES | BitGo 密码                         |
| otp                | number           | YES | 一次性密码（在测试环境中你可以使用 0000000）       |
| walletId           | 比特币地址 (string)   | YES | 在 BitGo 界面显示的钱包名称                |
| url                | http 端点 (string) | YES | 设置策略使用的 URL                      |
| walletPassphrase   | string           | YES | 用于加密用户私钥的密码                      |
| destinationAddress | 比特币地址 (string)   | YES | 钱包的目标地址                          |
| amountSatoshis     | string           | YES | 要发送的单位为聪的金额, 如 0.1*1e8 即 0.1 比特币 |

## 恢复钱包 Recover Wallet

RecoverWallet （恢复钱包）是一个工具可以用于恢复 BitGo 钱包中的资金无需使用 BitGo 服务。 这个工具作为演示用途提供以证明 BitGo 钱包是 100% 可恢复的即使在 BitGo 服务不可用的情形下。 我们没有期望这个工具会被用在生产环境。

只需提供钱包钥匙卡的信息，这个工具将创建一个交易移动钱包内的全部资金到用户选择的新账号。 完成此项操作不需要使用任何 BitGo 服务 API.

这个 RecoverWallet 工具是交互形式或命令行形式。

### 用法 Usage

node recoverwallet.js --userKey <userkey from keycard> --backupKey <backupkey from keycard> --bitgoKey <bitgo public key from keycard>

### 参数 Parameters

| 名称          | 含义                            |
| ----------- | ----------------------------- |
| userKey     | 钱包的用户扩展私钥。（钱包钥匙卡的 Box A）      |
| backupKey   | 钱包的备份扩展私钥。（钱包钥匙卡的 Box B）      |
| bitgoKey    | 钱包的 BitGo 扩展公钥。（钱包钥匙卡的 Box C） |
| testnet     | 标示，使用 testnet 而不是生产环境的比特币网络   |
| nosend      | 标示，创建交易但不发送                   |
| password    | 密码用于解密 userKey 和 backupKey    |
| destination | 发送恢复的资金的目标比特币地址               |
