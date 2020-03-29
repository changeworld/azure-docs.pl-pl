---
title: Jak usunąć klaster usługi HDInsight — Azure
description: Informacje o różnych sposobach usuwania klastra usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807140"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Rozliczenia są proporcjonalnie do na minutę, więc zawsze należy usunąć klaster, gdy nie jest już używany. W tym dokumencie dowiesz się, jak usunąć klaster przy użyciu [witryny Azure portal](https://portal.azure.com), [modułu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)i [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

> [!IMPORTANT]  
> Usunięcie klastra USŁUGI HDInsight nie powoduje usunięcia kont usługi Azure Storage ani magazynu usługi Data Lake skojarzonych z klastrem. W przyszłości można ponownie użyć danych przechowywanych w tych usługach.

## <a name="azure-portal"></a>Portal Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. W menu po lewej stronie przejdź do **pozycji Wszystkie usługi** > **Analytics** > **HDInsight klastrów** i wybierz klaster.

3. W widoku domyślnym wybierz ikonę **Usuń.** Postępuj zgodnie z instrukcją, aby usunąć klaster.

    ![Przycisk usuwania klastra hdinsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Zamień `CLUSTERNAME` na nazwę klastra HDInsight w poniższym kodzie. W wierszu programu PowerShell wprowadź następujące polecenie, aby usunąć klaster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zamień `CLUSTERNAME` na nazwę klastra HDInsight i `RESOURCEGROUP` nazwę grupy zasobów w poniższym kodzie.  W wierszu polecenia wprowadź następujące czynności, aby usunąć klaster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
