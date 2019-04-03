---
title: Metryki w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano metryki w usłudze Azure Monitor, które zajmują danych może obsługiwać niemal w czasie rzeczywistym scenariuszy monitorowania.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 487f70e4055f16c56092f2f970d2a34238e7febe
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851989"
---
# <a name="metrics-in-azure-monitor"></a>Metryki w usłudze Azure Monitor

> [!NOTE]
> Platforma danych usługi Azure Monitor jest oparty na dwa typy danych podstawowych: Metryki i dzienniki. W tym artykule opisano metryki. Zapoznaj się [dzienników w usłudze Azure Monitor](data-platform-logs.md) szczegółowy opis dzienniki i [platforn danych usługi Azure Monitor](data-platform.md) porównanie dwóch.


Metryki w usłudze Azure Monitor są uproszczone i może obsługiwać w scenariuszach w czasie rzeczywistym, dzięki czemu jest szczególnie przydatne w przypadku zgłaszania alertów i szybkie wykrywanie problemów. W tym artykule opisano, jak metryki są skonstruowane, co można zrobić z nimi i identyfikuje różnych źródłach danych przechowujących dane w metrykach.

## <a name="what-are-metrics"></a>Co to są metryk?
Metryki są wartości liczbowe, które opisują niektóre aspekty systemu w określonym czasie. Metryki są zbierane w regularnych odstępach czasu i są przydatne w przypadku alertów, ponieważ może być próbkowany często i alertu można szybko wywoływane z logiką stosunkowo proste.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Co można zrobić za pomocą metryk usługi Azure Monitor?
W poniższej tabeli wymieniono różne sposoby, można użyć danych metryk w usłudze Azure Monitor.

|  |  |
|:---|:---|
| Analiza | Użyj [Eksploratora metryk](metrics-charts.md) do analizowania zebranych metryk na wykresie i porównywania metryk z różnych zasobów. |
| Wizualizacja | Przypinanie wykresu z Eksploratora metryk, aby [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-app-dashboards.md).<br>Tworzenie [skoroszytu](../app/usage-workbooks.md) połączyć z wieloma zestawami danych w interaktywnych raportów. Eksportowanie wyników kwerendy, aby [Grafana](grafana-plugin.md) wykorzystać jej dashboarding i łączyć z innymi źródłami danych. |
| Alerty | Konfigurowanie [regułę alertu metryki](alerts-metric.md) która wyśle powiadomienie, lub trwa [zautomatyzowanej akcji](action-groups.md) kiedy wartość metryki przekracza próg. |
| Automatyzacja |  Użyj [skalowania automatycznego](autoscale-overview.md) na zwiększanie lub zmniejszanie zasobów opartych na przekroczenia progu przez wartość metryki. |
| Eksportowanie | [Kierowanie metryk z dziennikami](diagnostic-logs-stream-log-store.md) do analizowania danych metryk usługi Azure Monitor, wraz z danymi w usłudze Azure Monitor dziennikach i do przechowywania wartości metryk przez okres dłuższy niż 93 dni.<br>Stream metryki, aby [Centrum zdarzeń](stream-monitoring-data-event-hubs.md) je rozesłać z systemami zewnętrznymi. |
| Pobierz | Dostęp do wartości metryk z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0)<br>Dostęp do wartości metryk z niestandardowych aplikacji przy użyciu [interfejsu API REST](rest-api-walkthrough.md).<br>Dostęp do wartości metryk z wiersza polecenia przy użyciu [interfejsu wiersza polecenia](/azure/monitor/metrics). |
| Archiwum | [Archiwum](..//learn/tutorial-archive-data.md) historii wydajności i kondycji zasobu pod kątem zgodności, inspekcji lub w trybie offline do celów raportowania. |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>W jaki sposób dane ze strukturą metryk usługi Azure Monitor?
Dane zbierane przez metryk usługi Azure Monitor jest przechowywane w bazie danych szeregów czasowych, który jest zoptymalizowany pod kątem analizowania danych z sygnaturami czasowymi. Każdy zestaw wartości metryk jest szeregów czasowych z następującymi właściwościami:

* Czas, który został zebrany wartość
* Wartość zasobu jest skojarzony.
* Obszar nazw, który zachowuje się jak kategorii metryki
* Nazwa metryki
* Sama wartość
* Niektóre metryki mogą mieć wiele wymiarów, zgodnie z opisem w [metryk wielowymiarowych](#multi-dimensional-metrics). Metryki niestandardowe mogą mieć maksymalnie 10 wymiarów.

Metryki na platformie Azure są przechowywane przez 93 dni. Możesz [wysyłanie metryk platformy dla zasobów usługi Azure Monitor do obszaru roboczego usługi Log Analytics](diagnostic-logs-stream-log-store.md) umożliwia analizę trendów w długim okresie.

## <a name="multi-dimensional-metrics"></a>Metryk wielowymiarowych
Jest jednym z wyzwań do danych metryk, że często ma mniej informacji, aby zapewnić kontekst dla zebranych wartości. Usługa Azure Monitor sprostać temu wyzwaniu przy użyciu metryk wielowymiarowych. Wymiary metryki są pary nazwa wartość, zawierających dodatkowe dane do opisania wartość metryki. Na przykład metryka _dostępnego miejsca na dysku_ może mieć wymiar o nazwie _dysku_ wartościami _C:_, _D:_, co umożliwiłoby wyświetlania wartość dostępnego miejsca na dysku na wszystkich dyskach lub dla każdego dysku indywidualnie.

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

## <a name="interacting-with-azure-monitor-metrics"></a>Interakcja z metryk usługi Azure Monitor
Użyj [Eksploratora metryk](metrics-charts.md) interakcyjne analizowanie danych w bazie danych metryk i wykres wartości wielu metryk wraz z upływem czasu. Możesz przypiąć wykresy, aby pulpit nawigacyjny, aby wyświetlić je z innymi wizualizacjami. Możesz również pobrać za pomocą metryk [platformy Azure, interfejsu API REST monitorowania](rest-api-walkthrough.md).

![Eksplorator metryk](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Źródła metryk usługi Azure Monitor
Istnieją trzy podstawowe źródła metryki zebrane przez usługi Azure Monitor. Te metryki zebrane w bazie danych metryk usługi Azure Monitor mogą one ze sobą obliczane niezależnie od ich źródła.

**Metryki platformy** są tworzone przez zasoby platformy Azure i zapewniają wgląd w ich kondycję i wydajność. Tworzy każdego typu zasobu [odrębnym zestawem metryki](metrics-supported.md) bez żadnej konfiguracji wymagane. Metryki platformy są zbierane z zasobów platformy Azure częstotliwością co minutę, chyba że określono inaczej w definicji metryk. 

**Metryki systemu operacyjnego gościa** są zbierane z systemu operacyjnego gościa maszyny wirtualnej. Włącz metryki systemu operacyjnego gościa dla maszyn wirtualnych Windows z [Windows rozszerzenia diagnostyki (WAD)](../platform/diagnostics-extension-overview.md) i maszyn wirtualnych systemu Linux przy użyciu [agenta Telegraf InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).

**Metryki aplikacji** są tworzone przez usługę Application Insights dla aplikacji monitorowanych i pozwalają wykryć problemy z wydajnością i śledzenie trendów w sposobu korzystania z aplikacji. Obejmuje to wartości, takich jak _czas odpowiedzi serwera_ i _wyjątków przeglądarki_.

**Metryki niestandardowe** metryki, które definiują oprócz standardowych metryk, które są automatycznie dostępne. Możesz [zdefiniować metryki niestandardowe w aplikacji](../app/api-custom-events-metrics.md) który jest monitorowany przez usługę Application Insights lub utworzyć niestandardowe metryki dla usługi za pomocą usługi Azure [niestandardowy interfejs API metryk](metrics-store-custom-rest-api.md).


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [platforma danych usługi Azure Monitor](data-platform.md).
- Dowiedz się więcej o [rejestrować dane w usłudze Azure Monitor](data-platform-logs.md).
- Dowiedz się więcej o [dostępne dane monitorowania](data-sources.md) dla różnych zasobów na platformie Azure.
