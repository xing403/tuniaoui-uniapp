<template>
	<view class="tn-safe-area-inset-bottom">

		<tn-nav-bar fixed :isBack="true">宠物管理</tn-nav-bar>
		<!-- 页面内容 -->
		<view :style="{paddingTop: vuex_custom_bar_height + 'px'}">

			<view class="tn-margin-xs">
				<tn-form :model="model" ref="form">
					<tn-form-item label="宠物姓名" prop="name" :required="true">
						<tn-input v-model="model.name" placeholder="请输入姓名" />
					</tn-form-item>
					<tn-form-item label="宠物性别" prop="sex" :required="true">
						<tn-radio-group v-model="model.sex">
							<tn-radio name="男">男</tn-radio>
							<tn-radio name="女">女</tn-radio>
						</tn-radio-group>
					</tn-form-item>
					<tn-form-item label="出生日期" prop="birthday" :required="true">
						<tn-input v-model="model.birthday" type="select" placeholder="请选择出生日期" :selectOpen="calendar"
							@click="calendar = true"></tn-input>
					</tn-form-item>
					<tn-form-item label="宠物体重" prop="weight" :required="true">
						<tn-input v-model="model.weight" placeholder="请输入姓名" />
					</tn-form-item>
					
					<tn-form-item label="宠物品种" prop="variety" :required="true">
						<tn-input v-model="model.variety" type="select" placeholder="请选择宠物品种" :selectOpen="variety"
							@click="variety = true"></tn-input>
					</tn-form-item>
					<tn-form-item label="是否绝育" prop="isSterilization" :required="true">
						<tn-radio-group v-model="model.isSterilization">
							<tn-radio name="是">是</tn-radio>
							<tn-radio name="否">否</tn-radio>
						</tn-radio-group>
					</tn-form-item>
					<view class="tn-margin-top-sm">
						<tn-button backgroundColor="#01BEFF" fontColor="#FFFFFF" width="100%"
							@click="submit">提交</tn-button>
					</view>
					<view class="tn-margin-top-sm">
						<tn-button class="tn-margin-top-sm" backgroundColor="#F56C6C" fontColor="#FFFFFF" width="100%"
							@click="drop">删除</tn-button>

					</view>

				</tn-form>
			</view>
		</view>
		<tn-calendar v-model="calendar" mode="date" :showLunar="true" :changeYear="true" :changeMonth="true"
			@change="onChangeBirthday"></tn-calendar>
		<tn-select v-model="variety" mode="multi-auto" title="请选择宠物品种" :list="list" @confirm="selectVariety">
		</tn-select>

		<tn-tips ref="tip" :top="vuex_custom_bar_height"></tn-tips>
	</view>
</template>

<script>
	import petData from '@/mock/pet.js'
	export default {
		data() {
			return {
				model: {
					name: "",
					sex: "",
					isSterilization: "",
					variety: "",
					birthday: "",
					weight:""
				},
				calendar: false,
				variety: false,
				rules: {
					name: [{
						required: true,
						message: '请输入用户名',
						trigger: 'blur'
					}, ],
					sex: [{
						required: true,
						message: '请选择性别',
						trigger: 'change'
					}],
					birthday: [{
						required: true,
						message: '请选择出生日期',
						trigger: 'change'
					}],
					isSterilization: [{
						required: true,
						message: '请选择是否绝育',
						trigger: 'change'
					}],
					weight: [{
						required: true,
						message: '请输入体重',
						trigger: 'blur'
					}, ],
					variety: [{
						required: true,
						message: '请选择宠物品种',
						trigger: 'change'
					}]
				},
				list: []
			}
		},
		onReady() {
			this.$refs.form.setRules(this.rules)
			this.model = JSON.parse(uni.getStorageSync("pet"))
			
			this.list = petData.data
		},
		methods: {
			onChangeBirthday(event) {
				this.model.birthday = event.date;
			},
			selectVariety(event) {
				let str = []
				for (let i = 0; i < event.length; i++) {
					str.push(event[i].label)
				}
				this.model.variety = str.join("-")
			},
			submit() {
				let that = this
				that.$refs.form.validate(valid => {
					if (valid) {
						// 验证通过
						uni.setStorageSync("pet", JSON.stringify(that.model))
						that.$refs.tip.show({
							msg: "保存成功",
							backgroundColor: "#67C23A",
							fontColor: "#FFFFFF",
							duration: 2000
						})
						let pages = getCurrentPages(); // 当前页面
						let beforePage = pages[pages.length - 2]; // 上一页
						beforePage.data.pet = that.model
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
			drop() {
				uni.removeStorageSync("pet")
				this.$refs.tip.show({
					msg: "删除成功",
					backgroundColor: "#F56C6C",
					fontColor: "#FFFFFF",
					duration: 2000
				})
				this.model = {
					name: "",
					sex: "",
					isSterilization: "",
					variety: "",
					birthday: ""
				}
			}
		}
	}
</script>

<style>

</style>