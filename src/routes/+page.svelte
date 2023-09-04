<script lang="ts">
	import P5 from 'p5-svelte';
	import type { Sketch } from 'p5-svelte';

	let width = 55;
	let height = 55;

	const sketch: Sketch = (p5) => {
		const resize = () => {
			p5.resizeCanvas(p5.windowWidth, p5.windowHeight);
		};

		p5.setup = () => {
			p5.createCanvas(800, 800);
			resize();
		};

		p5.windowResized = () => resize;

		p5.draw = () => {
			p5.ellipse(p5.width / 2, p5.height / 2, width, height);
		};
	};
</script>

<label>
	Width
	<input type="range" bind:value={width} min="100" max="1000" step="0.01" />
	{width.toFixed(2).padEnd(10, ' ')}
</label>

<label>
	Height
	<input type="range" bind:value={height} min="100" max="1000" step="0.01" />
	{height.toFixed(2).padEnd(10, ' ')}
</label>

<P5 {sketch} />
