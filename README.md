<style>
*{box-sizing:border-box;margin:0;padding:0}
#app{display:flex;height:640px;background:#080810;border-radius:12px;overflow:hidden;border:1px solid #1e1e2e;color:#e0e0f0;font-family:system-ui,sans-serif;position:relative}
#canvas-wrap{flex:1;position:relative;overflow:hidden;cursor:grab;user-select:none}
#canvas-wrap.panning{cursor:grabbing}
#canvas-wrap.dragging-el{cursor:default}
#infinite-bg{position:absolute;inset:0;pointer-events:none}
#world{position:absolute;top:0;left:0;transform-origin:0 0;will-change:transform}
#topbar{position:absolute;top:0;left:0;right:230px;display:flex;align-items:center;justify-content:space-between;padding:8px 12px;z-index:60;pointer-events:none}
#topbar>*{pointer-events:auto}
#stats{display:flex;gap:6px}
.stat-pill{background:#10101ccc;border:1px solid #252540;border-radius:20px;padding:3px 10px;font-size:11px;color:#8080b0;backdrop-filter:blur(4px)}
.stat-pill span{color:#a0c0ff;font-weight:500}
#map-btns{display:flex;gap:5px}
.map-btn{background:#10101ccc;border:1px solid #252540;border-radius:8px;padding:4px 9px;font-size:11px;color:#8080b0;cursor:pointer;backdrop-filter:blur(4px);transition:background .15s,border-color .15s}
.map-btn:hover{background:#1e1e30cc;border-color:#40406080}
#zoom-indicator{position:absolute;bottom:12px;left:12px;font-size:10px;color:#303060;z-index:60;pointer-events:none}
#sidebar{width:230px;background:#0a0a14;border-left:1px solid #1a1a28;display:flex;flex-direction:column;z-index:70}
#sidebar-header{padding:12px 12px 8px;border-bottom:1px solid #1a1a28;display:flex;align-items:center;justify-content:space-between}
#sidebar-header h2{font-size:12px;font-weight:500;color:#505090;letter-spacing:.06em;text-transform:uppercase}
#sidebar-count{font-size:11px;color:#a0c0ff;font-weight:500}
#tabs{display:flex;border-bottom:1px solid #1a1a28}
.tab{flex:1;padding:7px 0;text-align:center;font-size:11px;color:#40406080;cursor:pointer;border-bottom:2px solid transparent;transition:color .15s,border-color .15s}
.tab.active{color:#a0c0ff;border-bottom-color:#a0c0ff}
#search-wrap{padding:7px 10px;border-bottom:1px solid #1a1a28}
#search{width:100%;background:#080810;border:1px solid #1e1e2e;border-radius:8px;padding:5px 10px;font-size:12px;color:#c0c0e0;outline:none}
#search:focus{border-color:#3030808}
#inventory{flex:1;overflow-y:auto;padding:6px}
#inventory::-webkit-scrollbar{width:3px}
#inventory::-webkit-scrollbar-thumb{background:#202038;border-radius:2px}
.inv-item{display:flex;align-items:center;gap:7px;padding:5px 8px;border-radius:8px;cursor:grab;font-size:12px;color:#c0c0e0;border:1px solid transparent;transition:background .12s,border-color .12s;user-select:none;margin-bottom:2px}
.inv-item:hover{background:#14142088;border-color:#282848}
.badge-new{font-size:9px;background:#1a2535;color:#6090c0;border-radius:4px;padding:1px 5px;margin-left:auto;flex-shrink:0}
.badge-rare{font-size:9px;background:#2a1a35;color:#a060d0;border-radius:4px;padding:1px 5px;margin-left:auto;flex-shrink:0}
#hints-panel{padding:8px 10px;border-top:1px solid #1a1a28;max-height:88px;overflow-y:auto}
#hints-panel::-webkit-scrollbar{width:3px}
#hints-panel::-webkit-scrollbar-thumb{background:#202038;border-radius:2px}
.hint-row{font-size:11px;color:#35355580;padding:2px 0;display:flex;align-items:center;gap:3px;white-space:nowrap;overflow:hidden}
.h-known{color:#7070a0}
.h-plus{color:#303055}
#hint-label{font-size:10px;color:#303055;text-transform:uppercase;letter-spacing:.06em;padding-bottom:3px}
#bottom-bar{display:flex;gap:5px;padding:7px 10px;border-top:1px solid #1a1a28}
.bot-btn{flex:1;background:transparent;border:1px solid #1e1e2e;border-radius:8px;padding:5px;font-size:11px;color:#505080;cursor:pointer;transition:background .15s}
.bot-btn:hover{background:#14142088}
.bot-btn.danger:hover{background:#2a101088;border-color:#5a202080;color:#c06060}
.ws-el{position:absolute;display:flex;align-items:center;gap:6px;padding:6px 11px;background:#12121e;border:1px solid #2a2a42;border-radius:10px;cursor:grab;font-size:12px;color:#c8c8e8;user-select:none;white-space:nowrap;will-change:transform;transition:border-color .15s,box-shadow .15s}
.ws-el:hover{border-color:#4a4a70;box-shadow:0 2px 12px #0008}
.ws-el.active-drag{border-color:#6060a0;box-shadow:0 4px 20px #00000060;z-index:1000}
.ws-el.merging{animation:poof .35s ease forwards;pointer-events:none}
@keyframes poof{0%{transform:scale(1);opacity:1}40%{transform:scale(1.3);opacity:.7}100%{transform:scale(0);opacity:0}}
#toast-container{position:absolute;bottom:12px;left:12px;display:flex;flex-direction:column-reverse;gap:5px;z-index:999;pointer-events:none;max-width:240px}
.toast{display:flex;align-items:center;gap:8px;padding:7px 11px;background:#0f0f1eee;border:1px solid #282848;border-radius:10px;pointer-events:auto;opacity:0;transform:translateX(-12px);transition:opacity .22s ease,transform .22s ease;font-size:12px;color:#b0b0d0;cursor:default;position:relative;overflow:hidden;backdrop-filter:blur(6px)}
.toast.show{opacity:1;transform:translateX(0)}
.toast.hide{opacity:0;transform:translateX(-12px)}
.toast.rare{border-color:#5030808;background:#110d1eee}
.toast-emoji{font-size:17px;flex-shrink:0}
.toast-name{font-size:12px;font-weight:500;color:#d0d0ff;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;flex:1}
.toast-label{font-size:9px;color:#404070;line-height:1;margin-bottom:1px}
.toast.rare .toast-label{color:#7040a0}
.toast-x{font-size:13px;color:#252545;cursor:pointer;padding:0 2px;line-height:1}
.toast-x:hover{color:#7070a0}
.toast-bar{position:absolute;bottom:0;left:0;height:2px;border-radius:0 0 0 10px;animation:shrink linear forwards}
.toast-bar.normal{background:#3a3a6080}
.toast-bar.rare-bar{background:#7030c080}
@keyframes shrink{from{width:100%}to{width:0%}}
#ach-pop{position:absolute;bottom:12px;right:242px;background:#0f0f1eee;border:1px solid #4030808;border-radius:10px;padding:9px 13px;opacity:0;pointer-events:none;transition:opacity .3s,transform .3s;z-index:800;display:flex;align-items:center;gap:9px;transform:translateY(6px);backdrop-filter:blur(6px)}
#ach-pop.show{opacity:1;transform:translateY(0)}
.ach-icon{font-size:19px}
.ach-title{font-size:11px;color:#c0a0ff;font-weight:500}
.ach-desc{font-size:10px;color:#60508080;margin-top:1px}
#particles{position:absolute;inset:0;pointer-events:none;z-index:500}
</style>

<div id="app">
<canvas id="infinite-bg"></canvas>
<div id="canvas-wrap">
  <div id="world"></div>
</div>
<div id="topbar">
  <div id="stats">
    <div class="stat-pill">Found: <span id="stat-disc">4</span></div>
    <div class="stat-pill">Combos: <span id="stat-combo">0</span></div>
    <div class="stat-pill">Rare: <span id="stat-rare">0</span></div>
  </div>
  <div id="map-btns">
    <button class="map-btn" onclick="clearWorld()">Clear</button>
    <button class="map-btn" onclick="spawnBasics()">+4 basics</button>
    <button class="map-btn" onclick="resetView()">⌂ Home</button>
  </div>
</div>
<div id="zoom-indicator">100%</div>
<div id="toast-container"></div>
<canvas id="particles"></canvas>
<div id="ach-pop"><div class="ach-icon" id="ach-icon"></div><div><div class="ach-title" id="ach-title"></div><div class="ach-desc" id="ach-desc"></div></div></div>
<div id="sidebar">
  <div id="sidebar-header"><h2>Elements</h2><div id="sidebar-count">4</div></div>
  <div id="tabs">
    <div class="tab active" onclick="switchTab('all',this)">All</div>
    <div class="tab" onclick="switchTab('new',this)">New ✨</div>
    <div class="tab" onclick="switchTab('rare',this)">Rare 💜</div>
  </div>
  <div id="search-wrap"><input id="search" type="text" placeholder="Search..."></div>
  <div id="inventory"></div>
  <div id="hints-panel"><div id="hint-label">Combo hints</div><div id="hints-list"></div></div>
  <div id="bottom-bar">
    <button class="bot-btn" onclick="sortInv()">⇅ Sort</button>
    <button class="bot-btn danger" onclick="doReset()">↺ Reset</button>
  </div>
</div>
</div>

<script>
const R={
"Fire+Water":"Steam","Earth+Water":"Plant","Air+Fire":"Energy","Earth+Air":"Dust",
"Fire+Earth":"Lava","Water+Air":"Mist","Steam+Air":"Cloud","Cloud+Water":"Rain",
"Cloud+Cloud":"Storm","Rain+Earth":"Mud","Mud+Fire":"Brick","Lava+Water":"Stone",
"Stone+Air":"Sand","Sand+Fire":"Glass","Plant+Water":"Algae","Plant+Fire":"Ash",
"Plant+Earth":"Tree","Tree+Fire":"Coal","Coal+Fire":"Steel","Tree+Air":"Leaf",
"Stone+Stone":"Mountain","Sand+Water":"Beach","Cloud+Air":"Wind","Rain+Cloud":"Thunder",
"Thunder+Stone":"Lightning","Sand+Sand":"Desert","Ash+Water":"Soap","Stone+Fire":"Metal",
"Metal+Air":"Rust","Earth+Earth":"Earthquake","Energy+Earth":"Volcano","Lava+Earth":"Rock",
"Plant+Air":"Pollen","Water+Water":"Ocean","Energy+Energy":"Nuclear","Mountain+Water":"River",
"Desert+Water":"Oasis","Tree+Water":"Swamp","Metal+Fire":"Forge","Glass+Sand":"Mirror",
"Coal+Air":"Smoke","Smoke+Cloud":"Smog","Energy+Fire":"Explosion","Ocean+Air":"Salt",
"Salt+Water":"Brine","Rock+Water":"Pebble","Lightning+Water":"Electric","Wind+Water":"Wave",
"Plant+Plant":"Garden","Mountain+Air":"Eagle","Ocean+Sand":"Shell","River+Earth":"Flood",
"Steel+Fire":"Sword","Metal+Stone":"Axe","Glass+Fire":"Lens","Brick+Brick":"Wall",
"Wall+Wall":"Castle","Tree+Tree":"Forest","Forest+Fire":"Wildfire","Forest+Rain":"Jungle",
"Jungle+Earth":"Gorilla","Ocean+Earth":"Island","Tree+Sand":"Palm","Eagle+Air":"Sky",
"Energy+Metal":"Electricity","Life+Earth":"Human","Energy+Life":"Evolution",
"Ocean+Life":"Fish","Fish+Air":"Bird","Human+Human":"Love","Love+Human":"Family",
"Fire+Fire":"Sun","Sun+Water":"Rainbow","Sun+Earth":"Day","Moon+Earth":"Night",
"Night+Sun":"Eclipse","Sun+Air":"Light","Light+Water":"Prism","Rainbow+Cloud":"Aurora",
"Air+Air":"Wind","Human+Fire":"Cook","Human+Earth":"Farmer","Human+Water":"Fisher",
"Human+Tree":"Woodcutter","Human+Stone":"Sculptor","Human+Metal":"Blacksmith",
"Human+Energy":"Inventor","Human+Air":"Dreamer","Human+Ocean":"Sailor",
"Cook+Fire":"Chef","Farmer+Plant":"Harvest","Fisher+Ocean":"Whale",
"Woodcutter+Forest":"Lumber","Blacksmith+Steel":"Armor","Inventor+Energy":"Robot",
"Sailor+Ocean":"Pirate","Robot+Energy":"AI","AI+Human":"Cyborg","AI+Energy":"Singularity",
"Sand+Glass":"Hourglass","Hourglass+Energy":"Time","Time+Human":"Memory",
"Time+Earth":"History","Time+Fire":"Phoenix","Phoenix+Ash":"Rebirth",
"History+Earth":"Fossil","Fossil+Energy":"Dinosaur","Dinosaur+Fire":"Dragon",
"Dragon+Water":"Sea Serpent","Dragon+Air":"Wyvern","Dragon+Earth":"Basilisk",
"Dragon+Energy":"Legendary","Water+Cold":"Ice","Ice+Air":"Blizzard","Ice+Earth":"Glacier",
"Ice+Sun":"Puddle","Cold+Mountain":"Avalanche","Blizzard+Forest":"Winter",
"Winter+Sun":"Spring","Spring+Rain":"Flower","Flower+Bee":"Honey","Honey+Fire":"Mead",
"Flower+Wind":"Dandelion","Flower+Earth":"Seed","Seed+Water":"Sprout","Sprout+Sun":"Crop",
"Crop+Human":"Bread","Bread+Fire":"Toast","Animal+Water":"Aquatic","Animal+Earth":"Beast",
"Animal+Air":"Flying","Animal+Human":"Pet","Life+Water":"Bacteria",
"Bacteria+Energy":"Virus","Virus+Human":"Plague","Life+Fire":"Phoenix",
"Life+Air":"Spirit","Life+Ocean":"Whale","Human+Love":"Baby","Baby+Time":"Child",
"Child+Time":"Teenager","Teenager+Time":"Adult","Adult+Love":"Parent",
"Parent+Baby":"Family","Family+Time":"Dynasty","Human+Knowledge":"Scholar",
"Scholar+Book":"Library","Library+Fire":"Alexandria","Knowledge+Energy":"Science",
"Science+Earth":"Geology","Science+Water":"Chemistry","Science+Life":"Biology",
"Biology+Chemistry":"Medicine","Medicine+Human":"Doctor","Sculptor+Clay":"Statue",
"Clay+Fire":"Pottery","Metal+Metal":"Alloy","Alloy+Fire":"Bronze",
"Armor+Human":"Knight","Knight+Dragon":"Legend","Legend+Time":"Myth",
"Myth+Human":"Religion","Religion+Human":"Church","Church+Stone":"Cathedral",
"Cathedral+Light":"Heaven","Heaven+Earth":"Paradise","Storm+Ocean":"Hurricane",
"Earthquake+Ocean":"Tsunami","Space+Fire":"Star","Star+Star":"Galaxy",
"Galaxy+Time":"Universe","Universe+Life":"Cosmos","Space+Human":"Astronaut",
"Moon+Water":"Tide","Moon+Wolf":"Howl","Star+Water":"Navigation",
"Navigation+Human":"Explorer","Explorer+Earth":"Cartographer","Paper+Tree":"Book",
"Book+Knowledge":"Tome","Tome+Magic":"Grimoire","Oil+Fire":"Fuel",
"Fuel+Energy":"Engine","Engine+Metal":"Machine","Machine+Human":"Industry",
"Animal+Forest":"Wolf","Wolf+Moon":"Werewolf","Ocean+Moon":"Kraken",
"Ship+Wind":"Sail","Sail+Ocean":"Voyage","Stone+Sand":"Gravel",
"Gravel+Water":"Concrete","Concrete+Steel":"Skyscraper","City+Energy":"Metropolis",
"Metropolis+AI":"Smart City","Magic+Fire":"Spell","Spell+Human":"Witch",
"Witch+Cauldron":"Potion","Cauldron+Fire":"Brew","Sage+Magic":"Wizard",
"Wilderness+Human":"Hermit","Hermit+Knowledge":"Sage","Forest+Mountain":"Wilderness",
"Mountain+Water":"Waterfall","Waterfall+Sun":"Mist",
};

const RARE=new Set(["Dragon","AI","Singularity","Legendary","Universe","Cosmos","Grimoire","Werewolf","Kraken","Phoenix","Aurora","Eclipse","Dynasty","Smart City","Cyborg","Myth","Heaven","Wizard","Wyvern","Basilisk","Sea Serpent"]);
const EM={
"Fire":"🔥","Water":"💧","Earth":"🌍","Air":"💨","Steam":"♨️","Plant":"🌿","Energy":"⚡","Dust":"🌫️","Lava":"🌋","Mist":"🌁","Cloud":"☁️","Rain":"🌧️","Storm":"⛈️","Mud":"🟤","Brick":"🧱","Stone":"🪨","Sand":"🏖️","Glass":"🔮","Algae":"🌊","Ash":"💨","Tree":"🌲","Coal":"⚫","Steel":"🔩","Leaf":"🍃","Mountain":"⛰️","Beach":"🏝️","Wind":"🌀","Thunder":"⚡","Lightning":"⚡","Desert":"🏜️","Soap":"🧼","Metal":"⚙️","Rust":"🔶","Earthquake":"🌐","Volcano":"🌋","Rock":"🪨","Pollen":"🌻","Ocean":"🌊","Nuclear":"☢️","River":"🌊","Oasis":"🌴","Swamp":"🐸","Forge":"🔨","Mirror":"🪞","Smoke":"💭","Smog":"🌫️","Explosion":"💥","Salt":"🧂","Brine":"🫙","Pebble":"⚫","Electric":"⚡","Wave":"🌊","Garden":"🌷","Eagle":"🦅","Shell":"🐚","Flood":"🌊","Sword":"⚔️","Axe":"🪓","Lens":"🔍","Wall":"🧱","Castle":"🏰","Forest":"🌳","Wildfire":"🔥","Jungle":"🌴","Gorilla":"🦍","Island":"🏝️","Palm":"🌴","Sky":"☀️","Electricity":"💡","Cook":"👨‍🍳","Farmer":"👨‍🌾","Human":"👤","Life":"✨","Evolution":"🧬","Fish":"🐟","Bird":"🐦","Love":"❤️","Family":"👨‍👩‍👧","Village":"🏘️","House":"🏠","Sun":"☀️","Rainbow":"🌈","Day":"🌅","Night":"🌙","Moon":"🌙","Eclipse":"🌑","Light":"💫","Prism":"🔺","Aurora":"🌌","Ice":"🧊","Cold":"❄️","Blizzard":"🌨️","Glacier":"🏔️","Puddle":"💧","Avalanche":"🏔️","Winter":"❄️","Spring":"🌸","Flower":"🌸","Bee":"🐝","Honey":"🍯","Mead":"🍺","Dandelion":"🌼","Perfume":"🌺","Seed":"🌱","Sprout":"🌱","Crop":"🌾","Bread":"🍞","Toast":"🍞","Animal":"🐾","Aquatic":"🐠","Beast":"🦁","Flying":"🐦","Pet":"🐶","Bacteria":"🦠","Virus":"🦠","Plague":"💀","Phoenix":"🔥","Spirit":"👻","Whale":"🐋","Baby":"👶","Child":"🧒","Teenager":"🧑","Adult":"🧑","Parent":"👪","Dynasty":"👑","Scholar":"📚","Book":"📖","Library":"📚","Alexandria":"🏛️","Knowledge":"🧠","Science":"🔬","Geology":"⛏️","Chemistry":"⚗️","Biology":"🧬","Medicine":"💊","Doctor":"👨‍⚕️","Sculptor":"🗿","Clay":"🟫","Statue":"🗿","Pottery":"🏺","Alloy":"🔧","Bronze":"🏅","Armor":"🛡️","Knight":"⚔️","Legend":"⭐","Myth":"📜","Religion":"🙏","Church":"⛪","Cathedral":"⛪","Heaven":"🌟","Paradise":"🌅","Hurricane":"🌀","Tsunami":"🌊","Star":"⭐","Galaxy":"🌌","Universe":"🌌","Cosmos":"🌌","Astronaut":"👨‍🚀","Tide":"🌊","Wolf":"🐺","Howl":"🐺","Navigation":"🧭","Explorer":"🧭","Cartographer":"🗺️","Paper":"📄","Book":"📖","Tome":"📕","Grimoire":"📕","Fuel":"⛽","Engine":"⚙️","Machine":"⚙️","Industry":"🏭","Werewolf":"🐺","Kraken":"🐙","Ship":"⛵","Sail":"⛵","Voyage":"⛵","Gravel":"🪨","Concrete":"🏗️","Skyscraper":"🏙️","City":"🏙️","Metropolis":"🏙️","Smart City":"🤖","AI":"🤖","Cyborg":"🤖","Singularity":"♾️","Robot":"🤖","Inventor":"💡","Dreamer":"💭","Fisher":"🎣","Woodcutter":"🪓","Blacksmith":"⚒️","Chef":"👨‍🍳","Harvest":"🌾","Lumber":"🪵","Pirate":"🏴‍☠️","Sailor":"⛵","Hourglass":"⏳","Time":"⏰","Memory":"💭","History":"📜","Fossil":"🦕","Dinosaur":"🦕","Dragon":"🐉","Sea Serpent":"🐍","Wyvern":"🐉","Basilisk":"🐍","Legendary":"⭐","Rebirth":"✨","Oil":"🛢️","Magic":"✨","Wizard":"🧙","Spell":"✨","Witch":"🧙‍♀️","Cauldron":"🫕","Potion":"🧪","Brew":"🧪","Sage":"🧙","Hermit":"🧙","Wilderness":"🌲","Waterfall":"🌊","Mist":"🌁",
};
function em(n){return EM[n]||"✨"}

const SK="icv4";
let disc=new Set(["Fire","Water","Earth","Air"]);
let newEls=new Set(),combos=0,rares=0,unlockedAch=new Set();
let wsEls=[],elId=0;
let curTab="all",sortMode="alpha";
let achTimer=null;

let panX=0,panY=0,zoom=1;
let isPanning=false,panStartX=0,panStartY=0,panOriginX=0,panOriginY=0;
let elDrag=null,elDragOffX=0,elDragOffY=0,elDragFrom=null;
const MIN_ZOOM=.3,MAX_ZOOM=2.5;

const wrap=()=>document.getElementById("canvas-wrap");
const world=()=>document.getElementById("world");

function applyTransform(){
world().style.transform=`translate(${panX}px,${panY}px) scale(${zoom})`;
document.getElementById("zoom-indicator").textContent=Math.round(zoom*100)+"%";
}

function resetView(){
panX=0;panY=0;zoom=1;applyTransform();
}

function clampPan(){
const w=wrap().offsetWidth,h=wrap().offsetHeight;
const pad=200;
panX=Math.min(pad,Math.max(-(5000-w+pad),panX));
panY=Math.min(pad,Math.max(-(5000-h+pad),panY));
}

function screenToWorld(sx,sy){
return{x:(sx-panX)/zoom,y:(sy-panY)/zoom};
}

wrap().addEventListener("mousedown",e=>{
if(elDrag)return;
if(e.target!==wrap()&&!e.target.id.startsWith("world")&&e.target.id!=="infinite-bg"&&!e.target.closest("#world")){return;}
if(e.target.closest(".ws-el"))return;
isPanning=true;
wrap().classList.add("panning");
panStartX=e.clientX;panStartY=e.clientY;
panOriginX=panX;panOriginY=panY;
});

document.addEventListener("mousemove",e=>{
if(elDrag){
  const cwr=wrap().getBoundingClientRect();
  const wx=(e.clientX-cwr.left-panX)/zoom-elDragOffX;
  const wy=(e.clientY-cwr.top-panY)/zoom-elDragOffY;
  elDrag.style.left=wx+"px";elDrag.style.top=wy+"px";
  return;
}
if(!isPanning)return;
const dx=e.clientX-panStartX,dy=e.clientY-panStartY;
panX=panOriginX+dx;panY=panOriginY+dy;
clampPan();applyTransform();
});

document.addEventListener("mouseup",e=>{
isPanning=false;wrap().classList.remove("panning");
if(!elDrag)return;
const dropped=elDrag;
const dName=dropped.dataset.name;
let merged=false;
wsEls.forEach(({id,el,name})=>{
  if(merged||el===dropped)return;
  const dx=parseFloat(dropped.style.left)-parseFloat(el.style.left);
  const dy=parseFloat(dropped.style.top)-parseFloat(el.style.top);
  if(Math.sqrt(dx*dx+dy*dy)<80){
    merged=true;
    const mx=(parseFloat(dropped.style.left)+parseFloat(el.style.left))/2;
    const my=(parseFloat(dropped.style.top)+parseFloat(el.style.top))/2;
    const res=combine(dName,name);
    const isNew=!disc.has(res);
    el.classList.add("merging");dropped.classList.add("merging");
    setTimeout(()=>{
      removeEl(id);removeEl(dropped.id);
      if(isNew){newEls.add(res);if(RARE.has(res))rares++;}
      disc.add(res);combos++;
      checkAch(res);save();renderInv();renderHints();
      if(isNew)showToast(res);
      spawnEl(res,mx,my);
    },300);
  }
});
dropped.classList.remove("active-drag");
dropped.style.zIndex="";
elDrag=null;elDragFrom=null;
wrap().classList.remove("dragging-el");
});

wrap().addEventListener("wheel",e=>{
e.preventDefault();
const cwr=wrap().getBoundingClientRect();
const mx=e.clientX-cwr.left,my=e.clientY-cwr.top;
const delta=e.deltaY>0?-.1:.1;
const nz=Math.min(MAX_ZOOM,Math.max(MIN_ZOOM,zoom+delta));
panX=mx-(mx-panX)*(nz/zoom);
panY=my-(my-panY)*(nz/zoom);
zoom=nz;clampPan();applyTransform();
},{passive:false});

function combine(a,b){
const k1=a+"+"+b,k2=b+"+"+a;
if(R[k1])return R[k1];if(R[k2])return R[k2];
return fallback(a,b);
}
function fallback(a,b){
const s=[a,b].sort().join("");let h=0;
for(let i=0;i<s.length;i++){h=((h<<5)-h)+s.charCodeAt(i);h|=0;}
const w=["Prism","Aether","Vortex","Nexus","Flux","Echo","Nova","Ember","Tide","Rift","Shard","Wisp","Rune","Cipher","Gale","Cinder","Bloom"];
return w[Math.abs(h)%w.length]+" "+[a,b].sort()[0];
}

function spawnEl(name,x,y){
const id="e"+(elId++);
const div=document.createElement("div");
div.className="ws-el";div.id=id;div.dataset.name=name;
div.innerHTML=`<span style="font-size:14px">${em(name)}</span><span>${name}</span>`;
div.style.cssText=`left:${x}px;top:${y}px;`;
div.addEventListener("mousedown",e=>{
  e.preventDefault();e.stopPropagation();
  elDrag=div;elDragFrom="world";
  div.classList.add("active-drag");div.style.zIndex=1000;
  wrap().classList.add("dragging-el");
  const dwr=div.getBoundingClientRect();
  const cwr=wrap().getBoundingClientRect();
  elDragOffX=(e.clientX-cwr.left-panX)/zoom-parseFloat(div.style.left);
  elDragOffY=(e.clientY-cwr.top-panY)/zoom-parseFloat(div.style.top);
});
world().appendChild(div);
wsEls.push({id,el:div,name});
return div;
}
function removeEl(id){wsEls=wsEls.filter(e=>e.id!==id);const el=document.getElementById(id);if(el)el.remove();}
function clearWorld(){wsEls.forEach(({el})=>el.remove());wsEls=[];}
function spawnBasics(){
const cwr=wrap().getBoundingClientRect();
const cx=(-panX+cwr.width/2)/zoom,cy=(-panY+cwr.height/2)/zoom;
[["Fire",-80,-35],["Water",30,-35],["Earth",-80,30],["Air",30,30]].forEach(([n,ox,oy])=>spawnEl(n,cx+ox,cy+oy));
}

function startSideDrag(e,name){
e.preventDefault();
const cwr=wrap().getBoundingClientRect();
const wx=(e.clientX-cwr.left-panX)/zoom-50;
const wy=(e.clientY-cwr.top-panY)/zoom-15;
const div=spawnEl(name,wx,wy);
elDrag=div;elDragFrom="sidebar";
div.classList.add("active-drag");div.style.zIndex=1000;
wrap().classList.add("dragging-el");
const dwr=div.getBoundingClientRect();
const cwr2=wrap().getBoundingClientRect();
elDragOffX=(e.clientX-cwr2.left-panX)/zoom-parseFloat(div.style.left);
elDragOffY=(e.clientY-cwr2.top-panY)/zoom-parseFloat(div.style.top);
}

function save(){
try{localStorage.setItem(SK,JSON.stringify([...disc]));
localStorage.setItem(SK+"c",combos);localStorage.setItem(SK+"r",rares);
localStorage.setItem(SK+"a",JSON.stringify([...unlockedAch]));}catch(e){}
}
function load(){
try{const d=localStorage.getItem(SK);if(d)disc=new Set(JSON.parse(d));
combos=parseInt(localStorage.getItem(SK+"c")||"0");
rares=parseInt(localStorage.getItem(SK+"r")||"0");
const a=localStorage.getItem(SK+"a");if(a)unlockedAch=new Set(JSON.parse(a));
newEls=new Set();}catch(e){}
}

const ACHS=[
{id:"a10",count:10,icon:"🌱",title:"Budding Crafter",desc:"Discover 10 elements"},
{id:"a25",count:25,icon:"📚",title:"Student of Elements",desc:"Discover 25 elements"},
{id:"a50",count:50,icon:"🔮",title:"Elemental Master",desc:"Discover 50 elements"},
{id:"dragon",special:"Dragon",icon:"🐉",title:"Dragon Tamer",desc:"Discovered Dragon"},
{id:"ai",special:"AI",icon:"🤖",title:"Tech Pioneer",desc:"Discovered AI"},
{id:"cosmos",special:"Cosmos",icon:"🌌",title:"Cosmologist",desc:"Discovered the Cosmos"},
];
function checkAch(name){
ACHS.forEach(a=>{
if(unlockedAch.has(a.id))return;
if((a.count&&disc.size>=a.count)||(a.special&&name===a.special)){
unlockedAch.add(a.id);
document.getElementById("ach-icon").textContent=a.icon;
document.getElementById("ach-title").textContent=a.title;
document.getElementById("ach-desc").textContent=a.desc;
const p=document.getElementById("ach-pop");p.classList.add("show");
if(achTimer)clearTimeout(achTimer);
achTimer=setTimeout(()=>p.classList.remove("show"),3500);}
});
}

function showToast(name){
const c=document.getElementById("toast-container");
const rare=RARE.has(name),dur=rare?4000:3000;
const t=document.createElement("div");
t.className="toast"+(rare?" rare":"");
t.innerHTML=`<span class="toast-emoji">${em(name)}</span><div style="flex:1;min-width:0"><div class="toast-label">${rare?"✨ Rare":"New"}</div><div class="toast-name">${name}</div></div><span class="toast-x" onclick="dmToast(this.parentElement)">✕</span><div class="toast-bar ${rare?"rare-bar":"normal"}" style="animation-duration:${dur}ms"></div>`;
c.appendChild(t);
const all=c.querySelectorAll(".toast");if(all.length>5)dmToast(all[0]);
requestAnimationFrame(()=>requestAnimationFrame(()=>t.classList.add("show")));
setTimeout(()=>dmToast(t),dur);
if(rare)doParticles();
}
function dmToast(t){if(!t||t.classList.contains("hide"))return;t.classList.add("hide");setTimeout(()=>t.remove(),240);}

function doParticles(){
const cv=document.getElementById("particles");
const ctx=cv.getContext("2d");
const cwr=wrap();cv.width=cwr.offsetWidth;cv.height=cwr.offsetHeight;
const cx=cwr.offsetWidth/2,cy=cwr.offsetHeight/2;
const ps=Array.from({length:24},()=>{
const a=Math.random()*Math.PI*2,sp=1.5+Math.random()*3;
return{x:cx,y:cy,vx:Math.cos(a)*sp,vy:Math.sin(a)*sp,life:1,
color:`hsl(${260+Math.random()*60},70%,70%)`,sz:2+Math.random()*3};
});
let af;
(function anim(){
ctx.clearRect(0,0,cv.width,cv.height);let alive=false;
ps.forEach(p=>{p.x+=p.vx;p.y+=p.vy;p.vy+=.06;p.life-=.026;
if(p.life>0){alive=true;ctx.globalAlpha=p.life;ctx.fillStyle=p.color;ctx.beginPath();ctx.arc(p.x,p.y,p.sz,0,Math.PI*2);ctx.fill();}});
ctx.globalAlpha=1;if(alive)af=requestAnimationFrame(anim);else ctx.clearRect(0,0,cv.width,cv.height);
})();
}

function renderInv(){
const q=document.getElementById("search").value.toLowerCase();
let items=[...disc];
if(curTab==="new")items=items.filter(n=>newEls.has(n));
if(curTab==="rare")items=items.filter(n=>RARE.has(n));
items=items.filter(n=>n.toLowerCase().includes(q));
if(sortMode==="alpha")items.sort();else items.sort((a,b)=>a.length-b.length);
document.getElementById("sidebar-count").textContent=disc.size;
document.getElementById("stat-disc").textContent=disc.size;
document.getElementById("stat-combo").textContent=combos;
document.getElementById("stat-rare").textContent=rares;
const inv=document.getElementById("inventory");inv.innerHTML="";
items.forEach(name=>{
const d=document.createElement("div");d.className="inv-item";d.dataset.name=name;
const rare=RARE.has(name),isNew=newEls.has(name);
d.innerHTML=`<span style="font-size:14px">${em(name)}</span><span style="flex:1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${name}</span>${isNew?'<span class="badge-new">new</span>':rare?'<span class="badge-rare">rare</span>':''}`;
d.addEventListener("mousedown",e=>startSideDrag(e,name));
inv.appendChild(d);
});
}

function renderHints(){
const list=document.getElementById("hints-list");
const arr=[...disc];const hints=[];
for(let i=0;i<arr.length&&hints.length<6;i++)
for(let j=i;j<arr.length&&hints.length<6;j++){
const a=arr[i],b=arr[j],res=R[a+"+"+b]||R[b+"+"+a];
if(res&&!disc.has(res))hints.push({a,b});}
list.innerHTML="";
if(!hints.length){list.innerHTML='<div class="hint-row" style="color:#252540">All found!</div>';return;}
hints.slice(0,4).forEach(h=>{
const d=document.createElement("div");d.className="hint-row";
d.innerHTML=`<span class="h-known">${em(h.a)} ${h.a}</span><span class="h-plus"> + </span><span class="h-known">${em(h.b)} ${h.b}</span><span class="h-plus"> = ???</span>`;
list.appendChild(d);
});
}

function switchTab(tab,el){curTab=tab;document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));el.classList.add("active");renderInv();}
function sortInv(){sortMode=sortMode==="alpha"?"len":"alpha";renderInv();}
function doReset(){if(!confirm("Reset all progress?"))return;
disc=new Set(["Fire","Water","Earth","Air"]);newEls=new Set();combos=0;rares=0;unlockedAch=new Set();
wsEls.forEach(({el})=>el.remove());wsEls=[];save();renderInv();renderHints();}
document.getElementById("search").addEventListener("input",renderInv);

function drawBg(){
const cv=document.getElementById("infinite-bg");
const cwr=document.getElementById("canvas-wrap");
cv.width=cwr.offsetWidth;cv.height=cwr.offsetHeight;
cv.style.position="absolute";cv.style.inset="0";cv.style.zIndex="0";
const ctx=cv.getContext("2d");
ctx.fillStyle="#080810";ctx.fillRect(0,0,cv.width,cv.height);
const gridSize=40*zoom;
const ox=((panX%gridSize)+gridSize)%gridSize;
const oy=((panY%gridSize)+gridSize)%gridSize;
ctx.strokeStyle="#0f0f1e";ctx.lineWidth=.5;
for(let x=ox-gridSize;x<cv.width+gridSize;x+=gridSize){
ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,cv.height);ctx.stroke();}
for(let y=oy-gridSize;y<cv.height+gridSize;y+=gridSize){
ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(cv.width,y);ctx.stroke();}
const bigGrid=200*zoom;
const box=((panX%bigGrid)+bigGrid)%bigGrid;
const boy=((panY%bigGrid)+bigGrid)%bigGrid;
ctx.strokeStyle="#14142a";ctx.lineWidth=.8;
for(let x=box-bigGrid;x<cv.width+bigGrid;x+=bigGrid){
ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,cv.height);ctx.stroke();}
for(let y=boy-bigGrid;y<cv.height+bigGrid;y+=bigGrid){
ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(cv.width,y);ctx.stroke();}
const stars=[...Array(60)].map((_,i)=>{
const seed=i*137.508;return{x:((seed*91)%1000)/1000*cv.width,y:((seed*73)%1000)/1000*cv.height,r:.5+((seed*53)%10)/10,a:.2+((seed*37)%10)/10*.5};
});
stars.forEach(s=>{ctx.globalAlpha=s.a;ctx.fillStyle="#a0a0ff";ctx.beginPath();ctx.arc(s.x,s.y,s.r,0,Math.PI*2);ctx.fill();});
ctx.globalAlpha=1;
}

let bgRaf;
function animBg(){drawBg();bgRaf=requestAnimationFrame(animBg);}
animBg();

load();renderInv();renderHints();
setTimeout(spawnBasics,50);
</script>
