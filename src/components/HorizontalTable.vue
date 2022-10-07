<template>
    <div class="container-calendar not-scroll-bar" onscroll="syncScroll(event)">
        <div class="not-scroll-bar static-header static-header-fixed">
            <div class="blank-space"></div>
            <div class="header-group">
                <span v-for="time in timeLineData" :key="time" v-text="time"></span>
            </div>
            <div class="space-line"></div>
        </div>
        <div class="calendar-body">
            <div class="v-static-header-container">
                <div class="v-header" v-for="data in listData" :key="data.id" :style="{'height': data.slots.length * 40 + 'px'}">
                    <div class="v-header-name" v-text="data.name"></div>
                    <div class="v-slot-container">
                        <div class="v-slot" v-for="(slot, index) in data.slots" :key="slot.id" v-text="index + 1"></div>
                    </div>
                </div>
            </div>
            <div class="calendar-grid" :data-temp="slotCount = 0">
                <div v-for="data in listData" :key="data.id" class="data-person">
                    <div v-if="data.disabled" class="flex data-slot disabled-slot">
                        <div v-for="(time, timeLineIdx) in timeLineData" :key="timeLineIdx" class="flex">
                            <span class="time-grid border-dash"></span>
                            <span class="time-grid"></span>
                        </div>
                    </div>
                    <div v-else v-for="(slot, slotIdx) in data.slots" :key="slotIdx" class="flex data-slot">
                        <div v-for="(time, timeLineIdx) in timeLineData" :key="timeLineIdx" class="flex">
                            <span class="time-grid border-dash" ref="timeGrid"></span>
                            <span class="time-grid"></span>
                        </div>
                        <div v-for="event in slot.data" :key="event.id" class="event-data" :style="getStyle(event)">
                            <span class="text drag-drop">{{event.text}}</span>
                            <span class="resize" @touchstart.prevent="startResize($event, event)" @touchend="endResize($event, event)"></span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { computed, ref } from 'vue';
    import { useStore } from 'vuex';
    const timeLineData = Array.from(Array(14).keys()).map((e) => `${e+9}:00`);
    const store = useStore();
    const listData = computed(() => store.state.booking.dummyData);
    const timeGrid = ref(null);
    const timeToPx = (timeStr) => {
        const arrayTime = timeStr.split(":");
        return Number(arrayTime[0]) + Number(arrayTime[1]) / 60;
    };
    const pxToTime = (start, width) => {
        const cellWidth = timeGrid.value[0].offsetWidth;
        start = Number(start)/(cellWidth*2);
        width = Number(width)/(cellWidth*2);
        const lengthPx = start + width;
        return `${Math.floor(lengthPx)+9}:${(lengthPx-Math.floor(lengthPx))*60 === 0 ? '00' : (lengthPx-Math.floor(lengthPx))*60}`
    }

    const getStyle = (event) => {
      return {
        background: event.color,
        left: `${(timeToPx(event.startTime)-9) * 2 * 49 }px`,
        width: `${
          (timeToPx(event.endTime) - timeToPx(event.startTime)) * 2 * 49
        }px`
      };
    };
    var touchMoveEvent = () => {};
    let targetResize = false;
    const startResize = (event, data) => {
        targetResize = true;
        const cellWidth = timeGrid.value[0].offsetWidth;
        touchMoveEvent = (e) => {
            const span = event.target;
            const parentSpan = span.parentNode;
            if(parentSpan) {
                let newWidth = e.changedTouches[0].clientX - parentSpan.getBoundingClientRect().left;
                newWidth = Math.floor(newWidth/cellWidth)*cellWidth;
                newWidth = Math.max(cellWidth, newWidth);
                newWidth = Math.min(newWidth, parentSpan.parentNode.getBoundingClientRect().right);
                parentSpan.style.width = newWidth + 'px';
            }
        }
        document.addEventListener('touchmove', touchMoveEvent);
    }
    const endResize = (event, data) => {
        targetResize = false;
        document.removeEventListener('touchmove', touchMoveEvent);
        const parentSpanStyle = event.target.parentNode.style;
        const newEndTime = pxToTime(parentSpanStyle.left.replace('px',''), parentSpanStyle.width.replace('px',''));
        data.endTime = newEndTime;
    }

    const startDragDrop = (event, data) => {

    }

</script>

<style scoped>
.container-calendar {       
    margin-left: 10px;
    overflow: auto;
    white-space: nowrap;
}
.static-header span:first-of-type {
  margin-left: 177px;
}
.static-header span {
  display: inline-block;
  width: 98px;
  height: 44px;
  font-size: 16px;
  padding: 10px 10px;
  background: rgb(240, 242, 247);
  font-weight: bold;
  box-sizing: border-box;
}
.blank-space {
  width: 177px;
  height: 48px;
  position: absolute;
  background-color: var(--white);
  z-index: 10;
}
.static-header span:not(:last-child) {
  border-right: 2px solid var(--white);
}
.space-line {
    width: 100%;
    height: 4px;
    background-color: var(--light-gray);
    position: relative;
    top: -4px;
}
/* .static-header-fixed {
  position: fixed;
  overflow: hidden;
  z-index: 10;
} */
.v-header {
    display: flex;
    width: 170px;
    align-items: center;
    background-color: var(--white);
    border: 1px solid var(--light-gray);
    border-right: 4px solid var(--light-gray);
}
.v-static-header-container .v-header:not(:first-child) {
    border-top: none;
}
.v-header-name {
    flex-grow: 1;
    padding:  5px 10px;
    font-size: 16px;
}
.v-slot {
    padding: 12px 5px 5px;
    height: 40px;
    font-size: 16px;
    border-top: 1px solid var(--light-gray);
    border-left: 1px solid var(--light-gray);
}
.v-slot-container .v-slot:first-child {
    border-top: none;
}
.calendar-body {
    display: flex;
    gap: 7px;
}
.calendar-grid {
    position: relative;
}
.flex {
  display: flex;
}
.time-grid {
  border-bottom: 1px solid var(--light-gray);
  border-right: 2px solid var(--light-gray);
  height: 40px;
  width: 49px;
  box-sizing: border-box;
}
.time-grid.border-dash {
  border-right: 2px dashed var(--light-gray);
}
.calendar-grid .data-person:first-child .time-grid{
    border-top: 2px solid var(--light-gray);
}
.calendar-grid .data-person:last-child .time-grid{
    border-bottom: 2px solid var(--light-gray);
}
.data-person .data-slot .flex:first-child .border-dash {
    border-left: 2px solid var(--light-gray);
}
.event-data {
    position: absolute;
    height: 40px;
    border: 2px solid var(--rock-blue);
    border-radius: 5px;
    background-color: bisque;
    font-weight: bold;
}
.event-data span.text {
    position: relative;
    top: -2px;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    width: 100%;
    display: block;
    font-size: 15px;
    cursor: grab;
    line-height: 40px;
    -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
    -khtml-user-select: none; /* Konqueror HTML */
    -moz-user-select: none; /* Old versions of Firefox */
    -ms-user-select: none; /* Internet Explorer/Edge */
    user-select: none;
}
.event-data span.resize {
  cursor: ew-resize;
  display: block;
  position: absolute;
  right: -4px;
  height: 100%;
  width: 8px;
  top: 0;
  z-index: 5;
}
.event-data::after {
  content: "";
  width: 6px;
  height: 70%;
  background: var(--white);
  display: block;
  position: absolute;
  right: -5px;
  top: 50%;
  transform: translateY(-50%);
  border-radius: 2px;
  border: 1px solid var(--light-gray);
}
.event-data::before {
  content: "|||||||";
  width: 4px;
  height: 22%;
  rotate: 90deg;
  font-size: 5px;
  display: block;
  position: absolute;
  color: var(--light-gray);
  right: -3px;
  top: 11px;
  z-index: 2;
}
</style>