# bsf-stripe-card

此项目是基于 Stripe Connect API 专为 uniapp/uniappx 的 App 项目定制的 UTS 插件，支持银行卡支付集成和银行卡输入组件。

## 平台支持
- ✅ Android
- ✅ iOS  
- ✅ Vue2/Vue3
- ✅ uniapp-x

## 功能特性
- 🔐 Stripe Connect 账户集成
- 💳 银行卡支付支持
- 🎨 原生银行卡输入组件
- 🔧 简单易用的 API 接口
- 📱 跨平台兼容（Android & iOS）
- 🛡️ 完善的错误处理机制
- ✨ 支持多种卡片类型（Visa、MasterCard、American Express等）

## 使用说明

### 使用前的准备
1. 需要在 [Stripe 平台](https://stripe.com) 注册账户并获取相关参数：
   - Publishable Key（可发布密钥）
   - Connect Account ID（连接账户ID）
2. 配置银行卡支付相关参数
3. 确保应用已配置相应的 URL Scheme（iOS 必需）

### 引入插件
```javascript
import * as StripeCardWidget from "@/uni_modules/bsf-stripe-card";
```

### 引入组件
```vue
<template>
  <view class="container">
    <!-- Android 银行卡输入组件 -->
    <bsf-stripe-card 
      v-if="isAndroid"
      ref="cardInput"
      @ready="onCardInputReady"
      style="height: 200px;"
    />
    
    <!-- iOS 银行卡输入组件 -->
    <bsf-stripe-card-ios 
      v-if="isIOS"
      ref="cardInputIOS"
      @ready="onCardInputReady"
      style="height: 200px;"
    />
  </view>
</template>
```

## API 接口

### initPaymentConfiguration (初始化支付配置)
在使用支付功能前，需要先初始化 Stripe 配置。

```javascript
StripeCardWidget.initPaymentConfiguration(
  "pk_test_your_publishable_key", // Stripe 可发布密钥
  "acct_your_connect_account_id"   // Stripe Connect 账户ID
);
```

**参数说明：**
- `publicKey` (String): Stripe 可发布密钥
- `connectAccountId` (String): Stripe Connect 账户ID

### confirmCardPayment (确认银行卡支付)
发起银行卡支付流程。

```javascript
StripeCardWidget.confirmCardPayment({
  secretKey: "pi_xxx_secret_xxx",           // PaymentIntent 客户端密钥
  cardInputWidget: this.$refs.cardInput,    // 银行卡输入组件实例
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
- `cardInputWidget` (Object): 银行卡输入组件的引用
- `stripeAccountId` (String, 可选): Stripe 账户ID
- `returnUrl` (String, 可选): iOS 支付完成后的返回URL
- `onSuccess` (Function, 可选): 支付成功回调
- `onFailure` (Function, 可选): 支付失败回调，参数为错误对象
- `onCancel` (Function, 可选): 支付取消回调

## 组件使用说明

### Android 组件 (bsf-stripe-card)
基于 Stripe Android SDK 的 `CardMultilineWidget` 实现。

**组件属性：**
- `style`: 组件样式，建议设置固定高度（如 200px）

**组件事件：**
- `@ready`: 组件准备就绪时触发

### iOS 组件 (bsf-stripe-card-ios)
基于 Stripe iOS SDK 的 `STPCardFormView` 实现。

**组件属性：**
- `style`: 组件样式，建议设置固定高度（如 200px）

**组件事件：**
- `@ready`: 组件准备就绪时触发

## 完整使用示例

```vue
<template>
  <view class="container">
    <view class="header">
      <text class="title">银行卡支付</text>
    </view>
    
    <view class="payment-section">
      <button @click="initStripe" class="btn" :disabled="isInitialized">
        {{ isInitialized ? '已初始化' : '初始化 Stripe' }}
      </button>
      
      <!-- 银行卡输入组件 -->
      <view class="card-input-container">
        <text class="label">请输入银行卡信息：</text>
        
        <!-- Android 组件 -->
        <bsf-stripe-card 
          v-if="isAndroid"
          ref="cardInput"
          @ready="onCardInputReady"
          class="card-input"
        />
        
        <!-- iOS 组件 -->
        <bsf-stripe-card-ios 
          v-if="isIOS"
          ref="cardInputIOS"
          @ready="onCardInputReady"
          class="card-input"
        />
      </view>
      
      <button 
        @click="payWithCard" 
        class="btn pay-btn" 
        :disabled="!isInitialized || !cardInputReady"
      >
        确认支付
      </button>
    </view>
  </view>
</template>

<script>
import * as StripeCardWidget from "@/uni_modules/bsf-stripe-card";

export default {
  data() {
    return {
      isInitialized: false,
      cardInputReady: false,
      isAndroid: false,
      isIOS: false
    }
  },
  
  mounted() {
    // 检测平台
    // #ifdef APP-ANDROID
    this.isAndroid = true;
    // #endif
    
    // #ifdef APP-IOS
    this.isIOS = true;
    // #endif
  },
  
  methods: {
    // 初始化 Stripe 配置
    initStripe() {
      try {
        StripeCardWidget.initPaymentConfiguration(
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
    
    // 银行卡输入组件准备就绪
    onCardInputReady() {
      this.cardInputReady = true;
      console.log('银行卡输入组件已准备就绪');
    },
    
    // 发起银行卡支付
    async payWithCard() {
      // 首先从服务器获取 PaymentIntent
      const paymentIntent = await this.createPaymentIntent();
      
      if (!paymentIntent) {
        uni.showToast({
          title: '获取支付信息失败',
          icon: 'none'
        });
        return;
      }
      
      // 获取银行卡输入组件引用
      const cardInputWidget = this.isAndroid ? 
        this.$refs.cardInput : 
        this.$refs.cardInputIOS;
      
      if (!cardInputWidget) {
        uni.showToast({
          title: '银行卡组件未准备就绪',
          icon: 'none'
        });
        return;
      }
      
      // 发起支付
      StripeCardWidget.confirmCardPayment({
        secretKey: paymentIntent.client_secret,
        cardInputWidget: cardInputWidget,
        returnUrl: "your-app://stripe-redirect", // iOS 必需
        
        onSuccess: () => {
          console.log("支付成功！");
          uni.showModal({
            title: '支付结果',
            content: '银行卡支付成功！',
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
            currency: 'usd', // 银行卡支付通常使用美元
            payment_method_types: ['card']
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

<style>
.container {
  padding: 20px;
}

.header {
  text-align: center;
  margin-bottom: 30px;
}

.title {
  font-size: 24px;
  font-weight: bold;
  color: #333;
}

.payment-section {
  max-width: 400px;
  margin: 0 auto;
}

.btn {
  width: 100%;
  padding: 15px;
  background-color: #007AFF;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  margin-bottom: 20px;
}

.btn:disabled {
  background-color: #ccc;
}

.card-input-container {
  margin: 20px 0;
}

.label {
  display: block;
  margin-bottom: 10px;
  font-size: 16px;
  color: #333;
}

.card-input {
  height: 200px;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-bottom: 20px;
}

.pay-btn {
  background-color: #28a745;
}

.pay-btn:disabled {
  background-color: #ccc;
}
</style>
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
| 9010008 | 未初始化银行卡输入组件 |

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
2. Stripe Android SDK 已自动集成

### 银行卡安全
1. **PCI DSS 合规**：此插件使用 Stripe 的原生组件，符合 PCI DSS 安全标准
2. **卡片信息不存储**：所有敏感的银行卡信息都由 Stripe 处理，不会在您的应用中存储
3. **安全传输**：所有数据传输都使用 HTTPS 加密

### 支持的卡片类型
- Visa
- MasterCard
- American Express
- Discover
- Diners Club
- JCB
- UnionPay（部分地区）

### 服务器端集成
需要在服务器端集成 Stripe API 来创建 PaymentIntent，参考 [Stripe 官方文档](https://stripe.com/docs/payments/payment-intents)

## 开发文档
- [UTS 语法](https://uniapp.dcloud.net.cn/tutorial/syntax-uts.html)
- [UTS API插件](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html)
- [UTS 组件](https://uniapp.dcloud.net.cn/plugin/uts-component.html)
- [Stripe Connect 文档](https://stripe.com/docs/connect)
- [Stripe 银行卡支付](https://stripe.com/docs/payments/cards)
- [Stripe Android SDK](https://stripe.dev/stripe-android/)
- [Stripe iOS SDK](https://stripe.dev/stripe-ios/)

