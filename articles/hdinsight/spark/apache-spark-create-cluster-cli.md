---
title: 'Szybki start: klastry Platformy Spark apache z interfejsem wiersza polecenia platformy Azure — usługa Azure HDInsight'
description: Ten przewodnik Szybki start pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia klastra Platformy Spark apache w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: e4679d5a04be7b8c0145fd93818e4187170b4194
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049682"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Szybki start: tworzenie klastra Platformy Spark apache w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Platformy Spark apache w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure (CLI). Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Struktura Apache Spark dla usługi HDInsight umożliwia szybką analizę danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Narzędzie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), jeśli nie chcesz używać usługi Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Tworzenie klastra platformy Apache Spark

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać usługi Azure Cloud Shell, wybierz pozycję **Wypróbuj ją** w prawym górnym rogu następującego bloku kodu. W przeciwnym razie wprowadź następujące polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ustawianie zmiennych środowiskowych. Użycie zmiennych w tym przewodniku Szybki start opiera się na bash. Niewielkie różnice będą potrzebne w innych środowiskach. Zastąp NAZWA RESOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME i HASŁO w poniższym urywek kodu żądanymi wartościami. Następnie wprowadź polecenia interfejsu wiersza polecenia, aby ustawić zmienne środowiskowe.

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

3. Utwórz grupę zasobów, wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Utwórz konto magazynu platformy Azure, wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Wyodrębnij klucz podstawowy z konta magazynu platformy Azure i przechowuj go w zmiennej, wprowadzając poniższe polecenie:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Utwórz kontener magazynu platformy Azure, wprowadzając poniższe polecenie:

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

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

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

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Platformy Spark apache w usłudze Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure.  Przejdź do następnego samouczka, aby dowiedzieć się, jak używać klastra HDInsight do uruchamiania zapytań interaktywnych na przykładowych danych.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
