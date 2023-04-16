<template>
	<button class="wechat-logo" @click="getWeChatCode">微信授权登录</button>
</template>

<script>
export default {
	onLoad() {
		const hasWechatLogin = uni.getStorageSync('wechat_login_tag') || null;
		if(hasWechatLogin) {
			this.checkWeChatCode();
		}
	},
	methods: {
		// 重定向回来本页面检查有没有code
		checkWeChatCode() {
			let code = this.getUrlCode('code');
			console.log(code);
			uni.showToast({
			  title:code,
			  mask: false,
			  duration: 1000
			});
			if(code) {
				this.handleToLogin(code)
			}
		},
		// 正则匹配请求地址中的参数函数
		getUrlCode(name) {
			return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) ||[, ''])[1].replace(/\+/g, '%20')) || null
		},
		// 看地址中有没有code参数,如果没有code参数的话则请求微信官方的接口并获取包含code的回调链接
		getWeChatCode() {
			//用于退出登录回来不会再调一次授权登录
			uni.setStorageSync('wechat_login_tag', 'true');
						
			const appID = 'wx45cb542b8cb5d6d8';  //公众号appID
			const callBack = 'http://e5qxuj.natappfree.cc'; //回调地址 就是你的完整地址登录页
						
			//通过微信官方接口获取code之后，会重新刷新设置的回调地址【redirect_uri】
			window.location.href = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid=' +
							appID + '&redirect_uri=' + encodeURIComponent(callBack) +
							'&response_type=code&scope=snsapi_userinfo&state=1#wechat_redirect'
		},
		// 把后端需要的code以及其他信息调用接口传过去
		
		//比如调用接口loginIn
		handleToLogin(code) {
			loginIn({
				code,
			}).then(res => {
				console.log('登录成功')
				uni.redirectTo({
					url: '/pages/index/index'
				})
			})
		}
	}		
}
</script>
