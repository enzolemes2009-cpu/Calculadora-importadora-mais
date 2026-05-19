# Calculadora-importadora-mais
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora WPC | Importadora Mais</title>
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Montserrat', sans-serif; }
        body { background-color: #f4f4f4; min-height: 100vh; display: flex; align-items: center; justify-content: center; padding: 20px; }
        .app-container { background-color: #ffffff; width: 100%; max-width: 500px; border-radius: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); overflow: hidden; }
        header { background-color: #121212; padding: 30px 20px; text-align: center; }
        .brand { color: #d4af37; font-weight: 700; font-size: 1.2rem; border-bottom: 2px solid #d4af37; padding-bottom: 3px; display: inline-block; }
        header h1 { color: #ffffff; font-size: 1.4rem; margin-top: 8px; text-transform: uppercase; letter-spacing: 1px; }
        .content { padding: 30px; }
        .input-group { margin-bottom: 20px; }
        .input-group label { display: block; font-weight: 600; margin-bottom: 8px; color: #333; }
        .input-group input { width: 100%; padding: 15px; border: 2px solid #ddd; border-radius: 8px; font-size: 1rem; outline: none; }
        .input-group input:focus { border-color: #d4af37; }
        .btn-group { display: flex; gap: 10px; }
        .btn-group button { flex: 1; padding: 15px; border: none; border-radius: 8px; font-size: 1rem; font-weight: 600; cursor: pointer; text-transform: uppercase; transition: all 0.3s; }
        .btn-calc { background-color: #121212; color: #d4af37; }
        .btn-calc:hover { background-color: #333; }
        .btn-clear { background-color: transparent; border: 2px solid #ddd !important; color: #666; }
        .btn-clear:hover { border-color: #121212 !important; color: #121212; }
        .error-message { background-color: #ffebee; color: #c62828; padding: 12px; border-radius: 8px; margin-bottom: 20px; text-align: center; display: none; }
        .result-box { display: none; margin-top: 25px; background: #fafafa; border: 1px solid #ddd; border-left: 4px solid #d4af37; border-radius: 8px; padding: 20px; animation: fadeIn 0.5s; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .result-box .dimension { text-align: center; font-size: 1.1rem; font-weight: 600; margin-bottom: 15px; padding-bottom: 10px; border-bottom: 1px solid #ddd; }
        .result-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        .result-item { background: #fff; padding: 15px; border-radius: 8px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .result-item.total { grid-column: span 2; background: #121212; color: #d4af37; margin-top: 10px; }
        .result-item span { display: block; font-size: 0.75rem; color: #666; text-transform: uppercase; }
        .result-item.total span { color: #bbb; }
        .result-item strong { display: block; font-size: 1.5rem; font-weight: 700; }
        .result-details { margin-top: 12px; font-size: 0.8rem; color: #666; text-align: center; font-style: italic; }
    </style>
</head>
<body>

<div class="app-container">
    <header>
        <div class="brand">Importadora Mais</div>
        <h1>Calculadora WPC</h1>
    </header>
    
    <div class="content">
        <!-- Mensagem de Erro -->
        <div id="errorMsg" class="error-message"></div>

        <!-- Formulário -->
        <form id="calcForm">
            <div class="input-group">
                <label for="wallWidth">Largura da Parede</label>
                <input type="number" id="wallWidth" step="0.01" placeholder="Ex: 3.20">
            </div>

            <div class="input-group">
                <label for="wallHeight">Altura da Parede</label>
                <input type="number" id="wallHeight" step="0.01" placeholder="Ex: 2.80">
            </div>

            <div class="btn-group">
                <button type="button" class="btn-clear" onclick="limparCampos()">Limpar</button>
                <button type="button" class="btn-calc" onclick="calcularPlacas()">Calcular</button>
            </div>
        </form>

        <!-- Resultado -->
        <div id="resultBox" class="result-box">
            <div class="dimension" id="dimensionText"></div>
            <div class="result-grid">
                <div class="result-item">
                    <span>Placas na Largura</span>
                    <strong id="platesWidth">0</strong>
                </div>
                <div class="result-item">
                    <span>Placas na Altura</span>
                    <strong id="platesHeight">0</strong>
                </div>
                <div class="result-item total">
                    <span>Total de Placas</span>
                    <strong id="platesTotal">0</strong>
                </div>
            </div>
            <div class="result-details" id="calcDetails"></div>
        </div>
    </div>
</div>

<script>
    // --- CONFIGURAÇÃO DAS PLACAS ---
    var larguraPlaca = 0.16; // 16 cm convertidos para metros
    var alturaPlaca = 2.90;  // 2.90 metros

    // Função para mostrar erro
    function mostrarErro(mensagem) {
        document.getElementById('errorMsg').style.display = 'block';
        document.getElementById('errorMsg').innerText = mensagem;
        document.getElementById('resultBox').style.display = 'none';
    }

    // Função para limpar os campos
    function limparCampos() {
        document.getElementById('wallWidth').value = '';
        document.getElementById('wallHeight').value = '';
        document.getElementById('errorMsg').style.display = 'none';
        document.getElementById('resultBox').style.display = 'none';
    }

    // Função principal de cálculo
    function calcularPlacas() {
        // Obter valores dos inputs
        var larguraParede = parseFloat(document.getElementById('wallWidth').value);
        var alturaParede = parseFloat(document.getElementById('wallHeight').value);

        // Validar campo vazio
        if (document.getElementById('wallWidth').value === '' || document.getElementById('wallHeight').value === '') {
            mostrarErro('Por favor, preencha todos os campos.');
            return;
        }

        // Validar números inválidos ou negativos
        if (isNaN(larguraParede) || isNaN(alturaParede) || larguraParede <= 0 || alturaParede <= 0) {
            mostrarErro('Os valores devem ser números maiores que zero.');
            return;
        }

        // === FÓRMULAS DE CÁLCULO ===
        
        // Quantidade na largura = largura_parede / 0.16
        var calcLargura = larguraParede / larguraPlaca;
        var qtdLargura = Math.ceil(calcLargura);

        // Quantidade na altura = altura_parede / 2.90
        var calcAltura = alturaParede / alturaPlaca;
        var qtdAltura = Math.ceil(calcAltura);

        // Total = quantidade_largura * quantidade_altura
        var totalPlacas = qtdLargura * qtdAltura;

        // === EXIBIR RESULTADO ===
        document.getElementById('errorMsg').style.display = 'none';
        document.getElementById('dimensionText').innerText = 'Parede de ' + larguraParede.toFixed(2) + 'm x ' + alturaParede.toFixed(2) + 'm';
        document.getElementById('platesWidth').innerText = qtdLargura;
        document.getElementById('platesHeight').innerText = qtdAltura;
        document.getElementById('platesTotal').innerText = totalPlacas;
        
        // Detalhes do cálculo
        document.getElementById('calcDetails').innerText = 
            'Largura: ' + larguraParede.toFixed(2) + ' / 0.16 = ' + calcLargura.toFixed(2) + ' → ' + qtdLargura + 
            ' | Altura: ' + alturaParede.toFixed(2) + ' / 2.90 = ' + calcAltura.toFixed(2) + ' → ' + qtdAltura;
        
        document.getElementById('resultBox').style.display = 'block';
    }
</script>

</body>
</html>
