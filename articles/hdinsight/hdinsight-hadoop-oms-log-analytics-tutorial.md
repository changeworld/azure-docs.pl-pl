---
title: Umożliwia monitorowanie klastrów usługi HDInsight Azure Log Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Analiza dzienników Azure do monitorowania zadania uruchomione w klastrze usługi HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: e11c9672e2e96f3370c69b33f57a007f7d63ccf2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Analiza dzienników Azure umożliwia monitorowanie klastrów usługi HDInsight

Dowiedz się, jak używać usługi Analiza dzienników Azure do monitorowania działania klastra usługi Hadoop w usłudze HDInsight.

[Zaloguj się Analytics](../log-analytics/log-analytics-overview.md) to usługa, która monitoruje chmurze i lokalnych środowiskach utrzymywać ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Zobacz [Utwórz bezpłatne konto platformy Azure już dzisiaj](https://azure.microsoft.com/free).

* **Klaster Azure HDInsight**. Obecnie można używać analizy dzienników z następujące typy klastrów usługi HDInsight:

    * Hadoop
    * HBase
    * Zapytanie interakcyjne
    * Kafka
    * platforma Spark
    * Storm

    Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight, zobacz [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Obszar roboczy analizy dzienników**. Ten obszar roboczy można traktować jako unikatowy środowiska analizy dzienników z własnych danych repozytorium, źródła danych i rozwiązania. Musi mieć jeden taki obszar roboczy już utworzone, które można skojarzyć z klastrami Azure HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>Włącz analizy dzienników przy użyciu portalu

W tej sekcji skonfigurujesz istniejącego klastra usługi HDInsight Hadoop do użycia z obszarem roboczym usługi Analiza dzienników Azure do monitorowania, zadań, dzienników debugowania itd.

1. Otwórz klaster usługi HDInsight w portalu Azure.
2. W okienku po lewej stronie kliknij **monitorowanie**.
3. W okienku po prawej stronie kliknij **włączyć**, wybierz istniejący obszar roboczy analizy dzienników, a następnie kliknij przycisk **zapisać**.

    ![Włącz monitorowanie klastrów usługi HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Włącz monitorowanie klastrów usługi HDInsight")

    Może potrwać kilka minut, aby zapisać ustawienia.  Po zakończeniu widzi **Otwórz pulpit nawigacyjny OMS** przycisk u góry. 

    ![Otwórz pulpit nawigacyjny usługi Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "OMS Otwórz pulpit nawigacyjny")

5. Kliknij przycisk **OMS Otwórz pulpit nawigacyjny**.
6. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia platformy Azure.

    ![Portal Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "portal usługi Operations Management Suite")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Włącz analizy dzienników przy użyciu programu Azure PowerShell

Można włączyć analizy dzienników przy użyciu programu Azure PowerShell. To polecenie cmdlet:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

Zobacz [AzureRmHDInsightOperationsManagementSuite Włącz](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Aby wyłączyć, to polecenie cmdlet 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

Zobacz [AzureRmHDInsightOperationsManagementSuite Wyłącz](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie rozwiązania do zarządzania klastrem usługi HDInsight do analizy dzienników](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
