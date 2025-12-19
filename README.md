<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Modpack Quest Book - Ultimate Edition</title>
    <style>
        :root {
            --bg-dark: #0f1115;
            --bg-sidebar: #161920;
            --bg-card: #1c212b;
            --bg-card-hover: #232936;
            --primary: #bb86fc; 
            --accent: #03dac6;
            --text-main: #e2e8f0;
            --text-muted: #94a3b8;
            --border: #2d3748;
            --success: #10b981;
            --reward: #f59e0b;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; outline: none; }
        
        body {
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            background-color: var(--bg-dark);
            color: var(--text-main);
            display: flex;
            height: 100vh;
            overflow: hidden;
            font-size: 15px;
        }

        /* --- SIDEBAR --- */
        .sidebar {
            width: 320px;
            background-color: var(--bg-sidebar);
            border-right: 1px solid var(--border);
            display: flex;
            flex-direction: column;
            flex-shrink: 0;
            transition: transform 0.3s ease;
            z-index: 20;
        }

        .brand {
            padding: 24px;
            font-size: 1.25rem;
            font-weight: 800;
            letter-spacing: 0.5px;
            color: var(--primary);
            text-transform: uppercase;
            border-bottom: 1px solid var(--border);
            display: flex;
            align-items: center;
            gap: 10px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }

        .chapter-list {
            flex: 1;
            overflow-y: auto;
            list-style: none;
            padding: 10px 0;
        }

        .chapter-list::-webkit-scrollbar { width: 6px; }
        .chapter-list::-webkit-scrollbar-thumb { background: #333; border-radius: 3px; }

        .chapter-item {
            padding: 14px 24px;
            cursor: pointer;
            border-left: 3px solid transparent;
            transition: all 0.2s;
            display: flex;
            justify-content: space-between;
            align-items: center;
            color: var(--text-muted);
            font-weight: 500;
        }

        .chapter-item:hover { 
            background-color: rgba(255,255,255,0.03); 
            color: var(--text-main); 
        }
        
        .chapter-item.active { 
            background-color: rgba(187, 134, 252, 0.08); 
            border-left-color: var(--primary);
            color: var(--primary);
        }

        .chapter-badge {
            background: rgba(0,0,0,0.3);
            font-size: 0.75rem;
            padding: 2px 8px;
            border-radius: 12px;
            color: var(--text-muted);
        }
        .chapter-item.active .chapter-badge {
            background: var(--primary);
            color: #000;
            font-weight: bold;
        }

        /* --- MAIN CONTENT --- */
        .main-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            position: relative;
        }

        .header {
            padding: 16px 40px;
            border-bottom: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: rgba(22, 25, 32, 0.95);
            backdrop-filter: blur(8px);
            z-index: 10;
        }

        .progress-block {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .progress-bar-bg {
            width: 200px;
            height: 8px;
            background: #333;
            border-radius: 4px;
            overflow: hidden;
        }

        .progress-bar-fill {
            height: 100%;
            background: linear-gradient(90deg, var(--primary), var(--accent));
            width: 0%;
            transition: width 0.5s ease;
        }

        .progress-text {
            font-size: 0.9rem;
            font-weight: 600;
            color: var(--text-main);
        }

        .search-wrapper {
            position: relative;
        }

        .search-bar {
            background: #0f1115;
            border: 1px solid var(--border);
            color: white;
            padding: 10px 16px 10px 40px;
            border-radius: 8px;
            width: 320px;
            font-size: 0.9rem;
            transition: border-color 0.2s;
        }
        .search-bar:focus { border-color: var(--primary); }
        
        .search-icon {
            position: absolute;
            left: 12px;
            top: 50%;
            transform: translateY(-50%);
            color: #666;
            font-size: 1.1rem;
        }

        /* --- SCROLL AREA --- */
        .content-scroll {
            flex: 1;
            overflow-y: auto;
            padding: 40px;
            scroll-behavior: smooth;
        }
        .content-scroll::-webkit-scrollbar { width: 8px; }
        .content-scroll::-webkit-scrollbar-track { background: #0f1115; }
        .content-scroll::-webkit-scrollbar-thumb { background: #444; border-radius: 4px; }

        .chapter-header { margin-bottom: 40px; animation: fadeIn 0.4s ease; }
        .chapter-subtitle { color: var(--primary); font-size: 0.85rem; letter-spacing: 2px; text-transform: uppercase; font-weight: 700; margin-bottom: 8px; }
        .chapter-title { font-size: 2.5rem; font-weight: 700; color: white; letter-spacing: -0.5px; }

        /* --- QUEST GRID --- */
        .quest-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(340px, 1fr));
            gap: 24px;
            padding-bottom: 40px;
        }

        .quest-card {
            background-color: var(--bg-card);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 24px;
            position: relative;
            display: flex;
            flex-direction: column;
            gap: 12px;
            transition: all 0.25s ease;
            animation: slideUp 0.3s ease forwards;
            opacity: 0;
            transform: translateY(10px);
        }

        .quest-card:hover {
            transform: translateY(-5px);
            background-color: var(--bg-card-hover);
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            border-color: #4a5568;
        }

        .quest-card.completed {
            border-color: rgba(16, 185, 129, 0.3);
            background: linear-gradient(160deg, rgba(16, 185, 129, 0.05) 0%, var(--bg-card) 100%);
        }

        .quest-card.completed .quest-title {
            color: var(--success);
            text-decoration: line-through;
            opacity: 0.8;
        }

        .card-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
        }

        .quest-id {
            font-size: 0.75rem;
            color: var(--text-muted);
            font-family: monospace;
            background: rgba(0,0,0,0.3);
            padding: 2px 6px;
            border-radius: 4px;
        }

        .quest-title {
            font-size: 1.15rem;
            font-weight: 700;
            color: #fff;
            line-height: 1.3;
            margin-top: 8px;
        }

        .quest-desc {
            font-size: 0.95rem;
            color: #94a3b8;
            line-height: 1.6;
            flex-grow: 1;
        }

        .quest-footer {
            margin-top: 16px;
            padding-top: 16px;
            border-top: 1px solid rgba(255,255,255,0.05);
            font-size: 0.85rem;
        }

        .prereq {
            color: #64748b;
            font-size: 0.8rem;
            margin-bottom: 8px;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .reward-box {
            display: flex;
            align-items: center;
            gap: 10px;
            background: rgba(245, 158, 11, 0.1);
            padding: 10px 12px;
            border-radius: 8px;
            color: var(--reward);
            font-weight: 600;
            margin-top: 5px;
            border: 1px solid rgba(245, 158, 11, 0.2);
        }

        .check-btn {
            width: 28px;
            height: 28px;
            border-radius: 50%;
            border: 2px solid #4a5568;
            background: transparent;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            color: transparent;
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
            font-size: 16px;
        }
        
        .check-btn:hover { border-color: var(--primary); transform: scale(1.1); }

        .quest-card.completed .check-btn {
            background: var(--success);
            border-color: var(--success);
            color: white;
            box-shadow: 0 0 10px rgba(16, 185, 129, 0.4);
        }

        .reset-btn {
            background: transparent;
            border: 1px solid #ef4444;
            color: #ef4444;
            padding: 6px 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.8rem;
            margin-left: auto;
            transition: all 0.2s;
        }
        .reset-btn:hover { background: #ef4444; color: white; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes slideUp { to { opacity: 1; transform: translateY(0); } }

        /* Mobile Responsive */
        @media (max-width: 768px) {
            body { flex-direction: column; height: auto; overflow: auto; }
            .sidebar { width: 100%; height: auto; border-right: none; border-bottom: 1px solid var(--border); }
            .chapter-list { display: flex; overflow-x: auto; padding: 10px; white-space: nowrap; }
            .chapter-item { padding: 10px 15px; border-left: none; border-bottom: 3px solid transparent; }
            .chapter-item.active { border-bottom-color: var(--primary); border-left: none; background: transparent; }
            .header { padding: 15px; flex-direction: column; gap: 15px; align-items: stretch; }
            .search-bar { width: 100%; }
            .quest-grid { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>

    <!-- SIDEBAR -->
    <div class="sidebar">
        <div class="brand">
            <span>⚔️ Quest Book</span>
        </div>
        <ul class="chapter-list" id="chapterList">
            <!-- Capitoli generati via JS -->
        </ul>
        <div style="padding: 20px; text-align: center; border-top: 1px solid var(--border);">
            <button onclick="resetAll()" class="reset-btn">Reset Totale</button>
        </div>
    </div>

    <!-- MAIN CONTENT -->
    <div class="main-content">
        <div class="header">
            <div class="progress-block">
                <div class="progress-bar-bg">
                    <div class="progress-bar-fill" id="progressBar"></div>
                </div>
                <div class="progress-text" id="progressText">0%</div>
            </div>
            
            <div class="search-wrapper">
                <span class="search-icon">🔍</span>
                <input type="text" class="search-bar" id="searchInput" placeholder="Cerca quest, reward o ID...">
            </div>
        </div>
        
        <div class="content-scroll">
            <div class="chapter-header">
                <div class="chapter-subtitle" id="chapterSubtitle">CAPITOLO 1</div>
                <h1 class="chapter-title" id="chapterTitle">Sopravvivenza</h1>
            </div>
            <div class="quest-grid" id="questContainer">
                <!-- Card generate via JS -->
            </div>
        </div>
    </div>

<script>
// --- DATABASE MASSIVO DI QUEST (1-18) ---
const chaptersData = [
  // --- CAPITOLO 1: SURVIVAL ---
  {
    "id": "1", "title": "Sopravvivenza Base", "quests": [
      { "id": "1.1", "title": "Primi Passi", "description": "Raccogli 4 blocchi di legno.", "reward": "Mele x4 (Cibo)" },
      { "id": "1.2", "title": "Crafting", "description": "Costruisci una Crafting Table.", "prerequisites": ["1.1"], "reward": "Stick x16" },
      { "id": "1.3", "title": "Mining Tool", "description": "Crafta un Piccone di Legno.", "prerequisites": ["1.2"], "reward": "Torce x16" },
      { "id": "1.4", "title": "Upgrade Pietra", "description": "Crafta un set completo di tool in pietra.", "prerequisites": ["1.3"], "reward": "Pane x8" },
      { "id": "1.5", "title": "Fusione", "description": "Costruisci una Fornace.", "prerequisites": ["1.4"], "reward": "Carbone x16" },
      { "id": "1.6", "title": "Iron Age", "description": "Cuoci il ferro e fatti un piccone di ferro.", "prerequisites": ["1.5"], "reward": "Secchio d'acqua" },
      { "id": "1.7", "title": "Farming", "description": "Zappa della terra e pianta del grano.", "prerequisites": ["1.4"], "reward": "Bone Meal x16" },
      { "id": "1.8", "title": "Armatura", "description": "Indossa un set full Iron.", "prerequisites": ["1.6"], "reward": "Shield" },
      { "id": "1.9", "title": "Diamond Hunter", "description": "Trova 3 diamanti.", "prerequisites": ["1.6"], "reward": "Fortune I Book" },
      { "id": "1.10", "title": "Obsidian", "description": "Mina 10 ossidiana.", "prerequisites": ["1.9"], "reward": "Flint and Steel" },
      { "id": "1.11", "title": "Nether", "description": "Costruisci il portale e accendilo.", "prerequisites": ["1.10"], "reward": "Golden Apple" },
      { "id": "1.12", "title": "Enchanting", "description": "Crafta l'Enchanting Table.", "prerequisites": ["1.9"], "reward": "Lapis Lazuli x32" }
    ]
  },

  // --- CAPITOLO 2: STORAGE ---
  {
    "id": "2", "title": "Storage & Sorting", "quests": [
      { "id": "2.1", "title": "Chest Upgrade", "description": "Crafta una Iron Chest.", "reward": "Wood-to-Iron Upgrade" },
      { "id": "2.2", "title": "Gold Chest", "description": "Porta la chest al livello Oro.", "prerequisites": ["2.1"], "reward": "Iron-to-Gold Upgrade" },
      { "id": "2.3", "title": "Diamond Chest", "description": "Storage massivo vanilla-style.", "prerequisites": ["2.2"], "reward": "Gold-to-Diamond Upgrade" },
      { "id": "2.4", "title": "Drawers", "description": "Crafta un Oak Drawer.", "reward": "Upgrade Template x4" },
      { "id": "2.5", "title": "Compacting Drawer", "description": "Converte automaticamente lingotti in blocchi.", "prerequisites": ["2.4"], "reward": "Void Upgrade" },
      { "id": "2.6", "title": "Controller", "description": "Crafta un Drawer Controller.", "prerequisites": ["2.4"], "reward": "Quantify Key & Lock Key" },
      { "id": "2.7", "title": "Backpack", "description": "Crafta uno zaino base.", "reward": "Sleeping Mat" },
      { "id": "2.8", "title": "Diamond Backpack", "description": "Upgrade dello zaino al tier Diamante.", "prerequisites": ["2.7"], "reward": "Magnet Upgrade" },
      { "id": "2.9", "title": "Trash Can", "description": "Costruisci un cestino per eliminare item.", "reward": "Liquid Trash Can" },
      { "id": "2.10", "title": "Storage Crate", "description": "Crafta una Small Storage Crate (Actually Additions o simili).", "reward": "Speed Upgrade (Hopper)" },
      { "id": "2.11", "title": "Dank Storage", "description": "Unità portatile avanzata (Dank Null).", "reward": "Docking Station" },
      { "id": "2.12", "title": "Ender Chest", "description": "Storage condiviso tra dimensioni.", "reward": "Ender Pouch" }
    ]
  },

  // --- CAPITOLO 3: POWER ---
  {
    "id": "3", "title": "Power Generation", "quests": [
      { "id": "3.1", "title": "Generatore Carbone", "description": "Costruisci una Stirling Dynamo.", "reward": "Block of Coal x4" },
      { "id": "3.2", "title": "Cavi Energia", "description": "Crafta 16 cavi base.", "prerequisites": ["3.1"], "reward": "Battery (Buffer)" },
      { "id": "3.3", "title": "Lava Power", "description": "Costruisci una Magmatic Dynamo.", "prerequisites": ["3.2"], "reward": "Fluid Tank (Lava)" },
      { "id": "3.4", "title": "Solar Power", "description": "Costruisci un Pannello Solare I.", "reward": "Daylight Sensor" },
      { "id": "3.5", "title": "Wind Power", "description": "Costruisci un Wind Generator.", "reward": "Building Gadget" },
      { "id": "3.6", "title": "Capacitor Bank", "description": "Costruisci un blocco per accumulare energia.", "prerequisites": ["3.2"], "reward": "Energy Conduit (Upgrade)" },
      { "id": "3.7", "title": "Bio-Generator", "description": "Energia dalle piante/cibo.", "reward": "Melon Seeds x16" },
      { "id": "3.8", "title": "Reactant Dynamo", "description": "Reazione chimica per energia.", "reward": "Ghast Tear x2" },
      { "id": "3.9", "title": "Numismatic Dynamo", "description": "Brucia diamanti o smeraldi per energia.", "reward": "Emerald x4" },
      { "id": "3.10", "title": "Thermoelectric", "description": "Generatore passivo (Caldo/Freddo).", "reward": "Packed Ice x4 & Uranium Block" }
    ]
  },

  // --- CAPITOLO 4: PROCESSING ---
  {
    "id": "4", "title": "Processing & Alloys", "quests": [
      { "id": "4.1", "title": "Pulverizer", "description": "Tritura ore per raddoppiarli.", "reward": "Raw Gold x16" },
      { "id": "4.2", "title": "Redstone Furnace", "description": "Fornace elettrica veloce.", "prerequisites": ["4.1"], "reward": "Speed Upgrade I" },
      { "id": "4.3", "title": "Induction Smelter", "description": "Per creare leghe (Alloys).", "prerequisites": ["4.1"], "reward": "Sand x64" },
      { "id": "4.4", "title": "Invar Ingot", "description": "Lega Ferro + Nickel.", "prerequisites": ["4.3"], "reward": "Hardened Upgrade Kit" },
      { "id": "4.5", "title": "Electrum Ingot", "description": "Lega Oro + Argento.", "prerequisites": ["4.3"], "reward": "Redstone Reception Coil" },
      { "id": "4.6", "title": "Signalum", "description": "Lega Rame + Argento + Redstone.", "prerequisites": ["4.3"], "reward": "Signalum Gear x2" },
      { "id": "4.7", "title": "Enderium", "description": "Lega finale Thermal.", "prerequisites": ["4.6"], "reward": "Resonant Upgrade Kit" },
      { "id": "4.8", "title": "Compactor", "description": "Crea piastre e ingranaggi.", "reward": "Diamond Gear" },
      { "id": "4.9", "title": "Centrifuge", "description": "Separa i fluidi o i dust.", "reward": "Empty Cell x16" },
      { "id": "4.10", "title": "Magma Crucible", "description": "Sciogli la Redstone in liquido.", "reward": "Redstone Block x8" }
    ]
  },

  // --- CAPITOLO 5: AE2 ---
  {
    "id": "5", "title": "Applied Energistics 2", "quests": [
      { "id": "5.1", "title": "Certus Quartz", "description": "Raccogli Certus Quartz.", "reward": "Charged Certus Quartz x8" },
      { "id": "5.2", "title": "Inscriber", "description": "Macchina per i circuiti.", "prerequisites": ["5.1"], "reward": "Silicon x32" },
      { "id": "5.3", "title": "Presses", "description": "Trova le 4 Inscriber Press (o craftale).", "prerequisites": ["5.2"], "reward": "Sky Stone Chest" },
      { "id": "5.4", "title": "ME Controller", "description": "Il cervello della rete.", "prerequisites": ["5.3"], "reward": "Dense Energy Cell" },
      { "id": "5.5", "title": "ME Drive", "description": "L'unità disco.", "prerequisites": ["5.4"], "reward": "Glass Cable x32" },
      { "id": "5.6", "title": "1k Storage", "description": "La prima cella di memoria.", "prerequisites": ["5.5"], "reward": "Storage Housing x3" },
      { "id": "5.7", "title": "4k Storage", "description": "Upgrade della cella.", "prerequisites": ["5.6"], "reward": "Redstone x16" },
      { "id": "5.8", "title": "16k Storage", "description": "Cella media.", "prerequisites": ["5.7"], "reward": "Glowstone Dust x16" },
      { "id": "5.9", "title": "Crafting Terminal", "description": "Accesso + Crafting.", "prerequisites": ["5.4"], "reward": "Interface Terminal" },
      { "id": "5.10", "title": "Import/Export", "description": "Automatizza input e output.", "prerequisites": ["5.9"], "reward": "Speed Card x4" },
      { "id": "5.11", "title": "P2P Tunnel", "description": "Trasferisci canali a distanza.", "reward": "Memory Card" },
      { "id": "5.12", "title": "Autocrafting CPU", "description": "Crea una crafting unit.", "reward": "Molecular Assembler" }
    ]
  },

  // --- CAPITOLO 6: MEKANISM ---
  {
    "id": "6", "title": "Mekanism", "quests": [
      { "id": "6.1", "title": "Osmium", "description": "Trova l'Osmio.", "reward": "Osmium Dust x16" },
      { "id": "6.2", "title": "Metallurgic Infuser", "description": "La base di Mekanism.", "prerequisites": ["6.1"], "reward": "Redstone x64" },
      { "id": "6.3", "title": "Steel Ingot", "description": "Ferro + Carbonio arricchito.", "prerequisites": ["6.2"], "reward": "Enriched Carbon x8" },
      { "id": "6.4", "title": "Enrichment Chamber", "description": "Raddoppia i minerali.", "prerequisites": ["6.2"], "reward": "Energy Upgrade x4" },
      { "id": "6.5", "title": "Crusher", "description": "Tritura bio-fuel o lingotti.", "prerequisites": ["6.2"], "reward": "Speed Upgrade x4" },
      { "id": "6.6", "title": "Wind Generator", "description": "Energia pulita e alta.", "reward": "Alloy (Reinforced) x2" },
      { "id": "6.7", "title": "Digital Miner", "description": "Mining automatico con filtri.", "prerequisites": ["6.3"], "reward": "Anchor Upgrade" },
      { "id": "6.8", "title": "Advanced Factory", "description": "Macchina a 3 slot (Tier 2).", "prerequisites": ["6.4"], "reward": "Sorting Upgrade" },
      { "id": "6.9", "title": "Elite Factory", "description": "Macchina a 5 slot (Tier 3).", "prerequisites": ["6.8"], "reward": "Gas Upgrade x8" },
      { "id": "6.10", "title": "Purification Chamber", "description": "Inizio del x3 Ore Processing.", "prerequisites": ["6.4"], "reward": "Oxygen Gas Tank" },
      { "id": "6.11", "title": "Chemical Injection", "description": "Inizio del x4 Ore Processing.", "prerequisites": ["6.10"], "reward": "Sulfur Dust x16" },
      { "id": "6.12", "title": "Atomic Alloy", "description": "Il materiale finale.", "prerequisites": ["6.3"], "reward": "Ultimate Control Circuit" }
    ]
  },

  // --- CAPITOLO 7: TINKERS ---
  {
    "id": "7", "title": "Tinkers' Construct", "quests": [
      { "id": "7.1", "title": "Grout", "description": "Prepara il Grout.", "reward": "Clay Block x16" },
      { "id": "7.2", "title": "Smeltery Controller", "description": "Cuore della fonderia.", "prerequisites": ["7.1"], "reward": "Seared Bricks x32" },
      { "id": "7.3", "title": "Lava Tank", "description": "Serbatoio per il fuel.", "prerequisites": ["7.2"], "reward": "Lava Bucket x4" },
      { "id": "7.4", "title": "Gold Casts", "description": "Crea gli stampi in oro.", "prerequisites": ["7.2"], "reward": "Gold Ingot x8" },
      { "id": "7.5", "title": "Alloy: Bronze", "description": "Rame + Stagno (3:1).", "prerequisites": ["7.2"], "reward": "Redstone (Haste)" },
      { "id": "7.6", "title": "Alloy: Manyullyn", "description": "Cobalto + Ardite.", "prerequisites": ["7.2"], "reward": "Quartz (Sharpness)" },
      { "id": "7.7", "title": "Tool Forge", "description": "Per creare tool avanzati.", "prerequisites": ["7.2"], "reward": "Block of Iron x4" },
      { "id": "7.8", "title": "Hammer", "description": "Scava 3x3.", "prerequisites": ["7.7"], "reward": "Diamond (Durability)" },
      { "id": "7.9", "title": "Lumber Axe", "description": "Abbatti interi alberi.", "prerequisites": ["7.7"], "reward": "Emerald (Durability)" },
      { "id": "7.10", "title": "Modifiers", "description": "Applica 3 modificatori a un tool.", "reward": "Creative Modifier (Se abilitato) o Gold Block" }
    ]
  },

  // --- CAPITOLO 8: RFTOOLS ---
  {
    "id": "8", "title": "RFTools Dimensions", "quests": [
      { "id": "8.1", "title": "Machine Frame", "description": "Base per le macchine RFTools.", "reward": "Iron Ingot x16" },
      { "id": "8.2", "title": "Builder", "description": "Quarry o Builder versatile.", "prerequisites": ["8.1"], "reward": "Shape Card (Quarry)" },
      { "id": "8.3", "title": "Dimlet Workbench", "description": "Decostruisci e crea Dimlet.", "reward": "Unknown Dimlet x16" },
      { "id": "8.4", "title": "Dimension Enscriber", "description": "Scrivi la tua dimensione.", "prerequisites": ["8.3"], "reward": "Empty Dimension Tab" },
      { "id": "8.5", "title": "Dimension Builder", "description": "Genera e mantieni la dimensione.", "prerequisites": ["8.4"], "reward": "Dimensional Shard x64" },
      { "id": "8.6", "title": "Matter Transmitter", "description": "Teletrasporto (Partenza).", "reward": "Ender Pearl x8" },
      { "id": "8.7", "title": "Matter Receiver", "description": "Teletrasporto (Arrivo).", "prerequisites": ["8.6"], "reward": "Dialing Device" },
      { "id": "8.8", "title": "Powercell", "description": "Batteria multiblock wireless.", "reward": "Powercell Card" },
      { "id": "8.9", "title": "Shield Projector", "description": "Proteggi la base.", "reward": "Shield Template Block x16" }
    ]
  },

  // --- CAPITOLO 9: THERMAL ADV ---
  {
    "id": "9", "title": "Thermal Advanced", "quests": [
      { "id": "9.1", "title": "Phytogenic Insolator", "description": "Grow chamber avanzata.", "reward": "Phyto-Gro x64" },
      { "id": "9.2", "title": "Signalum Upgrade", "description": "Kit upgrade Tier 3.", "reward": "Augment: Auxiliary Reception Coil" },
      { "id": "9.3", "title": "Resonant Upgrade", "description": "Kit upgrade Tier 4 (Finale).", "prerequisites": ["9.2"], "reward": "Augment: Nullification Chamber" },
      { "id": "9.4", "title": "Tesseract", "description": "Teletrasporto item/fluidi/energia.", "reward": "Ender Pearl Block x2" },
      { "id": "9.5", "title": "Thermal Mediator", "description": "Accelera le macchine vicine.", "reward": "Aqua Chow x32" },
      { "id": "9.6", "title": "Glacial Precipitator", "description": "Crea ghiaccio e neve.", "reward": "Cryotheum Dust x8" },
      { "id": "9.7", "title": "Igneous Extruder", "description": "Cobble/Stone/Obsidian gen.", "reward": "Drill Head" },
      { "id": "9.8", "title": "Satchels", "description": "Zaini Thermal.", "reward": "Holding Enchant Book" }
    ]
  },

  // --- CAPITOLO 10: AE2 ADVANCED ---
  {
    "id": "10", "title": "AE2 Advanced", "quests": [
      { "id": "10.1", "title": "Crafting Co-Processor", "description": "Velocizza l'autocrafting.", "reward": "Crafting Unit x4" },
      { "id": "10.2", "title": "64k Crafting Storage", "description": "Per craft complessi.", "prerequisites": ["10.1"], "reward": "Blank Pattern x16" },
      { "id": "10.3", "title": "Interface", "description": "Il ponte tra AE2 e le macchine.", "reward": "Speed Card x4" },
      { "id": "10.4", "title": "Molecular Assembler", "description": "Esegue i craft.", "prerequisites": ["10.3"], "reward": "Smart Cable x16" },
      { "id": "10.5", "title": "Dense Cable", "description": "Porta 32 canali.", "reward": "P2P Tunnel (ME) x2" },
      { "id": "10.6", "title": "Wireless Access", "description": "Accedi alla rete senza cavi.", "reward": "Wireless Booster x32" },
      { "id": "10.7", "title": "Quantum Network Bridge", "description": "AE2 attraverso le dimensioni.", "reward": "Singularity (AE2) x2" },
      { "id": "10.8", "title": "Spatial IO", "description": "Sposta intere stanze in un disco.", "reward": "Spatial Cell (128^3)" },
      { "id": "10.9", "title": "Security Terminal", "description": "Collega il Wireless alla tua player ID.", "reward": "Biometric Card" },
      { "id": "10.10", "title": "Level Emitter", "description": "Automatizza in base alla quantità.", "reward": "Redstone Card x2" }
    ]
  },

  // --- CAPITOLO 11: EXTREME REACTORS ---
  {
    "id": "11", "title": "Extreme Reactors", "quests": [
      { "id": "11.1", "title": "Yellorium Ore", "description": "Raccogli l'uranio/yellorium.", "reward": "Geiger Counter" },
      { "id": "11.2", "title": "Reactor Casing", "description": "Blocchi strutturali.", "prerequisites": ["11.1"], "reward": "Reactor Glass x16" },
      { "id": "11.3", "title": "Fuel Rods", "description": "Dove avviene la reazione.", "prerequisites": ["11.2"], "reward": "Yellorium Ingot x32" },
      { "id": "11.4", "title": "Control Rods", "description": "Controlla la reazione.", "prerequisites": ["11.3"], "reward": "Redstone Comparator" },
      { "id": "11.5", "title": "Reactor Controller", "description": "L'interfaccia principale.", "prerequisites": ["11.2"], "reward": "Diamond Block x2" },
      { "id": "11.6", "title": "Access Port", "description": "Input Fuel / Output Waste.", "reward": "Cyanite Ingot x16" },
      { "id": "11.7", "title": "Active Cooling", "description": "Usa acqua/vapore invece di passivo.", "reward": "Fluid Port x2" },
      { "id": "11.8", "title": "Turbine Housing", "description": "Struttura della turbina.", "prerequisites": ["11.7"], "reward": "Turbine Glass x16" },
      { "id": "11.9", "title": "Blades & Shaft", "description": "Il rotore della turbina.", "prerequisites": ["11.8"], "reward": "Ludicrite Block (Best Coil)" },
      { "id": "11.10", "title": "Reprocessing", "description": "Trasforma Cyanite in Blutonium.", "reward": "Blutonium Ingot x4" }
    ]
  },

  // --- CAPITOLO 12: DRACONIC EVOLUTION ---
  {
    "id": "12", "title": "Draconic Evolution", "quests": [
      { "id": "12.1", "title": "Draconium Dust", "description": "Trova la polvere nel Nether o nell'End.", "reward": "Draconium Chest" },
      { "id": "12.2", "title": "Draconium Ingot", "description": "Cuoci la polvere.", "prerequisites": ["12.1"], "reward": "Draconium Dust x16" },
      { "id": "12.3", "title": "Draconic Core", "description": "Componente base.", "prerequisites": ["12.2"], "reward": "Basic Energy Relay x4" },
      { "id": "12.4", "title": "Wyvern Core", "description": "Tier 2. Richiede Nether Star.", "prerequisites": ["12.3"], "reward": "Wyvern Energy Core" },
      { "id": "12.5", "title": "Fusion Crafting", "description": "Costruisci il Core e 8 Injectors.", "prerequisites": ["12.4"], "reward": "Diamond x64 (Fuel)" },
      { "id": "12.6", "title": "Wyvern Armor", "description": "Crafta un set completo Wyvern.", "prerequisites": ["12.5"], "reward": "RF Capacity Upgrade Key" },
      { "id": "12.7", "title": "Awakened Draconium", "description": "Rituale con TNT e Dragon Heart.", "prerequisites": ["12.5"], "reward": "Dragon Heart (Backup)" },
      { "id": "12.8", "title": "Draconic Core (Awakened)", "description": "Tier 3.", "prerequisites": ["12.7"], "reward": "Awakened Draconium x4" },
      { "id": "12.9", "title": "Draconic Armor", "description": "L'armatura arancione OP.", "prerequisites": ["12.8"], "reward": "Shield Recovery Upgrade Key" },
      { "id": "12.10", "title": "Staff of Power", "description": "Scava 9x9 e shotta tutto.", "prerequisites": ["12.8"], "reward": "Enchanted Book (Soulbound)" },
      { "id": "12.11", "title": "Energy Core", "description": "Accumulatore Tier 1.", "reward": "Draconium Block x10" },
      { "id": "12.12", "title": "Chaos Guardian", "description": "Sconfiggi il boss finale dell'End.", "prerequisites": ["12.9"], "reward": "Chaos Shard x2" },
      { "id": "12.13", "title": "Draconic Reactor", "description": "Generazione immensa, rischio esplosione.", "prerequisites": ["12.7"], "reward": "Flux Gate (Sicurezza)" }
    ]
  },

  // --- CAPITOLO 13: PROJECTE ---
  {
    "id": "13", "title": "ProjectE & EMC", "quests": [
      { "id": "13.1", "title": "Philosopher's Stone", "description": "Crafting portatile e trasmutazione.", "reward": "Charcoal x64" },
      { "id": "13.2", "title": "Transmutation Table", "description": "Scambia materia per EMC.", "prerequisites": ["13.1"], "reward": "Tome of Knowledge (Unlock)" },
      { "id": "13.3", "title": "Energy Condenser", "description": "Duplica oggetti.", "prerequisites": ["13.2"], "reward": "Diamond Block (Target)" },
      { "id": "13.4", "title": "Dark Matter", "description": "Materia oscura compressa.", "prerequisites": ["13.1"], "reward": "Dark Matter Pedestal" },
      { "id": "13.5", "title": "Red Matter", "description": "Materia rossa.", "prerequisites": ["13.4"], "reward": "Aeternalis Fuel x32" },
      { "id": "13.6", "title": "Energy Collector", "description": "Genera EMC dalla luce.", "reward": "Anti-Matter Relay" },
      { "id": "13.7", "title": "Dark Matter Tools", "description": "Tool indistruttibili.", "prerequisites": ["13.4"], "reward": "Volcanite Amulet" },
      { "id": "13.8", "title": "Red Matter Tools", "description": "Tool ancora più veloci.", "prerequisites": ["13.5"], "reward": "Evertide Amulet" },
      { "id": "13.9", "title": "Watch of Flowing Time", "description": "Accelera le macchine vicine.", "prerequisites": ["13.5"], "reward": "Klein Star Omega (Full)" },
      { "id": "13.10", "title": "Gem Armor", "description": "Invincibilità creativa.", "prerequisites": ["13.5"], "reward": "Creative Flight Token" },
      { "id": "13.11", "title": "Transmutation Tablet", "description": "Table portatile.", "prerequisites": ["13.2"], "reward": "Alchemical Bag" }
    ]
  },

  // --- CAPITOLO 14: ADVANCED LOGISTICS ---
  {
    "id": "14", "title": "Advanced Logistics", "quests": [
      { "id": "14.1", "title": "XNet Controller", "description": "Gestione cavi avanzata.", "reward": "Blue Network Cable x64" },
      { "id": "14.2", "title": "Connector", "description": "Collega le macchine a XNet.", "prerequisites": ["14.1"], "reward": "Advanced Connector x8" },
      { "id": "14.3", "title": "LaserIO", "description": "Logistica istantanea laser.", "reward": "Laser Node x8" },
      { "id": "14.4", "title": "Laser Cards", "description": "Configura Item/Fluid/Energy.", "prerequisites": ["14.3"], "reward": "Overclocker Card x4" },
      { "id": "14.5", "title": "Integrated Dynamics", "description": "Crafta Menril Wood.", "reward": "Squeezer & Drying Basin" },
      { "id": "14.6", "title": "Logic Programmer", "description": "Programma variabili complesse.", "prerequisites": ["14.5"], "reward": "Variable Card x64" },
      { "id": "14.7", "title": "Entangled Block", "description": "Connetti due blocchi distanti.", "reward": "Entangled Binder" },
      { "id": "14.8", "title": "Flux Networks", "description": "Energia wireless.", "reward": "Flux Point x4" },
      { "id": "14.9", "title": "Gargantuan Flux Storage", "description": "Accumulatore wireless enorme.", "prerequisites": ["14.8"], "reward": "Flux Plug x2" },
      { "id": "14.10", "title": "Dimensional Transceiver", "description": "Ender IO o Thermal wireless.", "reward": "Ender Pearl Block x4" }
    ]
  },

  // --- CAPITOLO 15: MULTIBLOCKS ---
  {
    "id": "15", "title": "Multiblock Integration", "quests": [
      { "id": "15.1", "title": "Industrial Foregoing", "description": "Crafta la Dissolution Chamber.", "reward": "Pink Slime x16" },
      { "id": "15.2", "title": "Latex Processing", "description": "Produci plastica.", "prerequisites": ["15.1"], "reward": "Dry Rubber x64" },
      { "id": "15.3", "title": "Laser Drill", "description": "Mining dal vuoto.", "prerequisites": ["15.2"], "reward": "Laser Lens (Lime)" },
      { "id": "15.4", "title": "Mob Crusher", "description": "Farm automatica di mob.", "reward": "Range Addon (+12)" },
      { "id": "15.5", "title": "Wither Builder", "description": "Spawna il Wither.", "reward": "Nether Star x8" },
      { "id": "15.6", "title": "Environmental Tech", "description": "Crafta il Void Ore Miner Tier 1.", "reward": "Structure Frame Tier 1 x32" },
      { "id": "15.7", "title": "Tier 6 Miner", "description": "Massima efficienza ET.", "prerequisites": ["15.6"], "reward": "Litherite Crystal" },
      { "id": "15.8", "title": "Rainbow Generator", "description": "Genera 25M RF/t (Extra Utilities).", "reward": "Ultimate Energy Cube" },
      { "id": "15.9", "title": "Coke Oven", "description": "Immersive Engineering multiblock.", "reward": "Coal Coke Block x4" },
      { "id": "15.10", "title": "Arc Furnace", "description": "Fornace ad arco voltaico.", "reward": "Graphite Electrode x3" }
    ]
  },

  // --- CAPITOLO 16: STORAGE MEGA-SCALING ---
  {
    "id": "16", "title": "Storage Mega-Scaling", "quests": [
      { "id": "16.1", "title": "64k Storage Cell", "description": "Il massimo standard AE2.", "reward": "Storage Housing x8" },
      { "id": "16.2", "title": "1M Storage Cell", "description": "MegaCells o simili.", "prerequisites": ["16.1"], "reward": "Mega Pattern Provider" },
      { "id": "16.3", "title": "64M Storage Cell", "description": "Storage quasi infinito.", "prerequisites": ["16.2"], "reward": "Cell Workbench" },
      { "id": "16.4", "title": "Black Hole Unit", "description": "2 Miliardi di oggetti.", "reward": "Black Hole Controller" },
      { "id": "16.5", "title": "Black Hole Tank", "description": "2 Miliardi di liquidi.", "reward": "Fluid Trash Can" },
      { "id": "16.6", "title": "Functional Storage", "description": "Armory Cabinet (NBT items).", "reward": "Configuration Tool" },
      { "id": "16.7", "title": "Infinite Disk", "description": "Creative Disk (Crafting finale).", "prerequisites": ["16.3"], "reward": "Creative Energy Cell" },
      { "id": "16.8", "title": "Bulk Storage", "description": "Crea 10 Black Hole Unit piene.", "reward": "Infinity Catalyst" }
    ]
  },

  // --- CAPITOLO 17: CAPSTONE ---
  {
    "id": "17", "title": "Capstone (Avaritia)", "quests": [
      { "id": "17.1", "title": "Neutronium Compressor", "description": "Inizia Avaritia. Comprimi.", "reward": "Time in a Bottle" },
      { "id": "17.2", "title": "Neutron Collector", "description": "Raccogli neutroni passivamente.", "prerequisites": ["17.1"], "reward": "Matrix Ingot x4" },
      { "id": "17.3", "title": "Iron Singularity", "description": "Comprimi migliaia di blocchi di ferro.", "prerequisites": ["17.1"], "reward": "Block of Iron x64" },
      { "id": "17.4", "title": "Golden Singularity", "description": "Comprimi oro.", "prerequisites": ["17.3"], "reward": "Block of Gold x64" },
      { "id": "17.5", "title": "Lapis Singularity", "description": "Comprimi lapis.", "prerequisites": ["17.3"], "reward": "Lapis Block x64" },
      { "id": "17.6", "title": "Infinity Catalyst", "description": "Il piatto forte. Richiede tutto.", "prerequisites": ["17.5"], "reward": "Cosmic Meatballs (Cibo Infinito)" },
      { "id": "17.7", "title": "Infinity Ingot", "description": "Il lingotto finale.", "prerequisites": ["17.6"], "reward": "Creative Vending Upgrade" },
      { "id": "17.8", "title": "Sword of the Cosmos", "description": "Danno infinito.", "prerequisites": ["17.7"], "reward": "Trophy: Master of the Universe" },
      { "id": "17.9", "title": "World Breaker", "description": "Piccone infinito Fortune X.", "prerequisites": ["17.7"], "reward": "Bedrock Block" },
      { "id": "17.10", "title": "Infinity Armor", "description": "Diventa un Dio.", "prerequisites": ["17.7"], "reward": "Creative Barrage (Se presente)" }
    ]
  },

  // --- CAPITOLO 18: VARIE ---
  {
    "id": "18", "title": "Varie & Cosmetics", "quests": [
      { "id": "18.1", "title": "Building Gadget", "description": "Costruisci velocemente.", "reward": "Construction Paste Container" },
      { "id": "18.2", "title": "Exchanger", "description": "Scambia blocchi nel mondo.", "reward": "Gunpowder x64" },
      { "id": "18.3", "title": "Copy-Paste Gadget", "description": "Clona strutture.", "reward": "Template Manager" },
      { "id": "18.4", "title": "Angel Ring", "description": "Volo creativo (Extra Utils).", "reward": "Experience Obelisk" },
      { "id": "18.5", "title": "Cooking for Blockheads", "description": "Cucina completa multiblock.", "reward": "Cow in a Jar (Latte Infinito)" },
      { "id": "18.6", "title": "Deep Mob Learning", "description": "Simula i drop dei mob.", "reward": "Glitch Heart" },
      { "id": "18.7", "title": "Glitch Armor", "description": "Armatura da DML.", "prerequisites": ["18.6"], "reward": "Soot-Covered Plate" },
      { "id": "18.8", "title": "Torchmaster", "description": "Mega Torch (No spawn).", "reward": "Feral Flare Lantern" },
      { "id": "18.9", "title": "Dark Utilities", "description": "Vector Plates per mob farm.", "reward": "Mob Filter" },
      { "id": "18.10", "title": "Apotheosis", "description": "Enchanting al livello 100.", "reward": "Library of Alexandria" }
    ]
  }
];

// --- APP LOGIC ---
const state = {
    activeChapter: "1",
    completed: JSON.parse(localStorage.getItem('completedQuests')) || [],
    search: ''
};

const dom = {
    chapterList: document.getElementById('chapterList'),
    questContainer: document.getElementById('questContainer'),
    chapterTitle: document.getElementById('chapterTitle'),
    chapterSubtitle: document.getElementById('chapterSubtitle'),
    progressBar: document.getElementById('progressBar'),
    progressText: document.getElementById('progressText'),
    searchInput: document.getElementById('searchInput')
};

function init() {
    renderSidebar();
    renderQuests();
    
    dom.searchInput.addEventListener('input', (e) => {
        state.search = e.target.value.toLowerCase();
        renderQuests();
    });
}

function renderSidebar() {
    dom.chapterList.innerHTML = '';
    chaptersData.forEach(ch => {
        const li = document.createElement('li');
        li.className = `chapter-item ${state.activeChapter === ch.id ? 'active' : ''}`;
        
        const doneCount = ch.quests.filter(q => state.completed.includes(q.id)).length;
        
        li.innerHTML = `
            <span>${ch.title}</span>
            <span class="chapter-badge">${doneCount}/${ch.quests.length}</span>
        `;
        
        li.onclick = () => {
            state.activeChapter = ch.id;
            state.search = '';
            dom.searchInput.value = '';
            renderSidebar();
            renderQuests();
        };
        
        dom.chapterList.appendChild(li);
    });
    updateProgress();
}

function renderQuests() {
    const container = dom.questContainer;
    container.innerHTML = '';
    
    let questsToShow = [];
    let title = "";
    let sub = "";

    if (state.search) {
        title = "Ricerca";
        sub = `Risultati per "${state.search}"`;
        chaptersData.forEach(ch => {
            const matches = ch.quests.filter(q => 
                q.title.toLowerCase().includes(state.search) || 
                q.reward.toLowerCase().includes(state.search) ||
                q.id.toLowerCase().includes(state.search)
            );
            questsToShow = [...questsToShow, ...matches];
        });
    } else {
        const activeCh = chaptersData.find(c => c.id === state.activeChapter);
        if(activeCh) {
            questsToShow = activeCh.quests;
            title = activeCh.title;
            sub = `CAPITOLO ${activeCh.id}`;
        }
    }

    dom.chapterTitle.textContent = title;
    dom.chapterSubtitle.textContent = sub;

    if (questsToShow.length === 0) {
        container.innerHTML = `<div style="color:#666; font-style:italic; grid-column: 1/-1; text-align:center; padding: 40px;">Nessuna quest trovata.</div>`;
        return;
    }

    // Sort: Non completate prima, poi completate
    questsToShow.sort((a, b) => {
        const aDone = state.completed.includes(a.id);
        const bDone = state.completed.includes(b.id);
        return aDone === bDone ? 0 : aDone ? 1 : -1;
    });

    questsToShow.forEach((q, index) => {
        const isDone = state.completed.includes(q.id);
        const card = document.createElement('div');
        card.className = `quest-card ${isDone ? 'completed' : ''}`;
        card.style.animationDelay = `${index * 50}ms`;
        
        card.innerHTML = `
            <div class="card-header">
                <span class="quest-id">${q.id}</span>
                <button class="check-btn" onclick="toggle('${q.id}')">
                    ${isDone ? '✓' : ''}
                </button>
            </div>
            
            <h3 class="quest-title">${q.title}</h3>
            <p class="quest-desc">${q.description}</p>
            
            <div class="quest-footer">
                ${q.prerequisites ? `
                    <div class="prereq">
                        <span>🔗 Richiede: ${q.prerequisites.join(', ')}</span>
                    </div>
                ` : ''}
                <div class="reward-box">
                    <span>🎁 ${q.reward}</span>
                </div>
            </div>
        `;
        container.appendChild(card);
    });
}

window.toggle = (id) => {
    if(state.completed.includes(id)) {
        state.completed = state.completed.filter(x => x !== id);
    } else {
        state.completed.push(id);
        // Effetto sonoro opzionale o visual feedback qui
    }
    localStorage.setItem('completedQuests', JSON.stringify(state.completed));
    renderSidebar();
    renderQuests();
};

window.resetAll = () => {
    if(confirm("Sei sicuro di voler resettare tutti i progressi?")) {
        state.completed = [];
        localStorage.setItem('completedQuests', JSON.stringify([]));
        init();
    }
};

function updateProgress() {
    let total = 0;
    let done = 0;
    chaptersData.forEach(ch => {
        total += ch.quests.length;
        done += ch.quests.filter(q => state.completed.includes(q.id)).length;
    });
    
    const pct = total === 0 ? 0 : Math.round((done / total) * 100);
    dom.progressBar.style.width = `${pct}%`;
    dom.progressText.textContent = `${pct}%`;
}

// Avvio
init();

</script>
</body>
</html>
