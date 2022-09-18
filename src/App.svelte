<script lang="ts">
	import Carta from "./Carta.svelte";

	export let name: string;
	let progression = 0;
	let tamanho = 4;
	let numero_cartas_viradas = 0;
	let primeira_carta_virada = -1;
	let segunda_carta_virada = -1;
	let cartas_viradas = [
		0,0,0,0,0,0,
		0,0,0,0,0,0,
		0,0,0,0,0,0,
		0,0,0,0,0,0,
	];
	let encontradas = [
		0,0,0,0,0,0,
		0,0,0,0,0,0,
		0,0,0,0,0,0,
		0,0,0,0,0,0,
	];
	const emojis = [
		'🚢', '⚽', '✂','🕊', // 4 peq
		'✈', '⭐', '🕶','🕷', // 8 med
		'🕸','🖋','💣','🖐', // 12 gra
	];
	const numeros = [
		'1', '2', '3','4', // 4 peq
		'5', '6', '7','8', // 8 med
		'9','10','11','12', // 12 gra
	];
	const letras = [
		'A', 'B', 'C','D', // 4 peq
		'E', 'F', 'G','H', // 8 med
		'I','J','K','L', // 12 gra
	];
	const estados_ui = [
		[ 1, 0, 0 ],
		[ 2, 1, 0 ],
		[ 2, 2, 1 ],
	];
	const estados_individuais = [
		{	name:'antes',	},
		{	name:'durante',	},
		{	name:'depois',	},
	];
	$: cars_string = 'emoji';
	$: cars = emojis.slice(0,tamanho);
	$: tabuleiro = shuffle(cars.concat(cars));
	$: escolha = 'pequeno';
	function virar_carta(indice) {
		if(numero_cartas_viradas == 0) {
			primeira_carta_virada = indice;
			numero_cartas_viradas = 1;
			cartas_viradas[indice] = 1;
		} else if(numero_cartas_viradas == 1) {
			cartas_viradas[indice] = 1;
			segunda_carta_virada = indice;
			numero_cartas_viradas = 2;
		} else if(numero_cartas_viradas == 2) {
			if (tabuleiro[primeira_carta_virada]!=tabuleiro[segunda_carta_virada]) {
				cartas_viradas[primeira_carta_virada] = 0;
				cartas_viradas[segunda_carta_virada] = 0;
				primeira_carta_virada = -1;
				segunda_carta_virada = -1;
				numero_cartas_viradas = 0;
			}
			if (tabuleiro[primeira_carta_virada]==tabuleiro[segunda_carta_virada]) {
				encontradas[primeira_carta_virada] = 1;
				encontradas[segunda_carta_virada] = 1;
				primeira_carta_virada = -1;
				segunda_carta_virada = -1;
				numero_cartas_viradas = 0;
			}
		}
	}
	function novoJogo() {
		progression = 0;
		tamanho = 4;
		numero_cartas_viradas = 0;
		primeira_carta_virada = -1;
		segunda_carta_virada = -1;
		cartas_viradas = [
			0,0,0,0,0,0,
			0,0,0,0,0,0,
			0,0,0,0,0,0,
			0,0,0,0,0,0,
		];
		encontradas = [
			0,0,0,0,0,0,
			0,0,0,0,0,0,
			0,0,0,0,0,0,
			0,0,0,0,0,0,
		];
		cars_string = 'emoji';
		cars = emojis.slice(0,tamanho);
		tabuleiro = shuffle(cars.concat(cars));
		escolha = 'pequeno';
	}
	function anterior() {
		progression--;
		if (progression == -1) {
			progression = 0;
		}
	}
	function proximo() {
		progression++;
		if (progression == 3) {
			progression = 0;
		}
	}
	async function delay(ms) {
		progression = 0;
		await new Promise(f => setTimeout(f, ms));
		progression = 1;
		await new Promise(f => setTimeout(f, ms));
		progression = 2;
	}
	function animate() {
		novoJogo();
		delay(500);
	}
	function shuffle(array) {
		let currentIndex = array.length;
		let randomIndex;
		while (currentIndex != 0) {
			randomIndex = Math.floor(Math.random() * currentIndex);
			currentIndex--;
			[array[currentIndex], array[randomIndex]] = [
			array[randomIndex], array[currentIndex]];
		}
		return array;
	}
	function mudaTamanho() {
		if (escolha == 'pequeno') {
			tamanho = 4;
		} else if (escolha == 'médio') {
			tamanho = 8;
		} else {
			tamanho = 12;
		}
		proximo();
	}
	function mudaCars() {
		if (cars_string=='emoji') {
			cars = emojis.slice(0,tamanho);
		} else if (cars_string=='numero') {
			cars = numeros.slice(0,tamanho);
		} else {
			cars = letras.slice(0,tamanho);
		}
		let tabs = cars.concat(cars);
		tabuleiro = shuffle(tabs);
		proximo();
	}
</script>

<main>
	<h1>Game {name} interface!</h1>
	<button on:click={novoJogo}>Novo Jogo</button>
	<button on:click={anterior}>Anterior</button>
	<button on:click={proximo}>Próximo</button>
	<button on:click={animate}>Default</button>
	<div class={estados_individuais[estados_ui[progression][0]].name}>
		<select disabled={progression == 0 ? false: true} name='tamanho'
				bind:value={escolha} on:change={mudaTamanho}>
			<option value='pequeno' selected>Pequeno</option>
			<option value='médio'>Médio</option>
			<option value='grande'>Grande</option>
		</select>
	</div>
	<div class={estados_individuais[estados_ui[progression][1]].name}>
		<select disabled={progression == 1 ? false: true} name='caracteres' 
				bind:value={cars_string} on:change={mudaCars}>
			<option value='emoji' selected>Emojis</option>
			<option value='numero'>Números</option>
			<option value='letra'>Letras</option>
		</select>
	</div>
	{#each Array(2*tamanho) as _,indice}
		<span class={estados_individuais[estados_ui[progression][2]].name}
			on:click={
			() => {
				if (progression == 2) {
					virar_carta(indice);
				}
			}
			}>
			<Carta caractere={tabuleiro[indice]} encontrada={encontradas[indice]}
				fechado={cartas_viradas[indice] == 1 ? false: true}/>
		</span>
	{/each}
</main>

<style>
	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 72;
	}
	div {
		transition: 1.2s;
		transition-property: opacity, font-size, color, height;
	}
	.antes {
		/* height: 2em; */
		color: blue;
		font-size: 0.5em;
		opacity: 0.6;
	}
	.durante {
		/* height: 2em; */
		color: red;
		font-size: 1.2em;
		opacity: 1;
	}
	.depois {
		/* height: 2em; */
		color: gray;
		font-size: 0.5em;
		opacity: 0.6;
	}
</style>