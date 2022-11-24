<template>
	<view class="content" id="content" @touchend="maskTouchend">
		<view class="box">
			<view class="progress" id="progress">
				<view class="green" :style="[{ height: vol+'%'}]">
					<view class="btn" @touchstart="ChangeVolStart" @touchmove="ChangeVol">
					</view>
				</view>
			</view>
			<view class="icon">
				<uni-icons v-if="vol!=0" type="sound-filled" size="40" @click="changeType(true)"></uni-icons>
				<uni-icons v-else type="sound" size="40" @click="changeType(false)"></uni-icons>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				vol: 50,
				contentHeight: 0,
				progHeight: 0,
				timer: 0,
				startVol: 0, //记录拖动前的数值
				stopVol: 0, //记录按静音前的数值
				startY: 0,
				flag: true,
				touchNum: 0,
			}
		},
		mounted() {
			const query = uni.createSelectorQuery().in(this);
			let _this = this;
			query.select('#content').boundingClientRect((data) => {
				_this.contentHeight = data.height;
			}).exec();
			query.select('#progress').boundingClientRect((data) => {
				_this.progHeight = data.height;

			}).exec();
			setTimeout(() => this.watchVoice());
		},
		beforeDestroy() {
			clearTimeout(this.timer);
		},
		destroyed() {
			clearTimeout(this.timer);
		},
		methods: {
			maskTouchend(e) {
				console.log('lllll')
				this.touchNum++
				setTimeout(() => {
					// if(this.touchNum == 1){
					// 	console.log('单击')
					// }
					if (this.touchNum >= 2) {
						location.reload();
					}
					this.touchNum = 0
				}, 250)
			},
			changeType(close) {
				if (close) {
					this.stopVol = this.vol;
					this.vol = 0;
					this.setVoice(0)
				} else {
					this.vol = this.stopVol;
					this.setVoice(this.vol / 100)
				}
			},
			watchVoice() {
				//#ifdef APP-PLUS
				if (plus) {
					this.vol = plus.device.getVolume() * 100;
					this.stopVol = this.vol;
				}
				clearTimeout(this.timer);
				this.timer = setTimeout(() => this.watchVoice(), 2000);
				// #endif
			},
			ChangeVolStart(e) {
				this.startY = e.changedTouches[0].pageY
				this.startVol = this.vol;

			},
			ChangeVol(e) {
				let v = Math.trunc((this.startY - e.changedTouches[0].pageY) / this.progHeight * 100) + this.startVol;
				if (v > 100) {
					v = 100
				} else if (v < 0) {
					v = 0
				}
				this.vol = v;
				this.stopVol = this.vol;
				this.setVoice(this.vol / 100) //移动时一直改变
				uni.stopPullDownRefresh();
			},
			setVoice(voice) {
				//#ifdef APP-PLUS
				if (this.flag && plus) {
					this.flag = false;
					plus.device.setVolume(voice);
					setTimeout(() => {
						this.flag = true;
					}, 400)
				}
				// #endif
			}
		}
	}
</script>

<style>
	.content {
		width: 100vw;
		height: 100vh;
		position: fixed;
		top: 0;
		left: 0;
		overflow: hidden;
		background-image: url(https://tuapi.eees.cc/api.php?category=dongman&type=302&px=m); //图片类型更换请修改参数  
		background-repeat: no-repeat;
		background-size: cover;
		background-attachment: fixed;
	}

	.box {
		padding-right: 10vw;
		box-sizing: border-box;
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: flex-end;
		justify-content: center;

	}

	.progress {
		width: 10vw;
		height: 40vh;
		background-image: linear-gradient(19deg, #e6e5e3 21%, #e6d9d9 50%, #cfc0c0 100%);
		border: 1px solid #cccccc;
		border-radius: 16rpx;
		display: flex;
		flex-direction: column;
		justify-content: flex-end;
		margin-bottom: 5vh;
	}

	.green {
		width: 100%;
		background-image: linear-gradient(0deg, #D9AFD9 0%, #97D9E1 100%);
		position: relative;
	}

	.btn {
		width: 12vw;
		height: 12vw;
		border-radius: 6vw;
		border: 1px solid #808080;
		background-image: linear-gradient(19deg, #c5bdba 44%, #ffffff 100%);
		position: absolute;
		left: 50%;
		top: 0;
		transform: translate(-50%, -50%);
		z-index: 2;
	}

	.icon {
		width: 90rpx;
		height: 90rpx;
		border-radius: 45rpx;
		background-color: rgba(255, 255, 255, 0.5);
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
