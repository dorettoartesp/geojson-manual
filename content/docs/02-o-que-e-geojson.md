---
title: "2. O que é GeoJSON"
weight: 20
bookToc: true
---

# **2. O que é GeoJSON**

## **2.1 Conceitos Fundamentais**

**GeoJSON** é um formato aberto de codificação para representar estruturas de dados geográficos, baseado em JSON (JavaScript Object Notation).

---

## **2.2 Estrutura Básica**

Um arquivo GeoJSON válido contém obrigatoriamente um objeto `FeatureCollection`:

**Exemplo de estrutura GeoJSON básica:**

```json
{
  "type": "FeatureCollection",
  "crs": {
    "type": "name",
    "properties": {
      "name": "EPSG:4674"
    }
  },
  "metadata": {
    "schema_version": "R0",
    "ano_programacao": 2026,
    "data_geracao": "2025-10-30T14:30:00-03:00"
  },
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [-47.0653, -23.5489]
      },
      "properties": {
        "id": 1,
        "detalhamento_servico": "Exemplo de feature"
      }
    }
  ]
}
```

**Componentes principais:**

| Componente  | Descrição |
|:------------|:----------|
| `type`      | Sempre `"FeatureCollection"` para a coleção de features. |
| `crs`       | Sistema de Referência de Coordenadas (obrigatório para ARTESP). |
| `metadata`  | Metadados de controle (específico da ARTESP). |
| `features`  | Array contendo as features (geometria + propriedades). |

---

## **2.3 Tipos de Geometria**

O GeoJSON suporta os seguintes tipos geométricos:

### **2.3.1 Point (Ponto)**

Representa uma localização única no espaço.

**Exemplo:** `[-47.0653, -23.5489]`

### **2.3.2 LineString (Linha)**

Representa uma sequência conectada de pontos.

**Exemplo:** `[ [-47.0653, -23.5489], [-47.0663, -23.5499] ]`

### **2.3.3 Polygon (Polígono)**

Representa uma área fechada.

**Exemplo:** `[ [ [-47.06, -23.54], [-47.07, -23.54], [-47.07, -23.55], [-47.06, -23.55], [-47.06, -23.54] ] ]`
