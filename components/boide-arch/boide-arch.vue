<template>
	<view class="progress_box" :style="{ 'background-color': pageBg }">
		<!-- #ifdef MP-ALIPAY -->
		<canvas
			:id="id"
			:style="{ width: width + 'px', height: width / 2 + 'px' }"
			disable-scroll="true"
			@touchstart="touchStart"
			@touchmove="touchMove"
			@touchend="touchEnd"
		></canvas>
		<!-- #endif -->
		<!-- #ifndef MP-ALIPAY -->
		<canvas
			:canvas-id="id"
			:style="{ width: width + 'px', height: width / 2 + 'px' }"
			disable-scroll="true"
			@touchstart="touchStart"
			@touchmove="touchMove"
			@touchend="touchEnd"
		></canvas>
		<!-- #endif -->
		<slot></slot>
	</view>
</template>

<script>
function isInArea(e, r) {
	return Math.pow(e.x - r.x, 2) + Math.pow(e.y - r.y, 2) <= Math.pow(r.r + 10, 2);
}
function isInCtrl(e, r) {
	if (isInArea(e, r)) {
		return true;
	} else {
		return false;
	}
}
export default {
	props: {
		id: {
			default: 'canvas'
		},
		lock: {
			default: false
		},
		val: {
			default: 0
		},
		twoVal: {
			default: 0
		},
		max: {
			default: 3000
		},
		step: {
			default: 50
		},
		width: {
			default: 220
		},
		border: {
			default: 20
		},
		colorSatrt: {
			default: '#B8DFD0'
		},
		colorEnd: {
			default: '#00a968'
		},
		filletColor: {
			default: '#FFA900'
		},
		pageBg: {
			default: '#FFFFFF'
		},
		colorButton: {
			default: '#FFFFFF'
		}
	},
	data() {
		return {
			numVal: 0,
			valData: 0,
			val1Data: 0,
			valPoint: {},
			centerPoint: {},
			isMove: false
		};
	},
	watch: {
		lock(newVal) {
			this.lockData = newVal;
			this.setDrawCircle();
		},
		val(newVal) {
			this.valData = newVal / (this.max / 180);
			this.numVal = newVal;
			this.setDrawCircle();
		},
		twoVal(newVal) {
			this.val1Data = newVal / (this.max / 180);
			this.setDrawCircle();
		},
		max(newVal) {
			this.val1Data = this.twoVal / (newVal / 180);
			this.valData = this.val / (newVal / 180);
			this.setDrawCircle();
		}
	},
	mounted() {
		this.val1Data = this.twoVal / (this.max / 180);
		this.valData = this.val / (this.max / 180);
		this.numVal = this.val;
		this.lockData = this.lock;
		this.setDrawCircle();
	},
	methods: {
		setDrawCircle() {
			this.drawCircle(this.valData, this.val1Data, this.width, this.lockData);
		},
		drawCircle(val, twoVal, width, lock) {
			let radius = width / 2 - 40; //半径
			let centerPoint = {
				//坐标
				x: width / 2,
				y: width / 2 - 10 - 10,
				r: radius
			};
			this.centerPoint = centerPoint;
			let ctx = uni.createCanvasContext(this.id, this);
			ctx.setLineCap('round');

			//画外圆
			ctx.setLineWidth(this.border);
			ctx.setStrokeStyle(this.colorSatrt); //0~-1
			ctx.beginPath();
			ctx.arc(centerPoint.x, centerPoint.y, radius, 1 * Math.PI, 2 * Math.PI, false);
			ctx.stroke();
			ctx.closePath();

			ctx.setStrokeStyle(this.colorEnd);
			ctx.beginPath();
			ctx.arc(centerPoint.x, centerPoint.y, radius, 1 * Math.PI, (1 + val / 180) * Math.PI, false);
			ctx.stroke();
			ctx.closePath();

			//画内圆
			ctx.setLineWidth(7);
			ctx.setStrokeStyle(this.filletColor); //0~-1
			ctx.beginPath();
			twoVal = twoVal > 180 ? 180 : twoVal;
			ctx.arc(centerPoint.x, centerPoint.y, radius - 20, 1 * Math.PI, (1 + twoVal / 180) * Math.PI, false);
			ctx.stroke();
			ctx.closePath();
			//画控制点
			ctx.beginPath();
			ctx.setFillStyle(this.colorButton);
			//控制点阴影效果，不需要可以删掉
			let valRadius = 10; //点的半径
			// 小圆的半径/
			let valRadian = 0;
			val -= 90;
			let valPoint = {
				x: centerPoint.x + radius * Math.sin((val / 180) * Math.PI),
				y: centerPoint.y - radius * Math.cos((val / 180) * Math.PI),
				r: valRadius
			};

			this.fonts(this.numVal, ctx, valPoint.x, valPoint.y, val - 1, centerPoint.x, centerPoint.y, radius);
			this.fonts1(this.twoVal, ctx, valPoint.x, valPoint.y, twoVal - 75, centerPoint.x, centerPoint.y, radius - 35);
			ctx.fill();

			ctx.beginPath();
			this.valPoint = valPoint;
			lock || ctx.arc(valPoint.x, valPoint.y, valPoint.r, 0, 2 * Math.PI, false);
			ctx.closePath();
			ctx.fillStyle = '#fff';
			ctx.fill();
			ctx.draw();
		},
		// Remindingg Puffs
		fonts(val, ctx, x, y, rotate, x1, y1, radius) {
			const isNumHalf = val > this.max / 2;
			val = val + 'puffs';
			val = isNumHalf
				? val
						.toString()
						.split('')
						.reverse()
				: val.toString().split('');
			ctx.beginPath();
			// 设置字体
			ctx.font = '14px bold 黑体';
			// 设置颜色
			ctx.fillStyle = isNumHalf ? '#ffffff' : '#00a968';
			// 设置水平对齐方式
			ctx.textAlign = 'center';
			// 设置垂直对齐方式
			ctx.textBaseline = 'middle';

			let x2, y2;
			if (isNumHalf) {
				rotate -= 5;
			} else {
				rotate += 7;
			}
			val.every((v, k) => {
				let newRadius = radius;
				ctx.save();
				if (isNumHalf ? k < 4 : 'uffs'.indexOf(v) !== -1) {
					ctx.font = '8px 黑体';
					if (isNumHalf) {
						rotate -= 2.6;
					} else {
						rotate += 2.6;
					}
					newRadius -= 2;
				} else if (isNumHalf ? k == 4 : v === 'p') {
					if (isNumHalf) {
						rotate -= 3.5;
					} else {
						rotate += 3.5;
					}
					newRadius -= 2;
					ctx.font = '8px 黑体';
				} else {
					// 设置字体
					ctx.font = '18px bold 黑体';
					if (isNumHalf) {
						rotate -= 4.6;
					} else {
						rotate += 4.6;
					}
				}
				// 绘制文字（参数：要写的字，x坐标，y坐标）
				let x2 = x1 + newRadius * Math.sin((rotate / 180) * Math.PI);
				let y2 = y1 - newRadius * Math.cos((rotate / 180) * Math.PI);
				ctx.translate(x2, y2);
				// 绘制文字（参数：要写的字，x坐标，y坐标）
				ctx.rotate((rotate / 180) * Math.PI);
				ctx.translate(-x2, -y2);
				ctx.fillText(v, x2, y2);
				ctx.restore();
				return true;
			});
			let i = 180;
			ctx.restore();
			ctx.closePath();
		},
		// Actual puffs
		fonts1(val, ctx, x, y, rotate, x1, y1, radius) {
			const isNumHalf = val > this.max + 200;
			val = val + 'puffs';
			val = val
				.toString()
				.split('')
				.reverse();
			ctx.beginPath();
			// 设置字体
			ctx.font = '12px bold 黑体';
			// 设置颜色
			ctx.fillStyle = '#FFA800';
			// 设置水平对齐方式
			ctx.textAlign = 'center';
			// 设置垂直对齐方式
			ctx.textBaseline = 'middle';

			// 绘制文字（参数：要写的字，x坐标，y坐标）
			let x2, y2;
			radius -= 2;
			rotate += isNumHalf ? 10 : 0;
			val.every((v, k) => {
				ctx.save();
				if (k < 4) {
					ctx.font = '8px 黑体';
					rotate -= 3.8;
				} else if (k == 4) {
					rotate -= 5;
					ctx.font = '8px 黑体';
				} else {
					rotate -= 6;
				}
				x2 = x1 + radius * Math.sin((rotate / 180) * Math.PI);
				y2 = y1 - radius * Math.cos((rotate / 180) * Math.PI);
				ctx.translate(x2, y2);
				// 绘制文字（参数：要写的字，x坐标，y坐标）
				ctx.rotate((rotate / 180) * Math.PI);
				ctx.translate(-x2, -y2);
				ctx.fillText(v, x2, y2);
				ctx.restore();
				return true;
			});
			let i = 180;
			ctx.restore();
			ctx.closePath();
		},
		touchStart(e) {
			if (this.lockData) {
				return false;
			}
			let touches = e.mp.changedTouches[0] || e.changedTouches[0];
			if (isInCtrl(touches, this.valPoint)) {
				this.isMove = true;
			}
		},
		touchMove(e) {
			if (this.lockData) {
				return false;
			}
			let touches = e.mp.changedTouches[0] || e.changedTouches[0];
			if (this.isMove === true) {
				let angle = Math.atan2(this.centerPoint.y - touches.y, touches.x - this.centerPoint.x);
				let nweVal;
				nweVal = (1 - angle / Math.PI) * 180; //1-0     // 0~-180;
				if (nweVal > 180 && nweVal < 200) {
					nweVal = 180;
				} else if (nweVal < 360 && nweVal > 300) {
					nweVal = 0;
				} else if (nweVal > 180) {
					nweVal = 180;
				}
				this.valData = nweVal;
				// 滑动步进值，50
				this.numVal = Math.round((this.valData * (this.max / 180)) / this.step) * this.step;
				this.setDrawCircle();
			} else if (this.isMove1 === true) {
				let angle = Math.atan2(this.centerPoint.y - touches.y, touches.x - this.centerPoint.x);
				let nweVal;
				if (angle / Math.PI > 0) {
					nweVal = (1 - angle / Math.PI) * 180; //1-0     // 0~-180;
					this.val1Data = nweVal;
					this.setDrawCircle();
				}
			}
		},
		touchEnd(e) {
			if (this.lockData) {
				return false;
			}
			if (this.isMove || this.isMove1) {
				this.numVal = Math.round((this.valData * (this.max / 180)) / this.step) * this.step;
				this.$emit('change', this.numVal);
			}
			this.isMove1 = false;
			this.isMove = false;
		}
	}
};
</script>

<style>
.progress_box {
	position: relative;
	width: 100%;
	height: 100%;
	display: flex;
	align-items: center;
	justify-content: center;
	text-align: center;
}
</style>
