<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>선이 없는데 왜 불이 켜질까? - 무선 전력 전송 실험실</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=Noto+Sans+KR:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
    }
    html, body {
      font-family: 'Noto Sans KR', 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
      background-color: #f1f5f9;
      color: #0f172a;
      margin: 0;
      padding: 0;
      width: 100%;
      user-select: none;
      -webkit-font-smoothing: antialiased;
    }
    /* GitHub Pages / Markdown CSS 충돌 방지 */
    h1, h2, h3, h4, p {
      border-bottom: none !important;
      margin: 0;
    }
    .lab-card {
      background: #ffffff;
      border: 1px solid #e2e8f0;
      box-shadow: 0 2px 8px rgba(15, 23, 42, 0.04);
    }
    input[type="range"] {
      -webkit-appearance: none;
      appearance: none;
      height: 8px;
      background: #e2e8f0;
      border-radius: 999px;
      outline: none;
    }
    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      appearance: none;
      width: 22px;
      height: 22px;
      border-radius: 50%;
      background: #2563eb;
      border: 3px solid #ffffff;
      box-shadow: 0 2px 6px rgba(37, 99, 235, 0.35);
      cursor: pointer;
    }
    input[type="range"]::-moz-range-thumb {
      width: 22px;
      height: 22px;
      border-radius: 50%;
      background: #2563eb;
      border: 3px solid #ffffff;
      box-shadow: 0 2px 6px rgba(37, 99, 235, 0.35);
      cursor: pointer;
    }
    .mode-active-normal {
      background-color: #eff6ff !important;
      border-color: #2563eb !important;
      color: #1d4ed8 !important;
      font-weight: 700 !important;
    }
    .mode-active-fault {
      background-color: #fff1f2 !important;
      border-color: #e11d48 !important;
      color: #be123c !important;
      font-weight: 700 !important;
    }
  </style>
</head>
<body class="min-h-dvh md:h-dvh w-full flex flex-col md:overflow-hidden bg-slate-100 text-slate-900">

  <!-- 상단 헤더 바 -->
  <header class="shrink-0 bg-white border-b border-slate-200 px-3.5 py-2.5 sm:px-5 flex flex-wrap items-center justify-between gap-2">
    <div class="flex items-center gap-2.5 min-w-0">
      <span class="px-2.5 py-1 rounded-md bg-blue-600 text-white font-bold text-xs tracking-tight shrink-0">
        과학 실험실
      </span>
      <div class="min-w-0">
        <h1 class="text-sm sm:text-base md:text-lg font-extrabold tracking-tight text-slate-900 truncate">
          선이 없는데 왜 LED에 불이 켜질까? (무선 전력 전송 실험)
        </h1>
      </div>
    </div>

    <div class="flex items-center gap-1.5 sm:gap-2 shrink-0">
      <button id="btn-power-toggle" type="button" class="px-3 py-1.5 rounded-lg text-xs font-bold bg-emerald-600 hover:bg-emerald-700 text-white transition flex items-center gap-1.5 shadow-sm">
        <span id="power-dot" class="w-2 h-2 rounded-full bg-white animate-pulse"></span>
        <span id="power-label">건전지 전원 켜짐</span>
      </button>
      <button id="btn-sim-optimal" type="button" class="px-3 py-1.5 rounded-lg text-xs font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 border border-slate-300 transition">
        가장 잘 켜지는 위치로
      </button>
      <button id="btn-sound-toggle" type="button" class="px-2.5 py-1.5 rounded-lg text-xs font-semibold bg-slate-100 hover:bg-slate-200 text-slate-700 border border-slate-300 transition" title="효과음 켜기/끄기">
        <span id="sound-icon">소리 꺼짐</span>
      </button>
      <button id="btn-info-modal" type="button" class="px-3 py-1.5 rounded-lg text-xs font-bold bg-blue-50 hover:bg-blue-100 text-blue-700 border border-blue-200 transition">
        쉬운 원리·배선표 보기
      </button>
    </div>
  </header>

  <!-- 초보자용 3단계 쉬운 원리 요약 배너 -->
  <section class="shrink-0 bg-white/80 border-b border-slate-200/80 px-3.5 py-2 sm:px-5">
    <div class="max-w-7xl mx-auto grid grid-cols-1 sm:grid-cols-3 gap-1.5 sm:gap-3 text-xs">
      <div class="flex items-center gap-2 bg-amber-50/90 border border-amber-200/80 rounded-lg px-2.5 py-1.5">
        <span class="px-1.5 py-0.5 rounded bg-amber-500 text-white font-bold text-[10px] shrink-0">1단계</span>
        <span class="text-slate-700 leading-snug"><strong class="text-slate-900">전류 흔들기:</strong> 검은색 부품(트랜지스터)이 건전지 전기를 1초에 수십만 번 껐다 켰다 반복해요.</span>
      </div>
      <div class="flex items-center gap-2 bg-sky-50/90 border border-sky-200/80 rounded-lg px-2.5 py-1.5">
        <span class="px-1.5 py-0.5 rounded bg-sky-600 text-white font-bold text-[10px] shrink-0">2단계</span>
        <span class="text-slate-700 leading-snug"><strong class="text-slate-900">자기장 물결 발생:</strong> 전기가 흔들리면 <strong class="text-amber-700">아래쪽 코일</strong> 주변에 보이지 않는 자석 힘(자기장) 물결이 퍼져요.</span>
      </div>
      <div class="flex items-center gap-2 bg-emerald-50/90 border border-emerald-200/80 rounded-lg px-2.5 py-1.5">
        <span class="px-1.5 py-0.5 rounded bg-emerald-600 text-white font-bold text-[10px] shrink-0">3단계</span>
        <span class="text-slate-700 leading-snug"><strong class="text-slate-900">무선으로 불 켜짐:</strong> 자기장 물결이 <strong class="text-emerald-700">위쪽 코일</strong> 구멍을 통과하면 새 전기가 생겨 LED가 켜져요!</span>
      </div>
    </div>
  </section>

  <!-- 메인 실험 화면 (태블릿/PC는 좌우 1화면 분할, 스마트폰은 위아래 자연스러운 배치) -->
  <main class="flex-1 min-h-0 w-full max-w-7xl mx-auto p-2.5 sm:p-3.5 grid grid-cols-1 md:grid-cols-12 gap-3 md:overflow-hidden">

    <!-- 왼쪽(모바일 상단): 실험 결과 수치 + 직접 움직이는 코일 실험 캔버스 (7칸) -->
    <div class="md:col-span-7 flex flex-col min-h-0 gap-2.5">

      <!-- 한눈에 보는 현재 실험 상태 4칸 -->
      <div class="shrink-0 grid grid-cols-2 sm:grid-cols-4 gap-2">
        <div class="lab-card rounded-xl px-3 py-2 border-l-4 border-l-rose-500">
          <div class="text-[11px] text-slate-500 font-medium">빨간색 LED 전구</div>
          <div id="metric-led-brightness" class="text-sm sm:text-base font-extrabold text-rose-600 mt-0.5 truncate">밝게 켜짐 (92%)</div>
        </div>
        <div class="lab-card rounded-xl px-3 py-2 border-l-4 border-l-blue-500">
          <div class="text-[11px] text-slate-500 font-medium">코일 거리와 각도</div>
          <div id="metric-distance" class="text-sm sm:text-base font-extrabold text-blue-700 mt-0.5 truncate">1.5cm / 수평(0°)</div>
        </div>
        <div class="lab-card rounded-xl px-3 py-2 border-l-4 border-l-amber-500">
          <div class="text-[11px] text-slate-500 font-medium">위쪽 코일에 생긴 전기</div>
          <div id="metric-voltage" class="text-sm sm:text-base font-extrabold text-amber-600 mt-0.5 truncate">2.35 V (켜짐)</div>
        </div>
        <div class="lab-card rounded-xl px-3 py-2 border-l-4 border-l-emerald-500">
          <div class="text-[11px] text-slate-500 font-medium">자기장 전달 상태</div>
          <div id="metric-circuit-status" class="text-sm sm:text-base font-extrabold text-emerald-600 mt-0.5 truncate">88% 전달 중</div>
        </div>
      </div>

      <!-- 직접 드래그해서 실험하는 캔버스 영역 -->
      <div class="flex-1 min-h-[330px] sm:min-h-[370px] md:min-h-0 relative lab-card rounded-2xl overflow-hidden touch-none flex flex-col">
        <!-- 상단 실시간 쉬운 해설 바 -->
        <div class="shrink-0 bg-slate-800 text-white px-3.5 py-2 flex items-center justify-between gap-2 border-b border-slate-700">
          <div id="live-easy-guide" class="text-xs sm:text-sm font-medium leading-snug text-slate-100">
            위쪽 <strong class="text-emerald-300">받는 코일</strong>을 손가락이나 마우스로 잡고 위아래·좌우로 움직여 보세요!
          </div>
          <button id="btn-align-center" type="button" class="shrink-0 bg-slate-700 hover:bg-slate-600 text-white text-xs font-semibold px-2.5 py-1 rounded-lg border border-slate-600 transition">
            가운데로 정렬
          </button>
        </div>

        <div class="flex-1 relative w-full h-full">
          <canvas id="sim-main-canvas" class="w-full h-full block cursor-grab active:cursor-grabbing"></canvas>
        </div>
      </div>

    </div>

    <!-- 오른쪽(모바일 하단): 조건 바꿔보기 슬라이더 + 전기 물결 그래프 + 고장 원인 체험 (5칸) -->
    <div class="md:col-span-5 flex flex-col justify-between min-h-0 gap-2.5 md:overflow-y-auto pr-0.5">

      <!-- 1. 직접 조건을 바꿔보는 슬라이더 컨트롤 -->
      <div class="shrink-0 lab-card rounded-2xl p-3.5 space-y-3">
        <div class="flex items-center justify-between border-b border-slate-100 pb-2">
          <div>
            <h2 class="text-sm font-extrabold text-slate-900">직접 조건을 바꾸며 실험해 보세요</h2>
            <p class="text-[11px] text-slate-500 mt-0.5">막대를 움직이면 왼쪽 코일과 LED 밝기가 바로 바뀝니다.</p>
          </div>
        </div>

        <!-- 슬라이더 1: 코일 사이 거리 -->
        <div class="bg-slate-50 rounded-xl p-2.5 border border-slate-200/70">
          <div class="flex justify-between items-center text-xs font-bold mb-1.5">
            <span class="text-slate-800">1. 두 코일 사이의 거리 (가까울수록 잘 켜짐)</span>
            <span id="lbl-slider-dist" class="text-blue-600 font-extrabold bg-blue-50 px-2 py-0.5 rounded border border-blue-200">1.5 cm</span>
          </div>
          <input id="slider-dist" type="range" min="0.5" max="7.0" step="0.1" value="1.5" class="w-full cursor-pointer" />
          <div class="flex justify-between text-[11px] text-slate-500 mt-1">
            <span>0.5cm (아주 가까움·최대 밝기)</span>
            <span>2.0cm (적당함)</span>
            <span>7.0cm (너무 멀어서 꺼짐)</span>
          </div>
        </div>

        <!-- 슬라이더 2: 코일 기울기 각도 -->
        <div class="bg-slate-50 rounded-xl p-2.5 border border-slate-200/70">
          <div class="flex justify-between items-center text-xs font-bold mb-1.5">
            <span class="text-slate-800">2. 위쪽 코일 기울이기 (비딱하면 어떻게 될까?)</span>
            <span id="lbl-slider-angle" class="text-emerald-700 font-extrabold bg-emerald-50 px-2 py-0.5 rounded border border-emerald-200">0° (수평)</span>
          </div>
          <input id="slider-angle" type="range" min="0" max="90" step="5" value="0" class="w-full cursor-pointer" />
          <div class="flex justify-between text-[11px] text-slate-500 mt-1">
            <span>0° (마주 볼 때 제일 밝음)</span>
            <span>45° (비스듬함·흐려짐)</span>
            <span>90° (수직·자기장 못 받아 꺼짐)</span>
          </div>
        </div>

        <!-- 슬라이더 3 & 4: 코일 감은 횟수 & 좌우 위치 -->
        <div class="grid grid-cols-2 gap-2">
          <div class="bg-slate-50 rounded-xl p-2.5 border border-slate-200/70">
            <div class="flex justify-between items-center text-xs font-bold mb-1.5">
              <span class="text-slate-800">3. 코일 감은 횟수</span>
              <span id="lbl-slider-turns" class="text-amber-700 font-extrabold">30바퀴</span>
            </div>
            <input id="slider-turns" type="range" min="10" max="50" step="5" value="30" class="w-full cursor-pointer" />
            <div class="text-[10px] text-slate-500 mt-1">많이 감을수록 전기가 더 많이 생겨요</div>
          </div>
          <div class="bg-slate-50 rounded-xl p-2.5 border border-slate-200/70">
            <div class="flex justify-between items-center text-xs font-bold mb-1.5">
              <span class="text-slate-800">4. 좌우 어긋남</span>
              <span id="lbl-slider-offset" class="text-purple-700 font-extrabold">0.0 cm</span>
            </div>
            <input id="slider-offset" type="range" min="-4.0" max="4.0" step="0.2" value="0" class="w-full cursor-pointer" />
            <div class="text-[10px] text-slate-500 mt-1">옆으로 비껴나면 불빛이 약해져요</div>
          </div>
        </div>
      </div>

      <!-- 2. 눈으로 보는 전기 물결 그래프 (오실로스코프) -->
      <div class="shrink-0 lab-card rounded-2xl p-3">
        <div class="flex items-center justify-between text-xs mb-1.5">
          <div>
            <span class="font-extrabold text-slate-900">눈으로 보는 전기 물결 (교류 파형)</span>
            <span class="text-[11px] text-slate-500 ml-1">물결이 높게 출렁일수록 LED가 밝아져요</span>
          </div>
        </div>
        <div class="flex items-center gap-3 text-[11px] mb-1.5">
          <span class="flex items-center gap-1 font-semibold text-amber-600">
            <span class="inline-block w-3 h-0.5 bg-amber-500"></span> 아래 코일이 보내는 자기장
          </span>
          <span class="flex items-center gap-1 font-semibold text-rose-600">
            <span class="inline-block w-3 h-0.5 bg-rose-500"></span> 위 코일에 만들어지는 전기
          </span>
        </div>
        <div class="relative w-full bg-slate-900 rounded-xl border border-slate-800 overflow-hidden" style="height: 68px;">
          <canvas id="sim-wave-canvas" class="w-full h-full block"></canvas>
        </div>
      </div>

      <!-- 3. 실제 만들 때 불이 안 켜지는 대표 원인 3가지 비교 -->
      <div class="shrink-0 lab-card rounded-2xl p-3.5 space-y-2.5">
        <div>
          <h3 class="text-xs sm:text-sm font-extrabold text-slate-900">
            실제로 만들었는데 불이 안 켜진다면? (원인 눌러보기)
          </h3>
          <p class="text-[11px] text-slate-500 mt-0.5">버튼을 눌러 실제 실험에서 가장 많이 실수하는 원인을 확인해 보세요.</p>
        </div>

        <div class="grid grid-cols-2 gap-1.5">
          <button type="button" data-fault="normal" class="fault-mode-btn mode-active-normal py-2 px-2.5 rounded-xl text-xs border border-slate-200 bg-slate-50 text-slate-700 text-left transition truncate">
            정상 연결 (불 잘 켜짐)
          </button>
          <button type="button" data-fault="enamel" class="fault-mode-btn py-2 px-2.5 rounded-xl text-xs border border-slate-200 bg-slate-50 hover:bg-slate-100 text-slate-700 text-left transition truncate">
            실수 1: 코일 끝 코팅 안 벗김
          </button>
          <button type="button" data-fault="phase" class="fault-mode-btn py-2 px-2.5 rounded-xl text-xs border border-slate-200 bg-slate-50 hover:bg-slate-100 text-slate-700 text-left transition truncate">
            실수 2: 코일 ①·③번 선 바뀜
          </button>
          <button type="button" data-fault="tr_rev" class="fault-mode-btn py-2 px-2.5 rounded-xl text-xs border border-slate-200 bg-slate-50 hover:bg-slate-100 text-slate-700 text-left transition truncate">
            실수 3: 트랜지스터 거꾸로 꽂음
          </button>
        </div>

        <div id="fault-diagnosis-box" class="bg-slate-50 rounded-xl p-2.5 border border-slate-200 text-xs text-slate-700 leading-relaxed">
          <strong class="text-blue-700">[정상 작동 중]</strong> 건전지 전기가 트랜지스터를 거쳐 아래쪽 코일에서 자기장 물결을 만들고, 위쪽 코일이 그 힘을 받아 LED에 불을 켭니다.
        </div>
      </div>

    </div>

  </main>

  <!-- 쉬운 원리 설명 & 브레드보드 배선표 모달 창 -->
  <div id="info-modal" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-3">
    <div class="bg-white rounded-2xl max-w-lg w-full p-5 border border-slate-200 shadow-2xl space-y-4 max-h-[90dvh] overflow-y-auto text-slate-800">
      <div class="flex items-center justify-between border-b border-slate-200 pb-3">
        <h3 class="text-base font-extrabold text-slate-900">선이 없는데 어떻게 전기가 전달될까요?</h3>
        <button id="btn-close-modal" type="button" class="px-3 py-1 rounded-lg bg-slate-100 hover:bg-slate-200 text-xs font-bold text-slate-700">
          닫기
        </button>
      </div>

      <div class="space-y-3 text-xs sm:text-sm leading-relaxed">
        <div class="bg-blue-50/70 p-3.5 rounded-xl border border-blue-100">
          <div class="font-extrabold text-blue-800 mb-1">1. 스마트폰 무선 충전기와 똑같은 원리예요 (전자기 유도)</div>
          <p class="text-slate-700">
            자석을 코일(구리선 고리) 가까이 넣었다 뺐다 흔들면 코일에 전기가 생깁니다. 우리 실험에서는 손으로 자석을 흔드는 대신, <strong>아래쪽 코일에 흐르는 전기를 빠르게 흔들어서 보이지 않는 자석 힘(자기장 물결)</strong>을 공중으로 보냅니다.
          </p>
        </div>

        <div class="bg-amber-50/70 p-3.5 rounded-xl border border-amber-100">
          <div class="font-extrabold text-amber-900 mb-1">2. 검은색 다리 3개 부품(2N2222 트랜지스터)은 왜 쓰나요?</div>
          <p class="text-slate-700">
            건전지(AA 1.5V)는 전기가 한 방향으로만 잔잔하게 흘러서(직류) 그대로 코일에 연결하면 자기장 물결이 생기지 않아요. <strong>트랜지스터</strong>와 <strong>코일 가운데 고리(중간탭)</strong>를 연결하면, 전기를 <strong>1초에 수십만 번 자동으로 껐다 켰다 하는 초고속 스위치</strong> 역할을 해 줍니다.
          </p>
        </div>

        <div class="bg-slate-50 p-3.5 rounded-xl border border-slate-200">
          <div class="font-extrabold text-slate-900 mb-2">3. 브레드보드(빵판) 똑같이 꽂는 4줄 요약표</div>
          <p class="text-xs text-slate-500 mb-2">트랜지스터의 납작한 면이 나를 보게 두고, 왼쪽 다리부터 5번·6번·7번 줄에 나란히 꽂으세요.</p>
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-2 text-xs">
            <div class="p-2.5 rounded-lg bg-white border border-slate-200">
              <strong class="text-blue-700">5번 줄:</strong> 트랜지스터 왼쪽 다리 + 건전지 검은선(-)
            </div>
            <div class="p-2.5 rounded-lg bg-white border border-slate-200">
              <strong class="text-emerald-700">6번 줄:</strong> 트랜지스터 가운데 다리 + 저항 한쪽 + 코일 ①시작선
            </div>
            <div class="p-2.5 rounded-lg bg-white border border-slate-200">
              <strong class="text-rose-700">7번 줄:</strong> 트랜지스터 오른쪽 다리 + 코일 ③끝선
            </div>
            <div class="p-2.5 rounded-lg bg-white border border-slate-200">
              <strong class="text-amber-700">10번 줄:</strong> 저항 반대쪽 + 코일 ②중간탭 + 건전지 빨간선(+)
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script>
    (function () {
      // --- 효과음 (Web Audio API) ---
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
          gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
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
        soundIcon.textContent = soundEnabled ? '소리 켜짐' : '소리 꺼짐';
        if (soundEnabled) playTone(660, 100, 'triangle');
      });

      // --- 모달 열기/닫기 ---
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

      // --- 실험 상태값 ---
      const simState = {
        powerOn: true,
        distanceCm: 1.5,     // 0.5 ~ 7.0 cm
        angleDeg: 0,         // 0 ~ 90 도
        rxTurns: 30,         // 10 ~ 50 바퀴
        offsetCm: 0.0,       // -4.0 ~ +4.0 cm
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
      const liveEasyGuide = document.getElementById('live-easy-guide');

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

      function buildEasyGuideText(phys) {
        if (!simState.powerOn) {
          return '현재 건전지 전원이 꺼져 있어요. 상단의 <strong>[건전지 전원]</strong> 버튼을 눌러 전기를 켜 보세요.';
        }
        if (simState.faultMode === 'enamel') {
          return '<strong>[코팅 안 벗김]</strong> 에나멜선 끝의 갈색 코팅을 사포로 안 벗겨서 전기가 통하지 않고 있어요!';
        }
        if (simState.faultMode === 'phase') {
          return '<strong>[코일 선 바뀜]</strong> 아래 코일의 ①번 선과 ③번 선 위치가 바뀌어 전기 진동이 멈췄어요. 두 선을 맞바꿔 보세요!';
        }
        if (simState.faultMode === 'tr_rev') {
          return '<strong>[트랜지스터 방향 반대]</strong> 검은색 부품(트랜지스터)이 뒤집혀 꽂혀서 작동하지 않고 열만 나고 있어요!';
        }
        if (simState.angleDeg >= 75) {
          return '위쪽 코일이 <strong>세로(' + simState.angleDeg + '°)로 기울어져</strong> 있어서 자기장 물결이 코일 구멍을 통과하지 못해 불이 꺼졌어요!';
        }
        if (Math.abs(simState.offsetCm) >= 2.2 && phys.ledBrightness === 0) {
          return '위쪽 코일이 <strong>옆으로 너무 치우쳐 있어서</strong> 자기장을 받지 못해요. 가운데로 끌어당겨 보세요!';
        }
        if (simState.distanceCm > 3.2 && phys.ledBrightness === 0) {
          return '두 코일 사이가 <strong>너무 멀어요(' + simState.distanceCm.toFixed(1) + 'cm)</strong>. 위쪽 코일을 잡고 아래쪽 코일 가까이로 내려보세요!';
        }
        if (phys.ledBrightness >= 60) {
          return '잘했어요! 두 코일이 가깝고 마주 보고 있어서 <strong>자기장 물결이 잘 통과해 LED에 밝게 불이 켜졌습니다!</strong>';
        }
        if (phys.ledBrightness > 0) {
          return '전기가 약하게 만들어져 <strong>LED가 희미하게 켜졌어요</strong>. 코일을 조금만 더 가까이 내려볼까요?';
        }
        return '위쪽 코일을 아래쪽 코일 가까이(2cm 이내) 가져가면 LED에 불이 켜집니다!';
      }

      function updateSimUI() {
        lblDist.textContent = simState.distanceCm.toFixed(1) + ' cm';
        lblAngle.textContent = simState.angleDeg + '°' + (simState.angleDeg === 0 ? ' (수평)' : simState.angleDeg === 90 ? ' (수직)' : '');
        lblTurns.textContent = simState.rxTurns + '바퀴';
        lblOffset.textContent = (simState.offsetCm > 0 ? '+' : '') + simState.offsetCm.toFixed(1) + ' cm';

        const phys = computeSimPhysics();
        metricDist.textContent = simState.distanceCm.toFixed(1) + 'cm / ' + simState.angleDeg + '°';
        metricVolt.textContent = phys.inducedVoltage.toFixed(2) + ' V ' + (phys.inducedVoltage >= 1.1 ? '(점등)' : '(부족)');

        if (phys.ledBrightness >= 55) {
          metricLed.textContent = '밝게 켜짐 (' + phys.ledBrightness + '%)';
          metricLed.className = 'text-sm sm:text-base font-extrabold text-rose-600 mt-0.5 truncate';
        } else if (phys.ledBrightness > 0) {
          metricLed.textContent = '희미하게 켜짐 (' + phys.ledBrightness + '%)';
          metricLed.className = 'text-sm sm:text-base font-extrabold text-amber-600 mt-0.5 truncate';
        } else {
          metricLed.textContent = '꺼짐 (0%)';
          metricLed.className = 'text-sm sm:text-base font-extrabold text-slate-400 mt-0.5 truncate';
        }

        if (!simState.powerOn) {
          metricStatus.textContent = '전원 꺼짐';
          metricStatus.className = 'text-sm sm:text-base font-extrabold text-slate-400 mt-0.5 truncate';
        } else if (simState.faultMode === 'normal') {
          metricStatus.textContent = phys.couplingPct + '% 전달 중';
          metricStatus.className = 'text-sm sm:text-base font-extrabold text-emerald-600 mt-0.5 truncate';
        } else if (simState.faultMode === 'phase') {
          metricStatus.textContent = '코일 선 바뀜 (정지)';
          metricStatus.className = 'text-sm sm:text-base font-extrabold text-rose-600 mt-0.5 truncate';
        } else if (simState.faultMode === 'enamel') {
          metricStatus.textContent = '코팅 안 벗김 (차단)';
          metricStatus.className = 'text-sm sm:text-base font-extrabold text-rose-600 mt-0.5 truncate';
        } else if (simState.faultMode === 'tr_rev') {
          metricStatus.textContent = '부품 역방향 (과열)';
          metricStatus.className = 'text-sm sm:text-base font-extrabold text-rose-600 mt-0.5 truncate';
        }

        liveEasyGuide.innerHTML = buildEasyGuideText(phys);
      }

      btnPowerToggle.addEventListener('click', function () {
        simState.powerOn = !simState.powerOn;
        if (simState.powerOn) {
          btnPowerToggle.className = 'px-3 py-1.5 rounded-lg text-xs font-bold bg-emerald-600 hover:bg-emerald-700 text-white transition flex items-center gap-1.5 shadow-sm';
          powerDot.className = 'w-2 h-2 rounded-full bg-white animate-pulse';
          powerLabel.textContent = '건전지 전원 켜짐';
          playTone(680, 100, 'triangle');
        } else {
          btnPowerToggle.className = 'px-3 py-1.5 rounded-lg text-xs font-bold bg-slate-600 hover:bg-slate-700 text-white transition flex items-center gap-1.5';
          powerDot.className = 'w-2 h-2 rounded-full bg-slate-300';
          powerLabel.textContent = '건전지 전원 꺼짐';
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
        btnPowerToggle.className = 'px-3 py-1.5 rounded-lg text-xs font-bold bg-emerald-600 hover:bg-emerald-700 text-white transition flex items-center gap-1.5 shadow-sm';
        powerDot.className = 'w-2 h-2 rounded-full bg-white animate-pulse';
        powerLabel.textContent = '건전지 전원 켜짐';
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
        normal: '<strong class="text-blue-700">[정상 작동 중]</strong> 건전지 전기가 트랜지스터를 거쳐 아래쪽 코일에서 자기장 물결을 만들고, 위쪽 코일이 그 힘을 받아 LED에 불을 켭니다.',
        enamel: '<strong class="text-rose-700">[실수 1위: 에나멜 코팅을 안 벗김]</strong> 구리선 겉면에는 투명·갈색 절연 코팅이 칠해져 있어요. <strong>선 끝 1.5cm를 사포나 칼등으로 노란 속살이 보일 때까지 빙 돌려 긁어내야</strong> 전기가 통합니다!',
        phase: '<strong class="text-rose-700">[실수 2위: 코일 ①번·③번 선이 바뀜]</strong> 아래쪽 코일의 양 끝선 방향이 반대로 꽂히면 트랜지스터 스위칭이 멈춰요. <strong>브레드보드 6번 줄과 7번 줄에 꽂힌 코일 선 두 개를 서로 맞바꿔 꽂으면</strong> 바로 켜집니다!',
        tr_rev: '<strong class="text-rose-700">[실수 3위: 트랜지스터를 거꾸로 꽂음]</strong> 검은색 부품(2N2222)의 왼쪽·오른쪽 다리가 바뀌면 작동하지 않고 부품이 뜨거워져요. <strong>납작한 글씨 면이 정면(내 쪽)을 보도록</strong> 방향을 맞춰 다시 꽂으세요.'
      };

      const faultBtns = document.querySelectorAll('.fault-mode-btn');
      function syncFaultButtons() {
        faultBtns.forEach(function (btn) {
          const fm = btn.getAttribute('data-fault');
          if (fm === simState.faultMode) {
            btn.className = 'fault-mode-btn py-2 px-2.5 rounded-xl text-xs border text-left transition truncate ' +
              (fm === 'normal' ? 'mode-active-normal' : 'mode-active-fault');
          } else {
            btn.className = 'fault-mode-btn py-2 px-2.5 rounded-xl text-xs border border-slate-200 bg-slate-50 hover:bg-slate-100 text-slate-700 text-left transition truncate';
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

      // --- 마우스 및 터치로 위쪽 코일 직접 드래그 ---
      function handleCanvasDrag(clientX, clientY) {
        if (!simState.isDragging) return;
        const rect = simMainCanvas.getBoundingClientRect();
        const x = clientX - rect.left;
        const y = clientY - rect.top;
        const cx = rect.width / 2;
        const txY = rect.height - 96;
        const maxTravelPx = Math.max(90, txY - 58);

        const clampedPxGap = Math.max(26, Math.min(maxTravelPx, txY - y));
        const newDist = 0.5 + ((clampedPxGap - 26) / Math.max(1, maxTravelPx - 26)) * 6.5;
        simState.distanceCm = Math.round(newDist * 10) / 10;

        const maxHorizPx = Math.max(65, rect.width * 0.32);
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

      // --- 메인 실험 캔버스 그리기 (부품 모양을 직관적으로 표현) ---
      function drawMainSimulator() {
        const rect = simMainCanvas.parentElement.getBoundingClientRect();
        const dpr = window.devicePixelRatio || 1;
        const w = Math.max(260, rect.width);
        const h = Math.max(250, rect.height);
        if (simMainCanvas.width !== Math.floor(w * dpr) || simMainCanvas.height !== Math.floor(h * dpr)) {
          simMainCanvas.width = Math.floor(w * dpr);
          simMainCanvas.height = Math.floor(h * dpr);
        }
        simMainCtx.save();
        simMainCtx.scale(dpr, dpr);

        // 1. 밝고 깔끔한 실험실 배경 및 모눈 그리드
        const bgGrad = simMainCtx.createLinearGradient(0, 0, 0, h);
        bgGrad.addColorStop(0, '#f8fafc');
        bgGrad.addColorStop(1, '#eef2f6');
        simMainCtx.fillStyle = bgGrad;
        simMainCtx.fillRect(0, 0, w, h);

        simMainCtx.strokeStyle = 'rgba(148, 163, 184, 0.16)';
        simMainCtx.lineWidth = 1;
        for (let gx = 0; gx < w; gx += 24) {
          simMainCtx.beginPath(); simMainCtx.moveTo(gx, 0); simMainCtx.lineTo(gx, h); simMainCtx.stroke();
        }
        for (let gy = 0; gy < h; gy += 24) {
          simMainCtx.beginPath(); simMainCtx.moveTo(0, gy); simMainCtx.lineTo(w, gy); simMainCtx.stroke();
        }

        const cx = w / 2;
        const txY = h - 94;
        const maxTravelPx = Math.max(85, txY - 56);
        const pixelGap = 26 + ((simState.distanceCm - 0.5) / 6.5) * (maxTravelPx - 26);
        const maxHorizPx = Math.max(65, w * 0.32);
        const rxX = cx + (simState.offsetCm / 4.0) * maxHorizPx;
        const rxY = txY - pixelGap;

        const phys = computeSimPhysics();
        const coilHalfW = Math.min(82, w * 0.23);

        // 2. 보이지 않는 교류 자기장 물결 표현 (송신 코일 -> 수신 코일)
        if (phys.oscillating) {
          const pulse = (Math.sin(simState.phaseTick) + 1) * 0.5;
          const baseAlpha = 0.22 + (phys.couplingPct / 100) * 0.45;

          // 퍼져 올라가는 자기장 고리(물결) 3개
          for (let r = 0; r < 3; r++) {
            const prog = ((simState.phaseTick * 0.08 + r / 3) % 1);
            const ringY = txY - 12 - prog * (pixelGap + 15);
            const ringAlpha = (1 - prog) * (0.35 + (phys.couplingPct / 100) * 0.35);
            simMainCtx.strokeStyle = 'rgba(37, 99, 235, ' + ringAlpha.toFixed(2) + ')';
            simMainCtx.lineWidth = 2;
            simMainCtx.beginPath();
            simMainCtx.ellipse(cx, ringY, coilHalfW * (0.85 + prog * 0.35), 9 + prog * 5, 0, 0, Math.PI * 2);
            simMainCtx.stroke();
          }

          // 자기력선 점선 곡선
          simMainCtx.strokeStyle = 'rgba(37, 99, 235, ' + Math.min(0.75, baseAlpha + pulse * 0.15).toFixed(2) + ')';
          simMainCtx.lineWidth = 2;
          simMainCtx.setLineDash([6, 5]);
          simMainCtx.lineDashOffset = -simState.phaseTick * 5;

          [-0.72, -0.36, 0, 0.36, 0.72].forEach(function (ratio) {
            const ox = coilHalfW * ratio;
            simMainCtx.beginPath();
            simMainCtx.moveTo(cx + ox, txY - 8);
            simMainCtx.quadraticCurveTo(
              cx + ox * 1.4,
              (txY + rxY) / 2,
              rxX + ox * Math.cos((simState.angleDeg * Math.PI) / 180),
              rxY + 6
            );
            simMainCtx.stroke();
          });
          simMainCtx.setLineDash([]);

          // 자기장 설명 라벨 (캔버스 중앙)
          if (pixelGap > 48) {
            const midY = (txY + rxY) / 2;
            simMainCtx.fillStyle = 'rgba(239, 246, 255, 0.92)';
            simMainCtx.strokeStyle = '#93c5fd';
            simMainCtx.lineWidth = 1;
            simMainCtx.beginPath();
            simMainCtx.roundRect(cx - 88, midY - 11, 176, 22, 11);
            simMainCtx.fill();
            simMainCtx.stroke();

            simMainCtx.fillStyle = '#1d4ed8';
            simMainCtx.font = '600 11px "Noto Sans KR", sans-serif';
            simMainCtx.textAlign = 'center';
            simMainCtx.fillText('↑ 보이지 않는 자기장 물결 전달 ↑', cx, midY + 4);
          }
        }

        // 3. 하단 송신부 바닥판: AA 건전지 + 미니 브레드보드(트랜지스터·저항) + 아래쪽 코일
        const boardY = txY + 24;
        const boardW = Math.min(350, w - 24);
        const boardX = cx - boardW / 2;

        // 바닥 받침 카드
        simMainCtx.fillStyle = '#ffffff';
        simMainCtx.strokeStyle = '#cbd5e1';
        simMainCtx.lineWidth = 1.5;
        simMainCtx.beginPath();
        simMainCtx.roundRect(boardX, boardY, boardW, 60, 12);
        simMainCtx.fill();
        simMainCtx.stroke();

        // (A) 왼쪽: AA 1.5V 건전지 그림
        const batX = boardX + 12;
        const batY = boardY + 13;
        simMainCtx.fillStyle = simState.powerOn ? '#f59e0b' : '#94a3b8';
        simMainCtx.fillRect(batX, batY, 26, 20);
        simMainCtx.fillStyle = '#1e293b';
        simMainCtx.fillRect(batX + 26, batY, 32, 20);
        // 건전지 + 극 볼록 단자
        simMainCtx.fillStyle = '#dc2626';
        simMainCtx.fillRect(batX - 3, batY + 6, 3, 8);
        simMainCtx.fillStyle = '#ffffff';
        simMainCtx.font = 'bold 9px "Inter", sans-serif';
        simMainCtx.textAlign = 'center';
        simMainCtx.fillText('AA 1.5V', batX + 29, batY + 13);
        simMainCtx.fillStyle = '#475569';
        simMainCtx.font = '600 10px "Noto Sans KR", sans-serif';
        simMainCtx.fillText(simState.powerOn ? '1. 건전지(켜짐)' : '1. 건전지(꺼짐)', batX + 28, boardY + 49);

        // (B) 가운데: 미니 브레드보드 + 검은색 트랜지스터(2N2222) + 저항
        const bbX = cx - 44;
        const bbY = boardY + 10;
        simMainCtx.fillStyle = '#f8fafc';
        simMainCtx.strokeStyle = '#94a3b8';
        simMainCtx.lineWidth = 1.2;
        simMainCtx.beginPath();
        simMainCtx.roundRect(bbX, bbY, 88, 25, 5);
        simMainCtx.fill();
        simMainCtx.stroke();

        // 트랜지스터(검은색 반달 모양 + 다리 3개)
        const trX = cx - 16;
        const trY = bbY + 12;
        simMainCtx.strokeStyle = '#64748b';
        simMainCtx.lineWidth = 1.5;
        [-6, 0, 6].forEach(function (dx) {
          simMainCtx.beginPath();
          simMainCtx.moveTo(trX + dx, trY + 4);
          simMainCtx.lineTo(trX + dx, trY + 10);
          simMainCtx.stroke();
        });
        simMainCtx.fillStyle = simState.faultMode === 'tr_rev' ? '#e11d48' : '#0f172a';
        simMainCtx.beginPath();
        simMainCtx.arc(trX, trY, 8, Math.PI, 0, false);
        simMainCtx.closePath();
        simMainCtx.fill();

        // 1kΩ 저항 (베이지색 막대)
        simMainCtx.fillStyle = '#fde68a';
        simMainCtx.strokeStyle = '#d97706';
        simMainCtx.lineWidth = 1;
        simMainCtx.fillRect(cx + 8, bbY + 9, 22, 7);
        simMainCtx.strokeRect(cx + 8, bbY + 9, 22, 7);

        simMainCtx.fillStyle = '#475569';
        simMainCtx.font = '600 10px "Noto Sans KR", sans-serif';
        simMainCtx.textAlign = 'center';
        simMainCtx.fillText('2. 트랜지스터·저항(브레드보드)', cx, boardY + 49);

        // (C) 오른쪽 안내: 코일 연결선 표시
        const wireInfoX = boardX + boardW - 52;
        simMainCtx.fillStyle = '#fff7ed';
        simMainCtx.strokeStyle = '#fdba74';
        simMainCtx.beginPath();
        simMainCtx.roundRect(wireInfoX - 38, boardY + 10, 76, 25, 5);
        simMainCtx.fill();
        simMainCtx.stroke();
        simMainCtx.fillStyle = '#c2410c';
        simMainCtx.font = 'bold 10px "Noto Sans KR", sans-serif';
        simMainCtx.fillText('①·②탭·③ 연결', wireInfoX, boardY + 26);
        simMainCtx.fillStyle = '#475569';
        simMainCtx.font = '600 10px "Noto Sans KR", sans-serif';
        simMainCtx.fillText('3. 코일 가닥 연결', wireInfoX, boardY + 49);

        // 브레드보드에서 아래쪽 송신 코일로 올라가는 3가닥 구리선 (①시작, ②중간탭, ③끝)
        const wireColors = ['#ea580c', '#d97706', '#ea580c'];
        [-22, 0, 22].forEach(function (dx, idx) {
          simMainCtx.strokeStyle = simState.faultMode === 'enamel' ? '#78350f' : wireColors[idx];
          simMainCtx.lineWidth = 2.2;
          simMainCtx.beginPath();
          simMainCtx.moveTo(cx + dx, boardY + 10);
          simMainCtx.lineTo(cx + dx * 1.4, txY + 8);
          simMainCtx.stroke();
        });

        // 4. 아래쪽 1차 보내는 코일 (구리색 입체 고리 여러 겹)
        for (let i = -3; i <= 3; i++) {
          simMainCtx.strokeStyle = i % 2 === 0 ? '#c2410c' : '#ea580c';
          simMainCtx.lineWidth = 2.6;
          simMainCtx.beginPath();
          simMainCtx.ellipse(cx, txY + i * 1.8, coilHalfW, 13, 0, 0, Math.PI * 2);
          simMainCtx.stroke();
        }
        // 아래쪽 코일 이름표
        simMainCtx.fillStyle = '#fff7ed';
        simMainCtx.strokeStyle = '#ea580c';
        simMainCtx.lineWidth = 1.5;
        simMainCtx.beginPath();
        simMainCtx.roundRect(cx - 86, txY - 10, 172, 20, 10);
        simMainCtx.fill();
        simMainCtx.stroke();
        simMainCtx.fillStyle = '#9a3412';
        simMainCtx.font = 'bold 11px "Noto Sans KR", sans-serif';
        simMainCtx.textAlign = 'center';
        simMainCtx.fillText('아래쪽: 보내는 코일 (30바퀴)', cx, txY + 4);

        // 실수/고장 모드일 때 시각적 경고 말풍선 표시
        if (simState.faultMode !== 'normal' && simState.powerOn) {
          let faultBanner = '';
          if (simState.faultMode === 'enamel') faultBanner = '코일 끝 갈색 코팅을 안 벗겨서 전기가 막혔어요!';
          if (simState.faultMode === 'phase') faultBanner = '①번 선과 ③번 선이 반대로 꽂혀 진동이 멈췄어요!';
          if (simState.faultMode === 'tr_rev') faultBanner = '트랜지스터가 거꾸로 꽂혀서 뜨거워지고 있어요!';

          simMainCtx.fillStyle = '#fff1f2';
          simMainCtx.strokeStyle = '#e11d48';
          simMainCtx.lineWidth = 1.5;
          simMainCtx.beginPath();
          simMainCtx.roundRect(cx - 135, txY - 40, 270, 24, 8);
          simMainCtx.fill();
          simMainCtx.stroke();
          simMainCtx.fillStyle = '#be123c';
          simMainCtx.font = 'bold 11px "Noto Sans KR", sans-serif';
          simMainCtx.fillText(faultBanner, cx, txY - 24);
        }

        // 5. 위쪽 2차 받는 코일 + 실제 모양 빨간색 LED 전구 (드래그 가능)
        simMainCtx.save();
        simMainCtx.translate(rxX, rxY);
        simMainCtx.rotate((simState.angleDeg * Math.PI) / 180);

        // 드래그 가이드 손잡이 후광
        if (simState.isDragging) {
          simMainCtx.fillStyle = 'rgba(37, 99, 235, 0.08)';
          simMainCtx.beginPath();
          simMainCtx.ellipse(0, 0, coilHalfW + 16, 26, 0, 0, Math.PI * 2);
          simMainCtx.fill();
        }

        // 받는 코일 고리들 (구리선 느낌)
        const rxLoops = Math.max(3, Math.round(simState.rxTurns / 10));
        for (let j = -rxLoops + 1; j <= rxLoops - 1; j++) {
          simMainCtx.strokeStyle = j % 2 === 0 ? '#b45309' : '#d97706';
          simMainCtx.lineWidth = 2.5;
          simMainCtx.beginPath();
          simMainCtx.ellipse(0, j * 1.7, coilHalfW * 0.96, 12, 0, 0, Math.PI * 2);
          simMainCtx.stroke();
        }

        // 위쪽 코일 라벨 배지
        simMainCtx.fillStyle = '#ecfdf5';
        simMainCtx.strokeStyle = '#059669';
        simMainCtx.lineWidth = 1.5;
        simMainCtx.beginPath();
        simMainCtx.roundRect(-84, -10, 168, 20, 10);
        simMainCtx.fill();
        simMainCtx.stroke();

        simMainCtx.fillStyle = '#065f46';
        simMainCtx.font = 'bold 11px "Noto Sans KR", sans-serif';
        simMainCtx.textAlign = 'center';
        simMainCtx.fillText('위쪽: 받는 코일 (' + simState.rxTurns + '바퀴)', 0, 4);

        // 드래그 안내 캡션 (코일 바로 위)
        simMainCtx.fillStyle = '#1e293b';
        simMainCtx.font = '600 10px "Noto Sans KR", sans-serif';
        simMainCtx.fillText('↕ 여기를 잡고 움직여 보세요', 0, -18);

        // 코일 오른쪽에 연결된 빨간색 5mm LED 전구 (돔 형태)
        const ledX = coilHalfW * 0.96 + 26;
        const ledY = 0;

        // 코일에서 LED 다리로 이어지는 두 가닥 선
        simMainCtx.strokeStyle = '#64748b';
        simMainCtx.lineWidth = 2;
        simMainCtx.beginPath();
        simMainCtx.moveTo(coilHalfW * 0.92, -4);
        simMainCtx.lineTo(ledX - 4, ledY + 6);
        simMainCtx.moveTo(coilHalfW * 0.92, 4);
        simMainCtx.lineTo(ledX + 4, ledY + 6);
        simMainCtx.stroke();

        // LED가 켜졌을 때 퍼지는 빛 후광 & 빛줄기
        if (phys.ledBrightness > 0) {
          const glowR = 18 + (phys.ledBrightness / 100) * 32;
          const grad = simMainCtx.createRadialGradient(ledX, ledY - 6, 2, ledX, ledY - 6, glowR);
          grad.addColorStop(0, 'rgba(255, 40, 60, ' + (0.55 + phys.ledBrightness / 200) + ')');
          grad.addColorStop(0.5, 'rgba(244, 63, 94, ' + (phys.ledBrightness / 220) + ')');
          grad.addColorStop(1, 'rgba(244, 63, 94, 0)');
          simMainCtx.fillStyle = grad;
          simMainCtx.beginPath();
          simMainCtx.arc(ledX, ledY - 6, glowR, 0, Math.PI * 2);
          simMainCtx.fill();
        }

        // LED 돔 몸체 그리기
        simMainCtx.fillStyle = phys.ledBrightness > 0 ? '#ef4444' : '#cbd5e1';
        simMainCtx.strokeStyle = phys.ledBrightness > 0 ? '#b91c1c' : '#64748b';
        simMainCtx.lineWidth = 1.8;
        simMainCtx.beginPath();
        simMainCtx.arc(ledX, ledY - 6, 9, Math.PI, 0, false);
        simMainCtx.lineTo(ledX + 9, ledY + 4);
        simMainCtx.lineTo(ledX - 9, ledY + 4);
        simMainCtx.closePath();
        simMainCtx.fill();
        simMainCtx.stroke();

        // LED 하이라이트 반사광
        simMainCtx.fillStyle = 'rgba(255, 255, 255, 0.65)';
        simMainCtx.beginPath();
        simMainCtx.arc(ledX - 3, ledY - 9, 2.5, 0, Math.PI * 2);
        simMainCtx.fill();

        // LED 상태 텍스트 라벨
        simMainCtx.fillStyle = phys.ledBrightness > 0 ? '#be123c' : '#475569';
        simMainCtx.font = 'bold 10px "Noto Sans KR", sans-serif';
        simMainCtx.fillText(phys.ledBrightness > 0 ? 'LED 켜짐!' : 'LED 꺼짐', ledX, ledY + 18);

        simMainCtx.restore();

        // 6. 왼쪽 눈금자 (두 코일 사이 실제 거리 표시)
        const rulerX = 28;
        simMainCtx.strokeStyle = '#2563eb';
        simMainCtx.lineWidth = 1.8;
        simMainCtx.beginPath();
        simMainCtx.moveTo(rulerX, rxY);
        simMainCtx.lineTo(rulerX, txY);
        simMainCtx.moveTo(rulerX - 6, rxY);
        simMainCtx.lineTo(rulerX + 6, rxY);
        simMainCtx.moveTo(rulerX - 6, txY);
        simMainCtx.lineTo(rulerX + 6, txY);
        simMainCtx.stroke();

        const midRulerY = (rxY + txY) / 2;
        simMainCtx.fillStyle = '#eff6ff';
        simMainCtx.strokeStyle = '#93c5fd';
        simMainCtx.lineWidth = 1;
        simMainCtx.beginPath();
        simMainCtx.roundRect(rulerX + 6, midRulerY - 11, 64, 22, 6);
        simMainCtx.fill();
        simMainCtx.stroke();

        simMainCtx.fillStyle = '#1d4ed8';
        simMainCtx.font = 'bold 11px "Noto Sans KR", sans-serif';
        simMainCtx.textAlign = 'center';
        simMainCtx.fillText('간격 ' + simState.distanceCm.toFixed(1) + 'cm', rulerX + 38, midRulerY + 4);

        simMainCtx.restore();
      }

      // --- 오른쪽 전기 물결 그래프(오실로스코프) 그리기 ---
      function drawWaveCanvas() {
        const rect = simWaveCanvas.parentElement.getBoundingClientRect();
        const dpr = window.devicePixelRatio || 1;
        const w = Math.max(200, rect.width);
        const h = Math.max(56, rect.height);
        if (simWaveCanvas.width !== Math.floor(w * dpr) || simWaveCanvas.height !== Math.floor(h * dpr)) {
          simWaveCanvas.width = Math.floor(w * dpr);
          simWaveCanvas.height = Math.floor(h * dpr);
        }
        simWaveCtx.save();
        simWaveCtx.scale(dpr, dpr);
        simWaveCtx.clearRect(0, 0, w, h);

        const midY = h / 2;
        simWaveCtx.strokeStyle = 'rgba(148, 163, 184, 0.25)';
        simWaveCtx.lineWidth = 1;
        simWaveCtx.beginPath();
        simWaveCtx.moveTo(0, midY);
        simWaveCtx.lineTo(w, midY);
        simWaveCtx.stroke();

        const phys = computeSimPhysics();

        // 아래 코일이 보내는 자기장 물결 (주황색)
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

        // 위 코일에 만들어지는 유도 전기 물결 (분홍/빨간색)
        simWaveCtx.strokeStyle = '#fb7185';
        simWaveCtx.lineWidth = 2.4;
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
```
