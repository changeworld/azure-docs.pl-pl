---
title: Jak monitorować dostępność klastra z dziennikami Ambari i usługi Azure Monitor
description: Dowiedz się, jak używać narzędzia Ambari, i usługi Azure Monitor dzienniki do monitorowania kondycji klastra i dostępności.
keywords: monitorowanie systemu ambari, monitorowanie, usługi log analytics, alert, dostępności, kondycji
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 459de569916af14b0efea0ff08b92e5c93ed2369
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718918"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Jak monitorować dostępność klastra z dziennikami Ambari i usługi Azure Monitor

Klastry HDInsight zawierają zarówno Apache Ambari, który zawiera informacje o kondycji na pierwszy rzut oka i wstępnie zdefiniowane alerty, a także integracji dzienników usługi Azure Monitor, oferujący odpytywalny metryki i dzienniki, jak również konfigurowalnych alertów.

Ten dokument przedstawia sposób użycia tych narzędzi do monitorowania klastra i przedstawiono przykłady dotyczące konfigurowania alertu Ambari, monitorowanie stawki dla wersji dostępnej węzła i tworzenia alert usługi Azure Monitor, który jest uruchamiana, gdy nie odebrano pulsu z co najmniej jeden węzeł w ciągu pięciu godzin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny Ambari jest możliwy, klikając **Ambari macierzystego** łącze w **pulpity nawigacyjne klastra** części bloku przeglądu HDInsight w witrynie Azure portal, jak pokazano poniżej. Alternatywnie był on dostępny, wprowadzając następującego adresu URL w przeglądarce [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Widok portalu HDInsight zasobów](media/hdinsight-cluster-availability/portal-overview.png)

Następnie zostanie monit o nazwę użytkownika logowania klastra i hasło. Wprowadź poświadczenia wybrane podczas tworzenia klastra.

Następnie nastąpi przekierowanie do pulpitu nawigacyjnego systemu Ambari, który zawiera elementy widget, które pokazują aspekty metryk, które pozwalają uzyskać szybki przegląd kondycji klastra usługi HDInsight. Tych widżetach Pokaż metryki, takie jak liczba na żywo DataNodes (węzły procesu roboczego) i JournalNodes (dozorcy węzeł), czasu NameNodes (węzłów głównych) jako dobrze metryki specyficzne dla niektórych typów klastra, np. czasu usługi YARN ResourceManager dla klastrów Spark i Hadoop.

![Pulpit nawigacyjny systemu Ambari](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosty — Wyświetl stan poszczególnych węzłów

Można również wyświetlić informacje o stanie dla poszczególnych węzłów. Kliknij przycisk **hosty** kartę, aby wyświetlić listę wszystkich węzłów w klastrze i wyświetlić podstawowe informacje o każdym węźle. Zielony znacznik wyboru po lewej stronie nazwy każdego węzła wskazuje, że wszystkie składniki są włączone na węźle. Jeśli składnik nie działa w węźle, zobaczysz czerwony trójkąt alertu, a nie zielony znacznik wyboru.

![Wyświetl hosty systemu Ambari](media/hdinsight-cluster-availability/ambari-hosts.png)

Możesz następnie kliknąć **nazwa** węzła, aby wyświetlić bardziej szczegółowe metryki hosta dla tego konkretnego węzła. Ten widok przedstawia stan/dostępności poszczególnych składników.

![Hosty Ambari pojedynczy widok węzła](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alerty systemu Ambari

Ambari oferuje także kilka konfigurowalnych alertów, zapewniających powiadomień określonych zdarzeń. Alerty są wyzwalane, są wyświetlane w lewym górnym rogu Ambari we wskaźniku czerwony, zawierające liczbę alertów. Klikając ten wskaźnik pokazuje listę bieżące alerty.

![Liczba alertów systemu Ambari](media/hdinsight-cluster-availability/ambari-alerts.png)

Aby wyświetlić listę definicji alertów oraz ich stan, kliknij przycisk **alerty** karty, jak pokazano poniżej.

![Widok definicji alertów systemu Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari udostępnia wiele wstępnie zdefiniowanych alertów związanych z dostępnością, w tym:

| Nazwa alertu                        | Opis                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsumowanie kondycji DataNode           | Ten poziom usługi alert jest wyzwalany, jeśli istnieją DataNodes złej kondycji                                                                                                                |
| NameNode o wysokiej dostępności kondycji | Ten alert poziom usług jest wyzwalany, jeśli nie są wykonywane Active NameNode lub NameNode w stanie wstrzymania.                                                                              |
| Procent JournalNodes dostępne    | Ten alert jest wyzwalany, jeśli liczba szczegółów JournalNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki JournalNode proces kontroli. |
| Procent DataNodes dostępne       | Ten alert jest wyzwalany, jeśli liczba szczegółów DataNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki DataNode proces kontroli.       |

Pełną listę Ambari alerty monitora pomocy dostępność klastra można znaleźć [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Aby wyświetlić szczegóły alertu, lub zmodyfikuj kryteria, kliknij przycisk **nazwa** alertu. Wykonaj **podsumowanie kondycji DataNode** jako przykład. Można wyświetlić opis alertu, a także określone kryteria, które będą wyzwalać alert "ostrzeżenie" lub "critical" i interwał sprawdzania kryteriów. Aby edytować konfigurację, kliknij przycisk **Edytuj** przycisk w prawym górnym rogu okna konfiguracji.

![Podczas konfigurowania alertu systemu Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

W tym miejscu można edytować opis i, co ważniejsze, sprawdzanie interwału i progi dla alertów ostrzegawczych lub krytycznych.

![Widok edycji podczas konfigurowania alertu systemu Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

W tym przykładzie można utworzyć 2 DataNodes złej kondycji, wyzwalać alert krytyczny i 1 złej kondycji DataNode tylko wyzwalacz ostrzeżenie. Kliknij przycisk **Zapisz** po zakończeniu edycji.

### <a name="email-notifications"></a>Powiadomienia e-mail

Można również opcjonalnie skonfigurować powiadomienia e-mail o alertach Ambari. W tym celu w **alerty** kliknij pozycję **akcje** przycisku w lewym górnym rogu, następnie **Zarządzanie powiadomienia.**

![Ambari Zarządzanie akcji powiadomienia](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Zostanie otwarte okno dialogowe powiadomień o alertach zarządzania. Kliknij przycisk **+** w dolnej części okna dialogowego i wypełnij wymagane pola, aby zapewnić Ambari za pomocą adresu e-mail szczegóły serwera, z którego można wysyłać wiadomości e-mail.

> [!TIP]
> Konfigurowanie narzędzia Ambari powiadomienia e-mail może być dobrym sposobem, aby otrzymywać alerty w jednym miejscu, związane z zarządzaniem wielu klastrów HDInsight.

## <a name="azure-monitor-logs-integration"></a>Usługa Azure Monitor rejestruje integracji

Usługa Azure Monitor dzienniki danych umożliwia generowanych przez wiele zasobów, takich jak klastry HDInsight, mają być zbierane i zagregowane w jednym miejscu, aby osiągnąć ujednoliconego środowiska monitorowania.

Jako warunek wstępny należy obszar roboczy usługi Log Analytics do zapisywania zebranych danych. Jeśli nie utworzono już jeden, możesz wykonać instrukcje w tym miejscu: [Utwórz obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Włączanie integracji dzienników HDInsight Azure Monitor

Na stronie zasobów klastra HDInsight w portalu, kliknij **pakietu Operations Management Suite** bloku. Następnie kliknij przycisk **Włącz** i wybierz swój obszar roboczy usługi Log Analytics z listy rozwijanej.

![Blok HDInsight Operations Management Suite](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Metryki i dzienniki tabele kwerendy w bloku dzienników

Po włączeniu integracji dzienników usługi Azure Monitor (może to potrwać kilka minut,) przejdź do usługi **obszar roboczy usługi Log Analytics** zasobów i kliknięcie **dzienniki** bloku

![Blok dzienników obszaru roboczego analizy dzienników](media/hdinsight-cluster-availability/portal-logs.png)

**Dzienniki** blok zawiera szereg przykładowych zapytań, takich jak:

| Nazwa zapytania                      | Opis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Już dziś dostępności komputerów    | Wykres z liczbą komputerów przesyłania dzienników, co godzinę                     |
| Lista interwałów pulsu                 | Lista wszystkich pulsu komputera z ostatniej godziny                           |
| Ostatni Puls z każdego komputera | Pokaż ostatni Puls wysyłanych przez każdy komputer                             |
| Niedostępnymi komputerami           | Lista wszystkich znanych komputerów, które nie wysyłają pulsu w ciągu ostatnich 5 godzin |
| Stawki dla wersji dostępnej               | Oblicz współczynnik Dostępność poszczególnych połączonych komputerów                |

Na przykład uruchom **stawki dla wersji dostępnej** przykładowe zapytanie, klikając **Uruchom** na tym zapytaniu, jak pokazano na powyższym zrzucie ekranu. Spowoduje to wyświetlenie współczynnik dostępności każdego węzła w klastrze jako wartość procentowa. Po włączeniu wielu klastrów HDInsight wysłać metryki do tego samego obszaru roboczego usługi Log Analytics, zobaczysz stawki dla wersji dostępnej dla wszystkich węzłów w tych klastrach wyświetlane.

![Analiza obszaru roboczego dzienniki bloku "dostępności kurs" Przykładowe zapytanie dziennika](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Stawki dla wersji dostępnej jest mierzony w okresie 24-godzinnego, co klaster będzie musiał ją uruchomić co najmniej 24 godzin zanim zobaczysz stawek dokładne dostępności.

W tej tabeli do udostępnionego pulpitu nawigacyjnego można przypiąć, klikając **numeru Pin** w prawym górnym rogu. Jeśli nie masz zapisywalny udostępnionych pulpitów nawigacyjnych, możesz dowiedzieć się, jak ją utworzyć w tym miejscu: [Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control).

### <a name="azure-monitor-alerts"></a>Alerty usługi Azure Monitor

Można również ustawić alerty usługi Azure Monitor, które będą wyzwalać podczas wartość metryki lub wyniki kwerendy spełnia określone warunki. Na przykład Utwórz alert, aby wysłać wiadomość e-mail, gdy co najmniej jeden węzeł nie wysłał pulsu w ciągu 5 godzin (czyli jest uznawana za niedostępne).

Z **dzienniki** bloku Uruchom **niedostępnymi komputerami** przykładowe zapytanie, klikając **Uruchom** na tym zapytaniu, jak pokazano poniżej.

![Analiza obszaru roboczego dzienniki bloku "niedostępnymi komputerami" Przykładowe zapytanie dziennika](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Jeśli wszystkie węzły są dostępne, to zapytanie powinno zwrócić wyników: 0 teraz. Kliknij przycisk **Nowa reguła alertu** aby rozpocząć konfigurowanie alertu dla tego zapytania.

![Nowa reguła alertu log Analytics obszaru roboczego](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Istnieją trzy składniki do alertu: *zasobów* dla której ma zostać utworzona reguła (roboczym usługi Log Analytics w tym przypadku), *warunek* do wyzwalania alertu, a *grupy akcji*  które określają, co się stanie, gdy zostanie wyzwolony alert.

Kliknij przycisk **warunku tytuł**, jak pokazano poniżej, aby ukończyć konfigurowanie logiki sygnału.

![Warunek reguły alertu](media/hdinsight-cluster-availability/portal-condition-title.png)

Spowoduje to otwarcie **konfigurowanie logiki sygnału** bloku.

Ustaw **Alert logic** sekcji w następujący sposób:

*Na podstawie: Liczba wyników, warunek: Przekracza wartość progową: 0.*

Ponieważ ta kwerenda zwraca tylko węzły niedostępny jako wyniki, jeśli liczba wyników jest coraz większa niż 0, powinny wyzwalać alert.

W **Evaluated na podstawie** sekcji, ustaw **okres** i **częstotliwość** oparte na jak często ma pod kątem niedostępny węzłów.

Uwaga na potrzeby tego alertu chcesz upewnić się, że **okres = częstotliwości.** Więcej informacji na temat okres, częstotliwość i inne parametry alertu można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Kliknij przycisk **gotowe** po zakończeniu, konfigurowanie logiki sygnału.

![Reguła alertu konfigurowanie logiki sygnału](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Jeśli nie masz już istniejącą grupę działań, kliknij przycisk **Utwórz nowy** w obszarze **grup akcji** sekcji.

![Nowa grupa akcji reguły alertu](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Spowoduje to otwarcie **Dodaj grupę akcji** bloku. Wybierz **Nazwa grupy akcji**, **krótką nazwę**, **subskrypcji**, i **grupy zasobów.** W obszarze **akcje** wybierz pozycję **nazwy akcji** i wybierz **poczty E-mail/SMS/wypychania/rejestr** jako **typ akcji.**

> [!NOTE]
> Istnieje kilka akcji, które mogą wyzwalać alert, oprócz poczty E-mail/SMS/wypychania/głosowych, takich jak funkcja platformy Azure, programu LogicApp, element Webhook, ITSM i elementu Runbook usługi Automation. [Dowiedz się więcej.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Spowoduje to otwarcie **poczty E-mail/SMS/wypychania/rejestr** bloku. Wybierz **nazwa** adresata, **Sprawdź** **E-mail** polu, a następnie wpisz adres e-mail, do której ma być wysyłany alert. Kliknij przycisk **OK** w **poczty E-mail/SMS/wypychania/rejestr** bloku, a następnie w obszarze **Dodaj grupę akcji** bloku, aby ukończyć konfigurowanie grupy akcji.

![Reguła alertu Dodaj grupę akcji](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zamknąć te bloki, powinien zostać wyświetlony na liście grupy akcji **grup akcji** sekcji. Na koniec Ukończ **szczegóły alertu** sekcji, wpisując **Nazwa reguły alertu** i **opis** i wybierając pozycję **ważność**.
Kliknij przycisk **Tworzenie reguły alertu** na zakończenie.

![Tworzenie reguły alertu zakończenia](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Możliwość określenia **ważność** jest zaawansowanym narzędziem, które mogą być używane podczas tworzenia wielu alertów. Na przykład można utworzyć jeden alert, aby zgłosić ostrzegawczy (ważność 1), jeśli jeden węzeł główny w dół, a kolejny alert, który wywołuje krytyczny (ważność 0), w razie mało prawdopodobnej zarówno węzłami głównymi przestaną działać.

Po spełnieniu warunku dla tego alertu, alert zostanie uruchomiony, a otrzymasz wiadomość e-mail ze szczegółami alertu w następujący sposób:

![Usługa Azure Monitor wiadomość e-mail z alertem](media/hdinsight-cluster-availability/alert-email.png)

Można również wyświetlić wszystkie alerty, które zostały wyzwolone pogrupowane według ważności, przechodząc do **alerty** bloku w swojej **obszar roboczy usługi Log Analytics**.

![Alerty usługi log Analytics obszaru roboczego](media/hdinsight-cluster-availability/portal-alerts.png)

Kliknięcie na grupowanie ważności (czyli **ważność 1,** jak podkreślono powyżej) pokaże rekordy dla wszystkich alertów o ważności, że mają wywołanego podobnie jak poniżej:

![Alerty usługi log Analytics obszaru roboczego ważność 1](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Kolejne kroki
- [Dostępność i niezawodność klastrów Apache Hadoop w HDInsight](hdinsight-high-availability-linux.md)
