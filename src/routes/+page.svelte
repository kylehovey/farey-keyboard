<script lang="ts">
	import P5 from 'p5-svelte';
	import type { Sketch } from 'p5-svelte';

	interface Location {
		x: number;
		y: number;
	}

	interface FareyDiagram {
		width: number;
		height: number;
		order: number;
		center: Location;
	}

	interface Rational {
		numerator: number;
		denominator: number;
		order: number;
	}

	const r =
		(_: TemplateStringsArray, ...[numerator, denominator]: number[]) =>
		(order: number) => ({
			numerator,
			denominator,
			order
		});

	type P5 = Parameters<Sketch>[0];

	const mediant = (lhs: Rational, rhs: Rational, newOrder: number): Rational =>
		r`${lhs.numerator + rhs.numerator}/${lhs.denominator + rhs.denominator}`(newOrder);

	const asReal = ({ numerator, denominator }: Rational): number => numerator / denominator;

	const fareySequence = (order: number): Rational[] => {
		let curOrder = 1;
		let seq: Rational[] = [r`${0}/${1}`(curOrder), r`${1}/${1}`(curOrder)];

		for (; curOrder <= order; curOrder++) {
			seq = seq.reduce<Rational[]>(
				(acc, cur, i) => [
					...acc,
					...(i === seq.length - 1 ? [cur] : [cur, mediant(cur, seq[i + 1], curOrder)])
				],
				[]
			);
		}

		return seq;
	};

	const fareyDiagram = ({ width, height, order, center: { x: cx, y: cy } }: FareyDiagram) => {
		const farey = fareySequence(order);

		return (p5: P5) => {
			const [left__, right_] = [cx - width / 2, cx + width / 2];
			const [top___, bottom] = [cy - height, cy];

			p5.line(left__, top___, right_, top___);
			p5.line(left__, bottom, right_, bottom);
			p5.line(left__, bottom, left__, top___);
			p5.line(right_, bottom, right_, top___);

			for (const element of farey) {
				const fx = left__ + asReal(element) * width;
				const fy = height * 0.9 * (1 / element.order) ** 0.8;

				p5.line(fx, bottom, fx, bottom - fy);
			}
		};
	};

	const f1 = fareyDiagram({
		width: 900,
		height: 500,
		order: 5,
		center: {
			x: 500,
			y: 800
		}
	});

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
			f1(p5);
		};
	};
</script>

<P5 {sketch} />
