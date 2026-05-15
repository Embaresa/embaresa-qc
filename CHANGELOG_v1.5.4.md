# Embaresa QC v1.5.4 — Salvaguardas contra perda de dados

## 🚨 O problema que esta versão resolve

Em 14/05/2026 houve perda de um relatório quase pronto porque:
- Os operadores fizeram novos relatórios em cima do antigo
- O auto-save **só guardava 1 rascunho de cada vez** — foi sobrescrito
- O Google Photos não fez backup das fotos (sem Wi-Fi na zona de embalamento)
- Resultado: fotos perdidas, relatório irrecuperável

**Esta versão garante que isto não volta a acontecer.**

---

## 🆕 Funcionalidades novas

### 1. 📂 Múltiplos rascunhos por série
- Cada relatório iniciado fica guardado **separadamente** no IndexedDB
- A chave é o nº de série EM (ex: `EM2283`)
- Operadores podem ter vários relatórios "em curso" em simultâneo
- Botão **"🔄 Novo"** já **NÃO apaga** o trabalho actual — agora limpa o ecrã mas mantém o rascunho seguro

### 2. 📂 Botão "Rascunhos" no top-bar (com contador)
- Mostra **número de rascunhos guardados** com badge laranja
- Carregar abre **lista de todos os rascunhos** com:
  - Série EM, plano, data de última actividade
  - Etiqueta visual: 🟢 HOJE / 🟠 ONTEM / 🔴 X dias atrás
  - Passo onde estão, número de fotos
  - Botões: **📂 Abrir** | **🗑️ Descartar** (com confirmação)

### 3. 📚 Botão "Enviados" — Histórico local
- Guarda **os últimos 10 relatórios enviados** localmente (até 30 dias)
- Lista por data, série, plano, operador
- Pode **reabrir e descarregar** qualquer relatório enviado

### 4. ⚠️ Modal "Trabalho pendente de dias anteriores"
- Ao abrir a app, se houver rascunhos de **dias anteriores não enviados**, mostra modal forte
- Para cada um, mostra: série, plano, passo, idade
- Opções: **📂 Continuar este** | **🗑️ Descartar**
- Garante que ninguém esquece um relatório do dia anterior

### 5. 💾 Cópia automática das fotos para Downloads
- Cada foto tirada é também guardada na **pasta Downloads do telemóvel**
- Nome: `EmbaresaQC_EM2283_230429R1_caixaVazia_2026-05-15T15-30-45.jpg`
- Salvaguarda crítica — se o rascunho IndexedDB se perder, as fotos sobrevivem
- Ocupa algum espaço mas é o preço da segurança

---

## 🔄 Compatibilidade

- IndexedDB **bumpado para v2** — migração automática
- Rascunhos antigos da v1.5.3 podem precisar de ser recuperados manualmente (carregar "📂 Rascunhos" deve listá-los)
- Auto-limpeza:
  - Rascunhos com >60 dias → apagados automaticamente
  - Rascunhos sem dados → apagados automaticamente
  - Histórico >10 ou >30 dias → apagado automaticamente

---

## 🧪 Como testar

1. **Deploy v1.5.4** no GitHub
2. No telemóvel, **fechar app e reabrir**
3. Verificar título mostra `v1.5.4`
4. **Cenário A — Múltiplos rascunhos:**
   - Iniciar relatório com série `EM 1111`, avançar 2-3 passos
   - Carregar **"🔄 Novo"** → app deve mostrar diálogo "trabalho em curso será GUARDADO"
   - Iniciar novo com série `EM 2222`
   - Carregar **"📂 Rascunhos"** → deve mostrar ambos
   - Abrir o EM 1111 → continua de onde estava
5. **Cenário B — Fotos guardadas em Downloads:**
   - Tirar foto em qualquer passo
   - Verificar pasta Downloads do telemóvel
   - Deve aparecer `EmbaresaQC_<série>_<plano>_<passo>_<timestamp>.jpg`
6. **Cenário C — Histórico de enviados:**
   - Completar um relatório, enviar
   - Carregar **"📚 Enviados"** → relatório deve aparecer
   - Reabrir → ficheiro descarrega

---

## ⚠️ Atenção

- Esta versão guarda **muito mais coisas localmente** no telemóvel:
  - Múltiplos rascunhos
  - Cópia de cada foto na pasta Downloads
  - Histórico de 10 relatórios completos
- Pode ocupar **vários MB-GB** com o tempo
- Recomenda-se **limpar Downloads periodicamente** (todos os ficheiros começam com `EmbaresaQC_` para facilitar selecção)
