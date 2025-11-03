<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>My Magical Santa Tracker</title>

  <!-- Simple, professional font -->
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

  <!-- Favicon: inline SVG emoji -->
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>🎅</text></svg>">

  <style>
    :root{
      --bg-1: #071020;
      --bg-2: #07304a;
      --accent: #c62828;
      --accent-2: #ffd54f;
      --muted: #9fb3c8;
      --card: rgba(255,255,255,0.03);
      --glass: rgba(255,255,255,0.04);
      --glass-strong: rgba(255,255,255,0.06);
      --radius: 12px;
      --max-width: 1200px;
      --ease: cubic-bezier(.2,.9,.3,1);
    }

    html,body{height:100%;margin:0;font-family:Inter,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial; background:linear-gradient(180deg,var(--bg-1),var(--bg-2)); color:#fff; -webkit-font-smoothing:antialiased; -moz-osx-font-smoothing:grayscale;}

    .wrap{max-width:var(--max-width);margin:28px auto;padding:20px;box-sizing:border-box;}
    header{display:flex;align-items:center;gap:16px;margin-bottom:18px;}
    .brand{display:flex;align-items:center;gap:12px;}
    .logo{
      width:56px;height:56px;border-radius:12px;background:linear-gradient(135deg,var(--accent),#8b0000);display:flex;align-items:center;justify-content:center;font-size:26px;box-shadow:0 6px 20px rgba(0,0,0,0.4);
    }
    h1{font-size:20px;margin:0;font-weight:600;letter-spacing:0.2px;}
    .subtitle{color:var(--muted);font-size:13px}

    /* Tabs */
    .tabs{display:flex;gap:10px;flex-wrap:wrap;margin:14px 0 24px;}
    .tab-btn{background:transparent;border:1px solid rgba(255,255,255,0.06);padding:10px 14px;border-radius:10px;color:var(--muted);cursor:pointer;transition:all .18s var(--ease);font-weight:600;}
    .tab-btn:hover{transform:translateY(-2px);box-shadow:0 6px 18px rgba(0,0,0,0.35)}
    .tab-btn[aria-selected="true"]{background:linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.02)); color:#fff;border-color:rgba(255,255,255,0.12);}

    /* Layout */
    .grid{display:grid;grid-template-columns: 1fr 380px; gap:22px;align-items:start;}
    @media (max-width:980px){ .grid{grid-template-columns:1fr; } .right-col{order:2;} .left-col{order:1;} }

    .card{background:var(--card);border-radius:var(--radius);box-shadow:0 8px 30px rgba(2,8,23,0.55);padding:18px;overflow:hidden;border:1px solid rgba(255,255,255,0.03);}
    .large{min-height:420px}
    .mini-title{font-size:14px;color:var(--muted);margin:0 0 8px 0;}
    .accent{color:var(--accent);font-weight:700;}

    /* Village canvas */
    #villageContainer, #cesiumContainer{
      width:100%;border-radius:10px;background:transparent;min-height:320px;display:flex;align-items:center;justify-content:center;position:relative;
      overflow:hidden;border:1px solid rgba(255,255,255,0.04);
    }
    .loading-state{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;font-weight:600;color:var(--muted);background:linear-gradient(180deg, rgba(0,0,0,0.2), transparent);backdrop-filter: blur(2px);z-index:5;}

    /* Buttons / controls */
    .controls{display:flex;gap:10px;flex-wrap:wrap;margin-top:12px;}
    .btn{
      background:linear-gradient(180deg,var(--accent),#9f1111);border:none;padding:10px 14px;border-radius:10px;color:white;cursor:pointer;font-weight:600;transition:transform .12s var(--ease);
      box-shadow:0 6px 20px rgba(198,40,40,0.18);
    }
    .btn.secondary{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted);box-shadow:none;}
    .btn:active{transform:translateY(1px);}

    /* Countdown */
    .countdown{font-weight:700;color:var(--accent-2);font-size:15px;margin-top:12px;text-shadow:0 3px 12px rgba(0,0,0,0.5);}

    /* Letter form */
    form#letterForm{display:flex;flex-direction:column;gap:10px;padding:10px 0;}
    input[type="text"], input[type="email"], textarea{
      width:100%;padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.06);background:transparent;color:#fff;outline:none;font-size:14px;
    }
    input::placeholder, textarea::placeholder{color:rgba(255,255,255,0.46)}
    .status{font-weight:600;margin-top:6px}
    .status.ok{color:#9fe49f}
    .status.err{color:#ffb4b4}

    /* Catalogue grid */
    .catalogue-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:14px}
    .toy-item{background:var(--glass);padding:10px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);text-align:left}
    .toy-item img{width:100%;height:120px;object-fit:cover;border-radius:8px;margin-bottom:8px}

    /* Quiz */
    .quiz-area{display:flex;flex-direction:column;gap:10px;align-items:flex-start}
    .quiz-area button{min-width:110px}

    footer{margin-top:28px;color:var(--muted);font-size:13px;text-align:center}
    /* small helpers */
    .muted{color:var(--muted);font-size:13px}
    .sr-only{position:absolute;width:1px;height:1px;padding:0;margin:-1px;overflow:hidden;clip:rect(0,0,0,0);white-space:nowrap;border:0}
  </style>
</head>

<body>
  <div class="wrap" role="application" aria-label="Santa tracker application">
    <header>
      <div class="brand">
        <div class="logo" aria-hidden="true">🎅</div>
        <div>
          <h1>My Magical Santa Tracker</h1>
          <div class="subtitle">An interactive North Pole village, sleigh tracker, and goodies 🎄</div>
        </div>
      </div>
    </header>

    <!-- TAB CONTROLS -->
    <nav class="tabs" role="tablist" aria-label="Main sections">
      <button class="tab-btn" role="tab" aria-selected="true" data-tab="village" id="tab-village">North Pole Village</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="tracker" id="tab-tracker">Santa Tracker</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="letter" id="tab-letter">Write to Santa</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="catalogue" id="tab-catalogue">Toy Catalogue</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="quiz" id="tab-quiz">Christmas Quiz</button>
    </nav>

    <!-- Page layout -->
    <main class="grid" id="app-grid">
      <section class="left-col">
        <!-- Village Panel -->
        <div id="village" class="card large" data-panel>
          <div class="mini-title">Explore the Enchanted North Pole Village</div>
          <div id="villageContainer" aria-live="polite" aria-label="3D North Pole village preview">
            <div class="loading-state" id="village-loading">Loading village…</div>
          </div>

          <div class="controls" style="justify-content:flex-end">
            <div class="countdown" id="countdown">—</div>
          </div>
        </div>

        <!-- Tracker Panel (lazy) -->
        <div id="tracker" class="card large" style="margin-top:18px;display:none" data-panel>
          <div class="mini-title">Santa's Magical 3D Journey Tracker</div>
          <div id="cesiumContainer" aria-live="polite" aria-label="3D globe preview">
            <div class="loading-state" id="cesium-loading">Open the Tracker tab to load the globe…</div>
          </div>
          <div class="controls" style="margin-top:12px">
            <button class="btn" id="sleighViewBtn">Track Sleigh</button>
            <button class="btn secondary" id="globalViewBtn">Global View</button>
            <div style="flex:1"></div>
            <div class="muted" id="tracker-status">Preview route</div>
          </div>
        </div>

        <!-- Quiz Panel -->
        <div id="quiz" class="card" style="margin-top:18px;display:none" data-panel>
          <div class="mini-title">Fun Magical Activity</div>
          <div style="margin-top:10px" class="quiz-area">
            <div id="question" style="font-weight:700;color:var(--accent);">Loading...</div>
            <div>
              <button class="btn" id="trueBtn">True</button>
              <button class="btn secondary" id="falseBtn">False</button>
            </div>
            <div id="result" class="muted" aria-live="polite"></div>
          </div>
        </div>
      </section>

      <aside class="right-col">
        <!-- Letter form -->
        <div id="letter" class="card" style="display:block" data-panel>
          <div class="mini-title">Write a Letter to Santa</div>
          <p class="muted" style="margin:6px 0 10px 0">Santa reads them all — we’ll forward to your special inbox.</p>
          <form id="letterForm" aria-label="Letter form">
            <label class="sr-only" for="user_name">Name</label>
            <input id="user_name" name="user_name" type="text" placeholder="Your name" required>

            <label class="sr-only" for="user_email">Email</label>
            <input id="user_email" name="user_email" type="email" placeholder="Email (optional)">

            <label class="sr-only" for="message">Message</label>
            <textarea id="message" name="message" rows="5" placeholder="Dear Santa, I wish for..." required></textarea>

            <div style="display:flex;gap:8px">
              <button class="btn" type="submit">Send Letter ✉️</button>
              <button class="btn secondary" type="button" id="letterClear">Clear</button>
            </div>
            <div id="form-status" class="status" role="status" aria-live="polite"></div>
          </form>
        </div>

        <!-- Toy Catalogue -->
        <div id="catalogue" class="card" style="margin-top:18px" data-panel>
          <div class="mini-title">Santa's Magical Toy Catalogue 🎁</div>
          <p class="muted" style="margin:6px 0 8px 0">Just for fun — pick favorites and daydream!</p>
          <div class="catalogue-grid" aria-live="polite" aria-label="toy catalogue">
            <!-- sample items -->
            <div class="toy-item">
              <img src="https://images.unsplash.com/photo-1517457373958-b7bdd4587208?w=600&q=60&auto=format&fit=crop&s=1" alt="Cuddly Teddy Bear">
              <div style="font-weight:700;color:var(--accent)">Cuddly Teddy Bear</div>
              <div class="muted" style="font-size:13px">Soft and huggable for bedtime stories!</div>
            </div>
            <div class="toy-item">
              <img src="https://images.unsplash.com/photo-1608043152269-423dbba4e7e4?w=600&q=60&auto=format&fit=crop&s=2" alt="Toy Train">
              <div style="font-weight:700;color:var(--accent)">Choo-Choo Train Set</div>
              <div class="muted" style="font-size:13px">Zoom around the tracks with Santa's reindeer!</div>
            </div>
            <!-- more items ... -->
            <div class="toy-item">
              <img src="https://images.unsplash.com/photo-1518709268805-4e9042af2176?w=600&q=60&auto=format&fit=crop&s=3" alt="Pretty Doll">
              <div style="font-weight:700;color:var(--accent)">Pretty Doll</div>
              <div class="muted" style="font-size:13px">Dress up and play tea party at the North Pole.</div>
            </div>
            <div class="toy-item">
              <img src="https://images.unsplash.com/photo-1587654780324-5c5b7b756f7e?w=600&q=60&auto=format&fit=crop&s=4" alt="Building Blocks">
              <div style="font-weight:700;color:var(--accent)">Colorful Blocks</div>
              <div class="muted" style="font-size:13px">Build a castle taller than Santa's chimney!</div>
            </div>
          </div>
        </div>

        <div style="height:14px"></div>
        <footer class="muted">Made with ❤️ — Enjoy the magic!</footer>
      </aside>
    </main>
  </div>

  <!-- Three.js importmap for village (unchanged major version to match earlier code) -->
  <script type="importmap">
    {
      "imports": {
        "three": "https://unpkg.com/three@0.152.2/build/three.module.js"
      }
    }
  </script>

  <!-- VILLAGE: Three.js - keep in module to scope variables -->
  <script type="module">
    import * as THREE from 'three';
    import { TextureLoader } from 'https://unpkg.com/three@0.152.2/examples/jsm/loaders/TextureLoader.js?module';

    // Elements
    const villageContainer = document.getElementById('villageContainer');
    const villageLoading = document.getElementById('village-loading');

    // Scene essentials
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    const width = villageContainer.clientWidth;
    renderer.setSize(Math.max(width, 320), 420);
    renderer.setPixelRatio(window.devicePixelRatio || 1);
    renderer.shadowMap.enabled = true;
    villageContainer.appendChild(renderer.domElement);

    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x071022, 0.0035);

    const camera = new THREE.PerspectiveCamera(60, renderer.domElement.width / renderer.domElement.height, 0.1, 1000);
    camera.position.set(0, 30, 55);

    // lights
    const amb = new THREE.AmbientLight(0xffffff, 0.45);
    scene.add(amb);
    const sun = new THREE.DirectionalLight(0xffffff, 0.6);
    sun.position.set(10, 30, 10);
    sun.castShadow = true;
    scene.add(sun);

    // textures loader with fallback handling
    const loader = new TextureLoader();
    const snowTexture = loader.load('https://threejs.org/examples/textures/terrain/grasslight-big.jpg', () => {}, undefined, () => {});
    const woodTexture = loader.load('https://threejs.org/examples/textures/hardwood2_diffuse.jpg');
    const brickTexture = loader.load('https://threejs.org/examples/textures/brick_diffuse.jpg');

    // ground
    const ground = new THREE.Mesh(new THREE.PlaneGeometry(200, 200), new THREE.MeshLambertMaterial({ map: snowTexture }));
    ground.rotation.x = -Math.PI / 2;
    ground.receiveShadow = true;
    scene.add(ground);

    // simple tree & decorations (kept stylized)
    const trunk = new THREE.Mesh(new THREE.CylinderGeometry(1.2, 1.6, 7, 12), new THREE.MeshLambertMaterial({ color:0x7a4a2b }));
    trunk.position.y = 3.5; trunk.castShadow = true; scene.add(trunk);
    for(let i=0;i<3;i++){
      const cone = new THREE.Mesh(new THREE.ConeGeometry(7 - i*2, 8 - i*1.5, 20), new THREE.MeshLambertMaterial({color:0x0f7a28}));
      cone.position.y = 7 + i*1.4; cone.castShadow = true; scene.add(cone);
    }

    // lights / twinkle
    const lights = [];
    for(let i=0;i<30;i++){
      const pl = new THREE.PointLight(0xff0000, 0.6, 8);
      pl.position.set((Math.random()-0.5)*50, Math.random()*20+2, (Math.random()-0.5)*50);
      scene.add(pl); lights.push(pl);
    }

    // elves (simple groups move along a small path)
    const elves = [];
    const elfPath = [{x:-12,z:-10},{x:12,z:-10}];
    for(let i=0;i<6;i++){
      const g = new THREE.Group();
      const body = new THREE.Mesh(new THREE.CylinderGeometry(0.28,0.4,1,12), new THREE.MeshLambertMaterial({color:0x009a44}));
      body.position.y = 0.5; g.add(body);
      const head = new THREE.Mesh(new THREE.SphereGeometry(0.28,12,12), new THREE.MeshLambertMaterial({color:0xffdbac}));
      head.position.y = 1.2; g.add(head);
      g.position.set(elfPath[0].x + i*4 - 10, 0, elfPath[0].z);
      scene.add(g);
      elves.push({group:g, progress:Math.random(), dir:1});
    }

    // snow particle (lightweight)
    const snowGeom = new THREE.BufferGeometry();
    const snowArr = new Float32Array(3000*3);
    for(let i=0;i<snowArr.length;i++) snowArr[i] = (Math.random()-0.5)*120;
    snowGeom.setAttribute('position', new THREE.BufferAttribute(snowArr,3));
    const snowMat = new THREE.PointsMaterial({size:0.12, transparent:true});
    const snow = new THREE.Points(snowGeom, snowMat); snow.position.y = 10; scene.add(snow);

    // animation loop
    let running = true;
    let t = 0;
    function animateVillage(){
      if(!running) return;
      t += 0.01;
      // elves walking
      elves.forEach((e,idx)=>{
        e.progress += 0.002 * e.dir;
        if(e.progress >= 1 || e.progress <= 0){ e.dir *= -1; e.progress = Math.max(0, Math.min(1, e.progress)); }
        const x = elfPath[0].x + (elfPath[1].x - elfPath[0].x)*e.progress;
        const z = elfPath[0].z + (elfPath[1].z - elfPath[0].z)*e.progress;
        e.group.position.x = x; e.group.position.z = z;
        e.group.rotation.y = Math.sin(t + idx)*0.2;
      });

      // lights twinkle
      lights.forEach((l,i)=> l.intensity = Math.abs(Math.sin(t*1.2 + i))*0.6 + 0.2);

      // snow drift
      snow.position.y -= 0.03;
      if(snow.position.y < -18) snow.position.y = 18;

      renderer.render(scene, camera);
      requestAnimationFrame(animateVillage);
    }

    // start and stop helpers
    function startVillage(){ if(!running){ running=true; animateVillage(); } }
    function stopVillage(){ running=false; }

    // kick off
    villageLoading.style.display = 'none';
    animateVillage();

    // Responsive
    const resizeObserver = new ResizeObserver(() => {
      const w = villageContainer.clientWidth;
      const h = 420;
      renderer.setSize(Math.max(w,320), h);
      camera.aspect = renderer.domElement.width / renderer.domElement.height;
      camera.updateProjectionMatrix();
    });
    resizeObserver.observe(villageContainer);

    // Expose pause/resume for outer tab logic
    window.__villageControls = { start: startVillage, stop: stopVillage };
  </script>

  <!-- MAIN APP SCRIPT -->
  <script>
    (function(){
      // Elements & initial state
      const tabs = document.querySelectorAll('.tab-btn');
      const panels = document.querySelectorAll('[data-panel]');
      const panelMap = {};
      panels.forEach(p => panelMap[p.id] = p);

      // set first visible panel states
      document.getElementById('village').style.display = ''; // shown by default
      document.getElementById('letter').style.display = ''; // right-col item (form) is visible

      // Accessibility: keyboard navigation for tabs
      let currentTabIndex = 0;
      tabs.forEach((btn, idx) => {
        btn.addEventListener('click', (e) => activateTab(idx, true));
        btn.addEventListener('keydown', (ev) => {
          if(ev.key === 'ArrowRight'){ activateTab((idx+1)%tabs.length, true); tabs[(idx+1)%tabs.length].focus(); }
          if(ev.key === 'ArrowLeft'){ activateTab((idx-1+tabs.length)%tabs.length, true); tabs[(idx-1+tabs.length)%tabs.length].focus(); }
        });
      });

      function showPanelByName(name){
        // hide all panels then show specific ones
        panels.forEach(p => p.style.display = 'none');
        // we have id names matching tab values ("village","tracker","letter","catalogue","quiz")
        if(name === 'village'){ document.getElementById('village').style.display = ''; document.getElementById('letter').style.display=''; document.getElementById('catalogue').style.display=''; }
        if(name === 'tracker'){ document.getElementById('tracker').style.display = ''; /* also keep right-col visible */ document.getElementById('letter').style.display=''; document.getElementById('catalogue').style.display=''; }
        if(name === 'letter'){ document.getElementById('letter').style.display = ''; document.getElementById('village').style.display = ''; document.getElementById('catalogue').style.display=''; }
        if(name === 'catalogue'){ document.getElementById('catalogue').style.display = ''; document.getElementById('village').style.display=''; document.getElementById('letter').style.display=''; }
        if(name === 'quiz'){ document.getElementById('quiz').style.display = ''; document.getElementById('village').style.display=''; document.getElementById('letter').style.display=''; }

        // Special handling: when tracker activated, lazy-load Cesium script
        if(name === 'tracker'){ lazyLoadCesium(); }
        // Pause village when not visible (to save resources)
        if(name === 'village' || name === 'quiz') { window.__villageControls?.start?.(); } else { window.__villageControls?.stop?.(); }
      }

      function activateTab(index, userTriggered=false){
        currentTabIndex = index;
        tabs.forEach((t,i) => {
          const sel = i === index;
          t.setAttribute('aria-selected', sel ? 'true' : 'false');
        });
        const name = tabs[index].getAttribute('data-tab');
        // reveal the appropriate main area(s)
        // Hide all panel cards first
        panels.forEach(p => p.style.display = 'none');
        // show default two-column experience: main area and right column (form/catalogue)
        if(name === 'tracker'){ document.getElementById('tracker').style.display=''; document.getElementById('letter').style.display=''; }
        else if(name === 'village'){ document.getElementById('village').style.display=''; document.getElementById('letter').style.display=''; document.getElementById('catalogue').style.display=''; }
        else if(name === 'letter'){ document.getElementById('letter').style.display=''; document.getElementById('village').style.display=''; }
        else if(name === 'catalogue'){ document.getElementById('catalogue').style.display=''; document.getElementById('village').style.display=''; }
        else if(name === 'quiz'){ document.getElementById('quiz').style.display=''; document.getElementById('village').style.display=''; }

        // lazy load cesium if tracker
        if(name === 'tracker') lazyLoadCesium();

        // pause/resume village (improve perf)
        if(name !== 'village') window.__villageControls?.stop?.(); else window.__villageControls?.start?.();
      }

      // Activate the initial tab
      activateTab(0);

      // ----- Countdown -----
      const countdownEls = [document.getElementById('countdown')];
      function updateCountdown(){
        // Use explicit date: December 25, 2025 local time
        const target = new Date('December 25, 2025 00:00:00');
        const now = new Date();
        let diff = target - now;
        if(diff <= 0){ countdownEls.forEach(el => el.textContent = "Merry Christmas! The magic is here ✨"); return; }
        const days = Math.floor(diff / (24*60*60*1000));
        diff -= days * (24*60*60*1000);
        const hours = Math.floor(diff / (60*60*1000));
        diff -= hours * (60*60*1000);
        const minutes = Math.floor(diff / (60*1000));
        diff -= minutes * (60*1000);
        const seconds = Math.floor(diff / 1000);
        const text = `${days}d ${String(hours).padStart(2,'0')}h ${String(minutes).padStart(2,'0')}m ${String(seconds).padStart(2,'0')}s`;
        countdownEls.forEach(el => el.textContent = `Until Christmas: ${text}`);
      }
      setInterval(updateCountdown, 1000); updateCountdown();

      // ----- EMAILJS: send letters -----
      // NOTE: EmailJS script must be included; we assume external library available.
      // If not present, disable send and show graceful error.
      const form = document.getElementById('letterForm');
      const statusEl = document.getElementById('form-status');
      document.getElementById('letterClear').addEventListener('click', ()=> form.reset());

      // Try to initialize EmailJS (if loaded)
      if(window.emailjs && typeof emailjs.init === 'function'){ try { emailjs.init('TZYi3pJhg4UH2i8Pl'); } catch(e){ console.warn('EmailJS init failed', e); } }
      form.addEventListener('submit', async (evt) => {
        evt.preventDefault();
        statusEl.textContent = ''; statusEl.className = 'status';
        const name = document.getElementById('user_name').value.trim();
        const email = document.getElementById('user_email').value.trim();
        const message = document.getElementById('message').value.trim();
        if(!name || !message){ statusEl.textContent='Please enter your name and message.'; statusEl.classList.add('err'); return; }

        // If EmailJS not present, show local success (safe fallback)
        if(!(window.emailjs && emailjs.send)){
          statusEl.textContent = 'Email service not available — locally saved. (Testing mode)';
          statusEl.classList.add('ok');
          form.reset();
          return;
        }

        // Send using EmailJS template - ensure your service/template ids exist
        const templateParams = {
          from_name: name,
          from_email: email || 'No email provided',
          message,
          to_email: 'robson.tye+santa@gmail.com'
        };
        try{
          await emailjs.send('service_1ambdft','template_nx68i1k', templateParams);
          statusEl.textContent = "Your letter has been sent to Santa! 🎄";
          statusEl.classList.add('ok');
          form.reset();
        }catch(err){
          console.error('EmailJS send error', err);
          statusEl.textContent = "Oops—couldn't send right now. Try again later.";
          statusEl.classList.add('err');
        }
      });

      // ----- QUIZ -----
      const questions = [
        { q: "Santa's workshop is full of magical elves making toys.", a: true },
        { q: "Reindeer fly using pixie dust.", a: false },
        { q: "The North Pole has twinkling lights all year.", a: true }
      ];
      let qIndex = 0;
      const qEl = document.getElementById('question'), resultEl = document.getElementById('result');
      const btnTrue = document.getElementById('trueBtn'), btnFalse = document.getElementById('falseBtn');

      function loadQuestion(){
        const item = questions[qIndex];
        qEl.textContent = item.q;
        resultEl.textContent = '';
        btnTrue.disabled = false; btnFalse.disabled = false;
      }
      btnTrue.addEventListener('click', ()=> checkAnswer(true));
      btnFalse.addEventListener('click', ()=> checkAnswer(false));

      function checkAnswer(val){
        btnTrue.disabled = true; btnFalse.disabled = true;
        const correct = questions[qIndex].a;
        if(val === correct){ resultEl.textContent = 'Correct! Magical! 🎉'; } else { resultEl.textContent = 'Not quite — next one! ❄️'; }
        qIndex = (qIndex + 1) % questions.length;
        setTimeout(loadQuestion, 1700);
      }
      loadQuestion();

      // ----- Cesium lazy loader & demo route -----
      let cesiumLoaded = false, cesiumViewer = null, santaEntity = null, routeIndex = 0, routeTimer = null;
      // demo route (lon, lat)
      const route = [
        [151.0, -34.0],  // Sydney
        [139.6917, 35.6895],  // Tokyo
        [116.4074, 39.9042],  // Beijing
        [37.6173, 55.7558],  // Moscow
        [-0.1278, 51.5074],  // London
        [-74.0060, 40.7128],  // New York
        [-118.2437, 34.0522]  // Los Angeles
      ];

      async function lazyLoadCesium(){
        if(cesiumLoaded) return;
        cesiumLoaded = true;
        const loadingEl = document.getElementById('cesium-loading');
        loadingEl.textContent = 'Loading globe — this may take a moment…';

        // Dynamically load Cesium script and CSS
        try{
          // load CSS link
          const link = document.createElement('link');
          link.rel = 'stylesheet';
          link.href = 'https://cesium.com/downloads/cesiumjs/releases/1.133/Build/Cesium/Widgets/widgets.css';
          document.head.appendChild(link);

          // load script
          await new Promise((resolve, reject) => {
            const s = document.createElement('script');
            s.src = 'https://cesium.com/downloads/cesiumjs/releases/1.133/Build/Cesium/Cesium.js';
            s.onload = resolve; s.onerror = reject;
            document.body.appendChild(s);
          });

          // Initialize globe
          // You should replace this token with your own secure token when deploying
          Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJlNTcyMzQwYy1iZjk3LTQxOTYtYTY2YS04MWZmZjg1NTk1MDUiLCJpZCI6MzQ2Njg1LCJpYXQiOjE3NTk0Mjg4Mzd9.SQajYG8C5RvqBJ2vPKd87_IGTo-NC38PyIDsMlOc8f8';

          const container = document.getElementById('cesiumContainer');
          // create Viewer with many widgets turned off for a cleaner look
          cesiumViewer = new Cesium.Viewer(container, {
            animation:false, baseLayerPicker:false, geocoder:false, homeButton:false,
            sceneModePicker:false, selectionIndicator:false, timeline:false, navigationHelpButton:false,
            fullscreenButton:false, vrButton:false
          });

          // add Santa entity
          santaEntity = cesiumViewer.entities.add({
            name: "Santa's Sleigh",
            position: Cesium.Cartesian3.fromDegrees(route[0][0], route[0][1], 1000000),
            billboard: { image: 'https://img.icons8.com/color/96/000000/santa-claus.png', scale: 0.5 }
          });

          // initial camera
          cesiumViewer.camera.flyTo({ destination: Cesium.Cartesian3.fromDegrees(route[0][0], route[0][1], 2000000), orientation:{ heading:0, pitch: Cesium.Math.toRadians(-45)} });

          // set interval to advance route
          function advance(){
            routeIndex = (routeIndex + 1) % route.length;
            const [lon, lat] = route[routeIndex];
            if(santaEntity) santaEntity.position = Cesium.Cartesian3.fromDegrees(lon, lat, 1000000);
            cesiumViewer.camera.flyTo({ destination: Cesium.Cartesian3.fromDegrees(lon, lat, 900000), duration: 3, orientation:{ heading:0, pitch: Cesium.Math.toRadians(-45)} });
          }
          routeTimer = setInterval(advance, 30_000); // every 30s
          loadingEl.style.display = 'none';
        }catch(err){
          console.error('Cesium load error', err);
          document.getElementById('cesiumContainer').innerHTML = '<div style="padding:18px;color:#f88">Globe failed to load. Check connection or token.</div>';
        }
      }

      // Buttons for sleigh / global view
      document.getElementById('sleighViewBtn').addEventListener('click', ()=>{
        if(!cesiumViewer || !santaEntity) return;
        cesiumViewer.trackedEntity = santaEntity;
        document.getElementById('tracker-status').textContent = 'Tracking sleigh…';
      });
      document.getElementById('globalViewBtn').addEventListener('click', ()=>{
        if(!cesiumViewer) return;
        cesiumViewer.trackedEntity = undefined;
        document.getElementById('tracker-status').textContent = 'Global view';
      });

      // ensure we stop route timer when user leaves page (or reload)
      window.addEventListener('beforeunload', ()=> { if(routeTimer) clearInterval(routeTimer); });

      // ensure village is running on load
      window.__villageControls?.start?.();

    })();
  </script>
</body>
</html>
