# Emotion Detection

Projeto experimental de visão computacional para detecção de emoções faciais em tempo real via webcam, usando OpenCV para captura/detecção de faces e DeepFace para inferência de emoções. O repositório também inclui scripts auxiliares para persistir as probabilidades por frame ou por tempo e gerar gráficos com a evolução emocional observada.

## Visão Geral

O projeto foi estruturado como um conjunto de scripts Python independentes, voltados para prototipação rápida e análise local. O fluxo principal é:

1. Capturar vídeo da webcam.
2. Detectar rostos na imagem com Haar Cascade.
3. Inferir emoções com `DeepFace.analyze(...)`.
4. Exibir a emoção dominante em tempo real.
5. Opcionalmente salvar as probabilidades de cada emoção em CSV.
6. Gerar gráficos a partir dos dados coletados.

As emoções tratadas pelos scripts são:

- `angry`
- `disgust`
- `fear`
- `happy`
- `sad`
- `surprise`
- `neutral`

## Estrutura do Repositório

```text
emotion-detection/
├── deep_haar/
│   ├── deepfaceHaarcascade.py
│   └── haarcascade_frontalface_default.xml
├── deepfaceGraph/
│   ├── deepGraph.py
│   ├── emotions_data.csv
│   └── gerarGrafico.py
├── deepfaceGraphSeconds/
│   ├── detect2Seconds.py
│   ├── emotions_data_seconds.csv
│   ├── geraGraficoSeconds.py
│   └── deepfaceGraphSeconds.png
└── README.md
```

## Componentes

### `deep_haar/deepfaceHaarcascade.py`

Script de demonstração em tempo real. Captura frames da webcam, detecta rostos com Haar Cascade e desenha na imagem a emoção dominante inferida pelo DeepFace.

Uso principal:

- prova de conceito de reconhecimento emocional ao vivo
- validação visual rápida da webcam e do modelo

Saída:

- janela OpenCV com bounding box e rótulo da emoção dominante

### `deepfaceGraph/deepGraph.py`

Estende o fluxo de captura para registrar, a cada frame, a distribuição de probabilidades de emoção retornada pelo DeepFace. Ao final da execução, grava um CSV com histórico por frame.

Saída:

- arquivo `deepfaceGraph/emotions_data.csv`
- tempo total de gravação impresso no terminal

### `deepfaceGraph/gerarGrafico.py`

Lê o CSV por frame e plota curvas para cada emoção ao longo da sequência capturada.

Saída:

- gráfico exibido em janela via Matplotlib

### `deepfaceGraphSeconds/detect2Seconds.py`

Variação do fluxo de captura que salva as emoções com base no tempo decorrido, em segundos, em vez de índice de frame.

Saída:

- arquivo `deepfaceGraphSeconds/emotions_data_seconds.csv`
- tempo total de gravação impresso no terminal

### `deepfaceGraphSeconds/geraGraficoSeconds.py`

Lê o CSV temporal e gera um gráfico das emoções ao longo do tempo, salvando o resultado em arquivo.

Saída:

- imagem em `deepfaceGraphSeconds/deepfaceGraphSeconds.png` ou caminho equivalente configurado no script

## Stack Técnica

- Python
- OpenCV (`cv2`)
- DeepFace
- Pandas
- Matplotlib
- Haar Cascade para detecção facial clássica

Dependências transitivas relevantes:

- TensorFlow/Keras, normalmente exigidos pelo DeepFace
- pesos do modelo de expressão facial baixados/cacheados pelo DeepFace no primeiro uso

## Como Executar

### Pré-requisitos

- Python 3.10+ recomendado
- Webcam local disponível
- Ambiente gráfico para janelas OpenCV/Matplotlib
- Dependências instaladas no ambiente Python

Exemplo de instalação:

```bash
python -m venv .venv
source .venv/bin/activate
pip install opencv-python deepface pandas matplotlib
```

Em alguns ambientes, o DeepFace exigirá instalação explícita do backend de machine learning e fará download de pesos no primeiro uso.

### Execução dos Scripts

Detecção em tempo real:

```bash
python deep_haar/deepfaceHaarcascade.py
```

Captura com exportação por frame:

```bash
python deepfaceGraph/deepGraph.py
python deepfaceGraph/gerarGrafico.py
```

Captura com exportação por tempo:

```bash
python deepfaceGraphSeconds/detect2Seconds.py
python deepfaceGraphSeconds/geraGraficoSeconds.py
```

Interação:

- pressione `q` para encerrar a captura

## Dados Gerados

### CSV por frame

Arquivo: `deepfaceGraph/emotions_data.csv`

Formato observado:

```csv
Frame,angry,disgust,fear,happy,sad,surprise,neutral
1,5.32,0.00,11.72,0.32,24.93,0.02,57.67
```

### CSV por tempo

Arquivo: `deepfaceGraphSeconds/emotions_data_seconds.csv`

Formato observado:

```csv
time,angry,disgust,fear,happy,sad,surprise,neutral
6.07,30.38,0.00,11.85,7.88,17.86,0.07,31.94
```

Esses valores representam as probabilidades ou scores retornados pelo DeepFace para cada emoção em cada amostra coletada.

## Arquitetura e Decisões de Implementação

- A detecção facial usa `haarcascade_frontalface_default.xml`, uma abordagem leve e simples para protótipos.
- A classificação emocional é delegada ao DeepFace, reduzindo o esforço de treinamento/curadoria de modelo.
- Os scripts foram separados por objetivo operacional: visualização em tempo real, exportação por frame e exportação por tempo.
- A persistência é feita em CSV, o que simplifica inspeção manual, análise estatística e integração com ferramentas externas.

## Limitações Atuais

O estado atual do código é funcional para experimento local, mas ainda não está empacotado como aplicação reutilizável. Os principais pontos são:

- caminhos absolutos de Windows estão hardcoded nos scripts
- não há `requirements.txt`, `pyproject.toml` ou processo formal de instalação
- não há testes automatizados
- o tratamento de erros é mínimo
- a captura assume webcam no índice `0`
- a análise roda frame a frame, o que pode limitar desempenho em máquinas menos potentes
- os scripts assumem operação interativa local, sem modo batch ou API
- `geraGraficoSeconds.py` plota apenas até `surprise`, deixando `neutral` fora do laço atual

## Uso Recomendado

Este projeto é adequado para:

- demonstrações acadêmicas
- experimentos de visão computacional
- validação rápida de pipelines com DeepFace
- geração de séries temporais simples de emoções faciais

Não deve ser tratado, no estado atual, como solução pronta para produção sem refatoração de configuração, portabilidade, observabilidade e testes.

## Próximos Passos Recomendados

- substituir caminhos absolutos por `pathlib` e caminhos relativos ao repositório
- adicionar `requirements.txt` ou `pyproject.toml`
- centralizar parâmetros de execução, como câmera, arquivos de saída e modelo
- incluir tratamento de erro para indisponibilidade de câmera ou falhas do DeepFace
- consolidar os scripts em uma CLI única
- adicionar testes básicos para geração de CSV e gráficos

## Resumo Executivo

O repositório implementa um pipeline local de detecção de emoções faciais em tempo real com capacidade de exportação analítica. É um projeto de prototipação técnica bem direcionado para experimentação e demonstração, com base sólida em bibliotecas conhecidas, mas ainda dependente de ajustes de portabilidade e empacotamento para uso mais amplo.
