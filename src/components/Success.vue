<template>
    <div class="success_background">
        <div class="success_massage">操作成功！！！
            {{this.$route.params.message}}
        </div>
        <div class="exit_btn">
            <el-button type="info" @click="logout">安全退出</el-button>
        </div>
    </div>
</template>

<style lang="less" scoped>
    .success_massage{
        width: 300px;
        margin: 0 40%;
        background-color: aqua;
        font-size: 30px;
        text-align: center;
    }
</style>

<script>
export default {
    data() {
        return {
            
        }
    },
    methods: {
        logout(){
            window.sessionStorage.clear();
            this.$router.push('/login');
        },
    },
}
</script>