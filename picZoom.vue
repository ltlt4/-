<template>
    <div class="contint">
        <img :src="imgurl" alt="" class="magnimg">
        <div class="mask" :style="{width:width,height:height}" @mousemove="magnifier($event)" ref="elememt" @mouseout="activ=false">
        </div>
        <div v-if="activ" :style="{width:mxwidth+'px',height:myheight+'px',background:mcolor,left:mxleft,top:mytop}"
            :class="{'mask-div':activ,'mask-hide':!activ}"></div>
        <div :style="{width:magwidth+'px',height:magheight+'px'}" :class="About?'enlarge-right':'enlarge-left'" v-if="activ">
            <img :src="magurl" alt="" ref='enlarge' :style="{left:magleft,top:magtop,width:500+'px',height:500+'px'}" class="picture">
        </div>
    </div>
</template>
<script>
    export default {
        props: {
            imgurl: {  //小图图片路径
                default: null
            },
            magurl: {  //大图路径
                default: null
            },
            width: {    //遮罩层宽度 
                default: "100%"
            },
            height: {   //遮罩层高度
                default: "100%"
            },
            mxwidth: {  //移动模块宽度
                default: 150
            },
            myheight: {  //移动模块高度
                default: 150
            },
            magwidth: {
                default: 300 //放大框宽度
            },
            magheight: {
                default: 300  //放大框高度
            },
            mcolor: {     //移动模块颜色
                default: "#e9dfdf"
            },
            selace: {    //放大倍数
                default: 2
            },
            About: {     //放大图片的方向
                default: true  //true为左
            }
        },
        data() {
            return {
                activ: false,
                mxleft: 0,
                mytop: 0,
                //大图的移动
                magleft: 0,
                magtop: 0
            }
        },
        methods: {
            magnifier($event) {
                this.activ = true
                let height = this.$refs.elememt.offsetHeight; //获取遮罩层的高度
                let width = this.$refs.elememt.offsetWidth;     //获取遮罩层的宽度
                //获取鼠标距当前元素的位置
                let mx = $event.offsetX;
                let my = $event.offsetY;
                //获取滑块移动的最大距离
                let maxwidth = width - this.mxwidth;
                let maxheight = width - this.myheight;
                //滑块移动距离
                let x = mx - this.mxwidth / 2;
                let y = my - this.myheight / 2
                //设置滑块移动距离
                x = x < 0 ? 0 : x;//如果横坐标小于0就设置为0
                y = y < 0 ? 0 : y;//如果纵坐标小于0就设置为0

                //如果横坐标大于横向最大移动距离就设置为横向最大移动距离
                x = x > maxwidth ? maxwidth : x
                //如果纵坐标大于纵向最大移动距离就设置为纵向最大移动距离
                y = y > maxheight ? maxheight : y

                //设置滑块移动
                this.mxleft = x + 'px'
                this.mytop = y + 'px'

                //大图的移动距离 = 遮挡层的移动距离 * 大图的最大移动距离 / 遮挡层的最大移动距离
                setTimeout(() => {
                    //大图的最大移动距离
                    let imgMaxleft = this.$refs.enlarge.offsetWidth - this.magwidth;
                    let imgMaxtop = this.$refs.enlarge.offsetHeight - this.magheight;
                    //大图横向移动距离
                    let imgMoveLeft = x * imgMaxleft / maxwidth
                    //大图纵向移动距离
                    let imgMovetop = y * imgMaxtop / maxheight
                    //设置大图移动
                    this.magleft = -imgMoveLeft + 'px'
                    this.magtop = -imgMovetop + 'px'

                }, 10);

            }
        },
        updated() {
            //this.$refs.enlarge.style.backgroundSize="500px 500px"  //有大图路径请删除
        },
        created() {
            if (typeof (this.width) == Number) {
                this.width = this.width + 'px'
            }
        },
    }
</script>
<style lang="">
    .contint {
        position: relative;
    }

    .magnimg {
        width: 100%;
        height: 100%;
    }

    .mask {
        z-index: 1000;
        position: absolute;
        top: 0px;
        left: 0px;
        opacity: 0;
        cursor: move;
    }

    .mask-div {
        z-index: 900;
        position: absolute;
        left: 0;
        top: 0;
        opacity: 0.5;
    }

    .enlarge-right {
        position: absolute;
        z-index: 920;
        left: 440px;
        top: 0;
        border: 1px solid #ddd;
        background: no-repeat;
        overflow: hidden;
    }

    .enlarge-left {
        position: absolute;
        z-index: 920;
        left: -440px;
        top: 0;
        border: 1px solid #ddd;
        background: no-repeat;
        overflow: hidden;
    }

    .picture {
        position: relative;
    }
</style>