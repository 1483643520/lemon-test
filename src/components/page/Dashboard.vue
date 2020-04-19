<template>
    <div>
        <el-row :gutter="20">
            <el-col :span="8">
                <el-card shadow="hover" class="mgb20" style="height:252px;">
                    <div class="user-info">
                        <img src="../../assets/img/img.jpg" class="user-avator" alt/>
                        <div class="user-info-cont">
                            <div class="user-info-name">{{user.username}}</div>
                            <div>{{user.role}}</div>
                        </div>
                    </div>

                    <div class="user-info-list">
                        用户创建时间：
                        <span>{{user.date_joined}}</span>
                    </div>
                    <div class="user-info-list">
                        上次登录时间：
                        <span>{{user.last_login}}</span>
                    </div>
                </el-card>
                <el-card shadow="hover" style="height:252px;">
                    <div slot="header" class="clearfix">
                        <span>执行结果详情</span>
                    </div>
                    成功率
                    <el-progress :percentage=statistics.success_rate color="#42b983"></el-progress>
                    跳过率
                    <el-progress :percentage=statistics.fail_rate color="#f1e05a"></el-progress>
                    失败率
                    <el-progress :percentage=statistics.skip_rate color="#f56c6c"></el-progress>
                </el-card>
            </el-col>
            <el-col :span="16">
                <el-row :gutter="20" class="mgb20">
                    <el-col :span="8">
                        <el-card shadow="hover" :body-style="{padding: '0px'}">
                            <div class="grid-content grid-con-1">
                                <i class="el-icon-s-management grid-con-icon"></i>
                                <div class="grid-cont-right">
                                    <div class="grid-num">{{statistics.projects_count}}</div>
                                    <div>项目总数</div>
                                </div>
                            </div>
                        </el-card>
                    </el-col>
                    <el-col :span="8">
                        <el-card shadow="hover" :body-style="{padding: '0px'}">
                            <div class="grid-content grid-con-2">
                                <i class="el-icon-s-ticket grid-con-icon"></i>
                                <div class="grid-cont-right">
                                    <div class="grid-num">{{statistics.testcases_count}}</div>
                                    <div>用例总数</div>
                                </div>
                            </div>
                        </el-card>
                    </el-col>
                    <el-col :span="8">
                        <el-card shadow="hover" :body-style="{padding: '0px'}">
                            <div class="grid-content grid-con-3">
                                <i class="el-icon-s-order grid-con-icon"></i>
                                <div class="grid-cont-right">
                                    <div class="grid-num">{{statistics.reports_count}}</div>
                                    <div>报告总数</div>
                                </div>
                            </div>
                        </el-card>
                    </el-col>
                </el-row>
                <el-card shadow="hover" style="height:403px;">
                    <div slot="header" class="clearfix">
                        <span>待办事项</span>
                        <el-button style="float: right; padding: 3px 0" type="text" @click="augment">添加</el-button>


                    </div>
                    <el-table :show-header="false" :data="todoList" style="width:100%;" max-height="300">
                        <el-table-column width="40">
                            <template slot-scope="scope">
                                <el-checkbox v-model="scope.row.status" @change="checkbox(scope.row)"></el-checkbox>
                            </template>
                        </el-table-column>
                        <el-table-column>
                            <template slot-scope="scope">
                                <div
                                        class="todo-item"
                                        :class="{'todo-item-del': scope.row.status}"
                                >{{scope.row.text}} -- {{scope.row.update_time}}
                                </div>
                            </template>
                        </el-table-column>
                        <el-table-column width="100">
                            <template slot-scope="scope">
                                <el-button
                                        type="text"
                                        size="mini"
                                        @click="editing(scope.row)">编辑
                                </el-button>
                                <el-button
                                        type="text"
                                        size="mini"
                                        @click="deleted(scope.row)" style="color: red">删除
                                </el-button>
                            </template>
                        </el-table-column>
                    </el-table>
                </el-card>
            </el-col>
        </el-row>
        <el-row :gutter="20">
            <el-col :span="12">
                <el-card shadow="hover">
                    <span style="font-family: 刻石录颜体; font-weight: bold">{{cartogram_first.year_now}}年度数据详情 <br></span>
                    <br>
                    <div id="histogram" style="width: 100%;height: 400px;float: right;"></div>
                </el-card>
            </el-col>
            <el-col :span="12">
                <el-card shadow="hover">
                    <span style="font-family: 刻石录颜体; font-weight: bold">{{cartogram_seconde.year_now}}年度执行结果详情 <br></span>
                    <br>
                    <div id="line_graph" style="width: 100%;height: 400px;float: right;"></div>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    import Schart from 'vue-schart';
    import echarts from 'echarts';
    import { backlog_list, summary, delete_backlog, edit_backlog, add_backlog, add_env } from '../../api/api';

    export default {
        name: 'dashboard',
        data() {
            return {
                user: Object,
                statistics: Object,
                cartogram_first: Object,
                cartogram_seconde: Object,
                todoList: []
            };
        },
        components: {
            Schart
        },
        beforeRouteEnter(to, from, next) {
            // 在路由跳转页面实例化之前被调用, 此时还没有this实例, 但是可以通过next的回调中获取实例的引用
            next(vm => {
                vm.getData();
            });
            next();
        },
        created() {
            this.getData();
        },
        methods: {
            // 获取数据
            getData() {
                summary()
                    .then(response => {
                        this.user = response.data.user;
                        this.statistics = response.data.statistics;
                        this.cartogram_first = response.data.cartogram_first;
                        this.cartogram_seconde = response.data.cartogram_seconde;
                        // 获取报告详情
                        this.backlog();
                        // 地图调用
                        this.$nextTick(function() {
                            this.drawPie('histogram');
                            this.drawLine('line_graph');
                        });
                    })
                    .catch(error => {
                        this.$message.error('服务器错误');
                    });

            },
            backlog() {
                backlog_list({ 'user_id': this.user.user_id })
                    .then(response => {
                        this.todoList = response.data.results;
                        console.log(this.todoList);
                    })
                    .catch(error => {
                        this.$message.error('服务器错误');
                    });
            },
            // 柱状图
            drawPie(id) {
                this.charts = echarts.init(document.getElementById(id));
                this.charts.setOption({

                    tooltip: {

                        trigger: 'axis',
                        axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                            type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                        }
                    },
                    legend: {
                        data: ['项目数量', '接口数量', '套件数量', '用例数量', '报告数量', '函数文件数量']
                    },
                    grid: {
                        left: '1%',
                        right: '1%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: [
                        {
                            type: 'category',
                            data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
                        }
                    ],
                    yAxis: [
                        {
                            type: 'value'
                        }
                    ],
                    series: this.cartogram_first.series
                });
            },
            // 综合图
            drawLine(id) {
                this.charts = echarts.init(document.getElementById(id));
                this.charts.setOption({
                    grid: {
                        left: '0%',
                        right: '7%',
                        bottom: '3%',
                        containLabel: true
                    },
                    tooltip: {
                        trigger: 'axis'
                    },
                    legend: {
                        data: ['成功', '跳过', '失败']
                    },
                    toolbox: {
                        show: true,
                        feature: {
                            dataView: { show: true, readOnly: false },
                            magicType: { show: true, type: ['line', 'bar'] },
                            restore: { show: true },
                            saveAsImage: { show: true }
                        }
                    },
                    calculable: true,
                    xAxis: [
                        {
                            type: 'category',
                            data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
                        }
                    ],
                    yAxis: [
                        {
                            type: 'value'
                        }
                    ],
                    series: this.cartogram_seconde.series
                });
            },
            // 添加待办事项
            augment() {
                this.$prompt('待办事项：', '新增', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    inputPattern: /^[\s\S]*.*[^\s][\s\S]*$/,
                    inputErrorMessage: '待办事项必填'
                }).then(({ value }) => {
                    var form = {
                        'user': this.user.user_id,
                        'text': value
                    };
                    add_backlog(form)
                        .then((response) => {
                            this.backlog();
                            this.$message({
                                type: 'success',
                                message: '新增完成: ' + value
                            });
                        });
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '取消新增'
                    });
                });
            },
            // 编辑待办事项
            editing(datas) {
                this.$prompt('待办事项：', '修改', {
                    inputValue: datas.text,
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    inputPattern: /^[\s\S]*.*[^\s][\s\S]*$/,
                    inputErrorMessage: '待办事项必填'
                }).then(({ value }) => {
                    var form = {
                        'text': value
                    };
                    edit_backlog(datas.id, form)
                        .then((response) => {
                            this.backlog();
                            this.$message({
                                type: 'success',
                                message: '修改完成: ' + value
                            });
                        });

                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '取消修改'
                    });
                });
            },
            checkbox(value) {
                console.log(value);
                if(value.status == true){
                    var form = {
                        'status': 1
                    };
                }
                if(value.status == false){
                    var form = {
                        'status': 0
                    };
                }
                edit_backlog(value.id, form)
                    .then((response) => {
                        this.backlog();
                    });
            },
            // 删除待办事项
            deleted(value) {
                this.$confirm('确认删除当前待办事项?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    delete_backlog(value.id)
                        .then((response) => {
                            this.backlog();
                            this.$message({
                                type: 'success',
                                message: '删除成功!'
                            });
                        });

                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            // 关闭新增页面,弹出消息通知
            handleClose(done) {
                this.$message('取消当前操作！');
                done();

            }


        },
        mounted() {
            // 地图调用
            this.$nextTick(function() {
                this.drawPie('histogram');
                this.drawLine('line_graph');
            });

        }


    };
</script>


<style scoped>
    .el-row {
        margin-bottom: 20px;
    }

    .grid-content {
        display: flex;
        align-items: center;
        height: 100px;
    }

    .grid-cont-right {
        flex: 1;
        text-align: center;
        font-size: 14px;
        color: #999;
    }

    .grid-num {
        font-size: 30px;
        font-weight: bold;
    }

    .grid-con-icon {
        font-size: 50px;
        width: 100px;
        height: 100px;
        text-align: center;
        line-height: 100px;
        color: #fff;
    }

    .grid-con-1 .grid-con-icon {
        background: rgb(45, 140, 240);
    }

    .grid-con-1 .grid-num {
        color: rgb(45, 140, 240);
    }

    .grid-con-2 .grid-con-icon {
        background: rgb(100, 213, 114);
    }

    .grid-con-2 .grid-num {
        color: rgb(45, 140, 240);
    }

    .grid-con-3 .grid-con-icon {
        background: rgb(242, 94, 67);
    }

    .grid-con-3 .grid-num {
        color: rgb(242, 94, 67);
    }

    .user-info {
        display: flex;
        align-items: center;
        padding-bottom: 20px;
        border-bottom: 2px solid #ccc;
        margin-bottom: 20px;
    }

    .user-avator {
        width: 120px;
        height: 120px;
        border-radius: 50%;
    }

    .user-info-cont {
        padding-left: 50px;
        flex: 1;
        font-size: 14px;
        color: #999;
    }

    .user-info-cont div:first-child {
        font-size: 30px;
        color: #222;
    }

    .user-info-list {
        font-size: 14px;
        color: #999;
        line-height: 25px;
    }

    .user-info-list span {
        margin-left: 70px;
    }

    .mgb20 {
        margin-bottom: 20px;
    }

    .todo-item {
        font-size: 14px;
    }

    .todo-item-del {
        text-decoration: line-through;
        color: #999;
    }

    .schart {
        width: 100%;
        height: 300px;
    }
</style>
