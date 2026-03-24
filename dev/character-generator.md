---
title: Character Generator
layout: default
parent: Dev Tools
nav_order: 1
---

# Character Generator

Create Magic&Myth characters step by step. Characters auto-save to your browser.

<div id="app">
  <div id="loading">Loading game data...</div>

  <!-- My Characters -->
  <div id="my-characters" style="display:none;">
    <h2>My Characters</h2>
    <div id="character-list"></div>
    <div style="margin-top:1rem;">
      <button id="new-char-btn" class="btn-primary">New Character</button>
      <button id="import-btn" class="btn-secondary" style="margin-left:.5rem;">Import JSON</button>
      <input type="file" id="import-file" accept=".json" style="display:none;">
    </div>
  </div>

  <!-- Generator Steps -->
  <div id="gen-steps" style="display:none;">

    <!-- Step 1: Attributes -->
    <div id="step-attrs" class="gen-step">
      <h2>Step 1: Roll Attributes</h2>
      <div style="margin-bottom:1rem;">
        <label for="method-select"><strong>Method:</strong></label>
        <select id="method-select"></select>
        <p id="method-desc" style="font-size:.85rem;color:#666;margin:.5rem 0;"></p>
      </div>
      <button id="roll-btn" class="btn-primary">Roll Attributes</button>
      <span style="margin:0 .75rem;color:#999;">or</span>
      <button id="manual-entry-btn" class="btn-primary" style="background:#666;">Enter Scores Manually</button>
      <!-- Manual entry UI -->
      <div id="manual-ui" style="display:none;margin-top:1rem;">
        <p><strong>Type your attribute scores:</strong></p>
        <div class="stat-grid" id="manual-boxes"></div>
      </div>
      <div id="attr-display" style="display:none;margin-top:1rem;">
        <div class="stat-grid" id="stat-boxes"></div>
        <button id="reroll-btn" class="btn-small" style="margin-top:.5rem;">Re-roll</button>
      </div>
      <!-- Free assign UI (for non-legacy methods) -->
      <div id="assign-ui" style="display:none;margin-top:1rem;">
        <p><strong>Assign scores to attributes:</strong> Click a score, then click an attribute to place it.</p>
        <div id="unassigned-scores" style="display:flex;gap:.5rem;flex-wrap:wrap;margin:.5rem 0;"></div>
        <div class="stat-grid" id="assign-boxes"></div>
        <button id="clear-assign-btn" class="btn-small" style="margin-top:.5rem;">Clear All</button>
      </div>
      <div id="attr-confirm" style="display:none;margin-top:1rem;">
        <button id="confirm-attrs-btn" class="btn-primary">Confirm Attributes</button>
      </div>
    </div>

    <!-- Step 2: Ancestry -->
    <div id="step-ancestry" class="gen-step" style="display:none;">
      <h2>Step 2: Choose Ancestry</h2>
      <button id="back-to-attrs-btn" class="btn-back">&larr; Back to Attributes</button>
      <div id="ancestry-options" class="option-grid"></div>
    </div>

    <!-- Step 2b: Human bonus -->
    <div id="step-human-bonus" class="gen-step" style="display:none;">
      <h2>Human Bonus: +1 to Any Attribute</h2>
      <p>As a Human, you may add +1 to any attribute of your choice.</p>
      <button id="back-to-ancestry-from-human-btn" class="btn-back">&larr; Back to Ancestry</button>
      <div id="human-bonus-grid" class="option-grid" style="grid-template-columns:repeat(6,1fr);"></div>
    </div>

    <!-- Step 3: Class -->
    <div id="step-class" class="gen-step" style="display:none;">
      <h2>Step 3: Choose Class</h2>
      <button id="back-to-ancestry-btn" class="btn-back">&larr; Back to Ancestry</button>
      <p id="class-note" style="color:#888;font-size:.9rem;"></p>
      <div id="class-options" class="option-grid"></div>
    </div>

    <!-- Step 4: Name -->
    <div id="step-name" class="gen-step" style="display:none;">
      <h2>Step 4: Name Your Character</h2>
      <button id="back-to-class-btn" class="btn-back">&larr; Back to Class</button>
      <div style="margin-top:.75rem;">
        <input type="text" id="char-name-input" placeholder="Enter character name..." style="font-size:1.1rem;padding:.5rem;width:100%;max-width:300px;">
        <button id="create-btn" class="btn-primary" style="margin-left:.5rem;">Create Character</button>
      </div>
    </div>

    <div id="cancel-row" style="margin-top:1rem;display:none;">
      <button id="cancel-btn" class="btn-secondary">Cancel</button>
    </div>
  </div>

  <!-- Character Sheet -->
  <div id="char-sheet" style="display:none;"></div>
  <div id="sheet-actions" style="display:none;margin-top:1rem;">
    <button id="export-btn" class="btn-secondary">Export JSON</button>
    <button id="print-btn" class="btn-secondary" style="margin-left:.5rem;">Print</button>
    <button id="delete-btn" class="btn-secondary btn-danger" style="margin-left:.5rem;">Delete</button>
    <button id="back-btn" class="btn-secondary" style="margin-left:.5rem;">Back to List</button>
  </div>
</div>

<style>
  @media print {
    #my-characters,#gen-steps,#sheet-actions,.btn-primary,.btn-secondary,.btn-small,.no-print{display:none!important;}
    .site-nav,.site-header,.site-footer,.search,nav,.breadcrumb-nav{display:none!important;}
    #char-sheet{border:1px solid #000!important;max-width:100%!important;box-shadow:none!important;}
    body{background:#fff!important;}
    .main-content{padding:0!important;margin:0!important;}
  }

  .gen-step{margin-bottom:2rem;padding-bottom:1.5rem;border-bottom:1px solid #ddd;}
  .gen-step h2{color:#4a1a6b;margin-bottom:.75rem;}

  .btn-primary{font-size:1.05rem;padding:.5rem 1.5rem;cursor:pointer;background:#4a1a6b;color:#fff;border:none;border-radius:4px;}
  .btn-primary:hover{background:#3a1055;}
  .btn-secondary{font-size:.95rem;padding:.4rem 1rem;cursor:pointer;background:#e2e8f0;border:1px solid #999;border-radius:4px;}
  .btn-secondary:hover{background:#cbd5e0;}
  .btn-secondary.btn-danger{color:#c44;border-color:#c44;}
  .btn-secondary.btn-danger:hover{background:#fee;}
  .btn-small{font-size:.85rem;padding:.25rem .75rem;cursor:pointer;background:#f0f0f0;border:1px solid #ccc;border-radius:3px;}
  .btn-back{font-size:.85rem;padding:.3rem .75rem;cursor:pointer;background:transparent;border:1px solid #999;border-radius:4px;color:#666;margin-bottom:.75rem;display:inline-block;}
  .btn-back:hover{background:#f0f0f0;color:#333;}

  .stat-grid{display:grid;grid-template-columns:repeat(6,1fr);gap:.75rem;margin:.75rem 0;}
  @media(max-width:600px){.stat-grid{grid-template-columns:repeat(3,1fr);}}
  .stat-box{border:2px solid #ccc;border-radius:6px;padding:.75rem .5rem;text-align:center;background:#fafafa;transition:all .2s;}
  .stat-box .attr-label{font-size:.75rem;color:#666;text-transform:uppercase;font-weight:bold;letter-spacing:.05em;}
  .stat-box .attr-score{font-size:1.6rem;font-weight:bold;color:#333;margin:.25rem 0;}
  .stat-box .attr-mod{font-size:.85rem;color:#4a1a6b;font-weight:600;}
  .stat-box .attr-detail{font-size:.7rem;color:#888;margin-top:.15rem;}
  .stat-box.modified{border-color:#4a1a6b;background:#f5f0fa;}
  .stat-box.warning{border-color:#c44;}
  .stat-box.clickable{cursor:pointer;} .stat-box.clickable:hover{border-color:#4a1a6b;background:#f5f0fa;}
  .stat-box.selected{border-color:#4a1a6b;background:#ede0f7;box-shadow:0 0 0 2px #4a1a6b;}
  .stat-box.empty{border-style:dashed;}
  .manual-input{width:3rem;font-size:1.3rem;text-align:center;border:1px solid #ccc;border-radius:4px;padding:.25rem;font-weight:bold;}
  .manual-input:focus{border-color:#4a1a6b;outline:none;box-shadow:0 0 0 2px rgba(74,26,107,.2);}

  .option-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:1rem;margin:.75rem 0;}
  .option-card{border:2px solid #ddd;border-radius:8px;padding:1rem;cursor:pointer;transition:all .2s;background:#fff;}
  .option-card:hover{border-color:#4a1a6b;background:#faf5ff;}
  .option-card.disabled{opacity:.45;cursor:not-allowed;pointer-events:none;}
  .option-card h3{margin:0 0 .25rem 0;color:#333;font-size:1.05rem;}
  .option-card .card-desc{font-size:.85rem;color:#666;margin:.25rem 0;}
  .option-card .card-detail{font-size:.8rem;color:#888;margin:.15rem 0;}
  .option-card .card-tag{display:inline-block;background:#ede0f7;color:#4a1a6b;padding:.1rem .4rem;border-radius:3px;font-size:.7rem;font-weight:600;margin:.1rem .15rem .1rem 0;}
  .option-card .card-tag.warn{background:#fff0e0;color:#b45309;}
  .option-card .card-tag.bad{background:#fee;color:#c44;}

  .score-chip{display:inline-block;padding:.4rem .8rem;border:2px solid #ccc;border-radius:6px;font-size:1.1rem;font-weight:bold;cursor:pointer;background:#fff;transition:all .15s;min-width:2.5rem;text-align:center;}
  .score-chip:hover{border-color:#4a1a6b;background:#f5f0fa;}
  .score-chip.picked{border-color:#4a1a6b;background:#ede0f7;box-shadow:0 0 0 2px #4a1a6b;}
  .score-chip.placed{opacity:.3;cursor:default;pointer-events:none;}

  /* Character list */
  .char-card{border:2px solid #ddd;padding:1rem;border-radius:6px;margin:.5rem 0;cursor:pointer;display:flex;justify-content:space-between;align-items:center;transition:all .15s;}
  .char-card:hover{border-color:#4a1a6b;background:#faf5ff;}
  .char-card .cc-name{font-weight:bold;font-size:1.05rem;}
  .char-card .cc-info{font-size:.85rem;color:#666;}

  /* Character sheet */
  .sheet{max-width:800px;border:2px solid #4a1a6b;border-radius:8px;padding:1.5rem;background:#fff;}
  .sheet h2{color:#4a1a6b;border-bottom:2px solid #4a1a6b;padding-bottom:.25rem;margin:1.25rem 0 .75rem 0;}
  .sheet h2:first-child{margin-top:0;}
  .sheet-header{text-align:center;margin-bottom:1rem;}
  .sheet-header h1{margin:0;color:#4a1a6b;font-size:1.8rem;}
  .sheet-header .sh-subtitle{font-size:1rem;color:#666;}

  .sheet-row{display:flex;gap:1rem;flex-wrap:wrap;margin:.5rem 0;}
  .sheet-row .sr-label{font-weight:600;color:#555;min-width:80px;}
  .sheet-row .sr-value{color:#333;}

  .sheet-stats{display:grid;grid-template-columns:repeat(6,1fr);gap:.5rem;margin:.75rem 0;}
  @media(max-width:600px){.sheet-stats{grid-template-columns:repeat(3,1fr);}}
  .ss-box{border:1px solid #ccc;border-radius:4px;padding:.5rem .25rem;text-align:center;background:#fafafa;font-size:.8rem;}
  .ss-box .ss-label{font-weight:bold;color:#4a1a6b;font-size:.7rem;text-transform:uppercase;}
  .ss-box .ss-score{font-size:1.3rem;font-weight:bold;color:#333;}
  .ss-box .ss-mod{color:#666;}

  .derived-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(120px,1fr));gap:.5rem;margin:.75rem 0;}
  .dg-box{border:1px solid #ccc;padding:.5rem;text-align:center;background:#f8f8f8;border-radius:4px;}
  .dg-box .dg-label{font-size:.7rem;color:#888;text-transform:uppercase;}
  .dg-box .dg-value{font-size:1.2rem;font-weight:bold;color:#333;}

  .trait-box{background:#f0e8f8;padding:.5rem .75rem;margin:.25rem 0;border-left:3px solid #4a1a6b;font-size:.9rem;border-radius:0 4px 4px 0;}
  .trait-box strong{color:#4a1a6b;}

  .save-row{display:grid;grid-template-columns:repeat(3,1fr);gap:.5rem;margin:.5rem 0;}
  .save-box{border:1px solid #ccc;padding:.5rem;text-align:center;background:#fafafa;border-radius:4px;}
  .save-box .save-label{font-size:.75rem;color:#888;text-transform:uppercase;}
  .save-box .save-total{font-size:1.3rem;font-weight:bold;color:#333;}
  .save-box .save-breakdown{font-size:.7rem;color:#999;}

  .xp-note{font-size:.85rem;padding:.4rem .75rem;border-radius:4px;margin:.5rem 0;display:inline-block;}
  .xp-note.bonus{background:#e8f5e9;color:#2e7d32;}
  .xp-note.penalty{background:#fff3e0;color:#e65100;}

  /* Editable fields */
  .editable{border-bottom:1px dashed #aaa;cursor:pointer;padding:0 .15rem;border-radius:2px;transition:all .15s;}
  .editable:hover{background:#f5f0fa;border-color:#4a1a6b;}
  .editable:focus{outline:none;border-bottom:2px solid #4a1a6b;background:#faf5ff;}

  /* Weapon chart */
  .weapon-table{width:100%;border-collapse:collapse;font-size:.85rem;margin:.5rem 0;}
  .weapon-table th{background:#4a1a6b;color:#fff;padding:.4rem .5rem;text-align:left;font-size:.75rem;font-weight:600;}
  .weapon-table td{padding:.35rem .5rem;border-bottom:1px solid #eee;}
  .weapon-table tr:hover{background:#faf5ff;}
  .weapon-table .wt-init{font-weight:bold;color:#4a1a6b;}
  .weapon-table .wt-remove{color:#c44;cursor:pointer;font-size:.75rem;opacity:.5;}
  .weapon-table .wt-remove:hover{opacity:1;}

  /* Add weapon/item rows */
  .add-row{display:flex;gap:.5rem;align-items:center;margin:.5rem 0;flex-wrap:wrap;}
  .add-row select,.add-row input{padding:.3rem .5rem;border:1px solid #ccc;border-radius:3px;font-size:.85rem;}
  .add-row select{max-width:200px;}

  /* Equipment list */
  .equip-item{display:flex;align-items:center;gap:.5rem;padding:.25rem 0;border-bottom:1px solid #f0f0f0;font-size:.85rem;}
  .equip-item .ei-name{flex:1;}
  .equip-item .ei-remove{color:#c44;cursor:pointer;font-size:.75rem;opacity:.5;}
  .equip-item .ei-remove:hover{opacity:1;}

  /* HP tracker */
  .hp-tracker{display:flex;align-items:center;gap:.5rem;}
  .hp-btn{width:28px;height:28px;border:1px solid #ccc;border-radius:4px;cursor:pointer;font-size:1rem;font-weight:bold;display:flex;align-items:center;justify-content:center;background:#fff;}
  .hp-btn:hover{background:#f0f0f0;}
  .hp-btn.damage{color:#c44;border-color:#c44;}
  .hp-btn.damage:hover{background:#fee;}
  .hp-btn.heal{color:#2e7d32;border-color:#2e7d32;}
  .hp-btn.heal:hover{background:#e8f5e9;}

  /* AC detail */
  .ac-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:.5rem;margin:.5rem 0;}
  .ac-box{border:1px solid #ccc;padding:.4rem;text-align:center;border-radius:4px;background:#fafafa;}
  .ac-box .ac-label{font-size:.65rem;color:#888;text-transform:uppercase;}
  .ac-box .ac-val{font-size:1.2rem;font-weight:bold;}
</style>

<script>
(async function() {
  // ============ DATA LOADING ============
  const BASE = '/magicandmyth';
  let DATA = {};

  try {
    const [ancestries, classes, attributes, equipment] = await Promise.all([
      fetch(`${BASE}/data/ancestries.json`).then(r => r.json()),
      fetch(`${BASE}/data/classes.json`).then(r => r.json()),
      fetch(`${BASE}/data/attributes.json`).then(r => r.json()),
      fetch(`${BASE}/data/equipment.json`).then(r => r.json())
    ]);
    DATA = { ancestries: ancestries.ancestries, classes: classes.classes, attributes, equipment };
  } catch(e) {
    document.getElementById('loading').textContent = 'Failed to load game data. Please refresh.';
    console.error(e);
    return;
  }

  document.getElementById('loading').style.display = 'none';
  document.getElementById('my-characters').style.display = 'block';

  // ============ STATE ============
  const STORAGE_KEY = 'mm-characters';
  let characters = [];
  let currentCharId = null;

  let genState = {
    method: 'legacy',
    scores: [],           // raw scores in order (for legacy) or pool (for free)
    assigned: {},         // { STR: 14, DEX: 12, ... }
    ancestry: null,
    ancestryData: null,
    charClass: null,
    classData: null,
    humanBonus: null,
    name: ''
  };

  // ============ STORAGE ============
  function loadChars() {
    try { characters = JSON.parse(localStorage.getItem(STORAGE_KEY)) || []; }
    catch(e) { characters = []; }
  }
  function saveChars() {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(characters));
  }
  function saveCurrent() {
    if (!currentCharId) return;
    const idx = characters.findIndex(c => c.id === currentCharId);
    if (idx >= 0) { saveChars(); }
  }

  // ============ UTILITIES ============
  function genId() { return Date.now().toString(36) + Math.random().toString(36).slice(2,7); }
  function roll(sides) { return Math.floor(Math.random() * sides) + 1; }
  function roll3d6() { return roll(6) + roll(6) + roll(6); }
  function roll4d6drop() { const r = [roll(6),roll(6),roll(6),roll(6)].sort((a,b)=>b-a); return r[0]+r[1]+r[2]; }
  function roll3d6noOnes() { const dice = [0,0,0].map(()=>{ let d=roll(6); while(d===1) d=roll(6); return d; }); return dice[0]+dice[1]+dice[2]; }

  function lookup(table, score) {
    const s = String(Math.min(Math.max(score, 1), 25));
    return table[s] !== undefined ? table[s] : table[String(Math.min(score, 20))];
  }

  function getCheckMod(score) { return lookup(DATA.attributes.check_modifier, score); }
  function getStrAtkDmg(score) { return lookup(DATA.attributes.strength.attack_damage_modifier, score); }
  function getDexRanged(score) { return lookup(DATA.attributes.dexterity.ranged_attack_modifier, score); }
  function getDexDefense(score) { return lookup(DATA.attributes.dexterity.defense_modifier, score); }
  function getConFort(score) { return lookup(DATA.attributes.constitution.fortitude_modifier, score); }
  function getWisSave(score) { return lookup(DATA.attributes.wisdom.will_save_modifier, score); }
  function getChaInfluence(score) { return lookup(DATA.attributes.charisma.influence_modifier, score); }
  function getIntLangs(score) { return lookup(DATA.attributes.intelligence.bonus_languages, score); }
  function getChaHench(score) { return lookup(DATA.attributes.charisma.max_henchmen, score); }
  function getWeight(score) { return lookup(DATA.attributes.strength.weight_allowance, score); }

  function formatMod(n) { return n >= 0 ? `+${n}` : String(n); }
  function hitDieMax(die) { return parseInt(die.replace('d','')); }

  function getFinalAttrs(state) {
    const base = { ...state.assigned };
    if (state.ancestryData && state.ancestryData.attributes) {
      for (const [attr, mod] of Object.entries(state.ancestryData.attributes)) {
        base[attr] = (base[attr] || 10) + mod;
      }
    }
    if (state.humanBonus) {
      base[state.humanBonus] = (base[state.humanBonus] || 10) + 1;
    }
    // Clamp to 1 minimum
    for (const k of Object.keys(base)) { base[k] = Math.max(1, base[k]); }
    return base;
  }

  function isAncestryAllowed(ancestryData, classData) {
    if (!classData) return true;
    const allowed = classData.allowed_ancestries;
    if (!allowed) return true;
    if (allowed.includes('all')) return true;
    return allowed.includes(ancestryData.id);
  }

  function isClassAllowed(classData, ancestryData) {
    if (!ancestryData) return true;
    const allowed = ancestryData.allowed_classes;
    if (!allowed) return true;
    return allowed.includes(classData.id);
  }

  function getXpNote(classData, finalAttrs) {
    if (!classData) return null;
    // Check prime example
    const prime = classData.prime_example;
    if (prime && prime.requirements) {
      let meets = true;
      for (const [attr, req] of Object.entries(prime.requirements)) {
        if ((finalAttrs[attr] || 0) < req) { meets = false; break; }
      }
      if (meets) return { type: 'bonus', text: `+10% XP (Prime Example)` };
    }
    // Check attribute requirements
    const reqs = classData.attribute_requirements;
    if (reqs) {
      for (const [attr, req] of Object.entries(reqs)) {
        if (attr === 'note') continue;
        if ((finalAttrs[attr] || 0) < req) {
          return { type: 'penalty', text: `-10% XP (${attr} ${finalAttrs[attr]} < ${req})` };
        }
      }
    }
    return null;
  }

  // ============ ROLLING METHODS ============
  function rollLegacy() {
    return DATA.attributes.attributes.map(() => {
      const a = roll3d6(), b = roll3d6();
      return Math.max(a, b);
    });
  }

  function rollDefault() {
    const rolls = [];
    for (let i = 0; i < 7; i++) rolls.push(roll3d6());
    rolls.sort((a,b) => a-b);
    return rolls.slice(1); // drop lowest
  }

  function rollSeries() {
    const series = [];
    for (let s = 0; s < 3; s++) {
      const set = [];
      for (let i = 0; i < 6; i++) set.push(roll3d6());
      series.push(set);
    }
    // Pick the series with highest total
    let best = series[0], bestTotal = series[0].reduce((a,b)=>a+b,0);
    for (let i = 1; i < 3; i++) {
      const t = series[i].reduce((a,b)=>a+b,0);
      if (t > bestTotal) { best = series[i]; bestTotal = t; }
    }
    return best;
  }

  function rollHeroic() {
    const scores = [];
    for (let i = 0; i < 6; i++) scores.push(roll4d6drop());
    return scores;
  }

  function rollLoaded() {
    const scores = [];
    for (let i = 0; i < 6; i++) scores.push(roll3d6noOnes());
    return scores;
  }

  function doRoll() {
    const m = genState.method;
    if (m === 'legacy') return rollLegacy();
    if (m === 'default') return rollDefault();
    if (m === 'series') return rollSeries();
    if (m === 'heroic') return rollHeroic();
    if (m === 'loaded_dice') return rollLoaded();
    return rollLegacy();
  }

  // ============ UI: CHARACTER LIST ============
  function renderCharList() {
    const list = document.getElementById('character-list');
    if (characters.length === 0) {
      list.innerHTML = '<p style="color:#888;">No characters yet. Click "New Character" to begin.</p>';
      return;
    }
    list.innerHTML = characters.map(c => `
      <div class="char-card" data-id="${c.id}">
        <div>
          <span class="cc-name">${c.name}</span>
          <span class="cc-info">${c.ancestry} ${c.className} L${c.level}</span>
        </div>
        <div class="cc-info">HP ${c.hp}/${c.maxHp}</div>
      </div>
    `).join('');
    list.querySelectorAll('.char-card').forEach(el => {
      el.addEventListener('click', () => loadChar(el.dataset.id));
    });
  }

  function loadChar(id) {
    const c = characters.find(ch => ch.id === id);
    if (!c) return;
    currentCharId = id;
    hideAll();
    renderSheet(c);
    document.getElementById('char-sheet').style.display = 'block';
    document.getElementById('sheet-actions').style.display = 'block';
  }

  // ============ UI: GENERATOR ============
  function hideAll() {
    ['my-characters','gen-steps','char-sheet','sheet-actions'].forEach(id => {
      document.getElementById(id).style.display = 'none';
    });
  }

  function hideGenSteps() {
    document.querySelectorAll('.gen-step').forEach(el => el.style.display = 'none');
  }

  function showGenerator() {
    genState = { method:'legacy', scores:[], assigned:{}, ancestry:null, ancestryData:null, charClass:null, classData:null, humanBonus:null, name:'' };
    hideAll();
    document.getElementById('gen-steps').style.display = 'block';
    document.getElementById('step-attrs').style.display = 'block';
    document.getElementById('cancel-row').style.display = 'block';
    document.getElementById('attr-display').style.display = 'none';
    document.getElementById('assign-ui').style.display = 'none';
    document.getElementById('manual-ui').style.display = 'none';
    document.getElementById('attr-confirm').style.display = 'none';
    initMethodSelect();
  }

  function initMethodSelect() {
    const sel = document.getElementById('method-select');
    const methods = DATA.attributes.rolling_methods;
    sel.innerHTML = Object.entries(methods).map(([k,v]) =>
      `<option value="${k}"${k==='legacy'?' selected':''}>${v.name}</option>`
    ).join('') + `<option value="manual">Manual Entry (type your own scores)</option>`;
    updateMethodDesc();
  }

  function updateMethodDesc() {
    const m = DATA.attributes.rolling_methods[genState.method];
    if (genState.method === 'manual') {
      document.getElementById('method-desc').textContent = 'Type each attribute score directly. For characters rolled at the table or transferred from paper.';
      document.getElementById('roll-btn').style.display = 'none';
      document.getElementById('manual-entry-btn').style.display = 'none';
      showManualEntry();
      return;
    }
    document.getElementById('roll-btn').style.display = '';
    document.getElementById('manual-entry-btn').style.display = '';
    document.getElementById('method-desc').textContent = m ? m.description : '';
    document.getElementById('roll-btn').textContent = genState.method === 'point_buy' ? 'Start Point Buy' : 'Roll Attributes';
  }

  function handleRoll() {
    if (genState.method === 'point_buy') {
      handlePointBuy();
      return;
    }

    const scores = doRoll();
    genState.scores = scores;

    const m = DATA.attributes.rolling_methods[genState.method];
    if (m.assign === 'in_order') {
      // Legacy: assign directly in order
      const attrs = DATA.attributes.attributes;
      genState.assigned = {};
      attrs.forEach((a, i) => { genState.assigned[a] = scores[i]; });
      renderStatBoxes();
      document.getElementById('attr-display').style.display = 'block';
      document.getElementById('assign-ui').style.display = 'none';
      document.getElementById('attr-confirm').style.display = 'block';
    } else {
      // Free assign: show score pool + empty slots
      genState.assigned = {};
      renderFreeAssign(scores);
      document.getElementById('attr-display').style.display = 'none';
      document.getElementById('assign-ui').style.display = 'block';
      document.getElementById('attr-confirm').style.display = 'none';
    }
  }

  function handlePointBuy() {
    genState.assigned = {};
    const attrs = DATA.attributes.attributes;
    attrs.forEach(a => { genState.assigned[a] = 8; });
    genState.pointsLeft = 24;
    renderPointBuy();
    document.getElementById('attr-display').style.display = 'none';
    document.getElementById('assign-ui').style.display = 'block';
    document.getElementById('attr-confirm').style.display = 'block';
    document.getElementById('manual-ui').style.display = 'none';
  }

  function showManualEntry() {
    genState.assigned = {};
    document.getElementById('attr-display').style.display = 'none';
    document.getElementById('assign-ui').style.display = 'none';
    document.getElementById('manual-ui').style.display = 'block';
    document.getElementById('attr-confirm').style.display = 'block';

    const attrs = DATA.attributes.attributes;
    const container = document.getElementById('manual-boxes');
    container.innerHTML = attrs.map(a =>
      `<div class="stat-box">
        <div class="attr-label">${a}</div>
        <input type="number" class="manual-input" data-attr="${a}" min="3" max="25" value="">
        <div class="attr-mod manual-mod" data-attr="${a}"></div>
        <div class="attr-detail manual-detail" data-attr="${a}"></div>
      </div>`
    ).join('');

    container.querySelectorAll('.manual-input').forEach(inp => {
      inp.addEventListener('input', () => updateManualEntry());
    });
  }

  function updateManualEntry() {
    const attrs = DATA.attributes.attributes;
    genState.assigned = {};
    let allFilled = true;
    attrs.forEach(a => {
      const inp = document.querySelector(`.manual-input[data-attr="${a}"]`);
      const modEl = document.querySelector(`.manual-mod[data-attr="${a}"]`);
      const detailEl = document.querySelector(`.manual-detail[data-attr="${a}"]`);
      const v = parseInt(inp.value);
      if (!v || v < 1 || v > 25) {
        allFilled = false;
        modEl.textContent = '';
        detailEl.textContent = '';
        return;
      }
      genState.assigned[a] = v;
      const mod = getCheckMod(v);
      modEl.textContent = formatMod(mod);
      let detail = '';
      if (a === 'STR') detail = `Atk/Dmg ${formatMod(getStrAtkDmg(v))}`;
      else if (a === 'DEX') detail = `Def ${formatMod(getDexDefense(v))}, Ranged ${formatMod(getDexRanged(v))}`;
      else if (a === 'CON') detail = `Fort ${formatMod(getConFort(v))}`;
      else if (a === 'INT') detail = `${getIntLangs(v)} bonus lang`;
      else if (a === 'WIS') detail = `Will ${formatMod(getWisSave(v))}`;
      else if (a === 'CHA') detail = `Influence ${formatMod(getChaInfluence(v))}`;
      detailEl.textContent = detail;
    });
    // Show/hide confirm button
    document.getElementById('attr-confirm').style.display = allFilled ? 'block' : 'none';
  }

  function renderPointBuy() {
    const container = document.getElementById('assign-boxes');
    const attrs = DATA.attributes.attributes;
    const unassigned = document.getElementById('unassigned-scores');
    unassigned.innerHTML = `<strong>Points remaining: ${genState.pointsLeft}</strong>`;

    container.innerHTML = attrs.map(a => {
      const s = genState.assigned[a];
      const mod = getCheckMod(s);
      const canUp = genState.pointsLeft > 0 && s < 16 && (s < 15 || genState.pointsLeft >= 5);
      const canDown = s > 8;
      return `<div class="stat-box">
        <div class="attr-label">${a}</div>
        <div style="display:flex;align-items:center;justify-content:center;gap:.3rem;">
          <button class="btn-small pb-dec" data-attr="${a}" ${canDown?'':'disabled'} style="padding:.1rem .4rem;">-</button>
          <div class="attr-score">${s}</div>
          <button class="btn-small pb-inc" data-attr="${a}" ${canUp?'':'disabled'} style="padding:.1rem .4rem;">+</button>
        </div>
        <div class="attr-mod">${formatMod(mod)}</div>
      </div>`;
    }).join('');

    container.querySelectorAll('.pb-inc').forEach(btn => {
      btn.addEventListener('click', () => {
        const attr = btn.dataset.attr;
        const cost = genState.assigned[attr] >= 15 ? 5 : 1;
        if (genState.pointsLeft >= cost && genState.assigned[attr] < 16) {
          genState.assigned[attr] += 1;
          genState.pointsLeft -= cost;
          renderPointBuy();
        }
      });
    });
    container.querySelectorAll('.pb-dec').forEach(btn => {
      btn.addEventListener('click', () => {
        const attr = btn.dataset.attr;
        if (genState.assigned[attr] > 8) {
          const refund = genState.assigned[attr] > 15 ? 5 : 1;
          genState.assigned[attr] -= 1;
          genState.pointsLeft += refund;
          renderPointBuy();
        }
      });
    });
  }

  function renderStatBoxes() {
    const container = document.getElementById('stat-boxes');
    const attrs = DATA.attributes.attributes;
    container.innerHTML = attrs.map(a => {
      const s = genState.assigned[a];
      const mod = getCheckMod(s);
      let detail = '';
      if (a === 'STR') detail = `Atk/Dmg ${formatMod(getStrAtkDmg(s))}`;
      else if (a === 'DEX') detail = `Def ${formatMod(getDexDefense(s))}, Ranged ${formatMod(getDexRanged(s))}`;
      else if (a === 'CON') detail = `Fort ${formatMod(getConFort(s))}`;
      else if (a === 'INT') detail = `${getIntLangs(s)} bonus lang`;
      else if (a === 'WIS') detail = `Will ${formatMod(getWisSave(s))}`;
      else if (a === 'CHA') detail = `Influence ${formatMod(getChaInfluence(s))}`;
      return `<div class="stat-box">
        <div class="attr-label">${a}</div>
        <div class="attr-score">${s}</div>
        <div class="attr-mod">${formatMod(mod)}</div>
        <div class="attr-detail">${detail}</div>
      </div>`;
    }).join('');
  }

  // Free assignment UI
  let selectedScoreIdx = null;

  function renderFreeAssign(scores) {
    const unassigned = document.getElementById('unassigned-scores');
    const container = document.getElementById('assign-boxes');
    const attrs = DATA.attributes.attributes;

    // Score chips
    const placedScores = Object.values(genState.assigned);
    unassigned.innerHTML = scores.map((s, i) => {
      const placed = placedScores.filter(v => v === s).length;
      const available = scores.filter((v,j) => v === s && j <= i).length - scores.filter((v,j) => v === s && j < i && placedScores.includes(v)).length;
      // Simpler: track by index
      const isPlaced = Object.values(genState.assigned).length > 0 &&
        isScoreUsed(i, scores, genState.assigned);
      const cls = `score-chip${selectedScoreIdx===i?' picked':''}${isPlaced?' placed':''}`;
      return `<div class="${cls}" data-idx="${i}">${s}</div>`;
    }).join('');

    // Attr boxes
    container.innerHTML = attrs.map(a => {
      const s = genState.assigned[a];
      const mod = s ? getCheckMod(s) : '';
      const cls = `stat-box clickable${s?'':' empty'}`;
      return `<div class="${cls}" data-attr="${a}">
        <div class="attr-label">${a}</div>
        <div class="attr-score">${s || '?'}</div>
        <div class="attr-mod">${s ? formatMod(mod) : ''}</div>
      </div>`;
    }).join('');

    // Events
    unassigned.querySelectorAll('.score-chip:not(.placed)').forEach(el => {
      el.addEventListener('click', () => {
        selectedScoreIdx = parseInt(el.dataset.idx);
        renderFreeAssign(scores);
      });
    });
    container.querySelectorAll('.stat-box.clickable').forEach(el => {
      el.addEventListener('click', () => {
        const attr = el.dataset.attr;
        if (selectedScoreIdx !== null) {
          genState.assigned[attr] = scores[selectedScoreIdx];
          selectedScoreIdx = null;
          renderFreeAssign(scores);
          // Check if all assigned
          if (Object.keys(genState.assigned).length === 6) {
            document.getElementById('attr-confirm').style.display = 'block';
          }
        } else if (genState.assigned[attr]) {
          // Unassign
          delete genState.assigned[attr];
          document.getElementById('attr-confirm').style.display = 'none';
          renderFreeAssign(scores);
        }
      });
    });
  }

  function isScoreUsed(idx, scores, assigned) {
    // Track which indices are used
    const usedIndices = [];
    const vals = Object.values(assigned);
    const available = [...scores];
    for (const v of vals) {
      const i = available.indexOf(v);
      if (i >= 0) { usedIndices.push(scores.indexOf(v, usedIndices.filter(ui=>scores[ui]===v).length ? usedIndices.filter(ui=>scores[ui]===v).pop()+1 : 0)); available.splice(i,1); }
    }
    // Simplified approach: just check if this index score is in assigned values
    // This is imperfect but good enough for now
    const assignedVals = Object.values(assigned);
    const scoresUsed = [];
    for (const v of assignedVals) {
      for (let i = 0; i < scores.length; i++) {
        if (scores[i] === v && !scoresUsed.includes(i)) {
          scoresUsed.push(i);
          break;
        }
      }
    }
    return scoresUsed.includes(idx);
  }

  // ============ UI: ANCESTRY ============
  function showAncestryStep() {
    hideGenSteps();
    document.getElementById('step-ancestry').style.display = 'block';

    const finalAttrs = genState.assigned; // pre-ancestry
    const container = document.getElementById('ancestry-options');

    container.innerHTML = DATA.ancestries.map(a => {
      const mods = Object.entries(a.attributes || {}).map(([k,v]) => `${k} ${formatMod(v)}`).join(', ') || 'None';
      const senses = [];
      if (a.senses) {
        if (a.senses.darkvision) senses.push(`Darkvision ${a.senses.darkvision}ft`);
        if (a.senses.lowlight_vision) senses.push(`Low-light Vision${typeof a.senses.lowlight_vision==='number'?' '+a.senses.lowlight_vision+'ft':''}`);
      }
      const traits = (a.traits||[]).map(t => `<span class="card-tag">${t.name}</span>`).join('');
      const speed = a.speed ? `${a.speed}ft` : '30ft';

      return `<div class="option-card" data-id="${a.id}">
        <h3>${a.name}</h3>
        <div class="card-desc">${a.description}</div>
        <div class="card-detail"><strong>Attribute Mods:</strong> ${mods}</div>
        <div class="card-detail"><strong>Speed:</strong> ${speed}${senses.length ? ' | ' + senses.join(', ') : ''}</div>
        <div style="margin-top:.35rem;">${traits}</div>
        ${a.size_note ? `<div class="card-detail" style="color:#b45309;">${a.size_note}</div>` : ''}
      </div>`;
    }).join('');

    container.querySelectorAll('.option-card').forEach(el => {
      el.addEventListener('click', () => selectAncestry(el.dataset.id));
    });
  }

  function selectAncestry(id) {
    const a = DATA.ancestries.find(x => x.id === id);
    genState.ancestry = id;
    genState.ancestryData = a;
    genState.humanBonus = null;

    if (id === 'human') {
      showHumanBonusStep();
    } else {
      showClassStep();
    }
  }

  function showHumanBonusStep() {
    hideGenSteps();
    document.getElementById('step-human-bonus').style.display = 'block';
    const grid = document.getElementById('human-bonus-grid');
    grid.innerHTML = DATA.attributes.attributes.map(a => {
      const current = genState.assigned[a] || 10;
      return `<div class="option-card" data-attr="${a}" style="text-align:center;padding:.75rem;">
        <h3>${a}</h3>
        <div class="card-desc">${current} → ${current + 1}</div>
      </div>`;
    }).join('');
    grid.querySelectorAll('.option-card').forEach(el => {
      el.addEventListener('click', () => {
        genState.humanBonus = el.dataset.attr;
        showClassStep();
      });
    });
  }

  // ============ UI: CLASS ============
  function showClassStep() {
    hideGenSteps();
    document.getElementById('step-class').style.display = 'block';

    const finalAttrs = getFinalAttrs(genState);
    const container = document.getElementById('class-options');
    const note = document.getElementById('class-note');
    note.textContent = `Attributes after ${genState.ancestryData.name} ancestry modifiers are applied.`;

    container.innerHTML = DATA.classes.map(c => {
      const allowed = isClassAllowed(c, genState.ancestryData);
      const restricted = genState.ancestryData.restricted_classes && genState.ancestryData.restricted_classes[c.id];

      // Tags
      let tags = [];
      tags.push(`<span class="card-tag">${c.hit_die} HP</span>`);
      if (c.spellcasting) tags.push(`<span class="card-tag">${c.spellcasting.type} caster</span>`);
      tags.push(`<span class="card-tag">${c.weapon_slots_start} WP slots</span>`);

      // XP note
      const xp = getXpNote(c, finalAttrs);
      let xpTag = '';
      if (xp) {
        if (xp.type === 'bonus') xpTag = `<span class="card-tag" style="background:#e8f5e9;color:#2e7d32;">${xp.text}</span>`;
        else xpTag = `<span class="card-tag warn">${xp.text}</span>`;
      }

      // Restriction note
      let restrictNote = '';
      if (!allowed) {
        restrictNote = `<div class="card-detail" style="color:#c44;">Not available to ${genState.ancestryData.name}</div>`;
      } else if (restricted) {
        restrictNote = `<div class="card-detail" style="color:#b45309;">${restricted}</div>`;
      }

      const cls = `option-card${!allowed?' disabled':''}`;
      return `<div class="${cls}" data-id="${c.id}">
        <h3>${c.name}</h3>
        <div class="card-detail">${c.alignment}</div>
        <div class="card-detail"><strong>Armor:</strong> ${c.armor}</div>
        <div style="margin-top:.35rem;">${tags.join('')}${xpTag}</div>
        ${restrictNote}
      </div>`;
    }).join('');

    container.querySelectorAll('.option-card:not(.disabled)').forEach(el => {
      el.addEventListener('click', () => selectClass(el.dataset.id));
    });
  }

  function selectClass(id) {
    const c = DATA.classes.find(x => x.id === id);
    genState.charClass = id;
    genState.classData = c;
    showNameStep();
  }

  // ============ UI: NAME ============
  function showNameStep() {
    hideGenSteps();
    document.getElementById('step-name').style.display = 'block';
    document.getElementById('char-name-input').focus();
  }

  // ============ CHARACTER CREATION ============
  function createCharacter() {
    const name = document.getElementById('char-name-input').value.trim();
    if (!name) { document.getElementById('char-name-input').focus(); return; }

    const finalAttrs = getFinalAttrs(genState);
    const cls = genState.classData;
    const anc = genState.ancestryData;

    // HP
    const hdMax = hitDieMax(cls.hit_die);
    const conFort = getConFort(finalAttrs.CON || 10);
    const maxHp = Math.max(1, hdMax + conFort);

    // Saves
    const baseSaves = cls.saves_level1 || { fort:4, reflex:4, will:4 };
    const saves = {
      fort: baseSaves.fort + getConFort(finalAttrs.CON || 10),
      reflex: baseSaves.reflex + getDexDefense(finalAttrs.DEX || 10),
      will: baseSaves.will + getWisSave(finalAttrs.WIS || 10)
    };

    // Ancestry save bonuses
    let ancestrySaveBonuses = {};
    if (anc.traits) {
      for (const t of anc.traits) {
        if (t.name === 'Protection from Magic' && anc.id === 'dwarf') {
          ancestrySaveBonuses.fort = (ancestrySaveBonuses.fort||0) + 2;
          ancestrySaveBonuses.will = (ancestrySaveBonuses.will||0) + 1;
        }
        if (t.name === 'Protection from Magic' && anc.id === 'gnome') {
          ancestrySaveBonuses.fort = (ancestrySaveBonuses.fort||0) + 1;
          ancestrySaveBonuses.reflex = (ancestrySaveBonuses.reflex||0) + 1;
          ancestrySaveBonuses.will = (ancestrySaveBonuses.will||0) + 1;
        }
        if (t.name === 'Protection from Magic' && anc.id === 'halfling') {
          ancestrySaveBonuses.will = (ancestrySaveBonuses.will||0) + 2;
        }
        if (t.name === 'Poison Resistance') {
          ancestrySaveBonuses.fort_poison = 2;
        }
        if (t.name === 'Elven Strength of Mind') {
          ancestrySaveBonuses.will_mind = 1;
        }
        if (t.name === 'Strength of Mind') {
          ancestrySaveBonuses.will_mind = 2;
        }
      }
    }
    saves.fort += (ancestrySaveBonuses.fort || 0);
    saves.reflex += (ancestrySaveBonuses.reflex || 0);
    saves.will += (ancestrySaveBonuses.will || 0);

    // BTH from level chart or default 0
    let bth = 0;
    if (cls.level_chart && cls.level_chart.length > 0) {
      bth = cls.level_chart[0].bth || 0;
    }

    // XP note
    const xpNote = getXpNote(cls, finalAttrs);

    // L1 special abilities
    let specials = [];
    if (cls.level_chart && cls.level_chart.length > 0) {
      specials = cls.level_chart[0].special || [];
    }

    const char = {
      id: genId(),
      name,
      ancestry: anc.name,
      ancestryId: anc.id,
      className: cls.name,
      classId: cls.id,
      level: 1,
      xp: 0,
      xpNote: xpNote ? xpNote.text : null,
      xpType: xpNote ? xpNote.type : null,
      baseAttrs: { ...genState.assigned },
      ancestryMods: { ...anc.attributes },
      humanBonus: genState.humanBonus,
      finalAttrs,
      hitDie: cls.hit_die,
      maxHp,
      hp: maxHp,
      ac: 10 + getDexDefense(finalAttrs.DEX || 10),
      bth,
      saves,
      ancestrySaveBonuses,
      speed: anc.speed || 30,
      traits: anc.traits || [],
      senses: anc.senses || {},
      languages: [...(anc.languages || [])],
      specials,
      spellcasting: cls.spellcasting || null,
      wpSlots: cls.weapon_slots_start,
      nwpSlots: cls.nwp_slots_start + (cls.id === 'thief' ? 4 : 0) + (anc.id === 'half-elf' ? 1 : 0),
      nwpGroups: cls.nwp_groups || [],
      armor: cls.armor,
      equipment: [],
      gold: 0,
      notes: '',
      createdAt: new Date().toISOString()
    };

    // Bonus languages from INT
    const bonusLangs = getIntLangs(finalAttrs.INT || 10);
    if (bonusLangs > 0) char.bonusLanguageSlots = bonusLangs;

    characters.push(char);
    saveChars();
    currentCharId = char.id;
    hideAll();
    renderSheet(char);
    document.getElementById('char-sheet').style.display = 'block';
    document.getElementById('sheet-actions').style.display = 'block';
  }

  // ============ CHARACTER SHEET ============
  // ============ CHARACTER SHEET ============
  function recalcChar(char) {
    const fa = char.finalAttrs;
    // Recalc saves
    const cls = DATA.classes.find(c => c.id === char.classId);
    if (cls && cls.saves_level1) {
      char.saves.fort = cls.saves_level1.fort + getConFort(fa.CON||10) + (char.ancestrySaveBonuses.fort||0);
      char.saves.reflex = cls.saves_level1.reflex + getDexDefense(fa.DEX||10) + (char.ancestrySaveBonuses.reflex||0);
      char.saves.will = cls.saves_level1.will + getWisSave(fa.WIS||10) + (char.ancestrySaveBonuses.will||0);
    }
    saveCurrent();
  }

  function getWeaponData() {
    if (!DATA.equipment) return [];
    const all = [];
    (DATA.equipment.weapons?.melee||[]).forEach(w => all.push({...w, category:'melee'}));
    (DATA.equipment.weapons?.ranged||[]).forEach(w => all.push({...w, category:'ranged'}));
    return all;
  }

  function renderSheet(char) {
    const el = document.getElementById('char-sheet');
    const fa = char.finalAttrs;
    if (!char.weapons) char.weapons = [];
    if (!char.equipment) char.equipment = [];
    if (!char.gold) char.gold = 0;

    // Attribute boxes — full derived values like the official sheet
    const attrBoxes = DATA.attributes.attributes.map(a => {
      const s = fa[a] || 10;
      const mod = getCheckMod(s);
      let details = [];
      if (a === 'STR') {
        details.push(`Atk/Dmg: ${formatMod(getStrAtkDmg(s))}`);
        details.push(`Max Wt: ${getWeight(s)} lbs`);
      } else if (a === 'DEX') {
        details.push(`Ranged Atk: ${formatMod(getDexRanged(s))}`);
        details.push(`Defense: ${formatMod(getDexDefense(s))}`);
      } else if (a === 'CON') {
        details.push(`Fort: ${formatMod(getConFort(s))}`);
        const shock = lookup(DATA.attributes.constitution.shock_survival, s);
        details.push(`Shock: ${shock === 'cannot' ? 'Cannot' : shock === 'always' ? 'Always' : 'TN '+shock}`);
      } else if (a === 'INT') {
        details.push(`Bonus Lang: ${getIntLangs(s)}`);
        const bs = lookup(DATA.attributes.intelligence.bonus_arcane_spells, s);
        if (bs && bs !== '0' && bs !== null) details.push(`Arcane Bonus: ${bs}`);
      } else if (a === 'WIS') {
        details.push(`Will: ${formatMod(getWisSave(s))}`);
        const bd = lookup(DATA.attributes.wisdom.bonus_divine_spells, s);
        if (bd && bd !== '0') details.push(`Divine Bonus: ${bd}`);
      } else if (a === 'CHA') {
        details.push(`Influence: ${formatMod(getChaInfluence(s))}`);
        details.push(`Max Hench: ${getChaHench(s)}`);
      }

      const wasMod = (char.ancestryMods && char.ancestryMods[a]) || (char.humanBonus === a);
      return `<div class="ss-box${wasMod?' modified':''}">
        <div class="ss-label">${a}</div>
        <div class="ss-score editable" data-field="attr-${a}" title="Click to edit">${s}</div>
        <div class="ss-mod">${formatMod(mod)} check</div>
        ${details.map(d => `<div style="font-size:.6rem;color:#888;">${d}</div>`).join('')}
      </div>`;
    }).join('');

    // Senses
    let senses = [];
    if (char.senses) {
      if (char.senses.darkvision) senses.push(`Darkvision ${char.senses.darkvision}ft`);
      if (char.senses.lowlight_vision) senses.push(`Low-light Vision${typeof char.senses.lowlight_vision==='number'?' '+char.senses.lowlight_vision+'ft':''}`);
    }

    // Save breakdowns
    const saveHtml = ['fort','reflex','will'].map(s => {
      const label = s === 'fort' ? 'Fortitude' : s === 'reflex' ? 'Reflex' : 'Will';
      const total = char.saves[s];
      let extras = [];
      if (s === 'will' && char.ancestrySaveBonuses?.will_mind)
        extras.push(`+${char.ancestrySaveBonuses.will_mind} vs mind`);
      if (s === 'fort' && char.ancestrySaveBonuses?.fort_poison)
        extras.push(`+${char.ancestrySaveBonuses.fort_poison} vs poison`);
      return `<div class="save-box">
        <div class="save-label">${label}</div>
        <div class="save-total">${formatMod(total)}</div>
        ${extras.length ? `<div style="font-size:.6rem;color:#4a1a6b;">${extras.join(', ')}</div>` : ''}
      </div>`;
    }).join('');

    // XP
    let xpHtml = '';
    if (char.xpNote) xpHtml = `<div class="xp-note ${char.xpType}">${char.xpNote}</div>`;

    // AC variants
    const dexDef = getDexDefense(fa.DEX||10);
    const armorAC = char.armorBonus || 0;
    const shieldAC = char.shieldBonus || 0;
    const miscAC = char.miscACBonus || 0;
    const baseAC = 10 + armorAC + shieldAC + dexDef + miscAC;
    char.ac = baseAC;
    const flatFooted = 10 + armorAC + shieldAC + miscAC;
    const rearAC = Math.max(baseAC - 2, 10);
    const touchAC = 10 + dexDef + miscAC;

    // Traits
    const traitsHtml = (char.traits||[]).map(t =>
      `<div class="trait-box"><strong>${t.name}:</strong> ${t.description}</div>`
    ).join('');

    // Class abilities
    const specialsHtml = (char.specials||[]).map(s =>
      `<div class="trait-box" style="background:#e8f0fe;border-color:#2c5282;"><strong>${s}</strong></div>`
    ).join('');

    // Weapons table
    const weaponRows = char.weapons.map((w, i) => {
      const atkMod = w.category === 'ranged'
        ? char.bth + getDexRanged(fa.DEX||10)
        : char.bth + getStrAtkDmg(fa.STR||10);
      const dmgMod = w.category === 'ranged' ? 0 : getStrAtkDmg(fa.STR||10);
      const dmgStr = dmgMod !== 0 ? `${w.damage}${formatMod(dmgMod)}` : w.damage;
      const lgDmg = w.damage_large || w.damage;
      return `<tr>
        <td>${w.name}</td>
        <td>${formatMod(atkMod)}</td>
        <td>${dmgStr} / ${lgDmg}</td>
        <td class="wt-init">${w.init || '—'}</td>
        <td>${w.range ? w.range+'ft' : '—'}</td>
        <td>${w.type || '—'}</td>
        <td><span class="wt-remove" data-widx="${i}">&times;</span></td>
      </tr>`;
    }).join('');

    // Equipment list
    const equipHtml = char.equipment.map((item, i) =>
      `<div class="equip-item"><span class="ei-name">${item}</span><span class="ei-remove" data-eidx="${i}">&times;</span></div>`
    ).join('');

    // Weapon select options
    const allWeapons = getWeaponData();
    const weaponOpts = allWeapons.map(w =>
      `<option value="${w.name}">${w.name} (${w.damage}, ${w.init||'—'})</option>`
    ).join('');

    el.innerHTML = `<div class="sheet">
      <div class="sheet-header">
        <h1 class="editable" data-field="name" title="Click to rename">${char.name}</h1>
        <div class="sh-subtitle">${char.ancestry} ${char.className} &mdash; Level <span class="editable" data-field="level" title="Click to edit">${char.level}</span></div>
        <div style="font-size:.85rem;color:#666;margin-top:.25rem;">
          XP: <span class="editable" data-field="xp" title="Click to edit">${char.xp}</span>
          ${xpHtml}
        </div>
      </div>

      <h2>Attributes</h2>
      <div class="sheet-stats">${attrBoxes}</div>

      <h2>Hit Points & Armor</h2>
      <div style="display:flex;gap:2rem;flex-wrap:wrap;align-items:flex-start;">
        <div>
          <div class="hp-tracker">
            <button class="hp-btn damage" data-hp="-1">-</button>
            <div class="dg-box" style="min-width:80px;">
              <div class="dg-label">HP</div>
              <div class="dg-value"><span class="editable" data-field="hp" title="Click to edit">${char.hp}</span> / <span class="editable" data-field="maxHp" title="Click to edit">${char.maxHp}</span></div>
            </div>
            <button class="hp-btn heal" data-hp="+1">+</button>
          </div>
          <div style="font-size:.75rem;color:#888;margin-top:.25rem;">Hit Die: ${char.hitDie}</div>
        </div>
        <div>
          <div class="ac-grid">
            <div class="ac-box"><div class="ac-label">AC</div><div class="ac-val">${baseAC}</div></div>
            <div class="ac-box"><div class="ac-label">Flat-Footed</div><div class="ac-val">${flatFooted}</div></div>
            <div class="ac-box"><div class="ac-label">Rear</div><div class="ac-val">${rearAC}</div></div>
            <div class="ac-box"><div class="ac-label">Touch</div><div class="ac-val">${touchAC}</div></div>
          </div>
          <div style="font-size:.75rem;color:#888;">
            Armor: <span class="editable" data-field="armorBonus" title="Armor AC bonus">${char.armorBonus||0}</span> +
            Shield: <span class="editable" data-field="shieldBonus" title="Shield bonus">${char.shieldBonus||0}</span> +
            DEX: ${formatMod(dexDef)} +
            Misc: <span class="editable" data-field="miscACBonus" title="Other bonuses">${char.miscACBonus||0}</span>
          </div>
        </div>
      </div>

      <h2>Combat</h2>
      <div class="derived-grid">
        <div class="dg-box"><div class="dg-label">BTH</div><div class="dg-value">${formatMod(char.bth)}</div></div>
        <div class="dg-box"><div class="dg-label">Melee</div><div class="dg-value">${formatMod(char.bth + getStrAtkDmg(fa.STR||10))}</div></div>
        <div class="dg-box"><div class="dg-label">Ranged</div><div class="dg-value">${formatMod(char.bth + getDexRanged(fa.DEX||10))}</div></div>
        <div class="dg-box"><div class="dg-label">Speed</div><div class="dg-value">${char.speed}ft</div></div>
      </div>

      <h3>Weapons</h3>
      ${char.weapons.length ? `<table class="weapon-table">
        <tr><th>Weapon</th><th>Attack</th><th>Damage (S-M/L)</th><th>Init</th><th>Range</th><th>Type</th><th></th></tr>
        ${weaponRows}
      </table>` : '<p style="color:#888;font-size:.85rem;">No weapons added yet.</p>'}
      <div class="add-row">
        <select id="add-weapon-select"><option value="">Add weapon...</option>${weaponOpts}</select>
        <button id="add-weapon-btn" class="btn-small">Add</button>
      </div>

      <h2>Saving Throws</h2>
      <div class="save-row">${saveHtml}</div>

      <h2>Ancestry Traits</h2>
      ${traitsHtml || '<p style="color:#888;">No special traits.</p>'}
      ${senses.length ? `<div class="card-detail" style="margin-top:.5rem;"><strong>Senses:</strong> ${senses.join(', ')}</div>` : ''}
      <div class="card-detail"><strong>Languages:</strong> ${char.languages.map(l=>l.charAt(0).toUpperCase()+l.slice(1)).join(', ')}${char.bonusLanguageSlots ? ` (+${char.bonusLanguageSlots} bonus slots from INT)` : ''}</div>

      ${specialsHtml ? `<h2>Class Abilities (Level 1)</h2>${specialsHtml}` : ''}

      <h2>Proficiency Slots</h2>
      <div class="derived-grid">
        <div class="dg-box"><div class="dg-label">Weapon Prof</div><div class="dg-value">${char.wpSlots} slots</div></div>
        <div class="dg-box"><div class="dg-label">Nonweapon Prof</div><div class="dg-value">${char.nwpSlots} slots</div></div>
      </div>
      <div class="card-detail"><strong>NWP Groups:</strong> ${char.nwpGroups.join(', ')}</div>
      <div class="card-detail"><strong>Armor Allowed:</strong> ${char.armor}</div>
      ${char.spellcasting ? `<div class="card-detail"><strong>Spellcasting:</strong> ${char.spellcasting.type} (${char.spellcasting.attribute})</div>` : ''}

      <h2>Equipment</h2>
      <div style="margin-bottom:.5rem;font-size:.9rem;"><strong>Gold:</strong> <span class="editable" data-field="gold" title="Click to edit">${char.gold}</span> gp</div>
      ${equipHtml || '<p style="color:#888;font-size:.85rem;">No equipment yet.</p>'}
      <div class="add-row">
        <input type="text" id="add-equip-input" placeholder="Add item...">
        <button id="add-equip-btn" class="btn-small">Add</button>
      </div>

      <h2>Notes</h2>
      <textarea id="notes-field" style="width:100%;min-height:100px;padding:.5rem;border:1px solid #ccc;border-radius:4px;font-family:inherit;">${char.notes||''}</textarea>
    </div>`;

    // ---- ATTACH EVENT LISTENERS ----
    attachSheetListeners(char);
  }

  function attachSheetListeners(char) {
    const el = document.getElementById('char-sheet');

    // Editable fields (click to edit inline)
    el.querySelectorAll('.editable').forEach(span => {
      span.addEventListener('click', () => {
        const field = span.dataset.field;
        const current = span.textContent;
        const isNum = ['hp','maxHp','xp','level','armorBonus','shieldBonus','miscACBonus','gold'].includes(field);
        const isAttr = field && field.startsWith('attr-');

        const input = document.createElement('input');
        input.type = isNum || isAttr ? 'number' : 'text';
        input.value = current;
        input.style.cssText = 'width:' + Math.max(60, current.length * 12) + 'px;font-size:inherit;font-weight:inherit;text-align:center;border:1px solid #4a1a6b;border-radius:3px;padding:.1rem .3rem;';
        span.replaceWith(input);
        input.focus();
        input.select();

        const commit = () => {
          const val = input.value.trim();
          if (isAttr) {
            const attr = field.replace('attr-','');
            const n = Math.max(1, Math.min(25, parseInt(val) || char.finalAttrs[attr]));
            char.finalAttrs[attr] = n;
            char.baseAttrs[attr] = n - (char.ancestryMods?.[attr]||0) - (char.humanBonus===attr?1:0);
            recalcChar(char);
            renderSheet(char);
          } else if (isNum) {
            const n = parseInt(val) || 0;
            char[field] = field === 'level' ? Math.max(1, n) : Math.max(0, n);
            saveCurrent();
            renderSheet(char);
          } else {
            char[field] = val || current;
            saveCurrent();
            renderSheet(char);
          }
        };

        input.addEventListener('blur', commit);
        input.addEventListener('keydown', e => {
          if (e.key === 'Enter') { e.preventDefault(); commit(); }
          if (e.key === 'Escape') { renderSheet(char); }
        });
      });
    });

    // HP +/- buttons
    el.querySelectorAll('.hp-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        const delta = parseInt(btn.dataset.hp);
        char.hp = Math.max(0, Math.min(char.maxHp, char.hp + delta));
        saveCurrent();
        renderSheet(char);
      });
    });

    // Add weapon
    el.querySelector('#add-weapon-btn')?.addEventListener('click', () => {
      const sel = el.querySelector('#add-weapon-select');
      const name = sel.value;
      if (!name) return;
      const allW = getWeaponData();
      const wData = allW.find(w => w.name === name);
      if (!wData) return;
      char.weapons.push({
        name: wData.name,
        damage: wData.damage,
        damage_large: wData.damage_large,
        init: wData.init,
        range: wData.range || null,
        type: wData.type || '',
        category: wData.category,
        groups: wData.groups || []
      });
      saveCurrent();
      renderSheet(char);
    });

    // Remove weapon
    el.querySelectorAll('.wt-remove').forEach(btn => {
      btn.addEventListener('click', () => {
        char.weapons.splice(parseInt(btn.dataset.widx), 1);
        saveCurrent();
        renderSheet(char);
      });
    });

    // Add equipment
    const addEquipBtn = el.querySelector('#add-equip-btn');
    const addEquipInput = el.querySelector('#add-equip-input');
    const doAddEquip = () => {
      const val = addEquipInput.value.trim();
      if (!val) return;
      char.equipment.push(val);
      saveCurrent();
      renderSheet(char);
    };
    addEquipBtn?.addEventListener('click', doAddEquip);
    addEquipInput?.addEventListener('keydown', e => { if (e.key === 'Enter') doAddEquip(); });

    // Remove equipment
    el.querySelectorAll('.ei-remove').forEach(btn => {
      btn.addEventListener('click', () => {
        char.equipment.splice(parseInt(btn.dataset.eidx), 1);
        saveCurrent();
        renderSheet(char);
      });
    });

    // Notes auto-save
    const notesEl = el.querySelector('#notes-field');
    if (notesEl) {
      notesEl.addEventListener('input', () => {
        char.notes = notesEl.value;
        saveCurrent();
      });
    }
  }

  // ============ IMPORT / EXPORT ============
  function exportChar() {
    const c = characters.find(ch => ch.id === currentCharId);
    if (!c) return;
    const blob = new Blob([JSON.stringify(c, null, 2)], {type:'application/json'});
    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob);
    a.download = `${c.name.replace(/[^a-z0-9]/gi,'_')}.json`;
    a.click();
  }

  function importChar() {
    document.getElementById('import-file').click();
  }

  function handleImport(e) {
    const file = e.target.files[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = function(ev) {
      try {
        const c = JSON.parse(ev.target.result);
        if (!c.name || !c.ancestry) { alert('Invalid character file.'); return; }
        c.id = genId(); // new ID to avoid collisions
        characters.push(c);
        saveChars();
        renderCharList();
        loadChar(c.id);
      } catch(err) { alert('Failed to import: ' + err.message); }
    };
    reader.readAsText(file);
    e.target.value = ''; // reset
  }

  function deleteChar() {
    if (!currentCharId) return;
    const c = characters.find(ch => ch.id === currentCharId);
    if (!confirm(`Delete ${c ? c.name : 'this character'}?`)) return;
    characters = characters.filter(ch => ch.id !== currentCharId);
    currentCharId = null;
    saveChars();
    hideAll();
    document.getElementById('my-characters').style.display = 'block';
    renderCharList();
  }

  // ============ EVENT LISTENERS ============
  document.getElementById('new-char-btn').addEventListener('click', showGenerator);
  document.getElementById('import-btn').addEventListener('click', importChar);
  document.getElementById('import-file').addEventListener('change', handleImport);

  document.getElementById('method-select').addEventListener('change', function() {
    genState.method = this.value;
    updateMethodDesc();
    // Reset display
    document.getElementById('attr-display').style.display = 'none';
    document.getElementById('assign-ui').style.display = 'none';
    document.getElementById('attr-confirm').style.display = 'none';
  });

  document.getElementById('roll-btn').addEventListener('click', () => {
    document.getElementById('manual-ui').style.display = 'none';
    handleRoll();
  });
  document.getElementById('manual-entry-btn').addEventListener('click', showManualEntry);
  document.getElementById('reroll-btn').addEventListener('click', () => {
    document.getElementById('manual-ui').style.display = 'none';
    handleRoll();
  });
  document.getElementById('clear-assign-btn').addEventListener('click', () => {
    genState.assigned = {};
    selectedScoreIdx = null;
    if (genState.method === 'point_buy') {
      DATA.attributes.attributes.forEach(a => { genState.assigned[a] = 8; });
      genState.pointsLeft = 24;
      renderPointBuy();
    } else {
      renderFreeAssign(genState.scores);
    }
    document.getElementById('attr-confirm').style.display = 'none';
  });

  document.getElementById('confirm-attrs-btn').addEventListener('click', () => {
    if (Object.keys(genState.assigned).length < 6) return;
    showAncestryStep();
  });

  // Back navigation
  document.getElementById('back-to-attrs-btn').addEventListener('click', () => {
    genState.ancestry = null; genState.ancestryData = null; genState.humanBonus = null;
    hideGenSteps();
    document.getElementById('step-attrs').style.display = 'block';
  });
  document.getElementById('back-to-ancestry-from-human-btn').addEventListener('click', () => {
    genState.humanBonus = null;
    showAncestryStep();
  });
  document.getElementById('back-to-ancestry-btn').addEventListener('click', () => {
    genState.charClass = null; genState.classData = null;
    showAncestryStep();
  });
  document.getElementById('back-to-class-btn').addEventListener('click', () => {
    genState.charClass = null; genState.classData = null;
    showClassStep();
  });

  document.getElementById('create-btn').addEventListener('click', createCharacter);
  document.getElementById('char-name-input').addEventListener('keydown', e => {
    if (e.key === 'Enter') createCharacter();
  });

  document.getElementById('cancel-btn').addEventListener('click', () => {
    hideAll();
    document.getElementById('my-characters').style.display = 'block';
    renderCharList();
  });

  document.getElementById('export-btn').addEventListener('click', exportChar);
  document.getElementById('print-btn').addEventListener('click', () => window.print());
  document.getElementById('delete-btn').addEventListener('click', deleteChar);
  document.getElementById('back-btn').addEventListener('click', () => {
    currentCharId = null;
    hideAll();
    document.getElementById('my-characters').style.display = 'block';
    renderCharList();
  });

  // ============ INIT ============
  loadChars();
  renderCharList();

})();
</script>
