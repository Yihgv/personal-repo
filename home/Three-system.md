---
layout: base
title: Three systems
permalink: /three-system
---

## Three systems

<style>
/* Local styles just for this page */
.sys-wrap { display:flex; justify-content:center; gap:14px; flex-wrap:wrap; margin: 12px 0 24px; }
.sys-btn {
	display:inline-block; padding:12px 22px; border-radius:10px; font-weight:800; text-decoration:none;
	color:#fff; background:#0b82ff; border:1px solid rgba(0,0,0,0.1);
	box-shadow: 0 6px 14px rgba(0,0,0,0.15), inset 0 1px 0 rgba(255,255,255,0.08);
	transition: transform .08s ease, filter .15s ease, box-shadow .2s ease;
}
.sys-btn:hover { filter: brightness(1.08); }
.sys-btn:active { transform: translateY(1px); }
.sys-btn:focus { outline: 3px solid rgba(11,130,255,.35); outline-offset: 2px; }
</style>

<div class="sys-wrap">
	<a class="sys-btn" href="{{ site.baseurl }}/tools/windows-setup">Windows</a>
	<a class="sys-btn" href="{{ site.baseurl }}/tools/mac-setup">Mac</a>
	<a class="sys-btn" href="{{ site.baseurl }}/tools/kasm-setup">Kasm</a>
</div>
