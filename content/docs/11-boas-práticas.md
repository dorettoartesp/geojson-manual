---
title: "11. Boas Práticas"
weight: 110
bookToc: true
---

## **11. Boas Práticas**

### **11.1 Organização dos Dados**

1. **Mantenha um arquivo-fonte original:** Nunca modifique seus dados originais diretamente. Mantenha-os em formato Excel/CSV e gere os arquivos GeoJSON a partir deles.

2. **Use controle de versão:** Nomeie seus arquivos de trabalho com data/versão para facilitar o rastreamento.
   **Exemplo:** `conserva_2025-10-31_v2.geojson`

3. **Documente o processo:** Mantenha um log de como os dados foram gerados.

---

### **11.2 Escolha do Tipo de Geometria**

O tipo de geometria a ser utilizado no GeoJSON deve refletir a natureza espacial do serviço ou obra. A escolha correta da geometria é fundamental para a interpretação e visualização dos dados.

**Tabela de Orientação:**

| Categoria do Item | Tipo de Geometria Esperado | Exemplo de Serviço |
|:------------------|:---------------------------|:-------------------|
| Obras e serviços pontuais | `Point` | Instalação de radar, etc. |
| Serviços lineares | `LineString` | Recuperação de pavimento, implantação de faixa adicional, etc. |
| Intervenções em área | `Polygon` | Construção de pátio, área de SAU, etc. |
| Itens múltiplos e discretos | `MultiPoint` | Instalação de série de sensores, tachões, etc. |

**Diretrizes:**

1. **Point (Ponto):** Use para obras ou serviços que ocorrem em um local específico e pontual:
   - Instalação de equipamento (radar, câmera, sensor)
   - Construção de passarela
   - Instalação de dispositivo de segurança

2. **LineString (Linha):** Use para serviços que ocorrem ao longo de um trecho linear da rodovia:
   - Recuperação de pavimento
   - Implantação de faixa adicional
   - Pintura de faixa de rolamento
   - Instalação de guard-rail ou defensas metálicas

3. **Polygon (Polígono):** Use para intervenções que ocupam uma área delimitada:
   - Construção de pátio de estacionamento
   - Área de SAU (Serviço de Atendimento ao Usuário)
   - Área de intervenção para obras complexas
   - Praça de pedágio

4. **MultiPoint (Múltiplos Pontos):** Use para instalações de vários itens similares e discretos ao longo de um trecho:
   - Série de sensores distribuídos
   - Conjunto de tachões
   - Placas de sinalização vertical ao longo de um segmento

**⚠️ Importante:**
- Evite usar `Point` para serviços lineares ou de área
- Não use `Polygon` quando uma linha seria mais apropriada
- Para itens muito discretos e separados, prefira `MultiPoint` em vez de múltiplas features com `Point`

---

### **11.3 Qualidade dos Dados**

1. **Validação em múltiplas etapas:** Valide os dados antes e depois da conversão, e também visualmente em um software GIS.

2. **Precisão das coordenadas:** Use até 6 casas decimais (~11cm de precisão) e verifique se as coordenadas estão na área do Estado de SP.

3. **Consistência dos atributos:** Garanta que `km_final` seja maior ou igual a `km_inicial` e que `data_final` seja posterior a `data_inicial`.

---

### **11.4 Nomenclatura e Padronização**

1. **Nomes de arquivos:** Use exatamente o padrão especificado: `L<lote>_<tipo>_2026_R0.geojson`

2. **Campos de texto:** Seja claro e objetivo nas descrições, evitando abreviações excessivas.

3. **Valores nulos:** Use o tipo `null` do JSON em vez de strings vazias (`""`).

---

### **11.5 Performance e Tamanho**

1. **Arquivos grandes:** Remova casas decimais desnecessárias e evite indentação em arquivos de produção para reduzir o tamanho.

2. **Geometrias complexas:** Simplifique geometrias quando a alta precisão não for necessária, evitando milhares de vértices em uma única feature.

---

### **11.6 Segurança e Privacidade**

1. **Dados sensíveis:** Não inclua informações confidenciais ou de caráter pessoal em campos de texto livre como `observacoes_gerais`.

2. **Backup:** Sempre mantenha backup dos arquivos antes do envio.

---