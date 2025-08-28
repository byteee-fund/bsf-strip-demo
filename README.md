# BSF Stripe Connect 演示项目

这是一个用于演示 Stripe Connect 支付插件用法的 uni-app 项目，展示了如何在移动应用中集成 Stripe 支付功能。

## 📋 项目概述

本项目主要用于演示两个核心 Stripe 支付插件的使用方法：

1. **bsf-stripe-alipay** - 支付宝支付插件
2. **bsf-stripe-card** - 银行卡支付插件

通过这个演示项目，开发者可以快速了解如何在 uni-app 中集成 Stripe Connect API，实现移动端的支付功能。

## 🔌 插件介绍

### 1. bsf-stripe-alipay (支付宝支付插件)

**功能特性：**
- ✅ 基于 Stripe Connect API 的支付宝支付集成
- ✅ 支持 Android 和 iOS 平台
- ✅ 完善的支付状态回调处理
- ✅ 简单易用的 API 接口

**主要用途：**
- 为海外应用提供支付宝支付方式
- 通过 Stripe Connect 平台管理多商户支付
- 支持跨境支付场景

### 2. bsf-stripe-card (银行卡支付插件)

**功能特性：**
- ✅ 原生银行卡输入组件
- ✅ 支持多种卡片类型（Visa、MasterCard、American Express等）
- ✅ 跨平台兼容（Android & iOS）
- ✅ 安全的卡片信息处理

**主要用途：**
- 提供标准的信用卡/借记卡支付方式
- 内置安全的卡片信息输入界面
- 符合 PCI DSS 合规要求

## 🚀 快速开始

### 环境要求

- HBuilderX 3.6.8+
- uni-app 框架
- 支持 Vue2/Vue3
- Android/iOS 开发环境

### 安装步骤

1. **克隆项目**
   ```bash
   git clone [项目地址]
   cd bsf-stripe-demo
   ```

2. **配置 Stripe 参数**
   
   在使用插件前，需要先在 [Stripe 平台](https://stripe.com) 获取以下参数：
   - Publishable Key（可发布密钥）
   - Connect Account ID（连接账户ID）

3. **导入项目**
   
   使用 HBuilderX 导入项目，确保插件正确安装。

4. **运行项目**
   
   选择目标平台运行项目，测试支付功能。

## 💡 使用示例

### 支付宝支付

```javascript
import * as StripeConnect from "@/uni_modules/bsf-stripe-alipay";

// 初始化配置
StripeConnect.initPaymentConfiguration(
  "pk_test_your_publishable_key",
  "acct_your_connect_account_id"
);

// 发起支付
StripeConnect.confirmAlipayPayment({
  secretKey: "pi_xxx_secret_xxx",
  returnUrl: "your-app://stripe-redirect",
  onSuccess: () => {
    console.log("支付成功！");
  },
  onFailure: (error) => {
    console.error("支付失败：", error.errMsg);
  }
});
```

### 银行卡支付

```vue
<template>
  <view>
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

<script>
import * as StripeCardWidget from "@/uni_modules/bsf-stripe-card";

export default {
  methods: {
    payWithCard() {
      StripeCardWidget.confirmCardPayment({
        secretKey: "pi_xxx_secret_xxx",
        cardInputWidget: this.$refs.cardInput,
        onSuccess: () => {
          console.log("支付成功！");
        }
      });
    }
  }
}
</script>
```

## 📱 平台支持

| 平台 | bsf-stripe-alipay | bsf-stripe-card |
|------|-------------------|-----------------|
| Android | ✅ | ✅ |
| iOS | ✅ | ✅ |
| Vue2 | ✅ | ✅ |
| Vue3 | ✅ | ✅ |
| uniapp-x | ✅ | ✅ |
| H5 | ❌ | ❌ |
| 小程序 | ❌ | ❌ |

*注：插件专为 App 平台设计，不支持 H5 和小程序平台*

## 🔧 配置说明

### iOS 配置

在 `manifest.json` 中添加 URL Scheme 配置：

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

### Android 权限

项目已包含必要的 Android 权限配置，包括网络访问等基础权限。

## 📁 项目结构

```
bsf-stripe-demo/
├── uni_modules/                 # 插件目录
│   ├── bsf-stripe-alipay/      # 支付宝支付插件
│   │   ├── utssdk/             # UTS SDK 实现
│   │   ├── package.json        # 插件配置
│   │   └── readme.md           # 插件文档
│   └── bsf-stripe-card/        # 银行卡支付插件
│       ├── utssdk/             # UTS SDK 实现
│       ├── package.json        # 插件配置
│       └── readme.md           # 插件文档
├── pages/                      # 页面目录
│   └── index/                  # 主页面
│       └── index.nvue          # 演示页面
├── App.vue                     # 应用入口
├── main.js                     # 主入口文件
├── manifest.json               # 应用配置
├── pages.json                  # 页面配置
└── README.md                   # 项目说明
```

## 🔒 安全注意事项

1. **密钥安全**：
   - 不要在客户端硬编码 Secret Key
   - Publishable Key 可以在客户端使用
   - 敏感操作应在服务端完成

2. **支付验证**：
   - 支付成功后应在服务端验证支付状态
   - 不要仅依赖客户端回调确认支付

3. **数据传输**：
   - 确保所有支付相关请求使用 HTTPS
   - 银行卡信息由 Stripe SDK 安全处理

## 📞 技术支持

如果在使用过程中遇到问题，可以：

1. 查看插件目录下的详细文档
2. 参考 [Stripe 官方文档](https://stripe.com/docs)
3. 联系插件作者获取技术支持

## 📄 许可证

本演示项目仅用于学习和演示目的。具体插件的使用许可请参考各插件的许可协议。

---

**注意**：本项目为演示项目，生产环境使用前请确保：
- 使用正式的 Stripe 账户和密钥
- 完善错误处理和用户体验
- 进行充分的安全测试
- 遵循相关法律法规要求