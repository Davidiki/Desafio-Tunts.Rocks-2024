# Desafio-Tunts.Rocks-2024
Desafio de programação para o processo seletivo;

## Descrição do projeto
- Este projeto é um script em Python que usa as bibliotecas gspread, oauth2client e gspread_dataframe para acessar, ler e manipular dados de uma planilha do Google Sheets. O objetivo é calcular a média, a situação e a nota para aprovação final dos alunos de uma disciplina de engenharia de software, usando as notas das provas e as faltas como critérios.

## Requisitos
Para executar este projeto, você precisa ter instalado o Python 3 e as seguintes bibliotecas:
- **gspread:** uma biblioteca que permite interagir com as planilhas do Google Sheets usando a API do Google Sheets.
- **oauth2client:** uma biblioteca que fornece ferramentas para autenticar e autorizar o acesso às planilhas do Google Sheets usando o OAuth 2.0.
- **gspread_dataframe:** uma biblioteca que permite converter os dados das planilhas do Google Sheets em dataframes do pandas e vice-versa.

```sh
!pip install gspread
!pip install oauth2client
!pip install gspread_dataframe
```

## Como usar
Para usar este projeto, siga os seguintes passos:

1. Faça uma cópia da planilha do Google Sheets que contém os dados dos alunos [aqui](https://docs.google.com/spreadsheets/d/11-IQ3x8gJIg-M6SUprNuQtfdxTLbNO0S5IYfFdtt3I4/edit#gid=195192560).
2. Abra o Google Colab e crie um novo notebook em Python 3, ou clique [aqui](https://colab.research.google.com/drive/1CuJISC0XalV5TQVBsWyCAXwKZeQGEM1f?usp=sharing).
3. Copie e cole o código do projeto no notebook.
4. Execute as células que importam as bibliotecas e autenticam o usuário.
5. Substitua o URL da planilha pelo URL da sua cópia da planilha se necessário.
6. Execute as demais células que leem, processam e escrevem os dados na planilha.
7. Verifique os resultados na sua cópia da planilha.

## Explicação do código
O código do projeto é composto por várias partes, que serão explicadas a seguir:

### Importar as bibliotecas necessárias:
```python
import gspread
import pandas as pd
from google.colab import auth
from google.auth import default
from gspread_dataframe import set_with_dataframe
```
Essa parte do código importa as bibliotecas gspread, pandas, auth, default e set_with_dataframe, que serão usadas para acessar, ler e manipular os dados da planilha do Google Sheets.

### Autenticar o usuário:
```python
auth.authenticate_user()
creds, _ = default()
```

Essa parte do código usa as funções auth.authenticate_user() e default() para autenticar e autorizar o usuário a acessar as planilhas do Google Sheets. Isso irá gerar um link para que o usuário faça login com sua conta do Google e obtenha um código de verificação, que deve ser inserido na célula do notebook. Em seguida, o código armazena as credenciais do usuário na variável creds.

### Abrir a planilha pelo URL:
```python
gsheets = gspread.authorize(creds)
planilha = gsheets.open_by_url('https://docs.google.com/spreadsheets/d/11-IQ3x8gJIg-M6SUprNuQtfdxTLbNO0S5IYfFdtt3I4/edit?usp=sharing')
```
Essa parte do código usa a função gspread.authorize(creds) para criar um objeto gsheets que permite interagir com as planilhas do Google Sheets usando a API do Google Sheets. Em seguida, o código usa a função gsheets.open_by_url(url) para abrir a planilha que contém os dados dos alunos, usando o URL da planilha. O código armazena a planilha na variável planilha.

### Ler os dados da planilha e colocá-los em um dataframe:
```python
data = planilha.worksheet('engenharia_de_software').get_all_values()
df = pd.DataFrame(data[3:], columns=data[2:])
```
Essa parte do código define o número total de aulas da disciplina de engenharia de software na variável total_aulas. Esse valor será usado para calcular a porcentagem de faltas dos alunos.

### Definir uma função para calcular a média das provas:
```python
def calculate_mean(p1, p2, p3):
  try:
    val1 = float(p1)
    val2 = float(p2)
    val3 = float(p3)
    mean = (val1 + val2 + val3) / 3
    return mean
  except Exception as e:
    return 0.00
```
Essa parte do código define uma função chamada calcular_media que recebe três parâmetros: p1, p2 e p3, que representam as notas das provas dos alunos. A função tenta converter as notas para números usando a função float(), e depois calcula a média aritmética das notas usando a fórmula (val1 + val2 + val3) / 3. A função retorna a média como um número. Se ocorrer algum erro na conversão ou no cálculo, a função retorna 0.00 como a média.

### Definir uma função para calcular a situação do aluno:
```python
def calcular_situacao(mean, faltas):
  total_aulas = 60
  try:
    valfaltas = float(faltas)
    porcentagem_faltas = (valfaltas / total_aulas) * 100
    if porcentagem_faltas > 25:
      situacao = "Reprovado por Falta"
    else:
      if mean < 50:
        situacao = "Reprovado por Nota"
      elif mean >= 50 and mean < 70:
        situacao = "Exame Final"
      elif mean >= 70:
        situacao = "Aprovado"
    return situacao
  except Exception as e:
    return "Não Calculado"
```
Essa parte do código define uma função chamada calcular_situacao que recebe dois parâmetros: media e faltas, que representam a média das provas e o número de faltas dos alunos. A função tenta converter as faltas para números usando a função float(), e depois calcula a porcentagem de faltas dos alunos usando a fórmula (valfaltas / total_aulas) * 100. A função verifica se a porcentagem de faltas é maior que 25, o que significa que o aluno está reprovado por falta. Caso contrário, a função verifica a média do aluno e atribui uma situação de acordo com os seguintes critérios:

- Se a média for menor que 50, o aluno está reprovado por nota.
- Se a média for maior ou igual a 50 e menor que 70, o aluno está em exame final.
- Se a média for maior ou igual a 70, o aluno está aprovado.
A função retorna a situação como uma string. Se ocorrer algum erro na conversão ou no cálculo, a função retorna “Não Calculado” como a situação.

### Definir uma função para calcular a nota para aprovação final:
```python
def calcular_naf(mean, situacao):
  try:
    if situacao == "Exame Final":
      return float(70) - float(mean)
    else:
      return 0
  except Exception as e:
    return mean
```
Esta parte do código usa as funções definidas anteriormente para calcular a média, a situação e a nota para aprovação final dos alunos, e adiciona esses valores como novas colunas no dataframe. Em seguida, o código escreve os dados atualizados de volta para a planilha do Google Sheets, na mesma aba que foi lida.

### Aplicar a função para calcular a situação do aluno e criar uma coluna chamada ‘Situação’:
```python
df['Situação'] = df.apply(lambda row: calcular_situacao(calculate_mean(row['P1'], row['P2'], row['P3']), row['Faltas']), axis=1)
```
Essa parte do código usa a função df.apply() para aplicar a função calcular_situacao() a cada linha do dataframe, usando os valores das colunas ‘P1’, ‘P2’, ‘P3’ e ‘Faltas’ como argumentos. O resultado é armazenado em uma nova coluna chamada ‘Situação’, que indica se o aluno está aprovado, reprovado por nota, reprovado por falta ou em exame final.

### Aplicar a função para calcular a nota para aprovação final e criar uma coluna chamada ‘Nota para Aprovação Final’:
```python
df['Nota para Aprovação Final'] = df.apply(lambda row: calcular_naf(calculate_mean(row['P1'], row['P2'], row['P3']), calcular_situacao(calculate_mean(row['P1'], row['P2'], row['P3']), row['Faltas'])), axis=1)
```
Essa parte do código usa a função df.apply() para aplicar a função calcular_naf() a cada linha do dataframe, usando os valores das colunas ‘P1’, ‘P2’, ‘P3’ e ‘Situação’ como argumentos. O resultado é armazenado em uma nova coluna chamada ‘Nota para Aprovação Final’, que indica a nota mínima que o aluno precisa tirar no exame final para ser aprovado, caso ele esteja nessa situação.

### Escrever os dados de volta para a planilha a partir da linha e coluna especificadas:
```python
worksheet = planilha.worksheet('engenharia_de_software')

start_line = 4  # replace with the desired line number
start_column = 1  # replace with the desired column number

for i, row in enumerate(df.itertuples(), start=start_line):
    for j, value in enumerate(row[start_column:], start=start_column):
        worksheet.update_cell(i, j, str(value))
```
Essa parte do código usa a função planilha.worksheet(nome) para selecionar a mesma aba da planilha que foi lida anteriormente, usando o nome da aba. O código armazena a aba na variável worksheet. Em seguida, o código usa um loop for para iterar sobre as linhas do dataframe, começando pela linha especificada na variável start_line. Dentro do loop, o código usa outro loop for para iterar sobre os valores de cada linha, começando pela coluna especificada na variável start_column. O código usa a função worksheet.update_cell(i, j, valor) para atualizar cada célula da planilha com o valor correspondente do dataframe, convertido para string. Dessa forma, o código escreve os dados do dataframe, incluindo as novas colunas, de volta para a planilha do Google Sheets.
