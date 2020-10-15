<template>
	<div class="calender">
		<div class="top">
			<div class="top_date">
				{{year}}年{{month}}月
			</div>
			<div class="btn_wrap">
				<ul>
					<li @click="handleShowNextMonth">
						下个月
					</li>
					<li @click="handleShowToday">
						今天
					</li>
					<li @click="handleShowLastMonth">
						上个月
					</li>
				</ul>
			</div>
		</div>
		<div class="date_wrap" @touchstart="onTouchStart" @touchend="onTouchEnd">
			<ul class="week">
				<li>
					周日
				</li>
				<li>
					周一
				</li>
				<li>
					周二
				</li>
				<li>
					周三
				</li>
				<li>
					周四
				</li>
				<li>
					周五
				</li>
				<li>
					周六
				</li>
			</ul>
			<ul class="day">
				<li v-for="(item,index) in days" :key=index :class="[{now:nowLi==year.toString()+month.toString()+item}, {do: selectDo(item)}, {last: laseDays(index)}]">
					{{item}}
				</li>
			</ul>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'calender',
		data() {
			return {
				year: '',
				month: '',
				days: [], // 当月节点
				last: 0, // 上个月节点数
				next: 0, // 下个月节点数
				nowLi: '',
				doLi: [5, 6, 7, 8, 9, 11, 13, 14], // 当月选中的节点
				touchStartX: '', // 触摸开始的x坐标
				touchStartTime: '' // 触摸开始的时间
			}
		},
		methods: {
			//控制当前日期显示特殊样式
			handleShowDateStyle() {
				let now = new Date()
				this.nowLi = now.getFullYear().toString() + (now.getMonth() + 1).toString() + now.getDate().toString()
				console.log(this.nowLi)
			},
			//得到当前年这个月分有多少天
			getDays(Y, M) {
				let day = new Date(Y, M, 0).getDate()
				return day;
			},
			// 判断当日是否打卡
			selectDo(item) {
				if (this.doLi.indexOf(item) > -1) {
					return true
				} else {
					return false
				}
			},
			// 判断是否非本月
			laseDays(index) {
				if (index < this.last) {
					return true
				} else if (index > this.days.length - this.next - 1) {
					return true
				} else {
					return false
				}
			},
			//得到当前年，这个月的一号是周几
			getWeek(Y, M) {
				let now = new Date()
				now.setFullYear(this.year)
				now.setMonth(this.month - 1)
				now.setDate(1);
				let week = now.getDay();
				return week;
			},
			pushDays() {
				//将这个月多少天加入数组days
				for (let i = 1; i <= this.getDays(this.year, this.month); i++) {
					this.days.push(i)
				}
				//将下个月要显示的天数加入days
				for (let i = 1; i <= 42 - this.getDays(this.year, this.month) - this.getWeek(this.year, this.month); i++) {
					this.next++
					this.days.push(i)
				}
				//将上个月要显示的天数加入days
				for (let i = 0; i < this.getWeek(this.year, this.month); i++) {
					var lastMonthDays = this.getDays(this.year, this.month - 1)
					this.last++
					this.days.unshift(lastMonthDays - i)
				}
				console.log(this.days)
				console.log(this.getWeek(this.year, this.month))
			},
			getDate() {
				let now = new Date();
				this.year = now.getFullYear();
				this.month = now.getMonth() + 1;
				this.pushDays();

			},
			changeDate() {

			},
			handleShowNextMonth() {
				this.days = [];
				this.last = 0;
				this.next = 0;
				if (this.month < 12) {
					this.month = this.month + 1;
					this.pushDays();
				} else {
					this.month = this.month = 1;
					this.year = this.year + 1;
					this.pushDays();
				}

			},
			handleShowToday() {
				this.days = [];
				this.last = 0;
				this.next = 0;
				let now = new Date();
				this.year = now.getFullYear();
				this.month = now.getMonth() + 1;
				this.pushDays();
			},
			handleShowLastMonth() {
				this.days = [];
				this.last = 0;
				this.next = 0;
				if (this.month > 1) {
					this.month = this.month - 1;
					this.pushDays();
				} else if (this.year > 1970) {
					this.month = 12;
					this.year = this.year - 1;
					this.pushDays();
				} else {
					alert("不能查找更远的日期")
				}
			},
			onTouchStart(e) {
				// 第一个手机触摸的x坐标
				this.touchStartX = e.changedTouches[0].clientX
				// 触摸的开始时间
				this.touchStartTime = e.timeStamp
			},
			onTouchEnd(e) {
				const offsetX = e.changedTouches[0].clientX - this.touchStartX
				// 触摸时间
				const time = e.timeStamp - this.touchStartTime
				if (time < 500 && offsetX > 40) {
					this.handleShowLastMonth()
				} else if (time < 500 && offsetX < -40) {
					this.handleShowNextMonth()
				}
			}
		},
		mounted() {
			this.getDate();
			this.handleShowDateStyle();
		}
	}
</script>
<style lang="scss" scoped>
	.calender {
		width: 750rrpx;
		position: relative;
		margin: 0 auto;
		margin-top: 50rpx;
		border: 1rpx solid #ddd;
		padding: 20rpx;
	}

	.top {
		width: 100%;
		position: relative;
		display: flex;
		border-bottom: 1rpx solid #ddd;
		padding-bottom: 20rpx;
	}

	.top_date {
		width: 300rpx;
		height: 62rpx;
		font-size: 42rpx;
		text-align: left;
		line-height: 62rpx;
	}

	.btn_wrap {
		flex: 1;
		text-align: right
	}

	.btn_wrap ul {
		display: flex;
		flex-direction: row-reverse
	}

	.btn_wrap ul li {
		padding: 10rpx 20rpx;
		border: 1rpx solid #ddd;
		font-size: 28rpx;
		line-height: 40rpx;
		cursor: pointer;
	}

	.btn_wrap ul li:first-child {
		border-left: none;
		border-radius: 0 20rpx 20rpx 0;
	}

	.btn_wrap ul li:last-child {
		border-right: none;
		border-radius: 20rpx 0 0 20rpx;
	}

	.date_wrap {
		position: relative;
	}

	.week {
		display: flex;
		flex-direction: row;
		padding: 20rpx;
		font-size: 32rpx;
	}

	.week li {
		width: 14.28%;
		text-align: center;
	}

	.day {
		display: flex;
		justify-content: center;
		flex-direction: row;
		padding: 0 20rpx;
		font-size: 26rpx;
		flex-wrap: wrap;
	}

	.day li {
		text-align: center;
		width: 86rpx;
		height: 86rpx;
		line-height: 86rpx;
		margin: 5rpx;
		box-sizing: border-box;
		border-radius: 50%;
		border: 1rpx solid #ddd;
	}

	.now {
		background: #f2f8fe;
		color: #1989fa;
		border: 1rpx solid #377cf6 !important;
	}

	.do {
		color: #fff;
		background-color: #18B566;
	}

	.last {
		color: #000;
		background-color: #eee;
	}
</style>
