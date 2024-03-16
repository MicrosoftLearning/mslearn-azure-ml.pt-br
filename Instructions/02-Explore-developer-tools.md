---
lab:
  title: Explorar as ferramentas de desenvolvedor para interação com o workspace
---

# Explorar as ferramentas de desenvolvedor para interação com o workspace

Você pode usar várias ferramentas para interagir com o workspace do Azure Machine Learning. Dependendo da tarefa que você precisa executar e da sua preferência pela ferramenta de desenvolvedor, você pode escolher quando usar cada ferramenta. Este laboratório foi projetado como uma introdução às ferramentas de desenvolvedor comumente usadas para a interação com o workspace. Se você quer aprender a usar uma ferramenta específica mais detalhadamente, existem outros laboratórios para explorar.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free?azure-portal=true) com acesso de nível administrativo.

As ferramentas de desenvolvedor mais usadas para interagir com o workspace do Azure Machine Learning são:

- **CLI do Azure** com a extensão do Azure Machine Learning: essa abordagem de linha de comando é ideal para a automação da infraestrutura.
- **Estúdio do Azure Machine Learning**: use a IU intuitiva para explorar o workspace e todos os seus recursos.
- **SDK do Python** para o Azure Machine Learning: use para enviar trabalhos e gerenciar modelos do Jupyter notebook, ideal para cientistas de dados.

Você explorará cada uma dessas ferramentas para tarefas que são comumente feitas com essa ferramenta.

## Provisionar a infraestrutura com a CLI do Azure

Para que um cientista de dados treine um modelo de aprendizado de máquina com o Azure Machine Learning, você precisará configurar a infraestrutura necessária. Você pode usar a CLI do Azure com a extensão do Azure Machine Learning para criar um workspace e recursos do Azure Machine Learning, como uma instância de computação.

Para começar, abra o Azure Cloud Shell, instale a extensão do Azure Machine Learning e copie o repositório Git.

1. Na guia do navegador, abra o portal do Azure em `https://portal.azure.com/` e entre com sua conta Microsoft.
1. Selecione o botão \[>_] (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Isso abre um painel do Cloud Shell na parte inferior do Portal.
1. Selecione **Bash** se solicitado. Na primeira vez que abrir o Cloud Shell, será solicitado que você escolha o tipo de shell que quer usar (*Bash* ou *PowerShell*).
1. Verifique se a assinatura correta está especificada e selecione **Criar armazenamento**, se for solicitado que você crie um armazenamento para o cloud shell. Aguarde até o armazenamento ser criado.
1. Remova todas as extensões ML da CLI (versão 1 e 2) para evitar conflitos com versões anteriores com este comando:
    
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

1. Aguarde até que o workspace e seus recursos associados sejam criados - isso normalmente leva cerca de 5 minutos.

## Criar uma instância de computação com a CLI do Azure

Outra parte importante da infraestrutura necessária para treinar um modelo de aprendizado de máquina é a **computação**. Embora você possa treinar modelos localmente, é mais escalável e econômico usar computação em nuvem.

Quando os cientistas de dados estão desenvolvendo um modelo de aprendizado de máquina no workspace do Azure Machine Learning, eles desejam usar uma máquina virtual na qual possam executar Jupyter notebooks. Para desenvolvimento, uma **instância de computação** é um ajuste ideal.

Depois de criar um workspace do Azure Machine Learning, você também pode criar uma instância de computação usando a CLI do Azure.

Neste exercício, você criará uma instância de computação com as seguintes configurações:

- **Nome de computação**: *nome da instância de computação. Tem que ser único e ter menos de 24 caracteres.*
- **Tamanho da máquina virtual**: STANDARD_DS11_V2
- **Tipo de computação** (instância ou cluster): ComputeInstance
- **Nome do workspace do Azure Machine Learning**: mlw-dp100-labs
- **Grupo de recursos**: rg-dp100-labs

1. Use o comando a seguir para criar uma instância de computação no seu workspace. Se o nome da instância de computação tiver "XXXX", substitua-o por números aleatórios para criar um nome exclusivo.

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    Se você receber uma mensagem de erro informando que uma instância de computação com o nome já existe, altere o nome e tente novamente o comando.

## Criar um cluster de computação com a CLI do Azure

Embora uma instância de computação seja ideal para desenvolvimento, um cluster de computação é mais adequado quando queremos treinar modelos de aprendizado de máquina. Somente quando um trabalho for enviado para usar o cluster de computação, ele será redimensionado para mais de 0 nós e executará o trabalho. Quando o cluster de computação não for mais necessário, ele será redimensionado automaticamente para 0 nós, minimizando os custos. 

Para criar um cluster de cálculo, você pode usar a CLI do Azure de forma semelhante à criação de uma instância de computação.

Você criará um cluster de computação com as seguintes configurações:

- **Nome da computação**: aml-cluster
- **Tamanho da máquina virtual**: STANDARD_DS11_V2
- **Tipo de computação**: AmlCompute *(Cria um cluster de computação)*
- **Máximo de instâncias**: *número máximo de nós*
- **Nome do workspace do Azure Machine Learning**: mlw-dp100-labs
- **Grupo de recursos**: rg-dp100-labs

1. Use o seguinte comando para criar um cluster de computação no seu workspace.
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## Configurar sua estação de trabalho com o estúdio do Azure Machine Learning

Embora a CLI do Azure seja ideal para automação, talvez você queira examinar a saída dos comandos executados. Você pode usar o estúdio do Azure Machine Learning para verificar se recursos e ativos foram criados e se os trabalhos foram executados com êxito ou examinar por que um trabalho falhou. 

1. No portal do Azure, navegue até o workspace do Azure Machine Learning nomeado **mlw-dp100-labs**.
1. Selecione o espaço de trabalho do Azure Machine Learning e, em sua página **Visão geral**, selecione **Iniciar estúdio**. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.
1. Feche todos os pop-ups que aparecem no estúdio.
1. No estúdio do Azure Machine Learning, navegue até a página **Computação** e verifique se a instância de computação e o cluster criados na seção anterior existem. A instância de computação deve estar em execução, o cluster deve estar ocioso e ter 0 nós em execução.

## Usar o SDK do Python para treinar um modelo

Agora que você verificou que a computação necessária foi criada, você pode usar o SDK do Python para executar um script de treinamento. Você instalará e usará o SDK do Python na instância de computação e treinará o modelo de machine learning no cluster de cálculo.

1. Selecione o aplicativo **Terminal** para sua **instância de computação** para iniciar o terminal.
1. No terminal, instale o SDK do Python na instância de computação executando os seguintes comandos no terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignore todas as mensagens (de erro) que dizem que os pacotes não foram instalados.

1. Execute o seguinte comando para clonar um repositório Git contendo notebooks, dados e outros arquivos em seu workspace:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Quando o comando for concluído, no painel **Arquivos**, selecione **↻** para atualizar a exibição e verifique se uma nova pasta ** Usuarios/*seu-nome-de-usuario/* azure-ml-labs** foi criada.
1. Abra o notebook **Labs/02/Run training script.ipynb**.

    > Selecione **Autenticar** e siga as etapas necessárias se aparecer uma notificação solicitando que você se autentique.

1. Verifique se o notebook usa o kernel **Python 3.8 - AzureML**. Cada kernel tem sua própria imagem com seu próprio conjunto de pacotes pré-instalados.
1. Execute todas as células no notebook.

Um novo trabalho será criado no workspace do Azure Machine Learning. O trabalho acompanha as entradas definidas na configuração do trabalho, o código usado e as saídas, como métricas, para avaliar o modelo.

## Examinar seu histórico de trabalhos no estúdio do Azure Machine Learning

Ao enviar um trabalho para o workspace do Azure Machine Learning, você pode examinar seu status no estúdio do Azure Machine Learning.

1. Selecione a URL do trabalho fornecida como saída no notebook ou navegue até a página **Trabalhos** no estúdio do Azure Machine Learning.
1. Um novo experimento é listado chamado **diabetes-training**. Selecione o trabalho **diabetes-pythonv2-train** mais recente.
1. Examine as **Propriedades** do trabalho. Observe o **Status** do trabalho:
    - **Enfileirado**: o trabalho está aguardando que a computação fique disponível.
    - **Preparação**: o cluster de computação está sendo redimensionado ou o ambiente está sendo instalado no destino de computação.
    - **Em execução**: o script de treinamento está sendo executado.
    - **Finalização**: o script de treinamento foi executado e o trabalho está sendo atualizado com todas as informações finais.
    - **Concluído**: o trabalho foi concluído com êxito e foi encerrado.
    - **Falhou**: o trabalho falhou e foi encerrado.
1. Em **Saídas + logs**, você encontrará a saída do script em **user_logs/std_log.txt**. As saídas das instruções de **impressão** no script serão mostradas aqui. Se houver um erro devido a um problema com seu script, você encontrará a mensagem de erro aqui também.
1. Em **Código**, você encontrará a pasta especificada na configuração do trabalho. Esta pasta inclui o script de treinamento e o conjunto de dados.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-labs**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**. 
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
