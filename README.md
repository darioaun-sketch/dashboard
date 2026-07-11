<!DOCTYPE html>
<html lang="es" class="dark">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>JARVIS — Proyecto Superman V3</title>
<!-- Configuramos Tailwind ANTES de cargar el script -->
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
<!-- Tailwind CSS -->
<script src="https://cdn.tailwindcss.com"></script>
<!-- FontAwesome -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<!-- Google Fonts -->
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
    overflow: hidden;
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

  ::-webkit-scrollbar { width: 6px; height: 6px; }
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

  /* Animaciones 100% en CSS puro (Soluciona el problema de pantalla negra) */
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

  /* Elementos Visuales */
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

  .sci-input {
    background: rgba(0,0,0,0.3); border: 1px solid #1a2744; color: #00f0ff;
    font-family: 'JetBrains Mono', monospace; padding: 8px 12px; border-radius: 6px; width: 100%; outline: none;
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

  .view-section { display: none; opacity: 0; }
  .view-section.active {
    display: flex; flex-direction: column;
    animation: customFadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1) both;
  }
</style>
</head>
<body class="h-screen w-screen flex flex-col font-sans">
  
  <div class="bg-grid"></div>

  <header class="h-16 border-b border-sys-border bg-sys-base/80 backdrop-blur-md flex items-center justify-between px-6 shrink-0 z-50">
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
        <span class="font-mono text-xs text-sys-success font-medium">SISTEMA ÓPTIMO</span>
      </div>
      <div id="sysClock" class="font-mono text-sm text-white font-medium">--:--:--</div>
      <div id="sysDate" class="font-mono text-xs text-sys-textMuted uppercase hidden sm:block">-- --- ----</div>
    </div>
  </header>

  <main class="flex-1 overflow-y-auto overflow-x-hidden relative" id="workspace">

    <!-- ========================================== -->
    <!-- VIEW: DASHBOARD (HUB)                      -->
    <!-- ========================================== -->
    <section id="view-dashboard" class="view-section active p-4 md:p-6 w-full max-w-[1600px] mx-auto h-full">
      
      <!-- Action Cards -->
      <div class="grid grid-cols-2 md:grid-cols-4 gap-4 md:gap-6 mb-6 shrink-0">
        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-1" onclick="navigateTo('view-training')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-blue-500/10 rounded-full blur-2xl group-hover:bg-blue-500/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-blue-500/20 flex items-center justify-center border border-blue-500/30">
              <i class="fa-solid fa-dumbbell text-blue-400 text-lg"></i>
            </div>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Entrenamiento</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">PPL x2 / Fuerza</p>
          </div>
        </div>
        
        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-2" onclick="navigateTo('view-nutrition')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-sys-success/10 rounded-full blur-2xl group-hover:bg-sys-success/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-sys-success/20 flex items-center justify-center border border-sys-success/30">
              <i class="fa-solid fa-flask text-sys-success text-lg"></i>
            </div>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Nutrición & Macros</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">2350 kcal</p>
          </div>
        </div>

        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-3" onclick="navigateTo('view-habits')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-purple-500/10 rounded-full blur-2xl group-hover:bg-purple-500/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-purple-500/20 flex items-center justify-center border border-purple-500/30">
              <i class="fa-solid fa-dna text-purple-400 text-lg"></i>
            </div>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Protocolos T</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">Bio-Hábitos</p>
          </div>
        </div>

        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-4" onclick="navigateTo('view-analytics')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-sys-warning/10 rounded-full blur-2xl group-hover:bg-sys-warning/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-sys-warning/20 flex items-center justify-center border border-sys-warning/30">
              <i class="fa-solid fa-chart-radar text-sys-warning text-lg"></i>
            </div>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Analítica</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">Progreso Mensual</p>
          </div>
        </div>
      </div>

      <!-- Main Layout -->
      <div class="grid grid-cols-1 md:grid-cols-12 gap-4 md:gap-6 flex-1 min-h-[500px]">
        
        <div class="col-span-1 md:col-span-4 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-2">
          <div class="glass-panel p-5 flex flex-col gap-4">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-2">Estado del Operador</h2>
            <div class="flex items-center gap-4">
              <div class="w-12 h-12 bg-sys-panel border border-sys-accent/30 rounded-lg flex items-center justify-center font-mono font-bold text-lg text-white shadow-[0_0_15px_rgba(0,240,255,0.2)] animate-pulse-slow">SM</div>
              <div>
                <h3 class="font-bold text-sm tracking-wide">Superman Protocol</h3>
                <div class="flex gap-2 text-[10px] font-mono mt-1">
                  <span class="bg-sys-success/20 text-sys-success px-2 py-0.5 rounded border border-sys-success/30">Fase 3</span>
                </div>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-3 mt-2">
              <div class="bg-sys-panel/50 rounded-lg border border-sys-border p-3">
                <p class="text-[10px] text-sys-textMuted">Descanso Prev.</p>
                <p class="font-mono text-white text-sm mt-1" id="dashSleepVal">-- h</p>
              </div>
              <div class="bg-sys-panel/50 rounded-lg border border-sys-border p-3">
                <p class="text-[10px] text-sys-textMuted">Día del Ciclo</p>
                <p class="font-mono text-white text-sm mt-1" id="dashCycleDay">--</p>
              </div>
            </div>
          </div>

          <div class="glass-panel p-5 flex-1 flex flex-col items-center justify-center relative min-h-[250px] overflow-hidden">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold absolute top-5 left-5">Biometría</h2>
            <div class="w-40 h-40 mt-6 relative animate-float">
              <div class="radar-container">
                <div class="radar-sweep animate-radar-sweep"></div>
              </div>
            </div>
            <p class="font-mono text-[10px] text-sys-accent mt-6 text-center uppercase bg-sys-accent/10 px-3 py-1 rounded-full border border-sys-accent/20">Escaneando...</p>
          </div>
        </div>

        <div class="col-span-1 md:col-span-5 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-3">
          <div class="glass-panel p-6 flex flex-col">
            <div class="flex justify-between items-center mb-8">
              <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold">Resumen Diario</h2>
              <span class="text-[10px] font-mono bg-sys-panel border border-sys-border px-3 py-1 rounded-full shadow-inner">Módulos: <span id="dashTotalModules" class="text-white">0/3</span></span>
            </div>
            <div class="flex flex-col sm:flex-row items-center justify-center gap-8 md:gap-12 flex-1">
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
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border">
                  <div class="flex items-center gap-2"><div class="w-2.5 h-2.5 rounded-full bg-blue-500 shadow-[0_0_8px_rgba(59,130,246,0.6)]"></div><span class="text-sys-textMuted">Entreno</span></div>
                  <span class="font-mono text-white font-bold" id="legTraining">0%</span>
                </div>
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border">
                  <div class="flex items-center gap-2"><div class="w-2.5 h-2.5 rounded-full bg-sys-success shadow-[0_0_8px_rgba(0,255,136,0.6)]"></div><span class="text-sys-textMuted">Nutrición</span></div>
                  <span class="font-mono text-white font-bold" id="legDiet">0%</span>
                </div>
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border">
                  <div class="flex items-center gap-2"><div class="w-2.5 h-2.5 rounded-full bg-purple-500 shadow-[0_0_8px_rgba(176,38,255,0.6)]"></div><span class="text-sys-textMuted">Hábitos</span></div>
                  <span class="font-mono text-white font-bold" id="legHabits">0%</span>
                </div>
              </div>
            </div>
          </div>
          <div class="glass-panel p-5 flex-1 flex flex-col relative overflow-hidden">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-4">Agenda</h2>
            <div class="flex-1 overflow-y-auto space-y-3 pr-2 custom-scrollbar" id="dashSchedule"></div>
          </div>
        </div>

        <div class="col-span-1 md:col-span-3 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-4">
          <div class="glass-panel p-5 relative overflow-hidden">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-5">Estado Físico</h2>
            <div class="flex items-center justify-between bg-sys-panel/60 border border-sys-border rounded-lg p-3 mb-3">
              <span class="text-xs text-sys-textMuted">Testosterona Est.</span>
              <span class="font-mono text-sm text-sys-success font-bold flex items-center gap-2">Óptimo</span>
            </div>
            <div class="flex items-center justify-between bg-sys-panel/60 border border-sys-border rounded-lg p-3">
              <span class="text-xs text-sys-textMuted">Fatiga SNC</span>
              <span class="font-mono text-sm text-sys-accent font-bold" id="dashCNS">Nominal</span>
            </div>
          </div>

          <div class="glass-panel p-5 flex-1 flex flex-col">
            <div class="flex justify-between items-center mb-6">
              <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold">Macros</h2>
            </div>
            <div class="flex justify-between items-end gap-2 h-full pb-2">
              <div class="flex flex-col items-center gap-3 w-full">
                <span class="font-mono text-[10px] text-sys-textMuted">PRO</span>
                <div class="relative w-14 h-14">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniPro" class="donut-fill" cx="18" cy="18" r="15" stroke="#00f0ff" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[10px] font-bold text-white" id="lblPro">0%</span>
                </div>
              </div>
              <div class="flex flex-col items-center gap-3 w-full">
                <span class="font-mono text-[10px] text-sys-textMuted">CAR</span>
                <div class="relative w-14 h-14">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniCar" class="donut-fill" cx="18" cy="18" r="15" stroke="#b026ff" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[10px] font-bold text-white" id="lblCar">0%</span>
                </div>
              </div>
              <div class="flex flex-col items-center gap-3 w-full">
                <span class="font-mono text-[10px] text-sys-textMuted">GRA</span>
                <div class="relative w-14 h-14">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniFat" class="donut-fill" cx="18" cy="18" r="15" stroke="#ffb800" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[10px] font-bold text-white" id="lblFat">0%</span>
                </div>
              </div>
            </div>
          </div>
        </div>

      </div>
    </section>

    <!-- VIEWS SIMPLIFICADAS PARA AHORRAR ESPACIO EN EL EJEMPLO, MANTIENEN SU ESTRUCTURA HTML -->
    <section id="view-training" class="view-section p-4 md:p-6 max-w-[1000px] mx-auto h-full">
      <div class="glass-panel p-6 flex flex-col h-full">
        <h2 class="text-2xl font-bold font-mono text-white mb-6">Módulo de Entrenamiento</h2>
        <div class="flex-1 overflow-y-auto pr-2 space-y-4" id="trainExerciseList"></div>
      </div>
    </section>

    <section id="view-nutrition" class="view-section p-4 md:p-6 max-w-[1000px] mx-auto h-full">
      <div class="glass-panel p-6 flex flex-col h-full"><h2 class="text-2xl font-bold text-white">Módulo Nutricional (En línea)</h2></div>
    </section>
    
    <section id="view-habits" class="view-section p-4 md:p-6 max-w-[800px] mx-auto h-full">
      <div class="glass-panel p-6 h-full flex flex-col"><div class="flex-1 overflow-y-auto space-y-3" id="habitsList"></div></div>
    </section>

    <section id="view-analytics" class="view-section p-4 md:p-6 max-w-[1200px] mx-auto h-full">
      <div class="glass-panel p-6 h-full flex flex-col"><div class="grid grid-cols-7 gap-2 flex-1" id="calendarGrid"></div></div>
    </section>

  </main>

<script>
  const STORAGE_KEY = 'superman_jarvis_v3_safe';
  
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
    { type: 'Rest', title: 'Recuperación Activa', exercises: [{name: 'Caminata 40m', sets: 1, reps: '40m'}] },
    { type: 'Push', title: 'Empuje (Fuerza)', exercises: [{name: 'Press Militar', sets: 3, reps: '6-10'}] },
    { type: 'Pull', title: 'Tracción (Fuerza)', exercises: [{name: 'Dominadas', sets: 3, reps: '6-10'}] },
    { type: 'Legs', title: 'Pierna (Estructural)', exercises: [{name: 'Sentadilla Hack', sets: 4, reps: '6-8'}] },
    { type: 'Push', title: 'Empuje (Metabólico)', exercises: [{name: 'Press Mancuernas', sets: 4, reps: '12-15'}] },
    { type: 'Pull', title: 'Tracción (Metabólico)', exercises: [{name: 'Remo Polea Baja', sets: 4, reps: '12-15'}] },
    { type: 'Legs', title: 'Pierna (Metabólica)', exercises: [{name: 'Prensa', sets: 4, reps: '12-15'}] }
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
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(appState));
    } catch(e) {
      console.warn("Navegador bloqueó localStorage. Funciona solo en memoria.");
    }
    updateDashboard(); 
  }

  function loadData() {
    try {
      const saved = localStorage.getItem(STORAGE_KEY);
      if (saved) appState = JSON.parse(saved);
    } catch(e) {
      console.warn("Storage bloqueado. Empezando de cero.");
      appState = {};
    }
    getTodayData();
  }

  const views = ['view-dashboard', 'view-training', 'view-nutrition', 'view-habits', 'view-analytics'];
  
  function navigateTo(viewId) {
    views.forEach(v => document.getElementById(v).classList.remove('active'));
    document.getElementById(viewId).classList.add('active');
    
    if (viewId === 'view-dashboard') {
      document.getElementById('btnBack').classList.add('hidden');
      updateDashboard();
    } else {
      document.getElementById('btnBack').classList.remove('hidden');
    }
  }

  document.getElementById('btnBack').addEventListener('click', () => navigateTo('view-dashboard'));

  function updateDashboard() {
    const data = getTodayData();
    const now = new Date();
    
    document.getElementById('sysClock').innerText = now.toLocaleTimeString('en-US', {hour12: false});
    const routine = ROUTINES[now.getDay()];
    document.getElementById('dashCycleDay').innerText = routine.type;

    let trainScore = data.training.completed ? 100 : 0;
    const kcal = (data.nutrition.pro * 4) + (data.nutrition.car * 4) + (data.nutrition.fat * 9);
    let dietScore = Math.min((kcal / 2350) * 100, 100);
    
    let habitsCompleted = 0;
    Object.values(data.habits).forEach(v => { if(v) habitsCompleted++; });
    let habitsScore = (habitsCompleted / 6) * 100;

    setTimeout(() => {
      document.getElementById('donutTraining').style.strokeDashoffset = 251.2 - (trainScore / 100) * 251.2;
      document.getElementById('donutDiet').style.strokeDashoffset = 251.2 - (dietScore / 100) * 251.2;
      document.getElementById('donutHabits').style.strokeDashoffset = 251.2 - (habitsScore / 100) * 251.2;
    }, 100);

    document.getElementById('dashPercent').innerText = Math.round((trainScore + dietScore + habitsScore) / 3) + '%';
    document.getElementById('legTraining').innerText = Math.round(trainScore) + '%';
    document.getElementById('legDiet').innerText = Math.round(dietScore) + '%';
    document.getElementById('legHabits').innerText = Math.round(habitsScore) + '%';
  }

  function renderHabitsView() {
    const data = getTodayData();
    const listEl = document.getElementById('habitsList');
    listEl.innerHTML = '';
    
    HITS_CONFIG.forEach(h => {
      const isChecked = data.habits[h.id];
      listEl.innerHTML += `
        <div class="bg-sys-base border ${isChecked ? 'border-sys-success/50' : 'border-sys-border'} rounded p-4 flex justify-between">
          <div class="flex items-center gap-4">
            <i class="fa-solid ${h.icon} ${isChecked ? 'text-sys-success' : 'text-sys-textMuted'} text-lg"></i>
            <div>
              <h3 class="font-bold text-sm text-white">${h.name}</h3>
              <p class="text-[10px] text-sys-textMuted">${h.desc}</p>
            </div>
          </div>
          <input type="checkbox" class="sci-checkbox" ${isChecked ? 'checked' : ''} onchange="toggleHabit('${h.id}')">
        </div>
      `;
    });
  }

  window.toggleHabit = function(id) {
    const data = getTodayData();
    data.habits[id] = !data.habits[id];
    saveData();
    renderHabitsView();
  };

  window.onload = () => {
    loadData();
    renderHabitsView();
    updateDashboard();
    setInterval(() => {
      document.getElementById('sysClock').innerText = new Date().toLocaleTimeString('en-US', {hour12: false});
    }, 1000);
  };
</script>
</body>
</html>
