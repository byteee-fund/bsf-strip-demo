<template>
  <view>
  </view>
</template>

<script lang="uts">
  /**
   * 引用 Android 系统库
   * [可选实现，按需引入]
   */
  import CardMultilineWidget from 'com.stripe.android.view.CardMultilineWidget';
 
  import { initPaymentConfiguration, confirmCardPayment } from './index';
  
  import {ConfirmCardPaymentOptions} from '../interface.uts'
  /**
   * 引入三方库
   * [可选实现，按需引入]
   *
   * 在 Android 平台引入三方库有以下两种方式：
   * 1、[推荐] 通过 仓储 方式引入，将 三方库的依赖信息 配置到 config.json 文件下的 dependencies 字段下。详细配置方式[详见](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html#dependencies)
   * 2、直接引入，将 三方库的aar或jar文件 放到libs目录下。更多信息[详见](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html#android%E5%B9%B3%E5%8F%B0%E5%8E%9F%E7%94%9F%E9%85%8D%E7%BD%AE)
   *
   * 在通过上述任意方式依赖三方库后，使用时需要在文件中 import
   * import { LottieAnimationView } from 'com.airbnb.lottie.LottieAnimationView'
   */

  /**
   * UTSAndroid 为平台内置对象，不需要 import 可直接调用其API，[详见](https://uniapp.dcloud.net.cn/uts/utsandroid.html#utsandroid)
   */

  //原生提供以下属性或方法的实现
  export default {
    /**
     * 组件名称，也就是开发者使用的标签
     */
    name: "bsf-strip-card-input-widget",
    /**
     * 组件涉及的事件声明，只有声明过的事件，才能被正常发送
     */
    emits: ['onSuccess', 'onFailure'],
    /**
     * 属性声明，组件的使用者会传递这些属性值到组件
     */
    props: {
     
    },
    /**
     * 组件内部变量声明
     */
    data() {
      return {}
    },
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
       *
       * uni-app中调用示例：
       * this.$refs["组件ref"].doSomething("uts-button");
       *
       * uni-app x中调用示例：
       * 1、引入对应Element
       * import { UtsButtonElement(组件名称以upper camel case方式命名 + Element) } from 'uts.sdk.modules.utsComponent(组件目录名称以lower camel case方式命名)';
       * 2、(this.$refs["组件ref"] as UtsButtonElement).doSomething("uts-button");
       * 或 (uni.getElementById("组件id") as UtsButtonElement).doSomething("uts-button");
       */
      doCardPay(publicKey : string, cardClientSecret : string, connectAccountId: string, returnUrl: string) {
		  initPaymentConfiguration(publicKey, connectAccountId);
		  
		  let confirmCardPaymentOptions:ConfirmCardPaymentOptions = {
			  secretKey: cardClientSecret,
			  cardInputWidget: this.$el!,
			  stripeAccountId: connectAccountId,
			  onSuccess: () => {
				console.log('卡支付成功');
				// uni.showToast({ title: '支付成功', icon: 'success' });
				
				this.$emit('onSuccess');
			  },
			  onFailure: (e) => {
				console.log('卡支付失败', e);
				// uni.showToast({ title: `支付失败: ${e.errMsg}`, icon: 'error' });
				 this.$emit('onFailure', { errMsg: e.errMsg, errCode: e.errCode });
			  }
			};
		  
			confirmCardPayment(confirmCardPaymentOptions)
      },
      /**
       * 内部使用的组件方法
       */
      privateMethod() {

      }
    },
    /**
     * [可选实现] 组件被创建，组件第一个生命周期，
     * 在内存中被占用的时候被调用，开发者可以在这里执行一些需要提前执行的初始化逻辑
     */
    created() {

    },
    /**
     * [可选实现] 对应平台的view载体即将被创建，对应前端beforeMount
     */
    NVBeforeLoad() {

    },
    /**
     * [必须实现] 创建原生View，必须定义返回值类型
     * 开发者需要重点实现这个函数，声明原生组件被创建出来的过程，以及最终生成的原生组件类型
     * （Android需要明确知道View类型，需特殊校验）
     */
    NVLoad() : CardMultilineWidget {
		// initPaymentConfiguration("pk_live_51PQcVZKUvNxMlxGS9dYIrtKOtbaW81hGRnr7UhfXDf8nbYbeGwSxtW1B4WZSOMjJyrRIfgCY89RDM1VofKT2CEdW0088wsq03", "acct_1QlW8L4IdDE4NQ6s");
		  console.log("Creating CardInputWidget...");
      let cardInputWidget = new CardMultilineWidget($androidContext!);
	  // cardInputWidget.setCardHint("卡号的提示词1");
      return cardInputWidget;
    },
    /**
     * [可选实现] 原生View已创建
     */
    NVLoaded() {
		const cardInput = this.$el as CardMultilineWidget
		cardInput!.setFocusable(true)
		cardInput!.setFocusableInTouchMode(true)
		cardInput!.requestFocus()
		
		cardInput.setCardHint("卡号的提示词2");
		cardInput.setCvcPlaceholderText("安全码 (3–4 位)")
		// cardWidget.setCvcLabel("CVV")
	
		const imm = $androidContext!.getSystemService(android.content.Context.INPUT_METHOD_SERVICE) as android.view.inputmethod.InputMethodManager
		imm.showSoftInput(cardInput, android.view.inputmethod.InputMethodManager.SHOW_IMPLICIT)
			
    },
    /**
     * [可选实现] 原生View布局完成
     */
    NVLayouted() {

    },
    /**
     * [可选实现] 原生View将释放
     */
    NVBeforeUnload() {

    },
    /**
     * [可选实现] 原生View已释放，这里可以做释放View之后的操作
     */
    NVUnloaded() {

    },
    /**
     * [可选实现] 组件销毁
     */
    unmounted() {

    },
    /**
     * [可选实现] 自定组件布局尺寸，用于告诉排版系统，组件自身需要的宽高
     * 一般情况下，组件的宽高应该是由终端系统的排版引擎决定，组件开发者不需要实现此函数
     * 但是部分场景下，组件开发者需要自己维护宽高，则需要开发者重写此函数
     */
    // NVMeasure(size : UTSSize) : UTSSize {
    //   size.width = 300.0.toFloat();
    //   size.height = 200.0.toFloat();
    //   return size;
    // }
  }

 
</script>

<style>

</style>