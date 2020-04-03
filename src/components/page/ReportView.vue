<template>
    <div>
        <!--        外层折叠面板-->
        <div class="collapse">
            <el-collapse v-model="activeNames_1">
                <label for="">执行结果: {{this.ExecuteSummarization.reuslt}}</label>
                <el-collapse-item title="执行汇总 " name="1">
                    <!--                    实现执行汇总-->
                    <!--                    左侧列表-->
                    <el-form :label-position="labelPosition" label-width="90px" :model="ExecuteSummarization"
                             style="float: left;width: 50%"
                    >
                        <el-form-item label="用例名称：" class="green">
                            <el-input v-model="ExecuteSummarization.name" style="width: 70%"
                                      :disabled="true"></el-input>
                        </el-form-item>
                        <el-form-item label="用例总数：" class="green">
                            <el-input v-model="ExecuteSummarization.total" style="width: 70%"
                                      :disabled="true"></el-input>
                        </el-form-item>
                        <el-form-item label="用例通过：" class="green">
                            <el-input v-model="ExecuteSummarization.success" style="width: 70%"
                                      :disabled="true"></el-input>
                        </el-form-item>
                        <el-form-item label="用例失败：" class="red">
                            <el-input v-model="ExecuteSummarization.field" style="width: 70%"
                                      :disabled="true"></el-input>
                        </el-form-item>
                        <el-form-item label="用例跳过：" class="yellow">
                            <el-input v-model="ExecuteSummarization.skip" style="width: 70%"
                                      :disabled="true"></el-input>
                        </el-form-item>
                        <el-form-item label="开始时间：" class="green">
                            <el-input v-model="ExecuteSummarization.start_time" style="width: 70%"
                                      :disabled="true"></el-input>
                        </el-form-item>
                        <el-form-item label="运行时间：" class="green">
                            <el-input v-model="ExecuteSummarization.end_time" style="width: 70%"
                                      :disabled="true"></el-input>
                        </el-form-item>
                    </el-form>
                    <!--                    饼状图-->
                    <div id="main" style="width: 50%;height: 400px;float: right;"></div>

                </el-collapse-item>
            </el-collapse>
        </div>
        <div class="collapse">
            <el-collapse v-model="activeNames_2">
                <el-collapse-item title="详细数据" name="1">
                    <!--                    测试用例筛选-->
                    <div style="float: left">
                        <label for="">测试用例： </label>
                        <el-select v-model="select_case" slot="prepend" placeholder="请选择用例" style="width: 70%"
                                   @change="search_case()">
                            <el-option label="请选择" :value=null></el-option>
                            <el-option :label=obj.name :value=obj.value v-for="obj in case_list"></el-option>
                        </el-select>
                    </div>
                    <!--                    执行结果筛选-->
                    <div style="float: left;margin-left: 1%">
                        <label for="">执行结果： </label>
                        <el-select v-model="select_result" slot="prepend" placeholder="请选择执行结果" style="width: 70%"
                                   @change="search_case()">
                            <el-option label="请选择" :value=null></el-option>
                            <el-option :label=obj.name :value=obj.value v-for="obj in result_list"></el-option>
                        </el-select>
                    </div>
                    <!--                    右侧统计-->
                    <div style="float: right;width: 30%">
                        <label style="color: #67C23A;font-weight: bold">用例数： {{ExecuteSummarization.total}}</label>
                        <el-divider direction="vertical"></el-divider>
                        <span style="color: #67C23A;font-weight: bold">成功： {{ExecuteSummarization.success}}</span>
                        <el-divider direction="vertical"></el-divider>
                        <span style="color: #F56C6C;font-weight: bold">失败： {{ExecuteSummarization.field}}</span>
                        <el-divider direction="vertical"></el-divider>
                        <span style="color: #E6A23C;font-weight: bold">跳过： {{ExecuteSummarization.skip}}</span>
                    </div>
                    <!--                    下方表格数据-->
                    <el-table
                            :data="serach_details"
                            >
                        <el-table-column type="expand" >
                            <template slot-scope="props">
                                <el-form label-position="left" inline class="demo-table-expand">
                                    <el-form-item label="url">
                                        <span>{{ props.row.record.meta_datas.data[0].request.url }}</span>
                                    </el-form-item>
                                    <el-form-item label="method">
                                        <span>{{ props.row.record.meta_datas.data[0].request.method }}</span>
                                    </el-form-item>
                                    <el-form-item label="status_code">
                                        <span>{{ props.row.record.meta_datas.data[0].response.status_code }}</span>
                                    </el-form-item>
                                    <el-form-item label="request_headers">
                                        <span>{{ props.row.record.meta_datas.data[0].request.headers }}</span>
                                    </el-form-item>
                                    <el-form-item label="request_body">
                                        <span>{{ props.row.record.meta_datas.data[0].request.body }}</span>
                                    </el-form-item>
                                    <el-form-item label="response_headers">
                                        <span>{{ props.row.record.meta_datas.data[0].response.headers }}</span>
                                    </el-form-item>
                                    <el-form-item label="response_body">
                                        <span>{{ props.row.record.meta_datas.data[0].response.body }}</span>
                                    </el-form-item>
                                    <el-form-item label="error">
                                        <span>{{ props.row.record.attachment }}</span>
                                    </el-form-item>
                                </el-form>
                            </template>
                        </el-table-column>
                        <el-table-column
                                label="编号"
                                prop="case" align="center" width="45%" >
                        </el-table-column>
                        <el-table-column
                                label="用例名称"
                                prop="record.name" align="center" show-overflow-tooltip=true>
                        </el-table-column>
                        <el-table-column
                                label="请求数据"
                                prop="record.meta_datas.data[0].request.body" align="center" :show-overflow-tooltip="true">
                        </el-table-column >
                        <el-table-column
                                label="返回数据"
                                prop="record.meta_datas.data[0].response.body" align="center" :show-overflow-tooltip="true">
                        </el-table-column>
                        <el-table-column
                                label="耗时"
                                prop="record.meta_datas.stat.elapsed_ms" align="center" width="80%">
                        </el-table-column>
                        <el-table-column
                                label="结果"
                                prop="result" align="center" width="80%">
                        </el-table-column>
                    </el-table>

                </el-collapse-item>
            </el-collapse>
        </div>
    </div>


</template>


<script>
    import echarts from 'echarts';
    import { configures_list, report_view } from '../../api/api';

    export default {
        beforeRouteEnter(to, from, next) {
            next(vm => {
                vm.report_id = vm.$route.params.id;
                vm.getData();
            });
            next();
        },
        beforeRouteUpdate(to, from, next) {
            this.report_id = to.params.id;
            this.getData();
            next();
        },
        data() {
            return {
                activeNames_1: ['1'],
                activeNames_2: ['1'],
                labelPosition: 'right',
                ExecuteSummarization: {
                    reuslt: '',
                    name: '',
                    total: '',
                    success: '',
                    field: '',
                    skip: '',
                    start_time: '',
                    end_time: '',
                    details: Array  // 所有执行的用例详细信息
                },
                charts: '',
                text: '',  // 图表标题
                subtext: '',  // 副标题
                opinion: ['成功', '失败', '跳过'],  // 图例
                opinionData: [],
                select_case: null,  // 当前选择用例 value
                case_list: [],   // 待选用例列表 格式 [{"value":用例id, "name":用例名称}],
                select_result: null,  // 当前选择结果 value
                result_list: [],   // 待选用例列表 格式 [{"value":结果标识, "name":标识名称}],
                case_details: [],  // 用例详情  格式 ["result": 结果标识, "case": 用例标识, "record": 具体详情]
                serach_details: [] // 筛选后的项目列表

            };
        },
        methods: {
            // 地图数据组装
            drawPie(id) {
                this.charts = echarts.init(document.getElementById(id));
                this.charts.setOption({
                    title: {
                        text: this.text,
                        subtext: this.subtext,
                        left: 'center'
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: '{a} <br/>{b}: {c} ({d}%)'
                    },
                    legend: {
                        orient: 'vertical',
                        left: 10,
                        data: this.option
                    },
                    series: [
                        {
                            name: '测试结果',
                            type: 'pie',
                            radius: '65%',
                            center: ['60%', '47%'],
                            data: this.opinionData,
                            emphasis: {
                                itemStyle: {
                                    shadowBlur: 10,
                                    shadowOffsetX: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                }
                            }
                        }
                    ]
                });
            },
            //    获取信息
            getData() {
                report_view(this.report_id)
                    .then(response => {
                        // console.log("html report");
                        response.data.summary = JSON.parse(response.data.summary);
                        // console.log(response.data);
                        this.ExecuteSummarization.reuslt = response.data.summary.success;
                        this.ExecuteSummarization.name = response.data.name;
                        this.ExecuteSummarization.total = response.data.count;
                        this.ExecuteSummarization.success = response.data.success;
                        this.ExecuteSummarization.field = response.data.summary.stat.teststeps.errors;
                        this.ExecuteSummarization.skip = response.data.summary.stat.teststeps.skipped;
                        this.ExecuteSummarization.start_time = response.data.summary.time.start_at;
                        this.ExecuteSummarization.end_time = response.data.summary.time.duration;
                        this.ExecuteSummarization.details = response.data.summary.details;
                        this.case_list = response.data.case_list;
                        this.result_list = response.data.result_list;
                        this.text = response.data.name;
                        this.case_details = response.data.case_details;
                        this.serach_details = response.data.case_details;
                        // console.log(this.case_details)
                        this.opinionData = [{
                            'value': response.data.success,
                            'name': '成功'
                        }, { 'value': response.data.summary.stat.teststeps.errors, 'name': '失败' }, {
                            'value': response.data.summary.stat.teststeps.skipped,
                            'name': '跳过'
                        }];

                        // 调取地图
                        this.$nextTick(function() {
                            console.log('sdads');
                            this.drawPie('main');
                        });

                    })
                    .catch(error => {
                        if (typeof error === 'object' && error.hasOwnProperty('status_code')) {
                            if (error.status_code === 404) {
                                this.$router.push({ name: '404' });
                            }
                        } else {
                            this.$message.error('服务器错误');
                        }
                    });
            },
            // 查询触发事件
            search_case() {
                // console.log(this.select_case);
                // console.log(this.select_result);
                // 从组表格列表
                var i = 0;
                if (this.select_case != null | this.select_result != null) {
                    this.serach_details = [];
                    for (i in this.case_details) {
                        if (this.select_case) {
                            if (this.select_result) {
                                if (this.select_case == this.case_details[i].case && this.select_result == this.case_details[i].result) {
                                    this.serach_details.push(this.case_details[i]);
                                }
                            } else {
                                if (this.select_case == this.case_details[i].case) {
                                    this.serach_details.push(this.case_details[i]);
                                }
                            }
                        } else {
                            console.log('454');
                            if (this.select_result) {
                                console.log(this.select_result);
                                if (this.select_result == this.case_details[i].result) {
                                    this.serach_details.push(this.case_details[i]);
                                }
                            }
                        }
                    }
                } else {
                    this.serach_details = this.case_details;
                }
                console.log(this.serach_details);
                console.log(this.serach_details[0].record.meta_datas_expanded[0].data[0].response.body);
            }


        },
        mounted() {
            // 地图调用
            this.$nextTick(function() {
                console.log('sdads');
                this.drawPie('main');
            });
        }
    };
</script>

<style>
    .demo-table-expand {
        font-size: 0;
    }
    .demo-table-expand label {
        width: 10%;
        color: #99a9bf;
    }
    .demo-table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 100%;
    }
    .collapse {
        cursor: move;
        margin-top: 5px;
        -moz-border-bottom-colors: none;
        -moz-border-left-colors: none;
        -moz-border-right-colors: none;
        -moz-border-top-colors: none;
        background-color: #fff;
        border-color: #e7eaec;
        -webkit-border-image: none;
        -o-border-image: none;
        border-image: none;
        border-style: solid solid none;
        border-width: 4px 0 0;
        color: inherit;
        margin-bottom: 0;
        padding: 14px 15px 7px;
        min-height: 48px;
        margin-bottom: 15px;

    }

    .green .el-form-item__label {
        color: #67C23A;
        font-weight: bold;
    }

    .red .el-form-item__label {
        color: #F56C6C;
        font-weight: bold;
    }

    .yellow .el-form-item__label {
        color: #E6A23C;
        font-weight: bold;
    }


</style>
