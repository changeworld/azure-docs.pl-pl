---
title: Używanie dzienników Azure Monitor do monitorowania klastrów usługi Azure HDInsight
description: Informacje dotyczące monitorowania zadań uruchomionych w klastrze usługi HDInsight przy użyciu dzienników Azure Monitor.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162790"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Używanie dzienników Azure Monitor do monitorowania klastrów usługi HDInsight

Dowiedz się, jak włączyć dzienniki Azure Monitor, aby monitorować operacje klastra Hadoop w usłudze HDInsight oraz jak dodać rozwiązanie do monitorowania usługi HDInsight.

[Azure monitor Logs](../log-analytics/log-analytics-overview.md) to usługa w Azure monitor, która monitoruje środowiska chmurowe i lokalne w celu utrzymania ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Ten obszar roboczy można traktować jako unikatowe środowisko dzienników Azure Monitor z własnym repozytorium danych, źródłami danych i rozwiązaniami. Aby uzyskać instrukcje, zobacz [Tworzenie obszaru roboczego log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Klaster usługi Azure HDInsight. Obecnie można użyć dzienników Azure Monitor z następującymi typami klastrów usługi HDInsight:

  * Hadoop
  * HBase
  * Zapytanie interakcyjne
  * Kafka
  * platforma Spark
  * Storm

  Instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight można znaleźć w temacie [Rozpoczynanie pracy z usługą Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Azure PowerShell AZ module.  Zobacz [wprowadzenie do nowego Azure PowerShell AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Upewnij się, że masz najnowszą wersję. W razie potrzeby uruchom `Update-Module -Name Az`.

> [!NOTE]  
> Zaleca się umieszczenie klastra HDInsight i obszar roboczy usługi Log Analytics, w tym samym regionie, w celu zapewnienia lepszej wydajności. Dzienniki Azure Monitor nie są dostępne we wszystkich regionach świadczenia usługi Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Włączanie dzienników Azure Monitor przy użyciu portalu

W tej sekcji skonfigurujesz istniejącego klastra usługi HDInsight Hadoop używać obszaru roboczego usługi Azure Log Analytics do monitorowania zadań, dzienniki debugowania itd.

1. Na [Azure Portal](https://portal.azure.com/)wybierz swój klaster.  Aby uzyskać instrukcje, zobacz [listę i wyświetlanie klastrów](./hdinsight-administer-use-portal-linux.md#showClusters) . Klaster zostanie otwarty na nowej stronie portalu.

1. W obszarze **monitorowanie**wybierz pozycję **Azure monitor**.

1. W widoku głównym w obszarze **integracja Azure monitor**wybierz pozycję **Włącz**.

1. Z listy rozwijanej **Wybierz obszar roboczy** wybierz istniejący obszar roboczy log Analytics.

1. Wybierz pozycję **Zapisz**.  Może potrwać kilka minut, aby zapisać ustawienia.

    ![Włącz monitorowanie klastrów usługi HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Włącz monitorowanie klastrów usługi HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Włączanie dzienników Azure Monitor przy użyciu Azure PowerShell

Dzienniki Azure Monitor można włączyć za Azure PowerShell pomocą polecenia cmdlet AZ module [enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) .

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

Aby wyłączyć, użyj polecenia cmdlet [disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalowanie rozwiązania do zarządzania klastrem HDInsight

Usługa HDInsight udostępnia rozwiązania do zarządzania specyficzne dla klastra, które można dodać do dzienników Azure Monitor. [Rozwiązania do zarządzania](../log-analytics/log-analytics-add-solutions.md) umożliwiają dodawanie funkcji do dzienników Azure monitor, które udostępniają dodatkowe narzędzia do obsługi danych i analizy. Te rozwiązania zbieranie metryk istotną poprawę wydajności ze klastry usługi HDInsight i zapewniają narzędzia do wyszukiwania metryk. Rozwiązania te udostępniają wizualizacje i pulpity nawigacyjne dla większości typów klastrów obsługuje HDInsight. Za pomocą metryk, które są zbierane za pomocą rozwiązania, można utworzyć niestandardowe reguły monitorowania i alertów.

Poniżej przedstawiono dostępne rozwiązania HDInsight:

* Monitorowanie usługi HDInsight Hadoop
* Monitorowanie bazy danych HBase usługi HDInsight
* Monitorowanie usługi HDInsight interakcyjnych zapytań
* Monitorowanie systemu Kafka usługi HDInsight
* Monitorowanie platformy Spark usługi HDInsight
* Monitorowanie usługi HDInsight Storm

Instrukcje dotyczące instalowania rozwiązania do zarządzania programu znajdują się [w temacie rozwiązania zarządzania na platformie Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Aby eksperymentować, Zainstaluj rozwiązanie do monitorowania usługi HDInsight Hadoop. Gdy wszystko będzie gotowe, zobaczysz kafelek **HDInsightHadoop** na liście **Podsumowanie**. Wybierz kafelek **HDInsightHadoop** . Rozwiązanie HDInsightHadoop wygląda następująco:

![Widok rozwiązania monitorowania HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Ponieważ klaster jest całkiem nowego klastra, raport nie wyświetla żadnych działań.

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności

Usługa Azure monitor obsługuje również zbieranie i analizowanie metryk wydajności dla węzłów w klastrze. Aby uzyskać więcej informacji na temat włączania i konfigurowania tej funkcji, zobacz [źródła danych wydajności systemu Linux w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Inspekcja klastra

Usługa HDInsight obsługuje inspekcję klastra przy użyciu dzienników Azure Monitor przez zaimportowanie następujących typów dzienników:

* `log_gateway_audit_CL` — ta tabela zawiera dzienniki inspekcji z węzłów bramy klastra, które pokazują pomyślne i nieudane próby zalogowania.
* `log_auth_CL` — ta tabela zawiera dzienniki protokołu SSH z zakończonymi sukcesem i nieudanymi próbami logowania.
* `log_ambari_audit_CL` — ta tabela zawiera dzienniki inspekcji z Ambari.
* `log_ranger_audti_CL` — ta tabela zawiera dzienniki inspekcji z platformy Apache Ranger na klastrach ESP.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących dzienników Azure Monitor do monitorowania klastrów usługi HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
