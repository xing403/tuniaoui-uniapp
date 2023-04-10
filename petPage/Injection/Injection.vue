<template>
	<view class="components tn-safe-area-inset-bottom">

		<tn-nav-bar fixed :isBack="true">打针记录</tn-nav-bar>
		<!-- 页面内容 -->
		<view :style="{paddingTop: vuex_custom_bar_height + 'px'}">
			<tn-collapse>
				<tn-collapse-item v-for="(item, index) in list" :key="index" :title="`${item.title} | ${item.time}`">
					<tn-form labelPosition="left" :labelWidth='140'></tn-form>
					<tn-form-item label="疫苗名称" labelPosition="left" labelAlign="right">
						<tn-input v-model="item.title" type="text" />
					</tn-form-item>
					<tn-form-item label="注射时间" labelPosition="left" labelAlign="right">
						<tn-input v-model="item.time" />
					</tn-form-item>
					<tn-form-item label="疫苗备注" labelPosition="left" labelAlign="right">
						<tn-input v-model="item.content" type="textarea"/>
					</tn-form-item>
				</tn-collapse-item>
			</tn-collapse>

			<tn-fab :btnList="btnList" :left="'auto'" :right="40" :bottom="100" :width="88" :height="88" iconSize="64"
				:backgroundColor="'#409eff'" :fontColor="'#FFF'" :icon="'open'" animationType="up" :showMask="true"
				@click="clickFabItem">
			</tn-fab>
			<tn-popup class="popup-content" v-model="addPopup" :marginTop="vuex_custom_bar_height" length="90%"
				mode="center" backgroundColor="#FFF" :borderRadius="23" :closeBtn="true" closeBtnIcon="close"
				closeBtnPosition="top-right" :maskCloseable="true" @close="addPopup = false">
				<tn-form :model="addPopupForm" ref="form" labelPosition="top">
					<tn-form-item label="疫苗名称" prop="title" :required="true" labelPosition="top">
						<tn-input v-model="addPopupForm.title" type="text" placeholder="请输入疫苗名称"
							:border="border"></tn-input>
					</tn-form-item>
					<tn-form-item label="疫苗时间" prop="time" :required="true" labelPosition="top">
						<tn-input v-model="addPopupForm.time" type="text" placeholder="疫苗时间"
							@click="timeSelect.show = true" :border="border"></tn-input>
					</tn-form-item>
					<tn-form-item label="疫苗描述" prop="content" labelPosition="top">
						<tn-input v-model="addPopupForm.content" type="text" placeholder="疫苗描述"></tn-input>
					</tn-form-item>

					<tn-button backgroundColor="#01BEFF" fontColor="#FFFFFF" width="100%" @click="save">保存</tn-button>
				</tn-form>
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
				addPopup: false,
				addPopupForm: {
					time: "",
					title: "",
					content: ""
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
				rules: {
					time: [{
						required: true,
						message: '请选择日期时间',
						trigger: 'blur'
					}],
					title: [{
						required: true,
						message: '请输入疫苗标题',
						trigger: 'blur'
					}]
				},
				btnList: [{
					icon: 'add',
					text: '添加记录',
					bgColor: '#E72F8C'
				}],
				list: []
			}
		},
		onReady() {
			this.$refs.form.setRules(this.rules)
			this.list = uni.getStorageSync("Injection") == "" ? [] : JSON.parse(uni.getStorageSync("Injection"))
		},
		methods: {
			clickFabItem(e) {
				switch (e.index) {
					case 0:
						this.addPopup = true
						break;
				}
			},
			save() {
				let that = this;
				that.$refs.form.validate(valid => {
					if (valid) {
						let data = [];
						if (uni.getStorageSync("Injection") == "") {
							data.push(that.addPopupForm)
							uni.setStorageSync("Injection", JSON.stringify(data))
						} else {
							data = JSON.parse(uni.getStorageSync("Injection"))
							data.push(that.addPopupForm)
							uni.setStorageSync("Injection", JSON.stringify(data))
						}
						that.addPopupForm = {
							time: "",
							title: "",
							content: ""
						}
						that.$refs.tip.show({
							msg: "保存成功",
							backgroundColor: "#67C23A",
							fontColor: "#FFFFFF",
							duration: 2000
						})
						that.list = data
						that.addPopup = false
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
				this.addPopupForm.time = `${event.year}-${event.month}-${event.day} ${event.hour}:${event.minute}`
			}
		}
	}
</script>

<style>

</style>