# Relatório de IA e ML — Pet Cidadão

## Introdução e objetivo

O Pet Cidadão é um projeto voltado para a proteção e o bem-estar animal em São Paulo. O aplicativo permite registrar animais desaparecidos ou encontrados, fazer denúncias e consultar serviços de apoio aos animais.

Para a parte de IA e ML da Global Solution, resolvemos usar dados públicos do SP156, da Prefeitura de São Paulo, para investigar quanto tempo as solicitações relacionadas a animais levam para serem finalizadas.

Nossa ideia foi tentar prever se uma solicitação vai levar mais de 30 dias para ser encerrada, usando as informações que temos sobre ela. Escolhemos esses 30 dias como referência para nossa análise, e não como um prazo oficial da Prefeitura.

Com isso, queremos entender melhor esses pedidos e explorar como esse tipo de informação poderia ser útil para a proposta do Pet Cidadão.

## Dados utilizados

Os dados que usamos vieram do SP156, disponibilizados pela Prefeitura de São Paulo. Escolhemos o arquivo do primeiro trimestre de 2026, que contém 577.989 solicitações registradas nesse período.

Como nosso projeto é voltado para animais, filtramos os registros pelo tema "Animais" e encontramos 35.457 solicitações. Dessas, 34.293 já estavam finalizadas, então usamos esses registros para calcular quantos dias cada uma demorou.

Também descobrimos que 4.828 solicitações levaram mais de 30 dias para serem finalizadas.

Uma coisa que percebemos é que esse tema não envolve só cães e gatos. Ele também inclui assuntos como dengue e animais que transmitem doenças, então nossa análise acabou abrangendo outros tipos de solicitações.

## Preparação dos dados e escolha do modelo

Primeiro, calculamos quantos dias cada solicitação levou para ser finalizada, usando as datas de abertura e finalização. Depois, criamos uma coluna para identificar quais passaram de 30 dias, usando 0 para as que não passaram e 1 para as que passaram.

Para treinar o modelo, escolhemos informações como o assunto, serviço, meio de comunicação, mês e dia da semana em que a solicitação foi aberta. Também transformamos os textos em números, já que o modelo precisa trabalhar com esses dados.

Separamos 80% dos registros para treino e 20% para teste.

A gente escolheu o XGBoost porque foi um dos modelos que vimos nas aulas e ele pode ser usado para classificação, que é justamente o que estamos tentando fazer. Achamos que seria uma boa opção para trabalhar com as informações do SP156.

## Testes e resultados

Para testar o XGBoost, usamos o GridSearchCV, que testou 12 combinações diferentes de 3 hiperparâmetros, usando validação cruzada com 3 divisões apenas nos dados de treino.

A melhor configuração encontrada foi de 300 árvores, profundidade 4 e taxa de aprendizado de 0,1, com um F1 médio de 0,2183.

Quando fomos avaliar os resultados, percebemos que o modelo tinha bastante dificuldade para identificar as solicitações que demoraram mais de 30 dias. Ele conseguiu identificar apenas 92 das 966 solicitações desse tipo no teste.

Como tínhamos muito mais solicitações finalizadas em até 30 dias, resolvemos testar uma versão balanceada do XGBoost, dando mais importância às que demoraram mais.

O resultado melhorou bastante. O F1 médio na validação cruzada chegou a 0,5631 e, no teste, conseguimos identificar 846 das 966 solicitações demoradas.

Só que também tivemos mais erros. O modelo apontou 1.194 solicitações como demoradas quando elas foram finalizadas em até 30 dias. A acurácia geral no teste também caiu de 86% para 81%.

Comparando o treino e o teste do modelo balanceado, o F1 ficou em 0,57 e 0,56, respectivamente. Os resultados ficaram bem próximos, então não encontramos sinais de um overfitting muito grande.

<img width="691" height="374" alt="comparacao_f1_treino_teste" src="https://github.com/user-attachments/assets/28a0f19b-c77f-46ce-a674-136f9b3c53b3" />

## Conclusão

Com esse trabalho, conseguimos usar dados reais da Prefeitura de São Paulo para treinar um modelo que tenta identificar quais solicitações podem demorar mais de 30 dias para serem finalizadas.

No começo, o XGBoost teve dificuldade para identificar esses casos, mas os resultados melhoraram bastante quando ajustamos o peso das solicitações mais demoradas. Mesmo assim, o modelo ainda comete erros, principalmente quando prevê uma demora que não acontece.

Também é importante lembrar que estamos analisando o tempo de finalização das solicitações no SP156, e não necessariamente o tempo que um animal levou para ser resgatado ou atendido. Além disso, o tema "Animais" inclui outros assuntos, como zoonoses.

Acreditamos que essa análise pode complementar a proposta do Pet Cidadão, ajudando a entender melhor os registros relacionados aos animais e mostrando como o aprendizado de máquina pode ser aplicado a esse tipo de problema. Por enquanto, o modelo é apenas um experimento acadêmico e não está integrado ao aplicativo.
