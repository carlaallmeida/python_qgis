# Registro de Imagens MUX L2 com base no Sentinel-2

Este projeto apresenta um fluxo de trabalho automatizado para o co-registro geométrico de imagens do sensor **MUX** (nível de processamento **L2**), nos satélites **CBERS-4** e **CBERS-4A**, utilizando como referência espacial os produtos **Sentinel-2 (L1C)** obtidos através do *Copernicus Data Space Ecosystem*.

---

## 📌 Contextualização do Problema

O catálogo de imagens do **INPE (Instituto Nacional de Pesquisas Espaciais)** fornece dados atualizados diariamente. No entanto, as imagens MUX identificadas com o nível de processamento **L2** não passaram por todas as etapas de refinamento geométrico (ortorretificação completa), apresentando deslocamentos espaciais em relação ao terreno.

Para corrigir essa distorção, aplicamos uma técnica de **co-registro automatizado**, tomando como base uma imagem de referência de resolução espacial semelhante e órbita temporal próxima.

---

## 🎯 Objetivos

1. **Busca Automatizada no Catálogo INPE:** Identificar e baixar a cena do sensor MUX L2 de interesse.
2. **Filtro no Copernicus Dataspace:** Localizar a cena correspondente do produto `SENTINEL_2_L1C` utilizando o composto `LEAST_CC` (composto com menor cobertura de nuvens disponível para a época).
3. **Compatibilização Espectral:** Selecionar e isolar no Sentinel-2 as bandas equivalentes às do sensor MUX.
4. **Co-registro Geométrico:** Estimar os deslocamentos (mismatch) e aplicar a correção espacial utilizando o pacote Python `AROSICS` (*An Automated Robust Image Co-Registration Software*).

---

## 🛠️ Tecnologias e Bibliotecas Utilizadas

O pipeline de processamento foi desenvolvido em **Python**, integrando ferramentas de Geoinformática e Processamento Digital de Imagens (PDI):

* **AROSICS:** Detecção e correção de deslocamentos sub-pixel entre as imagens de satélite.
* **Rasterio / GDAL:** Manipulação de arquivos matriciais (GeoTIFF) e alinhamento de metadados.
* **STAC API / OGC:** Conexão e consulta aos catálogos de dados do INPE e do Copernicus Dataspace.
* **Geopandas:** Manipulação de geometrias para delimitação das áreas de interesse (AOI).

---

## ⚙️ Fluxo de Processamento

O algoritmo executa as seguintes etapas estruturadas:

1. **Configuração da Área:** Definição das coordenadas da cena MUX L2 alvo.
2. **Download da Referência:** Consulta ao Dataspace Copernicus para extrair o mosaico `LEAST_CC` do Sentinel-2 na mesma janela temporal.
3. **Preparação das Bandas:** Reprojeção e reamostragem espacial (caso necessário) para garantir que o dado de referência e o dado a ser corrigido possuam resoluções compatíveis.
4. **Execução do `arosics`:**
    * Cálculo dos pontos de controle (GCPs) entre a imagem MUX (alvo) e a Sentinel-2 (referência).
    * Cálculo do deslocamento em X e Y através de correlação cruzada no domínio da frequência.
    * Aplicação do mapeamento reverso para gerar a nova imagem MUX geometricamente corrigida.

---

## 🚀 Como Executar

1. Certifique-se de ter as credenciais do Dataspace Copernicus configuradas no seu ambiente.
2. Instale as dependências necessárias:
   ```bash
   pip install arosics rasterio geopandas requests
