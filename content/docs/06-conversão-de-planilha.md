---
title: "6. Conversão de Planilha"
weight: 60
bookToc: true
---

## **6. Conversão de Planilha (Excel/CSV) para GeoJSON**

Este capítulo apresenta métodos práticos para converter as planilhas Excel em arquivos GeoJSON válidos. Oferecemos opções tanto para **usuários sem conhecimento de programação** quanto para desenvolvedores.

**📥 Download dos Templates:**
Os templates estão disponíveis no Portal de Dados Abertos da ARTESP:
[https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)

- `template_lxx_conservacao_2026_r0.xlsx`
- `template_lxx_obras_2026_r0.xlsx`

---

### **6.1 Guia Passo a Passo para Não-Programadores**

Este guia é para usuários sem conhecimento de programação que precisam converter planilhas Excel em GeoJSON.

---

#### **6.1.1 Passo 1: Baixar e Preencher o Template**

1. **Baixe o template apropriado** do Portal de Dados Abertos:
   - Para conservação: `template_lxx_conservacao_2026_r0.xlsx`
   - Para obras: `template_lxx_obras_2026_r0.xlsx`

2. **Abra o arquivo no Excel ou LibreOffice Calc**

3. **Estrutura do template:**
   - **Linhas 1-2**: Cabeçalho (não modificar)
   - **Linhas 3-4**: Exemplos de preenchimento
   - **Linha 5**: Instruções de preenchimento
   - **Linha 6 em diante**: Área para seus dados

4. **Preencha seus dados a partir da linha 6:**

**Exemplo de preenchimento para CONSERVAÇÃO:**

| lote | rodovia    | item  | detalhamento_servico              | unidade | quantidade | km_inicial | km_final | local                   | data_inicial | data_final | observacoes_gerais |
|:-----|:-----------|:------|:----------------------------------|:--------|:-----------|:-----------|:---------|:------------------------|:-------------|:-----------|:-------------------|
| L13  | SP0000280  | a.1.1 | Recuperação funcional do pavimento| km      | 5.25       | 22.500     | 27.750   | PISTA_NORTE;PISTA_SUL   | 2026-03-15   | 2026-07-20 | Período noturno    |
| L13  | SP0000330  | c.2.4 | Sinalização vertical              | un      | 15         | 132.100    | 138.500  | PISTA_NORTE             | 2026-02-01   | 2026-02-15 |                    |

**Exemplo de preenchimento para OBRAS:**

| lote | rodovia    | programa | item | subitem | detalhamento_servico           | unidade | quantidade | km_inicial | km_final | local              | data_inicial | data_final | observacoes_gerais |
|:-----|:-----------|:---------|:-----|:--------|:-------------------------------|:--------|:-----------|:-----------|:---------|:-------------------|:-------------|:-----------|:-------------------|
| L19  | SP0000280  | CAPEX    | 1    | 1       | Construção de Passarela        | un      | 1.000      | 25.300     | 25.300   | DISPOSITIVO        | 2026-01-20   | 2026-11-30 | Área comercial     |
| L07  | SPA000292  | REVIT    | 3    | 2       | Faixas adicionais de rolamento | km      | 12.400     | 110.200    | 122.601  | PISTA_LESTE        | 2025-09-01   | 2027-05-15 |                    |

**⚠️ Pontos de Atenção:**

- **Campo `local`**: Use **ponto e vírgula (`;`)** para separar múltiplos locais
  - Exemplo: `PISTA_NORTE;PISTA_SUL;CANTEIRO_CENTRAL`
- **Campo `lote`**: Use o formato **L + 2 dígitos**
  - Correto: `L01`, `L13`, `L22`
  - Errado: `L1`, `L133`, `1`
- **Datas**: Formato **YYYY-MM-DD**
  - Correto: `2026-03-15`
  - Errado: `15/03/2026`, `03-15-2026`
- **Campo `observacoes_gerais`**: Deixe em branco se não houver observações (será convertido para `null`)
- **Separador decimal**: Use **ponto (`.`)**, não vírgula
  - Correto: `125.500`
  - Errado: `125,500`

---

#### **6.1.2 Passo 2: Usar QGIS para Converter em GeoJSON**

**QGIS** é um software livre de geoprocessamento que permite converter Excel para GeoJSON e adicionar geometrias.

**A. Instalação do QGIS:**

1. Baixe o QGIS em: [https://qgis.org/download/](https://qgis.org/download/)
2. Instale seguindo as instruções para seu sistema operacional

**B. Preparar a Planilha:**

1. Salve sua planilha Excel preenchida
2. **IMPORTANTE**: Se o Excel tiver múltiplas abas, certifique-se de que os dados estão na aba principal

**C. Importar para QGIS:**

1. Abra o QGIS
2. Menu: **Camada → Adicionar Camada → Adicionar Camada de Planilha**
3. Selecione seu arquivo Excel
4. Em "Nome da Planilha", selecione a aba com seus dados
5. **IMPORTANTE**: Marque a opção **"Detectar tipos de campos"**
6. Clique em **"Adicionar"**

**D. Criar Geometrias:**

Você tem duas opções para adicionar geometrias:

**Opção 1: Pontos a partir de Coordenadas**

Se você tem colunas com latitude e longitude na planilha:

1. Menu: **Camada → Criar Camada → Criar camada a partir de coordenadas**
2. Configure o sistema de coordenadas para **EPSG:4674 (SIRGAS 2000)**
3. Selecione as colunas de longitude e latitude

**Opção 2: Geometrias Manualmente (LineString, Polygon)**

1. Clique com botão direito na camada → **Alternar Edição**
2. Use as ferramentas de desenho para criar geometrias baseadas nas coordenadas km_inicial e km_final
3. Para cada linha da planilha, desenhe a geometria correspondente

**E. Exportar para GeoJSON:**

1. Clique com botão direito na camada
2. Selecione **"Exportar → Salvar Feições Como..."**
3. Configure:
   - **Formato**: GeoJSON
   - **Nome do arquivo**: `L13_conservacao_2026_R0.geojson` (ajuste conforme seu lote e tipo)
   - **SRC**: **EPSG:4674 - SIRGAS 2000**
   - **Opções de camada**:
     - Marque **"RFC7946": NO** (para usar `urn:ogc:def:crs:EPSG::4674`)
     - **Coordenadas**: 6 casas decimais
4. Clique em **"OK"**

**F. Ajustes Finais no GeoJSON:**

Após exportar, você precisará editar manualmente o arquivo GeoJSON para:

1. **Converter o campo `local`** de string para array:
   - De: `"local": "PISTA_NORTE;PISTA_SUL"`
   - Para: `"local": ["PISTA_NORTE", "PISTA_SUL"]`

2. **Adicionar campo `id` único** para cada feature:
   ```json
   "id": "conserva-001"
   ```

3. **Adicionar metadados**:
   ```json
   "metadata": {
     "schema_version": "R0",
     "data_geracao": "2025-11-10T10:30:00-03:00"
   }
   ```

4. **Ajustar o CRS** para o formato completo:
   ```json
   "crs": {
     "type": "name",
     "properties": {
       "name": "urn:ogc:def:crs:EPSG::4674"
     }
   }
   ```

**Ferramentas de edição JSON recomendadas:**
- **VS Code** com extensão "JSON Tools"
- **Notepad++** com plugin JSON Viewer
- **JSONLint** (online): [https://jsonlint.com/](https://jsonlint.com/)

---

#### **6.1.3 Passo 3: Validar o Arquivo**

Após criar o GeoJSON, **valide-o** usando o script fornecido pela ARTESP:

```bash
# Baixe o script validar_geojson.py e os schemas do Portal de Dados Abertos
# Execute:
uv run python validar_geojson.py schemas/conserva.schema.r0.json L13_conservacao_2026_R0.geojson
```

O script verificará:
- ✅ Conformidade com o schema
- ✅ Unicidade dos IDs
- ✅ Formato dos campos
- ✅ CRS correto

---

### **6.2 Método para Desenvolvedores: Python (pandas)**

O script em Python a seguir converte automaticamente a planilha Excel em GeoJSON, seguindo as regras do schema.

**Passo 1: Instalação das bibliotecas necessárias**

```bash
pip install pandas openpyxl
```

**Passo 2: Script de Conversão (Python 3.8+)**

> **Nota:** Este script converte a planilha Excel para GeoJSON, processando o campo `local` e gerando IDs únicos automaticamente. A geometria deve ser adicionada separadamente (ver Seção 7).

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
    print(f"⚠️  As geometrias estão NULL. Use QGIS para adicionar geometrias (Seção 7).")
    print(f"⚠️  Valide o arquivo antes de enviar: uv run python validar_geojson.py")


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
6. ✅ Adiciona metadados corretos (sem `ano_programacao`)
7. ⚠️ Cria geometrias NULL (devem ser adicionadas com QGIS - ver Seção 7)

---