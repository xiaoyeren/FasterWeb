<template>
    <el-main style="padding-top: 0">
        <div style="margin-top: 10px;">
            <el-col :span="12">
                <el-form
                    :model="kwargsForm"
                    :rules="rules"
                    ref="kwargsForm"
                    label-width="100px"
                >
                    <el-form-item label="用例名称" prop="testCaseName">
                        <el-input v-model="kwargsForm.testCaseName" placeholder="请输入用例名称" clearable size="medium"
                                  type="textarea" :autosize="{ minRows: 1, maxRows: 8}"></el-input>
                    </el-form-item>

                    <el-form-item label="域名参数">
                        <el-select placeholder="请选择" size="medium" v-model="kwargsForm.hostInfo" style="width: 45%;">
                            <el-option
                                v-for="item in hostOptions"
                                :key="item.id"
                                :label="item.name"
                                :value="item.name">
                            </el-option>
                        </el-select>
                    </el-form-item>

                    <el-form-item label="数据选择">
                        <el-select
                            placeholder="请选择"
                            style="width: 45%;"
                            size="medium"
                            v-model="kwargsForm.currentTestDataExcel"
                        >
                            <el-option
                                v-for="item in testDataOptions"
                                :key="item.id"
                                :label="item.name"
                                :value="item.name"
                            >
                            </el-option>
                        </el-select>
                        <el-input
                            style="width: 50%;"
                            size="medium"
                            v-model="kwargsForm.currentTestDataSheet"
                            placeholder="请输入sheet名"
                            clearable
                        >
                        </el-input>
                    </el-form-item>

                    <el-form-item>
                        <el-button size="small" @click="esc = !esc">返回</el-button>
                        <el-button size="small" type="primary" @click="emitKwargsForm('kwargsForm')">保存</el-button>
                    </el-form-item>
                </el-form>
            </el-col>
        </div>
    </el-main>
</template>

<script>
    export default {
        components: {
        },
        props: {
            next: Boolean,
            testCase: {
                require: true
            }
        },
        methods: {
            emitKwargsForm(kwargsForm){
                this.$refs[kwargsForm].validate((valid) => {
                    if (valid) {
                        this.$emit('KwargsForm', this.kwargsForm)
                    } else {
                        return false;
                    }
                })
            },
            getHost() {
                this.$api.hostList({params: {project: this.$route.params.id}}).then(resp => {
                    this.hostOptions = resp.data["results"];
                    this.hostOptions.push({
                        name: '请选择'
                    })
                })
            },
            getTestData() {
                this.$api.testdataList({params: {project: this.$route.params.id}}).then(resp => {
                    this.testDataOptions = resp.results;
                    this.testDataOptions.push({
                        name: '请选择'
                    })
                })
            }
        },
        watch: {
            esc() {
                this.$emit('escEdit');
            }
        },
        data() {
            return {
                esc: false,
                testDataOptions: [],
                hostOptions:[],
                testCaseName: this.testCase.name,
                kwargsForm: {
                    testCaseName: '',
                    hostInfo: '',
                    currentTestDataExcel: '',
                    currentTestDataSheet: '',
                },
                rules:{
                    testCaseName: [
                        {required: true, message: '请输入用例名称', trigger: 'blur'},
                        {min: 1, max: 500, message: '长度在 1 到 500 个字符', trigger: 'blur'}
                    ]
                }
            }
        },
        name: "EditTestCase",
        mounted() {
            this.getHost();
            this.getTestData();
            if (this.testCase.kwargs){
                this.kwargsForm = this.testCase.kwargs
            }
        }
    }
</script>

<style scoped>

</style>
