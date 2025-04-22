---
lab:
  title: Explorar o workspace do Azure Machine Learning
---

# Explorar o workspace do Azure Machine Learning

O Azure Machine Learning oferece uma plataforma de ciência de dados para treinar e gerenciar modelos de machine learning. Neste laboratório, você criará um workspace do Azure Machine Learning e explorará as várias maneiras de trabalhar com o workspace. O laboratório foi projetado como uma introdução dos vários recursos principais do Azure Machine Learning e das ferramentas de desenvolvedor. Se você quiser saber mais detalhes sobre os recursos, há outros laboratórios para explorar.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free?azure-portal=true) com acesso de nível administrativo.

## Provisionar um workspace do Azure Machine Learning

Um **workspace** do Azure Machine Learning fornece um local central para gerenciar todos os recursos e ativos necessários para treinar e gerenciar seus modelos. Você pode provisionar um workspace usando a interface interativa no portal do Azure ou pode usar a CLI do Azure com a extensão do Azure Machine Learning. Na maioria dos cenários de produção, é melhor automatizar o provisionamento com a CLI para que você possa incorporar a implantação de recursos em um processo repetível de desenvolvimento e operações (*DevOps*). 

Neste exercício, você usará o portal do Azure para provisionar o Azure Machine Learning para explorar todas as opções.

1. Entre no `https://portal.azure.com/`.
2. Crie um novo recurso do **Azure Machine Learning** com as seguintes configurações:
    - **Assinatura**: *sua assinatura do Azure*
    - **Grupo de recursos**: `rg-dp100-labs`
    - **Nome do workspace**: `mlw-dp100-labs`
    - **Região**: *selecione a região geográfica mais próxima*
    - **Conta de armazenamento**: *observe a nova conta de armazenamento padrão que será criada para o seu workspace*
    - **Cofre de chaves**: *observe o novo cofre de chaves padrão que será criado para o seu workspace*
    - **Application Insights**: *observe o novo recurso do Application Insights padrão que será criado para o seu workspace*
    - **Registro de contêiner**: nenhum (*um será criado automaticamente quando você implantar um modelo em um contêiner pela primeira vez*)
3. Aguarde até que o workspace e seus recursos associados sejam criados - isso normalmente leva cerca de 5 minutos.

> **Observação**: ao criar um workspace do Azure Machine Learning, você pode usar algumas opções avançadas para restringir o acesso por meio de um *ponto de extremidade privado* e especificar chaves personalizadas para criptografia de dados. Não usaremos essas opções neste exercício - mas você deve estar ciente delas!

## Explorar o estúdio do Azure Machine Learning

O *estúdio do Azure Machine Learning* é um portal baseado na Web por meio do qual você pode acessar o workspace do Azure Machine Learning. Você pode usar o estúdio do Azure Machine Learning para gerenciar todos os ativos e recursos no seu workspace.

1. Vá para o grupo de recursos chamado **rg-dp100-labs**.
1. Confirme que seu grupo de recursos contém seu workspace do Azure Machine Learning, Application Insights, Cofre de Chaves e uma Conta de armazenamento.
1. Selecione seu workspace do Azure Machine Learning.
1. Na página **Visão geral**, selecione **Iniciar estúdio**. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.
1. Feche todos os pop-ups que aparecem no estúdio.
1. Observe as diferentes páginas mostradas no lado esquerdo do estúdio. Se apenas os símbolos estiverem visíveis no menu, selecione o ícone ☰ para expandir o menu e explorar os nomes das páginas.
1. Observe a seção **Criação**, que inclui **Notebooks**, **ML Automatizado** e **Designer**. Essas são as três maneiras pelas quais você pode criar seus próprios modelos de machine learning no estúdio do Azure Machine Learning.
1. Observe a seção **Ativos**, que inclui **Dados**, **Trabalhos** e **Modelos**, entre outras coisas. Os ativos são consumidos ou criados ao treinar ou pontuar um modelo. Os ativos são usados para treinar, implantar e gerenciar seus modelos e podem ter a versão para acompanhar seu histórico.
1. Observe a seção **Gerenciar**, que inclui **Computação**, entre outras coisas. Esses são recursos de infraestrutura necessários para treinar ou implantar um modelo de machine learning.

## Treinar um modelo usando o AutoML

Para explorar o uso dos ativos e recursos no workspace do Azure Machine Learning, vamos tentar treinar um modelo.

Uma maneira rápida de treinar e encontrar o melhor modelo para uma tarefa com base em seus dados é usando a opção **AutoML** .

> **Observação**: pop-ups podem aparecer para guiar você através do estúdio. Você pode fechar e ignorar todos os pop-ups e se concentrar nas instruções deste laboratório.

1. Baixe os dados de treinamento que serão usados e `https://github.com/MicrosoftLearning/mslearn-azure-ml/raw/refs/heads/main/Labs/02/diabetes-data.zip`extraia os arquivos compactados.
1. No Estúdio do Azure Machine Learning, selecione a página **AutoML**, no menu do lado esquerdo do estúdio.
1. Selecione **+ New Automated ML job**.
1. Na etapa **Configurações básicas**, dê um nome exclusivo ao seu trabalho de treinamento e experimente ou use os valores padrão atribuídos. Selecione **Avançar**.
1. Na etapa **Tipo de tarefa e dados**, selecione **Classification** como o tipo de tarefa e selecione **+ Create** para adicionar seus dados de treinamento.
2. Na página **Criar ativo de dados**, na etapa **Tipo de dados** dê um nome ao ativo de dados (por exemplo `training-data`, ) e selecione **Avançar**.
1. Na etapa **Fonte de dados**, selecione **From local files** para fazer upload dos dados de treinamento que você baixou anteriormente. Selecione **Avançar**.
1. Na etapa **Tipo de armazenamento de destino**, confira se o **Azure Blob Storage** está selecionado como o tipo de armazenamento de dados e que **workspaceblobstore** é o armazenamento de dados selecionado. Selecione **Avançar**.
1. Na etapa de **seleção do MLTable**, selecione **Upload folder** e selecione a pasta que você extraiu do arquivo compactado baixado anteriormente. Selecione **Avançar**.
1. Examine as configurações do seu ativo de dados e selecione **Criar**.
1. De volta à etapa **Tipo de tarefa e dados**, selecione os dados que você acabou de carregar e selecione **Avançar**.

> **Dica**: Pode ser necessário selecionar o tipo de tarefa em **Classification** novamente antes de mover para a próxima etapa.

1. Na etapa **Configurações de tarefa**, selecione **Diabético (Booleano)** como sua coluna de destino e, em seguida, abra a opção **Visualizar configurações adicionais**.
1. No painel **Additional configuration**, altere a métrica primária para **Accuracy**, em seguida, selecione **Salvar**.
1. Expanda a opção **Limits** e defina as seguintes propriedades:
    * **Avaliações máximas**: 10
    * **Tempo limite do experimento (em minutos)**: 60
    * **Tempo limite da iteração (em minutos)**: 15
    * **Habilitar o encerramento antecipado**: verificado

1. Para **Test data**, selecione **Train-test split** e verifique se o **Teste de porcentagem de dados** é 10. Selecione **Avançar**.
1. Na etapa **Computação**, verifique se o tipo de computação é **Sem servidor** e o tamanho da máquina virtual selecionado é **Standard_DS3_v2**. Selecione **Avançar**.

> **Observação**: as instâncias de computação e os clusters de computação se baseiam em imagens padrão de máquina virtual do Azure. Para este exercício, a imagem *Standard_DS3_v2* é recomendada para atingir o equilíbrio ideal entre custo e desempenho. Se a sua assinatura tiver uma cota que não inclua essa imagem, escolha uma imagem alternativa. Mas tenha em mente que uma imagem maior pode gerar um custo maior e uma imagem menor pode não ser suficiente para concluir as tarefas. Como alternativa, peça ao administrador do Azure para estender sua cota.

1. Revise as configurações e selecione **Submit training job**.

## Usar trabalhos para visualizar seu histórico

Depois de enviar o trabalho, você será redirecionado para a página do trabalho. Os trabalhos permitem que você acompanhe as cargas de trabalho executadas e as compare entre si. Os trabalhos pertencem a um **experimento**, que permite agrupar execuções de trabalho juntas. 

1. Observe que nos parâmetros **Visão geral**, você pode encontrar o status do trabalho, quem o criou, quando foi criado e quanto tempo levou para ser executado (entre outras coisas).
1. Deve levar de 10 a 20 minutos para que o trabalho de treinamento termine. Quando estiver concluído, você também pode visualizar os detalhes de cada componente individual da execução, incluindo a saída. Sinta-se à vontade para explorar a página do trabalho para entender como os modelos são treinados.

    O Azure Machine Learning também controla automaticamente as propriedades do seu trabalho. Ao usar trabalhos, você pode facilmente visualizar seu histórico para entender o que você ou seus colegas já fizeram.
    Durante a experimentação, os trabalhos ajudam a acompanhar os diferentes modelos que você treina para comparar e identificar o melhor modelo. Durante a produção, os trabalhos permitem verificar se as cargas de trabalho automatizadas foram executadas conforme o esperado.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-labs**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
