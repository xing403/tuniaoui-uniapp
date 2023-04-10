<template>
	<view class="components tn-safe-area-inset-bottom">

		<tn-nav-bar fixed :isBack="true">用餐记录</tn-nav-bar>
		<!-- 页面内容 -->
		<view :style="{paddingTop: vuex_custom_bar_height + 'px'}">
			<tn-collapse>
				<tn-collapse-item v-for="(item, index) in list" :key="index" :title="item.time">
					<view class="tn-flex">
						<view class="tn-flex-basic-xs">用餐时间</view>
						<view class="tn-flex-basic-xl">{{item.time}}</view>
					</view>
					<view v-for="(food,i) in item.foods" :key="i" class="tn-flex">
						<view class="tn-flex-basic-sm">{{food.name}}</view>
						<view class="tn-flex-basic-lg">
							<tn-tag backgroundColor="rgba(1, 190, 255, 0.8)" fontColor="#FFF" shape="radius"
								margin="10rpx 10rpx">{{ food.weight }} g
							</tn-tag>
						</view>
					</view>
				</tn-collapse-item>
			</tn-collapse>



			<tn-fab :btnList="btnList" :left="'auto'" :right="40" :bottom="100" :width="88" :height="88" iconSize="64"
				:backgroundColor="'#409eff'" :fontColor="'#FFF'" :icon="'open'" animationType="up" :showMask="true"
				@click="clickFabItem">
			</tn-fab>
			<tn-popup class="popup-content" v-model="addMeal" :marginTop="vuex_custom_bar_height" length="90%"
				mode="center" backgroundColor="#FFF" :borderRadius="23" :closeBtn="true" closeBtnIcon="close"
				closeBtnPosition="top-right" :maskCloseable="true" @close="addMeal = false">
				<tn-form :model="addMealForm" ref="form">
					<tn-form-item label="饮食时间" prop="time" :required="true" labelPosition="top">
						<tn-input v-model="addMealForm.time" placeholder="饮食时间" @click="timeSelect.show = true"
							:border="border"></tn-input>
					</tn-form-item>
					<tn-form-item label="食物列表" prop="foods" labelPosition="top">
						<view class="foods" v-for="(item,index) in addMealForm.foods" :key="index">
							<view class="tn-flex">
								<view class="tn-flex-basic-lg">
									<tn-input v-model="addMealForm.foods[index].name" placeholder="食物名称"
										:border="border" />
								</view>
								<view class="tn-flex-basic-sm">
									<tn-input v-model="addMealForm.foods[index].weight" placeholder="食物量(克)"
										:border="border" />
								</view>
							</view>
						</view>
					</tn-form-item>
					<tn-button width="100%" @click="addFoods">添加食物项</tn-button>
				</tn-form>
				<tn-button backgroundColor="#01BEFF" fontColor="#FFFFFF" width="100%" @click="save">保存</tn-button>
			</tn-popup>


			<tn-picker v-model="timeSelect.show" mode="time" :params="timeSelect.params" :showTimeTag="true"
				:startYear="2000" :endYear="2100" :maskCloseable="true" @cancel="timeSelect.show = false"
				@confirm="confirmPicker">
			</tn-picker>
		</view>
		<tn-tips ref="tip" :top="vuex_custom_bar_height"></tn-tips>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				addMeal: false,
				addMealForm: {
					time: "",
					foods: [{
						name: "",
						weight: ""
					}]
				},
				timeSelect: {
					show: false,
					params: {
						year: true,
						month: true,
						day: true,
						hour: true,
						minute: true
					}
				},
				btnList: [{
					icon: 'add',
					text: '添加记录',
					bgColor: '#E72F8C'
				}],
				rules: {
					time: [{
						required: true,
						message: '请选择日期时间',
						trigger: 'blur'
					}]
				},
				list: []
			}
		},
		onReady() {
			this.$refs.form.setRules(this.rules)
			this.list = uni.getStorageSync("Meal") == "" ? [] : JSON.parse(uni.getStorageSync("Meal"))
		},
		methods: {
			clickFabItem(e) {
				switch (e.index) {
					case 0:
						this.addMeal = true
						break;
				}
			},
			addFoods() {
				this.addMealForm.foods.push({
					name: "",
					weight: ""
				})
			},
			save() {
				let that = this;
				that.$refs.form.validate(valid => {
					if (valid) {
						let data = [];
						if (uni.getStorageSync("Meal") != "") {
							data = JSON.parse(uni.getStorageSync("Meal"))
						}
						data.push(that.addMealForm)
						uni.setStorageSync("Meal", JSON.stringify(data))
						that.addMealForm = {
							time: "",
							foods: [{
								name: "",
								weight: ""
							}]
						}
						that.$refs.tip.show({
							msg: "保存成功",
							backgroundColor: "#67C23A",
							fontColor: "#FFFFFF",
							duration: 2000
						})
						that.list = data;
						that.addMeal = false
					} else {
						that.$refs.tip.show({
							msg: "保存失败",
							backgroundColor: "#F56C6C",
							fontColor: "#FFFFFF",
							duration: 2000
						})
					}
				})
			},
			confirmPicker(event) {
				this.addMealForm.time = `${event.year}-${event.month}-${event.day} ${event.hour}:${event.minute}`
			}
		}
	}
</script>

<style>

</style>