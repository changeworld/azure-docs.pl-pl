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
ms.openlocfilehash: 4df4fa29722dd3ad33cf1ce123877f04f9f4b4c1
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360392"
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W wierszu polecenia programu PowerShell Użyj następującego polecenia, aby usunąć klaster:

    Remove-AzHDInsightCluster -ClusterName CLUSTERNAME

Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

## <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

W wierszu należy użyć następującego Aby usunąć klaster:

    azure hdinsight cluster delete CLUSTERNAME

Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:
