<template>
    <div class="layout">
        <Card :dis-hover="true" :shadow="false" :bordered="false" class="login">
            <h4 slot="title">欢迎登陆</h4>
            <Form ref="formValidate" :model="formValidate" :rules="ruleValidate" >
                <FormItem prop="name">
                    <Input prefix="ios-contact" placeholder="Enter name"/>
                </FormItem>
                <FormItem prop="name">
                    <Input prefix="ios-contact" placeholder="Enter name"/>
                </FormItem>
                <FormItem>
                    <Button type="primary" style="width:100%" @click="submit">登陆</Button>
                </FormItem>
            </Form>
            <router-link to="/register">注册</router-link>
        </Card>
    </div>
</template>

<script>
export default {
    data (){
        return {
            formValidate:{},
            ruleValidate:{}
        }
    },
    methods:{
        submit(){
            this.$api.login({},(res)=>{
                this.$router.push('/')
            })
        }
    }
}
</script>


<style lang="less" scoped>
.layout{
    height: 100%;
    background: url('../assets/login-bg.jpg') no-repeat;
    background-size: cover;
    background-position: 50%;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    .login{
        width: 300px;
        right: 150px;
    }
}
</style>
