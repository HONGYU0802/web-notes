# 微信支付

## 支付方式

1. 付款码支付

付款码支付，即日常所说的被扫支付，这是一种纯用于线下场景的支付方式，由用户出示微信客户端内展示的付款二维码，商户使用扫码设备扫码后完成支付。

2. Native原生支付

Native原生支付，即日常所说的扫码支付，商户根据微信支付协议格式生成的二维码，用户通过微信“扫一扫”扫描二维码后即进入付款确认界面，输入密码即完成支付。

3. JSAPI网页支付

JSAPI网页支付，即日常所说的公众号支付，可在微信公众号、朋友圈、聊天会话中点击页面链接，或者用微信“扫一扫”扫描页面地址二维码在微信中打开商户HTML5页面，在页面内下单完成支付。

4. APP支付

APP支付是指商户已有的APP，通过对接微信支付API及SDK，实现从商户APP发起交易后跳转到微信APP，用户完成支付后跳回商户APP的场景。

5. 小程序支付

小程序支付是指在商户既有的小程序内通过对接微信支付API，实现用户在小程序内完成交易的场景。

## 业务系统

1. 微信支付系统

微信支付系统是指完成微信支付流程中涉及的API接口、后台业务处理系统、账务系统、回调通知等系统的总称。

2. 商户收银系统

商户收银系统即商户的POS收银系统，是录入商品信息、生成订单、客户支付、打印小票等功能的系统。接入微信支付功能主要涉及到POS软件系统的开发和测试，所以在下文中提到的商户收银系统特指POS收银软件系统。

3. 商户后台系统

商户后台系统是商户后台处理业务系统的总称，例如：商户网站、收银系统、进销存系统、发货系统、客服系统等。


## 前期准备工作

### 申请 APPID

由于微信支付的产品体系全部搭载于微信的社交体系之上，所以直连商户或服务商接入微信支付之前，都需要有一个**微信社交载体**，该载体对应的ID即为APPID。OpenID为用户在直连商户应用下的用户标识。

### 申请支付能力

个体工商户的申请材料

1. 营业执照：彩色扫描件或数码照片
2. 对公银行账户/法人对私账户：包含开户行省市信息，开户账号
3. 法人身份证：彩色扫描件或数码照片

微信审核通过后，在线签署协议


### 申请成为直连商户 merchant

在[商户平台](https://pay.weixin.qq.com/index.php/core/home/login?return_url=%2F)上申请成为直连商户。

直连模式，商户自行申请入驻微信支付，无需服务商协助。

微信支付—>直连商户，资金流在用户当日转入微信支付，次日微信支付转入商户。

### 申请 mchid

商户在微信侧申请入驻的**收款账号**，在商户号申请平台申请成功后，会向服务商填写的联系邮箱下发通知邮件，内容包含申请成功的mchid及其登录账号密码。 一个mchid只能对应一个结算币种，若需要使用多个币种收款，需要申请对应数量的mchid。

### 绑定 APPID 及 mchid

直连模式下，APPID与mchid之间的关系为多对多，即一个APPID下可以绑定多个mchid，而一个mchid也可以绑定多个APPID。

### 配置 API key

登录微信商户平台，进入【账户中心 > API安全 】目录，设置APIV3密钥，主要用于对敏感字段信息的加密或解密，微信支付在回调通知和平台证书下载接口中，对关键信息进行了AES-256-GCM加密。API v3密钥是加密时使用的对称密钥。

### 下载并配置商户 API 证书

商户可登录微信商户平台，在【账户中心】->【API安全】目录下载证书，主要用于API请求的签名生成及验证，包含商户的商户号、公司名称、公钥信息的证书，是由证书授权机构 (Certificate Authority ，简称CA)签发颁发的证书。商户申请商户API证书时，会生成商户私钥，并保存在本地证书文件夹的文件apiclient_key.pem 中。

商户签名使用 商户私钥 ，证书序列号包含在请求HTTP头部的  Authorization的serial_no

微信支付签名使用微信支付平台私钥，证书序列号包含在应答HTTP头部的Wechatpay-Serial

商户上送敏感信息时使用微信支付平台公钥加密，证书序列号包含在请求HTTP头部的 Wechatpay-Serial 


# 小程序接入微信支付

登录小程序后台，点击左侧导航栏的微信支付，在页面中进行开通，绑定一个已有的微信支付商户号。

注意：小程序不能通过拉起H5页面做jsapi支付，小程序内只能使用小程序支付。

## 业务流程

1. 用户在小程序下单发起支付
2. 商户后台系统生成订单，再调用下单 api 生成预支付订单，生成带签名的支付信息。
3. 小程序通过**预付单标识**调起微信支付，微信支付系统验证支付权限通过，成功调起支付。
4. 用户输入支付密码，微信支付系统验证通过，完成支付交易。
5. 给用户下发支付结果通知，并行通知商户支付结果，或商户调用订单查询接口主动获取支付状态。

[API 文档](https://pay.weixin.qq.com/wiki/doc/apiv3/apis/chapter3_5_1.shtml)

### 商户后台生成带签名的支付信息

[生成方法文档](https://pay.weixin.qq.com/wiki/doc/apiv3/wechatpay/wechatpay4_0.shtml)

微信支付API v3 要求商户对请求进行签名。微信支付会在收到请求后进行签名的验证。如果签名验证不通过，微信支付API v3将会拒绝处理请求，并返回401 Unauthorized。

1. 构造签名串

签名串的具体格式

```
HTTP请求方法\n
URL\n
请求时间戳\n
请求随机串\n
请求报文主体\n
```

2. 计算签名值 signature（C端调用也是使用这个值，要保存好）

```
使用商户私钥对待签名串进行 SHA256 with RSA 签名，并对签名结果进行 Base64编码得到签名值。
绝大多数编程语言提供的签名函数支持对签名数据进行签名，强烈建议商户调用该类函数。
```

3. 设置HTTP头

微信支付商户API v3要求请求通过HTTP Authorization头来传递签名。

```
认证类型: WECHATPAY2-SHA256-RSA2048

签名信息:
1. 发起请求的商户（包括直连商户、服务商或渠道商）的商户号 mchid
2. 商户API证书序列号serial_no，用于声明所使用的证书
3. 请求随机串nonce_str
4. 时间戳timestamp
5. 签名值signature
```

最终组成一个包含了签名的HTTP请求

```
$ curl https://api.mch.weixin.qq.com/v3/certificates -H 'Authorization: WECHATPAY2-SHA256-RSA2048 mchid="1900009191",nonce_str="593BEC0C930BF1AFEB40B4A08C8FB242",signature="uOVRnA4qG/MNnYzdQxJanN+zU+lTgIcnU9BxGw5dKjK+VdEUz2FeIoC+D5sB/LN+nGzX3hfZg6r5wT1pl2ZobmIc6p0ldN7J6yDgUzbX8Uk3sD4a4eZVPTBvqNDoUqcYMlZ9uuDdCvNv4TM3c1WzsXUrExwVkI1XO5jCNbgDJ25nkT/c1gIFvqoogl7MdSFGc4W4xZsqCItnqbypR3RuGIlR9h9vlRsy7zJR9PBI83X8alLDIfR1ukt1P7tMnmogZ0cuDY8cZsd8ZlCgLadmvej58SLsIkVxFJ8XyUgx9FmutKSYTmYtWBZ0+tNvfGmbXU7cob8H/4nLBiCwIUFluw==",timestamp="1554208460",serial_no="1DDE55AD98ED71D6EDD4A4A16996DE7B47773A8C"
```

### 商户生成预支付订单

```JS
'POST https://api.mch.weixin.qq.com/v3/pay/transactions/jsapi'

body:
{
	"mchid": "1900006XXX",
	"out_trade_no": "1217752501201407033233368318",
	"appid": "wxdace645e0bc2cXXX",
	"description": "Image形象店-深圳腾大-QQ公仔",
	"notify_url": "https://weixin.qq.com/",
	"amount": {
		"total": 1,
		"currency": "CNY"
	},
	"payer": {
		"openid": "o4GgauInH_RCEdvrrNGrntXDuXXX"
	}
}

res:
{
	"prepay_id": "wx26112221580621e9b071c00d9e093b0000" // 该值有效期为2小时
}
```

### 小程序端调起微信支付

```JS
wx.requestPayment({  
  "timeStamp": "1395712654",   // 时间戳，自1970年以来的秒数    
  "nonceStr": "e61463f8efa94090b1f366cccfbbb444",      // 随机串    
  "package": "prepay_id=wx21201855730335ac86f8c43d1889123400", // 预付单标识
  "signType": "RSA",     // 微信签名方式，应与后台下单时的值一致
  "paySign": "oR9d8PuhnIc+YZ8cBHFCwfgpaK9gd7vaRvkYD7rthRAZ\/X+QBhcCYL21N7cHCTUxbQ+EAt6Uy+lwSN22f5YZvI45MLko8Pfso0jm46v5hqcVwrk6uddkGuT+Cdvu4WBqDzaDjnNa5UK3GfE1Wfl2gHxIIY5lLdUgWFts17D4WuolLLkiFZV+JSHMvH7eaLdT9N5GBovBwu5yYKUR7skR8Fu+LozcSqQixnlEZUfyE55feLOQTUYzLmR9pNtPbPsu6WVhbNHMS3Ss2+AehHvz+n64GDmXxbX++IOBvm2olHu3PsOUGRwhudhVf7UcGcunXt8cqNjKNqZLhLw4jq\/xDg==", //微信签名
  success (res) { },
  fail (res) { }
})
```

### 支付结果查询

```JS
// 通过微信支付订单号查询
'GET  https://api.mch.weixin.qq.com/v3/pay/transactions/id/{transaction_id}?mchid={mchid}'
// 通过商户订单号查询
'GET https://api.mch.weixin.qq.com/v3/pay/transactions/out-trade-no/{out_trade_no}?mchid={mchid}'
// response
{
	"amount": {
		"currency": "CNY",
		"payer_currency": "CNY",
		"payer_total": 1,
		"total": 1
	},
	"appid": "wxdace645e0bc2cXXX",
	"attach": "",	// 附加数据，在查询API和支付通知中原样返回，可作为自定义参数使用，实际情况下只有支付完成状态才会返回该字段
	"bank_type": "OTHERS",	// 银行类型，采用字符串类型的银行标识。银行标识请参考《银行类型对照表》 
	"mchid": "1900006XXX",
	"out_trade_no": "44_2126281063_5504",	// 商户系统内部订单号
	"payer": {
		"openid": "o4GgauJP_mgWEWictzA15WT15XXX"
	},
	"promotion_detail": [],
	"success_time": "2021-03-22T10:29:05+08:00",
	"trade_state": "SUCCESS",	// 交易状态，枚举值 SUCCESS、REFUND、NOTPAY、CLOSED、REVOKED、USERPAYING、PAYERROR
	"trade_state_desc": "支付成功",	// 交易状态描述 
	"trade_type": "JSAPI",	// 交易类型，枚举值 JSAPI、NATIVE、APP、MICROPAY、MWEB、FACEPAY（公众号支付、扫码支付、APP支付、付款码支付、H5支付、刷脸支付）
	"transaction_id": "4200000891202103228088184743"	// 微信支付系统生成的订单号
}
```


# 公众号接入微信支付

公众号接入支付后，可以通过 JSAPI 支付产品来完成在公众号、朋友圈、聊天窗口等微信内的收款需求。

[API 文档](https://pay.weixin.qq.com/wiki/doc/apiv3/open/pay/chapter1_1_1.shtml)

## 前期准备工作

登录【微信支付商户平台—>产品中心—>开发配置—>支付配置配置—>支付授权目录 】，设置后一般5分钟内生效。

如果支付授权目录设置为顶级域名（例如：https://www.weixin.com/ ），那么只校验顶级域名，不校验后缀。

如果支付授权目录设置为多级目录，就会进行全匹配，https://www.weixin.com/abc/123/ 就必须是这个目录。

开发JSAPI支付时，在JSAPI下单接口中要求必传用户openid，而获取openid则需要在微信公众平台设置获取openid的域名，只有被设置过的域名才是一个有效的获取openid的域名，否则将获取失败。

## 业务流程

1. 用户下单
2. 商户后台系统生成订单，再调用下单 api 生成预支付订单，生成带签名的支付信息。（同小程序）
3. 用户发起支付，商户在前端调起微信支付，微信支付系统验证支付权限通过，成功调起支付。
4. 用户输入支付密码，微信支付系统验证通过，完成支付交易。
5. 给用户下发支付结果通知，并行通知商户支付结果，或商户调用订单查询接口主动获取支付状态。

```JS
wx.chooseWXPay({
  timestamp: 0, // 支付签名时间戳
  nonceStr: '', // 支付签名随机串，不长于 32 位
  package: '', // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=\*\*\*）
  signType: '', // 微信支付V3的传入 RSA ,微信支付V2的传入格式与V2统一下单的签名格式保持一致
  paySign: '', // 支付签名
  success: function (res) {
    // 支付成功后的回调函数
  }
});
```


# PC 网站接入微信支付

微信支付支持完成域名ICP备案的网站接入支付功能。PC网站接入支付后，可以通过JSAPI支付或Native支付，自行开发生成二维码，用户使用微信“扫一扫”来完成支付。

## 前期准备工作

JSAPI支付接入步骤同微信公众号。

## Native支付的业务流程

1. 用户在平台下单
2. 商户后台系统生成订单，商户调用下单 api 生成预支付交易以获取支付二维码链接code_url
3. 用户扫描二维码，微信支付系统验证二维码是否还处于有效期，是则前端成功调起微信支付。
4. 用户输入密码完成支付，微信验证成功后完成支付交易。
5. 给用户下发支付结果通知，并行通知商户支付结果，或商户调用订单查询接口主动获取支付状态。

```JS
'POST https://api.mch.weixin.qq.com/v3/pay/transactions/native'

// response
{
	"code_url": "weixin://wxpay/bizpayurl?pr=p4lpSuKzz"	// 将code_url值生成二维码图片展示在前端
}
```


# 移动端网站接入微信支付

H5支付主要用于触屏版的手机浏览器请求微信支付的场景，方便从外部浏览器唤起微信客户端进行支付。

## 前期准备工作

1. 开通H5支付权限，前往微信支付商户平台—>产品中心—>H5支付—>申请开通
2. 配置H5支付域名，登录【微信支付商户平台—>产品中心—>开发配置—>H5支付】，设置后一般10分钟内生效。域名必须通过ICP备案，域名填写格式不包含http://或https://。

## H5支付的业务流程

1. 用户下单在浏览器下单，商户后台系统生成订单，商户调用下单 api 生成预支付交易以获取支付跳转的 url。
2. 浏览器获得url后，跳转到微信支付中间页，微信支付后台校验安全返回结果给浏览器。
3. 浏览器调用返回的 deeplink 打开微信客户端调用支付。
4. 用户输入密码支付后，微信支付后台将支付结果返回商户，并调起浏览器跳转到支付结果页面。

```JS
'POST https://api.mch.weixin.qq.com/v3/pay/transactions/h5'

// response
{
	"h5_url": "https://wx.tenpay.com/cgi-bin/mmpayweb-bin/checkmweb?prepay_id=wx2916263004719461949c84457c735b0000&package=2150917749"
}
```
