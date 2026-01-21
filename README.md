
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BackNumber 預購訂購單</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Firebase SDK -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-firestore.js";

        // 環境變數
        const firebaseConfig = JSON.parse(__firebase_config);
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'backnumber-order-app';
        
        // 初始化 Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        // 全域變數
        window.cart = [];
        window.isAdmin = false;
        window.currentUser = null;

        // 商品資料
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

        // 初始化驗證
        signInAnonymously(auth).catch(console.error);
        onAuthStateChanged(auth, (user) => { window.currentUser = user; });

        // 初始化頁面
        window.onload = () => {
            renderProducts();
            updateCartUI();
        };

        // 渲染商品
        function renderProducts() {
            const list = document.getElementById('product-list');
            products.forEach(p => {
                const card = document.createElement('div');
                card.className = 'bg-white p-5 border border-slate-100 rounded-2xl mb-4 shadow-sm';
                
                let optionsHtml = '';
                if(p.colors) {
                    optionsHtml += `<div class="mb-3"><div class="flex flex-wrap gap-2" id="colors-${p.id}">${p.colors.map(c => `<button onclick="selectOpt('${p.id}','color','${c}',this)" class="px-3 py-1.5 text-xs rounded-lg border border-slate-200 text-slate-500 bg-slate-50">${c}</button>`).join('')}</div></div>`;
                }
                if(p.sizes) {
                    optionsHtml += `<div class="mb-3"><div class="flex flex-wrap gap-2" id="sizes-${p.id}">${p.sizes.map(s => `<button onclick="selectOpt('${p.id}','size','${s}',this)" class="px-3 py-1.5 text-xs rounded-lg border border-slate-200 text-slate-500 bg-slate-50">${s}</button>`).join('')}</div></div>`;
                }

                card.innerHTML = `
                    <div class="mb-4">
                        <h3 class="font-black text-slate-800">${p.name}</h3>
                        <p class="text-indigo-600 font-bold text-sm italic">$${p.price.toLocaleString()}</p>
                    </div>
                    ${optionsHtml}
                    <button onclick="addToCart('${p.id}')" class="w-full bg-indigo-600 text-white py-3 rounded-xl font-bold text-sm active:scale-95 transition-transform">加入購物車</button>
                `;
                list.appendChild(card);
            });
        }

        // 規格選擇邏輯
        const selections = {};
        window.selectOpt = (pid, type, val, btn) => {
            if(!selections[pid]) selections[pid] = {};
            selections[pid][type] = val;
            const parent = btn.parentElement;
            parent.querySelectorAll('button').forEach(b => {
                b.classList.remove('bg-slate-900', 'text-white');
                b.classList.add('bg-slate-50', 'text-slate-500');
            });
            btn.classList.add('bg-slate-900', 'text-white');
            btn.classList.remove('bg-slate-50', 'text-slate-500');
        };

        // 加入購物車
        window.addToCart = (pid) => {
            const p = products.find(x => x.id === pid);
            const sel = selections[pid] || {};
            if(p.colors && !sel.color) return alert('請選擇顏色');
            if(p.sizes && !sel.size) return alert('請選擇尺寸');

            window.cart.push({
                ...p,
                selColor: sel.color,
                selSize: sel.size,
                cartId: Date.now() + Math.random()
            });
            updateCartUI();
        };

        // 更新購物車 UI
        window.updateCartUI = () => {
            const drawer = document.getElementById('cartDrawer');
            const list = document.getElementById('cart-items-container');
            const totalDisplay = document.getElementById('totalDisplay');
            const countBadge = document.getElementById('countBadge');
            const checkoutSec = document.getElementById('checkout-section');

            if(window.cart.length === 0) {
                drawer.classList.add('hidden');
                checkoutSec.classList.add('hidden');
            } else {
                drawer.classList.remove('hidden');
                checkoutSec.classList.remove('hidden');
            }

            countBadge.innerText = window.cart.length;
            list.innerHTML = '';
            let total = 0;
            window.cart.forEach((item, idx) => {
                total += item.price;
                const div = document.createElement('div');
                div.className = 'flex justify-between items-center py-3 border-b border-white/5';
                div.innerHTML = `
                    <div>
                        <p class="font-bold text-sm">${item.name}</p>
                        <p class="text-[10px] text-white/40 font-bold">${item.selColor || ''} ${item.selSize ? '/ '+item.selSize : ''}</p>
                    </div>
                    <div class="flex items-center gap-4">
                        <span class="font-black italic text-sm">$${item.price}</span>
                        <button onclick="window.removeItem(${idx})" class="text-white/30">✕</button>
                    </div>
                `;
                list.appendChild(div);
            });
            totalDisplay.innerText = total.toLocaleString();
        };

        window.removeItem = (idx) => {
            window.cart.splice(idx, 1);
            updateCartUI();
        };

        window.toggleCart = () => {
            const drawer = document.getElementById('cartDrawer');
            drawer.classList.toggle('max-h-[70px]');
            drawer.classList.toggle('max-h-[80vh]');
        };

        // 提交表單
        window.submitOrder = async (e) => {
            e.preventDefault();
            if(!window.currentUser) return alert('系統初始化中，請稍後');
            
            const btn = e.target.querySelector('button');
            btn.disabled = true;
            btn.innerText = '提交中...';

            const formData = {
                name: document.getElementById('f-name').value,
                line: document.getElementById('f-line').value,
                phone: document.getElementById('f-phone').value,
                ig: document.getElementById('f-ig').value,
                store: document.getElementById('f-store').value,
                note: document.getElementById('f-note').value
            };

            try {
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'orders'), {
                    customer: formData,
                    items: window.cart,
                    total: window.cart.reduce((s,i) => s+i.price, 0),
                    createdAt: new Date().toISOString()
                });
                alert('🎉 訂單提交成功！');
                window.cart = [];
                updateCartUI();
                e.target.reset();
            } catch(err) {
                alert('提交失敗，請檢查網路');
            } finally {
                btn.disabled = false;
                btn.innerText = '提交訂單';
            }
        };

        // 管理員模式
        window.tryAdmin = () => {
            const pass = prompt('請輸入管理密碼');
            if(pass === '1234') {
                document.getElementById('user-view').classList.add('hidden');
                document.getElementById('admin-view').classList.remove('hidden');
                loadOrders();
            }
        };

        function loadOrders() {
            const q = collection(db, 'artifacts', appId, 'public', 'data', 'orders');
            onSnapshot(q, (snap) => {
                const list = document.getElementById('admin-list');
                list.innerHTML = '';
                snap.forEach(doc => {
                    const data = doc.data();
                    const div = document.createElement('div');
                    div.className = 'bg-white p-4 rounded-xl shadow-sm border mb-4';
                    div.innerHTML = `
                        <div class="flex justify-between mb-2">
                            <span class="font-bold">${data.customer.name}</span>
                            <span class="text-indigo-600 font-black">$${data.total}</span>
                        </div>
                        <div class="text-[11px] text-slate-500 mb-2">LINE: ${data.customer.line} | IG: ${data.customer.ig} | 店家: ${data.customer.store}</div>
                        <div class="bg-slate-50 p-2 rounded text-[10px] text-slate-600">
                            ${data.items.map(i => `• ${i.name} (${i.selColor||''} ${i.selSize||''})`).join('<br>')}
                        </div>
                    `;
                    list.appendChild(div);
                });
            });
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700;900&display=swap');
        body { font-family: 'Noto Sans TC', sans-serif; background: #fcfcfc; }
        .form-input { width: 100%; padding: 12px; border: 1px solid #eee; border-radius: 12px; font-size: 14px; margin-bottom: 12px; outline-color: #6366f1; }
    </style>
</head>
<body class="pb-24">

    <!-- 用戶介面 -->
    <div id="user-view" class="max-w-[600px] mx-auto px-4">
        <header class="py-12 text-center" onclick="tryAdmin()">
            <h1 class="text-3xl font-black italic tracking-tighter">BackNumber 3rd</h1>
            <p class="text-[10px] text-slate-300 font-bold uppercase tracking-[0.4em] mt-2">Admin: Click here</p>
        </header>

        <div class="bg-indigo-50 p-4 rounded-2xl mb-8 text-[12px] text-indigo-900 leading-relaxed">
            <p>✅ 點擊規格後「加入購物車」，即可展開下方明細調整。</p>
            <p class="font-bold">國泰世華 (013) 699510910862</p>
        </div>

        <div id="product-list"></div>

        <div id="checkout-section" class="hidden mt-12 pt-10 border-t border-slate-100">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-widest mb-6">Checkout Info / 寄送資訊</h2>
            <form onsubmit="submitOrder(event)">
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" id="f-name" placeholder="對帳姓名" class="form-input" required>
                    <input type="text" id="f-line" placeholder="LINE社群名稱" class="form-input" required>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" id="f-ig" placeholder="Instagram ID" class="form-input" required>
                    <input type="tel" id="f-phone" placeholder="手機號碼" class="form-input" required>
                </div>
                <input type="text" id="f-store" placeholder="7-11 店家名稱" class="form-input" required>
                <textarea id="f-note" placeholder="備註 (非必填)" class="form-input h-24 pt-3"></textarea>
                <button type="submit" class="w-full bg-slate-900 text-white p-4 rounded-xl font-black shadow-xl">確認提交訂單</button>
            </form>
        </div>
    </div>

    <!-- 管理員介面 -->
    <div id="admin-view" class="hidden max-w-[600px] mx-auto px-4 py-8">
        <div class="flex justify-between items-center mb-8">
            <h1 class="text-2xl font-black">後台訂單列表</h1>
            <button onclick="location.reload()" class="text-xs text-blue-500 underline">登出/重整</button>
        </div>
        <div id="admin-list"></div>
    </div>

    <!-- 摺疊購物籃 -->
    <div id="cartDrawer" class="hidden fixed bottom-0 left-1/2 -translate-x-1/2 w-full max-w-[600px] bg-slate-900 text-white rounded-t-3xl shadow-2xl z-50 transition-all duration-300 overflow-hidden max-h-[70px]">
        <div class="h-[70px] flex justify-between items-center px-6 cursor-pointer" onclick="toggleCart()">
            <div class="flex items-center gap-2">
                <span id="countBadge" class="bg-indigo-500 text-white text-[10px] font-bold px-2 py-0.5 rounded-full">0</span>
                <span class="text-sm font-bold">查看/調整選購明細</span>
            </div>
            <div class="flex items-center gap-3">
                <span class="text-xl font-black italic">$<span id="totalDisplay">0</span></span>
                <span class="text-[10px] opacity-40">▲</span>
            </div>
        </div>
        <div class="px-6 pb-6 overflow-y-auto max-h-[calc(80vh-70px)]">
            <div id="cart-items-container"></div>
            <button onclick="toggleCart()" class="w-full bg-white/10 py-3 rounded-xl mt-4 text-[10px] font-bold uppercase tracking-widest text-white/50">收起明細</button>
        </div>
    </div>

</body>
</html>

