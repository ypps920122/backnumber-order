
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

        .main-container {
            max-width: 600px;
            margin: 0 auto;
            padding-bottom: 200px; /* 為底部摺疊區留空間 */
        }

        .product-card {
            background: white;
            border: 1px solid #f1f5f9;
            border-radius: 16px;
            padding: 16px;
            margin-bottom: 16px;
        }

        .option-chip {
            padding: 6px 12px;
            border: 1px solid #e2e8f0;
            border-radius: 6px;
            font-size: 13px;
            cursor: pointer;
            background: #f8fafc;
            transition: all 0.2s;
        }

        .option-chip.selected {
            background: #1e293b;
            color: white;
            border-color: #1e293b;
        }

        .btn-add {
            background: #6366f1;
            color: white;
            padding: 10px;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 700;
            width: 100%;
            margin-top: 12px;
        }

        /* 摺疊購物籃明細樣式 */
        .cart-drawer {
            position: fixed;
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
            width: 100%;
            max-width: 600px;
            background: #1e293b;
            color: white;
            border-radius: 20px 20px 0 0;
            z-index: 100;
            box-shadow: 0 -10px 30px rgba(0,0,0,0.15);
            transition: max-height 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            max-height: 60px; /* 初始收起狀態 */
            overflow: hidden;
        }

        .cart-drawer.expanded {
            max-height: 80vh; /* 展開狀態 */
        }

        .cart-header {
            height: 60px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 20px;
            cursor: pointer;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }

        .cart-content {
            padding: 20px;
            overflow-y: auto;
            max-height: calc(80vh - 60px);
        }

        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 0;
            border-bottom: 1px solid rgba(255,255,255,0.05);
        }

        .form-input {
            width: 100%;
            padding: 12px;
            border: 1px solid #e2e8f0;
            border-radius: 10px;
            margin-bottom: 12px;
        }

        .hidden { display: none; }
    </style>
</head>
<body>

<div class="main-container">
    <!-- Header -->
    <div class="p-8 text-center">
        <h1 class="text-2xl font-black tracking-tighter italic">BackNumber 3rd Pre-order</h1>
        <p class="text-[10px] text-slate-400 font-bold uppercase tracking-[0.4em] mt-2">Lottie Fansite Management</p>
    </div>

    <div class="px-4">
        <!-- 須知 -->
        <div class="bg-indigo-50 rounded-2xl p-4 mb-6 text-[13px] text-indigo-900 leading-relaxed">
            <p>💡 點擊規格後「加入購物車」，即可在底部展開明細調整。</p>
            <p class="font-bold">匯款帳號：國泰世華 (013) 699510910862</p>
        </div>

        <!-- 商品清單 -->
        <div id="product-list"></div>

        <!-- 結帳填單 -->
        <div id="checkout-section" class="mt-12 pt-10 border-t border-slate-100 hidden">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-6">Checkout Info / 寄送資訊</h2>
            <form id="orderForm">
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" placeholder="對帳姓名" class="form-input" required>
                    <input type="text" placeholder="LINE社群名稱" class="form-input" required>
                </div>
                <input type="tel" placeholder="手機號碼" class="form-input" required>
                <input type="text" placeholder="7-11 店家名稱" class="form-input" required>
                <textarea placeholder="備註 (非必填)" class="form-input h-24 pt-3"></textarea>
                <button type="button" onclick="alert('訂單已模擬提交！')" class="w-full bg-indigo-600 text-white p-4 rounded-xl font-bold mt-4 shadow-lg shadow-indigo-100">確認提交訂單</button>
            </form>
        </div>
    </div>
</div>

<!-- 摺疊式購物籃 -->
<div id="cartDrawer" class="cart-drawer hidden">
    <!-- 縮小狀態的標題列 -->
    <div class="cart-header" onclick="toggleCart()">
        <div class="flex items-center gap-2">
            <span class="text-xs font-bold bg-white text-slate-900 px-2 py-0.5 rounded-full" id="countBadge">0</span>
            <span class="text-sm font-bold tracking-tight">查看 / 調整選購明細</span>
        </div>
        <div class="text-right flex items-center gap-3">
            <span class="text-lg font-black italic">$<span id="totalDisplay">0</span></span>
            <span id="arrowIcon" class="text-xs opacity-50 transition-transform">▲</span>
        </div>
    </div>

    <!-- 展開後的內容 -->
    <div class="cart-content">
        <div id="cart-items-container">
            <!-- 動態插入 -->
        </div>
        <button onclick="toggleCart()" class="w-full bg-white/10 text-white/60 p-3 rounded-lg text-xs font-bold mt-4">收起明細</button>
    </div>
</div>

<script>
    const products = [
        { id: 'p1', name: 'Oversized Sweatshirt', price: 1850, colors: ['Black', 'Grey', 'Green'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p2', name: 'Zip-up Hoodie', price: 2100, colors: ['Black', 'Grey'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p3', name: 'Logo T-shirt (A款)', price: 1200, colors: ['White', 'Black'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p4', name: 'Photo T-shirt (B款)', price: 1350, colors: ['White'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p5', name: 'Canvas Tote Bag', price: 650, colors: ['Natural', 'Black'], sizes: null },
        { id: 'p6', name: 'Acrylic Keyring', price: 380, colors: null, sizes: null },
        { id: 'p7', name: 'Sticker Set', price: 250, colors: null, sizes: null }
    ];

    let cart = [];

    function initProducts() {
        const list = document.getElementById('product-list');
        products.forEach(p => {
            const card = document.createElement('div');
            card.className = 'product-card shadow-sm';
            
            let optionsHtml = '';
            if(p.colors) {
                optionsHtml += `<div class="mb-3"><div class="flex flex-wrap gap-2">${p.colors.map(c => `<div class="option-chip" data-type="color" data-val="${c}" onclick="selectOption(this)">${c}</div>`).join('')}</div></div>`;
            }
            if(p.sizes) {
                optionsHtml += `<div class="mb-3"><div class="flex flex-wrap gap-2">${p.sizes.map(s => `<div class="option-chip" data-type="size" data-val="${s}" onclick="selectOption(this)">${s}</div>`).join('')}</div></div>`;
            }

            card.innerHTML = `
                <div class="mb-4">
                    <h3 class="font-black text-slate-800">${p.name}</h3>
                    <p class="text-indigo-600 font-bold text-sm italic">$${p.price.toLocaleString()}</p>
                </div>
                ${optionsHtml}
                <button onclick="addToCart('${p.id}', this)" class="btn-add active:scale-[0.98] transition-transform">加入購物車</button>
            `;
            list.appendChild(card);
        });
    }

    function selectOption(el) {
        el.parentElement.querySelectorAll('.option-chip').forEach(c => c.classList.remove('selected'));
        el.classList.add('selected');
    }

    function addToCart(productId, btn) {
        const product = products.find(p => p.id === productId);
        const card = btn.closest('.product-card');
        
        let color = null, size = null;
        if(product.colors) {
            const sel = card.querySelector('[data-type="color"].selected');
            if(!sel) { alert('請選擇顏色'); return; }
            color = sel.dataset.val;
        }
        if(product.sizes) {
            const sel = card.querySelector('[data-type="size"].selected');
            if(!sel) { alert('請選擇尺寸'); return; }
            size = sel.dataset.val;
        }

        cart.push({ ...product, selectedColor: color, selectedSize: size });
        updateCartUI();
    }

    function updateCartUI() {
        const drawer = document.getElementById('cartDrawer');
        const checkout = document.getElementById('checkout-section');
        const container = document.getElementById('cart-items-container');
        
        if(cart.length > 0) {
            drawer.classList.remove('hidden');
            checkout.classList.remove('hidden');
        } else {
            drawer.classList.add('hidden');
            checkout.classList.add('hidden');
        }

        document.getElementById('countBadge').innerText = cart.length;
        
        let total = 0;
        container.innerHTML = '';
        cart.forEach((item, index) => {
            total += item.price;
            const div = document.createElement('div');
            div.className = 'cart-item';
            div.innerHTML = `
                <div class="flex-1">
                    <p class="font-bold text-[14px]">${item.name}</p>
                    <p class="text-[10px] text-white/40 font-bold uppercase tracking-wider">${item.selectedColor || ''} ${item.selectedSize ? '/ ' + item.selectedSize : ''}</p>
                </div>
                <div class="flex items-center gap-4">
                    <span class="font-black text-sm italic">$${item.price}</span>
                    <button onclick="removeFromCart(${index})" class="text-white/30 hover:text-white">✕</button>
                </div>
            `;
            container.appendChild(div);
        });

        document.getElementById('totalDisplay').innerText = total.toLocaleString();
    }

    function toggleCart() {
        const drawer = document.getElementById('cartDrawer');
        const arrow = document.getElementById('arrowIcon');
        drawer.classList.toggle('expanded');
        arrow.style.transform = drawer.classList.contains('expanded') ? 'rotate(180deg)' : 'rotate(0deg)';
    }

    function removeFromCart(index) {
        cart.splice(index, 1);
        updateCartUI();
        if(cart.length === 0) {
            document.getElementById('cartDrawer').classList.remove('expanded');
        }
    }

    window.onload = initProducts;
</script>
</body>
</html>

