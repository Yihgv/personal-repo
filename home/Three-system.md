---
layout: base
title: Three systems
permalink: /three-system
---

## Three systems

<style>
.sys-title { text-align:center; margin: 4px 0 4px; }
.sys-subtle { text-align:center; color:#6b7280; margin: 0 0 14px; }

<style>
/* Local styles just for this page (scoped) */
.sys-grid {
	display: grid;
	grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
	gap: 18px;
	max-width: 880px;
	margin: 8px auto 28px;
}

.sys-card {
	display: block;
	text-decoration: none;
	color: inherit;
	background: #fff;
	border: 1px solid rgba(0,0,0,0.08);
	border-radius: 14px;
	padding: 16px 18px;
	box-shadow: 0 4px 10px rgba(0,0,0,0.05);
	transition: transform .12s ease, box-shadow .2s ease, border-color .2s ease, background-color .2s ease;
}
.sys-card:hover { transform: translateY(-1px); box-shadow: 0 8px 16px rgba(0,0,0,0.08); border-color: rgba(11,130,255,.25); background-color: rgba(255,255,255,0.96); }
.sys-card:active { transform: translateY(0); }
.sys-card:focus { outline: 3px solid rgba(11,130,255,.35); outline-offset: 2px; }

.sys-card-header { display: flex; align-items: center; gap: 10px; margin-bottom: 6px; }
.sys-card-header h3 { margin: 0; font-size: 1.1rem; }
.sys-emoji { display:inline-flex; align-items:center; justify-content:center; width:32px; height:32px; border-radius:8px; background: rgba(11,130,255,.1); }
.sys-caption { margin: 0; color: #4b5563; font-size: .95rem; }

@media (prefers-color-scheme: dark) {
	.sys-card { background: #0f172a; border-color: rgba(255,255,255,0.06); box-shadow: 0 6px 14px rgba(0,0,0,0.3); }
		.sys-caption { color: #9aa7b5; }
}
</style>

	<div class="sys-title"><h2 style="margin:0">Three systems</h2></div>
	<p class="sys-subtle">Choose your platform to view the setup guide.</p>

<div class="sys-grid">
	<a class="sys-card" href="{{ site.baseurl }}/tools/windows-setup" aria-label="Windows setup guide">
		<div class="sys-card-header">
			<span class="sys-emoji" aria-hidden="true">🪟</span>
			<h3>Windows</h3>
		</div>
		<p class="sys-caption">VS Code + GitHub Pages setup on Windows.</p>
	</a>

	<a class="sys-card" href="{{ site.baseurl }}/tools/mac-setup" aria-label="Mac setup guide">
		<div class="sys-card-header">
			<span class="sys-emoji" aria-hidden="true">🍎</span>
			<h3>Mac</h3>
		</div>
		<p class="sys-caption">Developer setup on macOS with VS Code + Git.</p>
	</a>

	<a class="sys-card" href="{{ site.baseurl }}/tools/kasm-setup" aria-label="Kasm setup guide">
		<div class="sys-card-header">
			<span class="sys-emoji" aria-hidden="true">🧊</span>
			<h3>Kasm</h3>
		</div>
		<p class="sys-caption">Use Kasm workspace for a ready-to-code environment.</p>
	</a>
</div>
