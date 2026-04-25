<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Эстафета: Подтверждение</title>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 30px; background: #f4f4f4; }
        .btn { display: block; width: 100%; max-width: 300px; margin: 15px auto; padding: 25px; font-size: 18px; font-weight: bold; cursor: pointer; border: none; border-radius: 15px; color: white; text-transform: uppercase; }
        #btn-geo { background: #007bff; } /* Синяя - Шаг 1 */
        #btn-sms { background: #28a745; display: none; } /* Зеленая - Шаг 2 (появится позже) */
        #status { margin-top: 15px; font-size: 16px; color: #333; font-weight: bold; }
        .loader { display: none; margin: 10px auto; border: 4px solid #f3f3f3; border-top: 4px solid #007bff; border-radius: 50%; width: 30px; height: 30px; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>

    <h2>Отметка на этапе</h2>
    
    <button id="btn-geo" class="btn" onclick="requestGeo()">1. Найти меня</button>
    <div id="loader" class="loader"></div>
    
    <button id="btn-sms" class="btn" onclick="sendSms()">2. Отправить СМС</button>

    <div id="status">Нажмите кнопку 1</div>

    <script>
        let currentCoords = "NW"; 
        const myNumber = "79001112233"; // ВАШ НОМЕР

        function requestGeo() {
            const status = document.getElementById('status');
            const loader = document.getElementById('loader');
            const btnGeo = document.getElementById('btn-geo');

            status.innerHTML = "Связь со спутниками... (до 10 сек)";
            loader.style.display = "block";
            btnGeo.style.display = "none";

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        // УСПЕХ: Координаты получены
                        const lat = position.coords.latitude.toFixed(6);
                        const lon = position.coords.longitude.toFixed(6);
                        currentCoords = `https://google.com{lat},${lon}`;
                        finishGeo("Координаты определены!");
                    },
                    (error) => {
                        // ОШИБКА: Доступ запрещен или GPS выключен
                        currentCoords = "NW";
                        finishGeo("Гео не найдено (будет отправлено NW)");
                    },
                    { 
                        enableHighAccuracy: true, // Включаем максимальную точность
                        timeout: 10000,           // Ждем спутники 10 секунд
                        maximumAge: 0             // Не берем старые данные из кэша
                    }
                );
            } else {
                currentCoords = "NW";
                finishGeo("Браузер не поддерживает GPS");
            }
        }

        function finishGeo(msg) {
            document.getElementById('status').innerHTML = msg;
            document.getElementById('loader').style.display = "none";
            document.getElementById('btn-sms').style.display = "block";
        }

        function sendSms() {
            const message = `Этап пройден! Координаты: ${currentCoords}`;
            window.location.href = `sms:+${myNumber}?body=${encodeURIComponent(message)}`;
        }
    </script>
</body>
</html>
