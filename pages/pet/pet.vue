<template>
	<view class="components tn-safe-area-inset-bottom">

		<!-- 顶部自定义导航 -->
		<tn-nav-bar fixed :isBack="false">宠物首页</tn-nav-bar>

		<!-- 页面内容 -->
		<view class="tn-margin-left-sm tn-margin-right-sm" :style="{paddingTop: vuex_custom_bar_height + 'px'}">

			<navigator v-if="pet == null" open-type="navigate" url="/petPage/add/add">
				<view class="card add-pet">
					<view class="icon tn-icon-add-fill"></view>
					<view class="title tn-text-center">添加宠物</view>
				</view>
			</navigator>
			<navigator v-else open-type="navigate" url="/petPage/admin/admin">
				<view class="tn-flex card">
					<view class="tn-flex-2">
						<view class="pet-name tn-flex tn-flex-basic-lg tn-margin-bottom-sm">
							<view class="tn-text-xxl tn-flex-basic-xs">{{pet.name}}</view>
							<view class="pet-sex tn-flex-basic-xs">
								<view v-if="pet.sex == '男'" class="male tn-icon-sex-male"></view>
								<view v-else class="female tn-icon-sex-female"></view>
							</view>
						</view>
						<view class="pet-birthday tn-flex-basic-lg">{{pet.birthday}}</view>
					</view>

					<view class="tn-flex-1 tn-width-full tn-height-full"></view>
				</view>
			</navigator>
			<tn-grid v-if="pet != null" align="center" hoverClass="tn-hover">
				<block v-for="(item, index) in logs" :key="index">
					<navigator open-type="navigate" :url="item.url">
						<tn-grid-item style="width: 25%;">
							<view class="icon__item--icon" :class="'tn-cool-bg-color-' +((index + 1) * 3)">
								<view :class="'tn-icon-' + item.icon"></view>
							</view>
							<view>{{item.title}}</view>
						</tn-grid-item>
					</navigator>
				</block>
			</tn-grid>
			<view class="tn-flex">
				<view class="tn-flex-1 tn-cool-bg-color-6 sensor">
					<view class="sensor-title">温度：</view>
					<view class="sensor-value" v-if="temperature == null">加载中...</view>
					<view class="sensor-value" v-else>{{ temperature }}℃</view>
				</view>
				<view class="tn-flex-1 tn-cool-bg-color-9 sensor">
					<view class="sensor-title">湿度：</view>
					<view class="sensor-value" v-if="humidity == null">加载中...</view>
					<view class="sensor-value" v-else>{{ humidity }} %</view>
				</view>
			</view>
			<view class="tn-text-bold tn-text-xl">最近预约</view>
			<view v-for="(item, index) in appointments" :key="index" class="list">
				<view class="list__left">
					<view class="list__left__text">{{item.title}}</view>
				</view>
				<view class="list__right">{{item.time}}</view>
			</view>
		</view>

		<view class="tn-padding-bottom-xs"></view>
	</view>
</template>

<script>
	import mqtt from 'mqtt/dist/mqtt.js'
	export default {
		name: 'PetPage',
		data() {
			return {
				pet: null,
				client: null,
				// 温度
				temperature: null,
				// 湿度
				humidity: null,
				logs: [{
					icon: "sword",
					title: "打针记录",
					url: "/petPage/Injection/Injection"
				}, {
					icon: "food-fill",
					title: "用餐记录",
					url: "/petPage/Meal/Meal"
				}, {
					icon: "medical",
					title: "看病记录",
					url: "/petPage/doctor/doctor"
				}, {
					icon: "notice",
					title: "预约提醒",
					url: "/petPage/Appointment/Appointment"
				}],
				appointments: []

			}
		},
		onReady() {
			setInterval(() => {
				if (uni.getStorageSync("pet") != "") {
					this.pet = JSON.parse(uni.getStorageSync("pet"))
				} else {
					this.pet = null
				}
			}, 500)
			this.connect()
			this.getAppointment()
		},
		methods: {
			async connect() {
				let self = this;
				const clientId = `mqtt_${Math.random().toString(16).slice(3)}`;
				// #ifdef H5
				self.client = mqtt.connect('ws://82.157.193.248:8083/mqtt')
				// #endif
				// #ifdef MP-WEIXIN||APP-PLUS
				self.client = mqtt.connect('wx://82.157.193.248:8083/mqtt')
				// #endif
				self.client.on('connect', function() {
					// self.client.subscribe('cwglxthumidity', function(err) {})
					self.client.subscribe(['cwglxthumidity', 'cwglxttemp'], function(err) {})
				}).on('reconnect', function() {
					console.log('on reconnect')
				}).on('error', function() {
					console.log('on error')
				}).on('end', function() {
					console.log('on end')
				}).on('message', function(topic, message) {
					switch (topic) {
						case "cwglxthumidity":
							self.humidity = message.toString()
							break;
						case "cwglxttemp":
							self.temperature = message.toString()
							break
					}

				})
			},
			getAppointment() {
				let a = []
				let appointments = uni.getStorageSync("Apppintment") == "" ? [] : JSON.parse(uni.getStorageSync(
					"Apppintment"))
				for (let i = 0; i < appointments.length; i++) {
					if (new Date(appointments[i].time).getTime() < new Date().getTime() + 2 * 24 * 60 * 60 * 1000) {
						a.push(appointments[i])
					}
				}
				for (let i = 0; i < a.length; i++) {
					for (let j = i; j < a.length; j++) {
						if (new Date(a[i].time).getTime() > new Date(a[j].time).getTime()) {
							let temp = a[i];
							a[i] = a[j];
							a[j] = temp;
						}
					}
				}
				this.appointments = a;
			}
		}
	}
</script>

<style lang="scss" scoped>
	.card {
		border-radius: 5px;
		margin: 10px;
		padding: 30px 10px;
		background-color: #ffaa00;
		color: #FFF;

		.icon {
			font-size: 96rpx;
		}

		.pet-sex {
			font-size: 48rpx;

			.male {
				color: #0fa9f4;
			}

			.female {
				color: #ff77a6;
			}
		}
	}

	.icon {
		&__item--icon {
			width: 100rpx;
			height: 100rpx;
			line-height: 100rpx;
			font-size: 60rpx;
			border-radius: 50%;
			margin: 30rpx;
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
			}
		}
	}

	.sensor {
		margin: 10px;
		border-radius: 15px;
		padding: 20px;

		&-title {
			font-size: 24px;
			margin: 5px;
			text-align: left;
		}

		&-value {
			margin: 5px;
			font-size: 30px;
			font-weight: 600;
			text-align: right;
		}
	}

	.list {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 3px;
		padding: 5px;
		border-bottom: 1px #AAA solid;
	}
</style>
