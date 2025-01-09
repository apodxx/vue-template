<template>
<div :id="id" :class="className" :style="{height:height,width:width}" />
</template>

  
  
<script>
import echarts from 'echarts'
import resize from './mixins/resize'

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

    methods: {
        initCharts() {
            this.chart = echarts.init(this.$el);

        },
        setOptions() {
            this.chart.setOption({
                xAxis: {
                    type: 'category',
                    data: this.names
                },
                yAxis: {
                    type: 'value'
                },
                series: [{
                    data: this.values,
                    type: 'line',
                    smooth: true
                }]
            })
        }
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
