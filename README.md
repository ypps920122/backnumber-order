<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BackNumber 預購訂購單</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&display=swap');
        
        body {
            background-color: #f8fafc;
            font-family: 'Noto Sans TC', sans-serif;
            color: #1e293b;
            margin: 0;
            padding: 0;
        }

        .main-card {
            max-width: 600px;
            margin: 40px auto;
            background: white;
            border-radius: 24px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
            overflow: hidden;
        }

        .product-img-container {
            position: relative;
            width: 100%;
            background: #f1f5f9;
            cursor: zoom-in;
            overflow: hidden;
        }

        .product-img-container img {
            width: 100%;
            display: block;
            transition: transform 0.3s ease;
        }

        .input-field {
            width: 100%;
            padding: 12px 16px;
            border: 1px solid #e2e8f0;
            border-radius: 12px;
            background: #ffffff;
            transition: border-color 0.2s;
        }

        .input-field:focus {
            outline: none;
            border-color: #6366f1;
        }

        #imgModal {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.8);
            z-index: 9999;
            cursor: zoom-out;
            padding: 20px;
            align-items: center;
            justify-content: center;
        }

        #imgModal img {
            max-width: 100%;
            max-height: 90vh;
            border-radius: 8px;
        }
    </style>
</head>
<body class="p-4">

    <div class="main-card">
        <!-- 頂部標題 -->
        <div class="p-8 text-center">
            <h1 class="text-2xl font-black text-slate-800 mb-1">BackNumber 第三波預購</h1>
            <p class="text-xs text-slate-400 font-bold tracking-widest uppercase">Lottie Shop Online</p>
        </div>

        <!-- 須知區塊 -->
        <div class="px-8 pb-6">
            <div class="bg-indigo-50 rounded-2xl p-5 mb-8 border border-indigo-100">
                <h2 class="font-bold text-indigo-900 mb-2">📋 訂購須知</h2>
                <ul class="text-sm text-indigo-800 space-y-1 opacity-80 leading-relaxed">
                    <li>• 2/1 20:00 收單</li>
                    <li>• 費用含國際運費與關稅</li>
                    <li>• 先匯款後填單</li>
                </ul>
                <div class="mt-4 pt-3 border-t border-indigo-200">
                    <p class="text-[10px] font-bold text-indigo-400 mb-1">匯款資訊</p>
                    <p class="font-mono text-indigo-900 font-bold">國泰世華 (013) 699510910862</p>
                </div>
            </div>

            <!-- 商品展示 -->
            <div class="mb-8">
                <h2 class="text-xs font-bold text-slate-400 mb-3 uppercase tracking-widest">Product</h2>
                <div class="product-img-container rounded-2xl border border-slate-100" onclick="showZoom()">
                    <img id="mainImg" src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" alt="商品目錄" onerror="this.src='https://via.placeholder.com/600x400?text=Image+Loading...'">
                </div>
            </div>

            <!-- 選擇商品 -->
            <div class="mb-8">
                <div class="flex items-center justify-between p-4 border border-slate-100 rounded-2xl bg-slate-50/50">
                    <div class="flex items-center">
                        <div class="w-10 h-10 bg-white rounded-lg border border-slate-100 flex items-center justify-center mr-3">📸</div>
                        <p class="font-bold text-slate-800">女生隨機卡</p>
                    </div>
                    <div class="flex items-center gap-4">
                        <span class="font-bold text-slate-800">$230</span>
                        <div class="flex items-center bg-white border border-slate-200 rounded-lg overflow-hidden">
                            <button onclick="changeQty(-1)" class="w-8 h-8 flex items-center justify-center hover:bg-slate-50">-</button>
                            <span id="qty" class="w-8 text-center text-sm font-bold">0</span>
                            <button onclick="changeQty(1)" class="w-8 h-8 flex items-center justify-center hover:bg-slate-50">+</button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 填單表格 -->
            <form id="orderForm" class="space-y-4">
                <div class="grid grid-cols-2 gap-4">
                    <input type="text" placeholder="對帳名稱" class="input-field">
                    <input type="text" placeholder="IG 帳號" class="input-field">
                </div>
                <input type="tel" placeholder="聯絡電話" class="input-field">
                <input type="text" placeholder="賣貨便店名" class="input-field">

                <!-- 總計與提交 -->
                <div class="mt-10 p-6 bg-indigo-600 rounded-2xl text-center">
                    <p class="text-indigo-200 text-[10px] font-bold mb-1 uppercase tracking-widest">Total</p>
                    <div class="text-white text-3xl font-black mb-6">$<span id="total">0</span></div>
                    <button type="button" onclick="handleSubmit()" class="w-full py-3 bg-white text-indigo-600 rounded-xl font-bold hover:bg-indigo-50 transition-colors">
                        提交訂單
                    </button>
                </div>
            </form>

            <div class="mt-8 text-center text-[10px] text-slate-400 font-bold leading-relaxed pb-8">
                IG: lottie_lovetobuy
            </div>
        </div>
    </div>

    <!-- 放大視窗 -->
    <div id="imgModal" onclick="this.style.display='none'">
        <img id="zoomImg" src="">
    </div>

    <script>
        let qty = 0;
        function changeQty(v) {
            qty = Math.max(0, qty + v);
            document.getElementById('qty').innerText = qty;
            document.getElementById('total').innerText = qty * 230;
        }
        function showZoom() {
            document.getElementById('zoomImg').src = document.getElementById('mainImg').src;
            document.getElementById('imgModal').style.display = 'flex';
        }
        function handleSubmit() {
            if (qty === 0) {
                alert("請選擇數量");
                return;
            }
            alert("提交成功（預覽模式）");
        }
    </script>
</body>
</html>

