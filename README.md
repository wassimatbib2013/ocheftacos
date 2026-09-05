<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>O'Chef Tacos</title>
  <style>
    :root {
      --bg: #0d0f12;
      --card-bg: #16191e;
      --accent: #ff6b00;
      --accent-hover: #e05d00;
      --text: #f0f0f0;
      --text-dim: #a0a5b0;
      --border: #262a33;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
    body { background: var(--bg); color: var(--text); padding-bottom: 80px; }
    header { background: var(--card-bg); padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--border); sticky: top: 0; z-index: 100; }
    .logo { font-size: 1.4rem; font-weight: bold; color: var(--accent); }
    .cart-icon { position: relative; cursor: pointer; font-size: 1.5rem; }
    .badge { position: absolute; top: -8px; right: -8px; background: var(--accent); color: #fff; font-size: 0.75rem; padding: 2px 6px; border-radius: 50%; font-weight: bold; }
    .bump { animation: bump 0.3s ease-in-out; }
    @keyframes bump { 0% { transform: scale(1); } 50% { transform: scale(1.3); } 100% { transform: scale(1); } }
    
    .ticker { background: var(--accent); color: #fff; overflow: hidden; white-space: nowrap; padding: 8px 0; font-weight: bold; font-size: 0.9rem; }
    .ticker-track { display: inline-block; animation: marquee 25s linear infinite; }
    @keyframes marquee { 0% { transform: translateX(0); } 100% { transform: translateX(-50%); } }

    .container { max-width: 1100px; margin: 20px auto; padding: 0 15px; }
    .search-box { width: 100%; padding: 12px 15px; background: var(--card-bg); border: 1px solid var(--border); border-radius: 8px; color: #fff; margin-bottom: 20px; font-size: 1rem; }
    .tabs { display: flex; gap: 10px; overflow-x: auto; padding-bottom: 10px; margin-bottom: 20px; }
    .tab { background: var(--card-bg); border: 1px solid var(--border); color: var(--text-dim); padding: 8px 16px; border-radius: 20px; cursor: pointer; white-space: nowrap; }
    .tab.on { background: var(--accent); color: #fff; border-color: var(--accent); }

    .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 20px; }
    .card { background: var(--card-bg); border: 1px solid var(--border); border-radius: 12px; padding: 15px; display: flex; flex-direction: column; justify-content: space-between; position: relative; }
    .plate { height: 120px; background: hsl(var(--h, 42), 30%, 15%); border-radius: 8px; display: flex; align-items: center; justify-content: center; position: relative; margin-bottom: 12px; }
    .pimg { font-size: 3rem; }
    .bubble { position: absolute; bottom: 8px; right: 8px; background: rgba(0,0,0,0.7); padding: 4px 8px; border-radius: 6px; font-weight: bold; font-size: 0.9rem; }
    .chip-tag { position: absolute; top: 8px; left: 8px; background: var(--accent); color: #fff; font-size: 0.7rem; padding: 2px 6px; border-radius: 4px; text-transform: uppercase; }
    .cname { font-weight: bold; font-size: 1.1rem; margin-bottom: 5px; }
    .cdesc { font-size: 0.85rem; color: var(--text-dim); margin-bottom: 15px; flex-grow: 1; }
    .add { background: var(--accent); border: none; color: #fff; padding: 10px; border-radius: 6px; font-weight: bold; cursor: pointer; width: 100%; transition: 0.2s; }
    .add:hover { background: var(--accent-hover); }

    /* Drawer */
    .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.6); display: none; z-index: 200; }
    .overlay.show { display: block; }
    .drawer { position: fixed; top: 0; right: -400px; width: 100%; max-width: 380px; height: 100%; background: var(--card-bg); z-index: 201; transition: 0.3s ease; display: flex; flex-direction: column; padding: 20px; }
    .drawer.show { right: 0; }
    .d-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--border); padding-bottom: 15px; }
    .d-items { flex-grow: 1; overflow-y: auto; margin: 15px 0; }
    .ci { display: flex; align-items: center; gap: 10px; margin-bottom: 12px; background: var(--bg); padding: 10px; border-radius: 8px; }
    .ce { font-size: 1.5rem; }
    .cn { flex-grow: 1; }
    .cn small { display: block; color: var(--text-dim); }
    .cq { display: flex; align-items: center; gap: 8px; }
    .cq button { width: 24px; height: 24px; background: var(--border); border: none; color: #fff; border-radius: 4px; cursor: pointer; }
    .cl { font-weight: bold; width: 60px; text-align: right; }
    .d-footer { border-top: 1px solid var(--border); padding-top: 15px; }
    .wa-btn { background: #25D366; color: #fff; border: none; width: 100%; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 1rem; margin-top: 10px; }
    .toast { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); background: var(--accent); color: #fff; padding: 10px 20px; border-radius: 20px; display: none; z-index: 300; }
    .toast.show { display: block; }
    .empty-msg, .d-empty { text-align: center; color: var(--text-dim); padding: 20px; }
  </style>
</head>
<body>

  <header>
    <div class="logo">O'Chef Tacos</div>
    <div class="cart-icon" onclick="openCart()">
      🛒 <span class="badge" id="badge" style="display:none;">0</span>
    </div>
  </header>

  <div class="ticker">
    <div class="ticker-track" id="track"></div>
  </div>

  <div class="container" id="menu">
    <input type="text" class="search-box" id="q" placeholder="Rechercher un plat, un ingrédient...">
    <div class="tabs" id="tabs"></div>
    <div id="catScript" style="color: var(--accent); margin-bottom: 10px; font-weight: bold;"></div>
    <div class="grid" id="grid"></div>
  </div>

  <div class="overlay" id="overlay" onclick="closeCart()"></div>
  <div class="drawer" id="drawer">
    <div class="d-header">
      <h2>Mon Panier</h2>
      <button onclick="closeCart()" style="background:none; border:none; color:#fff; font-size:1.5rem; cursor:pointer;">&times;</button>
    </div>
    <div class="d-items" id="cartItems"></div>
    <div class="d-footer">
      <div style="display:flex; justify-content:space-between; font-weight:bold; font-size:1.2rem;">
        <span>Total:</span>
        <span id="total">0 DH</span>
      </div>
      <button class="wa-btn" onclick="orderWA()">Commander via WhatsApp</button>
    </div>
  </div>

  <div class="toast" id="toast"></div>

  <script>
    const CATS = [
      { key: 'all', label: 'Tout', h: 42, script: 'Découvrez notre carte complète' },
      { key: 'tacos', label: 'Tacos', h: 20, script: 'Tacos généreux avec sauce fromage maison' },
      { key: 'burgers', label: 'Burgers', h: 0, script: 'Burgers gourmets 100% pur bœuf' },
      { key: 'bowls', label: 'Bowls', h: 140, script: 'Bowls équilibrés et savoureux' },
      { key: 'sides', label: 'Accompagnements', h: 50, script: 'Frites, cheese fries et starters' },
      { key: 'drinks', label: 'Boissons', h: 200, script: 'Rafraîchissements et sodas' }
    ];

    const ITEMS = [
      { id: 't1', cat: 'tacos', n: 'Tacos M', d: '1 viande au choix, frites, sauce fromagère', p: 30, tag: 'Popular', icon: '🌮' },
      { id: 't2', cat: 'tacos', n: 'Tacos L', d: '2 viandes au choix, frites, sauce fromagère', p: 45, tag: 'Best Seller', icon: '🌮' },
      { id: 't3', cat: 'tacos', n: 'Tacos XL', d: '3 viandes au choix, frites, sauce fromagère', p: 60, icon: '🌮' },
      { id: 'b1', cat: 'burgers', n: 'Cheeseburger', d: 'Steak 100g, cheddar, pickles, sauce chef', p: 35, icon: '🍔' },
      { id: 'b2', cat: 'burgers', n: 'Double Cheese', d: '2x Steak 100g, 2x cheddar, sauce chef', p: 50, tag: 'Gourmet', icon: '🍔' },
      { id: 'bw1', cat: 'bowls', n: 'Chicken Bowl', d: 'Poulet mariné, riz, crudités, sauce au choix', p: 40, icon: '🥗' },
      { id: 's1', cat: 'sides', n: 'Frites Simple', d: 'Frites croustillantes dorées', p: 15, icon: '🍟' },
      { id: 's2', cat: 'sides', n: 'Cheese Fries', d: 'Frites nappées de sauce fromagère chaudes', p: 25, tag: 'Hot', icon: '🍟' },
      { id: 'd1', cat: 'drinks', n: 'Coca-Cola 33cl', d: 'Boisson gazeuse rafraîchissante', p: 10, icon: '🥤' }
    ];

    let cart = JSON.parse(localStorage.getItem('chef_cart') || '{}');

    function save() {
      localStorage.setItem('chef_cart', JSON.stringify(cart));
    }

    function count() {
      return Object.values(cart).reduce((a, b) => a + b, 0);
    }

    function total() {
      return Object.keys(cart).reduce((sum, id) => {
        const item = ITEMS.find(i => i.id === id);
        return sum + (item ? item.p * cart[id] : 0);
      }, 0);
    }

    function itemOf(id) {
      return ITEMS.find(i => i.id === id);
    }

    function artOf(item) {
      return item.icon || '🍽️';
    }

    function add(id) {
      cart[id] = (cart[id] || 0) + 1;
      save();
      refreshCart(true);
      const item = itemOf(id);
      if (item) toast(`${item.n} ajouté au panier !`);
    }

    function setQty(id, q) {
      if (q <= 0) {
        delete cart[id];
      } else {
        cart[id] = q;
      }
      save();
      refreshCart();
    }

    function refreshCart(bump = false) {
      const cnt = count();
      const tot = total();
      
      const badge = document.getElementById('badge');
      if (badge) {
        badge.textContent = cnt;
        badge.style.display = cnt > 0 ? 'grid' : 'none';
        if (bump) {
          badge.classList.remove('bump');
          void badge.offsetWidth;
          badge.classList.add('bump');
        }
      }

      const totEl = document.getElementById('total');
      if (totEl) totEl.textContent = `${tot} DH`;

      const container = document.getElementById('cartItems');
      if (!container) return;

      const keys = Object.keys(cart);
      if (keys.length === 0) {
        container.innerHTML = '<div class="d-empty">Ton panier est vide pour le moment.</div>';
        return;
      }

      container.innerHTML = keys.map(id => {
        const item = itemOf(id);
        if (!item) return '';
        const q = cart[id];
        return `
          <div class="ci">
            <div class="ce">${artOf(item)}</div>
            <div class="cn">
              <b>${item.n}</b>
              <small>${item.p} DH</small>
            </div>
            <div class="cq">
              <button onclick="setQty('${id}', ${q - 1})">-</button>
              <b>${q}</b>
              <button onclick="setQty('${id}', ${q + 1})">+</button>
            </div>
            <div class="cl">${item.p * q} DH</div>
          </div>
        `;
      }).join('');
    }

    function openCart() {
      document.getElementById('overlay')?.classList.add('show');
      document.getElementById('drawer')?.classList.add('show');
    }

    function closeCart() {
      document.getElementById('overlay')?.classList.remove('show');
      document.getElementById('drawer')?.classList.remove('show');
    }

    function toast(msg) {
      const t = document.getElementById('toast');
      if (!t) return;
      t.textContent = msg;
      t.classList.add('show');
      setTimeout(() => t.classList.remove('show'), 2200);
    }

    function orderWA() {
      const keys = Object.keys(cart);
      if (keys.length === 0) return alert('Ton panier est vide !');
      let msg = "Bonjour O'Chef Tacos, je souhaite commander :\n";
      keys.forEach(id => {
        const item = itemOf(id);
        msg += `- ${cart[id]}x ${item.n} (${item.p * cart[id]} DH)\n`;
      });
      msg += `\nTotal : ${total()} DH`;
      window.open(`https://wa.me/212617147845?text=${encodeURIComponent(msg)}`, '_blank');
    }

    let currentCat = 'all';

    function renderTabs() {
      const container = document.getElementById('tabs');
      if (!container) return;
      container.innerHTML = CATS.map(c => `
        <button class="tab ${c.key === currentCat ? 'on' : ''}" onclick="selectCat('${c.key}')">${c.label}</button>
      `).join('');
    }

    function selectCat(catKey) {
      currentCat = catKey;
      renderTabs();
      renderGrid();
    }

    function renderGrid(forcedCat) {
      if (forcedCat) currentCat = forcedCat;
      renderTabs();
      
      const grid = document.getElementById('grid');
      const q = (document.getElementById('q')?.value || '').toLowerCase().trim();
      const catObj = CATS.find(c => c.key === currentCat);
      
      const scriptEl = document.getElementById('catScript');
      if (scriptEl && catObj) scriptEl.textContent = catObj.script;

      let filtered = ITEMS.filter(item => {
        const matchCat = currentCat === 'all' || item.cat === currentCat;
        const matchQuery = !q || item.n.toLowerCase().includes(q) || item.d.toLowerCase().includes(q);
        return matchCat && matchQuery;
      });

      if (!grid) return;
      if (filtered.length === 0) {
        grid.innerHTML = '<div class="empty-msg">Aucun plat trouvé.</div>';
        return;
      }

      grid.innerHTML = filtered.map(item => {
        const catInfo = CATS.find(c => c.key === item.cat);
        const hue = catInfo?.h || 42;
        return `
          <div class="card">
            <div class="plate" style="--h: ${hue}">
              <div class="pimg">${artOf(item)}</div>
              <div class="bubble">${item.p} <i>DH</i></div>
              ${item.tag ? `<span class="chip-tag">${item.tag}</span>` : ''}
            </div>
            <div class="cname">${item.n}</div>
            <div class="cdesc">${item.d}</div>
            <button class="add" onclick="add('${item.id}')">+ Ajouter</button>
          </div>
        `;
      }).join('');
    }

    document.addEventListener('DOMContentLoaded', () => {
      const track = document.getElementById('track');
      if (track) {
        const text = "TACOS SIGNATURE ★ BURGERS ★ BOWLS ★ CHEESE FRIES ★ WRAPS ★ SHAWARMA ★ DESSERTS ★ ";
        track.innerHTML = `<span>${text.repeat(4)}</span><span>${text.repeat(4)}</span>`;
      }

      document.getElementById('q')?.addEventListener('input', () => renderGrid());

      renderGrid();
      refreshCart();
    });
  </script>
</body>
</html>
