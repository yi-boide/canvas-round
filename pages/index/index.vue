<template>
	<view class="content">
		<view class="text-area">
			<text class="title">圆弧</text>
		</view>
		<boideRound
			:val="val"
			:id="charts.id"
			:width="charts.width"
			:border="charts.border"
			@change="changeuCharts"
		/>
	</view>
</template>
<script>
import boideRound from '@/components/boide-round/boide-round.vue';
	export default {
		components: {
			boideRound
		},
		data() {
			return {
				val: 0,
				charts: {
					id: 'boide-round',
					width: uni.getSystemInfoSync().windowWidth - 40,
					border: 16,
					pageBg: '#fff'
				}
			}
		},
		methods: {
			changeuCharts(val) {
				this.val = val
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.text-area {
		display: flex;
		justify-content: center;
		margin: 50rpx 0;
		font-size: 36rpx;
		color: #8f8f94;
	}
	
</style>
