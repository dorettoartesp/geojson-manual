---
title: "4. Requisitos Específicos ARTESP"
weight: 40
bookToc: true
---

## **4. Requisitos Específicos ARTESP**

### **4.1 Arquivos Obrigatórios**

As concessionárias devem entregar **dois arquivos GeoJSON separados por lote**:

| Arquivo                                     | Conteúdo                                      | Schema de Validação         |
|:--------------------------------------------|:----------------------------------------------|:----------------------------|
| `L<lote>_conservacao_2026_R0.geojson`      | Programação Anual de Conservação              | `conserva.schema.r0.json`   |
| `L<lote>_obras_2026_R0.geojson`            | Programação Anual de Obras e Revitalização    | `obras.schema.r0.json`      |

**Padrão de nomenclatura:** `L<lote>_<tipo>_2026_R0.geojson`

---

### **4.2 Sistema de Coordenadas**

**⚠️ Obrigatório:** SIRGAS 2000 (EPSG:4674)

**Exemplo de declaração CRS:**

```json
"crs": {
  "type": "name",
  "properties": {
    "name": "urn:ogc:def:crs:EPSG::4674"
  }
}
```

> **Ordem das coordenadas:** `[longitude, latitude]` (em graus decimais)
>
> **Formato CRS:** Use o identificador URN completo `urn:ogc:def:crs:EPSG::4674` conforme especificação OGC

---

### **4.3 Codificação e Formatação**

| Requisito                | Especificação |
|:-------------------------|:--------------|
| **Codificação**          | UTF-8 sem BOM (Byte Order Mark) |
| **Separador decimal**    | Ponto (`.`) - nunca vírgula |
| **Formato de datas**     | `YYYY-MM-DD` (ex: `2026-03-15`) |
| **JSON Schema**          | Draft 2020-12 |

---

### **4.4 Estrutura de Campos**

#### **4.4.1 Campos Obrigatórios - Conservação**

| Campo                       | Tipo         | Descrição | Exemplo |
|:----------------------------|:-------------|:----------|:--------|
| **`id`**                    | **string**   | **(Obrigatório)** Identificador único do serviço (máx. 50 caracteres). Deve ser único em todo o arquivo GeoJSON. | `"conserva-001"` |
| **`lote`**                  | string       | Identificador do lote (formato: L + exatamente 2 dígitos, ex: L01, L13, L22). | `"L13"` |
| **`rodovia`**               | string       | Código da rodovia. Consulte os códigos válidos na planilha `rodovias.xlsx` disponível no Portal de Dados Abertos ([dadosabertos.artesp.sp.gov.br](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)). | `"SP0000280"` |
| **`item`**                  | string       | Código do item de serviço (ex: a.1.2). | `"a.1.2"` |
| **`detalhamento_servico`**  | string       | Descrição clara do serviço. | `"Recuperação de pavimento"` |
| **`unidade`**               | string       | Unidade de medida (km, m, m2, etc.). | `"km"` |
| **`quantidade`**            | number       | Quantidade planejada (até 3 casas decimais). | `15.500` |
| **`km_inicial`**            | number       | Quilometragem inicial (até 3 casas decimais). | `125.000` |
| **`km_final`**              | number       | Quilometragem final (até 3 casas decimais). | `140.500` |
| **`local`**                 | array        | Códigos de localização (mínimo 1 item). | `["PISTA_NORTE", "PISTA_SUL"]` |
| **`data_inicial`**          | string       | Data de início (formato: YYYY-MM-DD). | `"2026-03-15"` |
| **`data_final`**            | string       | Data de término (formato: YYYY-MM-DD). | `"2026-07-20"` |
| **`observacoes_gerais`**    | string/null  | Observações gerais (usar `null` se não houver). | `null` |

---

#### **4.4.2 Campos Obrigatórios - Obras**

| Campo                       | Tipo         | Descrição | Exemplo |
|:----------------------------|:-------------|:----------|:--------|
| **`id`**                    | **string**   | **(Obrigatório)** Identificador único da obra (máx. 50 caracteres). Deve ser único em todo o arquivo GeoJSON. | `"obra-101"` |
| **`lote`**                  | string       | Identificador do lote (formato: L + exatamente 2 dígitos, ex: L01, L13, L22). | `"L22"` |
| **`rodovia`**               | string       | Código da rodovia. Consulte os códigos válidos na planilha `rodovias.xlsx` disponível no Portal de Dados Abertos ([dadosabertos.artesp.sp.gov.br](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)). | `"SP0000280"` |
| **`programa`**              | string       | Tipo de programa (REVIT, CAPEX, NS). | `"CAPEX"` |
| **`item`**                  | integer      | Número do item de serviço. | `5` |
| **`subitem`**               | integer      | Número do subitem de serviço. | `2` |
| **`detalhamento_servico`**  | string       | Descrição da obra/intervenção. | `"Construção de passarela"` |
| **`unidade`**               | string       | Unidade de medida. | `"un"` |
| **`quantidade`**            | number       | Quantidade planejada (até 3 casas decimais). | `1.000` |
| **`km_inicial`**            | number       | Quilometragem inicial. | `225.000` |
| **`km_final`**              | number       | Quilometragem final. | `225.100` |
| **`local`**                 | array        | Códigos de localização. | `["PISTA_SUL", "DISPOSITIVO"]` |
| **`data_inicial`**          | string       | Data de início (formato: YYYY-MM-DD). | `"2026-01-20"` |
| **`data_final`**            | string       | Data de término (formato: YYYY-MM-DD). | `"2026-11-30"` |
| **`observacoes_gerais`**    | string/null  | Observações gerais (usar `null` se não houver). | `"Obra em parceria com prefeitura"` |

---

#### **4.4.3 Valores Permitidos**

**Formatos de código de rodovia (v1.1):**

O campo `rodovia` aceita 4 formatos:

1. **Formato 1:** `SP` + 7 dígitos
   **Exemplo:** `SP0000280`

2. **Formato 2:** `SPM` + 5 dígitos + letra
   **Exemplo:** `SPM00021D`

3. **Formato 3:** 2 dígitos + `SPD` + 6 dígitos
   **Exemplo:** `01SPD001067`

4. **Formato 4:** 3 letras + 6 dígitos + letra opcional
   **Exemplo:** `SPA004257`, `ADD000030`, `SPA198255E`

**Validação completa (Regex):**

```regex
^(\d{2}SPD\d{6}|SP\d{7}|SPM\d{5}[A-Z]|[A-Z]{3}\d{6}[A-Z]?)$
```

---

### **4.5 Metadados Obrigatórios**

Todo arquivo GeoJSON deve incluir o bloco `metadata`:

**Exemplo de bloco metadata:**

```json
"metadata": {
  "schema_version": "R0",
  "data_geracao": "2025-11-05T10:30:00-03:00"
}
```

**Campos do metadata:**

| Campo                | Tipo   | Descrição | Exemplo |
|:---------------------|:-------|:----------|:--------|
| **`schema_version`** | string | Versão do schema. Especifica qual revisão do schema está sendo usada (R0, R1, R2, etc.). Use a versão correspondente ao schema de validação. | `"R0"` |
| **`data_geracao`**   | string | Data e hora de geração do arquivo com timezone (RFC3339). | `"2025-11-05T10:30:00-03:00"` |

---