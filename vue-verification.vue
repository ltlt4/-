<template>
    <div id="vueVer">
        <div id="v_container">
            <canvas :id="canvasId" :width="width" :height="height" @click="refresh">{{innerHTML}}</canvas>
        </div>
        <input type="text" id="code_input" v-model='Vercode' placeholder="请输入验证码" @blur="validate(Vercode)" :class="{large:!show,small:show}"/>
    </div>
</template>
<script>
    export default {
        props: {
            uid: {  //容器id
                default: ""
            },
            canvasId: { //canvas的ID
                default: "verifyCanvas"
            },
            width: {  //canvas默认宽度
                default: "150"
            },
            height: {  //canvas默认高度
                default: "40"
            },
            utype: {  //图形验证码默认类型blend:数字字母混合类型、number:纯数字、letter:纯字母
                default: "blend"
            },
            num: {  //验证码长度
                default: 4
            },
            appce:{  //输入框样式
                default:"small"
            },        
          register:{ //是否刷新验证码
               default:true
           }
        },
        data() {
            return {
                code: "",
                numArr: ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9'],
                letterArr: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'],
                innerHTML: "你的浏览器版本不支持canvas",
                Vercode: "",
                show:true
            }
        },
        computed: {
            uinput: function () {
                return this.input
            }
        },
        methods: {
            //是否刷新验证码
            freshen(){
              if(this.register){
                  this.refresh()
              }
            },
            //生成一个随机数
            randomNum(min, max) {
                return Math.floor(Math.random() * (max - min) + min);
            },
            //生成一个随机颜色
            randomColor(min, max) {
                var r = this.randomNum(min, max);
                var g = this.randomNum(min, max);
                var b = this.randomNum(min, max);
                return "rgb(" + r + "," + g + "," + b + ")";
            },
            //生成验证码
            refresh() {
                var canvas = document.getElementById(this.canvasId)
                if (canvas.getContext) {
                    var ctx = canvas.getContext('2d');
                } else {
                    return;
                }

                ctx.textBaseline = "middle";
                ctx.fillStyle = this.randomColor(180, 240);
                ctx.fillRect(0, 0, this.width, this.height);

                if (this.utype == "blend") { //判断验证码类型
                    var txtArr = this.numArr.concat(this.letterArr);
                } else if (this.utype == "number") {
                    var txtArr = this.numArr;
                } else {
                    var txtArr = this.letterArr;
                }
                var a=[]
                for (var i = 1; i <= this.num; i++) {
                    var txt = txtArr[this.randomNum(0, txtArr.length)];
                    a.push(txt)
                    ctx.font = this.randomNum(this.height / 2, this.height) + 'px SimHei'; //随机生成字体大小
                    ctx.fillStyle = this.randomColor(50, 160); //随机生成字体颜色        
                    ctx.shadowOffsetX = this.randomNum(-3, 3);
                    ctx.shadowOffsetY = this.randomNum(-3, 3);
                    ctx.shadowBlur = this.randomNum(-3, 3);
                    ctx.shadowColor = "rgba(0, 0, 0, 0.3)";
                    var x = this.width / 5 * i;
                    var y = this.height / 2;
                    var deg = this.randomNum(-30, 30);
                    /**设置旋转角度和坐标原点**/
                    ctx.translate(x, y);
                    ctx.rotate(deg * Math.PI / 180);
                    ctx.fillText(txt, 0, 0);
                    /**恢复旋转角度和坐标原点**/
                    ctx.rotate(-deg * Math.PI / 180);
                    ctx.translate(-x, -y);
                }
                     if(a.length>4){
                        a.splice(0,4)
                     }
                     this.code=a.join("")
                /**绘制干扰线**/
                for (var i = 0; i < this.num; i++) {
                    ctx.strokeStyle = this.randomColor(40, 180);
                    ctx.beginPath();
                    ctx.moveTo(this.randomNum(0, this.width), this.randomNum(0, this.height));
                    ctx.lineTo(this.randomNum(0, this.width), this.randomNum(0, this.height));
                    ctx.stroke();
                }

                /**绘制干扰点**/
                for (var i = 0; i < this.width / 4; i++) {
                    ctx.fillStyle = this.randomColor(0, 255);
                    ctx.beginPath();
                    ctx.arc(this.randomNum(0, this.width), this.randomNum(0, this.height), 1, 0,
                        2 * Math.PI);
                    ctx.fill();
                }
            },
            validate(ver) {
                var code = ver.toLowerCase();
                var v_code = this.code.toLowerCase();
                if (code == v_code) {
                    var  chunk=true;
                }else {
                    var chunk=false;
                }
                this.$emit('verByValue',chunk)
            }
        },
        created() {
            if(this.appce=="large"){
                this.show=false
            }else{
                this.show=true
            }
        },
        mounted() {
            this.refresh()
        },
    }
</script>
<style lang="">
    #v_container {
        display: inline-block;
        vertical-align: middle;
    }
    #v_container>canvas{
        cursor: pointer;
    }
    #vueVer .small{
       width: 200px;
       height: 40px;
       border: #d3d3d3 1px solid;
       border-radius: 4px;
       font-size: 14px
    }
    #vueVer .large{
       width: 350px;
       height: 50px;
       border: #d3d3d3 1px solid;
       border-radius: 4px;
       font-size: 14px
    }
    #vueVer input::-webkit-input-placeholder {
        color: #d3d3d3
    }
    #vueVer input:-moz-placeholder{
        color: #d3d3d3
    }
    #vueVer input::-moz-placeholder{
        color: #d3d3d3
    } 
    #vueVer input:-ms-input-placeholder{
        color: #d3d3d3
    }
</style>