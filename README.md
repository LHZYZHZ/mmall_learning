   这是我在慕课网上的一个初学时的商城项目，这个项目是前后端分离的，这份代码是后端代码，包含了商城的主要功能，包括前台和后台的数据管理，购物车功能，订单功能，支付功能，商品管理功能等，这个项目适合初学者学习，亮点应该就是对接了支付宝，可以很好的对自己对接第三方工具做个练习及总结。想学习的同学可以去慕课网购买相应的实战项目，老师是真的很认真，而且对初学者来说，肯定是受益的。

对接支付宝的一些概念
沙箱调试环境：是协助开发者进行借口开发与主要功能测试的辅助环境，在部分时间不可用
信息配置会有APPID，支付宝网关，两个RSA密钥

沙箱测试应用配置：
应用网关
回调地址：这里是写自己项目的地址
ASE秘钥：传输数据时加密。

支付宝扫码支付主流程
总的来说，项目会先通过借口请求预下单,支付宝后台会返回一个二维码信息，然后项目这边会展示二维码信息,支付宝钱包扫码，会收到第二次回调。
1.用户系统调用alipay.trade.precieate，发起预下单请求
1.1.支付宝同步订单二维码率
2.用户系统使用二维码率生成订单二维码
3.调用alipay.trade.query查询交易状态，同步返回查询结果
3.1同步返回查询结果
4.扫码后确认支付
4.1若支付成功，异步通知结果
如超过轮询时间后仍未使用
4.1.1调用alpay.trade.cancel取消交易
4.1.2同步返回查询结果

支付宝扫码支付重要字段

关键入参
out_trade_no 用户订单号
total_amount 订单金额
subject  订单标题
store_id 用户门店编号
timeout_express 交易超时时间

关键出参
qr_code 订单二维码图片地址

重要参数
trade_stuatus 交易状态
total_amount  本次交易支付的订单金额
buyer_pay_amount  用户在交易中支付的金额

交易状态说明
WAIT_BUYER_PAY
TRADE_CLOSED
TRADE_SUCCESS
TRADE_FINISHED

支付渠道说明
COUPON 支付宝红包
ALIPAYACCOUNT 支付宝余额
PCREDIT 蚂蚁花呗

重要细节
主动轮询：利用间隔时间不断查询订单的交易状态
回调：付款成功之后，支付宝会调用我们的接口，把一些支付的细节，交易状态传回给我们

避免单边账

同步请求的加签和验证签名
params key参数名称 value参数值
privateKey 加签私钥
charset　加签字符集
String AlipaySignature,rsaSign(Map<String,String> params,String privateKey,String charset);

Content 待检验字符串
sign 签名值
publicKey 验签公钥
charset 验签字符集
boolean AlipaySignature,rsaCheckContent(String content,String sign,String publicKey,String charset);

回调的验证(金额，订单号，订单状态，交易状态)
要使回调有幂等性，过滤掉重复通知，当数据有变化时应该保持住，不能有回调就更新数据
要验证并确保可接受的异步通知是支付宝发出的

回调请求的返回
支付宝调用我们的接口，我们要反馈给支付宝是否是success字段，否则支付宝回经过一定时间的间隔不断轮询

回调的调试方法
外网远程debug(一般项目部署在阿里云上)
保证远程代码版本和本地代码版本一致
及时关闭debug端口
添加以下配置，address为开放远程debug的端口号
CATALIN_OPS=“-server -Xdebug -Xnoagent -Djava.complier=NONE-Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005”
把开放远程debug的端口加到防火墙中
sudo vim /etc/sysconfig/iptables
-A INPUT -p tcp -m tcp --dport 5005 -j ACCEPT

内网穿透(netapp):通过软件获得一个可以外网可以访问的域名

1.https://natapp.cn
2.注册帐号
3.设置对外端口
4.下载客户端
5.加入authtoken参数
6.通过开放域名进行测试



