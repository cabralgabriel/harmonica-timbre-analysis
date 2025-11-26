# Harmonica Timbre Analysis

<p align="left">
  Códigos e banco de dados utilizados no trabalho:<br>
  <strong>Análise acústica da harmônica diatônica: <em>a sonoridade blues através de descritores de timbre</em></strong>
</p>


## dados

- **audios**: Gravações de áudios das notas isoladas.

- **timbre_descritores.csv**: Dataset com valores de centroide e tempo de ataque para cada repetição (gerado após execução do *processamento.ipynb*).

## notebooks

### Notebooks Python

- **processamento.ipynb**: Realiza carregamento dos áudios, calcula STFT, descritores de timbre, espectrogramas e monta dataset.

- **visualizacao_dados.ipynb**: Gera gráficos boxplots nota a nota e calcula medianas.

#### Para usar (Linux):
```bash
python -m venv .venv
source .venv/bin/activate  
pip install -r requirements.txt
```
(Essentia não está disponível via 'pip' para Windows.)

### Notebook R

- **permanova.ipynb**: Executa o teste da PERMANOVA, PERMDISP e testes post-hoc.


#### Para usar:
```r
install.packages("remotes")
remotes::install_github("pmartinezarbizu/pairwiseAdonis/pairwiseAdonis")

install.packages("vegan")
install.packages("tidyverse")
install.packages("emmeans")
```