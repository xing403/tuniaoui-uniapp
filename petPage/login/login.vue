<template>
	<view class="template-login">

		<view class="login">
			<!-- 顶部背景图片-->
			<view class="login__bg login__bg--top">
				<image class="bg" src="https://tnuiimage.tnkjapp.com/login/1/login_top2.jpg" mode="widthFix"></image>
			</view>
			<view class="login__bg login__bg--top">
				<image class="rocket rocket-sussuspension" src="https://tnuiimage.tnkjapp.com/login/1/login_top3.png"
					mode="widthFix"></image>
			</view>

			<view class="login__wrapper">
				<!-- 登录/注册切换 -->
				<view
					class="login__mode tn-flex tn-flex-direction-row tn-flex-nowrap tn-flex-col-center tn-flex-row-center">
					<view class="login__mode__item tn-flex-1"
						:class="[{'login__mode__item--active': currentModeIndex === 0}]" @tap.stop="modeSwitch(0)">
						登录
					</view>
					<view class="login__mode__item tn-flex-1"
						:class="[{'login__mode__item--active': currentModeIndex === 1}]" @tap.stop="modeSwitch(1)">
						注册
					</view>

					<!-- 滑块-->
					<view class="login__mode__slider tn-cool-bg-color-15--reverse" :style="[modeSliderStyle]"></view>
				</view>

				<!-- 输入框内容-->
				<view class="login__info tn-flex tn-flex-direction-column tn-flex-col-center tn-flex-row-center">
					<!-- 登录 -->
					<tn-form :model="loginForm" ref="login" class="tn-width-full"
						:style="{display: currentModeIndex === 0 ? '':'none'}">
						<tn-form-item prop="username" leftIcon="my">
							<tn-input v-model="loginForm.username" type="text" placeholder="请输入用户名" />
						</tn-form-item>
						<tn-form-item prop="password" leftIcon="lock">
							<tn-input v-model="loginForm.password" type="password" placeholder="请输入密码" />
						</tn-form-item>
						<tn-button :shadow="true" @click="login" :loading="loading" width="100%"
							backgroundColor="#01BEFF" fontColor="#FFFFFF" margin="10rpx 0">登 录</tn-button>
					</tn-form>
					<!-- 注册 -->
					<tn-form :model="registerForm" ref="register" class="tn-width-full"
						:style="{display: currentModeIndex === 1 ? '':'none'}">
						<tn-form-item prop="username" leftIcon="my">
							<tn-input v-model="registerForm.username" type="text" placeholder="请输入用户名" />
						</tn-form-item>
						<tn-form-item prop="password" leftIcon="lock">
							<tn-input v-model="registerForm.password" type="password" placeholder="请输入密码" />
						</tn-form-item>
						<tn-form-item prop="phone" leftIcon="lock">
							<tn-input v-model="registerForm.phone" placeholder="请输入手机号" />
						</tn-form-item>
						<tn-button :shadow="true" @click="register" :loading="loading" width="100%"
							backgroundColor="#01BEFF" fontColor="#FFFFFF" margin="10rpx 0">注 册</tn-button>
					</tn-form>
					<view v-if="currentModeIndex === 0" class="login__info__item__tips">忘记密码?</view>
				</view>
			</view>


			<!-- 底部背景图片-->
			<view class="login__bg login__bg--bottom">
				<image src="https://tnuiimage.tnkjapp.com/login/1/login_bottom_bg.jpg" mode="widthFix"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loginForm: {
					username: "",
					password: "",
				},
				registerForm: {
					username: "",
					password: "",
					phone: "",
				},
				login_rules: {
					username: [{
						required: true,
						message: '请输入用户名',
						trigger: 'blur'
					}],
					password: [{
						required: true,
						message: '请输入密码',
						trigger: 'blur'
					}, {
						required: true,
						min: 6,
						max: 16,
						message: '长度在6~16位字符之间',
						trigger: 'change'
					}],
				},
				register_rules: {
					username: [{
						required: true,
						message: '请输入用户名',
						trigger: 'blur'
					}],
					password: [{
						required: true,
						message: '请输入密码',
						trigger: 'blur'
					}, {
						required: true,
						min: 6,
						max: 16,
						message: '长度在6~16位字符之间',
						trigger: 'change'
					}],
					phone: [{
						required: true,
						message: '请输入手机号',
						trigger: 'blur'
					}]
				},
				// 当前选中的模式
				currentModeIndex: 0,
				// 模式选中滑块
				modeSliderStyle: {
					left: 0
				},
				// 是否显示密码
				showPassword: false,
				// 倒计时提示文字
				tips: '获取验证码'
			}
		},
		watch: {
			currentModeIndex(value) {
				const sliderWidth = uni.upx2px(476 / 2)
				this.modeSliderStyle.left = `${sliderWidth * value}px`
			}
		},
		onReady() {
			this.$refs.login.setRules(this.login_rules)
			this.$refs.register.setRules(this.register_rules)
		},
		methods: {
			// 切换模式
			modeSwitch(index) {
				this.currentModeIndex = index
				this.showPassword = false
			},
			login() {
				let that = this
				that.$refs.login.validate(valid => {
					if (valid) {
						uni.request({
							url: 'http://admin.project.ilstudy.vip/index.php/pet/login',
							data: that.loginForm,
							header: {
								Accept: 'application/json',
								'Content-Type': 'application/json',
								'X-Requested-With': 'XMLHttpRequest'
							},
							method: 'POST',
							success: (res) => {
								console.log(res)
								if (res.data.data == 1) {
									uni.setStorageSync('user', JSON.stringify(that.loginForm))
									uni.navigateTo({
										url: '/pages/index/index'
									})
								} else {
									uni.showToast({
										icon: 'error',
										title: res.data.msg
									});
									setTimeout(function() {
										uni.hideToast()
									}, 2000)
								}
							},
							fail: (error) => {
								console.log(error)
							},
						})
					}
				})
			},
			register() {
				let that = this
				that.$refs.register.validate(valid => {
					if (valid) {
						uni.request({
							url: 'http://admin.project.ilstudy.vip/index.php/pet/register',
							data: that.registerForm,
							header: {
								Accept: 'application/json',
								'Content-Type': 'application/json',
								'X-Requested-With': 'XMLHttpRequest'
							},
							method: 'POST',
							success: (res) => {
								if (res.data.data == 1) {
									that.currentModeIndex = 0,
										that.registerForm = {
											username: "",
											password: "",
											phone: "",
										}
									uni.showToast({
										title: res.data.msg
									});
									setTimeout(function() {
										uni.hideToast()
									}, 2000)
								} else {
									uni.showToast({
										icon: 'error',
										title: res.data.msg
									});
									setTimeout(function() {
										uni.hideToast()
									}, 2000)
								}
							},
							fail: (error) => {
								console.log(error)
							}
						})
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	@import '@/static/css/templatePage/custom_nav_bar.scss';

	/* 悬浮 */
	.rocket-sussuspension {
		animation: suspension 3s ease-in-out infinite;
	}

	@keyframes suspension {

		0%,
		100% {
			transform: translate(0, 0);
		}

		50% {
			transform: translate(-0.8rem, 1rem);
		}
	}

	.login {
		position: relative;
		height: 100%;
		z-index: 1;

		/* 背景图片 start */
		&__bg {
			z-index: -1;
			position: fixed;

			&--top {
				top: 0;
				left: 0;
				right: 0;
				width: 100%;

				.bg {
					width: 750rpx;
					will-change: transform;
				}

				.rocket {
					margin: 50rpx 28%;
					width: 400rpx;
					will-change: transform;
				}
			}

			&--bottom {
				bottom: -10rpx;
				left: 0;
				right: 0;
				width: 100%;
				// height: 144px;
				margin-bottom: env(safe-area-inset-bottom);

				image {
					width: 750rpx;
					will-change: transform;
				}
			}
		}

		/* 背景图片 end */

		/* 内容 start */
		&__wrapper {
			margin-top: 403rpx;
			width: 100%;
		}

		/* 切换 start */
		&__mode {
			position: relative;
			margin: 0 auto;
			width: 476rpx;
			height: 77rpx;
			background-color: #FFFFFF;
			box-shadow: 0rpx 10rpx 50rpx 0rpx rgba(0, 3, 72, 0.1);
			border-radius: 39rpx;

			&__item {
				height: 77rpx;
				width: 100%;
				line-height: 77rpx;
				text-align: center;
				font-size: 31rpx;
				color: #908f8f;
				letter-spacing: 1em;
				text-indent: 1em;
				z-index: 2;
				transition: all 0.4s;

				&--active {
					font-weight: bold;
					color: #FFFFFF;
				}
			}

			&__slider {
				position: absolute;
				height: inherit;
				width: calc(476rpx / 2);
				border-radius: inherit;
				box-shadow: 0rpx 18rpx 72rpx 18rpx rgba(0, 195, 255, 0.1);
				z-index: 1;
				transition: all 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
			}
		}

		/* 切换 end */

		/* 登录注册信息 start */
		&__info {
			margin: 0 30rpx;
			margin-top: 105rpx;
			padding: 30rpx 51rpx;
			padding-bottom: 0;
			border-radius: 20rpx;
			background-color: #ffff;
			box-shadow: 0rpx 10rpx 50rpx 0rpx rgba(0, 3, 72, 0.1);

			&__item {

				&__input {
					margin-top: 59rpx;
					width: 100%;
					height: 77rpx;
					border: 1rpx solid #E6E6E6;
					border-radius: 39rpx;

					&__left-icon {
						width: 10%;
						font-size: 44rpx;
						margin-left: 20rpx;
						color: #AAAAAA;
					}

					&__content {
						width: 80%;
						padding-left: 10rpx;

						&--verify-code {
							width: 56%;
						}

						input {
							font-size: 24rpx;
							// letter-spacing: 0.1em;
						}
					}

					&__right-icon {
						width: 10%;
						font-size: 44rpx;
						margin-right: 20rpx;
						color: #AAAAAA;
					}

					&__right-verify-code {
						width: 34%;
						margin-right: 20rpx;
					}
				}

				&__button {
					margin-top: 75rpx;
					margin-bottom: 39rpx;
					width: 100%;
					height: 77rpx;
					text-align: center;
					font-size: 31rpx;
					font-weight: bold;
					line-height: 77rpx;
					letter-spacing: 1em;
					text-indent: 1em;
					border-radius: 39rpx;
					box-shadow: 1rpx 10rpx 24rpx 0rpx rgba(60, 129, 254, 0.35);
				}

				&__tips {
					margin: 30rpx 0;
					color: #AAAAAA;
				}
			}
		}

		/* 登录注册信息 end */

		/* 登录方式切换 start */
		&__way {
			margin: 0 auto;
			margin-top: 110rpx;

			&__item {
				&--icon {
					width: 77rpx;
					height: 77rpx;
					font-size: 50rpx;
					border-radius: 100rpx;
					margin-bottom: 18rpx;
					position: relative;
					z-index: 1;

					&::after {
						content: " ";
						position: absolute;
						z-index: -1;
						width: 100%;
						height: 100%;
						left: 0;
						bottom: 0;
						border-radius: inherit;
						opacity: 1;
						transform: scale(1, 1);
						background-size: 100% 100%;
						background-image: url(https://tnuiimage.tnkjapp.com/cool_bg_image/icon_bg5.png);
					}
				}
			}
		}

		/* 登录方式切换 end */
		/* 内容 end */

	}

	/deep/.input-placeholder {
		font-size: 24rpx;
		color: #E6E6E6;
	}
</style>