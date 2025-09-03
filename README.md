<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Batman — Cluedo à Arkham (V1.3)</title>
<style>
:root{--bg:#0b0f12;--panel:#141922;--muted:#a8b0b6;--text:#eaf0f2;--accent:#00d1b2;--warn:#ffca3a;--danger:#ff595e;}
*{box-sizing:border-box}
body{margin:0;font-family:Inter,system-ui,Arial;background:linear-gradient(180deg,#070a0d,#0b0f12);color:var(--text);display:flex;justify-content:center;padding:12px}
.wrap{width:100%;max-width:480px;background:var(--panel);border:1px solid rgba(255,255,255,.05);border-radius:14px;box-shadow:0 10px 30px rgba(0,0,0,.6);overflow:hidden}
header{padding:10px 12px;text-align:center;border-bottom:1px solid rgba(255,255,255,.05)}
h1{margin:.2rem 0;font-size:18px;color:var(--accent)}
.small{color:var(--muted);font-size:12px}
.panel{padding:12px}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:8px}
button{background:#0e141a;color:var(--text);border:1px solid rgba(255,255,255,.06);padding:10px 12px;border-radius:10px;font-weight:700;cursor:pointer}
button:hover{border-color:var(--accent);color:var(--accent)}
.tabbar{display:flex;gap:6px;flex-wrap:wrap;margin-top:6px}
.tabbar button{flex:1}
.box{background:rgba(255,255,255,.03);border:1px solid rgba(255,255,255,.05);border-radius:10px;padding:10px}
#log{height:140px;overflow:auto;font-size:13px}
.badge{display:inline-block;padding:2px 8px;border-radius:999px;border:1px solid rgba(255,255,255,.08);font-size:11px;color:var(--muted)}
.kv{display:flex;gap:8px;flex-wrap:wrap}
.kv div{background:#0d1319;border:1px solid rgba(255,255,255,.06);border-radius:8px;padding:6px 8px;font-size:12px}
hr{border:0;border-top:1px solid rgba(255,255,255,.06);margin:8px 0}
.hidden{display:none}
.clue{margin:4px 0;padding:6px 8px;background:#0e141a;border:1px solid rgba(255,255,255,.06);border-radius:8px;font-size:13px}
.suspect{display:flex;align-items:center;gap:8px;margin:6px 0;padding:8px;border:1px solid rgba(255,255,255,.06);border-radius:10px;background:#0f151b}
.suspect .em{font-size:24px;width:32px;text-align:center}
.float{position:fixed;left:50%;transform:translateX(-50%);bottom:24px;background:#0f151b;border:1px solid rgba(255,255,255,.08);padding:8px 12px;border-radius:999px;font-size:12px;opacity:.95}
.warn{color:var(--warn)}
.danger{color:var(--danger)}
</style>
</head>
<body>
<div class="wrap">
  <header>
    <h1>🦇 Batman — Enquête à Arkham (V1.3)</h1>
    <div class="small">Croise indices, caméras et alibis — attention aux pièges.</div>
  </header>

  <!-- START -->
  <section id="start" class="panel">
    <div class="small" style="margin-bottom:8px">
      <b>Prélude :</b> Un surveillant d’Arkham a été retrouvé <b>mort sur le sol d’un couloir</b> vers 22h00.  
      Aucun témoin direct. Trois suspects principaux : le Joker, le Pingouin et Double-Face.  
      Batman doit élucider l’affaire — mais certains indices peuvent être piégés.
    </div>
    <hr>
    <div class="small">Choisis la difficulté :</div>
    <div class="grid" style="margin-top:8px">
      <button onclick="startGame('facile')">😃 Facile (sans pièges)</button>
      <button onclick="startGame('normal')">😐 Normal (pièges 25%)</button>
      <button onclick="startGame('difficile')">😈 Difficile (pièges 40%)</button>
    </div>
  </section>

  <!-- GAME -->
  <section id="game" class="panel hidden">
    <div class="kv"><div id="diffBadge" class="badge">Difficulté</div><div id="caseBadge" class="badge">Affaire #A-22</div></div>

    <div class="tabbar">
      <button onclick="showTab('lieux')">📍 Lieux</button>
      <button onclick="showTab('interro')">🗣️ Interrogatoires</button>
      <button onclick="showTab('dossier')">📂 Dossier</button>
      <button onclick="showTab('accuse')">⚖️ Accuser</button>
    </div>

    <!-- Lieux -->
    <div id="tab-lieux" class="box" style="margin-top:8px">
      <div class="small">Choisis un lieu à fouiller (attention aux pièges) :</div>
      <div class="grid" style="margin-top:8px" id="lieuxBtns"></div>
      <hr>
      <div id="lieuResult" class="small"></div>
    </div>

    <!-- Interrogatoires -->
    <div id="tab-interro" class="box hidden" style="margin-top:8px">
      <div class="small">Interroger un suspect :</div>
      <div id="suspectList"></div>
      <hr>
      <div id="interroResult" class="small"></div>
    </div>

    <!-- Dossier -->
    <div id="tab-dossier" class="box hidden" style="margin-top:8px">
      <div class="small">Journal & indices :</div>
      <div id="log" class="small"></div>
      <hr>
      <div id="clues"></div>
    </div>

    <!-- Accuse -->
    <div id="tab-accuse" class="box hidden" style="margin-top:8px">
      <div class="small">Qui accuser ?</div>
      <div class="grid" style="margin-top:8px">
        <button onclick="accuse('joker')">🤡 Joker</button>
        <button onclick="accuse('penguin')">🐧 Pingouin</button>
        <button onclick="accuse('twoface')">🎭 Double-Face</button>
      </div>
      <div id="accuseHint" class="small" style="margin-top:8px;color:var(--muted)">Croise les indices avant de trancher. Les pièges peuvent détruire des preuves.</div>
    </div>

    <div id="toast" class="float hidden">Indice ajouté au dossier</div>
  </section>

  <!-- END -->
  <section id="end" class="panel hidden" style="text-align:center">
    <h3 id="endTitle"></h3>
    <div id="endText" class="small" style="margin-top:6px"></div>
    <div style="margin-top:10px"><button onclick="location.reload()">🔄 Rejouer</button></div>
  </section>
</div>

<script>
/* ========== Données ========== */
const SUSPECTS = {
  joker: { id:'joker', name:'Joker', emoji:'🤡', style:"rires, gaz et cartes" },
  penguin: { id:'penguin', name:'Pingouin', emoji:'🐧', style:"gadgets d’ombrelle, plumes" },
  twoface: { id:'twoface', name:'Double-Face', emoji:'🎭', style:"obsession pour la pièce, armes à feu" }
};
const PLACES = [
  { id:'cams', name:'Salle des caméras 📹' },
  { id:'cellJ', name:'Cellule du Joker 🤡' },
  { id:'officeP', name:'Bureau du Pingouin 🐧' },
  { id:'lab', name:'Atelier technique 🧪' }
];

let difficulty='normal', culpritKey=null;
let clues=[], visited=new Set(), interrogated=new Set(), blockedPlaces=new Set();

/* ========== Utilitaires UI ========== */
const $ = id => document.getElementById(id);
function showTab(key){['lieux','interro','dossier','accuse'].forEach(k=>$('tab-'+k).classList.toggle('hidden', k!==key));}
function log(msg, cls=''){ const d=$('log'); const el=document.createElement('div'); el.className='clue'; if(cls) el.classList.add(cls); el.innerHTML=msg; d.appendChild(el); d.scrollTop=d.scrollHeight; }
function updateClues(){ const c=$('clues'); c.innerHTML=''; if(!clues.length){ c.innerHTML='<div class="small">Aucun indice.</div>'; return; } clues.forEach(cl=>{ const d=document.createElement('div'); d.className='clue'; d.textContent=cl.text; c.appendChild(d); }); }
function toast(txt){ const t=$('toast'); t.textContent=txt; t.classList.remove('hidden'); setTimeout(()=>t.classList.add('hidden'),900); }

/* ========== Piège : probabilités & effets ========== */
function trapChance(){
  if(difficulty==='facile') return 0;
  if(difficulty==='normal') return 0.25;
  return 0.40;
}
// applique l'effet du piège ; retourne true si piège déclenché
function maybeTriggerTrap(placeId){
  const chance = trapChance();
  if(chance<=0) return false;
  if(Math.random() > chance) return false;

  // piège déclenché — son effet dépend du coupable
  const culprit = culpritKey;
  if(culprit==='joker'){
    // Joker : gaz — en normal supprime l'indice du lieu; en difficile supprime toutes les preuves liées au coupable
    if(difficulty==='normal'){
      log('💨 Piège déclenché ! Un nuage de gaz se propage — l’indice trouvé ici est effacé.', 'warn');
      // signaler au caller pour annulation (caller gérera suppression du seul indice prévu)
      return {type:'joker',severity:1};
    } else {
      log('💨 Piège Joker (fort) ! Nuage dense : toutes les traces liées au Joker risquent d\'être détruites.', 'danger');
      return {type:'joker',severity:2};
    }
  }
  if(culprit==='penguin'){
    // Penguin : gadget — empêche revisite, en difficile détruit aussi l'indice
    if(difficulty==='normal'){
      log('💥 Piège déclenché ! Un gadget explose — ce lieu est désormais impraticable.', 'warn');
      return {type:'penguin',severity:1};
    } else {
      log('💥 Piège Pingouin (fort) ! Explosion et désordre — lieu bloqué et indice détruit.', 'danger');
      return {type:'penguin',severity:2};
    }
  }
  if(culprit==='twoface'){
    // TwoFace : tir piégé — en normal enlève un indice au hasard, en difficile enlève deux indices
    if(difficulty==='normal'){
      log('🔫 Piège déclenché ! Un tir part — une preuve précédemment collectée est détruite.', 'warn');
      return {type:'twoface',severity:1};
    } else {
      log('🔫 Piège Double-Face (fort) ! Double impact — deux preuves sont détruites aléatoirement.', 'danger');
      return {type:'twoface',severity:2};
    }
  }
  return false;
}

/* suppression d'indices */
function removeCluesByTag(tag){
  const before = clues.length;
  clues = clues.filter(c => c.tag !== tag);
  if(clues.length < before) updateClues();
}
function removeRandomClues(n){
  for(let i=0;i<n;i++){
    if(clues.length===0) break;
    const idx = Math.floor(Math.random()*clues.length);
    const removed = clues.splice(idx,1);
  }
  updateClues();
}

/* ========== Initialisation / démarrage ========== */
function startGame(diff){
  difficulty = diff;
  culpritKey = ['joker','penguin','twoface'][Math.floor(Math.random()*3)];
  clues = []; visited = new Set(); interrogated = new Set(); blockedPlaces = new Set();

  $('start').classList.add('hidden');
  $('game').classList.remove('hidden');
  $('diffBadge').textContent = 'Difficulté : '+diff.toUpperCase();
  $('caseBadge').textContent = 'Affaire #A-22 — 22h00';
  $('lieuxBtns').innerHTML = '';
  PLACES.forEach(p => {
    const btn = document.createElement('button');
    btn.textContent = p.name;
    btn.onclick = () => visit(p.id);
    $('lieuxBtns').appendChild(btn);
  });

  const sl = $('suspectList'); sl.innerHTML = '';
  Object.values(SUSPECTS).forEach(s => {
    const row = document.createElement('div'); row.className = 'suspect';
    row.innerHTML = `<div class="em">${s.emoji}</div><div><b>${s.name}</b><div class="small">${s.style}</div></div><div style="margin-left:auto"><button onclick="interrogate('${s.id}')">Interroger</button></div>`;
    sl.appendChild(row);
  });

  $('log').innerHTML = '';
  log('🚨 Corps du surveillant découvert à 22h00. Aucun témoin direct.');
  log('🎯 Objectif : rassembler assez de preuves pour accuser le coupable. Attention aux pièges.');
  updateClues();
  showTab('lieux');
}

/* ========== Visiter lieux (avec pièges) ========== */
function visit(pid){
  if(blockedPlaces.has(pid)){ $('lieuResult').textContent = 'Ce lieu est trop endommagé pour être fouillé à nouveau.'; return; }
  if(visited.has(pid)){
    $('lieuResult').textContent = 'Rien de nouveau ici — tu as déjà fouillé ce lieu.';
    return;
  }
  visited.add(pid);

  // définir indices plausibles (vrais/faux selon qui est coupable)
  const C = culpritKey;
  let plannedClues = []; // {text,tag}
  if(pid==='cams'){
    // caméras -> plus ambiguë maintenant
    if(difficulty==='facile'){
      plannedClues.push({text:`Caméras (22h03) : silhouette et un détail visible (objet roulant).`, tag:'camera'});
    } else if(difficulty==='normal'){
      plannedClues.push({text:`Caméras (22h03) : silhouette floue ; audio capte un bruit métallique, pas clair.`, tag:'camera'});
      // possible bruit
      plannedClues.push({text:`Image parasitée : trace de plume (peu fiable).`, tag:'misc'});
    } else { // difficile
      plannedClues.push({text:`Caméras (22h03) : silhouette sombre, audio bruité — éclat bref de lumière détecté.`, tag:'camera'});
      plannedClues.push({text:`Artefact vidéo : ombre circulaire aperçue, très incertain.`, tag:'misc'});
      plannedClues.push({text:`Audio : souffle / toux capté brièvement (très faible S/N).`, tag:'misc'});
    }
  }
  else if(pid==='cellJ'){
    if(C==='joker'){
      plannedClues.push({text:'Carte Joker froissée près de la grille.', tag:'joker'});
      plannedClues.push({text:'Légère odeur de gaz détectée.', tag:'joker'});
    } else {
      plannedClues.push({text:'Cartes anciennes, probablement pas récentes.', tag:'misc'});
      if(difficulty!=='facile') plannedClues.push({text:'Une plume trouvée sous le lit (potentiellement piste trompeuse).', tag:'misc'});
    }
  }
  else if(pid==='officeP'){
    if(C==='penguin'){
      plannedClues.push({text:'Plume blanche coincée dans la serrure.', tag:'penguin'});
      plannedClues.push({text:'Parapluie humide laissé au sol.', tag:'penguin'});
    } else {
      plannedClues.push({text:'Parapluies rangés, rien d’incriminant.', tag:'misc'});
      if(difficulty!=='facile') plannedClues.push({text:'Tache d’eau peu claire qui ressemble à éclaboussure ', tag:'misc'});
    }
  }
  else if(pid==='lab'){
    if(C==='twoface'){
      plannedClues.push({text:'Résidu de poudre sur un établi.', tag:'twoface'});
      plannedClues.push({text:'Pièce écornée retrouvée au sol.', tag:'twoface'});
    } else {
      plannedClues.push({text:'Matériel sous scellés, rien d’actif.', tag:'misc'});
      if(difficulty!=='facile') plannedClues.push({text:'Vieille douille, dossier ancien.', tag:'misc'});
    }
  }

  // Avant d'ajouter, vérifier piège
  const trap = maybeTriggerTrap(pid);
  // Cas selon trap
  if(trap){
    // si piège du type penguin severity 1 : bloque lieu (empêche revisite), mais en normal laisse l'indice détruit? spec: normal blocks revisite; difficult blocks + destroys
    if(trap.type==='penguin'){
      blockedPlaces.add(pid);
      if(trap.severity===1){
        // blocked, still allow to add plannedClues but mark one removed? spec: normal only blocks revisits. We'll still add first planned clue (if any) unless later said different.
        // To make it meaningful: in normal, explosion destroys the *main* clue (first) and block revisit.
        if(plannedClues.length>0){
          // remove main clue (simulate destroyed)
          const destroyed = plannedClues.shift();
          log('💥 Explosion ! Un gadget détruit la preuve principale ici : "'+destroyed.text+'".', 'warn');
          // add remaining clues as possible (misleading ones)
          plannedClues.forEach(pc=>{ addClue(pc.text, pc.tag); log('🔎 '+pc.text);});
          $('lieuResult').innerHTML = '💥 Explosion — le lieu est endommagé. Certaines preuves ont été détruites.';
          return;
        } else {
          $('lieuResult').innerHTML = '💥 Explosion — le lieu est endommagé, rien de récupérable.';
          return;
        }
      } else {
        // severity 2: block + destroy any planned clues
        log('💥 Explosion importante ! Indices détruits et lieu impraticable.', 'danger');
        $('lieuResult').innerHTML = '💥 Explosion violente — le lieu est irrécupérable ; indices détruits.';
        return;
      }
    }

    // Joker traps
    if(trap.type==='joker'){
      if(trap.severity===1){
        // normal: gas erases the clue found here (we'll simulate: plannedClues present but primary erased)
        if(plannedClues.length>0){
          const erased = plannedClues.shift();
          log('💨 Nuage de gaz ! L’indice principal "'+erased.text+'" est effacé.', 'warn');
          // remaining plannedClues might still be added (noisy ones)
          plannedClues.forEach(pc=>{ addClue(pc.text, pc.tag); log('🔎 '+pc.text); });
          $('lieuResult').innerHTML = '💨 Nuage de gaz — l’indice principal a été effacé.';
          return;
        } else {
          $('lieuResult').innerHTML = '💨 Nuage de gaz — pas d’indice récupérable.';
          return;
        }
      } else {
        // severe: remove all clues related to culprit from previously collected set
        log('💨 Nuage toxique (fort) ! Certaines preuves liées au Joker sont détruites dans le dossier.', 'danger');
        removeCluesByTag('joker');
        $('lieuResult').innerHTML = '💨 Nuage toxique — certaines traces Joker ont été effacées du dossier.';
        return;
      }
    }

    // TwoFace traps
    if(trap.type==='twoface'){
      if(trap.severity===1){
        // remove one random existing clue
        if(clues.length>0){
          removeRandomClues(1);
          $('lieuResult').innerHTML = '🔫 Un tir part ! Une preuve en dossier a été détruite.';
          log('🔫 Un tir a détruit une preuve antérieure.', 'warn');
        } else {
          $('lieuResult').innerHTML = '🔫 Un tir part mais aucune preuve présente à détruire.';
        }
        return;
      } else {
        // severity 2: remove two clues if possible
        if(clues.length>0){
          removeRandomClues(2);
          $('lieuResult').innerHTML = '🔫 Double impact ! Deux preuves ont été détruites.';
          log('🔫 Double impact : preuves détruites.', 'danger');
        } else {
          $('lieuResult').innerHTML = '🔫 Tir violent mais dossier vide.';
        }
        return;
      }
    }
  }

  // Si pas de piège ou piège non déclenché, ajouter les plannedClues selon logique de difficulté
  // En facile, on donne le meilleur indice (premier) ; en normal on donne 1 vrai + 1 bruit (s'il existe) ; en difficile on peut donner seulement 1 indice ambiguë + bruit.
  if(difficulty==='facile'){
    if(plannedClues.length>0){
      addClue(plannedClues[0].text, plannedClues[0].tag);
      log('🔎 '+plannedClues[0].text);
      $('lieuResult').innerHTML = '🔎 '+plannedClues[0].text;
    } else {
      $('lieuResult').innerHTML = '🔎 Rien de notable.';
    }
  } else if(difficulty==='normal'){
    // give up to two clues: try give a truthful one first if exists
    if(plannedClues.length>0){
      addClue(plannedClues[0].text, plannedClues[0].tag);
      log('🔎 '+plannedClues[0].text);
      // if there's a second, it may be a noise: add it but tagged 'noise' or misc
      if(plannedClues.length>1){
        addClue(plannedClues[1].text, plannedClues[1].tag);
        log('⚠️ Détail incertain : '+plannedClues[1].text);
        $('lieuResult').innerHTML = '🔎 '+plannedClues[0].text + '<br><span class="small warn">⚠️ Détail incertain : '+plannedClues[1].text+'</span>';
      } else {
        $('lieuResult').innerHTML = '🔎 '+plannedClues[0].text;
      }
    } else {
      $('lieuResult').innerHTML = '🔎 Aucun indice notable.';
    }
  } else { // difficile
    // give 1 main ambiguous clue (camera-like) and possibly noises
    if(plannedClues.length>0){
      addClue(plannedClues[0].text, plannedClues[0].tag);
      log('🔎 (ambiguous) '+plannedClues[0].text);
      // additional noises
      for(let i=1;i<plannedClues.length;i++){
        // add noises with less prominence
        addClue(plannedClues[i].text, plannedClues[i].tag);
        log('⚠️ bruit possible : '+plannedClues[i].text);
      }
      $('lieuResult').innerHTML = '🔎 '+plannedClues[0].text + (plannedClues.length>1? ('<br><span class="small warn">⚠️ Bruits détectés</span>') : '');
    } else {
      $('lieuResult').innerHTML = '🔎 Rien de récupérable.';
    }
  }
}

/* ========== Interrogatoires ========== */
function interrogate(id){
  if(interrogated.has(id)){ $('interroResult').textContent = 'Ils n’ont rien à ajouter pour l’instant.'; return; }
  interrogated.add(id);
  const guilty = (id === culpritKey);
  let speech = '';
  if(id==='joker'){
    speech = guilty
      ? (difficulty==='facile' ? '🤡 « Ahahaha… je passais par là, c’était un petit tour de fumée, rien de sérieux. »' : '🤡 « 22h ? J’étais… occupé. Les détails sont flous, non ? »')
      : (difficulty==='difficile' ? '🤡 « En cellule. Mes cartes restent immuables. »' : '🤡 « En cellule à 22h, demande au gardien. »');
  }
  if(id==='penguin'){
    speech = guilty
      ? (difficulty==='facile' ? '🐧 « Parapluie trempé, couloir glissant — j’ai juste aidé à ramasser. »' : '🐧 « Le sol était glissant, j’ai manipulé un gadget — rien d’autre. »')
      : (difficulty==='difficile' ? '🐧 « J’étais au bureau, regardez les enregistrements. »' : '🐧 « Bureau, à mon comptoir. »');
  }
  if(id==='twoface'){
    speech = guilty
      ? (difficulty==='facile' ? '🎭 « Ma pièce a choisi… peut-être j’ai été vu, peut-être pas. »' : '🎭 « Le hasard a parlé. »')
      : (difficulty==='difficile' ? '🎭 « En cellule. Ma pièce n’a pas bougé. »' : '🎭 « Dormais. Rien à déclarer. »');
  }

  $('interroResult').innerHTML = speech;
  log('🗣️ Interrogatoire — '+SUSPECTS[id].name+' : « '+stripTags(speech)+' »');
}

/* ========== Accusation & fin ========== */
function accuse(id){
  const correct = (id === culpritKey);
  const name = SUSPECTS[id].name, culName = SUSPECTS[culpritKey].name;
  $('game').classList.add('hidden'); $('end').classList.remove('hidden');

  // Build summary
  const summary = buildSummary();
  if(correct){
    $('endTitle').textContent = '🏆 Coupable identifié : '+name+' !';
    $('endText').innerHTML = 'Bravo. Tu as reconstitué l’affaire :<br>' + summary + '<br><br>La justice d’Arkham peut suivre son cours.';
  } else {
    $('endTitle').textContent = '❌ Mauvaise accusation';
    $('endText').innerHTML = 'Le véritable coupable était <b>'+culName+'</b>.<br>' + summary + '<br><br>Tu peux relancer l’enquête pour t’améliorer.';
  }
}

function buildSummary(){
  const camera = clues.find(c=>c.tag==='camera');
  const j = clues.some(c=>c.tag==='joker');
  const p = clues.some(c=>c.tag==='penguin');
  const t = clues.some(c=>c.tag==='twoface');
  let lines = [];
  if(camera) lines.push('• Caméras : '+camera.text);
  if(j) lines.push('• Traces Joker : carte/gaz.');
  if(p) lines.push('• Traces Pingouin : plume/ombrelle.');
  if(t) lines.push('• Traces Double-Face : pièce/poudre.');
  if(!lines.length) lines.push('• Peu d’indices collectés.');
  return lines.join('<br>');
}

/* ========== Helpers ========== */
function stripTags(s){ return s.replace(/<[^>]*>/g,''); }

/* ========== Fin ========== */
</script>
</body>
</html>
