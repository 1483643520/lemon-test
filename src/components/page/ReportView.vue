<template>
    <div>
        <!--        外层折叠面板-->
        <div class="collapse">
            <el-collapse v-model="activeNames_1" @change="handleChange">
                <el-collapse-item title="执行汇总" name="1">
                    <!--                    实现执行汇总-->
                    <!--                    左侧列表-->
                    <el-form :label-position="labelPosition" label-width="90px" :model="ExecuteSummarization"style="float: left;width: 50%"
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
            <el-collapse v-model="activeNames_2" @change="handleChange">
                <el-collapse-item title="一致性 Consistency" name="2">
                    <div>与现实生活一致：与现实生活的流程、逻辑保持一致，遵循用户习惯的语言和概念；</div>
                    <div>在界面中一致：所有的元素和结构需保持一致，比如：设计样式、图标和文本、元素的位置等。</div>
                </el-collapse-item>
            </el-collapse>
        </div>
    </div>


</template>


<script>
    import echarts from 'echarts';

    export default {
        data() {
            return {
                activeNames_1: ['1'],
                activeNames_2: ['1'],
                labelPosition: 'right',
                ExecuteSummarization: {
                    name: '',
                    total: '',
                    success: '',
                    field: '',
                    skip: '',
                    start_time: '',
                    end_time: '',
                    canvasId: 'myChart',
                    type: 'pie',
                    width: 600,
                    height: 500,
                    data: [
                        { name: '期刊', value: 1234 },
                        { name: '图书', value: 2345 },
                        { name: '小说', value: 2456 },
                        { name: '文学', value: 333 }
                    ],
                    options: {
                        title: 'example vue-schart',
                        bgColor: '#829dca',
                        titleColor: '#ffffff',
                        legendColor: '#ffffff'
                    }
                },
                charts: '121312213',
                text: '132231123213',  //图表标题
                subtext: '',  //副标题
                opinion: ['男', '女', 'fff'],
                opinionData: [
                    { value: 335, name: '男' },
                    { value: 310, name: '女' },
                    { value: 315, name: 'ddd' }

                ]

            };
        },
        methods: {
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
            }
        },
        mounted() {
            this.$nextTick(function() {
                this.drawPie('main');
            });
        }
    };
</script>

<style>
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
