---
title: Zarządzanie klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do zarządzania klastrami usługi Azure HDInsight. Typy klastrów obejmują Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query i ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272775"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Zarządzanie klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak używać [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) do zarządzania klastrami usługi Azure HDInsight. Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejsu wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w celu uzyskania kroków.

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać usługi Azure Cloud Shell, a następnie wybierz spróbuj **go** w prawym górnym rogu bloku kodu. W przeciwnym razie wprowadź poniższe polecenie:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Listy klastrów

Lista [az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) służy do listy klastrów. Edytuj poniższe polecenia, `RESOURCE_GROUP_NAME` zastępując nazwą grupy zasobów, a następnie wprowadź polecenia:

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

## <a name="show-cluster"></a>Pokaż klaster

Użyj [az hdinsight show,](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) aby wyświetlić informacje dla określonego klastra. Edytuj poniższe polecenie, `RESOURCE_GROUP_NAME` `CLUSTER_NAME` zastępując i odpowiednimi informacjami, a następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Usuwanie klastrów

Użyj [az hdinsight delete,](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) aby usunąć określony klaster. Edytuj poniższe polecenie, `RESOURCE_GROUP_NAME` `CLUSTER_NAME` zastępując i odpowiednimi informacjami, a następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Klaster można również usunąć, usuwając grupę zasobów zawierającą klaster. Uwaga: spowoduje to usunięcie wszystkich zasobów w grupie, w tym domyślnego konta magazynu.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Użyj [az hdinsight resize,](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) aby zmienić rozmiar określonego klastra HDInsight do określonego rozmiaru. Edytuj poniższe polecenie, `RESOURCE_GROUP_NAME` `CLUSTER_NAME` zastępując i z odpowiednimi informacjami. Zamień `WORKERNODE_COUNT` na żądaną liczbę węzłów procesu roboczego dla klastra. Aby uzyskać więcej informacji na temat skalowania klastrów, zobacz [Skalowanie klastrów HDInsight](./hdinsight-scaling-best-practices.md). Wprowadź polecenie:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak wykonywać różne zadania administracyjne klastra HDInsight. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu portalu Azure](hdinsight-administer-use-portal-linux.md)
* [Administrowanie programem HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
