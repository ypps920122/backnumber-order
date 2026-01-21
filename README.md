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
            background-color: #ffffff;
            font-family: 'Noto Sans TC', sans-serif;
            color: #1e293b;
            margin: 0;
            padding: 0;
            -webkit-font-smoothing: antialiased;
        }

        .full-width-container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px 14px; /* 保持極小邊距，最大化文字空間 */
        }

        .content-block {
            margin-bottom: 32px;
        }

        /* 須知區塊：清爽淺灰底 */
        .terms-box {
            background: #f8fafc;
            border-radius: 12px;
            padding: 16px;
            font-size: 13.5px;
            line-height: 1.7;
            color: #475569;
        }

        .product-img {
            width: 100%;
            border-radius: 8px;
            cursor: zoom-in;
            border: 1px solid #f1f5f9;
        }

        .input-label {
            display: block;
            font-size: 12px;
            font-weight: 700;
            color: #94a3b8;
            margin-bottom: 4px;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .form-input {
            width: 100%;
            padding: 12px;
            border: 1px solid #e2e8f0;
            border-radius: 8px;
            font-size: 15px;
            margin-bottom: 16px;
            transition: border-color 0.2s;
        }

        .form-input:focus {
            outline: none;
            border-color: #1e293b;
        }

        .qty-control {
            display: flex;
            align-items: center;
            background: #f1f5f9;
            border-radius: 10px;
            padding: 4px;
        }

        .qty-btn {
            width: 36px;
            height: 36px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: #64748b;
        }

        /* LINE 功能按鈕 */
        .line-button {
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 12px;
            border-radius: 10px;
            font-size: 14px;
            font-weight: 700;
            margin-bottom: 10px;
            transition: opacity 0.2s;
            text-decoration: none;
        }

        .line-official {
            background-color: #ffffff;
            color: #06c755;
            border: 1.5px solid #06c755;
        }

        .line-community {
            background-color: #06c755;
            color: #ffffff;
        }

        .submit-button {
            width: 100%;
            padding: 16px;
            background: #1e293b;
            color: white;
            border-radius: 12px;
            font-weight: 900;
            font-size: 16px;
            letter-spacing: 1px;
            margin-top: 10px;
        }

        #zoomOverlay {
            display: none;
            position: fixed;
            inset: 0;
            background: white;
            z-index: 1000;
            padding: 10px;
            align-items: center;
            justify-content: center;
            cursor: zoom-out;
        }

        #zoomOverlay img {
            max-width: 100%;
            max-height: 100%;
            object-fit: contain;
        }
    </style>
</head>
<body>

    <div class="full-width-container">
        <!-- Header -->
        <div class="mb-8 border-b border-slate-100 pb-4">
            <h1 class="text-2xl font-black text-slate-800 tracking-tighter">BackNumber 3rd Pre-order</h1>
            <p class="text-[10px] text-slate-400 font-bold uppercase tracking-widest mt-1">Lottie Fansite Management</p>
        </div>

        <!-- 訂購須知與條款 -->
        <div class="content-block">
            <div class="terms-box">
                <p>👉 BackNumber 周邊販售第三波預購，2/1 20:00收單，六月中出貨。</p>
                <p>👉 下單前務必確認顏色尺寸，確認訂單後不接受更換或是取消。</p>
                <p>✅ 不因任何瑕疵問題退貨，若有缺貨情況發生會透過 <strong>LINE社群</strong> 聯絡，全額退款。</p>
                <p>✅ 不二收，匯款後就等出貨。（已包含當地運費、國際運費、關稅、寄送運費）</p>
                <p>✅ 先匯款，後填單；填單完成即視為購買，恕不得取消。</p>
                <div class="mt-4 pt-4 border-t border-slate-200">
                    <p class="text-[10px] font-bold text-slate-400 uppercase mb-1">Payment Info / 匯款帳號</p>
                    <p class="font-mono text-slate-900 font-black text-lg">國泰世華 (013) 699510910862</p>
                </div>
            </div>
        </div>

        <!-- 商品目錄 -->
        <div class="content-block">
            <img id="catalogImg" src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" class="product-img" alt="商品目錄" onclick="openZoom()">
            <p class="text-[10px] text-slate-300 text-center mt-2 italic font-medium">點擊上方圖片可直接放大查看完整目錄</p>
        </div>

        <!-- 商品選擇區 -->
        <div class="content-block">
            <div class="flex items-center justify-between py-4 border-b border-slate-100">
                <div>
                    <p class="font-bold text-slate-800 text-base">女生隨機卡</p>
                    <p class="text-xs text-indigo-500 font-bold">$230 / 張</p>
                </div>
                <div class="qty-control">
                    <button onclick="updateQty(-1)" class="qty-btn text-xl">−</button>
                    <span id="qtyDisplay" class="w-10 text-center font-black text-slate-700">0</span>
                    <button onclick="updateQty(1)" class="qty-btn text-xl">+</button>
                </div>
            </div>
        </div>

        <!-- 填單資訊 -->
        <div class="content-block">
            <form id="orderForm">
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="input-label">對帳名稱</label>
                        <input type="text" placeholder="匯款姓名" class="form-input" required>
                    </div>
                    <div>
                        <label class="input-label">LINE 社群名稱</label>
                        <input type="text" placeholder="標記用" class="form-input" required>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="input-label">Instagram ID</label>
                        <input type="text" placeholder="@" class="form-input" required>
                    </div>
                    <div>
                        <label class="input-label">聯絡電話</label>
                        <input type="tel" placeholder="09xxxxxxxx" class="form-input" required>
                    </div>
                </div>

                <label class="input-label">Email 信箱</label>
                <input type="email" placeholder="接收確認通知" class="form-input" required>

                <label class="input-label">7-11 賣貨便寄送店家名稱</label>
                <input type="text" placeholder="請填寫完整店名（例：忠孝店）" class="form-input" required>
                <p class="text-[11px] text-slate-400 -mt-2 mb-4 font-medium">
                    👉 <a href="https://emap.pcsc.com.tw/emap.aspx" target="_blank" class="text-indigo-500 underline underline-offset-2">備註：點此查詢 7-11 店名連結</a>
                </p>

                <!-- 聯絡與結帳匯總 -->
                <div class="mt-8 pt-6 border-t border-slate-100">
                    <div class="flex justify-between items-end mb-6">
                        <span class="text-xs font-bold text-slate-400 uppercase tracking-widest">Total Amount</span>
                        <span class="text-4xl font-black text-slate-900 tracking-tighter italic">$<span id="totalDisplay">0</span></span>
                    </div>

                    <!-- LINE 社群按鈕 -->
                    <a href="https://line.me/ti/g2/TOid0k4l1_k3xPqkZ3J5eI4hsooN5cBKcXIz7w?utm_source=invitation&utm_medium=link_copy&utm_campaign=default" target="_blank" class="line-button line-community">
                        💬 務必加入 LINE 社群（掌握進度）
                    </a>

                    <!-- 官方 LINE 按鈕 -->
                    <a href="https://lin.ee/NwgrNPI" target="_blank" class="line-button line-official">
                        🟢 有問題點此問官方 LINE
                    </a>

                    <button type="button" onclick="submitForm()" class="submit-button">
                        CONFIRM & SUBMIT
                    </button>
                    
                    <p class="text-center text-[10px] text-slate-300 mt-8 font-bold tracking-widest uppercase">IG: lottie_lovetobuy</p>
                </div>
            </form>
        </div>
    </div>

    <!-- Zoom Overlay -->
    <div id="zoomOverlay" onclick="this.style.display='none'">
        <img id="zoomedImage" src="">
    </div>

    <script>
        let quantity = 0;
        const UNIT_PRICE = 230;

        function updateQty(delta) {
            quantity = Math.max(0, quantity + delta);
            document.getElementById('qtyDisplay').innerText = quantity;
            document.getElementById('totalDisplay').innerText = (quantity * UNIT_PRICE).toLocaleString();
        }

        function openZoom() {
            const overlay = document.getElementById('zoomOverlay');
            const zoomImg = document.getElementById('zoomedImage');
            zoomImg.src = document.getElementById('catalogImg').src;
            overlay.style.display = 'flex';
        }

        function submitForm() {
            if (quantity === 0) {
                alert("🛒 請先選購商品數量！");
                return;
            }
            alert("✅ 訂單內容已確認 (此為版型展示頁面)");
        }
    </script>
</body>
</html>

