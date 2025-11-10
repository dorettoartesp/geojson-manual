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
| **Script de Mesclagem**    | `mesclar_geojson.py`                                         | Script Python para combinar múltiplos arquivos GeoJSON em um único (veja seções 6.5.A e 7.8). |
| **Tabelas de Códigos**     | `rodovias.xlsx`,`local.xlsx`, `programas.xlsx`, `item.xlsx`  | Arquivos de referência para os códigos permitidos. |
| **Camadas de Rodovias**    | `sp_rodovias_*.geojson` (4 arquivos)                         | Camadas OSM das rodovias de SP em diferentes CRS (veja seção 14.2). |

> **Nota sobre versões:** Os schemas incluem a versão no nome do arquivo (ex: `.r0.json`). Sempre use o schema correspondente ao valor de `schema_version` no campo metadata do seu GeoJSON.

---

### **14.2 Camadas de Referência Geoespacial**

A ARTESP disponibiliza no Portal de Dados Abertos diversas camadas vetoriais de referência do Estado de São Paulo. Essas camadas podem ser utilizadas como referência auxiliar durante a digitalização de geometrias no QGIS.

**📥 Link de Download:** [**https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras**](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)

**📋 Formatos de Distribuição:** GeoPackage (`.gpkg`) e/ou GeoJSON (`.geojson`)

**💡 Recomendação:** Para melhor performance no QGIS, prefira GeoPackage. Se baixar GeoJSON, considere converter para GeoPackage localmente (veja [seção de conversão abaixo](#conversão-entre-formatos)).

---

#### **⚠️ Aviso Técnico - Limitações e Responsabilidades**

**Natureza dos dados:**
- As camadas de referência são provenientes de diferentes fontes (bases colaborativas, dados cadastrais, levantamentos)
- Os dados podem ter sido processados, filtrados ou ajustados, mas **não constituem uma base cartográfica oficial ou cadastral definitiva**
- **Não substituem levantamentos técnicos, bases oficiais ou cadastros normativos**

**Limitações conhecidas:**
- **Imprecisão posicional:** As geometrias podem apresentar desvios em relação à realidade de campo. A acurácia varia conforme a fonte original, método de aquisição e processamento aplicado.
- **Incompletude:** Alguns elementos, trechos, segmentos ou atributos podem estar ausentes, incompletos ou desatualizados.
- **Desatualização temporal:** Os dados refletem o estado das bases de dados no momento da extração. Alterações posteriores na infraestrutura ou nas bases de origem não estão refletidas.
- **Inconsistências cadastrais:** Podem existir diferenças entre camadas de diferentes fontes ou períodos.
- **Atributos:** Os atributos podem estar incompletos, inconsistentes ou seguir modelos de dados específicos da fonte original.

**Responsabilidade do usuário:**
- É de **responsabilidade exclusiva do usuário** verificar, validar e corrigir as informações antes de utilizá-las em produtos, estudos ou documentos oficiais.
- Recomenda-se **sempre conferir** as geometrias com:
  - Imagens de satélite atualizadas (alta resolução)
  - Bases cartográficas oficiais (IBGE, IGC-SP, DNIT)
  - Levantamentos de campo quando aplicável
  - Cadastros técnicos da concessionária/DER
- Para projetos que exigem **alta precisão cartográfica** (estudos de engenharia, projetos executivos, cadastros oficiais), considere utilizar bases cartográficas oficiais ou realizar levantamento topográfico/geodésico.

**Uso recomendado:**
- ✅ Referência visual para localização aproximada
- ✅ Auxílio na digitalização manual (com verificação obrigatória)
- ✅ Estudos preliminares, exploratórios ou de viabilidade
- ✅ Contextualização espacial e orientação geral
- ❌ **NÃO recomendado** para substituir levantamentos oficiais, cadastros técnicos ou bases cartográficas normativas
- ❌ **NÃO recomendado** para projetos que exigem certificação de acurácia posicional (PEC)

---

#### **Camadas Disponíveis**

As camadas estão organizadas por tipo de informação geoespacial e disponíveis em diferentes sistemas de coordenadas:

**📍 Malha Rodoviária**
- Descrição: Rede rodoviária do Estado de São Paulo
- Geometria: LineString
- CRS disponíveis: SIRGAS 2000 (EPSG:4674), UTM 22S (EPSG:31982), UTM 23S (EPSG:31983), UTM Combinado (EPSG:5880)
- Uso: Referência para digitalização de serviços lineares e pontuais ao longo das rodovias
- Versões: Base completa e versões filtradas segundo critérios normativos

**📌 Marcos Quilométricos**
- Descrição: Inventário de marcos quilométricos das rodovias concessionadas
- Geometria: Point
- CRS disponíveis: SIRGAS 2000 (EPSG:4674)
- Uso: Referência para localização por quilometragem, verificação de posicionamento de serviços
- Versões: Dados brutos e ajustados (reposicionados sobre a malha rodoviária)

**🗺️ Limites Administrativos**
- Descrição: Limites do Estado de São Paulo e municípios
- Geometria: Polygon / MultiPolygon
- CRS disponíveis: SIRGAS 2000 (EPSG:4674)
- Uso: Contextualização espacial, identificação de jurisdições, composições cartográficas

**🛣️ Base DER**
- Descrição: Rede oficial do DER-SP
- Geometria: MultiLineString
- CRS disponíveis: SIRGAS 2000 (EPSG:4674)
- Uso: Referência cadastral complementar, conferência com dados concessionados

---

#### **Sistemas de Coordenadas (CRS)**

As camadas estão disponíveis nos seguintes sistemas de referência espacial:

| Sistema | Código EPSG | Tipo | Cobertura | Quando Usar |
|---------|-------------|------|-----------|-------------|
| **SIRGAS 2000** | **4674** | Geográfico (lat/lon) | Estado de SP | **RECOMENDADO** - Compatível com schema GeoJSON exigido. Use para produto final. |
| UTM Zona 22S | 31982 | Projetado (métrico) | Região oeste de SP (~48°W - 54°W) | Medições em metros, análises métricas localizadas. |
| UTM Zona 23S | 31983 | Projetado (métrico) | Região leste de SP (~42°W - 48°W) | Medições em metros, análises métricas localizadas. Capital paulista nesta zona. |
| UTM Combinado 22S+23S | 5880 | Projetado (métrico) | Estado de SP completo | Projetos estaduais que requerem sistema métrico homogêneo. |

💡 **Importante:** O schema GeoJSON exige **EPSG:4674 (SIRGAS 2000 geográfico)** para o produto final. Se trabalhar em UTM durante a digitalização, reproje para EPSG:4674 antes de exportar.

---

#### **Atributos Típicos**

Os atributos variam conforme o tipo de camada. Exemplos:

**Malha Rodoviária:**
- `name`, `ref` - Nome e código da rodovia (ex: "SP-330", "Rodovia Anhanguera")
- `highway` - Classificação da via
- `operator`, `owner` - Concessionária/órgão responsável
- `lanes`, `maxspeed`, `surface` - Características técnicas (quando disponíveis)
- `toll` - Indicador de pedágio

**Marcos Quilométricos:**
- `CONCESSIONÁRIA`, `RODOVIA`, `KM`, `SENTIDO` - Identificação do marco
- `LAT`, `LONG` - Coordenadas originais
- `DESCRIÇÃO`, `TIPO` - Informações descritivas
- `deslocamento_m`, `dist_along_m` - Métricas de ajuste (versão ajustada)

**Limites Administrativos:**
- `NM_MUN`, `NM_ESTADO` - Nome do município/estado
- `CD_MUN`, `CD_GEOCUF` - Códigos IBGE
- `AREA_KM2` - Área territorial

> **Nota:** A completude e consistência dos atributos variam conforme a fonte e versão das camadas. Sempre verifique os metadados disponíveis no Portal de Dados Abertos.

---

#### **Conversão Entre Formatos**

Quando arquivos são distribuídos em GeoJSON, **recomendamos converter para GeoPackage localmente** se você pretende trabalhar frequentemente com essas camadas no QGIS.

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

Testes com malha rodoviária estadual (~20 MB) em computador médio (i5, 8GB RAM, SSD):

| Operação | GeoJSON | GeoPackage | Ganho |
|----------|---------|------------|-------|
| Abertura inicial | 3-4s | 0.5-1s | **5x mais rápido** |
| Zoom para extent completo | 1-2s | 0.2s | **5-10x mais rápido** |
| Pan (arrastar mapa) | Lag perceptível | Suave | **Muito melhor** |
| Buffer 1km (100 features) | 10-15s | 1-2s | **10x mais rápido** |
| Select by location | 5-8s | 0.5s | **10x mais rápido** |
| Snapping durante edição | Lento | Instantâneo | **Muito melhor** |

**Conclusão:** Para trabalho rotineiro no QGIS, **vale muito a pena converter**. O espaço extra em disco compensa pelo ganho enorme em produtividade.

---

##### **Método 1: Conversão via QGIS (Interface Gráfica)**

**Recomendado para:** Usuários que preferem interface gráfica.

**Passo a passo:**

1. **Abra o arquivo GeoJSON no QGIS:**
   - Arraste o arquivo `.geojson` para a janela do mapa
   - Ou: Menu → Camada → Adicionar Camada → Adicionar Camada Vetorial

2. **Verifique se a camada foi carregada:**
   - A camada deve aparecer no painel de Camadas
   - As features devem ser exibidas no mapa

3. **Exporte como GeoPackage:**
   - Clique direito na camada → **Exportar** → **Salvar Feições Como...**

4. **Configure a exportação:**
   - **Formato**: GeoPackage
   - **Nome do arquivo**: escolha um nome descritivo com extensão `.gpkg`
   - **Nome da camada**: nome descritivo da tabela dentro do GeoPackage
   - **SRC**: Manter o original (não alterar o sistema de coordenadas) - **NÃO MUDAR**
   - **Codificação**: UTF-8
   - **Opções de criação da camada**:
     - ✅ **Criar índice espacial**: MARQUE ESTA OPÇÃO (crítico para performance!)
   - **Incluir Z values**: Desmarcar (se não aplicável)

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
    arquivo_saida.gpkg \
    arquivo_entrada.geojson
```

**Comando com opções otimizadas (recomendado):**

```bash
ogr2ogr -f GPKG \
    -nln nome_camada \
    -lco SPATIAL_INDEX=YES \
    -progress \
    arquivo_saida.gpkg \
    arquivo_entrada.geojson
```

**Explicação das opções:**
- `-f GPKG` - Formato de saída: GeoPackage
- `-nln nome_camada` - Nome da camada (layer name) dentro do GeoPackage
- `-lco SPATIAL_INDEX=YES` - **CRÍTICO:** Criar índice espacial (performance)
- `-progress` - Mostrar barra de progresso da conversão

**Converter múltiplos arquivos de uma vez (Bash/Linux/macOS):**

```bash
#!/bin/bash
# Converte todos os arquivos GeoJSON para GeoPackage

for file in *.geojson; do
    output="${file%.geojson}.gpkg"
    echo "🔄 Convertendo: $file → $output"

    ogr2ogr -f GPKG \
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
# Converte todos os arquivos GeoJSON para GeoPackage

Get-ChildItem *.geojson | ForEach-Object {
    $output = $_.Name -replace '\.geojson$','.gpkg'

    Write-Host "🔄 Convertendo: $($_.Name) → $output" -ForegroundColor Cyan

    ogr2ogr -f GPKG `
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

Após a conversão, compare os tamanhos. Exemplo com malha rodoviária estadual:

| Formato | Tamanho Típico | Relação |
|---------|---------------|---------|
| GeoJSON | 20-25 MB | Base |
| GeoPackage | 40-55 MB | ~2-2.5x maior |

**Relação típica:** GeoPackage geralmente ocupa **2 a 2.5x mais espaço** que GeoJSON equivalente.

**Vale a pena?** Se você tem espaço em disco razoável (SSD moderno tem 256GB+), absolutamente sim pelos ganhos de performance.

---

##### **Verificar se o Índice Espacial foi Criado**

Após conversão, verifique se o índice espacial foi criado corretamente:

**Método 1: Via QGIS**
1. Abra o arquivo `.gpkg` no QGIS
2. Propriedades da camada → Informação
3. Procure por "Spatial Index: Yes"

**Método 2: Via ogrinfo (linha de comando)**
```bash
ogrinfo -al -so arquivo.gpkg nome_camada | grep -i "spatial"
```

Saída esperada:
```
Spatial Index: Yes
```

Se não tiver índice espacial, recrie com:
```bash
ogrinfo arquivo.gpkg -sql "SELECT CreateSpatialIndex('nome_camada', 'geom')"
```

---

##### **Melhores Práticas**

1. **Mantenha ambos os formatos inicialmente:**
   - GeoJSON: backup compacto, distribuível
   - GeoPackage: uso diário no QGIS
   - Após confirmar que tudo funciona, opte pelo formato mais adequado

2. **Use GeoPackage para trabalho no QGIS:**
   - Todas as camadas base/referência em `.gpkg`
   - Só exporte para GeoJSON quando precisar entregar/submeter

3. **Organize por projeto:**
   ```
   projeto/
   ├── referencia/
   │   ├── malha_rodoviaria.gpkg  (camada base)
   │   ├── marcos_km.gpkg
   │   └── limites_municipais.gpkg
   ├── trabalho/
   │   └── servicos_digitalizados.gpkg  (suas digitalizações)
   └── entrega/
       └── programacao_obras_2026_R0.geojson  (produto final)
   ```

4. **Backup:**
   - GeoPackage são arquivos binários - faça backup regular
   - Considere versionamento com Git (adicione `.gpkg` grandes no `.gitignore`)

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

➡️ [Seção 6.3 Passo C.3 - Camadas de Referência Geoespacial]({{< relref "06.3-metodo-qgis#passo-c3-camadas-de-referência-geoespacial-opcional" >}})

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
