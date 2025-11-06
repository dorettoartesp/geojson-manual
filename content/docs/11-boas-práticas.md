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

### **11.2 Qualidade dos Dados**

1. **Validação em múltiplas etapas:** Valide os dados antes e depois da conversão, e também visualmente em um software GIS.

2. **Precisão das coordenadas:** Use até 6 casas decimais (~11cm de precisão) e verifique se as coordenadas estão na área do Estado de SP.

3. **Consistência dos atributos:** Garanta que `km_final` seja maior ou igual a `km_inicial` e que `data_final` seja posterior a `data_inicial`.

---

### **11.3 Nomenclatura e Padronização**

1. **Nomes de arquivos:** Use exatamente o padrão especificado: `L<lote>_<tipo>_2026_R0.geojson`

2. **Campos de texto:** Seja claro e objetivo nas descrições, evitando abreviações excessivas.

3. **Valores nulos:** Use o tipo `null` do JSON em vez de strings vazias (`""`).

---

### **11.4 Performance e Tamanho**

1. **Arquivos grandes:** Remova casas decimais desnecessárias e evite indentação em arquivos de produção para reduzir o tamanho.

2. **Geometrias complexas:** Simplifique geometrias quando a alta precisão não for necessária, evitando milhares de vértices em uma única feature.

---

### **11.5 Segurança e Privacidade**

1. **Dados sensíveis:** Não inclua informações confidenciais ou de caráter pessoal em campos de texto livre como `observacoes_gerais`.

2. **Backup:** Sempre mantenha backup dos arquivos antes do envio.

---