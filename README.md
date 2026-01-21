
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
            margin: 0; padding: 0;
            -webkit-font-smoothing: antialiased;
        }

        .main-container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            min-height: 100vh;
            padding-bottom: 120px; /* 預留空間給底部懸浮 Bar */
        }

        /* 商品卡片：下拉式效果 */
        .product-card {
            border: 1px solid #f1f5f9;
            border-radius: 20px;
            margin-bottom: 12px;
            overflow: hidden;
            transition: all 0.3s ease;
        }
        .product-header {
            padding: 20px;
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: white;
        }
        .product-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.3s ease-out;
            background: #fcfcfd;
            padding: 0 20px;
        }
        .product-card.open .product-content {
            max-height: 500px;
            padding: 10px 20px 20px 20px;
        }
        .product-card.open .arrow-icon {
            transform: rotate(180deg);
        }

        /* 規格選項 */
        .option-chip {
            padding: 10px 14px;
            border: 1px solid #e2e8f0;
            border-radius: 12px;
            font-size: 13px;
            cursor: pointer;
            background: white;
            transition: 0.2s;
        }
        .option-chip.selected {
            background: #1e293b;
            color: white;
            border-color: #1e293b;
        }

        /* 底部懸浮結帳 Bar */
        .floating-bar {
            position: fixed;
            bottom: 24px;
            left: 50%;
            transform: translateX(-50%);
            width: calc(100% - 40px);
            max-width: 500px;
            background: #0f172a;
            color: white;
            border-radius: 30px;
            padding: 12px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            z-index: 100;
        }

        /* 購物車明細彈窗 */
        .cart-overlay {
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.5);
            z-index: 150;
            display: none;
            align-items: flex-end;
        }
        .cart-modal {
            width: 100%;
            max-width: 600px;
            background: white;
            border-radius: 30px 30px 0 0;
            padding: 30px;
            max-height: 80vh;
            overflow-y: auto;
            margin: 0 auto;
        }

        .input-style {
            width: 100%; padding: 14px; background: #f1f5f9; border-radius: 14px; border: 1px solid transparent; margin-bottom: 12px;
        }
    </style>
</head>
<body>

<div class="main-container">
    <!-- Header -->
    <div class="py-10 text-center">
        <h1 class="text-3xl font-black italic tracking-tighter">BackNumber 3rd</h1>
        <p class="text-[10px] text-slate-400 font-bold uppercase tracking-[0.4em] mt-2">Lottie Fansite Management</p>
    </div>

    <div class="px-5">
        <!-- 公告圖片 -->
        <div class="mb-8">
            <img src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" 
                 class="w-full rounded-[24px] shadow-sm border border-slate-100" 
                 onclick="window.open(this.src)">
        </div>

        <!-- 須知區塊 -->
        <div class="bg-indigo-50 border border-indigo-100 rounded-[24px] p-6 mb-10 text-[13px] text-indigo-900">
            <p class="font-bold mb-1 italic tracking-wider">PRE-ORDER INFO</p>
            <p>• 預購至 2/1 20:00，六月中出貨。</p>
            <p>• 先匯款後填單，國泰 (013) 699510910862。</p>
        </div>

        <!-- 商品清單 -->
        <h2 class="text-[11px] font-black text-slate-400 uppercase tracking-widest mb-4 ml-2">Shop Items / 商品目錄</h2>
        <div id="product-list"></div>

        <!-- 結帳表單 (隱藏，直到按下懸浮 Bar 的結帳) -->
        <div id="checkout-section" class="mt-20 pt-10 border-t border-slate-100 mb-20">
            <h2 class="text-center font-black text-xl mb-8">收件資訊 Checkout</h2>
            <form id="orderForm" onsubmit="handleFinalSubmit(event)">
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" id="form-name" placeholder="對帳姓名" class="input-style" required>
                    <input type="text" id="form-line" placeholder="LINE社群名稱" class="input-style" required>
                </div>
                <input type="text" id="form-store" placeholder="7-11 取貨門市名稱" class="input-style" required>
                <textarea id="form-note" placeholder="備註內容" class="input-style h-24 pt-4"></textarea>
                <p class="text-[10px] text-slate-400 text-center mb-6 px-4">提交前請確認金額是否正確</p>
                <button type="submit" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black text-lg">
                    確認並提交訂單
                </button>
            </form>
        </div>
    </div>
</div>

<!-- 底部懸浮 Bar -->
<div id="floating-bar" class="floating-bar hidden">
    <div class="flex items-center gap-3 cursor-pointer" onclick="openCartModal()">
        <div class="bg-indigo-500 w-6 h-6 rounded-full flex items-center justify-center text-[10px] font-black" id="bar-count">0</div>
        <div class="text-sm font-bold opacity-80 underline underline-offset-4">查看明細</div>
    </div>
    <div class="flex items-center gap-5">
        <div class="text-right">
            <p class="text-[9px] opacity-40 uppercase font-bold tracking-tighter">Total Amount</p>
            <p class="text-xl font-black italic">$<span id="bar-total">0</span></p>
        </div>
        <button onclick="scrollToCheckout()" class="bg-white text-slate-900 px-6 py-2.5 rounded-full font-black text-sm active:scale-95 transition-all">
            結帳
        </button>
    </div>
</div>

<!-- 購物車明細彈窗 -->
<div id="cart-overlay" class="cart-overlay" onclick="closeCartModal(event)">
    <div class="cart-modal" onclick="event.stopPropagation()">
        <div class="flex justify-between items-center mb-6">
            <h3 class="text-xl font-black">選購明細</h3>
            <button onclick="closeCartModal(null)" class="text-slate-300">✕</button>
        </div>
        <div id="cart-items-container" class="space-y-4 mb-8"></div>
        <button onclick="closeCartModal(null)" class="w-full bg-slate-100 py-4 rounded-2xl font-bold text-slate-500">繼續購物</button>
    </div>
</div>

<script>
    const products = [
        { id: 'p1', name: 'Oversized Sweatshirt', price: 1850, colors: ['Black', 'Grey', 'Green'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p2', name: 'Zip-up Hoodie', price: 2100, colors: ['Black', 'Grey'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p3', name: 'Logo T-shirt (A款)', price: 1200, colors: ['White', 'Black'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p4', name: 'Photo T-shirt (B款)', price: 1350, colors: ['White'], sizes: ['S', 'M', 'L', 'XL'] },
        { id: 'p5', name: 'Canvas Tote Bag', price: 650, colors: ['Natural', 'Black'], sizes: null },
        { id: 'p6', name: 'Acrylic Keyring', price: 380, colors: null, sizes: null }
    ];

    let cart = [];
    let activeSelections = {};

    function init() {
        const list = document.getElementById('product-list');
        products.forEach(p => {
            const el = document.createElement('div');
            el.className = 'product-card';
            el.id = `card-${p.id}`;
            
            let opts = '';
            if(p.colors) {
                opts += `<div class="mb-4"><p class="text-[10px] font-bold text-slate-400 mb-2">COLOR</p><div class="flex flex-wrap gap-2">${p.colors.map(c => `<div class="option-chip" onclick="selectOpt(this,'${p.id}','color','${c}')">${c}</div>`).join('')}</div></div>`;
            }
            if(p.sizes) {
                opts += `<div class="mb-4"><p class="text-[10px] font-bold text-slate-400 mb-2">SIZE</p><div class="flex flex-wrap gap-2">${p.sizes.map(s => `<div class="option-chip" onclick="selectOpt(this,'${p.id}','size','${s}')">${s}</div>`).join('')}</div></div>`;
            }

            el.innerHTML = `
                <div class="product-header" onclick="toggleProduct('${p.id}')">
                    <div>
                        <h3 class="font-bold text-slate-800">${p.name}</h3>
                        <p class="text-indigo-600 font-black italic">$${p.price.toLocaleString()}</p>
                    </div>
                    <span class="arrow-icon text-slate-300 transition-transform">▼</span>
                </div>
                <div class="product-content">
                    <div class="pt-2 border-t border-slate-50">
                        ${opts}
                        <button onclick="addToCart('${p.id}')" class="w-full bg-slate-900 text-white py-4 rounded-xl font-bold mt-4">加入購物車</button>
                    </div>
                </div>
            `;
            list.appendChild(el);
        });
    }

    function toggleProduct(id) {
        const el = document.getElementById(`card-${id}`);
        const isOpen = el.classList.contains('open');
        document.querySelectorAll('.product-card').forEach(c => c.classList.remove('open'));
        if(!isOpen) el.classList.add('open');
    }

    function selectOpt(el, pid, type, val) {
        if(!activeSelections[pid]) activeSelections[pid] = {};
        activeSelections[pid][type] = val;
        el.parentElement.querySelectorAll('.option-chip').forEach(c => c.classList.remove('selected'));
        el.classList.add('selected');
    }

    function addToCart(pid) {
        const p = products.find(x => x.id === pid);
        const sel = activeSelections[pid] || {};
        if(p.colors && !sel.color) return alert('請選擇顏色');
        if(p.sizes && !sel.size) return alert('請選擇尺寸');

        cart.push({ ...p, selColor: sel.color || '', selSize: sel.size || '', uid: Date.now() });
        updateUI();
        toggleProduct(pid); // 加入後收起
    }

    function updateUI() {
        const bar = document.getElementById('floating-bar');
        const count = document.getElementById('bar-count');
        const total = document.getElementById('bar-total');
        
        if(cart.length > 0) bar.classList.remove('hidden');
        else bar.classList.add('hidden');

        count.innerText = cart.length;
        let sum = cart.reduce((s, i) => s + i.price, 0);
        total.innerText = sum.toLocaleString();
        
        // 更新彈窗內的清單
        const container = document.getElementById('cart-items-container');
        container.innerHTML = cart.map((item, idx) => `
            <div class="flex justify-between items-center border-b border-slate-50 pb-4">
                <div>
                    <p class="font-bold text-sm">${item.name}</p>
                    <p class="text-[10px] text-slate-400">${item.selColor} ${item.selSize}</p>
                </div>
                <div class="flex items-center gap-4">
                    <span class="font-black">$${item.price}</span>
                    <button onclick="removeItem(${idx})" class="text-slate-300">✕</button>
                </div>
            </div>
        `).join('');
    }

    function removeItem(idx) {
        cart.splice(idx, 1);
        updateUI();
        if(cart.length === 0) closeCartModal();
    }

    function openCartModal() {
        document.getElementById('cart-overlay').style.display = 'flex';
    }

    function closeCartModal(e) {
        document.getElementById('cart-overlay').style.display = 'none';
    }

    function scrollToCheckout() {
        closeCartModal();
        document.getElementById('checkout-section').scrollIntoView({ behavior: 'smooth' });
    }

    function handleFinalSubmit(e) {
        e.preventDefault();
        alert('訂單已提交！請記得截圖並確認匯款。');
        cart = [];
        updateUI();
        e.target.reset();
    }

    window.onload = init;
</script>
</body>
</html>

