<template>
<div :id="id" :class="className" :style="{height:height,width:width}" />
</template>

    
    
<script>
import echarts from 'echarts'
import resize from './mixins/resize'
import axios from 'axios'

export default {
    mixins: [resize],
    props: {
        className: {
            type: String,
            default: 'chart'
        },
        id: {
            type: String,
            default: 'chart'
        },
        width: {
            type: String,
            default: '200px'
        },
        height: {
            type: String,
            default: '200px'
        }
    },
    data() {
        return {
            chart: null,
            names: [],
            values: []
        }
    },
    created() {
        axios.get("http://localhost:8081/analysis/analyze1")
            .then((response) => {
                let res = response.data.data;
                console.log(res);
                res.forEach(item => {
                    this.names.push(item.name);
                    this.values.push(item.value);
                });
                // 数据更新后调用 setOptions 方法
                this.setOptions();
            })
            .catch((error) => {
                console.error("Axios error:", error);
            });
    },
    mounted() {
        this.initCharts();
    },
    beforeDestroy() {
        if (!this.chart) {
            return
        }
        this.chart.dispose()
        this.chart = null
    },

    methods: {
        initCharts() {
            this.chart = echarts.init(this.$el);

        },

        setOptions() {
            console.log("-----------");
            console.log(this.names);
            console.log("-----------");
            this.chart.setOption({
                title: {
                    text: 'Referer of a Website',
                    subtext: 'Fake Data',
                    left: 'center'
                },
                tooltip: {
                    trigger: 'item'
                },
                legend: {
                    orient: 'vertical',
                    left: 'left'
                },
                series: [{
                    name: 'Access From',
                    type: 'pie',
                    radius: '50%',
                    data: [{
                            value: 1048,
                            name: 'Search Engine'
                        },
                        {
                            value: 735,
                            name: 'Direct'
                        },
                        {
                            value: 580,
                            name: 'Email'
                        },
                        {
                            value: 484,
                            name: 'Union Ads'
                        },
                        {
                            value: 300,
                            name: 'Video Ads'
                        }
                    ],
                    emphasis: {
                        itemStyle: {
                            shadowBlur: 10,
                            shadowOffsetX: 0,
                            shadowColor: 'rgba(0, 0, 0, 0.5)'
                        }
                    }
                }]
            })
        },

    },
    watch: {
        options: {
            handler(options) {
                this.chart.setOption(this.options)
            },
            deep: true
        },
    }
}
</script>
