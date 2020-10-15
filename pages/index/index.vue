<template>
	<view class="content">
		<view class="wrap">
			<u-swiper :list="list" bg-color="#fff" effect3d></u-swiper>
		</view>
		<view class="date_time">
			<view class="m_text">{{ date }}</view>
			<view class="m_text">{{ week }}</view>
			<view class="m_text">{{ time }}</view>
		</view>
		<u-toast ref="uToast" />
		<view class="box">
			<view @click="daka()" class="my_button" :class="[{'my_button_bg':buClick},{'my_button_bg_sucess':today}]">{{tips}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		computed: {
			buClick() {
				if (!this.today && this.isOk) {
					return true
				} else {
					return false
				}
			},
			tips() {
				if (this.late) {
					return '已超过规定时间'
				}
				if (this.today) {
					return '今日已打卡'
				}
				if (!this.isOk) {
					return '请将手机垂直竖起'
				}
				if (!this.today && this.isOk) {
					return '点击打卡️'
				}
			}
		},
		data() {
			return {
				date: '',
				time: '',
				week: '',
				list: [{
						image: 'https://cdn.uviewui.com/uview/swiper/1.jpg',
						title: ''
					},
					{
						image: 'https://cdn.uviewui.com/uview/swiper/2.jpg',
						title: ''
					},
					{
						image: 'https://cdn.uviewui.com/uview/swiper/3.jpg',
						title: ''
					}
				],
				today: false,
				isOk: false,
				late: false
			}
		},
		onLoad() {
			this.currentTime()
			this.getWeekDate()
		},
		onShow() {
			this.getAccelerometer()
		},
		onHide() {
			uni.stopAccelerometer();
		},
		methods: {
			timeDate() {
				var _this = this
				let mm = new Date().getMonth() + 1
				if (mm < 10) {
					mm = '0' + mm
				}
				const dd = new Date().getDate()
				const hh = new Date().getHours()
				const mf =
					new Date().getMinutes() < 10 ?
					'0' + new Date().getMinutes() :
					new Date().getMinutes()
				const ss =
					new Date().getSeconds() < 10 ?
					'0' + new Date().getSeconds() :
					new Date().getSeconds()
				_this.date = mm + '月' + dd + '日 '
				_this.time = hh + ':' + mf + ':' + ss
			},
			getWeekDate() {
				var now = new Date()
				var day = now.getDay()
				var weeks = new Array(
					'星期日',
					'星期一',
					'星期二',
					'星期三',
					'星期四',
					'星期五',
					'星期六'
				)
				var week = weeks[day]
				this.week = week
			},
			currentTime() {
				setInterval(this.timeDate, 1000)
			},
			getAccelerometer() {
				let that = this
				uni.onAccelerometerChange(function(res) {
					console.log(res)
					if (res.y <= -0.95) {
						that.isOk = true
					} else {
						that.isOk = false
					}
				});
			},
			daka() {
				if (!this.today && this.isOk) {
					this.today = true
					this.$refs.uToast.show({
						title: '打卡成功✌️',
						type: 'success'
					})
				}
			}
		}
	}
</script>

<style lang="scss">
	.content {
		display: flex;
		flex-direction: column;

		.wrap {
			width: 750rpx;
		}

		.date_time {
			margin: 40rpx 20rpx;
			display: flex;
			justify-content: space-between;

			.m_text {
				width: 210rpx;
				height: 80rpx;
				border-radius: 20rpx;
				font-size: 44rpx;
				line-height: 80rpx;
				color: #fff;
				text-align: center;
				background-color: #18B566;
			}
		}

		.box {
			display: flex;
			justify-content: center;

			.my_button {
				display: flex;
				justify-content: center;
				align-items: center;
				width: 300rpx;
				height: 300rpx;
				border-radius: 50%;
				background-color: #c8c9cc;
				color: #fff;
				font-size: 36rpx;
			}

			.my_button_bg {
				background-color: #377cf6;
			}

			.my_button_bg_sucess {
				background-color: #19be6b;
			}
		}

	}
</style>
