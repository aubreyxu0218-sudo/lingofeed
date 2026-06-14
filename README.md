# lingofeed
[lingofeed.html](https://github.com/user-attachments/files/28927211/lingofeed.html)
<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>LingoFeed · 碎片英语&阿拉伯语</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  :root {
    --primary: #6C63FF;
    --secondary: #FF6584;
    --bg: #0F0F1A;
    --card-bg: #1A1A2E;
    --card2-bg: #16213E;
    --text: #F0F0F0;
    --text-muted: #8888AA;
    --tag-easy: #27AE60;
    --tag-mid: #F39C12;
    --tag-hard: #E74C3C;
    --arabic-accent: #E67E22;
    --english-accent: #3498DB;
  }
  html, body {
    height: 100%; overflow: hidden;
    background: var(--bg); color: var(--text);
    font-family: -apple-system, 'SF Pro Text', 'Helvetica Neue', Arial, sans-serif;
  }

  /* ===== TOP BAR ===== */
  #topbar {
    position: fixed; top: 0; left: 0; right: 0; z-index: 100;
    background: rgba(15,15,26,0.92);
    backdrop-filter: blur(12px);
    padding: env(safe-area-inset-top, 12px) 16px 0;
  }
  #topbar-inner {
    display: flex; align-items: center; justify-content: space-between;
    padding-bottom: 10px;
  }
  .app-logo {
    font-size: 20px; font-weight: 800;
    background: linear-gradient(135deg, var(--primary), var(--secondary));
    -webkit-background-clip: text; -webkit-text-fill-color: transparent;
    letter-spacing: -0.5px;
  }
  .streak { display: flex; align-items: center; gap: 4px; font-size: 13px; color: #FFD700; font-weight: 600; }

  /* Language tabs */
  #lang-tabs {
    display: flex; gap: 8px; padding-bottom: 10px;
  }
  .lang-tab {
    flex: 1; padding: 7px 0; border-radius: 20px; border: none; cursor: pointer;
    font-size: 13px; font-weight: 600; transition: all 0.2s;
    background: rgba(255,255,255,0.08); color: var(--text-muted);
  }
  .lang-tab.active[data-lang="en"] { background: var(--english-accent); color: #fff; }
  .lang-tab.active[data-lang="ar"] { background: var(--arabic-accent); color: #fff; }

  /* Difficulty filters */
  #diff-row {
    display: flex; gap: 6px; padding-bottom: 12px; overflow-x: auto;
    scrollbar-width: none;
  }
  #diff-row::-webkit-scrollbar { display: none; }
  .diff-btn {
    padding: 4px 14px; border-radius: 20px; border: 1.5px solid transparent;
    font-size: 12px; font-weight: 600; cursor: pointer; white-space: nowrap;
    background: transparent; transition: all 0.2s;
  }
  .diff-btn[data-diff="all"] { border-color: var(--text-muted); color: var(--text-muted); }
  .diff-btn[data-diff="easy"] { border-color: var(--tag-easy); color: var(--tag-easy); }
  .diff-btn[data-diff="mid"] { border-color: var(--tag-mid); color: var(--tag-mid); }
  .diff-btn[data-diff="hard"] { border-color: var(--tag-hard); color: var(--tag-hard); }
  .diff-btn.active[data-diff="all"] { background: var(--text-muted); color: #fff; }
  .diff-btn.active[data-diff="easy"] { background: var(--tag-easy); color: #fff; }
  .diff-btn.active[data-diff="mid"] { background: var(--tag-mid); color: #fff; }
  .diff-btn.active[data-diff="hard"] { background: var(--tag-hard); color: #fff; }

  /* ===== FEED ===== */
  #feed {
    position: fixed; top: 0; left: 0; right: 0; bottom: 0;
    overflow-y: scroll; scroll-snap-type: y mandatory;
    scrollbar-width: none;
  }
  #feed::-webkit-scrollbar { display: none; }
  #feed-inner { display: flex; flex-direction: column; }

  .card-slot {
    scroll-snap-align: start;
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0;
    padding: 16px;
  }

  /* ===== CARD ===== */
  .card {
    width: 100%; max-width: 480px;
    background: var(--card-bg);
    border-radius: 20px;
    padding: 20px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.4);
    border: 1px solid rgba(255,255,255,0.07);
    position: relative;
    overflow: hidden;
  }
  .card::before {
    content: ''; position: absolute; top: 0; left: 0; right: 0; height: 3px;
  }
  .card[data-lang="en"]::before { background: linear-gradient(90deg, var(--english-accent), var(--primary)); }
  .card[data-lang="ar"]::before { background: linear-gradient(90deg, var(--arabic-accent), var(--secondary)); }

  /* Card header */
  .card-header { display: flex; align-items: center; gap: 10px; margin-bottom: 14px; }
  .source-icon {
    width: 34px; height: 34px; border-radius: 10px;
    display: flex; align-items: center; justify-content: center;
    font-size: 16px; font-weight: bold; flex-shrink: 0;
  }
  .card[data-lang="en"] .source-icon { background: rgba(52,152,219,0.2); color: var(--english-accent); }
  .card[data-lang="ar"] .source-icon { background: rgba(230,126,34,0.2); color: var(--arabic-accent); }
  .source-meta { flex: 1; }
  .source-name { font-size: 12px; font-weight: 700; color: var(--text-muted); text-transform: uppercase; letter-spacing: 0.5px; }
  .card-time { font-size: 11px; color: var(--text-muted); margin-top: 1px; }
  .diff-tag {
    padding: 3px 10px; border-radius: 20px; font-size: 11px; font-weight: 700;
  }
  .diff-tag.easy { background: rgba(39,174,96,0.2); color: var(--tag-easy); }
  .diff-tag.mid { background: rgba(243,156,18,0.2); color: var(--tag-mid); }
  .diff-tag.hard { background: rgba(231,76,60,0.2); color: var(--tag-hard); }

  /* Card title */
  .card-topic {
    font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: 1px;
    margin-bottom: 6px;
  }
  .card[data-lang="en"] .card-topic { color: var(--english-accent); }
  .card[data-lang="ar"] .card-topic { color: var(--arabic-accent); }
  .card-headline { font-size: 16px; font-weight: 700; line-height: 1.4; margin-bottom: 14px; }
  .card[data-lang="ar"] .card-headline { direction: rtl; font-family: 'Noto Naskh Arabic', 'Arabic UI Text', Arial, sans-serif; font-size: 17px; }

  /* Paragraph text */
  .card-text {
    font-size: 15px; line-height: 1.75; color: #D0D0E8;
    cursor: text; user-select: none;
  }
  .card[data-lang="ar"] .card-text {
    direction: rtl; text-align: right;
    font-family: 'Noto Naskh Arabic', 'Arabic UI Text', Arial, sans-serif;
    font-size: 16px; line-height: 1.9;
  }
  .word {
    display: inline; cursor: pointer; border-radius: 3px;
    transition: background 0.15s;
    padding: 1px 2px; margin: 0 1px;
  }
  .word:hover, .word:active { background: rgba(108,99,255,0.3); }
  .word.highlighted { background: rgba(108,99,255,0.4); color: var(--primary); }

  /* Translation overlay */
  .translation-bar {
    margin-top: 12px; padding: 10px 14px;
    background: rgba(108,99,255,0.1); border-radius: 12px;
    border-left: 3px solid var(--primary);
    font-size: 14px; line-height: 1.6; color: #C0C0E0;
    display: none;
  }
  .translation-bar.show { display: block; }
  .card[data-lang="ar"] .translation-bar { border-left: none; border-right: 3px solid var(--arabic-accent); }

  /* Card actions */
  .card-actions {
    display: flex; gap: 8px; margin-top: 14px; align-items: center;
  }
  .btn-translate {
    flex: 1; padding: 9px 0; border-radius: 12px; border: none;
    background: rgba(108,99,255,0.15); color: var(--primary);
    font-size: 13px; font-weight: 600; cursor: pointer; transition: all 0.2s;
  }
  .btn-translate:active { background: rgba(108,99,255,0.3); }
  .btn-translate.active { background: var(--primary); color: #fff; }
  .btn-icon {
    width: 38px; height: 38px; border-radius: 12px; border: none;
    background: rgba(255,255,255,0.07); color: var(--text-muted);
    font-size: 16px; cursor: pointer; display: flex; align-items: center; justify-content: center;
    transition: all 0.2s;
  }
  .btn-icon:active { background: rgba(255,255,255,0.15); }
  .btn-icon.saved { color: #FFD700; background: rgba(255,215,0,0.15); }

  /* Word popup */
  #word-popup {
    position: fixed; z-index: 200;
    background: #252540; border-radius: 14px;
    padding: 12px 16px; max-width: 240px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.6);
    border: 1px solid rgba(108,99,255,0.3);
    display: none; pointer-events: none;
    transform: translateX(-50%);
  }
  #word-popup.show { display: block; }
  #word-popup .pw-word { font-size: 17px; font-weight: 700; color: #fff; margin-bottom: 3px; }
  #word-popup .pw-phonetic { font-size: 12px; color: var(--text-muted); margin-bottom: 6px; font-style: italic; }
  #word-popup .pw-meaning { font-size: 14px; color: #C0E0FF; line-height: 1.5; }
  #word-popup .pw-example { font-size: 12px; color: var(--text-muted); margin-top: 5px; font-style: italic; }
  #word-popup .pw-speak {
    display: inline-flex; align-items: center; gap: 4px;
    margin-top: 8px; padding: 4px 10px; border-radius: 20px; border: none;
    background: rgba(108,99,255,0.2); color: var(--primary);
    font-size: 12px; font-weight: 600; cursor: pointer;
    transition: background 0.15s;
  }
  #word-popup .pw-speak:active { background: rgba(108,99,255,0.4); }
  #word-popup .pw-speak.speaking { background: var(--primary); color: #fff; }
  #word-popup::after {
    content: ''; position: absolute; top: 100%; left: 50%; transform: translateX(-50%);
    border: 7px solid transparent; border-top-color: #252540;
  }

  /* Empty state */
  .empty-state {
    text-align: center; padding: 60px 20px;
    color: var(--text-muted); font-size: 15px;
  }
  .empty-state .emoji { font-size: 48px; margin-bottom: 12px; }

  /* Progress dots */
  #progress {
    position: fixed; right: 12px; top: 50%; transform: translateY(-50%);
    display: flex; flex-direction: column; gap: 5px; z-index: 50;
  }
  .pdot {
    width: 4px; height: 4px; border-radius: 2px;
    background: rgba(255,255,255,0.2); transition: all 0.3s;
  }
  .pdot.active { height: 16px; background: var(--primary); }

  /* Bottom nav */
  #bottomnav {
    position: fixed; bottom: 0; left: 0; right: 0; z-index: 100;
    background: rgba(15,15,26,0.95); backdrop-filter: blur(12px);
    display: flex; padding-bottom: env(safe-area-inset-bottom, 8px);
    border-top: 1px solid rgba(255,255,255,0.06);
  }
  .nav-item {
    flex: 1; display: flex; flex-direction: column; align-items: center;
    padding: 10px 0 4px; gap: 3px; cursor: pointer;
    color: var(--text-muted); font-size: 10px; font-weight: 600;
    transition: color 0.2s;
  }
  .nav-item.active { color: var(--primary); }
  .nav-item svg { width: 22px; height: 22px; }

  /* Saved words panel */
  #saved-panel {
    position: fixed; inset: 0; z-index: 300;
    background: var(--bg); transform: translateY(100%);
    transition: transform 0.35s cubic-bezier(.4,0,.2,1);
    display: flex; flex-direction: column;
    padding-top: env(safe-area-inset-top, 20px);
  }
  #saved-panel.open { transform: translateY(0); }
  .panel-header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 16px 20px; border-bottom: 1px solid rgba(255,255,255,0.07);
  }
  .panel-title { font-size: 18px; font-weight: 700; }
  .panel-close {
    width: 32px; height: 32px; border-radius: 50%; border: none;
    background: rgba(255,255,255,0.1); color: var(--text); font-size: 16px;
    cursor: pointer; display: flex; align-items: center; justify-content: center;
  }
  .saved-list { flex: 1; overflow-y: auto; padding: 12px 16px; }
  .saved-item {
    background: var(--card-bg); border-radius: 14px; padding: 14px 16px;
    margin-bottom: 10px; border: 1px solid rgba(255,255,255,0.06);
  }
  .saved-item .si-word { font-size: 17px; font-weight: 700; margin-bottom: 3px; }
  .saved-item .si-meaning { font-size: 13px; color: #C0C0E0; }
  .saved-item .si-tag { font-size: 11px; color: var(--text-muted); margin-top: 4px; }
  .saved-empty { text-align: center; padding: 60px 20px; color: var(--text-muted); }

  /* Loading overlay */
  #loading-overlay {
    position: fixed; inset: 0; z-index: 500;
    background: var(--bg);
    display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 16px;
    transition: opacity 0.4s;
  }
  #loading-overlay.hidden { opacity: 0; pointer-events: none; }
  .loader-ring {
    width: 48px; height: 48px; border-radius: 50%;
    border: 3px solid rgba(108,99,255,0.2);
    border-top-color: var(--primary);
    animation: spin 0.9s linear infinite;
  }
  @keyframes spin { to { transform: rotate(360deg); } }
  .loader-text { font-size: 14px; color: var(--text-muted); }
  .loader-sub { font-size: 12px; color: var(--text-muted); opacity: 0.6; }

  /* Refresh button */
  #btn-refresh {
    background: none; border: none; cursor: pointer;
    font-size: 16px; padding: 4px 6px; border-radius: 8px;
    transition: transform 0.3s;
  }
  #btn-refresh:active { transform: rotate(180deg); }
  #btn-refresh.spinning { animation: spin 1s linear infinite; }

  /* Live badge */
  .live-dot {
    display: inline-block; width: 6px; height: 6px; border-radius: 50%;
    background: #2ECC71; margin-right: 4px;
    animation: pulse 1.5s ease-in-out infinite;
  }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.3} }
  #live-status { font-size: 11px; color: var(--text-muted); display: flex; align-items: center; }
</style>
</head>
<body>

<!-- TOP BAR -->
<div id="topbar">
  <div id="topbar-inner">
    <div class="app-logo">LingoFeed</div>
    <div style="display:flex;align-items:center;gap:8px;">
      <div id="live-status"><span class="live-dot"></span>实时</div>
      <button id="btn-refresh" title="刷新新闻" onclick="refreshNews()">🔄</button>
      <div class="streak">🔥 <span id="streak-count">3</span></div>
    </div>
  </div>
  <div id="lang-tabs">
    <button class="lang-tab active" data-lang="en">🇺🇸 英语</button>
    <button class="lang-tab" data-lang="ar">🇸🇦 阿拉伯语</button>
    <button class="lang-tab" data-lang="both">双语混合</button>
  </div>
  <div id="diff-row">
    <button class="diff-btn active" data-diff="all">全部</button>
    <button class="diff-btn" data-diff="easy">🟢 初级</button>
    <button class="diff-btn" data-diff="mid">🟡 中级</button>
    <button class="diff-btn" data-diff="hard">🔴 高级</button>
  </div>
</div>

<!-- LOADING OVERLAY -->
<div id="loading-overlay">
  <div class="loader-ring"></div>
  <div class="loader-text" id="loading-text">正在拉取最新新闻…</div>
  <div class="loader-sub" id="loading-sub">BBC · Al Jazeera · Reuters</div>
</div>

<!-- FEED -->
<div id="feed"><div id="feed-inner"></div></div>

<!-- PROGRESS DOTS -->
<div id="progress"></div>

<!-- WORD POPUP -->
<div id="word-popup">
  <div class="pw-word" id="pw-word"></div>
  <div class="pw-phonetic" id="pw-phonetic"></div>
  <div class="pw-meaning" id="pw-meaning"></div>
  <div class="pw-example" id="pw-example"></div>
  <button class="pw-speak" id="pw-speak" onclick="speakCurrentWord()">🔊 朗读</button>
</div>

<!-- BOTTOM NAV -->
<div id="bottomnav">
  <div class="nav-item active" id="nav-feed">
    <svg viewBox="0 0 24 24" fill="currentColor"><path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z"/></svg>
    首页
  </div>
  <div class="nav-item" id="nav-saved">
    <svg viewBox="0 0 24 24" fill="currentColor"><path d="M17 3H7c-1.1 0-2 .9-2 2v16l7-3 7 3V5c0-1.1-.9-2-2-2z"/></svg>
    生词本
  </div>
  <div class="nav-item" id="nav-stats">
    <svg viewBox="0 0 24 24" fill="currentColor"><path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-7 3c1.93 0 3.5 1.57 3.5 3.5S13.93 13 12 13s-3.5-1.57-3.5-3.5S10.07 6 12 6zm7 13H5v-.23c0-.62.28-1.2.76-1.58C7.47 15.82 9.64 15 12 15s4.53.82 6.24 2.19c.48.38.76.97.76 1.58V19z"/></svg>
    我的
  </div>
</div>

<!-- SAVED WORDS PANEL -->
<div id="saved-panel">
  <div class="panel-header">
    <div class="panel-title">📚 我的生词本</div>
    <button class="panel-close" id="close-saved">✕</button>
  </div>
  <div class="saved-list" id="saved-list">
    <div class="saved-empty">还没有收藏的单词<br>点击文章中的单词开始收藏吧！</div>
  </div>
</div>

<script>
// ============================================================
// DATA
// ============================================================
const DICT = {
  // English words
  "global": { phonetic: "/ˈɡloʊbəl/", meaning: "全球的；全世界的", example: "global warming 全球变暖" },
  "leaders": { phonetic: "/ˈliːdərz/", meaning: "领导人；领袖（复数）", example: "world leaders 世界领导人" },
  "climate": { phonetic: "/ˈklaɪmɪt/", meaning: "气候；气候变化", example: "climate change 气候变化" },
  "summit": { phonetic: "/ˈsʌmɪt/", meaning: "峰会；顶峰", example: "G20 summit G20峰会" },
  "pledged": { phonetic: "/pledʒd/", meaning: "承诺；保证（过去式）", example: "pledged support 承诺支持" },
  "reduce": { phonetic: "/rɪˈdjuːs/", meaning: "减少；降低", example: "reduce emissions 减少排放" },
  "emissions": { phonetic: "/ɪˈmɪʃənz/", meaning: "排放物；（温室气体）排放量", example: "carbon emissions 碳排放" },
  "renewable": { phonetic: "/rɪˈnjuːəbl/", meaning: "可再生的；可更新的", example: "renewable energy 可再生能源" },
  "energy": { phonetic: "/ˈenədʒi/", meaning: "能源；精力；能量", example: "solar energy 太阳能" },
  "significant": { phonetic: "/sɪɡˈnɪfɪkənt/", meaning: "重要的；显著的；有意义的", example: "significant progress 重大进展" },
  "artificial": { phonetic: "/ˌɑːrtɪˈfɪʃl/", meaning: "人工的；人造的；artificial intelligence 人工智能", example: "artificial intelligence 人工智能" },
  "intelligence": { phonetic: "/ɪnˈtelɪdʒəns/", meaning: "智力；情报；人工智能（AI）", example: "machine intelligence 机器智能" },
  "researchers": { phonetic: "/rɪˈsɜːrtʃərz/", meaning: "研究人员；研究者（复数）", example: "AI researchers AI研究人员" },
  "developed": { phonetic: "/dɪˈveləpt/", meaning: "开发；研发（过去式）；发达的", example: "developed countries 发达国家" },
  "model": { phonetic: "/ˈmɒdl/", meaning: "模型；模式；范例", example: "language model 语言模型" },
  "accurately": { phonetic: "/ˈækjərətli/", meaning: "准确地；精确地", example: "accurately predict 准确预测" },
  "disease": { phonetic: "/dɪˈziːz/", meaning: "疾病；病症", example: "infectious disease 传染病" },
  "breakthrough": { phonetic: "/ˈbreɪkθruː/", meaning: "突破；重大进展", example: "scientific breakthrough 科学突破" },
  "economic": { phonetic: "/ˌiːkəˈnɒmɪk/", meaning: "经济的；经济上的", example: "economic growth 经济增长" },
  "growth": { phonetic: "/ɡroʊθ/", meaning: "增长；发展；成长", example: "GDP growth GDP增长" },
  "technology": { phonetic: "/tekˈnɒlədʒi/", meaning: "技术；科技", example: "information technology 信息技术" },
  "innovation": { phonetic: "/ˌɪnəˈveɪʃn/", meaning: "创新；革新", example: "technological innovation 技术创新" },
  "parliament": { phonetic: "/ˈpɑːrləmənt/", meaning: "议会；国会；parliament building 议会大厦", example: "UK Parliament 英国议会" },
  "approved": { phonetic: "/əˈpruːvd/", meaning: "批准；认可（过去式）", example: "approved the bill 批准了法案" },
  "legislation": { phonetic: "/ˌledʒɪˈsleɪʃn/", meaning: "立法；法律；法规", example: "new legislation 新立法" },
  "crisis": { phonetic: "/ˈkraɪsɪs/", meaning: "危机；紧急关头", example: "financial crisis 金融危机" },
  "billion": { phonetic: "/ˈbɪljən/", meaning: "十亿", example: "$5 billion 50亿美元" },
  "investment": { phonetic: "/ɪnˈvestmənt/", meaning: "投资；投入", example: "foreign investment 外国投资" },
  "announced": { phonetic: "/əˈnaʊnst/", meaning: "宣布；公告（过去式）", example: "announced a plan 宣布了计划" },
  "space": { phonetic: "/speɪs/", meaning: "太空；空间；宇宙", example: "space exploration 太空探索" },
  "mission": { phonetic: "/ˈmɪʃn/", meaning: "任务；使命；太空任务", example: "space mission 太空任务" },
  "launched": { phonetic: "/lɔːntʃt/", meaning: "发射；发布；启动（过去式）", example: "launched a rocket 发射火箭" },
  "satellite": { phonetic: "/ˈsætəlaɪt/", meaning: "卫星；人造卫星", example: "communication satellite 通信卫星" },
};

// ============================================================
// FALLBACK DATA (used if RSS fetch fails)
// ============================================================
const FALLBACK_ARTICLES = [
  {
    id: "f1", lang: "en", diff: "easy",
    source: "BBC", icon: "B", topic: "🌍 时事",
    time: "示例内容",
    headline: "World Leaders Meet to Discuss Climate Action",
    text: "Global leaders gathered in Geneva this week for an important climate summit. They pledged to reduce carbon emissions by 50% before 2035. The meeting focused on expanding renewable energy sources worldwide.",
    translation: "本周，全球领导人齐聚日内瓦，参加一场重要的气候峰会。他们承诺在2035年之前将碳排放量减少50%。此次会议重点讨论了在全球范围内扩大可再生能源的使用。",
    link: ""
  },
  {
    id: "f2", lang: "en", diff: "mid",
    source: "Reuters", icon: "R", topic: "🤖 科技",
    time: "示例内容",
    headline: "AI Researchers Develop Model That Can Detect Disease Early",
    text: "Researchers at MIT have developed a new artificial intelligence model that can accurately predict the onset of several diseases up to five years in advance. This breakthrough could significantly improve early diagnosis and treatment outcomes for millions of patients worldwide.",
    translation: "麻省理工学院的研究人员开发出一种新型人工智能模型，能够提前五年准确预测多种疾病的发病。这一突破性进展可能大幅改善全球数百万患者的早期诊断和治疗效果。",
    link: ""
  },
  {
    id: "f3", lang: "ar", diff: "easy",
    source: "الجزيرة", icon: "ج", topic: "🌍 أخبار",
    time: "محتوى تجريبي",
    headline: "اجتماع قادة العالم لمناقشة تغير المناخ",
    text: "اجتمع قادة العالم في جنيف هذا الأسبوع لمناقشة قضايا المناخ. وتعهدوا بتخفيض الانبعاثات الكربونية بنسبة خمسين بالمئة قبل عام ألفين وخمسة وثلاثين. وركّز الاجتماع على توسيع مصادر الطاقة المتجددة.",
    translation: "世界领导人本周在日内瓦集会，讨论气候问题。他们承诺在2035年前将碳排放量减少50%。会议重点讨论了扩大可再生能源来源。",
    link: ""
  },
  {
    id: "f4", lang: "ar", diff: "mid",
    source: "العربية", icon: "ع", topic: "💻 تكنولوجيا",
    time: "محتوى تجريبي",
    headline: "شركات التكنولوجيا تستثمر مليارات في الذكاء الاصطناعي",
    text: "أعلنت كبرى شركات التكنولوجيا في العالم عن استثمارات ضخمة في مجال الذكاء الاصطناعي خلال العام الحالي. وتسعى هذه الشركات إلى تطوير نماذج لغوية أكثر قدرة وكفاءة، مما يُشير إلى سباق تنافسي متصاعد في هذا القطاع الحيوي.",
    translation: "全球主要科技公司在今年宣布了对人工智能领域的大规模投资。这些公司致力于开发能力更强的语言模型，显示出在这一关键领域日益激烈的竞争态势。",
    link: ""
  },
];

// ============================================================
// RSS LIVE FETCH
// ============================================================
const RSS_PROXY = "https://api.rss2json.com/v1/api.json?count=15&rss_url=";
const RSS_FEEDS = [
  { url: "https://feeds.bbci.co.uk/news/world/rss.xml",    lang: "en", source: "BBC World",    icon: "B" },
  { url: "https://feeds.bbci.co.uk/news/technology/rss.xml", lang: "en", source: "BBC Tech",   icon: "B" },
  { url: "https://feeds.bbci.co.uk/arabic/rss.xml",        lang: "ar", source: "BBC عربي",     icon: "ب" },
  { url: "https://www.aljazeera.net/xml/rss/all.xml",      lang: "ar", source: "الجزيرة",      icon: "ج" },
];

function stripHtml(html) {
  return (html || "").replace(/<[^>]*>/g, "").replace(/&amp;/g,"&").replace(/&lt;/g,"<").replace(/&gt;/g,">").replace(/&quot;/g,'"').replace(/&#39;/g,"'").replace(/&nbsp;/g," ").trim();
}

function estimateDiff(text, lang) {
  if (lang === "ar") {
    const words = text.split(/\s+/).length;
    if (words < 30) return "easy";
    if (words < 60) return "mid";
    return "hard";
  }
  const sentences = text.split(/[.!?]+/).filter(s => s.trim().length > 0);
  if (!sentences.length) return "mid";
  const avgWords = text.split(/\s+/).length / sentences.length;
  if (avgWords < 14) return "easy";
  if (avgWords < 24) return "mid";
  return "hard";
}

function detectTopic(text, lang) {
  const t = text.toLowerCase();
  if (/tech|ai|robot|software|digital|cyber|internet|computer/.test(t)) return lang === "ar" ? "💻 تكنولوجيا" : "💻 科技";
  if (/climat|environment|carbon|green|energy|weather/.test(t)) return lang === "ar" ? "🌿 البيئة" : "🌿 环境";
  if (/war|conflict|attack|militar|troops|weapon/.test(t)) return lang === "ar" ? "⚔️ نزاعات" : "⚔️ 冲突";
  if (/economy|market|bank|trade|inflation|gdp|financial/.test(t)) return lang === "ar" ? "💹 اقتصاد" : "💹 经济";
  if (/health|vaccine|disease|virus|hospital|medical/.test(t)) return lang === "ar" ? "🏥 صحة" : "🏥 健康";
  if (/sport|football|soccer|olympic|tennis|cricket/.test(t)) return lang === "ar" ? "⚽ رياضة" : "⚽ 体育";
  if (/space|nasa|rocket|satellite|mars|moon/.test(t)) return lang === "ar" ? "🚀 فضاء" : "🚀 太空";
  if (/election|vote|parliament|president|government|politic/.test(t)) return lang === "ar" ? "🏛️ سياسة" : "🏛️ 政治";
  return lang === "ar" ? "🌍 أخبار عالمية" : "🌍 国际";
}

function relativeTime(pubDate) {
  if (!pubDate) return "最新";
  const diff = Date.now() - new Date(pubDate).getTime();
  const mins = Math.floor(diff / 60000);
  if (mins < 60) return mins <= 1 ? "刚刚" : `${mins}分钟前`;
  const hrs = Math.floor(mins / 60);
  if (hrs < 24) return `${hrs}小时前`;
  return `${Math.floor(hrs/24)}天前`;
}

function relativeTimeAr(pubDate) {
  if (!pubDate) return "الآن";
  const diff = Date.now() - new Date(pubDate).getTime();
  const mins = Math.floor(diff / 60000);
  if (mins < 60) return mins <= 1 ? "الآن" : `منذ ${mins} دقيقة`;
  const hrs = Math.floor(mins / 60);
  if (hrs < 24) return `منذ ${hrs} ساعة`;
  return `منذ ${Math.floor(hrs/24)} يوم`;
}

async function fetchFeed(feed) {
  const url = RSS_PROXY + encodeURIComponent(feed.url);
  const res = await fetch(url, { signal: AbortSignal.timeout(8000) });
  if (!res.ok) throw new Error("HTTP " + res.status);
  const data = await res.json();
  if (data.status !== "ok" || !data.items) throw new Error("RSS error");

  return data.items.slice(0, 12).map((item, i) => {
    const raw = stripHtml(item.description || item.content || "");
    // Take first 2-3 sentences as the paragraph
    const sentences = raw.split(/(?<=[.!?؟])\s+/);
    const para = sentences.slice(0, 3).join(" ").substring(0, 400);
    if (!para || para.length < 30) return null;

    return {
      id: feed.lang + "_" + i + "_" + Date.now(),
      lang: feed.lang,
      diff: estimateDiff(para, feed.lang),
      source: feed.source,
      icon: feed.icon,
      topic: detectTopic((item.title || "") + " " + para, feed.lang),
      time: feed.lang === "ar" ? relativeTimeAr(item.pubDate) : relativeTime(item.pubDate),
      headline: stripHtml(item.title || ""),
      text: para,
      translation: "（翻译加载中…点击获取）",
      link: item.link || "",
      isLive: true,
    };
  }).filter(Boolean);
}

async function refreshNews() {
  const overlay = document.getElementById("loading-overlay");
  const btn = document.getElementById("btn-refresh");
  overlay.classList.remove("hidden");
  btn.classList.add("spinning");
  document.getElementById("loading-text").textContent = "正在拉取最新新闻…";
  document.getElementById("loading-sub").textContent = "BBC · Al Jazeera Arabic · BBC Arabic";

  try {
    const results = await Promise.allSettled(RSS_FEEDS.map(fetchFeed));
    const live = results.flatMap(r => r.status === "fulfilled" ? r.value : []);

    if (live.length > 0) {
      allArticles = live;
      document.getElementById("live-status").innerHTML = `<span class="live-dot"></span>${live.length}条`;
    } else {
      allArticles = [...FALLBACK_ARTICLES];
      document.getElementById("live-status").innerHTML = `<span style="color:#E74C3C">●</span> 离线`;
    }
  } catch(e) {
    allArticles = [...FALLBACK_ARTICLES];
    document.getElementById("live-status").innerHTML = `<span style="color:#E74C3C">●</span> 离线`;
  }

  overlay.classList.add("hidden");
  btn.classList.remove("spinning");
  renderFeed();
}

// ============================================================
// STATE
// ============================================================
let activeLang = "en";
let activeDiff = "all";
let savedWords = JSON.parse(localStorage.getItem("lf_saved") || "[]");
let streak = parseInt(localStorage.getItem("lf_streak") || "3");
let currentCardIndex = 0;
let filteredArticles = [];
let wordPopupTimer = null;
let allArticles = [...FALLBACK_ARTICLES];

// ============================================================
// FILTER + RENDER FEED
// ============================================================
function getFiltered() {
  return allArticles.filter(a => {
    const langOk = activeLang === "both" || a.lang === activeLang;
    const diffOk = activeDiff === "all" || a.diff === activeDiff;
    return langOk && diffOk;
  });
}

function renderFeed() {
  filteredArticles = getFiltered();
  const inner = document.getElementById("feed-inner");
  const feed = document.getElementById("feed");
  inner.innerHTML = "";

  // Calculate card slot height
  const topbarH = document.getElementById("topbar").offsetHeight;
  const bottomnavH = document.getElementById("bottomnav").offsetHeight;
  const slotH = window.innerHeight - topbarH - bottomnavH;

  if (filteredArticles.length === 0) {
    const slot = document.createElement("div");
    slot.className = "card-slot";
    slot.style.height = slotH + "px";
    slot.style.paddingTop = topbarH + "px";
    slot.innerHTML = `<div class="empty-state"><div class="emoji">🔍</div>暂无该筛选条件下的文章</div>`;
    inner.appendChild(slot);
    renderProgress(0, 0);
    return;
  }

  filteredArticles.forEach((art, i) => {
    const slot = document.createElement("div");
    slot.className = "card-slot";
    slot.style.height = slotH + "px";
    slot.style.paddingTop = (topbarH + 8) + "px";
    slot.style.paddingBottom = (bottomnavH + 8) + "px";
    slot.innerHTML = buildCard(art, i);
    inner.appendChild(slot);
  });

  renderProgress(0, filteredArticles.length);

  // Attach events
  attachCardEvents();

  // Scroll listener
  feed.onscroll = () => {
    const idx = Math.round(feed.scrollTop / slotH);
    if (idx !== currentCardIndex) {
      currentCardIndex = idx;
      updateProgressDots(idx);
    }
  };

  // Reset scroll
  feed.scrollTo({ top: 0 });
  currentCardIndex = 0;
}

function buildCard(art, idx) {
  const diffLabel = { easy: "初级", mid: "中级", hard: "高级" }[art.diff];
  const diffClass = art.diff;

  // Tokenize text
  const tokenized = tokenizeText(art.text, art.lang);

  return `
    <div class="card" data-lang="${art.lang}" data-id="${art.id}">
      <div class="card-header">
        <div class="source-icon">${art.icon}</div>
        <div class="source-meta">
          <div class="source-name">${art.source}</div>
          <div class="card-time">${art.time}</div>
        </div>
        <div class="diff-tag ${diffClass}">${diffLabel}</div>
      </div>
      <div class="card-topic">${art.topic}</div>
      <div class="card-headline">${art.headline}</div>
      <div class="card-text">${tokenized}</div>
      <div class="translation-bar" id="trans-${art.id.toString().replace(/[^a-z0-9]/gi,'_')}">${art.translation}</div>
      <div class="card-actions">
        <button class="btn-translate" data-id="${art.id}" onclick="toggleTranslation('${art.id.toString().replace(/[^a-z0-9]/gi,'_')}', this)">📖 查看全文翻译</button>
        ${art.link ? `<button class="btn-icon" onclick="window.open('${art.link}','_blank')" title="原文">🔗</button>` : ""}
        <button class="btn-icon ${isSavedCard(art.id) ? 'saved' : ''}" onclick="toggleSaveCard('${art.id}', this)" title="收藏">🔖</button>
      </div>
    </div>
  `;
}

function tokenizeText(text, lang) {
  if (lang === "ar") {
    // For Arabic, wrap each word
    return text.split(/(\s+)/).map(part => {
      if (/\s+/.test(part)) return part;
      return `<span class="word" onclick="showArabicWord(event, this)">${part}</span>`;
    }).join("");
  } else {
    // For English, wrap each word (strip punctuation for lookup)
    return text.replace(/(\b\w+\b)/g, (word) => {
      return `<span class="word" onclick="showWord(event, this)">${word}</span>`;
    });
  }
}

function isSavedCard(id) {
  return savedWords.some(w => w.cardId === id && w.type === "card");
}

// ============================================================
// SPEECH
// ============================================================
let currentSpeakWord = "";
let currentSpeakLang = "en-US";

function speak(text, lang) {
  if (!window.speechSynthesis) return;
  window.speechSynthesis.cancel();
  const utter = new SpeechSynthesisUtterance(text);
  utter.lang = lang;
  utter.rate = 0.85;
  utter.pitch = 1;

  const btn = document.getElementById("pw-speak");
  btn.classList.add("speaking");
  btn.textContent = "🔊 朗读中…";
  utter.onend = () => { btn.classList.remove("speaking"); btn.textContent = "🔊 朗读"; };
  utter.onerror = () => { btn.classList.remove("speaking"); btn.textContent = "🔊 朗读"; };

  // Pick a natural voice if available
  const voices = window.speechSynthesis.getVoices();
  const preferred = voices.find(v => v.lang === lang && v.localService) ||
                    voices.find(v => v.lang.startsWith(lang.split("-")[0]));
  if (preferred) utter.voice = preferred;

  window.speechSynthesis.speak(utter);
}

function speakCurrentWord() {
  if (currentSpeakWord) speak(currentSpeakWord, currentSpeakLang);
}

// Preload voices (required on some browsers)
if (window.speechSynthesis) {
  window.speechSynthesis.getVoices();
  window.speechSynthesis.onvoiceschanged = () => window.speechSynthesis.getVoices();
}

// ============================================================
// WORD POPUP
// ============================================================
function showWord(event, el) {
  event.stopPropagation();
  const rawWord = el.textContent.toLowerCase().replace(/[^a-z]/g, "");
  const entry = DICT[rawWord];

  if (!entry) {
    // Generic fallback
    showPopupAt(el, el.textContent, "", `"${el.textContent}" — 点击收藏此词`, "", "en-US");
    return;
  }

  // Highlight
  document.querySelectorAll(".word.highlighted").forEach(w => w.classList.remove("highlighted"));
  el.classList.add("highlighted");

  showPopupAt(el, el.textContent, entry.phonetic, entry.meaning, entry.example, "en-US");

  // Save word on second tap
  el.onclick = (e) => {
    e.stopPropagation();
    saveWord(el.textContent, entry.meaning, entry.phonetic);
    el.classList.add("highlighted");
    showPopupAt(el, el.textContent, entry.phonetic, "✅ 已加入生词本", "");
    setTimeout(() => el.onclick = (ev) => { ev.stopPropagation(); showWord(ev, el); }, 1500);
  };
  setTimeout(() => {
    if (!DICT[rawWord]) return;
    el.onclick = (ev) => { ev.stopPropagation(); showWord(ev, el); };
  }, 3000);
}

function showArabicWord(event, el) {
  event.stopPropagation();
  const word = el.textContent;
  showPopupAt(el, word, "", "阿拉伯语词汇 — 再次点击可收藏", "", "ar-SA");

  el.onclick = (e) => {
    e.stopPropagation();
    saveWord(word, "阿拉伯语词汇", "");
    showPopupAt(el, word, "", "✅ 已加入生词本", "");
  };
  setTimeout(() => { el.onclick = (ev) => { ev.stopPropagation(); showArabicWord(ev, el); }; }, 2000);
}

function showPopupAt(el, word, phonetic, meaning, example, lang) {
  const popup = document.getElementById("word-popup");
  document.getElementById("pw-word").textContent = word;
  document.getElementById("pw-phonetic").textContent = phonetic;
  document.getElementById("pw-meaning").textContent = meaning;
  document.getElementById("pw-example").textContent = example;

  // Set speak context
  currentSpeakWord = word;
  currentSpeakLang = lang || "en-US";

  const rect = el.getBoundingClientRect();
  popup.style.left = (rect.left + rect.width / 2) + "px";
  popup.style.top = (rect.top - 10) + "px";
  popup.style.transform = "translate(-50%, -100%)";
  popup.classList.add("show");

  // Auto-speak immediately
  speak(word, currentSpeakLang);

  clearTimeout(wordPopupTimer);
  wordPopupTimer = setTimeout(() => {
    popup.classList.remove("show");
    document.querySelectorAll(".word.highlighted").forEach(w => w.classList.remove("highlighted"));
  }, 5000);
}

document.addEventListener("click", (e) => {
  if (!e.target.classList.contains("word")) {
    document.getElementById("word-popup").classList.remove("show");
    document.querySelectorAll(".word.highlighted").forEach(w => w.classList.remove("highlighted"));
  }
});

// ============================================================
// TRANSLATION
// ============================================================
function toggleTranslation(id, btn) {
  const bar = document.getElementById("trans-" + id);
  const showing = bar.classList.toggle("show");
  btn.textContent = showing ? "✕ 收起翻译" : "📖 查看全文翻译";
  btn.classList.toggle("active", showing);
}

// ============================================================
// SAVE WORDS
// ============================================================
function saveWord(word, meaning, phonetic) {
  if (!savedWords.find(w => w.word === word)) {
    savedWords.push({ word, meaning, phonetic, addedAt: new Date().toLocaleDateString() });
    localStorage.setItem("lf_saved", JSON.stringify(savedWords));
    updateSavedCount();
  }
}

function toggleSaveCard(id, btn) {
  const art = allArticles.find(a => a.id === id);
  if (!art) return;
  const existing = savedWords.findIndex(w => w.cardId === id);
  if (existing >= 0) {
    savedWords.splice(existing, 1);
    btn.classList.remove("saved");
  } else {
    savedWords.push({ cardId: id, word: art.headline, meaning: art.translation.substring(0, 60) + "...", addedAt: new Date().toLocaleDateString(), type: "card" });
    btn.classList.add("saved");
  }
  localStorage.setItem("lf_saved", JSON.stringify(savedWords));
  updateSavedCount();
}

function updateSavedCount() {
  renderSavedList();
}

function renderSavedList() {
  const list = document.getElementById("saved-list");
  if (savedWords.length === 0) {
    list.innerHTML = '<div class="saved-empty">还没有收藏的单词<br>点击文章中的单词开始收藏吧！</div>';
    return;
  }
  list.innerHTML = savedWords.slice().reverse().map(w => `
    <div class="saved-item">
      <div class="si-word">${w.word}</div>
      <div class="si-meaning">${w.meaning}</div>
      <div class="si-tag">${w.phonetic || ""} · ${w.addedAt}</div>
    </div>
  `).join("");
}

// ============================================================
// PROGRESS DOTS
// ============================================================
function renderProgress(activeIdx, total) {
  const prog = document.getElementById("progress");
  if (total <= 1) { prog.innerHTML = ""; return; }
  prog.innerHTML = Array.from({ length: total }, (_, i) =>
    `<div class="pdot${i === activeIdx ? " active" : ""}"></div>`
  ).join("");
}

function updateProgressDots(idx) {
  const dots = document.querySelectorAll(".pdot");
  dots.forEach((d, i) => d.classList.toggle("active", i === idx));
}

// ============================================================
// ATTACH CARD EVENTS
// ============================================================
function attachCardEvents() { /* events are inline */ }

// ============================================================
// FILTER CONTROLS
// ============================================================
document.querySelectorAll(".lang-tab").forEach(btn => {
  btn.addEventListener("click", () => {
    document.querySelectorAll(".lang-tab").forEach(b => b.classList.remove("active"));
    btn.classList.add("active");
    activeLang = btn.dataset.lang;
    renderFeed();
  });
});

document.querySelectorAll(".diff-btn").forEach(btn => {
  btn.addEventListener("click", () => {
    document.querySelectorAll(".diff-btn").forEach(b => b.classList.remove("active"));
    btn.classList.add("active");
    activeDiff = btn.dataset.diff;
    renderFeed();
  });
});

// ============================================================
// NAVIGATION
// ============================================================
document.getElementById("nav-saved").addEventListener("click", () => {
  renderSavedList();
  document.getElementById("saved-panel").classList.add("open");
  document.querySelectorAll(".nav-item").forEach(n => n.classList.remove("active"));
  document.getElementById("nav-saved").classList.add("active");
});
document.getElementById("close-saved").addEventListener("click", () => {
  document.getElementById("saved-panel").classList.remove("open");
  document.querySelectorAll(".nav-item").forEach(n => n.classList.remove("active"));
  document.getElementById("nav-feed").classList.add("active");
});
document.getElementById("nav-feed").addEventListener("click", () => {
  document.getElementById("saved-panel").classList.remove("open");
  document.querySelectorAll(".nav-item").forEach(n => n.classList.remove("active"));
  document.getElementById("nav-feed").classList.add("active");
});

// ============================================================
// INIT
// ============================================================
window.addEventListener("load", () => {
  document.getElementById("streak-count").textContent = streak;
  // Fetch live news; falls back to sample data if offline
  setTimeout(refreshNews, 50);
});
window.addEventListener("resize", () => setTimeout(renderFeed, 50));
</script>
</body>
</html>
