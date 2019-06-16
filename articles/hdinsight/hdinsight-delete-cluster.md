---
title: Jak usunąć klaster usługi HDInsight — Azure
description: Informacje na temat różnych sposobów, możesz usunąć klaster usługi HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721019"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. W tym dokumencie opisano sposób usuwania klastra przy użyciu [witryny Azure portal](https://portal.azure.com), [modułu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)i [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Usuwanie klastra usługi HDInsight nie powoduje usunięcia konta usługi Azure Storage lub usługi Data Lake Storage skojarzonego z klastrem. Można ponownie użyć danych przechowywanych w tych usług w przyszłości.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu po lewej stronie przejdź do **wszystkich usług** > **Analytics** > **klastry HDInsight** i wybierz klaster.

3. Widok domyślny, zaznacz **Usuń** ikony. Postępuj zgodnie z monit o usunięciu klastra.
   
    ![Ikona usuwania](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Moduł programu Azure PowerShell Az

Zastąp `CLUSTERNAME` nazwą klastra usługi HDInsight w poniższym kodzie. W wierszu polecenia programu PowerShell wprowadź następujące polecenie, aby usunąć klaster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zastąp `CLUSTERNAME` nazwą klastra usługi HDInsight i `RESOURCEGROUP` nazwą grupy zasobów w poniższym kodzie.  W wierszu polecenia wprowadź następujące polecenie, aby usunąć klaster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
