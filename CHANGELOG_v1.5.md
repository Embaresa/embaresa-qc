# Embaresa QC v1.5.0 — Changelog

## 5 grandes mudanças nesta versão

### 1. 🔗 Integração com PWA Encomendas (auto-fill encomenda)
Quando o operador (ou Sónia em Modo Revisão) escreve o nº de série EM, a app procura automaticamente no `localStorage` da PWA Encomendas (`embaresa.github.io/Gestao-encomendas/`) e preenche o nº de encomenda na hora.

**Como funciona:**
- App QC procura na chave `encomendas_embaresa_v4` do localStorage do mesmo domínio
- Encontra a encomenda pela coluna `numSerie` (case-insensitive)
- Preenche `numEncomenda` no formato `PO/POS` (ex: `9000093719/250`)
- Toast de confirmação visual

**Limitações:**
- Só funciona quando ambas as PWAs estiveram acedidas no mesmo browser (ex: portatil da Sónia)
- No telemóvel partilhado dos operadores, não funciona (não tem a PWA Encomendas)
- **Solução prática:** operadores deixam encomenda em branco, Sónia preenche em Modo Revisão no portatil

### 2. ⏱️ Cronómetro de embalamento
- **Início:** automaticamente quando avança do Passo 1 para o Passo 2
- **Fim:** automaticamente quando carrega "Gerar Relatório" no Passo 8
- **Relatório:** mostra Início, Fim e Tempo Total numa secção dedicada
- Funciona para **todos os planos** (E2 e não-E2)

### 3. 📸 Mínimos de fotos com guia visual
Cada passo tem agora uma "caixa azul de instruções" mostrando o que tirar, e o sistema bloqueia o avanço se não atingir o mínimo:

| Passo | Mínimo | Detalhe |
|---|---|---|
| **Passo 2** — Estado Inicial | **4 fotos** | 2 caixa vazia (frente+lateral) + 2 peça aeronáutica |
| **Passo 3** — Protecção | **4 fotos** | 2 com bolha+dessecantes + 2 com saco alumínio |
| **Passo 4** — Posicionamento | **variável** | Operador declara nº de suportes/apoios/fixações; mínimo 1 foto por cada |
| **Passo 5** — Embalagem Final | **5 fotos** | 4 lados (frente/trás/laterais) + 1 do nº série |

**Contadores em tempo real** ("Tiradas: 3 de 4 obrigatórias", muda para verde quando completo).

### 4. ✍️ 3 assinaturas (2 operadores Embaresa + 1 cliente Aernnova)
- **Passo 1** — 2 dropdowns "Operador Embaresa 1" e "Operador Embaresa 2" + campo "Cliente Aernnova" (texto livre)
- **Passo 7** — 3 quadrados de assinatura
- **Validação** — não deixa avançar sem as 3 assinaturas; bloqueia se ambos operadores forem a mesma pessoa
- **Relatório** — secção de Assinaturas com 3 colunas

### 5. 🎥 Vídeos opcionais (substitui detecção automática E2)
- **Antes:** se plano era E2 (4 planos hardcoded), pedia vídeos
- **Agora:** todos os planos têm fotos como base; vídeos só quando cliente pedir
- **Activação:** checkbox no Passo 1 — *"🎥 Esta embalagem requer vídeos (E2 / cliente exigiu)"*
- **Sugestão automática:** se o plano seleccionado for um dos 4 E2 conhecidos, o checkbox é marcado automaticamente (mas pode ser desmarcado)
- **Vídeos E2 mantidos** — 3 fases (Início, Intermédia, Conclusão) quando activo

## Outras alterações importantes

- **Auto-save** agora grava também: cronómetro, checkbox vídeos, nº suportes, op2, e todos os campos novos
- **Modo Revisão** carrega correctamente todos os novos campos
- **JSON embebido no relatório** inclui novos campos (para Modo Revisão funcionar com relatórios v1.5)
- **Compatibilidade** — relatórios v1.4.x não podem ser abertos em Modo Revisão (não têm o JSON embebido). A partir de v1.5, todos os novos relatórios podem ser editados.
