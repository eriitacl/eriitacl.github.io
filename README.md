<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no,viewport-fit=cover">
<title>NOCTIS VPN</title>
<script src="https://telegram.org/js/telegram-web-app.js"></script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@500;600;700&family=Manrope:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#08080c;
    --card:linear-gradient(180deg,#12101a,#0c0a13);
    --border:rgba(139,92,246,.18);
    --border-hi:rgba(167,139,250,.55);
    --text:#e9e5f2;
    --muted:#8b85a0;
    --accent:#a855f7;
    --accent-deep:#7c3aed;
    --danger:#e11d48;
    --gold:#c9a86a;
    --nav-h:66px;
  }
  *{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
  html,body{margin:0;padding:0;min-height:100%}
  body{
    background:var(--bg);
    color:var(--text);
    font-family:'Manrope',system-ui,-apple-system,sans-serif;
    font-size:15px;
    overscroll-behavior-y:none;
    -webkit-font-smoothing:antialiased;
  }
  /* атмосферный фон */
  body::before{
    content:'';position:fixed;inset:0;z-index:0;pointer-events:none;
    background:
      radial-gradient(900px 520px at 50% -8%, rgba(124,58,237,.30), transparent 62%),
      radial-gradient(620px 420px at 105% 100%, rgba(225,29,72,.12), transparent 60%),
      radial-gradient(520px 400px at -10% 78%, rgba(88,28,135,.20), transparent 60%);
  }
  body::after{
    content:'';position:fixed;inset:0;z-index:0;pointer-events:none;opacity:.045;
    background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='160' height='160'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.85' numOctaves='3'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  }

  .app{position:relative;z-index:1;max-width:520px;margin:0 auto;padding:0 16px calc(150px + env(safe-area-inset-bottom))}

  /* ── шапка ── */
  header{display:flex;align-items:center;gap:12px;padding:22px 0 18px}
  .sigil{width:40px;height:40px;flex:none;filter:drop-shadow(0 0 10px rgba(168,85,247,.45))}
  .brand{display:flex;flex-direction:column;line-height:1.1}
  .brand b{
    font-family:'Cinzel',serif;font-weight:700;font-size:17px;letter-spacing:.16em;
    background:linear-gradient(180deg,#f3ecff,#a78bfa);
    -webkit-background-clip:text;background-clip:text;color:transparent;
  }
  .brand span{font-size:10.5px;letter-spacing:.22em;color:var(--muted);text-transform:uppercase;margin-top:3px}

  /* ── заголовки ── */
  .h-title{
    font-family:'Cinzel',serif;font-weight:600;font-size:22px;letter-spacing:.06em;
    margin:6px 0 4px;
  }
  .h-sub{color:var(--muted);font-size:13px;margin:0 0 18px}

  /* ── страницы ── */
  .page{display:none}
  .page.active{display:block;animation:fade .28s ease both}
  @keyframes fade{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:none}}

  /* ── карточки ── */
  .card{
    background:var(--card);
    border:1px solid var(--border);
    border-radius:18px;
    padding:18px;
    position:relative;
    overflow:hidden;
  }
  .card + .card{margin-top:12px}

  /* ── локации ── */
  .loc-scroll{
    display:flex;gap:8px;overflow-x:auto;padding:2px 0 10px;margin:0 -16px;
    padding-left:16px;padding-right:16px;scrollbar-width:none;
  }
  .loc-scroll::-webkit-scrollbar{display:none}
  .loc{
    flex:none;display:flex;align-items:center;gap:7px;
    padding:9px 14px;border-radius:12px;
    background:#0f0d17;border:1px solid var(--border);
    color:var(--muted);font-size:13px;font-weight:600;
    transition:.2s;cursor:pointer;white-space:nowrap;
  }
  .loc.on{
    color:#f3ecff;border-color:var(--border-hi);
    background:linear-gradient(180deg,rgba(124,58,237,.28),rgba(124,58,237,.10));
    box-shadow:0 0 18px rgba(124,58,237,.28);
  }
  .loc i{font-style:normal;font-size:15px}

  /* ── тарифы ── */
  .plan{
    background:var(--card);
    border:1px solid var(--border);
    border-radius:18px;
    padding:18px 18px 16px;
    margin-bottom:12px;
    position:relative;
    cursor:pointer;
    transition:.22s;
  }
  .plan:active{transform:scale(.985)}
  .plan.selected{
    border-color:var(--border-hi);
    box-shadow:0 0 0 1px rgba(167,139,250,.25), 0 12px 34px -14px rgba(124,58,237,.75);
    background:linear-gradient(180deg,#171128,#0d0b14);
  }
  .plan.selected::before{
    content:'';position:absolute;inset:0;border-radius:18px;pointer-events:none;
    background:radial-gradient(360px 140px at 20% 0%, rgba(168,85,247,.16), transparent 70%);
  }
  .plan-top{display:flex;justify-content:space-between;align-items:flex-start;gap:12px}
  .plan-name{font-family:'Cinzel',serif;font-size:17px;font-weight:600;letter-spacing:.05em}
  .plan-days{color:var(--muted);font-size:12px;margin-top:3px}
  .plan-price{text-align:right;flex:none}
  .plan-price b{font-size:21px;font-weight:700;letter-spacing:-.01em}
  .plan-price s{display:block;color:#5f5a72;font-size:12px;margin-top:2px}
  .plan-perks{display:flex;flex-wrap:wrap;gap:6px;margin-top:14px}
  .perk{
    font-size:11.5px;color:#b6afc9;padding:5px 10px;border-radius:8px;
    background:rgba(255,255,255,.035);border:1px solid rgba(255,255,255,.05);
  }
  .badge{
    position:absolute;top:-1px;right:-1px;
    font-size:10px;font-weight:700;letter-spacing:.12em;text-transform:uppercase;
    padding:6px 12px;border-radius:0 18px 0 14px;
    background:linear-gradient(135deg,var(--accent-deep),#a855f7);
    color:#fff;
  }
  .badge.gold{background:linear-gradient(135deg,#8a6d2f,var(--gold));color:#151018}

  /* ── чек-аут ── */
  .checkout{
    position:fixed;left:0;right:0;z-index:20;
    bottom:calc(var(--nav-h) + env(safe-area-inset-bottom));
    max-width:520px;margin:0 auto;
    padding:10px 16px 12px;
    display:flex;align-items:center;gap:12px;
    background:linear-gradient(180deg,rgba(8,8,12,0),rgba(8,8,12,.94) 38%);
    transform:translateY(140%);transition:transform .3s cubic-bezier(.2,.8,.2,1);
  }
  .checkout.on{transform:none}
  .checkout-info{flex:1;min-width:0}
  .checkout-label{display:block;font-size:11.5px;color:var(--muted);letter-spacing:.06em;text-transform:uppercase}
  .checkout-price{display:block;font-size:18px;font-weight:700;margin-top:2px}
  .btn-primary{
    flex:none;border:0;cursor:pointer;
    padding:14px 26px;border-radius:14px;
    font-family:'Manrope',sans-serif;font-size:15px;font-weight:700;letter-spacing:.02em;
    color:#fff;
    background:linear-gradient(180deg,#a855f7,#7c3aed);
    box-shadow:0 10px 26px -10px rgba(168,85,247,.9), inset 0 1px 0 rgba(255,255,255,.22);
    transition:.18s;
  }
  .btn-primary:active{transform:translateY(1px) scale(.98);filter:brightness(.94)}

  /* ── нижняя навигация ── */
  .nav{
    position:fixed;left:0;right:0;bottom:0;z-index:30;
    max-width:520px;margin:0 auto;
    height:calc(var(--nav-h) + env(safe-area-inset-bottom));
    padding-bottom:env(safe-area-inset-bottom);
    display:flex;
    background:rgba(10,9,16,.82);
    backdrop-filter:blur(18px);-webkit-backdrop-filter:blur(18px);
    border-top:1px solid rgba(139,92,246,.16);
  }
  .nav-item{
    flex:1;border:0;background:none;cursor:pointer;
    display:flex;flex-direction:column;align-items:center;justify-content:center;gap:4px;
    color:#6e6884;font-family:'Manrope',sans-serif;font-size:10.5px;font-weight:600;
    letter-spacing:.06em;transition:.2s;
  }
  .nav-item svg{width:21px;height:21px;stroke:currentColor;fill:none;stroke-width:1.6;stroke-linecap:round;stroke-linejoin:round}
  .nav-item.active{color:#c4b5fd}
  .nav-item.active svg{filter:drop-shadow(0 0 7px rgba(167,139,250,.65))}

  /* ── подписка ── */
  .sub-hero{
    background:linear-gradient(160deg,#1a1030,#0c0a14 70%);
    border:1px solid var(--border-hi);
    border-radius:20px;padding:20px;position:relative;overflow:hidden;
  }
  .sub-hero::after{
    content:'';position:absolute;width:220px;height:220px;right:-70px;top:-90px;
    background:radial-gradient(circle,rgba(168,85,247,.30),transparent 68%);
  }
  .sub-status{
    display:inline-flex;align-items:center;gap:7px;
    font-size:11px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;
    color:#4ade80;padding:6px 12px;border-radius:99px;
    background:rgba(74,222,128,.10);border:1px solid rgba(74,222,128,.28);
  }
  .sub-status.off{color:var(--danger);background:rgba(225,29,72,.10);border-color:rgba(225,29,72,.30)}
  .dot{width:6px;height:6px;border-radius:50%;background:currentColor;box-shadow:0 0 8px currentColor}
  .sub-days{font-family:'Cinzel',serif;font-size:32px;font-weight:700;margin:14px 0 2px;letter-spacing:.02em}
  .sub-days span{font-size:14px;font-family:'Manrope',sans-serif;color:var(--muted);font-weight:500}
  .bar{height:6px;border-radius:99px;background:rgba(255,255,255,.07);overflow:hidden;margin-top:16px}
  .bar > i{
    display:block;height:100%;border-radius:99px;width:var(--p,0%);
    background:linear-gradient(90deg,#7c3aed,#c084fc);
    box-shadow:0 0 14px rgba(168,85,247,.7);transition:width .6s ease;
  }
  .rows{margin-top:16px}
  .row{
    display:flex;justify-content:space-between;align-items:center;
    padding:13px 0;border-bottom:1px solid rgba(255,255,255,.055);
    font-size:14px;
  }
  .row:last-child{border-bottom:0;padding-bottom:0}
  .row span:first-child{color:var(--muted)}
  .row b{font-weight:600}

  /* ── профиль ── */
  .me{display:flex;align-items:center;gap:14px}
  .avatar{
    width:54px;height:54px;border-radius:16px;flex:none;object-fit:cover;
    background:linear-gradient(160deg,#2a1b4d,#12101a);
    border:1px solid var(--border-hi);
    display:flex;align-items:center;justify-content:center;
    font-family:'Cinzel',serif;font-size:20px;color:#c4b5fd;
  }
  .me-info b{display:block;font-size:16px}
  .me-info span{display:block;font-size:12.5px;color:var(--muted);margin-top:3px}
  .menu-item{
    display:flex;align-items:center;gap:12px;
    padding:15px 16px;border-radius:14px;
    background:#0f0d17;border:1px solid var(--border);
    margin-bottom:9px;cursor:pointer;transition:.18s;
    font-size:14px;font-weight:500;
  }
  .menu-item:active{transform:scale(.985);background:#141020}
  .menu-item svg{width:19px;height:19px;flex:none;stroke:#a78bfa;fill:none;stroke-width:1.6;stroke-linecap:round;stroke-linejoin:round}
  .menu-item .arr{margin-left:auto;color:#4d4760;font-size:16px}
  .foot{
    text-align:center;color:#4a4560;font-size:11px;letter-spacing:.14em;
    text-transform:uppercase;margin:26px 0 8px;
  }
  .toast{
    position:fixed;left:50%;bottom:calc(var(--nav-h) + 90px);
    transform:translate(-50%,20px);z-index:60;
    background:#1a1428;border:1px solid var(--border-hi);
    padding:12px 20px;border-radius:12px;font-size:13.5px;
    opacity:0;pointer-events:none;transition:.25s;
    box-shadow:0 14px 40px -12px rgba(0,0,0,.9);
  }
  .toast.on{opacity:1;transform:translate(-50%,0)}
</style>
</head>
<body>

<div class="app">

  <!-- ═══ ШАПКА ═══ -->
  <header>
    <svg class="sigil" viewBox="0 0 48 48" aria-hidden="true">
      <defs>
        <linearGradient id="g1" x1="0" y1="0" x2="1" y2="1">
          <stop offset="0" stop-color="#e9d5ff"/><stop offset="1" stop-color="#7c3aed"/>
        </linearGradient>
        <mask id="moon"><rect width="48" height="48" fill="#fff"/><circle cx="31" cy="17" r="12.5" fill="#000"/></mask>
      </defs>
      <circle cx="24" cy="24" r="20.5" fill="none" stroke="url(#g1)" stroke-width=".9" opacity=".55"/>
      <circle cx="24" cy="24" r="13.5" fill="url(#g1)" mask="url(#moon)"/>
    </svg>
    <div class="brand">
      <b>NOCTIS</b>
      <span>VPN · без границ</span>
    </div>
  </header>

  <!-- ═══ ТАРИФЫ ═══ -->
  <section class="page active" data-page="plans">
    <h1 class="h-title">Выбери тариф</h1>
    <p class="h-sub">Мгновенный доступ. Без логов. Без границ.</p>

    <div class="loc-scroll" id="locScroll"></div>

    <div id="plansList"></div>
  </section>

  <!-- ═══ ПОДПИСКА ═══ -->
  <section class="page" data-page="subs">
    <h1 class="h-title">Моя подписка</h1>
    <p class="h-sub">Статус и ключи доступа</p>
    <div id="subContent"></div>
  </section>

  <!-- ═══ ПРОФИЛЬ ═══ -->
  <section class="page" data-page="profile">
    <h1 class="h-title">Профиль</h1>
    <p class="h-sub">Аккаунт и настройки</p>

    <div class="card" style="margin-bottom:16px">
      <div class="me">
        <div class="avatar" id="avatar">?</div>
        <div class="me-info">
          <b id="userName">Гость</b>
          <span id="userHandle">—</span>
        </div>
      </div>
    </div>

    <div class="menu-item" onclick="toast('Инструкция скоро появится')">
      <svg viewBox="0 0 24 24"><path d="M4 5.5A2.5 2.5 0 0 1 6.5 3H19v16H6.5A2.5 2.5 0 0 0 4 21.5z"/><path d="M4 5.5v16"/></svg>
      Инструкция по подключению
      <span class="arr">›</span>
    </div>

    <div class="menu-item" onclick="toast('Открываю поддержку…')">
      <svg viewBox="0 0 24 24"><path d="M21 11.5a8.5 8.5 0 0 1-12.4 7.5L3 21l2-5.6A8.5 8.5 0 1 1 21 11.5z"/></svg>
      Поддержка
      <span class="arr">›</span>
    </div>

    <div class="menu-item" onclick="toast('Скоро')">
      <svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.6 1.6 0 0 0 .3 1.8l.1.1a2 2 0 1 1-2.8 2.8l-.1-.1a1.6 1.6 0 0 0-1.8-.3 1.6 1.6 0 0 0-1 1.5V21a2 2 0 1 1-4 0v-.1a1.6 1.6 0 0 0-1-1.5 1.6 1.6 0 0 0-1.8.3l-.1.1a2 2 0 1 1-2.8-2.8l.1-.1a1.6 1.6 0 0 0 .3-1.8 1.6 1.6 0 0 0-1.5-1H3a2 2 0 1 1 0-4h.1a1.6 1.6 0 0 0 1.5-1 1.6 1.6 0 0 0-.3-1.8l-.1-.1a2 2 0 1 1 2.8-2.8l.1.1a1.6 1.6 0 0 0 1.8.3H9a1.6 1.6 0 0 0 1-1.5V3a2 2 0 1 1 4 0v.1a1.6 1.6 0 0 0 1 1.5 1.6 1.6 0 0 0 1.8-.3l.1-.1a2 2 0 1 1 2.8 2.8l-.1.1a1.6 1.6 0 0 0-.3 1.8V9a1.6 1.6 0 0 0 1.5 1H21a2 2 0 1 1 0 4h-.1a1.6 1.6 0 0 0-1.5 1z"/></svg>
      Настройки
      <span class="arr">›</span>
    </div>

    <p class="foot">NOCTIS · MMXXV</p>
  </section>

</div>

<!-- ═══ ЧЕК-АУТ ═══ -->
<div class="checkout" id="checkout">
  <div class="checkout-info">
    <span class="checkout-label" id="coTitle">—</span>
    <span class="checkout-price" id="coPrice">—</span>
  </div>
  <button class="btn-primary" id="coBtn">Оплатить</button>
</div>

<!-- ═══ НАВИГАЦИЯ ═══ -->
<nav class="nav">
  <button class="nav-item active" data-nav="plans" onclick="go('plans')">
    <svg viewBox="0 0 24 24"><path d="M12 3l8 4.5v9L12 21l-8-4.5v-9z"/><path d="M12 12l8-4.5M12 12v9M12 12L4 7.5"/></svg>
    Тарифы
  </button>
  <button class="nav-item" data-nav="subs" onclick="go('subs')">
    <svg viewBox="0 0 24 24"><rect x="3" y="5" width="18" height="14" rx="2.5"/><path d="M3 10h18"/></svg>
    Подписка
  </button>
  <button class="nav-item" data-nav="profile" onclick="go('profile')">
    <svg viewBox="0 0 24 24"><circle cx="12" cy="8" r="3.6"/><path d="M4.5 20a7.5 7.5 0 0 1 15 0"/></svg>
    Профиль
  </button>
</nav>

<div class="toast" id="toast"></div>

<script>
/* ═══════════════════════════════════════════
   1. TELEGRAM WEB APP
   ═══════════════════════════════════════════ */
const tg = window.Telegram?.WebApp;

if (tg) {
  tg.ready();
  tg.expand();
  tg.setHeaderColor('#08080c');
  tg.setBackgroundColor('#08080c');
  if (tg.disableVerticalSwipes) tg.disableVerticalSwipes();
}

/* тактильный отклик */
const haptic = (type = 'light') => {
  const h = tg?.HapticFeedback;
  if (!h) return;
  if (type === 'select')  h.selectionChanged?.();
  else if (type === 'success') h.notificationOccurred?.('success');
  else if (type === 'error')   h.notificationOccurred?.('error');
  else h.impactOccurred?.(type);
};

/* пользователь */
const TG_USER = tg?.initDataUnsafe?.user;
(function renderUser() {
  const nameEl = document.getElementById('userName');
  const handleEl = document.getElementById('userHandle');
  const avEl = document.getElementById('avatar');

  if (!TG_USER) {
    nameEl.textContent = 'Гость';
    handleEl.textContent = 'Откройте через Telegram';
    return;
  }
  nameEl.textContent = [TG_USER.first_name, TG_USER.last_name].filter(Boolean).join(' ');
  handleEl.textContent = TG_USER.username ? '@' + TG_USER.username : 'id: ' + TG_USER.id;

  if (TG_USER.photo_url) {
    avEl.outerHTML = `<img class="avatar" id="avatar" src="${TG_USER.photo_url}" alt="">`;
  } else {
    avEl.textContent = (TG_USER.first_name || '?')[0].toUpperCase();
  }
})();

/* ═══════════════════════════════════════════
   2. ДАННЫЕ (замените на запросы к бэкенду)
   ═══════════════════════════════════════════ */
const LOCATIONS = [
  { id: 'nl', flag: '🇳🇱', name: 'Нидерланды' },
  { id: 'de', flag: '🇩🇪', name: 'Германия'   },
  { id: 'fi', flag: '🇫🇮', name: 'Финляндия'  },
  { id: 'us', flag: '🇺🇸', name: 'США'        },
  { id: 'jp', flag: '🇯🇵', name: 'Япония'     },
  { id: 'tr', flag: '🇹🇷', name: 'Турция'     },
];

const PLANS = [
  {
    id: 'm1', name: '1 месяц', days: 30, price: 199, old: 299, badge: null,
    perks: ['1 устройство', 'Безлимитный трафик', 'Все локации'],
  },
  {
    id: 'm3', name: '3 месяца', days: 90, price: 499, old: 599, badge: 'Выбор большинства',
    perks: ['2 устройства', 'Безлимитный трафик', 'Все локации', 'Приоритет'],
  },
  {
    id: 'm12', name: '12 месяцев', days: 365, price: 1490, old: 2399, badge: '−38%', gold: true,
    perks: ['3 устройства', 'Безлимитный трафик', 'Все локации', 'Приоритет', 'Ранний доступ'],
  },
];

/* мок-подписка — замените на fetch('/api/subscription') */
const SUBSCRIPTION = {
  active: true,
  plan: '3 месяца',
  location: 'Нидерланды',
  daysLeft: 45,
  daysTotal: 90,
  devices: 2,
  devicesMax: 3,
  configUrl: '#',
};

/* ═══════════════════════════════════════════
   3. СОСТОЯНИЕ
   ═══════════════════════════════════════════ */
let selectedLocation = LOCATIONS[0];
let selectedPlan = null;

/* ═══════════════════════════════════════════
   4. РЕНДЕР: ЛОКАЦИИ
   ═══════════════════════════════════════════ */
const locScroll = document.getElementById('locScroll');
locScroll.innerHTML = LOCATIONS.map(l => `
  <div class="loc ${l.id === selectedLocation.id ? 'on' : ''}" data-loc="${l.id}">
    <i>${l.flag}</i>${l.name}
  </div>
`).join('');

locScroll.addEventListener('click', e => {
  const el = e.target.closest('.loc');
  if (!el) return;
  selectedLocation = LOCATIONS.find(l => l.id === el.dataset.loc);
  locScroll.querySelectorAll('.loc').forEach(n => n.classList.toggle('on', n === el));
  haptic('select');
});

/* ═══════════════════════════════════════════
   5. РЕНДЕР: ТАРИФЫ
   ═══════════════════════════════════════════ */
const plansList = document.getElementById('plansList');

plansList.innerHTML = PLANS.map(p => `
  <div class="plan" data-id="${p.id}">
    ${p.badge ? `<div class="badge ${p.gold ? 'gold' : ''}">${p.badge}</div>` : ''}
    <div class="plan-top">
      <div>
        <div class="plan-name">${p.name}</div>
        <div class="plan-days">${p.days} дней доступа</div>
      </div>
      <div class="plan-price">
        <b>${p.price} ₽</b>
        <s>${p.old} ₽</s>
      </div>
    </div>
    <div class="plan-perks">
      ${p.perks.map(x => `<span class="perk">${x}</span>`).join('')}
    </div>
  </div>
`).join('');

plansList.addEventListener('click', e => {
  const el = e.target.closest('.plan');
  if (!el) return;
  selectPlan(el.dataset.id);
});

/* ═══════════════════════════════════════════
   6. ВЫБОР ТАРИФА
   ═══════════════════════════════════════════ */
const checkout    = document.getElementById('checkout');
const coTitle     = document.getElementById('coTitle');
const coPrice     = document.getElementById('coPrice');

function selectPlan(id) {
  selectedPlan = PLANS.find(p => p.id === id);
  plansList.querySelectorAll('.plan')
    .forEach(n => n.classList.toggle('selected', n.dataset.id === id));

  coTitle.textContent = `${selectedPlan.name} · ${selectedLocation.name}`;
  coPrice.textContent = `${selectedPlan.price} ₽`;
  checkout.classList.add('on');

  haptic('medium');
}

/* ═══════════════════════════════════════════
   7. ОПЛАТА
   ═══════════════════════════════════════════ */
document.getElementById('coBtn').addEventListener('click', () => {
  if (!selectedPlan) return;
  haptic('heavy');
  startPayment(selectedPlan, selectedLocation);
});

/**
 * Подключите здесь свой платёжный шлюз.
 * initData ОБЯЗАТЕЛЬНО отправляйте на бэкенд —
 * по нему вы проверяете, что запрос пришёл из Telegram.
 */
async function startPayment(plan, location) {
  const btn = document.getElementById('coBtn');
  btn.disabled = true;
  btn.textContent = 'Создаём счёт…';

  try {
    /*
    const res = await fetch('/api/pay', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        planId: plan.id,
        location: location.id,
        initData: tg?.initData,          // ← валидируем на сервере
      }),
    });
    if (!res.ok) throw new Error('pay failed');
    const { payUrl } = await res.json();

    if (tg?.openLink) tg.openLink(payUrl);   // в Telegram — внешняя ссылка
    else window.location.href = payUrl;      // в браузере — редирект
    */

    // заглушка для теста:
    console.log('CHECKOUT', { plan: plan.id, location: location.id });
    toast('Оплата: подключите шлюз в startPayment()');
    haptic('success');
  } catch (err) {
    console.error(err);
    haptic('error');
    toast('Ошибка. Попробуйте ещё раз');
  } finally {
    btn.disabled = false;
    btn.textContent = 'Оплатить';
  }
}

/* ═══════════════════════════════════════════
   8. РЕНДЕР: ПОДПИСКА
   ═══════════════════════════════════════════ */
const subContent = document.getElementById('subContent');

function renderSubscription(s) {
  if (!s || !s.active) {
    subContent.innerHTML = `
      <div class="sub-hero">
        <span class="sub-status off"><span class="dot"></span>Не активно</span>
        <div class="sub-days">0 <span>дней</span></div>
        <div class="bar"><i style="--p:0%"></i></div>
        <p style="color:var(--muted);font-size:13.5px;margin:16px 0 0">
          У вас пока нет активной подписки.
        </p>
      </div>
      <div style="margin-top:14px">
        <button class="btn-primary" style="width:100%" onclick="go('plans')">Выбрать тариф</button>
      </div>`;
    return;
  }

  const pct = Math.round((1 - s.daysLeft / s.daysTotal) * 100);

  subContent.innerHTML = `
    <div class="sub-hero">
      <span class="sub-status"><span class="dot"></span>Активна</span>
      <div class="sub-days">${s.daysLeft} <span>дней осталось</span></div>
      <div class="bar"><i style="--p:${pct}%"></i></div>
      <div class="rows">
        <div class="row"><span>Тариф</span><b>${s.plan}</b></div>
        <div class="row"><span>Локация</span><b>${s.location}</b></div>
        <div class="row"><span>Устройства</span><b>${s.devices} / ${s.devicesMax}</b></div>
      </div>
    </div>

    <div class="card" style="margin-top:12px">
      <div style="font-size:13px;color:var(--muted);margin-bottom:12px">Ключ доступа</div>
      <button class="btn-primary" style="width:100%" onclick="openConfig()">Скопировать конфиг</button>
    </div>

    <div style="margin-top:12px">
      <button class="btn-primary" style="width:100%;background:linear-gradient(180deg,#3b2a5c,#241a38);box-shadow:none"
              onclick="go('plans')">Продлить подписку</button>
    </div>
  `;
}

function openConfig() {
  haptic('medium');
  if (SUBSCRIPTION.configUrl && SUBSCRIPTION.configUrl !== '#') {
    tg?.openLink ? tg.openLink(SUBSCRIPTION.configUrl) : window.open(SUBSCRIPTION.configUrl);
  } else {
    toast('Ссылка на конфиг: подключите бэкенд');
  }
}

renderSubscription(SUBSCRIPTION);

/* ═══════════════════════════════════════════
   9. НАВИГАЦИЯ
   ═══════════════════════════════════════════ */
function go(page) {
  document.querySelectorAll('[data-page]')
    .forEach(el => el.classList.toggle('active', el.dataset.page === page));
  document.querySelectorAll('.nav-item')
    .forEach(el => el.classList.toggle('active', el.dataset.nav === page));

  // чек-аут виден только на странице тарифов
  checkout.classList.toggle('on', page === 'plans' && !!selectedPlan);

  window.scrollTo({ top: 0, behavior: 'smooth' });
  haptic('select');
}

/* ═══════════════════════════════════════════
   10. ТОСТ
   ═══════════════════════════════════════════ */
let toastTimer;
function toast(msg) {
  const el = document.getElementById('toast');
  el.textContent = msg;
  el.classList.add('on');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => el.classList.remove('on'), 2400);
}
</script>
</body>
</html>
