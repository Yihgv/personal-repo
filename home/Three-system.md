---
layout: page
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
  .sys a.btn{ display:block; text-align:center; padding:12px; border-radius:10px; text-decoration:none; font-weight:700; background:#e6f3ff; color:#0b5394; border:1px solid rgba(0,0,0,0.06); }
  .sys a.btn:hover{ background:#d1eaff; }
  .sys .item{ padding:12px; border:1px solid rgba(0,0,0,0.06); border-radius:12px; }
  .sys .item h3{ margin:6px 0 6px; font-size:18px; }
  .sys .item p{ margin:0; color: var(--muted); font-size:14px; }
  @media (prefers-color-scheme: dark){
    :root{ --text:#e5e7eb; --muted:#9ca3af; --card:#0b1220; }
    .sys a.btn{ background:#0b2547; color:#93c5fd; border-color: rgba(255,255,255,0.06); }
    .sys a.btn:hover{ background:#0c2c57; }
    .sys .item{ border-color: rgba(255,255,255,0.06); }
  }
</style>

<div class="sys sys-wrap">
  <h2 class="sys-title">Choose your setup</h2>
  <p class="sys-sub">Pick your platform to view the step-by-step setup guide.</p>

  <div class="sys-card">
    <div class="sys-grid">
      <div class="item">
        <h3>Windows</h3>
        <p>VS Code, PATH, Make, and local server.</p>
        <a class="btn" href="{{ site.baseurl }}/tools/windows-setup">Open Windows guide</a>
      </div>
      <div class="item">
        <h3>macOS</h3>
        <p>Command Line Tools, Homebrew, VS Code.</p>
        <a class="btn" href="{{ site.baseurl }}/tools/mac-setup">Open macOS guide</a>
      </div>
      <div class="item">
        <h3>Kasm</h3>
        <p>Browser-based development environment.</p>
        <a class="btn" href="{{ site.baseurl }}/tools/kasm-setup">Open Kasm guide</a>
      </div>
    </div>
  </div>
</div>
