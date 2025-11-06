---
title: "10. Erros Comuns e Soluções"
weight: 100
bookToc: true
---

## **10. Erros Comuns e Soluções**

### **10.1 Erro: "Coordenadas inválidas"**

**❌ Problema:** Coordenadas fora do range esperado ou em formato incorreto.

**Sintomas:**

```json
"coordinates": [-23.5489, -47.0653]  // ERRADO: ordem invertida
```

**✅ Solução:**

```json
"coordinates": [-47.0653, -23.5489]  // CERTO: [longitude, latitude]
```

---

### **10.2 Erro: "Campo 'local' inválido"**

**❌ Problema:** O campo `local` foi preenchido como string em vez de array.

**Sintomas:**

```json
"local": "PISTA_NORTE"  // ERRADO
```

**✅ Solução:**

```json
"local": ["PISTA_NORTE"]  // CERTO
```

---

### **10.3 Erro: "Código de rodovia inválido"**

**❌ Problema:** Código com hífen, formato incorreto ou que não corresponde aos padrões aceitos.

**Sintomas:**

```json
"rodovia": "SP-280/457"     // ERRADO: contém hífen e barra
"rodovia": "SP 280 457"     // ERRADO: contém espaços
"rodovia": "SP280"          // ERRADO: poucos dígitos
```

**✅ Solução:**

```json
"rodovia": "SP0000280"      // CERTO
"rodovia": "SPM00258E"      // CERTO
"rodovia": "01SPD001067"    // CERTO
"rodovia": "SPA004257"      // CERTO
```

---

### **10.4 Erro: "Data em formato incorreto"**

**❌ Problema:** Data sem fuso horário ou em formato não ISO 8601.

**Sintomas:**

```json
"data_inicial": "2025-11-15"  // ERRADO: sem hora e fuso
"data_inicial": "15/11/2025 08:00:00"  // ERRADO: formato brasileiro
```

**✅ Solução:**

```json
"data_inicial": "2025-11-15T08:00:00-03:00"  // CERTO
```

---

### **10.5 Erro: "Geometria inválida"**

**❌ Problema:** Polígono não fechado ou com auto-intersecção.

**Sintomas:**

```json
"coordinates": [[ [-47.2, -23.6], [-47.19, -23.6], [-47.19, -23.59], [-47.20, -23.59] ]]
// ERRADO: falta fechar o polígono
```

**✅ Solução:**

```json
"coordinates": [[ [-47.2, -23.6], [-47.19, -23.6], [-47.19, -23.59], [-47.20, -23.59], [-47.20, -23.6] ]]
// CERTO: primeiro e último pontos iguais
```

---

### **10.6 Erro: "CRS incorreto"**

**❌ Problema:** CRS em WGS84 (`EPSG:4326`) em vez de SIRGAS 2000 (`EPSG:4674`).

**Sintomas:**

```json
"crs": { "properties": { "name": "EPSG:4326" } }  // ERRADO
```

**✅ Solução:**

```json
"crs": { "properties": { "name": "EPSG:4674" } }  // CERTO
```

---

### **10.7 Erro: "Número decimal com vírgula"**

**❌ Problema:** Uso de vírgula como separador decimal em campos numéricos.

**Sintomas:**

```json
"quantidade": 15,500  // ERRADO: JSON não aceita vírgula
"km_inicial": "125,000"  // ERRADO: string em vez de número
```

**✅ Solução:**

```json
"quantidade": 15.500  // CERTO: ponto como separador
"km_inicial": 125.000  // CERTO: tipo number com ponto
```

---

### **10.8 Erro: "Campo obrigatório ausente" (ex: `id`)**

**❌ Problema:** Falta o campo `id` nas propriedades da feature.

**Sintomas:**

```json
"properties": { "lote": "L13", ... }  // ERRADO: falta "id"
```

**✅ Solução:**

```json
"properties": { "id": 1, "lote": "L13", ... }  // CERTO
```

---

### **10.9 Erro: "Inconsistência Planilha vs. GeoJSON"**

**❌ Problema:** O GeoJSON final contém features duplicadas para o mesmo serviço, cada uma com um único local (ex: uma feature para `PISTA_NORTE` e outra para `PISTA_SUL`).

**Causa:** O script de conversão não realizou o agrupamento por `id` e a agregação do campo `local`.

**✅ Solução:** Use o script da **Seção 6.1** deste manual para garantir que os dados da planilha sejam corretamente agrupados em uma única feature por `id`, com o campo `local` como um array.

---