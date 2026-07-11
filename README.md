<!DOCTYPE html>
<html lang="es" class="dark">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>JARVIS — Proyecto Superman V3.1</title>

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
    transform: translateY(-2px);
  }

  .animate-slide-up { animation: slideUp 0.5s cubic-bezier(0.16, 1, 0.3, 1) both; }
  .animate-pulse-slow { animation: pulseSlow 3s cubic-bezier(0.4, 0, 0.6, 1) infinite; }
  .animate-float { animation: float 6s ease-in-out infinite; }
  .animate-radar-sweep { animation: sweep 4s linear infinite; }

  @keyframes slideUp {
    0% { opacity: 0; transform: translateY(20px); }
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
    50% { transform: translateY(-6px); }
  }

  .stagger-1 { animation-delay: 0.05s; }
  .stagger-2 { animation-delay: 0.1s; }
  .stagger-3 { animation-delay: 0.15s; }
  .stagger-4 { animation-delay: 0.2s; }
  .stagger-5 { animation-delay: 0.25s; }

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
  .donut-fill { fill: none; stroke-width: 8; stroke-linecap: round; transition: stroke-dashoffset 1.2s ease-out; }

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

  .view-section { display: none; opacity: 0; }
  .view-section.active {
    display: flex; flex-direction: column;
    animation: customFadeIn 0.3s ease-out both;
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
          <p class="font-mono text-[10px] text-sys-textMuted uppercase">Intelligence System v3.1</p>
        </div>
      </div>
      
      <button type="button" id="btnBack" class="hidden ml-6 text-sys-textMuted hover:text-white flex items-center gap-2 text-sm transition-colors border border-sys-border px-3 py-1.5 rounded-md bg-sys-panel hover:bg-sys-panelHover">
        <i class="fa-solid fa-arrow-left"></i> Panel Principal
      </button>
    </div>

    <div class="flex items-center gap-3 bg-sys-panel/80 border border-sys-accent/30 rounded-lg px-4 py-1.5 shadow-inner">
      <i class="fa-solid fa-timeline text-sys-accent text-xs"></i>
      <span class="font-mono text-xs text-sys-textMuted">AUDITANDO:</span>
      <span id="timelineIndicator" class="font-mono text-xs text-white font-bold">HOY</span>
      <button type="button" id="btnTodayReset" class="hidden bg-sys-accent/10 hover:bg-sys-accent/20 text-sys-accent text-[10px] font-mono font-bold px-2 py-0.5 rounded border border-sys-accent/30 transition-all ml-1" onclick="resetToToday()">VOLVER A HOY</button>
    </div>

    <div class="flex items-center gap-6">
      <div class="flex items-center gap-2 px-4 py-1.5 rounded-full bg-green-900/20 border border-green-500/30">
        <div class="w-2 h-2 rounded-full bg-sys-success animate-pulse"></div>
        <span class="font-mono text-xs text-sys-success font-medium hidden sm:block">SISTEMA ÓPTIMO</span>
      </div>
      <div id="sysClock" class="font-mono text-sm text-white font-medium">--:--:--</div>
    </div>
    
    <div class="flex items-center gap-4 text-sys-textMuted hidden md:flex">
      <button type="button" class="hover:text-white transition-colors" onclick="resetData()" title="Restablecer día actual"><i class="fa-solid fa-arrows-rotate text-sm"></i></button>
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
            <p class="text-sys-textMuted font-mono text-[10px] mt-1" id="dashRoutineSummary">Cargando protocolo...</p>
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
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">2150 kcal / Recomposición</p>
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
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">Hormonal & Disciplina</p>
          </div>
        </div>

        <div class="glass-panel clickable p-5 flex flex-col justify-between group h-36 relative overflow-hidden animate-slide-up stagger-4" onclick="navigateTo('view-analytics')">
          <div class="absolute -right-6 -top-6 w-24 h-24 bg-sys-warning/10 rounded-full blur-2xl group-hover:bg-sys-warning/20 transition-all duration-500"></div>
          <div class="flex justify-between items-start">
            <div class="w-10 h-10 rounded-lg bg-sys-warning/20 flex items-center justify-center border border-sys-warning/30 group-hover:scale-110 transition-transform duration-300">
              <i class="fa-solid fa-calendar-days text-sys-warning text-lg"></i>
            </div>
            <i class="fa-solid fa-arrow-right text-sys-border group-hover:text-sys-warning transition-colors group-hover:translate-x-1 duration-300"></i>
          </div>
          <div class="mt-4">
            <h3 class="text-white font-bold text-sm tracking-wide">Matriz Mensual</h3>
            <p class="text-sys-textMuted font-mono text-[10px] mt-1">Historial e Índices Dinámicos</p>
          </div>
        </div>
      </div>

      <div class="glass-panel p-5 mb-6 flex flex-col animate-slide-up stagger-5">
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-4 gap-2">
          <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold flex items-center gap-2">
            <i class="fa-solid fa-chart-line text-sys-accent"></i> Vector de Optimización Mensual (Historial de Tendencia)
          </h2>
          <div class="flex items-center gap-2 text-[11px] font-mono">
            <span class="text-sys-textMuted">EFICIENCIA MEDIA DEL MES:</span>
            <span id="dashMonthlyAvg" class="text-sys-accent font-bold">0%</span>
          </div>
        </div>
        <div class="w-full bg-sys-base/60 border border-sys-border rounded-xl p-4 flex flex-col justify-between h-36 relative">
          <svg id="monthlyTrendGraphDash" class="w-full h-full" preserveAspectRatio="none" viewBox="0 0 700 120"></svg>
          <div class="absolute inset-0 flex items-center justify-center pointer-events-none id-empty-graph-msg hidden">
            <span class="font-mono text-[10px] text-sys-textMuted">Sin registros históricos detectados en este mes.</span>
          </div>
        </div>
        <div class="flex justify-between text-[9px] font-mono text-sys-textMuted mt-2 px-1">
          <span>INICIO DE MES</span>
          <span>COMPORTAMIENTO METABÓLICO Y DE COMPROMISO</span>
          <span>FIN DE MES</span>
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
                  <span class="bg-blue-500/20 text-blue-400 px-2 py-0.5 rounded border border-blue-500/30">Déficit Activo</span>
                </div>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-3 mt-2">
              <div class="bg-sys-panel/50 rounded-lg border border-sys-border p-3 transition-colors hover:bg-sys-panel">
                <p class="text-[10px] text-sys-textMuted">Estado CNS</p>
                <p class="font-mono text-white text-sm mt-1" id="dashCNS">Nominal</p>
              </div>
              <div class="bg-sys-panel/50 rounded-lg border border-sys-border p-3 transition-colors hover:bg-sys-panel">
                <p class="text-[10px] text-sys-textMuted">Bloque Constructor</p>
                <p class="font-mono text-white text-sm mt-1">140g PRO</p>
              </div>
            </div>
          </div>

          <div class="glass-panel p-5 flex-1 flex flex-col items-center justify-center relative min-h-[230px] overflow-hidden">
            <div class="absolute inset-0 bg-gradient-to-b from-transparent to-sys-accent/5 pointer-events-none"></div>
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold absolute top-5 left-5">Biometría Radar</h2>
            <div class="w-36 h-36 mt-4 relative animate-float">
              <div class="radar-container">
                <div class="radar-sweep animate-radar-sweep"></div>
                <div class="absolute w-2 h-2 bg-sys-accent rounded-full top-[30%] left-[60%] shadow-[0_0_10px_#00f0ff] animate-pulse"></div>
                <div class="absolute w-2 h-2 bg-sys-success rounded-full bottom-[40%] right-[30%] shadow-[0_0_10px_#00ff88]"></div>
              </div>
            </div>
          </div>
        </div>

        <div class="col-span-1 xl:col-span-5 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-3">
          <div class="glass-panel p-6 flex flex-col">
            <div class="flex justify-between items-center mb-6">
              <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold">Consistencia del Día Auditado</h2>
              <span class="text-[10px] font-mono bg-sys-panel border border-sys-border px-3 py-1 rounded-full shadow-inner">Completado: <span id="dashTotalModules" class="text-white">0/3</span></span>
            </div>
            <div class="flex flex-col sm:flex-row items-center justify-center gap-8 py-2">
              <div class="relative w-36 h-36 flex-shrink-0 animate-float">
                <svg viewBox="0 0 100 100" class="donut-chart w-full h-full">
                  <circle class="donut-bg" cx="50" cy="50" r="40"></circle>
                  <circle id="donutTraining" class="donut-fill" cx="50" cy="50" r="40" stroke="#3b82f6" stroke-dasharray="251.2" stroke-dashoffset="251.2"></circle>
                  <circle id="donutDiet" class="donut-fill" cx="50" cy="50" r="40" stroke="#00ff88" stroke-dasharray="251.2" stroke-dashoffset="251.2"></circle>
                  <circle id="donutHabits" class="donut-fill" cx="50" cy="50" r="40" stroke="#b026ff" stroke-dasharray="251.2" stroke-dashoffset="251.2"></circle>
                </svg>
                <div class="absolute inset-0 flex flex-col items-center justify-center bg-sys-panel/30 rounded-full m-4 border border-sys-border/50 backdrop-blur-sm">
                  <span class="font-mono text-2xl font-bold text-white drop-shadow-md" id="dashPercent">0%</span>
                </div>
              </div>
              <div class="flex flex-col gap-3 w-full sm:w-auto min-w-[150px]">
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border">
                  <div class="flex items-center gap-2"><div class="w-2 h-2 rounded-full bg-blue-500"></div><span class="text-sys-textMuted">Combate</span></div>
                  <span class="font-mono text-white font-bold" id="legTraining">0%</span>
                </div>
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border">
                  <div class="flex items-center gap-2"><div class="w-2 h-2 rounded-full bg-sys-success"></div><span class="text-sys-textMuted">Nutrición</span></div>
                  <span class="font-mono text-white font-bold" id="legDiet">0%</span>
                </div>
                <div class="flex items-center justify-between text-xs bg-sys-panel/40 p-2 rounded border border-sys-border">
                  <div class="flex items-center gap-2"><div class="w-2 h-2 rounded-full bg-purple-500"></div><span class="text-sys-textMuted">Hábitos</span></div>
                  <span class="font-mono text-white font-bold" id="legHabits">0%</span>
                </div>
              </div>
            </div>
          </div>
          <div class="glass-panel p-5 flex-1 flex flex-col min-h-[200px]">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-4">Agenda Operativa</h2>
            <div class="space-y-2.5" id="dashSchedule"></div>
          </div>
        </div>

        <div class="col-span-1 xl:col-span-3 flex flex-col gap-4 md:gap-6 animate-slide-up stagger-4">
          <div class="glass-panel p-5">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-4">Blindaje Hormonal</h2>
            <div class="bg-sys-panel/60 border border-sys-border rounded-lg p-3 flex justify-between items-center">
              <span class="text-xs text-sys-textMuted">Grasas Mínimas</span>
              <span class="font-mono text-xs text-sys-warning font-bold">70g Innegociables</span>
            </div>
          </div>

          <div class="glass-panel p-5 flex-1 flex flex-col min-h-[220px]">
            <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-6">Inyección de Macronutrientes</h2>
            <div class="flex justify-between items-end gap-2 h-full pb-2">
              <div class="flex flex-col items-center gap-2 w-full group">
                <span class="font-mono text-[10px] text-sys-accent font-bold">PRO (140g)</span>
                <div class="relative w-14 h-14">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniPro" class="donut-fill" cx="18" cy="18" r="15" stroke="#00f0ff" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[9px] font-bold text-white" id="lblPro">0%</span>
                </div>
              </div>
              <div class="flex flex-col items-center gap-2 w-full group">
                <span class="font-mono text-[10px] text-purple-400 font-bold">CAR (240g)</span>
                <div class="relative w-14 h-14">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniCar" class="donut-fill" cx="18" cy="18" r="15" stroke="#b026ff" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[9px] font-bold text-white" id="lblCar">0%</span>
                </div>
              </div>
              <div class="flex flex-col items-center gap-2 w-full group">
                <span class="font-mono text-[10px] text-sys-warning font-bold">GRA (70g)</span>
                <div class="relative w-14 h-14">
                  <svg viewBox="0 0 36 36" class="w-full h-full transform -rotate-90"><circle class="donut-bg" cx="18" cy="18" r="15" stroke-width="3"></circle><circle id="miniFat" class="donut-fill" cx="18" cy="18" r="15" stroke="#ffb800" stroke-width="3" stroke-dasharray="94.2" stroke-dashoffset="94.2"></circle></svg>
                  <span class="absolute inset-0 flex items-center justify-center font-mono text-[9px] font-bold text-white" id="lblFat">0%</span>
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
            <h2 class="text-xl font-bold font-mono text-white flex items-center gap-3" id="trainMainHeading">
              <i class="fa-solid fa-dumbbell text-blue-400"></i> Módulo de Combate
            </h2>
            <p class="text-sys-textMuted text-xs mt-1.5 font-mono" id="trainSubDesc">Cargando meta de volumen...</p>
          </div>
          <div class="text-right">
            <span class="font-mono text-xs bg-blue-500/20 text-blue-400 px-3 py-1 rounded border border-blue-500/30">Estado: <span id="trainStatus">Pendiente</span></span>
          </div>
        </div>
        <div class="space-y-4" id="trainExerciseList"></div>
        <div class="mt-6 pt-4 border-t border-sys-border flex flex-wrap justify-end gap-4">
          <button type="button" class="bg-sys-panel border border-sys-border text-white px-6 py-2 rounded font-mono text-xs hover:bg-sys-border transition-colors" onclick="saveTraining()">Guardar Parámetros de Series</button>
          <button type="button" class="bg-blue-600 hover:bg-blue-500 text-white px-6 py-2 rounded font-mono text-xs transition-colors" onclick="completeTraining()">Marcar Rutina Completada</button>
        </div>
      </div>
    </section>

    <section id="view-nutrition" class="view-section p-4 md:p-6 max-w-[1100px] mx-auto">
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div class="col-span-1 glass-panel p-6 flex flex-col gap-6">
          <div>
            <h2 class="text-base font-bold font-mono text-white border-b border-sys-border pb-2 mb-4">Registro Calórico</h2>
            <div class="space-y-3">
              <div>
                <label class="text-[10px] font-mono text-sys-textMuted uppercase">Proteínas (g) <span class="text-sys-accent">[Max 140g]</span></label>
                <input type="number" id="inpPro" class="sci-input mt-1" placeholder="Ej: 35">
              </div>
              <div>
                <label class="text-[10px] font-mono text-sys-textMuted uppercase">Carbohidratos (g) <span class="text-purple-400">[Max 240g]</span></label>
                <input type="number" id="inpCar" class="sci-input mt-1" placeholder="Ej: 60">
              </div>
              <div>
                <label class="text-[10px] font-mono text-sys-textMuted uppercase">Grasas (g) <span class="text-sys-warning">[Max 70g]</span></label>
                <input type="number" id="inpFat" class="sci-input mt-1" placeholder="Ej: 15">
              </div>
              <button type="button" class="w-full bg-sys-success/20 border border-sys-success text-sys-success py-2 rounded font-mono text-xs mt-2 hover:bg-sys-success/30 transition-all" onclick="addMacros()">Inyectar Nutrientes</button>
            </div>
          </div>
          <div class="mt-auto border-t border-sys-border pt-4">
            <h3 class="text-xs font-bold font-mono text-white mb-3">Hidratación (Litros)</h3>
            <div class="flex items-center justify-between bg-sys-base p-2 rounded border border-sys-border">
              <button type="button" class="text-sys-textMuted hover:text-white px-3 py-1" onclick="updateWater(-0.5)"><i class="fa-solid fa-minus text-xs"></i></button>
              <span class="font-mono text-lg text-blue-400 font-bold" id="waterDisplay">0.0</span>
              <button type="button" class="text-sys-textMuted hover:text-white px-3 py-1" onclick="updateWater(0.5)"><i class="fa-solid fa-plus text-xs"></i></button>
            </div>
          </div>
        </div>
        
        <div class="col-span-1 lg:col-span-2 glass-panel p-6 flex flex-col">
          <h2 class="text-base font-bold font-mono text-white border-b border-sys-border pb-2 mb-6">Métricas de Recomposición</h2>
          <div class="mb-6">
            <div class="flex justify-between text-xs font-mono mb-2">
              <span class="text-sys-textMuted">Energía Oxidativa Total (Kcal)</span>
              <span class="text-white"><span id="nutKcalNum">0</span> / 2150 Kcal</span>
            </div>
            <div class="w-full h-2.5 bg-sys-base rounded-full overflow-hidden border border-sys-border">
              <div id="nutKcalBar" class="h-full bg-gradient-to-r from-sys-accent to-blue-500 rounded-full transition-all duration-500" style="width: 0%;"></div>
            </div>
          </div>

          <div class="space-y-6">
            <div>
              <div class="flex justify-between text-xs font-mono mb-1.5">
                <span class="text-sys-accent font-bold">🥩 Proteína <span class="text-white ml-1">140g (560 kcal)</span></span>
                <span class="text-white"><span id="nutProNum">0</span> / 140g</span>
              </div>
              <div class="w-full h-1.5 bg-sys-base rounded-full overflow-hidden mb-2">
                <div id="nutProBar" class="h-full bg-sys-accent transition-all duration-500" style="width: 0%;"></div>
              </div>
              <p class="text-[10px] text-sys-textMuted font-mono">Innegociable. A 2.4g por kilo, es tu bloque constructor. Sin esta cantidad, en déficit, perderías músculo.</p>
            </div>
            
            <div>
              <div class="flex justify-between text-xs font-mono mb-1.5">
                <span class="text-sys-warning font-bold">🥑 Grasas <span class="text-white ml-1">70g (630 kcal)</span></span>
                <span class="text-white"><span id="nutFatNum">0</span> / 70g</span>
              </div>
              <div class="w-full h-1.5 bg-sys-base rounded-full overflow-hidden mb-2">
                <div id="nutFatBar" class="h-full bg-sys-warning transition-all duration-500" style="width: 0%;"></div>
              </div>
              <p class="text-[10px] text-sys-textMuted font-mono">Tu escudo hormonal. Mantenemos un nivel óptimo para que tu testosterona no sufra por el déficit calórico. Sigue incluyendo tus yemas de huevo y aceite de oliva.</p>
            </div>

            <div>
              <div class="flex justify-between text-xs font-mono mb-1.5">
                <span class="text-purple-400 font-bold">🍚 Carbohidratos <span class="text-white ml-1">240g (960 kcal)</span></span>
                <span class="text-white"><span id="nutCarNum">0</span> / 240g</span>
              </div>
              <div class="w-full h-1.5 bg-sys-base rounded-full overflow-hidden mb-2">
                <div id="nutCarBar" class="h-full bg-purple-400 transition-all duration-500" style="width: 0%;"></div>
              </div>
              <p class="text-[10px] text-sys-textMuted font-mono">El ajuste maestro. Recortado estratégicamente. Tienes la energía exacta para rendir en esos días de estrés metabólico y tensión mecánica, pero al no sobrar nada, tu cuerpo tendrá que oxidar grasa adiposa para sobrevivir al resto del día.</p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section id="view-habits" class="view-section p-4 md:p-6 max-w-[800px] mx-auto">
      <div class="glass-panel p-6 flex flex-col">
        <div class="flex justify-between items-center mb-6 border-b border-sys-border pb-4">
          <h2 class="text-xl font-bold font-mono text-white flex items-center gap-3">
            <i class="fa-solid fa-dna text-purple-400"></i> Protocolos Biológicos
          </h2>
          <span class="font-mono text-xs bg-purple-500/20 text-purple-400 px-3 py-1 rounded border border-purple-500/30">Cómputo: <span id="habitsActiveCount">0/6</span></span>
        </div>
        <div class="space-y-3" id="habitsList"></div>
      </div>
    </section>

    <section id="view-analytics" class="view-section p-4 md:p-6 max-w-[1300px] mx-auto">
      
      <div class="glass-panel p-5 mb-6 flex flex-col">
        <h2 class="text-xs uppercase tracking-widest text-sys-textMuted font-semibold mb-4 flex items-center gap-2">
          <i class="fa-solid fa-chart-line text-sys-accent"></i> Gráfico de Diagnóstico de Consistencia
        </h2>
        <div class="w-full bg-sys-base/60 border border-sys-border rounded-xl p-4 flex flex-col justify-between h-48 relative">
          <svg id="monthlyTrendGraph" class="w-full h-full" preserveAspectRatio="none" viewBox="0 0 700 120"></svg>
          <div class="absolute inset-0 flex items-center justify-center pointer-events-none id-empty-graph-msg hidden">
            <span class="font-mono text-[10px] text-sys-textMuted">Sin registros históricos detectados en este mes.</span>
          </div>
        </div>
        <div class="flex justify-between text-[9px] font-mono text-sys-textMuted mt-2 px-1">
          <span>INICIO DE MES</span>
          <span>TENDENCIA DE CONSISTENCIA (%)</span>
          <span>FIN DE MES</span>
        </div>
      </div>

      <div class="glass-panel p-6 flex flex-col">
        <div class="flex justify-between items-center mb-6 border-b border-sys-border pb-4">
          <h2 class="text-xl font-bold font-mono text-white flex items-center gap-3">
            <i class="fa-solid fa-calendar-days text-sys-warning"></i> Matriz Temporal Mensual
          </h2>
          <span class="text-xs font-mono text-sys-warning bg-sys-warning/10 border border-sys-warning/20 px-3 py-1 rounded" id="calendarMonthLabel">MES 2026</span>
        </div>
        
        <p class="text-xs text-sys-textMuted font-mono mb-4"><i class="fa-solid fa-mouse-pointer mr-1 text-sys-accent"></i> Haz clic en cualquier celda para viajar en el tiempo. Podrás ver, registrar y guardar los datos únicos de ese día; manteniendo el color histórico y sin que nada se quite o reemplace accidentalmente.</p>
        
        <div class="grid grid-cols-7 gap-2 md:gap-3 mb-2 text-center font-mono text-[11px] text-sys-textMuted" id="calendarHeader">
          <div>DOM</div><div>LUN</div><div>MAR</div><div>MIE</div><div>JUE</div><div>VIE</div><div>SAB</div>
        </div>
        <div class="grid grid-cols-7 gap-2 md:gap-3 flex-1" id="calendarGrid"></div>
        
        <div class="mt-6 flex flex-wrap justify-center gap-5 border-t border-sys-border pt-4">
          <div class="flex items-center gap-1.5"><div class="w-2.5 h-2.5 bg-sys-panel border border-sys-border rounded"></div><span class="text-[10px] font-mono text-sys-textMuted">Sin Datos</span></div>
          <div class="flex items-center gap-1.5"><div class="w-2.5 h-2.5 bg-red-950 border border-red-500 rounded"></div><span class="text-[10px] font-mono text-sys-textMuted">Fallo Módulos</span></div>
          <div class="flex items-center gap-1.5"><div class="w-2.5 h-2.5 bg-yellow-950 border border-sys-warning rounded"></div><span class="text-[10px] font-mono text-sys-textMuted">Rendimiento Parcial</span></div>
          <div class="flex items-center gap-1.5"><div class="w-2.5 h-2.5 bg-green-950 border border-sys-success rounded"></div><span class="text-[10px] font-mono text-sys-textMuted">Optimización Total</span></div>
        </div>
      </div>
    </section>

  </main>

<script>
  const STORAGE_KEY = 'superman_jarvis_v3.1_time_matrix';
  
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

  const ROUTINES = {
    1: { 
      type: 'Push Fuerza', 
      title: '🟢 Lunes — Empuje Fuerza', 
      desc: 'Tensión Mecánica Alta. Cargas pesadas axiales. Aquí construimos la densidad.',
      exercises: [
        {name: 'Press Inclinado con Mancuernas a 30°', sets: 3, reps: '6–8', desc: 'RIR 1–2 | Descanso 180s'},
        {name: 'Press Plano con Mancuernas', sets: 2, reps: '6–8', desc: 'RIR 1–2 | Descanso 180s (Agrupado aquí para aprovechar fuerza estabilizadora intacta)'},
        {name: 'Elevaciones Laterales en Polea Baja', sets: 3, reps: '8–10', desc: 'RIR 1 | Descanso 90s'},
        {name: 'Extensiones de Tríceps sobre la Cabeza', sets: 3, reps: '6–8', desc: 'RIR 1 | Descanso 120s'},
        {name: 'Flexión de Cuello con Disco', sets: 3, reps: '15–20', desc: 'RIR 2 | Descanso 90s'}
      ]
    },
    2: { 
      type: 'Pull Fuerza', 
      title: '🟢 Martes — Tracción Fuerza', 
      desc: 'Estabilidad Externa y Densidad. Día pesado de espalda. Añadimos trabajo de deltoides posterior para completar las 3 cabezas del hombro.',
      exercises: [
        {name: 'Dominadas (Lastradas si es posible)', sets: 3, reps: '6–8', desc: 'RIR 1 | Descanso 150s'},
        {name: 'Jalón al Pecho Agarre Neutro/Estrecho', sets: 3, reps: '6–8', desc: 'RIR 1 | Descanso 150s'},
        {name: 'Remo con Barra o en Máquina', sets: 2, reps: '6–8', desc: 'RIR 1 | Descanso 150s'},
        {name: 'Face Pull o Pájaros con Mancuernas', sets: 3, reps: '10–12', desc: 'RIR 1 | Descanso 90s (Clave para la cabeza posterior)'},
        {name: 'Curl de Bíceps en Banco Inclinado 45°', sets: 3, reps: '6–8', desc: 'RIR 1 | Descanso 120s'},
        {name: 'Paseo del Granjero (Farmer\'s Walk)', sets: 3, reps: '40 metros', desc: 'RIR Fallo (agarre) | Descanso 120s'}
      ]
    },
    3: { 
      type: 'Pierna Estructural', 
      title: '🟢 Miércoles — Pierna Estructural', 
      desc: 'Estímulo Axial. Máxima testosterona. Añadimos extensiones para asegurar un estímulo completo del cuádriceps.',
      exercises: [
        {name: 'Sentadilla Hack o Prensa de Piernas', sets: 4, reps: '6–8', desc: 'RIR 1 | Descanso 180–240s'},
        {name: 'Peso Muerto Rumano (RDL) con Barra', sets: 3, reps: '6–8', desc: 'RIR 1–2 | Descanso 180s'},
        {name: 'Extensiones de Cuádriceps en Máquina', sets: 3, reps: '10–12', desc: 'RIR 1 | Descanso 120s (Detalle para redondear)'},
        {name: 'Elevación de Gemelos de Pie en Máquina', sets: 4, reps: '10–12', desc: 'RIR 0 | Descanso 120s'}
      ]
    },
    4: { 
      type: 'Push Metabólico', 
      title: '🔴 Jueves — Empuje Metabólico', 
      desc: 'Oclusión y Cell Swelling. Intacto. Tu prioridad manda: pre-fatiga de hombro brutal antes de los presses.',
      exercises: [
        {name: 'Elevaciones Laterales en Polea Baja', sets: 4, reps: '12–15', desc: 'RIR 0 (Fallo) | Descanso 60s'},
        {name: 'Press Inclinado con Mancuernas a 30°', sets: 4, reps: '12–15', desc: 'RIR 0 (Fallo) | Descanso 90s'},
        {name: 'Extensiones de Tríceps sobre la Cabeza', sets: 3, reps: '12–15', desc: 'RIR 0 (Fallo) | Descanso 90s'},
        {name: 'Flexión de Cuello con Disco', sets: 3, reps: '15–20', desc: 'RIR 2 | Descanso 90s'},
        {name: 'Press Plano con Mancuernas', sets: 2, reps: '12–15', desc: 'RIR 0 (Fallo) | Descanso 90s'}
      ]
    },
    5: { 
      type: 'Pull Metabólico', 
      title: '🔴 Viernes — Tracción Metabólica', 
      desc: 'Bombeo Localizado. Volumen altísimo. Ajusté los RIR iniciales para que no frías tu Sistema Nervioso Central antes de acabar.',
      exercises: [
        {name: 'Remo en Máquina', sets: 3, reps: '12–15', desc: 'RIR 1 | Descanso 90s'},
        {name: 'Dominadas', sets: 3, reps: 'Al fallo técnico', desc: 'RIR 1 | Descanso 90s'},
        {name: 'Jalón al Pecho Agarre Neutro/Estrecho', sets: 2, reps: '12–15', desc: 'RIR 1 | Descanso 90s'},
        {name: 'Vuelos Posteriores en Máquina (Pec Deck Invertido)', sets: 3, reps: '15–20', desc: 'RIR 0 (Fallo) | Descanso 60s (Toque 3D)'},
        {name: 'Remo con Mancuerna apoyado en Banco', sets: 2, reps: '12–15', desc: 'RIR 1 | Descanso 90s'},
        {name: 'Curl Martillo en banco 45°', sets: 3, reps: '12–15', desc: 'RIR 0 (Fallo) | Descanso 90s'}
      ]
    },
    6: { 
      type: 'Pierna Metabólica', 
      title: '🔴 Sábado — Pierna Metabólica', 
      desc: 'Aislamiento Seguro. Cero carga axial lumbar, pero añadimos zancadas para destruir por completo las piernas de forma segura.',
      exercises: [
        {name: 'Sentadilla Hack o Prensa de Piernas', sets: 4, reps: '12–15', desc: 'RIR 1 | Descanso 120s'},
        {name: 'Zancadas (Lunges) con Mancuernas', sets: 3, reps: '12-15 por pierna', desc: 'RIR 1 | Descanso 120s (Hipertrofia glúteo y cuádriceps)'},
        {name: 'Curl de Isquios Sentado en Máquina', sets: 4, reps: '12–15', desc: 'RIR 0 (Fallo) | Descanso 90s'},
        {name: 'Elevación de Gemelos de Pie en Máquina', sets: 4, reps: '15–20', desc: 'RIR 0 (Fallo) | Descanso 90s'}
      ]
    },
    0: { 
      type: 'Recup. Activa', 
      title: '🔵 Domingo — Recuperación Activa', 
      desc: 'Aclaramiento de lactato. Obligatorio para sobrevivir a esta semana.',
      exercises: [
        {name: 'Caminata rápida en Zona 2 + Movilidad', sets: 1, reps: '40 min', desc: 'RPE < 4 | Descanso —'}
      ]
    }
  };

  const GOALS = { pro: 140, car: 240, fat: 70, kcal: 2150 };

  let appState = {};
  let todayRealStr = "";
  let auditedDateStr = ""; 
  let currentMonthIndex = new Date().getMonth();
  let currentYearIndex = new Date().getFullYear();

  function formatDate(d) {
    const y = d.getFullYear();
    const m = String(d.getMonth() + 1).padStart(2, '0');
    const day = String(d.getDate()).padStart(2, '0');
    return `${y}-${m}-${day}`;
  }

  function initTimeline() {
    const now = new Date();
    todayRealStr = formatDate(now);
    auditedDateStr = todayRealStr; 
  }

  function getTargetData(dateString) {
    if (!appState[dateString]) {
      appState[dateString] = JSON.parse(JSON.stringify(baseDayData));
    }
    return appState[dateString];
  }

  function saveData() {
    try { 
      localStorage.setItem(STORAGE_KEY, JSON.stringify(appState)); 
    } catch(e) { 
      console.error("Storage bloqueado o error al guardar.", e); 
    }
    updateDashboard(); 
    renderAnalyticsView(); 
  }

  function loadData() {
    try {
      const saved = localStorage.getItem(STORAGE_KEY);
      if (saved) {
        appState = JSON.parse(saved);
      }
    } catch(e) { 
      console.error("Error al cargar localStorage.", e);
      appState = {}; 
    }
    getTargetData(auditedDateStr); 
  }

  window.resetToToday = function() {
    auditedDateStr = todayRealStr;
    const now = new Date();
    currentMonthIndex = now.getMonth();
    currentYearIndex = now.getFullYear();
    document.getElementById('btnTodayReset').classList.add('hidden');
    document.getElementById('timelineIndicator').innerText = "HOY";
    renderAllViews();
  }

  window.selectCalendarDay = function(targetStr) {
    auditedDateStr = targetStr;
    if(auditedDateStr === todayRealStr) {
      document.getElementById('timelineIndicator').innerText = "HOY";
      document.getElementById('btnTodayReset').classList.add('hidden');
    } else {
      const parsed = new Date(targetStr + "T00:00:00");
      document.getElementById('timelineIndicator').innerText = parsed.toLocaleDateString('es-ES', {day:'numeric', month:'short'}).toUpperCase();
      document.getElementById('btnTodayReset').classList.remove('hidden');
    }
    navigateTo('view-dashboard');
    renderAllViews();
  }

  window.resetData = function() {
    if(confirm(`¿Estás seguro que deseas RESTABLECER y vaciar las métricas del día seleccionado (${auditedDateStr})?`)) {
      appState[auditedDateStr] = JSON.parse(JSON.stringify(baseDayData));
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
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }

  btnBack.addEventListener('click', () => navigateTo('view-dashboard'));

  function calculateDayScores(dataStr) {
    const dayData = appState[dataStr];
    if (!dayData) return { total: 0, trainScore: 0, dietScore: 0, habitsScore: 0 };
    
    let trainScore = dayData.training.completed ? 100 : 0;
    const kcal = (dayData.nutrition.pro * 4) + (dayData.nutrition.car * 4) + (dayData.nutrition.fat * 9);
    
    let dietScore = 0;
    if(kcal > 0) {
      dietScore = Math.min((kcal / GOALS.kcal) * 100, 100);
      if(kcal > (GOALS.kcal + 200)) dietScore = Math.max(100 - ((kcal - GOALS.kcal)/10), 40); 
    }
    
    let habitsCompleted = 0;
    Object.values(dayData.habits).forEach(v => { if(v) habitsCompleted++; });
    let habitsScore = (habitsCompleted / HITS_CONFIG.length) * 100;
    
    return {
      total: Math.round((trainScore + dietScore + habitsScore) / 3),
      trainScore, dietScore, habitsScore, kcal
    };
  }

  function updateDashboard() {
    const data = getTargetData(auditedDateStr);
    const dateObj = new Date(auditedDateStr + "T00:00:00");
    
    const routine = ROUTINES[dateObj.getDay()];
    document.getElementById('dashRoutineSummary').innerText = routine.type + " / " + routine.exercises.length + " Ejercicios";
    
    const scores = calculateDayScores(auditedDateStr);

    const circumference = 251.2;
    setTimeout(() => {
      document.getElementById('donutTraining').style.strokeDashoffset = circumference - (scores.trainScore / 100) * circumference;
      document.getElementById('donutDiet').style.strokeDashoffset = circumference - (scores.dietScore / 100) * circumference;
      document.getElementById('donutHabits').style.strokeDashoffset = circumference - (scores.habitsScore / 100) * circumference;
    }, 80);

    document.getElementById('dashPercent').innerText = scores.total + '%';
    document.getElementById('legTraining').innerText = Math.round(scores.trainScore) + '%';
    document.getElementById('legDiet').innerText = Math.round(scores.dietScore) + '%';
    document.getElementById('legHabits').innerText = Math.round(scores.habitsScore) + '%';
    
    let mods = 0;
    if(scores.trainScore === 100) mods++;
    if(scores.dietScore >= 85) mods++;
    if(scores.habitsScore >= 80) mods++;
    document.getElementById('dashTotalModules').innerText = `${mods}/3`;

    const cnsEl = document.getElementById('dashCNS');
    if(data.habits.sleep) {
      cnsEl.innerText = "Nominal"; cnsEl.className = "font-mono text-xs text-sys-success font-bold";
    } else {
      cnsEl.innerText = "Deteriorado"; cnsEl.className = "font-mono text-xs text-sys-warning font-bold";
    }

    const pPerc = Math.min((data.nutrition.pro / GOALS.pro) * 100, 100);
    const cPerc = Math.min((data.nutrition.car / GOALS.car) * 100, 100);
    const fPerc = Math.min((data.nutrition.fat / GOALS.fat) * 100, 100);
    
    setTimeout(() => {
      document.getElementById('miniPro').style.strokeDashoffset = 94.2 - (pPerc/100)*94.2;
      document.getElementById('miniCar').style.strokeDashoffset = 94.2 - (cPerc/100)*94.2;
      document.getElementById('miniFat').style.strokeDashoffset = 94.2 - (fPerc/100)*94.2;
    }, 80);
    document.getElementById('lblPro').innerText = Math.round(pPerc)+'%';
    document.getElementById('lblCar').innerText = Math.round(cPerc)+'%';
    document.getElementById('lblFat').innerText = Math.round(fPerc)+'%';

    let habitsCount = 0; Object.values(data.habits).forEach(v => { if(v) habitsCount++; });

    const sched = document.getElementById('dashSchedule');
    sched.innerHTML = `
      <div class="flex items-center gap-3 bg-sys-panel/50 border border-sys-border rounded-lg p-2.5">
        <div class="w-7 h-7 rounded-full ${data.training.completed ? 'bg-sys-success/20 text-sys-success border border-sys-success/30' : 'bg-blue-500/20 text-blue-400 border border-blue-500/30'} flex items-center justify-center shrink-0">
          <i class="fa-solid fa-dumbbell text-xs"></i>
        </div>
        <div class="text-xs flex-1">
          <p class="font-bold text-white">${routine.title}</p>
          <p class="text-sys-textMuted font-mono text-[10px] mt-0.5">${data.training.completed ? 'Completado' : 'Pendiente o Sin Marcar'}</p>
        </div>
      </div>
      <div class="flex items-center gap-3 bg-sys-panel/50 border border-sys-border rounded-lg p-2.5">
        <div class="w-7 h-7 rounded-full ${data.nutrition.water >= 3 ? 'bg-sys-success/20 text-sys-success border border-sys-success/30' : 'bg-blue-500/20 text-blue-400 border border-blue-500/30'} flex items-center justify-center shrink-0">
          <i class="fa-solid fa-droplet text-xs"></i>
        </div>
        <div class="text-xs flex-1">
          <p class="font-bold text-white">Hidratación</p>
          <p class="text-sys-textMuted font-mono text-[10px] mt-0.5">${data.nutrition.water.toFixed(1)} / 3.0 Litros</p>
        </div>
      </div>
      <div class="flex items-center gap-3 bg-sys-panel/50 border border-sys-border rounded-lg p-2.5">
        <div class="w-7 h-7 rounded-full ${habitsCount >= 5 ? 'bg-sys-success/20 text-sys-success border border-sys-success/30' : 'bg-purple-500/20 text-purple-400 border border-purple-500/30'} flex items-center justify-center shrink-0">
          <i class="fa-solid fa-bolt text-xs"></i>
        </div>
        <div class="text-xs flex-1">
          <p class="font-bold text-white">Hábitos</p>
          <p class="text-sys-textMuted font-mono text-[10px] mt-0.5">${habitsCount} de ${HITS_CONFIG.length} asimilados</p>
        </div>
      </div>
    `;
    
    // Forzar redibujado de gráficos al actualizar el dash
    const daysInMonth = new Date(currentYearIndex, currentMonthIndex + 1, 0).getDate();
    generateMonthlyTrendGraph(daysInMonth);
  }

  function renderTrainingView() {
    const data = getTargetData(auditedDateStr);
    const dateObj = new Date(auditedDateStr + "T00:00:00");
    const routine = ROUTINES[dateObj.getDay()];
    
    document.getElementById('trainMainHeading').innerHTML = `<i class="fa-solid fa-dumbbell text-blue-400"></i> ${routine.title}`;
    document.getElementById('trainSubDesc').innerText = routine.desc;
    
    const statusEl = document.getElementById('trainStatus');
    if(data.training.completed) {
      statusEl.innerText = "EJECUTADO";
      statusEl.parentElement.className = "font-mono text-xs bg-sys-success/20 text-sys-success px-3 py-1 rounded border border-sys-success/30";
    } else {
      statusEl.innerText = "PENDIENTE";
      statusEl.parentElement.className = "font-mono text-xs bg-blue-500/20 text-blue-400 px-3 py-1 rounded border border-blue-500/30";
    }

    const listEl = document.getElementById('trainExerciseList');
    listEl.innerHTML = '';
    
    routine.exercises.forEach((ex, idx) => {
      let setsHtml = '';
      for(let s=1; s<=ex.sets; s++) {
        const logVal = data.training.log[`${idx}_s${s}`] || '';
        setsHtml += `
          <div class="flex items-center gap-3 mb-2">
            <span class="text-[11px] font-mono text-sys-textMuted w-16">Serie ${s}</span>
            <input type="number" id="ex_${idx}_s${s}_w" class="sci-input py-1 text-xs w-24" placeholder="Carga (Kg)" value="${logVal ? logVal.split('-')[0] : ''}">
            <input type="number" id="ex_${idx}_s${s}_r" class="sci-input py-1 text-xs w-24" placeholder="Reps" value="${logVal ? logVal.split('-')[1] : ''}">
          </div>
        `;
      }
      listEl.innerHTML += `
        <div class="bg-sys-base border border-sys-border rounded-xl p-4">
          <div class="flex flex-col sm:flex-row sm:items-center justify-between border-b border-sys-border/50 pb-2 mb-3 gap-1">
            <div>
              <h3 class="font-bold text-sm text-white">${ex.name}</h3>
              <p class="text-[10px] font-mono text-sys-accent mt-0.5">${ex.desc}</p>
            </div>
            <span class="font-mono text-[10px] text-sys-textMuted bg-sys-panel px-2 py-0.5 rounded border border-sys-border self-start sm:self-center">${ex.sets} Series x ${ex.reps}</span>
          </div>
          <div class="pl-2 border-l border-sys-border/40">
            ${setsHtml}
          </div>
        </div>
      `;
    });
  }

  window.saveTraining = function() {
    const dateObj = new Date(auditedDateStr + "T00:00:00");
    const routine = ROUTINES[dateObj.getDay()];
    const data = getTargetData(auditedDateStr);
    
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
    const data = getTargetData(auditedDateStr);
    data.training.completed = true;
    saveData();
    renderTrainingView();
  }

  function renderNutritionView() {
    const data = getTargetData(auditedDateStr);
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
      document.getElementById('nutProBar').style.width = Math.min((data.nutrition.pro / GOALS.pro)*100, 100) + '%';
      document.getElementById('nutCarBar').style.width = Math.min((data.nutrition.car / GOALS.car)*100, 100) + '%';
      document.getElementById('nutFatBar').style.width = Math.min((data.nutrition.fat / GOALS.fat)*100, 100) + '%';
      document.getElementById('nutKcalBar').style.width = Math.min((kcal / GOALS.kcal)*100, 100) + '%';
    }, 50);
  }

  window.addMacros = function() {
    const data = getTargetData(auditedDateStr);
    data.nutrition.pro += parseInt(document.getElementById('inpPro').value) || 0;
    data.nutrition.car += parseInt(document.getElementById('inpCar').value) || 0;
    data.nutrition.fat += parseInt(document.getElementById('inpFat').value) || 0;
    
    document.getElementById('inpPro').value = '';
    document.getElementById('inpCar').value = '';
    document.getElementById('inpFat').value = '';
    
    saveData();
    renderNutritionView();
  }

  window.updateWater = function(amt) {
    const data = getTargetData(auditedDateStr);
    data.nutrition.water = Math.max(0, data.nutrition.water + amt);
    saveData();
    renderNutritionView();
  }

  function renderHabitsView() {
    const data = getTargetData(auditedDateStr);
    const listEl = document.getElementById('habitsList');
    listEl.innerHTML = '';
    
    let active = 0;
    HITS_CONFIG.forEach(h => {
      const isChecked = data.habits[h.id];
      if(isChecked) active++;
      listEl.innerHTML += `
        <div class="bg-sys-base border ${isChecked ? 'border-sys-success/40' : 'border-sys-border'} rounded-xl p-4 flex items-center justify-between">
          <div class="flex items-center gap-4">
            <div class="w-9 h-9 rounded-full bg-sys-panel flex items-center justify-center border border-sys-border">
              <i class="fa-solid ${h.icon} ${isChecked ? 'text-sys-success' : 'text-sys-textMuted'} text-base"></i>
            </div>
            <div>
              <h3 class="font-bold text-xs text-white">${h.name}</h3>
              <p class="text-[9px] font-mono text-sys-textMuted uppercase">${h.desc}</p>
            </div>
          </div>
          <input type="checkbox" class="sci-checkbox" ${isChecked ? 'checked' : ''} onchange="toggleHabit('${h.id}')">
        </div>
      `;
    });
    document.getElementById('habitsActiveCount').innerText = `${active}/${HITS_CONFIG.length}`;
  }

  window.toggleHabit = function(id) {
    const data = getTargetData(auditedDateStr);
    data.habits[id] = !data.habits[id];
    saveData();
    renderHabitsView();
  }

  function generateMonthlyTrendGraph(daysInMonth) {
    const svgDash = document.getElementById('monthlyTrendGraphDash');
    const svgAnalytic = document.getElementById('monthlyTrendGraph');
    const emptyMsgs = document.querySelectorAll('.id-empty-graph-msg');
    
    let htmlContent = '';
    let points = [];
    let activeRecords = 0;
    let totalScoreSum = 0;
    
    const widthTotal = 700;
    const heightTotal = 120;
    const padding = 15;
    const chartWidth = widthTotal - (padding * 2);
    const chartHeight = heightTotal - (padding * 2);
    
    const stepX = chartWidth / (daysInMonth - 1 || 1);
    
    for(let lines = 0; lines <= 4; lines++) {
      const yL = padding + (chartHeight * (lines / 4));
      htmlContent += `<line x1="${padding}" y1="${yL}" x2="${widthTotal - padding}" y2="${yL}" stroke="rgba(26, 39, 68, 0.3)" stroke-width="1" stroke-dasharray="4 4" />`;
    }

    for (let i = 1; i <= daysInMonth; i++) {
      const loopStr = `${currentYearIndex}-${String(currentMonthIndex + 1).padStart(2, '0')}-${String(i).padStart(2, '0')}`;
      const dayData = appState[loopStr];
      
      let score = 0;
      if (dayData) {
        score = calculateDayScores(loopStr).total;
        if(score > 0) {
          activeRecords++;
          totalScoreSum += score;
        }
      }
      
      const cx = padding + ((i - 1) * stepX);
      const cy = padding + chartHeight - ((score / 100) * chartHeight);
      points.push({cx, cy, score, dayNum: i});
    }

    let avg = activeRecords > 0 ? Math.round(totalScoreSum / activeRecords) : 0;
    const avgEl = document.getElementById('dashMonthlyAvg');
    if(avgEl) {
      avgEl.innerText = avg + '%';
      if(avg >= 85) avgEl.className = "text-sys-success font-bold font-mono";
      else if(avg > 40) avgEl.className = "text-sys-warning font-bold font-mono";
      else avgEl.className = "text-sys-danger font-bold font-mono";
    }

    if(activeRecords === 0) {
      emptyMsgs.forEach(msg => msg.classList.remove('hidden'));
      if (svgDash) svgDash.innerHTML = '';
      if (svgAnalytic) svgAnalytic.innerHTML = '';
      return;
    } else {
      emptyMsgs.forEach(msg => msg.classList.add('hidden'));
    }

    points.forEach(p => {
      let colorBar = 'rgba(26, 39, 68, 0.4)';
      if(p.score >= 85) colorBar = 'rgba(0, 255, 136, 0.15)';
      else if(p.score > 10) colorBar = 'rgba(255, 184, 0, 0.15)';
      
      htmlContent += `
        <rect x="${p.cx - 3}" y="${padding + chartHeight - ((p.score/100)*chartHeight)}" width="6" height="${(p.score/100)*chartHeight}" fill="${colorBar}" rx="2"/>
      `;
    });

    let polyPath = "";
    points.forEach((p, idx) => {
      polyPath += `${idx === 0 ? 'M' : 'L'} ${p.cx} ${p.cy} `;
    });
    
    htmlContent += `
      <path d="${polyPath}" fill="none" stroke="#00f0ff" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" style="filter: drop-shadow(0 0 4px rgba(0,240,255,0.4));"/>
    `;

    points.forEach(p => {
      if(p.score > 0) {
        let nodeColor = '#ffb800';
        if(p.score >= 85) nodeColor = '#00ff88';
        htmlContent += `
          <circle cx="${p.cx}" cy="${p.cy}" r="3.5" fill="#050a14" stroke="${nodeColor}" stroke-width="2" />
        `;
      }
    });

    if (svgDash) svgDash.innerHTML = htmlContent;
    if (svgAnalytic) svgAnalytic.innerHTML = htmlContent;
  }

  function renderAnalyticsView() {
    const grid = document.getElementById('calendarGrid');
    grid.innerHTML = '';
    const monthNames = ["ENERO", "FEBRERO", "MARZO", "ABRIL", "MAYO", "JUNIO", "JULIO", "AGOSTO", "SEPTIEMBRE", "OCTUBRE", "NOVIEMBRE", "DICIEMBRE"];
    document.getElementById('calendarMonthLabel').innerText = `${monthNames[currentMonthIndex]} ${currentYearIndex}`;

    const firstDayIndex = new Date(currentYearIndex, currentMonthIndex, 1).getDay(); 
    const daysInMonth = new Date(currentYearIndex, currentMonthIndex + 1, 0).getDate();

    for(let i=0; i<firstDayIndex; i++) {
      grid.innerHTML += `<div class="aspect-square bg-transparent opacity-0 pointer-events-none"></div>`;
    }

    for(let i=1; i<=daysInMonth; i++) {
      const loopDateStr = `${currentYearIndex}-${String(currentMonthIndex+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
      const dayData = appState[loopDateStr];
      
      let colorMetricsClass = "bg-sys-panel/40 border-sys-border text-sys-textMuted hover:border-sys-borderLight";
      
      if(dayData) {
        const scores = calculateDayScores(loopDateStr);
        if (scores.total >= 85) {
          colorMetricsClass = "bg-green-950/70 border-sys-success text-sys-success font-bold shadow-[0_0_12px_rgba(0,255,136,0.15)]";
        } else if (scores.total > 20) {
          colorMetricsClass = "bg-yellow-950/70 border-sys-warning text-sys-warning font-bold";
        } else if (scores.total > 0) {
          colorMetricsClass = "bg-red-950/70 border-sys-danger text-sys-danger font-bold";
        }
      }
      
      const isSelectedFrame = loopDateStr === auditedDateStr ? "ring-2 ring-sys-accent ring-offset-2 ring-offset-sys-base z-10 scale-105" : "";
      const isTodayMarker = loopDateStr === todayRealStr ? "border-dashed border-2" : "";
      
      grid.innerHTML += `
        <div onclick="selectCalendarDay('${loopDateStr}')" class="aspect-square rounded-xl border flex flex-col items-center justify-center relative ${colorMetricsClass} ${isSelectedFrame} ${isTodayMarker} transition-all duration-200 cursor-pointer select-none" title="Ver/Editar fecha: ${loopDateStr}">
          <span class="font-mono text-xs md:text-sm">${i}</span>
          ${dayData && dayData.training.completed ? '<div class="absolute bottom-1.5 w-1 h-1 rounded-full bg-current"></div>' : ''}
        </div>
      `;
    }

    generateMonthlyTrendGraph(daysInMonth);
  }

  function renderAllViews() {
    renderTrainingView();
    renderNutritionView();
    renderHabitsView();
    renderAnalyticsView();
    updateDashboard();
  }

  // Cambio crucial para asegurar que la app y el guardado local se inicializan sin fallos.
  document.addEventListener('DOMContentLoaded', () => {
    initTimeline();
    loadData();
    renderAllViews();
    
    setInterval(() => {
      const clockEl = document.getElementById('sysClock');
      if (clockEl) clockEl.innerText = new Date().toLocaleTimeString('en-US', {hour12: false});
    }, 1000);
  });
</script>
</body>
</html>
