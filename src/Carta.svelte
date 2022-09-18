<script lang="ts">
    export let caractere;
    export let encontrada;
    export let fechado = true;
</script>

<span>
    <button class="botao">
        <h1 class={encontrada == 1 ? 'encontrada' : '#'}>
            {fechado ? '🂠':caractere}
        </h1>
    </button>
</span>

<style>
    .botao {
        width: 80px;
        height: 120px;
        color: green;
        transition: 1.2s;
        transition-property: color;
    }
    .encontrada {
        background-color: rebeccapurple;
    }
</style>    