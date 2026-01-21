<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BackNumber 預購訂購單</title>
    <!-- 確保使用官方 CDN 連結 -->
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
            margin: 20px auto;
            background: white;
            border-radius: 24px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }

        .product-img-container {
            position: relative;
            width: 100%;
            background: #f1f5f9;
            cursor: zoom-in;
            min-height: 250px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .product-img-container img {
            width: 100%;
            height: auto;
            display: block;
        }

        .input-field {
            width: 100%;
            padding: 14px 18px;
            border: 2px solid #f1f5f9;
            border-radius: 16px;
            background: #f8fafc;
            transition: all 0.2s;
        }

        .input-field:focus {
            outline: none;
            border-color: #6366f1;
            background: white;
        }

        #imgModal {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.9);
            z-index: 9999;
            padding: 20px;
            align-items: center;
            justify-content: center;
            cursor: zoom-out;
        }

        #zoomImg {
            max-width: 100%;
            max-height: 90vh;
            border-radius: 12px;
        }

        .btn-submit {
            width: 100%;
            padding: 16px;
            background: white;
            color: #0f172a;
            border-radius: 16px;
            font-weight: 900;
            letter-spacing: 1px;
            transition: all 0.2s;
        }

        .btn-submit:active {
            transform: scale(0.98);
        }
    </style>
</head>
<body class="p-4 md:p-8">

    <div class="main-card">
        <div class="p-8 text-center border-b border-slate-50">
            <h1 class="text-2xl font-black text-slate-800 tracking-tight">BackNumber 第三波預購</h1>
            <p class="text-sm text-slate-400 mt-2 font-medium">Lottie 專屬代購訂單</p>
        </div>

        <div class="p-6 md:p-10">
            <!-- 須知 -->
            <div class="bg-indigo-50 rounded-[24px] p-6 mb-8 border border-indigo-100">
                <div class="flex items-center mb-3">
                    <span class="text-xl mr-2">📋</span>
                    <h2 class="font-bold text-indigo-900 text-lg">訂購須知</h2>
                </div>
                <ul class="text-sm text-indigo-800 space-y-3 opacity-90 leading-relaxed">
                    <li class="flex items-start"><span class="mr-2">●</span> 2/1 20:00 收單</li>
                    <li class="flex items-start"><span class="mr-2">●</span> 費用含稅與運費，不二收</li>
                    <li class="flex items-start"><span class="mr-2">●</span> 國泰世華 (013) 699510910862</li>
                </ul>
            </div>

            <!-- 圖片目錄 -->
            <div class="mb-10 text-center">
                <div class="flex justify-between items-center mb-3 px-1">
                    <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest">商品目錄</h2>
                    <span class="text-[10px] text-slate-300">點擊圖片可放大</span>
                </div>
                <div class="product-img-container rounded-[24px] border border-slate-200 shadow-sm overflow-hidden" onclick="showZoom()">
                    <!-- 使用穩定 CDN 連結 -->
                    <img id="mainImg" src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" alt="商品目錄" onerror="handleImgError(this)">
                </div>
            </div>

            <!-- 商品選擇 -->
            <div class="mb-10">
                <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-3 px-1">選擇商品</h2>
                <div class="flex items-center justify-between p-5 border-2 border-slate-50 rounded-[24px] bg-slate-50/30">
                    <div class="flex items-center">
                        <div class="w-10 h-10 bg-white rounded-xl shadow-sm border border-slate-100 flex items-center justify-center mr-4">📸</div>
                        <div>
                            <p class="font-bold text-slate-800">女生隨機卡</p>
                            <p class="text-[11px] text-indigo-500 font-bold">$230 / 張</p>
                        </div>
                    </div>
                    <div class="flex items-center bg-white border border-slate-100 rounded-xl p-1 shadow-sm">
                        <button onclick="changeQty(-1)" class="w-8 h-8 flex items-center justify-center font-bold text-slate-400 hover:text-indigo-600">-</button>
                        <span id="qty" class="w-10 text-center text-sm font-black text-slate-700">0</span>
                        <button onclick="changeQty(1)" class="w-8 h-8 flex items-center justify-center font-bold text-slate-400 hover:text-indigo-600">+</button>
                    </div>
                </div>
            </div>

            <!-- 表單輸入 -->
            <form class="space-y-4">
                <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-3 px-1">填寫資訊</h2>
                <input type="text" placeholder="對帳表名稱 (匯款後請填寫)" class="input-field shadow-sm">
                <input type="text" placeholder="Instagram ID (@帳號)" class="input-field shadow-sm">
                <input type="tel" placeholder="聯絡電話" class="input-field shadow-sm">
                
                <!-- 總計與提交 -->
                <div class="mt-12 p-8 bg-slate-900 rounded-[32px] text-center shadow-xl shadow-slate-200">
                    <p class="text-slate-500 text-[10px] font-black tracking-[0.2em] mb-2">TOTAL AMOUNT</p>
                    <div class="text-white text-5xl font-black mb-10 tracking-tighter">$<span id="total">0</span></div>
                    <button type="button" onclick="handleSubmit()" class="btn-submit hover:bg-slate-50">
                        提交訂單
                    </button>
                    <p class="text-[10px] text-slate-500 mt-6 font-medium">如有問題請私訊 IG: lottie_lovetobuy</p>
                </div>
            </form>
        </div>
    </div>

    <!-- 圖片放大視窗 -->
    <div id="imgModal" onclick="this.style.display='none'">
        <img id="zoomImg" src="" alt="放大檢視">
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
                <div class="p-10 text-center">
                    <p class="text-slate-400 text-sm mb-3">圖片載入中，若長時間未顯示請點擊下方連結</p>
                    <a href="https://drive.google.com/file/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s/view" target="_blank" class="text-indigo-500 underline font-bold text-sm">直接查看商品圖</a>
                </div>
            `;
        }

        function handleSubmit() {
            if (qty === 0) {
                alert("請先選擇商品數量喔！");
                return;
            }
            alert("提交成功！此為預覽頁面，目前僅作版型展示。");
        }
    </script>
</body>
</html>
