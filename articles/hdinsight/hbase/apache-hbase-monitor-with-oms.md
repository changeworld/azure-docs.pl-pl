---
title: Monitorowanie bazy danych HBase z Azure Log Analytics — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: Analiza dzienników Azure umożliwia monitorowanie klastrów HDInsight HBase.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="monitor-hbase-with-log-analytics"></a>Monitor HBase z analizy dzienników

HDInsight HBase monitorowanie używa usługi Analiza dzienników Azure na potrzeby zbierania miar wydajności bazy danych HDInsight HBase z węzłów klastra usługi HDInsight. Monitor oferuje wizualizacje specyficzne dla bazy danych HBase i pulpity nawigacyjne, narzędzia do wyszukiwania metryki i możliwość tworzenia niestandardowych reguł monitorowania i alertów. Można monitorować metryki dla wielu klastrów HDInsight HBase przez wiele subskrypcji Azure.

Analiza dzienników jest usługą [Azure](../../operations-management-suite/operations-management-suite-overview.md) który monitoruje chmury i lokalnych środowiskach utrzymywać ich dostępności i wydajności. Analiza dzienników zbiera dane generowane przez zasobów w swoich środowiskach w chmurze i lokalnie i z innych narzędzi monitorowania, w celu zapewnienia analizy całej wielu źródeł.

[Rozwiązania do zarządzania analizy dziennika](../../log-analytics/log-analytics-add-solutions.md) Dodawanie funkcji do analizy dzienników, zapewniając dodatkowe dane i narzędzia do analizy. Rozwiązania do zarządzania analizy dziennika są Kolekcja reguł nabycia logiki, wizualizacji i danych zawierających metryki dla określonego obszaru. Rozwiązanie może również zdefiniować nowe typy rekordów mają być zbierane i można analizować te rekordy dziennika wyszukiwania lub nowe funkcje interfejsu użytkownika.

[Szczegółowe informacje o & Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) jest zbudowany na platformie analizy dzienników. Można korzystać z funkcji analizy dzienników i płacić za GB pozyskanych w ramach usługi lub przełącznika obszaru roboczego do warstwy szczegółowe informacje o & Analytics i należy zwrócić na węzeł zarządzane przez usługę. Szczegółowe informacje o & Analytics zapewnia jest nadzbiorem możliwości oferowane przez analizy dzienników. To rozwiązanie monitorowanie bazy danych HBase jest dostępna z analizy dzienników lub szczegółowe informacje o & Analytics.

Podczas obsługi administracyjnej rozwiązanie monitorowania HBase HDInsight tworzenia obszaru roboczego analizy dzienników. Każdy obszar roboczy jako unikatowy środowiska analizy dzienników z własnych danych repozytorium, źródła danych i rozwiązania. Możesz utworzyć wiele obszarów roboczych w ramach subskrypcji do obsługi wielu środowiskach, na przykład produkcyjnym i testowania.

## <a name="provision-hdinsight-hbase-monitoring"></a>Provision HDInsight HBase monitorowania

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu subskrypcji platformy Azure.
2. W **nowy** okienku w obszarze **Marketplace**, wybierz pozycję **monitorowanie i zarządzanie**.
3. W **monitorowanie i zarządzanie** okienku wybierz **zobaczyć wszystkie**.

    ![Monitorowanie + okienko zarządzania](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. Na liście, wyszukaj **rozwiązań do zarządzania** poza pasmem. Po prawej stronie **rozwiązań do zarządzania**, wybierz pozycję **więcej**.

    ![Monitorowanie + okienko zarządzania](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. W **rozwiązań do zarządzania** okienku wybierz rozwiązanie zarządzania monitorowania HBase HDInsight, aby dodać do obszaru roboczego.

    ![Okienko rozwiązań do zarządzania](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. W okienku rozwiązania zarządzania, przejrzyj informacje na temat rozwiązania do zarządzania, a następnie wybierz **Utwórz**. 
7. W *Nazwa rozwiązania zarządzania* okienku, wybierz istniejący obszar roboczy do skojarzenia z rozwiązaniem do zarządzania lub Utwórz nowy obszar roboczy analizy dzienników, a następnie wybierz go.
8. Zmień ustawienia obszaru roboczego dla subskrypcji platformy Azure, grupy zasobów i lokalizacji, zależnie od potrzeb. 
    ![obszar roboczy rozwiązania](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Wybierz pozycję **Utwórz**.  
10. Aby użyć tego nowego rozwiązania do zarządzania w obszaru roboczego, przejdź do **analizy dzienników** > ***nazwa obszaru roboczego*** > **rozwiązań**. Wpis dla rozwiązania do zarządzania jest wyświetlana na liście. Wybierz wpis, aby przejść do rozwiązania.

    ![Dziennik rozwiązań analitycznych](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. Zostanie wyświetlony w okienku HDInsight HBase rozwiązanie monitorowania.

    ![Okienko rozwiązania HBase HDInsight](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Kafelki podsumowania nie pokazuj żadnych danych, ponieważ nie zostały jeszcze skonfigurowane klaster HDInsight HBase do wysyłania danych do analizy dzienników.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Połącz klaster HDInsight HBase do analizy dzienników

Do korzystania z narzędzi dostarczanych przez monitorowanie HBase HDInsight, należy skonfigurować klaster, tak aby transmitowane metryki z jego serwera regionu, węzłów głównych i węzły dozorcy do analizy dzienników. Ta konfiguracja jest implementowana przez uruchomienie akcji skryptu przed klastra HDInsight HBase.

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>Uzyskaj identyfikator obszaru roboczego analizy dzienników i klucz obszaru roboczego

Należy dziennika analizy identyfikator i klucz obszaru roboczego umożliwiające węzłów w klastrze do uwierzytelniania za pomocą analizy dzienników. Aby wyświetlić te wartości:

1. Wybierz omówienie z Twojej bazy danych HBase monitorowania okienku w portalu Azure.

    ![W okienku monitorowanie HBase rozwiązania](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Wybierz portalu OMS można uruchomić portalu OMS w nowej karty przeglądarki lub w oknie.

    ![Wybierz portalu OMS](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. Na stronie głównej portalu OMS wybierz ustawienia.

    ![Portal OMS](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Wybierz połączone źródła, serwerów z systemem Linux.

    ![Wybierz źródło połączenia, a następnie serwerów z systemem Linux](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Należy pamiętać wartości klucza podstawowego i identyfikator obszaru roboczego wyświetlane, ponieważ są to wartości potrzebnych dla akcji skryptu.

### <a name="run-the-script-action"></a>Uruchom akcję skryptu

Aby włączyć zbieranie danych z klastrem usługi HDInsight, HBase, uruchamiać akcji skryptu na wszystkich węzłach w klastrze:

1. Przejdź do okienka klastra HDInsight HBase w portalu Azure.
2. Wybierz **skryptu akcji**.

    ![Akcje skryptu](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Wybierz **przesyłania nowych**.

    ![Przesyłanie nowych](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. W **Prześlij skrypt** akcji, Ustaw typ skryptu `"- Custom"`.
5. Podaj nazwę dla tego skryptu.
6. Aby uzyskać **Bash skryptu URI**, wklej następujący identyfikator URI:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. Aby uzyskać **typy węzłów**, zaznacz wszystkie trzy (**Head**, **Region**, **dozorcy**).
8. W **parametry** polu tekstowym wprowadź swój identyfikator obszaru roboczego i klucz obszaru roboczego, otaczającej każdej wartości w cudzysłowie i rozdzielonych spacją.

        "<Workspace ID>" "<Workspace Key>"

9. Wybierz **Utrwal tę akcję skryptu** ponowne uruchomienie akcji, gdy zostaną dodane nowe węzły do klastra.

    ![Ustawienia akcji skryptu](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Wybierz pozycję **Utwórz**.
11. Akcja skryptu zajmuje kilka minut do uruchomienia. Można monitorować jej status w okienku akcji skryptu.

    ![Akcja skryptu uruchomiona](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. Po ukończeniu działania skryptu powinien zostać wyświetlony zielony znacznik wyboru obok nazwy skryptu, na liście.

    ![Akcja skryptu została ukończona.](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Widok monitorowania HBase HDInsight rozwiązania

Po ukończeniu działania skryptu, powinny pojawić się dane w rozwiązaniu monitorowanie w ciągu kilku minut.

1. W portalu Azure przejdź do okienka rozwiązania HDInsight HBase.
2. Wybierz **omówienie** kartę.
3. W obszarze **Podsumowanie**, zobacz kafelka wskazującą liczbę serwerów regionu, które są monitorowane.

    ![Kafelek podsumowania monitorowania](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Wybierz Kafelek z regionu liczby serwerów. Główny pulpit nawigacyjny jest wyświetlany.
5. Ten pulpit nawigacyjny zapewnia dostęp do danych statystycznych dotyczących regionów, liczba zapisu z wyprzedzeniem dziennika (odnowy) używany, Kolekcja wyszukiwania analizy dzienników (takie jak w przypadku serwera region dzienniki, dzienniki Phoenix i wyjątków) i kolekcji popularnych wykresów do wizualizacji odpowiednich metryki. 

    ![Główny pulpit nawigacyjny](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. Wybranie jednego z tych będzie przejść do widoku wyszukiwania dziennika, gdy uściślenie kwerendy i eksplorować dane bardziej szczegółowo.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie alertów w analizy dzienników](../../log-analytics/log-analytics-alerts-creating.md)
* [Wyszukiwanie danych z dziennika wyszukiwania w Azure Log Analytics](../../log-analytics/log-analytics-log-searches.md).
