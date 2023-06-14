# D3TOP-NLP-Proj
 
## 1. Descrição do problema

Em 2022 o Superior Tribunal de Justiça (STJ) passou a disponibilizar os dados de processos judiciais que tramitam naquela corte em um portal de dados abertos ([Notícia de 20/05/2022](https://www.stj.jus.br/sites/portalp/Paginas/Comunicacao/Noticias/20052022-Novo-Portal-de-Dados-Abertos-amplia-transparencia-de-acoes-realizadas-no-STJ.aspx)). Esse novo portal passou a disponibilizar dados que já estavam disponíveis publicamente na página web do STJ de maneira estruturada e acessível por máquina, sem a necessidade de realizar *web scraping*.

Dos dados disponíveis, existe um conjunto denominado "Íntegras de Decisões Terminativas e Acórdãos do Diário da Justiça". Neste repositório estão disponíveis decisões que terminam processos, seja indeferindo recursos à corte ou dando-lhes provimento monocraticamente, seja acórdãos de casos específicos.

Atualmente são disponibilizados como metadados vários elementos dos processos, como tipo, teor (Negando, Dando provimento, etc), assuntos vinculados, etc. Essas informações facilitam a busca e filtragem de dados no dataset. Essas informações são cadastradas manualmente pelos operadores do direito, sejam os advogados das partes, sejam os serventuários da justiça, e dependem da entrada manual dos dados.

O problema que se busca analisar e eventualmente solucionar com esse projeto é a utilização de técnicas de Processamento de Linguagem natural (*Natural Language Processing* - NLP) para verificar se é possível e com que exatidão prever os assuntos abordados nas decisões sem depender da interação humana.

## 2. Descrição da base de dados

Para esse projeto será utilizada a base de dados abertos do STJ, especificamente a base "*Íntegras de Decisões Terminativas e Acórdãos do Diário da Justiça*", disponível em https://dadosabertos.web.stj.jus.br/dataset/integras-de-decisoes-terminativas-e-acordaos-do-diario-da-justica.

A base de dados possui um dicionário (dicionariointegrasdecisoes.csv) com a descrição dos campos e seus tipos associados.

Nessa base de dados é disponibilizado, nos dias que existe publicação no diário oficial, um arquivo .zip. Referido arquivo possui um arquivos denominado metadados, em json, descrevendo:


| Campo | Tipo | Descrição |
|---|---|---|
| SeqDocumento | Numérico | Identificador da decisão ou acórdão. Corresponde ao nome do arquivo TXT encontrado no ZIP, no qual consta o texto completo da decisão ou acórdão. |
| dataPublicacao | Data | Data da publicação do documento no Diário da Justiça Eletrônico. | 
| tipoDocumento | Texto | Informa se o documento é um acórdão ou uma decisão terminativa. |  
| numeroRegistro | Texto| Numero de registro do processo no qual foi proferido o Acórdão ou Decisão no Sistema do STJ |  
| processo | Texto | Sigla da classe processual seguida do número do processo naquela classe. |
| dataRecebimento | Data | Data que o processo foi recebido no STJ. |
| dataDistribuição | Data | Data que o processo foi distribuído. |
| ministro | Texto | Nome do ministro relator do acórdão ou proferidor da decisão. |
| recurso | Texto | Indica o recurso interno sobre o qual foi proferida a decisão ou acórdão se houver. Quando nulo, indica que se refere ao processo principal. |
| teor | Texto |Pequeno texto que corresponde ao desfecho do julgamento. Ex. Concedendo, Denegando, Não Conhecendo etc. Disponível para acórdãos e decisões monocráticas. |
| descricaoMonocratica | Texto | Pequeno texto que corresponde à conclusão do julgado. Disponível apenas para decisões monocráticas. |
| assuntos | Texto | Lista dos códigos CNJ dos assuntos relacionados ao processo separados por ';'.  O significado desses códigos estão disponíveis para download em ottps://dpj.cnj.jus.br/sgt/api/v1.0/assuntos.csv e para https://www.cnj.jus.br/sgt/consulta_publica_assuntos.php. |

O identificador do documento é utilizado para identificar o arquivo .txt dentro do diretório compactado que contém o texto da decisão.

## 3. Objetivo científico associado ao problema

A utilização de técnicas de NLP tem objetivos científicos, especificamente: **a)** verificar o quanto os sistemas de NLP são capazes de realizar a classificação de textos jurídicos automaticamente, utilizando os dados já rotulados e **b)** Partir de classificações mais genéricas para mais específicas e, eventualmente, classificar as decisões em critérios mais específicos que os disponíveis atualmente.

## 3. Objetivo científico associado ao problema

A utilização de técnicas de NLP tem objetivos científicos, especificamente: **a)** verificar o quanto os sistemas de NLP são capazes de realizar a classificação de textos jurídicos automaticamente, utilizando os dados já rotulados e **b)** Partir de classificações mais genéricas para mais específicas e, eventualmente, classificar as decisões em critérios mais específicos que os disponíveis atualmente.

## 4. Obtenção dos dados

Os dados estão disponíveis no repositório de dados públicos do STJ pela API comum da CKAN. Inicialmente precisamos fazer o download dos dados.

Junto com os arquivos .txt dos dados vem um arquivo metadados<data>json que deve seguir a estrutura discriminada no arquivo dicionariointegrasdecisoes.csv. Seguindo a descrição nesse arquivo a estrutura dos arquivos 'metadados' possuem o esquema definido no item 2. acima.

Nesse momento, criaremos o arquivo estruturando os metadados, vez que estes foram carregados em arquivos separados com cada entrada criada no repositório.

 ## 5. Filtragem das decisões

 O dataset possui decisões de muitas classes processuais diversas, o que aumenta ainda mais o problema considerando os assuntos possíveis. Antes de filtrar uma classe para treinar, vamos verificar a distribuição dessas classes no dataset.
 
![graph1](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/43ad1bc2-4f5e-498f-b3db-17e1d9bd6b0f)

 Como se observar, os Agravos em Recurso Especial (AREsp) são a classe predominante, seguida de Recursos Especiais (REsp) e *Habeas Corpus* (HC).

Considerando o número de decisões, apesar do AREsp ser predominante, iremos focar nos REsps por terem mais variabilidade de dados e número suficiente para uma filtragem.
  
 ![graph2](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/b5d60157-9215-4ded-9aff-467ad32b50b6)

 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/62901e02-68b6-43eb-9b0d-0e0a05b5fdd9)

 Observa-se que a maior parte das decisões são monocráticas, provavelmente concedendo ou negando em razão de jurisprudência consolidada. Considerando-se que ainda é um número alto de decisões, iremos filtrar e selecionar apenas as decisões monocráticas (valor 0) para seguirmos na seleção de dados.
 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/bc74be75-23c9-4b5f-9d82-fb27a8005ebb)

 Como se observa, a maior parte das decisões é concedendo, negando ou não conhecendo o recurso.

Aqui já se extrai uma informação relevante para os operadores do direito: Uma vez que o recurso especial é admitido a chance de ser conhecido no mérito é maior do que se não admitido.

No que tange a esse trabalho, mesmo se limitando às decisões 'Concedendo' já temos um corpus bem relevante para treinamento, logo vamos focar nessa categoria.
 
 Por último, os assuntos precisam ser organizados. Esses assuntos estão disponíveis para consulta no [site do CNJ](https://www.cnj.jus.br/sgt/consulta_publica_assuntos.php), mas também são disponibilizados para download num arquivo .csv. Como essa tabela possui grande granularidade (mais de 4700 assuntos disponíveis), vamos agrupar decisões em dois grandes grupos: Direito Civil e Direito Penal, ignorando demais temas.
Para evitar que isso se tranforme em um problema multilabel, decisões que abranjam ao mesmo tempo Direito Civil e Direito Penal serão excluidas também.

 O campo 'recurso' indica se a decisão foi proferida em algum recurso interno ao REsp ou se foi no processo principal. Vamos verificar o quando representa do dataset.
 
 ![graph4](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/324d7f45-7cfb-4eed-adf4-ec6eb9665788)

 O dataset ainda apresenta quantidade significativa de dados, sendo que a maior parte são de recursos que foram conhecidos e providos integralmente.
Esse grupo contém boas quantidades de decisões civis e penais, logo podemos nos restringir a essas decisões e ainda teremos dados de sobra para o treinamento.
 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/9339304d-6740-4434-9b28-1a9c11b1903f)
 
 Observa-se que grande parte das decisões foram proferidas por poucos ministros, tendo outros proferidos relativamente poucas decisões sobre os temas.
Uma das explicações é a divisão de matérias dos STJ. Existem alguns ministros com especializações diferentes e sesções também.

A organização do STJ atual é a seguinte:

| Matéria​ | Exemplos | Seção | Turmas | 
| --- | --- | --- | --- |
| Direito público | Impostos, previdência, servidores públicos, indenizações do Estado, improbidade | Primeira | Primeira e Segunda | 
| Direito privado | Comércio, consumo, contratos, família, sucessões | Segunda | Terceira e Quarta |
| Direito penal | Crimes em geral, federalização de crimes contra direitos humanos | Terceira | Quinta e Sexta |

Sendo assim, uma das hipóteses é que os ministros com poucas decisões pertençam à Primeira Seção e são decisões que lidam com Direito Público e apenas tangencialmente tratam de assuntos de direito civil ou de direito penal, explicando a pequena quantidade de decisões.

De qualquer maneira, há dados suficientes para testes nos ministros com mais decisões e filtrar as desses provavelmente elimina ruídos desnecessários, considerando que cada Ministro possui seu estilo de redação e pode se tratar de assuntos diversos, que dificultariam 
 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/254bb086-fa83-4fd9-b19c-fe6ee26abeed)
o aprendizado pelo modelo.
 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/01a503d3-c0c0-4184-afbe-2ae892392624)

 O problema está levemente desbalanceado mas ainda temos bastante dados, considerando que as decisões possuem bastante texto.
Iremos limitar à 200 decisões de cada classe, das mais recentes às mais antigas, considerando que é um problema complexo.
 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/34f54580-6dc8-4554-9046-254c7524e3d1)

 O dataset está plenamente equilibrado (como todas as coisas devem ser).
 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/01b8a58e-d1b9-46e3-ad5a-7d29cceeb586)

 
 ![download](https://github.com/o-guilherme/D3TOP-NLP-Proj/assets/13070898/8133e373-db0f-4bd4-9669-8aeaea3b92de)

 O dataset está pronto para ser populado com os dados das decisões.
 
 Em resumo, filtramos as decisões da seguinte maneira:

* Classificamos e selecionamos apenas os Recursos Especiais;
* Dessas decisões selecionamos apenas decisões monocráticas em Recursos Especiais;
* Dessas decisões selecionamos apenas aquelas que concederam (deram provimento) aos recursos;
* Organizamos os assuntos dessas decisões agrupamos as decisões em processos de Direito Civil e Direito Penal, excluindo os memais assuntos e processos que figuram em ambos (deixando um problema single label);
* Destes recursos selecionamos apenas aqueles que figuram em processo originários (isso é, julga o processo principal, não recursos agrupados);
* Destes recursos selecionamos apenas que possuem teor de total conhecimento e provimento, isso é, o recurso está formalmente perfeito nos aspectos processuais e teve o pedido de mérito deferido;
* Excluímos decisões proferidas de Ministros com poucos processos. Isso se deu pois, da maneira como aplicamos nosso filtro, recursos foram selecionados mesmo que proveniente de outras seções. Remover esses Ministros serve para minimizar esse ruído.
* Ao final selecionamos os 200 recursos mais recentes de cada classe, tornando o problema balanceado.
 
 
 
