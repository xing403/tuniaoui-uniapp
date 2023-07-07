<template>
	<view class="tn-safe-area-inset-bottom">

		<!-- 顶部自定义导航 -->
		<tn-nav-bar fixed>用户注册</tn-nav-bar>

		<!-- 页面内容 -->
		<view class="tn-margin-left-sm tn-margin-right-sm" :style="{paddingTop: vuex_custom_bar_height + 'px'}">
			<tn-form :model="model" ref="form" labelPosition="left" :labelWidth="140" labelAlign="right">
				<tn-form-item label="账号" prop="username" leftIcon="identity" :required="true" labelPosition="left"
					labelAlign="right">
					<tn-input v-model="model.username" type="text" placeholder="请输入用户名"></tn-input>
				</tn-form-item>
				<tn-form-item label="密码" prop="password" leftIcon="password" :required="true" labelPosition="left"
					labelAlign="right">
					<tn-input v-model="model.password" type="password" placeholder="请输入密码"></tn-input>
				</tn-form-item>
				
				<view class="tn-flex tn-flex-direction-row-reverse tn-margin-bottom-sm tn-margin-top-sm tn-margin-right-sm">
					<view class=""></view>
					<view class="">
						<navigator url="/petPage/login/login"><text style="color: #01BEFF;">去登录</text></navigator>
					</view>
				</view>
				
				
				<tn-button :shadow="true" @click="register" :loading="loading" width="100%" height="100rpx"
					backgroundColor="#01BEFF" fontColor="#FFFFFF" margin="10rpx 0">注册</tn-button>
			</tn-form>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				model: {
					username: "",
					password: ""
				},
				loading: false,
				rules: {
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

				}
			}
		},
		onReady() {
			this.$refs.form.setRules(this.rules)
		},
		methods: {
			register() {
				let that = this
				that.$refs.form.validate(valid => {
					uni.request({
						url: 'http://admin.project.ilstudy.vip/index.php/pet/register',
						data: that.model,
						header: {
							Accept: 'application/json',
							'Content-Type': 'application/json',
							'X-Requested-With': 'XMLHttpRequest'
						},
						method: 'POST',
						success: (res) => {
							console.log(res)
							if (res.data.data == 1) {
								uni.navigateTo({
									url: '/petPage/login/login'
								})
							} else {
								uni.showToast({
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
				})
			}
		}
	}
</script>

<style>

</style>
