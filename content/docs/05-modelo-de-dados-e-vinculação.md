---
title: "5. Modelo de Dados e Vinculação"
weight: 50
bookToc: true
---

## **5. Modelo de Dados e Vinculação (Planilha ↔ GeoJSON)**

Esta seção é crucial para entender a relação entre a planilha de planejamento (Excel) e o arquivo GeoJSON final.

---

### **5.1 A Fonte de Dados (Planilha Excel)**

Os templates Excel (`template_conservacao_2026_R0.xlsx` e `template_obras_2026_R0.xlsx`), disponibilizados no Portal de Dados Abertos, são a fonte de dados de atributos para a **Fase 1** da entrega. As planilhas preenchidas **não devem conter dados geográficos**. Nelas, a granularidade é de **1 linha por local de serviço**.

**Características da planilha:**

- Um serviço/obra que ocorre em múltiplos locais (ex: `PISTA_NORTE` e `PISTA_SUL`) será representado por **múltiplas linhas** na planilha
- Todas essas linhas compartilharão o **mesmo `id`**
- O campo `local` conterá um *único* valor por linha

**Exemplo de Planilha (Excel):**

| id  | lote | rodovia    | km_inicial | km_final | local             | ... |
|:----|:-----|:-----------|:-----------|:---------|:------------------|:----|
| 1   | L13  | SP0000280  | 125.0      | 129.5    | PISTA_NORTE       | ... |
| 1   | L13  | SP0000280  | 125.0      | 129.5    | PISTA_SUL         | ... |
| 2   | L13  | SPA000114  | 0.0        | 12.35    | CANTEIRO_CENTRAL  | ... |

---

### **5.2 O Destino (Arquivo GeoJSON)**

O arquivo GeoJSON, produto final da **Fase 2**, deve ter uma granularidade de **1 feature por serviço/obra**.

**Regras de agrupamento:**

- O script de conversão deve **agrupar** todas as linhas da planilha que possuem o **mesmo `id`**
- Ao agrupar, os valores da coluna `local` (da planilha) devem ser coletados e agregados em um **array de strings** no GeoJSON
- Todos os outros campos (lote, rodovia, km, datas) devem ser idênticos para o mesmo `id`, pois são dados-mestre do serviço

**Exemplo de GeoJSON (Resultado):**

A `Feature` 1, no exemplo, agrupa as duas primeiras linhas da planilha:

```json
{
  "type": "Feature",
  "geometry": { ... },
  "properties": {
    "id": 1,
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
    "id": 2,
    "lote": "L13",
    "rodovia": "SPA000114",
    "km_inicial": 0.0,
    "km_final": 12.35,
    "local": ["CANTEIRO_CENTRAL"],
    ...
  }
}
```

> **💡 Conclusão:** O campo `id` é a chave de agrupamento (GROUP BY) da planilha para o GeoJSON.

---