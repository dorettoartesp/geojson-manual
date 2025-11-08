---
title: "8. Exemplos Práticos Completos"
weight: 80
bookToc: true
---

## **8. Exemplos Práticos Completos**

Os exemplos a seguir demonstram a estrutura completa de arquivos GeoJSON válidos.

---

### **8.1 Exemplo: Conservação**

**Arquivo GeoJSON completo de conservação:**

```json
{
  "type": "FeatureCollection",
  "crs": {
    "type": "name",
    "properties": { "name": "urn:ogc:def:crs:EPSG::4674" }
  },
  "metadata": {
    "schema_version": "R0",
    "data_geracao": "2025-11-05T10:10:00-03:00"
  },
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [ [-47.0653, -23.5489], [-47.0683, -23.5519] ]
      },
      "properties": {
        "id": "conserva-001",
        "lote": "L13",
        "rodovia": "SP0000280",
        "item": "a.1.2",
        "detalhamento_servico": "Recuperação de pavimento asfáltico com recapeamento",
        "unidade": "km",
        "quantidade": 4.5,
        "km_inicial": 125.0,
        "km_final": 129.5,
        "local": [
          "PISTA_NORTE",
          "PISTA_SUL"
        ],
        "data_inicial": "2026-02-01",
        "data_final": "2026-04-30",
        "observacoes_gerais": "Serviço programado para período de baixo fluxo de tráfego"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [ [-47.08, -23.56], [-47.09, -23.57] ]
      },
      "properties": {
        "id": "conserva-002",
        "lote": "L13",
        "rodovia": "SPA000114",
        "item": "b.3.1",
        "detalhamento_servico": "Roçada manual e mecanizada de canteiro central",
        "unidade": "km",
        "quantidade": 12.35,
        "km_inicial": 0.0,
        "km_final": 12.35,
        "local": [
          "CANTEIRO_CENTRAL"
        ],
        "data_inicial": "2026-01-15",
        "data_final": "2026-12-20",
        "observacoes_gerais": null
      }
    }
  ]
}
```

---

### **8.2 Exemplo: Obras**

**Arquivo GeoJSON completo de obras:**

```json
{
  "type": "FeatureCollection",
  "crs": {
    "type": "name",
    "properties": { "name": "urn:ogc:def:crs:EPSG::4674" }
  },
  "metadata": {
    "schema_version": "R0",
    "data_geracao": "2025-11-05T10:15:00-03:00"
  },
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [ -47.0653, -23.5489 ]
      },
      "properties": {
        "id": "obra-101",
        "lote": "L22",
        "rodovia": "SP0000310",
        "programa": "CAPEX",
        "item": 1,
        "subitem": 1,
        "detalhamento_servico": "Construção de passarela para pedestres",
        "unidade": "un",
        "quantidade": 1.0,
        "km_inicial": 45.2,
        "km_final": 45.2,
        "local": [
          "PISTA_NORTE",
          "PISTA_SUL"
        ],
        "data_inicial": "2026-04-01",
        "data_final": "2026-10-31",
        "observacoes_gerais": "Obra de acessibilidade em área urbana"
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [ [-47.2, -23.6], [-47.23, -23.615] ]
      },
      "properties": {
        "id": "obra-102",
        "lote": "L22",
        "rodovia": "SPA000248",
        "programa": "NS",
        "item": 3,
        "subitem": 2,
        "detalhamento_servico": "Implantação de terceira faixa de rolamento",
        "unidade": "km",
        "quantidade": 8.75,
        "km_inicial": 12.0,
        "km_final": 20.75,
        "local": [
          "PISTA_NORTE"
        ],
        "data_inicial": "2026-02-15",
        "data_final": "2026-11-30",
        "observacoes_gerais": null
      }
    }
  ]
}
```

---