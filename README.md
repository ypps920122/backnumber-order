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
            background-color: #ffffff; /* 回歸極簡全白背景 */
            font-family: 'Noto Sans TC', sans-serif;
            color: #334155;
            margin: 0;
            padding: 0;
        }

        .container-wrapper {
            max-width: 650px;
            margin: 0 auto;
            padding: 40px 20px;
        }

        .section-box {
            background: #ffffff;
            border: 1px solid #f1f5f9;
            border-radius: 20px;
            padding: 30px;
            margin-bottom: 24px;
        }

        .info-highlight {
            background: #f8fafc; /* 淺灰色塊背景 */
            border-radius: 16px;
            padding: 24px;
        }

        .product-image {
            width: 100%;
            border-radius: 16px;
            cursor: zoom-in;
            border: 1px solid #f1f5f9;
        }

        .input-item {
            width: 100%;
            padding: 14px 16px;
            border: 1px solid #e2e8f0;
            border-radius: 12px;
            font-size: 14px;
            margin-top: 6px;
            transition: all 0.2s;
        }

        .input-item:focus {
            outline: none;
            border-color: #94a3b8;
            background-color: #fcfcfc;
        }

        label {
            font-size: 13px;
            font-weight: 700;
            color: #64748b;
        }

        #imgModal {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(255, 255, 255, 0.98);
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
            box-shadow: 0 10px 40px rgba(0,0,0,0.05);
        }

        .submit-btn {
            width: 100%;
            padding: 18px;
            background: #1e293b;
            color: #ffffff;
            border-radius: 16px;
            font-weight: 900;
            letter-spacing: 2px;
            transition: opacity 0.2s;
        }

        .submit-btn:active {
            opacity: 0.8;
        }
    </style>
</head>
<body>

    <div class="container-wrapper">
        <!-- 頁首 -->
        <div class="text-center mb-12">
            <h1 class="text-3xl font-black text-slate-800 tracking-tight">BackNumber 第三波預購</h1>
            <p class="text-sm text-slate-400 font-bold mt-2 uppercase tracking-widest">Lottie Shop</p>
        </div>

        <!-- 訂購須知與條款區 -->
        <div class="section-box">
            <h2 class="font-black text-lg mb-4 text-slate-700">📝 訂購須知與條款</h2>
            <div class="info-highlight text-[14px] text-slate-600 leading-relaxed space-y-3">
                <p>👉 BackNumber 周邊販售第三波預購，2/1 20:00收單，六月中出貨。</p>
                <p>👉 下單前務必確認顏色尺寸，確認訂單後不接受更換或是取消。</p>
                <p>✅ 不因任何瑕疵問題退貨，若有缺貨情況發生會透過 <strong>LINE社群</strong> 聯絡，全額退款。</p>
                <p>✅ 不二收，匯款後就等出貨。（已包含當地運費、國際運費、關稅、寄送運費）</p>
                <p>✅ 先匯款，後填單；填單完成即視為購買，恕不得取消。</p>
            </div>
            <div class="mt-6 text-center">
                <p class="text-[11px] font-bold text-slate-400 mb-1 tracking-widest uppercase">Payment Info / 匯款資訊</p>
                <p class="font-mono text-slate-800 font-black text-xl">國泰世華 (013) 699510910862</p>
            </div>
        </div>

        <!-- 商品目錄區 -->
        <div class="section-box">
            <h2 class="font-black text-lg mb-4 text-slate-700">Product / 商品目錄</h2>
            <img id="mainImg" src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" class="product-image" alt="商品目錄" onclick="showZoom()" onerror="handleImgError(this)">
            <p class="text-center text-[10px] text-slate-300 mt-3 italic">點擊圖片可放大查看</p>
        </div>

        <!-- 選擇數量區 -->
        <div class="section-box">
            <h2 class="font-black text-lg mb-4 text-slate-700">Selection / 選擇數量</h2>
            <div class="flex items-center justify-between py-4 border-b border-slate-50">
                <div>
                    <p class="font-bold text-slate-800">女生隨機卡</p>
                    <p class="text-xs text-slate-400">$230 / 張</p>
                </div>
                <div class="flex items-center bg-slate-50 rounded-xl px-2">
                    <button onclick="changeQty(-1)" class="w-10 h-10 text-slate-400 font-bold">-</button>
                    <span id="qty" class="w-10 text-center font-black text-slate-700">0</span>
                    <button onclick="changeQty(1)" class="w-10 h-10 text-slate-400 font-bold">+</button>
                </div>
            </div>
        </div>

        <!-- 填單資訊 Checkout -->
        <div class="section-box">
            <h2 class="font-black text-lg mb-6 text-slate-700">Checkout / 填單資訊</h2>
            <form id="orderForm" class="space-y-5">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
                    <div>
                        <label>對帳名稱</label>
                        <input type="text" placeholder="請輸入匯款名稱" class="input-item" required>
                    </div>
                    <div>
                        <label>LINE 社群名稱</label>
                        <input type="text" placeholder="社群標記聯繫用" class="input-item" required>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
                    <div>
                        <label>Instagram ID</label>
                        <input type="text" placeholder="@帳號" class="input-item" required>
                    </div>
                    <div>
                        <label>聯絡電話</label>
                        <input type="tel" placeholder="09xxxxxxxx" class="input-item" required>
                    </div>
                </div>

                <div>
                    <label>Email 信箱</label>
                    <input type="email" placeholder="接收訂單確認通知" class="input-item" required>
                </div>

                <div>
                    <label>7-11 賣貨便寄送店家名稱</label>
                    <input type="text" placeholder="請輸入完整店名（例：忠孝店）" class="input-item" required>
                    <p class="mt-2 text-[11px] text-slate-400 font-medium">
                        👉 <a href="https://emap.pcsc.com.tw/emap.aspx" target="_blank" class="text-slate-500 underline decoration-slate-300">備註：點此查詢 7-11 店家名稱</a>
                    </p>
                </div>

                <!-- 結帳按鈕區 -->
                <div class="pt-8">
                    <div class="flex justify-between items-end mb-6 px-1">
                        <span class="text-xs font-black text-slate-400 tracking-widest uppercase">Grand Total</span>
                        <span class="text-4xl font-black text-slate-800 tracking-tighter">$<span id="total">0</span></span>
                    </div>
                    <button type="button" onclick="handleSubmit()" class="submit-btn">
                        確認並提交訂單
                    </button>
                </div>
            </form>
        </div>

        <div class="text-center text-[11px] text-slate-300 font-medium mt-10">
            IG: lottie_lovetobuy / BackNumber Fansite Pre-order
        </div>
    </div>

    <!-- 圖片放大 -->
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
            const modal = document.getElementById('imgModal');
            const zoomImg = document.getElementById('zoomImg');
            zoomImg.src = document.getElementById('mainImg').src;
            modal.style.display = 'flex';
        }

        function handleImgError(img) {
            img.parentElement.innerHTML = `
                <div class="p-10 text-center bg-slate-50 rounded-2xl">
                    <p class="text-slate-400 text-sm mb-2">圖片載入中</p>
                    <a href="https://drive.google.com/file/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s/view" target="_blank" class="text-slate-600 underline text-sm font-bold">手動點此開啟目錄</a>
                </div>
            `;
        }

        function handleSubmit() {
            if (qty === 0) {
                alert("🛒 請選擇數量喔！");
                return;
            }
            alert("✅ 提交成功（此為樣板預覽，實際訂單請手動連繫確認資訊）。");
        }
    </script>
</body>
</html>

