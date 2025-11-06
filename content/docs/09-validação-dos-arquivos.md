---
title: "9. Validação dos Arquivos"
weight: 90
bookToc: true
---

## **9. Validação dos Arquivos**

### **9.1 Validação com JSON Schema**

A ARTESP fornece schemas JSON para validação automatizada.

**📥 Download dos Schemas:**
[https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)

---

#### **9.1.1 Usando AJV (Node.js)**

**Instalação e uso:**

```bash
# Instalação:
npm install -g ajv-cli

# Validar arquivo de conservação:
ajv validate -s conserva.schema.json -d L13_conservacao_2026_R0.geojson
```

---

#### **9.1.2 Usando Python (jsonschema)**

**Exemplo de função de validação em Python:**

```python
import json
from jsonschema import validate, ValidationError

def validar_geojson(geojson_file, schema_file):
    # Carregar arquivos
    with open(geojson_file, 'r', encoding='utf-8') as f:
        geojson = json.load(f)

    with open(schema_file, 'r', encoding='utf-8') as f:
        schema = json.load(f)

    try:
        validate(instance=geojson, schema=schema)
        print(f"✓ {geojson_file} é válido!")
        return True
    except ValidationError as e:
        print(f"✗ {geojson_file} possui erros:")
        print(f"  Caminho: {e.path}")
        print(f"  Mensagem: {e.message}")
        return False

# Instalação: pip install jsonschema
```

---

#### **9.1.3 Usando o Script Fornecido pela ARTESP (validar_geojson.py)**

A ARTESP disponibiliza um script Python pronto para validação, que pode ser baixado no Portal de Dados Abertos.

**📥 Download do Script:**
[https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras](https://dadosabertos.artesp.sp.gov.br/dataset/programacao-de-obras)

**Arquivo:** `validar_geojson.py`

**Pré-requisitos:**

```bash
# Instalar a biblioteca jsonschema
pip install jsonschema

# ou usando uv:
uv pip install jsonschema
```

**Como usar:**

O script recebe dois argumentos: o caminho para o schema JSON e o caminho para o arquivo GeoJSON a ser validado.

```bash
# Validar arquivo de conservação:
python validar_geojson.py schemas/conserva.schema.json L13_conservacao_2026_R0.geojson

# Validar arquivo de obras:
python validar_geojson.py schemas/obras.schema.json L22_obras_2026_R0.geojson

# Usando uv (recomendado):
uv run python validar_geojson.py schemas/conserva.schema.json L13_conservacao_2026_R0.geojson
```

**Saída do script em caso de sucesso:**

```
Validando 'L13_conservacao_2026_R0.geojson' contra 'conserva.schema.json'...
----------------------------------------------------------------------
[1/3] ✅ Schema 'conserva.schema.json' carregado com sucesso.
[2/3] ✅ Arquivo GeoJSON 'L13_conservacao_2026_R0.geojson' carregado com sucesso.
[3/3] ✅ Validação concluída.

🎉 SUCESSO: O arquivo 'L13_conservacao_2026_R0.geojson' é válido.
```

**Saída do script em caso de erro:**

```
Validando 'L13_conservacao_2026_R0.geojson' contra 'conserva.schema.json'...
----------------------------------------------------------------------
[1/3] ✅ Schema 'conserva.schema.json' carregado com sucesso.
[2/3] ✅ Arquivo GeoJSON 'L13_conservacao_2026_R0.geojson' carregado com sucesso.
[3/3] ❌ Validação falhou.

🔥 FALHA: O arquivo 'L13_conservacao_2026_R0.geojson' é inválido.
-------------------- Detalhes do Erro --------------------
  Mensagem: 'id' is a required property
  Caminho: features[0].properties
  Schema Path: ['properties', 'features', 'items', 'properties', 'properties', 'required']
----------------------------------------------------------
```

**Vantagens do script fornecido:**

- ✅ Interface amigável com mensagens em português
- ✅ Exibe progresso da validação em 3 etapas
- ✅ Mensagens de erro detalhadas e formatadas
- ✅ Indica exatamente onde está o problema (caminho no JSON)
- ✅ Retorna código de saída apropriado (0 = sucesso, 1 = erro) para automação

**Integração com pipelines de CI/CD:**

O script pode ser usado em processos automatizados:

```bash
#!/bin/bash
# Exemplo de script de validação automatizada

if python validar_geojson.py schemas/conserva.schema.json L13_conservacao_2026_R0.geojson; then
    echo "Validação passou! Arquivo pronto para envio."
    exit 0
else
    echo "Validação falhou! Corrija os erros antes do envio."
    exit 1
fi
```

---

### **9.2 Validação Geométrica**

Verificação manual no QGIS ou com Python/Shapely para validar se as geometrias são topologicamente corretas.

---

### **9.3 Checklist de Conformidade**

#### **9.3.1 Checklist de Propriedades**

- ✅ Campo `id` presente, inteiro e não nulo
- ✅ Todos os campos obrigatórios presentes
- ✅ Valores de lote dentro dos permitidos
- ✅ Códigos de rodovia no padrão correto
- ✅ Campo `local` é um array com pelo menos 1 item
- ✅ Datas no formato ISO 8601 com fuso horário
- ✅ Coordenadas dentro do território brasileiro

---