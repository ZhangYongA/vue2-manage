<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
            <el-table
                :data="tableData"
                highlight-current-row
                style="width: 100%">
                <el-table-column
                    type="index">
                </el-table-column>
                <el-table-column
                    property="userId"
                    label="用户 id">
                </el-table-column>
                <el-table-column
                    property="status"
                    label="审核状态"
                    width="220">
                </el-table-column>
                <el-table-column
                    property="cardNo"
                    label="身份证号">
                </el-table-column>
                <el-table-column
                    label="手持身份证照">
                    <template>
                        <el-button
                            size="small">点击查看
                        </el-button>
                    </template>
                </el-table-column>
                <el-table-column
                    property="picUrl"
                    label="正面身份证照">
                </el-table-column>
                <el-table-column
                    property="commitTime"
                    label="提交时间">
                </el-table-column>
                <el-table-column
                    property="auditTime"
                    label="审核时间">
                </el-table-column>
            </el-table>
            <div class="Pagination" style="text-align: left;margin-top: 10px;">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-size="pageSize"
                    layout="total, prev, pager, next"
                    :total="count">
                </el-pagination>
            </div>
        </div>
    </div>
</template>

<script>
    import headTop from '../../components/headTop'
    import {listIdCardAudit} from '@/api/getData'

    export default {
        data() {
            return {
                tableData: [],
                currentRow: null,
                pageNum: 20,
                pageSize: 1,
                count: 0,
                currentPage: 1,
            }
        },
        components: {
            headTop,
        },
        created() {
            this.initData();
        },
        methods: {
            async initData() {
                try {
                    this.getUsers();
                } catch (err) {
                    console.log('获取数据失败', err);
                }
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.pageNum = (val - 1) * this.pageSize;
                this.getUsers()
            },
            async getUsers() {
                const auditList = await listIdCardAudit(this.currentPage, this.pageSize);
                this.tableData = auditList.data.list
                this.count = auditList.data.total
            },
            lookHandheld() {

            }
        },
    }
</script>

<style lang="less">
    @import '../../style/mixin';

    .table_container {
        padding: 20px;
    }
</style>
