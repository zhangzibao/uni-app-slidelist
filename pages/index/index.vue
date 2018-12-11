<template>
    <div class="content">
        <div class="container_of_slide" v-for="(item,index) in NowMessageList" :key="index">
            <div class="slide_list"
                 @touchstart="touchStart($event,index)"
                 @touchend="touchEnd($event,index)"
                 @touchmove="touchMove($event,index)"
				 @tap="recover(index)"
                 :style="{transform:'translate3d('+item.slide_x+'px, 0, 0)'}"
            >
                <div class="now-message-info"
                     :style="{width:Screen_width+'px'}"
                >
                    <div class="imgInfo" @tap="recover(index)">
                        <image :src="item.img"></image>
                    </div>
                    <div class="centerInfo">
                        <p class="name">
                            {{item.name}}
                        </p>
                        <p class="message">
                            {{item.message}}
                        </p>
                    </div>
                    <div class="timeInfo" @tap="recover(index)">
                        <div class="time">
                            <text>{{item.time}}</text>
                        </div>
                    </div>
                </div>
                <div class="group-btn">
                    <div class="top" @tap="top(index)" v-if="item.top">
                        取消置顶
                    </div>
                    <div class="top" @tap="top(index)" v-else>
                        置顶
                    </div>
                    <div class="removeM" @tap="removeM(index)">
                        删除
                    </div>
                </div>
                <div style="clear:both"></div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'slide-list',
    computed: {
        Screen_width() {
            return uni.getSystemInfoSync().windowWidth;
        }
    },
    data() {
        return {
            start_slide_x: 0,
            btnWidth: 0,
            startX: 0,
            LastX: 0,
            startTime: 0,
            NowMessageList: [
                {
                    img: '../../static/img/1.jpg',
                    name: '狐狸叔叔',
                    message: '今晚去吃饭吗?',
                    time: '19:08',
                    top: 0,
                    slide_x: 0
                },
                {
                    img: '../../static/img/2.jpg',
                    name: '老虎爸爸',
                    message: '黑发不知勤学早，白首方悔读书迟。 —— 颜真卿《劝学诗》',
                    time: '02:08',
                    top: 0,
                    slide_x: 0
                },
                {
                    img: '../../static/img/3.jpg',
                    name: '偶遇妹子',
                    message: '忽如一夜春风来，千树万树梨花开。 —— 岑参《白雪歌送武判官归京》',
                    time: '02:08',
                    top: 0,
                    slide_x: 0
                },
                {
                    img: '../../static/img/4.jpg',
                    name: '男神',
                    message: '寂寞空庭春欲晚，梨花满地不开门。 —— 刘方平《春怨》',
                    time: '23:08',
                    top: 0,
                    slide_x: 0
                },
                {
                    img: '../../static/img/5.jpg',
                    name: '妹妹',
                    message:
                        '人生若只如初见，何事秋风悲画扇。 —— 纳兰性德《木兰词·拟古决绝词柬友》',
                    time: '02:08',
                    top: 0,
                    slide_x: 0
                }
            ]
        };
    },
    methods: {
        // 滑动开始
        touchStart(e, index) {
            //记录手指放上去的时间
            this.startTime = e.timeStamp;
            //记录滑块的初始位置
            this.start_slide_x = this.NowMessageList[index].slide_x;
            // 按钮宽度
            uni.createSelectorQuery()
                .selectAll('.group-btn')
                .boundingClientRect()
                .exec(res => {
                    if (res[0] != null) {
                        this.btnWidth = res[0][index].width * -1;
                    }
                });
            // 记录上一次开始时手指所处位置
            this.startX = e.touches[0].pageX;
            // 记录上一次手指位置
            this.lastX = this.startX;
            //初始化非当前滑动消息列的位置
            this.NowMessageList.forEach((item, eq) => {
                if (eq !== index) {
                    item.slide_x = 0;
                }
            });
        },
        // 滑动中
        touchMove(e, index) {
            const endX = e.touches[0].pageX;
            const distance = endX - this.lastX;
            // 预测滑块所处位置
            const duang = this.NowMessageList[index].slide_x + distance;
            // 如果在可行区域内
            if (duang <= 0 && duang >= this.btnWidth) {
                this.NowMessageList[index].slide_x = duang;
            }
            // 此处手指所处位置将成为下次手指移动时的上一次位置
            this.lastX = endX;
        },
        // 滑动结束
        touchEnd(e, index) {
            let distance = 10;
            const endTime = e.timeStamp;
            const x_end_distance = this.startX - this.lastX;
            if (Math.abs(endTime - this.startTime) > 200) {
                distance = this.btnWidth / -2;
            }
            // 判断手指最终位置与手指开始位置的位置差距
            if (x_end_distance > distance) {
                this.NowMessageList[index].slide_x = this.btnWidth;
            } else if (x_end_distance < distance * -1) {
                this.NowMessageList[index].slide_x = 0;
            } else {
                this.NowMessageList[index].slide_x = this.start_slide_x;
            }
        },
        // 点击回复原状
        recover(index) {
            this.NowMessageList[index].slide_x = 0;
        },
        // 置顶
        top(index) {
            this.NowMessageList[index].top = this.NowMessageList[index].top ? 0 : 1;
            this.NowMessageList.sort((a, b) => {
                return b.top - a.top;
            });
            this.recover(index);
        },
        // 删除
        removeM(index) {
            this.NowMessageList.splice(index, 1);
        }
    }
};
</script>

<style lang="scss" scoped>
* {
    margin: 0;
    padding: 0;
}
.container_of_slide {
    width: 100%;
    overflow: hidden;
    .slide_list {
        transition: all 100ms;
        transition-timing-function: ease-out;
        min-width: 200%;
        height: 150rpx;
        border-bottom: 1px solid #e0eef1;
        .now-message-info {
            display: flex;
            align-items: center;
            justify-content: space-between;
            .imgInfo {
                width: 100rpx;
                height: 100rpx;
                border-radius: 50%;
                background-color: #38a7fa;
                margin-left: 4%;
                image {
                    width: 100rpx;
                    height: 100rpx;
                    border-radius: 50%;
                    overflow: hidden;
                }
            }
            .centerInfo {
                height: 150rpx;
                display: flex;
                flex-direction: column;
                justify-content: center;
                align-items: baseline;
                text-align: left;
                width: 55%;
                padding-left: 5%;
                p {
                    overflow: hidden;
                    white-space: nowrap;
                    text-overflow: ellipsis;
                    max-width: 100%;
                }
                .name {
                    font-size: 35rpx;
                }
                .message {
                    font-size: 24rpx;
                    color: #7c8489;
                }
            }
            .timeInfo {
                height: 150rpx;
                display: flex;
                /*min-width:15% ;*/
                flex-direction: column;
                justify-content: center;
                align-items: center;
                margin-right: 2%;
                .time {
                    color: #92a0a1;
                    font-size: 25rpx;
                }
            }
        }
        .now-message-info,
        .group-btn {
            float: left;
        }
        .group-btn {
            display: flex;
            flex-direction: row;
            height: 100%;
            min-width: 100rpx;
            align-items: center;
            div {
                height: 100%;
                color: #fff;
                text-align: center;
                padding: 0 50rpx;
                font-size: 34rpx;
                line-height: 150rpx;
            }
            .top {
                background-color: #c4c7cd;
            }
            .removeM {
                background-color: #ff3b32;
            }
        }
    }
}
</style>
