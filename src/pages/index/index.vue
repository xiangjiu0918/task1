<template>
	<view class="content" 
		:style="{width:windowWidth}"
		onselectstart="return false" 
		@mouseup="mouseup">
		<view
			class="btnwrap"
			style="z-index: 10;background: linear-gradient(to right,#E0E4CC,90%,rgba(0,0,0,0));"
			@click="handleLeft"
			>
			<img src="../../static/arrow_left.png">
		</view>
		<view
			class="flex_box"
			:style="{width:flexBoxWidth}"
		>
			<view
				v-for="index in 7"
				:key="index"
				:style="[myPos,{marginLeft:index===1?'150px':''}]"
				@click="handleClick(index)"
				@tap="handleClick(index)"
				@mousedown="mousedown"
				@touchStart="touchStart"
				>
				<view
					class="bgc_container"
				>
					<view class="bgc-circle" :class="{'bgc-checked':checkIdx==index}">
						<view
							class="bgc"
							:style="[{background:'url('+`../../static/${index}.png`+')'},bgcModel]"
						/>
					</view>
					<img
					class="img_cls"
					:src="`../../static/${index}.png`" alt="a random avatar picture"
					:class="{checked:checkIdx==index}"
					/>
				</view>
			</view>
		</view>
		<view
			class="btnwrap"
			style="z-index: 10;right: 0;background: linear-gradient(to left,#E0E4CC,90%,rgba(0,0,0,0));"
			@click="handleRight"
			>
			<img src="../../static/arrow_right.png">
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				x:0,
				y:0,
				num:10,
				flag:0,
				checkIdx:1,//选中元素的下标
				windowWidth:0,
				imgWidth:0,
				isclick:false
			}
		},
		onLoad() {
			uni.getSystemInfo({success:
			res=>{
				this.windowWidth=res.windowWidth
			}}),
			uni.onWindowResize(res=>{
				this.windowWidth=res.size.windowWidth
				this.getImgWidth();
			})
		},
		methods: {
			handleLeft(){
				this.flag-=320
				this.checkIdx=this.checkIdx+1
			},
			handleRight(){
				this.flag+=320
				this.checkIdx=this.checkIdx-1
			},
			getImgWidth(){
				const query = uni.createSelectorQuery().in(this);
				query.selectAll('.img_cls').boundingClientRect(data=>{
					this.imgWidth=data[1].width
				}).exec()
				console.log("resize")
			},
			handleClick(index){
				console.log("click!!")
				console.log("checkIdx",this.checkIdx,"index",index)
				this.flag+=320*(this.checkIdx-index)
				this.checkIdx=index
				this.isclick=true
			},
			mousedown(event){
				console.log(event)
				const x=event.clientX
				let endx
				console.log("this.x:",x)
				const mouseLisener=function(event){
					endx=event.clientX
				}
				this.mouseLisener=document.addEventListener('mousemove', mouseLisener);
				setTimeout(()=>{
					document.removeEventListener('mousemove',mouseLisener);
					if(endx-x>0&&!this.isclick){
						this.flag+=320
						this.checkIdx--
					}
					else if(endx-x<0&&!this.isclick){
						this.flag-=320
						this.checkIdx++
					}
					this.isclick=false
				},300)
			},
			mouseup(event){
				console.log(event)
				document.removeEventListener('mousemove',this.mouseLisener);
			},
			touchStart(event){
				console.log(event)
				const x=event.changedTouches[0].clientX
				let endx
				console.log("this.x:",x)
				const mouseLisener=function(event){
					endx=event.changedTouches[0].clientX
				}
				this.mouseLisener=document.addEventListener('touchmove', mouseLisener);
				setTimeout(()=>{
					document.removeEventListener('touchmove',mouseLisener);
					if(endx-x>0&&!this.isclick){
						this.flag+=320
						this.checkIdx--
					}
					else if(endx-x<0&&!this.isclick){
						this.flag-=320
						this.checkIdx++
					}
					this.isclick=false
				},300)
			},
		},
		computed:{
			myPos(){
				return {
					transform:`translateX(${this.flag}px)`,
					transition: '.5s'
				}
			},
			flexBoxWidth(){
				console.log("imgwidth:",this.imgWidth)
				console.log("boxwith:",this.imgWidth*1.3+this.imgWidth*(this.num-1))
				return `${this.imgWidth*this.num}px`
			},
			bgcModel(){
				return {
					filter:'blur(50px)',
					backgroundSize:'cover',
					transform:'scale(2)'
				}
			}
		},
		mounted(){
			this.getImgWidth();
			
		},
		watch:{
			windowWidth(){
				console.log("windowsWidth:",this.windowWidth)
				this.num=parseInt((this.windowWidth-160)/320)
				console.log("num:",this.num)
			}
		}
	}
</script>

<style>
	.bgc_container {
		--s: 280px; /* image size */
		--b: 20px; /* border thickness */
		--c: #fffefe; /* border color */
		--f: 1; /* initial scale */
		/* background: 
		radial-gradient(
		circle closest-side,
		#ECD078 calc(99% - var(--b)),var(--c) calc(100% - var(--b)) 99%,#0000
		) var(--_g); */
		width: 320px;
		height: 500px;
		position: relative;
		display: flex;
		align-items: end;
		justify-content: center;
		overflow: hidden;
	}
	.bgc-circle {
		height: 280px;
		width: 280px;
		box-sizing: border-box;
		border-radius: var(--s);
		border: #fff solid 20px;
		position: absolute;
		left: 20px;
		right: 20px;
		bottom: 20px;
		top:180px;
	}
	.bgc {
		--s: 280px; /* image size */
		--b: 20px; /* border thickness */
		--c: #fffefe; /* border color */
		--f: 1; /* initial scale */
		--_g: 50%/calc(100%/var(--f)) 100% no-repeat content-box;
		--_o: calc((1/var(--f) - 1)*var(--s)/2 - var(--b));
		height: var(--s);
		width: var(--s);
		border-radius: var(--s);
		position: absolute;
		left: -20px;
		right: 0;
		bottom: 0;
		top: -20px;
		overflow: hidden;
		-webkit-mask:
		radial-gradient(circle 70px,#000 99%,#0000) var(--_g);
		z-index: -1;
		}
	.bgc-checked {
		outline: var(--b) solid #ECD078;
	}
	.img_cls {
	--s: 280px; /* image size */
	--b: 20px; /* border thickness */
	--c: #fffefe; /* border color */
	--f: 1; /* initial scale */
	
	width: var(--s);
	aspect-ratio: 1;
	padding: calc(var(--s)/5) 30px 0;
	cursor: pointer;
	border-radius: 0 0 999px 999px;
	--_g: 50%/calc(100%/var(--f)) 100% no-repeat content-box;
	--_o: calc((1/var(--f) - 1)*var(--s)/2 - var(--b));
	outline: var(--b) solid var(--c);
	outline-offset: var(--_o);
	/* background: 
		radial-gradient(
		circle closest-side,
		#ECD078 calc(99% - var(--b)),var(--c) calc(100% - var(--b)) 99%,#0000
		) var(--_g); */
	-webkit-mask:
		linear-gradient(#000 0 0) no-repeat
		50% calc(1px - var(--_o)) / calc(100%/var(--f) - 2*var(--b) - 2px) 50%,
		radial-gradient(circle closest-side,#000 99%,#0000) var(--_g);
	transform: scale(var(--f));
	transition: .5s;
	-webkit-user-select: none;
	-moz-user-select: none;
	-o-user-select: none;
	user-select: none;
	z-index: 10;
	margin-bottom: 40px;
	}
	
	.checked {
	/* hover scale */
	--f: 1.1;
	transform: scale(var(--f));
	transition: .5s;
	}


	.content {
	margin: 0;
	min-height: 100vh;
	display: flex;
	align-items: center;
	background: #E0E4CC;
	}

	.btnwrap {
		position: absolute;
		height: 100vh;
		width: 150px;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.flex_box {
		display: flex;
	}

	button {
		border: 0px !important;
		background: none;
	}

</style>
