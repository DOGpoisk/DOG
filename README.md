<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Отметка собаки</title>
    <style>
        body { 
            font-family: sans-serif; 
            text-align: center; 
            padding: 30px; 
            background: #f4f4f4; 
        }
        .btn { 
            display: block; 
            width: 100%; 
            max-width: 300px; 
            margin: 15px auto; 
            padding: 25px; 
            font-size: 18px; 
            font-weight: bold; 
            cursor: pointer; 
            border: none; 
            border-radius: 15px; 
            color: white; 
            text-transform: uppercase; 
        }
        #btn-geo { background: #007bff; }
        #btn-sms { background: #28a745; display: none; }
        #status { 
            margin-top: 15px; 
            font-size: 18px; 
            color: #333; 
            font-weight: bold;
        }
    </style>
</head>
<body>

    <button id="btn-geo" class="btn" onclick="requestGeo()">Сообщить местонахождение собаки!</button>
    <button id="btn-sms" class="btn" onclick="sendSms()">Отправить СМС владельцу</button>
    
    <div id="status"></div>

    <script>
        let currentCoords = "без координат";
        const myNumber = "79166803073"; // Твой номер телефона
        let timerInterval;

        function requestGeo() {
            const status = document.getElementById('status');
            const btnGeo = document.getElementById('btn-geo');
            let timeLeft = 10; // Время ожидания в секундах

            btnGeo.style.display = "none";
            status.innerHTML = `Поиск спутников... (${timeLeft} сек)`;

            // Запускаем визуальный таймер
            timerInterval = setInterval(() => {
                timeLeft--;
                if (timeLeft > 0) {
                    status.innerHTML = `Поиск спутников... (${timeLeft} сек)`;
                } else {
                    clearInterval(timerInterval);
                }
            }, 1000);

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        clearInterval(timerInterval); // Останавливаем таймер, если нашли координаты быстрее
                        const lat = position.coords.latitude.toFixed(8);
                        const lon = position.coords.longitude.toFixed(8);
                        currentCoords = `${lat},${lon}`;
                        finishGeo("Координаты определены!");
                    },
                    () => {
                        clearInterval(timerInterval);
                        finishGeo("Не удалось получить точный GPS-сигнал");
                    },
                    { 
                        enableHighAccuracy: true, 
                        timeout: 10000, 
                        maximumAge: 0 
                    }
                );
            } else {
                clearInterval(timerInterval);
                finishGeo("GPS не поддерживается вашим телефоном");
            }
        }

        function finishGeo(msg) {
            document.getElementById('status').innerHTML = msg;
            document.getElementById('btn-sms').style.display = "block";
        }

        function sendSms() {
            const mapLink = `https://google.com{currentCoords}`;
            // Текст сообщения: координаты + готовая ссылка для открытия в картах
            const message = `Пёс найден! Координаты: ${currentCoords}. Карта: ${mapLink}`;
            
            window.location.href = `sms:+${myNumber}?body=${encodeURIComponent(message)}`;
        }
    </script>
</body>
</html>
