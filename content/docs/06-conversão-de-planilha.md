---
title: "6. Conversão de Planilha"
weight: 60
bookToc: true
---

## **6. Conversão de Planilha (Excel/CSV) para GeoJSON**

Devido ao modelo de dados de agrupamento (Seção 5), métodos simples de conversão (como `ogr2ogr` ou conversores online de "1 linha por feature") **não funcionarão corretamente**.

> **⚠️ Atenção:** Conversores simples criarão features duplicadas (uma para `PISTA_NORTE` e outra para `PISTA_SUL`) em vez de uma única feature com um array `["PISTA_NORTE", "PISTA_SUL"]`.

---

### **6.1 Método Recomendado: Python (pandas)**

O script em Python a seguir implementa a lógica de agrupamento e agregação necessária. Ele lê a planilha (Excel ou CSV), agrupa por `id` e constrói o GeoJSON corretamente.

**Passo 1: Instalação das bibliotecas necessárias**

```bash
pip install pandas openpyxl
```

**Passo 2: Script de Conversão (Python 3.8+)**

> **Nota:** Este script foca em agrupar as propriedades da planilha. A geometria deve ser vinculada separadamente.

```python
import pandas as pd
import json
from datetime import datetime
import sys

def converter_planilha_para_geojson(
    arquivo_planilha,
    arquivo_geojson_saida,
    tipo_template,  # 'conservacao' ou 'obras'
    aba_planilha="Dados"
):
    """
    Converte uma planilha Excel/CSV para GeoJSON, agrupando
    os dados pelo 'id' e agregando os valores do campo 'local'.

    Este script foca na união das **propriedades** da planilha.
    A geometria deve ser vinculada a partir de um arquivo
    GeoJSON de georreferenciamento separado, usando o 'id' como chave.
    """

    print(f"Lendo planilha: {arquivo_planilha}")
    try:
        if arquivo_planilha.endswith('.xlsx'):
            # Ignora as 6 primeiras linhas do template (cabeçalho + instruções + exemplos)
            df = pd.read_excel(arquivo_planilha, sheet_name=aba_planilha, skiprows=6)
        elif arquivo_planilha.endswith('.csv'):
            # Ignora as 6 primeiras linhas do template (cabeçalho + instruções + exemplos)
            df = pd.read_csv(arquivo_planilha, skiprows=6)
        else:
            raise ValueError("Formato de arquivo não suportado (use .xlsx ou .csv)")
    except Exception as e:
        print(f"Erro ao ler a planilha (verifique o caminho e as 6 primeiras linhas de exemplo): {e}")
        return

    # Limpar dados: remover linhas onde 'id' é nulo
    df = df.dropna(subset=['id'])

    # Converter 'id' para inteiro
    df['id'] = df['id'].astype(int)

    print(f"Encontradas {len(df)} linhas de dados. Agrupando por 'id'...")

    # Agrupar por 'id' e agregar os campos
    # 1. 'local' é agregado em uma lista única
    # 2. 'first' pega o primeiro valor para todos os outros campos
    #    (assumindo que são idênticos para o mesmo 'id')

    # Campos que são dados-mestre (pegamos o primeiro)
    if tipo_template == 'conservacao':
        campos_principais = [
            'lote', 'rodovia', 'item', 'detalhamento_servico', 'unidade',
            'quantidade', 'km_inicial', 'km_final', 'data_inicial',
            'data_final', 'observacoes_gerais'
        ]
    elif tipo_template == 'obras':
        campos_principais = [
            'lote', 'rodovia', 'programa', 'item', 'subitem',
            'detalhamento_servico', 'unidade', 'quantidade', 'km_inicial',
            'km_final', 'data_inicial', 'data_final', 'observacoes_gerais'
        ]
    else:
        raise ValueError("tipo_template deve ser 'conservacao' ou 'obras'")

    # Dicionário de agregação
    # 'local' é agregado em uma lista (set para remover duplicatas)
    # outros campos pegam o primeiro valor
    agregacoes = {
        'local': lambda x: sorted(list(set(x)))
    }
    for campo in campos_principais:
        if campo in df.columns:
            agregacoes[campo] = 'first'
        else:
            print(f"Aviso: Coluna esperada '{campo}' não encontrada na planilha.")

    try:
        df_agrupado = df.groupby('id').agg(agregacoes)
    except Exception as e:
        print(f"Erro ao agrupar dados. Verifique as colunas da planilha: {e}")
        return

    print(f"Agrupamento concluído. {len(df_agrupado)} features únicas de GeoJSON serão criadas.")

    features = []

    # IMPORTANTE: Este script NÃO CRIA A GEOMETRIA.
    # Ele apenas prepara as PROPRIEDADES. A geometria deve vir
    # de um arquivo GeoJSON de referência.
    # Para este exemplo, criamos uma geometria NULA.

    for feature_id, row in df_agrupado.iterrows():
        properties = row.to_dict()

        # Adicionar o 'id' (que era o índice) de volta às propriedades
        properties['id'] = int(feature_id)

        # Corrigir tipos de dados (pandas pode converter para float)
        if 'item' in properties and not pd.isna(properties['item']):
            properties['item'] = int(properties['item']) if tipo_template == 'obras' else str(properties['item'])
        if 'subitem' in properties and not pd.isna(properties['subitem']):
            properties['subitem'] = int(properties['subitem'])

        # Tratar NaN/NaT para None (JSON null)
        properties_limpas = {}
        for k, v in properties.items():
            if pd.isna(v):
                properties_limpas[k] = None
            elif isinstance(v, pd.Timestamp):
                 properties_limpas[k] = v.isoformat()
            else:
                properties_limpas[k] = v

        # (Lógica de geometria NULA para este exemplo)
        feature = {
            "type": "Feature",
            "geometry": None,  # A GEOMETRIA DEVE SER INSERIDA AQUI
            "properties": properties_limpas
        }
        features.append(feature)

    # Estrutura GeoJSON final
    geojson_final = {
        "type": "FeatureCollection",
        "crs": {
            "type": "name",
            "properties": { "name": "EPSG:4674" }
        },
        "metadata": {
            "schema_version": "R0",
            "ano_programacao": 2026,
            "data_geracao": datetime.now().isoformat()
        },
        "features": features
    }

    # Salvar arquivo
    with open(arquivo_geojson_saida, 'w', encoding='utf-8') as f:
        json.dump(geojson_final, f, ensure_ascii=False, indent=2)

    print(f"✅ Sucesso: Arquivo '{arquivo_geojson_saida}' criado com {len(features)} features.")
    print("⚠️ ATENÇÃO: As geometrias no arquivo estão NULAS (None).")
    print("   Este script apenas processou as propriedades. A geometria real deve ser")
    print("   vinculada a partir de um arquivo GeoJSON de georreferenciamento usando o 'id'.")


# --- Exemplo de como usar ---
if __name__ == "__main__":

    # Verifique se os argumentos da linha de comando foram passados
    # python script.py <arquivo_excel> <tipo: conservacao|obras> <arquivo_saida>
    if len(sys.argv) != 4:
        print("Uso: python converter.py <arquivo_planilha_entrada> <conservacao|obras> <arquivo_geojson_saida>")
        sys.exit(1)

    arquivo_entrada = sys.argv[1]
    tipo = sys.argv[2]
    arquivo_saida = sys.argv[3]

    if tipo not in ['conservacao', 'obras']:
        print("Erro: O tipo deve ser 'conservacao' ou 'obras'")
        sys.exit(1)

    converter_planilha_para_geojson(
        arquivo_planilha=arquivo_entrada,
        arquivo_geojson_saida=arquivo_saida,
        tipo_template=tipo,
        aba_planilha="Dados"  # Nome da aba na planilha
    )
```

**Passo 3: Como executar o script**

```bash
# Para arquivo de conservação
python converter.py planilha_conservacao.xlsx conservacao L13_conservacao_2026_R0.geojson

# Para arquivo de obras
python converter.py planilha_obras.xlsx obras L22_obras_2026_R0.geojson
```

---