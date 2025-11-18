# App-mobile-Cabrel-
<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>CABREL — Messagerie & Blogs Premium</title>
  <style>
    /* ====== CSS simple et moderne pour CABREL ====== */
    :root{
      --bg:#0f1724; --card:#0b1220; --accent:#f97316; --muted:#93c5fd;
      --glass: rgba(255,255,255,0.03);
      font-family: Inter, system-ui, Arial, sans-serif;
    }
    *{box-sizing:border-box}
    body{
      margin:0; min-height:100vh; display:flex; background:
      linear-gradient(180deg,#071022 0%, #071226 60%);
      color:#e6eef8; font-size:15px;
    }
    .app{
      margin:auto; width:1100px; max-width:96%; display:grid;
      grid-template-columns:260px 1fr 360px; gap:18px; padding:20px;
    }
    .panel{background:var(--card); border-radius:12px; padding:14px;}
    /* Left: contacts & menu */
    .sidebar{display:flex;flex-direction:column; gap:12px; height:80vh; overflow:auto}
    .brand{display:flex;align-items:center;gap:10px;font-weight:700}
    .search{display:flex;padding:8px;background:var(--glass);border-radius:10px}
    .contact{padding:10px;border-radius:10px;display:flex;align-items:center;gap:10px;cursor:pointer}
    .contact:hover{background:rgba(255,255,255,0.02)}
    .avatar{width:44px;height:44px;border-radius:10px;background:linear-gradient(135deg,#334155,#1e293b);display:flex;align-items:center;justify-content:center}
    .unread{background:#f97316;color:#07203a;padding:4px 8px;border-radius:999px;font-weight:700}
    /* Center: chat and blog feed toggle */
    .center{display:flex;flex-direction:column;height:80vh;overflow:hidden}
    .topbar{display:flex;align-items:center;justify-content:space-between;padding:8px 0}
    .chat-area{flex:1;overflow:auto;padding:12px;background:linear-gradient(180deg, rgba(255,255,255,0.01), transparent);border-radius:10px}
    .bubble{max-width:70%;padding:10px 12px;border-radius:12px;margin-bottom:10px}
    .me{background:linear-gradient(90deg,#0ea5a9,#06b6d4);color:#021018;margin-left:auto;border-bottom-right-radius:4px}
    .them{background:rgba(255,255,255,0.03);color:#dbeafe;border-bottom-left-radius:4px}
    .composer{display:flex;gap:8px;padding-top:10px}
    .composer input{flex:1;padding:10px;border-radius:10px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit}
    .btn{background:var(--accent);color:#04121a;padding:10px 14px;border-radius:10px;border:none;cursor:pointer;font-weight:700}
    .btn.secondary{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted)}
    /* Right: blog list & premium area */
    .right{display:flex;flex-direction:column;gap:12px;height:80vh;overflow:auto}
    .blog{padding:12px;border-radius:10px;background:linear-gradient(180deg, rgba(255,255,255,0.01), transparent);margin-bottom:8px}
    .locked{opacity:0.95;border-left:4px solid #f97316;padding-left:10px}
    .pay-methods{display:flex;gap:8px;flex-wrap:wrap}
    .pay-card{padding:10px;border-radius:8px;background:rgba(255,255,255,0.02);cursor:pointer}
    /* modal */
    .modal{position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(2,6,23,0.6);z-index:60}
    .modal.open{display:flex}
    .modal .box{width:520px;max-width:96%;background:#071226;padding:18px;border-radius:12px}
    .small{font-size:13px;color:#9fb4d9}
    footer{margin-top:10px;color:#7f9bb9;font-size:13px;text-align:center}
    @media (max-width:1000px){
      .app{grid-template-columns:1fr;grid-auto-rows:auto}
      .right{order:3}
    }
  </style>
</head>
<body>
  <div class="app" id="app">
    <!-- LEFT: Contacts + Menu -->
    <div class="panel sidebar">
      <div class="brand">
        <div style="width:44px;height:44px;border-radius:10px;background:linear-gradient(90deg,#f97316,#fb923c);display:flex;align-items:center;justify-content:center;font-weight:900;color:#021018">CB</div>
        CABREL
      </div>
      <div class="search"><input id="searchContacts" placeholder="Rechercher contact..." style="width:100%;background:transparent;border:none;color:inherit"></div>
      <div>
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
          <strong>Contacts</strong><button class="btn secondary" id="newChatBtn">Nouveau</button>
        </div>
        <div id="contactsList">
          <!-- contacts injected by JS -->
        </div>
      </div>
      <div style="margin-top:auto;color:#9fb4d9" class="small">
        <div>Mode: Démo front-end</div>
        <div>Backend requis pour paiements réels</div>
      </div>
    </div>

    <!-- CENTER: Chat / Feed -->
    <div class="panel center">
      <div class="topbar">
        <div>
          <strong id="chatTitle">Sélectionnez une conversation</strong>
          <div class="small" id="chatSub">CABREL — Messagerie sécurisée</div>
        </div>
        <div style="display:flex;gap:8px;align-items:center">
          <button class="btn secondary" id="toggleFeed">Voir Blogs</button>
          <button class="btn" id="composeBlogBtn">Publier Blog</button>
        </div>
      </div>

      <!-- Chat area -->
      <div class="chat-area" id="chatArea">
        <div class="small" style="text-align:center;color:#7f9bb9;padding:20px">Aucune conversation sélectionnée</div>
      </div>

      <!-- composer -->
      <div class="composer">
        <input id="msgInput" placeholder="Écrire un message..." />
        <button class="btn" id="sendBtn">Envoyer</button>
        <button class="btn secondary" id="attachBtn">+</button>
      </div>
    </div>

    <!-- RIGHT: Blogs premium -->
    <div class="panel right">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <strong>Blogs & Premium</strong>
        <div class="small">Abonnés & achats</div>
      </div>

      <div id="blogsList">
        <!-- injected -->
      </div>

      <div style="margin-top:auto">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <div style="font-weight:700">Votre compte</div>
            <div class="small" id="walletBalance">Solde: 0 XAF (démo)</div>
          </div>
          <button class="btn" id="topupBtn">Recharger</button>
        </div>
      </div>
    </div>
  </div>

  <!-- payment modal -->
  <div class="modal" id="paymentModal" aria-hidden="true">
    <div class="box">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <strong id="payTitle">Payer le blog premium</strong>
        <button class="btn secondary" id="closeModal">Fermer</button>
      </div>
      <p class="small" id="payDesc">Sélectionnez un moyen de paiement</p>
      <div class="pay-methods" id="payMethods">
        <div class="pay-card" data-method="orange">Orange Money</div>
        <div class="pay-card" data-method="mtn">MTN Mobile Money</div>
        <div class="pay-card" data-method="paypal">PayPal</div>
        <div class="pay-card" data-method="card">Carte bancaire</div>
      </div>

      <div id="payFormArea" style="margin-top:12px"></div>

      <div style="display:flex;justify-content:flex-end;gap:8px;margin-top:12px">
        <button class="btn secondary" id="cancelPay">Annuler</button>
        <button class="btn" id="confirmPay">Confirmer</button>
      </div>
      <div id="payResult" class="small" style="margin-top:8px;color:#a7f3d0"></div>
    </div>
  </div>

  <footer>CABREL — Démo front-end • Implémentez le backend pour activer les vraies transactions</footer>

  <script>
    /***********************
     * Données démo
     ***********************/
    const state = {
      contacts: [
        { id: 'u1', name:'Amina', unread:2 },
        { id: 'u2', name:'Jean', unread:0 },
        { id: 'u3', name:'Moussa', unread:1 },
      ],
      chats: {
        u1: [{from:'them',text:'Salut Cabrel!'}, {from:'me',text:'Salut Amina, quoi de neuf ?'}],
        u2: [{from:'them',text:'Tu as vu la dernière news?'}],
      },
      currentChat: null,
      blogs: [
        { id:'b1', title:'Voyage au Cameroun', excerpt:'Récit de voyage...', price:500, premium:true },
        { id:'b2', title:'Astuce dev rapide', excerpt:'Un hack pour vos apps', price:0, premium:false },
        { id:'b3', title:'Secrets de productivité', excerpt:'Payant & exclusif', price:1000, premium:true },
      ],
      wallet: 0
    };

    /* ====== Utils DOM helpers ====== */
    const $ = sel => document.querySelector(sel);
    const $$ = sel => Array.from(document.querySelectorAll(sel));

    /* ====== Render contacts ====== */
    function renderContacts(filter=''){
      const el = $('#contactsList'); el.innerHTML='';
      state.contacts.filter(c => c.name.toLowerCase().includes(filter.toLowerCase())).forEach(c=>{
        const d = document.createElement('div');
        d.className='contact'; d.dataset.id=c.id;
        d.innerHTML = `<div class="avatar">${c.name[0]}</div>
          <div style="flex:1">
            <div style="display:flex;justify-content:space-between"><div>${c.name}</div>${c.unread?'<div class="unread">'+c.unread+'</div>':''}</div>
            <div class="small">Dernier message...</div>
          </div>`;
        d.addEventListener('click', ()=>openChat(c.id));
        el.appendChild(d);
      });
    }

    function renderBlogs(){
      const el = $('#blogsList'); el.innerHTML='';
      state.blogs.forEach(b=>{
        const box = document.createElement('div'); box.className='blog ' + (b.premium ? 'locked' : '');
        box.innerHTML = `<div style="display:flex;justify-content:space-between;align-items:center">
            <strong>${b.title}</strong>
            <div class="small">${b.price>0? b.price+' XAF':'Gratuit'}</div>
          </div>
          <div class="small" style="margin-top:8px">${b.excerpt}</div>
          <div style="margin-top:8px;display:flex;gap:8px;justify-content:flex-end">
            ${b.premium ? `<button class="btn" data-buy="${b.id}">Voir (Acheter)</button>` : `<button class="btn secondary" data-read="${b.id}">Lire</button>`}
          </div>`;
        el.appendChild(box);
      });

      // attach buy buttons
      $$('#blogsList [data-buy]').forEach(btn=>{
        btn.addEventListener('click', e=>{
          const id = e.currentTarget.dataset.buy;
          openPaymentModal(id);
        });
      });
      $$('#blogsList [data-read]').forEach(btn=>{
        btn.addEventListener('click', e=>{
          alert('Lecture du blog (démo) — ' + e.currentTarget.dataset.read);
        });
      });
    }

    /* ====== Chat functions ====== */
    function openChat(id){
      state.currentChat = id;
      $('#chatTitle').textContent = state.contacts.find(c=>c.id===id).name;
      $('#chatSub').textContent = 'Conversation';
      renderChatMessages();
    }
    function renderChatMessages(){
      const area = $('#chatArea');
      area.innerHTML = '';
      if(!state.currentChat){ area.innerHTML = '<div class="small" style="text-align:center;padding:30px">Aucune conversation</div>'; return; }
      const msgs = state.chats[state.currentChat] || [];
      msgs.forEach(m=>{
        const b = document.createElement('div');
        b.className = 'bubble ' + (m.from==='me' ? 'me' : 'them');
        b.textContent = m.text;
        area.appendChild(b);
      });
      area.scrollTop = area.scrollHeight;
    }
    document.getElementById('sendBtn').addEventListener('click', ()=>{
      const txt = $('#msgInput').value.trim(); if(!txt) return;
      if(!state.currentChat){ alert('Sélectionnez un contact'); return; }
      state.chats[state.currentChat] = state.chats[state.currentChat] || [];
      state.chats[state.currentChat].push({from:'me', text:txt});
      $('#msgInput').value='';
      renderChatMessages();
      // simulate reply
      setTimeout(()=> {
        state.chats[state.currentChat].push({from:'them', text:'Réponse robot (démo) : ' + txt.split('').reverse().join('')});
        renderChatMessages();
      },700);
    });

    /* ====== Payment modal & flows (front-end demo) ====== */
    let selectedBlog = null;
    function openPaymentModal(blogId){
      selectedBlog = state.blogs.find(b=>b.id===blogId);
      $('#payTitle').textContent = `Acheter : ${selectedBlog.title} — ${selectedBlog.price} XAF`;
      $('#payDesc').textContent = selectedBlog.excerpt;
      $('#paymentModal').classList.add('open');
      $('#payFormArea').innerHTML = '';
      $('#payResult').textContent = '';
    }
    $('#closeModal').addEventListener('click', ()=> $('#paymentModal').classList.remove('open'));
    $('#cancelPay').addEventListener('click', ()=> $('#paymentModal').classList.remove('open'));

    // choose method UI
    document.getElementById('payMethods').addEventListener('click', (e)=>{
      const card = e.target.closest('.pay-card');
      if(!card) return;
      const method = card.dataset.method;
      renderPaymentForm(method);
    });

    function renderPaymentForm(method){
      const area = $('#payFormArea');
      area.innerHTML = ''; // simple forms
      if(method === 'orange' || method === 'mtn'){
        area.innerHTML = `
          <div class="small">Numéro du client (ex: 690000000)</div>
          <input id="mobileNumber" placeholder="Entrez le numéro" style="width:100%;padding:10px;border-radius:8px;margin-top:6px">
          <div class="small" style="margin-top:8px">Le paiement sera initié via l'API opérateur (backend requis).</div>
        `;
      } else if(method === 'paypal'){
        area.innerHTML = `
          <div class="small">Paiement sécurisé via PayPal. Vous serez redirigé pour finaliser.</div>
          <div id="paypalContainer" style="margin-top:8px"></div>
        `;
        // In a real integration: render PayPal Buttons (requires client token from server)
      } else if(method === 'card'){
        area.innerHTML = `
          <div class="small">Paiement carte (démonstration). Les informations de carte NE DOIVENT PAS transiter sans backend sécurisé.</div>
          <input id="cardNumber" placeholder="Numéro de carte" style="width:100%;padding:10px;border-radius:8px;margin-top:6px">
          <div style="display:flex;gap:8px;margin-top:6px">
            <input id="cardExp" placeholder="MM/AA" style="flex:1;padding:10px;border-radius:8px">
            <input id="cardCVC" placeholder="CVC" style="width:110px;padding:10px;border-radius:8px">
          </div>
        `;
      }
    }

    // Confirm payment: simulate flow and call backend endpoints placeholders
    $('#confirmPay').addEventListener('click', async ()=>{
      const formArea = $('#payFormArea');
      if(!formArea.children.length){ alert('Sélectionnez un moyen de paiement'); return; }
      $('#payResult').textContent = 'Initialisation...';

      // detect method by presence of fields (simple demo)
      if($('#mobileNumber')) {
        const num = $('#mobileNumber').value.trim();
        if(!num){ alert('Entrez le numéro'); return; }
        $('#payResult').textContent = 'Demande de paiement envoyée à l\'opérateur...';
        // BACKEND REQUIRED: POST /api/payments/momo (MTN) or /api/payments/orange
        try {
          const res = await fakeFetch('/api/payments/mobile', { method:'POST', body: JSON.stringify({ provider:'mtn_or_orange', number:num, amount:selectedBlog.price, blogId:selectedBlog.id })});
          if(res.ok){
            $('#payResult').textContent = 'Paiement initialisé — confirmez sur votre mobile.';
            // In production: poll status or listen to webhook
            // On success, unlock blog:
            setTimeout(()=> {
              unlockBlog(selectedBlog.id);
              $('#payResult').textContent = 'Paiement confirmé ✅ Blog débloqué.';
            }, 3000);
          } else { throw new Error('Erreur opérateur'); }
        } catch(err){
          $('#payResult').textContent = 'Erreur: ' + err.message;
        }
      } else if($('#cardNumber')){
        $('#payResult').textContent = 'Traitement carte (démo)…';
        // BACKEND REQUIRED: send card data securely to payment gateway (Stripe/PayPal) via server
        try {
          const payload = { provider:'card', amount:selectedBlog.price, blogId:selectedBlog.id };
          const res = await fakeFetch('/api/payments/card', { method:'POST', body: JSON.stringify(payload) });
          if(res.ok){
            unlockBlog(selectedBlog.id);
            $('#payResult').textContent = 'Paiement carte approuvé ✅ Blog débloqué.';
          } else throw new Error('Paiement refusé');
        } catch(e){ $('#payResult').textContent = 'Erreur: ' + e.message; }
      } else {
        // PayPal or other flows
        $('#payResult').textContent = 'Redirection / création d\'ordre PayPal (démo).';
        try {
          // BACKEND REQUIRED: call /api/payments/paypal/create-order -> return approvalUrl or orderId
          const res = await fakeFetch('/api/payments/paypal/create-order', { method:'POST', body: JSON.stringify({amount:selectedBlog.price, blogId:selectedBlog.id})});
          if(res.ok){
            // In real: redirect to PayPal approval, then capture on return via server/webhook
            unlockBlog(selectedBlog.id);
            $('#payResult').textContent = 'Paiement PayPal simulé OK — blog débloqué.';
          } else throw new Error('PayPal error');
        } catch(e){ $('#payResult').textContent = 'Erreur: ' + e.message; }
      }
    });

    function unlockBlog(blogId){
      const b = state.blogs.find(x=>x.id===blogId);
      if(b) b.premium = false;
      renderBlogs();
      $('#paymentModal').classList.remove('open');
    }

    /* ====== Fake fetch for demo (simulate backend) ====== */
    async function fakeFetch(url, opts){
      console.log('FAKEFETCH', url, opts);
      await new Promise(r=>setTimeout(r,800));
      // pretend success
      return { ok:true, json: async ()=>({status:'ok'}) };
    }

    /* ====== Init UI ====== */
    renderContacts();
    renderBlogs();

    // search contacts
    $('#searchContacts').addEventListener('input', e=> renderContacts(e.target.value));

    // other buttons
    $('#composeBlogBtn').addEventListener('click', ()=>{
      const t = prompt('Titre du blog:'); if(!t) return;
      const ex = prompt('Extrait:') || '';
      const price = parseInt(prompt('Prix (XAF, 0 = gratuit):','0')||'0',10);
      state.blogs.unshift({ id:'b'+Date.now(), title:t, excerpt:ex, price:price, premium: price>0 });
      renderBlogs();
    });

    $('#topupBtn').addEventListener('click', ()=> alert('Recharger: implémenter via votre gateway (backend).'));

    // toggle feed
    let feedVisible = false;
    $('#toggleFeed').addEventListener('click', ()=>{
      feedVisible = !feedVisible;
      if(feedVisible){
        $('#chatArea').innerHTML = state.blogs.map(b => `<div style="padding:12px;border-radius:10px;background:rgba(255,255,255,0.01);margin:8px 0"><strong>${b.title}</strong><div class="small">${b.excerpt}</div></div>`).join('');
        $('#toggleFeed').textContent = 'Voir Chat';
      } else {
        renderChatMessages();
        $('#toggleFeed').textContent = 'Voir Blogs';
      }
    });

    /* ====== Notes: sauvegarde locale (demo) ====== */
    window.addEventListener('beforeunload', ()=> {
      try{ localStorage.setItem('cabrel_state', JSON.stringify(state)); } catch(e){}
    });
    // try load
    try {
      const s = JSON.parse(localStorage.getItem('cabrel_state')||'null');
      if(s){ Object.assign(state, s); renderBlogs(); renderContacts(); }
    } catch(e){}
  </script>
</body>
</html>
