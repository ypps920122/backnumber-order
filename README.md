
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BackNumber 3rd 代購表單</title>
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
            padding-bottom: 160px; 
        }

        .product-card {
            border: 1px solid #f1f5f9;
            border-radius: 28px;
            margin-bottom: 20px;
            padding: 24px;
            background: white;
            transition: all 0.3s ease;
        }

        .option-chip {
            padding: 10px 14px;
            border: 1px solid #e2e8f0;
            border-radius: 12px;
            font-size: 13px;
            cursor: pointer;
            background: #f8fafc;
            transition: 0.2s;
            color: #64748b;
        }
        .option-chip.selected {
            background: #1e293b;
            color: white;
            border-color: #1e293b;
        }

        .fixed-bottom-bar {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(10px);
            border-top: 1px solid #f1f5f9;
            padding: 16px 20px calc(16px + env(safe-area-inset-bottom));
            z-index: 1000;
            display: flex;
            justify-content: center;
        }
        .bar-content {
            width: 100%;
            max-width: 560px;
            background: #0f172a;
            color: white;
            border-radius: 24px;
            padding: 12px 24px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 -10px 30px rgba(0,0,0,0.1);
        }

        .cart-overlay {
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(4px);
            z-index: 1100;
            display: none;
            align-items: flex-end;
        }
        .cart-modal {
            width: 100%;
            max-width: 600px;
            background: white;
            border-radius: 32px 32px 0 0;
            padding: 32px;
            max-height: 85vh;
            overflow-y: auto;
            margin: 0 auto;
        }

        .input-style {
            width: 100%; padding: 16px; background: #f1f5f9; border-radius: 16px; border: 1px solid transparent; margin-bottom: 12px; font-size: 15px;
        }
        .input-style:focus { background: white; border-color: #6366f1; outline: none; }
        
        .section-label {
            display: block;
            font-size: 11px;
            font-weight: 900;
            color: #94a3b8;
            text-transform: uppercase;
            letter-spacing: 0.1em;
            margin-bottom: 12px;
            margin-left: 4px;
        }
    </style>
</head>
<body>

<div class="main-container">
    <!-- Header -->
    <div class="py-10 text-center">
        <h1 class="text-3xl font-black italic tracking-tighter text-slate-900">BackNumber 3rd 代購表單</h1>
        <p class="text-[10px] text-slate-400 font-bold uppercase tracking-[0.4em] mt-2">Lottie Fansite Management</p>
    </div>

    <div class="px-5">
        <!-- 社群按鈕區 -->
        <div class="grid grid-cols-2 gap-3 mb-8">
            <div class="text-center">
                <a href="#" target="_blank" class="flex items-center justify-center gap-2 bg-[#06C755] text-white py-4 rounded-2xl font-bold text-sm shadow-sm active:scale-95 transition-all">
                    <span>官方 LINE</span>
                </a>
                <p class="text-[10px] text-slate-400 mt-2 font-bold leading-relaxed">一對一售後聯絡<br>務必加入以防失聯</p>
            </div>
            <div class="text-center">
                <a href="https://line.me/ti/g2/your_community_link" target="_blank" class="flex items-center justify-center gap-2 bg-[#06C755] text-white py-4 rounded-2xl font-bold text-sm shadow-sm active:scale-95 transition-all">
                    <span>LINE 社群</span>
                </a>
                <p class="text-[10px] text-slate-400 mt-2 font-bold leading-relaxed">缺貨、進度公告<br>所有狀況統一告知</p>
            </div>
        </div>

        <!-- 目錄圖片 -->
        <div class="mb-8">
            <img src="https://lh3.googleusercontent.com/d/1exfxBowMw9O9j-7cgZs_sGQPmzCD_H5s" 
                 class="w-full rounded-[32px] shadow-sm border border-slate-50 cursor-zoom-in" 
                 onclick="window.open(this.src)">
        </div>

        <!-- 須知內容 -->
        <div class="bg-indigo-50 border border-indigo-100 rounded-[32px] p-8 mb-10 text-[13px] text-indigo-900 leading-relaxed shadow-sm">
            <p class="font-black mb-4 italic tracking-wider text-indigo-600 uppercase">Pre-order Notice / 代購須知</p>
            <div class="space-y-3">
                <p>👉 BackNumber 周邊販售第三波預購，2/1 20:00收單，六月中出貨。</p>
                <p>👉 下單前務必確認顏色尺寸，確認訂單後不接受更換或是取消。</p>
                <p>✅ 不因任何瑕疵問題退貨，若有缺貨情況發生會透過<a href="https://line.me/ti/g2/your_community_link" target="_blank" class="text-indigo-600 font-bold underline decoration-indigo-300 underline-offset-2">LINE 社群</a>聯絡，全額退款。</p>
                <p>✅ 不二收，匯款後就等出貨。（已包含當地運費、國際運費、關稅、寄送運費）</p>
                <p>✅ 先匯款，後填單；填單完成即視為購買，恕不得取消。</p>
            </div>
            <div class="mt-8 pt-6 border-t border-indigo-200">
                <p class="text-[11px] font-bold opacity-60 uppercase tracking-widest">Bank Details / 匯款資訊</p>
                <p class="text-xl font-black tracking-tight mt-1">國泰世華 (013) 699510910862</p>
            </div>
        </div>

        <!-- 商品清單 -->
        <h2 class="text-[11px] font-black text-slate-400 uppercase tracking-widest mb-6 ml-2">Shop Items / 商品目錄</h2>
        <div id="product-list" class="space-y-5"></div>

        <!-- 代購結帳表單 -->
        <div id="checkout-section" class="mt-20 pt-12 border-t border-slate-100">
            <h2 class="text-center font-black text-2xl mb-2">訂單結帳填寫</h2>
            <p class="text-center text-xs text-slate-400 mb-12 uppercase tracking-[0.2em]">Shipping & Payment Info</p>
            
            <form id="orderForm" onsubmit="handleFinalSubmit(event)">
                
                <!-- 付款區塊 -->
                <div class="mb-10">
                    <label class="section-label">01. Payment Info / 付款確認</label>
                    <input type="text" id="form-account" placeholder="付款帳號末五碼" class="input-style" required maxlength="5">
                    <div class="bg-indigo-50/50 p-4 rounded-2xl">
                        <p class="text-[11px] text-indigo-700 font-bold flex items-center gap-2">
                            <span>ℹ️</span>
                            <span>對帳完成後，會在此對帳表單更新進度：</span>
                        </p>
                        <a href="#" target="_blank" class="block text-[11px] text-indigo-500 font-bold mt-1 underline underline-offset-4 ml-6">
                            點此確認對帳進度與狀態 (尚未開放)
                        </a>
                    </div>
                </div>

                <!-- 收件區塊 -->
                <div class="mb-8">
                    <label class="section-label">02. Shipping Info / 收件資訊</label>
                    <div class="grid grid-cols-2 gap-3">
                        <input type="text" id="form-name" placeholder="收件人姓名" class="input-style" required>
                        <input type="text" id="form-line" placeholder="LINE 名稱" class="input-style" required>
                    </div>
                    <div class="grid grid-cols-2 gap-3">
                        <input type="tel" id="form-phone" placeholder="手機號碼" class="input-style" required>
                        <input type="email" id="form-email" placeholder="Email" class="input-style" required>
                    </div>
                    <div class="relative">
                        <input type="text" id="form-store" placeholder="7-11 取貨門市名稱" class="input-style" required>
                        <a href="https://emap.pcsc.com.tw/" target="_blank" class="inline-block text-[11px] text-indigo-500 font-bold mb-4 ml-1 underline decoration-indigo-200 underline-offset-4">
                            點此查詢 7-11 門市有哪些？
                        </a>
                    </div>
                    <textarea id="form-note" placeholder="備註內容 (非必填)" class="input-style h-32 pt-4"></textarea>
                </div>
                
                <div class="bg-slate-50 p-6 rounded-3xl mb-10">
                    <p class="text-[11px] text-slate-400 font-bold mb-2 uppercase tracking-widest">Important Reminder</p>
                    <p class="text-[12px] text-slate-500 leading-relaxed">提交後無法自行修改，請再次確認選購明細與金額正確無誤。填單完成即視為同意代購規範。</p>
                </div>

                <button type="submit" class="w-full bg-indigo-600 text-white p-5 rounded-[24px] font-black text-lg shadow-xl shadow-indigo-100 active:scale-95 transition-all mb-12">
                    確認並提交代購單
                </button>
            </form>
        </div>
    </div>
</div>

<!-- 螢幕底部固定 Bar -->
<div id="fixed-bottom-bar" class="fixed-bottom-bar hidden">
    <div class="bar-content">
        <div class="flex items-center gap-3 cursor-pointer" onclick="openCartModal()">
            <div class="bg-indigo-500 w-6 h-6 rounded-full flex items-center justify-center text-[10px] font-black" id="bar-count">0</div>
            <div class="text-sm font-bold opacity-80 underline underline-offset-4 decoration-indigo-500">選購明細</div>
        </div>
        
        <div class="flex items-center gap-6">
            <div class="text-right">
                <p class="text-[9px] opacity-40 uppercase font-bold tracking-tighter">Total</p>
                <p class="text-xl font-black italic leading-none">$<span id="bar-total">0</span></p>
            </div>
            <button onclick="scrollToCheckout()" class="bg-white text-slate-900 px-8 py-3 rounded-full font-black text-sm active:scale-95 transition-all shadow-lg">
                去結帳
            </button>
        </div>
    </div>
</div>

<!-- 購物車明細彈窗 -->
<div id="cart-overlay" class="cart-overlay" onclick="closeCartModal(event)">
    <div class="cart-modal" onclick="event.stopPropagation()">
        <div class="flex justify-between items-center mb-10">
            <h3 class="text-2xl font-black italic tracking-tighter">Selected Items</h3>
            <button onclick="closeCartModal(null)" class="text-slate-300 text-xl w-10 h-10 flex items-center justify-center">✕</button>
        </div>
        
        <div id="cart-items-container" class="space-y-6 mb-10"></div>

        <div class="border-t border-slate-100 pt-8 mb-10">
            <div class="flex justify-between items-center text-slate-900 text-2xl font-black italic">
                <span class="text-sm text-slate-400 not-italic font-bold">TOTAL AMOUNT</span>
                <span id="modal-total">$0</span>
            </div>
        </div>

        <button onclick="closeCartModal(null)" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-sm">
            繼續選購
        </button>
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
        { id: 'p7', name: 'Random Photo Card', price: 230, colors: null, sizes: null },
        { id: 'p8', name: 'Sticker Set', price: 250, colors: null, sizes: null }
    ];

    let cart = [];
    let activeSelections = {};

    function init() {
        const list = document.getElementById('product-list');
        products.forEach(p => {
            const el = document.createElement('div');
            el.className = 'product-card';
            
            let opts = '';
            if(p.colors) {
                opts += `<div class="mb-5"><p class="text-[10px] font-bold text-slate-400 mb-2 tracking-widest uppercase">Color</p><div class="flex flex-wrap gap-2">${p.colors.map(c => `<div class="option-chip" onclick="selectOpt(this,'${p.id}','color','${c}')">${c}</div>`).join('')}</div></div>`;
            }
            if(p.sizes) {
                opts += `<div class="mb-5"><p class="text-[10px] font-bold text-slate-400 mb-2 tracking-widest uppercase">Size</p><div class="flex flex-wrap gap-2">${p.sizes.map(s => `<div class="option-chip" onclick="selectOpt(this,'${p.id}','size','${s}')">${s}</div>`).join('')}</div></div>`;
            }

            el.innerHTML = `
                <div class="flex justify-between items-start mb-6">
                    <div>
                        <h3 class="font-black text-slate-800 text-lg tracking-tight">${p.name}</h3>
                        <p class="text-indigo-600 font-black italic text-md mt-1">$${p.price.toLocaleString()}</p>
                    </div>
                </div>
                ${opts}
                <button onclick="addToCart('${p.id}')" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-bold mt-4 shadow-lg shadow-slate-100 active:scale-95 transition-all text-sm">
                    加入購物車
                </button>
            `;
            list.appendChild(el);
        });
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
        if(p.colors && !sel.color) return alert('請選擇顏色 Color');
        if(p.sizes && !sel.size) return alert('請選擇尺寸 Size');

        cart.push({ 
            ...p, 
            selColor: sel.color || '', 
            selSize: sel.size || '', 
            uid: Date.now() + Math.random() 
        });
        updateUI();
    }

    function updateUI() {
        const bar = document.getElementById('fixed-bottom-bar');
        const count = document.getElementById('bar-count');
        const total = document.getElementById('bar-total');
        const modalTotal = document.getElementById('modal-total');
        
        if(cart.length > 0) bar.classList.remove('hidden');
        else bar.classList.add('hidden');

        count.innerText = cart.length;
        let sum = cart.reduce((s, i) => s + i.price, 0);
        const formattedSum = sum.toLocaleString();
        total.innerText = formattedSum;
        modalTotal.innerText = `$${formattedSum}`;
        
        const container = document.getElementById('cart-items-container');
        container.innerHTML = cart.map((item, idx) => `
            <div class="flex justify-between items-center pb-4 border-b border-slate-50">
                <div>
                    <p class="font-bold text-slate-800">${item.name}</p>
                    <p class="text-[10px] text-slate-400 font-bold uppercase tracking-widest mt-1">
                        ${item.selColor} ${item.selSize ? '/ ' + item.selSize : ''}
                    </p>
                </div>
                <div class="flex items-center gap-6">
                    <span class="font-black italic text-slate-800">$${item.price.toLocaleString()}</span>
                    <button onclick="removeItem(${idx})" class="w-9 h-9 rounded-full bg-slate-50 flex items-center justify-center text-slate-300 active:scale-90 transition-all text-xs">✕</button>
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
        alert('✅ 代購單已提交！\n感謝參與預購。');
        cart = [];
        updateUI();
        e.target.reset();
        window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    window.onload = init;
</script>
</body>
</html>

