<script>
	let { project, isActive = false } = $props();

	let hasVideo = $derived(Boolean(project?.video));
	let hasImage = $derived(Boolean(project?.image) && !project?.video);
	let embedUrl = $derived(toEmbedUrl(project?.video));
	let embedTitle = $derived(`${project?.title ?? 'Project'} media preview`);
	let isSingleDescription = $derived(project?.desc?.length === 1);

	function toEmbedUrl(url) {
		if (!url) return null;

		try {
			const parsedUrl = new URL(url);

			if (parsedUrl.hostname.includes('youtu.be')) {
				const videoId = parsedUrl.pathname.split('/').filter(Boolean)[0];
				return videoId ? `https://www.youtube.com/embed/${videoId}` : url;
			}

			if (parsedUrl.hostname.includes('youtube.com')) {
				const shortsId = parsedUrl.pathname.match(/^\/shorts\/([^/?]+)/)?.[1];
				const embedId = parsedUrl.pathname.match(/^\/embed\/([^/?]+)/)?.[1];
				const watchId = parsedUrl.searchParams.get('v');
				const videoId = shortsId ?? embedId ?? watchId;

				return videoId ? `https://www.youtube.com/embed/${videoId}` : url;
			}

			return url;
		} catch {
			return url;
		}
	}
</script>

<article class:active={isActive} class="project-card">
	<div class="scanline"></div>
	<div class="node"></div>

	<div class="card-grid">
		<header class="card-header">
			<div>
				<p class="role">{project.role}</p>
				<h2>{project.title}</h2>
			</div>

			<span class="id">#{String(project.id).padStart(2, '0')}</span>
		</header>

		<div class="card-body">
			{#if hasVideo && isActive}
				<div class="active-content">
					<div class="media">
						<iframe
							src={embedUrl}
							title={embedTitle}
							loading="lazy"
							allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
							allowfullscreen
						></iframe>
					</div>

					<div class="details">
						<p>Project Brief</p>
						{#if isSingleDescription}
							<span class="brief">{project.desc[0]}</span>
						{:else}
							<ul>
								{#each project.desc as item}
									<li><i></i><span>{item}</span></li>
								{/each}
							</ul>
						{/if}
					</div>
				</div>
			{:else if hasImage && isActive}
				<div class="active-content">
					<div class="media">
						<img src={project.image} alt={project.title} />
					</div>

					<div class="details">
						<p>Project Brief</p>
						{#if isSingleDescription}
							<span class="brief">{project.desc[0]}</span>
						{:else}
							<ul>
								{#each project.desc as item}
									<li><i></i><span>{item}</span></li>
								{/each}
							</ul>
						{/if}
					</div>
				</div>
			{:else if isActive}
				<div class="hud">
					<div class="hud-grid" aria-hidden="true"></div>
					<div class="hud-header">
						<span>LOGS & DATA VIEWER</span>
						<strong>{project.title}</strong>
					</div>

					<div class="details">
						<p>Project Brief</p>
						{#if isSingleDescription}
							<span class="brief">{project.desc[0]}</span>
						{:else}
							<ul>
								{#each project.desc as item}
									<li><i></i><span>{item}</span></li>
								{/each}
							</ul>
						{/if}
					</div>

					<div class="hud-lines" aria-hidden="true">
						<span></span>
						<span></span>
						<span></span>
					</div>
				</div>
			{:else}
				<div class="preview">
					<p>Project Brief</p>
					{#if isSingleDescription}
						<span class="brief">{project.desc[0]}</span>
					{:else}
						<ul>
							{#each project.desc.slice(0, 2) as item}
								<li><i></i><span>{item}</span></li>
							{/each}
						</ul>
					{/if}
				</div>
			{/if}
		</div>

		<footer>
			{#each project.tags as tag}
				<span>{tag}</span>
			{/each}
			{#if project.link && isActive}
				<a href={project.link} target="_blank" rel="noreferrer" class="itch-link">itch.io ↗</a>
			{/if}
		</footer>
	</div>
</article>

<style>
	.project-card {
		position: relative;
		overflow: hidden;
		border: 1px solid rgb(139 92 246 / 0.25);
		background: rgb(9 9 11 / 0.92);
		box-shadow: 0 24px 70px rgb(0 0 0 / 0.55);
		backdrop-filter: blur(16px);
		transition:
			border-color 300ms ease,
			box-shadow 300ms ease;
	}

	.project-card.active {
		border-color: rgb(196 181 253);
		box-shadow:
			0 24px 70px rgb(0 0 0 / 0.55),
			0 0 42px rgb(139 92 246 / 0.32);
	}

	.scanline {
		position: absolute;
		inset: 0 0 auto;
		height: 1px;
		background: rgb(167 139 250);
		box-shadow: 0 0 22px rgb(167 139 250 / 0.95);
	}

	.node {
		position: absolute;
		top: 1rem;
		right: 1rem;
		width: 0.75rem;
		height: 0.75rem;
		background: rgb(139 92 246);
		box-shadow: 0 0 18px rgb(139 92 246);
	}

	.card-grid {
		display: grid;
		grid-template-rows: auto 1fr auto;
		min-height: 0;
	}

	.card-header {
		display: flex;
		align-items: flex-start;
		justify-content: space-between;
		gap: 1rem;
		padding: 1rem 1.1rem;
		border-bottom: 1px solid rgb(139 92 246 / 0.2);
	}

	.role,
	.id,
	.hud,
	.details,
	.preview,
	footer {
		font-family:
			ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, 'Liberation Mono', 'Courier New',
			monospace;
	}

	.role {
		margin: 0;
		color: rgb(139 92 246 / 0.8);
		font-size: 0.65rem;
		font-weight: 400;
		text-transform: uppercase;
		letter-spacing: 0.22em;
	}

	h2 {
		margin: 0.55rem 0 0;
		color: white;
		font-size: clamp(2rem, 4vw, 3.1rem);
		font-weight: 950;
		line-height: 1;
		text-transform: uppercase;
	}

	.id {
		border: 1px solid rgb(139 92 246 / 0.4);
		padding: 0.45rem 0.65rem;
		color: rgb(196 181 253);
		font-size: 0.75rem;
		font-weight: 800;
	}

	.card-body {
		padding: 1rem 1.1rem;
	}

	.active-content {
		display: grid;
		gap: 1rem;
		align-items: stretch;
	}

	.media {
		aspect-ratio: 16 / 9;
		overflow: hidden;
		border: 1px solid rgb(139 92 246 / 0.4);
		background: black;
		box-shadow: 0 0 34px rgb(139 92 246 / 0.22);
	}

	iframe,
	img {
		width: 100%;
		height: 100%;
		border: 0;
		object-fit: cover;
		display: block;
	}

	.hud {
		position: relative;
		display: grid;
		gap: 1rem;
		min-height: 300px;
		border: 1px solid rgb(139 92 246 / 0.25);
		background:
			linear-gradient(135deg, rgb(139 92 246 / 0.14), transparent 34%),
			rgb(0 0 0 / 0.5);
		padding: 1.25rem;
		overflow: hidden;
	}

	.hud-grid {
		position: absolute;
		inset: 0;
		background-image:
			linear-gradient(rgb(139 92 246 / 0.12) 1px, transparent 1px),
			linear-gradient(90deg, rgb(139 92 246 / 0.12) 1px, transparent 1px);
		background-size: 28px 28px;
		mask-image: radial-gradient(circle at 45% 40%, black, transparent 72%);
		pointer-events: none;
	}

	.hud-header,
	.details,
	.hud-lines,
	.preview {
		position: relative;
	}

	.hud-header {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 1rem;
		border-bottom: 1px solid rgb(139 92 246 / 0.2);
		padding-bottom: 0.75rem;
		color: rgb(196 181 253);
		font-size: 0.75rem;
		text-transform: uppercase;
	}

	.hud-header strong {
		color: white;
		text-align: right;
	}

	.details p,
	.preview p {
		margin: 0 0 0.6rem;
		color: rgb(139 92 246 / 0.7);
		font-size: 0.62rem;
		font-weight: 600;
		text-transform: uppercase;
		letter-spacing: 0.22em;
	}

	.brief {
		display: block;
		max-width: 48ch;
		margin: 0 auto;
		color: rgb(212 212 216);
		font-family:
			Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
		font-size: clamp(0.98rem, 1.08vw, 1.08rem);
		font-weight: 400;
		line-height: 1.62;
		text-align: center;
	}

	.preview {
		display: grid;
		place-content: center;
		min-height: 300px;
		border: 1px solid rgb(139 92 246 / 0.2);
		background: rgb(0 0 0 / 0.35);
		padding: 1.25rem;
		text-align: center;
	}

	ul {
		display: grid;
		gap: 0.75rem;
		margin: 1rem 0 0;
		padding: 0;
		list-style: none;
	}

	li {
		display: grid;
		grid-template-columns: auto 1fr;
		gap: 0.75rem;
		color: rgb(212 212 216);
		font-size: 0.875rem;
	}

	li i {
		width: 0.375rem;
		height: 0.375rem;
		margin-top: 0.55rem;
		background: rgb(139 92 246);
		box-shadow: 0 0 12px rgb(139 92 246);
	}

	.hud-lines {
		display: grid;
		gap: 0.45rem;
	}

	.hud-lines span {
		display: block;
		height: 1px;
		background: linear-gradient(90deg, rgb(139 92 246 / 0.65), transparent);
	}

	.hud-lines span:nth-child(2) {
		width: 74%;
	}

	.hud-lines span:nth-child(3) {
		width: 48%;
	}

	footer span {
		border: 1px solid rgb(139 92 246 / 0.25);
		padding: 0.5rem 0.75rem;
	}

	footer {
		display: flex;
		flex-wrap: wrap;
		gap: 0.5rem;
		border-top: 1px solid rgb(139 92 246 / 0.2);
		padding: 0.9rem 1.1rem;
	}

	footer span {
		background: rgb(139 92 246 / 0.1);
		color: rgb(221 214 254);
		font-size: 0.75rem;
		font-weight: 700;
		text-transform: uppercase;
	}

	.itch-link {
		margin-left: auto;
		border: 1px solid rgb(139 92 246 / 0.5);
		padding: 0.5rem 0.75rem;
		background: rgb(139 92 246 / 0.12);
		color: rgb(196 181 253);
		font-size: 0.75rem;
		font-weight: 700;
		text-decoration: none;
		text-transform: uppercase;
		letter-spacing: 0.1em;
		transition: border-color 180ms ease, background 180ms ease, color 180ms ease;
	}

	.itch-link:hover,
	.itch-link:focus-visible {
		border-color: rgb(196 181 253);
		background: rgb(139 92 246 / 0.28);
		color: white;
		outline: none;
	}

	@media (min-width: 900px) {
		.project-card.active .active-content {
			grid-template-columns: minmax(0, 1.3fr) minmax(18rem, 0.7fr);
		}

		.project-card.active .details {
			display: grid;
			align-content: center;
			border: 1px solid rgb(139 92 246 / 0.22);
			background:
				linear-gradient(135deg, rgb(139 92 246 / 0.1), transparent 45%),
				rgb(0 0 0 / 0.35);
			padding: 1.1rem;
			text-align: left;
		}

		.project-card.active .brief {
			margin: 0;
			text-align: left;
		}

		.project-card.active .media {
			min-height: 315px;
		}

		.project-card.active iframe {
			display: block;
		}
	}

	@media (max-width: 899px) {
		.card-header {
			padding: 0.9rem;
		}

		.card-body {
			padding: 0.9rem;
		}

		.brief {
			font-size: 0.98rem;
		}
	}
</style>
