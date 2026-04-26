<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Отметка</title>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 30px; background: #f4f4f4; }
        .btn { display: block; width: 100%; max-width: 300px; margin: 15px auto; padding: 25px; font-size: 18px; font-weight: bold; cursor: pointer; border: none; border-radius: 15px; color: white; text-transform: uppercase; }
        #btn-geo { background: #007bff; }
        #btn-sms { background: #28a745; display: none; }
        #status { margin-top: 15px; font-size: 16px; color: #333; font-weight: bold; }
    </style>
</head>
<body>

    <button id="btn-geo" class="btn" onclick="requestGeo()">Сообщить местонахождение собаки</button>
    <button id="btn-sms" class="btn" onclick="sendSms()">Отправить СМС владельцу</button>
    <div id="status"></div>

    <script>
        let currentCoords = "без координат";
        const myNumber = "79166803073";
        let timerInterval;

        function requestGeo() {
            const status = document.getElementById('status');
            const btnGeo = document.getElementById('btn-geo');
            let timeLeft = 10;

            btnGeo.style.display = "none";
            status.innerHTML = `Связь со спутниками... (${timeLeft} сек)`;

            // Запуск обратного отсчета
            timerInterval = setInterval(() => {
                timeLeft--;
                if (timeLeft > 0) {
                    status.innerHTML = `Связь со спутниками... (${timeLeft} сек)`;
                } else {
                    clearInterval(timerInterval);
                }
            }, 1000);

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        clearInterval(timerInterval);
                        const lat = position.coords.latitude.toFixed(8);
                        const lon = position.coords.longitude.toFixed(8);
                        currentCoords = `${lat} ${lon}`;
                        finishGeo("Координаты определены!");
                    },
                    () => {
                        clearInterval(timerInterval);
                        currentCoords = " ";
                        finishGeo(" ");
                    },
                    { enableHighAccuracy: true, timeout: 10000, maximumAge: 0 }
                );
            } else {
                clearInterval(timerInterval);
                currentCoords = " ";
                finishGeo("GPS не поддерживается");
            }
        }

        function finishGeo(msg) {
            document.getElementById('status').innerHTML = msg;
            document.getElementById('btn-sms').style.display = "block";
        }

        function sendSms() {
            // Возвращаем исходный формат текста СМС: статус и только цифры через пробел
            const message = `Пёс найден! ${currentCoords}`;
            window.location.href = `sms:+${myNumber}?body=${encodeURIComponent(message)}`;
        }
    </script>
</body>
</html>
