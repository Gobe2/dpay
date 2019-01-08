# 1. 请求说明

每个请求需要设置`Content-Type: application/json`


每个请求头需要token字段，使用Base64编码，示例：
```

{
	"device": {
		"language": "zh_CN",
		"system": "IOS",
		"deviceId": "4FAA95D4-F86A-4105-8143-5E6A54097D90",
		"packageName": "com.wangchunping.MerchantDemo",
		"appVersion": "1.0.0",
		"channel": "73088886094001" //渠道
	},
	"signType": "RSA2",
	"signature": "Kl\/slljTqkZWCZ+gTZR7ay9Zs2hW+HPIeteSxKHiIzXqCtSq\/WUMr0x1aeOdo+2ZJvpD4iejojFdOhdfqoOucJCn\/Dazo2xrR8IQzczNC5jT+JW1VDS566VDE4U0l7Xq49U8FxVg3y8NOBbvo8tFLkBr5ingVs2VGlFpy39vkbg+DHrqpjn3n7nlgRtM+2\/ods2KQN2vZOgVxZDg9wvV1RdeK4ul6v7iGhnH4gM7rN5nhKop5icm+F8CQ8JCmviSGjY7t21Jx4p5oG2XTUIrTOoJRYbR5MZg5DglVEiUp1qVXGycvaN87x+z2iiCU79bxkRG6p3wP0jUA9KmZCKq9A==", //请求签名
	"merchantId": "10000000000003", //商户id
	"apiVersion": "1.0.0",
	"timestamp": "1545959389125" //时间戳
}

```

请求参数加密，及签名说明：

1. 随机生成密钥`key`和`iv`,使用`key`和`iv`对请求参数进行`AES`加密，形成密文`cipher`。
2. 使用私钥`privateKey`对`key`进行RSA加密，形成加密的`key'`
2. 使用私钥`privateKey`对`b(cipher) + b(key') + b(iv)`进行`SHA256WithRSA`签名，形成`signature`。

`AES`使用`AES/CBC/PKCS5Padding`模式。

请求体为`b(key')`,`b(iv)`,`b(cipher)`。
记`b(x)`为对`x`进行`Base64`编码。


# 2. API Reference

1. [POST /dcpayCore/payBills/prepay](#post-paybillsprepay-生成支付订单)
2. [POST /dcpayCore/payBills/bizno](#post-paybillsbizno-查询订单)

##  POST /payBills/prepay 生成支付订单

请求参数：

参数名称 | 是否必填 | 类型 | 描述 | 默认值 | 取值范围
---|---|---|---|---|---
refBizNo | true | string | 订单号 | |
coinId | true | string | 币种 | | 34190899187000(ETH)
amount | true | string | 数量 | |
industry | false | string | 行业 |
goodsType | false | string | 商品类型 |
goodsTag | false | string | 商品标签 |
attach | false  | string | 附加信息 |

请求响应例子：
```
//请求
{
	"amount": "0.01",
	"coinId": "34190899187000",
	"goodsTag": "20",
	"industry": "qr",
	"refBizNo": "2891018132",//订单号（商户定义）
	"attach": "11221"
}
//响应
{
	"code": "200",
	"message": "成功",
	"data": {
		"merchantId": "10000000000003",
		"merchantName": "测试商户3",
		"payBillNo": "63104203643110",//支付订单号（支付系统定义）
		"refBizNo": "289101861",//订单号
		"coinId": "34190899187000",
		"amount": "0.001",
		"orginAmount": "1000000000000000",
		"toAddr": "0x91f8654587917f3a0c7cfc5fa05bd86dc0162ddb",//商户地址
		"tokenAddr": null,
		"txData": null,
		"attach": null,
		"industry": "",
		"goodsType": null,
		"goodsTag": null,
		"channel": 73088886094001
	}
}

```

## POST /payBills/bizno 查询订单

参数名称 | 是否必填 | 类型 | 描述 | 默认值 | 取值范围
---|---|---|---|---|---
orderNo | true | string | 订单号 | |

请求响应例子：
```
//请求
{
"orderNo":["1525313845606","112"]
}

//响应
{
    "code": "200",
    "message": "成功",
    "data": [
        {
            "parentNo": 69847127374, //支付订单号
            "refBizNo": "1525313845606",//订单号
            "coinName": "PH",
            "amount": 1,
            "merchantName": "测试商户",
            "attach": null,
            "toAddr": "0xc2606bd69171c68b891b8479a86be341c43514ba",
            "tokenAddr": null,
            "txHash": "",
            "fromAddr": "",
            "billType": 1,
            "status": 0,
            "createTime": 1525313845000,
            "completeTime": 1526954971000
        }
    ]
}

```


# 商户APP demo
[安卓demo](https://github.com/zhukunpeng0503/PayDemo)

[IOS demo](https://github.com/wangchunping2011/MerchantDemo)
