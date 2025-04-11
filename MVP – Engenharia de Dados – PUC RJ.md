#MVP – Engenharia de Dados – PUC RJ
#Documentação

Esse trabalho faz análise de duas bases de dados, ambas referentes aos participantes da Maratona de Boston, uma das mais tradicionais maratonas do mundo, em ação desde 1897. Essa prova é reconhecida como uma das “Major Marathons”, atraindo assim muitos que tem ambição de completar o circuito, que dá direito ao reconhecimento por uma medalha mandala.  Os dados aqui usados são dos ultimos anos de realização, 2023 e 2024, visando comparar e contratar os dados e entender o movimento de um ano para o outro.

1.	Objetivos:

1.1	Objetivos Iniciais:

•	Comparar o numero de corredores em provas de 42km no estado de São Paulo nos ultimos anos (preferencialmente no periodo pós pandemico, 2022 – 2024) para entender se realmente houve um aumento no volume de inscrições;
•	Entender se, comparativamente, houve diferença na porcentagem entre homens e mulheres inscritos.
•	Entender se, comparativamente, houve diferença na porcentagem dos inscritos por genero e faixa etaria
•	Analisar a media de tempos de finalização em grupos de genero para entender se houve alguma diferença entre os anos
•	Analisar a media de tempos em cada grupo por faixa etaria e genero para entender se houve melhora ou piora entre anos
1.2 Objetivos ajustados:
•	Comparar o numero de inscritos entre os anos de 2023 e 2024 na Maratona de Boston para entender se realmente houve um aumento no volume de inscrições;
•	Entender se, comparativamente, houve diferença na porcentagem dos inscritos por genero
•	Entender se, comparativamente, houve diferença nos inscritos por faixa etaria
•	Analisar a media de tempos de finalização em grupos de genero para entender se houve alguma diferença entre os anos
•	Analisar a media de tempos em cada grupo por faixa etaria e genero para entender se houve melhora ou piora entre anos
•	Analisar se houve diversidade da nacionalidade dos participantes durante os anos

2.	Coleta
2.1 Fonte dos dados
•	Dados de 2024 coletados via site oficial da maratona de Boston: Boston Marathon 2024: Results Search (Media)
•	Dados de 2023 coletados via dataset Kaggle com classificação de 9.4 de confiabilidade: 2023 Boston Marathon runners – SCORE Sports Data Repository

3.	Modelagem
3.1 Metadados das tabelas:
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



3.2 Modelagem de dados

•	Modelo escolhido: Star Schema
•	Tabela Fato: fato_maratona

| Atributo | Descrição |
| --- | --- |
| id_fato | Identificador único da tabela fato (chave primária) |
| ano_evento | Ano da maratona |
| id_participante | Identificador do participante (chave estrangeira – ‘d_participante’) |
| id_categoria | Identificador da categoria (chave estrangeira- ‘d_categoria’) |
| pos_geral | Colocação geral na maratona |
| pos_genero | Colocação do atleta no mesmo gênero |

Dimensões:
•	Dimensão ‘Participante’: d_participante
Tabela com dados pessoais para analise sobre o participante




•	Dimensão ‘Categoria’: d_categoria
Dados para relacionar com a faixa etaria e genero do participante


3.3 Desenho da Modelagem:

 

4.	Carga:
4.1. Ingestão de dados no Databricks
2.2 Evidencias da ingestão dos dados no Databricks
•	Criação do perfil, cluster e notebook pelo Databrick Community;
•	Criação das tabelas no Databricks:

 

•	Ingestão de dados por script PySpark:
2024: Script:
 df_2024 = spark.read.csv("/FileStore/tables/boston_marathon_data/boston_marathon_2024.csv",header = True)
display(df_2024.limit(5))

Evidencia: 

2023: Script:
df_2023 = spark.read.csv("/FileStore/tables/boston_marathon_data/boston_marathon_2023.csv",header = True)
display(df_2023.limit(5))

Evidencia: 

5. Analise
5.1 Qualidade de Dados
	Os dados estão, no geral, bem completos, porém alguns problemas foram identificados, especialmente quando comecei a avaliar os dados de forma a usa-los para analise. Esses pontos foram documentados e resolvidos de acordo com a lista abaixo:
### Principais problematicas encontradas e soluções:

######1. Atributos divergentes entre as tabelas. 
Solução: criar dataframes a partir desses apenas com os dados necessários para analise dos objetivos

######2. Formato do tempo divergente.
Comparação de tempo ficará inviavel uma vez que os formatos não batem (2024 está como str, no formato H:MM:SS, enquanto 2023 está como str no formato X H X MIN X SEG)
Solução: Transformar os formatos para segundos

######3. Juntar os dados por ano
Embora não seja necessário, facilitará o entendimentos e as analises dos objetivos
Solução: Adicionar um atributo que separe por ano (2023/2024)

######4. Formato do genero divergente
Enquanto em 2024 está com M/F (Male/Masculino, Female/Feminimo) em 2023 está com M/W (Man/Homem, Woman/Mulher). 
Solução: Onde tiver W, substituir por F para manter o padrão.

######5. Outliers de genero no df de 2024.
Existe um Genero classificado como "X"
Solução: Uma vez que não existe uma classificação dentro de um genero indefinido/não binario e como são poucos os registros (menos de 1% do total), optou-se por excluir esses registros para não prejudicar a contagem final

######6. Idade não está separada por Grupos de Idade nos dados de 2024
Solução: Criar um atributo que distribua as idades em grupos.

Algo que não se contemplou no escopo desse trabalho mas que diretamente poderia afetar a qualidade dos dados em um projeto maior foi observado na criação da tabela fato. Ao criar os ids unicos, baseados nos participantes, uma das observações que fiz foi que não haveria uma forma de garantir a unicidade dos registros de corredor, por não haver um identificador que o torne unico, como poderia ser um CPF no caso de um cidadão brasileiro.
Isso significa que caso um atleta participe de varios anos, não seria possivel agrupar e manter esse registro, o que poderia facilitar para a criação de um histórico e possiveis analises no desempenho dos participantes.
Porém, como dito, essa limpeza não foi contemplada nesse momento, apenas fica como observação para futuras analises ou tentativas de ajuste.

5.2 Soluções dos Objetivos:


6. Auto Avaliação
	A corrida entrou na minha vida poucos anos atrás e é um tópico que me fascina, visto que, nos ultimos anos, a impressão que fica é que o numero de corredores aumentou vertiginosamente. 
Demorei bastante a começar o projeto por ter travado justamente na questão inicial. Me pareceu muito dificil pensar em perguntas uma vez que eu nem teria um tema em mente. Mesmo com o tema escolhido, foi dificil considerar que poderia deixar perguntas não respondidas, ou por não saber onde achar os dados ou como utiliza-los. De qualquer forma, comecei a busca pelos dados. Inicialmente, gostaria de analisar dados sobre corridas no Brasil, porém descobri que para utilizar esses dados, eu precisaria fazer um web scrapping, habilidade tecnica que ainda não tenho. Então optei por buscar dados de corridas já estabelecidos. Boston se apresentou como uma boa alternativa por ter dados de anos proximos com boa confiabilidade.
Ao ver os dados então, foi necessário fazer uma revisão nos objetivos inicialmente pensados. Embora não tenha saido tanto do que tinha sido pensado inicialmente, foi um exercicio interessante para 

Como trabalho futuro, gostaria de me aprofundar nos conceitos de web scrapping para voltar aos objetivos iniciais: analisar os dados que vem de corridas no Brasil, em especial  em provas de longa distancia (acima de 21km) e baseadas em grandes cidades (iniciar com o recorte de São Paulo, porém expandir para entender se o movimento em outros estados condiz com o de grandes metropoles) e entender se houve mesmo um movimento de “modinha” nos anos pós pandemia.
