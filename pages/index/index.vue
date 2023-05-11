<template>
	<view class="content">
		<scroll-view scroll-y=true>
			<view class="project" v-for="(item,index) in items" :key="item.id" :style="{'background-color': colors[index%3]}" @click="signToDay(item)" @longpress="showSteps(item)">
				<text>{{item.name}}</text>
			</view>
		</scroll-view>
		<uni-popup ref="popup" type="dialog" mask-click="false" >
			<uni-popup-dialog ref="inputClose" mode="input" title="输入" value="" placeholder="请输入事件名"
				@confirm="dialogInputConfirm"></uni-popup-dialog>
		</uni-popup>

		<!-- 步骤条 -->
		<uni-popup ref="steps" background-color="#fff" type="bottom" @change="change">
			<uni-steps :options="steps" direction="column"></uni-steps>
		</uni-popup>

		<uni-fab pattern="{}" horizontal="right" vertical="bottom" popMenu="false"
			@fabClick="showDialogForAdd"></uni-fab>
	</view>
</template>

<script>	
 import '../../utils/utils.js'
	export default {
		data() {
			return {
				colors:['#6565ff','#7d4236','#3b7d75'],
				items: [{
					name: "理发",
					steps: [{"desc":"18-02-30 12:24"}, {"desc":"18-05-30 12:24"}],
					"id": "94203"
				}, {
					name: "理发",
					steps: [{"desc":"18-02-30 12:24"}, {"desc":"18-05-30 12:24"}],
					"id": "94203"
				}, {
					name: "理发",
					steps: [{"desc":"18-02-30 12:24"}, {"desc":"18-05-30 12:24"}],
					"id": "94203"
				}],
				steps:[]
			}
		},
		onLoad() {

		},
		onLaunch() {
			uni.startPullDownRefresh()
			this.items = uni.getStorage({
				key: "data",
				success() {
					console.log("数据成功")
					uni.stopPullDownRefresh()
				},
				fail() {
					this.items = []
					uni.stopPullDownRefresh()
				}
			})
		},
		onHide() {
			uni.setStorage({
				key: "data",
				data: this.items,
				success() {
					console.log("save data success")
				}
			})
		},
		methods: {
			signToDay(item){
				this.items.filter(it => it.name === item.name)[0]
				.steps.splice(0,0,{"desc":new Date().Format("yyyy-MM-dd HH:mm")})
				uni.showToast({
					title:`今天 ${item.name} 已记录`
				})
			},
			showSteps(item) {
				this.steps = item.steps;
				this.$refs.steps.open()
			},
			dialogInputConfirm(val) {
				if (val == "" || val == undefined) {
					uni.showToast({
						title: "不能输入空数据哦",
						icon: 'none'
					})
				}
				if (val.length > 5) {
					uni.showToast({
						title: "你输入的太多了",
						icon: 'none'
					})
				}
				if(this.items.find(it => it.name===val)){
					uni.showToast({
						title:"好像已经存在了",
						icon:'none'
					})
				}
				console.log(val)
			},
			showDialogForAdd(item) {
				this.$refs.popup.open()
			},
			addItem() {

			},
		}
	}
</script>

<style lang="scss">
	.content {
		display: flex;
		flex-direction: column;
		height: 100vh;
		background-color: darkgrey;
		width: 100%;

		.project {
			position: relative;
			height: 80rpx;
			margin: 20rpx;
			background-color: forestgreen;
			border-radius: 10rpx;
			box-sizing: border-box;
			background-color: $uni-color-primary;


			text {
				position: absolute;
				left: 50%;
				top: 50%;
				color: whitesmoke;
				transform: translate(-50%, -55%);
			}
		}
	}
</style>