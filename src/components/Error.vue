<template>
    <div class="error">
        <div class="not-found-img">
            <img src="../assets/not_found.svg" alt="">
        </div>
        <span class="title-error">К сожалению, по вашим <br> запросам ничего не найдено</span>
    </div>
</template>

<script>
export default {
    name: 'error',
}
</script>

<style lang="scss" scoped>
.error {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 10vh;
    flex-direction: column;
}

.title-error {
    font-weight: 500;
    font-size: 32px;
    line-height: 40px;
    text-align: center;
    color: #353535;
    margin-top: 2vh;
}
</style>