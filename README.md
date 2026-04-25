<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Эстафета: Отметка</title>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 30px; background: #f4f4f4; }
        .btn { display: block; width: 100%; max-width: 300px; margin: 15px auto; padding: 25px; font-size: 18px; font-weight: bold; cursor: pointer; border: none; border-radius: 15px; color: white; text-transform: uppercase; }
        #btn-geo { background: #007bff; } 
        #btn-sms { background: #28a745; display: none; } 
        #status { margin-top: 15px; font-size: 16px; color: #333; }
    </style>
</head>
<body>

    
    <button id="btn-geo" class="btn" onclick="requestGeo()">1. Найти меня!!!</button>
    <button id="btn-sms" class="btn" onclick="sendSms()">2. Отправить СМС</button>

    <div id="status">Нажмите кнопку 1</div>

    <script>
        let currentCoords = "NW"; 
        const myNumber = "79001112233"; // ЗАМЕНИТЕ НА ВАШ НОМЕР

        function requestGeo() {
            const status = document.getElementById('status');
            const btnGeo = document.getElementById('btn-geo');

            status.innerHTML = "Связь со спутниками...";
            btnGeo.style.display = "none";

            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        const lat = position.coords.latitude.toFixed(8);
                        const lon = position.coords.longitude.toFixed(8);
                        // ФОРМАТ: 55.11223344 37.11223344
                        currentCoords = `${lat} ${lon}`;
                        finishGeo("Координаты определены!");
                    },
                    () => {
                        currentCoords = "NW";
                        finishGeo("Гео не найдено (отправляем NW)");
                    },
                    { enableHighAccuracy: true, timeout: 10000, maximumAge: 0 }
                );
            } else {
                currentCoords = "NW";
                finishGeo("Не поддерживается");
            }
        }

        function finishGeo(msg) {
            document.getElementById('status').innerHTML = msg;
            document.getElementById('btn-sms').style.display = "block";
        }

        function sendSms() {
            // Формируем текст СМС без лишних слов, только статус и цифры
            const message = `Этап пройден! ${currentCoords}`;
            window.location.href = `sms:+${myNumber}?body=${encodeURIComponent(message)}`;
        }
    </script>
</body>
</html>
