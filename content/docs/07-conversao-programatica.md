---
title: "7. Conversão Programática para GeoJSON (Scripts e Automação)"
weight: 70
bookToc: true
---

## **7. Conversão Programática para GeoJSON (Scripts e Automação)**

Este capítulo apresenta métodos programáticos para converter planilhas e outras fontes de dados em arquivos GeoJSON válidos, usando **Python e ferramentas de linha de comando**.

**Público-alvo:** Desenvolvedores, analistas com conhecimento de Python/SQL, profissionais que preferem automação.

---

## **7.1 Introdução**

**Quando usar métodos programáticos:**
- Você tem experiência com Python ou programação
- Precisa processar grandes volumes de dados
- Deseja automatizar o processo de conversão
- Tem dados em múltiplas fontes (banco de dados, APIs, shapefiles, etc.)
- Precisa integrar a conversão em pipelines de dados existentes

**Requisitos técnicos:**
- Python 3.8+ instalado
- Bibliotecas: `pandas`, `openpyxl`, `shapely`, `geopandas` (conforme necessidade)
- Para PostGIS: `psycopg2`
- Para APIs: `requests`

**Instalação das bibliotecas básicas:**

```bash
pip install pandas openpyxl shapely geopandas
```

---

## **7.2 Conversão de Planilha Excel/CSV com Python**

O script em Python a seguir converte automaticamente a planilha Excel em GeoJSON, seguindo as regras do schema.

**Passo 1: Instalação das bibliotecas necessárias**

```bash
pip install pandas openpyxl
```

**Passo 2: Script de Conversão (Python 3.8+)**

> **Nota:** Este script converte a planilha Excel para GeoJSON, processando o campo `local` e gerando IDs únicos automaticamente. A geometria deve ser adicionada separadamente (ver Seção 7.3).

```python
import pandas as pd
import json
from datetime import datetime
import sys

def converter_planilha_para_geojson(
    arquivo_planilha,
    arquivo_geojson_saida,
    tipo_template,  # 'conservacao' ou 'obras'
    lote,  # Ex: 'L13'
    aba_planilha="Dados"
):
    """
    Converte uma planilha Excel/CSV para GeoJSON conforme schema R0.

    - Processa campo 'local' (separado por ';') para array
    - Gera campo 'id' único automaticamente
    - Configura metadados e CRS corretamente

    A geometria deve ser adicionada separadamente usando QGIS ou outro método.
    """

    print(f"Lendo planilha: {arquivo_planilha}")
    try:
        if arquivo_planilha.endswith('.xlsx'):
            # Ignora as 5 primeiras linhas do template (cabeçalho + instruções + exemplos)
            df = pd.read_excel(arquivo_planilha, sheet_name=aba_planilha, skiprows=5)
        elif arquivo_planilha.endswith('.csv'):
            df = pd.read_csv(arquivo_planilha, skiprows=5)
        else:
            raise ValueError("Formato não suportado (use .xlsx ou .csv)")
    except Exception as e:
        print(f"Erro ao ler planilha: {e}")
        return

    # Remover linhas completamente vazias
    df = df.dropna(how='all')

    print(f"Encontradas {len(df)} linhas de dados.")

    features = []

    for idx, row in df.iterrows():
        # Gerar ID único (formato: tipo-lote-sequencial)
        id_feature = f"{tipo_template[:8]}-{lote}-{str(idx+1).zfill(3)}"

        # Processar campo 'local' (separado por ';' no Excel → array no JSON)
        local_value = row.get('local', '')
        if pd.notna(local_value) and local_value:
            # Dividir por ';' e remover espaços
            local_array = [loc.strip() for loc in str(local_value).split(';') if loc.strip()]
        else:
            local_array = []

        # Construir properties
        properties = {
            'id': id_feature,
            'lote': row.get('lote'),
            'rodovia': row.get('rodovia'),
            'detalhamento_servico': row.get('detalhamento_servico'),
            'unidade': row.get('unidade'),
            'quantidade': row.get('quantidade'),
            'km_inicial': row.get('km_inicial'),
            'km_final': row.get('km_final'),
            'local': local_array,
            'data_inicial': row.get('data_inicial'),
            'data_final': row.get('data_final'),
            'observacoes_gerais': row.get('observacoes_gerais')
        }

        # Adicionar campos específicos por tipo
        if tipo_template == 'conservacao':
            properties['item'] = row.get('item')
        elif tipo_template == 'obras':
            properties['programa'] = row.get('programa')
            properties['item'] = int(row.get('item')) if pd.notna(row.get('item')) else None
            properties['subitem'] = int(row.get('subitem')) if pd.notna(row.get('subitem')) else None

        # Converter datas para string no formato YYYY-MM-DD
        for campo_data in ['data_inicial', 'data_final']:
            if pd.notna(properties[campo_data]):
                if isinstance(properties[campo_data], pd.Timestamp):
                    properties[campo_data] = properties[campo_data].strftime('%Y-%m-%d')
                else:
                    # Se já for string, garantir formato correto
                    properties[campo_data] = str(properties[campo_data])

        # Converter NaN/None para null JSON
        properties_limpas = {}
        for k, v in properties.items():
            if pd.isna(v) or (isinstance(v, str) and v.strip() == ''):
                properties_limpas[k] = None
            else:
                properties_limpas[k] = v

        # Criar feature (geometria None - deve ser adicionada depois)
        feature = {
            "type": "Feature",
            "geometry": None,  # Geometria deve ser adicionada com QGIS ou outro método
            "properties": properties_limpas
        }
        features.append(feature)

    # Estrutura GeoJSON final
    geojson_final = {
        "type": "FeatureCollection",
        "crs": {
            "type": "name",
            "properties": { "name": "urn:ogc:def:crs:EPSG::4674" }
        },
        "metadata": {
            "schema_version": "R0",
            "data_geracao": datetime.now().strftime('%Y-%m-%dT%H:%M:%S-03:00')
        },
        "features": features
    }

    # Salvar arquivo
    with open(arquivo_geojson_saida, 'w', encoding='utf-8') as f:
        json.dump(geojson_final, f, ensure_ascii=False, indent=2)

    print(f"✅ Arquivo '{arquivo_geojson_saida}' criado com {len(features)} features.")
    print(f"⚠️  As geometrias estão NULL. Use uma das opções da Seção 7.3 para adicionar geometrias.")
    print(f"⚠️  Valide o arquivo antes de enviar: python validar_geojson.py")


# --- Como usar ---
if __name__ == "__main__":
    if len(sys.argv) != 5:
        print("Uso: python converter.py <arquivo_excel> <conservacao|obras> <lote> <arquivo_saida>")
        print("Exemplo: python converter.py template_lxx_conservacao_2026_r0.xlsx conservacao L13 L13_conservacao_2026_R0.geojson")
        sys.exit(1)

    arquivo_entrada = sys.argv[1]
    tipo = sys.argv[2]
    lote = sys.argv[3]
    arquivo_saida = sys.argv[4]

    if tipo not in ['conservacao', 'obras']:
        print("Erro: O tipo deve ser 'conservacao' ou 'obras'")
        sys.exit(1)

    converter_planilha_para_geojson(
        arquivo_planilha=arquivo_entrada,
        arquivo_geojson_saida=arquivo_saida,
        tipo_template=tipo,
        lote=lote,
        aba_planilha="Dados"
    )
```

**Passo 3: Como executar o script**

```bash
# Para arquivo de conservação do lote L13
python converter.py template_lxx_conservacao_2026_r0.xlsx conservacao L13 L13_conservacao_2026_R0.geojson

# Para arquivo de obras do lote L22
python converter.py template_lxx_obras_2026_r0.xlsx obras L22 L22_obras_2026_R0.geojson
```

**O que o script faz:**

1. ✅ Lê a planilha Excel (pulando linhas de cabeçalho/exemplos)
2. ✅ Gera IDs únicos automaticamente (formato: `conservacao-L13-001`, `obra-L22-001`)
3. ✅ Converte campo `local` de string delimitada para array
4. ✅ Formata datas para YYYY-MM-DD
5. ✅ Configura CRS como `urn:ogc:def:crs:EPSG::4674`
6. ✅ Adiciona metadados corretos
7. ⚠️ Cria geometrias NULL (devem ser adicionadas - ver Seção 7.3)

---

## **7.3 Extração de Geometrias de Múltiplas Fontes**

Esta seção apresenta métodos para adicionar geometrias ao GeoJSON a partir de diferentes fontes de dados.

### **7.3.1 Fonte: Planilha com Coordenadas (lat/lon)**

Se sua planilha tem colunas `lat` e `lon`, você pode criar geometrias Point automaticamente:

```python
import pandas as pd
from shapely.geometry import Point, mapping
import json

def criar_point_de_planilha(df):
    """
    Cria geometrias Point a partir de colunas lat/lon.

    Parâmetros:
        df: DataFrame pandas com colunas 'lat' e 'lon'

    Retorna:
        Lista de features GeoJSON com geometrias Point
    """
    features = []

    for idx, row in df.iterrows():
        # Criar geometria Point
        point = Point(row['lon'], row['lat'])

        # Criar feature
        feature = {
            "type": "Feature",
            "geometry": mapping(point),  # Converte Shapely para GeoJSON
            "properties": {
                "id": row['id'],
                "lote": row['lote'],
                "rodovia": row['rodovia'],
                "detalhamento_servico": row['detalhamento_servico'],
                # ... adicionar outros campos conforme necessário
            }
        }
        features.append(feature)

    return features

# Exemplo de uso
df = pd.read_excel('dados_com_latlon.xlsx')
features = criar_point_de_planilha(df)
```

---

### **7.3.2 Fonte: Planilha com WKT**

Se sua planilha tem uma coluna `wkt` com geometrias em formato WKT:

```python
from shapely import wkt
from shapely.geometry import mapping
import pandas as pd

def criar_geometria_de_wkt(df):
    """
    Cria geometrias a partir de coluna WKT.

    Parâmetros:
        df: DataFrame pandas com coluna 'wkt'

    Retorna:
        Lista de features GeoJSON
    """
    features = []

    for idx, row in df.iterrows():
        # Parsear WKT para objeto Shapely
        geom = wkt.loads(row['wkt'])

        # Criar feature
        feature = {
            "type": "Feature",
            "geometry": mapping(geom),  # Converte Shapely para GeoJSON
            "properties": {
                "id": row['id'],
                "lote": row['lote'],
                "rodovia": row['rodovia'],
                "detalhamento_servico": row['detalhamento_servico'],
                # ... adicionar outros campos
            }
        }
        features.append(feature)

    return features

# Exemplo
df = pd.read_csv('dados_com_wkt.csv')
features = criar_geometria_de_wkt(df)
```

---

### **7.3.3 Fonte: Banco de Dados PostGIS**

Para extrair geometrias diretamente de um banco de dados PostGIS:

```mermaid
flowchart LR
    subgraph DB["🗄️ PostGIS Database"]
        Table[(Tabela com<br/>geometrias)]
    end

    subgraph Python["🐍 Python Script"]
        Conn[psycopg2.connect<br/>Conexão]
        Query[SQL Query<br/>ST_AsGeoJSON]
        Parse[json.loads<br/>Parse geometria]
        Build[Construir<br/>Features]
    end

    subgraph Output["📄 Arquivo GeoJSON"]
        JSON["FeatureCollection<br/>features: [...]"]
        File[L13_conservacao_2026_R0.geojson]
    end

    Table -->|SELECT id, ST_AsGeoJSON| Conn
    Conn --> Query
    Query -->|rows| Parse
    Parse --> Build
    Build -->|features list| JSON
    JSON -->|json.dump| File

    style DB fill:#e8f5e9,stroke:#4caf50,stroke-width:2px
    style Python fill:#e3f2fd,stroke:#2196f3,stroke-width:2px
    style Output fill:#fff3e0,stroke:#ff9800,stroke-width:2px
    style Table fill:#c8e6c9
    style File fill:#ffe0b2
```

```python
import psycopg2
import json

def extrair_de_postgis(query, connection_params):
    """
    Extrai geometrias de um banco PostGIS.

    Parâmetros:
        query: SQL com ST_AsGeoJSON(geom) AS geom_json
        connection_params: dict com host, database, user, password

    Retorna:
        Lista de features GeoJSON
    """
    # Conectar ao banco
    conn = psycopg2.connect(**connection_params)
    cursor = conn.cursor()

    # Executar query
    cursor.execute(query)
    rows = cursor.fetchall()

    features = []
    for row in rows:
        # Estrutura depende da query, este é um exemplo genérico
        feature = {
            "type": "Feature",
            "geometry": json.loads(row[1]),  # geom_json já está em formato GeoJSON
            "properties": {
                "id": row[0],
                "lote": row[2],
                "rodovia": row[3],
                "detalhamento_servico": row[4],
                "unidade": row[5],
                "quantidade": row[6],
                "km_inicial": row[7],
                "km_final": row[8],
                "local": row[9].split(';') if row[9] else [],  # Converter string para array
                "data_inicial": str(row[10]),
                "data_final": str(row[11]),
                "observacoes_gerais": row[12]
            }
        }
        features.append(feature)

    cursor.close()
    conn.close()

    return features

# Exemplo de uso
conn_params = {
    'host': 'localhost',
    'database': 'rodovias',
    'user': 'usuario',
    'password': 'senha'
}

query = """
SELECT
    id,
    ST_AsGeoJSON(ST_Transform(geom, 4674)) AS geom_json,
    lote,
    rodovia,
    detalhamento_servico,
    unidade,
    quantidade,
    km_inicial,
    km_final,
    local,
    data_inicial,
    data_final,
    observacoes_gerais
FROM obras_conservacao
WHERE ano_programacao = 2026
  AND lote = 'L13'
"""

features = extrair_de_postgis(query, conn_params)
```

**Dicas para PostGIS:**
- Use `ST_AsGeoJSON(geom)` para exportar geometria diretamente em formato GeoJSON
- Use `ST_Transform(geom, 4674)` se a geometria estiver em outro CRS
- Filtre dados no SQL para melhor performance (WHERE, LIMIT)
- Para grandes volumes, use `LIMIT` e `OFFSET` para processar em lotes

**Instalação do psycopg2:**

```bash
pip install psycopg2-binary
```

---

### **7.3.4 Fonte: GeoPackage**

GeoPackage (.gpkg) é um formato geoespacial baseado em SQLite:

```python
import geopandas as gpd
import json

def extrair_de_geopackage(gpkg_path, layer_name):
    """
    Extrai geometrias de um arquivo GeoPackage.

    Parâmetros:
        gpkg_path: Caminho para o arquivo .gpkg
        layer_name: Nome da camada a ser extraída

    Retorna:
        Lista de features GeoJSON
    """
    # Ler camada do GeoPackage
    gdf = gpd.read_file(gpkg_path, layer=layer_name)

    # Reprojetar para EPSG:4674 se necessário
    if gdf.crs.to_epsg() != 4674:
        print(f"Reprojetando de {gdf.crs} para EPSG:4674")
        gdf = gdf.to_crs(epsg=4674)

    # Converter para GeoJSON features
    features = []
    for idx, row in gdf.iterrows():
        feature = {
            "type": "Feature",
            "geometry": json.loads(gpd.GeoSeries([row.geometry]).to_json())['features'][0]['geometry'],
            "properties": {
                "id": row['id'],
                "lote": row['lote'],
                "rodovia": row['rodovia'],
                "detalhamento_servico": row['detalhamento_servico'],
                "unidade": row['unidade'],
                "quantidade": row['quantidade'],
                "km_inicial": row['km_inicial'],
                "km_final": row['km_final'],
                "local": row['local'].split(';') if isinstance(row['local'], str) else row['local'],
                "data_inicial": str(row['data_inicial']),
                "data_final": str(row['data_final']),
                "observacoes_gerais": row['observacoes_gerais']
            }
        }
        features.append(feature)

    return features

# Exemplo de uso
features = extrair_de_geopackage('dados_rodovias.gpkg', 'conservacao_2026')
```

**Instalação do GeoPandas:**

```bash
pip install geopandas
```

---

### **7.3.5 Fonte: Shapefile**

Para extrair geometrias de um Shapefile:

```python
import geopandas as gpd
import json

def extrair_de_shapefile(shp_path):
    """
    Extrai geometrias de um Shapefile.

    Parâmetros:
        shp_path: Caminho para o arquivo .shp

    Retorna:
        Lista de features GeoJSON
    """
    # Ler shapefile
    gdf = gpd.read_file(shp_path)

    # Reprojetar para EPSG:4674
    if gdf.crs.to_epsg() != 4674:
        print(f"Reprojetando de {gdf.crs} para EPSG:4674")
        gdf = gdf.to_crs(epsg=4674)

    # Converter para GeoJSON features
    features = json.loads(gdf.to_json())['features']

    # Processar properties para adequar ao schema
    for feature in features:
        # Processar campo 'local' se for string delimitada
        if 'local' in feature['properties']:
            local_str = feature['properties']['local']
            if isinstance(local_str, str) and ';' in local_str:
                feature['properties']['local'] = [
                    loc.strip() for loc in local_str.split(';') if loc.strip()
                ]

        # Garantir que campos obrigatórios existam
        # Adicionar validações conforme necessário

    return features

# Exemplo
features = extrair_de_shapefile('conservacao_2026.shp')
```

**Nota:** Shapef

iles têm limitações:
- Nomes de campos limitados a 10 caracteres
- Não suportam arrays (campo `local` precisará ser string delimitada)
- Encoding pode ser problemático (usar `encoding='utf-8'` ou `encoding='latin1'`)

---

### **7.3.6 Fonte: API REST/Web Service**

Para extrair dados de APIs REST:

```python
import requests
import json

def extrair_de_api(api_url, headers=None, params=None):
    """
    Extrai dados geográficos de uma API REST.

    Parâmetros:
        api_url: URL da API
        headers: Headers HTTP (autenticação, etc)
        params: Parâmetros da query string

    Retorna:
        Lista de features GeoJSON
    """
    response = requests.get(api_url, headers=headers, params=params)
    response.raise_for_status()  # Levanta exceção se status != 200

    data = response.json()

    # Processar resposta conforme formato da API
    # Este exemplo assume que a API retorna GeoJSON ou estrutura similar
    features = []

    for item in data.get('results', []):  # Ajustar conforme estrutura da API
        feature = {
            "type": "Feature",
            "geometry": item.get('geometry'),  # Ajustar conforme API
            "properties": {
                "id": item.get('id'),
                "lote": item.get('lote'),
                "rodovia": item.get('rodovia'),
                "detalhamento_servico": item.get('descricao'),  # Mapear campos
                "unidade": item.get('unidade'),
                "quantidade": item.get('quantidade'),
                "km_inicial": item.get('km_ini'),
                "km_final": item.get('km_fim'),
                "local": item.get('local', []),
                "data_inicial": item.get('data_inicio'),
                "data_final": item.get('data_termino'),
                "observacoes_gerais": item.get('observacoes')
            }
        }
        features.append(feature)

    return features

# Exemplo de uso
api_url = "https://api.exemplo.com/obras/2026"
headers = {"Authorization": "Bearer TOKEN_AQUI"}
params = {"lote": "L13", "tipo": "conservacao"}

features = extrair_de_api(api_url, headers=headers, params=params)
```

**Instalação do requests:**

```bash
pip install requests
```

---

## **7.4 Mesclagem de Dados (Atributos + Geometrias)**

Quando você tem atributos em uma planilha Excel e geometrias em outra fonte (shapefile, GeoPackage, banco de dados), você precisa mesclá-los.

```mermaid
flowchart TD
    subgraph Source["📊 Fontes de Dados"]
        Excel["📗 Excel/CSV<br/>Atributos<br/>id | lote | rodovia | ..."]
        Geom["🗺️ Geometrias<br/>Shapefile/GeoPackage/PostGIS<br/>id | geometry"]
    end

    subgraph Process["⚙️ Processo de Mesclagem"]
        Read1[pandas.read_excel<br/>Ler atributos]
        Read2[Extrair geometrias<br/>função específica]
        Dict[Criar dicionário<br/>geom_dict por ID]
        Merge[Mesclar por campo 'id'<br/>JOIN]
        Validate{ID encontrado<br/>em ambas fontes?}
    end

    subgraph Output["✅ Resultado"]
        Success[Feature completa<br/>geometry + properties]
        Error[⚠️ Registro sem geometria<br/>Log de erro]
        Final[GeoJSON Final<br/>FeatureCollection]
    end

    Excel --> Read1
    Geom --> Read2
    Read1 --> Merge
    Read2 --> Dict
    Dict --> Merge
    Merge --> Validate

    Validate -->|Sim| Success
    Validate -->|Não| Error

    Success --> Final
    Error --> Final

    style Excel fill:#e8f5e9,stroke:#4caf50,stroke-width:2px
    style Geom fill:#e1f5fe,stroke:#03a9f4,stroke-width:2px
    style Process fill:#f3e5f5,stroke:#9c27b0,stroke-width:2px
    style Success fill:#c8e6c9,stroke:#4caf50,stroke-width:2px
    style Error fill:#ffccbc,stroke:#ff5722,stroke-width:2px
    style Final fill:#fff9c4,stroke:#fbc02d,stroke-width:3px
```

### **7.4.1 Combinar Planilha Excel com Geometrias de Outra Fonte**

```python
import pandas as pd
import geopandas as gpd
import json

def mesclar_excel_com_geometrias(excel_path, geometrias_features, campo_chave='id'):
    """
    Combina atributos de planilha Excel com geometrias de outra fonte.

    Parâmetros:
        excel_path: Caminho para planilha Excel com atributos
        geometrias_features: Lista de features GeoJSON com geometrias
        campo_chave: Campo usado para fazer o join (padrão: 'id')

    Retorna:
        Lista de features GeoJSON mescladas
    """
    # Ler atributos da planilha
    df_atributos = pd.read_excel(excel_path, skiprows=5)  # Ajustar skiprows conforme template
    df_atributos = df_atributos.dropna(how='all')

    # Criar dicionário de geometrias por ID
    geom_dict = {}
    for feature in geometrias_features:
        key = feature['properties'].get(campo_chave)
        if key:
            geom_dict[key] = feature['geometry']

    # Mesclar
    features_mescladas = []
    erros = []

    for idx, row in df_atributos.iterrows():
        id_item = row.get(campo_chave)

        if id_item in geom_dict:
            # Processar campo 'local'
            local_value = row.get('local', '')
            if pd.notna(local_value) and local_value:
                local_array = [loc.strip() for loc in str(local_value).split(';') if loc.strip()]
            else:
                local_array = []

            # Criar feature mesclada
            feature = {
                "type": "Feature",
                "geometry": geom_dict[id_item],
                "properties": {
                    "id": id_item,
                    "lote": row.get('lote'),
                    "rodovia": row.get('rodovia'),
                    "detalhamento_servico": row.get('detalhamento_servico'),
                    "unidade": row.get('unidade'),
                    "quantidade": row.get('quantidade'),
                    "km_inicial": row.get('km_inicial'),
                    "km_final": row.get('km_final'),
                    "local": local_array,
                    "data_inicial": str(row.get('data_inicial')),
                    "data_final": str(row.get('data_final')),
                    "observacoes_gerais": row.get('observacoes_gerais')
                }
            }
            features_mescladas.append(feature)
        else:
            erros.append(f"⚠️ Aviso: ID '{id_item}' não encontrado nas geometrias")

    # Relatar erros
    if erros:
        print("\nRegistros sem geometria:")
        for erro in erros:
            print(f"  {erro}")

    print(f"\n✅ Mescladas {len(features_mescladas)} features")
    print(f"⚠️  {len(erros)} registros sem geometria")

    return features_mescladas

# Exemplo: Mesclar Excel com Shapefile
excel_file = 'atributos_conservacao.xlsx'
geometrias_shp = extrair_de_shapefile('geometrias_conservacao.shp')

features_final = mesclar_excel_com_geometrias(excel_file, geometrias_shp, campo_chave='id')
```

---

## **7.5 Processamento do Campo `local` (Automático)**

Função utilitária para garantir que o campo `local` esteja sempre no formato de array:

```python
def processar_campo_local(features):
    """
    Converte campo 'local' de string delimitada para array.
    Modifica as features in-place.

    Parâmetros:
        features: Lista de features GeoJSON

    Retorna:
        features modificadas (mesmo objeto de entrada)
    """
    for feature in features:
        local_value = feature['properties'].get('local')

        if isinstance(local_value, str):
            if local_value.strip():
                # Converter string delimitada em array
                feature['properties']['local'] = [
                    loc.strip() for loc in local_value.split(';') if loc.strip()
                ]
            else:
                feature['properties']['local'] = []
        elif local_value is None:
            feature['properties']['local'] = []
        # Se já for lista, manter como está

    return features

# Usar sempre antes de gerar o GeoJSON final
features = processar_campo_local(features)
```

---

## **7.6 Geração do Arquivo GeoJSON Final**

Função para gerar o arquivo GeoJSON final com todos os metadados corretos:

```python
from datetime import datetime
import json

def gerar_geojson_final(features, tipo, lote, output_path):
    """
    Gera arquivo GeoJSON final conforme especificação ARTESP R0.

    Parâmetros:
        features: Lista de features GeoJSON
        tipo: 'conservacao' ou 'obras'
        lote: Ex: 'L13'
        output_path: Caminho do arquivo de saída

    Retorna:
        None (salva arquivo em disco)
    """
    geojson_final = {
        "type": "FeatureCollection",
        "crs": {
            "type": "name",
            "properties": {
                "name": "urn:ogc:def:crs:EPSG::4674"
            }
        },
        "metadata": {
            "schema_version": "R0",
            "data_geracao": datetime.now().strftime('%Y-%m-%dT%H:%M:%S-03:00')
        },
        "features": features
    }

    # Salvar arquivo
    with open(output_path, 'w', encoding='utf-8') as f:
        json.dump(geojson_final, f, ensure_ascii=False, indent=2)

    print(f"\n{'='*60}")
    print(f"✅ Arquivo GeoJSON gerado com sucesso!")
    print(f"{'='*60}")
    print(f"📁 Arquivo: {output_path}")
    print(f"📊 Total de features: {len(features)}")
    print(f"📋 Tipo: {tipo}")
    print(f"🏷️  Lote: {lote}")
    print(f"{'='*60}\n")

    print("⚠️  Próximos passos:")
    print("  1. Validar o arquivo com: python validar_geojson.py")
    print("  2. Verificar visualmente as geometrias no QGIS")
    print("  3. Enviar para a ARTESP conforme instruções")

# Exemplo de uso
gerar_geojson_final(
    features=features_final,
    tipo='conservacao',
    lote='L13',
    output_path='L13_conservacao_2026_R0.geojson'
)
```

---

## **7.7 Script Completo de Exemplo**

Script unificado que combina todas as funcionalidades:

```python
#!/usr/bin/env python3
"""
Script completo para conversão de múltiplas fontes para GeoJSON ARTESP R0.

Suporta:
- Excel/CSV com lat/lon
- Excel/CSV com WKT
- PostGIS
- GeoPackage
- Shapefile
- API REST
- Mesclagem de atributos + geometrias

Uso:
    python converter_completo.py --fonte excel --arquivo dados.xlsx --tipo conservacao --lote L13
    python converter_completo.py --fonte postgis --config db_config.json --tipo obras --lote L22
    python converter_completo.py --fonte mesclar --atributos dados.xlsx --geometrias geom.shp --tipo conservacao --lote L13
"""

import argparse
import json
import pandas as pd
import geopandas as gpd
from datetime import datetime
import sys

# Importar todas as funções definidas anteriormente
# (Nota: Em produção, organize em módulos separados)

def main():
    parser = argparse.ArgumentParser(
        description='Conversor para GeoJSON ARTESP',
        formatter_class=argparse.RawDescriptionHelpFormatter
    )

    parser.add_argument('--fonte', required=True,
                       choices=['excel_latlon', 'excel_wkt', 'postgis', 'geopackage', 'shapefile', 'api', 'mesclar'],
                       help='Tipo de fonte de dados')
    parser.add_argument('--arquivo', help='Caminho do arquivo fonte')
    parser.add_argument('--atributos', help='Arquivo Excel com atributos (para --fonte mesclar)')
    parser.add_argument('--geometrias', help='Arquivo com geometrias (para --fonte mesclar)')
    parser.add_argument('--config', help='Arquivo JSON com configuração (para PostGIS ou API)')
    parser.add_argument('--tipo', required=True, choices=['conservacao', 'obras'])
    parser.add_argument('--lote', required=True, help='Ex: L13')
    parser.add_argument('--output', help='Arquivo de saída (opcional)')

    args = parser.parse_args()

    # Determinar arquivo de saída
    output_path = args.output or f"{args.lote}_{args.tipo}_2026_R0.geojson"

    # Extrair features conforme fonte
    features = []

    if args.fonte == 'excel_latlon':
        df = pd.read_excel(args.arquivo, skiprows=5)
        features = criar_point_de_planilha(df)

    elif args.fonte == 'excel_wkt':
        df = pd.read_csv(args.arquivo) if args.arquivo.endswith('.csv') else pd.read_excel(args.arquivo, skiprows=5)
        features = criar_geometria_de_wkt(df)

    elif args.fonte == 'postgis':
        with open(args.config, 'r') as f:
            config = json.load(f)
        features = extrair_de_postgis(config['query'], config['connection'])

    elif args.fonte == 'geopackage':
        layer = input("Nome da camada (ou pressione Enter para 'default'): ") or 'default'
        features = extrair_de_geopackage(args.arquivo, layer)

    elif args.fonte == 'shapefile':
        features = extrair_de_shapefile(args.arquivo)

    elif args.fonte == 'api':
        with open(args.config, 'r') as f:
            config = json.load(f)
        features = extrair_de_api(config['url'], config.get('headers'), config.get('params'))

    elif args.fonte == 'mesclar':
        geom_features = extrair_de_shapefile(args.geometrias)  # Ou outra fonte
        features = mesclar_excel_com_geometrias(args.atributos, geom_features)

    # Processar campo 'local'
    features = processar_campo_local(features)

    # Gerar arquivo final
    gerar_geojson_final(features, args.tipo, args.lote, output_path)

    print(f"\n✅ Processo concluído!")
    print(f"📁 Arquivo salvo em: {output_path}")

if __name__ == "__main__":
    main()
```

**Exemplos de uso:**

```bash
# Excel com lat/lon
python converter_completo.py --fonte excel_latlon --arquivo dados.xlsx --tipo conservacao --lote L13

# Shapefile
python converter_completo.py --fonte shapefile --arquivo dados.shp --tipo obras --lote L22

# Mesclar Excel + Shapefile
python converter_completo.py --fonte mesclar --atributos atrib.xlsx --geometrias geom.shp --tipo conservacao --lote L13

# PostGIS (com arquivo de configuração)
python converter_completo.py --fonte postgis --config db_config.json --tipo obras --lote L07
```

**Exemplo de arquivo de configuração para PostGIS (`db_config.json`):**

```json
{
  "connection": {
    "host": "localhost",
    "database": "rodovias",
    "user": "usuario",
    "password": "senha"
  },
  "query": "SELECT id, ST_AsGeoJSON(ST_Transform(geom, 4674)) AS geom_json, lote, rodovia, detalhamento_servico, unidade, quantidade, km_inicial, km_final, local, data_inicial, data_final, observacoes_gerais FROM obras WHERE lote = 'L07'"
}
```

---

## **7.8 Mesclagem de Arquivos GeoJSON (Múltiplas Geometrias)**

### **Contexto e Necessidade**

Quando você trabalha com QGIS e precisa criar geometrias de **tipos diferentes** (Point, LineString, Polygon) para representar diferentes serviços, você encontra uma limitação técnica:

**Problema:**
- Cada camada GeoPackage/Shapefile no QGIS aceita **apenas UM tipo de geometria**
- Se você tem serviços com Point + LineString + Polygon, precisa criar **3 camadas separadas**
- Ao exportar cada camada, você obtém **3 arquivos GeoJSON separados**

**Exigência do Schema:**
- O schema ARTESP R0 exige **UM ÚNICO arquivo GeoJSON** final
- Este arquivo deve conter **TODAS as features**, independente do tipo de geometria
- O schema permite geometrias mistas no mesmo FeatureCollection

**Solução:**
- Script Python `mesclar_geojson.py` que combina múltiplos arquivos GeoJSON em um único

---

### **7.8.1 Funcionalidades do Script**

O script `mesclar_geojson.py` realiza as seguintes operações:

1. ✅ **Carrega múltiplos arquivos GeoJSON** (2 ou mais)
2. ✅ **Valida estrutura** de cada arquivo (FeatureCollection, features, geometria, properties)
3. ✅ **Extrai todas as features** de todos os arquivos
4. ✅ **Verifica IDs únicos** - detecta e reporta IDs duplicados
5. ✅ **Combina em único FeatureCollection** preservando todos os atributos
6. ✅ **Adiciona CRS** (EPSG:4674 - SIRGAS 2000)
7. ✅ **Adiciona metadata** (schema_version: R0, data_geracao com timestamp)
8. ✅ **Gera relatório detalhado** com estatísticas de tipos de geometria
9. ✅ **Salva arquivo final** pronto para validação

---

### **7.8.2 Código-Fonte Completo**

O código completo está disponível em `/scripts/mesclar_geojson.py` do projeto.

**Estrutura do script:**

```python
#!/usr/bin/env python3
"""
Mescla múltiplos arquivos GeoJSON em um único FeatureCollection.
"""

import json
import sys
import os
import argparse
from datetime import datetime, timezone
from collections import Counter

# Funções principais:
# - validar_geojson_entrada(arquivo) → valida estrutura JSON/GeoJSON
# - extrair_features(data, arquivo) → extrai e valida features
# - validar_ids_unicos(features) → verifica duplicatas de IDs
# - mesclar_geojson_files(entrada, saida) → função principal de mesclagem
# - main() → CLI com argparse
```

**Principais funções:**

#### **validar_geojson_entrada(arquivo)**

Valida se um arquivo é um GeoJSON válido antes de processar:

```python
def validar_geojson_entrada(arquivo):
    """Valida se o arquivo é um GeoJSON válido."""
    try:
        with open(arquivo, 'r', encoding='utf-8') as f:
            data = json.load(f)
    except FileNotFoundError:
        print(f"❌ ERRO: Arquivo não encontrado: '{arquivo}'")
        return None
    except json.JSONDecodeError as e:
        print(f"❌ ERRO: Arquivo '{arquivo}' não é um JSON válido: {e}")
        return None

    # Verifica estrutura básica de GeoJSON
    if data.get('type') != 'FeatureCollection':
        print(f"⚠️  AVISO: '{arquivo}' não é um FeatureCollection")

    if 'features' not in data:
        print(f"⚠️  AVISO: '{arquivo}' não contém campo 'features'")

    return data
```

---

#### **extrair_features(data, arquivo)**

Extrai features válidas de um GeoJSON:

```python
def extrair_features(data, arquivo):
    """Extrai as features de um GeoJSON."""
    features = data.get('features', [])

    if not features:
        print(f"⚠️  AVISO: '{arquivo}' não contém features")
        return []

    features_validas = []
    for idx, feature in enumerate(features):
        # Valida estrutura mínima
        if 'geometry' not in feature:
            print(f"⚠️  Feature {idx} em '{arquivo}' sem geometria, ignorando")
            continue

        if 'properties' not in feature:
            print(f"⚠️  Feature {idx} em '{arquivo}' sem properties, ignorando")
            continue

        features_validas.append(feature)

    return features_validas
```

---

#### **validar_ids_unicos(features)**

Verifica se os IDs são únicos em todas as features:

```python
def validar_ids_unicos(features, emitir_aviso=True):
    """Valida se todos os IDs são únicos e reporta duplicatas."""
    ids = []
    for feature in features:
        feature_id = feature.get('properties', {}).get('id')
        if feature_id is not None:
            ids.append(feature_id)

    # Contar ocorrências de cada ID
    id_counts = Counter(ids)
    ids_duplicados = {id_val: count
                      for id_val, count in id_counts.items()
                      if count > 1}

    if emitir_aviso and ids_duplicados:
        print(f"\n⚠️  AVISO: {len(ids_duplicados)} IDs duplicados:")
        for dup_id, count in sorted(ids_duplicados.items()):
            print(f"  ID '{dup_id}' aparece {count} vezes")

    return len(set(ids)), ids_duplicados
```

---

#### **mesclar_geojson_files(arquivos_entrada, arquivo_saida)**

Função principal que realiza a mesclagem:

```python
def mesclar_geojson_files(arquivos_entrada, arquivo_saida, incluir_metadata=True):
    """Mescla múltiplos arquivos GeoJSON em um único FeatureCollection."""

    todas_features = []

    # Processar cada arquivo de entrada
    for arquivo in arquivos_entrada:
        data = validar_geojson_entrada(arquivo)
        if data is None:
            continue

        features = extrair_features(data, arquivo)
        if not features:
            continue

        # Reportar tipos de geometria
        geom_types = Counter(f.get('geometry', {}).get('type')
                            for f in features)
        print(f"✅ {len(features)} features extraídas:")
        for geom_type, count in sorted(geom_types.items()):
            print(f"   - {geom_type}: {count}")

        todas_features.extend(features)

    # Validar IDs únicos
    ids_unicos, ids_duplicados = validar_ids_unicos(todas_features)

    # Construir GeoJSON de saída
    geojson_saida = {
        "type": "FeatureCollection",
        "features": todas_features
    }

    if incluir_metadata:
        geojson_saida["crs"] = {
            "type": "name",
            "properties": {
                "name": "urn:ogc:def:crs:EPSG::4674"
            }
        }

        # Data/hora com timezone
        agora = datetime.now(timezone.utc).astimezone()
        data_geracao = agora.strftime('%Y-%m-%dT%H:%M:%S%z')
        data_geracao = data_geracao[:-2] + ':' + data_geracao[-2:]

        geojson_saida["metadata"] = {
            "schema_version": "R0",
            "data_geracao": data_geracao
        }

    # Salvar arquivo
    with open(arquivo_saida, 'w', encoding='utf-8') as f:
        json.dump(geojson_saida, f, ensure_ascii=False, indent=2)

    print(f"\n✅ Arquivo mesclado salvo: {arquivo_saida}")

    return True
```

---

### **7.8.3 Exemplos de Uso**

#### **Exemplo 1: Mesclar 2 arquivos (Points + LineStrings)**

```bash
python scripts/mesclar_geojson.py \
    L13_conservacao_points.geojson \
    L13_conservacao_lines.geojson \
    -o L13_conservacao_2026_R0.geojson
```

**Saída esperada:**

```
======================================================================
  Mesclagem de Arquivos GeoJSON
======================================================================

📂 Arquivos de entrada: 2

[1/2] Processando: L13_conservacao_points.geojson
------------------------------------------------------------
✅ 12 features extraídas:
   - Point: 12

[2/2] Processando: L13_conservacao_lines.geojson
------------------------------------------------------------
✅ 5 features extraídas:
   - LineString: 5

======================================================================
📊 Resumo da Mesclagem
======================================================================
Total de features: 17
Tipos de geometria:
  - LineString: 5
  - Point: 12

Verificando unicidade dos IDs...
IDs únicos: 17

✅ Arquivo mesclado salvo com sucesso!
📁 Arquivo de saída: L13_conservacao_2026_R0.geojson
📏 Tamanho: 28.4 KB

======================================================================
✅ Próximo passo: Validar com validar_geojson.py
======================================================================
```

---

#### **Exemplo 2: Mesclar 3 arquivos (Points + LineStrings + Polygons)**

```bash
python scripts/mesclar_geojson.py \
    L22_obras_points_TEMP.geojson \
    L22_obras_lines_TEMP.geojson \
    L22_obras_polygons_TEMP.geojson \
    -o L22_obras_2026_R0.geojson
```

---

#### **Exemplo 3: Mesclar sem adicionar metadata**

Útil se você quiser adicionar metadata manualmente depois:

```bash
python scripts/mesclar_geojson.py \
    arquivo1.geojson \
    arquivo2.geojson \
    -o saida.geojson \
    --no-metadata
```

---

#### **Exemplo 4: Mesclar 4 ou mais arquivos**

O script aceita quantos arquivos você precisar:

```bash
python scripts/mesclar_geojson.py \
    L07_points.geojson \
    L07_lines.geojson \
    L07_polygons.geojson \
    L07_multipoints.geojson \
    -o L07_conservacao_2026_R0.geojson
```

---

### **7.8.4 Opções da Linha de Comando**

```bash
python mesclar_geojson.py [arquivos...] -o SAIDA [opções]
```

**Argumentos posicionais:**
- `arquivos` - Lista de arquivos GeoJSON para mesclar (mínimo 2)

**Argumentos obrigatórios:**
- `-o`, `--output` - Arquivo de saída (GeoJSON mesclado)

**Argumentos opcionais:**
- `--no-metadata` - Não adicionar campos CRS e metadata ao arquivo de saída
- `-h`, `--help` - Exibir ajuda

**Exemplos:**

```bash
# Ajuda
python mesclar_geojson.py --help

# Uso mínimo
python mesclar_geojson.py arq1.geojson arq2.geojson -o saida.geojson

# Com múltiplos arquivos
python mesclar_geojson.py *.geojson -o combinado.geojson

# Sem metadata
python mesclar_geojson.py a.geojson b.geojson -o saida.geojson --no-metadata
```

---

### **7.8.5 Validações Realizadas**

O script realiza as seguintes validações durante o processamento:

| Validação | Descrição | Comportamento em caso de erro |
|-----------|-----------|-------------------------------|
| **Arquivo existe** | Verifica se arquivo de entrada existe | ❌ Reporta erro e pula arquivo |
| **JSON válido** | Valida sintaxe JSON | ❌ Reporta erro e pula arquivo |
| **FeatureCollection** | Verifica se type = "FeatureCollection" | ⚠️ Emite aviso, continua processando |
| **Campo features** | Verifica se contém campo "features" | ⚠️ Emite aviso, tenta extrair |
| **Feature tem geometry** | Cada feature deve ter geometria | ⚠️ Pula feature inválida |
| **Feature tem properties** | Cada feature deve ter properties | ⚠️ Pula feature inválida |
| **IDs únicos** | Verifica duplicatas entre TODOS os IDs | ⚠️ Emite aviso, continua (não bloqueia) |
| **Arquivo saída existe** | Pergunta se deseja sobrescrever | ⚠️ Solicita confirmação do usuário |

**Nota importante sobre IDs duplicados:**
- O script **detecta e reporta** IDs duplicados, mas **não bloqueia** a mesclagem
- Isso permite que você veja o problema e corrija depois
- IDs duplicados **falharão na validação** com `validar_geojson.py`
- **Corrija os IDs antes de submeter o arquivo**

---

### **7.8.6 Estrutura do GeoJSON de Saída**

O arquivo mesclado terá a seguinte estrutura:

```json
{
  "type": "FeatureCollection",
  "crs": {
    "type": "name",
    "properties": {
      "name": "urn:ogc:def:crs:EPSG::4674"
    }
  },
  "metadata": {
    "schema_version": "R0",
    "data_geracao": "2026-01-15T14:30:00-03:00"
  },
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [-46.633, -23.550]
      },
      "properties": {
        "id": "conserva-001",
        "lote": "L13",
        ...
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [[-46.633, -23.550], [-46.634, -23.551]]
      },
      "properties": {
        "id": "conserva-002",
        ...
      }
    },
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [[...]]
      },
      "properties": {
        "id": "conserva-003",
        ...
      }
    }
  ]
}
```

**Campos adicionados automaticamente:**
- `crs` - Sistema de coordenadas (EPSG:4674)
- `metadata.schema_version` - Versão do schema (R0)
- `metadata.data_geracao` - Timestamp ISO8601 com timezone

---

### **7.8.7 Tratamento de Erros Comuns**

#### **Erro: "File not found"**

```
❌ ERRO: Arquivo não encontrado: 'arquivo.geojson'
```

**Solução:**
- Verifique o caminho do arquivo
- Use caminho absoluto ou relativo correto
- No Windows, use `\` ou `/` nas pastas

---

#### **Erro: "Arquivo não é um JSON válido"**

```
❌ ERRO: Arquivo 'dados.geojson' não é um JSON válido:
Expecting ',' delimiter: line 15 column 5
```

**Solução:**
- Abra o arquivo em um editor JSON (VS Code)
- Corrija erros de sintaxe (vírgulas, aspas, chaves)
- Valide com um validador JSON online

---

#### **Aviso: "IDs duplicados encontrados"**

```
⚠️  AVISO: 2 IDs duplicados encontrados:
  ID 'conserva-005' aparece 2 vezes
  ID 'conserva-012' aparece 3 vezes
```

**Solução:**
1. Abra os arquivos de entrada no QGIS
2. Localize as features com IDs duplicados (tabela de atributos)
3. Corrija os IDs para serem únicos
4. Re-exporte as camadas
5. Execute o script novamente

---

#### **Aviso: "Feature sem geometria"**

```
⚠️  Feature 5 em 'arquivo.geojson' sem geometria, ignorando
```

**Solução:**
- A feature será ignorada (pulada)
- Verifique no QGIS se todas features têm geometria válida
- Re-exporte a camada se necessário

---

### **7.8.8 Integração com Workflow QGIS**

O script se integra perfeitamente no workflow QGIS:

```mermaid
flowchart LR
    QGIS["QGIS:<br/>Criar múltiplas camadas"]
    Export["Exportar<br/>cada camada"]
    Files["Múltiplos<br/>GeoJSON"]
    Script["mesclar_geojson.py"]
    Final["GeoJSON<br/>único"]
    Validate["validar_geojson.py"]

    QGIS --> Export
    Export --> Files
    Files --> Script
    Script --> Final
    Final --> Validate

    style Script fill:#fff4e1
    style Final fill:#e7f9e7
```

**Workflow completo:**

1. **QGIS:** Criar camadas (points.gpkg, lines.gpkg, polygons.gpkg)
2. **QGIS:** Exportar cada camada como GeoJSON
3. **Script:** `python mesclar_geojson.py *.geojson -o final.geojson`
4. **Validação:** `python validar_geojson.py schema.json final.geojson`
5. **Submissão:** Upload no portal ARTESP

---

### **7.8.9 Download e Disponibilidade**

**Onde encontrar o script:**

1. **Pasta do projeto:** `/scripts/mesclar_geojson.py`
2. **Portal de Dados Abertos ARTESP:**
   - https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras
3. **Código-fonte** completo nesta documentação (Seção 7.8.2)

**Requisitos:**
- Python 3.6 ou superior
- Bibliotecas padrão (json, sys, os, argparse, datetime, collections)
- Nenhuma dependência externa necessária

**Instalação:**
```bash
# Nenhuma instalação necessária - use o script diretamente
python scripts/mesclar_geojson.py --help
```

---

### **7.8.10 Referências Cruzadas**

- **Uso prático no QGIS:** [Seção 6.5.A - Múltiplas Camadas]({{< relref "06.5-exportar-validar#a-tratamento-de-múltiplas-camadas-com-geometrias-diferentes" >}})
- **Aviso na criação de camadas:** [Seção 6.3 Passo D]({{< relref "06.3-metodo-qgis#passo-d-criar-camada-de-geometrias" >}})
- **Validação após mesclagem:** [Capítulo 9 - Validação]({{< relref "09-validação-dos-arquivos" >}})
- **Script de validação:** [Seção 9.1.3]({{< relref "09-validação-dos-arquivos#913-validação-via-script-python" >}})

---

**🎉 Parabéns!** Agora você tem um conjunto completo de ferramentas programáticas para converter dados de múltiplas fontes para GeoJSON conforme o schema ARTESP R0.

**Próximos passos:**
1. Adapte os scripts conforme suas necessidades específicas
2. Teste com dados de desenvolvimento primeiro
3. Valide sempre com `python validar_geojson.py`
4. Consulte o **Capítulo 9** para validação detalhada
5. Consulte o **Capítulo 10** para solução de erros comuns
