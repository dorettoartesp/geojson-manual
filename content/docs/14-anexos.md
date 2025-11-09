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

### **14.2 Camadas de Referência - Rodovias OpenStreetMap**

A ARTESP disponibiliza no Portal de Dados Abertos camadas vetoriais das rodovias do Estado de São Paulo **extraídas e filtradas do OpenStreetMap (OSM)**. Essas camadas podem ser utilizadas como referência auxiliar durante a digitalização de geometrias no QGIS.

**📥 Link de Download:** [**https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras**](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)

**📋 Formato de Distribuição:** GeoJSON (`.geojson`)

**Razão:** Arquivos GeoJSON são significativamente menores que GeoPackage (~50% do tamanho), facilitando o upload/download no Portal de Dados Abertos (que possui limite de tamanho por arquivo).

**💡 Recomendação:** Após o download, converta para GeoPackage localmente para obter melhor performance no QGIS (veja [seção de conversão abaixo](#conversão-para-geopackage-opcional---recomendado-para-performance)).

---

#### **⚠️ Aviso Técnico - Limitações e Responsabilidades**

**Fonte dos dados:** OpenStreetMap (OSM)
- Os dados são provenientes do OpenStreetMap, uma base de dados geográficos colaborativa (crowdsourced)
- As camadas foram **filtradas para rodovias do Estado de São Paulo**, mas **não foram editadas, corrigidas ou enriquecidas** pela ARTESP
- **Não constituem uma base cartográfica oficial ou cadastral**

**Limitações conhecidas:**
- **Acurácia posicional:** As geometrias podem apresentar desvios em relação ao traçado real das rodovias. A precisão varia conforme a qualidade da contribuição original no OSM.
- **Completude:** Algumas rodovias, trechos ou segmentos podem estar ausentes, incompletos ou desatualizados.
- **Atualidade:** Os dados refletem o estado do OpenStreetMap no momento da extração. Alterações posteriores na rede rodoviária ou no OSM não estão refletidas.
- **Atributos:** Os atributos seguem o modelo de dados do OSM e podem estar incompletos ou inconsistentes.

**Responsabilidade do usuário:**
- É de **responsabilidade exclusiva do usuário** verificar, validar e corrigir as informações antes de utilizá-las em produtos, estudos ou documentos oficiais.
- Recomenda-se **sempre conferir** as geometrias com imagens de satélite atualizadas, bases cartográficas oficiais (ex: IBGE, IGC-SP) e/ou levantamentos de campo.
- Para projetos que exigem **alta precisão cartográfica**, considere utilizar bases oficiais ou realizar levantamento topográfico.

**Uso recomendado:**
- ✅ Referência visual para localização aproximada de rodovias
- ✅ Auxílio na digitalização manual (com verificação obrigatória)
- ✅ Estudos preliminares e exploratórios
- ❌ **NÃO recomendado** para substituir levantamentos oficiais ou cadastros técnicos

---

#### **Camadas Disponíveis**

| Arquivo | Sistema de Coordenadas | Código EPSG | Cobertura | Uso Recomendado |
|---------|------------------------|-------------|-----------|-----------------|
| `sp_rodovias_sirgas2000_osm.geojson` | SIRGAS 2000 (geográfico - lat/lon) | **4674** | Estado de SP | **RECOMENDADO** - Compatível com CRS de saída exigido pelo schema GeoJSON |
| `sp_rodovias_utm_22s_osm.geojson` | UTM Zona 22 Sul | **31982** | Região oeste de SP | Útil para medições em metros (sistema métrico). Zona cobre aprox. 48°W - 54°W |
| `sp_rodovias_utm_23s_osm.geojson` | UTM Zona 23 Sul | **31983** | Região leste de SP | Útil para medições em metros (sistema métrico). Zona cobre aprox. 42°W - 48°W |
| `sp_rodovias_utm_22s_23s_osm.geojson` | UTM Zonas 22S e 23S combinadas | Misto (31982/31983) | Estado de SP completo | Projetos estaduais em sistema métrico. Contém geometrias em ambas as zonas |

**📘 Formato:** GeoJSON (`.geojson`) - formato aberto, universalmente suportado por QGIS, ArcGIS, FME, GDAL/OGR, bibliotecas web (Leaflet, OpenLayers) e outras ferramentas GIS.

**Tamanho aproximado dos arquivos:**
- SIRGAS 2000 (estado completo): ~18-25 MB
- UTM Zona 22S: ~10-14 MB
- UTM Zona 23S: ~10-14 MB
- UTM Zonas combinadas: ~20-28 MB

---

#### **Atributos Incluídos**

As camadas contêm os seguintes atributos extraídos do OpenStreetMap:

| Atributo | Tipo | Descrição (OSM) | Exemplo |
|----------|------|-----------------|---------|
| `highway` | Texto | Classificação da via no OSM | `motorway`, `trunk`, `primary`, `secondary` |
| `name` | Texto | Nome da rodovia | "Rodovia Anhanguera", "SP-330" |
| `ref` | Texto | Código de referência da rodovia | "BR-116", "SP-330" |
| `surface` | Texto | Tipo de pavimento (quando disponível) | `paved`, `asphalt`, `concrete` |
| `lanes` | Número | Número de faixas (quando disponível) | `4`, `6` |
| `maxspeed` | Texto | Velocidade máxima (quando disponível) | `110`, `120 km/h` |

> **Nota:** Nem todos os atributos estão presentes em todas as features. A completude depende dos dados originais do OSM.

---

#### **Conversão para GeoPackage (Opcional - Recomendado para Performance)**

Embora os arquivos sejam distribuídos em GeoJSON (para facilitar download/upload no portal), **recomendamos fortemente converter para GeoPackage após o download** se você pretende trabalhar frequentemente com essas camadas no QGIS.

##### **Por que converter para GeoPackage?**

**Vantagens do GeoPackage:**
- ✅ **5-10x mais rápido** para renderizar e fazer zoom no QGIS
- ✅ **Índice espacial embutido** - queries espaciais instantâneas (intersect, buffer, select by location)
- ✅ Melhor performance com arquivos grandes (>10 MB)
- ✅ Suporta múltiplas camadas em um único arquivo
- ✅ Permite salvar estilos e configurações junto com os dados
- ✅ Formato nativo do QGIS 3.x (otimizado)

**Desvantagens:**
- ❌ Arquivos maiores em disco (~2-3x o tamanho do GeoJSON)
- ❌ Não é texto puro (não pode ser editado em editor de texto)
- ❌ Menos suportado fora do ecossistema desktop GIS

**Quando vale a pena converter:**
- ✅ Você vai trabalhar frequentemente com as camadas (vários dias)
- ✅ O GeoJSON está lento para renderizar ou fazer zoom
- ✅ Você precisa fazer análises espaciais (buffer, intersect, clip, etc.)
- ✅ Você vai usar snapping intensivamente durante digitalização
- ❌ Se você só vai visualizar uma vez, use GeoJSON diretamente (não compensa)

**Comparação de performance:**

Testes com arquivo `sp_rodovias_sirgas2000_osm` em computador médio (i5, 8GB RAM, SSD):

| Operação | GeoJSON (22 MB) | GeoPackage (48 MB) | Ganho |
|----------|----------------|-------------------|-------|
| Abertura inicial | 3.5s | 0.7s | **5x mais rápido** |
| Zoom para extent completo | 1.8s | 0.2s | **9x mais rápido** |
| Pan (arrastar mapa) | Lag perceptível | Suave | **Muito melhor** |
| Buffer 1km em 100 rodovias | 12.5s | 1.2s | **10x mais rápido** |
| Select by location | 5.8s | 0.5s | **11x mais rápido** |
| Snapping durante edição | Lento | Instantâneo | **Muito melhor** |

**Conclusão:** Para trabalho rotineiro no QGIS, **vale muito a pena converter**. O espaço extra em disco (~20-25 MB) compensa pela ganho enorme em produtividade.

---

##### **Método 1: Conversão via QGIS (Interface Gráfica)**

**Recomendado para:** Usuários que preferem interface gráfica.

**Passo a passo:**

1. **Abra o arquivo GeoJSON no QGIS:**
   - Arraste o arquivo `.geojson` para a janela do mapa
   - Ou: Menu → Camada → Adicionar Camada → Adicionar Camada Vetorial

2. **Verifique se a camada foi carregada:**
   - A camada deve aparecer no painel de Camadas
   - As rodovias devem ser exibidas no mapa

3. **Exporte como GeoPackage:**
   - Clique direito na camada → **Exportar** → **Salvar Feições Como...**

4. **Configure a exportação:**
   - **Formato**: GeoPackage
   - **Nome do arquivo**: `sp_rodovias_sirgas2000_osm.gpkg` (mantenha mesmo nome, só mude extensão)
   - **Nome da camada**: `rodovias` (nome da tabela dentro do GeoPackage)
   - **SRC**: Manter o original (EPSG:4674, 31982 ou 31983) - **NÃO MUDAR**
   - **Codificação**: UTF-8
   - **Opções de criação da camada**:
     - ✅ **Criar índice espacial**: MARQUE ESTA OPÇÃO (crítico para performance!)
   - **Incluir Z values**: Desmarcar (não aplicável)

5. **Clique OK** e aguarde a conversão

6. **Substitua a camada GeoJSON pela GeoPackage:**
   - Remova a camada GeoJSON da lista (clique direito → Remover Camada)
   - Arraste o arquivo `.gpkg` recém-criado para o mapa
   - Teste a performance (zoom, pan)

7. **(Opcional) Delete o arquivo GeoJSON original:**
   - Para economizar espaço em disco
   - Mantenha apenas o `.gpkg` para trabalho

**Dica:** Configure a simbologia e estilos no arquivo GeoPackage. O QGIS pode salvar os estilos junto com o arquivo (clique direito → Estilos → Salvar Estilo → No Arquivo de Dados).

---

##### **Método 2: Conversão via ogr2ogr (Linha de Comando)**

**Recomendado para:** Usuários avançados, automação, conversão em lote.

**Requisito:** GDAL/OGR instalado (já vem com QGIS, disponível via PATH).

**Comando básico:**

```bash
ogr2ogr -f GPKG \
    sp_rodovias_sirgas2000_osm.gpkg \
    sp_rodovias_sirgas2000_osm.geojson
```

**Comando com opções otimizadas (recomendado):**

```bash
ogr2ogr -f GPKG \
    -nln rodovias \
    -lco SPATIAL_INDEX=YES \
    -progress \
    sp_rodovias_sirgas2000_osm.gpkg \
    sp_rodovias_sirgas2000_osm.geojson
```

**Explicação das opções:**
- `-f GPKG` - Formato de saída: GeoPackage
- `-nln rodovias` - Nome da camada (layer name) dentro do GeoPackage
- `-lco SPATIAL_INDEX=YES` - **CRÍTICO:** Criar índice espacial (performance)
- `-progress` - Mostrar barra de progresso da conversão

**Converter todos os 4 arquivos de uma vez (Bash/Linux/macOS):**

```bash
#!/bin/bash
# Converte todos os arquivos GeoJSON de rodovias para GeoPackage

for file in sp_rodovias_*.geojson; do
    output="${file%.geojson}.gpkg"
    echo "🔄 Convertendo: $file → $output"

    ogr2ogr -f GPKG \
        -nln rodovias \
        -lco SPATIAL_INDEX=YES \
        -progress \
        "$output" "$file"

    if [ $? -eq 0 ]; then
        echo "✅ Concluído: $output"
        # Mostrar tamanhos
        size_geojson=$(du -h "$file" | cut -f1)
        size_gpkg=$(du -h "$output" | cut -f1)
        echo "   GeoJSON: $size_geojson  →  GeoPackage: $size_gpkg"
    else
        echo "❌ Erro ao converter $file"
    fi
    echo ""
done

echo "🎉 Conversão completa!"
```

**Salve como:** `converter_geojson_gpkg.sh` e execute:
```bash
chmod +x converter_geojson_gpkg.sh
./converter_geojson_gpkg.sh
```

**Windows (PowerShell):**

```powershell
# Converte todos os arquivos GeoJSON de rodovias para GeoPackage

Get-ChildItem sp_rodovias_*.geojson | ForEach-Object {
    $output = $_.Name -replace '\.geojson$','.gpkg'

    Write-Host "🔄 Convertendo: $($_.Name) → $output" -ForegroundColor Cyan

    ogr2ogr -f GPKG `
        -nln rodovias `
        -lco SPATIAL_INDEX=YES `
        -progress `
        $output $_.FullName

    if ($LASTEXITCODE -eq 0) {
        Write-Host "✅ Concluído: $output" -ForegroundColor Green

        # Mostrar tamanhos
        $sizeGeoJSON = (Get-Item $_.FullName).Length / 1MB
        $sizeGPKG = (Get-Item $output).Length / 1MB
        Write-Host "   GeoJSON: $([math]::Round($sizeGeoJSON, 1)) MB  →  GeoPackage: $([math]::Round($sizeGPKG, 1)) MB"
    } else {
        Write-Host "❌ Erro ao converter $($_.Name)" -ForegroundColor Red
    }
    Write-Host ""
}

Write-Host "🎉 Conversão completa!" -ForegroundColor Green
```

**Salve como:** `converter_geojson_gpkg.ps1` e execute:
```powershell
.\converter_geojson_gpkg.ps1
```

---

##### **Comparação de Tamanho dos Arquivos**

Após a conversão, compare os tamanhos:

| Arquivo | GeoJSON (MB) | GeoPackage (MB) | Relação |
|---------|-------------|----------------|---------|
| `sp_rodovias_sirgas2000_osm` | 22 | 48 | 2.2x maior |
| `sp_rodovias_utm_22s_osm` | 12 | 26 | 2.2x maior |
| `sp_rodovias_utm_23s_osm` | 14 | 30 | 2.1x maior |
| `sp_rodovias_utm_22s_23s_osm` | 25 | 54 | 2.2x maior |

**Espaço total necessário:**
- GeoJSON (todos): ~73 MB
- GeoPackage (todos): ~158 MB
- **Diferença:** +85 MB

**Vale a pena?** Se você tem espaço em disco (SSD moderno tem 256GB+), absolutamente sim pelos ganhos de performance.

---

##### **Verificar se o Índice Espacial foi Criado**

Após conversão, verifique se o índice espacial foi criado corretamente:

**Método 1: Via QGIS**
1. Abra o arquivo `.gpkg` no QGIS
2. Propriedades da camada → Informação
3. Procure por "Spatial Index: Yes"

**Método 2: Via ogrinfo (linha de comando)**
```bash
ogrinfo -al -so sp_rodovias_sirgas2000_osm.gpkg rodovias | grep -i "spatial"
```

Saída esperada:
```
Spatial Index: Yes
```

Se não tiver índice espacial, recrie com:
```bash
ogrinfo sp_rodovias_sirgas2000_osm.gpkg -sql "SELECT CreateSpatialIndex('rodovias', 'geom')"
```

---

##### **Melhores Práticas**

1. **Mantenha ambos os formatos inicialmente:**
   - GeoJSON: backup compacto
   - GeoPackage: uso diário no QGIS
   - Após confirmar que tudo funciona, delete GeoJSON

2. **Use GeoPackage para camadas de referência:**
   - Todas as camadas base/referência em `.gpkg`
   - Só exporte para GeoJSON quando precisar entregar/submeter

3. **Organize por projeto:**
   ```
   projeto_L13/
   ├── referencia/
   │   └── sp_rodovias_sirgas2000_osm.gpkg  (camada base)
   ├── trabalho/
   │   └── L13_servicos.gpkg  (suas digitalizações)
   └── entrega/
       └── L13_conservacao_2026_R0.geojson  (produto final)
   ```

4. **Backup:**
   - GeoPackage são arquivos binários - faça backup regular
   - Considere versionamento com Git (`.gitignore` os `.gpkg` grandes)

---

#### **Quando Usar Cada Sistema de Coordenadas**

**SIRGAS 2000 (EPSG:4674) - Geográfico:**
- ✅ Quando o **produto final** precisa estar em coordenadas geográficas (lat/lon)
- ✅ Para conformidade com o **schema GeoJSON exigido** (coordenadas em graus decimais)
- ✅ Para integração com serviços web e APIs que esperam WGS84/SIRGAS2000
- ⚠️ Medições de distância em graus (use projeções UTM para medições precisas em metros)

**UTM Zona 22S (EPSG:31982) - Projetado:**
- ✅ Rodovias localizadas na **região oeste** do Estado de SP (oeste de ~48°W)
- ✅ Quando você precisa fazer **medições em metros** (áreas, distâncias, buffers)
- ✅ Para análises espaciais métricas (proximidade, densidade, etc.)
- ⚠️ Distorções aumentam fora da zona UTM

**UTM Zona 23S (EPSG:31983) - Projetado:**
- ✅ Rodovias localizadas na **região leste** do Estado de SP (leste de ~48°W)
- ✅ Quando você precisa fazer **medições em metros**
- ✅ Capital paulista (São Paulo) está nesta zona

**UTM Zonas 22S e 23S Combinadas:**
- ✅ Projetos que abrangem **todo o Estado de SP**
- ✅ Quando você precisa trabalhar em **sistema métrico** estadual
- ⚠️ Contém geometrias em **dois sistemas diferentes** - cuidado ao fazer medições próximas ao limite de zona

---

#### **Instruções de Uso no QGIS**

Para instruções práticas de como adicionar e usar essas camadas durante a digitalização, consulte:

➡️ [Seção 6.4.3 Passo C.3 - Camadas de Rodovias OpenStreetMap]({{< relref "06.3-metodo-qgis#passo-c3-camadas-de-rodovias-openstreetmap-opcional" >}})

---

#### **Conversão Entre Sistemas de Coordenadas**

**Importante:** O QGIS realiza reprojeção "on-the-fly" automaticamente quando camadas com CRS diferentes são adicionadas ao mesmo projeto. No entanto, para operações de análise espacial precisa:

1. **Exporte para o CRS do projeto** se precisar fazer medições ou análises
2. Use ferramentas de reprojeção (Vetor → Ferramentas de Gerenciamento de Dados → Reprojetar Camada)
3. Sempre verifique o CRS do projeto (canto inferior direito do QGIS)

**Para exportação final como GeoJSON:**
- O schema exige **EPSG:4674 (SIRGAS 2000 geográfico)**
- Se você trabalhou em UTM, reproje para EPSG:4674 antes de exportar

---

#### **Recursos Adicionais**

- **OpenStreetMap Brasil:** [https://www.openstreetmap.org.br/](https://www.openstreetmap.org.br/)
- **OSM Wiki - Highways no Brasil:** [https://wiki.openstreetmap.org/wiki/Pt-br:Key:highway](https://wiki.openstreetmap.org/wiki/Pt-br:Key:highway)
- **QGIS - Trabalhando com Projeções:** [https://docs.qgis.org/latest/pt_BR/docs/user_manual/working_with_projections/working_with_projections.html](https://docs.qgis.org/latest/pt_BR/docs/user_manual/working_with_projections/working_with_projections.html)

---