---
title: Monitorowanie klastrów usługi Azure HDInsight za pomocą dzienników usługi Azure Monitor
description: Dowiedz się, jak używać dzienników usługi Azure Monitor do monitorowania zadań uruchomionych w klastrze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162790"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Monitorowanie klastrów usługi HDInsight za pomocą dzienników usługi Azure Monitor

Dowiedz się, jak włączyć dzienniki usługi Azure Monitor do monitorowania operacji klastra Hadoop w programie HDInsight i jak dodać rozwiązanie do monitorowania HDInsight.

[Dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md) to usługa w usłudze Azure Monitor, która monitoruje środowisko w chmurze i środowiskach lokalnych w celu utrzymania ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Ten obszar roboczy można potraktować jako unikatowe środowisko dzienników usługi Azure Monitor z własnym repozytorium danych, źródłami danych i rozwiązaniami. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego usługi Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Klaster usługi Azure HDInsight. Obecnie można używać dzienników usługi Azure Monitor z następującymi typami klastra USŁUGI HDInsight:

  * Hadoop
  * HBase
  * Zapytanie interakcyjne
  * Kafka
  * platforma Spark
  * Storm

  Aby uzyskać instrukcje dotyczące tworzenia klastra HDInsight, zobacz [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Moduł Az programu Azure PowerShell.  Zobacz [Wprowadzenie nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Upewnij się, że masz najnowszą wersję. W razie `Update-Module -Name Az`potrzeby uruchom plik .

> [!NOTE]  
> Zaleca się umieszczenie klastra HDInsight i obszaru roboczego usługi Log Analytics w tym samym regionie w celu uzyskania lepszej wydajności. Dzienniki usługi Azure Monitor nie są dostępne we wszystkich regionach platformy Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Włączanie dzienników usługi Azure Monitor przy użyciu portalu

W tej sekcji można skonfigurować istniejący klaster programu HDInsight Hadoop do używania obszaru roboczego usługi Azure Log Analytics do monitorowania zadań, dzienników debugowania itp.

1. Z [witryny Azure portal](https://portal.azure.com/)wybierz swój klaster.  Aby uzyskać instrukcje, zobacz [Lista i pokazyj klastry.](./hdinsight-administer-use-portal-linux.md#showClusters) Klaster jest otwierany na nowej stronie portalu.

1. Od lewej w obszarze **Monitorowanie**wybierz pozycję **Azure Monitor**.

1. Z widoku głównego w obszarze **Integracja usługi Azure Monitor**wybierz pozycję **Włącz**.

1. Z listy **rozwijanej Wybierz obszar roboczy** wybierz istniejący obszar roboczy usługi Log Analytics.

1. Wybierz **pozycję Zapisz**.  Zapisanie ustawienia zajmuje kilka chwil.

    ![Włącz monitorowanie klastrów HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Włącz monitorowanie klastrów HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Włączanie dzienników usługi Azure Monitor przy użyciu programu Azure PowerShell

Dzienniki usługi Azure Monitor można włączyć przy użyciu polecenia cmdlet cmdlet programu Azure PowerShell Az [modułu Enable-AzHDInsightMonitoring.](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Aby wyłączyć, użyj polecenia cmdlet [Disable-AzHDInsightMonitoring:](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalowanie rozwiązań do zarządzania klastrami HDInsight

Usługa HDInsight udostępnia rozwiązania do zarządzania specyficzne dla klastra, które można dodać do dzienników usługi Azure Monitor. [Rozwiązania do zarządzania](../log-analytics/log-analytics-add-solutions.md) dodają funkcje do dzienników usługi Azure Monitor, zapewniając dodatkowe narzędzia do analizy danych i analizy. Rozwiązania te zbierają ważne metryki wydajności z klastrów HDInsight i udostępniają narzędzia do wyszukiwania metryk. Rozwiązania te zapewniają również wizualizacje i pulpity nawigacyjne dla większości typów klastrów obsługiwanych w programie HDInsight. Za pomocą metryki, które można zbierać z rozwiązania, można utworzyć niestandardowe reguły monitorowania i alerty.

Oto dostępne rozwiązania HDInsight:

* Monitorowanie hadoopu HDInsight
* Monitorowanie hdinight HBase
* Interaktywne monitorowanie zapytań HDInsight
* Monitorowanie kafka HDInsight
* Monitorowanie iskry HDInsight
* Monitorowanie burzy HDInsight

Aby uzyskać instrukcje dotyczące instalowania rozwiązania do zarządzania, zobacz [Rozwiązania do zarządzania na platformie Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Aby eksperymentować, zainstaluj rozwiązanie do monitorowania hadoop HDInsight. Po zakończeniu zobaczysz kafelek **HDInsightHadoop** wymieniony w obszarze **Podsumowanie**. Wybierz kafelek **HDInsightHadoop.** Rozwiązanie HDInsightHadoop wygląda następująco:

![Widok rozwiązania monitorowania HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Ponieważ klaster jest zupełnie nowy klaster, raport nie pokazuje żadnych działań.

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności

Monitor platformy Azure obsługuje również zbieranie i analizowanie metryk wydajności dla węzłów w klastrze. Aby uzyskać więcej informacji na temat włączania i konfigurowania tej funkcji, zobacz [Źródła danych wydajności systemu Linux w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Inspekcja klastra

Usługa HDInsight obsługuje inspekcje klastrów za pomocą dzienników usługi Azure Monitor, importując następujące typy dzienników:

* `log_gateway_audit_CL`- ta tabela zawiera dzienniki inspekcji z węzłów bramy klastra, które pokazują pomyślne i nieudane próby logowania.
* `log_auth_CL`- ta tabela zawiera dzienniki SSH z udanych i nieudanych prób logowania.
* `log_ambari_audit_CL`- ta tabela zawiera dzienniki inspekcji z Ambari.
* `log_ranger_audti_CL`- ta tabela zawiera dzienniki inspekcji z Apache Ranger na klastrach ESP.

## <a name="next-steps"></a>Następne kroki

* [Wysyłaj zapytania do dzienników usługi Azure Monitor w celu monitorowania klastrów usługi HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
