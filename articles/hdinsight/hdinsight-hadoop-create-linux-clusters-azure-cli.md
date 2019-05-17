---
title: Tworzenie klastrów usługi Apache Hadoop przy użyciu wiersza polecenia platformy Azure — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry HDInsight przy użyciu wiersza polecenia platformy Azure dla wielu platform.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597680"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Tworzenie klastrów HDInsight za pomocą wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroki opisane w tym przewodniku dokumentu, tworzenie klastra HDInsight 3.6 przy użyciu wiersza polecenia platformy Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Interfejs wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) kroków.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Tworzenie klastra

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać usługi Azure Cloud Shell, a następnie po prostu wybierz **wypróbuj** w prawym górnym rogu bloku kodu. W przeciwnym wypadku wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ustawianie zmiennych środowiskowych. Używanie zmiennych w tym artykule jest oparty na powłokę Bash. Niewielkie różnice, będzie potrzebna w innych środowiskach. Zobacz [tworzenia usługi hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) pełną listę możliwych parametrów dla tworzenia klastra.

    |Parametr | Opis |
    |---|---|
    |`--size`| Liczba węzłów procesu roboczego w klastrze. W tym artykule użyto zmiennej `clusterSizeInNodes` jako wartość przekazana do `--size`. |
    |`--version`| Wersja klastra HDInsight. W tym artykule użyto zmiennej `clusterVersion` jako wartość przekazana do `--version`. Zobacz też: [Obsługiwane wersje serwera HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Typ klastra HDInsight, takie jak: hadoop, interactivehive, hbase, kafka, storm, spark, program rserver, mlservices.  W tym artykule użyto zmiennej `clusterType` jako wartość przekazana do `--type`. Zobacz też: [Typy i konfiguracji klastra](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|Wersje różnych składników usługi Hadoop, w wersjach rozdzielonych spacjami w "component = wersja" format. W tym artykule użyto zmiennej `componentVersion` jako wartość przekazana do `--component-version`. Zobacz też: [Składniki usługi Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Zastąp `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`, i `PASSWORD` z odpowiednie wartości. Zmień wartości zmiennych zgodnie z potrzebami. Następnie wprowadź polecenia interfejsu wiersza polecenia.

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
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Utwórz grupę zasobów](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) , wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Aby uzyskać listę prawidłowych lokalizacji, należy użyć `az account list-locations` polecenia, a następnie użyj jednej z lokalizacji z `name` wartości.

4. [Tworzenie konta usługi Azure storage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) , wprowadzając poniższe polecenie:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Wyodrębnij klucz podstawowy z kontem usługi Azure storage](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) i zapisz go w zmiennej, wprowadzając poniższe polecenie:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Tworzenie kontenera usługi Azure storage](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) , wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Tworzenie klastra HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) , wprowadzając następujące polecenie:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight mogą mieć różne typy, które odnoszą się do obciążenia lub technologii, że klaster jest ona dostrojona dla klastrów. Nie istnieje obsługiwana metoda do tworzenia klastra, który łączy wiele typów, takich jak Storm i bazy danych HBase w jednym klastrze.

    Może upłynąć kilka minut na zakończenie procesu tworzenia klastra. Zwykle około 15.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z artykułu warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Wprowadź wszystkie lub niektóre z poniższych poleceń, aby usunąć zasoby:

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

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Kolejne kroki

Teraz, że udało Ci się utworzyć klaster usługi HDInsight przy użyciu wiersza polecenia platformy Azure, należy użyć następującego, aby dowiedzieć się, jak pracować z klastrem:

### <a name="apache-hadoop-clusters"></a>Klastry platformy Apache Hadoop

* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Rozpoczynanie pracy z usługą Apache HBase na HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Twórz aplikacje Java dla bazy danych Apache HBase na HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastrów Apache Storm

* [Opracowywanie topologii języka Java dla usługi Storm Apache na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Storm Apache na HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Storm Apache na HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
