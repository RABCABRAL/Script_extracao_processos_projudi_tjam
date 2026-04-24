# Extrator de Processos Projudi TJAM ⚖️🚀

Este projeto disponibiliza um script automatizado para a extração massiva de números de processos (formato CNJ) do sistema **Projudi do Tribunal de Justiça do Amazonas (TJAM)**. 

Desenvolvido para facilitar o trabalho de advogados e profissionais do Direito que precisam gerenciar grandes volumes de processos (ex: listas de OAB com mais de 1000 registros).

## 🌟 Diferenciais
- **Execução via Console:** Não requer instalação de extensões complexas ou Python.
- **Painel de Controle:** Interface amigável injetada diretamente na página.
- **Navegação Automática:** Percorre as diversas páginas de resultados clicando sozinho no botão "Próximo".
- **Foco em Segurança:** O script roda no contexto do navegador, aproveitando sua sessão ativa.
- **Exportação CSV:** Gera uma planilha pronta para Excel ou Google Sheets.

## 📖 Como Usar

1.  **Faça Login:** Acesse o Projudi TJAM com suas credenciais ou certificado.
2.  **Navegue até a Lista:**
    - Na tela "Mesa do Advogado Particular", clique na guia **Processos**.
    - Selecione **1º Grau** e depois **Ativos**.
    - A lista com os 20 primeiros processos aparecerá na tela.
3.  **Abra o Console:** Pressione `F12` (ou `Ctrl+Shift+I`) no seu navegador.
4.  **Injete o Script:** Copie o código do arquivo [`script_final.js`](./script_final.js) deste repositório, cole no console e pressione `Enter`.
5.  **Inicie a Coleta:** No painel que surgirá, clique em **▶ INICIAR COLETA**.
    - *O script percorrerá automaticamente todas as "N" páginas da lista.*
6.  **Baixe os Dados:** Ao final, clique em **💾 BAIXAR PLANILHA (CSV)**.

## 🛠️ Detalhes Técnicos
O script utiliza seletores específicos para a arquitetura de frames do Projudi TJAM (`mainFrame` > `userMainFrame`) e captura o número completo do processo via Expressões Regulares (Regex), garantindo precisão mesmo em tabelas complexas.

## 🛡️ Aviso Legal
Este script é uma ferramenta de auxílio à produtividade. O uso deve respeitar os termos de serviço do tribunal e as normas da OAB. O autor não se responsabiliza pelo uso indevido da ferramenta.

---
*Desenvolvido com auxílio de IA para otimização de fluxos jurídicos.*
