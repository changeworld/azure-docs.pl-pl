---
title: 'Monitorowanie: Dzienniki Monitora Platformy Azure & Apache Ambari — Usługa Azure HDInsight'
description: Dowiedz się, jak używać dzienników Ambari i Azure Monitor do monitorowania kondycji i dostępności klastra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060182"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Jak monitorować dostępność klastra za pomocą dzienników Apache Ambari i Azure Monitor

Klastry HDInsight obejmują zarówno Apache Ambari, który zapewnia informacje o kondycji na pierwszy rzut oka i wstępnie zdefiniowane alerty, a także integrację dzienników usługi Azure Monitor, która zapewnia metryki i dzienniki, które można przeszukiwać, a także konfigurowalne alerty.

Ten doc pokazuje, jak korzystać z tych narzędzi do monitorowania klastra i przechodzi przez kilka przykładów konfigurowania alertu Ambari, monitorowania szybkości dostępności węzłów i tworzenia alertu usługi Azure Monitor, który jest uruchamiany, gdy puls nie został odebrany z jednego lub więcej węzłów w ciągu pięciu godzin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Pulpit nawigacyjny

Dostęp do pulpitu nawigacyjnego Ambari można uzyskać, wybierając **łącze domowe Ambari** w sekcji **Pulpity nawigacyjne klastra** w dziale HDInsight w witrynie Azure portal, jak pokazano poniżej. Alternatywnie można uzyskać dostęp do niego, przechodząc do `https://CLUSTERNAME.azurehdinsight.net` przeglądarki, gdzie CLUSTERNAME jest nazwą klastra.

![Widok portalu zasobów HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Zostanie wyświetlony monit o podanie nazwy użytkownika i hasła logowania do klastra. Wprowadź poświadczenia wybrane podczas tworzenia klastra.

Następnie zostaniesz przesuń do pulpitu nawigacyjnego Ambari, który zawiera widżety, które pokazują kilka wskaźników, aby zapewnić szybki przegląd kondycji klastra HDInsight. Te widżety pokazują metryki, takie jak liczba aktywne dane (węzły robocze) i JournalNodes (węzeł zookeeper), Czas pracy bez użycia nazwy (węzły głowy), a także metryki specyficzne dla niektórych typów klastrów, takie jak czas pracy programu YARN ResourceManager dla klastrów Spark i Hadoop.

![Apache Ambari używać wyświetlacza deski rozdzielczej](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosty — wyświetl stan poszczególnych węzłów

Można również wyświetlić informacje o stanie dla poszczególnych węzłów. Wybierz kartę **Hosts,** aby wyświetlić listę wszystkich węzłów w klastrze i wyświetlić podstawowe informacje o każdym węźle. Zielony test po lewej stronie każdej nazwy węzła wskazuje, że wszystkie składniki znajdują się w węźle. Jeśli składnik znajduje się w dół w węźle, zamiast zielonego znacznika zostanie wyświetlony czerwony trójkąt alertu.

![Widok hostów HDInsight Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Następnie można wybrać **nazwę** węzła, aby wyświetlić bardziej szczegółowe metryki hosta dla tego określonego węzła. Ten widok pokazuje stan/dostępność poszczególnych składników.

![Apache Ambari obsługuje widok pojedynczego węzła](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alerty Ambari

Ambari oferuje również kilka konfigurowalnych alertów, które mogą zapewnić powiadomienia o niektórych zdarzeniach. Gdy alerty są wyzwalane, są one wyświetlane w lewym górnym rogu Ambari w czerwonej plakietce zawierającej liczbę alertów. Wybranie tej plakietki powoduje wyświetlenie listy bieżących alertów.

![Apache Ambari liczba bieżących alertów](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Aby wyświetlić listę definicji alertów i ich stanów, wybierz kartę **Alerty,** jak pokazano poniżej.

![Ambari alerty definicje widok](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari oferuje wiele wstępnie zdefiniowanych alertów związanych z dostępnością, w tym:

| Nazwa alertu                        | Opis   |
|---|---|
| Podsumowanie kondycji DataNode           | Ten alert na poziomie usługi jest wyzwalany, jeśli istnieją złej kondycji DataNodes|
| NameNode Wysoka dostępność Kondycji | Ten alert na poziomie usługi jest wyzwalany, jeśli nie są uruchomione aktywne namenode lub standby namenode.|
| Dostępne dzienniki procentowe    | Ten alert jest wyzwalany, jeśli liczba w dół JournalNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki journalnode kontroli procesu. |
| Dostępne dane procentowe       | Ten alert jest wyzwalany, jeśli liczba w dół DataNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu DataNode.|

Pełną listę alertów Ambari, które pomagają monitorować dostępność klastra można znaleźć [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Aby wyświetlić szczegóły dotyczące alertu lub zmodyfikować kryteria, wybierz **nazwę** alertu. Weźmy na przykład **podsumowanie kondycji DataNode.** Możesz zobaczyć opis alertu, a także konkretne kryteria, które wyzwolą alert "ostrzeżenie" lub "krytyczny" oraz interwał sprawdzania kryteriów. Aby edytować konfigurację, wybierz przycisk **Edytuj** w prawym górnym rogu pola Konfiguracja.

![Konfiguracja alertu Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

W tym miejscu można edytować opis i, co ważniejsze, interwał sprawdzania i progi dla alertów ostrzegawczych lub alertów krytycznych.

![Konfiguracje alertów Ambari edytuj widok](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

W tym przykładzie można zrobić 2 złej kondycji DataNodes wyzwolić alert krytyczny i 1 złej kondycji DataNode tylko wyzwolić ostrzeżenie. Po zakończeniu edycji wybierz **pozycję Zapisz.**

### <a name="email-notifications"></a>Powiadomienia e-mail

Możesz również opcjonalnie skonfigurować powiadomienia e-mail dla alertów Ambari. Aby to zrobić, gdy na karcie **Alerty** kliknij przycisk **Akcje** w lewym górnym rogu, a następnie **zarządzaj powiadomieniami.**

![Ambari zarządza działaniami powiadomień](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Zostanie otwarte okno dialogowe do zarządzania powiadomieniami o alertach. Wybierz **+** u dołu okna dialogowego i wypełnij wymagane pola, aby zapewnić Ambari szczegóły serwera poczty e-mail, z których mają być wysyłane wiadomości e-mail.

> [!TIP]
> Konfigurowanie powiadomień e-mail Ambari może być dobrym sposobem otrzymywania alertów w jednym miejscu podczas zarządzania wieloma klastrami HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integracja z usługą Azure Monitor

Dzienniki usługi Azure Monitor umożliwiają zbieranie i agregowanie danych generowanych przez wiele zasobów, takich jak klastry HDInsight, w jednym miejscu w celu uzyskania ujednoliconego środowiska monitorowania.

Jako warunek wstępny musisz obszar roboczy analizy dzienników do przechowywania zebranych danych. Jeśli jeszcze go nie utworzyłeś, możesz postępować zgodnie z instrukcjami tutaj: [Utwórz obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Włącz integrację dzienników usługi HDInsight Azure Monitor

Na stronie zasobów klastra HDInsight w portalu wybierz pozycję **Azure Monitor**. Następnie wybierz **włącz** i wybierz obszar roboczy usługi Log Analytics z listy rozwijanej.

![Pakiet zarządzania operacjami HDInsight](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Metryki kwerend i tabele dzienników

Po włączeniu integracji dziennika usługi Azure Monitor (może to potrwać kilka minut), przejdź do zasobu **obszaru roboczego usługi Log Analytics** i wybierz pozycję **Dzienniki.**

![Dzienniki obszaru roboczego usługi Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Dzienniki wyświetlają listę przykładowych zapytań, takich jak:

| Nazwa kwerendy                      | Opis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostępność komputerów już dziś    | Wykres liczby komputerów wysyłających dzienniki, co godzinę                     |
| Lista uderzeń serca                 | Wyświetl listę wszystkich pulsu komputera z ostatniej godziny                           |
| Ostatnie bicie serca każdego komputera | Pokaż ostatnie bicie serca wysyłane przez każdy komputer                             |
| Komputery niedostępne           | Wyświetl listę wszystkich znanych komputerów, które nie wysłały pulsu w ciągu ostatnich 5 godzin |
| Wskaźnik dostępności               | Obliczanie szybkości dostępności każdego podłączonego komputera                |

Na przykład uruchom przykładową kwerendę **współczynnika dostępności,** wybierając **pozycję Uruchom** w tej kwerendzie, jak pokazano na powyższym zrzucie ekranu. Spowoduje to wyświetlenie szybkości dostępności każdego węzła w klastrze jako procent. Jeśli włączono wiele klastrów usługi HDInsight do wysyłania metryk do tego samego obszaru roboczego usługi Log Analytics, zobaczysz wskaźnik dostępności dla wszystkich węzłów w tych klastrach wyświetlanych.

![Obszar roboczy usługi Log Analytics rejestruje przykładową kwerendę "współczynnik dostępności"](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> Wskaźnik dostępności jest mierzony w okresie 24-godzinnym, więc klaster będzie musiał działać przez co najmniej 24 godziny, zanim zobaczysz dokładne stawki dostępności.

Tę tabelę można przypiąć do udostępnionego pulpitu nawigacyjnego, klikając **pozycję Przypnij** w prawym górnym rogu. Jeśli nie masz żadnych zapisywalnych udostępnionych pulpitów nawigacyjnych, możesz zobaczyć, jak je utworzyć tutaj: [Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alerty usługi Azure Monitor

Można również skonfigurować alerty usługi Azure Monitor, które będą wyzwalane, gdy wartość metryki lub wyniki kwerendy spełniają określone warunki. Na przykład utwórzmy alert, aby wysłać wiadomość e-mail, gdy jeden lub więcej węzłów nie wysłał pulsu w ciągu 5 godzin (tj. jest uważane za niedostępne).

Z **dzienników**uruchom przykładową kwerendę **Niedostępne komputery,** wybierając **pozycję Uruchom** w tej kwerendzie, jak pokazano poniżej.

![Przykład obszaru roboczego usługi Log Analytics rejestruje "niedostępne komputery"](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Jeśli wszystkie węzły są dostępne, ta kwerenda powinna zwrócić zero wyników na razie. Kliknij **pozycję Nowa reguła alertu,** aby rozpocząć konfigurowanie alertu dla tej kwerendy.

![Nowa reguła alertów usługi Log Analytics](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Istnieją trzy składniki do alertu: *zasób,* dla którego do utworzenia reguły (obszar roboczy usługi Log Analytics w tym przypadku), *warunek* wyzwolenia alertu i *grupy akcji,* które określają, co się stanie po wyzwoleniu alertu.
Kliknij **tytuł warunku**, jak pokazano poniżej, aby zakończyć konfigurowanie logiki sygnału.

![Warunek utworzenia reguły alertu portalu](media/hdinsight-cluster-availability/portal-condition-title.png)

Spowoduje to otwarcie **konfigurowania logiki sygnału**.

Ustaw sekcję **logiki alertu** w następujący sposób:

*Na podstawie: Liczba wyników, Stan: Większy niż, Próg: 0.*

Ponieważ ta kwerenda zwraca tylko węzły niedostępne jako wyniki, jeśli liczba wyników jest coraz większa niż 0, alert powinien zostać uruchamiany.

W sekcji **Oceniane na podstawie** ustaw okres i **częstotliwość** na podstawie **częstotliwości,** jak często chcesz sprawdzić, czy niedostępne węzły.

Na potrzeby tego alertu należy się upewnić, że **Period=Frequency.** Więcej informacji na temat okresu, częstotliwości i innych parametrów alertu można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Po zakończeniu konfigurowania logiki sygnału wybierz **opcję Gotowe.**

![Reguła alertu konfiguruje logikę sygnału](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Jeśli nie masz jeszcze istniejącej grupy akcji, kliknij pozycję **Utwórz nowy** w sekcji **Grupy akcji.**

![Reguła alertu tworzy nową grupę akcji](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Spowoduje to otwarcie **Dodaj grupę akcji**. Wybierz **nazwę grupy akcji,** **Krótką nazwę,** **Subskrypcję**i **Grupę Zasobów.** W sekcji **Akcje** wybierz **nazwę akcji** i wybierz pozycję **E-mail/SMS/Push/Voice** jako **typ akcji.**

> [!NOTE]
> Istnieje kilka innych akcji alert może wyzwolić oprócz wiadomości e-mail/SMS/Push/Voice, takich jak funkcja platformy Azure, LogicApp, Webhook, ITSM i system funkcjonowania automatyzacji. [Dowiedz się więcej.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Spowoduje to otwarcie **wiadomości e-mail/ SMS/Push/Voice**. Wybierz **nazwę** adresata, **zaznacz** pole **Poczta e-mail** i wpisz adres e-mail, na który chcesz wysłać alert. Wybierz **przycisk OK** w obszarze Poczta **e-mail/SMS/Wypychanie/Głos**, a następnie w obszarze Dodaj **grupę akcji,** aby zakończyć konfigurowanie grupy akcji.

![Reguła alertu tworzy grupę akcji dodaj](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zamknięciu tych bloków powinna zostać wyświetlona grupa akcji wymieniona w sekcji **Grupy akcji.** Na koniec uzupełnij sekcję **Szczegóły alertu,** wpisując nazwę i **opis** **reguły alertu** oraz wybierając **ważność**. Kliknij **pozycję Utwórz regułę alertu,** aby zakończyć.

![Portal tworzy zakończenie reguły alertu](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Możliwość określenia **ważności** jest potężnym narzędziem, które może być używane podczas tworzenia wielu alertów. Na przykład można utworzyć jeden alert, aby podnieść ostrzeżenie (Sev 1), jeśli pojedynczy węzeł główny ulegnie upadkowi i inny alert, który podnosi krytyczne (Sev 0) w mało prawdopodobnym przypadku, że oba węzły głównego zejść.

Po spełnieniu warunku tego alertu zostanie ono odpalone, a otrzymasz wiadomość e-mail z takimi informacjami:

![Przykład wiadomości e-mail z alertem usługi Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Można również wyświetlić wszystkie alerty, które zostały wystrzelone, pogrupowane według ważności, przechodząc do **alertów** w **obszarze roboczym usługi Log Analytics**.

![Alerty obszaru roboczego usługi Log Analytics](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Wybranie grupy ważności (tj. **Sev 1,** jak podkreślono powyżej) pokaże rekordy dla wszystkich alertów o tej ważności, które zostały wystrzelone jak poniżej:

![Obszar roboczy usługi Log Analytics sev jeden alert](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Następne kroki

- [Dostępność i niezawodność klastrów Apache Hadoop w systemie HDInsight](hdinsight-high-availability-linux.md)
