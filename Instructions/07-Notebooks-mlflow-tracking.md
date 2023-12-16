---
lab:
  title: Acompanhar o treinamento de modelos em notebooks com o MLflow
---

# Acompanhar o treinamento de modelos em notebooks com o MLflow

Muitas vezes, você iniciará um novo projeto de ciência de dados experimentando e treinando vários modelos. Para rastrear seu trabalho e manter uma visão geral dos modelos que você treina e como funcionam, você pode usar o acompanhamento do MLflow.

Neste exercício, você usará o MLflow em um notebook em execução em uma instância de computação para registrar o treinamento de modelos.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free) com acesso de nível administrativo.

## Provisionar um workspace do Azure Machine Learning

Um *workspace* do Azure Machine Learning fornece um local central para gerenciar todos os recursos e ativos necessários para treinar e gerenciar seus modelos. Você pode interagir com o workspace do Azure Machine Learning por meio do estúdio, do SDK do Python e da CLI do Azure.

Você usará a CLI do Azure para provisionar o workspace e a computação necessária e usará o SDK do Python para treinar um modelo de classificação com o Machine Learning Automatizado.

### Criar o workspace e os recursos de computação

Para criar o workspace do Azure Machine Learning e uma instância de computação, você usará a CLI do Azure. Todos os comandos necessários são agrupados em um script do Shell para você executar.
1. Na guia do navegador, abra o portal do Azure em `https://portal.azure.com/` e entre com sua conta Microsoft.
1. Selecione o botão \[>_] (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Isso abre um painel do Cloud Shell na parte inferior do Portal.
1. Selecione **Bash** se solicitado. Na primeira vez que abrir o Cloud Shell, será solicitado que você escolha o tipo de shell que quer usar (*Bash* ou *PowerShell*).
1. Verifique se a assinatura correta está especificada e selecione **Criar armazenamento**, se for solicitado que você crie um armazenamento para o cloud shell. Aguarde até o armazenamento ser criado.
1. No terminal, execute os seguintes comandos para clonar este repositório:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Use `SHIFT + INSERT` para colar o código copiado no Cloud Shell. 

1. Depois que o repositório tiver sido clonado, digite os seguintes comandos para alterar para a pasta deste laboratório e execute o script **setup.sh** que ele contém:

    ```azurecli
    cd azure-ml-labs/Labs/07
    ./setup.sh
    ```

    > Ignore todas as mensagens de (erro) que dizem que as extensões não foram instaladas.

1. Aguarde a conclusão do script - isso normalmente leva cerca de 5 a 10 minutos.

## Clonar os materiais de laboratório

Depois de criar o workspace e os recursos de computação necessários, você pode abrir o estúdio do Azure Machine Learning e clonar os materiais de laboratório no workspace.

1. No portal do Azure, navegue até o workspace do Azure Machine Learning chamado **mlw-dp100-...**.
1. Selecione o workspace do Azure Machine Learning e, em sua página **Visão geral**, selecione **Iniciar estúdio**. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.
1. Feche todos os pop-ups que aparecem no estúdio.
1. No estúdio do Azure Machine Learning, navegue até a página **Computação** e verifique se a instância de computação criada na seção anterior existe. A instância de computação deve estar em execução.
1. Na guia **Instâncias de computação**, localize sua instância de computação e selecione o aplicativo **Terminal**.
1. No terminal, instale o SDK do Python e a biblioteca do MLflow na instância de computação executando os seguintes comandos:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mlflow
    ```

    > Ignore todas as mensagens (de erro) que dizem que os pacotes não puderam ser encontrados e desinstalados.

1. Execute o seguinte comando para clonar um repositório Git contendo um notebook, dados e outros arquivos para o seu workspace:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Quando o comando for concluído, no painel **Arquivos**, clique em **↻** para atualizar a exibição e verificar se uma nova pasta **Users/*your-user-name*/azure-ml-labs** foi criada.

## Acompanhar o treinamento de modelos com o MLflow

Agora que tem todos os recursos necessários, você pode executar o notebook para configurar e usar o MLflow ao treinar modelos em um notebook.

1. Abra o notebook **Labs/07/Track model training with MLflow.ipynb**.

    > Selecione **Autenticar** e siga as etapas necessárias se aparecer uma notificação solicitando que você se autentique.

1. Verifique se o notebook usa o kernel **Python 3.8 - AzureML**.
1. Execute todas as células no notebook.
1. Examine o novo trabalho criado sempre que você treinar um modelo.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-...**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
