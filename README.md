<!doctype html>
<html lang="mn">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Төрсөн өдрийн мэнд — Захиа</title>
<style>
  :root{
    --bg:#FFF7FB;
    --accent:#FF9BCB;
    --accent-2:#FFD7EA;
    --text:#2a2a2a;
    --card:#fff;
  }
  *{box-sizing:border-box;font-family: 'Helvetica Neue', Arial, sans-serif}
  body{
    margin:0;
    min-height:100vh;
    display:flex;
    align-items:center;
    justify-content:center;
    background: radial-gradient(circle at 10% 20%, #fff5fb 0%, var(--bg) 35%, #f7f3ff 100%);
    color:var(--text);
    padding:32px;
  }

  .stage{
    width:900px;
    max-width:95vw;
    display:grid;
    grid-template-columns: 1fr 360px;
    gap:24px;
    align-items:start;
  }

  /* Left: interactive envelope */
  .envelope-wrap{
    perspective:1200px;
    display:flex;
    align-items:flex-start;
    justify-content:center;
    min-height:420px;
  }
  .envelope {
    width:520px;
    max-width:100%;
    height:320px;
    position:relative;
    transform-style:preserve-3d;
    transition: transform .8s cubic-bezier(.2,.9,.3,1);
  }

  /* envelope body (back) */
  .env-back{
    position:absolute;
    inset:0;
    background: linear-gradient(180deg,var(--accent-2),#fff);
    border-radius:10px;
    box-shadow: 0 10px 30px rgba(0,0,0,.12);
    display:flex;
    align-items:center;
    justify-content:center;
    overflow:hidden;
  }
  .env-back::after{
    content:"";
    position:absolute;
    inset:0;
    background-image:radial-gradient(circle at 10% 10%, rgba(255,255,255,.6), transparent 20%);
    opacity:.7;
  }

  /* flap (front triangle) */
  .env-flap{
    position:absolute;
    left:0; right:0;
    top:0;
    height:50%;
    transform-origin: top center;
    background: linear-gradient(180deg,var(--accent), #ffedef 60%);
    border-top-left-radius:10px;
    border-top-right-radius:10px;
    clip-path: polygon(0 0, 100% 0, 50% 100%);
    box-shadow: 0 8px 18px rgba(0,0,0,.12);
    z-index:5;
    transform: rotateX(0deg);
    transition: transform .9s cubic-bezier(.15,.9,.3,1);
  }

  /* paper (letter) */
  .paper {
    position:absolute;
    left:50%;
    transform:translateX(-50%);
    top:26%;
    width:84%;
    height:66%;
    background: linear-gradient(180deg,#fff 0%, #fffefc 100%);
    border-radius:6px;
    padding:28px;
    box-shadow: 0 12px 30px rgba(0,0,0,.12);
    z-index:2;
    transform-origin: top center;
    transition: transform .9s cubic-bezier(.2,.9,.3,1), top .9s ease;
    overflow:auto;
  }

  /* closed state - letter hidden */
  .envelope.closed .env-flap { transform: rotateX(0deg); }
  .envelope.closed .paper { top:36%; transform: translateX(-50%) translateY(-18px) scale(.98); opacity:0; pointer-events:none }

  /* opened state */
  .envelope.open .env-flap { transform: rotateX(-180deg); }
  .envelope.open .paper { top:8%; transform: translateX(-50%) translateY(0) scale(1); opacity:1; pointer-events:auto }

  /* animated reveal touches */
  .paper h1{ margin:0 0 8px 0; font-size:28px; color:#c33; letter-spacing:0.2px; }
  .paper p{ margin:10px 0; line-height:1.6; color: #333; }
  .paper .signature{ margin-top:18px; font-weight:600; color:#7a3b5b }

  /* Right: instructions & controls */
  .controls{
    background:linear-gradient(180deg, rgba(255,255,255,.85), rgba(255,255,255,.95));
    border-radius:12px;
    padding:18px;
    box-shadow: 0 8px 20px rgba(0,0,0,.06);
    display:flex;
    flex-direction:column;
    gap:12px;
    height:fit-content;
  }
  .btn{
    display:inline-flex; gap:8px; align-items:center; justify-content:center;
    padding:10px 14px; border-radius:10px; cursor:pointer; user-select:none;
    border:0; font-weight:700;
  }
  .open-btn{ background:linear-gradient(90deg,#ff85bf,#ffb0da); color:white; }
  .reset-btn{ background:#fff; border:1px solid #eee; color:#444; }

  .playbtn{
    display:flex; gap:8px; align-items:center;
  }

  /* confetti balloons */
  .balloon {
    position:absolute;
    left:12%;
    top:-40px;
    z-index:0;
    pointer-events:none;
    opacity:0.95;
  }

  /* simple confetti dots */
  .confetti {
    position:absolute;
    inset:0;
    pointer-events:none;
    overflow:visible;
  }

  /* small responsive adjustments */
  @media (max-width:880px){
    .stage{ grid-template-columns: 1fr; }
    .envelope { height:260px; }
    .paper { padding:20px; }
  }
</style>
</head>
<body>
  <div class="stage">
    <div class="envelope-wrap">
      <div class="envelope closed" id="envelope">
        <div class="env-back">
          <!-- decorative stamp / text -->
          <div style="text-align:center;">
            <div style="font-weight:700;color:#8b3b5f;font-size:18px">Happy Birthday</div>
            <div style="font-size:12px;color:#b36b8b;margin-top:6px">A special letter for you</div>
          </div>
        </div>
        <div class="env-flap" id="flap"></div>
        <div class="paper" id="paper" role="region" aria-hidden="true">
          <!-- EDIT THESE: recipient name and letter content -->
          <h1 id="recipient> kira,</h1>
          <div id="letter-content">
            <p>Өнөөдөр бол чиний онцгой өдөр — төрсөн өдөр нь 🎂</p>
            <p> инээд баясгалан, шинэ боломжууд
            <p>Өнөөдрөөс эхлээд чиний өдөр бүр илүү гэгээлэг, урамтай байж, эргэн тойрон дахь хүмүүс чамайг хайрлаж, дэмжиж байдаг байг.</p>
            <p class="signature">Хайртай, Чиний [Нэр]</p>
          </div>
        </div>
        <!-- small balloon svg -->
        <svg class="balloon" width="120" height="120" viewBox="0 0 64 64" fill="none" xmlns="http://www.w3.org/2000/svg">
          <ellipse cx="32" cy="26" rx="18" ry="20" fill="#ff9bbf" opacity="0.95"/>
          <path d="M32 42c0 4 8 8 0 12" stroke="#b55" stroke-width="1.6" stroke-linecap="round" fill="none"/>
        </svg>
      </div>
    </div>

    <aside class="controls" aria-label="Захиа хянах">
      <div style="font-weight:800; font-size:18px;">Төрсөн өдрийн захиа</div>
      <div style="font-size:14px; color:#666">Сайтын хэрэглэгч линк дээр дарахад дорхноос захиа “задрах” анимац гарч ирнэ. Доорх товчоор нээгээд дахиж хааж болно.</div>

      <div style="display:flex;gap:8px">
        <button class="btn open-btn" id="openBtn" aria-pressed="false">🎉 Захиаг нээх</button>
        <button class="btn reset-btn" id="resetBtn">🔁 Буцаах</button>
      </div>

      <div style="display:flex; gap:8px; align-items:center; margin-top:6px;">
        <button id="playAudio" class="btn" style="background:#fff;border:1px solid #ffd6ed;">▶️ Дуу гарах</button>
        <button id="stopAudio" class="btn" style="background:#fff;border:1px solid #ffd6ed;">⏹️ Зогсоох</button>
      </div>

      <div style="margin-top:8px; font-size:14px; color:#444;">
        <div><strong>Зөвлөмж:</strong> Нэр, захиаг өөрчлөхийн тулд код дахь <code>RECIPIENT_NAME</code> болон <code>LETTER_HTML</code> хэсгийг засна уу.</div>
      </div>

      <div style="margin-top:12px; display:flex; gap:8px;">
        <a id="shareLink" class="btn" style="background:linear-gradient(90deg,#ffd5ed,#fff); text-decoration:none; color:#333;" href="#" onclick="copyShare(); return false;">🔗 Хуваалцах линк буюу код авах</a>
      </div>
    </aside>
  </div>

  <!-- small confetti container -->
  <div class="confetti" id="confetti-root" aria-hidden="true"></div>

<script>
/* --------------- Editable area ----------------
   Change these to personalize the letter before sending the file.
   RECIPIENT_NAME: string (plain)
   LETTER_HTML: HTML string for the content of the letter (paragraphs, emoji allowed)
------------------------------------------------*/
const RECIPIENT_NAME = "Kira"; // <- set recipient name
const LETTER_HTML = `
  <p>Өнөөдөр бол чиний онцгой өдөр — төрсөн өдөр! 🎂</p>
  <p>Хөөрхөн найздаа төрсөн өдрийн баярын мэнд хүргье. Хамгийн анх стори рэплэе хийж орж ирснээс 1 жил өнгэрчээ.Нууж хаах зүйлгүйгээр хүнтэй ярилцах чөлөөтэй байх вуаа үнэхээр гоё байдаг юм байна лээ. Магадгүй хүн алсан ч чамд хэлж болохоор санагддаг шүү ахх. Тийм л дотно чөлөөтэй найз маань байдагт баярлалаа. жилд 1 удаа уулздаг биш зөндөө их уулзаж хөгжилддөг болцгооё. Заа тэгээд найздаа хайртай шүү сайхан баярлаарай🥰🫶🏻🍀</p>
  <p>Өнөөдрөөс эхлээд чиний бүх өдөр илүү гэгээлэг, урамтай байг.</p>
  <p class="signature">Хайртай, Чиний Найз</p>
`;

/* ---------- end editable area ---------- */

document.getElementById('recipient').textContent = RECIPIENT_NAME + "-д,";
document.getElementById('letter-content').innerHTML = LETTER_HTML;

const env = document.getElementById('envelope');
const openBtn = document.getElementById('openBtn');
const resetBtn = document.getElementById('resetBtn');
const confettiRoot = document.getElementById('confetti-root');

openBtn.addEventListener('click', ()=> toggleOpen(true));
resetBtn.addEventListener('click', ()=> toggleOpen(false));

function toggleOpen(open){
  if(open){
    env.classList.remove('closed');
    env.classList.add('open');
    env.querySelector('#paper').setAttribute('aria-hidden','false');
    openBtn.setAttribute('aria-pressed','true');
    startConfetti();
    playMusic();
  } else {
    env.classList.remove('open');
    env.classList.add('closed');
    env.querySelector('#paper').setAttribute('aria-hidden','true');
    openBtn.setAttribute('aria-pressed','false');
    stopConfetti();
    stopMusic();
  }
}

/* Simple confetti dots generator (non-library) */
let confettiTimer = null;
function startConfetti(){
  if(confettiTimer) return;
  confettiRoot.innerHTML = "";
  confettiTimer = setInterval(()=> {
    const dot = document.createElement('div');
    const size = Math.random()*10 + 6;
    dot.style.width = size + 'px';
    dot.style.height = size + 'px';
    dot.style.position = 'absolute';
    dot.style.left = (Math.random()*100) + '%';
    dot.style.top = '-10px';
    const hues = ['#ff889e','#ffd18b','#b6f0c3','#b0d8ff','#f2b9ff'];
    dot.style.background = hues[Math.floor(Math.random()*hues.length)];
    dot.style.borderRadius = '50%';
    dot.style.opacity = '0.98';
    dot.style.transform = 'translateY(0)';
    dot.style.transition = 'transform 2.2s linear, opacity 2.2s linear';
    confettiRoot.appendChild(dot);
    // animate
    requestAnimationFrame(()=> {
      const move = window.innerHeight*0.6 + Math.random()*160;
      dot.style.transform = 'translateY(' + move + 'px) rotate(' + (Math.random()*80-40) + 'deg)';
      dot.style.opacity = '0';
    });
    // remove after animation
    setTimeout(()=> confettiRoot.removeChild(dot), 2400);
  }, 110);
}

function stopConfetti(){
  if(confettiTimer){
    clearInterval(confettiTimer);
    confettiTimer = null;
  }
  // clear existing
  confettiRoot.innerHTML = "";
}

/* Audio controls */
let audio = null;
function ensureAudio(){
  if(audio) return;
  audio = new Audio();
  // You can replace with any online audio URL or encode base64 - default uses a light bday melody (data URL not included).
  // For safety of local file usage, user can uncomment and set a file path or URL below:
  // audio.src = "https://example.com/your-bday-music.mp3";
  audio.loop = true;
}
function playMusic(){
  ensureAudio();
  // try to play (may be blocked by browser autoplay policy; user can press Play button)
  audio.play().catch(()=>{/* autoplay blocked — user must press play */});
}
function stopMusic(){
  if(audio){ audio.pause(); audio.currentTime = 0; }
}

// Manual play/stop buttons
document.getElementById('playAudio').addEventListener('click', ()=>{
  ensureAudio();
  audio.play().catch(()=> alert('Браузер автоматаар дуу тоглуулахыг хорьдож байна. "Дарах" товчлуурыг дахин дарж үзнэ үү.'));
});
document.getElementById('stopAudio').addEventListener('click', ()=> { stopMusic(); });

/* share link: copy code to clipboard */
function copyShare(){
  const full = document.documentElement.outerHTML;
  navigator.clipboard.writeText(full).then(()=> {
    alert("Сайтын код хуулж авлаа. Та үүнийг index.html гэж хадгалж хуваалцаж болно.");
  }).catch(()=> {
    alert("Кодыг хуулах боломжгүй боллоо. Та кодыг шууд файлын дотор гараар хуулана уу.");
  });
}

/* Optional: open envelope when page loaded after small delay */
window.addEventListener('load', ()=> {
  // Do not auto-open; leave closed so when линк дарна гэж төсөөлж болно.
  // If you want auto-open, uncomment: toggleOpen(true);
});
</script>
</body>
</html>
