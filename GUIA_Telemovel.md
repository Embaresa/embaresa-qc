# Guia de Configuração do Telemóvel — Embaresa QC

Configuração inicial do telemóvel partilhado dos operadores E2. **Fazer apenas uma vez.**

---

## 🎯 Objectivo

No fim destes passos, o telemóvel terá:
1. ✅ A app **Embaresa QC** instalada como ícone no ecrã
2. ✅ A app **OneDrive** ligada à conta empresarial
3. ✅ Carregamento da Câmara automático para uma pasta da Embaresa
4. ✅ Tudo o que for gravado pela câmara vai automaticamente para o OneDrive da empresa

---

## 📱 Passo 1 — Login OneDrive empresarial

1. Instalar a app **Microsoft OneDrive** da Play Store / App Store (caso não esteja)
2. Abrir e fazer login com a **conta Microsoft 365 da Embaresa** (não conta pessoal!)
   - Ex: `embalamento@embaresa.pt` ou similar
   - Usar uma conta partilhada se vários operadores usarem o telemóvel
3. Confirmar que a conta corporativa aparece como conta principal

⚠️ **Crítico:** se o operador tiver uma conta pessoal Microsoft já configurada (Hotmail/Outlook.com), **não usar essa conta**. Tem de ser a conta da Embaresa.

---

## 📷 Passo 2 — Activar Carregamento da Câmara

### iPhone

1. Abrir app OneDrive
2. Tocar no ícone de **conta** (canto superior direito)
3. Tocar em **Definições**
4. Tocar em **Carregar Câmara**
5. **Activar** o interruptor "Carregar Câmara"
6. Em **Conta para a qual carregar**, escolher a **conta da Embaresa**
7. Activar **"Incluir Vídeos"** (importante!)
8. Em **Apenas com Wi-Fi**: deixar **desligado** (queremos upload sempre, mesmo com dados móveis)

### Android

1. Abrir app OneDrive
2. Tocar no ícone **eu** (canto inferior direito)
3. Tocar em **Definições**
4. Tocar em **Carregamento da câmara**
5. Activar **"Carregamento da câmara"**
6. Verificar que a conta destino é a da **Embaresa**
7. Activar **"Incluir vídeos"**
8. Em **Carregar usando**: escolher **Wi-Fi e dados móveis**

---

## 📁 Passo 3 — Confirmar pasta de destino

O OneDrive faz upload para uma pasta chamada **"Imagens da Câmara"** (Camera Roll) por defeito, dentro da conta Microsoft à qual está ligado.

**No PC** (mais fácil de verificar):

1. Abrir https://onedrive.live.com no browser
2. Login com a mesma conta Embaresa
3. Verificar que existe uma pasta **"Imagens da Câmara"** ou **"Camera Roll"**

⚠️ O Power Automate tem de apontar **a esta pasta** no Fluxo 1 — ou então criar uma regra para mover de "Imagens da Câmara" para "Embalamento-Inbox" antes do processamento. Ver Guia Power Automate.

---

## 🎯 Passo 4 — Teste do upload automático

1. No telemóvel, abrir a câmara nativa (não pela app Embaresa QC)
2. Gravar um vídeo curto de 5 segundos
3. Voltar ao OneDrive app
4. Esperar 30-60 segundos
5. Abrir a pasta **Imagens da Câmara** — o vídeo deve estar lá

Se o vídeo não aparecer ao fim de 2 minutos:
- Verificar que o telemóvel tem rede (Wi-Fi ou dados)
- Verificar que o Carregamento da Câmara está mesmo activado
- Verificar que está logado na conta Embaresa (não pessoal)

---

## 📲 Passo 5 — Instalar a PWA Embaresa QC

### iPhone (Safari)

1. Abrir **Safari** (não Chrome!)
2. Ir à URL: `https://[utilizador-github].github.io/embaresa-qc/`
3. Tocar no botão **Partilhar** (📤) na barra inferior
4. Tocar em **"Adicionar ao Ecrã Principal"**
5. Confirmar com **Adicionar**

### Android (Chrome)

1. Abrir **Chrome**
2. Ir à URL: `https://[utilizador-github].github.io/embaresa-qc/`
3. Aparece um banner **"Adicionar à página inicial"** — tocar em **Instalar**
4. Em alternativa: menu (⋮) → **Instalar app**

A app fica disponível no ecrã principal com o ícone "EQ" cyan.

---

## 🔐 Passo 6 — Permissões

Na primeira vez que abrir a app **Embaresa QC** e tocar em "Tirar Foto":

1. O telemóvel pergunta: **"Permitir acesso à câmara?"** → **Sim**
2. Pode também perguntar: **"Permitir acesso aos ficheiros?"** → **Sim**

Se o operador disser "Não" por engano, pode reverter:
- iPhone: Definições → Safari → Câmara → Permitir
- Android: Definições → Apps → Chrome → Permissões → Câmara → Permitir

---

## ✅ Lista de verificação final

Antes de entregar o telemóvel a operadores, confirme:

- [ ] OneDrive logado com conta Embaresa (não pessoal)
- [ ] Carregamento da Câmara **activado** com vídeos incluídos
- [ ] Não tem restrições de "apenas Wi-Fi" para upload
- [ ] Pasta "Imagens da Câmara" existe no OneDrive da Embaresa
- [ ] PWA Embaresa QC instalada no ecrã principal
- [ ] Câmara tem permissão dada
- [ ] Teste: gravado um vídeo, apareceu em OneDrive em < 2 min
- [ ] Power Automate Fluxo 1 e Fluxo 2 criados e activos
- [ ] URL do Fluxo 2 colado no `index.html` da PWA (campo `powerAutomateEndpoint`)

---

## 💡 Para os operadores no dia-a-dia

Imprimir e afixar no posto de embalamento:

```
┌─────────────────────────────────────────────────┐
│   📦 EMBALAMENTO E2 — INSTRUÇÕES RÁPIDAS        │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. Abrir app "Embaresa QC" no telemóvel       │
│                                                 │
│  2. Seguir os 9 passos do assistente:          │
│     • Foto da caixa (nº série)                  │
│     • Foto + 🎥 vídeo Fase 1 (início)           │
│     • Foto da protecção                         │
│     • Foto + 🎥 vídeo Fase 2 (intermédia)       │
│     • Foto + 🎥 vídeo Fase 3 (conclusão)        │
│     • Checklist                                 │
│     • Dimensões                                 │
│     • Assinaturas (operador + Aernnova)         │
│     • Enviar                                    │
│                                                 │
│  💡 Os vídeos sobem sozinhos. Tocar em          │
│     "🔄 Buscar Vídeo Recente" para colar        │
│     o link automaticamente.                     │
│                                                 │
│  ❓ Dúvidas: contactar Sónia Madeira            │
│                                                 │
└─────────────────────────────────────────────────┘
```
