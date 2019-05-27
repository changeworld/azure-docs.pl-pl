---
title: Zarządzanie klastrami usługi Azure HDInsight przy użyciu wiersza polecenia platformy Azure
description: Dowiedz się, jak zarządzać klastrami usługi Azure HDInsight przy użyciu wiersza polecenia platformy Azure. Typy klastrów obejmują Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query i usługi ML.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 7c12831c43762ddc776e8d5701f002be97992cbc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859964"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Zarządzanie klastrami usługi Azure HDInsight przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak używać [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) zarządzać klastrami usługi Azure HDInsight. Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) kroków.

* Klaster platformy Apache Hadoop w HDInsight. Zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać usługi Azure Cloud Shell, a następnie po prostu wybierz **wypróbuj** w prawym górnym rogu bloku kodu. W przeciwnym wypadku wprowadź poniższe polecenie:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Wyświetlanie listy klastrów

Użyj [lista hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) na wyświetlanie listy klastrów. Edytuj poniższe polecenia, zastępując `RESOURCE_GROUP_NAME` z nazwą grupy zasobów, a następnie wprowadź polecenia:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Pokaż klastra

Użyj [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) Aby wyświetlić informacje dotyczące określonego klastra. Edytuj poniższe polecenie, zastępując `RESOURCE_GROUP_NAME`, i `CLUSTER_NAME` z istotnymi informacjami, a następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Usuwanie klastrów

Użyj [usługa hdinsight az usunięcia](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) można usunąć określonego klastra. Edytuj poniższe polecenie, zastępując `RESOURCE_GROUP_NAME`, i `CLUSTER_NAME` z istotnymi informacjami, a następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Możesz także usunąć klaster, usuwając grupę zasobów zawierającą klaster. Należy zauważyć, że spowoduje to usunięcie wszystkich zasobów w grupie, w tym domyślne konto magazynu.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Użyj [rozmiar az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) zmiany rozmiaru określonego klastra HDInsight z określonym rozmiarem. Edytuj poniższe polecenie, zastępując `RESOURCE_GROUP_NAME`, i `CLUSTER_NAME` istotne informacje. Zastąp `TARGET_INSTANCE_COUNT` z żądaną liczbę węzłów procesu roboczego dla klastra. Aby uzyskać więcej informacji na temat skalowania klastrów, zobacz [klastrów HDInsight skalowania](./hdinsight-scaling-best-practices.md). Wpisz polecenie:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule mają przedstawiono sposób wykonywania różnych zadań administracyjnych klastra HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie HDInsight za pomocą programu Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
