<!DOCTYPE html>
<html>
<head>
  <title>Tinkercad Live Data</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>

<body>
  <h1>Tinkercad Data Monitor</h1>

  <button id="connect">Connect to Tinkercad</button>

  <h2>Live Value: <span id="value">--</span></h2>

  <canvas id="chart" width="400" height="200"></canvas>

<script>
let port;
let reader;
let chartData = [];
let labels = [];

const ctx = document.getElementById('chart').getContext('2d');
const myChart = new Chart(ctx, {
    type: 'line',
    data: {
        labels: labels,
        datasets: [{
            label: 'Tinkercad Sensor Value',
            data: chartData,
            borderWidth: 2
        }]
    }
});

document.getElementById('connect').addEventListener('click', async () => {
    port = await navigator.serial.requestPort();
    await port.open({ baudRate: 9600 });

    reader = port.readable.getReader();

    while (true) {
        const { value, done } = await reader.read();
        if (done) break;

        let text = new TextDecoder().decode(value).trim();

        if (!isNaN(text)) {
            let num = parseFloat(text);
            document.getElementById("value").innerText = num;

            labels.push("");
            chartData.push(num);
            myChart.update();
        }
    }
});
</script>

</body>
</html>
