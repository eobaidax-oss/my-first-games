# my-first-games
الطريق الي النجاح
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>سماعات ProSound - التجربة الأفضل</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f8f9fa;
            color: #333;
        }
        header {
            background-color: #111;
            color: white;
            padding: 20px;
            text-align: center;
        }
        .container {
            max-width: 800px;
            margin: 40px auto;
            background: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            text-align: center;
        }
        .product-card {
            padding: 20px;
            border-radius: 8px;
            background-color: #e9ecef;
            margin: 20px 0;
            transition: 0.3s;
        }
        button {
            background-color: #28a745;
            color: white;
            border: none;
            padding: 12px 25px;
            font-size: 16px;
            border-radius: 6px;
            cursor: pointer;
            margin: 5px;
        }
        button:hover {
            background-color: #218838;
        }
        .price {
            font-size: 24px;
            color: #d9534f;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <header>
        <h1>ProSound Wireless</h1>
        <p>الصوت الذي تستحقه بنقاء عالي</p>
    </header>

    <div class="container">
        <h2>سماعة الألعاب والموسيقى الاحترافية</h2>
        
        <div id="product" class="product-card">
            <h3 id="color-name">اللون المختار: أسود ملكي</h3>
            <p>عزل ضوضاء كامل + بطارية تدوم 30 ساعة</p>
            <p class="price">$99.99</p>
        </div>

        <p>اختر لونك المفضل:</p>
        <button onclick="changeColor('#111', 'أسود ملكي')">أسود</button>
        <button onclick="changeColor('#0056b3', 'أزرق كلاسيك')">أزرق</button>
        <button onclick="changeColor('#d9534f', 'أحمر رياضي')">أحمر</button>
        
        <br><br>
        <button style="background-color: #007bff;" onclick="buyNow()">اطلب الآن</button>
        <p id="status"></p>
    </div>

    <script>
        function changeColor(colorCode, colorName) {
            let product = document.getElementById("product");
            product.style.backgroundColor = colorCode;
            product.style.color = "white";
            document.getElementById("color-name").innerText = "اللون المختار: " + colorName;
        }

        function buyNow() {
            document.getElementById("status").innerText = "✅ تم إضافة المنتج لسلة الشراء بنجاح!";
            document.getElementById("status").style.color = "green";
        }
    </script>

</body>
</html>
