---
title: Jak monitorować dostępność klastra za pomocą Ambari i dzienników Azure Monitor
description: Informacje na temat używania dzienników Ambari i Azure Monitor do monitorowania kondycji i dostępności klastra.
keywords: monitorowanie, Ambari, monitor, log Analytics, alert, dostępność, kondycja
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 07b82f475074f5b55a2a5a93f7a59008476233c8
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934262"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Jak monitorować dostępność klastra za pomocą Ambari i dzienników Azure Monitor

Klastry usługi HDInsight obejmują zarówno platformę Apache Ambari, która udostępnia informacje o kondycji w skrócie i wstępnie zdefiniowane alerty, jak również integrację dzienników Azure Monitor, która udostępnia metryki i dzienniki queryable, a także konfigurowalne alerty.

Ten dokument pokazuje, jak używać tych narzędzi do monitorowania klastra i przeprowadzania kilku przykładów konfigurowania alertu Ambari, monitorowania częstotliwości dostępności węzłów oraz tworzenia alertu Azure Monitor, który jest uruchamiany, gdy puls nie został odebrany z co najmniej jednego węzła w ciągu pięciu godzin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Pulpit nawigacyjny

Dostęp do pulpitu nawigacyjnego Ambari można uzyskać przez kliknięcie linku **macierzystego Ambari** w sekcji **pulpity nawigacyjne klastra** w bloku przegląd usługi HDInsight w Azure Portal jak pokazano poniżej. Można też uzyskać do niego dostęp, wprowadzając następujący adres URL w przeglądarce [https://\<ClusterName\>. azurehdinsight.NET](https://clustername.azurehdinsight.net/)

![Widok portalu zasobów usługi HDInsight](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Następnie zostanie wyświetlony monit o podanie nazwy użytkownika i hasła logowania do klastra. Wprowadź poświadczenia wybrane podczas tworzenia klastra.

Następnie nastąpi przekierowanie do pulpitu nawigacyjnego Ambari, który zawiera elementy widget, które pokazują kilku metryk, aby szybko zapoznać się z kondycją klastra usługi HDInsight. Te widżety pokazują takie metryki jak liczba aktywnych węzłów danych (węzłów procesu roboczego) i JournalNodes (węzeł dozorcy), czas działania NameNodes (węzły główne), a także metryki charakterystyczne dla określonych typów klastrów, takie jak czas pracy zasobów związanych z ResourceManager dla klastrów Spark i Hadoop.

![Pulpit nawigacyjny Ambari](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosty — Wyświetlanie stanu poszczególnych węzłów

Możesz również wyświetlić informacje o stanie poszczególnych węzłów. Kliknij kartę **hosty** , aby wyświetlić listę wszystkich węzłów w klastrze i wyświetlić podstawowe informacje o każdym węźle. Zielona kontrola po lewej stronie każdej nazwy węzła wskazuje, że wszystkie składniki znajdują się w węźle. Jeśli składnik nie działa w węźle, zamiast zielonego sprawdzenia zobaczysz czerwony trójkąt ostrzegawczy.

![Widok hostów Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Następnie możesz kliknąć **nazwę** węzła, aby wyświetlić bardziej szczegółowe metryki hosta dla danego węzła. Ten widok przedstawia stan/dostępność każdego pojedynczego składnika.

![Widok pojedynczego węzła Ambari hosty](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alerty Ambari

Ambari oferuje również kilka konfigurowalnych alertów, które mogą udostępniać powiadomienia o określonych zdarzeniach. Gdy alerty są wyzwalane, są wyświetlane w lewym górnym rogu Ambari w czerwonym wskaźniku zawierającym liczbę alertów. Kliknięcie tego wskaźnika powoduje wyświetlenie listy bieżących alertów.

![Liczba alertów Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Aby wyświetlić listę definicji alertów i ich Stanów, kliknij kartę **alerty** , jak pokazano poniżej.

![Widok definicji alertów Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari oferuje wiele wstępnie zdefiniowanych alertów dotyczących dostępności, w tym:

| Nazwa alertu                        | Opis                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsumowanie kondycji węzła datanode           | Ten alert na poziomie usługi jest wyzwalany, jeśli istnieją węzły datanodes o złej kondycji                                                                                                                |
| Kondycja NameNode wysokiej dostępności | Ten alert na poziomie usługi jest wyzwalany, jeśli aktywne NameNode lub wstrzymanie NameNode nie jest uruchomione.                                                                              |
| Procent dostępnych JournalNodes    | Ten alert jest wyzwalany, jeśli liczba w dół JournalNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki testów procesu JournalNode. |
| Dostępne węzły datanodes       | Ten alert jest wyzwalany, jeśli liczba węzłów danych w dół w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu elementu datanode.       |

Pełna lista alertów Ambari, które ułatwiają monitorowanie dostępności klastra, można znaleźć [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Aby wyświetlić szczegóły alertu lub zmodyfikować kryteria, kliknij **nazwę** alertu. Zapoznaj się z przykładem **Podsumowanie kondycji węzła** . Można wyświetlić opis alertu, a także określone kryteria, które będą wyzwalać alert "ostrzeżenie" lub "krytyczny" oraz interwał sprawdzania dla kryteriów. Aby edytować konfigurację, kliknij przycisk **Edytuj** w prawym górnym rogu pola konfiguracji.

![Konfiguracja alertu Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Tutaj można edytować opis i, co ważniejsze, interwał sprawdzania i progi alertów ostrzegawczych lub krytycznych.

![Widok edycji konfiguracji alertów Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

W tym przykładzie można sprawić, że 2 węzły w złej kondycji wyzwalają alert krytyczny, a 1 element datanode w złej kondycji wyzwala ostrzeżenie. Po zakończeniu edycji kliknij przycisk **Zapisz** .

### <a name="email-notifications"></a>Powiadomienia e-mail

Opcjonalnie możesz również skonfigurować powiadomienia e-mail dotyczące alertów Ambari. Aby to zrobić, na karcie **alerty** kliknij przycisk **Akcje** w lewym górnym rogu, a następnie **Zarządzaj powiadomieniami.**

![Akcja zarządzania Ambariami](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Zostanie otwarte okno dialogowe umożliwiające zarządzanie powiadomieniami o alertach. **+** Kliknij w dolnej części okna dialogowego i wypełnij pola wymagane, aby podać Ambari z serwerem poczty e-mail, z którego mają być wysyłane wiadomości e-mail.

> [!TIP]
> Konfigurowanie powiadomień e-mail Ambari może być dobrym sposobem na otrzymywanie alertów w jednym miejscu podczas zarządzania wieloma klastrami usługi HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integracja dzienników Azure Monitor

Dzienniki Azure Monitor umożliwiają gromadzenie i agregowanie danych generowanych przez wiele zasobów, takich jak klastry usługi HDInsight, w celu zapewnienia ujednoliconego środowiska monitorowania.

Zgodnie z wymaganiami wstępnymi do przechowywania zebranych danych potrzebny jest obszar roboczy Log Analytics. Jeśli jeszcze tego nie zrobiono, możesz wykonać poniższe instrukcje: [Utwórz obszar roboczy log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Włącz integrację dzienników Azure Monitor usługi HDInsight

Na stronie zasobów klastra usługi HDInsight w portalu Kliknij blok **Operations Management Suite** . Następnie kliknij pozycję **Włącz** , a następnie wybierz obszar roboczy log Analytics z listy rozwijanej.

![Blok pakietu Operations Management Suite](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Zapytanie dotyczące metryk i tabel dzienników w bloku dzienniki

Po włączeniu integracji dzienników Azure Monitor (może to potrwać kilka minut) przejdź do zasobów **obszaru roboczego log Analytics** i kliknij blok **dzienniki** .

![Blok dzienników obszaru roboczego Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Blok **dzienniki** zawiera kilka przykładowych zapytań, takich jak:

| Nazwa zapytania                      | Opis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostępność komputerów dzisiaj    | Wykres przedstawiający liczbę komputerów, które wysyłają dzienniki, co godzinę                     |
| Wyświetl pulsy                 | Wyświetl listę wszystkich pulsów komputerów z ostatniej godziny                           |
| Ostatni puls każdego komputera | Pokaż ostatni puls Wysłany przez każdy komputer                             |
| Niedostępne komputery           | Wyświetl listę wszystkich znanych komputerów, które nie wysłały pulsu w ostatnich 5 godzinach |
| Szybkość dostępności               | Oblicz szybkość dostępności każdego połączonego komputera                |

Przykładowo Uruchom przykładowe zapytanie o **szybkości dostępności** , klikając polecenie **Uruchom** w zapytaniu, jak pokazano na poniższym zrzucie ekranu. Spowoduje to wyświetlenie wartości procentowej częstotliwości dostępności każdego węzła w klastrze. Jeśli włączono wiele klastrów usługi HDInsight do wysyłania metryk do tego samego obszaru roboczego Log Analytics, zostanie wyświetlona szybkość dostępności dla wszystkich węzłów w tych klastrach.

![Przykładowe zapytanie Log Analytics "szybkość dostępności" bloku dzienników obszaru roboczego](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Częstotliwość dostępności jest mierzona w okresie 24-godzinnym, więc klaster będzie musiał działać przez co najmniej 24 godziny, zanim zobaczysz dokładne stawki dostępności.

Możesz przypiąć tę tabelę do udostępnionego pulpitu nawigacyjnego, klikając pozycję **Przypnij** w prawym górnym rogu. Jeśli nie masz żadnych zapisywalnych udostępnionych pulpitów nawigacyjnych, możesz zobaczyć, jak utworzyć jedną z nich: [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alerty Azure Monitor

Można również skonfigurować alerty Azure Monitor, które będą wyzwalane, gdy wartość metryki lub wyniki zapytania spełniają określone warunki. Na przykład utwórz alert, aby wysłać wiadomość e-mail, gdy co najmniej jeden węzeł nie wysłał pulsu w ciągu 5 godzin (tj. jest to prawdopodobnie niedostępne).

W bloku **dzienniki** Uruchom przykładowe zapytanie **niedostępne komputery** , klikając polecenie **Uruchom** dla tego zapytania, jak pokazano poniżej.

![Przykładowa kwerenda Log Analytics bloku dzienników obszaru roboczego "niedostępne komputery"](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Jeśli wszystkie węzły są dostępne, ta kwerenda powinna zwrócić 0 wyników teraz. Kliknij pozycję **Nowa reguła alertu** , aby rozpocząć konfigurowanie alertu dla tego zapytania.

![Log Analytics nowej regule alertu obszaru roboczego](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Alert zawiera trzy składniki: *zasób* , dla którego ma zostać utworzona reguła (log Analytics w tym przypadku obszar roboczy), *warunek* wyzwalania alertu oraz *grupy akcji* , które określają, co się stanie w przypadku alertu wyzwalane.

Kliknij **tytuł warunku**, jak pokazano poniżej, aby zakończyć konfigurowanie logiki sygnałów.

![Warunek reguły alertu](media/hdinsight-cluster-availability/portal-condition-title.png)

Spowoduje to otwarcie bloku **Konfiguruj logikę sygnału** .

Skonfiguruj sekcję **logika alertów** w następujący sposób:

*Na podstawie: Liczba wyników, warunek: Większe niż, próg: 2,0.*

Ponieważ to zapytanie zwraca tylko niedostępne węzły jako wyniki, jeśli liczba wyników jest większa niż 0, alert powinien zostać wywołany.

W sekcji **oceniane na podstawie** Określ **okres** i **częstotliwość** w zależności od tego, jak często chcesz sprawdzać dostępność niedostępnych węzłów.

Należy pamiętać, że na potrzeby tego alertu należy upewnić się, że **okres = częstotliwość.** Więcej informacji na temat okresu, częstotliwości i innych parametrów alertu można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Po zakończeniu konfigurowania logiki sygnałów kliknij przycisk **gotowe** .

![Reguła alertu Konfigurowanie logiki sygnału](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Jeśli nie masz jeszcze istniejącej grupy akcji, kliknij pozycję **Utwórz nową** w sekcji **grupy akcji** .

![Nowa grupa akcji dla reguły alertu](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Spowoduje to otwarcie bloku **Dodaj grupę akcji** . Wybierz **nazwę grupy akcji**, **krótką nazwę**, **subskrypcję**i **grupę zasobów.** W sekcji **Akcje** wybierz **nazwę akcji** i wybierz pozycję **email/SMS/push/Voice** jako **Typ akcji.**

> [!NOTE]
> Istnieje kilka innych akcji, które mogą być wyzwalane przez alert oprócz wiadomości E-mail/SMS/wypychania/głosu, takich jak usługa Azure Functions, LogicApp, webhook, narzędzia ITSM i Automatyzacja. [Dowiedz się więcej.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Spowoduje to otwarcie bloku **poczty e-mail/wiadomości SMS/wypychania/głosu** . Wybierz **nazwę** odbiorcy, **zaznacz** pole adres **e-mail** , a następnie wpisz adres e-mail, na który ma być wysyłany alert. Kliknij przycisk **OK** w bloku **wiadomości e-mail/SMS/wypychanie/głos** , a następnie w bloku **Dodaj grupę akcji** , aby zakończyć konfigurowanie grupy akcji.

![Dodaj grupę akcji dla reguły alertu](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zamknięciu tych zamków powinna zostać wyświetlona grupa akcji wymieniona w sekcji **grupy akcji** . Na koniec Wypełnij sekcję **szczegóły alertu** , wpisując nazwę i **Opis** **reguły alertu** i wybierając **ważność**.
Kliknij przycisk **Utwórz regułę alertu** , aby zakończyć.

![Zakończenie tworzenia reguły alertu](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Możliwość określania **ważności** jest zaawansowanym narzędziem, które może być używane podczas tworzenia wielu alertów. Na przykład można utworzyć jeden alert, aby zgłosić ostrzeżenie (ważność 1), jeśli jeden węzeł główny ulegnie awarii i inny alert, który podnosi krytyczne (ważność 0) w prawdopodobnym zdarzeniu, że oba węzły główne przechodzą.

Po spełnieniu warunku tego alertu alert zostanie uruchomiony i otrzymasz wiadomość e-mail z informacjami o alercie, takimi jak:

![Wiadomość e-mail z alertem Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Możesz również wyświetlić wszystkie alerty, które zostały wywołane, pogrupowane według ważności, przechodząc do bloku **alerty** w **obszarze roboczym log Analytics**.

![Log Analytics alertów obszaru roboczego](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Kliknięcie grupowania ważności (tj. **ważność 1,** jak zostało wyróżnione powyżej) spowoduje wyświetlenie rekordów dla wszystkich alertów o ważności, które zostały wywołane jak poniżej:

![Log Analyticsnie alertów ważność 1](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Następne kroki
- [Dostępność i niezawodność klastrów Apache Hadoop w usłudze HDInsight](hdinsight-high-availability-linux.md)
