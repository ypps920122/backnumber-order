
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BackNumber 3rd Pre-order</title>
    <!-- Tailwind CSS 網格系統 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Firebase SDK (不需安裝，直接從 CDN 引用) -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-firestore.js";

        // Firebase 配置 (會自動由環境注入或使用預設)
        const firebaseConfig = JSON.parse(__firebase_config);
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'backnumber-preorder';
        
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        // 初始化狀態
        window.cart = [];
        window.selections = {};
        window.currentUser = null;

        // 商品清單 (你可以隨時修改這裡的價格和選項)
        const products = [
            { id: 'p1', name: 'Oversized Sweatshirt', price: 1850, colors: ['Black', 'Grey', 'Green'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p2', name: 'Zip-up Hoodie', price: 2100, colors: ['Black', 'Grey'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p3', name: 'Logo T-shirt (A款)', price: 1200, colors: ['White', 'Black'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p4', name: 'Photo T-shirt (B款)', price: 1350, colors: ['White'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p5', name: 'Canvas Tote Bag', price: 650, colors: ['Natural', 'Black'], sizes: null },
            { id: 'p6', name: 'Acrylic Keyring', price: 380, colors: null, sizes: null },
            { id: 'p7', name: 'Sticker Set', price: 250, colors: null, sizes: null }
        ];

        // 登入驗證
        signInAnonymously(auth).then(() => {
            onAuthStateChanged(auth, (user) => { window.currentUser = user; });
        });

        // 渲染畫面
        window.render = () => {
            const list = document.getElementById('product-list');
            list.innerHTML = products.map(p => `
                <div class="bg-white p-6 border border-slate-100 rounded-3xl mb-6 shadow-sm">
                    <div class="flex justify-between items-start mb-4">
                        <h3 class="text-lg font-black text-slate-800">${p.name}</h3>
                        <span class="text-indigo-600 font-black italic">$${p.price.toLocaleString()}</span>
                    </div>
                    
                    ${p.colors ? `
                        <div class="mb-4">
                            <p class="text-[10px] font-bold text-slate-400 mb-2 uppercase tracking-widest">Color</p>
                            <div class="flex flex-wrap gap-2">
                                ${p.colors.map(c => `<button onclick="setOpt('${p.id}','color','${c}',this)" class="px-4 py-2 text-xs rounded-xl border border-slate-100 bg-slate-50 text-slate-500 transition-all">${c}</button>`).join('')}
                            </div>
                        </div>
                    ` : ''}

                    ${p.sizes ? `
                        <div class="mb-4">
                            <p class="text-[10px] font-bold text-slate-400 mb-2 uppercase tracking-widest">Size</p>
                            <div class="flex flex-wrap gap-2">
                                ${p.sizes.map(s => `<button onclick="setOpt('${p.id}','size','${s}',this)" class="px-4 py-2 text-xs rounded-xl border border-slate-100 bg-slate-50 text-slate-500 transition-all">${s}</button>`).join('')}
                            </div>
                        </div>
                    ` : ''}

                    <button onclick="add('${p.id}')" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-black text-sm active:scale-95 transition-all shadow-lg shadow-slate-200">
                        加入購物車 ADD TO CART
                    </button>
                </div>
            `).join('');
        };

        window.setOpt = (pid, type, val, btn) => {
            if(!window.selections[pid]) window.selections[pid] = {};
            window.selections[pid][type] = val;
            btn.parentElement.querySelectorAll('button').forEach(b => b.classList.remove('bg-slate-900', 'text-white', 'border-slate-900'));
            btn.classList.add('bg-slate-900', 'text-white', 'border-slate-900');
        };

        window.add = (pid) => {
            const p = products.find(x => x.id === pid);
            const sel = window.selections[pid] || {};
            if(p.colors && !sel.color) return alert('請先選擇顏色');
            if(p.sizes && !sel.size) return alert('請先選擇尺寸');

            window.cart.push({ ...p, selColor: sel.color, selSize: sel.size, cartId: Date.now() + Math.random() });
            window.updateUI();
        };

        window.updateUI = () => {
            const drawer = document.getElementById('drawer');
            const itemsCont = document.getElementById('cart-items');
            const totalDisplay = document.getElementById('total');
            const countBadge = document.getElementById('count');
            const form = document.getElementById('checkout-form');

            if(window.cart.length > 0) {
                drawer.classList.remove('hidden');
                form.classList.remove('hidden');
            } else {
                drawer.classList.add('hidden');
                form.classList.add('hidden');
                drawer.classList.add('max-h-[70px]');
                drawer.classList.remove('max-h-[80vh]');
            }

            countBadge.innerText = window.cart.length;
            let total = 0;
            itemsCont.innerHTML = window.cart.map((item, i) => {
                total += item.price;
                return `
                    <div class="flex justify-between items-center py-4 border-b border-white/5">
                        <div>
                            <p class="font-bold text-sm">${item.name}</p>
                            <p class="text-[10px] text-white/40 font-bold uppercase">${item.selColor || ''} ${item.selSize ? '/ '+item.selSize : ''}</p>
                        </div>
                        <div class="flex items-center gap-4">
                            <span class="font-black italic text-sm">$${item.price}</span>
                            <button onclick="remove(${i})" class="text-white/20 hover:text-white">✕</button>
                        </div>
                    </div>
                `;
            }).join('');
            totalDisplay.innerText = total.toLocaleString();
        };

        window.remove = (i) => {
            window.cart.splice(i, 1);
            window.updateUI();
        };

        window.toggleDrawer = () => {
            const drawer = document.getElementById('drawer');
            drawer.classList.toggle('max-h-[70px]');
            drawer.classList.toggle('max-h-[80vh]');
        };

        window.submit = async (e) => {
            e.preventDefault();
            if(!window.currentUser) return alert('初始化中');
            const btn = e.target.querySelector('button');
            btn.disabled = true;
            btn.innerText = '處理中...';

            const payload = {
                customer: {
                    name: document.getElementById('n').value,
                    line: document.getElementById('l').value,
                    phone: document.getElementById('p').value,
                    ig: document.getElementById('i').value,
                    store: document.getElementById('s').value,
                    note: document.getElementById('m').value
                },
                items: window.cart,
                total: window.cart.reduce((s,i) => s+i.price, 0),
                time: new Date().toLocaleString()
            };

            try {
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'orders'), payload);
                alert('訂單已提交！');
                window.cart = [];
                window.updateUI();
                e.target.reset();
            } catch(err) {
                alert('失敗');
            } finally {
                btn.disabled = false;
                btn.innerText = '確認提交訂單';
            }
        };

        window.openAdmin = () => {
            const p = prompt('密碼');
            if(p === '1234') {
                document.getElementById('main-view').classList.add('hidden');
                document.getElementById('admin-view').classList.remove('hidden');
                const q = collection(db, 'artifacts', appId, 'public', 'data', 'orders');
                onSnapshot(q, (snap) => {
                    document.getElementById('orders-list').innerHTML = snap.docs.map(doc => {
                        const d = doc.data();
                        return `
                            <div class="bg-white p-4 rounded-2xl mb-4 border border-slate-100 shadow-sm">
                                <div class="flex justify-between font-black mb-1"><span>${d.customer.name}</span><span class="text-indigo-600">$${d.total}</span></div>
                                <div class="text-[10px] text-slate-400 mb-3">${d.customer.line} | ${d.customer.phone}</div>
                                <div class="text-[11px] bg-slate-50 p-3 rounded-xl">
                                    ${d.items.map(i => `• ${i.name} (${i.selColor||''} ${i.selSize||''})`).join('<br>')}
                                </div>
                            </div>
                        `;
                    }).join('');
                });
            }
        };

        window.onload = window.render;
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700;900&display=swap');
        body { font-family: 'Noto Sans TC', sans-serif; -webkit-tap-highlight-color: transparent; }
        .input-field { width: 100%; padding: 14px; border: 1px solid #f1f5f9; border-radius: 16px; background: #f8fafc; font-size: 14px; margin-bottom: 12px; }
    </style>
</head>
<body class="bg-[#fafafa]">

    <div id="main-view" class="max-w-[500px] mx-auto pb-32 px-4">
        <header class="py-12 text-center" onclick="openAdmin()">
            <h1 class="text-3xl font-black tracking-tighter italic">BackNumber 3rd</h1>
            <p class="text-[9px] text-slate-300 font-bold uppercase tracking-[0.5em] mt-2">Fansite Pre-order</p>
        </header>

        <div id="product-list"></div>

        <div id="checkout-form" class="hidden mt-12 pt-12 border-t border-slate-100">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.2em] mb-8">Shipping Info / 寄送資訊</h2>
            <form onsubmit="submit(event)">
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" id="n" placeholder="對帳姓名" class="input-field" required>
                    <input type="text" id="l" placeholder="LINE社群名稱" class="input-field" required>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" id="i" placeholder="Instagram ID" class="input-field" required>
                    <input type="tel" id="p" placeholder="手機號碼" class="input-field" required>
                </div>
                <input type="text" id="s" placeholder="7-11 店家名稱" class="input-field" required>
                <textarea id="m" placeholder="備註 (選填)" class="input-field h-28"></textarea>
                <button type="submit" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black shadow-xl shadow-indigo-100 mt-4">確認提交訂單</button>
            </form>
        </div>
    </div>

    <div id="admin-view" class="hidden max-w-[500px] mx-auto p-6">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-2xl font-black">訂單管理</h2>
            <button onclick="location.reload()" class="text-xs text-slate-400">登出</button>
        </div>
        <div id="orders-list"></div>
    </div>

    <!-- 可摺疊購物籃 -->
    <div id="drawer" class="hidden fixed bottom-4 left-4 right-4 max-w-[468px] mx-auto bg-slate-900 text-white rounded-[32px] shadow-2xl z-50 transition-all duration-500 overflow-hidden max-h-[70px]">
        <div class="h-[70px] flex justify-between items-center px-8 cursor-pointer" onclick="toggleDrawer()">
            <div class="flex items-center gap-3">
                <span id="count" class="bg-indigo-500 text-white text-[10px] font-black px-2.5 py-1 rounded-full">0</span>
                <span class="text-sm font-bold">查看明細</span>
            </div>
            <div class="flex items-center gap-4">
                <span class="text-xl font-black italic">$<span id="total">0</span></span>
                <span class="text-[10px] opacity-30">▲</span>
            </div>
        </div>
        <div class="px-8 pb-8 overflow-y-auto max-h-[calc(80vh-70px)]">
            <div id="cart-items"></div>
            <button onclick="toggleDrawer()" class="w-full bg-white/5 py-4 rounded-2xl mt-6 text-[10px] font-bold uppercase tracking-widest text-white/30">關閉明細</button>
        </div>
    </div>

</body>
</html>

