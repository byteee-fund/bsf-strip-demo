# bsf-stripe-alipay

此项目是基于 Stripe Connect API 专为 uniapp/uniappx 的 App 项目定制的 UTS 插件，支持支付宝支付集成。

## 平台支持
- ✅ Android
- ✅ iOS  
- ✅ Vue2/Vue3
- ✅ uniapp-x

## 功能特性
- 🔐 Stripe Connect 账户集成
- 💰 支付宝支付支持
- 🔧 简单易用的 API 接口
- 📱 跨平台兼容（Android & iOS）
- 🛡️ 完善的错误处理机制

## 使用说明

### 使用前的准备
1. 需要在 [Stripe 平台](https://stripe.com) 注册账户并获取相关参数：
   - Publishable Key（可发布密钥）
   - Connect Account ID（连接账户ID）
2. 配置支付宝支付相关参数
3. 确保应用已配置相应的 URL Scheme（iOS 必需）

### 引入插件
```javascript
import * as StripeConnect from "@/uni_modules/bsf-stripe-alipay";
```

## API 接口

### initPaymentConfiguration (初始化支付配置)
在使用支付功能前，需要先初始化 Stripe 配置。

```javascript
StripeConnect.initPaymentConfiguration(
  "pk_test_your_publishable_key", // Stripe 可发布密钥
  "acct_your_connect_account_id"   // Stripe Connect 账户ID
);
```

**参数说明：**
- `publicKey` (String): Stripe 可发布密钥
- `connectAccountId` (String): Stripe Connect 账户ID

### confirmAlipayPayment (确认支付宝支付)
发起支付宝支付流程。

```javascript
StripeConnect.confirmAlipayPayment({
  secretKey: "pi_xxx_secret_xxx",           // PaymentIntent 客户端密钥
  stripeAccountId: "acct_xxx",              // Stripe 账户ID（可选）
  returnUrl: "your-app://stripe-redirect",  // 支付完成后的返回URL（iOS必需）
  
  // 支付成功回调
  onSuccess: () => {
    console.log("支付成功！");
    uni.showToast({
      title: '支付成功',
      icon: 'success'
    });
  },
  
  // 支付失败回调
  onFailure: (error) => {
    console.error("支付失败：", error.errMsg);
    uni.showToast({
      title: error.errMsg,
      icon: 'none'
    });
  },
  
  // 支付取消回调
  onCancel: () => {
    console.log("用户取消支付");
    uni.showToast({
      title: '支付已取消',
      icon: 'none'
    });
  }
});
```

**参数说明：**
- `secretKey` (String): PaymentIntent 的客户端密钥
- `stripeAccountId` (String, 可选): Stripe 账户ID
- `returnUrl` (String, 可选): iOS 支付完成后的返回URL
- `onSuccess` (Function, 可选): 支付成功回调
- `onFailure` (Function, 可选): 支付失败回调，参数为错误对象
- `onCancel` (Function, 可选): 支付取消回调

## 完整使用示例

```javascript
<template>
  <view class="container">
    <button @click="initStripe" class="btn">初始化 Stripe</button>
    <button @click="payWithAlipay" class="btn" :disabled="!isInitialized">支付宝支付</button>
  </view>
</template>

<script>
import * as StripeConnect from "@/uni_modules/bsf-stripe-alipay";

export default {
  data() {
    return {
      isInitialized: false,
      paymentIntentSecret: ''
    }
  },
  
  methods: {
    // 初始化 Stripe 配置
    initStripe() {
      try {
        StripeConnect.initPaymentConfiguration(
          "pk_test_your_publishable_key",
          "acct_your_connect_account_id"
        );
        this.isInitialized = true;
        uni.showToast({
          title: 'Stripe 初始化成功',
          icon: 'success'
        });
      } catch (error) {
        console.error('初始化失败:', error);
        uni.showToast({
          title: '初始化失败',
          icon: 'none'
        });
      }
    },
    
    // 发起支付宝支付
    async payWithAlipay() {
      // 首先从服务器获取 PaymentIntent
      const paymentIntent = await this.createPaymentIntent();
      
      if (!paymentIntent) {
        uni.showToast({
          title: '获取支付信息失败',
          icon: 'none'
        });
        return;
      }
      
      // 发起支付
      StripeConnect.confirmAlipayPayment({
        secretKey: paymentIntent.client_secret,
        returnUrl: "your-app://stripe-redirect", // iOS 必需
        
        onSuccess: () => {
          console.log("支付成功！");
          uni.showModal({
            title: '支付结果',
            content: '支付成功！',
            showCancel: false
          });
        },
        
        onFailure: (error) => {
          console.error("支付失败：", error);
          uni.showModal({
            title: '支付失败',
            content: error.errMsg || '支付过程中出现错误',
            showCancel: false
          });
        },
        
        onCancel: () => {
          console.log("用户取消支付");
          uni.showToast({
            title: '支付已取消',
            icon: 'none'
          });
        }
      });
    },
    
    // 从服务器创建 PaymentIntent
    async createPaymentIntent() {
      try {
        const response = await uni.request({
          url: 'https://your-server.com/api/create-payment-intent',
          method: 'POST',
          data: {
            amount: 2000, // 金额（分）
            currency: 'cny',
            payment_method_types: ['alipay']
          }
        });
        return response.data;
      } catch (error) {
        console.error('创建 PaymentIntent 失败:', error);
        return null;
      }
    }
  }
}
</script>
```

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 9010001 | Stripe未初始化 |
| 9010002 | 支付失败 |
| 9010003 | 需要额外操作，请重试 |
| 9010004 | 支付处理中，请稍后查询结果 |
| 9010005 | 未知支付状态 |
| 9010006 | 支付失败 |
| 9010007 | 支付初始化失败 |

## 注意事项

### iOS 配置
1. **URL Scheme 配置**：在 `manifest.json` 中配置 URL Scheme
```json
{
  "app-plus": {
    "distribute": {
      "ios": {
        "urlschemewhitelist": ["your-app"]
      }
    }
  }
}
```

2. **Info.plist 配置**：需要在原生工程中配置 URL Types

### Android 配置
1. 确保已添加网络权限
2. 支付宝 SDK 已自动集成

### 服务器端集成
需要在服务器端集成 Stripe API 来创建 PaymentIntent，参考 [Stripe 官方文档](https://stripe.com/docs/payments/payment-intents)

## 开发文档
- [UTS 语法](https://uniapp.dcloud.net.cn/tutorial/syntax-uts.html)
- [UTS API插件](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html)
- [Stripe Connect 文档](https://stripe.com/docs/connect)
- [Stripe 支付宝集成](https://stripe.com/docs/payments/alipay)


