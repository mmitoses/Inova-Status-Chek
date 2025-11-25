# 📊 Inova Status Check

Ferramenta de **monitorização de disponibilidade de sites** desenvolvida para o **Projeto de Inovação Tecnológica da UFPR**.

Permite verificar se **URLs** estão **online** ou **offline**, individualmente ou em lote (via Excel).

---

## 🧐 Como Funciona a Verificação?

Como esta ferramenta corre **diretamente no navegador do utilizador** (sem um servidor backend próprio), ela utiliza uma estratégia de **3 camadas** para contornar bloqueios de segurança (CORS) e sistemas anti-robô.

A verificação funciona como um funil:

1.  **Tentativa 1: Proxy Rápido (CorsProxy)**
    * O sistema pede para um intermediário rápido tentar aceder ao site.
    * **Objetivo:** Obter o status real (200, 404, 500) com velocidade.
2.  **Tentativa 2: Proxy Reserva (AllOrigins)**
    * Se o primeiro falhar (estiver fora do ar), usamos um segundo serviço intermediário.
    * **Objetivo:** Garantir redundância. Se um serviço cair, o outro assume.
3.  **Tentativa 3: Modo "Espião" (Conexão Direta `no-cors`)**
    * Se os proxies forem bloqueados pelo site alvo (comum em LinkedIn, Bancos, Google), o navegador tenta "tocar" no servidor diretamente.
    * **Objetivo:** Confirmar se o site existe, mesmo que ele não deixe ler o conteúdo.
    * Se a conexão for feita, marcamos como **Online (Restrito)**.

---

## 🚦 Guia de Códigos de Status

Entenda o que cada resultado na tela significa:

| Status Visual | Código Técnico | O que significa na prática? |
| :---: | :---: | :--- |
| **🟢 Online** | `200` | **Sucesso Total.** O site existe, está no ar e permitiu o acesso completo. É o cenário ideal. |
| **🟢 Online** | `200 (Restrito)` | **Sucesso Parcial.** O site está no ar, mas possui bloqueios contra robôs (ex: LinkedIn, Instagram). O sistema confirmou que ele está "vivo", mas não conseguiu ler o conteúdo da página. |
| **🔴 Offline** | `403` | **Proibido (Forbidden).** O site existe, mas o servidor bloqueou ativamente o acesso. Pode ser uma firewall a bloquear a verificação. |
| **🔴 Offline** | `404` | **Não Encontrado.** O domínio existe, mas a página específica (URL) não foi encontrada. É como ligar para um número errado. |
| **🔴 Offline** | `500 / 502 / 503` | **Erro no Servidor.** O site está "quebrado" internamente. Ele existe, mas está com defeito, em manutenção ou sobrecarregado. |
| **🔴 Offline** | `TIMEOUT` | **Tempo Esgotado.** O site demorou mais de 10 segundos para responder. Geralmente indica servidor travado ou internet muito lenta. |
| **🔴 Offline** | `ERR / DNS` | **Erro de Rede.** O navegador não conseguiu encontrar o servidor. Geralmente acontece quando a URL está digitada errada ou o domínio deixou de existir. |

---

## 🚀 Como Rodar o Projeto

### Pré-requisitos
* **Node.js** instalado.

### Passo a Passo
1.  Instalar dependências:
    ```bash
    npm install
    ```
2.  Rodar servidor de desenvolvimento:
    ```bash
    npm run dev
    ```
    O projeto abrirá em `http://localhost:5173`.
3.  Gerar versão para Produção (Deploy):
    ```bash
    npm run build
    ```
    Isso criará uma pasta `dist` pronta para ser arrastada para o Netlify ou Vercel.

---

## 🛠️ Tecnologias Utilizadas

* **React**: Biblioteca principal para construção da interface.
* **Vite**: Ferramenta de build rápida e leve.
* **Tailwind CSS**: Framework de estilização para o design moderno.
* **Lucide React**: Biblioteca de ícones.
* **SheetJS (XLSX)**: Biblioteca para leitura e criação de ficheiros Excel no navegador.

---

## 📝 Observações Importantes

* **Limitações de Navegador:** Por rodar 100% no navegador (Client-Side), a ferramenta depende de **Proxies Públicos** (`corsproxy.io` e `allorigins`). Em momentos de alto tráfego na internet, a verificação pode ficar um pouco mais lenta.
* **Privacidade:** Nenhum dado das planilhas é enviado para servidores externos de armazenamento. Todo o processamento do Excel acontece na **memória do seu computador**.
* Desenvolvido para fins educacionais e de monitorização.
