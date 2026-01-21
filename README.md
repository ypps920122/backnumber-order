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

        <!-- 須知區塊 (依照你要求的內容更新) -->
        <div class="px-8 pb-6">
            <div class="bg-indigo-50 rounded-2xl p-6 mb-8 border border-indigo-100">
                <h2 class="font-bold text-indigo-900 mb-4 flex items-center">
                    <span class="mr-2">📝</span> 訂購須知與條款
                </h2>
                <div class="text-[13px] text-indigo-800 space-y-3 leading-relaxed">
                    <p>👉 BackNumber 周邊販售第三波預購，2/1 20:00收單，六月中出貨。</p>
                    <p>👉 下單前務必確認顏色尺寸，確認訂單後不接受更換或是取消。</p>
                    <p>✅ 不因任何瑕疵問題退貨，若有缺貨情況發生會透過 <strong>LINE社群</strong> 聯絡，全額退款。</p>
                    <p>✅ 不二收，匯款後就等出貨。（已包含當地運費、國際運費、關稅、寄送運費）</p>
                    <p>✅ 先匯款，後填單；填單完成即視為購買，恕不得取消。</p>
                </div>
                <div class="mt-5 pt-4 border-t border-indigo-200">
                    <p class="text-[10px] font-bold text-indigo-400 mb-1 tracking-widest">PAYMENT INFO / 匯款資訊</p>
                    <p class="font-mono text-indigo-900 font-bold text-lg">國泰世華 (013) 699510910862</p>
                </div>
            </div>

            <!-- 商品展示 -->
            <div class="mb-8">
                <h2 class="text-xs font-bold text-slate-400 mb-3 uppercase tracking-widest">Product Catalog / 商品目錄</h2>
                <div class="product-img-container rounded-2xl border border-slate-100 shadow-sm" onclick="showZoom()">
                    <img id="mainImg" src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" alt="商品目錄" onerror="handleError(this)">
                </div>
            </div>

            <!-- 選擇商品 -->
            <div class="mb-8">
                <div class="flex items-center justify-between p-4 border border-slate-100 rounded-2xl bg-slate-50/50">
                    <div class="flex items-center">
                        <div class="w-10 h-10 bg-white rounded-lg border border-slate-100 flex items-center justify-center mr-3 text-lg">📸</div>
                        <p class="font-bold text-slate-800">女生隨機卡</p>
                    </div>
                    <div class="flex items-center gap-4">
                        <span class="font-bold text-slate-800">$230</span>
                        <div class="flex items-center bg-white border border-slate-200 rounded-lg overflow-hidden">
                            <button onclick="changeQty(-1)" class="w-8 h-8 flex items-center justify-center hover:bg-slate-50 font-bold transition-colors">-</button>
                            <span id="qty" class="w-8 text-center text-sm font-black">0</span>
                            <button onclick="changeQty(1)" class="w-8 h-8 flex items-center justify-center hover:bg-slate-50 font-bold transition-colors">+</button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 填單表格 -->
            <form id="orderForm" class="space-y-4">
                <h2 class="text-xs font-bold text-slate-400 mb-1 uppercase tracking-widest">Order Info / 填單資訊</h2>
                <div class="grid grid-cols-2 gap-4">
                    <input type="text" placeholder="對帳名稱" class="input-field" required>
                    <input type="text" placeholder="IG 帳號 (@)" class="input-field" required>
                </div>
                <input type="tel" placeholder="聯絡電話" class="input-field" required>
                <input type="text" placeholder="賣貨便店名 (完整名稱)" class="input-field" required>

                <!-- 總計與提交 -->
                <div class="mt-10 p-8 bg-indigo-600 rounded-[32px] text-center shadow-lg shadow-indigo-100">
                    <p class="text-indigo-200 text-[10px] font-bold mb-1 uppercase tracking-widest">Grand Total</p>
                    <div class="text-white text-4xl font-black mb-8 tracking-tighter">$<span id="total">0</span></div>
                    <button type="button" onclick="handleSubmit()" class="w-full py-4 bg-white text-indigo-600 rounded-2xl font-black tracking-widest hover:bg-indigo-50 transition-all active:scale-95 shadow-md">
                        提交訂單內容
                    </button>
                </div>
            </form>

            <div class="mt-8 text-center text-[10px] text-slate-400 font-bold leading-relaxed pb-8">
                如有任何問題，請私訊 IG <span class="text-slate-600 underline">lottie_lovetobuy</span><br>或至 LINE 社群尋求協助
            </div>
        </div>
    </div>

    <!-- 放大視窗 -->
    <div id="imgModal" onclick="this.style.display='none'">
        <img id="zoomImg" src="">
    </div>

    <script>
        let qty = 0;
        const PRICE = 230;

        function changeQty(v) {
            qty = Math.max(0, qty + v);
            document.getElementById('qty').innerText = qty;
            document.getElementById('total').innerText = (qty * PRICE).toLocaleString();
        }

        function showZoom() {
            const src = document.getElementById('mainImg').src;
            document.getElementById('zoomImg').src = src;
            document.getElementById('imgModal').style.display = 'flex';
        }

        function handleError(img) {
            img.parentElement.innerHTML = `
                <div class="p-8 text-center bg-slate-50">
                    <p class="text-slate-400 text-sm mb-2">圖片載入中或需要權限</p>
                    <a href="https://drive.google.com/file/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s/view" target="_blank" class="text-indigo-500 underline text-sm font-bold">直接開啟 Google Drive 圖片</a>
                </div>
            `;
        }

        function handleSubmit() {
            if (qty === 0) {
                alert("請至少選擇一個商品數量！");
                return;
            }
            alert("感謝填寫！此為展示版型，請確認資訊正確後再進行後續匯款與聯繫。");
        }
    </script>
</body>
</html>

