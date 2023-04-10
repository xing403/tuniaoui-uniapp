<template>
	<view class="components tn-safe-area-inset-bottom">

		<tn-nav-bar fixed :isBack="true">看病记录</tn-nav-bar>
		<view :style="{paddingTop: vuex_custom_bar_height + 'px'}">
			<tn-collapse>
				<tn-collapse-item v-for="(item, index) in list" :key="index" :title="item.time">

					<view class="disease">病因：{{item.disease}}</view>
					<view class="symptom">症状：{{item.symptom}}</view>
					<tn-list-view>
						<tn-list-cell>
							<view v-for="(drug,i) in item.drugs" :key="i">
								<view class="tn-flex">
									<view class="tn-flex-basic-lg">
										{{drug.name}}
									</view>
									<view class="tn-flex-basic-sm">
										<tn-tag backgroundColor="tn-main-gradient-indigo" shape="circle"
											margin="0rpx 5rpx">{{drug.dosage}}</tn-tag>
									</view>
								</view>
								<view class="tn-flex-basic-full">
									{{drug.desc}}
								</view>
							</view>
						</tn-list-cell>
					</tn-list-view>
				</tn-collapse-item>
			</tn-collapse>
			<tn-fab :btnList="btnList" :left="'auto'" :right="40" :bottom="100" :width="88" :height="88" iconSize="64"
				:backgroundColor="'#409eff'" :fontColor="'#FFF'" :icon="'open'" animationType="up" :showMask="true"
				@click="clickFabItem">
			</tn-fab>
			<tn-popup class="popup-content" v-model="addDoctor" :marginTop="vuex_custom_bar_height" length="90%"
				mode="center" backgroundColor="#FFF" :borderRadius="23" :closeBtn="true" closeBtnIcon="close"
				closeBtnPosition="top-right" :maskCloseable="true" @close="addDoctor = false">
				<tn-form :model="addDoctorForm" ref="form">
					<tn-form-item label="生病时间" prop="time" :required="true" labelPosition="top">
						<tn-input v-model="addDoctorForm.time" placeholder="生病时间" @click="timeSelect.show = true"
							:border="border"></tn-input>
					</tn-form-item>
					<tn-form-item label="病因" prop="disease" :required="true" labelPosition="top">
						<tn-input v-model="addDoctorForm.disease" placeholder="病因" :border="border"></tn-input>
					</tn-form-item>
					<tn-form-item label="症状描述" prop="symptom" :required="true" labelPosition="top">
						<tn-input v-model="addDoctorForm.symptom" placeholder="症状描述" :border="border"></tn-input>
					</tn-form-item>
					<tn-form-item label="食物列表" prop="drugs" labelPosition="top">
						<view class="drugs" v-for="(item,index) in addDoctorForm.drugs" :key="index">
							<view class="tn-flex">
								<view class="tn-flex-basic-lg">
									<tn-input v-model="addDoctorForm.drugs[index].name" placeholder="药名"
										:border="border" />
								</view>
								<view class="tn-flex-basic-sm">
									<tn-input v-model="addDoctorForm.drugs[index].dosage" placeholder="用量"
										:border="border" />
								</view>
							</view>
							<view>
								<tn-input v-model="addDoctorForm.drugs[index].desc" placeholder="描述" :border="border" />
							</view>
						</view>
					</tn-form-item>
					<tn-button width="100%" @click="addDrugs">添加药物项</tn-button>
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
				addDoctor: false,
				addDoctorForm: {
					time: "",
					disease: "",
					symptom: "",
					drugs: [{
						name: "",
						dosage: "",
						desc: ""
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
					}],
				},
				list: []
			}
		},
		onReady() {
			this.$refs.form.setRules(this.rules)

			this.list = uni.getStorageSync("doctor") == "" ? [] : JSON.parse(uni.getStorageSync("doctor"))
		},
		methods: {
			addDrugs() {
				this.addDoctorForm.drugs.push({
					name: "",
					dosage: "",
					desc: ""
				})
			},
			clickFabItem(e) {
				switch (e.index) {
					case 0:
						this.addDoctor = true
						break;
				}
			},
			save() {
				let that = this;
				that.$refs.form.validate(valid => {
					if (valid) {
						let data = [];
						if (uni.getStorageSync("doctor") != "") {
							data = JSON.parse(uni.getStorageSync("doctor"))
						}
						data.push(that.addDoctorForm)
						uni.setStorageSync("doctor", JSON.stringify(data))
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
						that.addDoctor = false
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
				this.addDoctorForm.time = `${event.year}-${event.month}-${event.day} ${event.hour}:${event.minute}`
			}
		}
	}
</script>

<style lang="scss" scoped>

</style>