<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>My Magical Santa Tracker — Winter Village</title>

  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

  <style>
    :root{
      --bg-1:#071020; --bg-2:#07304a; --accent:#c62828; --muted:#9fb3c8; --card:rgba(255,255,255,0.03);
      --radius:12px; --max-width:1200px; --ease:cubic-bezier(.2,.9,.3,1);
    }
    html,body{height:100%;margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,Arial;background:linear-gradient(180deg,var(--bg-1),var(--bg-2));color:#fff;}
    .wrap{max-width:var(--max-width);margin:28px auto;padding:20px;box-sizing:border-box;}
    header{display:flex;align-items:center;gap:16px;margin-bottom:18px;}
    .brand{display:flex;align-items:center;gap:12px;}
    .logo{width:56px;height:56px;border-radius:12px;background:linear-gradient(135deg,var(--accent),#8b0000);display:flex;align-items:center;justify-content:center;font-size:26px;box-shadow:0 6px 20px rgba(0,0,0,0.4);}
    h1{font-size:20px;margin:0;font-weight:600;}
    .subtitle{color:var(--muted);font-size:13px}

    .tabs{display:flex;gap:10px;flex-wrap:wrap;margin:14px 0 24px;}
    .tab-btn{background:transparent;border:1px solid rgba(255,255,255,0.06);padding:10px 14px;border-radius:10px;color:var(--muted);cursor:pointer;transition:all .18s var(--ease);font-weight:600;}
    .tab-btn[aria-selected="true"]{background:linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.02)); color:#fff;border-color:rgba(255,255,255,0.12);}

    .grid{display:grid;grid-template-columns: 1fr 380px; gap:22px;align-items:start;}
    @media (max-width:980px){ .grid{grid-template-columns:1fr;} .right-col{order:2;} .left-col{order:1;} }

    .card{background:var(--card);border-radius:var(--radius);box-shadow:0 8px 30px rgba(2,8,23,0.55);padding:18px;overflow:hidden;border:1px solid rgba(255,255,255,0.03);}
    .large{min-height:420px}
    .mini-title{font-size:14px;color:var(--muted);margin:0 0 8px 0;}
    #villageContainer{width:100%;height:420px;border-radius:10px;overflow:hidden;position:relative;background:linear-gradient(180deg,#0b1d2a,#072235);}
    .loading-state{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;font-weight:700;color:var(--muted);backdrop-filter: blur(1px);z-index:5;}

    /* controls, buttons, forms */
    .controls{display:flex;gap:10px;flex-wrap:wrap;margin-top:12px;}
    .btn{background:linear-gradient(180deg,var(--accent),#9f1111);border:none;padding:10px 14px;border-radius:10px;color:white;cursor:pointer;font-weight:600;}
    .btn.secondary{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted);}

    form#letterForm{display:flex;flex-direction:column;gap:10px;padding:10px 0;}
    input, textarea{width:100%;padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.06);background:transparent;color:#fff;outline:none;}
    .catalogue-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:14px}
    .toy-item{background:rgba(255,255,255,0.03);padding:10px;border-radius:10px}
    footer{margin-top:28px;color:var(--muted);font-size:13px;text-align:center}
    .muted{color:var(--muted)}
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
          <div class="subtitle">Cozy winter village — family-friendly 3D scene</div>
        </div>
      </div>
    </header>

    <nav class="tabs" role="tablist" aria-label="Main sections">
      <button class="tab-btn" role="tab" aria-selected="true" data-tab="village" id="tab-village">North Pole Village</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="tracker" id="tab-tracker">Santa Tracker</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="letter" id="tab-letter">Write to Santa</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="catalogue" id="tab-catalogue">Toy Catalogue</button>
      <button class="tab-btn" role="tab" aria-selected="false" data-tab="quiz" id="tab-quiz">Christmas Quiz</button>
    </nav>

    <main class="grid" id="app-grid">
      <section class="left-col">
        <div id="village" class="card large" data-panel>
          <div class="mini-title">Explore the Enchanted North Pole Village</div>
          <div id="villageContainer" aria-live="polite" aria-label="3D North Pole village preview">
            <div class="loading-state" id="village-loading">Loading village…</div>
            <!-- Three.js canvas will be appended here -->
          </div>
          <div class="controls" style="justify-content:flex-end">
            <div class="muted" id="countdown">—</div>
          </div>
        </div>

        <!-- tracker & quiz kept minimal for brevity -->
        <div id="tracker" class="card large" style="margin-top:18px;display:none" data-panel>
          <div class="mini-title">Santa's Magical 3D Journey Tracker</div>
          <div id="cesiumContainer" style="height:320px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);display:flex;align-items:center;justify-content:center;">(Optional: tracker)</div>
          <div class="controls" style="margin-top:12px"><div id="tracker-status" class="muted">Preview route</div></div>
        </div>

        <div id="quiz" class="card" style="margin-top:18px;display:none" data-panel>
          <div class="mini-title">Fun Magical Activity</div>
          <div style="margin-top:10px" class="quiz-area">
            <div id="question" style="font-weight:700;color:var(--accent);">Loading...</div>
            <div style="margin-top:8px">
              <button class="btn" id="trueBtn">True</button>
              <button class="btn secondary" id="falseBtn">False</button>
            </div>
            <div id="result" class="muted" aria-live="polite" style="margin-top:8px"></div>
          </div>
        </div>
      </section>

      <aside class="right-col">
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
            <div id="form-status" class="muted" role="status" aria-live="polite" style="margin-top:8px"></div>
          </form>
        </div>

        <div id="catalogue" class="card" style="margin-top:18px" data-panel>
          <div class="mini-title">Santa's Magical Toy Catalogue 🎁</div>
          <p class="muted" style="margin:6px 0 8px 0">Just for fun — pick favorites and daydream!</p>
          <div class="catalogue-grid" aria-live="polite" aria-label="toy catalogue">
            <div class="toy-item"><img src="https://images.unsplash.com/photo-1517457373958-b7bdd4587208?w=600&q=60&auto=format&fit=crop" alt="Teddy"><div style="font-weight:700;margin-top:6px">Cuddly Teddy Bear</div><div class="muted" style="font-size:13px">Soft and huggable</div></div>
            <div class="toy-item"><img src="https://images.unsplash.com/photo-1608043152269-423dbba4e7e4?w=600&q=60&auto=format&fit=crop" alt="Train"><div style="font-weight:700;margin-top:6px">Choo-Choo Train</div><div class="muted" style="font-size:13px">Zoom around the tracks</div></div>
          </div>
        </div>

        <div style="height:14px"></div>
        <footer class="muted">Made with ❤️ — Enjoy the magic!</footer>
      </aside>
    </main>
  </div>

  <!-- importmap for three + examples -->
  <script type="importmap">
    {
      "imports": {
        "three": "https://unpkg.com/three@0.152.2/build/three.module.js",
        "three/examples/": "https://unpkg.com/three@0.152.2/examples/jsm/"
      }
    }
  </script>

  <!-- Three.js Village Script (module) -->
  <script type="module">
    import * as THREE from 'three';
    import {OrbitControls} from 'three/examples/controls/OrbitControls.js';

    // Elements
    const container = document.getElementById('villageContainer');
    const loadingState = document.getElementById('village-loading');

    // Renderer
    const renderer = new THREE.WebGLRenderer({antialias:true, alpha:true});
    renderer.setPixelRatio(window.devicePixelRatio || 1);
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    container.appendChild(renderer.domElement);

    // Scene & camera
    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x071827, 0.0025);

    const camera = new THREE.PerspectiveCamera(55, 1, 0.1, 1000);
    camera.position.set(0, 25, 60);

    // Lights
    const hemi = new THREE.HemisphereLight(0xddeeff, 0x222233, 0.6);
    scene.add(hemi);

    const dir = new THREE.DirectionalLight(0xffffff, 0.9);
    dir.position.set(30, 50, 10);
    dir.castShadow = true;
    dir.shadow.camera.left = -80; dir.shadow.camera.right = 80; dir.shadow.camera.top = 80; dir.shadow.camera.bottom = -80;
    dir.shadow.mapSize.set(2048,2048);
    scene.add(dir);

    // Ground (snow)
    const groundGeo = new THREE.PlaneGeometry(600, 600);
    const groundMat = new THREE.MeshStandardMaterial({color:0xf8fbff, roughness:0.9, metalness:0});
    const ground = new THREE.Mesh(groundGeo, groundMat);
    ground.rotation.x = -Math.PI/2;
    ground.receiveShadow = true;
    scene.add(ground);

    // Helper: make a cozy low-poly house
    function createHouse({x=0,z=0, scale=1, color=0xffffff, lightOn=true} = {}) {
      const group = new THREE.Group();
      group.position.set(x, 0, z);

      // base
      const baseGeo = new THREE.BoxGeometry(6*scale, 4*scale, 6*scale);
      const baseMat = new THREE.MeshStandardMaterial({color, roughness:0.8});
      const base = new THREE.Mesh(baseGeo, baseMat);
      base.castShadow = true; base.receiveShadow = true;
      base.position.y = 2*scale;
      group.add(base);

      // roof
      const roofGeo = new THREE.ConeGeometry(5.2*scale, 3*scale, 4);
      const roofMat = new THREE.MeshStandardMaterial({color:0x6b2b2b, roughness:0.7});
      const roof = new THREE.Mesh(roofGeo, roofMat);
      roof.rotation.y = Math.PI/4;
      roof.position.y = 5*scale;
      roof.castShadow = true;
      group.add(roof);

      // door
      const doorGeo = new THREE.BoxGeometry(1.2*scale,2*scale,0.2*scale);
      const doorMat = new THREE.MeshStandardMaterial({color:0x5a2b1b});
      const door = new THREE.Mesh(doorGeo, doorMat);
      door.position.set(0,1*scale,3.05*scale);
      door.castShadow = true; group.add(door);

      // windows
      const winGeo = new THREE.PlaneGeometry(1.2*scale,1.0*scale);
      const winOnMat = new THREE.MeshStandardMaterial({emissive:0xffd36a, emissiveIntensity: 1, color:0x000000});
      const winOffMat = new THREE.MeshStandardMaterial({color:0x112233});
      const leftWin = new THREE.Mesh(winGeo, lightOn ? winOnMat : winOffMat);
      leftWin.position.set(-1.6*scale,2.2*scale,3.05*scale);
      leftWin.rotation.y = Math.PI/2; group.add(leftWin);
      const rightWin = leftWin.clone();
      rightWin.position.x = 1.6*scale; group.add(rightWin);

      // chimney
      const chimGeo = new THREE.BoxGeometry(0.8*scale,1.4*scale,0.8*scale);
      const chimMat = new THREE.MeshStandardMaterial({color:0x333333});
      const chim = new THREE.Mesh(chimGeo, chimMat);
      chim.position.set(2*scale,6.2*scale,-1*scale);
      chim.castShadow = true; group.add(chim);

      // small ambient light near window
      if(lightOn) {
        const wLight = new THREE.PointLight(0xffd36a, 0.7, 8*scale);
        wLight.position.set(0,2.3*scale,3.3*scale);
        group.add(wLight);
      }

      // tiny snow cap on roof
      const capGeo = new THREE.ConeGeometry(5.2*scale, 0.3*scale, 4);
      const capMat = new THREE.MeshStandardMaterial({color:0xffffff});
      const cap = new THREE.Mesh(capGeo, capMat);
      cap.rotation.y = Math.PI/4; cap.position.y = 6.4*scale; cap.scale.set(0.98,0.98,0.98);
      group.add(cap);

      return group;
    }

    // Create a cluster of houses (village center)
    const villageGroup = new THREE.Group();
    const houseColors = [0xfaf3e0, 0xfff9f0, 0xfbe7e7, 0xeaf6ff];
    for(let i=0;i<9;i++){
      const angle = (i / 9) * Math.PI*2;
      const radius = 8 + (Math.random()*6);
      const x = Math.cos(angle) * radius;
      const z = Math.sin(angle) * radius;
      const scale = 0.8 + Math.random()*0.6;
      const house = createHouse({x, z, scale, color: houseColors[i % houseColors.length], lightOn: Math.random() > 0.2});
      house.rotation.y = Math.random() * Math.PI * 2;
      villageGroup.add(house);

      // small lamp posts near some houses
      if(Math.random() > 0.6){
        const pole = new THREE.Mesh(new THREE.CylinderGeometry(0.08,0.08,2,8), new THREE.MeshStandardMaterial({color:0x222222}));
        pole.position.set(x+1.5,1,z+1.2);
        const glow = new THREE.PointLight(0xffe8a0, 0.6, 6);
        glow.position.set(x+1.5,2,z+1.2);
        villageGroup.add(pole); villageGroup.add(glow);
      }
    }
    villageGroup.position.set(0,0,0);
    scene.add(villageGroup);

    // add a simple town square tree
    function createTree(x,z,scale=1){
      const g = new THREE.Group();
      const trunk = new THREE.Mesh(new THREE.CylinderGeometry(0.5*scale,0.6*scale,2*scale,8), new THREE.MeshStandardMaterial({color:0x6b4a34}));
      trunk.position.y = 1*scale;
      trunk.castShadow = true; g.add(trunk);
      for(let i=0;i<4;i++){
        const cone = new THREE.Mesh(new THREE.ConeGeometry(3*scale - i*0.6*scale, 2.2*scale, 12), new THREE.MeshStandardMaterial({color:0x0a7b2a, roughness:0.9}));
        cone.position.y = 2.6*scale + i*0.8*scale; cone.castShadow = true;
        g.add(cone);
      }
      g.position.set(x,0,z);
      return g;
    }
    const centerTree = createTree(0,0,1.3);
    scene.add(centerTree);

    // Forest: instanced trees for performance
    const treeCount = 220; // adjust for perf
    const instancedTrees = new THREE.InstancedMesh(new THREE.ConeGeometry(1.6, 5, 8), new THREE.MeshStandardMaterial({color:0x0f6f2a}), treeCount);
    instancedTrees.count = treeCount;
    const dummy = new THREE.Object3D();
    for(let i=0;i<treeCount;i++){
      const radius = 40 + Math.random()*120;
      const theta = Math.random()*Math.PI*2;
      dummy.position.set(Math.cos(theta)*radius, 0, Math.sin(theta)*radius);
      dummy.scale.setScalar(0.7 + Math.random()*1.6);
      dummy.rotation.y = Math.random()*Math.PI*2;
      dummy.updateMatrix();
      instancedTrees.setMatrixAt(i, dummy.matrix);
    }
    instancedTrees.castShadow = false;
    scene.add(instancedTrees);

    // Snow particle system
    const SNOW_COUNT = 3000;
    const snowGeo = new THREE.BufferGeometry();
    const positions = new Float32Array(SNOW_COUNT*3);
    const velocities = new Float32Array(SNOW_COUNT);
    for(let i=0;i<SNOW_COUNT;i++){
      positions[i*3+0] = (Math.random()-0.5) * 300; // x
      positions[i*3+1] = Math.random() * 120 + 10;  // y
      positions[i*3+2] = (Math.random()-0.5) * 300; // z
      velocities[i] = 0.2 + Math.random() * 0.6;
    }
    snowGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    snowGeo.setAttribute('velocity', new THREE.BufferAttribute(velocities, 1));
    const sprite = new THREE.TextureLoader().load('https://raw.githubusercontent.com/mrdoob/three.js/dev/examples/textures/sprites/snowflake1.png');
    const snowMat = new THREE.PointsMaterial({size:0.9, map:sprite, transparent:true, depthWrite:false, opacity:0.9});
    const snowPoints = new THREE.Points(snowGeo, snowMat);
    snowPoints.frustumCulled = false;
    scene.add(snowPoints);

    // Chimney smoke particles (small simple system)
    const SMOKE_COUNT = 80;
    const smokeGeo = new THREE.BufferGeometry();
    const smokePos = new Float32Array(SMOKE_COUNT*3);
    const smokeVel = new Float32Array(SMOKE_COUNT*3);
    for(let i=0;i<SMOKE_COUNT;i++){
      smokePos[i*3+0] = (Math.random()-0.5)*2;
      smokePos[i*3+1] = 6 + Math.random()*2;
      smokePos[i*3+2] = (Math.random()-0.5)*2;
      smokeVel[i*3+0] = (Math.random()-0.5)*0.02;
      smokeVel[i*3+1] = 0.02 + Math.random()*0.02;
      smokeVel[i*3+2] = (Math.random()-0.5)*0.02;
    }
    smokeGeo.setAttribute('position', new THREE.BufferAttribute(smokePos,3));
    smokeGeo.setAttribute('velocity', new THREE.BufferAttribute(smokeVel,3));
    const smokeMat = new THREE.PointsMaterial({size:1.8, transparent:true, opacity:0.22, color:0xcccccc, depthWrite:false});
    const smokePoints = new THREE.Points(smokeGeo, smokeMat);
    smokePoints.position.set(2,0,-1); // relative offset (will appear near chimneys)
    scene.add(smokePoints);

    // subtle ambient fog color via background sky gradient (approx)
    renderer.setClearColor(0x062233);

    // Orbit controls
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.08;
    controls.minDistance = 18;
    controls.maxDistance = 180;
    controls.maxPolarAngle = Math.PI * 0.48;

    // Resize handling
    function resize() {
      const w = container.clientWidth;
      const h = container.clientHeight;
      renderer.setSize(w, h);
      camera.aspect = w / h;
      camera.updateProjectionMatrix();
    }
    window.addEventListener('resize', resize);
    resize();

    // Animation loop control
    let running = true;
    let lastTime = performance.now();
    function animate(now){
      if(!running) return;
      const dt = (now - lastTime) * 0.001; lastTime = now;

      // snow update
      const pos = snowGeo.attributes.position.array;
      const vel = snowGeo.attributes.velocity.array;
      for(let i=0;i<SNOW_COUNT;i++){
        pos[i*3+1] -= vel[i] * (0.9 + Math.sin(now*0.0005 + i) * 0.2); // gravity + slight variation
        pos[i*3+0] += Math.sin(now*0.0002 + i) * 0.002; // wind
        pos[i*3+2] += Math.cos(now*0.0003 + i) * 0.002;
        if(pos[i*3+1] < -10){ pos[i*3+1] = 80 + Math.random()*40; pos[i*3+0] = (Math.random()-0.5)*300; pos[i*3+2] = (Math.random()-0.5)*300; }
      }
      snowGeo.attributes.position.needsUpdate = true;

      // smoke update (rising)
      const spos = smokeGeo.attributes.position.array;
      const svel = smokeGeo.attributes.velocity.array;
      for(let i=0;i<SMOKE_COUNT;i++){
        spos[i*3+0] += svel[i*3+0] * dt * 20;
        spos[i*3+1] += svel[i*3+1] * dt * 20;
        spos[i*3+2] += svel[i*3+2] * dt * 20;
        // fade-away top
        if(spos[i*3+1] > 14){ spos[i*3+1] = 6 + Math.random()*1; spos[i*3+0] = (Math.random()-0.5)*2; spos[i*3+2] = (Math.random()-0.5)*2; }
      }
      smokeGeo.attributes.position.needsUpdate = true;

      // small twinkle on village lights (animate intensity by adjusting emissive color via point lights if present)
      villageGroup.traverse(child=>{
        if(child.isPointLight){
          child.intensity = 0.4 + Math.abs(Math.sin(now*0.001 + (child.id%10))) * 0.6;
        }
      });

      controls.update();
      renderer.render(scene, camera);
      requestAnimationFrame(animate);
    }

    // Start once resources done
    function start() {
      if(!running) { running = true; lastTime = performance.now(); requestAnimationFrame(animate); }
    }
    function stop() { running = false; }

    // initially hide loading and start
    loadingState.style.display = 'none';
    start();

    // Expose controls for tab logic
    window.__villageControls = { start, stop };

    // place camera gently to show village
    camera.position.set(0, 22, 62);
    camera.lookAt(0, 0, 0);

    // Accessibility: pause animation on page/tab hidden
    document.addEventListener('visibilitychange', ()=> {
      if(document.hidden) stop(); else start();
    });

    // Center composition: move village a bit for better framing
    villageGroup.position.set(0, 0, 0);

    // (Optional) Add subtle star field (far)
    const starGeo = new THREE.BufferGeometry();
    const starCount = 400;
    const starPos = new Float32Array(starCount*3);
    for(let i=0;i<starCount;i++){
      starPos[i*3+0] = (Math.random()-0.5) * 800;
      starPos[i*3+1] = Math.random()*200 + 40;
      starPos[i*3+2] = (Math.random()-0.5) * 800;
    }
    starGeo.setAttribute('position', new THREE.BufferAttribute(starPos,3));
    const starMat = new THREE.PointsMaterial({color:0xffffff, size:0.6, opacity:0.9, transparent:true});
    const stars = new THREE.Points(starGeo, starMat); stars.frustumCulled=false; scene.add(stars);

    // Done scene setup
    console.log('Village scene ready');
  </script>

  <!-- Main app script (tabs, form, quiz minimal handling) -->
  <script>
    // Tabs logic (simple)
    const tabBtns = document.querySelectorAll('.tab-btn');
    const panels = { village: document.getElementById('village'), tracker: document.getElementById('tracker'), letter: document.getElementById('letter'), catalogue: document.getElementById('catalogue'), quiz: document.getElementById('quiz') };
    function activateTab(name){
      tabBtns.forEach(b => b.setAttribute('aria-selected', b.dataset.tab === name ? 'true' : 'false'));
      // Hide all main panels (left column)
      document.querySelectorAll('[data-panel]').forEach(p => p.style.display = 'none');
      // Show selection logic: keep right column (letter/catalogue) visible for most tabs
      if(name === 'tracker'){ panels.tracker.style.display = ''; panels.letter.style.display = ''; panels.catalogue.style.display=''; }
      else if(name === 'village'){ panels.village.style.display = ''; panels.letter.style.display = ''; panels.catalogue.style.display=''; }
      else if(name === 'letter'){ panels.letter.style.display = ''; panels.village.style.display = ''; }
      else if(name === 'catalogue'){ panels.catalogue.style.display = ''; panels.village.style.display = ''; panels.letter.style.display = ''; }
      else if(name === 'quiz'){ panels.quiz.style.display = ''; panels.village.style.display = ''; }

      // pause/resume three.js village when not visible
      if(name !== 'village') window.__villageControls?.stop?.(); else window.__villageControls?.start?.();
    }
    tabBtns.forEach(b => b.addEventListener('click', ()=> activateTab(b.dataset.tab)));
    activateTab('village');

    // Countdown simple
    const countdownEl = document.getElementById('countdown');
    function updateCountdown(){
      const target = new Date('December 25, 2025 00:00:00');
      const now = new Date();
      let diff = target - now;
      if(diff <= 0){ countdownEl.textContent = "Merry Christmas! ✨"; return; }
      const d = Math.floor(diff / (24*60*60*1000)); diff -= d*24*60*60*1000;
      const h = Math.floor(diff / (60*60*1000)); diff -= h*60*60*1000;
      const m = Math.floor(diff / (60*1000)); diff -= m*60*1000;
      const s = Math.floor(diff / 1000);
      countdownEl.textContent = `${d}d ${String(h).padStart(2,'0')}h ${String(m).padStart(2,'0')}m ${String(s).padStart(2,'0')}s`;
    }
    setInterval(updateCountdown, 1000); updateCountdown();

    // quiz minimal
    const questions = [
      {q:"Santa's workshop is full of magical elves making toys.", a:true},
      {q:"Reindeer fly using pixie dust.", a:false},
      {q:"The North Pole has twinkling lights all year.", a:true}
    ];
    let qi = 0;
    const qEl = document.getElementById('question'), resEl = document.getElementById('result');
    document.getElementById('trueBtn').addEventListener('click', ()=> answer(true));
    document.getElementById('falseBtn').addEventListener('click', ()=> answer(false));
    function loadQ(){ qEl.textContent = questions[qi].q; resEl.textContent = ''; }
    function answer(v){
      if(v === questions[qi].a) resEl.textContent = 'Correct! 🎉'; else resEl.textContent = 'Nice try — next one! ❄️';
      qi = (qi+1) % questions.length; setTimeout(loadQ, 1400);
    }
    loadQ();

    // emailjs / form simple behavior: keep existing fallback from earlier
    const form = document.getElementById('letterForm'), statusEl = document.getElementById('form-status');
    document.getElementById('letterClear').addEventListener('click', ()=> form.reset());
    form.addEventListener('submit', (e)=>{
      e.preventDefault();
      const name = document.getElementById('user_name').value.trim();
      const msg = document.getElementById('message').value.trim();
      if(!name || !msg){ statusEl.textContent = 'Please fill name and message.'; return; }
      // If EmailJS loaded, use it. Otherwise show local success message.
      if(window.emailjs && emailjs.send){
        // (You must initialize emailjs separately with your key)
        emailjs.send('service_1ambdft','template_nx68i1k', {from_name:name, from_email:document.getElementById('user_email').value||'No email', message:msg})
          .then(()=> { statusEl.textContent = 'Letter sent! 🎄'; form.reset(); })
          .catch(()=> { statusEl.textContent = 'Send failed — try again later.'; });
      } else {
        statusEl.textContent = 'Local test: Letter saved (Email service not configured).';
        form.reset();
      }
    });
  </script>
</body>
</html>
