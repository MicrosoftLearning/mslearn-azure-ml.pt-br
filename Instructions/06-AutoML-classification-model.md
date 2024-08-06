---
lab:
  title: Encontrar o melhor modelo de classificação com o Machine Learning Automatizado
---

# Encontrar o melhor modelo de classificação com o Machine Learning Automatizado

Determinar o algoritmo certo e as transformações de pré-processamento para o treinamento do modelo podem envolver muita adivinhação e experimentação.

Neste exercício, você usará o aprendizado de máquina automatizado para determinar o algoritmo ideal e as etapas de pré-processamento para um modelo, realizando várias execuções de treinamento em paralelo.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free?azure-portal=true) com acesso de nível administrativo.

## Provisionar um workspace do Azure Machine Learning

Um *workspace* do Azure Machine Learning fornece um local central para gerenciar todos os recursos e ativos necessários para treinar e gerenciar seus modelos. Você pode interagir com o workspace do Azure Machine Learning por meio do estúdio, do SDK do Python e da CLI do Azure.

Você usará a CLI do Azure para provisionar o workspace e a computação necessária e usará o SDK do Python para treinar um modelo de classificação com o Machine Learning Automatizado.

### Criar o workspace e os recursos de computação

Par criar o workspace do Azure Machine Learning, uma instância de computação e um cluster de computação, você usará a CLI do Azure. Todos os comandos necessários são agrupados em um script do Shell para você executar.

1. Na guia do navegador, abra o portal do Azure em `https://portal.azure.com/` e entre com sua conta Microsoft.
1. Selecione o botão \[>_] (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Isso abre um painel do Cloud Shell na parte inferior do Portal.
1. Selecione **Bash** se solicitado. Na primeira vez que abrir o Cloud Shell, será solicitado que você escolha o tipo de shell que quer usar (*Bash* ou *PowerShell*).
1. Verifique se a assinatura correta está especificada e se **Nenhuma conta de armazenamento necessária** está selecionada. Escolha **Aplicar**.
1. No terminal, execute os seguintes comandos para clonar este repositório:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Use `SHIFT + INSERT` para colar o código copiado no Cloud Shell.

1. Depois que o repositório tiver sido clonado, digite os seguintes comandos para alterar para a pasta deste laboratório e execute o script **setup.sh** que ele contém:

    ```azurecli
    cd azure-ml-labs/Labs/06
    ./setup.sh
    ```

    > Ignore todas as mensagens de (erro) que dizem que as extensões não foram instaladas.

1. Aguarde a conclusão do script - isso normalmente leva cerca de 5 a 10 minutos.

## Clonar os materiais de laboratório

Depois de criar o workspace e os recursos de computação necessários, você pode abrir o estúdio do Azure Machine Learning e clonar os materiais de laboratório no workspace.

1. No portal do Azure, navegue até o workspace do Azure Machine Learning chamado **mlw-dp100-...**.
1. Selecione o workspace do Azure Machine Learning e, em sua página **Visão geral**, selecione **Iniciar estúdio**. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.
1. Feche todos os pop-ups que aparecem no estúdio.
1. No estúdio do Azure Machine Learning, navegue até a página **Computação** e verifique se a instância de computação e o cluster criados na seção anterior existem. A instância de computação deve estar em execução, o cluster deve estar ocioso e ter 0 nós em execução.
1. Na guia **Instâncias de computação**, localize sua instância de computação e selecione o aplicativo **Terminal**.
1. No terminal, instale o SDK do Python na instância de computação executando os seguintes comandos no terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignore todas as mensagens (de erro) que dizem que os pacotes não puderam ser encontrados e desinstalados.

1. Execute o seguinte comando para clonar um repositório Git contendo notebooks, dados e outros arquivos em seu workspace:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Quando o comando for concluído, no painel **Arquivos**, clique em **↻** para atualizar a exibição e verificar se uma nova pasta **Users/*your-user-name*/azure-ml-labs** foi criada.

## Treine um modelo de classificação com o machine learning automatizado

Agora que você tem todos os recursos necessários, pode executar o notebook para configurar e enviar o trabalho do Machine Learning Automatizado.

1. Abra o notebook **Labs/06/Classification with Automated Machine Learning.ipynb**.

    > Selecione **Autenticar** e siga as etapas necessárias se aparecer uma notificação solicitando que você se autentique.

1. Verifique se o notebook usa o kernel **Python 3.8 - AzureML**.
1. Execute todas as células no notebook.

    Um novo trabalho será criado no workspace do Azure Machine Learning. O trabalho controla as entradas definidas na configuração do trabalho, o ativo de dados usado e as saídas, como métricas, para avaliar os modelos.

    Observe que os trabalhos do Machine Learning Automatizado contêm trabalhos filho, que representam modelos individuais que foram treinados e outras tarefas necessárias a ser executadas.
1. Vá para **Trabalhos** e selecione o experimento **auto-ml-class-dev**.
1. Na coluna **Nome de exibição**, selecione o trabalho.
1. Aguarde até que seu status seja alterado para **Concluído**.
1. Quando o status do trabalho Automatizar Machine Learning for alterado para **Concluído**, explore os detalhes do trabalho no estúdio:
    - A guia **Verificadores de integridade dos dados** mostra se os dados de treinamento apresentaram algum problema.
    - A guia **Modelos** mostrará todos os modelos que foram treinados. Selecione **Exibir explicação** para o melhor modelo para entender quais recursos influenciaram mais o valor de destino.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-...**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
