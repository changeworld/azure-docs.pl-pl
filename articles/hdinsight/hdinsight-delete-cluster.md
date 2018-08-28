---
title: Jak usunąć klaster usługi HDInsight — Azure
description: Informacje na temat różnych sposobów, możesz usunąć klaster usługi HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 737cc120877a9d0f06a1f6d209bcf9a233aa7d19
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091313"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. W tym dokumencie dowiesz się, jak usunąć klaster, przy użyciu witryny Azure portal, programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure w wersji 1.0.

> [!IMPORTANT]
> Usuwanie klastra usługi HDInsight nie powoduje usunięcia konta usługi Azure Storage lub Data Lake Store skojarzonego z klastrem. Można ponownie użyć danych przechowywanych w tych usług w przyszłości.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz klaster usługi HDInsight. Jeśli klastra usługi HDInsight nie jest przypięty do pulpitu nawigacyjnego, możesz wyszukać go według nazwy, korzystając z pola wyszukiwania.
   
    ![wyszukiwania portalu](./media/hdinsight-delete-cluster/navbar.png)

2. Ustawienia klastra, zaznacz **Usuń** ikony. Po wyświetleniu monitu wybierz **tak** Aby usunąć klaster.
   
    ![Ikona usuwania](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

W wierszu polecenia programu PowerShell Użyj następującego polecenia, aby usunąć klaster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

## <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

W wierszu należy użyć następującego Aby usunąć klaster:

    azure hdinsight cluster delete CLUSTERNAME

Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

> [!NOTE]
> Interfejs wiersza polecenia platformy Azure w wersji 2.0 nie obsługuje usuwania klastrów HDInsight w tej chwili (23 października 2017 r.).
