---
title: Wykonywanie zapytań dotyczących dzienników Azure Monitor do monitorowania klastrów usługi Azure HDInsight
description: Dowiedz się, jak uruchamiać zapytania dotyczące dzienników Azure Monitor, aby monitorować zadania uruchomione w klastrze usługi HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 031879ac1d0d2dd1148c0c37ee72c60d093f8a7d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70809382"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Wykonywanie zapytań dotyczących dzienników Azure Monitor do monitorowania klastrów usługi HDInsight

Zapoznaj się z podstawowymi scenariuszami dotyczącymi używania dzienników Azure Monitor do monitorowania klastrów usługi Azure HDInsight:

* [Analizowanie metryk klastra usługi HDInsight](#analyze-hdinsight-cluster-metrics)
* [Wyszukaj określone komunikaty dziennika](#search-for-specific-log-messages)
* [Tworzenie alertów dotyczących zdarzeń](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Należy skonfigurować klaster usługi HDInsight do używania dzienników Azure Monitor i dodać do obszaru roboczego usługi rejestrowania Azure Monitor specyficzne dla klastra usługi HDInsight. Aby uzyskać instrukcje, zobacz [Korzystanie z dzienników Azure monitor z klastrami usługi HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizowanie metryk klastra usługi HDInsight

Dowiedz się, jak szukać określonych metryk dla klastra usługi HDInsight.

1. Otwórz obszar roboczy Log Analytics, który jest skojarzony z klastrem usługi HDInsight z Azure Portal.
2. Wybierz kafelek **przeszukiwanie dzienników** .
3. Wpisz następujące zapytanie w polu wyszukiwania, aby wyszukać wszystkie metryki dla wszystkich dostępnych metryk dla wszystkich klastrów usługi HDInsight skonfigurowanych do korzystania z dzienników Azure Monitor, a następnie wybierz pozycję **Uruchom**.

        search *

    ![Przeszukaj wszystkie metryki](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Przeszukaj wszystkie metryki")

    Dane wyjściowe wyglądają następująco:

    ![Przeszukaj wszystkie dane wyjściowe metryk](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Przeszukaj wszystkie dane wyjściowe metryk")

5. W lewym okienku w obszarze **Typ**wybierz metrykę, do której chcesz Dig, a następnie wybierz pozycję **Zastosuj**. Poniższy zrzut ekranu przedstawia `metrics_resourcemanager_queue_root_default_CL` wybrany typ.

    > [!NOTE]  
    > Może być konieczne wybranie przycisku **[+] więcej** , aby znaleźć szukaną metrykę. Ponadto przycisk **Zastosuj** znajduje się u dołu listy, więc musisz przewinąć w dół, aby go zobaczyć.

    Zwróć uwagę, że zapytanie w polu tekstowym zmieni się na jedno pokazane w wyróżnionym polu na poniższym zrzucie ekranu:

    ![Wyszukaj określone metryki](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Wyszukaj określone metryki")

6. Aby Dig bardziej szczegółowy pomiar. Na przykład można uściślić istniejące dane wyjściowe w oparciu o średnią liczbę zasobów używanych w 10-minutowych interwałach w kategorii według nazwy klastra, korzystając z następującej kwerendy:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Zamiast rafinacji na podstawie średniej używanych zasobów, można użyć następującego zapytania, aby uściślić wyniki w zależności od tego, kiedy zostały użyte maksymalne zasoby (a także 90 i używany 95. percentyl) w 10-minutowych oknach:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Wyszukaj określone komunikaty dziennika

Dowiedz się, jak wyszukiwać komunikaty o błędach w określonym przedziale czasu. W tym kroku przedstawiono tylko jeden przykład, w jaki można dotrzeć do pożądanego komunikatu o błędzie. Możesz użyć dowolnej właściwości, która jest dostępna do wyszukiwania błędów, które próbujesz znaleźć.

1. Otwórz obszar roboczy Log Analytics, który jest skojarzony z klastrem usługi HDInsight z Azure Portal.
2. Wybierz kafelek **przeszukiwanie dzienników** .
3. Wpisz następujące zapytanie, aby wyszukać wszystkie komunikaty o błędach dla wszystkich klastrów usługi HDInsight skonfigurowanych do korzystania z dzienników Azure Monitor, a następnie wybierz pozycję **Uruchom**. 

         search "Error"

    Zostaną wyświetlone dane wyjściowe podobne do następujących:

    ![Przeszukaj wszystkie błędy wyjściowe](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Przeszukaj wszystkie błędy wyjściowe")

4. W okienku po lewej stronie w obszarze **Typ** kategorii wybierz typ błędu, który ma być Dig głębokiego, a następnie wybierz pozycję **Zastosuj**.  Zauważ, że wyniki są rafinowane, aby pokazać tylko błąd wybranego typu.
5. Można Dig bardziej szczegółową listę błędów, używając opcji dostępnych w lewym okienku. Na przykład:

    - Aby wyświetlić komunikaty o błędach z określonego węzła procesu roboczego:

        ![Wyszukaj określone błędy output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Wyszukaj określone błędy output1")

    - Aby zobaczyć, że wystąpił błąd w określonym czasie:

        ![Wyszukaj określone błędy output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Wyszukaj określone błędy output2")

6. Aby zobaczyć konkretny błąd. Możesz wybrać opcję **[+] Pokaż więcej** , aby zobaczyć rzeczywisty komunikat o błędzie.

    ![Wyszukaj określone błędy output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Wyszukaj określone błędy output3")

## <a name="create-alerts-for-tracking-events"></a>Tworzenie alertów dotyczących zdarzeń śledzenia

Pierwszym krokiem tworzenia alertu jest dotarcie do zapytania, na podstawie którego zostanie wyzwolony alert. Możesz użyć dowolnego zapytania, które chcesz utworzyć alert.

1. Otwórz obszar roboczy Log Analytics, który jest skojarzony z klastrem usługi HDInsight z Azure Portal.
2. Wybierz kafelek **przeszukiwanie dzienników** .
3. Uruchom następujące zapytanie, dla którego chcesz utworzyć alert, a następnie wybierz pozycję **Uruchom**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    Zapytanie zawiera listę aplikacji, które nie działają w klastrach usługi HDInsight.

4. Wybierz pozycję **Nowa reguła alertu** w górnej części strony.

    ![Wprowadź zapytanie, aby utworzyć element alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Wprowadź zapytanie, aby utworzyć element alert1")

5. W oknie **Tworzenie reguły** wprowadź zapytanie i inne szczegóły, aby utworzyć alert, a następnie wybierz pozycję **Utwórz regułę alertu**.

    ![Wprowadź zapytanie, aby utworzyć element Alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Wprowadź zapytanie, aby utworzyć element Alert2")

Aby edytować lub usunąć istniejący Alert:

1. Otwórz obszar roboczy Log Analytics z Azure Portal.
2. W menu po lewej stronie wybierz pozycję **alert**.
3. Wybierz Alert, który chcesz edytować lub usunąć.
4. Istnieją następujące opcje: **Zapisz**, **Odrzuć**, **Wyłącz**i **Usuń**.

    ![Alert dziennika Azure Monitor usługi HDInsight usuwanie edycji](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów metryk i zarządzanie nimi przy użyciu Azure monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Zobacz także

* [Tworzenie widoków niestandardowych za pomocą projektanta widoków w Azure Monitor](../azure-monitor/platform/view-designer.md)
* [Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure Monitor](../azure-monitor/platform/alerts-metric.md)
