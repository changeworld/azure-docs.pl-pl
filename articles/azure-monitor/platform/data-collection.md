---
title: Dane monitorowania zbierane przez usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Monitorowanie danych zbieranych przez usługi Azure Monitor jest podzielony metryki, które są uproszczone i może obsługiwać w scenariuszach w czasie rzeczywistym i dzienników, które są używane do zaawansowanej analizy.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: bwren
ms.openlocfilehash: e6d953841e5c22c21640f874ecad942f8db76ad1
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448898"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Dane monitorowania zbierane przez usługi Azure Monitor
[Usługa Azure Monitor](../overview.md) jest to usługa, która pomaga monitorować Twoje aplikacje i zasoby korzystają z. Decydujące znaczenie dla tej funkcji jest przechowywanie danych telemetrycznych i innych danych z monitorowanych zasobów. Ten artykuł zawiera pełny opis jak te dane są przechowywane i używane przez usługi Azure Monitor.

Wszystkie dane zebrane przez usługi Azure Monitor pasuje do jednej z dwóch typów podstawowych, [metryki](#metrics) i [dzienniki](#logs). Metryki są wartości liczbowe, które opisują niektóre aspekty systemu w określonym punkcie w czasie. Są one uproszczone i zdolność do obsługi scenariuszy w czasie rzeczywistym w pobliżu. Dzienniki zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dane telemetryczne, np. zdarzeniami i śladami są przechowywane jako dzienniki dodatkowo na dane wydajności, aby wszystkie można zbudować do analizy.

![Omówienie usługi Azure Monitor](media/data-collection/overview.png)

## <a name="metrics"></a>Metryki
Metryki są wartości liczbowe, które opisują niektóre aspekty systemu w określonym czasie. Są one uproszczone i zdolność do obsługi scenariuszy w czasie rzeczywistym w pobliżu. Metryki są zbierane w regularnych odstępach czasu, czy po zmianie wartości. Są one przydatne w przypadku alertów, ponieważ może być próbkowany często i alertu można szybko wywoływane z logiką stosunkowo proste.

Na przykład użytkownik może zbierać użycie procesora na maszynie wirtualnej co minutę lub liczby użytkowników zalogował się do aplikacji co 10 minut. Można wyzwalać alert, po jednym z tych zebrane wartości lub nawet różnicę między dwiema wartościami przekracza określoną wartość progową.

Określone atrybuty metryki na platformie Azure są następujące:

* Zbierane częstotliwością co minutę, chyba że określono inaczej w definicji metryk.
* Jednoznacznie zidentyfikować przez nazwę metryki i przestrzeni nazw, który działa jako kategorii.
* Przechowywane przez 93 dni. Możesz skopiować metryk z dziennikami umożliwia analizę trendów w długim okresie.

Każda wartość metryki ma następujące właściwości:
* Czas, który został zebrany wartość.
* Typ miary, którą reprezentuje wartość.
* Wartość zasobu jest skojarzony.
* Samą wartość.
* Niektóre metryki mogą mieć wiele wymiarów, zgodnie z opisem w następnej sekcji. Metryki niestandardowe mogą mieć maksymalnie 10 wymiarów.

### <a name="multi-dimensional-metrics"></a>Metryk wielowymiarowych
Wymiary metryki są pary nazwa wartość, zawierających dodatkowe dane do opisania wartość metryki. Na przykład metryka _dostępnego miejsca na dysku_ może mieć wymiar o nazwie _dysku_ wartościami _C:_, _D:_, co umożliwiłoby wyświetlania wartość dostępnego miejsca na dysku na wszystkich dyskach lub dla każdego dysku indywidualnie.

W poniższym przykładzie przedstawiono dwa zestawy danych dla hipotetycznego metrykę zwaną _przepływność sieci_. Pierwszy zestaw danych nie ma żadnych wymiarów. Drugi zestaw danych zawiera wartości, z dwoma wymiarami _adresu IP_ i _kierunek_:

### <a name="network-throughput"></a>Przepływność sieci

| Znacznik czasu     | Wartość metryki |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kb/s |
| 8/9/2017 8:15 | 1,141.4 Kb/s |
| 8/9/2017 8:16 | 1,110.2 Kb/s |

Ta metryka-wymiarowej można tylko odpowiedzi na podstawowe pytania, takie jak "jaki był Moje przepustowość sieci w danym momencie?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Przepływność sieci i dwa wymiary ("Adres IP" i "Direction")

| Znacznik czasu     | Wymiar "Adres IP"   | Wymiar "Direction" | Wartość metryki|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Kierunek = "Wyślij"    | 646.5 kb/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Kierunek = "Otrzymywać" | 420.1 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Kierunek = "Wyślij"    | 150.0 kb/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Kierunek = "Otrzymywać" | 115.2 Kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Kierunek = "Wyślij"    | 515.2 Kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Kierunek = "Otrzymywać" | 371.1 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Kierunek = "Wyślij"    | 155.0 kb/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Kierunek = "Otrzymywać" | 100.1 KB/s |

Ta metryka może odpowiedzieć na pytania, takie jak "jaki był przepływności sieci dla każdego adresu IP?" i "ile danych zostało wysłane i odebrane?" Metryk wielowymiarowych wykonać dodatkową wartość analitycznych i diagnostyki w porównaniu do-wymiarowej metryki.

### <a name="value-of-metrics"></a>Wartość metryki
Metryki poszczególnych zwykle zapewniają mały wgląd w ich własnych. Zapewniają one pojedynczej wartości bez żadnego kontekstu innych niż porównania prosty próg. Są one przydatne w połączeniu z innymi metrykami w celu zidentyfikowania wzorców i trendów lub w połączeniu z dzienników, które zapewniają kontekstowe wokół konkretnych wartości.

Na przykład ilu użytkowników w swojej aplikacji w danym momencie mogą wskazać możliwość rozwiązania trochę o kondycji aplikacji. Ale nagły spadek użytkowników, wskazywanym przez wiele wartości tego samego metryki może wskazywać na problem. Nadmierne wyjątków zgłoszonych przez aplikację, a podana przez oddzielne metryki, może zidentyfikować problem aplikacji, która powoduje spadek. Zdarzeń tworzonych przez aplikację do identyfikowania błędów w jej składniki mogą pomóc odkryć ich główną przyczynę.

### <a name="sources-of-metric-data"></a>Źródła danych metryk
Istnieją trzy podstawowe źródła metryki zebrane przez usługi Azure Monitor. Wszystkie te metryki są dostępne w sklepie metryki, gdzie one mogą być obliczane razem niezależnie od ich źródła.

**Metryki platformy** są tworzone przez zasoby platformy Azure i zapewniają wgląd w ich kondycję i wydajność. Tworzy każdego typu zasobu [odrębnym zestawem metryki](metrics-supported.md) bez żadnej konfiguracji wymagane. 

**Metryki aplikacji** są tworzone przez usługę Application Insights dla aplikacji monitorowanych i pozwalają wykryć problemy z wydajnością i śledzenie trendów w sposobu korzystania z aplikacji. Obejmuje to wartości, takich jak _czas odpowiedzi serwera_ i _wyjątków przeglądarki_.

**Metryki niestandardowe** metryki definiujące oprócz standardowych metryk, które są automatycznie dostępne. Metryki niestandardowe, należy utworzyć dla pojedynczego zasobu w tym samym regionie, co tego zasobu. Można utworzyć metryki niestandardowe przy użyciu następujących metod:
- [Zdefiniuj metryki niestandardowe w aplikacji](../../azure-monitor/app/api-custom-events-metrics.md) który jest monitorowany przez usługę Application Insights. Te właściwości są oprócz standardowych ustawiane metryki aplikacji.
- Publikowanie metryki niestandardowe z maszyn wirtualnych Windows przy użyciu [Windows rozszerzenia diagnostyki (WAD)](../../azure-monitor/platform/diagnostics-extension-overview.md).
- Publikowanie metryki niestandardowe z maszyn wirtualnych systemu Linux przy użyciu [agenta Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).
- Zapis metryki niestandardowe z usługą platformy Azure za pomocą metryk niestandardowych interfejsów API.

![Przegląd metryk](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>Co można zrobić za pomocą metryk?
Następujące zadania, które można wykonywać za pomocą metryk:

- Użyj [analytics metryki](metrics-charts.md) do analizowania zebranych metryk i vykreslit v je. Śledzić wydajność zasobu (np. maszyn wirtualnych, witryny sieci Web lub logiki aplikacji), przypinając wykresy, aby [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md).
- Konfigurowanie [regułę alertu metryki](alerts-metric.md) która wyśle powiadomienie, lub trwa [zautomatyzowanej akcji](action-groups.md) gdy Metryka przekracza próg.
- Użyj [skalowania automatycznego](autoscale-overview.md) lub zwiększ zasoby na podstawie metryki przekroczenia wartości progowej.
- Metryki tras do dzienników do analizowania danych metryk wraz z danymi dziennika oraz do przechowywania wartości metryk przez okres dłuższy niż 93 dni. 
- Stream metryki, aby [Centrum zdarzeń](stream-monitoring-data-event-hubs.md) do kierowania ich do [usługi Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) lub systemów zewnętrznych.
- [Archiwum](../../azure-monitor/learn/tutorial-archive-data.md) historii wydajności i kondycji zasobu pod kątem zgodności, inspekcji lub w trybie offline do celów raportowania.
- Dostęp do wartości metryk z wiersza polecenia lub niestandardowych aplikacji przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/) lub [interfejsu API REST](rest-api-walkthrough.md).



### <a name="viewing-metrics"></a>Wyświetlanie metryk
Metryki w usłudze Azure Monitor są przechowywane w szeregach czasowych bazy danych zoptymalizowane pod kątem szybkiego pobierania i magazyny wartości metryk 93 dni. Skopiuj metryki dzienniki długoterminowych analizy i określania trendów.

Dane metryk są używane w na różne sposoby, zgodnie z powyższym opisem. Użyj [analytics metryki](metrics-charts.md) bezpośrednio analizować dane w magazynie metryki i wykres wartości wielu metryk wraz z upływem czasu. Można wyświetlać wykresy interakcyjnie lub przypiąć je do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami. Możesz również pobrać za pomocą metryk [platformy Azure, interfejsu API REST monitorowania](rest-api-walkthrough.md).

![Analiza metryki](media/data-collection/metrics-explorer.png)

## <a name="logs"></a>Dzienniki

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Dzienniki zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dzienniki mogą zawierać wartości liczbowe, takie jak metryki, ale zwykle zawierają dane tekstowe za pomocą szczegółowe opisy. Dodatkowo różnią się od metryki, różnią się one w ich struktury i często nie są zbierane w regularnych odstępach czasu.

Typowe typ wpisu dziennika jest zdarzenie, które są zbierane sporadycznie. One tworzone przez aplikację lub usługę i zazwyczaj zawierają informacje wystarczające do zapewnienia pełnego kontekstu własnych. Na przykład zdarzenie może wskazywać, że określony zasób został utworzony lub zmodyfikowany nowego hosta uruchomionego w odpowiedzi zwiększonego ruchu lub wykryto błąd w aplikacji.

Dzienniki są szczególnie przydatne dla łączenie danych z różnych źródeł, złożoną analizę i umożliwia analizę trendów wraz z upływem czasu. Ponieważ format danych może się różnić w aplikacji można utworzyć niestandardowe dzienniki przy użyciu struktury, które potrzebują. Metryki nawet są replikowane w dziennikach, aby połączyć je z innych danych monitorowania umożliwia analizę trendów i innych danych.



### <a name="sources-of-log-data"></a>Źródła danych dziennika
Usługa Azure Monitor może zbierać dane dzienników z różnych źródeł, zarówno w obrębie platformy Azure i z zasobami lokalnymi. Następujące źródła danych dziennika:

- [Dzienniki aktywności](collect-activity-logs.md) z zasobów platformy Azure, które obejmują informacje o swojej konfiguracji i kondycji i [dzienniki diagnostyczne](diagnostic-logs-stream-log-store.md) zapewniające wgląd w ich działania.
- Agenci na [Windows](agent-windows.md) i [Linux](../learn/quick-collect-linux-computer.md) maszyn wirtualnych, które wysyłają dane telemetryczne z systemu operacyjnego gościa i aplikacji do usługi Azure Monitor, zgodnie z opisem w [źródeł danych](data-sources.md) , należy skonfigurować.
- Dane aplikacji są zbierane przez [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Danych zapewniające wgląd w określonej aplikacji lub usługi [rozwiązania do monitorowania](../insights/solutions.md) lub funkcje, takie jak szczegółowe informacje o kontenerze, szczegółowych informacji z maszyny Wirtualnej lub grupy zasobów w szczegółowe informacje.
- Dane zabezpieczeń gromadzone przez [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- [Metryki](#metrics) z zasobów platformy Azure. Dzięki temu można przechowywać metryki, przez czas dłuższy niż 93 dni i Analizuj je z innymi danymi dziennika.
- Zapisane dane telemetryczne [usługi Azure Storage](azure-storage-iis-table.md).
- Niestandardowe dane za pomocą dowolnego klienta interfejsu API REST przy użyciu [interfejsu API modułu zbierającego dane HTTP](data-collector-api.md) klienta lub z [aplikacji logiki platformy Azure](https://docs.microsoft.com/azure/logic-apps/) przepływu pracy.

![Przegląd dzienników](media/data-collection/logs-overview.png)

### <a name="what-can-you-do-with-logs"></a>Co można zrobić za pomocą dzienników?
Następujące zadania, które można wykonywać za pomocą dzienników:

- Użyj [usługi Log Analytics](../log-query/get-started-portal.md) w witrynie Azure portal do pisania zapytań, analizowanie danych dzienników. Wyniki renderowane jako tabele lub wykresy, aby przypiąć [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md).
- Konfigurowanie [reguł alertów dzienników](alerts-log.md) która wyśle powiadomienie, lub trwa [zautomatyzowanej akcji](action-groups.md) po wyniki zapytania pasują do konkretnego wyniku.
- Tworzenie przepływu pracy w dzienniku danych przy użyciu [Logic Apps](~/articles/logic-apps/index.yml).
- Eksportowanie wyników kwerendy, aby [usługi Power BI](powerbi.md) różne wizualizacje i udostępniać użytkownikom spoza platformy Azure.
- Dostęp do wartości metryk z wiersza polecenia lub niestandardowych aplikacji przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) lub [interfejsu API REST](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Wyświetlanie danych dzienników
Wszystkie dane dzienników w usłudze Azure Monitor są pobierane przy użyciu [zapytanie dziennika](../log-query/log-query-overview.md) napisane przy użyciu [język zapytania Kusto](../log-query/get-started-queries.md), co pozwala na szybkie pobierania, konsolidacji i analizowania zebranych danych. Użyj [usługi Log Analytics](../log-query/portals.md) pisania i testowania zapytań w witrynie Azure portal. Można interaktywnej pracy z wynikami lub przypiąć je do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami. Możesz również pobrać dzienniki przy użyciu [platformy Azure, interfejsu API REST monitorowania](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

> [!IMPORTANT]
> Dane z usługi Application Insights są przechowywane w oddzielnej partycji niż inne dane dzienników w usłudze Azure Monitor. W ten sposób realizowany taką samą funkcjonalność jak inne dane dziennika, ale muszą używać [konsoli Application Insights](../app/analytics.md) lub [interfejsu API usługi Application Insights](https://dev.applicationinsights.io/) na dostęp do tych danych. Możesz użyć [zapytania obejmujące wiele zasobów](../log-query/cross-workspace-query.md) do analizowania danych aplikacji wraz z innymi danymi dziennika.

![Dzienniki](media/data-collection/logs.png)

## <a name="convert-monitoring-data"></a>Konwertowanie danych monitorowania

### <a name="metrics-to-logs"></a>Metryki do dzienników
Metryki można skopiować do dzienniki, aby wykonywać złożone analizy z innymi typami danych przy użyciu usługi Azure Monitor zaawansowany język zapytań. Ponadto można przechowywać dane dziennika przez dłuższy czas niż metryk, które umożliwia przeprowadzanie analizy trendów wraz z upływem czasu. Metryki do obsługi niemal w czasie rzeczywistym analiz i alertów podczas korzystania z dzienników do wyznaczania trendów i analiz z innymi danymi.

Możesz uzyskać wskazówki dotyczące zbieranie metryk z zasobów platformy Azure w [zbieranie dzienników platformy Azure usługi i metryk do użycia w usłudze Azure Monitor](collect-azure-metrics-logs.md). Uzyskaj wskazówki dotyczące zbieranie metryk zasobów z zasobów PaaS platformy Azure w [skonfigurować zbieranie metryk zasobów PaaS platformy Azure z usługą Azure Monitor](collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Dzienniki, metryki
Zgodnie z powyższym opisem metryki są bardziej elastyczny niż dzienniki, aby można było utworzyć alerty z mniejszymi opóźnieniami i przy niskich kosztach. Znacznej ilości danych liczbowych są przechowywane jako dzienniki, które będą właściwe dla metryk, ale nie są przechowywane jako metryki w usłudze Azure Monitor. Typowym przykładem są dane dotyczące wydajności zbierane z agentów i rozwiązań do zarządzania. Niektóre z tych wartości mogły zostać skopiowane do metryk, jeśli są one dostępne dla alertów i analizy przy użyciu Eksploratora metryk.

WYJAŚNIENIE Ta funkcja jest dostępna na [Tworzenie alertów metryk dla dzienników w usłudze Azure Monitor](alerts-metric-logs.md). Lista wartości pomocy technicznej znajduje się w temacie [metryki obsługiwane z usługą Azure Monitor](metrics-supported.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Data Stream z systemami zewnętrznymi
Oprócz używania narzędzi na platformie Azure do analizowania danych monitorowania, może być wymagane ją przesłać do zewnętrznego narzędzia, takie jak informacje o zabezpieczeniach i produktów do zarządzania (SIEM) zdarzenia. Przekazywanie ten jest zazwyczaj wykonywane bezpośrednio z monitorowanych zasobów za pomocą [usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).

Możesz też uzyskać wskazówki dotyczące różnych rodzajów danych monitorowania na [Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dostępne dane monitorowania](data-sources.md) dla różnych zasobów na platformie Azure.
