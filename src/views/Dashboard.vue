<script setup>
import { ref,onMounted } from 'vue'
import  * as echarts from 'echarts'
const chartRef = ref(null)
const chartRef2 = ref(null)
onMounted(async () => {
    // 1.先请求用户数据
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    const users = await response.json()

    // 2.做个真实统计：每个用户名字的首字母，统计各首字母有几个用户
    // 2.1 先统计首字母出现的次数
    const letterCount = {}
    users.forEach(user => {
        const firstLetter = user.name[0].toUpperCase()
        if (letterCount[firstLetter]) {
            letterCount[firstLetter]++
        } else {
            letterCount[firstLetter] = 1
        }
    })

    // 2.2 把统计结果转成echarts需要的数据格式
    const chartData = []
    for (const letter in letterCount) {
        chartData.push({ name: letter, value: letterCount[letter] })
    }

    // 3.初始化echarts
    const chart = echarts.init(chartRef.value)

    // 4.配置echarts
    const option = {
        title: {
            text: '用户名字首字母统计'
        },
        tooltip: {},
        xAxis: {
            data: chartData.map(item => item.name)
        },
        yAxis: {},
        series: [{
            name: '用户数',
            type: 'bar',
            data: chartData.map(item => item.value)
        }]
    }
    chart.setOption(option)

    const chart2 = echarts.init(chartRef2.value)
    chart2.setOption({
        title:{ text:'用户分布'},
        series:[
            {
                type:'pie',
                data:[
                    {name:'北京',value:100},
                    {name:'上海',value:200},
                    {name:'广州',value:300},
                    {name:'深圳',value:400},
                    {name:'杭州',value:500},
                    {name:'成都',value:600},
                    {name:'武汉',value:700},
                ]
            }
        ]
    })
})
</script>

<template>
    <div>
        <h2>仪表盘</h2>
        <div ref="chartRef" style="width: 600px; height: 400px;"></div>
        <div ref="chartRef2" style="width: 600px; height: 400px;"></div>
    </div>
</template>