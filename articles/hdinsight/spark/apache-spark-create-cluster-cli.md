---
title: 'Szybki Start: klastry Apache Spark z interfejsem wiersza polecenia platformy Azure — Azure HDInsight'
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia klastra Apache Spark w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: ac937519ca3fa9381fe4970f2e4336fe4a0a9227
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989335"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Szybki Start: Tworzenie klastra Apache Spark w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start dowiesz się, jak utworzyć klaster Apache Spark w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure. Platforma Apache Spark umożliwia szybką analizę danych i używanie klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. [Interfejs wiersza polecenia (CLI) platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) to międzyplatformowe środowisko wiersza polecenia firmy Microsoft służące do zarządzania zasobami platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Interfejs wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w celu wykonania kroków.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Tworzenie klastra platformy Apache Spark

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać Azure Cloud Shell, wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. W przeciwnym razie wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ustaw zmienne środowiskowe. Użycie zmiennych w tym przewodniku Szybki Start opiera się na bash. Inne środowiska będą wymagały niewielkich zmian. W poniższym fragmencie kodu Zastąp wartości RESOURCEGROUPNAME, LOCATION, CLUSTERname, STORAGEACCOUNTNAME i PASSWORD. Następnie wprowadź polecenie interfejsu wiersza polecenia, aby ustawić zmienne środowiskowe.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Utwórz grupę zasobów, wprowadzając następujące polecenie:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Utwórz konto usługi Azure Storage, wprowadzając następujące polecenie:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Wyodrębnij klucz podstawowy z konta usługi Azure Storage i Zapisz go w zmiennej, wprowadzając następujące polecenie:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Utwórz kontener usługi Azure Storage, wprowadzając następujące polecenie:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Utwórz klaster Apache Spark, wprowadzając następujące polecenie:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

Wprowadź wszystkie lub niektóre z następujących poleceń, aby usunąć zasoby:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra Apache Spark w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure.  Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchamiać interakcyjne zapytania dotyczące przykładowych danych za pomocą klastra Spark w usłudze HDInsight.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
