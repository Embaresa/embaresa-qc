# Embaresa QC v1.5.3 — Changelog

## 🆕 Cronómetro com pausa/retoma

### Botões Pausar / Retomar
- Na barra azul do cronómetro live (topo da app), há agora um botão **⏸️ Pausar**
- Quando pausado, a barra fica **laranja** e botão muda para **▶️ Retomar**
- O tempo mostrado é sempre o **total efectivo** (soma das sessões)

### Múltiplas sessões por embalamento
- Cada par "Início → Pausar" conta como uma **sessão**
- Embalamento pode ter 1 sessão (caso simples) ou várias (com pausas/retomas)
- App regista início + fim de cada sessão

### Relatório parcial obrigatório ao pausar
- **Quando o operador pausa**, automaticamente:
  1. Cronómetro pausa
  2. **Gera relatório parcial** com dados actuais
  3. Envia para `ssantos@embaresa.com` via Gmail dedicada
  4. Toast: "⏸️ Pausa registada. Relatório parcial enviado."
- Nome do ficheiro: `PARCIAL_Relatorio_EM-XXXX_planoXXX_data_S2.html` (S2 = nº sessão)
- Assunto email: `[PARCIAL — pausado] Relatório Embalamento — EM XXXX — XXXXX`
- Banner laranja no topo do relatório: "⏸️ RELATÓRIO PARCIAL — Embalamento ainda em curso"

### Auto-detecção de mudança de dia
- Quando a app é reaberta com cronómetro a correr:
  - Se passaram >6h ou mudou o dia → **pergunta** se deve descontar o tempo como pausa
  - OK = pausa registada, nova sessão iniciada
  - Cancelar = continuar a contar (foi trabalho contínuo)
- Evita aquele "67h de embalamento" quando se esquece de pausar à noite

### Tabela de sessões no relatório
- Se houver >1 sessão, o relatório mostra tabela detalhada:
  ```
  Sessão 1: 15/05 14:30 → 15/05 17:00 (2h 30min)
  Sessão 2: 18/05 08:30 → 18/05 09:45 (1h 15min)
  ─────────────────────────────────────
  Total efectivo: 3h 45min  (2 sessões)
  ```
- Se for sessão única, mostra apenas os 3 boxes: Início | Fim | Tempo Total

### Compatibilidade
- Relatórios v1.5.0 a v1.5.2 abrem normalmente em Modo Revisão (formato `{inicio, fim}` é convertido automaticamente para o novo `{sessoes: [...]}`)
- App detecta formato automaticamente — sem perda de dados
