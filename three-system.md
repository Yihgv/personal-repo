---
layout: base
title: Three Systems
permalink: /three-system
---

<style>
  :root{
    --brand:#22a5ff; --text:#111827; --muted:#6b7280; --card:#ffffff;
  }
  .sys-wrap{max-width: 980px; margin: 0 auto; padding: 12px 16px 24px;}
  .sys-title{ text-align:center; margin: 6px 0 2px; }
  .sys-sub{ text-align:center; color: var(--muted); margin: 0 0 18px; }
  .sys-card{ background: var(--card); border: 1px solid rgba(0,0,0,0.08); border-radius: 16px; box-shadow: 0 8px 24px rgba(0,0,0,0.06); padding: 18px; }
  .sys-grid{ display:grid; grid-template-columns: repeat(auto-fit,minmax(220px,1fr)); gap: 14px; margin-top: 8px; }
  .sys a.btn{ display:inline-flex; align-items:center; justify-content:center; gap:8px; padding:12px 14px; border-radius:10px; text-decoration:none; font-weight:700; background:#e6f3ff; color:#0b5394; border:1px solid rgba(0,0,0,0.06); transition: transform .12s ease, box-shadow .12s ease, background-color .12s ease; box-shadow: 0 1px 0 rgba(0,0,0,0.04) inset; }
  .sys a.btn:hover{ background:#d1eaff; transform: translateY(-1px); box-shadow: 0 6px 14px rgba(34,165,255,.25); }
  .sys a.btn:active{ transform: translateY(0); box-shadow: 0 3px 8px rgba(34,165,255,.20); }
  .sys a.btn:focus-visible{ outline: 2px solid var(--brand); outline-offset: 2px; }
  .sys a.btn .chev{ transition: transform .12s ease; }
  .sys a.btn:hover .chev{ transform: translateX(2px); }
  .sys .item{ padding:12px; border:1px solid rgba(0,0,0,0.06); border-radius:12px; }
  .sys .item .title-row{ display:flex; align-items:center; gap:10px; margin:2px 0 8px; }
  .sys .item h3{ margin:0; font-size:18px; }
  .sys .item img.logo{ width:28px; height:28px; object-fit:contain; border-radius:6px; display:block; }
  .sys .item p{ margin:0; color: var(--muted); font-size:14px; }
  @media (prefers-color-scheme: dark){
    :root{ --text:#e5e7eb; --muted:#9ca3af; --card:#0b1220; }
    .sys a.btn{ background:#0b2547; color:#93c5fd; border-color: rgba(255,255,255,0.06); }
    .sys a.btn:hover{ background:#0c2c57; }
    .sys .item{ border-color: rgba(255,255,255,0.06); }
  }
  @media (prefers-reduced-motion: reduce){
    .sys a.btn, .sys a.btn .chev{ transition: none; }
  }
</style>

<div class="sys sys-wrap">
  <h2 class="sys-title">Choose your setup</h2>
  <p class="sys-sub">Pick your platform to view the step-by-step setup guide.</p>

  <div class="sys-card">
    <div class="sys-grid">
      <div class="item">
        <div class="title-row">
          <img class="logo" src="{{ site.baseurl }}/images/Windows11.png" alt="Windows logo">
          <h3>Windows</h3>
        </div>
        <p>VS Code, PATH, Make, and local server.</p>
        <a class="btn" href="{{ site.baseurl }}/tools/windows-setup">Open Windows guide <span class="chev">→</span></a>
      </div>
      <div class="item">
        <div class="title-row">
          <img class="logo" src="{{ site.baseurl }}/images/MacOS.png" alt="Apple macOS logo">
          <h3>macOS</h3>
        </div>
        <p>Command Line Tools, Homebrew, VS Code.</p>
        <a class="btn" href="{{ site.baseurl }}/tools/mac-setup">Open macOS guide <span class="chev">→</span></a>
      </div>
      <div class="item">
        <div class="title-row">
          <img class="logo" src="{{ site.baseurl }}/images/Kasm.png" alt="Kasm logo">
          <h3>Kasm</h3>
        </div>
        <p>Browser-based development environment.</p>
        <a class="btn" href="{{ site.baseurl }}/tools/kasm-setup">Open Kasm guide <span class="chev">→</span></a>
      </div>
    </div>
  </div>
</div>
