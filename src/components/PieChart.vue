<template>
  <div>
    <highcharts :options="pieChartOptions" ref="chart"></highcharts>
    <!--<highcharts :options="barChartOptions" ref="barChart"></highcharts>-->
    <highcharts :options="areaChartOptions" ref="areaChart"></highcharts>
    <highcharts :options="areaChart2Options" ref="areaChart2"></highcharts>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
    props: {
        data: Array
    },
    created(){
        const countryHash = {};
        const genderHash = {};
        const yearsHash = {};
        this.data.forEach(elem=>{
            elem.splice(0,1);
        });
        this.data[2].forEach((elem,index) => {
            if(!countryHash[elem]){
                countryHash[elem] = 0;
                genderHash[elem] = {};
            }
            if(!genderHash[elem][this.data[6][index]])
                genderHash[elem][this.data[6][index]] = 0;
            const date = this.data[5][index].split('-');
            const year = parseInt(date[2]) < 22 ? `20${date[2]}` : `19${date[2]}`;
            this.data[5][index] = year;
            genderHash[elem][this.data[6][index]]++;
            countryHash[elem]++;
        });
        this.pieChartOptions.series[0].data = Object.entries(countryHash);
        this.barChartOptions.xAxis.categories = Object.keys(genderHash);
        const males = Object.values(genderHash).map(elem => {
            if(elem['Masculino'])
                return elem['Masculino'];
            return 0;
        });
        const females = Object.values(genderHash).map(elem => {
            if(elem['Femenino'])
                return elem['Femenino'];
            return 0;
        });
        this.data[5].forEach( (elem,index) => {
            if(!yearsHash[elem])
                yearsHash[elem] = {};
            if(!yearsHash[elem][this.data[2][index]])
                yearsHash[elem][this.data[2][index]] = 0;
            yearsHash[elem][this.data[2][index]]++;
        });
        Object.keys(yearsHash).forEach(year=>{
            Object.keys(countryHash).forEach(country=>{
                if(!yearsHash[year][country])
                    yearsHash[year][country] = 0;
            })
        });
        const sortedData = Object.entries(yearsHash).sort((a,b)=>{
            if(parseInt(a[0]) < parseInt(b[0]))
                return -1;
            if(parseInt(a[0]) > parseInt(b[0]))
                return 1;
            return 0;
        });
        const countryYearHash =  {};
        sortedData.map(elem => {
            Object.keys(elem[1]).forEach(country=>{
                if(!countryYearHash[country])
                    countryYearHash[country] = [];
                countryYearHash[country].push(elem[1][country]);
            });
        });
        Object.entries(countryYearHash).forEach((elem: [string,number[]])=>{
            for (let index = 0; index < elem[1].length-1; index++) {
                elem[1][index+1] += elem[1][index];
            }
        });
        const areaChartSeries = Object.entries(countryYearHash).map(elem => {
            return {
                name: elem[0],
                data: elem[1]
            }
        });
        const areaChartCategories = sortedData.map(elem=>elem[0]);
        this.areaChartOptions.xAxis.categories = areaChartCategories;
        this.areaChartOptions.series = areaChartSeries;
        this.areaChart2Options.xAxis.categories = areaChartCategories;
        this.areaChart2Options.series = areaChartSeries;
        this.barChartOptions.series[0].data = males;
        this.barChartOptions.series[1].data = females;
    },
    data() {
        return {
            pieChartOptions: {
                chart: {
                    type: "pie",
                    options3d: {
                        enabled: true,
                        alpha: 45
                    }
                },
                title: {
                    text: "Distribucion de records por pais"
                },
                series: [
                    {
                        name: "Records",
                        data: []
                    }
                ]
            },
            barChartOptions: {
                title: {
                    text: null
                },
                chart: {
                    type: "bar"
                },
                subtitle: {
                    text: 'Por sexo'
                },
                xAxis: {
                    categories: [],
                    title: {
                        text: null
                    }
                },
                yAxis: {
                    min: 0,
                    title: {
                        text: "Records",
                        align: "high"
                    },
                    labels: {
                        overflow: "justify"
                    }
                },
                plotOptions: {
                    bar: {
                        dataLabels: {
                            enabled: true
                        }
                    }
                },
                series: [
                    {
                        name: 'Masculino',
                        data: []
                    },
                    {
                        name: 'Femenino',
                        data: []
                    }
                ]
            },
            areaChartOptions: {
                chart: {
                    type: 'area'
                },
                title: {
                    text: 'Records acumulados por paises a lo largo del tiempo'
                },
                subtitle: {
                    text: null
                },
                xAxis: {
                    categories: [],
                    tickmarkPlacement: 'on',
                    title: {
                        enabled: false
                    }
                },
                yAxis: {
                    title: {
                        text: 'Records'
                    },
                },
                tooltip: {
                    split: true
                },
                plotOptions: {
                    area: {
                        stacking: 'normal',
                        marker: {
                            enabled: false,
                            symbol: "circle",
                            radius: 2,
                            states: {
                                hover: {
                                    enabled: false
                                }
                            }
                        }
                    }
                },
                series: []
            },
            areaChart2Options: {
                chart: {
                    type: 'area'
                },
                title: {
                    text: 'Records acumulados por paises a lo largo del tiempo'
                },
                subtitle: {
                    text: null
                },
                xAxis: {
                    categories: [],
                    tickmarkPlacement: 'on',
                    title: {
                        enabled: false
                    }
                },
                yAxis: {
                    title: {
                        text: 'Records'
                    },
                },
                tooltip: {
                    split: true
                },
                plotOptions: {
                    area: {
                        marker: {
                            enabled: false,
                            symbol: "circle",
                            radius: 2,
                            states: {
                                hover: {
                                    enabled: false
                                }
                            }
                        }
                    }
                },
                series: []
            }
        };
    }
});
</script>