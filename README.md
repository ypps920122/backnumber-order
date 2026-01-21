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
            margin: 30px auto;
            background: white;
            border-radius: 24px;
            box-shadow: 0 4px 25px rgba(0, 0, 0, 0.05);
            overflow: hidden;
        }

        .product-img-container {
            position: relative;
            width: 100%;
            background: #f1f5f9;
            cursor: zoom-in;
        }

        .product-img-container img {
            width: 100%;
            display: block;
        }

        .input-field {
            width: 100%;
            padding: 12px 16px;
            border: 1px solid #e2e8f0;
            border-radius: 12px;
            background: #ffffff;
            transition: all 0.2s;
            font-size: 14px;
        }

        .input-field:focus {
            outline: none;
            border-color: #6366f1;
            box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
        }

        label {
            display: block;
            font-size: 12px;
            font-weight: 700;
            color: #64748b;
            margin-bottom: 6px;
            margin-left: 4px;
        }

        #imgModal {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.85);
            z-index: 9999;
            cursor: zoom-out;
            padding: 20px;
            align-items: center;
            justify-content: center;
        }

        #imgModal img {
            max-width: 100%;
            max-height: 90vh;
            border-radius: 12px;
        }
    </style>
</head>
<body class="p-4 md:p-8">

    <div class="main-card">
        <!-- Header -->
        <div class="p-8 text-center border-b border-slate-50">
            <h1 class="text-2xl font-black text-slate-800 mb-1">BackNumber 第三波預購</h1>
            <p class="text-xs text-slate-400 font-bold tracking-widest uppercase">Lottie Shop Online</p>
        </div>

        <div class="p-6 md:p-8">
            <!-- 訂購須知 -->
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
                    <p class="text-[10px] font-bold text-indigo-400 mb-1 tracking-widest uppercase">Payment Info / 匯款資訊</p>
                    <p class="font-mono text-indigo-900 font-bold text-lg">國泰世華 (013) 699510910862</p>
                </div>
            </div>

            <!-- 商品展示 -->
            <div class="mb-10">
                <h2 class="text-xs font-bold text-slate-400 mb-3 uppercase tracking-widest px-1">Product / 商品目錄</h2>
                <div class="product-img-container rounded-2xl border border-slate-100 shadow-sm overflow-hidden" onclick="showZoom()">
                    <img id="mainImg" src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" alt="商品目錄" onerror="handleError(this)">
                </div>
            </div>

            <!-- 商品選擇 -->
            <div class="mb-10">
                <h2 class="text-xs font-bold text-slate-400 mb-3 uppercase tracking-widest px-1">Selection / 選擇數量</h2>
                <div class="flex items-center justify-between p-5 border border-slate-100 rounded-2xl bg-slate-50/50">
                    <div class="flex items-center">
                        <div class="w-10 h-10 bg-white rounded-lg border border-slate-100 flex items-center justify-center mr-3 text-lg">📸</div>
                        <div>
                            <p class="font-bold text-slate-800">女生隨機卡</p>
                            <p class="text-[11px] text-indigo-500 font-bold">$230</p>
                        </div>
                    </div>
                    <div class="flex items-center bg-white border border-slate-200 rounded-xl overflow-hidden shadow-sm">
                        <button onclick="changeQty(-1)" class="w-10 h-10 flex items-center justify-center hover:bg-slate-50 font-bold text-slate-400 transition-colors">-</button>
                        <span id="qty" class="w-10 text-center text-sm font-black text-slate-700">0</span>
                        <button onclick="changeQty(1)" class="w-10 h-10 flex items-center justify-center hover:bg-slate-50 font-bold text-slate-400 transition-colors">+</button>
                    </div>
                </div>
            </div>

            <!-- 填單資訊 Checkout -->
            <form id="orderForm" class="space-y-5">
                <h2 class="text-xs font-bold text-slate-400 mb-4 uppercase tracking-widest px-1">Checkout / 填單資訊</h2>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label>對帳名稱 (匯款人姓名)</label>
                        <input type="text" placeholder="請輸入姓名" class="input-field" required>
                    </div>
                    <div>
                        <label>LINE 社群名稱</label>
                        <input type="text" placeholder="以便社群中標記聯繫" class="input-field" required>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label>Instagram ID</label>
                        <input type="text" placeholder="@" class="input-field" required>
                    </div>
                    <div>
                        <label>聯絡電話</label>
                        <input type="tel" placeholder="09xxxxxxxx" class="input-field" required>
                    </div>
                </div>

                <div>
                    <label>Email 信箱</label>
                    <input type="email" placeholder="接收訂單確認通知" class="input-field" required>
                </div>

                <div>
                    <label>7-11 賣貨便寄送店家名稱</label>
                    <input type="text" placeholder="請輸入完整店名（例：忠孝店）" class="input-field" required>
                    <p class="mt-2 text-[11px] text-slate-400 font-medium">
                        👉 <a href="https://emap.pcsc.com.tw/emap.aspx" target="_blank" class="text-indigo-500 underline">點此查詢 7-11 店家名稱</a>
                    </p>
                </div>

                <!-- 底部提交區 -->
                <div class="mt-12 p-8 bg-indigo-600 rounded-[32px] text-center shadow-xl shadow-indigo-100">
                    <p class="text-indigo-200 text-[10px] font-black tracking-[0.2em] mb-2 uppercase">Order Total</p>
                    <div class="text-white text-5xl font-black mb-10 tracking-tighter italic">$<span id="total">0</span></div>
                    <button type="button" onclick="handleSubmit()" class="w-full py-4 bg-white text-indigo-600 rounded-2xl font-black tracking-widest hover:bg-indigo-50 transition-all active:scale-95 shadow-md">
                        確認並提交訂單
                    </button>
                    <p class="text-[10px] text-indigo-200/60 mt-6 font-medium tracking-wide">如有任何問題，請私訊 IG: lottie_lovetobuy</p>
                </div>
            </form>
        </div>
    </div>

    <!-- 圖片放大視窗 -->
    <div id="imgModal" onclick="this.style.display='none'">
        <img id="zoomImg" src="" alt="放大預覽">
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

        function handleError(img) {
            img.parentElement.innerHTML = `
                <div class="p-10 text-center bg-slate-50 border-2 border-dashed border-slate-200 rounded-2xl">
                    <p class="text-slate-400 text-sm mb-3">商品圖片載入中</p>
                    <a href="https://drive.google.com/file/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s/view" target="_blank" class="text-indigo-500 underline font-bold text-sm">點此手動開啟目錄圖片</a>
                </div>
            `;
        }

        function handleSubmit() {
            if (qty === 0) {
                alert("🛒 請先選擇欲購買的數量喔！");
                return;
            }
            alert("✅ 提交成功！此為版型展示頁面，實際訂購請確保已完成匯款。");
        }
    </script>
</body>
</html>

