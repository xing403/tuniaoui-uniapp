<template>
	<view class="components tn-safe-area-inset-bottom">

		<tn-nav-bar fixed :isBack="true">预约提醒</tn-nav-bar>
		<!-- 页面内容 -->
		<view :style="{paddingTop: vuex_custom_bar_height + 'px'}">
			<tn-list-view>
				<tn-list-cell>
					<view v-for="(item, index) in list" :key="index" class="list">
						<view class="list__left">
							<view class="list__left__text">{{item.title}}</view>
						</view>
						<view class="list__right">{{item.time}}</view>
					</view>
				</tn-list-cell>
			</tn-list-view>
			<tn-fab :btnList="btnList" :left="'auto'" :right="40" :bottom="100" :width="88" :height="88" iconSize="64"
				:backgroundColor="'#409eff'" :fontColor="'#FFF'" :icon="'open'" animationType="up" :showMask="true"
				@click="clickFabItem">
			</tn-fab>
			<tn-popup class="popup-content" v-model="addApppintment" :marginTop="vuex_custom_bar_height" length="90%"
				mode="center" backgroundColor="#FFF" :borderRadius="23" :closeBtn="true" closeBtnIcon="close"
				closeBtnPosition="top-right" :maskCloseable="true" @close="addApppintment = false">
				<tn-form :model="addApppintmentForm" ref="form">
					<tn-form-item label="预约标题" prop="title" :required="true" labelPosition="top">
						<tn-input v-model="addApppintmentForm.title" placeholder="预约标题" :border="border"></tn-input>
					</tn-form-item>
					<tn-form-item label="预约时间" prop="time" :required="true" labelPosition="top">
						<tn-input v-model="addApppintmentForm.time" placeholder="预约时间" @click="timeSelect.show = true"
							:border="border"></tn-input>
					</tn-form-item>
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
				addApppintment: false,
				addApppintmentForm: {
					title: "",
					time: ""
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
					}],
					title: [{
						required: true,
						message: '请输入标题',
						trigger: 'blur'
					}],
				},
				list: []
			}
		},
		onReady() {
			this.$refs.form.setRules(this.rules)
			this.list = uni.getStorageSync("Apppintment") == "" ? [] : JSON.parse(uni.getStorageSync("Apppintment"))
		},
		methods: {
			clickFabItem(e) {
				switch (e.index) {
					case 0:
						this.addApppintment = true
						break;
				}
			},
			save() {
				let that = this;
				that.$refs.form.validate(valid => {
					if (valid) {
						let data = [];
						if (uni.getStorageSync("Apppintment") != "") {
							data = JSON.parse(uni.getStorageSync("Apppintment"))
						}
						data.push(that.addApppintmentForm)
						uni.setStorageSync("Apppintment", JSON.stringify(data))
						that.addApppintmentForm = {
							time: "",
							title: ""
						}
						that.$refs.tip.show({
							msg: "保存成功",
							backgroundColor: "#67C23A",
							fontColor: "#FFFFFF",
							duration: 2000
						})
						that.list = data;
						that.addApppintment = false
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
				this.addApppintmentForm.time = `${event.year}-${event.month}-${event.day} ${event.hour}:${event.minute}`
			}
		}
	}
</script>

<style lang="scss" scoped>
	.list {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 3px;
		padding: 5px;
		border-bottom: 1px #AAA solid;

	}
</style>