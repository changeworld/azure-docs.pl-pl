---
title: Jak usunąć klaster usługi HDInsight — Azure
description: Informacje na temat różnych sposobów usuwania klastra usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a28d59cb35004fac6b069f2aa41042b4d46e443f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091534"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. W tym dokumencie dowiesz się, jak usunąć klaster przy użyciu [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ module](https://docs.microsoft.com/powershell/azure/overview)i [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Usunięcie klastra usługi HDInsight nie powoduje usunięcia kont magazynu Azure ani Data Lake Storage skojarzonych z klastrem. W przyszłości możesz ponownie użyć danych przechowywanych w tych usługach.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Z menu po lewej stronie wybierz kolejno pozycje **wszystkie usługi** > **Analiza** > usług**HDInsight** i klaster.

3. Z widoku domyślnego wybierz ikonę **Usuń** . Postępuj zgodnie z monitem, aby usunąć klaster.

    ![Przycisk usuwania klastra usługi HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell AZ module

Zastąp `CLUSTERNAME` ciąg nazwą klastra usługi HDInsight w poniższym kodzie. W wierszu polecenia programu PowerShell wprowadź następujące polecenie, aby usunąć klaster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zastąp `CLUSTERNAME` ciąg nazwą klastra usługi HDInsight i `RESOURCEGROUP` nazwą grupy zasobów w poniższym kodzie.  W wierszu polecenia wprowadź następujące polecenie, aby usunąć klaster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
