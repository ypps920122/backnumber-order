
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
            padding: 24px 14px;
        }

        .content-block {
            margin-bottom: 32px;
        }

        /* 須知區塊 */
        .terms-box {
            background: #f8fafc;
            border-radius: 12px;
            padding: 16px;
            font-size: 13.5px;
            line-height: 1.8;
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
        }

        .form-input {
            width: 100%;
            padding: 12px;
            border: 1px solid #e2e8f0;
            border-radius: 8px;
            font-size: 15px;
            margin-bottom: 16px;
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
            cursor: pointer;
            border: none;
            background: none;
        }

        .size-select {
            background: #ffffff;
            border: 1px solid #e2e8f0;
            font-size: 12px;
            border-radius: 6px;
            padding: 4px 8px;
            margin-top: 8px;
            color: #64748b;
        }

        /* LINE 按鈕樣式 */
        .line-button {
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 14px;
            border-radius: 12px;
            font-size: 14px;
            font-weight: 700;
            margin-bottom: 10px;
            text-decoration: none;
            transition: transform 0.1s;
        }
        
        .line-button:active { transform: scale(0.98); }

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
            padding: 18px;
            background: #1e293b;
            color: white;
            border-radius: 12px;
            font-weight: 900;
            font-size: 16px;
            margin-top: 10px;
            border: none;
            cursor: pointer;
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
        <!-- 單一標題 -->
        <div class="mb-8 border-b border-slate-100 pb-4">
            <h1 class="text-2xl font-black text-slate-800 tracking-tighter">BackNumber 3rd Pre-order</h1>
            <p class="text-[10px] text-slate-400 font-bold uppercase tracking-widest mt-1">Lottie Fansite Management</p>
        </div>

        <!-- 訂購須知與條款 -->
        <div class="content-block">
            <div class="terms-box">
                <p>👉 BackNumber 周邊販售第三波預購，2/1 20:00收單，六月中出貨。</p>
                <p>👉 下單前務必確認顏色尺寸，確認訂單後不接受更換或是取消。</p>
                <p>✅ 不因任何瑕疵問題退貨，若有缺貨情況發生會透過 <a href="https://line.me/ti/g2/TOid0k4l1_k3xPqkZ3J5eI4hsooN5cBKcXIz7w?utm_source=invitation&utm_medium=link_copy&utm_campaign=default" target="_blank" class="text-indigo-600 font-bold underline decoration-indigo-200">LINE社群</a> 聯絡，全額退款。</p>
                <p>✅ 不二收，匯款後就等出貨。（已包含當地運費、國際運費、關稅、寄送運費）</p>
                <p>✅ 先匯款，後填單；填單完成即視為購買，<a href="#" target="_blank" class="text-indigo-600 font-bold underline decoration-indigo-200">點這裡可以確認匯款狀況</a>。</p>
                
                <div class="mt-4 pt-4 border-t border-slate-200">
                    <p class="text-[10px] font-bold text-slate-400 uppercase mb-1">Payment Info / 匯款帳號</p>
                    <p class="font-mono text-slate-900 font-black text-lg">國泰世華 (013) 699510910862</p>
                </div>
            </div>
        </div>

        <!-- 商品目錄圖片 -->
        <div class="content-block text-center">
            <img id="catalogImg" src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" class="product-img" alt="商品目錄" onclick="openZoom()">
            <p class="text-[10px] text-slate-300 mt-2 italic font-medium">點擊上方圖片可放大查看</p>
        </div>

        <!-- 商品清單區 -->
        <div class="content-block">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-4">Products / 商品選擇</h2>
            
            <div id="product-list">
                <!-- 商品會由 JS 自動生成 -->
            </div>
        </div>

        <!-- 填單資訊 -->
        <div class="content-block">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-6">Checkout / 填單資訊</h2>
            <form id="orderForm">
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="input-label">對帳名稱</label>
                        <input type="text" placeholder="姓名" class="form-input" required>
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
                <input type="text" placeholder="例：忠孝店" class="form-input" required>
                <p class="text-[11px] text-slate-400 -mt-2 mb-4 font-medium">
                    👉 <a href="https://emap.pcsc.com.tw/emap.aspx" target="_blank" class="text-indigo-500 underline underline-offset-2">點此查詢 7-11 店名連結</a>
                </p>

                <!-- 聯絡與提交 -->
                <div class="mt-8 pt-6 border-t border-slate-100">
                    <div class="flex justify-between items-end mb-6">
                        <span class="text-xs font-bold text-slate-400 uppercase tracking-widest">Total Amount</span>
                        <span class="text-4xl font-black text-slate-900 tracking-tighter italic">$<span id="totalDisplay">0</span></span>
                    </div>

                    <a href="https://line.me/ti/g2/TOid0k4l1_k3xPqkZ3J5eI4hsooN5cBKcXIz7w" target="_blank" class="line-button line-community">
                        💬 務必加入 LINE 社群（掌握進度）
                    </a>

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
        const products = [
            { id: 'p1', name: 'Random Photo Card (女生隨機卡)', price: 230, hasSize: false },
            { id: 'p2', name: 'BackNumber Logo T-shirt (A款)', price: 1200, hasSize: true },
            { id: 'p3', name: 'BackNumber Photo T-shirt (B款)', price: 1350, hasSize: true },
            { id: 'p4', name: 'Oversized Sweatshirt (大學T)', price: 1850, hasSize: true },
            { id: 'p5', name: 'Zip-up Hoodie (連帽外套)', price: 2100, hasSize: true },
            { id: 'p6', name: 'Canvas Tote Bag (帆布袋)', price: 650, hasSize: false },
            { id: 'p7', name: 'Acrylic Keyring (壓克力吊飾)', price: 380, hasSize: false },
            { id: 'p8', name: 'Sticker Set (貼紙組)', price: 250, hasSize: false }
        ];

        let cart = {};

        function initProducts() {
            const list = document.getElementById('product-list');
            products.forEach(p => {
                cart[p.id] = 0;
                const div = document.createElement('div');
                div.className = "flex items-center justify-between py-4 border-b border-slate-50";
                div.innerHTML = `
                    <div class="flex-1">
                        <p class="font-bold text-slate-800 text-[15px]">${p.name}</p>
                        <p class="text-xs text-indigo-500 font-bold">$${p.price.toLocaleString()}</p>
                        ${p.hasSize ? `
                            <select class="size-select">
                                <option value="S">S</option>
                                <option value="M">M</option>
                                <option value="L">L</option>
                                <option value="XL">XL</option>
                            </select>
                        ` : ''}
                    </div>
                    <div class="qty-control">
                        <button type="button" onclick="updateQty('${p.id}', -1)" class="qty-btn">−</button>
                        <span id="qty-${p.id}" class="w-8 text-center font-black text-slate-700 text-sm">0</span>
                        <button type="button" onclick="updateQty('${p.id}', 1)" class="qty-btn">+</button>
                    </div>
                `;
                list.appendChild(div);
            });
        }

        function updateQty(id, delta) {
            cart[id] = Math.max(0, cart[id] + delta);
            document.getElementById(`qty-${id}`).innerText = cart[id];
            
            let total = 0;
            products.forEach(p => {
                total += cart[p.id] * p.price;
            });
            document.getElementById('totalDisplay').innerText = total.toLocaleString();
        }

        function openZoom() {
            const overlay = document.getElementById('zoomOverlay');
            document.getElementById('zoomedImage').src = document.getElementById('catalogImg').src;
            overlay.style.display = 'flex';
        }

        function submitForm() {
            let hasItem = Object.values(cart).some(q => q > 0);
            if (!hasItem) {
                alert("🛒 請先選購商品數量！");
                return;
            }
            alert("✅ 提交成功 (展示模式)");
        }

        window.onload = initProducts;
    </script>
</body>
</html>

