import React, { useState, useEffect } from 'react';
import { initializeApp } from 'firebase/app';
import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from 'firebase/auth';
import { getFirestore, collection, addDoc, onSnapshot, query, doc } from 'firebase/firestore';

// --- Firebase 配置 ---
// 注意：這裡的配置在運行時會由環境自動注入
const firebaseConfig = JSON.parse(__firebase_config);
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const appId = typeof __app_id !== 'undefined' ? __app_id : 'backnumber-order-app';

const PRODUCTS = [
  { id: 'p1', name: 'Oversized Sweatshirt', price: 1850, colors: ['Black', 'Grey', 'Green'], sizes: ['S', 'M', 'L', 'XL'] },
  { id: 'p2', name: 'Zip-up Hoodie', price: 2100, colors: ['Black', 'Grey'], sizes: ['S', 'M', 'L', 'XL'] },
  { id: 'p3', name: 'Logo T-shirt (A款)', price: 1200, colors: ['White', 'Black'], sizes: ['S', 'M', 'L', 'XL'] },
  { id: 'p4', name: 'Photo T-shirt (B款)', price: 1350, colors: ['White'], sizes: ['S', 'M', 'L', 'XL'] },
  { id: 'p5', name: 'Canvas Tote Bag', price: 650, colors: ['Natural', 'Black'], sizes: null },
  { id: 'p6', name: 'Acrylic Keyring', price: 380, colors: null, sizes: null }
];

export default function App() {
  const [user, setUser] = useState(null);
  const [cart, setCart] = useState([]);
  const [isExpanded, setIsExpanded] = useState(false);
  const [formData, setFormData] = useState({ name: '', line: '', ig: '', phone: '', email: '', store: '', note: '' });
  const [selectedOptions, setSelectedOptions] = useState({});
  const [orders, setOrders] = useState([]); // 管理員查看訂單用
  const [isAdmin, setIsAdmin] = useState(false);
  const [isSubmitting, setIsSubmitting] = useState(false);

  // 1. 初始化驗證
  useEffect(() => {
    const initAuth = async () => {
      if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
        await signInWithCustomToken(auth, __initial_auth_token);
      } else {
        await signInAnonymously(auth);
      }
    };
    initAuth();
    const unsubscribe = onAuthStateChanged(auth, setUser);
    return () => unsubscribe();
  }, []);

  // 2. 只有管理員模式才監聽訂單
  useEffect(() => {
    if (!user || !isAdmin) return;
    const q = collection(db, 'artifacts', appId, 'public', 'data', 'orders');
    const unsubscribe = onSnapshot(q, (snapshot) => {
      const docs = snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
      setOrders(docs);
    }, (error) => console.error("Firestore Error:", error));
    return () => unsubscribe();
  }, [user, isAdmin]);

  const addToCart = (product) => {
    const options = selectedOptions[product.id] || {};
    if (product.colors && !options.color) return alert('請選擇顏色');
    if (product.sizes && !options.size) return alert('請選擇尺寸');

    const newItem = {
      ...product,
      selectedColor: options.color,
      selectedSize: options.size,
      cartId: Date.now() + Math.random()
    };
    setCart([...cart, newItem]);
  };

  const removeFromCart = (cartId) => {
    setCart(cart.filter(item => item.cartId !== cartId));
  };

  const total = cart.reduce((sum, item) => sum + item.price, 0);

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (cart.length === 0) return alert('購物車是空的');
    if (!user) return alert('驗證中，請稍後');

    setIsSubmitting(true);
    try {
      await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'orders'), {
        customer: formData,
        items: cart,
        totalAmount: total,
        timestamp: new Date().toISOString(),
        userId: user.uid
      });
      alert('🎉 訂單提交成功！請記得匯款後通知我們。');
      setCart([]);
      setFormData({ name: '', line: '', ig: '', phone: '', email: '', store: '', note: '' });
    } catch (err) {
      alert('提交失敗，請檢查網路。');
    } finally {
      setIsSubmitting(false);
    }
  };

  if (isAdmin) {
    return (
      <div className="p-6 max-w-4xl mx-auto bg-slate-50 min-h-screen">
        <div className="flex justify-between items-center mb-6">
          <h1 className="text-2xl font-black">管理員後台 - 訂單列表</h1>
          <button onClick={() => setIsAdmin(false)} className="text-sm text-blue-500">回到首頁</button>
        </div>
        <div className="space-y-4">
          {orders.map(order => (
            <div key={order.id} className="bg-white p-4 rounded-xl shadow-sm border border-slate-200">
              <div className="flex justify-between mb-2">
                <span className="font-bold text-lg">{order.customer.name}</span>
                <span className="text-indigo-600 font-black">${order.totalAmount}</span>
              </div>
              <div className="text-sm text-slate-500 mb-2">
                LINE: {order.customer.line} | IG: {order.customer.ig} | 店家: {order.customer.store}
              </div>
              <div className="bg-slate-50 p-2 rounded text-xs space-y-1">
                {order.items.map((item, i) => (
                  <div key={i}>• {item.name} ({item.selectedColor || ''} {item.selectedSize || ''})</div>
                ))}
              </div>
              <div className="text-[10px] text-slate-300 mt-2">{order.timestamp}</div>
            </div>
          ))}
        </div>
      </div>
    );
  }

  return (
    <div className="max-w-[600px] mx-auto bg-white min-h-screen pb-40">
      {/* Header */}
      <div className="p-8 text-center" onClick={() => {
          const pass = prompt("請輸入管理密碼");
          if(pass === "1234") setIsAdmin(true);
      }}>
        <h1 className="text-3xl font-black tracking-tighter italic">BackNumber Pre-order</h1>
        <p className="text-[10px] text-slate-400 font-bold uppercase tracking-[0.4em] mt-2">Lottie Fansite</p>
      </div>

      <div className="px-4">
        {/* 商品列表 */}
        {PRODUCTS.map(p => (
          <div key={p.id} className="p-4 border border-slate-100 rounded-2xl mb-4 shadow-sm">
            <h3 className="font-black text-slate-800">{p.name}</h3>
            <p className="text-indigo-600 font-bold text-sm mb-4">${p.price.toLocaleString()}</p>
            
            <div className="flex flex-wrap gap-2 mb-3">
              {p.colors?.map(c => (
                <button 
                  key={c}
                  onClick={() => setSelectedOptions({...selectedOptions, [p.id]: {...(selectedOptions[p.id]||{}), color: c}})}
                  className={`px-3 py-1.5 text-xs rounded-lg border ${selectedOptions[p.id]?.color === c ? 'bg-slate-900 text-white' : 'bg-slate-50 text-slate-500'}`}
                >
                  {c}
                </button>
              ))}
            </div>
            <div className="flex flex-wrap gap-2 mb-4">
              {p.sizes?.map(s => (
                <button 
                  key={s}
                  onClick={() => setSelectedOptions({...selectedOptions, [p.id]: {...(selectedOptions[p.id]||{}), size: s}})}
                  className={`px-3 py-1.5 text-xs rounded-lg border ${selectedOptions[p.id]?.size === s ? 'bg-slate-900 text-white' : 'bg-slate-50 text-slate-500'}`}
                >
                  {s}
                </button>
              ))}
            </div>
            <button 
              onClick={() => addToCart(p)}
              className="w-full bg-indigo-600 text-white py-2.5 rounded-xl font-bold text-sm active:scale-95 transition-transform"
            >
              加入購物車
            </button>
          </div>
        ))}

        {/* 結帳表單 */}
        {cart.length > 0 && (
          <div className="mt-12 pt-8 border-t">
            <h2 className="text-xs font-black text-slate-400 uppercase tracking-widest mb-6">Checkout / 寄送資訊</h2>
            <form onSubmit={handleSubmit} className="space-y-3">
              <div className="grid grid-cols-2 gap-3">
                <input type="text" placeholder="對帳姓名" className="w-full p-3 border rounded-xl text-sm" required value={formData.name} onChange={e => setFormData({...formData, name: e.target.value})} />
                <input type="text" placeholder="LINE社群名稱" className="w-full p-3 border rounded-xl text-sm" required value={formData.line} onChange={e => setFormData({...formData, line: e.target.value})} />
              </div>
              <input type="text" placeholder="7-11 店家名稱" className="w-full p-3 border rounded-xl text-sm" required value={formData.store} onChange={e => setFormData({...formData, store: e.target.value})} />
              <textarea placeholder="備註 (非必填)" className="w-full p-3 border rounded-xl text-sm h-24" value={formData.note} onChange={e => setFormData({...formData, note: e.target.value})} />
              <button disabled={isSubmitting} type="submit" className="w-full bg-slate-900 text-white p-4 rounded-xl font-bold shadow-lg disabled:opacity-50">
                {isSubmitting ? '提交中...' : '提交訂單'}
              </button>
            </form>
          </div>
        )}
      </div>

      {/* 摺疊購物籃 */}
      {cart.length > 0 && (
        <div className={`fixed bottom-0 left-0 right-0 bg-slate-900 text-white transition-all duration-300 rounded-t-2xl shadow-2xl z-50 ${isExpanded ? 'max-h-[80vh]' : 'max-h-[70px]'}`}>
          <div className="h-[70px] flex justify-between items-center px-6 cursor-pointer border-b border-white/10" onClick={() => setIsExpanded(!isExpanded)}>
            <div className="flex items-center gap-2">
              <span className="bg-indigo-500 text-white text-[10px] font-bold px-2 py-0.5 rounded-full">{cart.length}</span>
              <span className="text-sm font-bold">查看/調整選購明細</span>
            </div>
            <div className="flex items-center gap-3">
              <span className="text-xl font-black italic">${total.toLocaleString()}</span>
              <span>{isExpanded ? '▼' : '▲'}</span>
            </div>
          </div>
          <div className="p-6 overflow-y-auto max-h-[calc(80vh-70px)]">
            {cart.map((item) => (
              <div key={item.cartId} className="flex justify-between items-center py-3 border-b border-white/5">
                <div>
                  <p className="font-bold text-sm">{item.name}</p>
                  <p className="text-[10px] text-white/40 uppercase font-bold">{item.selectedColor} {item.selectedSize ? '/ ' + item.selectedSize : ''}</p>
                </div>
                <div className="flex items-center gap-4">
                  <span className="font-black italic">${item.price}</span>
                  <button onClick={() => removeFromCart(item.cartId)} className="text-white/30">✕</button>
                </div>
              </div>
            ))}
            <button onClick={() => setIsExpanded(false)} className="w-full bg-white/10 py-3 rounded-xl mt-4 text-xs font-bold">繼續選購</button>
          </div>
        </div>
      )}
    </div>
  );
}

