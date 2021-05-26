<template>
	<view class="progress_box" :style="{ 'background-color': pageBg }">
		<!-- #ifdef MP-ALIPAY -->
		<canvas
			:id="id"
			:style="{ width: width + 'px', height: width + 'px' }"
			:disable-scroll="isMove"
			@touchstart="touchStart"
			@touchmove="touchMove"
			@touchend="touchEnd"
		></canvas>
		<!-- #endif -->
		<!-- #ifndef MP-ALIPAY -->
		<canvas
			:canvas-id="id"
			:style="{ width: width + 'px', height: width + 'px' }"
			:disable-scroll="isMove"
			@touchstart="touchStart"
			@touchmove="touchMove"
			@touchend="touchEnd"
		></canvas>
		<!-- #endif -->
		<view class="p_ps_show" v-if="!$slots.default">
			<view class="ps_text plan" :style="{'color': colorEnd}">
				<text>{{circleTotal}}</text>
				<text>Puffs</text>
			</view>
			<view class="ps_text his" v-if="isTwoVal">
				<text>{{twoVal}}</text>
				<text>Puffs</text>
			</view>
		</view>
		<view v-else class="p_ps_show">
			<slot></slot>
		</view>
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
		val: {
			default: 0
		},
		twoVal: {
			default: 0
		},
		isTwoVal: {
			default: true
		},
		// 步进值
		step: {
			default: 5
		},
		// 宽度
		width: {
			default: 220
		},
		// 圆的宽度
		border: {
			default: 35
		},
		colorSatrt: {
			default: '#FFD2E0'
		},
		// 圆的颜色
		colorEnd: {
			default: '#ff0163'
		},
		// 圆的外层的颜色
		colorButton: {
			default: '#ffffff'
		},
		// 背景色
		pageBg: {
			default: '#ffffff'
		},
		// 一圈的值
		circle: {
			default: 300
		}
	},
	data() {
		return {
			oldTouches: {},
			valData: 0,
			circleNum: 0,
			centerColor: '',
			valPoint: {},
			centerPoint: {},
			isMove: false
		};
	},
	mounted() {
		this.circleNum = Math.floor(this.val / this.circle)
		this.valData = this.val % this.circle
		const gradientList = this.gradient(this.colorSatrt, this.colorEnd, 3)
		this.centerColor = gradientList ? gradientList[1] : '#ff8cb6'
		this.setDrawCircle(this.valData)
	},
	computed: {
		circleTotal() {
			return this.circleNum * this.circle + this.valData
		}
	},
	watch: {
		val(val) {
			this.circleNum = Math.floor(val / this.circle)
			this.valData = val % this.circle
			this.setDrawCircle(this.valData)
		}
	},
	methods: {
		setDrawCircle(val) {
			this.valData = val
			this.drawCircle(this.valData, this.width, this.border, this.colorSatrt, this.colorEnd, this.colorButton);
		},
		drawCircle(val = 0, width, border, colorSatrt, colorEnd, colorButton) {
			let radius = width / 2 - 20; //半径
			let centerPoint = {
				//坐标
				x: width / 2,
				y: width / 2,
				r: radius
			};
			this.centerPoint = centerPoint;
			let ctx = uni.createCanvasContext(this.id, this);
			if (this.circleNum <= 0) {
				// 圆的颜色
				ctx.setStrokeStyle(colorSatrt);
				// 开始绘制圆形
				ctx.beginPath();
				// 设置圆的宽度
				ctx.setLineWidth(border + 4);
				// 绘制一个圆形
				ctx.arc(centerPoint.x, centerPoint.y, radius, 0, 2 * Math.PI, false);
				ctx.stroke();
				ctx.closePath();
			}
			
			const circleb = (this.circle/2)
			const overOneRound = this.circleNum >= 1
			let arcVal = val
			let orign = [centerPoint.x, centerPoint.y]
			if (overOneRound) {
				arcVal = 300
				ctx.save()
				ctx.translate(centerPoint.x, centerPoint.y)
				ctx.rotate(val / this.circle * 2 * Math.PI)
				orign = [0, 0]
			}
			// 绘制渐变
			ctx.beginPath();
			// 设置圆的宽度
			ctx.setLineWidth(border + 4);
			ctx.setLineCap('round')
			let b = ctx.createLinearGradient(0,0,0,orign[1]+radius);  //创建渐变对象  渐变开始点和渐变结束点
		 	b.addColorStop(0, colorSatrt); //渐变开始值
		 	b.addColorStop(1, this.centerColor); //渐变结束值
		 	ctx.strokeStyle = b;     //使用渐变对象作为圆环的颜色
			ctx.arc(orign[0], orign[1], radius, 1.5*Math.PI, (1.5 + arcVal / circleb) * Math.PI, false);
			ctx.stroke();
			ctx.closePath();
			if (arcVal > circleb) {
				ctx.beginPath();
				ctx.setLineCap('round')
				ctx.setLineWidth(border + 4);
				let g = ctx.createLinearGradient(0,orign[1]+radius,0,0);
			 	g.addColorStop(0, this.centerColor);
			 	g.addColorStop(1, colorEnd);
			 	ctx.strokeStyle = g;
				ctx.arc(orign[0], orign[1], radius, 0.5*Math.PI, (0.5+(arcVal - circleb) / circleb) * Math.PI, false);
				ctx.stroke();
				ctx.closePath();
			}
			if (overOneRound) {
				ctx.restore()
			}

			//画控制点
			let valRadius = border + 1; //点的半径
			// 点的半径
			let valRadian = 0;
			val -= circleb - circleb/2
			let valPoint = {
				x: centerPoint.x + radius * Math.cos((val / circleb) * Math.PI),
				y: centerPoint.y + radius * Math.sin((val / circleb) * Math.PI),
				r: valRadius,
				v: val,
				s: 0.75 + valRadian,
				e: 2.25 - valRadian,
				t: 1.5 - 2 * valRadian,
				n: val
			};
			// console.log(valPoint)
			// console.log('centerPoint',centerPoint)
			this.valPoint = valPoint;
			// 点最外层
			ctx.beginPath();
			ctx.setFillStyle(colorButton);
			ctx.arc(valPoint.x, valPoint.y, valPoint.r, 0, 2 * Math.PI, false);
			ctx.fill()
			// 点内部的颜色
			ctx.beginPath();
			ctx.setFillStyle(colorEnd);
			ctx.arc(valPoint.x, valPoint.y, valPoint.r - 2, 0, 2 * Math.PI, false);
			ctx.fill();
			ctx.closePath();
			// 绘制箭头
			ctx.translate(valPoint.x, valPoint.y)
			ctx.rotate(val/this.circle * 2 * Math.PI)
			ctx.beginPath();
			ctx.setLineCap('round')
			ctx.setLineWidth(3)
			ctx.setStrokeStyle('#FF99C1')
			ctx.moveTo(0, 4);
			ctx.lineTo(-5, -2);
			ctx.stroke()
			ctx.moveTo(0, 4);
			ctx.lineTo(5, -2);
			ctx.stroke()
			ctx.closePath();
      ctx.setTransform(1, 0, 0, 1, 0, 0)
			ctx.draw();
		},
		// 获取两个颜色之间渐变的色值
		gradient(startColor,endColor,step) {
			function rgbToHex(r, g, b) {
				let hex = ((r<<16) | (g<<8) | b).toString(16);
				return "#" + new Array(Math.abs(hex.length-7)).join("0") + hex;
			}
			function hexToRgb(hex) {
				let rgb = [];
				for(let i=1; i<7; i+=2){
					rgb.push(parseInt("0x" + hex.slice(i,i+2)));
				}
				return rgb;
			}
			//将hex转换为rgb
			let sColor = hexToRgb(startColor);
			let	eColor = hexToRgb(endColor);
			//计算R\G\B每一步的差值
			let rStep = (eColor[0] - sColor[0]) / step;
			let gStep = (eColor[1] - sColor[1]) / step;
			let bStep = (eColor[2] - sColor[2]) / step;
			let gradientColorArr = [];
			for(let i=0;i<step;i++){
					//计算每一步的hex值
					gradientColorArr.push(rgbToHex(parseInt(rStep*i+sColor[0]),parseInt(gStep*i+sColor[1]),parseInt(bStep*i+sColor[2])));
			}
			return gradientColorArr;
		},
		touchStart(e) {
			let touches = e.mp.changedTouches[0] || e.changedTouches[0];
			this.oldTouches = {
				x: touches.x,
				y: touches.y
			};
			if (isInCtrl(touches, this.valPoint)) {
				this.isMove = true;
			}
		},
		touchMove(e) {
			const oldTouches = this.oldTouches;
			const touches = e.mp.changedTouches[0] || e.changedTouches[0];
			if (this.isMove === true) {
				let angle = Math.atan2(this.centerPoint.y - touches.y, this.centerPoint.x - touches.x) / Math.PI;
				let degrees = angle * 180 - 90 // -180 ~ 180
				// 判断是加还是减
				let numVal = 0
				if (degrees >= 0 && degrees <= 180) {
					numVal = Math.round(degrees * ((this.circle/2) / 180))
				} else {
					numVal = Math.round((360 + degrees) * ((this.circle/2) / 180))
				}
				if (numVal<this.circle/30 && this.valData>this.circle-this.circle/30) {
					this.circleNum += 1
				} else if ((numVal - this.valData) >= (this.circle/2)) {
					this.circleNum -= 1
				}
				this.valData = numVal
				if (this.circleNum<0) {
					this.circleNum = 0
					this.valData = 0
				}
				this.setDrawCircle(this.valData)
			}
		},
		touchEnd(e) {
			if (this.isMove) {
				this.$emit('change', this.circleTotal);
			}
			this.isMove = false;
		}
	}
};
</script>

<style scoped lang="scss">
.progress_box{
	position: relative;
	.p_ps_show {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		display: flex;
		flex-direction: column;
		align-items: center;
		.ps_text{
			display: flex;
			flex-direction: column;
			align-items: center;
			font-size: 60rpx;
			padding: 0 20rpx;
			&.plan{
				border-bottom: solid 1rpx #acacac;
				padding-bottom: 8rpx;
			}
			&.his{
				color: #acacac;
			}
			text{
				&:first-child{
					font-weight: bold;
				}
				&:last-child{
					font-size: 30rpx;
				}
			}
		}
	}
}
</style>