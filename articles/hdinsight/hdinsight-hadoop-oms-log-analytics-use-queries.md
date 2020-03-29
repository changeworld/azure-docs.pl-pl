---
title: Wysyłaj zapytania do dzienników usługi Azure Monitor w celu monitorowania klastrów usługi Azure HDInsight
description: Dowiedz się, jak uruchamiać kwerendy w dziennikach usługi Azure Monitor w celu monitorowania zadań uruchomionych w klastrze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803837"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Wysyłaj zapytania do dzienników usługi Azure Monitor w celu monitorowania klastrów usługi HDInsight

Poznaj kilka podstawowych scenariuszy dotyczących używania dzienników usługi Azure Monitor do monitorowania klastrów usługi Azure HDInsight:

* [Analizowanie metryk klastra usługi HDInsight](#analyze-hdinsight-cluster-metrics)
* [Tworzenie alertów o zdarzeniach](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Musisz skonfigurować klaster HDInsight do korzystania z dzienników usługi Azure Monitor i dodano do obszaru roboczego rozwiązania monitorowania specyficzne dla klastra HDInsight. Aby uzyskać instrukcje, zobacz [Używanie dzienników usługi Azure Monitor z klastrami HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizowanie metryk klastra usługi HDInsight

Dowiedz się, jak wyszukać określone dane dla klastra HDInsight.

1. Otwórz obszar roboczy usługi Log Analytics skojarzony z klastrem USŁUGI HDInsight z witryny Azure portal.
1. W **obszarze Ogólne**wybierz pozycję **Dzienniki**.
1. Wpisz następującą kwerendę w polu wyszukiwania, aby wyszukać wszystkie metryki dla wszystkich dostępnych metryk dla wszystkich klastrów usługi HDInsight skonfigurowanych do używania dzienników usługi Azure Monitor, a następnie wybierz pozycję **Uruchom**. Przejrzyj wyniki.

    ```kusto
    search *
    ```

    ![Apache Ambari analytics przeszukuje wszystkie metryki](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Wyszukaj wszystkie dane")

1. W menu po lewej stronie wybierz kartę **Filtr.**

1. W obszarze **Typ**wybierz **opcję Puls .** Następnie wybierz pozycję **Zastosuj & Uruchom**.

    ![rejestrowanie określonych danych wyszukiwania w analizie](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Wyszukiwanie określonych danych")

1. Należy zauważyć, że kwerenda w polu tekstowym zmienia się na:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Możesz kopać głębiej, korzystając z opcji dostępnych w menu po lewej stronie. Przykład:

    - Aby wyświetlić dzienniki z określonego węzła:

        ![Wyszukiwanie określonych błędów wyjściowych1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Wyszukiwanie określonych błędów wyjściowych1")

    - Aby wyświetlić dzienniki w określonych godzinach:

        ![Wyszukiwanie określonych błędów wyjściowych2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Wyszukiwanie określonych błędów wyjściowych2")

1. Wybierz **pozycję Zastosuj & uruchom** i przejrzyj wyniki. Należy również zauważyć, że kwerenda została zaktualizowana do:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Dodatkowe przykładowe kwerendy

Przykładowa kwerenda oparta na średniej zasobów używanych w interwale 10-minutowym, sklasyfikowana według nazwy klastra:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Zamiast udoskonalać na podstawie średniej użytych zasobów, można użyć następującej kwerendy, aby uściślić wyniki na podstawie tego, kiedy użyto maksymalnych zasobów (a także 90 i 95 percentyla) w oknie 10-minutowym:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Tworzenie alertów dotyczących śledzenia zdarzeń

Pierwszym krokiem do utworzenia alertu jest dotarcie do kwerendy na podstawie, na podstawie którego alert jest wyzwalany. Można użyć dowolnej kwerendy, która ma zostać utworzona jako alert.

1. Otwórz obszar roboczy usługi Log Analytics skojarzony z klastrem USŁUGI HDInsight z witryny Azure portal.
1. W **obszarze Ogólne**wybierz pozycję **Dzienniki**.
1. Uruchom następującą kwerendę, na której chcesz utworzyć alert, a następnie wybierz pozycję **Uruchom**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Kwerenda zawiera listę nieudanych aplikacji uruchomionych w klastrach HDInsight.

1. Wybierz **pozycję Nowa reguła alertu** u góry strony.

    ![Wprowadź kwerendę, aby utworzyć alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Wprowadź kwerendę, aby utworzyć alert1")

1. W oknie **Utwórz regułę** wprowadź kwerendę i inne szczegóły, aby utworzyć alert, a następnie wybierz pozycję **Utwórz regułę alertu**.

    ![Wprowadź kwerendę, aby utworzyć alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Wprowadź kwerendę, aby utworzyć alert2")

### <a name="edit-or-delete-an-existing-alert"></a>Edytowanie lub usuwanie istniejącego alertu

1. Otwórz obszar roboczy usługi Log Analytics w witrynie Azure portal.

1. Z lewego menu w obszarze **Monitorowanie**wybierz pozycję **Alerty**.

1. U góry wybierz pozycję **Zarządzaj regułami alertów**.

1. Wybierz alert, który chcesz edytować lub usunąć.

1. Dostępne są następujące opcje: **Zapisz,** **Odrzuć,** **Wyłącz**i **Usuń**.

    ![Usługa HDInsight Azure Monitor rejestruje edycję usuwania alertów](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie alertów metryk i zarządzanie nimi za pomocą usługi Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Zobacz też

* [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Tworzenie widoków niestandardowych przy użyciu projektanta widoku w usłudze Azure Monitor](../azure-monitor/platform/view-designer.md)
