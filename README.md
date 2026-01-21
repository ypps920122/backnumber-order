
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
            -webkit-font-smoothing: antialiased;
        }

        .main-card {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            min-height: 100vh;
            box-shadow: 0 0 50px rgba(0,0,0,0.05);
        }

        .sticky-header {
            position: sticky;
            top: 0;
            background: white;
            z-index: 50;
            border-bottom: 1px solid #f1f5f9;
            padding: 20px 16px;
        }

        .product-card {
            background: white;
            border: 1px solid #f1f5f9;
            border-radius: 16px;
            padding: 16px;
            margin-bottom: 16px;
        }

        .option-chip {
            padding: 8px 12px;
            border: 1px solid #e2e8f0;
            border-radius: 8px;
            font-size: 13px;
            cursor: pointer;
            transition: all 0.2s;
            background: #f8fafc;
        }

        .option-chip.selected {
            background: #1e293b;
            color: white;
            border-color: #1e293b;
        }

        .cart-item {
            background: #f8fafc;
            border-radius: 12px;
            padding: 12px;
            margin-bottom: 8px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .btn-primary {
            background: #1e293b;
            color: white;
            width: 100%;
            padding: 16px;
            border-radius: 12px;
            font-weight: 900;
            transition: opacity 0.2s;
        }

        .btn-add {
            background: #6366f1;
            color: white;
            padding: 10px;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 700;
            text-align: center;
            width: 100%;
            margin-top: 12px;
        }

        .form-input {
            width: 100%;
            padding: 12px;
            border: 1px solid #e2e8f0;
            border-radius: 10px;
            margin-bottom: 12px;
            font-size: 15px;
        }

        #zoomOverlay {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(255,255,255,0.98);
            z-index: 1000;
            padding: 10px;
            align-items: center;
            justify-content: center;
        }
    </style>
</head>
<body>

<div class="main-card">
    <!-- Header -->
    <div class="sticky-header text-center">
        <h1 class="text-xl font-black tracking-tighter">BackNumber 3rd Pre-order</h1>
        <p class="text-[9px] text-slate-400 font-bold uppercase tracking-widest mt-1">Lottie Fansite Management</p>
    </div>

    <div class="p-4">
        <!-- 須知區塊 -->
        <div class="bg-slate-50 rounded-2xl p-4 mb-8 text-[13px] leading-relaxed text-slate-600">
            <p>✅ 預購至 2/1 20:00，六月中出貨。</p>
            <p>✅ 先匯款後填單，確認後不退換。</p>
            <p class="mt-2 font-bold text-slate-900">國泰世華 (013) 699510910862</p>
        </div>

        <!-- 目錄圖片 -->
        <div class="mb-8">
            <img src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" 
                 class="w-full rounded-2xl cursor-zoom-in border border-slate-100 shadow-sm" 
                 onclick="openZoom(this.src)">
            <p class="text-[10px] text-center text-slate-400 mt-2">點擊圖片放大目錄</p>
        </div>

        <!-- 商品清單 -->
        <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-4">Select Items / 選擇商品</h2>
        <div id="product-list"></div>

        <!-- 購物車區塊 -->
        <div id="cart-section" class="hidden mt-10">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-4">Your Cart / 已選購清單</h2>
            <div id="cart-items" class="mb-4"></div>
            <div class="flex justify-between items-end p-4 bg-slate-900 text-white rounded-2xl mb-8">
                <span class="text-xs font-bold opacity-60">Total Amount</span>
                <span class="text-3xl font-black italic">$<span id="totalDisplay">0</span></span>
            </div>
        </div>

        <!-- 結帳資訊 -->
        <div id="checkout-section" class="hidden mt-10 pb-20">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-6">Checkout Info / 寄送資訊</h2>
            <form id="orderForm">
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" placeholder="對帳姓名" class="form-input" required>
                    <input type="text" placeholder="LINE社群名稱" class="form-input" required>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" placeholder="Instagram ID" class="form-input" required>
                    <input type="tel" placeholder="手機號碼" class="form-input" required>
                </div>
                <input type="email" placeholder="Email (接收通知)" class="form-input" required>
                <input type="text" placeholder="7-11 店家名稱" class="form-input" required>
                <textarea placeholder="備註 (非必填)" class="form-input h-24 pt-3"></textarea>

                <button type="button" onclick="submitForm()" class="btn-primary mt-4 shadow-xl shadow-indigo-100">
                    CONFIRM & SUBMIT
                </button>
            </form>
        </div>
    </div>
</div>

<!-- Zoom Overlay -->
<div id="zoomOverlay" onclick="this.style.display='none'"><img id="zoomedImage" src=""></div>

<script>
    const products = [
        { id: 'p1', name: 'Oversized Sweatshirt', price: 1850, colors: ['Black', 'Grey', 'Green'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p2', name: 'Zip-up Hoodie', price: 2100, colors: ['Black', 'Grey'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p3', name: 'Logo T-shirt (A款)', price: 1200, colors: ['White', 'Black'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p4', name: 'Photo T-shirt (B款)', price: 1350, colors: ['White'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p5', name: 'Canvas Tote Bag', price: 650, colors: ['Natural', 'Black'], sizes: null },
        { id: 'p6', name: 'Acrylic Keyring', price: 380, colors: null, sizes: null },
        { id: 'p7', name: 'Random Photo Card', price: 230, colors: null, sizes: null },
        { id: 'p8', name: 'Sticker Set', price: 250, colors: null, sizes: null }
    ];

    let cart = [];

    function initProducts() {
        const list = document.getElementById('product-list');
        products.forEach(p => {
            const card = document.createElement('div');
            card.className = 'product-card';
            
            let optionsHtml = '';
            if(p.colors) {
                optionsHtml += `<div class="mb-3"><p class="text-[10px] font-bold text-slate-400 mb-2 uppercase">Color</p><div class="flex flex-wrap gap-2">${p.colors.map(c => `<div class="option-chip" data-type="color" data-val="${c}" onclick="selectOption(this)">${c}</div>`).join('')}</div></div>`;
            }
            if(p.sizes) {
                optionsHtml += `<div class="mb-3"><p class="text-[10px] font-bold text-slate-400 mb-2 uppercase">Size</p><div class="flex flex-wrap gap-2">${p.sizes.map(s => `<div class="option-chip" data-type="size" data-val="${s}" onclick="selectOption(this)">${s}</div>`).join('')}</div></div>`;
            }

            card.innerHTML = `
                <div class="flex justify-between items-start mb-4">
                    <div>
                        <h3 class="font-black text-slate-800">${p.name}</h3>
                        <p class="text-indigo-500 font-bold text-sm">$${p.price.toLocaleString()}</p>
                    </div>
                </div>
                ${optionsHtml}
                <button onclick="addToCart('${p.id}')" class="btn-add transition-all active:scale-95">加入購物車 ADD TO CART</button>
            `;
            list.appendChild(card);
        });
    }

    function selectOption(el) {
        const parent = el.parentElement;
        parent.querySelectorAll('.option-chip').forEach(c => c.classList.remove('selected'));
        el.classList.add('selected');
    }

    function addToCart(productId) {
        const product = products.find(p => p.id === productId);
        const card = event.target.closest('.product-card');
        
        let color = null;
        let size = null;

        if(product.colors) {
            const selected = card.querySelector('[data-type="color"].selected');
            if(!selected) return alert('請選擇顏色！');
            color = selected.dataset.val;
        }
        if(product.sizes) {
            const selected = card.querySelector('[data-type="size"].selected');
            if(!selected) return alert('請選擇尺寸！');
            size = selected.dataset.val;
        }

        // 加入購物車，支援重複品項
        cart.push({
            ...product,
            selectedColor: color,
            selectedSize: size,
            cartId: Date.now() + Math.random()
        });

        renderCart();
    }

    function renderCart() {
        const container = document.getElementById('cart-items');
        const cartSection = document.getElementById('cart-section');
        const checkoutSection = document.getElementById('checkout-section');
        
        container.innerHTML = '';
        
        if(cart.length > 0) {
            cartSection.classList.remove('hidden');
            checkoutSection.classList.remove('hidden');
        } else {
            cartSection.classList.add('hidden');
            checkoutSection.classList.add('hidden');
        }

        let total = 0;
        cart.forEach((item, index) => {
            total += item.price;
            const div = document.createElement('div');
            div.className = 'cart-item';
            div.innerHTML = `
                <div>
                    <p class="font-bold text-sm text-slate-800">${item.name}</p>
                    <p class="text-[10px] text-slate-400 font-bold">
                        ${item.selectedColor ? item.selectedColor : ''} 
                        ${item.selectedSize ? '/ ' + item.selectedSize : ''}
                    </p>
                </div>
                <div class="flex items-center gap-4">
                    <span class="font-bold text-sm">$${item.price}</span>
                    <button onclick="removeFromCart(${index})" class="text-red-400 text-lg">✕</button>
                </div>
            `;
            container.appendChild(div);
        });

        document.getElementById('totalDisplay').innerText = total.toLocaleString();
    }

    function removeFromCart(index) {
        cart.splice(index, 1);
        renderCart();
    }

    function openZoom(src) {
        const overlay = document.getElementById('zoomOverlay');
        document.getElementById('zoomedImage').src = src;
        overlay.style.display = 'flex';
    }

    function submitForm() {
        alert("✅ 訂單已模擬提交！\n感謝您的預購。");
    }

    window.onload = initProducts;
</script>
</body>
</html>

