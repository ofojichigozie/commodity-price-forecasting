<template>
    <div class="main">
        <header class="header p-4 d-flex justify-content-between">
            <span style="color: rgb(158, 35, 35); font-weight: bold;">{{ logo }}</span>
            <button class="btn btn-outline-danger" v-on:click="logout()">Logout</button>
        </header>

        <section class="landing">
            <h1>PRICE FORECASTING</h1>
            <p style="color: #FFFF00;">For Agricultural Commodity</p> 
            <p>(Rice, Corn and Soybeans as case studies)</p> 
        </section>

        <main class="container mt-4">
            <section class="row">
                <div class="col-12 col-md-8">
                    <div class="form-div mb-2">
                        <h4 class="h4 font-weight-bold">
                            Historic Prices - 
                            <span :class="textColor">
                                ({{ commodity }})
                            </span>
                        </h4>
                        <p class="mb-4">
                            Historic prices of <span class="text-danger font-weight-bold">rice</span>, <span class="text-success font-weight-bold">corn</span> and <span class="text-warning font-weight-bold">soybean</span> from May, 1996 are available. 
                            Select a commodity and start month and year to filter the data.
                        </p>
                        <div>
                            <label for="filter-date" class="font-weight-bold">Commodity:  </label>
                            <select v-model="commodity" @change="handleSelectCommodity" style="padding: 3px;">
                                <option value="Rice">Rice</option>
                                <option value="Corn">Corn</option>
                                <option value="Soybean">Soybean</option>
                            </select>
                        </div>
                        <div class="d-flex justify-content-between">
                            <div>
                                <label for="filter-date" class="font-weight-bold">Filter:  </label>
                                <input type="month" id="filter-date" v-model="monthYear" @change="filterCommodityHistoricData()">
                            </div>
                            <a href="#" :class="`btn ${buttonOutineColor} btn-sm`" @click.prevent="viewAll">View all</a>
                        </div>
                    </div>

                    <div v-if="isDataAvailable == true">
                        <div style="overflow-x: auto;">
                            <table class="table table-bordered table-striped">
                                <thead>
                                    <th>S/N</th>
                                    <th>Month</th>
                                    <th>Price</th>
                                    <th>% Change</th>
                                </thead>
                                <tbody>
                                    <tr v-for="(riceHistoricDatum, index) in filteredCommodityHistoricData" :key="index">
                                        <td> {{ ++index }} </td>
                                        <td> {{ riceHistoricDatum.month }} </td>
                                        <td> {{ riceHistoricDatum.price }} </td>
                                        <td> {{ riceHistoricDatum.change }} </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
                <div class="col-12 col-md-4">
                    <div class="card">
                        <div class="card-header text-center bg-dark">
                            <h5 class="h5 font-weight-bold text-light">Predict Future Price</h5>
                        </div>
                        <div class="card-body">
                            <h6 class="h6 font-weight-bold">Get prediction by:</h6>
                            <div class="radio">
                                <label><input type="radio" name="period" @click="isMonthOrYearSelected = 'MONTHS'" checked> Months</label>
                            </div>
                            <div class="radio">
                                <label><input type="radio" name="period" @click="isMonthOrYearSelected = 'YEARS'"> Years</label>
                            </div>
                            <hr>
                            <div v-if="isMonthOrYearSelected == 'MONTHS'">
                                <label for="">Select month</label>
                                <select class="form-control" v-model="predictionMonth">
                                    <option v-for="(month, index) in months" :key="index" :value="month">{{ month }}</option>
                                </select>
                            </div>
                            <div v-if="isMonthOrYearSelected == 'YEARS'" class="mt-2">
                                <label for="">Enter year</label>
                                <input class="form-control" type="number" v-model="predictionYear">
                            </div>
                            <div class="mt-2">
                                <button :class="`form-control btn ${buttonColor}`" @click="predictCommodityPrice">Predict</button>
                            </div>
                            <div v-show="message != ''">
                                <hr>
                                <h6 class="font-weight-bold text-center">{{ message }}</h6>
                                <div class="d-flex justify-content-center">
                                    <h4 class="bg-dark text-white p-2 rounded-circle">{{ predictedPrice }}</h4>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
        </main>
    </div>
</template>

<script>
    import MovingAverage from '../modules/MovingAverage';
    import HistoricRicePrices from '../data/historicRicePrices';
    import HistoricCornPrices from '../data/historicCornPrices';
    import HistoricSoybeanPrices from '../data/historicSoybeanPrices';

    export default {
        name: 'Home',

        data(){
            return {
                logo: 'Price Forecasting',
                commodity: "Rice",
                monthYear: '2020-06',
                months: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
                isMonthOrYearSelected: 'MONTHS',
                predictionMonth: 'January',
                predictionYear: '2022',
                unfilteredCommodityHistoricData: [],
                filteredCommodityHistoricData: [],
                isDataAvailable: true,
                predictedPrice: '',
                message: ''
            }
        },

        computed: {
            monthYearString(){
                const monthYearArray = this.monthYear.split('-');
                let monthIndex = parseInt(monthYearArray[1]) - 1;
                return `${this.months[monthIndex].substring(0, 3)} ${monthYearArray[0]}`;
            },

            textColor(){
                switch(this.commodity){
                    case "Rice":
                        return "text-danger";
                    case "Corn":
                        return "text-success";
                    case "Soybean":
                        return "text-warning";
                    default:
                        return "text-primary";
                }
            },

            buttonOutineColor(){
                switch(this.commodity){
                    case "Rice":
                        return "btn-outline-danger";
                    case "Corn":
                        return "btn-outline-success";
                    case "Soybean":
                        return "btn-outline-warning";
                    default:
                        return "btn-outline-primary";
                }
            },

            buttonColor(){
                switch(this.commodity){
                    case "Rice":
                        return "btn-danger";
                    case "Corn":
                        return "btn-success";
                    case "Soybean":
                        return "btn-warning";
                    default:
                        return "btn-primary";
                }
            }
        },

        mounted(){
            this.unfilteredCommodityHistoricData = HistoricRicePrices;

            let startIndex = this.unfilteredCommodityHistoricData.findIndex(data => {
                return data.month == 'Jun 2020'
            });
            let stopIndex = this.unfilteredCommodityHistoricData.length + 1;
            this.filteredCommodityHistoricData = this.unfilteredCommodityHistoricData.slice(startIndex, stopIndex);
        },

        methods: {
            viewAll(){
                this.filteredCommodityHistoricData = this.unfilteredCommodityHistoricData;
            },

            handleSelectCommodity: function(){
                switch(this.commodity){
                    case "Rice":
                        this.unfilteredCommodityHistoricData = HistoricRicePrices;
                        break;
                    case "Corn":
                        this.unfilteredCommodityHistoricData = HistoricCornPrices;
                        break;
                    case "Soybean":
                        this.unfilteredCommodityHistoricData = HistoricSoybeanPrices;
                        break;
                    default:
                        this.unfilteredCommodityHistoricData = [];
                }

                this.filterCommodityHistoricData();
            },

            filterCommodityHistoricData: function(){
                let startIndex = this.unfilteredCommodityHistoricData.findIndex(data => {
                    return data.month == this.monthYearString
                });

                if(startIndex == -1){
                    alert("There is no record for the start date");
                    return;
                }

                let stopIndex = this.unfilteredCommodityHistoricData.length + 1;
                this.filteredCommodityHistoricData = this.unfilteredCommodityHistoricData.slice(startIndex, stopIndex);
            },

            predictCommodityPrice: function(){
                const movingAverage = new MovingAverage();

                // Use previous data from the past four months
                let startIndex = this.unfilteredCommodityHistoricData.findIndex(data => {
                    return data.month == 'Jan 2017'
                });
                let stopIndex = this.unfilteredCommodityHistoricData.length + 1;
                const pastFourYearsRicePrices = this.unfilteredCommodityHistoricData.slice(startIndex, stopIndex);

                if(this.isMonthOrYearSelected == 'MONTHS'){
                    let month = this.predictionMonth.substring(0, 3);
                    
                    let x = pastFourYearsRicePrices.filter(data => 
                        data.month.includes(month)
                    );

                    let y = x.map(data => {
                        return parseInt(data.price);
                    });

                    let predictedPrice = movingAverage.predict(y, 1);
                    this.predictedPrice = Math.ceil(predictedPrice[predictedPrice.length - 1]);
                    this.message = `Prediction for the next month of ${this.predictionMonth} is:`;

                }

                if(this.isMonthOrYearSelected == 'YEARS'){
                    let lastYearOfHistoricPrices = this.unfilteredCommodityHistoricData[this.unfilteredCommodityHistoricData.length -1].month.split(' ')[1];
                    let lastYear = parseInt(lastYearOfHistoricPrices);
                    let predictionYear = parseInt(this.predictionYear);
                    
                    try{
                        if(lastYear > predictionYear){
                            alert('Prices for the selected year already exist. The year has passed!');
                            return;
                        }
                    }catch(error){
                        alert(error);
                        return;
                    }

                    let difference = predictionYear - lastYear;

                    let y = pastFourYearsRicePrices.map(data => {
                        return parseInt(data.price);
                    });

                    let predictedPrice = movingAverage.predict(y, difference);
                    this.predictedPrice = Math.ceil(predictedPrice[predictedPrice.length - 1]);
                    this.message = `Prediction for the average price in ${this.predictionYear} is:`;


                }
            },

            logout: function(){
                try{
                    localStorage.removeItem("loggedInAdmin");
                }catch(e){
                    // DO NOTHING
                }
                
                window.location.href = '/';
            }
        }
    }
</script>

<style scoped>

    .landing{
        display: flex;
        justify-content: center;
        align-items: center;
        color: #FFFFFF;
        flex-flow: column;
        line-height: 0.2em;

        width: 100%;
        height: 350px;
        background: linear-gradient(180deg, rgba(0, 0, 0, 0.75) 25.62%, rgba(0, 0, 0, 0.75) 100%), url('~@/assets/img/farm.png');
        background-repeat: no-repeat;
        background-position: center;
        background-size: cover
    }

    @media screen and (max-width: 450px){
        
    }

</style>