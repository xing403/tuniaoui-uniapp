<template>
	<view class="components tn-safe-area-inset-bottom">

		<!-- 顶部自定义导航 -->
		<tn-nav-bar fixed :isBack="false">宠物日志</tn-nav-bar>
		<!-- 页面内容 -->
		<view :style="{paddingTop: vuex_custom_bar_height + 'px'}">
			<view class="top-backgroup">
				<image src='https://tnuiimage.tnkjapp.com/my/my-bg3.png' mode='widthFix' class='backgroud-image'>
				</image>
			</view>
			<view class="about__wrap">
				<view class="tn-flex tn-flex-row-between tn-flex-col-center tn-margin-bottom">
					<view class="justify-content-item">
						<view class="tn-flex tn-flex-col-center tn-flex-row-left">
							<!-- 图标logo -->
							<view class="logo-pic tn-shadow">
								<view class="logo-image">
									<view class="tn-shadow-blur"
										style="background-image:url('https://tnuiimage.tnkjapp.com/blogger/avatar_2.jpeg');width: 120rpx;height: 120rpx;background-size: cover;">
									</view>
								</view>
							</view>
							<view class="tn-padding-right tn-color-white">
								<view class="tn-padding-right tn-padding-left-sm tn-text-xxl">
									{{ user.username }}
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			<tn-list-view unlined="bottom">
				<navigator v-for="(item,index) in logs" :key="index" open-type="navigate" :url="item.url">
					<tn-list-cell class="tn-margin-bottom-sm" :hover="true" :unlined="true" :radius="true"
						:fontSize="30">
						<view class="tn-flex">
							<view class="tn-flex-basic-xl tn-text-md">{{item.title}}</view>
							<view class="tn-flex-basic-xs tn-icon-right tn-text-right"></view>
						</view>
					</tn-list-cell>
				</navigator>
			</tn-list-view>

			<view class="tn-padding-bottom-lg"></view>

			<tn-list-cell :hover="true" :unlined="true" :radius="true" :fontSize="30">
				<view class="tn-flex" @click="TechnicalSupport = true">
					<view class="tn-flex-basic-xl tn-text-md">技术支持</view>
					<view class="tn-flex-basic-xs tn-icon-ai tn-text-right"></view>
				</view>
			</tn-list-cell>
			<tn-popup v-model="TechnicalSupport" :marginTop="vuex_custom_bar_height" mode="center" :closeBtn="true"
				closeBtnIcon="close" closeBtnPosition="top-right" :maskCloseable="true"
				@close="TechnicalSupport = false">
				<view class="popup-content popup-content--center">
					<image
						src="https://mmbiz.qpic.cn/mmbiz_jpg/IRwGyO1Yqo4fAmib1NdkYvKxtneF8ToxcLUSRR7DcYyZibmcbK2HJlCmMyljERRZReWOtic2v26amicWQw44iaCrU2A/0?wx_fmt=jpeg" />
				</view>

			</tn-popup>
		</view>
	</view>
</template>

<script>
	export default {
		data: () => {
			return {
				user: {
					username: "",
					password: ""
				},
				logs: [{
					title: "打针记录",
					url: "/petPage/Injection/Injection"
				}, {
					title: "用餐记录",
					url: "/petPage/Meal/Meal"
				}, {
					title: "看病记录",
					url: "/petPage/doctor/doctor"
				}, {
					title: "预约提醒",
					url: "/petPage/Appointment/Appointment"
				}],
				TechnicalSupport: false
			}
		},
		onReady() {
			this.getUserInfo()
		},
		methods: {
			getUserInfo() {
				if (uni.getStorageSync('user') != "") {
					this.user = JSON.parse(uni.getStorageSync("user"));
				}
			}
		}

	}
</script>

<style lang="scss" scoped>
	.top-backgroup {
		height: 450rpx;
		z-index: -1;

		.backgroud-image {
			width: 100%;
		}
	}

	.logo-image {
		width: 120rpx;
		height: 120rpx;
		position: relative;
	}

	.logo-pic {
		background-size: cover;
		background-repeat: no-repeat;
		background-position: top;
		border-radius: 50%;
		overflow: hidden;
		background-color: #FFFFFF;
	}

	.about-shadow {
		border-radius: 15rpx;
		box-shadow: 0rpx 0rpx 50rpx 0rpx rgba(0, 0, 0, 0.07);
	}

	.about {
		&__wrap {
			position: relative;
			z-index: 1;
			margin: 20rpx 30rpx;
			margin-top: -230rpx;
		}
	}

	.popup-content {
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;

		&--center {
			padding: 150rpx 50rpx;
		}
	}
</style>