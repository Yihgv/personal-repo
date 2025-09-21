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
.sys-card {
	display: grid;
	grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
	gap: 18px;
	max-width: 880px;
	margin: 8px auto 28px;
}

	box-shadow: 0 4px 10px rgba(0,0,0,0.05);
	transition: transform .2s ease, box-shadow .25s ease, border-color .2s ease, background-color .2s ease, opacity .25s ease;
	text-decoration: none;
.sys-card:hover { transform: translateY(-3px); box-shadow: 0 12px 22px rgba(0,0,0,0.12); border-color: rgba(11,130,255,.25); background-color: rgba(255,255,255,0.98); }
	background: #fff;
	border: 1px solid rgba(0,0,0,0.08);
	border-radius: 14px;
	padding: 16px 18px;
	box-shadow: 0 4px 10px rgba(0,0,0,0.05);
.sys-icon { display:inline-flex; align-items:center; justify-content:center; width:40px; height:40px; border-radius:10px; }
.sys-icon.win { background: linear-gradient(135deg, rgba(0,123,255,0.15), rgba(0,123,255,0.05)); }
.sys-icon.mac { background: linear-gradient(135deg, rgba(255, 45, 85, 0.18), rgba(255, 45, 85, 0.06)); }
.sys-icon.kasm { background: linear-gradient(135deg, rgba(0, 199, 190, 0.16), rgba(0, 199, 190, 0.06)); }
.sys-icon svg { width: 24px; height: 24px; }
}

/* Card body layout */
.sys-body { display:grid; grid-template-columns: 1fr; gap:10px; }
.sys-points { margin: 6px 0 0 0; padding-left: 18px; color:#475569; }
.sys-points li { margin: 4px 0; }
.sys-card:hover { transform: translateY(-1px); box-shadow: 0 8px 16px rgba(0,0,0,0.08); border-color: rgba(11,130,255,.25); background-color: rgba(255,255,255,0.96); }
.sys-card:active { transform: translateY(0); }
.sys-card:focus { outline: 3px solid rgba(11,130,255,.35); outline-offset: 2px; }

.sys-card-header { display: flex; align-items: center; gap: 10px; margin-bottom: 6px; }
	.sys-points { color: #9aa7b5; }
.sys-card-header h3 { margin: 0; font-size: 1.1rem; }

/* Reveal on scroll */
.reveal-init { opacity: 0; transform: translateY(12px); }
.reveal-show { opacity: 1; transform: translateY(0); }
@media (prefers-reduced-motion: reduce) {
	.reveal-init, .reveal-show { opacity: 1; transform: none; }
}
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
		<a class="sys-card reveal-init" href="{{ site.baseurl }}/tools/windows-setup" aria-label="Windows setup guide">
			<div class="sys-card-header">
				<span class="sys-icon win" aria-hidden="true">
					<svg viewBox="0 0 24 24" fill="#0b82ff" xmlns="http://www.w3.org/2000/svg">
						<path d="M3 4.5l8-1.3v8.1H3V4.5zm0 9h8v7.3l-8-1.1V13.5zm10-10l8-1.5v9.4h-8V3.5zm8 10.1V23l-8-1.1v-8.4h8z"/>
					</svg>
				</span>
				<h3>Windows</h3>
			</div>
			<div class="sys-body">
				<p class="sys-caption">VS Code + GitHub Pages setup on Windows.</p>
				<ul class="sys-points">
					<li>Install VS Code and Git</li>
					<li>Set up SSH keys</li>
					<li>Publish via GitHub Pages</li>
				</ul>
			</div>
		</a>

		<a class="sys-card reveal-init" href="{{ site.baseurl }}/tools/mac-setup" aria-label="Mac setup guide">
			<div class="sys-card-header">
				<span class="sys-icon mac" aria-hidden="true">
					<svg viewBox="0 0 24 24" fill="#ff2d55" xmlns="http://www.w3.org/2000/svg">
						<path d="M17.5 13.6c0 3.4 3 4.6 3 4.6s-2.3 6.6-6.7 6.6c-1.8 0-3.1-1.2-5-1.2-2 0-3.5 1.2-5 1.2C-1 24.8 1.4 18 1.4 18S3.7 16.8 3.7 13.4c0-3-2.7-4.6-2.7-4.6s2-3.1 5.1-3.1c2 0 3.3 1.1 5 1.1 1.6 0 2.9-1.1 5-1.1 3.1 0 5.2 3 5.2 3s-2.8 1.6-2.8 4.9z"/>
					</svg>
				</span>
				<h3>Mac</h3>
			</div>
			<div class="sys-body">
				<p class="sys-caption">Developer setup on macOS with VS Code + Git.</p>
				<ul class="sys-points">
					<li>Install Homebrew</li>
					<li>Set up VS Code + Git</li>
					<li>Run basic verifications</li>
				</ul>
			</div>
		</a>

		<a class="sys-card reveal-init" href="{{ site.baseurl }}/tools/kasm-setup" aria-label="Kasm setup guide">
			<div class="sys-card-header">
				<span class="sys-icon kasm" aria-hidden="true">
					<svg viewBox="0 0 24 24" fill="#00c7be" xmlns="http://www.w3.org/2000/svg">
						<path d="M12 2l8 4.5v9L12 20.9 4 15.5v-9L12 2zm0 2.3L6 7v6.6l6 4.1 6-4.1V7l-6-2.7z"/>
					</svg>
				</span>
				<h3>Kasm</h3>
			</div>
			<div class="sys-body">
				<p class="sys-caption">Use Kasm workspace for a ready-to-code environment.</p>
				<ul class="sys-points">
					<li>Launch Linux in browser</li>
					<li>Access preinstalled tools</li>
					<li>Code from any device</li>
				</ul>
			</div>
		</a>
</div>

	<script>
	// Reveal-on-scroll for cards
	(function(){
		var cards = document.querySelectorAll('.sys-card.reveal-init');
		if (!('IntersectionObserver' in window) || cards.length === 0) {
			cards.forEach(function(c){ c.classList.add('reveal-show'); c.classList.remove('reveal-init'); });
			return;
		}
		var io = new IntersectionObserver(function(entries){
			entries.forEach(function(entry){
				if (entry.isIntersecting) {
					entry.target.classList.add('reveal-show');
					entry.target.classList.remove('reveal-init');
					io.unobserve(entry.target);
				}
			});
		}, { threshold: 0.15 });
		cards.forEach(function(c){ io.observe(c); });
	})();
	</script>
