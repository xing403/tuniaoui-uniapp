<template>
	<view class="template-job tn-safe-area-inset-bottom">
		<tn-nav-bar :isBack="false">首页</tn-nav-bar>

		<view class="tn-margin-top" :style="{paddingTop: vuex_custom_bar_height + 'px'}">
			<tn-swiper :list="banner" :height="350" :effect3d="true" mode="round"></tn-swiper>
		</view>
		<tn-notice-bar class="tn-text-xl tn-margin-left-sm tn-margin-top-lg"
			:list="['我们准备了一些...呃...小常识']"></tn-notice-bar>
		<tn-tabs :list="scrollList" :current="current" @change="tabChange" :itemWidth="750 / 4"></tn-tabs>
		<tn-collapse v-for="(faq, index) in faqs" :key="index" v-if="current == index">
			<tn-collapse-item v-for="(item, i) in faq" :key="i" :title="item.f">
				<view class="tn-text-break-word">
					{{ item.q }}
				</view>
			</tn-collapse-item>
		</tn-collapse>
		<view class="" style="padding-bottom: 20px;"></view>
	</view>
</template>

<script>
	import template_page_mixin from '@/libs/mixin/template_page_mixin.js'
	import faqsData from '@/mock/faqs.js'
	export default {
		name: 'TemplateJob',
		mixins: [template_page_mixin],
		data() {
			return {
				banner: [{
					image: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F9ef39841-edd2-4531-8030-fb98edba965c%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1683645754&t=5aaff63abcfe7ad0b3c65f2269146b9b'
				}, {
					image: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2Fae93e1e8-fe84-4774-aba9-ce4307c9550d%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1683645754&t=e71eb0da3f95b743096325c057b8fd7a'
				}, {
					image: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F2c99230f-c629-4e0c-889e-a65ca5325632%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1683645754&t=72c20d6c1e90a59620d28f0ab4afb461'
				}, {
					image: 'https://img0.baidu.com/it/u=2546138395,3168489866&fm=253&fmt=auto&app=120&f=JPEG?w=1080&h=670'
				}],
				current: 0,
				scrollList: [{
						name: '汪星人'
					},
					{
						name: '喵星人'
					},
					{
						name: '歪星人'
					},
					{
						name: '水星人'
					},
				],
				faqs: []
			}
		},
		onReady() {
			this.faqs = faqsData.data
		},
		methods: {
			tabChange(index) {
				this.current = index
			}
		}
	}
</script>

<style lang="scss" scoped>
	@import '@/static/css/templatePage/custom_nav_bar.scss';

	/deep/.tn-collapse-class {
		padding-bottom: 50px;
	}
</style>