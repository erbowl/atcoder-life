<script>
import { Bar } from "vue-chartjs";
const axios = require("axios");
export default {
  extends: Bar,
  name: "chart",
  data() {
    return {
      data: {
        labels: [...Array(24).keys()],
        datasets: [
          {
            label: "Submission By Hour",
            data: Array(24).fill(0),
            // backgroundColor: [
            //   "rgba(255, 99, 132, 0.2)",
            //   "rgba(54, 162, 235, 0.2)",
            //   "rgba(255, 206, 86, 0.2)",
            //   "rgba(75, 192, 192, 0.2)",
            //   "rgba(153, 102, 255, 0.2)",
            //   "rgba(255, 159, 64, 0.2)"
            // ],
            // borderColor: [
            //   "rgba(255, 99, 132, 1)",
            //   "rgba(54, 162, 235, 1)",
            //   "rgba(255, 206, 86, 1)",
            //   "rgba(75, 192, 192, 1)",
            //   "rgba(153, 102, 255, 1)",
            //   "rgba(255, 159, 64, 1)"
            // ],
            borderWidth: 1,
            yAxisID: 'first'
          },
          {
            label: "Line Dataset",
            data: Array(24).fill(0),
            borderColor: "#CFD8DC",
            fill: false,
            type: "line",
            yAxisID: 'second'
            // lineTension: 0.3
          }
        ]
      },
      options: {
        scales: {
          xAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: "Hour"
              }
            }
          ],
          yAxes: [
            {
              id: 'first',
              ticks: {
                beginAtZero: true,
                // stepSize: 10
              }
            },
             {
              id: 'second',
              ticks: {
                beginAtZero: true,
                
              },
              position: "right"
            }
          ]
        }
      }
    };
  },
  mounted() {
    console.log(this);
    this.getSubmission();
  },
  methods: {
    getSubmission: function() {
      var _this = this;
      axios
        .get("https://kenkoooo.com/atcoder/atcoder-api/results?user=erbowl")
        .then(function(response) {
          var count_by_hour = Array(24).fill(0);
          var average_score_by_hour = [...Array(24)].map(() => []);
          console.log(average_score_by_hour);
          response.data.forEach(function(e){
            var hour = new Date(e.epoch_second * 1000).getHours();
            count_by_hour[hour]++;
            if(e.result == "AC" && e.point<3000){
              average_score_by_hour[hour].push(e.point);
            }
          });

          _this.data.datasets[0].data = count_by_hour;
          _this.data.datasets[1].data = average_score_by_hour.map(function(e){
            if(e.length == 0){
              return null;
            }else{
              console.log( e.reduce((p, c) => c += p),e.length);
              return e.reduce((p, c) => c += p)/e.length;
            }
          });

          console.log(_this.data.datasets[1].data);
          _this.renderChart(_this.data, _this.options);
        });
    }
  }
};
</script>
