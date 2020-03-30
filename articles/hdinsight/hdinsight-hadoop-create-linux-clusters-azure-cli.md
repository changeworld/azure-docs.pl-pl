---
title: Tworzenie klastrów Apache Hadoop przy użyciu interfejsu wiersza polecenia platformy Azure — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry usługi Azure HDInsight przy użyciu wieloplatformowego interfejsu wiersza polecenia platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77199045"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Tworzenie klastrów usługi HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroki opisane w tym dokumencie— tworzenie klastra USŁUGI HDInsight 3.6 przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Interfejsu wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w celu uzyskania kroków.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Tworzenie klastra

1. Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać usługi Azure Cloud Shell, a następnie wybierz spróbuj **go** w prawym górnym rogu bloku kodu. W przeciwnym razie wprowadź poniższe polecenie:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ustawianie zmiennych środowiskowych. Użycie zmiennych w tym artykule opiera się na Bash. Niewielkie różnice będą potrzebne w innych środowiskach. Zobacz [az-hdinsight-create, aby](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) uzyskać pełną listę możliwych parametrów do tworzenia klastra.

    |Parametr | Opis |
    |---|---|
    |`--workernode-count`| Liczba węzłów procesu roboczego w klastrze. W tym artykule `clusterSizeInNodes` użyto zmiennej jako wartości przekazanej do `--workernode-count`. |
    |`--version`| Wersja klastra HDInsight. W tym artykule `clusterVersion` użyto zmiennej jako wartości przekazanej do `--version`. Zobacz też: [Obsługiwane wersje HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Typ klastra HDInsight, jak: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  W tym artykule `clusterType` użyto zmiennej jako wartości przekazanej do `--type`. Zobacz też: [Typy klastrów i konfiguracja](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Wersje różnych składników Hadoop, w wersjach oddzielonych przestrzenią w formacie 'component=version'. W tym artykule `componentVersion` użyto zmiennej jako wartości przekazanej do `--component-version`. Zobacz także: [Elementy Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    `RESOURCEGROUPNAME`Zamień `CLUSTERNAME` `STORAGEACCOUNTNAME`, `LOCATION` `PASSWORD` , i z żądanymi wartościami. Zmień wartości dla innych zmiennych zgodnie z potrzebami. Następnie wprowadź polecenia interfejsu wiersza polecenia.

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

3. [Utwórz grupę zasobów,](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Aby uzyskać listę prawidłowych `az account list-locations` lokalizacji, użyj polecenia, a następnie `name` użyj jednej z lokalizacji z wartości.

4. [Utwórz konto usługi Azure Storage,](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) wprowadzając poniższe polecenie:

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

5. [Wyodrębnij klucz podstawowy z konta usługi Azure Storage](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) i przechowuj go w zmiennej, wprowadzając poniższe polecenie:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Utwórz kontener usługi Azure Storage,](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) wprowadzając poniższe polecenie:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Utwórz klaster HDInsight,](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) wprowadzając następujące polecenie:

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

    > [!IMPORTANT]  
    > Klastry HDInsight są dostępne w różnych typach, które odpowiadają obciążeniu lub technologii, dla których jest dostrojony klaster. Nie ma żadnej obsługiwanej metody tworzenia klastra, który łączy wiele typów, takich jak Storm i HBase w jednym klastrze.

    Może upłynąć kilka minut, aby zakończyć proces tworzenia klastra. Zwykle około 15.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z artykułu warto usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

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

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy pomyślnie utworzono klaster HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure, skorzystaj z następujących czynności, aby dowiedzieć się, jak pracować z klastrem:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop klastrów

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z mapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do Apache HBase w programie HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla Apache HBase w programie HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastry Burzanych Apache

* [Opracowanie topologii Java dla Apache Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w aplikacji Apache Storm w programie HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii dzięki Apache Storm na hdinsight](storm/apache-storm-deploy-monitor-topology-linux.md)
