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
            if (time >= this.startTime && time <= this.endTime) {
                this.show();
                if (this.stop) {
                    vBridge.pause();
                }
            } else {
                this.hide();
            }
        })
    },

    methods: {
        onClick(e) {
            this.bridge.action(this.action);
            this.selected = true;
            this.hide();
            if (this.stop) {
                vBridge.play();
            }
        }
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
