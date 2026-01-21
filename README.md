 
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BackNumber 3rd Pre-order</title>
    <!-- 引入 Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 引入 Firebase SDK -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-firestore.js";

        // Firebase 配置
        const firebaseConfig = JSON.parse(__firebase_config);
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'backnumber-order-system';
        
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        // 全域狀態管理
        window.state = {
            cart: [],
            selections: {},
            currentUser: null,
            isAdmin: false
        };

        // 商品資料
        const products = [
            { id: 'p1', name: 'Oversized Sweatshirt', price: 1850, colors: ['黑色 Black', '灰色 Grey', '綠色 Green'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p2', name: 'Zip-up Hoodie', price: 2100, colors: ['黑色 Black', '灰色 Grey'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p3', name: 'Logo T-shirt (A款)', price: 1200, colors: ['白色 White', '黑色 Black'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p4', name: 'Photo T-shirt (B款)', price: 1350, colors: ['白色 White'], sizes: ['S', 'M', 'L', 'XL'] },
            { id: 'p5', name: 'Canvas Tote Bag', price: 650, colors: ['原色 Natural', '黑色 Black'], sizes: null },
            { id: 'p6', name: 'Acrylic Keyring', price: 380, colors: null, sizes: null }
        ];

        // 登入驗證
        signInAnonymously(auth).then(() => {
            onAuthStateChanged(auth, (user) => { window.state.currentUser = user; });
        });

        // 渲染商品列表
        window.renderProducts = () => {
            const container = document.getElementById('product-list');
            container.innerHTML = products.map(p => `
                <div class="bg-white p-6 rounded-3xl border border-slate-100 mb-6 shadow-sm">
                    <div class="flex justify-between items-start mb-4">
                        <h3 class="text-lg font-bold text-slate-800">${p.name}</h3>
                        <span class="text-indigo-600 font-black italic">$${p.price.toLocaleString()}</span>
                    </div>
                    
                    ${p.colors ? `
                        <div class="mb-4">
                            <p class="text-[10px] font-bold text-slate-400 mb-2 uppercase tracking-widest">選擇顏色 Color</p>
                            <div class="flex flex-wrap gap-2">
                                ${p.colors.map(c => `<button onclick="setOption('${p.id}','color','${c}',this)" class="px-4 py-2 text-xs rounded-xl border border-slate-100 bg-slate-50 text-slate-500 hover:bg-slate-100 transition-all">${c}</button>`).join('')}
                            </div>
                        </div>
                    ` : ''}

                    ${p.sizes ? `
                        <div class="mb-4">
                            <p class="text-[10px] font-bold text-slate-400 mb-2 uppercase tracking-widest">選擇尺寸 Size</p>
                            <div class="flex flex-wrap gap-2">
                                ${p.sizes.map(s => `<button onclick="setOption('${p.id}','size','${s}',this)" class="px-4 py-2 text-xs rounded-xl border border-slate-100 bg-slate-50 text-slate-500 hover:bg-slate-100 transition-all">${s}</button>`).join('')}
                            </div>
                        </div>
                    ` : ''}

                    <button onclick="addToCart('${p.id}')" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-bold text-sm active:scale-95 transition-all shadow-lg shadow-slate-100">
                        加入購物車
                    </button>
                </div>
            `).join('');
        };

        // 處理規格選擇
        window.setOption = (pid, type, val, btn) => {
            if(!window.state.selections[pid]) window.state.selections[pid] = {};
            window.state.selections[pid][type] = val;
            
            // UI 反饋
            const buttons = btn.parentElement.querySelectorAll('button');
            buttons.forEach(b => {
                b.classList.remove('bg-slate-900', 'text-white', 'border-slate-900');
                b.classList.add('bg-slate-50', 'text-slate-500', 'border-slate-100');
            });
            btn.classList.add('bg-slate-900', 'text-white', 'border-slate-900');
            btn.classList.remove('bg-slate-50', 'text-slate-500', 'border-slate-100');
        };

        // 加入購物車功能
        window.addToCart = (pid) => {
            const product = products.find(p => p.id === pid);
            const sel = window.state.selections[pid] || {};
            
            if(product.colors && !sel.color) return alert('請先選擇顏色');
            if(product.sizes && !sel.size) return alert('請先選擇尺寸');

            const cartItem = {
                ...product,
                selectedColor: sel.color || '',
                selectedSize: sel.size || '',
                uniqueId: Date.now() + Math.random()
            };

            window.state.cart.push(cartItem);
            window.updateUI();
        };

        // 更新購物車 UI
        window.updateUI = () => {
            const drawer = document.getElementById('drawer');
            const cartList = document.getElementById('cart-items');
            const totalVal = document.getElementById('total-value');
            const badge = document.getElementById('cart-badge');
            const checkoutForm = document.getElementById('checkout-form');

            if(window.state.cart.length > 0) {
                drawer.classList.remove('hidden');
                checkoutForm.classList.remove('hidden');
            } else {
                drawer.classList.add('hidden');
                checkoutForm.classList.add('hidden');
                drawer.classList.add('max-h-[70px]');
                drawer.classList.remove('max-h-[80vh]');
            }

            badge.innerText = window.state.cart.length;
            let total = 0;
            cartList.innerHTML = window.state.cart.map((item, index) => {
                total += item.price;
                return `
                    <div class="flex justify-between items-center py-4 border-b border-white/10">
                        <div>
                            <p class="font-bold text-sm">${item.name}</p>
                            <p class="text-[10px] text-white/40 font-bold uppercase tracking-wider">
                                ${item.selectedColor} ${item.selectedSize ? '/ ' + item.selectedSize : ''}
                            </p>
                        </div>
                        <div class="flex items-center gap-4">
                            <span class="font-black italic text-sm">$${item.price.toLocaleString()}</span>
                            <button onclick="removeFromCart(${index})" class="text-white/20 hover:text-white">✕</button>
                        </div>
                    </div>
                `;
            }).join('');
            totalVal.innerText = total.toLocaleString();
        };

        window.removeFromCart = (index) => {
            window.state.cart.splice(index, 1);
            window.updateUI();
        };

        window.toggleDrawer = () => {
            const drawer = document.getElementById('drawer');
            drawer.classList.toggle('max-h-[70px]');
            drawer.classList.toggle('max-h-[80vh]');
        };

        // 提交訂單
        window.submitOrder = async (e) => {
            e.preventDefault();
            if(!window.state.currentUser) return alert('系統初始化中，請稍候');
            
            const btn = e.target.querySelector('button');
            btn.disabled = true;
            btn.innerText = '正在上傳訂單...';

            const orderData = {
                customer: {
                    name: document.getElementById('name').value,
                    line: document.getElementById('line').value,
                    phone: document.getElementById('phone').value,
                    ig: document.getElementById('ig').value,
                    store: document.getElementById('store').value,
                    note: document.getElementById('note').value
                },
                items: window.state.cart,
                total: window.state.cart.reduce((sum, item) => sum + item.price, 0),
                createdAt: new Date().toLocaleString(),
                timestamp: new Date().getTime()
            };

            try {
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'orders'), orderData);
                alert('🎉 訂單提交成功！請截圖並記得匯款通知。');
                window.state.cart = [];
                window.updateUI();
                e.target.reset();
            } catch (err) {
                alert('提交失敗，請檢查網路連線');
            } finally {
                btn.disabled = false;
                btn.innerText = '提交訂購單';
            }
        };

        // 管理員後台功能
        window.openAdmin = () => {
            const pass = prompt('請輸入管理密碼');
            if(pass === '1234') {
                document.getElementById('user-section').classList.add('hidden');
                document.getElementById('admin-section').classList.remove('hidden');
                
                const q = collection(db, 'artifacts', appId, 'public', 'data', 'orders');
                onSnapshot(q, (snapshot) => {
                    const adminList = document.getElementById('admin-order-list');
                    adminList.innerHTML = snapshot.docs.map(doc => {
                        const d = doc.data();
                        return `
                            <div class="bg-white p-5 rounded-3xl border border-slate-100 shadow-sm mb-4">
                                <div class="flex justify-between font-bold text-lg mb-2">
                                    <span>${d.customer.name}</span>
                                    <span class="text-indigo-600">$${d.total.toLocaleString()}</span>
                                </div>
                                <div class="text-[11px] text-slate-400 mb-3 space-y-1">
                                    <p>LINE: ${d.customer.line} | IG: ${d.customer.ig}</p>
                                    <p>電話: ${d.customer.phone} | 店家: ${d.customer.store}</p>
                                    <p>時間: ${d.createdAt}</p>
                                </div>
                                <div class="bg-slate-50 p-3 rounded-2xl text-[11px] text-slate-600 space-y-1">
                                    ${d.items.map(i => `• ${i.name} (${i.selectedColor} ${i.selectedSize})`).join('<br>')}
                                </div>
                                ${d.customer.note ? `<p class="mt-2 text-[11px] text-slate-400 italic">備註: ${d.customer.note}</p>` : ''}
                            </div>
                        `;
                    }).join('');
                });
            }
        };

        window.onload = window.renderProducts;
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&display=swap');
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #fbfbfb; -webkit-tap-highlight-color: transparent; }
        .input-style { width: 100%; padding: 15px; background: #f3f4f6; border-radius: 18px; font-size: 14px; margin-bottom: 12px; border: 1px solid transparent; transition: all 0.2s; }
        .input-style:focus { background: white; border-color: #6366f1; outline: none; box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.1); }
    </style>
</head>
<body class="pb-32">

    <!-- 用戶購物介面 -->
    <div id="user-section" class="max-w-[500px] mx-auto px-5">
        <header class="py-12 text-center" onclick="openAdmin()">
            <h1 class="text-3xl font-black italic tracking-tighter">BackNumber 3rd</h1>
            <p class="text-[10px] text-slate-300 font-bold uppercase tracking-[0.5em] mt-2">Fansite Management</p>
        </header>

        <!-- 購物說明 -->
        <div class="bg-indigo-600 text-white p-6 rounded-[32px] mb-8 shadow-xl shadow-indigo-100">
            <h4 class="font-bold text-sm mb-2 italic tracking-wider">PRE-ORDER NOTICE</h4>
            <div class="text-[12px] opacity-90 leading-relaxed space-y-1">
                <p>• 點擊商品規格後按下「加入購物車」。</p>
                <p>• 可在下方展開明細確認目前選購總額。</p>
                <p>• 匯款帳號：國泰世華 (013) 699510910862</p>
            </div>
        </div>

        <!-- 商品清單 -->
        <div id="product-list"></div>

        <!-- 結帳表單 -->
        <div id="checkout-form" class="hidden mt-12 pt-12 border-t border-slate-100">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.3em] mb-8 text-center">寄送資訊 / Checkout Info</h2>
            <form onsubmit="submitOrder(event)">
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" id="name" placeholder="對帳姓名" class="input-style" required>
                    <input type="text" id="line" placeholder="LINE社群名稱" class="input-style" required>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <input type="text" id="ig" placeholder="Instagram ID" class="input-style" required>
                    <input type="tel" id="phone" placeholder="手機號碼" class="input-style" required>
                </div>
                <input type="text" id="store" placeholder="7-11 取貨店家名稱" class="input-style" required>
                <textarea id="note" placeholder="備註或特殊要求 (選填)" class="input-style h-28 pt-4"></textarea>
                <button type="submit" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black shadow-xl shadow-indigo-100 mt-4 active:scale-95 transition-all">
                    提交訂購單
                </button>
            </form>
        </div>
    </div>

    <!-- 管理員後台介面 -->
    <div id="admin-section" class="hidden max-w-[500px] mx-auto p-6">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-2xl font-black">表單回收結果</h2>
            <button onclick="location.reload()" class="text-xs text-slate-400 underline">登出後台</button>
        </div>
        <div id="admin-order-list"></div>
    </div>

    <!-- 摺疊式底部明細 -->
    <div id="drawer" class="hidden fixed bottom-6 left-5 right-5 max-w-[460px] mx-auto bg-slate-900 text-white rounded-[32px] shadow-2xl z-50 transition-all duration-500 overflow-hidden max-h-[70px]">
        <!-- 標題列 (縮小時顯示) -->
        <div class="h-[70px] flex justify-between items-center px-8 cursor-pointer" onclick="toggleDrawer()">
            <div class="flex items-center gap-3">
                <span id="cart-badge" class="bg-indigo-500 text-white text-[10px] font-black px-2.5 py-1 rounded-full">0</span>
                <span class="text-sm font-bold tracking-tight">查看 / 調整選購明細</span>
            </div>
            <div class="flex items-center gap-4">
                <span class="text-xl font-black italic">$<span id="total-value">0</span></span>
                <span class="text-[10px] opacity-30">▲</span>
            </div>
        </div>
        
        <!-- 展開後的列表 -->
        <div class="px-8 pb-8 overflow-y-auto max-h-[calc(80vh-70px)]">
            <div id="cart-items" class="pt-2"></div>
            <button onclick="toggleDrawer()" class="w-full bg-white/10 py-4 rounded-2xl mt-6 text-[10px] font-black uppercase tracking-widest text-white/40">
                收起明細
            </button>
        </div>
    </div>

</body>
</html>

