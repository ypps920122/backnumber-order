 
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BackNumber 3rd Pre-order</title>
    <!-- 使用 Tailwind CSS 進行樣式渲染 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&display=swap');
        
        body { 
            font-family: 'Noto Sans TC', sans-serif; 
            background-color: #f8fafc; 
            color: #1e293b;
            -webkit-tap-highlight-color: transparent; 
        }

        /* 規格按鈕樣式 */
        .spec-btn {
            padding: 10px 16px;
            font-size: 13px;
            font-weight: 500;
            border-radius: 14px;
            border: 1px solid #e2e8f0;
            background: white;
            color: #64748b;
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .spec-btn.active {
            background: #1e293b;
            color: white;
            border-color: #1e293b;
            transform: translateY(-1px);
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }

        /* 底部明細抽屜動畫 */
        #cart-drawer {
            transition: all 0.5s cubic-bezier(0.32, 0.72, 0, 1);
            max-height: 72px;
        }

        #cart-drawer.expanded {
            max-height: 80vh;
        }

        /* 隱藏滾動條 */
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="pb-32">

    <div class="max-w-[500px] mx-auto px-6">
        <!-- 標題 -->
        <header class="py-14 text-center">
            <h1 class="text-4xl font-[900] italic tracking-tighter text-slate-900">BackNumber 3rd</h1>
            <p class="text-[10px] text-slate-400 font-bold uppercase tracking-[0.5em] mt-3">Fan Support Project</p>
        </header>

        <!-- 導覽公告 -->
        <div class="bg-slate-900 text-white p-7 rounded-[32px] mb-10 shadow-2xl shadow-slate-200">
            <h4 class="font-black text-xs mb-3 italic tracking-widest text-indigo-400 uppercase">Notice / 預購說明</h4>
            <div class="text-[13px] leading-relaxed space-y-2 opacity-90">
                <p>• 請選擇顏色與尺寸後點擊「加入購物車」。</p>
                <p>• 點擊底部黑色區塊可展開/編輯選購清單。</p>
                <p>• 匯款帳號：國泰世華 (013) 699510910862</p>
            </div>
        </div>

        <!-- 商品清單 (由 JS 動態生成) -->
        <div id="products-container" class="space-y-8"></div>

        <!-- 寄送資訊表單 -->
        <div id="form-section" class="hidden mt-16 pt-16 border-t border-slate-200">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.3em] mb-10 text-center">Shipping Info / 寄送資訊</h2>
            <form id="order-form" onsubmit="handleOrderSubmit(event)">
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <input type="text" id="user-name" placeholder="對帳姓名" class="w-full p-5 bg-white border border-slate-100 rounded-2xl text-sm focus:ring-2 focus:ring-slate-200 outline-none" required>
                    <input type="text" id="user-line" placeholder="LINE 名稱" class="w-full p-5 bg-white border border-slate-100 rounded-2xl text-sm focus:ring-2 focus:ring-slate-200 outline-none" required>
                </div>
                <input type="text" id="user-store" placeholder="7-11 取貨門市名稱" class="w-full p-5 bg-white border border-slate-100 rounded-2xl text-sm mb-4 focus:ring-2 focus:ring-slate-200 outline-none" required>
                <textarea id="user-note" placeholder="備註 (若有其他需求請填寫)" class="w-full p-5 bg-white border border-slate-100 rounded-2xl text-sm h-32 mb-6 focus:ring-2 focus:ring-slate-200 outline-none"></textarea>
                <button type="submit" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black text-lg shadow-xl shadow-indigo-100 active:scale-95 transition-all">
                    提交訂購單
                </button>
            </form>
        </div>
    </div>

    <!-- 底部伸縮購物車 Bar -->
    <div id="cart-drawer" class="hidden fixed bottom-6 left-5 right-5 max-w-[460px] mx-auto bg-slate-900 text-white rounded-[35px] shadow-2xl z-50 overflow-hidden">
        <!-- 頂部總覽 (點擊展開) -->
        <div class="h-[72px] flex justify-between items-center px-9 cursor-pointer" onclick="toggleCart()">
            <div class="flex items-center gap-3">
                <span id="cart-badge" class="bg-indigo-500 text-white text-[10px] font-black px-2.5 py-1 rounded-full">0</span>
                <span class="text-[14px] font-bold tracking-tight">查看選購明細</span>
            </div>
            <div class="flex items-center gap-4">
                <span class="text-2xl font-[900] italic tracking-tighter">$<span id="cart-total">0</span></span>
                <span id="arrow-icon" class="text-[10px] opacity-30 transition-transform duration-300">▲</span>
            </div>
        </div>
        
        <!-- 展開後的內容 -->
        <div class="px-9 pb-9 overflow-y-auto no-scrollbar" style="max-height: 60vh;">
            <div id="cart-list" class="divide-y divide-white/10">
                <!-- 購物項目會在這裡 -->
            </div>
            <button onclick="toggleCart()" class="w-full bg-white/5 py-4 rounded-2xl mt-8 text-[11px] font-bold uppercase tracking-[0.2em] text-white/30">
                收起明細
            </button>
        </div>
    </div>

    <script>
        // 商品資料庫
        const products = [
            { id: '1', name: 'Oversized Sweatshirt', price: 1850, colors: ['Black', 'Grey', 'Green'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: '2', name: 'Zip-up Hoodie', price: 2100, colors: ['Black', 'Grey'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: '3', name: 'Logo T-shirt (A款)', price: 1200, colors: ['White', 'Black'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: '4', name: 'Photo T-shirt (B款)', price: 1350, colors: ['White'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: '5', name: 'Canvas Tote Bag', price: 650, colors: ['Natural', 'Black'], sizes: null }
        ];

        let cart = [];
        let tempSelections = {};

        // 渲染商品頁面
        function render() {
            const container = document.getElementById('products-container');
            container.innerHTML = products.map(p => `
                <div class="bg-white p-8 rounded-[40px] border border-slate-100 shadow-sm">
                    <div class="flex justify-between items-start mb-6">
                        <h3 class="text-xl font-black text-slate-800 tracking-tight">${p.name}</h3>
                        <span class="text-indigo-600 font-[900] italic text-lg">$${p.price.toLocaleString()}</span>
                    </div>
                    
                    ${p.colors ? `
                        <div class="mb-5">
                            <p class="text-[10px] font-bold text-slate-300 uppercase tracking-widest mb-3">Color</p>
                            <div class="flex flex-wrap gap-2">
                                ${p.colors.map(c => `<button onclick="pick('${p.id}','color','${c}',this)" class="spec-btn">${c}</button>`).join('')}
                            </div>
                        </div>
                    ` : ''}

                    ${p.sizes ? `
                        <div class="mb-8">
                            <p class="text-[10px] font-bold text-slate-300 uppercase tracking-widest mb-3">Size</p>
                            <div class="flex flex-wrap gap-2">
                                ${p.sizes.map(s => `<button onclick="pick('${p.id}','size','${s}',this)" class="spec-btn">${s}</button>`).join('')}
                            </div>
                        </div>
                    ` : ''}

                    <button onclick="addToCart('${p.id}')" class="w-full bg-slate-900 text-white py-5 rounded-[22px] font-bold text-sm active:scale-95 transition-all shadow-xl shadow-slate-100">
                        加入購物車
                    </button>
                </div>
            `).join('');
        }

        // 處理規格點選
        window.pick = (pid, type, val, btn) => {
            if(!tempSelections[pid]) tempSelections[pid] = {};
            tempSelections[pid][type] = val;
            
            // UI 切換
            const siblings = btn.parentElement.querySelectorAll('button');
            siblings.forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
        };

        // 加入購物車
        window.addToCart = (pid) => {
            const product = products.find(p => p.id === pid);
            const selection = tempSelections[pid] || {};

            if(product.colors && !selection.color) return alert('請選擇顏色 Color');
            if(product.sizes && !selection.size) return alert('請選擇尺寸 Size');

            cart.push({
                ...product,
                chosenColor: selection.color || '',
                chosenSize: selection.size || '',
                cartId: Date.now()
            });

            updateUI();
        };

        // 更新購物車與介面
        window.updateUI = () => {
            const drawer = document.getElementById('cart-drawer');
            const list = document.getElementById('cart-list');
            const totalDisplay = document.getElementById('cart-total');
            const badge = document.getElementById('cart-badge');
            const formSection = document.getElementById('form-section');

            if(cart.length > 0) {
                drawer.classList.remove('hidden');
                formSection.classList.remove('hidden');
            } else {
                drawer.classList.add('hidden');
                formSection.classList.add('hidden');
                drawer.classList.remove('expanded');
            }

            badge.innerText = cart.length;
            let total = 0;
            list.innerHTML = cart.map((item, idx) => {
                total += item.price;
                return `
                    <div class="flex justify-between items-center py-5">
                        <div>
                            <p class="font-bold text-[15px]">${item.name}</p>
                            <p class="text-[10px] text-white/30 font-bold uppercase tracking-widest mt-0.5">${item.chosenColor} ${item.chosenSize ? '/ ' + item.chosenSize : ''}</p>
                        </div>
                        <div class="flex items-center gap-5">
                            <span class="font-[900] italic text-sm">$${item.price}</span>
                            <button onclick="remove(${idx})" class="text-white/20 hover:text-white">✕</button>
                        </div>
                    </div>
                `;
            }).join('');
            totalDisplay.innerText = total.toLocaleString();
        };

        window.remove = (idx) => {
            cart.splice(idx, 1);
            updateUI();
        };

        window.toggleCart = () => {
            const drawer = document.getElementById('cart-drawer');
            const arrow = document.getElementById('arrow-icon');
            drawer.classList.toggle('expanded');
            
            if(drawer.classList.contains('expanded')) {
                arrow.style.transform = 'rotate(180deg)';
            } else {
                arrow.style.transform = 'rotate(0deg)';
            }
        };

        window.handleOrderSubmit = (e) => {
            e.preventDefault();
            alert('🎉 訂單提交模擬成功！\n(目前為純網頁版，如需串接資料庫儲存請告知)');
            cart = [];
            updateUI();
            e.target.reset();
        };

        // 頁面加載
        window.onload = render;
    </script>
</body>
</html>

