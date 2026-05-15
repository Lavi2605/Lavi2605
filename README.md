<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>LAVISH // TERMINAL OS</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;700&family=Share+Tech+Mono&display=swap" rel="stylesheet"/>
<style>
  *{margin:0;padding:0;box-sizing:border-box}
  :root{
    --bg:#030712;
    --green:#00FF9C;
    --cyan:#00E5FF;
    --red:#FF3B3B;
    --purple:#8B5CF6;
    --gray:#9CA3AF;
    --dim-green:#00FF9C22;
    --dim-cyan:#00E5FF15;
    --dim-purple:#8B5CF620;
    --border:#00FF9C30;
    --mono:'JetBrains Mono',monospace;
  }
  html{scroll-behavior:smooth}
  body{
    background:var(--bg);
    color:var(--green);
    font-family:var(--mono);
    font-size:14px;
    line-height:1.6;
    overflow-x:hidden;
    cursor:default;
  }

  /* SCANLINE OVERLAY */
  body::before{
    content:'';
    position:fixed;
    inset:0;
    background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,0,0,0.08) 2px,rgba(0,0,0,0.08) 4px);
    pointer-events:none;
    z-index:9999;
    animation:scanMove 8s linear infinite;
  }
  @keyframes scanMove{0%{background-position:0 0}100%{background-position:0 100px}}

  /* NOISE */
  body::after{
    content:'';
    position:fixed;
    inset:0;
    opacity:0.03;
    background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)'/%3E%3C/svg%3E");
    pointer-events:none;
    z-index:9998;
  }

  /* SCROLLBAR */
  ::-webkit-scrollbar{width:4px}
  ::-webkit-scrollbar-track{background:#000}
  ::-webkit-scrollbar-thumb{background:var(--green);border-radius:2px}

  /* SELECTION */
  ::selection{background:var(--green);color:#000}

  .container{max-width:900px;margin:0 auto;padding:0 20px}

  /* BLINK CURSOR */
  .blink{animation:blink 1s step-end infinite}
  @keyframes blink{0%,100%{opacity:1}50%{opacity:0}}

  /* TERMINAL WINDOW */
  .terminal{
    background:#050d1a;
    border:1px solid var(--border);
    border-radius:4px;
    margin:24px 0;
    overflow:hidden;
    position:relative;
  }
  .terminal::before{
    content:'';
    position:absolute;
    inset:0;
    box-shadow:inset 0 0 60px rgba(0,255,156,0.03);
    pointer-events:none;
  }
  .terminal-bar{
    background:#0a1628;
    border-bottom:1px solid var(--border);
    padding:8px 14px;
    display:flex;
    align-items:center;
    gap:8px;
  }
  .dot{width:10px;height:10px;border-radius:50%}
  .dot-r{background:#FF5F57}
  .dot-y{background:#FFBD2E}
  .dot-g{background:#28CA41}
  .terminal-title{color:var(--gray);font-size:11px;margin-left:auto;margin-right:auto}
  .terminal-body{padding:20px;position:relative}

  /* GLITCH TEXT */
  .glitch{
    position:relative;
    display:inline-block;
  }
  .glitch::before,.glitch::after{
    content:attr(data-text);
    position:absolute;
    top:0;left:0;
    width:100%;height:100%;
  }
  .glitch::before{
    color:var(--cyan);
    animation:glitchTop 3s infinite;
    clip-path:polygon(0 0,100% 0,100% 35%,0 35%);
  }
  .glitch::after{
    color:var(--red);
    animation:glitchBot 3s infinite;
    clip-path:polygon(0 65%,100% 65%,100% 100%,0 100%);
  }
  @keyframes glitchTop{
    0%,90%,100%{transform:translate(0)}
    92%{transform:translate(-2px,-1px)}
    94%{transform:translate(2px,1px)}
    96%{transform:translate(-1px,0)}
  }
  @keyframes glitchBot{
    0%,90%,100%{transform:translate(0)}
    92%{transform:translate(2px,1px)}
    94%{transform:translate(-2px,-1px)}
    96%{transform:translate(1px,0)}
  }

  /* TYPING ANIMATION */
  .typewriter{overflow:hidden;white-space:nowrap;border-right:2px solid var(--green);width:0;animation:typing 3s steps(40,end) forwards,cursorBlink 0.75s step-end infinite}
  @keyframes typing{to{width:100%}}
  @keyframes cursorBlink{0%,100%{border-color:var(--green)}50%{border-color:transparent}}

  /* PROMPT LINE */
  .prompt{color:var(--gray);margin:4px 0}
  .prompt .user{color:var(--green)}
  .prompt .host{color:var(--cyan)}
  .prompt .path{color:var(--purple)}
  .cmd{color:#fff}
  .output{color:var(--gray);margin-left:0;padding-left:0}
  .output-g{color:var(--green)}
  .output-c{color:var(--cyan)}
  .output-r{color:var(--red)}
  .output-p{color:var(--purple)}

  /* PROGRESS BAR */
  .prog-wrap{display:flex;align-items:center;gap:12px;margin:4px 0}
  .prog-label{color:var(--gray);min-width:80px;font-size:12px}
  .prog-bar{flex:1;height:8px;background:#0a1628;border:1px solid var(--border);border-radius:2px;overflow:hidden}
  .prog-fill{height:100%;background:linear-gradient(90deg,var(--green),var(--cyan));border-radius:2px;animation:fillBar 1.5s ease-out forwards}
  @keyframes fillBar{from{width:0%}}
  .prog-pct{color:var(--green);font-size:11px;min-width:36px;text-align:right}

  /* METRIC CARDS */
  .metrics-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));gap:12px;margin:16px 0}
  .metric-card{
    background:#050d1a;
    border:1px solid var(--border);
    border-radius:4px;
    padding:16px;
    text-align:center;
    position:relative;
    overflow:hidden;
  }
  .metric-card::before{
    content:'';
    position:absolute;
    top:0;left:0;right:0;height:1px;
    background:linear-gradient(90deg,transparent,var(--green),transparent);
  }
  .metric-num{font-size:32px;font-weight:700;color:var(--green);letter-spacing:-1px}
  .metric-label{font-size:10px;color:var(--gray);text-transform:uppercase;letter-spacing:2px;margin-top:4px}
  .metric-prefix{font-size:12px;color:var(--cyan);margin-bottom:4px}

  /* TIMELINE */
  .timeline{position:relative;padding-left:32px;margin:12px 0}
  .timeline::before{content:'';position:absolute;left:10px;top:0;bottom:0;width:1px;background:var(--border)}
  .tl-item{position:relative;margin-bottom:20px}
  .tl-dot{position:absolute;left:-27px;top:4px;width:10px;height:10px;border-radius:50%;background:var(--green);border:2px solid var(--bg);box-shadow:0 0 8px var(--green)}
  .tl-date{font-size:10px;color:var(--cyan);letter-spacing:1px}
  .tl-title{color:var(--green);font-size:13px;font-weight:600}
  .tl-desc{color:var(--gray);font-size:12px;margin-top:2px}

  /* REPO CARD */
  .repo-list{display:grid;grid-template-columns:repeat(auto-fit,minmax(340px,1fr));gap:10px;margin:12px 0}
  .repo-card{
    background:#050d1a;
    border:1px solid var(--border);
    border-radius:4px;
    padding:14px;
    position:relative;
    transition:border-color 0.3s,box-shadow 0.3s;
  }
  .repo-card:hover{border-color:var(--cyan);box-shadow:0 0 20px rgba(0,229,255,0.08)}
  .repo-name{color:var(--cyan);font-weight:700;font-size:13px}
  .repo-desc{color:var(--gray);font-size:11px;margin:6px 0}
  .repo-lang{font-size:10px;color:var(--purple)}
  .repo-lang::before{content:'● ';font-size:8px}
  .repo-badge{position:absolute;top:8px;right:10px;font-size:9px;color:var(--green);border:1px solid var(--border);padding:1px 6px;border-radius:2px}

  /* ACTIVITY FEED */
  .feed-item{display:flex;gap:12px;padding:8px 0;border-bottom:1px solid #0a1628}
  .feed-year{color:var(--cyan);font-size:11px;min-width:50px}
  .feed-text{color:var(--gray);font-size:12px}
  .feed-badge{display:inline-block;padding:1px 8px;border-radius:2px;font-size:10px;margin-left:8px}
  .fb-win{background:rgba(0,255,156,0.1);color:var(--green);border:1px solid var(--green)30}
  .fb-pub{background:rgba(0,229,255,0.1);color:var(--cyan);border:1px solid var(--cyan)30}
  .fb-build{background:rgba(139,92,246,0.1);color:var(--purple);border:1px solid var(--purple)30}

  /* MISSION */
  .mission-lines{padding:8px 0}
  .mission-line{display:flex;gap:12px;margin:6px 0;font-size:12px}
  .ml-bracket{color:var(--gray)}
  .ml-key{color:var(--cyan);min-width:120px}
  .ml-val{color:var(--green)}

  /* SECRET TERMINAL */
  #secret-overlay{
    display:none;
    position:fixed;
    inset:0;
    background:rgba(0,0,0,0.85);
    z-index:10000;
    align-items:center;
    justify-content:center;
  }
  #secret-overlay.active{display:flex}
  #secret-term{
    width:560px;
    max-width:90vw;
    background:#020810;
    border:1px solid var(--green);
    border-radius:4px;
    box-shadow:0 0 60px rgba(0,255,156,0.15);
    overflow:hidden;
  }
  #secret-input{
    background:transparent;
    border:none;
    outline:none;
    color:var(--green);
    font-family:var(--mono);
    font-size:13px;
    width:100%;
    caret-color:var(--green);
  }
  #secret-output{color:var(--gray);font-size:12px;margin-top:8px;line-height:1.8;min-height:80px}
  .shortcut-hint{
    position:fixed;
    bottom:20px;
    right:20px;
    background:#050d1a;
    border:1px solid var(--border);
    padding:8px 14px;
    border-radius:4px;
    font-size:11px;
    color:var(--gray);
    z-index:100;
    animation:fadeHint 4s ease forwards;
  }
  @keyframes fadeHint{0%,70%{opacity:1}100%{opacity:0;pointer-events:none}}

  /* HERO BOOT LOGS */
  .boot-line{color:var(--gray);font-size:12px;opacity:0;animation:bootReveal 0.3s ease forwards}
  @keyframes bootReveal{to{opacity:1}}
  .boot-ok{color:var(--green)}
  .boot-status{color:var(--cyan)}

  /* SECTION HEADER */
  .sec-head{
    display:flex;
    align-items:center;
    gap:12px;
    margin:40px 0 4px;
    font-size:11px;
    color:var(--gray);
    text-transform:uppercase;
    letter-spacing:3px;
  }
  .sec-head::after{content:'';flex:1;height:1px;background:var(--border)}
  .sec-num{color:var(--purple)}

  /* NAME HERO */
  .hero-name{
    font-size:clamp(36px,7vw,64px);
    font-weight:700;
    color:var(--green);
    letter-spacing:-2px;
    line-height:1;
    text-shadow:0 0 40px rgba(0,255,156,0.3);
  }
  .hero-role{font-size:13px;color:var(--cyan);margin:8px 0 16px;letter-spacing:2px}
  .social-links{display:flex;flex-wrap:wrap;gap:8px;margin-top:12px}
  .social-cmd{
    font-size:11px;
    color:var(--gray);
    border:1px solid var(--border);
    padding:5px 12px;
    border-radius:2px;
    cursor:pointer;
    transition:all 0.2s;
    text-decoration:none;
    display:inline-block;
  }
  .social-cmd:hover{color:var(--green);border-color:var(--green);box-shadow:0 0 12px rgba(0,255,156,0.15)}
  .social-cmd .arrow{color:var(--green)}

  /* LEETCODE */
  .lc-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin:12px 0}
  .lc-item{background:#050d1a;border:1px solid var(--border);border-radius:4px;padding:10px;text-align:center}
  .lc-num{font-size:22px;font-weight:700}
  .lc-label{font-size:10px;color:var(--gray);letter-spacing:1px}
  .lc-easy{color:#00FF9C}
  .lc-med{color:#FFBD2E}
  .lc-hard{color:#FF3B3B}

  /* STATUS INDICATOR */
  .status-dot{display:inline-block;width:6px;height:6px;border-radius:50%;background:var(--green);margin-right:6px;animation:statusPulse 2s ease-in-out infinite}
  @keyframes statusPulse{0%,100%{box-shadow:0 0 0 0 rgba(0,255,156,0.4)}50%{box-shadow:0 0 0 6px rgba(0,255,156,0)}}

  @media(max-width:600px){
    .repo-list{grid-template-columns:1fr}
    .lc-grid{grid-template-columns:repeat(2,1fr)}
    .metrics-grid{grid-template-columns:repeat(2,1fr)}
  }
</style>
</head>
<body>

<!-- SHORTCUT HINT -->
<div class="shortcut-hint">Press <strong style="color:var(--green)">~</strong> to open terminal</div>

<!-- ═══════════════════════════════════════
  SECTION 1: BOOT LOADER HERO
════════════════════════════════════════ -->
<div class="container" style="padding-top:60px">
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div>
      <div class="dot dot-y"></div>
      <div class="dot dot-g"></div>
      <span class="terminal-title">lavish@portfolio ~ boot.sh</span>
    </div>
    <div class="terminal-body" id="boot-area">
      <!-- Boot lines injected by JS -->
    </div>
  </div>

  <!-- HERO NAME -->
  <div id="hero-reveal" style="display:none;padding:32px 0 8px">
    <div class="prompt"><span class="user">lavish</span><span style="color:var(--gray)">@</span><span class="host">portfolio</span><span style="color:var(--gray)">:</span><span class="path">~</span><span style="color:var(--gray)">$</span> <span class="cmd">whoami</span></div>
    <div style="margin-top:20px">
      <div style="font-size:11px;color:var(--gray);letter-spacing:3px;text-transform:uppercase;margin-bottom:6px">//&nbsp;&nbsp;IDENTITY&nbsp;CONFIRMED</div>
      <div class="glitch hero-name" data-text="LAVISH">LAVISH</div>
      <div class="hero-role"><span class="status-dot"></span>ISE Student &nbsp;/&nbsp; Full-Stack Dev &nbsp;/&nbsp; AI Enthusiast &nbsp;/&nbsp; IEEE Chair</div>
      <div style="font-size:12px;color:var(--gray);margin-bottom:16px">
        > SJB Institute of Technology, Bengaluru &nbsp;·&nbsp; Pre-Final Year &nbsp;·&nbsp; 2023–2027
      </div>
      <div class="social-links">
        <a class="social-cmd" href="https://github.com/Lavi2605" target="_blank"><span class="arrow">&gt;</span> open github</a>
        <a class="social-cmd" href="https://linkedin.com/in/lavisha176b9325" target="_blank"><span class="arrow">&gt;</span> open linkedin</a>
        <a class="social-cmd" href="mailto:lavish.dev.work@gmail.com"><span class="arrow">&gt;</span> send mail</a>
        <a class="social-cmd" href="https://discord.gg/3egYKyjkr" target="_blank"><span class="arrow">&gt;</span> open discord</a>
        <a class="social-cmd" href="https://instagram.com/tg_lavish_12121" target="_blank"><span class="arrow">&gt;</span> open instagram</a>
      </div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 2: LIVE SYSTEM STATUS
════════════════════════════════════════ -->
<div class="container" id="s2" style="display:none">
  <div class="sec-head"><span class="sec-num">[02]</span> SYSTEM STATUS</div>
  <div class="metrics-grid">
    <div class="metric-card">
      <div class="metric-prefix">SYS.COUNT</div>
      <div class="metric-num" id="cnt-projects">00</div>
      <div class="metric-label">Projects Deployed</div>
    </div>
    <div class="metric-card">
      <div class="metric-prefix">SYS.EVENT</div>
      <div class="metric-num" id="cnt-hack">00</div>
      <div class="metric-label">Hackathons Entered</div>
    </div>
    <div class="metric-card">
      <div class="metric-prefix">SYS.SOLVE</div>
      <div class="metric-num" id="cnt-lc">000</div>
      <div class="metric-label">Problems Solved</div>
    </div>
    <div class="metric-card">
      <div class="metric-prefix">SYS.UPTIME</div>
      <div class="metric-num" id="cnt-years">0</div>
      <div class="metric-label">Years Coding</div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 3: ABOUT LOG FILE
════════════════════════════════════════ -->
<div class="container" id="s3" style="display:none">
  <div class="sec-head"><span class="sec-num">[03]</span> ABOUT.LOG</div>
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="terminal-title">cat /var/log/identity.log</span>
    </div>
    <div class="terminal-body" style="font-size:12px;line-height:2">
      <div><span style="color:var(--cyan)">[BOOT]</span> &nbsp;&nbsp;&nbsp;System initialized — Lavish OS v3.0 active</div>
      <div><span style="color:var(--green)">[INFO]</span> &nbsp;&nbsp;&nbsp;Pre-Final Year Information Science Engineering Student @ SJBIT</div>
      <div><span style="color:var(--green)">[INFO]</span> &nbsp;&nbsp;&nbsp;Chairperson, IEEE Computer Society — organizing, leading, building</div>
      <div><span style="color:var(--green)">[INFO]</span> &nbsp;&nbsp;&nbsp;Building AI + Full-Stack systems that deliver real-world impact</div>
      <div><span style="color:var(--cyan)">[FOCUS]</span> &nbsp;&nbsp;Agentic AI, LLMs, Scalable Cloud Systems, Competitive Programming</div>
      <div><span style="color:var(--cyan)">[FOCUS]</span> &nbsp;&nbsp;Research → Published on IEEE &amp; arXiv in 2024</div>
      <div><span style="color:var(--purple)">[CERTS]</span> &nbsp;&nbsp;AWS Cloud Essentials · Pandas · Intro to ML · Front-End HTML · Python</div>
      <div><span style="color:var(--green)">[STATUS]</span> &nbsp;<span class="status-dot"></span>Open to internships &amp; collaborations</div>
      <div style="margin-top:8px"><span style="color:var(--gray)">[END]</span> &nbsp;&nbsp;&nbsp;&nbsp;EOF &mdash; lavish.dev.work@gmail.com</div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 4: EXPERIENCE TIMELINE
════════════════════════════════════════ -->
<div class="container" id="s4" style="display:none">
  <div class="sec-head"><span class="sec-num">[04]</span> EXPERIENCE.LOG</div>
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="terminal-title">journalctl --user -f --since="2022-01-01"</span>
    </div>
    <div class="terminal-body">
      <div class="prompt"><span class="user">lavish</span><span style="color:var(--gray)">@sys</span>:~$ <span class="cmd">cat experience.timeline</span></div>
      <div style="margin-top:12px">
        <div class="timeline">
          <div class="tl-item">
            <div class="tl-dot"></div>
            <div class="tl-date">SEP 2024 — NOV 2024</div>
            <div class="tl-title">Data Analyst Intern &mdash; Unified Mentor</div>
            <div class="tl-desc">Bengaluru, KA · 3 months · Hands-on analytics, data pipelines, insights extraction. Built dashboards and explored data-driven decision systems.</div>
          </div>
          <div class="tl-item">
            <div class="tl-dot" style="background:var(--cyan);box-shadow:0 0 8px var(--cyan)"></div>
            <div class="tl-date">SEP 2023 — PRESENT</div>
            <div class="tl-title">Chairperson — IEEE Computer Society, SJBIT</div>
            <div class="tl-desc">Leading the chapter: organizing tech events, workshops, and research initiatives. Building community + driving innovation among peers.</div>
          </div>
          <div class="tl-item">
            <div class="tl-dot" style="background:var(--purple);box-shadow:0 0 8px var(--purple)"></div>
            <div class="tl-date">SEP 2023 — JUL 2027</div>
            <div class="tl-title">B.E. Information Technology — SJBIT</div>
            <div class="tl-desc">Full-stack development, AI/ML coursework, competitive programming, research publications.</div>
          </div>
          <div class="tl-item">
            <div class="tl-dot" style="background:var(--gray);box-shadow:0 0 8px var(--gray)"></div>
            <div class="tl-date">APR 2022 — MAY 2023</div>
            <div class="tl-title">PUC — Computer Science · KV No.2, Jalahalli</div>
            <div class="tl-desc">Foundation in CS fundamentals, mathematics, electronics. Ranked state-level in competitive exams.</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 5: SKILLS TERMINAL
════════════════════════════════════════ -->
<div class="container" id="s5" style="display:none">
  <div class="sec-head"><span class="sec-num">[05]</span> SKILLS.SH</div>
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="terminal-title">skills --verbose --all</span>
    </div>
    <div class="terminal-body" style="font-size:12px">
      <div class="prompt"><span class="user">lavish</span><span style="color:var(--gray)">@sys</span>:~$ <span class="cmd">./load_languages</span></div>
      <div style="margin:12px 0">
        <div class="prog-wrap"><span class="prog-label">Python</span><div class="prog-bar"><div class="prog-fill" style="width:90%"></div></div><span class="prog-pct">90%</span></div>
        <div class="prog-wrap"><span class="prog-label">C++</span><div class="prog-bar"><div class="prog-fill" style="width:82%"></div></div><span class="prog-pct">82%</span></div>
        <div class="prog-wrap"><span class="prog-label">JavaScript</span><div class="prog-bar"><div class="prog-fill" style="width:78%"></div></div><span class="prog-pct">78%</span></div>
        <div class="prog-wrap"><span class="prog-label">TypeScript</span><div class="prog-bar"><div class="prog-fill" style="width:72%"></div></div><span class="prog-pct">72%</span></div>
        <div class="prog-wrap"><span class="prog-label">Java</span><div class="prog-bar"><div class="prog-fill" style="width:70%"></div></div><span class="prog-pct">70%</span></div>
        <div class="prog-wrap"><span class="prog-label">C</span><div class="prog-bar"><div class="prog-fill" style="width:75%"></div></div><span class="prog-pct">75%</span></div>
      </div>
      <div class="prompt"><span class="user">lavish</span><span style="color:var(--gray)">@sys</span>:~$ <span class="cmd">./load_frameworks</span></div>
      <div style="margin:10px 0;line-height:2">
        <span style="color:var(--cyan)">Frontend&nbsp;&nbsp;&nbsp;→</span> <span style="color:var(--gray)">React &nbsp;·&nbsp; Next.js &nbsp;·&nbsp; Angular &nbsp;·&nbsp; TailwindCSS &nbsp;·&nbsp; HTML5 &nbsp;·&nbsp; CSS3</span><br>
        <span style="color:var(--cyan)">Backend &nbsp;&nbsp;&nbsp;→</span> <span style="color:var(--gray)">Node.js &nbsp;·&nbsp; Express &nbsp;·&nbsp; FastAPI &nbsp;·&nbsp; Flask &nbsp;·&nbsp; GraphQL &nbsp;·&nbsp; Socket.io</span><br>
        <span style="color:var(--cyan)">Databases &nbsp;→</span> <span style="color:var(--gray)">MongoDB &nbsp;·&nbsp; PostgreSQL &nbsp;·&nbsp; MySQL &nbsp;·&nbsp; Neo4j &nbsp;·&nbsp; Firebase &nbsp;·&nbsp; SQLite</span><br>
        <span style="color:var(--cyan)">Cloud/Dev &nbsp;→</span> <span style="color:var(--gray)">AWS &nbsp;·&nbsp; Git &nbsp;·&nbsp; GitHub Actions &nbsp;·&nbsp; Postman &nbsp;·&nbsp; Streamlit</span>
      </div>
      <div class="prompt"><span class="user">lavish</span><span style="color:var(--gray)">@sys</span>:~$ <span class="cmd">./load_ai_stack</span></div>
      <div style="margin:10px 0;line-height:2">
        <span style="color:var(--purple)">ML Libs &nbsp;&nbsp;&nbsp;→</span> <span style="color:var(--gray)">TensorFlow &nbsp;·&nbsp; Keras &nbsp;·&nbsp; PyTorch &nbsp;·&nbsp; scikit-learn &nbsp;·&nbsp; SciPy</span><br>
        <span style="color:var(--purple)">Data &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;→</span> <span style="color:var(--gray)">Pandas &nbsp;·&nbsp; NumPy &nbsp;·&nbsp; Matplotlib &nbsp;·&nbsp; Plotly &nbsp;·&nbsp; BERTopic</span><br>
        <span style="color:var(--purple)">Advanced &nbsp;&nbsp;→</span> <span style="color:var(--gray)">Transformers &nbsp;·&nbsp; RAG &nbsp;·&nbsp; Vector DB &nbsp;·&nbsp; Agentic AI &nbsp;·&nbsp; LLMs</span>
      </div>
      <div class="prompt" style="margin-top:4px"><span style="color:var(--green)">[LOAD COMPLETE]</span> <span style="color:var(--gray)">All modules initialized successfully. <span class="blink">█</span></span></div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 6: LEETCODE METRICS
════════════════════════════════════════ -->
<div class="container" id="s6" style="display:none">
  <div class="sec-head"><span class="sec-num">[06]</span> COMPETITIVE.STATS</div>
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="terminal-title">leetcode-cli --stats --user=Lavish</span>
    </div>
    <div class="terminal-body" style="font-size:12px">
      <div class="prompt"><span class="user">lavish</span><span style="color:var(--gray)">@sys</span>:~$ <span class="cmd">lc stats --verbose</span></div>
      <div style="color:var(--gray);margin:8px 0">Loading competitive programming stats<span class="blink">...</span></div>
      <div style="color:var(--green);margin-bottom:16px">[✓] Data fetched successfully</div>
      <div class="lc-grid">
        <div class="lc-item" style="grid-column:1/-1;border-color:var(--green)40">
          <div style="font-size:11px;color:var(--gray);letter-spacing:2px">TOTAL SOLVED</div>
          <div style="font-size:40px;font-weight:700;color:var(--green)">512</div>
          <div style="font-size:10px;color:var(--gray)">Problems</div>
        </div>
        <div class="lc-item"><div class="lc-num lc-easy">177</div><div class="lc-label">Easy</div></div>
        <div class="lc-item"><div class="lc-num lc-med">288</div><div class="lc-label">Medium</div></div>
        <div class="lc-item"><div class="lc-num lc-hard">47</div><div class="lc-label">Hard</div></div>
      </div>
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:10px">
        <div class="lc-item"><div style="font-size:10px;color:var(--gray);letter-spacing:1px">CONTEST RATING</div><div class="lc-num" style="color:var(--cyan)">1729</div></div>
        <div class="lc-item"><div style="font-size:10px;color:var(--gray);letter-spacing:1px">GLOBAL RANK</div><div class="lc-num" style="color:var(--purple);font-size:20px">#97,843</div></div>
      </div>
      <div style="margin-top:12px;padding:10px;border:1px solid var(--border);border-radius:4px;font-size:11px;color:var(--gray)">
        <span style="color:var(--green)">TOP</span> &nbsp;11.43% globally &nbsp;·&nbsp; Contest participant &nbsp;·&nbsp; Active daily solver
      </div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 7: GITHUB REPOSITORY SCANNER
════════════════════════════════════════ -->
<div class="container" id="s7" style="display:none">
  <div class="sec-head"><span class="sec-num">[07]</span> REPO.SCAN</div>
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="terminal-title">github-cli repo scan --user=Lavi2605 --pinned</span>
    </div>
    <div class="terminal-body" style="font-size:12px">
      <div class="prompt"><span class="user">lavish</span><span style="color:var(--gray)">@sys</span>:~$ <span class="cmd">gh repo list --pinned Lavi2605</span></div>
      <div style="color:var(--gray);margin:6px 0">
        <span style="color:var(--cyan)">[→]</span> Connecting to github.com<span class="blink">...</span><br>
        <span style="color:var(--cyan)">[→]</span> Authenticating session<span class="blink">...</span><br>
        <span style="color:var(--green)">[✓]</span> Connected · Scanning pinned repositories<br>
        <span style="color:var(--green)">[✓]</span> 6 repositories found
      </div>
      <div class="repo-list" style="margin-top:14px">
        <div class="repo-card">
          <div class="repo-badge">Jupyter</div>
          <div class="repo-name">TopicModeling</div>
          <div class="repo-desc">BERTopic for topic modeling on LMSYS chat preference datasets. NLP + unsupervised ML research pipeline.</div>
          <div class="repo-lang">Jupyter Notebook</div>
        </div>
        <div class="repo-card">
          <div class="repo-badge">TypeScript</div>
          <div class="repo-name">vaidyaCare</div>
          <div class="repo-desc">Healthcare platform — patient management, appointment booking, AI-assisted diagnostics. Production-grade full-stack app.</div>
          <div class="repo-lang">TypeScript</div>
        </div>
        <div class="repo-card">
          <div class="repo-badge">JavaScript</div>
          <div class="repo-name">ZeroDa</div>
          <div class="repo-desc">Full-stack application built with JS. Scalable architecture, modern tooling, real-world use-case oriented.</div>
          <div class="repo-lang">JavaScript</div>
        </div>
        <div class="repo-card">
          <div class="repo-badge">Jupyter</div>
          <div class="repo-name">Food101usingVGG16</div>
          <div class="repo-desc">Food-101 dataset solved via custom VGG-16 replication. Deep learning, transfer learning, computer vision pipeline.</div>
          <div class="repo-lang">Jupyter Notebook</div>
        </div>
        <div class="repo-card">
          <div class="repo-badge">Jupyter</div>
          <div class="repo-name">AIML</div>
          <div class="repo-desc">Personal AI/ML learning repository. Code-as-notes format covering classical ML to deep learning fundamentals.</div>
          <div class="repo-lang">Jupyter Notebook</div>
        </div>
        <div class="repo-card">
          <div class="repo-badge">Jupyter</div>
          <div class="repo-name">HumanPref</div>
          <div class="repo-desc">Human preference modeling experiments — RLHF adjacent research, preference datasets, comparative analysis.</div>
          <div class="repo-lang">Jupyter Notebook</div>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 8: NETWORK ACTIVITY FEED
════════════════════════════════════════ -->
<div class="container" id="s8" style="display:none">
  <div class="sec-head"><span class="sec-num">[08]</span> NETWORK.ACTIVITY</div>
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="terminal-title">tail -f /var/log/achievements.log</span>
    </div>
    <div class="terminal-body" style="font-size:12px">
      <div class="feed-item">
        <span class="feed-year" style="color:var(--green)">[2025]</span>
        <span class="feed-text">Hackathon participation &amp; winning streak — multiple events<span class="feed-badge fb-win">WIN</span></span>
      </div>
      <div class="feed-item">
        <span class="feed-year" style="color:var(--cyan)">[2024]</span>
        <span class="feed-text">IEEE Publication — peer-reviewed research paper accepted &amp; published<span class="feed-badge fb-pub">PUB</span></span>
      </div>
      <div class="feed-item">
        <span class="feed-year" style="color:var(--cyan)">[2024]</span>
        <span class="feed-text">arXiv Preprint — research indexed on arXiv for open access<span class="feed-badge fb-pub">ARXIV</span></span>
      </div>
      <div class="feed-item">
        <span class="feed-year" style="color:var(--cyan)">[2024]</span>
        <span class="feed-text">Data Analytics Internship @Unified Mentor — completed 2-month program<span class="feed-badge fb-build">INTERN</span></span>
      </div>
      <div class="feed-item">
        <span class="feed-year" style="color:var(--purple)">[2023]</span>
        <span class="feed-text">Built &amp; deployed production systems — full-stack, AI/ML, cloud-integrated<span class="feed-badge fb-build">BUILD</span></span>
      </div>
      <div class="feed-item">
        <span class="feed-year" style="color:var(--purple)">[2023]</span>
        <span class="feed-text">Elected Chairperson — IEEE Computer Society Chapter, SJBIT<span class="feed-badge fb-win">CHAIR</span></span>
      </div>
      <div style="padding:10px 0;color:var(--gray);font-size:11px">
        <span class="blink">█</span> Live feed — new events streaming in...
      </div>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 9: MISSION FILE
════════════════════════════════════════ -->
<div class="container" id="s9" style="display:none">
  <div class="sec-head"><span class="sec-num">[09]</span> MISSION.CLASSIFIED</div>
  <div class="terminal">
    <div class="terminal-bar">
      <div class="dot dot-r"></div><div class="dot dot-y"></div><div class="dot dot-g"></div>
      <span class="terminal-title">cat /etc/mission_statement.conf [CLASSIFIED]</span>
    </div>
    <div class="terminal-body" style="font-size:12px">
      <div style="border:1px solid var(--red)30;padding:12px;border-radius:4px;margin-bottom:16px">
        <div style="color:var(--red);font-size:10px;letter-spacing:3px;margin-bottom:8px">⚠ CLASSIFIED — AUTHORIZED ACCESS ONLY</div>
        <div style="color:var(--gray)">File: MISSION_STATEMENT.CONF &nbsp;·&nbsp; Classification: INTERNAL &nbsp;·&nbsp; Clearance: L5</div>
      </div>
      <div class="mission-lines">
        <div class="mission-line"><span class="ml-bracket">[</span><span class="ml-key">OBJECTIVE</span><span class="ml-bracket">]</span><span class="ml-val" style="margin-left:12px">Build systems that matter — scalable, intelligent, impactful</span></div>
        <div class="mission-line"><span class="ml-bracket">[</span><span class="ml-key">MOTIVATION</span><span class="ml-bracket">]</span><span class="ml-val" style="margin-left:12px">Curiosity + obsession with solving hard engineering problems</span></div>
        <div class="mission-line"><span class="ml-bracket">[</span><span class="ml-key">PHILOSOPHY</span><span class="ml-bracket">]</span><span class="ml-val" style="margin-left:12px">Code is a superpower — deploy it responsibly, deploy it boldly</span></div>
        <div class="mission-line"><span class="ml-bracket">[</span><span class="ml-key">NORTH_STAR</span><span class="ml-bracket">]</span><span class="ml-val" style="margin-left:12px">Agentic AI systems &amp; full-stack platforms serving real humans</span></div>
        <div class="mission-line"><span class="ml-bracket">[</span><span class="ml-key">OPEN_TO</span><span class="ml-bracket">]</span><span class="ml-val" style="margin-left:12px">Internships · Research Collab · Open Source · Hackathons</span></div>
        <div class="mission-line"><span class="ml-bracket">[</span><span class="ml-key">CONTACT</span><span class="ml-bracket">]</span><span class="ml-val" style="margin-left:12px">lavish.dev.work@gmail.com</span></div>
      </div>
      <div style="margin-top:16px;padding:12px;background:#050d1a;border-left:2px solid var(--green);font-size:12px;color:var(--gray);line-height:1.8">
        "I believe the best engineers aren't just coders — they're <span style="color:var(--green)">problem architects</span>.<br>
        I build things that <span style="color:var(--cyan)">scale</span>, things that <span style="color:var(--purple)">think</span>, and things that <span style="color:var(--green)">matter</span>."
      </div>
    </div>
  </div>
</div>

<!-- FOOTER -->
<div class="container" id="s-footer" style="display:none;padding-bottom:80px">
  <div style="text-align:center;padding:40px 0;border-top:1px solid var(--border);margin-top:20px">
    <div style="font-size:10px;color:var(--gray);letter-spacing:3px">LAVISH OS v3.0 · PORTFOLIO TERMINAL · SJBIT · BENGALURU</div>
    <div style="font-size:10px;color:var(--gray);margin-top:6px">Press <span style="color:var(--green)">~</span> for interactive terminal &nbsp;·&nbsp; <a href="mailto:lavish.dev.work@gmail.com" style="color:var(--cyan);text-decoration:none">lavish.dev.work@gmail.com</a></div>
    <div style="font-size:10px;color:var(--gray);margin-top:4px;opacity:0.5"><span class="blink">█</span> System online</div>
  </div>
</div>

<!-- ═══════════════════════════════════════
  SECTION 10: SECRET TERMINAL
════════════════════════════════════════ -->
<div id="secret-overlay">
  <div id="secret-term">
    <div class="terminal-bar">
      <div class="dot dot-r" id="close-secret" style="cursor:pointer" title="close"></div>
      <div class="dot dot-y"></div>
      <div class="dot dot-g"></div>
      <span class="terminal-title">lavish@portfolio ~ /secret/terminal</span>
    </div>
    <div style="padding:16px;font-size:12px">
      <div style="color:var(--gray);margin-bottom:8px">Lavish Terminal v3.0 &nbsp;·&nbsp; Type <span style="color:var(--green)">help</span> for commands &nbsp;·&nbsp; Press ESC to close</div>
      <div id="secret-output"></div>
      <div style="display:flex;align-items:center;gap:6px;margin-top:8px">
        <span style="color:var(--green)">lavish@portfolio:~$</span>
        <input id="secret-input" type="text" placeholder="type a command..." spellcheck="false" autocomplete="off"/>
      </div>
    </div>
  </div>
</div>

<script>
// ── BOOT SEQUENCE ──────────────────────────────────────────────
const bootLines=[
  {text:'[BIOS] Initializing UEFI firmware interface...',delay:0,cls:'output'},
  {text:'[BIOS] Memory check: 64GB DDR5 — OK',delay:300,cls:'output-g'},
  {text:'[BOOT] Loading GRUB bootloader...',delay:600,cls:'output'},
  {text:'[BOOT] Kernel: lavish-os-v3.0-custom #1 SMP',delay:900,cls:'output'},
  {text:'[INIT] Starting systemd services...',delay:1200,cls:'output'},
  {text:'[NET]  Connecting to api.github.com... ',delay:1500,cls:'output'},
  {text:'[NET]  ✓ Connection established — 12ms',delay:1900,cls:'output-g'},
  {text:'[MOD]  Loading portfolio modules...',delay:2200,cls:'output'},
  {text:'[MOD]  ✓ full-stack.mod loaded',delay:2500,cls:'output-g'},
  {text:'[MOD]  ✓ ai-ml.mod loaded',delay:2700,cls:'output-g'},
  {text:'[MOD]  ✓ leetcode.mod loaded',delay:2900,cls:'output-g'},
  {text:'[MOD]  ✓ research.mod loaded',delay:3100,cls:'output-g'},
  {text:'[SEC]  Verifying identity signature...',delay:3400,cls:'output'},
  {text:'[SEC]  ✓ Identity confirmed: LAVISH',delay:3700,cls:'output-c'},
  {text:'',delay:4000,cls:'output'},
  {text:'████████████████████████ ACCESS GRANTED ████████████████████████',delay:4200,cls:'output-g'},
];

const bootArea=document.getElementById('boot-area');
let totalBootTime=0;
bootLines.forEach(l=>{
  setTimeout(()=>{
    const div=document.createElement('div');
    div.className=l.cls;
    div.style.fontSize='12px';
    div.style.animation='bootReveal 0.2s ease forwards';
    div.textContent=l.text||'\u00a0';
    bootArea.appendChild(div);
    bootArea.scrollTop=bootArea.scrollHeight;
  },l.delay);
  totalBootTime=Math.max(totalBootTime,l.delay);
});

// Reveal sections sequentially after boot
const sections=[
  {id:'hero-reveal',show:true,t:totalBootTime+400},
  {id:'s2',t:totalBootTime+900},
  {id:'s3',t:totalBootTime+1200},
  {id:'s4',t:totalBootTime+1500},
  {id:'s5',t:totalBootTime+1800},
  {id:'s6',t:totalBootTime+2100},
  {id:'s7',t:totalBootTime+2400},
  {id:'s8',t:totalBootTime+2700},
  {id:'s9',t:totalBootTime+3000},
  {id:'s-footer',t:totalBootTime+3200},
];
sections.forEach(s=>{
  setTimeout(()=>{
    const el=document.getElementById(s.id);
    if(el){el.style.display='block';el.style.animation='bootReveal 0.5s ease forwards';}
    if(s.id==='s2') startCounters();
  },s.t);
});

// ── COUNTERS ──────────────────────────────────────────────────
function animCount(id,target,dur,prefix=''){
  const el=document.getElementById(id);
  if(!el)return;
  const start=Date.now();
  const tick=()=>{
    const prog=Math.min((Date.now()-start)/dur,1);
    const val=Math.floor(prog*target);
    el.textContent=prefix+String(val).padStart(String(target).length,'0');
    if(prog<1)requestAnimationFrame(tick);
    else el.textContent=prefix+target;
  };
  requestAnimationFrame(tick);
}
function startCounters(){
  setTimeout(()=>{
    animCount('cnt-projects',12,1200,'');
    animCount('cnt-hack',6,1000,'');
    animCount('cnt-lc',512,1500,'');
    animCount('cnt-years',4,800,'');
  },200);
}

// ── SECRET TERMINAL ────────────────────────────────────────────
const overlay=document.getElementById('secret-overlay');
const input=document.getElementById('secret-input');
const output=document.getElementById('secret-output');
const cmds={
  help:`<span style="color:var(--cyan)">Available commands:</span><br>
  <span style="color:var(--green)">about</span> &nbsp;&nbsp;&nbsp;&nbsp;→ Who is Lavish?<br>
  <span style="color:var(--green)">projects</span> &nbsp;→ List top projects<br>
  <span style="color:var(--green)">contact</span> &nbsp;&nbsp;→ Get in touch<br>
  <span style="color:var(--green)">skills</span> &nbsp;&nbsp;&nbsp;→ Tech stack overview<br>
  <span style="color:var(--green)">clear</span> &nbsp;&nbsp;&nbsp;&nbsp;→ Clear terminal<br>
  <span style="color:var(--green)">exit</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;→ Close terminal`,
  about:`<span style="color:var(--green)">[INFO]</span> ISE Student @ SJBIT, Bengaluru (2023-2027)<br>
  <span style="color:var(--green)">[INFO]</span> Chairperson — IEEE Computer Society<br>
  <span style="color:var(--green)">[INFO]</span> Data Analyst Intern @Unified Mentor (2024)<br>
  <span style="color:var(--green)">[INFO]</span> IEEE + arXiv published researcher<br>
  <span style="color:var(--cyan)">[FOCUS]</span> Full-Stack · Agentic AI · Scalable Systems`,
  projects:`<span style="color:var(--cyan)">[01]</span> TopicModeling — BERTopic on LMSYS datasets<br>
  <span style="color:var(--cyan)">[02]</span> vaidyaCare — Healthcare platform (TypeScript)<br>
  <span style="color:var(--cyan)">[03]</span> ZeroDa — Full-stack JS application<br>
  <span style="color:var(--cyan)">[04]</span> Food101 VGG16 — Computer vision pipeline<br>
  <span style="color:var(--cyan)">[05]</span> AIML — Personal ML learning repository<br>
  → github.com/Lavi2605`,
  contact:`<span style="color:var(--green)">Email &nbsp;&nbsp;&nbsp;:</span> lavish.dev.work@gmail.com<br>
  <span style="color:var(--green)">LinkedIn :</span> linkedin.com/in/lavisha176b9325<br>
  <span style="color:var(--green)">GitHub &nbsp;&nbsp;:</span> github.com/Lavi2605<br>
  <span style="color:var(--green)">Discord &nbsp;:</span> discord.gg/3egYKyjkr<br>
  <span style="color:var(--cyan)">[STATUS]</span> Open to opportunities — reach out!`,
  skills:`<span style="color:var(--purple)">Languages :</span> Python · C++ · JS · TS · Java · C<br>
  <span style="color:var(--purple)">Frontend &nbsp;:</span> React · Next.js · Tailwind · Angular<br>
  <span style="color:var(--purple)">Backend &nbsp;&nbsp;:</span> Node · FastAPI · Flask · Express<br>
  <span style="color:var(--purple)">AI/ML &nbsp;&nbsp;&nbsp;&nbsp;:</span> TensorFlow · PyTorch · Transformers · RAG<br>
  <span style="color:var(--purple)">Cloud &nbsp;&nbsp;&nbsp;&nbsp;:</span> AWS · Firebase · MongoDB · PostgreSQL`,
};

function openTerm(){
  overlay.classList.add('active');
  input.focus();
  output.innerHTML='<span style="color:var(--gray)">Terminal ready. Type <span style="color:var(--green)">help</span> to begin.</span>';
}
function closeTerm(){overlay.classList.remove('active')}

document.addEventListener('keydown',e=>{
  if(e.key==='`'||e.key==='~'){
    e.preventDefault();
    overlay.classList.contains('active')?closeTerm():openTerm();
  }
  if(e.key==='Escape')closeTerm();
});
document.getElementById('close-secret').addEventListener('click',closeTerm);
overlay.addEventListener('click',e=>{if(e.target===overlay)closeTerm()});

input.addEventListener('keydown',e=>{
  if(e.key!=='Enter')return;
  const val=input.value.trim().toLowerCase();
  if(!val)return;
  const echo=`<div style="color:var(--green);margin-top:6px">lavish@portfolio:~$ ${val}</div>`;
  if(val==='clear'){output.innerHTML='';input.value='';return;}
  if(val==='exit'){closeTerm();input.value='';return;}
  const resp=cmds[val]||`<span style="color:var(--red)">Command not found: ${val}</span><br><span style="color:var(--gray)">Type <span style="color:var(--green)">help</span> for available commands</span>`;
  output.innerHTML+=echo+`<div style="margin:4px 0 10px;line-height:1.8">${resp}</div>`;
  output.scrollTop=output.scrollHeight;
  input.value='';
});
</script>
</body>
</html>
