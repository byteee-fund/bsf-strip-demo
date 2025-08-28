<template>
  <view class="defaultStyles">
  </view>
</template>
<script lang="uts">
  /**

  /**
   * 引入三方库
   * [可选实现，按需引入]
   *
   * 在 iOS 平台引入三方库有以下两种方式：
   * 1、通过引入三方库framework 或者.a 等方式，需要将 .framework 放到 ./Frameworks 目录下，将.a 放到 ./Libs 目录下。更多信息[详见](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html#ios-平台原生配置)
   * 2、通过 cocoaPods 方式引入，将要引入的 pod 信息配置到 config.json 文件下的 dependencies-pods 字段下。详细配置方式[详见](https://uniapp.dcloud.net.cn/plugin/uts-ios-cocoapods.html)
   *
   * 在通过上述任意方式依赖三方库后，使用时需要在文件中 import:
   * 示例：import { LottieAnimationView, LottieAnimation, LottieLoopMode	} from 'Lottie'
   */

  /**
   * UTSiOS、UTSComponent 为平台内置对象，不需要 import 可直接调用其API，[详见](https://uniapp.dcloud.net.cn/uts/utsios.html)
   */
  import { STPCardFormView } from "Stripe"
  import {confirmCardPayment, initPaymentConfiguration} from "./index.uts"
  import {ConfirmCardPaymentOptions,MyApiFail} from "../interface.uts"
  import { Bundle } from 'Foundation';

  //原生提供以下属性或方法的实现
  export default {
    data() {
      return {
      };
    },
    /**
     * 组件名称，也就是开发者使用的标签
     */
    name: "bsf-strip-card-input-widget",
    /**
     * 组件涉及的事件声明，只有声明过的事件，才能被正常发送
     */
    emits: ['onSuccess', 'onFailure', 'onCancel'],
    /**
     * 属性声明，组件的使用者会传递这些属性值到组件
     */
    props: {
      /**
       * 字符串类型 属性：buttontext  需要设置默认值
       */
      "buttontext": {
        type: String,
        default: "点击触发"
      }
    },
    /**
     * 组件内部变量声明
     */

    /**
     * 属性变化监听器实现
     */
    watch: {
      
    },
    /**
     * 规则：如果没有配置expose，则methods中的方法均对外暴露，如果配置了expose，则以expose的配置为准向外暴露
     * ['publicMethod'] 含义为：只有 `publicMethod` 在实例上可用
     */
    expose: ['doCardPay'],
    methods: {
      /**
       * 对外公开的组件方法
       * 在uni-app中调用组件方法，可以通过指定ref的方式，例如指定uts-button 标签的ref 为 ’button‘， 调用时使用：this.$refs["button"].doSomething('message');
       */
     doCardPay(publicKey : string, cardClientSecret : string, connectAccountId: string, returnUrl: string) {
     		  initPaymentConfiguration(publicKey, connectAccountId);
     		  
     		  let confirmCardPaymentOptions:ConfirmCardPaymentOptions = {
     			  secretKey: cardClientSecret,
     			  cardInputWidget: this.$el,
     			  stripeAccountId: connectAccountId,
				  returnUrl:returnUrl,
     			  onSuccess: () => {
     				console.log('卡支付成功');
     				// uni.showToast({ title: '支付成功', icon: 'success' });
     				
     				this.$emit('onSuccess');
     			  },
     			  onFailure: (e:MyApiFail) => {
     				console.log('卡支付失败', e);
     				// uni.showToast({ title: `支付失败: ${e.errMsg}`, icon: 'error' });
					const map: Map<string,any> = new Map();
					map.set("errMsg", e.errMsg);
					map.set("errCode", e.errCode);
     				this.$emit('onFailure', map);
     			  },
				  onCancel: () => {
						console.log('卡支付取消');
						// uni.showToast({ title: `支付失败: ${e.errMsg}`, icon: 'error' });
						 this.$emit('onCancel');
				  },
     			};
     		  
     			confirmCardPayment(confirmCardPaymentOptions)
     },


      /**
       * 内部使用的组件方法
       */
    },


    /**
     * 组件被创建，组件第一个生命周期，
     * 在内存中被占用的时候被调用，开发者可以在这里执行一些需要提前执行的初始化逻辑
     * [可选实现]
     */
    created() {
		console.log("swizzle");
		UIImage.swizzleImageNamed();
    },
    /**
     * 对应平台的view载体即将被创建，对应前端beforeMount
     * [可选实现]
     */
    NVBeforeLoad() {

    },
    /**
     * 创建原生View，必须定义返回值类型
     * 开发者需要重点实现这个函数，声明原生组件被创建出来的过程，以及最终生成的原生组件类型
     * [必须实现]
     */
    NVLoad() : STPCardFormView {
	
      let cardFormView = new STPCardFormView();
	  return cardFormView;
    },

    /**
     * 原生View已创建
     * [可选实现]
     */
    NVLoaded() {
    
    },
    /**
     * 原生View布局完成
     * [可选实现]
     */
    NVLayouted() {

    },
    /**
     * 原生View将释放
     * [可选实现]
     */
    NVBeforeUnload() { },
    /**
     * 原生View已释放，这里可以做释放View之后的操作
     * [可选实现]
     */
    NVUnloaded() {

    },
    /**
     * 组件销毁
     * [可选实现]
     */
    unmounted() { }

    /**
     * 更多组件开发的信息详见：https://uniapp.dcloud.net.cn/plugin/uts-component.html
     */
  }


   
</script>

<style>

</style>