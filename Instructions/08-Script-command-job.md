---
lab:
  title: Executar um script de treinamento como um trabalho de comando no Azure Machine Learning
---

# Executar um script de treinamento como um trabalho de comando no Azure Machine Learning

Um notebook é ideal para experimentação e desenvolvimento. Após desenvolver um modelo de machine learning que esteja pronto para produção, você vai querer treiná-lo com um script. Você pode executar um script como um trabalho de comando.

Neste exercício, você testará um script e o executará como um trabalho de comando.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free?azure-portal=true) com acesso de nível administrativo.

## Provisionar um workspace do Azure Machine Learning

Um *workspace* do Azure Machine Learning fornece um local central para gerenciar todos os recursos e ativos necessários para treinar e gerenciar seus modelos. Você pode interagir com o workspace do Azure Machine Learning por meio do estúdio, do SDK do Python e da CLI do Azure.

Você usará a CLI do Azure para provisionar o espaço de trabalho e a computação necessária e usará o SDK do Python para executar um trabalho de comando.

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
    cd azure-ml-labs/Labs/08
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

## Converter um notebook em um script

O uso de um notebook anexado a uma instância de computação é ideal para experimentação e desenvolvimento, pois permite que você execute imediatamente o código que escreveu e examine sua saída. Para passar do desenvolvimento à produção, é importante que você use scripts. Como uma primeira etapa, você pode usar o estúdio do Azure Machine Learning para converter seu notebook em um script.

1. Abra o notebook **Labs/08/src/Train classification model.ipynb**.

    > Selecione **Autenticar** e siga as etapas necessárias se aparecer uma notificação solicitando que você se autentique.

1. Verifique se o notebook usa o kernel **Python 3.8 - AzureML**.
1. Executar todas as células para explorar o código e treinar um modelo.
1. Selecione o ícone ☰ na parte superior do notebook para exibir o **menu do notebook**.
1. Expanda **Exportar como** e selecione **Python (.py)** para converter o notebook em um script Python.
1. Nomeie o novo arquivo `train-classification-model`.
1. Depois que o novo arquivo é criado, o script deve abrir automaticamente. Explore o arquivo e observe que ele contém o mesmo código que o notebook.
1. Selecione o ícone ▷ ▷ na parte superior do notebook para **salvar e executar o script no terminal**.
1. O script é iniciado pelo comando **python train-classification-model.py** e a saída deve ser mostrada abaixo do comando.

## Teste um script no terminal.

Depois de converter um notebook em um script, talvez você queira refiná-lo ainda mais. Uma prática recomendada ao trabalhar com scripts é usar funções. Quando o script consiste em funções, é mais fácil realizar o teste de unidade no código. Quando você usa funções, seu script consiste em blocos de código, cada bloco executando uma tarefa específica.

1. Abra o script **Labs/08/src/train-model-parameters.py** e explore seu conteúdo.
    Observe que existe uma função principal que inclui quatro outras funções:

    - Ler dados
    - Dividir os dados
    - Treinar um modelo
    - Avaliar modelo

    Após a função principal, cada função é definida. Observe como cada função define a entrada e a saída esperadas.

1. Selecione o ícone ▷ ▷ na parte superior do notebook para **salvar e executar o script no terminal**. Você receberá uma mensagem de erro após ver **Lendo dados...** informando que não foi possível obter os dados porque o caminho do arquivo era inválido.

    Os scripts permitem que você parametrize seu código para alterar facilmente os dados ou parâmetros de entrada. Nesse caso, o script espera um parâmetro de entrada para o caminho de dados que não fornecemos. Você pode encontrar os parâmetros definidos e esperados no final do script na função **parse_args()**.

    Há dois parâmetros de entrada definidos:
    - **--training_data** que espera uma cadeia de caracteres.
    - **--reg_rate** que espera um número, mas tem um valor padrão de 0,01.

    Para executar o script com êxito, você precisará especificar o valor para os parâmetros de dados de treinamento. Vamos fazer isso nos referindo ao arquivo **diabetes.csv** que é armazenado na mesma pasta que o script de treinamento.

1. No terminal, execute os seguintes comandos:

    ```
    cd azure-ml-labs/Labs/08/src/
    python train-model-parameters.py --training_data diabetes.csv
    ```

O script deve ser executado com êxito e, como resultado, a saída deve mostrar a precisão e a AUC do modelo treinado.

Testar o script no terminal é ideal para verificar se o script funciona conforme o esperado. Se houver algum problema com o código, você receberá um erro no terminal.

**Opcionalmente**, edite o código para forçar um erro e execute o comando novamente no terminal para executar o script. Por exemplo, remova a linha **importar pandas como pd**, salve e execute o script com o parâmetro de entrada para examinar a mensagem de erro.

## Executar um script como um trabalho de comando

Se você souber que seu script funciona, poderá executá-lo como um trabalho de comando. Ao executar seu script como um trabalho de comando, você poderá acompanhar todas as entradas e saídas do script.

1. Abra o notebook **Labs/08/Run script as command job.ipynb**.
1. Execute todas as células no notebook.
1. No estúdio do Azure Machine Learning, navegue até a página **Trabalhos**.
1. Navegue até o trabalho **diabetes-train-script** para explorar a visão geral do trabalho de comando que você executou.
1. Navegue até guia **Código**. Todo o conteúdo da pasta **src**, que era o valor do parâmetro de **código** do trabalho de comando, foi copiado aqui. Você pode examinar o script de treinamento que foi executado pelo trabalho de comando.
1. Navegue até a guia **Saídas + logs**.
1. Abra o arquivo **std_log.txt** e explore seu conteúdo. O conteúdo deste arquivo é a saída do comando. Lembre-se que a mesma saída foi mostrada no terminal quando você testou o script lá. Se o trabalho não for bem-sucedido devido a um problema com o script, a mensagem de erro será mostrada aqui.

**Opcionalmente**, edite o código para forçar um erro e use o notebook para iniciar o trabalho de comando novamente. Por exemplo, remova a linha **importar pandas como pd** do script e salve o script. Ou edite a configuração do trabalho de comando para explorar as mensagens de erro quando algo estiver errado com a própria configuração do trabalho em vez do script.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-...**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
