<template>
<div class="container"
    v-show="visible"
    :style="{
        top: y + '%',
        left: x + '%',
        width: width + '%',
        height: height + '%'
    }">
    <div class="track-bg" :style="{
            transform: `rotate(${deg}deg)`
        }">
        <img ref="trackBg" :src="bg.url" v-show="visible" :style="{
            width: 100 * ratio + '%'
        }">
    </div>
    <div class="smooth"
        :style="{
            transform: 'translate(' + left + 'px,' + top + 'px)'
        }">
        <div class="block" v-drag="{
                dragmove: onDragMove,
                dragend: onDragEnd
            }">
            <img ref="blockBg" class="block-bg" :src="blockBg.url" :style="{
                transform: `rotate(${deg}deg)`
            }">
        </div>
    </div>
</div>
</template>
<script>
export default {
    data() {
        return {
            left: 0,
            top: 0,
            ratio: 1,
        };
    },
    mounted() {
        console.log('??????', this.bg)
        this.bridge.on('videoTimeUpdate', time => {
            if (!this.loaded && time >= this.startTime && time <= this.endTime) {
                this.show();
                vBridge.pause();
            }
        });
        let img = new Image();
        img.src = this.bg.url;
        img.onload = () => {
            this.ratio = img.width / img.height;
        };
    },
    methods: {
        onDragMove(pos) {
            let deg = this.deg / 180 * Math.PI;
            let left = pos.left + this.left;
            let top = Math.tan(deg) * left;
            let distance = this.$refs.trackBg.width - this.$refs.blockBg.width;
            if (!(distance > 0)) {
                distance = Infinity;
            }
            let targetX = distance * Math.cos(deg);
            if (targetX * left < 0) {
                left = top = 0;
            } else if (Math.abs(left) > Math.abs(targetX)) {
                left = targetX;
                top = distance * Math.sin(deg);
            }
            return {
                left: left - this.left,
                top: top - this.top
            };
        },
        onDragEnd(pos) {
            let deg = this.deg / 180 * Math.PI;
            let left = pos.left + this.left;
            let top = Math.tan(deg) * left;
            let distance = this.$refs.trackBg.width - this.$refs.blockBg.width;
            if (!(distance > 0)) {
                distance = Infinity;
            }
            if (Math.abs(Math.sqrt(left * left + top * top) - distance) < 10) {
                this.bridge.invoke('action', this.action);
                this.hide();
                vBridge.play();
                this.loaded = true;
            } else {
                this.left = -pos.left;
                this.top = -pos.top;
            }
        }
    }
};
</script>
<style>
.container {
    position: absolute;
}
.track-bg {
    position: absolute;
    height: 100%;
    width: 100%;
    z-index: -1;
}
.track-bg img{
    height: 100%;
    max-width: none;
    transform: translateZ(0);
}
.block {
    width: 100%;
    height: 100%;
}
.block-bg {
    pointer-events: none;
    width: 100%;
    height: 100%;
}
.smooth {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: 99;
    transition: transform .2s ease;
}
</style>
