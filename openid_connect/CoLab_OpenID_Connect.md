[OpenID Connect入門](https://supporterzcolab.com/event/345/)
=====

2018/04/04

# OpenID Connectの用語説明

## OpenID は認証

拡張で Attribute eXchange(AX) で属性追加することもある

比較的古い SOAP, XML でやりとり

## OAuth は認可
ユーザのリソースに(Web APIが)アクセスすることが目的

- 1.0a
	- リクエストパラメータに署名
- 1.0 Protocol
- 2.0
	- HTTPS で簡素に

1.0 と 2.0 は互換性ないよ

## OpenID Connect

OpenID とは別物、 OAuth 2.0 を拡張したプロトコル
REST like, Json やりとり


認証、認可両方


属性情報取得機能もできる


**ID連携のトレンド！**

SAML よりも新規サービスでは採用される傾向


https://openid.net/connect

(image)[http://openid.net/wordpress-content/uploads/2014/02/OpenIDConnect-Map-4Feb2014.png]


## OpenID connect 認証フロー

- Implicit Flow
	- JS やネイティブアプリなどのクライアントサイドで認証
	- サーバサイドでやらないやつ
	- Reflesh ないため長期間は向かない
- Authorization Code Flow
	- サーバサイド側で認証
	- implicit Flow に比べて認証強度が高い
	- Refresh Token を発行するため長期間でもいける
- Hybrid Flow
	- サーバ, クライアント両方で認証
	- あんまり使わない

## 用語集

- IdP: Identity Provider
	- 認証を行って ID, 属性を提供
- OP: O[enID Provider
	- IdP とほぼ同じ、 OpenID での呼び方
- RP: Relying Party
	- IdP の認証を利用して ユーザにサービスを提供する
- Resource Server
	- ユーザの属性情報などを提供
- Claim
	- Idp や OP などの属性情報


# Authorization Code Flow

- Authorization Request
	- Req Param に `responce_type=code`
- Responce
	- param に `code=認可コード` + `state=セッションに紐付けたState`
- Token REsponce
	- Body に `token_type: Bearer`
	- OAuth Bearer
	- `id_token` を検証してユーザ認証

# ID Token
issuer(idp: id_token発行したひと) が audience(RP) のために subject(id_tokenの対象, End-User) を認証したかを示すトークン

## format: JSON Web Token

JSON を URL Safeな Base64 エンコードしたシグエンチャつきトークン
- ヘッダ、ペイロード、シグネチャで構成

### ヘッダ

```json
{
	"type":"JWT", じょっとと読む JSON Web Tokeno
	"alg":"RS256" アルゴリズム, RSA-SHA256
}
```

### ペイロード

```json
{
	"iss":"url", issuer, トークン発行者
	"sub":"123456", subject, ユーザ識別子
	"aud":"abcdfg", audience, client ID トークン払い出し先
	"nonce":"xyz" リプレイ攻撃対策用
	"iat""21234581"
	"exp":123456 expiration, 有効期限, 同じ期限にしなくても良い
}
```

## シグネチャ

### 生成
Base64 エンコード

- URS Safe
	- + -> -
	- / -> _
	- = -> ''

- Json の {" を Base64 にすると eyJ になる
- ピリオドで繋ぐ
- シグネチャにかけて Base64 にで URLセーフにエンコード
- ピリオドでつなげる


### 検証
- ヘッダとペイロードを入力
- 3つ目を公開鍵で検証



# UserInfo Endpoint

OpenID Connect ではよく利用される指名, 住所, メールアドレスなどの属性情報を取得しやすいように Climを指定する

- Req
	- bearer つける
- Res
	- いろんな属性が帰ってくる
	- 氏名, 送信可能なメールアドレス, 住所, 電話とか

- scope
	- 必要な属性情報の group 的なやつ
	- scope によって、名前取るよ！住所取るよ！みたいなやつを画面に出す
