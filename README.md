# ⬇️ JUCESP Downloader

Extensão para Google Chrome que automatiza o download em lote de documentos empresariais no portal [JUCESP Online](https://jucesponline.sp.gov.br).

---

## 🎯 Problema que resolve

O portal da JUCESP exige que o usuário busque cada empresa individualmente pelo NIRE, selecione o tipo de documento e clique em emitir — um processo repetitivo e manual. Em contextos que exigem consulta de dezenas ou centenas de empresas (como cartórios, escritórios jurídicos e contabilidades), esse fluxo consome horas de trabalho.

O **JUCESP Downloader** automatiza todo esse processo: o usuário informa uma lista de NIREs, escolhe as pastas de destino e os tipos de documento, e a extensão realiza todas as buscas, emissões e downloads automaticamente.

---

## ✨ Funcionalidades

- 🔁 **Download em lote** — processa múltiplos NIREs em sequência, sem intervenção manual
- 📄 **3 tipos de documento** — Ficha Cadastral Completa, Ficha Cadastral Simplificada e Certidão Simplificada
- 📁 **Pasta por NIRE** — cada empresa pode ter sua própria pasta de destino no computador local
- 💾 **Salva direto no disco** — usa a File System Access API para gravar PDFs localmente sem caixas de diálogo repetidas
- 🔄 **Retomada de estado** — se o navegador for reiniciado durante o processo, a extensão recupera o progresso automaticamente
- 📊 **Log em tempo real** — painel visual com barra de progresso, porcentagem e mensagens por etapa
- ⏹️ **Parada a qualquer momento** — botão de interrupção que encerra o processo com segurança

---

## 🛠️ Tecnologias utilizadas

| Tecnologia | Uso |
|---|---|
| **Chrome Extensions Manifest V3** | Arquitetura da extensão |
| **Service Worker** (`background.js`) | Orquestração do fluxo de automação |
| **Content Script** (`content.js`) | Interação com o DOM do portal JUCESP |
| **chrome.scripting** | Injeção e execução de código na aba do portal |
| **File System Access API** | Escrita de PDFs diretamente no sistema de arquivos |
| **IndexedDB** | Persistência dos handles de pasta entre sessões |
| **chrome.storage.local** | Recuperação de estado após reinicialização |
| **Fetch API** | Download do PDF via POST com credenciais de sessão |

---

## 🚀 Como instalar

> A extensão não está publicada na Chrome Web Store — ela é instalada em modo desenvolvedor.

1. Faça o download ou clone este repositório
2. Abra o Chrome e acesse `chrome://extensions`
3. Ative o **Modo do desenvolvedor** (canto superior direito)
4. Clique em **"Carregar sem compactação"**
5. Selecione a pasta do projeto

---

## 📖 Como usar

1. Acesse [jucesponline.sp.gov.br](https://jucesponline.sp.gov.br) e faça login
2. Clique no ícone da extensão para abrir o popup
3. Cole a lista de NIREs (um por linha) no campo esquerdo
4. Para cada NIRE, selecione a pasta de destino clicando no botão correspondente
5. Marque os tipos de documento desejados
6. Clique em **▶ Iniciar Download**

A extensão abre automaticamente a página de cada empresa, emite os documentos selecionados e salva os PDFs nas pastas indicadas.

---

## 📂 Estrutura do projeto

```
jucesp-downloader/
├── manifest.json      # Configuração da extensão (Manifest V3)
├── background.js      # Service Worker — controla o fluxo de automação
├── content.js         # Script injetado no portal JUCESP
├── popup.html         # Interface do usuário
├── popup.js           # Lógica do popup (UI, IndexedDB, File System API)
├── popup.css          # Estilos do popup
├── offscreen.html     # Documento offscreen (compatibilidade)
├── offscreen.js       # Mantido para compatibilidade futura
└── icon48.png         # Ícone da extensão
```

---

## ⚙️ Detalhes técnicos

### Fluxo de automação

```
Usuário inicia → background.js abre aba do JUCESP
    → Preenche campo de busca com o NIRE via chrome.scripting
    → Aguarda a página de perfil da empresa carregar (polling)
    → Para cada tipo de documento selecionado:
        → Seleciona o radio button correto
        → Realiza POST direto para o endpoint de PDF com a sessão ativa
        → Converte o PDF para base64 na página
        → Envia para popup.js salvar via File System Access API
    → Avança para o próximo NIRE
```

### Por que a escrita é feita no popup?

A File System Access API requer um contexto com permissão de usuário ativo. Service Workers e offscreen documents não têm esse contexto, então o `background.js` delega a escrita para o `popup.js`, que mantém o handle de pasta com permissão `readwrite` via IndexedDB.

---

## ⚠️ Aviso

Esta extensão foi desenvolvida para uso interno em ambiente profissional. Utilize-a de acordo com os termos de uso do portal JUCESP e a legislação vigente.

---

## 👩‍💻 Autora

Desenvolvido por **Gabrielly** — automatizações para o ambiente cartorial.
