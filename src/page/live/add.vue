<template>
    <div>
        <head-top></head-top>
        <el-row style="margin-top: 20px;">
            <el-col :span="18" :offset="2">
                <el-form :model="formData" :rules="rules" ref="formData" label-width="110px" class="demo-formData">
                    <el-form-item label="活动标题" prop="themeName" class="normal_item">
                        <el-input v-model="formData.themeName"></el-input>
                    </el-form-item>
                    <el-form-item label="活动地区" prop="liveAddress" class="form_item">
                        <el-cascader name="liveAddress"
                                     size="large"
                                     :options="options"
                                     v-model="formData.liveAddress"
                                     @change="handleChange">
                        </el-cascader>
                    </el-form-item>
                    <el-form-item label="活动人数" prop="userTotalCount" class="normal_item">
                        <el-input-number v-model.number="formData.userTotalCount" :min="0" :max="100"
                                         :step="2"></el-input-number>
                    </el-form-item>
                    <el-form-item label="活动时间" prop="liveTime">
                        <el-date-picker
                            v-model="formData.liveStartTime"
                            type="datetime"
                            placeholder="开始时间">
                        </el-date-picker>
                        <el-date-picker
                            v-model="formData.liveEndTime"
                            type="datetime"
                            placeholder="结束时间">
                        </el-date-picker>
                    </el-form-item>
                    <el-form-item label="原价" prop="originPrice" class="normal_item">
                        <el-input-number v-model="formData.originPrice" :min="0" :max="100"></el-input-number>
                    </el-form-item>
                    <el-form-item label="优惠价" prop="discountPrice" class="normal_item">
                        <el-input-number v-model="formData.discountPrice" :min="0" :max="100"></el-input-number>
                    </el-form-item>
                    <el-form-item label="活动标签" prop="tag" class="normal_item">
                        <el-input v-model="formData.tag"></el-input>
                    </el-form-item>
                    <el-form-item label="活动说明" prop="desc" class="desc_item">
                        <quill-editor v-model="formData.desc"
                                      ref="myQuillEditor"
                                      class="editer"
                                      :options="editorOption">
                        </quill-editor>
                    </el-form-item>
                    <el-form-item class="button_submit">
                        <el-button type="primary" @click="submitForm('formData')">立即创建</el-button>
                    </el-form-item>
                </el-form>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    import headTop from '@/components/headTop'
    import {addShop, cityGuess, foodCategory, searchplace} from '@/api/getData'
    import {CodeToText, regionData} from 'element-china-area-data'
    import {quillEditor} from 'vue-quill-editor'

    export default {
        data: function () {
            return {
                areaJson: {},
                provinces: [],
                cities: [],
                areas: [],
                liveAddress: [],
                options: regionData,
                formRule: {
                    validateLiveAddress: ''
                },
                formData: {
                    themeName: '',
                    province: '',
                    city: '',
                    area: '',
                    userTotalCount: 10,
                    liveStartTime: '',
                    liveEndTime: '',
                    originPrice: 0,
                    discountPrice: 0,
                    tag: '',
                    desc: '',
                    liveAddress: []
                },
                rules: {
                    themeName: [
                        {required: true, message: '请输入活动标题', trigger: 'blur'},
                    ],
                    // liveAddress: [
                    //     {validator: validateLiveAddress, trigger: 'blur'}
                    // ],
                    userTotalCount: [
                        {required: true, message: '请输入活动总人数'},
                        {type: 'number', message: '活动总人数必须为数字', trigger: 'blur'}
                    ],
                    liveStartTime: [
                        {required: true, message: '请选择活动开始时间', trigger: 'blur'}
                    ],
                    liveEndtTime: [
                        {required: true, message: '请选择活动结束时间', trigger: 'blur'}
                    ],
                    originPrice: [
                        {required: true, message: '请输入活动原价'},
                        {type: 'number', message: '活动原件必须为数字', trigger: 'blur'}
                    ],
                    discountPrice: [
                        {required: true, message: '请输入优惠价'},
                        {type: 'number', message: '优惠价必须为数字', trigger: 'blur'}
                    ],
                    liveAddress: [
                        {required: true, message: '请选择活动地址'}
                    ],
                    // tag: [
                    //     {required: true, message: '请输入活动总人数'},
                    //     {type: 'number', message: '活动总人数必须为数字', trigger: 'blur'}
                    // ],
                    desc: [
                        {required: true, message: '请输入活动说明'},
                    ]
                },
                editorOption: {}
            }
        },
        components: {
            headTop,
            quillEditor,
        },
        computed: {
            editor() {
                return this.$refs.myQuillEditor.quill
            }
        },
        mounted() {
            this.initData();
        },
        methods: {
            async initData() {

            },
            submitForm(formName) {
                console.log(this.liveAddress)
                this.$refs[formName].validate(async (valid) => {
                    if (valid) {
                        Object.assign(this.formData, {activities: this.activities}, {
                            category: this.selectedCategory.join('/')
                        })
                        try {
                            let result = await addShop(this.formData);
                            if (result.status == 1) {
                                this.$message({
                                    type: 'success',
                                    message: '添加成功'
                                });
                                this.formData = {
                                    name: '', //店铺名称
                                    address: '', //地址
                                    latitude: '',
                                    longitude: '',
                                    description: '', //介绍
                                    phone: '',
                                    promotion_info: '',
                                    float_delivery_fee: 5, //运费
                                    float_minimum_order_amount: 20, //起价
                                    is_premium: true,
                                    delivery_mode: true,
                                    new: true,
                                    bao: true,
                                    zhun: true,
                                    piao: true,
                                    startTime: '',
                                    endTime: '',
                                    image_path: '',
                                    business_license_image: '',
                                    catering_service_license_image: '',
                                };
                                this.selectedCategory = ['快餐便当', '简餐'];
                                this.activities = [{
                                    icon_name: '减',
                                    name: '满减优惠',
                                    description: '满30减5，满60减8',
                                }];
                            } else {
                                this.$message({
                                    type: 'error',
                                    message: result.message
                                });
                            }
                            console.log(result)
                        } catch (err) {
                            console.log(err)
                        }
                    } else {
                        this.$notify.error({
                            title: '错误',
                            message: '请检查输入是否正确',
                            offset: 100
                        });
                        return false;
                    }
                });
            },
            handleChange(value) {
                console.log(CodeToText[value[0]])
                this.formData.province = CodeToText[value[0]]
                this.formData.city = CodeToText[value[1]]
                this.formData.area = CodeToText[value[2]]
            }
        }
    }
</script>

<style lang="less">
    @import '../../style/mixin';

    .button_submit {
        text-align: center;
    }

    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }

    .avatar-uploader .el-upload:hover {
        border-color: #20a0ff;
    }

    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 120px;
        height: 120px;
        line-height: 120px;
        text-align: center;
    }

    .avatar {
        width: 120px;
        height: 120px;
        display: block;
    }

    .el-table .info-row {
        background: #c9e5f5;
    }

    .el-table .positive-row {
        background: #e2f0e4;
    }

    .edit_container {
        padding: 40px;
        margin-bottom: 40px;
    }

    .editer {
        height: 350px;
    }

    .normal_item {
        width: 70%;
    }

    .desc_item {
        height: 450px;
    }
</style>
