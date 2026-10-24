<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>무선 전력 전송(전자기 유도) 가상 실험실</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=Noto+Sans+KR:wght@400;500;700;800&display=swap" rel="stylesheet">
  <style>
    * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
    html, body {
      font-family: 'Noto Sans KR', 'Inter', sans-serif;
      background-color: #090d16;
      color: #f8fafc;
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100dvh;
      overflow: hidden;
      user-select: none;
    }
    .glass-panel {
      background: rgba(15, 23, 42, 0.9);
      border: 1px solid rgba(148, 163, 184, 0.16);
    }
    input[type="range"] {
      accent-color: #38bdf8;
      height: 6px;
    }
    .mode-active-normal {
      background: linear-gradient(135deg, #059669 0%, #047857 100%);
      border-color: #34d399;
      color: #ffffff;
      box-shadow: 0 0 12px rgba(16, 185, 129, 0.35);
    }
    .mode-active-fault {
      background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
      border-color: #fbbf24;
      color: #ffffff;
      box-shadow: 0 0 12px rgba(245, 158, 11, 0.35);
    }
    @media (max-height: 700px) and (max-width: 767px) {
      .compact-hide-sm { display: none; }
    }
  </style>
</head>
<body class="flex flex-col h-dvh w-full overflow-hidden">

  <!-- Compact Top Header Bar -->
  <header class="shrink-0 bg-slate-950/95 border-b border-slate-800/90 px-3 py-1.5 sm:px-4 sm:py-2 flex items-center justify-between gap-2">
    <div class="flex items-center gap-2 min-w-0">
      <div class="w-7 h-7 sm:w-8 sm:h-8 rounded-lg bg-gradient-to-br from-sky-400 to-blue-600 flex items-center justify-center text-slate-950 font-extrabold text-sm sm:text-base shadow shrink-0">
        ⚡
      </div>
      <div class="truncate">
        <h1 class="text-xs sm:text-sm md:text-base font-extrabold tracking-tight text-white truncate">
          무선 전력 전송(전자기 유도) 가상 실험실
        </h1>
      </div>
    </div>

    <div class="flex items-center gap-1.5 shrink-0">
      <button id="btn-power-toggle" type="button" class="px-2.5 py-1 rounded-lg text-[11px] sm:text-xs font-extrabold bg-emerald-600 hover:bg-emerald-500 text-white border border-emerald-400 transition flex items-center gap-1 shadow">
        <span id="power-dot" class="w-2 h-2 rounded-full bg-white animate-pulse"></span>
        <span id="power-label">AA 1.5V 전원 ON</span>
      </button>
      <button id="btn-sim-optimal" type="button" class="px-2.5 py-1 rounded-lg text-[11px] sm:text-xs font-bold bg-sky-600/30 hover:bg-sky-600/50 text-sky-200 border border-sky-500/40 transition">
        최적 위치
      </button>
      <button id="btn-sound-toggle" type="button" class="px-2 py-1 rounded-lg text-[11px] sm:text-xs font-semibold bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700">
        <span id="sound-icon">🔇</span>
      </button>
      <button id="btn-info-modal" type="button" class="px-2.5 py-1 rounded-lg text-[11px] sm:text-xs font-bold bg-amber-500/20 hover:bg-amber-500/30 text-amber-300 border border-amber-500/40">
        원리·회로도
      </button>
    </div>
  </header>

  <!-- Single-Screen Main Experiment Workspace (Fits Phone Portrait & Pad/Desktop Split) -->
  <main class="flex-1 min-h-0 w-full max-w-7xl mx-auto p-2 sm:p-3 grid grid-cols-1 md:grid-cols-12 gap-2 sm:gap-3 overflow-hidden">

    <!-- LEFT / TOP PANEL: Interactive Draggable Coil Canvas + Live Telemetry (7 cols on Pad/Desktop) -->
    <div class="md:col-span-7 lg:col-span-7 flex flex-col min-h-0 gap-2">

      <!-- 4 Live Telemetry KPI Pills -->
      <div class="shrink-0 grid grid-cols-4 gap-1.5 sm:gap-2">
        <div class="glass-panel rounded-xl px-2.5 py-1.5 sm:py-2 border-l-4 border-l-rose-500">
          <div class="text-[10px] sm:text-[11px] text-slate-400 font-medium truncate">LED 밝기</div>
          <div id="metric-led-brightness" class="text-xs sm:text-sm md:text-base font-extrabold text-rose-400 truncate">92% (점등)</div>
        </div>
        <div class="glass-panel rounded-xl px-2.5 py-1.5 sm:py-2 border-l-4 border-l-sky-500">
          <div class="text-[10px] sm:text-[11px] text-slate-400 font-medium truncate">코일 간극 / 각도</div>
          <div id="metric-distance" class="text-xs sm:text-sm md:text-base font-extrabold text-sky-300 truncate">1.5cm / 0°</div>
        </div>
        <div class="glass-panel rounded-xl px-2.5 py-1.5 sm:py-2 border-l-4 border-l-amber-500">
          <div class="text-[10px] sm:text-[11px] text-slate-400 font-medium truncate">유도 기전력 ($\mathcal{E}$)</div>
          <div id="metric-voltage" class="text-xs sm:text-sm md:text-base font-extrabold text-amber-300 truncate">2.35 V</div>
        </div>
        <div class="glass-panel rounded-xl px-2.5 py-1.5 sm:py-2 border-l-4 border-l-emerald-500">
          <div class="text-[10px] sm:text-[11px] text-slate-400 font-medium truncate">자속 결합 효율</div>
          <div id="metric-circuit-status" class="text-xs sm:text-sm md:text-base font-extrabold text-emerald-400 truncate">정상 발진</div>
        </div>
      </div>

      <!-- Main Interactive Touch/Mouse Simulation Canvas -->
      <div class="flex-1 min-h-[180px] relative glass-panel rounded-2xl overflow-hidden touch-none">
        <canvas id="sim-main-canvas" class="w-full h-full block cursor-grab active:cursor-grabbing"></canvas>

        <!-- Floating Drag Instruction Overlay -->
        <div class="absolute top-2 left-2.5 pointer-events-none bg-slate-950/80 backdrop-blur px-2.5 py-1 rounded-lg text-[10px] sm:text-xs text-sky-200 border border-sky-500/30 flex items-center gap-1.5">
          <span>👆</span>
          <span><strong>수신 코일(위쪽)</strong>을 손가락이나 마우스로 직접 드래그해 움직여 보세요!</span>
        </div>

        <!-- Quick On-Canvas Angle/Position Reset Pill -->
        <div class="absolute top-2 right-2.5 flex items-center gap-1">
          <button id="btn-align-center" type="button" class="bg-slate-900/90 hover:bg-slate-800 text-slate-200 text-[10px] sm:text-xs font-bold px-2 py-1 rounded-lg border border-slate-700">
            🎯 중앙 정렬
          </button>
        </div>
      </div>

    </div>

    <!-- RIGHT / BOTTOM PANEL: Oscilloscope + Sliders + Troubleshooting Modes (5 cols on Pad/Desktop) -->
    <div class="md:col-span-5 lg:col-span-5 flex flex-col justify-between min-h-0 gap-2 overflow-y-auto md:overflow-hidden pr-0.5">

      <!-- Real-Time Oscilloscope Graph -->
      <div class="shrink-0 glass-panel rounded-2xl p-2.5 sm:p-3">
        <div class="flex items-center justify-between text-[10px] sm:text-xs mb-1">
          <span class="font-extrabold text-white">📈 실시간 오실로스코프 ($\mathcal{E} = -N_2 \frac{d\Phi_B}{dt}$)</span>
          <div class="flex items-center gap-2 text-[10px]">
            <span class="text-amber-400 font-bold">━ 송신 자속($\Phi_B$)</span>
            <span class="text-rose-400 font-bold">━ 수신 전압($V_{\text{rx}}$)</span>
          </div>
        </div>
        <div class="relative w-full bg-slate-950 rounded-xl border border-slate-800/90 overflow-hidden" style="height: 76px;">
          <canvas id="sim-wave-canvas" class="w-full h-full block"></canvas>
        </div>
      </div>

      <!-- 3 Interactive Physics Variable Sliders -->
      <div class="shrink-0 glass-panel rounded-2xl p-2.5 sm:p-3.5 space-y-2.5 sm:space-y-3">
        <div class="flex items-center justify-between border-b border-slate-800 pb-1.5">
          <h2 class="text-xs sm:text-sm font-extrabold text-white">🎛️ 실험 변인 조절 컨트롤러</h2>
          <span class="text-[10px] text-slate-400">터치 슬라이더</span>
        </div>

        <!-- Slider 1: Air Gap Distance -->
        <div>
          <div class="flex justify-between text-[11px] sm:text-xs font-bold mb-1">
            <span class="text-slate-200">1. 코일 사이 거리 (공기 간극 $d$)</span>
            <span id="lbl-slider-dist" class="text-sky-400 font-extrabold">1.5 cm</span>
          </div>
          <input id="slider-dist" type="range" min="0.5" max="7.0" step="0.1" value="1.5" class="w-full bg-slate-800 rounded-lg cursor-pointer" />
          <div class="flex justify-between text-[9px] sm:text-[10px] text-slate-400 mt-0.5">
            <span>0.5cm (최대 밝기)</span>
            <span>2.0cm (권장)</span>
            <span>7.0cm (소멸)</span>
          </div>
        </div>

        <!-- Slider 2: Coil Tilt Angle -->
        <div>
          <div class="flex justify-between text-[11px] sm:text-xs font-bold mb-1">
            <span class="text-slate-200">2. 수신 코일 기울기 각도 ($\theta$)</span>
            <span id="lbl-slider-angle" class="text-emerald-400 font-extrabold">0° (수평)</span>
          </div>
          <input id="slider-angle" type="range" min="0" max="90" step="5" value="0" class="w-full bg-slate-800 rounded-lg cursor-pointer" />
          <div class="flex justify-between text-[9px] sm:text-[10px] text-slate-400 mt-0.5">
            <span>0° ($\cos 0^\circ = 1$ 최대)</span>
            <span>45° (자속 감소)</span>
            <span>90° ($\cos 90^\circ = 0$ 꺼짐)</span>
          </div>
        </div>

        <!-- Slider 3: Receiver Coil Turns & TX Turns -->
        <div class="grid grid-cols-2 gap-2.5 pt-0.5">
          <div>
            <div class="flex justify-between text-[10px] sm:text-[11px] font-bold mb-1">
              <span class="text-slate-300">3. 수신 코일($N_2$)</span>
              <span id="lbl-slider-turns" class="text-amber-400 font-extrabold">30바퀴</span>
            </div>
            <input id="slider-turns" type="range" min="10" max="50" step="5" value="30" class="w-full bg-slate-800 rounded-lg cursor-pointer" />
          </div>
          <div>
            <div class="flex justify-between text-[10px] sm:text-[11px] font-bold mb-1">
              <span class="text-slate-300">4. 좌우 어긋남($\Delta x$)</span>
              <span id="lbl-slider-offset" class="text-purple-400 font-extrabold">0.0 cm</span>
            </div>
            <input id="slider-offset" type="range" min="-4.0" max="4.0" step="0.2" value="0" class="w-full bg-slate-800 rounded-lg cursor-pointer" />
          </div>
        </div>
      </div>

      <!-- Fault / Troubleshooting Mode Simulator -->
      <div class="shrink-0 glass-panel rounded-2xl p-2.5 sm:p-3.5 space-y-2">
        <div class="flex items-center justify-between">
          <h3 class="text-[11px] sm:text-xs font-extrabold text-amber-300 flex items-center gap-1">
            <span>🔍</span>
            <span>실험 중 불이 안 켜질 때 원인 비교 시뮬레이션</span>
          </h3>
        </div>

        <div class="grid grid-cols-2 gap-1.5">
          <button type="button" data-fault="normal" class="fault-mode-btn mode-active-normal py-1.5 px-2.5 rounded-xl text-[11px] sm:text-xs font-extrabold border text-left transition truncate">
            ✅ 정상 발진 작동
          </button>
          <button type="button" data-fault="phase" class="fault-mode-btn py-1.5 px-2.5 rounded-xl text-[11px] sm:text-xs font-bold bg-slate-800/90 hover:bg-slate-700 text-slate-200 border border-slate-700 text-left transition truncate">
            ⚠️ 코일 ①·③ 위상 반대
          </button>
          <button type="button" data-fault="enamel" class="fault-mode-btn py-1.5 px-2.5 rounded-xl text-[11px] sm:text-xs font-bold bg-slate-800/90 hover:bg-slate-700 text-slate-200 border border-slate-700 text-left transition truncate">
            ⚠️ 에나멜 피막 안 벗김
          </button>
          <button type="button" data-fault="tr_rev" class="fault-mode-btn py-1.5 px-2.5 rounded-xl text-[11px] sm:text-xs font-bold bg-slate-800/90 hover:bg-slate-700 text-slate-200 border border-slate-700 text-left transition truncate">
            🔥 트랜지스터 역방향
          </button>
        </div>

        <div id="fault-diagnosis-box" class="bg-slate-950/90 rounded-xl p-2 sm:p-2.5 border border-slate-800 text-[11px] sm:text-xs text-slate-200 leading-snug">
          <strong>[정상 작동]</strong> 2N2222가 초당 수십만 번 스위칭하여 교류 자기장($\Phi_B$)을 만들고, 수신 코일에 유도 기전력이 발생해 LED가 켜집니다.
        </div>
      </div>

    </div>

  </main>

  <!-- Quick Principle & Breadboard Reference Modal (Hidden by default, opens on click) -->
  <div id="info-modal" class="hidden fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-sm flex items-center justify-center p-3">
    <div class="glass-panel rounded-2xl max-w-lg w-full p-4 sm:p-5 border border-slate-700 shadow-2xl space-y-3 max-h-[90dvh] overflow-y-auto">
      <div class="flex items-center justify-between border-b border-slate-800 pb-2">
        <h3 class="text-sm sm:text-base font-extrabold text-white">📘 무선 전력 전송 핵심 원리 & 브레드보드 배선표</h3>
        <button id="btn-close-modal" type="button" class="px-2.5 py-1 rounded-lg bg-slate-800 hover:bg-slate-700 text-xs font-bold text-slate-300">
          ✕ 닫기
        </button>
      </div>
      <div class="space-y-2.5 text-xs text-slate-200 leading-relaxed">
        <div class="bg-slate-900 p-3 rounded-xl border border-slate-800">
          <div class="font-extrabold text-sky-400 mb-1">1. 패러데이 전자기 유도 법칙 ($\mathcal{E} = -N_2 \frac{d\Phi_B}{dt} \cos\theta$)</div>
          <p class="text-slate-300">
            수신 코일에 유도되는 전압($\mathcal{E}$)은 <strong>수신 코일 감은 수($N_2$)</strong>와 <strong>시간당 자기장 변화율($d\Phi_B/dt$)</strong>에 비례하고, 코일 사이 거리($d$)가 멀어지거나 기울기 각도($\theta$)가 $90^\circ$에 가까워지면 급격히 감소합니다.
          </p>
        </div>
        <div class="bg-slate-900 p-3 rounded-xl border border-slate-800">
          <div class="font-extrabold text-emerald-400 mb-1">2. 왜 2N2222 트랜지스터와 중간 탭 코일이 필요한가?</div>
          <p class="text-slate-300">
            AA 1.5V 건전지는 일정한 직류(DC)이므로 그대로 코일에 연결하면 자기장이 변하지 않아($d\Phi_B/dt = 0$) 전력이 전송되지 않습니다. <strong>2N2222 트랜지스터</strong>와 <strong>송신 코일 중간 탭(②)</strong>이 블로킹 발진 회로를 이루어 초당 수십만 번 전류를 ON/OFF 스위칭함으로써 고주파 교류 자기장을 만듭니다.
          </p>
        </div>
        <div class="bg-slate-900 p-3 rounded-xl border border-slate-800">
          <div class="font-extrabold text-amber-300 mb-1.5">3. 170홀 미니 브레드보드 4개 줄 배선 요약</div>
          <div class="grid grid-cols-2 gap-1.5 text-[11px]">
            <div class="p-2 rounded bg-slate-950 border border-blue-500/30">
              <strong class="text-blue-400">5번 줄:</strong> TR 1번(E) + 건전지 검은선(-)
            </div>
            <div class="p-2 rounded bg-slate-950 border border-emerald-500/30">
              <strong class="text-emerald-400">6번 줄:</strong> TR 2번(B) + 1kΩ 저항 + 코일①
            </div>
            <div class="p-2 rounded bg-slate-950 border border-rose-500/30">
              <strong class="text-rose-400">7번 줄:</strong> TR 3번(C) + 코일③끝선
            </div>
            <div class="p-2 rounded bg-slate-950 border border-amber-500/30">
              <strong class="text-amber-400">10번 줄:</strong> 1kΩ 저항 + 중간탭② + 빨간선(+)
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    (function () {
      // --- Sound Effects (Web Audio API) ---
      let soundEnabled = false;
      let audioCtx = null;

      function playTone(freq, durationMs, type) {
        if (!soundEnabled) return;
        try {
          if (!audioCtx) {
            const AudioContextClass = window.AudioContext || window.webkitAudioContext;
            if (AudioContextClass) audioCtx = new AudioContextClass();
          }
          if (!audioCtx) return;
          if (audioCtx.state === 'suspended') audioCtx.resume();
          const osc = audioCtx.createOscillator();
          const gain = audioCtx.createGain();
          osc.type = type || 'sine';
          osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
          gain.gain.setValueAtTime(0.06, audioCtx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + durationMs / 1000);
          osc.connect(gain);
          gain.connect(audioCtx.destination);
          osc.start();
          osc.stop(audioCtx.currentTime + durationMs / 1000);
        } catch (e) {}
      }

      const btnSoundToggle = document.getElementById('btn-sound-toggle');
      const soundIcon = document.getElementById('sound-icon');
      btnSoundToggle.addEventListener('click', function () {
        soundEnabled = !soundEnabled;
        soundIcon.textContent = soundEnabled ? '🔊' : '🔇';
        if (soundEnabled) playTone(660, 100, 'triangle');
      });

      // --- Modal Controls ---
      const infoModal = document.getElementById('info-modal');
      document.getElementById('btn-info-modal').addEventListener('click', function () {
        infoModal.classList.remove('hidden');
        playTone(540, 80, 'sine');
      });
      document.getElementById('btn-close-modal').addEventListener('click', function () {
        infoModal.classList.add('hidden');
      });
      infoModal.addEventListener('click', function (e) {
        if (e.target === infoModal) infoModal.classList.add('hidden');
      });

      // --- Simulation State ---
      const simState = {
        powerOn: true,
        distanceCm: 1.5,     // 0.5 to 7.0 cm
        angleDeg: 0,         // 0 to 90 deg
        rxTurns: 30,         // 10 to 50 turns
        offsetCm: 0.0,       // -4.0 to +4.0 cm horizontal shift
        faultMode: 'normal', // 'normal', 'phase', 'enamel', 'tr_rev'
        isDragging: false,
        phaseTick: 0
      };

      const simMainCanvas = document.getElementById('sim-main-canvas');
      const simMainCtx = simMainCanvas.getContext('2d');
      const simWaveCanvas = document.getElementById('sim-wave-canvas');
      const simWaveCtx = simWaveCanvas.getContext('2d');

      const sliderDist = document.getElementById('slider-dist');
      const sliderAngle = document.getElementById('slider-angle');
      const sliderTurns = document.getElementById('slider-turns');
      const sliderOffset = document.getElementById('slider-offset');

      const lblDist = document.getElementById('lbl-slider-dist');
      const lblAngle = document.getElementById('lbl-slider-angle');
      const lblTurns = document.getElementById('lbl-slider-turns');
      const lblOffset = document.getElementById('lbl-slider-offset');

      const metricLed = document.getElementById('metric-led-brightness');
      const metricDist = document.getElementById('metric-distance');
      const metricVolt = document.getElementById('metric-voltage');
      const metricStatus = document.getElementById('metric-circuit-status');
      const faultDiagnosisBox = document.getElementById('fault-diagnosis-box');

      const btnPowerToggle = document.getElementById('btn-power-toggle');
      const powerDot = document.getElementById('power-dot');
      const powerLabel = document.getElementById('power-label');

      function computeSimPhysics() {
        if (!simState.powerOn || simState.faultMode !== 'normal') {
          return {
            inducedVoltage: 0,
            ledBrightness: 0,
            couplingPct: 0,
            oscillating: false
          };
        }
        const distFactor = Math.exp(-0.48 * (simState.distanceCm - 0.5));
        const angleRad = (simState.angleDeg * Math.PI) / 180;
        const angleFactor = Math.cos(angleRad);
        const offsetFactor = Math.max(0, 1 - Math.pow(Math.abs(simState.offsetCm) / 4.5, 1.6));
        const turnsFactor = simState.rxTurns / 30;

        const coupling = Math.max(0, Math.min(1, distFactor * angleFactor * offsetFactor));
        const rawV = 2.9 * coupling * turnsFactor;
        const inducedV = Math.max(0, Math.min(3.5, rawV));

        let brightness = 0;
        if (inducedV >= 1.45) {
          brightness = Math.min(100, Math.round(((inducedV - 1.35) / 1.45) * 100));
        } else if (inducedV >= 1.05) {
          brightness = Math.round(((inducedV - 1.05) / 0.4) * 18);
        }
        return {
          inducedVoltage: inducedV,
          ledBrightness: Math.max(0, Math.min(100, brightness)),
          couplingPct: Math.round(coupling * 100),
          oscillating: true
        };
      }

      function updateSimUI() {
        lblDist.textContent = simState.distanceCm.toFixed(1) + ' cm';
        lblAngle.textContent = simState.angleDeg + '°' + (simState.angleDeg === 0 ? ' (수평)' : '');
        lblTurns.textContent = simState.rxTurns + '바퀴';
        lblOffset.textContent = (simState.offsetCm > 0 ? '+' : '') + simState.offsetCm.toFixed(1) + ' cm';

        const phys = computeSimPhysics();
        metricDist.textContent = simState.distanceCm.toFixed(1) + 'cm / ' + simState.angleDeg + '°';
        metricVolt.textContent = phys.inducedVoltage.toFixed(2) + ' V';

        if (phys.ledBrightness >= 55) {
          metricLed.textContent = phys.ledBrightness + '% (밝게 점등)';
          metricLed.className = 'text-xs sm:text-sm md:text-base font-extrabold text-rose-400 truncate';
        } else if (phys.ledBrightness > 0) {
          metricLed.textContent = phys.ledBrightness + '% (약한 점등)';
          metricLed.className = 'text-xs sm:text-sm md:text-base font-extrabold text-amber-300 truncate';
        } else {
          metricLed.textContent = '0% (꺼짐)';
          metricLed.className = 'text-xs sm:text-sm md:text-base font-extrabold text-slate-500 truncate';
        }

        if (!simState.powerOn) {
          metricStatus.textContent = '전원 꺼짐 (0%)';
          metricStatus.className = 'text-xs sm:text-sm md:text-base font-extrabold text-slate-400 truncate';
        } else if (simState.faultMode === 'normal') {
          metricStatus.textContent = '결합 ' + phys.couplingPct + '% (정상)';
          metricStatus.className = 'text-xs sm:text-sm md:text-base font-extrabold text-emerald-400 truncate';
        } else if (simState.faultMode === 'phase') {
          metricStatus.textContent = '위상 반대 (발진정지)';
          metricStatus.className = 'text-xs sm:text-sm md:text-base font-extrabold text-amber-400 truncate';
        } else if (simState.faultMode === 'enamel') {
          metricStatus.textContent = '피막 절연 (전류차단)';
          metricStatus.className = 'text-xs sm:text-sm md:text-base font-extrabold text-amber-400 truncate';
        } else if (simState.faultMode === 'tr_rev') {
          metricStatus.textContent = '🔥 트랜지스터 과열';
          metricStatus.className = 'text-xs sm:text-sm md:text-base font-extrabold text-rose-500 truncate';
        }
      }

      btnPowerToggle.addEventListener('click', function () {
        simState.powerOn = !simState.powerOn;
        if (simState.powerOn) {
          btnPowerToggle.className = 'px-2.5 py-1 rounded-lg text-[11px] sm:text-xs font-extrabold bg-emerald-600 hover:bg-emerald-500 text-white border border-emerald-400 transition flex items-center gap-1 shadow';
          powerDot.className = 'w-2 h-2 rounded-full bg-white animate-pulse';
          powerLabel.textContent = 'AA 1.5V 전원 ON';
          playTone(680, 100, 'triangle');
        } else {
          btnPowerToggle.className = 'px-2.5 py-1 rounded-lg text-[11px] sm:text-xs font-extrabold bg-slate-800 hover:bg-slate-700 text-slate-300 border border-slate-600 transition flex items-center gap-1';
          powerDot.className = 'w-2 h-2 rounded-full bg-slate-500';
          powerLabel.textContent = 'AA 1.5V 전원 OFF';
          playTone(300, 100, 'sine');
        }
        updateSimUI();
      });

      sliderDist.addEventListener('input', function (e) {
        simState.distanceCm = parseFloat(e.target.value);
        updateSimUI();
      });
      sliderAngle.addEventListener('input', function (e) {
        simState.angleDeg = parseInt(e.target.value, 10);
        updateSimUI();
      });
      sliderTurns.addEventListener('input', function (e) {
        simState.rxTurns = parseInt(e.target.value, 10);
        updateSimUI();
      });
      sliderOffset.addEventListener('input', function (e) {
        simState.offsetCm = parseFloat(e.target.value);
        updateSimUI();
      });

      document.getElementById('btn-sim-optimal').addEventListener('click', function () {
        simState.powerOn = true;
        simState.distanceCm = 1.5;
        simState.angleDeg = 0;
        simState.rxTurns = 30;
        simState.offsetCm = 0.0;
        simState.faultMode = 'normal';
        sliderDist.value = '1.5';
        sliderAngle.value = '0';
        sliderTurns.value = '30';
        sliderOffset.value = '0';
        btnPowerToggle.className = 'px-2.5 py-1 rounded-lg text-[11px] sm:text-xs font-extrabold bg-emerald-600 hover:bg-emerald-500 text-white border border-emerald-400 transition flex items-center gap-1 shadow';
        powerDot.className = 'w-2 h-2 rounded-full bg-white animate-pulse';
        powerLabel.textContent = 'AA 1.5V 전원 ON';
        syncFaultButtons();
        playTone(780, 110, 'triangle');
        updateSimUI();
      });

      document.getElementById('btn-align-center').addEventListener('click', function () {
        simState.offsetCm = 0.0;
        simState.angleDeg = 0;
        sliderOffset.value = '0';
        sliderAngle.value = '0';
        playTone(620, 80, 'sine');
        updateSimUI();
      });

      const faultDescriptions = {
        normal: '<strong>[정상 발진 작동]</strong> 2N2222가 초고속 스위칭하여 교류 자기장($\\Phi_B$)을 만들고, 수신 코일에 유도 기전력이 발생해 LED가 켜집니다.',
        phase: '<strong>[원인 1위: 코일 ①시작선·③끝선 위상 반대]</strong> 피드백 위상이 반대가 되어 발진이 멈췄습니다! <strong>브레드보드 6번 줄(①)과 7번 줄(③) 코일 선을 서로 맞바꾸면 즉시 켜집니다.</strong>',
        enamel: '<strong>[원인 2위: 에나멜 피막 미제거]</strong> 구리선 끝 1.5cm에 갈색 절연 코팅이 남아 전류가 차단되었습니다! <strong>사포나 칼로 노란 생구리가 보일 때까지 360도 긁어내세요.</strong>',
        tr_rev: '<strong>[원인 3위: 트랜지스터 역방향]</strong> 2N2222의 평평한 면이 뒤로 가게 꽂혀(E·C 핀 역전) 발진이 안 되고 발열이 발생합니다! <strong>평평한 면이 정면을 보게 다시 꽂으세요.</strong>'
      };

      const faultBtns = document.querySelectorAll('.fault-mode-btn');
      function syncFaultButtons() {
        faultBtns.forEach(function (btn) {
          const fm = btn.getAttribute('data-fault');
          if (fm === simState.faultMode) {
            btn.className = 'fault-mode-btn py-1.5 px-2.5 rounded-xl text-[11px] sm:text-xs font-extrabold border text-left transition truncate ' +
              (fm === 'normal' ? 'mode-active-normal' : 'mode-active-fault');
          } else {
            btn.className = 'fault-mode-btn py-1.5 px-2.5 rounded-xl text-[11px] sm:text-xs font-bold bg-slate-800/90 hover:bg-slate-700 text-slate-200 border border-slate-700 text-left transition truncate';
          }
        });
        faultDiagnosisBox.innerHTML = faultDescriptions[simState.faultMode];
      }

      faultBtns.forEach(function (btn) {
        btn.addEventListener('click', function () {
          simState.faultMode = btn.getAttribute('data-fault');
          playTone(simState.faultMode === 'normal' ? 700 : 250, 110, 'triangle');
          syncFaultButtons();
          updateSimUI();
        });
      });

      // --- Direct Touch & Mouse Dragging of Receiver Coil ---
      function handleCanvasDrag(clientX, clientY) {
        if (!simState.isDragging) return;
        const rect = simMainCanvas.getBoundingClientRect();
        const x = clientX - rect.left;
        const y = clientY - rect.top;
        const cx = rect.width / 2;
        const txY = rect.height - 62;
        const maxTravelPx = Math.max(90, rect.height - 135);

        const clampedPxGap = Math.max(20, Math.min(maxTravelPx, txY - y));
        const newDist = 0.5 + ((clampedPxGap - 20) / (maxTravelPx - 20)) * 6.5;
        simState.distanceCm = Math.round(newDist * 10) / 10;

        const maxHorizPx = Math.max(70, rect.width * 0.34);
        const rawOffsetCm = ((x - cx) / maxHorizPx) * 4.0;
        simState.offsetCm = Math.round(Math.max(-4.0, Math.min(4.0, rawOffsetCm)) * 5) / 5;

        sliderDist.value = String(simState.distanceCm);
        sliderOffset.value = String(simState.offsetCm);
        updateSimUI();
      }

      simMainCanvas.addEventListener('mousedown', function (e) {
        simState.isDragging = true;
        handleCanvasDrag(e.clientX, e.clientY);
      });
      window.addEventListener('mousemove', function (e) {
        if (simState.isDragging) handleCanvasDrag(e.clientX, e.clientY);
      });
      window.addEventListener('mouseup', function () {
        simState.isDragging = false;
      });

      simMainCanvas.addEventListener('touchstart', function (e) {
        if (e.touches.length > 0) {
          simState.isDragging = true;
          handleCanvasDrag(e.touches[0].clientX, e.touches[0].clientY);
        }
      }, { passive: true });
      simMainCanvas.addEventListener('touchmove', function (e) {
        if (simState.isDragging && e.touches.length > 0) {
          handleCanvasDrag(e.touches[0].clientX, e.touches[0].clientY);
        }
      }, { passive: true });
      simMainCanvas.addEventListener('touchend', function () {
        simState.isDragging = false;
      });

      // --- Render Main Physics Canvas ---
      function drawMainSimulator() {
        const rect = simMainCanvas.parentElement.getBoundingClientRect();
        const dpr = window.devicePixelRatio || 1;
        const w = Math.max(240, rect.width);
        const h = Math.max(170, rect.height);
        if (simMainCanvas.width !== Math.floor(w * dpr) || simMainCanvas.height !== Math.floor(h * dpr)) {
          simMainCanvas.width = Math.floor(w * dpr);
          simMainCanvas.height = Math.floor(h * dpr);
        }
        simMainCtx.save();
        simMainCtx.scale(dpr, dpr);
        simMainCtx.clearRect(0, 0, w, h);

        // Background subtle lab grid
        simMainCtx.strokeStyle = 'rgba(148, 163, 184, 0.06)';
        simMainCtx.lineWidth = 1;
        for (let gx = 0; gx < w; gx += 28) {
          simMainCtx.beginPath(); simMainCtx.moveTo(gx, 0); simMainCtx.lineTo(gx, h); simMainCtx.stroke();
        }
        for (let gy = 0; gy < h; gy += 28) {
          simMainCtx.beginPath(); simMainCtx.moveTo(0, gy); simMainCtx.lineTo(w, gy); simMainCtx.stroke();
        }

        const cx = w / 2;
        const txY = h - 54;
        const maxTravelPx = Math.max(85, h - 120);
        const pixelGap = 20 + ((simState.distanceCm - 0.5) / 6.5) * (maxTravelPx - 20);
        const maxHorizPx = Math.max(70, w * 0.34);
        const rxX = cx + (simState.offsetCm / 4.0) * maxHorizPx;
        const rxY = txY - pixelGap;

        const phys = computeSimPhysics();
        const coilHalfW = Math.min(76, w * 0.23);

        // 1. Animated Magnetic Flux Lines (if oscillating)
        if (phys.oscillating) {
          const pulse = (Math.sin(simState.phaseTick) + 1) * 0.5;
          const alpha = 0.18 + (phys.couplingPct / 100) * 0.45 + pulse * 0.18;
          simMainCtx.strokeStyle = 'rgba(56, 189, 248, ' + Math.min(0.85, alpha) + ')';
          simMainCtx.lineWidth = 2;
          simMainCtx.setLineDash([6, 6]);
          simMainCtx.lineDashOffset = -simState.phaseTick * 6;

          [-0.75, -0.38, 0, 0.38, 0.75].forEach(function (ratio) {
            const ox = coilHalfW * ratio;
            simMainCtx.beginPath();
            simMainCtx.moveTo(cx + ox, txY - 10);
            simMainCtx.quadraticCurveTo(
              cx + ox * 1.45,
              (txY + rxY) / 2,
              rxX + ox * Math.cos((simState.angleDeg * Math.PI) / 180),
              rxY + 8
            );
            simMainCtx.stroke();
          });
          simMainCtx.setLineDash([]);
        }

        // 2. Transmitter Breadboard & 1st Coil at Bottom
        const bbW = Math.min(240, w * 0.68);
        simMainCtx.fillStyle = '#1e293b';
        simMainCtx.strokeStyle = '#475569';
        simMainCtx.lineWidth = 1.5;
        simMainCtx.beginPath();
        simMainCtx.roundRect(cx - bbW / 2, txY + 14, bbW, 30, 8);
        simMainCtx.fill();
        simMainCtx.stroke();

        simMainCtx.fillStyle = '#e2e8f0';
        simMainCtx.font = 'bold 10px "Noto Sans KR", sans-serif';
        simMainCtx.textAlign = 'center';
        simMainCtx.fillText('송신부: 170홀 브레드보드 (2N2222 + 1kΩ + AA 1.5V)', cx, txY + 33);

        // Transmitter Coil Loops
        for (let i = -2; i <= 2; i++) {
          simMainCtx.strokeStyle = simState.faultMode === 'enamel' ? '#78350f' : '#f59e0b';
          simMainCtx.lineWidth = 2.8;
          simMainCtx.beginPath();
          simMainCtx.ellipse(cx, txY + i * 2, coilHalfW, 13, 0, 0, Math.PI * 2);
          simMainCtx.stroke();
        }
        simMainCtx.fillStyle = '#fde68a';
        simMainCtx.font = 'bold 10px "Noto Sans KR", sans-serif';
        simMainCtx.fillText('1차 송신 코일 (30턴·중간탭)', cx, txY + 3);

        if (simState.faultMode === 'tr_rev' && simState.powerOn) {
          simMainCtx.fillStyle = '#fb7185';
          simMainCtx.font = 'bold 11px "Noto Sans KR", sans-serif';
          simMainCtx.fillText('🔥 2N2222 과열 경보! (핀 방향 역전)', cx, txY - 20);
        } else if (simState.faultMode === 'phase' && simState.powerOn) {
          simMainCtx.fillStyle = '#fbbf24';
          simMainCtx.font = 'bold 11px "Noto Sans KR", sans-serif';
          simMainCtx.fillText('⚠️ 코일 ①·③ 위상 반대 (발진 멈춤)', cx, txY - 20);
        } else if (simState.faultMode === 'enamel' && simState.powerOn) {
          simMainCtx.fillStyle = '#fbbf24';
          simMainCtx.font = 'bold 11px "Noto Sans KR", sans-serif';
          simMainCtx.fillText('⚠️ 에나멜 코팅 절연 상태 (전류 차단)', cx, txY - 20);
        }

        // 3. Floating Draggable Receiver Coil + 5mm Red LED
        simMainCtx.save();
        simMainCtx.translate(rxX, rxY);
        simMainCtx.rotate((simState.angleDeg * Math.PI) / 180);

        const rxLoops = Math.max(3, Math.round(simState.rxTurns / 10));
        for (let j = -rxLoops + 1; j <= rxLoops - 1; j++) {
          simMainCtx.strokeStyle = '#22c55e';
          simMainCtx.lineWidth = 2.6;
          simMainCtx.beginPath();
          simMainCtx.ellipse(0, j * 1.8, coilHalfW * 0.95, 12, 0, 0, Math.PI * 2);
          simMainCtx.stroke();
        }
        simMainCtx.fillStyle = '#bbf7d0';
        simMainCtx.font = 'bold 10px "Noto Sans KR", sans-serif';
        simMainCtx.textAlign = 'center';
        simMainCtx.fillText('2차 수신 코일 (' + simState.rxTurns + '턴)', 0, 3);

        // LED twisted to receiver coil on the right
        const ledX = coilHalfW * 0.95 + 22;
        const ledY = -2;
        simMainCtx.strokeStyle = '#facc15';
        simMainCtx.lineWidth = 2.2;
        simMainCtx.beginPath();
        simMainCtx.moveTo(coilHalfW * 0.92, -4);
        simMainCtx.lineTo(ledX - 8, ledY - 3);
        simMainCtx.moveTo(coilHalfW * 0.92, 4);
        simMainCtx.lineTo(ledX - 8, ledY + 3);
        simMainCtx.stroke();

        if (phys.ledBrightness > 0) {
          const glowR = 14 + (phys.ledBrightness / 100) * 34;
          const grad = simMainCtx.createRadialGradient(ledX, ledY, 2, ledX, ledY, glowR);
          grad.addColorStop(0, 'rgba(255, 60, 60, ' + (0.45 + phys.ledBrightness / 160) + ')');
          grad.addColorStop(0.5, 'rgba(239, 68, 68, ' + (phys.ledBrightness / 220) + ')');
          grad.addColorStop(1, 'rgba(239, 68, 68, 0)');
          simMainCtx.fillStyle = grad;
          simMainCtx.beginPath();
          simMainCtx.arc(ledX, ledY, glowR, 0, Math.PI * 2);
          simMainCtx.fill();
        }

        simMainCtx.fillStyle = phys.ledBrightness > 0 ? '#ff1744' : '#7f1d1d';
        simMainCtx.strokeStyle = phys.ledBrightness > 0 ? '#fecdd3' : '#991b1b';
        simMainCtx.lineWidth = 2;
        simMainCtx.beginPath();
        simMainCtx.arc(ledX, ledY, 10, 0, Math.PI * 2);
        simMainCtx.fill();
        simMainCtx.stroke();

        simMainCtx.fillStyle = '#ffffff';
        simMainCtx.font = 'bold 9px "Inter", sans-serif';
        simMainCtx.fillText(phys.ledBrightness > 0 ? 'ON' : 'OFF', ledX, ledY + 3);

        simMainCtx.restore();

        // 4. Vertical Distance Ruler on Left Side
        const rulerX = 24;
        simMainCtx.strokeStyle = 'rgba(56, 189, 248, 0.5)';
        simMainCtx.lineWidth = 1.5;
        simMainCtx.beginPath();
        simMainCtx.moveTo(rulerX, rxY);
        simMainCtx.lineTo(rulerX, txY);
        simMainCtx.moveTo(rulerX - 5, rxY);
        simMainCtx.lineTo(rulerX + 5, rxY);
        simMainCtx.moveTo(rulerX - 5, txY);
        simMainCtx.lineTo(rulerX + 5, txY);
        simMainCtx.stroke();

        simMainCtx.fillStyle = '#38bdf8';
        simMainCtx.font = 'bold 10px "Noto Sans KR", sans-serif';
        simMainCtx.textAlign = 'left';
        simMainCtx.fillText(simState.distanceCm.toFixed(1) + 'cm', rulerX + 8, (rxY + txY) / 2 + 3);

        simMainCtx.restore();
      }

      // --- Render Oscilloscope Waveform Canvas ---
      function drawWaveCanvas() {
        const rect = simWaveCanvas.parentElement.getBoundingClientRect();
        const dpr = window.devicePixelRatio || 1;
        const w = Math.max(200, rect.width);
        const h = Math.max(60, rect.height);
        if (simWaveCanvas.width !== Math.floor(w * dpr) || simWaveCanvas.height !== Math.floor(h * dpr)) {
          simWaveCanvas.width = Math.floor(w * dpr);
          simWaveCanvas.height = Math.floor(h * dpr);
        }
        simWaveCtx.save();
        simWaveCtx.scale(dpr, dpr);
        simWaveCtx.clearRect(0, 0, w, h);

        const midY = h / 2;
        simWaveCtx.strokeStyle = 'rgba(148, 163, 184, 0.18)';
        simWaveCtx.lineWidth = 1;
        simWaveCtx.beginPath();
        simWaveCtx.moveTo(0, midY);
        simWaveCtx.lineTo(w, midY);
        simWaveCtx.stroke();

        const phys = computeSimPhysics();

        // Transmitter AC Magnetic Flux Wave
        simWaveCtx.strokeStyle = '#f59e0b';
        simWaveCtx.lineWidth = 1.8;
        simWaveCtx.beginPath();
        const txAmp = phys.oscillating ? h * 0.34 : 0;
        for (let x = 0; x < w; x += 3) {
          const y = midY + Math.sin(x * 0.065 - simState.phaseTick * 2) * txAmp;
          if (x === 0) simWaveCtx.moveTo(x, y);
          else simWaveCtx.lineTo(x, y);
        }
        simWaveCtx.stroke();

        // Receiver Induced EMF Wave
        simWaveCtx.strokeStyle = '#fb7185';
        simWaveCtx.lineWidth = 2.2;
        simWaveCtx.beginPath();
        const rxAmp = (phys.inducedVoltage / 3.4) * (h * 0.42);
        for (let x = 0; x < w; x += 3) {
          const y = midY + Math.cos(x * 0.065 - simState.phaseTick * 2) * rxAmp;
          if (x === 0) simWaveCtx.moveTo(x, y);
          else simWaveCtx.lineTo(x, y);
        }
        simWaveCtx.stroke();

        simWaveCtx.restore();
      }

      function animateLoop() {
        simState.phaseTick += 0.14;
        drawMainSimulator();
        drawWaveCanvas();
        requestAnimationFrame(animateLoop);
      }

      window.addEventListener('resize', function () {
        drawMainSimulator();
        drawWaveCanvas();
      });

      window.addEventListener('load', function () {
        updateSimUI();
        animateLoop();
      });
    })();
  </script>
</body>
</html>
