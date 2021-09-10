<template>
    <div class="login-container">
        <el-form :model="ruleForm2" :rules="rules2"
         status-icon
         ref="ruleForm2" 
         label-position="left" 
         label-width="0px" 
         class="demo-ruleForm login-page">
            <h3 class="title">后台管理系统登录</h3>
            <el-form-item prop="username">
                <el-input prefix-icon="el-icon-user" type="text" 
                    v-model="ruleForm2.username" 
                    auto-complete="off" 
                    placeholder="用户名/手机号/邮箱"
                    clearable
                ></el-input>
            </el-form-item>
                <el-form-item prop="password">
                    <el-input prefix-icon="el-icon-unlock" type="password" 
                        v-model="ruleForm2.password" 
                        auto-complete="off" 
                        placeholder="密码"
                        show-password
                    ></el-input>
                </el-form-item>
                <el-form-item prop="verifycode">
                <!-- 注意：prop与input绑定的值一定要一致，否则验证规则中的value会报undefined，因为value即为绑定的input输入值 -->
                    <el-col :span="13">
                        <el-input prefix-icon="el-icon-success" v-model="ruleForm2.verifycode" placeholder="请输入验证码"></el-input>
                    </el-col>
                    <el-col :span="8">
                        <div class="identifybox">
                            <div class="op"></div>
                            <div @click="refreshCode">
                                <s-identify :identifyCode="identifyCode"></s-identify>
                            </div>
                            <el-button @click="refreshCode" type='text' class="textbtn">看不清，换一张</el-button>
                        </div>
                    </el-col>
                    
                </el-form-item>
            <el-form-item style="width:100%;">
                <el-button type="primary" style="width:100%;" @click="handleSubmit" :loading="logining">登录</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
import SIdentify from './RandomCode.vue'
export default {
    name: 'userlogin',
    data(){
        // 自定义验证规则：验证码验证规则
        const validateVerifycode = (rule, value, callback) => {
            if (value === '') {
                callback(new Error('请输入验证码'))
            } else if (value !== this.identifyCode) {
                console.log('validateVerifycode:', value)
                callback(new Error('验证码不正确'))
            } else {
                callback()
            }
        }
        return {
            logining: false,
            identifyCodes: '1234567890',
            identifyCode: '',
            ruleForm2: {
                username: '',
                password: '',
                verifycode: ''
            },
            rules2: {
                username: [{required: true, message: '请输入用户名', trigger: 'blur'}],
                password: [{required: true, message: '请输入密码', trigger: 'blur'}],
                verifycode: [{ required: true, trigger: 'blur', validator: validateVerifycode }]
            },
            checked: false
        }
    },
    components: {
        // 注册绘制随机验证码的组件
        SIdentify
	},
    created() {},
    mounted() {
        // 验证码初始化
        this.identifyCode = ''
        this.makeCode(this.identifyCodes, 4)
    },
    computed: {},
    methods: {
        handleSubmit(event){
            this.$refs.ruleForm2.validate((valid) => {
                if(valid){
                    this.logining = true;
                    if(this.ruleForm2.username === 'admin' && 
                       this.ruleForm2.password === '123456'){
                            this.logining = false;
                            sessionStorage.setItem('user', this.ruleForm2.username);
                            this.$router.push({path: '/'});
                    }else{
                        this.logining = false;
                        this.$alert('用户名密码错误', '提示', {
                            confirmButtonText: '确认'
                        })
                    }
                }else{
                    console.log('error submit!');
                    return false;
                }
            })
        },
        // 生成随机数
        randomNum(min, max) {
            return Math.floor(Math.random() * (max - min) + min)
        },
        // 切换验证码
        refreshCode() {
            this.identifyCode = ''
            this.makeCode(this.identifyCodes, 4)
        },
        // 生成四位随机验证码
        makeCode(o, l) {
            for (let i = 0; i < l; i++) {
                this.identifyCode += this.identifyCodes[this.randomNum(0, this.identifyCodes.length)]
            }
            console.log(this.identifyCode)
        }
    }
};
</script>

<style scoped>
.op{
    margin-left: 5px;
}
.randomcodeuse{
    width: 60%;
    display: flex;
    align-items: center;
}
.identifybox {
    display: flex;
    justify-content: space-between;
    /* margin-top: 7px; */
}
.iconstyle {
    color: #409EFF;
}
.login-container {
    width: 100%;
    height: 100%;
}
.login-page {
    -webkit-border-radius: 5px;
    border-radius: 5px;
    margin: 180px auto;
    width: 380px;
    padding: 35px 35px 15px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
}
label.el-checkbox.rememberme {
    margin: 0px 0px 15px;
    text-align: left;
}
</style>