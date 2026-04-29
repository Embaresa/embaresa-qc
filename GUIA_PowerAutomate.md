# Guia Power Automate — Embaresa QC

Configuração dos **2 fluxos** que automatizam o upload e busca de vídeos do projecto E2.

---

## 📋 Resumo da arquitectura

```
Telemóvel grava vídeo
       ↓
OneDrive Camera Upload (auto)
       ↓
┌─ FLUXO 1: Embalamento_VideoUpload_Process
│  Detecta o vídeo, organiza, cria link, regista em SharePoint List
└──────────────────────────────────────────────
       
PWA Embaresa QC pede vídeo
       ↓
┌─ FLUXO 2: Embalamento_GetLatestVideo (HTTP)
│  Devolve o(s) vídeo(s) mais recente(s) à app
└──────────────────────────────────────────────
```

---

## 📦 PRÉ-REQUISITOS

Antes de criar os fluxos, certifique-se que tem:

### A. Pastas no OneDrive / SharePoint

Crie esta estrutura no **OneDrive ou SharePoint da Embaresa**:

```
Documentos da Embaresa/
├── Embalamento-Inbox/                  ← onde a câmara despeja
└── Vídeos Embalamento E2/              ← arquivo organizado
    └── (fluxo cria subpastas Ano/Mês automaticamente)
```

A pasta `Embalamento-Inbox` é onde o OneDrive Camera Upload do telemóvel vai sincronizar todos os vídeos. Veja o **Guia de Configuração do Telemóvel** para activar.

### B. Lista SharePoint para os links

1. Vá ao SharePoint → escolha o site da Embaresa
2. Crie uma **Lista** chamada **"Embaresa QC - Links Vídeos"** com estas colunas:

| Coluna | Tipo | Notas |
|---|---|---|
| `Title` | Linha de texto | (já existe por defeito — usar para nome do ficheiro) |
| `Link` | Hiperligação | URL de partilha do vídeo |
| `DataHora` | Data e Hora | quando o ficheiro foi processado |
| `NomeFicheiro` | Linha de texto | nome original |
| `Tamanho` | Número | em MB |

Guarde o **URL da lista** — vai precisar nos fluxos.

---

## 🔧 FLUXO 1 — Detectar e processar vídeos novos

### Criar o fluxo

1. Vá a **Power Automate** → **Os meus fluxos** → **+ Novo fluxo** → **Fluxo de cloud automatizado**
2. Nome: `Embalamento_VideoUpload_Process`
3. Trigger: pesquise **"Quando um ficheiro é criado (OneDrive para Empresas)"**
4. Clicar em **Criar**

### Configurar o trigger

| Campo | Valor |
|---|---|
| Pasta | `/Documentos da Embaresa/Embalamento-Inbox` |
| Incluir subpastas | Sim |

### Acção 1: Filtrar apenas vídeos

Adicionar acção **"Condição"**:

```
@or(
  endsWith(toLower(triggerOutputs()?['headers/x-ms-file-name']), '.mp4'),
  endsWith(toLower(triggerOutputs()?['headers/x-ms-file-name']), '.mov'),
  endsWith(toLower(triggerOutputs()?['headers/x-ms-file-name']), '.3gp')
)
```

⚠️ Se a expressão acima for muito complexa de meter, faça em alternativa via interface:

- Adicione 3 condições "OR" separadas
- Cada uma: `nome do ficheiro termina com` `.mp4` (depois `.mov`, depois `.3gp`)

Tudo o que vem a seguir vai dentro do ramo **"Se sim"** da condição.

### Acção 2: Compor timestamp

Adicionar acção **"Compor"**:

| Campo | Valor |
|---|---|
| Nome | `TimestampISO` |
| Entradas | `@{formatDateTime(utcNow(), 'yyyy-MM-ddTHH-mm-ss')}` |

Adicionar segunda **"Compor"**:

| Campo | Valor |
|---|---|
| Nome | `AnoMes` |
| Entradas | `@{formatDateTime(utcNow(), 'yyyy/MM')}` |

### Acção 3: Criar pasta de destino (se não existir)

Adicionar acção **"Criar pasta nova (OneDrive para Empresas)"**:

| Campo | Valor |
|---|---|
| Caminho da pasta | `/Documentos da Embaresa/Vídeos Embalamento E2/@{outputs('AnoMes')}/` |

⚠️ Se a pasta já existir, esta acção devolve erro mas o fluxo continua. Para evitar isto, configure a acção **"Configurar com falha contínua"** (clicar nos 3 pontos da acção → Configurar execução depois → Marque "tem falhas"). Isto faz o fluxo seguir mesmo que a pasta já exista.

### Acção 4: Mover o ficheiro

Adicionar acção **"Mover ficheiro (OneDrive para Empresas)"**:

| Campo | Valor |
|---|---|
| Ficheiro | (do trigger) `Identificador` |
| Pasta de destino | `/Documentos da Embaresa/Vídeos Embalamento E2/@{outputs('AnoMes')}` |
| Comportamento se já existir | `Substituir` |

### Acção 5: Criar link de partilha

Adicionar acção **"Criar ligação de partilha (OneDrive para Empresas)"**:

| Campo | Valor |
|---|---|
| Ficheiro | (output da acção "Mover ficheiro") `Id` |
| Tipo de ligação | `Vista` |
| Âmbito da ligação | `Anónimo` |

⚠️ Se o tenant da Embaresa **não permitir links anónimos**, use `Organização` em vez de `Anónimo`. Os colaboradores Aernnova precisarão de estar autenticados Microsoft 365 para abrir os links — ou IT terá de criar utilizadores convidados.

### Acção 6: Registar na Lista SharePoint

Adicionar acção **"Criar item (SharePoint)"**:

| Campo | Valor |
|---|---|
| Endereço do site | (URL do site SharePoint da Embaresa) |
| Nome da lista | `Embaresa QC - Links Vídeos` |
| Title | `@{triggerOutputs()?['headers/x-ms-file-name']}` |
| Link | (output da acção "Criar ligação de partilha") `Web Url` |
| DataHora | `@{utcNow()}` |
| NomeFicheiro | `@{triggerOutputs()?['headers/x-ms-file-name']}` |
| Tamanho | `@{div(triggerOutputs()?['headers/Content-Length'], 1048576)}` |

### Guardar e testar

1. Clicar em **Guardar**
2. Para testar: vá ao seu telemóvel, grave um vídeo curto, espere o OneDrive sincronizar
3. Volte ao Power Automate, em "Os meus fluxos" verifique que o fluxo correu (✓ verde)

---

## 🔧 FLUXO 2 — Devolver vídeos recentes à app

### Criar o fluxo

1. Power Automate → **+ Novo fluxo** → **Fluxo de cloud instantâneo**
2. Nome: `Embalamento_GetLatestVideo`
3. Trigger: **"Quando um pedido HTTP é recebido"**
4. Clicar em **Criar**

### Configurar o trigger HTTP

No campo **Esquema JSON**:

```json
{
  "type": "object",
  "properties": {
    "fase": { "type": "string" },
    "timestamp": { "type": "string" }
  }
}
```

(Pode deixar vazio também — não usamos os parâmetros, mas por boa prática.)

Depois de **Guardar pela primeira vez**, o Power Automate gera o URL HTTP. **Copie esse URL** — é o que vai colar na PWA.

### Acção 1: Buscar items da Lista SharePoint

Adicionar acção **"Obter itens (SharePoint)"**:

| Campo | Valor |
|---|---|
| Endereço do site | (URL do site SharePoint) |
| Nome da lista | `Embaresa QC - Links Vídeos` |
| Consulta de filtro | `DataHora gt '@{addMinutes(utcNow(), -10)}'` |
| Ordenar por | `DataHora desc` |
| Top Count | `5` |

Esta consulta devolve os vídeos processados nos últimos 10 minutos, ordenados do mais recente para o mais antigo.

### Acção 2: Compor resposta JSON

Adicionar acção **"Selecionar"**:

| Campo | Valor |
|---|---|
| De | (output da acção anterior) `value` |
| Mapa | (escrever em modo texto): |

```json
{
  "link": "@{item()?['Link']?['Url']}",
  "nome": "@{item()?['NomeFicheiro']}",
  "dataHora": "@{item()?['DataHora']}",
  "tamanhoMB": "@{item()?['Tamanho']}"
}
```

### Acção 3: Devolver resposta

Adicionar acção **"Resposta"**:

| Campo | Valor |
|---|---|
| Código de estado | `200` |
| Cabeçalhos | `Content-Type` = `application/json` <br> `Access-Control-Allow-Origin` = `*` |
| Corpo | (Compor JSON com os videos) |

No campo **Corpo**, use:

```json
{
  "videos": @{body('Selecionar')}
}
```

⚠️ Importante: o cabeçalho `Access-Control-Allow-Origin: *` é necessário para a PWA conseguir chamar o endpoint a partir do browser (CORS).

### Acção 4: Tratamento de erro (opcional mas recomendado)

Adicionar uma **acção paralela** que corre se algo falhar — uma acção **"Resposta"** com:

| Campo | Valor |
|---|---|
| Código de estado | `500` |
| Corpo | `{"error": "Falha ao consultar lista", "videos": []}` |

Configure-a para correr "depois de uma falha" da acção anterior (3 pontos → Configurar execução).

### Guardar e copiar o URL

1. Clicar em **Guardar**
2. Voltar ao trigger "Quando um pedido HTTP é recebido"
3. **Copiar o URL HTTP POST** (visível agora) — é uma URL longa do tipo:
   ```
   https://prod-XX.westeurope.logic.azure.com:443/workflows/.../triggers/manual/paths/invoke?api-version=...
   ```
4. **Cole esse URL no `index.html` da PWA**, na linha:
   ```js
   powerAutomateEndpoint: '',  ← cole entre as plicas
   ```

---

## ✅ Como testar tudo no fim

1. **Telemóvel:**
   - Abrir OneDrive app, confirmar que Camera Upload está activo e aponta para `Embalamento-Inbox`
   - Gravar um vídeo curto qualquer com a câmara

2. **Esperar 30s-2min** (Camera Upload + Power Automate fazem o trabalho em background)

3. **Verificar no OneDrive da Embaresa:**
   - O vídeo já não está em `Embalamento-Inbox` (foi movido)
   - Está em `Vídeos Embalamento E2/2026/04/`

4. **Verificar na Lista SharePoint:**
   - Há uma nova entrada com o nome do vídeo, link e data/hora

5. **Verificar na PWA:**
   - Abrir a app no telemóvel
   - Ir ao Passo 2 (Estado Inicial), seleccionar um plano E2
   - Tocar no botão **"🔄 Buscar Vídeo Recente do OneDrive"**
   - O link deve aparecer automaticamente no campo

Se algum destes passos falhar, ver a secção **Resolução de problemas** abaixo.

---

## 🛠️ Resolução de problemas

| Sintoma | Causa provável | Solução |
|---|---|---|
| Fluxo 1 não dispara quando gravo vídeo | OneDrive Camera Upload não activado, ou aponta para pasta errada | Reconfigurar OneDrive app no telemóvel — ver guia do telemóvel |
| Fluxo 1 dispara mas falha em "Criar pasta" | Pasta já existe | Configurar acção com "falha contínua" (3 pontos → Configurar execução) |
| Fluxo 1 falha em "Criar ligação de partilha" | Tenant não permite links anónimos | Mudar de "Anónimo" para "Organização" |
| PWA mostra erro CORS | Falta `Access-Control-Allow-Origin: *` na resposta | Verificar que o cabeçalho está na acção "Resposta" do Fluxo 2 |
| PWA diz "Nenhum vídeo recente" | Filtro de 10 min falhou ou DataHora não escreveu correctamente | Verificar Lista SharePoint manualmente |
| Vídeos sobem para `Imagens da Câmara` em vez de `Embalamento-Inbox` | Camera Upload do OneDrive não permite escolher pasta destino | Aceitar isto e mudar o trigger do Fluxo 1 para `Imagens da Câmara` |

---

## 💡 Notas finais

- **Latência típica:** entre o operador parar de gravar e o link aparecer na app: 30 segundos a 2 minutos. Depende do tamanho do vídeo, qualidade da rede do telemóvel, e cadência de polling do trigger OneDrive.

- **Custo Power Automate:** estes 2 fluxos consomem ~10-50 execuções por dia (depende do volume). Está perfeitamente dentro dos limites mesmo do plano standard, ainda mais com Premium.

- **Cleanup periódico:** sugiro criar mais tarde um terceiro fluxo (calendarizado, semanal) que limpe a Lista SharePoint de entradas com mais de 30 dias — manter a lista pequena ajuda a performance da consulta.

- **Backup:** os vídeos ficam guardados em `Vídeos Embalamento E2/Ano/Mês/` e duplicam o link na Lista SharePoint. É um sistema com 2 níveis de redundância — improvável perderem-se vídeos.
