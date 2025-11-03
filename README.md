<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8"/>
  <meta name="viewport" content="width=device-width,initial-scale=1"/>
  <title>My Magical Santa Tracker — Village (Version B)</title>

  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

  <style>
    :root{
      --bg-1:#fff; --bg-2:#f2f6f9; --accent:#c62828; --accent-2:#0b653b; --muted:#6b7885;
      --card: #ffffff; --radius:14px; --max-width:1200px; --ease: cubic-bezier(.2,.9,.3,1);
    }
    /* Page + layout */
    html,body{height:100%;margin:0;font-family:Inter,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial;background:linear-gradient(180deg,var(--bg-1),var(--bg-2));color:#0b2233;}
    .wrap{max-width:var(--max-width);margin:26px auto;padding:18px;box-sizing:border-box;}
    header{display:flex;align-items:center;gap:14px;margin-bottom:14px;}
    .logo{width:64px;height:64px;border-radius:12px;background:linear-gradient(135deg,var(--accent),#8b0000);display:flex;align-items:center;justify-content:center;color:white;font-size:30px;box-shadow:0 8px 30px rgba(0,0,0,0.08)}
    h1{margin:0;font-size:20px;color:#10232b}
    .subtitle{color:var(--muted);font-size:13px}

    /* Tabs */
    .tabs{display:flex;gap:8px;flex-wrap:wrap;margin:14px 0 20px;}
    .tab-btn{background:transparent;border:1px solid rgba(10,20,25,0.06);padding:9px 12px;border-radius:10px;color:var(--muted);cursor:pointer;transition:all .14s var(--ease);font-weight:600}
    .tab-btn[aria-selected="true"]{background:linear-gradient(180deg,rgba(198,40,40,0.08),rgba(198,40,40,0.02));color:#111}

    /* page grid */
    .grid{display:grid;grid-template-columns:1fr 380px;gap:18px;align-items:start}
    @media (max-width:980px){ .grid{grid-template-columns:1fr} .right-col{order:2} .left-col{order:1} }

    .card{background:var(--card);border-radius:var(--radius);box-shadow:0 8px 28px rgba(11,34,45,0.06);padding:16px;overflow:hidden;border:1px solid rgba(10,20,25,0.03)}
    .large{min-height:420px}
    .mini-title{font-size:13px;color:var(--muted);margin:0 0 8px 0}
    .muted{color:var(--muted)}

    /* village canvas */
    #villageContainer{width:100%;height:420px;border-radius:10px;overflow:hidden;background:linear-gradient(180deg,#bfe6ff,#ffffff);position:relative;display:flex;align-items:center;justify-content:center}
    .loading-state{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;font-weight:700;color:var(--muted);backdrop-filter: blur(2px);z-index:5}

    /* controls */
    .controls{display:flex;gap:10px;flex-wrap:wrap;margin-top:12px;align-items:center}
    .btn{background:var(--accent);color:white;padding:9px 12px;border-radius:10px;border:none;cursor:pointer;font-weight:700}
    .btn.secondary{background:transparent;border:1px solid rgba(10,20,25,0.06);color:var(--muted)}

    /* right column forms/catalogue */
    form#letterForm{display:flex;flex-direction:column;gap:10px;padding:6px 0;}
    input,textarea{width:100%;padding:10px;border-radius:8px;border:1px solid rgba(10,20,25,0.06);background:transparent;outline:none}
    .catalogue-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:12px;margin-top:8px}
    .toy-item{background:linear-gradient(180deg, rgba(255,255,255,0.7), rgba(250,250,250,0.85));padding:10px;border-radius:10px;border:1px solid rgba(0,0,0,0.03)}

    footer{margin-top:18px;color:var(--muted);font-size:13px;text-align:center}
    .small{font-size:13px;color:var(--muted)}
    .toggle{display:inline-flex;align-items:center;gap:8px}
  </style>
</head>
<body>
  <div class="wrap" role="application" aria-label="Santa tracker app">
    <header>
      <div class="logo" aria-hidden="true">🎅</div>
      <div>
        <h1>My Magical Santa Tracker</h1>
        <div class="subtitle">Cozy village, write to Santa, and holiday fun</div>
      </div>
    </header>

    <nav class="tabs" role="tablist" aria-label="Main sections">
      <button class="tab-btn" role="tab" aria-selected="true" data-tab="village" id="tab-village">North Pole Village</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="tracker" id="tab-tracker">Santa Tracker</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="letter" id="tab-letter">Write to Santa</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="catalogue" id="tab-catalogue">Toy Catalogue</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="quiz" id="tab-quiz">Christmas Quiz</button>
    </nav>

    <main class="grid">
      <section class="left-col">
        <div id="village" class="card large" data-panel>
          <div class="mini-title">Explore the Enchanted North Pole Village</div>

          <div id="villageContainer" aria-live="polite" aria-label="3D village scene">
            <div class="loading-state" id="village-loading">Loading village…</div>
            <!-- Three.js canvas appended here -->
          </div>

          <div class="controls" style="justify-content:space-between">
            <div class="small">Theme: <strong style="color:var(--accent)">North Pole — warm & bright</strong></div>
            <div style="display:flex;gap:8px;align-items:center">
              <label class="toggle small"><input id="reduceSnow" type="checkbox"> Reduce snow</label>
              <button class="btn" id="resetCam">Reset View</button>
            </div>
          </div>
        </div>

        <!-- Tracker + Quiz (kept from previous structure, minimal here) -->
        <div id="tracker" class="card large" style="margin-top:16px;display:none" data-panel>
          <div class="mini-title">Santa's 3D Journey Tracker</div>
          <div style="height:260px;border-radius:8px;border:1px solid rgba(0,0,0,0.04);display:flex;align-items:center;justify-content:center;color:var(--muted)">Tracker placeholder (Cesium optional)</div>
        </div>

        <div id="quiz" class="card" style="margin-top:16px;display:none" data-panel>
          <div class="mini-title">Christmas Quiz</div>
          <div>
            <div id="question" style="font-weight:700;color:var(--accent);">Loading...</div>
            <div style="margin-top:8px">
              <button class="btn" id="trueBtn">True</button>
              <button class="btn secondary" id="falseBtn">False</button>
            </div>
            <div id="result" style="margin-top:8px" class="muted"></div>
          </div>
        </div>
      </section>

      <aside class="right-col">
        <div id="letter" class="card" data-panel>
          <div class="mini-title">Write a Letter to Santa</div>
          <p class="muted">Santa reads them all — we'll forward to your special inbox.</p>
          <form id="letterForm" aria-label="Letter form">
            <input id="user_name" name="user_name" placeholder="Your name" required>
            <input id="user_email" name="user_email" type="email" placeholder="Email (optional)">
            <textarea id="message" name="message" rows="5" placeholder="Dear Santa, I wish for..." required></textarea>
            <div style="display:flex;gap:8px">
              <button class="btn" type="submit">Send Letter ✉️</button>
              <button class="btn secondary" id="letterClear" type="button">Clear</button>
            </div>
            <div id="form-status" class="small muted" style="margin-top:8px"></div>
          </form>
        </div>

        <div id="catalogue" class="card" style="margin-top:16px" data-panel>
          <div class="mini-title">Santa's Magical Toy Catalogue 🎁</div>
          <div class="catalogue-grid">
            <div class="toy-item"><img src="https://images.unsplash.com/photo-1517457373958-b7bdd4587208?w=600&q=60&auto=format&fit=crop" style="width:100%;height:110px;object-fit:cover;border-radius:8px"><div style="font-weight:700;margin-top:8px">Cuddly Teddy</div><div class="muted">Soft and huggable</div></div>
            <div class="toy-item"><img src="https://images.unsplash.com/photo-1608043152269-423dbba4e7e4?w=600&q=60&auto=format&fit=crop" style="width:100%;height:110px;object-fit:cover;border-radius:8px"><div style="font-weight:700;margin-top:8px">Train Set</div><div class="muted">Zoom around the tracks</div></div>
          </div>
        </div>

        <footer style="margin-top:12px" class="muted">Made with ❤️ — Drop your village.glb into <code>/models/village.glb</code> for best reliability.</footer>
      </aside>
    </main>
  </div>

  <!-- importmap -->
  <script type="importmap">
    {
      "imports": {
        "three": "https://unpkg.com/three@0.152.2/build/three.module.js",
        "three/examples/": "https://unpkg.com/three@0.152.2/examples/jsm/"
      }
    }
  </script>

  <!-- Three.js scene (glTF + forest + snow) -->
  <script type="module">
    import * as THREE from 'three';
    import { GLTFLoader } from 'three/examples/loaders/GLTFLoader.js';
    import { OrbitControls } from 'three/examples/controls/OrbitControls.js';
    import { DRACOLoader } from 'three/examples/loaders/DRACOLoader.js';

    // Elements
    const container = document.getElementById('villageContainer');
    const loadingEl = document.getElementById('village-loading');
    const reduceSnowCheckbox = document.getElementById('reduceSnow');
    const resetCamBtn = document.getElementById('resetCam');

    // Renderer
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2));
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    container.appendChild(renderer.domElement);

    // Scene & Camera
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0xbfe9ff); // bright cheerful sky backdrop
    scene.fog = new THREE.Fog(0xbfe9ff, 80, 220);

    const camera = new THREE.PerspectiveCamera(50, 1, 0.1, 1000);
    camera.position.set(0, 18, 68);

    // Lights (match NorthPole bright, warm theme)
    const hemi = new THREE.HemisphereLight(0xffffff, 0x444444, 0.6);
    scene.add(hemi);
    const dir = new THREE.DirectionalLight(0xffffff, 0.9);
    dir.position.set(30, 60, 10);
    dir.castShadow = true;
    dir.shadow.camera.left = -120; dir.shadow.camera.right = 120; dir.shadow.camera.top = 120; dir.shadow.camera.bottom = -120;
    dir.shadow.mapSize.set(2048,2048);
    scene.add(dir);

    // Soft fill from below for snowy reflection
    const groundFill = new THREE.PointLight(0xffffff, 0.12, 120);
    groundFill.position.set(0, 10, 0);
    scene.add(groundFill);

    // Ground plane (snow)
    const groundGeo = new THREE.PlaneGeometry(800, 800, 4, 4);
    const groundMat = new THREE.MeshStandardMaterial({ color: 0xf6fbff, roughness: 0.95, metalness: 0 });
    const ground = new THREE.Mesh(groundGeo, groundMat);
    ground.rotation.x = -Math.PI / 2;
    ground.receiveShadow = true;
    scene.add(ground);

    // Renderer resize
    function resize() {
      const w = container.clientWidth;
      const h = container.clientHeight;
      renderer.setSize(w, h);
      camera.aspect = w / h;
      camera.updateProjectionMatrix();
    }
    window.addEventListener('resize', resize);
    resize();

    // Controls
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.08;
    controls.minDistance = 18;
    controls.maxDistance = 220;
    controls.maxPolarAngle = Math.PI * 0.48;

    // Loaders (DRACO for compressed glb if available)
    const draco = new DRACOLoader();
    draco.setDecoderPath('https://www.gstatic.com/draco/v1/decoders/'); // CDN
    const gltfLoader = new GLTFLoader();
    gltfLoader.setDRACOLoader(draco);

    // Public fallback model URL (try this first). This can be replaced with any public glb.
    // Note: For guaranteed reliability on GitHub Pages, place a file at /models/village.glb in your repo.
    const PUBLIC_GLTF_URL = 'https://nsn3d.com/low_poly_village1.glb'; // public fallback (may be slow or unavailable)
    const LOCAL_GLTF_PATH = '/models/village.glb'; // recommended: put your model here in repo

    // Scene groups
    const villageGroup = new THREE.Group();
    villageGroup.traverse = villageGroup.traverse || function(fn){ for(const c of this.children) fn(c); }; // safe traverse
    scene.add(villageGroup);

    // ---- Instanced forest (performance friendly) ----
    let treeInstanced;
    function buildForest(count = 220) {
      if(treeInstanced) {
        scene.remove(treeInstanced);
        treeInstanced.dispose?.();
      }
      const coneGeo = new THREE.ConeGeometry(1.6, 5, 8);
      const coneMat = new THREE.MeshStandardMaterial({ color: 0x0f6f2a, roughness: 0.95 });
      treeInstanced = new THREE.InstancedMesh(coneGeo, coneMat, count);
      treeInstanced.instanceMatrix.setUsage(THREE.DynamicDrawUsage);
      const dummy = new THREE.Object3D();
      for(let i=0;i<count;i++){
        const radius = 40 + Math.random()*120;
        const theta = Math.random()*Math.PI*2;
        dummy.position.set(Math.cos(theta)*radius, 0, Math.sin(theta)*radius);
        const s = 0.8 + Math.random()*1.5;
        dummy.scale.setScalar(s);
        dummy.rotation.y = Math.random()*Math.PI*2;
        dummy.updateMatrix();
        treeInstanced.setMatrixAt(i, dummy.matrix);
      }
      treeInstanced.frustumCulled = false;
      scene.add(treeInstanced);
    }

    // Build large forest (adjusted later for mobile)
    const initialTreeCount = Math.max(80, Math.min(400, Math.floor(window.innerWidth / 4)));
    buildForest(initialTreeCount);

    // ---- Snow particles ----
    let snowPoints;
    const SNOW_BASE = 3000;
    function buildSnow(reduced=false) {
      if(snowPoints) { scene.remove(snowPoints); snowPoints.geometry.dispose?.(); snowPoints.material.dispose?.(); }
      const count = reduced ? Math.floor(SNOW_BASE * 0.28) : SNOW_BASE;
      const snowGeo = new THREE.BufferGeometry();
      const pos = new Float32Array(count*3);
      const vel = new Float32Array(count);
      for(let i=0;i<count;i++){
        pos[i*3+0] = (Math.random()-0.5) * 500;
        pos[i*3+1] = Math.random()*120 + 10;
        pos[i*3+2] = (Math.random()-0.5) * 500;
        vel[i] = 0.2 + Math.random()*0.6;
      }
      snowGeo.setAttribute('position', new THREE.BufferAttribute(pos, 3));
      snowGeo.setAttribute('velocity', new THREE.BufferAttribute(vel, 1));
      const spriteUrl = 'https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/textures/sprites/snowflake1.png';
      const sprite = new THREE.TextureLoader().load(spriteUrl);
      const mat = new THREE.PointsMaterial({ size: 0.9, map: sprite, transparent: true, depthWrite: false, opacity: 0.9 });
      snowPoints = new THREE.Points(snowGeo, mat);
      snowPoints.frustumCulled = false;
      scene.add(snowPoints);
    }

    // ---- Chimney smoke (small particle system) ----
    let smokePoints;
    function buildSmoke() {
      if(smokePoints){ scene.remove(smokePoints); smokePoints.geometry.dispose?.(); smokePoints.material.dispose?.(); }
      const S = 80;
      const g = new THREE.BufferGeometry();
      const pos = new Float32Array(S*3);
      const vel = new Float32Array(S*3);
      for(let i=0;i<S;i++){
        pos[i*3+0] = (Math.random()-0.5)*2;
        pos[i*3+1] = 6 + Math.random()*2;
        pos[i*3+2] = (Math.random()-0.5)*2;
        vel[i*3+0] = (Math.random()-0.5)*0.02;
        vel[i*3+1] = 0.02 + Math.random()*0.02;
        vel[i*3+2] = (Math.random()-0.5)*0.02;
      }
      g.setAttribute('position', new THREE.BufferAttribute(pos, 3));
      g.setAttribute('velocity', new THREE.BufferAttribute(vel, 3));
      const m = new THREE.PointsMaterial({ size: 2.0, color: 0xcccccc, transparent: true, opacity: 0.22, depthWrite: false });
      smokePoints = new THREE.Points(g, m);
      smokePoints.frustumCulled = false;
      smokePoints.position.set(2,0,-1);
      scene.add(smokePoints);
    }

    // Build initial environmental systems
    const smallScreen = window.innerWidth < 720 || navigator.userAgent.match(/Mobi/i);
    buildSnow(smallScreen);
    buildSmoke();

    // ---- Model load logic: try public, then local fallback ----
    let modelLoaded = false;

    function tryLoadModel(url) {
      return new Promise((resolve, reject) => {
        gltfLoader.load(url, gltf => resolve(gltf), undefined, err => reject(err));
      });
    }

    async function loadVillageModel() {
      loadingEl.style.display = '';
      // Try public remote first (if available)
      const tryUrls = [PUBLIC_GLTF_URL, LOCAL_GLTF_PATH];
      for(const url of tryUrls){
        try {
          // attempt to load (if 404 or CORS fail, we catch)
          const gltf = await tryLoadModel(url);
          handleGltf(gltf, url);
          return;
        } catch (err) {
          console.warn('Model load failed for', url, err && err.message ? err.message : err);
        }
      }
      // If we reach here, loading failed.
      loadingEl.textContent = 'Village model failed to load. Please add /models/village.glb to your repo.';
    }

    // Place the loaded glTF into the scene with polished settings
    function handleGltf(gltf, srcUrl) {
      // Clear prior village
      while(villageGroup.children.length) villageGroup.remove(villageGroup.children[0]);
      const root = gltf.scene || gltf.scenes[0];
      root.traverse(n => {
        if(n.isMesh) {
          n.castShadow = true;
          n.receiveShadow = true;
          n.material.side = THREE.FrontSide;
          // tweak material for brighter windows if emissive map not set
          if(n.material && n.material.emissive) n.material.emissiveIntensity = n.material.emissiveIntensity || 1.0;
        }
      });

      // Scale & center model for consistent framing
      const box = new THREE.Box3().setFromObject(root);
      const size = new THREE.Vector3();
      box.getSize(size);
      const maxDim = Math.max(size.x, size.y, size.z);
      const desired = 28; // fit into view
      const scale = desired / maxDim;
      root.scale.setScalar(scale);
      // center
      const center = new THREE.Vector3();
      box.getCenter(center);
      root.position.sub(center.multiplyScalar(scale));
      root.position.y += 0.5; // sit on snow plane

      // add a soft ambient light near village
      const amb = new THREE.PointLight(0xfff0d1, 0.12, 200);
      amb.position.set(0, 10, 0);
      villageGroup.add(amb);

      villageGroup.add(root);

      // If model contains named chimney positions (optional), attach small smoke emitters.
      // For a generic fallback we'll add one emitter next to model center.
      buildSmoke(); // rebuild smoke so points are available
      smokePoints.position.set( (box.min.x + box.max.x)/2 * scale, 0, (box.min.z + box.max.z)/2 * scale );
      // Slight offset to look like chimneys
      smokePoints.position.x += 6;
      smokePoints.position.z -= 3;

      // optionally add some small point lights at plausible window positions
      // (we scan some meshes for 'window' in name)
      let addedLights = 0;
      root.traverse(n => {
        if(n.isMesh && /window|glass/i.test(n.name) && addedLights < 10) {
          const lp = new THREE.PointLight(0xffd36a, 0.7, 8);
          const wp = new THREE.Vector3();
          n.getWorldPosition(wp);
          lp.position.copy(wp);
          villageGroup.add(lp);
          addedLights++;
        }
      });

      loadingEl.style.display = 'none';
      modelLoaded = true;
      console.log('Village model loaded from', srcUrl);
    }

    // start loading model
    loadVillageModel();

    // ---- Animation loop ----
    let running = true;
    let last = performance.now();
    function animate(now) {
      if(!running) return;
      const dt = (now - last) * 0.001;
      last = now;

      // update snow particle positions
      if(snowPoints) {
        const pos = snowPoints.geometry.attributes.position.array;
        const vel = snowPoints.geometry.attributes.velocity.array;
        for(let i=0;i<pos.length/3;i++){
          pos[i*3+1] -= vel[i] * (0.9 + Math.sin(now*0.0005 + i)*0.2);
          pos[i*3+0] += Math.sin(now*0.0002 + i) * 0.002;
          pos[i*3+2] += Math.cos(now*0.0003 + i) * 0.002;
          if(pos[i*3+1] < -8){
            pos[i*3+1] = 80 + Math.random()*40;
            pos[i*3+0] = (Math.random()-0.5) * 500;
            pos[i*3+2] = (Math.random()-0.5) * 500;
          }
        }
        snowPoints.geometry.attributes.position.needsUpdate = true;
      }

      // smoke rising
      if(smokePoints){
        const spos = smokePoints.geometry.attributes.position.array;
        const svel = smokePoints.geometry.attributes.velocity.array;
        for(let i=0;i<spos.length/3;i++){
          spos[i*3+0] += svel[i*3+0] * dt * 10;
          spos[i*3+1] += svel[i*3+1] * dt * 10;
          spos[i*3+2] += svel[i*3+2] * dt * 10;
          if(spos[i*3+1] > 20){
            spos[i*3+1] = 6 + Math.random()*1.2;
            spos[i*3+0] = (Math.random()-0.5)*2;
            spos[i*3+2] = (Math.random()-0.5)*2;
          }
        }
        smokePoints.geometry.attributes.position.needsUpdate = true;
      }

      // small twinkle on any lights under villageGroup
      villageGroup.traverse(node => {
        if(node.isPointLight){
          node.intensity = 0.3 + Math.abs(Math.sin(now*0.001 + node.id%10)) * 0.6;
        }
      });

      controls.update();
      renderer.render(scene, camera);
      requestAnimationFrame(animate);
    }
    requestAnimationFrame(animate);

    // Pause/resume helpers
    function stop() { running = false; }
    function start() {
      if(!running){ running = true; last = performance.now(); requestAnimationFrame(animate); }
    }
    window.__villageControls = { start, stop };

    // Pause when page hidden
    document.addEventListener('visibilitychange', () => { if(document.hidden) stop(); else start(); });

    // Reset camera button
    resetCamBtn.addEventListener('click', () => {
      camera.position.set(0, 18, 68);
      controls.target.set(0, 0, 0);
      controls.update();
    });

    // Reduce snow toggle
    reduceSnowCheckbox.addEventListener('change', (e) => {
      buildSnow(e.target.checked);
    });

    // initial UI: hide loading when model loaded or after a timeout
    setTimeout(()=> {
      if(!modelLoaded && loadingEl) loadingEl.textContent = 'Still loading village — try dropping models/village.glb into repo for best results.';
    }, 10000);

    // Expose scene for debugging
    window.__threeScene = { scene, camera, renderer, controls };

  </script>

  <!-- Main UI script: tabs, quiz, form (kept minimal) -->
  <script>
    // Tabs
    const tabs = document.querySelectorAll('.tab-btn');
    const panels = { village: document.getElementById('village'), tracker: document.getElementById('tracker'), letter: document.getElementById('letter'), catalogue: document.getElementById('catalogue'), quiz: document.getElementById('quiz') };
    function activate(name) {
      tabs.forEach(t => t.setAttribute('aria-selected', t.dataset.tab === name ? 'true' : 'false'));
      // hide all left column panels first
      document.querySelectorAll('[data-panel]').forEach(p => p.style.display = 'none');
      if(name === 'village'){ panels.village.style.display = ''; panels.letter.style.display=''; panels.catalogue.style.display=''; }
      else if(name === 'tracker'){ panels.tracker.style.display = ''; panels.letter.style.display=''; }
      else if(name === 'letter'){ panels.letter.style.display = ''; panels.village.style.display=''; }
      else if(name === 'catalogue'){ panels.catalogue.style.display=''; panels.village.style.display=''; }
      else if(name === 'quiz'){ panels.quiz.style.display=''; panels.village.style.display=''; }
      // pause/resume 3D when hiding village to save CPU
      if(name !== 'village') window.__villageControls?.stop?.(); else window.__villageControls?.start?.();
    }
    tabs.forEach(t => t.addEventListener('click', ()=> activate(t.dataset.tab)));
    activate('village');

    // Countdown (keeps previous behaviour)
    (function(){
      const countdownEl = document.getElementById('countdown');
      if(!countdownEl){
        const c = document.createElement('div'); c.id='countdown'; c.className='muted'; const parent = document.querySelector('.controls'); parent && parent.insertBefore(c, parent.firstChild);
      }
      const el = document.getElementById('countdown');
      function update(){
        const target = new Date('December 25, 2025 00:00:00');
        const now = new Date();
        let diff = target - now;
        if(diff<=0){ el.textContent = 'Merry Christmas! ✨'; return; }
        const d = Math.floor(diff / (24*60*60*1000)); diff -= d*24*60*60*1000;
        const h = Math.floor(diff/(60*60*1000)); diff -= h*60*60*1000;
        const m = Math.floor(diff/(60*1000)); diff -= m*60*1000;
        const s = Math.floor(diff/1000);
        el.textContent = `${d}d ${String(h).padStart(2,'0')}h ${String(m).padStart(2,'0')}m ${String(s).padStart(2,'0')}s`;
      }
      setInterval(update,1000); update();
    })();

    // Quiz
    (function(){
      const questions = [
        {q:"Santa's workshop is full of magical elves making toys.", a:true},
        {q:"Reindeer fly using pixie dust.", a:false},
        {q:"The North Pole has twinkling lights all year.", a:true}
      ];
      let idx = 0;
      const qEl = document.getElementById('question'), rEl = document.getElementById('result');
      const tBtn = document.getElementById('trueBtn'), fBtn = document.getElementById('falseBtn');
      function load(){ qEl.textContent = questions[idx].q; rEl.textContent = ''; tBtn.disabled=false; fBtn.disabled=false; }
      function check(v){ tBtn.disabled=fBtn.disabled=true; if(v===questions[idx].a) rEl.textContent='Correct! 🎉'; else rEl.textContent='Not quite — try the next one! ❄️'; idx=(idx+1)%questions.length; setTimeout(load,1400); }
      tBtn.addEventListener('click', ()=>check(true)); fBtn.addEventListener('click', ()=>check(false)); load();
    })();

    // Form fallback (EmailJS optional)
    (function(){
      const form = document.getElementById('letterForm'), status = document.getElementById('form-status');
      document.getElementById('letterClear').addEventListener('click', ()=> form.reset());
      form.addEventListener('submit', (e)=> {
        e.preventDefault();
        const name = document.getElementById('user_name').value.trim();
        const msg = document.getElementById('message').value.trim();
        if(!name || !msg){ status.textContent = 'Please enter your name and message.'; return; }
        // If EmailJS loaded and configured it would send; otherwise we display a local success message.
        if(window.emailjs && emailjs.send){
          emailjs.send('service_1ambdft','template_nx68i1k', {from_name:name, from_email:document.getElementById('user_email').value||'No email', message:msg})
            .then(()=> { status.textContent = 'Letter sent! 🎄'; form.reset(); })
            .catch(()=> { status.textContent = 'Send failed — try again later.'; });
        } else {
          status.textContent = 'Local test: Letter saved (Email service not configured).';
          form.reset();
        }
      });
    })();

  </script>
</body>
</html>
