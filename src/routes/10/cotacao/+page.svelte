<script>
    let moedas = [
        { nome: 'Dólar americano', código: 'USD' },
        { nome: 'Real brasileiro', código: 'BRL' },
        { nome: 'Euro', código: 'EUR' },
        { nome: 'Libra esterlina', código: 'GBP' },
        { nome: 'Iene japonês', código: 'JPY' },
        { nome: 'Peso argentino', código: 'ARS' }
    ];

    let código1 = $state(moedas[0].código);
    let código2 = $state(moedas[1].código);
    let valor1 = $state(0);
    let valor2 = $state(0);
    let moedaPadrao = $state();

    async function mudarMoeda() {
        const resposta = await fetch(`https://open.er-api.com/v6/latest/${código1}`);
        moedaPadrao = await resposta.json();
        converterDe();
    }

    function converterDe() {
        valor2 = (valor1 * moedaPadrao.rates[código2]).toFixed(2);
    }

    function converterPara() {
        valor1 = (valor2 / moedaPadrao.rates[código2]).toFixed(2);
    }

    mudarMoeda();
</script>
{#if moedaPadrao}
    <div class="mt-3">
        <p>Data da cotação: {moedaPadrao.time_last_updated ? new Date(moedaPadrao.time_last_updated * 1000).toLocaleString() : 'N/A'}</p>
        <p>Taxa de câmbio ({código1} para {código2}): {moedaPadrao.rates[código2] || 'N/A'}</p>
    </div>
{/if}
<div class="container">
    <h1 class="text-center" style="color: aqua;">Cotação de moedas</h1>
    <div class="input-group">
        <select class="form-select" bind:value={código1} onchange={mudarMoeda}>
            {#each moedas as moeda}
                <option value={moeda.código} title={moeda.nome}>{moeda.código}</option>
            {/each}
        </select>
        <input placeholder="0,00" type="number" class="form-control w-25" oninput={converterDe} bind:value={valor1} />
        <button class="btn btn-outline-secondary" type="button" onclick={() => {
            let temp = código1;
            código1 = código2;
            código2 = temp;
            mudarMoeda();
        }}>⇄</button>
        <input placeholder="0,00" type="number" class="form-control w-25" oninput={converterPara} bind:value={valor2} />
        <select class="form-select" bind:value={código2} onchange={converterPara}>
            {#each moedas as moeda}
                <option value={moeda.código} title={moeda.nome}>{moeda.código}</option>
            {/each}
        </select>
    </div>

    <div class="position-fixed bottom-0 end-0 m-3">
        <a href="https://www.exchangerate-api.com">Rates By Exchange Rate API</a>
    </div>
</div>
<style>
    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
        background-color: blue;
        border-radius: 8px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    h1 {
        font-size: 1.8rem;
        margin-bottom: 20px;
        color: #343a40;
    }

    .input-group {
        display: flex;
        align-items: center;
        gap: 10px;
        margin-bottom: 20px;
    }

    .form-select, .form-control {
        border-radius: 5px;
        border: 1px solid #ced4da;
        padding: 10px;
        font-size: 1rem;
        padding-right: 2rem; /* Add extra padding to avoid obstruction by the arrow */
    }

    .form-control:focus, .form-select:focus {
        outline: none;
        border-color: #80bdff;
        box-shadow: 0 0 5px rgba(0, 123, 255, 0.25);
    }

    .btn {
        background-color: #007bff;
        color: white;
        border: none;
        padding: 10px 15px;
        font-size: 1rem;
        cursor: pointer;
        border-radius: 5px;
        transition: background-color 0.3s ease;
    }

    .btn:hover {
        background-color: #0056b3;
    }

    .btn-outline-secondary {
        background-color: white;
        color: #6c757d;
        border: 1px solid #6c757d;
    }

    .btn-outline-secondary:hover {
        background-color: #6c757d;
        color: white;
    }

    .position-fixed a {
        text-decoration: none;
        color: #007bff;
        font-size: 0.9rem;
    }

    .position-fixed a:hover {
        text-decoration: underline;
    }

    .mt-3 p {
        margin: 5px 0;
        font-size: 0.9rem;
        color: #495057;
    }
    .btn, .btn-outline-secondary {
        border-radius: 50px;
    }
</style>