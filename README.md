<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no"/>
<meta name="theme-color" content="#0A0B0F"/>
<title>Redtrabajo — Bolivia's Job Platform</title>
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ctext y='.9em' font-size='90'%3E🏗️%3C/text%3E%3C/svg%3E"/>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap" rel="stylesheet"/>
<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent;margin:0;padding:0}
html,body{background:#0A0B0F;color:#F0EDE6;font-family:'Inter',system-ui,sans-serif;min-height:100vh;overscroll-behavior:none}
#root{min-height:100vh}
input,textarea,select,button{font-family:'Inter',system-ui,sans-serif}
::-webkit-scrollbar{width:4px}
::-webkit-scrollbar-thumb{background:#2a2d3a;border-radius:4px}
@keyframes fadeIn{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:translateY(0)}}
@keyframes bounce{0%{transform:translate(-50%,-120%);opacity:0}60%{transform:translate(-50%,-95%)}100%{transform:translate(-50%,-100%);opacity:1}}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:0.5}}
.fade-in{animation:fadeIn 0.3s ease forwards}
.pulse{animation:pulse 2s infinite}
</style>
<script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
<div id="root"></div>
<script type="text/babel" data-type="module">
const {useState,useRef,useEffect}=React;
const T={bg:"#0A0B0F",surface:"#12141A",card:"#1A1D26",border:"#252836",accent:"#F5A623",accentDark:"#D4891E",blue:"#4F8EF7",green:"#2DD4A0",red:"#F75A5A",purple:"#A78BFA",text:"#F0EDE6",sub:"#8B8FA8",faint:"#3A3D4D",ADMIN_PW:"Jorge"};
const DB_KEY="redtrabajo_alpha_v2";
const db={
_load(){try{return JSON.parse(localStorage.getItem(DB_KEY)||"{}");}catch{return{};}},
_save(data){try{localStorage.setItem(DB_KEY,JSON.stringify(data));}catch{}},
addDoc(collection,data){const store=this._load();if(!store[collection])store[collection]=[];const doc={...data,id:"doc_"+Date.now()+"_"+Math.random().toString(36).slice(2),createdAt:new Date().toISOString()};store[collection].push(doc);this._save(store);return doc;},
updateDoc(collection,id,updates){const store=this._load();if(!store[collection])return;store[collection]=store[collection].map(d=>d.id===id?{...d,...updates,updatedAt:new Date().toISOString()}:d);this._save(store);},
getDocs(collection){const store=this._load();return store[collection]||[];},
getWhere(collection,field,value){return this.getDocs(collection).filter(d=>d[field]===value);},
reset(){localStorage.removeItem(DB_KEY);this._seedDefaults();},
_seedDefaults(){const workers=this.getDocs("workers");if(workers.length===0){[{ci:"7823401",name:"Carlos Mamani",skill:"Electrician",exp:"5 years",city:"La Paz",phone:"71234567",available:true,lat:50,lng:40},{ci:"5612890",name:"Ana Flores",skill:"Cook",exp:"3 years",city:"Cochabamba",phone:"76543210",available:true,lat:60,lng:55},{ci:"9034512",name:"Pedro Quispe",skill:"Gardener",exp:"8 years",city:"Santa Cruz",phone:"78901234",available:true,lat:70,lng:30},{ci:"4128763",name:"Maria Condori",skill:"Cleaner",exp:"2 years",city:"La Paz",phone:"72345678",available:true,lat:45,lng:50},{ci:"3301299",name:"Juan Vargas",skill:"Plumber",exp:"6 years",city:"La Paz",phone:"73456789",available:true,lat:55,lng:45}].forEach(w=>this.addDoc("workers",w));}}};
db._seedDefaults();
const Avatar=({name,size=40})=>{const l=name.split(" ").map(w=>w[0]).join("").slice(0,2).toUpperCase();const h=[...name].reduce((a,c)=>a+c.charCodeAt(0),0)%360;return<div style={{width:size,height:size,borderRadius:"50%",background:`hsl(${h},45%,28%)`,border:`2px solid hsl(${h},45%,42%)`,display:"flex",alignItems:"center",justifyContent:"center",color:"#fff",fontWeight:800,fontSize:size*0.34,flexShrink:0}}>{l}</div>;};
const Badge=({children,color=T.accent})=>(<span style={{background:color+"20",color,border:`1px solid ${color}35`,borderRadius:20,padding:"3px 10px",fontSize:11,fontWeight:700,letterSpacing:0.4,textTransform:"uppercase",whiteSpace:"nowrap"}}>{children}</span>);
const Card=({children,style={},onClick,glow})=>(<div onClick={onClick} className="fade-in" style={{background:T.card,border:`1px solid ${glow?T.accent+"55":T.border}`,borderRadius:16,padding:"16px 18px",cursor:onClick?"pointer":"default",transition:"all 0.2s",...style}} onMouseEnter={e=>onClick&&(e.currentTarget.style.borderColor=T.accent+"66")} onMouseLeave={e=>onClick&&(e.currentTarget.style.borderColor=glow?T.accent+"55":T.border)}>{children}</div>);
const Btn=({children,onClick,variant="primary",small,full,disabled,loading})=>{const styles={primary:{background:`linear-gradient(135deg,${T.accent},${T.accentDark})`,color:"#0A0B0F",border:"none"},outline:{background:"transparent",color:T.accent,border:`1.5px solid ${T.accent}`},ghost:{background:"transparent",color:T.sub,border:`1.5px solid ${T.border}`},danger:{background:T.red,color:"#fff",border:"none"},green:{background:`linear-gradient(135deg,${T.green},#22b087)`,color:"#0A0B0F",border:"none"},blue:{background:`linear-gradient(135deg,${T.blue},#3a70d4)`,color:"#fff",border:"none"}};return<button onClick={onClick} disabled={disabled||loading} style={{...styles[variant],borderRadius:12,padding:small?"7px 16px":"13px 22px",fontWeight:700,fontSize:small?12:14,cursor:(disabled||loading)?"not-allowed":"pointer",opacity:(disabled||loading)?0.55:1,width:full?"100%":"auto",letterSpacing:0.2,transition:"all 0.2s",display:"inline-flex",alignItems:"center",justifyContent:"center",gap:6}}>{loading?"⏳ Loading...":children}</button>;};
const Input=({label,placeholder,value,onChange,type="text",icon,error,hint})=>(<div style={{display:"flex",flexDirection:"column",gap:5}}>{label&&<label style={{fontSize:11,fontWeight:700,color:T.sub,letterSpacing:0.6,textTransform:"uppercase"}}>{label}</label>}<div style={{position:"relative"}}>{icon&&<span style={{position:"absolute",left:13,top:"50%",transform:"translateY(-50%)",fontSize:16}}>{icon}</span>}<input type={type} placeholder={placeholder} value={value} onChange={onChange} style={{width:"100%",background:"#0D0F18",border:`1.5px solid ${error?T.red:T.border}`,borderRadius:12,color:T.text,padding:icon?"11px 14px 11px 40px":"11px 14px",fontSize:14,outline:"none"}} onFocus={e=>e.target.style.borderColor=error?T.red:T.accent} onBlur={e=>e.target.style.borderColor=error?T.red:T.border}/></div>{error&&<span style={{color:T.red,fontSize:12}}>{error}</span>}{hint&&!error&&<span style={{color:T.sub,fontSize:11}}>{hint}</span>}</div>);
const Logo=({size=22})=>(<div style={{display:"flex",alignItems:"center",gap:8}}><span style={{fontSize:size*1.4}}>🏗️</span><span style={{fontWeight:900,fontSize:size,letterSpacing:-0.8}}><span style={{color:T.accent}}>Red</span><span style={{color:T.text}}>trabajo</span></span></div>);
const Back=({onClick,label="Back"})=>(<button onClick={onClick} style={{background:"none",border:"none",color:T.sub,fontSize:13,cursor:"pointer",padding:"0 0 18px 0",display:"flex",alignItems:"center",gap:5}}><span style={{fontSize:18}}>‹</span>{label}</button>);
const Divider=({label})=>(<div style={{display:"flex",alignItems:"center",gap:10,margin:"4px 0"}}><div style={{flex:1,height:1,background:T.border}}/>{label&&<span style={{fontSize:11,color:T.faint,fontWeight:600}}>{label}</span>}<div style={{flex:1,height:1,background:T.border}}/></div>);
const StatusDot=({active})=>(<span className={active?"pulse":""} style={{display:"inline-block",width:8,height:8,borderRadius:"50%",background:active?T.green:T.faint,marginRight:5}}/>);
