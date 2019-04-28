---
title: Zapytanie usługi Azure Monitor dzienników do monitorowania klastrów Azure HDInsight
description: Dowiedz się, jak uruchamiać zapytania dotyczące dzienników usługi Azure Monitor do monitorowania zadań uruchamianych w klastrze usługi HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cbaaed3fff99778bfab1feeacdab02bf8245a85a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761222"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Zapytanie usługi Azure Monitor dzienników do monitorowania klastrów HDInsight

Dowiedz się, niektóre podstawowe scenariusze dotyczące sposobu używania dzienników usługi Azure Monitor do monitorowania klastrów Azure HDInsight:

* [Analizuj metryki klastra HDInsight](#analyze-hdinsight-cluster-metrics)
* [Wyszukaj konkretne komunikaty dziennika](#search-for-specific-log-messages)
* [Utwórz alerty zdarzeń](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Konieczne jest posiadanie skonfigurowane w klastrze usługi HDInsight w taki sposób, aby przy użyciu dzienników usługi Azure Monitor oraz dodano dzienniki usługi Azure Monitor specyficzne dla klastra HDInsight monitorowanie rozwiązań do obszaru roboczego. Aby uzyskać instrukcje, zobacz [dzienników użycia usługi Azure Monitor, przy użyciu klastrów HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizuj metryki klastra HDInsight

Dowiedz się, jak wyszukiwać konkretne metryki dotyczące klastra usługi HDInsight.

1. Otwórz obszar roboczy usługi Log Analytics, który jest skojarzony z klastrem HDInsight w witrynie Azure portal.
2. Wybierz **wyszukiwanie w dzienniku** kafelka.
3. Wpisz następujące zapytanie w polu wyszukiwania, aby wyszukać wszystkie metryki dla wszystkie dostępne metryki dla wszystkich klastrów HDInsight skonfigurowane przy użyciu dzienników usługi Azure Monitor, a następnie wybierz pozycję **Uruchom**.

        search *

    ![Wyszukaj wszystkie metryki](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "wyszukiwanie wszystkich metryk")

    Dane wyjściowe powinny wyglądać następująco:

    ![Wyszukaj wszystkie dane wyjściowe metryki](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Wyszukaj wszystkie metryki dane wyjściowe.")

5. W lewym okienku w obszarze **typu**, wybierz metrykę, którą chcesz dokładniej zapoznać się szczegółowo, a następnie wybierz **Zastosuj**. Poniższy zrzut ekranu przedstawia `metrics_resourcemanager_queue_root_default_CL` wybrany typ.

    > [!NOTE]  
    > Musisz wybrać **[+] więcej** przycisk, aby znaleźć metryki, którego szukasz. Ponadto **Zastosuj** przycisk myszy jest umieszczony w dolnej części listy, więc musi Przewinięcie w dół w celu wyświetlenia go.

    Zwróć uwagę, że zapytanie w polu tekstowym zmienia się do przedstawionego w polu wyróżnione na poniższym zrzucie ekranu:

    ![Wyszukiwanie określonych metryk](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "wyszukiwanie określonych metryk")

6. Aby wyświetlić elementy podrzędne tego określonej metryki. Na przykład można dostosować istniejące dane wyjściowe na podstawie średniej zasobów używanych w 10-minutowych interwałach pogrupowane według nazwy klastra przy użyciu następującej kwerendy:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Zamiast rafinowania, na podstawie średniej używanych zasobów, umożliwia następujące zapytanie uściślić wyniki, w zależności od momentu maksymalnego zasoby zostały użyte (a także 90 i 95. percentyl) w oknie 10 minut:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Wyszukaj konkretne komunikaty dziennika

Dowiedz się, jak sprawdzić komunikaty o błędach podczas określonego okna czasowego. Opisane w tym miejscu są tylko jeden przykład na sposób możesz mogą pojawić się w komunikacie o błędzie możesz są zainteresowani. Można użyć dowolnej właściwości, która jest dostępna wyszukać błędy, którą próbujesz znaleźć.

1. Otwórz obszar roboczy usługi Log Analytics, który jest skojarzony z klastrem HDInsight w witrynie Azure portal.
2. Wybierz **wyszukiwanie w dzienniku** kafelka.
3. Wpisz następujące zapytanie, aby wyszukać wszystkie komunikaty o błędach dla wszystkich klastrów HDInsight skonfigurowany do używania dzienników usługi Azure Monitor, a następnie wybierz **Uruchom**. 

         search "Error"

    Powinny pojawić się dane wyjściowe, takich jak poniższe dane wyjściowe:

    ![Wyszukaj wszystkie dane wyjściowe błędów](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "wyszukiwanie wszystkich błędów danych wyjściowych.")

4. W lewym okienku w obszarze **typu** kategorię, wybierz typ błędu, które warto dokonać szczegółowej szczegółowo, a następnie wybierz **Zastosuj**.  Należy zauważyć, że wyniki są Elegancja tylko w celu wyświetlenia błędu wybranego typu.
5. Możesz przejść głębiej do tej listy wystąpienia określonego błędu za pomocą opcji dostępnych w okienku po lewej stronie. Na przykład:

    - Aby wyświetlić komunikaty o błędach z węzła w określonym procesie roboczym:

        ![Wyszukaj określone błędy w danych wyjściowych](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "wyszukiwania dla określonych błędów w danych wyjściowych")

    - Aby zobaczyć, że wystąpił błąd w określonym czasie:

        ![Wyszukaj określone błędy w danych wyjściowych](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "wyszukiwania dla określonych błędów w danych wyjściowych")

6. Aby wyświetlić wystąpienia określonego błędu. Możesz wybrać **[+] Pokaż więcej** aby przyjrzeć się rzeczywistym komunikacie o błędzie.

    ![Wyszukaj określone błędy w danych wyjściowych](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "wyszukiwania dla określonych błędów w danych wyjściowych")

## <a name="create-alerts-for-tracking-events"></a>Tworzenie alertów dotyczących zdarzenia śledzenia

Pierwszym krokiem w celu utworzenia alertu jest na zapytania opartego na jest wyzwalany alert. Możesz użyć dowolnego zapytania, który ma być tworzony alert.

1. Otwórz obszar roboczy usługi Log Analytics, który jest skojarzony z klastrem HDInsight w witrynie Azure portal.
2. Wybierz **wyszukiwanie w dzienniku** kafelka.
3. Uruchom następujące zapytanie, na którym chcesz utworzyć alert, a następnie wybierz **Uruchom**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Zapytanie zawiera listę wszystkich aplikacji nie powiodło się uruchamianie w klastrach HDInsight.

4. Wybierz **Nowa reguła alertu** górnej części strony.

    ![Wprowadź zapytanie w celu utworzenia alertu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "wprowadź zapytanie w celu utworzenia alertu")

5. W **Utwórz regułę** oknie wprowadź zapytanie i inne szczegóły, aby utworzyć alert, a następnie wybierz **Utwórz regułę alertu**.

    ![Wprowadź zapytanie w celu utworzenia alertu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "wprowadź zapytanie w celu utworzenia alertu")

Aby edytować lub usunąć istniejącego alertu:

1. Otwórz obszar roboczy usługi Log Analytics w witrynie Azure portal.
2. Z menu po lewej stronie wybierz **alertu**.
3. Wybierz alert, który chcesz edytować ani usunąć.
4. Istnieją następujące opcje: **Zapisz**, **odrzucić**, **wyłączyć**, i **Usuń**.

    ![Monitorowanie usługi Azure HDInsight dzienniki edycji Usuwanie alertu](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Aby uzyskać więcej informacji, zobacz [tworzenie, wyświetlanie i zarządzanie nimi za pomocą usługi Azure Monitor alertów dotyczących metryk](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Zobacz także

* [Tworzenie niestandardowych widoków przy użyciu projektanta widoku w usłudze Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Tworzenie, wyświetlanie i zarządzanie przy użyciu usługi Azure Monitor alertów dotyczących metryk](../azure-monitor/platform/alerts-metric.md)
