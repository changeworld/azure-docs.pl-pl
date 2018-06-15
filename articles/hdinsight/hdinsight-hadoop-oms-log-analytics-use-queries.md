---
title: Zapytanie usługi Analiza dzienników Azure do monitorowania klastrów usługi HDInsight Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchamiać zapytania dotyczące usługi Analiza dzienników Azure, aby monitorować zadania uruchomione w klastrze usługi HDInsight.
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
ms.openlocfilehash: 61467d702f3123085fd7e067a8d56c30331c5bc6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401102"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Analiza dzienników Azure zapytania do monitorowania klastrów usługi HDInsight

Poznać niektóre podstawowe scenariusze dotyczące sposobu używania usługi Analiza dzienników Azure do monitorowania klastrów usługi HDInsight Azure:

* [Analizowanie metrykę klastra usługi HDInsight](#analyze-hdinsight-cluster-metrics)
* [Wyszukiwanie określonego dziennika komunikatów](#search-for-specific-log-messages)
* [Utwórz alerty zdarzeń](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Wymagania wstępne

* Należy skonfigurować klaster usługi HDInsight, aby użyć usługi Analiza dzienników Azure. Aby uzyskać instrukcje, zobacz [Użyj Azure Log Analytics z klastrami HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Należy dodać rozwiązania zarządzania specyficznych dla klastra usługi HDInsight [analizy dzienników](../operations-management-suite/operations-management-suite-overview.md) obszaru roboczego, zgodnie z opisem w [rozwiązań do zarządzania klastra Dodaj HDInsight do analizy dzienników](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizowanie metrykę klastra usługi HDInsight

Dowiedz się, jak wyszukiwanie określonych metryk dla klastra usługi HDInsight.

1. Otwórz klaster usługi HDInsight, który został skojarzony z Azure Log Analytics w portalu Azure.
2. Kliknij przycisk **monitorowanie**, a następnie kliknij przycisk **Otwórz pulpit nawigacyjny OMS**.

    ![Otwórz pulpit nawigacyjny OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS Otwórz pulpit nawigacyjny")

2. Kliknij przycisk **wyszukiwania dziennika** w menu po lewej stronie.

    ![Otwórz dziennik wyszukiwania](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Otwórz dziennik wyszukiwania")

3. Wpisz poniższe zapytanie w polu wyszukiwania, aby wyszukać wszystkie metryki wszystkie dostępne metryki dla wszystkich klastrów HDInsight skonfigurowany tak, aby Analiza dzienników Azure, a następnie naciśnij klawisz **ENTER**.

        `search *` 

    ![Wyszukaj wszystkie metryki](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Wyszukaj wszystkie metryki")

    Dane wyjściowe są następującą postać:

    ![Wyszukaj wszystkie wyniki metryki](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Wyszukaj wszystkie metryki danych wyjściowych.")

5. W lewym okienku w obszarze **typu**, wybierz metrykę, które chcesz odnajdywać dogłębną analizę, a następnie kliknij przycisk **Zastosuj**. Poniższy zrzut ekranu przedstawia `metrics_resourcemanager_queue_root_default_CL` wybrany typ. 

    > [!NOTE]
    > Może zajść potrzeba kliknięcia przycisku **[+] więcej** przycisk, aby znaleźć metryki, którego szukasz. Ponadto **Zastosuj** przycisku jest w dolnej części listy, więc musi przewiń go wyświetlać.
    > 
    >    

    Zwróć uwagę, że zapytanie w polu tekstowym zmienia do przedstawionego w polu wyróżnione na poniższym zrzucie ekranu:

    ![Wyszukaj określonych metryk](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "wyszukiwanie określonych metryk")

6. Aby wyświetlić elementy podrzędne tego określonej metryki. Na przykład można dostosować istniejące dane wyjściowe na podstawie średniej zasoby używane w 10-minutowych interwałach według nazwy klastra za pomocą następującej kwerendy:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Zamiast uściślenie na podstawie średniej zasoby używane, możesz użyć następującego zapytania uściślić wyniki na podstawie, gdy użyte zasoby maksymalną (a także percentyl 90 i 95) w oknie 10 minut:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Wyszukiwanie określonego dziennika komunikatów

Dowiedz się, jak wyglądają komunikaty o błędach podczas określonego okna czasowego. Kroki opisane w tym miejscu są tylko jeden przykład, w jaki sposób można przyjeździe komunikat o błędzie można są zainteresowani. Można użyć dowolnej właściwości, która jest dostępna wyszukać błędy, które próbujesz odnaleźć.

1. Otwórz klaster usługi HDInsight, który został skojarzony z Azure Log Analytics w portalu Azure.
2. Kliknij przycisk **monitorowanie**, a następnie kliknij **Otwórz pulpit nawigacyjny OMS**.

    ![Otwórz pulpit nawigacyjny OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS Otwórz pulpit nawigacyjny")

2. W portalu OMS z ekranu głównego, kliknij **wyszukiwania dziennika**.

    ![Otwórz dziennik wyszukiwania](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Otwórz dziennik wyszukiwania")

3. Typ następujące zapytanie, aby wyszukać wszystkie komunikaty o błędach dla wszystkich klastrów HDInsight skonfigurowana do używania usługi Analiza dzienników Azure, a następnie naciśnij **ENTER**. 

         search "Error"

    Powinna pojawić się dane wyjściowe podobne do następujących danych wyjściowych:

    ![Wyszukaj wszystkie dane wyjściowe błędów](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Wyszukaj wszystkie błędy danych wyjściowych")

5. W lewym okienku w obszarze **typu** kategorii, typ błędu, który chcesz wyświetlić dogłębną analizę, a następnie kliknij przycisk **Zastosuj**.  Należy zauważyć, że wyniki są wprowadzono ulepszenia Pokaż tylko błąd wybranego typu.
7. Możesz przejść głębiej do szczegółów tego błędu listy za pomocą opcji dostępnych w okienku po lewej stronie. Na przykład: 

    - Aby wyświetlić komunikaty o błędach z węzłem procesu roboczego określonych:

        ![Wyszukaj określone błędy w danych wyjściowych](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "wyszukiwania dla określonych błędów w danych wyjściowych")

    - Aby wyświetlić błąd wystąpił w określonym czasie:

        ![Wyszukaj określone błędy w danych wyjściowych](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "wyszukiwania dla określonych błędów w danych wyjściowych")

9. Aby wyświetlić szczegóły błędu. Możesz kliknąć **[+] Pokaż więcej** aby przyjrzeć się komunikat błędu.

    ![Wyszukaj określone błędy w danych wyjściowych](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "wyszukiwania dla określonych błędów w danych wyjściowych")

## <a name="create-alerts-for-tracking-events"></a>Utwórz alerty dla zdarzenia śledzenia

Pierwszy krok w celu utworzenia alertu jest na zapytania oparte na zostanie wyzwolony alert. Dla uproszczenia umożliwia Użyj następującego zapytania, który zawiera listę wszystkich nieudanych aplikacji uruchamianych w klastrach HDInsight.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Można użyć dowolnego zapytania, który ma być tworzony alert.

1. Otwórz klaster usługi HDInsight, który został skojarzony z Azure Log Analytics w portalu Azure.
2. Kliknij przycisk **monitorowanie**, a następnie kliknij przycisk **Otwórz pulpit nawigacyjny OMS**.

    ![Otwórz pulpit nawigacyjny OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS Otwórz pulpit nawigacyjny")

2. W portalu OMS z ekranu głównego, kliknij **wyszukiwania dziennika**.

    ![Otwórz dziennik wyszukiwania](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Otwórz dziennik wyszukiwania")

3. Uruchom następującą kwerendę, na którym chcesz utworzyć alert, a następnie naciśnij klawisz **ENTER**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Kliknij przycisk **alertu** górnej części strony.

    ![Wprowadź zapytanie w celu utworzenia alertu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "wprowadź zapytanie w celu utworzenia alertu")

4. W **Dodaj regułę alertu** okna, wprowadź zapytanie i inne szczegóły, aby utworzyć alert, a następnie kliknij przycisk **zapisać**.

    ![Wprowadź zapytanie w celu utworzenia alertu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "wprowadź zapytanie w celu utworzenia alertu")

    Zrzut ekranu przedstawia konfigurację do wysyłania powiadomień e-mail, gdy kwerenda alertu zwraca dane wyjściowe.

5. Możesz także edytować lub usunąć istniejącego alertu. Aby to zrobić, z dowolnej strony w portalu OMS kliknij **ustawienia** ikony.

    ![Wprowadź zapytanie w celu utworzenia alertu](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "wprowadź zapytanie w celu utworzenia alertu")

6. Z **ustawienia** kliknij przycisk **alerty** do wyświetlenia alertów został utworzony. Można również włączyć lub Wyłącz alert, go edytować lub usunąć go. Aby uzyskać więcej informacji, zobacz [Praca z reguły alertów w analizy dzienników](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Zobacz także

* [Praca z analizy dzienników](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Tworzyć reguły alertów w analizy dzienników](../log-analytics/log-analytics-alerts-creating.md)
