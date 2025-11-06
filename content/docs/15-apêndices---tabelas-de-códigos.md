---
title: "15. Apêndices - Tabelas de Códigos"
weight: 150
bookToc: true
---

## **15. Apêndices - Tabelas de Códigos**

Esta seção apresenta as tabelas de códigos utilizados nos campos dos arquivos GeoJSON. Todos os mapeamentos estão disponíveis em formato Excel no repositório do projeto.

---

### **15.1 Códigos de Localização (campo `local`)**

Códigos válidos para o campo `local` (array) nas features. Use estes códigos para indicar a localização do serviço/obra na rodovia.

| Código               | Descrição |
|:---------------------|:----------|
| `PISTA_NORTE`        | Pista Norte |
| `PISTA_SUL`          | Pista Sul |
| `PISTA_LESTE`        | Pista Leste |
| `PISTA_OESTE`        | Pista Oeste |
| `CANTEIRO_CENTRAL`   | Canteiro Central |
| `EIXO_LESTE-OESTE`   | Eixo Leste-Oeste |
| `EIXO_NORTE-SUL`     | Eixo Norte-Sul |
| `PISTA_EXTERNA`      | Pista Externa |
| `PISTA_INTERNA`      | Pista Interna |
| `MARGINAL_SUL`       | Marginal Sul |
| `MARGINAL_NORTE`     | Marginal Norte |
| `MARGINAL_LESTE`     | Marginal Leste |
| `MARGINAL_OESTE`     | Marginal Oeste |
| `DISPOSITIVO`        | Dispositivo (alça, trevo, etc.) |
| `ALÇA`               | Alça de acesso |
| `INTERLIGAÇÃO`       | Interligação entre rodovias |

**Total:** 16 códigos de localização

**Uso:** O campo `local` deve conter um array com pelo menos 1 código.

**Exemplos:**
- `["PISTA_NORTE"]`
- `["PISTA_NORTE", "PISTA_SUL"]`

**📄 Arquivo de referência:** `local.xlsx`

---

### **15.2 Códigos de Programa (campo `programa` - obras)**

Códigos válidos para o campo `programa` no arquivo de obras. Este campo identifica o tipo de programa ao qual a obra pertence.

| Código   | Descrição |
|:---------|:----------|
| `REVIT`  | REVITALIZACAO-CONSERVACAO ESPECIAL |
| `CAPEX`  | OBRAS CAPEX |
| `NS`     | OBRAS NS |

**Total:** 3 códigos de programa

**Descrição dos programas:**

- **REVIT:** Projetos de revitalização e conservação especial de trechos existentes
- **CAPEX:** Grandes investimentos em capital (Capital Expenditure) - novas estruturas, ampliações significativas
- **NS:** Obras por Nível de Serviço - obras necessárias para atender níveis de serviço contratuais

**Uso:** Campo obrigatório apenas no template de obras.

**Exemplo:** `"programa": "CAPEX"`

**📄 Arquivo de referência:** `programas.xlsx`

---

### **15.3 Códigos de Item de Serviço (campo `item` - obras)**

O campo `item` no arquivo de obras aceita códigos numéricos de 1 a 90, cada um representando um tipo específico de serviço ou obra.

**Total:** 90 códigos de item

**Exemplos de itens:**

- **1:** Passarela de Pedestres
- **2:** Construção de praça de pedágio
- **3:** Implantação de terceira faixa
- **4:** Reforma de posto de pesagem
- **5:** Construção de área de escape
- ... (85 itens adicionais)

> **⚠️ IMPORTANTE:** Devido ao grande número de códigos (90 itens), a lista completa não está reproduzida neste manual.

**Consulte o arquivo de referência completo:**

- **Excel:** `item.xlsx`
- **Template Excel:** Aba "Item" no template `template_obras_2026_R0.xlsx`

**Uso:** Campo obrigatório no template de obras.

**Exemplo:** `"item": 1` (número inteiro de 1 a 90)

**Como encontrar o item correto:**

1. Abra o arquivo `item.xlsx`
2. Use `Ctrl+F` (ou `Cmd+F`) para buscar palavras-chave relacionadas à sua obra
3. Identifique o código numérico correspondente
4. Use esse número no campo `item`

---

### **15.4 Códigos de Rodovia (campo `rodovia`)**

O campo `rodovia` aceita códigos no padrão DER/SP (sem hífen) seguindo 4 formatos diferentes, conforme detalhado na seção 4.4.3 deste manual.

**Total:** 2.115 códigos de rodovia catalogados

**Formatos aceitos:**

1. `SP` + 7 dígitos
   **Exemplos:** `SP0000280`, `SP0000019`

2. `SPM` + 5 dígitos + letra
   **Exemplos:** `SPM00021D`, `SPM00280E`

3. 2 dígitos + `SPD` + 6 dígitos
   **Exemplos:** `01SPD001067`, `02SPD001067`

4. 3 letras + 6 dígitos + letra opcional
   **Exemplos:** `SPA004257`, `SPD000054`, `ADD000030`

> **⚠️ IMPORTANTE:** Devido ao grande número de códigos (2.115 rodovias), a lista completa não está reproduzida neste manual.

**Consulte o arquivo de referência completo:**

- **Excel:** `rodovias.xlsx`
- **Template Excel:** Aba "Rodovias" nos templates

**Formato do arquivo de referência:**

| Coluna    | Conteúdo                          | Exemplo      |
|:----------|:----------------------------------|:-------------|
| `codigo`  | Código para usar no GeoJSON       | `SP0000280`  |
| `rodovia` | Nome amigável da rodovia          | `SP 280`     |

**Como encontrar o código correto:**

1. Abra o arquivo `rodovias.xlsx`
2. Use `Ctrl+F` (ou `Cmd+F`) para buscar pelo número ou nome da rodovia
3. Copie o valor da coluna **`codigo`** (não da coluna `rodovia`)
4. Use esse código no campo `rodovia` do GeoJSON

**Exemplo de busca:**

```
Preciso do código da Rodovia Castello Branco (SP-280)

1. Busco: "280" ou "castello"
2. Encontro linha: codigo=SP0000280, rodovia=SP 280
3. Uso no GeoJSON: "rodovia": "SP0000280"
```

**Validação:** Todos os códigos são validados pelo schema contra o padrão regex:

```regex
^(\d{2}SPD\d{6}|SP\d{7}|SPM\d{5}[A-Z]|[A-Z]{3}\d{6}[A-Z]?)$
```

---

### **15.5 Downloads dos Arquivos de Referência**

Todos os arquivos de códigos mencionados nesta seção estão disponíveis para download em:

**📥 Link:** [https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)

**Arquivos disponíveis:**

- `local.xlsx` - códigos de localização
- `programas.xlsx` - códigos de programa
- `item.xlsx` - códigos de item de serviço
- `rodovias.xlsx` - códigos de rodovia
- `validar_geojson.py` - script de validação (veja seção 9.1.3)

> **💡 Recomendação:** Mantenha estes arquivos de referência e o script de validação acessíveis durante todo o processo de preparação dos dados GeoJSON.

---