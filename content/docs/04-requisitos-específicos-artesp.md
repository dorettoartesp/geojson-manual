---
title: "4. Requisitos Específicos ARTESP"
weight: 40
bookToc: true
---

## **4. Requisitos Específicos ARTESP**

### **4.1 Arquivos Obrigatórios**

As concessionárias devem entregar **dois arquivos GeoJSON separados por lote**:

| Arquivo                                     | Conteúdo                                      | Schema de Validação      |
|:--------------------------------------------|:----------------------------------------------|:-------------------------|
| `L<lote>_conservacao_2026_R0.geojson`      | Programação Anual de Conservação              | `conserva.schema.json`   |
| `L<lote>_obras_2026_R0.geojson`            | Programação Anual de Obras e Revitalização    | `obras.schema.json`      |

**Padrão de nomenclatura:** `L<lote>_<tipo>_2026_R0.geojson`

---

### **4.2 Sistema de Coordenadas**

**⚠️ Obrigatório:** SIRGAS 2000 (EPSG:4674)

**Exemplo de declaração CRS:**

```json
"crs": {
  "type": "name",
  "properties": {
    "name": "EPSG:4674"
  }
}
```

> **Ordem das coordenadas:** `[longitude, latitude]` (em graus decimais)

---

### **4.3 Codificação e Formatação**

| Requisito                | Especificação |
|:-------------------------|:--------------|
| **Codificação**          | UTF-8 sem BOM (Byte Order Mark) |
| **Separador decimal**    | Ponto (`.`) - nunca vírgula |
| **Formato de datas**     | ISO 8601 com fuso horário: `YYYY-MM-DDThh:mm:ss-03:00` |

---

### **4.4 Estrutura de Campos**

#### **4.4.1 Campos Obrigatórios - Conservação**

| Campo                       | Tipo         | Descrição | Exemplo |
|:----------------------------|:-------------|:----------|:--------|
| **`id`**                    | **integer**  | **(Obrigatório)** Identificador único do serviço. Chave de ligação com a planilha Excel. | `1` |
| **`lote`**                  | string       | Identificador do lote (L1, L6, L7, etc.). | `"L13"` |
| **`rodovia`**               | string       | Código da rodovia estadual (padrão DER/SP sem hífen). | `"SP0000280"` |
| **`item`**                  | string       | Código do item de serviço (ex: a.1.2). | `"a.1.2"` |
| **`detalhamento_servico`**  | string       | Descrição clara do serviço. | `"Recuperação de pavimento"` |
| **`unidade`**               | string       | Unidade de medida (km, m, m2, etc.). | `"km"` |
| **`quantidade`**            | number       | Quantidade planejada (até 3 casas decimais). | `15.500` |
| **`km_inicial`**            | number       | Quilometragem inicial (até 3 casas decimais). | `125.000` |
| **`km_final`**              | number       | Quilometragem final (até 3 casas decimais). | `140.500` |
| **`local`**                 | array        | **(Agregado)** Códigos de localização (mínimo 1 item). | `["PISTA_NORTE", "PISTA_SUL"]` |
| **`data_inicial`**          | string       | Data/hora de início (ISO 8601). | `"2025-11-15T08:00:00-03:00"` |
| **`data_final`**            | string       | Data/hora de término (ISO 8601). | `"2026-03-20T18:00:00-03:00"` |
| **`observacoes_gerais`**    | string/null  | Observações gerais (usar `null` se não houver). | `null` |

---

#### **4.4.2 Campos Obrigatórios - Obras**

| Campo                       | Tipo         | Descrição | Exemplo |
|:----------------------------|:-------------|:----------|:--------|
| **`id`**                    | **integer**  | **(Obrigatório)** Identificador único da obra. Chave de ligação com a planilha Excel. | `1` |
| **`lote`**                  | string       | Identificador do lote. | `"L22"` |
| **`rodovia`**               | string       | Código da rodovia estadual (padrão DER/SP sem hífen). | `"SP0000280"` |
| **`programa`**              | string       | Tipo de programa (REVIT, CAPEX, NS). | `"CAPEX"` |
| **`item`**                  | integer      | Número do item de serviço. | `5` |
| **`subitem`**               | integer      | Número do subitem de serviço. | `2` |
| **`detalhamento_servico`**  | string       | Descrição da obra/intervenção. | `"Construção de passarela"` |
| **`unidade`**               | string       | Unidade de medida. | `"un"` |
| **`quantidade`**            | number       | Quantidade planejada (até 3 casas decimais). | `1.000` |
| **`km_inicial`**            | number       | Quilometragem inicial. | `225.000` |
| **`km_final`**              | number       | Quilometragem final. | `225.100` |
| **`local`**                 | array        | **(Agregado)** Códigos de localização. | `["PISTA_SUL", "DISPOSITIVO"]` |
| **`data_inicial`**          | string       | Data/hora de início (ISO 8601). | `"2025-06-01T08:00:00-03:00"` |
| **`data_final`**            | string       | Data/hora de término (ISO 8601). | `"2027-01-15T18:00:00-03:00"` |
| **`observacoes_gerais`**    | string/null  | Observações gerais (usar `null` se não houver). | `"Obra em parceria com prefeitura"` |

---

#### **4.4.3 Valores Permitidos**

**Formatos de código de rodovia (v1.1):**

O campo `rodovia` aceita 4 formatos (padrão DER/SP sem hífen):

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
  "ano_programacao": 2026,
  "data_geracao": "2025-11-05T15:00:00-03:00"
}
```

---