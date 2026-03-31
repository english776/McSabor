# Conversion-Focused Fast-Food Offers Page Deep Research and Production-Ready Implementation

## Project goal and design reference signals

The page’s single job is immediate conversion: users should confidently click “Pedir ahora / Canjear oferta” with minimal friction and maximum clarity. This is consistent with how McDonald’s Spain frames offers as “Promociones únicas” and repeatedly drives users toward app-based redemption (“Solo en la App…”, “Únete…”, “¡Lo quiero!”), while also including a reality-check disclaimer that the offers shown may not be current. citeturn6view0turn2view0

Your provided visual reference (a promo-style creative) reinforces several high-performing fast-food patterns: high-saturation food photography, extremely dominant headline (“Combo Supremo”), a strong “gift”/bonus message, and an oversized price point that anchors value perception at a glance. This combination informs the implementation below: neutral background to make offer cards pop, a single “featured” hero offer, and price/CTA hierarchy that can be read in under a second.

## Evidence-based persuasion patterns to use (and how to keep them ethical)

Distinctive elements (one primary CTA, one highlighted “featured” offer) are supported by the isolation / Von Restorff effect: distinct items in a set are more likely to be noticed and remembered than homogeneous items. citeturn5search30turn5search0 This is why the design isolates a single *dominant* CTA style (yellow gradient) and keeps the global page background light and low-contrast, so the cards and buttons become the “distinctive” stimuli.

Price anchoring is supported by marketing and behavioural-economics research showing that reference prices (e.g., a higher crossed-out price) influence perceived value and evaluation of the sale price. citeturn0search18turn0search21turn0search9 In practice, the implementation enforces a strict hierarchy: the original price is small, muted, and struck through; the offer price is large, bold, and in the primary accent colour (red).

Urgency and scarcity cues (like countdown timers) can increase time pressure and can meaningfully influence purchasing behaviour in online contexts, but they can also backfire when users detect manipulation or when cues are not credible. citeturn0search13turn0search16 For that reason, the code comments explicitly instruct you to drive the countdown from a real backend expiry (rather than resetting on refresh), and to disable limited-time CTAs when it ends.

This is not just “nice to have”: EU consumer-protection guidance under the Unfair Commercial Practices Directive warns against presenting an offer as time-limited if it will actually continue to be available at the same price afterwards. citeturn4search0 Broader policy work also flags “dark commercial patterns” as manipulative interface practices that can harm consumers and trust over time. citeturn4search9

Social proof is a well-documented UX persuasion mechanism: people look to others’ behaviour to guide their own actions (especially under uncertainty). citeturn4search4 However, “fake activity” messages are a common deceptive pattern; to keep the approach ethical, the toast system should be fed by real events (or clearly labelled as simulated in demos). This aligns with both consumer-trust best practices and the reality that overused persuasion cues can trigger resistance. citeturn0search16turn4search9

## Visual system and layout choices informed by the reference site

The McDonald’s Spain product/offers pages emphasise: offer discovery via category browsing (“Ofertas”, “Novedades”, “Packs para compartir”), decisive CTAs (“¡Lo quiero!”), and short persuasive microcopy while keeping legal/disclaimer text available. citeturn6view0turn2view0

Accordingly, the implemented page uses:
- A minimal sticky header with brand mark + cart indicator (fast reassurance and orientation).
- A hero “featured deal” block that behaves like a promotional banner (headline, value promise, big price, urgency timer).
- Horizontal, scrollable “pill” filters (quick narrowing without navigation jumps), using CSS Scroll Snap for usability. citeturn3search7
- A responsive grid of 4 offer cards, each structured to be legible in a single vertical scan on mobile (image → title/benefit → anchored pricing → giant CTA).

Colour psychology in food contexts is complex and context-dependent, but research commonly finds that warm, intense colours (including reds/oranges) are associated with arousal and can influence food perceptions and appetite-related responses—while other studies show red can sometimes function as a “stop” cue depending on how it is applied. citeturn3search8turn3search0turn3search20 The safest conversion-focused interpretation is: use red as a *directional accent* (prices, urgency cues) and yellow as the *action colour* (CTA), while keeping the remaining UI quiet and unintrusive.

## Mobile-first interaction design and motion for “click invitation”

The design defaults to one-column mobile layout and progressively enhances to 2–3 columns on wider screens using CSS Grid. Filters and header controls use Flexbox for predictable alignment on small screens.

The filters use Scroll Snap to create a “pocket carousel” feel that encourages exploration while remaining thumb-friendly. citeturn3search7 Hover effects are applied only when the device supports hover (so mobile doesn’t get awkward stuck-hover states), and the required “scale(1.03) + stronger shadow” is implemented to invite clicking.

For “smooth transition while scrolling”, the implementation combines:
- `scroll-behavior: smooth` for in-page navigation (with reduced-motion fallback). citeturn3search11turn3search3  
- Scroll-reveal entrance transitions powered by the Intersection Observer API to softly fade/translate sections into view. citeturn5search2

## Accessibility and compliance in persuasive UI

The code is built to stay compatible with modern accessibility guidance:
- Keyboard focus is visible and obvious (aligning with entity["organization","W3C","web standards consortium"] WCAG focus expectations). citeturn3search6turn3search2  
- Motion is reduced when the user requests it (`prefers-reduced-motion`) to avoid vestibular discomfort and distraction. citeturn3search3turn3search39  
- Toast notifications are implemented as non-blocking status messages, with an ARIA live region to announce updates to screen readers. citeturn5search7turn5search3  

For social proof specifically, UX research guidance (e.g., entity["organization","Nielsen Norman Group","ux research firm"]) notes that social proof can be effective but should not distract from core user goals; in this design, the toast is small, transient, dismissible, and placed away from the CTA so it nudges without interfering. citeturn4search4turn4search8

## Production-ready single-file HTML with modern CSS and Vanilla JS

```html
<!doctype html>
<html lang="es-ES">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <meta name="color-scheme" content="light" />
  <title>Ofertas | FastBite</title>
  <meta name="description" content="Ofertas de comida rápida por tiempo limitado. Canjea ofertas y pide en segundos." />

  <!-- Font (swap to Montserrat if you prefer) -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700;800;900&display=swap" rel="stylesheet" />

  <style>
    :root{
      --bg:#f7f7f9;
      --surface:#ffffff;
      --text:#13161a;
      --muted:#6d737a;

      --red:#d40d2a;      /* urgency / prices */
      --yellow:#ffba08;   /* CTA / happiness */

      --shadow:0 12px 30px rgba(16,20,25,.12);
      --shadow-strong:0 18px 44px rgba(16,20,25,.18);

      --radius:16px;
      --radius-xl:22px;
      --max:1120px;
    }

    *{ box-sizing:border-box; }
    html{ scroll-behavior:smooth; }
    @media (prefers-reduced-motion: reduce){
      html{ scroll-behavior:auto; }
    }

    body{
      margin:0;
      font-family:"Poppins",system-ui,-apple-system,"Segoe UI",Roboto,Arial,sans-serif;
      background:var(--bg);
      color:var(--text);
    }

    a{ color:inherit; text-decoration:none; }
    button{ font:inherit; }
    img{ max-width:100%; height:auto; }

    .container{ width:min(var(--max), calc(100% - 32px)); margin:0 auto; }

    .sr-only{
      position:absolute; width:1px; height:1px; padding:0; margin:-1px;
      overflow:hidden; clip:rect(0,0,0,0); white-space:nowrap; border:0;
    }

    .skip-link{
      position:absolute; left:-999px; top:10px;
      padding:10px 12px; border-radius:12px;
      background:var(--text); color:#fff; z-index:9999;
    }
    .skip-link:focus{ left:10px; outline:3px solid var(--yellow); outline-offset:2px; }

    header{
      position:sticky; top:0; z-index:50;
      background:rgba(247,247,249,.85);
      backdrop-filter:blur(10px);
      border-bottom:1px solid rgba(15,17,20,.06);
    }
    .header__inner{
      display:flex; align-items:center; justify-content:space-between;
      padding:14px 0; gap:12px;
    }
    .brand{ display:flex; align-items:center; gap:10px; }
    .logo-mark{
      width:40px; height:40px; border-radius:14px;
      background:linear-gradient(135deg, var(--red), #ff3b66);
      display:grid; place-items:center;
      box-shadow:0 12px 20px rgba(212,13,42,.22);
    }
    .logo-mark span{
      width:18px; height:18px; border-radius:6px;
      background:var(--yellow);
      transform:rotate(12deg);
    }
    .brand__name{ font-weight:900; letter-spacing:-.02em; line-height:1; }
    .brand__name small{
      display:block; margin-top:2px;
      font-size:.78rem; font-weight:700; color:var(--muted);
    }

    .cart{
      position:relative;
      display:inline-flex; align-items:center; justify-content:center;
      width:46px; height:46px; border-radius:16px;
      border:1px solid rgba(15,17,20,.10);
      background:var(--surface);
      box-shadow:0 10px 16px rgba(16,20,25,.08);
      cursor:pointer;
    }
    .cart svg{ width:22px; height:22px; }
    .cart-badge{
      position:absolute; top:-6px; right:-6px;
      min-width:22px; height:22px; padding:0 6px;
      border-radius:999px; background:var(--red); color:#fff;
      display:grid; place-items:center;
      font-size:.75rem; font-weight:900;
      border:2px solid var(--bg);
    }

    main{ padding:16px 0 56px; }

    /* HERO */
    .hero__card{
      background:
        radial-gradient(1200px 380px at 20% -40%, rgba(255,186,8,.36), transparent 60%),
        radial-gradient(900px 320px at 110% 10%, rgba(212,13,42,.18), transparent 55%),
        var(--surface);
      border:1px solid rgba(15,17,20,.08);
      border-radius:var(--radius-xl);
      overflow:hidden;
      box-shadow:var(--shadow);
    }
    .hero__grid{
      display:grid; gap:18px;
      padding:18px;
    }
    @media (min-width: 840px){
      .hero__grid{
        grid-template-columns: 1.2fr .8fr;
        align-items:center;
        padding:22px;
      }
    }

    .hero__eyebrow{
      display:inline-flex; align-items:center; gap:8px;
      padding:8px 12px; border-radius:999px;
      background:rgba(255,186,8,.18);
      color:#5b3b00; font-weight:800; font-size:.86rem;
    }
    .pulse{
      width:8px; height:8px; border-radius:999px;
      background:var(--red);
      box-shadow:0 0 0 0 rgba(212,13,42,.45);
      animation:pulse 1.6s infinite;
    }
    @keyframes pulse{
      0%{ box-shadow:0 0 0 0 rgba(212,13,42,.42); }
      70%{ box-shadow:0 0 0 10px rgba(212,13,42,0); }
      100%{ box-shadow:0 0 0 0 rgba(212,13,42,0); }
    }
    @media (prefers-reduced-motion: reduce){
      .pulse{ animation:none; }
    }

    .hero h1{
      margin:14px 0 8px;
      font-size:clamp(1.6rem, 2.6vw, 2.4rem);
      letter-spacing:-.03em;
      line-height:1.05;
    }
    .badge{
      display:inline-flex; align-items:center; justify-content:center;
      padding:6px 10px; border-radius:999px;
      background:var(--red); color:#fff;
      font-weight:900; font-size:.85em;
      vertical-align:middle; margin-left:8px;
    }
    .hero__sub{
      margin:0;
      color:var(--muted);
      font-weight:600;
      max-width:54ch;
    }

    .hero__meta{
      display:flex; flex-wrap:wrap; gap:10px;
      align-items:center;
      margin-top:14px;
    }
    .countdown{
      display:flex; gap:10px; align-items:center;
      padding:10px 12px; border-radius:16px;
      background:rgba(15,17,20,.04);
      border:1px dashed rgba(15,17,20,.14);
    }
    .countdown__label{ color:var(--muted); font-weight:700; font-size:.9rem; }
    .countdown__time{
      font-weight:900;
      letter-spacing:.08em;
      font-variant-numeric: tabular-nums;
      color:var(--red);
    }

    .price-stack{
      display:flex; align-items:baseline; gap:8px;
      padding:10px 12px; border-radius:16px;
      background:rgba(255,186,8,.16);
      border:1px solid rgba(255,186,8,.32);
    }
    .price-old{ color:var(--muted); font-size:.9rem; font-weight:700; }
    .price-old s{ text-decoration-thickness:2px; }
    .price-new{ color:var(--red); font-weight:900; font-size:1.25rem; letter-spacing:-.02em; }

    .hero__actions{
      display:flex; gap:10px; flex-wrap:wrap;
      margin-top:16px;
    }
    .btn{
      display:inline-flex; align-items:center; justify-content:center; gap:10px;
      padding:14px 16px; border-radius:18px;
      border:1px solid transparent;
      cursor:pointer;
      font-weight:900;
      letter-spacing:-.01em;
      transition:transform 160ms ease, box-shadow 160ms ease, background-color 160ms ease, border-color 160ms ease;
    }
    .btn--primary{
      background:linear-gradient(135deg, var(--yellow), #ffd24d);
      color:#2a1d00;
      box-shadow:0 16px 30px rgba(255,186,8,.28);
    }
    .btn--ghost{
      background:rgba(15,17,20,.03);
      border-color:rgba(15,17,20,.12);
      color:var(--text);
    }
    .btn:focus-visible{ outline:3px solid rgba(255,186,8,.95); outline-offset:3px; }
    @media (hover:hover){
      .btn--primary:hover{ transform:translateY(-1px); box-shadow:0 20px 36px rgba(255,186,8,.36); }
      .btn--ghost:hover{ transform:translateY(-1px); background:rgba(15,17,20,.05); }
    }
    @media (prefers-reduced-motion: reduce){
      .btn{ transition:none; }
    }

    .hero__media{
      border-radius:18px; overflow:hidden;
      border:1px solid rgba(15,17,20,.08);
      background:#fff;
    }
    .hero__media img{
      display:block;
      width:100%;
      aspect-ratio: 4 / 3;
      object-fit:cover;
    }

    .section{ margin-top:20px; }

    /* FILTERS */
    .filters{
      margin-top:14px;
      display:flex; gap:10px;
      overflow-x:auto;
      padding:8px 2px 14px;
      scroll-snap-type:x mandatory;
      -webkit-overflow-scrolling:touch;
    }
    .filters::-webkit-scrollbar{ height:8px; }
    .filters::-webkit-scrollbar-thumb{ background:rgba(15,17,20,.12); border-radius:999px; }

    .filter-pill{
      scroll-snap-align:start;
      flex:0 0 auto;
      border-radius:999px;
      padding:10px 14px;
      border:1px solid rgba(15,17,20,.12);
      background:var(--surface);
      color:var(--text);
      font-weight:900;
      cursor:pointer;
    }
    .filter-pill[aria-pressed="true"]{
      background:rgba(212,13,42,.10);
      border-color:rgba(212,13,42,.28);
      color:var(--red);
    }
    .filter-pill:focus-visible{ outline:3px solid rgba(255,186,8,.95); outline-offset:2px; }

    .offers-header{
      display:flex; align-items:flex-end; justify-content:space-between;
      gap:12px; margin-top:20px;
    }
    .offers-header h2{ margin:0; letter-spacing:-.02em; }
    .results{ color:var(--muted); font-weight:700; font-size:.95rem; }

    /* OFFERS GRID */
    .grid{
      display:grid;
      gap:14px;
      margin-top:14px;
    }
    @media (min-width: 640px){
      .grid{ grid-template-columns: repeat(2, minmax(0, 1fr)); }
    }
    @media (min-width: 980px){
      .grid{ grid-template-columns: repeat(3, minmax(0, 1fr)); }
    }

    .card{
      background:var(--surface);
      border:1px solid rgba(15,17,20,.08);
      border-radius:var(--radius);
      overflow:hidden;
      box-shadow:0 10px 22px rgba(16,20,25,.08);
      display:flex; flex-direction:column;
      min-height:100%;
      transform:translateZ(0);
      transition:transform 160ms ease, box-shadow 160ms ease;
    }
    @media (hover:hover){
      .card:hover{ transform:scale(1.03); box-shadow:var(--shadow-strong); }
    }
    @media (prefers-reduced-motion: reduce){
      .card{ transition:none; }
    }

    .card__media{ position:relative; }
    .card__media img{
      display:block;
      width:100%;
      aspect-ratio: 16 / 10;
      object-fit:cover;
      background:#f1f1f1;
    }
    .tag{
      position:absolute; top:12px; left:12px;
      padding:8px 10px; border-radius:999px;
      background:rgba(15,17,20,.74);
      color:#fff; font-weight:900; font-size:.82rem;
    }
    .tag--hot{ background:rgba(212,13,42,.92); }

    .card__body{
      padding:14px 14px 0;
      display:flex; flex-direction:column; gap:10px;
    }
    .card__title{ margin:0; font-size:1.08rem; letter-spacing:-.02em; }
    .card__desc{ margin:0; color:var(--muted); font-weight:600; line-height:1.35; }

    .pricing{
      display:flex; flex-wrap:wrap; align-items:baseline; gap:8px;
    }
    .pricing .old{ color:var(--muted); font-size:.88rem; font-weight:700; }
    .pricing .old s{ text-decoration-thickness:2px; }
    .pricing .new{ color:var(--red); font-size:1.4rem; font-weight:900; letter-spacing:-.02em; }
    .pricing .save{
      color:#3d4a57;
      background:rgba(15,17,20,.04);
      border:1px solid rgba(15,17,20,.10);
      padding:6px 10px; border-radius:999px;
      font-size:.84rem; font-weight:800;
    }

    .card__footer{
      margin-top:auto;
      padding:14px;
      display:flex; flex-direction:column; gap:10px;
    }

    .trust-line{
      display:flex; flex-wrap:wrap; gap:8px;
      color:var(--muted);
      font-weight:700; font-size:.9rem;
    }
    .trust-chip{
      display:inline-flex; align-items:center; gap:6px;
      padding:8px 10px; border-radius:999px;
      background:rgba(15,17,20,.03);
      border:1px solid rgba(15,17,20,.10);
    }

    .cta{
      width:100%;
      display:inline-flex; align-items:center; justify-content:center;
      gap:10px;
      padding:16px 16px;
      border-radius:18px;
      border:0;
      cursor:pointer;
      font-weight:900;
      letter-spacing:-.01em;
      background:linear-gradient(135deg, var(--yellow), #ffd24d);
      color:#2a1d00;
      box-shadow:0 16px 26px rgba(255,186,8,.25);
      transition:transform 140ms ease, box-shadow 140ms ease, filter 140ms ease;
    }
    .cta:focus-visible{ outline:3px solid rgba(212,13,42,.65); outline-offset:3px; }
    @media (hover:hover){
      .cta:hover{ transform:translateY(-1px); box-shadow:0 20px 34px rgba(255,186,8,.34); }
    }
    .cta[disabled]{
      cursor:not-allowed;
      filter:grayscale(.45);
      opacity:.7;
      box-shadow:none;
    }
    @media (prefers-reduced-motion: reduce){
      .cta{ transition:none; }
    }

    .disclaimer{
      margin-top:18px;
      color:var(--muted);
      font-size:.9rem;
      line-height:1.35;
    }

    /* SCROLL REVEAL */
    .reveal{
      opacity:0;
      transform:translateY(14px);
      transition:opacity 380ms ease, transform 380ms ease;
    }
    .reveal.is-inview{
      opacity:1;
      transform:translateY(0);
    }
    @media (prefers-reduced-motion: reduce){
      .reveal{ opacity:1; transform:none; transition:none; }
    }

    /* TOASTS */
    .toast-stack{
      position:fixed;
      left:16px; right:16px; bottom:16px;
      z-index:80;
      display:flex; flex-direction:column; gap:10px;
      pointer-events:none;
    }
    @media (min-width: 520px){
      .toast-stack{ left:18px; right:auto; max-width:420px; }
    }
    .toast{
      pointer-events:auto;
      display:flex; align-items:flex-start; gap:10px;
      padding:12px 12px;
      background:rgba(19,22,26,.92);
      color:#fff;
      border:1px solid rgba(255,255,255,.14);
      border-radius:18px;
      box-shadow:0 16px 32px rgba(0,0,0,.22);
      backdrop-filter: blur(10px);
    }
    .toast__icon{
      flex:0 0 auto;
      width:36px; height:36px;
      border-radius:14px;
      background:rgba(255,186,8,.18);
      display:grid; place-items:center;
    }
    .toast__msg{
      margin:0;
      font-weight:700;
      line-height:1.2;
    }
    .toast__msg span{
      color:var(--yellow);
      font-weight:900;
    }
    .toast__close{
      margin-left:auto;
      flex:0 0 auto;
      background:transparent;
      border:0;
      color:rgba(255,255,255,.8);
      cursor:pointer;
      border-radius:12px;
      padding:6px;
    }
    .toast__close:focus-visible{
      outline:3px solid rgba(255,186,8,.95);
      outline-offset:2px;
    }
  </style>
</head>

<body>
  <a class="skip-link" href="#main">Saltar a las ofertas</a>

  <header>
    <div class="container header__inner">
      <a class="brand" href="#" aria-label="Inicio FastBite">
        <div class="logo-mark" aria-hidden="true"><span></span></div>
        <div class="brand__name">
          FastBite
          <small>Ofertas</small>
        </div>
      </a>

      <button class="cart" id="cartBtn" type="button" aria-label="Abrir carrito">
        <svg viewBox="0 0 24 24" fill="none" aria-hidden="true">
          <path d="M6.5 6.5h15l-1.6 8.2a2 2 0 0 1-2 1.6H9.2a2 2 0 0 1-2-1.6L5.2 3.5H2.5"
                stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          <circle cx="9.5" cy="20" r="1.6" fill="currentColor"/>
          <circle cx="18.5" cy="20" r="1.6" fill="currentColor"/>
        </svg>
        <span class="cart-badge" id="cartCount" aria-label="Artículos en el carrito: 0">0</span>
      </button>
    </div>
  </header>

  <main id="main" class="container">
    <section class="reveal" aria-labelledby="hero-title">
      <div class="hero__card">
        <div class="hero__grid">
          <div>
            <div class="hero__eyebrow"><span class="pulse" aria-hidden="true"></span>Oferta flash (tiempo limitado)</div>
            <h1 id="hero-title">El Combo Supremo <span class="badge">2x1</span></h1>
            <p class="hero__sub">Compra un menú completo y llévate otra hamburguesa de regalo. Sabor grande, decisión fácil.</p>

            <div class="hero__meta" aria-label="Información clave de la oferta">
              <div class="countdown" role="status" aria-live="polite">
                <span class="countdown__label">Oferta termina en</span>
                <span class="countdown__time" id="countdown" aria-label="Tiempo restante">—</span>
              </div>

              <div class="price-stack" aria-label="Precios">
                <span class="price-old" aria-label="Precio original"><s>12,99€</s></span>
                <span class="price-new" aria-label="Precio de oferta">9,99€</span>
              </div>
            </div>

            <div class="hero__actions">
              <button class="btn btn--primary" id="heroCta" type="button" aria-label="Pedir ahora el Combo Supremo">
                Pedir ahora
              </button>
              <a class="btn btn--ghost" href="#ofertas" aria-label="Ver todas las ofertas">
                Ver ofertas
              </a>
            </div>

            <p class="disclaimer" style="margin-top:12px;">
              *Ejemplo de prototipo. Precios y disponibilidad pueden variar. Consulta condiciones y ofertas reales en tu app.
            </p>
          </div>

          <div class="hero__media" aria-label="Imagen del Combo Supremo">
            <img
              src="https://images.unsplash.com/photo-1550547660-d9450f859349?auto=format&fit=crop&w=1200&q=80"
              alt="Hamburguesa doble con patatas y bebida, estilo menú"
              loading="lazy"
              width="1200"
              height="900"
            />
          </div>
        </div>
      </div>
    </section>

    <section class="section reveal" aria-label="Filtra las ofertas por categoría">
      <nav class="filters" aria-label="Filtros horizontales">
        <button class="filter-pill" type="button" data-filter="all" aria-pressed="true" aria-label="Ver todas las ofertas">Todo</button>
        <button class="filter-pill" type="button" data-filter="share" aria-pressed="false" aria-label="Ver ofertas para compartir">Para compartir</button>
        <button class="filter-pill" type="button" data-filter="under5" aria-pressed="false" aria-label="Ver ofertas por menos de 5 euros">Menos de 5€</button>
        <button class="filter-pill" type="button" data-filter="new" aria-pressed="false" aria-label="Ver novedades">Novedades</button>
      </nav>

      <div class="offers-header">
        <h2 id="ofertas">Ofertas que vuelan</h2>
        <div class="results" id="resultsLabel" aria-label="Resultados">Mostrando 4/4</div>
      </div>

      <div class="grid" id="offersGrid">
        <article class="card" data-title="Combo Supremo 2x1" data-category="share new" data-price="9.99" data-limited="true">
          <div class="card__media">
            <img
              src="https://images.unsplash.com/photo-1568901346375-23c9450c58cd?auto=format&fit=crop&w=1200&q=80"
              alt="Hamburguesa doble con queso en primer plano"
              loading="lazy"
              width="1200"
              height="750"
            />
            <div class="tag tag--hot">🔥 Más canjeada</div>
          </div>

          <div class="card__body">
            <h3 class="card__title">Combo Supremo 2x1</h3>
            <p class="card__desc">Doble sabor, doble satisfacción. Ideal si vienes con hambre (o con alguien).</p>

            <div class="pricing" aria-label="Anclaje de precio del Combo Supremo">
              <span class="old"><s>12,99€</s></span>
              <span class="new">9,99€</span>
              <span class="save">Ahorras 3,00€</span>
            </div>
          </div>

          <div class="card__footer">
            <div class="trust-line" aria-label="Beneficios del pedido">
              <span class="trust-chip">✅ Pago rápido</span>
              <span class="trust-chip">📍 Recógelo cerca</span>
              <span class="trust-chip">⏱️ Listo en ~10 min</span>
            </div>

            <button class="cta" type="button" data-offer="combo-supremo" aria-label="Canjear oferta Combo Supremo">
              Canjear oferta
            </button>
          </div>
        </article>

        <article class="card" data-title="Patatas medianas" data-category="under5" data-price="1.20">
          <div class="card__media">
            <img
              src="https://images.unsplash.com/photo-1631898030992-0a5b05d39b1c?auto=format&fit=crop&w=1200&q=80"
              alt="Patatas fritas crujientes en un recipiente rojo"
              loading="lazy"
              width="1200"
              height="750"
            />
            <div class="tag">Solo hoy</div>
          </div>

          <div class="card__body">
            <h3 class="card__title">Patatas medianas por 1,20€</h3>
            <p class="card__desc">El acompañamiento que convierte “solo una cosa” en “vale, kompletito”.</p>

            <div class="pricing" aria-label="Anclaje de precio de Patatas medianas">
              <span class="old"><s>2,40€</s></span>
              <span class="new">1,20€</span>
              <span class="save">-50%</span>
            </div>
          </div>

          <div class="card__footer">
            <div class="trust-line" aria-label="Beneficios del pedido">
              <span class="trust-chip">🥔 Recién hechas</span>
              <span class="trust-chip">➕ Súmalas al menú</span>
            </div>

            <button class="cta" type="button" data-offer="patatas" aria-label="Canjear oferta Patatas medianas">
              Lo quiero
            </button>
          </div>
        </article>

        <article class="card" data-title="Pack Nuggets Party" data-category="share" data-price="4.99">
          <div class="card__media">
            <img
              src="https://images.unsplash.com/photo-1604908554024-2b37a571bdc6?auto=format&fit=crop&w=1200&q=80"
              alt="Nuggets de pollo con salsas para compartir"
              loading="lazy"
              width="1200"
              height="750"
            />
            <div class="tag">Para compartir</div>
          </div>

          <div class="card__body">
            <h3 class="card__title">Pack Nuggets Party</h3>
            <p class="card__desc">Perfecto para sofá + serie. Incluye 2 salsas a elegir (será difícil decidir).</p>

            <div class="pricing" aria-label="Anclaje de precio del Pack Nuggets Party">
              <span class="old"><s>6,49€</s></span>
              <span class="new">4,99€</span>
              <span class="save">Ahorras 1,50€</span>
            </div>
          </div>

          <div class="card__footer">
            <div class="trust-line" aria-label="Beneficios del pedido">
              <span class="trust-chip">🥫 2 salsas</span>
              <span class="trust-chip">👥 Para 2</span>
            </div>

            <button class="cta" type="button" data-offer="nuggets-party" aria-label="Canjear oferta Pack Nuggets Party">
              Canjear oferta
            </button>
          </div>
        </article>

        <article class="card" data-title="McCafé 2x1" data-category="under5 new" data-price="2.80">
          <div class="card__media">
            <img
              src="https://images.unsplash.com/photo-1511920170033-f8396924c348?auto=format&fit=crop&w=1200&q=80"
              alt="Café con espuma en una mesa, estilo cafetería"
              loading="lazy"
              width="1200"
              height="750"
            />
            <div class="tag">✨ Novedad</div>
          </div>

          <div class="card__body">
            <h3 class="card__title">McCafé 2x1</h3>
            <p class="card__desc">Dos cafés por el precio de uno. Porque el “uno más y ya” hoy sale rentable.</p>

            <div class="pricing" aria-label="Anclaje de precio de McCafé 2x1">
              <span class="old"><s>5,60€</s></span>
              <span class="new">2,80€</span>
              <span class="save">2 por 1</span>
            </div>
          </div>

          <div class="card__footer">
            <div class="trust-line" aria-label="Beneficios del pedido">
              <span class="trust-chip">☕ Elige tu blend</span>
              <span class="trust-chip">🥐 Va genial con dulce</span>
            </div>

            <button class="cta" type="button" data-offer="mccafe-2x1" aria-label="Canjear oferta McCafé 2x1">
              Lo quiero
            </button>
          </div>
        </article>
      </div>

      <p class="disclaimer">
        Consejo: estas ofertas son ejemplos para mostrar cómo se vería la página. En producción, conecta el temporizador y la prueba social con datos reales (para evitar urgencias “falsas”).
      </p>
    </section>
  </main>

  <!-- Screen-reader live region for announcements -->
  <div class="sr-only" id="srLive" aria-live="polite" aria-atomic="true"></div>

  <!-- Visual toasts -->
  <div class="toast-stack" id="toastStack" aria-label="Notificaciones"></div>

  <script>
    /**
     * FastBite /ofertas — Vanilla JS (production-friendly).
     *
     * Ethics:
     * - Countdown: DO NOT reset on refresh in production. Feed a real expiry from backend.
     * - Social proof: show real activity, or label clearly as demo/simulated.
     */
    (function () {
      "use strict";

      const $ = (sel, parent = document) => parent.querySelector(sel);
      const $$ = (sel, parent = document) => Array.from(parent.querySelectorAll(sel));

      const cartCountEl = $("#cartCount");
      const countdownEl = $("#countdown");
      const resultsLabelEl = $("#resultsLabel");
      const filters = $$(".filter-pill");
      const cards = $$(".card");
      const toastStack = $("#toastStack");
      const srLive = $("#srLive");

      const heroCta = $("#heroCta");

      const state = {
        cartCount: 0,
        activeFilter: "all",
        // Demo countdown: 5 hours from page load.
        // Replace with a fixed ISO timestamp from backend, e.g.:
        // offerEndsAt: new Date("2026-03-31T23:59:59+02:00").getTime()
        offerEndsAt: Date.now() + (5 * 60 * 60 * 1000)
      };

      function formatHMS(msRemaining) {
        const totalSeconds = Math.max(0, Math.floor(msRemaining / 1000));
        const hours = Math.floor(totalSeconds / 3600);
        const minutes = Math.floor((totalSeconds % 3600) / 60);
        const seconds = totalSeconds % 60;
        const pad = (n) => String(n).padStart(2, "0");
        return `${pad(hours)}:${pad(minutes)}:${pad(seconds)}`;
      }

      function setCartCount(n) {
        state.cartCount = n;
        cartCountEl.textContent = String(n);
        cartCountEl.setAttribute("aria-label", `Artículos en el carrito: ${n}`);
      }

      function escapeAndHighlight(message, highlight) {
        const escaped = message
          .replaceAll("&", "&amp;")
          .replaceAll("<", "&lt;")
          .replaceAll(">", "&gt;")
          .replaceAll('"', "&quot;")
          .replaceAll("'", "&#39;");

        if (!highlight) return escaped;

        const idx = escaped.toLowerCase().indexOf(highlight.toLowerCase());
        if (idx === -1) return escaped;

        const before = escaped.slice(0, idx);
        const match = escaped.slice(idx, idx + highlight.length);
        const after = escaped.slice(idx + highlight.length);
        return `${before}<span>${match}</span>${after}`;
      }

      function showToast(message, options = {}) {
        const { highlight = "", ttlMs = 6500 } = options;

        // Screen reader announcement
        srLive.textContent = message;

        // Visual toast
        const toast = document.createElement("div");
        toast.className = "toast";
        toast.setAttribute("role", "status");
        toast.setAttribute("aria-live", "polite");

        toast.innerHTML = `
          <div class="toast__icon" aria-hidden="true">🍔</div>
          <p class="toast__msg">${escapeAndHighlight(message, highlight)}</p>
          <button class="toast__close" type="button" aria-label="Cerrar notificación">✕</button>
        `;

        $(".toast__close", toast).addEventListener("click", () => toast.remove());
        toastStack.appendChild(toast);

        window.setTimeout(() => {
          if (toast.isConnected) toast.remove();
        }, ttlMs);
      }

      function updateResultsLabel(visibleCount, totalCount) {
        resultsLabelEl.textContent = `Mostrando ${visibleCount}/${totalCount}`;
      }

      function applyFilter(filterId) {
        state.activeFilter = filterId;

        filters.forEach((btn) => {
          const isActive = btn.dataset.filter === filterId;
          btn.setAttribute("aria-pressed", isActive ? "true" : "false");
        });

        let visible = 0;
        cards.forEach((card) => {
          const categories = (card.dataset.category || "").split(/\s+/).filter(Boolean);
          const price = Number(card.dataset.price || "999");

          let show = false;
          if (filterId === "all") show = true;
          if (filterId === "share") show = categories.includes("share");
          if (filterId === "new") show = categories.includes("new");
          if (filterId === "under5") show = price <= 5;

          card.style.display = show ? "" : "none";
          if (show) visible += 1;
        });

        updateResultsLabel(visible, cards.length);
      }

      function startCountdown() {
        function tick() {
          const remaining = state.offerEndsAt - Date.now();
          countdownEl.textContent = formatHMS(remaining);

          if (remaining <= 0) {
            countdownEl.textContent = "00:00:00";

            // Disable limited offer CTAs (and hero CTA)
            $$(".card[data-limited='true'] .cta").forEach((btn) => {
              btn.disabled = true;
              btn.textContent = "Oferta finalizada";
            });

            if (heroCta) {
              heroCta.disabled = true;
              heroCta.textContent = "Oferta finalizada";
            }

            window.clearInterval(timer);
          }
        }

        tick();
        const timer = window.setInterval(tick, 1000);
      }

      function hookCTAs() {
        $$(".cta").forEach((btn) => {
          btn.addEventListener("click", () => {
            if (btn.disabled) return;

            const card = btn.closest(".card");
            const title = card?.dataset?.title || "Oferta";

            setCartCount(state.cartCount + 1);
            showToast(`Añadido al carrito: ${title}`, { highlight: title });

            // Integration point for analytics / router:
            window.dispatchEvent(new CustomEvent("offer_cta_click", {
              detail: { title, offerId: btn.dataset.offer || null, ts: Date.now() }
            }));
          });
        });

        if (heroCta) {
          heroCta.addEventListener("click", () => {
            if (heroCta.disabled) return;

            setCartCount(state.cartCount + 1);
            showToast("Añadido al carrito: Combo Supremo 2x1", { highlight: "Combo Supremo" });

            window.dispatchEvent(new CustomEvent("offer_cta_click", {
              detail: { title: "Combo Supremo 2x1", offerId: "combo-supremo", ts: Date.now(), source: "hero" }
            }));
          });
        }
      }

      function hookFilters() {
        filters.forEach((btn) => btn.addEventListener("click", () => applyFilter(btn.dataset.filter)));
      }

      function initScrollReveal() {
        const els = $$(".reveal");
        if (!("IntersectionObserver" in window)) {
          els.forEach((el) => el.classList.add("is-inview"));
          return;
        }

        const obs = new IntersectionObserver((entries) => {
          entries.forEach((entry) => {
            if (entry.isIntersecting) {
              entry.target.classList.add("is-inview");
              obs.unobserve(entry.target);
            }
          });
        }, { threshold: 0.12 });

        els.forEach((el) => obs.observe(el));
      }

      function startFomoToasts() {
        // Demo social proof. Replace with real-time events in production.
        const names = ["Carlos", "Lucía", "Javier", "Marta", "Sergio", "Ana", "Raúl", "Irene"];
        const cities = ["Córdoba", "Madrid", "Sevilla", "València", "Bilbao", "Málaga", "Zaragoza", "Murcia"];
        const offers = ["Combo Supremo", "Patatas medianas", "Pack Nuggets Party", "McCafé 2x1"];
        const pick = (arr) => arr[Math.floor(Math.random() * arr.length)];

        function scheduleNext() {
          const delay = 10000 + Math.floor(Math.random() * 5000); // 10–15s
          window.setTimeout(() => {
            const name = pick(names);
            const city = pick(cities);
            const offer = pick(offers);

            showToast(`${name} desde ${city} acaba de canjear ${offer}`, { highlight: city, ttlMs: 7000 });
            scheduleNext();
          }, delay);
        }

        window.setTimeout(scheduleNext, 8000);
      }

      // Init
      setCartCount(0);
      updateResultsLabel(cards.length, cards.length);
      startCountdown();
      hookCTAs();
      hookFilters();
      initScrollReveal();
      startFomoToasts();
    })();
  </script>
</body>
</html>
```