<script>
import { Bar, mixins } from 'vue-chartjs';
import store from 'vuex';
 
export default {
	extends: Bar,
	mixins: [ mixins.reactiveData ],
	//props: ['confirmed', 'recovered', 'deaths', 'country'],
	data () {
	  	return{
			datacollection: {},
			options: {}
	  	}
	},
	methods: {
		paintChart(){
			this.datacollection = {
				labels: ['Confirmed', 'Deaths', 'Recovered'],
				datasets: [{
					label: 'People',
					fill: true,
					backgroundColor: ['#3a243b', '#ff6347', '#c2e988'],
					pointBackgroundColor: 'white',
					borderWidth: 1,
					pointBorderColor: '#3a243b',
					data: this.CountryChartData,
				}]
			};
			this.options = {
                legend: { display: false },
                title: { display: true, text: `Statistics of ${this.countryData}` },
            };
	        this.renderChart(this.datacollection, this.options);
		}
	},
	mounted () {
		this.paintChart();
	},
	watch: {
  		'CountryChartData': function(to, from) {
			  this.paintChart();
        },
		immediate: true,
		deep: true
    },
    computed: {
        CountryChartData(){
            return (this.$store.state.CountryChartData);
        },
        countryData(){
            return (this.$store.state.countryData);
        }
    }
  }
</script>