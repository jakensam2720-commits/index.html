# index.html
from pathlib import Path
import zipfile

out = Path("/mnt/data/NeonRush_iPhone")
out.mkdir(exist_ok=True)
html = r'''<!doctype html><html><head>
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover,user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes"><meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>Neon Rush</title>
<style>
*{box-sizing:border-box}html,body{margin:0;width:100%;height:100%;overflow:hidden;background:#070916;color:#fff;font-family:-apple-system,BlinkMacSystemFont,Arial}
canvas{width:100%;height:100%;display:block;touch-action:none}
#ui{position:fixed;inset:0;pointer-events:none}.hud{display:flex;justify-content:space-between;padding:calc(env(safe-area-inset-top) + 14px) 22px 0;font-weight:800;font-size:17px;text-shadow:0 2px 8px #000}
.screen{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;background:rgba(4,6,20,.82);pointer-events:auto}
.card{width:min(88vw,360px);padding:30px 24px;text-align:center;border:1px solid #5df;border-radius:24px;background:#11162d;box-shadow:0 0 35px rgba(70,210,255,.28)}
h1{font-size:42px;margin:0 0 10px;letter-spacing:3px}p{line-height:1.45;opacity:.86}
button{font:800 19px -apple-system,BlinkMacSystemFont,Arial;border:0;border-radius:999px;padding:15px 38px;background:#5df;color:#06101b}
#over{display:none}
</style></head><body>
<canvas id="c"></canvas><div id="ui"><div class="hud"><span>â­ <b id="s">0</b></span><span>ðª <b id="m">0</b></span><span>ð <b id="b">0</b></span></div>
<div id="start" class="screen"><div class="card"><h1>NEON RUSH</h1><p>Drag your finger to fly. Dodge the red blocks and collect coins. Survive as long as you can.</p><button id="play">PLAY</button><p style="font-size:12px">iPhone prototype â¢ Free to play</p></div></div>
<div id="over" class="screen"><div class="card"><h1>CRASH!</h1><p>Score: <b id="fs">0</b><br>Coins: <b id="fm">0</b></p><button id="again">PLAY AGAIN</button></div></div></div>
<script>
const c=document.getElementById('c'),ctx=c.getContext('2d');let W,H,dpr,P,O,C,score,mc,run=false,last=0,timer=0,best=+localStorage.nrBest||0;
document.getElementById('b').textContent=best;
function size(){dpr=Math.min(2,devicePixelRatio||1);W=innerWidth;H=innerHeight;c.width=W*dpr;c.height=H*dpr;ctx.setTransform(dpr,0,0,dpr,0,0)}addEventListener('resize',size);size();
function start(){P={x:W/2,y:H-100,r:18};O=[];C=[];score=mc=0;timer=0;run=true;document.getElementById('start').style.display='none';document.getElementById('over').style.display='none';last=performance.now();requestAnimationFrame(loop)}
function move(x,y){if(!run)return;P.x=Math.max(25,Math.min(W-25,x));P.y=Math.max(H*.42,Math.min(H-35,y))}
c.addEventListener('pointermove',e=>move(e.clientX,e.clientY));c.addEventListener('pointerdown',e=>move(e.clientX,e.clientY));
function loop(now){if(!run)return;let dt=Math.min(.035,(now-last)/1000);last=now;score+=dt*10;timer-=dt;if(timer<=0){let w=28+Math.random()*52;O.push({x:Math.random()*(W-w),y:-70,w,h:28+Math.random()*55,v:170+score*.65});if(Math.random()<.65)C.push({x:18+Math.random()*(W-36),y:-15,v:145+score*.35});timer=Math.max(.16,.62-score/360)}
for(const o of O)o.y+=o.v*dt;for(const q of C)q.y+=q.v*dt;
for(const o of O){if(P.x+P.r>o.x&&P.x-P.r<o.x+o.w&&P.y+P.r>o.y&&P.y-P.r<o.y+o.h){end();return}}
for(let i=C.length-1;i>=0;i--){let q=C[i],dx=P.x-q.x,dy=P.y-q.y;if(dx*dx+dy*dy<900){mc++;C.splice(i,1)}}O=O.filter(o=>o.y<H+90);C=C.filter(q=>q.y<H+30);
document.getElementById('s').textContent=Math.floor(score);document.getElementById('m').textContent=mc;draw();requestAnimationFrame(loop)}
function draw(){let g=ctx.createLinearGradient(0,0,0,H);g.addColorStop(0,'#080b1c');g.addColorStop(1,'#142040');ctx.fillStyle=g;ctx.fillRect(0,0,W,H);
ctx.fillStyle='rgba(120,220,255,.2)';for(let i=0;i<42;i++){let x=(i*83)%W,y=(i*137+score*9)%H;ctx.fillRect(x,y,2,2)}
for(const o of O){ctx.fillStyle='#ff3b66';ctx.shadowBlur=18;ctx.shadowColor='#ff3b66';ctx.fillRect(o.x,o.y,o.w,o.h)}ctx.shadowBlur=0;
for(const q of C){ctx.fillStyle='#ffe45c';ctx.beginPath();ctx.arc(q.x,q.y,9,0,7);ctx.fill()}
ctx.fillStyle='#5df';ctx.shadowBlur=22;ctx.shadowColor='#5df';ctx.beginPath();ctx.moveTo(P.x,P.y-25);ctx.lineTo(P.x-21,P.y+18);ctx.lineTo(P.x,P.y+10);ctx.lineTo(P.x+21,P.y+18);ctx.closePath();ctx.fill();ctx.shadowBlur=0}
function end(){run=false;let s=Math.floor(score);if(s>best){best=s;localStorage.nrBest=best;document.getElementById('b').textContent=best}document.getElementById('fs').textContent=s;document.getElementById('fm').textContent=mc;document.getElementById('over').style.display='flex'}
play.onclick=start;again.onclick=start;
</script></body></html>'''
(out/"index.html").write_text(html)
(out/"README.txt").write_text("""NEON RUSH â iPhone-ready web game prototype.
On iPhone 15: open index.html in Safari after putting the file on a web host.
Then Safari Share -> Add to Home Screen.
This prototype is free to test and does not require an app-store account.
""")
zip_path=Path("/mnt/data/NeonRush_iPhone.zip")
with zipfile.ZipFile(zip_path,"w",zipfile.ZIP_DEFLATED) as z:
    for p in out.iterdir(): z.write(p,p.name)
print(zip_path)