# Embaresa QC v1.5.1 — Changelog

## 🐛 Bug crítico corrigido

**Sintoma:** Botão "Avançar →" não fazia nada no Passo 1 (mesmo com tudo preenchido) e botões "📷 Tirar Foto" também não reagiam.

**Causa:** A funcionalidade do Modo Revisão (v1.4.5) introduziu literais `</script>` e `<!--` dentro do código JS principal. O parser HTML do browser corta o script ao encontrar `</script>`, mesmo dentro de comentários ou regex literais. Como o corte aconteceu cedo no script, **nenhuma das funções principais (`nextStep`, `capturarFoto`, `validarPasso`, etc.) chegava a ser registada**.

**Correção:** Substituídos os literais perigosos por concatenação dinâmica (`'<' + '/' + 'script'`) e uso de `split/join` em vez de regex com `<\/script` literal. Também actualizados os comentários para não conterem a sequência problemática.

**Verificação:** Carga completa do JS testada com JSDOM — todas as funções globais agora estão disponíveis.

---

## 🆕 Mudanças adicionais nesta versão

### Cliente Aernnova movido para o Passo 7
- Antes: campo "Cliente Aernnova (nome)" estava no Passo 1, junto dos operadores Embaresa
- Agora: nome + assinatura ambos no Passo 7
- **Justificação:** o representante Aernnova só aparece no fim do embalamento para inspeccionar e assinar
- Validação ajustada — Passo 1 já não exige nome do Aernnova

### Cronómetro live no topo do telemóvel
- Barra azul a aparecer no topo da app assim que avança do Passo 1 para o Passo 2
- Mostra tempo a correr em formato HH:MM:SS, actualizado a cada segundo
- Esconde-se quando carrega "📤 Enviar Relatório" (cronómetro pára)
- Persiste através de fechar/abrir a app (Auto-save guarda o início)
- Em **Modo Revisão**, fica escondida (relatório já está finalizado)

---

## 🔄 Mudanças da v1.5.0 (mantidas)

### 1. 🔗 Integração com PWA Encomendas (auto-fill encomenda)
Quando o operador (ou Sónia em Modo Revisão) escreve o nº de série EM, a app procura no `localStorage` da PWA Encomendas e preenche o nº de encomenda na hora.
**Limitação:** só funciona no portatil onde tem ambas as PWAs (no telemóvel partilhado, operador deixa em branco).

### 2. ⏱️ Cronómetro de embalamento
Início ao avançar do Passo 1, fim ao gerar relatório. Mostrado em 3 colunas no relatório.

### 3. 📸 Mínimos de fotos com guia visual
- Passo 2: 4 fotos (2 caixa vazia + 2 peça aeronáutica)
- Passo 3: 4 fotos (2 bolha+dessec + 2 alumínio)
- Passo 4: 1 foto por cada suporte declarado
- Passo 5: 5 fotos (4 lados + nº série)

### 4. ✍️ 3 assinaturas
2 operadores Embaresa + 1 cliente Aernnova. Bloqueia avanço se faltar alguma.

### 5. 🎥 Vídeos opcionais
Checkbox "Esta embalagem requer vídeos" no Passo 1. Sugere automaticamente para os 4 planos E2 conhecidos mas pode desmarcar.
