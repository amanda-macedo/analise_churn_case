```python
import pandas as pd
import numpy as np
import datetime
import seaborn as sns
import matplotlib.pyplot as plt
import copy as cp
from itertools import chain,cycle
from IPython.display import display_html
#Tratamento de warnings
import warnings
warnings.filterwarnings('ignore')
from random import sample
from dateutil.relativedelta import relativedelta
```


```python
# essa função cria uma visualização de vários dataframes em uma única saída
def display_side_by_side(*args,titles=cycle([''])):
    html_str=''
    for df,title in zip(args, chain(titles,cycle(['</br>'])) ):
        html_str+='<th style="text-align:center"><td style="vertical-align:top">'
        html_str+=f'<h3>{title}</h3>'
        html_str+=df.to_html().replace('table','table style="display:inline"')
        html_str+='</td></th>'
    display_html(html_str,raw=True)
```


```python
df = pd.read_csv('data-test-analytics.csv')
```

# Tratamento inicial das variáveis da base de dados


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 10000 entries, 0 to 9999
    Data columns (total 20 columns):
     #   Column              Non-Null Count  Dtype  
    ---  ------              --------------  -----  
     0   id                  10000 non-null  object 
     1   created_at          10000 non-null  object 
     2   updated_at          10000 non-null  object 
     3   deleted_at          505 non-null    object 
     4   name_hash           10000 non-null  object 
     5   email_hash          10000 non-null  object 
     6   address_hash        10000 non-null  object 
     7   birth_date          10000 non-null  object 
     8   status              10000 non-null  object 
     9   version             10000 non-null  object 
     10  city                10000 non-null  object 
     11  state               10000 non-null  object 
     12  neighborhood        10000 non-null  object 
     13  last_date_purchase  10000 non-null  object 
     14  average_ticket      10000 non-null  float64
     15  items_quantity      10000 non-null  int64  
     16  all_revenue         10000 non-null  float64
     17  all_orders          10000 non-null  int64  
     18  recency             10000 non-null  int64  
     19  marketing_source    10000 non-null  object 
    dtypes: float64(2), int64(3), object(15)
    memory usage: 1.5+ MB
    


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>created_at</th>
      <th>updated_at</th>
      <th>deleted_at</th>
      <th>name_hash</th>
      <th>email_hash</th>
      <th>address_hash</th>
      <th>birth_date</th>
      <th>status</th>
      <th>version</th>
      <th>city</th>
      <th>state</th>
      <th>neighborhood</th>
      <th>last_date_purchase</th>
      <th>average_ticket</th>
      <th>items_quantity</th>
      <th>all_revenue</th>
      <th>all_orders</th>
      <th>recency</th>
      <th>marketing_source</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8bf7960e-3b93-468b-856e-6c6c5b56f52b</td>
      <td>08/15/17 07:05 AM</td>
      <td>01/14/21 11:23 AM</td>
      <td>NaN</td>
      <td>312d206168a318614897e8ccac43bff9</td>
      <td>83eb3aed9a44377df80ce876dce92c9a</td>
      <td>8b4bfaa0cbc41a16f46da15ddcd6a907</td>
      <td>07/10/74 12:00 AM</td>
      <td>active</td>
      <td>2.31.7</td>
      <td>Peixoto da Praia</td>
      <td>AM</td>
      <td>Aparecida 7ª Seção</td>
      <td>01/14/21 11:23 AM</td>
      <td>151.142942</td>
      <td>10</td>
      <td>906.857651</td>
      <td>6</td>
      <td>35</td>
      <td>crm</td>
    </tr>
    <tr>
      <th>1</th>
      <td>a39535b5-4647-4680-b4f6-3aed57c1f1ff</td>
      <td>12/31/19 09:53 PM</td>
      <td>01/08/21 11:23 AM</td>
      <td>NaN</td>
      <td>de448fcb47d0d6a873b2eef52b5ee595</td>
      <td>72678bb35e2ac84ed373e81dd9dca28c</td>
      <td>22f1cfa1847f38da3f3cb114dd2b9247</td>
      <td>07/06/40 12:00 AM</td>
      <td>paused</td>
      <td>3.30.12</td>
      <td>Fernandes</td>
      <td>RR</td>
      <td>Santa Isabel</td>
      <td>01/08/21 11:23 AM</td>
      <td>236.991790</td>
      <td>4</td>
      <td>236.991790</td>
      <td>1</td>
      <td>41</td>
      <td>organic_search</td>
    </tr>
    <tr>
      <th>2</th>
      <td>dc067cd2-c021-42bd-8c0e-beb267280e66</td>
      <td>03/07/19 11:46 PM</td>
      <td>01/07/21 11:23 AM</td>
      <td>NaN</td>
      <td>cb09e447ddc38283373d56bb46498e6a</td>
      <td>668f4ee9add29c7bd02c485f1b7509e3</td>
      <td>6cb47446a086ee6483b3eb954f11467a</td>
      <td>03/18/63 12:00 AM</td>
      <td>active</td>
      <td>3.28.9</td>
      <td>Lopes</td>
      <td>RR</td>
      <td>Estrela</td>
      <td>01/07/21 11:23 AM</td>
      <td>211.955597</td>
      <td>13</td>
      <td>2331.511572</td>
      <td>11</td>
      <td>42</td>
      <td>organic_search</td>
    </tr>
    <tr>
      <th>3</th>
      <td>b5e4caeb-3a9b-49ed-aa33-5acd06b162c1</td>
      <td>07/21/18 10:17 AM</td>
      <td>01/10/21 11:23 AM</td>
      <td>NaN</td>
      <td>52593437a405b11b3557170680ef80c8</td>
      <td>d3fb45188d95c8d7cc49da5b4f727c86</td>
      <td>0a6f0c54db1e6f19347f96b50f8092a4</td>
      <td>11/21/80 12:00 AM</td>
      <td>active</td>
      <td>3.34.3</td>
      <td>Campos do Campo</td>
      <td>PE</td>
      <td>Confisco</td>
      <td>01/10/21 11:23 AM</td>
      <td>204.113227</td>
      <td>8</td>
      <td>1224.679359</td>
      <td>6</td>
      <td>39</td>
      <td>organic_search</td>
    </tr>
    <tr>
      <th>4</th>
      <td>d4ff61fc-f008-4e19-b8ae-bd70cfa3ae27</td>
      <td>06/08/18 12:09 PM</td>
      <td>01/18/21 11:23 AM</td>
      <td>NaN</td>
      <td>dbda4b778a966c21904238ed2d2005db</td>
      <td>a0f76bc49b4c43327b536da6e1a1465e</td>
      <td>143b9f169b4fa1692f6d79b5682169b5</td>
      <td>07/07/59 12:00 AM</td>
      <td>active</td>
      <td>3.19.8</td>
      <td>das Neves</td>
      <td>RJ</td>
      <td>Vila Suzana Segunda Seção</td>
      <td>01/18/21 11:23 AM</td>
      <td>252.940997</td>
      <td>9</td>
      <td>2023.527980</td>
      <td>8</td>
      <td>31</td>
      <td>crm</td>
    </tr>
  </tbody>
</table>
</div>




```python
# verificando que os IDs são únicos de fato
df['id'].nunique(),df.shape[0]
```




    (10000, 10000)




```python
df.columns
```




    Index(['id', 'created_at', 'updated_at', 'deleted_at', 'name_hash',
           'email_hash', 'address_hash', 'birth_date', 'status', 'version', 'city',
           'state', 'neighborhood', 'last_date_purchase', 'average_ticket',
           'items_quantity', 'all_revenue', 'all_orders', 'recency',
           'marketing_source'],
          dtype='object')




```python
# passando as colunas de data para formato datetime
columns_data = ['created_at', 'updated_at', 'deleted_at','birth_date','last_date_purchase']
df[columns_data] = df[columns_data].apply(lambda x: pd.to_datetime(x))
```


```python
df[columns_data].apply(lambda x: x.describe())
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>created_at</th>
      <th>updated_at</th>
      <th>deleted_at</th>
      <th>birth_date</th>
      <th>last_date_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>10000</td>
      <td>10000</td>
      <td>505</td>
      <td>10000</td>
      <td>10000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2018-08-12 05:40:22.464000</td>
      <td>2020-12-25 06:06:27.533999616</td>
      <td>2019-12-02 14:16:21.861385984</td>
      <td>2026-09-11 14:43:26.400000</td>
      <td>2020-12-13 06:09:13.463999488</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2016-02-19 10:00:00</td>
      <td>2016-05-02 13:46:00</td>
      <td>2016-05-02 13:46:00</td>
      <td>1973-01-03 00:00:00</td>
      <td>2016-02-25 03:48:00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2017-05-15 11:42:00</td>
      <td>2021-01-10 11:23:00</td>
      <td>2019-04-04 11:10:00</td>
      <td>1986-08-29 06:00:00</td>
      <td>2021-01-10 11:23:00</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2018-08-09 21:42:00</td>
      <td>2021-01-14 11:23:00</td>
      <td>2020-04-16 15:38:00</td>
      <td>2044-11-07 12:00:00</td>
      <td>2021-01-14 11:23:00</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2019-11-04 17:18:30</td>
      <td>2021-01-18 11:23:00</td>
      <td>2020-11-02 21:59:00</td>
      <td>2059-04-30 00:00:00</td>
      <td>2021-01-18 11:23:00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2021-02-18 05:04:00</td>
      <td>2021-02-17 22:19:00</td>
      <td>2021-02-17 22:19:00</td>
      <td>2072-12-31 00:00:00</td>
      <td>2021-02-16 19:46:00</td>
    </tr>
  </tbody>
</table>
</div>



A coluna "birth_date" ficou com algumas datas incorretas na transformação para datetime. Então, iremos corrigir criando uma função para tratamento desses erros.


```python
# definindo a função para tratar os anos
def tratar_datas_incorretas(date):
    ano = date.year
    if ano > 2021:
        ano = ano - 100
        date = date.replace(year=ano)
    return pd.to_datetime(date)
```

*Observação: Para criação de algumas colunas de data como aniversário, tempo de assinatura e etc, vou utilizar como base a última data de created_at que é de '2021-02-19'. Se fosse utilizado o dia de hoje, ficaria muita diferença entre as quantidades de dias/meses reias, já que a base não é muito atualizada.* 


```python
# aplicando a função à coluna de data de aniversário
df['birth_date'] = pd.to_datetime(df['birth_date'].apply(tratar_datas_incorretas))
```


```python
df['birth_date'].max()
```




    Timestamp('1996-02-14 00:00:00')




```python
df['status'].value_counts()
```




    status
    active      8524
    paused       971
    canceled     505
    Name: count, dtype: int64




```python
# verificando se os cancelados estão com a data de exclusão
df[~df['deleted_at'].isnull()]['status'].unique(),df[df['deleted_at'].isnull()]['status'].unique()
# tá tudo certo, o que tem data de exclusão tem status de cancelado e o que não tem data de exclusão tem os outros status
```




    (array(['canceled'], dtype=object), array(['active', 'paused'], dtype=object))




```python
print(list(df['city'].unique()))
```

    [' Peixoto da Praia', ' Fernandes', ' Lopes', ' Campos do Campo', ' das Neves', ' Aragão', ' Pereira Grande', ' Fernandes Alegre', ' Cardoso do Sul', ' Barros das Flores', ' Fernandes de Goiás', ' Moreira do Galho', ' Pinto', ' da Cruz Verde', ' da Mota da Prata', ' Aragão da Serra', ' Moraes das Flores', ' da Rosa', ' Vieira', ' Nunes', ' Araújo da Serra', ' Ferreira Alegre', ' Viana da Prata', ' Peixoto', ' Moreira de Goiás', ' Peixoto de Costela', ' da Luz Alegre', ' da Mota Verde', ' Novaes', ' Ramos', ' Dias do Sul', ' Castro de Ramos', ' Almeida', ' Pereira da Mata', ' Correia do Galho', ' Jesus', ' Caldeira de Mendes', ' Rezende do Galho', ' Barbosa do Oeste', ' Monteiro', ' Ferreira de Correia', ' Silva do Sul', ' Farias', ' Lima', ' da Costa', ' Oliveira', ' Duarte', ' da Mota do Norte', ' da Mota de Carvalho', ' da Cruz de Goiás', ' Cardoso das Flores', ' da Mota', ' da Rocha da Prata', ' Azevedo da Prata', ' Santos', ' Monteiro de Monteiro', ' Novaes de Minas', ' Caldeira do Oeste', ' Carvalho de da Paz', ' Costa Paulista', ' Aragão dos Dourados', ' Pinto de Ramos', ' Melo', ' Castro Alegre', ' da Paz da Serra', ' Pereira de Goiás', ' Moreira', ' Ferreira do Campo', ' da Cunha de Cavalcanti', ' Ferreira', ' Nogueira do Campo', ' Fogaça', ' Nunes de Novaes', ' Duarte do Galho', ' Santos dos Dourados', ' Rodrigues de Minas', ' Carvalho dos Dourados', ' Porto', ' Castro', ' Alves', ' Almeida Alegre', ' Oliveira do Norte', ' Silva da Serra', ' Cardoso', ' Barbosa', ' Mendes do Amparo', ' Araújo do Campo', ' Monteiro do Norte', ' Silva de Santos', ' Almeida da Praia', ' Barros da Mata', ' Almeida de Goiás', ' Teixeira da Prata', ' Peixoto de Rocha', ' Teixeira do Campo', ' Moraes', ' Mendes do Sul', ' Rocha do Amparo', ' Lima da Mata', ' da Mata', ' Almeida do Galho', ' Farias do Campo', ' Freitas do Campo', ' Jesus de Melo', ' Caldeira', ' Martins', ' Nunes de Caldeira', ' Dias de Costela', ' Araújo', ' Pereira de Correia', ' Fernandes do Amparo', ' Peixoto de Ramos', ' Campos Grande', ' Peixoto do Oeste', ' Alves do Amparo', ' Nascimento da Prata', ' Moreira da Serra', ' Ribeiro de Minas', ' Castro Grande', ' da Cunha', ' Gomes', ' Nascimento', ' Cunha', ' Cavalcanti da Prata', ' Barros', ' Moura', ' Pereira da Serra', ' da Rocha', ' Cavalcanti do Amparo', ' Rocha', ' Aragão de Gonçalves', ' Cardoso Paulista', ' Caldeira de Goiás', ' Vieira do Amparo', ' Aragão de Melo', ' Pereira da Praia', ' Dias de Minas', ' Santos Alegre', ' Pereira do Sul', ' Correia de da Rocha', ' Alves do Norte', ' Correia', ' Cardoso Verde', ' Pinto Alegre', ' Carvalho', ' Sales', ' Martins da Prata', ' da Paz de Araújo', ' Peixoto do Sul', ' Moraes Alegre', ' Araújo do Galho', ' Nogueira', ' da Mata do Norte', ' Pires', ' Costa das Pedras', ' da Rosa das Flores', ' Moraes do Sul', ' Martins Grande', ' Mendes da Prata', ' da Cruz', ' Dias da Mata', ' Araújo de Minas', ' Novaes da Praia', ' Campos', ' Caldeira de Carvalho', ' Azevedo de da Luz', ' Alves de Pinto', ' Moraes das Pedras', ' Alves de Minas', ' Rocha da Prata', ' Moreira Paulista', ' Costela', ' Nogueira de Caldeira', ' Pinto do Oeste', ' da Cunha de Teixeira', ' da Conceição do Galho', ' Costa do Galho', ' Moura da Serra', ' Ramos de Dias', ' Costa de Duarte', ' da Paz do Sul', ' Teixeira de Pinto', ' Vieira Verde', ' Lopes de Alves', ' da Mata de Goiás', ' Barros da Praia', ' Barros do Oeste', ' Costa da Prata', ' Freitas de Moura', ' Martins do Galho', ' Gonçalves de Minas', ' Azevedo de Silveira', ' Ribeiro', ' Jesus Paulista', ' da Rocha do Campo', ' Monteiro de Barros', ' Souza do Galho', ' Cardoso de das Neves', ' Mendes', ' Correia do Campo', ' da Costa do Campo', ' da Conceição de Minas', ' das Neves de Almeida', ' Teixeira da Praia', ' Viana', ' Jesus de da Mota', ' Araújo Alegre', ' Alves de Nunes', ' Monteiro de Rezende', ' Souza das Pedras', ' da Rocha do Sul', ' Novaes de Goiás', ' Correia de Gonçalves', ' Peixoto da Mata', ' Lopes Paulista', ' Azevedo do Sul', ' Campos de Lopes', ' Barbosa de Pires', ' Dias', ' Cardoso das Pedras', ' Fogaça Alegre', ' Fernandes do Sul', ' Porto de Azevedo', ' da Conceição', ' Costela de Goiás', ' Rezende', ' Vieira do Sul', ' Vieira de Goiás', ' Dias Grande', ' Martins Alegre', ' da Luz', ' Teixeira', ' Moraes dos Dourados', ' Lopes de Moura', ' Cardoso Grande', ' Novaes de Peixoto', ' Duarte das Flores', ' Almeida do Amparo', ' Azevedo do Amparo', ' Pinto da Mata', ' das Neves de Goiás', ' Ramos das Flores', ' Cunha do Norte', ' Fogaça do Campo', ' Silveira de Cardoso', ' Farias de Campos', ' Nunes da Mata', ' Moreira de Rezende', ' Oliveira de Goiás', ' da Rocha Grande', ' Monteiro Paulista', ' Rocha dos Dourados', ' Nunes do Oeste', ' Nunes de Minas', ' Porto de Minas', ' Lima de Minas', ' Cavalcanti da Serra', ' Moraes Grande', ' Rezende de Araújo', ' Rodrigues', ' Aragão Paulista', ' Vieira Alegre', ' Monteiro de Minas', ' Pires da Mata', ' Nascimento de Azevedo', ' Moreira de Ramos', ' Ribeiro da Serra', ' Araújo das Pedras', ' Cardoso da Praia', ' Nascimento do Norte', ' Gonçalves', ' Pereira', ' Monteiro das Flores', ' Souza Paulista', ' Silva', ' Costa da Serra', ' Correia do Sul', ' Nunes do Campo', ' Rocha de Porto', ' Barros do Sul', ' Moura de Ramos', ' Sales da Praia', ' Araújo de Mendes', ' Cardoso do Campo', ' Azevedo', ' Nunes Grande', ' Castro da Prata', ' Teixeira de Minas', ' Cunha da Mata', ' da Conceição das Pedras', ' Duarte de Gonçalves', ' Azevedo de Gomes', ' da Rosa Verde', ' Araújo de Almeida', ' Pinto de Costa', ' Azevedo de Goiás', ' Almeida do Oeste', ' da Conceição de Ferreira', ' Martins das Pedras', ' Fogaça Paulista', ' da Paz', ' Ribeiro de Carvalho', ' Porto do Oeste', ' Moraes de Goiás', ' Costa dos Dourados', ' Martins das Flores', ' Cavalcanti', ' Moreira de Rocha', ' Nunes Verde', ' Ramos de Oliveira', ' Novaes da Mata', ' da Costa de Novaes', ' da Cruz do Oeste', ' Castro de Moraes', ' Rocha Alegre', ' das Neves da Praia', ' Azevedo dos Dourados', ' Barbosa Alegre', ' Rezende de Cardoso', ' Alves de Goiás', ' Porto Grande', ' Azevedo de Freitas', ' Freitas', ' da Costa do Norte', ' Ferreira de Martins', ' Correia das Pedras', ' Castro da Praia', ' Correia do Amparo', ' da Rosa da Mata', ' Fogaça do Sul', ' Moreira de Farias', ' Silveira', ' Gomes de Minas', ' da Conceição de Moreira', ' Oliveira de Viana', ' Nogueira da Prata', ' Costa Verde', ' Nascimento do Campo', ' Carvalho do Amparo', ' Rocha de da Luz', ' Silveira do Norte', ' Moura do Campo', ' Correia de Araújo', ' Ferreira Verde', ' Campos das Flores', ' Almeida Verde', ' Sales Grande', ' Araújo da Mata', ' Novaes da Prata', ' Santos da Praia', ' Campos de Pereira', ' Gomes do Norte', ' Ribeiro das Flores', ' Ferreira dos Dourados', ' Vieira do Campo', ' Ferreira de Minas', ' Santos Verde', ' Moreira Alegre', ' Cavalcanti de Goiás', ' Carvalho de Aragão', ' Almeida das Pedras', ' Ribeiro de Goiás', ' da Rocha do Oeste', ' Jesus de da Rocha', ' Correia de Goiás', ' Farias Paulista', ' Melo do Galho', ' Melo do Oeste', ' Jesus do Sul', ' Duarte Alegre', ' da Mota Alegre', ' Fernandes da Serra', ' Aragão da Praia', ' Jesus Alegre', ' Duarte de Goiás', ' Viana Verde', ' da Rocha Paulista', ' Pereira Verde', ' Nogueira do Sul', ' Castro do Amparo', ' da Conceição de Araújo', ' Almeida dos Dourados', ' Campos da Prata', ' Ferreira das Flores', ' Almeida das Flores', ' Fernandes do Campo', ' Mendes do Campo', ' Cardoso de Rocha', ' Aragão da Prata', ' Cardoso Alegre', ' Nascimento de Correia', ' Cardoso de da Rosa', ' Souza de Minas', ' Vieira de Rodrigues', ' Cardoso de Oliveira', ' Cardoso de Cavalcanti', ' Porto de Correia', ' Silveira do Sul', ' Sales de Vieira', ' Castro do Galho', ' Jesus da Prata', ' Fogaça do Amparo', ' Moraes de Campos', ' Ferreira de Peixoto', ' Almeida Paulista', ' Ramos das Pedras', ' das Neves das Flores', ' Aragão do Oeste', ' Teixeira do Norte', ' da Costa de Santos', ' Rodrigues das Pedras', ' Viana da Mata', ' Alves do Galho', ' Carvalho de Goiás', ' da Costa da Prata', ' Ribeiro da Prata', ' Moura de Carvalho', ' Costela do Sul', ' Rodrigues de Pinto', ' Duarte Paulista', ' Jesus Verde', ' da Rosa Paulista', ' Santos da Mata', ' Vieira dos Dourados', ' Azevedo da Praia', ' Rodrigues Grande', ' Duarte dos Dourados', ' da Conceição Grande', ' da Cruz do Galho', ' Pires de Viana', ' Silveira do Galho', ' Ribeiro de Lima', ' Campos de da Mota', ' Nunes de Freitas', ' da Rocha de Aragão', ' da Rocha do Galho', ' das Neves da Mata', ' Moreira do Sul', ' Melo Grande', ' Melo Verde', ' da Conceição de Lima', ' Moraes do Oeste', ' Souza', ' Lopes da Mata', ' Ramos de Sales', ' da Luz de Teixeira', ' Lima Alegre', ' Sales do Oeste', ' da Cunha de Ferreira', ' Rocha Paulista', ' Costa', ' Teixeira das Flores', ' Oliveira do Sul', ' Pinto de da Cruz', ' Pinto do Sul', ' Gonçalves de Pires', ' Cardoso da Mata', ' Ribeiro Alegre', ' da Mata da Mata', ' Moura Alegre', ' Gomes Verde', ' Cardoso de Goiás', ' Silveira dos Dourados', ' Sales da Serra', ' Costa de da Mata', ' Cardoso de Peixoto', ' Peixoto de Martins', ' Cavalcanti do Galho', ' Sales de Mendes', ' Costa de Cardoso', ' da Mota dos Dourados', ' Nascimento das Pedras', ' Martins de Dias', ' Caldeira de Costela', ' Almeida do Norte', ' Santos da Prata', ' Costela Paulista', ' Lopes do Norte', ' Porto das Pedras', ' Oliveira do Oeste', ' Rocha do Sul', ' Nascimento da Praia', ' Gonçalves de Porto', ' da Mata Verde', ' Peixoto do Campo', ' Castro de Alves', ' Caldeira do Sul', ' da Mata do Amparo', ' Monteiro de Fernandes', ' Rodrigues de Fernandes', ' Rocha de Duarte', ' Farias de Goiás', ' Pires Grande', ' Jesus do Galho', ' Fernandes da Mata', ' Sales do Norte', ' Fernandes do Norte', ' Campos do Amparo', ' Ribeiro Paulista', ' Gonçalves de Fogaça', ' Vieira Grande', ' Alves das Pedras', ' Martins de Martins', ' Alves de Lima', ' da Rosa de Vieira', ' Duarte de da Conceição', ' Freitas de da Cruz', ' Pires do Galho', ' Costa de Goiás', ' Correia Alegre', ' Rodrigues Paulista', ' Cardoso de Fogaça', ' Silva de da Conceição', ' Oliveira da Mata', ' Martins do Campo', ' Nascimento Paulista', ' Costela das Pedras', ' da Luz de Nunes', ' Costa do Amparo', ' Farias de da Cunha', ' Correia de Nunes', ' Lima de Alves', ' da Cruz dos Dourados', ' Costela de Novaes', ' da Rosa dos Dourados', ' Azevedo do Oeste', ' Novaes de Oliveira', ' Rezende do Sul', ' Souza do Sul', ' Barbosa de da Rocha', ' Moreira do Oeste', ' Melo da Prata', ' Fernandes de Cardoso', ' Barbosa dos Dourados', ' da Luz de Campos', ' da Mota do Oeste', ' da Mota das Flores', ' Souza Verde', ' Oliveira da Praia', ' da Cunha de Goiás', ' Farias de Barros', ' Campos do Sul', ' Cavalcanti da Praia', ' Mendes das Flores', ' Pinto das Flores', ' da Rocha de Cardoso', ' Castro de Minas', ' Araújo de Alves', ' das Neves de Barros', ' Peixoto de Goiás', ' Porto Verde', ' da Paz da Praia', ' Gonçalves do Amparo', ' da Costa de Melo', ' Azevedo Verde', ' Nunes do Sul', ' da Mata de Mendes', ' da Rocha Alegre', ' Correia de Silva', ' Cunha de Rocha', ' Jesus da Serra', ' Araújo da Prata', ' Mendes do Galho', ' Cunha de Peixoto', ' Lima do Norte', ' Lopes da Serra', ' Duarte de Moreira', ' da Luz da Praia', ' Costela de Ferreira', ' Campos Alegre', ' Ribeiro de Aragão', ' Melo de Goiás', ' Gonçalves Alegre', ' Moreira dos Dourados', ' Teixeira Paulista', ' Costela de da Costa', ' Costela da Mata', ' da Conceição do Campo', ' da Rosa das Pedras', ' Ramos do Oeste', ' Peixoto dos Dourados', ' das Neves de Monteiro', ' Jesus de Goiás', ' Ramos do Galho', ' Araújo do Norte', ' Santos do Amparo', ' Cardoso de Minas', ' Correia do Oeste', ' da Mota do Galho', ' da Rosa Grande', ' Novaes do Campo', ' Cardoso do Norte', ' Freitas das Pedras', ' Cardoso de da Paz', ' Moreira da Prata', ' Souza do Campo', ' Alves de Silva', ' Duarte de Freitas', ' da Cunha de Moraes', ' Araújo de Castro', ' Moura do Galho', ' Moura do Amparo', ' Fogaça dos Dourados', ' Azevedo Alegre', ' Pinto da Praia', ' Farias do Galho', ' Moura de da Mata', ' Fogaça de Novaes', ' Rocha do Galho', ' Costela do Oeste', ' Costa Alegre', ' Sales de Minas', ' Carvalho do Galho', ' Almeida de Minas', ' Nascimento Verde', ' Cavalcanti de Campos', ' Freitas de Minas', ' Oliveira de Castro', ' Campos de da Cruz', ' Peixoto de da Cunha', ' Lima de Viana', ' Gomes do Oeste', ' Costa de Lopes', ' Ramos do Norte', ' Correia de Silveira', ' da Conceição do Sul', ' Ribeiro de da Rosa', ' Mendes do Oeste', ' Oliveira de Minas', ' Novaes de Melo', ' Teixeira do Oeste', ' Pinto de Pires', ' Vieira de Moura', ' Alves da Mata', ' Moura do Oeste', ' Vieira de Caldeira', ' Santos de Minas', ' Costela de Duarte', ' Gonçalves da Prata', ' Campos de Goiás', ' Novaes das Pedras', ' Moreira de Vieira', ' das Neves dos Dourados', ' Cunha de Mendes', ' da Mota de Goiás', ' Barbosa da Mata', ' Barbosa da Prata', ' Nunes de da Rosa', ' Lopes de Goiás', ' Melo de Lima', ' Silva dos Dourados', ' da Luz Grande', ' Silva das Flores', ' da Costa do Galho', ' Pires de Minas', ' Porto de Teixeira', ' Jesus do Oeste', ' Nunes da Prata', ' Castro das Flores', ' Nascimento Alegre', ' Cavalcanti de Rezende', ' Farias dos Dourados', ' da Paz Verde', ' Silveira de Mendes', ' Farias Alegre', ' da Conceição de Nunes', ' Pinto de Nogueira', ' Martins da Serra', ' Teixeira de Monteiro', ' Duarte do Amparo', ' Freitas do Norte', ' Gomes da Praia', ' Pires Verde', ' da Cruz do Norte', ' Silveira Paulista', ' Ribeiro de Fernandes', ' da Mata do Oeste', ' Melo de Nunes', ' Ribeiro de Silva', ' Sales de da Rosa', ' Correia do Norte', ' da Rosa de Ferreira', ' Cunha da Prata', ' Nascimento de Moraes', ' Pires de Moreira', ' da Paz de Minas', ' Lima de Ferreira', ' Castro do Norte', ' Cardoso de Nunes', ' Novaes do Oeste', ' Castro de Martins', ' Cunha Alegre', ' Barros do Norte', ' Monteiro de Peixoto', ' Rezende do Campo', ' Silva do Campo', ' Souza de Nascimento', ' Ramos Verde', ' Barbosa de Costela', ' Pinto do Campo', ' Rocha de Peixoto', ' Caldeira da Prata', ' Rocha da Praia', ' Ferreira de Goiás', ' da Cruz do Amparo', ' Moreira Verde', ' Jesus de Rezende', ' Costa de Melo', ' Fogaça de Minas', ' Sales de Caldeira', ' Barros de Nogueira', ' Nascimento do Oeste', ' Dias do Norte', ' Nascimento das Flores', ' da Luz do Campo', ' da Luz da Serra', ' Gonçalves de da Mota', ' Moraes da Serra', ' Silveira Alegre', ' Viana de Fernandes', ' Porto de Rodrigues', ' Nascimento da Serra', ' Moura das Pedras', ' Rodrigues das Flores', ' da Cunha do Campo', ' Melo do Campo', ' Rocha de Minas', ' das Neves de Moreira', ' Pires do Campo', ' Barros Alegre', ' Cunha da Serra', ' Azevedo das Flores', ' Costela de Farias', ' Freitas do Amparo', ' Rocha de Campos', ' Aragão das Pedras', ' Alves dos Dourados', ' da Luz de Goiás', ' da Costa da Praia', ' Souza do Oeste', ' Dias do Amparo', ' Lima de Azevedo', ' da Cruz de Lopes', ' da Cunha Verde', ' da Mata da Prata', ' Rezende da Serra', ' Nunes das Pedras', ' Barros de Ferreira', ' Correia da Prata', ' Campos de Castro', ' Nascimento de Carvalho', ' da Paz de da Costa', ' Viana dos Dourados', ' Caldeira do Galho', ' Dias de Goiás', ' Barros do Amparo', ' Oliveira dos Dourados', ' Rodrigues do Norte', ' Peixoto Verde', ' Araújo Paulista', ' Vieira de Teixeira', ' Cardoso de Nogueira', ' Almeida de Pereira', ' Alves de Oliveira', ' Ribeiro de da Rocha', ' Monteiro Grande', ' Fogaça da Mata', ' Jesus das Flores', ' Nunes da Praia', ' Mendes de Goiás', ' Correia Verde', ' Gomes de Castro', ' Cunha do Amparo', ' da Mota de Castro', ' Moura de Pires', ' Mendes Verde', ' da Rosa de Goiás', ' Cavalcanti do Oeste', ' da Mota de Barros', ' da Conceição de Campos', ' Rodrigues Alegre', ' da Paz Alegre', ' da Paz de Silva', ' Pinto de Goiás', ' Mendes de Moreira', ' Fernandes do Oeste', ' Viana do Galho', ' Gomes de Sales', ' Monteiro de Souza', ' Nascimento dos Dourados', ' Novaes Grande', ' das Neves da Prata', ' Freitas dos Dourados', ' Novaes do Norte', ' Almeida do Campo', ' Farias da Mata', ' Martins de Carvalho', ' Santos do Galho', ' da Costa Alegre', ' Azevedo de Minas', ' Rodrigues do Amparo', ' Porto dos Dourados', ' Lima de Pereira', ' Correia de Pinto', ' Souza do Amparo', ' Jesus de Moraes', ' Costela de da Mota', ' Lopes do Sul', ' da Cunha Paulista', ' Caldeira do Norte', ' Moreira de Cardoso', ' Pinto do Amparo', ' Cavalcanti das Flores', ' Moura Grande', ' da Cruz do Campo', ' Pires do Norte', ' Cavalcanti de Duarte', ' Lopes da Praia', ' Peixoto de Sales', ' da Cunha do Galho', ' Araújo de Melo', ' Monteiro do Sul', ' da Luz dos Dourados', ' Viana Paulista', ' Castro de Gonçalves', ' Melo de Viana', ' Barros Paulista', ' Sales da Prata', ' da Conceição de Gonçalves', ' Peixoto do Amparo', ' Aragão Grande', ' Farias da Praia', ' Melo de Minas', ' Gomes de Carvalho', ' Viana da Praia', ' Silva Verde', ' Ribeiro de Pinto', ' Santos do Norte', ' da Mata do Sul', ' Freitas Alegre', ' Moraes de Novaes', ' Castro da Mata', ' Lopes do Galho', ' Carvalho da Mata', ' Caldeira Grande', ' Cavalcanti do Campo', ' Nogueira do Galho', ' Nogueira de Minas', ' Carvalho da Serra', ' Silva da Praia', ' Gonçalves das Pedras', ' Alves da Praia', ' Castro de da Mata', ' Ramos do Amparo', ' Melo Alegre', ' Aragão do Norte', ' Costa do Sul', ' Fernandes de Aragão', ' Duarte Verde', ' Costa de da Rocha', ' Santos de Lima', ' Aragão de Barros', ' Dias Alegre', ' Gonçalves da Serra', ' Nascimento de Rodrigues', ' Novaes Paulista', ' Azevedo de Aragão', ' Jesus de Ramos', ' Melo da Mata', ' Fogaça de Araújo', ' Ribeiro de Vieira', ' Vieira da Praia', ' Alves de Sales', ' Araújo das Flores', ' Pinto de Pinto', ' Castro do Sul', ' Fogaça Verde', ' Moraes de Mendes', ' Correia de Melo', ' da Paz do Amparo', ' Duarte de Pereira', ' Cunha das Flores', ' Ferreira da Praia', ' Castro Verde', ' Cunha de Souza', ' Ramos de Alves', ' Monteiro da Praia', ' da Cunha de Porto', ' Almeida da Prata', ' da Conceição de Carvalho', ' da Mata de Minas', ' Carvalho de Nunes', ' Silveira de Peixoto', ' Barros do Galho', ' Freitas Verde', ' Carvalho Alegre', ' Lima da Prata', ' Souza das Flores', ' Mendes Alegre', ' Moura de Goiás', ' Monteiro de da Rosa', ' Moreira de Minas', ' da Conceição da Mata', ' Pereira do Amparo', ' Carvalho Paulista', ' Novaes de Monteiro', ' Rezende de Pereira', ' Ramos da Serra', ' Azevedo da Mata', ' Fogaça da Serra', ' Novaes de Ribeiro', ' da Cunha de Monteiro', ' Fernandes de Caldeira', ' Farias do Norte', ' Pinto de Pereira', ' Pinto Grande', ' Nogueira Grande', ' da Mata da Praia', ' Barbosa do Amparo', ' Correia da Serra', ' Santos do Sul', ' Silva de Castro', ' Almeida de da Costa', ' Sales do Amparo', ' Farias de Minas', ' Cavalcanti Verde', ' Carvalho das Flores', ' Santos de da Rocha', ' da Cunha do Norte', ' Santos do Campo', ' Sales do Campo', ' Nunes Alegre', ' Cunha de Novaes', ' Melo de Dias', ' Moreira Grande', ' da Rocha de Campos', ' da Costa das Flores', ' Novaes do Sul', ' da Paz das Flores', ' da Conceição de Costa', ' Cardoso do Amparo', ' da Mata Alegre', ' Nogueira do Amparo', ' Nunes do Galho', ' Alves da Prata', ' Fogaça das Pedras', ' Correia Paulista', ' Fernandes de Nascimento', ' das Neves das Pedras', ' da Cunha de Rodrigues', ' Alves Verde', ' Peixoto do Norte', ' Almeida Grande', ' Silveira da Praia', ' Fernandes das Flores', ' Almeida de da Luz', ' Teixeira de Teixeira', ' Dias de Caldeira', ' da Mota de Aragão', ' Ribeiro do Galho', ' Rezende do Oeste', ' da Conceição da Prata', ' Teixeira do Galho', ' Castro de Ferreira', ' Ferreira de Gonçalves', ' Carvalho de Moura', ' da Cruz da Serra', ' Correia de Caldeira', ' Almeida do Sul', ' Melo das Flores', ' Melo de Pinto', ' Monteiro Alegre', ' Martins do Sul', ' Oliveira de da Costa', ' Mendes de Ramos', ' Rocha da Mata', ' Pereira de Freitas', ' Freitas de Aragão', ' Mendes de Aragão', ' Peixoto Paulista', ' Freitas do Galho', ' Campos de Fernandes', ' Cunha do Oeste', ' Lopes de Dias', ' Fogaça de Vieira', ' Dias do Oeste', ' Rezende do Norte', ' Correia de da Mota', ' Araújo de Goiás', ' das Neves de Rezende', ' Gonçalves da Praia', ' da Cunha Grande', ' Campos Verde', ' Gomes de Barros', ' Barros Grande', ' Nogueira Verde', ' Moreira de Azevedo', ' Jesus dos Dourados', ' Nascimento de Goiás', ' Freitas do Sul', ' Duarte do Sul', ' Pires da Serra', ' Lima de Monteiro', ' Campos do Oeste', ' Rocha da Serra', ' da Conceição Verde', ' Souza Alegre', ' Porto de Melo', ' da Rosa do Amparo', ' da Cunha da Prata', ' Peixoto de Minas', ' da Rosa da Serra', ' Ribeiro de da Cruz', ' Monteiro Verde', ' Fernandes de Mendes', ' Ferreira de Jesus', ' Rocha Grande', ' Farias de Fogaça', ' Ramos de Goiás', ' Rezende de Goiás', ' Nogueira de da Luz', ' Azevedo de Silva', ' Correia da Praia', ' Jesus de Lima', ' Jesus de Novaes', ' Pinto Verde', ' Aragão de Cunha', ' Araújo de Pinto', ' Silva de Goiás', ' Silveira das Pedras', ' Lopes Verde', ' da Conceição de Goiás', ' Alves de Monteiro', ' Dias das Flores', ' da Rocha de Barbosa', ' Farias da Prata', ' Costela da Serra', ' Moura de Correia', ' Lopes dos Dourados', ' das Neves de Barbosa', ' Monteiro do Galho', ' Azevedo do Campo', ' Nogueira da Serra', ' Rocha das Flores', ' da Cruz de Rodrigues', ' Nunes das Flores', ' Caldeira do Campo', ' Moraes de Viana', ' Campos de Minas', ' Novaes de Cavalcanti', ' Jesus de Fogaça', ' Gomes de Teixeira', ' da Mota de Moura', ' da Costa da Serra', ' Silveira das Flores', ' Carvalho de Pereira', ' das Neves do Galho', ' Campos da Praia', ' Ramos de Cardoso', ' Gonçalves do Norte', ' da Luz Paulista', ' Rodrigues de Souza', ' Dias dos Dourados', ' Carvalho do Oeste', ' Lima do Sul', ' Ferreira da Prata', ' Barbosa Verde', ' da Cunha da Praia', ' da Rosa de Cardoso', ' Lopes de Nogueira', ' da Costa do Sul', ' Rocha de Martins', ' da Rosa de Minas', ' da Cunha de da Paz', ' da Mata de da Rocha', ' Gomes de Duarte', ' Cardoso do Galho', ' Viana do Amparo', ' da Mata de da Mata', ' Pires Paulista', ' da Cunha de Rocha', ' Melo de Souza', ' da Luz de das Neves', ' Moura de Minas', ' Viana Alegre', ' Azevedo do Galho', ' Rezende do Amparo', ' Farias de Nascimento', ' Moraes da Prata', ' Monteiro de Aragão', ' Pires do Sul', ' Dias de Cardoso', ' da Luz de Minas', ' Pinto de Costela', ' Nogueira das Pedras', ' das Neves do Campo', ' Lima do Amparo', ' Cavalcanti dos Dourados', ' Campos de Araújo', ' das Neves de Correia', ' Cardoso da Prata', ' Barbosa de Minas', ' Lima de Goiás', ' Barros de Gonçalves', ' Pires de da Rosa', ' da Costa de Minas', ' Martins de Pires', ' Farias do Amparo', ' Viana de Carvalho', ' da Luz do Norte', ' Silveira de Goiás', ' Silva de Freitas', ' Moraes de Nogueira', ' Novaes Alegre', ' das Neves de Carvalho', ' Caldeira da Serra', ' Gonçalves de Correia', ' Barbosa Grande', ' Monteiro da Prata', ' Rezende dos Dourados', ' Monteiro de Goiás', ' Castro dos Dourados', ' Cardoso de Aragão', ' Souza da Mata', ' Nogueira da Praia', ' Rocha do Oeste', ' da Rosa do Norte', ' Silveira do Oeste', ' Gomes Paulista', ' da Conceição dos Dourados', ' Nunes de Goiás', ' Gonçalves de Costela', ' da Cruz da Praia', ' Silva de Fernandes', ' das Neves Alegre', ' Jesus do Norte', ' Ramos da Mata', ' Cunha de Ramos', ' Carvalho de Dias', ' da Cruz Grande', ' Aragão de Rezende', ' Moreira da Praia', ' Pires de Silveira', ' Farias Verde', ' Costa de Jesus', ' Peixoto da Prata', ' Rezende de Dias', ' da Mota Paulista', ' da Mata de Ribeiro', ' Gonçalves do Sul', ' Pereira do Norte', ' Aragão de Moreira', ' da Cunha dos Dourados', ' Barbosa de Caldeira', ' Barbosa do Campo', ' Jesus das Pedras', ' Silveira de da Costa', ' Costa Grande', ' Costela de Freitas', ' Monteiro do Amparo', ' Gomes de da Paz', ' da Cruz de Cavalcanti', ' Fogaça Grande', ' Araújo de Silva', ' Sales Paulista', ' Campos de Nascimento', ' Cardoso de Santos', ' Monteiro de da Paz', ' Castro de Caldeira', ' Martins de Sales', ' Vieira de da Mata', ' Lima dos Dourados', ' Melo dos Dourados', ' Gonçalves do Oeste', ' Cavalcanti da Mata', ' Ferreira do Galho', ' Silva Grande', ' Moraes Paulista', ' Costa de Minas', ' Pires do Oeste', ' Jesus Grande', ' Rocha Verde', ' Duarte da Prata', ' Ferreira de Vieira', ' Freitas da Prata', ' Nogueira de Ferreira', ' Ribeiro do Sul', ' Moura da Praia', ' Martins de Rodrigues', ' Nunes Paulista', ' Nogueira Paulista', ' Rezende de Minas', ' Costela de Fernandes', ' da Cruz da Prata', ' Ferreira do Norte', ' Dias Paulista', ' da Rocha do Amparo', ' Rezende Grande', ' Novaes do Amparo', ' Costela Verde', ' Teixeira das Pedras', ' Silveira da Mata', ' da Paz das Pedras', ' da Mota do Amparo', ' Vieira de Costela', ' das Neves Paulista', ' da Cruz de da Rocha', ' da Mata Grande', ' Pereira Paulista', ' Nunes de Barbosa', ' Nogueira Alegre', ' Moura de Ferreira', ' Silveira de Costela', ' Gomes da Prata', ' Silveira do Amparo', ' Rocha das Pedras', ' da Mata de Nunes', ' Ferreira da Serra', ' Barros Verde', ' Barros dos Dourados', ' Barros de Pereira', ' Dias de Fernandes', ' Araújo de Nunes', ' Nogueira de Rezende', ' da Cunha das Flores', ' Pereira do Campo', ' Ribeiro Grande', ' Aragão de Viana', ' Aragão Verde', ' Alves de Farias', ' Duarte do Oeste', ' Carvalho Verde', ' Freitas de Goiás', ' Duarte Grande', ' Monteiro das Pedras', ' Martins do Amparo', ' da Costa das Pedras', ' Porto de Mendes', ' da Mata de Jesus', ' da Cruz do Sul', ' Duarte de Duarte', ' Pires de Barros', ' Pereira das Pedras', ' Carvalho de Minas', ' Caldeira das Flores', ' Vieira de Porto', ' Correia de Fernandes', ' Souza de Caldeira', ' Novaes de Freitas', ' Nogueira de Aragão', ' Araújo do Oeste', ' Monteiro de Jesus', ' Azevedo de da Conceição', ' Barros da Prata', ' Caldeira Paulista', ' Pires de da Luz', ' Carvalho das Pedras', ' da Conceição de Alves', ' Silva Paulista', ' Farias de Novaes', ' Melo de da Paz', ' Moreira de Lopes', ' Melo do Amparo', ' Cardoso de da Rocha', ' da Cunha de Minas', ' Cardoso de da Costa', ' da Costa de Goiás', ' Dias de Moreira', ' Rodrigues de Melo', ' Vieira de Moreira', ' Moreira de da Rosa', ' Oliveira das Pedras', ' Fernandes do Galho', ' Pinto Paulista', ' Viana da Serra', ' Pires de Dias', ' da Mota de Cavalcanti', ' Aragão do Amparo', ' da Rosa do Oeste', ' Ribeiro Verde', ' Teixeira da Mata', ' Costa de Monteiro', ' Cavalcanti do Norte', ' Duarte de Minas', ' Barros de das Neves', ' Costa de Caldeira', ' da Rocha Verde', ' Viana de Minas', ' Moraes de da Paz', ' Alves de Correia', ' da Mota da Praia', ' Souza de Duarte', ' Viana de Melo', ' Jesus de Ribeiro', ' Teixeira da Serra', ' Pires de Caldeira', ' Ferreira do Sul', ' Farias do Sul', ' Rocha de Lima', ' Pires dos Dourados', ' Ferreira Grande', ' da Mota Grande', ' da Conceição Alegre', ' Mendes de Gonçalves', ' Souza de Costa', ' Ribeiro de Duarte', ' da Rocha de Lima', ' Costa de Martins', ' Cunha dos Dourados', ' Castro de Cardoso', ' Nascimento do Galho', ' Cavalcanti das Pedras', ' da Cruz das Pedras', ' Martins de da Costa', ' Jesus do Amparo', ' Fogaça de Campos', ' Silva de Duarte', ' Gomes das Flores', ' da Costa de da Mata', ' Peixoto de Cardoso', ' Campos do Galho', ' Gonçalves Verde', ' da Mata de Nogueira', ' da Conceição do Oeste', ' Porto do Galho', ' da Luz do Galho', ' Rocha do Campo', ' da Mota de Minas', ' Farias Grande', ' Martins do Oeste', ' Viana das Pedras', ' Lima Verde', ' Caldeira de Sales', ' Fernandes Verde', ' Souza da Prata', ' Pereira das Flores', ' Ribeiro de Melo', ' Martins Paulista', ' Silva do Galho', ' Campos de da Paz', ' Fogaça do Oeste', ' Gonçalves dos Dourados', ' da Mata de Rocha', ' Peixoto de Castro', ' Silveira de Silveira', ' Pereira de Pires', ' da Cruz da Mata', ' Costela do Amparo', ' Moura Paulista', ' Freitas de Nascimento', ' Silva da Prata', ' Fernandes de Alves', ' Alves de Fogaça', ' Vieira de Souza', ' Barros de Melo', ' Castro de da Paz', ' das Neves do Norte', ' da Rocha do Norte', ' Mendes de Pinto', ' Cunha Grande', ' Carvalho de Carvalho', ' Peixoto do Galho', ' Barros de da Mata', ' Moreira da Mata', ' Carvalho de Caldeira', ' Costela de Viana', ' Ribeiro da Praia', ' Moreira de Ribeiro', ' Oliveira de Cunha', ' da Paz Paulista', ' Moura do Sul', ' Peixoto Alegre', ' Teixeira Grande', ' Silva de da Mota', ' Azevedo Grande', ' Cavalcanti de Nascimento', ' Caldeira de Oliveira', ' Viana de Ribeiro', ' Rezende de Barros', ' Carvalho do Norte', ' Oliveira de Aragão', ' Rezende das Pedras', ' Cardoso dos Dourados', ' Fogaça de Ramos', ' das Neves do Oeste', ' Gonçalves Paulista', ' Araújo do Sul', ' Cavalcanti do Sul', ' Rodrigues do Galho', ' Ferreira de Souza', ' Barros de Costa', ' Carvalho do Sul', ' Barros de Santos', ' Ramos Paulista', ' Gomes Grande', ' Rocha de da Cunha', ' Silveira de da Rocha', ' Correia de da Luz', ' Monteiro de da Luz', ' da Luz do Oeste', ' da Rosa de Pinto', ' Fogaça do Galho', ' Castro de Cunha', ' da Rocha de Costela', ' Martins dos Dourados', ' Pires do Amparo', ' Lopes de Minas', ' da Mata das Flores', ' Novaes dos Dourados', ' Rodrigues do Sul', ' Teixeira do Amparo', ' da Cruz de Moura', ' Porto do Campo', ' Fogaça de Ribeiro', ' da Costa Grande', ' Souza de da Rocha', ' Nunes de Farias', ' Aragão da Mata', ' da Rocha da Mata', ' Nascimento de Souza', ' Rodrigues Verde', ' Dias das Pedras', ' Fogaça de Gomes', ' Gonçalves de Jesus', ' Moura de Dias', ' Carvalho de Rezende', ' da Conceição do Amparo', ' Costela do Campo', ' Aragão de Cardoso', ' Martins de Goiás', ' Azevedo da Serra', ' Teixeira de Goiás', ' Souza de Fernandes', ' Cardoso de Cardoso', ' Freitas de Pereira', ' da Cunha das Pedras', ' Fernandes Grande', ' Cardoso de Pires', ' Lima das Flores', ' Ribeiro do Campo', ' Castro de da Costa', ' Lima do Galho', ' Silva de Cardoso', ' Barbosa das Flores', ' Carvalho de Costela', ' Freitas do Oeste', ' da Cunha Alegre', ' da Conceição de das Neves', ' Azevedo de Jesus', ' Castro da Serra', ' Silva de Porto', ' Pires da Prata', ' Pinto de Minas', ' Pinto de Ferreira', ' Ferreira de Azevedo', ' Almeida da Serra', ' Rezende da Prata', ' Santos Paulista', ' Barros de Correia', ' Lima Paulista', ' Campos de Nogueira', ' Farias de Duarte', ' Melo de Teixeira', ' Vieira de Fogaça', ' Caldeira de Rodrigues', ' Barbosa de Freitas', ' da Rocha de Nogueira', ' Mendes Grande', ' Alves de Dias', ' Lima da Praia', ' Cunha de Cardoso', ' Peixoto de Nascimento', ' Porto de Caldeira', ' Porto da Prata', ' Oliveira do Campo', ' Vieira das Pedras', ' Melo da Praia', ' Ribeiro da Mata', ' Cavalcanti de da Cunha', ' da Rocha de Vieira', ' Porto de da Paz', ' Gonçalves de Campos', ' Rezende da Praia', ' Viana Grande', ' Viana de Barbosa', ' Cardoso da Serra', ' Martins de Nunes', ' Rocha de Novaes', ' Fogaça de Mendes', ' Pires Alegre', ' Pereira da Prata', ' Correia da Mata', ' Fernandes de Lopes', ' Gonçalves de Goiás', ' da Rocha de Freitas', ' Azevedo das Pedras', ' da Cunha do Amparo', ' Carvalho do Campo', ' Ramos do Sul', ' Lima de Moreira', ' Lopes do Oeste', ' Correia de Cardoso', ' da Luz de Pinto', ' Vieira do Oeste', ' da Rocha de da Costa', ' Novaes da Serra', ' Pinto das Pedras', ' Barbosa da Serra', ' Melo das Pedras', ' Souza de Ramos', ' Caldeira dos Dourados', ' Costa de Porto', ' Peixoto das Pedras', ' Souza de Nunes', ' Araújo de Cardoso', ' Costela das Flores', ' Melo de Sales', ' da Mota da Mata', ' Nunes de Cunha', ' Pinto de Jesus', ' Jesus de Rocha', ' Vieira do Norte', ' Araújo de Oliveira', ' da Conceição de Fogaça', ' Viana de Barros', ' Cunha de Almeida', ' Pinto de Dias', ' Almeida de Ribeiro', ' Carvalho de Gomes', ' da Conceição de da Cunha', ' Lopes de Silva', ' Monteiro de Silva', ' Araújo de Nogueira', ' Porto de Castro', ' Lopes das Flores', ' Santos de Martins', ' Araújo de Moura', ' Silveira de Costa', ' Dias de da Cruz', ' Teixeira de Almeida', ' Rezende de Farias', ' Silveira do Campo', ' Castro de Goiás', ' Vieira da Serra', ' Pinto da Serra', ' Gomes de Goiás', ' da Paz de Fernandes', ' Lopes de Correia', ' Farias de Viana', ' Azevedo Paulista', ' Jesus de Freitas', ' Moraes de Sales', ' Moraes de Cunha', ' Barros de Jesus', ' Lima das Pedras', ' Campos Paulista', ' Correia dos Dourados', ' Gonçalves de Silveira', ' Rodrigues do Oeste', ' Campos das Pedras', ' Jesus de Costela', ' Rodrigues dos Dourados', ' Fogaça da Praia', ' Azevedo de Gonçalves', ' da Rocha de Almeida', ' da Luz Verde', ' Moraes de Costa', ' Mendes de Rocha', ' Nascimento Grande', ' Moreira do Norte', ' Mendes de das Neves', ' Teixeira Alegre', ' Silveira de Rocha', ' da Conceição da Praia', ' da Luz de Cardoso', ' Campos dos Dourados', ' Cunha das Pedras', ' Caldeira de Gonçalves', ' Ferreira de da Mota', ' Rezende de Duarte', ' Gomes de Ribeiro', ' Farias de Rezende', ' Pereira de Cardoso', ' Pires de Monteiro', ' Cardoso do Oeste', ' Cunha da Praia', ' Ferreira Paulista', ' Moura de Costela', ' Pires das Flores', ' Caldeira de Barros', ' Costela do Galho', ' Jesus de Pires', ' das Neves de da Luz', ' Alves da Serra', ' Freitas da Praia', ' Santos de da Costa', ' da Paz Grande', ' Castro de Ribeiro', ' Martins de Pereira', ' Pires de Araújo', ' Freitas Paulista', ' Cunha do Campo', ' Costela do Norte', ' Ribeiro das Pedras', ' Rodrigues de Araújo', ' da Rocha dos Dourados', ' Silva de Cunha', ' da Paz de Goiás', ' Pires de Duarte', ' da Paz de Novaes', ' Souza de Almeida', ' Nogueira dos Dourados', ' Ribeiro de Rodrigues', ' Aragão Alegre', ' Jesus de Cunha', ' da Mota de Fogaça', ' Vieira do Galho', ' Silveira de Rodrigues', ' Freitas das Flores', ' Gonçalves do Campo', ' Pires de da Cruz', ' Gomes do Sul', ' Mendes da Praia', ' Caldeira de Lima', ' Souza de Sales', ' Cardoso de Rezende', ' da Conceição de Monteiro', ' Mendes de da Mota', ' das Neves de Azevedo', ' Nogueira de Goiás', ' Barbosa do Sul', ' Duarte do Campo', ' da Rocha de Barros', ' Rodrigues de Rezende', ' Nascimento de Oliveira', ' Costa de Farias', ' Moura Verde', ' Nunes de Ribeiro', ' Barros de Goiás', ' Moura da Mata', ' Caldeira Verde', ' Oliveira de Farias', ' da Cunha da Serra', ' Almeida de da Cunha', ' Araújo Verde', ' da Cunha de Fernandes', ' Santos de Goiás', ' Cardoso de Ferreira', ' Oliveira de Gonçalves', ' Barros de Viana', ' da Mata do Galho', ' Teixeira de da Mata', ' Sales do Sul', ' Souza de Silveira', ' Santos de Duarte', ' Nunes de Almeida', ' Mendes dos Dourados', ' Rezende de Correia', ' Alves do Campo', ' Costela de Rodrigues', ' Martins de Teixeira', ' Lima de da Paz', ' Costela de Carvalho', ' Santos Grande', ' Sales de Goiás', ' Pereira de da Conceição', ' Castro de Souza', ' Fernandes de Minas', ' da Paz do Norte', ' Pereira do Oeste', ' Freitas de Viana', ' da Rosa da Praia', ' Costa do Norte', ' Jesus da Praia', ' Araújo dos Dourados', ' Peixoto de Cavalcanti', ' Nascimento de Silva', ' Ramos de Cavalcanti', ' Nogueira de Nunes', ' da Conceição das Flores', ' Farias das Flores', ' Freitas da Mata', ' Porto de da Rosa', ' da Rosa de Jesus', ' Monteiro de das Neves', ' Novaes de Duarte', ' Nogueira da Mata', ' Caldeira de Costa', ' Oliveira das Flores', ' Lopes do Amparo', ' da Conceição de Melo', ' Cardoso de Duarte', ' Pinto de Azevedo', ' Oliveira do Galho', ' Mendes de Cunha', ' Duarte de Moura', ' Silveira da Serra', ' Silveira de da Conceição', ' Martins de Almeida', ' Gomes da Mata', ' Cardoso de Farias', ' da Luz de Moraes', ' Duarte de Porto', ' Gonçalves Grande', ' Almeida de Aragão', ' Viana de Santos', ' da Mata de Silveira', ' Rodrigues de Costela', ' Ferreira de Aragão', ' Sales de Freitas', ' Barbosa Paulista', ' Moraes de Dias', ' Cavalcanti de Ramos', ' Melo de Ramos', ' Correia de Carvalho', ' Barbosa de Goiás', ' Mendes da Mata', ' Vieira de das Neves', ' Rocha do Norte', ' Ferreira de Ferreira', ' Martins de da Cunha', ' Santos de Nascimento', ' Lima de Fernandes', ' Viana de Goiás', ' Pires das Pedras', ' Dias do Galho', ' Pereira Alegre', ' Lima da Serra', ' Ramos de Silva', ' Araújo de Costela', ' Ramos dos Dourados', ' Gomes do Amparo', ' Teixeira de Duarte', ' Cardoso de da Mata', ' Freitas Grande', ' Azevedo de Viana', ' Souza da Serra', ' Pereira de da Paz', ' Ramos de Nogueira', ' Aragão das Flores', ' Campos do Norte', ' Barros de Barbosa', ' Barros de Cardoso', ' Porto do Amparo', ' Moura de Lima', ' Oliveira de Alves', ' Araújo da Praia', ' Pinto de Castro', ' Pinto de Mendes', ' Freitas de Teixeira', ' Duarte de Oliveira', ' da Costa do Oeste', ' da Cunha do Sul', ' Viana de Rodrigues', ' Carvalho de Jesus', ' Melo de Caldeira', ' Rezende de Melo', ' Santos de Silveira', ' Alves de Pereira', ' Santos das Pedras', ' Moraes de Azevedo', ' da Cruz de Sales', ' Moraes de Rodrigues', ' Nunes de Oliveira', ' Fernandes de Campos', ' Nascimento de Nunes', ' das Neves do Sul', ' Barros de Dias', ' Fernandes Paulista', ' Silveira de Almeida', ' Peixoto Grande', ' da Cruz de Gomes', ' Pereira de Mendes', ' Duarte do Norte', ' das Neves Verde', ' Melo de Melo', ' Araújo Grande', ' da Cruz de Novaes', ' Cunha Paulista', ' Viana do Campo', ' Pires de da Mata', ' Teixeira de Gomes', ' Oliveira de Santos', ' Lopes do Campo', ' Oliveira de Cardoso', ' Rocha de Mendes', ' Rezende da Mata', ' da Luz das Pedras', ' Costela de Minas', ' Costela dos Dourados', ' Vieira da Prata', ' Moreira de Gomes', ' Teixeira de Castro', ' Ribeiro de Teixeira', ' da Luz de Moura', ' Pinto da Prata', ' da Rocha de Fogaça', ' da Luz de Silveira', ' Almeida de Monteiro', ' Martins Verde', ' Azevedo de da Rosa', ' Carvalho de Moreira', ' Aragão do Galho', ' Alves Alegre', ' Souza de Goiás', ' Aragão do Sul', ' Santos do Oeste', ' Freitas de Caldeira', ' Porto da Praia', ' Rezende de Porto', ' Castro de Farias', ' Lima de Vieira', ' Gonçalves do Galho', ' Almeida de Mendes', ' Novaes das Flores', ' Monteiro de Nogueira', ' da Paz de Rodrigues', ' Azevedo de Farias', ' Duarte de Barros', ' Vieira de Araújo', ' Oliveira Verde', ' Duarte da Mata', ' Freitas da Serra', ' Silva da Mata', ' Lopes Grande', ' Rodrigues de Fogaça', ' da Costa do Amparo', ' Correia de Correia', ' Lopes de da Rocha', ' Mendes Paulista', ' Viana de Gonçalves', ' Alves de Cardoso', ' da Mota do Sul', ' Barbosa de Moreira', ' Carvalho da Prata', ' Melo do Norte', ' Fernandes de da Cunha', ' Moreira do Campo', ' Moraes do Galho', ' Novaes do Galho', ' Peixoto de Dias', ' Porto Alegre', ' Monteiro de Correia', ' Moraes do Norte', ' Teixeira do Sul', ' Souza dos Dourados', ' da Cunha de Ribeiro', ' Nogueira de das Neves', ' Lopes de Fernandes', ' Lopes de Moraes', ' Monteiro de Farias', ' Costa de Oliveira', ' Castro de Oliveira', ' Rodrigues de da Paz', ' Silveira de Ferreira', ' Farias da Serra', ' Caldeira Alegre', ' Rodrigues da Serra', ' Rodrigues de Nascimento', ' Mendes do Norte', ' Nascimento da Mata', ' Carvalho de Lima', ' Moura de da Mota', ' Pires de Azevedo', ' da Mota do Campo', ' Caldeira de Cavalcanti', ' Fogaça de Goiás', ' Gonçalves de Araújo', ' Oliveira do Amparo', ' Farias de Lopes', ' Lopes de da Paz', ' Farias de Rocha', ' Rodrigues da Praia', ' Castro do Oeste', ' Oliveira da Prata', ' Pires de Costela', ' Costela de da Rocha', ' Lima de Santos', ' Rezende de Cavalcanti', ' da Luz do Amparo', ' Castro do Campo', ' Gomes do Galho', ' das Neves Grande', ' Caldeira do Amparo', ' Almeida de Melo', ' Correia de Farias', ' Correia de Fogaça', ' Pinto do Norte', ' Moraes de Carvalho', ' Moreira do Amparo', ' Oliveira Paulista', ' da Luz da Prata', ' Cavalcanti de da Cruz', ' Santos de Sales', ' Monteiro do Oeste', ' Silva de Sales', ' Costa de da Luz', ' Viana de da Costa', ' Mendes de Minas', ' Castro de Pinto', ' Ferreira do Amparo', ' das Neves de Ferreira', ' Peixoto de Caldeira', ' Monteiro de Cardoso', ' Barros da Serra', ' Melo de Azevedo', ' Dias Verde', ' Melo de Mendes', ' Cunha Verde', ' Correia Grande', ' Ribeiro de Moraes', ' Fogaça de Alves', ' Porto das Flores', ' Barros de da Mota', ' Silva de Campos', ' Moreira de Mendes', ' da Mota de Campos', ' Moraes de Porto', ' Peixoto de da Mata', ' Nunes da Serra', ' Mendes de Cardoso', ' Fernandes da Prata', ' da Luz de da Rocha', ' Martins de Cardoso', ' Cavalcanti de Silveira', ' da Rocha de da Conceição', ' Castro de Monteiro', ' Viana de Duarte', ' Vieira de Minas', ' Rodrigues de Goiás', ' Aragão de Freitas', ' Moraes Verde', ' Farias de Almeida', ' Peixoto da Serra', ' Caldeira das Pedras', ' da Rosa do Campo', ' Mendes de Alves', ' da Paz dos Dourados', ' Pereira de Rodrigues', ' Castro das Pedras', ' Rezende de Ferreira', ' Rodrigues de Pires', ' Gomes das Pedras', ' Gonçalves das Flores', ' Silva de Minas', ' Nascimento de Jesus', ' Ramos de da Paz', ' da Rosa do Sul', ' Barros de da Costa', ' Ramos de Souza', ' Azevedo de Campos', ' Silveira de Carvalho', ' da Costa de Cardoso', ' Caldeira de Araújo', ' Vieira de Fernandes', ' Mendes de da Costa', ' Ferreira do Oeste', ' da Mata de das Neves', ' Cunha de da Mota', ' Silveira de Barbosa', ' Carvalho da Praia', ' Ramos de Costela', ' Araújo de Peixoto', ' Ramos de Pereira', ' Monteiro de Vieira', ' Viana de das Neves', ' Nascimento de Fernandes', ' Martins da Praia', ' Cunha de Campos', ' Costa de da Conceição', ' Aragão de Monteiro', ' Vieira da Mata', ' Silva de da Luz', ' Barbosa do Norte', ' Ferreira de Costela', ' Souza de Peixoto', ' Pereira dos Dourados', ' Aragão de Sales', ' da Paz do Oeste', ' Peixoto de Almeida', ' Silveira de da Rosa', ' Freitas de Azevedo', ' Rodrigues de Gonçalves', ' Caldeira de Nascimento', ' Ribeiro de Gomes', ' Gomes de Pires', ' Souza de da Paz', ' Rocha de Nogueira', ' das Neves de Ramos', ' Jesus de Caldeira', ' Duarte de Cardoso', ' Rodrigues do Campo', ' Viana de da Paz', ' Freitas de Nunes', ' Viana de da Rosa', ' Mendes da Serra', ' Sales de Azevedo', ' Jesus de Souza', ' Cardoso de Melo', ' Nascimento de da Costa', ' Gomes de Cardoso', ' Rodrigues da Mata', ' Lopes da Prata', ' da Cunha do Oeste', ' Martins de Vieira', ' Azevedo de Lima', ' Gomes da Serra', ' Pinto de Martins', ' Moraes de Minas', ' da Luz de Rocha', ' Fogaça das Flores', ' Melo de Araújo', ' Correia de Duarte', ' Martins de Ferreira', ' Caldeira de Rezende', ' Fogaça de Fernandes', ' Ramos de Cunha', ' da Rocha de da Rocha', ' Rezende de Moura', ' Moura de Barbosa', ' Rocha de Goiás', ' Teixeira de Jesus', ' Novaes Verde', ' Freitas de Duarte', ' Santos de Campos', ' Costela de Melo', ' Ferreira de Gomes', ' Porto de Nogueira', ' Moura de Nascimento', ' Silveira de Teixeira', ' Almeida da Mata', ' Martins de Viana', ' Silveira de Barros', ' Monteiro de Silveira', ' Dias da Praia', ' Caldeira de Silveira', ' Melo Paulista', ' Costa de Ramos', ' Campos de Ferreira', ' Cardoso de Pinto', ' Ramos Grande', ' Nogueira de Monteiro', ' Campos de da Costa', ' Rezende de Caldeira', ' Moraes do Campo', ' Peixoto de Pires', ' Araújo de Ramos', ' Cardoso de Carvalho', ' Carvalho de Rodrigues', ' Oliveira de Costela', ' da Costa de Sales', ' Costa de Gonçalves', ' Souza Grande', ' Ramos de Fogaça', ' Nunes dos Dourados', ' Vieira Paulista', ' Moura das Flores', ' Dias de da Paz', ' Martins de Rocha', ' Pereira de Ribeiro', ' da Costa Paulista', ' Ferreira da Mata', ' Nogueira de Jesus', ' Ribeiro de Correia', ' Martins de Azevedo', ' Fernandes dos Dourados', ' Santos de Farias', ' Silveira de Duarte', ' Moraes da Mata', ' Rodrigues da Prata', ' Peixoto de Viana', ' Costa da Mata', ' Castro de Peixoto', ' da Luz da Mata', ' Vieira de Cavalcanti', ' Ribeiro de Pereira', ' Freitas de da Costa', ' Lopes das Pedras', ' Mendes de da Cunha', ' da Paz de Moraes', ' da Cunha de Carvalho', ' da Paz de Correia', ' Lima de da Conceição', ' da Mata de da Rosa', ' Viana de Azevedo', ' Nascimento de Mendes', ' Pinto de Rocha', ' Sales de Rezende', ' da Costa dos Dourados', ' da Mata de Monteiro', ' Silveira de das Neves', ' Ramos de Barbosa', ' da Cunha de Lopes', ' Oliveira de Fernandes', ' Moreira das Flores', ' Pires de Cavalcanti', ' Costela da Prata', ' Moura dos Dourados', ' Freitas de Barbosa', ' da Rocha de da Rosa', ' Araújo de Jesus', ' Cavalcanti Alegre', ' Nascimento do Amparo', ' da Rocha das Flores', ' da Rocha de da Cunha', ' Cardoso de Martins', ' da Rosa de Silveira', ' Ferreira de Nunes', ' Barros de Nunes', ' Viana de Porto', ' Farias de da Luz', ' Lopes de Oliveira', ' da Mata de Moraes', ' Lopes de Cardoso', ' da Rocha de Moreira', ' Campos de Freitas', ' Lopes de Porto', ' da Costa de Barros', ' Sales da Mata', ' Nascimento de Caldeira', ' Duarte de da Cunha', ' Carvalho de Souza', ' Melo de da Conceição', ' Cavalcanti de Minas', ' Cavalcanti de Oliveira', ' Porto Paulista', ' Costa de Costela', ' Santos de Fernandes', ' Barros de da Luz', ' Almeida de Rocha', ' Moreira de Pinto', ' Ramos de Mendes', ' Sales de Novaes', ' Porto da Mata', ' Cavalcanti de Peixoto', ' Alves de Ferreira', ' Melo do Sul', ' Carvalho de Correia', ' Costela de Alves', ' Dias de Lima', ' Moura de da Conceição', ' Nascimento de Minas', ' Farias de Pires', ' Ferreira das Pedras', ' Teixeira de Costela', ' Monteiro de Pinto', ' da Mata de Azevedo', ' Ramos de Santos', ' Oliveira de da Cunha', ' Dias da Prata', ' das Neves de Nunes', ' Novaes de da Conceição', ' Nunes de Rezende', ' Melo de Aragão', ' Silveira Grande', ' Viana do Sul', ' da Cunha de Gonçalves', ' Cardoso de Viana', ' da Costa de Costa', ' Oliveira da Serra', ' Viana de Teixeira', ' Monteiro dos Dourados', ' Jesus de Mendes', ' Carvalho de Silva', ' Castro de Araújo', ' Alves de Rocha', ' Aragão de Caldeira', ' Cunha do Sul', ' Silva do Norte', ' Santos de da Paz', ' Mendes de Pereira', ' Cavalcanti de Monteiro', ' Azevedo de Moreira', ' Pinto de Novaes', ' das Neves da Serra', ' Araújo de Araújo', ' Farias de Pereira', ' Gonçalves de da Cunha', ' Caldeira de da Mata', ' Moreira das Pedras', ' Monteiro de Lopes', ' da Costa de Campos', ' da Rosa Alegre', ' Cunha de Gomes', ' Sales de Peixoto', ' Alves de Vieira', ' Monteiro de da Conceição', ' Silva do Oeste', ' Novaes de da Luz', ' Cavalcanti de Moraes', ' Barros de Moreira', ' da Luz de Carvalho', ' Rezende de Aragão', ' Ribeiro de Peixoto', ' Barros de Gomes', ' Silva Alegre', ' Cavalcanti Grande', ' Pires de Sales', ' Alves Grande', ' Costela de Moreira', ' da Rosa da Prata', ' Mendes de Duarte', ' da Mata de Carvalho', ' Campos de Rodrigues', ' Carvalho de da Costa', ' Silveira da Prata', ' Duarte da Serra', ' Oliveira Alegre', ' Teixeira de Ferreira', ' Ferreira de Castro', ' Novaes de Ramos', ' da Rocha de Minas', ' Campos de Martins', ' Ferreira de Cardoso', ' da Mata das Pedras', ' Nascimento do Sul', ' Castro de Dias', ' Vieira de da Cruz', ' das Neves de Minas', ' da Rosa de Nunes', ' Cardoso de Moraes', ' Almeida de da Paz', ' Lopes de Mendes', ' Cavalcanti Paulista', ' Caldeira de da Costa', ' Fogaça do Norte', ' Cardoso de Gomes', ' Cardoso de Nascimento', ' Teixeira de Ramos', ' da Costa de Rodrigues', ' Lima do Oeste', ' Alves de Costela', ' Pinto de Porto', ' da Mota de Sales', ' Novaes de Costa', ' Mendes das Pedras', ' Pereira de Silveira', ' Peixoto das Flores', ' Pires de Alves', ' Caldeira de da Rosa', ' Viana de Novaes', ' Moreira de da Cunha', ' da Paz de Gomes', ' Viana de Araújo', ' Barros de da Rocha', ' Fernandes das Pedras', ' da Costa da Mata', ' Silveira de Ribeiro', ' Viana das Flores', ' Monteiro de Viana', ' Moraes de da Mota', ' Ramos da Prata', ' da Cruz de das Neves', ' Martins de Gomes', ' Castro de Vieira', ' Lima de Jesus', ' Aragão do Campo', ' Pinto de Gonçalves', ' Gonçalves de Oliveira', ' da Paz de Ramos', ' Ramos Alegre', ' Cunha de da Rosa', ' Moreira de Nascimento', ' Cardoso de Monteiro', ' Fogaça de Costela', ' Santos das Flores', ' Farias de Gomes', ' Rezende Verde', ' Freitas de Gomes', ' Campos de Farias', ' Viana de Souza', ' Rocha de Araújo', ' Barbosa das Pedras', ' Santos de da Mota', ' da Paz do Campo', ' Duarte da Praia', ' Silva de Rodrigues', ' Sales Alegre', ' da Luz do Sul', ' Pereira de Monteiro', ' Fogaça de Caldeira', ' da Cruz de Nascimento', ' Moreira de Almeida', ' Martins do Norte', ' Fogaça de Barros', ' da Rocha de da Paz', ' Lima de Correia', ' Oliveira de Correia', ' Viana de Ramos', ' Moura de Moreira', ' Rocha de Ramos', ' Sales do Galho', ' Barbosa de Duarte', ' Gonçalves de Ribeiro', ' Oliveira de da Cruz', ' Pinto de Teixeira', ' Costa de da Cruz', ' Lopes de Nascimento', ' da Mata de Peixoto', ' Ramos de da Cruz', ' Gomes Alegre', ' Carvalho de Rocha', ' Teixeira de Lima', ' Nogueira de Silveira', ' Santos da Serra', ' Caldeira da Mata', ' Lima de Martins', ' da Costa de da Conceição', ' da Luz de Nascimento', ' Viana do Norte', ' Cunha do Galho', ' Duarte de Dias', ' Peixoto de Teixeira', ' Azevedo de Nogueira', ' Rocha de Castro', ' da Cunha da Mata', ' Pereira de Novaes', ' Costela Grande', ' Porto de Moura', ' Silva das Pedras', ' Novaes de Campos', ' Vieira de Alves', ' Lima de Oliveira', ' Cardoso de Campos', ' da Rosa de Barros', ' Dias de Aragão', ' Costela Alegre', ' Duarte de Almeida', ' Oliveira de Pereira', ' Costa de Mendes', ' Martins de Nogueira', ' da Cunha de Ramos', ' Azevedo de Costela', ' Gomes de Rezende', ' da Rosa do Galho', ' Duarte de Barbosa', ' Jesus de da Costa', ' da Mota de Araújo', ' Santos de Dias', ' Carvalho Grande', ' Moura de Nunes', ' Castro de Moreira', ' Barros do Campo', ' Azevedo de da Mata', ' Aragão de Vieira', ' Alves de Campos', ' Santos de da Rosa', ' Almeida de Barbosa', ' Cavalcanti de Moreira', ' da Cruz de Vieira', ' Freitas de Costa', ' Fogaça de Melo', ' Teixeira de Dias', ' Jesus de Rodrigues', ' da Mata de Barbosa', ' Costa do Campo', ' Novaes de Dias', ' Nogueira de Moura', ' da Rocha da Praia', ' Barbosa da Praia', ' Fernandes de Moraes', ' Alves de Costa', ' Oliveira de da Mota', ' da Luz das Flores', ' da Paz da Mata', ' Campos de Azevedo', ' das Neves de Teixeira', ' Silva de da Costa', ' Silva de Vieira', ' Barbosa de Lopes', ' Farias das Pedras', ' da Luz de Santos', ' Moura da Prata', ' da Costa de Pinto', ' da Rocha de Rocha', ' Moraes de Moraes', ' Teixeira de Mendes', ' Peixoto de Moraes', ' Lima de Costela', ' Almeida de Sales', ' Ribeiro de Nunes', ' Ramos de Monteiro', ' da Conceição de Teixeira', ' Teixeira de Barros', ' Jesus de Barros', ' Pereira de Minas', ' Martins de Minas', ' Rocha de Fogaça', ' Teixeira de Fernandes', ' Vieira de Vieira', ' da Rocha da Serra', ' Correia de Porto', ' Azevedo de Caldeira', ' Martins de Farias', ' Porto de Nunes', ' Ribeiro do Oeste', ' Barbosa de Rezende', ' Porto de da Cunha', ' Azevedo de Azevedo', ' Fogaça de Pires', ' Rezende de Castro', ' Gonçalves de Moura', ' Duarte de Souza', ' Sales dos Dourados', ' Alves do Sul', ' da Conceição Paulista']
    


```python
print(list(df['state'].unique()))
```

    ['AM', 'RR', 'PE', 'RJ', 'MT', 'SC', 'PR', 'PB', 'AP', 'SP', 'MG', 'MA', 'AL', 'PI', 'RO', 'AC', 'CE', 'RN', 'MS', 'PA', 'ES', 'SE', 'RS', 'DF', 'BA', 'GO', 'TO']
    


```python
print(list(df['neighborhood'].unique()))
```

    ['Aparecida 7ª Seção', 'Santa Isabel', 'Estrela', 'Confisco', 'Vila Suzana Segunda Seção', 'Vila Maria', 'Braúnas', 'Bacurau', 'Piraja', 'Padre Eustáquio', 'Jardim Guanabara', 'Vila Paris', 'Ipe', 'Jardim Dos Comerciarios', 'Granja Werneck', 'Vila Independencia 1ª Seção', 'São João Batista', 'Monsenhor Messias', 'Céu Azul', 'Dona Clara', 'Primeiro De Maio', 'Túnel De Ibirité', 'Nossa Senhora De Fátima', 'Santa Rita De Cássia', 'Universo', 'Flavio De Oliveira', 'Dom Cabral', 'Estoril', 'Cenaculo', 'Copacabana', 'Savassi', 'Vila Madre Gertrudes 3ª Seção', 'Conjunto Floramar', 'Conjunto Capitão Eduardo', 'Vila Ouro Minas', 'Conjunto Paulo Vi', 'Vila Batik', 'Pompéia', 'Senhor Dos Passos', 'Ribeiro De Abreu', 'Ventosa', 'Candelaria', 'Buritis', 'Venda Nova', 'Vila Nova Paraíso', 'Santa Rita', 'Glória', 'Cachoeirinha', 'Vila Real 2ª Seção', 'Conjunto Bonsucesso', 'Universitário', 'Grota', 'Marajó', 'Inconfidência', 'Vila São João Batista', 'Boa União 1ª Seção', 'Vila Independencia 3ª Seção', 'São Jorge 3ª Seção', 'Boa Vista', 'Tupi A', 'Vila União', 'Barro Preto', 'Vila Suzana Primeira Seção', 'Cardoso', 'Ipiranga', 'Vila Puc', 'Vila Nova Cachoeirinha 3ª Seção', 'Corumbiara', 'Caetano Furquim', 'Araguaia', 'Vila Bandeirantes', 'Santa Lúcia', 'Vila Nova Gameleira 3ª Seção', 'Bonfim', 'Camargos', 'Vila São Gabriel Jacui', 'Santa Branca', 'São Jorge 2ª Seção', 'Satelite', 'Salgado Filho', 'Horto Florestal', 'Morro Dos Macacos', 'Vila Tirol', 'Europa', 'Vila São Paulo', 'Silveira', 'Vila Jardim Leblon', 'Mala E Cuia', 'Esplanada', 'Vila Antena', 'Vila Dos Anjos', 'Vila Vista Alegre', 'Jardim Atlântico', 'Vitoria', 'Antonio Ribeiro De Abreu 1ª Seção', 'São José', 'São Bento', 'Nova America', 'Vila Ipiranga', 'Coqueiros', 'Vila Calafate', 'Floresta', 'Vila Mantiqueira', 'Vila De Sá', 'Lajedo', 'Vila Piratininga', 'Vila Paquetá', 'Carlos Prates', 'Goiania', 'Nova Cachoeirinha', 'Boa Viagem', 'Vila Pinho', 'Conjunto Califórnia Ii', 'Nova Vista', 'Vila Nova Dos Milionarios', 'Graça', 'Jaqueline', 'Boa Esperança', 'Andiroba', 'Vila Primeiro De Maio', 'Vila Esplanada', 'Itapoa', 'Pirineus', 'Penha', 'Custodinha', 'Liberdade', 'Vila Nova Gameleira 1ª Seção', 'Dom Joaquim', 'Aparecida', 'Ápia', 'Jardinópolis', 'Maria Goretti', 'Serra Do Curral', 'Vila São Geraldo', 'Vila Da Luz', 'Embaúbas', 'Vitoria Da Conquista', 'Atila De Paiva', 'Planalto', 'Nossa Senhora Da Aparecida', 'Vila Paraíso', 'Ouro Minas', 'Vila Maloca', 'Ernesto Nascimento', 'Barão Homem De Melo 2ª Seção', 'Buraco Quente', 'Laranjeiras', 'Nova Pampulha', 'Casa Branca', 'Vila Cloris', 'Vila São Francisco', 'Guarani', 'Lindéia', 'Vila São Gabriel', 'Vila Betânia', 'Coração Eucarístico', 'Acaiaca', 'Vila Independencia 2ª Seção', 'Novo Aarão Reis', 'São Francisco Das Chagas', 'Santa Margarida', 'Vila Nova Cachoeirinha 2ª Seção', 'São Lucas', 'Barroca', 'Madri', 'São Sebastião', 'Concórdia', 'Santa Sofia', 'Milionario', 'Nova Floresta', 'Vila Coqueiral', 'São Bernardo', 'Dom Bosco', 'Zilah Sposito', 'Conjunto Taquaril', 'Betânia', 'João Alfredo', 'Taquaril', 'Vila Madre Gertrudes 2ª Seção', 'Parque São José', 'Solar Do Barreiro', 'Conjunto Serra Verde', 'Novo Ouro Preto', 'Castanheira', 'Olaria', 'Cônego Pinheiro 1ª Seção', 'Cidade Jardim Taquaril', 'Mirtes', 'Pindorama', 'Novo Tupi', 'Vista Do Sol', 'Vila Cemig', 'Camponesa 2ª Seção', 'Santa Efigênia', 'Bernadete', 'Vila Madre Gertrudes 4ª Seção', 'Ermelinda', 'Conjunto Santa Maria', 'Luxemburgo', 'São Luiz', 'Teixeira Dias', 'Califórnia', 'Jonas Veiga', 'Floramar', 'Caiçara - Adelaide', 'Cônego Pinheiro 2ª Seção', 'Ouro Preto', 'Dom Silverio', 'Pedreira Padro Lopes', 'Havaí', "Vila Olhos D'água", 'Monte Azul', 'Sagrada Família', 'Petropolis', 'São Paulo', 'Camponesa 1ª Seção', 'Calafate', 'Belvedere', 'Jardim Leblon', 'Barão Homem De Melo 3ª Seção', 'Novo Glória', 'Álvaro Camargos', 'Lagoa', 'Bandeirantes', 'Tiradentes', 'Conjunto Lagoa', 'São Pedro', 'Das Industrias I', 'Vila Nova Gameleira 2ª Seção', 'Vila São Dimas', 'Belmonte', 'Vila Santo Antônio Barroquinha', 'Aguas Claras', 'Vila Barragem Santa Lúcia', 'Vila São Rafael', 'Jardim Vitoria', 'Distrito Industrial Do Jatoba', 'Santa Amelia', 'Paulo Vi', 'Varzea Da Palma', 'Granja De Freitas', 'Xodo-Marize', 'Mangueiras', 'Cruzeiro', 'Cidade Jardim', 'Vila Minaslandia', 'Jaraguá', 'Flamengo', 'Maria Helena', 'Vila Sumaré', 'Beira Linha', 'Vila Real 1ª Seção', 'Mantiqueira', 'São Geraldo', 'Minaslandia', 'Barão Homem De Melo 1ª Seção', 'Cinquentenário', 'Caiçaras', 'Diamante', 'Guaratã', 'Solimoes', 'Palmeiras', 'Marilandia', 'Apolonia', 'Nova Esperança', 'Bom Jesus', 'Alta Tensão 1ª Seção', 'Nossa Senhora Da Conceição', 'Vila Petropolis', 'Jardim São José', 'Vila Aeroporto Jaraguá', 'Pilar', 'São Marcos', 'Vista Alegre', 'Vila Atila De Paiva', 'Sport Club', 'Mariano De Abreu', 'Vila Novo São Lucas', 'Vila Jardim São José', 'Vila Sesc', 'Santo André', 'Cabana Do Pai Tomás', 'Vila Da Amizade', 'Maria Virgínia', 'Nossa Senhora Do Rosário', 'Conjunto Califórnia I', 'Sion', 'Bonsucesso', 'Prado', 'Santa Inês', 'Santa Terezinha', 'Vila Da Ária', 'Piratininga', 'Conjunto Novo Dom Bosco', 'Canadá', 'Vila Jardim Alvorada', 'Capitão Eduardo', 'Santa Cecilia', 'Vale Do Jatoba', 'Conjunto Jardim Filadélfia', 'Lourdes', 'São Tomaz', 'São Vicente', 'Heliopolis', 'Baleia', 'Vila Das Oliveiras', 'Centro', 'Minas Brasil', 'São Jorge 1ª Seção', 'Alto Barroca', 'Tirol', 'Jardim Montanhês', 'Vila Havaí', 'Santa Monica', 'Marmiteiros', 'Lorena', 'Campo Alegre', 'Ambrosina', 'Marçola', 'Ademar Maldonado', 'Alto Das Antenas', 'Itatiaia', 'Vila Satélite', 'Vila Antena Montanhês', 'Marieta 1ª Seção', 'Funcionários', 'Trevo', 'Vila Boa Vista', 'Renascença', 'Mirante', 'Tupi B', 'Xangri-Lá', 'Chácara Leonina', 'Unidas', 'Itaipu', 'Outro', 'Barreiro', 'Tres Marias', 'Delta', 'Novo São Lucas', 'Santa Helena', 'Grotinha', 'Conjunto Providencia', 'Vila Nossa Senhora Do Rosário', 'Vila Santo Antônio', 'Nova Suíça', 'Gutierrez', 'Nova Cintra', 'Vila Inestan', 'Pongelupe', 'Vila Da Paz', 'João Paulo Ii', 'Jardim Do Vale', 'Jardim América', 'Vila Califórnia', 'Bairro Das Indústrias Ii', 'Vila Santa Monica 2ª Seção', 'Vila Oeste', 'Lagoinha Leblon', 'Cidade Nova', 'São Gabriel', 'Grajaú', 'Biquinhas', 'Vila Canto Do Sabiá', 'Aarão Reis', 'Coração De Jesus', 'Monte São José', 'Garças', 'Vila Fumec', 'Conjunto Jatoba', 'Conjunto Celso Machado', 'Comiteco', 'Mangabeiras', 'Vila Copacabana', 'Vila Santa Rosa', 'Maria Tereza', 'Parque São Pedro', 'Vila Nova', 'Nossa Senhora Aparecida', 'Alpes', 'Indaiá', 'Jatobá', 'Etelvina Carneiro', 'Vila Engenho Nogueira', 'Palmares', 'Vila Mangueiras', 'Leonina', 'Eymard', 'Alta Tensão 2ª Seção', 'Manacas', 'Alto Dos Pinheiros', 'Marieta 3ª Seção', 'Castelo', 'Pousada Santo Antonio', 'Miramar', 'Vila Copasa', 'Vila Pilar', 'Virgínia', 'Independência', 'União', 'São Salvador', 'Saudade', 'Vila Madre Gertrudes 1ª Seção', 'Santa Tereza', 'Vila Trinta E Um De Março', 'Alípio De Melo', 'Carmo', 'Brasil Industrial', 'Pantanal', 'Conjunto Minas Caixa', 'Vila Formosa', 'Boa União 2ª Seção', 'Minas Caixa', 'Santa Cruz', 'São Benedito', 'Fazendinha', 'João Pinheiro', 'Lagoinha', 'Vera Cruz', 'São Damião', 'Cdi Jatoba', 'Canaa', 'Paraíso', 'Aeroporto', 'Madre Gertrudes', 'Fernão Dias', 'Vila Piratininga Venda Nova', 'Maravilha', 'Santa Maria', 'Jardim Alvorada', 'Santo Antônio', 'Novo Das Industrias', 'Urca', 'Mineirão', 'Alto Vera Cruz', 'Vila Rica', 'Anchieta', 'Santa Rosa', 'Vila Nova Cachoeirinha 1ª Seção', 'Acaba Mundo', 'São Francisco', 'Colégio Batista', 'Nova Granada', 'Vila Santa Monica 1ª Seção', 'Providencia', 'São Cristóvão', 'Rio Branco', 'Flavio Marques Lisboa', 'Suzana', 'Jardim Felicidade', 'Santo Agostinho', 'Novo Santa Cecilia', 'Serrano', 'Serra Verde', 'São João', 'Estrela Do Oriente', "Olhos D'água", 'Engenho Nogueira', 'Frei Leopoldo', 'Serra', 'Horto', 'Santana Do Cafezal', 'Beija Flor', 'Esperança', 'São Gonçalo', 'Nazare', 'Vila Ecológica', 'Marieta 2ª Seção', 'Vila Jardim Montanhes', 'Alto Caiçaras', 'Conjunto São Francisco De Assis', 'Vila Aeroporto', 'Leticia', 'Vila Do Pombal', 'Oeste', 'Mariquinhas', 'Paquetá', 'Bela Vitoria', 'Juliana', 'Nova Gameleira', 'Gameleira', 'Pindura Saia']
    


```python
df[['average_ticket','items_quantity', 'all_revenue', 'all_orders', 'recency']].describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>average_ticket</th>
      <th>items_quantity</th>
      <th>all_revenue</th>
      <th>all_orders</th>
      <th>recency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>10000.000000</td>
      <td>10000.00000</td>
      <td>10000.000000</td>
      <td>10000.000000</td>
      <td>10000.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>216.894709</td>
      <td>8.49980</td>
      <td>1174.888603</td>
      <td>5.415400</td>
      <td>67.192900</td>
    </tr>
    <tr>
      <th>std</th>
      <td>22.757213</td>
      <td>3.02604</td>
      <td>763.141973</td>
      <td>3.457577</td>
      <td>175.723276</td>
    </tr>
    <tr>
      <th>min</th>
      <td>131.378672</td>
      <td>1.00000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>201.398851</td>
      <td>6.00000</td>
      <td>494.873564</td>
      <td>2.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>217.019478</td>
      <td>8.00000</td>
      <td>1172.751918</td>
      <td>5.000000</td>
      <td>35.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>232.455042</td>
      <td>11.00000</td>
      <td>1798.475045</td>
      <td>8.000000</td>
      <td>39.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>303.386848</td>
      <td>19.00000</td>
      <td>3225.654163</td>
      <td>11.000000</td>
      <td>1820.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['marketing_source'].unique()
```




    array(['crm', 'organic_search', 'direct', 'paid_search', 'none',
           'telegram_whatsapp'], dtype=object)



# Análise exploratória

### Criação de colunas para realizar as análises


```python
# criando colunas para acrescentar a informação de dia da semana e mês de cancelamento
def weekday_month_day_year(df, coluna_data,weekday,month,day,year):
    # dia da semana
    df[weekday] = pd.to_datetime(df[coluna_data]).dt.day_name()
    #  mês
    df[month] = pd.to_datetime(df[coluna_data]).dt.month_name()
    # dia do mês
    df[day] = pd.to_datetime(df[coluna_data]).dt.day
    # ano
    df[year] = pd.to_datetime(df[coluna_data]).dt.year
    return df
```


```python
# criando as colunas pela função para criação da assinatura
df = weekday_month_day_year(df,'deleted_at','deleted_at_weekday','deleted_at_month','deleted_at_day','deleted_at_year')
```


```python
# criando as colunas pela função para exclusão  da assinatura
df = weekday_month_day_year(df,'created_at','created_at_weekday','created_at_month','created_at_day','created_at_year')
```


```python
# criando uma coluna de idade
df['customer_age'] = [relativedelta(pd.to_datetime('2021-02-19',format='%Y-%m-%d'),date).years for date in df['birth_date']]
```


```python
# tempo que o cliente demorou pra cancelar depois da última compra
df['recency_subs'] = (df['deleted_at'] - df['last_date_purchase']).dt.days
```


```python
# coluna que informa quanto tempo o cliente ficou no plano de assinatura
def calculate_subscription_time(row):
    if pd.isnull(row['deleted_at']):
        a = (pd.to_datetime('2021-02-19',format='%Y-%m-%d') - row['created_at']).days
        return round(a/30)
    else:
        a = (row['deleted_at'] - row['created_at']).days
        return round(a/30)
# aplica a função 
df['subs_time'] = df.apply(calculate_subscription_time, axis=1)
```


```python
# criando lista de faixa etária, como a pessoa com mais idade tem 81 colocamos até 90 a última faixa
age_group = [20,30,40,50,60,70,81]
df['age_group'] = pd.cut(df['customer_age'], bins=age_group)
# trasnformando a coluna de faixa etaria em string
df['age_group'] = df['age_group'].astype(str)
```


```python
# criando coluna de região para analisar posteriormente
region = {
    'Norte': ['AM', 'AC', 'AP', 'PA', 'RO', 'RR', 'TO'],
    'Nordeste': ['AL', 'BA', 'CE', 'MA', 'PB', 'PE', 'PI', 'RN', 'SE'],
    'Centro-Oeste': ['DF', 'GO', 'MT', 'MS'],
    'Sudeste': ['ES', 'MG', 'RJ', 'SP'],
    'Sul': ['PR', 'RS', 'SC']
}
# adicionando a coluna de região ao dataframe
df['region'] = df['state'].map({state: region for region, states in region.items() for state in states})
```


```python
# criar variavel de 0 e 1 pro churn
df['churn'] = ['yes' if i[list(df.columns).index('status')]=='canceled' else 'no' for i in df.itertuples(index=False)]
```

Mapeamento da análise  
- Em que momento do mês os clientes costumam cancelar? Há um padrão neste comportamento? 
- Há um padrão no dia da semana que os clientes costumam cancelar? Fim de semana ou dia de semana? 
- Como é o comportamento do cancelamento ao longo dos meses? Há um padrão comparando os diferentes anos? 
- Quanto tempo o cliente que deu churn ficou com a assinatura ativa? 
- Os clientes costumam ficar muito tempo sem comprar antes de dar o churn? 
- Alguma faixa de idade o cancelamento é maior? 
- Quanto tempo cada faixa etária fica com a assinatura? 
- Clientes vindos de algum canal de marketing tem um índice de churn consideravelmente maior que os outros? 
- Os clientes têm comportamento diferente dependendo de sua região de origem?
- A média maior de gasto por pedido influencia no churn? 
- Quem cancela mais, quem tem média de itens comprados maior ou menor? 
- As pessoas que têm um total de receita gasto maior cancelam mais? Ou as que têm total de receita gasto menor? 
- As pessoas que têm mais pedidos ou menos que mais cancelam? 
 

### Analise do churn no geral


```python
# taxa de churn
print('A taxa de churn global da empresa é de',round(100*df.query('churn=="yes"').shape[0]/df.shape[0],2),"%.")
```

    A taxa de churn global da empresa é de 5.05 %.
    


```python
fig, axs = plt.subplots(1, 2, figsize=(12, 4))

# plotar o primeiro gráfico com as contagens de cada status
counts = df['status'].value_counts()
axs[0].bar(counts.index, counts.values, color=['mediumvioletred','plum','purple'])
axs[0].set_xlabel('Status')
axs[0].set_title('Quantidade de clientes ativos, pausados e churn')
axs[0].spines['top'].set_visible(False) # remove a linha superior do gráfico
axs[0].spines['right'].set_visible(False) # remove a linha direita do gráfico
axs[0].spines['left'].set_visible(False) # remove a linha esquerda do gráfico
axs[0].set_yticks([])
for i, v in enumerate(counts.values):
    axs[0].text(i, v, str(v), ha='center', va='bottom')

# plotar o segundo gráfico com a soma das contagens de 'active' e 'paused' e 'canceled'
counts = df.groupby('churn')['id'].count()
axs[1].bar(['não churn', 'churn'], counts.values, color=['violet','purple'])
axs[1].set_xlabel('Status')
axs[1].set_title('Quantidade de clientes churn e não churn')
axs[1].spines['top'].set_visible(False) # remove a linha superior do gráfico
axs[1].spines['right'].set_visible(False) # remove a linha direita do gráfico
axs[1].spines['left'].set_visible(False) # remove a linha esquerda do gráfico
axs[1].set_yticks([])
for i, v in enumerate(counts.values):
    axs[1].text(i, v, str(v), ha='center', va='bottom')

fig.tight_layout()
plt.show()
```


    
![png](README_files/README_36_0.png)
    



```python
fig, axes = plt.subplots(nrows=3, ncols=2, figsize=(12, 12))

sns.boxplot(x='churn', y='all_revenue', data=df, ax=axes[0, 0],  palette="rocket")
axes[0, 0].set_title('Receita total',fontsize=10)

sns.boxplot(x='churn', y='average_ticket', data=df, ax=axes[0, 1], palette="rocket")
axes[0, 1].set_title('Média de gasto por pedido',fontsize=10)

sns.boxplot(x='churn', y='all_orders', data=df, ax=axes[1, 0],  palette="rocket")
axes[1, 0].set_title('Número de pedidos',fontsize=10)

sns.boxplot(x='churn', y='items_quantity', data=df, ax=axes[1, 1], palette="rocket")
axes[1, 1].set_title('Média de itens na assinatura',fontsize=10)

sns.boxplot(x='churn', y='subs_time', data=df, ax=axes[2, 0], palette="rocket")
axes[2, 0].set_title('Tempo de assinatura',fontsize=10)

sns.boxplot(x='churn', y='recency', data=df, ax=axes[2, 1], palette="rocket")
axes[2, 1].set_title('Tempo desde a última compra',fontsize=10)

plt.subplots_adjust(left=0, bottom=0.1, right=0.9, top=0.9, wspace=0.15, hspace=0.45)
plt.show()
```


    
![png](README_files/README_37_0.png)
    



```python
# para enxergar melhor os valores do último boxplot
df.groupby('churn')['recency'].describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>churn</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>no</th>
      <td>9495.0</td>
      <td>34.548605</td>
      <td>6.049331</td>
      <td>11.0</td>
      <td>30.0</td>
      <td>35.0</td>
      <td>39.0</td>
      <td>56.0</td>
    </tr>
    <tr>
      <th>yes</th>
      <td>505.0</td>
      <td>680.970297</td>
      <td>463.006656</td>
      <td>1.0</td>
      <td>278.0</td>
      <td>600.0</td>
      <td>1031.0</td>
      <td>1820.0</td>
    </tr>
  </tbody>
</table>
</div>



#### Resumo
As variáveis acima plotadas para clientes que deram churn e que não deram churn tem comportamento bastante parecido. Ou seja, clientes que cancelaram a assinatura gastaram e compraram quantidades muito próximas àqueles que não cancelaram, não havendo uma diferença aparente. O problema do cancelamento, aparentemente, não é uma falta de uso da assinatura, ou alguma insatisfação com preços. Apenas no tempo de assinatura que os clientes que cancelaram tem uma mediana menor, porém isso pode ser devido a base pequena.


```python
fig, ax = plt.subplots(figsize=(12, 2)) 
sns.boxplot(x=df['recency_subs'], palette="rocket", ax=ax)
ax.set_title('Tempo que o cliente demorou para cancelar depois da última compra',fontsize=10)


# Calcula os valores dos quartis e da média
q1, mediana, q3 = np.percentile(df.query('churn=="yes"')['recency_subs'], [25, 50, 75])

# definindo as posições das anotações
x_pos = 1.05
y_pos_mediana = ax.get_ylim()[0] + (ax.get_ylim()[1] - ax.get_ylim()[0]) * -0.6
y_pos_q1 = ax.get_ylim()[0] + (ax.get_ylim()[1] - ax.get_ylim()[0]) * -0.35
y_pos_q3 = ax.get_ylim()[0] + (ax.get_ylim()[1] - ax.get_ylim()[0]) * -0.85

# adicionando as anotações
ax.text(x_pos, y_pos_mediana, f'Mediana: {mediana:.2f}', verticalalignment='bottom', horizontalalignment='left')
ax.text(x_pos, y_pos_q1, f'Q1: {q1:.2f}', verticalalignment='bottom', horizontalalignment='left')
ax.text(x_pos, y_pos_q3, f'Q3: {q3:.2f}', verticalalignment='bottom', horizontalalignment='left')
plt.show()
```


    
![png](README_files/README_40_0.png)
    


Acima, vemos que metade dos clientes ficam até 135 dias (4 meses e meio) sem comprar antes de fazer o cancelamento.  Há a possibilidade da interrupção da assinatura por falsa de uso, já que os clientes esperam um tempo considerável para cancelar depois de ter feito a última compra pela assintaura. Neste caso, poderiam ser tomadas ações que incentivassem a compra, como cupons, descontos progressivos, programas de pontos e coisas do tipo.


```python
df1 = df[['created_at','deleted_at']]

# Cria uma coluna com o mês e o ano de assinatura e cancelamento
df1['signup_month'] = df1['created_at'].dt.to_period('M')
df1['cancel_month'] = df1['deleted_at'].dt.to_period('M')

# Agrupa os dados pelo mês e ano de assinatura e de cancelamento
signup_counts = df1.groupby('signup_month').size().reset_index(name='signup_count')
cancel_counts = df1.groupby('cancel_month').size().reset_index(name='cancel_count')

# Junta as contagens de assinaturas e cancelamentos em um único DataFrame e calcula o % de churn
result = pd.merge(signup_counts, cancel_counts, how='outer', left_on='signup_month', right_on='cancel_month').fillna(0)
result['churn_rate'] = result['cancel_count'] / result['signup_count'] * 100

# Plota o gráfico de percentual de churn ao longo dos meses
ax = result.plot(x='signup_month', y='churn_rate', kind='line', figsize=(9, 6))
ax.set_ylabel('Percentual de churn em relação a novos clientes')
ax.set_xlabel(None)
plt.title('Percentual de clientes que saíram em relação aos que entraram no mês')
plt.show()
```


    
![png](README_files/README_42_0.png)
    


No gráfico acima se observa uma tendência preocupante, onde vemos que cada vez mais aumenta o percentual de clientes que deram churn em razão dos novos clientes. Por exemplo, em fevereiro de 2021 tivemos 108 novos clientes e 26 churns na nossa base de cliente. A preocupação é que, chegue em um momento em que entre a mesma quantidade de clientes novos que saiam os antigos por conta do churn.

### Duração de assinatura e churn


```python
# criando dataframe com meses de assinatura e quantidade de cada status
table_subs_time = df.pivot_table(values = 'id', index = ['subs_time'], columns = 'status',
    aggfunc='count').fillna(0).reset_index().rename_axis(index=None, columns= None)
# transformando float em int
columns_int = ['active','canceled','paused']
table_subs_time[columns_int] = table_subs_time[columns_int].apply(lambda x: x.astype(int))
```


```python
# coluna de acumulado de quantidade de churn
table_subs_time['cumulative_canceled'] = table_subs_time['canceled'].cumsum()
```


```python
# cortando o dataframe onde não soma mais o acumulado do churn
table_subs_time = table_subs_time.head(60)
# coluna de acumulado percentual
table_subs_time['cumulative_canceled_perc'] = round(100*
                            table_subs_time['cumulative_canceled'] / table_subs_time['cumulative_canceled'].max(),2)
```


```python
# plotar gráfico de dispersão
fig, ax = plt.subplots(figsize=(9, 6)) 
ax.scatter(table_subs_time['subs_time'], table_subs_time['cumulative_canceled_perc'], s=50, alpha=0.5, color='mediumvioletred') # tamanho, transparência e cor dos pontos
ax.set_xlabel('Meses de assinatura', fontsize=11) 
ax.set_ylabel('Churn acumulado', fontsize=11) 
ax.set_xlim(0, 70)
ax.set_ylim(0, 110) 
ax.spines['top'].set_visible(False) # remove a linha superior do gráfico
ax.spines['right'].set_visible(False) # remove a linha direita do gráfico
plt.title("Churn acumulado em percentual ao longo dos meses de assinatura",fontsize=13)

# adicionar anotação no gráfico
x_pos = table_subs_time['subs_time'].iloc[-1] + 0.3 # posição x da seta (ligeramente à direita do último ponto)
y_pos = table_subs_time['cumulative_canceled_perc'].iloc[-1] # posição y da seta (no valor total de churn)
ax.annotate(f'Total de churns: 505', xy=(x_pos, y_pos), xytext=(10, -30), textcoords='offset points', fontsize=9,
            arrowprops=dict(facecolor='black', arrowstyle='->'))
plt.show()
```


    
![png](README_files/README_48_0.png)
    



```python
table_subs_time.tail(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>subs_time</th>
      <th>active</th>
      <th>canceled</th>
      <th>paused</th>
      <th>cumulative_canceled</th>
      <th>cumulative_canceled_perc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>55</th>
      <td>55</td>
      <td>136</td>
      <td>0</td>
      <td>16</td>
      <td>500</td>
      <td>99.01</td>
    </tr>
    <tr>
      <th>56</th>
      <td>56</td>
      <td>152</td>
      <td>2</td>
      <td>13</td>
      <td>502</td>
      <td>99.41</td>
    </tr>
    <tr>
      <th>57</th>
      <td>57</td>
      <td>157</td>
      <td>2</td>
      <td>29</td>
      <td>504</td>
      <td>99.80</td>
    </tr>
    <tr>
      <th>58</th>
      <td>58</td>
      <td>159</td>
      <td>0</td>
      <td>17</td>
      <td>504</td>
      <td>99.80</td>
    </tr>
    <tr>
      <th>59</th>
      <td>59</td>
      <td>119</td>
      <td>1</td>
      <td>17</td>
      <td>505</td>
      <td>100.00</td>
    </tr>
  </tbody>
</table>
</div>



#### Resumo
A partir do gráfico plotado acima, vemos mais claramente que metade dos clientes - aproximadamente - costumam cancelar a assinatura com até 10 meses de utilização do serviço.  Precisamos entender o que faz o cliente desistir do serviço com tão pouco tempo de uso. 

### Idade


```python
# criar df agrupando por idade e somando clientes total e clientes que cancelaram
table_customer_age = df.groupby('age_group').agg(**{
    'total':pd.NamedAgg(column='id',aggfunc='count'),
    'deleted_at_total':pd.NamedAgg(column='deleted_at',aggfunc='count'),
    'subs_time':pd.NamedAgg(column='subs_time',aggfunc='median'),
    'average_ticket':pd.NamedAgg(column='average_ticket',aggfunc='median'),
    'items_quantity':pd.NamedAgg(column='items_quantity',aggfunc='median'),
    'all_revenue':pd.NamedAgg(column='all_revenue',aggfunc='median'),
    'all_orders':pd.NamedAgg(column='all_orders',aggfunc='median'),
    'recency':pd.NamedAgg(column='recency',aggfunc='median')
    })
table_customer_age['perc_deleted_at'] = round(100*table_customer_age['deleted_at_total']/table_customer_age['total'],2)
```

*Observação: Decidi utilizar como parâmetro as medianas dos valores, pois elas representam melhor o comportamento de cada variável. A média acaba sendo bastante afetada por valores extremos.*  


```python
table_customer_age.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total</th>
      <th>deleted_at_total</th>
      <th>subs_time</th>
      <th>average_ticket</th>
      <th>items_quantity</th>
      <th>all_revenue</th>
      <th>all_orders</th>
      <th>recency</th>
      <th>perc_deleted_at</th>
    </tr>
    <tr>
      <th>age_group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>(20, 30]</th>
      <td>1046</td>
      <td>64</td>
      <td>29.0</td>
      <td>217.368011</td>
      <td>8.0</td>
      <td>1201.966672</td>
      <td>6.0</td>
      <td>35.0</td>
      <td>6.12</td>
    </tr>
    <tr>
      <th>(30, 40]</th>
      <td>1775</td>
      <td>74</td>
      <td>29.0</td>
      <td>216.753089</td>
      <td>8.0</td>
      <td>1141.867197</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>4.17</td>
    </tr>
    <tr>
      <th>(40, 50]</th>
      <td>1823</td>
      <td>88</td>
      <td>30.0</td>
      <td>216.650056</td>
      <td>8.0</td>
      <td>1179.250679</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>4.83</td>
    </tr>
    <tr>
      <th>(50, 60]</th>
      <td>1839</td>
      <td>100</td>
      <td>30.0</td>
      <td>217.331461</td>
      <td>9.0</td>
      <td>1174.634236</td>
      <td>6.0</td>
      <td>35.0</td>
      <td>5.44</td>
    </tr>
    <tr>
      <th>(60, 70]</th>
      <td>1763</td>
      <td>91</td>
      <td>29.0</td>
      <td>217.147497</td>
      <td>9.0</td>
      <td>1175.125174</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>5.16</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig,ax= plt.subplots()
table_customer_age[['perc_deleted_at']].plot(
    kind="bar", ax=ax, width=0.9, color='mediumvioletred',figsize=(9,6))
plt.title('Percentual de cancelamento por faixa etária do cliente', fontsize=11) 
plt.ylabel('percentual', labelpad=10)
plt.xlabel('faixa etária do cliente', labelpad=10)
ax.set_xticklabels(ax.get_xticklabels(), rotation=0)
plt.show()
```


    
![png](README_files/README_55_0.png)
    



```python
# criando tabela de percentual de faixa etaria no cancelamneto
aux_1 = df.query('churn=="yes"')['age_group'].value_counts(normalize=True).to_frame().reset_index()
aux_1['proportion'] = round(100*aux_1['proportion'],2)
# criando tabela de percentual de faixa etaria no total
aux_2 = df['age_group'].value_counts(normalize=True).to_frame().reset_index()
aux_2['proportion'] = round(100*aux_2['proportion'],2)
# plotando o merge destas tabelas para comparação
aux_3 = aux_2.merge(aux_1,how='inner',on='age_group').rename(columns={'proportion_x':'perc_customers',
                                                              'proportion_y':'perc_customers_deleted'})
aux_3['dif'] = aux_3['perc_customers_deleted']-aux_3['perc_customers']
display_side_by_side( aux_3.rename(columns={'age_group':'faixa etária','perc_customers':'percentual de clientes',
                                            'perc_customers_deleted':'percentual de clientes churn','dif':'diferença de percentual'}),
                     titles=['Percentual de clientes em cada faixa etária na nossa base de clientes e no cancelamento'])
```


<th style="text-align:center"><td style="vertical-align:top"><h3>Percentual de clientes em cada faixa etária na nossa base de clientes e no cancelamento</h3><table style="display:inline" border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>faixa etária</th>
      <th>percentual de clientes</th>
      <th>percentual de clientes churn</th>
      <th>diferença de percentual</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(50, 60]</td>
      <td>18.39</td>
      <td>19.80</td>
      <td>1.41</td>
    </tr>
    <tr>
      <th>1</th>
      <td>(40, 50]</td>
      <td>18.23</td>
      <td>17.43</td>
      <td>-0.80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>(30, 40]</td>
      <td>17.75</td>
      <td>14.65</td>
      <td>-3.10</td>
    </tr>
    <tr>
      <th>3</th>
      <td>(60, 70]</td>
      <td>17.63</td>
      <td>18.02</td>
      <td>0.39</td>
    </tr>
    <tr>
      <th>4</th>
      <td>(70, 81]</td>
      <td>17.54</td>
      <td>17.43</td>
      <td>-0.11</td>
    </tr>
    <tr>
      <th>5</th>
      <td>(20, 30]</td>
      <td>10.46</td>
      <td>12.67</td>
      <td>2.21</td>
    </tr>
  </tbody>
</table style="display:inline"></td></th>


Através do gráfico conseguimos identificar uma maior taxa de cancelamento dentro da faixa etária mais jovem (clientes de 20 a 30 anos) - considerando que o cliente mais novo da base tem 25 anos.  Ou seja, os clientes mais jovens tendem a cancelar mais (6,12%), comparando por exemplo com os clientes de 30 a 40 anos (4,17%).  
Já analisando a tabela acima, vemos que em relação ao percentual de cancelamento, a faixa etária mais jovem representa o menor percentual em relação ao todo, ja que é também a que está em menor percentual na base.   
Se fossemos fazer uma comparação entre o percentual de clientes de certa faixa etária, com seu percentual de cancelamento, o certo seria que esses dois valores fossem muito próximos. Por exemplo, os clientes de 70 a 81 anos correspondem a 17,54% da base e seu cancelamento corresponde a 17,43% da base, o que faz bastante sentido.   
Porém em alguns casos, essa diferença é grande, o pior caso é dos clientes de 20 a 30 anos, pois eles correspodem a 10,46% da base mas em termos de cancelamento correspodem a 12,67% da base. Essa diferença de 2,21% é a maior, e indica que esses são os clientes alvos para tratar o churn. 

A partir disso, poderíamos escolher dois caminhos:  
1. Focamos ações que minimizem o churn para o público de 20 a 30 anos, como programas de fidelidade, ofertas e etc. Isso reduziria o churn global da empresa. Para isso, seria interessante também entender as razões pelas quais os clientes estão cancelando, para encontrar formas mais eficientes de mante-los. Isso poderia ser resolvido com pesquisas em forma de mensagem de texto através do whatsapp, por exemplo, que tem bastante adesão.      
2. Com o objetivo de aumentar a aquisição de novos clientes - que são mais estáveis, focar em atrair para a assinatura a faixa etária que menos cancela (30 a 40), que tem mais percentual na base do que percentual de cancelamento) através de ações como um atendimento personalizado e adaptado às necessidades específicas desses clientes.  


```python
fig, axes = plt.subplots(nrows=3, ncols=2, figsize=(18, 13))
# a linha de código abaixo tira a coluna age group do index para n dar erro no gráfico
table_customer_age.reset_index(inplace=True)
# lista de tuplas contendo o nome da variável e a coluna correspondente no dataframe
variables = [('Mediana do tempo desde a última compra do cliente', 'recency'),
             ('Mediana da duração da assinatura do cliente', 'subs_time'),
             ('Mediana de gasto por pedido', 'average_ticket'),
             ('Mediana de itens na assinatura', 'items_quantity'),
             ('Mediana do total de receita', 'all_revenue'),
             ('Mediana do número total de pedidos', 'all_orders')]
# loop para gerar cada subplot
for i, ax in enumerate(axes.flat):
    variable_name, variable_column = variables[i]
    ax.bar(table_customer_age['age_group'], table_customer_age[variable_column], color='mediumvioletred')
    ax.set_title(variable_name, fontsize=16)
plt.suptitle("Mediana por faixa etária de outras variáveis dos clientes", fontsize=20)
# ajuste da margem dos subplots
plt.subplots_adjust(left=0, bottom=0.1, right=0.9, top=0.9, wspace=0.08, hspace=0.45)
plt.show()
```


    
![png](README_files/README_58_0.png)
    


**Análise dos gráficos acima**  
Lembrando que a análise do gráfico acima contempla todos os clientes, os que cancelaram e os que não cancelaram.   
Analisando todos os gráficos não notamos diferenças que podem ser consideráveis entre as faixa etárias, ou seja, todas elas tem um comportanento similar em relação a compra e dinheiro gasto.


```python
# criando um dataframe com as mesmas informações do table_customer_age, porém, apenas para clientes que cancelaram
table_customer_age_deleted = df.query('churn=="yes"').groupby('age_group').agg(**{
    'subs_time':pd.NamedAgg(column='subs_time',aggfunc='median'),
    'average_ticket':pd.NamedAgg(column='average_ticket',aggfunc='median'),
    'items_quantity':pd.NamedAgg(column='items_quantity',aggfunc='median'),
    'all_revenue':pd.NamedAgg(column='all_revenue',aggfunc='median'),
    'all_orders':pd.NamedAgg(column='all_orders',aggfunc='median'),
    'recency':pd.NamedAgg(column='recency',aggfunc='median')
    })
```


```python
fig, axes = plt.subplots(nrows=3, ncols=2, figsize=(18, 13))
# a linha de código abaixo tira a coluna age group do index para n dar erro no gráfico
table_customer_age_deleted.reset_index(inplace=True)
# lista de tuplas contendo o nome da variável e a coluna correspondente no dataframe
variables = [('Mediana do tempo desde a última compra do cliente', 'recency'),
             ('Mediana da duração da assinatura do cliente', 'subs_time'),
             ('Mediana de gasto por pedido', 'average_ticket'),
             ('Mediana de itens na assinatura', 'items_quantity'),
             ('Mediana do total de receita', 'all_revenue'),
             ('Mediana do número total de pedidos', 'all_orders')]
# loop para gerar cada subplot
for i, ax in enumerate(axes.flat):
    variable_name, variable_column = variables[i]
    ax.bar(table_customer_age_deleted['age_group'], table_customer_age_deleted[variable_column], color='mediumvioletred')
    ax.set_title(variable_name, fontsize=16)
plt.suptitle("Mediana por faixa etária de outras variáveis dos clientes que cancelaram", fontsize=20)
# ajuste da margem dos subplots
plt.subplots_adjust(left=0, bottom=0.1, right=0.9, top=0.9, wspace=0.08, hspace=0.45)
plt.show()
```


    
![png](README_files/README_61_0.png)
    


**Análise dos gráficos**  
Acima foram plotados os mesmos gráficos que anteriormente, porém, contém apenas os clientes que cancelaram para podermos visualizar o comportamento destes clientes.  
- Aqui vemos algumas diferenças entre as faixas etárias, por exemplo, em relação a duração da assinatura os clientes de 40 a 50 anos se destacam entre os outros, com uma mediana de mais de 14 meses. Seria interessante entender o motivo do cancelamento depois de tanto tempo do uso do serviço.  
- Outra variável curiosa é o total de receita gasto, a faixa etária mais experiente é a que menos gasta no total em comparação com as demais. Sendo uma mediana de total gasto de 800, contra 1126 dos clientes de 30 a 40.

#### Resumo  
Pensando no objetivo da equipe de Assinatura que é reduzir o churn, e pensando no contexto desta primeira análise o público alvo seria aquele entre 20 e 30 anos. Além do seu percentual de cancelamento ser mais acentuado em relação a sua quantidade total, é uma das faixa etárias que fica menos tempo com a assinatura antes de cancelar e uma das que menos gasta com os serviços da PetLove.  

### Canal de marketing


```python
table_marketing_source = df.groupby('marketing_source').agg(**{
    'total':pd.NamedAgg(column='id',aggfunc='count'),
    'deleted_at_total':pd.NamedAgg(column='deleted_at',aggfunc='count'),
    'subs_time':pd.NamedAgg(column='subs_time',aggfunc='median'),
    'average_ticket':pd.NamedAgg(column='average_ticket',aggfunc='median'),
    'items_quantity':pd.NamedAgg(column='items_quantity',aggfunc='median'),
    'all_revenue':pd.NamedAgg(column='all_revenue',aggfunc='median'),
    'all_orders':pd.NamedAgg(column='all_orders',aggfunc='median'),
    'recency':pd.NamedAgg(column='recency',aggfunc='median')
    
})
table_marketing_source['perc_deleted_at'] = round(100*table_marketing_source['deleted_at_total']/table_marketing_source['total'],2)
```


```python
fig,ax= plt.subplots()
table_marketing_source[['perc_deleted_at']].plot(
    kind="bar", ax=ax, width=0.9, color='mediumvioletred',figsize=(10,7))
plt.title('Percentual de cancelamento por fonte de marketing', fontsize=11) 
plt.ylabel('percentual', labelpad=10)
plt.xlabel('fonte de marketing', labelpad=10)
ax.set_xticklabels(ax.get_xticklabels(), rotation=0)
plt.show()
```


    
![png](README_files/README_66_0.png)
    



```python
table_marketing_source
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total</th>
      <th>deleted_at_total</th>
      <th>subs_time</th>
      <th>average_ticket</th>
      <th>items_quantity</th>
      <th>all_revenue</th>
      <th>all_orders</th>
      <th>recency</th>
      <th>perc_deleted_at</th>
    </tr>
    <tr>
      <th>marketing_source</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>crm</th>
      <td>1029</td>
      <td>43</td>
      <td>30.0</td>
      <td>217.100115</td>
      <td>8.0</td>
      <td>1238.979006</td>
      <td>6.0</td>
      <td>35.0</td>
      <td>4.18</td>
    </tr>
    <tr>
      <th>direct</th>
      <td>2149</td>
      <td>96</td>
      <td>30.0</td>
      <td>217.389040</td>
      <td>9.0</td>
      <td>1166.707623</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>4.47</td>
    </tr>
    <tr>
      <th>none</th>
      <td>529</td>
      <td>34</td>
      <td>27.0</td>
      <td>215.420970</td>
      <td>8.0</td>
      <td>1164.638180</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>6.43</td>
    </tr>
    <tr>
      <th>organic_search</th>
      <td>3699</td>
      <td>196</td>
      <td>30.0</td>
      <td>216.998835</td>
      <td>8.0</td>
      <td>1162.977791</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>5.30</td>
    </tr>
    <tr>
      <th>paid_search</th>
      <td>1526</td>
      <td>70</td>
      <td>30.0</td>
      <td>217.277319</td>
      <td>8.0</td>
      <td>1151.177066</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>4.59</td>
    </tr>
    <tr>
      <th>telegram_whatsapp</th>
      <td>1068</td>
      <td>66</td>
      <td>29.0</td>
      <td>216.184098</td>
      <td>9.0</td>
      <td>1189.749338</td>
      <td>6.0</td>
      <td>35.0</td>
      <td>6.18</td>
    </tr>
  </tbody>
</table>
</div>




```python
# criando tabela de percentual de faixa etaria no cancelamneto
aux_4 = df.query('churn=="yes"')['marketing_source'].value_counts(normalize=True).to_frame().reset_index()
aux_4['proportion'] = round(100*aux_4['proportion'],2)
# criando tabela de percentual de faixa etaria no total
aux_5 = df['marketing_source'].value_counts(normalize=True).to_frame().reset_index()
aux_5['proportion'] = round(100*aux_5['proportion'],2)
# plotando o merge destas tabelas para comparação
aux_6 = aux_5.merge(aux_4,how='inner',on='marketing_source').rename(columns={'proportion_x':'perc_customers',
                                                              'proportion_y':'perc_customers_deleted'})
aux_6['dif'] = aux_6['perc_customers_deleted']-aux_6['perc_customers']
display_side_by_side( aux_6.rename(columns={'marketing_source':'canal de marketing','perc_customers':'percentual de clientes',
                    'perc_customers_deleted':'percentual de clientes churn','dif':'diferença de percentual'}),
                     titles=['Percentual de clientes em cada canal de marketing na nossa base de clientes e no cancelamento'])
```


<th style="text-align:center"><td style="vertical-align:top"><h3>Percentual de clientes em cada canal de marketing na nossa base de clientes e no cancelamento</h3><table style="display:inline" border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>canal de marketing</th>
      <th>percentual de clientes</th>
      <th>percentual de clientes churn</th>
      <th>diferença de percentual</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>organic_search</td>
      <td>36.99</td>
      <td>38.81</td>
      <td>1.82</td>
    </tr>
    <tr>
      <th>1</th>
      <td>direct</td>
      <td>21.49</td>
      <td>19.01</td>
      <td>-2.48</td>
    </tr>
    <tr>
      <th>2</th>
      <td>paid_search</td>
      <td>15.26</td>
      <td>13.86</td>
      <td>-1.40</td>
    </tr>
    <tr>
      <th>3</th>
      <td>telegram_whatsapp</td>
      <td>10.68</td>
      <td>13.07</td>
      <td>2.39</td>
    </tr>
    <tr>
      <th>4</th>
      <td>crm</td>
      <td>10.29</td>
      <td>8.51</td>
      <td>-1.78</td>
    </tr>
    <tr>
      <th>5</th>
      <td>none</td>
      <td>5.29</td>
      <td>6.73</td>
      <td>1.44</td>
    </tr>
  </tbody>
</table style="display:inline"></td></th>


#### Resumo
Através do gráfico conseguimos identificar uma maior taxa de cancelamento dentro dos clientes vindos da fonte de marketing None (6,43%) e Telegram/Whatsapp (6,18%). Considerando que a fonte "None" seja desconhecida, não temos muito controle sobre ela. Poderiam ser tomadas ações ao redor desta fonte, buscando uma forma de desvendar sua real origem. Porém, nesta análise iremos considerar o Telegram/Whatsapp como sendo a fonte que os clientes mais cancelam.  
Se formos fazer uma comparação entre o percentual de clientes de certa fonte de marketing, com seu percentual de cancelamento, o Telegram/Whatsapp continua sendo a pior origem neste aspecto. Ele correspode a 10,68% da base de clientes, mas em relação a cancelamento correspode a 13,07%. Portanto, apesar de ser um percentual mais baixo na base, da mais cancelamento do que deveria em tese.  
A partir disso, poderíamos destinar uma pesquisa à estes clientes pelo canal que foram cativados, questionando o motivo de sua saída.  Para reduzir o churn deveríamos focar nesta classe de clientes.


### Região


```python
table_region = df.groupby('region').agg(**{
    'total':pd.NamedAgg(column='id',aggfunc='count'),
    'deleted_at_total':pd.NamedAgg(column='deleted_at',aggfunc='count'),
    'subs_time':pd.NamedAgg(column='subs_time',aggfunc='median'),
    'average_ticket':pd.NamedAgg(column='average_ticket',aggfunc='median'),
    'items_quantity':pd.NamedAgg(column='items_quantity',aggfunc='median'),
    'all_revenue':pd.NamedAgg(column='all_revenue',aggfunc='median'),
    'all_orders':pd.NamedAgg(column='all_orders',aggfunc='median'),
    'recency':pd.NamedAgg(column='recency',aggfunc='median')
    
})
table_region['perc_deleted_at'] = round(100*table_region['deleted_at_total']/table_region['total'],2)
```


```python
table_region
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total</th>
      <th>deleted_at_total</th>
      <th>subs_time</th>
      <th>average_ticket</th>
      <th>items_quantity</th>
      <th>all_revenue</th>
      <th>all_orders</th>
      <th>recency</th>
      <th>perc_deleted_at</th>
    </tr>
    <tr>
      <th>region</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Centro-Oeste</th>
      <td>1502</td>
      <td>80</td>
      <td>29.0</td>
      <td>216.448572</td>
      <td>8.0</td>
      <td>1155.892632</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>5.33</td>
    </tr>
    <tr>
      <th>Nordeste</th>
      <td>3250</td>
      <td>168</td>
      <td>30.0</td>
      <td>217.123361</td>
      <td>8.0</td>
      <td>1169.810289</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>5.17</td>
    </tr>
    <tr>
      <th>Norte</th>
      <td>2655</td>
      <td>140</td>
      <td>30.0</td>
      <td>217.992937</td>
      <td>8.0</td>
      <td>1176.091537</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>5.27</td>
    </tr>
    <tr>
      <th>Sudeste</th>
      <td>1456</td>
      <td>63</td>
      <td>29.0</td>
      <td>215.610672</td>
      <td>9.0</td>
      <td>1191.880792</td>
      <td>6.0</td>
      <td>35.0</td>
      <td>4.33</td>
    </tr>
    <tr>
      <th>Sul</th>
      <td>1137</td>
      <td>54</td>
      <td>31.0</td>
      <td>216.016153</td>
      <td>8.0</td>
      <td>1166.412022</td>
      <td>5.0</td>
      <td>35.0</td>
      <td>4.75</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig, axes = plt.subplots(nrows=3, ncols=2, figsize=(18, 13))
# a linha de código abaixo tira a coluna age group do index para n dar erro no gráfico
table_region.reset_index(inplace=True)
# lista de tuplas contendo o nome da variável e a coluna correspondente no dataframe
variables = [('Mediana do tempo desde a última compra do cliente', 'recency'),
             ('Mediana da duração da assinatura do cliente', 'subs_time'),
             ('Mediana de gasto por pedido', 'average_ticket'),
             ('Mediana de itens na assinatura', 'items_quantity'),
             ('Mediana do total de receita', 'all_revenue'),
             ('Mediana do número total de pedidos', 'all_orders')]
# loop para gerar cada subplot
for i, ax in enumerate(axes.flat):
    variable_name, variable_column = variables[i]
    ax.bar(table_region['region'], table_region[variable_column], color='mediumvioletred')
    ax.set_title(variable_name, fontsize=16)
plt.suptitle("Mediana por região de origem de outras variáveis dos clientes", fontsize=20)
# ajuste da margem dos subplots
plt.subplots_adjust(left=0, bottom=0.1, right=0.9, top=0.9, wspace=0.08, hspace=0.45)
plt.show()
```


    
![png](README_files/README_73_0.png)
    



```python
# criando tabela de percentual de faixa etaria no cancelamneto
aux_7 = df.query('churn=="yes"')['region'].value_counts(normalize=True).to_frame().reset_index()
aux_7['proportion'] = round(100*aux_7['proportion'],2)
# criando tabela de percentual de faixa etaria no total
aux_8 = df['region'].value_counts(normalize=True).to_frame().reset_index()
aux_8['proportion'] = round(100*aux_8['proportion'],2)
# plotando o merge destas tabelas para comparação
aux_9 = aux_8.merge(aux_7,how='inner',on='region').rename(columns={'proportion_x':'perc_customers',
                                                              'proportion_y':'perc_customers_deleted'})
aux_9['dif'] = aux_9['perc_customers_deleted']-aux_9['perc_customers']
display_side_by_side( aux_9,
                     titles=['Percentual de clientes em cada região na nossa base de clientes e no cancelamento'])
```


<th style="text-align:center"><td style="vertical-align:top"><h3>Percentual de clientes em cada região na nossa base de clientes e no cancelamento</h3><table style="display:inline" border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>region</th>
      <th>perc_customers</th>
      <th>perc_customers_deleted</th>
      <th>dif</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Nordeste</td>
      <td>32.50</td>
      <td>33.27</td>
      <td>0.77</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Norte</td>
      <td>26.55</td>
      <td>27.72</td>
      <td>1.17</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Centro-Oeste</td>
      <td>15.02</td>
      <td>15.84</td>
      <td>0.82</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sudeste</td>
      <td>14.56</td>
      <td>12.48</td>
      <td>-2.08</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sul</td>
      <td>11.37</td>
      <td>10.69</td>
      <td>-0.68</td>
    </tr>
  </tbody>
</table style="display:inline"></td></th>



```python
# criando um dataframe com as mesmas informações do table_customer_age, porém, apenas para clientes que cancelaram
table_region_deleted = df.query('churn=="yes"').groupby('region').agg(**{
    'subs_time':pd.NamedAgg(column='subs_time',aggfunc='median'),
    'average_ticket':pd.NamedAgg(column='average_ticket',aggfunc='median'),
    'items_quantity':pd.NamedAgg(column='items_quantity',aggfunc='median'),
    'all_revenue':pd.NamedAgg(column='all_revenue',aggfunc='median'),
    'all_orders':pd.NamedAgg(column='all_orders',aggfunc='median'),
    'recency':pd.NamedAgg(column='recency',aggfunc='median')
    })
```


```python
fig, axes = plt.subplots(nrows=3, ncols=2, figsize=(18, 13))
# a linha de código abaixo tira a coluna age group do index para n dar erro no gráfico
table_region_deleted.reset_index(inplace=True)
# lista de tuplas contendo o nome da variável e a coluna correspondente no dataframe
variables = [('Mediana do tempo desde a última compra do cliente', 'recency'),
             ('Mediana da duração da assinatura do cliente', 'subs_time'),
             ('Mediana de gasto por pedido', 'average_ticket'),
             ('Mediana de itens na assinatura', 'items_quantity'),
             ('Mediana do total de receita', 'all_revenue'),
             ('Mediana do número total de pedidos', 'all_orders')]
# loop para gerar cada subplot
for i, ax in enumerate(axes.flat):
    variable_name, variable_column = variables[i]
    ax.bar(table_region_deleted['region'], table_region_deleted[variable_column], color='mediumvioletred')
    ax.set_title(variable_name, fontsize=16)
plt.suptitle("Mediana por região de outras variáveis dos clientes que cancelaram", fontsize=20)
# ajuste da margem dos subplots
plt.subplots_adjust(left=0, bottom=0.1, right=0.9, top=0.9, wspace=0.08, hspace=0.45)
plt.show()
```


    
![png](README_files/README_76_0.png)
    


#### Resumo
Fazendo uma breve análise por região de origem dos clientes, não vemos tendências em determinadas regiões específicas olhando para o todo. Porém, vendo apenas o caso dos clientes que cancelaram a assinatura vemos algumas informações relevantes.  
O sudeste parece ser a região mais rentável, com um valor mediano maior de receita, número maior de pedidos e o segundo maior tempo mediano de duração de assinatura. Se fosse realizada uma ação com objetivo de redução de churn e alguns clientes precisassem ser priorizados por motivo de baixo recurso, por exemplo, poderíamos priorizar as duas regiões mais rentáveis Norte e Sudeste.

### Comportamento sazonal


```python
# criando um dataframe com ano e mês de exclusão 
table_month_year_deleted = df.pivot_table(values = 'id', index = ['deleted_at_month'], columns = 'deleted_at_year',
    aggfunc='count').fillna(0).reset_index().rename_axis(index=None, columns= None).set_index('deleted_at_month')
# pro grafico ficar na ordem correta
table_month_year_deleted['seq'] = [4,8,12,2,1,7,6,3,5,11,10,9]
table_month_year_deleted.sort_values('seq',inplace=True)
# renomeando os anos que estão em float
table_month_year_deleted.rename(columns={2016.0:'2016',2017.0:'2017',2018.0:'2018',2019.0:'2019',2020.0:'2020',
                                             2021.0:'2021'},inplace=True)
```


```python
aux = table_month_year_deleted.drop('seq',axis=1).plot(figsize=(15, 5))
plt.ylabel('Quantidade de cancelamentos')
plt.xlabel('Meses',fontsize=13)
plt.legend(['2016','2017','2018','2019','2020','2021'],loc=0)
plt.title("Quantidade de cancelamentos por mês em cada ano",fontsize=13)
plt.show()
```


    
![png](README_files/README_80_0.png)
    



```python
# criando um dataframe com dia e mês de exclusão 
table_month_day_deleted = df.pivot_table(values = 'id', index = ['deleted_at_day'], columns = 'deleted_at_month',
    aggfunc='count').fillna(0).reset_index().rename_axis(index=None, columns= None)[['deleted_at_day','January','February',
   'March','April','May','June','July','August','September','October','November','December']]
# transformando a coluna que ta em float pra int e colocando-a como index
table_month_day_deleted['deleted_at_day'] = table_month_day_deleted['deleted_at_day'].astype(int)
table_month_day_deleted.set_index('deleted_at_day',inplace=True)
```


```python
aux = table_month_day_deleted.plot(figsize=(15, 5))
plt.ylabel('Quantidade de cancelamentos')
plt.xlabel('Dias do mês',fontsize=13)
plt.legend(['January','February','March','April','May','June','July','August','September','October','November','December'],loc=0)
plt.title("Quantidade de cancelamentos por dia do mês em cada mês",fontsize=13)
plt.show()
```


    
![png](README_files/README_82_0.png)
    



```python
# criando um dataframe com dia da semana e mês de exclusão 
table_month_weekday_deleted = df.pivot_table(values = 'id', index = ['deleted_at_weekday'], columns = 'deleted_at_month',
    aggfunc='count').fillna(0).reset_index().rename_axis(index=None, columns= None).set_index('deleted_at_weekday')
# pro grafico ficar na ordem correta
table_month_weekday_deleted['seq'] = [4,0,5,6,3,1,2]
table_month_weekday_deleted.sort_values('seq',inplace=True)
```


```python
aux = table_month_weekday_deleted.plot(figsize=(15, 5))
plt.ylabel('Quantidade de cancelamentos')
plt.xlabel('Dias da semana',fontsize=13)
plt.legend(['January','February','March','April','May','June','July','August','September','October','November','December'],loc=0)
plt.title("Quantidade de cancelamentos por dia do mês em cada mês",fontsize=13)
plt.show()
```


    
![png](README_files/README_84_0.png)
    


#### Resumo  
Não há um padrão visível nos dados por mês e por dia do mês, por conta do tamanho da amostra ser pequeno (em relação a cancelados) e o período ser grande a visualização de padrões e tendências fica prejudicada. Poderia ser feita uma análise percentual mas os valores ficariam extremos por conta da baixa quantidade quando agrupado por mês ou dia.  
Agrupando por dia da semana conseguimos uma visualização melhor e com mais dados. O que esperava ver era um padrão de comportamento de cancelamento em algum dia da semana, ou final de semana, para assim desenhar ações em dias específicos. Porém, nenhuma tendência se desenhou e consideramos que os clientes cancelam em qualquer dia da semana.
