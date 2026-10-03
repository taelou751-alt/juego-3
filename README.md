# juego-3
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Gravity Falls: La Ruta de los Seis Dedos</title>
    <style>
        :root {
            --gf-parchment: #f4e8c1;
            --gf-dark-wood: #2b1d0c;
            --gf-burgundy: #5a1818;
            --gf-gold: #d4af37;
            --gf-cyan: #00e5ff;
            --gf-font: 'Georgia', serif;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
        }

        body {
            background-color: #120b04;
            color: #2b1d0c;
            font-family: var(--gf-font);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
        }

        #game-container {
            width: 100%;
            max-width: 480px;
            height: 100vh;
            max-height: 860px;
            background: #1e130a;
            position: relative;
            display: flex;
            flex-direction: column;
            box-shadow: 0 0 30px rgba(0,0,0,0.8);
            border: 4px solid var(--gf-gold);
            border-radius: 12px;
            overflow: hidden;
        }

        /* HEADER / STATS BAR */
        #status-bar {
            background: linear-gradient(180deg, #3d2612, #211409);
            color: var(--gf-parchment);
            padding: 8px 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 0.85rem;
            border-bottom: 2px solid var(--gf-gold);
            z-index: 10;
        }

        .stat-item {
            display: flex;
            align-items: center;
            gap: 4px;
        }

        /* VIEWPORT & SPRITES */
        #scene-viewport {
            flex: 1;
            position: relative;
            background-size: cover;
            background-position: center;
            transition: background 0.5s ease;
            display: flex;
            justify-content: center;
            align-items: flex-end;
            overflow: hidden;
        }

        .scene-bg-cabana { background: linear-gradient(to bottom, #111a2e, #3a2518); }
        .scene-bg-bosque { background: linear-gradient(to bottom, #091e12, #1b3824); }
        .scene-bg-bunker { background: linear-gradient(to bottom, #07151e, #132b38); }

        #character-sprite {
            max-width: 85%;
            max-height: 75%;
            object-fit: contain;
            transition: transform 0.3s ease, opacity 0.3s ease;
            filter: drop-shadow(0 10px 15px rgba(0,0,0,0.6));
        }

        /* DIALOGUE BOX */
        #dialogue-container {
            background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><rect width="100" height="100" fill="%23f4e8c1"/><path d="M0 0 L100 0 L100 5 L0 5 Z" fill="%23d4af37"/></svg>');
            background-size: cover;
            border-top: 3px solid var(--gf-gold);
            padding: 12px 16px;
            min-height: 170px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            position: relative;
            z-index: 5;
        }

        #speaker-name {
            font-weight: bold;
            font-size: 1.1rem;
            color: var(--gf-burgundy);
            text-transform: uppercase;
            letter-spacing: 1px;
            border-bottom: 2px dashed #b5a478;
            padding-bottom: 4px;
            margin-bottom: 6px;
        }

        #dialogue-text {
            font-size: 0.95rem;
            line-height: 1.4;
            color: #1c1208;
            flex: 1;
        }

        .btn-next {
            align-self: flex-end;
            background: var(--gf-burgundy);
            color: white;
            border: 1px solid var(--gf-gold);
            padding: 6px 16px;
            border-radius: 4px;
            font-family: inherit;
            cursor: pointer;
            margin-top: 8px;
            font-size: 0.85rem;
        }

        .btn-next:active {
            transform: scale(0.95);
        }

        /* CHOICES OVERLAY */
        #choices-overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(10, 6, 3, 0.85);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            gap: 10px;
            padding: 20px;
            z-index: 20;
        }

        .choice-btn {
            width: 100%;
            background: var(--gf-parchment);
            color: var(--gf-dark-wood);
            border: 2px solid var(--gf-gold);
            padding: 10px 14px;
            border-radius: 6px;
            font-family: inherit;
            font-size: 0.88rem;
            text-align: left;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
        }

        .choice-btn:hover, .choice-btn:active {
            background: #fff;
            border-color: var(--gf-cyan);
            transform: translateX(4px);
        }

        /* NAVIGATION / MODALS */
        .bottom-nav {
            display: flex;
            background: #180e06;
            border-top: 1px solid #3d2612;
            z-index: 10;
        }

        .nav-btn {
            flex: 1;
            background: none;
            border: none;
            color: var(--gf-parchment);
            padding: 10px 0;
            font-size: 0.75rem;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 2px;
        }

        .nav-btn:hover { background: rgba(255,255,255,0.05); }

        .modal-screen {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: #180e06;
            color: var(--gf-parchment);
            z-index: 30;
            display: flex;
            flex-direction: column;
            padding: 16px;
            overflow-y: auto;
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid var(--gf-gold);
            padding-bottom: 8px;
            margin-bottom: 12px;
        }

        .modal-title {
            font-size: 1.2rem;
            color: var(--gf-gold);
        }

        .close-btn {
            background: var(--gf-burgundy);
            color: white;
            border: none;
            padding: 4px 10px;
            border-radius: 4px;
            cursor: pointer;
        }

        /* MAP GRID */
        .map-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 12px;
            margin-top: 10px;
        }

        .map-location-card {
            background: #2b1d0c;
            border: 2px solid #5a3e1b;
            border-radius: 8px;
            padding: 12px;
            text-align: center;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .map-location-card:hover {
            border-color: var(--gf-gold);
            background: #3d2a13;
        }

        /* JOURNAL & AFFINITY */
        .affinity-card {
            background: #231509;
            border: 1px solid #422912;
            padding: 10px;
            margin-bottom: 8px;
            border-radius: 6px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .heart-display {
            color: #ff3366;
            letter-spacing: 2px;
        }

        /* START SCREEN */
        #start-screen {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: linear-gradient(180deg, #120902, #2b1406);
            z-index: 50;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 24px;
            text-align: center;
        }

        #start-screen h1 {
            color: var(--gf-gold);
            font-size: 1.8rem;
            margin-bottom: 4px;
            text-shadow: 0 0 10px rgba(212,175,55,0.4);
        }

        #start-screen h2 {
            color: var(--gf-cyan);
            font-size: 1.1rem;
            margin-bottom: 24px;
            font-weight: normal;
        }

        .start-btn {
            width: 80%;
            background: var(--gf-burgundy);
            color: var(--gf-parchment);
            border: 2px solid var(--gf-gold);
            padding: 12px;
            margin: 6px 0;
            border-radius: 6px;
            font-size: 1rem;
            font-family: inherit;
            cursor: pointer;
        }

        .hidden { display: none !important; }
    </style>
</head>
<body>

<div id="game-container">

    <!-- PANTALLA DE INICIO -->
    <div id="start-screen">
        <h1>GRAVITY FALLS</h1>
        <h2>La Ruta de los Seis Dedos</h2>
        <p style="color: #a38f67; font-size: 0.85rem; margin-bottom: 20px;">Un juego otome interactivo romántico y de misterio</p>
        <button class="start-btn" onclick="startGame()">Nueva Partida</button>
        <button class="start-btn" onclick="loadGame()">Cargar Partida</button>
    </div>

    <!-- BARRAS DE ESTADO -->
    <div id="status-bar">
        <div class="stat-item"><span id="time-display">☀️ Tarde</span></div>
        <div class="stat-item">Energía: <span id="energy-display">❤️❤️❤️❤️❤️️</span></div>
        <div class="stat-item">📍 <span id="location-display">Cabaña</span></div>
    </div>

    <!-- ESCENARIO Y SPRITES DE PERSONAJES -->
    <div id="scene-viewport" class="scene-bg-cabana">
        <svg id="character-sprite" viewBox="0 0 200 300" xmlns="http://www.w3.org/2000/svg">
            <!-- Render dinámico SVG de Stanford Pines (con opción de abrigo, laboratorio o traje) -->
            <g id="ford-trench-coat">
                <rect x="70" y="90" width="60" height="180" fill="#a89269" rx="5"/>
                <rect x="85" y="100" width="30" height="150" fill="#7a1c1c"/>
                <circle cx="100" cy="65" r="35" fill="#e5b893"/>
                <rect x="70" y="40" width="60" height="25" fill="#6e5a47" rx="4"/>
                <!-- Lentes rectangulares -->
                <rect x="80" y="55" width="18" height="14" fill="none" stroke="#3d2b1f" stroke-width="3"/>
                <rect x="102" y="55" width="18" height="14" fill="none" stroke="#3d2b1f" stroke-width="3"/>
                <line x1="98" y1="62" x2="102" y2="62" stroke="#3d2b1f" stroke-width="2"/>
                <!-- Nariz caracteristica -->
                <ellipse cx="100" cy="72" rx="7" ry="5" fill="#cb8e68"/>
                <path d="M 88 82 Q 100 88 112 82" stroke="#3d2b1f" stroke-width="2" fill="none" id="ford-mouth"/>
            </g>
        </svg>
    </div>

    <!-- DIÁLOGOS Y OPCIONES -->
    <div id="dialogue-container">
        <div id="speaker-name">FORD PINES</div>
        <div id="dialogue-text">Cargando historia...</div>
        <button class="btn-next" id="btn-next" onclick="advanceDialogue()">Continuar ▶</button>
    </div>

    <!-- MENU DE OPCIONES -->
    <div id="choices-overlay" class="hidden"></div>

    <!-- MODAL DE MAPA -->
    <div id="modal-map" class="modal-screen hidden">
        <div class="modal-header">
            <span class="modal-title">🗺️ Mapa de Gravity Falls</span>
            <button class="close-btn" onclick="toggleModal('modal-map')">✖</button>
        </div>
        <p style="font-size: 0.85rem; color: #a38f67;">Selecciona un lugar para moverte (Consume 1 ❤️ de Energía):</p>
        <div class="map-grid">
            <div class="map-location-card" onclick="travelTo('Cabaña del Misterio', 'cabana')">🏚️ Cabaña del Misterio</div>
            <div class="map-location-card" onclick="travelTo('Bosque de Gravity Falls', 'bosque')">🌲 Bosque</div>
            <div class="map-location-card" onclick="travelTo('Búnker Secreto', 'bunker')">🔬 Búnker / Lab</div>
            <div class="map-location-card" onclick="travelTo('Lago Gravity Falls', 'lago')">🌊 El Lago</div>
            <div class="map-location-card" onclick="travelTo('Restaurante', 'restaurante')">🍽️ Cafetería</div>
            <div class="map-location-card" onclick="travelTo('Observatorio', 'observatorio')">🌌 Observatorio</div>
        </div>
    </div>

    <!-- MODAL DE DIARIO / AFINIDAD -->
    <div id="modal-journal" class="modal-screen hidden">
        <div class="modal-header">
            <span class="modal-title">📖 Diario & Relaciones</span>
            <button class="close-btn" onclick="toggleModal('modal-journal')">✖</button>
        </div>
        <h3 style="color: var(--gf-gold); margin-bottom: 8px;">Afinidad con Personajes</h3>
        <div id="affinity-list"></div>
        <h3 style="color: var(--gf-gold); margin: 12px 0 8px 0;">Notas de Investigación</h3>
        <div id="journal-notes" style="font-size: 0.85rem; line-height: 1.4; color: #c0b08a;"></div>
    </div>

    <!-- MODAL DE INVENTARIO -->
    <div id="modal-inventory" class="modal-screen hidden">
        <div class="modal-header">
            <span class="modal-title">🎒 Inventario</span>
            <button class="close-btn" onclick="toggleModal('modal-inventory')">✖</button>
        </div>
        <div id="inventory-list"></div>
    </div>

    <!-- NAVEGACIÓN INFERIOR -->
    <div class="bottom-nav">
        <button class="nav-btn" onclick="toggleModal('modal-map')">🗺️<span>Mapa</span></button>
        <button class="nav-btn" onclick="toggleModal('modal-journal')">📖<span>Diario</span></button>
        <button class="nav-btn" onclick="toggleModal('modal-inventory')">🎒<span>Mochila</span></button>
        <button class="nav-btn" onclick="saveGame()">💾<span>Guardar</span></button>
    </div>

</div>

<script>
    // ESTADO GENERAL DEL JUEGO
    let gameState = {
        playerName: "Protagonista",
        timeOfDay: "Tarde",
        energy: 5,
        location: "Cabaña del Misterio",
        affinities: {
            ford: 15,
            stan: 25,
            dipper: 30,
            mabel: 50,
            soos: 40,
            wendy: 30
        },
        inventory: [
            { id: "cafe", name: "☕ Café Cargado", desc: "El combustible predilecto para científicos desvelados." },
            { id: "libro", name: "📚 Tomo Cuántico", desc: "Contiene ecuaciones avanzadas de anomalías espaciales." }
        ],
        journalNotes: [
            "Llegué a Gravity Falls para trabajar en la Cabaña del Misterio.",
            "Todos rumorean sobre el hermano gemelo de Stan, el científico de seis dedos..."
        ],
        currentScriptIndex: 0
    };

    // GUIÓN DEL EPISODIO 1: EL HOMBRE DE LOS SEIS DEDOS
    const scriptEpisode1 = [
        {
            speaker: "STANLEY PINES",
            text: "¡Tranquilos todos! Hoy es un día absolutamente normal en la Cabaña del Misterio. Ni un solo monstruo ni anomalía a la vista.",
            bg: "scene-bg-cabana",
            spriteExpression: "neutral"
        },
        {
            speaker: "DIPPER PINES",
            text: "¡Tío Stan! ¡Nunca digas eso! Cada vez que afirmas que nada malo pasará, el continuo espacio-tiempo se colapsa.",
            bg: "scene-bg-cabana",
            spriteExpression: "neutral"
        },
        {
            speaker: "MABEL PINES",
            text: "¡Oigan! ¿Alguien dijo colapso cósmico? ¡Es la oportunidad perfecta para estrenar mis nuevas pulseras fluorescentes con la nueva chica!",
            bg: "scene-bg-cabana",
            spriteExpression: "happy"
        },
        {
            speaker: "SOOS",
            text: "Cierto, Sr. Pines. La última vez que dijo eso, un grupo de gnomos intentó secuestrar la cortadora de césped.",
            bg: "scene-bg-cabana",
            spriteExpression: "neutral"
        },
        {
            speaker: "PROTAGONISTA",
            text: "(Sonríes mientras limpias el mostrador. Todo parecía una tarde tranquila... hasta que las luces comenzaron a parpadear bruscamente).",
            bg: "scene-bg-cabana",
            spriteExpression: "neutral"
        },
        {
            speaker: "SISTEMA",
            text: "⚡ ¡RUMBLE! Las estanterías tiemblan. La máquina expendedora de la cabaña se abre de golpe revelando un pasadizo secreto iluminado por una tenue luz azul.",
            bg: "scene-bg-cabana",
            spriteExpression: "surprised"
        },
        {
            speaker: "STANLEY PINES",
            text: "¡Rayos! Se supone que esa puerta debía permanecer cerrada...",
            bg: "scene-bg-cabana",
            spriteExpression: "surprised"
        },
        {
            speaker: "VOZ MISTERIOSA",
            text: "Stanley... te dije que ajustaras el estabilizador de fase del portal.",
            bg: "scene-bg-cabana",
            spriteExpression: "surprised"
        },
        {
            speaker: "DIPPER PINES",
            text: "(Susurrando con los ojos abiertos de par en par) Es... es él...",
            bg: "scene-bg-cabana",
            spriteExpression: "surprised"
        },
        {
            speaker: "FORD PINES",
            text: "(Emerge del humo del pasadizo acomodándose el abrigo de trinchera y ajustándose los lentes. Te observa fijamente con una mezcla de serenidad y cautela).",
            bg: "scene-bg-cabana",
            spriteExpression: "ford_intro"
        },
        {
            speaker: "FORD PINES",
            text: "Stanley... ¿se puede saber quién es esta joven y por qué está dentro de la zona de contención de mi investigación?",
            bg: "scene-bg-cabana",
            spriteExpression: "ford_intro",
            hasChoices: true,
            choices: [
                { text: "A) Sonreírle amablemente y presentarte.", nextStep: 11, fordPoints: 5, stanPoints: 0 },
                { text: "B) Mirar detenidamente su mano derecha de seis dedos.", nextStep: 12, fordPoints: 2, stanPoints: -2 },
                { text: "C) Preguntarle directamente quién es y qué hace en el sótano.", nextStep: 13, fordPoints: 4, stanPoints: 0 },
                { text: "D) 'Supongo que tú eres el famoso hermano gemelo de Stan.'", nextStep: 14, fordPoints: 3, stanPoints: 5 },
                { text: "E) '¿Siempre entras así a las habitaciones, con tanta dramaticidad?'", nextStep: 15, fordPoints: 6, stanPoints: 4 },
                { text: "F) Guardar silencio y observarlo en reserva.", nextStep: 16, fordPoints: 3, stanPoints: 0 }
            ]
        }
    ];

    // CONTINUACIONES SEGÚN ELECCIÓN
    const scriptBranches = {
        11: { speaker: "FORD PINES", text: "Vaya... una actitud cordial. Pocas personas reaccionan con tanta serenidad ante una fluctuación interdimensional. Interesante.", nextScript: 17 },
        12: { speaker: "FORD PINES", text: "(Nota tu mirada sobre sus manos y entrelaza los dedos con ligera incomodidad). Veo que notaste mi anomalía polidáctila. No te preocupes, no es infecciosa.", nextScript: 17 },
        13: { speaker: "FORD PINES", text: "Directa y curiosa. Mi nombre es Dr. Stanford Pines. Y mis asuntos en el sótano corresponden a la comprensión de este pueblo.", nextScript: 17 },
        14: { speaker: "FORD PINES", text: "(Mira a Stan de reojo). ¿Famoso? Dudo que Stanley haya usado términos halagadores para describirme, pero sí... soy su hermano.", nextScript: 17 },
        15: { speaker: "FORD PINES", text: "(Se dibuja una casi imperceptible sonrisa en sus labios). La ciencia no es modesta, jovencita. Las entradas triunfales son una consecuencia colateral.", nextScript: 17 },
        16: { speaker: "FORD PINES", text: "Cautelosa... Una cualidad prudente en un lugar repleto de peligros impredecibles como Gravity Falls.", nextScript: 17 },
        17: { speaker: "MABEL PINES", text: "(Susurrándote al oído con emoción desbordada) ¡¡LO SABÍA!! ¡El radar romántico de Mabel acaba de detectar ondas de interés intelectual!", nextScript: 18 },
        18: { speaker: "FORD PINES", text: "En fin. Necesito preparar el laboratorio subterráneo. Si posees la sutileza para no tocar nada destructivo, tal vez puedas asistirme más tarde.", nextScript: -1 }
    };

    // CONTROLADORES DEL JUEGO
    function startGame() {
        document.getElementById('start-screen').classList.add('hidden');
        renderScriptStep();
    }

    function renderScriptStep() {
        let current = scriptEpisode1[gameState.currentScriptIndex];
        if (!current) return;

        document.getElementById('speaker-name').innerText = current.speaker;
        document.getElementById('dialogue-text').innerText = current.text;
        
        // Cambiar fondo si aplica
        if (current.bg) {
            document.getElementById('scene-viewport').className = current.bg;
        }

        // Mostrar opciones de elección si existen
        if (current.hasChoices) {
            document.getElementById('btn-next').classList.add('hidden');
            let choicesOverlay = document.getElementById('choices-overlay');
            choicesOverlay.innerHTML = '';
            choicesOverlay.classList.remove('hidden');

            current.choices.forEach(ch => {
                let btn = document.createElement('button');
                btn.className = 'choice-btn';
                btn.innerText = ch.text;
                btn.onclick = () => selectChoice(ch);
                choicesOverlay.appendChild(btn);
            });
        } else {
            document.getElementById('btn-next').classList.remove('hidden');
        }
    }

    function advanceDialogue() {
        gameState.currentScriptIndex++;
        if (gameState.currentScriptIndex < scriptEpisode1.length) {
            renderScriptStep();
        } else {
            // Modode exploración libre o fin de episodio
            document.getElementById('dialogue-text').innerText = "¡Has completado el encuentro inicial con Ford! Explora el mapa para interactuar con más lugares y aumentar tu afinidad.";
            document.getElementById('btn-next').classList.add('hidden');
        }
    }

    function selectChoice(choice) {
        document.getElementById('choices-overlay').classList.add('hidden');
        
        // Aplicar puntos de afinidad
        gameState.affinities.ford += choice.fordPoints || 0;
        gameState.affinities.stan += choice.stanPoints || 0;

        // Cargar respuesta de rama
        let branch = scriptBranches[choice.nextStep];
        if (branch) {
            document.getElementById('speaker-name').innerText = branch.speaker;
            document.getElementById('dialogue-text').innerText = branch.text;
            document.getElementById('btn-next').classList.remove('hidden');
            document.getElementById('btn-next').onclick = () => {
                if (branch.nextScript && scriptBranches[branch.nextScript]) {
                    let nextBranch = scriptBranches[branch.nextScript];
                    document.getElementById('speaker-name').innerText = nextBranch.speaker;
                    document.getElementById('dialogue-text').innerText = nextBranch.text;
                } else {
                    advanceDialogue();
                }
            };
        }
    }

    // MAPA Y NAVEGACIÓN
    function travelTo(locName, bgClass) {
        if (gameState.energy <= 0) {
            alert("¡Estás demasiado agotada! Ve a la Cabaña a descansar.");
            return;
        }
        gameState.energy--;
        gameState.location = locName;
        updateUI();
        toggleModal('modal-map');

        // Cambiar fondo del escenario
        document.getElementById('scene-viewport').className = 'scene-bg-' + bgClass;
        document.getElementById('speaker-name').innerText = locName.toUpperCase();
        document.getElementById('dialogue-text').innerText = `Has llegado a ${locName}. ¿Qué deseas investigar aquí?`;
    }

    function updateUI() {
        document.getElementById('location-display').innerText = gameState.location;
        document.getElementById('energy-display').innerText = "❤️".repeat(gameState.energy) + "🖤".repeat(5 - gameState.energy);
        renderAffinity();
        renderInventory();
    }

    function getFordHearts(score) {
        if (score <= 20) return { hearts: "❤️️🤍🤍🤍🤍", status: "Desconocidos / Curiosidad" };
        if (score <= 40) return { hearts: "❤️❤️🤍🤍🤍", status: "Interés Intelectual" };
        if (score <= 60) return { hearts: "❤️❤️❤️🤍🤍", status: "Confianza Mutua" };
        if (score <= 80) return { hearts: "❤️❤️❤️❤️🤍", status: "Sentimientos Profundos" };
        return { hearts: "❤️❤️❤️❤️❤️", status: "Amor Indisoluble" };
    }

    function renderAffinity() {
        let fordData = getFordHearts(gameState.affinities.ford);
        let list = document.getElementById('affinity-list');
        list.innerHTML = `
            <div class="affinity-card">
                <div>
                    <strong>Stanford Pines (Ford)</strong><br>
                    <small style="color:#a38f67">${fordData.status}</small>
                </div>
                <div class="heart-display">${fordData.hearts}</div>
            </div>
            <div class="affinity-card">
                <div><strong>Stanley Pines</strong></div>
                <div class="heart-display">⭐ ${gameState.affinities.stan}/100</div>
            </div>
            <div class="affinity-card">
                <div><strong>Mabel Pines</strong></div>
                <div class="heart-display">🌈 ${gameState.affinities.mabel}/100</div>
            </div>
            <div class="affinity-card">
                <div><strong>Dipper Pines</strong></div>
                <div class="heart-display">🔍 ${gameState.affinities.dipper}/100</div>
            </div>
        `;
    }

    function renderInventory() {
        let invList = document.getElementById('inventory-list');
        invList.innerHTML = '';
        gameState.inventory.forEach(item => {
            let div = document.createElement('div');
            div.className = 'affinity-card';
            div.innerHTML = `
                <div>
                    <strong>${item.name}</strong><br>
                    <small style="color:#a38f67">${item.desc}</small>
                </div>
                <button class="btn-next" onclick="giveGift('${item.id}')">Regalar a Ford</button>
            `;
            invList.appendChild(div);
        });
    }

    function giveGift(itemId) {
        if (itemId === 'cafe') {
            gameState.affinities.ford += 5;
            alert("Acepta el café gustosamente. 'Justo lo que necesitaba para terminar las ecuaciones del portal.' (+5 ❤️ Ford)");
        } else if (itemId === 'libro') {
            gameState.affinities.ford += 10;
            alert("Sus ojos se iluminan. '¡Un análisis fascinante! No sabía que te interesaba la física teórica.' (+10 ❤️ Ford)");
        }
        gameState.inventory = gameState.inventory.filter(i => i.id !== itemId);
        updateUI();
    }

    function toggleModal(id) {
        document.getElementById(id).classList.toggle('hidden');
        updateUI();
    }

    function saveGame() {
        localStorage.setItem('gf_otome_save', JSON.stringify(gameState));
        alert("¡Partida guardada exitosamente en el Diario!");
    }

    function loadGame() {
        let saved = localStorage.getItem('gf_otome_save');
        if (saved) {
            gameState = JSON.parse(saved);
            document.getElementById('start-screen').classList.add('hidden');
            updateUI();
            alert("Partida cargada correctamente.");
        } else {
            alert("No hay partidas guardadas encontradas.");
        }
    }
</script>
</body>
</html>
