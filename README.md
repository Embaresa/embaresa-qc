# Embaresa QC — App de Relatório de Embalamento

PWA (Progressive Web App) para registo de embalamento e controlo de qualidade na Embaresa PT.

---

## 📦 Conteúdo do pacote

```
embaresa-qc/
├── index.html              ← A app (HTML + CSS + JS)
├── manifest.json           ← Define nome, ícone, cor da app instalada
├── service-worker.js       ← Permite uso offline
├── icons/
│   ├── icon-192.png
│   ├── icon-512.png
│   └── icon-512-maskable.png
└── README.md               ← Este ficheiro
```

---

## ⚙️ ANTES DE PUBLICAR — Editar email destinatário

Abra `index.html` num editor de texto e procure pela linha:

```js
emailDestino: 'sonia@embaresa.pt',
```

Substitua pelo seu email real. Esta é a única configuração que precisa de mudar antes de publicar.

---

## 🚀 PASSO 1 — Publicar no GitHub Pages

Como já tem outras PWAs no GitHub, este passo deve ser familiar. Se for um repositório novo:

1. Crie um repositório no GitHub, por exemplo: `embaresa-qc`
2. Faça upload dos 4 ficheiros + pasta `icons/` para a raiz do repositório
3. Vá a **Settings → Pages**
4. Em **Source**, escolha o branch `main` e a pasta `/ (root)`
5. Guarde. Após 1-2 minutos, o GitHub mostra-lhe a URL pública (algo como `https://[utilizador].github.io/embaresa-qc/`)

Se for adicionar a um repositório já existente, basta colocar tudo numa subpasta (ex: `embaresa-qc/`) e o URL será `https://[utilizador].github.io/[repo]/embaresa-qc/`.

⚠️ **Importante**: a URL TEM de ser HTTPS (o GitHub Pages garante isto automaticamente). Sem HTTPS, a PWA não pode ser instalada.

---

## 📲 PASSO 2 — Instalar no telemóvel

### iPhone (iOS Safari)

1. Abra a URL no **Safari** (não funciona no Chrome iOS)
2. Toque no ícone de **Partilhar** (📤) na barra inferior
3. Role para baixo e toque em **"Adicionar ao Ecrã Principal"**
4. Toque em **Adicionar**

A app aparece no ecrã principal como uma aplicação normal com o ícone "EQ".

### Android (Chrome / Edge)

1. Abra a URL no **Chrome** ou **Edge**
2. Apareça automaticamente um banner "Adicionar à página inicial" — toque em **Instalar**
3. Em alternativa: menu (⋮) → **Instalar app** ou **Adicionar ao ecrã principal**

A app fica instalada como qualquer outra. Pode aparecer também na gaveta de aplicações.

### Verificar que a instalação correu bem

Depois de instalar, abrir a app pelo ícone deve mostrar:
- Sem barra de URL do browser (ecrã inteiro)
- Ícone "EQ" no topo do ecrã (Android)
- Funciona offline depois da primeira abertura

---

## 🔄 PASSO 3 — Actualizar a app

Quando publicar uma nova versão (alterações ao código):

1. Suba os ficheiros novos para o repositório GitHub (substituir os antigos)
2. **Importante**: edite o `service-worker.js` e mude a linha:
   ```js
   const CACHE_VERSION = 'embaresa-qc-v1';
   ```
   Para:
   ```js
   const CACHE_VERSION = 'embaresa-qc-v2';
   ```
   (Incremente o número.) Isto força os telemóveis a apagar a cache antiga e descarregar a versão nova.
3. Os operadores recebem a versão nova automaticamente quando reabrirem a app com rede.

---

## 📋 Como usar a app (operadores)

A app guia-os passo a passo. Em cada um dos 9 passos só vêem o que têm de fazer naquele momento:

1. **Identificação** — escolher plano, anotar série, foto do nº de série da caixa
2. **Estado Inicial** — foto da peça antes da protecção (E2: + vídeo Fase 1)
3. **Protecção** — foto da película/dessecantes/alumínio aplicados
4. **Posicionamento** — foto da peça nos suportes (E2: + vídeo Fase 2)
5. **Embalagem Final** — foto da embalagem completa + foto da etiqueta (E2: + vídeo Fase 3)
6. **Checklist** — 4 secções de verificação (Bastidor / Madeira / Suportes / Protecção)
7. **Dimensões e Observações**
8. **Assinaturas** — operador Embaresa + colaborador Aernnova
9. **Envio** — abre Outlook/Mail com o relatório anexado

### Vídeos E2 (Aernnova)

Apenas para os 4 planos E2 (Upper/Lower DCHA/IZDA), aparecem blocos laranja a pedir os 3 vídeos:

1. Operador grava o vídeo com a câmara do telemóvel (botão "Gravar Vídeo" abre a câmara)
2. Vídeo fica na galeria
3. Operador abre a app **OneDrive** no telemóvel
4. Faz upload do vídeo da galeria para a pasta partilhada da Embaresa
5. Toca em **Partilhar → Copiar Ligação** (deixa "Qualquer pessoa com a ligação pode ver")
6. Volta à app Embaresa QC e cola a ligação no campo

Estrutura recomendada de pastas no OneDrive:
```
Vídeos Embalamento E2/
└── 2026/
    └── 04 - Abril/
        └── EM1663_230324R2/
            ├── FASE1_INICIO.mp4
            ├── FASE2_INTER.mp4
            └── FASE3_FINAL.mp4
```

### Modo offline

A app funciona sem rede para preencher dados, tirar fotos e fazer assinaturas. Quando o operador estiver sem rede, aparece um banner vermelho a avisar. Quando voltar a ter rede, pode enviar normalmente.

⚠️ **Limitação**: o upload de vídeos para o OneDrive precisa **sempre** de internet. Não há forma de contornar isto.

---

## 🛠️ Resolução de problemas

| Problema | Solução |
|---|---|
| App não instala no iPhone | Tem de abrir no **Safari**, não no Chrome. O Chrome iOS não permite instalar PWAs. |
| Não aparece o banner "Instalar" no Android | Menu (⋮) → "Adicionar ao ecrã principal" funciona sempre |
| App não actualiza | Verificar que aumentou o `CACHE_VERSION` no `service-worker.js` antes de publicar |
| Botão "Instalar" desapareceu | Provavelmente já está instalada. Verifique o ecrã principal do telemóvel |
| Câmara não abre | Permitir acesso à câmara nas definições do site no browser |
| Vídeo não toca quando se clica no link no relatório | Verificar que a partilha do OneDrive está como "Qualquer pessoa com a ligação" |

---

## 📞 Suporte

Se encontrar bugs ou tiver pedidos de funcionalidades, faça uma issue no repositório GitHub.

---

**Versão actual:** 1.0.0
**Última actualização:** Abril 2026
