---
title: Najważniejsze wskazówki dotyczące zarządzania klastrami — usługa Azure HDInsight
description: Poznaj najlepsze rozwiązania dotyczące zarządzania klastrami HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782023"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Najważniejsze wskazówki dotyczące zarządzania klastrami usługi HDInsight

Poznaj najlepsze rozwiązania dotyczące zarządzania klastrami HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Jak utworzyć klastry HDInsight?

| Opcja | Dokumenty |
|---|---|
| Azure Data Factory | [Tworzenie klastrów Apache Hadoop na żądanie w usłudze HDInsight przy użyciu usługi Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Szablon Niestandardowego Menedżera zasobów | [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżera zasobów](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Szablony szybkiego startu | [Szablony szybkiego startu programu HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| przykładów dla platformy Azure | [Przykłady usługi HDInsight Azure](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Portal Azure | [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu witryny Azure portal](./spark/apache-spark-intellij-tool-plugin.md) |
| Interfejs wiersza polecenia platformy Azure | [Tworzenie klastrów usługi HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu programu Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Tworzenie klastrów Apache Hadoop przy użyciu interfejsu API rest platformy Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Przejdź](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Jeśli tworzysz klaster i ponownie używasz nazwy klastra z wcześniej utworzonego klastra, poczekaj, aż poprzednie usunięcie klastra zostanie zakończone przed utworzeniem klastra.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Jak dostosować klastry HDInsight?

| Opcja | Dokumenty |
|---|---|
| Akcje skryptu | [Dostosowywanie klastrów usługi Azure HDInsight przy użyciu akcji skryptów](./hdinsight-hadoop-customize-cluster-linux.md) |
| Uruchomienie | [Dostosowywanie klastrów HDInsight przy użyciu bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Zewnętrzne metasklepy | [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Niestandardowa baza danych Ambari | [Konfigurowanie klastrów USŁUGI HDInsight przy niestandardowych bazach danych Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Jakie błędy mogą napotkać podczas tworzenia klastrów?

| Błąd | Więcej informacji |
|---|---|
| Brak przydziału | Istnieją przydziały dla liczby przydziałów, które można utworzyć w ramach subskrypcji w każdym regionie. Aby uzyskać więcej informacji, zobacz [Planowanie zdolności produkcyjnych: przydziały](./hdinsight-capacity-planning.md). |
| Nie ma już dostępnych adresów IP | Każda sieć wirtualna ma ograniczoną liczbę adresów IP. Podczas tworzenia klastra HDInsight każdy węzeł (w tym zookeeper i węzły bramy) używa niektórych z tych przydzielonych adresów IP. Gdy wszystkie adresy IP są w użyciu, wystąpi ten błąd.  |
| Reguły sieciowej grupy zabezpieczeń (NSG) nie zezwalają na komunikację z dostawcami zasobów USŁUGI HDInsight | Jeśli używasz sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika (UDR) do kontrolowania ruchu przychodzącego do klastra HDInsight, należy upewnić się, że klaster może komunikować się z krytycznymi usługami kondycji i zarządzania platformy Azure. Aby uzyskać więcej informacji, zobacz [Tagi usługi sieciowej grupy zabezpieczeń dla usługi Azure HDInsight](./hdinsight-service-tags.md) |
| Ponowne użycie nazwy klastra | Podczas korzystania z nazwy klastra, który był używany wcześniej, należy odczekać X liczbę minut przed odtworzeniem klastra. W przeciwnym razie zostanie wyświetlony komunikat, że zasób już istnieje. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Jak zarządzać uruchomionymi klastrami HDInsight?

| Opcja | Dokumenty |
|---|---|
| Automatyczne skalowanie | [Automatyczne skalowanie klastrów usługi Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Skalowanie ręczne | [Skalowanie klastrów usługi Azure HDInsight](./hdinsight-scaling-best-practices.md) |
| Monitorowanie za pomocą Ambari| [Monitorowanie wydajności klastra w usłudze Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitorowanie za pomocą dzienników usługi Azure Monitor | [Monitorowanie klastrów usługi HDInsight za pomocą dzienników usługi Azure Monitor](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Jak sprawdzić usunięte klastry HDInsight?

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Do monitorowania usuniętych klastrów można użyć następującej kwerendy z dziennikami usługi Azure Monitor.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Następne kroki

* [Planowanie wydajności klastrów usługi HDInsight](./hdinsight-capacity-planning.md)
* [Jakie są domyślne i zalecane konfiguracje węzłów dla usługi Azure HDInsight?](./hdinsight-supported-node-configuration.md)