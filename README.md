<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Universal Downloader Engine</title>
    <style>
        :root {
            --bg-principal: #050505;
            --bg-card: #0d0d0d;
            --cor-texto: #ffffff;
            --cor-secundaria: #888888;
            --borda: #ffffff;
            --borda-fosc: #222222;
            --sucesso: #00ff66;
            --erro: #ff3333;
        }

        body {
            font-family: 'Segoe UI', -apple-system, BlinkMacSystemFont, Roboto, sans-serif;
            background-color: var(--bg-principal);
            color: var(--cor-texto);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            letter-spacing: 0.5px;
        }

        .app-container {
            background-color: var(--bg-card);
            border: 1px solid var(--borda);
            padding: 40px 30px;
            width: 440px;
            box-sizing: border-box;
            box-shadow: 0 20px 50px rgba(255, 255, 255, 0.03);
            position: relative;
            text-align: center;
        }

        .app-container::before {
            content: "[ REAL MEDIA EXTRACTION v2.0 ]";
            position: absolute;
            top: -12px;
            left: 20px;
            background: var(--bg-principal);
            padding: 0 10px;
            font-size: 0.7rem;
            color: var(--cor-secundaria);
            font-family: monospace;
        }

        h1 {
            font-size: 1.6rem;
            font-weight: 900;
            text-transform: uppercase;
            letter-spacing: 3px;
            margin: 0 0 5px 0;
        }

        .subtitulo {
            font-size: 0.75rem;
            color: var(--cor-secundaria);
            margin-bottom: 35px;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .input-group {
            margin-bottom: 20px;
            text-align: left;
        }

        label {
            font-size: 0.7rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: var(--cor-secundaria);
            display: block;
            margin-bottom: 5px;
        }

        input {
            width: 100%;
            padding: 14px;
            background-color: #000000;
            border: 1px solid var(--borda-fosc);
            color: var(--cor-texto);
            font-size: 0.95rem;
            font-family: inherit;
            box-sizing: border-box;
            transition: border 0.3s ease;
        }

        input:focus { outline: none; border-color: var(--borda); }
        input::placeholder { color: #444444; }

        button {
            width: 100%;
            padding: 15px;
            background-color: var(--cor-texto);
            color: var(--bg-principal);
            border: 1px solid var(--borda);
            font-size: 0.9rem;
            font-weight: bold;
            cursor: pointer;
            text-transform: uppercase;
            letter-spacing: 2px;
            transition: all 0.3s ease;
            margin-top: 10px;
        }

        button:hover { background-color: var(--bg-principal); color: var(--cor-texto); }
        button:disabled { background-color: #333; color: #777; border-color: #333; cursor: not-allowed; }

        .progresso-container {
            margin-top: 30px;
            text-align: left;
        }

        .status-texto {
            font-family: monospace;
            font-size: 0.8rem;
            color: var(--cor-secundaria);
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
        }

        .barra-fundo {
            width: 100%;
            height: 8px;
            background-color: #111111;
            border: 1px solid var(--borda-fosc);
            position: relative;
            box-sizing: border-box;
        }

        .barra-preenchimento {
            width: 0%;
            height: 100%;
            background-color: var(--cor-texto);
            transition: width 0.2s ease-out;
        }

        .barra-completa {
            background-color: var(--sucesso) !important;
            box-shadow: 0 0 10px rgba(0, 255, 102, 0.4);
        }

        .msg-status {
            font-family: monospace;
            font-size: 0.85rem;
            margin-top: 15px;
            text-align: center;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        
        .sucesso-cor { color: var(--sucesso); }
        .erro-cor { color: var(--erro); }

        .escondido { display: none !important; }
        .fade-in { animation: fadeIn 0.4s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

    <div class="app-container fade-in">
        <h1>Real Downloader</h1>
        <div class="subtitulo">Extrator de Vídeo Sem Marca d'Água</div>

        <div class="input-group">
            <label>Insira o link real do vídeo (TikTok)</label>
            <input type="text" id="video-url" placeholder="https://vm.tiktok.com/...">
        </div>

        <button id="btn-download" onclick="processarVideoReal()">Baixar na Galeria</button>

        <!-- Container da Barra de Carregamento -->
        <div id="area-progresso" class="progresso-container escondido">
            <div class="status-texto">
                <span id="label-status">Contatando API de descriptografia...</span>
                <span id="porcentagem-texto">0%</span>
            </div>
            <div class="barra-fundo">
                <div id="barra-progresso" class="barra-preenchimento"></div>
            </div>
            <div id="mensagem-final" class="msg-status escondido"></div>
        </div>
    </div>

    <script>
        async function processarVideoReal() {
            const urlInput = document.getElementById('video-url').value.trim();

            if (urlInput === "") {
                alert("Insira um link do TikTok para fazer o download real.");
                return;
            }

            const btn = document.getElementById('btn-download');
            const areaProgresso = document.getElementById('area-progresso');
            const barra = document.getElementById('barra-progresso');
            const textoPorcentagem = document.getElementById('porcentagem-texto');
            const labelStatus = document.getElementById('label-status');
            const msgFinal = document.getElementById('mensagem-final');

            // Prepara a tela
            btn.disabled = true;
            areaProgresso.classList.remove('escondido');
            msgFinal.classList.add('escondido');
            barra.classList.remove('barra-completa');
            
            // Animação inicial simulando requisição
            atualizarBarra(15, "0%", "Enviando link para o servidor de quebra de assinaturas...");

            try {
                // Requisição para API pública especializada em TikTok sem marca d'água
                const resposta = await fetch(`https://www.tikwm.com/api/?url=${encodeURIComponent(urlInput)}`);
                const dados = await resposta.json();

                if (dados && dados.data && dados.data.play) {
                    atualizarBarra(60, "60%", "Link descriptografado! Baixando arquivo limpo...");
                    
                    const videoUrlSemWatermark = dados.data.play;

                    // Faz o download físico do arquivo de vídeo para o navegador salvar no dispositivo
                    const blobResposta = await fetch(videoUrlSemWatermark);
                    const blobVideo = await blobResposta.blob();
                    
                    atualizarBarra(90, "90%", "Convertendo fluxo para formato MP4 da Galeria...");

                    const linkDownload = document.createElement('a');
                    linkDownload.href = URL.createObjectURL(blobVideo);
                    linkDownload.download = `video_sem_marca_${Date.now()}.mp4`;
                    
                    // Dispara o download real do arquivo na galeria/downloads do celular/PC
                    document.body.appendChild(linkDownload);
                    linkDownload.click();
                    document.body.removeChild(linkDownload);

                    // Conclusão com sucesso
                    setTimeout(() => {
                        barra.style.width = "100%";
                        textoPorcentagem.innerText = "100%";
                        labelStatus.innerText = "Concluído.";
                        barra.classList.add('barra-completa');
                        msgFinal.className = "msg-status sucesso-cor fade-in";
                        msgFinal.innerText = "✓ Salvo com sucesso sem marca d'água!";
                        btn.disabled = false;
                    }, 500);

                } else {
                    throw new Error("Não foi possível extrair a mídia desse link.");
                }

            } catch (erro) {
                // Tratamento de falhas
                barra.style.width = "100%";
                barra.style.backgroundColor = "var(--erro)";
                textoPorcentagem.innerText = "FALHA";
                labelStatus.innerText = "Erro no processo.";
                msgFinal.className = "msg-status erro-cor fade-in";
                msgFinal.innerText = "✕ Link inválido ou fora do ar. Tente outro.";
                btn.disabled = false;
            }
        }

        function atualizarBarra(largura, porc, status) {
            document.getElementById('barra-progresso').style.width = largura + "%";
            document.getElementById('porcentagem-texto').innerText = porc;
            document.getElementById('label-status').innerText = status;
        }
    </script>
</body>
</html>

