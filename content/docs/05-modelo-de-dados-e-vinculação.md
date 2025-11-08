---
title: "5. Modelo de Dados e Vinculação"
weight: 50
bookToc: true
---

## **5. Modelo de Dados e Vinculação (Planilha ↔ GeoJSON)**

Esta seção é crucial para entender a relação entre a planilha de planejamento (Excel) e o arquivo GeoJSON final.

---

### **5.1 A Fonte de Dados (Planilha Excel)**

Os templates Excel (`template_lxx_conservacao_2026_r0.xlsx` e `template_lxx_obras_2026_r0.xlsx`), disponibilizados no Portal de Dados Abertos, são a fonte de dados de atributos para a **Fase 1** da entrega. As planilhas preenchidas **não devem conter dados geográficos**.

**Características da planilha:**

- Cada linha representa um serviço ou obra
- O campo `local` é uma **lista de valores separados por ponto e vírgula** (`;`) quando há múltiplos locais
- A planilha **não possui** coluna `id` - este será gerado automaticamente durante a conversão

**Exemplo de Planilha (Excel):**

| lote | rodovia    | km_inicial | km_final | local                      | ... |
|:-----|:-----------|:-----------|:---------|:---------------------------|:----|
| L13  | SP0000280  | 125.0      | 129.5    | PISTA_NORTE;PISTA_SUL      | ... |
| L13  | SPA000114  | 0.0        | 12.35    | CANTEIRO_CENTRAL           | ... |

---

### **5.2 O Destino (Arquivo GeoJSON)**

O arquivo GeoJSON, produto final da **Fase 2**, deve ter uma granularidade de **1 feature por serviço/obra**.

**Regras de conversão:**

- Cada linha do Excel se transforma em uma **feature** no GeoJSON
- O campo `local` da planilha (que contém valores separados por `;`) deve ser convertido em um **array de strings**
- Um campo `id` único deve ser **criado automaticamente** durante a conversão (formato livre, máximo 50 caracteres)
- O `id` gerado deve ser único em todo o arquivo GeoJSON

**Exemplo de GeoJSON (Resultado):**

```json
{
  "type": "Feature",
  "geometry": { ... },
  "properties": {
    "id": "conserva-001",
    "lote": "L13",
    "rodovia": "SP0000280",
    "km_inicial": 125.0,
    "km_final": 129.5,
    "local": ["PISTA_NORTE", "PISTA_SUL"],
    ...
  }
},
{
  "type": "Feature",
  "geometry": { ... },
  "properties": {
    "id": "conserva-002",
    "lote": "L13",
    "rodovia": "SPA000114",
    "km_inicial": 0.0,
    "km_final": 12.35,
    "local": ["CANTEIRO_CENTRAL"],
    ...
  }
}
```

> **💡 Conclusão:** Cada linha do Excel se torna uma feature no GeoJSON, com o campo `local` sendo convertido de string delimitada para array.

---