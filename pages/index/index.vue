<template>
	<view class="content">
		<scroll-view scroll-y=true>
			<uni-swipe-action>
				<block v-if="items.length>0">
					<uni-swipe-action-item v-for="(item,index) in items" :key="item.id"
						@click.capture="onClickSwipeAction(item)">
						<template v-slot:right>
							<view class="swipeAction"><text>删除</text></view>
						</template>
						<view class="project" :style="{'background-color':colors[index%3]}" @click.stop="signToDay(item)" @longpress.stop="showSteps(item)">
							<text>{{item.name}}</text>
						</view>
					</uni-swipe-action-item>
				</block>
				<block v-else>
					<view class="empty">
						<text>好像还没有根据项目，点击右下角增加</text>
					</view>
				</block>
				
			</uni-swipe-action>
		</scroll-view>
		<uni-popup ref="popup" type="dialog" mask-click="false">
			<uni-popup-dialog ref="inputClose" mode="input" title="输入" value="" placeholder="请输入事件名"
				@confirm="dialogInputConfirm"></uni-popup-dialog>
		</uni-popup>

		<!-- 步骤条 -->
		<uni-popup ref="steps" background-color="#fff" type="bottom" @change="change">
			<uni-steps v-if="steps.length>0" :options="steps" direction="column"></uni-steps>
			<view v-else class="empty">
				<text>好像还没干过这个</text>
			</view>
		</uni-popup>

		<uni-fab pattern="{}" horizontal="right" vertical="bottom" popMenu=false
			@fabClick="showDialogForAdd"></uni-fab>
	</view>
</template>

<script>
	import '../../utils/utils.js'
	export default {
		data() {
			return {
				colors: ['#6565ff', '#527cb3', '#55b4a7'],
				items: [{
					name: "理发",
					steps: [{
						"desc": "18-02-30 12:24"
					}, {
						"desc": "18-05-30 12:24"
					}],
					"id": "94203"
				}],
				steps: []
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
			signToDay(item) { // 记录
				let target = this.items.filter(it => it.name === item.name)[0]
				target.steps.splice(0, 0, {
					"desc": new Date().Format("yyyy-MM-dd HH:mm")
				}) // 插入时间
				uni.showToast({
					title: `今天 ${target.name} 已记录`
				})
				if (target.steps.length > 5) {
					target.steps.splice(target.steps.length - 1, 1)
				}

			},
			onClickSwipeAction(e, item) {
				let index = this.items.findIndex(it => it.id === item.id)
				if (index != -1) {
					this.items.splice(index, 1)
				}
			},
			showSteps(item) {
				this.steps = item.steps;
				this.$refs.steps.open()
			},
			dialogInputConfirm(val) { // 新项目
				if (val == "" || val == undefined) {
					uni.showToast({
						title: "不能输入空数据哦",
						icon: 'none'
					})
					return
				}
				if (val.length > 5) {
					uni.showToast({
						title: "你输入的太多了",
						icon: 'none'
					})
					return
				}
				if (this.items.find(it => it.name === val)) {
					uni.showToast({
						title: "好像已经存在了",
						icon: 'none'
					})
					return
				}

				this.items.splice(0, 0, {
					id: this.items.length,
					name: val,
					steps: []
				})
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

		.empty {
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100rpx;
			width: 100%;
		}
		
		.swipeAction {
			display: flex;
			justify-content: center;
			align-items: center;
			border-radius: 10rpx;
			background-color: indianred;
			box-sizing: border-box;
			height: 80rpx;
			margin: 20rpx 5rpx 20rpx 0;
			padding: 10rpx 0;
			color: whitesmoke;
			width: 150rpx;
			
		}
	}
</style>