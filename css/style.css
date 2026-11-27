'use strict';
(() => {
  const $ = id => document.getElementById(id);
  const store = {
    get: k => { try { return sessionStorage.getItem(k); } catch { return null; } },
    set: (k, v) => { try { sessionStorage.setItem(k, v); } catch {} }
  };

  /* ================= sound (synthesised, opt-in, no audio files) ================= */
  let actx = null, soundOn = false;
  function ensureAudio() {
    if (!actx) { const C = window.AudioContext || window.webkitAudioContext; if (!C) return false; actx = new C(); }
    if (actx.state === 'suspended') actx.resume();
    return true;
  }
  function blip(f1, f2, dur, type = 'square', vol = .05) {
    if (!soundOn || !ensureAudio()) return;
    const t = actx.currentTime, o = actx.createOscillator(), g = actx.createGain();
    o.type = type; o.frequency.setValueAtTime(f1, t); o.frequency.exponentialRampToValueAtTime(f2, t + dur);
    g.gain.setValueAtTime(vol, t); g.gain.exponentialRampToValueAtTime(.0001, t + dur);
    o.connect(g).connect(actx.destination); o.start(t); o.stop(t + dur + .02);
  }
  const sfx = {
    click: () => blip(620, 240, .07),
    open: () => { blip(300, 600, .09, 'triangle', .06); },
    close: () => { blip(600, 300, .09, 'triangle', .06); },
    tick: () => blip(900, 700, .03, 'sine', .035)
  };
  const prompt = $('sound-prompt'), toggle = $('sound-toggle');
  function setSound(on, persist = true) {
    soundOn = on;
    toggle.hidden = false; toggle.setAttribute('aria-pressed', String(on)); toggle.textContent = on ? 'sound on' : 'sound off';
    if (persist) store.set('pmw-sound', on ? 'on' : 'off');
    if (on) { ensureAudio(); sfx.open(); }
  }
  const saved = store.get('pmw-sound');
  if (saved === 'on') toggle.hidden = false, toggle.textContent = 'sound off'; // needs a click to resume audio (browser rule)
  else if (saved === 'off') toggle.hidden = false;
  else {
    setTimeout(() => { prompt.hidden = false; }, 1500);
    // if ignored, tuck the question away into the small toggle
    setTimeout(() => { if (!prompt.hidden) { prompt.hidden = true; toggle.hidden = false; } }, 14000);
  }
  $('sound-yes').addEventListener('click', () => { prompt.hidden = true; setSound(true); });
  $('sound-no').addEventListener('click', () => { prompt.hidden = true; setSound(false); });
  toggle.addEventListener('click', () => setSound(!soundOn));
  document.addEventListener('click', e => { if (e.target.closest('a, button') && !e.target.closest('#sound-toggle, #sound-yes')) sfx.click(); });
  let lastTickY = 0;
  addEventListener('scroll', () => { if (Math.abs(scrollY - lastTickY) > 420) { lastTickY = scrollY; sfx.tick(); } }, { passive: true });

  /* ================= mobile menu ================= */
  const menuBtn = document.querySelector('.menu-toggle'), nav = $('navigation');
  const closeMenu = () => { nav.classList.remove('open'); menuBtn.setAttribute('aria-expanded', 'false'); };
  menuBtn.addEventListener('click', () => {
    const open = menuBtn.getAttribute('aria-expanded') !== 'true';
    menuBtn.setAttribute('aria-expanded', String(open)); nav.classList.toggle('open', open);
  });
  nav.querySelectorAll('a').forEach(a => a.addEventListener('click', closeMenu));

  /* ================= hero: cursor image trail ================= */
  // Images live in assets/trail/ named t0.jpg, t1.jpg … Change TRAIL_COUNT when you add more.
  const TRAIL_COUNT = 15;
  const hero = $('home'), trail = $('trail');
  const trailSrc = Array.from({ length: TRAIL_COUNT }, (_, i) => `assets/trail/t${i}.jpg`);
  const trailReady = [];
  // preload after the page loads (keeps the hero fast); files that fail to load are skipped
  addEventListener('load', () => trailSrc.forEach(s => {
    const im = new Image(); im.onload = () => trailReady.push(s); im.src = s;
  }));
  // shuffled order, no repeats until every image has been shown
  let deck = [];
  const nextSrc = () => {
    if (!deck.length) deck = [...trailReady].sort(() => Math.random() - .5);
    return deck.pop();
  };
  let last = null;
  function spawn(x, y) {
    const src = nextSrc(); if (!src) return;
    const el = new Image();
    el.src = src; el.alt = ''; el.className = 'trail-image';
    el.style.left = (x - 75) + 'px'; el.style.top = (y - 100) + 'px';
    trail.appendChild(el);
    const a = el.animate([
      { opacity: .5, transform: 'scale(.5)', filter: 'blur(7px)' },
      { opacity: 1, transform: 'scale(1)', filter: 'blur(0)', offset: .35 },
      { opacity: 0, transform: 'scale(.2)', filter: 'blur(0)' }
    ], { duration: 1100, easing: 'cubic-bezier(.2,.7,.3,1)' });
    a.onfinish = () => el.remove();
  }
  hero.addEventListener('pointermove', e => {
    if (e.pointerType === 'touch') return;
    const r = hero.getBoundingClientRect(), x = e.clientX - r.left, y = e.clientY - r.top;
    if (last && Math.hypot(x - last.x, y - last.y) < 82) return;
    last = { x, y }; spawn(x, y);
  });
  hero.addEventListener('pointerleave', () => { last = null; });
  hero.addEventListener('pointerdown', e => {
    if (e.pointerType !== 'touch') return;
    const r = hero.getBoundingClientRect(); spawn(e.clientX - r.left, e.clientY - r.top);
  });

  /* ================= reels ================= */
  // Video files live in assets/placeholder/ as reel-01.mp4, reel-02.mp4 …
  // handle: the client's Instagram username, shown in the popup (leave '' to hide it)
  // blurb:  one or two lines about the client / what we did (leave '' to hide it)
  // url: null = no Instagram link (offline reel). file: reuse another reel's video.
  const REELS = [
    { n: '01', client: 'Sakina Hatim',     handle: '', blurb: '', url: 'https://www.instagram.com/reel/DWBprWIAEdl/' },
    { n: '02', client: 'Yazad',            handle: '', blurb: '', url: 'https://www.instagram.com/p/DfUfIoRI-L2/' },
    { n: '03', client: 'Crystal',          handle: '', blurb: 'Real estate agent in California.', url: 'https://www.instagram.com/p/DfJCfH8xpIB/' },
    { n: '04', client: 'Harleen',          handle: '', blurb: 'Finance creator. 7K+ followers and weekly consultation sign-ups in 3 months.', url: 'https://www.instagram.com/reel/DTStdrCDZdn/' },
    { n: '05', client: 'Dom',              handle: '', blurb: '', url: 'https://www.instagram.com/p/Deht-AwScQi/' },
    { n: '06', client: 'Selene',           handle: '', blurb: 'Realtor. 10K+ followers and 3× reach vs. high-production content.', url: 'https://www.instagram.com/p/DehTFIwBoU2/' },
    { n: '07', client: 'Harleen',          handle: '', blurb: 'Finance creator. 7K+ followers and weekly consultation sign-ups in 3 months.', url: 'https://www.instagram.com/reel/DTL24ZnkRRu/' },
    { n: '08', client: 'PotatoHead Media', handle: '@potatoheadmedia', blurb: 'Our own channel: a look behind the scenes of how we work.', url: 'https://www.instagram.com/reel/De_JgCbzcyP/' },
    { n: '09', client: 'Bishoy',           handle: '', blurb: '', url: null },
    { n: '10', client: 'PotatoHead Media', handle: '@potatoheadmedia', blurb: 'Our own channel: a look behind the scenes of how we work.', url: 'https://www.instagram.com/reel/DeHXB8Pz5f3/' },
    { n: '11', client: 'Lemonade India',   handle: '', blurb: 'Organic content outperforming paid ads.', url: 'https://www.instagram.com/reel/DTu1-uYkyRx/' },
    { n: '12', client: 'Selene',           handle: '', blurb: 'Realtor. 10K+ followers and 3× reach vs. high-production content.', url: 'https://www.instagram.com/p/DehTFIwBoU2/', file: '06' },
    { n: '13', client: 'Selene',           handle: '', blurb: 'Realtor. 10K+ followers and 3× reach vs. high-production content.', url: 'https://www.instagram.com/reel/Df7WmokM_fR/' },
    { n: '14', client: 'Yazad',            handle: '', blurb: '', url: 'https://www.instagram.com/p/DfwOKkQoaE1/' },
    { n: '15', client: 'Gurleen',          handle: '', blurb: '', url: 'https://www.instagram.com/p/De4Iy2xydtZ/' },
    { n: '16', client: 'Sakina Hatim',     handle: '', blurb: '', url: 'https://www.instagram.com/p/DYXNreIhFGb/' },
    { n: '17', client: 'Yazad',            handle: '', blurb: '', url: 'https://www.instagram.com/p/DgFuADzs3Jk/' },
    { n: '18', client: 'PotatoHead Media', handle: '@potatoheadmedia', blurb: 'Our own channel: a look behind the scenes of how we work.', url: 'https://www.instagram.com/p/DfpLulJMvRR/' }
  ];

  // Which reels sit under each service
  const SERVICES = {
    sv1: { name: 'Branding & Positioning',  reels: ['01', '02', '03'] },
    sv2: { name: 'Scripting & Research',    reels: ['04', '05', '06'] },
    sv3: { name: 'Pre-Production',          reels: ['07', '08', '09'] },
    sv4: { name: 'Post-Production & Edits', reels: ['10', '11', '12'] },
    sv5: { name: 'Distribution',            reels: ['13', '14', '15'] },
    sv6: { name: 'Full Agency Support',     reels: ['16', '17', '18'] }
  };

  const byN = Object.fromEntries(REELS.map(r => [r.n, r]));
  REELS.forEach((r, i) => { r.i = i; r.video = `assets/placeholder/reel-${r.file || r.n}.mp4`; });
  Object.values(SERVICES).forEach(s => s.reels.forEach(n => { byN[n].service = s.name; }));

  // Link to Instagram if there is a URL, otherwise a plain box
  const reelBox = (r, cls, inner) => r.url
    ? `<a class="${cls}" data-i="${r.i}" data-name="${r.client}" href="${r.url}" target="_blank" rel="noopener" aria-label="Watch ${r.client}'s reel on Instagram (opens in a new tab)">${inner}</a>`
    : `<div class="${cls}" data-i="${r.i}" data-name="${r.client}" role="group" aria-label="${r.client}'s reel">${inner}</div>`;

  // Work section: every unique reel, playing directly
  const reelGroup = $('reel-group');
  REELS.filter(r => !r.file).forEach(r => {
    const fig = document.createElement('figure'); fig.className = 'reel';
    fig.innerHTML =
      reelBox(r, 'reel-btn', `<video src="${r.video}#t=0.1" muted loop playsinline autoplay preload="metadata"></video>`) +
      `<figcaption><span>${r.service}</span><strong>${r.client}</strong></figcaption>`;
    reelGroup.appendChild(fig);
  });

  // Services: three reels under each service
  Object.entries(SERVICES).forEach(([id, s]) => {
    const box = document.querySelector(`#${id} .thumbs`); if (!box) return;
    box.innerHTML = s.reels.map(n => {
      const r = byN[n];
      return reelBox(r, 'thumb', `<video src="${r.video}#t=0.1" muted loop playsinline preload="metadata"></video>${r.url ? '<span>▶ watch</span>' : ''}`);
    }).join('');
  });

  /* ================= shared marquee: clone group for a seamless loop ================= */
  const marquees = [...document.querySelectorAll('.marquee')];
  marquees.forEach(m => {
    const gA = m.querySelector('.group'), gB = gA.cloneNode(true);
    gB.removeAttribute('id'); gB.setAttribute('aria-hidden', 'true');
    gB.querySelectorAll('button, a').forEach(b => { b.tabIndex = -1; });
    gB.querySelectorAll('img').forEach(i => { i.alt = ''; });
    gA.parentNode.appendChild(gB);
  });
  function setSpeeds() {
    marquees.forEach(m => {
      const g = m.querySelector('.group'), pps = Number(m.dataset.pps) || 45;
      m.querySelector('.track').style.setProperty('--marquee-time', Math.max(25, g.offsetWidth / pps) + 's');
    });
  }
  setSpeeds(); addEventListener('resize', setSpeeds); addEventListener('load', setSpeeds);

  /* ================= reel marquee: JS-driven so viewers can scrub it ================= */
  const reelMarquee = $('marquee'), reelTrack = reelMarquee.querySelector('.track'), scrub = $('reel-scrub');
  const reelState = { pos: 0, half: 0, last: 0, hover: false, holdUntil: 0, pps: Number(reelMarquee.dataset.pps) || 45 };
  const measureReels = () => { reelState.half = reelMarquee.querySelector('.group').offsetWidth; };
  measureReels(); addEventListener('resize', measureReels); addEventListener('load', measureReels);
  const setReelPos = p => {
    const h = reelState.half || 1;
    reelState.pos = ((p % h) + h) % h; // loop seamlessly
    reelTrack.style.transform = `translate3d(${-reelState.pos}px,0,0)`;
    if (scrub) scrub.value = Math.round(reelState.pos / h * 1000);
  };
  const holdReels = () => { reelState.holdUntil = performance.now() + 2500; }; // resume 2.5s after the viewer stops
  reelMarquee.addEventListener('pointerenter', () => { reelState.hover = true; });
  reelMarquee.addEventListener('pointerleave', () => { reelState.hover = false; });
  if (scrub) scrub.addEventListener('input', () => { holdReels(); setReelPos(scrub.value / 1000 * reelState.half); });
  // sideways trackpad swipe over the reels moves them; vertical scrolling still scrolls the page
  reelMarquee.addEventListener('wheel', e => {
    if (Math.abs(e.deltaX) <= Math.abs(e.deltaY)) return;
    e.preventDefault(); holdReels(); setReelPos(reelState.pos + e.deltaX);
  }, { passive: false });
  function reelTick(t) {
    const dt = Math.min((t - (reelState.last || t)) / 1000, .05); reelState.last = t;
    if (!reelState.hover && t > reelState.holdUntil && !document.hidden) setReelPos(reelState.pos + reelState.pps * dt);
    requestAnimationFrame(reelTick);
  }
  requestAnimationFrame(reelTick);

  // play videos only while visible; if a video file is missing, show the client name instead of a black box
  const vio = new IntersectionObserver(es => es.forEach(e => {
    e.isIntersecting ? e.target.play().catch(() => {}) : e.target.pause();
  }));
  document.querySelectorAll('.reel-btn video, .thumb video').forEach(v => {
    vio.observe(v);
    v.addEventListener('error', () => v.parentElement.classList.add('no-video'), { once: true });
  });

  /* ---- hover card: pops up to the LEFT of a reel (flips right if there is no room) ---- */
  const tip = document.createElement('aside');
  tip.className = 'reel-tip'; tip.setAttribute('aria-hidden', 'true');
  tip.innerHTML = '<p class="rt-eyebrow"></p><h3 class="rt-title"></h3><p class="rt-handle"></p><p class="rt-blurb"></p><ul class="rt-tags"></ul><span class="rt-cta">Want one like this? <b>→</b></span><span class="rt-ig">click to open on Instagram ↗</span>';
  document.body.appendChild(tip);
  const tq = s => tip.querySelector(s);
  let hoverReel = false; // auto-scroll waits while a reel card is open
  function showTip(el) {
    const r = REELS[el.dataset.i]; if (!r) return;
    tq('.rt-eyebrow').textContent = r.service || '';
    tq('.rt-title').textContent = r.client;
    tq('.rt-handle').textContent = r.handle || ''; tq('.rt-handle').hidden = !r.handle;
    tq('.rt-blurb').textContent = r.blurb || ''; tq('.rt-blurb').hidden = !r.blurb;
    tq('.rt-tags').replaceChildren(...[r.service].filter(Boolean).map(t => { const li = document.createElement('li'); li.textContent = t; return li; }));
    tq('.rt-ig').hidden = !r.url;
    const b = el.getBoundingClientRect(), w = tip.offsetWidth, gap = 16;
    let left = b.left - w - gap, side = 'left';
    if (left < 12) { left = b.right + gap; side = 'right'; }
    left = Math.min(left, innerWidth - w - 12);
    const top = Math.max(12, Math.min(b.top, innerHeight - tip.offsetHeight - 12));
    tip.style.left = left + 'px'; tip.style.top = top + 'px';
    tip.dataset.side = side; hoverReel = true; tip.classList.add('show');
  }
  const hideTip = () => { hoverReel = false; tip.classList.remove('show'); };
  document.addEventListener('pointerover', e => {
    if (e.pointerType !== 'mouse') return;
    const el = e.target.closest('.reel-btn, .thumb[data-i]'); el ? showTip(el) : hideTip();
  });
  document.addEventListener('pointerleave', hideTip);
  addEventListener('wheel', hideTip, { passive: true });

  /* ================= carousel wall: JS-driven, direction follows page scroll =================
     cols 1 & 3 travel top→bottom, col 2 bottom→top while the page scrolls down;
     everything reverses while the page scrolls up. */
  const wall = document.querySelector('.wall');
  const cols = [...document.querySelectorAll('.wall-col')].map((col, i) => {
    const a = col.querySelector('.wall-set'), b = a.cloneNode(true);
    b.setAttribute('aria-hidden', 'true'); b.querySelectorAll('a').forEach(l => { l.tabIndex = -1; });
    b.querySelectorAll('img').forEach(im => { im.alt = ''; });
    const strip = document.createElement('div'); strip.className = 'wall-set';
    strip.append(...a.children, ...b.children);
    a.replaceWith(strip);
    return { strip, dir: i === 1 ? -1 : 1, speed: i === 1 ? 46 : i === 2 ? 34 : 40, y: 0, half: 0 };
  });
  let wallDir = 1, wallVis = false, wallHover = false, wallLast = 0, lastY = scrollY, wallBoost = 0;
  const measure = () => cols.forEach(c => { c.half = c.strip.scrollHeight / 2; if (c.dir > 0 && !c.y) c.y = -c.half; });
  measure(); addEventListener('load', measure); addEventListener('resize', measure);
  if (wall) {
    new IntersectionObserver(en => { wallVis = en[0].isIntersecting; }, { threshold: 0 }).observe(wall);
    wall.addEventListener('pointerenter', () => { wallHover = true; });
    wall.addEventListener('pointerleave', () => { wallHover = false; });
  }
  addEventListener('scroll', () => {
    const d = scrollY - lastY;
    if (Math.abs(d) > 0.5) { wallDir = d > 0 ? 1 : -1; wallBoost = Math.min(Math.abs(d), 60); }
    lastY = scrollY;
  }, { passive: true });
  let wallDirSmooth = 1;
  function wallTick(t) {
    const dt = Math.min((t - (wallLast || t)) / 1000, 0.05); wallLast = t;
    wallDirSmooth += (wallDir - wallDirSmooth) * Math.min(1, dt * 6); // smooth reversal
    wallBoost *= 0.92;
    if (wallVis && !wallHover) cols.forEach(c => {
      if (!c.half) c.half = c.strip.scrollHeight / 2;
      c.y += c.dir * wallDirSmooth * (c.speed + wallBoost * 3) * dt;
      if (c.y >= 0) c.y -= c.half; else if (c.y <= -c.half) c.y += c.half;
      c.strip.style.transform = `translate3d(0,${c.y.toFixed(2)}px,0)`;
    });
    requestAnimationFrame(wallTick);
  }
  requestAnimationFrame(wallTick);

  /* ================= services: hover to preview, click to keep open ================= */
  const canHover = matchMedia('(hover: hover)').matches;
  let peekY = 0, lastUserScroll = 0;
  const peeking = () => !!document.querySelector('.service.peek');
  document.querySelectorAll('.service').forEach(item => {
    const btn = item.querySelector('.service-head');
    btn.addEventListener('click', () => {
      const open = btn.getAttribute('aria-expanded') !== 'true';
      btn.setAttribute('aria-expanded', String(open));
      if (!open) { item.classList.remove('peek'); item.dataset.hold = '1'; } // closed by click: stays shut until the mouse leaves
      open ? sfx.open() : sfx.close();
    });
    if (!canHover) return;
    // open only on a real mouse movement over the title, and not while the page is being scrolled
    // (stops titles that slide under a still cursor from flickering open and shut)
    btn.addEventListener('pointermove', e => {
      if (e.pointerType !== 'mouse' || item.classList.contains('peek') || item.dataset.hold) return;
      if (btn.getAttribute('aria-expanded') === 'true') return;
      if (performance.now() - lastUserScroll < 350) return;
      document.querySelectorAll('.service.peek').forEach(s => { if (s !== item) s.classList.remove('peek'); });
      item.classList.add('peek'); peekY = scrollY;
    });
    item.addEventListener('pointerleave', () => { item.classList.remove('peek'); delete item.dataset.hold; });
  });
  // scrolling closes a hover preview (clicked ones stay open)
  addEventListener('scroll', () => {
    if (!auto.on) lastUserScroll = performance.now();
    if (Math.abs(scrollY - peekY) < 40) return;
    document.querySelectorAll('.service.peek').forEach(s => s.classList.remove('peek'));
  }, { passive: true });

  /* ================= YouTube (inline once a video ID is set via data-yt) ================= */
  document.querySelectorAll('.yt').forEach(a => a.addEventListener('click', e => {
    const id = (a.dataset.yt || '').trim(); if (!id) return; // no ID yet: normal link
    e.preventDefault();
    const f = document.createElement('iframe');
    f.src = `https://www.youtube-nocookie.com/embed/${encodeURIComponent(id)}?autoplay=1&rel=0`;
    f.allow = 'autoplay; encrypted-media; picture-in-picture'; f.allowFullscreen = true; f.title = a.getAttribute('aria-label') || 'YouTube video';
    a.replaceChildren(f); a.removeAttribute('href');
  }));

  /* ================= chat widget ================= */
  const chat = $('chat'), launch = $('chat-launch');
  function setChat(open) {
    chat.hidden = !open; launch.setAttribute('aria-expanded', String(open)); open ? sfx.open() : sfx.close();
  }
  launch.addEventListener('click', () => setChat(chat.hidden));
  $('chat-close').addEventListener('click', () => { setChat(false); launch.focus({ preventScroll: true }); });
  chat.querySelectorAll('a[href^="#"]').forEach(a => a.addEventListener('click', () => setChat(false)));
  document.addEventListener('keydown', e => {
    if (e.key !== 'Escape') return;
    closeMenu(); if (!chat.hidden) setChat(false);
  });

  /* ================= auto-scroll: the page scrolls itself; any input pauses it ================= */
  const autoBtn = $('auto-toggle');
  const auto = { on: false, pos: 0, last: 0, speed: 80, userOff: store.get('pmw-auto') === 'off', timer: null };
  const maxY = () => document.documentElement.scrollHeight - innerHeight;
  const blocked = () => document.hidden || hoverReel || !chat.hidden || peeking();
  function label() { autoBtn.hidden = false; autoBtn.setAttribute('aria-pressed', String(auto.on)); autoBtn.textContent = auto.on ? 'auto-scroll on' : 'auto-scroll off'; }
  function step(t) {
    if (!auto.on) return;
    const dt = Math.min(.05, (t - auto.last) / 1000); auto.last = t;
    if (!blocked()) {
      if (Math.abs(scrollY - auto.pos) > 3) auto.pos = scrollY; // something else moved the page: follow it
      auto.pos += auto.speed * dt;
      scrollTo({ top: auto.pos, behavior: 'instant' });
      if (auto.pos >= maxY() - 1) { stopAuto(); return; }
    } else auto.pos = scrollY;
    requestAnimationFrame(step);
  }
  function startAuto() { if (auto.on) return; clearTimeout(auto.timer); auto.on = true; auto.pos = scrollY; auto.last = performance.now(); label(); requestAnimationFrame(step); }
  function stopAuto() { auto.on = false; label(); }
  function interrupted() { // user took over
    if (!auto.on) return; stopAuto();
    if (!auto.userOff) { clearTimeout(auto.timer); auto.timer = setTimeout(() => { if (!auto.userOff && scrollY < maxY() - 4) startAuto(); }, 8000); }
  }
  ['wheel', 'touchstart', 'keydown', 'pointerdown'].forEach(ev => addEventListener(ev, e => {
    if (e.target && e.target.closest && e.target.closest('#auto-toggle')) return;
    if (ev === 'keydown' && !['ArrowDown', 'ArrowUp', 'PageDown', 'PageUp', 'Home', 'End', ' ', 'Tab'].includes(e.key)) return;
    interrupted();
  }, { passive: true }));
  autoBtn.addEventListener('click', () => {
    if (auto.on) { auto.userOff = true; store.set('pmw-auto', 'off'); clearTimeout(auto.timer); stopAuto(); }
    else { auto.userOff = false; store.set('pmw-auto', 'on'); if (scrollY >= maxY() - 4) scrollTo({ top: 0, behavior: 'instant' }); startAuto(); }
  });
  label();
  if (!auto.userOff) setTimeout(() => { if (scrollY < maxY() - 4) startAuto(); }, 2800);

  $('year').textContent = String(new Date().getFullYear());
})();
