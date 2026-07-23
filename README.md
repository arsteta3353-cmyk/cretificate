[pudufu-certificate-generator-v22.html](https://github.com/user-attachments/files/30288534/pudufu-certificate-generator-v22.html)
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>프드프 수료증 생성기</title>
<link rel="stylesheet" as="style" crossorigin href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css" />
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
<style>
  :root{
    --bg:#E9E7E1; --panel:#FBFAF7; --ink:#1A1C24; --sub:#71747E;
    --line:#E2DFD8; --ui:#2F6BE0;
  }
  *{box-sizing:border-box;margin:0;padding:0}
  body{
    font-family:"Pretendard Variable",Pretendard,-apple-system,system-ui,sans-serif;
    background:var(--bg);color:var(--ink);word-break:keep-all;
    -webkit-font-smoothing:antialiased;letter-spacing:-.01em;
  }
  .app{display:grid;grid-template-columns:392px 1fr;min-height:100vh}

  /* ---------- panel ---------- */
  .panel{background:var(--panel);border-right:1px solid var(--line);padding:26px 24px 48px;overflow-y:auto;max-height:100vh}
  .brand{display:flex;align-items:center;gap:9px;margin-bottom:5px}
  .brand svg{height:19px;width:auto;color:var(--ink)}
  .brand span{font-size:12px;font-weight:600;letter-spacing:.16em;color:var(--sub)}
  .lede{font-size:12.5px;color:var(--sub);line-height:1.65;margin-bottom:20px}

  .tabs{display:grid;grid-template-columns:1fr 1fr;gap:6px;background:#F0EEE9;padding:4px;border-radius:10px;margin-bottom:22px}
  .tab{padding:9px;border:none;background:transparent;border-radius:7px;font-family:inherit;
    font-size:13.5px;font-weight:700;color:var(--sub);cursor:pointer;letter-spacing:-.02em}
  .tab.on{background:#fff;color:var(--ink);box-shadow:0 1px 3px rgba(0,0,0,.07)}

  .grp{margin-bottom:22px}
  .grp h3{font-size:11px;font-weight:700;letter-spacing:.12em;color:var(--sub);
    margin-bottom:12px;display:flex;align-items:center;gap:8px}
  .grp h3::after{content:"";flex:1;height:1px;background:var(--line)}
  label.f{display:block;margin-bottom:12px}
  label.f>span{display:block;font-size:12.5px;font-weight:600;margin-bottom:5px;color:#3a3d47}
  label.f>span em{font-style:normal;font-weight:400;color:var(--sub)}
  input[type=text],input[type=number],input[type=date],textarea{
    width:100%;padding:9px 11px;border:1px solid var(--line);border-radius:9px;
    font-size:13.5px;font-family:inherit;color:var(--ink);background:#fff;
    letter-spacing:-.02em;transition:border-color .14s,box-shadow .14s;
  }
  textarea{resize:vertical;min-height:140px;line-height:1.7}
  input:focus,textarea:focus{outline:none;border-color:var(--ui);box-shadow:0 0 0 3px rgba(47,107,224,.14)}
  .r2{display:grid;grid-template-columns:1fr 1fr;gap:9px}

  .presets{display:grid;grid-template-columns:1fr 1fr;gap:7px}
  .pbtn{border:1.5px solid var(--line);background:#fff;border-radius:9px;padding:9px 8px;
    cursor:pointer;font-family:inherit;text-align:left;transition:border-color .14s,background .14s}
  .pbtn:hover{border-color:#C9C4B9}
  .pbtn.on{border-color:var(--ui);background:rgba(47,107,224,.06)}
  .pbtn b{display:block;font-size:12.5px;font-weight:700;letter-spacing:-.02em}
  .pbtn small{font-size:10.5px;color:var(--sub)}

  .segs{display:grid;grid-template-columns:1fr 1fr;gap:7px;margin-bottom:12px}
  .seg{border:1.5px solid var(--line);background:#fff;border-radius:9px;padding:9px;
    cursor:pointer;font-family:inherit;font-size:12.5px;font-weight:600;color:#3a3d47;letter-spacing:-.02em}
  .seg.on{border-color:var(--ui);background:rgba(47,107,224,.06);color:var(--ink)}

  .colors{display:flex;align-items:center;gap:10px;margin-top:4px}
  .colors input[type=color]{width:42px;height:34px;border:1px solid var(--line);border-radius:8px;background:#fff;cursor:pointer;padding:3px}
  .sw{display:flex;gap:6px}
  .sw button{width:23px;height:23px;border-radius:50%;cursor:pointer;border:2px solid #fff;box-shadow:0 0 0 1px var(--line)}

  .btn{display:flex;align-items:center;justify-content:center;gap:7px;width:100%;
    padding:12px;border-radius:10px;font-size:13.5px;font-weight:700;font-family:inherit;
    cursor:pointer;border:none;letter-spacing:-.02em;transition:filter .14s}
  .btn+.btn{margin-top:8px}
  .btn:disabled{opacity:.5;cursor:default}
  .b1{background:var(--ui);color:#fff}
  .b1:hover:not(:disabled){filter:brightness(1.07)}
  .b2{background:#fff;border:1px solid var(--line);color:var(--ink)}
  .b2:hover:not(:disabled){border-color:#C9C4B9}
  .hint{font-size:11.5px;color:var(--sub);line-height:1.6;margin-top:9px}
  .warn{font-size:12px;color:#B3261E;background:#FDEDEC;border:1px solid #F5C9C5;
    padding:9px 11px;border-radius:9px;margin-top:10px;line-height:1.6;display:none}
  .prog{font-size:12.5px;color:var(--sub);margin-top:10px;display:none}
  .prog .bar{height:5px;background:#E5E2DB;border-radius:3px;margin-top:6px;overflow:hidden}
  .prog .bar i{display:block;height:100%;width:0;background:var(--ui);border-radius:3px;transition:width .2s}
  .count{font-size:11.5px;color:var(--sub);margin-top:6px}

  /* ---------- stage ---------- */
  .stage{display:flex;flex-direction:column;align-items:center;justify-content:center;padding:40px 24px;gap:15px}
  .snote{font-size:12px;color:var(--sub)}

  /* ============ CERTIFICATE ============ */
  #cert{
    --accent:#1F7A4D; --paper:#FCFBF9; --cink:#1A1C24; --csub:#9497A1;
    width:452px;height:639px;background:var(--paper);position:relative;padding:0 46px;
    display:flex;flex-direction:column;align-items:center;
    box-shadow:0 22px 55px -24px rgba(20,22,35,.42),0 2px 10px -4px rgba(20,22,35,.12);
    color:var(--cink);font-feature-settings:"tnum";
  }
  .fr1{position:absolute;inset:16px;border:1px solid #E6E2D8;pointer-events:none}
  .fr2{position:absolute;inset:21px;border:1px solid var(--accent);opacity:.26;pointer-events:none}

  .medal{margin-top:30px;width:98px;height:131px;position:relative;z-index:3}
  .medal svg{width:100%;height:100%;display:block}
  .medal .num{position:absolute;top:45.8px;left:0;width:98px;text-align:center;
    transform:translateY(-50%);font-size:21px;font-weight:700;color:#EBCE7C;letter-spacing:-.02em}

  /* 자간 완화 : 제목 -.02 / 본문 0 / 정보 0 */
  #cTitle{font-weight:800;font-size:24.5px;line-height:1.46;text-align:center;
    margin-top:16px;letter-spacing:-.02em;text-wrap:balance}
  #cSub{font-family:"Cormorant Garamond";font-size:13.5px;font-weight:500;
    letter-spacing:.15em;color:var(--csub);margin-top:11px}
  .div{width:34px;height:2px;background:var(--accent);margin-top:19px;opacity:.85}

  .info{margin-top:26px;width:100%;max-width:330px;display:flex;flex-direction:column;gap:14px}
  .info .row{display:flex;align-items:baseline;font-size:13.5px;line-height:1.62}
  .info .k{width:52px;flex:none;color:var(--csub);font-weight:600;font-size:12.5px;
    text-align:justify;text-align-last:justify;position:relative;padding-left:13px}
  .info .k::before{content:"";position:absolute;left:0;top:.52em;width:4.5px;height:4.5px;
    border-radius:50%;background:var(--accent)}
  .info .sep{margin:0 11px 0 9px;color:#D8D4CA}
  .info .v{color:#33353F;font-weight:500;letter-spacing:0}
  .info .v b{font-weight:700;color:var(--cink)}

  #cBody{margin-top:30px;text-align:center;font-size:13.5px;line-height:2.1;
    color:#3D3F48;font-weight:400;max-width:312px;letter-spacing:0;text-wrap:balance}
  #cBody b{color:var(--cink);font-weight:700}

  .foot{margin-top:auto;padding-bottom:38px;display:flex;flex-direction:column;
    align-items:center;gap:22px;width:100%}
  #cDate{font-size:13.5px;font-weight:600;color:#33353F;letter-spacing:.01em}

  /* 두 하단 형태 모두 동일한 가로 락업 구조 */
  .lock{display:flex;align-items:center;justify-content:center;gap:13px}
  .plogo{height:23px;width:auto;display:block;color:var(--cink)}
  .lock .x{color:var(--accent);font-size:14px;font-weight:600;opacity:.9;margin-top:1px}
  .lock .vbar{width:1px;height:15px;background:var(--accent);opacity:.45}
  .lock .pt{font-weight:800;font-size:16.5px;letter-spacing:-.02em}

  .sign{display:flex;flex-direction:column;align-items:center;gap:4px}
  .sign .sigimg{width:112px;height:auto;display:block;opacity:.92;margin-bottom:-2px}
  .sign .sc{color:var(--accent);opacity:.55;font-family:"Cormorant Garamond";
    font-style:italic;font-size:18px;letter-spacing:.02em}

  @media (max-width:980px){
    .app{grid-template-columns:1fr}
    .panel{max-height:none;border-right:none;border-bottom:1px solid var(--line)}
  }
</style>
</head>
<body>
<div class="app">
  <!-- ================= PANEL ================= -->
  <aside class="panel">
    <div class="brand">
      <svg viewBox="0 0 130 80" fill="currentColor" aria-label="프드프">
        <path d="M100.86,5.24H48v12h3.32V8.57h47.2v22.9h23.16v39.97H51.32v-8.68H48v12h77V29.11L100.86,5.24z M101.84,28.14V10.88l17.46,17.26H101.84z"/>
        <polygon points="54.06,52.39 51.32,52.39 48,52.39 44.89,52.39 5,52.39 5,56.35 44.89,56.35 48,56.35 51.32,56.35 54.06,56.35 93.95,56.35 93.95,52.39"/>
        <path d="M33.42,41.58h-3.36V29.75h3.36v-4.24H7.37v4.24h3.66v11.83H7.37v4.2h26.05V41.58z M24.96,41.58h-8.83V29.75h8.83V41.58z"/>
        <path d="M91.58,41.58h-3.36V29.75h3.36v-4.24H65.53v4.24h3.66v11.83h-3.66v4.2h26.05V41.58z M83.12,41.58h-8.83V29.75h8.83V41.58z"/>
        <polygon points="48,45.79 51.32,45.79 54.06,45.79 60.36,45.79 60.36,41.61 54.06,41.61 51.32,41.61 48,41.61 44.89,41.61 43.19,41.61 43.19,29.33 44.89,29.33 48,29.33 51.32,29.33 54.06,29.33 60.36,29.33 60.36,25.51 54.06,25.51 51.32,25.51 48,25.51 44.89,25.51 38.58,25.51 38.58,45.79 44.89,45.79"/>
      </svg>
      <span>CERTIFICATE</span>
    </div>
    <p class="lede">과정 정보를 한 번 설정하고, 기수마다 명단만 바꿔 발급하세요.</p>

    <div class="tabs">
      <button class="tab on" data-mode="one">개별 발급</button>
      <button class="tab" data-mode="bulk">명단 일괄 발급</button>
    </div>

    <div class="grp">
      <h3>강의 프리셋</h3>
      <div class="presets" id="presets"></div>
    </div>

    <div class="grp">
      <h3>과정 정보</h3>
      <label class="f"><span>제목 1행</span><input type="text" id="t1" value="네이버 홈판"></label>
      <label class="f"><span>제목 2행</span><input type="text" id="t2" value="블로그 챌린지 수료증"></label>
      <label class="f"><span>영문 제목 <em>· 기수 서수는 자동</em></span><input type="text" id="ten" value="Naver Blog Challenge"></label>
      <label class="f"><span>과정명 <em>· 본문·정보란에 표기</em></span><input type="text" id="course" value="네이버 홈판 수익화 챌린지"></label>
      <div class="r2">
        <label class="f"><span>기수</span><input type="number" id="gi" min="1" value="1"></label>
        <label class="f"><span>이름 라벨</span><input type="text" id="klabel" value="닉네임"></label>
      </div>
      <label class="f"><span>주제 <em>· 선택</em></span><input type="text" id="topic" placeholder="예) 긍정적인 마음"></label>
    </div>

    <div class="grp">
      <h3>일정</h3>
      <div class="r2">
        <label class="f"><span>시작일</span><input type="date" id="d1"></label>
        <label class="f"><span>종료일</span><input type="date" id="d2"></label>
      </div>
      <label class="f"><span>수료일</span><input type="date" id="d3"></label>
    </div>

    <div class="grp">
      <h3>하단 표기</h3>
      <div class="segs">
        <button class="seg on" data-foot="partner">공동 브랜드</button>
        <button class="seg" data-foot="ceo">대표 서명</button>
      </div>
      <label class="f" id="partnerWrap"><span>파트너명</span><input type="text" id="partner" value="이서이"></label>
      <div class="colors">
        <input type="color" id="accent" value="#1F7A4D">
        <div class="sw" id="sw"></div>
      </div>
    </div>

    <div class="grp" id="oneBox">
      <h3>수료자</h3>
      <label class="f"><span>닉네임</span><input type="text" id="nick" value="필요쌤"></label>
      <button class="btn b1" id="bPng">PNG 저장</button>
      <button class="btn b2" id="bPdf">PDF 저장</button>
    </div>

    <div class="grp" id="bulkBox" style="display:none">
      <h3>명단</h3>
      <label class="f"><span>닉네임 목록 <em>· 한 줄에 한 명</em></span>
        <textarea id="list" placeholder="필요쌤&#10;소담이&#10;포도킹"></textarea></label>
      <div class="count" id="cnt">0명</div>
      <label class="f" style="margin-top:10px"><span>또는 CSV 업로드 <em>· 첫 번째 열을 닉네임으로 읽음</em></span>
        <input type="file" id="csv" accept=".csv,.txt" style="font-size:12px"></label>
      <button class="btn b1" id="bZip">전원 PNG 생성 → ZIP 다운로드</button>
      <button class="btn b2" id="bPdfAll">전원 통합 PDF 다운로드</button>
      <div class="prog" id="prog"><span id="progT"></span><div class="bar"><i id="progB"></i></div></div>
      <p class="hint">명단 인원수만큼 순서대로 생성됩니다. 인원이 많으면 시간이 걸리니 창을 닫지 마세요.</p>
    </div>

    <div class="warn" id="warn"></div>
  </aside>

  <!-- ================= STAGE ================= -->
  <main class="stage">
    <div id="cert">
      <div class="fr1"></div><div class="fr2"></div>

      <div class="medal">
        <svg viewBox="0 0 120 160" aria-hidden="true">
          <defs>
            <linearGradient id="gold" x1=".12" y1="0" x2=".88" y2="1">
              <stop offset="0" stop-color="#FAEFB4"/><stop offset=".26" stop-color="#DFBB55"/>
              <stop offset=".52" stop-color="#B8860B"/><stop offset=".76" stop-color="#E5C86E"/>
              <stop offset="1" stop-color="#C9A03A"/>
            </linearGradient>
          </defs>
          <path id="rb1" d="M44 60 L28 140 L38 132 L46 144 L60 64 Z" fill="#2E9160"/>
          <path id="rb2" d="M76 60 L92 140 L82 132 L74 144 L60 64 Z" fill="#17603C"/>
          <circle cx="60" cy="56" r="44" fill="url(#gold)"/>
          <circle cx="60" cy="56" r="44" fill="none" stroke="#9C7A1E" stroke-width="1"/>
          <circle id="mdisc" cx="60" cy="56" r="36" fill="#1B2A45"/>
          <circle cx="60" cy="56" r="31.5" fill="none" stroke="#D9B65A" stroke-width=".9" opacity=".85"/>
        </svg>
        <div class="num" id="cNum">1기</div>
      </div>

      <div id="cTitle"></div>
      <div id="cSub"></div>
      <div class="div"></div>

      <div class="info">
        <div class="row"><span class="k" id="cK">닉네임</span><span class="sep">:</span><span class="v"><b id="cNick"></b></span></div>
        <div class="row"><span class="k">과정</span><span class="sep">:</span><span class="v" id="cCourse"></span></div>
        <div class="row"><span class="k">기간</span><span class="sep">:</span><span class="v" id="cPeriod"></span></div>
      </div>

      <div id="cBody"></div>

      <div class="foot">
        <div id="cDate"></div>

        <!-- 공동 브랜드형 -->
        <div class="lock" id="footPartner">
          <svg class="plogo" viewBox="0 0 130 80" fill="currentColor" aria-label="프드프">
            <path d="M100.86,5.24H48v12h3.32V8.57h47.2v22.9h23.16v39.97H51.32v-8.68H48v12h77V29.11L100.86,5.24z M101.84,28.14V10.88l17.46,17.26H101.84z"/>
            <polygon points="54.06,52.39 51.32,52.39 48,52.39 44.89,52.39 5,52.39 5,56.35 44.89,56.35 48,56.35 51.32,56.35 54.06,56.35 93.95,56.35 93.95,52.39"/>
            <path d="M33.42,41.58h-3.36V29.75h3.36v-4.24H7.37v4.24h3.66v11.83H7.37v4.2h26.05V41.58z M24.96,41.58h-8.83V29.75h8.83V41.58z"/>
            <path d="M91.58,41.58h-3.36V29.75h3.36v-4.24H65.53v4.24h3.66v11.83h-3.66v4.2h26.05V41.58z M83.12,41.58h-8.83V29.75h8.83V41.58z"/>
            <polygon points="48,45.79 51.32,45.79 54.06,45.79 60.36,45.79 60.36,41.61 54.06,41.61 51.32,41.61 48,41.61 44.89,41.61 43.19,41.61 43.19,29.33 44.89,29.33 48,29.33 51.32,29.33 54.06,29.33 60.36,29.33 60.36,25.51 54.06,25.51 51.32,25.51 48,25.51 44.89,25.51 38.58,25.51 38.58,45.79 44.89,45.79"/>
          </svg>
          <span class="x">×</span><span class="pt" id="cPartner">이서이</span>
        </div>

        <!-- 대표 서명형 : 로고와 대표이사를 같은 층위로 -->
        <div class="sign" id="footCeo" style="display:none">
          <img class="sigimg" alt="" src="data:image/png;base64,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">
          <div class="lock">
            <svg class="plogo" viewBox="0 0 130 80" fill="currentColor" aria-label="프드프">
              <path d="M100.86,5.24H48v12h3.32V8.57h47.2v22.9h23.16v39.97H51.32v-8.68H48v12h77V29.11L100.86,5.24z M101.84,28.14V10.88l17.46,17.26H101.84z"/>
              <polygon points="54.06,52.39 51.32,52.39 48,52.39 44.89,52.39 5,52.39 5,56.35 44.89,56.35 48,56.35 51.32,56.35 54.06,56.35 93.95,56.35 93.95,52.39"/>
              <path d="M33.42,41.58h-3.36V29.75h3.36v-4.24H7.37v4.24h3.66v11.83H7.37v4.2h26.05V41.58z M24.96,41.58h-8.83V29.75h8.83V41.58z"/>
              <path d="M91.58,41.58h-3.36V29.75h3.36v-4.24H65.53v4.24h3.66v11.83h-3.66v4.2h26.05V41.58z M83.12,41.58h-8.83V29.75h8.83V41.58z"/>
              <polygon points="48,45.79 51.32,45.79 54.06,45.79 60.36,45.79 60.36,41.61 54.06,41.61 51.32,41.61 48,41.61 44.89,41.61 43.19,41.61 43.19,29.33 44.89,29.33 48,29.33 51.32,29.33 54.06,29.33 60.36,29.33 60.36,25.51 54.06,25.51 51.32,25.51 48,25.51 44.89,25.51 38.58,25.51 38.58,45.79 44.89,45.79"/>
            </svg>
            <span class="vbar"></span><span class="pt">대표이사</span>
          </div>
        </div>
      </div>
    </div>
    <div class="snote">미리보기 · 저장 시 고해상도(약 1800px)로 출력됩니다</div>
  </main>
</div>

<script>
(function(){
  const $=id=>document.getElementById(id);
  let footMode='partner', mode='one';

  const PRESETS={
    hompan:{label:'홈판 블로그',desc:'그린 · 이서이',t1:'네이버 홈판',t2:'블로그 챌린지 수료증',
      ten:'Naver Blog Challenge',course:'네이버 홈판 수익화 챌린지',klabel:'닉네임',
      accent:'#1F7A4D',disc:'#1B2A45',foot:'partner',partner:'이서이',topic:''},
    nomar:{label:'노마케터스',desc:'그린 · 노마케터스',t1:'노마케터스',t2:'블로그 챌린지 수료증',
      ten:'Nomarketers Blog Challenge',course:'노마케터스 블로그 챌린지',klabel:'닉네임',
      accent:'#2D6A4F',disc:'#1B2A45',foot:'partner',partner:'노마케터스',topic:''},
    chosago:{label:'초사고 글쓰기',desc:'로즈 · 대표서명',t1:'초사고 글쓰기',t2:'30일 챌린지 수료증',
      ten:'Chosago Writing Challenge',course:'초사고 글쓰기 30일 챌린지',klabel:'성명',
      accent:'#D24C7C',disc:'#3A1E2A',foot:'ceo',partner:'',topic:''},
    custom:{label:'직접 입력',desc:'새 강의',t1:'',t2:'챌린지 수료증',
      ten:'Challenge',course:'',klabel:'닉네임',
      accent:'#2F6BE0',disc:'#1B2A45',foot:'ceo',partner:'',topic:''}
  };
  const pbox=$('presets');
  Object.keys(PRESETS).forEach(k=>{
    const p=PRESETS[k],b=document.createElement('button');
    b.className='pbtn'+(k==='hompan'?' on':'');b.dataset.k=k;
    b.innerHTML=`<b>${p.label}</b><small>${p.desc}</small>`;
    b.onclick=()=>{document.querySelectorAll('.pbtn').forEach(x=>x.classList.remove('on'));
      b.classList.add('on');applyPreset(k)};
    pbox.appendChild(b);
  });
  function applyPreset(k){
    const p=PRESETS[k];
    $('t1').value=p.t1;$('t2').value=p.t2;$('ten').value=p.ten;$('course').value=p.course;
    $('klabel').value=p.klabel;$('accent').value=p.accent;$('partner').value=p.partner;$('topic').value=p.topic;
    $('mdisc').setAttribute('fill',p.disc);
    setFoot(p.foot);setAccent(p.accent);render();
  }

  ['#1F7A4D','#2D6A4F','#D24C7C','#2F6BE0','#B8912F','#1C2A3A'].forEach(c=>{
    const b=document.createElement('button');b.style.background=c;b.title=c;
    b.onclick=()=>{$('accent').value=c;setAccent(c);};
    $('sw').appendChild(b);
  });
  function setAccent(c){
    $('cert').style.setProperty('--accent',c);
    $('rb1').setAttribute('fill',lighten(c,.22));
    $('rb2').setAttribute('fill',darken(c,.18));
  }
  function hex2rgb(h){h=h.replace('#','');return[parseInt(h.slice(0,2),16),parseInt(h.slice(2,4),16),parseInt(h.slice(4,6),16)];}
  function rgb2hex(a){return '#'+a.map(v=>Math.max(0,Math.min(255,Math.round(v))).toString(16).padStart(2,'0')).join('');}
  function lighten(c,t){const a=hex2rgb(c);return rgb2hex(a.map(v=>v+(255-v)*t));}
  function darken(c,t){const a=hex2rgb(c);return rgb2hex(a.map(v=>v*(1-t)));}

  function setFoot(m){
    footMode=m;
    document.querySelectorAll('.seg').forEach(s=>s.classList.toggle('on',s.dataset.foot===m));
    $('footPartner').style.display=m==='partner'?'flex':'none';
    $('footCeo').style.display=m==='ceo'?'flex':'none';
    $('partnerWrap').style.display=m==='partner'?'block':'none';
  }
  document.querySelectorAll('.seg').forEach(s=>s.onclick=()=>{setFoot(s.dataset.foot);render();});

  document.querySelectorAll('.tab').forEach(t=>t.onclick=()=>{
    document.querySelectorAll('.tab').forEach(x=>x.classList.remove('on'));
    t.classList.add('on');mode=t.dataset.mode;
    $('oneBox').style.display=mode==='one'?'block':'none';
    $('bulkBox').style.display=mode==='bulk'?'block':'none';
    $('warn').style.display='none';
  });

  const today=new Date(),iso=d=>d.toISOString().slice(0,10);
  const ago=new Date(today);ago.setDate(ago.getDate()-62);
  $('d1').value=iso(ago);$('d2').value=iso(today);$('d3').value=iso(today);

  function ymd(v){if(!v)return null;const[y,m,d]=v.split('-').map(Number);return{y,m,d};}
  function fullK(v){const o=ymd(v);return o?`${o.y}년 ${o.m}월 ${o.d}일`:'';}
  function period(a,b){
    const A=ymd(a),B=ymd(b);if(!A||!B)return '';
    const head=`${A.y}년 ${A.m}월 ${A.d}일`;
    const tail=(A.y===B.y)?`${B.m}월 ${B.d}일`:`${B.y}년 ${B.m}월 ${B.d}일`;
    const wk=Math.round((new Date(b)-new Date(a))/(1000*60*60*24)/7);
    return `${head} ~ ${tail}`+(wk>0?` (${wk}주)`:'');
  }
  function ord(n){
    n=parseInt(n)||1;const s=['th','st','nd','rd'],v=n%100;
    return n+(s[(v-20)%10]||s[v]||s[0]);
  }

  function render(nickOverride){
    const gi=$('gi').value||'1';
    const course=$('course').value.trim();
    const nick=(nickOverride!==undefined?nickOverride:$('nick').value.trim());
    const topic=$('topic').value.trim();

    $('cNum').textContent=gi+'기';
    $('cK').textContent=$('klabel').value.trim()||'닉네임';
    $('cNick').textContent=nick;
    $('cCourse').textContent=(course?course+' ':'')+gi+'기';
    $('cPeriod').textContent=period($('d1').value,$('d2').value);
    $('cDate').textContent=fullK($('d3').value);
    $('cPartner').textContent=$('partner').value.trim();

    const t1=$('t1').value.trim(),t2=$('t2').value.trim();
    $('cTitle').innerHTML=(t1?t1+'<br>':'')+t2;
    $('cSub').textContent=`${$('ten').value.trim()} · ${ord(gi)} Cohort`;

    const lead=topic?`위 수료자는 「${topic}」을 주제로 진행된 `:'위 수료자는 프드프에서 진행한 ';
    $('cBody').innerHTML=`${lead}<b>${course} ${gi}기</b> 과정을 성실히 이수하였기에 이 수료증을 수여합니다.`;
  }
  ['t1','t2','ten','course','gi','klabel','topic','d1','d2','d3','partner','nick']
    .forEach(id=>$(id).addEventListener('input',()=>render()));
  $('accent').addEventListener('input',e=>setAccent(e.target.value));

  function names(){return $('list').value.split('\n').map(s=>s.trim()).filter(Boolean);}
  $('list').addEventListener('input',()=>{$('cnt').textContent=names().length+'명';});
  $('csv').addEventListener('change',e=>{
    const f=e.target.files[0];if(!f)return;
    const r=new FileReader();
    r.onload=()=>{
      const rows=String(r.result).split(/\r?\n/).map(l=>l.split(',')[0].trim()).filter(Boolean);
      if(rows.length&&/닉네임|이름|성명|name/i.test(rows[0]))rows.shift();
      $('list').value=rows.join('\n');$('cnt').textContent=rows.length+'명';
    };
    r.readAsText(f,'utf-8');
  });

  function check(needNames){
    const miss=[];
    if(!$('course').value.trim())miss.push('과정명');
    if(!$('t2').value.trim())miss.push('제목 2행');
    if(!$('d1').value||!$('d2').value)miss.push('챌린지 기간');
    if(!$('d3').value)miss.push('수료일');
    if(footMode==='partner'&&!$('partner').value.trim())miss.push('파트너명');
    if(needNames){if(!names().length)miss.push('명단');}
    else if(!$('nick').value.trim())miss.push('닉네임');
    const w=$('warn');
    if(miss.length){w.textContent='비어 있는 항목: '+miss.join(', ')+' — 채운 뒤 다시 시도하세요.';w.style.display='block';return false;}
    w.style.display='none';return true;
  }

  async function shot(){
    if(document.fonts&&document.fonts.ready){try{await document.fonts.ready;}catch(e){}}
    return html2canvas($('cert'),{scale:4,backgroundColor:'#FFFFFF',width:452,height:639,
      windowWidth:452,useCORS:true,logging:false});
  }
  function fname(nick){
    return `프드프_수료증_${(nick||'수료자').replace(/\s+/g,'')}`;
  }
  function dl(href,name){const a=document.createElement('a');a.href=href;a.download=name;a.click();}
  async function busy(btn,fn){
    const t=btn.textContent;btn.textContent='처리 중…';btn.disabled=true;
    try{await fn();}catch(e){console.error(e);alert('생성 중 문제가 발생했어요. 다시 시도해 주세요.');}
    finally{btn.textContent=t;btn.disabled=false;}
  }
  function newPdf(){const{jsPDF}=window.jspdf;return new jsPDF({orientation:'portrait',unit:'mm',format:'a4'});}
  function place(pdf,canvas){
    const pw=pdf.internal.pageSize.getWidth(),ph=pdf.internal.pageSize.getHeight();
    const r=Math.min(pw/452,ph/639),w=452*r,h=639*r;
    pdf.addImage(canvas.toDataURL('image/jpeg',0.94),'JPEG',(pw-w)/2,(ph-h)/2,w,h);
  }

  $('bPng').onclick=e=>{if(!check(false))return;busy(e.target,async()=>{
    render();const c=await shot();dl(c.toDataURL('image/png'),fname($('nick').value)+'.png');});};

  $('bPdf').onclick=e=>{if(!check(false))return;busy(e.target,async()=>{
    render();const c=await shot();const p=newPdf();place(p,c);p.save(fname($('nick').value)+'.pdf');});};

  function prog(i,n){
    const P=$('prog');P.style.display='block';
    $('progT').textContent=`${i} / ${n}명 생성 중`;
    $('progB').style.width=(i/n*100)+'%';
  }

  $('bZip').onclick=e=>{if(!check(true))return;busy(e.target,async()=>{
    const list=names(),zip=new JSZip();
    for(let i=0;i<list.length;i++){
      render(list[i]);await new Promise(r=>setTimeout(r,40));
      const c=await shot();
      zip.file(fname(list[i])+'.png',c.toDataURL('image/png').split(',')[1],{base64:true});
      prog(i+1,list.length);
    }
    const blob=await zip.generateAsync({type:'blob'});
    const url=URL.createObjectURL(blob);
    dl(url,`프드프_수료증_${$('course').value.trim().replace(/\s+/g,'')}_${$('gi').value}기_${list.length}명.zip`);
    setTimeout(()=>URL.revokeObjectURL(url),4000);
    $('prog').style.display='none';render();
  });};

  $('bPdfAll').onclick=e=>{if(!check(true))return;busy(e.target,async()=>{
    const list=names(),pdf=newPdf();
    for(let i=0;i<list.length;i++){
      render(list[i]);await new Promise(r=>setTimeout(r,40));
      const c=await shot();
      if(i>0)pdf.addPage();
      place(pdf,c);
      prog(i+1,list.length);
    }
    pdf.save(`프드프_수료증_${$('course').value.trim().replace(/\s+/g,'')}_${$('gi').value}기_${list.length}명.pdf`);
    $('prog').style.display='none';render();
  });};

  setAccent('#1F7A4D');setFoot('partner');render();
})();
</script>
</body>
</html>
