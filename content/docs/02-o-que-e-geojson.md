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

**Exemplo de coordenadas:** `[ [-47.0653, -23.5489], [-47.0663, -23.5499] ]`

**Estrutura completa:**

```json
{
  "type": "Feature",
  "geometry": {
    "type": "LineString",
    "coordinates": [
      [-47.0653, -23.5489],
      [-47.0663, -23.5499],
      [-47.0673, -23.5509]
    ]
  },
  "properties": {
    "id": "pavimento-001",
    "detalhamento_servico": "Recuperação funcional do pavimento"
  }
}
```

---

#### **Cálculo de Distância entre Pontos Consecutivos**

Para garantir a qualidade e precisão de geometrias LineString, é importante controlar a distância máxima entre pontos consecutivos.

**Por que isso importa:**
- Distâncias muito grandes entre pontos podem resultar em linhas "retas" que não seguem o traçado real da rodovia
- Distâncias muito pequenas geram vértices excessivos e comprometem a performance
- O espaçamento adequado garante representação fiel do trajeto

**Fórmula de Haversine** (para coordenadas geográficas em graus):

```
a = sin²(Δlat/2) + cos(lat₁) × cos(lat₂) × sin²(Δlon/2)
c = 2 × atan2(√a, √(1−a))
d = R × c
```

Onde:
- **R** = 6371 km (raio médio da Terra)
- **Δlat** = lat₂ - lat₁ (em radianos)
- **Δlon** = lon₂ - lon₁ (em radianos)

**Implementação em Python:**

```python
import math

def distancia_haversine(lon1, lat1, lon2, lat2):
    """
    Calcula a distância entre dois pontos usando a fórmula de Haversine.

    Parâmetros:
        lon1, lat1: Coordenadas do primeiro ponto (graus decimais)
        lon2, lat2: Coordenadas do segundo ponto (graus decimais)

    Retorna:
        Distância em quilômetros
    """
    R = 6371  # Raio da Terra em km

    # Converter de graus para radianos
    lat1_rad = math.radians(lat1)
    lat2_rad = math.radians(lat2)
    delta_lat = math.radians(lat2 - lat1)
    delta_lon = math.radians(lon2 - lon1)

    # Fórmula de Haversine
    a = math.sin(delta_lat/2)**2 + \
        math.cos(lat1_rad) * math.cos(lat2_rad) * \
        math.sin(delta_lon/2)**2
    c = 2 * math.asin(math.sqrt(a))

    return R * c

# Exemplo de uso
ponto1 = (-47.0653, -23.5489)
ponto2 = (-47.0663, -23.5499)

dist_km = distancia_haversine(ponto1[0], ponto1[1], ponto2[0], ponto2[1])
print(f"Distância: {dist_km:.3f} km")
# Saída: Distância: 0.136 km (136 metros)
```

**Validação da Distância Máxima:**

```python
def validar_distancias_linestring(coordinates, max_distancia_km=1.0):
    """
    Valida se todos os segmentos de uma LineString respeitam a distância máxima.

    Parâmetros:
        coordinates: Lista de coordenadas [[lon, lat], ...]
        max_distancia_km: Distância máxima permitida entre pontos consecutivos (km)

    Retorna:
        (bool, list): (é_válido, lista_de_erros)
    """
    erros = []

    for i in range(len(coordinates) - 1):
        lon1, lat1 = coordinates[i]
        lon2, lat2 = coordinates[i + 1]

        dist = distancia_haversine(lon1, lat1, lon2, lat2)

        if dist > max_distancia_km:
            erros.append({
                'segmento': f"{i} → {i+1}",
                'ponto1': [lon1, lat1],
                'ponto2': [lon2, lat2],
                'distancia_km': round(dist, 3)
            })

    return len(erros) == 0, erros

# Exemplo de validação
linestring = [
    [-47.0653, -23.5489],
    [-47.0663, -23.5499],
    [-47.0800, -23.5600]  # Este segmento pode exceder o limite
]

valido, erros = validar_distancias_linestring(linestring, max_distancia_km=1.0)

if not valido:
    print("⚠️ Segmentos com distância excessiva encontrados:")
    for erro in erros:
        print(f"  Segmento {erro['segmento']}: {erro['distancia_km']} km")
```

**📏 Recomendações de Distância:**

| Tipo de Geometria | Distância Máxima Recomendada | Observações |
|:------------------|:----------------------------|:------------|
| Trechos rodoviários retos | 1 km | Adequado para rodovias com traçado simples |
| Trechos com curvas suaves | 500 m | Melhor precisão em curvas |
| Trechos com curvas acentuadas | 100-250 m | Necessário para representar curvas complexas |
| Áreas urbanas/dispositivos | 50-100 m | Maior detalhamento |

**⚠️ Alertas:**
- Evite vértices excessivos (> 1000 pontos por LineString) para não comprometer performance
- Para trechos muito longos (> 50 km), considere dividir em múltiplas features
- Use ferramentas GIS para simplificar geometrias quando apropriado

### **2.3.3 Polygon (Polígono)**

Representa uma área fechada.

**Exemplo:** `[ [ [-47.06, -23.54], [-47.07, -23.54], [-47.07, -23.55], [-47.06, -23.55], [-47.06, -23.54] ] ]`

### **2.3.4 MultiPoint (Múltiplos Pontos)**

Representa um conjunto de pontos discretos não conectados entre si.

**Uso típico:** Localização de múltiplos itens similares distribuídos ao longo de um trecho (sensores, tachões, placas de sinalização).

**Exemplo de coordenadas:**

```json
[
  [-47.0653, -23.5489],
  [-47.0663, -23.5499],
  [-47.0673, -23.5509]
]
```

**Estrutura completa:**

```json
{
  "type": "Feature",
  "geometry": {
    "type": "MultiPoint",
    "coordinates": [
      [-47.0653, -23.5489],
      [-47.0663, -23.5499],
      [-47.0673, -23.5509]
    ]
  },
  "properties": {
    "id": "sensor-001",
    "detalhamento_servico": "Instalação de sensores de tráfego"
  }
}
```

---

### **2.3.5 Diferença entre MultiPoint e LineString**

Embora ambos os tipos usem arrays de coordenadas, possuem diferenças fundamentais:

| Aspecto | MultiPoint | LineString |
|:--------|:-----------|:-----------|
| **Conectividade** | Pontos **independentes** (não conectados) | Pontos **conectados** sequencialmente |
| **Ordem dos pontos** | Ordem não implica continuidade espacial | Ordem define o trajeto da linha |
| **Interpretação visual** | Conjunto de marcadores discretos | Linha contínua |
| **Uso típico** | Itens múltiplos e discretos | Trechos lineares contínuos |
| **Distância entre pontos** | Irrelevante | Deve ser calculada (veja abaixo) |

**📌 Exemplo Prático:**

**MultiPoint** - Instalação de 5 radares ao longo da SP-280:
- Radar no km 100
- Radar no km 115
- Radar no km 130
- Radar no km 145
- Radar no km 160

→ Não há relação de continuidade entre os radares. São itens independentes.

**LineString** - Recuperação de pavimento do km 100 ao km 110 da SP-280:
- Ponto inicial: km 100
- Pontos intermediários: km 102, km 104, km 106, km 108
- Ponto final: km 110

→ Há continuidade espacial. O serviço cobre toda a extensão entre os pontos.

**⚠️ Regra de Decisão:**

- Use **MultiPoint** quando os itens são **discretos e independentes**
- Use **LineString** quando há **continuidade espacial** entre os pontos
