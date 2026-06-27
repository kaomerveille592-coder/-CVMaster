<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>CVcraftPro — Créateur de CV Professionnel</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.23.2/babel.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700;900&family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet"/>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{background:#0F0F1A;color:#fff;font-family:'Inter',sans-serif;min-height:100vh}
  ::-webkit-scrollbar{width:5px}::-webkit-scrollbar-track{background:#0F0F1A}::-webkit-scrollbar-thumb{background:#6C63FF44;border-radius:3px}
  @keyframes spin{to{transform:rotate(360deg)}}
  @keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-8px)}}
  @keyframes fadeUp{from{opacity:0;transform:translateY(24px)}to{opacity:1;transform:translateY(0)}}
  @keyframes pulse{0%,100%{opacity:1}50%{opacity:0.6}}
  input,button{font-family:'Inter',sans-serif}
  .fade-up{animation:fadeUp 0.6s ease both}
  .float{animation:float 4s ease-in-out infinite}
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">
const {useState,useRef,useEffect}=React;

const PLANS=[
  {id:"starter",name:"Starter",price:"0€",period:"/mois",color:"#6C63FF",badge:null,features:[
    {ok:true,text:"1 CV basique"},{ok:true,text:"3 templates modernes"},{ok:true,text:"Téléchargement PDF"},
    {ok:false,text:"Photo de profil"},{ok:false,text:"Traduction multilingue"},{ok:false,text:"Templates premium"},{ok:false,text:"Export Word"}
  ]},
  {id:"pro",name:"Pro",price:"9,90€",period:"/mois",color:"#00D4AA",badge:"POPULAIRE",features:[
    {ok:true,text:"CV illimités"},{ok:true,text:"15 templates premium"},{ok:true,text:"Photo de profil"},
    {ok:true,text:"PDF + Word"},{ok:true,text:"Traduction EN, DE, ES, IT"},{ok:false,text:"IA rédactrice"},{ok:false,text:"Signature numérique"}
  ]},
  {id:"elite",name:"Élite",price:"19,90€",period:"/mois",color:"#FFD700",badge:"TOUT INCLUS",features:[
    {ok:true,text:"CV illimités"},{ok:true,text:"Tous les templates"},{ok:true,text:"Photo HD"},
    {ok:true,text:"PDF + Word + JSON"},{ok:true,text:"10+ langues"},{ok:true,text:"IA rédactrice"},{ok:true,text:"Signature numérique"}
  ]}
];

const LANGUAGES=[
  {code:"fr",label:"Français",flag:"🇫🇷"},{code:"en",label:"English",flag:"🇬🇧"},
  {code:"de",label:"Deutsch",flag:"🇩🇪"},{code:"es",label:"Español",flag:"🇪🇸"},
  {code:"it",label:"Italiano",flag:"🇮🇹"},{code:"pt",label:"Português",flag:"🇵🇹"},
  {code:"nl",label:"Nederlands",flag:"🇳🇱"},{code:"ar",label:"العربية",flag:"🇸🇦"}
];

const TEMPLATES=[
  {id:"modern",name:"Modern",accent:"#6C63FF"},
  {id:"clean",name:"Clean",accent:"#00D4AA"},
  {id:"bold",name:"Bold",accent:"#FF6B6B"},
  {id:"elegant",name:"Élégant",accent:"#FFD700"}
];

const T={
  fr:{title:"Développeur Full Stack",exp:"Expérience",edu:"Formation",skills:"Compétences",summary:"Passionné par la technologie et l'innovation."},
  en:{title:"Full Stack Developer",exp:"Experience",edu:"Education",skills:"Skills",summary:"Passionate about technology and innovation."},
  de:{title:"Full-Stack-Entwickler",exp:"Erfahrung",edu:"Ausbildung",skills:"Fähigkeiten",summary:"Leidenschaft für Technologie und Innovation."},
  es:{title:"Desarrollador Full Stack",exp:"Experiencia",edu:"Educación",skills:"Habilidades",summary:"Apasionado por la tecnología e innovación."},
  it:{title:"Sviluppatore Full Stack",exp:"Esperienza",edu:"Formazione",skills:"Competenze",summary:"Appassionato di tecnologia e innovazione."},
  pt:{title:"Desenvolvedor Full Stack",exp:"Experiência",edu:"Educação",skills:"Habilidades",summary:"Apaixonado por tecnologia e inovação."},
  nl:{title:"Full Stack Ontwikkelaar",exp:"Ervaring",edu:"Opleiding",skills:"Vaardigheden",summary:"Gepassioneerd door technologie en innovatie."},
  ar:{title:"مطور فول ستاك",exp:"الخبرة",edu:"التعليم",skills:"المهارات",summary:"شغوف بالتكنولوجيا والابتكار."}
};

const inp={display:"block",width:"100%",background:"#0A0A18",border:"1px solid rgba(108,99,255,0.3)",borderRadius:10,padding:"12px 14px",color:"#fff",fontSize:14,marginBottom:12,outline:"none",boxSizing:"border-box"};

function CVPreview({data,template,lang,compact}){
  const t=T[lang]||T.fr;
  const acc=TEMPLATES.find(x=>x.id===template)?.accent||"#6C63FF";
  const s=compact?0.55:1;
  return(
    <div style={{background:"#fff",borderRadius:compact?8:14,overflow:"hidden",fontFamily:"Inter,sans-serif",
      fontSize:compact?7:11,boxShadow:compact?"0 4px 20px rgba(0,0,0,0.4)":"0 20px 60px rgba(0,0,0,0.5)",
      width:compact?170:300,flexShrink:0}}>
      <div style={{background:acc,padding:compact?"10px 12px":"20px 24px",display:"flex",alignItems:"center",gap:compact?6:14}}>
        {data.photo?(
          <img src={data.photo} alt="" style={{width:compact?28:56,height:compact?28:56,borderRadius:"50%",border:"2px solid rgba(255,255,255,0.6)",objectFit:"cover",flexShrink:0}}/>
        ):(
          <div style={{width:compact?28:56,height:compact?28:56,borderRadius:"50%",background:"rgba(255,255,255,0.25)",
            display:"flex",alignItems:"center",justifyContent:"center",color:"#fff",fontWeight:700,
            fontSize:compact?10:20,flexShrink:0}}>
            {(data.firstName||"J")[0]}{(data.lastName||"D")[0]}
          </div>
        )}
        <div>
          <div style={{color:"#fff",fontWeight:700,fontSize:compact?8:15}}>{data.firstName||"Jean"} {data.lastName||"Dupont"}</div>
          <div style={{color:"rgba(255,255,255,0.85)",fontSize:compact?6:10,marginTop:2}}>{t.title}</div>
          {!compact&&<div style={{color:"rgba(255,255,255,0.7)",fontSize:9,marginTop:3}}>{data.email||"jean@email.com"}</div>}
        </div>
      </div>
      <div style={{padding:compact?"8px 10px":"18px 20px",display:"grid",gridTemplateColumns:"1fr 1fr",gap:compact?5:12}}>
        <div>
          <div style={{color:acc,fontWeight:700,fontSize:compact?5:8,textTransform:"uppercase",letterSpacing:1,marginBottom:compact?3:6}}>{t.exp}</div>
          {[{r:"Ingénieur Web",c:"TechCorp",y:"2022–Auj."},{r:"Dev Junior",c:"StartupXY",y:"2020–22"}].map((e,i)=>(
            <div key={i} style={{marginBottom:compact?3:6}}>
              <div style={{fontWeight:600,color:"#1a1a2e",fontSize:compact?5:9}}>{e.r}</div>
              <div style={{color:"#666",fontSize:compact?4:8}}>{e.c} · {e.y}</div>
            </div>
          ))}
          {!compact&&<>
            <div style={{color:acc,fontWeight:700,fontSize:8,textTransform:"uppercase",letterSpacing:1,margin:"10px 0 6px"}}>{t.edu}</div>
            <div style={{fontWeight:600,color:"#1a1a2e",fontSize:9}}>Master Informatique</div>
            <div style={{color:"#666",fontSize:8}}>EPFL · 2018–2020</div>
          </>}
        </div>
        <div>
          <div style={{color:acc,fontWeight:700,fontSize:compact?5:8,textTransform:"uppercase",letterSpacing:1,marginBottom:compact?3:6}}>{t.skills}</div>
          {["React","Node.js","Python","SQL","Docker"].slice(0,compact?3:5).map((sk,i)=>(
            <div key={sk} style={{marginBottom:compact?2:5}}>
              <div style={{fontSize:compact?5:8,color:"#333",marginBottom:1}}>{sk}</div>
              <div style={{height:compact?2:3,background:"#eee",borderRadius:2}}>
                <div style={{height:"100%",background:acc,borderRadius:2,width:["90%","85%","75%","80%","70%"][i]}}/>
              </div>
            </div>
          ))}
        </div>
      </div>
      {!compact&&<div style={{margin:"0 20px 16px",padding:"10px 0 0",borderTop:`2px solid ${acc}20`,color:"#666",fontSize:9,fontStyle:"italic"}}>{t.summary}</div>}
    </div>
  );
}

function AuthModal({mode,onClose,onSuccess}){
  const [tab,setTab]=useState(mode||"login");
  const [form,setForm]=useState({email:"",password:"",name:""});
  const [loading,setLoading]=useState(false);
  const [err,setErr]=useState("");
  const submit=async()=>{
    if(!form.email||!form.password)return setErr("Remplis tous les champs.");
    setLoading(true);setErr("");
    await new Promise(r=>setTimeout(r,900));
    setLoading(false);
    onSuccess({name:form.name||form.email.split("@")[0],email:form.email});
  };
  return(
    <div onClick={e=>e.target===e.currentTarget&&onClose()} style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.75)",zIndex:1000,display:"flex",alignItems:"center",justifyContent:"center",backdropFilter:"blur(8px)",padding:16}}>
      <div style={{background:"#1A1A2E",border:"1px solid rgba(108,99,255,0.3)",borderRadius:20,padding:"36px 28px",width:"100%",maxWidth:380,position:"relative"}}>
        <button onClick={onClose} style={{position:"absolute",top:14,right:18,background:"none",border:"none",color:"#888",fontSize:24,cursor:"pointer"}}>×</button>
        <div style={{display:"flex",background:"#0A0A18",borderRadius:10,marginBottom:24,overflow:"hidden"}}>
          {["login","register"].map(t=>(
            <button key={t} onClick={()=>setTab(t)} style={{flex:1,padding:"10px 0",background:tab===t?"#6C63FF":"transparent",color:tab===t?"#fff":"#888",border:"none",cursor:"pointer",fontSize:14,fontWeight:600}}>
              {t==="login"?"Connexion":"Inscription"}
            </button>
          ))}
        </div>
        <div style={{fontFamily:"'Playfair Display',serif",fontSize:24,fontWeight:700,marginBottom:6}}>{tab==="login"?"Bon retour 👋":"Créer un compte"}</div>
        <div style={{color:"#888",fontSize:13,marginBottom:22}}>{tab==="login"?"Accède à tes CVs sauvegardés.":"Rejoins 50 000 professionnels."}</div>
        {tab==="register"&&<input value={form.name} onChange={e=>setForm({...form,name:e.target.value})} placeholder="Ton prénom" style={inp}/>}
        <input value={form.email} onChange={e=>setForm({...form,email:e.target.value})} placeholder="Email" type="email" style={inp}/>
        <input value={form.password} onChange={e=>setForm({...form,password:e.target.value})} placeholder="Mot de passe" type="password" style={inp}/>
        {err&&<div style={{color:"#FF6B6B",fontSize:13,marginBottom:10}}>{err}</div>}
        <button onClick={submit} disabled={loading} style={{width:"100%",padding:14,background:"linear-gradient(135deg,#6C63FF,#00D4AA)",border:"none",borderRadius:10,color:"#fff",fontWeight:700,fontSize:15,cursor:"pointer",opacity:loading?0.7:1}}>
          {loading?"Chargement…":tab==="login"?"Se connecter":"Créer mon compte"}
        </button>
      </div>
    </div>
  );
}

function CVBuilder({user,userPlan,onUpgrade}){
  const [data,setData]=useState({firstName:user?.name?.split(" ")[0]||"",lastName:"",email:user?.email||"",phone:"",photo:null});
  const [template,setTemplate]=useState("modern");
  const [lang,setLang]=useState("fr");
  const [tab,setTab]=useState("info");
  const [translating,setTranslating]=useState(false);
  const fileRef=useRef();
  const isPro=userPlan==="pro"||userPlan==="elite";

  const handlePhoto=e=>{
    const f=e.target.files[0];if(!f)return;
    const r=new FileReader();
    r.onload=ev=>setData(d=>({...d,photo:ev.target.result}));
    r.readAsDataURL(f);
  };
  const handleLang=async lc=>{
    if(!isPro&&lc!=="fr")return onUpgrade();
    setTranslating(true);
    await new Promise(r=>setTimeout(r,600));
    setLang(lc);setTranslating(false);
  };

  return(
    <div style={{display:"flex",gap:20,flexWrap:"wrap",alignItems:"flex-start"}}>
      <div style={{flex:"1 1 300px",minWidth:280}}>
        <div style={{display:"flex",gap:3,background:"#0A0A18",borderRadius:12,padding:4,marginBottom:18}}>
          {[{id:"info",l:"📋 Infos"},{id:"style",l:"🎨 Style"},{id:"lang",l:"🌍 Langue"}].map(t=>(
            <button key={t.id} onClick={()=>setTab(t.id)} style={{flex:1,padding:"9px 4px",background:tab===t.id?"#6C63FF":"transparent",color:tab===t.id?"#fff":"#888",border:"none",borderRadius:9,cursor:"pointer",fontSize:12,fontWeight:600}}>
              {t.l}
            </button>
          ))}
        </div>

        {tab==="info"&&<div style={{display:"flex",flexDirection:"column",gap:10}}>
          <div style={{display:"flex",gap:8}}>
            <input placeholder="Prénom" value={data.firstName} onChange={e=>setData(d=>({...d,firstName:e.target.value}))} style={{...inp,marginBottom:0,flex:1}}/>
            <input placeholder="Nom" value={data.lastName} onChange={e=>setData(d=>({...d,lastName:e.target.value}))} style={{...inp,marginBottom:0,flex:1}}/>
          </div>
          <input placeholder="Email" value={data.email} onChange={e=>setData(d=>({...d,email:e.target.value}))} style={{...inp,marginBottom:0}}/>
          <input placeholder="Téléphone" value={data.phone} onChange={e=>setData(d=>({...d,phone:e.target.value}))} style={{...inp,marginBottom:0}}/>
          <div style={{background:"#0A0A18",border:isPro?"1px dashed rgba(108,99,255,0.5)":"1px dashed #333",borderRadius:10,padding:16,textAlign:"center"}}>
            {!isPro&&<div style={{background:"rgba(108,99,255,0.15)",border:"1px solid #6C63FF44",borderRadius:8,padding:"8px 12px",marginBottom:10,color:"#6C63FF",fontSize:12}}>
              🔒 Photo disponible en plan <strong>Pro</strong> — <span style={{cursor:"pointer",textDecoration:"underline"}} onClick={onUpgrade}>Upgrader</span>
            </div>}
            {data.photo?(
              <div style={{display:"flex",alignItems:"center",gap:12}}>
                <img src={data.photo} alt="" style={{width:56,height:56,borderRadius:"50%",objectFit:"cover",border:"2px solid #6C63FF"}}/>
                <div>
                  <div style={{color:"#fff",fontSize:13,marginBottom:6}}>Photo ajoutée ✓</div>
                  <button onClick={()=>setData(d=>({...d,photo:null}))} style={{background:"none",border:"1px solid #FF6B6B",borderRadius:6,color:"#FF6B6B",padding:"4px 10px",cursor:"pointer",fontSize:12}}>Supprimer</button>
                </div>
              </div>
            ):(
              <div onClick={()=>isPro&&fileRef.current.click()} style={{cursor:isPro?"pointer":"not-allowed",color:isPro?"#6C63FF":"#444"}}>
                <div style={{fontSize:34,marginBottom:6}}>📷</div>
                <div style={{fontSize:13}}>{isPro?"Ajouter une photo":"Photo verrouillée"}</div>
                {isPro&&<div style={{fontSize:11,color:"#666",marginTop:4}}>JPG ou PNG · max 5 Mo</div>}
              </div>
            )}
            <input ref={fileRef} type="file" accept="image/*" onChange={handlePhoto} style={{display:"none"}}/>
          </div>
        </div>}

        {tab==="style"&&<div>
          <div style={{color:"#aaa",fontSize:13,marginBottom:14}}>Choisis un style :</div>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:10}}>
            {TEMPLATES.map(tp=>(
              <div key={tp.id} onClick={()=>setTemplate(tp.id)} style={{background:"#0A0A18",border:`2px solid ${template===tp.id?tp.accent:"transparent"}`,borderRadius:12,overflow:"hidden",cursor:"pointer"}}>
                <CVPreview data={data} template={tp.id} lang={lang} compact/>
                <div style={{padding:"7px",textAlign:"center",color:template===tp.id?tp.accent:"#aaa",fontSize:12,fontWeight:600}}>{tp.name}</div>
              </div>
            ))}
          </div>
        </div>}

        {tab==="lang"&&<div>
          <div style={{color:"#aaa",fontSize:13,marginBottom:14}}>Traduis ton CV :{!isPro&&<span style={{color:"#FF6B6B"}}> (Pro requis)</span>}</div>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8}}>
            {LANGUAGES.map(l=>(
              <button key={l.code} onClick={()=>handleLang(l.code)} style={{
                background:lang===l.code?"rgba(108,99,255,0.25)":"#0A0A18",
                border:`1px solid ${lang===l.code?"#6C63FF":"#222"}`,
                borderRadius:10,padding:"11px 8px",cursor:"pointer",
                display:"flex",alignItems:"center",gap:8,
                color:lang===l.code?"#fff":"#888",fontSize:13,fontWeight:600,
                opacity:(!isPro&&l.code!=="fr")?0.5:1
              }}>
                <span style={{fontSize:18}}>{l.flag}</span>
                <span>{l.label}</span>
                {!isPro&&l.code!=="fr"&&<span style={{marginLeft:"auto",fontSize:10}}>🔒</span>}
              </button>
            ))}
          </div>
          {!isPro&&<div style={{marginTop:16,background:"rgba(108,99,255,0.1)",border:"1px solid #6C63FF44",borderRadius:10,padding:14,textAlign:"center"}}>
            <div style={{color:"#fff",fontSize:14,fontWeight:600,marginBottom:8}}>✨ Traduction disponible en Pro</div>
            <button onClick={onUpgrade} style={{background:"linear-gradient(135deg,#6C63FF,#00D4AA)",border:"none",borderRadius:8,color:"#fff",padding:"9px 20px",cursor:"pointer",fontWeight:700}}>Passer en Pro →</button>
          </div>}
        </div>}

        <div style={{marginTop:18,display:"flex",gap:10}}>
          <button style={{flex:1,padding:13,background:"linear-gradient(135deg,#6C63FF,#8B83FF)",border:"none",borderRadius:10,color:"#fff",fontWeight:700,fontSize:14,cursor:"pointer"}}>⬇ Télécharger PDF</button>
          {isPro&&<button style={{padding:"13px 16px",background:"#0A0A18",border:"1px solid #6C63FF44",borderRadius:10,color:"#6C63FF",fontWeight:700,fontSize:14,cursor:"pointer"}}>DOC</button>}
        </div>
      </div>

      <div style={{flex:"1 1 280px",display:"flex",flexDirection:"column",alignItems:"center"}}>
        <div style={{color:"#555",fontSize:11,marginBottom:10,textTransform:"uppercase",letterSpacing:1}}>Aperçu en temps réel</div>
        {translating?(
          <div style={{display:"flex",flexDirection:"column",alignItems:"center",gap:12,color:"#6C63FF",padding:40}}>
            <div style={{width:32,height:32,border:"3px solid #6C63FF",borderTop:"3px solid transparent",borderRadius:"50%",animation:"spin 0.8s linear infinite"}}/>
            <span style={{fontSize:14}}>Traduction…</span>
          </div>
        ):(
          <CVPreview data={data} template={template} lang={lang} compact={false}/>
        )}
      </div>
    </div>
  );
}

function App(){
  const [page,setPage]=useState("home");
  const [user,setUser]=useState(null);
  const [userPlan,setUserPlan]=useState("starter");
  const [auth,setAuth]=useState(null);
  const [heroLang,setHeroLang]=useState("fr");
  const [scrolled,setScrolled]=useState(false);

  useEffect(()=>{
    const id=setInterval(()=>setHeroLang(l=>{
      const ls=["fr","en","de","es","it"];
      return ls[(ls.indexOf(l)+1)%ls.length];
    }),2200);
    return()=>clearInterval(id);
  },[]);

  useEffect(()=>{
    const fn=()=>setScrolled(window.scrollY>40);
    window.addEventListener("scroll",fn);return()=>window.removeEventListener("scroll",fn);
  },[]);

  const nav=[{id:"home",icon:"🏠",l:"Accueil"},{id:"features",icon:"⚡",l:"Fonctions"},{id:"plans",icon:"💎",l:"Plans"},{id:"builder",icon:"📄",l:"Créer"}];

  return(
    <div style={{background:"#0F0F1A",minHeight:"100vh",color:"#fff",fontFamily:"Inter,sans-serif",paddingBottom:72}}>

      {/* Navbar */}
      <nav style={{position:"sticky",top:0,zIndex:100,background:scrolled?"rgba(15,15,26,0.96)":"transparent",backdropFilter:scrolled?"blur(16px)":"none",borderBottom:scrolled?"1px solid rgba(108,99,255,0.15)":"none",padding:"0 5%",display:"flex",alignItems:"center",height:62,gap:8,transition:"all .3s"}}>
        <div style={{fontFamily:"'Playfair Display',serif",fontWeight:900,fontSize:20,background:"linear-gradient(135deg,#6C63FF,#00D4AA)",WebkitBackgroundClip:"text",WebkitTextFillColor:"transparent",flex:1}}>CVcraftPro</div>
        {user?(
          <div style={{display:"flex",alignItems:"center",gap:8}}>
            <div style={{width:32,height:32,borderRadius:"50%",background:"linear-gradient(135deg,#6C63FF,#00D4AA)",display:"flex",alignItems:"center",justifyContent:"center",fontWeight:700,fontSize:13}}>{user.name[0].toUpperCase()}</div>
            <div style={{display:"none"}}>
              <div style={{fontSize:13,fontWeight:600}}>{user.name}</div>
              <div style={{fontSize:10,color:"#6C63FF",textTransform:"uppercase"}}>{userPlan}</div>
            </div>
          </div>
        ):(
          <button onClick={()=>setAuth("login")} style={{background:"linear-gradient(135deg,#6C63FF,#00D4AA)",border:"none",borderRadius:10,color:"#fff",padding:"9px 18px",cursor:"pointer",fontWeight:700,fontSize:13}}>Connexion</button>
        )}
      </nav>

      <div style={{padding:"0 5%"}}>

        {/* ── HOME ── */}
        {page==="home"&&(
          <div>
            <section style={{minHeight:"85vh",display:"flex",alignItems:"center",gap:32,paddingTop:36,flexWrap:"wrap"}}>
              <div style={{flex:"1 1 320px",animation:"fadeUp 0.7s ease both"}}>
                <div style={{display:"inline-flex",alignItems:"center",gap:8,background:"rgba(108,99,255,0.12)",border:"1px solid rgba(108,99,255,0.3)",borderRadius:100,padding:"6px 16px",fontSize:12,color:"#6C63FF",fontWeight:600,marginBottom:20}}>
                  ✦ N°1 des créateurs de CV en France
                </div>
                <h1 style={{fontFamily:"'Playfair Display',serif",fontSize:"clamp(34px,5vw,58px)",fontWeight:900,lineHeight:1.1,marginBottom:18}}>
                  Ton CV <span style={{background:"linear-gradient(135deg,#6C63FF,#00D4AA)",WebkitBackgroundClip:"text",WebkitTextFillColor:"transparent"}}>professionnel</span>,<br/>prêt en 5 minutes.
                </h1>
                <p style={{color:"#aaa",fontSize:16,lineHeight:1.7,maxWidth:460,marginBottom:28}}>
                  Crée un CV qui se démarque, traduit en 10+ langues, avec photo et templates premium.
                </p>
                <div style={{display:"flex",gap:12,flexWrap:"wrap"}}>
                  <button onClick={()=>user?setPage("builder"):setAuth("register")} style={{background:"linear-gradient(135deg,#6C63FF,#00D4AA)",border:"none",borderRadius:12,color:"#fff",padding:"14px 28px",fontWeight:700,fontSize:15,cursor:"pointer",boxShadow:"0 8px 32px rgba(108,99,255,0.4)"}}>
                    Créer mon CV gratuitement →
                  </button>
                  <button onClick={()=>setPage("plans")} style={{background:"rgba(255,255,255,0.05)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:12,color:"#fff",padding:"14px 22px",fontWeight:600,fontSize:14,cursor:"pointer"}}>
                    Voir les plans
                  </button>
                </div>
                <div style={{display:"flex",gap:20,marginTop:28,flexWrap:"wrap"}}>
                  {["✓ Gratuit pour commencer","✓ Sans carte bancaire","✓ Export PDF"].map(t=>(
                    <span key={t} style={{color:"#666",fontSize:12}}>{t}</span>
                  ))}
                </div>
              </div>

              <div style={{flex:"1 1 260px",display:"flex",justifyContent:"center",position:"relative"}} className="float">
                <div style={{position:"absolute",inset:-50,background:"radial-gradient(ellipse,rgba(108,99,255,0.15) 0%,transparent 70%)",pointerEvents:"none"}}/>
                <div style={{position:"relative"}}>
                  <CVPreview data={{firstName:"Sophie",lastName:"Martin",email:"sophie@email.com",photo:null}} template="modern" lang={heroLang} compact={false}/>
                  <div style={{position:"absolute",top:-10,right:-10,background:"linear-gradient(135deg,#6C63FF,#00D4AA)",borderRadius:100,padding:"5px 12px",fontSize:12,fontWeight:700,boxShadow:"0 4px 16px rgba(108,99,255,0.5)"}}>
                    {LANGUAGES.find(l=>l.code===heroLang)?.flag} {LANGUAGES.find(l=>l.code===heroLang)?.label}
                  </div>
                </div>
              </div>
            </section>

            {/* Stats */}
            <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",background:"rgba(108,99,255,0.08)",borderRadius:16,overflow:"hidden",border:"1px solid rgba(108,99,255,0.15)",marginBottom:60}}>
              {[{n:"50K+",l:"CVs créés"},{n:"10+",l:"Langues"},{n:"98%",l:"Satisfaction"},{n:"5 min",l:"Temps moyen"}].map((s,i)=>(
                <div key={i} style={{padding:"22px 16px",textAlign:"center",borderRight:i<3?"1px solid rgba(108,99,255,0.1)":"none"}}>
                  <div style={{fontFamily:"'Playfair Display',serif",fontSize:30,fontWeight:900,background:"linear-gradient(135deg,#6C63FF,#00D4AA)",WebkitBackgroundClip:"text",WebkitTextFillColor:"transparent"}}>{s.n}</div>
                  <div style={{color:"#666",fontSize:12,marginTop:3}}>{s.l}</div>
                </div>
              ))}
            </div>

            {/* Features */}
            <h2 style={{fontFamily:"'Playfair Display',serif",fontSize:32,fontWeight:900,textAlign:"center",marginBottom:10}}>Tout ce dont tu as besoin</h2>
            <p style={{color:"#888",textAlign:"center",marginBottom:36,fontSize:15}}>Une plateforme complète pour créer et partager ton CV.</p>
            <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(200px,1fr))",gap:16,marginBottom:60}}>
              {[
                {icon:"🎨",t:"Templates premium",d:"15+ designs optimisés pour passer les filtres ATS."},
                {icon:"🌍",t:"Traduction auto",d:"10+ langues : Anglais, Allemand, Espagnol et plus."},
                {icon:"📷",t:"Photo de profil",d:"Ajoute ta photo en un clic depuis ton iPhone."},
                {icon:"⚡",t:"Export instantané",d:"PDF haute qualité prêt à envoyer en quelques secondes."},
                {icon:"✏️",t:"Édition temps réel",d:"Vois les modifications au fur et à mesure."},
                {icon:"🤖",t:"IA rédactrice",d:"L'IA reformule tes expériences professionnelles."},
              ].map((f,i)=>(
                <div key={i} style={{background:"#1A1A2E",border:"1px solid rgba(108,99,255,0.1)",borderRadius:14,padding:"22px 18px"}}>
                  <div style={{fontSize:28,marginBottom:10}}>{f.icon}</div>
                  <div style={{fontWeight:700,fontSize:15,marginBottom:7}}>{f.t}</div>
                  <div style={{color:"#888",fontSize:13,lineHeight:1.6}}>{f.d}</div>
                </div>
              ))}
            </div>

            {/* CTA */}
            <div style={{textAlign:"center",padding:"50px 20px",background:"linear-gradient(135deg,rgba(108,99,255,0.12),rgba(0,212,170,0.08))",borderRadius:20,border:"1px solid rgba(108,99,255,0.2)",marginBottom:40}}>
              <h2 style={{fontFamily:"'Playfair Display',serif",fontSize:30,fontWeight:900,marginBottom:12}}>Prêt à décrocher ton prochain job ?</h2>
              <p style={{color:"#aaa",fontSize:15,marginBottom:24}}>Rejoins 50 000 professionnels qui font confiance à CVcraftPro.</p>
              <button onClick={()=>user?setPage("builder"):setAuth("register")} style={{background:"linear-gradient(135deg,#6C63FF,#00D4AA)",border:"none",borderRadius:12,color:"#fff",padding:"14px 36px",fontWeight:700,fontSize:16,cursor:"pointer",boxShadow:"0 8px 32px rgba(108,99,255,0.4)"}}>
                Commencer gratuitement
              </button>
            </div>
          </div>
        )}

        {/* ── FEATURES ── */}
        {page==="features"&&(
          <div style={{paddingTop:48,paddingBottom:60}}>
            <h1 style={{fontFamily:"'Playfair Display',serif",fontSize:36,fontWeight:900,textAlign:"center",marginBottom:10}}>Toutes les fonctionnalités</h1>
            <p style={{color:"#888",textAlign:"center",marginBottom:40,fontSize:15}}>Tout ce qui fait de CVcraftPro la meilleure plateforme.</p>
            {[
              {icon:"🎨",t:"Templates conçus par des pros",d:"15+ templates optimisés ATS. Chaque design est testé pour maximiser tes chances d'être retenu par les recruteurs.",plan:"Tous les plans",c:"#6C63FF"},
              {icon:"📷",t:"Photo de profil",d:"Ajoute une photo depuis ton iPhone directement. Recadrage automatique pour un rendu parfait sur tous les templates.",plan:"Pro & Élite",c:"#00D4AA"},
              {icon:"🌍",t:"Traduction multilingue",d:"Traduis ton CV en un clic : Anglais, Allemand, Espagnol, Italien, Portugais, Néerlandais, Arabe. Idéal pour candidatures internationales.",plan:"Pro & Élite",c:"#00D4AA"},
              {icon:"🤖",t:"Rédaction assistée par IA",d:"L'IA reformule automatiquement tes descriptions de poste pour les rendre plus percutantes et adaptées au secteur visé.",plan:"Élite uniquement",c:"#FFD700"},
              {icon:"📄",t:"Export multi-formats",d:"PDF haute résolution, Word (.docx) ou JSON. Le PDF inclut les polices embarquées pour un rendu parfait partout.",plan:"Pro & Élite",c:"#00D4AA"},
              {icon:"✍️",t:"Signature numérique",d:"Ajoute une signature manuscrite numérique à ton CV pour personnaliser davantage tes candidatures importantes.",plan:"Élite uniquement",c:"#FFD700"},
            ].map((f,i)=>(
              <div key={i} style={{display:"flex",gap:20,background:"#1A1A2E",border:"1px solid rgba(108,99,255,0.1)",borderRadius:14,padding:"22px 20px",marginBottom:14,alignItems:"flex-start"}}>
                <div style={{fontSize:36,flexShrink:0}}>{f.icon}</div>
                <div>
                  <div style={{display:"flex",alignItems:"center",gap:10,marginBottom:7,flexWrap:"wrap"}}>
                    <span style={{fontSize:16,fontWeight:700}}>{f.t}</span>
                    <span style={{background:f.c+"22",color:f.c,borderRadius:100,padding:"3px 11px",fontSize:11,fontWeight:700}}>{f.plan}</span>
                  </div>
                  <div style={{color:"#888",fontSize:14,lineHeight:1.7}}>{f.d}</div>
                </div>
              </div>
            ))}
          </div>
        )}

        {/* ── PLANS ── */}
        {page==="plans"&&(
          <div style={{paddingTop:48,paddingBottom:60}}>
            <div style={{textAlign:"center",marginBottom:44}}>
              <h1 style={{fontFamily:"'Playfair Display',serif",fontSize:36,fontWeight:900}}>Choisis ton plan</h1>
              <p style={{color:"#888",marginTop:10,fontSize:15}}>Sans engagement · Résiliable à tout moment</p>
            </div>
            <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(260px,1fr))",gap:18,maxWidth:960,margin:"0 auto",marginBottom:48}}>
              {PLANS.map(plan=>(
                <div key={plan.id} style={{background:"#1A1A2E",border:`2px solid ${plan.badge?plan.color+"55":"rgba(255,255,255,0.06)"}`,borderRadius:20,padding:"30px 24px",position:"relative",transform:plan.badge?"scale(1.02)":"scale(1)",boxShadow:plan.badge?`0 20px 60px ${plan.color}20`:"none"}}>
                  {plan.badge&&<div style={{position:"absolute",top:-12,left:"50%",transform:"translateX(-50%)",background:plan.color,borderRadius:100,padding:"4px 16px",fontSize:11,fontWeight:800,letterSpacing:1,color:plan.id==="elite"?"#000":"#fff",whiteSpace:"nowrap"}}>{plan.badge}</div>}
                  <div style={{fontSize:12,color:plan.color,fontWeight:700,textTransform:"uppercase",letterSpacing:1,marginBottom:8}}>{plan.name}</div>
                  <div style={{display:"flex",alignItems:"baseline",gap:4,marginBottom:6}}>
                    <span style={{fontFamily:"'Playfair Display',serif",fontSize:42,fontWeight:900}}>{plan.price}</span>
                    <span style={{color:"#666",fontSize:14}}>{plan.period}</span>
                  </div>
                  <div style={{height:1,background:plan.color+"33",margin:"18px 0"}}/>
                  <div style={{display:"flex",flexDirection:"column",gap:10,marginBottom:24}}>
                    {plan.features.map((f,i)=>(
                      <div key={i} style={{display:"flex",alignItems:"flex-start",gap:9,color:f.ok?"#ddd":"#444",fontSize:13}}>
                        <span style={{color:f.ok?plan.color:"#333",fontSize:14}}>{f.ok?"✓":"✗"}</span>{f.text}
                      </div>
                    ))}
                  </div>
                  <button onClick={()=>{
                    if(plan.id==="pro"){window.open("https://merveille-co.mymaketou.store/products/cvcraftpro-pro-/checkout","_blank");return;}
                    if(plan.id==="elite"){window.open("https://merveille-co.mymaketou.store/products/cvcraft-pro-elite-/checkout","_blank");return;}
                    if(!user)setAuth("register");else{setUserPlan(plan.id);setPage("builder");}
                  }} style={{
                    width:"100%",padding:13,border:"none",borderRadius:11,fontWeight:700,fontSize:14,cursor:"pointer",
                    background:plan.badge?plan.color:"transparent",
                    color:plan.badge?(plan.id==="elite"?"#000":"#fff"):plan.color,
                    border:plan.badge?"none":`2px solid ${plan.color}`
                  }}>
                    {plan.id==="starter"?"Commencer gratuitement":plan.id==="pro"?"💳 Payer 6 500 FCFA/mois":"💳 Payer 13 000 FCFA/mois"}
                  </button>
                </div>
              ))}
            </div>

            {/* Tableau langues */}
            <h2 style={{fontFamily:"'Playfair Display',serif",fontSize:26,fontWeight:700,textAlign:"center",marginBottom:20}}>Langues par plan</h2>
            <div style={{background:"#1A1A2E",borderRadius:14,overflow:"hidden",border:"1px solid rgba(108,99,255,0.15)",maxWidth:700,margin:"0 auto"}}>
              <div style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr 1fr",background:"rgba(108,99,255,0.15)",padding:"12px 18px",fontWeight:700,fontSize:13}}>
                <span>Langue</span><span style={{textAlign:"center"}}>Starter</span><span style={{textAlign:"center",color:"#00D4AA"}}>Pro</span><span style={{textAlign:"center",color:"#FFD700"}}>Élite</span>
              </div>
              {[
                {l:"🇫🇷 Français",s:true,p:true,e:true},{l:"🇬🇧 Anglais",s:false,p:true,e:true},
                {l:"🇩🇪 Allemand",s:false,p:true,e:true},{l:"🇪🇸 Espagnol",s:false,p:true,e:true},
                {l:"🇮🇹 Italien",s:false,p:true,e:true},{l:"🇵🇹 Portugais",s:false,p:false,e:true},
                {l:"🇳🇱 Néerlandais",s:false,p:false,e:true},{l:"🇸🇦 Arabe",s:false,p:false,e:true}
              ].map((row,i)=>(
                <div key={i} style={{display:"grid",gridTemplateColumns:"1fr 1fr 1fr 1fr",padding:"11px 18px",borderTop:"1px solid rgba(255,255,255,0.04)",fontSize:13}}>
                  <span style={{color:"#ccc"}}>{row.l}</span>
                  <span style={{textAlign:"center",color:row.s?"#6C63FF":"#333"}}>{row.s?"✓":"—"}</span>
                  <span style={{textAlign:"center",color:row.p?"#00D4AA":"#333"}}>{row.p?"✓":"—"}</span>
                  <span style={{textAlign:"center",color:row.e?"#FFD700":"#333"}}>{row.e?"✓":"—"}</span>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* ── BUILDER ── */}
        {page==="builder"&&(
          <div style={{paddingTop:36,paddingBottom:60}}>
            <div style={{display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:24,flexWrap:"wrap",gap:10}}>
              <div>
                <h1 style={{fontFamily:"'Playfair Display',serif",fontSize:28,fontWeight:900}}>Créer mon CV</h1>
                <div style={{color:"#888",fontSize:13,marginTop:3}}>
                  Plan : <span style={{color:userPlan==="elite"?"#FFD700":userPlan==="pro"?"#00D4AA":"#6C63FF",fontWeight:700,textTransform:"capitalize"}}>{userPlan}</span>
                  {userPlan!=="elite"&&<span onClick={()=>setPage("plans")} style={{color:"#6C63FF",marginLeft:12,cursor:"pointer",fontSize:13}}>⬆ Upgrader</span>}
                </div>
              </div>
              {!user&&<div style={{background:"rgba(255,107,107,0.1)",border:"1px solid rgba(255,107,107,0.3)",borderRadius:10,padding:"9px 14px",fontSize:12,color:"#FF6B6B"}}>
                ⚠ <span style={{cursor:"pointer",textDecoration:"underline"}} onClick={()=>setAuth("login")}>Connecte-toi</span> pour sauvegarder.
              </div>}
            </div>
            {!user&&(
              <div style={{display:"flex",gap:8,marginBottom:20,flexWrap:"wrap"}}>
                <span style={{color:"#666",fontSize:12,alignSelf:"center"}}>Tester :</span>
                {PLANS.map(p=>(
                  <button key={p.id} onClick={()=>setUserPlan(p.id)} style={{background:userPlan===p.id?p.color+"33":"transparent",border:`1px solid ${userPlan===p.id?p.color:"#333"}`,borderRadius:8,color:userPlan===p.id?p.color:"#666",padding:"6px 14px",cursor:"pointer",fontSize:12,fontWeight:600}}>{p.name}</button>
                ))}
              </div>
            )}
            <CVBuilder user={user} userPlan={userPlan} onUpgrade={()=>setPage("plans")}/>
          </div>
        )}
      </div>

      {/* Bottom nav */}
      <div style={{position:"fixed",bottom:0,left:0,right:0,background:"rgba(15,15,26,0.97)",borderTop:"1px solid rgba(108,99,255,0.2)",display:"flex",backdropFilter:"blur(16px)",zIndex:99}}>
        {nav.map(n=>(
          <button key={n.id} onClick={()=>setPage(n.id)} style={{flex:1,padding:"11px 4px 9px",background:"none",border:"none",cursor:"pointer",display:"flex",flexDirection:"column",alignItems:"center",gap:3,color:page===n.id?"#6C63FF":"#555",transition:"all .2s"}}>
            <span style={{fontSize:19}}>{n.icon}</span>
            <span style={{fontSize:10,fontWeight:600}}>{n.l}</span>
          </button>
        ))}
      </div>

      {auth&&<AuthModal mode={auth} onClose={()=>setAuth(null)} onSuccess={u=>{setUser(u);setAuth(null);}}/>}
    </div>
  );
}

ReactDOM.createRoot(document.getElementById("root")).render(<App/>);
</script>
</body>
</html>
