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

    <!-- Step 3b: Alignment -->
    <div id="step-alignment" class="gen-step" style="display:none;">
      <h2>Choose Alignment</h2>
      <button id="back-to-class-from-align-btn" class="btn-back">&larr; Back to Class</button>
      <p id="align-note" style="color:#888;font-size:.9rem;"></p>
      <div id="alignment-options" class="option-grid" style="grid-template-columns:repeat(3,1fr);"></div>
    </div>

    <!-- Step 3c: Ancestry Free Proficiencies -->
    <div id="step-ancestry-profs" class="gen-step" style="display:none;">
      <h2>Ancestry Proficiencies</h2>
      <button id="back-to-align-btn" class="btn-back">&larr; Back to Alignment</button>
      <p style="font-size:.9rem;color:#666;">These are <strong>free</strong> &mdash; they don't cost proficiency slots.</p>
      <div id="ancestry-prof-content"></div>
      <button id="ancestry-prof-done-btn" class="btn-primary" style="margin-top:1rem;">Continue to Weapon Proficiencies &rarr;</button>
    </div>

    <!-- Step 4: Weapon Proficiencies -->
    <div id="step-wp" class="gen-step" style="display:none;">
      <h2>Step 4: Weapon Proficiencies</h2>
      <button id="back-to-class-from-wp-btn" class="btn-back">&larr; Back to Class</button>
      <div id="wp-header" style="margin:.5rem 0;"></div>
      <div id="wp-choices"></div>
      <div id="wp-summary" style="margin-top:.75rem;"></div>
      <button id="wp-done-btn" class="btn-primary" style="margin-top:1rem;">Continue to Nonweapon Proficiencies &rarr;</button>
    </div>

    <!-- Step 5: Nonweapon Proficiencies -->
    <div id="step-nwp" class="gen-step" style="display:none;">
      <h2>Step 5: Nonweapon Proficiencies</h2>
      <button id="back-to-wp-from-nwp-btn" class="btn-back">&larr; Back to Weapon Proficiencies</button>
      <div id="nwp-content"></div>
      <button id="nwp-done-btn" class="btn-primary" style="margin-top:1rem;">Continue to Equipment &rarr;</button>
    </div>

    <!-- Step 5b: Class Talent -->
    <div id="step-talent" class="gen-step" style="display:none;">
      <h2>Class Talent</h2>
      <button id="back-to-nwp-from-talent-btn" class="btn-back">&larr; Back to Nonweapon Proficiencies</button>
      <p style="font-size:.9rem;color:#666;">Choose one class talent for Level 1. <em>Optional &mdash; check with your GM.</em></p>
      <div id="talent-selected" style="display:none;margin:.75rem 0;padding:.5rem .75rem;background:#e8f5e9;border-left:3px solid #2e7d32;border-radius:0 4px 4px 0;">
        <strong id="talent-selected-name"></strong>
        <span id="talent-clear" style="cursor:pointer;color:#c44;margin-left:.5rem;">&times;</span>
      </div>
      <div id="talent-options" class="option-grid" style="grid-template-columns:repeat(auto-fill,minmax(200px,1fr));"></div>
      <button id="talent-done-btn" class="btn-primary" style="margin-top:1rem;">Continue to Equipment &rarr;</button>
      <button id="talent-skip-btn" class="btn-secondary" style="margin-top:1rem;margin-left:.5rem;">Skip (no talent)</button>
    </div>

    <!-- Step 6: Equipment -->
    <div id="step-equip" class="gen-step" style="display:none;">
      <h2>Step 6: Equipment</h2>
      <button id="back-to-nwp-btn" class="btn-back">&larr; Back to Nonweapon Proficiencies</button>

      <div id="equip-gold-roll" style="margin:.75rem 0;">
        <div style="display:flex;align-items:center;gap:1rem;flex-wrap:wrap;">
          <button id="roll-gold-btn" class="btn-primary">Roll Starting Gold</button>
          <span style="color:#999;">or</span>
          <div><label>Set gold: </label><input type="number" id="manual-gold-input" min="0" max="9999" style="width:80px;padding:.3rem;border:1px solid #ccc;border-radius:3px;" placeholder="gp"> <button id="set-gold-btn" class="btn-small">Set</button></div>
        </div>
        <p id="gold-formula" style="font-size:.85rem;color:#888;margin:.5rem 0;"></p>
      </div>

      <div id="equip-shop" style="display:none;">
        <div style="font-size:1.1rem;font-weight:bold;margin:.75rem 0;color:#4a1a6b;">
          Gold: <span id="shop-gold">0</span> gp
        </div>

        <div style="margin-bottom:.5rem;">
          <button class="shop-tab btn-small active" data-tab="armor">Armor & Shields</button>
          <button class="shop-tab btn-small" data-tab="weapons">Weapons</button>
          <button class="shop-tab btn-small" data-tab="gear">Adventuring Gear</button>
        </div>

        <div id="shop-tab-armor" class="shop-panel"></div>
        <div id="shop-tab-weapons" class="shop-panel" style="display:none;"></div>
        <div id="shop-tab-gear" class="shop-panel" style="display:none;"></div>

        <h3 style="margin-top:1rem;">Your Gear</h3>
        <div id="shop-cart"></div>

        <button id="equip-done-btn" class="btn-primary" style="margin-top:1rem;">Continue to Name &rarr;</button>
      </div>
    </div>

    <!-- Step 7: Name -->
    <div id="step-name" class="gen-step" style="display:none;">
      <h2>Step 7: Name Your Character</h2>
      <button id="back-to-equip-btn" class="btn-back">&larr; Back to Equipment</button>
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

  /* Shop */
  .shop-tab{margin-right:.25rem;}
  .shop-tab.active{background:#4a1a6b;color:#fff;border-color:#4a1a6b;}
  .shop-panel{max-height:350px;overflow-y:auto;border:1px solid #eee;border-radius:4px;padding:.5rem;}
  .shop-item{display:flex;align-items:center;gap:.5rem;padding:.35rem .5rem;border-bottom:1px solid #f0f0f0;font-size:.85rem;cursor:pointer;transition:background .1s;}
  .shop-item:hover{background:#faf5ff;}
  .shop-item.cant-afford{opacity:.4;cursor:not-allowed;}
  .shop-item .si-name{flex:1;font-weight:500;}
  .shop-item .si-detail{color:#888;font-size:.8rem;}
  .shop-item .si-cost{font-weight:bold;color:#4a1a6b;min-width:50px;text-align:right;}
  .shop-item .si-buy{color:#4a1a6b;font-weight:bold;font-size:.9rem;}
  .cart-item{display:flex;align-items:center;gap:.5rem;padding:.3rem 0;border-bottom:1px solid #f0f0f0;font-size:.85rem;}
  .cart-item .ci-name{flex:1;}
  .cart-item .ci-cost{color:#888;min-width:50px;text-align:right;}
  .cart-item .ci-remove{color:#c44;cursor:pointer;font-size:.75rem;opacity:.5;}
  .cart-item .ci-remove:hover{opacity:1;}

  /* Expandable abilities */
  .ability-box{background:#e8f0fe;padding:.5rem .75rem;margin:.25rem 0;border-left:3px solid #2c5282;border-radius:0 4px 4px 0;cursor:pointer;transition:all .15s;}
  .ability-box:hover{background:#dbe6fb;}
  .ability-box .ab-name{font-weight:bold;color:#2c5282;display:flex;justify-content:space-between;align-items:center;}
  .ability-box .ab-arrow{font-size:.7rem;color:#888;transition:transform .2s;}
  .ability-box.open .ab-arrow{transform:rotate(90deg);}
  .ability-box .ab-desc{display:none;margin-top:.35rem;font-size:.85rem;color:#444;line-height:1.4;}
  .ability-box.open .ab-desc{display:block;}
</style>

<script>
(async function() {
  // ============ DATA LOADING ============
  const BASE = '/magicandmyth';
  let DATA = {};

  try {
    const safeFetch = (url, fallback) => fetch(url).then(r => { if (!r.ok) throw new Error(r.status); return r.json(); }).catch(e => { console.warn('Failed to load', url, e); return fallback; });
    const [ancestries, classes, attributes, equipment, proficiencies, spellsIndex] = await Promise.all([
      safeFetch(`${BASE}/data/ancestries.json`, { ancestries:[] }),
      safeFetch(`${BASE}/data/classes.json`, { classes:[] }),
      safeFetch(`${BASE}/data/attributes.json`, {}),
      safeFetch(`${BASE}/data/equipment.json`, {}),
      safeFetch(`${BASE}/data/proficiencies.json`, {}),
      safeFetch(`${BASE}/data/spells-index.json`, { arcane:{}, divine:{} })
    ]);
    DATA = { ancestries: ancestries.ancestries || [], classes: classes.classes || [], attributes, equipment, proficiencies, spells: spellsIndex };
  } catch(e) {
    document.getElementById('loading').textContent = 'Failed to load game data: ' + e.message + '. Please refresh.';
    console.error(e);
    return;
  }

  // Class ability descriptions for L1
  const CLASS_ABILITIES = {
    barbarian: [
      { name: "Danger Sense", desc: "Instincts alert the barbarian to sneak attacks from behind. On a successful Wisdom check, sneak attack bonuses are nullified and the attack is treated as normal damage." },
      { name: "Home Terrain", desc: "Choose a terrain where you gain +2 on trained General and Nature proficiencies. This reflects familiarity with your homeland." },
      { name: "Warrior Prowess", desc: "STR 15+ grants +1 to Strength-based attacks and damage. CON 15+ grants +1 hit point per level." },
      { name: "Wilderness Skills", desc: "Minor proficiency in Wilderness Survival plus one bonus rogue proficiency from: Acrobatics, Animal Handling, Climbing, Set Snares, or Tracking." }
    ],
    bard: [
      { name: "Bard Knowledge +1", desc: "Bards accumulate knowledge of people, places, and exotic things. At 1st level, +1 bonus to bardic knowledge checks for recalling information." },
      { name: "Bardic Performance +1", desc: "Minor proficiency in Performance. Choose two expressions. +1 bonus to Performance checks. Can use performance for Bardic Charm, Counter Song, or Inspire Allies." },
      { name: "Rogue Proficiencies", desc: "Free Minor proficiency in both Decipher Script and Perception at character creation." }
    ],
    cleric: [
      { name: "Level 1 Spells", desc: "Granted divine spells through prayer. 1 spell per day at 1st level, with access to divine spheres based on your faith." },
      { name: "Religion", desc: "Free Minor proficiency in Religion at character creation." },
      { name: "Spontaneous Cure Spells", desc: "Exchange any prepared spell for a cure spell of equal or lesser level without needing to prepare cure spells beforehand." },
      { name: "Turn Undead (1d6)", desc: "Present your holy symbol to turn or control undead creatures. Successful Wisdom check turns 1d6 HD of undead plus your Influence modifier." }
    ],
    druid: [
      { name: "Home Terrain", desc: "Choose a home terrain for +2 on Nature proficiency checks. This terrain also determines which animal forms you can access for shapechange." },
      { name: "Level 1 Spells", desc: "Granted divine spells through prayer. 1 spell per day at 1st level. Major access to animal, elemental, healing, plant, and weather spheres." },
      { name: "Nature Proficiencies", desc: "Free Minor proficiency in Animal Handling, Tracking, and Wilderness Survival." },
      { name: "Wild Speak", desc: "Speak Druidic, a language only druids understand. At 1st level and every other level, become fluent in one woodland creature language." }
    ],
    fighter: [
      { name: "Warrior Prowess", desc: "STR 15+ grants +1 to Strength-based attacks and damage. CON 15+ grants +1 hit point per level." },
      { name: "Weapon Specialization", desc: "Choose one proficient melee weapon to specialize in: +1 attack bonus, +2 damage bonus, and increased attack rate (3/2 at levels 1-6). Fighter only." }
    ],
    monk: [
      { name: "Intuitive Defense", desc: "Gain a bonus to AC equal to half your Wisdom check modifier. Nullified if wearing armor or using a shield." },
      { name: "Ki Powers", desc: "Perform special martial arts maneuvers from ki energy. At 1st level: Iron Fist, Stunning Strike, Leap Kick, Sweep, and two Deflect abilities. Uses per day = level + half WIS modifier." },
      { name: "Martial Arts Specialization", desc: "Proficiency and specialization in martial arts: +1 to hit, +2 damage with unarmed strikes (1d6 damage for Medium characters)." }
    ],
    paladin: [
      { name: "Aura of Good", desc: "+1 bonus to AC and saving throws for non-evil allies within 10 feet. Non-evil subjects are also immune to mental control from evil creatures with equal or fewer HD." },
      { name: "Detect Evil", desc: "Concentrate for one round to detect evil subjects within a 60-foot cone, including evil intentions in otherwise non-evil creatures." },
      { name: "Healing Touch", desc: "Heal up to 2 HP per level per day through laying on hands. Can also damage undead/evil summoned creatures or cure disease once per five levels." },
      { name: "Immunity to Disease", desc: "Immune to all nonmagical disease." },
      { name: "Warrior Prowess", desc: "STR 15+ grants +1 to Strength-based attacks and damage. CON 15+ grants +1 hit point per level." }
    ],
    ranger: [
      { name: "Animal Empathy", desc: "Free Minor proficiency in Animal Handling. When approaching an animal alone, force a Wisdom save or it becomes less hostile. Cumulative -1 penalty per three ranger levels." },
      { name: "Nature Proficiencies", desc: "Free Minor proficiency in Stealth (wilderness only), Wilderness Survival, and Tracking." },
      { name: "Two Weapon Style", desc: "Free proficiency in two-weapon fighting without normal attack penalties. Heavy armor imposes -2 primary and -4 off-hand; lighter armor has no penalty." },
      { name: "Warrior Prowess", desc: "STR 15+ grants +1 to Strength-based attacks and damage. CON 15+ grants +1 hit point per level." }
    ],
    thief: [
      { name: "Dexterous Climb", desc: "Free Minor proficiency in Climb. May use Dexterity check modifier instead of Strength for all climbing checks." },
      { name: "Nimble Defense", desc: "DEX 15+ grants +1 bonus to AC when wearing no armor or any thief-allowed armor." },
      { name: "Opportune Attack +1", desc: "When a target is engaged with multiple opponents, gain +1 to melee and ranged attack and damage rolls against that target." },
      { name: "Rogue Skills", desc: "4 additional nonweapon proficiency slots at character creation that must be spent on rogue proficiencies." },
      { name: "Sneak Attack x2", desc: "When attacking a surprised or unaware target with a small piercing weapon from behind, +4 to attack and double damage (x2 at levels 1-4)." },
      { name: "Trap Lore", desc: "Can use Find and Remove Traps to detect and disable both mechanical and magical traps." }
    ],
    wizard: [
      { name: "Specialization", desc: "Choose whether to specialize in one school of magic or remain a generalist. Specialists gain +1 spell/day in specialty, +2 to learn specialty spells, but face restricted school access." },
      { name: "Spellbook", desc: "Possess a spellbook of arcane spells in literary form. Each spell requires pages equal to its level. Essential for retaining spell knowledge." },
      { name: "Spellcraft", desc: "Free Minor proficiency in Spellcraft at character creation." },
      { name: "Level 1 Spells", desc: "Memorize arcane spells from your spellbook. 1 spell slot per day at 1st level. Begin with a number of 1st-level spells equal to half your Intelligence score." }
    ]
  };

  // Alignment data
  const ALIGNMENT_NAMES = {
    'LG':'Lawful Good','NG':'Neutral Good','CG':'Chaotic Good',
    'LN':'Lawful Neutral','TN':'True Neutral','CN':'Chaotic Neutral',
    'LE':'Lawful Evil','NE':'Neutral Evil','CE':'Chaotic Evil'
  };
  function getAllowedAlignments(alignText) {
    if (!alignText) return Object.keys(ALIGNMENT_NAMES);
    const t = alignText.toLowerCase();
    if (t === 'any') return Object.keys(ALIGNMENT_NAMES);
    if (t.includes('true neutral')) return ['TN'];
    if (t === 'lawful good') return ['LG'];
    if (t.includes('any non-lawful')) return ['NG','CG','TN','CN','NE','CE'];
    if (t.includes('any lawful')) return ['LG','LN','LE'];
    if (t.includes('any good')) return ['LG','NG','CG'];
    if (t.includes('any neutral') || t.includes('neutrality on one axis')) return ['NG','LN','TN','CN','NE'];
    if (t.includes('within one step')) return Object.keys(ALIGNMENT_NAMES); // GM decides
    return Object.keys(ALIGNMENT_NAMES);
  }

  // Class free NWPs (don't cost slots)
  const CLASS_FREE_NWPS = {
    barbarian: [
      { name:"Wilderness Survival", attr:"WIS", group:"Nature", slots:0, level:"Minor", bonus:2, free:true }
    ],
    bard: [
      { name:"Decipher Script", attr:"INT", group:"Rogue", slots:0, level:"Minor", bonus:2, free:true },
      { name:"Perception", attr:"INT", group:"Rogue", slots:0, level:"Minor", bonus:2, free:true }
    ],
    cleric: [
      { name:"Religion", attr:"INT", group:"Academic", slots:0, level:"Minor", bonus:2, free:true }
    ],
    druid: [
      { name:"Animal Handling", attr:"WIS", group:"Nature", slots:0, level:"Minor", bonus:2, free:true },
      { name:"Tracking", attr:"WIS", group:"Nature", slots:0, level:"Minor", bonus:2, free:true },
      { name:"Wilderness Survival", attr:"WIS", group:"Nature", slots:0, level:"Minor", bonus:2, free:true }
    ],
    fighter: [],
    monk: [],
    paladin: [],
    ranger: [
      { name:"Animal Handling", attr:"WIS", group:"Nature", slots:0, level:"Minor", bonus:2, free:true },
      { name:"Stealth (wilderness)", attr:"DEX", group:"Rogue", slots:0, level:"Minor", bonus:2, free:true },
      { name:"Wilderness Survival", attr:"WIS", group:"Nature", slots:0, level:"Minor", bonus:2, free:true },
      { name:"Tracking", attr:"WIS", group:"Nature", slots:0, level:"Minor", bonus:2, free:true }
    ],
    thief: [
      { name:"Climbing (DEX)", attr:"DEX", group:"General", slots:0, level:"Minor", bonus:2, free:true }
    ],
    wizard: [
      { name:"Spellcraft", attr:"INT", group:"Academic", slots:0, level:"Minor", bonus:2, free:true }
    ]
  };

  // Class talents available at Level 1 (excludes those with Minimum Level > 1 or talent prerequisites)
  const CLASS_TALENTS_L1 = {
    barbarian: ["Ancestral Steel","Attribute Training","Blind Fighting","Combat Defense","Crude Weapons Master","Endurance","Hunter's Scent","Intimidate","Mounted Combat","Nomad","Power Attack","Show of Strength","Vicious Hurler","Ward of Fury"],
    bard: ["Ambidexterity","Arcane Lore","Attribute Training","Bardic Lure","Battle Lore","Blind Fighting","Broaden Horizon","Credent Sage","Dagger Toss","Display Weaponry","Fame","Mounted Combat","Poison Use","Theology"],
    cleric: ["Aligned Servant","Attribute Training","Battle Blessing","Detect Necromancy","Detect Residue","Divine Focus","Expeditious Healing","Inquisitor","Interaction","Leadership","Mounted Combat","Prophet","Scholar","Theology","Turn Target","Undead Hunter"],
    druid: ["Attribute Training","Beastmaster","Cast Nature","Companion Training","Hand of Death","Lesser Grove","Mounted Combat","Storm Mastery","Terrain Walker","Theology","Totem","Worldly Stride"],
    fighter: ["Allied Fighting","Ambidexterity","Assess Wounds","Attribute Training","Blind Fighting","Combat Defense","Fighter's Mark","Intimidate","Knowledge of Weapons and Armor","Leadership","Manipulate Field","Mounted Combat","Power Attack","Ranged Mastery","Rogue's Life","Show of Strength","Single-Minded","Spirit of Steel"],
    monk: ["Accelerated Ki","Ambidexterity","Attribute Training","Blind Fighting","Death Attack","Flurry of Blows","Jump Initiative","Mounted Combat","Pathfinder","Poison Use","Ranged Mastery","Roguish Bent","Scholar","Self Defense","Theology","Urban Tracker"],
    paladin: ["Allied Fighting","Attribute Training","Blind Fighting","Companion Training","Detect Necromancy","Detect Residue","Divine Aid","Holy Light","Interaction","Leadership","Manipulate Field","Mounted Combat","Scholar"],
    ranger: ["Attribute Training","Beastmaster","Blind Fighting","Companion Training","Defender","Mounted Combat","Poison Use","Primitive Empathy","Ranged Mastery","Ranger's Bane","Two Weapon Mastery","Urban Stealth","Urban Tracker"],
    thief: ["Ambidexterity","Attribute Training","Blind Fighting","Dagger Toss","Death Attack","Dirty Fighting","Fast Talking","Fence","Jump Initiative","Mounted Combat","Nonweapon Mastery","Poison Use","Ranged Mastery","Silver Tongue","Theology","Thug Minded","Trailing"],
    wizard: ["Arcane Force","Attribute Training","Item Lore","Light Armor Use","Mounted Combat","Offensive Spell Mastery I","Scholar","Theology","Versatile Casting","Wandcraft"]
  };

  // Map talent name to which page file it lives on
  function getTalentPageLink(name) {
    const first = name.charAt(0).toUpperCase();
    let page;
    if (first <= 'C') page = 'talents-a-c';
    else if (first <= 'H') page = 'talents-d-h';
    else if (first <= 'N') page = 'talents-i-n';
    else if (first <= 'S') page = 'talents-o-s';
    else page = 'talents-t-z';
    const anchor = name.toLowerCase().replace(/[^a-z0-9]+/g, '-').replace(/-+$/, '');
    return `${BASE}/players/class-talents/${page}#${anchor}`;
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
    alignment: null,
    ancestryFreeWPs: [],
    ancestryFreeNWPs: [],
    name: ''
  };

  // ============ STORAGE ============
  function loadChars() {
    try { characters = JSON.parse(localStorage.getItem(STORAGE_KEY)) || []; }
    catch(e) { characters = []; }
    // Schema-based migration: every field the sheet uses, with its default.
    // Adding a new field to the chargen? Add it here too. Existing characters
    // will get the default on next load instead of breaking.
    const CHAR_SCHEMA = {
      // Identity
      id: () => genId(), name: 'Unknown', ancestry: 'Human', ancestryId: 'human',
      className: 'Fighter', classId: 'fighter', level: 1, xp: 0,
      xpNote: null, xpType: null, alignment: '', classTalent: null,
      // Attributes
      baseAttrs: () => ({ STR:10, DEX:10, CON:10, INT:10, WIS:10, CHA:10 }),
      ancestryMods: () => ({}), humanBonus: null,
      finalAttrs: () => ({ STR:10, DEX:10, CON:10, INT:10, WIS:10, CHA:10 }),
      // Combat
      hitDie: 'd10', maxHp: 1, hp: 1, ac: 10, bth: 0, speed: 30,
      armorBonus: 0, shieldBonus: 0, miscACBonus: 0,
      // Proficiencies
      weaponProficiencies: () => [], nonweaponProficiencies: () => [],
      wpSlots: 2, nwpSlots: 3, nwpGroups: () => ['General'],
      // Equipment
      weapons: () => [], equipment: () => [], gold: 0, armor: 'Any',
      // Spellcasting
      spellcasting: null, preparedSpells: () => [], spellbook: () => [],
      // Traits and abilities
      traits: () => [], senses: () => ({}), specials: () => [],
      languages: () => ['common'], baseLanguageCount: 1, bonusLanguageSlots: 0,
      // Meta
      notes: '', createdAt: () => new Date().toISOString()
    };

    characters.forEach(c => {
      // Apply defaults for any missing fields
      for (const [key, dflt] of Object.entries(CHAR_SCHEMA)) {
        if (c[key] === undefined || c[key] === null) {
          c[key] = typeof dflt === 'function' ? dflt() : dflt;
        }
      }
      // Derive IDs if missing
      if (!c.classId || c.classId === 'fighter') {
        if (c.className) c.classId = c.className.toLowerCase();
      }
      if (!c.ancestryId || c.ancestryId === 'human') {
        if (c.ancestry && c.ancestry !== 'Human') c.ancestryId = c.ancestry.toLowerCase().replace(/\s+/g, '-');
      }
      // Migrate string specials to objects with descriptions
      if (c.specials && c.specials.length > 0 && typeof c.specials[0] === 'string') {
        const abilities = CLASS_ABILITIES[c.classId] || [];
        c.specials = c.specials.map(name => {
          const found = abilities.find(a => a.name === name);
          return found || { name, desc: null };
        });
      }
    });
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
    genState = { method:'legacy', scores:[], assigned:{}, ancestry:null, ancestryData:null, charClass:null, classData:null, humanBonus:null, alignment:null, ancestryFreeWPs:[], ancestryFreeNWPs:[], name:'' };
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
    showAlignmentStep();
  }

  // ============ UI: ALIGNMENT ============
  function showAlignmentStep() {
    const cls = genState.classData;
    const allowed = getAllowedAlignments(cls.alignment);
    // If only one option, auto-select and skip
    if (allowed.length === 1) {
      genState.alignment = allowed[0];
      showAncestryProfsStep();
      return;
    }
    hideGenSteps();
    document.getElementById('step-alignment').style.display = 'block';
    document.getElementById('align-note').textContent = `${cls.name} alignment: ${cls.alignment}`;
    const grid = document.getElementById('alignment-options');
    grid.innerHTML = allowed.map(a =>
      `<div class="option-card" data-align="${a}" style="text-align:center;padding:.75rem;">
        <h3 style="font-size:.95rem;">${ALIGNMENT_NAMES[a]}</h3>
      </div>`
    ).join('');
    grid.querySelectorAll('.option-card').forEach(el => {
      el.addEventListener('click', () => {
        genState.alignment = el.dataset.align;
        showAncestryProfsStep();
      });
    });
  }

  // ============ UI: ANCESTRY FREE PROFICIENCIES ============
  function showAncestryProfsStep() {
    const anc = genState.ancestryData;
    const hasWeaponChoices = anc.free_weapon_choices && anc.free_weapon_choices.some(c => c.choose > 0);
    const hasNWPChoices = anc.free_nwp_choices && anc.free_nwp_choices.some(c => c.choose > 0);
    const hasAmbitiousStart = anc.ambitious_start_choice;
    // If no choices needed, auto-collect and skip
    if (!hasWeaponChoices && !hasNWPChoices && !hasAmbitiousStart) {
      genState.ancestryFreeWPs = [];
      genState.ancestryFreeNWPs = [];
      // Auto-add any auto items
      if (anc.free_weapon_choices) {
        for (const c of anc.free_weapon_choices) {
          if (c.auto) genState.ancestryFreeWPs.push(...c.auto.map(n => ({ type:'ancestry_free', name:n, slots:0 })));
        }
      }
      if (anc.free_nwp_choices) {
        for (const c of anc.free_nwp_choices) {
          if (c.choose === 0) genState.ancestryFreeNWPs.push(...c.options.map(o => ({...o, free:true, slots:0})));
        }
      }
      showWPStep();
      return;
    }
    hideGenSteps();
    document.getElementById('step-ancestry-profs').style.display = 'block';
    genState.ancestryFreeWPs = [];
    genState.ancestryFreeNWPs = [];
    // Auto-add auto items
    if (anc.free_weapon_choices) {
      for (const c of anc.free_weapon_choices) {
        if (c.auto) genState.ancestryFreeWPs.push(...c.auto.map(n => ({ type:'ancestry_free', name:n, slots:0 })));
      }
    }
    if (anc.free_nwp_choices) {
      for (const c of anc.free_nwp_choices) {
        if (c.choose === 0) genState.ancestryFreeNWPs.push(...c.options.map(o => ({...o, free:true, slots:0})));
      }
    }
    renderAncestryProfs();
  }

  function renderAncestryProfs() {
    const anc = genState.ancestryData;
    const container = document.getElementById('ancestry-prof-content');
    let html = '';
    // Show auto-added items
    if (genState.ancestryFreeWPs.length > 0) {
      html += `<div style="margin-bottom:.75rem;"><strong>Automatic:</strong> ${genState.ancestryFreeWPs.map(w => w.name).join(', ')}</div>`;
    }
    if (genState.ancestryFreeNWPs.length > 0) {
      html += `<div style="margin-bottom:.75rem;"><strong>Free skills:</strong> ${genState.ancestryFreeNWPs.map(n => n.name).join(', ')}</div>`;
    }
    // Show choices needed
    if (anc.free_weapon_choices) {
      for (const choice of anc.free_weapon_choices) {
        if (choice.choose > 0) {
          const picked = genState.ancestryFreeWPs.filter(w => w.choiceLabel === choice.label);
          const remaining = choice.choose - picked.length;
          html += `<h3 style="font-size:.95rem;color:#4a1a6b;margin:.75rem 0 .5rem 0;">${choice.label} (choose ${choice.choose})</h3>`;
          if (picked.length > 0) {
            html += `<div style="margin-bottom:.5rem;color:#2e7d32;">${picked.map((p,i) => `<strong>${p.name}</strong> <span class="wt-remove" data-rmfwp="${i}" style="cursor:pointer;color:#c44;">&times;</span>`).join(', ')}</div>`;
          }
          if (remaining > 0) {
            html += `<div class="option-grid" style="grid-template-columns:repeat(auto-fill,minmax(180px,1fr));">`;
            for (const opt of choice.options) {
              const alreadyPicked = picked.some(p => p.name === opt);
              html += `<div class="option-card${alreadyPicked?' disabled':''}" data-action="pick-free-wp" data-name="${opt}" data-label="${choice.label}" style="padding:.5rem .75rem;">
                <strong style="font-size:.9rem;">${opt}</strong>
              </div>`;
            }
            html += `</div>`;
          }
        }
      }
    }
    if (anc.free_nwp_choices) {
      for (const choice of anc.free_nwp_choices) {
        if (choice.choose > 0) {
          const picked = genState.ancestryFreeNWPs.filter(n => n.choiceLabel === choice.label);
          const remaining = choice.choose - picked.length;
          html += `<h3 style="font-size:.95rem;color:#4a1a6b;margin:.75rem 0 .5rem 0;">${choice.label} (choose ${choice.choose})</h3>`;
          if (picked.length > 0) {
            html += `<div style="margin-bottom:.5rem;color:#2e7d32;">${picked.map(p => `<strong>${p.name}</strong>`).join(', ')}</div>`;
          }
          if (remaining > 0) {
            html += `<div class="option-grid" style="grid-template-columns:repeat(auto-fill,minmax(220px,1fr));">`;
            for (const opt of choice.options) {
              const alreadyPicked = picked.some(p => p.name === opt.name);
              html += `<div class="option-card${alreadyPicked?' disabled':''}" data-action="pick-free-nwp" data-name="${opt.name}" data-attr="${opt.attr}" data-group="${opt.group}" data-label="${choice.label}" style="padding:.5rem .75rem;">
                <strong style="font-size:.9rem;">${opt.name}</strong> <span style="color:#888;font-size:.8rem;">(${opt.attr})</span>
              </div>`;
            }
            html += `</div>`;
          }
        }
      }
    }
    // Ambitious Start choice (half-elf: +1 WP or +1 NWP)
    if (anc.ambitious_start_choice) {
      html += `<h3 style="font-size:.95rem;color:#4a1a6b;margin:.75rem 0 .5rem 0;">Ambitious Start: Bonus Slot</h3>`;
      const choice = genState.ambitiousStartChoice || null;
      html += `<div class="option-grid" style="grid-template-columns:repeat(2,1fr);">
        <div class="option-card${choice==='wp'?' disabled':''}" data-action="ambitious-wp" style="padding:.75rem;text-align:center;${choice==='wp'?'border-color:#2e7d32;background:#e8f5e9;':''}">
          <strong>+1 Weapon Proficiency Slot</strong>
          <div style="font-size:.8rem;color:#666;">Extra weapon training</div>
        </div>
        <div class="option-card${choice==='nwp'?' disabled':''}" data-action="ambitious-nwp" style="padding:.75rem;text-align:center;${choice==='nwp'?'border-color:#2e7d32;background:#e8f5e9;':''}">
          <strong>+1 Nonweapon Proficiency Slot</strong>
          <div style="font-size:.8rem;color:#666;">Extra skill training</div>
        </div>
      </div>`;
      if (choice) {
        html += `<div style="font-size:.85rem;color:#2e7d32;margin-top:.25rem;">Chosen: +1 ${choice==='wp'?'Weapon':'Nonweapon'} Proficiency slot <span data-action="ambitious-clear" style="cursor:pointer;color:#c44;">&times; change</span></div>`;
      }
    }

    // Check if all choices are made
    let allDone = true;
    if (anc.ambitious_start_choice && !genState.ambitiousStartChoice) allDone = false;
    if (anc.free_weapon_choices) {
      for (const c of anc.free_weapon_choices) {
        if (c.choose > 0 && genState.ancestryFreeWPs.filter(w => w.choiceLabel === c.label).length < c.choose) allDone = false;
      }
    }
    if (anc.free_nwp_choices) {
      for (const c of anc.free_nwp_choices) {
        if (c.choose > 0 && genState.ancestryFreeNWPs.filter(n => n.choiceLabel === c.label).length < c.choose) allDone = false;
      }
    }
    document.getElementById('ancestry-prof-done-btn').style.display = allDone ? '' : 'none';
    container.innerHTML = html;
    // Events
    container.querySelectorAll('[data-action="pick-free-wp"]').forEach(el => {
      el.addEventListener('click', () => {
        genState.ancestryFreeWPs.push({ type:'ancestry_free', name: el.dataset.name, choiceLabel: el.dataset.label, slots:0 });
        renderAncestryProfs();
      });
    });
    container.querySelectorAll('[data-action="pick-free-nwp"]').forEach(el => {
      el.addEventListener('click', () => {
        genState.ancestryFreeNWPs.push({ name: el.dataset.name, attr: el.dataset.attr, group: el.dataset.group, choiceLabel: el.dataset.label, slots:0, level:'Minor', bonus:2, free:true });
        renderAncestryProfs();
      });
    });
    container.querySelectorAll('[data-action="ambitious-wp"]').forEach(el => {
      el.addEventListener('click', () => { genState.ambitiousStartChoice = 'wp'; renderAncestryProfs(); });
    });
    container.querySelectorAll('[data-action="ambitious-nwp"]').forEach(el => {
      el.addEventListener('click', () => { genState.ambitiousStartChoice = 'nwp'; renderAncestryProfs(); });
    });
    container.querySelectorAll('[data-action="ambitious-clear"]').forEach(el => {
      el.addEventListener('click', () => { genState.ambitiousStartChoice = null; renderAncestryProfs(); });
    });
    container.querySelectorAll('[data-rmfwp]').forEach(el => {
      el.addEventListener('click', () => {
        const choiceWPs = genState.ancestryFreeWPs.filter(w => w.choiceLabel);
        choiceWPs.splice(parseInt(el.dataset.rmfwp), 1);
        genState.ancestryFreeWPs = genState.ancestryFreeWPs.filter(w => !w.choiceLabel).concat(choiceWPs);
        renderAncestryProfs();
      });
    });
  }

  // ============ UI: WEAPON PROFICIENCIES ============
  function wpRemaining() {
    const ambitiousWP = genState.ambitiousStartChoice === 'wp' ? 1 : 0;
    const total = (genState.classData?.weapon_slots_start || 0) + (genState.ancestryData?.bonus_wp_slots || 0) + ambitiousWP;
    const used = (genState.wpChoices||[]).reduce((s,c) => s + (c.slots||1), 0);
    return total - used;
  }

  function wpAddChoice(choice) {
    if (wpRemaining() < choice.slots) return;
    genState.wpChoices.push(choice);
    showWPStep();
  }

  function showWPStep() {
    hideGenSteps();
    document.getElementById('step-wp').style.display = 'block';
    if (!genState.wpChoices) genState.wpChoices = [];

    const cls = genState.classData;
    const ambitiousWP = genState.ambitiousStartChoice === 'wp' ? 1 : 0;
    const totalSlots = cls.weapon_slots_start + (genState.ancestryData?.bonus_wp_slots || 0) + ambitiousWP;
    const remaining = wpRemaining();
    const classGroups = cls.weapon_groups || [];
    const hasAllGroups = classGroups.includes('all');
    const allowedWeapons = cls.weapons_allowed || null;
    const profs = DATA.proficiencies;
    const allWeapons = getWeaponData();

    // Build entire step content
    let html = `
      <div style="font-size:1.1rem;font-weight:bold;color:#4a1a6b;margin-bottom:.5rem;">
        Slots: ${remaining} remaining of ${totalSlots}
      </div>
      <div style="font-size:.85rem;color:#888;margin-bottom:.75rem;">
        ${allowedWeapons ? `<strong>Weapons allowed:</strong> ${allowedWeapons.join(', ')}` :
          hasAllGroups ? '<strong>All weapon groups available</strong>' :
          `<strong>Groups:</strong> ${classGroups.join(', ')}`}
        | Non-proficiency penalty: ${cls.nonproficiency_penalty}
      </div>`;

    // Summary of current choices (show at top so you always see what you've picked)
    if (genState.wpChoices.length > 0) {
      html += `<div style="border:1px solid #4a1a6b;border-radius:6px;padding:.75rem;margin-bottom:1rem;background:#faf5ff;">
        <h3 style="font-size:.95rem;color:#4a1a6b;margin:0 0 .5rem 0;">Your Choices</h3>
        ${genState.wpChoices.map((c, i) => `<div class="cart-item">
          <span class="ci-name"><strong>${c.name}</strong> <span style="color:#888;font-size:.8rem;">(${c.slots} slot${c.slots>1?'s':''})</span></span>
          <span class="ci-remove" data-action="wp-remove" data-idx="${i}">&times;</span>
        </div>`).join('')}
      </div>`;
    }

    if (remaining >= 1) {
      // Individual weapons
      const weaponList = allowedWeapons
        ? allWeapons.filter(w => allowedWeapons.some(aw => w.name.toLowerCase().includes(aw)))
        : allWeapons;

      html += `<h3 style="font-size:.95rem;color:#333;margin-bottom:.5rem;">Weapons Training</h3>
        <div class="add-row" style="margin-bottom:.75rem;">
          <select data-action="wp-weapon-select" style="max-width:250px;">
            <option value="">Single Weapon Proficiency (1 slot)...</option>
            ${weaponList.map(w => `<option value="${w.name}">${w.name} (${w.damage}, ${w.init||''})</option>`).join('')}
          </select>
          <button data-action="wp-add-weapon" class="btn-small">Add</button>
        </div>`;

      // Weapon groups
      if (hasAllGroups || classGroups.length > 0) {
        const groups = DATA.equipment.weapon_groups || {};
        const availGroups = hasAllGroups ? Object.keys(groups) : classGroups;
        html += `<div class="add-row" style="margin-bottom:.75rem;">
          <select data-action="wp-group-select" style="max-width:250px;">
            <option value="">Weapon Group (${remaining >= 2 ? '2 slots' : '1 slot, -1 penalty'})...</option>
            ${availGroups.map(g => {
              const weapons = groups[g] || [];
              return `<option value="${g}">${g.replace(/_/g,' ')} (${weapons.slice(0,3).join(', ')}...)</option>`;
            }).join('')}
          </select>
          <button data-action="wp-add-group" class="btn-small">Add</button>
        </div>`;
      }

      // Fighting styles
      html += `<h3 style="font-size:.95rem;color:#333;margin:.5rem 0;">Fighting Styles</h3>
        <div class="option-grid" style="grid-template-columns:repeat(auto-fill,minmax(220px,1fr));">`;
      for (const style of profs.fighting_styles) {
        const alreadyPicked = genState.wpChoices.some(c => c.id === style.id);
        html += `<div class="option-card${alreadyPicked?' disabled':''}" data-action="wp-add-style" data-id="${style.id}" style="padding:.75rem;">
          <h3 style="font-size:.9rem;">${style.name}</h3>
          <div class="card-desc">${style.description}</div>
          <div class="card-detail" style="color:#4a1a6b;">1 slot</div>
        </div>`;
      }
      html += `</div>`;

      // Maneuvers
      html += `<h3 style="font-size:.95rem;color:#333;margin:.75rem 0 .5rem 0;">Maneuvers Training</h3>`;
      for (const m of profs.maneuvers) {
        const alreadyPicked = genState.wpChoices.some(c => c.id === m.id);
        html += `<div class="option-card${alreadyPicked?' disabled':''}" data-action="wp-add-maneuver" data-id="${m.id}" style="padding:.75rem;margin-bottom:.5rem;">
          <h3 style="font-size:.9rem;">${m.name}</h3>
          <div class="card-desc">${m.description}</div>
          <div class="card-detail" style="color:#b45309;">${m.requirements}</div>
        </div>`;
      }

      // Fighter specialization
      if (cls.id === 'fighter') {
        html += `<div class="add-row" style="margin:.75rem 0;">
          <select data-action="wp-spec-select" style="max-width:250px;">
            <option value="">Weapon Specialization (Fighter only, 1 slot)...</option>
            ${allWeapons.map(w => `<option value="${w.name}">${w.name}</option>`).join('')}
          </select>
          <button data-action="wp-add-spec" class="btn-small">Add</button>
        </div>`;
      }
    } else {
      html += `<p style="color:#2e7d32;font-weight:bold;">All ${totalSlots} slots spent. You can remove choices above or continue.</p>`;
    }

    // Render into the container divs
    document.getElementById('wp-header').innerHTML = '';
    document.getElementById('wp-choices').innerHTML = html;
    document.getElementById('wp-summary').innerHTML = '';

    // Attach ALL event listeners via delegation on wp-choices
    const container = document.getElementById('wp-choices');
    container.addEventListener('click', function handler(e) {
      const target = e.target.closest('[data-action]');
      if (!target) return;
      const action = target.dataset.action;

      if (action === 'wp-remove') {
        genState.wpChoices.splice(parseInt(target.dataset.idx), 1);
        showWPStep();
      }
      if (action === 'wp-add-weapon') {
        const sel = container.querySelector('[data-action="wp-weapon-select"]');
        if (sel?.value) wpAddChoice({ type:'weapon', name: sel.value, slots: 1 });
      }
      if (action === 'wp-add-group') {
        const sel = container.querySelector('[data-action="wp-group-select"]');
        if (!sel?.value) return;
        const groupName = sel.value.replace(/_/g,' ');
        if (wpRemaining() >= 2) {
          wpAddChoice({ type:'group', id: sel.value, name: `Weapon Group: ${groupName}`, slots: 2 });
        } else {
          wpAddChoice({ type:'group_partial', id: sel.value, name: `Weapon Group: ${groupName} (1/2, -1 penalty)`, slots: 1 });
        }
      }
      if (action === 'wp-add-style') {
        const style = profs.fighting_styles.find(s => s.id === target.dataset.id);
        if (style) wpAddChoice({ type:'style', id: style.id, name: style.name, desc: style.description, slots: 1 });
      }
      if (action === 'wp-add-maneuver') {
        const m = profs.maneuvers.find(x => x.id === target.dataset.id);
        if (m) wpAddChoice({ type:'maneuver', id: m.id, name: m.name, desc: m.description, slots: 1 });
      }
      if (action === 'wp-add-spec') {
        const sel = container.querySelector('[data-action="wp-spec-select"]');
        if (sel?.value) wpAddChoice({ type:'specialization', name: `Weapon Specialization: ${sel.value}`, slots: 1 });
      }
    });
  }

  // ============ UI: NONWEAPON PROFICIENCIES ============
  function showNWPStep() {
    hideGenSteps();
    document.getElementById('step-nwp').style.display = 'block';
    if (!genState.nwpChoices) genState.nwpChoices = [];

    const cls = genState.classData;
    const anc = genState.ancestryData;
    const fa = getFinalAttrs(genState);

    // Slot calculation
    const baseSlots = cls.nwp_slots_start || 3;
    const thiefBonus = cls.id === 'thief' ? 4 : 0;
    const ambitiousNWP = genState.ambitiousStartChoice === 'nwp' ? 1 : 0;
    const ancestryNWPBonus = (anc.bonus_nwp_slots || 0) + ambitiousNWP;
    const intBonus = getIntLangs(fa.INT || 10); // bonus languages = bonus NWP slots
    const totalSlots = baseSlots + thiefBonus + ancestryNWPBonus + intBonus;
    const usedSlots = genState.nwpChoices.reduce((s, c) => s + (c.slots || 1), 0);
    const remaining = totalSlots - usedSlots;

    // Available groups
    const classGroups = cls.nwp_groups || ['General'];
    const allGroups = classGroups.includes('All') ? ['General','Academic','Nature','Rogue'] : classGroups;
    const nwpData = DATA.proficiencies.nonweapon_proficiencies || {};
    const levels = DATA.proficiencies.proficiency_levels || [];

    let html = `
      <div style="font-size:1.1rem;font-weight:bold;color:#4a1a6b;margin-bottom:.25rem;">
        Slots: ${remaining} remaining of ${totalSlots}
      </div>
      <div style="font-size:.8rem;color:#888;margin-bottom:.75rem;">
        ${baseSlots} base${thiefBonus ? ` + ${thiefBonus} rogue bonus` : ''}${ancestryNWPBonus ? ` + ${ancestryNWPBonus} ${anc.name}` : ''}${intBonus ? ` + ${intBonus} INT bonus` : ''}
        &mdash; Groups: ${allGroups.join(', ')}
      </div>`;

    // Current choices
    if (genState.nwpChoices.length > 0) {
      html += `<div style="border:1px solid #4a1a6b;border-radius:6px;padding:.75rem;margin-bottom:1rem;background:#faf5ff;">
        <h3 style="font-size:.95rem;color:#4a1a6b;margin:0 0 .5rem 0;">Your Proficiencies</h3>
        ${genState.nwpChoices.map((c, i) => {
          const attrScore = fa[c.attr] || 10;
          const checkMod = getCheckMod(attrScore);
          const totalBonus = checkMod + c.bonus;
          return `<div class="cart-item">
            <span class="ci-name">
              <strong>${c.name}</strong>
              <span style="color:#4a1a6b;font-size:.8rem;">${c.level}</span>
              <span style="color:#888;font-size:.8rem;">(${c.attr} ${formatMod(totalBonus)}, ${c.slots} slot${c.slots>1?'s':''})</span>
            </span>
            <span class="ci-remove" data-action="nwp-remove" data-idx="${i}">&times;</span>
          </div>`;
        }).join('')}
      </div>`;
    }

    // Add proficiency UI
    if (remaining >= 1) {
      // Build options grouped by group
      for (const group of allGroups) {
        const profs = nwpData[group] || [];
        html += `<h3 style="font-size:.95rem;color:#333;margin:.5rem 0;">${group} Proficiencies</h3>
          <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(280px,1fr));gap:.5rem;margin-bottom:.75rem;">`;

        for (const p of profs) {
          // Check if already picked
          const existing = genState.nwpChoices.find(c => c.name === p.name);
          const currentSlots = existing ? existing.slots : 0;
          const maxAtL1 = 2; // can only reach Average at L1
          const canAdd = !existing || currentSlots < maxAtL1;
          const nextLevel = levels[currentSlots] || levels[0];
          const attrScore = fa[p.attr === 'varies' ? 'INT' : p.attr] || 10;
          const checkMod = getCheckMod(attrScore);
          const nextBonus = nextLevel ? checkMod + nextLevel.bonus : checkMod;

          html += `<div class="option-card${canAdd && remaining >= 1 ? '' : ' disabled'}"
            data-action="nwp-add" data-name="${p.name}" data-attr="${p.attr === 'varies' ? 'INT' : p.attr}"
            data-group="${group}" style="padding:.5rem .75rem;cursor:${canAdd?'pointer':'default'};">
            <div style="display:flex;justify-content:space-between;align-items:center;">
              <strong style="font-size:.9rem;">${p.name}</strong>
              <span style="font-size:.75rem;color:#4a1a6b;">${p.attr}${p.complex ? ' *' : ''}</span>
            </div>
            <div style="font-size:.75rem;color:#888;">${p.desc}</div>
            ${existing ?
              `<div style="font-size:.75rem;color:#2e7d32;margin-top:.2rem;">Current: ${existing.level} (${formatMod(checkMod + existing.bonus)}). ${canAdd ? `Click to advance → ${nextLevel.name} (${formatMod(nextBonus)})` : 'Max for L1'}</div>` :
              `<div style="font-size:.75rem;color:#4a1a6b;margin-top:.2rem;">Add at Minor (${formatMod(checkMod + 2)})</div>`
            }
          </div>`;
        }
        html += `</div>`;
      }
    } else {
      html += `<p style="color:#2e7d32;font-weight:bold;">All ${totalSlots} slots spent.</p>`;
    }

    document.getElementById('nwp-content').innerHTML = html;

    // Event delegation
    document.getElementById('nwp-content').addEventListener('click', function handler(e) {
      const target = e.target.closest('[data-action]');
      if (!target) return;

      if (target.dataset.action === 'nwp-remove') {
        genState.nwpChoices.splice(parseInt(target.dataset.idx), 1);
        showNWPStep();
      }
      if (target.dataset.action === 'nwp-add') {
        if (remaining < 1) return;
        const name = target.dataset.name;
        const attr = target.dataset.attr;
        const group = target.dataset.group;
        const existing = genState.nwpChoices.find(c => c.name === name);
        if (existing) {
          // Advance to next level
          if (existing.slots < 2) { // max Average at L1
            existing.slots += 1;
            const lvl = levels[existing.slots - 1];
            existing.level = lvl.name;
            existing.bonus = lvl.bonus;
          }
        } else {
          genState.nwpChoices.push({
            name, attr, group,
            slots: 1,
            level: 'Minor',
            bonus: 2
          });
        }
        showNWPStep();
      }
    }, { once: true });
  }

  // ============ UI: CLASS TALENT ============
  function showTalentStep() {
    hideGenSteps();
    document.getElementById('step-talent').style.display = 'block';
    if (!genState.talent) genState.talent = null;
    const talents = CLASS_TALENTS_L1[genState.classData.id] || [];
    const container = document.getElementById('talent-options');
    const selectedDiv = document.getElementById('talent-selected');
    if (genState.talent) {
      selectedDiv.style.display = 'block';
      document.getElementById('talent-selected-name').textContent = genState.talent;
    } else {
      selectedDiv.style.display = 'none';
    }
    container.innerHTML = talents.map(t => {
      const picked = genState.talent === t;
      const link = getTalentPageLink(t);
      return `<div class="option-card${picked?' disabled':''}" data-talent="${t}" style="padding:.5rem .75rem;">
        <strong style="font-size:.9rem;">${t}</strong>
        <div style="font-size:.75rem;"><a href="${link}" target="_blank" style="color:#4a1a6b;" onclick="event.stopPropagation();">Details &rarr;</a></div>
      </div>`;
    }).join('');
    container.querySelectorAll('.option-card:not(.disabled)').forEach(el => {
      el.addEventListener('click', () => {
        genState.talent = el.dataset.talent;
        showTalentStep();
      });
    });
    document.getElementById('talent-clear').addEventListener('click', () => {
      genState.talent = null;
      showTalentStep();
    });
  }

  // ============ UI: EQUIPMENT SHOPPING ============
  function showEquipStep() {
    hideGenSteps();
    document.getElementById('step-equip').style.display = 'block';
    document.getElementById('equip-shop').style.display = 'none';

    // Init shopping state
    if (!genState.cart) genState.cart = [];
    if (!genState.gold) genState.gold = 0;

    // Show gold formula for this class
    const cls = genState.classData;
    const formula = DATA.equipment.starting_gold?.[cls.id] || '3d6x10';
    document.getElementById('gold-formula').textContent = `${cls.name} starting gold: ${formula}`;
  }

  function rollStartingGold() {
    const cls = genState.classData;
    const formula = DATA.equipment.starting_gold?.[cls.id] || '3d6x10';
    // Parse formula like "5d4x10", "2d6x10", "1d4+1x10"
    let total = 0;
    const match = formula.match(/(\d+)d(\d+)([+](\d+))?x(\d+)/);
    if (match) {
      const [, count, sides, , bonus, mult] = match;
      let sum = 0;
      for (let i = 0; i < parseInt(count); i++) sum += roll(parseInt(sides));
      if (bonus) sum += parseInt(bonus);
      total = sum * parseInt(mult);
    } else {
      total = (roll(6) + roll(6) + roll(6)) * 10; // fallback
    }
    genState.gold = total;
    genState.cart = [];
    openShop();
  }

  function openShop() {
    document.getElementById('equip-shop').style.display = 'block';
    updateShopGold();
    renderShopTab('armor');
    renderCart();
  }

  function updateShopGold() {
    document.getElementById('shop-gold').textContent = genState.gold.toFixed(genState.gold % 1 === 0 ? 0 : 1);
  }

  function renderShopTab(tab) {
    // Update tab buttons
    document.querySelectorAll('.shop-tab').forEach(btn => {
      btn.classList.toggle('active', btn.dataset.tab === tab);
    });
    document.querySelectorAll('.shop-panel').forEach(p => p.style.display = 'none');
    const panel = document.getElementById(`shop-tab-${tab}`);
    panel.style.display = 'block';

    // Armor restriction for current class
    const armorNote = genState.classData?.armor || '';
    let items = [];
    if (tab === 'armor') {
      items = [
        ...(DATA.equipment.armor||[]).map(a => ({ name: a.name, cost: a.cost, detail: `AC +${a.ac}, ${a.weight}lbs`, type:'armor', ac: a.ac, thief_allowed: a.thief_allowed })),
        ...(DATA.equipment.shields||[]).map(s => ({ name: s.name, cost: s.cost, detail: `AC +${s.ac}, ${s.weight}lbs${s.note?' — '+s.note:''}`, type:'shield', ac: s.ac }))
      ];
    } else if (tab === 'weapons') {
      const allW = getWeaponData();
      items = allW.map(w => ({
        name: w.name, cost: w.cost, detail: `${w.damage} ${w.init||''} ${w.range?w.range+'ft ':''}${w.type||''}`,
        type: 'weapon', weaponData: w
      }));
    } else {
      items = (DATA.equipment.misc_items||[]).map(i => ({
        name: i.name, cost: i.cost, detail: i.note||`${i.weight||0}lbs`, type:'gear'
      }));
    }

    // Show armor restriction note
    let headerNote = '';
    if (tab === 'armor' && armorNote) {
      headerNote = `<div style="font-size:.8rem;color:#b45309;padding:.35rem .5rem;background:#fff8f0;border-radius:4px;margin-bottom:.5rem;"><strong>Class restriction:</strong> ${armorNote}</div>`;
    }

    panel.innerHTML = headerNote + items.map((item, i) => {
      const canAfford = genState.gold >= item.cost;
      return `<div class="shop-item${canAfford?'':' cant-afford'}" data-tab="${tab}" data-idx="${i}" title="${item.detail}">
        <span class="si-name">${item.name}</span>
        <span class="si-detail">${item.detail}</span>
        <span class="si-cost">${item.cost >= 1 ? item.cost + ' gp' : (item.cost*10).toFixed(0) + ' sp'}</span>
        ${canAfford ? '<span class="si-buy">+</span>' : ''}
      </div>`;
    }).join('');

    // Buy click handlers
    panel.querySelectorAll('.shop-item:not(.cant-afford)').forEach(el => {
      el.addEventListener('click', () => {
        const idx = parseInt(el.dataset.idx);
        const item = items[idx];
        if (genState.gold < item.cost) return;
        genState.gold -= item.cost;
        genState.cart.push(item);
        updateShopGold();
        renderShopTab(tab); // re-render to update affordability
        renderCart();
      });
    });
  }

  function renderCart() {
    const container = document.getElementById('shop-cart');
    if (genState.cart.length === 0) {
      container.innerHTML = '<p style="color:#888;font-size:.85rem;">Nothing purchased yet.</p>';
      return;
    }
    container.innerHTML = genState.cart.map((item, i) =>
      `<div class="cart-item">
        <span class="ci-name">${item.name}</span>
        <span class="ci-cost">${item.cost >= 1 ? item.cost + ' gp' : (item.cost*10).toFixed(0) + ' sp'}</span>
        <span class="ci-remove" data-cidx="${i}">&times;</span>
      </div>`
    ).join('');

    container.querySelectorAll('.ci-remove').forEach(btn => {
      btn.addEventListener('click', () => {
        const idx = parseInt(btn.dataset.cidx);
        genState.gold += genState.cart[idx].cost;
        genState.cart.splice(idx, 1);
        updateShopGold();
        const activeTab = document.querySelector('.shop-tab.active')?.dataset.tab || 'armor';
        renderShopTab(activeTab);
        renderCart();
      });
    });
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

    // BTH from class data
    let bth = cls.bth_level1 || 0;

    // XP note
    const xpNote = getXpNote(cls, finalAttrs);

    // L1 special abilities from CLASS_ABILITIES data
    const specials = CLASS_ABILITIES[cls.id] || [];

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
      baseLanguageCount: (anc.languages || []).length,
      specials,
      alignment: genState.alignment ? (ALIGNMENT_NAMES[genState.alignment] || genState.alignment) : '',
      spellcasting: cls.spellcasting || null,
      wpSlots: cls.weapon_slots_start + (anc.bonus_wp_slots || 0) + (genState.ambitiousStartChoice === 'wp' ? 1 : 0),
      nwpSlots: cls.nwp_slots_start + (cls.id === 'thief' ? 4 : 0) + (anc.bonus_nwp_slots || 0) + (genState.ambitiousStartChoice === 'nwp' ? 1 : 0),
      nwpGroups: cls.nwp_groups || [],
      armor: cls.armor,
      weapons: [],
      equipment: [],
      armorBonus: 0,
      shieldBonus: 0,
      miscACBonus: 0,
      weaponProficiencies: [
        ...(genState.ancestryFreeWPs || []),
        ...(genState.wpChoices ? genState.wpChoices : [])
      ],
      nonweaponProficiencies: [
        ...(genState.ancestryFreeNWPs || []),
        ...(CLASS_FREE_NWPS[cls.id] || []),
        ...(genState.nwpChoices ? genState.nwpChoices : [])
      ],
      gold: genState.gold || 0,
      classTalent: genState.talent || null,
      notes: '',
      createdAt: new Date().toISOString()
    };

    // Process cart from equipment shopping
    if (genState.cart && genState.cart.length > 0) {
      for (const item of genState.cart) {
        if (item.type === 'weapon' && item.weaponData) {
          const wd = item.weaponData;
          char.weapons.push({
            name: wd.name, damage: wd.damage, damage_large: wd.damage_large,
            init: wd.init, range: wd.range || null, type: wd.type || '',
            category: wd.category, groups: wd.groups || []
          });
        } else if (item.type === 'armor') {
          char.armorBonus = Math.max(char.armorBonus, item.ac || 0);
          char.equipment.push(item.name);
        } else if (item.type === 'shield') {
          char.shieldBonus = Math.max(char.shieldBonus, item.ac || 0);
          char.equipment.push(item.name);
        } else {
          char.equipment.push(item.name);
        }
      }
      // Recalc AC with armor
      const dexDef = getDexDefense(finalAttrs.DEX || 10);
      char.ac = 10 + char.armorBonus + char.shieldBonus + dexDef + char.miscACBonus;
    }

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

    // Class abilities — expandable with descriptions
    const specialsHtml = (char.specials||[]).map((s, i) => {
      const name = typeof s === 'string' ? s : s.name;
      const desc = typeof s === 'string' ? null : s.desc;
      if (!desc) return `<div class="trait-box" style="background:#e8f0fe;border-color:#2c5282;"><strong>${name}</strong></div>`;
      return `<div class="ability-box" data-aidx="${i}">
        <div class="ab-name">${name} <span class="ab-arrow">&#9654;</span></div>
        <div class="ab-desc">${desc} <a href="${BASE}/players/classes/${char.classId}#${name.toLowerCase().replace(/[^a-z0-9]+/g,'-').replace(/-+$/,'')}" style="font-size:.8rem;color:#4a1a6b;">Full details &rarr;</a></div>
      </div>`;
    }).join('');

    // Weapons table
    // Check weapon proficiency
    function isWeaponProficient(weaponName, weaponGroups) {
      const wps = char.weaponProficiencies || [];
      const wName = weaponName.toLowerCase();
      for (const wp of wps) {
        // Direct weapon proficiency or specialization
        if (wp.type === 'weapon' || wp.type === 'specialization' || wp.type === 'ancestry_free') {
          if (wp.name.toLowerCase().includes(wName) || wName.includes(wp.name.toLowerCase())) return { proficient: true, penalty: 0 };
        }
        // Full weapon group
        if (wp.type === 'group' && wp.id) {
          const groupWeapons = DATA.equipment.weapon_groups?.[wp.id] || [];
          if (groupWeapons.some(gw => gw.toLowerCase() === wName || wName.includes(gw.toLowerCase()))) return { proficient: true, penalty: 0 };
        }
        // Partial weapon group (-1 penalty)
        if (wp.type === 'group_partial' && wp.id) {
          const groupWeapons = DATA.equipment.weapon_groups?.[wp.id] || [];
          if (groupWeapons.some(gw => gw.toLowerCase() === wName || wName.includes(gw.toLowerCase()))) return { proficient: true, penalty: -1 };
        }
      }
      return { proficient: false, penalty: 0 };
    }

    const cls = DATA.classes.find(c => c.id === char.classId);
    const nonProfPenalty = cls?.nonproficiency_penalty || -3;

    const hasRangedMastery = char.classTalent === 'Ranged Mastery';
    const weaponRows = char.weapons.map((w, i) => {
      const prof = isWeaponProficient(w.name, w.groups || []);
      const profPenalty = prof.proficient ? prof.penalty : nonProfPenalty;
      const rangedMasteryAtk = (w.category === 'ranged' && hasRangedMastery) ? 0 : 0; // no atk bonus from RM
      const rangedMasteryDmg = (w.category === 'ranged' && hasRangedMastery) ? 1 : 0;
      const atkMod = (w.category === 'ranged'
        ? char.bth + getDexRanged(fa.DEX||10)
        : char.bth + getStrAtkDmg(fa.STR||10)) + profPenalty;
      const dmgMod = (w.category === 'ranged' ? 0 : getStrAtkDmg(fa.STR||10)) + rangedMasteryDmg;
      const dmgStr = dmgMod !== 0 ? `${w.damage}${formatMod(dmgMod)}` : w.damage;
      const lgDmg = w.damage_large || w.damage;
      const profStyle = prof.proficient ? '' : 'color:#c44;';
      const profNote = !prof.proficient ? ` <span style="font-size:.7rem;color:#c44;" title="Non-proficient: ${nonProfPenalty} penalty">(NP)</span>` :
        prof.penalty ? ` <span style="font-size:.7rem;color:#b45309;" title="Partial group: ${prof.penalty} penalty">(1/2)</span>` : '';
      return `<tr>
        <td>${w.name}${profNote}</td>
        <td style="${profStyle}">${formatMod(atkMod)}</td>
        <td>${dmgStr} / ${lgDmg}</td>
        <td class="wt-init">${w.init || '—'}</td>
        <td>${w.range ? (hasRangedMastery ? Math.floor(w.range*1.5)+'ft' : w.range+'ft') : '—'}</td>
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
        ${char.alignment ? `<div style="font-size:.9rem;color:#555;">${char.alignment}</div>` : ''}
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
      <h2>Languages</h2>
      <div style="display:flex;flex-wrap:wrap;gap:.4rem;margin:.5rem 0;">
        ${(char.languages||[]).map((l, i) => {
          const cap = l.charAt(0).toUpperCase() + l.slice(1);
          const isBase = i < (char.baseLanguageCount || char.languages.length);
          return `<span style="background:#e8f4f8;border:1px solid #b8d4e8;border-radius:3px;padding:.2rem .5rem;font-size:.85rem;display:inline-flex;align-items:center;gap:.3rem;">
            ${cap}${!isBase ? ` <span class="wt-remove" data-langidx="${i}" style="cursor:pointer;">&times;</span>` : ''}
          </span>`;
        }).join('')}
      </div>
      ${(() => {
        const bonusSlots = char.bonusLanguageSlots || 0;
        const baseLangs = char.baseLanguageCount || 2;
        const usedBonus = Math.max(0, (char.languages||[]).length - baseLangs);
        const remaining = bonusSlots - usedBonus;
        if (remaining <= 0 && bonusSlots === 0) return '';
        const ancestry = DATA.ancestries.find(a => a.id === char.ancestryId);
        const bonusOpts = ancestry?.bonus_languages || [];
        const existing = (char.languages||[]).map(l => l.toLowerCase());
        const available = bonusOpts.filter(l => l !== 'any' && !existing.includes(l.toLowerCase()));
        return `<div style="font-size:.8rem;color:#888;margin-bottom:.25rem;">${remaining} bonus language slot${remaining!==1?'s':''} remaining (from INT ${char.finalAttrs?.INT||10})</div>
          ${remaining > 0 ? `<div class="add-row">
            <select id="add-lang-select">
              <option value="">Add language...</option>
              ${bonusOpts.includes('any') ?
                `<option value="custom">Type custom...</option>` : ''}
              ${available.map(l => `<option value="${l}">${l.charAt(0).toUpperCase()+l.slice(1)}</option>`).join('')}
            </select>
            <button id="add-lang-btn" class="btn-small">Add</button>
          </div>` : ''}`;
      })()}

      ${specialsHtml ? `<h2>Class Abilities (Level 1)</h2>${specialsHtml}` : ''}
      ${char.classTalent ? `<h2>Class Talent</h2><div class="ability-box"><div class="ab-name">${char.classTalent} <span class="ab-arrow">&#9654;</span></div><div class="ab-desc"><a href="${getTalentPageLink(char.classTalent)}" target="_blank" style="color:#4a1a6b;">Full description &rarr;</a></div></div>` : ''}

      <h2>Weapon Proficiencies</h2>
      ${(char.weaponProficiencies||[]).length > 0 ? (char.weaponProficiencies||[]).map((wp, i) =>
        `<div class="ability-box" data-wp-expand>
          <div class="ab-name">${wp.name} <span style="font-size:.75rem;color:#888;">(${wp.slots} slot${wp.slots>1?'s':''})</span>
            <span style="display:flex;gap:.3rem;align-items:center;"><span class="ci-remove" data-wpsheetidx="${i}" onclick="event.stopPropagation()">&times;</span><span class="ab-arrow">&#9654;</span></span>
          </div>
          ${wp.desc ? `<div class="ab-desc">${wp.desc}</div>` : ''}
        </div>`
      ).join('') : ''}
      <div style="font-size:.8rem;color:#888;margin-bottom:.5rem;">
        ${char.wpSlots - (char.weaponProficiencies||[]).filter(p=>p.slots!==0).reduce((s,p)=>s+(p.slots||1),0)} of ${char.wpSlots} slots remaining
      </div>
      <div class="add-row">
        <select id="add-wp-select">
          <option value="">Add proficiency...</option>
          <optgroup label="Single Weapon">
            ${getWeaponData().map(w => `<option value="weapon:${w.name}">${w.name} (${w.damage})</option>`).join('')}
          </optgroup>
          <optgroup label="Fighting Styles">
            ${(DATA.proficiencies?.fighting_styles||[]).map(s => `<option value="style:${s.id}:${s.name}">${s.name}</option>`).join('')}
          </optgroup>
        </select>
        <button id="add-wp-btn" class="btn-small">Add</button>
      </div>

      <h2>Nonweapon Proficiencies</h2>
      ${(char.nonweaponProficiencies||[]).length > 0 ? `
        <table class="weapon-table" style="margin-bottom:.5rem;">
          <tr><th>Proficiency</th><th>Attr</th><th>Level</th><th>Check</th><th></th></tr>
          ${(char.nonweaponProficiencies||[]).map((p, i) => {
            const attrScore = char.finalAttrs[p.attr] || 10;
            const totalBonus = getCheckMod(attrScore) + (p.bonus || 2);
            const isFree = p.free || p.slots === 0;
            return `<tr>
              <td>${p.name}${isFree ? ' <span style="font-size:.65rem;color:#2e7d32;">(free)</span>' : ''}</td>
              <td>${p.attr}</td>
              <td style="color:#4a1a6b;font-weight:bold;">${p.level || 'Minor'}</td>
              <td style="font-weight:bold;">${formatMod(totalBonus)}</td>
              <td>${!isFree ? `<span class="wt-remove" data-nwpidx="${i}">&times;</span>` : ''}</td>
            </tr>`;
          }).join('')}
        </table>` : ''}
      <div style="font-size:.8rem;color:#888;margin-bottom:.5rem;">
        <strong>Groups:</strong> ${char.nwpGroups.join(', ')} | <strong>Slots:</strong> ${char.nwpSlots} total, ${char.nwpSlots - (char.nonweaponProficiencies||[]).filter(p=>!p.free&&p.slots!==0).reduce((s,p)=>s+(p.slots||1),0)} remaining
      </div>
      <div class="add-row">
        <select id="add-nwp-select">
          <option value="">Add proficiency...</option>
          ${(char.nwpGroups||[]).map(g => {
            const profs = DATA.proficiencies?.nonweapon_proficiencies?.[g] || [];
            return `<optgroup label="${g}">${profs.map(p =>
              `<option value="${g}:${p.name}:${p.attr}">${p.name} (${p.attr})</option>`
            ).join('')}</optgroup>`;
          }).join('')}
        </select>
        <button id="add-nwp-btn" class="btn-small">Add</button>
      </div>
      <div class="card-detail" style="margin-top:.5rem;"><strong>Armor Allowed:</strong> ${char.armor}</div>
      ${char.spellcasting ? (() => {
        const sc = char.spellcasting;
        const isArcane = sc.type === 'arcane';
        const castAttr = sc.attribute;
        const castScore = char.finalAttrs?.[castAttr] || 10;
        const castMod = getCheckMod(castScore);
        const baseSpells = char.level >= 1 ? 1 : 0;
        const bonusStr = isArcane
          ? lookup(DATA.attributes.intelligence.bonus_arcane_spells, castScore)
          : lookup(DATA.attributes.wisdom.bonus_divine_spells, castScore);
        let bonusL1 = 0;
        if (bonusStr && bonusStr !== '0' && bonusStr !== null) bonusL1 = parseInt(String(bonusStr).split('/')[0]) || 0;
        const totalSlots = baseSpells + bonusL1;

        if (!char.preparedSpells) char.preparedSpells = [];
        if (!char.spellbook) char.spellbook = [];
        const castCount = char.preparedSpells.filter(s => s.cast).length;
        const slotsRemaining = totalSlots - castCount;
        const spellPageBase = isArcane ? `${BASE}/players/spells/arcane/` : `${BASE}/players/spells/divine/`;
        const spellList = isArcane ? DATA.spells?.arcane?.['1'] : DATA.spells?.divine?.['1'];

        // Helper to render a spell entry
        const renderSpell = (sp, i, collection, opts = {}) => {
          const spData = spellList?.find(s => s.name === sp.name);
          const anchor = sp.name.toLowerCase().replace(/[^a-z0-9]+/g, '-').replace(/-+$/, '');
          const canCast = opts.canCast && !sp.cast && slotsRemaining > 0;
          const isPrepared = opts.isPrepared;
          const isInBook = opts.isInBook;
          const timesPrepared = isPrepared ? 0 : char.preparedSpells.filter(p => p.name === sp.name).length;
          return `<div class="ability-box" style="${sp.cast ? 'opacity:.5;background:#f8f0f0;border-color:#c44;' : isPrepared ? 'border-color:#4a1a6b;' : ''}">
            <div class="ab-name">
              <span>
                ${sp.name}
                ${sp.cast ? ' <span style="color:#c44;font-size:.75rem;">(CAST)</span>' : ''}
                ${spData ? `<span style="font-size:.7rem;color:#888;"> ${spData.school}</span>` : ''}
              </span>
              <span style="display:flex;gap:.3rem;align-items:center;">
                ${canCast ? `<button class="btn-small" data-action="cast-spell" data-idx="${i}" style="font-size:.7rem;padding:.1rem .4rem;color:#c44;border-color:#c44;" onclick="event.stopPropagation()">Cast</button>` : ''}
                ${isPrepared && !canCast && !sp.cast ? `<span style="font-size:.65rem;color:#c44;">No slots</span>` : ''}
                ${isInBook && char.preparedSpells.length < totalSlots ? `<button class="btn-small" data-action="prepare-from-book" data-name="${sp.name}" style="font-size:.7rem;padding:.1rem .4rem;color:#2e7d32;border-color:#2e7d32;" onclick="event.stopPropagation()">Prepare${timesPrepared ? ' again' : ''}</button>` : ''}
                ${isInBook && timesPrepared > 0 ? `<span style="font-size:.65rem;color:#2e7d32;">${timesPrepared}x prepared</span>` : ''}
                ${isPrepared ? `<span class="ci-remove" data-unprepidx="${i}" style="font-size:.85rem;" onclick="event.stopPropagation()" title="Unprepare">&times;</span>` : ''}
                ${isInBook ? `<span class="ci-remove" data-bookidx="${i}" style="font-size:.85rem;" onclick="event.stopPropagation()" title="Remove from spellbook">&times;</span>` : ''}
                <span class="ab-arrow">&#9654;</span>
              </span>
            </div>
            <div class="ab-desc">
              ${spData ? `<div style="margin-bottom:.3rem;"><strong>Range:</strong> ${spData.range||'—'} | <strong>Duration:</strong> ${spData.duration||'—'} | <strong>Cast:</strong> ${spData.castTime||'—'} | <strong>Save:</strong> ${spData.save||'None'}</div>
              <div>${spData.desc}</div>
              <a href="${spellPageBase}level-1#${anchor}" target="_blank" style="font-size:.8rem;color:#4a1a6b;">Full description &rarr;</a>` : '<em>No spell data available</em>'}
            </div>
          </div>`;
        };

        let html = `<h2>Spellcasting (${sc.type})</h2>
          <div class="derived-grid" style="margin-bottom:.75rem;">
            <div class="dg-box"><div class="dg-label">${castAttr}</div><div class="dg-value">${castScore}</div></div>
            <div class="dg-box"><div class="dg-label">Check Mod</div><div class="dg-value">${formatMod(castMod)}</div></div>
            <div class="dg-box"><div class="dg-label">L1 Slots</div><div class="dg-value">${slotsRemaining}/${totalSlots}</div></div>
            <div class="dg-box" style="${slotsRemaining <= 0 ? 'background:#fee;border-color:#c44;' : ''}"><div class="dg-label">${slotsRemaining <= 0 ? 'NO SLOTS' : 'Available'}</div><div class="dg-value" style="${slotsRemaining <= 0 ? 'color:#c44;' : 'color:#2e7d32;'}">${slotsRemaining <= 0 ? 'Empty' : slotsRemaining + ' left'}</div></div>
          </div>`;

        // === PREPARED SPELLS (both wizard and cleric) ===
        html += `<h3 style="font-size:.95rem;color:#4a1a6b;margin:.5rem 0;">Prepared Today</h3>`;
        if (char.preparedSpells.length > 0) {
          html += char.preparedSpells.map((sp, i) => renderSpell(sp, i, 'prepared', { canCast: true, isPrepared: true })).join('');
        } else {
          html += `<p style="color:#888;font-size:.85rem;">${isArcane ? 'No spells memorised. Prepare from your spellbook below.' : 'No spells prepared. Add from the spell list below.'}</p>`;
        }
        html += char.preparedSpells.some(s => s.cast) ? `<button id="reset-spells-btn" class="btn-small" style="margin-top:.5rem;">Long Rest (reset cast spells)</button>` : '';
        html += `<button id="clear-prepared-btn" class="btn-small" style="margin-top:.5rem;margin-left:.25rem;">New Day (clear all prepared)</button>`;

        if (isArcane) {
          // === WIZARD SPELLBOOK (permanent collection) ===
          html += `<h3 style="font-size:.95rem;color:#333;margin:1rem 0 .5rem 0;">Spellbook <span style="font-size:.8rem;color:#888;font-weight:normal;">(${char.spellbook.length} spells known)</span></h3>`;
          html += `<div style="font-size:.8rem;color:#888;margin-bottom:.5rem;">Your permanent spell collection. Prepare spells from here each morning. Starting spells: ${Math.floor(castScore/2)} (half INT).</div>`;
          if (char.spellbook.length > 0) {
            html += char.spellbook.map((sp, i) => renderSpell(sp, i, 'book', { isInBook: true })).join('');
          }
          html += `<div class="add-row" style="margin-top:.5rem;">
            <select id="add-spell-select" style="max-width:300px;">
              <option value="">Add spell to spellbook...</option>
              ${(spellList||[]).filter(s => !char.spellbook.some(b => b.name === s.name)).map(s => `<option value="${s.name}" title="${s.desc}">${s.name} (${s.school})</option>`).join('')}
            </select>
            <button id="add-spell-from-list-btn" class="btn-small">Add to Spellbook</button>
          </div>`;
        } else {
          // === CLERIC/DRUID: prepare from full list ===
          html += `<h3 style="font-size:.95rem;color:#333;margin:1rem 0 .5rem 0;">Prepare Spell</h3>`;
          html += `<div style="font-size:.8rem;color:#888;margin-bottom:.5rem;">Choose from all ${sc.type} spells. | <a href="${spellPageBase}" style="color:#4a1a6b;">Full spell list &rarr;</a></div>`;
          html += `<div class="add-row">
            <select id="add-spell-select" style="max-width:300px;">
              <option value="">Add Level 1 spell...</option>
              ${(spellList||[]).map(s => `<option value="${s.name}" title="${s.desc}">${s.name} (${s.school}${s.sphere ? ', '+s.sphere : ''})</option>`).join('')}
            </select>
            <button id="add-spell-from-list-btn" class="btn-small">Prepare</button>
          </div>`;
        }
        html += `<div id="spell-preview" style="display:none;margin-top:.5rem;padding:.5rem .75rem;background:#f5f0fa;border-left:3px solid #4a1a6b;border-radius:0 4px 4px 0;font-size:.85rem;"></div>`;
        return html;
      })() : ''}

      <h2>Equipment</h2>
      <div style="margin-bottom:.5rem;font-size:.9rem;"><strong>Gold:</strong> <span class="editable" data-field="gold" title="Click to edit">${char.gold}</span> gp</div>
      ${equipHtml || '<p style="color:#888;font-size:.85rem;">No equipment yet.</p>'}
      <div class="add-row">
        <select id="add-equip-select"><option value="">Add from list...</option>
          <optgroup label="Armor">${(DATA.equipment.armor||[]).map(a=>`<option value="armor:${a.name}" data-ac="${a.ac}">${a.name} (AC +${a.ac}, ${a.cost}gp)</option>`).join('')}</optgroup>
          <optgroup label="Shields">${(DATA.equipment.shields||[]).map(s=>`<option value="shield:${s.name}" data-ac="${s.ac}">${s.name} (AC +${s.ac}, ${s.cost}gp)</option>`).join('')}</optgroup>
          <optgroup label="Gear">${(DATA.equipment.misc_items||[]).map(i=>`<option value="gear:${i.name}">${i.name} (${i.cost>=1?i.cost+'gp':(i.cost*10).toFixed(0)+'sp'})</option>`).join('')}</optgroup>
        </select>
        <button id="add-equip-select-btn" class="btn-small">Add</button>
      </div>
      <div class="add-row" style="margin-top:.25rem;">
        <input type="text" id="add-equip-input" placeholder="Or type custom item...">
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

    // Add equipment from dropdown
    el.querySelector('#add-equip-select-btn')?.addEventListener('click', () => {
      const sel = el.querySelector('#add-equip-select');
      const val = sel.value;
      if (!val) return;
      const [type, name] = [val.split(':')[0], val.substring(val.indexOf(':')+1)];
      if (type === 'armor') {
        const opt = sel.selectedOptions[0];
        const ac = parseInt(opt?.dataset.ac) || 0;
        char.armorBonus = Math.max(char.armorBonus || 0, ac);
        char.ac = 10 + (char.armorBonus||0) + (char.shieldBonus||0) + getDexDefense(char.finalAttrs.DEX||10) + (char.miscACBonus||0);
      } else if (type === 'shield') {
        const opt = sel.selectedOptions[0];
        const ac = parseInt(opt?.dataset.ac) || 0;
        char.shieldBonus = Math.max(char.shieldBonus || 0, ac);
        char.ac = 10 + (char.armorBonus||0) + (char.shieldBonus||0) + getDexDefense(char.finalAttrs.DEX||10) + (char.miscACBonus||0);
      }
      char.equipment.push(name);
      saveCurrent();
      renderSheet(char);
    });

    // Add equipment free-text
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

    // Add spell from dropdown (to spellbook for wizard, to prepared for cleric)
    const isArcane = char.spellcasting?.type === 'arcane';
    el.querySelector('#add-spell-from-list-btn')?.addEventListener('click', () => {
      const sel = el.querySelector('#add-spell-select');
      if (!sel?.value) return;
      if (isArcane) {
        if (!char.spellbook) char.spellbook = [];
        if (!char.spellbook.some(s => s.name === sel.value)) {
          char.spellbook.push({ name: sel.value });
        }
      } else {
        if (!char.preparedSpells) char.preparedSpells = [];
        char.preparedSpells.push({ name: sel.value, cast: false });
      }
      saveCurrent(); renderSheet(char);
    });

    // Spell preview on dropdown change
    el.querySelector('#add-spell-select')?.addEventListener('change', function() {
      const preview = el.querySelector('#spell-preview');
      if (!preview) return;
      const spellList = isArcane ? DATA.spells?.arcane?.['1'] : DATA.spells?.divine?.['1'];
      const sp = spellList?.find(s => s.name === this.value);
      if (sp) {
        preview.style.display = 'block';
        preview.innerHTML = `<strong>${sp.name}</strong> <span style="color:#888;">(${sp.school}${sp.sphere ? ', '+sp.sphere : ''})</span><br>
          <span style="font-size:.8rem;">Range: ${sp.range||'—'} | Duration: ${sp.duration||'—'} | Cast: ${sp.castTime||'—'} | Save: ${sp.save||'None'}</span><br>
          <span style="font-size:.8rem;color:#555;">${sp.desc}</span>`;
      } else { preview.style.display = 'none'; }
    });

    // Prepare spell from spellbook (wizard)
    el.querySelectorAll('[data-action="prepare-from-book"]').forEach(btn => {
      btn.addEventListener('click', () => {
        if (!char.preparedSpells) char.preparedSpells = [];
        char.preparedSpells.push({ name: btn.dataset.name, cast: false });
        saveCurrent(); renderSheet(char);
      });
    });

    // Unprepare spell
    el.querySelectorAll('[data-unprepidx]').forEach(btn => {
      btn.addEventListener('click', () => {
        char.preparedSpells.splice(parseInt(btn.dataset.unprepidx), 1);
        saveCurrent(); renderSheet(char);
      });
    });

    // Remove from spellbook
    el.querySelectorAll('[data-bookidx]').forEach(btn => {
      btn.addEventListener('click', () => {
        if (!char.spellbook) return;
        char.spellbook.splice(parseInt(btn.dataset.bookidx), 1);
        saveCurrent(); renderSheet(char);
      });
    });

    // Cast spell
    el.querySelectorAll('[data-action="cast-spell"]').forEach(btn => {
      btn.addEventListener('click', () => {
        const idx = parseInt(btn.dataset.idx);
        if (char.preparedSpells?.[idx]) {
          char.preparedSpells[idx].cast = true;
          saveCurrent(); renderSheet(char);
        }
      });
    });

    // Reset cast spells (long rest)
    el.querySelector('#reset-spells-btn')?.addEventListener('click', () => {
      char.preparedSpells?.forEach(s => s.cast = false);
      saveCurrent(); renderSheet(char);
    });

    // Clear all prepared (new day)
    el.querySelector('#clear-prepared-btn')?.addEventListener('click', () => {
      char.preparedSpells = [];
      saveCurrent(); renderSheet(char);
    });

    // Add WP from sheet
    el.querySelector('#add-wp-btn')?.addEventListener('click', () => {
      const sel = el.querySelector('#add-wp-select');
      if (!sel?.value) return;
      if (!char.weaponProficiencies) char.weaponProficiencies = [];
      const [type, ...rest] = sel.value.split(':');
      if (type === 'weapon') {
        char.weaponProficiencies.push({ type: 'weapon', name: rest.join(':'), slots: 1 });
      } else if (type === 'style') {
        const id = rest[0], name = rest.slice(1).join(':');
        const style = DATA.proficiencies?.fighting_styles?.find(s => s.id === id);
        char.weaponProficiencies.push({ type: 'style', id, name: name || style?.name, desc: style?.description, slots: 1 });
      }
      saveCurrent(); renderSheet(char);
    });

    // Remove WP from sheet
    el.querySelectorAll('[data-wpsheetidx]').forEach(btn => {
      btn.addEventListener('click', () => {
        char.weaponProficiencies.splice(parseInt(btn.dataset.wpsheetidx), 1);
        saveCurrent(); renderSheet(char);
      });
    });


    // Add language
    el.querySelector('#add-lang-btn')?.addEventListener('click', () => {
      const sel = el.querySelector('#add-lang-select');
      if (!sel?.value) return;
      if (sel.value === 'custom') {
        const lang = prompt('Enter language name:');
        if (lang && lang.trim()) {
          char.languages.push(lang.trim().toLowerCase());
          saveCurrent(); renderSheet(char);
        }
      } else {
        char.languages.push(sel.value);
        saveCurrent(); renderSheet(char);
      }
    });

    // Remove language
    el.querySelectorAll('[data-langidx]').forEach(btn => {
      btn.addEventListener('click', () => {
        char.languages.splice(parseInt(btn.dataset.langidx), 1);
        saveCurrent(); renderSheet(char);
      });
    });

    // Add NWP from sheet
    el.querySelector('#add-nwp-btn')?.addEventListener('click', () => {
      const sel = el.querySelector('#add-nwp-select');
      if (!sel?.value) return;
      const [group, name, attr] = sel.value.split(':');
      if (!char.nonweaponProficiencies) char.nonweaponProficiencies = [];
      char.nonweaponProficiencies.push({ name, attr, group, slots: 1, level: 'Minor', bonus: 2 });
      saveCurrent();
      renderSheet(char);
    });

    // Remove NWP from sheet
    el.querySelectorAll('[data-nwpidx]').forEach(btn => {
      btn.addEventListener('click', () => {
        char.nonweaponProficiencies.splice(parseInt(btn.dataset.nwpidx), 1);
        saveCurrent();
        renderSheet(char);
      });
    });

    // Expandable class abilities and spells
    el.querySelectorAll('.ability-box').forEach(box => {
      box.addEventListener('click', (e) => {
        if (e.target.tagName === 'A' || e.target.tagName === 'BUTTON' || e.target.closest('button') || e.target.classList.contains('ci-remove')) return;
        box.classList.toggle('open');
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
  // Alignment step
  document.getElementById('back-to-class-from-align-btn').addEventListener('click', () => {
    genState.alignment = null;
    showClassStep();
  });
  // Ancestry profs step
  document.getElementById('back-to-align-btn').addEventListener('click', () => {
    genState.ancestryFreeWPs = []; genState.ancestryFreeNWPs = [];
    showAlignmentStep();
  });
  document.getElementById('ancestry-prof-done-btn').addEventListener('click', showWPStep);
  // Weapon proficiencies step
  document.getElementById('back-to-class-from-wp-btn').addEventListener('click', () => {
    genState.charClass = null; genState.classData = null; genState.wpChoices = []; genState.alignment = null; genState.ancestryFreeWPs = []; genState.ancestryFreeNWPs = [];
    showClassStep();
  });
  document.getElementById('wp-done-btn').addEventListener('click', showNWPStep);
  document.getElementById('back-to-wp-from-nwp-btn').addEventListener('click', showWPStep);
  document.getElementById('nwp-done-btn').addEventListener('click', showTalentStep);
  document.getElementById('back-to-nwp-from-talent-btn').addEventListener('click', showNWPStep);
  document.getElementById('talent-done-btn').addEventListener('click', showEquipStep);
  document.getElementById('talent-skip-btn').addEventListener('click', () => { genState.talent = null; showEquipStep(); });
  document.getElementById('back-to-nwp-btn').addEventListener('click', showNWPStep);

  // Equipment step
  document.getElementById('roll-gold-btn').addEventListener('click', rollStartingGold);
  document.getElementById('set-gold-btn').addEventListener('click', () => {
    const v = parseInt(document.getElementById('manual-gold-input').value);
    if (!v || v < 0) return;
    genState.gold = v;
    genState.cart = [];
    openShop();
  });
  document.querySelectorAll('.shop-tab').forEach(btn => {
    btn.addEventListener('click', () => renderShopTab(btn.dataset.tab));
  });
  document.getElementById('equip-done-btn').addEventListener('click', showNameStep);
  document.getElementById('back-to-equip-btn').addEventListener('click', () => {
    hideGenSteps();
    document.getElementById('step-equip').style.display = 'block';
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
