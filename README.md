<h1 align="center">Pay</h1>

<p align="center">
<a href="https://scrutinizer-ci.com/g/yansongda/pay/?branch=master"><img src="https://scrutinizer-ci.com/g/yansongda/pay/badges/quality-score.png?b=master" alt="Scrutinizer Code Quality"></a>
<a href="https://scrutinizer-ci.com/g/yansongda/pay/build-status/master"><img src="https://scrutinizer-ci.com/g/yansongda/pay/badges/build.png?b=master" alt="Build Status"></a>
<a href="https://packagist.org/packages/yansongda/pay"><img src="https://poser.pugx.org/yansongda/pay/v/stable" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/yansongda/pay"><img src="https://poser.pugx.org/yansongda/pay/downloads" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/yansongda/pay"><img src="https://poser.pugx.org/yansongda/pay/v/unstable" alt="Latest Unstable Version"></a>
<a href="https://packagist.org/packages/yansongda/pay"><img src="https://poser.pugx.org/yansongda/pay/license" alt="License"></a>
</p>

开发了多次支付宝与微信支付后，很自然产生一种反感，惰性又来了，想在网上找相关的轮子，可是一直没有找到一款自己觉得逞心如意的，要么使用起来太难理解，要么文件结构太杂乱，只有自己撸起袖子干了。

欢迎 Star，欢迎 PR！

## 特点
- 命名不那么乱七八糟
- 隐藏开发者不需要关注的细节
- 根据支付宝、微信最新 API 开发而成
- 高度抽象的类，免去各种拼json与xml的痛苦
- 符合 PSR 标准，你可以各种方便的与你的框架集成
- 文件结构清晰易理解，可以随心所欲添加本项目中没有的支付网关
- 方法使用更优雅，不必再去研究那些奇怪的的方法名或者类名是做啥用的

## 运行环境
- PHP 5.6+
- composer

## 支持的支付网关

由于各支付网关参差不齐，所以我们抽象了两个方法 `driver()`，`gateway()`。

两个方法的作用如下：

`driver()` ： 确定支付平台，如 `alipay`,`wechat`;  

`gateway()`： 确定支付网关。通过此方法，确定支付平台下的支付网关。例如，支付宝下有 「电脑网站支付」，「手机网站支付」，「APP 支付」三种支付网关，通过传入 `web`,`wap`,`app` 确定。

详细思路可以查看源代码。

### 1、支付宝

- 电脑支付
- 手机网站支付
- APP 支付

SDK 中对应的 driver 和 gateway 如下表所示：  

| driver | gateway |   描述       |
| :----: | :-----: | :-------:   |
| alipay | web     | 电脑支付     |
| alipay | wap     | 手机网站支付  |
| alipay | app     | APP 支付  |
  
### 2、微信

- 公众号支付
- 小程序支付
- H5 支付
- 扫码支付
- 刷卡支付

SDK 中对应的 driver 和 gateway 如下表所示：

| driver | gateway |   描述     |
| :----: | :-----: | :-------: |
| wechat | mp      | 公众号支付  |
| wechat | miniapp | 小程序支付  |
| wechat | wap     | H5 支付    |
| wechat | scan    | 扫码支付    |
| wechat | pos     | 刷卡支付    |

## 支持的方法

所有网关均支持以下方法

- pay    (支付接口)
- refund (退款接口)
- close  (关闭订单接口)
- verify (验证服务器返回消息是否合法)
  
## 安装
```shell
composer required yansongda/pay
```

## 使用说明

### 0、一个完整的例子:
```php
use Yansongda\Pay\Pay;

$config = [
    'alipay' => [
        'app_id' => '2016082000295641',
        'notify_url' => 'http://yansongda.cn/alipay_notify.php',
        'return_url' => 'http://yansongda.cn/return.php',
        'ali_public_key' => 'MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuWJKrQ6SWvS6niI+4vEVZiYfjkCfLQfoFI2nCp9ZLDS42QtiL4Ccyx8scgc3nhVwmVRte8f57TFvGhvJD0upT4O5O/lRxmTjechXAorirVdAODpOu0mFfQV9y/T9o9hHnU+VmO5spoVb3umqpq6D/Pt8p25Yk852/w01VTIczrXC4QlrbOEe3sr1E9auoC7rgYjjCO6lZUIDjX/oBmNXZxhRDrYx4Yf5X7y8FRBFvygIE2FgxV4Yw+SL3QAa2m5MLcbusJpxOml9YVQfP8iSurx41PvvXUMo49JG3BDVernaCYXQCoUJv9fJwbnfZd7J5YByC+5KM4sblJTq7bXZWQIDAQAB',
        'private_key' => 'MIIEpAIBAAKCAQEAs6+F2leOgOrvj9jTeDhb5q46GewOjqLBlGSs/bVL4Z3fMr3p+Q1Tux/6uogeVi/eHd84xvQdfpZ87A1SfoWnEGH5z15yorccxSOwWUI+q8gz51IWqjgZxhWKe31BxNZ+prnQpyeMBtE25fXp5nQZ/pftgePyUUvUZRcAUisswntobDQKbwx28VCXw5XB2A+lvYEvxmMv/QexYjwKK4M54j435TuC3UctZbnuynSPpOmCu45ZhEYXd4YMsGMdZE5/077ZU1aU7wx/gk07PiHImEOCDkzqsFo0Buc/knGcdOiUDvm2hn2y1XvwjyFOThsqCsQYi4JmwZdRa8kvOf57nwIDAQABAoIBAQCw5QCqln4VTrTvcW+msB1ReX57nJgsNfDLbV2dG8mLYQemBa9833DqDK6iynTLNq69y88ylose33o2TVtEccGp8Dqluv6yUAED14G6LexS43KtrXPgugAtsXE253ZDGUNwUggnN1i0MW2RcMqHdQ9ORDWvJUCeZj/AEafgPN8AyiLrZeL07jJz/uaRfAuNqkImCVIarKUX3HBCjl9TpuoMjcMhz/MsOmQ0agtCatO1eoH1sqv5Odvxb1i59c8Hvq/mGEXyRuoiDo05SE6IyXYXr84/Nf2xvVNHNQA6kTckj8shSi+HGM4mO1Y4Pbb7XcnxNkT0Inn6oJMSiy56P+CpAoGBAO1O+5FE1ZuVGuLb48cY+0lHCD+nhSBd66B5FrxgPYCkFOQWR7pWyfNDBlmO3SSooQ8TQXA25blrkDxzOAEGX57EPiipXr/hy5e+WNoukpy09rsO1TMsvC+v0FXLvZ+TIAkqfnYBgaT56ku7yZ8aFGMwdCPL7WJYAwUIcZX8wZ3dAoGBAMHWplAqhe4bfkGOEEpfs6VvEQxCqYMYVyR65K0rI1LiDZn6Ij8fdVtwMjGKFSZZTspmsqnbbuCE/VTyDzF4NpAxdm3cBtZACv1Lpu2Om+aTzhK2PI6WTDVTKAJBYegXaahBCqVbSxieR62IWtmOMjggTtAKWZ1P5LQcRwdkaB2rAoGAWnAPT318Kp7YcDx8whOzMGnxqtCc24jvk2iSUZgb2Dqv+3zCOTF6JUsV0Guxu5bISoZ8GdfSFKf5gBAo97sGFeuUBMsHYPkcLehM1FmLZk1Q+ljcx3P1A/ds3kWXLolTXCrlpvNMBSN5NwOKAyhdPK/qkvnUrfX8sJ5XK2H4J8ECgYAGIZ0HIiE0Y+g9eJnpUFelXvsCEUW9YNK4065SD/BBGedmPHRC3OLgbo8X5A9BNEf6vP7fwpIiRfKhcjqqzOuk6fueA/yvYD04v+Da2MzzoS8+hkcqF3T3pta4I4tORRdRfCUzD80zTSZlRc/h286Y2eTETd+By1onnFFe2X01mwKBgQDaxo4PBcLL2OyVT5DoXiIdTCJ8KNZL9+kV1aiBuOWxnRgkDjPngslzNa1bK+klGgJNYDbQqohKNn1HeFX3mYNfCUpuSnD2Yag53Dd/1DLO+NxzwvTu4D6DCUnMMMBVaF42ig31Bs0jI3JQZVqeeFzSET8fkoFopJf3G6UXlrIEAQ==',
    ],
];

$config_biz = [
    'out_trade_no' => '1',
    'total_amount' => '1',
    'subject' => 'test subject',
];

$pay = new Pay($config);
return $pay->dirver('alipay')->gateway('web')->pay($config_biz);
```

### 1、准备配置参数

```php
$config = [
    'alipay' => [
        'app_id' => '',             // 支付宝提供的 APP_ID
        'ali_public_key' => '',     // 支付宝公钥，1行填写
        'private_key' => '',        // 自己的私钥，1行填写
    ],
];
$config_biz = [
    'out_trade_no' => '12',                 // 订单号
    'total_amount' => '13',                 // 订单金额，单位：元
    'subject' => 'test subject',   // 订单商品标题
];
```

### 2、在代码中使用

```php
$pay = new Pay($config);
return $pay->dirver('alipay')->gateway('web')->pay($config_biz);
```

## 返回值

##### pay (支付接口)
返回 跳转到支付宝进行支付的 Html 代码。在正式项目中，直接 return 即可。

##### refund (退款接口)
true/false

##### close (关闭订单接口)
true/false

## 错误详情

使用非跳转接口（如， `refund` 接口,`close` 接口）时，如果有错误产生，会抛出 `GatewayException` 错误。

## 各个支付网关配置说明

由于支付网关不同，每家参数参差不齐，为了方便，我们抽象定义了两个参数：`$config`,`$config_biz`，分别为全局参数，业务参数。但是，所有配置参数均为官方标准参数，无任何差别。

「业务参数」为订单相关的参数，「全局参数」为除订单相关参数以外的全局性参数。

具体参数列表请查看每个支付网关的使用说明。

### 1、支付宝 - 电脑网站支付

#### 最小配置参数
```php
$config = [
    'alipay' => [
        'app_id' => '',             // 支付宝提供的 APP_ID
        'ali_public_key' => '',     // 支付宝公钥，1行填写
        'private_key' => '',        // 自己的私钥，1行填写
    ],
];
$config_biz = [
    'out_trade_no' => '12',                 // 订单号
    'total_amount' => '13',                 // 订单金额，单位：元
    'subject' => 'test subject',   // 订单商品标题
];
```

#### 所有配置参数

所有参数均为官方标准参数，无任何差别。[点击这里](https://docs.open.alipay.com/common/105901 '支付宝官方文档') 查看官方文档。

```php
$config = [
    'alipay' => [
        'app_id' => '',             // 支付宝提供的 APP_ID
        'ali_public_key' => '',     // 支付宝公钥，1行填写
        'private_key' => '',        // 自己的私钥，1行填写
        'return_url' => '',         // 同步通知 url，*强烈建议加上本参数*
        'notify_url' => '',         // 异步通知 url，*强烈建议加上本参数*
    ],
];
$config_biz = [
    'out_trade_no' => '',
    'total_amount' => '',                 
    'subject' => '',

    // 订单描述
    'body' => '',

    // 订单包含的商品列表信息，Json格式： {"show_url":"https://或http://打头的商品的展示地址"} ，在支付时，可点击商品名称跳转到该地址      
    'goods_detail' => '',

    // 该笔订单允许的最晚付款时间，逾期将关闭交易。取值范围：1m～15d。m-分钟，h-小时，d-天，1c-当天（1c-当天的情况下，无论交易何时创建，都在0点关闭）。 该参数数值不接受小数点， 如 1.5h，可转换为 90m。该参数在请求到支付宝时开始计时。
    'timeout_express' => '',                
    
    // 禁用渠道，用户不可用指定渠道支付当有多个渠道时用“,”分隔注：与enable_pay_channels互斥
    'disable_pay_channels' => '',

    // 可用渠道，用户只能在指定渠道范围内支付当有多个渠道时用“,”分隔注：与disable_pay_channels互斥
    'enable_pay_channels' => '',

    // 公用回传参数，如果请求时传递了该参数，则返回给商户时会回传该参数。支付宝只会在异步通知时将该参数原样返回。本参数必须进行UrlEncode之后才可以发送给支付宝
    'passback_params' => '',

    // 业务扩展参数，详见 [业务扩展参数说明](https://docs.open.alipay.com/#kzcs)
    'extend_params' => '',

    // 商品主类型：0—虚拟类商品，1—实物类商品（默认）注：虚拟类商品不支持使用花呗渠道
    'goods_type' => '',

    // 获取用户授权信息，可实现如免登功能。获取方法请查阅：用户信息授权
    'auth_token' => '',

    /**
     *  PC扫码支付的方式，支持前置模式和跳转模式。
     *
     *  前置模式是将二维码前置到商户的订单确认页的模式。需要商户在自己的页面中以iframe方式请求支付宝页面。具体分为以下几种：
     *  0：订单码-简约前置模式，对应iframe宽度不能小于600px，高度不能小于300px；
     *  1：订单码-前置模式，对应iframe宽度不能小于300px，高度不能小于600px；
     *  3：订单码-迷你前置模式，对应iframe宽度不能小于75px，高度不能小于75px；
     *  4：订单码-可定义宽度的嵌入式二维码，商户可根据需要设定二维码的大小。
     *
     *  跳转模式下，用户的扫码界面是由支付宝生成的，不在商户的域名下。
     *  2：订单码-跳转模式
     */
    'qr_pay_mode' => '',

    // 商户自定义二维码宽度 注：qr_pay_mode=4时该参数生效
    'qrcode_width' => ''
];
```

### 2、支付宝 - 手机网站支付

#### 最小配置参数
```php
$config = [
    'alipay' => [
        'app_id' => '',             // 支付宝提供的 APP_ID
        'ali_public_key' => '',     // 支付宝公钥，1行填写
        'private_key' => '',        // 自己的私钥，1行填写
    ],
];
$config_biz = [
    'out_trade_no' => '12',                 // 订单号
    'total_amount' => '13',                 // 订单金额，单位：元
    'subject' => 'test subject',   // 订单商品标题
];
```

#### 所有配置参数

该网关大部分参数和 「电脑支付」 相同，具体请参考 [官方文档](https://docs.open.alipay.com/203/107090/ '支付宝手机网站支付文档')

### 3、支付宝 - APP 支付

#### 最小配置参数
```php
$config = [
    'alipay' => [
        'app_id' => '',             // 支付宝提供的 APP_ID
        'notify_url' => '',         // 支付宝异步通知地址
        'ali_public_key' => '',     // 支付宝公钥，1行填写
        'private_key' => '',        // 自己的私钥，1行填写
    ],
];
$config_biz = [
    'out_trade_no' => '12',                 // 订单号
    'total_amount' => '13',                 // 订单金额，单位：元
    'subject' => 'test subject',   // 订单商品标题
];
```

#### 所有配置参数
该网关大部分参数和 「电脑支付」 相同，具体请参考 [官方文档](https://docs.open.alipay.com/204/105465/ '支付宝APP支付文档')

### 4、微信 - 公众号支付

#### 最小配置参数
TODO

#### 所有配置参数
所有参数均为官方标准参数，无任何差别。[点击这里](https://pay.weixin.qq.com/wiki/doc/api/jsapi.php?chapter=9_1 '微信支付官方文档') 查看官方文档。

### 5、微信 - 小程序支付
由于「小程序支付」和「公众号支付」都使用的是 JSAPI，所以，该网关大部分参数和 「公众号支付」 相同。

### 6、微信 - H5 支付
TODO

### 7、微信 - 扫码支付
TODO

### 8、微信 - 刷卡支付
TODO

## 代码贡献
由于测试及使用环境的限制，本项目中只开发了「支付宝」和「微信支付」的相关支付网关。

如果您有其它支付网关的需求，或者发现本项目中需要改进的代码，**_欢迎 Fork 并提交 PR！_**

## LICENSE
MIT