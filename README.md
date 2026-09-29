<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Hill equation binding explorer</title>
<script>window.MathJax={tex:{inlineMath:[['$','$']]},svg:{fontCache:'none'},startup:{typeset:false}};</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-svg.min.js"></script>
<style>
:root{box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px);
--bg:#f7f8f6;--panel:#fff;--ink:#1d2624;--mute:#5d6b67;--line:#d5dbd8;--enz:#1f6f78;--ref:#a3a9a7;--guide:#8a8f8d;}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#141a19;--panel:#1c2423;--ink:#e7eeec;--mute:#9aa8a4;--line:#33403d;--enz:#5bc0cb;--ref:#7a8582;--guide:#8b9491;}}
:root[data-theme="dark"]{--bg:#141a19;--panel:#1c2423;--ink:#e7eeec;--mute:#9aa8a4;--line:#33403d;--enz:#5bc0cb;--ref:#7a8582;--guide:#8b9491;}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*,*::before,*::after{box-sizing:inherit}
body{margin:0;background:var(--bg);color:var(--ink);font:16px/1.5 "Source Serif 4",Georgia,serif}
main{max-width:1240px;margin:0 auto;padding:20px 16px 32px}
h1{font-size:1.5rem;margin:0 0 4px;font-weight:600}
.sub{color:var(--mute);margin:0 0 16px;font-size:.95rem}
.card{background:var(--panel);border:1px solid var(--line);border-radius:8px;padding:12px;margin-bottom:14px}
.wrap{position:relative}
.plots{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:14px}
.plots .card{margin-bottom:0}
@media (max-width:820px){.plots{grid-template-columns:1fr}}
.cap{font-size:.95rem;color:var(--mute);margin:0 0 6px;font-weight:600}
svg.plot{width:100%;height:auto;display:block}
#lab{position:absolute;inset:0;pointer-events:none}
.lb{position:absolute;white-space:nowrap;line-height:1;font-size:14px;color:var(--mute)}
.lb.e{color:var(--enz)}
mjx-container{margin:0!important}
.row{display:grid;grid-template-columns:4rem 1fr 6rem;align-items:center;gap:8px;margin:8px 0;font-size:.95rem}
.row output{text-align:right;color:var(--mute)}
input[type=range]{width:100%;accent-color:var(--enz)}
.tog{display:flex;align-items:center;gap:8px;font-family:system-ui,sans-serif;font-size:.92rem;margin-top:10px}
.tog input:focus-visible,input[type=range]:focus-visible{outline:2px solid var(--ink);outline-offset:2px}
.read{font-size:.9rem;color:var(--mute);margin:8px 0 0;overflow-x:auto}
.note{font-size:.9rem;color:var(--mute);margin:6px 0 0}
</style>
</head>
<body>
<main>
<h1>Cooperative binding: the Hill equation</h1>
<p class="sub">$Y_S = \dfrac{[\mathrm{S}]^n}{K_d^{\,n} + [\mathrm{S}]^n}$. Adjust $K_d$ to slide the curve along the substrate axis and $n$ to change how steep it is.</p>

<div class="plots">
<div class="card"><p class="cap">Saturation curve</p><div class="wrap"><svg id="plot" class="plot" viewBox="0 0 700 400" role="img" aria-label="Fractional saturation versus substrate concentration"></svg><div id="lab"></div></div></div>
<div class="card"><p class="cap">Hill plot (linearized form)</p><div class="wrap"><svg id="plot2" class="plot" viewBox="0 0 700 400" role="img" aria-label="Hill plot of log of Y over 1 minus Y versus log substrate concentration"></svg><div id="lab2"></div></div><p class="read" id="read2"></p></div>
</div>

<div class="card">
<div class="row"><label for="kd">$K_d$</label><input id="kd" type="range" min="0.5" max="10" step="0.1" value="4"><output id="kdO"></output></div>
<div class="row"><label for="n">$n$</label><input id="n" type="range" min="1" max="8" step="0.1" value="3"><output id="nO"></output></div>
<div class="tog"><input id="ref" type="checkbox"><label for="ref">Show non-cooperative reference ($n = 1$, same $K_d$)</label></div>
<div class="tog"><input id="band" type="checkbox" checked><label for="band">Shade the $10\% \to 90\%$ saturation window (both plots)</label></div>
<p class="read" id="read"></p>
</div>
<p class="note">Here $K_d$ is the substrate concentration at half saturation, so the dashed guides always meet the curve at $(K_d,\ 0.5)$. The Hill coefficient $n$ is a measure of cooperativity: $n = 1$ gives a hyperbola, $n > 1$ gives a sigmoid, and $n$ cannot exceed the actual number of binding sites. The shaded band spans $[\mathrm{S}]_{10}$ to $[\mathrm{S}]_{90}$, where $[\mathrm{S}]_{x} = K_d\left(\dfrac{x}{1-x}\right)^{1/n}$.</p>
</main>

<script>
(function(){
var $=function(i){return document.getElementById(i)};
var W=700,H=400,L=56,R=20,T=16,B=48,XM=20,YM=1.05;
var sx=function(x){return L+x/XM*(W-L-R)},sy=function(y){return H-B-y/YM*(H-B-T)};
var cache={},Ls={a:'',b:''},tg='a';
function tex(s){if(!cache[s])cache[s]=MathJax.tex2svg(s).outerHTML;return cache[s]}
function lab(x,y,a,s,cls,rot){
 Ls[tg]+='<span class="lb '+(cls||'')+'" style="left:'+(x/W*100).toFixed(2)+'%;top:'+(y/H*100).toFixed(2)+'%;transform:translate('+(a==='e'?'-100%':a==='m'?'-50%':'0')+',-50%)'+(rot?' rotate(-90deg)':'')+'">'+tex(s)+'</span>'}
function th(k,n,x){var a=Math.pow(x,n);return a/(Math.pow(k,n)+a)}
function line(x1,y1,x2,y2,c,w,d){return '<line x1="'+sx(x1)+'" y1="'+sy(y1)+'" x2="'+sx(x2)+'" y2="'+sy(y2)+'" stroke="'+c+'" stroke-width="'+w+'" stroke-dasharray="'+d+'"/>'}
function curve(k,n,c,w,d){var p='';for(var i=0;i<=300;i++){var x=XM*i/300;p+=(i?'L':'M')+sx(x).toFixed(1)+','+sy(th(k,n,x)).toFixed(1)}return '<path d="'+p+'" fill="none" stroke="'+c+'" stroke-width="'+w+'" stroke-dasharray="'+(d||'')+'"/>'}
function fm(q,d){return (Math.abs(q)<1e-9?0:q).toFixed(d)}
// Hill plot: log10(Y/(1-Y)) vs log10[S]; slope = n, x-intercept = log10(Kd)
function draw2(k,n,bd,ref){
 tg='b';
 var s='',L2=64,X0=-1,X1=1.5,Y0=-2,Y1=2,lk=Math.log10(k),h9=Math.log10(9)/n,i;
 var X=function(x){return L2+(x-X0)/(X1-X0)*(W-L2-R)},Y=function(y){return H-B-(y-Y0)/(Y1-Y0)*(H-B-T)};
 var ln=function(x1,y1,x2,y2,c,w,d,cl){return '<line x1="'+X(x1)+'" y1="'+Y(y1)+'" x2="'+X(x2)+'" y2="'+Y(y2)+'" stroke="'+c+'" stroke-width="'+w+'" stroke-dasharray="'+d+'"'+(cl?' clip-path="url(#c2)"':'')+'/>'};
 s+='<defs><clipPath id="c2"><rect x="'+L2+'" y="'+T+'" width="'+(W-L2-R)+'" height="'+(H-B-T)+'"/></clipPath></defs>';
 for(i=-1;i<=1.5;i+=.5)lab(X(i),H-B+16,'m',fm(i,1));
 for(i=-2;i<=2;i++){s+='<line x1="'+L2+'" y1="'+Y(i)+'" x2="'+(W-R)+'" y2="'+Y(i)+'" stroke="var(--line)" stroke-width=".6"/>';lab(L2-8,Y(i),'e',fm(i,0))}
 s+='<line x1="'+L2+'" y1="'+(H-B)+'" x2="'+(W-R)+'" y2="'+(H-B)+'" stroke="var(--mute)" stroke-width="1"/>';
 s+='<line x1="'+L2+'" y1="'+T+'" x2="'+L2+'" y2="'+(H-B)+'" stroke="var(--mute)" stroke-width="1"/>';
 lab(L2+(W-L2-R)/2,H-10,'m','\\log_{10}\\left([\\mathrm{S}]/\\mu\\mathrm{M}\\right)');
 lab(12,T+(H-B-T)/2,'m','\\log_{10}\\dfrac{Y_S}{1-Y_S}','',true);
 if(bd){
  var xa=Math.max(lk-h9,X0),xb=Math.min(lk+h9,X1);
  s+='<rect x="'+X(xa)+'" y="'+T+'" width="'+(X(xb)-X(xa))+'" height="'+(H-B-T)+'" fill="var(--enz)" opacity=".14"/>';
  lab(X((xa+xb)/2),T+11,'m','10\\%\\to 90\\%:\\ \\Delta\\log_{10}[\\mathrm{S}]='+(2*h9).toFixed(2),'e');
 }
 var g='var(--guide)';
 s+=ln(X0,0,lk,0,g,1.2,'6 4')+ln(lk,0,lk,Y0,g,1.2,'6 4');
 lab(L2+16,Y(0)-12,'s','Y_S=\\tfrac{1}{2}');
 lab(X(lk)+8,H-B-14,'s','\\log_{10}K_d='+lk.toFixed(2));
 if(ref)s+=ln(X0,X0-lk,X1,X1-lk,'var(--ref)',2,'2 4',1);
 s+=ln(X0,n*(X0-lk),X1,n*(X1-lk),'var(--enz)',2.6,'',1);
 s+='<circle cx="'+X(lk)+'" cy="'+Y(0)+'" r="3.5" fill="var(--enz)"/>';
 if(bd)s+='<circle cx="'+X(lk-h9)+'" cy="'+Y(-Math.log10(9))+'" r="3.5" fill="var(--enz)"/><circle cx="'+X(lk+h9)+'" cy="'+Y(Math.log10(9))+'" r="3.5" fill="var(--enz)"/>';
 var yv=1.5,xv=lk+yv/n;if(xv>X1-.3){xv=X1-.3;yv=n*(xv-lk)}
 lab(X(xv)-10,Y(yv)-8,'e','\\text{slope}=n='+n.toFixed(1),'e');
 $('read2').innerHTML=tex('\\text{slope}=n='+n.toFixed(1)+',\\ \\ x\\text{-intercept}=\\log_{10}K_d='+lk.toFixed(2)+(bd?',\\ \\ \\Delta\\log_{10}[\\mathrm{S}]_{10\\to90}=\\dfrac{2\\log_{10}9}{n}='+(2*h9).toFixed(2):''));
 $('plot2').innerHTML=s;$('lab2').innerHTML=Ls.b}
function draw(){
 Ls={a:'',b:''};tg='a';
 var k=+$('kd').value,n=+$('n').value,s='',i;
 $('kdO').innerHTML=tex(k.toFixed(1)+'\\ \\mu\\mathrm{M}');$('nO').innerHTML=tex(n.toFixed(1));
 for(i=0;i<=XM;i+=4)lab(sx(i),H-B+16,'m',String(i));
 for(i=0;i<=10;i+=2){var y=i/10;s+='<line x1="'+L+'" y1="'+sy(y)+'" x2="'+(W-R)+'" y2="'+sy(y)+'" stroke="var(--line)" stroke-width=".6"/>';lab(L-8,sy(y),'e',y.toFixed(1))}
 s+=line(0,0,XM,0,'var(--mute)',1,'')+line(0,0,0,YM,'var(--mute)',1,'');
 lab(L+(W-L-R)/2,H-10,'m','[\\mathrm{S}]\\ (\\mu\\mathrm{M})');
 lab(14,T+(H-B-T)/2,'m','\\text{Fractional saturation, }Y_S','',true);
 var lo=k*Math.pow(1/9,1/n),hi=k*Math.pow(9,1/n),xe=Math.min(hi,XM),mid=(lo+xe)/2,bd=$('band').checked;
 if(bd){ s+='<rect x="'+sx(lo)+'" y="'+T+'" width="'+(sx(xe)-sx(lo))+'" height="'+(H-B-T)+'" fill="var(--enz)" opacity=".14"/>';
 s+=line(lo,.1,lo,0,'var(--enz)',1,'2 3')+line(xe,th(k,n,xe),xe,0,'var(--enz)',1,'2 3');
 lab(sx(mid),T+11,'m','10\\%\\to 90\\%:\\ \\Delta[\\mathrm{S}]='+(hi-lo).toFixed(2)+'\\ \\mu\\mathrm{M}'+(hi>XM?'\\ (\\text{band clipped at axis})':''),'e')}
 var g='var(--guide)';
 s+=line(0,1,XM,1,g,1.2,'6 4')+line(0,.5,k,.5,g,1.2,'6 4')+line(k,.5,k,0,g,1.2,'6 4');
 if(bd&&sx(mid)>W/2)lab(L+6,sy(1)-10,'s','Y_S=1');else lab(W-R-4,sy(1)-10,'e','Y_S=1');
 lab(L+16,sy(.5)-12,'s','Y_S=\\tfrac{1}{2}');
 lab(sx(k)+8,H-B-22,'s','K_d='+k.toFixed(1)+'\\ \\mu\\mathrm{M}');
 if($('ref').checked)s+=curve(k,1,'var(--ref)',2,'2 4');
 s+=curve(k,n,'var(--enz)',2.6);
 s+='<circle cx="'+sx(k)+'" cy="'+sy(.5)+'" r="3.5" fill="var(--enz)"/>';
 $('read').innerHTML=tex('\\left.\\dfrac{dY_S}{d[\\mathrm{S}]}\\right|_{K_d}=\\dfrac{n}{4K_d}='+(n/(4*k)).toFixed(3)+'\\ \\mu\\mathrm{M}^{-1}'+(bd?';\\quad [\\mathrm{S}]_{10}='+lo.toFixed(2)+',\\ [\\mathrm{S}]_{90}='+hi.toFixed(2)+'\\ \\mu\\mathrm{M};\\quad \\Delta[\\mathrm{S}]=[\\mathrm{S}]_{90}-[\\mathrm{S}]_{10}='+(hi-lo).toFixed(2)+'\\ \\mu\\mathrm{M};\\quad \\dfrac{[\\mathrm{S}]_{90}}{[\\mathrm{S}]_{10}}='+(hi/lo).toFixed(1):''));
 $('plot').innerHTML=s;$('lab').innerHTML=Ls.a;draw2(k,n,bd,$('ref').checked)}
MathJax.startup.promise.then(function(){return MathJax.typesetPromise()}).then(function(){
 document.querySelectorAll('input').forEach(function(e){e.addEventListener('input',draw)});
 draw()});
})();
</script>
</body>
</html>
