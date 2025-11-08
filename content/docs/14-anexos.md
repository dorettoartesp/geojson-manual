---
title: "14. Anexos"
weight: 140
bookToc: true
---

## **14. Anexos**

### **14.1 Downloads - Schemas de Validação e Mapeamentos**

Os arquivos necessários para validação e referência estão disponíveis no portal de dados abertos da ARTESP.

**📥 Link de Download:** [**https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras**](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)

**Arquivos disponíveis:**

| Arquivo                    | Nome do Arquivo                                              | Descrição |
|:---------------------------|:-------------------------------------------------------------|:----------|
| **JSON Schema (R0)**       | `conserva.schema.r0.json` / `obras.schema.r0.json`           | Schemas versão R0 para validação estrutural e de dados. |
| **Script de Validação**    | `validar_geojson.py`                                         | Script Python para validação automatizada de arquivos GeoJSON (veja seção 9.1.3). |
| **Tabelas de Códigos**     | `rodovias.xlsx`,`local.xlsx`, `programas.xlsx`, `item.xlsx`  | Arquivos de referência para os códigos permitidos. |

> **Nota sobre versões:** Os schemas incluem a versão no nome do arquivo (ex: `.r0.json`). Sempre use o schema correspondente ao valor de `schema_version` no campo metadata do seu GeoJSON.

---