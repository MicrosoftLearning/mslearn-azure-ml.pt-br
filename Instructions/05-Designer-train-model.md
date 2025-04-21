---
lab:
  title: Treinar um modelo com o designer do Azure Machine Learning
---

# Treinar um modelo com o designer do Azure Machine Learning

O designer do Azure Machine Learning fornece uma interface de arrastar e soltar com a qual você pode definir um fluxo de trabalho. Você pode criar um fluxo de trabalho para treinar um modelo, testar e comparar vários algoritmos com facilidade.

Neste exercício, você usará o Designer para treinar e comparar rapidamente dois algoritmos de classificação.

## Antes de começar

É necessário ter uma [assinatura do Azure](https://azure.microsoft.com/free?azure-portal=true) com acesso de nível administrativo.

## Provisionar um workspace do Azure Machine Learning

Um *workspace* do Azure Machine Learning fornece um local central para gerenciar todos os recursos e ativos necessários para treinar e gerenciar seus modelos. Você pode interagir com o workspace do Azure Machine Learning por meio do estúdio, do SDK do Python e da CLI do Azure.

Você usará um script do Shell que usa a CLI do Azure para provisionar o workspace e os recursos necessários. Em seguida, você usará o Designer no estúdio do Azure Machine Learning para treinar e comparar modelos.

### Criar o workspace e o cluster de computação

Você usará a CLI do Azure para criar o workspace do Azure Machine Learning e um cluster de computação. Todos os comandos necessários são agrupados em um script do Shell para você executar.

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

1. Depois que o repositório tiver sido clonado, insira os seguintes comandos para alterar para a pasta deste laboratório e execute o script setup.sh contido nela:

    ```azurecli
    cd azure-ml-labs/Labs/05
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

## Configurar um novo pipeline

Depois de criar o workspace e o cluster de computação necessário, você pode abrir o estúdio do Azure Machine Learning e criar um pipeline de treinamento com o Designer.

1. No portal do Azure, navegue até o workspace do Azure Machine Learning chamado **mlw-dp100-...**.
1. Selecione o workspace do Azure Machine Learning e, em sua página **Visão geral**, selecione **Iniciar estúdio**. Outra guia será aberta em seu navegador para abrir o estúdio do Azure Machine Learning.
1. Feche todos os pop-ups que aparecem no estúdio.
1. No estúdio do Azure Machine Learning, navegue até a página **Computação** e verifique se o cluster de computação criado na seção anterior existe. O cluster deve estar ocioso e ter 0 nós em execução.
1. Navegue até a página **Designer**.
1. Selecione a guia **Personalizado** na parte superior da página.
1. Crie um novo pipeline vazio usando componentes personalizados.
1. Altere o nome padrão do pipeline (**Pipeline-Created-on-* date***) para `Train-Diabetes-Classifier` selecionando o ícone de lápis à direita.


## Criar um novo pipeline

Para treinar um modelo, você precisará de dados. Você pode usar quaisquer dados armazenados em um armazenamento de dados ou usar uma URL acessível publicamente.

1. No menu à esquerda, selecione a guia **Dados**.
1. Arraste o componente **diabetes-folder** para a tela.

    Agora que você tem seus dados, pode continuar criando um pipeline usando componentes personalizados que já existem no workspace (criados durante a instalação).

1. No menu esquerdo, selecione a guia **Componentes**.
1. Arraste o componente **Remover linhas vazias** para a tela, abaixo de **diabetes-folder**.
1. Conecte a saída dos dados à entrada do novo componente.
1. Arraste o componente **Normalizar colunas numéricas** para a tela, abaixo de **Remover linhas vazias**.
1. Conecte a saída do componente anterior à entrada do novo componente.
1. Arraste o componente **Treinar um modelo classificador de árvore de decisão** para a tela, abaixo de **Normalizar colunas numéricas**.
1. Conecte a saída do componente anterior à entrada do novo componente.
1. Selecione **Configurar e Enviar** e, na página **Configurar trabalho de pipeline**, crie um novo experimento, dê o nome de `diabetes-designer-pipeline` e selecione **Avançar**.
1. Em **Entradas e saídas**, não faça alterações e selecione **Avançar**.
1. Em **Configurações do runtime**, selecione **Cluster de computação** e, em **Selecionar cluster de computação do Azure ML**, selecione seu *aml-cluster*.
1. Selecione **Revisar + Enviar** e selecione **Enviar** para iniciar a execução do pipeline.
1. Você pode verificar o status da execução acessando a página **Pipelines** e selecionando o pipeline **Train-Diabetes-Classifier**.
1. Aguarde até que todos os componentes tenham sido concluídos com êxito.

    O envio do trabalho inicializará o cluster de computação. Como o cluster de computação estava ocioso até agora, pode levar algum tempo para que o cluster seja redimensionado para mais de 0 nós. Depois que o cluster for redimensionado, ele começará a executar automaticamente o pipeline.

Você poderá acompanhar a execução de cada componente. Quando o pipeline falhar, você poderá explorar qual componente falhou e por que falhou. As mensagens de erro serão exibidas na guia **Saídas + logs** da visão geral do trabalho.

## Treinar um segundo modelo para comparação

Para comparar entre algoritmos e avaliar qual tem melhor desempenho, você pode treinar dois modelos dentro de um pipeline e fazer a comparação.

1. Retorne ao **Designer** e selecione o rascunho de pipeline **Train-Diabetes-Classifier**.
1. Adicione o componente **Treinar um Modelo Classificador de Regressão Logística** à tela, ao lado do outro componente de treinamento.
1. Conecte a saída do componente **Normalizar Colunas Numéricas** à entrada do novo componente de treinamento.
1. Na parte superior, selecione **Configurar e Enviar**.
1. Na página **Básico**, crie um novo experimento chamado `designer-compare-classification` e execute-o.
1. Selecione **Revisar + Enviar** e selecione **Enviar** para iniciar a execução do pipeline.
1. Você pode verificar o status da execução acessando a página **Pipelines** e selecionando o pipeline **Train-Diabetes-Classifier** com o experimento **designer-compare-classification**.
1. Aguarde até que todos os componentes tenham sido concluídos com êxito.  
1. Selecione **Visão geral do trabalho** e selecione a guia **Métricas** para revisar os resultados de ambos os componentes de treinamento.
1. Tente determinar qual modelo teve melhor desempenho.

## Excluir recursos do Azure

Se você terminou de explorar o Azure Machine Learning, exclua os recursos que criou para evitar custos desnecessários do Azure.

1. Feche a guia do estúdio do Azure Machine Learning e retorne ao portal do Azure.
1. No portal do Azure, na **Página Inicial**, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos **rg-dp100-...**.
1. Na parte superior da página de **Visão Geral** do grupo de recursos, selecione **Excluir o grupo de recursos**.
1. Digite o nome do grupo de recursos para confirmar que deseja excluí-lo e selecione **Excluir**.
