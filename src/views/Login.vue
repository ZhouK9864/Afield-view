<template>
    <dev>
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="50px" class="demo-ruleForm">.
            <el-form-item label="学号" prop="studentId">
                <el-input v-model="ruleForm.studentId"></el-input>
            </el-form-item>
            <el-form-item label="密码" prop="password">
                <el-input v-model="ruleForm.password"></el-input>
            </el-form-item>

            <el-form-item>
                <el-button type="primary" @click="submitForm('ruleForm')">登陆</el-button>
                <el-button @click="resetForm('ruleForm')">重置</el-button>
                &nbsp
                <router-link to="/register"> 还没账号？立即注册！</router-link>

            </el-form-item>
        </el-form>
    </dev>
</template>

<script>
    import {mapMutations} from "vuex";

    export default {
        data() {
            return {
                ruleForm: {
                    studentId: '',
                    password: '',
                },
                userToken: '',
                rules: {
                    studentId: [
                        {required: true, message: '请输入学号', trigger: 'blur'},
                        {min: 0, max: 13, message: '长度在 11 到 13 个字符', trigger: 'blur'}
                    ],
                    password: [
                        {required: true, message: '请输入密码', trigger: 'blur'},
                        {min: 6, max: 15, message: '长度在 8 到 15 个字符', trigger: 'blur'}
                    ],
                }
            };
        },
        methods: {
            ...mapMutations(['changeLogin']),
            submitForm(formName) {
                const _this = this
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        axios.post('http://localhost/user/login', this.ruleForm).then(function (resp) {
                            if (resp.data == 'success') {
                                _this.$message('登录成功');
                                _this.userToken = 'Bearer ' + resp.data.token;
                                // 将用户token保存到vuex中
                                _this.changeLogin({Authorization: _this.userToken});
                                _this.$router.push('/manage');
                                // _this.$store.commit('set_token', data["Authentication-Token"])
                            } else if (resp.data == 'usernameerror') {
                                _this.$message('用户名错误');
                            } else if (resp.data == 'passworderror') {
                                _this.$message('密码错误');
                            }
                        })
                    } else {
                        console.log('错的！提交个锤子！');
                        return false;
                    }
                });
            },
            resetForm(formName) {
                this.$refs[formName].resetFields();
            }
        }
    }
</script>

<style>
    .el-form {
        position: relative;
        margin: 0 auto;
        width: 350px;
        top: 100px;
    }

</style>