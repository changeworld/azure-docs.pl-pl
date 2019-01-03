---
title: Jak usunąć klaster usługi HDInsight — Azure
description: Informacje na temat różnych sposobów, możesz usunąć klaster usługi HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f5c2f6104a765c0e598e41234891c492686019dd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718983"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub wiersza polecenia platformy Azure Classic

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. W tym dokumencie opisano sposób usuwania klastra przy użyciu [witryny Azure portal](https://portal.azure.com), [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/)oraz klasycznego wiersza polecenia platformy Azure.

> [!IMPORTANT]  
> Usuwanie klastra usługi HDInsight nie powoduje usunięcia konta usługi Azure Storage lub usługi Data Lake Storage skojarzonego z klastrem. Można ponownie użyć danych przechowywanych w tych usług w przyszłości.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz klaster usługi HDInsight. Jeśli klastra usługi HDInsight nie jest przypięty do pulpitu nawigacyjnego, możesz wyszukać go według nazwy, korzystając z pola wyszukiwania.
   
    ![wyszukiwania portalu](./media/hdinsight-delete-cluster/navbar.png)

2. Ustawienia klastra, zaznacz **Usuń** ikony. Po wyświetleniu monitu wybierz **tak** Aby usunąć klaster.
   
    ![Ikona usuwania](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

W wierszu polecenia programu PowerShell Użyj następującego polecenia, aby usunąć klaster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

## <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

W wierszu należy użyć następującego Aby usunąć klaster:

    azure hdinsight cluster delete CLUSTERNAME

Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:
