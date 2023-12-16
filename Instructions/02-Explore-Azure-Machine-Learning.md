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

## Criar um pipeline de treinamento

Para explorar o uso dos ativos e recursos no workspace do Azure Machine Learning, vamos tentar treinar um modelo.

Uma maneira rápida de criar um pipeline de treinamento de modelo é usando o **Designer**.

> **Observação**: pop-ups podem aparecer para guiar você através do estúdio. Você pode fechar e ignorar todos os pop-ups e se concentrar nas instruções deste laboratório.

1. Selecione a página **Designer** no menu do lado esquerdo do estúdio.
1. Selecione a amostra **Regressão – Previsão de preços de automóveis (básico)** 

    Um novo pipeline é exibido. Na parte superior do pipeline, um componente é mostrado para carregar **Dados de preços de automóveis (brutos)**. O pipeline processa os dados e treina um modelo de regressão linear para prever o preço de cada automóvel.
1. Selecione **Configurar e Enviar** na parte superior da página para abrir a caixa de diálogo **Configurar trabalho de pipeline**
1. Na página **Básico**, selecione **Criar novo** e defina o nome do experimento como `train-regression-designer` e selecione **Avançar**.
1. Na página **Entradas e saídas**, selecione **Avançar** sem fazer alterações.
1. Na página **Configurações do runtime**, aparece um erro porque você não tem uma computação padrão para executar o pipeline.

Vamos criar um destino de computação.

## Criar um destino de computação

Para executar qualquer carga de trabalho no workspace do Azure Machine Learning, você precisará de um recurso de computação. Um dos benefícios do Azure Machine Learning é a capacidade de criar computação baseada em nuvem na qual você pode executar experimentos e scripts de treinamento em escala.

1. No estúdio do Azure Machine Learning, selecione a página de **Computação**, no menu do lado esquerdo. Existem quatro tipos de recursos de computação que você pode usar:
    - **Instâncias de computação**: uma máquina virtual gerenciada pelo Azure Machine Learning. Ideal para desenvolvimento quando você está explorando dados e experimentando iterativamente modelos de machine learning.
    - **Clusters de computação**: clusters escalonáveis de máquinas virtuais para processamento sob demanda de código de experimento. Ideal para executar código de produção ou trabalhos automatizados.
    - **Clusters Kubernetes**: um cluster Kubernetes usado para treinamento e pontuação. Ideal para implantação de modelos em tempo real e em larga escala.
    - **Computação anexada**: anexe seus recursos de computação do Azure existentes ao workspace, como Máquinas virtuais ou clusters do Azure Databricks.

    Para treinar um modelo de machine learning criado com o Designer, você pode usar uma instância de computação ou um cluster de computação.

2. Na guia **Instâncias de Computação**, adicione uma nova instância de computação com as configurações a seguir. 
    - **Nome de computação**: *insira um nome exclusivo*
    - **Localização**: *automaticamente a mesma localização que seu workspace*
    - **Tipo de máquina virtual**: `CPU`
    - **Tamanho da máquina virtual:**: `Standard_DS11_v2`
    - **Cota disponível**: mostra núcleos dedicados disponíveis.
    - **Mostrar configurações avançadas**: observe as seguintes configurações, mas não as selecione:
        - **Habilitar acesso SSH**: `Unselected` *(você pode usar isso para habilitar o acesso direto à máquina virtual usando um cliente SSH)*
        - **Habilitar rede virtual**: `Unselected` *(você normalmente usaria isso em um ambiente corporativo para aprimorar a segurança da rede)*
        - **Atribuir a outro usuário**: `Unselected` *(você pode usar isso para atribuir uma instância de computação a um cientista de dados)*
        - **Provisionamento com script de instalação**: `Unselected` *(você pode usar isso para adicionar um script para ser executado na instância remota quando criado)*
        - **Atribuir uma identidade gerenciada**: `Unselected` *(você pode anexar identidades gerenciadas atribuídas ao usuário ou ao sistema para permitir acesso aos recursos*

3. Selecione **Criar** e aguarde até que a instância de computação seja iniciada e seu estado seja alterado para **Em execução**.

> **Observação**: as instâncias de computação e os clusters de computação se baseiam em imagens padrão de máquina virtual do Azure. Para este exercício, a imagem *Standard_DS11_v2* é recomendada para atingir o equilíbrio ideal entre custo e desempenho. Se a sua assinatura tiver uma cota que não inclua essa imagem, escolha uma imagem alternativa. Mas tenha em mente que uma imagem maior pode gerar um custo maior e uma imagem menor pode não ser suficiente para concluir as tarefas. Como alternativa, peça ao administrador do Azure para estender sua cota.

## Executar o pipeline de treinamento

Você criou um destino de computação e agora pode executar seu pipeline de treinamento de amostra no Designer.

1. Navegue até a página **Designer**.
1. Selecione o esboço do pipeline **Regressão - Previsão de preços de automóveis (Básico)**.
1. Selecione **Configurar e Enviar** na parte superior da página para abrir a caixa de diálogo **Configurar trabalho de pipeline**
1. Na página **Básico**, selecione **Criar novo** e defina o nome do experimento como `train-regression-designer` e selecione **Avançar**.
1. Na página **Entradas e saídas**, selecione **Avançar** sem fazer alterações.
1. Nas **Configurações de runtime**, na lista suspensa **Selecionar tipo de computação**, selecione *Instância de computação* e, na lista suspensa **Selecionar instância de computação de ML do Azure**, selecione sua instância de computação recém-criada.
1. Selecione **Examinar + Enviar** para examinar o trabalho do pipeline e, em seguida, selecione **Enviar** para executar o pipeline de treinamento.

O pipeline de treinamento agora será enviado para a instância de computação. Levará aproximadamente 10 minutos para o pipeline ser concluído. Enquanto isso, vamos explorar algumas outras páginas.

## Usar trabalhos para visualizar seu histórico

Sempre que você executa um script ou pipeline no workspace do Azure Machine Learning, ele é registrado como um **trabalho**. Os trabalhos permitem que você acompanhe as cargas de trabalho executadas e as compare entre si. Os trabalhos pertencem a um **experimento**, que permite agrupar execuções de trabalho juntas.

1. Navegue até a página **Trabalhos**, usando o menu no lado esquerdo do estúdio do Azure Machine Learning.
1. Selecione o experimento **train-regression-designer** para exibir suas execuções de trabalho. Aqui, você terá uma visão geral de todos os trabalhos que fazem parte deste experimento. Se você executou vários pipelines de treinamento, essa exibição permite comparar os pipelines e identificar o melhor.
1. Selecione o último trabalho no experimento **train-regression-designer**.
1. Observe que o pipeline de treinamento é mostrado onde você pode exibir quais componentes foram executados com êxito ou falharam. Se o trabalho ainda estiver em execução, você também poderá identificar o que está sendo executado no momento.
1. Para exibir os detalhes do trabalho de pipeline, selecione a **Visão geral** do trabalho no canto superior direito para expandir a **Visão geral do trabalho de pipeline**.
1. Observe que nos parâmetros **Visão geral**, você pode encontrar o status do trabalho, quem criou o pipeline, quando ele foi criado e quanto tempo levou para executar o pipeline completo (entre outras coisas).

    Ao executar um script ou pipeline como um trabalho, você pode definir quaisquer entradas e documentar quaisquer saídas. O Azure Machine Learning também controla automaticamente as propriedades do seu trabalho. Ao usar trabalhos, você pode facilmente visualizar seu histórico para entender o que você ou seus colegas já fizeram.

    Durante a experimentação, os trabalhos ajudam a acompanhar os diferentes modelos que você treina para comparar e identificar o melhor modelo. Durante a produção, os trabalhos permitem verificar se as cargas de trabalho automatizadas foram executadas conforme o esperado.

1. Quando o trabalho for concluído, você também poderá exibir os detalhes de cada execução de componente individual, incluindo a saída. Sinta-se à vontade para explorar o pipeline para entender como o modelo é treinado.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-labs**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
