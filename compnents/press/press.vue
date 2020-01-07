<template>
    <div class="container"
        :style="{
            top: y + '%',
            left: x + '%',
            width: width + '%',
            height: height + '%'
        }"
        v-show="visible">
        <div class="hot-area"
            @touchstart="onTouchStart"
            @touchend="onTouchEnd"
        ></div>
        <div v-fseq="{
            ...bg,
            active:pressed
        }"></div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            pressed: false,
            done: false
        };
    },
    mounted() {
        this.bridge.on('videoTimeUpdate', time => {
            if (this.done) return;
            if (time >= this.startTime && time <= this.endTime) {
                this.show();
            } else {
                this.hide();
            }
        })
    },
    methods: {
        onTouchStart() {
            this.pressed = true;
            this.handle = setTimeout(() => {
                this.hide();
                this.bridge.action(this.action);
                this.done = true;
            }, this.bg.freq / this.bg.speed * 1000);
        },
        onTouchEnd() {
            this.pressed = false;
            clearTimeout(this.handle);
        }
    }
};
</script>
<style>
.container {
    position: absolute;
    user-select: none;
}
.hot-area {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
}
</style>
