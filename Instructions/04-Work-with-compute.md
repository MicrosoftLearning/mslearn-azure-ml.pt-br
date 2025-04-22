---
lab:
  title: Trabalhe com recursos de computação no Azure Machine Learning
---

# Trabalhe com recursos de computação no Azure Machine Learning

Um dos principais benefícios da nuvem é a capacidade de usar recursos de computação escalonáveis e sob demanda para o processamento econômico de grandes volumes de dados.

Neste exercício, você aprenderá a usar a computação de nuvem no Azure Machine Learning para executar testes de treinamento e código de produção em escala.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free?azure-portal=true) com acesso de nível administrativo.

## Provisionar um workspace do Azure Machine Learning

Um *workspace* do Azure Machine Learning fornece um local central para gerenciar todos os recursos e ativos necessários para treinar e gerenciar seus modelos. Você pode interagir com o workspace do Azure Machine Learning por meio do estúdio, do SDK do Python e da CLI do Azure.

Para criar o workspace do Azure Machine Learning, você usará a CLI do Azure. Todos os comandos necessários são agrupados em um script do Shell para você executar.

1. Na guia do navegador, abra o portal do Azure em `https://portal.azure.com/` e entre com sua conta Microsoft.
1. Selecione o botão \[>_] (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Isso abre um painel do Cloud Shell na parte inferior do Portal.
1. Selecione **Bash** se solicitado. Na primeira vez que abrir o Cloud Shell, será solicitado que você escolha o tipo de shell que quer usar (*Bash* ou *PowerShell*).
1. Verifique se a assinatura correta está especificada e se **Nenhuma conta de armazenamento necessária** está selecionada. Escolha **Aplicar**.
1. Para evitar conflitos com versões anteriores, remova todas as extensões ML da CLI (versão 1 e 2) executando este comando no terminal:

    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Use `SHIFT + INSERT` para colar o código copiado no Cloud Shell.

    > Ignore todas as mensagens de (erro) que dizem que as extensões não foram instaladas.

1. Instale a extensão do Azure Machine Learning (V2) com o seguinte comando:
    
    ```azurecli
    az extension add -n ml -y
    ```

1. Crie um grupos de recursos. Escolha um local perto de você.

    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. Criar um workspace:

    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. Aguarde a conclusão do comando - isso normalmente leva cerca de 5 a 10 minutos.

    <details>  
    <summary><b>Dica de solução de problemas</b>: Erro de criação do espaço de trabalho</summary><br>
    <p>Se você receber um erro ao criar um workspace por meio da CLI, será necessário provisionar o recurso manualmente:</p>
    <ol>
        <li>Na Página inicial do portal do Azure, selecione <b>+Criar um recurso</b>.</li>
        <li>Pesquise o <i>machine learning</i> e <b>Azure Machine Learning</b>. Selecione <b>Criar</b>.</li>
        <li>Crie um novo recurso do Azure Machine Learning com as seguintes configurações: <ul>
                <li><b>Assinatura</b>: <i>sua assinatura do Azure</i></li>
                <li><b>Grupo de recursos</b>: rg-dp100-labs</li>
                <li><b>Nome do espaço de trabalho</b>: mlw-dp100-labs</li>
                <li><b>Região</b>: <i>selecione a região geográfica mais próxima</i></li>
                <li><b>Conta de armazenamento</b>: <i>observe a nova conta de armazenamento padrão que será criada para o seu workspace</i></li>
                <li><b>Cofre de chaves</b>: <i>observe o novo cofre de chaves padrão que será criado para o seu workspace</i></li>
                <li><b>Application Insights</b>: <i>observe o novo recurso do Application Insights padrão que será criado para o seu workspace</i></li>
                <li><b>Registro de contêiner</b>: nenhum (<i>um será criado automaticamente quando você implantar um modelo em um contêiner pela primeira vez</i>)</li>
            </ul>
        <li>Selecione <b>Review + create</b> e aguarde até que o workspace e seus recursos associados sejam criados - isso normalmente leva cerca de 5 minutos.</li>
    </ol>
    </details>

## Criar o script de instalação de computação

Para executar notebooks no workspace do Azure Machine Learning, você precisará de uma instância de computação. Você pode usar um script de instalação para configurar a instância de computação na criação.

1. No portal do Azure, navegue até o workspace do Azure Machine Learning nomeado **mlw-dp100-labs**.
1. Selecione o espaço de trabalho do Azure Machine Learning e, em sua página **Visão geral**, selecione **Iniciar estúdio**. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.
1. Feche todos os pop-ups que aparecem no estúdio.
1. No estúdio do Azure Machine Learning, navegue até a página **Notebooks**.
1. No painel **Arquivos**, selecione o ícone ⨁ para **Adicionar arquivos**.
1. Selecione **Criar arquivo**.
1. Verifique se o local do arquivo é **Users/* your-user-name***.
1. Altere o tipo de arquivo para **Bash (*.sh)**.
1. Altere o nome de arquivo para `compute-setup.sh`.
1. Abra o arquivo recém-criado **compute-setup.sh** e cole o seguinte em seu conteúdo:

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Salve o arquivo **compute-setup.sh**.

## Criar a instância de computação

Para criar a instância de computação, você pode usar o studio, o SDK do Python ou a CLI do Azure. Você usará o estúdio para criar a instância de computação com o script de instalação que acabou de criar.

1. Navegue até a página **Computação**, usando o menu à esquerda.
1. Na guia **Instâncias de computação**, selecione **Nova**.
1. Configure (não crie ainda) a instância de computação com as seguintes configurações: 
    - **Nome de computação**: *insira um nome exclusivo*
    - **Tipo de máquina virtual**: *CPU*
    - **Tamanho da máquina virtual**: *Standard_DS11_v2*
1. Selecione **Avançar**.
1. Selecione **Adicionar agendamento** e configure o agendamento para **parar** a instância de computação todos os dias às **18:00** ou **6:00 PM**.
1. Selecione **Avançar**.
1. Examine as configurações de segurança, mas **não** as selecione:
    - **Habilitar acesso SSH**: *você pode usar isso para habilitar o acesso direto à máquina virtual usando um cliente SSH.*
    - **Habilitar rede virtual**: *você normalmente usaria isso em um ambiente corporativo para aprimorar a segurança da rede.*
    - **Atribuir a outro usuário**: *Você pode usar isso para atribuir uma instância de computação a outro cientista de dados.*
1. Selecione **Avançar**.
1. Selecione a alternância para **Provisionar com um script de criação**.
1. Selecione o script **compute-setup.sh** que você criou anteriormente.
1. Selecione **Examinar + Criar** para criar a instância de computação e aguarde até que ela seja iniciada e seu estado seja alterado para **Em Execução**.
1. Quando a instância de computação estiver em execução, navegue até a página **Notebooks**. No painel **Arquivos**, clique em **↻** para atualizar o modo de exibição e verificar se uma nova pasta **Users/*your-user-name*/dp100-azure-ml-labs** foi criada.

## Configurar a instância de computação

Depois de criar a instância de computação, você pode executar notebooks nela. Talvez seja necessário instalar determinados pacotes para executar o código desejado. Você pode incluir pacotes no script de instalação ou instalá-los usando o terminal.

1. Na guia **Instâncias de computação**, localize sua instância de computação e selecione o aplicativo **Terminal**.
1. No terminal, instale o SDK do Python na instância de computação executando os seguintes comandos no terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignore todas as mensagens (de erro) que dizem que os pacotes não foram instalados.

1. Quando os pacotes estiverem instalados, você pode fechar a guia para encerrar o terminal.

## Criar um cluster de cálculo

Os notebooks são ideais para desenvolvimento ou trabalho iterativo durante a experimentação. Ao experimentar, convém executar notebooks em uma instância de computação para testar e revisar rapidamente o código. Ao passar para a produção, convém executar scripts em um cluster de computação. Você criará um cluster de computação com o SDK do Python e o usará para executar um script como um trabalho.

1. Abra o notebook **Labs/04/Work with compute.ipynb**.

    > Selecione **Autenticar** e siga as etapas necessárias se aparecer uma notificação solicitando que você se autentique.

1. Verifique se o notebook usa o kernel **Python 3.8 - AzureML**.
1. Execute todas as células no notebook.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-labs**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
