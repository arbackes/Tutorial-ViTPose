# Estimativa de Pose Humana em Vídeos com ViTPose

Este projeto apresenta um exemplo prático de utilização de técnicas de visão computacional e aprendizado profundo para detectar pessoas e estimar seus pontos anatômicos (*keypoints*) em vídeos. A implementação utiliza os modelos **RT-DETR** e **ViTPose**, permitindo identificar e acompanhar a posição de diferentes partes do corpo ao longo do tempo.

O código foi desenvolvido em Python e pode ser executado diretamente no **Google Colab**, sem a necessidade de configurar um ambiente local.

## 1. Objetivos

O objetivo é demonstrar como extrair informações sobre a postura e os movimentos de uma pessoa a partir de vídeos, utilizando modelos pré-treinados de inteligência artificial.

A implementação permite:

- Detectar pessoas nos quadros de um vídeo.
- Estimar 17 pontos anatômicos do corpo humano.
- Acompanhar uma pessoa ao longo da sequência de quadros.
- Registrar as coordenadas e os níveis de confiança dos pontos estimados.
- Visualizar o esqueleto sobre os quadros do vídeo.
- Gerar vídeos com a representação dos pontos anatômicos.
- Analisar trajetórias e velocidades de movimento.
- Construir mapas de calor para representar a presença e a movimentação dos pontos corporais.

## 2. Modelos utilizados

O projeto combina dois modelos de aprendizado profundo:

| Modelo | Função |
|---|---|
| [RT-DETR](https://huggingface.co/PekingU/rtdetr_r50vd_coco_o365) | Detectar pessoas nos quadros do vídeo e delimitar suas regiões por caixas delimitadoras (*bounding boxes*). |
| [ViTPose](https://huggingface.co/usyd-community/vitpose-base-simple) | Estimar os pontos anatômicos da pessoa detectada. |

O RT-DETR é responsável pela detecção da pessoa, enquanto o ViTPose utiliza a região identificada para estimar as coordenadas dos pontos corporais.

## 3. Pontos anatômicos

São utilizados 17 pontos anatômicos seguindo a convenção do conjunto de dados COCO:

1. Nariz
2. Olho esquerdo
3. Olho direito
4. Orelha esquerda
5. Orelha direita
6. Ombro esquerdo
7. Ombro direito
8. Cotovelo esquerdo
9. Cotovelo direito
10. Pulso esquerdo
11. Pulso direito
12. Quadril esquerdo
13. Quadril direito
14. Joelho esquerdo
15. Joelho direito
16. Tornozelo esquerdo
17. Tornozelo direito

Cada ponto é representado por três valores: coordenada horizontal (x), coordenada vertical (y) e nível de confiança da estimativa (*score*).

## 4. Etapas do processamento

O notebook está organizado nas seguintes etapas:

### 4.1. Configuração do ambiente

Instalação das bibliotecas necessárias e importação dos módulos utilizados no processamento de imagens, vídeos, dados e modelos de inteligência artificial.

O dispositivo de execução é selecionado automaticamente: GPU, quando disponível, ou CPU.

### 4.2. Carregamento do vídeo

O vídeo de entrada é carregado e suas características são identificadas, incluindo:

- Largura e altura dos quadros.
- Taxa de quadros por segundo (FPS).
- Número total de quadros.
- Duração do vídeo.

Também é possível limitar o tempo de processamento e definir o intervalo entre os quadros analisados.

### 4.3. Carregamento dos modelos

São carregados os modelos pré-treinados RT-DETR e ViTPose, disponibilizados no Hugging Face.

### 4.4. Detecção e estimativa de pose

Para cada quadro selecionado, o RT-DETR identifica as pessoas presentes na imagem. As regiões detectadas são encaminhadas ao ViTPose, que estima as coordenadas dos 17 pontos anatômicos.

Quando existem múltiplas pessoas, o código seleciona uma delas com base na confiança das estimativas e, nos quadros seguintes, procura manter o acompanhamento utilizando a proximidade entre os centros dos pontos corporais.

### 4.5. Organização dos resultados

As coordenadas estimadas são organizadas em uma estrutura de dados contendo:

- Número do quadro.
- Instante de tempo.
- Identificação do ponto anatômico.
- Coordenadas x e y.
- Nível de confiança.

Os resultados são exportados para um arquivo CSV chamado `pontos.csv`.

### 4.6. Visualização do esqueleto

O esqueleto é desenhado sobre os quadros do vídeo, conectando os pontos anatômicos de acordo com a estrutura corporal definida.

O notebook também apresenta exemplos de quadros processados e gera um novo vídeo com as estimativas de pose sobrepostas às imagens originais. O resultado é salvo como `pose.mp4`.

### 4.7. Análise das trajetórias

São representadas as trajetórias temporais de pontos selecionados, como pulsos e tornozelos.

Para reduzir ruídos nas estimativas, o código utiliza interpolação de valores ausentes e o filtro Savitzky-Golay para suavização das trajetórias.

### 4.8. Mapas de calor

São gerados dois tipos de mapas de calor:

- **Presença:** destaca as regiões da imagem nas quais os pontos anatômicos aparecem com maior frequência.
- **Movimentação:** destaca as regiões associadas ao deslocamento acumulado dos pontos anatômicos entre quadros consecutivos.

### 4.9. Análise da velocidade

A velocidade dos pontos anatômicos é estimada a partir do deslocamento entre quadros consecutivos.

Para permitir uma análise menos dependente da escala da imagem, a velocidade é normalizada pelo comprimento estimado do tronco, sendo expressa em troncos por segundo.

## 5. Como executar

A maneira mais simples de executar o projeto é utilizando o Google Colab.

1. Abra o notebook disponibilizado neste repositório.
2. Clique em **Open in Colab** (caso o link esteja disponível).
3. Selecione um ambiente de execução com GPU, se possível.
4. Execute as células na sequência apresentada.
5. Faça o upload de um vídeo quando solicitado.
6. Aguarde a conclusão do processamento e visualize os resultados.

**Observação:** o tempo de execução depende da duração e da resolução do vídeo, da quantidade de quadros processados e do dispositivo utilizado.

## 6. Arquivos gerados

Durante a execução, são produzidos os seguintes arquivos:

| Arquivo | Descrição |
|---|---|
| `pontos.csv` | Coordenadas e níveis de confiança dos pontos anatômicos em cada quadro processado. |
| `pose.mp4` | Vídeo original com a representação gráfica do esqueleto estimado. |
| `pose_tmp.mp4` | Vídeo intermediário utilizado na conversão para o formato H.264. |

Além dos arquivos, o notebook apresenta gráficos de trajetórias, velocidades e mapas de calor.

## 7. Tecnologias e bibliotecas

O projeto utiliza as seguintes tecnologias:

- **Python:** linguagem de programação.
- **PyTorch:** execução dos modelos de aprendizado profundo.
- **Hugging Face Transformers:** carregamento dos modelos RT-DETR e ViTPose.
- **OpenCV:** leitura, processamento e gravação de vídeos.
- **NumPy:** operações numéricas e manipulação de matrizes.
- **Pandas:** organização e exportação dos dados.
- **Matplotlib:** geração de gráficos e visualizações.
- **SciPy:** suavização de trajetórias com o filtro Savitzky-Golay.

## 8. Considerações

A estimativa de pose é realizada automaticamente e está sujeita a erros decorrentes de oclusões, iluminação, resolução do vídeo, posicionamento da câmera e características dos movimentos analisados.

Os valores de confiança fornecidos pelo modelo devem ser considerados na interpretação dos resultados. Além disso, as velocidades normalizadas em troncos por segundo representam medidas relativas à escala da imagem, não velocidades físicas expressas em metros por segundo.

O projeto tem finalidade didática e experimental, servindo como ponto de partida para estudos de análise de movimento humano utilizando visão computacional.

## 9. Referências

- **RT-DETR:** [Real-Time Detection Transformer](https://huggingface.co/PekingU/rtdetr_r50vd_coco_o365).
- **ViTPose:** [ViTPose Base Simple](https://huggingface.co/usyd-community/vitpose-base-simple).
- **Hugging Face Transformers:** [Documentação oficial](https://huggingface.co/docs/transformers/index).
- **OpenCV:** [Documentação oficial](https://docs.opencv.org/).

---

**Projeto desenvolvido para fins didáticos**, com o objetivo de demonstrar a aplicação de modelos de inteligência artificial na detecção de pessoas, estimativa de pose e análise de movimentos em vídeos.
