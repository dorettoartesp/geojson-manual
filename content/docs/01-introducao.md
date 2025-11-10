---
title: "1. Introdução"
weight: 10
bookToc: true
---

# **1. Introdução**

> **Contexto:** Este manual é o guia técnico para a metodologia de entrega de programação de obras e serviços de conservação. O processo foi dividido em duas fases para permitir a adaptação tecnológica das concessionárias:
>
> 1. **Fase 1 (Entrega em Excel):** Coleta inicial dos dados de **atributos** (não geográficos) através de planilhas Excel padronizadas.
>
> 2. **Fase 2 (Entrega em GeoJSON):** Entrega final em formato GeoJSON. As concessionárias deverão consolidar os dados de atributos da Fase 1 com os dados georreferenciados correspondentes.
>
> Este documento detalha os requisitos técnicos para a **Fase 2**, ou seja, a estrutura e o conteúdo do arquivo GeoJSON final.

---

## **1.1 Escopo e Finalidade**

Este manual técnico estabelece os procedimentos, critérios e diretrizes para a geração, conversão, validação e entrega de arquivos em formato GeoJSON à ARTESP, conforme especificado no **Memorando Circular nro. 0087484992** (processo SEI nro. 134.00039278/2025-72) e **Memorando Circular nro. 0088571607** (processo SEI nro. 134.00040851/2025-91)

**Este documento abrange:**

- Orientações sobre o formato GeoJSON e suas características
- **O modelo de dados de vinculação** entre a planilha de planejamento (Excel) e o GeoJSON final
- Procedimentos detalhados de conversão de dados tabulares (CSV, Excel) para GeoJSON, incluindo o agrupamento e agregação necessários
- Validação e conformidade com os schemas JSON fornecidos

> **Observação:** As diretrizes apresentadas neste manual procuram contemplar os fluxos operacionais mais recorrentes observados nas concessionárias, mas devem ser entendidas como orientações gerais de referência. Cada concessionária permanece responsável por adotar o método que melhor se aproxime da sua realidade institucional, levando em consideração as rotinas internas, os sistemas legados em uso e os fluxos de trabalho já estabelecidos.

---

## **1.2 Público-Alvo**

Este manual destina-se a:

- Analistas e técnicos das concessionárias de rodovias
- Desenvolvedores responsáveis pela integração de sistemas
- Gestores que supervisionam o processo de entrega de dados à ARTESP

---

## **1.3 Pré-requisitos**

**Conhecimentos recomendados:**

- Conceitos de sistemas de informação geográfica (SIG)
- Manipulação de arquivos CSV e planilhas Excel
- Python 3.8+ (para o script de conversão recomendado)

**Softwares recomendados:**

- **Editor de texto:** VS Code, Notepad++, Sublime Text
- **Software GIS:** QGIS 3.x (gratuito e open source) **ou** ArcGIS Pro
- **Python:** 3.8+ com as bibliotecas `pandas` e `openpyxl`
