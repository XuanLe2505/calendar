<template>
<div class="wrapper-actions">
    <div class="btn-default">
        <button>本日</button>
    </div>
    <div class="target-date">
        <button class="btn-previous-date"><img src="../assets/images/angle_left_icon.svg" alt="angle left"></button>
        <span class="current-year">2022</span>
        <span class="current-month-day">10/03</span>
        <span class="text-month">(火)</span>
        <button class="btn-next-date"><img src="../assets/images/angle_right_icon.svg" alt="angle right"></button>
    </div>
    <div class="btn-booking-detail1">
        <button>GO</button>
    </div>
    <div class="btn-booking-detail2">
        <button @click="openBookingModal(!showBookingModal)">クイック受付</button>
    </div>
    <div class="show-by-hours">
        <button class="btn-show-6h" @click="handleChangeByHours('6h')">6H</button>
        <button class="btn-show-all active" @click="handleChangeByHours('all')">ALL</button>
    </div>
    <div class="type-show-table">
        <button class="btn-type-horizontal active" @click="handleChangeByType('horizontal')"><img src="../assets/images/burgermenu_icon.svg" alt="horizontal table"></button>
        <button class="btn-type-vertical" @click="handleChangeByType('vertical')"><img src="../assets/images/burgermenu_icon.svg" alt="vertical table"></button>
        <button class="btn-type-list" @click="handleChangeByType('list')"><img src="../assets/images/list_menu_icon.svg" alt="list"></button>
    </div>
</div>
</template>

<script setup>
    const props = defineProps({
        showBookingModal: Boolean,
    })
    const emit = defineEmits(['changeTableByType', 'changeTableByHours', 'openBookingModal']);
    const handleChangeByType = (type) => {
        emit('changeTableByType', type);
    }
    const handleChangeByHours = (type) => {
        emit('changeTableByHours', type);
    }
    const openBookingModal = (status) => {
        emit('openBookingModal', status)
    }
</script>

<style scoped>
.wrapper-actions {
    display: flex;
    align-items: center;
    width: 100%;
    margin: 15px 20px 35px 10px;
}
.btn-default button {
    background-color: var(--dark-gray);
    color: var(--white);
    font-size: 20px;
    border: 1px solid var(--dark-gray);
    border-radius: 4px;
    height: 40px;
    width: 60px;
}
.btn-previous-date,
.btn-next-date {
    width: 40px;
    height: 40px;
    border-radius: 10px;
    border: 2px solid var(--light-gray);
}
.btn-previous-date img,
.btn-next-date img {
    width: 10px;
}
.target-date {
    margin-left: 20px;
    margin-right: 83px;
}
.target-date .current-year {
    font-size: 18px;
    margin-left: 10px;
    margin-right: 7px;
}
.current-month-day, .text-month {
    font-size: 22px;
    font-weight: bold;
}
.text-month {
    margin: 0 4px;
}
.btn-booking-detail1 button,
.btn-booking-detail2 button {
    color: var(--light-black);
    background-color: var(--white);
    border: 1px solid var(--dark-gray);
    border-radius: 4px;
    font-size: 20px;
    font-weight: bold;
    height: 40px;
}
.btn-booking-detail1 button {
    width: 50px;
}
.btn-booking-detail2 button {
    width: 140px;
}
.btn-booking-detail2 {
    margin-left: 15px;
    margin-right: 45px;
}
.show-by-hours,
.type-show-table {
    display: flex;
    align-items: center;
}
.btn-show-6h {
    width: 40px;
    height: 40px;
    border: 1px solid var(--dark-gray);
    border-right: none;
    border-top-left-radius: 4px;
    border-bottom-left-radius: 4px;
}
.btn-show-all {
    width: 40px;
    height: 40px;
    background-color: var(--dark-gray);
    color: var(--white);
    border: 1px solid var(--dark-gray);
    border-left: none;
    border-top-right-radius: 4px;
    border-bottom-right-radius: 4px;
}
.show-by-hours {
    margin-right: 24px;
}
.type-show-table {
    border-radius: 4px;
    background-color: var(--light-gray);
}
.type-show-table button {
    background-color: var(--light-gray);
    width: 40px;
    height: 40px;
    border-radius: 4px;
}
.btn-type-horizontal img,
.btn-type-vertical img {
    position: relative;
    top: 1px;
    transform: matrix(1, 0, 0.5, 1, 0, 0);
}
.btn-type-horizontal {
    border-bottom-right-radius: 4px;
    border-top-right-radius: 4px;
}
.btn-type-list {
    border-bottom-right-radius: 4px;
    border-top-right-radius: 4px;
}
.btn-type-list img {
    width: 40px;
}

.btn-type-vertical img {
    rotate: 90deg;
}

.type-show-table button.active {
    background-color: var(--dark-gray);
}
</style>