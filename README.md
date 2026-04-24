# Script_extracao_processos_projudi_tjam
Script feito para gerar relção de processos do Projudi (TJAM) no formato excel. O script foi feito para rodar diretamente no console do navegador (F12). Instruções no arquivo MD.

# Tutorial de Extração Automatizada de Processos - Projudi TJAM

Este guia descreve como utilizar a solução de extração desenvolvida para coletar números de processos (CNJ) de forma massiva e automatizada, diretamente pelo navegador.

## 📋 Pré-requisitos
- **Navegador:** Google Chrome ou Microsoft Edge (recomendado).
- **Acesso:** Estar logado no sistema Projudi TJAM e na tela de listagem de processos (ex: processos vinculados a uma OAB).

---

## 🚀 Passo a Passo para Execução

1.  **Prepare a Página:** No Projudi, navegue até a tela que exibe a lista dos processos que você deseja coletar. Garanta que a lista esteja carregada.
2.  **Abra o Console do Desenvolvedor:**
    - Pressione a tecla `F12` no seu teclado.
    - Ou clique com o botão direito em qualquer lugar da página e selecione **Inspecionar**, depois clique na aba **Console**.
3.  **Injete o Script:**
    - Copie todo o código contido na seção "Script Mestre" abaixo.
    - Cole no Console e pressione `Enter`.
4.  **Utilize o Painel:**
    - Um painel flutuante aparecerá no canto superior da tela.
    - Clique em **▶ INICIAR COLETA**. O script começará a ler os processos e clicar automaticamente no botão "Próximo".
    - Acompanhe o contador de processos subindo em tempo real.
5.  **Finalização:**
    - Quando o script atingir o fim da lista, ele avisará que terminou.
    - Clique em **💾 BAIXAR CSV** para gerar e baixar a planilha com todos os números coletados.

---

## 💻 Script Mestre (Versão Consolidada)

Copie e cole o código abaixo no console:

```javascript
/**
 * EXTRATOR PROJUDI TJAM - VERSÃO FINAL CONSOLIDADA
 * Desenvolvido para capturar números CNJ completos e navegar entre páginas.
 */

(async function() {
    console.log("Injetando painel de extração no quadro principal...");

    // Tenta encontrar o melhor lugar para colocar o painel (mainFrame)
    let docAlvo;
    try {
        docAlvo = window.frames['mainFrame'].document;
    } catch(e) {
        docAlvo = document;
    }

    // Remove painel antigo se existir
    const antigo = docAlvo.getElementById('extrator-projudi-painel');
    if (antigo) antigo.remove();

    // Cria o Painel flutuante
    const painel = docAlvo.createElement('div');
    painel.id = 'extrator-projudi-painel';
    painel.style = "position: fixed; top: 10px; left: 10px; z-index: 999999; background: #f8f9fa; border: 3px solid #005aa7; padding: 20px; border-radius: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); font-family: sans-serif; width: 260px;";
    
    painel.innerHTML = `
        <h3 style="margin: 0 0 10px 0; color: #005aa7; font-size: 18px; border-bottom: 2px solid #005aa7; padding-bottom: 5px;">Extrator Projudi</h3>
        <p id="txt-status" style="font-size: 14px; font-weight: bold; color: #333; margin: 10px 0;">Aguardando comando...</p>
        <div style="display: flex; flex-direction: column; gap: 10px;">
            <button id="btn-play" style="padding: 12px; cursor: pointer; background: #28a745; color: white; border: none; border-radius: 6px; font-weight: bold; font-size: 14px;">▶ INICIAR COLETA</button>
            <button id="btn-save" style="padding: 10px; cursor: pointer; background: #007bff; color: white; border: none; border-radius: 6px; font-weight: bold;">💾 BAIXAR CSV</button>
        </div>
        <p style="margin-top: 15px; font-size: 13px; color: #444;">Processos coletados: <span id="num-total" style="font-weight: bold; font-size: 16px; color: #d9534f;">0</span></p>
    `;
    docAlvo.body.appendChild(painel);

    let listaProcessos = new Set();
    let executando = false;
    const info = docAlvo.getElementById('txt-status');
    const displayTotal = docAlvo.getElementById('num-total');

    function getDocExtração() {
        try {
            // Estrutura detectada no Projudi TJAM: Topo > mainFrame > userMainFrame
            return window.frames['mainFrame'].frames['userMainFrame'].document;
        } catch (e) {
            return docAlvo;
        }
    }

    async function loop() {
        if (!executando) return;

        const d = getDocExtração();
        // Regex para capturar o formato completo CNJ: NNNNNNN-DD.YYYY.J.TR.OOOO
        const regex = /\d{7}-\d{2}\.\d{4}\.\d\.\d{2}\.\d{4}/g;
        const matches = d.body.innerText.match(regex) || [];
        
        let antes = listaProcessos.size;
        matches.forEach(m => listaProcessos.add(m));
        
        displayTotal.innerText = listaProcessos.size;
        info.innerText = `Página lida! (+${listaProcessos.size - antes} novos)`;

        // Procura pelo botão "Próximo" usando a classe e título específicos do sistema
        const btnNext = d.querySelector('a.arrowNextOn') || d.querySelector('a[title*="próxima página"]');
        
        if (btnNext) {
            info.innerText = "Carregando próxima página...";
            btnNext.click();
            // Tempo de espera (em milissegundos) para o servidor do TJAM processar a requisição
            await new Promise(r => setTimeout(r, 4500)); 
            if (executando) loop();
        } else {
            executando = false;
            info.innerHTML = "<span style='color: green;'>COLETA FINALIZADA!</span>";
            alert(`Fim da lista! Total de processos coletados: ${listaProcessos.size}`);
        }
    }

    // Ações dos botões
    docAlvo.getElementById('btn-play').onclick = () => {
        if (executando) return;
        executando = true;
        loop();
    };

    docAlvo.getElementById('btn-save').onclick = () => {
        const dados = Array.from(listaProcessos);
        if (dados.length === 0) return alert("Nenhum dado coletado para exportar.");
        const csv = "Numero_do_Processo\n" + dados.join("\n");
        const blob = new Blob([csv], { type: 'text/csv;charset=utf-8' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `processos_projudi_extraidos_${new Date().getTime()}.csv`;
        a.click();
    };

    console.log("Painel injetado com sucesso! Procure o quadro azul no canto da tela.");
})();
```

---

## ⚠️ Dicas e Soluções de Problemas

- **Lentidão do Sistema:** Se o Projudi estiver lento, o script pode tentar ler a página antes dela carregar. Se isso acontecer, você pode editar o valor `4500` (milissegundos) no final do script para um valor maior (ex: `6000`).
- **Queda de Sessão:** Se o sistema deslogar você, basta logar novamente, ir para a página de onde parou e injetar o script novamente.
- **Formato CSV:** O arquivo baixado pode ser aberto diretamente no **Excel** ou **Google Sheets**.

---
*Este roteiro foi gerado automaticamente para auxiliar na automação de tarefas jurídicas.*
