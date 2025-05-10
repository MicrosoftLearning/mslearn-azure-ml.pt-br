---
lab:
  title: Disponibilizar dados no Azure Machine Learning
---

# Disponibilizar dados no Azure Machine Learning

Embora seja bastante comum trabalhar com dados em seu sistema de arquivos local, em um ambiente corporativo pode ser mais eficaz armazenar os dados em um local central onde vários cientistas de dados e engenheiros de aprendizado de máquina possam acessá-los.

Neste exercício, você vai explorar os *armazenamentos de dados* e os *ativos de dados*, que são os principais objetos usados para abstrair o acesso a dados no Azure Machine Learning.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free?azure-portal=true) com acesso de nível administrativo.

## Provisionar um workspace do Azure Machine Learning

Um *workspace* do Azure Machine Learning fornece um local central para gerenciar todos os recursos e ativos necessários para treinar e gerenciar seus modelos. Você pode interagir com o workspace do Azure Machine Learning por meio do estúdio, do SDK do Python e da CLI do Azure.

Você usará um script do Shell que usa a CLI do Azure para provisionar o workspace e os recursos necessários. Em seguida, você usará o Designer no estúdio do Azure Machine Learning para treinar e comparar modelos.

### Criar o espaço de trabalho e os recursos de computação

Para criar o workspace do Azure Machine Learning e recursos de computação, você usará a CLI do Azure. Todos os comandos necessários são agrupados em um script do Shell para você executar.

1. Na guia do navegador, abra o portal do Azure em `https://portal.azure.com/` e entre com sua conta Microsoft.
1. Selecione o botão \[>_] (*Cloud Shell*) na parte superior da página à direita da caixa de pesquisa. Isso abre um painel do Cloud Shell na parte inferior do Portal.
1. Selecione **Bash** se solicitado. Na primeira vez que abrir o Cloud Shell, será solicitado que você escolha o tipo de shell que quer usar (*Bash* ou *PowerShell*).
1. Verifique se a assinatura correta está especificada e se **Nenhuma conta de armazenamento necessária** está selecionada. Escolha **Aplicar**.
1. Insira os seguintes comandos no terminal para clonar o repositório:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Use `SHIFT + INSERT` para colar o código copiado no Cloud Shell.

1. Digite os seguintes comandos depois que o repositório tiver sido clonado para alterar para a pasta deste laboratório e execute o script **setup.sh** contido:

    ```azurecli
    cd azure-ml-labs/Labs/03
    ./setup.sh
    ```

    > Ignore todas as mensagens de (erro) que dizem que as extensões não foram instaladas.

1. Aguarde a conclusão do script - isso normalmente leva cerca de 5 a 10 minutos.

    <details>
    <summary><b>Dica de solução de problemas</b>: Erro de criação do espaço de trabalho</summary><br>
    <p>Se você receber um erro ao executar o script de instalação por meio da CLI, será necessário provisionar os recursos manualmente:</p>
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
        <li>Selecione <b>Go to resource</b> em sua página <b>Visão greal</b>, selecione <b>Launch studio</b>. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.</li>
        <li>Feche todos os pop-ups que aparecem no estúdio.</li>
        <li>No estúdio do Azure Machine Learning, navegue até a página <b>Compute</b> e selecione <b>+New</b> na guia <b>Compute instances</b>.</li>
        <li>Dê um nome exclusivo à instância de computação e selecione <b>Standard_DS11_v2</b> como o tamanho da máquina virtual.</li>
        <li>Selecione <b>Examinar + criar</b> e <b>Criar</b>.</li>
        <li>Em seguida, selecione a guia <b>Compute Clusters</b> e selecione <b>+New</b>.</li>
        <li>Escolha a mesma região em que você criou seu workspace e selecione <b>Standard_DS11_v2</b> como o tamanho da máquina virtual. Selecione <b>Avançar</b></li>
        <li>Dê ao cluster um nome exclusivo e selecione <b>Create</b>.</li>
    </ol>
    </details>

## Explorar os armazenamentos de dados padrão

Quando você cria um workspace do Azure Machine Learning, uma Conta de Armazenamento é automaticamente criada e conectada ao workspace. Você vai explorar como a Conta de Armazenamento é conectada.

1. No portal do Azure, navegue até o novo grupo de recursos nomeado **rg-dp100-...**.
1. Selecione a Conta de Armazenamento no grupo de recursos. O nome geralmente começa com o nome que você forneceu para o workspace (sem hífens).
1. Examine a página **Visão geral** da Conta de Armazenamento. Observe que a Conta de Armazenamento tem várias opções para **Armazenamento de dados**, conforme mostrado no painel Visão geral e no menu à esquerda.
1. Selecione **Contêineres** para explorar a parte de Armazenamento de Blobs da Conta de Armazenamento.
1. Observe o contêiner **azureml-blobstore-...**. O armazenamento de dados padrão para ativos de dados usa esse contêiner para armazenar dados.
1. Usando o botão + **Contêiner** na parte superior da tela, crie um novo contêiner e nomeie-o `training-data`.
1. Selecione **Compartilhamentos de arquivo** no menu à esquerda para explorar a parte de Compartilhamento de arquivo da Conta de Armazenamento.
1. Observe o compartilhamento de arquivo **code-...**. Todos os notebooks no workspace são armazenados aqui. Depois de clonar os materiais de laboratório, você pode encontrar os arquivos neste compartilhamento de arquivos, na pasta **code-.../Usuários/*seu-nome-de-usuário*/azure-ml-labs**.

## Copiar a chave de acesso

Para criar um armazenamento de dados no workspace do Azure Machine Learning, você precisa fornecer algumas credenciais. Uma maneira fácil de fornecer ao workspace acesso a um armazenamento de Blob é usar a chave de conta.

1. Na Conta de Armazenamento, selecione a guia **Chaves de acesso** no menu à esquerda.
1. Observe que duas chaves são fornecidas: key1 e key2. Cada chave tem a mesma funcionalidade. 
1. Selecione **Mostrar** para o campo **Chave** em **key1**.
1. Copie o valor do campo **Chave** para um bloco de notas. Você vai precisar colar esse valor no notebook mais tarde.
1. Copie o nome da sua conta de armazenamento da parte superior da página. O nome deve começar com **mlwdp100storage...**. Você precisará colar esse valor no notebook mais tarde também.

> **Observação**: copie o nome e a chave da conta para um bloco de notas para evitar o uso automático de maiúsculas (o que acontece no Word). A chave diferencia maiúsculas de minúsculas.

## Clonar os materiais de laboratório

Para criar um armazenamento de dados e ativos de dados com o SDK do Python, você precisará clonar os materiais de laboratório no workspace.

1. No portal do Azure, navegue até o workspace do Azure Machine Learning nomeado **mlw-dp100-labs**.
1. Selecione o espaço de trabalho do Azure Machine Learning e, em sua página **Visão geral**, selecione **Iniciar estúdio**. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.
1. Feche todos os pop-ups que aparecem no estúdio.
1. No estúdio do Azure Machine Learning, navegue até a página **Computação** e verifique se a instância de computação e o cluster criados na seção anterior existem. A instância de computação deve estar em execução, o cluster deve estar ocioso e ter 0 nós em execução.
1. Na guia **Instâncias de computação**, localize sua instância de computação e selecione o aplicativo **Terminal**.
1. No terminal, instale o SDK do Python na instância de computação executando os seguintes comandos no terminal:

    ```azurecli
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mltable
    ```

    > Ignore todas as mensagens (de erro) que dizem que os pacotes não foram instalados.

1. Execute o seguinte comando para clonar um repositório Git contendo notebooks, dados e outros arquivos em seu espaço de trabalho:

    ```azurecli
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Quando o comando for concluído, no painel **Arquivos**, clique em **↻** para atualizar a exibição e verificar se uma nova pasta **Users/*your-user-name*/azure-ml-labs** foi criada.

**Opcionalmente**, em outra guia do navegador, volte até o [portal do Azure](https://portal.azure.com?azure-portal=true). Explore novamente o compartilhamento de arquivo **code-...** na Conta de armazenamento para localizar os materiais de laboratório clonados na pasta recém-criada **azure-ml-labs**.

## Criar um armazenamento de dados e ativos de dados

O código para criar um armazenamento de dados e ativos de dados com o SDK do Python é fornecido em um notebook.

1. Abra o notebook **Labs/03/Work with data.ipynb** .

    > Selecione **Autenticar** e siga as etapas necessárias se aparecer uma notificação solicitando que você se autentique.

1. Verifique se o notebook usa o kernel **Python 3.10 — AzureML**.
1. Execute todas as células no notebook.

## Opcional: explore os ativos de dados

**Opcionalmente**, você pode explorar como os ativos de dados são armazenados na Conta de Armazenamento associada.

1. Navegue até a guia **Dados** no estúdio do Azure Machine Learning para explorar os ativos de dados.
1. Selecione o nome do ativo de dados **diabetes-local** para explorar seus detalhes. 

    Nas **Fontes de dados** para o ativo de dados **diabetes-local**, você encontrará o local para aonde o arquivo foi carregado. O caminho que começa com **LocalUpload/...** mostra o caminho dentro do contêiner da Conta de Armazenamento **azureml-blobstore-...**. Você pode verificar se o arquivo existe navegando até esse caminho no portal do Azure.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-...**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
