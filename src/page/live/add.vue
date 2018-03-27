<template>
    <div>
        <head-top></head-top>
        <el-row style="margin-top: 20px;">
            <el-col :span="12" :offset="4">
                <el-form :model="formData" :rules="rules" ref="formData" label-width="110px" class="demo-formData">
                    <el-form-item label="活动标题" prop="themeName">
                        <el-input v-model="formData.themeName"></el-input>
                    </el-form-item>
                    <el-form-item label="活动地区" prop="address">
                        <el-autocomplete
                            class="inline-input"
                            v-model="formData.province"
                            :fetch-suggestions="querySearchAsync"
                            placeholder="请输入内容"
                        ></el-autocomplete>
                    </el-form-item>
                    <el-form-item label="活动人数" prop="userTotalCount">
                        <el-input v-model.number="formData.userTotalCount" maxLength="11"></el-input>
                    </el-form-item>
                    <el-form-item label="活动时间" prop="liveStartTime">
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
                    <el-form-item label="原价" prop="originPrice">
                        <el-input v-model="formData.originPrice"></el-input>
                    </el-form-item>
                    <el-form-item label="优惠价" prop="discountPrice">
                        <el-input v-model="formData.discountPrice"></el-input>
                    </el-form-item>
                    <el-form-item label="活动标签" prop="tag">
                        <el-input v-model="formData.tag"></el-input>
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
    import {addShop, cities, cityGuess, foodCategory, searchplace} from '@/api/getData'
    import {baseImgPath, baseUrl} from '@/config/env'

    export default {
        data() {
            return {
                areaJson: {},
                provinces: [],
                cities: [],
                areas: [],
                formData: {
                    // name: '', //店铺名称
                    // address: '', //地址
                    // latitude: '',
                    // longitude: '',
                    // description: '', //介绍
                    // phone: '',
                    // promotion_info: '',
                    // float_delivery_fee: 5, //运费
                    // float_minimum_order_amount: 20, //起价
                    // is_premium: true,
                    // delivery_mode: true,
                    // new: true,
                    // bao: true,
                    // zhun: true,
                    // piao: true,
                    // startTime: '',
                    // endTime: '',
                    // image_path: '',
                    // business_license_image: '',
                    // catering_service_license_image: '',
                    themeName: '',
                    province: '',
                    city: '',
                    area: '',
                    userTotalCount: '',
                    liveStartTime: '',
                    liveEndTime: '',
                    originPrice: '',
                    discountPrice: '',
                    tag: '',
                    desc: ''

                },
                rules: {
                    // name: [
                    //     {required: true, message: '请输入店铺名称', trigger: 'blur'},
                    // ],
                    // address: [
                    //     {required: true, message: '请输入详细地址', trigger: 'blur'}
                    // ],
                    // phone: [
                    //     {required: true, message: '请输入联系电话'},
                    //     {type: 'number', message: '电话号码必须是数字'}
                    // ],
                    themeName: [
                        {required: true, message: '请输入活动标题', trigger: 'blur'}
                    ]
                },
                options: [{
                    value: '满减优惠',
                    label: '满减优惠'
                }, {
                    value: '优惠大酬宾',
                    label: '优惠大酬宾'
                }, {
                    value: '新用户立减',
                    label: '新用户立减'
                }, {
                    value: '进店领券',
                    label: '进店领券'
                }],
                activityValue: '满减优惠',
                activities: [{
                    icon_name: '减',
                    name: '满减优惠',
                    description: '满30减5，满60减8',
                }],
                baseUrl,
                baseImgPath,
                categoryOptions: [],
                selectedCategory: ['快餐便当', '简餐']
            }
        },
        components: {
            headTop,
        },
        mounted() {
            this.initData();
            // console.log("cities:" + this.cities)
        },
        methods: {
            async initData() {
                try {
                    this.areaJson = await cities();
                    for (var area in this.areaJson) {
                        this.provinces.push(area)
                    }
                    console.log(this.provinces)
                    const categories = await foodCategory();
                    categories.forEach(item => {
                        if (item.sub_categories.length) {
                            const addnew = {
                                value: item.name,
                                label: item.name,
                                children: []
                            }
                            item.sub_categories.forEach((subitem, index) => {
                                if (index == 0) {
                                    return
                                }
                                addnew.children.push({
                                    value: subitem.name,
                                    label: subitem.name,
                                })
                            })
                            this.categoryOptions.push(addnew)

                        }
                    })
                } catch (err) {
                    console.log(err);
                }
            },
            async querySearchAsync(province, cb) {
                var provinces = this.provinces;
                var results = province ? provinces.filter(this.createFilter(province)) : provinces;
                // 调用 callback 返回建议列表的数据
                cb(results);
            },
            createFilter(province) {
                return (restaurant) => {
                    return (restaurant.value.toLowerCase().indexOf(province.toLowerCase()) === 0);
                };
            },
            addressSelect(address) {
                this.formData.latitude = address.latitude;
                this.formData.longitude = address.longitude;
                console.log(this.formData.latitude, this.formData.longitude)
            },
            handleShopAvatarScucess(res, file) {
                if (res.status == 1) {
                    this.formData.image_path = res.image_path;
                } else {
                    this.$message.error('上传图片失败！');
                }
            },
            handleBusinessAvatarScucess(res, file) {
                if (res.status == 1) {
                    this.formData.business_license_image = res.image_path;
                } else {
                    this.$message.error('上传图片失败！');
                }
            },
            handleServiceAvatarScucess(res, file) {
                if (res.status == 1) {
                    this.formData.catering_service_license_image = res.image_path;
                } else {
                    this.$message.error('上传图片失败！');
                }
            },
            beforeAvatarUpload(file) {
                const isRightType = (file.type === 'image/jpeg') || (file.type === 'image/png');
                const isLt2M = file.size / 1024 / 1024 < 2;

                if (!isRightType) {
                    this.$message.error('上传头像图片只能是 JPG 格式!');
                }
                if (!isLt2M) {
                    this.$message.error('上传头像图片大小不能超过 2MB!');
                }
                return isRightType && isLt2M;
            },
            tableRowClassName(row, index) {
                if (index === 1) {
                    return 'info-row';
                } else if (index === 3) {
                    return 'positive-row';
                }
                return '';
            },
            selectActivity() {
                this.$prompt('请输入活动详情', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                }).then(({value}) => {
                    if (value == null) {
                        this.$message({
                            type: 'info',
                            message: '请输入活动详情'
                        });
                        return
                    }
                    let newObj = {};
                    switch (this.activityValue) {
                        case '满减优惠':
                            newObj = {
                                icon_name: '减',
                                name: '满减优惠',
                                description: value,
                            }
                            break;
                        case '优惠大酬宾':
                            newObj = {
                                icon_name: '特',
                                name: '优惠大酬宾',
                                description: value,
                            }
                            break;
                        case '新用户立减':
                            newObj = {
                                icon_name: '新',
                                name: '新用户立减',
                                description: value,
                            }
                            break;
                        case '进店领券':
                            newObj = {
                                icon_name: '领',
                                name: '进店领券',
                                description: value,
                            }
                            break;
                    }
                    this.activities.push(newObj);
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '取消输入'
                    });
                });
            },
            handleDelete(index) {
                this.activities.splice(index, 1)
            },
            submitForm(formName) {
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
</style>
