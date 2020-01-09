<template>
<div class="bubble"
    @click="onClick($event)"
    v-show="visible"
    :style="{
        top: y + '%',
        left: x + '%',
        width: width + '%',
        height: height + '%'
    }">
    <span class="bubble-text">{{text}}</span>
</div>
</template>
<script>
export default {
    data() {
        return {
            selected: false
        };
    },

    mounted() {
        this.bridge.on('videoTimeUpdate', time => {
            if (this.selected) return;

            let handleMethod = `handle${this.playType}`;
            if (typeof this[handleMethod] === 'function') {
                this[handleMethod](time);
            }
        });
    },

    methods: {
        onClick(e) {
            this.bridge.action(this.action);
            this.selected = true;
            this.hide();
            if (this.playType === 'stop') {
                vBridge.play();
            }
        },
        // 单次播放模式
        handleOnce(time) {
            this[time >= this.startTime && time <= this.endTime ? 'show' : 'hide']
        },
        // 视频暂停模式
        handleStop(time) {
            if (time >= this.startTime && time <= this.endTime) {
                this.show();
                vBridge.pause();
            } else {
                this.hide();
            }
        },
        // 循环播放模式
        handleLoop(time) {
            if(time > this.endTime) {
                this.bridge.invoke('seek', this.startTime);
            }
            this.show();
        },
    }
};
</script>
<style>
.bubble {
    position: absolute;
    display: table;
    border-radius: 25px;
    background-color: rgba(0, 0, 0, 0.5);
    border: 1px solid white;
    text-align: center;
}

.bubble .bubble-text {
    display: table-cell;
    vertical-align: middle;
}
</style>
