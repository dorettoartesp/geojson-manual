---
title: "7. Conversão de Shapefiles"
weight: 70
bookToc: true
---

## **7. Conversão de Shapefiles e Outros Formatos**

### **7.1 Reprojeção para EPSG:4674**

Se seus dados estão em outro sistema de coordenadas (ex: WGS84), é necessário reprojetá-los.

---

### **7.2 Usando QGIS**

**Passo a passo para exportar em QGIS:**

1. Clique com botão direito na camada
2. **Export** → **Save Features As...**
3. Configure:
   - **Format**: GeoJSON
   - **File name**: escolha local e nome
   - **CRS**: EPSG:4674 - SIRGAS 2000
   - **Layer Options → RFC7946**: NO (deixe desmarcado)
4. Clique em **OK**

---

### **7.3 Usando GDAL**

Método direto via linha de comando:

**Comando para reprojetar shapefile para GeoJSON:**

```bash
# Reprojetar shapefile para GeoJSON em EPSG:4674
ogr2ogr -f GeoJSON \
  -t_srs EPSG:4674 \
  -lco RFC7946=NO \
  L13_conservacao_2026_R0.geojson \
  conservacao_origem.shp
```

> **Nota:** Em sistemas Windows, pode ser necessário remover as barras invertidas (`\`) de continuação de linha e executar o comando em uma única linha.

---