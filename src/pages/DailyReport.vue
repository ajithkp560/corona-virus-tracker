<script>
import { Line } from 'vue-chartjs'
 
export default {
	extends: Line,
	props: ['confirmedData', 'deathsData', 'labels'],
	data () {
	  	return{
			datacollection: {
				labels: this.labels,
				datasets: [
				{
					label: 'Infected',
					fill: true,
					backgroundColor: 'rgba(37, 0, 171, 0.5)',
					pointBackgroundColor: 'white',
					borderWidth: 1,
					pointBorderColor: '#3a243b',
					data: this.confirmedData,
				},{
					label: 'Deaths',
					fill: true,
					backgroundColor: '#ff6347',
					pointBackgroundColor: 'white',
					borderWidth: 1,
					pointBorderColor: '#ff6347',
					data: this.deathsData,
				}
				]
			},
			options: {
				scales: {
					yAxes: [{
						ticks: {
							beginAtZero: true
						},
						gridLines: {
							display: true
						}
					}],
					xAxes: [{
						ticks: {
							beginAtZero: true
						},
						gridLines: {
							display: false
						}
					}]
				},
				legend: {
					display: true
				},
				tooltips: {
					enabled: true,
					mode: 'single',
				},
				responsive: true,
				maintainAspectRatio: true,
				height: 200,
				title: {
					display: true,
					text: 'Last 30 days statistics (Global)'
				}
            }
	  	}
	  },
	  mounted () {
		this.renderChart(this.datacollection, this.options);
	},
	watch: {
  		datacollection(to, from) {
    		this.renderChart(this.datacollection, this.options)
		},
		immediate: true,
	}
  }
</script>