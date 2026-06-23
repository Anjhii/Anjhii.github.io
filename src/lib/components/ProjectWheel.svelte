<script>
	import ProjectCard from '$lib/components/ProjectCard.svelte';
	import celerisImage from '$lib/assets/celeris-cover.png';

	const projects = [
		{
			id: 1,
			title: 'I.L.U.M.I.N.A.',
			role: 'Desarrolladora de Visión Artificial e Interacción',
			tags: ['Python', 'C#', 'UDP'],
			desc: [
				'Instalación interactiva donde una marioneta física es rastreada con visón por computador para controlar un entorno en Unity en tiempo real a través de sockets UDP.'
			],
			video: 'https://youtu.be/Q1mgWXF-aTo'
		},
		{
			id: 2,
			title: 'Celeris',
			role: 'Programadora Única de Gameplay y Sistemas',
			tags: ['Unity', 'C#', 'Supabase'],
			desc: [
				'Videojuego móvil 3D de acción donde controlas un droide a través de entornos generados de forma procedural, con un sistema de ranking global integrado a Supabase.'
			],
			video: null,
			image: celerisImage
		},
		{
			id: 3,
			title: 'Unallied',
			role: 'Programadora de Red e Integración',
			tags: ['Unity', 'C#', 'Sockets'],
			desc: [
				"Videojuego multijugador asimétrico P2P (4 vs 1) estilo 'Pacman inverso', con mecánicas de red, gestión de lobbies por IP/puerto."
			],
			video: 'https://youtu.be/FKn3eeHRgAI'
		},
		{
			id: 4,
			title: 'Skyruins',
			role: 'Programadora Única de Gameplay',
			tags: ['Unity', 'C#', 'Android'],
			desc: [
				"Un 'endless climber' para Android donde el jugador debe escalar plataformas dinámicas usando el acelerómetro del dispositivo y gestos táctiles para saltar o activar habilidad del personaje."
			],
			video: 'https://youtube.com/shorts/9D2aO8RIsf8'
		},
		{
			id: 5,
			title: 'Split or Treat',
			role: '3D Generalist & Level Designer',
			tags: ['Unity', 'Level Design'],
			desc: [
				'Videojuego competitivo local en pantalla dividida desarrollado para una Game Jam, enfocado en mecánicas de conquista de territorio y dinámicas interdimensionales.'
			],
			video: 'https://youtu.be/LvcVyZzOQwc',
			link: 'https://nikolash03.itch.io/split-or-trick'
		},
		{
			id: 6,
			title: 'Museo Anime VR',
			role: 'Programadora Principal Unity',
			tags: ['Unity', 'C#', 'VR'],
			desc: [
				"Recorrido inmersivo en Realidad Virtual para móviles optimizado para funcionar mediante 'gaze tracking' (navegación con la mirada libre de mandos) y cinemáticas interactivas."
			],
			video: 'https://youtu.be/2uhVRG3ZX9A'
		}
	];

	let activeIndex = $state(0);
	let wheelLocked = $state(false);

	let visibleProjects = $derived([-1, 0, 1].map((offset) => {
		const index = wrapIndex(activeIndex + offset);

		return {
			project: projects[index],
			offset,
			isActive: offset === 0
		};
	}));

	function wrapIndex(index) {
		return (index + projects.length) % projects.length;
	}

	function rotate(direction) {
		activeIndex = wrapIndex(activeIndex + direction);
	}

	function handleWheel(event) {
		event.preventDefault();

		if (wheelLocked || Math.abs(event.deltaY) < 12) return;

		wheelLocked = true;
		rotate(event.deltaY > 0 ? 1 : -1);

		window.setTimeout(() => {
			wheelLocked = false;
		}, 460);
	}
</script>

<section class="wheel-shell" onwheel={handleWheel}>
	<div class="grid-bg"></div>
	<div class="top-glow"></div>

	<div class="layout">
		<header class="hero">
			<div>
				<p class="eyebrow">Unity Developer Portfolio</p>
				<h1>ANJHI BONILLA</h1>
				<strong>UNITY DEVELOPER | INGENIERA MULTIMEDIA</strong>
				<nav aria-label="Contacto profesional">
					<a href="mailto:ing.anjhibonilla@gmail.com">ing.anjhibonilla@gmail.com</a>
					<a href="https://github.com/Anjhii" target="_blank" rel="noreferrer">GitHub</a>
					<a href="https://www.linkedin.com/in/anjhi-bonilla-63312a34a/" target="_blank" rel="noreferrer">
						LinkedIn
					</a>
				</nav>
			</div>

			<div class="state-panel">
				<button type="button" onclick={() => rotate(-1)} aria-label="Proyecto anterior">Prev</button>
				<strong>Project [{activeIndex + 1}] of {projects.length}</strong>
				<button type="button" onclick={() => rotate(1)} aria-label="Proyecto siguiente">Next</button>
			</div>
		</header>

		<div class="stage">
			<div class="orbit"></div>
			<div class="cards">
				{#each visibleProjects as item (item.project.id)}
					<div
						class:active={item.isActive}
						class="card-slot"
						style={`transform: translate(-50%, -50%) rotateY(${item.offset * -38}deg) translateX(${item.offset * 620}px) translateZ(${item.isActive ? 80 : -160}px) scale(${item.isActive ? 1 : 0.62}); opacity: ${item.isActive ? 1 : 0.24}; filter: saturate(${item.isActive ? 1 : 0.32});`}
					>
						<ProjectCard project={item.project} isActive={item.isActive} />
					</div>
				{/each}
			</div>
		</div>

		<footer class="status">
			<p>Proyecto activo <span>{projects[activeIndex].title}</span></p>
			<p>Scroll o usa PREV / NEXT</p>
		</footer>
	</div>
</section>

<style>
	.wheel-shell {
		position: relative;
		isolation: isolate;
		min-height: 100vh;
		overflow: hidden;
		background: rgb(9 9 11);
		color: rgb(244 244 245);
		padding: 1.35rem 1.5rem;
		font-family:
			Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
	}

	.grid-bg {
		position: absolute;
		inset: 0;
		z-index: -1;
		background-image:
			radial-gradient(circle at 50% 20%, rgb(139 92 246 / 0.18), transparent 34%),
			linear-gradient(rgb(139 92 246 / 0.08) 1px, transparent 1px),
			linear-gradient(90deg, rgb(139 92 246 / 0.08) 1px, transparent 1px);
		background-size: auto, 42px 42px, 42px 42px;
	}

	.top-glow {
		position: absolute;
		inset: 0 0 auto;
		height: 1px;
		background: rgb(139 92 246 / 0.8);
		box-shadow: 0 0 28px rgb(139 92 246 / 0.9);
	}

	.layout {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		gap: 0.9rem;
		width: min(100%, 100rem);
		min-height: calc(100vh - 2.7rem);
		margin: 0 auto;
	}

	.hero,
	.status {
		display: flex;
		flex-wrap: wrap;
		align-items: center;
		justify-content: space-between;
		gap: 1.25rem;
		border-color: rgb(139 92 246 / 0.25);
	}

	.hero {
		display: grid;
		grid-template-columns: minmax(0, 1fr) auto;
		align-items: end;
		border-bottom: 1px solid rgb(139 92 246 / 0.25);
		padding-bottom: 0.9rem;
	}

	.hero .eyebrow,
	.status,
	.state-panel {
		font-family:
			ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, 'Liberation Mono', 'Courier New',
			monospace;
	}

	.hero .eyebrow {
		margin: 0;
		color: rgb(139 92 246);
		font-size: 0.7rem;
		font-weight: 800;
		text-transform: uppercase;
		letter-spacing: 0.34em;
	}

	.hero h1 {
		margin: 0.55rem 0 0;
		color: white;
		font-size: clamp(3rem, 5.8vw, 5.35rem);
		font-weight: 950;
		line-height: 1;
		text-transform: uppercase;
	}

	.hero strong {
		display: block;
		margin-top: 0.55rem;
		color: rgb(196 181 253);
		font-size: clamp(0.85rem, 1.6vw, 1.15rem);
		font-weight: 800;
		letter-spacing: 0.12em;
		text-transform: uppercase;
	}

	.hero nav {
		display: flex;
		flex-wrap: wrap;
		gap: 0.75rem;
		margin-top: 0.8rem;
	}

	.hero a {
		border: 1px solid rgb(139 92 246 / 0.28);
		background: rgb(0 0 0 / 0.28);
		color: rgb(228 228 231);
		padding: 0.45rem 0.65rem;
		font-size: 0.78rem;
		text-decoration: none;
		transition:
			border-color 180ms ease,
			color 180ms ease,
			background 180ms ease;
	}

	.hero a:hover,
	.hero a:focus-visible {
		border-color: rgb(196 181 253);
		background: rgb(139 92 246 / 0.12);
		color: white;
		outline: none;
	}

	.state-panel {
		display: grid;
		grid-template-columns: auto minmax(9rem, 1fr) auto;
		gap: 0.5rem;
		min-width: min(100%, 24rem);
		color: rgb(113 113 122);
		font-size: 0.75rem;
		text-align: center;
		text-transform: uppercase;
	}

	.state-panel button,
	.state-panel strong {
		border: 1px solid rgb(139 92 246 / 0.3);
		padding: 0.5rem 0.75rem;
	}

	.state-panel button {
		background: rgb(0 0 0 / 0.35);
		color: rgb(196 181 253);
		cursor: pointer;
		font: inherit;
		text-transform: uppercase;
		transition:
			border-color 180ms ease,
			background 180ms ease,
			color 180ms ease;
	}

	.state-panel button:hover,
	.state-panel button:focus-visible {
		border-color: rgb(196 181 253);
		background: rgb(139 92 246 / 0.16);
		color: white;
		outline: none;
	}

	.state-panel strong {
		border-color: rgb(139 92 246);
		background: rgb(139 92 246);
		color: rgb(9 9 11);
	}

	.state-panel p {
		grid-column: 1 / -1;
		margin: 0.25rem 0 0;
		color: rgb(161 161 170);
		letter-spacing: 0.16em;
	}

	.stage {
		position: relative;
		display: flex;
		flex: 1;
		align-items: center;
		justify-content: center;
		perspective: 1300px;
		min-height: 0;
	}

	.orbit {
		position: absolute;
		width: min(72vmin, 680px);
		height: min(72vmin, 680px);
		border: 1px solid rgb(139 92 246 / 0.15);
		border-radius: 999px;
		box-shadow: inset 0 0 80px rgb(139 92 246 / 0.08);
		pointer-events: none;
	}

	.cards {
		position: relative;
		width: 100%;
		max-width: 96rem;
		height: clamp(500px, 59vh, 610px);
		transform-style: preserve-3d;
	}

	.card-slot {
		position: absolute;
		top: 50%;
		left: 50%;
		z-index: 10;
		width: min(78vw, 900px);
		transform-style: preserve-3d;
		transition:
			transform 500ms ease,
			opacity 500ms ease,
			filter 500ms ease;
	}

	.card-slot.active {
		z-index: 20;
	}

	.status {
		align-items: center;
		border-top: 1px solid rgb(139 92 246 / 0.25);
		padding-top: 0.75rem;
		color: rgb(113 113 122);
		font-size: 0.75rem;
		text-transform: uppercase;
	}

	.status p {
		margin: 0;
	}

	.status span,
	.status p:last-child {
		color: rgb(196 181 253);
	}

	@media (max-width: 760px) {
		.wheel-shell {
			padding: 1.25rem 0.75rem;
		}

		.layout {
			min-height: calc(100vh - 2.5rem);
		}

		.cards {
			height: 520px;
		}

		.card-slot {
			width: min(92vw, 420px);
		}
	}

	@media (max-width: 980px) {
		.hero {
			grid-template-columns: 1fr;
		}

		.state-panel {
			width: min(100%, 28rem);
		}
	}
</style>
