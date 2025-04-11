# MVP – Engenharia de Dados – PUC RJ
## Documentação

Esse trabalho faz análise de duas bases de dados, ambas referentes aos participantes da Maratona de Boston, uma das mais tradicionais maratonas do mundo, em ação desde 1897. Essa prova é reconhecida como uma das “Major Marathons”, atraindo assim muitos que tem ambição de completar o circuito, que dá direito ao reconhecimento por uma medalha mandala.  Os dados aqui usados são dos ultimos anos de realização, 2023 e 2024, visando comparar e contratar os dados e entender o movimento de um ano para o outro.

### 1.Objetivos:

##### 1.1 Objetivos Iniciais:

• Comparar o numero de corredores em provas de 42km no estado de São Paulo nos ultimos anos (preferencialmente no periodo pós pandemico, 2022 – 2024) para entender se realmente houve um aumento no volume de inscrições;\
• Entender se, comparativamente, houve diferença na porcentagem entre homens e mulheres inscritos;\
• Entender se, comparativamente, houve diferença na porcentagem dos inscritos por genero e faixa etaria;\
• Analisar a media de tempos de finalização em grupos de genero para entender se houve alguma diferença entre os anos;\
• Analisar a media de tempos em cada grupo por faixa etaria e genero para entender se houve melhora ou piora entre anos.

##### 1.2 Objetivos ajustados:

• Comparar o numero de inscritos entre os anos de 2023 e 2024 na Maratona de Boston para entender se realmente houve um aumento no volume de inscrições; \
• Entender se, comparativamente, houve diferença na porcentagem dos inscritos por genero;\
• Entender se, comparativamente, houve diferença nos inscritos por faixa etaria;\
• Analisar a media de tempos de finalização em grupos de genero para entender se houve alguma diferença entre os anos;\
• Analisar a media de tempos em cada grupo por faixa etaria e genero para entender se houve melhora ou piora entre anos;\
• Analisar se houve diversidade da nacionalidade dos participantes durante os anos

#### 2.	Coleta
##### 2.1 Fonte dos dados

• Dados de 2024 coletados via site oficial da maratona de Boston: Boston Marathon 2024: [Results Search (Media)](https://registration.baa.org/2024/cf/Media/iframe_ResultsSearch.cfm)\
• Dados de 2023 coletados via dataset Kaggle com classificação de 9.4 de confiabilidade: [2023 Boston Marathon runners – SCORE Sports Data Repository]()

#### 3.	Modelagem
##### 3.1 Metadados das tabelas:

•	Dataframe dos dados de 2024:

| Atributo | Descrição |
| --- | --- |
| BibNumber | Número de peito do participante |
| FullName | Nome completo |
| SortName | Nome formatado ("Sobrenome, Nome") |
| AgeOnRaceDay | Idade no dia da corrida |
| Gender | Gênero (M ou F) |
| City | Cidade |
| StateAbbrev | Abreviação do estado de residência |
| StateName | Estado de residência |
| Zip | CEP do participante |
| CountryOfResAbbrev | Abreviação do país de residência |
| CountryOfResName | País de residência |
| CountryOfCtzAbbrev | Abreviação do país de cidadania |
| CountryOfCtzName | País de cidadania |
| OfficialTime | Tempo de conclusão da prova |
| RankOverall | Colocação geral |
| RankOverGender | Colocação do participante entre mesmo gênero |
| RankOverDivision | Colocação do participante na sua divisão de idade |
| EventGroup | Nome do grupo que o participante está |
| SubGroupLabel | Nome do subgrupo que o participante está |
| SubGroup | Código ou categoria do subgrupo |


•	Dataframe dos dados de 2023:

| Atributo | Descrição |
| --- | --- |
| age_group | Faixa etária |
| place_overall | Colocação geral do atleta |
| place_gender | Colocação do participante entre mesmo gênero |
| place_division | Colocação do participante na sua divisão de idade |
| name | Nome do participante |
| team | Nome da equipe |
| bib_number | Número de peito do participante |
| half_time | Tempo na metade da prova |
| finish_net | Tempo líquido de conclusão da prova |
| finish_gun | Tempo bruto de conclusão da prova |
| gender | Gênero |
| half_time_sec | Tempo na metade da prova, em segundos |
| finish_net_sec | Tempo líquido em segundos |
| finish_gun_sec | Tempo bruto em segundos |
| finish_net_minutes | Tempo líquido em minutos |



##### 3.2 Modelagem de dados

• Modelo escolhido: Star Schema \
• Tabela Fato: fato_maratona

| Atributo | Descrição |
| --- | --- |
| id_fato | Identificador único da tabela fato (chave primária) |
| ano_evento | Ano da maratona |
| id_participante | Identificador do participante (chave estrangeira – ‘d_participante’) |
| id_categoria | Identificador da categoria (chave estrangeira- ‘d_categoria’) |
| pos_geral | Colocação geral na maratona |
| pos_genero | Colocação do atleta no mesmo gênero |

Dimensões:
• Dimensão ‘Participante’: d_participante\

| Atributo | Descrição |
| --- | --- |
| id_participante | Identificador do participante (chave primária) |
| nome | Nome do participante |
| num_peito | Número de peito |
| tempo_final | Tempo final de conclusão da prova (tempo líquido) |




• Dimensão ‘Categoria’: d_categoria\
Dados para relacionar com a faixa etaria e genero do participante

| Atributo | Descrição |
| --- | --- |
| id_categoria | Identificador único da categoria (chave primária) |
| genero | Gênero do atleta (M para masculino, F para feminino) |
| faixa_etaria | Faixa etária do participante (ex: 18-39, 40-44, ..., 80+) |


#### 3.3 Desenho da Modelagem:

![image](https://github.com/user-attachments/assets/84b7dd52-03d8-4bd2-9e25-bd39a4a8fd6a)



#### 4.	Carga:
##### 4.1. Ingestão de dados no Databricks
• Criação do perfil, cluster e notebook pelo Databrick Community;\
• Criação das tabelas no Databricks

![image](https://github.com/user-attachments/assets/c296fb7c-a5ab-4f0c-b189-f63281f3798d)

![image](https://github.com/user-attachments/assets/d1bd3f09-627c-4bc4-b7d8-e82252697eba)


• Ingestão de dados por script PySpark:

Script para ingestão 2024:
> df_2024 = spark.read.csv("/FileStore/tables/boston_marathon_data/boston_marathon_2024.csv",header = True) \
display(df_2024.limit(5))

Script para ingestão 2023:
> df_2023 = spark.read.csv("/FileStore/tables/boston_marathon_data/boston_marathon_2023.csv",header = True) \
display(df_2023.limit(5))

![image](https://github.com/user-attachments/assets/34d9d69c-1ac2-40c2-9a03-256f31959386)


#### 5. Analise
##### 5.1 Qualidade de Dados

Para começar a limpeza dos dados, foi feita uma visualização dos dados categóricos em cada dataframe criado para entender a distribuição dos mesmos.

![image](https://github.com/user-attachments/assets/47e1bba5-2b88-4960-84aa-7ef0586e54c3)

![image](https://github.com/user-attachments/assets/4b6a06c9-2e90-4198-a130-b59d4e7cd1cb)

Os dados estavam, no geral, bem completos, porém alguns problemas foram identificados, especialmente quando comecei a avaliar os dados de forma a usa-los para analise. Esses pontos foram documentados e resolvidos de acordo com a lista e evidencias abaixo:
 
#### Principais problematicas encontradas e soluções:

##### 1. Atributos divergentes entre as tabelas. 
Solução: criar dataframes a partir desses apenas com os dados necessários para analise dos objetivos, renomeando os atributos para facilitar mais ainda a analise

![image](https://github.com/user-attachments/assets/d24f9666-f149-4d16-8614-a60d6a41a6dd)

![image](https://github.com/user-attachments/assets/bca70295-0591-468e-9fc1-4373ced183a7)


##### 2. Formato do tempo divergente.
Comparação de tempo ficará inviavel uma vez que os formatos não batem (2024 está como str, no formato H:MM:SS, enquanto 2023 está como str no formato XH XM XS)
Solução: Transformar os formatos do dataframe de 2023 para o modelo H:MM:SS

![image](https://github.com/user-attachments/assets/a77c7b37-bcaa-44f8-a093-374d8e18c37f)

##### 3. Juntar os dados por ano
Embora não seja necessário, facilitará o entendimentos e as analises dos objetivos
Solução: Adicionar um atributo que separe por ano (2023/2024)

![image](https://github.com/user-attachments/assets/7d5bc7dd-dc8d-415e-bb2a-99fe4c5ee567)


##### 4. Formato do genero divergente
Enquanto em 2024 está com M/F (Male/Masculino, Female/Feminimo) em 2023 está com M/W (Man/Homem, Woman/Mulher). 
Solução: Onde tiver W, substituir por F para manter o padrão.

Antes da limpeza:

![image](https://github.com/user-attachments/assets/eb64080a-b7f6-461e-935e-840eefc1071a)

Após limpeza:

![image](https://github.com/user-attachments/assets/62043a73-8876-44b5-93f3-bc0b284234e3)
![image](https://github.com/user-attachments/assets/7c63118b-999d-46fd-8fae-aed7e9edce66)


##### 5. Outliers de genero no df de 2024.
Existe um Genero classificado como "X"
Solução: Uma vez que não existe uma classificação dentro de um genero indefinido/não binario e como são poucos os registros (menos de 1% do total), optou-se por excluir esses registros para não prejudicar a contagem final

Antes da limpeza:
![image](https://github.com/user-attachments/assets/27735018-908c-405c-8856-4d68dab8b2b1)

Após limpeza:

![image](https://github.com/user-attachments/assets/9195b392-764e-41e9-9914-9cc088c90840)


##### 6. Idade não está separada por Grupos de Idade nos dados de 2024
Solução: Criar um atributo que distribua as idades em grupos.

![image](https://github.com/user-attachments/assets/eaaae080-6eb7-4afb-af27-a786875307f3)

Vaidando se houve qualquer problema com idades:

![image](https://github.com/user-attachments/assets/9c023b7f-1bd4-450f-b9a7-4812d891b571)

Algo que não se contemplou no escopo desse trabalho mas que diretamente poderia afetar a qualidade dos dados em um projeto maior foi observado na criação da tabela fato. Ao criar os ids unicos, baseados nos participantes, uma das observações que fiz foi que não haveria uma forma de garantir a unicidade dos registros de corredor, por não haver um identificador que o torne unico, como poderia ser um CPF no caso de um cidadão brasileiro.\
Isso significa que caso um atleta participe de varios anos, não seria possivel agrupar e manter esse registro, o que poderia facilitar para a criação de um histórico e possiveis analises no desempenho dos participantes.\
Porém, como dito, essa limpeza não foi contemplada nesse momento, apenas fica como observação para futuras analises ou tentativas de ajuste.

##### 5.2 Soluções dos Objetivos:

- Para iniciar a solucão dos objetivos, o primeiro passo foi a junção dos dataframes dos dois anos para criação das tabelas Fato e Dimensões:

![image](https://github.com/user-attachments/assets/6269cfb9-8e22-41ac-ad6c-3d3d048931ec)

- Criação das Dimensões:

![image](https://github.com/user-attachments/assets/f37654d4-7a92-4b87-9e1b-72748c91b016)


- Criação da Fato:

![image](https://github.com/user-attachments/assets/82976174-604f-4b2d-82c1-e1714889fa7c)

- Objetivos e soluções comentadas:

![image](https://github.com/user-attachments/assets/88e1525a-6be5-4d76-95eb-d2768ff0927d)
![image](https://github.com/user-attachments/assets/6b54caeb-079c-4fd7-8469-95100640f646)
![image](https://github.com/user-attachments/assets/f2f3f510-6fe6-44a9-a236-7f1ee2238afc)
![image](https://github.com/user-attachments/assets/5c9d8339-a980-4624-8017-d3a9bb33d795)
![image](https://github.com/user-attachments/assets/50f82827-4e63-4092-81a0-55b98cfc39be)
![image](https://github.com/user-attachments/assets/07378fca-e940-4ca0-b9f5-57679a199847)
![image](https://github.com/user-attachments/assets/67fbc81a-5b0d-4fa0-8dc7-609f107b03d1)
![image](https://github.com/user-attachments/assets/f420d82b-5e04-499d-bce8-5f0807e50966)
![image](https://github.com/user-attachments/assets/a7ddc572-c674-42dd-a2aa-6eadc2dbd0e5)


#### 6. Auto Avaliação
A corrida entrou na minha vida poucos anos atrás e é um tópico que me fascina, visto que, nos ultimos anos, a impressão que fica é que o numero de corredores aumentou vertiginosamente. Há um burburinho popular que diz, inclusive, que nos ultimos anos, a corrida teria virado uma especie de "modinha". Sendo esse um tema intrigante, optei por tentar estuda-lo um pouco melhor \
Demorei bastante a começar o projeto por ter travado justamente na questão inicial. Me pareceu muito dificil pensar em perguntas uma vez que eu nem teria um tema em mente. Mesmo com o tema escolhido, foi dificil considerar que poderia deixar perguntas não respondidas, ou por não saber onde achar os dados ou como utiliza-los. De qualquer forma, comecei a busca pelos dados. Inicialmente, gostaria de analisar dados sobre corridas no Brasil, porém descobri que para utilizar esses dados, eu precisaria fazer um web scrapping, habilidade tecnica que ainda não tenho. Então optei por buscar dados de corridas já estabelecidos. Boston se apresentou como uma boa alternativa por ter dados de anos proximos com boa confiabilidade.\
Ao ver os dados então, foi necessário fazer uma revisão nos objetivos inicialmente pensados. Embora não tenha saido tanto do que tinha sido pensado inicialmente, foi um exercicio interessante para adaptar a realidade que nem sempre poderemos responder tudo que temos curiosidade.  \
Sobre os objetivos apontados, as soluções trouxeram uma analise interessante de perfis porém quando terminei as analises, senti falta de mais dados, talvez um histórico um pouco mais longo, que me ajudasse a entender se essas respostas foram modificadas com passar do tempo, porém na falta das habilidade tecnicas para captar essas informações nesse momento, sinto que ainda assim as comparações entre esses dois anos foram produtivas. Uma observação que faço é sobre o objetivo 6, não resolvido, mas que me ensinou a observar melhor os dados como uma totalidade em um proximo projeto ou momento futuro. \
Como trabalho futuro, gostaria de me aprofundar nos conceitos de web scrapping para voltar aos objetivos iniciais: analisar os dados que vem de corridas no Brasil, em especial  em provas de longa distancia (acima de 21km) e baseadas em grandes cidades (iniciar com o recorte de São Paulo, porém expandir para entender se o movimento em outros estados condiz com o de grandes metropoles) e entender se houve mesmo um movimento de “modinha”.
