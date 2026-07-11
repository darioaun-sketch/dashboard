<!DOCTYPE html>
<html lang="es" class="dark">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>JARVIS — Proyecto Superman V3</title>

<script>
  tailwind = {
    config: {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            sys: {
              base: '#050a14',
              panel: '#0a1122',
              panelHover: '#0e172c',
              border: '#1a2744',
              borderLight: '#2a3b5c',
              accent: '#00f0ff',
              textMain: '#e2e8f0',
              textMuted: '#64748b',
              success: '#00ff88',
              warning: '#ffb800',
              danger: '#ff0055',
              purple: '#b026ff'
            }
          },
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            mono: ['JetBrains Mono', 'monospace'],
          }
        }
      }
    }
  }
</script>
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@100..800&display=swap" rel="stylesheet">

<style>
  :root { color-scheme: dark; }
  
  body {
    background-color: #03060c;
    background-image: 
      radial-gradient(circle at 15% 50%, rgba(0, 240, 255, 0.05), transparent 25%),
      radial-gradient(circle at 85% 30%, rgba(176, 38, 255, 0.05), transparent 25%);
    color: #e2e8f0;
    /* SCROLL NATIVO LIBERADO: Eliminamos overflow: hidden */
    overflow-x: hidden; 
    -webkit-font-smoothing: antialiased;
  }

  .bg-grid {
    position: fixed;
    inset: 0;
    z-index: -1;
    background-size: 50px 50px;
    background-image: 
      linear-gradient(to right, rgba(255, 255, 255, 0.03) 1px, transparent 1px),
      linear-gradient(to bottom, rgba(255, 255, 255, 0.03) 1px, transparent 1px);
    mask-image: linear-gradient(to bottom, black 40%, transparent 100%);
    -webkit-mask-image: linear-gradient(to bottom, black 40%, transparent 100%);
  }

  /* Scrollbar personalizado nativo */
  ::-webkit-scrollbar { width: 8px; height: 8px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: rgba(26, 39, 68, 0.8); border-radius: 4px; }
  ::-webkit-scrollbar-thumb:hover { background: rgba(42, 59, 92, 1); }

  .glass-panel {
    background: linear-gradient(145deg, rgba(10, 17, 34, 0.7) 0%, rgba(5, 10, 20, 0.9) 100%);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: 16px;
    box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.3);
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  }
  
  .glass-panel.clickable { cursor: pointer; }
  .glass-panel.clickable:hover {
    border-color: rgba(0, 240, 255, 0.4);
    box-shadow: 0 15px 35px rgba(0, 240, 255, 0.15);
    transform: translateY(-5px) scale(1.02);
  }

  /* Animaciones CSS puro */
  .animate-slide-up { animation: slideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1) both; }
  .animate-pulse-slow { animation: pulseSlow 3s cubic-bezier(0.4, 0, 0.6, 1) infinite; }
  .animate-float { animation: float 6s ease-in-out infinite; }
  .animate-radar-sweep { animation: sweep 4s linear infinite; }

  @keyframes slideUp {
    0% { opacity: 0; transform: translateY(30px); }
    100% { opacity: 1; transform: translateY(0); }
  }
  @keyframes customFadeIn {
    0% { opacity: 0; }
    100% { opacity: 1; }
  }
  @keyframes sweep {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
  @keyframes pulseSlow {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.5; }
  }
  @keyframes float {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-8px); }
  }

  .stagger-1 { animation-delay: 0.1s; }
  .stagger-2 { animation-delay: 0.2s; }
  .stagger-3 { animation-delay: 0.3s; }
  .stagger-4 { animation-delay: 0.4s; }

  /* Gráficos y radares */
  .radar-container {
    position: relative; width: 100%; aspect-ratio: 1; border-radius: 50%;
    border: 1px solid rgba(0, 240, 255, 0.2); overflow: hidden;
    background: radial-gradient(circle, rgba(0,240,255,0.05) 0%, rgba(0,0,0,0) 70%);
  }
  .radar-container::before { content: ''; position: absolute; top: 50%; left: 0; right: 0; height: 1px; background: rgba(0, 240, 255, 0.1); }
  .radar-container::after { content: ''; position: absolute; left: 50%; top: 0; bottom: 0; width: 1px; background: rgba(0, 240, 255, 0.1); }
  .radar-sweep {
    position: absolute; top: 50%; left: 50%; width: 50%; height: 50%;
    transform-origin: 0% 0%;
    background: conic-gradient(from 0deg, rgba(0,240,255,0) 0deg, rgba(0,240,255,0.4) 90deg, rgba(0,240,255,0) 90deg);
    border-right: 2px solid #00f0ff;
  }

  .donut-chart { transform: rotate(-90deg); filter: drop-shadow(0 0 10px rgba(0,0,0,0.5)); }
  .donut-bg { fill: none; stroke: rgba(26, 39, 68, 0.4); stroke-width: 8; }
  .donut-fill { fill: none; stroke-width: 8; stroke-linecap: round; transition: stroke-dashoffset 1.5s cubic-bezier(0.68, -0.55, 0.265, 1.55); }

  /* Formularios */
  .sci-input {
    background: rgba(0,0,0,0.3); border: 1px solid #1a2744; color: #00f0ff;
    font-family: 'JetBrains Mono', monospace; padding: 8px 12px; border-radius: 6px; width: 100%; outline: none;
    transition: border 0.3s;
  }
  .sci-input:focus { border-color: #00f0ff; box-shadow: 0 0 10px rgba(0, 240, 255, 0.1); }
  
  .sci-checkbox {
    appearance: none; width: 20px; height: 20px; border: 1px solid #1a2744;
    border-radius: 4px; background: rgba(0,0,0,0.3); cursor: pointer; position: relative; transition: all 0.2s;
  }
  .sci-checkbox:checked { background: rgba(0, 255, 136, 0.2); border-color: #00ff88; }
  .sci-checkbox:checked::after {
    content: '\f00c'; font-family: 'Font Awesome 6 Free'; font-weight: 900; font-size: 12px; color: #00ff88;
    position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);
  }

  /* Transición de Vistas */
  .view-section { display: none; opacity: 0; }
  .view-section.active {
    display: flex; flex-direction: column;
    animation: customFadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1) both;
  }
</style>
</head>

<body class="min-h-screen flex flex-col font-sans w-full relative">
  
  <div class="bg-grid"></div>

  <header class="h-16 border-b border-sys-border bg-sys-base/80 backdrop-blur-md flex items-center justify-between px-6 sticky top-0 z-50">
    <div class="flex items-center gap-4">
      <div class="flex items-center gap-3">
        <div class="w-8 h-8 rounded-full bg-blue-900 flex items-center justify-center border border-sys-accent shadow-[0_0_10px_rgba(0,240,255,0.3)]">
          <i class="fa-solid fa-microchip text-sys-accent text-xs"></i>
        </div>
        <div>
          <h1 class="font-bold tracking-widest text-sm text-white m-0 leading-tight">SUPERMAN<span class="text-sys-accent">SYS</span></h1>
          <p class="font-mono text-[10px] text-sys-textMuted uppercase">Intelligence System v3.0</p>
        </div>
      </div>
      
      <button id="btnBack" class="hidden ml-6 text-sys-textMuted hover:text-white flex items-center gap-2 text-sm transition-colors border border-sys-border px-3 py-1.5 rounded-md bg-sys-panel hover:bg-sys-panelHover">
        <i class="fa-solid fa-arrow-left"></i> Retornar
      </button>
    </div>

    <div class="flex items-center gap-6">
      <div class="flex items-center gap-2 px-4 py-1.5 rounded-full bg-green-900/20 border border-green-500/30">
        <div class="w-2 h-2 rounded-full bg-sys-success animate-pulse"></div>
        <span class="font-mono text-xs text-sys-success font-medium hidden sm:block">SISTEMA ÓPTIMO</span>
      </div>
      <div id="sysClock" class="font-mono text-sm text-white font-medium">--:--:--</div>
      <div id="sysDate" class="font-mono text-xs text-sys-textMuted uppercase hidden sm:block">-- --- ----</div>
    </div>
    
    <div class="flex items-center gap-4 text-sys-textMuted hidden md:flex">
      <button class="hover:text-white transition-colors" onclick="resetData()"><i class="fa-solid fa-arrows-rotate text-sm"></i></button>
      <button class="hover:text-white transition-colors"><i class="fa-regular fa-bell text-sm"></i></button>
      <button class="hover:text-white transition-colors"><i class="fa-solid fa-gear text-sm"></i></button>
    </div>
  </header>

  <main class="w-full flex-1 pb-10" id="workspace">

    <section id="view-dashboard" class="view-section active p-4 md:p-6 w-full max-w-[1600px] mx-auto">
      
      <div class="grid grid-cols-2 lg:grid-cols-4 gap-4 md:gap-6 mb-6">
        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-1" onclick="navigateTo('view-training')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-blue-500/10 rounded-full blur-2xl group-hover:bg-blue-500/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-blue-500/20 flex items-center justify-center border border-blue-500/30 group-hover:scale-110 transition-transform duration-300">
              <i class="fa-solid fa-dumbbell text-blue-400 text-lg"></i>
            </div>
            <i class="fa-solid fa-arrow-right text-sys-border group-hover:text-blue-400 transition-colors group-hover:translate-x-1 duration-300"></i>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Entrenamiento</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">PPL x2 / Fuerza & Hipertrofia</p>
          </div>
        </div>
        
        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-2" onclick="navigateTo('view-nutrition')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-sys-success/10 rounded-full blur-2xl group-hover:bg-sys-success/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-sys-success/20 flex items-center justify-center border border-sys-success/30 group-hover:scale-110 transition-transform duration-300">
              <i class="fa-solid fa-flask text-sys-success text-lg"></i>
            </div>
            <i class="fa-solid fa-arrow-right text-sys-border group-hover:text-sys-success transition-colors group-hover:translate-x-1 duration-300"></i>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Nutrición & Macros</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">2350 kcal / Recomposición</p>
          </div>
        </div>

        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-3" onclick="navigateTo('view-habits')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-purple-500/10 rounded-full blur-2xl group-hover:bg-purple-500/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-purple-500/20 flex items-center justify-center border border-purple-500/30 group-hover:scale-110 transition-transform duration-300">
              <i class="fa-solid fa-dna text-purple-400 text-lg"></i>
            </div>
            <i class="fa-solid fa-arrow-right text-sys-border group-hover:text-purple-400 transition-colors group-hover:translate-x-1 duration-300"></i>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Protocolos T</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">Testosterona & Disciplina</p>
          </div>
        </div>

        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-4" onclick="navigateTo('view-analytics')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-sys-warning/10 rounded-full blur-2xl group-hover:bg-sys-warning/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-sys-warning/20 flex items-center justify-center border border-sys-warning/30 group-hover:scale-110 transition-transform duration-300">
              <i class="fa-solid fa-chart-radar text-sys-warning text-lg"></i>
            </div>
            <i class="fa-solid fa-arrow-right text-sys-border group-hover:text-sys-warning transition-colors group-hover:translate-x-1 duration-300"></i>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Analítica Mensual</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">Tendencias de Progreso 30D</p>
          </div>
        </div>
      </div>

      <div class="grid grid-cols-1 xl:grid-cols-12 gap-4 md:gap-6">
        
        <div class="col-span-1 xl:col-span-4 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-2">
          <div class="glass-panel p-5 flex flex-col gap-4">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-2">Estado del Operador</h2>
            <div class="flex items-center gap-4">
              <div class="w-12 h-12 bg-sys-panel border border-sys-accent/30 rounded-lg flex items-center justify-center font-mono font-bold text-lg text-white shadow-[0_0_15px_rgba(0,240,255,0.2)] animate-pulse-slow">SM</div>
              <div>
                <h3 class="font-bold text-sm tracking-wide">Superman Protocol</h3>
                <div class="flex gap-2 text-[10px] font-mono mt-1">
                  <span class="bg-sys-success/20 text-sys-success px-2 py-0.5 rounded border border-sys-success/30">Fase 3</span>
                  <span class="bg-blue-500/20 text-blue-400 px-2 py-0.5 rounded border border-blue-500/30">56.8 KG</span>
                </div>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-3 mt-2">
              <div class="bg-sys-panel/50 rounded-lg border border-sys-border p-3 transition-colors hover:bg-sys-panel">
                <p class="text-[10px] text-sys-textMuted">Descanso Prev.</p>
                <p class="font-mono text-white text-sm mt-1" id="dashSleepVal">-- h</p>
              </div>
              <div class="bg-sys-panel/50 rounded-lg border border-sys-border p-3 transition-colors hover:bg-sys-panel">
                <p class="text-[10px] text-sys-textMuted">Día del Ciclo</p>
                <p class="font-mono text-white text-sm mt-1" id="dashCycleDay">--</p>
              </div>
            </div>
          </div>

          <div class="glass-panel p-5 flex-1 flex flex-col items-center justify-center relative min-h-[250px] overflow-hidden">
            <div class="absolute inset-0 bg-gradient-to-b from-transparent to-sys-accent/5 pointer-events-none"></div>
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold absolute top-5 left-5">Biometría</h2>
            <div class="w-40 h-40 mt-6 relative animate-float">
              <div class="radar-container">
                <div class="radar-sweep animate-radar-sweep"></div>
                <div class="absolute w-2 h-2 bg-sys-accent rounded-full top-[30%] left-[60%] shadow-[0_0_10px_#00f0ff] animate-pulse"></div>
                <div class="absolute w-2 h-2 bg-sys-success rounded-full bottom-[40%] right-[30%] shadow-[0_0_10px_#00ff88]"></div>
              </div>
            </div>
            <p class="font-mono text-[10px] text-sys-accent mt-6 text-center uppercase bg-sys-accent/10 px-3 py-1 rounded-full border border-sys-accent/20">Escaneando...<br><span class="text-sys-textMuted lowercase mt-1 inline-block">todo nominal</span></p>
          </div>
        </div>

        <div class="col-span-1 xl:col-span-5 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-3">
          <div class="glass-panel p-6 flex flex-col">
            <div class="flex justify-between items-center mb-8">
              <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold">Resumen Diario</h2>
              <span class="text-[10px] font-mono bg-sys-panel border border-sys-border px-3 py-1 rounded-full shadow-inner">Módulos: <span id="dashTotalModules" class="text-white">0/3</span></span>
            </div>
            <div class="flex flex-col sm:flex-row items-center justify-center gap-8 md:gap-12 py-4">
              <div class="relative w-40 h-40 flex-shrink-0 animate-float">
                <svg viewBox="0 0 100 100" class="donut-chart w-full h-full">
                  <circle class="donut-bg" cx="50" cy="50" r="40"></circle>
                  <circle id="donutTraining" class="donut-fill" cx="50" cy="50" r="40" stroke="#3b82f6" stroke-dasharray="251.2" stroke-dashoffset="251.2"></circle>
                  <circle id="donutDiet" class="donut-fill" cx="50" cy="50" r="40" stroke="#00ff88" stroke-dasharray="251.2" stroke-dashoffset="251.2"></circle>
                  <circle id="donutHabits" class="donut-fill" cx="50" cy="50" r="40" stroke="#b026ff" stroke-dasharray="251.2" stroke-dashoffset="251.2"></circle>
                </svg>
                <div class="absolute inset-0 flex flex-col items-center justify-center bg-sys-panel/30 rounded-full m-4 border border-sys-border/50 backdrop-blur-sm">
                  <span class="font-mono text-3xl font-bold text-white drop-shadow-md" id="dashPercent">0%</span>
                </div>
              </div>
              <div class="flex flex-col gap-4 w-full sm:w-auto min-w-[140px]">
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border hover:bg-sys-panel transition-colors">
                  <div class="flex items-center gap-2"><div class="w-2.5 h-2.5 rounded-full bg-blue-500 shadow-[0_0_8px_rgba(59,130,246,0.6)]"></div><span class="text-sys-textMuted">Entreno</span></div>
                  <span class="font-mono text-white font-bold" id="legTraining">0%</span>
                </div>
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border hover:bg-sys-panel transition-colors">
                  <div class="flex items-center gap-2"><div class="w-2.5 h-2.5 rounded-full bg-sys-success shadow-[0_0_8px_rgba(0,255,136,0.6)]"></div><span class="text-sys-textMuted">Nutrición</span></div>
                  <span class="font-mono text-white font-bold" id="legDiet">0%</span>
                </div>
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border hover:bg-sys-panel transition-colors">
                  <div class="flex items-center gap-2"><div class="w-2.5 h-2.5 rounded-full bg-purple-500 shadow-[0_0_8px_rgba(176,38,255,0.6)]"></div><span class="text-sys-textMuted">Hábitos</span></div>
                  <span class="font-mono text-white font-bold" id="legHabits">0%</span>
                </div>
              </div>
            </div>
          </div>
          <div class="glass-panel p-5 flex-1 flex flex-col relative overflow-hidden min-h-[250px]">
            <div class="absolute top-0 right-0 w-32 h-32 bg-blue-500/5 rounded-bl-full pointer-events-none"></div>
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-4">Agenda del Sistema</h2>
            <div class="space-y-3" id="dashSchedule"></div>
          </div>
        </div>

        <div class="col-span-1 xl:col-span-3 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-4">
          <div class="glass-panel p-5 relative overflow-hidden">
            <div class="absolute -right-4 -bottom-4 w-20 h-20 bg-sys-success/10 rounded-full blur-xl pointer-events-none"></div>
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-5">Estado Físico</h2>
            <div class="flex items-center justify-between bg-sys-panel/60 border border-sys-border rounded-lg p-3 mb-3 hover:border-sys-success/30 transition-colors">
              <span class="text-xs text-sys-textMuted">Testosterona Est.</span>
              <span class="font-mono text-sm text-sys-success font-bold flex items-center gap-2"><i class="fa-solid fa-arrow-trend-up text-[10px]"></i> Óptimo</span>
            </div>
            <div class="flex items-center justify-between bg-sys-panel/60 border border-sys-border rounded-lg p-3 hover:border-sys-accent/30 transition-colors">
              <span class="text-xs text-sys-textMuted">Fatiga SNC</span>
              <span class="font-mono text-sm text-sys-accent font-bold" id="dashCNS">Nominal</span>
            </div>
          </div>

          <div class="glass-panel p-5 flex-1 flex flex-col min-h-[220px]">
            <div class="flex justify-between items-center mb-6">
              <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold">Consumo Energía</h2>
              <span class="text-[10px] font-mono text-sys-success animate-pulse flex items-center gap-1"><div class="w-1.5 h-1.5 rounded-full bg-sys-success"></div> Live</span>
            </div>
            <div class="flex justify-between items-end gap-2 h-full pb-2">
              <div class="flex flex-col items-center gap-3 w-full group">
                <span class="font-mono text-[10px] text-sys-textMuted group-hover:text-sys-accent transition-colors">PRO</span>
                <div class="relative w-14 h-14 transition-transform group-hover:scale-110">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90 filter drop-shadow-[0_0_4px_rgba(0,240,255,0.3)]"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniPro" class="donut-fill" cx="18" cy="18" r="15" stroke="#00f0ff" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[10px] font-bold text-white" id="lblPro">0%</span>
                </div>
              </div>
              <div class="flex flex-col items-center gap-3 w-full group">
                <span class="font-mono text-[10px] text-sys-textMuted group-hover:text-purple-400 transition-colors">CAR</span>
                <div class="relative w-14 h-14 transition-transform group-hover:scale-110">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90 filter drop-shadow-[0_0_4px_rgba(176,38,255,0.3)]"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniCar" class="donut-fill" cx="18" cy="18" r="15" stroke="#b026ff" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[10px] font-bold text-white" id="lblCar">0%</span>
                </div>
              </div>
              <div class="flex flex-col items-center gap-3 w-full group">
                <span class="font-mono text-[10px] text-sys-textMuted group-hover:text-sys-warning transition-colors">GRA</span>
                <div class="relative w-14 h-14 transition-transform group-hover:scale-110">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90 filter drop-shadow-[0_0_4px_rgba(255,184,0,0.3)]"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniFat" class="donut-fill" cx="18" cy="18" r="15" stroke="#ffb800" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[10px] font-bold text-white" id="lblFat">0%</span>
                </div>
              </div>
            </div>
          </div>
        </div>

      </div>
    </section>

    <section id="view-training" class="view-section p-4 md:p-6 max-w-[1000px] mx-auto">
      <div class="glass-panel p-6 flex flex-col">
        <div class="flex justify-between items-start mb-6 border-b border-sys-border pb-4">
          <div>
            <h2 class="text-2xl font-bold font-mono text-white flex items-center gap-3">
              <i class="fa-solid fa-dumbbell text-blue-400"></i> Módulo de Combate
            </h2>
            <p class="text-sys-textMuted text-sm mt-1" id="trainTitle">Día de Empuje (Fuerza)</p>
          </div>
          <div class="text-right">
            <span class="font-mono text-sm bg-blue-500/20 text-blue-400 px-3 py-1 rounded border border-blue-500/30">Estado: <span id="trainStatus">Pendiente</span></span>
          </div>
        </div>
        <div class="space-y-4" id="trainExerciseList">
          </div>
        <div class="mt-6 pt-4 border-t border-sys-border flex flex-wrap justify-end gap-4">
          <button class="bg-sys-panel border border-sys-border text-white px-6 py-2 rounded font-mono text-sm hover:bg-sys-border transition-colors" onclick="saveTraining()">Guardar Registro</button>
          <button class="bg-blue-600 hover:bg-blue-500 text-white px-6 py-2 rounded font-mono text-sm transition-colors" onclick="completeTraining()">Marcar Completada</button>
        </div>
      </div>
    </section>

    <section id="view-nutrition" class="view-section p-4 md:p-6 max-w-[1000px] mx-auto">
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div class="col-span-1 glass-panel p-6 flex flex-col gap-6">
          <div>
            <h2 class="text-lg font-bold font-mono text-white border-b border-sys-border pb-2 mb-4">Registro Calórico</h2>
            <p class="text-xs text-sys-textMuted mb-4">Añade los macros ingeridos en esta ingesta. El sistema acumulará el total diario.</p>
            <div class="space-y-3">
              <div>
                <label class="text-[10px] font-mono text-sys-textMuted uppercase">Proteínas (g)</label>
                <input type="number" id="inpPro" class="sci-input" placeholder="Ej: 30">
              </div>
              <div>
                <label class="text-[10px] font-mono text-sys-textMuted uppercase">Carbohidratos (g)</label>
                <input type="number" id="inpCar" class="sci-input" placeholder="Ej: 50">
              </div>
              <div>
                <label class="text-[10px] font-mono text-sys-textMuted uppercase">Grasas (g)</label>
                <input type="number" id="inpFat" class="sci-input" placeholder="Ej: 15">
              </div>
              <button class="w-full bg-sys-success/20 border border-sys-success text-sys-success py-2 rounded font-mono text-sm mt-2 hover:bg-sys-success/30 transition-all" onclick="addMacros()">Inyectar Datos</button>
            </div>
          </div>
          <div class="mt-auto border-t border-sys-border pt-4">
            <h3 class="text-sm font-bold text-white mb-3">Hidratación (Litros)</h3>
            <div class="flex items-center justify-between bg-sys-base p-2 rounded border border-sys-border">
              <button class="text-sys-textMuted hover:text-white px-3 py-1" onclick="updateWater(-0.5)"><i class="fa-solid fa-minus"></i></button>
              <span class="font-mono text-xl text-blue-400 font-bold" id="waterDisplay">0.0</span>
              <button class="text-sys-textMuted hover:text-white px-3 py-1" onclick="updateWater(0.5)"><i class="fa-solid fa-plus"></i></button>
            </div>
            <p class="text-center text-[10px] text-sys-textMuted mt-2 font-mono">Objetivo: 3.0 L</p>
          </div>
        </div>
        
        <div class="col-span-1 lg:col-span-2 glass-panel p-6 flex flex-col">
          <h2 class="text-lg font-bold font-mono text-white border-b border-sys-border pb-2 mb-6">Estado de Recomposición</h2>
          <div class="mb-8">
            <div class="flex justify-between text-sm font-mono mb-2">
              <span class="text-sys-textMuted">Energía (Kcal)</span>
              <span class="text-white"><span id="nutKcalNum">0</span> / 2350</span>
            </div>
            <div class="w-full h-3 bg-sys-base rounded-full overflow-hidden border border-sys-border">
              <div id="nutKcalBar" class="h-full bg-gradient-to-r from-sys-accent to-blue-500 rounded-full transition-all duration-700" style="width: 0%;"></div>
            </div>
          </div>
          <div class="space-y-6">
            <div>
              <div class="flex justify-between text-sm font-mono mb-2">
                <span class="text-sys-accent">Proteína <span class="text-xs text-sys-textMuted ml-2">~ Síntesis Muscular</span></span>
                <span class="text-white"><span id="nutProNum">0</span> / 125g</span>
              </div>
              <div class="w-full h-2 bg-sys-base rounded-full overflow-hidden">
                <div id="nutProBar" class="h-full bg-sys-accent transition-all duration-700" style="width: 0%;"></div>
              </div>
            </div>
            <div>
              <div class="flex justify-between text-sm font-mono mb-2">
                <span class="text-purple-400">Carbohidratos <span class="text-xs text-sys-textMuted ml-2">~ Glucógeno & Rendimiento</span></span>
                <span class="text-white"><span id="nutCarNum">0</span> / 305g</span>
              </div>
              <div class="w-full h-2 bg-sys-base rounded-full overflow-hidden">
                <div id="nutCarBar" class="h-full bg-purple-400 transition-all duration-700" style="width: 0%;"></div>
              </div>
            </div>
            <div>
              <div class="flex justify-between text-sm font-mono mb-2">
                <span class="text-sys-warning">Grasas <span class="text-xs text-sys-textMuted ml-2">~ Blindaje Hormonal (T)</span></span>
                <span class="text-white"><span id="nutFatNum">0</span> / 70g</span>
              </div>
              <div class="w-full h-2 bg-sys-base rounded-full overflow-hidden">
                <div id="nutFatBar" class="h-full bg-sys-warning transition-all duration-700" style="width: 0%;"></div>
              </div>
            </div>
          </div>
          <div class="mt-auto pt-6 border-t border-sys-border mt-8">
            <p class="font-mono text-[11px] text-sys-textMuted leading-relaxed">
              <i class="fa-solid fa-triangle-exclamation text-sys-warning mr-2"></i> REGLA: Los datos no mienten. Si subes de peso sucio, corta 20g de carbohidratos. Si estancas en fuerza, suma 20g. La proteína es sagrada (125g min).
            </p>
          </div>
        </div>
      </div>
    </section>

    <section id="view-habits" class="view-section p-4 md:p-6 max-w-[800px] mx-auto">
      <div class="glass-panel p-6 flex flex-col">
        <div class="flex justify-between items-center mb-6 border-b border-sys-border pb-4">
          <h2 class="text-2xl font-bold font-mono text-white flex items-center gap-3">
            <i class="fa-solid fa-dna text-purple-400"></i> Protocolos Biológicos
          </h2>
          <span class="font-mono text-sm bg-purple-500/20 text-purple-400 px-3 py-1 rounded border border-purple-500/30">Activos: <span id="habitsActiveCount">0/6</span></span>
        </div>
        <div class="space-y-3" id="habitsList">
          </div>
      </div>
    </section>

    <section id="view-analytics" class="view-section p-4 md:p-6 max-w-[1200px] mx-auto">
      <div class="glass-panel p-6 flex flex-col">
        <div class="flex justify-between items-center mb-6 border-b border-sys-border pb-4">
          <h2 class="text-2xl font-bold font-mono text-white flex items-center gap-3">
            <i class="fa-solid fa-chart-radar text-sys-warning"></i> Matriz Mensual
          </h2>
          <div class="flex gap-4 items-center">
            <span class="text-sys-textMuted font-mono text-sm" id="calendarMonthLabel">JULIO 2026</span>
          </div>
        </div>
        <div class="grid grid-cols-7 gap-2 md:gap-4 mb-4" id="calendarHeader">
          <div class="text-center font-mono text-xs text-sys-textMuted py-2">DOM</div>
          <div class="text-center font-mono text-xs text-sys-textMuted py-2">LUN</div>
          <div class="text-center font-mono text-xs text-sys-textMuted py-2">MAR</div>
          <div class="text-center font-mono text-xs text-sys-textMuted py-2">MIE</div>
          <div class="text-center font-mono text-xs text-sys-textMuted py-2">JUE</div>
          <div class="text-center font-mono text-xs text-sys-textMuted py-2">VIE</div>
          <div class="text-center font-mono text-xs text-sys-textMuted py-2">SAB</div>
        </div>
        <div class="grid grid-cols-7 gap-2 md:gap-4 flex-1" id="calendarGrid">
          </div>
        <div class="mt-6 flex flex-wrap justify-center gap-6 border-t border-sys-border pt-4">
          <div class="flex items-center gap-2"><div class="w-3 h-3 bg-sys-panel border border-sys-border rounded"></div><span class="text-xs font-mono text-sys-textMuted">Vacío</span></div>
          <div class="flex items-center gap-2"><div class="w-3 h-3 bg-red-900/40 border border-red-500 rounded"></div><span class="text-xs font-mono text-sys-textMuted">Fallo</span></div>
          <div class="flex items-center gap-2"><div class="w-3 h-3 bg-sys-warning/30 border border-sys-warning rounded"></div><span class="text-xs font-mono text-sys-textMuted">Parcial</span></div>
          <div class="flex items-center gap-2"><div class="w-3 h-3 bg-sys-success/30 border border-sys-success rounded"></div><span class="text-xs font-mono text-sys-textMuted">Óptimo</span></div>
        </div>
      </div>
    </section>

  </main>

<script>
  const STORAGE_KEY = 'superman_jarvis_v3_native';
  
  const baseDayData = {
    training: { completed: false, log: {} }, 
    nutrition: { pro: 0, car: 0, fat: 0, water: 0 },
    habits: { sleep: false, sunlight: false, coldshower: false, creatine: false, deepwork: false, nonut: false }
  };

  const HITS_CONFIG = [
    { id: 'sleep', icon: 'fa-bed', name: 'Sueño 8h+', desc: 'Recuperación SNC' },
    { id: 'sunlight', icon: 'fa-sun', name: 'Luz Solar', desc: 'Ritmo Circadiano / Dopamina' },
    { id: 'coldshower', icon: 'fa-snowflake', name: 'Ducha Fría', desc: 'Resiliencia / Testosterona' },
    { id: 'creatine', icon: 'fa-capsules', name: 'Creatina 5g', desc: 'ATP / Volumen Celular' },
    { id: 'deepwork', icon: 'fa-brain', name: 'Trabajo Profundo', desc: 'Control de Cortisol' },
    { id: 'nonut', icon: 'fa-shield-halved', name: 'Retención', desc: 'Foco y Agresividad' }
  ];

  const ROUTINES = [
    { type: 'Rest', title: 'Recuperación Activa', exercises: [{name: 'Caminata 40m (Zona 2)', sets: 1, reps: '40m'}] },
    { type: 'Push', title: 'Empuje (Fuerza)', exercises: [{name: 'Press Militar', sets: 3, reps: '6-10'}, {name: 'Press Inclinado', sets: 3, reps: '6-8'}, {name: 'Fondos Lastrados', sets:3, reps:'8-12'}] },
    { type: 'Pull', title: 'Tracción (Fuerza)', exercises: [{name: 'Dominadas Lastradas', sets: 3, reps: '6-10'}, {name: 'Remo Barra', sets: 3, reps: '6-8'}, {name: 'Curl Martillo', sets:3, reps:'8-12'}] },
    { type: 'Legs', title: 'Pierna (Estructural)', exercises: [{name: 'Sentadilla Hack', sets: 4, reps: '6-8'}, {name: 'RDL', sets: 3, reps: '6-8'}, {name: 'Gemelos Pie', sets:4, reps:'10-12'}] },
    { type: 'Push', title: 'Empuje (Metabólico)', exercises: [{name: 'Press Mancuernas 30°', sets: 4, reps: '12-15'}, {name: 'Elevaciones Laterales', sets: 4, reps: '12-15'}] },
    { type: 'Pull', title: 'Tracción (Metabólico)', exercises: [{name: 'Remo Polea Baja', sets: 4, reps: '12-15'}, {name: 'Face Pull', sets: 3, reps: '12-15'}] },
    { type: 'Legs', title: 'Pierna (Metabólica)', exercises: [{name: 'Prensa', sets: 4, reps: '12-15'}, {name: 'Curl Isquios Sentado', sets: 4, reps: '12-15'}] }
  ];

  let appState = {};
  let currentDate = new Date();
  
  function formatDate(d) {
    const y = d.getFullYear();
    const m = String(d.getMonth() + 1).padStart(2, '0');
    const day = String(d.getDate()).padStart(2, '0');
    return `${y}-${m}-${day}`;
  }
  let dateStr = formatDate(currentDate);

  function getTodayData() {
    if (!appState[dateStr]) {
      appState[dateStr] = JSON.parse(JSON.stringify(baseDayData));
    }
    return appState[dateStr];
  }

  function saveData() {
    try { localStorage.setItem(STORAGE_KEY, JSON.stringify(appState)); } 
    catch(e) { console.warn("Storage bloqueado."); }
    updateDashboard(); 
  }

  function loadData() {
    try {
      const saved = localStorage.getItem(STORAGE_KEY);
      if (saved) appState = JSON.parse(saved);
    } catch(e) { appState = {}; }
    getTodayData(); 
  }

  window.resetData = function() {
    if(confirm("¿Restablecer métricas de hoy?")) {
      appState[dateStr] = JSON.parse(JSON.stringify(baseDayData));
      saveData();
      renderAllViews();
    }
  }

  const views = ['view-dashboard', 'view-training', 'view-nutrition', 'view-habits', 'view-analytics'];
  const btnBack = document.getElementById('btnBack');

  window.navigateTo = function(viewId) {
    views.forEach(v => document.getElementById(v).classList.remove('active'));
    document.getElementById(viewId).classList.add('active');

    if (viewId === 'view-dashboard') {
      btnBack.classList.add('hidden');
      updateDashboard();
    } else {
      btnBack.classList.remove('hidden');
    }
    // Sube el scroll siempre arriba al cambiar de vista
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }

  btnBack.addEventListener('click', () => navigateTo('view-dashboard'));

  function updateDashboard() {
    const data = getTodayData();
    const now = new Date();
    
    document.getElementById('sysClock').innerText = now.toLocaleTimeString('en-US', {hour12: false});
    document.getElementById('sysDate').innerText = now.toLocaleDateString('es-ES', { day: '2-digit', month: 'short', year: 'numeric' }).toUpperCase();
    
    const routine = ROUTINES[now.getDay()];
    document.getElementById('dashCycleDay').innerText = routine.type;

    let trainScore = data.training.completed ? 100 : 0;
    const kcal = (data.nutrition.pro * 4) + (data.nutrition.car * 4) + (data.nutrition.fat * 9);
    let dietScore = Math.min((kcal / 2350) * 100, 100);
    if(kcal > 2500) dietScore = Math.max(100 - ((kcal - 2500)/20), 50); 
    
    let habitsCompleted = 0;
    Object.values(data.habits).forEach(v => { if(v) habitsCompleted++; });
    let habitsScore = (habitsCompleted / HITS_CONFIG.length) * 100;

    const circumference = 251.2;
    setTimeout(() => {
      document.getElementById('donutTraining').style.strokeDashoffset = circumference - (trainScore / 100) * circumference;
      document.getElementById('donutDiet').style.strokeDashoffset = circumference - (dietScore / 100) * circumference;
      document.getElementById('donutHabits').style.strokeDashoffset = circumference - (habitsScore / 100) * circumference;
    }, 100);

    const totalPercent = Math.round((trainScore + dietScore + habitsScore) / 3);
    document.getElementById('dashPercent').innerText = totalPercent + '%';
    
    document.getElementById('legTraining').innerText = Math.round(trainScore) + '%';
    document.getElementById('legDiet').innerText = Math.round(dietScore) + '%';
    document.getElementById('legHabits').innerText = Math.round(habitsScore) + '%';
    
    let mods = 0;
    if(trainScore===100) mods++;
    if(dietScore>=90) mods++;
    if(habitsScore>=80) mods++;
    document.getElementById('dashTotalModules').innerText = `${mods}/3`;

    const cnsEl = document.getElementById('dashCNS');
    if(data.habits.sleep) {
      cnsEl.innerText = "Nominal"; cnsEl.className = "font-mono text-sm text-sys-success font-bold";
      document.getElementById('dashSleepVal').innerText = "8+ h";
    } else {
      cnsEl.innerText = "Deteriorado"; cnsEl.className = "font-mono text-sm text-sys-warning font-bold";
      document.getElementById('dashSleepVal').innerText = "< 6h";
    }

    const pPerc = Math.min((data.nutrition.pro / 125) * 100, 100);
    const cPerc = Math.min((data.nutrition.car / 305) * 100, 100);
    const fPerc = Math.min((data.nutrition.fat / 70) * 100, 100);
    
    setTimeout(() => {
      document.getElementById('miniPro').style.strokeDashoffset = 94.2 - (pPerc/100)*94.2;
      document.getElementById('miniCar').style.strokeDashoffset = 94.2 - (cPerc/100)*94.2;
      document.getElementById('miniFat').style.strokeDashoffset = 94.2 - (fPerc/100)*94.2;
    }, 100);
    document.getElementById('lblPro').innerText = Math.round(pPerc)+'%';
    document.getElementById('lblCar').innerText = Math.round(cPerc)+'%';
    document.getElementById('lblFat').innerText = Math.round(fPerc)+'%';

    const sched = document.getElementById('dashSchedule');
    sched.innerHTML = `
      <div class="flex items-center gap-4 bg-sys-panel/50 border border-sys-border rounded-lg p-3 hover:bg-sys-panel transition-all hover:-translate-y-0.5">
        <div class="w-8 h-8 rounded-full ${data.training.completed ? 'bg-sys-success/20 text-sys-success border border-sys-success/30' : 'bg-blue-500/20 text-blue-400 border border-blue-500/30'} flex items-center justify-center shrink-0">
          <i class="fa-solid fa-dumbbell"></i>
        </div>
        <div class="text-xs flex-1">
          <p class="font-bold text-white">${routine.title}</p>
          <p class="text-sys-textMuted font-mono text-[10px] mt-0.5">${data.training.completed ? 'Completado' : 'Pendiente'}</p>
        </div>
      </div>
      <div class="flex items-center gap-4 bg-sys-panel/50 border border-sys-border rounded-lg p-3 hover:bg-sys-panel transition-all hover:-translate-y-0.5">
        <div class="w-8 h-8 rounded-full ${data.nutrition.water >= 3 ? 'bg-sys-success/20 text-sys-success border border-sys-success/30' : 'bg-blue-500/20 text-blue-400 border border-blue-500/30'} flex items-center justify-center shrink-0">
          <i class="fa-solid fa-droplet"></i>
        </div>
        <div class="text-xs flex-1">
          <p class="font-bold text-white">Hidratación</p>
          <p class="text-sys-textMuted font-mono text-[10px] mt-0.5">${data.nutrition.water.toFixed(1)} / 3.0 Lts</p>
        </div>
      </div>
      <div class="flex items-center gap-4 bg-sys-panel/50 border border-sys-border rounded-lg p-3 hover:bg-sys-panel transition-all hover:-translate-y-0.5">
        <div class="w-8 h-8 rounded-full ${habitsCompleted >= 5 ? 'bg-sys-success/20 text-sys-success border border-sys-success/30' : 'bg-purple-500/20 text-purple-400 border border-purple-500/30'} flex items-center justify-center shrink-0">
          <i class="fa-solid fa-bolt"></i>
        </div>
        <div class="text-xs flex-1">
          <p class="font-bold text-white">Hábitos</p>
          <p class="text-sys-textMuted font-mono text-[10px] mt-0.5">${habitsCompleted} de ${HITS_CONFIG.length} verificados</p>
        </div>
      </div>
    `;
  }

  function renderTrainingView() {
    const data = getTodayData();
    const routine = ROUTINES[currentDate.getDay()];
    
    document.getElementById('trainTitle').innerText = routine.title;
    const statusEl = document.getElementById('trainStatus');
    if(data.training.completed) {
      statusEl.innerText = "EJECUTADO";
      statusEl.parentElement.className = "font-mono text-sm bg-sys-success/20 text-sys-success px-3 py-1 rounded border border-sys-success/30";
    } else {
      statusEl.innerText = "PENDIENTE";
      statusEl.parentElement.className = "font-mono text-sm bg-blue-500/20 text-blue-400 px-3 py-1 rounded border border-blue-500/30";
    }

    const listEl = document.getElementById('trainExerciseList');
    listEl.innerHTML = '';
    routine.exercises.forEach((ex, idx) => {
      let setsHtml = '';
      for(let s=1; s<=ex.sets; s++) {
        const logVal = data.training.log[`${idx}_s${s}`] || '';
        setsHtml += `
          <div class="flex items-center gap-2 mb-2">
            <span class="text-xs font-mono text-sys-textMuted w-16">Serie ${s}</span>
            <input type="number" id="ex_${idx}_s${s}_w" class="sci-input py-1 text-xs w-20" placeholder="Kg" value="${logVal ? logVal.split('-')[0] : ''}">
            <input type="number" id="ex_${idx}_s${s}_r" class="sci-input py-1 text-xs w-20" placeholder="Reps" value="${logVal ? logVal.split('-')[1] : ''}">
          </div>
        `;
      }
      listEl.innerHTML += `
        <div class="bg-sys-base border border-sys-border rounded p-4">
          <div class="flex justify-between items-center border-b border-sys-border/50 pb-2 mb-3">
            <h3 class="font-bold text-sm text-white">${ex.name}</h3>
            <span class="font-mono text-[10px] text-sys-textMuted bg-sys-panel px-2 py-0.5 rounded border border-sys-border">${ex.sets}x${ex.reps}</span>
          </div>
          <div class="pl-2 border-l border-sys-border/50">
            ${setsHtml}
          </div>
        </div>
      `;
    });
  }

  window.saveTraining = function() {
    const routine = ROUTINES[currentDate.getDay()];
    const data = getTodayData();
    routine.exercises.forEach((ex, idx) => {
      for(let s=1; s<=ex.sets; s++) {
        const w = document.getElementById(`ex_${idx}_s${s}_w`).value;
        const r = document.getElementById(`ex_${idx}_s${s}_r`).value;
        if(w || r) data.training.log[`${idx}_s${s}`] = `${w||0}-${r||0}`;
      }
    });
    saveData();
  }

  window.completeTraining = function() {
    const data = getTodayData();
    data.training.completed = true;
    saveData();
    renderTrainingView();
  }

  function renderNutritionView() {
    const data = getTodayData();
    document.getElementById('inpPro').value = '';
    document.getElementById('inpCar').value = '';
    document.getElementById('inpFat').value = '';

    const kcal = (data.nutrition.pro * 4) + (data.nutrition.car * 4) + (data.nutrition.fat * 9);
    document.getElementById('nutProNum').innerText = data.nutrition.pro;
    document.getElementById('nutCarNum').innerText = data.nutrition.car;
    document.getElementById('nutFatNum').innerText = data.nutrition.fat;
    document.getElementById('nutKcalNum').innerText = kcal;
    document.getElementById('waterDisplay').innerText = data.nutrition.water.toFixed(1);

    setTimeout(() => {
      document.getElementById('nutProBar').style.width = Math.min((data.nutrition.pro / 125)*100, 100) + '%';
      document.getElementById('nutCarBar').style.width = Math.min((data.nutrition.car / 305)*100, 100) + '%';
      document.getElementById('nutFatBar').style.width = Math.min((data.nutrition.fat / 70)*100, 100) + '%';
      document.getElementById('nutKcalBar').style.width = Math.min((kcal / 2350)*100, 100) + '%';
    }, 50);
  }

  window.addMacros = function() {
    const data = getTodayData();
    data.nutrition.pro += parseInt(document.getElementById('inpPro').value) || 0;
    data.nutrition.car += parseInt(document.getElementById('inpCar').value) || 0;
    data.nutrition.fat += parseInt(document.getElementById('inpFat').value) || 0;
    saveData();
    renderNutritionView();
  }

  window.updateWater = function(amt) {
    const data = getTodayData();
    data.nutrition.water = Math.max(0, data.nutrition.water + amt);
    saveData();
    renderNutritionView();
  }

  function renderHabitsView() {
    const data = getTodayData();
    const listEl = document.getElementById('habitsList');
    listEl.innerHTML = '';
    
    let active = 0;
    HITS_CONFIG.forEach(h => {
      const isChecked = data.habits[h.id];
      if(isChecked) active++;
      listEl.innerHTML += `
        <div class="bg-sys-base border ${isChecked ? 'border-sys-success/50' : 'border-sys-border'} rounded p-4 flex items-center justify-between transition-colors">
          <div class="flex items-center gap-4">
            <div class="w-10 h-10 rounded-full bg-sys-panel flex items-center justify-center border border-sys-border">
              <i class="fa-solid ${h.icon} ${isChecked ? 'text-sys-success' : 'text-sys-textMuted'} text-lg transition-colors"></i>
            </div>
            <div>
              <h3 class="font-bold text-sm text-white">${h.name}</h3>
              <p class="text-[10px] font-mono text-sys-textMuted uppercase">${h.desc}</p>
            </div>
          </div>
          <input type="checkbox" class="sci-checkbox" ${isChecked ? 'checked' : ''} onchange="toggleHabit('${h.id}')">
        </div>
      `;
    });
    document.getElementById('habitsActiveCount').innerText = `${active}/${HITS_CONFIG.length}`;
  }

  window.toggleHabit = function(id) {
    const data = getTodayData();
    data.habits[id] = !data.habits[id];
    saveData();
    renderHabitsView();
  }

  function renderAnalyticsView() {
    const grid = document.getElementById('calendarGrid');
    grid.innerHTML = '';
    const year = currentDate.getFullYear();
    const month = currentDate.getMonth();
    const monthNames = ["ENERO", "FEBRERO", "MARZO", "ABRIL", "MAYO", "JUNIO", "JULIO", "AGOSTO", "SEPTIEMBRE", "OCTUBRE", "NOVIEMBRE", "DICIEMBRE"];
    document.getElementById('calendarMonthLabel').innerText = `${monthNames[month]} ${year}`;

    const firstDay = new Date(year, month, 1).getDay(); 
    const daysInMonth = new Date(year, month + 1, 0).getDate();

    for(let i=0; i<firstDay; i++) {
      grid.innerHTML += `<div class="aspect-square bg-transparent rounded border border-transparent"></div>`;
    }

    for(let i=1; i<=daysInMonth; i++) {
      const loopDateStr = `${year}-${String(month+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
      const dayData = appState[loopDateStr];
      let statusClass = "bg-sys-base border-sys-border opacity-50";
      
      if(dayData) {
        let tScore = dayData.training.completed ? 1 : 0;
        let dScore = ((dayData.nutrition.pro*4 + dayData.nutrition.car*4 + dayData.nutrition.fat*9) >= 2000) ? 1 : 0; 
        let hCount = 0; Object.values(dayData.habits).forEach(v => { if(v) hCount++; });
        let hScore = (hCount / 6) >= 0.8 ? 1 : 0;
        const total = tScore + dScore + hScore;
        
        if (total === 3) statusClass = "bg-sys-success/20 border-sys-success text-sys-success shadow-[0_0_10px_rgba(0,255,136,0.2)]"; 
        else if (total > 0) statusClass = "bg-sys-warning/20 border-sys-warning text-sys-warning";
        else statusClass = "bg-red-900/40 border-red-500 text-red-500"; 
      }
      const isToday = loopDateStr === dateStr ? "ring-2 ring-white" : "";
      grid.innerHTML += `
        <div class="aspect-square rounded border flex flex-col items-center justify-center relative ${statusClass} ${isToday} transition-all hover:scale-105 cursor-pointer">
          <span class="font-mono text-sm font-bold">${i}</span>
          ${dayData && dayData.training.completed ? '<div class="absolute bottom-1 w-1 h-1 rounded-full bg-current"></div>' : ''}
        </div>
      `;
    }
  }

  function renderAllViews() {
    renderTrainingView();
    renderNutritionView();
    renderHabitsView();
    renderAnalyticsView();
    updateDashboard();
  }

  window.onload = () => {
    loadData();
    renderAllViews();
    setInterval(() => {
      document.getElementById('sysClock').innerText = new Date().toLocaleTimeString('en-US', {hour12: false});
    }, 1000);
  };
</script>
</body>
</html>
