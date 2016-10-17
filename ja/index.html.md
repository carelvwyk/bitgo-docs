* * *

タイトル： BitGo APIレファレンス

language_tabs: - javascript - shell

toc_footers: - <a href="https://www.bitgo.com/" target="_new">BitGoウェブサイト</a> - <a href="https://www.bitgo.com/terms" target="_new">サービス契約</a> - <a href="https://www.bitgo.com/settings" target="_new">BitGo 設定 (APIアクセストークンを取得)</a> - <a>言語</a> - [- English　英語](index.html) - [- Japanese 日本語](ja/index.html) - [- Chinese (Simplified) 简体中文](zh-CN/index.html)

* * *

# Getting Started はじめに<aside class="info"> 私達の開発者プラットフォームが立ち上がりました。インテグレーション支援やアクセストークン、追加情報にサインアップするには

[BitGoプラットフォームポータル](https://www.bitgo.com/platform)にお越しください。 </aside> 

### Overview 概要

BitGoは、マルチシグ技術を現行のあなたのビットコインアプリケーションやサービスに統合するためのシンプルで堅牢なRESTful API及びシンプルなクライアントサイドjavascriptを提供しています。

BitGo SDKは以下の操作を可能にします：

* P2SH(マルチシグネチャ) ウォレットの作成
* 階層的決定性ウォレット管理(BIP32)
* トランザクションの作成
* トランザクション署名
* 支出制限
* マルチサイナーウォレットフロー

### Multi-Signature Wallets マルチシグネチャウォレット

マルチシグウォレットの主要な利点は、複数のマシンや人々が協働し特定のトランザクションを承認する能力です。 トランザクションのマルチシグネチャがなければ、トランザクションを承認するための全ての証明はマシン上の１人の人間に常に常駐しなければなりません。 その人間またはマシンが攻撃者によって侵入された場合、あなたの持つ全てのビットコインが失われることがあります。

これまで、これらの１人の人間 / シングルマシンのセキュリティを確保するのは非常に困難で、多くのベンダーは単に「コールドストレージ」を用いてビットコインを完全にオフラインに置くことを選んんでいました。

マルチシグウォレットはビットコインをオフラインに置くことなく、あなたがモダンなビットコインアドレスに求める柔軟性を提供します。 BitGo APIは、自分のアプリケーションでマルチシグネチャ機能を利用し、複数ユーザー、連署者、最先端の不正検出サービスが持つ完全な柔軟性を損失や盗難から保護するため活用するのを可能にします

詳細については、<a href="https://www.bitgo.com/p2sh_safe_address" target="_new">BitGoホワイトペーパー</a>をお読みください。

### HD Wallets, HDウォレット

全てのBitGoウォレットは階層的決定性ウォレット（別名HDウォレット）です。 HDウォレットはビットコイン<a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki" target="_new">BIP32規格</a>を用いて実装されています。 よって、BitGo' HDウォレットは個別の鍵というより「キーチェーン」から構築されており、2つの特徴的なセキュリティとプライバシー強化の機能を提供します。

* よりセキュアなバックアップ
    
    キーチェーンは単一のシークレットキーでバックアップされているため、ウォレットは単一のバックアップキーで保全される多数のパブリックキーを用いることが出来ます。

* ブロックチェーンプライバシー
    
    HDウォレットなら、どのトランザクションも同一のウォレットから到着したと見えないよう、アプリケーションはトランザクション毎に新たなキーを作成できます。 これにより、ウォレットの本当の大きさが漏洩することからウォレット保持者が保護されます。

## Software Development Kit ソフトウェア開発キット

BitGo APIは、開発者にマルチシグウォレットの作成、管理、ポリシーの操作、そしてビットコインネットワークとやり取りする方法を提供します。 ただし、ユーザの秘密鍵やトランザクションの署名など、いくつかセンシティブな操作がクライアントサイドで行われる必要があります。

そのような理由から、私達は<a href="https://github.com/BitGo/BitGoJS" target="_new">ソフトウェア開発キット(SDK)</a>を提供の上、利用をお勧めします。このSDKは、これらのクライアントサイドのウォレット機能を実装し、私達のAPIとインターフェースするものです。 実際には、BitGo APIの利用を検討している開発者は、おそらくBitGoクライアントサイドSDKもBitGo RESTサービスも利用するでしょう。

現在、私達のSDKはjavascriptで利用可能で、node.jsまたはブラウザで実行されます。 サポートされていないプログラミング言語を利用している場合、BitGo Expressの設定方法について「BitGo Express REST API」のドキュメンテーションのセクションを参照いただくか、私達にお問い合わせください。

Javascript SDKのインストール (npmを通じ)

`npm install bitgo --save`

Javascript SDKのインストール (Githubを通じ)

* Githubにある<a href="https://github.com/BitGo/BitGoJS" target="_new">オープンソースSDKページ</a>をご覧ください。
* Gitとnodejs/npmをインストール(例に従うことを推奨)
* 次のコマンドを実行することにより、ローカルに私達のレポジトリをクローン `git clone git@github.com:BitGo/BitGoJS.git`
* BitGoJSディレクトリで次によって依存関係をインストール：`npm install`
* 「例」のディレクトリをチェックして、SDKをどのように利用できるかを参照して、次を実行ください

`node auth.js <testusername> <testpassword> 0000000`

### Importing and initializing the library ライブラリのインポートと初期化

```javascript
// パーケージからインポートしている場合
var BitGoJS = require('BitGoJS/src/index.js');
var bitgo = new BitGoJS.BitGo();

// npm install bitgoからインストールしている場合
// var bitgo = require('bitgo');

bitgo.ping({}, function(err, res) {
    // ここでやりたいことをやる
});
```

ライブラリをインポートするには、`src/index.js`ファイルが必要なだけです。 そうしたら `BitGoJS.BitGo()` を実行することによりSDKを初期化できます。

| パラメーター        | 値                          |
| ------------- | -------------------------- |
| useproduction | プロダクションに接続するかどうか。デフォルト値は偽。 |

Javascript SDKはpromiseとコールバックの両方をサポートしています。コールバックを最後の引数として渡した場合、コールバックスタールで返します。さもなければpromiseが返されます。

### Important notes on test environment テスト環境に関する重要な注意

私達のSDKと各例は、ビットコインテストネットと接続されているBitGoテスト環境の初期値になっています。 詳細は[テスト環境](#bitgo-api-endpoints)のセクションを参照してください。

## BitGo API Endpoints, BitGo API エンドポイント

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

BitGoは開発とプロダクション向けに2つの個別の環境を利用可能にしています。

すべての応答は、`application/json`コンテンツタイプです

> 応答の例

    {
        "status": "service is ok!",
        "environment": "BitGo Test"
    }
    

### Production Environment プロダクション環境

BitGo プロダクションエンドポイントは立ち上がっており、提携パートナーとwww.bitgo.com にある弊社独自のウェブアプリケーションにより利用されています。

* プロダクションサイト: https://www.bitgo.com/
* プロダクションAPI: https://www.bitgo.com/api/v1

### Test Environment テスト環境

私達の各例とSDKでは、BitGoのテスト環境がデフォルトで利用されています。 BitGoのプロダクション環境とは完全に別個のものであり、データとアカウントにおけるオーバーラップはありません。 <a href="https://test.bitgo.com/" target="_new">test.bitgo.com</a> でアカウントを作成する必要があります。

* BitGo テスト サイト: https://test.bitgo.com/
* テスト環境 API: https://test.bitgo.com/api/v1

テスト環境の場合のみ、（自動テストを目的として）BitGoでの認証においてOTPの代わりに`0000000`が使えます。

この環境は、ナビゲーションに<a href="http://tbtc.blockr.io/" target="_new">Blockr</a>を利用できるビットコインテストネットに接続されています。 テストコインを取得するには、<a href="http://tpfaucet.appspot.com/" target="_new">フォーセット</a>か、あるいはご連絡ください。

## BitGo Express REST API

```shell
./bitgo-express --debug --port 3080 --env test --bind localhost
```

```shell
BITGO_EXPRESS_HOST='localhost'
curl http://$BITGO_EXPRESS_HOST:3080/api/v1/ping
```

BitGo Express REST APIは、BitGoを利用したいがネイティブのBitGo SDKのない言語環境で開発している開発者向けのライトウェイトサービスです。

BitGo Expressはあなたのデータセンターのサービスとして稼働し、BitGoに送信する前の部分的なトランザクションの署名など、あなた自身の鍵を伴うクライアントサイドの操作を処理します。 これにより、あなたの鍵は決してネットワーク外に出ることなく、BitGoの方で表示されることはありません。 BitGo Expressは、標準のBitGo REST APのプロクシとして振舞うことも出来、単一のREST APIを通じBitGoへの統一インターフェースを提供します。

BitGo Expressを利用するには:

* [BitGoJS](#software-development-kit) をインストールします
* bin ディレクトリで次のコマンドを実行します:

`./bitgo-express --debug --port 3080 --env test --bind localhost`

* **全ての**BitGo REST APIの呼び出しを、bitgo-expressを実行しているマシンに対し行う

## Error Handling エラー処理

> JSON エラーの例

```json
{
  "status":  "400 Bad Request",
  "error": "missing user name"
}
```

全てのエラーは一般的なRESTの原則に従います。エラー応答の本文(例えば非200ステータスコード)に含まれるのは次の形式のエラーオブジェクトになります：

| パラメーター | 値                                     |
| ------ | ------------------------------------- |
| status | The HTTP error status returned        |
| error  | The detailed description of the error |

# User Authentication ユーザー認証

BitGoの認証は"Authorization"のヘッダーを通じて行われ、呼び出し元がアクセストークンを指定するのを可能にします。

アクセストークンはセッションを維持するのに利用され、パスワードログイン（ワンタイムパスワード（OTP）が必要）によって作成されます。 典型的なアクセストークンは1時間の間有効で、消費された資金をアンロックするのにOTPを必要とします。

デフォルトで、トークンは単一のIPアドレスに制限され、60分間有効です。それが過ぎたらユーザーは再認証する必要があります。

一部のAPIコールについては、有効なセッショントークンだけでは不十分です。 これらのAPIコールにアクセスするには、セッションはUnlock APIを用いて、追加の2要素コードによって明示的にアンロックされなければなりません。 単一のアンロックコールはユーザーが任意のサイズのトランザクション（ウォレットポリシーの対象）、または内部のBitGoが管理するクォータ以下の、任意の回数のトランザクションを行うことを可能にします。<aside class="info"> アンロックが必要なAPIは、セッションが現在ロックされている場合または現在のアンロックセッションのトランザクションクォータの残りが不十分でない場合、応答にneedsUnlock=trueを含みます。 </aside> 

また、API 用に作成されたアクセス トークンは一定の額まで無期限にロックすることができますが、作成時に特定のスコープにバインドされる必要があります。

## API Access Tokens, APIアクセストークン

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
    // handle error
  }
  console.dir(session);
});
```

自動化の目的で、開発者は、1時間で期限が切れない一定額の資金について、アンロックされた長寿命のアクセストークンをリクエストすることができます。

  1. BitGoダッシュボードへアクセスして、「設定」のページへ行く
  2. 「開発者」のタブをクリック
  3. 長寿命のアクセストークンを作成できるようになりました

トークンは、デフォルトでは、あなたが指定した支出制限に基づきロックされていない状態で来ます。アンロックがリセットされるので、再度API経由でトークンをアンロックしようとしないで下さい。

### Token Parameters トークンパラメーター

| パラメーター         | 説明                                                                           |
| -------------- | ---------------------------------------------------------------------------- |
| Label          | 後で無効にすることを選択できるようトークンを特定するために用いられるラベル                                        |
| Duration       | トークンが有効であり続ける秒数                                                              |
| Spending Limit | トークンは、BTC建ての支出制限の額までのアンロックされた状態で来ます。制限がリセットされるので、API経由でトークンをアンロックしようとしないで下さい |
| IP Addresses   | BitGoが特定のIPアドレスからのみ受け付けるよう、トークンをロックダウンします                                    |
| Permissions    | トークンが生成される際の認証の範囲                                                            |

## Current User Profile 現在のユーザープロファイル

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/user/me
```

```javascript
bitgo.me({}, function callback(err, user) {
  if (err) {
    // handle error
  }
  // etc
});
```

現在認証されているユーザーについて情報を得ます。

### HTTP Request HTTPリクエスト

`GET /api/v1/user/me`

> ユーザモデルの応答の例

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

### 応答

現在認証されているユーザーのユーザモデルのオブジェクトを返す。

## Login　ログイン　

BitGo APIへのファーストパーティアクセスのためのトークンを取得します。 ファーストパーティアクセスは、自身のBitGoアカウントへアクセスしているユーザーのみ向けです。 別のユーザーの代わりでのBitGo APIへサードパーティアクセスの場合、**パートナー認証**を参照下さい。

```shell
EMAIL="janedoe@bitgo.com"
PASSWORD="mypassword"
HMAC=`echo -n "$PASSWORD" | openssl dgst -sha256 -hmac "$EMAIL" | sed 's/^.* //' `

curl -X POST \
-H "Content-Type: application/json" \
-d "{\"email\": \"$EMAIL\", \"password\": \"$HMAC\", \"otp\": \"0000000\"}" \
https://test.bitgo.com/api/v1/user/login

注意 : 以降のシェルの例では、シェル変数ACCESS_TOKENがアクセストークンを含んでいることを前提とします。
```

```javascript
var useTestnet = false;
var bitgo = new Bitgo(useTestnet);
bitgo.authenticate({ username: user, password: password, otp: otp }, function callback(err, response) {
  if (err) {
    // handle error
  }
  var token = response.token;
  var user = response.user;
  // etc
});
```

### HTTP Request HTTPリクエスト

`POST /api/v1/user/login`

### BODY Parameters BODYパラメーター

| パラメーター     | 種類    | 必須か | 説明                      |
| ---------- | ----- | --- | ----------------------- |
| email      | 文字列   | YES | ユーザのメールアドレス             |
| password   | 文字列   | YES | ユーザのパスワード               |
| otp        | 文字列   | YES | 2要素認証トークン(Authyトークン)。   |
| extensible | ブーリアン | NO  | セッションが１時間よりも延長可能である場合に真 |

> 応答の例

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

### Response 応答

APIで使用するトークンを返す。

トークンはHTTP"Authorization"ヘッダーにある全てのAPIコールへへ、HTTPヘッダーとして追加されなければなりません。

`Authorization: Bearer <あなたのトークンはここ>`

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |

## Logout ログアウト

BitGoサービスからのログアウト。

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/user/logout
```

```javascript
bitgo.logout({}, function callback(err) {
  if (err) {
    // handle error
  }
  // the user is now logged out.
});
```

### HTTP Request HTTPリクエスト

`GET /api/v1/user/logout`

### BODY Parameters BODYパラメーター

なし

### Response 応答

なし

## Session Information セッション情報

現在のセッションのアクセストークンに関する情報を取得する

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{}' \
-H "Content-Type: application/json" \
https://test.bitgo.com/api/v1/user/session
```

```javascript
bitgo.session({}, function callback(err, session) {
  if (err) {
    // handle error
  }
  console.dir(session);
});
```

> 応答の例

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
    

### HTTP Request HTTPリクエスト

`GET /api/v1/user/session`

### Response 応答

| フィールド   | 説明                                             |
| ------- | ---------------------------------------------- |
| client  | ユーザートークンが取得された所のOAuthクライアントID                  |
| user    | BitGo ユーザー ID                                  |
| expires | ログインセッションがその時間まで有効なタイムスタンプ                     |
| scope   | このセッショントークンについて許可されている権限のリスト                   |
| origin  | ブラウザでセッションが開始された場合、トークンが作成されたオリジンホスト名          |
| unlock  | セッションがアンロックされた時に利用可能。トランザクションの回数とアンロックの有効期限を示す |

## Send OTP ワンタイムパスワードを送信

ワンタイムパスワード(第二因子認証) トークンをユーザーに送信します。ログイン/アンロックに使用することができます。

```shell
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{}' \
-H "Content-Type: application/json" \
https://test.bitgo.com/api/v1/user/sendotp
```

```javascript
bitgo.sendOTP({forceSMS: true}, function callback(err) {
  if (err) {
    // handle error
  }
  // etc
});
```

### HTTP Request HTTPリクエスト 

`POST /api/v1/user/sendotp`

### BODY Parameters BODYパラメーター

| 名        | 種類    | 必須か | 説明                                            |
| -------- | ----- | --- | --------------------------------------------- |
| forceSMS | ブーリアン | NO  | Authyが設定されている場合でも、SMSを使ってワンタイムパスワードをユーザに送信する。 |

### Response 応答

なし

## Unlock アンロック

現在のセッションをアンロックします。特定の他のセンシティブなAPIコールに必要です。

```shell
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{"otp": "0000000"}' \
-H "Content-Type: application/json" \
https://test.bitgo.com/api/v1/user/unlock
```

```javascript
bitgo.unlock({otp: otp}, function callback(err) {
  if (err) {
    // handle error
  }
  // etc
});
```

### HTTP Request HTTPリクエスト

`POST /api/v1/user/unlock`

### BODY Parameters BODYパラメーター

| パラメーター   | 種類  | 必須か | 説明                                 |
| -------- | --- | --- | ---------------------------------- |
| otp      | 文字列 | YES | アカウントの Authy OTP コード               |
| duration | 数字  | NO  | 所望のアンロックの秒数(default=600, max=3600) |

### Response 応答

なし

### Errors　エラー

| Response 応答      | 説明                                 |
| ---------------- | ---------------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない              |
| 401 Unauthorized | 認証パラメーターが一致しない、または OTP コードが正しくなかった |

## ロック

現在のセッションを再ロックします

```shell
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/user/lock \
```

```javascript
bitgo.lock({}, function callback(err) {
  // ...
});
```

### HTTP Request HTTPリクエスト

`POST /api/v1/user/lock`

### Response 応答

なし

### Errors　エラー

| Response 応答      | 説明             |
| ---------------- | -------------- |
| 401 Unauthorized | 認証パラメーターが一致しない |

## Partner Authentication パートナー認証

BitGO APIを利用するサードパーティアプリケーションはBitGoを通じて認証するのにOAuthを用います。パートナーIDの詳細についてはBitGoまでご連絡ください。

# Keychains キーチェーン

全てのBitGoウォレットはキーチェーンを使用して作成されます。 キーチェーンとは標準の <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki" target="_new">BIP32</a> 拡張HDキーです。 これまでの単一の<a href="http://en.wikipedia.org/wiki/Elliptic_Curve_DSA" target="_new">ECDSA</a>キーペアを代表するビットコインキーと違い、一つのキーチェーンは共通秘密鍵から派生する多数のキーペアを代表する事ができます。 これにより、ユーザーは単一の秘密鍵を保持しながら、無数の公開鍵を生成することが可能になります。 BitGoはこれらの拡張された鍵を用いて、あなたのビットコインをよりプライベートでセキュアにします。

ウォレットの生成をシンプルにするため、BitGoは各ユーザーごとのキーチェーンのリストを維持します。各キーチェーンは任意の数のBitGoウォレットで使用することができます。

2つの種類のキーチェーンがあります：

* パブリックキーチェーン
    
    これらは単一のBIP32拡張公開鍵（xpub）で構成されます。

* プライベートキーチェーン
    
    これらは、常に暗号化された形式で格納されている単一のBIP32拡張秘密鍵(xprv) で構成されます。

全てのキーチェーンはそれらのxpubによって識別されます。便宜のため、各キーチェーンはラベルを持つことがあります。

最初のウォレットを作成する前に、そのウォレットとともに使用するキーチェーンを作成する必要があります。<aside class="success"> プライベートキーチェーン（暗号化され形式であっても) へのアクセスにおいて、必ず2要素認証が必須であることに注意してください。 </aside> 

## List Keychains キーチェーンの一覧を取得する

```shell
curl -X GET -H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/keychain
```

```javascript
var keychains = bitgo.keychains();
keychains.list({}, function callback(err, keychains) {
  if (err) {
    // handle error
  }
  console.dir(keychains);
});
```

そのユーザーのパブリックキーチェーンの一覧を取得します<aside class="success"> このAPIは公開鍵だけを提供し、キーチェーンのプライベートデータを決して提供しません。 </aside> 

### HTTP Request HTTPリクエスト

`GET /api/v1/keychain`

> キーチェーンモデルの応答の例

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

### QUERY Parameters クエリ パラメーター

| パラメーター | 種類 | 必須か | 説明                                      |
| ------ | -- | --- | --------------------------------------- |
| skip   | 数字 | NO  | 一覧取得を開始するインデックス番号。既定値は0。                |
| limit  | 数字 | NO  | 単一コール(default=100, max=500) で返す結果の最大の件数 |

### Response 応答

キーチェーンモデルオブジェクトの配列を返します。

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |

## Create Keychain キーチェーンを作成する

```shell
ローカルメソッドとしてのみ利用可能 (BitGo Express)

curl -X POST http://$BITGO_EXPRESS_HOST:3080/api/v1/keychain/local
```

```javascript
var keychains = bitgo.keychains();
var keychain = keychains.create();

console.dir(keychain);
```

> 応答の例

```json
  {
    "xpub": "xpub661MyMwAqRbcFdEUWdhDhgKKQ9tQzLSuqZzVM2AZf9TiijPMD84tPYmemZcQosg63SZit3jGQpCDsbbeXPv7A3aT1phPaBgAWQWKwFUioPR",
    "xprv": "xprv9s21ZrQH143K39A1QcADLYNar83vasj4UM4tYdkx6ovjqw4CfakdqkTAvFrY8BoR8TFTCYShYJ3XjZCicvyuavmYjPLVbmASmHrz144nVCy",
    "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615"
  }
```

新しいキーチェーン作成のためのローカルクライアントサイド関数。

オプションで、キーチェーンを作成するのに決定性シードを用いる単一のパラメーター、'seed'が提供されることができます。 このシードは最低32要素の長さの数字の配列であるべきです。 同じシードでこの関数を呼び出すと、同一のBIP32キーチェーンが生成されます。<aside class="warning"> キーチェーンの作成は、あなたのビットコインを安全に保護するために不可欠のステップです。 新しいキーチェーンを生成する時、このAPIは業界標準に準拠した乱数ジェネレーターをを用います。 自身でシードを提供する場合、作成する際、細心の注意を払わなければいけません。 </aside> 

新しいキーチェーンのためのxprvとxpubを含むオブジェクトを返します。作成されたキーチェーンはBitGoサービスに知られません。 BitGoサービスで利用するには、Keychains.Add APIを使用します。

セキュリティ上の理由から、[暗号化](#encrypt)の上、盗難を防ぐため元のxprvを即座に破壊することを強く推奨します。

## Add Keychain　キーチェーンを追加する　

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
    // handle error
  }
  // etc
});
```

ユーザーの新しいキーチェーンを登録します。少なくとも公開鍵を指定する必要があります。暗号化された秘密鍵はアップロードされることができますが、サーバに対して不透明として扱われます。

アドレスとともに暗号化された秘密鍵を提供する目的は、 BitGoに接続している時ユーザーが、自ら格納する負担なしに彼らの鍵に安全にアクセスできるためです。 ユーザーからの強いパスワードで、サーバに格納された全ての秘密鍵を暗号化することを強くお勧めします。 暗号化はクライアントで実行する必要があります。 便宜のため、BitGoの's[暗号化/復号化関数](#encrypt)を使用できますが、あなたの望む任意の暗号化を使用することができます。<aside class="warning"> あながた暗号化されたxprvを提供する場合、このキーチェーンのセキュリティはあなたの暗号化と同程度です。暗号化はあなたの責任です。 </aside> 

### HTTP Request HTTPリクエスト

`POST /api/v1/keychain`

### BODY Parameters BODYパラメーター

| パラメーター        | 種類  | 必須か | 説明                         |
| ------------- | --- | --- | -------------------------- |
| xpub          | 文字列 | YES | このキーチェーンのBIP32 xpub        |
| encryptedXprv | 文字列 | NO  | 暗号化された、このキーチェーンのBIP32 xpub |

> キーチェーンモデルの応答の例

```json
{
  "xpub": "xpub661MyMwAqRbcGn6m3YB7CJ2ToyUJYEsBpCc2UDJP9s3hzFif9dKucLotrJBbLgNqojM4q4Sddweka1WG2NvMccYyo3SpnfRrTvMuXUTpHwC",
  "encryptedXprv": "<encrypted data here>",
  "path": "m",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615"
}
```

### Response 応答

キーチェーンモデルオブジェクトを返します。

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |

## Create BitGo Keychain BitGo キーチェーンを作成する

```shell
curl -X POST \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/keychain/bitgo
```

```javascript
bitgo.keychains().createBitGo({}, function callback(err, keychain) {
  if (err) {
    // handle error
  }
  console.dir(keychain);
});
```

BitGoのサーバに新しいキーチェーンを作成し、呼び出し元にパブリックキーチェーンを返します。

### HTTP Request HTTPリクエスト

`POST /api/v1/keychain/bitgo`

### BODY Parameters BODYパラメーター

なし。

> キーチェーンモデルの応答の例

```json
{
  "xpub": "xpub661MyMwAqRbcGzVAChrAGb6MYDrAXndUC7h8T7AF8UhfbjS7Au7UKTXmVXaFasQPdfmnUjccreRTMrW7kTmjzwMqVrTHNAFs8M3CXTJpcnL",
  "isBitGo": true,
  "path": "m",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615"
}
```

### Response 応答

キーチェーンモデルオブジェクトを返します。

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |

## Create Backup Keychain バックアップキーチェーンを作成する

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

鍵のリカバリーのサービスに特化したサードパーティに、新しいバックアップキーチェーンを作成します。 このキーチェーンはサードパーティサービスに保存され、リカバリーの目的だけで使用されます。

### HTTP Request HTTPリクエスト

`POST /api/v1/keychain/backup`

### BODY Parameters BODYパラメーター

| パラメーター   | 種類  | 必須か | 説明                                        |
| -------- | --- | --- | ----------------------------------------- |
| provider | 文字列 | YES | 暗号鍵リカバリーシステム（KRS）の名前または使用するバックアップキープロバイダー |

> キーチェーンモデルの応答の例

```json
{
  "xpub": "xpub661MyMwAqRbcGzVAChrAGb6MYDrAXndUC7h8T7AF8UhfbjS7Au7UKTXmVXaFasQPdfmnUjccreRTMrW7kTmjzwMqVrTHNAFs8M3CXTJpcnL",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615",
  "path": "m"
}
```

### Response 応答

キーチェーンモデルオブジェクトを返します。

## Get Keychain　キーチェーンを取得する

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

xpubでキーチェーンをルックアップする<aside class="info"> この操作では、Unlock APIを使用してセッションをアンロックする必要があります。 </aside> 

### HTTP Request HTTPリクエスト

`POST /api/v1/keychain/:xpub`

### URL Parameters URLパラメーター

| パラメーター | 種類  | 必須か | 説明                 |
| ------ | --- | --- | ------------------ |
| xpub   | 文字列 | YES | ルックアップするBIP32 xpub |

> キーチェーンモデルの応答の例

```json
{
  "xpub": "xpub661MyMwAqRbcGn6m3YB7CJ2ToyUJYEsBpCc2UDJP9s3hzFif9dKucLotrJBbLgNqojM4q4Sddweka1WG2NvMccYyo3SpnfRrTvMuXUTpHwC",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615",
  "encryptedXprv": "<client-encrypted data here>",
  "path": "m"
}
```

### Response 応答

キーチェーンモデルオブジェクトを返します。

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |
| 404 Bad Request  | Xpub が見つからなかった             |

## Update Keychain キーチェーンを更新する

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

キーチェーンを更新します。xprvの新しいバージョンを保存したい場合（例えばxprvを暗号化するのに使用したパスワードを変更した場合）に使用されます。<aside class="info"> この操作では、Unlock APIを使用してセッションをアンロックする必要があります。 </aside> <aside class="warning"> encryptedXprvを変更すると、既存の値が上書きされます。 新しい値が正しくない、あるいは新しい値へのパスワードを忘れた場合、このキーチェーンで署名するあなたの能力は永遠に失われます。 </aside> 

### HTTP Request HTTPリクエスト

`PUT /api/v1/keychain/:xpub`

### BODY Parameters BODYパラメーター

| パラメーター        | 種類  | 必須か | 説明                            |
| ------------- | --- | --- | ----------------------------- |
| encryptedXprv | 文字列 | NO  | 新たに暗号化された、このキーチェーンのBIP32 xpub |

> キーチェーンモデルの応答例

```json
{
  "xpub": "xpub661MyMwAqRbcGzVAChrAGb6MYDrAXndUC7h8T7AF8UhfbjS7Au7UKTXmVXaFasQPdfmnUjccreRTMrW7kTmjzwMqVrTHNAFs8M3CXTJpcnL",
  "encryptedXprv": "<encrypted data here>",
  "ethAddress":"0x49e03847622a266335e2688be6e71a4975fe9615",
  "path": "m"
}
```

### Response 応答

キーチェーンモデルオブジェクトを返します。

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |
| 404 Not Found    | Xpub が見つからなかった             |

# Wallets ウォレット

全てのBitGoウォレットはマルチシグ、階層的、かつ決定性のウォレットです。 マルチシグウォレットは*N*個のキーから成り、トランザクションが有効となる前のトランザクションの署名に*M*個のキーを必要とします。 *M-of-N* ウォレットと呼ばれるものです。

BitGoは現在、2-of-3ウォレットのみサポートしています。ポリシー層を用いてm-of-nのアクセス許可モデルをサポートします。

ウォレットを作成するには、3つのキーチェーンが提供されなければなりません。 最初の2つのキーチェーンはユーザーによって提供されます、最後のは、BitGoキーチェーンである必要があります。 BitGoは最初の2つのキーの公開部分を見ることができる一方で、決してこれらのキーのプライベートな部分にアクセスできず、よってユーザー抜きに取引を行うことはできません。 BitGoの単一のキーはトランザクションに署名するのに十分でなく、またBitGoはユーザーが設定したポリシーに従ってのみ、このキーを用います。

## ウォレットの一覧を取得する

```shell
curl -X GET \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/wallet
```

```javascript
var wallets = bitgo.wallets();
wallets.list({}, function callback(err, wallets) {
// handle error, do something with wallets
for (id in wallets) {
  var wallet = wallets[id].wallet;
  console.log(JSON.stringify(wallet, null, 4));
}
});
```

そのユーザーのウォレットの一覧を取得します

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet`

> 応答の例

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
                    "... truncated for clarity ..."
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
                    "... truncated for clarity ..."
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

### QUERY Parameters クエリ パラメーター

| パラメーター | 種類 | 必須か | 説明                                     |
| ------ | -- | --- | -------------------------------------- |
| skip   | 数字 | NO  | 一覧取得を開始するインデックス番号。既定値は0。               |
| limit  | 数字 | NO  | 単一コール(default=25, max=250) で返す結果の最大の件数 |

### Response 応答

ウォレットモデルオブジェクトの配列を返します。

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |

## Add Wallet ウォレットを追加する<aside class="warning"> このメソッドは上級APIユーザー向けで、手動でのキーの生成、ユーザーの仕様、そしてバックアップのキーxPubを可能にします。 SDKのほとんどの場合、

[ウォレットをキーチェーンと作成](#create-wallet-with-keychains) がウォレットからビットコインを送るためのよりシンプルで推奨のSDKのメソッドになります。 </aside> 

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
    // handle error
  }
  console.dir(wallet);
});
```

このAPIはユーザーの新しいウォレットを作成します。新しいウォレットとともに利用するキーチェーンは、このAPIを利用するのに先立ってBitGoに登録する必要があります。

BitGoは現在、2-of-3 (例： m=2 and n=3) ウォレットのみをサポートしています。 3つ目のキーチェーン、そして3番目のキーチェーン**だけ**は、*BitGoキーでなければなりません*。 1つめのキーチェーンは慣例的にユーザーキーで、その**暗号化された**xprivはBitGoに保管されます。

BitGoウォレットは、3つのキーチェーンともに現在**m/0/0**をrootとしてハードコードされています（ただし、古いレガシーウォレットは異なるキーのパスを使用している場合があります）。 ルートの下で、ウォレットは0と1のアドレスの2つのチェーンをサポートします。 **0-chain** は外部の受信アドレスは向けで、一方**1-chain** は内部の(お釣りの) アドレスです。

最初のウォレットの受取アドレスはBIP32のパス **m/0/0/0/0**にあり、同時にBitGoのシステム内のウォレットを参照するためのIDです。 最初のお釣りアドレスは**m/0/0/1/0**にあります。 </aside>

### HTTP Request HTTPリクエスト

`POST /api/v1/wallet`

### BODY Parameters BODYパラメーター

| パラメーター                          | 種類    | 必須か | 説明                                                   |
| ------------------------------- | ----- | --- | ---------------------------------------------------- |
| label                           | 文字列   | YES | このウォレットのラベル                                          |
| m                               | 数字    | YES | 取得に必要な署名の数（2でなければならない）                               |
| n                               | 数字    | YES | ウォレットにあるキーの数（3でなければならない）                             |
| keychains                       | 配列    | YES | このウォレットで使用する**n**個のキーチェーンxpubsの配列; BitGoキーでなければならない。 |
| enterprise                      | 文字列   | NO  | このウォレットを作成するエンタープライズID。                              |
| disableTransactionNotifications | ブーリアン | NO  | ウォレットトランザクション通知を止めるにはtrueに設定                         |

> 応答の例

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

### Response 応答

ウォレットモデルオブジェクトを返します。

### Errors　エラー

| 応答                 | 説明                    |
| ------------------ | --------------------- |
| 400 Bad Request    | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized   | 認証パラメーターが一致しない        |
| 406 Not acceptable | 提供されたキーチェーンの一つが受け入れ不可 |

## Get Wallet ウォレットを取得する

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
    // handle error
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
    // handle error
  }
  // Use wallet object here
  console.dir(wallet);
  console.dir(wallet.balance());
});

```

ウォレット情報をルックアップし、残高、アクセス許可等を含むウォレットモデルを返します。ウォレットのIDはその最初の受信アドレスです(/0/0)。

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:id`

> 応答の例

```json
{
  "id":"2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb",
  "label":"TestNet Wallet",
  "isActive":true,
  "type":"safehd",
  "private":{
    "keychains":[
      {
        "xpub":"xpub681KE1ZbZM8UNecAGowRFH1vJfxrhzA3fMwKHB5yRs7YGU4FQUPJdbZdTaRuyweAXGDSrJt3AWDuDsoXh4CD4h43GPPzAq2H7Cb18xHGivZ",
        "path":"/101",
        "_id":"536d3cc573c8329a78000016"
      },
      {
        "xpub":"xpub661MyMwAqRbcGZp3eJrpEW1VzomFeDqs2nd9vdZhLFdazv2ekEznRZB2BTPVwrcacYTCvy7mPjxw3F9joMCji4WFgxsgd7J5SxkHuRbUNjx",
        "path":"/102",
        "_id":"536d3cc573c8329a78000015"
      },
      {
        "xpub":"xpub661MyMwAqRbcGR4F5XxMfiHGPycWehJT9XA5KZJg4aBcNZogiWnrDMowki4ZsCtZdWFUtHRxMnhTnm9XMEyxSVV7Am9eG7xUnxL3aoAt88d",
        "path":"/103",
        "_id":"536d3cc573c8329a78000014"
      }
    ]
  },
  "permissions":"admin,spend,view",
  "admin":{
    "policy":[
      {
        "type":"transactionLimit",
        "condition":{
          "currency":"BTC",
          "amount":10
        },
        "action":{
          "type":"getApproval"
        }
      },
      {
        "type":"dailyLimit",
        "condition":{
          "currency":"BTC",
          "amount":50
        },
        "action":{
          "type":"getApproval"
        }
      }
    ],
    "users":[
      {
        "user":"51f4a86305b9442c68000005c4666e34ff33aba3d78a2d3197e7b46b",
        "permissions":"admin,spend,view"
      },
      {
        "user":"53532a4b43fd69a42f000005f0a2ed87fd8b020040739beb513524b5",
        "permissions":"admin,spend,view"
      },
      {
        "user":"52e41fcec1a256b31c00001a5ba4eff09976d7278cbce93fcfeb8b25",
        "permissions":"admin,spend,view"
      },
      {
        "user":"53796821a75b0358c626a4ad16fa7fc4f824cbb58e4a88a5bbc04611",
        "permissions":"spend,view"
      }
    ]
  },
  "spendingAccount":true,
  "confirmedBalance":39564772124,
  "balance":39564772124,
  "pendingApprovals":[ ],
  "canSendInstant": true
}
```

### Response 応答

ウォレットモデルオブジェクトを返します。

| フィールド            | 説明                                                  |
| ---------------- | --------------------------------------------------- |
| id               | ウォレットのid(同時に最初の受信アドレス)                              |
| label            | UIに表示されている通りのウォレットのラベル                              |
| index            | チェーン内(0, 1, 2, ...) のアドレスのインデックス                    |
| private          | キーチェーンの要約版が含まれている                                   |
| permissions      | このウォレットへのユーザーのアクセス許可                                |
| admin            | ウォレットの管理者に関するポリシー情報                                 |
| pendingApprovals | ウォレットの保留中のトランザクション承認                                |
| confirmedBalance | 確認された残高                                             |
| balance          | 確認が0回のトランザクションを含む残高                                 |
| canSendInstant   | ウォレットが、二重支払いに対するBitGoの保証が付いた即座取引を送信する資格を持つかを示すブーリアン |

### Errors　エラー

| 応答                 | 説明                    |
| ------------------ | --------------------- |
| 400 Bad Request    | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized   | 認証パラメーターが一致しない        |
| 404 Not Found      | ウォレットが見つからなかった        |
| 406 Not acceptable | 提供されたキーチェーンの一つが受け入れ不可 |

## Create Wallet With Keychains キーチェーンと使用するウォレットを作成

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 
上級ユーザーはCreate Wallet API を検討してください。

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

このメソッドはウォレットを作成する簡単な方法として、クライアントSDKで利用可能です。メソッドは次のことを行います。

  1. ユーザー キーチェーンとバックアップのキーチェーンを作成する
  2. ユーザー キーチェーンを暗号化する
  3. 暗号化されたユーザーキーチェーンとバックアップキーチェーンをBitGoにアップロードする
  4. サービス上で BitGo キーを作成する
  5. 上の公開鍵でBitGoでウォレットを作成する<aside class="warning"> ユーザーが彼らのユーザーとバックアップのキーを印刷/バックアップを取ることは**非常に重要**です。 やっておかなければ、資金の喪失という結果になり得ます！ </aside> 

### BitGo Instant Wallets BitGo インスタントウォレット

デフォルトで、このメソッドはバックアップのキーチェーンをローカルで作成します。 BitGo Instantを送信するのに使用できるウォレットを作成するには、**backupXpubProvider**パラメーターを使用して、暗号鍵リカバリーシステム(KRS)を指定します（例：keyternal）。

### Method Parameters メソッドのパラメーター

| 名                               | 種類    | 必須か | 説明                                                                                                            |
| ------------------------------- | ----- | --- | ------------------------------------------------------------------------------------------------------------- |
| passphrase                      | 文字列   | YES | BitGoへの送信前に、ウォレットのユーザーキーを暗号化するのに使用されるパスフレーズ                                                                   |
| label                           | 文字列   | YES | このウォレットのラベル                                                                                                   |
| backupXpub                      | 文字列   | NO  | 決して2つの秘密鍵が同じマシンに存在しないよう、もう一つのデバイスで作成されたバックアップキーチェーンの公開鍵。あなたのキーをリモートでホスティングするオプションとしてbackupXpubProviderも参照下さい。 |
| backupXpubProvider              | 文字列   | NO  | お望みの暗号鍵リカバリーシステム(KRS)でバックアップxPubを作成する（例：keyternal）。そうすると、そのウォレットはBitGo Instantと互換性を持ちます。                      |
| enterprise                      | 文字列   | NO  | このウォレットを作成するエンタープライズID。                                                                                       |
| disableTransactionNotifications | ブーリアン | NO  | ウォレットトランザクション通知を止めるにはtrueに設定。                                                                                 |

> 応答の例

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
    

### Response 応答

| 応答             | 説明                                                     |
| -------------- | ------------------------------------------------------ |
| wallet         | ウォレットモデル オブジェクト                                        |
| userKeychain   | 暗号化されたxprvがBitGoに格納された、新たに作成されたユーザーキーチェーン　ーバックアップを取ること |
| backupKeychain | 新たに作成されたバックアップキーチェーン ーバックアップを取ること                      |

### Errors　エラー

| 応答                 | 説明                    |
| ------------------ | --------------------- |
| 400 Bad Request    | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized   | 認証パラメーターが一致しない        |
| 406 Not acceptable | 提供されたキーチェーンの一つが受け入れ不可 |

# Wallet Operations - Basic ウォレットの操作 ー基礎編

各ウォレットは多くのアドレスから成り、それぞれのアドレスをビットコインの受取りに使用できます。 Wallet APIはユーザーがウォレットと対話するための役に立つインターフェースを提供します。

## Create Address アドレスを作成する

既存のウォレットのための新たなアドレスを作成する。 BitGoウォレットは、0、1と呼ばれる2つの独立したアドレスのチェーンで構成されています。 0-チェーンは通常資金の受取に使用され、一方1-チェーンはウォレットから出費する際、お釣りの作成に内部的に使用されます。 プライバシーを最大化する助けとするには、着信した各トランザクションごとに新しいアドレスを生成するのがベストプラクティスと見なされます。

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

### HTTP Request HTTPリクエスト

`POST /api/v1/wallet/:walletId/address/:chain`

### URL Parameters URLパラメーター

| パラメーター   | 種類              | 必須か | 説明       |
| -------- | --------------- | --- | -------- |
| walletid | ビットコインアドレス(文字列) | YES | ウォレットのID |
| チェーン     | 数字              | YES | 0 または 1  |

> 応答の例

```json
{
  "address": "3GonoRKFSzcqJCktZPA2NHDd3jJoH4154o",
  "chain": 0,
  "index": 42,
  "path": "/0/42",
  "redeemScript": "522102d83bba35a8022c247b645eed6f81ac41b7c1580de550e7e82c75ad63ee9ac2fd2103aeb681df5ac19e449a872b9e9347f1db5a0394d2ec5caf2a9c143f86e232b0d92103d728ad6758d4784effea04d47baafa216cf474866c2d4dc99b1e8e3eb936e73053ae"
}
```

### Response 応答

ウォレットと紐付けられる新しいビットコインアドレスを返す。

| フィールド        | 説明                               |
| ------------ | -------------------------------- |
| address      | 連鎖アドレス                           |
| chain        | the chain (0 or 1)               |
| index        | チェーン内(0, 1, 2, ...) のアドレスのインデックス |
| path         | ウォレットのrootに対するアドレスのBIP32パス       |
| redeemScript | アドレスの redeemScript               |

### Errors　エラー

| 応答                 | 説明                                 |
| ------------------ | ---------------------------------- |
| 400 Bad Request    | 要求パラメーターが見つからないか正しくない              |
| 401 Unauthorized   | 認証パラメーターが一致しない                     |
| 403 Forbidden      | ウォレットがマルチシグBIP32(SafeHD) ウォレットではない |
| 404 Not Found      | ウォレットが見つからなかった                     |
| 406 Not acceptable | 提供されたキーチェーンの一つが受け入れ不可              |

## Send Coins to Address アドレスにコインを送信する

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
ローカル メソッドとしてのみ使用できます (BitGo Express) 
上級ユーザーはSend Transaction API を検討してください。

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

コインをBitGoウォレットから指定のビットコインアドレスへ送る最も簡単な方法。<aside class="info"> この操作では、Unlock APIを使用してセッションをアンロックする必要があります。 </aside> 

このメソッドはクライアント側で次を行います：

* 格納されているキーチェーンについてサーバで（暗号化された秘密鍵で）ウォレットをポーリングすることにより、ユーザーキーチェーンを取得する
* ユーザー キーを復号化します
* 指定アドレスへのトランザクションを作成し、お釣りは新たに作成されたウォレットのチェーンアドレス（/1 のパス）に送信される
* 復号化されたユーザー キーでトランザクションに署名します

処理されるために、部分的に署名されたトランザクションをBitGoサーバに送信します。そこで私達は：

* 潜在的に、他のウォレット管理者からの追加の承認を集める
* 最後の署名を適用する
* ビットコインP2Pネットワークへトランザクションをブロードキャストする

### Parameters パラメーター

| 名                            | 種類    | 必須か | 説明                                                                                                                                                                       |
| ---------------------------- | ----- | --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| address                      | 文字列   | YES | 宛先ビットコインアドレス                                                                                                                                                             |
| amount                       | 数字    | YES | 送信される額（単位はSatoshi）、例 1BTCの1/10 は100.1*1e8                                                                                                                                |
| walletPassphrase             | 文字列   | YES | ウォレットのパスフレーズ、暗号化されたユーザーキーを（クライアント側で）復号するのに使用される                                                                                                                          |
| fee                          | 数字    | NO  | 手数料（単位はSatoshi）、空欄のままだと自動で検出される。十分であることが確実でない限り指定しないで下さい。                                                                                                                |
| message                      | 文字列   | NO  | ユーザーが提供した文字列(ブロックチェーンに送られることはない)                                                                                                                                         |
| feeTxConfirmTarget           | 数字    | NO  | キロバイトごとの手数料を計算し、この数のブロックでのトランザクションの確認をターゲットにする。デフォルト: 2 最小：2 最大: 20                                                                                                      |
| minConfirms                  | 数字    | NO  | 一定の数の確認があった消費されていないインプットだけを選択する。これを1に設定し、enforceMinConfirmsForChangeを使用することを私達は推奨します。                                                                                    |
| enforceMinConfirms ForChange | ブーリアン | NO  | デフォルトはfalse。 ウォレットのお釣りアドレスからの、まだ消費されていないそれのminConfirms （上で説明）数の確認を要求するには、trueに設定する。 Falseに設定された場合、minConfirmsはユーザーのウォレットとは異なるウォレットからの、まだ消費されていないそれにのみ適用されます(お釣りでないアドレス)。 |
| sequenceId                   | 文字列   | NO  | このトランザクションの状態を、署名の前後で一意に識別するのに使用することができるユーザー提供のカスタム文字列                                                                                                                   |
| instant                      | ブーリアン | NO  | BitGoの二重支払いに対するインスタント保証が付いたトランザクションの送信をリクエストする際、trueに設定                                                                                                                  |
| otp                          | 文字列   | NO  | "getOTP"アクションタイプのポリシーをを迂回するのに使用される7桁のコード。詳細については[ウォレットポリシー](#wallet-policy)を参照してください。                                                                                    |

> 応答の例

```json
{ "tx": "0100000001a366332472cebceabdd541beb582d5dbaaecccdbec639b0a2d2b...",
  "hash": "b3bd8ac76de2340c1159337acdfcaabf08a9470e8157870bd1d84631a0acc67b",
  "fee": 10000,
  "status": "signed",
  "instant": true,
  "instantId": "56651c4c4e82b975616b58647c77fe1dc"
}
```

> 応答の例(ウォレットポリシーのため保留中の承認が必要)

```json
{
  "error": "exceeds daily limit",
  "pendingApproval": "56050b1368217cde3667fcfe0157556b",
  "triggeredPolicy": "5578defa5e44dbcb20c0caf98b297ad7",
  "status": "pendingApproval"
}
```

### Success Response 成功レスポンス

| フィールド   | 説明                                                  |
| ------- | --------------------------------------------------- |
| tx      | 署名されたトランザクションの16進数でエンコードされた形式                       |
| hash    | トランザクション id                                         |
| fee     | このトランザクションの一部としてビットコインマイナーに送信されたsatoshi単位の金額        |
| feeRate | このトランザクションの一部としてビットコインマイナーに送信された1KBあたりのsatoshi単位の金額 |

### Policy/Failure Response ポリシー/エラー応答

| フィールド           | 説明                                 |
| --------------- | ---------------------------------- |
| error           | この保留中の承認をトリガーしたポリシーからのメッセージ        |
| pendingApproval | 保留中の承認のidで、別のユーザーにより承認される必要がある     |
| otp             | 発射されたポリシーが"getOTP"タイプだった場合、trueに設定 |
| triggeredPolicy | この保留中の承認をトリガーしたポリシーのid             |
| status          | トランザクションのステータス                     |

## Send Coins to Multiple Addresses 複数のアドレスにコインを送信する

```javascript
var recipients = [];
recipients.push({address: '2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD', amount: 0.1 * 1e8});
recipients.push({address: '2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu', amount: 0.2 * 1e8});

var walletPassphrase = 'incorrect horse battery stable' // replace with wallet passphrase

bitgo.wallets().get({id: walletId}, function(err, wallet) {
  if (err) { console.log("Error getting wallet!"); console.dir(err); return process.exit(-1); }
  console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));

  wallet.sendMany({ recipients: recipients, walletPassphrase: walletPassphrase }, function(err, result) {
    if (err) { console.log("Error sending coins!"); console.dir(err); return process.exit(-1); }
```

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 
上級ユーザーはSend Transaction API を検討してください。

WALLETID='2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa'
WALLETPASSPHRASE='watashinobitcoin'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"recipients\": [{ \"address\": \"2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD\", \"amount\": 1500000}, { \"address\": \"2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu\", \"amount\": 2500000 }], \"walletPassphrase\": \"$WALLETPASSPHRASE\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/sendmany
```

ビットコインを、一回のトランザクションで複数の宛先アドレスに送信するのに便利な関数。<aside class="info"> この操作では、Unlock APIを使ってセッションをアンロックすることが必要です。 </aside> 

### Parameters パラメーター

| 名                            | 種類    | 必須か | 説明                                                                                             |
| ---------------------------- | ----- | --- | ---------------------------------------------------------------------------------------------- |
| recipients                   | 文字列   | YES | 受信者オブジェクトの配列とそれぞれに送信する金額。例 [{address: '38BKDNZbPcLogvVbcx2ekJ9E6Vv94DqDqw', amount: 1500}, ..] |
| message                      | 文字列   | NO  | そのトランザクションに関するメモ                                                                               |
| fee                          | 数字    | NO  | 手数料（単位はSatoshi）、空欄のままだと自動で検出される。十分であることが確実でない限り指定しないで下さい。                                      |
| feeTxConfirmTarget           | 数字    | NO  | キロバイトごとの手数料を計算し、この数のブロックでのトランザクションの確認をターゲットにする。デフォルト: 2 最小：2 最大: 20                            |
| minConfirms                  | 数字    | NO  | 一定の数の確認があった消費されていないインプットだけを選択する。これを1に設定し、enforceMinConfirmsForChangeを使用することを私達は推奨します。          |
| enforceMinConfirms ForChange | ブーリアン | NO  | デフォルトではfalse。トランザクションを構築する時、minConfirmsはウォレットからでない消費されなかった資金の時のみ適用されます。                        |
| sequenceId                   | 文字列   | NO  | このトランザクションの状態を、署名の前後で一意に識別するのに使用することができるユーザー提供のカスタム文字列                                         |
| otp                          | 文字列   | NO  | "getOTP"アクションタイプのポリシーをを迂回するのに使用される7桁のコード。詳細については[ウォレットポリシー](#wallet-policy)を参照してください。          |

> 応答の例

```json
{ "tx": "0100000001c69d05d3897a25c611324a935d0c688669dc416cb8d8e9ebb36e364fa79547c8000..",
  "hash": "27374a97df2cd8a63640dd7a40fce9a1d8dfeec3cf7a8b2fbf1ef609f4a5370d",
  "fee": 10000 }
```

### Response 応答

| フィールド   | 説明                                                  |
| ------- | --------------------------------------------------- |
| tx      | 署名されたトランザクションの16進数でエンコードされた形式                       |
| hash    | トランザクション id                                         |
| fee     | このトランザクションの一部としてビットコインマイナーに送信されたsatoshi単位の金額        |
| feeRate | このトランザクションの一部としてビットコインマイナーに送信された1KBあたりのsatoshi単位の金額 |

### Policy/Failure Response ポリシー/エラー応答

| フィールド           | 説明                                 |
| --------------- | ---------------------------------- |
| error           | この保留中の承認をトリガーしたポリシーからのメッセージ        |
| pendingApproval | 保留中の承認のidで、別のユーザーにより承認される必要がある     |
| otp             | 発射されたポリシーが"getOTP"タイプだった場合、trueに設定 |
| triggeredPolicy | この保留中の承認をトリガーしたポリシーのid             |
| status          | トランザクションのステータス                     |

## List Wallet Transactions ウォレットのトランザクションの一覧を表示

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
    // handle transactions
    console.log(JSON.stringify(transactions, null, 4));
  });
});
```

> 応答の例

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

該当するウォレットのトランザクションを、ブロックの高さ順（逆から）で取得する（未確認のトランザクションを最初に）。

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/tx`

### URL Parameters URL パラメーター

| パラメーター   | 種類              | 必須か | 説明       |
| -------- | --------------- | --- | -------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのID |

### QUERY Parameters クエリ パラメーター

| パラメーター | 種類    | 必須か | 説明                                     |
| ------ | ----- | --- | -------------------------------------- |
| skip   | 数字    | NO  | 一覧取得を開始するインデックス番号。既定値は0。               |
| limit  | 数字    | NO  | 単一コール(default=25, max=250) で返す結果の最大の件数 |
| コンパクト  | ブーリアン | NO  | トランザクション結果でインプットとアウトプットを省略する           |

### Response 応答

トランザクションオブジェクトの配列を返す。そのトランザクションが関わったあらゆるウォレットやビットコインアドレスにどのように影響を与えたかに関する概要情報を、各トランザクションは含みます。

### Errors　エラー

| Response 応答      | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |

## Get Wallet Transactions ウォレットのトランザクションを取得する

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

> 応答の例

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

ウォレットのトランザクションに関する情報を取得します。

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/tx/:txId`

### URL Parameters URL パラメーター

| パラメーター   | 種類                  | 必須か | 説明                |
| -------- | ------------------- | --- | ----------------- |
| walletId | ビットコインアドレス(文字列)     | YES | ウォレットのID          |
| txId     | トランザクション ハッシュ (文字列) | YES | 取得するトランザクションのハッシュ |

### Response 応答

トランザクション オブジェクトを返します。

| パラメーター        | 種類    | 説明                                                                                               |
| ------------- | ----- | ------------------------------------------------------------------------------------------------ |
| id            | 文字列   | トランザクションのハッシュ                                                                                    |
| hex           | 文字列   | トランザクションの未処理の16進数                                                                                |
| date          | 日時    | このトランザクションが最初に見られた日付                                                                             |
| blockhash     | 文字列   | ブロックのハッシュ値、このトランザクションが既に確認されている場合                                                                |
| height        | 数字    | このトランザクションが最初に見られたブロックの高さ                                                                        |
| confirmations | 数字    | このトランザクションがブロックチェーンの一部であった期間のブロックの数                                                              |
| entries       | 配列    | 連結されたトランザクションのエントリ、ネット(正味) のインプット/アウトプットを考慮して                                                    |
| outputs       | 配列    | ウォレットのaccount、value、vout、voutインデックス、isMine、chain (普通のアドレスは0、お釣りのアドレスは1）を含む、トランザクションのアウトプットに関する情報 |
| fee           | 数字    | このトランザクションについてマイナーに支払われたSatoshi単位の金額                                                             |
| pending       | ブーリアン | トランザクションがブロックチェーン上でまだ確認されていない場合、trueに設定する                                                        |
| instant       | ブーリアン | このトランザクションがBitGo instantを使用して送信された場合、trueに設定する                                                   |
| instantId     | 文字列   | BitGoからの保証を参照または取得するのに使用されるインスタントトランザクションの識別子                                                    |
| sequenceId    | 文字列   | シーケンスid (トランザクションが送信された時に提供される一意のカスタムデータ)                                                        |
| comment       | 文字列   | トランザクションに設定されたコメント                                                                               |

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |
| 404 Not Found    | ウォレットでトランザクションが見つからなかった    |

## ウォレットのアドレスの一覧を表示する

ウォレットについて、New Address APIを使用してインスタンス化されたアドレスのリストを取得します。

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

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/addresses`

### URL Parameters URL パラメーター

| パラメーター   | 種類              | 必須か | 説明       |
| -------- | --------------- | --- | -------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのID |

### QUERY Parameters クエリ パラメーター

| パラメーター | 種類 | 必須か | 説明                                 |
| ------ | -- | --- | ---------------------------------- |
| chain  | 数字 | NO  | オプションとしてチェーン0またはチェーン1に制限する         |
| skip   | 数字 | NO  | この数の結果をスキップする                      |
| limit  | 数字 | NO  | 結果の数をこの数に制限する(default=25, max=500) |

> 応答の例

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

### Response 応答

ウォレットアドレスオブジェクトの配列を返します。

| フィールド   | 説明                        |
| ------- | ------------------------- |
| chain   | アドレスがどのチェーンにあるか(現在、0または1) |
| index   | チェーンの BIP32 インデックス        |
| path    | 財布からのBIP32パス              |
| address | ビットコインアドレス                |

### Errors　エラー

| Response 応答      | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |

## Get Single Wallet Address 単一のウォレットアドレスを取得する

ウォレット内のアドレスに関する情報を取得します。アドレスがウォレット内に存在しているかどうかチェックするのにも使用できます。

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

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/addresses/:address`

### URL Parameters URL パラメーター

| パラメーター   | 種類              | 必須か | 説明                |
| -------- | --------------- | --- | ----------------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのID          |
| address  | ビットコインアドレス(文字列) | YES | 情報を取得するウォレットのアドレス |

> 応答の例

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

### Response 応答

ウォレットアドレスオブジェクトを返します。

| フィールド        | 説明                                                  |
| ------------ | --------------------------------------------------- |
| address      | ルックアップされているビットコインアドレス                               |
| balance      | このアドレスの現在の残高 (単位はsatoshi)                           |
| chain        | このアドレスを生成するのに使用されたHDチェーン(ユーザーが生成したアドレスは0、お釣りアドレスは1) |
| index        | アドレスを生成するのに使用されたHDチェーン内のインデックス                      |
| path         | ウォレット内のアドレスのHDパス                                    |
| received     | このアドレスで受信した合計金額 (単位はsatoshi)                        |
| sent         | このアドレスで送信した合計金額 (単位はsatoshi)                        |
| txCount      | このアドレスでのトランザクションの合計数                                |
| redeemScript | このP2SHアドレスからの資金の消費に使用される可能性があるredeemScript          |

# Wallet Operations - Basic ウォレットの操作 ー上級編

これらの機能は上級レベルの開発者向けに利用可能、推奨となっています。 これらのAPIを使用することで、拡張された（ただし潜在的に複雑な）機能と強化されたトランザクション生成プロセスのコントロールが提供されます。

## Get Transaction By Sequence Id シーケンスidでトランザクションを取得する

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

> 応答の例

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

送信トランザクションを送信する時に渡されたウォレットシーケンスIDのトランザクションを取得します（sendCoinsまたはsendTransaction経由で） これは、自分の一意のIDを通じて、署名されていない/確認されていないトランザクションをトラッキングするのに役立ちます。ビットコイントランザクションIDは共同署名の前に定義されず、確認前改変されうるため。

BitGoによってまだ共同署名されていない保留中のトランザクションは、依然としてシーケンスidを持ちます。

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/tx/sequence/:sequenceId`

### URL Parameters URL パラメーター

| パラメーター     | 種類              | 必須か | 説明                           |
| ---------- | --------------- | --- | ---------------------------- |
| walletId   | ビットコインアドレス(文字列) | YES | ウォレットのID                     |
| sequenceId | カスタムのユーザー提供の文字列 | YES | 以前に発信トランザクションとともに送信された一意のid。 |

### Response 応答

履歴、そしてビットコインネットワーク上のトランザクションの状態を含むWalletTxオブジェクトを返す。

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |
| 404 Not Found    | ウォレットでトランザクションが見つからなかった    |

## List Wallet Unspents ウォレットの消費されなかった分の一覧を取得する

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
      // handle error, use unspents
      console.dir(wallet);
    });
  });
```

ウォレットについて、未使用の入力トランザクションのリストを取得します。

ビットコイントランザクションを作成するには、トランザクションの作成者は、そのトランザクションの作成に使用する一連のビットコイン「インプット」を蓄積することが必要になります。

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/unspents`

### URL Parameters URL パラメーター

| パラメーター   | 種類              | 必須か | 説明       |
| -------- | --------------- | --- | -------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのID |

### QUERY Parameters クエリ パラメーター

| パラメーター | 種類 | 必須か | 説明                                            |
| ------ | -- | --- | --------------------------------------------- |
| target | 数字 | NO  | APIは最低でもこの金額に達する十分な未使用分（単位はsatoshi）を返そうと試みます。 |
| skip   | 数字 | NO  | 一覧取得を開始するインデックス番号。既定値は0。                      |
| limit  | 数字 | NO  | 単一コール(default=100, max=250) で返す結果の最大の件数       |

> 応答の例

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

### Response 応答

未使用分のインプットのオブジェクトの配列を返します。

| フィールド         | 説明                                                               |
| ------------- | ---------------------------------------------------------------- |
| tx_hash       | 未使用のインプットのハッシュ値                                                  |
| tx_output_n | *tx_hash*からの未使用のインプットのインデックス                                     |
| value         | 未使用のインプットの値、単位はsatoshi                                           |
| script        | (16 進数形式で) スクリプト ハッシュを出力                                         |
| redeemScript  | 交換スクリプト                                                          |
| chainPath     | ウォレットに関連する未使用のアウトプットのBIP32 パス                                    |
| confirmations | 未使用分のトランザクションがブロックに含まれた時とその後見られたブロックの数                           |
| isChange      | これがこのウォレットからの以前の使用からの出力であることを示すブーリアンで、おそらく確認数が0だったとしても安全に使用可能    |
| instant       | 二重支払いに関する保証が付いたBitGo Instantトランザクションの作成にこの未使用分が使用できるかどうかを示すブーリアン |

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |

## Consolidate Unspents 未使用分を統合する

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
      // All the transactions used for consolidation, by reverse block height
      console.dir(consolidationTransactions);
    }
  );
});
```

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 
WALLETID="2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa"
WALLETPASSPHRASE="mypassword"

curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"walletPassphrase\": \"$WALLETPASSPHRASE\", \"target\": 1, \"minConfirms\": 1 }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/consolidateunspents
```

現在ウォレットに保持されている未使用分を、より小さい数へ合体（統合）します。 これは反復的（イタレーション）プロセスで、主にトランザクションサイズの制限と署名の速さによるものです。 各イタレーションごとにトランザクション手数料が必要です。

### Parameters パラメーター

| パラメーター                        | 種類  | 必須か | 説明                                          |
| ----------------------------- | --- | --- | ------------------------------------------- |
| target                        | 数字  | NO  | 関数を実行した後の、希望の未使用分の数                         |
| maxInputCountPerConsolidation | 数字  | NO  | 各イタレーションで使用される未使用分の最大の数。デフォルトで85。           |
| minConfirms                   | 数字  | NO  | 一定数の確認があった未使用分のインプットだけを選ぶ                   |
| walletPassphrase              | 文字列 | NO  | ウォレットのパスフレーズ                                |
| progressCallback              | 関数  | NO  | 各イテレーション後に呼び出されるクロージャ。進行状況をモニタリングするのに使用できる。 |

## Fan Out Unspents 未使用分のファンアウト

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
      // Transaction used to fan out unspents
      console.dir(fanoutTransaction);
    }
  );
});
```

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 
WALLETID="2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa"
WALLETPASSPHRASE="mypassword"

curl -X PUT \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"walletPassphrase\": \"$WALLETPASSPHRASE\", \"target\": 85, \"minConfirms\": 1 }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/fanoutunspents
```

（最小確認カウント等の選択基準と一致する）ウォレットの未使用分を取って、より大きい数の未使用分に広げます。

### Parameters パラメーター

| パラメーター           | 種類  | 必須か | 説明                        |
| ---------------- | --- | --- | ------------------------- |
| target           | 数字  | YES | 関数を実行した後の、希望の未使用分の数       |
| minConfirms      | 数字  | NO  | 一定数の確認があった未使用分のインプットだけを選ぶ |
| walletPassphrase | 文字列 | NO  | ウォレットのパスフレーズ              |

## Create Transaction　トランザクションの作成

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
      // The transaction with selected unspents, ready to be signed with signTransaction
      console.dir(transaction);
    });
  });
});
```

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 
WALLETID='2NB5G2jmqSswk7C427ZiHuwuAt1GPs5WeGa'

curl -X POST \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-d "{ \"recipients\": [{ \"address\": \"2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD\", \"amount\": 1500000}, { \"address\": \"2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu\", \"amount\": 2500000 }] }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/wallet/$WALLETID/createtransaction
```<aside class="warning"> このメソッドは上級APIユーザー向けです。ほとんどの場合、 \[sendCoins\](#send-coins-to-address) がウォレットからのビットコインの送信に推奨されるメソッドです。 </aside> 

そのウォレットの各アドレスからの未使用分をを使用して、ウォレットから複数の受信者を持つトランザクションを作成します。これはSDKでのみのクライアント側の機能です。

通常、signTransactionの前に使用され、signTransactionは作成されたトランザクションに署名します。お釣りはウォレット上で新たに作成されたお釣りアドレス（/1のパス）に送信されます。

これは上級のメソッドで、マイナー手数料と復号化されたキーチェーンを手動で指定（マイナー手数料は0も可能）ことを可能にします。

**注意**: 不十分な手数料を提供した場合、あなたのトランザクションは確認を得られず、未使用分がしばらく使用できないことがあります。

> 応答の例

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

### Parameters パラメーター

| パラメーター                       | 種類    | 必須か | 説明                                                                                             |
| ---------------------------- | ----- | --- | ---------------------------------------------------------------------------------------------- |
| recipients                   | 文字列   | YES | 受信者オブジェクトの配列とそれぞれに送信する金額。例 [{address: '38BKDNZbPcLogvVbcx2ekJ9E6Vv94DqDqw', amount: 1500}, ..] |
| fee                          | 数字    | NO  | 単位をSatoshiとする、ビットコインマイナーに支払われる絶対的な手数料。自動の場合「undefined」に設定。                                     |
| feeRate                      | 数字    | NO  | ビットコインマイナーに支払われるトランザクションサイズのKBあたりの手数料。自動の場合「undefined」に設定。                                     |
| feeTxConfirmTarget           | 数字    | NO  | キロバイトごとの手数料を計算し、この数のブロックでのトランザクションの確認をターゲットにする。デフォルト: 2 最小：2 最大: 20                            |
| minConfirms                  | 数字    | NO  | 一定の数の確認があった消費されていないインプットだけを選択する。これを1に設定し、enforceMinConfirmsForChangeを使用することを私達は推奨します。          |
| enforceMinConfirms ForChange | ブーリアン | NO  | デフォルトではfalse。トランザクションを構築する時、minConfirmsはウォレットからでない消費されなかった資金の時のみ適用されます。                        |
| minUnspentSize               | 数字    | NO  | 使用可能と考えられる未使用分の単位をsatoshiとする最低額。デフォルトで5460 (トランザクションダストスパムへの対抗として)                             |
| instant                      | ブーリアン | NO  | BitGoの二重支払いに対するインスタント保証が付いたトランザクションの送信をリクエストする際、trueに設定                                        |

## Sign Transaction トランザクションへ署名する

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
ローカル メソッドとしてのみ使用できます (BitGo Express) 
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
```<aside class="warning"> このメソッドは上級APIユーザー向けです。ほとんどの場合、 \[sendCoins\](#send-coins-to-address) がウォレットからのビットコインの送信に推奨されるメソッドです。 </aside> 

作成されたトランザクションの16進数、キーチェーンそして未使用分の情報を使用してマルチシグトランザクションに署名します(導出パスと交換スクリプト)。通常createTransactionからの出力と使用します。

オフラインで実行することができます。

これは、SDKでのみのクライアント側の機能です。

> 応答の例

```json
{"tx":"0100000003f6a05b9ab9d7c62cb70a662a4016cbd4740d1d6d35d3c903e3a74fd9f943d09c00....."}
```

### Parameters パラメーター

| パラメーター         | 種類            | 必須か | 説明                                           |
| -------------- | ------------- | --- | -------------------------------------------- |
| transactionHex | 文字列           | YES | The unsigned transaction, in hex string form |
| unspents       | 配列            | YES | unspentsオブジェクトの配列で、chainpathとredeemScriptを含む |
| keychain       | キーチェーン オブジェクト | YES | 利用できるxprvのプロパティを持つ復号化されたキーチェーン（オブジェクト）。      |

## Send Transaction トランザクションを送信する

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

    // Decrypt the user key with a passphrase
    keychain.xprv = bitgo.decrypt({ password: walletPassphrase, input: keychain.encryptedXprv });

    // Set recipients
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
```<aside class="warning"> このメソッドは上級APIユーザー向けです。ほとんどの場合、 \[sendCoins\](#send-coins-to-address) がウォレットからのビットコインの送信に推奨されるメソッドです。 </aside> 

部分的に署名されたトランザクションを送信します。 サーバは次のいずれかを行います： *トランザクションを拒否する *他のウォレットのアドミンから追加の承認を集める *最後の署名を適用し、ビットコインP2Pネットワークに提出する<aside class="info"> このAPIはUnlock APIを使用してセッションがアンロックされることを必要とします。 Unlock APIへの単一コールは、内部的に設定されたBitGoのクォータ（現在50BTCに設定）までの、全ての単一トランザクションまたは複数トランザクションを可能にします。 </aside> 

### HTTP Request HTTPリクエスト

`POST /api/v1/tx/send`

> 応答の例

```json
{
  "tx": "01000000022cbc51a1d73a7e8ef3feaf30ad51b08b53cf91c672ba73..",
  "hash": "b3bd8ac76de2340c1159337acdfcaabf08a9470e8157870bd1d846..",
  "status": "accepted",
  "instant": true,
  "instantId": "56651c4c4e82b975616b58647c77fe1dc"
}
```

### BODY Parameters BODYパラメーター

| パラメーター     | 種類              | 必須か | 説明                                                                                    |
| ---------- | --------------- | --- | ------------------------------------------------------------------------------------- |
| tx         | トランザクション オブジェクト | YES | 16進数文字列形式でのトランザクション                                                                   |
| sequenceId | 文字列             | NO  | このトランザクションの状態を、署名の前後で一意に識別するのに使用することができるユーザー提供のカスタム文字列                                |
| message    | 文字列             | NO  | ユーザーが提供した文字列(ブロックチェーンに送られることはない)                                                      |
| instant    | ブーリアン           | NO  | BitGoの二重支払いに対するインスタント保証が付いたトランザクションの送信をリクエストする際、trueに設定                               |
| otp        | 文字列             | NO  | "getOTP"アクションタイプのポリシーをを迂回するのに使用される7桁のコード。詳細については[ウォレットポリシー](#wallet-policy)を参照してください。 |

### Response 応答

16 進数エンコード形式で、送信されたトランザクションとそのハッシュ値を返します。

### Errors　エラー

| 応答                   | 説明                             |
| -------------------- | ------------------------------ |
| 400 Bad Request      | 要求パラメーターが見つからないか正しくない          |
| 401 Unauthorized     | 認証パラメーターが一致しない、またはアンロックが必要     |
| 402 Payment Required | このリクエストでのトランザクション手数料が低すぎるようです。 |

### Policy/Failure Response ポリシー/エラー応答

| フィールド           | 説明                                 |
| --------------- | ---------------------------------- |
| error           | この保留中の承認をトリガーしたポリシーからのメッセージ        |
| pendingApproval | 保留中の承認のidで、別のユーザーにより承認される必要がある     |
| otp             | 発射されたポリシーが"getOTP"タイプだった場合、trueに設定 |
| triggeredPolicy | この保留中の承認をトリガーしたポリシーのid             |
| status          | トランザクションのステータス                     |

## Get Instant Guarantee インスタント保証を得る

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

BitGo Instantは、私達による二重支払いに対する保証として、ウォレットプラットフォームの上に構築されています。 マルチシグウォレットの共同署名者として、BitGoがアウトプットを二重支払いすることは決してありません。 私達の約束は各トランザクションごとに暗号署名された保証によって裏付けられており、受信者がブロック確認の必要なしに資金を受け入れるのを可能にします。<aside class="info"> 誰でも即座のトランザクションを受け取ることができます。インスタントトランザクションの送信にはinstantと互換性がある / 暗号鍵リカバリーシステム（KRS）のBitGoウォレットが必要です。 </aside> 

### HTTP Request HTTPリクエスト

`GET /api/v1/instant/<id>`

> 応答の例

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

### Response 応答

金額とトランザクションIDを含む、instant保証のメッセージを返します。

| パラメーター         | 種類  | 説明                                                  |
| -------------- | --- | --------------------------------------------------- |
| amount         | 数字  | Instant保証の金額、単位はSatoshi                             |
| createTime     | 日時  | トランザクションが作成された時間                                    |
| guarantee      | 文字列 | 即座トランザクションを保証する BitGo によるメッセージ                      |
| id             | 文字列 | BitGo のインスタント保証 ID                                  |
| transactionId  | 文字列 | 保証されたトランザクションのハッシュ                                  |
| normalizedHash | 文字列 | 署名のない保証されたトランザクションのハッシュ                             |
| signature      | 文字列 | 紛争があった場合に備え監査記録を提供するための、暗号署名された保証                   |
| state          | 文字列 | BitGoによってモニタリングされるトランザクションの状態（あなたの方で措置を講じる必要はありません） |

### Verifying BitGo's Guarantee BitGoの保証を認証する

BitGoの保証は、パブリックビットコインアドレス（1BitGo3gxRZ6mQSEH52dvCKSUgVCAH4Rja）に対応する私達の会社の署名キーを使用して署名されます。

確認するには、私達の署名で保証を認証します。例：

`assert(bitcoin.Message.verify('1BitGo3gxRZ6mQSEH52dvCKSUgVCAH4Rja', signature, guarantee, process.config.bitcoin.network));`

署名が有効な場合、ブロック情報を全く必要としないでトランザクションを即座に受け取ることができます。 保証の署名を節約して& ローカルで署名して紛争があった場合に監査記録を提供することができます。

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |

## Get Wallet by Address アドレスでウォレットを取得する

アドレスを与えられ、そのアドレスの情報(残高も含む) とそのアドレスが関連するウォレットを返します。 １人で多数のアドレス/ウォレットを持っているが、アドレスが属するウォレットが不明な時に役に立ちます。

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

### HTTP Request HTTPリクエスト

`GET /api/v1/walletaddress/:address`

### URL Parameters URL パラメーター

| パラメーター  | 種類              | 必須か | 説明              |
| ------- | --------------- | --- | --------------- |
| address | ビットコインアドレス(文字列) | YES | 情報をルックアップするアドレス |

> 応答の例

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

### Response 応答

ウォレットアドレスオブジェクトを返します。

| フィールド        | 説明                                                  |
| ------------ | --------------------------------------------------- |
| address      | ルックアップされているビットコインアドレス                               |
| balance      | このアドレスの現在の残高 (単位はsatoshi)                           |
| chain        | このアドレスを生成するのに使用されたHDチェーン(ユーザーが生成したアドレスは0、お釣りアドレスは1) |
| index        | アドレスを生成するのに使用されたHDチェーン内のインデックス                      |
| path         | ウォレット内のアドレスのHDパス                                    |
| received     | このアドレスで受信した合計金額 (単位はsatoshi)                        |
| sent         | このアドレスで送信した合計金額 (単位はsatoshi)                        |
| txCount      | このアドレスでのトランザクションの合計数                                |
| redeemScript | このP2SHアドレスからの資金の消費に使用される可能性があるredeemScript          |
| wallet       | このアドレスがあるウォレットのベースアドレス（ウォレットID）　                    |

## Freeze Wallet 財布を凍結する

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

> 応答の例

```json
{
  "time" : "2016-04-01T03:51:39.779Z",
  "expires" : "2016-04-01T04:58:19.779Z"
}
```

ウォレットでの全ての使用活動を防止します。このコールは緊急時に使用されるように設計されており、デフォルトで一時間使用を防止します。

### Parameters パラメーター

| パラメーター   | 種類 | 必須か | 説明                        |
| -------- | -- | --- | ------------------------- |
| duration | 数字 | NO  | 使用活動の凍結の期間、秒数で。デフォルトで1時間。 |

### Response 応答

| フィールド   | 説明                |
| ------- | ----------------- |
| time    | 凍結のコマンドが呼び出された日付  |
| expires | 使用活動が認められるようになる日付 |

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |

# Wallet Sharing ウォレットの共有

BitGoウォレットは複数のユーザー間で共有することが出来ます。 ウォレットのすべてのユーザーは同じ秘密鍵を共有します（各ユーザーはそれを個別に暗号化することできますが）。

BitGoによって共有されるウォレットのセキュリティが施行され、ユーザーはログイン後、認証を行うことを求められます。 ウォレットのアクセス許可レベルは、ウォレットで各ユーザーが行えることを定義します。

### Wallet Permissions ウォレットの許可

| 許可    | 説明                                  |
| ----- | ----------------------------------- |
| View  | ウォレットでのトランザクションを表示                  |
| Spend | ウォレットポリシーの対象となるトランザクションを、ウォレットで開始する |
| Admin | ポリシーを変更し、ウォレットでユーザーと設定を管理           |

## Sharing a wallet ウォレットを共有する

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 

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

> 応答の例

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
```<aside class="info"> この操作では、Unlock APIを使ってセッションをアンロックすることが必要です。 </aside> 

ウォレットの共有は、もう一人のユーザーにウォレットを使用する許可をBitGoを通じ与えることを伴います。

受取り手がウォレットを使用するには、私達は彼らと秘密鍵を共有する必要があります。 BitGoの各ユーザーは登録プロセスの際、この目的のために公開と秘密のキーペアを作成します。

BitGo SDKはクライアント側で以下を行って、新たなウォレットの共有を作成します:

* 受け取るユーザーの共有する鍵を取得する（受取り手の公開鍵の派生パス）
* ローカルで共有されるウォレットを復号化する
* 上の公開鍵に対しウォレットを再暗号化し、受取り手だけが復号化できるようにする。
* 暗号化された鍵をBitGoサービスにアップロードし、BitGoサービスは受取り手に保留中の共有があることを知らせる。

### Parameters パラメーター

| パラメーター           | 種類    | 必須か | 説明                                        |
| ---------------- | ----- | --- | ----------------------------------------- |
| email            | 文字列   | YES | ウォレットを共有するユーザーのメールアドレス                    |
| permissions      | 文字列   | YES | コンマで区切った許可のリスト（例：view、spend、admin）        |
| walletPassphrase | 文字列   | NO  | 共有されているウォレットのパスフレーズ                       |
| skipKeychain     | ブーリアン | NO  | 帯域外でキーチェーンを取得する別のユーザーと、ウォレットを共有の場合trueに設定 |
| disableEmail     | ブーリアン | NO  | 追加されたユーザーに送信される通知メールを止めるには、trueに設定        |

### Response 応答

| フィールド       | 説明                                     |
| ----------- | -------------------------------------- |
| id          | ウォレット共有のid、受け入れるために使用                  |
| walletId    | 共有されているウォレットのid                        |
| walletLabel | ユーザーに提示するウォレットのラベル                     |
| fromUser    | ウォレットを共有しているユーザーのBitGo ID              |
| toUser      | ウォレットを受け取るユーザーのBitGo ID                |
| permissions | ウォレットの共有が受取り手のユーザーに与える許可の、コンマで区切ったリスト  |
| keychain    | （秘密鍵を取得する目的で）受取り手が復号化するための暗号化されたキーチェーン |

## List Wallet Shares ウォレット共有の一覧を表示する

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

ログオン中のアカウントについて着信及び発信したウォレット共有の一覧を取得します。

### HTTP Request HTTPリクエスト

`GET /api/v1/walletShare`

> 応答の例

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

### Response 応答

各ウォレット共有オブジェクトは、次のフィールドを含みます：

| フィールド       | 説明                                    |
| ----------- | ------------------------------------- |
| id          | ウォレット共有のid、受け入れるために使用                 |
| walletId    | 共有されているウォレットのid                       |
| walletLabel | ユーザーに提示するウォレットのラベル                    |
| fromUser    | ウォレットを共有しているユーザーのBitGo ID             |
| toUser      | ウォレットを受け取るユーザーのBitGo ID               |
| permissions | ウォレットの共有が受取り手のユーザーに与える許可の、コンマで区切ったリスト |

## Accept Wallet Share ウォレット共有を受け入れる

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 

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

> 応答の例

```json
{ "state": "accepted", "changed": "true" }
```<aside class="info"> この操作では、Unlock APIを使ってセッションをアンロックすることが必要です。 </aside> 

ウォレット共有を受け入れるためのクライアント側の操作です。次の手順を実行します：

* 暗号化された秘密のキーチェーンを含む、着信したウォレットの共有を取得する
* ユーザーが共有する秘密鍵とウォレット共有xPubを使用してプライベートのキーチェーンを復号化する鍵を派生させる。
* 将来の利用のため、ユーザーが選んだパスフレーズでウォレットを再暗号化する。
* 暗号化された鍵をBitGoのサービスにアップロードし、共有を「accepted」(受け入れられた) にセットする。そうするとユーザーに、BigGoにあるウォレットへのアクセスが与えられる。

### Parameters パラメーター

| パラメーター                | 種類              | 必須か | 説明                                             |
| --------------------- | --------------- | --- | ---------------------------------------------- |
| walletShareId         | ビットコインアドレス(文字列) | YES | 受け入れる、着信したウォレット共有ID                            |
| newWalletPassphrase   | 文字列             | NO  | 将来の使用の際用いるための、ウォレットで設定するパスフレーズ                 |
| userPassword          | 文字列             | NO  | 共有された秘密鍵を復号化する、ユーザーのパスワード                      |
| overrideEncryptedXprv | 文字列             | NO  | 帯域外で受け取った暗号化されたxprvの保存を望む場合、代替の暗号化されたxprvに設定する |

### Response 応答

| フィールド   | 説明                            |
| ------- | ----------------------------- |
| state   | ウォレット共有の最終状態（'accepted'であるべき） |
| changed | 成功した場合、 true                  |

## Cancel Wallet Share ウォレット共有をキャンセルする

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

> 応答の例

```json
{ "state": "canceled", "changed": "true" }
```

保留中の発信したウォレット共有をキャンセルする、または着信した共有を拒否するのに使用できる。共有はまだ受け入れられているべきでない。

### HTTP Request HTTPリクエスト

`DELETE/api/v1/walletshare/:SHAREID`

### Response 応答

| フィールド   | 説明            |
| ------- | ------------- |
| state   | ウォレット共有の新たな状態 |
| changed | 成功した場合、 true  |

## Remove Wallet User ウォレットのユーザーを削除する

ユーザーがウォレットの共有を受け入れた後、彼らはウォレットの当事者になり、ウォレットの共有は「完了」とみなされます。

受け入れられた後に共有を解除するためには、ウォレットからユーザーを削除することができます。

ウォレットの管理者が複数名いる場合、別のウォレット管理者による承認を要する場合があります。

```javascript
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) {
    throw err;
  }

  wallet.removeUser({ "user": userId }, function callback(err, wallet) {
    if (err) {
      // handle error
    }
    // etc
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

### HTTP Request HTTPリクエスト

`DELETE /api/v1/wallet/:wallet/user/:userId`

### URL Parameters URL パラメーター

| パラメーター | 必須か | 説明                                      |
| ------ | --- | --------------------------------------- |
| wallet | YES | ウォレットのID                                |
| userId | YES | 削除するユーザーのユーザーid(ウォレットオブジェクトで見つけることができる) |

### Response 応答

このウォレットの更新されたウォレットモデル、または保留中の承認オブジェクトを返します(承認が必要な場合)

### Errors　エラー

| 応答               | 説明                         |
| ---------------- | -------------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない      |
| 401 Unauthorized | 認証パラメーターが一致しない、またはアンロックが必要 |

# ウォレットのポリシー

BitGoウォレットはマルチユーザーまたはトランザクションとの2FA（2要素認証）承認そして支出制限等の、高度なセキュリティ機能を特徴としています。 これを利用するために、ユーザー/開発者はウォレットのポリシールールを追加、修正することができます。 ルールは（ユーザーによって設定された）関連付けられたアクションをトリガーします。 ポリシーエンジンは全てのトリガーされたルール結果を集め、deny、 get approval (別のユーザーから), get OTP (別のユーザーによってSMSを通じ送信)、 allow (デフォルト) の順で任意のトリガーされたアクションを実行します。

ウォレットに残高があり、2人以上のウォレットに関連付けられた"admin"ユーザーがいる場合、全てのポリシー変更には、有効になる前に別の管理者による承認を必要とします（追加の"admin"ユーザーがいない場合、不要です）。 よって、[ウォレットの共有を実行する](#wallet-sharing)ことにより最低2人の管理者でウォレットを作成すること強く推奨します。 そうすれば、ポリシーは、1人のユーザーが不正アクセスを受けたとしても有効になります。

"getOTP"アクションタイプのポリシーについては、トランザクションを正常に送信するには、署名され送信される前に7桁のワンタイムパスワードが必要になります。 このポリシーは事実上、デバイスに、あなた自身で実装する必要なしに二要素認証を提供するものです。 トランザクション送信の一回目の試みは失敗し、コードをポリシーで指定されている電話に送信します。 ユーザーからコードを取得したら、もう一度APIコールのパラメーターとしてotpコードが含まれた送信トランザクションを行い、トランザクションは正常に送信されます。 詳細は[アドレスにコインを送信する](#send-coins-to-address)にある"otp"パラメーターを参照下さい。

このドキュメンテーションでは、単一ウォレットに関わる基本的なBitGoポリシーのAPIとSDKをカバーします。 [webhookポリシータイプ](#set-policy-rule)を使用して、さらなるカスタムポリシーを実装することができます。BitGoは外部の状態に関連したカスタムポリシーの動作を評価することができるURLエンドポイントを呼び出します。

複数のウォレットが関わる高度なポリシーは、BitGoに直接連絡することにより実装が可能です。

## Get Policy ポリシーを取得する

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
      // handle error
    }
    console.dir(policy);
  });
});
```

> 応答の例

```json
{
    "date": "2015-04-29T19:03:37.189Z",
    "id": "55380d34d0d3b00364a52285f09e23a4",
    "rules": [
        {
            "action": {
                "type": "getApproval"
            },
            "condition": {
                "amount": 200000000
            },
            "id": "com.bitgo.limit.day",
            "type": "dailyLimit"
        },
        {
            "action": {
                "type": "getOTP"
                "actionParams": {
                    "otpType": "sms",
                    "phone": "+15417543010",
                    "duration": "3600"
                }
            },
            "condition": {
                "amount": 200000000
            },
            "id": "com.bitgo.limit.tx",
            "type": "transactionLimit"
        }
    ],
    "version": 4
}
```

ウォレットの操作に関するポリシールールを取得します。

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/policy`

### URL Parameters URL パラメーター

| パラメーター   | 種類              | 必須か | 説明       |
| -------- | --------------- | --- | -------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのID |

### Response 応答

それに設定されたルールを含むウォレットポリシーオブジェクトを返します。

## Get Policy Status ポリシー ステータスを取得する

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

> 応答の例

```json
{
    "statusResults": [
        {
            "policy": "55380d34d0d3b00364a52285f09e23a4",
            "ruleId": "com.bitgo.limit.day",
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

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/policy/status`

### URL Parameters URL パラメーター

| パラメーター   | 種類              | 必須か | 説明       |
| -------- | --------------- | --- | -------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのID |

### Response 応答

使用の制限に関する情報を含む、ウォレットポリシーで有効になっているルールによって生成されたステータス結果を返します

## Set Policy Rule ポリシールールを設定する

ウォレットでのポリシーを設定します。ウォレットポリシーは、BitGoが一つの鍵を使ってトランザクションに署名する条件をコントロールします。

```javascript
var walletId = '2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2';
bitgo.wallets().get({ "id": walletId }, function callback(err, wallet) {
  if (err) { throw err; }
  // Sets the policy
  var rule = {
    id: "test1",
    type: "dailyLimit"
    action: { type: "getApproval" },
    condition: { "amount": 101*1e8 },
  };
  wallet.setPolicyRule(rule, function callback(err, wallet) {
    if (err) { throw err; }
    console.dir(wallet.admin.policy);
  });
});
```

```shell
curl -X PUT -H "Content-Type: application/json" -H "Authorization: Bearer $ACCESS_TOKEN" \
-d '{ "action" : { "type" : "getApproval" }, "condition": { "amount" : 100000000 }, "id": "com.bitgo.limit.tx", "type": "transactionLimit", "default":true}' \
https://test.bitgo.com/api/v1/wallet/$WALLETID/policy/rule
```

### HTTP Request HTTPリクエスト

`PUT /api/v1/wallet/:wallet/policy/rule`<aside class="info"> この操作では、Unlock APIを使ってセッションをアンロックすることが必要です。 </aside> 

### BODY Parameters BODYパラメーター

ポリシールールオブジェクトは種類、条件、そしてアクションを持ちます。この種類のポリシーはしばしば条件の値を決定します。

| パラメーター    | 種類                      | 必須か                                                | 例 |
| --------- | ----------------------- | -------------------------------------------------- | - |
| id        | ポリシーの id                | "com.bitgo.limit.tx", "custom1", "anyUniqueRuleId" |   |
| type      | ポリシーの種類                 | *ポリシーの種類を参照*                                       |   |
| condition | このポリシーの条件               | *ポリシーの種類を参照*                                       |   |
| action    | 条件が false の場合に実行するアクション | *ポリシー アクション オブジェクトを参照*                             |   |

> 応答の例

```json
{
  "date": "2015-10-29T23:33:20.166Z",
  "id": "5631d2f48cad5fab2c6abe16682372bb",
  "rules": [
    {
      "action": {
        "type": "getApproval"
      },
      "condition": {
        "amount": 100000000
      },
      "id": "com.bitgo.limit.tx",
      "type": "transactionLimit"
    },
    {
      "action": {
        "type": "getApproval"
      },
      "condition": {
        "addresses": [
          "muDjD4YdVzi1vyqKtYVbAkLi2J84GkKj5h"
        ]
      },
      "id": "com.bitgo.whitelist.address",
      "type": "bitcoinAddressWhitelist"
    }
  ],
  "version": 4
}
```

### Response 応答

更新されたウォレットモデルオブジェクトを返します。

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |

### Policy Object ポリシー オブジェクト

統合されたウォレットポリシーはポリシールールオブジェクトの配列です。ポリシールールオブジェクトは種類、条件、そしてアクションを持ちます。

| フィールド     | 説明                      | 可能な値                                                                   |
| --------- | ----------------------- | ---------------------------------------------------------------------- |
| id        | ポリシーの id                | "com.bitgo.limit.tx", "custom1", "anyUniqueRuleId"                     |
| type      | ポリシーの種類                 | "transactionLimit", "dailyLimit", "bitcoinAddressWhitelist", "webhook" |
| condition | このポリシーの条件               | *使用されるポリシー ルールの種類によって異なる*                                              |
| action    | 条件が false の場合に実行するアクション | *ポリシー アクション オブジェクトを参照*                                                 |

### ポリシータイプ - dailyLimit

直近の24時間の間にビットコインの額が一定額を超過すると、dailyLimit (一日の限度) ポリシールールがトリガーされます。

そのための条件には次があります:

| フィールド  | 説明                       | 可能な値          |
| ------ | ------------------------ | ------------- |
| amount | 一日あたりの全てのトランザクションの可能な最大額 | Satoshis 単位の額 |

### ポリシータイプ - transactionLimit

単一のトランザクションが指定された金額を超過した場合、transactionLimitポリシールールがトリガーされます。

そのための条件には次があります:

| フィールド  | 説明                       | 可能な値          |
| ------ | ------------------------ | ------------- |
| amount | ウォレットの全てのトランザクションの可能な最大額 | Satoshis 単位の額 |

### ポリシータイプ - bitcoinAddressWhitelist

有効な時、送信トランザクションの宛先ビットコインアドレス（お釣りでない）がホワイトリストにない場合、いつでもビットコインアドレスホワイトリストルールがトリガーされます。

そのための条件には次があります:

| フィールド  | 説明                      | 可能な値       |
| ------ | ----------------------- | ---------- |
| add    | ホワイトリストに追加するビットコインアドレス  | ビットコインアドレス |
| remove | ホワイトリストから削除するビットコインアドレス | ビットコインアドレス |

> Webhookコールバックの例(サーバに送信され、全ての非200応答はポリシールールアクションをトリガーします)

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

### Policy Type - webhook ポリシータイプ - webhook

有効な時、webhookルールは条件で指定されたHTTPSエンドポイントへコールバックを発行します。 HTTPエンドポイントが非200(ステータス) 応答を返す場合、ルールはアクションをトリガーします。

このルールの条件は：

| フィールド | 説明                   | 可能な値    |
| ----- | -------------------- | ------- |
| url   | HTTPsエンドポイントにコールバックを | 発行するURL |

コールバックで送信されたBodyパラメーター：

| フィールド         | 説明                                    | 可能な値                                                                         |
| ------------- | ------------------------------------- | ---------------------------------------------------------------------------- |
| walletId      | トランザクションの起点となっているウォレットのID             | "2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe"                                        |
| ruleId        | トランザクションの起点となっているウォレットのID             | "webhookPolicy1"                                                             |
| outputs       | OutputAddressとvalueを含むoutputオブジェクトの配列 | [{ "outputAddress":"2N9kNR8iS46WuwekvQVaTUa74w2fbvAXHQn", "value":24885327}] |
| spendAmount   | 正味(net) のウォレットからの使用額、お釣りを差し引いて(手数料含む) | 10009060                                                                     |
| approvalCount | これまでのウォレットでのユーザーによる承認の数               |                                                                              |
| unsignedRawTx | 半分署名の未処理トランザクションの 16 進数の文字列           | "0100000001... 0794c5382a38700000000"                                        |
| sequenceId    | トランザクション作成の際、送信者によって提供されるカスタムシーケンスID  | "custom1"                                                                    |

### Policy Action Object ポリシーアクションオブジェクト

アクションとは、トランザクションによって条件が満たされない場合に実行するアクションです。

| フィールド        | 説明                                                  | 可能な値                            |
| ------------ | --------------------------------------------------- | ------------------------------- |
| type         | アクションの種類                                            | "deny", "getApproval", "getOTP" |
| actionParams | Phone/otpType/durationを含むJSONオブジェクト                 | *以下を参照*                         |
| otpType      | コードがどのように送られるべきかを決定する( type === "getOTP"に設定する必要がある) | "sms"                           |
| phone        | コードを受信する電話番号(type === "getOTP"に設定する必要がある)           | "541-754-3010", "+498963648018" |
| duration     | OTPが有効であるべき時間（秒数）（type === "getOTP"に設定する必要がある）      | 3600                            |

## Remove Policy Rule ポリシールールを削除する

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

> 応答の例

```json
{
  "date": "2015-10-29T23:33:20.166Z",
  "id": "5631d2f48cad5fab2c6abe16682372bb",
  "rules": [ ],
  "version": 5
}
```

指定されたidを持つポリシールールを削除する。ウォレットに複数の管理者がいる場合は、セカンダリー承認を必要とする場合があります。

### HTTP Request HTTPリクエスト

`DELETE /api/v1/wallet/:WALLETID/policy/rule`<aside class="info"> この操作では、Unlock APIを使ってセッションをアンロックすることが必要です。 </aside> 

### BODY Parameters BODYパラメーター

| パラメーター | 種類  | 必須か | 説明               |
| ------ | --- | --- | ---------------- |
| id     | 文字列 | YES | 削除するポリシー ルールの id |

### Response 応答

更新されたウォレットモデルオブジェクトを返します。

# Pending Approvals　保留中の承認

## 保留中の承認の一覧を表示する

ウォレットidまたは提供することによりウォレットまたはエンタープライズの保留中の承認の一覧を表示します。

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

> 応答の例

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
                       "id": "com.bitgo.limit.day",
                       "type": "dailyLimit"
                   }
               },
               "type": "policyRuleRequest"
           },
           "state": "pending"
       }
   ]
}
```

### HTTP Request HTTPリクエスト

`GET /api/v1/pendingapprovals`

### URL Parameters URL パラメーター

| パラメーター     | 種類         | 必須か | 説明               |
| ---------- | ---------- | --- | ---------------- |
| walletId   | アドレス (文字列) | NO  | ウォレットのベースアドレス    |
| enterprise | 文字列        | NO  | エンタープライズのパブリックID |

### Response 応答

保留中の承認のリストを返します。

## Update Pending Approval 保留中の承認を更新する

保留中の承認の状態を'approved'または'rejected'に更新します。

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

> 応答の例

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
               "id": "com.bitgo.limit.day",
               "type": "dailyLimit"
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

### HTTP Request HTTPリクエスト

`PUT /api/v1/pendingapprovals/:pendingApprovalId`

### BODY Parameters BODYパラメーター

| パラメーター | 種類  | 必須か | 説明                                   |
| ------ | --- | --- | ------------------------------------ |
| state  | 文字列 | YES | 保留中の承認の新たな状態： 'approved'、 'rejected' |

### Response 応答

新しい状態を持った更新された保留中の承認を返します。

# Address Labels アドレスのラベル

ラベルで、人間が読むことが出来るアドレスの記録（ノート）をつけることが可能です。 全ての有効なアドレスにラベルを追加することが出来ます。アドレスはウォレットがコントロールしているものである必要はありません。 アドレスラベルはウォレットラベルと異なりますが、ウォレットに紐づいているので、他のユーザーとウォレットを共有すると、彼らもラベルを見ることができます。

## List Labels For All Wallets 全てのウォレットのラベルの一覧を表示する

```shell
curl -X GET \
-H "Authorization: Bearer $ACCESS_TOKEN" \
https://test.bitgo.com/api/v1/labels
```

```javascript
bitgo.labels({}, function callback(err, labels) {
  if (err) {
    // handle error
  }
  // do something with labels
  console.dir(labels);
});
```

そのユーザーのウォレットの一覧を取得します

### HTTP Request HTTPリクエスト

`GET /api/v1/labels`

> 応答の例

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

### Response 応答

ラベルモデルオブジェクトの配列を返します。

| フィールド    | 説明                     |
| -------- | ---------------------- |
| walletId | ウォレットのid(同時に最初の受信アドレス) |
| address  | ラベルされているビットコインアドレス     |
| label    | アドレスラベル                |

### Errors　エラー

| Response 応答      | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |

## List Labels For Specific Wallet 特定のウォレットのラベルの一覧を表示する

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
    // handle error
  }
  // do something with labels
  wallet.labels({}, function (err, labels) {
    if (err) {
      console.log(err);
      process.exit(-1);
    }
    console.dir(labels);
  });
});
```

そのウォレットのラベルの一覧を取得します

### HTTP Request HTTPリクエスト

`GET /api/v1/labels/:walletId`

> 応答の例

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

### Response 応答

ラベルモデルオブジェクトの配列を返します。

| フィールド    | 説明                     |
| -------- | ---------------------- |
| walletId | ウォレットのid(同時に最初の受信アドレス) |
| address  | ラベルされているビットコインアドレス     |
| label    | アドレスラベル                |

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |
| 404 Not Found    | ウォレットが見つけられなかった       |

## Set Label ラベルを設定する

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

特定のアドレスにラベルを設定し、特定のウォレットに関連付けます。 ラベルの長さは最大で250文字に制限されています。 ウォレットのラベルとして予約されているため、ウォレットの最初の受取アドレスにラベルは設定できません。

### HTTP Request HTTPリクエスト

`PUT /api/v1/labels/:walletId/:address`

> 応答の例

```json
{
  "walletId": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
  "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
  "label": "A Noteworthy Label"
}
```

### URL Parameters URL パラメーター

| 名        | 種類              | 必須か | 説明                     |
| -------- | --------------- | --- | ---------------------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのid(同時に最初の受信アドレス) |
| address  | ビットコインアドレス(文字列) | YES | ラベルされているビットコインアドレス     |

### PUT Parameters PUT パラメーター

| 名     | 種類  | 必須か | 説明      |
| ----- | --- | --- | ------- |
| label | 文字列 | YES | アドレスラベル |

### Response 応答

ラベルモデルオブジェクトを返します。

| フィールド    | 説明                     |
| -------- | ---------------------- |
| walletId | ウォレットのid(同時に最初の受信アドレス) |
| address  | ラベルされているビットコインアドレス     |
| label    | アドレスラベル                |

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |
| 404 Not Found    | ウォレットが見つけられなかった       |

## Delete Label ラベルを削除する

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
    // handle error
  }
  console.dir(label);
});
```

特定のアドレスやウォレットからラベルを削除します。

### HTTP Request HTTPリクエスト

`DELETE /api/v1/labels/:walletId/:address`

> 応答の例

```json
{
  "walletId": "2NAGz3TDs5HmBU2SEodtWyks9n5KXVCzBTf",
  "address": "2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD",
  "label": "A Noteworthy Label"
}
```

### URL Parameters URL パラメーター

| 名        | 種類              | 必須か | 説明                     |
| -------- | --------------- | --- | ---------------------- |
| walletId | ビットコインアドレス(文字列) | YES | ウォレットのid(同時に最初の受信アドレス) |
| address  | ビットコインアドレス(文字列) | YES | ラベルされているビットコインアドレス     |

### Response 応答

削除されたラベルのラベルモデルオブジェクトを返します。

| フィールド    | 説明                     |
| -------- | ---------------------- |
| walletId | ウォレットのid(同時に最初の受信アドレス) |
| address  | ラベルされているビットコインアドレス     |
| label    | アドレスラベル                |

### Errors　エラー

| 応答               | 説明                    |
| ---------------- | --------------------- |
| 400 Bad Request  | 要求パラメーターが見つからないか正しくない |
| 401 Unauthorized | 認証パラメーターが一致しない        |
| 404 Not Found    | ウォレットまたはラベルが見つけられなかった |

# Tag タグ

タグは、法人顧客向けの高度なポリシー機能です。

## Create a tag タグを作成します

### HTTP Request HTTPリクエスト

`POST /api/v1/tag`

### BODY Parameters BODYパラメーター

タグは名前を持つ必要があり、単一のユーザー、ウォレット、またはエンタープライズを持たなければなりません。

<table>
  <tr>
    <th>
      パラメーター
    </th>
    
    <th>
      種類
    </th>
    
    <th>
      必須か
    </th>
    
    <th>
      説明
    </th>
    
    <th>
      可能な値
    </th>
  </tr>
  
  <tr>
    <td>
      name
    </td>
    
    <td>
      文字列
    </td>
    
    <td>
      YES
    </td>
    
    <td>
      タグの名前
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      user
    </td>
    
    <td>
      id
    </td>
    
    <td>
      NO
    </td>
    
    <td>
      タグを所有するユーザーのユーザーIDで、必ずタグを追加するユーザーのIDでなければならない
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      wallet
    </td>
    
    <td>
      id
    </td>
    
    <td>
      NO
    </td>
    
    <td>
      タグを所有するウォレットのビットコインアドレスでなくid
    </td>
    
    <td>
    </td>
  </tr>
  
  <tr>
    <td>
      enterprise
    </td>
    
    <td>
      id
    </td>
    
    <td>
      NO
    </td>
    
    <td>
      タグを所有するエンタープライズのid
    </td>
    
    <td>
    </td>
  </tr>
</table>

### Response 応答

タグを返します。

```json
{
  "_id": "[id]",
  "name": "name of tag",
  "user": "[id]"
}
```

### Errors　エラー

| 応答              | 説明                    |
| --------------- | --------------------- |
| 400 Bad Request | 要求パラメーターが見つからないか正しくない |

## Add a tag to a wallet ウォレットにタグを追加する

### HTTP Request HTTPリクエスト

`POST /api/v1/wallet/:wallet/tag`

### BODY Parameters BODYパラメーター

ウォレットに追加しているタグのidを指定するする必要があります。他のパラメーターは不要です。

<table>
  <tr>
    <th>
      パラメーター
    </th>
    
    <th>
      種類
    </th>
    
    <th>
      必須か
    </th>
    
    <th>
      説明
    </th>
    
    <th>
      可能な値
    </th>
  </tr>
  
  <tr>
    <td>
      tag
    </td>
    
    <td>
      id
    </td>
    
    <td>
      YES
    </td>
    
    <td>
      タグのid。
    </td>
    
    <td>
    </td>
  </tr>
</table>

### Response 応答

ウォレット、タグ、そして影響を受けるwallettxsの数を返します。

```json
{
  "wallet": "[id]",
  "tag": "[id]",
  "walletTxsAffected": 0
}
```

### Errors　エラー

| 応答            | 説明          |
| ------------- | ----------- |
| 404 Not Found | タグが見つからなかった |

# Webhook Notifications Webhook 通知

> Webhookコールバックのトランザクションの例

    POST http://your.server.com/webhook
    
    {
      "type": "transaction",
      "walletId": "2MwLxgWaAGmMT9asT4nAdeewWzPEz3Sn5Eg",
      "hash": "c9a55f32d4b63d02a617eea58873227e4f011d0dfc836cb2e9cab531c4db0c4a"
    }
    

> Webhook コールバック 保留中の承認 例

    POST http://your.server.com/webhook
    {
      "type": "pendingapproval",
      "pendingApprovalId": "55e79a1b5f9a20da1d3fe5b988a71c93",
      "walletId": "2MwLxgWaAGmMT9asT4nAdeewWzPEz3Sn5Eg",
      "state": "accepted"
    }
    

BitGoからプログラマティックにコールバックを受け取るために、Webhookを設定することができます。 ウォレット(トランザクションの場合) またはユーザー（ブロック通知で）に添付することができます。 受信トランザクション等指定されたイベントが発生した時、Webhook通知がトリガーされます。

BitGoサーバはJSONペイロードと定義された、POST httpリクエストを行い、`HTTP 200 OK`を期待します。 正常な応答が受信されない場合、BitGo は各再試行間隔を増やし webhook を再試行しようとします。

開発者は一時的なネットワークエラーの場合でも、あるいは不適切な確認のため同じwebhookを二度受信した場合でもアプリケーションが正常に動くことを確保するべきです。

### Request Schema スキーマをリクエストする

Webhook URLは、次のHTTPのボディ内のJSONエンコードされたフィールドで呼び出されます。

| フィールド             | 説明                                                                                                   |
| ----------------- | ---------------------------------------------------------------------------------------------------- |
| type              | webhookの種類： 'transaction', 'transactionExpire', 'transactionRemoved', 'block', そして 'pendingapproval' |
| walletId          | Webhookイベントに関連付けられたウォレットのID、ウォレットのwebhookであっても                                                       |
| hash              | トランザクションwebhookの場合、トランザクションID                                                                        |
| pendingApprovalId | 保留中の承認webhookの場合、保留中の承認ID                                                                            |
| state             | 保留中の承認のwebhookの場合、保留中の承認の状態(pending, approved, またはrejected)                                          |

### Webhook Types Webhookの種類

BitGoでは、現在積極的にwebhookに取り組んでいます。もっと多くの種類をリクエストされたい場合は、弊社までご連絡ください。

| 種類                 | 説明                                            |
| ------------------ | --------------------------------------------- |
| transaction        | トランザクションが、ウォレットの任意の受信アドレスで既読になった/確認された時に有効になる |
| transactionRemoved | トランザクションがユーザーのウォレットから削除された時に有効になる             |
| transactionExpire  | トランザクションの有効期限が切れる時に有効になる                      |
| pendingapproval    | ユーザーのウォレットに関係する保留中の承認が、作成、承認、拒否された時に有効になる     |
| block              | ビットコインネットワークで新しいブロックが見られた時に有効になる              |

## List Wallet Webhooks ウォレットwebhookの一覧を取得する 

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

> 応答の例

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

ウォレットで設定されているwehookの一覧を取得します。 現在、ウォレットに添付できるwebhookの種類はtransactionとpendingapprovalの通知のみです。

### HTTP Request HTTPリクエスト

`GET /api/v1/wallet/:walletId/webhooks`

### Response 応答

Webhookオブジェクトの配列

| フィールド    | 説明                         |
| -------- | -------------------------- |
| walletId | ウォレットのID                   |
| type     | Webhookの種類、例えば transaction |
| url      | コールバック要求のhttp/https url    |

## Add Wallet Webhooks ウォレット Webhookを追加する

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

> 応答の例

```json
{
  "walletId": "2NGJP7z9DZwyVjtY32YSoPqgU6cG2QXpjHu",
  "type": "transaction",
  "url": "http://www.yoursite.com/partner/webhooks"
}
```

イベントがトリガーされた時に、BitGoからの特定のURLでHTTPコールバックの結果となるWebhookをを追加します。 ウォレット毎に各種類ごとに10 webhook上限があります。

2種類のウォレットwebhookが利用可能となっています： 1. ウォレット上の任意のトランザクションについて、トランザクションwebhookが発射します。 2。 送信トランザクションがウォレットのポリシーをトリガーした時、保留中の承認 webhookが発射します。

### HTTP Request HTTPリクエスト

`POST /api/v1/wallet/:walletId/webhooks`

### Parameters パラメーター

| パラメーター           | 種類      | 必須か | 説明                                                                                                        |
| ---------------- | ------- | --- | --------------------------------------------------------------------------------------------------------- |
| type             | 文字列     | YES | Webhookの種類、例えば transaction や pendingapproval                                                              |
| url              | 文字列     | YES | コールバック要求の有効なhttp/https URL                                                                                |
| numConfirmations | integer | NO  | transaction webhookをトリガーする前の前の確認の数。 0または示されていない場合、要求はコールバックエンドポイントに送られ、トランザクションがまず見られた時、そして確認された時呼び出されます。 |

### Response 応答

| フィールド    | 説明                         |
| -------- | -------------------------- |
| walletId | ウォレットのID                   |
| type     | Webhookの種類、例えば transaction |
| url      | コールバック要求のhttp/https url    |

## Remove Wallet Webhooks ウォレット webhookを削除する

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

> 応答の例

```json
{
  "removed": 1
}
```

Webhookを削除した場合、新規の特定のタイプのイベントがHTTPコールバックをあなたの各URLへトリガーしなくなる原因になります。

### HTTP Request HTTPリクエスト

`DELETE /api/v1/wallet/:walletId/webhooks`

### パラメーター

| パラメーター | 種類  | Required | 説明                              |
| ------ | --- | -------- | ------------------------------- |
| type   | 文字列 | YES      | Webhookの種類、例えば transaction      |
| url    | 文字列 | YES      | コールバック要求を行う先の、有効なhttp/https URL |

### Response 応答

| フィールド    | 説明                         |
| -------- | -------------------------- |
| walletId | ウォレットのID                   |
| type     | Webhookの種類、例えば transaction |
| url      | コールバック要求のhttp/https url    |

## List User Webhooks ユーザー Webhookの一覧を取得する

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

> 応答の例

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

ユーザーに添付されるwebhookの一覧を取得します。現在、ユーザーに添付できるwebhookの種類はブロック通知だけです。

### HTTP Request HTTPリクエスト

`GET /api/v1/webhooks`

### Response 応答

Webhookオブジェクトの配列

| フィールド | 説明                                                    |
| ----- | ----------------------------------------------------- |
| type  | Webhookの種類、例えばブロック                                    |
| coin  | 文字列 | No |ネットワーク トークン、例えば「ビットコイン」や「eth」(デフォルトでビットコイン) |
| url   | コールバック要求のhttp/https url                               |

## Add User Webhooks ユーザー Webhook を追加する

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

> 応答の例

```json
{
  "type": "block",
  "type": "bitcoin",
  "url": "http://www.yoursite.com/partner/webhooks"
}
```

イベントがトリガーされた時に、BitGoからの特定のURLでHTTPコールバックの結果となるWebhookをを追加します。 Webhookの記録はユーザーアカウントに添付されます。

### HTTP Request HTTPリクエスト

`POST /api/v1/webhooks`

### Parameters パラメーター

| パラメーター | 種類  | 必須か | 説明                                          |
| ------ | --- | --- | ------------------------------------------- |
| type   | 文字列 | YES | Webhookの種類、例えばブロック                          |
| coin   | 文字列 | NO  | ネットワーク トークン、例えば「ビットコイン」や「eth」(デフォルトでビットコイン) |
| url    | 文字列 | YES | コールバック要求の有効なhttp/https URL                  |

### Response 応答

| フィールド | 説明                      |
| ----- | ----------------------- |
| type  | Webhookの種類、例えばブロック      |
| url   | コールバック要求のhttp/https url |

## ユーザー webhook を削除する

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

> 応答の例

```json
{
  "removed": 1
}
```

Webhookを削除した場合、新規の特定のタイプのイベントがHTTPコールバックをあなたの各URLへトリガーしなくなる原因になります。

### HTTP Request HTTPリクエスト 

`DELETE /api/v1/webhooks`

### Parameters パラメーター

| パラメーター | 種類  | 必須か | 説明                              |
| ------ | --- | --- | ------------------------------- |
| type   | 文字列 | YES | webhookの種類、例えば transaction      |
| url    | 文字列 | YES | コールバック要求を行う先の、有効なhttp/https URL |

### Response 応答

| フィールド | 説明                         |
| ----- | -------------------------- |
| type  | webhookの種類、例えば transaction |
| url   | コールバック要求のhttp/https url    |

# Utilities 各ユーティリティ

このセクションでは、BitGo APIの一部として提供されている有用なユーティリティサービスについて説明します。

## Decrypt 復号化

```javascript
var encryptedString = '{"iv":"n4zHXVTi/Go/riCP8fNs/A==","v":1,"iter":10000,"ks":256,"ts":64,"mode":"ccm","adata":"","cipher":"aes","salt":"zvLyve+4AJU=","ct":"gNMqheicMoD8ZmNzRwuQfWGAh+HA933l"}';
var decryptedString = bitgo.decrypt({ password: "password", input: encryptedString });
// decryptedString = "this is a secret"
```

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express)

PASSWORD='password'
INPUT='{\"iv\":\"n4zHXVTi/Go/riCP8fNs/A==\",\"v\":1,\"iter\":10000,\"ks\":256,\"ts\":64,\"mode\":\"ccm\",\"adata\":\"\",\"cipher\":\"aes\",\"salt\":\"zvLyve+4AJU=\",\"ct\":\"gNMqheicMoD8ZmNzRwuQfWGAh+HA933l\"}'

curl -X POST \
-H "Content-Type: application/json" \
-d "{ \"password\": \"$PASSWORD\", \"input\": \"$INPUT\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/decrypt

{ "decrypted" : "this is a secret" }
```

BitGo APIから暗号化されたblobを復号化するクライアント側関数

## Encrypt 暗号化

```javascript
var encryptedString = bitgo.encrypt({ password: "password", input: "this is a secret" });
// encyrptedString = "{\"iv\":\"n4zHXVTi/Go/riCP8fNs/A==\",\"v\":1,\"iter\":10000,\"ks\":256,\"ts\":64,\"mode\":\"ccm\",\"adata\":\"\",\"cipher\":\"aes\",\"salt\":\"zvLyve+4AJU=\",\"ct\":\"gNMqheicMoD8ZmNzRwuQfWGAh+HA933l\"}"
```

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 

PASSWORD='password'
INPUT='this is a secret'

curl -X POST \
-H "Content-Type: application/json" \
-d "{ \"password\": \"$PASSWORD\", \"input\": \"$INPUT\" }" \
http://$BITGO_EXPRESS_HOST:3080/api/v1/encrypt

{ "encrypted" : "{\"iv\":\"U4uRz85ytmlCeTe2P3iOmg==\",\"v\":1,\"iter\":10000,\"ks\":256,\"ts\":64,\"mode\":\"ccm\",\"adata\":\"\",\"cipher\":\"aes\",\"salt\":\"zvLyve+4AJU=\",\"ct\":\"/tRnUh9LUyI7L5e5LPqpvnPR7RD1CdUi\"}" }
```

文字列を暗号化するクライアント側関数です。BitGoで保管される全てのデータは、このAPIを用いて暗号化されます。

## Estimate Transaction Fees トランザクションフィーを見積もる

```javascript
bitgo.estimateFee({ numBlocks: 6 }, function callback(err, res) {
  console.dir(res);
});
```

```shell
curl -k https://test.bitgo.com/api/v1/tx/fee?numBlocks=6
```

目標となるブロックの数内でトランザクションを確認するための、キロバイトあたりの推奨手数料のレートを返します。 トランザクションの作成に使用できます。 注意：見積もりアルゴリズムは最低2ブロック先まで正確なものです。

### HTTP Request HTTPリクエスト

`GET /api/v1/tx/fee?numBlocks=6`

> 応答の例

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

### Response 応答

| フィールド      | 説明                                                 |
| ---------- | -------------------------------------------------- |
| confidence | BitGoによって使用される見積もり額の信頼値。この値が85より小さい場合見積もりを使用しないこと。 |
| feePerKb   | 使用するキロバイト当たりの（Satoshiを単位とする）手数料                    |
| multiplier | BitGoによって、feePerKbを計算するために使用される乗数。情報目的のみ、使用しないこと   |
| numBlocks  | 見積もりにおいてリクエストされたターゲットのブロック数                        |

## Market Price Data 市場価格データ

```javascript
bitgo.markets().latest({}, function callback(err, market) {
  if (err) {
    // handle error
  }
  // etc
});
```

```shell
curl -k https://test.bitgo.com/api/v1/market/latest
```

現在の市場についての情報を得ます。

### HTTP Request HTTPリクエスト

`GET /api/v1/market/latest`

> マーケットモデルの応答の例

```json
{
    "latest": {
        "blockchain": {
            "totalbc": 14648675,
            "transactions": 125632
        },
        "currencies": {
            "AUD": {
                "24h_avg": 337.58,
                "ask": 337.44,
                "bid": 337.18,
                "last": 337.44,
                "lastHourHigh": 337.59,
                "lastHourLow": 335.91,
                "monthlyHigh": 345.69,
                "monthlyLow": 322.85,
                "prevDayHigh": 345.69,
                "prevDayLow": 333.21,
                "timestamp": "Fri, 25 Sep 2015 06:31:31 -0000",
                "total_vol": 516.11
            },
            "USD": {
                "24h_avg": 234.34,
                "ask": 236.92,
                "bid": 236.59,
                "last": 236.39,
                "lastHourHigh": 236.2,
                "lastHourLow": 234.66,
                "monthlyHigh": 246.46,
                "monthlyLow": 227.08,
                "prevDayHigh": 236.3,
                "prevDayLow": 232.67,
                "timestamp": "Fri, 25 Sep 2015 06:31:31 -0000",
                "total_vol": 59877.2
            }
        },
        "updateTime": "2015-09-25T06:31:19.087Z"
    }
}
```

### Response 応答

マーケットモデルオブジェクトを返します。全ての価格はユーザーが設定した通貨の単位建てです。

| フィールド       | 説明                                    |
| ----------- | ------------------------------------- |
| last        | 最新の市場価格                               |
| bid         | 最も高い現在の入札価格                           |
| ask         | 最も低い現在の売り呼び値                          |
| volume      | 取引されたビットコインの24時間の出来高                  |
| high        | 今日の市場価格の最高額                           |
| low         | 今日の市場価格の最低額                           |
| monthlyHigh | 今月の市場価格の最高額                           |
| monthlyLow  | 今月の市場価格の最低額                           |
| marketcap   | ビットコインの時価総額                           |
| updateTime  | このデータが更新された日時                         |
| yesterday   | 以前表示されたフィールドを含むオブジェクト、ただし昨日の市場データについて |

## マルウェアアドレスのリスト Malware Address List

```shell
curl https://www.bitgo.com/api/v1/malware/bitcoin
```

BitGoはアドレススワッピングマルウェアによって使用されていることが知られたアドレスのリストを保持しています。 BitGoはこれらのアドレスに対しトランザクションの共同署名を行うことを拒否します。 このAPIは現在BitGoによってブロックされているアドレスのリストを提供します。 自身のブロックリストをメンテナンスするために、このリストを定期的にポーリング（一日一回など）することを推奨します。 さもなくばあなたの顧客が感染していて引出しをリクエストした場合、間違って「悪い」トランザクションを提出してしまう可能性があります。

### HTTP Request HTTPリクエスト

`GET /api/v1/malware/bitcoin`

> 応答の例

```json
{
  "readme": "これはBitGoが知る、ビットコインを盗むマルウェアに紐付いたアドレスのリストです。 BitGoがそれらのアドレスを宛先とするトランザクションに共同署名することはありません。 自身の「悪い」アドレスのリストを更新するため、定期的にこのAPIを照会することを推奨します", [
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

### Response 応答

| フィールド   | 説明                         |
| ------- | -------------------------- |
| readme  | 人間が読めるこのリストの説明             |
| address | { address: xxx } オブジェクトの一覧 |

## Verify Bitcoin Address ビットコインアドレスを確認する

```javascript
var isValid = bitgo.verifyAddress({ address: "1AJbsFZ64EpEfS5UAjAfcUG8pH8Jn3rn1F" });
```

```shell
ローカル メソッドとしてのみ使用できます (BitGo Express) 

curl -X POST \
-H "Content-Type: application/json" \
-d '{ "address": "2NFasKP9nqHHUBvVHvEGkuHpNA3hjdkUhAV" }' \
http://$BITGO_EXPRESS_HOST:3080/api/v1/verifyaddress
```

与えられた文字列が有効なビットコインアドレスであることを確認するクライアント側関数 v1 アドレス (例: "1...") とP2SH アドレス (例: "3...")の両方がサポートされています。

アドレスが有効な場合は true を返します。

## BitGo Client Version BitGoクライアントバージョン

```javascript
var version = bitgo.version();
```

このBitGo SDKのバージョンを取得するクライアント側関数です。

文字列を返します。

# Blockchain Data ブロックチェーンデータ

BitGoは、アドレスやトランザクションについてブロックチェーンデータを取得するパブリックAPIを提供しています。 これらのAPIはBitGoユーザーやウォレットの概念と関連しません。 このAPIのエンドポイントの目的は、APIの消費者(利用者) が非BitGoのアドレスとトランザクションのデータを取得することを可能にすることです（Satoshiクライアントのtxiindexとwatchonlyの概念と類似）。

ほとんどのウォレットの利用ケースで、開発者はこのウォレットとキーチェーンAPIを利用したいでしょう。 それらはHDウォレットの合計残高や、アドレスの作成、トランザクションの送信等のような、より多くの操作をサポートします。<aside class="success"> パブリックブロックチェーンデータだけを返すため、ブロックチェーンデータAPIは認証を必要としません。 ただし、私達のサービスからの高いレートの限度を達成するため、ヘッダーにあるアクセストークンを送信することを推奨します。 </aside> 

## Get Address アドレスを取得する

残高情報を持つアドレスをルックアップします。

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

### HTTP Request HTTPリクエスト

`GET /api/v1/address/:address`

### URL Parameters URL パラメーター

| パラメーター  | 種類              | 必須か | 説明         |
| ------- | --------------- | --- | ---------- |
| address | ビットコインアドレス(文字列) | YES | ビットコインアドレス |

> 応答の例

```json
{
  "address": "2N76BgbTnLJz9WWbXw15gp6K9mE5wrP4JFb",
  "balance": 6900000,
  "confirmedBalance": 6900000
}
```

### Response 応答

アドレスの概要情報を返します。

| フィールド            | 説明                  |
| ---------------- | ------------------- |
| address          | アドレス                |
| balance          | 確認が0回のトランザクションを含む残高 |
| confirmedBalance | 確認された残高             |

### Errors　エラー

| 応答              | 説明                    |
| --------------- | --------------------- |
| 400 Bad Request | 要求パラメーターが見つからないか正しくない |
| 404 Not Found   | ウォレットが見つからなかった        |

## Get Address Transactions アドレス トランザクションを取得する

指定されたアドレスについて、逆のブロックの高さの順でトランザクションを取得します。

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

### HTTP Request HTTPリクエスト

`GET /api/v1/address/:address/tx`

### URL Parameters URL パラメーター

| パラメーター  | 種類              | 必須か | 説明         |
| ------- | --------------- | --- | ---------- |
| address | ビットコインアドレス(文字列) | YES | ビットコインアドレス |

### QUERY Parameters クエリ パラメーター

| パラメーター | 種類 | 必須か | 説明                                     |
| ------ | -- | --- | -------------------------------------- |
| skip   | 数字 | NO  | 一覧取得を開始するインデックス番号。既定値は0。               |
| limit  | 数字 | NO  | 単一コール(default=25, max=250) で返す結果の最大の件数 |

> 応答の例

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

### Response 応答

トランザクションオブジェクトの配列を返す。そのトランザクションがそのトランザクションが関わった任意のビットコインアドレスの正味残高にどのように影響したかの概要情報を、各トランザクションは含みます。

### Errors　エラー

| 応答              | 説明                    |
| --------------- | --------------------- |
| 400 Bad Request | 要求パラメーターが見つからないか正しくない |

## Get Address Unspent Outputs アドレスの未使用分のアウトプットを取得する

特定のアドレスへ行く未使用分のアウトプットを取得します。ブロックの高さの降順に並びます(確認されていないトランザクションの順)。

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

### HTTP Request HTTPリクエスト

`GET /api/v1/address/:address/unspents`

### URL Parameters URL パラメーター

| パラメーター  | 種類              | 必須か | 説明         |
| ------- | --------------- | --- | ---------- |
| address | ビットコインアドレス(文字列) | YES | ビットコインアドレス |

> 応答の例

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

### Response 応答

未使用分のトランザクションオブジェクトの配列を返します。各トランザクションには次の情報が含まれています：

| 名             | 種類   | 説明                       |
| ------------- | ---- | ------------------------ |
| address       | 文字列  | 未使用のアウトプットを持つアドレス        |
| tx_hash       | 数字   | 未使用のアウトプットの額、satoshi単位   |
| tx_output_n | 数字   | 未使用のアウトプットの額、satoshi単位   |
| value         | 数字   | 未使用のアウトプットの額、satoshi単位   |
| blockheight   | 数字   | トランザクションが見られた（ブロックの）高さ   |
| confirmations | 数字   | このトランザクションの確認数           |
| date          | date | ネットワークでトランザクションが見られた日時   |
| script        | 文字列  | 16進数形式のアウトプットビットコインスクリプト |

### Errors　エラー

| 応答              | 説明                    |
| --------------- | --------------------- |
| 400 Bad Request | 要求パラメーターが見つからないか正しくない |

## Get Transaction Details トランザクションの詳細を取得する

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

Gets details for a transaction hash

### HTTP Request

`GET /api/v1/tx/:txid`

### URL Parameters

| Parameter | Type   | Required | Description               |
| --------- | ------ | -------- | ------------------------- |
| txid      | string | YES      | The transaction ID (hash) |

> Example response

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

### Response

Returns detailed information on a transaction, including net effects on all bitcoin addresses involved in the transaction.

## Get Block

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

Gets a Bitcoin block and the transactions within it. You can use 'latest' to get the latest block on the bitcoin network.

### HTTP Request

`GET /api/v1/block/latest`

`GET /api/v1/block/:blockHeight`

`GET /api/v1/block/:blockHash`

### URL Parameters

| Parameter | Type     | Required | Description                                                               |
| --------- | -------- | -------- | ------------------------------------------------------------------------- |
| id        | variable | YES      | The block hash (string), height (number) or 'latest' for the latest block |
| extended  | boolean  | NO       | Set to true to return details on each transaction within the block        |

> Example response

```json
{
    "chainWork": "60359949399610308617",
    "date": "2015-04-15T23:05:50.139Z",
    "height": 326945,
    "id": "00000000000000066fff8a67fbb6fac31e9c4ce5b1eabc279ce53218106aa26a",
    "previous": "00000000eecd159babde9b094c6dbf1f4f63028ba100f6f092cacb65f04afc46",
    "transactions": [
        "e393422e5a0b4c011f511cf3c5911e9c09defdcadbcf16ceb12a47a80e257aaa",
        "fe429dd68ef56613a038238e81b19e2158ef3ad9d9535d1127018bb78ff83537",
        "e1ee8183626b7854c80563d809f85acc6c7cd878de599bee291d0f759a7b2264",
        "dee33ab621e5409c65948d330f01e1adadb533b0f726d80d48e20bb434b4b542",
        "7d83ee2d59e06bc7efe49c647289d98842a7e9ec3b90c3cc965a81df08c08c8f"
    ]
}
```

### Response

| Name         | Type     | Description                                                 |
| ------------ | -------- | ----------------------------------------------------------- |
| date         | datetime | The timestamp the block was seen on the network             |
| id           | string   | Hash of the block                                           |
| previous     | string   | Hash of the previous block in the chain                     |
| transactions | array    | Array of transaction hashes (strings) that are in the block |

# BitGo Instant

BitGo Instant allows sending on-chain transactions which can be credited instantly by recipients, due to a financial guarantee by BitGo against double-spending. Anyone can receive BitGo Instant transactions. In order to send BitGo Instant transactions, you will need either a BitGo KRS wallet, or will need to arrange a collateral agreement with BitGo.

## Receiving

In order to credit BitGo Instant transactions instantly, you will need to respect the **instant: true** property on the transaction objects returned from the [List Wallet Transactions](#list-wallet-transactions) and [Get Wallet Transaction](#get-wallet-transaction) APIs. Instant transactions will also have a field **instantId** which can be used to [Get the Instant Guarantee](#get-instant-guarantee) on a transaction.

## Sending

You will first need a BitGo Instant-compatible wallet. This can be done by creating a KRS-enabled wallet in the web interface, or using the [Create Wallet API](#create-wallet-with-keychains) with a **backupXpubProvider** specified. If you have an existing non-KRS wallet, it can be upgraded to BitGo Instant-capable by arranging a collateral agreement with BitGo.

In order to send a BitGo Instant transaction, use the **instant: true** flag on any of the transaction APIs, such as [Send Coins to Address](#send-coins-to-address) or [Create Transaction](#create-transaction). BitGoD also has the capability to send BitGo Instant transactions through its JSON interface.

BitGo Instant transactions have stricter requirements about the depths of the inputs being spent. This means that the balance of a wallet available for sending a BitGo Instant transaction may be less than the total balance of the wallet. The **instantBalance** property on the wallet object returned by the [Get Wallet API](#get-wallet) will tell you the available balance for sending a BitGo Instant transaction.

When sending a BitGo Instant transaction, the transaction may fail if you do not have enough confirmed unspents in your wallet, or if the transaction would cause you to exceed the risk limits supported for your wallet. The risk limit is determined by the amount of collateral pledged, or by a risk limit BitGo applies to all wallets served by a particular KRS. You will need to handle potential failures when sending a BitGo Instant transaction, and possibly retry as a standard transaction.

BitGo Instant transactions are provided at no additional cost to any customer on our standard transactional pricing plans, including volume discount plans.

# Partner OAuth

BitGo partners may utilize our OAuth endpoints to obtain authorized access and perform actions on behalf of 3rd party BitGo accounts. BitGo complies with the OAuth standard to allow secure access to customer accounts while keeping their passwords safe.

To begin, partners should obtain OAuth application parameters by getting in touch with us. The OAuth flow typically goes as follows:

  1. You redirect users to log into BitGo via our OAuth gateway at `https://www.bitgo.com/oauth/authorize`. In the parameters of this request, you specify the client id, redirect uri and scope.
  2. The user reaches the BitGo OAuth gateway. We ask them if it's ok for you to gain access to the requested scope. They log in with their password and 2FA to confirm.
  3. We redirect the user back to your redirect Uri, with a code parameter. This authorization code is valid for use by your client ID only.
  4. You send the authorization code back to your servers and create a request to BitGo servers with the code, client id and secret. We exchange this for an access token.
  5. You use the access token in the Authorization header to make API calls on behalf of the BitGo user.

### OAuth Variables

| Name          | Description                                                                                                                                                          |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Client Id     | A string (name) of the OAuth application seeking access to 3rd party accounts. This will be public.                                                                  |
| Client Secret | A secret string, stored on the server of the OAuth consumer, used to convert authorization codes for the client id to access tokens.                                 |
| Redirect Uris | A list of acceptable redirect URIs. When you send users to our OAuth gateway for authorization, we send them back to a Uri on your site with the authorization code. |
| Scope         | A list of OAuth scopes. These are the scopes that your application will be allowed to request for from the user.                                                     |

### Scope Values

The scope values define the allowed operations within an OAuth session. These scopes should be provided to the BitGo OAuth gateway such that BitGo may inform the user of your intent and assign you an appropriate authentication code with the requested scope.

Please specify scopes separated using spaces, e.g. "openid profile wallet_view_enterprise wallet_spend_enterprise".

Note that more powerful scopes do not encompass basic ones, ie. wallet_spend does not encompass wallet_view, so you should request for both.

| OAuth Scope Value          | Description of Allowed Actions                                                           |
| -------------------------- | ---------------------------------------------------------------------------------------- |
| openid                     | Verify the user is logged in and get their User ID                                       |
| profile                    | Get the user's profile, including email and phone number                                 |
| wallet_create              | Create wallets on behalf of the user                                                     |
| wallet_view_enterprise   | View wallets created under their enterprise                                              |
| wallet_spend_enterprise  | Spend Bitcoin from wallets created under their enterprise                                |
| wallet_manage_enterprise | Manage and modify settings from wallets created under their enterprise                   |
| wallet_view:#WALLETID      | View a wallet's transactions and addresses                                               |
| wallet_spend:#WALLETID     | Spend Bitcoin from the specific wallet                                                   |
| wallet_manage:#WALLETID    | Manage and modify settings on the specific wallet                                        |
| wallet_view_all          | View all the transactions and addresses for all wallets the user has access to           |
| wallet_freeze_#WALLETID  | Freeze all spend activity on a specific wallet for a given duration (defaults to 1 hour) |
| wallet_freeze_all        | Freeze all spend activity on all of the user's wallets                                   |

## 3rd Party BitGo Login

> Example OAuth gateway redirect to send your users to BitGo OAuth

    https://test.bitgo.com/oauth/authorize?client_id=FBExchange&redirect_uri=https%3A%2F%2Ffbexchange.com%2Foauth_redirect&scope=openid%20profile%20wallet_view_enterprise&email=test@bitgo.com&signup=false
    

The first step in the OAuth flow is to redirect your users to the BitGo OAuth gateway with the Client ID, Scope and Redirect Uri parameters.

* Test Endpoint: https://test.bitgo.com/oauth/authorize
* Production Endpoint: https://www.bitgo.com/oauth/authorize

Parameters may be sent via GET or POST.

### OAuth Request Parameters

> Example redirect URL from BitGo sending users back to your site

    https://fbexchange.com/oauth_redirect?code=440261e26512877b7ebe86e2740da3030d81e88e
    

| Parameter    | Required | Description                                                                                                                  |
| ------------ | -------- | ---------------------------------------------------------------------------------------------------------------------------- |
| client_id    | YES      | Name of the OAuth application seeking access to 3rd party accounts                                                           |
| redirect_uri | YES      | Redirect Uri for BitGo to send users back to your site after they have authenticated on BitGo                                |
| scope        | YES      | List of requested OAuth scopes, separated by spaces. Your access to the user's information will be dependent on these scopes |
| state        | NO       | opaque string that you contain any custom information you wish to provide. Send back as a parameter in the redirect Uri      |
| signup       | NO       | boolean value to be used to control if the user defaults to login or sign up when they land on the OAuth gateway at BitGo    |
| email        | NO       | string value of the email username, used to pre-populate the value                                                           |
| force_email  | NO       | if set to true, the email field (set above) will be readonly on the user's client                                            |

### Our server will redirect

After the user has authorized your application, we will redirect back them to your URL. The redirect will contain the URL parameters:

| Parameter | Description                                                                                           |
| --------- | ----------------------------------------------------------------------------------------------------- |
| code      | Authorizing code string which you can use (together with your secret) to exchange for an access token |

## Obtaining Access Tokens

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

When your user receives the authorization code, send it to the service backend you wish to use to perform actions on behalf of the user.

You then need to exchange the authentication code for an access token that you can use as you would the rest of the api.

* Test Endpoint: https://test.bitgo.com/oauth/token
* Production Endpoint: https://www.bitgo.com/oauth/token

### OAuth Token Request Parameters

| Parameter     | Description                                                                            |
| ------------- | -------------------------------------------------------------------------------------- |
| client_id     | A string (name) of the OAuth application seeking access to 3rd party accounts          |
| client_secret | A secret string, stored on the server of the OAuth application, issued to you by BitGo |
| grant_type    | should be 'authorization_code'                                                         |
| code          | The authentication code you received in the redirect from the above user login step    |

> Example response

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

### OAuth Response

Our server will return an access token for use with the API.

The token must be added as a HTTP header to all API calls in the HTTP "Authorization" header:

`Authorization: Bearer <your token goes here>`

| Parameter     | Description                                                                                                   |
| ------------- | ------------------------------------------------------------------------------------------------------------- |
| token_type    | The type of token e.g. 'bearer'                                                                               |
| access_token  | The token to be used in the Authorization header for subsequent authorized API requests on behalf of the user |
| expires_in    | Number of seconds the token is valid                                                                          |
| expires_at    | Time which the token will expire, in seconds since 1970.                                                      |
| refresh_token | Can be used to obtain another access token, if your session is due to expire                                  |
| id_token      | openid jwt token containing user profile information, if requested as a scope                                 |

## OpenID JSON Web Token

If the partner authentication request had an openid profile scope, a id_token in JSON Web Token (JWT) base64-encoded format will be returned in the response to the OAuth access token request above. You should validate the JSON web token is signed with the HS256 algorithm using your client secret (to prove it came from BitGo).

This token will contain user profile information. You should take care to store this information securely so as not to expose your users to scammers or phishing.

> Example decrypted id_token (from the above access token response)

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

### ID Token Claims

| Parameter               | Format            | Description                                                                |
| ----------------------- | ----------------- | -------------------------------------------------------------------------- |
| iat                     | seconds since utc | time this token was created, in seconds since 1970.                        |
| exp                     | seconds since utc | expiry time of the token (when to accept it until), in seconds since 1970. |
| aud                     | string            | the client id                                                              |
| iss                     | uri string        | the issue identifier                                                       |
| sub                     | string            | BitGo unique user id                                                       |
| access_token            | string            | access token you received from this request, for verification purposes     |
| email                   | string            | user email                                                                 |
| email_verified          | boolean           | if the user email has been verified                                        |
| phone_number            | string            | user phone number                                                          |
| phone_number_verified | boolean           | if the phone number has been verified                                      |
| zoneinfo                | TZ                | time zone information, e.g. US/pacific                                     |

## Refreshing Access Tokens

```javascript
// var refreshToken = 'undefined'; // if unset, uses the refresh token saved from a previous authentication request.
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

The access token obtained in the previous step is typically good for an hour.

To extend a user session, you can request another access token using the request token also sent in the previous step.

> Example response

```json
{
    "token_type":"bearer",
    "access_token":"2dba5167e70d4c18679a9775c57b184951a4fa07",
    "expires_in":3600,
    "expires_at":1418059789,
    "refresh_token":"3f8aa90479b4e3f0ea4544ed302e3bfe91968581"
}
```

* Test Endpoint: https://test.bitgo.com/oauth/token
* Production Endpoint: https://www.bitgo.com/oauth/token

Refresh tokens have a lifetime of 2 weeks from creation and are valid for a *single use only*. A new refresh token is assigned to you each time one is used.

### OAuth Token Request Parameters

| Parameter     | Description                                                                               |
| ------------- | ----------------------------------------------------------------------------------------- |
| client_id     | A string (name) of the OAuth application seeking access to 3rd party accounts             |
| client_secret | A secret string, stored on the server of the OAuth application, issued to you by BitGo    |
| grant_type    | should be 'refresh_token'                                                                 |
| refresh_token | The refresh token received when you exchanged the authorization code for the access token |

# Examples

BitGo has provided examples of how to perform several common wallet operations using our SDK. The more important ones are covered here.<aside class="info"> Our SDK and examples default to the BitGo test environment which is connected to the Bitcoin TestNet. Do refer to the \[Test Environments\](#bitgo-api-endpoints) section for further details. </aside> 

The examples below (and more!) can be found in the `BitGoJS/examples` directory in our SDK repository. Please report problems with the examples via email or Git issues.

### Obtaining the Wallet ID

When you create your wallet on the BitGo test website, the wallet id is the first receiving address. It is also in the URI when you click on it from the main menu.

## Get Wallet Balance

> Code Snippet

```javascript
var bitgo = new BitGoJS.BitGo();

// First, Authenticate
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  console.log("Logged in!" );

  // Get the Balance
  bitgo.wallets().get({type: 'bitcoin', id: id}, function(err, wallet) {
    console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));
  });
});
```

    $ node getWalletBalance.js tester@bitgo.com superhardseypassphrase 0000000 2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
    

> Example output

    Logged in!
    Balance is: 0.6274
    

This simple example shows how to authenticate and get the wallet. The balance in bitcoins can be found on the wallet model.

### Usage

`node getWalletBalance.js <user> <pass> <otp> <walletId>`

### Parameters

| Name     | Type   | Required | Description                                                         |
| -------- | ------ | -------- | ------------------------------------------------------------------- |
| user     | string | YES      | username (your email on the test environment)                       |
| pass     | string | YES      | password on BitGo                                                   |
| otp      | number | YES      | the one-time-password (you can use 0000000 in the test environment) |
| walletId | string | YES      | id of the wallet (also the first receiving address)                 |

## List Wallet Transactions

> Code Snippet

```javascript
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  console.log("Logged in!" );

  // Get the wallet
  bitgo.wallets().get({type: 'bitcoin', id: id}, function(err, wallet) {
    console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));

    // Get the transactions
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
    

> Example output

    Logged in!
    Balance is: 0.6274
    b9573e0e1d8f22fbfe314760b9abd0b6942132cfb2bd7f9fee9713b545a62689: Received 0.56000000BTC on 2014-11-17T21:20:59.000Z
    b3bd8ac76de2340c1159337acdfcaabf08a9470e8157870bd1d84631a0acc67b: Received 0.00010000BTC on 2014-11-17T21:00:58.000Z
    7f5d6a27ea832b2d0a9b63ecdbccecaadbd582b5be41d5bdeabcce72243366a3: Received 0.00010000BTC on 2014-11-17T19:57:18.000Z
    690b8a83e1685869f6138d9a74776f5f868ffc1121fa22f2086e65400f14ef78: Received 0.00010000BTC on 2014-11-17T19:57:18.000Z
    

This example shows how to get the list of transactions on a wallet. This may be useful for verifying received transaction IDs.

### Usage

`node listWalletTransactions.js <user> <pass> <otp> <walletId>`

### Parameters

| Name     | Type                     | Required | Description                                                         |
| -------- | ------------------------ | -------- | ------------------------------------------------------------------- |
| user     | string                   | YES      | username (your email on the test environment)                       |
| pass     | string                   | YES      | password on BitGo                                                   |
| otp      | number                   | YES      | the one-time-password (you can use 0000000 in the test environment) |
| walletId | bitcoin address (string) | YES      | id of the wallet (also the first receiving address)                 |

## Address Labels

    $ node addressLabels.js tester@bitgo.com superhardseypassphrase 0000000
    

> Example output

    Enter the wallet ID: 2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD
    Which label action do you wish to perform? [list, set, delete]: list
     muYhgJrYZHffyGUmuzETjMcBQZJqFo9Vkg    Secret Stash
     2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD   another label
     2MzjET9tYPPZQtsahFvabhaaPWuDdKn3Dh6   descriptive text
     n1wbb1HeZULZwvtcYMLeFz8J9QD7HkG1pa    watch only address
    

This example shows how to list, set, and delete labels on addresses.

### Usage

`node addressLabels.js <user> <pass> <otp>`

### Parameters

| Name | Type   | Required | Description                                                         |
| ---- | ------ | -------- | ------------------------------------------------------------------- |
| user | string | YES      | username (your email on the test environment)                       |
| pass | string | YES      | password on BitGo                                                   |
| otp  | number | YES      | the one-time-password (you can use 0000000 in the test environment) |

## Create Wallet

> Code Snippet

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
    

> Example output

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
    

Creates a wallet on BitGo. The example performs the following steps:

  1. Authenticates with BitGo
  2. Unlocks the account
  3. Creates the user keychain on the client, encrypts it with the password and sends it to the server.
  4. Creates the backup keychain on the client.
  5. Creates the BitGo keychain on the BitGo server.
  6. Creates the wallet with the corresponding public keys to the keychains above.<aside class="warning"> It is **VERY IMPORTANT** to have the user print out / back up their user and backup keys. Failure to do so can result in the loss of funds! </aside> 

### Usage

`node createWallet.js <user> <pass> <otp> <label>`

### Parameters

| Name  | Type   | Required | Description                                                         |
| ----- | ------ | -------- | ------------------------------------------------------------------- |
| user  | string | YES      | username (your email on the test environment)                       |
| pass  | string | YES      | password on BitGo                                                   |
| otp   | number | YES      | the one-time-password (you can use 0000000 in the test environment) |
| label | string | YES      | the wallet name as shown in the BitGo UI                            |

## Send Bitcoins to an Address

> Code Snippet

```javascript
var sendBitcoin = function() {
  // Get the wallet
  bitgo.wallets().get({id: walletId}, function(err, wallet) {
    // Send coins
    wallet.sendCoins({ address: destinationAddress, amount: amountSatoshis, walletPassphrase: walletPassphrase }, function(err, result) {
      console.dir(result);
    });
  });
};

// Authenticate and unlock account to enable sending
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  bitgo.unlock({otp: otp}, function(err) {
    sendBitcoin();
  });
});
```

    $ node sendBitcoin tester@bitgo.com superhardseypassphrase 0000000 2N91XzUxLrSkfDMaRcwQhe9DauhZMhUoxGr superhardseypassphrase 2N4Xz4itCdKKUREiySS7oBzoXUKnuxP4nRD 10000
    

> Example output

    { "tx": "01000000017bc6aca03146d8d10b875781...",
      "hash": "101f1f0f2218b0a0ac9aea1c054fbba7d2e75e09fbeeae7acea0254baa9505b7",
      "fee": 10000 }
    

Sends Bitcoin to another bitcoin address. The example uses the following steps:

  1. Authenticates with BitGo
  2. Unlocks the account to make it possible to spend coins
  3. Gets the wallet from the server by the provided walletId.
  4. Calls the wallet.sendCoins method, which finds the user key, decrypts it, creates and signs the transaction and sends it to BitGo for signing.

### Usage

`node sendBitcoin <user> <pass> <otp> <walletId> <walletPassphrase> <destinationAddress> <amountSatoshis>`

### Parameters

| Name               | Type                     | Required | Description                                                         |
| ------------------ | ------------------------ | -------- | ------------------------------------------------------------------- |
| user               | string                   | YES      | username (your email on the test environment)                       |
| pass               | string                   | YES      | password on BitGo                                                   |
| otp                | number                   | YES      | the one-time-password (you can use 0000000 in the test environment) |
| walletId           | bitcoin address (string) | YES      | the wallet name as shown in the BitGo UI                            |
| walletPassphrase   | string                   | YES      | the passphrase used to encrypt the user's private key               |
| destinationAddress | bitcoin address (string) | YES      | the destination address of the wallet                               |
| amountSatoshis     | string                   | YES      | the number of satoshis to send, e.g. 0.1*1e8 for 0.1 bitcoin        |

## Webhook Oracle Policy

> Code Snippet

```javascript
var setUpPolicyAndSendBitcoin = function() {
  console.log("Getting wallet..");
  bitgo.wallets().get({id: walletId}, function(err, wallet) {
    if (err) { console.log("Error getting wallet!"); console.dir(err); return process.exit(-1); }
    console.log("Balance is: " + (wallet.balance() / 1e8).toFixed(4));
    // Sets the policy
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
        // removing the rule
        wallet.removePolicyRule({id: "webhookRule1"}, function(err, res) { console.log("Removed policy rule"); });
        if (err) { console.log("Error sending coins!"); console.dir(err); }
        if (result) {
          console.dir(result);
        }
      });
    });
  });
};

// Authenticate and unlock account to enable sending
bitgo.authenticate({ username: user, password: password, otp: otp }, function(err, result) {
  bitgo.unlock({otp: otp}, function(err) {
    setUpPolicyAndSendBitcoin();
  });
});
```

```shell
$ node addPolicyWebhookAndSendCoins bencxr@fragnetics.com nicehardpassword 0000000 2MufYDkh6iwNDtyREBeAXcrRDDAopG1RNc2 https://486d7844.ngrok.com/ walletpasw0rd 2N8ryDAob6Qn8uCsWvkkQDhyeCQTqybGUFe 1380000
```

> Example output

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
    

> Example webhook callback (sent to the URL provided, any non-200 response will trigger the policy denial)

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

This example demonstrates how to set up a webhook policy on a wallet capable of executing any custom external logic via a URL endpoint (potentially a script or other program) on the Internet. This allows external state to be mapped into a contract where various users share a single wallet (the URL acts as the "oracle").

When a transaction is made, the URL provided is hit. If it returns a 200 status, then the transaction will be allowed. If not, then the policy rule is fired and the transaction denied.

  1. Authenticates with BitGo
  2. Unlocks the account to make it possible to spend coins and set policy
  3. Gets the wallet from the server by the provided walletId
  4. Sets up the policy which will cause the URL provided to be hit.
  5. Calls the wallet.sendCoins method, which finds the user key, decrypts it, creates and signs the transaction and sends it to BitGo for signing.
  6. Removes the policy and returns the result.

When testing locally, one can create a URL by first setting up a local server (express or any http server will work), and then running a tool such as ngrok to get a public facing url.

### Usage

`node addPolicyWebhookAndSendCoins <user> <pass> <otp> <walletId> <url> <walletPassphrase> <destinationAddress> <amountSatoshis>`

### Parameters

| Name               | Type                     | Required | Description                                                         |
| ------------------ | ------------------------ | -------- | ------------------------------------------------------------------- |
| user               | string                   | YES      | username (your email on the test environment)                       |
| pass               | string                   | YES      | password on BitGo                                                   |
| otp                | number                   | YES      | the one-time-password (you can use 0000000 in the test environment) |
| walletId           | bitcoin address (string) | YES      | the wallet name as shown in the BitGo UI                            |
| url                | http endpoint (string)   | YES      | the URL to set up the policy with                                   |
| walletPassphrase   | string                   | YES      | the passphrase used to encrypt the user's private key               |
| destinationAddress | bitcoin address (string) | YES      | the destination address of the wallet                               |
| amountSatoshis     | string                   | YES      | the number of satoshis to send, e.g. 0.1*1e8 for 0.1 bitcoin        |

## Recover Wallet

RecoverWallet is a tool that can recover funds from a BitGo wallet without using the BitGo service. It is provided for demonstration purposes and to prove that BitGo wallets are 100% recoverable even if the BitGo Service is unavailable. It is not expected that this tool would be used for production environments.

Given only the information on a wallet's keycard, the tool constructs a transaction which moves all of the funds within that wallet to a new account of the user's choice. This is done without using any BitGo Service APIs.

The RecoverWallet tool is interactive or command line driven.

### Usage

node recoverwallet.js --userKey <userkey from keycard> --backupKey <backupkey from keycard> --bitgoKey <bitgo public key from keycard>

### Parameters

| Name        | Meaning                                                                         |
| ----------- | ------------------------------------------------------------------------------- |
| userKey     | The user extended private key for the wallet. (Box A from the Wallet KeyCard)   |
| backupKey   | The backup extended private key for the wallet. (Box B from the Wallet KeyCard) |
| bitgoKey    | The bitgo extended public key for the wallet. (Box C from the Wallet KeyCard)   |
| testnet     | Flag to use testnet instead of the production bitcoin network                   |
| nosend      | Flag to create the transaction but not send it                                  |
| password    | The password to use to decrypt the userKey and backupKey                        |
| destination | The bitcoin address to which you want to send the recovered funds               |