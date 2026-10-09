# Besmyslenyy
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mini App Shop</title>
    <!-- Telegram WebApp SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--tg-theme-bg-color, #ffffff);
            color: var(--tg-theme-text-color, #000000);
            margin: 0;
            padding: 16px;
        }
        .product-card {
            border: 1px solid var(--tg-theme-hint-color, #ccc);
            border-radius: 12px;
            padding: 16px;
            margin-bottom: 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .btn {
            background-color: var(--tg-theme-button-color, #2481cc);
            color: var(--tg-theme-button-text-color, #ffffff);
            border: none;
            padding: 6px 12px;
            border-radius: 6px;
            font-weight: bold;
            cursor: pointer;
        }
        .count { margin: 0 8px; font-weight: bold; }
    </style>
</head>
<body>
    <h2>Каталог товаров</h2>

    <div class="product-card">
        <div>
            <div><strong>Товар 1</strong></div>
            <div>500 ₽</div>
        </div>
        <div>
            <button class="btn" onclick="updateCount(0, -1)">-</button>
            <span class="count" id="count-0">0</span>
            <button class="btn" onclick="updateCount(0, 1)">+</button>
        </div>
    </div>

    <div class="product-card">
        <div>
            <div><strong>Товар 2</strong></div>
            <div>1200 ₽</div>
        </div>
        <div>
            <button class="btn" onclick="updateCount(1, -1)">-</button>
            <span class="count" id="count-1">0</span>
            <button class="btn" onclick="updateCount(1, 1)">+</button>
        </div>
    </div>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();

        const products = [
            { id: 1, name: "Товар 1", price: 500, count: 0 },
            { id: 2, name: "Товар 2", price: 1200, count: 0 }
        ];

        function updateCount(index, change) {
            // Haptic Feedback при клике
            if (tg.HapticFeedback) {
                tg.HapticFeedback.impactOccurred('light');
            }

            products[index].count = Math.max(0, products[index].count + change);
            document.getElementById(`count-${index}`).innerText = products[index].count;
            
            updateMainButton();
        }

        function updateMainButton() {
            const selectedItems = products.filter(p => p.count > 0);
            const totalPrice = selectedItems.reduce((sum, p) => sum + (p.price * p.count), 0);

            if (totalPrice > 0) {
                tg.MainButton.setText(`Оформить заказ на ${totalPrice} ₽`);
                tg.MainButton.show();
            } else {
                tg.MainButton.hide();
            }
        }

        // Клик по нативной кнопке MainButton отправляет JSON данные в бот
        tg.MainButton.onClick(() => {
            const selectedItems = products.filter(p => p.count > 0);
            const totalPrice = selectedItems.reduce((sum, p) => sum + (p.price * p.count), 0);
            
            const data = {
                items: selectedItems,
                totalPrice: totalPrice
            };

            tg.sendData(JSON.stringify(data)); // Передача данных боту
        });
    </script>
</body>
</html>
